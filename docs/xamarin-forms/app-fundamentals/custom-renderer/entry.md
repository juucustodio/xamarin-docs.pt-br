---
title: Personalizando uma entrada
description: O controle Entry do Xamarin.Forms permite a edição de uma única linha de texto. Este artigo demonstra como criar um renderizador personalizado para o controle Entry, permitindo que os desenvolvedores substituam a renderização nativa padrão por sua própria personalização específica da plataforma.
ms.prod: xamarin
ms.assetid: 7B5DD10D-0411-424F-88D8-8A474DF16D8D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/26/2018
ms.openlocfilehash: 775ef2a38cf423536072358299ac4f30db974d9a
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53056990"
---
# <a name="customizing-an-entry"></a>Personalizando uma entrada

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/entry/)

_O controle Entry do Xamarin.Forms permite a edição de uma única linha de texto. Este artigo demonstra como criar um renderizador personalizado para o controle Entry, permitindo que os desenvolvedores substituam a renderização nativa padrão por sua própria personalização específica da plataforma._

Cada controle do Xamarin.Forms tem um renderizador que o acompanha para cada plataforma que cria uma instância de um controle nativo. Quando um controle [`Entry`](xref:Xamarin.Forms.Entry) é renderizado por um aplicativo Xamarin.Forms, no iOS, é criada uma instância da classe `EntryRenderer`, que, por sua vez, cria uma instância de um controle `UITextField` nativo. Na plataforma Android, a classe `EntryRenderer` cria uma instância de um controle `EditText`. No UWP (Plataforma Universal do Windows), a classe `EntryRenderer` cria uma instância de um controle `TextBox`. Para obter mais informações sobre as classes de renderizador e de controle nativo para as quais os controles do Xamarin.Forms são mapeadas, confira [Classes base do renderizador e controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

O diagrama a seguir ilustra a relação entre o controle [`Entry`](xref:Xamarin.Forms.Entry) e os controles nativos correspondentes que o implementam:

![](entry-images/entry-classes.png "Relação entre o controle Entry e seus controles de nativos de implementação")

É possível aproveitar o processo de renderização para implementar personalizações específicas da plataforma criando um renderizador personalizado para o controle [`Entry`](xref:Xamarin.Forms.Entry) em cada plataforma. O processo para fazer isso é o seguinte:

1. [Criar](#Creating_the_Custom_Entry_Control) um controle personalizado do Xamarin.Forms.
1. [Consumir](#Consuming_the_Custom_Control) o controle personalizado do Xamarin.Forms.
1. [Criar](#Creating_the_Custom_Renderer_on_each_Platform) o renderizador personalizado para o controle em cada plataforma.

Cada item agora será abordado um por vez, para implementar um controle [`Entry`](xref:Xamarin.Forms.Entry) que tenha uma cor da tela de fundo diferente em cada plataforma.

> [!IMPORTANT]
> Este artigo explica como criar um renderizador personalizado simples. No entanto, não é necessário criar um renderizador personalizado para implementar uma `Entry` que tenha uma cor da tela de fundo diferente em cada plataforma. Isso pode ser feito com mais facilidade usando a classe [`Device`](xref:Xamarin.Forms.Device), ou a extensão de marcação `OnPlatform`, para fornecer valores específicos da plataforma. Para obter mais informações, confira [Fornecendo valores específicos da plataforma](~/xamarin-forms/platform/device.md#providing-platform-specific-values) e [Extensão de marcação OnPlatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension).

<a name="Creating_the_Custom_Entry_Control" />

## <a name="creating-the-custom-entry-control"></a>Criando o controle de entrada personalizado

É possível criar um controle [`Entry`](xref:Xamarin.Forms.Entry) personalizado criando subclasses da classe `Entry`, conforme mostrado no seguinte exemplo de código:

```csharp
public class MyEntry : Entry
{
}
```

O controle `MyEntry` é criado no projeto da biblioteca do .NET Standard e é apenas um controle [`Entry`](xref:Xamarin.Forms.Entry). A personalização do controle será realizada no renderizador personalizado. Portanto, nenhuma implementação adicional é necessária no controle `MyEntry`.

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>Consumindo o controle personalizado

O controle `MyEntry` pode ser referenciado em XAML no projeto da biblioteca do .NET Standard declarando um namespace para sua localização e usando o prefixo do namespace no elemento de controle. O seguinte exemplo de código mostra como o controle `MyEntry` pode ser consumido por uma página XAML:

```xaml
<ContentPage ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <local:MyEntry Text="In Shared Code" />
    ...
</ContentPage>
```

O prefixo do namespace `local` pode ter qualquer nome. No entanto, os valores de `clr-namespace` e `assembly` devem corresponder aos detalhes do controle personalizado. Quando o namespace é declarado, o prefixo é usado para referenciar o controle personalizado.

O seguinte exemplo de código mostra como o controle `MyEntry` pode ser consumido por uma página em C#:

```csharp
public class MainPage : ContentPage
{
  public MainPage ()
  {
    Content = new StackLayout {
      Children = {
        new Label {
          Text = "Hello, Custom Renderer !",
        },
        new MyEntry {
          Text = "In Shared Code",
        }
      },
      VerticalOptions = LayoutOptions.CenterAndExpand,
      HorizontalOptions = LayoutOptions.CenterAndExpand,
    };
  }
}
```

Esse código cria uma instância de um novo objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage) que exibirá um [`Label`](xref:Xamarin.Forms.Label) e um controle `MyEntry`, centralizado vertical e horizontalmente na página.

Agora, um renderizador personalizado pode ser adicionado a cada projeto de aplicativo para personalizar a aparência do controle em cada plataforma.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Criando o renderizador personalizado em cada plataforma

O processo para criar a classe do renderizador personalizado é o seguinte:

1. Criar uma subclasse da classe `EntryRenderer` que renderiza o controle nativo.
1. Substituir o método `OnElementChanged` que renderiza o controle nativo e escrever a lógica para personalizá-lo. Esse método é chamado quando o controle do Xamarin.Forms correspondente é criado.
1. Adicionar um atributo `ExportRenderer` à classe do renderizador personalizado para especificar que ele será usado para renderizar o controle do Xamarin.Forms. Esse atributo é usado para registrar o renderizador personalizado no Xamarin.Forms.

> [!NOTE]
> O fornecimento de um renderizador personalizado em cada projeto da plataforma é opcional. Se um renderizador personalizado não estiver registrado, será usado o renderizador padrão da classe base do controle.

O seguinte diagrama ilustra as responsabilidades de cada projeto no aplicativo de exemplo, bem como as relações entre elas:

![](entry-images/solution-structure.png "Responsabilidades do projeto de renderizador personalizado de MyEntry")

O controle `MyEntry` é renderizado por classes `MyEntryRenderer` específicas da plataforma, que derivam da classe `EntryRenderer` para cada plataforma. Isso faz com que cada controle `MyEntry` seja renderizado com a cor da tela de fundo específica da plataforma, conforme mostrado nas seguintes capturas de tela:

![](entry-images/screenshots.png "Controle MyEntry em cada plataforma")

A classe `EntryRenderer` expõe o método `OnElementChanged`, que é chamado quando um controle do Xamarin.Forms é criado para renderizar o controle nativo correspondente. Esse método usa um parâmetro `ElementChangedEventArgs`, que contém as propriedades `OldElement` e `NewElement`. Essas propriedades representam o elemento do Xamarin.Forms ao qual o renderizador *estava* anexado e o elemento do Xamarin.Forms ao qual o renderizador *está* anexado, respectivamente. No aplicativo de exemplo, a propriedade `OldElement` será `null` e a propriedade `NewElement` conterá uma referência ao controle `MyEntry`.

Uma versão de substituição do método `OnElementChanged` na classe `MyEntryRenderer` é o lugar para realizar a personalização do controle nativo. Uma referência tipada ao controle nativo que está sendo usado na plataforma pode ser acessada por meio da propriedade `Control`. Além disso, é possível obter uma referência ao controle do Xamarin.Forms que está sendo renderizado por meio da propriedade `Element`, embora ela não seja usada no aplicativo de exemplo.

Cada classe de renderizador personalizado é decorada com um atributo `ExportRenderer` que registra o renderizador no Xamarin.Forms. O atributo usa dois parâmetros – o nome do tipo do controle do Xamarin.Forms que está sendo renderizado e o nome do tipo do renderizador personalizado. O prefixo `assembly` do atributo especifica que o atributo se aplica a todo o assembly.

As seções a seguir abordam a implementação de cada classe de renderizador personalizado `MyEntryRenderer` específica da plataforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Criando o renderizador personalizado no iOS

O seguinte exemplo de código mostra o renderizador personalizado para a plataforma iOS:

```csharp
using Xamarin.Forms.Platform.iOS;

[assembly: ExportRenderer (typeof(MyEntry), typeof(MyEntryRenderer))]
namespace CustomRenderer.iOS
{
    public class MyEntryRenderer : EntryRenderer
    {
        protected override void OnElementChanged (ElementChangedEventArgs<Entry> e)
        {
            base.OnElementChanged (e);

            if (Control != null) {
                // do whatever you want to the UITextField here!
                Control.BackgroundColor = UIColor.FromRGB (204, 153, 255);
                Control.BorderStyle = UITextBorderStyle.Line;
            }
        }
    }
}
```

A chamada ao método `OnElementChanged` da classe base cria uma instância de um controle `UITextField` do iOS, com uma referência ao controle que está sendo atribuído à propriedade `Control` do renderizador. A cor da tela de fundo é definida como roxo-claro com o método `UIColor.FromRGB`.

### <a name="creating-the-custom-renderer-on-android"></a>Criando o renderizador personalizado no Android

O seguinte exemplo de código mostra o renderizador personalizado para a plataforma Android:

```csharp
using Xamarin.Forms.Platform.Android;

[assembly: ExportRenderer(typeof(MyEntry), typeof(MyEntryRenderer))]
namespace CustomRenderer.Android
{
    class MyEntryRenderer : EntryRenderer
    {
        public MyEntryRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(ElementChangedEventArgs<Entry> e)
        {
            base.OnElementChanged(e);

            if (Control != null)
            {
                Control.SetBackgroundColor(global::Android.Graphics.Color.LightGreen);
            }
        }
    }
}
```

A chamada ao método `OnElementChanged` da classe base cria uma instância de um controle `EditText` do Android, com uma referência ao controle que está sendo atribuído à propriedade `Control` do renderizador. A cor da tela de fundo é definida como verde-claro com o método `Control.SetBackgroundColor`.

### <a name="creating-the-custom-renderer-on-uwp"></a>Criando o renderizador personalizado na UWP

O seguinte exemplo de código mostra o renderizador personalizado para o UWP:

```csharp
[assembly: ExportRenderer(typeof(MyEntry), typeof(MyEntryRenderer))]
namespace CustomRenderer.UWP
{
    public class MyEntryRenderer : EntryRenderer
    {
        protected override void OnElementChanged(ElementChangedEventArgs<Entry> e)
        {
            base.OnElementChanged(e);

            if (Control != null)
            {
                Control.Background = new SolidColorBrush(Colors.Cyan);
            }
        }
    }
}
```

A chamada ao método `OnElementChanged` da classe base cria uma instância de um controle `TextBox`, com uma referência ao controle que está sendo atribuído à propriedade `Control` do renderizador. A cor da tela de fundo é então definida como ciano pela criação de uma instância `SolidColorBrush`.

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar um renderizador de controle personalizado para o controle [`Entry`](xref:Xamarin.Forms.Entry) do Xamarin.Forms, permitindo que os desenvolvedores substituam a renderização nativa padrão por sua própria personalização específica da plataforma. Os renderizadores personalizados fornecem uma abordagem eficiente para personalizar a aparência de controles do Xamarin.Forms. Eles podem ser usados para pequenas alterações de estilo ou personalização sofisticada de comportamento e de layout específico da plataforma.


## <a name="related-links"></a>Links relacionados

- [CustomRendererEntry (amostra)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/entry/)
