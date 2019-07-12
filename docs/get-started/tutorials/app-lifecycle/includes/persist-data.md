---
ms.openlocfilehash: 16ceaba572ca932777bb366d9f7c58f6dcb24f70
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841488"
---
A subclasse [`Application`](xref:Xamarin.Forms.Application) tem um dicionário [`Properties`](xref:Xamarin.Forms.Application.Properties) estático que pode ser usado para armazenar dados entre alterações de estado do ciclo de vida. Esse dicionário usa uma chave `string` e armazena um valor de `object`. O dicionário é salvo automaticamente no dispositivo e é populado novamente quando o aplicativo é reiniciado.

> [!IMPORTANT]
> O dicionário [`Properties`](xref:Xamarin.Forms.Application.Properties) pode serializar apenas tipos primitivos para armazenamento.

Neste exercício, você modificará o aplicativo para persistir o texto de um [`Entry`](xref:Xamarin.Forms.Entry) após ser passado para segundo plano e restaurará o texto no `Entry` quando o aplicativo for reiniciado.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. No **Gerenciador de Soluções**, no projeto **AppLifecycleTutorial**, expanda **App.xaml** e clique duas vezes em **App.xaml.cs** para abri-lo. Em seguida, em **App.xaml.cs**, remova todo o código de modelo e substitua-o pelo código a seguir:

    ```csharp
    using System;
    using Xamarin.Forms;

    namespace AppLifecycleTutorial
    {
        public partial class App : Application
        {
            const string displayText = "displayText";

            public string DisplayText { get; set; }

            public App()
            {
                InitializeComponent();

                MainPage = new MainPage();
            }

            protected override void OnStart()
            {
                Console.WriteLine("OnStart");

                if (Properties.ContainsKey(displayText))
                {
                    DisplayText = (string)Properties[displayText];
                }
            }

            protected override void OnSleep()
            {
                Console.WriteLine("OnSleep");
                Properties[displayText] = DisplayText;
            }

            protected override void OnResume()
            {
                Console.WriteLine("OnResume");
            }
        }
    }
    ```

    Esse código define uma propriedade `DisplayText` e um constante `displayText`. Quando o aplicativo é colocado em segundo plano ou encerrado, a substituição do método `OnSleep` adiciona o valor da propriedade `DisplayText` ao dicionário [`Properties`](xref:Xamarin.Forms.Application.Properties), em relação a uma chave de `displayText`. Em seguida, quando o aplicativo é iniciado, desde que o dicionário `Properties` contenha a chave `displayText`, o valor da chave é restaurado na propriedade `DisplayText`.

    > [!IMPORTANT]
    > Sempre verifique no dicionário [`Properties`](xref:Xamarin.Forms.Application.Properties) a presença de uma chave antes de acessá-lo, a fim de evitar erros inesperados.

    Não é necessário restaurar dados do dicionário [`Properties`](xref:Xamarin.Forms.Application.Properties) na sobrecarga de método `OnResume`. Isso ocorre porque, quando um aplicativo é colocado em segundo plano, ele e seu estado ainda estão na memória.

1. No **Gerenciador de Soluções**, no projeto **AppLifecycleTutorial**, clique duas vezes em **MainPage.xaml** para abri-lo. Em seguida, em **MainPage.xaml**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="AppLifecycleTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Entry x:Name="entry"
                   Placeholder="Enter text here"
                   Completed="OnEntryCompleted" />
        </StackLayout>
    </ContentPage>
    ```

    Esse código define declarativamente a interface do usuário para a página, que é composta por um [`Entry`](xref:Xamarin.Forms.Entry) em um [`StackLayout`](xref:Xamarin.Forms.StackLayout). A propriedade [`Entry.Placeholder`](xref:Xamarin.Forms.Entry.Placeholder) especifica o texto de espaço reservado mostrado quando o `Entry` é exibido pela primeira vez e um manipulador de eventos denominado `OnEntryCompleted` é registrado com o evento [`Completed`](xref:Xamarin.Forms.Entry.Completed). Além disso, o `Entry` tem um nome especificado com o atributo `x:Name`. Isso permite que o arquivo code-behind acesse o objeto `Entry` usando o nome atribuído a ele.

1. No **Gerenciador de Soluções**, no projeto **AppLifecycleTutorial**, expanda **MainPage.xaml** e clique duas vezes em **MainPage.xaml.cs** para abri-lo. Em seguida, em **MainPage.xaml.cs**, adicione uma substituição para o método `OnAppearing` e o manipulador de eventos `OnEntryCompleted` à classe:

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();

        entry.Text = (Application.Current as App).DisplayText;
    }

    void OnEntryCompleted(object sender, EventArgs e)
    {
        (Application.Current as App).DisplayText = entry.Text;
    }
    ```

    O método `OnAppearing` recupera o valor da propriedade `App.DisplayText` e o define como o valor de propriedade [`Text`](xref:Xamarin.Forms.Entry.Text) do [`Entry`](xref:Xamarin.Forms.Entry).

    > [!NOTE]
    > A substituição do método `OnAppearing` é executada após a [`ContentPage`](xref:Xamarin.Forms.ContentPage) ser apresentada, mas apenas antes de se tornar visível. Portanto, esse é um bom lugar para definir o conteúdo das exibições do Xamarin.Forms.

    Quando o texto é finalizado no [`Entry`](xref:Xamarin.Forms.Entry), com a tecla de retorno, o método `OnEntryCompleted` é executado e o texto `Entry` é armazenado na propriedade `App.DisplayText`.

1. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo dentro de seu simulador remoto de iOS ou do Android Emulator escolhido.

    Digite algum texto no [`Entry`](xref:Xamarin.Forms.Entry) e pressione a tecla de retorno. Em seguida, coloque o aplicativo em segundo plano tocando botão Página Inicial para invocar o método `OnSleep`.

    Por fim, inicie o aplicativo novamente usando o Visual Studio e o texto que foi inserido em [`Entry`](xref:Xamarin.Forms.Entry) será restaurado:

    [![Captura de tela de uma Entrada cuja propriedade de Texto é persistida entre alterações de estado do ciclo de vida, no iOS e Android](../images/persist-data.png "Entrada cuja propriedade de Texto é persistida entre alterações de estado do ciclo de vida")](../images/persist-data-large.png#lightbox "Entrada cuja propriedade de Texto é persistida entre alterações de estado do ciclo de vida")

    Para obter mais informações sobre como persistir dados no dicionário de propriedades, confira [Dicionário de propriedades](~/xamarin-forms/app-fundamentals/application-class.md#properties-dictionary) no guia [Classe de aplicativo do Xamarin.Forms](~/xamarin-forms/app-fundamentals/application-class.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. No **Painel de Soluções**, no projeto **AppLifecycleTutorial**, expanda **App.xaml** e clique duas vezes em **App.xaml.cs** para abri-lo. Em seguida, em **App.xaml.cs**, remova todo o código de modelo e substitua-o pelo código a seguir:

    ```csharp
    using System;
    using Xamarin.Forms;

    namespace AppLifecycleTutorial
    {
        public partial class App : Application
        {
            const string displayText = "displayText";

            public string DisplayText { get; set; }

            public App()
            {
                InitializeComponent();

                MainPage = new MainPage();
            }

            protected override void OnStart()
            {
                Console.WriteLine("OnStart");

                if (Properties.ContainsKey(displayText))
                {
                    DisplayText = (string)Properties[displayText];
                }
            }

            protected override void OnSleep()
            {
                Console.WriteLine("OnSleep");
                Properties[displayText] = DisplayText;
            }

            protected override void OnResume()
            {
                Console.WriteLine("OnResume");
            }
        }
    }
    ```

    Esse código define uma propriedade `DisplayText` e um constante `displayText`. Quando o aplicativo é colocado em segundo plano ou encerrado, a substituição do método `OnSleep` adiciona o valor da propriedade `DisplayText` ao dicionário [`Properties`](xref:Xamarin.Forms.Application.Properties), em relação a uma chave de `displayText`. Em seguida, quando o aplicativo é iniciado, desde que o dicionário `Properties` contenha a chave `displayText`, o valor da chave é restaurado na propriedade `DisplayText`.

    > [!IMPORTANT]
    > Sempre verifique no dicionário [`Properties`](xref:Xamarin.Forms.Application.Properties) a presença de uma chave antes de acessá-lo, a fim de evitar erros inesperados.

    Não é necessário restaurar dados do dicionário [`Properties`](xref:Xamarin.Forms.Application.Properties) na sobrecarga de método `OnResume`. Isso ocorre porque, quando um aplicativo é colocado em segundo plano, ele e seu estado ainda estão na memória.

1. No **Painel de Soluções**, no projeto **AppLifecycleTutorial**, clique duas vezes em **MainPage.xaml** para abri-lo. Em seguida, em **MainPage.xaml**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="AppLifecycleTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Entry x:Name="entry"
                   Placeholder="Enter text here"
                   Completed="OnEntryCompleted" />
        </StackLayout>
    </ContentPage>
    ```

    Esse código define declarativamente a interface do usuário para a página, que é composta por um [`Entry`](xref:Xamarin.Forms.Entry) em um [`StackLayout`](xref:Xamarin.Forms.StackLayout). A propriedade [`Entry.Placeholder`](xref:Xamarin.Forms.Entry.Placeholder) especifica o texto de espaço reservado mostrado quando o `Entry` é exibido pela primeira vez e um manipulador de eventos denominado `OnEntryCompleted` é registrado com o evento [`Completed`](xref:Xamarin.Forms.Entry.Completed). Além disso, o `Entry` tem um nome especificado com o atributo `x:Name`. Isso permite que o arquivo code-behind acesse o objeto `Entry` usando o nome atribuído a ele.

1. No **Painel de Soluções**, no projeto **AppLifecycleTutorial**, expanda **MainPage.xaml** e clique duas vezes em **MainPage.xaml.cs** para abri-lo. Em seguida, em **MainPage.xaml.cs**, adicione uma substituição para o método `OnAppearing` e o manipulador de eventos `OnEntryCompleted` à classe:

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();

        entry.Text = (Application.Current as App).DisplayText;
    }

    void OnEntryCompleted(object sender, EventArgs e)
    {
        (Application.Current as App).DisplayText = entry.Text;
    }
    ```

    O método `OnAppearing` recupera o valor da propriedade `App.DisplayText` e o define como o valor de propriedade [`Text`](xref:Xamarin.Forms.Entry.Text) do [`Entry`](xref:Xamarin.Forms.Entry).

    > [!NOTE]
    > A substituição do método `OnAppearing` é executada após a [`ContentPage`](xref:Xamarin.Forms.ContentPage) ser apresentada, mas apenas antes de se tornar visível. Portanto, esse é um bom lugar para definir o conteúdo das exibições do Xamarin.Forms.

    Quando o texto é finalizado no [`Entry`](xref:Xamarin.Forms.Entry), com a tecla de retorno, o método `OnEntryCompleted` é executado e o texto `Entry` é armazenado na propriedade `App.DisplayText`.

1. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com um botão Reproduzir) para iniciar o aplicativo dentro do simulador de iOS ou do Android Emulator escolhido.

    Digite algum texto no [`Entry`](xref:Xamarin.Forms.Entry) e pressione a tecla de retorno. Em seguida, coloque o aplicativo em segundo plano tocando botão Página Inicial para invocar o método `OnSleep`.

    Por fim, inicie o aplicativo novamente usando o Visual Studio para Mac e o texto que foi inserido em [`Entry`](xref:Xamarin.Forms.Entry) será restaurado:

    [![Captura de tela de uma Entrada cuja propriedade de Texto é persistida entre alterações de estado do ciclo de vida, no iOS e Android](../images/persist-data.png "Entrada cuja propriedade de Texto é persistida entre alterações de estado do ciclo de vida")](../images/persist-data-large.png#lightbox "Entrada cuja propriedade de Texto é persistida entre alterações de estado do ciclo de vida")

    Para obter mais informações sobre como persistir dados no dicionário de propriedades, confira [Dicionário de propriedades](~/xamarin-forms/app-fundamentals/application-class.md#properties-dictionary) no guia [Classe de aplicativo do Xamarin.Forms](~/xamarin-forms/app-fundamentals/application-class.md).
