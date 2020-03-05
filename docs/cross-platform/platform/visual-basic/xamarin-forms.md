---
title: Xamarin. Forms usando o Visual Basic.NET
description: O modelo de projeto Xamarin. Forms pode ser modificado para usar Visual Basic para o assembly principal, permitindo efetivamente que você crie aplicativos móveis de plataforma cruzada usando o VB.NET.
ms.prod: xamarin
ms.assetid: da4b4ba9-9205-47dc-8bae-23272ede2c50
author: davidortinau
ms.author: daortin
ms.date: 04/24/2019
ms.openlocfilehash: 42be47e74b4b0da60d517a17bb6090c58448b718
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292105"
---
# <a name="xamarinforms-using-visual-basicnet"></a>Xamarin. Forms usando o Visual Basic.NET

O Xamarin não oferece suporte a Visual Basic diretamente-siga as instruções nesta página para criar C# uma solução Xamarin. Forms e, em seguida C# , substitua o projeto .net Standard com Visual Basic.

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/mobile-samples/visualbasic-xamarinformsvb/)

[![criar uma solução Xamarin. Forms e, em seguida, substituir o projeto .NET Standard por Visual Basic](xamarin-forms-images/hero-sml.png)](xamarin-forms-images/hero.png#lightbox)

> [!NOTE]
> Você deve usar o Visual Studio no Windows para programar com Visual Basic.

## <a name="xamarinforms-with-visual-basic-walkthrough"></a>Xamarin. Forms com instruções Visual Basic

Siga estas etapas para criar um projeto do Xamarin. Forms simples que usa Visual Basic:

1. No Visual Studio 2019, escolha **criar um novo projeto**.

2. Na janela **criar um novo projeto** , digite **Xamarin. Forms** para filtrar a lista e selecione **aplicativo móvel (xamarin. Forms)** e pressione **Avançar**.

    [Filtro de ![para aplicativos Xamarin. Forms](xamarin-forms-images/02-sml.png)](xamarin-forms-images/02.png#lightbox)

3. Na próxima tela, digite um nome para o projeto e pressione **criar**.

4. Escolha o modelo **em branco** e pressione **OK**:

    [![modelo Xamarin. Forms em branco](xamarin-forms-images/04-sml.png)](xamarin-forms-images/04.png#lightbox)

    Isso cria uma solução Xamarin. Forms no Visual Studio, usando C#. As próximas etapas modificam a solução para usar Visual Basic.

5. Clique com o botão direito do mouse na solução e escolha **adicionar > novo projeto...**

6. Digite **Visual Basic biblioteca** para filtrar as opções de projeto e escolha a opção **biblioteca de classes (.net Standard)** com o ícone de Visual Basic:

    [Filtro de ![para Visual Basic biblioteca](xamarin-forms-images/06-sml.png)](xamarin-forms-images/06.png#lightbox)

7. Na próxima tela, digite um nome para o projeto e pressione **criar**.

8. Clique com o botão direito do mouse no projeto Visual Basic e escolha **Propriedades**e, em seguida, altere o C# **namespace padrão** para corresponder aos projetos existentes:

    [![garantir que o namespace de raiz de Visual Basic corresponda ao aplicativo Xamarin. Forms](xamarin-forms-images/07a-sml.png)](xamarin-forms-images/07a.png#lightbox)

9. Clique com o botão direito do mouse no novo projeto Visual Basic e escolha **gerenciar pacotes NuGet**e, em seguida, instale o **Xamarin. Forms** e feche a janela do Gerenciador de pacotes.

    [![formulários e fechar a janela do Gerenciador de pacotes](xamarin-forms-images/07b-sml.png)](xamarin-forms-images/07b.png#lightbox)

10. Renomeie o arquivo **Class1. vb** padrão para **app. vb**:

    [![renomear o arquivo Class1 padrão e a classe para o aplicativo](xamarin-forms-images/08.png)](xamarin-forms-images/08.png#lightbox)

11. Cole o código a seguir no arquivo **app. vb** , que se tornará o ponto inicial do aplicativo Xamarin. Forms:

    ```vb
    Imports Xamarin.Forms

    Public Class App
        Inherits Application

        Public Sub New()
            Dim label = New Label With {.HorizontalTextAlignment = TextAlignment.Center,
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

12. Atualize os projetos do Android e do iOS para que eles façam referência a novos projetos de C# Visual Basic (e não ao projeto criado pelo modelo).
Clique com o botão direito do mouse no nó **referências** nos projetos do Android e Ios para abrir o **Gerenciador de referências**. Desmarcar a C# biblioteca e marcar a biblioteca Visual Basic (não se esqueça disso, faça isso para os projetos Android e Ios).

    [![remover referência de projeto antiga, Adicionar referência de Visual Basic](xamarin-forms-images/10-sml.png)](xamarin-forms-images/10.png#lightbox)

13. Exclua C# o projeto. Adicione novos arquivos **. vb** para criar seu aplicativo Xamarin. Forms. Um modelo para novos `ContentPage`s em Visual Basic é mostrado abaixo:

    ```vb
    Imports Xamarin.Forms

    Public Class Page2
    Inherits ContentPage

        Public Sub New()
            Dim label = New Label With {.HorizontalTextAlignment = TextAlignment.Center,
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

## <a name="limitations-of-visual-basic-in-xamarinforms"></a>Limitações de Visual Basic no Xamarin. Forms

Conforme indicado na [página do Visual Basic.net portátil](~/cross-platform/platform/visual-basic/index.md), o Xamarin não oferece suporte à linguagem Visual Basic. Isso significa que há algumas limitações sobre onde você pode usar Visual Basic:

- As páginas XAML não podem ser incluídas no projeto Visual Basic-o gerador code-behind só pode C#compilar. É possível incluir o XAML em uma biblioteca de classes separada, C# referenciada e portátil e usar DataBinding para popular os arquivos XAML por meio de modelos de Visual Basic (um exemplo disso é incluído no [exemplo](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB)).

- Renderizadores personalizados não podem ser gravados em Visual Basic, eles devem ser C# gravados nos projetos da plataforma nativa.

- As implementações do serviço de dependência não podem ser gravadas em Visual Basic C# , elas devem ser gravadas nos projetos da plataforma nativa.

## <a name="related-links"></a>Links relacionados

- [XamarinFormsVB (exemplo)](https://docs.microsoft.com/samples/xamarin/mobile-samples/visualbasic-xamarinformsvb/)
- [Desenvolvimento de plataforma cruzada com o .NET Framework](https://docs.microsoft.com/dotnet/standard/cross-platform/)
