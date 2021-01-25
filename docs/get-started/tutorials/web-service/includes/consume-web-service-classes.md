---
ms.openlocfilehash: a642e34c5f49e63503b34c44f536f0428993335b
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98634916"
---
Neste exercício, você criará uma interface do usuário para consumir a classe `RestService`, que recupera os dados do repositório .NET da API Web do GitHub. Os dados recuperados serão exibidos por um [`CollectionView`](xref:Xamarin.Forms.CollectionView).

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. No **Gerenciador de Soluções**, no projeto **WebServiceTutorial**, clique duas vezes em **MainPage.xaml** para abri-lo. Em seguida, em **MainPage.xaml**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="WebServiceTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Button Text="Get Repository Data"
                    Clicked="OnButtonClicked" />
            <CollectionView x:Name="collectionView">
                <CollectionView.ItemTemplate>
                    <DataTemplate>
                        <StackLayout>
                            <Label Text="{Binding Name}"
                                   FontSize="Medium" />
                            <Label Text="{Binding Description}"
                                   TextColor="Silver"
                                   FontSize="Small" />
                            <Label Text="{Binding GitHubHomeUrl}"
                                   TextColor="Gray"
                                   FontSize="Caption" />
                        </StackLayout>
                    </DataTemplate>
                </CollectionView.ItemTemplate>
            </CollectionView>
        </StackLayout>
    </ContentPage>
    ```

    Esse código define declarativamente a interface do usuário para a página, que é composta por um [`Button`](xref:Xamarin.Forms.Button) e um [`CollectionView`](xref:Xamarin.Forms.CollectionView). O `Button` define seu evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) para um manipulador de eventos chamado `OnButtonClicked` que será criado na próxima etapa. O `CollectionView` define a propriedade [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) para um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que define a aparência de cada item no `CollectionView`. O filho do `DataTemplate` é um [`StackLayout`](xref:Xamarin.Forms.StackLayout), que contém três objetos [`Label`](xref:Xamarin.Forms.Label). Os objetos `Label` associam as propriedades [`Text`](xref:Xamarin.Forms.Label.Text) deles às propriedades `Name`, `Description` e `GitHubHomeUrl` de cada objeto `Repository`. Para obter mais informações sobre associação de dados, confira [Associação de Dados do Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

    Além disso, a [`CollectionView`](xref:Xamarin.Forms.CollectionView) tem um nome especificado com o atributo `x:Name`. Isso permite que o arquivo code-behind acesse o objeto usando o nome atribuído.

1. No **Gerenciador de Soluções**, no projeto **WebServiceTutorial**, expanda **MainPage.xaml** e clique duas vezes em **MainPage.xaml.cs** para abri-lo. Então, em **MainPage.xaml.cs**, remova todo o código de modelo e substitua-o pelo código a seguir:

    ```csharp
    using System;
    using System.Collections.Generic;
    using Xamarin.Forms;

    namespace WebServiceTutorial
    {
        public partial class MainPage : ContentPage
        {
            RestService _restService;

            public MainPage()
            {
                InitializeComponent();
                _restService = new RestService();
            }

            async void OnButtonClicked(object sender, EventArgs e)
            {
                List<Repository> repositories = await _restService.GetRepositoriesAsync(Constants.GitHubReposEndpoint);
                collectionView.ItemsSource = repositories;
            }
        }
    }
    ```

    O método `OnButtonClicked`, executado quando alguém toca em [`Button`](xref:Xamarin.Forms.Button), invoca o método `RestService.GetRepositoriesAsync` para recuperar os dados do repositório .NET da API Web do GitHub. O método `GetRepositoriesAsync` exige um argumento `string` que representa o URI da API Web que está sendo invocada e isso é retornado pelo campo `Constants.GitHubReposEndpoint`.

    Depois de recuperar os dados solicitados, a propriedade [`CollectionView.ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) será definida para os dados recuperados.

1. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo dentro de seu simulador remoto de iOS ou do Android Emulator escolhido. Toque no [`Button`](xref:Xamarin.Forms.Button) para recuperar os dados do repositório .NET do GitHub:

    [![Captura de tela de repositórios .NET do GitHub, no iOS e no Android](../images/consume-web-service.png)](../images/consume-web-service-large.png#lightbox)

    Interrompa o aplicativo no Visual Studio.

    Para obter mais informações sobre como consumir serviços Web baseados em REST no Xamarin.Forms, confira [Consumir um serviço Web RESTful (guia)](~/xamarin-forms/data-cloud/web-services/rest.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

1. No **Painel de Soluções**, no projeto **WebServiceTutorial**, clique duas vezes em **MainPage.xaml** para abri-lo. Em seguida, em **MainPage.xaml**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="WebServiceTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Button Text="Get Repository Data"
                    Clicked="OnButtonClicked" />
            <CollectionView x:Name="collectionView">
                <CollectionView.ItemTemplate>
                    <DataTemplate>
                        <StackLayout>
                            <Label Text="{Binding Name}"
                                   FontSize="Medium" />
                            <Label Text="{Binding Description}"
                                   TextColor="Silver"
                                   FontSize="Small" />
                            <Label Text="{Binding GitHubHomeUrl}"
                                   TextColor="Gray"
                                   FontSize="Caption" />
                        </StackLayout>
                    </DataTemplate>
                </CollectionView.ItemTemplate>
            </CollectionView>
        </StackLayout>
    </ContentPage>
    ```

    Esse código define declarativamente a interface do usuário para a página, que é composta por um [`Button`](xref:Xamarin.Forms.Button) e um [`CollectionView`](xref:Xamarin.Forms.CollectionView). O `Button` define seu evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) para um manipulador de eventos chamado `OnButtonClicked` que será criado na próxima etapa. O `CollectionView` define a propriedade [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) para um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que define a aparência de cada item no `CollectionView`. O filho do `DataTemplate` é um [`StackLayout`](xref:Xamarin.Forms.StackLayout), que contém três objetos [`Label`](xref:Xamarin.Forms.Label). Os objetos `Label` associam as propriedades [`Text`](xref:Xamarin.Forms.Label.Text) deles às propriedades `Name`, `Description` e `GitHubHomeUrl` de cada objeto `Repository`. Para obter mais informações sobre associação de dados, confira [Associação de Dados do Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

    Além disso, a [`CollectionView`](xref:Xamarin.Forms.CollectionView) tem um nome especificado com o atributo `x:Name`. Isso permite que o arquivo code-behind acesse o objeto usando o nome atribuído.

1. No **Painel de Soluções**, no projeto **WebServiceTutorial**, expanda **MainPage.xaml** e clique duas vezes em **MainPage.xaml.cs** para abri-lo. Então, em **MainPage.xaml.cs**, remova todo o código de modelo e substitua-o pelo código a seguir:

    ```csharp
    using System;
    using System.Collections.Generic;
    using Xamarin.Forms;

    namespace WebServiceTutorial
    {
        public partial class MainPage : ContentPage
        {
            RestService _restService;

            public MainPage()
            {
                InitializeComponent();
                _restService = new RestService();
            }

            async void OnButtonClicked(object sender, EventArgs e)
            {
                List<Repository> repositories = await _restService.GetRepositoriesAsync(Constants.GitHubReposEndpoint);
                collectionView.ItemsSource = repositories;
            }
        }
    }
    ```

    O método `OnButtonClicked`, executado quando alguém toca em [`Button`](xref:Xamarin.Forms.Button), invoca o método `RestService.GetRepositoriesAsync` para recuperar os dados do repositório .NET da API Web do GitHub. O método `GetRepositoriesAsync` exige um argumento `string` que representa o URI da API Web que está sendo invocada e isso é retornado pelo campo `Constants.GitHubReposEndpoint`.

    Depois de recuperar os dados solicitados, a propriedade [`CollectionView.ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) será definida para os dados recuperados.

1. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com um botão Reproduzir) para iniciar o aplicativo dentro do simulador de iOS ou do Android Emulator escolhido. Toque no [`Button`](xref:Xamarin.Forms.Button) para recuperar os dados do repositório .NET do GitHub:

    [![Captura de tela de repositórios .NET do GitHub, no iOS e no Android](../images/consume-web-service.png)](../images/consume-web-service-large.png#lightbox)

    Interrompa o aplicativo no Visual Studio para Mac.

    Para obter mais informações sobre como consumir serviços Web baseados em REST no Xamarin.Forms, confira [Consumir um serviço Web RESTful (guia)](~/xamarin-forms/data-cloud/web-services/rest.md).
