---
title: "Início rápido da multitela do Xamarin.Forms"
ms.topic: article
ms.prod: xamarin
ms.assetid: 255d93b9-518c-4e5d-a9cd-4dd8a7945a7f
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/06/2018
ms.openlocfilehash: 6a24ff05ae2a2c2368650c368cc408f0219ce21e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="xamarinforms-multiscreen-quickstart"></a>Início rápido da multitela do Xamarin.Forms

O início rápido demonstra como estender o aplicativo Phoneword ao adicionar uma segunda tela para controlar o histórico de chamadas para o aplicativo. O aplicativo final é mostrado abaixo:

[![](quickstart-images/intro-app-examples-sml.png "Aplicativo Phoneword")](quickstart-images/intro-app-examples.png "Aplicativo Phoneword")

Estenda o aplicativo Phoneword da seguinte maneira:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Inicie o Visual Studio. Na página inicial, clique em **Abrir Projeto...** e, na caixa de diálogo **Abrir Projeto**, selecione o arquivo de solução para o projeto Phoneword:

  ![](quickstart-images/vs/open-solution.png "Abrir Projeto")

1. No **Gerenciador de Soluções**, clique com botão direito do mouse no projeto **Phoneword** e selecione **Adicionar > Novo Item…**:

  ![](quickstart-images/vs/add-new-item.png "Adicionar Novo Item")

1. Na caixa de diálogo **Adicionar Novo Item**, selecione **Itens do Visual C# > Xamarin.Forms > Página de Conteúdo**, nomeie o novo item como **CallHistoryPage** e clique no botão **Adicionar**. Isso adicionará uma página chamada **CallHistoryPage** ao projeto:

  ![](quickstart-images/vs/add-callhistorypage-class.png "Modelos de projeto Xamarin.Forms")

1. Em **CallHistoryPage.xaml**, remova todo o código de modelo e substitua-o pelo código a seguir. Esse código define declarativamente a interface do usuário para a página:

        <?xml version="1.0" encoding="UTF-8"?>
        <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                           xmlns:local="clr-namespace:Phoneword;assembly=Phoneword"
                           xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                           x:Class="Phoneword.CallHistoryPage"
                           Title="Call History">
            <ContentPage.Padding>
                <OnPlatform x:TypeArguments="Thickness">
                    <On Platform="iOS" Value="20, 40, 20, 20" />
                    <On Platform="Android, WinPhone, Windows" Value="20" />
                </OnPlatform>
            </ContentPage.Padding>
            <StackLayout>
              <ListView ItemsSource="{x:Static local:App.PhoneNumbers}" />
            </StackLayout>
        </ContentPage>

  Salve as alterações em **CallHistoryPage.xaml** ao pressionar **CTRL+S** e feche o arquivo.

1. No **Gerenciador de Soluções**, clique duas vezes em para **App.xaml.cs** abri-lo:

  ![](quickstart-images/vs/open-app-class.png "Abrir App.xaml.cs")

1. Em **App.xaml.cs**, importe o namespace `System.Collections.Generic`, adicione a declaração da propriedade `PhoneNumbers`, inicialize a propriedade no construtor `App` e inicialize a propriedade [`MainPage`](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/) para que seja uma [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/). A coleção `PhoneNumbers` será usada para armazenar uma lista de cada número de telefone traduzido chamado pelo aplicativo:

        using System.Collections.Generic;
        using Xamarin.Forms;
        using Xamarin.Forms.Xaml;

        [assembly: XamlCompilation(XamlCompilationOptions.Compile)]
        namespace Phoneword
        {
            public partial class App : Application
            {
                public static IList<string> PhoneNumbers { get; set; }

                public App()
                {
                    InitializeComponent();
                    PhoneNumbers = new List<string>();
                    MainPage = new NavigationPage(new MainPage());
                }
                ...
            }
        }

  Salve as alterações em **App.xaml.cs** ao pressionar **CTRL + S** e feche o arquivo.

1. No **Gerenciador de Soluções**, clique duas vezes em **MainPage.xaml** para abri-lo:

  ![](quickstart-images/vs/open-mainpage-xaml.png "Abrir MainPage.xaml")

1. Em **MainPage.xaml**, adicione um controle [`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) no final do controle [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/). O botão será usado para navegar até a página do histórico de chamadas:

        <StackLayout VerticalOptions="FillAndExpand"
                     HorizontalOptions="FillAndExpand"
                     Orientation="Vertical"
                     Spacing="15">
          ...
          <Button x:Name="callButton" Text="Call" IsEnabled="false" Clicked="OnCall" />
          <Button x:Name="callHistoryButton" Text="Call History" IsEnabled="false"
                  Clicked="OnCallHistory" />
        </StackLayout>

  Salve as alterações em **MainPage.xaml** ao pressionar **CTRL+S** e feche o arquivo.

1. No **Gerenciador de Soluções**, clique duas vezes em **MainPage.xaml.cs** para abri-lo:

  ![](quickstart-images/vs/open-mainpage-codebehind.png "Abrir MainPage.xaml.cs")

1. Em **MainPage.xaml.cs**, adicione o método do manipulador de eventos `OnCallHistory` e modifique o método do manipulador de eventos `OnCall` para adicionar o número de telefone traduzido à coleção `App.PhoneNumbers` e habilitar o `callHistoryButton`, desde que a variável `dialer` não seja `null`:

        using System;
        using Xamarin.Forms;

        namespace Phoneword
        {
            public partial class MainPage : ContentPage
            {
                ...

                async void OnCall(object sender, EventArgs e)
                {
                    ...
                    if (dialer != null) {
                        App.PhoneNumbers.Add (translatedNumber);
                        callHistoryButton.IsEnabled = true;
                        dialer.Dial (translatedNumber);
                    }
                    ...
                }

                async void OnCallHistory(object sender, EventArgs e)
                {
                    await Navigation.PushAsync (new CallHistoryPage ());
                }
            }
        }

  Salve as alterações em **MainPage.xaml.cs** ao pressionar **CTRL+S** e feche o arquivo.

1. No Visual Studio, selecione o item de menu **Compilar > Compilar Solução** (ou pressione **CTRL+SHIFT+B**). O aplicativo será criado e uma mensagem de êxito será exibida na barra de status do Visual Studio:

  ![](quickstart-images/vs/build-successful.png "Build bem-sucedido")

  Se houver erros, repita as etapas anteriores e corrija qualquer erro até que o aplicativo seja compilado com êxito.

1. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para inicializar o aplicativo:

  ![](quickstart-images/vs/start.png "Barra de Ferramentas do Visual Studio")
  ![](quickstart-images/vs/phone-result-uwp.png "UWP do Aplicativo Phoneword")

1. No **Gerenciador de Soluções**, clique com botão direito do mouse no projeto **Phoneword.Droid** e selecione **Definir como Projeto de Inicialização**.
1. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para inicializar o aplicativo em um emulador do Android.
1. Se você tiver um dispositivo iOS e atender aos requisitos de sistema do Mac para o desenvolvimento do Xamarin.Forms, use uma técnica semelhante para implantar o aplicativo no dispositivo iOS. Como alternativa, implante o aplicativo para o [simulador remoto de iOS](~/tools/ios-simulator.md).

  Observação: não há suporte para chamadas telefônicas em todos os simuladores.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Inicialize o Visual Studio para Mac. Na página inicial, clique em **Abrir...** e, na caixa de diálogo, selecione o arquivo de solução para o projeto Phoneword:

  ![](quickstart-images/xs/open-solution.png "Abrir Solução")

1. No **Painel de Soluções**, selecione o projeto **Phoneword**, clique com botão direito do mouse e selecione **Adicionar > Novo arquivo...**:

  ![](quickstart-images/xs/add-new-file.png "Adicionar Novo Arquivo")

1. Na caixa de diálogo **Novo Arquivo**, selecione **Formulários > Formulários ContentPage XAML**, nomeie o novo arquivo **CallHistoryPage** e clique no botão **Novo**. Isso adicionará uma página chamada **CallHistoryPage** ao projeto:

  ![](quickstart-images/xs/add-callhistorypage-class.png "Adicionar ContentPage do Forms")

1. No **Painel de Soluções**, clique duas vezes em **CallHistoryPage.xaml** para abri-lo:

  ![](quickstart-images/xs/open-callhistorypage-xaml.png "Abrir CallHistoryPage.xaml")

1. Em **CallHistoryPage.xaml**, remova todo o código de modelo e substitua-o pelo código a seguir. Esse código define declarativamente a interface do usuário para a página:

        <?xml version="1.0" encoding="UTF-8"?>
        <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                           xmlns:local="clr-namespace:Phoneword;assembly=Phoneword"
                           xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                           x:Class="Phoneword.CallHistoryPage"
                           Title="Call History">
            <ContentPage.Padding>
                <OnPlatform x:TypeArguments="Thickness">
                    <On Platform="iOS" Value="20, 40, 20, 20" />
                    <On Platform="Android, WinPhone, Windows" Value="20" />
                </OnPlatform>
            </ContentPage.Padding>
            <StackLayout>
              <ListView ItemsSource="{x:Static local:App.PhoneNumbers}" />
            </StackLayout>
        </ContentPage>      

  Salve as alterações em **CallHistoryPage.xaml** escolhendo **Arquivo > Salvar** (ou pressionando **&#8984; + S**) e feche o arquivo.

1. No **Painel de Soluções**, clique duas vezes em **App.xaml.cs** para abri-lo:

  ![](quickstart-images/xs/open-app-class.png "Abrir App.xaml.cs")

1. Em **App.xaml.cs**, importe o namespace `System.Collections.Generic`, adicione a declaração da propriedade `PhoneNumbers`, inicialize a propriedade no construtor `App` e inicialize a propriedade [`MainPage`](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/) para que seja uma [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/). A coleção `PhoneNumbers` será usada para armazenar uma lista de cada número de telefone traduzido chamado pelo aplicativo:

        using System.Collections.Generic;
        using Xamarin.Forms;
        using Xamarin.Forms.Xaml;

        [assembly: XamlCompilation(XamlCompilationOptions.Compile)]
        namespace Phoneword
        {
            public partial class App : Application
            {
                public static IList<string> PhoneNumbers { get; set; }

                public App()
                {
                    InitializeComponent();
                    PhoneNumbers = new List<string>();
                    MainPage = new NavigationPage(new MainPage());
                }
                ...
            }
        }

  Salve as alterações em **App.xaml.cs** escolhendo **Arquivo > Salvar** (ou pressionando **&#8984; + S**) e feche o arquivo.

1. No **Painel de Soluções**, clique duas vezes em **MainPage.xaml** para abri-lo:

  ![](quickstart-images/xs/open-mainpage-xaml.png "Abrir MainPage.xaml")

1. Em **MainPage.xaml**, adicione um controle [`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) no final do controle [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/). O botão será usado para navegar até a página do histórico de chamadas:

        <StackLayout VerticalOptions="FillAndExpand"
                     HorizontalOptions="FillAndExpand"
                     Orientation="Vertical"
                     Spacing="15">
          ...
          <Button x:Name="callButton" Text="Call" IsEnabled="false" Clicked="OnCall" />
          <Button x:Name="callHistoryButton" Text="Call History" IsEnabled="false"
                  Clicked="OnCallHistory" />
        </StackLayout>

  Salve as alterações em **MainPage.xaml** ao escolher **Arquivo > Salvar** (ou ao pressionar **&#8984; + S**) e feche o arquivo.

1. No **Painel de Soluções**, clique duas vezes em **MainPage.xaml.cs** para abri-lo:

  ![](quickstart-images/xs/open-mainpage-codebehind.png "Abrir MainPage.xaml.cs")

1. Em **MainPage.xaml.cs**, adicione o método do manipulador de eventos `OnCallHistory` e modifique o método do manipulador de eventos `OnCall` para adicionar o número de telefone traduzido à coleção `App.PhoneNumbers` e habilitar o `callHistoryButton`, desde que a variável `dialer` não seja `null`:

        using System;
        using Xamarin.Forms;

        namespace Phoneword
        {
            public partial class MainPage : ContentPage
            {
                ...

                async void OnCall(object sender, EventArgs e)
                {
                    ...
                    if (dialer != null) {
                        App.PhoneNumbers.Add (translatedNumber);
                        callHistoryButton.IsEnabled = true;
                        dialer.Dial (translatedNumber);
                    }
                    ...
                }

                async void OnCallHistory(object sender, EventArgs e)
                {
                    await Navigation.PushAsync (new CallHistoryPage ());
                }
            }
        }

  Salve as alterações em **MainPage.xaml.cs** ao escolher **Arquivo > Salvar** (ou ao pressionar **&#8984; + S**) e feche o arquivo.

1. No Visual Studio para Mac, selecione o item de menu **Compilar > Compilar Tudo** (ou pressione **&#8984; + B**). O aplicativo será criado e uma mensagem de êxito será exibida na barra de ferramentas do Visual Studio para Mac:

  ![](quickstart-images/xs/build-successful.png "Build bem-sucedido")

  Se houver erros, repita as etapas anteriores e corrija qualquer erro até que o aplicativo seja compilado com êxito.

1. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para inicializar o aplicativo no Simulador do iOS:

  ![](quickstart-images/xs/start.png "Barra de Ferramentas do Visual Studio para Mac")
  ![](quickstart-images/xs/phone-result-ios.png "Simulador de iOS")

  Observação: não há suporte para chamadas telefônicas no Simulador de iOS.

1. No **Painel de Soluções**, selecione o projeto **Phoneword.Droid**, clique com botão direito do mouse e selecione **Definir como projeto de inicialização**:

  ![](quickstart-images/xs/set-startup-project.png "Definir como Projeto Inicial")

1. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para inicializar o aplicativo em um emulador do Android:

  ![](quickstart-images/xs/phone-result-android.png "Android Emulator")

  Observação: não há suporte para chamadas telefônicas em emuladores de Android.

-----

Parabéns por concluir um aplicativo Xamarin.Forms multitela. O [próximo tópico](~/xamarin-forms/get-started/hello-xamarin-forms-multiscreen/deepdive.md) deste guia analisa as etapas seguidas para entender a navegação de página e a associação de dados usando o Xamarin.Forms.


## <a name="related-links"></a>Links relacionados

- [Phoneword (amostra)](https://developer.xamarin.com/samples/xamarin-forms/Phoneword/)
- [PhonewordMultiscreen (amostra)](https://developer.xamarin.com/samples/xamarin-forms/PhonewordMultiscreen/)
