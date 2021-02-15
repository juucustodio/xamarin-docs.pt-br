---
title: Personalizando um marcador de mapa
description: Este artigo demonstra como criar um renderizador personalizado para o Controle de Mapeamento, que exibe um mapa nativo com um marcador personalizado e uma exibição personalizada dos dados de marcador em cada plataforma.
ms.prod: xamarin
ms.assetid: C5481D86-80E9-4E3D-9FB6-57B0F93711A6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/06/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 81a273091d14f8da908d1d52751f262b3a00027c
ms.sourcegitcommit: 63029dd7ea4edb707a53ea936ddbee684a926204
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98609060"
---
# <a name="customizing-a-map-pin"></a>Personalizando um marcador de mapa

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/customrenderers-map-pin)

_Este artigo demonstra como criar um renderizador personalizado para o Controle de Mapeamento, que exibe um mapa nativo com um marcador personalizado e uma exibição personalizada dos dados de marcador em cada plataforma._

Cada Xamarin.Forms exibição tem um renderizador que acompanha para cada plataforma que cria uma instância de um controle nativo. Quando um [`Map`](xref:Xamarin.Forms.Maps.Map) é renderizado por um Xamarin.Forms aplicativo no Ios, a `MapRenderer` classe é instanciada, que por sua vez instancia um `MKMapView` controle nativo. Na plataforma Android, a classe `MapRenderer` cria uma instância de um controle `MapView` nativo. Na UWP (Plataforma Universal do Windows), a classe `MapRenderer` cria uma instância de um `MapControl` nativo. Para obter mais informações sobre o renderizador e as classes de controle nativo que Xamarin.Forms controlam o mapa para o, consulte [classes base do processador e controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

O diagrama a seguir ilustra a relação entre o [`Map`](xref:Xamarin.Forms.Maps.Map) e os controles nativos correspondentes que o implementam:

![Relação entre o Controle de Mapeamento e a implementação de controles nativos](map-pin-images/map-classes.png)

O processo de renderização pode ser usado para implementar personalizações específicas da plataforma criando um renderizador personalizado para um [`Map`](xref:Xamarin.Forms.Maps.Map) em cada plataforma. O processo para fazer isso é o seguinte:

1. [Crie](#creating-the-custom-map) um Xamarin.Forms mapa personalizado.
1. [Consuma](#consuming-the-custom-map) o mapa personalizado de Xamarin.Forms .
1. [Criar](#creating-the-custom-renderer-on-each-platform) o renderizador personalizado para o mapa em cada plataforma.

Cada item agora será abordado por vez, para implementar um renderizador `CustomMap` que exibe um mapa nativo com um marcador personalizado e uma exibição personalizada dos dados do marcador em cada plataforma.

> [!NOTE]
> [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) deve ser inicializado e configurado antes do uso. Para obter mais informações, confira [`Maps Control`](~/xamarin-forms/user-interface/map/index.md).

## <a name="creating-the-custom-map"></a>Criando o mapa personalizado

Um controle de mapa personalizado pode ser criado por meio da subclasse da [`Map`](xref:Xamarin.Forms.Maps.Map) classe, conforme mostrado no exemplo de código a seguir:

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
    public string Name { get; set; }
    public string Url { get; set; }
}
```

Essa classe define um `CustomPin` como herdando as propriedades da [`Pin`](xref:Xamarin.Forms.Maps.Pin) classe e adicionando `Name` `Url` Propriedades e.

## <a name="consuming-the-custom-map"></a>Consumindo o mapa personalizado

O controle `CustomMap` pode ser referenciado em XAML no projeto da biblioteca do .NET Standard declarando um namespace para sua localização e usando o prefixo do namespace no Controle de Mapeamento personalizado. O seguinte exemplo de código mostra como o controle `CustomMap` pode ser consumido por uma página XAML:

```xaml
<ContentPage ...
                   xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer">
    <local:CustomMap x:Name="customMap"
                   MapType="Street" />
</ContentPage>

```

O prefixo do namespace `local` pode ser qualquer nome. No entanto, os valores de `clr-namespace` e `assembly` devem corresponder aos detalhes do mapa personalizado. Quando o namespace é declarado, o prefixo é usado para referenciar o mapa personalizado.

O seguinte exemplo de código mostra como o controle `CustomMap` pode ser consumido por uma página em C#:

```csharp
public class MapPageCS : ContentPage
{
    public MapPageCS()
    {
        CustomMap customMap = new CustomMap
        {
            MapType = MapType.Street
        };
        // ...
        Content = customMap;
    }
}
```

A instância `CustomMap` será usada para exibir o mapa nativo em cada plataforma. [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType)A propriedade é define o estilo de exibição do [`Map`](xref:Xamarin.Forms.Maps.Map) , com os valores possíveis definidos na [`MapType`](xref:Xamarin.Forms.Maps.MapType) enumeração.

A localização do mapa e os marcadores que ele contém são inicializados conforme mostrado no seguinte exemplo de código:

```csharp
public MapPage()
{
    // ...
    CustomPin pin = new CustomPin
    {
        Type = PinType.Place,
        Position = new Position(37.79752, -122.40183),
        Label = "Xamarin San Francisco Office",
        Address = "394 Pacific Ave, San Francisco CA",
        Name = "Xamarin",
        Url = "http://xamarin.com/about/"
    };
    customMap.CustomPins = new List<CustomPin> { pin };
    customMap.Pins.Add(pin);
    customMap.MoveToRegion(MapSpan.FromCenterAndRadius(new Position(37.79752, -122.40183), Distance.FromMiles(1.0)));
}
```

Essa inicialização adiciona um PIN personalizado e posiciona o modo de exibição do mapa com o [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) método, que altera a posição e o nível de zoom do mapa, criando um [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) de a [`Position`](xref:Xamarin.Forms.Maps.Position) e um [`Distance`](xref:Xamarin.Forms.Maps.Distance) .

Agora, um renderizador personalizado pode ser adicionado a cada projeto de aplicativo para personalizar os controles de mapa nativos.

## <a name="creating-the-custom-renderer-on-each-platform"></a>Criando o renderizador personalizado em cada plataforma

O processo para criar a classe do renderizador personalizado é a seguinte:

1. Criar uma subclasse da classe `MapRenderer` que renderiza o mapa personalizado.
1. Substituir o método `OnElementChanged` que renderiza o mapa personalizado e escrever a lógica para personalizá-lo. Esse método é chamado quando o Xamarin.Forms mapa personalizado correspondente é criado.
1. Adicione um `ExportRenderer` atributo à classe de processador personalizado para especificar que ele será usado para renderizar o Xamarin.Forms mapa personalizado. Esse atributo é usado para registrar o renderizador personalizado com Xamarin.Forms .

> [!NOTE]
> O fornecimento de um renderizador personalizado em cada projeto da plataforma é opcional. Se um renderizador personalizado não estiver registrado, será usado o renderizador padrão da classe base do controle.

O diagrama a seguir ilustra as responsabilidades de cada projeto no aplicativo de exemplo, bem como as relações entre elas:

![Responsabilidades do projeto de renderizador personalizado de CustomMap](map-pin-images/solution-structure.png)

O controle `CustomMap` é renderizado por classes de renderizador específicas da plataforma, que derivam da classe `MapRenderer` para cada plataforma. Isso faz com que cada controle `CustomMap` seja renderizado com controles específicos da plataforma, conforme mostrado nas seguintes capturas de tela:

![CustomMap em cada plataforma](map-pin-images/screenshots.png)

A `MapRenderer` classe expõe o `OnElementChanged` método, que é chamado quando o Xamarin.Forms mapa personalizado é criado para renderizar o controle nativo correspondente. Esse método usa um parâmetro `ElementChangedEventArgs`, que contém as propriedades `OldElement` e `NewElement`. Essas propriedades representam o Xamarin.Forms elemento ao qual o renderizador *foi* anexado e o Xamarin.Forms elemento ao qual o renderizador *está* anexado, respectivamente. No aplicativo de exemplo, a propriedade `OldElement` será `null` e a propriedade `NewElement` conterá uma referência à instância de `CustomMap`.

Uma versão de substituição do método `OnElementChanged`, em cada classe de renderizador específica da plataforma, é o lugar para realização da personalização do controle nativo. Uma referência tipada ao controle nativo que está sendo usado na plataforma pode ser acessada por meio da propriedade `Control`. Além disso, uma referência ao Xamarin.Forms controle que está sendo processado pode ser obtida por meio da `Element` propriedade.

É necessário ter cuidado ao assinar manipuladores de eventos no método `OnElementChanged`, conforme demonstrado no seguinte exemplo de código:

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<Xamarin.Forms.View> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null)
  {
      // Unsubscribe from event handlers
  }

  if (e.NewElement != null)
  {
      // Configure the native control and subscribe to event handlers
  }
}
```

O controle nativo deve ser configurado e os manipuladores de eventos se inscreveram somente quando o renderizador personalizado é anexado a um novo Xamarin.Forms elemento. De forma semelhante, a assinatura dos manipuladores de eventos assinados só deverá ser cancelada quando o elemento ao qual o renderizador está anexado for alterado. Adotar essa abordagem ajudará a criar um renderizador personalizado que não sofre perdas de memória.

Cada classe de processador personalizado é decorada com um `ExportRenderer` atributo que registra o renderizador com Xamarin.Forms . O atributo usa dois parâmetros – o nome do tipo do Xamarin.Forms controle personalizado que está sendo renderizado e o nome do tipo do renderizador personalizado. O prefixo `assembly` do atributo especifica que o atributo se aplica a todo o assembly.

As seções a seguir abordam a implementação de cada classe de renderizador personalizado específica da plataforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Criando o renderizador personalizado no iOS

As seguintes capturas de tela mostram o mapa, antes e após a personalização:

![As capturas de tela mostram um dispositivo móvel com um PIN comum e um PIN anotado.](map-pin-images/map-layout-ios.png)

No iOS, o marcador é chamado de *anotação* e pode ser uma imagem personalizada ou um marcador definido pelo sistema de várias cores. As anotações podem opcionalmente mostrar um *texto explicativo*, que é exibido em resposta à seleção da anotação pelo usuário. O texto explicativo exibe o `Label` e as propriedades `Address` da instância `Pin`, com as exibições acessório direita e esquerda opcionais. Na captura de tela acima, a exibição acessório esquerda é a imagem de um macaco, com a exibição acessório direita sendo o botão *Informações*.

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

            if (e.OldElement != null)
            {
                var nativeMap = Control as MKMapView;
                if (nativeMap != null)
                {
                    nativeMap.RemoveAnnotations(nativeMap.Annotations);
                    nativeMap.GetViewForAnnotation = null;
                    nativeMap.CalloutAccessoryControlTapped -= OnCalloutAccessoryControlTapped;
                    nativeMap.DidSelectAnnotationView -= OnDidSelectAnnotationView;
                    nativeMap.DidDeselectAnnotationView -= OnDidDeselectAnnotationView;
                }
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MKMapView;
                customPins = formsMap.CustomPins;

                nativeMap.GetViewForAnnotation = GetViewForAnnotation;
                nativeMap.CalloutAccessoryControlTapped += OnCalloutAccessoryControlTapped;
                nativeMap.DidSelectAnnotationView += OnDidSelectAnnotationView;
                nativeMap.DidDeselectAnnotationView += OnDidDeselectAnnotationView;
            }
        }
        // ...
    }
}
```

O `OnElementChanged` método executa a seguinte configuração da [`MKMapView`](xref:MapKit.MKMapView) instância, desde que o renderizador personalizado esteja anexado a um novo Xamarin.Forms elemento:

- A [`GetViewForAnnotation`](xref:MapKit.MKMapView.GetViewForAnnotation*) propriedade é definida como o `GetViewForAnnotation` método. Esse método é chamado quando a [localização da anotação se torna visível no mapa](#displaying-the-annotation) e é usado para personalizar a anotação antes da exibição.
- Os manipuladores de eventos para os eventos `CalloutAccessoryControlTapped`, `DidSelectAnnotationView` e `DidDeselectAnnotationView` são registrados. Esses eventos são disparados quando o usuário [toca o acessório direito no texto explicativo](#tapping-on-the-right-callout-accessory-view) e quando o usuário [marca](#selecting-the-annotation) e [desmarca](#deselecting-the-annotation) a anotação, respectivamente. A assinatura dos eventos é cancelada somente quando o elemento ao qual o renderizador está anexado é alterado.

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
    if (customPin == null)
    {
        throw new Exception("Custom pin not found");
    }

    annotationView = mapView.DequeueReusableAnnotation(customPin.Name);
    if (annotationView == null)
    {
        annotationView = new CustomMKAnnotationView(annotation, customPin.Name);
        annotationView.Image = UIImage.FromFile("pin.png");
        annotationView.CalloutOffset = new CGPoint(0, 0);
        annotationView.LeftCalloutAccessoryView = new UIImageView(UIImage.FromFile("monkey.png"));
        annotationView.RightCalloutAccessoryView = UIButton.FromType(UIButtonType.DetailDisclosure);
        ((CustomMKAnnotationView)annotationView).Name = customPin.Name;
        ((CustomMKAnnotationView)annotationView).Url = customPin.Url;
    }
    annotationView.CanShowCallout = true;

    return annotationView;
}
```

Esse método garante que a anotação seja exibida como uma imagem personalizada, em vez de como um marcador definido pelo sistema, e que quando a anotação for tocada, um texto explicativo seja exibido que inclua conteúdo adicional à esquerda e à direita do título da anotação e do endereço. Isso é feito da seguinte maneira:

1. O método `GetCustomPin` é chamado para retornar os dados de marcador personalizados da anotação.
1. Para conservar a memória, a exibição da anotação é agrupada para reutilização com a chamada para [`DequeueReusableAnnotation`](xref:MapKit.MKMapView.DequeueReusableAnnotation*) .
1. A classe `CustomMKAnnotationView` estende a classe `MKAnnotationView` com as propriedades `Name` e `Url` que correspondem às propriedades idênticas na instância `CustomPin`. Uma nova instância da `CustomMKAnnotationView` é criada, desde que a anotação seja `null`:
    - A propriedade `CustomMKAnnotationView.Image` está definida como a imagem que representará a anotação no mapa.
    - A propriedade `CustomMKAnnotationView.CalloutOffset` é definida como um `CGPoint` que especifica que o texto explicativo será centralizado acima da anotação.
    - A propriedade `CustomMKAnnotationView.LeftCalloutAccessoryView` é definida como uma imagem de um macaco que será exibido à esquerda do título da anotação e do endereço.
    - A propriedade `CustomMKAnnotationView.RightCalloutAccessoryView` é definida como um botão *Informações* que será exibido à direita do título da anotação e do endereço.
    - A propriedade `CustomMKAnnotationView.Name` é definida como a propriedade `CustomPin.Name` retornada pelo método `GetCustomPin`. Isso permite que a anotação seja identificada para que seu [texto explicativo possa ser personalizado ainda mais](#selecting-the-annotation), se desejado.
    - A propriedade `CustomMKAnnotationView.Url` é definida como a propriedade `CustomPin.Url` retornada pelo método `GetCustomPin`. A URL será direcionada quando o usuário [tocar o botão exibido na exibição acessório direita do texto explicativo](#tapping-on-the-right-callout-accessory-view).
1. A [`MKAnnotationView.CanShowCallout`](xref:MapKit.MKAnnotationView.CanShowCallout*) propriedade é definida como para `true` que o texto explicativo seja exibido quando a anotação é tocada.
1. A anotação é retornada para a exibição no mapa.

#### <a name="selecting-the-annotation"></a>Selecionando a anotação

Quando o usuário toca a anotação, o evento `DidSelectAnnotationView` é disparado, que, por sua vez, executa o método `OnDidSelectAnnotationView`:

```csharp
void OnDidSelectAnnotationView(object sender, MKAnnotationViewEventArgs e)
{
    CustomMKAnnotationView customView = e.View as CustomMKAnnotationView;
    customPinView = new UIView();

    if (customView.Name.Equals("Xamarin"))
    {
        customPinView.Frame = new CGRect(0, 0, 200, 84);
        var image = new UIImageView(new CGRect(0, 0, 200, 84));
        image.Image = UIImage.FromFile("xamarin.png");
        customPinView.AddSubview(image);
        customPinView.Center = new CGPoint(0, -(e.View.Frame.Height + 75));
        e.View.AddSubview(customPinView);
    }
}
```

Esse método estende o texto explicativo existente (que contém as exibições acessório esquerda e direita), adicionando uma instância `UIView` a ele que contém uma imagem do logotipo do Xamarin, desde que a anotação selecionada tenha sua propriedade `Name` definida como `Xamarin`. Isso permite cenários em que diferentes textos explicativos podem ser exibidos para diferentes anotações. A instância `UIView` será exibida centralizada acima do texto explicativo existente.

#### <a name="tapping-on-the-right-callout-accessory-view"></a>Tocando a exibição acessório direita do texto explicativo

Quando o usuário toca o botão *Informações* na exibição acessório direita do texto explicativo, o evento `CalloutAccessoryControlTapped` é disparado, que, por sua vez, executa o método `OnCalloutAccessoryControlTapped`:

```csharp
void OnCalloutAccessoryControlTapped(object sender, MKMapViewAccessoryTappedEventArgs e)
{
    CustomMKAnnotationView customView = e.View as CustomMKAnnotationView;
    if (!string.IsNullOrWhiteSpace(customView.Url))
    {
        UIApplication.SharedApplication.OpenUrl(new Foundation.NSUrl(customView.Url));
    }
}
```

Esse método abre um navegador da Web e navega para o endereço armazenado na propriedade `CustomMKAnnotationView.Url`. Observe que o endereço foi definido durante a criação da coleção `CustomPin` no projeto da biblioteca do .NET Standard.

#### <a name="deselecting-the-annotation"></a>Cancelando a seleção da anotação

Quando a anotação é exibida e o usuário toca o mapa, o evento `DidDeselectAnnotationView` é disparado, que, por sua vez, executa o método `OnDidDeselectAnnotationView`:

```csharp
void OnDidDeselectAnnotationView(object sender, MKAnnotationViewEventArgs e)
{
    if (!e.View.Selected)
    {
        customPinView.RemoveFromSuperview();
        customPinView.Dispose();
        customPinView = null;
    }
}
```

Esse método garante que, quando o texto explicativo existente não estiver selecionado, a parte estendida do texto explicativo (a imagem do logotipo do Xamarin) também não será mais exibida e seus recursos serão liberados.

Para obter mais informações sobre como personalizar uma instância `MKMapView`, confira [Mapas do iOS](~/ios/user-interface/controls/ios-maps/index.md).

### <a name="creating-the-custom-renderer-on-android"></a>Criando o renderizador personalizado no Android

As seguintes capturas de tela mostram o mapa, antes e após a personalização:

![As capturas de tela mostram um dispositivo móvel com um marcador comum e um marcador personalizado.](map-pin-images/map-layout-android.png)

No Android, o marcador é chamado de *marcador* e pode ser uma imagem personalizada ou um marcador definido pelo sistema de várias cores. Os marcadores podem mostrar uma *janela de informações*, que é exibida na resposta ao toque do usuário no marcador. A janela de informações exibe as propriedades `Label` e `Address` da instância `Pin` e pode ser personalizada para incluir outros tipos de conteúdo. No entanto, apenas uma janela de informações pode ser mostrada por vez.

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

Desde que o renderizador personalizado esteja anexado a um novo Xamarin.Forms elemento, o `OnElementChanged` método recupera a lista de Pins personalizados do controle. Depois que a instância `GoogleMap` estiver disponível, a substituição `OnMapReady` será invocada. Esse método registra um manipulador de eventos para o evento `InfoWindowClick`, que é disparado quando a [janela de informações recebe um clique](#clicking-on-the-info-window) e tem a assinatura cancelada somente quando o elemento ao qual o renderizador está anexado é alterado. A substituição `OnMapReady` também chama o método `SetInfoWindowAdapter` para especificar que a instância da classe `CustomMapRenderer` fornecerá os métodos para personalizar a janela de informações.

A classe `CustomMapRenderer` implementa a interface `GoogleMap.IInfoWindowAdapter` para [personalizar a janela de informações](#customizing-the-info-window). Essa interface especifica que os seguintes métodos precisam ser implementados:

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

#### <a name="customizing-the-info-window"></a>Personalizando a janela de informações

Quando um usuário toca o marcador, o método `GetInfoContents` é executado, desde que o método `GetInfoWindow` retorne `null`. O seguinte exemplo de código mostra o método `GetInfoContents`:

```csharp
public Android.Views.View GetInfoContents(Marker marker)
{
    var inflater = Android.App.Application.Context.GetSystemService(Context.LayoutInflaterService) as Android.Views.LayoutInflater;
    if (inflater != null)
    {
        Android.Views.View view;

        var customPin = GetCustomPin(marker);
        if (customPin == null)
        {
            throw new Exception("Custom pin not found");
        }

        if (customPin.Name.Equals("Xamarin"))
        {
            view = inflater.Inflate(Resource.Layout.XamarinMapInfoWindow, null);
        }
        else
        {
            view = inflater.Inflate(Resource.Layout.MapInfoWindow, null);
        }

        var infoTitle = view.FindViewById<TextView>(Resource.Id.InfoWindowTitle);
        var infoSubtitle = view.FindViewById<TextView>(Resource.Id.InfoWindowSubtitle);

        if (infoTitle != null)
        {
            infoTitle.Text = marker.Title;
        }
        if (infoSubtitle != null)
        {
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
- O layout `XamarinMapInfoWindow` é inflado se a propriedade `CustomPin.Name` é igual a `Xamarin`. Caso contrário, o layout `MapInfoWindow` é inflado. Isso permite cenários em que diferentes layouts da janela de informações podem ser exibidos para diferentes marcadores.
- Os recursos `InfoWindowTitle` e `InfoWindowSubtitle` são recuperados do layout inflado e suas propriedades `Text` são definidas com os dados correspondentes por meio da instância `Marker`, desde que os recursos não sejam `null`.
- A instância `View` é retornada para exibição no mapa.

> [!NOTE]
> Uma janela de informações não é uma `View` dinâmica. Em vez disso, o Android converterá a `View` em um bitmap estático e os exibirá como uma imagem. Isso significa que, embora uma janela de informações possa responder a um evento de clique, ela não pode responder a eventos de toque ou gestos e os controles individuais na janela de informações não podem responder a seus próprios eventos de clique.

#### <a name="clicking-on-the-info-window"></a>Clicando na janela de informações

Quando o usuário clica a janela de informações, o evento `InfoWindowClick` é disparado, que, por sua vez, executa o método `OnInfoWindowClick`:

```csharp
void OnInfoWindowClick(object sender, GoogleMap.InfoWindowClickEventArgs e)
{
    var customPin = GetCustomPin(e.Marker);
    if (customPin == null)
    {
        throw new Exception("Custom pin not found");
    }

    if (!string.IsNullOrWhiteSpace(customPin.Url))
    {
        var url = Android.Net.Uri.Parse(customPin.Url);
        var intent = new Intent(Intent.ActionView, url);
        intent.AddFlags(ActivityFlags.NewTask);
        Android.App.Application.Context.StartActivity(intent);
    }
}
```

Esse método abre um navegador da Web e navega para o endereço armazenado na propriedade `Url` da instância `CustomPin` recuperada para o `Marker`. Observe que o endereço foi definido durante a criação da coleção `CustomPin` no projeto da biblioteca do .NET Standard.

Para obter mais informações sobre como personalizar uma instância `MapView`, confira [API de Mapas](~/android/platform/maps-and-location/maps/maps-api.md).

### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Criando o renderizador personalizado na Plataforma Universal do Windows

As seguintes capturas de tela mostram o mapa, antes e após a personalização:

![As capturas de tela mostram um dispositivo móvel com um ícone de mapa comum e um ícone de mapa personalizado.](map-pin-images/map-layout-uwp.png)

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

O `OnElementChanged` método executa as seguintes operações, desde que o renderizador personalizado esteja anexado a um novo Xamarin.Forms elemento:

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

            if (customPin.Name.Equals("Xamarin"))
            {
                if (mapOverlay == null)
                {
                    mapOverlay = new XamarinMapOverlay(customPin);
                }

                var snPosition = new BasicGeoposition { Latitude = customPin.Position.Latitude, Longitude = customPin.Position.Longitude };
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

Para obter mais informações sobre como personalizar uma instância `MapControl`, confira [Visão geral de mapas e localização](/windows/uwp/maps-and-location/) no MSDN.

## <a name="related-links"></a>Links Relacionados

- [Controle de mapas](~/xamarin-forms/user-interface/map/index.md)
- [Mapas do iOS](~/ios/user-interface/controls/ios-maps/index.md)
- [API de mapas](~/android/platform/maps-and-location/maps/maps-api.md)
- [Marcador personalizado (amostra)](/samples/xamarin/xamarin-forms-samples/customrenderers-map-pin)