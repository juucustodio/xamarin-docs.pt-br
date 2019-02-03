---
title: Compilar seu primeiro aplicativo Xamarin.Forms
description: Guia em vídeo mostrando como compilar seu primeiro aplicativo Xamarin.Forms no Visual Studio.
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 72B6AF82-4D98-47E5-AB54-0A35B3253468
ms.technology: xamarin-forms
ms.custom: video
author: conceptdev
ms.author: crdun
ms.date: 02/02/2019
ms.openlocfilehash: 8adf52d8e08dedeebae8362cf730e5dc3c7c93a9
ms.sourcegitcommit: 9dcb7377dc92ad921285fbb857b0be13030bbea3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2019
ms.locfileid: "55668557"
---
# <a name="build-your-first-xamarinforms-app"></a>Compilar seu primeiro aplicativo Xamarin.Forms

_Assista a este vídeo e acompanhe-o para compilar seu primeiro aplicativo móvel com Xamarin.Forms._

::: zone pivot="windows"

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Building-Your-First-Android--iOS-App-in-Visual-Studio-2017/player]

## <a name="step-by-step-instructions-for-windows"></a>Instruções passo a passo para Windows

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/FirstApp/)

Siga estas etapas em conjunto com o vídeo acima:

1. Escolha **Arquivo > Novo > Projeto...**  ou pressione o botão **Criar novo projeto...** e, em seguida, selecione **Visual C# > Multiplataforma > Aplicativo Móvel (Xamarin.Forms)**:

    [![Aplicativo Móvel (Xamarin.Forms)](images/win/01-sml.png)](images/win/01.png#lightbox)

2. Certifique-se de que **Android** e **iOS** estejam selecionados, com compartilhamento de código **.NET Standard**:

    [![Android e iOS, com .NET Standard](images/win/02-sml.png)](images/win/02.png#lightbox)

3. Aguarde até que os pacotes do NuGet sejam restaurados (a mensagem "Restauração concluída" será exibida na barra de status).

4. Inicie o Android Emulator pressionando o botão de depuração (ou o item de menu **Depurar > Iniciar Depuração**).

5. Edite **MainPage.xaml**, adicionando este XAML antes do final de `</StackLayout>`:

    ```xaml
    <Button Text="Click Me" Clicked="Button_Clicked" />
    ```

6. Edite **MainPage.xaml.cs**, adicionando este código ao final da classe:

    ```csharp
    int count = 0;
    void Button_Clicked(object sender, System.EventArgs e)
    {
        count++;
        ((Button)sender).Text = $"You clicked {count} times.";
    }
    ```

7. Depure o aplicativo no Android:

    ![Aplicativo Android](images/win/07-sml.png)

    > [!TIP]
    > É possível compilar e depurar o aplicativo iOS no Visual Studio com um computador Mac em rede. Veja as [instruções de instalação](~/ios/get-started/installation/windows/index.md) para obter mais informações.

::: zone-end
::: zone pivot="macos"

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Building-Your-First-iOS--Android-App-in-Visual-Studio-for-Mac/player]

## <a name="step-by-step-instructions-for-mac"></a>Instruções passo a passo para Mac

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/FirstApp/)

Siga estas etapas em conjunto com o vídeo acima:

1. Escolha **Arquivo > Nova Solução...** ou pressione o botão **Novo Projeto...** e, em seguida, selecione **Multiplataforma > Aplicativo > Aplicativo de Formulários em Branco**:

    [![Aplicativo de Formulários em Branco](images/01-sml.png)](images/01.png#lightbox)

2. Certifique-se de que **Android** e **iOS** estejam selecionados, com compartilhamento de código **.NET Standard**:

    [![Android e iOS, com .NET Standard](images/02-sml.png)](images/02.png#lightbox)

3. Restaure os pacotes do NuGet clicando com o botão direito do mouse na solução:

    ![Aplicativo Android](images/03-sml.png)

4. Inicie o Android Emulator pressionando o botão de depuração (ou **Executar > Iniciar Depuração**).

5. Edite **MainPage.xaml**, adicionando este XAML antes do final de `</StackLayout>`:

    ```xaml
    <Button Text="Click Me" Clicked="Handle_Clicked" />
    ```

6. Edite **MainPage.xaml.cs**, adicionando este código ao final da classe:

    ```csharp
    int count = 0;
    void Handle_Clicked(object sender, System.EventArgs e)
    {
        count++;
        ((Button)sender).Text = $"You clicked {count} times.";
    }
    ```

7. Depure o aplicativo no Android:

    ![Aplicativo Android](images/07-sml.png)

8. Clique com o botão direito do mouse para definir o iOS para o **Projeto de inicialização**:

    [![Defina o projeto de inicialização como iOS](images/08-sml.png)](images/08.png#lightbox)

9. Depure o aplicativo no iOS:

    ![Aplicativo iOS](images/09-sml.png)

::: zone-end

Você pode baixar o código completo na [Galeria de amostras](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/FirstApp/) ou exibi-lo no [GitHub](https://github.com/xamarin/xamarin-forms-samples/tree/master/GetStarted/FirstApp).

## <a name="next-steps"></a>Próximas etapas

- [Único Page Quickstart](~/get-started/quickstarts/single-page.md) &ndash; compilar um aplicativo mais funcional.
- [Exemplos de Xamarin.Forms](~/xamarin-forms/samples/index.yml) &ndash; baixe e execute exemplos de código e de aplicativos.
- [Livro eletrônico Creating Mobile Apps (Criando aplicativos móveis)](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) &ndash; capítulos detalhados que ensinam o desenvolvimento de Xamarin.Forms, disponível em PDF e incluindo centenas de exemplos adicionais.
