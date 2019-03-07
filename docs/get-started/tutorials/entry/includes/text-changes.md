# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Em **MainPage.xaml**, modifique a declaração [`Entry`](xref:Xamarin.Forms.Entry) de modo que ela defina um manipulador para os eventos [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) e [`Completed`](xref:Xamarin.Forms.Entry.Completed):

    ```xaml
    <Entry Placeholder="Enter text"
           TextChanged="OnEntryTextChanged"
           Completed="OnEntryCompleted" />
    ```

    Esse código define o evento [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) para um manipulador de eventos chamado `OnEntryTextChanged` e o evento [`Completed`](xref:Xamarin.Forms.Entry.Completed) para um manipulador de eventos chamado `OnEntryCompleted`. Ambos os manipuladores de eventos serão criados na próxima etapa.

1. No **Gerenciador de Soluções**, no projeto **LabelTutorial**, expanda **MainPage.xaml** e clique duas vezes em **MainPage.xaml.cs** para abri-lo. Em seguida, em **MainPage.xaml.cs**, adicione os manipuladores de eventos `OnEntryTextChanged` e `OnEntryCompleted` à classe:

    ```csharp
    void OnEntryTextChanged(object sender, TextChangedEventArgs e)
    {
        string oldText = e.OldTextValue;
        string newText = e.NewTextValue;
    }

    void OnEntryCompleted(object sender, EventArgs e)
    {
        string text = ((Entry)sender).Text;
    }
    ```

    Quando o texto na [`Entry`](xref:Xamarin.Forms.Entry) mudar, o método `OnEntryTextChanged` será executado. O argumento `sender` é o objeto `Entry` responsável por acionar o evento `TextChanged` e pode ser usado para acessar o objeto `Entry`. O argumento [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) fornece os valores novos e antigos do texto de antes e depois da alteração do texto.

    Quando você finalizar o texto na [`Entry`](xref:Xamarin.Forms.Entry) com a tecla de retorno, o método `OnEntryCompleted` será executado. O argumento `sender` é o objeto `Entry` responsável por acionar o evento `TextChanged` e pode ser usado para acessar o objeto `Entry`.

    > [!IMPORTANT]
    > Qualquer texto inserido em uma [`Entry`](xref:Xamarin.Forms.Entry) será armazenado na propriedade [`Text`](xref:Xamarin.Forms.Entry.Text).

1. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com um botão Reproduzir) para iniciar o aplicativo dentro do simulador remoto de iOS ou do Android Emulator escolhido:

    [![Captura de tela da Entrada contendo texto, em iOS e Android](../images/text-changes.png "Entrada com texto")](../images/text-changes-large.png#lightbox "Entrada com texto")

    Defina pontos de interrupção nos dois manipuladores de eventos e insira o texto na [`Entry`](xref:Xamarin.Forms.Entry), então observe os eventos [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) e [`Completed`](xref:Xamarin.Forms.Entry.Completed) serem disparados.

    Para obter mais informações sobre eventos [`Entry`](xref:Xamarin.Forms.Entry), confira [Eventos e interatividade](~/xamarin-forms/user-interface/text/entry.md#events-and-interactivity) no guia de [Entrada do Xamarin.Forms](~/xamarin-forms/user-interface/text/entry.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Em **MainPage.xaml**, modifique a declaração [`Entry`](xref:Xamarin.Forms.Entry) de modo que ela defina um manipulador para os eventos [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) e [`Completed`](xref:Xamarin.Forms.Entry.Completed):

    ```xaml
    <Entry Placeholder="Enter text"
           TextChanged="OnEntryTextChanged"
           Completed="OnEntryCompleted" />
    ```

    Esse código define o evento [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) para um manipulador de eventos chamado `OnEntryTextChanged` e o evento [`Completed`](xref:Xamarin.Forms.Entry.Completed) para um manipulador de eventos chamado `OnEntryCompleted`. Ambos os manipuladores de eventos serão criados na próxima etapa.

1. No **Painel de Soluções**, no projeto **LabelTutorial**, expanda **MainPage.xaml** e clique duas vezes em **MainPage.xaml.cs** para abri-lo. Em seguida, em **MainPage.xaml.cs**, adicione os manipuladores de eventos `OnEntryTextChanged` e `OnEntryCompleted` à classe:

    ```csharp
    void OnEntryTextChanged(object sender, TextChangedEventArgs e)
    {
        string oldText = e.OldTextValue;
        string newText = e.NewTextValue;
    }

    void OnEntryCompleted(object sender, EventArgs e)
    {
        string text = ((Entry)sender).Text;
    }
    ```

    Quando o texto na [`Entry`](xref:Xamarin.Forms.Entry) mudar, o método `OnEntryTextChanged` será executado. O argumento `sender` é o objeto `Entry` responsável por acionar o evento `TextChanged` e pode ser usado para acessar o objeto `Entry`. O argumento [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) fornece os valores novos e antigos do texto de antes e depois da alteração do texto.

    Quando você finalizar o texto na [`Entry`](xref:Xamarin.Forms.Entry) com a tecla de retorno, o método `OnEntryCompleted` será executado. O argumento `sender` é o objeto `Entry` responsável por acionar o evento `TextChanged` e pode ser usado para acessar o objeto `Entry`.

    > [!IMPORTANT]
    > Qualquer texto inserido em uma [`Entry`](xref:Xamarin.Forms.Entry) será armazenado na propriedade [`Text`](xref:Xamarin.Forms.Entry.Text).

1. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo dentro do simulador de iOS ou do Android Emulator escolhido:

    [![Captura de tela da Entrada contendo texto, em iOS e Android](../images/text-changes.png "Entrada com texto")](../images/text-changes-large.png#lightbox "Entrada com texto")

    Defina pontos de interrupção nos dois manipuladores de eventos e insira o texto na [`Entry`](xref:Xamarin.Forms.Entry), então observe os eventos [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) e [`Completed`](xref:Xamarin.Forms.Entry.Completed) serem disparados.

    Para obter mais informações sobre eventos [`Entry`](xref:Xamarin.Forms.Entry), confira [Eventos e interatividade](~/xamarin-forms/user-interface/text/entry.md#events-and-interactivity) no guia de [Entrada do Xamarin.Forms](~/xamarin-forms/user-interface/text/entry.md).
