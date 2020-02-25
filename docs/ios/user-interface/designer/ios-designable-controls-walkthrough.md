---
title: Como usar controles personalizados com o iOS Designer
description: Este documento descreve como criar um controle personalizado e usá-lo com o Xamarin Designer para iOS. Ele mostra como tornar o controle disponível na caixa de ferramentas do designer do iOS, implementar o controle para que ele seja renderizado corretamente e o tempo de design e muito mais.
ms.prod: xamarin
ms.assetid: 9032B32E-97BD-4DA6-9955-811B84682578
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 4b8c72da1e280b83e215bca9316bc0b9de99402c
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73003796"
---
# <a name="using-custom-controls-with-the-ios-designer"></a>Como usar controles personalizados com o iOS Designer

## <a name="requirements"></a>Requisitos

O Xamarin Designer para iOS está disponível no Visual Studio para Mac e no Visual Studio 2017 e posterior no Windows.

Este guia pressupõe familiaridade com o conteúdo abordado nos [guias de introdução](~/ios/get-started/index.md).

## <a name="walkthrough"></a>Passo a passo

> [!IMPORTANT]
> A partir do Xamarin. Studio 5,5, a maneira como os controles personalizados são criados é um pouco diferente das versões anteriores. Para criar um controle personalizado, a interface de `IComponent` é necessária (com os métodos de implementação associados) ou a classe pode ser anotada com `[DesignTimeVisible(true)]`. O último método está sendo usado no exemplo a seguir.

1. Crie uma nova solução do aplicativo de **> do IOS > modelo de > C# de aplicativo de exibição única** , nomeie-o`ScratchTicket`e continue pelo assistente de novo projeto:

    [![](ios-designable-controls-walkthrough-images/01new.png "Create a new solution")](ios-designable-controls-walkthrough-images/01new.png#lightbox)

1. Crie um novo arquivo de classe vazio chamado `ScratchTicketView`:

    [![](ios-designable-controls-walkthrough-images/02new.png "Create a new ScratchTicketView class")](ios-designable-controls-walkthrough-images/02new.png#lightbox)

1. Adicione o seguinte código para `ScratchTicketView` classe:

    ```csharp
    using System;
    using System.ComponentModel;
    using CoreGraphics;
    using Foundation;
    using UIKit;

    namespace ScratchTicket
    {
        [Register("ScratchTicketView"), DesignTimeVisible(true)]
        public class ScratchTicketView : UIView
        {
            CGPath path;
            CGPoint initialPoint;
            CGPoint latestPoint;
            bool startNewPath = false;
            UIImage image;

            [Export("Image"), Browsable(true)]
            public UIImage Image
            {
                get { return image; }
                set
                {
                    image = value;
                    SetNeedsDisplay();
                }
            }

            public ScratchTicketView(IntPtr p)
                : base(p)
            {
                Initialize();
            }

            public ScratchTicketView()
            {
                Initialize();
            }

            void Initialize()
            {
                initialPoint = CGPoint.Empty;
                latestPoint = CGPoint.Empty;
                BackgroundColor = UIColor.Clear;
                Opaque = false;
                path = new CGPath();
                SetNeedsDisplay();
            }

            public override void TouchesBegan(NSSet touches, UIEvent evt)
            {
                base.TouchesBegan(touches, evt);

                var touch = touches.AnyObject as UITouch;

                if (touch != null)
                {
                    initialPoint = touch.LocationInView(this);
                }
            }

            public override void TouchesMoved(NSSet touches, UIEvent evt)
            {
                base.TouchesMoved(touches, evt);

                var touch = touches.AnyObject as UITouch;

                if (touch != null)
                {
                    latestPoint = touch.LocationInView(this);
                    SetNeedsDisplay();
                }
            }

            public override void TouchesEnded(NSSet touches, UIEvent evt)
            {
                base.TouchesEnded(touches, evt);
                startNewPath = true;
            }

            public override void Draw(CGRect rect)
            {
                base.Draw(rect);

                using (var g = UIGraphics.GetCurrentContext())
                {
                    if (image != null)
                        g.SetFillColor((UIColor.FromPatternImage(image).CGColor));
                    else
                        g.SetFillColor(UIColor.LightGray.CGColor);
                    g.FillRect(rect);

                    if (!initialPoint.IsEmpty)
                    {
                        g.SetLineWidth(20);
                        g.SetBlendMode(CGBlendMode.Clear);
                        UIColor.Clear.SetColor();

                        if (path.IsEmpty || startNewPath)
                        {
                            path.AddLines(new CGPoint[] { initialPoint, latestPoint });
                            startNewPath = false;
                        }
                        else
                        {
                            path.AddLineToPoint(latestPoint);
                        }

                        g.SetLineCap(CGLineCap.Round);
                        g.AddPath(path);
                        g.DrawPath(CGPathDrawingMode.Stroke);
                    }
                }
            }
        }
    }
    ```

1. Adicione os arquivos de `FillTexture.png`, `FillTexture2.png` e `Monkey.png` (disponíveis [no GitHub](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)) à pasta de **recursos** .

1. Clique duas vezes no arquivo `Main.storyboard` para abri-lo no designer:

    [![](ios-designable-controls-walkthrough-images/03new.png "The iOS Designer")](ios-designable-controls-walkthrough-images/03new.png#lightbox)

1. Arraste/solte um **modo de exibição de imagem** da **caixa de ferramentas** para a exibição no storyboard.

    [![](ios-designable-controls-walkthrough-images/04new.png "An Image View added to the layout")](ios-designable-controls-walkthrough-images/04new.png#lightbox)

1. Selecione a **exibição de imagem** e altere sua propriedade **Image** para `Monkey.png`.

    [![](ios-designable-controls-walkthrough-images/05new.png "Setting Image View Image property to Monkey.png")](ios-designable-controls-walkthrough-images/05new.png#lightbox)

1. Como estamos usando classes de tamanho, precisaremos restringir essa exibição de imagem. Clique na imagem duas vezes para colocá-la no modo de restrição. Vamos restringi-lo ao centro clicando no identificador de fixação central e alinhando-o vertical e horizontalmente:

    [![](ios-designable-controls-walkthrough-images/06new.png "Centering the image")](ios-designable-controls-walkthrough-images/06new.png#lightbox)

1. Para restringir a altura e a largura, clique nas alças de fixação de tamanho (as alças moldadas ' Bone ') e selecione largura e altura, respectivamente:

    [![](ios-designable-controls-walkthrough-images/07new.png "Adding Constraints")](ios-designable-controls-walkthrough-images/07new.png#lightbox)

1. Atualize o quadro com base nas restrições clicando no botão atualizar na barra de ferramentas:

    [![](ios-designable-controls-walkthrough-images/08new.png "The Constraints toolbar")](ios-designable-controls-walkthrough-images/08new.png#lightbox)

1. Em seguida, compile o projeto para que a **exibição de tíquete transitório** apareça em **componentes personalizados** na caixa de ferramentas:

    [![](ios-designable-controls-walkthrough-images/09new.png "The Custom Components toolbox")](ios-designable-controls-walkthrough-images/09new.png#lightbox)

1. Arraste e solte um **modo de exibição de tíquete transitório** para que ele apareça sobre a imagem do macaco. Ajuste as alças de arrastar para que a exibição de tíquete transitório cubra completamente o princípio, conforme mostrado abaixo:

    [![](ios-designable-controls-walkthrough-images/10new.png "A Scratch Ticket View over the Image View")](ios-designable-controls-walkthrough-images/10new.png#lightbox)

1. Restrinja a exibição do tíquete transitório ao modo de exibição de imagem desenhando um retângulo delimitador para selecionar ambas as exibições. Selecione as opções para restringi-lo à largura, altura, centro e central e atualizar quadros com base em restrições, conforme mostrado abaixo:

    [![](ios-designable-controls-walkthrough-images/11new.png "Centering and adding Constraints")](ios-designable-controls-walkthrough-images/11new.png#lightbox)

1. Executar o aplicativo e "riscar" a imagem para revelar o macaco.

    [![](ios-designable-controls-walkthrough-images/10-app.png "A sample app run")](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="adding-design-time-properties"></a>Adicionando propriedades de tempo de design

O designer também inclui suporte a tempo de design para controles personalizados de tipo de propriedade Numeric, Enumeration, String, bool, CGSize, UIColor e UIImage. Para demonstrar, vamos adicionar uma propriedade à `ScratchTicketView` para definir a imagem que é "riscada".

Adicione o seguinte código à classe `ScratchTicketView` para a propriedade:

```csharp
[Export("Image"), Browsable(true)]
public UIImage Image
{
    get { return image; }
    set {
            image = value;
              SetNeedsDisplay ();
        }
}
```

Talvez também queiramos adicionar uma verificação nula ao método `Draw`, desta forma:

```csharp
public override void Draw(CGRect rect)
{
    base.Draw(rect);

    using (var g = UIGraphics.GetCurrentContext())
    {
        if (image != null)
            g.SetFillColor ((UIColor.FromPatternImage (image).CGColor));
        else
            g.SetFillColor (UIColor.LightGray.CGColor);

        g.FillRect(rect);

        if (!initialPoint.IsEmpty)
        {
             g.SetLineWidth(20);
             g.SetBlendMode(CGBlendMode.Clear);
             UIColor.Clear.SetColor();

             if (path.IsEmpty || startNewPath)
             {
                 path.AddLines(new CGPoint[] { initialPoint, latestPoint });
                 startNewPath = false;
             }
             else
             {
                 path.AddLineToPoint(latestPoint);
             }

             g.SetLineCap(CGLineCap.Round);
             g.AddPath(path);
             g.DrawPath(CGPathDrawingMode.Stroke);
        }
    }
}
```

Incluir um `ExportAttribute` e um `BrowsableAttribute` com o argumento definido como `true` resulta na exibição da propriedade no painel de **Propriedades** do designer. Alterar a propriedade para outra imagem incluída no projeto, como `FillTexture2.png`, resulta na atualização do controle em tempo de design, conforme mostrado abaixo:

 [![](ios-designable-controls-walkthrough-images/11-customproperty.png "Editing Design Time properties")](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="summary"></a>Resumo

Neste artigo, mostramos como criar um controle personalizado, bem como consumi-lo em um aplicativo iOS usando o designer do iOS. Vimos como criar e criar o controle para torná-lo disponível para um aplicativo na **caixa de ferramentas**do designer. Além disso, vimos como implementar o controle de modo que ele seja renderizado corretamente no tempo de design e no runtime, bem como expor propriedades de controle personalizadas no designer.

## <a name="related-links"></a>Links relacionados

- [ScratchTicket (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/scratchticket)
- [imagens necessárias (exemplo)](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)
