---
title: Como usar controles personalizados com o iOS Designer
description: Este documento descreve como criar um controle personalizado e usá-lo com o Xamarin Designer para iOS. Ele mostra como tornar o controle disponível na caixa de ferramentas do designer do iOS, implementar o controle para que ele seja renderizado corretamente e o tempo de design e muito mais.
ms.prod: xamarin
ms.assetid: 9032B32E-97BD-4DA6-9955-811B84682578
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: f4b161159423bc7e5d6d99e9dfd4407532106979
ms.sourcegitcommit: d1f0e0a9100548cfe0960ed2225b979cc1d7c28f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96439447"
---
# <a name="using-custom-controls-with-the-ios-designer"></a>Como usar controles personalizados com o iOS Designer

> [!WARNING]
> O designer do iOS começará a ser dividido no Visual Studio 2019 versão 16,8 e no Visual Studio 2019 para Mac versão 8,8.
> A maneira recomendada para criar interfaces de usuário do iOS é diretamente em um Mac que executa o Xcode. Para obter mais informações, consulte [projetando interfaces do usuário com o Xcode](../storyboards/index.md). 

## <a name="requirements"></a>Requisitos

O Xamarin Designer para iOS está disponível no Visual Studio para Mac e no Visual Studio 2017 e posterior no Windows.

Este guia pressupõe familiaridade com o conteúdo abordado nos [guias de introdução](~/ios/get-started/index.md).

## <a name="walkthrough"></a>Passo a passo

> [!IMPORTANT]
> A partir do Xamarin. Studio 5,5, a maneira como os controles personalizados são criados é um pouco diferente das versões anteriores. Para criar um controle personalizado, a `IComponent` interface é necessária (com os métodos de implementação associados) ou a classe pode ser anotada `[DesignTimeVisible(true)]` . O último método está sendo usado no exemplo a seguir.

1. Crie uma nova solução do aplicativo **> do iOS > aplicativo de exibição única > modelo C#** , nomeie-o `ScratchTicket` e continue pelo assistente de novo projeto:

    [![Criar uma nova solução](ios-designable-controls-walkthrough-images/01new.png)](ios-designable-controls-walkthrough-images/01new.png#lightbox)

1. Crie um novo arquivo de classe vazio chamado `ScratchTicketView` :

    [![Criar uma nova classe ScratchTicketView](ios-designable-controls-walkthrough-images/02new.png)](ios-designable-controls-walkthrough-images/02new.png#lightbox)

1. Adicione o seguinte código para a `ScratchTicketView` classe:

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

1. Adicione os `FillTexture.png` `FillTexture2.png` arquivos e `Monkey.png` (disponíveis [no GitHub](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)) à pasta **recursos** .

1. Clique duas vezes no `Main.storyboard` arquivo para abri-lo no designer:

    [![O designer do iOS](ios-designable-controls-walkthrough-images/03new.png)](ios-designable-controls-walkthrough-images/03new.png#lightbox)

1. Arraste/solte um **modo de exibição de imagem** da **caixa de ferramentas** para a exibição no storyboard.

    [![Uma exibição de imagem adicionada ao layout](ios-designable-controls-walkthrough-images/04new.png)](ios-designable-controls-walkthrough-images/04new.png#lightbox)

1. Selecione a **exibição de imagem** e altere sua propriedade **Image** para `Monkey.png` .

    [![Definindo Propriedade Image View Image para Monkey.png](ios-designable-controls-walkthrough-images/05new.png)](ios-designable-controls-walkthrough-images/05new.png#lightbox)

1. Como estamos usando classes de tamanho, precisaremos restringir essa exibição de imagem. Clique na imagem duas vezes para colocá-la no modo de restrição. Vamos restringi-lo ao centro clicando no identificador de fixação central e alinhando-o vertical e horizontalmente:

    [![Centralizando a imagem](ios-designable-controls-walkthrough-images/06new.png)](ios-designable-controls-walkthrough-images/06new.png#lightbox)

1. Para restringir a altura e a largura, clique nas alças de fixação de tamanho (as alças moldadas ' Bone ') e selecione largura e altura, respectivamente:

    [![Adicionando restrições](ios-designable-controls-walkthrough-images/07new.png)](ios-designable-controls-walkthrough-images/07new.png#lightbox)

1. Atualize o quadro com base nas restrições clicando no botão atualizar na barra de ferramentas:

    [![A barra de ferramentas restrições](ios-designable-controls-walkthrough-images/08new.png)](ios-designable-controls-walkthrough-images/08new.png#lightbox)

1. Em seguida, compile o projeto para que a **exibição de tíquete transitório** apareça em **componentes personalizados** na caixa de ferramentas:

    [![A caixa de ferramentas de componentes personalizados](ios-designable-controls-walkthrough-images/09new.png)](ios-designable-controls-walkthrough-images/09new.png#lightbox)

1. Arraste e solte um **modo de exibição de tíquete transitório** para que ele apareça sobre a imagem do macaco. Ajuste as alças de arrastar para que a exibição de tíquete transitório cubra completamente o princípio, conforme mostrado abaixo:

    [![Uma exibição de tíquete transitório sobre a exibição de imagem](ios-designable-controls-walkthrough-images/10new.png)](ios-designable-controls-walkthrough-images/10new.png#lightbox)

1. Restrinja a exibição do tíquete transitório ao modo de exibição de imagem desenhando um retângulo delimitador para selecionar ambas as exibições. Selecione as opções para restringi-lo à largura, altura, centro e central e atualizar quadros com base em restrições, conforme mostrado abaixo:

    [![Centralizando e adicionando restrições](ios-designable-controls-walkthrough-images/11new.png)](ios-designable-controls-walkthrough-images/11new.png#lightbox)

1. Executar o aplicativo e "riscar" a imagem para revelar o macaco.

    [![Uma execução de aplicativo de exemplo](ios-designable-controls-walkthrough-images/10-app.png)](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="adding-design-time-properties"></a>Adicionando propriedades de Design-Time

O designer também inclui suporte a tempo de design para controles personalizados de tipo de propriedade Numeric, Enumeration, String, bool, CGSize, UIColor e UIImage. Para demonstrar, vamos adicionar uma propriedade ao `ScratchTicketView` para definir a imagem que está "riscada".

Adicione o seguinte código à `ScratchTicketView` classe para a propriedade:

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

Talvez também queiramos adicionar uma verificação nula ao `Draw` método, da seguinte forma:

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

Incluir um `ExportAttribute` e `BrowsableAttribute` com o argumento definido como `true` Results na propriedade que está sendo exibida no painel de **Propriedades** do designer. Alterar a propriedade para outra imagem incluída no projeto, como `FillTexture2.png` , resulta na atualização do controle em tempo de design, conforme mostrado abaixo:

 [![Editando propriedades de tempo de design](ios-designable-controls-walkthrough-images/11-customproperty.png)](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="summary"></a>Resumo

Neste artigo, mostramos como criar um controle personalizado, bem como consumi-lo em um aplicativo iOS usando o designer do iOS. Vimos como criar e criar o controle para torná-lo disponível para um aplicativo na **caixa de ferramentas** do designer. Além disso, vimos como implementar o controle de modo que ele seja renderizado corretamente no tempo de design e no runtime, bem como expor propriedades de controle personalizadas no designer.

## <a name="related-links"></a>Links Relacionados

- [ScratchTicket (exemplo)](/samples/xamarin/ios-samples/scratchticket)
- [Imagens necessárias (exemplo)](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)
