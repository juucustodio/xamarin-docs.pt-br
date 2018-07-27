---
title: Editor do xamarin. Forms
description: Este artigo explica como usar o controle de Editor do xamarin. Forms para aceitar a entrada de texto de várias linhas em um aplicativo.
ms.prod: xamarin
ms.assetid: 7074DB3A-30D2-4A6B-9A89-B029EEF20B07
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/13/2018
ms.openlocfilehash: 6e3cf12431440823b1d32d91927bc634f60fd5e2
ms.sourcegitcommit: 46bb04016d3c35d91ff434b38474e0cb8197961b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2018
ms.locfileid: "39270450"
---
# <a name="xamarinforms-editor"></a>Editor do xamarin. Forms

_Entrada de texto de várias linhas_

O [ `Editor` ](xref:Xamarin.Forms.Editor) controle é usado para aceitar a entrada de várias linha. Este artigo aborda:

- **[Personalização](#customization)**  &ndash; opções de teclado e cor.
- **[Interatividade](#interactivity)**  &ndash; eventos que podem ser escutados para fornecer interatividade.

## <a name="customization"></a>Personalização

### <a name="setting-and-reading-text"></a>Definir e ler texto

O [ `Editor` ](xref:Xamarin.Forms.Editor), assim como outras exibições de apresentação de texto, expõe o `Text` propriedade. Essa propriedade pode ser usada para definir e ler o texto apresentado pelo `Editor`. O exemplo a seguir demonstra a configuração de `Text` propriedade em XAML:

```xaml
<Editor Text="I am an Editor" />
```

No C#:

```csharp
var MyEditor = new Editor { Text = "I am an Editor" };
```

Para ler o texto, acessar o `Text` propriedade em c#:

```csharp
var text = MyEditor.Text;
```

### <a name="limiting-input-length"></a>Limitação de tamanho de entrada

O [ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength) propriedade pode ser usada para limitar o tamanho de entrada é permitido para o [ `Editor` ](xref:Xamarin.Forms.Editor). Essa propriedade deve ser definida como um inteiro positivo:

```xaml
<Editor ... MaxLength="10" />
```

```csharp
var editor = new Editor { ... MaxLength = 10 };
```

Um [ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength) valor de propriedade de 0 indica que nenhuma entrada será permitida e um valor de `int.MaxValue`, que é o valor padrão para um [ `Editor` ](xref:Xamarin.Forms.Editor), indica que não há nenhum limite efetivo no número de caracteres que podem ser inseridos.

### <a name="auto-sizing-an-editor"></a>Um Editor de dimensionamento automático

Uma [ `Editor` ](xref:Xamarin.Forms.Editor) podem ser feitas para dimensionar automaticamente ao seu conteúdo, definindo o [ `Editor.AutoSize` ](xref:Xamarin.Forms.Editor.AutoSize) propriedade [ `TextChanges` ](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges), que é um valor da [ `EditoAutoSizeOption` ](xref:Xamarin.Forms.EditorAutoSizeOption) enumeração. Esta enumeração tem dois valores:

- [`Disabled`](xref:Xamarin.Forms.EditorAutoSizeOption.Disabled) indica que o redimensionamento automático está desabilitado e é o valor padrão.
- [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges) indica que o redimensionamento automático está habilitado.

Isso pode ser feito no código da seguinte maneira:

```xaml
<Editor Text="Enter text here" AutoSize="TextChanges" />
```

```csharp
var editor = new Editor { Text = "Enter text here", AutoSize = EditorAutoSizeOption.TextChanges };
```

Quando o redimensionamento automático está habilitado, a altura do [ `Editor` ](xref:Xamarin.Forms.Editor) aumentarão quando o usuário preenche com texto e a altura diminuirá conforme o usuário exclui texto.

> [!NOTE]
> Uma [ `Editor` ](xref:Xamarin.Forms.Editor) será tamanho automático não se o [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) propriedade foi definida.

### <a name="customizing-the-keyboard"></a>Personalizando o teclado

O teclado que é apresentado quando os usuários interagem com um [ `Editor` ](xref:Xamarin.Forms.Editor) podem ser definidas programaticamente por meio dos [ `Keyboard` ](xref:Xamarin.Forms.InputView.Keyboard) propriedade a uma das seguintes propriedades do [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) classe:

- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat) – usado para mandar um texto e locais em que o emoji são úteis.
- [`Default`](xref:Xamarin.Forms.Keyboard.Default) – o teclado padrão.
- [`Email`](xref:Xamarin.Forms.Keyboard.Email) – usado ao inserir endereços de email.
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric) – usado ao inserir números.
- [`Plain`](xref:Xamarin.Forms.Keyboard.Plain) – usado ao inserir texto, sem nenhuma [ `KeyboardFlags` ](xref:Xamarin.Forms.KeyboardFlags) especificado.
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone) – usado ao inserir números de telefone.
- [`Text`](xref:Xamarin.Forms.Keyboard.Text) – usado ao inserir texto.
- [`Url`](xref:Xamarin.Forms.Keyboard.Url) – usado para inserir caminhos de arquivo & endereços da web.

Isso pode ser feito no XAML da seguinte maneira:

```xaml
<Editor Keyboard="Chat" />
```

O código c# equivalente é:

```csharp
var editor = new Editor { Keyboard = Keyboard.Chat };
```

Exemplos de cada teclado podem ser encontrados em nossa [receitas](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/choose-keyboard-for-entry) repositório.

O [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) classe também tem uma [ `Create` ](xref:Xamarin.Forms.Keyboard.Create*) método de fábrica que pode ser usado para personalizar um teclado, especificando o comportamento de letras maiusculas, verificação ortográfica e sugestão. [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) valores de enumeração são especificados como argumentos para o método, com um personalizado `Keyboard` que está sendo retornado. O `KeyboardFlags` enumeração contém os seguintes valores:

- [`None`](xref:Xamarin.Forms.KeyboardFlags.None) – Não há recursos são adicionados ao teclado.
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence) – indica que a primeira letra da primeira palavra de cada frase inserida serão automaticamente maiusculas.
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck) – indica que essa verificação ortográfica será executada em texto digitado.
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions) – indica que o preenchimentos serão oferecidos em texto digitado.
- [`CapitalizeWord`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeWord) – indica que a primeira letra de cada palavra serão automaticamente maiusculas.
- [`CapitalizeCharacter`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeCharacter) – indica que todos os caracteres serão automaticamente maiusculas.
- [`CapitalizeNone`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeNone) – indica que nenhum capitalização automática ocorrerá.
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All) – indica que a verificação ortográfica, preenchimentos de palavra e capitalização de frase serão ocorrer em texto digitado.

O exemplo de código XAML a seguir mostra como personalizar o padrão [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) para oferecer preenchimentos de palavra e tirar proveito de todos os caracteres inseridos:

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

O código c# equivalente é:

```csharp
var editor = new Editor();
editor.Keyboard = Keyboard.Create(KeyboardFlags.Suggestions | KeyboardFlags.CapitalizeCharacter);
```

### <a name="enabling-and-disabling-spell-checking"></a>Habilitar e desabilitar a verificação ortográfica

O [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propriedade controla se verificação ortográfica está habilitada. Por padrão, a propriedade é definida como `true`. Enquanto o usuário insere texto, erros de ortografia são indicados.

No entanto, para alguns cenários de entrada de texto, como inserir um nome de usuário, verificação ortográfica fornece uma experiência negativa e, portanto, devem ser desabilitados definindo a [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propriedade `false`:

```xaml
<Editor ... IsSpellCheckEnabled="false" />
```

```csharp
var editor = new Editor { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> Quando o [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) estiver definida como `false`e um teclado personalizado não está sendo usado, o verificador ortográfico nativo será desabilitado. No entanto, se um [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) tem sido conjunto que desabilita ortográfica verificação, como [ `Keyboard.Chat` ](xref:Xamarin.Forms.Keyboard.Chat), o `IsSpellCheckEnabled` propriedade será ignorada. Portanto, a propriedade não pode ser usada para habilitar a verificação ortográfica para um `Keyboard` que desabilita explicitamente a ele.

### <a name="colors"></a>Cores

`Editor` pode ser definido para usar uma cor de plano de fundo personalizado por meio de `BackgroundColor` propriedade. Cuidado especial é necessário para garantir que as cores poderá ser usadas em cada plataforma. Como cada plataforma tem diferentes padrões de cor do texto, você talvez precise definir uma cor de plano de fundo personalizado para cada plataforma. Ver [trabalhando com ajustes de plataforma](~/xamarin-forms/platform/device.md) para obter mais informações sobre como otimizar a interface do usuário para cada plataforma.

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

![](editor-images/textbackgroundcolor.png "Editor de exemplo BackgroundColor")

Certifique-se de que as cores de plano de fundo e texto que você escolher podem ser usados em cada plataforma e não ocultam qualquer texto de espaço reservado.

## <a name="interactivity"></a>Interatividade

`Editor` expõe dois eventos:

- [TextChanged](xref:Xamarin.Forms.Editor.TextChanged) &ndash; gerado quando o texto é alterado no editor. Fornece o texto antes e após a alteração.
- [Concluída](xref:Xamarin.Forms.Editor.Completed) &ndash; gerado quando o usuário terminou entrada pressionando a tecla return no teclado.

### <a name="completed"></a>Concluído

O `Completed` evento é usado para responder à realização de uma interação com um `Editor`. `Completed` é gerado quando o usuário encerra a entrada com um campo, inserindo a tecla return no teclado. O manipulador para o evento é um manipulador de eventos genéricos, levando o remetente e `EventArgs`:

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

O `TextChanged` evento é usado para responder a uma alteração no conteúdo de um campo.

`TextChanged` é gerado sempre que o `Text` do `Editor` alterações. O manipulador para o evento usa uma instância de `TextChangedEventArgs`. `TextChangedEventArgs` fornece acesso aos valores novos e antigos do `Editor` `Text` por meio de `OldTextValue` e `NewTextValue` propriedades:

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

- [Texto (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [Editor de API](xref:Xamarin.Forms.Editor)
