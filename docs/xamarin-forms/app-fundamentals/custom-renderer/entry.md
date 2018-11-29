---
title: Personalizar uma entrada
description: O controle de entrada do xamarin. Forms permite que uma única linha de texto a ser editado. Este artigo demonstra como criar um renderizador personalizado para o controle de entrada, permitindo que os desenvolvedores substituam a renderização de nativa padrão com sua próprias personalização específicas da plataforma.
ms.prod: xamarin
ms.assetid: 7B5DD10D-0411-424F-88D8-8A474DF16D8D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/26/2018
ms.openlocfilehash: 7fea736b0a04a69fd64100ae1d6bcd42c244359f
ms.sourcegitcommit: 2f6a5c1abf90fbdb0475fd8a3ce6de3cd7c7d575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52459844"
---
# <a name="customizing-an-entry"></a>Personalizar uma entrada

_O controle de entrada do xamarin. Forms permite que uma única linha de texto a ser editado. Este artigo demonstra como criar um renderizador personalizado para o controle de entrada, permitindo que os desenvolvedores substituam a renderização de nativa padrão com sua próprias personalização específicas da plataforma._

Todos os controles xamarin. Forms tem um renderizador que acompanha este artigo para cada plataforma que cria uma instância de um controle nativo. Quando um [ `Entry` ](xref:Xamarin.Forms.Entry) controle é processado por um aplicativo xamarin. Forms, no iOS o `EntryRenderer` classe é instanciada, que instancia um nativo `UITextField` controle. Na plataforma Android, o `EntryRenderer` cria uma instância de classe um `EditText` controle. Na Universal Windows Platform (UWP), o `EntryRenderer` cria uma instância de classe um `TextBox` controle. Para obter mais informações sobre as classes de controle nativo que mapeiam controles xamarin. Forms e o renderizador, consulte [Classes de Base do renderizador e controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

O diagrama a seguir ilustra o relacionamento entre o [ `Entry` ](xref:Xamarin.Forms.Entry) controle e os controles nativos correspondentes que implementação-lo:

![](entry-images/entry-classes.png "Relação entre o controle de entrada e a implementação de controles nativos")

O processo de renderização pode ser aproveitado para implementar personalizações específicas de plataforma, criando um renderizador personalizado para o [ `Entry` ](xref:Xamarin.Forms.Entry) controle em cada plataforma. O processo para fazer isso é o seguinte:

1. [Criar](#Creating_the_Custom_Entry_Control) um controle personalizado do xamarin. Forms.
1. [Consumir](#Consuming_the_Custom_Control) o controle personalizado do xamarin. Forms.
1. [Criar](#Creating_the_Custom_Renderer_on_each_Platform) o renderizador personalizado para o controle em cada plataforma.

Cada item agora será discutida por sua vez, para implementar uma [ `Entry` ](xref:Xamarin.Forms.Entry) controle que tem uma cor de plano de fundo diferente em cada plataforma.

> [!IMPORTANT]
> Este artigo explica como criar um renderizador personalizado simple. No entanto, não é necessário criar um renderizador personalizado para implementar um `Entry` que tem uma cor de plano de fundo diferente em cada plataforma. Isso pode ser feito com mais facilidade usando o [ `Device` ](xref:Xamarin.Forms.Device) classe, ou o `OnPlatform` extensão de marcação para fornecer valores específicos da plataforma. Para obter mais informações, consulte [fornecendo valores de específico da plataforma](~/xamarin-forms/platform/device.md#providing-platform-specific-values) e [extensão de marcação OnPlatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension).

<a name="Creating_the_Custom_Entry_Control" />

## <a name="creating-the-custom-entry-control"></a>Criando o controle de entrada personalizada

Um personalizado [ `Entry` ](xref:Xamarin.Forms.Entry) controle pode ser criado, a criação de subclasses de `Entry` controle, conforme mostrado no exemplo de código a seguir:

```csharp
public class MyEntry : Entry
{
}
```

O `MyEntry` controle é criada no projeto da biblioteca .NET Standard e é simplesmente uma [ `Entry` ](xref:Xamarin.Forms.Entry) controle. Personalização do controle será realizada no renderizador personalizado, portanto, nenhuma implementação adicional é necessária a `MyEntry` controle.

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>Consumindo o controle personalizado

O `MyEntry` controle pode ser referenciada em XAML no projeto da biblioteca .NET Standard, declarando um namespace para seu local e usando o prefixo de namespace no elemento de controle. O seguinte exemplo de código mostra como o `MyEntry` controle pode ser consumido por uma página XAML:

```xaml
<ContentPage ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <local:MyEntry Text="In Shared Code" />
    ...
</ContentPage>
```

O `local` prefixo de namespace pode ser qualquer nome. No entanto, o `clr-namespace` e `assembly` valores devem corresponder aos detalhes de controle personalizado. Depois que o namespace é declarado, que o prefixo é usado para referenciar o controle personalizado.

O seguinte exemplo de código mostra como o `MyEntry` controle pode ser consumido por uma página do c#:

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

Esse código cria uma nova [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) objeto exibirá um [ `Label` ](xref:Xamarin.Forms.Label) e `MyEntry` controle centralizado tanto verticalmente quanto horizontalmente na página.

Agora pode ser adicionado a um renderizador personalizado para cada projeto de aplicativo para personalizar a aparência do controle em cada plataforma.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Criando o renderizador personalizado em cada plataforma

O processo para criar a classe de renderizador personalizado é da seguinte maneira:

1. Criar uma subclasse do `EntryRenderer` classe que renderiza o controle nativo.
1. Substituir o `OnElementChanged` método que processa a lógica de controle e gravar nativa para personalizar o controle. Esse método é chamado quando o controle correspondente do xamarin. Forms é criado.
1. Adicionar um `ExportRenderer` atributo à classe de renderizador personalizado para especificar que será usada para renderizar o controle do xamarin. Forms. Este atributo é usado para registrar o renderizador personalizado com o xamarin. Forms.

> [!NOTE]
> É opcional fornecer um renderizador personalizado em cada projeto de plataforma. Se um renderizador personalizado não estiver registrado, o renderizador padrão para a classe base do controle será usado.

O diagrama a seguir ilustra as responsabilidades de cada projeto no aplicativo de exemplo, juntamente com as relações entre eles:

![](entry-images/solution-structure.png "Responsabilidades do projeto de renderizador personalizado MyEntry")

O `MyEntry` controle é processado pelo específicos da plataforma `MyEntryRenderer` classes, que derivam de `EntryRenderer` classe para cada plataforma. Isso resulta em cada `MyEntry` de controle que está sendo renderizado com uma cor de plano de fundo específica da plataforma, conforme mostrado nas capturas de tela seguir:

![](entry-images/screenshots.png "Controle MyEntry em cada plataforma")

O `EntryRenderer` classe expõe o `OnElementChanged` método, que é chamado quando o controle do xamarin. Forms é criado para renderizar o controle nativo correspondente. Esse método usa um `ElementChangedEventArgs` parâmetro que contém `OldElement` e `NewElement` propriedades. Essas propriedades representam o elemento do xamarin. Forms que o renderizador *foi* associada e o elemento do xamarin. Forms que o renderizador *é* anexado, respectivamente. No aplicativo de exemplo do `OldElement` propriedade será `null` e o `NewElement` propriedade conterá uma referência para o `MyEntry` controle.

Uma versão de substituição de `OnElementChanged` método no `MyEntryRenderer` classe é o lugar para realizar a personalização de controle nativo. Uma referência tipada para o controle nativo que está sendo usado na plataforma pode ser acessada por meio de `Control` propriedade. Além disso, uma referência para o controle de xamarin. Forms que está sendo renderizado pode ser obtida por meio de `Element` propriedade, embora não seja usado no aplicativo de exemplo.

Cada classe de renderizador personalizado é decorado com um `ExportRenderer` atributo que registra o renderizador com xamarin. Forms. O atributo utiliza dois parâmetros – o nome do tipo do controle xamarin. Forms que está sendo renderizado e o nome do tipo de renderizador personalizado. O `assembly` prefixo para o atributo especifica que o atributo se aplica a todo o assembly.

As seções a seguir discutem a implementação de cada específicos da plataforma `MyEntryRenderer` classe de renderizador personalizado.

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

A chamada para a classe base `OnElementChanged` método cria uma instância de um iOS `UITextField` controle, com uma referência para o controle que está sendo atribuído ao renderizador de `Control` propriedade. A cor do plano de fundo é definida como roxo-claro com o `UIColor.FromRGB` método.

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

A chamada para a classe base `OnElementChanged` método cria uma instância de um Android `EditText` controle, com uma referência para o controle que está sendo atribuído ao renderizador de `Control` propriedade. A cor do plano de fundo é definida como verde claro com o `Control.SetBackgroundColor` método.

### <a name="creating-the-custom-renderer-on-uwp"></a>Criando o renderizador personalizado na UWP

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

A chamada para a classe base `OnElementChanged` método instancia uma `TextBox` controle, com uma referência para o controle que está sendo atribuído ao renderizador de `Control` propriedade. A cor do plano de fundo é definida como ciano, criando um `SolidColorBrush` instância.

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar um renderizador de controle personalizado para o xamarin. Forms [ `Entry` ](xref:Xamarin.Forms.Entry) controle, permitindo que os desenvolvedores substituam a renderização de nativa padrão com sua própria renderização específicos da plataforma. Renderizadores personalizados fornecem uma abordagem eficiente para personalizar a aparência dos controles do xamarin. Forms. Eles podem ser usados para estilo pequenas alterações ou layout de específico da plataforma sofisticada e personalização de comportamento.


## <a name="related-links"></a>Links relacionados

- [CustomRendererEntry (amostra)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/entry/)
