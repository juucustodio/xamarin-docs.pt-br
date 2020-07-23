---
title: Eventos de toque e gestos no Xamarin. iOS
description: Este documento descreve como trabalhar com eventos de toque, multitoque, gestos, vários gestos e gestos personalizados em aplicativos Xamarin. iOS.
ms.prod: xamarin
ms.assetid: DA666DC9-446E-4CD1-B5A0-C6FFBC7E53AD
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 0fe6b0b46035ac61d4aaddccb585276a80337202
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86928803"
---
# <a name="touch-events-and-gestures-in-xamarinios"></a>Eventos de toque e gestos no Xamarin. iOS

É importante entender os eventos de toque e as APIs de toque em um aplicativo iOS, pois eles são centrais para todas as interações físicas com o dispositivo. Todas as interações de toque envolvem um `UITouch` objeto. Neste artigo, aprenderemos como usar a `UITouch` classe e suas APIs para dar suporte ao toque. Mais tarde, expandiremos nosso conhecimento para aprender a dar suporte a gestos.

## <a name="enabling-touch"></a>Habilitando toque

Controles em `UIKit` – aqueles com subclasse de UIControl – são tão dependentes da interação do usuário de que têm gestos internos para UIKit e, portanto, não é necessário habilitar o toque. Ele já está habilitado.

No entanto, muitas das exibições no `UIKit` não têm o Touch habilitado por padrão. Há duas maneiras de habilitar o toque em um controle. A primeira é marcar a caixa de seleção interação do usuário habilitada no painel de propriedades do designer do iOS, conforme mostrado na seguinte captura de tela:

 [![Marque a caixa de seleção habilitada para interação do usuário no painel de propriedades do designer do iOS](touch-in-ios-images/image1.png)](touch-in-ios-images/image1.png#lightbox)

Também podemos usar um controlador para definir a `UserInteractionEnabled` propriedade como true em uma `UIView` classe. Isso será necessário se a interface do usuário for criada no código.

A linha de código a seguir é um exemplo:

```csharp
imgTouchMe.UserInteractionEnabled = true;
```

## <a name="touch-events"></a>Eventos de Toque

Há três fases de toque que ocorrem quando o usuário toca na tela, move o dedo ou remove o dedo. Esses métodos são definidos em `UIResponder` , que é a classe base para UIView. o iOS substituirá os métodos associados no `UIView` e no `UIViewController` para lidar com o Touch:

- `TouchesBegan`– Isso é chamado quando a tela é tocada pela primeira vez.
- `TouchesMoved`– Isso é chamado quando o local do toque muda à medida que o usuário está deslizando seus dedos em volta da tela.
- `TouchesEnded`ou `TouchesCancelled` – `TouchesEnded` é chamado quando os dedos do usuário são levantados da tela.  `TouchesCancelled`será chamado se o iOS cancelar o toque – por exemplo, se um usuário deslizar seu dedo para longe de um botão para cancelar uma prensa.

Eventos de toque viajam recursivamente por meio da pilha de UIViews, para verificar se o evento de toque está dentro dos limites de um objeto de exibição. Isso geralmente é chamado _de teste de colisão_. Eles serão chamados primeiro na primeira `UIView` ou `UIViewController` e, em seguida, serão chamados no `UIView` e `UIViewControllers` abaixo deles na hierarquia de exibição.

Um `UITouch` objeto será criado toda vez que o usuário tocar na tela. O `UITouch` objeto inclui dados sobre o toque, como quando o toque ocorreu, onde ocorreu, se o toque foi um dedo, etc. Os eventos de toque são aprovados em uma propriedade de toques – um `NSSet` contendo um ou mais toques. Podemos usar essa propriedade para obter uma referência a um toque e determinar a resposta do aplicativo.

As classes que substituem um dos eventos de toque devem primeiro chamar a implementação base e, em seguida, obter o `UITouch` objeto associado ao evento. Para obter uma referência ao primeiro toque, chame a `AnyObject` propriedade e converta-a como a `UITouch` mostrada no exemplo a seguir:

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

o iOS reconhece automaticamente os toques rápidos na tela e os coletará como um toque em um único `UITouch` objeto. Isso torna a verificação de um toque duplo tão fácil quanto verificar a `TapCount` propriedade, conforme ilustrado no código a seguir:

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

O multitoque não está habilitado por padrão em controles. O multitoque pode ser habilitado no designer do iOS, conforme ilustrado pela seguinte captura de tela:

 [![Multi-Touch habilitado no designer do iOS](touch-in-ios-images/image2.png)](touch-in-ios-images/image2.png#lightbox)

Também é possível definir o multitoque por meio de programação, definindo a `MultipleTouchEnabled` propriedade conforme mostrado na seguinte linha de código:

```csharp
imgTouchMe.MultipleTouchEnabled = true;
```

Para determinar quantos dedos tocaram a tela, use a `Count` Propriedade na `UITouch` Propriedade:

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    base.TouchesBegan (touches, evt);
    lblNumberOfFingers.Text = "Number of fingers: " + touches.Count.ToString();
}
```

## <a name="determining-touch-location"></a>Determinando local do toque

O método `UITouch.LocationInView` retorna um objeto CGPoint que contém as coordenadas do toque dentro de uma determinada exibição. Além disso, podemos testar para ver se esse local está dentro de um controle chamando o método `Frame.Contains` . O trecho de código a seguir mostra um exemplo disso:

```csharp
if (this.imgTouchMe.Frame.Contains (touch.LocationInView (this.View)))
{
    // the touch event happened inside the UIView imgTouchMe.
}
```

Agora que temos uma compreensão dos eventos de toque no iOS, vamos aprender sobre os reconhecedores de gestos.

## <a name="gesture-recognizers"></a>Reconhecedores de gesto

Os reconhecedores de gestos podem simplificar e reduzir muito o esforço de programação para dar suporte ao toque em um aplicativo. Os reconhecedores de gestos do iOS agregam uma série de eventos de toque em um único evento de toque.

O Xamarin. iOS fornece a classe `UIGestureRecognizer` como uma classe base para os seguintes reconhecedores de gestos internos:

- *UITapGestureRecognizer* – é para um ou mais toques.
- *UIPinchGestureRecognizer* – Pinçando e espalhando os dedos.
- *UIPanGestureRecognizer* – panorâmica ou arrastar.
- *UISwipeGestureRecognizer* – passando o dedo em qualquer direção.
- *UIRotationGestureRecognizer* – girando dois dedos em um movimento no sentido horário ou no sentido anti-horário.
- *UILongPressGestureRecognizer* – pressione e segure, às vezes chamado de pressionamento longo ou de um clique longo.

O padrão básico para usar um reconhecedor de gestos é o seguinte:

1. **Criar uma instância do reconhecedor de gestos** – primeiro, crie uma instância de uma `UIGestureRecognizer` subclasse. O objeto que é instanciado será associado a uma exibição e será coletado como lixo quando a exibição for descartada de. Não é necessário criar essa exibição como uma variável de nível de classe.
1. **Definir as configurações de gesto** – a próxima etapa é configurar o reconhecedor de gestos. Consulte a documentação do Xamarin em `UIGestureRecognizer` e suas subclasses para obter uma lista de propriedades que podem ser definidas para controlar o comportamento de uma `UIGestureRecognizer` instância do.
1. **Configurar o destino** – devido à sua herança de Objective-C, o Xamarin. Ios não gera eventos quando um reconhecedor de gestos corresponde a um gesto.  `UIGestureRecognizer`tem um método – `AddTarget` que pode aceitar um delegado anônimo ou um seletor de Objective-C com o código a ser executado quando o reconhecedor de gestos faz uma correspondência.
1. **Habilitar reconhecedor de gestos** – assim como ocorre com eventos de toque, os gestos só serão reconhecidos se as interações de toque estiverem habilitadas.
1. **Adicione o reconhecedor de gestos à exibição** – a etapa final é adicionar o gesto a uma exibição chamando `View.AddGestureRecognizer` e passando um objeto de reconhecedor de gesto.

Consulte os [exemplos do reconhecedor de gesto](~/ios/app-fundamentals/touch/ios-touch-walkthrough.md#Gesture_Recognizer_Samples) para obter mais informações sobre como implementá-los no código.

Quando o destino do gesto for chamado, será passado uma referência para o gesto que ocorreu. Isso permite que o destino do gesto Obtenha informações sobre o gesto que ocorreu. A extensão das informações disponíveis depende do tipo de reconhecedor de gesto que foi usado. Consulte a documentação do Xamarin para obter informações sobre os dados disponíveis para cada `UIGestureRecognizer` subclasse.

É importante lembrar que, uma vez que um reconhecedor de gesto foi adicionado a um modo de exibição, a exibição (e qualquer exibição abaixo dele) não receberá nenhum evento de toque. Para permitir eventos de toque simultaneamente com gestos, a `CancelsTouchesInView` propriedade deve ser definida como false, conforme ilustrado pelo seguinte código:

```csharp
_tapGesture.Recognizer.CancelsTouchesInView = false;
```

Cada `UIGestureRecognizer` tem uma propriedade State que fornece informações importantes sobre o status do reconhecedor de gestos. Sempre que o valor dessa propriedade for alterado, o iOS chamará o método de assinatura, fornecendo uma atualização. Se um reconhecedor de gestor personalizado nunca atualizar a propriedade de estado, o assinante nunca será chamado, renderizando o reconhecedor de gesto inútil.

Os gestos podem ser resumidos como um dos dois tipos:

1. *Discreto* – esses gestos são acionados apenas na primeira vez em que são reconhecidos.
1. *Contínuo* – esses gestos continuam a ser acionados desde que sejam reconhecidos.

Os reconhecedores de gestos existem em um dos seguintes Estados:

- *Possível* – este é o estado inicial de todos os reconhecedores de gesto. Esse é o valor padrão da propriedade State.
- *Iniciado* – quando um gesto contínuo é reconhecido pela primeira vez, o estado é definido como iniciado. Isso permite que os assinantes diferenciem entre quando o reconhecimento do gesto é iniciado e quando ele é alterado.
- *Alterado* – depois que um gesto contínuo for iniciado, mas não tiver terminado, o estado será definido como alterado sempre que um toque for movido ou alterado, desde que ainda esteja dentro dos parâmetros esperados do gesto.
- *Cancelado* – esse Estado será definido se o reconhecedor tiver iniciado a alteração e os toques forem alterados de forma que não caibam mais no padrão do gesto.
- *Reconhecido* – o estado será definido quando o reconhecedor de gestos corresponder a um conjunto de toques e informará ao Assinante que o gesto foi concluído.
- *Concluído* – este é um alias para o estado reconhecido.
- *Falha* – quando o reconhecedor de gestos não pode mais corresponder aos toques que está ouvindo, o estado será alterado para falha.

O Xamarin. iOS representa esses valores na `UIGestureRecognizerState` enumeração.

## <a name="working-with-multiple-gestures"></a>Trabalhando com vários gestos

Por padrão, o iOS não permite que os gestos padrão sejam executados simultaneamente. Em vez disso, cada reconhecedor de gesto receberá eventos de toque em uma ordem não determinística. O trecho de código a seguir ilustra como fazer com que um reconhecedor de gestos seja executado simultaneamente:

```csharp
gesture.ShouldRecognizeSimultaneously += (UIGestureRecognizer r) => { return true; };
```

Também é possível desabilitar um gesto no iOS. Há duas propriedades delegadas que permitem que um reconhecedor de gesto examine o estado de um aplicativo e os eventos de toque atuais, para tomar decisões sobre como e se um gesto deve ser reconhecido. Os dois eventos são:

1. *ShouldReceiveTouch* – esse delegado é chamado logo antes que o reconhecedor de gestos transmita um evento de toque e fornece uma oportunidade para examinar os toques e decidir quais toques serão tratados pelo reconhecedor de gestos.
1. *ShouldBegin* – isso é chamado quando um reconhecedor tenta alterar o estado de possível para algum outro Estado. Retornar false forçará o estado do reconhecedor de gesto a ser alterado para falha.

Você pode substituir esses métodos por um tipo fortemente tipado, `UIGestureRecognizerDelegate` um delegado fraco ou associar por meio da sintaxe do manipulador de eventos, conforme ilustrado pelo seguinte trecho de código:

```csharp
gesture.ShouldReceiveTouch += (UIGestureRecognizer r, UITouch t) => { return true; };
```

Por fim, é possível colocar um reconhecedor de gesto na fila para que ele só tenha êxito se outro reconhecedor de gesto falhar. Por exemplo, um reconhecedor de gesto de toque único deve ser bem sucedido apenas quando um reconhecedor de gesto de toque duplo falha. O trecho de código a seguir fornece um exemplo disso:

```csharp
singleTapGesture.RequireGestureRecognizerToFail(doubleTapGesture);
```

## <a name="creating-a-custom-gesture"></a>Criando um gesto personalizado

Embora o iOS forneça alguns reconhecedores de gestos padrão, pode ser necessário criar reconhecedores de gestos personalizados em determinados casos. A criação de um reconhecedor de gestor personalizado envolve as seguintes etapas:

1. Subclasse `UIGestureRecognizer` .
1. Substitua os métodos de evento de toque apropriados.
1. Emergir o status de reconhecimento por meio da propriedade de estado da classe base.

Um exemplo prático disso será abordado na explicação sobre o [uso do touch no Ios](ios-touch-walkthrough.md) .
