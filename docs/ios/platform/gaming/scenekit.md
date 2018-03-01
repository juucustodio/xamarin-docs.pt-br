---
title: SceneKit
ms.topic: article
ms.prod: xamarin
ms.assetid: 19049ED5-B68E-4A0E-9D57-B7FAE3BB8987
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/14/2017
ms.openlocfilehash: 785faab84c4f3c5176750f4d86eeaeae3bb9332d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="scenekit"></a>SceneKit

Kit de cena é um API que simplifica o trabalho com gráficos 3D do gráfico de cena 3D. Ele foi introduzido nos X 10.8 e agora chegou iOS 8. Com o Kit de cena Criando visualizações 3D envolventes e jogos casuais de 3D não requer experiência em OpenGL. Aproveitando os conceitos de gráfico de cena comuns, Kit de cena abstrai a complexidade de OpenGL e OpenGL ES, facilitando muito 3D de adicionar conteúdo a um aplicativo. No entanto, se você for um especialista OpenGL, o Kit de cena tem excelente suporte para associação diretamente com OpenGL também. Ele também inclui vários recursos que complementam os gráficos 3D, como física e se integra muito bem com várias outras estruturas de Apple, como principais de animação, imagem principal e entidade gráfica Kit.

Kit de cena é muito fácil trabalhar com. É uma API declarativa que cuida da renderização. Você simplesmente configurar uma cena, adicionar propriedades e identificadores de cena Kit a renderização da cena.

Para trabalhar com o Kit de cena que você criar um gráfico de cena usando o `SCNScene` classe. Uma cena contém uma hierarquia de nós, representados por instâncias de `SCNNode`, definindo locais em espaço 3D. Cada nó tem propriedades como geometria, iluminação e materiais que afetam sua aparência, conforme ilustrado pela figura a seguir:

![](scenekit-images/image7.png "A hierarquia de SceneKit") 

## <a name="create-a-scene"></a>Criar uma cena

Para tornar uma cena aparecem na tela, você adicioná-lo para um `SCNView` atribuindo a ele a propriedade de cena do modo de exibição. Além disso, se você fizer alterações à cena, `SCNView` será atualizado automaticamente para exibir as alterações.

```csharp
scene = SCNScene.Create ();
sceneView = new SCNView (View.Frame);
sceneView.Scene = scene;
```

Segundo plano pode ser preenchido a partir de arquivos exportados por meio de uma ferramenta de modelagem de 3d ou programaticamente primitivos geométricos. Por exemplo, isso é como criar uma esfera e adicioná-lo à cena:

```csharp
sphere = SCNSphere.Create (10.0f);
sphereNode = SCNNode.FromGeometry (sphere);
sphereNode.Position = new SCNVector3 (0, 0, 0);
scene.RootNode.AddChildNode (sphereNode);
```

## <a name="adding-light"></a>Adicionando a luz

Neste ponto o círculo não exibirá nada porque não há nenhuma luz da cena. Anexando `SCNLight` instâncias a nós cria luzes no Kit de cena. Há vários tipos de luzes variando de várias formas de iluminação direcional a luz ambiente. Por exemplo, o código a seguir cria uma luz unidirecional no lado da esfera:

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

Iluminação unidirecional produz um reflexo difuso, resultando em uma mesmo iluminação, classificação de como iluminar uma lâmpada. Criar luz ambiente é semelhante, embora não tem nenhuma direção, já que ele brilha igualmente em todas as direções. Pense nisso como tom de iluminação :)

```csharp
// ambient light
ambientLight = SCNLight.Create ();
ambientLightNode = SCNNode.Create ();
ambientLight.LightType = SCNLightType.Ambient;
ambientLight.Color = UIColor.Purple;
ambientLightNode.Light = ambientLight;
scene.RootNode.AddChildNode (ambientLightNode);
```

Com as luzes em vigor, o círculo agora está visível na cena.

![](scenekit-images/image8.png "O círculo está visível na cena quando aceso")
 
## <a name="adding-a-camera"></a>Adicionando uma câmera

A adição de uma câmera (SCNCamera) à cena altera o ponto de vista. O padrão para adicionar a câmera é semelhante. Crie a câmera, anexá-lo a um nó e adicionar o nó de cena.

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

Como você pode ver no código acima, objetos podem ser criados usando construtores de Kit de cena ou do método de fábrica de criar. Permite o anterior usando a sintaxe do inicializador de c#, mas qual delas usar é principalmente uma questão de preferência.

Com a câmera em vigor, esfera de inteiro é visível para o usuário:

![](scenekit-images/image9.png "O círculo inteiro está visível para o usuário")
 
Você pode adicionar luzes adicionais à cena também. Aqui está o que se parece com alguns luzes unidirecional mais:

![](scenekit-images/image10.png "O círculo com alguns luzes unidirecional mais")
 
Além disso, definindo `sceneView.AllowsCameraControl = true`, o usuário pode alterar o ponto de vista com um gesto de toque.

### <a name="materials"></a>Materiais

Materiais são criados com a classe SCNMaterial. Por exemplo adicionar uma imagem na superfície da esfera, definir a imagem em relação ao material *difuso* conteúdo.

```csharp
material = SCNMaterial.Create ();
material.Diffuse.Contents = UIImage.FromFile ("monkey.png");
sphere.Materials = new SCNMaterial[] { material };
```

Isso camadas da imagem para o nó, conforme mostrado abaixo:

![](scenekit-images/image11.png "Dispondo em camadas da imagem para a esfera")
 
Um material pode ser definido para responder a outros tipos de iluminação muito. Por exemplo, o objeto pode ser feito brilhante e seu conteúdo especular definiu para exibir reflexão especular, resultando em um ponto brilhante na superfície, conforme mostrado abaixo:

![](scenekit-images/image12.png "O objeto feito brilhante com reflexão especular, resultando em um ponto brilhante na superfície de")
 
Materiais são muito flexíveis, permitindo que você atinja muito com muito pouco código. Por exemplo, em vez de configurar a imagem para o conteúdo difuso, defina-o conteúdo refletiva em vez disso.

```csharp
material.Reflective.Contents = UIImage.FromFile ("monkey.png");
```

Agora o monkey parece visualmente ficam dentro da esfera, independente do ponto de vista.

### <a name="animation"></a>Animação

Kit de cena foi projetado para funcionar bem com animação. Você pode criar animações implícitas ou explícitas e ainda pode processar uma cena de uma árvore de camada de animação de núcleo. Ao criar uma animação implícita, o Kit de cena fornece sua própria classe de transição, `SCNTransaction`.

Aqui está um exemplo que gira o círculo:

```csharp
SCNTransaction.Begin ();
SCNTransaction.AnimationDuration = 2.0;
sphereNode.Rotation = new SCNVector4 (0, 1, 0, (float)Math.PI * 4);
SCNTransaction.Commit ();
```

É possível animar muito mais do que a rotação embora. Muitas propriedades do Kit de cena são pode ser animadas. Por exemplo, o código a seguir anima o material `Shininess` para aumentar a reflexão especular.

```csharp
SCNTransaction.Begin ();
SCNTransaction.AnimationDuration = 2.0;
material.Shininess = 0.1f;
SCNTransaction.Commit ();
```

Kit de cena é muito simples de usar. Ele oferece uma variedade de recursos adicionais, incluindo restrições, física, ações declarativas, texto 3D, profundidade de suporte de campo, integration Kit entidade gráfica e integração de imagem principal para mencionar apenas alguns.