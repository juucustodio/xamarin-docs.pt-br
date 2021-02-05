---
title: Xamarin.Forms Editor
description: Este artigo explica como usar o Xamarin.Forms controle editor para aceitar a entrada de texto de várias linhas em um aplicativo.
ms.prod: xamarin
ms.assetid: 7074DB3A-30D2-4A6B-9A89-B029EEF20B07
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/21/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0a0517f0eac552f18a88d84bafba2e7e1e1f2631
ms.sourcegitcommit: 10c7dd16fe78226053d1d036492b6c9102fc421b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "93367550"
---
# <a name="xamarinforms-editor"></a>Xamarin.Forms Editor

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-text)

O [`Editor`](xref:Xamarin.Forms.Editor) controle é usado para aceitar a entrada de várias linhas.

## <a name="set-and-read-text"></a>Definir e ler texto

O [`Editor`](xref:Xamarin.Forms.Editor) , como outras exibições de apresentação de texto, expõe a `Text` propriedade. Essa propriedade pode ser usada para definir e ler o texto apresentado pelo `Editor` . O exemplo a seguir demonstra como definir a `Text` propriedade em XAML:

```xaml
<Editor Text="I am an Editor" />
```

No C#:

```csharp
var MyEditor = new Editor { Text = "I am an Editor" };
```

Para ler o texto, acesse a `Text` propriedade em C#:

```csharp
var text = MyEditor.Text;
```

## <a name="set-placeholder-text"></a>Definir texto do espaço reservado

O [`Editor`](xref:Xamarin.Forms.Editor) pode ser definido para mostrar o texto do espaço reservado quando não estiver armazenando a entrada do usuário. Isso é feito definindo a [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) propriedade como a `string` , e geralmente é usado para indicar o tipo de conteúdo apropriado para o `Editor` . Além disso, a cor do texto do espaço reservado pode ser controlada definindo a [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor) propriedade como a [`Color`](xref:Xamarin.Forms.Color) :

```xaml
<Editor Placeholder="Enter text here" PlaceholderColor="Olive" />
```

```csharp
var editor = new Editor { Placeholder = "Enter text here", PlaceholderColor = Color.Olive };
```

## <a name="prevent-text-entry"></a>Impedir entrada de texto

Os usuários podem ser impedidos de modificar o texto em um [`Editor`](xref:Xamarin.Forms.Editor) definindo a `IsReadOnly` propriedade, que tem um valor padrão de `false` , para `true` :

```xaml
<Editor Text="This is a read-only Editor"
        IsReadOnly="true" />
```

```csharp
var editor = new Editor { Text = "This is a read-only Editor", IsReadOnly = true });
```

> [!NOTE]
> A `IsReadonly` propriedade não altera a aparência visual de um [`Editor`](xref:Xamarin.Forms.Editor) , diferente da `IsEnabled` propriedade que também altera a aparência visual do `Editor` para cinza.

## <a name="transform-text"></a>Texto de transformação

Um [`Editor`](xref:Xamarin.Forms.Editor) pode transformar a capitalização de seu texto, armazenado na `Text` propriedade, definindo a `TextTransform` propriedade como um valor da `TextTransform` enumeração. Essa enumeração tem quatro valores:

- `None` indica que o texto não será transformado.
- `Default` indica que o comportamento padrão para a plataforma será usado. Este é o valor padrão da propriedade `TextTransform`.
- `Lowercase` indica que o texto será transformado em minúsculas.
- `Uppercase` indica que o texto será transformado em letras maiúsculas.

O exemplo a seguir mostra a transformação de texto em maiúsculas:

```xaml
<Editor Text="This text will be displayed in uppercase."
        TextTransform="Uppercase" />
```

Este é o código C# equivalente:

```csharp
Editor editor = new Editor
{
    Text = "This text will be displayed in uppercase.",
    TextTransform = TextTransform.Uppercase
};
```

## <a name="limit-input-length"></a>Limitar comprimento de entrada

A [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) propriedade pode ser usada para limitar o comprimento de entrada permitido para o [`Editor`](xref:Xamarin.Forms.Editor) . Essa propriedade deve ser definida como um inteiro positivo:

```xaml
<Editor ... MaxLength="10" />
```

```csharp
var editor = new Editor { ... MaxLength = 10 };
```

Um [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) valor de propriedade 0 indica que nenhuma entrada será permitida e um valor de `int.MaxValue` , que é o valor padrão de um [`Editor`](xref:Xamarin.Forms.Editor) , indica que não há um limite efetivo no número de caracteres que podem ser inseridos.

## <a name="character-spacing"></a>Espaçamento de caracteres

O espaçamento de caracteres pode ser aplicado a um [`Editor`](xref:Xamarin.Forms.Editor) definindo a `Editor.CharacterSpacing` propriedade como um `double` valor:

```xaml
<Editor ...
        CharacterSpacing="10" />
```

Este é o código C# equivalente:

```csharp
Editor editor = new editor { CharacterSpacing = 10 };
```

O resultado é que os caracteres no texto exibidos pelas [`Editor`](xref:Xamarin.Forms.Editor) unidades independentes de dispositivo espaçadas são `CharacterSpacing` separadas.

> [!NOTE]
> O `CharacterSpacing` valor da propriedade é aplicado ao texto exibido pelas `Text` Propriedades e `Placeholder` .

## <a name="auto-size-an-editor"></a>Dimensionar automaticamente um editor

É [`Editor`](xref:Xamarin.Forms.Editor) possível fazer o dimensionamento automático para seu conteúdo definindo a [`Editor.AutoSize`](xref:Xamarin.Forms.Editor.AutoSize) propriedade como [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges) , que é um valor da [`EditorAutoSizeOption`](xref:Xamarin.Forms.EditorAutoSizeOption) enumeração. Essa enumeração tem dois valores:

- [`Disabled`](xref:Xamarin.Forms.EditorAutoSizeOption.Disabled) indica que o redimensionamento automático está desabilitado e é o valor padrão.
- [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges) indica que o redimensionamento automático está habilitado.

Isso pode ser feito no código da seguinte maneira:

```xaml
<Editor Text="Enter text here" AutoSize="TextChanges" />
```

```csharp
var editor = new Editor { Text = "Enter text here", AutoSize = EditorAutoSizeOption.TextChanges };
```

Quando o redimensionamento automático estiver habilitado, a altura do [`Editor`](xref:Xamarin.Forms.Editor) será aumentada quando o usuário o preencher com texto e a altura diminuirá à medida que o usuário excluir o texto.

> [!NOTE]
> Um [`Editor`](xref:Xamarin.Forms.Editor) não será dimensionado automaticamente se a [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) propriedade tiver sido definida.

## <a name="customize-the-keyboard"></a>Personalizar o teclado

O teclado apresentado quando os usuários interagem com um [`Editor`](xref:Xamarin.Forms.Editor) pode ser definido programaticamente por meio da [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) propriedade, para uma das seguintes propriedades da [`Keyboard`](xref:Xamarin.Forms.Keyboard) classe:

- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat) – usado para texto e locais em que emojis são úteis.
- [`Default`](xref:Xamarin.Forms.Keyboard.Default) – o teclado padrão.
- [`Email`](xref:Xamarin.Forms.Keyboard.Email) – usado ao inserir endereços de email.
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric) – usado ao inserir números.
- [`Plain`](xref:Xamarin.Forms.Keyboard.Plain) – usado ao inserir texto, sem nenhum [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) especificado.
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone) – usado ao inserir números de telefone.
- [`Text`](xref:Xamarin.Forms.Keyboard.Text) – usado ao inserir texto.
- [`Url`](xref:Xamarin.Forms.Keyboard.Url) – usado para inserir caminhos de arquivo & endereços da Web.

Isso pode ser feito no XAML da seguinte maneira:

```xaml
<Editor Keyboard="Chat" />
```

Este é o código C# equivalente:

```csharp
var editor = new Editor { Keyboard = Keyboard.Chat };
```

Exemplos de cada teclado podem ser encontrados em nosso repositório de [receitas](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/choose-keyboard-for-entry) .

A [`Keyboard`](xref:Xamarin.Forms.Keyboard) classe também tem um [`Create`](xref:Xamarin.Forms.Keyboard.Create*) método de fábrica que pode ser usado para personalizar um teclado especificando a capitalização, a verificação ortográfica e o comportamento de sugestão. [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) os valores de enumeração são especificados como argumentos para o método, com um `Keyboard` retorno personalizado sendo retornado. A enumeração `KeyboardFlags` contém os seguintes valores:

- [`None`](xref:Xamarin.Forms.KeyboardFlags.None) – nenhum recurso é adicionado ao teclado.
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence) – indica que a primeira letra da primeira palavra de cada sentença inserida será automaticamente capitalizada.
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck) – indica que a verificação ortográfica será executada no texto inserido.
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions) – indica que as conclusões do Word serão oferecidas no texto inserido.
- [`CapitalizeWord`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeWord) – indica que a primeira letra de cada palavra será automaticamente capitalizada.
- [`CapitalizeCharacter`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeCharacter) – indica que cada caractere será colocado em maiúscula automaticamente.
- [`CapitalizeNone`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeNone) – indica que nenhuma capitalização automática ocorrerá.
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All) – indica que a verificação ortográfica, as preenchimentos de palavras e a capitalização de frase ocorrerão no texto inserido.

O exemplo de código XAML a seguir mostra como personalizar o padrão [`Keyboard`](xref:Xamarin.Forms.Keyboard) para oferecer preenchimentos de palavras e colocar todos os caracteres inseridos em maiúsculas:

```xaml
<Editor>
    <Editor.Keyboard>
        <Keyboard x:FactoryMethod="Create">
            <x:Arguments>
                <KeyboardFlags>Suggestions,CapitalizeCharacter</KeyboardFlags>
            </x:Arguments>
        </Keyboard>
    </Editor.Keyboard>
</Editor>
```

Este é o código C# equivalente:

```csharp
var editor = new Editor();
editor.Keyboard = Keyboard.Create(KeyboardFlags.Suggestions | KeyboardFlags.CapitalizeCharacter);
```

## <a name="enable-and-disable-spell-checking"></a>Habilitar e desabilitar a verificação ortográfica

A [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propriedade controla se a verificação ortográfica está habilitada. Por padrão, a propriedade é definida como `true` . Conforme o usuário digita o texto, erros de ortografia são indicados.

No entanto, para alguns cenários de entrada de texto, como inserir um nome de usuário, a verificação ortográfica fornece uma experiência negativa e, portanto, deve ser desabilitada definindo a [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propriedade como `false` :

```xaml
<Editor ... IsSpellCheckEnabled="false" />
```

```csharp
var editor = new Editor { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> Quando a [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propriedade é definida como `false` e um teclado personalizado não está sendo usado, o verificador ortográfico nativo será desabilitado. No entanto, se um [`Keyboard`](xref:Xamarin.Forms.Keyboard) tiver sido definido que desabilita a verificação ortográfica, como [`Keyboard.Chat`](xref:Xamarin.Forms.Keyboard.Chat) , a `IsSpellCheckEnabled` propriedade será ignorada. Portanto, a propriedade não pode ser usada para habilitar a verificação ortográfica para um `Keyboard` que a desabilita explicitamente.

## <a name="enable-and-disable-text-prediction"></a>Habilitar e desabilitar a previsão de texto

A `IsTextPredictionEnabled` propriedade controla se a previsão de texto e a correção automática de texto estão habilitadas. Por padrão, a propriedade é definida como `true` . À medida que o usuário insere texto, as previsões de palavras são apresentadas.

No entanto, para alguns cenários de entrada de texto, como inserir um nome de usuário, a previsão de texto e a correção automática de texto fornecem uma experiência negativa e devem ser desabilitadas definindo a `IsTextPredictionEnabled` propriedade como `false` :

```xaml
<Editor ... IsTextPredictionEnabled="false" />
```

```csharp
var editor = new Editor { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> Quando a `IsTextPredictionEnabled` propriedade é definida como `false` , e um teclado personalizado não está sendo usado, a previsão de texto e a correção automática de texto são desabilitadas. No entanto, se um [`Keyboard`](xref:Xamarin.Forms.Keyboard) tiver sido definido que desabilita a previsão de texto, a `IsTextPredictionEnabled` propriedade será ignorada. Portanto, a propriedade não pode ser usada para habilitar a previsão de texto para um `Keyboard` que a desabilita explicitamente.

## <a name="colors"></a>Cores

`Editor` pode ser definido para usar uma cor de plano de fundo personalizada por meio da `BackgroundColor` propriedade. É necessário ter cuidado especial para garantir que as cores sejam utilizáveis em cada plataforma. Como cada plataforma tem padrões diferentes para a cor do texto, talvez seja necessário definir uma cor de plano de fundo personalizada para cada plataforma. Consulte [trabalhando com ajustes de plataforma](~/xamarin-forms/platform/device.md) para obter mais informações sobre como otimizar a interface do usuário para cada plataforma.

No C#:

```csharp
public partial class EditorPage : ContentPage
{
    public EditorPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        //dark blue on UWP & Android, light blue on iOS
        var editor = new Editor { BackgroundColor = Device.RuntimePlatform == Device.iOS ? Color.FromHex("#A4EAFF") : Color.FromHex("#2c3e50") };
        layout.Children.Add(editor);
    }
}
```

Em XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    x:Class="TextSample.EditorPage"
    Title="Editor Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
            <Editor>
                <Editor.BackgroundColor>
                    <OnPlatform x:TypeArguments="x:Color">
                        <On Platform="iOS" Value="#a4eaff" />
                        <On Platform="Android, UWP" Value="#2c3e50" />
                    </OnPlatform>
                </Editor.BackgroundColor>
            </Editor>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

![Editor com exemplo BackgroundColor](editor-images/textbackgroundcolor.png)

Certifique-se de que as cores de plano de fundo e de texto escolhidas sejam utilizáveis em cada plataforma e não oculte nenhum texto de espaço reservado.

## <a name="events-and-interactivity"></a>Eventos e interatividade

`Editor` expõe dois eventos:

- [TextChanged](xref:Xamarin.Forms.InputView.TextChanged) &ndash; gerado quando o texto é alterado no editor. Fornece o texto antes e depois da alteração.
- [Concluído](xref:Xamarin.Forms.Editor.Completed) &ndash; gerado quando o usuário termina a entrada pressionando a tecla de retorno no teclado.

> [!NOTE]
> A [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe, da qual [`Entry`](xref:Xamarin.Forms.Entry) herda, também tem [`Focused`](xref:Xamarin.Forms.VisualElement.Focused) [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused) eventos e.

### <a name="completed"></a>Concluído

O `Completed` evento é usado para reagir à conclusão de uma interação com um `Editor` . `Completed` é gerado quando o usuário termina a entrada com um campo digitando a tecla de retorno no teclado (ou pressionando a tecla Tab no UWP). O manipulador para o evento é um manipulador de eventos genérico, levando o remetente e `EventArgs` :

```csharp
void EditorCompleted (object sender, EventArgs e)
{
    var text = ((Editor)sender).Text; // sender is cast to an Editor to enable reading the `Text` property of the view.
}
```

O evento Completed pode ser assinado no código e no XAML:

No C#:

```csharp
public partial class EditorPage : ContentPage
{
    public EditorPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        var editor = new Editor ();
        editor.Completed += EditorCompleted;
        layout.Children.Add(editor);
    }
}
```

Em XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TextSample.EditorPage"
Title="Editor Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
            <Editor Completed="EditorCompleted" />
        </StackLayout>
    </ContentPage.Content>
</Contentpage>
```

### <a name="textchanged"></a>TextChanged

O `TextChanged` evento é usado para reagir a uma alteração no conteúdo de um campo.

`TextChanged` é gerado sempre que o `Text` das `Editor` alterações. O manipulador para o evento usa uma instância do `TextChangedEventArgs` . `TextChangedEventArgs` fornece acesso aos valores novos e antigos de `Editor` `Text` pelas `OldTextValue` `NewTextValue` Propriedades e:

```csharp
void EditorTextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

O evento Completed pode ser assinado no código e no XAML:

No código:

```csharp
public partial class EditorPage : ContentPage
{
    public EditorPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        var editor = new Editor ();
        editor.TextChanged += EditorTextChanged;
        layout.Children.Add(editor);
    }
}
```

Em XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TextSample.EditorPage"
Title="Editor Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
            <Editor TextChanged="EditorTextChanged" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

## <a name="related-links"></a>Links relacionados

- [Texto (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [API de Editor](xref:Xamarin.Forms.Editor)