---
title: Técnicas de interação rápida para watchOS 3 no Xamarin
description: Este artigo aborda as técnicas de interação rápida Apple foi adicionado no watchOS 3 e como implementá-los no xamarin. IOS para Apple Watch.
ms.prod: xamarin
ms.assetid: 26697F68-AF7E-4A36-988F-85E2674A4DD1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 5086724b565fb95274c4988ca1b6e4bb11064575
ms.sourcegitcommit: 946ce514fd6575aa6b93ff24181e02a60b24b106
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2019
ms.locfileid: "58677931"
---
# <a name="quick-interaction-techniques-for-watchos-3-in-xamarin"></a>Técnicas de interação rápida para watchOS 3 no Xamarin

_Este artigo aborda as técnicas de interação rápida Apple foi adicionado no watchOS 3 e como implementá-los no xamarin. IOS para Apple Watch._

Interações do usuário rápida fornecendo são essenciais para criar aplicativos atraentes do Apple Watch e complicações. Novo ao watchOS 3, a Apple adicionou suporte para reconhecedores de gestos, acesso aos Crown Digital e novas técnicas de notificação do usuário e a navegação. Isso, juntamente com o suporte adicionado para SceneKit e SpriteKit, permitem ao desenvolvedor criar com facilidade interfaces avançadas e glanceable que são rápidos e responsivos.

## <a name="what-are-quick-interactions"></a>Quais são as interações rápido

Para um desenvolvedor que é usado para a criação de aplicativos para iOS ou macOS (em que a quantidade de tempo que um usuário gasta interagindo com o aplicativo é medida em minutos ou horas), criando um aplicativo com êxito para o Apple Watch pode ser um desafio e requer uma opção diferente abordagem.

No watchOS, o usuário normalmente deseja elevar seu pulso, rapidamente interagir com um aplicativo (geralmente por um breve alguns segundos), em seguida, descartar sua pulso e continuar a tudo o que era que estava fazendo.

Seguem alguns exemplos de interações típicas de rápida sobre o Apple Watch:

- Iniciar um timer.
- Verificando o clima.
- Marcar um item fora de uma lista de tarefas pendentes.

Para atingir essas metas, um aplicativo na Apple Watch deve ser:

- **Glanceable** -que significa que, com uma rápida visão do usuário deve ser capaz de obter as informações que eles precisam. 
- **Acionáveis** -que significa que os usuários deve ser capaz de tomar decisões rápidas e bem informadas.
- **Responsivo** -que significa que o usuário nunca deve aguardar para receber as informações de que necessitam ou para obter a ação desejada.

### <a name="quick-interactions-length"></a>Comprimento de interações rápida

Devido à natureza glanceable de aplicativos Apple Watch, a Apple sugere que o comprimento ideal de uma interação rápida deve ser de dois segundos ou menos. Como resultado desse limite de segundo de dois, o desenvolvedor precisará gastar uma quantidade considerável de tempo tanto Projetando e implementando um aplicativo de Apple Watch. 

## <a name="new-watchos-3-features-and-apis"></a>Novo watchOS 3 recursos e APIs

Apple adicionou vários recursos novos e APIs para WatchKit para ajudar o desenvolvedor adicionando interações rápidas a seus aplicativos do Apple Watch:

- watchOS 3 fornece acesso a novos tipos de entrada como de usuário:
    - Reconhecedores de gestos
    - Rotação de coroa digital 
- watchOS 3 fornece novas maneiras de exibir e atualizar informações, tais como:
    - Navegação aprimorada de tabela
    - Novo suporte de estrutura de notificação do usuário
    - Integração do SpriteKit e SceneKit

Implementando esses novos recursos, o desenvolvedor pode garantir que seu aplicativo watchOS 3 é Glanceable, prático e responsiva.

### <a name="gesture-recognizer-support"></a>Suporte do reconhecedor de gestos

Se o desenvolvedor tiver implementado reconhecedores de gestos no iOS, eles devem ser muito familiarizados com como reconhecedores de gestos funciona no watchOS 3. Para atualizar, reconhecedores de gestos são objetos que analisar eventos de toque de nível baixo em gestos reconhecíveis, previamente definidos.

watchOS 3 dará suporte a quatro seguintes reconhecedores de gestos:

- Tipos de gestos discreto:
    - O gesto de passar o dedo (`WKSwipeGestureRecognizer`).
    - O gesto de toque (`WKTapGestureRecognizer`).
- Tipos de gesto contínua:
    - O gesto de Pan (`WKPanGestureRecognizer`).
    - O gesto de pressionar longa (`WKLongPressGestureRecognizer`).

Para implementar um dos novos reconhecedores de gestos, simplesmente arraste-o para uma superfície de design no iOS Designer no Visual Studio para Mac e configure suas propriedades.

No código, responda à ação do reconhecedor para lidar com o gesto que está sendo disparado pelo usuário. Novamente, isso é feito da mesma forma como ele pode ser tratado no iOS.

#### <a name="discrete-gesture-states"></a>Estados de gesto discretos

Para gestos discretos, a ação é chamada quando o gesto é reconhecido e um estado (`WKGestureRecognizerState`) é atribuído como:

[![](quick-interaction-techniques-images/quick01.png "Estados de gesto discretos")](quick-interaction-techniques-images/quick01.png#lightbox)

Todos os gestos discretos começam `Possible` estado e fazer a transição para um a `Failed` ou `Recognized` estado. Ao usar gestos discretos, o desenvolvedor geralmente não lida diretamente com o estado. Em vez disso, eles confiam na ação que está sendo chamada quando o gesto é reconhecido somente.

#### <a name="continuous-gesture-states"></a>Estados de gesto contínua

Gestos contínuos são ligeiramente diferentes de gestos discreta, em que a ação é chamada várias vezes conforme o gesto está sendo reconhecido:

[![](quick-interaction-techniques-images/quick02.png "Estados de gesto contínua")](quick-interaction-techniques-images/quick02.png#lightbox)

Novamente, gestos contínua começa no `Possible` estado, mas eles progresso ao longo de várias atualizações. Aqui o desenvolvedor precisará considerar o estado do reconhecedor e atualizar a interface de usuário do aplicativo durante o `Changed` fase até que o gesto é finalmente `Recognized` ou `Canceled`.

#### <a name="gesture-recognizer-usage-tips"></a>Dicas de uso do reconhecedor de gestos

Apple sugere o seguinte ao trabalhar com reconhecedores de gestos no watchOS 3:

- Adicione os reconhecedores de gestos para agrupar elementos em vez de controles individuais. Como o Apple Watch tem um tamanho menor da tela física, elementos de grupo tendem a ser maiores e mais fáceis de destinos para o usuário pressionar. Além disso, os reconhecedores de gestos podem entrar em conflito com gestos já está em controles nativos da interface do usuário interna.
- Definir relações de dependência no Storyboard do aplicativo watch.
- Alguns gestos têm precedência sobre outros tipos de gesto, tais como:
    - Rolagem
    - Forçar um toque
 
### <a name="digital-crown-rotation"></a>Rotação da coroa digital

Implementando o suporte da coroa Digital em seus aplicativos do watchOS 3, um desenvolvedor pode fornecer navegação maior velocidade e precisão interações para seus usuários.

Desde o watchOS 2, o aplicativo de Apple Watch poderia usar o `WKInterfacePicker` objeto para acessar o Crown Digital, fornecendo uma lista de `WKPickerItems` e um estilo seletor (lista, empilhada ou sequência de imagem). watchOS permitido, em seguida, o usuário use o Crown Digital para selecionar um item da lista.

Ao usar um `WKInterfacePicker`, WatchKit está tratando a maior parte do trabalho por:

- A lista e os elementos individuais de interface de desenho.
- Processamento dos eventos da coroa Digital.
- Chamar uma ação quando um item é selecionado.

Novo ao watchOS 3, o desenvolvedor agora tem acesso direto aos eventos de rotação Crown Digital que permite que eles criem seus próprios elementos de interface do usuário que respondem aos valores de rotação.

Acesso digital da coroa é fornecido pelos seguintes elementos:

- `WKCrownSequencer` -Fornece acesso a rotações por segundo.
- `WKCrownDelegate` -Fornece acesso aos eventos de delta rotacional.

#### <a name="rotations-per-second"></a>Rotações por segundo

Acessando as rotações por segundo do Crown Digital é útil ao trabalhar com física com base em animações. Para acessar as rotações por segundo, use o `CrownSequencer` propriedade do `WKInterfaceController` da extensão de inspeção. Por exemplo:

```csharp
var rotationsPerSecond = CrownSequencer.RotationsPerSecond;
```

#### <a name="rotational-deltas"></a>Deltas rotacional

Use os Deltas rotacional da coroa a Digital para contar o número de rotações. Use o `CrownDidRotate` substituir método da `WKCrownDelegate` para acessar os Deltas rotacional. Por exemplo:

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

Aqui, o aplicativo mantém um acumulador (`AccumulatedRotations`) para determinar o número de rotações. Uma rotação completa da coroa Digital é igual a um delta acumulado de `1.0` e uma rotação metade seria `0.5`.

Apple deixou cargo do desenvolvedor para determinar como as contagens de rotação correspondem a confidencialidade das alterações no elemento de interface do usuário que está sendo atualizado.

O sinal (`+/-`) do Delta rotacional indica a direção que o usuário está se tornando o Crown Digital:

[![](quick-interaction-techniques-images/quick03.png "O sinal de Delta rotacional indica a direção que o usuário está se tornando o Crown Digital")](quick-interaction-techniques-images/quick03.png#lightbox)


Se o usuário está rolando para cima, WatchKit retornará deltas positivos e se rolando para baixo, em seguida, deltas negativos serão retornados, não importa quais orientação o usuário está usando a inspeção em.

#### <a name="digital-crown-focus"></a>Foco da coroa digital

Assim como quaisquer outros elementos de interface, a Digital Crown tem o conceito de foco. Esse foco pode ser deslocado o Crown Digital a outros elementos de interface com base em como o usuário está interagindo com o relógio. 

Por exemplo, qualquer um dos seguintes controles poderia roubar o foco o Crown Digital:

- Seletor
- Controle deslizante
- Controlador de rolagem

Cabe ao desenvolvedor determinar quando seu elemento de interface personalizada precisa ser o foco o Crown Digital. A Apple sugere usando o novo reconhecedores de gestos para adquirir o foco no elemento de interface do usuário personalizado.

### <a name="vertical-paging"></a>Paginação vertical

O modo padrão que um usuário navega de uma exibição de tabela em um aplicativo do watchOS é role até a parte dos dados desejado, toque em uma linha específica para exibir a exibição detalhada, toque no botão Voltar quando terminar de exibir os detalhes e repita o processo para qualquer outra informação que o y está interessado de dentro da tabela:

[![](quick-interaction-techniques-images/quick04.png "Movendo entre uma tabela e a exibição de detalhes")](quick-interaction-techniques-images/quick04.png#lightbox)

Novo ao watchOS 3, o desenvolvedor pode habilitar a paginação Vertical em seus controles de exibição de tabela. Com esse recurso habilitado, o usuário pode rolar para localizar uma linha do modo de exibição de tabela e toque em linha para exibir seus detalhes como antes. No entanto, eles podem agora passa o dedo para cima para selecionar a próxima linha na tabela ou para baixo para selecionar a linha anterior (ou use o Crown Digital), tudo isso sem precisar retornar à exibição de tabela pela primeira vez:

[![](quick-interaction-techniques-images/quick05.png "Movendo entre uma tabela e a exibição de detalhes e passar o dedo para cima para baixo para mover entre as outras linhas")](quick-interaction-techniques-images/quick05.png#lightbox)

Para habilitar esse modo, abra o Storyboard do aplicativo watchOS para edição no Xcode, selecione o modo de exibição de tabela e marque os **paginação Vertical de detalhes** caixa de seleção:

[![](quick-interaction-techniques-images/quick06.png "Marque a caixa de seleção de paginação Vertical de detalhes")](quick-interaction-techniques-images/quick06.png#lightbox)

Certifique-se de que a tabela está usando transições para mostrar a exibição detalhada e salvar as alterações ao Storyboard e retorne ao Visual Studio para Mac para sincronizar.

O desenvolvedor por meio de programação pode envolver paginação Vertical para uma linha específica usando o seguinte código em relação a uma exibição de tabela:

```csharp
// Segue into Vertical Paging and select the first row
MenuTable.PerformSegue (0);
```

Ao usar a paginação Vertical, o desenvolvedor precisa estar ciente que WatchKit manipulará automaticamente o pré-carregamento dos controladores e como resultado, alguns métodos de ciclo de vida do controlador podem ser chamados antes que a interface do usuário seja realmente visível.

### <a name="notification-enhancements"></a>Aprimoramentos de notificação

Notificação são a principal forma de interação rápida que um usuário geralmente apresenta no watchOS e estão disponível desde a primeira de Apple Watch e watchOS 1.

Uma interação rápida de notificação típico é da seguinte maneira:

1. O usuário sentir Hápticos a notificação quando uma nova notificação é recebida.
2. Eles geram seu pulso para ver a interface parecer curto para a notificação.
3. Se eles continuam a manter seu pulso gerado, watchOS automaticamente faz a transição para a interface de notificação de parecer longa.

Há várias maneiras que um usuário possa responder à notificação:

- Para um bem definidas e apresentada a notificação, o usuário não faça nada e simplesmente ignorar a notificação.
- Eles também podem tocar da notificação para iniciar o aplicativo watchOS.
- Para uma notificação que dá suporte a ações personalizadas, o usuário pode selecionar uma das ações personalizadas. Eles podem ser:
    - **Ações de primeiro plano** -eles iniciam o aplicativo para executar a ação.
    - **Ações do plano de fundo** – foram sempre roteadas para o iPhone no watchOS 2, mas podem ser roteadas para o watchApp no watchOS 3.

Novo para watchOS 3:

* Notificação de usar uma API semelhante entre todas as plataformas (iOS, watchOS, tvOS e macOS).
* Notificação de local pode ser agendada na Apple Watch.
* Notificação de plano de fundo será roteada para a extensão do aplicativo se elas foram agendadas na Apple Watch.

#### <a name="notification-scheduling-and-delivery"></a>Notificação de agendamento e entrega

Notificação do iPhone do usuário será encaminhar para o Apple Watch, quando ocorre o seguinte:

* Tela do iPhone está desativado.
* O Apple Watch estiver sendo usado e foi desbloqueada.

No watchOS 3, notificações locais podem ser agendadas no Apple Watch e só são entregues no relógio. Cabe ao desenvolvedor de agendar um notificação do iPhone correspondente, se for exigida pelo aplicativo.

Ao incluir o mesmo identificador de notificação no Apple Watch e iPhone versões das notificações, impede que as notificações duplicadas sejam exibidas no relógio. A versão do Apple Watch da notificação terá precedência sobre a versão para iPhone.

Como watchOS 3 usa os mesmos `UINotification` estrutura da API como iOS 10, consulte nosso iOS 10 [estrutura de notificação do usuário](~/ios/platform/user-notifications/index.md) documentação para obter mais detalhes.

### <a name="using-spritekit-and-scenekit"></a>Usando o SpriteKit e SceneKit

Novo ao watchOS 3, o desenvolvedor pode agora usar objetos SpritKit e SceneKit no design de Interface do usuário do seu aplicativo apresentar gráficos 2D e 3D.

Duas novas classes de interface foram adicionadas para dar suporte a esse recurso:

- `WKInterfaceSKScene` – Para trabalhar com gráficos 2D de SpriteKit.
- `WKInterfaceSCNScene` – Para trabalhar com gráficos 3D do SceneKit.

Para usar esses objetos, basta arrastá-los para a superfície de design dentro Storyboard do aplicativo watch no Interface Builder do Xcode e usar o **Inspetor de atributos** configurá-los.

Desse ponto, trabalhar com o SpriteKit ou SceneKit cenas funciona da mesma forma como acontece dentro de um aplicativo iOS. O aplicativo watch apresentará uma `WKInterfaceSKScene` chamando um do `Present` métodos. Para o SceneKit, basta definir a `Scene` propriedade do `WKInterfaceSCNScene` objeto.

## <a name="actionable-complications"></a>Complicações acionáveis

No watchOS 2, a Apple introduziu complicações para os aplicativos de terceiros 3ª. No watchOS 3, a Apple expandiu as habilidades que um desenvolvedor pode incluir em uma complicação de WatchKit. 

Além disso, mais da compilação na inspeção faces agora podem incluir complicações e faces de inspeção existente que já complicações com suporte podem incluídos complicações ainda mais.

Outra novidade é a capacidade de um usuário para rapidamente passe o dedo para esquerda ou direito para fazer a transição por todas as faces de inspeção, que eles têm instalado em seu Apple Watch. Usando a Galeria de novo no aplicativo de iPhone complementar do de Apple Watch, o usuário pode adicionar e personalizar novos mostradores de relógio e qualquer uma das complicações que podem incluir.

Devido a esses novos recursos, a Apple sugere que todos os aplicativos do Apple Watch também devem incluir pelo menos uma complicação e como tal, todos do aplicativo nativo da Apple Watch agora tem complicações.

Complicações fornecem os seguintes recursos para um aplicativo:

- Eles são altamente glanceable, pois eles estão sempre presentes na face do relógio.
- Complicações com frequência são atualizadas pelo watchOS. Qualquer aplicativo que inclui uma complicação no Mostrador exibido no momento do usuário é atualizado pelo menos duas vezes em uma hora.
- Qualquer aplicativo com uma complicação no Mostrador exibido no momento do usuário é mantido na memória que faz com que o aplicativo Iniciar rapidamente e melhora a velocidade de respostas do aplicativo.
- Complicações tornam mais fácil para o usuário inicie a funcionalidade específica em um aplicativo do watchOS.

## <a name="glanceable-notification"></a>Notificação glanceable

Notificação no Apple Watch fornecem uma maneira excelente e personalizável para informar o usuário de eventos ou novas informações como mensagens de entrada ou para atingir um objetivo em um aplicativo de ginástica rapidamente.

Por meio de uma notificação, informações valiosas podem ser rapidamente apresentadas ao usuário. Em muitas situações, uma notificação bem projetada pode remover a necessidade do usuário realmente inicia o aplicativo.

Novo no watchOS 3, todas as notificações agora têm suporte para:

- SpriteKit
- SceneKit
- Vídeo embutido

## <a name="enhanced-ui-with-spritekit-and-scenekit"></a>Interface do usuário avançada com SpriteKit e SceneKit

Normalmente, um desenvolvedor pode pensar em jogo da interface do usuário quando SpriteKit e SceneKit são mencionados. No entanto, SpriteKit e SceneKit podem ser útil para criar interfaces do usuário que incluem layouts personalizados, conteúdos de não relacionados a jogos e animações que não são possíveis no WatchKit sozinho.

Por exemplo, uma notificação de usuário de um aplicativo de compartilhamento de fotos pode usar SpriteKit para fornecer uma experiência de usuário, incluindo o usuário que postou a imagem, juntamente com uma imagem real e outras informações personalizadas que aprimora a experiência do usuário.

Além disso, SpriteKit e SceneKit podem ser combinados com elementos padrão da UI WatchKit no design de Interface do usuário do aplicativo.

## <a name="simple-navigation"></a>Navegação simples

watchOS 3 apresenta várias maneiras que um desenvolvedor pode simplificar a navegação dentro de seus aplicativos watchOS, como o novo [paginação Vertical](#vertical-paging), [suporte do reconhecedor de gestos](#gesture-recognizer-support) e [Crown Digital Rotação](#digital-crown-rotation) recursos apresentados acima.

O Crown Digital é exclusivo para o Apple Watch e pode ser usado de várias maneiras para simplificar a navegação. Por exemplo, um aplicativo de temporizador pode usar o Crown Digital para depurar por meio de comprimentos de temporizador disponíveis.

Gestos personalizados podem apresentar maneiras novas e exclusivas para o usuário interagir com um aplicativo de inspeção e também podem ser usados para simplificar a navegação do aplicativo.

Apple sugere procurando maneiras de combinar todos os novos recursos de interação rápida adicionados no watchOS 3 para apresentar avançada, fácil e rápido usar interfaces de aplicativo do watchOS.

## <a name="finishing-the-quick-interaction"></a>Concluindo a interação rápida

Uma experiência de interação rápida bem projetado concederá ao usuário a confiança para descartar sua pulso (e desencaixá-lo com o aplicativo) quando eles terminam a interação atual.

Onde isso especificamente se torna um problema é quando o aplicativo watch é fazer qualquer tipo de conexão de rede ou compartilhar informações com seu aplicativo de iPhone complementar. Geralmente, isso pode levar a um indicador de espera enquanto a transação está em andamento, o que não é desejável durante uma interação rápida. Veja o exemplo a seguir:

[![](quick-interaction-techniques-images/quick07.png "Diagrama do aplicativo watch fazendo uma conexão de rede e compartilhamento de informações com seu aplicativo de iPhone complementar")](quick-interaction-techniques-images/quick07.png#lightbox)

1. O usuário escolhe um item para comprar no relógio.
2. Eles toque no botão comprar.
3. O aplicativo inicia a transação de rede e exibe um indicador de carregamento.
4. Algum tempo depois, a transação seja concluída e o aplicativo exibe uma confirmação de compra.
5. O usuário solta seu pulso e desconecta-se com o aplicativo.

Desde o momento em que o usuário toca no botão comprar até que a transação seja concluída, eles têm seu pulso gerado examinando um indicador de carregamento. Para resolver essa situação, a Apple sugere que o desenvolvedor deve apresentar comentários instantâneos para o usuário em vez de mostrar um indicador de carregamento. 

Usando sugerido para o modelo da Apple, dar uma olhada em novamente a mesma interação rápida:

[![](quick-interaction-techniques-images/quick08.png "Diagrama de sugerido para o modelo de maçãs")](quick-interaction-techniques-images/quick08.png#lightbox)

1. O usuário escolhe um item para comprar no relógio.
2. Eles toque no botão comprar.
3. O aplicativo inicia a transação de rede e exibe uma mensagem informando que a compra foi iniciada com êxito.
4. O usuário solta seu pulso e desconecta-se com o aplicativo.
5. Quando a transação for concluída com êxito algum tempo depois, o aplicativo exibe uma notificação de Local para informar o usuário de uma compra bem-sucedida.

Desta vez, assim que o usuário toca no botão de comprar, que elas serão mostradas em uma mensagem dizendo que a compra foi iniciada, para que possam descartar sua pulso com confiança e encerrar a interação rápida neste momento. Posteriormente eles serão informados sobre o sucesso ou fracasso da transação em uma notificação de usuário. Dessa forma, o usuário só está interagindo com o aplicativo durante as fases do processo de "ativos".

Para aplicativos que estão fazendo a rede, eles podem usar um plano de fundo `NSURLSession` para lidar com a comunicação de rede com uma tarefa de download. Isso permitirá que o aplicativo a ser ativados no plano de fundo para processar as informações baixadas. Para aplicativos que exigem processamento em segundo plano, use uma asserção de tarefa em segundo plano para lidar com o processamento necessário.

## <a name="quick-interaction-design-tips"></a>Dicas de Design de interação rápida

Uma vez que o tamanho desejado de uma interação rápida é dois segundos ou menos, o desenvolvedor deve se concentrar no design das interações do aplicativo, desde o início do processo de design. Localize áreas onde dessas interações podem ser simplificadas (usando a técnica apresentada acima) e usam os novos recursos do watchOS 3 para tornar o aplicativo rápido e responsivo.

A Apple sugere o seguinte:

- Se concentrar em interações rápida, colocando os recursos mais usados do aplicativo para a frente.
- Use complicações e notificações do usuário para funções e recursos comuns de superfície.
- Crie a Interface do usuário avançada e glanceable com SceneKit e SpriteKit.
- Sempre que possível, simplifique a navegação dentro do aplicativo.
- Nunca faça o usuário esperar, permitir que eles descartar sua pulso e solte com o aplicativo assim que possível.

## <a name="summary"></a>Resumo

Este artigo abordou as técnicas de interação rápida Apple foi adicionado no watchOS 3 e como implementá-los no xamarin. IOS para Apple Watch.

## <a name="related-links"></a>Links relacionados

- [Exemplos de watchOS](https://developer.xamarin.com/samples/watchos/all/)
