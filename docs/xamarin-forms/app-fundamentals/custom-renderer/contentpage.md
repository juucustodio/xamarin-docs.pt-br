---
title: Personalizando um ContentPage
description: Um ContentPage é um elemento visual que exibe uma única e ocupa a maior parte da tela. Este artigo demonstra como criar um renderizador personalizado para a página de ContentPage, permitindo que os desenvolvedores substituir a renderização nativo padrão com sua próprias personalização específica de plataforma.
ms.prod: xamarin
ms.assetid: A4E61D93-73D9-4668-8D1C-DB6FC2491822
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 5fe7250b5b8fcea97d4fbe6846999be60e8e8673
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34848168"
---
# <a name="customizing-a-contentpage"></a>Personalizando um ContentPage

_Um ContentPage é um elemento visual que exibe uma única e ocupa a maior parte da tela. Este artigo demonstra como criar um renderizador personalizado para a página de ContentPage, permitindo que os desenvolvedores substituir a renderização nativo padrão com sua próprias personalização específica de plataforma._

Cada controle xamarin. Forms tem um processador que o acompanha para cada plataforma que cria uma instância de um controle nativo. Quando um [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) é processado por um aplicativo xamarin. Forms, no iOS a `PageRenderer` classe é instanciada, que por sua vez instancia um nativo `UIViewController` controle. Na plataforma Android, o `PageRenderer` classe instancia um `ViewGroup` controle. Sobre o Windows UWP (plataforma Universal), o `PageRenderer` classe instancia um `FrameworkElement` controle. Para obter mais informações sobre o processador e classes de controle nativo que mapeiam xamarin. Forms controles, consulte [Classes de Base de processador e controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

O diagrama a seguir ilustra o relacionamento entre o [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) e os controles nativo correspondentes que implementação-la:

![](contentpage-images/contentpage-classes.png "Relação entre a classe ContentPage e implementar controles nativos")

O processo de renderização pode ser tomado aproveitar para implementar personalizações específicas da plataforma, criando um renderizador personalizado para um [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) em cada plataforma. O processo para fazer isso é como segue:

1. [Criar](#Creating_the_Xamarin.Forms_Page) uma página xamarin. Forms.
1. [Consumir](#Consuming_the_Xamarin.Forms_Page) a página xamarin. Forms.
1. [Criar](#Creating_the_Page_Renderer_on_each_Platform) renderizador personalizado para a página em cada plataforma.

Cada item agora será discutida por sua vez, para implementar um `CameraPage` que fornece uma feed de câmera ao vivo e a capacidade de capturar uma foto.

<a name="Creating_the_Xamarin.Forms_Page" />

## <a name="creating-the-xamarinforms-page"></a>Criando a página xamarin. Forms

Um inalterados [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) podem ser adicionados ao projeto compartilhado xamarin. Forms, conforme mostrado no exemplo de código XAML a seguir:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CustomRenderer.CameraPage">
    <ContentPage.Content>
    </ContentPage.Content>
</ContentPage>
```

Da mesma forma, o arquivo code-behind para o [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) também deve permanecer inalterada, conforme mostrado no exemplo de código a seguir:

```csharp
public partial class CameraPage : ContentPage
{
    public CameraPage ()
    {
        // A custom renderer is used to display the camera UI
        InitializeComponent ();
    }
}
```

O exemplo de código a seguir mostra como a página pode ser criada em c#:

```csharp
public class CameraPageCS : ContentPage
{
    public CameraPageCS ()
    {
    }
}
```

Uma instância do `CameraPage` será usado para exibir a câmera em tempo real de alimentação em cada plataforma. Personalização do controle será realizada no renderizador personalizado, portanto nenhuma implementação adicional é necessário o `CameraPage` classe.

<a name="Consuming_the_Xamarin.Forms_Page" />

## <a name="consuming-the-xamarinforms-page"></a>Consumindo a página xamarin. Forms

Vazio `CameraPage` deve ser exibido pelo aplicativo xamarin. Forms. Isso ocorre quando um botão de `MainPage` instância é tocada, que por sua vez executa o `OnTakePhotoButtonClicked` método, conforme mostrado no exemplo de código a seguir:

```csharp
async void OnTakePhotoButtonClicked (object sender, EventArgs e)
{
    await Navigation.PushAsync (new CameraPage ());
}
```

Esse código simplesmente navega para o `CameraPage`, no qual renderizadores personalizados que deseja personalizar a aparência da página em cada plataforma.

<a name="Creating_the_Page_Renderer_on_each_Platform" />

## <a name="creating-the-page-renderer-on-each-platform"></a>Criando o renderizador de página em cada plataforma

O processo para criar a classe de renderizador personalizado é o seguinte:

1. Criar uma subclasse do `PageRenderer` classe.
1. Substituir o `OnElementChanged` método que processa a lógica de página e de gravação nativa para personalizar a página. O `OnElementChanged` método é chamado quando o controle xamarin. Forms correspondente é criado.
1. Adicionar um `ExportRenderer` de atributo para a classe de renderizador de página para especificar que será usada para renderizar a página xamarin. Forms. Este atributo é usado para registrar o renderizador personalizado com xamarin. Forms.

> [!NOTE]
> É opcional fornecer um renderizador de página em cada projeto da plataforma. Se um renderizador de página não estiver registrado, será usado o renderizador padrão para a página.

O diagrama a seguir ilustra as responsabilidades de cada projeto de aplicativo de exemplo, juntamente com a relação entre eles:

![](contentpage-images/solution-structure.png "Responsabilidades de projeto do renderizador CameraPage personalizado")

O `CameraPage` instância é processada pelo específico da plataforma `CameraPageRenderer` classes que derivam de `PageRenderer` classe para a plataforma. Isso resulta em cada `CameraPage` instância que está sendo processada com um feed de câmera em tempo real, conforme mostrado nas capturas de tela seguir:

![](contentpage-images/screenshots.png "CameraPage em cada plataforma")

O `PageRenderer` classe expõe o `OnElementChanged` método, que é chamado quando a página xamarin. Forms é criada para renderizar o controle nativo correspondente. Esse método usa um `ElementChangedEventArgs` parâmetro que contém `OldElement` e `NewElement` propriedades. Essas propriedades representam o elemento xamarin. Forms que o renderizador *foi* associada e o elemento xamarin. Forms que o renderizador *é* anexado, respectivamente. No aplicativo de amostra a `OldElement` propriedade será `null` e `NewElement` propriedade conterá uma referência para a `CameraPage` instância.

Uma versão de substituição de `OnElementChanged` método o `CameraPageRenderer` classe é o local para executar a personalização da página nativo. Uma referência à instância page xamarin. Forms que está sendo renderizada pode ser obtida por meio de `Element` propriedade.

Cada classe de renderizador personalizado é decorado com um `ExportRenderer` atributo que registra o renderizador com xamarin. Forms. O atributo utiliza dois parâmetros – o nome do tipo da página xamarin. Forms está sendo processado e o nome do tipo de renderizador personalizado. O `assembly` prefixo para o atributo especifica que o atributo se aplica ao assembly inteiro.

As seções a seguir abordam a implementação de `CameraPageRenderer` renderizador personalizado para cada plataforma.

### <a name="creating-the-page-renderer-on-ios"></a>Criando o renderizador de página no iOS

O exemplo de código a seguir mostra o renderizador de página para a plataforma iOS:

```csharp
[assembly:ExportRenderer (typeof(CameraPage), typeof(CameraPageRenderer))]
namespace CustomRenderer.iOS
{
    public class CameraPageRenderer : PageRenderer
    {
        ...

        protected override void OnElementChanged (VisualElementChangedEventArgs e)
        {
            base.OnElementChanged (e);

            if (e.OldElement != null || Element == null) {
                return;
            }

            try {
                SetupUserInterface ();
                SetupEventHandlers ();
                SetupLiveCameraStream ();
                AuthorizeCameraUse ();
            } catch (Exception ex) {
                System.Diagnostics.Debug.WriteLine (@"            ERROR: ", ex.Message);
            }
        }
        ...
    }
}
```

A chamada para a classe base `OnElementChanged` método instancia um iOS `UIViewController` controle. O fluxo ao vivo câmera só é processado desde que o processador não estiver anexado a um elemento xamarin. Forms existente, e desde que existe uma instância de página que está sendo renderizado pelo renderizador personalizado.

A página é personalizada, em seguida, por uma série de métodos que usam o `AVCapture` APIs para fornecer a transmissão ao vivo de câmera e a capacidade de capturar uma foto.

### <a name="creating-the-page-renderer-on-android"></a>Criando o renderizador de página no Android

O exemplo de código a seguir mostra o renderizador de página para a plataforma Android:

```csharp
[assembly: ExportRenderer(typeof(CameraPage), typeof(CameraPageRenderer))]
namespace CustomRenderer.Droid
{
    public class CameraPageRenderer : PageRenderer, TextureView.ISurfaceTextureListener
    {
        ...
        public CameraPageRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(ElementChangedEventArgs<Page> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null || Element == null)
            {
                return;
            }

            try
            {
                SetupUserInterface();
                SetupEventHandlers();
                AddView(view);
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(@"            ERROR: ", ex.Message);
            }
        }
        ...
    }
}
```

A chamada para a classe base `OnElementChanged` método instancia um Android `ViewGroup` controle, que é um grupo de modos de exibição. O fluxo ao vivo câmera só é processado desde que o processador não estiver anexado a um elemento xamarin. Forms existente, e desde que existe uma instância de página que está sendo renderizado pelo renderizador personalizado.

A página, em seguida, é personalizada com a invocação de uma série de métodos que usam o `Camera` API para fornecer a transmissão ao vivo de câmera e a capacidade de capturar uma foto, antes de `AddView` método é invocado para adicionar a câmera em tempo real fluxo de interface do usuário para o `ViewGroup`.

### <a name="creating-the-page-renderer-on-uwp"></a>Criando o renderizador de página em UWP

O exemplo de código a seguir mostra o renderizador de página de UWP:

```csharp
[assembly: ExportRenderer(typeof(CameraPage), typeof(CameraPageRenderer))]
namespace CustomRenderer.UWP
{
    public class CameraPageRenderer : PageRenderer
    {
        ...
        protected override void OnElementChanged(ElementChangedEventArgs<Xamarin.Forms.Page> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null || Element == null)
            {
                return;
            }

            try
            {
                ...
                SetupUserInterface();
                SetupBasedOnStateAsync();

                this.Children.Add(page);
            }
            ...
        }

        protected override Size ArrangeOverride(Size finalSize)
        {
            page.Arrange(new Windows.Foundation.Rect(0, 0, finalSize.Width, finalSize.Height));
            return finalSize;
        }
        ...
    }
}

```

A chamada para a classe base `OnElementChanged` método instancia um `FrameworkElement` controle, no qual a página é renderizada. O fluxo ao vivo câmera só é processado desde que o processador não estiver anexado a um elemento xamarin. Forms existente, e desde que existe uma instância de página que está sendo renderizado pelo renderizador personalizado. A página, em seguida, é personalizada com a invocação de uma série de métodos que usam o `MediaCapture` API para fornecer a transmissão ao vivo de câmera e a capacidade de capturar uma foto antes da página personalizada é adicionada para o `Children` coleção para exibição.

Ao implementar um renderizador personalizado que é derivada de `PageRenderer` em UWP, o `ArrangeOverride` método também deve ser implementado para organizar os controles da página, porque o processador de base não sabe o que fazer com elas. Caso contrário, resultados de uma página em branco. Portanto, no exemplo de `ArrangeOverride` chamadas de método de `Arrange` método na `Page` instância.

> [!NOTE]
> É importante parar e descartar os objetos que fornecem acesso à câmera em um aplicativo de UWP. Falha ao fazer isso pode interferir em outros aplicativos que tentam acessar a câmera do dispositivo. Para obter mais informações, consulte [exibir a visualização de câmera](https://msdn.microsoft.com/windows/uwp/audio-video-camera/simple-camera-preview-access).

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar um renderizador personalizado para o [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) página, permitindo que os desenvolvedores substituir a renderização nativo padrão com sua próprias personalização específica de plataforma. Um `ContentPage` é um elemento visual que exibe uma única e ocupa a maior parte da tela.


## <a name="related-links"></a>Links relacionados

- [CustomRendererContentPage (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/contentpage/)
