---
title: Personalizando um Pin de mapa
description: "Este artigo demonstra como criar um renderizador personalizado para o controle de mapa que exibe um mapa nativo com um pin personalizado e uma exibição personalizada dos dados pin em cada plataforma."
ms.topic: article
ms.prod: xamarin
ms.assetid: C5481D86-80E9-4E3D-9FB6-57B0F93711A6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 312bda44a6b390c6ba486d5a3d60dfe4fb770a2e
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2018
---
# <a name="customizing-a-map-pin"></a>Personalizando um Pin de mapa

_Este artigo demonstra como criar um renderizador personalizado para o controle de mapa que exibe um mapa nativo com um pin personalizado e uma exibição personalizada dos dados pin em cada plataforma._

Cada exibição xamarin. Forms tem um processador que o acompanha para cada plataforma que cria uma instância de um controle nativo. Quando um [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) é processado por um aplicativo xamarin. Forms no iOS, o `MapRenderer` classe é instanciada, que por sua vez instancia um nativo `MKMapView` controle. Na plataforma Android, o `MapRenderer` classe instancia um nativo `MapView` controle. Sobre o Windows UWP (plataforma Universal), o `MapRenderer` classe instancia um nativo `MapControl`. Para obter mais informações sobre o processador e classes de controle nativo que mapeiam xamarin. Forms controles, consulte [Classes de Base de processador e controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

O diagrama a seguir ilustra o relacionamento entre o [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) e os controles nativo correspondentes que implementação-la:

![](customized-pin-images/map-classes.png "Relação entre o controle de mapa e implementar controles nativos")

O processo de renderização pode ser usado para implementar as personalizações específicas da plataforma, criando um renderizador personalizado para um [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) em cada plataforma. O processo para fazer isso é como segue:

1. [Criar](#Creating_the_Custom_Map) um mapa personalizado xamarin. Forms.
1. [Consumir](#Consuming_the_Custom_Map) o mapa personalizado do xamarin. Forms.
1. [Criar](#Creating_the_Custom_Renderer_on_each_Platform) renderizador personalizado para o mapa em cada plataforma.

Cada item agora será discutida por sua vez, para implementar um `CustomMap` processador que exibe um mapa nativo com um pin personalizado e uma exibição personalizada dos dados pin em cada plataforma.

> [!NOTE]
> [`Xamarin.Forms.Maps`](https://developer.xamarin.com/api/namespace/Xamarin.Forms.Maps/) deve ser inicializada e configurada antes do uso. Para obter mais informações, consulte [`Maps Control`](~/xamarin-forms/user-interface/map.md).

<a name="Creating_the_Custom_Map" />

## <a name="creating-the-custom-map"></a>Criando o mapa personalizado

Um controle de mapa personalizado pode ser criado, subclassificação o [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) classe, conforme mostrado no exemplo de código a seguir:

```csharp
public class CustomMap : Map
{
  public List<CustomPin> CustomPins { get; set; }
}
```

O `CustomMap` controle é criado no projeto de biblioteca (PCL) de classes portátil e define a API para o mapa personalizado. O mapa personalizado expõe o `CustomPins` propriedade que representa a coleção de `CustomPin` objetos que serão processados pelo controle de mapa nativo em cada plataforma. O `CustomPin` é mostrada no exemplo de código a seguir:

```csharp
public class CustomPin : Pin
{
  public string Url { get; set; }
}
```

Essa classe define um `CustomPin` como herança de propriedades do [ `Pin` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Pin/) classe e adicionar um `Url` propriedade.

<a name="Consuming_the_Custom_Map" />

## <a name="consuming-the-custom-map"></a>Consumindo o mapa personalizado

O `CustomMap` controle pode ser referenciado em XAML no projeto PCL declarando um namespace para seu local e usando o prefixo de namespace do controle de mapa personalizado. O seguinte exemplo de código mostra como o `CustomMap` controle pode ser consumido por uma página XAML:

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

O `local` o prefixo do namespace pode ser qualquer nome. No entanto, o `clr-namespace` e `assembly` valores devem coincidir com os detalhes do mapa personalizado. Depois que o namespace for declarado, o prefixo é usado para referenciar o mapa personalizado.

O seguinte exemplo de código mostra como o `CustomMap` controle pode ser consumido por uma página c#:

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

O `CustomMap` instância será usada para exibir o mapa nativo em cada plataforma. Ele tem [ `MapType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.MapType/) propriedade define o estilo de exibição da [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/), com os valores possíveis que está sendo definidos no [ `MapType` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.MapType/) enumeração. Para iOS e Android, a largura e altura do mapa é definido por meio de propriedades da `App` classe que são inicializadas nos projetos de plataforma específica.

O local do mapa e os pins que ele contém, são inicializados conforme mostrado no exemplo de código a seguir:

```csharp
public MapPage ()
{
  ...
  var pin = new CustomPin {
    Type = PinType.Place,
    Position = new Position (37.79752, -122.40183),
    Label = "Xamarin San Francisco Office",
    Address = "394 Pacific Ave, San Francisco CA",
    Id = "Xamarin",
    Url = "http://xamarin.com/about/"
  };

  customMap.CustomPins = new List<CustomPin> { pin };
  customMap.Pins.Add (pin);
  customMap.MoveToRegion (MapSpan.FromCenterAndRadius (
    new Position (37.79752, -122.40183), Distance.FromMiles (1.0)));
}
```

Essa inicialização adiciona um pino personalizados e posiciona o modo de exibição do mapa com o [ `MoveToRegion` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Map.MoveToRegion(Xamarin.Forms.Maps.MapSpan)/) método, o que altera a posição e o nível de zoom do mapa, criando um [ `MapSpan` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.MapSpan/) de um [ `Position` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Position/) e um [ `Distance` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Distance/).

Agora é possível adicionar um renderizador personalizado para cada projeto de aplicativo para personalizar os controles de mapa nativo.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Criando o renderizador personalizado em cada plataforma

O processo para criar a classe de renderizador personalizado é o seguinte:

1. Criar uma subclasse do `MapRenderer` classe que renderiza o mapa personalizado.
1. Substituir o `OnElementChanged` método que renderiza o mapa personalizado e a lógica de gravação para personalizá-lo. Esse método é chamado quando o mapa personalizado do xamarin. Forms correspondente é criado.
1. Adicionar um `ExportRenderer` de atributo para a classe de renderizador personalizado para especificar que ele será usado para renderizar o mapa personalizado xamarin. Forms. Este atributo é usado para registrar o renderizador personalizado com xamarin. Forms.

> [!NOTE]
> É opcional fornecer um renderizador personalizado em cada projeto da plataforma. Se um renderizador personalizado não estiver registrado, será usado o renderizador padrão para a classe base do controle.

O diagrama a seguir ilustra as responsabilidades de cada projeto de aplicativo de exemplo, juntamente com as relações entre eles:

![](customized-pin-images/solution-structure.png "Responsabilidades de projeto do renderizador CustomMap personalizado")

O `CustomMap` controle é processado por classes de processador específico da plataforma, que derivam de `MapRenderer` classe para cada plataforma. Isso resulta em cada `CustomMap` controle sendo processada com controles de plataforma específica, conforme mostrado nas capturas de tela seguir:

![](customized-pin-images/screenshots.png "CustomMap em cada plataforma")

O `MapRenderer` classe expõe o `OnElementChanged` método, que é chamado quando o mapa personalizado xamarin. Forms é criado para renderizar o controle nativo correspondente. Esse método usa um `ElementChangedEventArgs` parâmetro que contém `OldElement` e `NewElement` propriedades. Essas propriedades representam o elemento xamarin. Forms que o renderizador *foi* associada e o elemento xamarin. Forms que o renderizador *é* anexado, respectivamente. No aplicativo de amostra a `OldElement` propriedade será `null` e `NewElement` propriedade conterá uma referência para a `CustomMap` instância.

Uma versão de substituição de `OnElementChanged` método na classe cada renderizador específico da plataforma, é o local para executar a personalização do controle nativo. Uma referência de tipo para o controle nativo que está sendo usado na plataforma pode ser acessada por meio de `Control` propriedade. Além disso, uma referência para o controle de xamarin. Forms que está sendo renderizado pode ser obtida por meio de `Element` propriedade.

Tome cuidado ao assinar manipuladores de eventos de `OnElementChanged` método, conforme demonstrado no exemplo de código a seguir:

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<Xamarin.Forms.ListView> e)
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

O controle nativo deve ser configurado e manipuladores de eventos inscreveu somente quando o renderizador personalizado está anexado a um novo elemento xamarin. Forms. Da mesma forma, os manipuladores de evento que assinou devem ser cancelados somente quando o elemento que o processador está anexado a alterações. Adotar essa abordagem ajudará a criar um renderizador personalizado que não apresenta vazamentos de memória.

Cada classe de renderizador personalizado é decorado com um `ExportRenderer` atributo que registra o renderizador com xamarin. Forms. O atributo utiliza dois parâmetros – o nome do tipo do controle personalizado xamarin. Forms está sendo processado e o nome do tipo de renderizador personalizado. O `assembly` prefixo para o atributo especifica que o atributo se aplica ao assembly inteiro.

As seções a seguir discutem a implementação de cada classe de renderizador personalizado específico da plataforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Criando o renderizador personalizado no iOS

As capturas de tela a seguir mostram o mapa, antes e depois da personalização:

![](customized-pin-images/map-layout-ios.png "Controle de mapa antes e depois da personalização")

No iOS, o pin é chamado um *anotação*, e pode ser uma imagem personalizada ou um pin definido pelo sistema de várias cores. Anotações opcionalmente podem mostrar um *texto explicativo*, que é exibido na resposta para o usuário selecionar a anotação. Exibe o texto explicativo a `Label` e `Address` propriedades da `Pin` instância com exibições Acessórios à direita e esquerda opcional. Na captura de tela acima, a exibição de acessório esquerda é a imagem de um monkey, com a exibição à direita acessório sendo o *informações* botão.

O exemplo de código a seguir mostra o renderizador personalizado para a plataforma iOS:

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

O `OnElementChanged` método executa a seguinte configuração do [ `MKMapView` ](https://developer.xamarin.com/api/type/MapKit.MKMapView/) instância, desde que o renderizador personalizado está anexado a um novo elemento xamarin. Forms:

- O [ `GetViewForAnnotation` ](https://developer.xamarin.com/api/property/MapKit.MKMapView.GetViewForAnnotation/) está definida como o `GetViewForAnnotation` método. Este método é chamado quando o [local da anotação fica visível no mapa](#Displaying_the_Annotation)e é usado para personalizar o antes de anotação para exibir.
- Manipuladores de eventos para o `CalloutAccessoryControlTapped`, `DidSelectAnnotationView`, e `DidDeselectAnnotationView` eventos são registrados. Esses eventos acionam quando o usuário [toca o acessório à direita do texto explicativo](#Tapping_on_the_Right_Callout_Accessory_View)e quando o usuário [seleciona](#Selecting_the_Annotation) e [desmarca](#Deselecting_the_Annotation) a anotação, respectivamente. Os eventos são cancelados somente quando o elemento que o processador está anexado a alterações.

<a name="Displaying_the_Annotation" />

#### <a name="displaying-the-annotation"></a>Exibir a anotação

O `GetViewForAnnotation` método é chamado quando o local da anotação fica visível no mapa e é usado para personalizar o antes de anotação para exibir. Uma anotação tem duas partes:

- `MkAnnotation` – inclui o título, subtítulo e localização da anotação.
- `MkAnnotationView` – contém a imagem para representar a anotação e, opcionalmente, um texto explicativo que é mostrado quando o usuário toca a anotação.

O `GetViewForAnnotation` método aceita um `IMKAnnotation` que contém os dados da anotação e retorna um `MKAnnotationView` para exibição do mapa e é mostrado no exemplo de código a seguir:

```csharp
MKAnnotationView GetViewForAnnotation(MKMapView mapView, IMKAnnotation annotation)
{
    MKAnnotationView annotationView = null;

    if (annotation is MKUserLocation)
        return null;

    var customPin = GetCustomPin(annotation as MKPointAnnotation);
    if (customPin == null) {
        throw new Exception("Custom pin not found");
    }

    annotationView = mapView.DequeueReusableAnnotation(customPin.Id.ToString());
    if (annotationView == null) {
        annotationView = new CustomMKAnnotationView(annotation, customPin.Id.ToString());
        annotationView.Image = UIImage.FromFile("pin.png");
        annotationView.CalloutOffset = new CGPoint(0, 0);
        annotationView.LeftCalloutAccessoryView = new UIImageView(UIImage.FromFile("monkey.png"));
        annotationView.RightCalloutAccessoryView = UIButton.FromType(UIButtonType.DetailDisclosure);
        ((CustomMKAnnotationView)annotationView).Id = customPin.Id.ToString();
        ((CustomMKAnnotationView)annotationView).Url = customPin.Url;
    }
    annotationView.CanShowCallout = true;

    return annotationView;
}
```

Esse método garante que a anotação será exibida como uma imagem personalizada, em vez de pin definido pelo sistema e que quando a anotação é tocada um texto explicativo será exibido que inclui conteúdo adicional à esquerda e à direita do título da anotação e do endereço . Isso é feito da seguinte maneira:

1. O `GetCustomPin` método é chamado para retornar os dados de pin personalizado para a anotação.
1. Para conservar a memória, modo de exibição da anotação é colocado em pool para reutilização com a chamada para [ `DequeueReusableAnnotation` ](https://developer.xamarin.com/api/member/MapKit.MKMapView.DequeueReusableAnnotation/(System.String)/).
1. O `CustomMKAnnotationView` classe estende a `MKAnnotationView` classe com `Id` e `Url` propriedades que correspondem às propriedades idênticas no `CustomPin` instância. Uma nova instância do `CustomMKAnnotationView` é criado, desde que a anotação é `null`:
  - O `CustomMKAnnotationView.Image` estiver definida como a imagem que representa a anotação no mapa.
  - O `CustomMKAnnotationView.CalloutOffset` está definida como um `CGPoint` que especifica que o texto explicativo será centralizado acima a anotação.
  - O `CustomMKAnnotationView.LeftCalloutAccessoryView` propriedade é definida como uma imagem de um monkey que será exibido à esquerda do título da anotação e do endereço.
  - O `CustomMKAnnotationView.RightCalloutAccessoryView` está definida como um *informações* botão aparecerá à direita do título da anotação e do endereço.
  - O `CustomMKAnnotationView.Id` está definida como o `CustomPin.Id` propriedade retornada pelo `GetCustomPin` método. Isso permite que a anotação seja identificada de forma que ele tenha [texto explicativo pode ser personalizado](#Selecting_the_Annotation), se desejado.
  - O `CustomMKAnnotationView.Url` está definida como o `CustomPin.Url` propriedade retornada pelo `GetCustomPin` método. A URL será direcionada quando o usuário [tocar no botão exibido na exibição de acessório do texto explicativo direito](#Tapping_on_the_Right_Callout_Accessory_View).
1. O [ `MKAnnotationView.CanShowCallout` ](https://developer.xamarin.com/api/property/MapKit.MKAnnotationView.CanShowCallout/) está definida como `true` para que o texto explicativo é exibido quando a anotação é tocada.
1. A anotação é retornada para exibição do mapa.

<a name="Selecting_the_Annotation" />

#### <a name="selecting-the-annotation"></a>Selecionar a anotação

Quando o usuário toca na anotação, o `DidSelectAnnotationView` evento ser acionado, que por sua vez executa o `OnDidSelectAnnotationView` método:

```csharp
void OnDidSelectAnnotationView (object sender, MKAnnotationViewEventArgs e)
{
  var customView = e.View as CustomMKAnnotationView;
  customPinView = new UIView ();

  if (customView.Id == "Xamarin") {
    customPinView.Frame = new CGRect (0, 0, 200, 84);
    var image = new UIImageView (new CGRect (0, 0, 200, 84));
    image.Image = UIImage.FromFile ("xamarin.png");
    customPinView.AddSubview (image);
    customPinView.Center = new CGPoint (0, -(e.View.Frame.Height + 75));
    e.View.AddSubview (customPinView);
  }
}
```

Este método estende o texto explicativo existente (que contém exibições de esquerda e direita Acessórios) adicionando um `UIView` instância a ele que contém uma imagem do logotipo Xamarin, desde que a anotação selecionada tem seu `Id` propriedade definida como `Xamarin`. Isso possibilita cenários em que os textos explicativos diferentes podem ser exibidos para anotações diferentes. O `UIView` instância será exibida centralizado acima do texto explicativo existente.

<a name="Tapping_on_the_Right_Callout_Accessory_View" />

#### <a name="tapping-on-the-right-callout-accessory-view"></a>Tocar na exibição acessório à direita do texto explicativo

Quando o usuário toca no *informações* botão na exibição de texto explicativo direita acessório, o `CalloutAccessoryControlTapped` evento ser acionado, que por sua vez executa o `OnCalloutAccessoryControlTapped` método:

```csharp
void OnCalloutAccessoryControlTapped (object sender, MKMapViewAccessoryTappedEventArgs e)
{
  var customView = e.View as CustomMKAnnotationView;
  if (!string.IsNullOrWhiteSpace (customView.Url)) {
    UIApplication.SharedApplication.OpenUrl (new Foundation.NSUrl (customView.Url));
  }
}
```

Este método abre um navegador da web e navega para o endereço armazenado no `CustomMKAnnotationView.Url` propriedade. Observe que o endereço foi definido durante a criação de `CustomPin` coleção do projeto PCL.

<a name="Deselecting_the_Annotation" />

#### <a name="deselecting-the-annotation"></a>Cancelar a seleção de anotação

Quando a anotação é exibida e o usuário toca no mapa, o `DidDeselectAnnotationView` evento ser acionado, que por sua vez executa o `OnDidDeselectAnnotationView` método:

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

Esse método garante que, quando o texto explicativo existente não for selecionado, a parte estendida do texto explicativo (a imagem do logotipo Xamarin) também vai interromper o que está sendo exibido e seus recursos serão liberados.

Para obter mais informações sobre como personalizar um `MKMapView` da instância, consulte [iOS mapas](~/ios/user-interface/controls/ios-maps/index.md).

### <a name="creating-the-custom-renderer-on-android"></a>Criando o renderizador personalizado no Android

As capturas de tela a seguir mostram o mapa, antes e depois da personalização:

![](customized-pin-images/map-layout-android.png "Controle de mapa antes e depois da personalização")

No Android, o pin é chamado um *marcador*, e pode ser uma imagem personalizada ou um marcador definido pelo sistema de várias cores. Marcadores podem mostrar um *janela informações*, que é exibido quando o usuário tocar no marcador de. Exibe a janela de informações de `Label` e `Address` propriedades da `Pin` instância e pode ser personalizado para incluir outros tipos de conteúdo. No entanto, apenas uma janela de informações pode ser mostrada de uma vez.

O exemplo de código a seguir mostra o renderizador personalizado para a plataforma Android:

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

Desde que o renderizador personalizado está anexado a um novo elemento xamarin. Forms, o `OnElementChanged` chamadas de método a `MapView.GetMapAsync` método, que obtém subjacente `GoogleMap` que está vinculado à exibição. Uma vez o `GoogleMap` instância estiver disponível, o `OnMapReady` substituição será invocada. Este método registra um manipulador de eventos para o `InfoWindowClick` evento, que é acionado quando o [janela informações é clicada](#Clicking_on_the_Info_Window)e é não inscrito em somente quando o elemento que o processador está anexado a alterações. O `OnMapReady` substituir também chama o `SetInfoWindowAdapter` método para especificar que o `CustomMapRenderer` instância da classe fornece métodos para personalizar a janela de informações.

O `CustomMapRenderer` classe implementa o `GoogleMap.IInfoWindowAdapter` interface [personalizar a janela de informações](#Customizing_the_Info_Window). Essa interface Especifica que devem ser implementados os seguintes métodos:

- `public Android.Views.View GetInfoWindow(Marker marker)` – Esse método é chamado para retornar uma janela de informações personalizadas para um marcador. Se ele retornar `null`, a renderização de janela padrão será usado. Se ele retorna um `View`, em seguida, que `View` será colocado dentro do quadro de janela de informações.
- `public Android.Views.View GetInfoContents(Marker marker)` – Esse método é chamado para retornar um `View` que contém o conteúdo da janela informações e só será chamado se o `GetInfoWindow` método retornará `null`. Se ele retorna `null`, será usado o processamento padrão do conteúdo da janela de informações.

No aplicativo de exemplo, somente o conteúdo da janela de informações é personalizado e portanto o `GetInfoWindow` método retorna `null` para permitir isso.

#### <a name="customizing-the-marker"></a>Personalizando o marcador

O ícone usado para representar um marcador pode ser personalizado ao chamar o `MarkerOptions.SetIcon` método. Isso pode ser feito por meio da substituição de `CreateMarker` método, que é invocado para cada `Pin` que é adicionada ao mapa:

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

Esse método cria um novo `MarkerOption` instância para cada `Pin` instância. Depois de definir a posição, o rótulo e o endereço do marcador, o ícone é definido com o `SetIcon` método. Esse método usa um `BitmapDescriptor` objeto que contém os dados necessários para renderizar o ícone com o `BitmapDescriptorFactory` classe fornece métodos auxiliares para simplificar a criação da `BitmapDescriptor`.

Para obter mais informações sobre como usar o `BitmapDescriptorFactory` classe personalizar um marcador, consulte [Personalizando um marcador](~/android/platform/maps-and-location/maps/maps-api.md).

<a name="Customizing_the_Info_Window" />

#### <a name="customizing-the-info-window"></a>Personalizar a janela de informações

Quando um usuário toca no marcador, o `GetInfoContents` o método é executado, desde que o `GetInfoWindow` método retornará `null`. O seguinte exemplo de código mostra o `GetInfoContents` método:

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

    if (customPin.Id.ToString() == "Xamarin") {
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

Este método retorna um `View` que contém o conteúdo da janela de informações. Isso é feito da seguinte maneira:

- Um `LayoutInflater` instância é recuperada. Isso é usado para criar uma instância de um arquivo XML de layout em correspondente `View`.
- O `GetCustomPin` método é chamado para retornar os dados de pin personalizado para a janela de informações.
- O `XamarinMapInfoWindow` layout é inflado se o `CustomPin.Id` propriedade é igual a `Xamarin`. Caso contrário, o `MapInfoWindow` layout é aumentado. Isso possibilita cenários onde os layouts de janela de informações diferentes podem ser exibidos para diferentes marcadores.
- O `InfoWindowTitle` e `InfoWindowSubtitle` recursos são recuperados do layout inflated e seus `Text` propriedades são definidas para os dados correspondentes do `Marker` instância, desde que os recursos não sejam `null`.
- O `View` instância é retornada para exibição do mapa.

> [!NOTE]
> Uma janela de informações não é um live `View`. Em vez disso, o Android converterá o `View` para um estático de bitmap e exibir que como uma imagem. Isso significa que, durante uma janela de informações pode responder a um evento de clique, ele não pode responder a qualquer evento de toque ou gestos e os controles individuais na janela de informações não podem responder a seus próprios eventos de clique.

<a name="Clicking_on_the_Info_Window" />

#### <a name="clicking-on-the-info-window"></a>Clicar na janela de informações

Quando o usuário clica na janela de informações, o `InfoWindowClick` evento ser acionado, que por sua vez executa o `OnInfoWindowClick` método:

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

Este método abre um navegador da web e navega para o endereço armazenado no `Url` propriedade recuperada `CustomPin` instância para o `Marker`. Observe que o endereço foi definido durante a criação de `CustomPin` coleção do projeto PCL.

Para obter mais informações sobre como personalizar um `MapView` da instância, consulte [API Maps](~/android/platform/maps-and-location/maps/maps-api.md).

### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Criando o renderizador personalizado na plataforma Universal do Windows

As capturas de tela a seguir mostram o mapa, antes e depois da personalização:

![](customized-pin-images/map-layout-uwp.png "Controle de mapa antes e depois da personalização")

Em UWP o pin é chamado uma *ícone do mapa*, e pode ser uma imagem personalizada ou a imagem padrão definidas pelo sistema. Ícone de um mapa pode mostrar um `UserControl`, que é exibido quando o usuário tocando no ícone do mapa. O `UserControl` pode exibir qualquer conteúdo, incluindo o `Label` e `Address` propriedades da `Pin` instância.

O exemplo de código a seguir mostra o renderizador UWP personalizado:

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

O `OnElementChanged` método executa as seguintes operações, desde que o renderizador personalizado está anexado a um novo elemento xamarin. Forms:

- Ele limpa o `MapControl.Children` coleção para remover os elementos de interface de usuário existentes do mapa, antes de registrar um manipulador de eventos para o `MapElementClick` evento. Esse evento é acionado quando o usuário toca ou clicar em um `MapElement` no `MapControl`e é não inscrito em somente quando o elemento que o processador está anexado a alterações.
- Cada pin no `customPins` coleção é exibida na localização geográfica correta no mapa da seguinte maneira:
  - O local para o pin é criado como um `Geopoint` instância.
  - Um `MapIcon` instância é criada para representar o pin.
  - A imagem usada para representar o `MapIcon` é especificado pela configuração de `MapIcon.Image` propriedade. No entanto, imagem do ícone de mapa não é garantida sempre seja exibida, como ele pode ser encoberto por outros elementos no mapa. Do portanto, o ícone do mapa `CollisionBehaviorDesired` está definida como `MapElementCollisionBehavior.RemainVisible`, para garantir que ele permanece visível.
  - O local do `MapIcon` é especificado pela configuração de `MapIcon.Location` propriedade.
  - O `MapIcon.NormalizedAnchorPoint` propriedade é definida como a localização aproximada do ponteiro na imagem. Se essa propriedade retém seu valor padrão de (0,0), que representa o canto superior esquerdo da imagem, as alterações no nível de zoom do mapa podem resultar na imagem apontando para um local diferente.
  - O `MapIcon` instância é adicionada para o `MapControl.MapElements` coleção. Isso resulta no ícone de mapa que está sendo exibido no `MapControl`.

> [!NOTE]
> Ao usar a mesma imagem para vários ícones de mapa, o `RandomAccessStreamReference` instância deve ser declarada no nível do aplicativo ou página para melhor desempenho.

#### <a name="displaying-the-usercontrol"></a>Exibindo o UserControl

Quando um usuário toca no ícone do mapa, o `OnMapElementClick` o método é executado. O exemplo de código a seguir mostra esse método:

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

            if (customPin.Id.ToString() == "Xamarin")
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

Esse método cria um `UserControl` instância que exibe informações sobre o pin. Isso é feito da seguinte maneira:

- O `MapIcon` instância é recuperada.
- O `GetCustomPin` método é chamado para retornar os dados de pin personalizado que serão exibidos.
- Um `XamarinMapOverlay` instância é criada para exibir os dados de pino personalizados. Essa classe é um controle de usuário.
- A localização geográfica na qual exibir o `XamarinMapOverlay` de instância no `MapControl` é criado como um `Geopoint` instância.
- O `XamarinMapOverlay` instância é adicionada para o `MapControl.Children` coleção. Esta coleção contém elementos de interface de usuário XAML que serão exibidos no mapa.
- A localização geográfica do `XamarinMapOverlay` instância no mapa é definida ao chamar o `SetLocation` método.
- O local relativo no `XamarinMapOverlay` instância, que corresponde ao local especificado, é definida ao chamar o `SetNormalizedAnchorPoint` método. Isso garante que as alterações no nível de zoom do resultado no mapa de `XamarinMapOverlay` sempre sendo exibida no local correto da instância.

Como alternativa, se informações sobre o pin já está sendo exibidas no mapa, toque no mapa remove o `XamarinMapOverlay` da instância do `MapControl.Children` coleção.

#### <a name="tapping-on-the-information-button"></a>Toque no botão de informações

Quando o usuário toca no *informações* no botão de `XamarinMapOverlay` controle de usuário, o `Tapped` evento ser acionado, que por sua vez executa o `OnInfoButtonTapped` método:

```csharp
private async void OnInfoButtonTapped(object sender, TappedRoutedEventArgs e)
{
    await Launcher.LaunchUriAsync(new Uri(customPin.Url));
}
```

Este método abre um navegador da web e navega para o endereço armazenado no `Url` propriedade o `CustomPin` instância. Observe que o endereço foi definido durante a criação de `CustomPin` coleção do projeto PCL.

Para obter mais informações sobre como personalizar um `MapControl` da instância, consulte [mapas e visão geral do local](https://msdn.microsoft.com/library/windows/apps/mt219699.aspx) no MSDN.

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar um renderizador personalizado para o `Map` controle, permitindo que os desenvolvedores substituir a renderização nativo padrão com sua próprias personalização específica de plataforma. Xamarin.Forms.Maps fornece uma abstração de plataforma cruzada para exibir mapas que usam o mapa nativo experiência de APIs em cada plataforma para fornecer um mapa rápido e familiar para os usuários.


## <a name="related-links"></a>Links relacionados

- [Controle de mapas](~/xamarin-forms/user-interface/map.md)
- [iOS Maps](~/ios/user-interface/controls/ios-maps/index.md)
- [API de mapas](~/android/platform/maps-and-location/maps/maps-api.md)
- [Pin personalizado (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/pin/)
