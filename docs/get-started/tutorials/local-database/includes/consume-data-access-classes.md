---
ms.openlocfilehash: 6e546d85c87962b022f72c8c16cb0ece14e9bde6
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98690203"
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
            <CollectionView x:Name="collectionView">
                <CollectionView.ItemTemplate>
                    <DataTemplate>
                        <StackLayout>
                            <Label Text="{Binding Name}"
                                   FontSize="Medium" />
                            <Label Text="{Binding Age}"
                                   TextColor="Silver"
                                   FontSize="Small" />
                        </StackLayout>
                    </DataTemplate>
                </CollectionView.ItemTemplate>
            </CollectionView>
        </StackLayout>
    </ContentPage>
    ```

    Esse código define declarativamente a interface do usuário para a página, que consiste em duas instâncias [`Entry`](xref:Xamarin.Forms.Entry), um [`Button`](xref:Xamarin.Forms.Button) e uma [`CollectionView`](xref:Xamarin.Forms.CollectionView) em um [`StackLayout`](xref:Xamarin.Forms.StackLayout). Cada `Entry` tem sua propriedade definida [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder), que especifica o texto de espaço reservado mostrado antes da entrada do usuário. O `Button` define seu evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) para um manipulador de eventos chamado `OnButtonClicked` que será criado na próxima etapa. A `CollectionView` define sua propriedade [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) como um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que usa um `StackLayout` e dois objetos [`Label`](xref:Xamarin.Forms.Label) para definir a aparência de cada linha na `CollectionView`. Os objetos `Label` associam as propriedades deles `Text` às propriedades `Name` e `Age` de cada objeto `Person`, respectivamente.

    Além disso, as instâncias [`Entry`](xref:Xamarin.Forms.Entry) e [`CollectionView`](xref:Xamarin.Forms.CollectionView) têm nomes especificados com o atributo `x:Name`. Isso permite que o arquivo code-behind acesse esses objetos usando os nomes atribuídos.

1. No **Gerenciador de Soluções**, no projeto **LocalDatabaseTutorial**, expanda **MainPage.xaml** e clique duas vezes em **MainPage.xaml.cs** para abri-lo. Em seguida, em **MainPage.xaml.cs**, adicione a substituição `OnAppearing` e o manipulador de eventos `OnButtonClicked` à classe:

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();
        collectionView.ItemsSource = await App.Database.GetPeopleAsync();
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
            collectionView.ItemsSource = await App.Database.GetPeopleAsync();
        }
    }
    ```

    O método `OnAppearing` preenche [`CollectionView`](xref:Xamarin.Forms.CollectionView) com todos os dados armazenados no banco de dados. O método `OnButtonClicked`, que é executado quando o [`Button`](xref:Xamarin.Forms.Button) é tocado, salva os dados inseridos no banco de dados antes de limpar as instâncias [`Entry`](xref:Xamarin.Forms.Entry) e atualizar os dados na `CollectionView`.

    > [!NOTE]
    > A substituição do método `OnAppearing` é executada após a [`ContentPage`](xref:Xamarin.Forms.ContentPage) ser apresentada, mas apenas antes de se tornar visível. Portanto, esse é um bom lugar para definir o conteúdo das exibições do Xamarin.Forms.

1. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo dentro de seu simulador remoto de iOS ou do Android Emulator escolhido.

    Insira vários itens de dados tocando em [`Button`](xref:Xamarin.Forms.Button) para cada item de dados. Isso salvará os dados no banco de dados e preencherá novamente a [`CollectionView`](xref:Xamarin.Forms.CollectionView) com todos os dados do banco de dados:

    [![Captura de tela da persistência de dados do banco de dados SQLite.NET local, no iOS e no Android](../images/consume-data-access-classes.png "Persistência de dados do banco de dados local")](../images/consume-data-access-classes-large.png#lightbox "Persistência de dados do banco de dados local")

    Interrompa o aplicativo no Visual Studio.

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
            <CollectionView x:Name="collectionView">
                <CollectionView.ItemTemplate>
                    <DataTemplate>
                        <StackLayout>
                            <Label Text="{Binding Name}"
                                   FontSize="Medium" />
                            <Label Text="{Binding Age}"
                                   TextColor="Silver"
                                   FontSize="Small" />
                        </StackLayout>
                    </DataTemplate>
                </CollectionView.ItemTemplate>
            </CollectionView>
        </StackLayout>
    </ContentPage>
    ```

    Esse código define declarativamente a interface do usuário para a página, que consiste em duas instâncias [`Entry`](xref:Xamarin.Forms.Entry), um [`Button`](xref:Xamarin.Forms.Button) e uma [`CollectionView`](xref:Xamarin.Forms.CollectionView) em um [`StackLayout`](xref:Xamarin.Forms.StackLayout). Cada `Entry` tem sua propriedade definida [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder), que especifica o texto de espaço reservado mostrado antes da entrada do usuário. O `Button` define seu evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) para um manipulador de eventos chamado `OnButtonClicked` que será criado na próxima etapa. A `CollectionView` define sua propriedade [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) como um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que usa um `StackLayout` e dois objetos [`Label`](xref:Xamarin.Forms.Label) para definir a aparência de cada linha na `CollectionView`. Os objetos `Label` associam as propriedades deles `Text` às propriedades `Name` e `Age` de cada objeto `Person`, respectivamente.

    Além disso, as instâncias [`Entry`](xref:Xamarin.Forms.Entry) e [`ListView`](xref:Xamarin.Forms.ListView) têm nomes especificados com o atributo `x:Name`. Isso permite que o arquivo code-behind acesse esses objetos usando os nomes atribuídos.

1. No **Painel de Soluções**, no projeto **LocalDatabaseTutorial**, expanda **MainPage.xaml** e clique duas vezes em **MainPage.xaml.cs** para abri-lo. Em seguida, em **MainPage.xaml.cs**, adicione a substituição `OnAppearing` e o manipulador de eventos `OnButtonClicked` à classe:

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();
        collectionView.ItemsSource = await App.Database.GetPeopleAsync();
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
            collectionView.ItemsSource = await App.Database.GetPeopleAsync();
        }
    }
    ```

    O método `OnAppearing` preenche [`CollectionView`](xref:Xamarin.Forms.CollectionView) com todos os dados armazenados no banco de dados. O método `OnButtonClicked`, que é executado quando o [`Button`](xref:Xamarin.Forms.Button) é tocado, salva os dados inseridos no banco de dados antes de limpar as instâncias [`Entry`](xref:Xamarin.Forms.Entry) e atualizar os dados na `CollectionView`.

    > [!NOTE]
    > A substituição do método `OnAppearing` é executada após a [`ContentPage`](xref:Xamarin.Forms.ContentPage) ser apresentada, mas apenas antes de se tornar visível. Portanto, esse é um bom lugar para definir o conteúdo das exibições do Xamarin.Forms.

1. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com um botão Reproduzir) para iniciar o aplicativo dentro do simulador de iOS ou do Android Emulator escolhido.

    Insira vários itens de dados tocando em [`Button`](xref:Xamarin.Forms.Button) para cada item de dados. Isso salvará os dados no banco de dados e preencherá novamente a [`CollectionView`](xref:Xamarin.Forms.CollectionView) com todos os dados do banco de dados:

    [![Captura de tela da persistência de dados do banco de dados SQLite.NET local, no iOS e no Android](../images/consume-data-access-classes.png "Persistência de dados do banco de dados local")](../images/consume-data-access-classes-large.png#lightbox "Persistência de dados do banco de dados local")

    Interrompa o aplicativo no Visual Studio para Mac.

    Para obter mais informações sobre bancos de dados locais no Xamarin.Forms, confira [Bancos de dados locais do Xamarin.Forms (guia)](~/xamarin-forms/data-cloud/data/databases.md)
