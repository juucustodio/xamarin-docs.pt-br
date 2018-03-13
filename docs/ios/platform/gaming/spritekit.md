---
title: SpriteKit
ms.topic: article
ms.prod: xamarin
ms.assetid: 93971DAE-ED6B-48A8-8E61-15C0C79786BB
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/14/2017
ms.openlocfilehash: c533e38fcd8775fd6b9a49c2e14de76673641553
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="spritekit"></a>SpriteKit

Kit de entidade gráfica, a estrutura de jogos 2D da Apple, tem alguns novos recursos interessantes do iOS 8 e OS X Yosemite. Isso inclui a integração com o Kit de cena, suporte de sombreador, iluminação, sombras, restrições, geração de mapa normal e aprimoramentos física. Em particular, os novos recursos de física facilitam muito a adicionar efeitos realistas para um jogo.

## <a name="physics-bodies"></a>Corpos de física

Kit de entidade gráfica inclui um 2D, física rígida corpo API. Cada entidade gráfica tem um corpo física associada (`SKPhysicsBody`) que define as propriedades de física, como em massa e fricção, bem como a geometria do corpo do mundo física.

## <a name="creating-a-physics-body-from-a-texture"></a>Criando um corpo física de uma textura
Kit de entidade gráfica agora dá suporte a derivação de corpo de uma entidade gráfica física de sua textura. Isso facilita a implementar colisões mais natural.

Por exemplo, observe na colisão a seguir como a banana e monkey colidem quase na superfície de cada imagem:
 
![](spritekit-images/image13.png "O banana e monkey colidem quase na superfície de cada imagem")

Kit de entidade gráfica possibilita a criação de tal um corpo de física com uma única linha de código. Basta chamar `SKPhysicsBody.Create` com a textura e o tamanho: entidade gráfica. PhysicsBody = SKPhysicsBody.Create (entidade gráfica. Textura, entidade gráfica. Tamanho);

## <a name="alpha-threshold"></a>Alpha Threshold

Além de simplesmente definir o `PhysicsBody` propriedade diretamente a geometria derivada de textura, aplicativos podem definir e o limite alfa para controlar como a geometria é derivada. 

O limite alfa define o valor mínimo de alfa que um pixel deve ter para ser incluído no corpo da física resultante. Por exemplo, o código a seguir resulta em um corpo física ligeiramente diferentes:

```chsarp
sprite.PhysicsBody = SKPhysicsBody.Create (sprite.Texture, 0.7f, sprite.Size);
```

O efeito de ajustar o limite alfa assim fine-tunes a colisão anterior, que o monkey cai quando conflito com o banana:

![](spritekit-images/image14.png "O monkey cai quando conflito com o banana")
 
## <a name="physics-fields"></a>Campos física

Outro importante acréscimo ao Kit de entidade gráfica é compatível com o novo campo física. Elas permitem que você adicionar itens, como campos de vortex, campos de gravidade radial e campos de spring para mencionar apenas alguns.

Campos física são criados usando a classe SKFieldNode, que é adicionada a uma cena assim como qualquer outro `SKNode`. Há uma variedade de métodos de fábrica em `SKFieldNode` para criar campos física diferente. Você pode criar um campo de spring chamando `SKFieldNode.CreateSpringField()`, um campo de gravidade radial chamando `SKFieldNode.CreateRadialGravityField()`, e assim por diante.

`SKFieldNode` também tem propriedades para controlar os atributos de campo, como a força do campo, a região de campo e a atenuação de força de campo.

## <a name="spring-field"></a>Campo de Spring

Por exemplo, o código a seguir cria um campo de spring e o adiciona à cena:

```csharp
SKFieldNode fieldNode = SKFieldNode.CreateSpringField ();
fieldNode.Enabled = true;
fieldNode.Position = new PointF (Size.Width / 2, Size.Height / 2);
fieldNode.Strength = 0.5f;
fieldNode.Region = new SKRegion(Frame.Size);
AddChild (fieldNode);
```

Você pode adicionar sprites e definir suas `PhysicsBody` propriedades para que o campo física afetará os sprites, assim como o código a seguir quando o usuário toca a tela:

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

Isso faz com que o bananas oscilar como um spring ao redor do nó de campo:

![](spritekit-images/image15.png "Os bananas oscilar como um spring ao redor do nó de campo")
 
## <a name="radial-gravity-field"></a>Campo de gravidade Radial

Adicionar um campo diferente é semelhante. Por exemplo, o código a seguir cria um campo de gravidade radial:

```csharp
SKFieldNode fieldNode = SKFieldNode.CreateRadialGravityField ();
fieldNode.Enabled = true;
fieldNode.Position = new PointF (Size.Width / 2, Size.Height / 2);
fieldNode.Strength = 10.0f;
fieldNode.Falloff = 1.0f;
```

Isso resulta em um campo diferente de força, onde os bananas são extraídas radialmente sobre o campo de:

![](spritekit-images/image16.png "Os bananas são extraídas radialmente ao redor do campo")