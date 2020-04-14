---
ms.openlocfilehash: 93ee0681adcc63fe05b4be88ff67f0aeee3e03ca
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "61384366"
---
Arquivos de imagem podem ser adicionados a projetos de plataforma e referenciados no código compartilhado do Xamarin.Forms. Esse método de distribuição de imagens é necessário quando as imagens são específicas da plataforma, como ao usar resoluções diferentes em diferentes plataformas ou designs ligeiramente diferentes.

Neste exercício, você modificará a solução **ImageTutorial** para exibir uma imagem local, em vez de uma imagem baixada de um URI. A imagem local é o logotipo do Xamarin, que deve ser baixado clicando no botão abaixo.

> [!div class="nextstepaction"]
> [Baixar XamarinLogo.png](https://raw.githubusercontent.com/xamarin/xamarin-forms-samples/master/UserInterface/PlatformSpecifics/Droid/Resources/drawable/XamarinLogo.png)

> [!IMPORTANT]
> Para usar uma única imagem em todas as plataformas *o mesmo nome de arquivo deve ser usado em todas as plataformas*, e deve ser um nome de recurso válido do Android (ou seja, apenas letras minúsculas, números, sublinhado e ponto são permitidos).

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. Na **Gerenciador de Soluções**, no projeto **ImageTutorial.iOS**, expanda **Catálogos de Ativos** e clique duas vezes em **Ativos** para abri-lo. Em seguida, na guia **Assets.xcassets**, clique no botão **Mais** e selecione **Adicionar Conjunto de Imagens**:

    ![Captura de tela da criação de um conjunto de imagens no catálogo de ativos no Visual Studio](../images/vs/new-image-set.png "Novo conjunto de imagens do catálogo de ativos")

1. Na guia **Assets.xcassets**, selecione o novo conjunto de imagens e o editor será exibido:

    ![Captura de tela de um novo conjunto de imagens no catálogo de ativos no Visual Studio](../images/vs/new-image-set-editor.png "Editor do conjunto de imagens do catálogo de ativos")

1. Arraste **XamarinLogo.png** de seu sistema de arquivos para a caixa **1x** para a categoria **Universal**:

    ![Captura de tela do conjunto de imagens que contém uma imagem no Visual Studio](../images/vs/image-set-with-image.png "Conjunto de imagens contendo uma imagem")

1. Na guia **Assets.xcassets**, clique com o botão direito do mouse no nome do conjunto de imagens e altere seu nome para **XamarinLogo**:

    ![Captura de tela do conjunto de imagens renomeado no Visual Studio](../images/vs/rename-image-set.png "Conjunto de imagens renomeado")

    Salve e feche a guia **Assets.xcassets**.

1. No **Gerenciador de Soluções**, no projeto **ImageTutorial.Android**, expanda a pasta **Recursos**. Em seguida, arraste **XamarinLogo.png** de seu sistema de arquivos para a pasta **desenhável**:

    ![Captura de tela do arquivo de imagem como um recurso do Android no Visual Studio](../images/vs/android-resource.png "Arquivo de imagem local na pasta de recursos do Android")

    > [!NOTE]
    > O Visual Studio definirá automaticamente a ação de compilação para a imagem como **AndroidResource**.

1. No projeto **ImageTutorial**, na **MainPage.xaml**, modifique a declaração [`Image`](xref:Xamarin.Forms.Editor) para exibir o arquivo **XamarinLogo** local:

    ```xaml
    <Image Source="XamarinLogo"
           WidthRequest="{OnPlatform iOS=300, Android=250}"
           HorizontalOptions="Center" />
    ```

    Esse código define a propriedade [`Source`](xref:Xamarin.Forms.Image.Source) para o arquivo local exibir. A propriedade [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) é definida como 300 unidades independentes de dispositivo em iOS e 250 unidades independentes de dispositivo em Android. Além disso, a propriedade [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) especifica que a imagem será centralizada horizontalmente.

    > [!NOTE]
    > Para imagens PNG no iOS, a extensão **.png** pode ser omitida do nome de arquivo especificado na propriedade [`Source`](xref:Xamarin.Forms.Image.Source). Para outros formatos de imagem, a extensão é necessária.

1. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo dentro do simulador de iOS ou do Android Emulator escolhido:

    [![Captura de tela de uma exibição de imagem que mostra uma imagem local, no iOS e no Android](../images/local-file.png "Exibição de imagem mostrando uma imagem local")](../images/local-file-large.png#lightbox "Exibição de imagem mostrando uma imagem local")

    Para obter mais informações sobre imagens locais, confira [Imagens locais](~/xamarin-forms/user-interface/images.md#local-images) no guia [Imagens no Xamarin.Forms](~/xamarin-forms/user-interface/images.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

1. No **Painel de Soluções**, no projeto **ImageTutorial.iOS**, clique duas vezes em **Assets.xcassets** para abri-lo. Em seguida, na **Lista de Ativos**, clique com o botão direito do mouse e selecione **Novo Conjunto de Imagens**:

    ![Captura de tela da criação de um conjunto de imagens no catálogo de ativos no Visual Studio para Mac](../images/vsmac/new-image-set.png "Novo conjunto de imagens do catálogo de ativos")

1. Na guia **Lista de Ativos**, selecione o novo conjunto de imagens e o editor será exibido:

    ![Captura de tela do novo conjunto de imagens no catálogo de ativos no Visual Studio para Mac](../images/vsmac/new-image-set-editor.png "Editor do conjunto de imagens do catálogo de ativos")

1. Arraste **XamarinLogo.png** de seu sistema de arquivos para a caixa **1x** para a categoria **Universal**:

    ![Captura de tela do conjunto de imagens que contém uma imagem no Visual Studio para Mac](../images/vsmac/image-set-with-image.png "Conjunto de imagens contendo uma imagem")

1. Na **Lista de Ativos**, clique duas vezes no nome do novo conjunto de imagens e altere seu nome para **XamarinLogo**:

    ![Captura de tela do conjunto de imagens renomeado no Visual Studio para Mac](../images/vsmac/rename-image-set.png "Conjunto de imagens renomeado")

1. No **Painel de Soluções**, no projeto **ImageTutorial.Android**, expanda a pasta **Recursos**. Em seguida, arraste **XamarinLogo.png** de seu sistema de arquivos para a pasta **desenhável**.

1. Na caixa de diálogo **Adicionar Arquivo à Pasta**, selecione **OK**.

    ![Captura de tela do arquivo de imagem como um recurso do Android no Visual Studio para Mac](../images/vsmac/android-resource.png "Arquivo de imagem local na pasta de recursos do Android")

    > [!NOTE]
    > O Visual Studio para Mac definirá automaticamente a ação de compilação para a imagem como **AndroidResource**.

1. No projeto **ImageTutorial**, na **MainPage.xaml**, modifique a declaração [`Image`](xref:Xamarin.Forms.Editor) para exibir o arquivo **XamarinLogo** local:

    ```xaml
    <Image Source="XamarinLogo"
           WidthRequest="{OnPlatform iOS=300, Android=250}"
           HorizontalOptions="Center" />
    ```

    Esse código define a propriedade [`Source`](xref:Xamarin.Forms.Image.Source) para o arquivo local exibir. A propriedade [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) é definida como 300 unidades independentes de dispositivo em iOS e 250 unidades independentes de dispositivo em Android. Além disso, a propriedade [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) especifica que a imagem será centralizada horizontalmente.

    > [!NOTE]
    > Para imagens PNG no iOS, a extensão **.png** pode ser omitida do nome de arquivo especificado na propriedade [`Source`](xref:Xamarin.Forms.Image.Source). Para outros formatos de imagem, a extensão é necessária.

1. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo dentro do simulador de iOS ou do Android Emulator escolhido:

    [![Captura de tela de uma exibição de imagem que mostra uma imagem local, no iOS e no Android](../images/local-file.png "Exibição de imagem mostrando uma imagem local")](../images/local-file-large.png#lightbox "Exibição de imagem mostrando uma imagem local")

    Para obter mais informações sobre imagens locais, confira [Imagens locais](~/xamarin-forms/user-interface/images.md#local-images) no guia [Imagens no Xamarin.Forms](~/xamarin-forms/user-interface/images.md).
