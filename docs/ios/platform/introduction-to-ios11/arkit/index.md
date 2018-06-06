---
title: Introdução ao ARKit em xamarin
description: Este documento descreve a realidade aumentada no iOS 11 com ARKit. Ele aborda como adicionar um modelo 3D a um aplicativo, configure o modo de exibição, implementar um delegado de sessão, posicione o modelo 3D no mundo e pausar a sessão realidade aumentada.
ms.prod: xamarin
ms.assetid: 70291430-BCC1-445F-9D41-6FBABE87078E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/30/2016
ms.openlocfilehash: 55ef2004f66cb808f878b2215dfdd59a45015877
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787157"
---
# <a name="introduction-to-arkit-in-xamarinios"></a>Introdução ao ARKit em xamarin

_Realidade aumentada para iOS 11_

ARKit permite uma ampla variedade de jogos e aplicativos realidade aumentada. Esta seção abrange os seguintes tópicos:

- [Introdução ao ARKit](#gettingstarted)
- [Usando ARKit com UrhoSharp](urhosharp.md)

<a name="gettingstarted" />

## <a name="getting-started-with-arkit"></a>Introdução ao ARKit

Para começar a realidade aumentada, as instruções a seguir orientam por meio de um aplicativo simples: posicionamento de um modelo 3D e permitindo que ARKit manter o modelo no local com a funcionalidade de controle.

![Flutuante da câmera imagem de modelo 3D do Jet](images/jet-sml.png)

### <a name="1-add-a-3d-model"></a>1. Adicionar um modelo 3D

Ativos devem ser adicionados ao projeto com o **SceneKitAsset** ação de compilação.

![SceneKit ativos em um projeto](images/scene-assets.png)


### <a name="2-configure-the-view"></a>2. Configurar o modo de exibição

O controlador de exibição `ViewDidLoad` método, carregar o ativo de cena e defina o `Scene` propriedade no modo de exibição:

```csharp
ARSCNView SceneView = (View as ARSCNView);

// Create a new scene
var scene = SCNScene.FromFile("art.scnassets/ship");

// Set the scene to the view
SceneView.Scene = scene;
```

### <a name="3-optionally-implement-a-session-delegate"></a>3. Opcionalmente, implemente um delegado de sessão

Embora não seja necessário para casos simples, implementar um delegado de sessão pode ser útil para depurar o estado da sessão ARKit (e em aplicativos reais, fornecer comentários para o usuário). Crie um delegado simple usando o código a seguir:

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

Atribuir o representante no `ViewDidLoad` método:

```csharp
// Track changes to the session
SceneView.Session.Delegate = new SessionDelegate();
```

### <a name="4-position-the-3d-model-in-the-world"></a>4. Posicione o modelo 3D no mundo

Em `ViewWillAppear`, o código a seguir estabelece uma sessão de ARKit e define a posição do modelo 3D no espaço em relação a câmera do dispositivo:

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

Cada vez que o aplicativo é executado ou retomado, o modelo 3D será posicionado na frente a câmera. Depois que o modelo é posicionado, mova a câmera e observe como ARKit mantém o modelo posicionado.

### <a name="5-pause-the-augmented-reality-session"></a>5. Pausar a sessão realidade aumentada

É uma boa prática para pausar a sessão ARKit quando o controlador de exibição não está visível (no `ViewWillDisappear` método:

```csharp
SceneView.Session.Pause();
```

## <a name="summary"></a>Resumo

O código acima resulta em um aplicativo de ARKit simples. Exemplos mais complexos esperava o controlador de exibição hospedando a sessão realidade aumentada para implementar `IARSCNViewDelegate`, e métodos adicionais de ser implementado.

ARKit fornece muitos recursos mais sofisticados, como controle de superfície e interação do usuário. Consulte o [UrhoSharp demonstração](urhosharp.md) para obter um exemplo combinando ARKit com UrhoSharp de controle.


## <a name="related-links"></a>Links relacionados

- [Realidade aumentada (Apple)](https://developer.apple.com/arkit/)
- [Usando ARKit com UrhoSharp](urhosharp.md)
- [Exemplo simples ARKit (Jet)](https://developer.xamarin.com/samples/monotouch/ios11/ARKitSample/)
- [ARKit colocação de objetos (exemplo)](https://developer.xamarin.com/samples/monotouch/ios11/ARKitPlacingObjects/)
- [Introdução ao ARKit - realidade aumentada para iOS (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/602/)
