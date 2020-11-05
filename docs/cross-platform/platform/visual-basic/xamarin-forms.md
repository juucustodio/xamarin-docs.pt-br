---
title: Xamarin. Forms usando o Visual Basic.NET
description: O modelo de projeto Xamarin. Forms pode ser modificado para usar Visual Basic para o assembly principal, permitindo efetivamente que você crie aplicativos móveis de plataforma cruzada usando o VB.NET.
ms.prod: xamarin
ms.assetid: da4b4ba9-9205-47dc-8bae-23272ede2c50
author: davidortinau
ms.author: daortin
ms.date: 04/24/2019
ms.openlocfilehash: 8cb26c3d8cec03cfaa12f7acb974c9944119bdfc
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374102"
---
# <a name="xamarinforms-using-visual-basicnet"></a>Xamarin. Forms usando o Visual Basic.NET

O Xamarin não oferece suporte a Visual Basic diretamente – siga as instruções nesta página para criar uma solução de Xamarin. Forms em C# e, em seguida, substitua o projeto .NET Standard C# por Visual Basic.

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/mobile-samples/visualbasic-xamarinformsvb/)

[![Crie uma solução Xamarin. Forms e substitua o projeto .NET Standard por Visual Basic](xamarin-forms-images/hero-sml.png)](xamarin-forms-images/hero.png#lightbox)

> [!NOTE]
> Você deve usar o Visual Studio no Windows para programar com Visual Basic.

## <a name="xamarinforms-with-visual-basic-walkthrough"></a>Xamarin. Forms com instruções Visual Basic

Siga estas etapas para criar um projeto do Xamarin. Forms simples que usa Visual Basic:

1. No Visual Studio 2019, escolha **criar um novo projeto**.

2. Na janela **criar um novo projeto** , digite **Xamarin. Forms** para filtrar a lista e selecione **aplicativo móvel (xamarin. Forms)** e pressione **Avançar**.

    [![Filtro para aplicativos Xamarin. Forms](xamarin-forms-images/02-sml.png)](xamarin-forms-images/02.png#lightbox)

3. Na próxima tela, digite um nome para o projeto e pressione **criar**.

4. Escolha o modelo **em branco** e pressione **OK** :

    [![Modelo Xamarin. Forms em branco](xamarin-forms-images/04-sml.png)](xamarin-forms-images/04.png#lightbox)

    Isso cria uma solução Xamarin. Forms no Visual Studio, usando C#. As próximas etapas modificam a solução para usar Visual Basic.

5. Clique com o botão direito do mouse na solução e escolha **adicionar > novo projeto...**

6. Digite **Visual Basic biblioteca** para filtrar as opções de projeto e escolha a opção **biblioteca de classes (.net Standard)** com o ícone de Visual Basic:

    [![Filtro para a biblioteca de Visual Basic](xamarin-forms-images/06-sml.png)](xamarin-forms-images/06.png#lightbox)

7. Na próxima tela, digite um nome para o projeto e pressione **criar**.

8. Clique com o botão direito do mouse no projeto Visual Basic e escolha **Propriedades** e, em seguida, altere o **namespace padrão** para corresponder aos projetos C# existentes:

    [![Verifique se o namespace raiz do Visual Basic corresponde ao aplicativo Xamarin. Forms](xamarin-forms-images/07a-sml.png)](xamarin-forms-images/07a.png#lightbox)

9. Clique com o botão direito do mouse no novo projeto Visual Basic e escolha **gerenciar pacotes NuGet** e, em seguida, instale o **Xamarin. Forms** e feche a janela do Gerenciador de pacotes.

    [![Formulários e fechar a janela do Gerenciador de pacotes](xamarin-forms-images/07b-sml.png)](xamarin-forms-images/07b.png#lightbox)

10. Renomeie o arquivo **Class1. vb** padrão para **app. vb** :

    [![Renomear o arquivo Class1 padrão e a classe para o aplicativo](xamarin-forms-images/08.png)](xamarin-forms-images/08.png#lightbox)

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

12. Atualize os projetos do Android e iOS para que eles façam referência a novos projetos de Visual Basic (e não ao projeto C# criado pelo modelo).
Clique com o botão direito do mouse no nó **referências** nos projetos do Android e Ios para abrir o **Gerenciador de referências**. Desmarcar a biblioteca do C# e marcar a biblioteca de Visual Basic (não se esqueça disso, faça isso para os projetos do Android e do iOS).

    [![Remover referência de projeto antiga, Adicionar referência de Visual Basic](xamarin-forms-images/10-sml.png)](xamarin-forms-images/10.png#lightbox)

13. Exclua o projeto C#. Adicione novos arquivos **. vb** para criar seu aplicativo Xamarin. Forms. Um modelo para novos `ContentPage` s no Visual Basic é mostrado abaixo:

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

- As páginas XAML não podem ser incluídas no projeto Visual Basic-o gerador code-behind só pode compilar C#. É possível incluir o XAML em uma biblioteca de classes portátil do C#, referenciada e separada, e usar DataBinding para popular os arquivos XAML por meio de modelos de Visual Basic (um exemplo disso é incluído no [exemplo](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB)).

- Renderizadores personalizados não podem ser gravados em Visual Basic, eles devem ser escritos em C# nos projetos da plataforma nativa.

- As implementações do serviço de dependência não podem ser gravadas em Visual Basic, elas devem ser escritas em C# nos projetos da plataforma nativa.

## <a name="related-links"></a>Links Relacionados

- [XamarinFormsVB (exemplo)](/samples/xamarin/mobile-samples/visualbasic-xamarinformsvb/)
- [Desenvolvimento entre plataformas com o .NET Framework](/dotnet/standard/cross-platform/)