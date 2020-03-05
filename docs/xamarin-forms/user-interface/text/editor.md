---
title: Editor do xamarin. Forms
description: Este artigo explica como usar o controle de Editor do xamarin. Forms para aceitar a entrada de texto de várias linhas em um aplicativo.
ms.prod: xamarin
ms.assetid: 7074DB3A-30D2-4A6B-9A89-B029EEF20B07
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/26/2019
ms.openlocfilehash: 1ae176cfebdde31038c30895d1bf562ff3396eaa
ms.sourcegitcommit: ccbf914615c0ce6b3f308d930f7a77418aeb4dbc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2020
ms.locfileid: "78292392"
---
# <a name="xamarinforms-editor"></a>Editor do xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Entrada de texto de várias linhas_

O controle [`Editor`](xref:Xamarin.Forms.Editor) é usado para aceitar a entrada de várias linhas. Este artigo cobre:

- **[Personalização](#customization)** &ndash; opções de teclado e cor.
- Eventos de &ndash; **[interatividade](#interactivity)** que podem ser escutados para fornecer interatividade.

## <a name="customization"></a>Personalização

### <a name="setting-and-reading-text"></a>Definir e ler texto

A [`Editor`](xref:Xamarin.Forms.Editor), como outras exibições de apresentação de texto, expõe a propriedade `Text`. Essa propriedade pode ser usada para definir e ler o texto apresentado pelo `Editor`. O exemplo a seguir demonstra como definir a propriedade `Text` em XAML:

```xaml
<Editor Text="I am an Editor" />
```

No C#:

```csharp
var MyEditor = new Editor { Text = "I am an Editor" };
```

Para ler o texto, acesse a propriedade C#`Text` em:

```csharp
var text = MyEditor.Text;
```

### <a name="setting-placeholder-text"></a>Definir o texto de espaço reservado

O [`Editor`](xref:Xamarin.Forms.Editor) pode ser definido para mostrar o texto do espaço reservado quando não estiver armazenando a entrada do usuário. Isso é feito definindo a propriedade [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) como um `string`e é geralmente usado para indicar o tipo de conteúdo apropriado para o `Editor`. Além disso, a cor do texto do espaço reservado pode ser controlada definindo a propriedade [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor) como uma [`Color`](xref:Xamarin.Forms.Color):

```xaml
<Editor Placeholder="Enter text here" PlaceholderColor="Olive" />
```

```csharp
var editor = new Editor { Placeholder = "Enter text here", PlaceholderColor = Color.Olive };
```

### <a name="preventing-text-entry"></a>Impedindo a entrada de texto

Os usuários podem ser impedidos de modificar o texto em um [`Editor`](xref:Xamarin.Forms.Editor) definindo a propriedade `IsReadOnly`, que tem um valor padrão de `false`, para `true`:

```xaml
<Editor Text="This is a read-only Editor"
        IsReadOnly="true" />
```

```csharp
var editor = new Editor { Text = "This is a read-only Editor", IsReadOnly = true });
```

> [!NOTE]
> A propriedade `IsReadonly` não altera a aparência visual de um [`Editor`](xref:Xamarin.Forms.Editor), ao contrário da propriedade `IsEnabled`, que também altera a aparência visual do `Editor` para cinza.

### <a name="limiting-input-length"></a>Limitação de tamanho de entrada

A propriedade [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) pode ser usada para limitar o comprimento de entrada permitido para o [`Editor`](xref:Xamarin.Forms.Editor). Essa propriedade deve ser definida como um inteiro positivo:

```xaml
<Editor ... MaxLength="10" />
```

```csharp
var editor = new Editor { ... MaxLength = 10 };
```

Um valor de propriedade de [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) de 0 indica que nenhuma entrada será permitida e um valor de `int.MaxValue`, que é o valor padrão de um [`Editor`](xref:Xamarin.Forms.Editor), indica que não há nenhum limite efetivo no número de caracteres que podem ser inseridos.

### <a name="character-spacing"></a>Espaçamento de caracteres

O espaçamento de caracteres pode ser aplicado a um [`Editor`](xref:Xamarin.Forms.Editor) definindo a propriedade `Editor.CharacterSpacing` como um valor `double`:

```xaml
<Editor ...
        CharacterSpacing="10" />
```

Este é o código C# equivalente:

```csharp
Editor editor = new editor { CharacterSpacing = 10 };
```

O resultado é que os caracteres no texto exibido pelo [`Editor`](xref:Xamarin.Forms.Editor) são espaçados `CharacterSpacing` unidades independentes de dispositivo.

> [!NOTE]
> O valor da propriedade `CharacterSpacing` é aplicado ao texto exibido pelas propriedades `Text` e `Placeholder`.

### <a name="auto-sizing-an-editor"></a>Um Editor de dimensionamento automático

Uma [`Editor`](xref:Xamarin.Forms.Editor) pode ser feita para o dimensionamento automático para seu conteúdo definindo a propriedade [`Editor.AutoSize`](xref:Xamarin.Forms.Editor.AutoSize) como [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges), que é um valor da enumeração de [`EditoAutoSizeOption`](xref:Xamarin.Forms.EditorAutoSizeOption) . Esta enumeração tem dois valores:

- [`Disabled`](xref:Xamarin.Forms.EditorAutoSizeOption.Disabled) indica que o redimensionamento automático está desabilitado e é o valor padrão.
- [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges) indica que o redimensionamento automático está habilitado.

Isso pode ser feito no código da seguinte maneira:

```xaml
<Editor Text="Enter text here" AutoSize="TextChanges" />
```

```csharp
var editor = new Editor { Text = "Enter text here", AutoSize = EditorAutoSizeOption.TextChanges };
```

Quando o redimensionamento automático estiver habilitado, a altura do [`Editor`](xref:Xamarin.Forms.Editor) aumentará quando o usuário o preencher com texto, e a altura diminuirá à medida que o usuário excluir o texto.

> [!NOTE]
> Um [`Editor`](xref:Xamarin.Forms.Editor) não será automaticamente redimensionado se a propriedade [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) tiver sido definida.

### <a name="customizing-the-keyboard"></a>Personalizando o teclado

O teclado que é apresentado quando os usuários interagem com um [`Editor`](xref:Xamarin.Forms.Editor) pode ser definido programaticamente por meio da propriedade [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) , para uma das seguintes propriedades da classe [`Keyboard`](xref:Xamarin.Forms.Keyboard) :

- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat) – usado para mensagens de texto e lugares em que os emojis são úteis.
- [`Default`](xref:Xamarin.Forms.Keyboard.Default) – o teclado padrão.
- [`Email`](xref:Xamarin.Forms.Keyboard.Email) – usado ao inserir endereços de email.
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric) – usado ao inserir números.
- [`Plain`](xref:Xamarin.Forms.Keyboard.Plain) – usado ao inserir texto, sem nenhum [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) especificado.
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone) – usado ao inserir números de telefone.
- [`Text`](xref:Xamarin.Forms.Keyboard.Text) – usado ao inserir texto.
- [`Url`](xref:Xamarin.Forms.Keyboard.Url) – usado para inserir caminhos de arquivos e endereços web.

Isso pode ser feito no XAML da seguinte maneira:

```xaml
<Editor Keyboard="Chat" />
```

Este é o código C# equivalente:

```csharp
var editor = new Editor { Keyboard = Keyboard.Chat };
```

Exemplos de cada teclado podem ser encontrados em nosso repositório de [receitas](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/choose-keyboard-for-entry) .

A classe [`Keyboard`](xref:Xamarin.Forms.Keyboard) também tem um método de fábrica [`Create`](xref:Xamarin.Forms.Keyboard.Create*) que pode ser usado para personalizar um teclado especificando o comportamento de capitalização, de verificação ortográfica e de sugestão. Os valores de enumeração [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) são especificados como argumentos para o método, e um `Keyboard` personalizado é retornado. A enumeração `KeyboardFlags` contém os seguintes valores:

- [`None`](xref:Xamarin.Forms.KeyboardFlags.None) – nenhum recurso é adicionado ao teclado.
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence) – indica que as primeiras letras das primeiras palavras de cada frase serão automaticamente maiúsculas.
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck) – indica que a verificação ortográfica será executada no texto digitado.
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions) – indica que os preenchimentos de palavra sugerida será realizado no texto digitado.
- [`CapitalizeWord`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeWord) – indica que a primeira letra de cada palavra será automaticamente maiúscula.
- [`CapitalizeCharacter`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeCharacter) – indica que todos os caracteres serão automaticamente colocados em maiúsculas.
- [`CapitalizeNone`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeNone) – indica que não ocorrerá nenhuma capitalização automática ocorrerá.
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All) – indica que a verificação ortográfica, os preenchimentos de palavra sugerida e a capitalização de frases ocorrerão no texto digitado.

O seguinte exemplo de código XAML mostra como personalizar o [`Keyboard`](xref:Xamarin.Forms.Keyboard) padrão para oferecer preenchimentos de palavra sugerida e capitalizar todos os caracteres inseridos:

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

### <a name="enabling-and-disabling-spell-checking"></a>Habilitar e desabilitar a verificação ortográfica

A propriedade [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) controla se a verificação ortográfica está habilitada. Por padrão, a propriedade é definida como `true`. Enquanto o usuário insere texto, erros de ortografia são indicados.

No entanto, para alguns cenários de entrada de texto, como inserir um nome de usuário, a verificação ortográfica fornece uma experiência negativa e, portanto, deve ser desabilitada definindo a propriedade [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) como `false`:

```xaml
<Editor ... IsSpellCheckEnabled="false" />
```

```csharp
var editor = new Editor { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> Quando a propriedade [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) é definida como `false`e um teclado personalizado não está sendo usado, o verificador ortográfico nativo será desabilitado. No entanto, se um [`Keyboard`](xref:Xamarin.Forms.Keyboard) tiver sido definido que desabilita a verificação ortográfica, como [`Keyboard.Chat`](xref:Xamarin.Forms.Keyboard.Chat), a propriedade `IsSpellCheckEnabled` será ignorada. Portanto, a propriedade não pode ser usada para habilitar a verificação ortográfica de um `Keyboard` que o desabilita explicitamente.

### <a name="enabling-and-disabling-text-prediction"></a>Habilitando e desabilitando a previsão de texto

A propriedade `IsTextPredictionEnabled` controla se a previsão de texto e a correção automática de texto estão habilitadas. Por padrão, a propriedade é definida como `true`. Enquanto o usuário insere texto, previsões do word são apresentados.

No entanto, para alguns cenários de entrada de texto, como inserir um nome de usuário, a previsão de texto e a correção automática de texto fornecem uma experiência negativa e devem ser desabilitadas definindo a propriedade `IsTextPredictionEnabled` como `false`:

```xaml
<Editor ... IsTextPredictionEnabled="false" />
```

```csharp
var editor = new Editor { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> Quando a propriedade `IsTextPredictionEnabled` é definida como `false`e um teclado personalizado não está sendo usado, a previsão de texto e a correção automática de texto são desabilitadas. No entanto, se um [`Keyboard`](xref:Xamarin.Forms.Keyboard) tiver sido definido que desabilite a previsão de texto, a propriedade `IsTextPredictionEnabled` será ignorada. Portanto, a propriedade não pode ser usada para habilitar a previsão de texto para um `Keyboard` que o desabilita explicitamente.

### <a name="colors"></a>Cores

`Editor` pode ser definido para usar uma cor de plano de fundo personalizada por meio da propriedade `BackgroundColor`. Cuidado especial é necessário para garantir que as cores poderá ser usadas em cada plataforma. Como cada plataforma tem diferentes padrões de cor do texto, você talvez precise definir uma cor de plano de fundo personalizado para cada plataforma. Consulte [trabalhando com ajustes de plataforma](~/xamarin-forms/platform/device.md) para obter mais informações sobre como otimizar a interface do usuário para cada plataforma.

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

No XAML:

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

![](editor-images/textbackgroundcolor.png "Editor with BackgroundColor Example")

Certifique-se de que as cores de plano de fundo e texto que você escolher podem ser usados em cada plataforma e não ocultam qualquer texto de espaço reservado.

## <a name="interactivity"></a>Interatividade

`Editor` expõe dois eventos:

- [TextChanged](xref:Xamarin.Forms.InputView.TextChanged) &ndash; gerado quando o texto é alterado no editor. Fornece o texto antes e após a alteração.
- [Concluído](xref:Xamarin.Forms.Editor.Completed) &ndash; gerado quando o usuário finaliza a entrada pressionando a tecla de retorno no teclado.

> [!NOTE]
> A classe [`VisualElement`](xref:Xamarin.Forms.VisualElement) , da qual [`Entry`](xref:Xamarin.Forms.Entry) herda, também tem eventos [`Focused`](xref:Xamarin.Forms.VisualElement.Focused) e [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused) .

### <a name="completed"></a>Concluído

O evento `Completed` é usado para reagir à conclusão de uma interação com um `Editor`. `Completed` é gerado quando o usuário termina a entrada com um campo inserindo a chave de retorno no teclado (ou pressionando a tecla Tab no UWP). O manipulador para o evento é um manipulador de eventos genérico, levando o remetente e `EventArgs`:

```csharp
void EditorCompleted (object sender, EventArgs e)
{
    var text = ((Editor)sender).Text; // sender is cast to an Editor to enable reading the `Text` property of the view.
}
```

O evento concluído pode ser assinado no código e XAML:

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

No XAML:

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

O evento `TextChanged` é usado para reagir a uma alteração no conteúdo de um campo.

`TextChanged` é gerado sempre que o `Text` do `Editor` é alterado. O manipulador para o evento usa uma instância de `TextChangedEventArgs`. `TextChangedEventArgs` fornece acesso aos valores novos e antigos da `Text` de `Editor` por meio das propriedades `OldTextValue` e `NewTextValue`:

```csharp
void EditorTextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

O evento concluído pode ser assinado no código e XAML:

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

No XAML:

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

- [Texto (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [API de Editor](xref:Xamarin.Forms.Editor)
