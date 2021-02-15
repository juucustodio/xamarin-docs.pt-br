---
ms.openlocfilehash: 9ff398b53e88f38589733b1caac623e564e1ed39
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98634812"
---

O Xamarin.Forms tem um pop-up modal, conhecido como uma folha de ações, que pode ser usado para orientar os usuários durante uma tarefa. Neste exercício, você usará o método [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*), da classe [`Page`](xref:Xamarin.Forms.Page), para exibir uma folha de ações que orienta os usuários durante uma tarefa.

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. Em **MainPage.xaml**, adicione uma nova declaração [`Button`](xref:Xamarin.Forms.Button) que exibirá uma folha de ações:

    ```xaml
    <Button Text="Display action sheet"
            Clicked="OnDisplayActionSheetButtonClicked" />
    ```

     A propriedade [`Button.Text`](xref:Xamarin.Forms.Button.Text) especifica o texto exibido no `Button`. Além disso, o evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) é definido como um manipulador de eventos chamado `OnDisplayActionSheetButtonClicked` que será criado na próxima etapa.

1. No **Gerenciador de Soluções**, no projeto **PopupsTutorial**, expanda **MainPage.xaml** e clique duas vezes em **MainPage.xaml.cs** para abri-lo. Em seguida, em **MainPage.xaml.cs**, adicione o manipulador de eventos `OnDisplayActionSheetButtonClicked` à classe:

    ```csharp
    async void OnDisplayActionSheetButtonClicked(object sender, EventArgs e)
    {
        string action = await DisplayActionSheet("Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
        Console.WriteLine("Action: " + action);
    }
    ```

    Quando o [`Button`](xref:Xamarin.Forms.Button) é tocado, o método `OnDisplayActionSheetButtonClicked` é executado. Este método chama o método [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*), para apresentar ao usuário um conjunto de alternativas de como proceder em uma tarefa. Após o usuário selecionar uma das alternativas, a seleção será retornada como um `string`.

    > [!IMPORTANT]
    > O método [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) é assíncrono e sempre deve ser esperado com a palavra-chave `await`.

1. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo dentro de seu simulador remoto de iOS ou do Android Emulator escolhido. Em seguida, toque no [`Button`](xref:Xamarin.Forms.Button) que você adicionou ao [`ContentPage`](xref:Xamarin.Forms.ContentPage):

    [![Captura de tela de uma folha de ações, no iOS e no Android](../images/actionsheet.png "Folha de ações que orienta os usuários por meio de uma tarefa")](../images/actionsheet-large.png#lightbox "Folha de ações que orienta os usuários por meio de uma tarefa")

    Observe que, após selecionar uma alternativa na caixa de diálogo de folha de ações, a seleção aparece na janela de **Saída** do Visual Studio. Caso essa janela não esteja visível, será possível torná-la visível selecionando a opção de menu **Exibir > Saída**.

    Interrompa o aplicativo no Visual Studio.

    Confira mais informações sobre a exibição de folhas de ação em [Orientar usuários durante tarefas](~/xamarin-forms/user-interface/pop-ups.md#guide-users-through-tasks) no guia [Exibir pop-ups](~/xamarin-forms/user-interface/pop-ups.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Em **MainPage.xaml**, adicione uma nova declaração [`Button`](xref:Xamarin.Forms.Button) que exibirá uma folha de ações:

    ```xaml
    <Button Text="Display action sheet"
            Clicked="OnDisplayActionSheetButtonClicked" />
    ```

    A propriedade [`Button.Text`](xref:Xamarin.Forms.Button.Text) especifica o texto exibido no `Button`. Além disso, o evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) é definido como um manipulador de eventos chamado `OnDisplayActionSheetButtonClicked` que será criado na próxima etapa.

1. No **Painel de Soluções**, no projeto **PopupsTutorial**, expanda **MainPage.xaml** e clique duas vezes em **MainPage.xaml.cs** para abri-lo. Em seguida, em **MainPage.xaml.cs**, adicione o manipulador de eventos `OnDisplayActionSheetButtonClicked` à classe:

    ```csharp
    async void OnDisplayActionSheetButtonClicked(object sender, EventArgs e)
    {
        string action = await DisplayActionSheet("Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
        Console.WriteLine("Action: " + action);
    }
    ```

    Quando o [`Button`](xref:Xamarin.Forms.Button) é tocado, o método `OnDisplayActionSheetButtonClicked` é executado. Este método chama o método [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*), para apresentar ao usuário um conjunto de alternativas de como proceder em uma tarefa. Após o usuário selecionar uma das alternativas, a seleção será retornada como um `string`.

    > [!IMPORTANT]
    > O método [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) é assíncrono e sempre deve ser esperado com a palavra-chave `await`.

1. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com um botão Reproduzir) para iniciar o aplicativo dentro do simulador de iOS ou do Android Emulator escolhido. Em seguida, toque no [`Button`](xref:Xamarin.Forms.Button) que você adicionou ao [`ContentPage`](xref:Xamarin.Forms.ContentPage):

    [![Captura de tela de uma folha de ações, no iOS e no Android](../images/actionsheet.png "Folha de ações que orienta os usuários por meio de uma tarefa")](../images/actionsheet-large.png#lightbox "Folha de ações que orienta os usuários por meio de uma tarefa")

    Observe que, após selecionar uma alternativa na caixa de diálogo de folha de ações, a seleção aparece na janela de **Saída do Aplicativo** do Visual Studio para Mac. Caso essa janela não esteja visível, será possível torná-la visível selecionando a opção de menu **Exibir > Outras Janelas > Saída do Aplicativo**.

    Interrompa o aplicativo no Visual Studio para Mac.

    Confira mais informações sobre a exibição de folhas de ação em [Orientar usuários durante tarefas](~/xamarin-forms/user-interface/pop-ups.md#guide-users-through-tasks) no guia [Exibir pop-ups](~/xamarin-forms/user-interface/pop-ups.md).
