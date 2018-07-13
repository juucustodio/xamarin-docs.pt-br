---
title: Personalizar um ContentPage
description: Um ContentPage é um elemento visual que exibe uma única exibição e ocupa a maior parte da tela. Este artigo demonstra como criar um renderizador personalizado para a página com ContentPage, permitindo que os desenvolvedores substituam a renderização de nativa padrão com sua próprias personalização específicas da plataforma.
ms.prod: xamarin
ms.assetid: A4E61D93-73D9-4668-8D1C-DB6FC2491822
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 2369b249681b926476cf3938c51c99745eba9098
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995736"
---
# <a name="customizing-a-contentpage"></a>Personalizar um ContentPage

_Um ContentPage é um elemento visual que exibe uma única exibição e ocupa a maior parte da tela. Este artigo demonstra como criar um renderizador personalizado para a página com ContentPage, permitindo que os desenvolvedores substituam a renderização de nativa padrão com sua próprias personalização específicas da plataforma._

Todos os controles xamarin. Forms tem um renderizador que acompanha este artigo para cada plataforma que cria uma instância de um controle nativo. Quando um [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) é processado por um aplicativo xamarin. Forms, no iOS o `PageRenderer` classe é instanciada, que por sua vez cria uma instância de um nativo `UIViewController` controle. Na plataforma Android, o `PageRenderer` cria uma instância de classe um `ViewGroup` controle. Na Universal Windows Platform (UWP), o `PageRenderer` cria uma instância de classe um `FrameworkElement` controle. Para obter mais informações sobre as classes de controle nativo que mapeiam controles xamarin. Forms e o renderizador, consulte [Classes de Base do renderizador e controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

O diagrama a seguir ilustra o relacionamento entre o [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) e controles nativos correspondentes que implementação-lo:

![](contentpage-images/contentpage-classes.png "Relação entre a classe do ContentPage e a implementação de controles nativos")

O processo de renderização pode ser aproveitado para implementar personalizações específicas de plataforma, criando um renderizador personalizado para um [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) em cada plataforma. O processo para fazer isso é o seguinte:

1. [Criar](#Creating_the_Xamarin.Forms_Page) uma página do xamarin. Forms.
1. [Consumir](#Consuming_the_Xamarin.Forms_Page) a página do xamarin. Forms.
1. [Criar](#Creating_the_Page_Renderer_on_each_Platform) o renderizador personalizado para a página em cada plataforma.

Cada item agora será discutida por sua vez, para implementar um `CameraPage` que fornece uma feed de câmera em tempo real e a capacidade de capturar uma foto.

<a name="Creating_the_Xamarin.Forms_Page" />

## <a name="creating-the-xamarinforms-page"></a>Criando a página do xamarin. Forms

Um inalterados [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) podem ser adicionados ao projeto xamarin. Forms compartilhado, conforme mostrado no exemplo de código XAML a seguir:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CustomRenderer.CameraPage">
    <ContentPage.Content>
    </ContentPage.Content>
</ContentPage>
```

Da mesma forma, o arquivo code-behind para o [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) deve também permanecem inalterados, conforme mostrado no exemplo de código a seguir:

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

Uma instância da `CameraPage` será usado para exibir o feed em cada plataforma de câmera em tempo real. Personalização do controle será realizada no renderizador personalizado, portanto, nenhuma implementação adicional é necessária a `CameraPage` classe.

<a name="Consuming_the_Xamarin.Forms_Page" />

## <a name="consuming-the-xamarinforms-page"></a>Consumindo a página do xamarin. Forms

Vazio `CameraPage` deve ser exibido pelo aplicativo xamarin. Forms. Isso ocorre quando um botão na `MainPage` instância é tocada, que por sua vez executa o `OnTakePhotoButtonClicked` método, conforme mostrado no exemplo de código a seguir:

```csharp
async void OnTakePhotoButtonClicked (object sender, EventArgs e)
{
    await Navigation.PushAsync (new CameraPage ());
}
```

Esse código simplesmente navega para o `CameraPage`, no quais os renderizadores personalizados que deseja personalizar a aparência da página em cada plataforma.

<a name="Creating_the_Page_Renderer_on_each_Platform" />

## <a name="creating-the-page-renderer-on-each-platform"></a>Criando o renderizador de página em cada plataforma

O processo para criar a classe de renderizador personalizado é da seguinte maneira:

1. Criar uma subclasse do `PageRenderer` classe.
1. Substituir o `OnElementChanged` método que processa a lógica de página e de gravação nativa para personalizar a página. O `OnElementChanged` método é chamado quando o controle correspondente do xamarin. Forms é criado.
1. Adicionar um `ExportRenderer` atributo à classe de renderizador de página para especificar que ele será usado para renderizar a página do xamarin. Forms. Este atributo é usado para registrar o renderizador personalizado com o xamarin. Forms.

> [!NOTE]
> É opcional fornecer um renderizador de página em cada projeto de plataforma. Se um renderizador de página não estiver registrado, o renderizador padrão para a página será usado.

O diagrama a seguir ilustra as responsabilidades de cada projeto no aplicativo de exemplo, juntamente com a relação entre eles:

![](contentpage-images/solution-structure.png "Responsabilidades do projeto de renderizador personalizado CameraPage")

O `CameraPage` instância é processada pelo específicos da plataforma `CameraPageRenderer` classes, que derivam de `PageRenderer` classe para a plataforma. Isso resulta em cada `CameraPage` da instância que está sendo renderizado com um feed de câmera em tempo real, conforme mostrado nas capturas de tela seguir:

![](contentpage-images/screenshots.png "CameraPage em cada plataforma")

O `PageRenderer` classe expõe o `OnElementChanged` método, que é chamado quando a página do xamarin. Forms é criada para renderizar o controle nativo correspondente. Esse método usa um `ElementChangedEventArgs` parâmetro que contém `OldElement` e `NewElement` propriedades. Essas propriedades representam o elemento do xamarin. Forms que o renderizador *foi* associada e o elemento do xamarin. Forms que o renderizador *é* anexado, respectivamente. No aplicativo de exemplo do `OldElement` propriedade será `null` e o `NewElement` propriedade conterá uma referência para o `CameraPage` instância.

Uma versão de substituição a `OnElementChanged` método no `CameraPageRenderer` classe é o lugar para realizar a personalização de página nativa. Uma referência à instância de página do xamarin. Forms que está sendo renderizada pode ser obtida por meio de `Element` propriedade.

Cada classe de renderizador personalizado é decorado com um `ExportRenderer` atributo que registra o renderizador com xamarin. Forms. O atributo utiliza dois parâmetros – o nome do tipo da página xamarin. Forms que está sendo renderizado e o nome do tipo de renderizador personalizado. O `assembly` prefixo para o atributo especifica que o atributo se aplica a todo o assembly.

As seções a seguir discutem a implementação do `CameraPageRenderer` renderizador personalizado para cada plataforma.

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

A chamada para a classe base `OnElementChanged` método cria uma instância de um iOS `UIViewController` controle. O fluxo ao vivo câmera só é processado desde que o renderizador não estiver anexado a um elemento existente do xamarin. Forms, e desde que exista uma instância de página que está sendo processado, o renderizador personalizado.

A página, em seguida, é personalizada por uma série de métodos que usam o `AVCapture` APIs para fornecer a transmissão ao vivo da câmera e a capacidade de capturar uma foto.

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

A chamada para a classe base `OnElementChanged` método cria uma instância de um Android `ViewGroup` controle, que é um grupo de modos de exibição. O fluxo ao vivo câmera só é processado desde que o renderizador não estiver anexado a um elemento existente do xamarin. Forms, e desde que exista uma instância de página que está sendo processado, o renderizador personalizado.

A página é personalizada, em seguida, invocando uma série de métodos que usam o `Camera` API para fornecer a transmissão ao vivo da câmera e a capacidade de capturar uma foto, antes o `AddView` método é invocado para adicionar a câmera ao vivo transmitir a interface do usuário para o `ViewGroup`.

### <a name="creating-the-page-renderer-on-uwp"></a>Criando o renderizador de página na UWP

O exemplo de código a seguir mostra o renderizador de página para UWP:

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

A chamada para a classe base `OnElementChanged` método instancia um `FrameworkElement` controle, em que a página é renderizada. O fluxo ao vivo câmera só é processado desde que o renderizador não estiver anexado a um elemento existente do xamarin. Forms, e desde que exista uma instância de página que está sendo processado, o renderizador personalizado. A página é personalizada, em seguida, invocando uma série de métodos que usam o `MediaCapture` API para fornecer a transmissão ao vivo da câmera e a capacidade de capturar uma foto antes que a página personalizada é adicionada ao `Children` coleção para exibição.

Ao implementar um renderizador personalizado que deriva `PageRenderer` na UWP, o `ArrangeOverride` método também deve ser implementado para organizar os controles de página, porque o renderizador de base não sabe o que fazer com eles. Caso contrário, resultados de uma página em branco. Portanto, no exemplo o `ArrangeOverride` chamadas de método de `Arrange` método no `Page` instância.

> [!NOTE]
> É importante parar e descartar os objetos que fornecem acesso à câmera em um aplicativo UWP. Falha ao fazer isso pode interferir em outros aplicativos que tentam acessar a câmera do dispositivo. Para obter mais informações, consulte [exibir a visualização da câmera](https://msdn.microsoft.com/windows/uwp/audio-video-camera/simple-camera-preview-access).

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar um renderizador personalizado para o [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) página, permitindo que os desenvolvedores substituam a renderização de nativa padrão com sua próprias personalização específicas da plataforma. Um `ContentPage` é um elemento visual que exibe uma única exibição e ocupa a maior parte da tela.


## <a name="related-links"></a>Links relacionados

- [CustomRendererContentPage (amostra)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/contentpage/)
