---
ms.openlocfilehash: 31ba5d809c88f7042f7cd178dd152acdc6448e0b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61037330"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Inicie o Visual Studio e crie um novo aplicativo Xamarin.Forms em branco chamado **ListViewTutorial**. Verifique se o aplicativo usa o .NET Standard como o mecanismo de código compartilhado.

    > [!IMPORTANT]
    > Os snippets de C# e XAML neste tutorial exigem que a solução seja denominada **ListViewTutorial**. Usar um nome diferente causará erros de build ao copiar o código deste tutorial para a solução.

    Para obter mais informações sobre a biblioteca .NET Standard criada, confira [Anatomia de um aplicativo Xamarin.Forms](~/get-started/first-app/index.md) em [Aprofundamento do Início Rápido do Xamarin.Forms](~/get-started/first-app/index.md).

1. No **Gerenciador de Soluções**, no projeto **ListViewTutorial**, clique duas vezes em **MainPage.xaml** para abri-lo. Em seguida, em **MainPage.xaml**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="ListViewTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <ListView>
                <ListView.ItemsSource>
                    <x:Array Type="{x:Type x:String}">
                      <x:String>Baboon</x:String>
                      <x:String>Capuchin Monkey</x:String>
                      <x:String>Blue Monkey</x:String>
                      <x:String>Squirrel Monkey</x:String>
                      <x:String>Golden Lion Tamarin</x:String>
                      <x:String>Howler Monkey</x:String>
                      <x:String>Japanese Macaque</x:String>
                    </x:Array>
                </ListView.ItemsSource>
            </ListView>
        </StackLayout>
    </ContentPage>
    ```

    Esse código define declarativamente a interface do usuário para a página, que é composta por um [`ListView`](xref:Xamarin.Forms.Image) em um [`StackLayout`](xref:Xamarin.Forms.StackLayout). A propriedade [`ListView.ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) especifica os itens serem exibidos, que são definidos em uma matriz de cadeias de caracteres.

1. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com um botão Reproduzir) para iniciar o aplicativo dentro do simulador remoto de iOS ou do Android Emulator escolhido:

    [![Captura de tela de uma ListView no iOS e no Android](../images/create-listview.png "ListView exibindo dados")](../images/create-listview-large.png#lightbox "ListView exibindo dados")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Inicie o Visual Studio para Mac e crie um novo aplicativo Xamarin.Forms em branco chamado **ListViewTutorial**. Verifique se o aplicativo usa o .NET Standard como o mecanismo de código compartilhado.

    > [!IMPORTANT]
    > Os snippets de C# e XAML neste tutorial exigem que a solução seja denominada **ListViewTutorial**. Usar um nome diferente causará erros de build ao copiar o código deste tutorial para a solução.

    Para obter mais informações sobre a biblioteca .NET Standard criada, confira [Anatomia de um aplicativo Xamarin.Forms](~/get-started/first-app/index.md) em [Aprofundamento do Início Rápido do Xamarin.Forms](~/get-started/first-app/index.md).

1. No **Painel de Soluções**, no projeto **ListViewTutorial**, clique duas vezes em **MainPage.xaml** para abri-lo. Em seguida, em **MainPage.xaml**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="ListViewTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <ListView>
                <ListView.ItemsSource>
                    <x:Array Type="{x:Type x:String}">
                      <x:String>Baboon</x:String>
                      <x:String>Capuchin Monkey</x:String>
                      <x:String>Blue Monkey</x:String>
                      <x:String>Squirrel Monkey</x:String>
                      <x:String>Golden Lion Tamarin</x:String>
                      <x:String>Howler Monkey</x:String>
                      <x:String>Japanese Macaque</x:String>
                    </x:Array>
                </ListView.ItemsSource>
            </ListView>
        </StackLayout>
    </ContentPage>
    ```

    Esse código define declarativamente a interface do usuário para a página, que é composta por um [`ListView`](xref:Xamarin.Forms.Image) em um [`StackLayout`](xref:Xamarin.Forms.StackLayout). A propriedade [`ListView.ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) especifica os itens serem exibidos, que são definidos em uma matriz de cadeias de caracteres.

1. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo dentro do simulador de iOS ou do Android Emulator escolhido:

    [![Captura de tela de uma ListView no iOS e no Android](../images/create-listview.png "ListView exibindo dados")](../images/create-listview-large.png#lightbox "ListView exibindo dados")
