---
ms.openlocfilehash: 624d7f2866767bbb748988a3c486718d7691faf6
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98634853"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. Em **MainPage.xaml**, modifique a declaração [`Grid`](xref:Xamarin.Forms.Grid) para definir colunas e linhas e coloque o conteúdo nas colunas e linhas:

    ```xaml
    <Grid Margin="20,35,20,20">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.5*" />
            <ColumnDefinition Width="0.5*" />
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition Height="50" />
            <RowDefinition Height="50" />
        </Grid.RowDefinitions>
        <Label Text="Column 0, Row 0" />
        <Label Grid.Column="1"
               Text="Column 1, Row 0" />
        <Label Grid.Row="1"
               Text="Column 0, Row 1" />
        <Label Grid.Column="1"
               Grid.Row="1"
               Text="Column 1, Row 1" />
    </Grid>
    ```

    Esse código define as colunas e linhas para a [`Grid`](xref:Xamarin.Forms.Grid) e posiciona as instâncias de [`Label`](xref:Xamarin.Forms.Label) em linhas e colunas específicas. Os dados das linhas e colunas são armazenados nas propriedades [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) e [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions), que são coleções de objetos [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) e [`RowDefinition`](xref:Xamarin.Forms.RowDefinition), respectivamente. A largura de cada `ColumnDefinition` é definida pela propriedade [`ColumnDefinition.Width`](xref:Xamarin.Forms.ColumnDefinition.Width) e a altura de cada `RowDefinition` é definida pela propriedade [`RowDefinition.Height`](xref:Xamarin.Forms.RowDefinition.Height). Os valores válidos de largura e altura são:

    - [`Auto`](xref:Xamarin.Forms.GridUnitType.Auto), que dimensiona a coluna ou linha para que o conteúdo caiba nela.
    - Valores proporcionais, que dimensionam as colunas e linhas de forma proporcional ao espaço restante. Valores proporcionais são indicados pela terminação com um `*`.
    - Valores absolutos, que dimensionam as colunas ou linhas com valores fixos específicos.

    Sendo assim, no código acima, cada coluna tem uma largura equivalente a metade de [`Grid`](xref:Xamarin.Forms.Grid), enquanto cada linha tem uma altura de 50 unidades independentes do dispositivo.

    A posição de cada [`Label`](xref:Xamarin.Forms.Label) no [`Grid`](xref:Xamarin.Forms.Grid) é especificada com as propriedades anexadas [`Grid.Column`](xref:Xamarin.Forms.Grid.ColumnProperty) e [`Grid.Row`](xref:Xamarin.Forms.Grid.RowProperty), usando um índice baseado em zero. Portanto, a primeira coluna é a coluna 0 e a primeira linha é a linha 0. O primeiro `Label` não tem essas propriedades anexadas porque as exibições filho que não as definem são renderizadas automaticamente na coluna 0, linha 0.

    > [!NOTE]
    > O espaçamento entre colunas e linhas em uma [`Grid`](xref:Xamarin.Forms.Grid) pode ser definido com as propriedades [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) e [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing). Para obter mais informações, confira [Espaçamento](~/xamarin-forms/user-interface/layouts/grid.md#space-between-rows-and-columns) no guia [Grade do Xamarin.Forms](~/xamarin-forms/user-interface/layouts/grid.md).

1. Caso o aplicativo ainda esteja em execução, salve as alterações no arquivo. Dessa maneira, a interface do usuário do aplicativo será atualizada de modo automático em seu simulador ou emulador. Caso contrário, na barra de ferramentas do Visual Studio, clique no botão **Iniciar** (um botão triangular semelhante ao botão Reproduzir) para executar a inicialização do aplicativo dentro do simulador remoto de iOS ou do Android Emulator de sua preferência:

    [![Captura de tela de uma grade que tem conteúdo disposto em colunas e linhas, no iOS e no Android](../images/columns-rows.png "Grade com conteúdo em colunas e linhas")](../images/columns-rows-large.png#lightbox "Grade com conteúdo em colunas e linhas")

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Em **MainPage.xaml**, modifique a declaração [`Grid`](xref:Xamarin.Forms.Grid) para definir colunas e linhas e coloque o conteúdo nas colunas e linhas:

    ```xaml
    <Grid Margin="20,35,20,20">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.5*" />
            <ColumnDefinition Width="0.5*" />
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition Height="50" />
            <RowDefinition Height="50" />
        </Grid.RowDefinitions>
        <Label Text="Column 0, Row 0" />
        <Label Grid.Column="1"
               Text="Column 1, Row 0" />
        <Label Grid.Row="1"
               Text="Column 0, Row 1" />
        <Label Grid.Column="1"
               Grid.Row="1"
               Text="Column 1, Row 1" />
    </Grid>
    ```

    Esse código define as colunas e linhas para a [`Grid`](xref:Xamarin.Forms.Grid) e posiciona as instâncias de [`Label`](xref:Xamarin.Forms.Label) em linhas e colunas específicas. Os dados das linhas e colunas são armazenados nas propriedades [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) e [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions), que são coleções de objetos [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) e [`RowDefinition`](xref:Xamarin.Forms.RowDefinition), respectivamente. A largura de cada `ColumnDefinition` é definida pela propriedade [`ColumnDefinition.Width`](xref:Xamarin.Forms.ColumnDefinition.Width) e a altura de cada `RowDefinition` é definida pela propriedade [`RowDefinition.Height`](xref:Xamarin.Forms.RowDefinition.Height). Os valores válidos de largura e altura são:

    - [`Auto`](xref:Xamarin.Forms.GridUnitType.Auto), que dimensiona a coluna ou linha para que o conteúdo caiba nela.
    - Valores proporcionais, que dimensionam as colunas e linhas de forma proporcional ao espaço restante. Valores proporcionais são indicados pela terminação com um `*`.
    - Valores absolutos, que dimensionam as colunas ou linhas com valores fixos específicos.

    Sendo assim, no código acima, cada coluna tem uma largura equivalente a metade de [`Grid`](xref:Xamarin.Forms.Grid), enquanto cada linha tem uma altura de 50 unidades independentes do dispositivo.

    A posição de cada [`Label`](xref:Xamarin.Forms.Label) no [`Grid`](xref:Xamarin.Forms.Grid) é especificada com as propriedades anexadas [`Grid.Column`](xref:Xamarin.Forms.Grid.ColumnProperty) e [`Grid.Row`](xref:Xamarin.Forms.Grid.RowProperty), usando um índice baseado em zero. Portanto, a primeira coluna é a coluna 0 e a primeira linha é a linha 0. O primeiro `Label` não tem essas propriedades anexadas porque as exibições filho que não as definem são renderizadas automaticamente na coluna 0, linha 0.

    > [!NOTE]
    > O espaçamento entre colunas e linhas em uma [`Grid`](xref:Xamarin.Forms.Grid) pode ser definido com as propriedades [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) e [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing). Para obter mais informações, confira [Espaçamento](~/xamarin-forms/user-interface/layouts/grid.md#space-between-rows-and-columns) no guia [Grade do Xamarin.Forms](~/xamarin-forms/user-interface/layouts/grid.md).

1. Caso o aplicativo ainda esteja em execução, salve as alterações no arquivo. Dessa maneira, a interface do usuário do aplicativo será atualizada de modo automático em seu simulador ou emulador. Caso contrário, na barra de ferramentas do Visual Studio para Mac, clique no botão **Iniciar** (um botão triangular semelhante ao botão Reproduzir) para executar a inicialização do aplicativo dentro do simulador de iOS ou do Android Emulator de sua preferência:

    [![Captura de tela de uma grade que tem conteúdo disposto em colunas e linhas, no iOS e no Android](../images/columns-rows.png "Grade com conteúdo em colunas e linhas")](../images/columns-rows-large.png#lightbox "Grade com conteúdo em colunas e linhas")
