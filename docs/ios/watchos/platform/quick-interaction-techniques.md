---
title: "Técnicas de interação rápida para watchOS 3"
description: "Este artigo aborda as técnicas de interação rápida Apple adicionou watchOS 3 e como implementá-los no xamarin para Apple Watch."
ms.topic: article
ms.prod: xamarin
ms.assetid: 26697F68-AF7E-4A36-988F-85E2674A4DD1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 75a8e807a68a3fccfa76fc7ba1f260818b25174d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="quick-interaction-techniques-for-watchos-3"></a>Técnicas de interação rápida para watchOS 3

_Este artigo aborda as técnicas de interação rápida Apple adicionou watchOS 3 e como implementá-los no xamarin para Apple Watch._

Fornecendo interação do usuário rápido é essenciais para a criação de aplicativos Apple Watch atraentes e complicações. Novo para watchOS 3, Apple adicionou suporte para reconhecedores de gestos, acesso às novas técnicas de notificação do usuário e a navegação e coroa Digital. Isso, juntamente com o suporte adicionado para SceneKit e SpriteKit, permitem que o desenvolvedor crie facilmente interfaces avançadas e rápidas, rápida e responsivo.

## <a name="what-are-quick-interactions"></a>Quais são as interações rápido

Para um desenvolvedor que é usado para criar aplicativos para iOS ou macOS (em que a quantidade de tempo que um usuário gasta interagir com o aplicativo é medida em minutos ou horas), criando um aplicativo com êxito para o Apple Watch pode ser um desafio e requer um diferente abordagem.

WatchOS, o usuário normalmente deseja gerar seu antiestática, rapidamente interagir com um aplicativo (normalmente por um breve alguns segundos), e em seguida, descartar seu pulso e continuar que era que estava fazendo.

Seguem alguns exemplos típicos interações rápidos sobre o Apple Watch:

- Iniciar um timer.
- Verificando o clima.
- Marcar um item fora de uma lista de tarefas.

Para atingir essas metas, um aplicativo sobre o Apple Watch deve ser:

- **Rápidas** -que significa que, com uma rápida visão do usuário deve ser capaz de obter as informações que eles precisam. 
- **Acionáveis** -que significa que os usuários deve ser capaz de tomar decisões rápidas e bem informadas.
- **Capacidade de resposta** -que significa que o usuário nunca deve esperar para receber as informações que precisam ou para obter a ação que desejarem.

### <a name="quick-interactions-length"></a>Comprimento de interações rápida

Devido à natureza rápida de aplicativos Apple Watch, Apple sugere que o tamanho ideal de uma interação rápido deve ser dois segundos ou menos. Como resultado desse limite segundo dois, o desenvolvedor precisa gastam um tempo considerável tanto projetar e implementar um aplicativo Apple Watch. 

## <a name="new-watchos-3-features-and-apis"></a>Novo watchOS 3 recursos e APIs

Apple tenha adicionado vários novos recursos e APIs para WatchKit para ajudar o desenvolvedor adicionando interações rápidas para seus aplicativos Apple Watch:

- watchOS 3 fornece acesso a novos tipos de entrada, como usuário:
    - Reconhecedores de gestos
    - Rotação de coroa digital 
- watchOS 3 fornece novas maneiras de exibir e atualizar informações, como:
    - Navegação aprimorada de tabela
    - Novo suporte de estrutura de notificação do usuário
    - Integração SpriteKit e SceneKit

Ao implementar esses novos recursos, o desenvolvedor pode garantir que seu aplicativo watchOS 3 é Glanceable, prático e Responsive.

### <a name="gesture-recognizer-support"></a>Suporte do reconhecedor de gesto

Se o desenvolvedor tenha implementado reconhecedores de gestos em iOS, ele devem ser bem conhecidos como reconhecedores de gestos funcionam em watchOS 3. Para atualizar, reconhecedores de gestos são objetos que analisa os eventos de nível baixo de toque em gestos reconhecíveis, predefinidos.

watchOS 3 dará suporte a quatro reconhecedores de gestos seguintes:

- Tipos de gestos distintas:
    - O gesto de passar (`WKSwipeGestureRecognizer`).
    - O gesto de tocar (`WKTapGestureRecognizer`).
- Tipos de gesto contínua:
    - O gesto de Panorâmica (`WKPanGestureRecognizer`).
    - O gesto pressione longa (`WKLongPressGestureRecognizer`).

Para implementar um dos novos identificadores de gesto, simplesmente arraste-o em uma superfície de design no iOS Designer no Visual Studio para Mac e configure suas propriedades.

No código, responda à ação do reconhecedor para lidar com o gesto sendo disparado pelo usuário. Novamente, isso é feito da mesma forma como ela deve ser tratada em iOS.

#### <a name="discrete-gesture-states"></a>Estados de gesto discretos

Para gestos discretos, a ação é chamada quando o gesto é reconhecido e um estado (`WKGestureRecognizerState`) é atribuído como:

[ ![](quick-interaction-techniques-images/quick01.png "Estados de gesto discretos")](quick-interaction-techniques-images/quick01.png)

Todos os gestos discretos Iniciar do `Possible` estado e façam a transição para um o `Failed` ou `Recognized` estado. Ao usar gestos discretos, o desenvolvedor geralmente não lida diretamente com o estado. Em vez disso, eles usam a ação que está sendo chamada quando o gesto é reconhecido somente.

#### <a name="continuous-gesture-states"></a>Estados de gesto contínua

Gestos contínuos são um pouco diferentes dos gestos discretos, onde a ação é chamada várias vezes, conforme o gesto está sendo reconhecido:

[ ![](quick-interaction-techniques-images/quick02.png "Estados de gesto contínua")](quick-interaction-techniques-images/quick02.png)

Novamente, gestos contínua inicia o `Possible` estado, mas eles andamento sobre várias atualizações. Aqui o desenvolvedor precisa considerar o estado do reconhecedor e atualizar a interface do usuário do aplicativo durante o `Changed` fase até que o gesto é finalmente `Recognized` ou `Canceled`.

#### <a name="gesture-recognizer-usage-tips"></a>Dicas de uso do reconhecedor de gesto

Apple sugerimos o seguinte ao trabalhar com reconhecedores de gestos em watchOS 3:

- Adicione os reconhecedores de gestos para agrupar elementos em vez de controles individuais. Como o Apple Watch tem um tamanho menor da tela física, elementos de grupo tendem a aumentar o tamanho e os destinos mais fácil para o usuário pressionar. Além disso, os identificadores de gesto podem conflitar com gestos já nos controles da interface do usuário nativa incorporada.
- Definir relações de dependência no Storyboard de inspeção do aplicativo.
- Alguns gesto têm precedência sobre outros tipos de gesto, tais como:
    - Rolagem
    - Forçar toque
 
### <a name="digital-crown-rotation"></a>Rotação de coroa digital

Implementando suporte coroa Digital em seus aplicativos watchOS 3, um desenvolvedor pode fornecer navegação maior velocidade e precisão interações para seus usuários.

Desde watchOS 2, Apple Watch aplicativo pode usar o `WKInterfacePicker` objeto para acessar o coroa Digital, fornecendo uma lista de `WKPickerItems` e um estilo de seletor (lista, empilhada ou sequência de imagem). watchOS permitido, em seguida, o usuário a usar o coroa Digital para selecionar um item da lista.

Ao usar um `WKInterfacePicker`, WatchKit está manipulando a maioria do trabalho por:

- A lista e os elementos de interface individual do desenho.
- Processar os eventos coroa Digital.
- Chamar uma ação quando um item é selecionado.

Novo para watchOS 3, o desenvolvedor agora tem acesso direto aos eventos de rotação coroa Digital que permite que eles criem seus próprios elementos de interface do usuário que respondem a valores de rotação.

Acesso de coroa digital é fornecido pelos seguintes elementos:

- `WKCrownSequencer` -Fornece acesso a rotações por segundo.
- `WKCrownDelegate` -Fornece acesso a eventos de rotação delta.

#### <a name="rotations-per-second"></a>Rotações por segundo

Acessando o rotações por segundo da Crown Digital é útil ao trabalhar com física com base em animações. Para acessar o rotações por segundo, use o `CrownSequencer` propriedade o `WKInterfaceController` da extensão de inspeção. Por exemplo:

```csharp
var rotationsPerSecond = CrownSequencer.RotationsPerSecond;
```

#### <a name="rotational-deltas"></a>Deltas de rotação

Use os Deltas de rotação da Crown Digital para contar o número de rotações. Use o `CrownDidRotate` substituir o método do `WKCrownDelegate` para acessar os Deltas de rotação. Por exemplo:

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

Aqui, o aplicativo mantém um acumulador (`AccumulatedRotations`) para determinar o número de rotações. Uma rotação total da Crown Digital é igual a um delta acumulado de `1.0` e uma rotação metade seria `0.5`.

Apple deixou responsabilidade do desenvolvedor para determinar como as contagens de rotação correspondem à sensibilidade das alterações no elemento de interface do usuário que está sendo atualizado.

O sinal (`+/-`) de rotação Delta indica a direção que o usuário está ligado a Crown Digital:

[ ![](quick-interaction-techniques-images/quick03.png "O sinal de Delta rotação indica a direção que o usuário está ligado a Crown Digital")](quick-interaction-techniques-images/quick03.png)


Se o usuário está rolando para cima, WatchKit retornará se rolando para baixo, em seguida, deltas negativos serão retornados, independentemente de qual orientação o usuário está usando o relógio no e deltas positivos.

#### <a name="digital-crown-focus"></a>Foco coroa digital

Assim como quaisquer outros elementos de interface, o coroa Digital tem o conceito de foco. Esse foco pode ser deslocado a Crown Digital para outros elementos de interface com base em como o usuário está interagindo com o relógio. 

Por exemplo, qualquer um dos seguintes controles pode roubar o foco de coroa Digital:

- Seletor
- Controle deslizante
- Controlador de rolagem

É responsabilidade do desenvolvedor para determinar quando o seu elemento de interface personalizada deve ser o foco de coroa Digital. Apple sugere usando o novo reconhecedores de gestos para obter o foco no elemento de interface do usuário personalizado.

### <a name="vertical-paging"></a>Paginação vertical

É o modo padrão de um usuário navega um modo de exibição de tabela em um aplicativo de watchOS rolar para a parte desejada de dados, toque em uma linha específica para exibir a exibição detalhada, toque no botão Voltar quando terminar de exibir os detalhes e repita o processo para qualquer outra informação que o y está interessado em de dentro da tabela:

[ ![](quick-interaction-techniques-images/quick04.png "Movendo entre uma tabela e a exibição de detalhes")](quick-interaction-techniques-images/quick04.png)

Novo para watchOS 3, o desenvolvedor pode habilitar a paginação Vertical em seus controles de exibição de tabela. Com esse recurso habilitado, o usuário pode rolar para localizar uma linha do modo de exibição de tabela e toque em linha para exibir seus detalhes como antes. No entanto, eles podem agora passa o dedo para cima para selecionar a próxima linha na tabela ou para baixo para selecionar a linha anterior (ou use o coroa Digital), sem precisar retornar ao modo de exibição de tabela pela primeira vez:

[ ![](quick-interaction-techniques-images/quick05.png "Movendo entre uma tabela e a exibição de detalhes e passar o dedo para cima e para baixo para mover entre as outras linhas")](quick-interaction-techniques-images/quick05.png)

Para habilitar esse modo, abra o Storyboard watchOS do aplicativo no Xcode para edição, selecione o modo de exibição de tabela e verifique o **paginação Vertical de detalhes** caixa de seleção:

[ ![](quick-interaction-techniques-images/quick06.png "Marque a caixa de seleção de paginação Vertical de detalhes")](quick-interaction-techniques-images/quick06.png)

Certifique-se de que a tabela está usando Segues para exibir a exibição detalhada e salvar as alterações para o Storyboard e retornar ao Visual Studio para Mac sincronizar.

O desenvolvedor programaticamente pode envolver a paginação Vertical para uma linha específica usando o seguinte código em uma exibição de tabela:

```csharp
// Segue into Vertical Paging and select the first row
MenuTable.PerformSegue (0);
```

Ao usar a paginação Vertical, o desenvolvedor precisa estar ciente que WatchKit automaticamente tratará o pré-carregamento de controladores e como resultado, alguns métodos de ciclo de vida do controlador podem ser chamados antes que a interface do usuário seja realmente visível.

### <a name="notification-enhancements"></a>Aprimoramentos de notificação

Notificação são a forma principal de interação rápida geralmente apresenta um usuário em watchOS e estão disponível desde a primeira da Apple Watch e watchOS 1.

Um típico interação rápida de notificação é o seguinte:

1. O usuário sentir a notificação Haptic quando uma nova notificação é recebida.
2. Eles geram seu pulso para ver a interface pesquisar curto para a notificação.
3. Se eles continuam a manter seu pulso gerado, watchOS automaticamente faz a transição para a interface de notificação longo pesquisar.

Há várias maneiras que um usuário possa responder à notificação:

- Para um bem definidos e apresentados notificação, o usuário não fazer nada e simplesmente ignorar a notificação.
- Eles também podem tocar da notificação para iniciar o aplicativo watchOS.
- Para uma notificação que oferece suporte a ações personalizadas, o usuário pode selecionar uma das ações personalizadas. Eles podem ser:
    - **Ações de primeiro plano** -esses iniciam o aplicativo para executar a ação.
    - **Ações do plano de fundo** - sempre foi roteada para o iPhone em watchOS 2, mas pode ser roteado para watchApp em watchOS 3.

Novo para watchOS 3:

* Notificação de usar uma API semelhante em todas as plataformas (iOS, watchOS, tvOS e macOS).
* Notificação de local pode ser agendada no Apple Watch.
* Notificação de plano de fundo será roteada para a extensão do aplicativo se eles foram agendados no Apple Watch.

#### <a name="notification-scheduling-and-delivery"></a>Notificação de agendamento e entrega

Notificação do iPhone do usuário será encaminhar para o Apple Watch, quando ocorre o seguinte:

* Tela do iPhone está desativado.
* O Apple Watch está sendo gasto e desbloqueada.

Em watchOS 3, notificações Local podem ser agendadas no Apple Watch e só são entregues no relógio. É o desenvolvedor para agendar um iPhone correspondente notificação se for exigida pelo aplicativo.

Incluindo o mesmo identificador de notificação no Apple Watch e iPhone versões das notificações, ele evita notificações duplicadas sejam exibidas no relógio. A versão do Apple Watch da notificação terá precedência sobre a versão do iPhone.

Como watchOS 3 usa os mesmos `UINotification` estrutura API como iOS 10, consulte nosso iOS 10 [Framework de notificação do usuário](~/ios/platform/user-notifications/index.md) documentação para obter mais detalhes.

### <a name="using-spritekit-and-scenekit"></a>Usando SpriteKit e SceneKit

Novo para watchOS 3, o desenvolvedor pode agora usar objetos SpritKit e SceneKit no design de Interface do usuário do seu aplicativo apresentar gráficos 2D e 3D.

Foram adicionadas duas novas classes de interface para oferecer suporte a esse recurso:

- `WKInterfaceSKScene` -Para trabalhar com gráficos 2D SpriteKit.
- `WKInterfaceSCNScene` -Para trabalhar com gráficos 3D SceneKit.

Para usar esses objetos, simplesmente arraste-os para a superfície de design dentro de Storyboard do aplicativo de inspeção no construtor de Interface do Xcode e use o **atributos Inspetor** para configurá-los.

Desse ponto, trabalhar com o SpriteKit ou SceneKit cenas funciona da mesma forma dentro de um aplicativo iOS. O aplicativo watch apresentará um `WKInterfaceSKScene` chamando um do `Present` métodos. Para SceneKit, basta definir o `Scene` propriedade o `WKInterfaceSCNScene` objeto.

## <a name="actionable-complications"></a>Complicações acionáveis

Em watchOS 2, a Apple introduziu complicações para 3º aplicativos de terceiros. Em watchOS 3, Apple expandiu os recursos que um desenvolvedor pode incluir em um Complicativo WatchKit. 

Além disso, mais interna na inspeção faces agora podem incluir complicações e inspecionar existente faces que já complicações com suporte podem agora incluídos complicações ainda mais.

Também novo é a capacidade de um usuário rapidamente passe o dedo para esquerda ou direito para a transição por todas as faces inspecionar instalado no seu Apple Watch. Usando a Galeria de novo no aplicativo de iPhone complementar do o Apple Watch, o usuário pode adicionar e personalizar as faces Inspecionar novo e qualquer as complicações que podem incluir.

Devido a esses novos recursos, a Apple sugere que todos os aplicativos do Apple Watch também devem incluir pelo menos um Complicativo e como tal, todo o aplicativo nativo do Apple Watch agora tem complicações.

Complicações fornecem os seguintes recursos para um aplicativo:

- Eles são altamente rápidos, pois eles estão sempre presentes na face do relógio.
- Complicações com frequência são atualizadas pelo watchOS. Qualquer aplicativo que inclui um Complicativo na face de inspecionar exibido no momento do usuário é atualizado pelo menos duas vezes em uma hora.
- Qualquer aplicativo com um Complicativo na face de inspecionar exibido no momento do usuário é mantido na memória que faz com que o aplicativo Iniciar rapidamente e melhora a velocidade de respostas do aplicativo.
- Complicações tornam mais fácil para o usuário iniciar a funcionalidade específica em um aplicativo watchOS.

## <a name="glanceable-notification"></a>Notificação rápida

Notificação no Apple Watch fornecem uma maneira excelente e personalizável para rapidamente informar o usuário de eventos ou novas informações como mensagens de entrada ou atingir uma meta em um aplicativo de treinamento.

Por meio de uma notificação, informações importantes podem ser rapidamente apresentadas ao usuário. Em muitas situações, uma notificação bem projetada pode remover a necessidade para o usuário, na verdade, inicie o aplicativo.

Novo para watchOS 3, todas as notificações agora oferecem suporte ao:

- SpriteKit
- SceneKit
- Vídeo embutido

## <a name="enhanced-ui-with-spritekit-and-scenekit"></a>Interface do usuário avançada com SpriteKit e SceneKit

Normalmente, um desenvolvedor pode pensar em jogo da interface do usuário quando SpriteKit e SceneKit são mencionados. No entanto, SpriteKit e SceneKit podem ser útil para a criação de jogos não interfaces do usuário que incluem layouts personalizados, conteúdos e animações que não são possíveis no WatchKit sozinho.

Por exemplo, uma notificação de usuário de uma aplicativo de compartilhamento de fotos pode usar SpriteKit para fornecer uma experiência de usuário, incluindo o usuário que lançou a imagem junto com uma imagem real e outras informações personalizadas que enriquece a experiência do usuário.

Além disso, SpriteKit e SceneKit podem ser combinados com elementos de UI WatchKit padrão no design de Interface do usuário do aplicativo.

## <a name="simple-navigation"></a>Navegação simples

watchOS 3 apresenta várias maneiras que um desenvolvedor pode simplificar a navegação em aplicativos como o novo watchOS [paginação Vertical](#Vertical-Paging), [suporte do reconhecedor de gesto](#Gesture-Recognizer-Support) e [coroa Digital Rotação](#Digital-Crown-Rotation) recursos apresentados a seguir.

O coroa Digital é exclusiva para a Apple Watch e pode ser usada de várias maneiras diferentes para simplificar a navegação. Por exemplo, um aplicativo de temporizador pode usar o coroa Digital para limpar por meio de comprimentos de temporizador disponíveis.

Gestos personalizados podem apresentar formas novas e exclusivas para o usuário interaja com um aplicativo de inspeção e também podem ser usados para simplificar a navegação de aplicativo.

Apple sugerir procurando formas de combinar todos os novos recursos de interação rápida adicionados no watchOS 3 para apresentar avançados, fácil e rápido usar interfaces de aplicativo watchOS.

## <a name="finishing-the-quick-interaction"></a>Concluindo a interação rápida

Uma experiência de interação rápida bem projetado dar ao usuário a confiança para descartar sua antiestática (e soltar com o aplicativo) quando ele tiver terminado a interação atual.

Onde isso especificamente se torna um problema é quando o aplicativo watch é fazer qualquer tipo de conexão de rede ou compartilhar informações com seu aplicativo de iPhone complementar. Geralmente, isso pode levar a um indicador de espera enquanto a transação está em andamento, que não é desejável durante uma interação rápida. Veja o exemplo a seguir:

[ ![](quick-interaction-techniques-images/quick07.png "Diagrama do aplicativo inspecionar fazendo uma conexão de rede e compartilhamento de informações com seu aplicativo de iPhone complementar")](quick-interaction-techniques-images/quick07.png)

1. O usuário escolhe um item para comprar no relógio.
2. Eles tocar no botão comprar.
3. O aplicativo inicia a transação de rede e exibe um indicador de carregamento.
4. Algum tempo depois, a transação seja concluída e o aplicativo exibe uma confirmação de compra.
5. O usuário cancela seu pulso e desconecta-se com o aplicativo.

Desde o momento em que o usuário toca no botão comprar até que a transação seja concluída, eles têm seu pulso gerado observando um indicador de carregamento. Para resolver essa situação, a Apple sugere que o desenvolvedor deve apresentar um feedback para o usuário em vez de mostrar um indicador de carregamento. 

Usando o modelo sugerido da Apple, examine novamente a mesma interação rápida:

[ ![](quick-interaction-techniques-images/quick08.png "Diagrama de sugerido para o modelo de maçãs")](quick-interaction-techniques-images/quick08.png)

1. O usuário escolhe um item para comprar no relógio.
2. Eles tocar no botão comprar.
3. O aplicativo inicia a transação de rede e exibirá uma mensagem informando que a compra foi iniciada com êxito.
4. O usuário cancela seu pulso e desconecta-se com o aplicativo.
5. Quando a transação seja concluída com êxito a algum momento mais tarde, o aplicativo exibe uma notificação de Local para informar o usuário de uma compra bem-sucedida.

Desta vez, assim que o usuário toca no botão de comprar, que eles verão uma mensagem informando que a compra foi iniciada, para que possam remover seu antiestática com confiança e terminar a interação rápida neste momento. Posteriormente, eles serão informados sobre o êxito ou falha da transação em uma notificação do usuário. Dessa forma, somente o usuário interage com o aplicativo durante as fases do processo de "ativas".

Para aplicativos que estão fazendo a rede, eles podem usar um plano de fundo `NSURLSession` para lidar com a comunicação de rede com uma tarefa de download. Isso permitirá que o aplicativo ser ativados no plano de fundo para processar as informações de download. Para aplicativos que exigem processamento em segundo plano, use uma asserção de tarefa em segundo plano para lidar com o processamento necessário.

## <a name="quick-interaction-design-tips"></a>Dicas de Design de interação rápida

Como o comprimento desejado de uma interação rápida é dois segundos ou menos, o desenvolvedor deve se concentrar no design de interações do aplicativo desde o início do processo de design. Localize áreas em que as interações podem ser simplificadas (usando a técnica apresentada acima) e usam os novos recursos de watchOS 3 para tornar o aplicativo rápido e capacidade de resposta.

Apple sugere o seguinte:

- Se concentrar em interações rápida, colocando os recursos mais usados do aplicativo para a frente.
- Use complicações e notificações do usuário para funções e recursos comuns de superfície.
- Crie Interface do usuário avançada, rápidas com SceneKit e SpriteKit.
- Sempre que possível, simplifique a navegação dentro do aplicativo.
- Nunca faça o usuário esperar, permitir que eles descartar sua antiestática e soltar com o aplicativo assim que possível.

## <a name="summary"></a>Resumo

Este artigo abordou as técnicas de interação rápida Apple adicionou watchOS 3 e como implementá-los no xamarin para Apple Watch.

## <a name="related-links"></a>Links relacionados

- [Exemplos de watchOS](https://developer.xamarin.com/samples/watchos/all/)
