---
title: Usando controles personalizados com o Designer do iOS
description: Este documento descreve como criar um controle personalizado e usá-lo com o Designer de Xamarin para iOS. Ele mostra como tornar o controle disponíveis na caixa de ferramentas do Designer do iOS, implementar o controle para que ele renderiza corretamente e tempo de design e muito mais.
ms.prod: xamarin
ms.assetid: 9032B32E-97BD-4DA6-9955-811B84682578
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: dae675d65cb2be93ac828a1aebe560354630ab54
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790159"
---
# <a name="using-custom-controls-with-the-ios-designer"></a>Usando controles personalizados com o Designer do iOS

## <a name="requirements"></a>Requisitos

O Designer de Xamarin para iOS está disponível no Visual Studio para Mac e Visual Studio 2015 e 2017 no Windows.

Este guia presume familiaridade com o conteúdo abordados o [guias de Introdução](~/ios/get-started/index.md).

## <a name="walkthrough"></a>Passo a passo

> [!IMPORTANT]
> A maneira na qual os controles personalizados são criados a partir de Xamarin.Studio 5.5, é ligeiramente diferente para versões anteriores. Para criar um controle personalizado, ou o `IComponent` interface é necessária (com os métodos de implementação associada) ou a classe pode ser ser anotada com `[DesignTimeVisible(true)]`. O último método está sendo usado no exemplo a seguir de instruções passo a passo.


1. Criar uma nova solução do **iOS > aplicativo > único aplicativo de exibição > c#** modelo, nomeie-o `ScratchTicket`e continue com o Assistente do novo projeto:

    [![](ios-designable-controls-walkthrough-images/01new.png "Criar uma nova solução")](ios-designable-controls-walkthrough-images/01new.png#lightbox)

1. Criar um novo arquivo de classe vazia denominado `ScratchTicketView`:

    [![](ios-designable-controls-walkthrough-images/02new.png "Criar uma nova classe ScratchTicketView")](ios-designable-controls-walkthrough-images/02new.png#lightbox)

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


1. Adicionar o `FillTexture.png`, `FillTexture2.png` e `Monkey.png` arquivos (disponível [do GitHub](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)) para o **recursos** pasta.
    
1. Clique duas vezes o `Main.storyboard` arquivo para abri-lo no designer:

    [![](ios-designable-controls-walkthrough-images/03new.png "O Designer do iOS")](ios-designable-controls-walkthrough-images/03new.png#lightbox)


1. Arrastar/soltar um **imagem** do **caixa de ferramentas** para o modo de exibição no storyboard.

    [![](ios-designable-controls-walkthrough-images/04new.png "Uma exibição de imagem adicionados ao layout do")](ios-designable-controls-walkthrough-images/04new.png#lightbox)


1. Selecione o **imagem** e altere seu **imagem** propriedade `Monkey.png`.

    [! [] (ios-projetáveis-controles-passo a passo-imagens/05new.png "propriedade de imagem de exibição de imagem de configuração para Monkey.png)](ios-designable-controls-walkthrough-images/05new.png#lightbox)

    
1. Como estamos usando classes de tamanho precisaremos restringir esse modo de exibição de imagem. Clique na imagem duas vezes para colocá-lo em modo de restrição. Vamos restringi-la para o Centro de clicando na alça de fixação center e alinhe-vertical e horizontalmente:

    [![](ios-designable-controls-walkthrough-images/06new.png "Centralizar a imagem")](ios-designable-controls-walkthrough-images/06new.png#lightbox)

1. Para restringir a altura e largura, clique nas alças de fixação de tamanho (os identificadores 'bone' formatada) e selecione a largura e altura respectivamente:

    [![](ios-designable-controls-walkthrough-images/07new.png "Adicionar restrições")](ios-designable-controls-walkthrough-images/07new.png#lightbox)


1. Atualize o quadro com base em restrições clicando no botão Atualizar na barra de ferramentas:

    [![](ios-designable-controls-walkthrough-images/08new.png "A barra de ferramentas de restrições")](ios-designable-controls-walkthrough-images/08new.png#lightbox)


1. Em seguida, compile o projeto para que o **rascunho tíquete exibição** aparecerá em **componentes personalizados** na caixa de ferramentas:

    [![](ios-designable-controls-walkthrough-images/09new.png "A caixa de ferramentas de componentes personalizados")](ios-designable-controls-walkthrough-images/09new.png#lightbox)


1. Arraste e solte um **rascunho tíquete exibição** para que ele apareça sobre a imagem monkey. Ajuste as alças de arrastar para a exibição de tíquete rascunho abrange o monkey completamente, conforme mostrado abaixo:

    [![](ios-designable-controls-walkthrough-images/10new.png "Um modo de exibição de rascunho do tíquete sobre a exibição de imagem")](ios-designable-controls-walkthrough-images/10new.png#lightbox)

1. Restringir a exibição de tíquete de risco para o modo de exibição de imagem ao desenhar um retângulo para selecionar ambos os modos de exibição. Selecione as opções para restringi-la para os quadros de largura, altura, centro e intermediária e atualização com base em restrições, conforme mostrado abaixo:

    [![](ios-designable-controls-walkthrough-images/11new.png "Centralizar e adicionar restrições")](ios-designable-controls-walkthrough-images/11new.png#lightbox)


1. Execute o aplicativo e "scratch off" a imagem para revelar o monkey.

    [![](ios-designable-controls-walkthrough-images/10-app.png "Executar um aplicativo de exemplo")](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="adding-design-time-properties"></a>Adicionando propriedades de tempo de Design

O designer também inclui o suporte de tempo de design para controles personalizados de tipo numérico da propriedade, enumeração, string, bool, CGSize, UIColor e UIImage. Para demonstrar, vamos adicionar uma propriedade para o `ScratchTicketView` para definir a imagem que é "pequena off."

Adicione o seguinte código para o `ScratchTicketView` classe da propriedade:

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

Podemos também querer adicionar uma verificação de nulos para a `Draw` método, da seguinte forma:

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

Incluindo um `ExportAttribute` e um `BrowsableAttribute` com o argumento definido como `true` resulta na propriedade que está sendo exibida no designer de **propriedade** painel. Alterar a propriedade de outra imagem incluída com o projeto, como `FillTexture2.png`, resulta em atualização de controle em tempo de design, conforme mostrado abaixo:

 [![](ios-designable-controls-walkthrough-images/11-customproperty.png "Editar propriedades de tempo de Design")](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="summary"></a>Resumo

Este artigo percorremos como criar um controle personalizado, bem como consumi-lo em um aplicativo do iOS usando o designer de iOS. Vimos como criar e compilar o controle para disponibilizá-lo para um aplicativo no designer de **caixa de ferramentas**. Além disso, vimos como implementar o controle, de modo que ele renderiza corretamente no tempo de design e tempo de execução, bem como expor propriedades de controle personalizado no designer.



## <a name="related-links"></a>Links relacionados

- [ScratchTicket (exemplo)](https://developer.xamarin.com/samples/monotouch/ScratchTicket/)
- [imagens necessárias (exemplo)](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)
