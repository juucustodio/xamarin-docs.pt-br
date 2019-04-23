---
ms.openlocfilehash: 48af50d31013f696879174a5cf108ab9fde92d0b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61343384"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

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

1. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo dentro de seu simulador remoto de iOS ou do Android Emulator escolhido. Insira o texto em [`Entry`](xref:Xamarin.Forms.Entry) e observe que cada caractere é substituído por um caractere de máscara de senha e que o número máximo de caracteres que pode ser inserido é 15:

    [![Captura de tela de uma entrada de texto mascarada por caracteres de senha em iOS e Android](../images/customize-behavior.png "Entrada com caracteres de senha mascarados")](../images/customize-behavior-large.png#lightbox "Entrada com caracteres de senha mascarados")

    Para obter mais informações sobre como personalizar o comportamento [`Entry`](xref:Xamarin.Forms.Entry), confira o guia [Entrada do Xamarin.Forms](~/xamarin-forms/user-interface/text/entry.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

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

1. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com um botão Reproduzir) para iniciar o aplicativo dentro do simulador de iOS ou do Android Emulator escolhido. Insira o texto em [`Entry`](xref:Xamarin.Forms.Entry) e observe que cada caractere é substituído por um caractere de máscara de senha e que o número máximo de caracteres que pode ser inserido é 15:

    [![Captura de tela de uma entrada de texto mascarada por caracteres de senha em iOS e Android](../images/customize-behavior.png "Entrada com caracteres de senha mascarados")](../images/customize-behavior-large.png#lightbox "Entrada com caracteres de senha mascarados")

    Para obter mais informações sobre como personalizar o comportamento [`Entry`](xref:Xamarin.Forms.Entry), confira o guia [Entrada do Xamarin.Forms](~/xamarin-forms/user-interface/text/entry.md).
