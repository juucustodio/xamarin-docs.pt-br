---
ms.openlocfilehash: d1f7d209eaaca62a55b768646f51024609057a63
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61372893"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Em **MainPage.xaml**, modifique a declaração [`Button`](xref:Xamarin.Forms.Button) de modo que ela defina um manipulador para o evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked):

    ```xaml
    <Button Text="Click me"
            Clicked="OnButtonClicked" />
    ```

    Este código define o evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) como um manipulador de eventos chamado `OnButtonClicked` que será criado na próxima etapa.

1. No **Gerenciador de Soluções**, no projeto **ButtonTutorial**, expanda **MainPage.xaml** e clique duas vezes em **MainPage.xaml.cs** para abri-lo. Em seguida, em **MainPage.xaml.cs**, adicione o manipulador de eventos `OnButtonClicked` à classe:

    ```csharp
    void OnButtonClicked(object sender, EventArgs e)
    {
        (sender as Button).Text = "Click me again!";
    }
    ```

    Quando o [`Button`](xref:Xamarin.Forms.Button) é tocado, o método `OnButtonClicked` é executado. O argumento `sender` é o objeto `Button` responsável por acionar o evento `Clicked` e pode ser usado para acessar o objeto `Button`. Esse manipulador de eventos atualiza o texto exibido pelo `Button`.

    > [!NOTE]
    > Além de evento `Clicked`, `Button` também define eventos [`Pressed`](xref:Xamarin.Forms.Button.Pressed) e [`Released`](xref:Xamarin.Forms.Button.Released). Para obter mais informações, confira [Pressionando e soltando o botão](~/xamarin-forms/user-interface/button.md#pressing-and-releasing-the-button) no guia [Botão do Xamarin.Forms](~/xamarin-forms/user-interface/button.md).

1. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo dentro de seu simulador remoto de iOS ou do Android Emulator escolhido. Clique no [`Button`](xref:Xamarin.Forms.Button) e observe que o texto que ele exibe é alterado:

    [![Captura de tela do texto do Botão sendo alterado após receber um clique, no iOS e Android](../images/handle-button-click.png "Lidar com um clique no botão")](../images/handle-button-click-large.png#lightbox "Lidar com um clique no botão")

    Para obter mais informações sobre como lidar com cliques no botão, confira [Lidando com cliques no botão](~/xamarin-forms/user-interface/button.md#handling-button-clicks) no guia [Botão do Xamarin.Forms](~/xamarin-forms/user-interface/button.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Em **MainPage.xaml**, modifique a declaração [`Button`](xref:Xamarin.Forms.Button) de modo que ela defina um manipulador para o evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked):

    ```xaml
    <Button Text="Click me"
            Clicked="OnButtonClicked" />
    ```

    Este código define o evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) como um manipulador de eventos chamado `OnButtonClicked` que será criado na próxima etapa.

1. No **Painel de Soluções**, no projeto **ButtonTutorial**, expanda **MainPage.xaml** e clique duas vezes em **MainPage.xaml.cs** para abri-lo. Em seguida, em **MainPage.xaml.cs**, adicione o manipulador de eventos `OnButtonClicked` à classe:

    ```csharp
    void OnButtonClicked(object sender, EventArgs e)
    {
        (sender as Button).Text = "Click me again!";
    }
    ```

    Quando o [`Button`](xref:Xamarin.Forms.Button) é tocado, o método `OnButtonClicked` é executado. O argumento `sender` é o objeto `Button` responsável por acionar o evento `Clicked` e pode ser usado para acessar o objeto `Button`. Esse manipulador de eventos atualiza o texto exibido pelo `Button`.

    > [!NOTE]
    > Além de evento `Clicked`, `Button` também define eventos [`Pressed`](xref:Xamarin.Forms.Button.Pressed) e [`Released`](xref:Xamarin.Forms.Button.Released). Para obter mais informações, confira [Pressionando e soltando o botão](~/xamarin-forms/user-interface/button.md#pressing-and-releasing-the-button) no guia [Botão do Xamarin.Forms](~/xamarin-forms/user-interface/button.md).

1. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com um botão Reproduzir) para iniciar o aplicativo dentro do simulador de iOS ou do Android Emulator escolhido. Clique no [`Button`](xref:Xamarin.Forms.Button) e observe que o texto que ele exibe é alterado:

    [![Captura de tela do texto do Botão sendo alterado após receber um clique, no iOS e Android](../images/handle-button-click.png "Lidar com um clique no botão")](../images/handle-button-click-large.png#lightbox "Lidar com um clique no botão")

    Para obter mais informações sobre como lidar com cliques no botão, confira [Lidando com cliques no botão](~/xamarin-forms/user-interface/button.md#handling-button-clicks) no guia [Botão do Xamarin.Forms](~/xamarin-forms/user-interface/button.md).
