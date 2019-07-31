---
title: Introdução ao iOS 7
description: Este artigo aborda as principais novas APIs introduzidas no iOS 7, incluindo as transições do controlador de exibição, aprimoramentos em animações UIViews, UIKit Dynamics e o kit de texto. Ele também aborda algumas das alterações na interface do usuário e os novos recursos de multitarefas em risco.
ms.prod: xamarin
ms.assetid: 2C33018F-D64A-4BAA-A34E-082EF311D162
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 067d97e6a36dae6c11f056241c08c21899e96c08
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68649318"
---
# <a name="introduction-to-ios-7"></a>Introdução ao iOS 7

_Este artigo aborda as principais novas APIs introduzidas no iOS 7, incluindo as transições do controlador de exibição, aprimoramentos em animações UIViews, UIKit Dynamics e o kit de texto. Ele também aborda algumas das alterações na interface do usuário e os novos recursos de multitarefas em risco._

o iOS 7 é uma atualização importante para o iOS. Ele apresenta um design de interface do usuário completamente novo que coloca o foco no conteúdo, e não no Chrome do aplicativo. Juntamente com as alterações visuais, o iOS 7 adiciona uma grande quantidade de novas APIs para criar interações e experiências mais ricas. Este documento pesquisa as novas tecnologias introduzidas com o iOS 7 e serve como ponto de partida para explorar ainda mais.

## <a name="uiview-animation-enhancements"></a>Aprimoramentos de animação UIView

o iOS 7 aumenta o suporte à animação no UIKit, permitindo que os aplicativos façam coisas que anteriormente exigiam descartar diretamente para a estrutura de animação principal. Por exemplo, `UIView` agora pode executar animações Spring, bem como animações de quadro-chave, `CAKeyframeAnimation` que anteriormente eram `CALayer`aplicadas a um.

### <a name="spring-animations"></a>Animações Spring

 `UIView`Agora dá suporte à animação de alterações de propriedade com um efeito de mola. Para adicionar isso, chame o `AnimateNotify` método ou `AnimateNotifyAsync` , passando valores para a taxa de amortecimento de mola e a velocidade de mola inicial, conforme descrito abaixo:

-  `springWithDampingRatio`– Um valor entre 0 e 1, em que o oscilação aumenta para um valor menor.
-  `initialSpringVelocity`– A velocidade inicial da mola como uma porcentagem da distância total da animação por segundo.


O código a seguir produz um efeito de mola quando o centro da exibição de imagem é alterado:

```csharp
void AnimateWithSpring ()
{ 
    float springDampingRatio = 0.25f;
    float initialSpringVelocity = 1.0f;
    
    UIView.AnimateNotify (3.0, 0.0, springDampingRatio, initialSpringVelocity, 0, () => {
    
        imageView.Center = new CGPoint (imageView.Center.X, 400);   
            
    }, null);
}
```

Esse efeito de mola faz com que a exibição de imagem pareça saltar, pois ela conclui sua animação para um novo local central, conforme ilustrado abaixo:

 ![](images/spring-animation.png "Esse efeito de mola faz com que a exibição de imagem pareça ser devolvida à medida que ela conclui sua animação para um novo local central")

### <a name="keyframe-animations"></a>Animações de quadro-chave

A `UIView` classe agora inclui o `AnimateWithKeyframes` método para criar animações de quadro- `UIView`chave em um. Esse método é semelhante a outros `UIView` métodos de animação, exceto que um `NSAction` adicional é passado como um parâmetro para incluir os quadros-chave. Dentro do `NSAction`, os quadros-chave são adicionados chamando `UIView.AddKeyframeWithRelativeStartTime`.

Por exemplo, o trecho de código a seguir cria uma animação de quadro-chave para animar o centro de uma exibição, bem como para girar a exibição:

```csharp
void AnimateViewWithKeyframes ()
{
    var initialTransform = imageView.Transform;
    var initialCeneter = imageView.Center;

    // can now use keyframes directly on UIView without needing to drop directly into Core Animation

    UIView.AnimateKeyframes (2.0, 0, UIViewKeyframeAnimationOptions.Autoreverse, () => {
        UIView.AddKeyframeWithRelativeStartTime (0.0, 0.5, () => { 
            imageView.Center = new CGPoint (200, 200);
        });

        UIView.AddKeyframeWithRelativeStartTime (0.5, 0.5, () => { 
            imageView.Transform = CGAffineTransform.MakeRotation ((float)Math.PI / 2);
        });
    }, (finished) => {
        imageView.Center = initialCeneter;
        imageView.Transform = initialTransform;

        AnimateWithSpring ();
    });
}
```

Os dois primeiros parâmetros para o `AddKeyframeWithRelativeStartTime` método especificam a hora de início e a duração do quadro-chave, respectivamente, como uma porcentagem do tamanho geral da animação. O exemplo acima resulta na animação da exibição de imagem para seu novo centro ao longo do primeiro segundo, seguido de rotação de 90 graus no próximo segundo. Como a animação especifica `UIViewKeyframeAnimationOptions.Autoreverse` como uma opção, ambos os quadros-chave são animados também em ordem inversa. Por fim, os valores finais são definidos para o estado inicial no manipulador de conclusão.

As capturas de tela abaixo ilustram a animação combinada por meio dos quadros-chave:

 ![](images/keyframes.png "Estas capturas de tela ilustram a animação combinada por meio dos quadros-chave")

## <a name="uikit-dynamics"></a>UIKit Dynamics

O UIKit Dynamics é um novo conjunto de APIs no UIKit que permite que os aplicativos criem interações animadas com base na física. O UIKit Dynamics encapsula um mecanismo de física 2D para tornar isso possível.

A API é declarativa por natureza. Você declara como as interações de física se comportam criando objetos chamados *comportamentos* – para expressar conceitos de física, como gravidade, colisões, molas, etc. Em seguida, você anexa os comportamentos a outro objeto, chamado de *Animator dinâmico*, que encapsula uma exibição. O Animator dinâmico se preocupa em aplicar os comportamentos de física declarados a *itens* dinâmicos `IUIDynamicItem`– itens que implementam, como um. `UIView`

Há vários comportamentos primitivos diferentes disponíveis para disparar interações complexas, incluindo:

-  `UIAttachmentBehavior`– Anexa dois itens dinâmicos, de forma que eles se movam juntos ou anexe um item dinâmico a um ponto de anexo.
-  `UICollisionBehavior`– Permite que itens dinâmicos participem de colisões.
-  `UIDynamicItemBehavior`– Especifica um conjunto geral de propriedades para aplicar a itens dinâmicos, como elasticidade, densidade e fricção.
-  `UIGravityBehavior`– Aplica a gravidade a um item dinâmico, fazendo com que os itens acelerem na direção do Gravitational.
-  `UIPushBehavior`– Aplica-se força a um item dinâmico.
-  `UISnapBehavior`– Permite que um item dinâmico se ajuste a uma posição com um efeito de mola.


Embora existam muitos primitivos, o processo geral para adicionar interações baseadas em física a uma exibição usando o UIKit Dynamics é consistente entre os comportamentos:

1.  Crie um Animator dinâmico.
1.  Criar comportamento (s).
1.  Adicione comportamentos ao Animator dinâmico.


### <a name="dynamics-example"></a>Exemplo do Dynamics

Vejamos um exemplo que adiciona gravidade e um limite de colisão a um `UIView`.

#### <a name="uigravitybehavior"></a>UIGravityBehavior

A adição de gravidade a uma exibição de imagem segue as 3 etapas descritas acima.

Vamos trabalhar no `ViewDidLoad` método para este exemplo. Primeiro, adicione uma `UIImageView` instância da seguinte maneira:

```csharp
image = UIImage.FromFile ("monkeys.jpg");

imageView = new UIImageView (new CGRect (new CGPoint (View.Center.X - image.Size.Width / 2, 0), image.Size)) {
                    Image =  image
                }

View.AddSubview (imageView);
```

Isso cria uma exibição de imagem centralizada na borda superior da tela. Para tornar a imagem "cair" com a gravidade, crie uma instância de `UIDynamicAnimator`um:

```csharp
dynAnimator = new UIDynamicAnimator (this.View);
```

O `UIDynamicAnimator` usa uma instância de uma referência `UIView` ou um `UICollectionViewLayout`, que contém os itens que serão animados de acordo com os comportamentos anexados.

Em seguida, crie `UIGravityBehavior` uma instância. Você pode passar um ou mais objetos implementando `IUIDynamicItem`o, como `UIView`um:

```csharp
var gravity = new UIGravityBehavior (dynItems);
```

O comportamento é passado a uma matriz `IUIDynamicItem`de, que nesse caso contém a única `UIImageView` instância que estamos animados.

Por fim, adicione o comportamento ao Animator dinâmico:

```csharp
dynAnimator.AddBehavior (gravity);
```

Isso resulta na animação de imagem abaixo com gravidade, conforme ilustrado abaixo:

![](images/gravity2.png "O local") 
![](images/gravity3.png "da imagem inicial do local da imagem final")

Como não há nada restringindo os limites da tela, a exibição da imagem simplesmente fica fora da parte inferior. Para restringir a exibição de forma que a imagem colide com as bordas da tela, podemos adicionar um `UICollisionBehavior`. Abordaremos isso na próxima seção.

#### <a name="uicollisionbehavior"></a>UICollisionBehavior

Vamos começar criando um `UICollisionBehavior` e adicionando-o ao Animator dinâmico, exatamente como fizemos para o. `UIGravityBehavior`

Modifique o código para incluir `UICollisionBehavior`:

```csharp
using (image = UIImage.FromFile ("monkeys.jpg")) {

    imageView = new UIImageView (new CGRect (new CGPoint (View.Center.X - image.Size.Width / 2, 0), image.Size)) {
        Image =  image
    };

    View.AddSubview (imageView);

    // 1. create the dynamic animator
    dynAnimator = new UIDynamicAnimator (this.View);

    // 2. create behavior(s)
    var gravity = new UIGravityBehavior (imageView);
    var collision = new UICollisionBehavior (imageView) {
        TranslatesReferenceBoundsIntoBoundary = true
    };

    // 3. add behaviors(s) to the dynamic animator
    dynAnimator.AddBehaviors (gravity, collision);
}
```

O `UICollisionBehavior` tem uma propriedade chamada `TranslatesReferenceBoundsIntoBoundry`. Definir isso como `true` faz com que os limites da exibição de referência sejam usados como um limite de colisão.

Agora, quando a imagem é animada verticalmente com gravidade, ela salta um pouco da parte inferior da tela antes de liquidar o restante.

<!--, as shown below:

 ![](images/bounce.png "Now, when the image animates downward with gravity, it bounces slightly off the bottom of the screen before settling to rest there")-->

#### <a name="uidynamicitembehavior"></a>UIDynamicItemBehavior

Podemos controlar ainda mais o comportamento da exibição da imagem descendente com comportamentos adicionais. Por exemplo, poderíamos adicionar um `UIDynamicItemBehavior` para aumentar a elasticidade, fazendo com que o modo de exibição de imagem salte mais quando ele colidir com a parte inferior da tela.

Adicionar um `UIDynamicItemBehavior` segue as mesmas etapas que os outros comportamentos. Primeiro, crie o comportamento:

```csharp
var dynBehavior = new UIDynamicItemBehavior (dynItems) {
    Elasticity = 0.7f
};
```

Em seguida, adicione o comportamento ao Animator dinâmico:

 `dynAnimator.AddBehavior (dynBehavior);`

Com esse comportamento em vigor, a exibição de imagem salta mais quando ela colidi com o limite.

## <a name="general-user-interface-changes"></a>Alterações gerais da interface do usuário

Além das novas APIs UIKit, como UIKit Dynamics, transições de controlador e animações UIView aprimoradas descritas acima, o iOS 7 apresenta uma variedade de alterações visuais na interface do usuário e alterações de API relacionadas para vários modos de exibição e controles. Para obter mais informações, consulte [visão geral da interface do usuário do IOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md).

## <a name="text-kit"></a>Kit de texto

O kit de texto é uma nova API que oferece recursos avançados de layout de texto e renderização. Ele se baseia na estrutura de texto principal de nível baixo, mas é muito mais fácil de usar do que o texto principal.

Para obter mais informações, consulte nosso [TextKit](~/ios/platform/textkit.md)

## <a name="multitasking"></a>Multitarefa

o iOS 7 muda quando e como o trabalho em segundo plano é executado. A conclusão da tarefa no iOS 7 não mantém mais os aplicativos ativos quando as tarefas estão sendo executadas em segundo plano, e os aplicativos são ativados para processamento em segundo plano de forma não contígua. o iOS 7 também adiciona três novas APIs para atualizar aplicativos com novo conteúdo em segundo plano:

-  Busca em segundo plano – permite que os aplicativos atualizem o conteúdo em segundo plano em intervalos regulares.
-  Notificações remotas – permite que os aplicativos atualizem conteúdo ao receber uma notificação por push. As notificações podem ser silenciosas ou podem exibir uma faixa na tela de bloqueio.
-  Serviço de transferência em segundo plano – permite carregar e baixar dados, como arquivos grandes, sem um limite de tempo fixo.


Para obter mais detalhes sobre os novos recursos de multitarefa, consulte as seções do iOS do [Guia de fundo](~/ios/app-fundamentals/backgrounding/index.md)do Xamarin.

## <a name="summary"></a>Resumo

Este artigo aborda várias novas adições importantes ao iOS. Primeiro, ele mostra como adicionar transições personalizadas para exibir controladores. Em seguida, ele mostra como usar as transições nas exibições de coleção, de dentro de um controlador de navegação, bem como interativamente entre exibições de coleção. Em seguida, ele apresenta vários aprimoramentos feitos em animações UIViews, mostrando como os aplicativos usam o UIKit para coisas que anteriormente exigiam programação diretamente na animação principal. Por fim, a nova API do Dynamics UIKit, que traz um mecanismo de física para o UIKit, é introduzida junto com o suporte a Rich Text agora disponível na estrutura do kit de texto.

## <a name="related-links"></a>Links relacionados

- [Introdução ao iOS 7 (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/introtoios7)
- [Visão geral da interface do usuário do iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Backgrounding](~/ios/app-fundamentals/backgrounding/index.md)
