---
ms.openlocfilehash: caee3eeda90a560f032c17657072ae5ba5023a69
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "77135148"
---
Neste exercício, você criará uma interface do usuário para consumir as classes de acesso a dados criadas anteriormente.

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. No **Gerenciador de Soluções**, no projeto **LocalDatabaseTutorial**, clique duas vezes em **MainPage.xaml** para abri-lo. Em seguida, em **MainPage.xaml**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="LocalDatabaseTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Entry x:Name="nameEntry"
                   Placeholder="Enter name" />
            <Entry x:Name="ageEntry"
                   Placeholder="Enter age" />
            <Button Text="Add to Database"
                    Clicked="OnButtonClicked" />
            <ListView x:Name="listView">
                <ListView.ItemTemplate>
                    <DataTemplate>
                        <TextCell Text="{Binding Name}"
                                  Detail="{Binding Age}" />
                    </DataTemplate>
                </ListView.ItemTemplate>
            </ListView>
        </StackLayout>
    </ContentPage>
    ```

    Esse código define declarativamente a interface do usuário para a página, que consiste em duas instâncias [`Entry`](xref:Xamarin.Forms.Entry), um [`Button`](xref:Xamarin.Forms.Button) e uma [`ListView`](xref:Xamarin.Forms.ListView) em um [`StackLayout`](xref:Xamarin.Forms.StackLayout). Cada `Entry` tem sua propriedade definida [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder), que especifica o texto de espaço reservado mostrado antes da entrada do usuário. O `Button` define seu evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) para um manipulador de eventos chamado `OnButtonClicked` que será criado na próxima etapa. O `ListView` define sua propriedade [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) como um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que usa uma [`TextCell`](xref:Xamarin.Forms.TextCell) para definir a aparência de cada linha na [`ListView`](xref:Xamarin.Forms.ListView). Os dados `TextCell` associam suas propriedades [`Text`](xref:Xamarin.Forms.TextCell.Text) e [`Detail`](xref:Xamarin.Forms.TextCell.Detail) às propriedades `Name` e `Age` de cada objeto `Person`, respectivamente.

    Além disso, as instâncias [`Entry`](xref:Xamarin.Forms.Entry) e [`ListView`](xref:Xamarin.Forms.ListView) têm nomes especificados com o atributo `x:Name`. Isso permite que o arquivo code-behind acesse esses objetos usando os nomes atribuídos.

1. No **Gerenciador de Soluções**, no projeto **LocalDatabaseTutorial**, expanda **MainPage.xaml** e clique duas vezes em **MainPage.xaml.cs** para abri-lo. Em seguida, em **MainPage.xaml.cs**, adicione a substituição `OnAppearing` e o manipulador de eventos `OnButtonClicked` à classe:

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();
        listView.ItemsSource = await App.Database.GetPeopleAsync();
    }

    async void OnButtonClicked(object sender, EventArgs e)
    {
        if (!string.IsNullOrWhiteSpace(nameEntry.Text) && !string.IsNullOrWhiteSpace(ageEntry.Text))
        {
            await App.Database.SavePersonAsync(new Person
            {
                Name = nameEntry.Text,
                Age = int.Parse(ageEntry.Text)
            });

            nameEntry.Text = ageEntry.Text = string.Empty;
            listView.ItemsSource = await App.Database.GetPeopleAsync();
        }
    }
    ```

    O método `OnAppearing` preenche [`ListView`](xref:Xamarin.Forms.ListView) com todos os dados armazenados no banco de dados. O método `OnButtonClicked`, que é executado quando o [`Button`](xref:Xamarin.Forms.Button) é tocado, salva os dados inseridos no banco de dados antes de limpar as instâncias [`Entry`](xref:Xamarin.Forms.Entry) e atualizar os dados na `ListView`.

    > [!NOTE]
    > A substituição do método `OnAppearing` é executada após a [`ContentPage`](xref:Xamarin.Forms.ContentPage) ser apresentada, mas apenas antes de se tornar visível. Portanto, esse é um bom lugar para definir o conteúdo das exibições do Xamarin.Forms.

1. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo dentro de seu simulador remoto de iOS ou do Android Emulator escolhido.

    Insira vários itens de dados tocando em [`Button`](xref:Xamarin.Forms.Button) para cada item de dados. Isso salvará os dados no banco de dados e preencherá novamente a [`ListView`](xref:Xamarin.Forms.ListView) com todos os dados do banco de dados:

    [![Captura de tela da persistência de dados do banco de dados SQLite.NET local, no iOS e no Android](../images/consume-data-access-classes.png "Persistência de dados do banco de dados local")](../images/consume-data-access-classes-large.png#lightbox "Persistência de dados do banco de dados local")

    Para obter mais informações sobre bancos de dados locais no Xamarin.Forms, confira [Bancos de dados locais do Xamarin.Forms (guia)](~/xamarin-forms/data-cloud/data/databases.md)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

1. No **Painel de Soluções**, no projeto **LocalDatabaseTutorial**, clique duas vezes em **MainPage.xaml** para abri-lo. Em seguida, em **MainPage.xaml**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="LocalDatabaseTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Entry x:Name="nameEntry"
                   Placeholder="Enter name" />
            <Entry x:Name="ageEntry"
                   Placeholder="Enter age" />
            <Button Text="Add to Database"
                    Clicked="OnButtonClicked" />
            <ListView x:Name="listView">
                <ListView.ItemTemplate>
                    <DataTemplate>
                        <TextCell Text="{Binding Name}"
                                  Detail="{Binding Age}" />
                    </DataTemplate>
                </ListView.ItemTemplate>
            </ListView>
        </StackLayout>
    </ContentPage>
    ```

    Esse código define declarativamente a interface do usuário para a página, que consiste em duas instâncias [`Entry`](xref:Xamarin.Forms.Entry), um [`Button`](xref:Xamarin.Forms.Button) e uma [`ListView`](xref:Xamarin.Forms.ListView) em um [`StackLayout`](xref:Xamarin.Forms.StackLayout). Cada `Entry` tem sua propriedade definida [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder), que especifica o texto de espaço reservado mostrado antes da entrada do usuário. O `Button` define seu evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) para um manipulador de eventos chamado `OnButtonClicked` que será criado na próxima etapa. O `ListView` define sua propriedade [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) como um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que usa uma [`TextCell`](xref:Xamarin.Forms.TextCell) para definir a aparência de cada linha na [`ListView`](xref:Xamarin.Forms.ListView). Os dados `TextCell` associam suas propriedades [`Text`](xref:Xamarin.Forms.TextCell.Text) e [`Detail`](xref:Xamarin.Forms.TextCell.Detail) às propriedades `Name` e `Age` de cada objeto `Person`, respectivamente.

    Além disso, as instâncias [`Entry`](xref:Xamarin.Forms.Entry) e [`ListView`](xref:Xamarin.Forms.ListView) têm nomes especificados com o atributo `x:Name`. Isso permite que o arquivo code-behind acesse esses objetos usando os nomes atribuídos.

1. No **Painel de Soluções**, no projeto **LocalDatabaseTutorial**, expanda **MainPage.xaml** e clique duas vezes em **MainPage.xaml.cs** para abri-lo. Em seguida, em **MainPage.xaml.cs**, adicione a substituição `OnAppearing` e o manipulador de eventos `OnButtonClicked` à classe:

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();
        listView.ItemsSource = await App.Database.GetPeopleAsync();
    }

    async void OnButtonClicked(object sender, EventArgs e)
    {
        if (!string.IsNullOrWhiteSpace(nameEntry.Text) && !string.IsNullOrWhiteSpace(ageEntry.Text))
        {
            await App.Database.SavePersonAsync(new Person
            {
                Name = nameEntry.Text,
                Age = int.Parse(ageEntry.Text)
            });

            nameEntry.Text = ageEntry.Text = string.Empty;
            listView.ItemsSource = await App.Database.GetPeopleAsync();
        }
    }
    ```

    O método `OnAppearing` preenche [`ListView`](xref:Xamarin.Forms.ListView) com todos os dados armazenados no banco de dados. O método `OnButtonClicked`, que é executado quando o [`Button`](xref:Xamarin.Forms.Button) é tocado, salva os dados inseridos no banco de dados antes de limpar as instâncias [`Entry`](xref:Xamarin.Forms.Entry) e atualizar os dados na `ListView`.

    > [!NOTE]
    > A substituição do método `OnAppearing` é executada após a [`ContentPage`](xref:Xamarin.Forms.ContentPage) ser apresentada, mas apenas antes de se tornar visível. Portanto, esse é um bom lugar para definir o conteúdo das exibições do Xamarin.Forms.

1. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com um botão Reproduzir) para iniciar o aplicativo dentro do simulador de iOS ou do Android Emulator escolhido.

    Insira vários itens de dados tocando em [`Button`](xref:Xamarin.Forms.Button) para cada item de dados. Isso salvará os dados no banco de dados e preencherá novamente a [`ListView`](xref:Xamarin.Forms.ListView) com todos os dados do banco de dados:

    [![Captura de tela da persistência de dados do banco de dados SQLite.NET local, no iOS e no Android](../images/consume-data-access-classes.png "Persistência de dados do banco de dados local")](../images/consume-data-access-classes-large.png#lightbox "Persistência de dados do banco de dados local")

    Para obter mais informações sobre bancos de dados locais no Xamarin.Forms, confira [Bancos de dados locais do Xamarin.Forms (guia)](~/xamarin-forms/data-cloud/data/databases.md)
