---
ms.openlocfilehash: 2446b4410b7ded6aa070a19ae0ac742bb519b331
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98689736"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

Para concluir este tutorial, você precisará ter o Visual Studio 2019 (versão mais recente) com a carga de trabalho **Desenvolvimento mobile com .NET** instalada. Além disso, você precisará de um Mac emparelhado para compilar o aplicativo do tutorial no iOS. Para obter informações sobre como instalar a plataforma Xamarin, confira [Instalando o Xamarin](~/get-started/installation/index.md). Para obter informações sobre como conectar o Visual Studio 2019 a um host de build Mac, confira [Emparelhar com o Mac para desenvolvimento no Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Inicie o Visual Studio e crie um novo aplicativo Xamarin.Forms em branco chamado **LabelTutorial**.

    > [!IMPORTANT]
    > Os snippets de C# e XAML neste tutorial exigem que a solução seja denominada **LabelTutorial**. Usar um nome diferente causará erros de build ao copiar o código deste tutorial para a solução.

    Para obter mais informações sobre a biblioteca .NET Standard criada, confira [Anatomia de um aplicativo Xamarin.Forms](~/get-started/first-app/index.md) em [Aprofundamento do Início Rápido do Xamarin.Forms](~/get-started/first-app/index.md).

1. No **Gerenciador de Soluções**, no projeto **LabelTutorial**, clique duas vezes em **MainPage.xaml** para abri-lo. Em seguida, em **MainPage.xaml**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="LabelTutorial.MainPage">
       <StackLayout Margin="20,35,20,20">
            <Label Text="Welcome to Xamarin.Forms!"
                   HorizontalOptions="Center" />
       </StackLayout>
    </ContentPage>
    ```

    Esse código define declarativamente a interface do usuário para a página, que é composta por um [`Label`](xref:Xamarin.Forms.Label) em um [`StackLayout`](xref:Xamarin.Forms.StackLayout). A propriedade [`Label.Text`](xref:Xamarin.Forms.Label.Text) especifica o texto que é exibido e a propriedade [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) especifica que o `Label` será horizontalmente centralizado.

1. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com um botão Reproduzir) para iniciar o aplicativo dentro do simulador remoto de iOS ou do Android Emulator escolhido:

    [![Captura de tela de um rótulo centralizado no iOS e no Android](../images/create-label.png "Um rótulo centralizado")](../images/create-label-large.png#lightbox "Um rótulo centralizado")

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

Para concluir este tutorial, você precisará ter o Visual Studio para Mac (versão mais recente), com suporte para plataforma iOS e Android, instalado. Além disso, você também precisará do Xcode (versão mais recente). Para obter mais informações sobre como instalar a plataforma Xamarin, confira [Instalando o Xamarin](~/get-started/installation/index.md).

1. Inicie o Visual Studio para Mac e crie um novo aplicativo Xamarin.Forms em branco chamado **LabelTutorial**.

    > [!IMPORTANT]
    > Os snippets de C# e XAML neste tutorial exigem que a solução seja denominada **LabelTutorial**. Usar um nome diferente causará erros de build ao copiar o código deste tutorial para a solução.

    Para obter mais informações sobre a biblioteca .NET Standard criada, confira [Anatomia de um aplicativo Xamarin.Forms](~/get-started/first-app/index.md) em [Aprofundamento do Início Rápido do Xamarin.Forms](~/get-started/first-app/index.md).

1. No **Painel de Soluções**, no projeto **LabelTutorial**, clique duas vezes em **MainPage.xaml** para abri-lo. Em seguida, em **MainPage.xaml**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="LabelTutorial.MainPage">
       <StackLayout Margin="20,35,20,20">
            <Label Text="Welcome to Xamarin.Forms!"
                   HorizontalOptions="Center" />
       </StackLayout>
    </ContentPage>
    ```

    Esse código define declarativamente a interface do usuário para a página, que é composta por um [`Label`](xref:Xamarin.Forms.Label) em um [`StackLayout`](xref:Xamarin.Forms.StackLayout). A propriedade [`Label.Text`](xref:Xamarin.Forms.Label.Text) especifica o texto que é exibido e a propriedade [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) especifica que o texto seja centralizado horizontalmente.

1. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo dentro do simulador de iOS ou do Android Emulator escolhido:

    [![Captura de tela de um rótulo centralizado no iOS e no Android](../images/create-label.png "Um rótulo centralizado")](../images/create-label-large.png#lightbox "Um rótulo centralizado")
