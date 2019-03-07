# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Em **MainPage.xaml**, modifique a declaração [`Editor`](xref:Xamarin.Forms.Editor) para personalizar seu comportamento:

    ```xaml
    <Editor Placeholder="Enter multi-line text here"
            AutoSize="TextChanges"
            MaxLength="200"
            IsSpellCheckEnabled="false"
            IsTextPredictionEnabled="false" />
    ```

    Esse código define propriedades que personalizam o comportamento do [`Editor`](xref:Xamarin.Forms.Editor). A propriedade [`AutoSize`](xref:Xamarin.Forms.Editor.AutoSize) é definida como [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges), o que indica que a altura do `Editor` aumentará quando for preenchida com texto e diminuirá quando o texto for removido. A propriedade [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) limita o tamanho de entrada permitido para o `Editor`. Além disso, a propriedade [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) é definida como `false` para desabilitar a verificação de ortografia, enquanto a propriedade `IsTextPredictionEnabled` é definida como `false` para desabilitar a previsão de texto e a previsão de texto automática.

1. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo dentro de seu simulador remoto de iOS ou do Android Emulator escolhido. Insira o texto para [`Editor`](xref:Xamarin.Forms.Entry) e observe que a altura do `Editor` aumenta à medida que é preenchido com texto e diminui à medida que o texto é removido e que o número máximo de caracteres que pode ser inserido é de 200:

    [![Captura de tela de um Editor de dimensionamento automático em iOS e Android](../images/customize-behavior.png "Editor de dimensionamento automático")](../images/customize-behavior-large.png#lightbox "Editor de dimensionamento automático")

    Para obter mais informações sobre como personalizar o comportamento [`Editor`](xref:Xamarin.Forms.Editor), confira o guia [Editor do Xamarin.Forms](~/xamarin-forms/user-interface/text/editor.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Em **MainPage.xaml**, modifique a declaração [`Editor`](xref:Xamarin.Forms.Editor) para personalizar seu comportamento:

    ```xaml
    <Editor Placeholder="Enter multi-line text here"
            AutoSize="TextChanges"
            MaxLength="200"
            IsSpellCheckEnabled="false"
            IsTextPredictionEnabled="false" />
    ```

    Esse código define propriedades que personalizam o comportamento do [`Editor`](xref:Xamarin.Forms.Editor). A propriedade [`AutoSize`](xref:Xamarin.Forms.Editor.AutoSize) é definida como [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges), o que indica que a altura do `Editor` aumentará quando for preenchida com texto e diminuirá quando o texto for removido. A propriedade [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) limita o tamanho de entrada permitido para o `Editor`. Além disso, a propriedade [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) é definida como `false` para desabilitar a verificação de ortografia, enquanto a propriedade `IsTextPredictionEnabled` é definida como `false` para desabilitar a previsão de texto e a previsão de texto automática.

1. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com um botão Reproduzir) para iniciar o aplicativo dentro do simulador de iOS ou do Android Emulator escolhido. Insira o texto para [`Editor`](xref:Xamarin.Forms.Entry) e observe que a altura do `Editor` aumenta à medida que é preenchido com texto e diminui à medida que o texto é removido e que o número máximo de caracteres que pode ser inserido é de 200:

    [![Captura de tela de um Editor de dimensionamento automático em iOS e Android](../images/customize-behavior.png "Editor de dimensionamento automático")](../images/customize-behavior-large.png#lightbox "Editor de dimensionamento automático")

    Para obter mais informações sobre como personalizar o comportamento [`Editor`](xref:Xamarin.Forms.Editor), confira o guia [Editor do Xamarin.Forms](~/xamarin-forms/user-interface/text/editor.md).
