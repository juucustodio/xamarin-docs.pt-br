---
title: Entrada do xamarin. Forms
description: Este artigo explica como usar a classe de entrada do xamarin. Forms para aceitar a entrada de senha em um aplicativo ou de texto de linha única.
ms.prod: xamarin
ms.assetid: 9923C541-3C10-4D14-BAB5-C4D6C514FB1E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2018
ms.openlocfilehash: c9be74bcc6e5d149856e06c98d3bef9a841d4bab
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61230192"
---
# <a name="xamarinforms-entry"></a>Entrada do xamarin. Forms

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)

_Texto de linha única ou de entrada de senha_

O xamarin. Forms [ `Entry` ](xref:Xamarin.Forms.Entry) é usado para entrada de texto de linha única. O `Entry`, como o [ `Editor` ](xref:Xamarin.Forms.Editor) exibir, dá suporte a vários tipos de teclado. Além disso, o `Entry` pode ser usado como um campo de senha.

## <a name="display-customization"></a>Personalização da exibição

### <a name="setting-and-reading-text"></a>Definir e ler texto

O `Entry`, assim como outras exibições de apresentação de texto, expõe o [ `Text` ](xref:Xamarin.Forms.Entry.Text) propriedade. Essa propriedade pode ser usada para definir e ler o texto apresentado pelo `Entry`. O exemplo a seguir demonstra a configuração de `Text` propriedade em XAML:

```xaml
<Entry Text="I am an Entry" />
```

No C#:

```csharp
var MyEntry = new Entry { Text = "I am an Entry" };
```

Para ler o texto, acessar o `Text` propriedade em C#:

```csharp
var text = MyEntry.Text;
```

### <a name="setting-placeholder-text"></a>Definir o texto de espaço reservado

O [ `Entry` ](xref:Xamarin.Forms.Entry) pode ser definido para mostrar o texto de espaço reservado quando ele não está armazenando a entrada do usuário. Isso é feito definindo a [ `Placeholder` ](xref:Xamarin.Forms.Entry.Placeholder) propriedade como um `string`e geralmente é usado para indicar o tipo de conteúdo que é apropriado para o `Entry`. Além disso, a cor do texto de espaço reservado pode ser controlada definindo a [ `PlaceholderColor` ](xref:Xamarin.Forms.Entry.PlaceholderColor) propriedade como um [ `Color` ](xref:Xamarin.Forms.Color):

```xaml
<Entry Placeholder="Username" PlaceholderColor="Olive" />
```

```csharp
var entry = new Entry { Placeholder = "Username", PlaceholderColor = Color.Olive };
```

> [!NOTE]
> A largura de uma `Entry` pode ser definida ao configurar seu `WidthRequest` propriedade. Não dependem da largura de uma `Entry` que está sendo definida com base no valor de seu `Text` propriedade.

### <a name="preventing-text-entry"></a>Impedindo a entrada de texto

Os usuários podem ser impedidos de modificar o texto em uma [ `Entry` ](xref:Xamarin.Forms.Entry) definindo a `IsReadOnly` propriedade, que tem um valor padrão de `false`, para `true`:

```xaml
<Entry Text="This is a read-only Entry"
       IsReadOnly="true" />
```

```csharp
var entry = new Entry { Text = "This is a read-only Entry", IsReadOnly = true });
```

> [!NOTE]
> O `IsReadonly` propriedade não altera a aparência visual de um [ `Entry` ](xref:Xamarin.Forms.Entry), ao contrário o `IsEnabled` propriedade que também altera a aparência visual do `Entry` em cinza.

### <a name="limiting-input-length"></a>Limitação de tamanho de entrada

O [ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength) propriedade pode ser usada para limitar o tamanho de entrada é permitido para o [ `Entry` ](xref:Xamarin.Forms.Entry). Essa propriedade deve ser definida como um inteiro positivo:

```xaml
<Entry ... MaxLength="10" />
```

```csharp
var entry = new Entry { ... MaxLength = 10 };
```

Um [ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength) valor de propriedade de 0 indica que nenhuma entrada será permitida e um valor de `int.MaxValue`, que é o valor padrão para um [ `Entry` ](xref:Xamarin.Forms.Entry), indica que não há nenhum limite efetivo no número de caracteres que podem ser inseridos.

### <a name="password-fields"></a>Campos de senha

`Entry` fornece o `IsPassword` propriedade. Quando `IsPassword` é `true`, o conteúdo do campo será apresentado como círculos pretos:

No XAML:

```xaml
<Entry IsPassword="true" />
```

No C#:

```csharp
var MyEntry = new Entry { IsPassword = true };
```

![](entry-images/password.png "Exemplo de entrada de IsPassword")

Os espaços reservados podem ser usados com instâncias de `Entry` que são configurados como campos de senha:

No XAML:

```xaml
<Entry IsPassword="true" Placeholder="Password" />
```

No C#:

```csharp
var MyEntry = new Entry { IsPassword = true, Placeholder = "Password" };
```

![](entry-images/passwordplaceholder.png "Exemplo de espaço reservado e IsPassword de entrada")

### <a name="setting-the-cursor-position-and-text-selection-length"></a>Definindo a posição do Cursor e o comprimento da seleção de texto

O [ `CursorPosition` ](xref:Xamarin.Forms.Entry.CursorPosition) propriedade pode ser usada para retornar ou definir a posição em que o próximo caractere será inserido na cadeia de caracteres armazenada em do [ `Text` ](xref:Xamarin.Forms.Entry.Text) propriedade:

```xaml
<Entry Text="Cursor position set" CursorPosition="5" />
```

```csharp
var entry = new Entry { Text = "Cursor position set", CursorPosition = 5 };
```

O valor padrão de [ `CursorPosition` ](xref:Xamarin.Forms.Entry.CursorPosition) propriedade é 0, que indica que o texto será inserido no início do `Entry`.

Além disso, o [ `SelectionLength` ](xref:Xamarin.Forms.Entry.SelectionLength) propriedade pode ser usada para retornar ou definir o comprimento da seleção de texto dentro de `Entry`:

```xaml
<Entry Text="Cursor position and selection length set" CursorPosition="2" SelectionLength="10" />
```

```csharp
var entry = new Entry { Text = "Cursor position and selection length set", CursorPosition = 2, SelectionLength = 10 };
```

O valor padrão de [ `SelectionLength` ](xref:Xamarin.Forms.Entry.SelectionLength) propriedade é 0, que indica que nenhum texto estiver selecionado.

### <a name="customizing-the-keyboard"></a>Personalizando o teclado

O teclado que é apresentado quando os usuários interagem com um [ `Entry` ](xref:Xamarin.Forms.Entry) podem ser definidas programaticamente por meio dos [ `Keyboard` ](xref:Xamarin.Forms.InputView.Keyboard) propriedade a uma das seguintes propriedades do [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) classe:

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
<Entry Keyboard="Chat" />
```

O código C# equivalente é:

```csharp
var entry = new Entry { Keyboard = Keyboard.Chat };
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

O código C# equivalente é:

```csharp
var entry = new Entry { Placeholder = "Enter text here" };
entry.Keyboard = Keyboard.Create(KeyboardFlags.Suggestions | KeyboardFlags.CapitalizeCharacter);
```

#### <a name="customizing-the-return-key"></a>Personalizando a tecla Return

A aparência da chave retorno sobre o teclado virtual, que é exibido quando um [ `Entry` ](xref:Xamarin.Forms.Entry) tem o foco, pode ser personalizado definindo as [ `ReturnType` ](xref:Xamarin.Forms.Entry.ReturnType) propriedade um valor de [ `ReturnType` ](xref:Xamarin.Forms.ReturnType) enumeração:

- [`Default`](xref:Xamarin.Forms.ReturnType.Default) – indica que nenhuma chave de retorno específico é necessário e que será usado o padrão de plataforma.
- [`Done`](xref:Xamarin.Forms.ReturnType.Done) – indica uma chave de retornada "Done".
- [`Go`](xref:Xamarin.Forms.ReturnType.Go) – indica uma chave de retorno de "Ir".
- [`Next`](xref:Xamarin.Forms.ReturnType.Next) – indica uma chave de retornada "Avançar".
- [`Search`](xref:Xamarin.Forms.ReturnType.Search) – indica uma chave de retorno de "Pesquisar".
- [`Send`](xref:Xamarin.Forms.ReturnType.Send) – indica uma chave de retorno de "Envio".

O exemplo XAML a seguir mostra como definir a chave de retornada:

```xaml
<Entry ReturnType="Send" />
```

O código C# equivalente é:

```csharp
var entry = new Entry { ReturnType = ReturnType.Send };
```

> [!NOTE]
> A aparência exata da chave de retorno depende da plataforma. No iOS, a tecla return é um botão com base em texto. No entanto, nas plataformas universais do Windows e Android, a tecla return é um botão de ícone.

Quando a tecla return é pressionada, o [ `Completed` ](xref:Xamarin.Forms.Entry.Completed) evento é acionado e qualquer `ICommand` especificado pela [ `ReturnCommand` ](xref:Xamarin.Forms.Entry.ReturnCommand) propriedade é executada. Além disso, qualquer `object` especificado pelo [ `ReturnCommandParameter` ](xref:Xamarin.Forms.Entry.ReturnCommandParameter) propriedade será passada para o `ICommand` como um parâmetro. Para obter mais informações sobre comandos, consulte [a Interface de comando](~/xamarin-forms/app-fundamentals/data-binding/commanding.md).

### <a name="enabling-and-disabling-spell-checking"></a>Habilitar e desabilitar a verificação ortográfica

O [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propriedade controla se verificação ortográfica está habilitada. Por padrão, a propriedade é definida como `true`. Enquanto o usuário insere texto, erros de ortografia são indicados.

No entanto, para alguns cenários de entrada de texto, como inserir um nome de usuário, verificação ortográfica fornece uma experiência negativa e deve ser desabilitada definindo a [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propriedade `false`:

```xaml
<Entry ... IsSpellCheckEnabled="false" />
```

```csharp
var entry = new Entry { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> Quando o [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) estiver definida como `false`e um teclado personalizado não está sendo usado, o verificador ortográfico nativo será desabilitado. No entanto, se um [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) tem sido conjunto que desabilita ortográfica verificação, como [ `Keyboard.Chat` ](xref:Xamarin.Forms.Keyboard.Chat), o `IsSpellCheckEnabled` propriedade será ignorada. Portanto, a propriedade não pode ser usada para habilitar a verificação ortográfica para um `Keyboard` que desabilita explicitamente a ele.

### <a name="enabling-and-disabling-text-prediction"></a>Habilitando e desabilitando a previsão de texto

O [ `IsTextPredictionEnabled` ](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) propriedade controla se a previsão de texto e automático correção de texto está habilitada. Por padrão, a propriedade é definida como `true`. Enquanto o usuário insere texto, previsões do word são apresentados.

No entanto, para alguns cenários de entrada de texto, como inserir um nome de usuário, a previsão de texto e o texto automático correção fornece uma experiência negativa e deve ser desabilitada definindo a [ `IsTextPredictionEnabled` ](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) propriedade `false`:

```xaml
<Entry ... IsTextPredictionEnabled="false" />
```

```csharp
var entry = new Entry { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> Quando o [ `IsTextPredictionEnabled` ](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) estiver definida como `false`, e um teclado personalizado não está sendo usado, a previsão de texto e automático correção de texto está desabilitada. No entanto, se um [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) tiver sido definido que desabilita a previsão de texto, o `IsTextPredictionEnabled` propriedade será ignorada. Portanto, a propriedade não pode ser usada para habilitar a previsão de texto para um `Keyboard` que desabilita explicitamente a ele.

### <a name="colors"></a>Cores

Pode ser configurada para usar um plano de fundo personalizado e as cores de texto por meio das seguintes propriedades vinculáveis:

- **TextColor** &ndash; define a cor do texto.
- **BackgroundColor** &ndash; define a cor mostrada atrás do texto.

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

![](entry-images/textcolor.png "Exemplo de entrada de TextColor")

Observe que o espaço reservado não é afetado pela especificado `TextColor`.

Para definir a cor do plano de fundo no XAML:

```xaml
<Entry BackgroundColor="#2c3e50" />
```

No C#:

```csharp
var entry = new Entry();
entry.BackgroundColor = Color.FromHex("#2c3e50");
```

![](entry-images/textbackgroundcolor.png "Exemplo de entrada de BackgroundColor")

Tenha cuidado para certificar-se de que as cores de plano de fundo e texto que você escolher podem ser usados em cada plataforma e não ocultam qualquer texto de espaço reservado.

## <a name="events-and-interactivity"></a>Eventos e interatividade

Entrada expõe dois eventos:

- [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) &ndash; gerado quando o texto é alterado na entrada. Fornece o texto antes e após a alteração.
- [`Completed`](xref:Xamarin.Forms.Entry.Completed) &ndash; gerado quando o usuário terminou entrada pressionando a tecla return no teclado.

> [!NOTE]
> O [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) classe da qual [ `Entry` ](xref:Xamarin.Forms.Entry) herda, também tem [ `Focused` ](xref:Xamarin.Forms.VisualElement.Focused) e [ `Unfocused` ](xref:Xamarin.Forms.VisualElement.Unfocused)eventos.

### <a name="completed"></a>Concluído

O `Completed` evento é usado para responder à realização de uma interação com uma entrada. `Completed` é gerado quando o usuário encerra a entrada com um campo, pressionando a tecla return no teclado. O manipulador para o evento é um manipulador de eventos genéricos, levando o remetente e `EventArgs`:

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

e o C#:

```csharp
var entry = new Entry ();
entry.Completed += Entry_Completed;
```

Após o [ `Completed` ](xref:Xamarin.Forms.Entry.Completed) evento é acionado, qualquer `ICommand` especificado pela [ `ReturnCommand` ](xref:Xamarin.Forms.Entry.ReturnCommand) propriedade for executada, com o `object` especificado pelo [ `ReturnCommandParameter` ](xref:Xamarin.Forms.Entry.ReturnCommandParameter) propriedade que está sendo passada para o `ICommand`.

### <a name="textchanged"></a>TextChanged

O `TextChanged` evento é usado para responder a uma alteração no conteúdo de um campo.

`TextChanged` é gerado sempre que o `Text` do `Entry` alterações. O manipulador para o evento usa uma instância de `TextChangedEventArgs`. `TextChangedEventArgs` fornece acesso aos valores novos e antigos do `Entry` `Text` por meio de `OldTextValue` e `NewTextValue` propriedades:

```csharp
void Entry_TextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

O `TextChanged` eventos podem ser assinados em XAML:

```xaml
<Entry TextChanged="Entry_TextChanged" />
```

e o C#:

```csharp
var entry = new Entry ();
entry.TextChanged += Entry_TextChanged;
```


## <a name="related-links"></a>Links relacionados

- [Texto (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [Entrada de API](xref:Xamarin.Forms.Entry)
