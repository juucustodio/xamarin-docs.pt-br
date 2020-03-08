---
title: Entrada do xamarin. Forms
description: Este artigo explica como usar a classe de entrada do xamarin. Forms para aceitar a entrada de senha em um aplicativo ou de texto de linha única.
ms.prod: xamarin
ms.assetid: 9923C541-3C10-4D14-BAB5-C4D6C514FB1E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/25/2019
ms.openlocfilehash: c7daad8898d3048f10c9489ee4e2c78f147c6e52
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78914589"
---
# <a name="xamarinforms-entry"></a>Entrada do xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Entrada de texto ou senha de linha única_

O [`Entry`](xref:Xamarin.Forms.Entry) Xamarin. Forms é usado para entrada de texto de linha única. O `Entry`, como a exibição de [`Editor`](xref:Xamarin.Forms.Editor) , dá suporte a vários tipos de teclado. Além disso, o `Entry` pode ser usado como um campo de senha.

## <a name="display-customization"></a>Personalização da exibição

### <a name="setting-and-reading-text"></a>Definir e ler texto

A `Entry`, como outras exibições de apresentação de texto, expõe a propriedade [`Text`](xref:Xamarin.Forms.InputView.Text) . Essa propriedade pode ser usada para definir e ler o texto apresentado pelo `Entry`. O exemplo a seguir demonstra como definir a propriedade `Text` em XAML:

```xaml
<Entry Text="I am an Entry" />
```

No C#:

```csharp
var MyEntry = new Entry { Text = "I am an Entry" };
```

Para ler o texto, acesse a propriedade C#`Text` em:

```csharp
var text = MyEntry.Text;
```

### <a name="setting-placeholder-text"></a>Definir o texto de espaço reservado

O [`Entry`](xref:Xamarin.Forms.Entry) pode ser definido para mostrar o texto do espaço reservado quando não estiver armazenando a entrada do usuário. Isso é feito definindo a propriedade [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) como um `string`e é geralmente usado para indicar o tipo de conteúdo apropriado para o `Entry`. Além disso, a cor do texto do espaço reservado pode ser controlada definindo a propriedade [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor) como uma [`Color`](xref:Xamarin.Forms.Color):

```xaml
<Entry Placeholder="Username" PlaceholderColor="Olive" />
```

```csharp
var entry = new Entry { Placeholder = "Username", PlaceholderColor = Color.Olive };
```

> [!NOTE]
> A largura de um `Entry` pode ser definida definindo sua propriedade `WidthRequest`. Não dependem da largura de um `Entry` que está sendo definido com base no valor de sua propriedade `Text`.

### <a name="preventing-text-entry"></a>Impedindo a entrada de texto

Os usuários podem ser impedidos de modificar o texto em um [`Entry`](xref:Xamarin.Forms.Entry) definindo a propriedade `IsReadOnly`, que tem um valor padrão de `false`, para `true`:

```xaml
<Entry Text="This is a read-only Entry"
       IsReadOnly="true" />
```

```csharp
var entry = new Entry { Text = "This is a read-only Entry", IsReadOnly = true });
```

> [!NOTE]
> A propriedade `IsReadonly` não altera a aparência visual de um [`Entry`](xref:Xamarin.Forms.Entry), ao contrário da propriedade `IsEnabled`, que também altera a aparência visual do `Entry` para cinza.

### <a name="limiting-input-length"></a>Limitação de tamanho de entrada

A propriedade [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) pode ser usada para limitar o comprimento de entrada permitido para o [`Entry`](xref:Xamarin.Forms.Entry). Essa propriedade deve ser definida como um inteiro positivo:

```xaml
<Entry ... MaxLength="10" />
```

```csharp
var entry = new Entry { ... MaxLength = 10 };
```

Um valor de propriedade de [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) de 0 indica que nenhuma entrada será permitida e um valor de `int.MaxValue`, que é o valor padrão de um [`Entry`](xref:Xamarin.Forms.Entry), indica que não há nenhum limite efetivo no número de caracteres que podem ser inseridos.

### <a name="character-spacing"></a>Espaçamento de caracteres

O espaçamento de caracteres pode ser aplicado a um [`Entry`](xref:Xamarin.Forms.Entry) definindo a propriedade `Entry.CharacterSpacing` como um valor `double`:

```xaml
<Entry ...
       CharacterSpacing="10" />
```

Este é o código C# equivalente:

```csharp
Entry entry = new Entry { CharacterSpacing = 10 };
```

O resultado é que os caracteres no texto exibido pelo [`Entry`](xref:Xamarin.Forms.Entry) são espaçados `CharacterSpacing` unidades independentes de dispositivo.

> [!NOTE]
> O valor da propriedade `CharacterSpacing` é aplicado ao texto exibido pelas propriedades `Text` e `Placeholder`.

### <a name="password-fields"></a>Campos de senha

`Entry` fornece a propriedade `IsPassword`. Quando `IsPassword` for `true`, o conteúdo do campo será apresentado como círculos pretos:

No XAML:

```xaml
<Entry IsPassword="true" />
```

No C#:

```csharp
var MyEntry = new Entry { IsPassword = true };
```

![Exemplo de IsPassword de entrada](entry-images/password.png)

Os espaços reservados podem ser usados com instâncias de `Entry` configuradas como campos de senha:

No XAML:

```xaml
<Entry IsPassword="true" Placeholder="Password" />
```

No C#:

```csharp
var MyEntry = new Entry { IsPassword = true, Placeholder = "Password" };
```

![Exemplo de IsPassword e PlaceHolder de entrada](entry-images/passwordplaceholder.png)

### <a name="setting-the-cursor-position-and-text-selection-length"></a>Definindo a posição do Cursor e o comprimento da seleção de texto

A propriedade [`CursorPosition`](xref:Xamarin.Forms.Entry.CursorPosition) pode ser usada para retornar ou definir a posição na qual o próximo caractere será inserido na cadeia de caracteres armazenada na propriedade [`Text`](xref:Xamarin.Forms.InputView.Text) :

```xaml
<Entry Text="Cursor position set" CursorPosition="5" />
```

```csharp
var entry = new Entry { Text = "Cursor position set", CursorPosition = 5 };
```

O valor padrão da propriedade [`CursorPosition`](xref:Xamarin.Forms.Entry.CursorPosition) é 0, o que indica que o texto será inserido no início do `Entry`.

Além disso, a propriedade [`SelectionLength`](xref:Xamarin.Forms.Entry.SelectionLength) pode ser usada para retornar ou definir o comprimento da seleção de texto dentro do `Entry`:

```xaml
<Entry Text="Cursor position and selection length set" CursorPosition="2" SelectionLength="10" />
```

```csharp
var entry = new Entry { Text = "Cursor position and selection length set", CursorPosition = 2, SelectionLength = 10 };
```

O valor padrão da propriedade [`SelectionLength`](xref:Xamarin.Forms.Entry.SelectionLength) é 0, o que indica que nenhum texto está selecionado.

### <a name="displaying-a-clear-button"></a>Exibindo um botão limpar

A propriedade `ClearButtonVisibility` pode ser usada para controlar se um [`Entry`](xref:Xamarin.Forms.Entry) exibe um botão limpar, que permite ao usuário limpar o texto. Essa propriedade deve ser definida como um membro de enumeração `ClearButtonVisibility`:

- `Never` indica que um botão limpar nunca será exibido. Este é o valor padrão da propriedade `Entry.ClearButtonVisibility`.
- `WhileEditing` indica que um botão limpar será exibido na [`Entry`](xref:Xamarin.Forms.Entry), enquanto ele tem o foco e o texto.

O exemplo a seguir mostra a definição da propriedade em XAML:

```xaml
<Entry Text="Xamarin.Forms"
       ClearButtonVisibility="WhileEditing" />
```

Este é o código C# equivalente:

```csharp
var entry = new Entry { Text = "Xamarin.Forms", ClearButtonVisibility = ClearButtonVisibility.WhileEditing };
```

As capturas de tela a seguir mostram uma [`Entry`](xref:Xamarin.Forms.Entry) com o botão limpar habilitado:

![Captura de tela de uma entrada com um botão limpar, no iOS e no Android](entry-images/entry-clear-button.png)

### <a name="customizing-the-keyboard"></a>Personalizando o teclado

O teclado que é apresentado quando os usuários interagem com um [`Entry`](xref:Xamarin.Forms.Entry) pode ser definido programaticamente por meio da propriedade [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) , para uma das seguintes propriedades da classe [`Keyboard`](xref:Xamarin.Forms.Keyboard) :

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
<Entry Keyboard="Chat" />
```

Este é o código C# equivalente:

```csharp
var entry = new Entry { Keyboard = Keyboard.Chat };
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

#### <a name="customizing-the-return-key"></a>Personalizando a tecla Return

A aparência da chave de retorno no teclado virtual, que é exibida quando um [`Entry`](xref:Xamarin.Forms.Entry) tem foco, pode ser personalizada definindo a propriedade [`ReturnType`](xref:Xamarin.Forms.Entry.ReturnType) como um valor da enumeração [`ReturnType`](xref:Xamarin.Forms.ReturnType) :

- [`Default`](xref:Xamarin.Forms.ReturnType.Default) – indica que nenhuma chave de retorno específica é necessária e que o padrão de plataforma será usado.
- [`Done`](xref:Xamarin.Forms.ReturnType.Done) – indica uma chave de retorno "Done".
- [`Go`](xref:Xamarin.Forms.ReturnType.Go) – indica uma chave de retorno "Go".
- [`Next`](xref:Xamarin.Forms.ReturnType.Next) – indica uma "próxima" chave de retorno.
- [`Search`](xref:Xamarin.Forms.ReturnType.Search) – indica uma chave de retorno de "pesquisa".
- [`Send`](xref:Xamarin.Forms.ReturnType.Send) – indica uma chave de retorno "Send".

O exemplo XAML a seguir mostra como definir a chave de retornada:

```xaml
<Entry ReturnType="Send" />
```

Este é o código C# equivalente:

```csharp
var entry = new Entry { ReturnType = ReturnType.Send };
```

> [!NOTE]
> A aparência exata da chave de retorno depende da plataforma. No iOS, a tecla return é um botão com base em texto. No entanto, nas plataformas universais do Windows e Android, a tecla return é um botão de ícone.

Quando a tecla de retorno é pressionada, o evento [`Completed`](xref:Xamarin.Forms.Entry.Completed) é acionado e qualquer `ICommand` especificada pela propriedade [`ReturnCommand`](xref:Xamarin.Forms.Entry.ReturnCommand) é executada. Além disso, qualquer `object` especificada pela propriedade [`ReturnCommandParameter`](xref:Xamarin.Forms.Entry.ReturnCommandParameter) será passada para o `ICommand` como um parâmetro. Para obter mais informações sobre comandos, confira [A Interface de Comando](~/xamarin-forms/app-fundamentals/data-binding/commanding.md).

### <a name="enabling-and-disabling-spell-checking"></a>Habilitar e desabilitar a verificação ortográfica

A propriedade [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) controla se a verificação ortográfica está habilitada. Por padrão, a propriedade é definida como `true`. Enquanto o usuário insere texto, erros de ortografia são indicados.

No entanto, para alguns cenários de entrada de texto, como inserir um nome de usuário, a verificação ortográfica fornece uma experiência negativa e deve ser desabilitada definindo a propriedade [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) como `false`:

```xaml
<Entry ... IsSpellCheckEnabled="false" />
```

```csharp
var entry = new Entry { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> Quando a propriedade [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) é definida como `false`e um teclado personalizado não está sendo usado, o verificador ortográfico nativo será desabilitado. No entanto, se um [`Keyboard`](xref:Xamarin.Forms.Keyboard) tiver sido definido que desabilita a verificação ortográfica, como [`Keyboard.Chat`](xref:Xamarin.Forms.Keyboard.Chat), a propriedade `IsSpellCheckEnabled` será ignorada. Portanto, a propriedade não pode ser usada para habilitar a verificação ortográfica de um `Keyboard` que o desabilita explicitamente.

### <a name="enabling-and-disabling-text-prediction"></a>Habilitando e desabilitando a previsão de texto

A propriedade [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) controla se a previsão de texto e a correção automática de texto estão habilitadas. Por padrão, a propriedade é definida como `true`. Enquanto o usuário insere texto, previsões do word são apresentados.

No entanto, para alguns cenários de entrada de texto, como inserir um nome de usuário, a previsão de texto e a correção automática de texto fornecem uma experiência negativa e devem ser desabilitadas definindo a propriedade [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) como `false`:

```xaml
<Entry ... IsTextPredictionEnabled="false" />
```

```csharp
var entry = new Entry { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> Quando a propriedade [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) é definida como `false`e um teclado personalizado não está sendo usado, a previsão de texto e a correção automática de texto são desabilitadas. No entanto, se um [`Keyboard`](xref:Xamarin.Forms.Keyboard) tiver sido definido que desabilite a previsão de texto, a propriedade `IsTextPredictionEnabled` será ignorada. Portanto, a propriedade não pode ser usada para habilitar a previsão de texto para um `Keyboard` que o desabilita explicitamente.

### <a name="colors"></a>Cores

Pode ser configurada para usar um plano de fundo personalizado e as cores de texto por meio das seguintes propriedades vinculáveis:

- **TextColor** &ndash; define a cor do texto.
- **BackgroundColor** &ndash; define a cor mostrada por trás do texto.

Cuidado especial é necessário para garantir que as cores poderá ser usadas em cada plataforma. Como cada plataforma tem diferentes padrões de cores de plano de fundo e texto, geralmente você precisará definir ambos se você definir um.

Use o código a seguir para definir a cor do texto de uma entrada:

No XAML:

```xaml
<Entry TextColor="Green" />
```

No C#:

```csharp
var entry = new Entry();
entry.TextColor = Color.Green;
```

![Exemplo de TextColor de entrada](entry-images/textcolor.png)

Observe que o espaço reservado não é afetado pelo `TextColor`especificado.

Para definir a cor do plano de fundo no XAML:

```xaml
<Entry BackgroundColor="#2c3e50" />
```

No C#:

```csharp
var entry = new Entry();
entry.BackgroundColor = Color.FromHex("#2c3e50");
```

![Exemplo de BackgroundColor de entrada](entry-images/textbackgroundcolor.png)

Tenha cuidado para certificar-se de que as cores de plano de fundo e texto que você escolher podem ser usados em cada plataforma e não ocultam qualquer texto de espaço reservado.

## <a name="events-and-interactivity"></a>Eventos e interatividade

Entrada expõe dois eventos:

- [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) &ndash; gerado quando o texto é alterado na entrada. Fornece o texto antes e após a alteração.
- [`Completed`](xref:Xamarin.Forms.Entry.Completed) &ndash; gerado quando o usuário termina a entrada pressionando a tecla de retorno no teclado.

> [!NOTE]
> A classe [`VisualElement`](xref:Xamarin.Forms.VisualElement) , da qual [`Entry`](xref:Xamarin.Forms.Entry) herda, também tem eventos [`Focused`](xref:Xamarin.Forms.VisualElement.Focused) e [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused) .

### <a name="completed"></a>Concluído

O evento `Completed` é usado para reagir à conclusão de uma interação com uma entrada. `Completed` é gerado quando o usuário termina a entrada com um campo pressionando a tecla de retorno no teclado (ou pressionando a tecla Tab no UWP). O manipulador para o evento é um manipulador de eventos genérico, levando o remetente e `EventArgs`:

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

e o c#:

```csharp
var entry = new Entry ();
entry.Completed += Entry_Completed;
```

Depois que o evento [`Completed`](xref:Xamarin.Forms.Entry.Completed) é acionado, qualquer `ICommand` especificada pela propriedade [`ReturnCommand`](xref:Xamarin.Forms.Entry.ReturnCommand) é executada, com o `object` especificado pela propriedade [`ReturnCommandParameter`](xref:Xamarin.Forms.Entry.ReturnCommandParameter) que está sendo passada para o `ICommand`.

### <a name="textchanged"></a>TextChanged

O evento `TextChanged` é usado para reagir a uma alteração no conteúdo de um campo.

`TextChanged` é gerado sempre que o `Text` do `Entry` é alterado. O manipulador para o evento usa uma instância de `TextChangedEventArgs`. `TextChangedEventArgs` fornece acesso aos valores novos e antigos da `Text` de `Entry` por meio das propriedades `OldTextValue` e `NewTextValue`:

```csharp
void Entry_TextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

O evento `TextChanged` pode ser assinado em XAML:

```xaml
<Entry TextChanged="Entry_TextChanged" />
```

e o c#:

```csharp
var entry = new Entry ();
entry.TextChanged += Entry_TextChanged;
```

## <a name="related-links"></a>Links relacionados

- [Texto (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [API de Entrada](xref:Xamarin.Forms.Entry)
