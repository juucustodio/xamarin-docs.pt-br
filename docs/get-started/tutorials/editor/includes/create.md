---
ms.openlocfilehash: ce3e0f18d299d2bdf8d9bd81c467d45924d0d2bc
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61373335"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Inicie o Visual Studio e crie um novo aplicativo Xamarin.Forms em branco chamado **EditorTutorial**. Verifique se o aplicativo usa o .NET Standard como o mecanismo de código compartilhado.

    > [!IMPORTANT]
    > Os snippets de C# e XAML neste tutorial exigem que a solução seja denominada **EditorTutorial**. Usar um nome diferente causará erros de build ao copiar o código deste tutorial para a solução.

    Para obter mais informações sobre a biblioteca .NET Standard criada, confira [Anatomia de um aplicativo Xamarin.Forms](~/get-started/first-app/index.md) em [Aprofundamento do Início Rápido do Xamarin.Forms](~/get-started/first-app/index.md).

1. No **Gerenciador de Soluções**, no projeto **EditorTutorial**, clique duas vezes em **MainPage.xaml** para abri-lo. Em seguida, em **MainPage.xaml**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="EditorTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Editor Placeholder="Enter multi-line text here"
                    HeightRequest="200" />
        </StackLayout>
    </ContentPage>
    ```

    Esse código define declarativamente a interface do usuário para a página, que é composta por um [`Editor`](xref:Xamarin.Forms.Editor) em um [`StackLayout`](xref:Xamarin.Forms.StackLayout). A propriedade [`Editor.Placeholder`](xref:Xamarin.Forms.Editor.Placeholder) especifica o texto de espaço reservado mostrado quando o `Editor` é exibido pela primeira vez. Além disso, a propriedade [`HeightRequest`](xref:Xamarin.Forms.VisualElement) especifica a altura de `Editor` em unidades independentes do dispositivo.

1. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com um botão Reproduzir) para iniciar o aplicativo dentro do simulador remoto de iOS ou do Android Emulator escolhido:

    [![Captura de tela de um Editor no iOS e Android](../images/create-editor.png "Editor contendo texto de espaço reservado")](../images/create-editor-large.png#lightbox "Editor contendo texto de espaço reservado")

    > [!NOTE]
    > Enquanto o Android indica a altura do [`Editor`](xref:Xamarin.Forms.Editor), o iOS não faz isso.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Inicie o Visual Studio para Mac e crie um novo aplicativo Xamarin.Forms em branco chamado **EditorTutorial**. Verifique se o aplicativo usa o .NET Standard como o mecanismo de código compartilhado.

    > [!IMPORTANT]
    > Os snippets de C# e XAML neste tutorial exigem que a solução seja denominada **EditorTutorial**. Usar um nome diferente causará erros de build ao copiar o código deste tutorial para a solução.

    Para obter mais informações sobre a biblioteca .NET Standard criada, confira [Anatomia de um aplicativo Xamarin.Forms](~/get-started/first-app/index.md) em [Aprofundamento do Início Rápido do Xamarin.Forms](~/get-started/first-app/index.md).

1. No **Painel de Soluções**, no projeto **EditorTutorial**, clique duas vezes em **MainPage.xaml** para abri-lo. Em seguida, em **MainPage.xaml**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="EditorTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Editor Placeholder="Enter multi-line text here"
                    HeightRequest="200" />
        </StackLayout>
    </ContentPage>
    ```

    Esse código define declarativamente a interface do usuário para a página, que é composta por um [`Editor`](xref:Xamarin.Forms.Editor) em um [`StackLayout`](xref:Xamarin.Forms.StackLayout). A propriedade [`Editor.Placeholder`](xref:Xamarin.Forms.Editor.Placeholder) especifica o texto de espaço reservado mostrado quando o `Editor` é exibido pela primeira vez. Além disso, a propriedade [`HeightRequest`](xref:Xamarin.Forms.VisualElement) especifica a altura de `Editor` em unidades independentes do dispositivo.

1. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo dentro do simulador de iOS ou do Android Emulator escolhido:

    [![Captura de tela de um Editor no iOS e Android](../images/create-editor.png "Editor contendo texto de espaço reservado")](../images/create-editor-large.png#lightbox "Editor contendo texto de espaço reservado")

    > [!NOTE]
    > Enquanto o Android indica a altura do [`Editor`](xref:Xamarin.Forms.Editor), o iOS não faz isso.
