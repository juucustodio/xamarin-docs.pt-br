---
title: TextKit em xamarin
description: Este documento descreve como usar TextKit em xamarin. TextKit fornece recursos de layout e renderização de texto avançado.
ms.prod: xamarin
ms.assetid: 1D0477E8-CD1E-48A9-B7C8-7CA892069EFF
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: ac80d1d07f5649d377dd6fdefcb4911ba9ec2dcb
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788329"
---
# <a name="textkit-in-xamarinios"></a>TextKit em xamarin

TextKit é uma nova API que oferece recursos de layout e renderização de texto avançado. Ele é criado sobre a estrutura de texto principal de baixo nível, mas é muito mais fácil de usar do que o texto de núcleo.

Para disponibilizar os recursos de TextKit para controles padrão, vários controles de texto de iOS foram implementados novamente para usar TextKit, incluindo:

-  UITextView
-  UITextField
-  UILabel

## <a name="architecture"></a>Arquitetura

TextKit fornece uma arquitetura em camadas que separa o armazenamento de texto do layout e exibição, incluindo as seguintes classes:

-  `NSTextContainer` – Fornece o sistema de coordenadas e geometria que é usado para o texto do layout.
-  `NSLayoutManager` – Apresenta o texto ao transformar o texto em glifos. 
-  `NSTextStorage` – Mantém os dados de texto, bem como manipula atualizações de propriedade de texto de lote. As atualizações em lotes são passadas para o Gerenciador de layout para o processamento real das alterações, como recalcular o layout e redesenhar o texto.


Esses três classes são aplicadas a uma exibição que renderiza o texto. O texto interno de tratamento de modos de exibição, como `UITextView`, `UITextField`, e `UILabel` têm definido, mas você pode criar e aplicá-las a qualquer `UIView` instância também.

A figura a seguir ilustra essa arquitetura:

 ![](textkit-images/textkitarch.png "Esta figura ilustra a arquitetura de TextKit")

## <a name="text-storage-and-attributes"></a>Atributos e o armazenamento de texto

O `NSTextStorage` classe contém o texto que é exibido por uma exibição. Ela também se comunica as alterações para o texto -, como alterações de caracteres ou seus atributos - para o Gerenciador de layout para exibição. `NSTextStorage` herda de `MSMutableAttributed` cadeia de caracteres, permitindo que as alterações nos atributos de texto a ser especificado em lotes entre `BeginEditing` e `EndEditing` chamadas.

Por exemplo, o trecho de código a seguir especifica uma alteração para o primeiro plano e plano de fundo, cores, respectivamente e tem como alvo a intervalos específicos:

```csharp
textView.TextStorage.BeginEditing ();
textView.TextStorage.AddAttribute(UIStringAttributeKey.ForegroundColor, UIColor.Green, new NSRange(200, 400));
textView.TextStorage.AddAttribute(UIStringAttributeKey.BackgroundColor, UIColor.Black, new NSRange(210, 300));
textView.TextStorage.EndEditing ();
```

Depois de `EndEditing` é chamado, as alterações são enviadas para o Gerenciador de layout, que por sua vez executa qualquer layout necessária e os cálculos de renderização para o texto a ser exibido no modo de exibição.

## <a name="layout-with-exclusion-path"></a>Layout com o caminho de exclusão

TextKit também oferece suporte a layout e permite cenários complexos, como texto com várias colunas e o fluxo de texto em torno de caminhos especificados chamado *caminhos de exclusão*. Caminhos de exclusão são aplicados para o contêiner de texto, que modifica a geometria do layout do texto, fazendo com que o texto deve fluir em torno de caminhos especificados.

Adição de um caminho de exclusão requer a configuração de `ExclusionPaths` propriedade no Gerenciador de layout. A definição dessa propriedade faz com que o Gerenciador de layout invalidar o layout de texto e de fluxo de texto em torno do caminho de exclusão.

### <a name="exclusion-based-on-a-cgpath"></a>Exclusão com base em um CGPath

Considere o seguinte `UITextView` implementação subclasse:

```csharp
public class ExclusionPathView : UITextView
{
    CGPath exclusionPath;
    CGPoint initialPoint;
    CGPoint latestPoint;
    UIBezierPath bezierPath;

    public ExclusionPathView (string text)
    {
        Text = text;
        ContentInset = new UIEdgeInsets (20, 0, 0, 0);
        BackgroundColor = UIColor.White;
        exclusionPath = new CGPath ();
        bezierPath = UIBezierPath.Create ();

        LayoutManager.AllowsNonContiguousLayout = false;
    }

    public override void TouchesBegan (NSSet touches, UIEvent evt)
    {
        base.TouchesBegan (touches, evt);

        var touch = touches.AnyObject as UITouch;

        if (touch != null) {
            initialPoint = touch.LocationInView (this);
        }
    }

    public override void TouchesMoved (NSSet touches, UIEvent evt)
    {
        base.TouchesMoved (touches, evt);

        UITouch touch = touches.AnyObject as UITouch;

        if (touch != null) {
            latestPoint = touch.LocationInView (this);
            SetNeedsDisplay ();
        }
    }

    public override void TouchesEnded (NSSet touches, UIEvent evt)
    {
        base.TouchesEnded (touches, evt);

        bezierPath.CGPath = exclusionPath;
        TextContainer.ExclusionPaths = new UIBezierPath[] { bezierPath };
    }

    public override void Draw (CGRect rect)
    {
        base.Draw (rect);

        if (!initialPoint.IsEmpty) {

            using (var g = UIGraphics.GetCurrentContext ()) {

                g.SetLineWidth (4);
                UIColor.Blue.SetStroke ();

                if (exclusionPath.IsEmpty) {
                    exclusionPath.AddLines (new CGPoint[] { initialPoint, latestPoint });
                } else {
                    exclusionPath.AddLineToPoint (latestPoint);
                }

                g.AddPath (exclusionPath);
                g.DrawPath (CGPathDrawingMode.Stroke);
            }
        }
    }
}
```

Esse código adiciona suporte para o modo de exibição de texto usando gráficos de núcleo de desenho. Desde o `UITextView` classe baseia-se agora para usar TextKit para seu layout e renderização de texto, ele pode usar todos os recursos do TextKit, como definir caminhos de exclusão.

> [!IMPORTANT]
> Subclasses Este exemplo `UITextView` para adicionar suporte de desenho de toque. Subclasses `UITextView` não é necessário para obter os recursos de TextKit.



Depois que o usuário utiliza o modo de texto, o desenho `CGPath` é aplicado a um `UIBezierPath` instância definindo o `UIBezierPath.CGPath` propriedade:

```csharp
bezierPath.CGPath = exclusionPath;
```

Atualizar a linha de código a seguir faz com que o layout de texto em torno do caminho de atualização:

```csharp
TextContainer.ExclusionPaths = new UIBezierPath[] { bezierPath };
```

Captura de tela a seguir ilustra como o layout de texto muda para fluir em torno do caminho desenhado:

<!-- ![](textkit-images/exclusionpath1.png "This screenshot illustrates how the text layout changes to flow around the drawn path")--> 
![](textkit-images/exclusionpath2.png "Esta captura de tela ilustra como o layout de texto muda para fluir em torno do caminho desenhado")

Observe que o Gerenciador de layout `AllowsNonContiguousLayout` propriedade é definida como false, nesse caso. Isso faz com que o layout ser recalculado para todos os casos em que o texto é alterado. Definir isso como true pode tirar proveito do desempenho, evitando uma atualização de layout completo, especialmente no caso de documentos grandes. Entretanto, a configuração `AllowsNonContiguousLayout` para true poderia impedir que o caminho de exclusão de atualização do layout em algumas circunstâncias - por exemplo, se o texto é inserido em tempo de execução sem retorno à direita antes do caminho que está sendo definido.


## <a name="related-links"></a>Links relacionados

- [Introdução ao iOS 7 (exemplo)](https://developer.xamarin.com/samples/monotouch/IntroToiOS7)
- [Visão geral da interface do usuário do iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Backgrounding](~/ios/app-fundamentals/backgrounding/index.md)
