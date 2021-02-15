---
Título: implementando uma exibição Descrição: Este artigo explica como criar um renderizador personalizado para um Xamarin.Forms controle personalizado que é usado para exibir um fluxo de vídeo de visualização da câmera do dispositivo.
MS. Prod: xamarin MS. AssetID: 915E25E7-4A6B-4F34-B7B4-07D5F4B240F2 MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 10/30/2020 no-loc:
- "Xamarin.Forms"
- "Xamarin.Essentials"

---
# <a name="implementing-a-view"></a>Implementando uma exibição

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/customrenderers-view)

_Xamarin.Forms controles de interface do usuário personalizados devem derivar da classe View, que é usada para posicionar layouts e controles na tela. Este artigo demonstra como criar um renderizador personalizado para um Xamarin.Forms controle personalizado que é usado para exibir um fluxo de vídeo de visualização da câmera do dispositivo._

Cada Xamarin.Forms exibição tem um renderizador que acompanha para cada plataforma que cria uma instância de um controle nativo. Quando um [`View`](xref:Xamarin.Forms.View) é renderizado por um Xamarin.Forms aplicativo no Ios, a `ViewRenderer` classe é instanciada, que por sua vez instancia um `UIView` controle nativo. Na plataforma Android, a classe `ViewRenderer` cria uma instância de um controle `View` nativo. Na UWP (Plataforma Universal do Windows), a classe `ViewRenderer` cria uma instância de um controle `FrameworkElement` nativo. Para obter mais informações sobre o renderizador e as classes de controle nativo que Xamarin.Forms controlam o mapa para o, consulte [classes base do processador e controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

> [!NOTE]
> Alguns controles no Android usam renderizadores rápidos, que não consomem a `ViewRenderer` classe. Para obter mais informações sobre renderizadores rápidos, consulte [ Xamarin.Forms renderizadores rápidos](~/xamarin-forms/internals/fast-renderers.md).

O diagrama a seguir ilustra a relação entre o [`View`](xref:Xamarin.Forms.View) e os controles nativos correspondentes que o implementam:

![Relação entre a classe View e suas classes de implementação nativas](view-images/view-classes.png)

O processo de renderização pode ser usado para implementar personalizações específicas da plataforma criando um renderizador personalizado para um [`View`](xref:Xamarin.Forms.View) em cada plataforma. O processo para fazer isso é o seguinte:

1. [Crie](#creating-the-custom-control) um Xamarin.Forms controle personalizado.
1. [Consuma](#consuming-the-custom-control) o controle personalizado do Xamarin.Forms .
1. [Criar](#creating-the-custom-renderer-on-each-platform) o renderizador personalizado para o controle em cada plataforma.

Agora, cada item será discutido separadamente, a fim de implementar um renderizador `CameraPreview` que exibe um fluxo de vídeo de visualização usando a câmera do dispositivo. Tocar no fluxo de vídeo o interromperá e iniciará.

## <a name="creating-the-custom-control"></a>Criando o controle personalizado

Um controle personalizado pode ser criado por meio da subclasse da [`View`](xref:Xamarin.Forms.View) classe, conforme mostrado no exemplo de código a seguir:

```csharp
public class CameraPreview : View
{
  public static readonly BindableProperty CameraProperty = BindableProperty.Create (
    propertyName: "Camera",
    returnType: typeof(CameraOptions),
    declaringType: typeof(CameraPreview),
    defaultValue: CameraOptions.Rear);

  public CameraOptions Camera
  {
    get { return (CameraOptions)GetValue (CameraProperty); }
    set { SetValue (CameraProperty, value); }
  }
}
```

O controle personalizado `CameraPreview` é criado no projeto da biblioteca do .NET Standard e define a API para o controle. O controle personalizado expõe uma propriedade `Camera` que é usada para controlar se o fluxo de vídeo deve ser exibido na parte frontal ou traseira da câmera do dispositivo. Se não for especificado um valor para a propriedade `Camera` quando o controle for criado, o comportamento padrão será especificar a câmera traseira.

## <a name="consuming-the-custom-control"></a>Consumindo o controle personalizado

O controle personalizado `CameraPreview` pode ser referenciado em XAML no projeto da biblioteca .NET Standard declarando um namespace para sua localização e usando o prefixo do namespace no elemento de controle personalizado. O exemplo de código a seguir mostra como o controle personalizado `CameraPreview` pode ser consumido por uma página XAML:

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
             ...>
    <StackLayout>
        <Label Text="Camera Preview:" />
        <local:CameraPreview Camera="Rear"
                             HorizontalOptions="FillAndExpand"
                             VerticalOptions="FillAndExpand" />
    </StackLayout>
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
    Content = new StackLayout
    {
      Children =
      {
        new Label { Text = "Camera Preview:" },
        new CameraPreview
        {
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

## <a name="creating-the-custom-renderer-on-each-platform"></a>Criando o renderizador personalizado em cada plataforma

O processo para criar a classe de processador personalizado em iOS e UWP é o seguinte:

1. Crie uma subclasse da classe `ViewRenderer<T1,T2>` que renderiza o controle personalizado. O primeiro argumento de tipo deve ser o controle personalizado a que o renderizador se aplica, neste caso, `CameraPreview`. O segundo argumento de tipo deve ser o controle nativo que implementará o controle personalizado.
1. Substitua o método `OnElementChanged` que renderiza o controle personalizado e escreva a lógica para personalizá-lo. Esse método é chamado quando o Xamarin.Forms controle correspondente é criado.
1. Adicione um `ExportRenderer` atributo à classe de processador personalizado para especificar que ele será usado para renderizar o Xamarin.Forms controle personalizado. Esse atributo é usado para registrar o renderizador personalizado com Xamarin.Forms .

O processo para criar a classe de processador personalizado no Android, como um renderizador rápido, é o seguinte:

1. Crie uma subclasse do controle do Android que renderiza o controle personalizado. Além disso, especifique que a subclasse irá implementar as `IVisualElementRenderer` interfaces e `IViewRenderer` .
1. Implemente `IVisualElementRenderer` as `IViewRenderer` interfaces e na classe de processador rápido.
1. Adicione um `ExportRenderer` atributo à classe de processador personalizado para especificar que ele será usado para renderizar o Xamarin.Forms controle personalizado. Esse atributo é usado para registrar o renderizador personalizado com Xamarin.Forms .

> [!NOTE]
> Para a maioria dos Xamarin.Forms elementos, é opcional fornecer um renderizador personalizado em cada projeto de plataforma. Se um renderizador personalizado não estiver registrado, será usado o renderizador padrão da classe base do controle. No entanto, são necessários renderizadores personalizados em cada projeto da plataforma durante a renderização de um elemento [View](xref:Xamarin.Forms.View).

O diagrama a seguir ilustra as responsabilidades de cada projeto no aplicativo de exemplo, bem como as relações entre elas:

![Responsabilidades do projeto de renderizador personalizado de CameraPreview](view-images/solution-structure.png)

O `CameraPreview` controle personalizado é renderizado por classes de processador específicas da plataforma, que derivam da `ViewRenderer` classe em Ios e UWP, e da `FrameLayout` classe no Android. Isso faz com que cada controle personalizado `CameraPreview` seja renderizado com controles específicos da plataforma, conforme mostrado nas capturas de tela seguir:

![CameraPreview em cada plataforma](view-images/screenshots.png)

A `ViewRenderer` classe expõe o `OnElementChanged` método, que é chamado quando o Xamarin.Forms controle personalizado é criado para renderizar o controle nativo correspondente. Esse método usa um parâmetro `ElementChangedEventArgs`, que contém as propriedades `OldElement` e `NewElement`. Essas propriedades representam o Xamarin.Forms elemento ao qual o renderizador *foi* anexado e o Xamarin.Forms elemento ao qual o renderizador *está* anexado, respectivamente. No aplicativo de exemplo, a propriedade `OldElement` será `null` e a propriedade `NewElement` conterá uma referência à instância de `CameraPreview`.

Uma versão de substituição do método `OnElementChanged`, em cada classe de renderizador específica da plataforma, é o lugar para realização da instanciação e da personalização do controle nativo. O método `SetNativeControl` deve ser usado para instanciar o controle nativo e esse método também atribuirá a referência de controle à propriedade `Control`. Além disso, uma referência ao Xamarin.Forms controle que está sendo processado pode ser obtida por meio da `Element` propriedade.

Em algumas circunstâncias, o método `OnElementChanged` pode ser chamado várias vezes. Portanto, para evitar perdas de memória, é necessário ter cuidado ao instanciar um novo controle nativo. A abordagem a ser usada ao instanciar um novo controle nativo em um renderizador personalizado é mostrada no exemplo de código a seguir:

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<NativeListView> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null)
  {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null)
  {    
    if (Control == null)
    {
      // Instantiate the native control and assign it to the Control property with
      // the SetNativeControl method
    }
    // Configure the control and subscribe to event handlers
  }
}
```

Um novo controle nativo deve ser instanciado apenas uma vez, quando a propriedade `Control` é `null`. Além disso, o controle só deve ser criado, configurado e manipuladores de eventos assinados quando o renderizador personalizado é anexado a um novo Xamarin.Forms elemento. Da mesma forma, a inscrição de quaisquer manipuladores de evento inscritos só deve ser cancelada quando o elemento ao qual o renderizador está anexado for alterado. Adotar essa abordagem ajudará a criar um renderizador personalizado com bom desempenho que não sofre perdas de memória.

> [!IMPORTANT]
> O método `SetNativeControl` só deve ser chamado se `e.NewElement` não for `null`.

Cada classe de processador personalizado é decorada com um `ExportRenderer` atributo que registra o renderizador com Xamarin.Forms . O atributo usa dois parâmetros – o nome do tipo do Xamarin.Forms controle personalizado que está sendo renderizado e o nome do tipo do renderizador personalizado. O prefixo `assembly` do atributo especifica que o atributo se aplica a todo o assembly.

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

Desde que a propriedade `Control` seja `null`, o método `SetNativeControl` será chamado para instanciar um novo controle `UICameraPreview` e para atribuir uma referência a ele para a propriedade `Control`. O controle `UICameraPreview` é um controle personalizado específico da plataforma que usa as APIs `AVCapture` para fornecer o fluxo de visualização da câmera. Ele expõe um evento `Tapped` que é manipulado pelo método `OnCameraPreviewTapped` parar e iniciar a visualização do vídeo quando é tocado. O `Tapped` evento é assinado quando o renderizador personalizado é anexado a um novo Xamarin.Forms elemento e cancela a assinatura somente quando o elemento ao qual o renderizador está anexado é alterado.

### <a name="creating-the-custom-renderer-on-android"></a>Criando o renderizador personalizado no Android

O exemplo de código a seguir mostra o renderizador rápido para a plataforma Android:

```csharp
[assembly: ExportRenderer(typeof(CustomRenderer.CameraPreview), typeof(CameraPreviewRenderer))]
namespace CustomRenderer.Droid
{
    public class CameraPreviewRenderer : FrameLayout, IVisualElementRenderer, IViewRenderer
    {
        // ...
        CameraPreview element;
        VisualElementTracker visualElementTracker;
        VisualElementRenderer visualElementRenderer;
        FragmentManager fragmentManager;
        CameraFragment cameraFragment;

        FragmentManager FragmentManager => fragmentManager ??= Context.GetFragmentManager();

        public event EventHandler<VisualElementChangedEventArgs> ElementChanged;
        public event EventHandler<PropertyChangedEventArgs> ElementPropertyChanged;

        CameraPreview Element
        {
            get => element;
            set
            {
                if (element == value)
                {
                    return;
                }

                var oldElement = element;
                element = value;
                OnElementChanged(new ElementChangedEventArgs<CameraPreview>(oldElement, element));
            }
        }

        public CameraPreviewRenderer(Context context) : base(context)
        {
            visualElementRenderer = new VisualElementRenderer(this);
        }

        void OnElementChanged(ElementChangedEventArgs<CameraPreview> e)
        {
            CameraFragment newFragment = null;

            if (e.OldElement != null)
            {
                e.OldElement.PropertyChanged -= OnElementPropertyChanged;
                cameraFragment.Dispose();
            }
            if (e.NewElement != null)
            {
                this.EnsureId();

                e.NewElement.PropertyChanged += OnElementPropertyChanged;

                ElevationHelper.SetElevation(this, e.NewElement);
                newFragment = new CameraFragment { Element = element };
            }

            FragmentManager.BeginTransaction()
                .Replace(Id, cameraFragment = newFragment, "camera")
                .Commit();
            ElementChanged?.Invoke(this, new VisualElementChangedEventArgs(e.OldElement, e.NewElement));
        }

        async void OnElementPropertyChanged(object sender, PropertyChangedEventArgs e)
        {
            ElementPropertyChanged?.Invoke(this, e);

            switch (e.PropertyName)
            {
                case "Width":
                    await cameraFragment.RetrieveCameraDevice();
                    break;
            }
        }       
        // ...
    }
}
```

Neste exemplo, o `OnElementChanged` método cria um `CameraFragment` objeto, desde que o renderizador personalizado esteja anexado a um novo Xamarin.Forms elemento. O `CameraFragment` tipo é uma classe personalizada que usa a `Camera2` API para fornecer o fluxo de visualização da câmera. O `CameraFragment` objeto é Descartado quando o Xamarin.Forms elemento ao qual o renderizador é anexado é alterado.

### <a name="creating-the-custom-renderer-on-uwp"></a>Criando o renderizador personalizado na UWP

O seguinte exemplo de código mostra o renderizador personalizado para o UWP:

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

Desde que a propriedade `Control` seja `null`, um novo `CaptureElement` será instanciado e o método `SetupCamera` será chamado, que usa a API `MediaCapture` para fornecer o fluxo de visualização da câmera. Em seguida, o método `SetNativeControl` é chamado para atribuir uma referência para a instância `CaptureElement` para a propriedade `Control`. O controle `CaptureElement` expõe um evento `Tapped` que é manipulado pelo método `OnCameraPreviewTapped` para parar e iniciar a visualização do vídeo quando é tocado. O `Tapped` evento é assinado quando o renderizador personalizado é anexado a um novo Xamarin.Forms elemento e cancela a assinatura somente quando o elemento ao qual o renderizador está anexado é alterado.

> [!NOTE]
> É importante parar e descartar os objetos que fornecem acesso à câmera em um aplicativo da UWP. Deixar de fazer isso pode interferir em outros aplicativos que tentam acessar a câmera do dispositivo. Para obter mais informações, confira [Exibir a visualização da câmera](/windows/uwp/audio-video-camera/simple-camera-preview-access/).

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar um renderizador personalizado para um Xamarin.Forms controle personalizado usado para exibir um fluxo de vídeo de visualização da câmera do dispositivo. Xamarin.Forms os controles personalizados da interface do usuário devem derivar da [`View`](xref:Xamarin.Forms.View) classe, que é usada para posicionar layouts e controles na tela.

## <a name="related-links"></a>Links Relacionados

- [CustomRendererView (amostra)](/samples/xamarin/xamarin-forms-samples/customrenderers-view)