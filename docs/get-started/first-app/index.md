---
title: Crie seu primeiro Xamarin.Forms aplicativo
description: Guia de vídeo mostrando como criar seu primeiro Xamarin.Forms aplicativo no Visual Studio.
zone_pivot_groups: platform-dev16
ms.prod: xamarin
ms.assetid: 72B6AF82-4D98-47E5-AB54-0A35B3253468
ms.technology: xamarin-forms
ms.custom: video
author: conceptdev
ms.author: crdun
ms.date: 05/23/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d2bb00eeadb3862d25032da45860f1cc12e3aad2
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93369877"
---
# <a name="build-your-first-no-locxamarinforms-app"></a>Crie seu primeiro Xamarin.Forms aplicativo

_Assista a este vídeo e acompanhe para criar seu primeiro aplicativo móvel com o Xamarin.Forms ._

::: zone pivot="windows"

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Build-Your-First-Android-App-with-Visual-Studio-2019-and-Xamarin/player]

## <a name="step-by-step-instructions-for-windows"></a>Instruções passo a passo para Windows

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/getstarted-firstapp/)

Siga estas etapas em conjunto com o vídeo acima:

1. Escolha **Arquivo > Novo > Projeto…** ou pressione o botão **Criar projeto…** :

    [![Criar um novo projeto](images/win-2019/01-sml.png)](images/win-2019/01.png#lightbox)

2. Pesquise "Xamarin" ou escolha **Móvel** no menu **Tipo de projeto**. Selecione o tipo de projeto **aplicativo móvel ( Xamarin.Forms )** :

    [![Filtro para projetos do Xamarin](images/win-2019/02-sml.png)](images/win-2019/02.png#lightbox)

3. Escolha um nome de projeto &ndash; o exemplo usa "AwesomeApp":

    [![Escolher um nome de projeto](images/win-2019/03-sml.png)](images/win-2019/03.png#lightbox)

4. Clique no tipo de projeto **Em branco** e verifique se **Android** e **iOS** estão selecionados:

    [![Android e iOS, com .NET Standard](images/win-2019/04-sml.png)](images/win-2019/04.png#lightbox)

5. Aguarde até que os pacotes do NuGet sejam restaurados (a mensagem "Restauração concluída" será exibida na barra de status).

6. As novas instalações do Visual Studio 2019 não terão um Android Emulator configurado. Clique na seta suspensa no botão **Depurar** e escolha **Criar Android Emulator** para iniciar a tela de criação do emulador:

    ![Criar lista suspensa do Android Emulator](images/win-2019/debug-dropdown.png)

7. Na tela de criação do emulador, use as configurações padrão e clique no botão **Criar** :

    [![Tela de criação do emulador do Android](images/win-2019/create-emulator-sml.png)](images/win-2019/create-emulator.png#lightbox)

8. A criação de um emulador o levará de volta à janela Gerenciador de Dispositivos. Clique no botão **Iniciar** para iniciar o novo emulador:

    ![Android Emulator no Gerenciador de Dispositivos](images/win-2019/start-emulator.png)

9. O Visual Studio 2019 agora deve mostrar o nome do novo emulador no botão **Depurar** :

    ![Nome do Android Emulator no botão Depurar](images/win-2019/debug-emulator-name.png)

10. Clique no botão **Depurar** para compilar e implantar o aplicativo no Android Emulator:

    ![Android Emulator exibindo o aplicativo](images/win-2019/android-emulator.png)

## <a name="customize-the-application"></a>Personalizar o aplicativo

O aplicativo pode ser personalizado para adicionar funcionalidade interativa. Execute as etapas a seguir para adicionar a interação do usuário ao aplicativo:

1. Edite **MainPage.xaml** , adicionando este XAML antes do final de `</StackLayout>`:

    ```xaml
    <Button Text="Click Me" Clicked="Button_Clicked" />
    ```

2. Edite **MainPage.xaml.cs** , adicionando este código ao final da classe:

    ```csharp
    int count = 0;
    void Button_Clicked(object sender, System.EventArgs e)
    {
        count++;
        ((Button)sender).Text = $"You clicked {count} times.";
    }
    ```

3. Depure o aplicativo no Android:

    ![Aplicativo Android](images/win/07-sml.png)

> [!NOTE]
> O aplicativo de exemplo inclui funcionalidade interativa adicional que não é abordada no vídeo.

## <a name="build-an-ios-app-in-visual-studio-2019"></a>Compilar um aplicativo iOS no Visual Studio 2019

É possível compilar e depurar o aplicativo iOS no Visual Studio com um computador Mac em rede. Veja as [instruções de instalação](~/ios/get-started/installation/windows/index.md) para obter mais informações.

Este vídeo aborda o processo de criação e teste de um aplicativo iOS usando o Visual Studio 2019 no Windows:

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Build-Your-First-iOS-App-with-Visual-Studio-2019-and-Xamarin/player]

::: zone-end
::: zone pivot="win-vs2017"

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Building-Your-First-Android--iOS-App-in-Visual-Studio-2017/player]

## <a name="step-by-step-instructions-for-windows"></a>Instruções passo a passo para Windows

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/getstarted-firstapp/)

Siga estas etapas em conjunto com o vídeo acima:

1. Escolha **arquivo > novo projeto de >...** ou pressione o botão **criar novo projeto...** e, em seguida, selecione **Visual C# > aplicativo móvel > de plataforma cruzada ( Xamarin.Forms )** :

    [![Aplicativo móvel (::: no-Loc (Xamarin. Forms):::)](images/win/01-sml.png)](images/win/01.png#lightbox)

2. Certifique-se de que **Android** e **iOS** estejam selecionados, com compartilhamento de código **.NET Standard** :

    [![Android e iOS, com .NET Standard](images/win/02-sml.png)](images/win/02.png#lightbox)

3. Aguarde até que os pacotes do NuGet sejam restaurados (a mensagem "Restauração concluída" será exibida na barra de status).

4. Inicie o Android Emulator pressionando o botão de depuração (ou o item de menu **Depurar > Iniciar Depuração** ).

5. Edite **MainPage.xaml** , adicionando este XAML antes do final de `</StackLayout>`:

    ```xaml
    <Button Text="Click Me" Clicked="Button_Clicked" />
    ```

6. Edite **MainPage.xaml.cs** , adicionando este código ao final da classe:

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

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/getstarted-firstapp/)

Siga estas etapas em conjunto com o vídeo acima:

1. Escolha **Arquivo > Nova Solução...** ou pressione o botão **Novo Projeto...** e, em seguida, selecione **Multiplataforma > Aplicativo > Aplicativo de Formulários em Branco** :

    [![Aplicativo de formulários em branco](images/01-sml.png)](images/01.png#lightbox)

2. Certifique-se de que **Android** e **iOS** estejam selecionados, com compartilhamento de código **.NET Standard** :

    [![Android e iOS, com .NET Standard](images/02-sml.png)](images/02.png#lightbox)

3. Restaure os pacotes do NuGet clicando com o botão direito do mouse na solução:

    ![Aplicativo Android](images/03-sml.png)

4. Inicie o Android Emulator pressionando o botão de depuração (ou **Executar > Iniciar Depuração** ).

5. Edite **MainPage.xaml** , adicionando este XAML antes do final de `</StackLayout>`:

    ```xaml
    <Button Text="Click Me" Clicked="Handle_Clicked" />
    ```

6. Edite **MainPage.xaml.cs** , adicionando este código ao final da classe:

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

8. Clique com o botão direito do mouse para definir o iOS para o **Projeto de inicialização** :

    [![Definir o projeto de inicialização para iOS](images/08-sml.png)](images/08.png#lightbox)

9. Depure o aplicativo no iOS:

    ![Aplicativo iOS](images/09-sml.png)

::: zone-end

Você pode baixar o código completo na [Galeria de amostras](/samples/xamarin/xamarin-forms-samples/getstarted-firstapp/) ou exibi-lo no [GitHub](https://github.com/xamarin/xamarin-forms-samples/tree/master/GetStarted/FirstApp).

## <a name="next-steps"></a>Próximas etapas

- [Início Rápido de Página Única](~/get-started/quickstarts/single-page.md) &ndash; Criar um aplicativo mais funcional.
- [ Xamarin.Forms Exemplos](~/xamarin-forms/samples/index.md) &ndash; Baixe e execute exemplos de código e aplicativos de exemplo.
- [Criando ebook](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) &ndash; de aplicativos móveis Capítulos detalhados que ensinam o Xamarin.Forms desenvolvimento, disponível como um PDF e incluindo centenas de exemplos adicionais.