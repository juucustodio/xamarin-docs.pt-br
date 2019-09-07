---
title: SpriteKit no Xamarin. iOS
description: Este documento descreve o SpriteKit, a estrutura de gráficos 2D da Apple que se integra ao SceneKit, incorpora física e animação, inclui suporte para iluminação e sombreamento e muito mais. SpriteKit pode ser usado para criar jogos 2D.
ms.prod: xamarin
ms.assetid: 93971DAE-ED6B-48A8-8E61-15C0C79786BB
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/14/2017
ms.openlocfilehash: d2466de4891c289f4686c37bc9fe73c24a5a48ca
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70753057"
---
# <a name="spritekit-in-xamarinios"></a>SpriteKit no Xamarin. iOS

SpriteKit, a estrutura gráfica 2D da Apple, tem alguns novos recursos interessantes no iOS 8 e no OS X Yosemite. Isso inclui a integração com SceneKit, suporte a sombreador, iluminação, sombras, restrições, geração de mapa normal e aprimoramentos de física. Em particular, os novos recursos de física tornam muito fácil adicionar efeitos realistas a um jogo.

## <a name="physics-bodies"></a>Corpos de física

SpriteKit inclui uma API física de corpo rígida e 2D. Cada Sprite tem um corpo de física associado`SKPhysicsBody`() que define as propriedades da física, como Mass e fric, bem como a geometria do corpo no mundo da física.

## <a name="creating-a-physics-body-from-a-texture"></a>Criando um corpo de física de uma textura
O SpriteKit agora dá suporte à derivação do corpo de física de um Sprite de sua textura. Isso facilita a implementação de colisões que parecem mais naturais.

Por exemplo, observe na seguinte colisão como o banana e o macaco colidem quase na superfície de cada imagem:

![](spritekit-images/image13.png "O banana e o macaco colidem quase na superfície de cada imagem")

O SpriteKit facilita a criação de um corpo de física possível com uma única linha de código. Basta chamar `SKPhysicsBody.Create` com a textura e o tamanho: Sprite. PhysicsBody = SKPhysicsBody. Create (Sprite. Textura, Sprite. Tamanho);

## <a name="alpha-threshold"></a>Limite alfa

Além de simplesmente definir a `PhysicsBody` propriedade diretamente para a geometria derivada da textura, os aplicativos podem definir e o limite alfa para controlar como a geometria é derivada. 

O limite alfa define o valor alfa mínimo que um pixel deve ter para ser incluído no corpo da física resultante. Por exemplo, o código a seguir resulta em um corpo de física ligeiramente diferente:

```chsarp
sprite.PhysicsBody = SKPhysicsBody.Create (sprite.Texture, 0.7f, sprite.Size);
```

O efeito de ajustar o limite alfa como esse ajustará a colisão anterior, de modo que o macaco se sobreportará quando estiver colisando com o banana:

![](spritekit-images/image14.png "O macaco se enquadra ao colidir com o banana")

## <a name="physics-fields"></a>Campos de física

Outra grande adição ao SpriteKit é o novo suporte ao campo física. Eles permitem que você adicione coisas como campos vortex, campos de gravidade radial e campos Spring para citar apenas alguns.

Os campos de física são criados usando a classe SKFieldNode, que é adicionada a uma cena assim como `SKNode`qualquer outra. Há uma variedade de métodos de fábrica no `SKFieldNode` para criar campos de física diferentes. Você pode criar um campo Spring chamando `SKFieldNode.CreateSpringField()`, um campo de gravidade radial chamando `SKFieldNode.CreateRadialGravityField()`e assim por diante.

`SKFieldNode`também tem propriedades para controlar atributos de campo, como a intensidade de campo, a região de campo e a atenuação de forças de campo.

## <a name="spring-field"></a>Campo Spring

Por exemplo, o código a seguir cria um campo Spring e o adiciona à cena:

```csharp
SKFieldNode fieldNode = SKFieldNode.CreateSpringField ();
fieldNode.Enabled = true;
fieldNode.Position = new PointF (Size.Width / 2, Size.Height / 2);
fieldNode.Strength = 0.5f;
fieldNode.Region = new SKRegion(Frame.Size);
AddChild (fieldNode);
```

Em seguida, você pode adicionar sprites e definir `PhysicsBody` suas propriedades para que o campo física afete os sprites, como o código a seguir faz quando o usuário toca na tela:

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

Isso faz com que o bananas oscilar como uma mola em volta do nó de campo:

![](spritekit-images/image15.png "Bananas oscilar como uma mola em volta do nó de campo")

## <a name="radial-gravity-field"></a>Campo de gravidade radial

A adição de um campo diferente é semelhante. Por exemplo, o código a seguir cria um campo de gravidade radial:

```csharp
SKFieldNode fieldNode = SKFieldNode.CreateRadialGravityField ();
fieldNode.Enabled = true;
fieldNode.Position = new PointF (Size.Width / 2, Size.Height / 2);
fieldNode.Strength = 10.0f;
fieldNode.Falloff = 1.0f;
```

Isso resulta em um campo de força diferente, no qual os bananas são puxados radialmente sobre o campo:

![](spritekit-images/image16.png "Os bananas são puxados radialmente em volta do campo")
