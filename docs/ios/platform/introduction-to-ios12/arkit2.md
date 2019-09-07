---
title: ARKit 2 no Xamarin. iOS
description: Este documento descreve as atualizações do ARKit no iOS 12. Ele se concentra no uso de objetos de referência e imagens para detecção, inclui código para texturing ambiental e discute problemas comuns na programação do ARKit.
ms.prod: xamarin
ms.assetid: af758092-1523-4ab7-aa53-c37a81fb156a
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 08/22/2018
ms.openlocfilehash: 36779446a132dc696f28903c3f0b27329bcd4aaf
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70752112"
---
# <a name="arkit-2-in-xamarinios"></a>ARKit 2 no Xamarin. iOS

O ARKit amadureceu consideravelmente desde sua introdução no ano passado no iOS 11. Em primeiro lugar, agora você pode detectar planos verticais e horizontais, o que melhora muito a praticidade de experiências de realidade aumentadas para o interno. Além disso, há novos recursos:

- Reconhecendo imagens e objetos de referência como a junção entre o mundo real e a imagens digitais
- Um novo modo de iluminação que simula iluminação do mundo real
- A capacidade de compartilhar e persistir ambientes de AR
- Um novo formato de arquivo preferencial para armazenar conteúdo de AR

## <a name="recognizing-reference-objects"></a>Reconhecendo objetos de referência

Um recurso de demonstração no ARKit 2 é a capacidade de reconhecer objetos e imagens de referência. As imagens de referência podem ser carregadas a partir de arquivos de imagem normais ([discutidos posteriormente](#more-tracking-configurations)), mas os objetos de referência devem [`ARObjectScanningConfiguration`](xref:ARKit.ARObjectScanningConfiguration)ser verificados, usando o foco do desenvolvedor.

### <a name="sample-app-scanning-and-detecting-3d-objects"></a>Aplicativo de exemplo: Verificando e detectando objetos 3D

O exemplo de [verificação e detecção de objetos 3D](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-scanninganddetecting3dobjects) é uma porta de um [projeto da Apple](https://developer.apple.com/documentation/arkit/scanning_and_detecting_3d_objects?language=objc) que demonstra:

- Gerenciamento de estado do [`NSNotification`](xref:Foundation.NSNotification) aplicativo usando objetos
- Visualização personalizada
- Gestos complexos
- Verificação de objeto
- Armazenando um[`ARReferenceObject`](xref:ARKit.ARReferenceObject)

A verificação de um objeto de referência é a bateria e os dispositivos mais antigos geralmente terão problemas para alcançar o acompanhamento estável.

### <a name="state-management-using-nsnotification-objects"></a>Gerenciamento de estado usando objetos NSNotification

Esse aplicativo usa uma máquina de estado que faz a transição entre os seguintes Estados:

- `AppState.StartARSession`
- `AppState.NotReady`
- `AppState.Scanning`
- `AppState.Testing`

Além disso, o usa um conjunto inserido de Estados e transições `AppState.Scanning`quando em:

- `Scan.ScanState.Ready`
- `Scan.ScanState.DefineBoundingBox`
- `Scan.ScanState.Scanning`
- `Scan.ScanState.AdjustingOrigin`

O aplicativo usa uma arquitetura reativa que posta notificações de transição de estado [`NSNotificationCenter`](xref:Foundation.NSNotificationCenter) para e assina essas notificações. A configuração é semelhante a este trecho `ViewController.cs`de código:

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

Um manipulador de notificação típico atualizará a interface do usuário e possivelmente modificará o estado do aplicativo, como esse manipulador que é atualizado conforme o objeto é verificado:

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

Por fim `Enter{State}` , os métodos modificam o modelo e a UX conforme apropriado para o novo Estado:

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

O aplicativo mostra a "nuvem de ponto" de baixo nível do objeto contido em uma caixa delimitadora projetada em um plano horizontal detectado.

Essa nuvem de ponto está disponível para desenvolvedores na [`ARFrame.RawFeaturePoints`](xref:ARKit.ARFrame.RawFeaturePoints) propriedade. Visualizar a nuvem de ponto com eficiência pode ser um problema complicado. Iterar nos pontos, criar e colocar um novo nó SceneKit para cada ponto eliminaria a taxa de quadros. Como alternativa, se for feito de forma assíncrona, haveria um retardo. O exemplo mantém o desempenho com uma estratégia de três partes:

- Usar código não seguro para fixar os dados no local e interpretar os dados como um buffer bruto de bytes.
- Converter esse buffer bruto em um [`SCNGeometrySource`](xref:SceneKit.SCNGeometrySource) e criar um objeto de " [`SCNGeometryElement`](xref:SceneKit.SCNGeometryElement) modelo".
- Reunir rapidamente os dados brutos e o modelo usando[`SCNGeometry.Create(SCNGeometrySource[], SCNGeometryElement[])`](xref:SceneKit.SCNGeometry.Create(SceneKit.SCNGeometrySource[],SceneKit.SCNGeometryElement[]))

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

O resultado é semelhante a este:

![point_cloud](images/arkit_point_cloud.jpeg)

### <a name="complex-gestures"></a>Gestos complexos

O usuário pode dimensionar, girar e arrastar a caixa delimitadora que circunda o objeto de destino. Há duas coisas interessantes nos reconhecedores de gestos associados.

Primeiro, todos os reconhecedores de gestos são ativados somente depois que um limite é passado; por exemplo, um dedo arrastou muitos pixels ou a rotação excede algum ângulo. A técnica é acumular a movimentação até que o limite seja excedido e, em seguida, aplicá-la de forma incremental:

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

A segunda coisa interessante que está sendo feita em relação a gestos é a maneira como a caixa delimitadora é movida em relação aos planos do mundo real detectados. Esse aspecto é abordado nesta [postagem no blog do Xamarin](https://blog.xamarin.com/exploring-new-ios-12-arkit-capabilities-with-xamarin/).

## <a name="other-new-features-in-arkit-2"></a>Outros novos recursos no ARKit 2

### <a name="more-tracking-configurations"></a>Mais configurações de controle

Agora, você pode usar qualquer um dos seguintes como base para uma experiência de realidade mista:

- Somente o acelerômetro do dispositivo[`AROrientationTrackingConfiguration`](xref:ARKit.AROrientationTrackingConfiguration)(, Ios 11)
- Faces ([`ARFaceTrackingConfiguration`](xref:ARKit.ARFaceTrackingConfiguration), Ios 11)
- Imagens de referência[`ARImageTrackingConfiguration`](xref:ARKit.ARImageTrackingConfiguration)(, IOS 12)
- Verificando objetos 3D[`ARObjectScanningConfiguration`](xref:ARKit.ARObjectScanningConfiguration)(, IOS 12)
- Visual inércia odometry ([`ARWorldTrackingConfiguration`](xref:ARKit.ARWorldTrackingConfiguration), aprimorado no IOS 12)

`AROrientationTrackingConfiguration`, abordado nesta [postagem de F# blog e exemplo](https://github.com/lobrien/FSharp_Face_AR), é o mais limitado e fornece uma experiência de realidade misturada ruim, pois só coloca objetos digitais em relação ao movimento do dispositivo, sem tentar vincular o dispositivo e a tela ao mundo real.

O `ARImageTrackingConfiguration` permite que você reconheça imagens 2D do mundo real (pinturas, logotipos, etc.) e use-as para ancorar a imagens digitais:

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

- É eficiente e pode ser usado com um número potencialmente grande de imagens de referência
- As imagens digitais são ancoradas à imagem, mesmo que essa imagem seja movida no mundo real (por exemplo, se a capa de um livro for reconhecida, ela acompanhará o livro à medida que for retirada da prateleira, contratada, etc.).

O `ARObjectScanningConfiguration` foi discutido [anteriormente](#recognizing-reference-objects) e é uma configuração centrada no desenvolvedor para verificação de objetos 3D. Ele é altamente intensivo de processador e bateria e não deve ser usado em aplicativos de usuário final. O exemplo de [verificação e detecção de objetos 3D](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-scanninganddetecting3dobjects) demonstra o uso dessa configuração.

A configuração de controle final `ARWorldTrackingConfiguration` ,, é a força da maioria das experiências de realidade mista. Essa configuração usa "Visual inércia odometry" para relacionar "pontos de recursos" do mundo real a imagens digitais. A geometria digital ou os sprites são ancorados em relação aos planos horizontais e verticais do mundo real `ARReferenceObject` ou em relação às instâncias detectadas. Nessa configuração, a origem mundial é a posição original da câmera no espaço com o eixo Z alinhado à gravidade e os objetos digitais "permanecem em vigor" em relação aos objetos no mundo real.

### <a name="environmental-texturing"></a>Texturing ambiental

ARKit 2 dá suporte a "ambiente texturing" que usa imagens capturadas para estimar a iluminação e até mesmo aplicar realces especulares a objetos brilhantes. O cubemap ambiental é criado dinamicamente e, depois que a câmera olha em todas as direções, pode produzir uma experiência impressionantemente realista:

![imagem de demonstração do texturing ambiental](images/arkit_env_texturing.png)

Para usar o texturing ambiental:

- Seus [`SCNMaterial`](xref:SceneKit.SCNMaterial) objetos devem usar [`SCNLightingModel.PhysicallyBased`](xref:SceneKit.SCNLightingModel.PhysicallyBased) e atribuir um valor no intervalo de 0 a 1 para [`Metalness.Contents`](xref:SceneKit.SCNMaterial.Metalness) e [`Roughness.Contents`](xref:SceneKit.SCNMaterialProperty.Contents) e
- Sua configuração de rastreamento deve [`EnvironmentTexturing`](xref:ARKit.ARWorldTrackingConfiguration.EnvironmentTexturing) definir  =  [`AREnvironmentTexturing.Automatic`](xref:ARKit.AREnvironmentTexturing.Automatic) :

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

Embora a textura perfeitamente reflexiva mostrada no trecho de código anterior seja divertida em um exemplo, a texturing ambiental provavelmente é melhor usada com o retentor última disparar uma resposta de "vale indefinido" (a textura é apenas uma estimativa com base no que a câmera registrado).

### <a name="shared-and-persistent-ar-experiences"></a>Experiências de AR compartilhadas e persistentes

Outra adição importante ao ARKit 2 é a [`ARWorldMap`](xref:ARKit.ARWorldMap) classe, que permite compartilhar ou armazenar dados de acompanhamento mundial. Você Obtém o mapa do mundo atual [`ARSession.GetCurrentWorldMapAsync`](xref:ARKit.ARSession.GetCurrentWorldMapAsync) com [`GetCurrentWorldMap(Action<ARWorldMap,NSError>)`](xref:ARKit.ARSession.GetCurrentWorldMap(System.Action{ARKit.ARWorldMap,Foundation.NSError})) ou:

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
2. Desarquive-o `ARWorldMap` em um objeto,
3. Use-o como o valor para [`ARWorldTrackingConfiguration.InitialWorldMap`](xref:ARKit.ARWorldTrackingConfiguration.InitialWorldMap) a propriedade:

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

O `ARWorldMap` só contém dados de acompanhamento mundial não visíveis e os [`ARAnchor`](xref:ARKit.ARAnchor) objetos, _não contém ativos_ digitais. Para compartilhar geometria ou imagens, você precisará desenvolver sua própria estratégia apropriada ao seu caso de uso (talvez armazenando/transmitindo apenas o local e a orientação da geometria e aplicando-o a `SCNGeometry` estático ou talvez armazenando/transmitindo objetos serializados). O benefício do `ARWorldMap` é que os ativos, uma vez colocados em relação a `ARAnchor`um compartilhado, aparecerão consistentemente entre os dispositivos ou as sessões.

### <a name="universal-scene-description-file-format"></a>Formato de arquivo de descrição de cena universal

O recurso Headline final da ARKit 2 é a adoção da Apple do formato de arquivo de [Descrição de cena universal](https://graphics.pixar.com/usd/docs/Introduction-to-USD.html) da Pixar. Esse formato substitui o formato do DAE do arquivo COLLADA como o formato preferencial para compartilhar e armazenar ativos do ARKit. O suporte para visualização de ativos é criado no iOS 12 e no Mojave. A extensão de arquivo USDZ é um arquivo zip descompactado e não criptografado que contém USD arquivos. O Pixar [fornece ferramentas para trabalhar com USD arquivos](https://graphics.pixar.com/usd/docs/USD-Toolset.html#USDToolset-usdedit) , mas ainda não há muito suporte de terceiros.

## <a name="arkit-programming-tips"></a>Dicas de programação do ARKit

### <a name="manual-resource-management"></a>Gerenciamento manual de recursos

No ARKit, é crucial gerenciar manualmente os recursos. Isso não só permite altas taxas de quadros, na verdade é _necessário_ evitar um "congelamento de tela confuso". A estrutura ARKit é lenta ao fornecer um novo quadro de câmera ([`ARSession.CurrentFrame`](xref:ARKit.ARSession.CurrentFrame). Até que o [`ARFrame`](xref:ARKit.ARFrame) atual tenha `Dispose()` sido chamado, ARKit não fornecerá um novo quadro! Isso faz com que o vídeo seja "congelado" mesmo que o restante do aplicativo responda. A solução é sempre acessar `ARSession.CurrentFrame` um `using` bloco ou chamá `Dispose()` -lo manualmente.

Todos os objetos derivados `NSObject` de `IDisposable` são `NSObject` e implementam o [padrão Dispose](https://docs.microsoft.com/dotnet/standard/design-guidelines/dispose-pattern), portanto, você normalmente deve seguir [esse `Dispose` padrão para implementação em uma classe derivada](https://docs.microsoft.com/dotnet/standard/garbage-collection/implementing-dispose).

### <a name="manipulating-transform-matrices"></a>Manipulando matrizes de transformação

Em qualquer aplicativo 3D, você vai lidar com as matrizes de transformação 4x4 que descrevem compactmente como mover, girar e distorcer um objeto por meio de espaço 3D. No SceneKit, esses são [`SCNMatrix4`](xref:SceneKit.SCNMatrix4) objetos.  

A [`SCNNode.Transform`](xref:SceneKit.SCNNode.Transform) propriedade retorna a `SCNMatrix4` matriz de transformação para [`SCNNode`](xref:SceneKit.SCNNode) o _conforme apoiado pelo_ tipo de linha `simdfloat4x4` principal. Portanto, por exemplo:

```csharp
var node = new SCNNode { Position = new SCNVector3(2, 3, 4) };  
var xform = node.Transform;
Console.WriteLine(xform);
// Output is: "(1, 0, 0, 0)\n(0, 1, 0, 0)\n(0, 0, 1, 0)\n(2, 3, 4, 1)"
```  

Como você pode ver, a posição é codificada nos três primeiros elementos da linha inferior.

No Xamarin, o tipo comum para manipular matrizes de transformação `NVector4`é, que por convenção é interpretada de forma principal de coluna. Isso significa que o componente de conversão/posição é esperado em M14, M24, M34, não M41, M42, M43:

![linha-principal vs coluna-principal](images/arkit_row_vs_column.png)

Ser consistente com a escolha da interpretação de matriz é vital para o comportamento adequado. Como as matrizes de transformação 3D são 4x4, os erros de consistência não produzirão nenhum tipo de tempo de compilação ou até mesmo de uma exceção de tempo de execução – é apenas que as operações agirão inesperadamente. Se seus objetos SceneKit/ARKit parecerem estar presos, desaparecer ou tremular, uma matriz de transformação incorreta será uma boa possibilidade. A solução é simples: [`NMatrix4.Transpose`](xref:OpenTK.NMatrix4.Transpose*) executará um transposição no local de elementos.

## <a name="related-links"></a>Links relacionados

- [Aplicativo de exemplo – verificando e detectando objetos 3D](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-scanninganddetecting3dobjects)
- [O que há de novo no ARKit 2 (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/602/)
- [Entendendo o rastreamento e a detecção de ARKit (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/610/)
- [Introdução ao ARKit no Xamarin. iOS](https://docs.microsoft.com/xamarin/ios/platform/introduction-to-ios11/arkit/)
