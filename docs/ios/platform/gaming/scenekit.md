---
title: SceneKit no xamarin. IOS
description: Este documento descreve o SceneKit, uma cena 3D API do graph que simplifica o trabalho com gráficos 3D, removendo as complexidades do OpenGL.
ms.prod: xamarin
ms.assetid: 19049ED5-B68E-4A0E-9D57-B7FAE3BB8987
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/14/2017
ms.openlocfilehash: 0944978b34c8e164acd6e829db177bf4fd72dea9
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61376237"
---
# <a name="scenekit-in-xamarinios"></a>SceneKit no xamarin. IOS

SceneKit é uma API que simplifica o trabalho com gráficos 3D de grafo de cena 3D. Ele foi introduzido pela primeira vez nos X 10.8 e agora chegou ao iOS 8. Com SceneKit Criando visualizações 3D imersivas e casuais jogos 3D não requer experiência em OpenGL. Criando nos conceitos comuns de grafo de cena, SceneKit abstrai as complexidades de OpenGL e OpenGL ES, tornando muito mais fácil adicionar 3D conteúdo a um aplicativo. No entanto, se você for um especialista do OpenGL, SceneKit tem excelente suporte para juntar na diretamente com o OpenGL também. Ele também inclui vários recursos que complementam os gráficos 3D, como física e se integra muito bem com várias outras estruturas de Apple, como animação principal, a imagem principal e Sprite Kit.

SceneKit é extremamente fácil de trabalhar com. É uma API declarativa que se encarrega de renderização. Você simplesmente configurar uma cena, adicionar propriedades e identificadores de SceneKit a renderização da cena.

Para trabalhar com SceneKit criar um grafo de cena usando o `SCNScene` classe. Uma cena contém uma hierarquia de nós, representados por instâncias da `SCNNode`, definindo locais no espaço 3D. Cada nó tem propriedades como geometria, iluminação e os materiais que afetam sua aparência, conforme ilustrado pela figura a seguir:

![](scenekit-images/image7.png "A hierarquia do SceneKit") 

## <a name="create-a-scene"></a>Criar uma cena

Para tornar uma cena são exibidos na tela, você adicioná-lo para um `SCNView` atribuindo-o à propriedade de cena do modo de exibição. Além disso, se você fizer alterações à cena, `SCNView` será atualizado automaticamente para exibir as alterações.

```csharp
scene = SCNScene.Create ();
sceneView = new SCNView (View.Frame);
sceneView.Scene = scene;
```

Cenas podem ser preenchidas a partir de arquivos exportados por meio de uma ferramenta de modelagem de 3d ou por meio de programação primitivos geométricos. Por exemplo, isso é como criar uma esfera e adicioná-lo para a cena:

```csharp
sphere = SCNSphere.Create (10.0f);
sphereNode = SCNNode.FromGeometry (sphere);
sphereNode.Position = new SCNVector3 (0, 0, 0);
scene.RootNode.AddChildNode (sphereNode);
```

## <a name="adding-light"></a>Adição de luz

Neste momento a esfera não exibirá nada porque não há nenhuma luz na cena. Anexando `SCNLight` instâncias para nós cria as luzes em SceneKit. Há vários tipos de luzes, variando de várias formas de iluminação direcional a luz ambiente. Por exemplo, o código a seguir cria uma luz onidirecional na lateral da esfera:

```csharp
// omnidirectional light
var light = SCNLight.Create ();
var lightNode = SCNNode.Create ();
light.LightType = SCNLightType.Omni;
light.Color = UIColor.Blue;
lightNode.Light = light;
lightNode.Position = new SCNVector3 (-40, 40, 60);
scene.RootNode.AddChildNode (lightNode);
```

Iluminação onidirecional produz um reflexo difuso, resultando em uma mesma iluminação, classificação de como iluminar uma lanterna. Criar luz ambiente é semelhante, embora ele tenha nenhuma direção conforme ela se destaca igualmente em todas as direções. Pense nisso como humor iluminação :)

```csharp
// ambient light
ambientLight = SCNLight.Create ();
ambientLightNode = SCNNode.Create ();
ambientLight.LightType = SCNLightType.Ambient;
ambientLight.Color = UIColor.Purple;
ambientLightNode.Light = ambientLight;
scene.RootNode.AddChildNode (ambientLightNode);
```

Com as luzes em vigor, a esfera agora está visível na cena.

![](scenekit-images/image8.png "A esfera é visível na cena quando aceso")
 
## <a name="adding-a-camera"></a>Adicionar uma câmera

Adicionando uma câmera (SCNCamera) à cena altera o ponto de vista. O padrão para adicionar a câmera é semelhante. Crie a câmera, anexá-lo a um nó e adicionar o nó para a cena.

```csharp
// camera
camera = new SCNCamera {
    XFov = 80,
    YFov = 80
};
cameraNode = new SCNNode {
    Camera = camera,
    Position = new SCNVector3 (0, 0, 40)
};
scene.RootNode.AddChildNode (cameraNode);
```

Como você pode ver a partir de código acima, SceneKit objetos podem ser criados usando construtores ou o método de fábrica Create. O primeiro permite o uso de C# sintaxe do inicializador, mas qual delas usar é principalmente uma questão de preferência.

Com a câmera em vigor, a esfera inteira é visível para o usuário:

![](scenekit-images/image9.png "O círculo todo está visível para o usuário")
 
Você pode adicionar mais luzes à cena também. Aqui está o que se parece com as luzes onidirecionais mais alguns:

![](scenekit-images/image10.png "O círculo com alguns luzes onidirecionais mais")
 
Além disso, definindo `sceneView.AllowsCameraControl = true`, o usuário pode alterar o ponto de vista com um gesto de toque.

### <a name="materials"></a>Materiais

Os materiais criados com a classe SCNMaterial. Por exemplo adicionar uma imagem na superfície da esfera, definir a imagem para o material *difuso* conteúdo.

```csharp
material = SCNMaterial.Create ();
material.Diffuse.Contents = UIImage.FromFile ("monkey.png");
sphere.Materials = new SCNMaterial[] { material };
```

Esses camadas da imagem para o nó, conforme mostrado abaixo:

![](scenekit-images/image11.png "Disposição em camadas da imagem para a esfera")
 
Um material pode ser definido para responder a outros tipos de iluminação muito. Por exemplo, o objeto pode ser feito brilhante e ter seu conteúdo especular definido para exibir a reflexão especular, resultando em um ponto brilhante na superfície, conforme mostrado abaixo:

![](scenekit-images/image12.png "O objeto feita brilhante com reflexão especular, resultando em um ponto na superfície de brilhante")
 
Materiais são muito flexíveis, permitindo que você atinja muito com muito pouco código. Por exemplo, em vez de definir a imagem para o conteúdo difuso, defina-o conteúdo refletiva em vez disso.

```csharp
material.Reflective.Contents = UIImage.FromFile ("monkey.png");
```

Agora o monkey aparece visualmente ficará dentro do círculo, independente do ponto de vista.

### <a name="animation"></a>Animação

SceneKit foi projetado para funcionar bem com a animação. Você pode criar animações implícitas ou explícitas e pode até mesmo renderizar uma cena de uma árvore de camada de animação de núcleo. Ao criar uma animação implícita, SceneKit fornece sua própria classe de transição, `SCNTransaction`.

Aqui está um exemplo que gira a esfera:

```csharp
SCNTransaction.Begin ();
SCNTransaction.AnimationDuration = 2.0;
sphereNode.Rotation = new SCNVector4 (0, 1, 0, (float)Math.PI * 4);
SCNTransaction.Commit ();
```

Você pode animar muito mais do que a rotação entanto. Muitas propriedades de SceneKit sejam animáveis. Por exemplo, o código a seguir anima o material `Shininess` para aumentar a reflexão especular.

```csharp
SCNTransaction.Begin ();
SCNTransaction.AnimationDuration = 2.0;
material.Shininess = 0.1f;
SCNTransaction.Commit ();
```

SceneKit é muito fácil de usar. Ele oferece uma infinidade de recursos adicionais, incluindo restrições, física, ações declarativas, texto 3D, profundidade de suporte de campo, integração do Sprite Kit e integração de imagem principal para mencionar apenas alguns.