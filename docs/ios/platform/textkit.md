---
title: TextKit no xamarin. IOS
description: Este documento descreve como usar TextKit no xamarin. IOS. TextKit fornece recursos de layout e renderização de texto avançada.
ms.prod: xamarin
ms.assetid: 1D0477E8-CD1E-48A9-B7C8-7CA892069EFF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: f08e37d17cc32e45232d54cc4a51bb48d7ec94b1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61184495"
---
# <a name="textkit-in-xamarinios"></a>TextKit no xamarin. IOS

TextKit é uma nova API que oferece recursos de layout e renderização de texto avançada. Ele é criado sobre a estrutura de texto principal de baixo nível, mas é muito mais fácil de usar do que o texto principal.

Para disponibilizar os recursos do TextKit para controles padrão, vários controles de texto de iOS foram reimplementados usar TextKit, incluindo:

-  UITextView
-  UITextField
-  UILabel

## <a name="architecture"></a>Arquitetura

TextKit fornece uma arquitetura em camadas que separa o armazenamento de texto do layout e exibição, incluindo as classes a seguir:

-  `NSTextContainer` – Fornece o sistema de coordenadas e a geometria usada para o layout do texto.
-  `NSLayoutManager` – Dispõe texto ao transformar texto em glifos. 
-  `NSTextStorage` – Contém os dados de texto, bem como manipula atualizações de propriedade de texto de lote. Quaisquer atualizações em lotes são entregues para o Gerenciador de layout para o processamento real das alterações, como recalcular o layout e redesenho o texto.


Essas três classes são aplicadas a uma exibição que renderiza o texto. O texto interno tratamento modos de exibição, como `UITextView`, `UITextField`, e `UILabel` os tiver definido, mas você pode criar e aplicá-las a qualquer `UIView` instância também.

A figura a seguir ilustra essa arquitetura:

 ![](textkit-images/textkitarch.png "Esta figura ilustra a arquitetura de TextKit")

## <a name="text-storage-and-attributes"></a>Atributos e o armazenamento de texto

O `NSTextStorage` classe contém o texto que é exibido por uma exibição. Ele também se comunica a todas as alterações para o texto -, como alterações para caracteres ou seus atributos - para o Gerenciador de layout para exibição. `NSTextStorage` herda de `MSMutableAttributed` cadeia de caracteres, permitindo que as alterações a atributos de texto devem ser especificados em lotes entre `BeginEditing` e `EndEditing` chamadas.

Por exemplo, o trecho de código a seguir especifica uma alteração em primeiro plano e plano de fundo, cores, respectivamente e tem como alvo a intervalos específicos:

```csharp
textView.TextStorage.BeginEditing ();
textView.TextStorage.AddAttribute(UIStringAttributeKey.ForegroundColor, UIColor.Green, new NSRange(200, 400));
textView.TextStorage.AddAttribute(UIStringAttributeKey.BackgroundColor, UIColor.Black, new NSRange(210, 300));
textView.TextStorage.EndEditing ();
```

Depois de `EndEditing` é chamado, as alterações são enviadas para o Gerenciador de layout, que por sua vez executa qualquer layout necessário e os cálculos de renderização para o texto a ser exibido no modo de exibição.

## <a name="layout-with-exclusion-path"></a>Layout com o caminho de exclusão

TextKit também dá suporte a layout e permite cenários complexos, como o texto de várias coluna e o fluxo de texto em torno de caminhos especificados chamados *caminhos de exclusão*. Caminhos de exclusão são aplicados ao contêiner de texto, que modifica a geometria do layout do texto, fazendo com que o texto fluir em torno de caminhos especificados.

Adicionar um caminho de exclusão exige configuração o `ExclusionPaths` propriedade no Gerenciador de layout. A definição dessa propriedade faz com que o Gerenciador de layout invalidar o layout de texto e o texto em torno do caminho de exclusão de fluxo.

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

Esse código adiciona suporte para o modo de exibição de texto usando elementos gráficos principais de desenho. Uma vez que o `UITextView` classe baseia-se agora para usar TextKit para seu layout e renderização de texto, ele pode usar todos os recursos do TextKit, como a configuração de caminhos de exclusão.

> [!IMPORTANT]
> Subclasses esse exemplo `UITextView` para adicionar suporte de desenho de toque. Criando subclasses `UITextView` não é necessário para obter os recursos de TextKit.



Depois que o usuário desenha na exibição de texto, o desenhado `CGPath` é aplicado a um `UIBezierPath` instância definindo a `UIBezierPath.CGPath` propriedade:

```csharp
bezierPath.CGPath = exclusionPath;
```

Atualizando a seguinte linha de código faz com que o layout de texto em torno do caminho de atualização:

```csharp
TextContainer.ExclusionPaths = new UIBezierPath[] { bezierPath };
```

Captura de tela a seguir ilustra como o layout de texto é alterado para fluir em torno do caminho desenhado:

<!-- ![](textkit-images/exclusionpath1.png "This screenshot illustrates how the text layout changes to flow around the drawn path")--> 
![](textkit-images/exclusionpath2.png "Esta captura de tela que ilustra como o layout de texto é alterado para fluir em torno do caminho desenhado")

Observe que o Gerenciador de layout `AllowsNonContiguousLayout` propriedade é definida como false, nesse caso. Isso faz com que o layout a ser recalculado para todos os casos em que o texto é alterado. Definir isso como true pode beneficiar o desempenho, evitando uma atualização de layout completo, especialmente no caso de documentos grandes. No entanto, definir `AllowsNonContiguousLayout` para true impediria o caminho de exclusão de Atualizando o layout em algumas circunstâncias - por exemplo, se o texto é inserido em tempo de execução sem retorno à direita antes do caminho que está sendo definido.


## <a name="related-links"></a>Links relacionados

- [Introdução ao iOS 7 (amostra)](https://developer.xamarin.com/samples/monotouch/IntroToiOS7)
- [Visão geral da interface do usuário do iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Backgrounding](~/ios/app-fundamentals/backgrounding/index.md)
