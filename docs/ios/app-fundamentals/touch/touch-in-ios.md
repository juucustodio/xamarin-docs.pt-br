---
title: Toque no iOS
ms.prod: xamarin
ms.assetid: DA666DC9-446E-4CD1-B5A0-C6FFBC7E53AD
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 9ed90a9c4ddcd398d834cb8c91553a57e7bd5ad8
ms.sourcegitcommit: a4c2a63ba76b839cda99e4474e7ab46fe307cd39
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34689535"
---
# <a name="touch-in-ios"></a>Toque no iOS

É importante entender os eventos de toque e APIs de toque em um aplicativo do iOS, como eles são essenciais para todas as interações físicas com o dispositivo. Todas as interações de toque envolvem uma `UITouch` objeto. Neste artigo, aprenderemos a usar o `UITouch` classe e suas APIs para dar suporte ao toque. Posteriormente, podemos expandirá em nosso conhecimento para saber como dar suporte a gestos.

## <a name="enabling-touch"></a>Habilitar toque

Controles em `UIKit` – essas subclasse de UIControl – é caso depende da interação do usuário que tiverem gestos internos do UIKit e, portanto, não é necessário habilitar toque. Já está habilitado.

No entanto, muitas das exibições em `UIKit` não possuem toque habilitado por padrão. Há duas maneiras de habilitar toque em um controle. O primeiro modo é verificar a caixa de seleção habilitado de interação do usuário no painel de propriedade do iOS Designer, conforme mostrado na seguinte captura de tela:

 [![](touch-in-ios-images/image1.png "Marque a caixa de seleção habilitado de interação do usuário no painel de propriedade do Designer de iOS")](touch-in-ios-images/image1.png#lightbox)

Podemos também pode usar um controlador para definir o `UserInteractionEnabled` propriedade como true em uma `UIView` classe. Isso é necessário se a interface do usuário é criado em código.

A linha de código a seguir é um exemplo:

```csharp
imgTouchMe.UserInteractionEnabled = true;
```

## <a name="touch-events"></a>Eventos de Toque

Há três fases de toque que ocorrem quando o usuário toca a tela, passa o dedo ou remove o dedo. Esses métodos são definidos no `UIResponder`, que é a classe base para UIView. iOS substituirá os métodos associados no `UIView` e `UIViewController` para lidar com toque:

-  `TouchesBegan` – Isso é chamado quando a tela é primeiro tocada.
-  `TouchesMoved` – Isso é chamado quando o local das alterações de toque como o usuário é deslizante seus dedos na tela.
-  `TouchesEnded` ou `TouchesCancelled` – `TouchesEnded` é chamado quando dedos do usuário são retirados da tela.  `TouchesCancelled` será chamado se iOS cancela o toque – por exemplo, se um usuário slides seus dedos para fora de um botão para cancelar um pressionamento.


Toque eventos viagem recursivamente para baixo pela pilha de UIViews, para verificar se o evento de toque está dentro dos limites de um objeto de exibição. Isso é geralmente chamado _testes_. Eles serão chamados pela primeira vez no nível superior `UIView` ou `UIViewController` e, em seguida, será solicitado a `UIView` e `UIViewControllers` abaixo na hierarquia do modo de exibição.

Um `UITouch` objeto será criado toda vez que o usuário toca na tela. O `UITouch` objeto inclui dados sobre o toque, como quando o toque ocorreu, onde ocorreu, se o toque foi um dedo, etc. Os eventos de toque são passados a uma propriedade de toques – um `NSSet` que contém um ou mais ajustes. Podemos usar essa propriedade para obter uma referência a um toque e determinar a resposta do aplicativo.

Classes que substituem um dos eventos toque devem primeiro chamar a implementação base e, em seguida, obter o `UITouch` objeto associado ao evento. Para obter uma referência ao toque primeiro, chame o `AnyObject` propriedade e convertê-la em um `UITouch` como mostrar no exemplo a seguir:

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

iOS reconhece automaticamente rápido sucessivo toca na tela e coletará como um toque em um único `UITouch` objeto. Isso faz com que Verificando um toque duplo tão fácil quanto verificando o `TapCount` propriedade, conforme ilustrado no código a seguir:

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

Multitoque não está habilitado por padrão em controles. Multitoque pode ser habilitada no iOS Designer, conforme ilustrado na captura de tela a seguir:

 [![](touch-in-ios-images/image2.png "Multitoque habilitado no Designer de iOS")](touch-in-ios-images/image2.png#lightbox)

Também é possível definir multitoque programaticamente, definindo o `MultipleTouchEnabled` propriedade, conforme mostrado na seguinte linha de código:

```csharp
imgTouchMe.MultipleTouchEnabled = true;
```

Para determinar quantas dedos tocadas tela, use o `Count` propriedade o `UITouch` propriedade:

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    base.TouchesBegan (touches, evt);
    lblNumberOfFingers.Text = "Number of fingers: " + touches.Count.ToString();
}
```

## <a name="determining-touch-location"></a>Determinar o local de toque

O método `UITouch.LocationInView` retorna um objeto CGPoint que contém as coordenadas de toque em um determinado modo de exibição. Além disso, podemos testar para ver se esse local está em um controle chamando o método `Frame.Contains`. O trecho de código a seguir mostra um exemplo disso:

```csharp
if (this.imgTouchMe.Frame.Contains (touch.LocationInView (this.View)))
{
    // the touch event happened inside the UIView imgTouchMe.
}
```

Agora que temos uma compreensão dos eventos toque no iOS, vamos aprender sobre reconhecedores de gestos.

## <a name="gesture-recognizers"></a>Reconhecedores de gestos

Reconhecedores de gestos consideravelmente podem simplificar e reduzir o esforço de programação para dar suporte ao toque em um aplicativo. iOS reconhecedores de gestos agregam uma série de eventos de toque em um evento de toque.

Xamarin fornece a classe `UIGestureRecognizer` como uma classe base para os seguintes identificadores de gesto internos:

-  *UITapGestureRecognizer* – isso é para um ou mais toques.
-  *UIPinchGestureRecognizer* – Pinching e propagação dedos separados.
-  *UIPanGestureRecognizer* – panorâmica ou arrastar.
-  *UISwipeGestureRecognizer* – passar o dedo em qualquer direção.
-  *UIRotationGestureRecognizer* – girando dois dedos em um movimento no sentido horário ou no sentido anti-horário.
-  *UILongPressGestureRecognizer* – pressione e segure a tecla, às vezes conhecida como uma longa pressione ou clique longa.


O padrão básico para usar um reconhecedor de gestos é o seguinte:

1.  **Instanciar o reconhecedor de gestos** – primeiro, criar uma instância de um `UIGestureRecognizer` subclasse. O objeto que é instanciado será associado por uma exibição e será lixo coletado quando a exibição é descartada. Não é necessário criar essa exibição como uma variável de nível de classe.
1.  **Defina as configurações de gesto** – a próxima etapa é configurar o reconhecedor de gestos. Consulte a documentação do Xamarin em `UIGestureRecognizer` e suas subclasses para obter uma lista de propriedades que podem ser definidas para controlar o comportamento de um `UIGestureRecognizer` instância.
1.  **Configurar o destino** – devido a sua herança Objective-C, xamarin não geram eventos quando um reconhecedor de gestos corresponde a um gesto.  `UIGestureRecognizer` tem um método – `AddTarget` – que pode aceitar um delegado anônimo ou um seletor Objective-C com o código para executar quando o reconhecedor de gestos faz uma correspondência.
1.  **Habilitar o reconhecedor de gestos** – assim como com os eventos de toque, gestos só serão reconhecidos se interações de toque estão habilitadas.
1.  **Adicionar o reconhecedor de gestos para o modo de exibição** – a etapa final é adicionar o gesto de passar para um modo de exibição chamando `View.AddGestureRecognizer` e passá-lo em um objeto do reconhecedor de gesto.

Consulte o [exemplos do reconhecedor de gestos](~/ios/app-fundamentals/touch/ios-touch-walkthrough.md#Gesture_Recognizer_Samples) para obter mais informações sobre como implementá-los no código.

Quando o destino do gesto é chamado, ela será transmitida uma referência para o gesto que ocorreu. Isso permite que o destino de gesto obter informações sobre o gesto que ocorreu. A extensão das informações disponíveis depende do tipo de reconhecedor de gestos que foi usado. Consulte a documentação do Xamarin para obter informações sobre os dados disponíveis para cada `UIGestureRecognizer` subclasse.

É importante lembrar que após a adição de um reconhecedor de gestos em uma exibição, o modo de exibição (e qualquer exibição abaixo dele) não receberá nenhum evento de toque. Para permitir que os eventos de toque simultaneamente com gestos, o `CancelsTouchesInView` propriedade deve ser definida como false, conforme ilustrado pelo código a seguir:

```csharp
_tapGesture.Recognizer.CancelsTouchesInView = false;
```

Cada `UIGestureRecognizer` tem uma propriedade de estado que fornece informações importantes sobre o status do reconhecedor de gestos. Sempre que o valor dessa propriedade for alterada, o iOS chamará o método de assinatura fornecendo uma atualização. Se um reconhecedor de gestos personalizados nunca atualiza a propriedade de estado, o assinante nunca for chamado, tornando o reconhecedor de gestos inúteis.

Gestos podem ser resumidos como um dos dois tipos:

1.  *Discreto* – esses gestos única acionar a primeira vez que forem reconhecidos.
1.  *Contínua* – esses gestos continuam a ser acionado como eles são reconhecidos.


Reconhecedores de gestos existe em um dos seguintes estados:

-  *Possíveis* – este é o estado inicial de todos os identificadores de gesto. Este é o valor padrão da propriedade State.
-  *Began* – quando um gesto contínuo é reconhecido pela primeira vez, o estado é definido como Began. Isso permite que se inscreve para diferenciar entre quando o reconhecimento de gesto inicia e quando ele é alterado.
-  *Alterado* – depois de um gesto contínuo foi iniciado, mas não foi concluída, o estado será definido para alterado toda vez que um toque move ou altera, desde que ele ainda estiver dentro de parâmetros esperados do gesto de.
-  *Cancelado* – esse estado será definido se o reconhecedor passou do Began para alterado e, em seguida, os ajustes alterados de forma como a não atender ao padrão do gesto.
-  *Reconhecido* – o estado será definido quando o reconhecedor de gestos corresponde a um conjunto de toques e informará o assinante que terminou o gesto.
-  *Terminou* – este é um alias para o estado de reconhecido.
-  *Falha* – quando o reconhecedor de gestos não pode corresponder as ajustes está escutando para, o estado será alterado para falha.


Xamarin representa esses valores no `UIGestureRecognizerState` enumeração.

## <a name="working-with-multiple-gestures"></a>Trabalhando com vários gestos

Por padrão, o iOS não permite gestos padrão sejam executados simultaneamente. Em vez disso, cada reconhecedor de gestos receberá eventos de toque em uma ordem não determinística. O trecho de código a seguir ilustra como fazer um reconhecedor de gestos executados simultaneamente:

```csharp
gesture.ShouldRecognizeSimultaneously += (UIGestureRecognizer r) => { return true; };
```

Também é possível desabilitar um gesto do iOS. Há duas propriedades de representante que permitem que um reconhecedor de gestos examinar o estado de um aplicativo e os eventos de toque atual, para tomar decisões sobre como e se um gesto deveria ser reconhecido. Os dois eventos são:

1.  *ShouldReceiveTouch* – este delegado é chamado logo antes do reconhecedor de gestos é passado a um evento de toque e fornece uma oportunidade para examinar os ajustes e decidir quais ajustes serão tratados pelo reconhecedor de gestos.
1.  *ShouldBegin* – isso é chamado quando um identificador de tentativas de alterar o estado de possíveis para outro estado. Retornar falso fará com que o estado do reconhecedor de gesto seja alterado para falha de.


Você pode substituir esses métodos com um fortemente tipada `UIGestureRecognizerDelegate`, um delegado fraco ou ligação por meio da sintaxe do manipulador de eventos, conforme ilustrado pelo seguinte trecho de código:

```csharp
gesture.ShouldReceiveTouch += (UIGestureRecognizer r, UITouch t) => { return true; };
```

Por fim, é possível enfileirar um reconhecedor de gestos para que somente funcionará se outro reconhecedor de gestos falhar. Por exemplo, um reconhecedor de gestos de toque único só deve ter sucesso quando um reconhecedor de gestos de toque duplo falha. O trecho de código a seguir fornece um exemplo disso:

```csharp
singleTapGesture.RequireGestureRecognizerToFail(doubleTapGesture);
```

## <a name="creating-a-custom-gesture"></a>Criando um gesto personalizado

Embora iOS fornece algumas padrão reconhecedores de gestos, ela poderá ser necessária criar reconhecedores de gestos personalizados em determinados casos. Criar um reconhecedor de gestos personalizado envolve as seguintes etapas:

1.  Subclasse `UIGestureRecognizer` .
1.  Substitua os métodos de evento de toque apropriado.
1.  O status de reconhecimento por meio da propriedade de estado a classe base de bolhas.


Um exemplo prático de isso será abordado a [usando tocar no iOS](ios-touch-walkthrough.md) passo a passo.
