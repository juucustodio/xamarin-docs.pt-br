---
title: Xamarin. Forms usando o Visual Basic.NET
description: Modelo de projeto PCL xamarin. Forms pode ser modificado para usar o Visual Basic para o assembly principal, permitindo a criação de aplicativos móveis de plataforma cruzada usando VB.NET efetivamente.
ms.prod: xamarin
ms.assetid: da4b4ba9-9205-47dc-8bae-23272ede2c50
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: e26d330d62e6ffdfdb3f9b8eab59e57a6377a86c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="xamarinforms-using-visual-basicnet"></a>Xamarin. Forms usando o Visual Basic.NET

Xamarin não oferece suporte Visual Basic diretamente - siga as instruções nesta página para criar uma solução de C# xamarin. Forms PCL e, em seguida, substituir o projeto PCL código comum com o Visual Basic.

[![](xamarin-forms-images/hero-sml.png "Criar uma solução PCL xamarin. Forms e, em seguida, substituir o projeto PCL código comum com o Visual Basic")](xamarin-forms-images/hero.png#lightbox)

> [!NOTE]
> Você deve usar o Visual Studio no Windows para o programa com o Visual Basic.

## <a name="xamarinforms-with-visual-basic-walkthrough"></a>Xamarin. Forms com instruções passo a passo do Visual Basic

Siga estas etapas para criar um projeto xamarin. Forms simple que usa o Visual Basic:

1. Criar um novo *xamarin. Forms c#* solução que usa as bibliotecas de classe portáteis (PCL).
Vá para **arquivo > Novo projeto** e no **novo projeto** janela navegue até **instalado > Modelos > Visual C# > plataforma cruzada** , em seguida, escolha  **Entre o aplicativo de plataforma (xamarin. Forms ou nativo) > xamarin. Forms**.

2. Com o botão direito na solução e **Adicionar > Novo projeto**.

3. Escolha o **Visual Basic > biblioteca de classes (portátil)** tipo de projeto:

   [![](xamarin-forms-images/add-vb-2-sml.png "Adicionar novo projeto de biblioteca de classes portátil")](xamarin-forms-images/add-vb-2.png#lightbox)

4. Selecione as plataformas, como mostrado para configurar o perfil correto de PCL (certifique-se de incluir xamarin e xamarin):

   ![](xamarin-forms-images/add-vb-3-sml.png "Escolha as plataformas para dar suporte")

5. Com o botão direito no projeto do Visual Basic e escolha **propriedades**, em seguida, altere o **namespace padrão** para corresponder a existente c# projetos:

   ![](xamarin-forms-images/add-vb-4s-sml.png "Certifique-se de que o namespace de raiz do Visual Basic coincide com o aplicativo xamarin. Forms")

6. Clique com botão direito no novo projeto do Visual Basic e escolha **gerenciar pacotes Nuget**, em seguida, instalar **xamarin. Forms** e feche a janela do Gerenciador de pacote.

   [![](xamarin-forms-images/add-vb-4-sml.png "Formulários e fechar a janela do Gerenciador de pacote")](xamarin-forms-images/add-vb-4.png#lightbox)

7. Renomeie o padrão **Class1** arquivo *e* de classe para `App`:

   [![](xamarin-forms-images/add-vb-5-sml.png "Renomeie o arquivo de Class1 padrão e a classe de aplicativo")](xamarin-forms-images/add-vb-5.png#lightbox)

8. Cole o seguinte código para o **App.vb** arquivo, que se tornará o ponto de partida do aplicativo xamarin. Forms. Lembre-se de incluir `Imports Xamarin.Forms` e adicionar `Inherits Application` à classe:

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

9. Agora, precisamos apontar projetos Android e iOS para o novo projeto do Visual Basic.
Clique com botão direito no **referências** nó no iOS e Android projetos para abrir o **Gerenciador de referências**. Escala de cancelar o a escala biblioteca portátil do VB e c# biblioteca portátil (não se esqueça de, faça isso para projetos Android e iOS).

   [![](xamarin-forms-images/add-vb-8-sml.png "Remover a referência de projeto antigo, adicione a referência do Visual Basic")](xamarin-forms-images/add-vb-8.png#lightbox)

10. Exclua o projeto c# portátil. Adicionar novo **. vb** out de arquivos para criar seu aplicativo xamarin. Forms. Um modelo para o novo `ContentPage`s no Visual Basic é mostrado abaixo:

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

Conforme mencionado no [portátil Visual Basic.NET página](~/cross-platform/platform/visual-basic/index.md), Xamarin não oferece suporte a linguagem Visual Basic. Isso significa que há algumas limitações sobre onde você pode usar o Visual Basic:

 - Processadores personalizados não podem ser escritos em Visual Basic, ele devem ser escritos em c# nos projetos de plataforma nativo.

 - Implementações de serviço de dependência não podem ser escritas em Visual Basic, ele devem ser escritos em c# nos projetos de plataforma nativo.

 - Páginas XAML não podem ser incluídas no projeto do Visual Basic - só pode construir o gerador de código-behind c#. É possível incluir o XAML em uma separado, referenciada, c# biblioteca de classes portátil e usar a associação de dados para preencher os arquivos XAML por meio de modelos do Visual Basic (um exemplo disso é incluído no [exemplo](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB/XamlPages)).

 - Xamarin não oferece suporte a linguagem Visual Basic.NET.

## <a name="related-links"></a>Links relacionados

- [XamarinFormsVB (sample)](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB)
- [Desenvolvimento de plataforma cruzada com o .NET Framework (Microsoft)](http://msdn.microsoft.com/en-us/library/gg597391(v=vs.110).aspx)
