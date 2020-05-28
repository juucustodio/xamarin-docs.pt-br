---
title: ''
description: O Xamarin.Forms controle de entrada permite que uma única linha de texto seja editada. Este artigo demonstra como criar um renderizador personalizado para o controle Entry, permitindo que os desenvolvedores substituam a renderização nativa padrão por sua própria personalização específica da plataforma.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 61bd66fd25b7aea3e5be346f79e63d410164b002
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138977"
---
# <a name="customizing-an-entry"></a>Personalizando uma entrada

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-entry)

_O Xamarin.Forms controle de entrada permite que uma única linha de texto seja editada. Este artigo demonstra como criar um renderizador personalizado para o controle de entrada, permitindo que os desenvolvedores substituam a renderização nativa padrão por sua própria personalização específica da plataforma._

Cada Xamarin.Forms controle tem um renderizador que acompanha para cada plataforma que cria uma instância de um controle nativo. Quando um [`Entry`](xref:Xamarin.Forms.Entry) controle é renderizado por um Xamarin.Forms aplicativo, no Ios a `EntryRenderer` classe é instanciada, o que, por sua instância, instancia um `UITextField` controle nativo. Na plataforma Android, a classe `EntryRenderer` cria uma instância de um controle `EditText`. No UWP (Plataforma Universal do Windows), a classe `EntryRenderer` cria uma instância de um controle `TextBox`. Para obter mais informações sobre o renderizador e as classes de controle nativo que Xamarin.Forms controlam o mapa para o, consulte [classes base do processador e controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

O diagrama a seguir ilustra a relação entre o [`Entry`](xref:Xamarin.Forms.Entry) controle e os controles nativos correspondentes que o implementam:

![](entry-images/entry-classes.png "Relationship Between Entry Control and Implementing Native Controls")

O processo de renderização pode ser aproveitado para implementar personalizações específicas da plataforma criando um renderizador personalizado para o [`Entry`](xref:Xamarin.Forms.Entry) controle em cada plataforma. O processo para fazer isso é o seguinte:

1. [Crie](#Creating_the_Custom_Entry_Control) um Xamarin.Forms controle personalizado.
1. [Consuma](#Consuming_the_Custom_Control) o controle personalizado do Xamarin.Forms .
1. [Criar](#Creating_the_Custom_Renderer_on_each_Platform) o renderizador personalizado para o controle em cada plataforma.

Cada item agora será discutido, por sua vez, para implementar um [`Entry`](xref:Xamarin.Forms.Entry) controle que tenha uma cor de plano de fundo diferente em cada plataforma.

> [!IMPORTANT]
> Este artigo explica como criar um renderizador personalizado simples. No entanto, não é necessário criar um renderizador personalizado para implementar uma `Entry` que tenha uma cor da tela de fundo diferente em cada plataforma. Isso pode ser feito com mais facilidade usando a [`Device`](xref:Xamarin.Forms.Device) classe, ou a `OnPlatform` extensão de marcação, para fornecer valores específicos da plataforma. Para obter mais informações, confira [Fornecendo valores específicos da plataforma](~/xamarin-forms/platform/device.md#provide-platform-specific-values) e [Extensão de marcação OnPlatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension).

<a name="Creating_the_Custom_Entry_Control" />

## <a name="creating-the-custom-entry-control"></a>Criando o controle de entrada personalizado

Um [`Entry`](xref:Xamarin.Forms.Entry) controle personalizado pode ser criado por meio da subclasse do `Entry` controle, conforme mostrado no exemplo de código a seguir:

```csharp
public class MyEntry : Entry
{
}
```

O `MyEntry` controle é criado no projeto de biblioteca de .net Standard e é simplesmente um [`Entry`](xref:Xamarin.Forms.Entry) controle. A personalização do controle será realizada no renderizador personalizado. Portanto, nenhuma implementação adicional é necessária no controle `MyEntry`.

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

O prefixo do namespace `local` pode ser qualquer nome. No entanto, os valores de `clr-namespace` e `assembly` devem corresponder aos detalhes do controle personalizado. Quando o namespace é declarado, o prefixo é usado para referenciar o controle personalizado.

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

Esse código instancia um novo [`ContentPage`](xref:Xamarin.Forms.ContentPage) objeto que exibirá um [`Label`](xref:Xamarin.Forms.Label) controle e será `MyEntry` centralizado verticalmente e horizontalmente na página.

Agora, um renderizador personalizado pode ser adicionado a cada projeto de aplicativo para personalizar a aparência do controle em cada plataforma.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Criando o renderizador personalizado em cada plataforma

O processo para criar a classe do renderizador personalizado é a seguinte:

1. Criar uma subclasse da classe `EntryRenderer` que renderiza o controle nativo.
1. Substituir o método `OnElementChanged` que renderiza o controle nativo e escrever a lógica para personalizá-lo. Esse método é chamado quando o Xamarin.Forms controle correspondente é criado.
1. Adicione um `ExportRenderer` atributo à classe de processador personalizado para especificar que ele será usado para renderizar o Xamarin.Forms controle. Esse atributo é usado para registrar o renderizador personalizado com Xamarin.Forms .

> [!NOTE]
> O fornecimento de um renderizador personalizado em cada projeto da plataforma é opcional. Se um renderizador personalizado não estiver registrado, será usado o renderizador padrão da classe base do controle.

O diagrama a seguir ilustra as responsabilidades de cada projeto no aplicativo de exemplo, bem como as relações entre elas:

![](entry-images/solution-structure.png "MyEntry Custom Renderer Project Responsibilities")

O controle `MyEntry` é renderizado por classes `MyEntryRenderer` específicas da plataforma, que derivam da classe `EntryRenderer` para cada plataforma. Isso faz com que cada controle `MyEntry` seja renderizado com a cor da tela de fundo específica da plataforma, conforme mostrado nas seguintes capturas de tela:

![](entry-images/screenshots.png "MyEntry Control on each Platform")

A `EntryRenderer` classe expõe o `OnElementChanged` método, que é chamado quando o Xamarin.Forms controle é criado para renderizar o controle nativo correspondente. Esse método usa um parâmetro `ElementChangedEventArgs`, que contém as propriedades `OldElement` e `NewElement`. Essas propriedades representam o Xamarin.Forms elemento ao qual o renderizador *foi* anexado e o Xamarin.Forms elemento ao qual o renderizador *está* anexado, respectivamente. No aplicativo de exemplo, a propriedade `OldElement` será `null` e a propriedade `NewElement` conterá uma referência ao controle `MyEntry`.

Uma versão de substituição do método `OnElementChanged` na classe `MyEntryRenderer` é o lugar para realizar a personalização do controle nativo. Uma referência tipada ao controle nativo que está sendo usado na plataforma pode ser acessada por meio da propriedade `Control`. Além disso, uma referência ao Xamarin.Forms controle que está sendo processado pode ser obtida por meio da `Element` propriedade, embora não seja usada no aplicativo de exemplo.

Cada classe de processador personalizado é decorada com um `ExportRenderer` atributo que registra o renderizador com Xamarin.Forms . O atributo usa dois parâmetros – o nome do tipo do Xamarin.Forms controle que está sendo renderizado e o nome do tipo do renderizador personalizado. O prefixo `assembly` do atributo especifica que o atributo se aplica a todo o assembly.

As seções a seguir abordam a implementação de cada classe de renderizador personalizado `MyEntryRenderer` específica da plataforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Criando o renderizador personalizado no iOS

O exemplo de código a seguir mostra o renderizador personalizado para a plataforma iOS:

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

O exemplo de código a seguir mostra o renderizador personalizado para a plataforma Android:

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

Este artigo demonstrou como criar um renderizador de controle personalizado para o Xamarin.Forms [`Entry`](xref:Xamarin.Forms.Entry) controle, permitindo que os desenvolvedores substituam a renderização nativa padrão por sua própria renderização específica da plataforma. Os renderizadores personalizados fornecem uma abordagem poderosa para personalizar a aparência dos Xamarin.Forms controles. Eles podem ser usados para pequenas alterações de estilo ou personalização sofisticada de comportamento e de layout específico da plataforma.

## <a name="related-links"></a>Links relacionados

- [CustomRendererEntry (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-entry)
