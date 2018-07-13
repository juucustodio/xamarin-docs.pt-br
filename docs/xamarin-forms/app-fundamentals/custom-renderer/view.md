---
title: Implementando um modo de exibição
description: Este artigo explica como criar um renderizador personalizado para um controle personalizado do xamarin. Forms que é usado para exibir uma transmissão de vídeo de visualização da câmera do dispositivo.
ms.prod: xamarin
ms.assetid: 915E25E7-4A6B-4F34-B7B4-07D5F4B240F2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2018
ms.openlocfilehash: 8ee9926eb3b726673711141e7c75a68b607d02d3
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994696"
---
# <a name="implementing-a-view"></a>Implementando um modo de exibição

_Controles de interface de usuário personalizada do xamarin. Forms devem derivar da classe de exibição, que é usado para colocar os layouts e controles na tela. Este artigo demonstra como criar um renderizador personalizado para um controle personalizado do xamarin. Forms que é usado para exibir uma transmissão de vídeo de visualização da câmera do dispositivo._

Cada modo de exibição do xamarin. Forms tem um renderizador que acompanha este artigo para cada plataforma que cria uma instância de um controle nativo. Quando um [ `View` ](xref:Xamarin.Forms.View) é processado por um aplicativo xamarin. Forms no iOS, o `ViewRenderer` classe é instanciada, que por sua vez cria uma instância de um nativo `UIView` controle. Na plataforma Android, o `ViewRenderer` classe instancia um nativo `View` controle. Na Universal Windows Platform (UWP), o `ViewRenderer` classe instancia um nativo `FrameworkElement` controle. Para obter mais informações sobre as classes de controle nativo que mapeiam controles xamarin. Forms e o renderizador, consulte [Classes de Base do renderizador e controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

O diagrama a seguir ilustra o relacionamento entre o [ `View` ](xref:Xamarin.Forms.View) e controles nativos correspondentes que implementação-lo:

![](view-images/view-classes.png "Relação entre a classe de exibição e suas implementação Classes nativas")

O processo de renderização pode ser usado para implementar personalizações específicas de plataforma, criando um renderizador personalizado para um [ `View` ](xref:Xamarin.Forms.View) em cada plataforma. O processo para fazer isso é o seguinte:

1. [Criar](#Creating_the_Custom_Control) um controle personalizado do xamarin. Forms.
1. [Consumir](#Consuming_the_Custom_Control) o controle personalizado do xamarin. Forms.
1. [Criar](#Creating_the_Custom_Renderer_on_each_Platform) o renderizador personalizado para o controle em cada plataforma.

Cada item agora será discutida por sua vez, para implementar um `CameraPreview` renderizador que exibe uma transmissão de vídeo de visualização da câmera do dispositivo. Tocar no fluxo de vídeo interromperá e iniciá-lo.

<a name="Creating_the_Custom_Control" />

## <a name="creating-the-custom-control"></a>Criando o controle personalizado

Um controle personalizado pode ser criado pela criação de subclasses a [ `View` ](xref:Xamarin.Forms.View) de classe, conforme mostrado no exemplo de código a seguir:

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

O `CameraPreview` controle personalizado é criado no projeto PCL (biblioteca) de classes portátil e define a API para o controle. O controle personalizado expõe um `Camera` propriedade que é usada para controlar se o fluxo de vídeo deve ser exibido na parte frontal ou traseira câmera do dispositivo. Se um valor não for especificado para o `Camera` propriedade quando o controle é criado, ele assume como padrão especificando a câmera traseira.

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>Consumindo o controle personalizado

O `CameraPreview` controle personalizado pode ser referenciado em XAML no projeto da PCL, declarando um namespace para seu local e usando o prefixo de namespace no elemento de controle personalizado. O seguinte exemplo de código mostra como o `CameraPreview` controle personalizado pode ser consumido por uma página XAML:

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

O `local` prefixo de namespace pode ser qualquer nome. No entanto, o `clr-namespace` e `assembly` valores devem corresponder aos detalhes de controle personalizado. Depois que o namespace é declarado, o prefixo é usado para referenciar o controle personalizado.

O seguinte exemplo de código mostra como o `CameraPreview` controle personalizado pode ser consumido por uma página do c#:

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

Uma instância da `CameraPreview` controle personalizado será usado para exibir a transmissão de vídeo de visualização da câmera do dispositivo. Além de, opcionalmente, especificando um valor para o `Camera` propriedade, a personalização do controle será realizada no renderizador personalizado.

Agora pode ser adicionado a um renderizador personalizado para cada projeto de aplicativo para criar controles de visualização da câmera específica da plataforma.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Criando o renderizador personalizado em cada plataforma

O processo para criar a classe de renderizador personalizado é da seguinte maneira:

1. Criar uma subclasse do `ViewRenderer<T1,T2>` classe que renderiza o controle personalizado. O primeiro argumento de tipo deve ser o controle personalizado que é o renderizador, neste caso `CameraPreview`. O segundo argumento de tipo deve ser o controle nativo que implementará o controle personalizado.
1. Substituir o `OnElementChanged` método que processa a lógica personalizada de controle e gravar para personalizá-lo. Esse método é chamado quando o controle correspondente do xamarin. Forms é criado.
1. Adicionar um `ExportRenderer` atributo à classe de renderizador personalizado para especificar que será usada para renderizar o controle personalizado do xamarin. Forms. Este atributo é usado para registrar o renderizador personalizado com o xamarin. Forms.

> [!NOTE]
> Para a maioria dos elementos de xamarin. Forms, é opcional fornecer um renderizador personalizado em cada projeto de plataforma. Se um renderizador personalizado não estiver registrado, o renderizador padrão para a classe base do controle será usado. No entanto, os renderizadores personalizados são necessárias em cada projeto da plataforma durante a renderização de um [exibição](xref:Xamarin.Forms.View) elemento.

O diagrama a seguir ilustra as responsabilidades de cada projeto no aplicativo de exemplo, juntamente com as relações entre eles:

![](view-images/solution-structure.png "Responsabilidades do projeto de renderizador personalizado CameraPreview")

O `CameraPreview` controle personalizado é renderizado por classes de renderizador específica da plataforma, que derivam de `ViewRenderer` classe para cada plataforma. Isso resulta em cada `CameraPreview` controle personalizado que está sendo renderizado com controles específicos da plataforma, conforme mostrado nas capturas de tela seguir:

![](view-images/screenshots.png "CameraPreview em cada plataforma")

O `ViewRenderer` classe expõe o `OnElementChanged` método, que é chamado quando o controle personalizado do xamarin. Forms é criado para renderizar o controle nativo correspondente. Esse método usa um `ElementChangedEventArgs` parâmetro que contém `OldElement` e `NewElement` propriedades. Essas propriedades representam o elemento do xamarin. Forms que o renderizador *foi* associada e o elemento do xamarin. Forms que o renderizador *é* anexado, respectivamente. No aplicativo de exemplo, o `OldElement` propriedade será `null` e o `NewElement` propriedade conterá uma referência para o `CameraPreview` instância.

Uma versão de substituição a `OnElementChanged` método em cada classe de renderizador específica da plataforma é o lugar para executar a instanciação de controle nativo e a personalização. O `SetNativeControl` método deve ser usado para instanciar o controle nativo e esse método também atribuirá a referência de controle para o `Control` propriedade. Além disso, uma referência para o controle de xamarin. Forms que está sendo renderizado pode ser obtida por meio de `Element` propriedade.

Em algumas circunstâncias, o `OnElementChanged` método pode ser chamado várias vezes. Portanto, para evitar vazamentos de memória, é necessário ter cuidado ao instanciar um novo controle nativo. A abordagem a ser usada ao instanciar um novo controle nativo em um renderizador personalizado é mostrada no exemplo de código a seguir:

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

Um novo controle nativo deve ser instanciado apenas uma vez, quando a propriedade `Control` é `null`. O controle deve ser configurado e os manipuladores de eventos devem ser inscritos apenas quando o renderizador personalizado for anexado a um novo elemento Xamarin.Forms. Da mesma forma, quaisquer manipuladores de evento inscritos só devem ser cancelados quando muda o elemento que o renderizador está anexado. Adotar essa abordagem ajudará a criar um renderizador personalizado de alto desempenho que não sofra perdas de memória.

Cada classe de renderizador personalizado é decorado com um `ExportRenderer` atributo que registra o renderizador com xamarin. Forms. O atributo utiliza dois parâmetros – o nome do tipo do controle personalizado xamarin. Forms que está sendo renderizado e o nome do tipo de renderizador personalizado. O `assembly` prefixo para o atributo especifica que o atributo se aplica a todo o assembly.

As seções a seguir discutem a implementação de cada classe de renderizador personalizado específica da plataforma.

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

Desde que o `Control` é de propriedade `null`, o `SetNativeControl` método é chamado para instanciar um novo `UICameraPreview` controle e atribuir uma referência a ele para o `Control` propriedade. O `UICameraPreview` controle for um controle personalizado específico da plataforma que usa o `AVCapture` APIs para fornecer o fluxo de visualização da câmera. Ela expõe um `Tapped` evento é manipulado pelo `OnCameraPreviewTapped` método parar e iniciar a visualização de vídeo quando ele é tocado. O `Tapped` evento é assinado quando o renderizador personalizado é anexado a um novo elemento xamarin. Forms e cancelado a assinatura de apenas quando o elemento o renderizador está anexado for alterado.

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

Desde que o `Control` é de propriedade `null`, o `SetNativeControl` método é chamado para instanciar um novo `CameraPreview` controlar e atribua uma referência a ele para o `Control` propriedade. O `CameraPreview` controle for um controle personalizado específico da plataforma que usa o `Camera` API para fornecer o fluxo de visualização da câmera. O `CameraPreview` controle, em seguida, é configurado, desde que o renderizador personalizado está anexado a um novo elemento xamarin. Forms. Essa configuração envolve a criação de um novo nativo `Camera` do objeto para acessar uma câmera de hardware específico e registrar um manipulador de eventos para processar o `Click` eventos. Por sua vez esse manipulador parará e iniciará a visualização de vídeo quando ele é tocado. O `Click` evento é cancelado a assinatura de se o elemento de xamarin. Forms o renderizador está anexado for alterado.

### <a name="creating-the-custom-renderer-on-uwp"></a>Criando o renderizador personalizado na UWP

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

Desde que o `Control` é de propriedade `null`, uma nova `CaptureElement` é instanciada e a `SetupCamera` método é chamado, que usa o `MediaCapture` API para fornecer o fluxo de visualização da câmera. O `SetNativeControl` método é chamado para atribuir uma referência para o `CaptureElement` da instância para o `Control` propriedade. O `CaptureElement` controle expõe um `Tapped` evento é manipulado pelo `OnCameraPreviewTapped` método parar e iniciar a visualização de vídeo quando ele é tocado. O `Tapped` evento é assinado quando o renderizador personalizado é anexado a um novo elemento xamarin. Forms e cancelado a assinatura de apenas quando o elemento o renderizador está anexado for alterado.

> [!NOTE]
> É importante parar e descartar os objetos que fornecem acesso à câmera em um aplicativo UWP. Falha ao fazer isso pode interferir em outros aplicativos que tentam acessar a câmera do dispositivo. Para obter mais informações, consulte [exibir a visualização da câmera](/windows/uwp/audio-video-camera/simple-camera-preview-access/).

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar um renderizador personalizado para um controle personalizado do xamarin. Forms que é usado para exibir uma transmissão de vídeo de visualização da câmera do dispositivo. Controles de interface de usuário personalizada do xamarin. Forms devem derivar de [ `View` ](xref:Xamarin.Forms.View) classe, que é usado para colocar os layouts e controles na tela.


## <a name="related-links"></a>Links relacionados

- [CustomRendererView (amostra)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/view/)
