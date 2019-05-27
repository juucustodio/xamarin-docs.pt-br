---
ms.openlocfilehash: 87eb021e6cc571a9a5522697cde2aa11ee991308
ms.sourcegitcommit: 6ad272c2c7b0c3c30e375ad17ce6296ac1ce72b2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66193821"
---

O Xamarin.Forms tem um pop-up modal, conhecido como uma folha de ações, que pode ser usado para orientar os usuários durante uma tarefa. Neste exercício, você usará o método [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*), da classe [`Page`](xref:Xamarin.Forms.Page), para exibir uma folha de ações que orienta os usuários durante uma tarefa.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

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

    [![Captura de tela de uma folha de ações, no iOS e Android](../images/actionsheet.png "Folha de ações que orienta os usuários durante uma tarefa")](../images/actionsheet-large.png#lightbox "Folha de ações que orienta os usuários durante uma tarefa")

    Observe que, após selecionar uma alternativa na caixa de diálogo de folha de ações, a seleção aparece na janela de **Saída** do Visual Studio.

    Confira mais informações sobre a exibição de folhas de ação em [Orientar usuários durante tarefas](~/xamarin-forms/user-interface/pop-ups.md#guide-users-through-tasks) no guia [Exibir pop-ups](~/xamarin-forms/user-interface/pop-ups.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

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

    [![Captura de tela de uma folha de ações, no iOS e Android](../images/actionsheet.png "Folha de ações que orienta os usuários durante uma tarefa")](../images/actionsheet-large.png#lightbox "Folha de ações que orienta os usuários durante uma tarefa")

    Observe que, após selecionar uma alternativa na caixa de diálogo de folha de ações, a seleção aparece na janela de **Saída do Aplicativo** do Visual Studio para Mac.

    Confira mais informações sobre a exibição de folhas de ação em [Orientar usuários durante tarefas](~/xamarin-forms/user-interface/pop-ups.md#guide-users-through-tasks) no guia [Exibir pop-ups](~/xamarin-forms/user-interface/pop-ups.md).
