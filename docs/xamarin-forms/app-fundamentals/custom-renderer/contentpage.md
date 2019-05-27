---
title: Personalizando uma ContentPage
description: Uma ContentPage é um elemento visual que mostra uma única exibição e ocupa a maior parte da tela. Este artigo mostra como criar um renderizador personalizado para a página ContentPage, permitindo que os desenvolvedores substituam a renderização nativa padrão por sua própria personalização específica a uma plataforma.
ms.prod: xamarin
ms.assetid: A4E61D93-73D9-4668-8D1C-DB6FC2491822
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 290cd36f36d86d161b7fc124f8961888ade41955
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65925945"
---
# <a name="customizing-a-contentpage"></a>Personalizando uma ContentPage

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/CustomRenderers/ContentPage/)

_Uma ContentPage é um elemento visual que mostra uma única exibição e ocupa a maior parte da tela. Este artigo mostra como criar um renderizador personalizado para a página ContentPage, permitindo que os desenvolvedores substituam a renderização nativa padrão por sua própria personalização específica a uma plataforma._

Cada um dos controles do Xamarin.Forms tem um renderizador que o acompanha para cada plataforma que cria uma instância de um controle nativo. Quando um [`ContentPage`](xref:Xamarin.Forms.ContentPage) é renderizado por um aplicativo Xamarin.Forms, no iOS é criada uma instância da classe `PageRenderer`, o que por sua vez cria uma instância de um controle `UIViewController` nativo. Na plataforma Android, a classe `PageRenderer` cria uma instância de um controle `ViewGroup`. Na UWP (Plataforma Universal do Windows), a classe `PageRenderer` cria uma instância de um controle `FrameworkElement`. Para obter mais informações sobre as classes de renderizador e de controle nativo para as quais os controles do Xamarin.Forms são mapeadas, confira [Classes base do renderizador e controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

O diagrama a seguir ilustra a relação entre o [`ContentPage`](xref:Xamarin.Forms.ContentPage) e os controles nativos correspondentes que o implementam:

![](contentpage-images/contentpage-classes.png "Relação entre a classe ContentPage e seus controles de implementação nativos")

E possível aproveitar o processo de renderização para implementar personalizações específicas da plataforma criando um renderizador personalizado para um [`ContentPage`](xref:Xamarin.Forms.ContentPage) em cada plataforma. O processo para fazer isso é o seguinte:

1. [Criar](#Creating_the_Xamarin.Forms_Page) uma página no Xamarin.Forms.
1. [Consumir](#Consuming_the_Xamarin.Forms_Page) a página do Xamarin.Forms.
1. [Criar](#Creating_the_Page_Renderer_on_each_Platform) o renderizador personalizado para a página em cada plataforma.

Agora, cada item será abordado separadamente, a fim de implementar um `CameraPage` que fornece um feed de câmera em tempo real e a capacidade de tirar uma foto.

<a name="Creating_the_Xamarin.Forms_Page" />

## <a name="creating-the-xamarinforms-page"></a>Criando a página do Xamarin.Forms

Um [`ContentPage`](xref:Xamarin.Forms.ContentPage) inalterado pode ser adicionado ao projeto do Xamarin.Forms compartilhado, conforme mostrado no exemplo de código XAML a seguir:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CustomRenderer.CameraPage">
    <ContentPage.Content>
    </ContentPage.Content>
</ContentPage>
```

De forma semelhante, o arquivo code-behind do [`ContentPage`](xref:Xamarin.Forms.ContentPage) também deve permanecer inalterado, conforme mostrado no exemplo de código a seguir:

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

O exemplo de código a seguir mostra como a página pode ser criada em C#:

```csharp
public class CameraPageCS : ContentPage
{
    public CameraPageCS ()
    {
    }
}
```

Uma instância do `CameraPage` será usada para exibir o feed de câmera em tempo real em cada plataforma. A personalização do controle será realizada no renderizador personalizado, portanto, nenhuma implementação adicional é necessária na classe `CameraPage`.

<a name="Consuming_the_Xamarin.Forms_Page" />

## <a name="consuming-the-xamarinforms-page"></a>Consumindo a página do Xamarin.Forms

O `CameraPage` vazio deve ser exibido pelo aplicativo do Xamarin.Forms. Isso ocorre quando um botão na instância de `MainPage` é tocado, o que por sua vez executa o método `OnTakePhotoButtonClicked`, conforme mostrado no exemplo de código a seguir:

```csharp
async void OnTakePhotoButtonClicked (object sender, EventArgs e)
{
    await Navigation.PushAsync (new CameraPage ());
}
```

Esse código simplesmente navega para o `CameraPage`, em que os renderizadores personalizados personalizarão a aparência da página em cada plataforma.

<a name="Creating_the_Page_Renderer_on_each_Platform" />

## <a name="creating-the-page-renderer-on-each-platform"></a>Criando o renderizador da página em cada plataforma

O processo para criar a classe do renderizador personalizado é a seguinte:

1. Crie uma subclasse da classe `PageRenderer`.
1. Substitua o método `OnElementChanged` que renderiza a página nativa e escreva a lógica para personalizá-la. O método `OnElementChanged` é chamado quando o controle do Xamarin.Forms correspondente é criado.
1. Adicione um atributo `ExportRenderer` à classe do renderizador da página para especificar que ele será usado para renderizar a página do Xamarin.Forms. Este atributo é usado para registrar o renderizador personalizado no Xamarin.Forms.

> [!NOTE]
> O fornecimento de um renderizador de página em cada projeto de plataforma é opcional. Se um renderizador de página não estiver registrado, será usado o renderizador de página padrão.

O diagrama a seguir ilustra as responsabilidades de cada projeto no aplicativo de exemplo, bem como a relação entre elas:

![](contentpage-images/solution-structure.png "Responsabilidades do projeto de renderizador personalizado de CameraPage")

A instância de `CameraPage` é renderizada por classes de `CameraPageRenderer` específicas da plataforma, que derivam da classe `PageRenderer` para cada plataforma. Isso faz com que cada instância de `CameraPage` seja renderizada com um feed de câmera em tempo real, conforme mostrado nas capturas de tela seguir:

![](contentpage-images/screenshots.png "CameraPage em cada plataforma")

A classe `PageRenderer` expõe o método `OnElementChanged`, que é chamado quando a página do Xamarin.Forms é criada para renderizar o controle nativo correspondente. Esse método usa um parâmetro `ElementChangedEventArgs`, que contém as propriedades `OldElement` e `NewElement`. Essas propriedades representam o elemento do Xamarin.Forms ao qual o renderizador *estava* anexado e o elemento do Xamarin.Forms ao qual o renderizador *está* anexado, respectivamente. No aplicativo de exemplo, a propriedade `OldElement` será `null` e a propriedade `NewElement` conterá uma referência à instância de `CameraPage`.

Uma versão de substituição do método `OnElementChanged` na classe `CameraPageRenderer` é o lugar para realização da personalização da página nativa. É possível obter uma referência para a instância da página do Xamarin.Forms que está sendo renderizada por meio da propriedade `Element`.

Cada classe de renderizador personalizado é decorada com um atributo `ExportRenderer` que registra o renderizador no Xamarin.Forms. O atributo aceita dois parâmetros – o nome do tipo de página do Xamarin.Forms que está sendo renderizada e o nome do tipo de renderizador personalizado. O prefixo `assembly` do atributo especifica que o atributo se aplica a todo o assembly.

As seções a seguir abordam a implementação do renderizador personalizado `CameraPageRenderer` para cada plataforma.

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

A chamada para o método `OnElementChanged` da classe base cria uma instância do controle `UIViewController` do iOS. O fluxo da câmera em tempo real será renderizado somente se o renderizador ainda não estiver anexado a um elemento existente do Xamarin.Forms e se houver uma instância da página sendo renderizada pelo renderizador personalizado.

A página, então, é personalizada por uma série de métodos que usam as APIs `AVCapture` para fornecer o -fluxo em tempo real da câmera e a capacidade de tirar uma foto.

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

A chamada para o método `OnElementChanged` da classe base cria uma instância de um controle `ViewGroup` do Android, que é um grupo de exibições. O fluxo da câmera em tempo real será renderizado somente se o renderizador ainda não estiver anexado a um elemento existente do Xamarin.Forms e se houver uma instância da página sendo renderizada pelo renderizador personalizado.

A página, então, é personalizada invocando uma série de métodos que usam a API `Camera` para fornecer o fluxo em tempo real da câmera e a capacidade de tirar uma foto, antes que o método `AddView` seja invocado para adicionar a interface do usuário de fluxo em tempo real da câmera ao `ViewGroup`. Observe que, no Android, também é necessário substituir o método `OnLayout` para executar operações de medida e layout na exibição. Para obter mais informações, confira o [Exemplo de renderizador de ContentPage](https://developer.xamarin.com/samples/xamarin-forms/CustomRenderers/ContentPage/).

### <a name="creating-the-page-renderer-on-uwp"></a>Criando o renderizador de página na UWP

O exemplo de código a seguir mostra o renderizador de página para a UWP:

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

A chamada para o método `OnElementChanged` da classe base instancia um controle `FrameworkElement`, em que a página é renderizada. O fluxo da câmera em tempo real será renderizado somente se o renderizador ainda não estiver anexado a um elemento existente do Xamarin.Forms e se houver uma instância da página sendo renderizada pelo renderizador personalizado. A página, então, é personalizada invocando uma série de métodos que usam a API `MediaCapture` para fornecer o fluxo em tempo real da câmera e a capacidade de tirar uma foto, antes que a página personalizada seja adicionada à coleção `Children` para exibição.

Ao implementar um renderizador personalizado que deriva de `PageRenderer` na UWP, o método `ArrangeOverride` também deve ser implementado para organizar os controles da página, porque o renderizador de base não sabe o que fazer com eles. Caso contrário, será gerada uma página em branco. Portanto, neste exemplo, o método `ArrangeOverride` chamada o método `Arrange` na instância de `Page`.

> [!NOTE]
> É importante parar e descartar os objetos que fornecem acesso à câmera em um aplicativo da UWP. Deixar de fazer isso pode interferir em outros aplicativos que tentam acessar a câmera do dispositivo. Para obter mais informações, confira [Exibir a visualização da câmera](https://msdn.microsoft.com/windows/uwp/audio-video-camera/simple-camera-preview-access).

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar um renderizador personalizado para a página [`ContentPage`](xref:Xamarin.Forms.ContentPage), permitindo que os desenvolvedores substituam a renderização nativa padrão com sua própria personalização específica a uma plataforma. Um `ContentPage` é um elemento visual que mostra uma única exibição e ocupa a maior parte da tela.


## <a name="related-links"></a>Links relacionados

- [CustomRendererContentPage (amostra)](https://developer.xamarin.com/samples/xamarin-forms/CustomRenderers/ContentPage/)
