---
title: Editor do xamarin. Forms
description: Este artigo explica como usar o controle de Editor do xamarin. Forms para aceitar a entrada de texto de várias linhas em um aplicativo.
ms.prod: xamarin
ms.assetid: 7074DB3A-30D2-4A6B-9A89-B029EEF20B07
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: 4879ff88d5bbdab5aa92024bee7f50239a141e3b
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995857"
---
# <a name="xamarinforms-editor"></a>Editor do xamarin. Forms

_Entrada de texto de várias linhas_

O `Editor` controle é usado para aceitar a entrada de várias linha. Este artigo abordará:

- **[Personalização](#customization)**  &ndash; opções de teclado e cor.
- **[Interatividade](#interactivity)**  &ndash; eventos que podem ser escutados para fornecer interatividade.

## <a name="customization"></a>Personalização

### <a name="setting-and-reading-text"></a>Definir e ler texto

O `Editor`, assim como outras exibições de apresentação de texto, expõe o `Text` propriedade. Essa propriedade pode ser usada para definir e ler o texto apresentado pelo `Editor`. O exemplo a seguir demonstra a configuração de `Text` propriedade em XAML:

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

### <a name="keyboards"></a>Teclados

O teclado que é apresentado quando os usuários interagem com um `Editor` podem ser definidas programaticamente por meio de [ ``Keyboard`` ](xref:Xamarin.Forms.Keyboard) propriedade.

As opções para o tipo de teclado são:

- **Padrão** &ndash; teclado padrão
- **Bate-papo** &ndash; usado para mandar um texto e locais em que o emoji são úteis
- **Email** &ndash; usado ao inserir endereços de email
- **Numérico** &ndash; usado ao inserir números
- **Telefone** &ndash; usado ao inserir números de telefone
- **URL** &ndash; usado para inserir caminhos de arquivo & endereços da web

Há um [exemplo de cada teclado](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/choose-keyboard-for-entry/) em nossa seção de receitas.

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
