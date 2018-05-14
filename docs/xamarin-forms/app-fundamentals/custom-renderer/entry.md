---
title: Personalizando uma entrada
description: O controle de entrada do xamarin. Forms permite uma única linha de texto a ser editado. Este artigo demonstra como criar um renderizador personalizado para o controle de entrada, permitindo que os desenvolvedores substituir a renderização nativo padrão com sua próprias personalização específica de plataforma.
ms.prod: xamarin
ms.assetid: 7B5DD10D-0411-424F-88D8-8A474DF16D8D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: c93681c3bfd8de8d813cbe98a7ac28b3ee8b74fc
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="customizing-an-entry"></a>Personalizando uma entrada

_O controle de entrada do xamarin. Forms permite uma única linha de texto a ser editado. Este artigo demonstra como criar um renderizador personalizado para o controle de entrada, permitindo que os desenvolvedores substituir a renderização nativo padrão com sua próprias personalização específica de plataforma._

Cada controle xamarin. Forms tem um processador que o acompanha para cada plataforma que cria uma instância de um controle nativo. Quando um [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controle é processado por um aplicativo xamarin. Forms, no iOS a `EntryRenderer` classe é instanciada, que por sua vez instancia um nativo `UITextField` controle. Na plataforma Android, o `EntryRenderer` classe instancia um `EditText` controle. Sobre o Windows UWP (plataforma Universal), o `EntryRenderer` classe instancia um `TextBox` controle. Para obter mais informações sobre o processador e classes de controle nativo que mapeiam xamarin. Forms controles, consulte [Classes de Base de processador e controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

O diagrama a seguir ilustra o relacionamento entre o [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controle e os controles nativo correspondentes que implementação-la:

![](entry-images/entry-classes.png "Relação entre o controle de entrada e implementar controles nativos")

O processo de renderização pode ser tomado aproveitar para implementar personalizações específicas da plataforma, criando um renderizador personalizado para o [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controle em cada plataforma. O processo para fazer isso é como segue:

1. [Criar](#Creating_the_Custom_Entry_Control) um controle personalizado do xamarin. Forms.
1. [Consumir](#Consuming_the_Custom_Control) o controle personalizado do xamarin. Forms.
1. [Criar](#Creating_the_Custom_Renderer_on_each_Platform) renderizador personalizado para o controle em cada plataforma.

Cada item agora será discutida por sua vez, para implementar um [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controle que tem uma cor de plano de fundo diferente em cada plataforma.

<a name="Creating_the_Custom_Entry_Control" />

## <a name="creating-the-custom-entry-control"></a>Criando o controle de entrada personalizada

Um personalizado [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controle pode ser criado, subclassificação o `Entry` controlar, conforme mostrado no exemplo de código a seguir:

```csharp
public class MyEntry : Entry
{
}
```

O `MyEntry` controle é criado no projeto da biblioteca .NET padrão e é simplesmente uma [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controle. Personalização do controle será realizada no renderizador personalizado, portanto nenhuma implementação adicional é necessário o `MyEntry` controle.

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>Consumindo o controle personalizado

O `MyEntry` controle pode ser referenciado em XAML no projeto da biblioteca .NET padrão declarando um namespace para seu local e usando o prefixo de namespace no elemento de controle. O seguinte exemplo de código mostra como o `MyEntry` controle pode ser consumido por uma página XAML:

```xaml
<ContentPage ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <local:MyEntry Text="In Shared Code" />
    ...
</ContentPage>
```

O `local` o prefixo do namespace pode ser qualquer nome. No entanto, o `clr-namespace` e `assembly` valores devem coincidir com os detalhes do controle personalizado. Depois que o namespace é declarado que o prefixo é usado para referenciar o controle personalizado.

O seguinte exemplo de código mostra como o `MyEntry` controle pode ser consumido por uma página c#:

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

Esse código cria um novo [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) objeto exibirá um [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) e `MyEntry` controle centralizado ambos vertical e horizontalmente na página.

Agora é possível adicionar um renderizador personalizado para cada projeto de aplicativo para personalizar a aparência do controle em cada plataforma.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Criando o renderizador personalizado em cada plataforma

O processo para criar a classe de renderizador personalizado é o seguinte:

1. Criar uma subclasse do `EntryRenderer` classe que renderiza o controle nativo.
1. Substituir o `OnElementChanged` método que processa a lógica de controle e gravar nativo para personalizar o controle. Esse método é chamado quando o controle xamarin. Forms correspondente é criado.
1. Adicionar um `ExportRenderer` de atributo para a classe de renderizador personalizado para especificar que será usada para renderizar o controle xamarin. Forms. Este atributo é usado para registrar o renderizador personalizado com xamarin. Forms.

> [!NOTE]
> É opcional fornecer um renderizador personalizado em cada projeto da plataforma. Se um renderizador personalizado não estiver registrado, será usado o renderizador padrão para a classe base do controle.

O diagrama a seguir ilustra as responsabilidades de cada projeto de aplicativo de exemplo, juntamente com as relações entre eles:

![](entry-images/solution-structure.png "Responsabilidades de projeto do renderizador MyEntry personalizado")

O `MyEntry` controle é processado pelo específico da plataforma `MyEntryRenderer` classes que derivam de `EntryRenderer` classe para cada plataforma. Isso resulta em cada `MyEntry` controle está sendo processado com uma cor de plano de fundo de plataforma específica, conforme mostrado nas capturas de tela seguir:

![](entry-images/screenshots.png "Controle de MyEntry em cada plataforma")

O `EntryRenderer` classe expõe o `OnElementChanged` método, que é chamado quando o controle xamarin. Forms é criado para renderizar o controle nativo correspondente. Esse método usa um `ElementChangedEventArgs` parâmetro que contém `OldElement` e `NewElement` propriedades. Essas propriedades representam o elemento xamarin. Forms que o renderizador *foi* associada e o elemento xamarin. Forms que o renderizador *é* anexado, respectivamente. No aplicativo de amostra a `OldElement` propriedade será `null` e `NewElement` propriedade conterá uma referência para o `MyEntry` controle.

Uma versão de substituição de `OnElementChanged` método o `MyEntryRenderer` classe é o local para executar a personalização do controle nativo. Uma referência de tipo para o controle nativo que está sendo usado na plataforma pode ser acessada por meio de `Control` propriedade. Além disso, uma referência para o controle de xamarin. Forms que está sendo renderizado pode ser obtida por meio de `Element` propriedade, embora ele não é usado no aplicativo de exemplo.

Cada classe de renderizador personalizado é decorado com um `ExportRenderer` atributo que registra o renderizador com xamarin. Forms. O atributo utiliza dois parâmetros – o nome do tipo do controle xamarin. Forms está sendo processado e o nome do tipo de renderizador personalizado. O `assembly` prefixo para o atributo especifica que o atributo se aplica ao assembly inteiro.

As seções a seguir abordam a implementação de cada plataforma específica `MyEntryRenderer` classe renderizador personalizado.

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

A chamada para a classe base `OnElementChanged` método instancia um iOS `UITextField` controle, com uma referência para o controle que está sendo atribuído ao renderizador de `Control` propriedade. A cor de plano de fundo é definida como lilás com o `UIColor.FromRGB` método.

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

A chamada para a classe base `OnElementChanged` método instancia um Android `EditText` controle, com uma referência para o controle que está sendo atribuído ao renderizador de `Control` propriedade. A cor de plano de fundo é definida como verde-claro com o `Control.SetBackgroundColor` método.

### <a name="creating-the-custom-renderer-on-uwp"></a>Criando o renderizador personalizado em UWP

O exemplo de código a seguir mostra o renderizador personalizado para UWP:

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

A chamada para a classe base `OnElementChanged` método instancia um `TextBox` controle, com uma referência para o controle que está sendo atribuído ao renderizador de `Control` propriedade. A cor de plano de fundo é definida como ciano criando um `SolidColorBrush` instância.

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar um renderizador de controle personalizado para o xamarin. Forms [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controle, permitindo que os desenvolvedores substituir a renderização nativo padrão com sua próprias renderização específico de plataforma. Renderizadores personalizados fornecem uma abordagem avançada para personalizar a aparência dos controles xamarin. Forms. Eles podem ser usados para estilo pequenas alterações ou layout de específico de plataforma sofisticada e personalização de comportamento.


## <a name="related-links"></a>Links relacionados

- [CustomRendererEntry (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/entry/)
