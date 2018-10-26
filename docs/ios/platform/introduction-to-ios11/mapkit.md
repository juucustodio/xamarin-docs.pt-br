---
title: Novos recursos no MapKit no iOS 11
description: 'Este documento descreve novos recursos MapKit no iOS 11: agrupamento de marcadores, o botão a bússola, a exibição de escala e o botão de controle de usuário.'
ms.prod: xamarin
ms.assetid: 304AE5A3-518F-422F-BE24-92D62CE30F34
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/30/2017
ms.openlocfilehash: 3c1b29a4aba03ffe8a3131625ef29cf64766bb6c
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116908"
---
# <a name="new-features-in-mapkit-on-ios-11"></a>Novos recursos no MapKit no iOS 11

iOS 11 adiciona os seguintes recursos novos para MapKit:

- [Anotação de Clustering](#clustering)
- [Botão da bússola](#compass)
- [Exibição de escala](#scale)
- [Botão de controle de usuário](#user-tracking)

![Mapa mostrando marcadores em cluster e do botão da bússola](mapkit-images/cyclemap-heading.png)

<a name="clustering" />

## <a name="automatically-grouping-markers-while-zooming"></a>Marcadores de agrupamento automaticamente durante a aplicação de zoom

O exemplo [MapKit exemplo "Tandm"](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample/) mostra como implementar a anotação de iOS 11 novo recurso de clustering.

### <a name="1-create-an-mkpointannotation-subclass"></a>1. Criar um `MKPointAnnotation` subclasse

A classe de anotação ponto representa cada marcador do mapa. Eles podem ser adicionados usando individualmente `MapView.AddAnnotation()` ou de uma matriz usando `MapView.AddAnnotations()`.

Classes de ponto de anotação não tem uma representação visual, eles só são necessários para representar os dados associados com o marcador (o mais importante é o `Coordinate` propriedade que é sua latitude e longitude no mapa) e todas as propriedades personalizadas:

```csharp
public class Bike : MKPointAnnotation
{
  public BikeType Type { get; set; } = BikeType.Tricycle;
  public Bike(){}
  public Bike(NSNumber lat, NSNumber lgn, NSNumber type)
  {
    Coordinate = new CLLocationCoordinate2D(lat.NFloatValue, lgn.NFloatValue);
    switch(type.NUIntValue) {
      case 0:
        Type = BikeType.Unicycle;
        break;
      case 1:
        Type = BikeType.Tricycle;
        break;
    }
  }
}
```

### <a name="2-create-an-mkmarkerannotationview-subclass-for-single-markers"></a>2. Criar um `MKMarkerAnnotationView` subclasse para marcadores únicos

O modo de exibição de anotação de marcador é a representação visual de cada anotação e o estilo é feito usando as propriedades, como:

- **MarkerTintColor** – a cor do marcador.
- **GlyphText** – texto exibido no marcador.
- **GlyphImage** – define a imagem que é exibida no marcador.
- **DisplayPriority** – determina a ordem z (comportamento empilhamento) quando o mapa está lotado com marcadores. Use um dos `Required`, `DefaultHigh`, ou `DefaultLow`.

Para dar suporte a clustering automática, você também deve definir:

- **ClusteringIdentifier** – Isso controla quais marcadores clusterizadas juntos. Você pode usar o mesmo identificador para todos os seus marcadores ou usam identificadores diferentes para controlar a forma como eles são agrupados juntos.

```csharp
[Register("BikeView")]
public class BikeView : MKMarkerAnnotationView
{
  public static UIColor UnicycleColor = UIColor.FromRGB(254, 122, 36);
  public static UIColor TricycleColor = UIColor.FromRGB(153, 180, 44);
  public override IMKAnnotation Annotation
  {
    get {
      return base.Annotation;
    }
    set {
      base.Annotation = value;

      var bike = value as Bike;
      if (bike != null){
        ClusteringIdentifier = "bike";
        switch(bike.Type){
          case BikeType.Unicycle:
            MarkerTintColor = UnicycleColor;
            GlyphImage = UIImage.FromBundle("Unicycle");
            DisplayPriority = MKFeatureDisplayPriority.DefaultLow;
            break;
          case BikeType.Tricycle:
            MarkerTintColor = TricycleColor;
            GlyphImage = UIImage.FromBundle("Tricycle");
            DisplayPriority = MKFeatureDisplayPriority.DefaultHigh;
            break;
        }
      }
    }
  }
```

### <a name="3-create-an-mkannotationview-to-represent-clusters-of-markers"></a>3. Criar um `MKAnnotationView` para representar a clusters de marcadores

Enquanto o modo de exibição de anotação que represente um cluster de marcadores _poderia_ ser uma imagem simples, os usuários esperam que o aplicativo para fornecer dicas visuais sobre quantos marcadores foram agrupadas.

O [código de exemplo](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample/) usa CoreGraphics para renderizar o número de marcadores em cluster, bem como uma representação gráfica de círculo da proporção de cada tipo de marcador.

Você também deve definir:

- **DisplayPriority** – determina a ordem z (comportamento empilhamento) quando o mapa está lotado com marcadores. Use um dos `Required`, `DefaultHigh`, ou `DefaultLow`.
- **CollisionMode** – `Circle` ou `Rectangle`.

```csharp
[Register("ClusterView")]
public class ClusterView : MKAnnotationView
{
  public static UIColor ClusterColor = UIColor.FromRGB(202, 150, 38);
  public override IMKAnnotation Annotation
  {
    get {
      return base.Annotation;
    }
    set {
      base.Annotation = value;
      var cluster = MKAnnotationWrapperExtensions.UnwrapClusterAnnotation(value);
      if (cluster != null)
      {
        var renderer = new UIGraphicsImageRenderer(new CGSize(40, 40));
        var count = cluster.MemberAnnotations.Length;
        var unicycleCount = CountBikeType(cluster.MemberAnnotations, BikeType.Unicycle);

        Image = renderer.CreateImage((context) => {
          // Fill full circle with tricycle color
          BikeView.TricycleColor.SetFill();
          UIBezierPath.FromOval(new CGRect(0, 0, 40, 40)).Fill();
          // Fill pie with unicycle color
          BikeView.UnicycleColor.SetFill();
          var piePath = new UIBezierPath();
          piePath.AddArc(new CGPoint(20,20), 20, 0, (nfloat)(Math.PI * 2.0 * unicycleCount / count), true);
          piePath.AddLineTo(new CGPoint(20, 20));
          piePath.ClosePath();
          piePath.Fill();
          // Fill inner circle with white color
          UIColor.White.SetFill();
          UIBezierPath.FromOval(new CGRect(8, 8, 24, 24)).Fill();
          // Finally draw count text vertically and horizontally centered
          var attributes = new UIStringAttributes() {
            ForegroundColor = UIColor.Black,
            Font = UIFont.BoldSystemFontOfSize(20)
          };
          var text = new NSString($"{count}");
          var size = text.GetSizeUsingAttributes(attributes);
          var rect = new CGRect(20 - size.Width / 2, 20 - size.Height / 2, size.Width, size.Height);
          text.DrawString(rect, attributes);
        });
      }
    }
  }
  public ClusterView(){}
  public ClusterView(MKAnnotation annotation, string reuseIdentifier) : base(annotation, reuseIdentifier)
  {
    DisplayPriority = MKFeatureDisplayPriority.DefaultHigh;
    CollisionMode = MKAnnotationViewCollisionMode.Circle;
    // Offset center point to animate better with marker annotations
    CenterOffset = new CoreGraphics.CGPoint(0, -10);
  }
  private nuint CountBikeType(IMKAnnotation[] members, BikeType type) {
    nuint count = 0;
    foreach(Bike member in members){
      if (member.Type == type) ++count;
    }
    return count;
  }
}
```

### <a name="4-register-the-view-classes"></a>4. Registre as classes de exibição

Quando o controle de exibição de mapa está sendo criado e adicionado a um modo de exibição, registrar os tipos de exibição de anotação para habilitar o comportamento automático de clustering, como o mapa é ampliado de entrada e saída:

```csharp
MapView.Register(typeof(BikeView), MKMapViewDefault.AnnotationViewReuseIdentifier);
MapView.Register(typeof(ClusterView), MKMapViewDefault.ClusterAnnotationViewReuseIdentifier);
```

### <a name="5-render-the-map"></a>5. Renderizar o mapa!

Quando o mapa é processado, marcadores de anotação serão clusterizados ou renderizados dependendo do nível de zoom. O nível de zoom que as alterações, marcadores de animar dentro e fora de clusters.

![Mostrar marcadores em cluster no mapa de simulador](mapkit-images/cyclemap-sml.png)

Consulte a [mapeia seção](~/ios/user-interface/controls/ios-maps/index.md) para obter mais informações sobre como exibir dados com MapKit.

<a name="compass" />

## <a name="compass-button"></a>Botão da bússola

iOS 11 adiciona a capacidade de remover a bússola reduzir o mapa e renderizá-lo em outro lugar no modo de exibição. Consulte a [aplicativo de exemplo Tandm](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample/) para obter um exemplo.

Criar um botão que se parece com uma bússola (incluindo animação em tempo real quando a orientação do mapa é alterada), e renderiza-o em outro controle.

![Bússola botão na barra de navegação](mapkit-images/compass-sml.png)

O código a seguir cria um botão de bússola e renderiza-o na barra de navegação:

```csharp
var compass = MKCompassButton.FromMapView(MapView);
compass.CompassVisibility = MKFeatureVisibility.Visible;
NavigationItem.RightBarButtonItem = new UIBarButtonItem(compass);
MapView.ShowsCompass = false; // so we don't have two compasses!
```

O `ShowsCompass` propriedade pode ser usada para controlar a visibilidade da bússola padrão dentro da exibição do mapa.

<a name="scale" />

## <a name="scale-view"></a>Exibição de escala

Adicionar a escala em outro lugar no modo de exibição usando o `MKScaleView.FromMapView()` método para obter uma instância do modo de exibição para adicionar em outro lugar na hierarquia de exibição de escala.

![Exibição de escala sobreposta em um mapa](mapkit-images/scale-sml.png)

```csharp
var scale = MKScaleView.FromMapView(MapView);
scale.LegendAlignment = MKScaleViewAlignment.Trailing;
scale.TranslatesAutoresizingMaskIntoConstraints = false;
View.AddSubview(scale); // constraints omitted for simplicity
MapView.ShowsScale = false; // so we don't have two scale displays!
```

O `ShowsScale` propriedade pode ser usada para controlar a visibilidade da bússola padrão dentro da exibição do mapa.

<a name="user-tracking" />

## <a name="user-tracking-button"></a>Botão de controle de usuário

O botão de controle de usuário centraliza o mapa no local atual do usuário. Use o `MKUserTrackingButton.FromMapView()` método para obter uma instância do botão, aplicar alterações de formatação e adicionar em outro lugar na hierarquia de exibição.

![Botão de localização do usuário sobreposto em um mapa](mapkit-images/user-location-sml.png)

```csharp
var button = MKUserTrackingButton.FromMapView(MapView);
button.Layer.BackgroundColor = UIColor.FromRGBA(255,255,255,80).CGColor;
button.Layer.BorderColor = UIColor.White.CGColor;
button.Layer.BorderWidth = 1;
button.Layer.CornerRadius = 5;
button.TranslatesAutoresizingMaskIntoConstraints = false;
View.AddSubview(button); // constraints omitted for simplicity
```


## <a name="related-links"></a>Links relacionados

- [Exemplo de MapKit 'Tandm'](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample/)
- [MKCompassButton](https://developer.apple.com/documentation/mapkit/mkcompassbutton)
- [O que há de novo no MapKit (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/237/)
