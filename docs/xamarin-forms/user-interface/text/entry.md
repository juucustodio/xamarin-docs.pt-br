---
title: Entrada do xamarin. Forms
description: Este artigo explica como usar a classe xamarin. Forms entrada para aceitar texto de uma linha ou entrada de senha em um aplicativo.
ms.prod: xamarin
ms.assetid: 9923C541-3C10-4D14-BAB5-C4D6C514FB1E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: b6188b986589a56229ad2e092d4100ff3f75dbe4
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245547"
---
# <a name="xamarinforms-entry"></a>Entrada do xamarin. Forms

_Texto de uma linha ou a senha de entrada_

O xamarin. Forms `Entry` é usado para entrada de texto de linha única. O `Entry`, como o `Editor` exibir, dá suporte a vários tipos de teclado. Além disso, o `Entry` pode ser usado como um campo de senha.

## <a name="display-customization"></a>Personalização de exibição

### <a name="setting-and-reading-text"></a>Configuração e lendo texto

O `Entry`, como outros modos de apresentação de texto, expõe a `Text` propriedade. Essa propriedade pode ser usada para definir e ler o texto apresentado pelo `Entry`. O exemplo a seguir mostra a configuração de `Text` propriedade em XAML:

```xaml
<Entry Text="I am an Entry" />
```

No C#:

```csharp
var MyEntry = new Entry { Text = "I am an Entry" };
```

Para ler o texto, acessar o `Text` propriedade em c#:

```csharp
var text = MyEntry.Text;
```

> [!NOTE]
> A largura de uma `Entry` podem ser definidas, definindo seu `WidthRequest` propriedade. Não dependem da largura de uma `Entry` que está sendo definido com base no valor de seu `Text` propriedade.

### <a name="limiting-input-length"></a>Limitar tamanho de entrada

O [ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength) propriedade pode ser usada para limitar o tamanho permitido para o [ `Entry` ](xref:Xamarin.Forms.Entry). Essa propriedade deve ser definida como um número inteiro positivo:

```xaml
<Entry ... MaxLength="10" />
```

```csharp
var entry = new Entry { ... MaxLength = 10 };
```

Um [ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength) valor da propriedade de 0 indica que nenhuma entrada será permitida e um valor de `int.MaxValue`, que é o valor padrão para um [ `Entry` ](xref:Xamarin.Forms.Entry), indica que há nenhum limite efetivo do número de caracteres que podem ser inseridos.

### <a name="keyboards"></a>Teclados

O teclado é apresentado quando os usuários interagem com um `Entry` podem ser definidas programaticamente por meio de `Keyboard` propriedade.

As opções para o tipo de teclado são:

- **Padrão** &ndash; o teclado de padrão
- **Bate-papo** &ndash; usado para textos & lugares onde emoji são úteis
- **Email** &ndash; usado ao inserir endereços de email
- **Numérico** &ndash; usado ao inserir números
- **Telefone** &ndash; usado ao inserir números de telefone
- **URL** &ndash; usado para inserir os caminhos de arquivo e endereços da web

Há um [exemplo de cada teclado](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/choose-keyboard-for-entry/) em nossa seção de receitas.

### <a name="enabling-and-disabling-spell-checking"></a>Habilitar e desabilitar a verificação de ortografia

O [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propriedade controla se verificação ortográfica está habilitada. Por padrão, a propriedade é definida como `true`. Como o usuário insere texto, erros de ortografia são indicados.

No entanto, para alguns cenários de entrada de texto, como inserir um nome de usuário, a verificação ortográfica fornece uma experiência negativa e, portanto devem ser desativados, definindo o [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propriedade `false`:

```xaml
<Entry ... IsSpellCheckEnabled="false" />
```

```csharp
var entry = new Entry { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> Quando o [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) está definida como `false`e um teclado personalizado não está sendo usado, o verificador ortográfico nativo será desabilitado. No entanto, se um [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) tem foi conjunto que desabilita ortográfica verificação, como [ `Keyboard.Chat` ](xref:Xamarin.Forms.Keyboard.Chat), o `IsSpellCheckEnabled` propriedade será ignorada. Portanto, a propriedade não pode ser usada para habilitar a verificação ortográfica para um `Keyboard` que desabilita explicitamente a ele.

### <a name="placeholders"></a>Espaços reservados

`Entry` pode ser definido para mostrar texto do espaço reservado quando ele não está armazenando a entrada do usuário. Na prática, isso é geralmente visto em formulários para esclarecer o conteúdo que é apropriado para um determinado campo. Cor do texto de espaço reservado não pode ser personalizada e será o mesmo, independentemente do `TextColor` configuração. Se seu design exigir uma cor de espaço reservado personalizado, você precisará fazer fallback para uma [renderizador personalizado](). A seguir criará um `Entry` com "Username" como o espaço reservado em XAML:

```xaml
<Entry Placeholder="Username" />
```

No C#:

```csharp
var MyEntry = new Entry { Placeholder = "Username" };
```

![](entry-images/placeholder.png "Exemplo de entrada de espaço reservado")

### <a name="password-fields"></a>Campos de senha

`Entry` fornece o `IsPassword` propriedade. Quando `IsPassword` é `true`, o conteúdo do campo será apresentado como círculos pretos:

Em XAML:

```xaml
<Entry IsPassword="true" />
```

No C#:

```csharp
var MyEntry = new Entry { IsPassword = true };
```

![](entry-images/password.png "Exemplo de entrada de IsPassword")

Espaços reservados podem ser usados com instâncias de `Entry` que são configurados como campos de senha:

Em XAML:

```xaml
<Entry IsPassword="true" Placeholder="Password" />
```

No C#:

```csharp
var MyEntry = new Entry { IsPassword = true, Placeholder = "Password" };
```

![](entry-images/passwordplaceholder.png "Exemplo de espaço reservado e IsPassword de entrada")


### <a name="colors"></a>Cores

Pode ser configurada para usar um plano de fundo personalizado e as cores de texto por meio das propriedades vinculáveis aos seguintes:

- **TextColor** &ndash; define a cor do texto.
- **BackgroundColor** &ndash; define a cor mostrada atrás do texto.

Cuidado especial é necessário para assegurar que as cores poderá ser usadas em cada plataforma. Como cada plataforma tem diferentes padrões de cores de plano de fundo e texto, geralmente você precisará definir ambos, se você definir um.

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

![](entry-images/textcolor.png "Exemplo de entrada de TextColor")

Observe que o espaço reservado não é afetado por especificado `TextColor`.

Para definir a cor de plano de fundo em XAML:

```xaml
<Entry BackgroundColor="#2c3e50" />
```

No C#:

```csharp
var entry = new Entry();
entry.BackgroundColor = Color.FromHex("#2c3e50");
```

![](entry-images/textbackgroundcolor.png "Exemplo de entrada de BackgroundColor")

Tenha cuidado para garantir que as cores de plano de fundo e texto que você escolher podem ser usados em cada plataforma e não podem ocultar qualquer texto de espaço reservado.

## <a name="events-and-interactivity"></a>Eventos e interatividade

Entrada expõe dois eventos:

- [TextChanged](http://developer.xamarin.com/api/event/Xamarin.Forms.Entry.TextChanged/) &ndash; gerado quando o texto é alterado na entrada. Fornece o texto antes e depois da alteração.
- [Concluída](http://developer.xamarin.com/api/event/Xamarin.Forms.Entry.Completed/) &ndash; gerado quando o usuário foi finalizada entrada pressionando a tecla return no teclado.

### <a name="completed"></a>Concluído

O `Completed` eventos é usado para reagir a conclusão de uma interação com uma entrada. `Completed` é gerado quando o usuário termina de entrada com um campo inserindo a chave de retorno no teclado. O manipulador para o evento é um manipulador de eventos genérico, levando o remetente e `EventArgs`:

```csharp
void Entry_Completed (object sender, EventArgs e)
{
    var text = ((Entry)sender).Text; //cast sender to access the properties of the Entry
}
```

O evento concluído pode ser inscrito em XAML:

```xaml
<Entry Completed="Entry_Completed" />
```

e c#:

```csharp
var entry = new Entry ();
entry.Completed += Entry_Completed;
```

### <a name="textchanged"></a>TextChanged

O `TextChanged` eventos é usado para reagir a uma alteração no conteúdo de um campo.

`TextChanged` é gerado sempre que o `Text` do `Entry` alterações. O manipulador para o evento usa uma instância de `TextChangedEventArgs`. `TextChangedEventArgs` fornece acesso aos valores novos e antigos do `Entry` `Text` por meio de `OldTextValue` e `NewTextValue` propriedades:

```csharp
void Entry_TextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

O `TextChanged` evento pode ser inscrita em XAML:

```xaml
<Entry TextChanged="Entry_TextChanged" />
```

e c#:

```csharp
var entry = new Entry ();
entry.TextChanged += Entry_TextChanged;
```


## <a name="related-links"></a>Links relacionados

- [Texto (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [Entrada de API](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)
