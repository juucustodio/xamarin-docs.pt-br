---
ms.openlocfilehash: 4352ff38e785a0a222cf8d2d64511ed14b3ae192
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98689812"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. Em **MainPage.xaml**, modifique a declaração [`Label`](xref:Xamarin.Forms.Label) para apresentar texto que usa vários formatos, em um único `Label`.

    ```xaml
    <Label TextColor="Gray"
           FontSize="Medium">
        <Label.FormattedText>
            <FormattedString>
                <Span Text="This sentence contains " />
                <Span Text="words that are emphasized, "
                      FontAttributes="Italic" />
                <Span Text="and underlined."
                      TextDecorations="Underline" />
            </FormattedString>
        </Label.FormattedText>
    </Label>
    ```

    Este código exibe texto, em um único [`Label`](xref:Xamarin.Forms.Label), que usa vários formatos. O texto no primeiro [`Span`](xref:Xamarin.Forms.Span) é exibido usando a formatação definida em `Label`, enquanto o texto na segunda e terceira instâncias de `Span` é exibido usando a formatação definida em `Label` e a formatação adicional definida em cada `Span`.

    > [!NOTE]
    > A propriedade [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) é do tipo [`FormattedString`](xref:Xamarin.Forms.FormattedString), que inclui uma ou mais instâncias de [`Span`](xref:Xamarin.Forms.Span).

1. Caso o aplicativo ainda esteja em execução, salve as alterações no arquivo. Dessa maneira, a interface do usuário do aplicativo será atualizada de modo automático em seu simulador ou emulador. Caso contrário, na barra de ferramentas do Visual Studio, clique no botão **Iniciar** (um botão triangular semelhante ao botão Reproduzir) para executar a inicialização do aplicativo dentro do simulador remoto de iOS ou do Android Emulator de sua preferência. Observe que a aparência de [`Label`](xref:Xamarin.Forms.Label) mudou:

    [![Captura de tela de um rótulo exibindo o texto formatado, no iOS e no Android](../images/label-formatted-text.png "Rótulo com texto formatado")](../images/label-formatted-text-large.png#lightbox "Rótulo com texto formatado")

    Interrompa o aplicativo no Visual Studio.

    Para obter mais informações sobre como definir a aparência de [`Span`](xref:Xamarin.Forms.Span), confira [Texto formatado](~/xamarin-forms/user-interface/text/label.md#formatted-text) no guia [Rótulo do Xamarin.Forms](~/xamarin-forms/user-interface/text/label.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Em **MainPage.xaml**, modifique a declaração [`Label`](xref:Xamarin.Forms.Label) para apresentar texto que usa vários formatos, em um único `Label`.

    ```xaml
    <Label TextColor="Gray"
           FontSize="Medium">
        <Label.FormattedText>
            <FormattedString>
                <Span Text="This sentence contains " />
                <Span Text="words that are emphasized, "
                      FontAttributes="Italic" />
                <Span Text="and underlined."
                      TextDecorations="Underline" />
            </FormattedString>
        </Label.FormattedText>
    </Label>
    ```

    Este código exibe texto, em um único [`Label`](xref:Xamarin.Forms.Label), que usa vários formatos. O texto no primeiro [`Span`](xref:Xamarin.Forms.Span) é exibido usando a formatação definida em `Label`, enquanto o texto na segunda e terceira instâncias de `Span` é exibido usando a formatação definida em `Label` e a formatação adicional definida em cada `Span`.

    > [!NOTE]
    > A propriedade [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) é do tipo [`FormattedString`](xref:Xamarin.Forms.FormattedString), que inclui uma ou mais instâncias de [`Span`](xref:Xamarin.Forms.Span).

1. Caso o aplicativo ainda esteja em execução, salve as alterações no arquivo. Dessa maneira, a interface do usuário do aplicativo será atualizada de modo automático em seu simulador ou emulador. Caso contrário, na barra de ferramentas do Visual Studio para Mac, clique no botão **Iniciar** (um botão triangular semelhante ao botão Reproduzir) para executar a inicialização do aplicativo dentro do simulador de iOS ou do Android Emulator de sua preferência. Observe que a aparência de [`Label`](xref:Xamarin.Forms.Label) mudou:

    [![Captura de tela de um rótulo exibindo o texto formatado, no iOS e no Android](../images/label-formatted-text.png "Rótulo com texto formatado")](../images/label-formatted-text-large.png#lightbox "Rótulo com texto formatado")

    Interrompa o aplicativo no Visual Studio para Mac.

    Para obter mais informações sobre como definir a aparência de [`Span`](xref:Xamarin.Forms.Span), confira [Texto formatado](~/xamarin-forms/user-interface/text/label.md#formatted-text) no guia [Rótulo do Xamarin.Forms](~/xamarin-forms/user-interface/text/label.md).
