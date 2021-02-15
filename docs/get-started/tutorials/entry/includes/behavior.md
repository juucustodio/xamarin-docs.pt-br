---
ms.openlocfilehash: f4fccbfe7e00a353682b0dfe787c291b38e5cd76
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98690040"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. Em **MainPage.xaml**, modifique a declaração [`Entry`](xref:Xamarin.Forms.Entry) para personalizar seu comportamento:

    ```xaml
    <Entry Placeholder="Enter password"
           MaxLength="15"
           IsSpellCheckEnabled="false"
           IsTextPredictionEnabled="false"
           IsPassword="true" />
    ```

    Esse código define propriedades que personalizam o comportamento do [`Entry`](xref:Xamarin.Forms.Entry). A propriedade [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) limita o tamanho de entrada permitido para `Entry`, e a propriedade [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) é definida como `false` para desabilitar a verificação ortográfica. Da mesma forma, a propriedade [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) é definida como `false` para desabilitar a previsão de texto e a previsão de texto automática. Além disso, a propriedade [`IsPassword`](xref:Xamarin.Forms.Entry.IsPassword) garante que os caracteres digitados sejam mascarados com um caractere de senha (um círculo preto).

    > [!NOTE]
    > Para alguns cenários de entrada de texto, como inserir uma senha, a verificação ortográfica e a previsão de texto fornecem uma experiência negativa e, portanto, devem ser desabilitadas.

1. Caso o aplicativo ainda esteja em execução, salve as alterações no arquivo. Dessa maneira, a interface do usuário do aplicativo será atualizada de modo automático em seu simulador ou emulador. Caso contrário, na barra de ferramentas do Visual Studio, clique no botão **Iniciar** (um botão triangular semelhante ao botão Reproduzir) para executar a inicialização do aplicativo dentro do simulador remoto de iOS ou do Android Emulator de sua preferência. Insira o texto em [`Entry`](xref:Xamarin.Forms.Entry) e observe que cada caractere é substituído por um caractere de máscara de senha e que o número máximo de caracteres que pode ser inserido é 15:

    [![Captura de tela de uma entrada com texto mascarado por caracteres de senha, no iOS e no Android](../images/customize-behavior.png "Entrada com caracteres de senha mascarados")](../images/customize-behavior-large.png#lightbox "Entrada com caracteres de senha mascarados")

    Interrompa o aplicativo no Visual Studio.

    Para obter mais informações sobre como personalizar o comportamento [`Entry`](xref:Xamarin.Forms.Entry), confira o guia [Entrada do Xamarin.Forms](~/xamarin-forms/user-interface/text/entry.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Em **MainPage.xaml**, modifique a declaração [`Entry`](xref:Xamarin.Forms.Entry) para personalizar seu comportamento:

    ```xaml
    <Entry Placeholder="Enter password"
           MaxLength="15"
           IsSpellCheckEnabled="false"
           IsTextPredictionEnabled="false"
           IsPassword="true" />
    ```

    Esse código define propriedades que personalizam o comportamento do [`Entry`](xref:Xamarin.Forms.Entry). A propriedade [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) limita o tamanho de entrada permitido para `Entry`, e a propriedade [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) é definida como `false` para desabilitar a verificação ortográfica. Da mesma forma, a propriedade [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) é definida como `false` para desabilitar a previsão de texto e a previsão de texto automática. Além disso, a propriedade [`IsPassword`](xref:Xamarin.Forms.Entry.IsPassword) garante que os caracteres digitados sejam mascarados com um caractere de senha (um círculo preto).

    > [!NOTE]
    > Para alguns cenários de entrada de texto, como inserir uma senha, a verificação ortográfica e a previsão de texto fornecem uma experiência negativa e, portanto, devem ser desabilitadas.

1. Caso o aplicativo ainda esteja em execução, salve as alterações no arquivo. Dessa maneira, a interface do usuário do aplicativo será atualizada de modo automático em seu simulador ou emulador. Caso contrário, na barra de ferramentas do Visual Studio para Mac, clique no botão **Iniciar** (um botão triangular semelhante ao botão Reproduzir) para executar a inicialização do aplicativo dentro do simulador de iOS ou do Android Emulator de sua preferência. Insira o texto em [`Entry`](xref:Xamarin.Forms.Entry) e observe que cada caractere é substituído por um caractere de máscara de senha e que o número máximo de caracteres que pode ser inserido é 15:

    [![Captura de tela de uma entrada com texto mascarado por caracteres de senha, no iOS e no Android](../images/customize-behavior.png "Entrada com caracteres de senha mascarados")](../images/customize-behavior-large.png#lightbox "Entrada com caracteres de senha mascarados")

    Interrompa o aplicativo no Visual Studio para Mac.

    Para obter mais informações sobre como personalizar o comportamento [`Entry`](xref:Xamarin.Forms.Entry), confira o guia [Entrada do Xamarin.Forms](~/xamarin-forms/user-interface/text/entry.md).
