# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

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

1. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo dentro de seu simulador remoto de iOS ou do Android Emulator escolhido. Observe que a aparência de [`Label`](xref:Xamarin.Forms.Label) mudou:

    [![Captura de tela de um Rótulo exibindo texto formatado, no iOS e Android](../images/label-formatted-text.png "Rótulo com texto formatado")](../images/label-formatted-text-large.png#lightbox "Rótulo com texto formatado")

    Para obter mais informações sobre como definir a aparência de [`Span`](xref:Xamarin.Forms.Span), confira [Texto formatado](~/xamarin-forms/user-interface/text/label.md#formatted-text) no guia [Rótulo do Xamarin.Forms](~/xamarin-forms/user-interface/text/label.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

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

1. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com um botão Reproduzir) para iniciar o aplicativo dentro do simulador de iOS ou do Android Emulator escolhido. Observe que a aparência de [`Label`](xref:Xamarin.Forms.Label) mudou:

    [![Captura de tela de um Rótulo exibindo texto formatado, no iOS e Android](../images/label-formatted-text.png "Rótulo com texto formatado")](../images/label-formatted-text-large.png#lightbox "Rótulo com texto formatado")

    Para obter mais informações sobre como definir a aparência de [`Span`](xref:Xamarin.Forms.Span), confira [Texto formatado](~/xamarin-forms/user-interface/text/label.md#formatted-text) no guia [Rótulo do Xamarin.Forms](~/xamarin-forms/user-interface/text/label.md).
