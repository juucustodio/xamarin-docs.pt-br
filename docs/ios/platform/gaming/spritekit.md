---
title: SpriteKit no xamarin. IOS
description: Este documento descreve o SpriteKit, a estrutura de gráficos 2D da Apple que se integra com o SceneKit, incorpora a animação e física, inclui suporte para a iluminação e sombreamento e muito mais. SpriteKit pode ser usado para criar jogos 2D.
ms.prod: xamarin
ms.assetid: 93971DAE-ED6B-48A8-8E61-15C0C79786BB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/14/2017
ms.openlocfilehash: ef1e9a98b76166f4ee5638d1ab9762896d1e3bc8
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121640"
---
# <a name="spritekit-in-xamarinios"></a>SpriteKit no xamarin. IOS

SpriteKit, a estrutura de gráficos 2D da Apple, tem alguns novos recursos interessantes no iOS 8 e nos X Yosemite. Isso inclui a integração com o SceneKit, suporte de sombreador, iluminação, sombras, restrições, geração de mapa normal e aprimoramentos de física. Em particular, os novos recursos de física tornam muito fácil adicionar efeitos realistas para um jogo.

## <a name="physics-bodies"></a>Corpos de física

SpriteKit inclui um 2D, física de um corpo rígido API. Cada sprite tem um corpo de física associada (`SKPhysicsBody`) que define as propriedades de física, como em massa e atrito, bem como a geometria do corpo do mundo física.

## <a name="creating-a-physics-body-from-a-texture"></a>Criando um corpo de física de uma textura
SpriteKit agora dá suporte ao derivar o corpo de física de um sprite de sua textura. Isso torna mais fácil de implementar colisões mais natural.

Por exemplo, observe na colisão a seguir como a banana e monkey colidem quase na superfície de cada imagem:
 
![](spritekit-images/image13.png "O banana e monkey colidem quase na superfície de cada imagem")

SpriteKit possibilita criar tal um corpo de física com uma única linha de código. Basta chamar `SKPhysicsBody.Create` com o tamanho e a textura: sprite. PhysicsBody = SKPhysicsBody.Create (sprite. Textura, sprite. Tamanho);

## <a name="alpha-threshold"></a>Limite de alfa

Além de simplesmente definir o `PhysicsBody` propriedade diretamente à geometria derivada de textura, os aplicativos podem definir e o limite de alfa para controlar como a geometria é derivada. 

O limite de alfa define o valor alfa mínimo, que um pixel deve ter para ser incluído no corpo da física resultante. Por exemplo, o código a seguir resulta em um corpo de física ligeiramente diferente:

```chsarp
sprite.PhysicsBody = SKPhysicsBody.Create (sprite.Texture, 0.7f, sprite.Size);
```

O efeito das alterações do limite alfa assim ajusta a colisão anterior, o, de modo que o monkey diminui ao longo do quando colidam o banana:

![](spritekit-images/image14.png "O monkey diminui ao longo do quando colidam o banana")
 
## <a name="physics-fields"></a>Campos de física

Outra ótima adição à SpriteKit é o novo campo física dão suporte. Elas permitem que você adicionar itens, como campos vortex, campos de gravidade radial e spring para mencionar apenas alguns.

Campos de física são criados usando a classe SKFieldNode, que é adicionada a uma cena como qualquer outro `SKNode`. Há uma variedade de métodos de fábrica em `SKFieldNode` para criar campos de física diferente. Você pode criar um campo de spring chamando `SKFieldNode.CreateSpringField()`, um campo de gravidade radial chamando `SKFieldNode.CreateRadialGravityField()`e assim por diante.

`SKFieldNode` também tem propriedades para controlar os atributos de campo, como a força do campo, a região de campo e a atenuação de força de campo.

## <a name="spring-field"></a>Campo do Spring

Por exemplo, o código a seguir cria um campo de spring e adiciona à cena:

```csharp
SKFieldNode fieldNode = SKFieldNode.CreateSpringField ();
fieldNode.Enabled = true;
fieldNode.Position = new PointF (Size.Width / 2, Size.Height / 2);
fieldNode.Strength = 0.5f;
fieldNode.Region = new SKRegion(Frame.Size);
AddChild (fieldNode);
```

Você pode, em seguida, adicione sprites e defina seus `PhysicsBody` propriedades para que o campo física afetará os sprites, como o código a seguir faz quando o usuário toca na tela:

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    var touch = touches.AnyObject as UITouch;
    var pt = touch.LocationInNode (this);
    var node = SKSpriteNode.FromImageNamed ("TinyBanana");
    node.PhysicsBody = SKPhysicsBody.Create (node.Texture, node.Size);
    node.PhysicsBody.AffectedByGravity = false;
    node.PhysicsBody.AllowsRotation = true;
    node.PhysicsBody.Mass = 0.03f;
    node.Position = pt;
    AddChild (node);
}
```

Isso faz com que o bananas oscilar, como uma mola ao redor do nó de campo:

![](spritekit-images/image15.png "Os bananas oscilar, como uma mola ao redor do nó de campo")
 
## <a name="radial-gravity-field"></a>Campo de gravidade Radial

Adicionar um campo diferente é semelhante. Por exemplo, o código a seguir cria um campo de gravidade radial:

```csharp
SKFieldNode fieldNode = SKFieldNode.CreateRadialGravityField ();
fieldNode.Enabled = true;
fieldNode.Position = new PointF (Size.Width / 2, Size.Height / 2);
fieldNode.Strength = 10.0f;
fieldNode.Falloff = 1.0f;
```

Isso resulta em um campo de força diferente, onde os bananas são extraídos radialmente sobre o campo:

![](spritekit-images/image16.png "Os bananas são extraídos radialmente em torno do campo")
