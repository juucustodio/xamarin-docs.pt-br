---
ms.openlocfilehash: 41c1c8ae97c62a3eb2a73681b215e7687d0e473c
ms.sourcegitcommit: b75c369adb8e02a429b6c0fed8ba4a855099bf01
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98558981"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. Em **MainPage.xaml**, modifique a declaração [`CollectionView`](xref:Xamarin.Forms.CollectionView) de modo que ela defina a propriedade [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) como `Single` e defina um manipulador para o evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged):

    ```xaml
    <CollectionView ItemsSource="{Binding Monkeys}"
                    SelectionMode="Single"
                    SelectionChanged="OnSelectionChanged" />
    ```

    Esses conjuntos de código habilitam a seleção de um único item no [`CollectionView`](xref:Xamarin.Forms.CollectionView) e definem o evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) para um manipulador de eventos chamado `OnSelectionChanged`. O manipulador de eventos será criado na próxima etapa.

1. No **Gerenciador de Soluções**, no projeto **CollectionViewTutorial**, expanda **MainPage.xaml** e clique duas vezes em **MainPage.xaml.cs** para abri-lo. Em seguida, em **MainPage.xaml.cs**, adicione o manipulador de eventos `OnSelectionChanged` à classe:

    ```csharp
    void OnSelectionChanged(object sender, SelectionChangedEventArgs e)
    {
        Monkey selectedItem = e.CurrentSelection[0] as Monkey;
    }
    ```

    Quando um item no [`CollectionView`](xref:Xamarin.Forms.CollectionView) é selecionado, o evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) é acionado, o que executa o método `OnSelectionChanged`. O argumento `sender` para o método é o objeto `CollectionView` responsável por acionar o evento, que pode ser usado para acessar o objeto `CollectionView`. O argumento [`SelectionChangedEventArgs`](xref:Xamarin.Forms.SelectionChangedEventArgs) para o método `OnSelectionChanged` fornece o item selecionado.

1. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com um botão Reproduzir) para iniciar o aplicativo dentro do simulador remoto de iOS ou do Android Emulator escolhido:

    [![Captura de tela de uma CollectionView que responde à seleção do item, no iOS e no Android](../images/item-selection.png "Seleção de item da CollectionView")](../images/item-selection-large.png#lightbox "Seleção de item da CollectionView")

    Defina um ponto de interrupção no manipulador de eventos `OnSelectionChanged` e selecione um item no [`CollectionView`](xref:Xamarin.Forms.CollectionView). Examine o valor da variável `selectedItem` para garantir que ela contenha os dados do item selecionado.

    Para obter mais informações sobre a seleção de itens, confira [Seleção da CollectionView do Xamarin.Forms](~/xamarin-forms/user-interface/collectionview/selection.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Em **MainPage.xaml**, modifique a declaração [`CollectionView`](xref:Xamarin.Forms.CollectionView) de modo que ela defina a propriedade [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) como `Single` e defina um manipulador para o evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged):

    ```xaml
    <CollectionView ItemsSource="{Binding Monkeys}"
                    SelectionMode="Single"
                    SelectionChanged="OnSelectionChanged" />
    ```

    Esses conjuntos de código habilitam a seleção de um único item no [`CollectionView`](xref:Xamarin.Forms.CollectionView) e definem o evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) para um manipulador de eventos chamado `OnSelectionChanged`. O manipulador de eventos será criado na próxima etapa.

1. No **Painel de Soluções**, no projeto **CollectionViewTutorial**, expanda **MainPage.xaml** e clique duas vezes em **MainPage.xaml.cs** para abri-lo. Em seguida, em **MainPage.xaml.cs**, adicione o manipulador de eventos `OnSelectionChanged` à classe:

    ```csharp
    void OnSelectionChanged(object sender, SelectionChangedEventArgs e)
    {
        Monkey selectedItem = e.CurrentSelection[0] as Monkey;
    }
    ```

    Quando um item no [`CollectionView`](xref:Xamarin.Forms.CollectionView) é selecionado, o evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) é acionado, o que executa o método `OnSelectionChanged`. O argumento `sender` para o método é o objeto `CollectionView` responsável por acionar o evento, que pode ser usado para acessar o objeto `CollectionView`. O argumento [`SelectionChangedEventArgs`](xref:Xamarin.Forms.SelectionChangedEventArgs) para o método `OnSelectionChanged` fornece o item selecionado.

1. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo dentro do simulador de iOS ou do Android Emulator escolhido:

    [![Captura de tela de uma CollectionView que responde à seleção do item, no iOS e no Android](../images/item-selection.png "Seleção de item da CollectionView")](../images/item-selection-large.png#lightbox "Seleção de item da CollectionView")

    Defina um ponto de interrupção no manipulador de eventos `OnSelectionChanged` e selecione um item no [`CollectionView`](xref:Xamarin.Forms.CollectionView). Examine o valor da variável `selectedItem` para garantir que ela contenha os dados do item selecionado.

    Para obter mais informações sobre a seleção de itens, confira [Seleção da CollectionView do Xamarin.Forms](~/xamarin-forms/user-interface/collectionview/selection.md).
