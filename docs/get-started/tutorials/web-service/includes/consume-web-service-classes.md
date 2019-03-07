Neste exercício, você criará uma interface do usuário para consumir a classe `RestService`, que, por sua vez, recupera dados da API Web [OpenWeatherMap](https://openweathermap.org/).

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. No **Gerenciador de Soluções**, no projeto **WebServiceTutorial**, clique duas vezes em **MainPage.xaml** para abri-lo. Em seguida, em **MainPage.xaml**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="WebServiceTutorial.MainPage">
        <Grid Margin="20,35,20,20">
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="0.4*" />
                <ColumnDefinition Width="0.6*" />
            </Grid.ColumnDefinitions>
            <Grid.RowDefinitions>
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
            </Grid.RowDefinitions>
            <Entry x:Name="cityEntry"
                   Grid.ColumnSpan="2"
                   Text="Seattle" />
            <Button Grid.ColumnSpan="2"
                    Grid.Row="1"
                    Text="Get Weather"
                    Clicked="OnButtonClicked" />                
            <Label Grid.Row="2"
                   Text="Location:" />
            <Label Grid.Row="2"
                   Grid.Column="1"
                   Text="{Binding Title}" />            
            <Label Grid.Row="3"
                   Text="Temperature:" />
            <Label Grid.Row="3"
                   Grid.Column="1"
                   Text="{Binding Main.Temperature}" />            
            <Label Grid.Row="4"
                   Text="Wind Speed:" />
            <Label Grid.Row="4"
                   Grid.Column="1"
                   Text="{Binding Wind.Speed}" />            
            <Label Grid.Row="5"
                   Text="Humidity:" />
            <Label Grid.Row="5"
                   Grid.Column="1"
                   Text="{Binding Main.Humidity}" />            
            <Label Grid.Row="6"
                   Text="Visibility:" />
            <Label Grid.Row="6"
                   Grid.Column="1"
                   Text="{Binding Weather[0].Visibility}" />
        </Grid>
    </ContentPage>
    ```

    Esse código define declarativamente a interface do usuário para a página, que consiste em uma [`Entry`](xref:Xamarin.Forms.Entry), um [`Button`](xref:Xamarin.Forms.Button) e uma série de instâncias [`Label`](xref:Xamarin.Forms.Label) em uma [`Grid`](xref:Xamarin.Forms.Grid). O `Entry` é preenchido com "Seattle" configurando sua propriedade [`Text`](xref:Xamarin.Forms.Entry.Text). O `Button` define seu evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) para um manipulador de eventos chamado `OnButtonClicked` que será criado na próxima etapa. Metade das instâncias `Label` exibem texto estático, com a associação de dados das instâncias restantes para propriedades `WeatherData`. Em tempo de execução, as instâncias `Label` que usam a associação de dados analisarão suas respectivas propriedades [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) para o objeto `WeatherData` a ser usado em suas expressões de associação. Para obter mais informações sobre associação de dados, confira [Associação de Dados do Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

    Além disso, a [`Entry`](xref:Xamarin.Forms.Entry) tem um nome especificado com o atributo `x:Name`. Isso permite que o arquivo code-behind acesse o objeto usando o nome atribuído.

1. No **Gerenciador de Soluções**, no projeto **WebServiceTutorial**, expanda **MainPage.xaml** e clique duas vezes em **MainPage.xaml.cs** para abri-lo. Então, em **MainPage.xaml.cs**, remova todo o código de modelo e substitua-o pelo código a seguir:

    ```csharp
    using System;
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
                if (!string.IsNullOrWhiteSpace(cityEntry.Text))
                {
                    WeatherData weatherData = await _restService.GetWeatherDataAsync(GenerateRequestUri(Constants.OpenWeatherMapEndpoint));
                    BindingContext = weatherData;
                }
            }

            string GenerateRequestUri(string endpoint)
            {
                string requestUri = endpoint;
                requestUri += $"?q={cityEntry.Text}";
                requestUri += "&units=imperial"; // or units=metric
                requestUri += $"&APPID={Constants.OpenWeatherMapAPIKey}";
                return requestUri;
            }
        }
    }
    ```

    O método `OnButtonClicked`, que é executado quando o [`Button`](xref:Xamarin.Forms.Button) é tocado, invoca o método `RestService.GetWeatherDataAsync` para recuperar dados de clima para a cidade cujo nome foi inserido na [`Entry`](xref:Xamarin.Forms.Entry). O método `GetWeatherDataAsync` exige um argumento `string` que representa o URI da API Web que está sendo invocada e isso é produzido pelo método `GenerateRequestUri`. Esse método usa o endereço do ponto de extremidade armazenado na constante `OpenWeatherMapEndpoint` e adiciona a ele parâmetros de consulta que especificam:

    - A cidade para a qual os dados de clima são solicitados.
    - As unidades nas quais retornar dados de clima.
    - Sua chave de API pessoal.

    Depois de recuperar os dados meteorológicos solicitados, o [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) da página é definido como o objeto `WeatherData`. Para obter mais informações sobre a propriedade `BindingContext`, confira [Associações com um contexto de associação](~/xamarin-forms/app-fundamentals/data-binding/basic-bindings.md#bindings-with-a-binding-context) no guia [Associação de dados do Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

    > [!IMPORTANT]
    > A propriedade [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) é herdada por meio da árvore visual. Portanto, porque ela foi configurada no objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage), objetos filho do `ContentPage` herdam seu valor, incluindo as instâncias [`Label`](xref:Xamarin.Forms.Label).

1. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo dentro de seu simulador remoto de iOS ou do Android Emulator escolhido. Toque no [`Button`](xref:Xamarin.Forms.Button) para recuperar dados de clima atual para Seattle:

    [![Captura de tela de dados de clima de Seattle, em iOS e Android](../images/consume-web-service.png "Dados de clima de Seattle")](../images/consume-web-service-large.png#lightbox "Dados de clima de Seattle")

    > [!IMPORTANT]
    > Sua chave de API OpenWeatherMap pessoal deve ser definida como o valor da constante `OpenWeatherMapAPIKey` na classe `Constants`.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. No **Painel de Soluções**, no projeto **WebServiceTutorial**, clique duas vezes em **MainPage.xaml** para abri-lo. Em seguida, em **MainPage.xaml**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="WebServiceTutorial.MainPage">
        <Grid Margin="20,35,20,20">
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="0.4*" />
                <ColumnDefinition Width="0.6*" />
            </Grid.ColumnDefinitions>
            <Grid.RowDefinitions>
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
            </Grid.RowDefinitions>
            <Entry x:Name="cityEntry"
                   Grid.ColumnSpan="2"
                   Text="Seattle" />
            <Button Grid.ColumnSpan="2"
                    Grid.Row="1"
                    Text="Get Weather"
                    Clicked="OnButtonClicked" />                
            <Label Grid.Row="2"
                   Text="Location:" />
            <Label Grid.Row="2"
                   Grid.Column="1"
                   Text="{Binding Title}" />            
            <Label Grid.Row="3"
                   Text="Temperature:" />
            <Label Grid.Row="3"
                   Grid.Column="1"
                   Text="{Binding Main.Temperature}" />            
            <Label Grid.Row="4"
                   Text="Wind Speed:" />
            <Label Grid.Row="4"
                   Grid.Column="1"
                   Text="{Binding Wind.Speed}" />            
            <Label Grid.Row="5"
                   Text="Humidity:" />
            <Label Grid.Row="5"
                   Grid.Column="1"
                   Text="{Binding Main.Humidity}" />            
            <Label Grid.Row="6"
                   Text="Visibility:" />
            <Label Grid.Row="6"
                   Grid.Column="1"
                   Text="{Binding Weather[0].Visibility}" />
        </Grid>
    </ContentPage>
    ```

    Esse código define declarativamente a interface do usuário para a página, que consiste em uma [`Entry`](xref:Xamarin.Forms.Entry), um [`Button`](xref:Xamarin.Forms.Button) e uma série de instâncias [`Label`](xref:Xamarin.Forms.Label) em uma [`Grid`](xref:Xamarin.Forms.Grid). O `Entry` é preenchido com "Seattle" configurando sua propriedade [`Text`](xref:Xamarin.Forms.Entry.Text). O `Button` define seu evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) para um manipulador de eventos chamado `OnButtonClicked` que será criado na próxima etapa. Metade das instâncias `Label` exibem texto estático, com a associação de dados das instâncias restantes para propriedades `WeatherData`. Em tempo de execução, as instâncias `Label` que usam a associação de dados analisarão suas respectivas propriedades [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) para o objeto `WeatherData` a ser usado em suas expressões de associação. Para obter mais informações sobre associação de dados, confira [Associação de Dados do Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

    Além disso, a [`Entry`](xref:Xamarin.Forms.Entry) tem um nome especificado com o atributo `x:Name`. Isso permite que o arquivo code-behind acesse o objeto usando o nome atribuído.

    Para obter mais informações sobre como consumir serviços Web baseados em REST no Xamarin.Forms, confira [Consumindo um serviço Web RESTful (guia)](~/xamarin-forms/data-cloud/consuming/rest.md).

1. No **Painel de Soluções**, no projeto **WebServiceTutorial**, expanda **MainPage.xaml** e clique duas vezes em **MainPage.xaml.cs** para abri-lo. Então, em **MainPage.xaml.cs**, remova todo o código de modelo e substitua-o pelo código a seguir:

    ```csharp
    using System;
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
                if (!string.IsNullOrWhiteSpace(cityEntry.Text))
                {
                    WeatherData weatherData = await _restService.GetWeatherDataAsync(GenerateRequestUri(Constants.OpenWeatherMapEndpoint));
                    BindingContext = weatherData;
                }
            }

            string GenerateRequestUri(string endpoint)
            {
                string requestUri = endpoint;
                requestUri += $"?q={cityEntry.Text}";
                requestUri += "&units=imperial"; // or units=metric
                requestUri += $"&APPID={Constants.OpenWeatherMapAPIKey}";
                return requestUri;
            }
        }
    }
    ```

    O método `OnButtonClicked`, que é executado quando o [`Button`](xref:Xamarin.Forms.Button) é tocado, invoca o método `RestService.GetWeatherDataAsync` para recuperar dados de clima para a cidade cujo nome foi inserido na [`Entry`](xref:Xamarin.Forms.Entry). O método `GetWeatherDataAsync` exige um argumento `string` que representa o URI da API Web que está sendo invocada e isso é produzido pelo método `GenerateRequestUri`. Esse método usa o endereço do ponto de extremidade armazenado na constante `OpenWeatherMapEndpoint` e adiciona a ele parâmetros de consulta que especificam:

    - A cidade para a qual os dados de clima são solicitados.
    - As unidades nas quais retornar dados de clima.
    - Sua chave de API pessoal.

    Depois de recuperar os dados meteorológicos solicitados, o [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) da página é definido como o objeto `WeatherData`. Para obter mais informações sobre a propriedade `BindingContext`, confira [Associações com um contexto de associação](~/xamarin-forms/app-fundamentals/data-binding/basic-bindings.md#bindings-with-a-binding-context) no guia [Associação de dados do Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

    > [!IMPORTANT]
    > A propriedade [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) é herdada por meio da árvore visual. Portanto, porque ela foi configurada no objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage), objetos filho do `ContentPage` herdam seu valor, incluindo as instâncias [`Label`](xref:Xamarin.Forms.Label).

1. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com um botão Reproduzir) para iniciar o aplicativo dentro do simulador de iOS ou do Android Emulator escolhido. Toque no [`Button`](xref:Xamarin.Forms.Button) para recuperar dados de clima atual para Seattle:

    [![Captura de tela de dados de clima de Seattle, em iOS e Android](../images/consume-web-service.png "Dados de clima de Seattle")](../images/consume-web-service-large.png#lightbox "Dados de clima de Seattle")

    > [!IMPORTANT]
    > Sua chave de API OpenWeatherMap pessoal deve ser definida como o valor da constante `OpenWeatherMapAPIKey` na classe `Constants`.

    Para obter mais informações sobre como consumir serviços Web baseados em REST no Xamarin.Forms, confira [Consumindo um serviço Web RESTful (guia)](~/xamarin-forms/data-cloud/consuming/rest.md).
