---
ms.openlocfilehash: c33db7de63a585cb6fb47aa1145aa724c9eb790f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61037262"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Em **MainPage.xaml**, modifique a declaração [`ListView`](xref:Xamarin.Forms.ListView) de modo que ela defina manipuladores para os eventos [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) e [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped):

    ```xaml
    <ListView ItemsSource="{Binding Monkeys}"
              ItemSelected="OnListViewItemSelected"
              ItemTapped="OnListViewItemTapped" />
    ```

    Esse código define o evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) para um manipulador de eventos chamado `OnListViewItemSelected` e o evento [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) para um manipulador de eventos chamado `OnListViewItemTapped`. Ambos os manipuladores de eventos serão criados na próxima etapa.

1. No **Gerenciador de Soluções**, no projeto **ListViewTutorial**, expanda **MainPage.xaml** e clique duas vezes em **MainPage.xaml.cs** para abri-lo. Em seguida, em **MainPage.xaml.cs**, adicione os manipuladores de eventos `OnListViewItemSelected` e `OnListViewItemTapped` à classe:

    ```csharp
    void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
    {
        Monkey selectedItem = e.SelectedItem as Monkey;
    }

    void OnListViewItemTapped(object sender, ItemTappedEventArgs e)
    {
        Monkey tappedItem = e.Item as Monkey;
    }
    ```

    Quando um item no [`ListView`](xref:Xamarin.Forms.ListView) é tocado, os eventos [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) e [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) são acionados, executando os métodos `OnListViewItemSelected` e `OnListItemTapped`, respectivamente. O argumento `sender` para os dois métodos é o objeto `ListView` responsável por acionar o evento e pode ser usado para acessar o objeto `ListView`. O argumento [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs) no método `OnListViewItemSelected` fornece o item selecionado e o argumento [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs) no `OnListViewItemTapped` método fornece o item tocado.

    > [!IMPORTANT]
    > O evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) só é acionado quando um novo item é selecionado no [`ListView`](xref:Xamarin.Forms.ListView). Portanto, tocar duas vezes no mesmo item acionará dois eventos [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped), mas somente um evento `ItemSelected` será acionado.

1. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com um botão Reproduzir) para iniciar o aplicativo dentro do simulador remoto de iOS ou do Android Emulator escolhido:

    [![Captura de tela de um ListView que responde à seleção e toques no item, no iOS e Android](../images/item-selection.png "Seleção de item de ListView")](../images/item-selection-large.png#lightbox "Seleção de item de ListView")

    Defina pontos de interrupção nos dois manipuladores de eventos e toque em itens no [`ListView`](xref:Xamarin.Forms.ListView). Observe que o evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) é acionado apenas quando um novo item é selecionado no [`ListView`](xref:Xamarin.Forms.ListView), enquanto o evento [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) é acionado sempre que um item é tocado.

    Para obter mais informações sobre a seleção e toques em itens, confira [Seleção e toques](~/xamarin-forms/user-interface/listview/interactivity.md#selection--taps) no guia [Interatividade de ListView](~/xamarin-forms/user-interface/listview/interactivity.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Em **MainPage.xaml**, modifique a declaração [`ListView`](xref:Xamarin.Forms.ListView) de modo que ela defina manipuladores para os eventos [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) e [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped):

    ```xaml
    <ListView ItemsSource="{Binding Monkeys}"
              ItemSelected="OnListViewItemSelected"
              ItemTapped="OnListViewItemTapped" />
    ```

    Esse código define o evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) para um manipulador de eventos chamado `OnListViewItemSelected` e o evento [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) para um manipulador de eventos chamado `OnListViewItemTapped`. Ambos os manipuladores de eventos serão criados na próxima etapa.

1. No **Painel de Soluções**, no projeto **ListViewTutorial**, expanda **MainPage.xaml** e clique duas vezes em **MainPage.xaml.cs** para abri-lo. Em seguida, em **MainPage.xaml.cs**, adicione os manipuladores de eventos `OnListViewItemSelected` e `OnListViewItemTapped` à classe:

    ```csharp
    void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
    {
        Monkey selectedItem = e.SelectedItem as Monkey;
    }

    void OnListViewItemTapped(object sender, ItemTappedEventArgs e)
    {
        Monkey tappedItem = e.Item as Monkey;
    }
    ```

    Quando um item no [`ListView`](xref:Xamarin.Forms.ListView) é tocado, os eventos [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) e [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) são acionados, executando os métodos `OnListViewItemSelected` e `OnListItemTapped`, respectivamente. O argumento `sender` para os dois métodos é o objeto `ListView` responsável por acionar o evento e pode ser usado para acessar o objeto `ListView`. O argumento [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs) no método `OnListViewItemSelected` fornece o item selecionado e o argumento [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs) no `OnListViewItemTapped` método fornece o item tocado.

    > [!IMPORTANT]
    > O evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) só é acionado quando um novo item é selecionado no [`ListView`](xref:Xamarin.Forms.ListView). Portanto, tocar duas vezes no mesmo item acionará dois eventos [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped), mas somente um evento `ItemSelected` será acionado.

1. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo dentro do simulador de iOS ou do Android Emulator escolhido:

    [![Captura de tela de um ListView que responde à seleção e toques no item, no iOS e Android](../images/item-selection.png "Seleção de item de ListView")](../images/item-selection-large.png#lightbox "Seleção de item de ListView")

    Defina pontos de interrupção nos dois manipuladores de eventos e toque em itens no [`ListView`](xref:Xamarin.Forms.ListView). Observe que o evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) é acionado apenas quando um novo item é selecionado no [`ListView`](xref:Xamarin.Forms.ListView), enquanto o evento [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) é acionado sempre que um item é tocado.

    Para obter mais informações sobre a seleção e toques em itens, confira [Seleção e toques](~/xamarin-forms/user-interface/listview/interactivity.md#selection--taps) no guia [Interatividade de ListView](~/xamarin-forms/user-interface/listview/interactivity.md).
