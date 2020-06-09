---
title: Introdução ao ARKit no Xamarin. iOS
description: Este documento descreve a realidade aumentada no iOS 11 com ARKit. Ele aborda como adicionar um modelo 3D a um aplicativo, configurar a exibição, implementar um delegado de sessão, posicionar o modelo 3D no mundo e pausar a sessão de realidade aumentada.
ms.prod: xamarin
ms.assetid: 70291430-BCC1-445F-9D41-6FBABE87078E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/30/2017
ms.openlocfilehash: 51b28ec05af91dea21b1291956de30c549b1868e
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571669"
---
# <a name="introduction-to-arkit-in-xamarinios"></a>Introdução ao ARKit no Xamarin. iOS

_Realidade aumentada para o iOS 11_

O ARKit permite uma ampla variedade de aplicativos e jogos de realidade aumentada. Esta seção contém os seguintes tópicos:

- [Introdução com ARKit](#gettingstarted)
- [Usando ARKit com UrhoSharp](urhosharp.md)

<a name="gettingstarted"></a>

## <a name="getting-started-with-arkit"></a>Introdução com ARKit

Para começar a usar a realidade aumentada, as instruções a seguir percorrem um aplicativo simples: posicionando um modelo 3D e permitindo que ARKit Mantenha o modelo em vigor com sua funcionalidade de controle.

![Modelo Jet 3D flutuante na imagem da câmera](images/jet-sml.png)

### <a name="1-add-a-3d-model"></a>1. adicionar um modelo 3D

Os ativos devem ser adicionados ao projeto com a ação de compilação **SceneKitAsset** .

![Ativos do SceneKit em um projeto](images/scene-assets.png)

### <a name="2-configure-the-view"></a>2. configurar o modo de exibição

No método do controlador de exibição `ViewDidLoad` , carregue o ativo de cena e defina a `Scene` Propriedade na exibição:

```csharp
ARSCNView SceneView = (View as ARSCNView);

// Create a new scene
var scene = SCNScene.FromFile("art.scnassets/ship");

// Set the scene to the view
SceneView.Scene = scene;
```

### <a name="3-optionally-implement-a-session-delegate"></a>3. como opção, implemente um delegado de sessão

Embora não seja necessário para casos simples, implementar um delegado de sessão pode ser útil para depurar o estado da sessão ARKit (e em aplicativos reais, fornecendo comentários para o usuário). Crie um delegado simples usando o código abaixo:

```csharp
public class SessionDelegate : ARSessionDelegate
{
  public SessionDelegate() {}
  public override void CameraDidChangeTrackingState(ARSession session, ARCamera camera)
  {
    Console.WriteLine("{0} {1}", camera.TrackingState, camera.TrackingStateReason);
  }
}
```

Atribua o delegado no no `ViewDidLoad` método:

```csharp
// Track changes to the session
SceneView.Session.Delegate = new SessionDelegate();
```

### <a name="4-position-the-3d-model-in-the-world"></a>4. posicionar o modelo 3D no mundo

No `ViewWillAppear` , o código a seguir estabelece uma sessão ARKit e define a posição do modelo 3D no espaço em relação à câmera do dispositivo:

```csharp
// Create a session configuration
var configuration = new ARWorldTrackingConfiguration {
  PlaneDetection = ARPlaneDetection.Horizontal,
  LightEstimationEnabled = true
};

// Run the view's session
SceneView.Session.Run(configuration, ARSessionRunOptions.ResetTracking);

// Find the ship and position it just in front of the camera
var ship = SceneView.Scene.RootNode.FindChildNode("ship", true);

ship.Position = new SCNVector3(2f, -2f, -9f);
```

Cada vez que o aplicativo é executado ou retomado, o modelo 3D será posicionado na frente da câmera. Depois que o modelo estiver posicionado, mova a câmera e veja como ARKit mantém o modelo posicionado.

### <a name="5-pause-the-augmented-reality-session"></a>5. pausar a sessão de realidade aumentada

É uma boa prática pausar a sessão ARKit quando o controlador de exibição não estiver visível (no `ViewWillDisappear` método:

```csharp
SceneView.Session.Pause();
```

## <a name="summary"></a>Resumo

O código acima resulta em um aplicativo ARKit simples. Exemplos mais complexos esperam que o controlador de exibição hospede a sessão de realidade aumentada para implementar `IARSCNViewDelegate` e métodos adicionais sejam implementados.

O ARKit fornece muitos recursos mais sofisticados, como o controle de superfície e a interação do usuário. Consulte a [demonstração de UrhoSharp](urhosharp.md) para obter um exemplo de combinação de rastreamento de ARKit com UrhoSharp.

## <a name="related-links"></a>Links relacionados

- [Realidade aumentada (Apple)](https://developer.apple.com/arkit/)
- [Usando ARKit com UrhoSharp](urhosharp.md)
- [Exemplo simples de ARKit (Jet)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-arkitsample)
- [ARKit colocando objetos (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-arkitplacingobjects)
- [Introdução à realidade ARKitda para iOS (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/602/)
