---
title: "Introdução ao iOS 7"
description: "Este artigo aborda as principais novas APIs introduzidas no iOS 7, incluindo transições de controlador de exibição, aprimoramentos UIView animações, UIKit Dynamics e o Kit de texto. Ele também aborda algumas das alterações para a interface do usuário e os novos recursos de multitarefa avançada."
ms.topic: article
ms.prod: xamarin
ms.assetid: 2C33018F-D64A-4BAA-A34E-082EF311D162
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: a7bebc2b73ecb564028a92340c726bd5c1f1c54b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-ios-7"></a>Introdução ao iOS 7

_Este artigo aborda as principais novas APIs introduzidas no iOS 7, incluindo transições de controlador de exibição, aprimoramentos UIView animações, UIKit Dynamics e o Kit de texto. Ele também aborda algumas das alterações para a interface do usuário e os novos recursos de multitarefa avançada._

iOS 7 é uma atualização importante para iOS. Ele apresenta um design de interface de usuário completamente nova que coloca o foco no chrome conteúdo em vez de aplicativo. Junto com o visual é alterado, o iOS 7 adiciona uma infinidade de novas APIs para criar experiências e interações mais sofisticadas. Pesquisas este documento novas tecnologias introduzida com o iOS 7 e serve como um ponto de partida para exploração adicional.

## <a name="uiview-animation-enhancements"></a>Aprimoramentos de animação UIView

iOS 7 amplia o suporte da animação UIKit, permitindo que os aplicativos para funções que antes de descartar diretamente no framework Core animação. Por exemplo, `UIView` agora pode executar animações de spring, bem como animações de quadro-chave, que anteriormente um `CAKeyframeAnimation` aplicado a um `CALayer`.

### <a name="spring-animations"></a>Animações de Spring

 `UIView` agora dá suporte a alterações de propriedade animação com um efeito de spring. Para adicionar isso, chame o `AnimateNotify` ou `AnimateNotifyAsync` método, passando valores para a taxa de amortecimento spring e velocidade spring inicial, conforme descrito abaixo:

-  `springWithDampingRatio` – Um valor entre 0 e 1, onde a oscilação aumenta para o menor valor.
-  `initialSpringVelocity` – A velocidade de spring inicial como uma porcentagem da distância animação total por segundo.


O código a seguir produz um efeito de spring quando alterações de centro de exibição de imagem:

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

Esse efeito spring faz com que o modo de exibição de imagem aparece no Elástico quando conclui sua animação para um novo local de centro, conforme ilustrado abaixo:

 ![](images/spring-animation.png "Esse efeito spring faz com que o modo de exibição de imagem aparece no Elástico quando conclui sua animação para um novo local de centro")

### <a name="keyframe-animations"></a>Animações de quadro-chave

O `UIView` classe agora inclui o `AnimateWithKeyframes` método para criar animações de quadro-chave em um `UIView`. Esse método é semelhante a outro `UIView` métodos de animação, exceto que adicional `NSAction` é passado como um parâmetro para incluir os quadros-chave. Dentro de `NSAction`, quadros-chave são adicionados ao chamar `UIView.AddKeyframeWithRelativeStartTime`.

Por exemplo, o trecho de código a seguir cria uma animação de quadro-chave para animar center de um modo de exibição, bem como para girar o modo de exibição:

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

Os dois primeiros parâmetros para o `AddKeyframeWithRelativeStartTime` método especificar a hora de início e a duração do quadro-chave, respectivamente, como uma porcentagem do tamanho geral de animação. O exemplo acima resulta na imagem exibição animar seu novo centro pela segunda primeiro, seguido de girar 90 graus sobre o próximo segundo. Desde que a animação especifica `UIViewKeyframeAnimationOptions.Autoreverse` como uma opção, ambos os quadros-chave animar na ordem inversa também. Por fim, os valores finais são definidos para o estado inicial no manipulador de conclusão.

Capturas de tela abaixo ilustra a animação combinada por meio de quadros-chave:

 ![](images/keyframes.png "Este capturas de tela ilustra a animação combinada por meio de quadros-chave")

## <a name="uikit-dynamics"></a>Dynamics UIKit

UIKit Dynamics é um novo conjunto de APIs em UIKit que permitem que aplicativos criar interações animadas com base em física. UIKit Dynamics encapsula um mecanismo de 2D física tornam isso possível.

A API é declarativa por natureza. Declare o comportam de interações física com a criação de objetos - chamados *comportamentos* - para conceitos de física expressa como gravidade, colisões, molas, etc. Em seguida, anexar o behavior(s) a outro objeto, chamado um *animator dinâmico*, que encapsula um modo de exibição. A dinâmica animator leva cuidados de aplicar os comportamentos física declarados para *itens dinâmicos* -itens que implementam `IUIDynamicItem`, como um `UIView`.

Há vários comportamentos primitivos diferentes disponíveis para disparar interações complexas, incluindo:

-  `UIAttachmentBehavior` – Conecta dois itens dinâmicos, de modo que eles se movam juntos ou anexa um item dinâmico para um ponto de anexo.
-  `UICollisionBehavior` – Permite que itens dinâmicos participar de colisões.
-  `UIDynamicItemBehavior` – Especifica um conjunto geral de propriedades para aplicar aos itens dinâmicos, como elasticidade, a densidade e a fricção.
-  `UIGravityBehavior` -Se aplica a gravidade a um item dinâmico, fazendo com que itens acelerar a direção gravitacional.
-  `UIPushBehavior` – Se aplica a força a um item dinâmico.
-  `UISnapBehavior` – Permite que um item dinâmico para ajustar-se para uma posição com um efeito de spring.


Embora haja muitos primitivos, o processo geral para adição de interações baseadas em física para uma exibição usando o UIKit Dynamics é consistente em comportamentos:

1.  Crie um animator dinâmico.
1.  Crie behavior(s).
1.  Adicione comportamentos para o animator dinâmico.


### <a name="dynamics-example"></a>Exemplo do Dynamics

Vejamos um exemplo que adiciona a gravidade e um limite de colisão para um `UIView`.

#### <a name="uigravitybehavior"></a>UIGravityBehavior

A adição de gravidade a um modo de exibição de imagem segue as 3 etapas descritas acima.

Trabalharemos `ViewDidLoad` método para este exemplo. Primeiro, adicione um `UIImageView` instância da seguinte maneira:

```csharp
image = UIImage.FromFile ("monkeys.jpg");

imageView = new UIImageView (new CGRect (new CGPoint (View.Center.X - image.Size.Width / 2, 0), image.Size)) {
                    Image =  image
                }

View.AddSubview (imageView);
```

Isso cria uma exibição de imagem centralizada na borda superior da tela. Para criar a imagem "queda" com a gravidade, crie uma instância de um `UIDynamicAnimator`:

```csharp
dynAnimator = new UIDynamicAnimator (this.View);
```

O `UIDynamicAnimator` usa uma instância de uma referência de `UIView` ou `UICollectionViewLayout`, que contém os itens que serão animados pelo behavior(s) anexado.

Em seguida, crie um `UIGravityBehavior` instância. Você pode passar um ou mais objetos que implementam o `IUIDynamicItem`, como um `UIView`:

```csharp
var gravity = new UIGravityBehavior (dynItems);
```

O comportamento é passado a uma matriz de `IUIDynamicItem`, nesse caso, que contém o único `UIImageView` instância que são de animação.

Finalmente, adicione o comportamento para o animator dinâmico:

```csharp
dynAnimator.AddBehavior (gravity);
```

Isso resulta na imagem a animação para baixo com gravidade, conforme ilustrado abaixo:

![](images/gravity2.png "A localização da imagem inicial") 
![](images/gravity3.png "a localização da imagem final")

Como não há nada restringir os limites da tela, o modo de exibição de imagem simplesmente se enquadra da parte inferior. Para restringir a exibição de modo que a imagem entra em conflito com as bordas da tela, podemos adicionar um `UICollisionBehavior`. Abordaremos isso na próxima seção.

#### <a name="uicollisionbehavior"></a>UICollisionBehavior

Vamos começar criando um `UICollisionBehavior` e adicioná-lo para o animator dinâmico, exatamente como foi feito o `UIGravityBehavior`.

Modifique o código para incluir o `UICollisionBehavior`:

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

O `UICollisionBehavior` tem uma propriedade chamada `TranslatesReferenceBoundsIntoBoundry`. A definição para `true` faz com que a referência dos limites do modo de exibição a ser usado como um limite de colisão.

Agora, quando a imagem para baixo anima com gravidade, ele devoluções ligeiramente da parte inferior da tela antes de se acomodar para rest existe.

<!--, as shown below:

 ![](images/bounce.png "Now, when the image animates downward with gravity, it bounces slightly off the bottom of the screen before settling to rest there")-->

#### <a name="uidynamicitembehavior"></a>UIDynamicItemBehavior

Controlar ainda mais o comportamento do modo de exibição de imagem queda com comportamentos adicionais. Por exemplo, poderíamos adicionar uma `UIDynamicItemBehavior` para aumentar a elasticidade, fazendo com que o modo de exibição de imagem Elástico mais quando ele entra em conflito com a parte inferior da tela.

Adicionando um `UIDynamicItemBehavior` segue as mesmas etapas, assim como acontece com os outros comportamentos. Primeiro, crie o comportamento:

```csharp
var dynBehavior = new UIDynamicItemBehavior (dynItems) {
    Elasticity = 0.7f
};
```

Em seguida, adicione o comportamento de animator dinâmico:

 `dynAnimator.AddBehavior (dynBehavior);`

Com esse comportamento em vigor, o modo de exibição de imagem devoluções mais quando ele entra em conflito com o limite.

## <a name="general-user-interface-changes"></a>Alterações gerais da Interface de usuário

Além de novas APIs UIKit como UIKit Dynamics, transições de controlador e aprimoradas animações UIView descritas acima, iOS 7 apresenta uma variedade de alterações visuais na interface do usuário e as alterações de API relacionadas de vários modos de exibição e controles. Para obter mais informações, consulte o [iOS 7 visão geral sobre a Interface do usuário](~/ios/platform/introduction-to-ios7/ios7-ui.md).

## <a name="text-kit"></a>Kit de texto

Kit de texto é uma nova API que oferece recursos de layout e renderização de texto avançado. Ele é criado sobre o estrutura do texto principal de nível baixo, mas é muito mais fácil de usar do que o texto de núcleo.

Para obter mais informações, consulte nosso [TextKit](~/ios/platform/textkit.md)

## <a name="multitasking"></a>Multitarefa

iOS 7 é alterado quando e como trabalho em segundo plano é executado. Conclusão da tarefa no iOS 7 não mantém os aplicativos ativos quando as tarefas estão sendo executadas em segundo plano e aplicativos são ativados para processamento de uma maneira não contíguas em segundo plano. iOS 7 também adiciona três novas APIs para atualizar aplicativos com novo conteúdo em segundo plano:

-  Busca em segundo plano – permite aos aplicativos para atualizar conteúdo em segundo plano em intervalos regulares.
-  Notificações remotas - permite que os aplicativos atualizar o conteúdo ao receber uma notificação por push. As notificações podem ser silenciosa ou pode exibir uma faixa na tela de bloqueio.
-  Serviço de transferência em segundo plano – permite upload e download de dados, como arquivos grandes, sem um limite de tempo fixo.


Para obter mais detalhes sobre os novos recursos de multitarefa, consulte as seções de iOS do Xamarin [Backgrounding guia](~/ios/app-fundamentals/backgrounding/index.md).

## <a name="summary"></a>Resumo

Este artigo aborda diversas novas adições principais para iOS. Primeiro, ele mostra como adicionar transições personalizadas para controladores de exibição. Em seguida, ele mostra como usar transições nas exibições de coleção, tanto de dentro de um controlador de navegação, bem como de forma interativa entre exibições de coleção. Em seguida, ele apresenta vários aprimoramentos feitos no UIView animações, mostrando como os aplicativos usam UIKit para coisas que antes de programar diretamente na animação de núcleo. Por fim, nova API UIKit Dynamics, que apresenta um mecanismo de física para UIKit, foi introduzido junto com o suporte de RTF agora disponível na estrutura de Kit de texto.

## <a name="related-links"></a>Links relacionados

- [Introdução ao iOS 7 (exemplo)](https://developer.xamarin.com/samples/monotouch/IntroToiOS7)
- [iOS 7 visão geral sobre a Interface do usuário](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Backgrounding](~/ios/app-fundamentals/backgrounding/index.md)
