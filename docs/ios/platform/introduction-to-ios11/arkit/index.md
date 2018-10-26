---
title: Introdução ao ARKit no xamarin. IOS
description: Este documento descreve a realidade aumentada no iOS 11 com ARKit. Ele discute como adicionar um modelo 3D a um aplicativo, configure o modo de exibição, implementar um representante de sessão, posicione o modelo 3D no mundo e pausar a sessão de realidade aumentada.
ms.prod: xamarin
ms.assetid: 70291430-BCC1-445F-9D41-6FBABE87078E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/30/2017
ms.openlocfilehash: 348d2f2090105ed693da7be5a44c82ef18bd2a89
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119729"
---
# <a name="introduction-to-arkit-in-xamarinios"></a>Introdução ao ARKit no xamarin. IOS

_Realidade aumentada para iOS 11_

ARKit permite uma ampla variedade de jogos e aplicativos de realidade aumentada. Esta seção abrange os seguintes tópicos:

- [Introdução ao ARKit](#gettingstarted)
- [Usando o ARKit com UrhoSharp](urhosharp.md)

<a name="gettingstarted" />

## <a name="getting-started-with-arkit"></a>Introdução ao ARKit

Para obter uma introdução realidade aumentada, as instruções a seguir orientam através de um aplicativo simple: um modelo 3D de posicionamento e permitindo que o ARKit manter o modelo em vigor com sua funcionalidade de rastreamento.

![Flutuante da câmera de imagem de modelo 3D do Jet](images/jet-sml.png)

### <a name="1-add-a-3d-model"></a>1. Adicionar um modelo 3D

Ativos devem ser adicionados ao projeto com o **SceneKitAsset** ação de build.

![Em um projeto de ativos SceneKit](images/scene-assets.png)


### <a name="2-configure-the-view"></a>2. Configurar o modo de exibição

No controlador de exibição `ViewDidLoad` método, carregar o ativo de cena e definir o `Scene` propriedade no modo de exibição:

```csharp
ARSCNView SceneView = (View as ARSCNView);

// Create a new scene
var scene = SCNScene.FromFile("art.scnassets/ship");

// Set the scene to the view
SceneView.Scene = scene;
```

### <a name="3-optionally-implement-a-session-delegate"></a>3. Opcionalmente, implemente um delegado de sessão

Embora não seja necessário para casos simples, a implementação de um delegado de sessão pode ser útil para depurar o estado da sessão ARKit (e em aplicativos reais, fornecer comentários ao usuário). Crie um delegado simple usando o código a seguir:

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

Atribuir o delegado nas no `ViewDidLoad` método:

```csharp
// Track changes to the session
SceneView.Session.Delegate = new SessionDelegate();
```

### <a name="4-position-the-3d-model-in-the-world"></a>4. Posicione o modelo 3D no mundo

No `ViewWillAppear`, o código a seguir estabelece uma sessão de ARKit e define a posição do modelo 3D no espaço em relação a câmera do dispositivo:

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

Cada vez que o aplicativo é executado ou retomado, o modelo 3D será posicionado na frente da câmera. Depois que o modelo está posicionado, mover a câmera e observe como o ARKit mantém o modelo posicionado.

### <a name="5-pause-the-augmented-reality-session"></a>5. Pausar a sessão de realidade aumentada

Ele é uma boa prática para pausar a sessão ARKit quando o controlador de exibição não está visível (no `ViewWillDisappear` método:

```csharp
SceneView.Session.Pause();
```

## <a name="summary"></a>Resumo

O código acima resulta em um aplicativo ARKit simples. Exemplos mais complexos esperaria que o controlador de exibição hospedando a sessão de realidade aumentada para implementar `IARSCNViewDelegate`, e métodos adicionais ser implementado.

ARKit fornece muitos dos recursos mais sofisticados, como acompanhamento de superfície e interação do usuário. Consulte a [UrhoSharp demonstração](urhosharp.md) para obter um exemplo combinando ARKit com UrhoSharp de acompanhamento.


## <a name="related-links"></a>Links relacionados

- [Realidade aumentada (Apple)](https://developer.apple.com/arkit/)
- [Usando o ARKit com UrhoSharp](urhosharp.md)
- [Exemplo simples ARKit (Jet)](https://developer.xamarin.com/samples/monotouch/ios11/ARKitSample/)
- [ARKit colocando objetos (amostra)](https://developer.xamarin.com/samples/monotouch/ios11/ARKitPlacingObjects/)
- [Apresentando o ARKit - realidade aumentada para iOS (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/602/)
