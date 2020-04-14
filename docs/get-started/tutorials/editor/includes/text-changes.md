---
ms.openlocfilehash: 653d3677f96d7da78af61531c535b1b7db684e7e
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "77135059"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. Em **MainPage.xaml**, modifique a declaração [`Editor`](xref:Xamarin.Forms.Editor) de modo que ela defina um manipulador para os eventos [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) e [`Completed`](xref:Xamarin.Forms.Editor.Completed):

    ```xaml
    <Editor Placeholder="Enter multi-line text here"
            HeightRequest="200"
            TextChanged="OnEditorTextChanged"
            Completed="OnEditorCompleted" />
    ```

    Esse código define o evento [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) para um manipulador de eventos chamado `OnEditorTextChanged` e o evento [`Completed`](xref:Xamarin.Forms.Editor.Completed) para um manipulador de eventos chamado `OnEditorCompleted`. Ambos os manipuladores de eventos serão criados na próxima etapa.

1. No **Gerenciador de Soluções**, no projeto **EditorTutorial**, expanda **MainPage.xaml** e clique duas vezes em **MainPage.xaml.cs** para abri-lo. Em seguida, em **MainPage.xaml.cs**, adicione os manipuladores de eventos `OnEditorTextChanged` e `OnEditorCompleted` à classe:

    ```csharp
    void OnEditorTextChanged(object sender, TextChangedEventArgs e)
    {
        string oldText = e.OldTextValue;
        string newText = e.NewTextValue;
    }

    void OnEditorCompleted(object sender, EventArgs e)
    {
        string text = ((Editor)sender).Text;
    }
    ```

    Quando o texto na [`Editor`](xref:Xamarin.Forms.Editor) mudar, o método `OnEditorTextChanged` será executado. O argumento `sender` é o objeto `Editor` responsável por acionar o evento `TextChanged` e pode ser usado para acessar o objeto `Editor`. O argumento [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) fornece os valores novos e antigos do texto de antes e depois da alteração do texto.

    Quando a edição é concluída, o método `OnEditorCompleted` é executado. Isso é feito desfocando [`Editor`](xref:Xamarin.Forms.Editor) ou, além disso, pressionando o botão "Concluído" no iOS. O argumento `sender` é o objeto `Editor` responsável por acionar o evento `TextChanged` e pode ser usado para acessar o objeto `Editor`.

    > [!IMPORTANT]
    > Qualquer texto inserido em uma [`Editor`](xref:Xamarin.Forms.Editor) será armazenado na propriedade [`Text`](xref:Xamarin.Forms.InputView.Text).

1. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com um botão Reproduzir) para iniciar o aplicativo dentro do simulador remoto de iOS ou do Android Emulator escolhido:

    [![Captura de tela de um editor que contém texto, no iOS e no Android](../images/text-changes.png "Editor com texto")](../images/text-changes-large.png#lightbox "Editor com texto")

    Defina pontos de interrupção nos dois manipuladores de eventos, insira texto no [`Editor`](xref:Xamarin.Forms.Editor) e observe o acionamento de evento [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged). Desfoque o `Editor` para observar o evento [`Completed`](xref:Xamarin.Forms.Entry.Completed) sendo acionado.

    Para obter mais informações sobre eventos [`Editor`](xref:Xamarin.Forms.Editor), confira [Interatividade](~/xamarin-forms/user-interface/text/editor.md#interactivity) no guia [Editor do Xamarin.Forms](~/xamarin-forms/user-interface/text/editor.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Em **MainPage.xaml**, modifique a declaração [`Editor`](xref:Xamarin.Forms.Editor) de modo que ela defina um manipulador para os eventos [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) e [`Completed`](xref:Xamarin.Forms.Editor.Completed):

    ```xaml
    <Editor Placeholder="Enter multi-line text here"
            HeightRequest="200"
            TextChanged="OnEditorTextChanged"
            Completed="OnEditorCompleted" />
    ```

    Esse código define o evento [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) para um manipulador de eventos chamado `OnEditorTextChanged` e o evento [`Completed`](xref:Xamarin.Forms.Editor.Completed) para um manipulador de eventos chamado `OnEditorCompleted`. Ambos os manipuladores de eventos serão criados na próxima etapa.

1. No **Painel de Soluções**, no projeto **EditorTutorial**, expanda **MainPage.xaml** e clique duas vezes em **MainPage.xaml.cs** para abri-lo. Em seguida, em **MainPage.xaml.cs**, adicione os manipuladores de eventos `OnEditorTextChanged` e `OnEditorCompleted` à classe:

    ```csharp
    void OnEditorTextChanged(object sender, TextChangedEventArgs e)
    {
        string oldText = e.OldTextValue;
        string newText = e.NewTextValue;
    }

    void OnEditorCompleted(object sender, EventArgs e)
    {
        string text = ((Editor)sender).Text;
    }
    ```

    Quando o texto na [`Editor`](xref:Xamarin.Forms.Editor) mudar, o método `OnEditorTextChanged` será executado. O argumento `sender` é o objeto `Editor` responsável por acionar o evento `TextChanged` e pode ser usado para acessar o objeto `Editor`. O argumento [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) fornece os valores novos e antigos do texto de antes e depois da alteração do texto.

    Quando a edição é concluída, o método `OnEditorCompleted` é executado. Isso é feito desfocando [`Editor`](xref:Xamarin.Forms.Editor) ou, além disso, pressionando o botão "Concluído" no iOS. O argumento `sender` é o objeto `Editor` responsável por acionar o evento `TextChanged` e pode ser usado para acessar o objeto `Editor`.

    > [!IMPORTANT]
    > Qualquer texto inserido em uma [`Editor`](xref:Xamarin.Forms.Editor) será armazenado na propriedade [`Text`](xref:Xamarin.Forms.InputView.Text).

1. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo dentro do simulador de iOS ou do Android Emulator escolhido:

    [![Captura de tela de um editor que contém texto, no iOS e no Android](../images/text-changes.png "Editor com texto")](../images/text-changes-large.png#lightbox "Editor com texto")

    Defina pontos de interrupção nos dois manipuladores de eventos, insira texto no [`Editor`](xref:Xamarin.Forms.Editor) e observe o acionamento de evento [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged). Desfoque o `Editor` para observar o evento [`Completed`](xref:Xamarin.Forms.Entry.Completed) sendo acionado.

    Para obter mais informações sobre eventos [`Editor`](xref:Xamarin.Forms.Editor), confira [Interatividade](~/xamarin-forms/user-interface/text/editor.md#interactivity) no guia [Editor do Xamarin.Forms](~/xamarin-forms/user-interface/text/editor.md).
