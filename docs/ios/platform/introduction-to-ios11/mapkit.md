---
title: Novos recursos no MapKit no iOS 11
description: 'Este documento descreve os novos recursos do MapKit no iOS 11: agrupando marcadores, o botão de bússola, a exibição de escala e o botão de controle de usuário.'
ms.prod: xamarin
ms.assetid: 304AE5A3-518F-422F-BE24-92D62CE30F34
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/30/2017
ms.openlocfilehash: bddab35044c2b85b69146a03babd9884784baceb
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84574581"
---
# <a name="new-features-in-mapkit-on-ios-11"></a>Novos recursos no MapKit no iOS 11

o iOS 11 adiciona os seguintes novos recursos ao MapKit:

- [Clustering de anotação](#clustering)
- [Botão de bússola](#compass)
- [Exibição de escala](#scale)
- [Botão de controle do usuário](#user-tracking)

![Mapa mostrando marcadores clusterizados e botão de bússola](mapkit-images/cyclemap-heading.png)

<a name="clustering"></a>

## <a name="automatically-grouping-markers-while-zooming"></a>Agrupamento de marcadores automaticamente ao aplicar zoom

O exemplo de MapKit de exemplo ["Tandm"](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-mapkitsample) mostra como implementar o novo recurso de clustering de anotação do IOS 11.

### <a name="1-create-an-mkpointannotation-subclass"></a>1. criar uma `MKPointAnnotation` subclasse

A classe de anotação de ponto representa cada marcador no mapa. Eles podem ser adicionados individualmente usando `MapView.AddAnnotation()` ou de uma matriz usando `MapView.AddAnnotations()` .

As classes de anotação de ponto não têm uma representação visual, elas só precisam representar os dados associados ao marcador (o mais importante, a `Coordinate` propriedade que é a latitude e a longitude no mapa) e todas as propriedades personalizadas:

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

### <a name="2-create-an-mkmarkerannotationview-subclass-for-single-markers"></a>2. criar uma `MKMarkerAnnotationView` subclasse para marcadores únicos

A exibição de anotação do marcador é a representação visual de cada anotação e é estilizada usando propriedades como:

- **MarkerTintColor** – a cor do marcador.
- **GlyphText** – texto exibido no marcador.
- **Glyphimage** – define a imagem que é exibida no marcador.
- **DisplayPriority** – determina a ordem z (comportamento de empilhamento) quando o mapa está lotado com marcadores. Use um dos `Required` , `DefaultHigh` ou `DefaultLow` .

Para dar suporte ao clustering automático, você também deve definir:

- **ClusteringIdentifier** – isso controla quais marcadores são agrupados. Você pode usar o mesmo identificador para todos os seus marcadores ou usar identificadores diferentes para controlar a maneira como eles são agrupados juntos.

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

### <a name="3-create-an-mkannotationview-to-represent-clusters-of-markers"></a>3. criar um `MKAnnotationView` para representar clusters de marcadores

Embora a exibição de anotação que representa um cluster de marcadores _possa_ ser uma imagem simples, os usuários esperam que o aplicativo forneça indicações visuais sobre o número de marcadores agrupados.

O [código de exemplo](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-mapkitsample) usa CoreGraphics para renderizar o número de marcadores no cluster, bem como uma representação de gráfico de círculo da proporção de cada tipo de marcador.

Você também deve definir:

- **DisplayPriority** – determina a ordem z (comportamento de empilhamento) quando o mapa está lotado com marcadores. Use um dos `Required` , `DefaultHigh` ou `DefaultLow` .
- **Colisãomode** – `Circle` ou `Rectangle` .

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

### <a name="4-register-the-view-classes"></a>4. registrar as classes de exibição

Quando o controle de exibição de mapa está sendo criado e adicionado a um modo de exibição, registre os tipos de exibição de anotação para habilitar o comportamento de clustering automático à medida que o mapa é ampliado e reduzido:

```csharp
MapView.Register(typeof(BikeView), MKMapViewDefault.AnnotationViewReuseIdentifier);
MapView.Register(typeof(ClusterView), MKMapViewDefault.ClusterAnnotationViewReuseIdentifier);
```

### <a name="5-render-the-map"></a>5. renderizar o mapa!

Quando o mapa é renderizado, os marcadores de anotação serão clusterizados ou renderizados, dependendo do nível de zoom. Como as alterações no nível de zoom, os marcadores animam e saem dos clusters.

![Simulador mostrando marcadores clusterizados no mapa](mapkit-images/cyclemap-sml.png)

Consulte a [seção mapas](~/ios/user-interface/controls/ios-maps/index.md) para obter mais informações sobre como exibir dados com o MapKit.

<a name="compass"></a>

## <a name="compass-button"></a>Botão de bússola

o iOS 11 adiciona a capacidade de retirar a bússola do mapa e renderizá-la em outro lugar na exibição. Consulte o [aplicativo de exemplo Tandm](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-mapkitsample) para obter um exemplo.

Crie um botão que se parece com uma bússola (incluindo uma animação ao vivo quando a orientação do mapa é alterada) e a renderiza em outro controle.

![Botão de bússola na barra de navegação](mapkit-images/compass-sml.png)

O código a seguir cria um botão de bússola e o renderiza na barra de navegação:

```csharp
var compass = MKCompassButton.FromMapView(MapView);
compass.CompassVisibility = MKFeatureVisibility.Visible;
NavigationItem.RightBarButtonItem = new UIBarButtonItem(compass);
MapView.ShowsCompass = false; // so we don't have two compasses!
```

A `ShowsCompass` propriedade pode ser usada para controlar a visibilidade da bússola padrão dentro da exibição do mapa.

<a name="scale"></a>

## <a name="scale-view"></a>Exibição de escala

Adicione a escala em outro lugar na exibição usando o `MKScaleView.FromMapView()` método para obter uma instância da exibição de escala a ser adicionada em outro lugar na hierarquia de exibição.

![Exibição de escala sobreposta em um mapa](mapkit-images/scale-sml.png)

```csharp
var scale = MKScaleView.FromMapView(MapView);
scale.LegendAlignment = MKScaleViewAlignment.Trailing;
scale.TranslatesAutoresizingMaskIntoConstraints = false;
View.AddSubview(scale); // constraints omitted for simplicity
MapView.ShowsScale = false; // so we don't have two scale displays!
```

A `ShowsScale` propriedade pode ser usada para controlar a visibilidade da bússola padrão dentro da exibição do mapa.

<a name="user-tracking"></a>

## <a name="user-tracking-button"></a>Botão de controle do usuário

O botão de controle do usuário centraliza o mapa no local atual do usuário. Use o `MKUserTrackingButton.FromMapView()` método para obter uma instância do botão, aplicar alterações de formatação e adicionar em outro lugar na hierarquia de exibição.

![Botão local do usuário sobreposto em um mapa](mapkit-images/user-location-sml.png)

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

- [Exemplo de MapKit ' Tandm '](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-mapkitsample)
- [MKCompassButton](https://developer.apple.com/documentation/mapkit/mkcompassbutton)
- [O que há de novo no MapKit (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/237/)
