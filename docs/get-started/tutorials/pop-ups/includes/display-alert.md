O Xamarin.Forms tem um pop-up modal, conhecido como um alerta, para alertar o usuário ou para fazer perguntas simples sobre eles. Neste exercício, você aprenderá a usar o método [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*), da classe [`Page`](xref:Xamarin.Forms.Page), para exibir um alerta ao usuário e fazer uma pergunta simples.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Inicie o Visual Studio e crie um novo aplicativo Xamarin.Forms em branco chamado **PopupsTutorial**. Verifique se o aplicativo usa o .NET Standard como o mecanismo de código compartilhado.

    > [!IMPORTANT]
    > Os snippets de C# e XAML neste tutorial exigem que a solução seja denominada **PopupsTutorial**. Usar um nome diferente causará erros de build ao copiar o código deste tutorial para a solução.

    Para obter mais informações sobre a biblioteca .NET Standard criada, confira [Anatomia de um aplicativo Xamarin.Forms](~/get-started/first-app/index.md) em [Aprofundamento do Início Rápido do Xamarin.Forms](~/get-started/first-app/index.md).

1. No **Gerenciador de Soluções**, no projeto **PopupsTutorial**, clique duas vezes em **MainPage.xaml** para abri-lo. Em seguida, em **MainPage.xaml**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="PopupsTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Button Text="Display alert"
                    Clicked="OnDisplayAlertButtonClicked" />
            <Button Text="Display alert question"
                    Clicked="OnDisplayAlertQuestionButtonClicked" />
        </StackLayout>
    </ContentPage>
    ```

    Esse código define declarativamente a interface do usuário para a página, que consiste em dois objetos [`Button`](xref:Xamarin.Forms.Button) em um [`StackLayout`](xref:Xamarin.Forms.StackLayout). As propriedades [`Button.Text`](xref:Xamarin.Forms.Button.Text) especificam o texto que aparece em cada `Button` e os eventos [`Clicked`](xref:Xamarin.Forms.Button.Clicked) são definidos como manipuladores de eventos que serão criados na próxima etapa.

1. No **Gerenciador de Soluções**, no projeto **PopupsTutorial**, expanda **MainPage.xaml** e clique duas vezes em **MainPage.xaml.cs** para abri-lo. Em seguida, em **MainPage.xaml.cs**, adicione os manipuladores de eventos `OnDisplayAlertButtonClicked` e `OnDisplayAlertQuestionButtonClicked` à classe:

    ```csharp
    async void OnDisplayAlertButtonClicked(object sender, EventArgs e)
    {
        await DisplayAlert("Alert", "This is an alert.", "OK");
    }

    async void OnDisplayAlertQuestionButtonClicked(object sender, EventArgs e)
    {
        bool response = await DisplayAlert("Save?", "Would you like to save your data?", "Yes", "No");
        Console.WriteLine("Save data: " + response);
    }
    ```

    Quando um [`Button`](xref:Xamarin.Forms.Button) é tocado, o método de manipulador do respectivo evento é executado. O método `OnDisplayAlertButtonClicked` chama o método [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) para exibir um alerta modal com um botão Cancelar único. Depois que o alerta é descartado, o usuário pode continuar interagindo com o aplicativo.

    O método `OnDisplayAlertQuestionButtonClicked` chama uma sobrecarga do método [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) para exibir um alerta modal com um botão aceitar e um botão cancelar. Após o usuário selecionar um dos botões, a seleção será retornada como um `boolean`.

    > [!IMPORTANT]
    > O método [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) é assíncrono e sempre deve ser esperado com a palavra-chave `await`.

1. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo dentro de seu simulador remoto de iOS ou do Android Emulator escolhido. Em seguida, toque no primeiro [`Button`](xref:Xamarin.Forms.Button):

    [![Captura de tela de um alerta em iOS e Android](../images/alert.png "Alerta")](../images/alert-large.png#lightbox "Alerta")

    Depois de ignorar o alerta, toque no segundo [`Button`](xref:Xamarin.Forms.Button):

    [![Captura de tela de um alerta que gera uma pergunta, em iOS e Android](../images/alert-question.png "Alerta que faz uma pergunta")](../images/alert-question-large.png#lightbox "Alerta que faz uma pergunta")

    Observe que, depois de selecionar uma resposta à pergunta, a resposta é enviada para a janela de **Saída** do Visual Studio.

    Para obter mais informações sobre como exibir alertas, confira [Exibindo um alerta](~/xamarin-forms/app-fundamentals/navigation/pop-ups.md#displaying-an-alert) no guia [Exibindo pop-ups](~/xamarin-forms/app-fundamentals/navigation/pop-ups.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Inicie o Visual Studio para Mac e crie um novo aplicativo Xamarin.Forms em branco chamado **PopupsTutorial**. Verifique se o aplicativo usa o .NET Standard como o mecanismo de código compartilhado.

    > [!IMPORTANT]
    > Os snippets de C# e XAML neste tutorial exigem que a solução seja denominada **PopupsTutorial**. Usar um nome diferente causará erros de build ao copiar o código deste tutorial para a solução.

    Para obter mais informações sobre a biblioteca .NET Standard criada, confira [Anatomia de um aplicativo Xamarin.Forms](~/get-started/first-app/index.md) em [Aprofundamento do Início Rápido do Xamarin.Forms](~/get-started/first-app/index.md).

1. No **Painel de Soluções**, no projeto **PopupsTutorial**, clique duas vezes em **MainPage.xaml** para abri-lo. Em seguida, em **MainPage.xaml**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="PopupsTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Button Text="Display alert"
                    Clicked="OnDisplayAlertButtonClicked" />
            <Button Text="Display alert question"
                    Clicked="OnDisplayAlertQuestionButtonClicked" />
        </StackLayout>
    </ContentPage>
    ```

    Esse código define declarativamente a interface do usuário para a página, que consiste em dois objetos [`Button`](xref:Xamarin.Forms.Button) em um [`StackLayout`](xref:Xamarin.Forms.StackLayout). As propriedades [`Button.Text`](xref:Xamarin.Forms.Button.Text) especificam o texto que aparece em cada `Button` e os eventos [`Clicked`](xref:Xamarin.Forms.Button.Clicked) são definidos como manipuladores de eventos que serão criados na próxima etapa.

1. No **Painel de Soluções**, no projeto **PopupsTutorial**, expanda **MainPage.xaml** e clique duas vezes em **MainPage.xaml.cs** para abri-lo. Em seguida, em **MainPage.xaml.cs**, adicione os manipuladores de eventos `OnDisplayAlertButtonClicked` e `OnDisplayAlertQuestionButtonClicked` à classe:

    ```csharp
    async void OnDisplayAlertButtonClicked(object sender, EventArgs e)
    {
        await DisplayAlert("Alert", "This is an alert.", "OK");
    }

    async void OnDisplayAlertQuestionButtonClicked(object sender, EventArgs e)
    {
        bool response = await DisplayAlert("Save?", "Would you like to save your data?", "Yes", "No");
        Console.WriteLine("Save data: " + response);
    }
    ```

    Quando um [`Button`](xref:Xamarin.Forms.Button) é tocado, o método de manipulador do respectivo evento é executado. O método `OnDisplayAlertButtonClicked` chama o método [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) para exibir um alerta modal com um botão Cancelar único. Depois que o alerta é descartado, o usuário pode continuar interagindo com o aplicativo.

    O método `OnDisplayAlertQuestionButtonClicked` chama uma sobrecarga do método [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) para exibir um alerta modal com um botão aceitar e um botão cancelar. Após o usuário selecionar um dos botões, a seleção será retornada como um `boolean`.

    > [!IMPORTANT]
    > O método [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) é assíncrono e sempre deve ser esperado com a palavra-chave `await`.

1. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com um botão Reproduzir) para iniciar o aplicativo dentro do simulador de iOS ou do Android Emulator escolhido. Em seguida, toque no primeiro [`Button`](xref:Xamarin.Forms.Button):

    [![Captura de tela de um alerta em iOS e Android](../images/alert.png "Alerta")](../images/alert-large.png#lightbox "Alerta")

    Depois de ignorar o alerta, toque no segundo [`Button`](xref:Xamarin.Forms.Button):

    [![Captura de tela de um alerta que gera uma pergunta, em iOS e Android](../images/alert-question.png "Alerta que faz uma pergunta")](../images/alert-question-large.png#lightbox "Alerta que faz uma pergunta")

    Observe que, depois de selecionar uma resposta à pergunta, a resposta é enviada para a janela de **Saída do Aplicativo** do Visual Studio para Mac.

    Para obter mais informações sobre como exibir alertas, confira [Exibindo um alerta](~/xamarin-forms/app-fundamentals/navigation/pop-ups.md#displaying-an-alert) no guia [Exibindo pop-ups](~/xamarin-forms/app-fundamentals/navigation/pop-ups.md).
