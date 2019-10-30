---
title: TextKit no Xamarin. iOS
description: Este documento descreve como usar o TextKit no Xamarin. iOS. O TextKit fornece recursos avançados de layout de texto e renderização.
ms.prod: xamarin
ms.assetid: 1D0477E8-CD1E-48A9-B7C8-7CA892069EFF
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 6c1dee464de1f7ba708b1f7d60affc1616e71ee9
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031414"
---
# <a name="textkit-in-xamarinios"></a>TextKit no Xamarin. iOS

TextKit é uma nova API que oferece recursos avançados de layout de texto e renderização. Ele se baseia na estrutura de texto principal de baixo nível, mas é muito mais fácil de usar do que o texto principal.

Para tornar os recursos do TextKit disponíveis para os controles padrão, vários controles de texto do iOS foram reimplementados para usar o TextKit, incluindo:

- UITextView
- UITextField
- UILabel

## <a name="architecture"></a>Arquitetura

O TextKit fornece uma arquitetura em camadas que separa o armazenamento de texto do layout e da exibição, incluindo as seguintes classes:

- `NSTextContainer` – fornece o sistema de coordenadas e a geometria usada para layout de texto.
- `NSLayoutManager` – apresenta o texto virando o texto em glifos.
- `NSTextStorage` – armazena os dados de texto, bem como manipula as atualizações de propriedade de texto em lotes. Todas as atualizações do lote são enviadas ao Gerenciador de layout para o processamento real das alterações, como recalcular o layout e redesenhar o texto.

Essas três classes são aplicadas a uma exibição que renderiza texto. As exibições de manipulação de texto internas, como `UITextView`, `UITextField`e `UILabel` já as têm definidas, mas você também pode criá-las e aplicá-las a qualquer instância de `UIView`.

A figura a seguir ilustra essa arquitetura:

 ![](textkit-images/textkitarch.png "This figure illustrates the TextKit architecture")

## <a name="text-storage-and-attributes"></a>Atributos e armazenamento de texto

A classe `NSTextStorage` contém o texto que é exibido por uma exibição. Ele também comunica quaisquer alterações no texto, como alterações em caracteres ou seus atributos, para o Gerenciador de layout para exibição. `NSTextStorage` herda de `MSMutableAttributed` cadeia de caracteres, permitindo que as alterações nos atributos de texto sejam especificadas em lotes entre as chamadas `BeginEditing` e `EndEditing`.

Por exemplo, o trecho de código a seguir especifica uma alteração nas cores de primeiro plano e de segundo plano, respectivamente, e tem como alvo intervalos específicos:

```csharp
textView.TextStorage.BeginEditing ();
textView.TextStorage.AddAttribute(UIStringAttributeKey.ForegroundColor, UIColor.Green, new NSRange(200, 400));
textView.TextStorage.AddAttribute(UIStringAttributeKey.BackgroundColor, UIColor.Black, new NSRange(210, 300));
textView.TextStorage.EndEditing ();
```

Depois que `EndEditing` é chamado, as alterações são enviadas ao Gerenciador de layout, que, por sua vez, executa qualquer layout necessário e os cálculos de renderização para o texto a ser exibido na exibição.

## <a name="layout-with-exclusion-path"></a>Layout com caminho de exclusão

O TextKit também dá suporte a layout e permite cenários complexos, como texto de várias colunas e texto de fluxo ao contrário de caminhos especificados, chamados de *caminhos de exclusão*. Os caminhos de exclusão são aplicados ao contêiner de texto, que modifica a geometria do layout do texto, fazendo com que o texto flua em volta dos caminhos especificados.

A adição de um caminho de exclusão requer a definição da propriedade `ExclusionPaths` no Gerenciador de layout. Definir essa propriedade faz com que o Gerenciador de layout invalidasse o layout de texto e flua o texto em volta do caminho de exclusão.

### <a name="exclusion-based-on-a-cgpath"></a>Exclusão baseada em um CGPath

Considere a seguinte implementação de subclasse de `UITextView`:

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

Esse código adiciona suporte para desenhar na exibição de texto usando gráficos de núcleo. Como a classe `UITextView` agora é criada para usar TextKit para seu processamento e layout de texto, ela pode usar todos os recursos de TextKit, como a configuração de caminhos de exclusão.

> [!IMPORTANT]
> Este exemplo subclasses `UITextView` para adicionar suporte a desenho de toque. A `UITextView` de subclasses não é necessária para obter os recursos do TextKit.

Depois que o usuário desenha na exibição de texto, a `CGPath` desenhada é aplicada a uma instância de `UIBezierPath` Configurando a propriedade `UIBezierPath.CGPath`:

```csharp
bezierPath.CGPath = exclusionPath;
```

Atualizar a linha de código a seguir faz com que a atualização do layout de texto fique em torno do caminho:

```csharp
TextContainer.ExclusionPaths = new UIBezierPath[] { bezierPath };
```

A captura de tela a seguir ilustra como o layout de texto muda para o fluxo ao redor do caminho desenhado:

<!-- ![](textkit-images/exclusionpath1.png "This screenshot illustrates how the text layout changes to flow around the drawn path")-->
![](textkit-images/exclusionpath2.png "This screenshot illustrates how the text layout changes to flow around the drawn path")

Observe que a propriedade `AllowsNonContiguousLayout` do Gerenciador de layout está definida como false nesse caso. Isso faz com que o layout seja recalculado para todos os casos em que o texto é alterado. Definir isso como verdadeiro pode beneficiar o desempenho ao evitar uma atualização de layout completo, especialmente no caso de documentos grandes. No entanto, definir `AllowsNonContiguousLayout` como true impediria que o caminho de exclusão atualizasse o layout em algumas circunstâncias, por exemplo, se o texto for inserido em tempo de execução sem um retorno de carro à direita antes do caminho que está sendo definido.

## <a name="related-links"></a>Links relacionados

- [Introdução ao iOS 7 (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/introtoios7)
- [Visão geral da interface do usuário do iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Backgrounding](~/ios/app-fundamentals/backgrounding/index.md)
