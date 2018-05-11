---
title: Implementando um modo de exibição
description: Controles de interface de usuário personalizada do xamarin. Forms devem derivar da classe de exibição, que é usada para colocar controles na tela e layouts. Este artigo demonstra como criar um renderizador personalizado para um controle personalizado do xamarin. Forms que é usado para exibir um fluxo de vídeo de visualização da câmera do dispositivo.
ms.prod: xamarin
ms.assetid: 915E25E7-4A6B-4F34-B7B4-07D5F4B240F2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2018
ms.openlocfilehash: 757cd9c0b3b8414b5a8c01af0cf4ffc9b9b8afc4
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="implementing-a-view"></a>Implementando um modo de exibição

_Controles de interface de usuário personalizada do xamarin. Forms devem derivar da classe de exibição, que é usada para colocar controles na tela e layouts. Este artigo demonstra como criar um renderizador personalizado para um controle personalizado do xamarin. Forms que é usado para exibir um fluxo de vídeo de visualização da câmera do dispositivo._

Cada exibição xamarin. Forms tem um processador que o acompanha para cada plataforma que cria uma instância de um controle nativo. Quando um [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) é processado por um aplicativo xamarin. Forms no iOS, o `ViewRenderer` classe é instanciada, que por sua vez instancia um nativo `UIView` controle. Na plataforma Android, o `ViewRenderer` classe instancia um nativo `View` controle. Sobre o Windows UWP (plataforma Universal), o `ViewRenderer` classe instancia um nativo `FrameworkElement` controle. Para obter mais informações sobre o processador e classes de controle nativo que mapeiam xamarin. Forms controles, consulte [Classes de Base de processador e controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

O diagrama a seguir ilustra o relacionamento entre o [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) e os controles nativo correspondentes que implementação-la:

![](view-images/view-classes.png "Relação entre a classe de exibição e suas Classes nativas de implementação")

O processo de renderização pode ser usado para implementar as personalizações específicas da plataforma, criando um renderizador personalizado para um [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) em cada plataforma. O processo para fazer isso é como segue:

1. [Criar](#Creating_the_Custom_Control) um controle personalizado do xamarin. Forms.
1. [Consumir](#Consuming_the_Custom_Control) o controle personalizado do xamarin. Forms.
1. [Criar](#Creating_the_Custom_Renderer_on_each_Platform) renderizador personalizado para o controle em cada plataforma.

Cada item agora será discutida por sua vez, para implementar um `CameraPreview` processador que exibe um fluxo de vídeo de visualização da câmera do dispositivo. Tocar no fluxo de vídeo irá interromper e iniciar a ele.

<a name="Creating_the_Custom_Control" />

## <a name="creating-the-custom-control"></a>Criando o controle personalizado

Um controle personalizado pode ser criado por subclassificação o [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) classe, conforme mostrado no exemplo de código a seguir:

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

O `CameraPreview` controle personalizado é criado no projeto de biblioteca (PCL) de classes portátil e define a API para o controle. O controle personalizado expõe um `Camera` propriedade que é usada para controlar se o fluxo de vídeo deve ser exibido da parte frontal ou câmera traseira do dispositivo. Se não for especificado um valor para o `Camera` propriedade quando o controle é criado, o padrão é especificar a câmera traseira.

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>Consumindo o controle personalizado

O `CameraPreview` controle personalizado pode ser referenciado em XAML no projeto PCL declarando um namespace para seu local e usando o prefixo de namespace no elemento de controle personalizado. O seguinte exemplo de código mostra como o `CameraPreview` controle personalizado pode ser consumido por uma página XAML:

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

O `local` o prefixo do namespace pode ser qualquer nome. No entanto, o `clr-namespace` e `assembly` valores devem coincidir com os detalhes do controle personalizado. Depois que o namespace for declarado, o prefixo é usado para referenciar o controle personalizado.

O seguinte exemplo de código mostra como o `CameraPreview` controle personalizado pode ser consumido por uma página c#:

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

Uma instância do `CameraPreview` controle personalizado será usado para exibir o fluxo de vídeo de visualização da câmera do dispositivo. Além de, opcionalmente, especificando um valor para o `Camera` propriedade, a personalização do controle será realizada no renderizador personalizado.

Agora é possível adicionar um renderizador personalizado para cada projeto de aplicativo para criar controles de visualização de câmera específico da plataforma.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Criando o renderizador personalizado em cada plataforma

O processo para criar a classe de renderizador personalizado é o seguinte:

1. Criar uma subclasse do `ViewRenderer<T1,T2>` classe que renderiza o controle personalizado. O primeiro argumento de tipo deve ser um controle personalizado que o processador é nesse caso, `CameraPreview`. O segundo argumento de tipo deve ser o controle nativo que implementa o controle personalizado.
1. Substituir o `OnElementChanged` método que processa a lógica personalizada de controle e de gravação para personalizá-lo. Esse método é chamado quando o controle xamarin. Forms correspondente é criado.
1. Adicionar um `ExportRenderer` de atributo para a classe de renderizador personalizado para especificar que será usada para renderizar o controle personalizado do xamarin. Forms. Este atributo é usado para registrar o renderizador personalizado com xamarin. Forms.

> [!NOTE]
> Para a maioria dos elementos de xamarin. Forms, é opcional fornecer um renderizador personalizado em cada projeto da plataforma. Se um renderizador personalizado não estiver registrado, será usado o renderizador padrão para a classe base do controle. No entanto, renderizadores personalizados são necessários em cada projeto de plataforma ao renderizar um [exibição](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) elemento.

O diagrama a seguir ilustra as responsabilidades de cada projeto de aplicativo de exemplo, juntamente com as relações entre eles:

![](view-images/solution-structure.png "Responsabilidades de projeto do renderizador CameraPreview personalizado")

O `CameraPreview` controle personalizado é processado por classes de processador específico da plataforma, que derivam de `ViewRenderer` classe para cada plataforma. Isso resulta em cada `CameraPreview` controle personalizado está sendo processado com controles de plataforma específica, conforme mostrado nas capturas de tela seguir:

![](view-images/screenshots.png "CameraPreview em cada plataforma")

O `ViewRenderer` classe expõe o `OnElementChanged` método, que é chamado quando o controle personalizado do xamarin. Forms é criado para renderizar o controle nativo correspondente. Esse método usa um `ElementChangedEventArgs` parâmetro que contém `OldElement` e `NewElement` propriedades. Essas propriedades representam o elemento xamarin. Forms que o renderizador *foi* associada e o elemento xamarin. Forms que o renderizador *é* anexado, respectivamente. No aplicativo de exemplo, o `OldElement` propriedade será `null` e `NewElement` propriedade conterá uma referência para a `CameraPreview` instância.

Uma versão de substituição de `OnElementChanged` método na classe cada renderizador específico da plataforma, é o local para executar a instanciação do controle nativo e personalização. O `SetNativeControl` método deve ser usado para criar uma instância do controle nativo e esse método também atribuirá a referência de controle para o `Control` propriedade. Além disso, uma referência para o controle de xamarin. Forms que está sendo renderizado pode ser obtida por meio de `Element` propriedade.

Em algumas circunstâncias, o `OnElementChanged` método pode ser chamado várias vezes. Portanto, para evitar perdas de memória, deve ter cuidado ao instanciar um novo controle nativo. A abordagem a ser usada ao instanciar um novo controle nativo em um renderizador personalizado é mostrada no exemplo de código a seguir:

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<NativeListView> e)
{
  base.OnElementChanged (e);

  if (Control == null) {
    // Instantiate the native control and assign it to the Control property with
    // the SetNativeControl method
  }

  if (e.OldElement != null) {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null) {
    // Configure the control and subscribe to event handlers
  }
}
```

Um novo controle nativo deve ser instanciado apenas uma vez, quando a propriedade `Control` é `null`. O controle deve ser configurado e os manipuladores de eventos devem ser inscritos apenas quando o renderizador personalizado for anexado a um novo elemento Xamarin.Forms. Da mesma forma, os manipuladores de evento que assinou só devem ser cancelados quando muda o elemento que o processador está associado. Adotar essa abordagem ajudará a criar um renderizador personalizado de alto desempenho que não apresenta vazamentos de memória.

Cada classe de renderizador personalizado é decorado com um `ExportRenderer` atributo que registra o renderizador com xamarin. Forms. O atributo utiliza dois parâmetros – o nome do tipo do controle personalizado xamarin. Forms está sendo processado e o nome do tipo de renderizador personalizado. O `assembly` prefixo para o atributo especifica que o atributo se aplica ao assembly inteiro.

As seções a seguir discutem a implementação de cada classe de renderizador personalizado específico da plataforma.

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

            if (Control == null) {
                uiCameraPreview = new UICameraPreview (e.NewElement.Camera);
                SetNativeControl (uiCameraPreview);
            }
            if (e.OldElement != null) {
                // Unsubscribe
                uiCameraPreview.Tapped -= OnCameraPreviewTapped;
            }
            if (e.NewElement != null) {
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

Desde que o `Control` é de propriedade `null`, o `SetNativeControl` método é chamado para instanciar um novo `UICameraPreview` controle e atribuir uma referência a ele para o `Control` propriedade. O `UICameraPreview` o controle é específico da plataforma personalizado que usa o `AVCapture` APIs para fornecer o fluxo de visualização da câmera. Expõe um `Tapped` evento é manipulado pelo `OnCameraPreviewTapped` método para parar e iniciar a visualização do vídeo quando ele é tocado. O `Tapped` evento está inscrito quando o renderizador personalizado é anexado a um novo elemento xamarin. Forms e não inscrito em apenas quando o elemento que o processador é anexado a alterações.

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

            if (Control == null)
            {
                cameraPreview = new CameraPreview(Context);
                SetNativeControl(cameraPreview);
            }

            if (e.OldElement != null)
            {
                // Unsubscribe
                cameraPreview.Click -= OnCameraPreviewClicked;
            }
            if (e.NewElement != null)
            {
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

Desde que o `Control` é de propriedade `null`, o `SetNativeControl` método é chamado para instanciar um novo `CameraPreview` controlar e atribuir uma referência a ele para o `Control` propriedade. O `CameraPreview` o controle é específico da plataforma personalizado que usa o `Camera` API para fornecer o fluxo de visualização da câmera. O `CameraPreview` controle, em seguida, é configurado, desde que o renderizador personalizado está anexado a um novo elemento xamarin. Forms. Essa configuração envolve a criação de um novo nativo `Camera` de objeto para acessar uma câmera de hardware específico e registrar um manipulador de eventos para processar o `Click` evento. Por sua vez este manipulador de mensagens irá interromper e iniciar a visualização do vídeo quando ele é tocado. O `Click` evento é não inscrito em se o elemento xamarin. Forms o processador está anexado a alterações.

### <a name="creating-the-custom-renderer-on-uwp"></a>Criando o renderizador personalizado em UWP

O exemplo de código a seguir mostra o renderizador personalizado para UWP:

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

            if (Control == null)
            {
                ...
                _captureElement = new CaptureElement();
                _captureElement.Stretch = Stretch.UniformToFill;

                SetupCamera();
                SetNativeControl(_captureElement);
            }
            if (e.OldElement != null)
            {
                // Unsubscribe
                Tapped -= OnCameraPreviewTapped;
                ...
            }
            if (e.NewElement != null)
            {
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

Desde que o `Control` é de propriedade `null`, um novo `CaptureElement` é instanciada e `SetupCamera` método é chamado, que usa o `MediaCapture` API para fornecer o fluxo de visualização da câmera. O `SetNativeControl` método é chamado para atribuir uma referência para o `CaptureElement` de instância para o `Control` propriedade. O `CaptureElement` controlar expõe um `Tapped` evento é manipulado pelo `OnCameraPreviewTapped` método para parar e iniciar a visualização do vídeo quando ele é tocado. O `Tapped` evento está inscrito quando o renderizador personalizado é anexado a um novo elemento xamarin. Forms e não inscrito em apenas quando o elemento que o processador é anexado a alterações.

> [!NOTE]
> É importante parar e descartar os objetos que fornecem acesso à câmera em um aplicativo de UWP. Falha ao fazer isso pode interferir em outros aplicativos que tentam acessar a câmera do dispositivo. Para obter mais informações, consulte [exibir a visualização de câmera](/windows/uwp/audio-video-camera/simple-camera-preview-access/).

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar um renderizador personalizado para um controle personalizado do xamarin. Forms que é usado para exibir um fluxo de vídeo de visualização da câmera do dispositivo. Controles de interface de usuário personalizada do xamarin. Forms devem derivar de [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) classe, que é usado para colocar controles na tela e layouts.


## <a name="related-links"></a>Links relacionados

- [CustomRendererView (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/view/)
