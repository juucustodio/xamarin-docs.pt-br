---
title: Personalizando um marcador de mapa
description: Este artigo demonstra como criar um renderizador personalizado para o Controle de Mapeamento, que exibe um mapa nativo com um marcador personalizado e uma exibição personalizada dos dados de marcador em cada plataforma.
ms.prod: xamarin
ms.assetid: C5481D86-80E9-4E3D-9FB6-57B0F93711A6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 8df5b373fccdef93a8ffbc66fd53a94378f47a6e
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68650827"
---
# <a name="customizing-a-map-pin"></a>Personalizando um marcador de mapa

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-map-pin)

_Este artigo demonstra como criar um renderizador personalizado para o Controle de Mapeamento, que exibe um mapa nativo com um marcador personalizado e uma exibição personalizada dos dados de marcador em cada plataforma._

Cada exibição do Xamarin.Forms tem um renderizador que o acompanha para cada plataforma que cria uma instância de um controle nativo. Quando um [`Map`](xref:Xamarin.Forms.Maps.Map) é renderizado por um aplicativo Xamarin.Forms no iOS, é criada uma instância da classe `MapRenderer`, que, por sua vez, cria uma instância de um controle `MKMapView` nativo. Na plataforma Android, a classe `MapRenderer` cria uma instância de um controle `MapView` nativo. Na UWP (Plataforma Universal do Windows), a classe `MapRenderer` cria uma instância de um `MapControl` nativo. Para obter mais informações sobre as classes de renderizador e de controle nativo para as quais os controles do Xamarin.Forms são mapeadas, confira [Classes base do renderizador e controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

O seguinte diagrama ilustra a relação entre o [`Map`](xref:Xamarin.Forms.Maps.Map) e os controles nativos correspondentes que o implementam:

![](customized-pin-images/map-classes.png "Relação entre o Controle de Mapeamento e a implementação de controles nativos")

E possível usar o processo de renderização para implementar personalizações específicas da plataforma criando um renderizador personalizado para um [`Map`](xref:Xamarin.Forms.Maps.Map) em cada plataforma. O processo para fazer isso é o seguinte:

1. [Criar](#Creating_the_Custom_Map) um mapa personalizado do Xamarin.Forms.
1. [Consumir](#Consuming_the_Custom_Map) o mapa personalizado do Xamarin.Forms.
1. [Criar](#Creating_the_Custom_Renderer_on_each_Platform) o renderizador personalizado para o mapa em cada plataforma.

Cada item agora será abordado por vez, para implementar um renderizador `CustomMap` que exibe um mapa nativo com um marcador personalizado e uma exibição personalizada dos dados do marcador em cada plataforma.

> [!NOTE]
> [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) precisa ser inicializado e configurado antes do uso. Para obter mais informações, consulte [`Maps Control`](~/xamarin-forms/user-interface/map.md).

<a name="Creating_the_Custom_Map" />

## <a name="creating-the-custom-map"></a>Criando o mapa personalizado

É possível criar um Controle de Mapeamento personalizado criando subclasses da classe [`Map`](xref:Xamarin.Forms.Maps.Map), conforme mostrado no seguinte exemplo de código:

```csharp
public class CustomMap : Map
{
  public List<CustomPin> CustomPins { get; set; }
}
```

O controle `CustomMap` é criado no projeto da biblioteca do .NET Standard e define a API para o mapa personalizado. O mapa personalizado expõe a propriedade `CustomPins` que representa a coleção de objetos `CustomPin` que serão renderizados pelo Controle de Mapeamento nativo em cada plataforma. A classe `CustomPin` é mostrada no seguinte exemplo de código:

```csharp
public class CustomPin : Pin
{
  public string Url { get; set; }
}
```

Essa classe define um `CustomPin` como herdando as propriedades da classe [`Pin`](xref:Xamarin.Forms.Maps.Pin) e adicionando uma propriedade `Url`.

<a name="Consuming_the_Custom_Map" />

## <a name="consuming-the-custom-map"></a>Consumindo o mapa personalizado

O controle `CustomMap` pode ser referenciado em XAML no projeto da biblioteca do .NET Standard declarando um namespace para sua localização e usando o prefixo do namespace no Controle de Mapeamento personalizado. O seguinte exemplo de código mostra como o controle `CustomMap` pode ser consumido por uma página XAML:

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer">
    <ContentPage.Content>
        <local:CustomMap x:Name="myMap" MapType="Street"
          WidthRequest="{x:Static local:App.ScreenWidth}"
          HeightRequest="{x:Static local:App.ScreenHeight}" />
    </ContentPage.Content>
</ContentPage>
```

O prefixo do namespace `local` pode ter qualquer nome. No entanto, os valores de `clr-namespace` e `assembly` devem corresponder aos detalhes do mapa personalizado. Quando o namespace é declarado, o prefixo é usado para referenciar o mapa personalizado.

O seguinte exemplo de código mostra como o controle `CustomMap` pode ser consumido por uma página em C#:

```csharp
public class MapPageCS : ContentPage
{
  public MapPageCS ()
  {
    var customMap = new CustomMap {
      MapType = MapType.Street,
      WidthRequest = App.ScreenWidth,
      HeightRequest = App.ScreenHeight
    };
    ...

    Content = customMap;
  }
}
```

A instância `CustomMap` será usada para exibir o mapa nativo em cada plataforma. Sua propriedade [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) define o estilo de exibição do [`Map`](xref:Xamarin.Forms.Maps.Map), com os valores possíveis sendo definidos na enumeração [`MapType`](xref:Xamarin.Forms.Maps.MapType). Para o iOS e o Android, a largura e a altura do mapa são definidas por meio das propriedades da classe `App` são inicializadas nos projetos específicos da plataforma.

A localização do mapa e os marcadores que ele contém são inicializados conforme mostrado no seguinte exemplo de código:

```csharp
public MapPage ()
{
  ...
  var pin = new CustomPin {
    Type = PinType.Place,
    Position = new Position (37.79752, -122.40183),
    Label = "Xamarin San Francisco Office",
    Address = "394 Pacific Ave, San Francisco CA",
    MarkerId = "Xamarin",
    Url = "http://xamarin.com/about/"
  };

  customMap.CustomPins = new List<CustomPin> { pin };
  customMap.Pins.Add (pin);
  customMap.MoveToRegion (MapSpan.FromCenterAndRadius (
    new Position (37.79752, -122.40183), Distance.FromMiles (1.0)));
}
```

Essa inicialização adiciona um marcador personalizado e posiciona a exibição do mapa com o método [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*), que altera a posição e o nível de aplicação de zoom do mapa com a criação de um [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) com base em uma [`Position`](xref:Xamarin.Forms.Maps.Position) e uma [`Distance`](xref:Xamarin.Forms.Maps.Distance).

Agora, um renderizador personalizado pode ser adicionado a cada projeto de aplicativo para personalizar os controles de mapa nativos.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Criando o renderizador personalizado em cada plataforma

O processo para criar a classe do renderizador personalizado é o seguinte:

1. Criar uma subclasse da classe `MapRenderer` que renderiza o mapa personalizado.
1. Substituir o método `OnElementChanged` que renderiza o mapa personalizado e escrever a lógica para personalizá-lo. Esse método é chamado quando o mapa personalizado do Xamarin.Forms correspondente é criado.
1. Adicione um atributo `ExportRenderer` à classe de renderizador personalizado para especificar que ele será usado para renderizar o mapa personalizado do Xamarin.Forms. Esse atributo é usado para registrar o renderizador personalizado no Xamarin.Forms.

> [!NOTE]
> O fornecimento de um renderizador personalizado em cada projeto da plataforma é opcional. Se um renderizador personalizado não estiver registrado, será usado o renderizador padrão da classe base do controle.

O seguinte diagrama ilustra as responsabilidades de cada projeto no aplicativo de exemplo, bem como as relações entre elas:

![](customized-pin-images/solution-structure.png "Responsabilidades do projeto de renderizador personalizado de CustomMap")

O controle `CustomMap` é renderizado por classes de renderizador específicas da plataforma, que derivam da classe `MapRenderer` para cada plataforma. Isso faz com que cada controle `CustomMap` seja renderizado com controles específicos da plataforma, conforme mostrado nas seguintes capturas de tela:

![](customized-pin-images/screenshots.png "CustomMap em cada plataforma")

A classe `MapRenderer` expõe o método `OnElementChanged`, que é chamado quando o mapa personalizado do Xamarin.Forms é criado para renderizar o controle nativo correspondente. Esse método usa um parâmetro `ElementChangedEventArgs`, que contém as propriedades `OldElement` e `NewElement`. Essas propriedades representam o elemento do Xamarin.Forms ao qual o renderizador *estava* anexado e o elemento do Xamarin.Forms ao qual o renderizador *está* anexado, respectivamente. No aplicativo de exemplo, a propriedade `OldElement` será `null` e a propriedade `NewElement` conterá uma referência à instância de `CustomMap`.

Uma versão de substituição do método `OnElementChanged`, em cada classe de renderizador específica da plataforma, é o lugar para realizar a personalização do controle nativo. Uma referência tipada ao controle nativo que está sendo usado na plataforma pode ser acessada por meio da propriedade `Control`. Além disso, é possível obter uma referência ao controle do Xamarin.Forms que está sendo renderizado por meio da propriedade `Element`.

É necessário ter cuidado ao assinar manipuladores de eventos no método `OnElementChanged`, conforme demonstrado no seguinte exemplo de código:

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<Xamarin.Forms.View> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null) {
    // Unsubscribe from event handlers
  }

  if (e.NewElement != null) {
    // Configure the native control and subscribe to event handlers
  }
}
```

O controle nativo deverá ser configurado e os manipuladores de eventos deverão ser assinados apenas quando o renderizador personalizado for anexado a um novo elemento do Xamarin.Forms. De forma semelhante, a assinatura dos manipuladores de eventos assinados só deverá ser cancelada quando o elemento ao qual o renderizador está anexado for alterado. A adoção dessa abordagem ajudará a criar um renderizador personalizado que não sofre perdas de memória.

Cada classe de renderizador personalizado é decorada com um atributo `ExportRenderer` que registra o renderizador no Xamarin.Forms. O atributo aceita dois parâmetros – o nome do tipo de controle personalizado do Xamarin.Forms que está sendo renderizado e o nome do tipo de renderizador personalizado. O prefixo `assembly` do atributo especifica que o atributo se aplica a todo o assembly.

As seções a seguir abordam a implementação de cada classe de renderizador personalizado específica da plataforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Criando o renderizador personalizado no iOS

As seguintes capturas de tela mostram o mapa, antes e após a personalização:

![](customized-pin-images/map-layout-ios.png "Controle de Mapeamento antes e após a personalização")

No iOS, o marcador é chamado de *anotação* e pode ser uma imagem personalizada ou um marcador definido pelo sistema de várias cores. As anotações podem opcionalmente mostrar um *texto explicativo*, que é exibido em resposta à seleção da anotação pelo usuário. O texto explicativo exibe o `Label` e as propriedades `Address` da instância `Pin`, com as exibições acessório direita e esquerda opcionais. Na captura de tela acima, a exibição acessório esquerda é a imagem de um macaco, com a exibição acessório direita sendo o botão *Informações*.

O seguinte exemplo de código mostra o renderizador personalizado para a plataforma iOS:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace CustomRenderer.iOS
{
    public class CustomMapRenderer : MapRenderer
    {
        UIView customPinView;
        List<CustomPin> customPins;

        protected override void OnElementChanged(ElementChangedEventArgs<View> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null) {
                var nativeMap = Control as MKMapView;
                if (nativeMap != null) {
                    nativeMap.RemoveAnnotations(nativeMap.Annotations);
                    nativeMap.GetViewForAnnotation = null;
                    nativeMap.CalloutAccessoryControlTapped -= OnCalloutAccessoryControlTapped;
                    nativeMap.DidSelectAnnotationView -= OnDidSelectAnnotationView;
                    nativeMap.DidDeselectAnnotationView -= OnDidDeselectAnnotationView;
                }
            }

            if (e.NewElement != null) {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MKMapView;
                customPins = formsMap.CustomPins;

                nativeMap.GetViewForAnnotation = GetViewForAnnotation;
                nativeMap.CalloutAccessoryControlTapped += OnCalloutAccessoryControlTapped;
                nativeMap.DidSelectAnnotationView += OnDidSelectAnnotationView;
                nativeMap.DidDeselectAnnotationView += OnDidDeselectAnnotationView;
            }
        }
        ...
    }
}
```

O método `OnElementChanged` executa a seguinte configuração da instância [`MKMapView`](xref:MapKit.MKMapView), desde que o renderizador personalizado esteja anexado a um novo elemento do Xamarin.Forms:

- A propriedade [`GetViewForAnnotation`](xref:MapKit.MKMapView.GetViewForAnnotation*) é definida como o método `GetViewForAnnotation`. Esse método é chamado quando a [localização da anotação se torna visível no mapa](#Displaying_the_Annotation) e é usado para personalizar a anotação antes da exibição.
- Os manipuladores de eventos para os eventos `CalloutAccessoryControlTapped`, `DidSelectAnnotationView` e `DidDeselectAnnotationView` são registrados. Esses eventos são disparados quando o usuário [toca o acessório direito no texto explicativo](#Tapping_on_the_Right_Callout_Accessory_View) e quando o usuário [marca](#Selecting_the_Annotation) e [desmarca](#Deselecting_the_Annotation) a anotação, respectivamente. A assinatura dos eventos é cancelada somente quando o elemento ao qual o renderizador está anexado é alterado.

<a name="Displaying_the_Annotation" />

#### <a name="displaying-the-annotation"></a>Exibindo a anotação

O método `GetViewForAnnotation` é chamado quando a localização da anotação se torna visível no mapa e é usado para personalizar a anotação antes da exibição. Uma anotação tem duas partes:

- `MkAnnotation` – inclui o título, o subtítulo e a localização da anotação.
- `MkAnnotationView` – contém a imagem para representar a anotação e, opcionalmente, um texto explicativo que é mostrado quando o usuário toca a anotação.

O método `GetViewForAnnotation` aceita uma `IMKAnnotation` que contém os dados da anotação e retorna uma `MKAnnotationView` para exibição no mapa e é mostrado no seguinte exemplo de código:

```csharp
protected override MKAnnotationView GetViewForAnnotation(MKMapView mapView, IMKAnnotation annotation)
{
    MKAnnotationView annotationView = null;

    if (annotation is MKUserLocation)
        return null;

    var customPin = GetCustomPin(annotation as MKPointAnnotation);
    if (customPin == null) {
        throw new Exception("Custom pin not found");
    }

    annotationView = mapView.DequeueReusableAnnotation(customPin.MarkerId.ToString());
    if (annotationView == null) {
        annotationView = new CustomMKAnnotationView(annotation, customPin.MarkerId.ToString());
        annotationView.Image = UIImage.FromFile("pin.png");
        annotationView.CalloutOffset = new CGPoint(0, 0);
        annotationView.LeftCalloutAccessoryView = new UIImageView(UIImage.FromFile("monkey.png"));
        annotationView.RightCalloutAccessoryView = UIButton.FromType(UIButtonType.DetailDisclosure);
        ((CustomMKAnnotationView)annotationView).MarkerId = customPin.MarkerId.ToString();
        ((CustomMKAnnotationView)annotationView).Url = customPin.Url;
    }
    annotationView.CanShowCallout = true;

    return annotationView;
}
```

Esse método garante que a anotação seja exibida como uma imagem personalizada, em vez de como um marcador definido pelo sistema, e que quando a anotação for tocada, um texto explicativo seja exibido que inclua conteúdo adicional à esquerda e à direita do título da anotação e do endereço. Isso é feito da seguinte maneira:

1. O método `GetCustomPin` é chamado para retornar os dados de marcador personalizados da anotação.
1. Para conservar a memória, a exibição da anotação é colocada em pool para reutilização com a chamada a [`DequeueReusableAnnotation`](xref:MapKit.MKMapView.DequeueReusableAnnotation*).
1. A classe `CustomMKAnnotationView` estende a classe `MKAnnotationView` com as propriedades `MarkerId` e `Url` que correspondem às propriedades idênticas na instância `CustomPin`. Uma nova instância da `CustomMKAnnotationView` é criada, desde que a anotação seja `null`:
    - A propriedade `CustomMKAnnotationView.Image` está definida como a imagem que representará a anotação no mapa.
    - A propriedade `CustomMKAnnotationView.CalloutOffset` é definida como um `CGPoint` que especifica que o texto explicativo será centralizado acima da anotação.
    - A propriedade `CustomMKAnnotationView.LeftCalloutAccessoryView` é definida como uma imagem de um macaco que será exibido à esquerda do título da anotação e do endereço.
    - A propriedade `CustomMKAnnotationView.RightCalloutAccessoryView` é definida como um botão *Informações* que será exibido à direita do título da anotação e do endereço.
    - A propriedade `CustomMKAnnotationView.MarkerId` é definida como a propriedade `CustomPin.MarkerId` retornada pelo método `GetCustomPin`. Isso permite que a anotação seja identificada para que seu [texto explicativo possa ser personalizado ainda mais](#Selecting_the_Annotation), se desejado.
    - A propriedade `CustomMKAnnotationView.Url` é definida como a propriedade `CustomPin.Url` retornada pelo método `GetCustomPin`. A URL será direcionada quando o usuário [tocar o botão exibido na exibição acessório direita do texto explicativo](#Tapping_on_the_Right_Callout_Accessory_View).
1. A propriedade [`MKAnnotationView.CanShowCallout`](xref:MapKit.MKAnnotationView.CanShowCallout*) é definida como `true` para que o texto explicativo seja exibido quando a anotação é tocada.
1. A anotação é retornada para a exibição no mapa.

<a name="Selecting_the_Annotation" />

#### <a name="selecting-the-annotation"></a>Selecionando a anotação

Quando o usuário toca a anotação, o evento `DidSelectAnnotationView` é disparado, que, por sua vez, executa o método `OnDidSelectAnnotationView`:

```csharp
void OnDidSelectAnnotationView (object sender, MKAnnotationViewEventArgs e)
{
  var customView = e.View as CustomMKAnnotationView;
  customPinView = new UIView ();

  if (customView.MarkerId == "Xamarin") {
    customPinView.Frame = new CGRect (0, 0, 200, 84);
    var image = new UIImageView (new CGRect (0, 0, 200, 84));
    image.Image = UIImage.FromFile ("xamarin.png");
    customPinView.AddSubview (image);
    customPinView.Center = new CGPoint (0, -(e.View.Frame.Height + 75));
    e.View.AddSubview (customPinView);
  }
}
```

Esse método estende o texto explicativo existente (que contém as exibições acessório esquerda e direita), adicionando uma instância `UIView` a ele que contém uma imagem do logotipo do Xamarin, desde que a anotação selecionada tenha sua propriedade `MarkerId` definida como `Xamarin`. Isso permite cenários em que diferentes textos explicativos podem ser exibidos para diferentes anotações. A instância `UIView` será exibida centralizada acima do texto explicativo existente.

<a name="Tapping_on_the_Right_Callout_Accessory_View" />

#### <a name="tapping-on-the-right-callout-accessory-view"></a>Tocando a exibição acessório direita do texto explicativo

Quando o usuário toca o botão *Informações* na exibição acessório direita do texto explicativo, o evento `CalloutAccessoryControlTapped` é disparado, que, por sua vez, executa o método `OnCalloutAccessoryControlTapped`:

```csharp
void OnCalloutAccessoryControlTapped (object sender, MKMapViewAccessoryTappedEventArgs e)
{
  var customView = e.View as CustomMKAnnotationView;
  if (!string.IsNullOrWhiteSpace (customView.Url)) {
    UIApplication.SharedApplication.OpenUrl (new Foundation.NSUrl (customView.Url));
  }
}
```

Esse método abre um navegador da Web e navega para o endereço armazenado na propriedade `CustomMKAnnotationView.Url`. Observe que o endereço foi definido durante a criação da coleção `CustomPin` no projeto da biblioteca do .NET Standard.

<a name="Deselecting_the_Annotation" />

#### <a name="deselecting-the-annotation"></a>Cancelando a seleção da anotação

Quando a anotação é exibida e o usuário toca o mapa, o evento `DidDeselectAnnotationView` é disparado, que, por sua vez, executa o método `OnDidDeselectAnnotationView`:

```csharp
void OnDidDeselectAnnotationView (object sender, MKAnnotationViewEventArgs e)
{
  if (!e.View.Selected) {
    customPinView.RemoveFromSuperview ();
    customPinView.Dispose ();
    customPinView = null;
  }
}
```

Esse método garante que, quando o texto explicativo existente não estiver selecionado, a parte estendida do texto explicativo (a imagem do logotipo do Xamarin) também não será mais exibida e seus recursos serão liberados.

Para obter mais informações sobre como personalizar uma instância `MKMapView`, confira [Mapas do iOS](~/ios/user-interface/controls/ios-maps/index.md).

### <a name="creating-the-custom-renderer-on-android"></a>Criando o renderizador personalizado no Android

As seguintes capturas de tela mostram o mapa, antes e após a personalização:

![](customized-pin-images/map-layout-android.png "Controle de Mapeamento antes e após a personalização")

No Android, o marcador é chamado de *marcador* e pode ser uma imagem personalizada ou um marcador definido pelo sistema de várias cores. Os marcadores podem mostrar uma *janela de informações*, que é exibida na resposta ao toque do usuário no marcador. A janela de informações exibe as propriedades `Label` e `Address` da instância `Pin` e pode ser personalizada para incluir outros tipos de conteúdo. No entanto, apenas uma janela de informações pode ser mostrada por vez.

O seguinte exemplo de código mostra o renderizador personalizado para a plataforma Android:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace CustomRenderer.Droid
{
    public class CustomMapRenderer : MapRenderer, GoogleMap.IInfoWindowAdapter
    {
        List<CustomPin> customPins;

        public CustomMapRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(Xamarin.Forms.Platform.Android.ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                NativeMap.InfoWindowClick -= OnInfoWindowClick;
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                customPins = formsMap.CustomPins;
                Control.GetMapAsync(this);
            }
        }

        protected override void OnMapReady(GoogleMap map)
        {
            base.OnMapReady(map);

            NativeMap.InfoWindowClick += OnInfoWindowClick;
            NativeMap.SetInfoWindowAdapter(this);
        }
        ...
    }
}
```

Desde que o renderizador personalizado esteja anexado a um novo elemento do Xamarin.Forms, o método `OnElementChanged` chamará o método `MapView.GetMapAsync`, que obterá o `GoogleMap` subjacente que está vinculado à exibição. Depois que a instância `GoogleMap` estiver disponível, a substituição `OnMapReady` será invocada. Esse método registra um manipulador de eventos para o evento `InfoWindowClick`, que é disparado quando a [janela de informações recebe um clique](#Clicking_on_the_Info_Window) e tem a assinatura cancelada somente quando o elemento ao qual o renderizador está anexado é alterado. A substituição `OnMapReady` também chama o método `SetInfoWindowAdapter` para especificar que a instância da classe `CustomMapRenderer` fornecerá os métodos para personalizar a janela de informações.

A classe `CustomMapRenderer` implementa a interface `GoogleMap.IInfoWindowAdapter` para [personalizar a janela de informações](#Customizing_the_Info_Window). Essa interface especifica que os seguintes métodos precisam ser implementados:

- `public Android.Views.View GetInfoWindow(Marker marker)` – esse método é chamado para retornar uma janela de informações personalizada para um marcador. Se ele retornar `null`, a renderização de janela padrão será usada. Se ele retornar uma `View`, essa `View` será colocada dentro do quadro da janela de informações.
- `public Android.Views.View GetInfoContents(Marker marker)` – esse método é chamado para retornar uma `View` que traz o conteúdo da janela de informações e só será chamado se o método `GetInfoWindow` retornar `null`. Se ele retornar `null`, a renderização padrão do conteúdo da janela de informações será usada.

No aplicativo de exemplo, somente o conteúdo da janela de informações é personalizado e, portanto, o método `GetInfoWindow` retorna `null` para permitir isso.

#### <a name="customizing-the-marker"></a>Personalizando o marcador

O ícone usado para representar um marcador pode ser personalizado por meio da chamada ao método `MarkerOptions.SetIcon`. Isso pode ser feito pela substituição do método `CreateMarker`, que é invocado para cada `Pin` adicionado ao mapa:

```csharp
protected override MarkerOptions CreateMarker(Pin pin)
{
    var marker = new MarkerOptions();
    marker.SetPosition(new LatLng(pin.Position.Latitude, pin.Position.Longitude));
    marker.SetTitle(pin.Label);
    marker.SetSnippet(pin.Address);
    marker.SetIcon(BitmapDescriptorFactory.FromResource(Resource.Drawable.pin));
    return marker;
}
```

Esse método cria uma instância `MarkerOption` para cada instância `Pin`. Depois de definir a posição, o rótulo e o endereço do marcador, seu ícone será definido com o método `SetIcon`. Esse método usa um objeto `BitmapDescriptor` que contém os dados necessários para renderizar o ícone, com a classe `BitmapDescriptorFactory` fornecendo métodos auxiliares para simplificar a criação do `BitmapDescriptor`. Para obter mais informações sobre como usar a classe `BitmapDescriptorFactory` para personalizar um marcador, confira [Personalizando um marcador](~/android/platform/maps-and-location/maps/maps-api.md).

> [!NOTE]
> Se necessário, o método `GetMarkerForPin` pode ser invocado no renderizador de mapa para recuperar um `Marker` de um `Pin`.

<a name="Customizing_the_Info_Window" />

#### <a name="customizing-the-info-window"></a>Personalizando a janela de informações

Quando um usuário toca o marcador, o método `GetInfoContents` é executado, desde que o método `GetInfoWindow` retorne `null`. O seguinte exemplo de código mostra o método `GetInfoContents`:

```csharp
public Android.Views.View GetInfoContents (Marker marker)
{
  var inflater = Android.App.Application.Context.GetSystemService (Context.LayoutInflaterService) as Android.Views.LayoutInflater;
  if (inflater != null) {
    Android.Views.View view;

    var customPin = GetCustomPin (marker);
    if (customPin == null) {
      throw new Exception ("Custom pin not found");
    }

    if (customPin.MarkerId.ToString() == "Xamarin") {
      view = inflater.Inflate (Resource.Layout.XamarinMapInfoWindow, null);
    } else {
      view = inflater.Inflate (Resource.Layout.MapInfoWindow, null);
    }

    var infoTitle = view.FindViewById<TextView> (Resource.Id.InfoWindowTitle);
    var infoSubtitle = view.FindViewById<TextView> (Resource.Id.InfoWindowSubtitle);

    if (infoTitle != null) {
      infoTitle.Text = marker.Title;
    }
    if (infoSubtitle != null) {
      infoSubtitle.Text = marker.Snippet;
    }

    return view;
  }
  return null;
}
```

Esse método retorna uma `View` que traz o conteúdo da janela de informações. Isso é feito da seguinte maneira:

- Uma instância `LayoutInflater` é recuperada. Isso é usado para criar uma instância de um arquivo XML de layout em sua `View` correspondente.
- O método `GetCustomPin` é chamado para retornar os dados de marcador personalizados para a janela de informações.
- O layout `XamarinMapInfoWindow` é inflado se a propriedade `CustomPin.MarkerId` é igual a `Xamarin`. Caso contrário, o layout `MapInfoWindow` é inflado. Isso permite cenários em que diferentes layouts da janela de informações podem ser exibidos para diferentes marcadores.
- Os recursos `InfoWindowTitle` e `InfoWindowSubtitle` são recuperados do layout inflado e suas propriedades `Text` são definidas com os dados correspondentes por meio da instância `Marker`, desde que os recursos não sejam `null`.
- A instância `View` é retornada para exibição no mapa.

> [!NOTE]
> Uma janela de informações não é uma `View` dinâmica. Em vez disso, o Android converterá a `View` em um bitmap estático e os exibirá como uma imagem. Isso significa que, embora uma janela de informações possa responder a um evento de clique, ela não pode responder a eventos de toque ou gestos e os controles individuais na janela de informações não podem responder a seus próprios eventos de clique.

<a name="Clicking_on_the_Info_Window" />

#### <a name="clicking-on-the-info-window"></a>Clicando na janela de informações

Quando o usuário clica a janela de informações, o evento `InfoWindowClick` é disparado, que, por sua vez, executa o método `OnInfoWindowClick`:

```csharp
void OnInfoWindowClick (object sender, GoogleMap.InfoWindowClickEventArgs e)
{
  var customPin = GetCustomPin (e.Marker);
  if (customPin == null) {
    throw new Exception ("Custom pin not found");
  }

  if (!string.IsNullOrWhiteSpace (customPin.Url)) {
    var url = Android.Net.Uri.Parse (customPin.Url);
    var intent = new Intent (Intent.ActionView, url);
    intent.AddFlags (ActivityFlags.NewTask);
    Android.App.Application.Context.StartActivity (intent);
  }
}
```

Esse método abre um navegador da Web e navega para o endereço armazenado na propriedade `Url` da instância `CustomPin` recuperada para o `Marker`. Observe que o endereço foi definido durante a criação da coleção `CustomPin` no projeto da biblioteca do .NET Standard.

Para obter mais informações sobre como personalizar uma instância `MapView`, confira [API de Mapas](~/android/platform/maps-and-location/maps/maps-api.md).

### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Criando o renderizador personalizado na Plataforma Universal do Windows

As seguintes capturas de tela mostram o mapa, antes e após a personalização:

![](customized-pin-images/map-layout-uwp.png "Controle de Mapeamento antes e após a personalização")

No UWP, o marcador é chamado de *ícone de mapa* e pode ser uma imagem personalizada ou a imagem padrão definida pelo sistema. Um ícone de mapa pode mostrar um `UserControl`, que é exibido em resposta ao toque do usuário no ícone de mapa. O `UserControl` pode exibir qualquer conteúdo, incluindo as propriedades `Label` e `Address` da instância `Pin`.

O seguinte exemplo de código mostra o renderizador personalizado do UWP:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace CustomRenderer.UWP
{
    public class CustomMapRenderer : MapRenderer
    {
        MapControl nativeMap;
        List<CustomPin> customPins;
        XamarinMapOverlay mapOverlay;
        bool xamarinOverlayShown = false;

        protected override void OnElementChanged(ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                nativeMap.MapElementClick -= OnMapElementClick;
                nativeMap.Children.Clear();
                mapOverlay = null;
                nativeMap = null;
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                nativeMap = Control as MapControl;
                customPins = formsMap.CustomPins;

                nativeMap.Children.Clear();
                nativeMap.MapElementClick += OnMapElementClick;

                foreach (var pin in customPins)
                {
                    var snPosition = new BasicGeoposition { Latitude = pin.Pin.Position.Latitude, Longitude = pin.Pin.Position.Longitude };
                    var snPoint = new Geopoint(snPosition);

                    var mapIcon = new MapIcon();
                    mapIcon.Image = RandomAccessStreamReference.CreateFromUri(new Uri("ms-appx:///pin.png"));
                    mapIcon.CollisionBehaviorDesired = MapElementCollisionBehavior.RemainVisible;
                    mapIcon.Location = snPoint;
                    mapIcon.NormalizedAnchorPoint = new Windows.Foundation.Point(0.5, 1.0);

                    nativeMap.MapElements.Add(mapIcon);                    
                }
            }
        }
        ...
    }
}
```

O método `OnElementChanged` executa as seguintes operações, desde que o renderizador personalizado esteja anexado a um novo elemento do Xamarin.Forms:

- Ele limpa a coleção `MapControl.Children` para remover os elementos de interface do usuário existentes do mapa, antes de registrar um manipulador de eventos para o evento `MapElementClick`. Esse evento é disparado quando o usuário toca um `MapElement` ou clica nele no `MapControl` e tem a assinatura cancelada somente quando o elemento ao qual o renderizador está anexado é alterado.
- Cada marcador da coleção `customPins` é exibido na localização geográfica correta no mapa da seguinte maneira:
  - A localização para o marcador é criado como uma instância `Geopoint`.
  - Uma instância `MapIcon` é criada para representar o marcador.
  - A imagem usada para representar o `MapIcon` é especificada definindo a propriedade `MapIcon.Image`. No entanto, não há a garantia de que a imagem do ícone de mapa seja sempre mostrada, pois ela pode ser obscurecida por outros elementos no mapa. Portanto, a propriedade `CollisionBehaviorDesired` do ícone de mapa é definida como `MapElementCollisionBehavior.RemainVisible`, para garantir que ela permaneça visível.
  - A localização do `MapIcon` é especificado definindo a propriedade `MapIcon.Location`.
  - A propriedade `MapIcon.NormalizedAnchorPoint` é definida como a localização aproximada do ponteiro na imagem. Se essa propriedade retiver seu valor padrão de (0,0), que representa o canto superior esquerdo da imagem, as alterações no nível de aplicação de zoom do mapa poderão fazer com que a imagem aponte para uma localização diferente.
  - A instância `MapIcon` é adicionada à coleção `MapControl.MapElements`. Isso resulta na exibição do ícone de mapa no `MapControl`.

> [!NOTE]
> Ao usar a mesma imagem para vários ícones de mapa, a instância `RandomAccessStreamReference` deve ser declarada no nível de página ou de aplicativo para melhor desempenho.

#### <a name="displaying-the-usercontrol"></a>Exibindo o UserControl

Quando um usuário toca o ícone de mapa, o método `OnMapElementClick` é executado. O seguinte exemplo de código mostra esse método:

```csharp
private void OnMapElementClick(MapControl sender, MapElementClickEventArgs args)
{
    var mapIcon = args.MapElements.FirstOrDefault(x => x is MapIcon) as MapIcon;
    if (mapIcon != null)
    {
        if (!xamarinOverlayShown)
        {
            var customPin = GetCustomPin(mapIcon.Location.Position);
            if (customPin == null)
            {
                throw new Exception("Custom pin not found");
            }

            if (customPin.MarkerId.ToString() == "Xamarin")
            {
                if (mapOverlay == null)
                {
                    mapOverlay = new XamarinMapOverlay(customPin);
                }

                var snPosition = new BasicGeoposition { Latitude = customPin.Pin.Position.Latitude, Longitude = customPin.Pin.Position.Longitude };
                var snPoint = new Geopoint(snPosition);

                nativeMap.Children.Add(mapOverlay);
                MapControl.SetLocation(mapOverlay, snPoint);
                MapControl.SetNormalizedAnchorPoint(mapOverlay, new Windows.Foundation.Point(0.5, 1.0));
                xamarinOverlayShown = true;
            }
        }
        else
        {
            nativeMap.Children.Remove(mapOverlay);
            xamarinOverlayShown = false;
        }
    }
}
```

Esse método cria uma instância `UserControl` que exibe informações sobre o marcador. Isso é feito da seguinte maneira:

- A instância `MapIcon` é recuperada.
- O método `GetCustomPin` é chamado para retornar os dados de marcador personalizados que serão exibidos.
- Uma instância `XamarinMapOverlay` é criada para exibir os dados de fixação personalizados. Essa classe é um controle de usuário.
- A localização geográfica na qual exibir a instância `XamarinMapOverlay` no `MapControl` é criada como uma instância `Geopoint`.
- A instância `XamarinMapOverlay` é adicionada à coleção `MapControl.Children`. Essa coleção contém elementos de interface do usuário XAML que serão exibidos no mapa.
- A localização geográfica da instância `XamarinMapOverlay` no mapa é definida chamando o método `SetLocation`.
- A localização relativa na instância `XamarinMapOverlay`, que corresponde à localização especificada, é definida chamando o método `SetNormalizedAnchorPoint`. Isso garante que as alterações no nível de aplicação de zoom do mapa resultem na exibição constante da instância `XamarinMapOverlay` na localização correta.

Como alternativa, se as informações sobre o marcador já estiverem sendo exibidas no mapa, o toque no mapa removerá a instância `XamarinMapOverlay` da coleção `MapControl.Children`.

#### <a name="tapping-on-the-information-button"></a>Tocando no botão de informações

Quando o usuário toca o botão *Informações* no controle de usuário `XamarinMapOverlay`, o evento `Tapped` é disparado, que, por sua vez, executa o método `OnInfoButtonTapped`:

```csharp
private async void OnInfoButtonTapped(object sender, TappedRoutedEventArgs e)
{
    await Launcher.LaunchUriAsync(new Uri(customPin.Url));
}
```

Esse método abre um navegador da Web e navega para o endereço armazenado na propriedade `Url` da instância `CustomPin`. Observe que o endereço foi definido durante a criação da coleção `CustomPin` no projeto da biblioteca do .NET Standard.

Para obter mais informações sobre como personalizar uma instância `MapControl`, confira [Visão geral de mapas e localização](https://msdn.microsoft.com/library/windows/apps/mt219699.aspx) no MSDN.

## <a name="related-links"></a>Links relacionados

- [Controle de mapas](~/xamarin-forms/user-interface/map.md)
- [Mapas do iOS](~/ios/user-interface/controls/ios-maps/index.md)
- [API de mapas](~/android/platform/maps-and-location/maps/maps-api.md)
- [Marcador personalizado (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-map-pin)
