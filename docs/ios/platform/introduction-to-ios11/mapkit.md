---
title: Novos recursos no MapKit no iOS 11
ms.prod: xamarin
ms.assetid: 304AE5A3-518F-422F-BE24-92D62CE30F34
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/30/2016
ms.openlocfilehash: 3b1ac8015a86292f00f26133277ead2f211dca33
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="new-features-in-mapkit-on-ios-11"></a>Novos recursos no MapKit no iOS 11

iOS 11 adiciona os seguintes novos recursos para MapKit:

- [Anotação de Clustering](#clustering)
- [Botão bússola](#compass)
- [Exibição de escala](#scale)
- [Botão de controle de usuário](#user-tracking)

![Mapa mostrando marcadores em cluster e bússola botão](mapkit-images/cyclemap-heading.png)

<a name="clustering" />

## <a name="automatically-grouping-markers-while-zooming"></a>Marcadores de agrupamento automaticamente durante a aplicação de zoom

O exemplo [MapKit exemplo "Tandm"](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample/) mostra como implementar a anotação iOS 11 novo recurso de clustering.

### <a name="1-create-an-mkpointannotation-subclass"></a>1. Criar um `MKPointAnnotation` subclasse

A classe de anotação de ponto representa cada marcador do mapa. Eles podem ser adicionados individualmente usando `MapView.AddAnnotation()` ou de uma matriz usando `MapView.AddAnnotations()`.

Classes de anotação de ponto não tem uma representação visual, são necessárias apenas para representar os dados associados com o marcador (o mais importante é o `Coordinate` propriedade que é seu latitude e longitude no mapa) e quaisquer propriedades personalizadas:

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

### <a name="2-create-an-mkmarkerannotationview-subclass-for-single-markers"></a>2. Criar um `MKMarkerAnnotationView` subclasse marcadores único

O modo de exibição de anotação do marcador é a representação visual de cada anotação e estilo usando propriedades como:

- **MarkerTintColor** – a cor do marcador.
- **GlyphText** – texto exibido no marcador.
- **GlyphImage** – define a imagem que é exibida no marcador.
- **DisplayPriority** – determina a ordem z (comportamento empilhamento) quando o mapa está cheio com marcadores. Use um dos `Required`, `DefaultHigh`, ou `DefaultLow`.

Para dar suporte a clustering automática, você também deve definir:

- **ClusteringIdentifier** – Isso controla quais marcadores obtenham agrupados juntos. Você pode usar o mesmo identificador para todos os seus marcadores ou usam identificadores diferentes para controlar a maneira como eles são agrupados juntos.

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

### <a name="3-create-an-mkannotationview-to-represent-clusters-of-markers"></a>3. Criar um `MKAnnotationView` representar clusters de marcadores

Embora o modo de exibição de anotação que representa um cluster de marcadores _foi_ ser uma imagem simples, os usuários esperam que o aplicativo para fornecer dicas visuais sobre quantas marcadores foram agrupadas.

O [código de exemplo](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample/) usa CoreGraphics para processar o número de marcadores em cluster, bem como uma representação gráfica de círculo da proporção de cada tipo de marcador.

Você também deve definir:

- **DisplayPriority** – determina a ordem z (comportamento empilhamento) quando o mapa está cheio com marcadores. Use um dos `Required`, `DefaultHigh`, ou `DefaultLow`.
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

### <a name="4-register-the-view-classes"></a>4. Registrar as classes de exibição

Quando o controle de exibição de mapa está sendo criado e adicionada a uma exibição, registre os tipos de exibição de anotação para habilitar o comportamento automático de clustering, como o mapa é ampliado e sair:

```csharp
MapView.Register(typeof(BikeView), MKMapViewDefault.AnnotationViewReuseIdentifier);
MapView.Register(typeof(ClusterView), MKMapViewDefault.ClusterAnnotationViewReuseIdentifier);
```

### <a name="5-render-the-map"></a>5. Renderizar o mapa!

Quando o mapa é processado, marcadores de anotação serão clusterizados ou renderizados dependendo do nível de zoom. Como altera o nível de zoom, marcadores animar dentro e fora de clusters.

![Mostrar marcadores de cluster no mapa do simulador](mapkit-images/cyclemap-sml.png)

Consulte o [mapeia seção](~/ios/user-interface/controls/ios-maps/index.md) para obter mais informações sobre como exibir dados com MapKit.

<a name="compass" />

## <a name="compass-button"></a>Botão bússola

iOS 11 adiciona a capacidade de pop bússola fora do mapa e processá-lo em outro lugar no modo de exibição. Consulte o [Tandm aplicativo de exemplo](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample/) para obter um exemplo.

Criar um botão que parece uma bússola (incluindo animação dinâmica quando a orientação do mapa é alterada), e renderiza-o em outro controle.

![Bússola botão na barra de navegação](mapkit-images/compass-sml.png)

O código a seguir cria um botão bússola e renderiza-o na barra de navegação:

```csharp
var compass = MKCompassButton.FromMapView(MapView);
compass.CompassVisibility = MKFeatureVisibility.Visible;
NavigationItem.RightBarButtonItem = new UIBarButtonItem(compass);
MapView.ShowsCompass = false; // so we don't have two compasses!
```

O `ShowsCompass` propriedade pode ser usada para controlar a visibilidade da bússola padrão dentro da exibição do mapa.

<a name="scale" />

## <a name="scale-view"></a>Exibição de escala

Adicionar a escala em outro lugar no modo de exibição usando o `MKScaleView.FromMapView()` método para obter uma instância da exibição de escala para adicionar em outro lugar na hierarquia do modo de exibição.

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

O botão de controle de usuário centraliza o mapa no local atual do usuário. Use o `MKUserTrackingButton.FromMapView()` método para obter uma instância do botão Aplicar alterações de formatação e adicionar em outro lugar na hierarquia do modo de exibição.

![Botão de local do usuário sobreposto em um mapa](mapkit-images/user-location-sml.png)

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
- [Novidades no MapKit (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/237/)
