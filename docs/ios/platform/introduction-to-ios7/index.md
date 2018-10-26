---
title: Introdução ao iOS 7
description: Este artigo aborda as principais novas APIs introduzidas no iOS 7, incluindo as transições de controlador de exibição, aprimoramentos UIView animações, Kit de texto e UIKit Dynamics. Ele também aborda algumas das alterações para a interface do usuário e os novos recursos de multitarefa avançada.
ms.prod: xamarin
ms.assetid: 2C33018F-D64A-4BAA-A34E-082EF311D162
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: db2ce779962947e2121ff03280544a080e193e2e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118039"
---
# <a name="introduction-to-ios-7"></a>Introdução ao iOS 7

_Este artigo aborda as principais novas APIs introduzidas no iOS 7, incluindo as transições de controlador de exibição, aprimoramentos UIView animações, Kit de texto e UIKit Dynamics. Ele também aborda algumas das alterações para a interface do usuário e os novos recursos de multitarefa avançada._

iOS 7 é uma atualização importante para o iOS. Ele apresenta um design de interface de usuário completamente novo que coloca o foco no conteúdo em vez de aplicativo chrome. Junto com o visual é alterado, o iOS 7 adiciona uma grande quantidade de novas APIs para criar experiências e interações mais sofisticadas. Este pesquisas de documento as novas tecnologias introduzidas com iOS 7 e serve como um ponto de partida para obter uma exploração.

## <a name="uiview-animation-enhancements"></a>Aperfeiçoamentos de animação UIView

iOS 7 amplia o suporte a animação no UIKit, permitindo que os aplicativos fazem coisas que anteriormente exigiam descartando diretamente para a estrutura de animação de núcleo. Por exemplo, `UIView` agora pode executar animações de spring, bem como animações de quadro-chave, que anteriormente uma `CAKeyframeAnimation` aplicados a um `CALayer`.

### <a name="spring-animations"></a>Animações de Spring

 `UIView` agora dá suporte a alterações de propriedade de animação com um efeito de spring. Para adicionar isso, chame o `AnimateNotify` ou `AnimateNotifyAsync` método, passando valores para a taxa de amortecimento spring e a velocidade inicial spring, conforme descrito abaixo:

-  `springWithDampingRatio` – Um valor entre 0 e 1, onde a oscilação aumenta para um valor menor.
-  `initialSpringVelocity` – A velocidade de spring inicial como um percentual da distância total da animação por segundo.


O código a seguir produz um efeito de spring ao centro de exibição de imagem é alterado:

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

Esse efeito spring faz com que o modo de exibição de imagem aparece no Elástico conforme ela conclui sua animação para um novo local de centro, conforme ilustrado abaixo:

 ![](images/spring-animation.png "Esse efeito spring faz com que o modo de exibição de imagem aparece no Elástico conforme ela conclui sua animação para um novo local de centro")

### <a name="keyframe-animations"></a>Animações de quadro-chave

O `UIView` classe agora inclui o `AnimateWithKeyframes` método de criação de animações de quadro-chave em um `UIView`. Esse método é semelhante aos outros `UIView` métodos de animação, exceto que mais `NSAction` é passado como um parâmetro para incluir os quadros-chave. Dentro de `NSAction`, os quadros-chave são adicionados ao chamar `UIView.AddKeyframeWithRelativeStartTime`.

Por exemplo, o trecho de código a seguir cria uma animação de quadro-chave para animar o Centro de um modo de exibição, bem como para girar o modo de exibição:

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

Os dois primeiros parâmetros para o `AddKeyframeWithRelativeStartTime` método especificam a hora de início e a duração do quadro de chave, respectivamente, como uma porcentagem do tamanho geral de animação. O exemplo acima resulta na imagem exibição animar seu novo centro de segundo o primeiro, seguido de rotação de 90 graus ao longo do próximo segundo. Uma vez que a animação especifica `UIViewKeyframeAnimationOptions.Autoreverse` como opção, os dois quadros-chave animar em ordem inversa também. Por fim, os valores finais são definidos para o estado inicial no manipulador de conclusão.

As capturas de tela abaixo ilustra a animação combinada por meio dos quadros-chave:

 ![](images/keyframes.png "Este capturas de tela ilustra a animação combinada por meio dos quadros-chave")

## <a name="uikit-dynamics"></a>Dynamics UIKit

UIKit Dynamics é um novo conjunto de APIs que permitem que aplicativos criar interações animadas com base em física no UIKit. UIKit Dynamics encapsula um mecanismo de física de 2D para tornar isso possível.

A API é declarativa por natureza. Você declara como as interações de física se comportam, criando objetos - chamados *comportamentos* – para conceitos da física expressa como a gravidade, colisões, springs, etc. Em seguida, anexe o comportamento a outro objeto, chamado de um *animator dinâmico*, que encapsula um modo de exibição. O animador dinâmico leva só queira de aplicar os comportamentos de física declarado para *itens dinâmicos* -itens que implementam `IUIDynamicItem`, como um `UIView`.

Há diversos comportamentos primitivos diferentes disponíveis para disparar interações complexas, incluindo:

-  `UIAttachmentBehavior` – Anexa dois itens dinâmicos, de modo que eles se movam juntos ou anexa um item dinâmico para um ponto de anexo.
-  `UICollisionBehavior` – Permite que os itens de dinâmicos participar de colisões.
-  `UIDynamicItemBehavior` – Especifica um conjunto geral de propriedades para aplicar aos itens dinâmicos, como elasticidade, densidade e atrito.
-  `UIGravityBehavior` -Aplica-se a gravidade a um item dinâmico, fazendo com que itens acelerar a direção gravitacional.
-  `UIPushBehavior` – Se aplica a força a um item dinâmico.
-  `UISnapBehavior` – Permite que um item de dinâmico para ajustar-se para uma posição com um efeito de spring.


Embora haja muitos primitivos, o processo geral para adição de interações baseadas em física para uma exibição usando o UIKit Dynamics é consistente entre os comportamentos:

1.  Crie um animator dinâmico.
1.  Crie o comportamento.
1.  Adicione comportamentos ao animador dinâmico.


### <a name="dynamics-example"></a>Exemplo do Dynamics

Vejamos um exemplo que adiciona a gravidade e um limite de colisão para um `UIView`.

#### <a name="uigravitybehavior"></a>UIGravityBehavior

Adição de gravidade para um modo de exibição de imagem segue as 3 etapas descritas acima.

Trabalharemos `ViewDidLoad` método para este exemplo. Primeiro, adicione um `UIImageView` instância como segue:

```csharp
image = UIImage.FromFile ("monkeys.jpg");

imageView = new UIImageView (new CGRect (new CGPoint (View.Center.X - image.Size.Width / 2, 0), image.Size)) {
                    Image =  image
                }

View.AddSubview (imageView);
```

Isso cria uma exibição de imagem centralizada na borda superior da tela. Para tornar a imagem "fall" com a gravidade, criar uma instância de um `UIDynamicAnimator`:

```csharp
dynAnimator = new UIDynamicAnimator (this.View);
```

O `UIDynamicAnimator` usa uma instância de uma referência `UIView` ou um `UICollectionViewLayout`, que contém os itens que serão animados pelo comportamento anexado.

Em seguida, crie um `UIGravityBehavior` instância. Você pode passar um ou mais objetos que implementam o `IUIDynamicItem`, como um `UIView`:

```csharp
var gravity = new UIGravityBehavior (dynItems);
```

O comportamento é passado uma matriz de `IUIDynamicItem`, nesse caso, que contém o único `UIImageView` podemos estiver animando de instância.

Por fim, adicione o comportamento para o animador dinâmico:

```csharp
dynAnimator.AddBehavior (gravity);
```

Isso resulta na imagem para baixo a animação com gravidade, conforme ilustrado abaixo:

![](images/gravity2.png "O local inicial da imagem") 
![](images/gravity3.png "a localização da imagem final")

Como não há nada restringindo os limites da tela, o modo de exibição de imagem simplesmente se enquadra da parte inferior. Para restringir a exibição de modo que a imagem entra em conflito com as bordas da tela, podemos adicionar um `UICollisionBehavior`. Abordaremos isso na próxima seção.

#### <a name="uicollisionbehavior"></a>UICollisionBehavior

Vamos começar criando um `UICollisionBehavior` e adicioná-lo para o animador dinâmico, exatamente como fizemos o `UIGravityBehavior`.

Modificar o código para incluir o `UICollisionBehavior`:

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

O `UICollisionBehavior` tem uma propriedade chamada `TranslatesReferenceBoundsIntoBoundry`. Definir isso como `true` faz com que a referência dos limites da exibição a ser usado como um limite de colisão.

Agora, quando a imagem é animado para baixo com gravidade, ela quicar um pouco da parte inferior da tela antes de escolherem enterro lá.

<!--, as shown below:

 ![](images/bounce.png "Now, when the image animates downward with gravity, it bounces slightly off the bottom of the screen before settling to rest there")-->

#### <a name="uidynamicitembehavior"></a>UIDynamicItemBehavior

Podemos controlar ainda mais o comportamento do modo de exibição de imagem queda com comportamentos adicionais. Por exemplo, poderíamos adicionar uma `UIDynamicItemBehavior` para aumentar a elasticidade, fazendo com que o modo de exibição de imagem devolver mais quando ele entra em conflito com a parte inferior da tela.

Adicionando um `UIDynamicItemBehavior` segue as mesmas etapas, assim como acontece com os outros comportamentos. Primeiro, crie o comportamento:

```csharp
var dynBehavior = new UIDynamicItemBehavior (dynItems) {
    Elasticity = 0.7f
};
```

Em seguida, adicione o comportamento para o animador dinâmico:

 `dynAnimator.AddBehavior (dynBehavior);`

Com esse comportamento em vigor, o modo de exibição de imagem bounces mais ao colidir com o limite.

## <a name="general-user-interface-changes"></a>Alterações gerais da Interface do usuário

Além das novas APIs de UIKit como UIKit Dynamics, transições de controlador e aprimoradas animações UIView descritas acima, o iOS 7 apresenta uma variedade de alterações visuais na interface do usuário e as alterações de API relacionadas para vários modos de exibição e controles. Para obter mais informações, consulte o [iOS 7 visão geral da Interface do usuário](~/ios/platform/introduction-to-ios7/ios7-ui.md).

## <a name="text-kit"></a>Kit de texto

Kit de texto é uma nova API que oferece recursos de layout e renderização de texto avançada. Ele é criado sobre o estrutura do texto principal de nível baixo, mas é muito mais fácil de usar do que o texto principal.

Para obter mais informações, consulte nosso [TextKit](~/ios/platform/textkit.md)

## <a name="multitasking"></a>Multitarefa

iOS 7 é alterado quando e como o trabalho em segundo plano é executado. Conclusão da tarefa no iOS 7 não mantém os aplicativos ativos quando tarefas em execução em segundo plano e aplicativos são ativados para processamento de uma maneira não contíguas em segundo plano. iOS 7 também adiciona três novas APIs de atualização de aplicativos com novo conteúdo em segundo plano:

-  Busca em segundo plano – permite aos aplicativos para atualizar o conteúdo em segundo plano em intervalos regulares.
-  Notificações remotas - permite que os aplicativos atualizar o conteúdo ao receber uma notificação por push. As notificações podem ser silenciosa ou pode exibir uma faixa na tela de bloqueio.
-  Serviço de transferência em segundo plano – permite upload e download de dados, como arquivos grandes, sem um limite de tempo fixo.


Para obter mais detalhes sobre os novos recursos de multitarefa, consulte as seções de iOS do Xamarin [Backgrounding guia](~/ios/app-fundamentals/backgrounding/index.md).

## <a name="summary"></a>Resumo

Este artigo aborda várias novas adições principais para iOS. Primeiro, ele mostra como adicionar transições personalizadas aos controladores de exibição. Em seguida, ele mostra como usar as transições nas exibições de coleção, tanto de dentro de um controlador de navegação, bem como de forma interativa entre exibições de coleção. Em seguida, ele apresenta vários aprimoramentos feitos no UIView animações, mostrando como os aplicativos usam UIKit para coisas que anteriormente exigiam programar diretamente em animação de núcleo. Por fim, a nova API do Dynamics UIKit, que apresenta um mecanismo de física para o UIKit, é introduzido junto com o suporte de texto avançado agora disponível no Kit de texto framework.

## <a name="related-links"></a>Links relacionados

- [Introdução ao iOS 7 (amostra)](https://developer.xamarin.com/samples/monotouch/IntroToiOS7)
- [Visão geral da interface do usuário do iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Backgrounding](~/ios/app-fundamentals/backgrounding/index.md)
