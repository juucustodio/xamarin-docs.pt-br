---
title: Implementando uma exibição
description: Este artigo explica como criar um renderizador personalizado para um controle personalizado do Xamarin.Forms, que é usado para exibir um fluxo de vídeo de visualização com a câmera do dispositivo.
ms.prod: xamarin
ms.assetid: 915E25E7-4A6B-4F34-B7B4-07D5F4B240F2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2018
ms.openlocfilehash: 8f5c440784205fa0e7e2001c981e37eab8646f24
ms.sourcegitcommit: a153623a69b5cb125f672df8007838afa32e9edf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67268939"
---
# <a name="implementing-a-view"></a>Implementando uma exibição

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/CustomRenderers/View/)

_Controles de interfaces do usuário personalizadas do Xamarin.Forms devem derivar da classe View, que é usada para colocar os layouts e controles na tela. Este artigo demonstra como criar um renderizador personalizado para um controle personalizado do Xamarin.Forms, que é usado para exibir um fluxo de vídeo de visualização com a câmera do dispositivo._

Cada exibição do Xamarin.Forms tem um renderizador que o acompanha para cada plataforma que cria uma instância de um controle nativo. Quando um [`View`](xref:Xamarin.Forms.View) é renderizado por um aplicativo Xamarin.Forms no iOS, é criada uma instância da classe `ViewRenderer`, que, por sua vez, cria uma instância de um controle `UIView` nativo. Na plataforma Android, a classe `ViewRenderer` cria uma instância de um controle `View` nativo. Na UWP (Plataforma Universal do Windows), a classe `ViewRenderer` cria uma instância de um controle `FrameworkElement` nativo. Para obter mais informações sobre as classes de renderizador e de controle nativo para as quais os controles do Xamarin.Forms são mapeadas, confira [Classes base do renderizador e controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

O seguinte diagrama ilustra a relação entre o [`View`](xref:Xamarin.Forms.View) e os controles nativos correspondentes que o implementam:

![](view-images/view-classes.png "Relação entre a classe View e suas classes de implementação nativas")

E possível usar o processo de renderização para implementar personalizações específicas da plataforma criando um renderizador personalizado para um [`View`](xref:Xamarin.Forms.View) em cada plataforma. O processo para fazer isso é o seguinte:

1. [Criar](#Creating_the_Custom_Control) um controle personalizado do Xamarin.Forms.
1. [Consumir](#Consuming_the_Custom_Control) o controle personalizado do Xamarin.Forms.
1. [Criar](#Creating_the_Custom_Renderer_on_each_Platform) o renderizador personalizado para o controle em cada plataforma.

Agora, cada item será discutido separadamente, a fim de implementar um renderizador `CameraPreview` que exibe um fluxo de vídeo de visualização usando a câmera do dispositivo. Tocar no fluxo de vídeo o interromperá e iniciará.

<a name="Creating_the_Custom_Control" />

## <a name="creating-the-custom-control"></a>Criando o controle personalizado

É possível criar um controle personalizado criando subclasses da classe [`View`](xref:Xamarin.Forms.View), conforme mostrado no exemplo de código a seguir:

```csharp
public class CameraPreview : View
{
  public static readonly BindableProperty CameraProperty = BindableProperty.Create (
    propertyName: "Camera",
    returnType: typeof(CameraOptions),
    declaringType: typeof(CameraPreview),
    defaultValue: CameraOptions.Rear);

  public CameraOptions Camera {
    get { return (CameraOptions)GetValue (CameraProperty); }
    set { SetValue (CameraProperty, value); }
  }
}
```

O controle personalizado `CameraPreview` é criado no projeto da biblioteca do .NET Standard e define a API para o controle. O controle personalizado expõe uma propriedade `Camera` que é usada para controlar se o fluxo de vídeo deve ser exibido na parte frontal ou traseira da câmera do dispositivo. Se não for especificado um valor para a propriedade `Camera` quando o controle for criado, o comportamento padrão será especificar a câmera traseira.

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>Consumindo o controle personalizado

O controle personalizado `CameraPreview` pode ser referenciado em XAML no projeto da biblioteca .NET Standard declarando um namespace para sua localização e usando o prefixo do namespace no elemento de controle personalizado. O exemplo de código a seguir mostra como o controle personalizado `CameraPreview` pode ser consumido por uma página XAML:

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
             ...>
    <ContentPage.Content>
        <StackLayout>
            <Label Text="Camera Preview:" />
            <local:CameraPreview Camera="Rear"
              HorizontalOptions="FillAndExpand" VerticalOptions="FillAndExpand" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

O prefixo do namespace `local` pode ser qualquer nome. No entanto, os valores de `clr-namespace` e `assembly` devem corresponder aos detalhes do controle personalizado. Quando o namespace é declarado, o prefixo é usado para referenciar o controle personalizado.

O seguinte exemplo de código mostra como o controle personalizado `CameraPreview` pode ser consumido por um página em C#:

```csharp
public class MainPageCS : ContentPage
{
  public MainPageCS ()
  {
    ...
    Content = new StackLayout {
      Children = {
        new Label { Text = "Camera Preview:" },
        new CameraPreview {
          Camera = CameraOptions.Rear,
          HorizontalOptions = LayoutOptions.FillAndExpand,
          VerticalOptions = LayoutOptions.FillAndExpand
        }
      }
    };
  }
}
```

Uma instância do controle personalizado `CameraPreview` será usada para exibir o fluxo de vídeo de visualização da câmera do dispositivo. Além de, opcionalmente, especificar um valor para a propriedade `Camera`, a personalização do controle será realizada no renderizador personalizado.

Agora, é possível adicionar um renderizador personalizado a cada projeto de aplicativo para criar controles de visualização de câmera específicos da plataforma.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Criando o renderizador personalizado em cada plataforma

O processo para criar a classe do renderizador personalizado é o seguinte:

1. Crie uma subclasse da classe `ViewRenderer<T1,T2>` que renderiza o controle personalizado. O primeiro argumento de tipo deve ser o controle personalizado a que o renderizador se aplica, neste caso, `CameraPreview`. O segundo argumento de tipo deve ser o controle nativo que implementará o controle personalizado.
1. Substitua o método `OnElementChanged` que renderiza o controle personalizado e escreva a lógica para personalizá-lo. Esse método é chamado quando o controle do Xamarin.Forms correspondente é criado.
1. Adicione um atributo `ExportRenderer` à classe do renderizador personalizado para especificar que ele será usado para renderizar o controle personalizado do Xamarin.Forms. Esse atributo é usado para registrar o renderizador personalizado no Xamarin.Forms.

> [!NOTE]
> Para a maioria dos elementos do Xamarin.Forms, o fornecimento de um renderizador personalizado em cada projeto de plataforma é opcional. Se um renderizador personalizado não estiver registrado, será usado o renderizador padrão da classe base do controle. No entanto, são necessários renderizadores personalizados em cada projeto da plataforma durante a renderização de um elemento [View](xref:Xamarin.Forms.View).

O diagrama a seguir ilustra as responsabilidades de cada projeto no aplicativo de exemplo, bem como as relações entre elas:

![](view-images/solution-structure.png "Responsabilidades do projeto de renderizador personalizado de CameraPreview")

O controle personalizado `CameraPreview` é renderizado por classes de renderizador específicas da plataforma, que derivam da classe `ViewRenderer` para cada plataforma. Isso faz com que cada controle personalizado `CameraPreview` seja renderizado com controles específicos da plataforma, conforme mostrado nas capturas de tela seguir:

![](view-images/screenshots.png "CameraPreview em cada plataforma")

A classe `ViewRenderer` expõe o método `OnElementChanged`, que é chamado quando um controle personalizado do Xamarin.Forms é criado para renderizar o controle nativo correspondente. Esse método usa um parâmetro `ElementChangedEventArgs`, que contém as propriedades `OldElement` e `NewElement`. Essas propriedades representam o elemento do Xamarin.Forms a que o renderizador *estava* anexado e o elemento a que o renderizador *está* anexado, respectivamente. No aplicativo de exemplo, a propriedade `OldElement` será `null` e a propriedade `NewElement` conterá uma referência à instância de `CameraPreview`.

Uma versão de substituição do método `OnElementChanged`, em cada classe de renderizador específica da plataforma, é o lugar para realização da instanciação e da personalização do controle nativo. O método `SetNativeControl` deve ser usado para instanciar o controle nativo e esse método também atribuirá a referência de controle à propriedade `Control`. Além disso, é possível obter uma referência ao controle do Xamarin.Forms que está sendo renderizado por meio da propriedade `Element`.

Em algumas circunstâncias, o método `OnElementChanged` pode ser chamado várias vezes. Portanto, para evitar perdas de memória, é necessário ter cuidado ao instanciar um novo controle nativo. A abordagem a ser usada ao instanciar um novo controle nativo em um renderizador personalizado é mostrada no exemplo de código a seguir:

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<NativeListView> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null) {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null) {
    if (Control == null) {
      // Instantiate the native control and assign it to the Control property with
      // the SetNativeControl method
    }
    // Configure the control and subscribe to event handlers
  }
}
```

Um novo controle nativo deve ser instanciado apenas uma vez, quando a propriedade `Control` é `null`. Além disso, o controle só deve ser criado e configurado e os manipuladores de eventos devem ser inscritos apenas quando o renderizador personalizado for anexado a um novo elemento Xamarin.Forms. Da mesma forma, a inscrição de quaisquer manipuladores de evento inscritos só deve ser cancelada quando o elemento ao qual o renderizador está anexado for alterado. Adotar essa abordagem ajudará a criar um renderizador personalizado com bom desempenho que não sofre perdas de memória.

> [!IMPORTANT]
> O método `SetNativeControl` só deve ser chamado se `e.NewElement` não for `null`.

Cada classe de renderizador personalizado é decorada com um atributo `ExportRenderer` que registra o renderizador no Xamarin.Forms. O atributo aceita dois parâmetros – o nome do tipo de controle personalizado do Xamarin.Forms que está sendo renderizado e o nome do tipo de renderizador personalizado. O prefixo `assembly` do atributo especifica que o atributo se aplica a todo o assembly.

As seções a seguir abordam a implementação de cada classe de renderizador personalizado específica da plataforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Criando o renderizador personalizado no iOS

O exemplo de código a seguir mostra o renderizador personalizado para a plataforma iOS:

```csharp
[assembly: ExportRenderer (typeof(CameraPreview), typeof(CameraPreviewRenderer))]
namespace CustomRenderer.iOS
{
    public class CameraPreviewRenderer : ViewRenderer<CameraPreview, UICameraPreview>
    {
        UICameraPreview uiCameraPreview;

        protected override void OnElementChanged (ElementChangedEventArgs<CameraPreview> e)
        {
            base.OnElementChanged (e);

            if (e.OldElement != null) {
                // Unsubscribe
                uiCameraPreview.Tapped -= OnCameraPreviewTapped;
            }
            if (e.NewElement != null) {
                if (Control == null) {
                  uiCameraPreview = new UICameraPreview (e.NewElement.Camera);
                  SetNativeControl (uiCameraPreview);
                }
                // Subscribe
                uiCameraPreview.Tapped += OnCameraPreviewTapped;
            }
        }

        void OnCameraPreviewTapped (object sender, EventArgs e)
        {
            if (uiCameraPreview.IsPreviewing) {
                uiCameraPreview.CaptureSession.StopRunning ();
                uiCameraPreview.IsPreviewing = false;
            } else {
                uiCameraPreview.CaptureSession.StartRunning ();
                uiCameraPreview.IsPreviewing = true;
            }
        }
        ...
    }
}
```

Desde que a propriedade `Control` seja `null`, o método `SetNativeControl` será chamado para instanciar um novo controle `UICameraPreview` e para atribuir uma referência a ele para a propriedade `Control`. O controle `UICameraPreview` é um controle personalizado específico da plataforma que usa as APIs `AVCapture` para fornecer o fluxo de visualização da câmera. Ele expõe um evento `Tapped` que é manipulado pelo método `OnCameraPreviewTapped` parar e iniciar a visualização do vídeo quando é tocado. O evento `Tapped` é assinado quando o renderizador personalizado é anexado a um novo elemento do Xamarin.Forms e a assinatura é cancelada apenas quando o elemento a que o renderizador está anexado é alterado.

### <a name="creating-the-custom-renderer-on-android"></a>Criando o renderizador personalizado no Android

O exemplo de código a seguir mostra o renderizador personalizado para a plataforma Android:

```csharp
[assembly: ExportRenderer(typeof(CustomRenderer.CameraPreview), typeof(CameraPreviewRenderer))]
namespace CustomRenderer.Droid
{
    public class CameraPreviewRenderer : ViewRenderer<CustomRenderer.CameraPreview, CustomRenderer.Droid.CameraPreview>
    {
        CameraPreview cameraPreview;

        public CameraPreviewRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(ElementChangedEventArgs<CustomRenderer.CameraPreview> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // Unsubscribe
                cameraPreview.Click -= OnCameraPreviewClicked;
            }
            if (e.NewElement != null)
            {
                if (Control == null)
                {
                  cameraPreview = new CameraPreview(Context);
                  SetNativeControl(cameraPreview);
                }
                Control.Preview = Camera.Open((int)e.NewElement.Camera);

                // Subscribe
                cameraPreview.Click += OnCameraPreviewClicked;
            }
        }

        void OnCameraPreviewClicked(object sender, EventArgs e)
        {
            if (cameraPreview.IsPreviewing)
            {
                cameraPreview.Preview.StopPreview();
                cameraPreview.IsPreviewing = false;
            }
            else
            {
                cameraPreview.Preview.StartPreview();
                cameraPreview.IsPreviewing = true;
            }
        }
        ...
    }
}
```

Desde que a propriedade `Control` seja `null`, o método `SetNativeControl` será chamado para instanciar um novo controle `CameraPreview` e para atribuir uma referência a ele para a propriedade `Control`. O controle `CameraPreview` é um controle personalizado específico da plataforma que usa a API `Camera` para fornecer o fluxo de visualização da câmera. O controle `CameraPreview` é configurado, desde que o renderizador personalizado esteja anexado a um novo elemento do Xamarin.Forms. Essa configuração envolve a criação de um novo objeto `Camera` nativo para acessar uma câmera de hardware específica e registrar um manipulador de eventos para processar o evento `Click`. Por sua vez, esse manipulador parará e iniciará a visualização do vídeo quando for tocado. O evento `Click` terá a assinatura cancelada se o elemento Xamarin.Forms a que o renderizador está anexado for alterado.

### <a name="creating-the-custom-renderer-on-uwp"></a>Criando o renderizador personalizado na UWP

O exemplo de código a seguir mostra o renderizador personalizado para a UWP:

```csharp
[assembly: ExportRenderer(typeof(CameraPreview), typeof(CameraPreviewRenderer))]
namespace CustomRenderer.UWP
{
    public class CameraPreviewRenderer : ViewRenderer<CameraPreview, Windows.UI.Xaml.Controls.CaptureElement>
    {
        ...
        CaptureElement _captureElement;
        bool _isPreviewing;

        protected override void OnElementChanged(ElementChangedEventArgs<CameraPreview> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // Unsubscribe
                Tapped -= OnCameraPreviewTapped;
                ...
            }
            if (e.NewElement != null)
            {
                if (Control == null)
                {
                  ...
                  _captureElement = new CaptureElement();
                  _captureElement.Stretch = Stretch.UniformToFill;

                  SetupCamera();
                  SetNativeControl(_captureElement);
                }
                // Subscribe
                Tapped += OnCameraPreviewTapped;
            }
        }

        async void OnCameraPreviewTapped(object sender, TappedRoutedEventArgs e)
        {
            if (_isPreviewing)
            {
                await StopPreviewAsync();
            }
            else
            {
                await StartPreviewAsync();
            }
        }
        ...
    }
}
```

Desde que a propriedade `Control` seja `null`, um novo `CaptureElement` será instanciado e o método `SetupCamera` será chamado, que usa a API `MediaCapture` para fornecer o fluxo de visualização da câmera. Em seguida, o método `SetNativeControl` é chamado para atribuir uma referência para a instância `CaptureElement` para a propriedade `Control`. O controle `CaptureElement` expõe um evento `Tapped` que é manipulado pelo método `OnCameraPreviewTapped` para parar e iniciar a visualização do vídeo quando é tocado. O evento `Tapped` é assinado quando o renderizador personalizado é anexado a um novo elemento do Xamarin.Forms e a assinatura é cancelada apenas quando o elemento a que o renderizador está anexado é alterado.

> [!NOTE]
> É importante parar e descartar os objetos que fornecem acesso à câmera em um aplicativo da UWP. Deixar de fazer isso pode interferir em outros aplicativos que tentam acessar a câmera do dispositivo. Para obter mais informações, confira [Exibir a visualização da câmera](/windows/uwp/audio-video-camera/simple-camera-preview-access/).

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar um renderizador personalizado para um controle personalizado do Xamarin.Forms, que é usado para exibir um fluxo de vídeo de visualização com a câmera do dispositivo. Controles da interface do usuário personalizadas do Xamarin.Forms devem derivar da classe [`View`](xref:Xamarin.Forms.View), que é usada para colocar os layouts e controles na tela.


## <a name="related-links"></a>Links relacionados

- [CustomRendererView (amostra)](https://developer.xamarin.com/samples/xamarin-forms/CustomRenderers/View/)
