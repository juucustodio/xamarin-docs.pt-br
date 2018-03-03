---
title: Editor
description: "Entrada de texto de várias linhas"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7074DB3A-30D2-4A6B-9A89-B029EEF20B07
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: 6833f8fb2de35139c35779d9eae4c3aff70568c7
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="editor"></a>Editor

_Entrada de texto de várias linhas_

O `Editor` controle é usado para aceitar a entrada de várias linha. Este artigo aborda:

- **[Personalização](#Customization)**  &ndash; opções de teclado e cor.
- **[Interatividade](#Interactivity)**  &ndash; eventos que podem ser de escuta para fornecer interatividade.

## <a name="customization"></a>Personalização

### <a name="setting-and-reading-text"></a>Configuração e lendo texto

Editor, como outros modos de apresentação de texto, expõe a `Text` propriedade. `Text` pode ser usado para definir e ler o texto apresentado pelo `Editor`. O exemplo a seguir demonstra a definição de texto em XAML:

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

### <a name="keyboards"></a>Teclados

O teclado é apresentado quando os usuários interagem com um `Editor` podem ser definidas programaticamente por meio de [ ``Keyboard`` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Keyboard/) propriedade.

As opções para o tipo de teclado são:

- **Padrão** &ndash; o teclado de padrão
- **Bate-papo** &ndash; usado para textos & lugares onde emoji são úteis
- **Email** &ndash; usado ao inserir endereços de email
- **Numérico** &ndash; usado ao inserir números
- **Telefone** &ndash; usado ao inserir números de telefone
- **URL** &ndash; usado para a inserção de caminhos de arquivo & endereços da web

Há um [exemplo de cada teclado](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/choose-keyboard-for-entry/) em nossa seção de receitas.

### <a name="colors"></a>Cores

`Editor` pode ser definido para usar uma cor de plano de fundo personalizado por meio de `BackgroundColor` propriedade. Cuidado especial é necessário para assegurar que as cores poderá ser usadas em cada plataforma. Como cada plataforma tem diferentes padrões de cor do texto, talvez seja necessário definir uma cor de plano de fundo personalizado para cada plataforma. Consulte [trabalhando com a plataforma Tweaks](~/xamarin-forms/platform/device.md) para obter mais informações sobre como otimizar a interface do usuário para cada plataforma.

No C#:

```csharp
public partial class EditorPage : ContentPage
{
    public EditorPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        //dark blue on Windows Phone & Android, light blue on iOS
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
                        <On Platform="Android, WinPhone, UWP" Value="#2c3e50" />
                    </OnPlatform>
                </Editor.BackgroundColor>
            </Editor>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

![](editor-images/textbackgroundcolor.png "Editor de exemplo BackgroundColor")

Certifique-se de que as cores de plano de fundo e texto que você escolher podem ser usados em cada plataforma e não podem ocultar qualquer texto de espaço reservado.

## <a name="interactivity"></a>Interatividade

`Editor` expõe dois eventos:

- [TextChanged](http://developer.xamarin.com/api/event/Xamarin.Forms.Editor.TextChanged/) &ndash; gerado quando o texto é alterado no editor. Fornece o texto antes e depois da alteração.
- [Concluída](http://developer.xamarin.com/api/event/Xamarin.Forms.Editor.Completed/) &ndash; gerado quando o usuário foi finalizada entrada pressionando a tecla return no teclado.

### <a name="completed"></a>Concluído

O `Completed` eventos é usado para reagir a conclusão de uma interação com um `Editor`. `Completed` é gerado quando o usuário termina de entrada com um campo inserindo a chave de retorno no teclado. O manipulador para o evento é um manipulador de eventos genérico, levando o remetente e `EventArgs`:

```csharp
void EditorCompleted (object sender, EventArgs e)
{
    var text = ((Editor)sender).Text; // sender is cast to an Editor to enable reading the `Text` property of the view.
}
```

O evento concluído pode ser inscrito em código e XAML:

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

O `TextChanged` eventos é usado para reagir a uma alteração no conteúdo de um campo.

`TextChanged` é gerado sempre que o `Text` do `Editor` alterações. O manipulador para o evento usa uma instância de `TextChangedEventArgs`. `TextChangedEventArgs` fornece acesso aos valores novos e antigos do `Editor` `Text` por meio de `OldTextValue` e `NewTextValue` propriedades:

```csharp
void EditorTextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

O evento concluído pode ser inscrito em código e XAML:

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

- [Texto (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [Editor de API](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/)
