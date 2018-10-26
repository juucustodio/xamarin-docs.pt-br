---
title: Gestos no xamarin. IOS e eventos de toque
description: Este documento descreve como trabalhar com eventos de toque, multitoque, gestos, vários gestos e gestos personalizados em aplicativos xamarin. IOS.
ms.prod: xamarin
ms.assetid: DA666DC9-446E-4CD1-B5A0-C6FFBC7E53AD
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: f7160c48e1b1ac85f4aa0173c0eb9f42b8fefca2
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114763"
---
# <a name="touch-events-and-gestures-in-xamarinios"></a>Gestos no xamarin. IOS e eventos de toque

É importante compreender os eventos de toque e APIs de toque em um aplicativo iOS, conforme eles são essenciais para todas as interações com o dispositivo físicas. Todas as interações de toque envolvem um `UITouch` objeto. Neste artigo, aprenderemos a usar o `UITouch` classe e suas APIs para dar suporte ao toque. Mais tarde, vamos expandir em nosso conhecimento para saber como dar suporte a gestos.

## <a name="enabling-touch"></a>Habilitar toque

Controles em `UIKit` – esses subclasse de UIControl – é então depende da interação do usuário que eles têm gestos internas no UIKit e, portanto, não é necessário habilitar toque. Já está habilitado.

No entanto, muitas das exibições na `UIKit` não têm toque habilitado por padrão. Há duas maneiras de habilitar toque em um controle. A primeira maneira é verificar a caixa de seleção habilitado de interação do usuário no painel de propriedade do iOS Designer, conforme mostrado na seguinte captura de tela:

 [![](touch-in-ios-images/image1.png "Marque a caixa de seleção habilitado de interação do usuário no painel de propriedade do Designer do iOS")](touch-in-ios-images/image1.png#lightbox)

Podemos também usar um controlador para definir a `UserInteractionEnabled` propriedade como true em uma `UIView` classe. Isso é necessário se a interface do usuário é criada no código.

A seguinte linha de código é um exemplo:

```csharp
imgTouchMe.UserInteractionEnabled = true;
```

## <a name="touch-events"></a>Eventos de Toque

Há três fases de toque que ocorrem quando o usuário toca na tela, move o dedo ou remove seu dedo. Esses métodos são definidos no `UIResponder`, que é a classe base para UIView. iOS substituirá os métodos associados na `UIView` e o `UIViewController` para lidar com o toque:

-  `TouchesBegan` – Isso é chamado quando a tela pela primeira vez é tocada.
-  `TouchesMoved` – Isso é chamado quando o local das alterações de toque, como o usuário é deslizante seus dedos na tela.
-  `TouchesEnded` ou `TouchesCancelled` – `TouchesEnded` é chamado quando os dedos do usuário são elevados fora da tela.  `TouchesCancelled` é chamado se iOS cancelar a interatividade – por exemplo, se um usuário desliza seus dedos para fora de um botão para cancelar um pressionamento.


Recursivamente de viagem de eventos de toque para baixo por meio da pilha de UIViews, para verificar se o evento de toque está dentro dos limites de um objeto de exibição. Isso geralmente é chamado _teste de clique_. Eles primeiro serão chamados no nível superior `UIView` ou `UIViewController` e, em seguida, será chamado na `UIView` e `UIViewControllers` abaixo na hierarquia de exibição.

Um `UITouch` objeto será criado toda vez que o usuário toca na tela. O `UITouch` objeto inclui dados sobre o toque, como quando o toque ocorreu, em que ocorreu, se o toque foi um dedo, etc. Os eventos de toque forem transmitidos a uma propriedade de toques – um `NSSet` que contém um ou mais toques. Podemos usar essa propriedade para obter uma referência a um toque e determinar a resposta do aplicativo.

Classes que substituem um dos eventos de toque devem primeiro chamar a implementação base e, em seguida, obtenha o `UITouch` objeto associado ao evento. Para obter uma referência ao primeiro toque, chame o `AnyObject` propriedade e convertê-la como um `UITouch` conforme mostrado no exemplo a seguir:

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    base.TouchesBegan (touches, evt);
    UITouch touch = touches.AnyObject as UITouch;
    if (touch != null)
    {
        //code here to handle touch
    }
}
```

iOS reconhece automaticamente rápido sucessivo toca na tela e coletará tudo como um toque em um único `UITouch` objeto. Isso faz com que Verificando um toque duplo tão fácil quanto verificando o `TapCount` propriedade, conforme ilustrado no código a seguir:

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    base.TouchesBegan (touches, evt);
    UITouch touch = touches.AnyObject as UITouch;
    if (touch != null)
    {
        if (touch.TapCount == 2)
        {
            // do something with the double touch.
        }
    }
}
```

## <a name="multi-touch"></a>Multitoque

Multitoque não está habilitado por padrão nos controles. Multitoque pode ser habilitado no iOS Designer, conforme ilustrado pela captura de tela a seguir:

 [![](touch-in-ios-images/image2.png "Multitoque habilitado no iOS Designer")](touch-in-ios-images/image2.png#lightbox)

Também é possível definir multitoque programaticamente, definindo o `MultipleTouchEnabled` propriedade conforme mostrado na seguinte linha de código:

```csharp
imgTouchMe.MultipleTouchEnabled = true;
```

Para determinar quantos dedos tocadas a tela, use o `Count` propriedade no `UITouch` propriedade:

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    base.TouchesBegan (touches, evt);
    lblNumberOfFingers.Text = "Number of fingers: " + touches.Count.ToString();
}
```

## <a name="determining-touch-location"></a>Determinando o local de toque

O método `UITouch.LocationInView` retorna um objeto CGPoint que contém as coordenadas de toque dentro de um determinado modo de exibição. Além disso, podemos testar para ver se esse local estiver dentro de um controle, chamando o método `Frame.Contains`. O trecho de código a seguir mostra um exemplo disso:

```csharp
if (this.imgTouchMe.Frame.Contains (touch.LocationInView (this.View)))
{
    // the touch event happened inside the UIView imgTouchMe.
}
```

Agora que temos uma compreensão dos eventos de toque no iOS, vamos aprender sobre os reconhecedores de gestos.

## <a name="gesture-recognizers"></a>Reconhecedores de gestos

Reconhecedores de gestos bastante podem simplificar e reduzir o esforço de programação para dar suporte a toque em um aplicativo. reconhecedores de gestos de iOS agregam uma série de eventos de toque em um evento de toque único.

Xamarin. IOS fornece a classe `UIGestureRecognizer` como uma classe base para os reconhecedores de gestos internos seguintes:

-  *UITapGestureRecognizer* – isso é para um ou mais toques.
-  *UIPinchGestureRecognizer* – Pinching e distribuindo dedos de distância.
-  *UIPanGestureRecognizer* – panorâmica ou arrastar.
-  *UISwipeGestureRecognizer* – passar o dedo em qualquer direção.
-  *UIRotationGestureRecognizer* – girando dois dedos em um movimento no sentido horário ou anti-horário.
-  *UILongPressGestureRecognizer* – pressione e segure a tecla, às vezes conhecida como uma longa pressione ou longa clique.


O padrão básico para usar um reconhecedor de gestos é da seguinte maneira:

1.  **Instanciar o reconhecedor de gestos** – primeiro, criar uma instância de um `UIGestureRecognizer` subclasse. O objeto que é instanciado por uma exibição será associado e será ser coletado como lixo quando o modo de exibição é descartado. Não é necessário criar este modo de exibição como uma variável de nível de classe.
1.  **Defina as configurações de gesto** – a próxima etapa é configurar o reconhecedor de gestos. Consulte a documentação do Xamarin em `UIGestureRecognizer` e suas subclasses para obter uma lista de propriedades que podem ser definidas para controlar o comportamento de um `UIGestureRecognizer` instância.
1.  **Configurar o destino** – devido a sua herança Objective-C, o xamarin. IOS não geram eventos quando um reconhecedor de gestos corresponde a um gesto.  `UIGestureRecognizer` tem um método – `AddTarget` – que pode aceitar um delegado anônimo ou um seletor de Objective-C com o código a ser executado quando o reconhecedor de gestos faz uma correspondência.
1.  **Habilitar o reconhecedor de gestos** – assim como com eventos de toque, gestos só serão reconhecidos se interações de toque estão habilitadas.
1.  **Adicionar o reconhecedor de gestos para o modo de exibição** – a etapa final é adicionar o gesto para uma exibição chamando `View.AddGestureRecognizer` e passando-o um objeto do reconhecedor de gestos.

Consulte a [amostras do reconhecedor de gestos](~/ios/app-fundamentals/touch/ios-touch-walkthrough.md#Gesture_Recognizer_Samples) para obter mais informações sobre como implementá-los no código.

Quando o destino do gesto é chamado, ele passará uma referência para o gesto que ocorreu. Isso permite que o destino de gesto obter informações sobre o gesto que ocorreu. A extensão das informações disponíveis depende do tipo de reconhecedor de gestos que foi usado. Consulte a documentação do Xamarin para obter informações sobre os dados disponíveis para cada `UIGestureRecognizer` subclasse.

É importante lembrar-se de que depois que um reconhecedor de gestos tiver sido adicionado a uma exibição, o modo de exibição (e todas as exibições abaixo dele) não receberá quaisquer eventos de toque. Para permitir que os eventos de toque simultaneamente com gestos, o `CancelsTouchesInView` propriedade deve ser definida como false, conforme ilustrado pelo código a seguir:

```csharp
_tapGesture.Recognizer.CancelsTouchesInView = false;
```

Cada `UIGestureRecognizer` tem uma propriedade State que fornece informações importantes sobre o status do reconhecedor de gestos. Sempre que o valor dessa propriedade for alterada, o iOS chamará o método de assinatura dando a ele uma atualização. Se um reconhecedor de gestos personalizados nunca atualiza a propriedade de estado, o assinante nunca é chamado, tornando o reconhecedor de gestos inúteis.

Gestos podem ser resumidos como um dos dois tipos:

1.  *Discreto* – esses gestos única incêndio a primeira vez que são reconhecidas.
1.  *Contínua* – esses gestos continuam a ser acionado, desde que eles são reconhecidos.


Reconhecedores de gestos existe em um dos seguintes estados:

-  *Possíveis* – este é o estado inicial de todos os reconhecedores de gestos. Esse é o valor padrão a propriedade State.
-  *6ffc308f* – quando um gesto contínuo é reconhecido pela primeira vez, o estado é definido como 6ffc308f. Isso permite que se inscreve para diferenciar entre quando o reconhecimento de gesto é iniciado e quando ele é alterado.
-  *Changed* – depois de um gesto contínuo ter começado, mas não foi concluída, o estado será definido para Changed sempre que um toque move ou for alterado, enquanto ele estiver ainda dentro dos parâmetros esperados do gesto.
-  *Cancelado* – esse estado será definido se o reconhecedor deu a partir do 6ffc308f para alterado e, em seguida, os toques alterados de tal forma que para não mais corresponda ao padrão do gesto.
-  *Reconhecido* – o estado será definido quando o reconhecedor de gestos corresponde a um conjunto de toques e informar o assinante a que o gesto terminou.
-  *Finalizada* – esse é um alias para o estado de reconhecido.
-  *Falha* – quando o reconhecedor de gestos pode não corresponder os toques está escutando para, o estado será alterado para falha.


Xamarin. IOS representa esses valores no `UIGestureRecognizerState` enumeração.

## <a name="working-with-multiple-gestures"></a>Trabalhando com vários gestos

Por padrão, o iOS não permite a gestos padrão sejam executados simultaneamente. Em vez disso, cada reconhecedor de gestos receberá eventos de toque em uma ordem não-determinística. O seguinte trecho de código ilustrado como fazer com que um reconhecedor de gestos executadas simultaneamente:

```csharp
gesture.ShouldRecognizeSimultaneously += (UIGestureRecognizer r) => { return true; };
```

Também é possível desabilitar um gesto no iOS. Há duas propriedades de delegado que permitem que um reconhecedor de gestos examinar o estado de um aplicativo e os eventos de toque atual, para tomar decisões sobre como e se um gesto deve ser reconhecido. Os dois eventos são:

1.  *ShouldReceiveTouch* – esse delegado é chamado logo antes que o reconhecedor de gestos é passado a um evento de toque e fornece uma oportunidade para examinar os toques e decidir quais toques serão manipuladas pelo reconhecedor de gestos.
1.  *ShouldBegin* – isso é chamado quando um reconhecedor tenta alterar o estado de possíveis para outro estado. Retornando false forçará o estado do reconhecedor de gestos seja alterado para falha.


Você pode substituir esses métodos com fortemente tipado `UIGestureRecognizerDelegate`, um delegado fraco ou ligação por meio da sintaxe de manipulador de eventos, conforme ilustrado pela seguinte trecho de código:

```csharp
gesture.ShouldReceiveTouch += (UIGestureRecognizer r, UITouch t) => { return true; };
```

Por fim, é possível enfileirar um reconhecedor de gestos para que ele terá êxito apenas se o reconhecedor de gestos de outra falha. Por exemplo, um reconhecedor de gestos de toque único só deve ser bem-sucedidos quando um reconhecedor de gestos de toque duplo falha. O trecho de código a seguir fornece um exemplo disso:

```csharp
singleTapGesture.RequireGestureRecognizerToFail(doubleTapGesture);
```

## <a name="creating-a-custom-gesture"></a>Criando um gesto personalizado

Embora o iOS fornece algum padrão reconhecedores de gestos, pode ser necessário criar os reconhecedores de gestos personalizados em determinados casos. Criar um reconhecedor de gestos personalizado envolve as seguintes etapas:

1.  Subclasse `UIGestureRecognizer` .
1.  Substitua os métodos de evento de toque apropriado.
1.  Propagados para cima o status de reconhecimento por meio da propriedade de estado da classe base.


Um exemplo prático, isso será abordado na [usando o Touch no iOS](ios-touch-walkthrough.md) passo a passo.
