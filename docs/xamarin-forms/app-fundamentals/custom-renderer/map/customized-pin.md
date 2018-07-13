---
title: Personalizando um Pin de mapa
description: Este artigo demonstra como criar um renderizador personalizado para o controle de mapa, que exibe um mapa nativo com um pin personalizado e uma exibição personalizada dos dados pin em cada plataforma.
ms.prod: xamarin
ms.assetid: C5481D86-80E9-4E3D-9FB6-57B0F93711A6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 4fee67f08e86c40709aa226c40c0f7721dc26800
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998281"
---
# <a name="customizing-a-map-pin"></a>Personalizando um Pin de mapa

_Este artigo demonstra como criar um renderizador personalizado para o controle de mapa, que exibe um mapa nativo com um pin personalizado e uma exibição personalizada dos dados pin em cada plataforma._

Cada modo de exibição do xamarin. Forms tem um renderizador que acompanha este artigo para cada plataforma que cria uma instância de um controle nativo. Quando um [ `Map` ](xref:Xamarin.Forms.Maps.Map) é processado por um aplicativo xamarin. Forms no iOS, o `MapRenderer` classe é instanciada, que por sua vez cria uma instância de um nativo `MKMapView` controle. Na plataforma Android, o `MapRenderer` classe instancia um nativo `MapView` controle. Na Universal Windows Platform (UWP), o `MapRenderer` classe instancia um nativo `MapControl`. Para obter mais informações sobre as classes de controle nativo que mapeiam controles xamarin. Forms e o renderizador, consulte [Classes de Base do renderizador e controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

O diagrama a seguir ilustra o relacionamento entre o [ `Map` ](xref:Xamarin.Forms.Maps.Map) e controles nativos correspondentes que implementação-lo:

![](customized-pin-images/map-classes.png "Relação entre o controle de mapa e os implementação controles nativos")

O processo de renderização pode ser usado para implementar personalizações específicas de plataforma, criando um renderizador personalizado para um [ `Map` ](xref:Xamarin.Forms.Maps.Map) em cada plataforma. O processo para fazer isso é o seguinte:

1. [Criar](#Creating_the_Custom_Map) um mapa personalizado do xamarin. Forms.
1. [Consumir](#Consuming_the_Custom_Map) o mapa personalizado do xamarin. Forms.
1. [Criar](#Creating_the_Custom_Renderer_on_each_Platform) o renderizador personalizado para o mapa em cada plataforma.

Cada item agora será discutida por sua vez, para implementar um `CustomMap` renderizador que exibe um mapa nativo com um pin personalizado e uma exibição personalizada dos dados pin em cada plataforma.

> [!NOTE]
> [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) deve ser inicializado e configurado antes do uso. Para obter mais informações, consulte [`Maps Control`](~/xamarin-forms/user-interface/map.md).

<a name="Creating_the_Custom_Map" />

## <a name="creating-the-custom-map"></a>Criando o mapa personalizado

Um controle de mapa personalizado pode ser criado pela criação de subclasses a [ `Map` ](xref:Xamarin.Forms.Maps.Map) de classe, conforme mostrado no exemplo de código a seguir:

```csharp
public class CustomMap : Map
{
  public List<CustomPin> CustomPins { get; set; }
}
```

O `CustomMap` controle é criado no projeto da biblioteca .NET Standard e define a API para o mapa personalizado. O mapa personalizado expõe o `CustomPins` propriedade que representa a coleção de `CustomPin` objetos que serão processados pelo controle de mapa nativa em cada plataforma. O `CustomPin` classe é mostrada no exemplo de código a seguir:

```csharp
public class CustomPin : Pin
{
  public string Url { get; set; }
}
```

Essa classe define um `CustomPin` quanto herdar as propriedades do [ `Pin` ](xref:Xamarin.Forms.Maps.Pin) classe e adicionando um `Url` propriedade.

<a name="Consuming_the_Custom_Map" />

## <a name="consuming-the-custom-map"></a>Consumindo o mapa personalizado

O `CustomMap` controle pode ser referenciada em XAML no projeto da biblioteca .NET Standard, declarando um namespace para seu local e usando o prefixo de namespace no controle de mapa personalizado. O seguinte exemplo de código mostra como o `CustomMap` controle pode ser consumido por uma página XAML:

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

O `local` prefixo de namespace pode ser qualquer nome. No entanto, o `clr-namespace` e `assembly` valores devem corresponder os detalhes do mapa personalizado. Depois que o namespace é declarado, o prefixo é usado para referenciar o mapa personalizado.

O seguinte exemplo de código mostra como o `CustomMap` controle pode ser consumido por uma página do c#:

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

O `CustomMap` instância será usada para exibir o mapa nativo em cada plataforma. Ele tem [ `MapType` ](xref:Xamarin.Forms.Maps.Map.MapType) propriedade define o estilo de exibição dos [ `Map` ](xref:Xamarin.Forms.Maps.Map), com os valores possíveis que está sendo definidos no [ `MapType` ](xref:Xamarin.Forms.Maps.MapType) enumeração. Para iOS e Android, a largura e altura do mapa é definido por meio das propriedades do `App` classe são inicializados nos projetos específicos da plataforma.

O local do mapa e os pinos que ele contém, são inicializados conforme mostrado no exemplo de código a seguir:

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

Essa inicialização adiciona um pino personalizados e posiciona a exibição do mapa com o [ `MoveToRegion` ](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) método, que altera a posição e o nível de zoom do mapa com a criação de uma [ `MapSpan` ](xref:Xamarin.Forms.Maps.MapSpan) de um [ `Position` ](xref:Xamarin.Forms.Maps.Position) e uma [ `Distance` ](xref:Xamarin.Forms.Maps.Distance).

Agora pode ser adicionado a um renderizador personalizado para cada projeto de aplicativo para personalizar os controles de mapa nativo.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Criando o renderizador personalizado em cada plataforma

O processo para criar a classe de renderizador personalizado é da seguinte maneira:

1. Criar uma subclasse do `MapRenderer` classe que renderiza o mapa personalizado.
1. Substituir o `OnElementChanged` método que renderiza o mapa personalizado e a lógica de gravação para personalizá-lo. Esse método é chamado quando o mapa personalizado do xamarin. Forms correspondente é criado.
1. Adicionar um `ExportRenderer` atributo à classe de renderizador personalizado para especificar que será usada para renderizar o mapa personalizado do xamarin. Forms. Este atributo é usado para registrar o renderizador personalizado com o xamarin. Forms.

> [!NOTE]
> É opcional fornecer um renderizador personalizado em cada projeto de plataforma. Se um renderizador personalizado não estiver registrado, o renderizador padrão para a classe base do controle será usado.

O diagrama a seguir ilustra as responsabilidades de cada projeto no aplicativo de exemplo, juntamente com as relações entre eles:

![](customized-pin-images/solution-structure.png "Responsabilidades do projeto de renderizador personalizado CustomMap")

O `CustomMap` controle é processado por classes de renderizador específica da plataforma, que derivam de `MapRenderer` classe para cada plataforma. Isso resulta em cada `CustomMap` de controle que está sendo renderizado com controles específicos da plataforma, conforme mostrado nas capturas de tela seguir:

![](customized-pin-images/screenshots.png "CustomMap em cada plataforma")

O `MapRenderer` classe expõe o `OnElementChanged` método, que é chamado quando o mapa personalizado do xamarin. Forms é criado para renderizar o controle nativo correspondente. Esse método usa um `ElementChangedEventArgs` parâmetro que contém `OldElement` e `NewElement` propriedades. Essas propriedades representam o elemento do xamarin. Forms que o renderizador *foi* associada e o elemento do xamarin. Forms que o renderizador *é* anexado, respectivamente. No aplicativo de exemplo do `OldElement` propriedade será `null` e o `NewElement` propriedade conterá uma referência para o `CustomMap` instância.

Uma versão de substituição a `OnElementChanged` método em cada classe de renderizador específica da plataforma é o lugar para realizar a personalização de controle nativo. Uma referência tipada para o controle nativo que está sendo usado na plataforma pode ser acessada por meio de `Control` propriedade. Além disso, uma referência para o controle de xamarin. Forms que está sendo renderizado pode ser obtida por meio de `Element` propriedade.

Tome cuidado ao assinar manipuladores de eventos no `OnElementChanged` método, conforme demonstrado no exemplo de código a seguir:

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

O controle nativo deve ser configurado e manipuladores de eventos inscritos apenas quando o renderizador personalizado é anexado a um novo elemento xamarin. Forms. Da mesma forma, os manipuladores de evento inscritos devem ser cancelados somente quando o elemento que o renderizador está anexado a muda. Adotar essa abordagem ajudará a criar um renderizador personalizado que não sofra perdas de memória.

Cada classe de renderizador personalizado é decorado com um `ExportRenderer` atributo que registra o renderizador com xamarin. Forms. O atributo utiliza dois parâmetros – o nome do tipo do controle personalizado xamarin. Forms que está sendo renderizado e o nome do tipo de renderizador personalizado. O `assembly` prefixo para o atributo especifica que o atributo se aplica a todo o assembly.

As seções a seguir discutem a implementação de cada classe de renderizador personalizado específica da plataforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Criando o renderizador personalizado no iOS

Capturas de tela as seguir mostram o mapa, antes e após a personalização:

![](customized-pin-images/map-layout-ios.png "Controle de mapa antes e após a personalização")

No iOS, o pin é chamado um *anotação*, e pode ser uma imagem personalizada ou um pin definido pelo sistema de várias cores. Anotações opcionalmente podem mostrar uma *texto explicativo*, que é exibido em resposta ao usuário selecionar a anotação. Exibe o texto explicativo a `Label` e `Address` propriedades do `Pin` instância, com exibições Acessórios à direita e esquerda opcional. Na captura de tela acima, o modo de exibição de acessório à esquerda é a imagem de um monkey, com o modo de exibição à direita acessório sendo o *informações* botão.

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

O `OnElementChanged` método executa a seguinte configuração das [ `MKMapView` ](https://developer.xamarin.com/api/type/MapKit.MKMapView/) da instância, desde que o renderizador personalizado está anexado a um novo elemento xamarin. Forms:

- O [ `GetViewForAnnotation` ](https://developer.xamarin.com/api/property/MapKit.MKMapView.GetViewForAnnotation/) estiver definida como o `GetViewForAnnotation` método. Esse método é chamado quando o [local da anotação se torna visível no mapa](#Displaying_the_Annotation)e é usado para personalizar o antes de anotação para exibir.
- Manipuladores de eventos para o `CalloutAccessoryControlTapped`, `DidSelectAnnotationView`, e `DidDeselectAnnotationView` os eventos são registrados. Esses eventos são acionados quando o usuário [toca o acessório à direita do texto explicativo](#Tapping_on_the_Right_Callout_Accessory_View)e quando o usuário [seleciona](#Selecting_the_Annotation) e [desmarca](#Deselecting_the_Annotation) a anotação, respectivamente. Os eventos são cancelados somente quando o elemento o renderizador está anexado for alterado.

<a name="Displaying_the_Annotation" />

#### <a name="displaying-the-annotation"></a>Exibindo a anotação

O `GetViewForAnnotation` método é chamado quando o local da anotação se torna visível no mapa e é usado para personalizar o antes de anotação para exibir. Uma anotação tem duas partes:

- `MkAnnotation` – inclui o título, subtítulo e local da anotação.
- `MkAnnotationView` – contém a imagem para representar a anotação e, opcionalmente, um texto explicativo que é mostrado quando o usuário toca a anotação.

O `GetViewForAnnotation` método aceita um `IMKAnnotation` que contém os dados da anotação e retorna um `MKAnnotationView` para exibição no mapa e é mostrado no exemplo de código a seguir:

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

Esse método garante que a anotação será exibida como uma imagem personalizada, em vez de como pin definido pelo sistema e que quando a anotação é tocada um texto explicativo será exibido que inclui conteúdo adicional à esquerda e à direita do título da anotação e do endereço . Isso é feito da seguinte maneira:

1. O `GetCustomPin` método é chamado para retornar os dados personalizados de pin da anotação.
1. Para conservar a memória, modo de exibição da anotação é colocado em pool para reutilização com a chamada para [ `DequeueReusableAnnotation` ](https://developer.xamarin.com/api/member/MapKit.MKMapView.DequeueReusableAnnotation/(System.String)/).
1. O `CustomMKAnnotationView` classe estende a `MKAnnotationView` classe com `Id` e `Url` as propriedades que correspondem às propriedades idênticas no `CustomPin` instância. Uma nova instância dos `CustomMKAnnotationView` é criada, desde que a anotação é `null`:
  - O `CustomMKAnnotationView.Image` estiver definida como a imagem que representará a anotação no mapa.
  - O `CustomMKAnnotationView.CalloutOffset` estiver definida como um `CGPoint` que especifica que o texto explicativo será centralizado acima a anotação.
  - O `CustomMKAnnotationView.LeftCalloutAccessoryView` estiver definida como uma imagem de um monkey que será exibido à esquerda do título da anotação e do endereço.
  - O `CustomMKAnnotationView.RightCalloutAccessoryView` estiver definida como uma *informações* botão aparecerá à direita do título da anotação e do endereço.
  - O `CustomMKAnnotationView.Id` estiver definida como o `CustomPin.Id` propriedade retornada pelo `GetCustomPin` método. Isso permite que a anotação a ser identificado para que ele tenha [balão pode ser personalizado](#Selecting_the_Annotation), se desejado.
  - O `CustomMKAnnotationView.Url` estiver definida como o `CustomPin.Url` propriedade retornada pelo `GetCustomPin` método. A URL será direcionada quando o usuário [toca no botão exibido na exibição de acessório texto explicativo certa](#Tapping_on_the_Right_Callout_Accessory_View).
1. O [ `MKAnnotationView.CanShowCallout` ](https://developer.xamarin.com/api/property/MapKit.MKAnnotationView.CanShowCallout/) estiver definida como `true` para que o texto explicativo seja exibido quando a anotação é tocada.
1. A anotação é retornada para a exibição do mapa.

<a name="Selecting_the_Annotation" />

#### <a name="selecting-the-annotation"></a>Selecionar a anotação

Quando o usuário toca na anotação, o `DidSelectAnnotationView` evento é acionado, por sua vez executa o `OnDidSelectAnnotationView` método:

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

Este método estende o texto explicativo existente (que contém as exibições de esquerda e direita Acessórios), adicionando um `UIView` instância a ele que contém uma imagem do logotipo do Xamarin, desde que a anotação selecionada tem seu `Id` propriedade definida como `Xamarin`. Isso permite cenários em que os textos explicativos diferentes podem ser exibidos para anotações diferentes. O `UIView` instância será exibida centralizado acima do texto explicativo existente.

<a name="Tapping_on_the_Right_Callout_Accessory_View" />

#### <a name="tapping-on-the-right-callout-accessory-view"></a>Tocar na exibição de acessório à direita do texto explicativo

Quando o usuário toca na *informações* botão na exibição de acessório texto explicativo à direita, o `CalloutAccessoryControlTapped` evento é acionado, por sua vez executa o `OnCalloutAccessoryControlTapped` método:

```csharp
void OnCalloutAccessoryControlTapped (object sender, MKMapViewAccessoryTappedEventArgs e)
{
  var customView = e.View as CustomMKAnnotationView;
  if (!string.IsNullOrWhiteSpace (customView.Url)) {
    UIApplication.SharedApplication.OpenUrl (new Foundation.NSUrl (customView.Url));
  }
}
```

Este método abre um navegador da web e navega para o endereço armazenado no `CustomMKAnnotationView.Url` propriedade. Observe que o endereço foi definido durante a criação de `CustomPin` coleta no projeto da biblioteca .NET Standard.

<a name="Deselecting_the_Annotation" />

#### <a name="deselecting-the-annotation"></a>Cancelando a seleção de anotação

Quando a anotação é exibida e o usuário toca no mapa, o `DidDeselectAnnotationView` evento é acionado, por sua vez executa o `OnDidDeselectAnnotationView` método:

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

Esse método garante que, quando o texto explicativo do existente não estiver selecionado, a parte estendida do texto explicativo (a imagem do logotipo do Xamarin) também vai interromper o que está sendo exibida e seus recursos serão liberados.

Para obter mais informações sobre como personalizar uma `MKMapView` da instância, consulte [mapas do iOS](~/ios/user-interface/controls/ios-maps/index.md).

### <a name="creating-the-custom-renderer-on-android"></a>Criando o renderizador personalizado no Android

Capturas de tela as seguir mostram o mapa, antes e após a personalização:

![](customized-pin-images/map-layout-android.png "Controle de mapa antes e após a personalização")

No Android o pin é chamado um *marcador*, e pode ser uma imagem personalizada ou um marcador definida pelo sistema de várias cores. Marcadores podem mostrar um *janela informações*, que é exibido na resposta para o usuário tocar no marcador. A janela de informações exibe a `Label` e `Address` propriedades do `Pin` da instância e pode ser personalizado para incluir outros tipos de conteúdo. No entanto, apenas uma janela de informações pode ser mostrada ao mesmo tempo.

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

Desde que o renderizador personalizado está anexado a um novo elemento xamarin. Forms, o `OnElementChanged` chamadas de método de `MapView.GetMapAsync` método, que obtém subjacente `GoogleMap` que está vinculada à exibição. Uma vez a `GoogleMap` instância está disponível, o `OnMapReady` substituição será invocada. Esse método registra um manipulador de eventos para o `InfoWindowClick` evento, que é acionado quando o [janela informações é clicada](#Clicking_on_the_Info_Window)e é cancelado a assinatura de somente quando o elemento o renderizador está anexado for alterado. O `OnMapReady` também substitui as chamadas a `SetInfoWindowAdapter` método para especificar que o `CustomMapRenderer` instância da classe fornecerá os métodos para personalizar a janela de informações.

O `CustomMapRenderer` classe implementa o `GoogleMap.IInfoWindowAdapter` interface [personalizar a janela de informações](#Customizing_the_Info_Window). Essa interface Especifica que os métodos a seguir devem ser implementados:

- `public Android.Views.View GetInfoWindow(Marker marker)` – Esse método é chamado para retornar uma janela de informações personalizadas para um marcador. Se ele retornar `null`, então a renderização de janela padrão será usada. Se ele retornar um `View`, em seguida, que `View` serão colocados dentro do quadro de janela de informações.
- `public Android.Views.View GetInfoContents(Marker marker)` – Esse método é chamado para retornar um `View` que contém o conteúdo da janela informações e só será chamado se o `GetInfoWindow` retorno do método `null`. Se ele retornar `null`, então será usado o processamento padrão, o conteúdo da janela de informações.

No aplicativo de exemplo, somente o conteúdo da janela de informações é personalizado e então o `GetInfoWindow` retorno do método `null` para habilitar isso.

#### <a name="customizing-the-marker"></a>Personalizando o marcador

O ícone usado para representar um marcador pode ser personalizado por meio da chamada a `MarkerOptions.SetIcon` método. Isso pode ser feito por meio da substituição de `CreateMarker` método, que é invocado para cada `Pin` que é adicionado ao mapa:

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

Esse método cria uma nova `MarkerOption` instância para cada `Pin` instância. Depois de definir a posição, o rótulo e o endereço do marcador, seu ícone é definido com o `SetIcon` método. Esse método usa um `BitmapDescriptor` objeto que contém os dados necessários para renderizar o ícone com o `BitmapDescriptorFactory` classe fornece métodos auxiliares para simplificar a criação do `BitmapDescriptor`.

Para obter mais informações sobre como usar o `BitmapDescriptorFactory` classe personalizar um marcador, consulte [Personalizando um marcador](~/android/platform/maps-and-location/maps/maps-api.md).

<a name="Customizing_the_Info_Window" />

#### <a name="customizing-the-info-window"></a>Personalizando a janela de informações

Quando um usuário toca no marcador, o `GetInfoContents` método é executado, contanto que o `GetInfoWindow` retorno do método `null`. O seguinte exemplo de código mostra o `GetInfoContents` método:

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

Esse método retorna um `View` que contém o conteúdo da janela de informações. Isso é feito da seguinte maneira:

- Um `LayoutInflater` instância é recuperada. Isso é usado para criar uma instância de um arquivo XML de layout em correspondente `View`.
- O `GetCustomPin` método é chamado para retornar os dados de pin personalizado para a janela de informações.
- O `XamarinMapInfoWindow` layout está inflado se o `CustomPin.Id` propriedade é igual a `Xamarin`. Caso contrário, o `MapInfoWindow` layout está inflado. Isso permite cenários onde os layouts de janela diferentes de informações podem ser exibidos para diferentes marcadores.
- O `InfoWindowTitle` e `InfoWindowSubtitle` os recursos são recuperados do layout inflado e seus `Text` são definidas para os dados correspondentes do `Marker` da instância, desde que os recursos não sejam `null`.
- O `View` instância é retornada para exibição do mapa.

> [!NOTE]
> Uma janela de informações não é um live `View`. Em vez disso, o Android converterá o `View` a um estático de bitmap e os exiba como uma imagem. Isso significa que, durante uma janela de informações pode responder a um evento de clique, ele não pode responder a quaisquer eventos de toque ou gestos e os controles individuais na janela de informações não podem responder a seus próprios eventos de clique.

<a name="Clicking_on_the_Info_Window" />

#### <a name="clicking-on-the-info-window"></a>Clicar na janela de informações

Quando o usuário clica na janela de informações, o `InfoWindowClick` evento é acionado, por sua vez executa o `OnInfoWindowClick` método:

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

Este método abre um navegador da web e navega para o endereço armazenado na `Url` propriedade recuperada `CustomPin` da instância para o `Marker`. Observe que o endereço foi definido durante a criação de `CustomPin` coleta no projeto da biblioteca .NET Standard.

Para obter mais informações sobre como personalizar uma `MapView` da instância, consulte [API de mapas](~/android/platform/maps-and-location/maps/maps-api.md).

### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Criando o renderizador personalizado na plataforma Windows Universal

Capturas de tela as seguir mostram o mapa, antes e após a personalização:

![](customized-pin-images/map-layout-uwp.png "Controle de mapa antes e após a personalização")

Na UWP, o pin é chamado um *ícone do mapa*, e pode ser uma imagem personalizada ou a imagem padrão definida pelo sistema. Ícone de um mapa pode mostrar um `UserControl`, que é exibido em resposta ao usuário tocando no ícone do mapa. O `UserControl` pode exibir qualquer conteúdo, incluindo o `Label` e `Address` propriedades do `Pin` instância.

O exemplo de código a seguir mostra o renderizador personalizado de UWP:

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

- Ele limpa o `MapControl.Children` coleção para remover os elementos de interface de usuário existentes do mapa, antes de registrar um manipulador de eventos para o `MapElementClick` eventos. Esse evento é acionado quando o usuário toca ou clica em um `MapElement` sobre o `MapControl`e é cancelado a assinatura de somente quando o elemento o renderizador está anexado for alterado.
- Cada pino do `customPins` coleção é exibida no local geográfico correto no mapa da seguinte maneira:
  - O local para o pin é criado como um `Geopoint` instância.
  - Um `MapIcon` instância é criada para representar o pin.
  - A imagem usada para representar o `MapIcon` seja especificado definindo a `MapIcon.Image` propriedade. No entanto, imagem do ícone do mapa não é garantida sempre deve ser mostrado como ele pode ser obscurecido por outros elementos no mapa. Portanto, mapa do ícone `CollisionBehaviorDesired` estiver definida como `MapElementCollisionBehavior.RemainVisible`, para garantir que ele permaneça visível.
  - O local do `MapIcon` seja especificado definindo a `MapIcon.Location` propriedade.
  - O `MapIcon.NormalizedAnchorPoint` estiver definida como a localização aproximada do ponteiro na imagem. Se essa propriedade retém seu valor padrão de (0,0), que representa o canto superior esquerdo da imagem, as alterações no nível de zoom do mapa podem resultar na imagem apontando para um local diferente.
  - O `MapIcon` instância é adicionada ao `MapControl.MapElements` coleção. Isso resulta no ícone do mapa que está sendo exibido no `MapControl`.

> [!NOTE]
> Ao usar a mesma imagem para vários ícones de mapa, o `RandomAccessStreamReference` instância deve ser declarada no nível de página ou do aplicativo para melhor desempenho.

#### <a name="displaying-the-usercontrol"></a>Exibindo o UserControl

Quando um usuário tocar no ícone do mapa, o `OnMapElementClick` método é executado. O exemplo de código a seguir mostra esse método:

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
- Um `XamarinMapOverlay` instância é criada para exibir os dados personalizados de pin. Essa classe é um controle de usuário.
- A localização geográfica na qual exibir o `XamarinMapOverlay` de instância em de `MapControl` é criado como um `Geopoint` instância.
- O `XamarinMapOverlay` instância é adicionada ao `MapControl.Children` coleção. Esta coleção contém elementos de interface de usuário XAML que serão exibidos no mapa.
- A localização geográfica do `XamarinMapOverlay` instância no mapa é definida chamando o `SetLocation` método.
- O local relativo a `XamarinMapOverlay` instância, que corresponde ao local especificado, é definida chamando o `SetNormalizedAnchorPoint` método. Isso garante que as alterações no nível de zoom do resultado no mapa de `XamarinMapOverlay` sempre que está sendo exibido no local correto da instância.

Como alternativa, se as informações sobre o pin já está sendo exibidas no mapa, tocar no mapa removerá o `XamarinMapOverlay` da instância da `MapControl.Children` coleção.

#### <a name="tapping-on-the-information-button"></a>Tocar no botão de informações

Quando o usuário toca na *informações* botão na `XamarinMapOverlay` controle de usuário, o `Tapped` evento é acionado, por sua vez executa o `OnInfoButtonTapped` método:

```csharp
private async void OnInfoButtonTapped(object sender, TappedRoutedEventArgs e)
{
    await Launcher.LaunchUriAsync(new Uri(customPin.Url));
}
```

Este método abre um navegador da web e navega para o endereço armazenado na `Url` propriedade do `CustomPin` instância. Observe que o endereço foi definido durante a criação de `CustomPin` coleta no projeto da biblioteca .NET Standard.

Para obter mais informações sobre como personalizar uma `MapControl` da instância, consulte [mapas e visão geral de localização](https://msdn.microsoft.com/library/windows/apps/mt219699.aspx) no MSDN.

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar um renderizador personalizado para o `Map` controle, permitindo que os desenvolvedores substituam a renderização de nativa padrão com sua próprias personalização específicas da plataforma. Xamarin.Forms.Maps fornece uma abstração de plataforma cruzada para a exibição de mapas que usam o mapa nativo a experiência de APIs em cada plataforma para fornecer um mapa rápido e familiar para os usuários.


## <a name="related-links"></a>Links relacionados

- [Controle de mapas](~/xamarin-forms/user-interface/map.md)
- [Mapas do iOS](~/ios/user-interface/controls/ios-maps/index.md)
- [API de mapas](~/android/platform/maps-and-location/maps/maps-api.md)
- [Pin personalizado (amostra)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/pin/)
