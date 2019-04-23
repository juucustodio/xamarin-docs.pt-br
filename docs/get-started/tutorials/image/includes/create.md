---
ms.openlocfilehash: 550828327eb6b72c1c9712e54e6e36c9e30bd1f0
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61384365"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Inicie o Visual Studio e crie um novo aplicativo Xamarin.Forms em branco chamado **ImageTutorial**. Verifique se o aplicativo usa o .NET Standard como o mecanismo de código compartilhado.

    > [!IMPORTANT]
    > Os snippets de C# e XAML neste tutorial exigem que a solução seja denominada **ImageTutorial**. Usar um nome diferente causará erros de build ao copiar o código deste tutorial para a solução.

    Para obter mais informações sobre a biblioteca .NET Standard criada, confira [Anatomia de um aplicativo Xamarin.Forms](~/get-started/first-app/index.md) em [Aprofundamento do Início Rápido do Xamarin.Forms](~/get-started/first-app/index.md).

1. No **Gerenciador de Soluções**, no projeto **ImageTutorial**, clique duas vezes em **MainPage.xaml** para abri-lo. Em seguida, em **MainPage.xaml**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="ImageTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Image Source="http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
                   HeightRequest="300" />
        </StackLayout>
    </ContentPage>
    ```

    Esse código define declarativamente a interface do usuário para a página, que é composta por um [`Image`](xref:Xamarin.Forms.Image) em um [`StackLayout`](xref:Xamarin.Forms.StackLayout). A propriedade [`Image.Source`](xref:Xamarin.Forms.Image.Source) especifica a imagem a ser exibida por meio de um URI. A propriedade [`Image.Source`](xref:Xamarin.Forms.Image.Source) é do tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource), o que permite que as imagens sejam originadas de arquivos, URIs ou recursos. Para obter mais informações, confira [Exibindo imagens](~/xamarin-forms/user-interface/images.md#displaying-images) no guia [Imagens no Xamarin.Forms](~/xamarin-forms/user-interface/images.md).

    A propriedade [`HeightRequest`](xref:Xamarin.Forms.VisualElement) especifica a altura de `Image` em unidades independentes do dispositivo.

    > [!NOTE]
    > Não é necessário definir a propriedade [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) neste exemplo. Isso acontece porque, por padrão, [`Image`](xref:Xamarin.Forms.Image) mantém a taxa de proporção da imagem.

1. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com um botão Reproduzir) para iniciar o aplicativo dentro do simulador remoto de iOS ou do Android Emulator escolhido:

    [![Captura de tela de uma imagem no iOS e Android](../images/create-image.png "Exibição de imagem exibindo uma imagem")](../images/create-image-large.png#lightbox "Exibição de imagem exibindo uma imagem")

    > [!NOTE]
    > A exibição [`Image`](xref:Xamarin.Forms.Image) armazena automaticamente as imagens baixadas por 24 horas. Para obter mais informações, confira [Cache de imagens baixadas](~/xamarin-forms/user-interface/images.md#downloaded-image-caching) no guia [Imagens no Xamarin.Forms](~/xamarin-forms/user-interface/images.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Inicie o Visual Studio para Mac e crie um novo aplicativo Xamarin.Forms em branco chamado **ImageTutorial**. Verifique se o aplicativo usa o .NET Standard como o mecanismo de código compartilhado.

    > [!IMPORTANT]
    > Os snippets de C# e XAML neste tutorial exigem que a solução seja denominada **ImageTutorial**. Usar um nome diferente causará erros de build ao copiar o código deste tutorial para a solução.

    Para obter mais informações sobre a biblioteca .NET Standard criada, confira [Anatomia de um aplicativo Xamarin.Forms](~/get-started/first-app/index.md) em [Aprofundamento do Início Rápido do Xamarin.Forms](~/get-started/first-app/index.md).

1. No **Painel de Soluções**, no projeto **ImageTutorial**, clique duas vezes em **MainPage.xaml** para abri-lo. Em seguida, em **MainPage.xaml**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="ImageTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Image Source="http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
                   HeightRequest="300" />
        </StackLayout>
    </ContentPage>
    ```

    Esse código define declarativamente a interface do usuário para a página, que é composta por um [`Image`](xref:Xamarin.Forms.Image) em um [`StackLayout`](xref:Xamarin.Forms.StackLayout). A propriedade [`Image.Source`](xref:Xamarin.Forms.Image.Source) especifica a imagem a ser exibida por meio de um URI. A propriedade [`Image.Source`](xref:Xamarin.Forms.Image.Source) é do tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource), o que permite que as imagens sejam originadas de arquivos, URIs ou recursos. Para obter mais informações, confira [Exibindo imagens](~/xamarin-forms/user-interface/images.md#displaying-images) no guia [Imagens no Xamarin.Forms](~/xamarin-forms/user-interface/images.md).

    A propriedade [`HeightRequest`](xref:Xamarin.Forms.VisualElement) especifica a altura de `Image` em unidades independentes do dispositivo.

    > [!NOTE]
    > Não é necessário definir a propriedade [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) neste exemplo. Isso acontece porque, por padrão, [`Image`](xref:Xamarin.Forms.Image) mantém a taxa de proporção da imagem.

1. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo dentro do simulador de iOS ou do Android Emulator escolhido:

    [![Captura de tela de uma imagem no iOS e Android](../images/create-image.png "Exibição de imagem exibindo uma imagem")](../images/create-image-large.png#lightbox "Exibição de imagem exibindo uma imagem")

    > [!NOTE]
    > A exibição [`Image`](xref:Xamarin.Forms.Image) armazena automaticamente as imagens baixadas por 24 horas. Para obter mais informações, confira [Cache de imagens baixadas](~/xamarin-forms/user-interface/images.md#downloaded-image-caching) no guia [Imagens no Xamarin.Forms](~/xamarin-forms/user-interface/images.md).
