---
ms.openlocfilehash: d8f9445a0fc45c2700e8d9a901cfce9bd6307d2b
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2020
ms.locfileid: "75490649"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Em **MainPage.xaml**, modifique a declaração [`Image`](xref:Xamarin.Forms.Image) para personalizar sua aparência:

    ```xaml
    <Image Source="http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
           Aspect="Fill"
           HeightRequest="{OnPlatform iOS=300, Android=250}"
           WidthRequest="{OnPlatform iOS=300, Android=250}"
           HorizontalOptions="Center" />
    ```

    Esse código define a propriedade [`Aspect`](xref:Xamarin.Forms.Image.Aspect), que define o modo de escala da imagem, como [`Fill`](xref:Xamarin.Forms.Aspect.Fill). O membro `Fill` é definido na enumeração [`Aspect`](xref:Xamarin.Forms.Aspect) e alonga a imagem para preencher completamente a exibição, independentemente de se a imagem está distorcida. Para obter mais informações sobre o dimensionamento de imagem, confira [Exibindo imagens](~/xamarin-forms/user-interface/images.md#display-images) no guia [Imagens no Xamarin.Forms](~/xamarin-forms/user-interface/images.md).

    A extensão de marcação `OnPlatform` permite que você personalize a aparência da interface do usuário por plataforma. Neste exemplo, a extensão de marcação é usada para definir as propriedades [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) e [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) como 300 unidades independentes de dispositivo em iOS e 250 unidades independentes de dispositivo em Android. Para obter mais informações sobre a extensão de marcação `OnPlatform`, confira [extensão de marcação OnPlatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform) no guia [Consumindo extensões de marcação XAML](~/xamarin-forms/xaml/markup-extensions/consuming.md).

    Além disso, a propriedade [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) especifica que a imagem será centralizada horizontalmente.

1. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com um botão Reproduzir) para iniciar o aplicativo dentro do simulador remoto de iOS ou do Android Emulator escolhido:

    [![Captura de tela de uma imagem que é dimensionada diferentemente no iOS e no Android](../images/customize-appearance.png "Imagem dimensionada segundo cada plataforma")](../images/customize-appearance-large.png#lightbox "Imagem dimensionada segundo cada plataforma")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Em **MainPage.xaml**, modifique a declaração [`Image`](xref:Xamarin.Forms.Image) para personalizar sua aparência:

    ```xaml
    <Image Source="http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
           Aspect="Fill"
           HeightRequest="{OnPlatform iOS=300, Android=250}"
           WidthRequest="{OnPlatform iOS=300, Android=250}"
           HorizontalOptions="Center" />
    ```

    Esse código define a propriedade [`Aspect`](xref:Xamarin.Forms.Image.Aspect), que define o modo de escala da imagem, como [`Fill`](xref:Xamarin.Forms.Aspect.Fill). O membro `Fill` é definido na enumeração [`Aspect`](xref:Xamarin.Forms.Aspect) e alonga a imagem para preencher completamente a exibição, independentemente de se a imagem está distorcida. Para obter mais informações sobre o dimensionamento de imagem, confira [Exibindo imagens](~/xamarin-forms/user-interface/images.md#display-images) no guia [Imagens no Xamarin.Forms](~/xamarin-forms/user-interface/images.md).

    A extensão de marcação `OnPlatform` permite que você personalize a aparência da interface do usuário por plataforma. Neste exemplo, a extensão de marcação é usada para definir as propriedades [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) e [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) como 300 em iOS e 250 em Android. Para obter mais informações sobre a extensão de marcação `OnPlatform`, confira [extensão de marcação OnPlatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform) no guia [Consumindo extensões de marcação XAML](~/xamarin-forms/xaml/markup-extensions/consuming.md).

    Além disso, a propriedade [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) especifica que a imagem será centralizada horizontalmente.

1. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo dentro do simulador de iOS ou do Android Emulator escolhido:

    [![Captura de tela de uma imagem que é dimensionada diferentemente no iOS e no Android](../images/customize-appearance.png "Imagem dimensionada segundo cada plataforma")](../images/customize-appearance-large.png#lightbox "Imagem dimensionada segundo cada plataforma")
