---
title: Técnicas de interação rápida para watchOS 3 no Xamarin
description: Este artigo aborda as técnicas de interação rápida que a Apple adicionou no watchOS 3 e como implementá-las no Xamarin. iOS para Apple Watch.
ms.prod: xamarin
ms.assetid: 26697F68-AF7E-4A36-988F-85E2674A4DD1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 0252626cb58ff334e3cff2cc60e6544e4eba18c0
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435383"
---
# <a name="quick-interaction-techniques-for-watchos-3-in-xamarin"></a>Técnicas de interação rápida para watchOS 3 no Xamarin

_Este artigo aborda as técnicas de interação rápida que a Apple adicionou no watchOS 3 e como implementá-las no Xamarin. iOS para Apple Watch._

Fornecer interações rápidas do usuário é essencial para criar aplicativos Apple Watch atraentes e complicações. Novo no watchOS 3, a Apple adicionou suporte para reconhecedores de gestos, acesso ao Digital Crown e novas técnicas de notificação e navegação de usuário. Isso, juntamente com suporte adicional para SceneKit e SpriteKit, permitem que o desenvolvedor crie facilmente interfaces avançadas e resumidas que são rápidas e responsivas.

## <a name="what-are-quick-interactions"></a>O que são interações rápidas

Para um desenvolvedor que é usado para criar aplicativos para iOS ou macOS (em que a quantidade de tempo que um usuário gasta interagindo com o aplicativo é medida em minutos ou horas), a criação de um aplicativo bem-sucedido para o Apple Watch pode ser um desafio e requer uma abordagem diferente.

No watchOS, o usuário normalmente deseja elevar seu pulso, interagir rapidamente com um aplicativo (geralmente por alguns segundos) e, em seguida, derrubar o pulso e continuar o que fosse.

Veja a seguir alguns exemplos de interações rápidas típicas sobre o Apple Watch:

- Iniciando um temporizador.
- Verificando o clima.
- Marcando um item fora de uma lista de tarefas pendentes.

Para atingir essas metas, um aplicativo na Apple Watch deve ser:

- É uma visão **geral** – o que significa que, com uma visão rápida, o usuário deve ser capaz de obter as informações de que precisam. 
- **Acionável** – o que significa que os usuários devem ser capazes de tomar decisões rápidas e bem informadas.
- **Responsivo** -o que significa que o usuário nunca deve esperar para receber as informações necessárias ou para obter a ação que desejam.

### <a name="quick-interactions-length"></a>Comprimento de interações rápidas

Devido à natureza resumida dos aplicativos Apple Watch, a Apple sugere que o comprimento ideal de uma interação rápida deve ser de dois segundos ou menos. Como resultado desse limite de dois segundos, o desenvolvedor precisará gastar uma quantidade considerável de tempo criando e implementando um aplicativo Apple Watch. 

## <a name="new-watchos-3-features-and-apis"></a>Novos recursos e APIs do watchOS 3

A Apple adicionou vários novos recursos e APIs para WatchKit para auxiliar o desenvolvedor na adição de interações rápidas aos seus aplicativos Apple Watch:

- o watchOS 3 fornece acesso a novos tipos de entrada do usuário, como:
  - Reconhecedores de gesto
  - Rotação de Digital Crown 
- o watchOS 3 fornece novas maneiras de exibir e atualizar informações, como:
  - Navegação de tabela avançada
  - Novo suporte à estrutura de notificação de usuário
  - Integração de SpriteKit e SceneKit

Ao implementar esses novos recursos, o desenvolvedor pode garantir que seu aplicativo watchOS 3 seja resumido, acionável e responsivo.

### <a name="gesture-recognizer-support"></a>Suporte do reconhecedor de gesto

Se o desenvolvedor tiver implementado os reconhecedores de gestos no iOS, eles deverão estar muito familiarizados com o modo como os reconhecedores de gesto funcionam no watchOS 3. Para atualizar, os reconhecedores de gestos são objetos que analisam eventos de toque de baixo nível em gestos reconhecíveis e predefinidos.

o watchOS 3 dará suporte aos quatro reconhecedores de gestos a seguir:

- Tipos de gestos discretos:
  - Gesto de passar o dedo ( `WKSwipeGestureRecognizer` ).
  - O gesto de toque ( `WKTapGestureRecognizer` ).
- Tipos de gestos contínuos:
  - O gesto de panorâmica ( `WKPanGestureRecognizer` ).
  - O gesto de pressionamento longo ( `WKLongPressGestureRecognizer` ).

Para implementar um dos novos reconhecedores de gesto, basta arrastá-lo para uma superfície de design no designer do iOS em Visual Studio para Mac e configurar suas propriedades.

No código, responda à ação do reconhecedor para manipular o gesto que está sendo disparado pelo usuário. Novamente, isso é feito da mesma maneira como seria tratado no iOS.

#### <a name="discrete-gesture-states"></a>Estados de gesto discretos

Para gestos discretos, a ação é chamada quando o gesto é reconhecido e um estado ( `WKGestureRecognizerState` ) é atribuído como:

[![Estados de gesto discretos](quick-interaction-techniques-images/quick01.png)](quick-interaction-techniques-images/quick01.png#lightbox)

Todos os gestos discretos começam no `Possible` estado e são transferidos para o `Failed` `Recognized` estado ou. Ao usar gestos discretos, o desenvolvedor geralmente não lida diretamente com o estado. Em vez disso, eles dependem da ação que está sendo chamada quando o gesto é reconhecido apenas.

#### <a name="continuous-gesture-states"></a>Estados de gesto contínuo

Gestos contínuos são um pouco diferentes de gestos discretos, em que a ação é chamada várias vezes enquanto o gesto está sendo reconhecido:

[![Estados de gesto contínuo](quick-interaction-techniques-images/quick02.png)](quick-interaction-techniques-images/quick02.png#lightbox)

Novamente, os gestos contínuos começam no `Possible` estado, mas progredim em várias atualizações. Aqui, o desenvolvedor precisará considerar o estado do reconhecedor e atualizar a interface do usuário do aplicativo durante a `Changed` fase até que o gesto seja finalmente `Recognized` ou `Canceled` .

#### <a name="gesture-recognizer-usage-tips"></a>Dicas de uso do reconhecedor de gestos

A Apple sugere o seguinte ao trabalhar com reconhecedores de gestos no watchOS 3:

- Adicione os reconhecedores de gestos para agrupar elementos em vez de controles individuais. Como o Apple Watch tem um tamanho de tela física menor, os elementos de grupo tendem a ser alvos maiores e mais fáceis para o usuário chegar. Além disso, os reconhecedores de gestos podem entrar em conflito com gestos internos já existentes nos controles de interface do usuário nativos.
- Defina relações de dependência no storyboard do aplicativo de inspeção.
- Alguns gestos têm precedência sobre outros tipos de gestos, como:
  - Rolagem
  - Force Touch

### <a name="digital-crown-rotation"></a>Rotação de Digital Crown

Ao implementar o suporte de Digital Crown em seus aplicativos watchOS 3, um desenvolvedor pode fornecer maior velocidade de navegação e interações de precisão para seus usuários.

Desde watchOS 2, o aplicativo Apple Watch pode usar o `WKInterfacePicker` objeto para acessar o digital Crown fornecendo uma lista de `WKPickerItems` e um estilo de seletor (lista, empilhado ou sequência de imagens). em seguida, o watchOS permitiu que o usuário utilizasse o Digital Crown para selecionar um item da lista.

Ao usar um `WKInterfacePicker` , o WatchKit está lidando com a maior parte do trabalho:

- Desenhando a lista e os elementos da interface individual.
- Processando os eventos de Digital Crown.
- Chamar uma ação quando um item é selecionado.

Novo no watchOS 3, o desenvolvedor agora tem acesso direto aos eventos de rotação de Digital Crown, que permite que eles criem seus próprios elementos da interface do usuário que respondem a valores de rotação.

Digital Crown acesso é fornecido pelos seguintes elementos:

- `WKCrownSequencer` -Fornece acesso a rotações por segundo.
- `WKCrownDelegate` -Fornece acesso a eventos Delta rotacionais.

#### <a name="rotations-per-second"></a>Rotações por segundo

O acesso às rotações por segundo da Digital Crown é útil ao trabalhar com animações baseadas em física. Para acessar as rotações por segundo, use a `CrownSequencer` propriedade da `WKInterfaceController` extensão Watch. Por exemplo:

```csharp
var rotationsPerSecond = CrownSequencer.RotationsPerSecond;
```

#### <a name="rotational-deltas"></a>Deltas rotacionais

Use os deltas de rotação do Digital Crown para contar o número de rotações. Use o `CrownDidRotate` método override do `WKCrownDelegate` para acessar os deltas de rotação. Por exemplo:

```csharp
using System;
using WatchKit;
using Foundation;

namespace MonkeyWatch.MonkeySeeExtension
{
  public class CrownDelegate : WKCrownDelegate
  {
    #region Computed Properties
    public double AccumulatedRotations { get; set;}
    #endregion

    #region Constructors
    public CrownDelegate ()
    {
    }
    #endregion

    #region Override Methods
    public override void CrownDidRotate (WKCrownSequencer crownSequencer, double rotationalDelta)
    {
      base.CrownDidRotate (crownSequencer, rotationalDelta);

      // Accumulate rotations
      AccumulatedRotations += rotationalDelta;
    }
    #endregion
  }
}
```

Aqui, o aplicativo mantém um acumulador ( `AccumulatedRotations` ) para determinar o número de rotações. Uma rotação completa da Digital Crown é igual a um Delta acumulado de `1.0` e uma meia rotação seria `0.5` .

A Apple o deixou para o desenvolvedor determinar como as contagens de rotação correspondem à sensibilidade das alterações no elemento da interface do usuário que está sendo atualizado.

O sinal ( `+/-` ) do Delta rotacional indica a direção em que o usuário está ligando a digital Crown:

[![O sinal do Delta rotacional indica a direção em que o usuário está ligando a Digital Crown](quick-interaction-techniques-images/quick03.png)](quick-interaction-techniques-images/quick03.png#lightbox)

Se o usuário estiver rolando para cima, o WatchKit retornará deltas positivos e, se estiver rolando para baixo, os deltas negativos serão retornados, independentemente da orientação em que o usuário está gastando o relógio.

#### <a name="digital-crown-focus"></a>Digital Crown foco

Assim como acontece com qualquer outro elemento de interface, o Digital Crown tem o conceito de foco. Esse foco pode ser deslocado para fora do Digital Crown para outros elementos de interface com base em como o usuário está interagindo com o relógio. 

Por exemplo, qualquer um dos seguintes controles poderia roubar o foco do Digital Crown:

- Seletor
- Controle deslizante
- Controlador de rolagem

Cabe ao desenvolvedor determinar quando seu elemento de interface personalizado precisa ser o foco do Digital Crown. A Apple sugere o uso dos novos reconhecedores de gestos para obter foco no elemento de interface do usuário personalizado.

### <a name="vertical-paging"></a>Paginação vertical

A maneira padrão de o usuário navegar em um modo de exibição de tabela em um aplicativo watchOS é rolar para a parte desejada de dados, tocar em uma linha específica para exibir a exibição detalhada, tocar no botão voltar quando terminar de exibir os detalhes e repetir o processo para quaisquer outras informações em que estejam interessados dentro da tabela:

[![Movendo entre uma tabela e a exibição de detalhes](quick-interaction-techniques-images/quick04.png)](quick-interaction-techniques-images/quick04.png#lightbox)

Novo no watchOS 3, o desenvolvedor pode habilitar a paginação vertical em seus controles de exibição de tabela. Com esse recurso habilitado, o usuário pode rolar para localizar uma linha de exibição de tabela e tocar na linha para exibir seus detalhes como antes. No entanto, agora eles podem passar por cima para selecionar a próxima linha na tabela ou para baixo para selecionar a linha anterior (ou usar o Digital Crown), tudo sem precisar retornar para a exibição de tabela primeiro:

[![Movendo entre uma tabela e a exibição de detalhes e passando o dedo para cima e para baixo para se mover entre as outras linhas](quick-interaction-techniques-images/quick05.png)](quick-interaction-techniques-images/quick05.png#lightbox)

Para habilitar esse modo, abra o storyboard do aplicativo watchOS no Xcode para edição, selecione a exibição de tabela e marque a caixa de seleção de **paginação de detalhes vertical** :

[![Marque a caixa de seleção de paginação de detalhes vertical](quick-interaction-techniques-images/quick06.png)](quick-interaction-techniques-images/quick06.png#lightbox)

Verifique se a tabela está usando continuações para exibir a exibição detalhada e salvar as alterações no storyboard e retornar ao Visual Studio para Mac para sincronização.

O desenvolvedor pode envolver programaticamente a paginação vertical em uma linha específica usando o seguinte código em relação a uma exibição de tabela:

```csharp
// Segue into Vertical Paging and select the first row
MenuTable.PerformSegue (0);
```

Ao usar a paginação vertical, o desenvolvedor precisa estar ciente de que o WatchKit manipulará automaticamente o pré-carregamento de controladores e, como resultado, alguns métodos de ciclo de vida do controlador podem ser chamados antes que a interface do usuário seja realmente visível.

### <a name="notification-enhancements"></a>Aprimoramentos de notificação

A notificação é a forma principal de interação rápida que um usuário normalmente enfrenta no watchOS e que esteve disponível desde o primeiro Apple Watch e o watchOS 1.

Uma interação rápida de notificação típica é a seguinte:

1. O usuário sentirá a notificação haptic quando uma nova notificação for recebida.
2. Eles geram o pulso para ver a interface visual curta da notificação.
3. Se eles continuarem a manter seu pulso elevado, o watchOS faz a transição automaticamente para a interface de notificação de aparência longa.

Há várias maneiras pelas quais um usuário pode responder à notificação:

- Para uma notificação bem definida e apresentada, o usuário não fará nada e simplesmente descartará a notificação.
- Eles também podem tocar na notificação para iniciar o aplicativo watchOS.
- Para uma notificação que dá suporte a ações personalizadas, o usuário pode selecionar uma das ações personalizadas. Eles podem ser:
  - **Ações de primeiro plano** -essas iniciam o aplicativo para executar a ação.
  - **Ações em segundo plano** -foram sempre roteadas para o iPhone no watchOS 2, mas podem ser roteadas para o WatchApp no watchOS 3.

Novo para watchOS 3:

- A notificação usa uma API semelhante em todas as plataformas (iOS, watchOS, tvOS e macOS).
- A notificação local pode ser agendada na Apple Watch.
- A notificação em segundo plano será roteada para a extensão do aplicativo se ela estiver agendada na Apple Watch.

#### <a name="notification-scheduling-and-delivery"></a>Agendamento e entrega de notificações

A notificação do iPhone do usuário será encaminhada para a Apple Watch quando ocorrer o seguinte:

- A tela do iPhone está desligada.
- O Apple Watch está sendo gasto e foi desbloqueado.

No watchOS 3, as notificações locais podem ser agendadas na Apple Watch e são fornecidas apenas na inspeção. É o desenvolvedor agendar uma notificação do iPhone correspondente se for exigido pelo aplicativo.

Ao incluir o mesmo identificador de notificação nas versões Apple Watch e iPhone das notificações, ele impede que notificações duplicadas sejam exibidas no relógio. A versão Apple Watch da notificação terá precedência sobre a versão do iPhone.

Como o watchOS 3 usa a mesma `UINotification` estrutura de API que o Ios 10, consulte nossa documentação da [estrutura de notificação de usuário](~/ios/platform/user-notifications/index.md) do IOS 10 para obter mais detalhes.

### <a name="using-spritekit-and-scenekit"></a>Usando SpriteKit e SceneKit

Novo no watchOS 3, o desenvolvedor agora pode usar os objetos SpritKit e SceneKit no design da interface do usuário do aplicativo para apresentar gráficos 2D e 3D.

Duas novas classes de interface foram adicionadas para dar suporte a esse recurso:

- `WKInterfaceSKScene` -Para trabalhar com gráficos SpriteKit 2D.
- `WKInterfaceSCNScene` -Para trabalhar com gráficos SceneKit 3D.

Para usar esses objetos, basta arrastá-los para a superfície de design dentro do storyboard do aplicativo Watch no Interface Builder do Xcode e usar o **Inspetor de atributos** para configurá-los.

A partir desse ponto, trabalhar com os bastidores SpriteKit ou SceneKit funciona da mesma forma que dentro de um aplicativo iOS. O aplicativo Watch apresentará um `WKInterfaceSKScene` chamando um dos `Present` métodos. Para SceneKit, basta definir a `Scene` Propriedade do `WKInterfaceSCNScene` objeto.

## <a name="actionable-complications"></a>Complicações acionáveis

No watchOS 2, a Apple introduziu complicações para aplicativos de terceiros. No watchOS 3, a Apple expandiu as capacidades que um desenvolvedor pode incluir em uma complicação de WatchKit. 

Além disso, mais dos rostos de inspeção internos agora podem incluir complicações e rostos de inspeção existentes que já têm suporte às complicações podem agora ser incluídas ainda mais complicações.

Além disso, o novo é a capacidade de um usuário passar rapidamente à esquerda ou à direita para fazer a transição por todas as faces de inspeção instaladas em seus Apple Watch. Usando a nova galeria no aplicativo de iPhone do Apple Watch Companion, o usuário pode adicionar e personalizar novos rostos de inspeção e qualquer uma das complicações que eles podem incluir.

Por causa desses novos recursos, a Apple sugere que cada aplicativo em Apple Watch também deve incluir pelo menos uma complicação e, como tal, todo o aplicativo de Apple Watch nativo agora tem complicações.

As complicações fornecem os seguintes recursos para um aplicativo:

- Eles são altamente resumidos, pois estão sempre presentes no rosto de inspeção.
- As complicações são frequentemente atualizadas pelo watchOS. Qualquer aplicativo que inclua uma complicação na face de inspeção atualmente exibida do usuário é atualizado pelo menos duas vezes por hora.
- Qualquer aplicativo com uma complicação na face de inspeção atualmente exibida do usuário é mantido na memória, o que faz com que o aplicativo seja iniciado rapidamente e aumente a velocidade das respostas do aplicativo.
- As complicações facilitam o usuário de iniciar uma funcionalidade específica em um aplicativo watchOS.

## <a name="glanceable-notification"></a>Notificação de visualização

A notificação em Apple Watch fornece uma maneira ótima e personalizável para informar rapidamente o usuário de eventos ou novas informações, como mensagens de entrada ou atingir uma meta em um aplicativo de treinamento.

Usando uma notificação, informações valiosas podem ser rapidamente apresentadas ao usuário. Em muitas situações, uma notificação bem projetada pode remover a necessidade do usuário de realmente iniciar o aplicativo.

Novo no watchOS 3, todas as notificações agora dão suporte a:

- SpriteKit
- SceneKit
- Vídeo embutido

## <a name="enhanced-ui-with-spritekit-and-scenekit"></a>Interface do usuário aprimorada com SpriteKit e SceneKit

Normalmente, um desenvolvedor pode considerar a interface do usuário do jogo quando SpriteKit e SceneKit são mencionados. No entanto, tanto SpriteKit quanto SceneKit podem ser úteis para a criação de UIs que não sejam de jogos que incluem layouts personalizados, conteúdo e animações que, de outra forma, não são possíveis no WatchKit sozinho.

Por exemplo, uma notificação de usuário de um aplicativo de compartilhamento de fotos pode usar o SpriteKit para fornecer uma experiência de usuário avançada, incluindo o usuário que postou a imagem junto com uma imagem real e outras informações personalizadas que enriquecem a experiência do usuário.

Além disso, SpriteKit e SceneKit podem ser misturados com os elementos da interface do usuário do WatchKit padrão no design da interface de usuários do aplicativo.

## <a name="simple-navigation"></a>Navegação simples

o watchOS 3 apresenta várias maneiras pelas quais um desenvolvedor pode simplificar a navegação dentro de seus aplicativos watchOS, como a nova [paginação vertical](#vertical-paging), o [suporte do reconhecedor de gestos](#gesture-recognizer-support) e os recursos de [rotação de digital Crown](#digital-crown-rotation) apresentados acima.

O Digital Crown é exclusivo para o Apple Watch e pode ser usado de várias maneiras diferentes para simplificar a navegação. Por exemplo, um aplicativo de timer pode usar o Digital Crown para depurar por meio de comprimentos de temporizador disponíveis.

Os gestos personalizados podem apresentar maneiras novas e exclusivas para o usuário interagir com um aplicativo Watch e também pode ser usado para simplificar a navegação do aplicativo.

A Apple sugere a busca de maneiras de combinar todos os novos recursos de interação rápida adicionados no watchOS 3 para apresentar as interfaces de aplicativo watchOS avançadas, fáceis e rápidas de usar.

## <a name="finishing-the-quick-interaction"></a>Concluindo a interação rápida

Uma experiência de interação rápida bem projetada dará ao usuário a confiança de descartar seus pulsos (e soltar com o aplicativo) quando tiver concluído a interação atual.

Onde isso se torna especificamente um problema quando o aplicativo Watch está fazendo qualquer tipo de conexão de rede ou compartilhando informações com seu aplicativo complementar do iPhone. Isso geralmente pode levar a um indicador de espera enquanto a transação está ocorrendo, o que não é desejável durante uma interação rápida. Veja o exemplo seguinte:

[![Diagrama do aplicativo Watch fazendo uma conexão de rede e compartilhando informações com seu aplicativo complementar do iPhone](quick-interaction-techniques-images/quick07.png)](quick-interaction-techniques-images/quick07.png#lightbox)

1. O usuário escolhe um item para comprar no relógio.
2. Eles tocam no botão comprar.
3. O aplicativo inicia a transação de rede e exibe um indicador de carregamento.
4. Algum tempo depois, a transação é concluída e o aplicativo exibe uma conformidade de compra.
5. O usuário descarta o pulso e se desconecta com o aplicativo.

A partir do momento em que o usuário toca no botão comprar até que a transação seja concluída, eles têm o pulso levantado examinando um indicador de carregamento. Para resolver essa situação, a Apple sugere que o desenvolvedor deve apresentar comentários instantâneos para o usuário, em vez de mostrar um indicador de carregamento. 

Usando o modelo sugerido da Apple, dê uma olhada na mesma interação rápida novamente:

[![Diagrama de modelo sugerido por maçãs](quick-interaction-techniques-images/quick08.png)](quick-interaction-techniques-images/quick08.png#lightbox)

1. O usuário escolhe um item para comprar no relógio.
2. Eles tocam no botão comprar.
3. O aplicativo inicia a transação de rede e exibe uma mensagem dizendo que a compra foi iniciada com êxito.
4. O usuário descarta o pulso e se desconecta com o aplicativo.
5. Quando a transação for concluída com êxito algum tempo posteriormente, o aplicativo exibirá uma notificação local para informar ao usuário de uma compra bem-sucedida.

Desta vez, assim que o usuário toca no botão comprar, ele mostra uma mensagem informando que a compra foi iniciada, para que possa descartar com segurança seu pulso e terminar a interação rápida neste ponto. Posteriormente, eles são informados sobre o êxito ou a falha da transação em uma notificação do usuário. Dessa forma, o usuário está apenas interagindo com o aplicativo durante as fases "ativas" do processo.

Para aplicativos que estão fazendo a rede, eles podem usar um plano de fundo `NSURLSession` para lidar com a comunicação de rede com uma tarefa de download. Isso permitirá que o aplicativo seja ativadosdo em segundo plano para processar as informações baixadas. Para o aplicativo que requer processamento em segundo plano, use uma declaração de tarefa em segundo plano para manipular o processamento necessário.

## <a name="quick-interaction-design-tips"></a>Dicas de design de interação rápida

Como o comprimento desejado de uma interação rápida é de dois segundos ou menos, o desenvolvedor deve se concentrar no design das interações do aplicativo desde o início do processo de design. Encontre áreas em que essas interações podem ser simplificadas (usando a técnica apresentada acima) e use os novos recursos do watchOS 3 para tornar o aplicativo rápido e responsivo.

A Apple sugere o seguinte:

- Concentre-se em interações rápidas colocando os recursos mais usados do aplicativo em frente.
- Use complicações e notificações do usuário para trazer recursos e funções comuns.
- Crie uma interface do usuário rica e resumida com SceneKit e SpriteKit.
- Sempre que possível, Simplifique a navegação dentro do aplicativo.
- Nunca faça o usuário esperar, permita que eles descartam seus pulsos e se soltem com o aplicativo assim que possível.

## <a name="summary"></a>Resumo

Este artigo abordou as técnicas de interação rápida que a Apple adicionou no watchOS 3 e como implementá-las no Xamarin. iOS para Apple Watch.

## <a name="related-links"></a>Links Relacionados

- [Exemplos de watchOS](/samples/browse/?products=xamarin&term=Xamarin.iOS%2bwatchOS)