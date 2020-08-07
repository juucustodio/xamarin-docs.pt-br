---
title: Xamarin.FormsInicial
description: Este artigo explica como usar a Xamarin.Forms classe de entrada para aceitar entrada de texto ou senha de linha única em um aplicativo.
ms.prod: xamarin
ms.assetid: 9923C541-3C10-4D14-BAB5-C4D6C514FB1E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/21/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3738a0fa3519f18864c2430430a6716bed5be130
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87918223"
---
# <a name="no-locxamarinforms-entry"></a>Xamarin.FormsInicial

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

O Xamarin.Forms [`Entry`](xref:Xamarin.Forms.Entry) é usado para entrada de texto de linha única. O `Entry` , como a [`Editor`](xref:Xamarin.Forms.Editor) exibição, dá suporte a vários tipos de teclado. Além disso, o `Entry` pode ser usado como um campo de senha.

## <a name="set-and-read-text"></a>Definir e ler texto

O `Entry` , como outras exibições de apresentação de texto, expõe a [`Text`](xref:Xamarin.Forms.InputView.Text) propriedade. Essa propriedade pode ser usada para definir e ler o texto apresentado pelo `Entry` . O exemplo a seguir demonstra como definir a `Text` propriedade em XAML:

```xaml
<Entry Text="I am an Entry" />
```

No C#:

```csharp
var MyEntry = new Entry { Text = "I am an Entry" };
```

Para ler o texto, acesse a `Text` propriedade em C#:

```csharp
var text = MyEntry.Text;
```

## <a name="set-placeholder-text"></a>Definir texto do espaço reservado

O [`Entry`](xref:Xamarin.Forms.Entry) pode ser definido para mostrar o texto do espaço reservado quando não estiver armazenando a entrada do usuário. Isso é feito definindo a [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) propriedade como a `string` , e geralmente é usado para indicar o tipo de conteúdo apropriado para o `Entry` . Além disso, a cor do texto do espaço reservado pode ser controlada definindo a [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor) propriedade como a [`Color`](xref:Xamarin.Forms.Color) :

```xaml
<Entry Placeholder="Username" PlaceholderColor="Olive" />
```

```csharp
var entry = new Entry { Placeholder = "Username", PlaceholderColor = Color.Olive };
```

> [!NOTE]
> A largura de um `Entry` pode ser definida definindo sua `WidthRequest` propriedade. Não depende da largura de um `Entry` que está sendo definido com base no valor de sua `Text` propriedade.

## <a name="prevent-text-entry"></a>Impedir entrada de texto

Os usuários podem ser impedidos de modificar o texto em um [`Entry`](xref:Xamarin.Forms.Entry) definindo a `IsReadOnly` propriedade, que tem um valor padrão de `false` , para `true` :

```xaml
<Entry Text="This is a read-only Entry"
       IsReadOnly="true" />
```

```csharp
var entry = new Entry { Text = "This is a read-only Entry", IsReadOnly = true });
```

> [!NOTE]
> A `IsReadonly` propriedade não altera a aparência visual de um [`Entry`](xref:Xamarin.Forms.Entry) , diferente da `IsEnabled` propriedade que também altera a aparência visual do `Entry` para cinza.

## <a name="transform-text"></a>Texto de transformação

Um [`Entry`](xref:Xamarin.Forms.Entry) pode transformar a capitalização de seu texto, armazenado na `Text` propriedade, definindo a `TextTransform` propriedade como um valor da `TextTransform` enumeração. Essa enumeração tem quatro valores:

- `None`indica que o texto não será transformado.
- `Default`indica que o comportamento padrão para a plataforma será usado. Este é o valor padrão da propriedade `TextTransform`.
- `Lowercase`indica que o texto será transformado em minúsculas.
- `Uppercase`indica que o texto será transformado em letras maiúsculas.

O exemplo a seguir mostra a transformação de texto em maiúsculas:

```xaml
<Entry Text="This text will be displayed in uppercase."
       TextTransform="Uppercase" />
```

Este é o código C# equivalente:

```csharp
Entry entry = new Entry
{
    Text = "This text will be displayed in uppercase.",
    TextTransform = TextTransform.Uppercase
};
```

## <a name="limit-input-length"></a>Limitar comprimento de entrada

A [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) propriedade pode ser usada para limitar o comprimento de entrada permitido para o [`Entry`](xref:Xamarin.Forms.Entry) . Essa propriedade deve ser definida como um inteiro positivo:

```xaml
<Entry ... MaxLength="10" />
```

```csharp
var entry = new Entry { ... MaxLength = 10 };
```

Um [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) valor de propriedade 0 indica que nenhuma entrada será permitida e um valor de `int.MaxValue` , que é o valor padrão de um [`Entry`](xref:Xamarin.Forms.Entry) , indica que não há um limite efetivo no número de caracteres que podem ser inseridos.

## <a name="character-spacing"></a>Espaçamento de caracteres

O espaçamento de caracteres pode ser aplicado a um [`Entry`](xref:Xamarin.Forms.Entry) definindo a `Entry.CharacterSpacing` propriedade como um `double` valor:

```xaml
<Entry ...
       CharacterSpacing="10" />
```

Este é o código C# equivalente:

```csharp
Entry entry = new Entry { CharacterSpacing = 10 };
```

O resultado é que os caracteres no texto exibidos pelas [`Entry`](xref:Xamarin.Forms.Entry) unidades independentes de dispositivo espaçadas são `CharacterSpacing` separadas.

> [!NOTE]
> O `CharacterSpacing` valor da propriedade é aplicado ao texto exibido pelas `Text` Propriedades e `Placeholder` .

## <a name="password-fields"></a>Campos de senha

`Entry`fornece a `IsPassword` propriedade. Quando `IsPassword` for `true` , o conteúdo do campo será apresentado como círculos pretos:

Em XAML:

```xaml
<Entry IsPassword="true" />
```

No C#:

```csharp
var MyEntry = new Entry { IsPassword = true };
```

![Exemplo de IsPassword de entrada](entry-images/password.png)

Os espaços reservados podem ser usados com instâncias do `Entry` que são configuradas como campos de senha:

Em XAML:

```xaml
<Entry IsPassword="true" Placeholder="Password" />
```

No C#:

```csharp
var MyEntry = new Entry { IsPassword = true, Placeholder = "Password" };
```

![Exemplo de IsPassword e PlaceHolder de entrada](entry-images/passwordplaceholder.png)

## <a name="set-the-cursor-position-and-text-selection-length"></a>Definir a posição do cursor e o comprimento da seleção de texto

A [`CursorPosition`](xref:Xamarin.Forms.Entry.CursorPosition) propriedade pode ser usada para retornar ou definir a posição na qual o próximo caractere será inserido na cadeia de caracteres armazenada na [`Text`](xref:Xamarin.Forms.InputView.Text) Propriedade:

```xaml
<Entry Text="Cursor position set" CursorPosition="5" />
```

```csharp
var entry = new Entry { Text = "Cursor position set", CursorPosition = 5 };
```

O valor padrão da [`CursorPosition`](xref:Xamarin.Forms.Entry.CursorPosition) propriedade é 0, o que indica que o texto será inserido no início do `Entry` .

Além disso, a [`SelectionLength`](xref:Xamarin.Forms.Entry.SelectionLength) propriedade pode ser usada para retornar ou definir o comprimento da seleção de texto dentro de `Entry` :

```xaml
<Entry Text="Cursor position and selection length set" CursorPosition="2" SelectionLength="10" />
```

```csharp
var entry = new Entry { Text = "Cursor position and selection length set", CursorPosition = 2, SelectionLength = 10 };
```

O valor padrão da [`SelectionLength`](xref:Xamarin.Forms.Entry.SelectionLength) propriedade é 0, o que indica que nenhum texto está selecionado.

## <a name="display-a-clear-button"></a>Exibir um botão limpar

A `ClearButtonVisibility` propriedade pode ser usada para controlar se um [`Entry`](xref:Xamarin.Forms.Entry) exibe um botão limpar, que permite ao usuário limpar o texto. Essa propriedade deve ser definida como um `ClearButtonVisibility` membro de enumeração:

- `Never`indica que um botão limpar nunca será exibido. Este é o valor padrão para a propriedade de `Entry.ClearButtonVisibility` .
- `WhileEditing`indica que um botão limpar será exibido no [`Entry`](xref:Xamarin.Forms.Entry) , enquanto ele tem o foco e o texto.

O exemplo a seguir mostra a definição da propriedade em XAML:

```xaml
<Entry Text="Xamarin.Forms"
       ClearButtonVisibility="WhileEditing" />
```

Este é o código C# equivalente:

```csharp
var entry = new Entry { Text = "Xamarin.Forms", ClearButtonVisibility = ClearButtonVisibility.WhileEditing };
```

As capturas de tela a seguir mostram um [`Entry`](xref:Xamarin.Forms.Entry) com o botão limpar habilitado:

![Captura de tela de uma entrada com um botão limpar, no iOS e no Android](entry-images/entry-clear-button.png)

## <a name="customize-the-keyboard"></a>Personalizar o teclado

O teclado apresentado quando os usuários interagem com um [`Entry`](xref:Xamarin.Forms.Entry) pode ser definido programaticamente por meio da [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) propriedade, para uma das seguintes propriedades da [`Keyboard`](xref:Xamarin.Forms.Keyboard) classe:

- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat)– usado para texto e locais em que emojis são úteis.
- [`Default`](xref:Xamarin.Forms.Keyboard.Default)– o teclado padrão.
- [`Email`](xref:Xamarin.Forms.Keyboard.Email)– usado ao inserir endereços de email.
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric)– usado ao inserir números.
- [`Plain`](xref:Xamarin.Forms.Keyboard.Plain)– usado ao inserir texto, sem nenhum [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) especificado.
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone)– usado ao inserir números de telefone.
- [`Text`](xref:Xamarin.Forms.Keyboard.Text)– usado ao inserir texto.
- [`Url`](xref:Xamarin.Forms.Keyboard.Url)– usado para inserir caminhos de arquivo & endereços da Web.

Isso pode ser feito no XAML da seguinte maneira:

```xaml
<Entry Keyboard="Chat" />
```

Este é o código C# equivalente:

```csharp
var entry = new Entry { Keyboard = Keyboard.Chat };
```

Exemplos de cada teclado podem ser encontrados em nosso repositório de [receitas](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/choose-keyboard-for-entry) .

A [`Keyboard`](xref:Xamarin.Forms.Keyboard) classe também tem um [`Create`](xref:Xamarin.Forms.Keyboard.Create*) método de fábrica que pode ser usado para personalizar um teclado especificando a capitalização, a verificação ortográfica e o comportamento de sugestão. [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags)os valores de enumeração são especificados como argumentos para o método, com um `Keyboard` retorno personalizado sendo retornado. A enumeração `KeyboardFlags` contém os seguintes valores:

- [`None`](xref:Xamarin.Forms.KeyboardFlags.None)– nenhum recurso é adicionado ao teclado.
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence)– indica que a primeira letra da primeira palavra de cada sentença inserida será automaticamente capitalizada.
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck)– indica que a verificação ortográfica será executada no texto inserido.
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions)– indica que as conclusões do Word serão oferecidas no texto inserido.
- [`CapitalizeWord`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeWord)– indica que a primeira letra de cada palavra será automaticamente capitalizada.
- [`CapitalizeCharacter`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeCharacter)– indica que cada caractere será colocado em maiúscula automaticamente.
- [`CapitalizeNone`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeNone)– indica que nenhuma capitalização automática ocorrerá.
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All)– indica que a verificação ortográfica, as preenchimentos de palavras e a capitalização de frase ocorrerão no texto inserido.

O exemplo de código XAML a seguir mostra como personalizar o padrão [`Keyboard`](xref:Xamarin.Forms.Keyboard) para oferecer preenchimentos de palavras e colocar todos os caracteres inseridos em maiúsculas:

```xaml
<Entry Placeholder="Enter text here">
    <Entry.Keyboard>
        <Keyboard x:FactoryMethod="Create">
            <x:Arguments>
                <KeyboardFlags>Suggestions,CapitalizeCharacter</KeyboardFlags>
            </x:Arguments>
        </Keyboard>
    </Entry.Keyboard>
</Entry>
```

Este é o código C# equivalente:

```csharp
var entry = new Entry { Placeholder = "Enter text here" };
entry.Keyboard = Keyboard.Create(KeyboardFlags.Suggestions | KeyboardFlags.CapitalizeCharacter);
```

### <a name="customize-the-return-key"></a>Personalizar a chave de retorno

A aparência da chave de retorno no teclado soft, que é exibida quando um [`Entry`](xref:Xamarin.Forms.Entry) tem foco, pode ser personalizada definindo a [`ReturnType`](xref:Xamarin.Forms.Entry.ReturnType) propriedade como um valor da [`ReturnType`](xref:Xamarin.Forms.ReturnType) enumeração:

- [`Default`](xref:Xamarin.Forms.ReturnType.Default)– indica que nenhuma chave de retorno específica é necessária e que o padrão de plataforma será usado.
- [`Done`](xref:Xamarin.Forms.ReturnType.Done)– indica uma chave de retorno "Done".
- [`Go`](xref:Xamarin.Forms.ReturnType.Go)– indica uma chave de retorno "Go".
- [`Next`](xref:Xamarin.Forms.ReturnType.Next)– indica uma "próxima" chave de retorno.
- [`Search`](xref:Xamarin.Forms.ReturnType.Search)– indica uma chave de retorno de "pesquisa".
- [`Send`](xref:Xamarin.Forms.ReturnType.Send)– indica uma chave de retorno de "envio".

O exemplo de XAML a seguir mostra como definir a chave de retorno:

```xaml
<Entry ReturnType="Send" />
```

Este é o código C# equivalente:

```csharp
var entry = new Entry { ReturnType = ReturnType.Send };
```

> [!NOTE]
> A aparência exata da chave de retorno depende da plataforma. No iOS, a chave de retorno é um botão baseado em texto. No entanto, nas plataformas Android e universal do Windows, a chave de retorno é um botão baseado em ícone.

Quando a tecla de retorno é pressionada, o [`Completed`](xref:Xamarin.Forms.Entry.Completed) evento é acionado e qualquer `ICommand` especificado pela [`ReturnCommand`](xref:Xamarin.Forms.Entry.ReturnCommand) propriedade é executado. Além disso, qualquer `object` especificado pela [`ReturnCommandParameter`](xref:Xamarin.Forms.Entry.ReturnCommandParameter) propriedade será passado para o `ICommand` como um parâmetro. Para obter mais informações sobre comandos, confira [A Interface de Comando](~/xamarin-forms/app-fundamentals/data-binding/commanding.md).

## <a name="enable-and-disable-spell-checking"></a>Habilitar e desabilitar a verificação ortográfica

A [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propriedade controla se a verificação ortográfica está habilitada. Por padrão, a propriedade é definida como `true` . Conforme o usuário digita o texto, erros de ortografia são indicados.

No entanto, para alguns cenários de entrada de texto, como inserir um nome de usuário, a verificação ortográfica fornece uma experiência negativa e deve ser desabilitada definindo a [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propriedade como `false` :

```xaml
<Entry ... IsSpellCheckEnabled="false" />
```

```csharp
var entry = new Entry { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> Quando a [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propriedade é definida como `false` e um teclado personalizado não está sendo usado, o verificador ortográfico nativo será desabilitado. No entanto, se um [`Keyboard`](xref:Xamarin.Forms.Keyboard) tiver sido definido que desabilita a verificação ortográfica, como [`Keyboard.Chat`](xref:Xamarin.Forms.Keyboard.Chat) , a `IsSpellCheckEnabled` propriedade será ignorada. Portanto, a propriedade não pode ser usada para habilitar a verificação ortográfica para um `Keyboard` que a desabilita explicitamente.

## <a name="enable-and-disable-text-prediction"></a>Habilitar e desabilitar a previsão de texto

A [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) propriedade controla se a previsão de texto e a correção automática de texto estão habilitadas. Por padrão, a propriedade é definida como `true` . À medida que o usuário insere texto, as previsões de palavras são apresentadas.

No entanto, para alguns cenários de entrada de texto, como inserir um nome de usuário, a previsão de texto e a correção automática de texto fornecem uma experiência negativa e devem ser desabilitadas definindo a [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) propriedade como `false` :

```xaml
<Entry ... IsTextPredictionEnabled="false" />
```

```csharp
var entry = new Entry { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> Quando a [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) propriedade é definida como `false` , e um teclado personalizado não está sendo usado, a previsão de texto e a correção automática de texto são desabilitadas. No entanto, se um [`Keyboard`](xref:Xamarin.Forms.Keyboard) tiver sido definido que desabilita a previsão de texto, a `IsTextPredictionEnabled` propriedade será ignorada. Portanto, a propriedade não pode ser usada para habilitar a previsão de texto para um `Keyboard` que a desabilita explicitamente.

## <a name="colors"></a>Cores

A entrada pode ser definida para usar um plano de fundo personalizado e cores de texto por meio das seguintes propriedades vinculáveis:

- **TextColor** &ndash; define a cor do texto.
- **BackgroundColor** &ndash; define a cor mostrada atrás do texto.

É necessário ter cuidado especial para garantir que as cores sejam utilizáveis em cada plataforma. Como cada plataforma tem padrões diferentes para cores de texto e de plano de fundo, muitas vezes você precisará definir ambos se definir um.

Use o código a seguir para definir a cor do texto de uma entrada:

Em XAML:

```xaml
<Entry TextColor="Green" />
```

No C#:

```csharp
var entry = new Entry();
entry.TextColor = Color.Green;
```

![Exemplo de TextColor de entrada](entry-images/textcolor.png)

Observe que o espaço reservado não é afetado pelo especificado `TextColor` .

Para definir a cor do plano de fundo em XAML:

```xaml
<Entry BackgroundColor="#2c3e50" />
```

No C#:

```csharp
var entry = new Entry();
entry.BackgroundColor = Color.FromHex("#2c3e50");
```

![Exemplo de BackgroundColor de entrada](entry-images/textbackgroundcolor.png)

Tenha cuidado para certificar-se de que as cores de plano de fundo e de texto escolhidas sejam utilizáveis em cada plataforma e não oculte nenhum texto de espaço reservado.

## <a name="events-and-interactivity"></a>Eventos e interatividade

A entrada expõe dois eventos:

- [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged)&ndash;gerado quando o texto é alterado na entrada. Fornece o texto antes e depois da alteração.
- [`Completed`](xref:Xamarin.Forms.Entry.Completed)&ndash;gerado quando o usuário termina a entrada pressionando a tecla de retorno no teclado.

> [!NOTE]
> A [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe, da qual [`Entry`](xref:Xamarin.Forms.Entry) herda, também tem [`Focused`](xref:Xamarin.Forms.VisualElement.Focused) [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused) eventos e.

### <a name="completed"></a>Concluído

O `Completed` evento é usado para reagir à conclusão de uma interação com uma entrada. `Completed`é gerado quando o usuário termina a entrada com um campo pressionando a tecla de retorno no teclado (ou pressionando a tecla Tab no UWP). O manipulador para o evento é um manipulador de eventos genérico, levando o remetente e `EventArgs` :

```csharp
void Entry_Completed (object sender, EventArgs e)
{
    var text = ((Entry)sender).Text; //cast sender to access the properties of the Entry
}
```

O evento concluído pode ser assinado em XAML:

```xaml
<Entry Completed="Entry_Completed" />
```

e C#:

```csharp
var entry = new Entry ();
entry.Completed += Entry_Completed;
```

Depois que o [`Completed`](xref:Xamarin.Forms.Entry.Completed) evento é acionado, qualquer `ICommand` especificado pela [`ReturnCommand`](xref:Xamarin.Forms.Entry.ReturnCommand) propriedade é executado, com o `object` especificado pela [`ReturnCommandParameter`](xref:Xamarin.Forms.Entry.ReturnCommandParameter) propriedade que está sendo passada para o `ICommand` .

### <a name="textchanged"></a>TextChanged

O `TextChanged` evento é usado para reagir a uma alteração no conteúdo de um campo.

`TextChanged`é gerado sempre que o `Text` das `Entry` alterações. O manipulador para o evento usa uma instância do `TextChangedEventArgs` . `TextChangedEventArgs`fornece acesso aos valores novos e antigos de `Entry` `Text` pelas `OldTextValue` `NewTextValue` Propriedades e:

```csharp
void Entry_TextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

O `TextChanged` evento pode ser assinado em XAML:

```xaml
<Entry TextChanged="Entry_TextChanged" />
```

e C#:

```csharp
var entry = new Entry ();
entry.TextChanged += Entry_TextChanged;
```

## <a name="related-links"></a>Links Relacionados

- [Texto (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [API de Entrada](xref:Xamarin.Forms.Entry)
