---
title: Xamarin. Forms usando o Visual Basic.NET
description: Modelo de projeto de PCL do xamarin. Forms pode ser modificado para usar o Visual Basic para o assembly principal, efetivamente, permitindo que você crie aplicativos móveis de plataforma cruzada usando o VB.NET.
ms.prod: xamarin
ms.assetid: da4b4ba9-9205-47dc-8bae-23272ede2c50
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: f397cf595a9ae151c5f105341733b2c57023fe99
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61282233"
---
# <a name="xamarinforms-using-visual-basicnet"></a>Xamarin. Forms usando o Visual Basic.NET

Xamarin não oferece suporte a Visual Basic diretamente – siga as instruções nesta página para criar uma solução do xamarin. Forms PCL de c# e, em seguida, substituir o projeto PCL de código comum com o Visual Basic.

[![](xamarin-forms-images/hero-sml.png "Criar uma solução do xamarin. Forms PCL e, em seguida, substituir o projeto PCL de código comum com o Visual Basic")](xamarin-forms-images/hero.png#lightbox)

> [!NOTE]
> Você deve usar o Visual Studio no Windows para o programa com o Visual Basic.

## <a name="xamarinforms-with-visual-basic-walkthrough"></a>Xamarin. Forms com instruções passo a passo do Visual Basic

Siga estas etapas para criar um projeto xamarin. Forms simples que usa o Visual Basic:

1. Criar um novo *xamarin. Forms c#* solução que usa Portable Class Libraries (PCL).
Vá para **arquivo > Novo projeto** e, nas **novo projeto** janela navegue até **instalado > Modelos > Visual C# > plataforma cruzada** , em seguida, escolha **Cross Platform App (xamarin. Forms ou nativo) > xamarin. Forms**.

2. Clique com botão direito na solução e **Adicionar > Novo projeto**.

3. Escolha o **Visual Basic > biblioteca de classes (portátil)** tipo de projeto:

   [![](xamarin-forms-images/add-vb-2-sml.png "Adicionar novo projeto de biblioteca de classes portátil")](xamarin-forms-images/add-vb-2.png#lightbox)

4. Selecione as plataformas, conforme mostrado para configurar o perfil de PCL correto (certifique-se de incluir o xamarin. IOS e xamarin. Android):

   ![](xamarin-forms-images/add-vb-3-sml.png "Escolha as plataformas para dar suporte")

5. Clique com botão direito no projeto do Visual Basic e escolha **propriedades**, em seguida, altere o **namespace padrão** para corresponder ao existente c# projetos:

   ![](xamarin-forms-images/add-vb-4s-sml.png "Verifique se que o namespace de raiz de Visual Basic corresponde ao aplicativo xamarin. Forms")

6. Clique com botão direito no novo projeto do Visual Basic e escolha **gerenciar pacotes Nuget**, em seguida, instale **xamarin. Forms** e feche a janela do Gerenciador de pacote.

   [![](xamarin-forms-images/add-vb-4-sml.png "Formulários e fechar a janela do Gerenciador de pacotes")](xamarin-forms-images/add-vb-4.png#lightbox)

7. Renomeie o padrão **Class1** arquivo *e* classe `App`:

   [![](xamarin-forms-images/add-vb-5-sml.png "Renomeie o arquivo Class1 de padrão e a classe para o aplicativo")](xamarin-forms-images/add-vb-5.png#lightbox)

8. Cole o seguinte código para o **App.vb** arquivo, que se tornará o ponto de partida do aplicativo xamarin. Forms. Lembre-se de incluir `Imports Xamarin.Forms` e adicione `Inherits Application` à classe:

    ```vb 
    Imports Xamarin.Forms

    Public Class App
        Inherits Application

        Public Sub New()
            Dim label = New Label With {.HoriztonalTextAlignment = TextAlignment.Center,
                                        .FontSize = Device.GetNamedSize(NamedSize.Medium, GetType(Label)),
                                        .Text = "Welcome to Xamarin.Forms with Visual Basic.NET"}

            Dim stack = New StackLayout With {
                .VerticalOptions = LayoutOptions.Center
            }
            stack.Children.Add(label)

            Dim page = New ContentPage
            page.Content = stack
            MainPage = page

        End Sub

    End Class
    ```

9. Agora precisamos apontar os projetos do iOS e Android no novo projeto do Visual Basic.
Clique com botão direito no **referências** nó no iOS e projetos do Android para abrir o **Gerenciador de referências**. Escala de cancelar o C# biblioteca portátil e escala a biblioteca portátil do VB (não se esqueça, faça isso para projetos Android e iOS).

   [![](xamarin-forms-images/add-vb-8-sml.png "Remover a referência de projeto antigo, adicione a referência do Visual Basic")](xamarin-forms-images/add-vb-8.png#lightbox)

10. Exclua o projeto portátil do c#. Adicionar novo **. vb** out de arquivos para compilar seu aplicativo xamarin. Forms. Um modelo para o novo `ContentPage`s no Visual Basic é mostrado abaixo:

    ```vb
    Imports Xamarin.Forms

    Public Class Page2
    Inherits ContentPage

        Public Sub New()
            Dim label = New Label With {.HoriztonalTextAlignment = TextAlignment.Center,
                                        .FontSize = Device.GetNamedSize(NamedSize.Medium, GetType(Label)),
                                        .Text = "Visual Basic ContentPage"}

            Dim stack = New StackLayout With {
                .VerticalOptions = LayoutOptions.Center
            }
            stack.Children.Add(label)

            Content = stack
        End Sub
    End Class
    ```

## <a name="limitations-of-visual-basic-in-xamarinforms"></a>Limitações do Visual Basic no xamarin. Forms

Conforme mencionado na [portátil Visual Basic.NET página](~/cross-platform/platform/visual-basic/index.md), Xamarin não oferece suporte a linguagem Visual Basic. Isso significa que há algumas limitações sobre onde você pode usar o Visual Basic:

 - Renderizadores personalizados não podem ser escritos em Visual Basic, eles devem ser escritos em c# nos projetos de plataforma nativa.

 - As implementações de serviço de dependência não podem ser escritas em Visual Basic, eles devem ser escritos em c# nos projetos de plataforma nativa.

 - Páginas XAML não podem ser incluídas no projeto do Visual Basic - só pode construir o gerador de código-behind em C#. É possível incluir XAML em uma separado e referenciada, c# biblioteca de classes portátil e usar a associação de dados para popular os arquivos XAML por meio de modelos do Visual Basic (um exemplo disso está incluído na [exemplo](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB/XamlPages)).

 - Xamarin não oferece suporte a linguagem Visual Basic.NET.

## <a name="related-links"></a>Links relacionados

- [XamarinFormsVB (sample)](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB)
- [Desenvolvimento de plataforma cruzada com o .NET Framework](https://docs.microsoft.com/dotnet/standard/cross-platform/)
