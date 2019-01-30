---
title: ARKit 2 no xamarin. IOS
description: Este documento descreve as atualizações para ARKit no iOS 12. Se concentra no uso de imagens e objetos de referência para a detecção, inclui código para texturização ambientais e aborda problemas comuns na programação ARKit.
ms.prod: xamarin
ms.assetid: af758092-1523-4ab7-aa53-c37a81fb156a
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/22/2018
ms.openlocfilehash: 7f3c196eafd71e8571ea49a17784e5290e7ef44e
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233595"
---
# <a name="arkit-2-in-xamarinios"></a>ARKit 2 no xamarin. IOS

ARKit transformou consideravelmente desde sua introdução no ano passado no iOS 11. Primeiramente, você agora pode detectar vertical, bem como planos horizontais, que melhora significativamente a viabilidade de experiências de realidade aumentada interno. Além disso, há novos recursos:

* Reconhecimento de imagens de referência e objetos como a junção entre o mundo real e imagens digitais
* Um novo modo de iluminação que simula a iluminação do mundo real
* A capacidade de compartilhar e manter ambientes de AR
* Um novo formato preferido para armazenar o conteúdo de AR

## <a name="recognizing-reference-objects"></a>Reconhecendo os objetos de referência

Um recurso de showcase ARKit 2 é a capacidade de reconhecer imagens de referência e objetos. Imagens de referência podem ser carregadas de arquivos de imagem normal ([discutido posteriormente](#more-tracking-configurations)), mas objetos devem ser verificados, usando o desenvolvimento de referência [ `ARObjectScanningConfiguration` ](xref:ARKit.ARObjectScanningConfiguration).

### <a name="sample-app-scanning-and-detecting-3d-objects"></a>Aplicativo de exemplo: Verificação e detecção de objetos 3D

O [varredura e detecção de objetos 3D](https://developer.xamarin.com/samples/monotouch/ios12/ScanningAndDetecting3DObjects/) amostra é uma porta de uma [projeto Apple](https://developer.apple.com/documentation/arkit/scanning_and_detecting_3d_objects?language=objc) que demonstra:

* Gerenciamento de estado de aplicativo usando [ `NSNotification` ](xref:Foundation.NSNotification) objetos
* Visualização personalizada
* Gestos complexos
* Verificação de objeto
* Armazenando um [`ARReferenceObject`](xref:ARKit.ARReferenceObject)

A verificação de um objeto de referência é da bateria e intenso e dispositivos mais antigos serão geralmente têm problemas para atingir o acompanhamento estável.

### <a name="state-management-using-nsnotification-objects"></a>Gerenciamento de estado usando objetos NSNotification

Esse aplicativo usa uma máquina de estado faz a transição entre estados a seguir:

* `AppState.StartARSession`
* `AppState.NotReady`
* `AppState.Scanning`
* `AppState.Testing`

Além disso usa um conjunto incorporado de estados e transições quando estiver no `AppState.Scanning`:

* `Scan.ScanState.Ready`
* `Scan.ScanState.DefineBoundingBox`
* `Scan.ScanState.Scanning`
* `Scan.ScanState.AdjustingOrigin`

O aplicativo usa uma arquitetura reativa que envia notificações de transição de estado para [ `NSNotificationCenter` ](xref:Foundation.NSNotificationCenter) e assina essas notificações. A configuração se parece com este trecho de `ViewController.cs`:

```csharp
// Configure notifications for application state changes
var notificationCenter = NSNotificationCenter.DefaultCenter;

notificationCenter.AddObserver(Scan.ScanningStateChangedNotificationName, State.ScanningStateChanged);
notificationCenter.AddObserver(ScannedObject.GhostBoundingBoxCreatedNotificationName, State.GhostBoundingBoxWasCreated);
notificationCenter.AddObserver(ScannedObject.GhostBoundingBoxRemovedNotificationName, State.GhostBoundingBoxWasRemoved);
notificationCenter.AddObserver(ScannedObject.BoundingBoxCreatedNotificationName, State.BoundingBoxWasCreated);
notificationCenter.AddObserver(BoundingBox.ScanPercentageChangedNotificationName, ScanPercentageChanged);
notificationCenter.AddObserver(BoundingBox.ExtentChangedNotificationName, BoundingBoxExtentChanged);
notificationCenter.AddObserver(BoundingBox.PositionChangedNotificationName, BoundingBoxPositionChanged);
notificationCenter.AddObserver(ObjectOrigin.PositionChangedNotificationName, ObjectOriginPositionChanged);
notificationCenter.AddObserver(NSProcessInfo.PowerStateDidChangeNotification, DisplayWarningIfInLowPowerMode);

```

Um manipulador de notificação típico atualizará a interface do usuário e possivelmente modificar o estado do aplicativo, como esse manipulador de atualizações em que o objeto é verificado:

```csharp
private void ScanPercentageChanged(NSNotification notification)
{
    var pctNum = TryGet<NSNumber>(notification.UserInfo, BoundingBox.ScanPercentageUserKey);
    if (pctNum == null)
    {
        return;
    }
    double percentage = pctNum.DoubleValue;
    // Switch to the next state if scan is complete
    if (percentage >= 100.0)
    {
        State.SwitchToNextState();
    }
    else
    {
        DispatchQueue.MainQueue.DispatchAsync(() => navigationBarController.SetNavigationBarTitle($"Scan ({percentage})"));
    }
}

```

Por fim, `Enter{State}` métodos de modificam o modelo e a experiência do usuário conforme apropriado para o novo estado:

```csharp
internal void EnterStateTesting()
{
    navigationBarController.SetNavigationBarTitle("Testing");
    navigationBarController.ShowBackButton(false);
    loadModelButton.Hidden = true;
    flashlightButton.Hidden = false;
    nextButton.Enabled = true;
    nextButton.SetTitle("Share", UIControlState.Normal);

    testRun = new TestRun(sessionInfo, sceneView);
    TestObjectDetection();
    CancelMaxScanTimeTimer();
}
```

### <a name="custom-visualization"></a>Visualização personalizada

Mostra o aplicativo de baixo nível "ponto" nuvem do objeto contido em uma caixa delimitadora projetado em um plano horizontal detectado.

Esse ponto de nuvem está disponível para desenvolvedores na [ `ARFrame.RawFeaturePoints` ](xref:ARKit.ARFrame.RawFeaturePoints) propriedade. Visualizando a nuvem do ponto de maneira eficiente pode ser um problema difícil. Iterando sobre os pontos, em seguida, criar e colocar um novo nó de SceneKit para cada ponto de seriam eliminar a taxa de quadros. Como alternativa, se feito de forma assíncrona, deve haver um atraso. O exemplo mantém o desempenho com uma estratégia de três partes:

* Usando código não seguro para fixar os dados em colocar e interpretam os dados como um buffer bruto de bytes.
* Convertendo o buffer bruto em um [ `SCNGeometrySource` ](xref:SceneKit.SCNGeometrySource) e a criação de um "modelo" [ `SCNGeometryElement` ](xref:SceneKit.SCNGeometryElement) objeto.
* Rapidamente "unir" os dados brutos e o modelo usando [`SCNGeometry.Create(SCNGeometrySource[], SCNGeometryElement[])`](xref:SceneKit.SCNGeometry.Create(SceneKit.SCNGeometrySource[],SceneKit.SCNGeometryElement[]))

```csharp
internal static SCNGeometry CreateVisualization(NVector3[] points, UIColor color, float size)
{
  if (points.Length == 0)
  {
    return null;
  }

  unsafe
  {
    var stride = sizeof(float) * 3;

    // Pin the data down so that it doesn't move
    fixed (NVector3* pPoints = &amp;points[0])
    {
      // Important: Don't unpin until after `SCNGeometry.Create`, because geometry creation is lazy

      // Grab a pointer to the data and treat it as a byte buffer of the appropriate length
      var intPtr = new IntPtr(pPoints);
      var pointData = NSData.FromBytes(intPtr, (System.nuint) (stride * points.Length));

      // Create a geometry source (factory) configured properly for the data (3 vertices)
      var source = SCNGeometrySource.FromData(
        pointData,
        SCNGeometrySourceSemantics.Vertex,
        points.Length,
        true,
        3,
        sizeof(float),
        0,
        stride
      );

      // Create geometry element
      // The null and bytesPerElement = 0 look odd, but this is just a template object
      var template = SCNGeometryElement.FromData(null, SCNGeometryPrimitiveType.Point, points.Length, 0);
      template.PointSize = 0.001F;
      template.MinimumPointScreenSpaceRadius = size;
      template.MaximumPointScreenSpaceRadius = size;

      // Stitch the data (source) together with the template to create the new object
      var pointsGeometry = SCNGeometry.Create(new[] { source }, new[] { template });
      pointsGeometry.Materials = new[] { Utilities.Material(color) };
      return pointsGeometry;
    }
  }
}
```

O resultado terá esta aparência:

![point_cloud](images/arkit_point_cloud.jpeg)

### <a name="complex-gestures"></a>Gestos complexos

O usuário pode dimensionar, girar e arraste a caixa delimitadora que circunda o objeto de destino. Há duas coisas interessantes no reconhecedores gesto associado.

Primeiro, todos os reconhecedores de gestos ativar somente depois que um limite foi passado; Por exemplo, um dedo arrastou tantas pixels ou a rotação excede algumas ângulo. A técnica é para acumular a movimentação até que o limite foi excedido e, em seguida, aplicá-lo de forma incremental:

```csharp
// A custom rotation gesture recognizer that fires only when a threshold is passed
internal partial class ThresholdRotationGestureRecognizer : UIRotationGestureRecognizer
{
    // The threshold after which this gesture is detected.
    const double threshold = Math.PI / 15; // (12°)

    // Indicates whether the currently active gesture has exceeded the threshold
    private bool thresholdExceeded = false;

    private double previousRotation = 0;
    internal double RotationDelta { get; private set; }

    internal ThresholdRotationGestureRecognizer(IntPtr handle) : base(handle)
    {
    }

    // Observe when the gesture's state changes to reset the threshold
    public override UIGestureRecognizerState State
    {
        get => base.State;
        set
        {
            base.State = value;

            switch(value)
            {
                case UIGestureRecognizerState.Began :
                case UIGestureRecognizerState.Changed :
                    break;
                default :
                    // Reset threshold check
                    thresholdExceeded = false;
                    previousRotation = 0;
                    RotationDelta = 0;
                    break;
            }
        }
    }

    public override void TouchesMoved(NSSet touches, UIEvent evt)
    {
        base.TouchesMoved(touches, evt);

        if (thresholdExceeded)
        {
            RotationDelta = Rotation - previousRotation;
            previousRotation = Rotation;
        }

        if (! thresholdExceeded && Math.Abs(Rotation) > threshold)
        {
            thresholdExceeded = true;
            previousRotation = Rotation;
        }
    }
}
```

A segunda coisa interessante que está sendo feita em relação a gestos é a maneira que a caixa delimitadora é movida em relação ao detectado planos do mundo real. Esse aspecto é discutido [esta postagem de blog do Xamarin](https://blog.xamarin.com/exploring-new-ios-12-arkit-capabilities-with-xamarin/).

## <a name="other-new-features-in-arkit-2"></a>Outros novos recursos no ARKit 2

### <a name="more-tracking-configurations"></a>Mais configurações de rastreamento

Agora, você pode usar qualquer um dos seguintes como a base para uma experiência de realidade misturada:

* Somente o acelerômetro do dispositivo ([`AROrientationTrackingConfiguration`](xref:ARKit.AROrientationTrackingConfiguration), iOS 11)
* Faces ([`ARFaceTrackingConfiguration`](xref:ARKit.ARFaceTrackingConfiguration), iOS 11)
* Imagens de referência ([`ARImageTrackingConfiguration`](xref:ARKit.ARImageTrackingConfiguration), iOS 12)
* Verificação de objetos 3D ([`ARObjectScanningConfiguration`](xref:ARKit.ARObjectScanningConfiguration), iOS 12)
* Odometry inercial Visual ([`ARWorldTrackingConfiguration`](xref:ARKit.ARWorldTrackingConfiguration)aprimorado no iOS 12)

`AROrientationTrackingConfiguration`, discutidos [esta postagem de blog e F# amostra](https://github.com/lobrien/FSharp_Face_AR), é mais limitado e fornece uma experiência de realidade misturada ruim, pois apenas coloca objetos digitais em relação ao movimento do dispositivo, sem tentar ligar o dispositivo e de tela em o mundo real.

O `ARImageTrackingConfiguration` permite que você reconhecer imagens 2D do mundo real (pinturas, logotipos, etc.) e usá-los para imagens digitais de âncora:

```csharp
var imagesAndWidths = new[] {
    ("cover1.jpg", 0.185F),
    ("cover2.jpg", 0.185F),
     //...etc...
    ("cover100.jpg", 0.185F),
};

var referenceImages = new NSSet<ARReferenceImage>(
    imagesAndWidths.Select( imageAndWidth =>
    {
      // Tuples cannot be destructured in lambda arguments
        var (image, width) = imageAndWidth;
        // Read the image
        var img = UIImage.FromFile(image).CGImage;
        return new ARReferenceImage(img, ImageIO.CGImagePropertyOrientation.Up, width);
    }).ToArray());

configuration.TrackingImages = referenceImages;
```

Há dois aspectos interessantes para essa configuração:

* É eficiente e pode ser usado com um número potencialmente grande de imagens de referência
* As imagens digitais está ancorada à imagem, mesmo se essa imagem se move no mundo real (por exemplo, se a tampa de um livro for reconhecida, ele irá controlar o livro conforme ele é extraído prontos para uso, apresentados para baixo, etc.).

O `ARObjectScanningConfiguration` foi discutido [anteriormente](#recognizing-reference-objects) e uma configuração centrado no desenvolvedor para a digitalização de objetos 3D. É altamente processador e com uso intensivo de bateria e não deve ser usado em aplicativos de usuário final. O exemplo [varredura e detecção de objetos 3D](https://developer.xamarin.com/samples/monotouch/ios12/ScanningAndDetecting3DObjects/) demonstra o uso dessa configuração.

A configuração de controle final, `ARWorldTrackingConfiguration` , é a força da maioria das experiências de realidade misturada. Essa configuração usa "visual odometry inercial" para relacionar reais "pontos de recurso" para imagens digitais. Geometria digital ou sprites são ancorados em relação a planos de horizontais e verticais do mundo real ou relativo ao detectado `ARReferenceObject` instâncias. Nessa configuração, a origem do mundo é a posição original da câmera no espaço com o eixo z alinhado à gravidade e objetos digitais "continuam no lugar" em relação a objetos no mundo real.

### <a name="environmental-texturing"></a>A texturização ambiental

ARKit 2 dá suporte a "ambientais texturização" que usa imagens capturadas para estimar a iluminação e até mesmo aplicar realces especulares para objetos brilhantes. Mapa de cubo ambiental é criado dinamicamente e, depois que a câmera já terá analisado em todas as direções, pode produzir uma experiência impressionantemente realista:

![imagem de demonstração texturização ambiental](images/arkit_env_texturing.png)

Para usar a texturização ambientais:

* Sua [ `SCNMaterial` ](xref:SceneKit.SCNMaterial) objetos devem usar [ `SCNLightingModel.PhysicallyBased` ](xref:SceneKit.SCNLightingModel.PhysicallyBased) e atribua um valor no intervalo de 0 a 1 para [ `Metalness.Contents` ](xref:SceneKit.SCNMaterial.Metalness) e [ `Roughness.Contents` ](xref:SceneKit.SCNMaterialProperty.Contents) e
* A configuração de rastreamento deve ser definido [ `EnvironmentTexturing` ](xref:ARKit.ARWorldTrackingConfiguration.EnvironmentTexturing)  =  [AREnvironmentTexturing.Automatic'](xref:ARKit.AREnvironmentTexturing.Automatic) :

```csharp
var sphere = SCNSphere.Create(0.33F);
sphere.FirstMaterial.LightingModelName = SCNLightingModel.PhysicallyBased;
// Shiny metallic sphere
sphere.FirstMaterial.Metalness.Contents = new NSNumber(1.0F);
sphere.FirstMaterial.Roughness.Contents = new NSNumber(0.0F);

// Session configuration:
var configuration = new ARWorldTrackingConfiguration
{
    PlaneDetection = ARPlaneDetection.Horizontal | ARPlaneDetection.Vertical,
    LightEstimationEnabled = true,
    EnvironmentTexturing = AREnvironmentTexturing.Automatic
};
```

Embora a textura perfeitamente refletiva mostrada no trecho de código anterior é divertido em uma amostra, é provavelmente melhor texturização ambientais usado com moderação deixa que ela dispare uma resposta de "predisposição silício" (a textura é apenas uma estimativa com base no qual a câmera gravada).


### <a name="shared-and-persistent-ar-experiences"></a>Experiências de AR compartilhadas e persistentes

Outra grande adição como ARKit 2 é o [ `ARWorldMap` ](xref:ARKit.ARWorldMap) classe, que permite que você compartilhe ou armazenar dados de acompanhamento do mundo. Obter o mapa-múndi atual com [ `ARSession.GetCurrentWorldMapAsync` ](xref:ARKit.ARSession.GetCurrentWorldMapAsync) ou [ `GetCurrentWorldMap(Action<ARWorldMap,NSError>` ](xref:ARKit.ARSession.GetCurrentWorldMap(System.Action{ARKit.ARWorldMap,Foundation.NSError})) :

```csharp
// Local storage
var PersistentWorldPath => Environment.GetFolderPath(Environment.SpecialFolder.Personal) + "/arworldmap";

// Later, after scanning the environment thoroughly...
var worldMap = await Session.GetCurrentWorldMapAsync();
if (worldMap != null)
{
    var data = NSKeyedArchiver.ArchivedDataWithRootObject(worldMap, true, out var err);
    if (err != null)
    {
        Console.WriteLine(err);
    }
    File.WriteAllBytes(PersistentWorldPath, data.ToArray());
}
```

Para compartilhar ou restaurar o mapa mundial:

1. Carregar os dados do arquivo,
2. Desarquivará-lo em um `ARWorldMap` objeto,
3. Usá-lo como o valor para o [ `ARWorldTrackingConfiguration.InitialWorldMap` ](xref:ARKit.ARWorldTrackingConfiguration.InitialWorldMap) propriedade:

```csharp
var data = NSData.FromArray(File.ReadAllBytes(PersistentWorldController.PersistenWorldPath));
var worldMap = (ARWorldMap)NSKeyedUnarchiver.GetUnarchivedObject(typeof(ARWorldMap), data, out var err);

var configuration = new ARWorldTrackingConfiguration
{
    PlaneDetection = ARPlaneDetection.Horizontal | ARPlaneDetection.Vertical,
    LightEstimationEnabled = true,
    EnvironmentTexturing = AREnvironmentTexturing.Automatic,
    InitialWorldMap = worldMap
};
```

O `ARWorldMap` contém apenas dados de acompanhamento de mundo não visíveis e o [ `ARAnchor` ](xref:ARKit.ARAnchor) objetos, ele faz _não_ contêm os ativos digitais. Para compartilhar a geometria ou imagens, você precisará desenvolver sua própria estratégia apropriada para seu caso de uso (talvez por armazenar/transmitir apenas o local e a orientação da geometria e aplicá-lo como estático `SCNGeometry` ou talvez por armazenar/transmitir objetos serializados). A vantagem dos `ARWorldMap` é que os ativos, uma vez colocado em relação a um compartilhado `ARAnchor`, aparecerá consistentemente entre dispositivos ou sessões.

### <a name="universal-scene-description-file-format"></a>Formato de arquivo de descrição de cena universal

O recurso de headline final do ARKit 2 é a adoção da Apple do Pixar [Universal Description cena](https://graphics.pixar.com/usd/docs/Introduction-to-USD.html) formato de arquivo. Esse formato substitui o formato do Collada DAE como o formato preferencial para compartilhamento e armazenamento ARKit ativos. Suporte para a visualização de ativos é incorporado no iOS 12 e Mojave. A extensão de arquivo USDZ é um arquivo zip descompactados e não criptografado que contém os arquivos USD. Pixar [fornece ferramentas para trabalhar com arquivos USD](https://graphics.pixar.com/usd/docs/USD-Toolset.html#USDToolset-usdedit) , mas ainda não há suporte de terceiros muito.

## <a name="arkit-programming-tips"></a>Dicas de programação ARKit

### <a name="manual-resource-management"></a>Gerenciamento de recurso manual

Em ARKit, é crucial gerenciar os recursos manualmente. Não apenas isso permite altas taxas de quadro, é realmente _necessário_ para evitar uma confusão "congelamento de tela". A estrutura de ARKit é lenta sobre o fornecimento de um novo quadro de câmera ([`ARSession.CurrentFrame`](xref:ARKit.ARSession.CurrentFrame). Até o atual [ `ARFrame` ](xref:ARKit.ARFrame) teve `Dispose()` chamado nela, ARKit não fornecer um novo quadro! Isso faz com que o vídeo "congelar", mesmo que o resto do aplicativo está respondendo. A solução é sempre acessar `ARSession.CurrentFrame` com um `using` bloquear ou chamar manualmente `Dispose()` nele.

Todos os objetos derivados de `NSObject` estão `IDisposable` e `NSObject` implementa o [padrão de descarte](https://docs.microsoft.com/dotnet/standard/design-guidelines/dispose-pattern), portanto, você normalmente deve seguir [esse padrão para a implementação de `Dispose` em um derivada classe](https://docs.microsoft.com/dotnet/standard/garbage-collection/implementing-dispose).

### <a name="manipulating-transform-matrices"></a>Manipulação de matrizes de transformação

Em qualquer aplicativo 3D, você vai estar lidando com matrizes de transformação de 4 x 4 que forma compacta descrevem como mover, girar e distorcer um objeto por meio do espaço 3D. SceneKit, existem [ `SCNMatrix4` ](xref:SceneKit.SCNMatrix4) objetos.  

O [ `SCNNode.Transform` ](xref:SceneKit.SCNNode.Transform) propriedade retorna o `SCNMatrix4` matriz de transformação para o [ `SCNNode` ](xref:SceneKit.SCNNode) _como apoio_ linhas principais `simdfloat4x4` tipo. Portanto, por exemplo:

```csharp
var node = new SCNNode { Position = new SCNVector3(2, 3, 4) };  
var xform = node.Transform;
Console.WriteLine(xform);
// Output is: "(1, 0, 0, 0)\n(0, 1, 0, 0)\n(0, 0, 1, 0)\n(2, 3, 4, 1)"
```  

Como você pode ver, a posição está codificada em três primeiros elementos da linha inferior.

No Xamarin, o tipo comum para manipular matrizes de transformação é `NVector4`, que, por convenção é interpretado de forma coluna principal. Ou seja, o componente de conversão/posição é esperado no M14, M24, M34, não M41, M42, M43:

![linhas principais vs coluna principal](images/arkit_row_vs_column.png)

Seja consistente com a opção de interpretação de matriz é vital para o comportamento apropriado. Como matrizes de transformação 3D são 4 x 4, erros de consistência não produzirá nenhum tipo de exceção de tempo de compilação ou tempo de execução até mesmo — ele é apenas que as operações serão agir inesperadamente. Se seu SceneKit / ARKit objetos parecem estar preso, voar distância ou tremulação, uma matriz de transformação incorreta é uma boa possibilidade. A solução é simples: [ `NMatrix4.Transpose` ](https://developer.xamarin.com/api/member/OpenTK.NMatrix4.Transpose) executará uma transposição no local de elementos.

## <a name="related-links"></a>Links relacionados

- [Aplicativo de exemplo – varredura e detecção de objetos 3D](https://developer.xamarin.com/samples/monotouch/iOS12/ScanningAndDetecting3DObjects/)
- [O que há de novo no ARKit 2 (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/602/)
- [ARKit Noções básicas sobre acompanhamento e detecção (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/610/)
- [Introdução ao ARKit no xamarin. IOS](https://docs.microsoft.com/xamarin/ios/platform/introduction-to-ios11/arkit/)
