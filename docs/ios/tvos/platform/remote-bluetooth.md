---
title: Controladores remotos e Bluetooth Siri para tvOS no Xamarin
description: Este artigo descreve como trabalhar com os controladores de jogos Siri remotos e Bluetooth em aplicativos tvOS escritos com o Xamarin.
ms.prod: xamarin
ms.assetid: BDB9894A-236B-424B-9032-ACD12A6C5720
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: c0338fce694d61dc19484c56dbc00bb854d0d0d7
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2020
ms.locfileid: "78292387"
---
# <a name="siri-remote-and-bluetooth-controllers-for-tvos-in-xamarin"></a>Controladores remotos e Bluetooth Siri para tvOS no Xamarin

Os usuários do seu aplicativo Xamarin. tvOS não serão interagindo com sua interface diretamente como com iOS, em que eles tocam imagens na tela do dispositivo, mas indiretamente de toda a sala usando o [Siri remoto](#The-Siri-Remote).

Se seu aplicativo for um jogo, você poderá, opcionalmente, criar suporte para [controladores de jogos Bluetooth](#Bluetooth-Game-Controllers) do IOS (acessórios MFI) em seu aplicativo também.

[![](remote-bluetooth-images/intro01.png "The Bluetooth Remote and Game Controller")](remote-bluetooth-images/intro01.png#lightbox)

Este artigo descreve os botões [remotos Siri](#The-Siri-Remote), [gestos de superfície de toque](#Touch-Surface-Gestures) e [Siri remoto](#Siri-Remote-Buttons) e mostra como trabalhar com eles por meio de [gestos e storyboards](#Gestures-and-Storyboards), [gestos e código](#Gestures-and-Code) e [manipulação de eventos de baixo nível](#Low-Level-Event-Handling). Por fim, ele aborda o [trabalho com controladores de jogo](#Working-with-Game-Controllers) em um aplicativo Xamarin. tvOS.

<a name="The-Siri-Remote" />

## <a name="the-siri-remote"></a>O Siri remoto

A principal maneira que os usuários vão interagir com a Apple TV e seu aplicativo Xamarin. tvOS, é por meio do Siri remoto incluído. A Apple criou o remoto para ligar a distância entre o usuário e a interface do usuário da Apple TV exibida na tela da TV.

Seu desafio como desenvolvedor de aplicativos tvOS é criar uma interface do usuário rápida, fácil de usar e visualmente atraente que aproveita a superfície de toque do Siri remoto, o acelerômetro, o giroscópio e os botões.

[![](remote-bluetooth-images/remote01.png "The Siri Remote")](remote-bluetooth-images/remote01.png#lightbox)

O Siri Remote tem os seguintes recursos e usos esperados em seu aplicativo tvOS:

|Recurso|Uso geral do aplicativo|Uso do aplicativo de jogo|
|---|---|---|
|**Superfície de toque**<br />Passe o dedo para navegar, pressione para selecionar e manter os menus contextuais.|**Tocar/passar o dedo**<br />Navegação de interface do usuário entre itens com foco.<br /><br />**Clique**<br />Ativa o item selecionado (em foco).|**Tocar/passar o dedo**<br />Depende do design do jogo e pode ser usado como um teclado D tocando nas bordas.<br /><br />**Clique**<br />Execute a função de botão principal.|
|**Menu**<br />Pressione para retornar à tela ou ao menu anterior.|Retorna à tela anterior e sai para a tela inicial da Apple TV na tela principal do aplicativo.|Pausar e retomar o jogo, retorna à tela anterior e sai para a tela inicial da Apple TV na tela principal do aplicativo.|
|**Siri/pesquisa**<br />Em países com siri, pressione e segure para controle de voz, em todos os outros países, exibe a tela de pesquisa.|n/d|n/d|
|**Reproduzir/pausar**<br />Reproduzir e pausar mídia ou fornecer uma função secundária em aplicativos.|Inicia a reprodução de mídia e pausa/retoma a reprodução.|Executa a função de botão secundário ou ignora o vídeo introdutório (se existir).|
|**Página Inicial**<br />Pressione para retornar à tela inicial, clique duas vezes para exibir aplicativos em execução, pressione e mantenha pressionado o dispositivo de suspensão.|n/d|n/d|
|**Volume**<br />Controla o volume de equipamento de áudio/vídeo anexado.|n/d|n/d|

<a name="Touch-Surface-Gestures" />

## <a name="touch-surface-gestures"></a>Gestos de superfície de toque

A superfície de toque do Siri remoto é capaz de detectar uma variedade de gestos de dedo único que você pode responder em seu aplicativo Xamarin. tvOS:

|Passar o dedo|Clique em|Toque|
|---|---|---|
|![](remote-bluetooth-images/Gesture01.png)|![](remote-bluetooth-images/Gesture02.png)|![](remote-bluetooth-images/Gesture03.png)|
|Move a seleção (foco) entre os elementos da interface do usuário na tela (para cima, para baixo à esquerda, à direita). O passe do dedo pode ser usado para rolar por listas grandes de conteúdo rapidamente usando o inércia.|Ativa o item selecionado (em foco) ou atua como o botão principal em um jogo. Clicar e segurar pode ativar menus contextuais ou funções secundárias.|Tocar levemente na superfície de toque nas bordas atua como botões direcionais em um teclado D, movendo o foco para cima, para baixo, para a esquerda ou para a direita, dependendo da área tocada. Dependendo do aplicativo, o pode ser usado para revelar controles ocultos.|

A Apple fornece as seguintes sugestões para trabalhar com gestos de superfície de toque:

- **Diferenciar entre cliques e toques** -clicar é uma ação intencional pelo usuário e é bem adequado para seleção, ativação e o botão principal de um jogo. Tocar é mais sutil e deve ser usado com moderação porque o usuário geralmente está mantendo o Siri remoto em sua mão e pode acidentalmente ativar um evento Tap facilmente.
- **Não redefina gestos padrão** -o usuário tem uma expectativa de que gestos específicos executam ações específicas, você não deve redefinir o significado ou a função desses gestos em seu aplicativo. A única exceção é um aplicativo de jogos durante o jogo ativo.
- **Definir novos gestos de forma moderada** de novo, o usuário tem uma expectativa de que gestos específicos executam ações específicas. Você deve evitar a definição de gestos personalizados para executar ações padrão. E novamente, os jogos são a exceção mais comum em que os gestos personalizados podem adicionar diversão, reprodução de imersão ao jogo.
- **Se apropriado, responder a toques de quadro d** – levemente tocando nas bordas de canto da superfície de toque reagirá como um painel D em um controlador de jogo movendo o foco ou a direção para cima, para baixo, para a esquerda ou para a direita. Se apropriado, você deve responder a esses gestos em seu aplicativo ou jogo.

<a name="Siri-Remote-Buttons" />

## <a name="siri-remote-buttons"></a>Botões remotos do Siri

Além dos gestos na superfície de toque, seu aplicativo pode responder ao usuário clicando na superfície de toque ou pressionando o botão reproduzir/pausar. Se você estiver acessando o Siri Remote usando a estrutura do controlador de jogo, também poderá detectar o botão de menu que está sendo pressionado.

Além disso, os pressionamentos de botão de menu podem ser detectados usando um reconhecedor de gesto com elementos de `UIKit` padrão. Se você interceptar o botão de menu que está sendo pressionado, será responsável por fechar a exibição atual e o controlador de exibição e retornar para o anterior.

> [!IMPORTANT]
> Você **sempre** deve atribuir uma função ao botão reproduzir/pausar no controle remoto. Ter um botão não funcional pode fazer com que seu aplicativo pareça quebrado para o usuário final. Se você não tiver uma função válida para esse botão, atribua a mesma função que o botão primário (clique de superfície de toque).

<a name="Gestures-and-Storyboards" />

## <a name="gestures-and-storyboards"></a>Gestos e storyboards

A maneira mais fácil de trabalhar com o remoto Siri em seu aplicativo Xamarin. tvOS é adicionar reconhecedores de gestos às suas exibições no designer de interface.

Para adicionar um reconhecedor de gestos, faça o seguinte:

1. Na **Gerenciador de soluções**, clique duas vezes no arquivo `Main.storyboard` e abra-o para editar o designer de interface.
2. Arraste um **reconhecedor de gestos de toque** da **biblioteca** e solte-o na exibição:

    [![](remote-bluetooth-images/storyboard01.png "A Tap Gesture Recognizer")](remote-bluetooth-images/storyboard01.png#lightbox)
3. Marque **selecionar** na seção de **botão** do **Inspetor de atributo**:

    [![](remote-bluetooth-images/storyboard02.png "Check Select")](remote-bluetooth-images/storyboard02.png#lightbox)
4. **Select** significa que o gesto responderá ao usuário clicando na **superfície de toque** no Siri remoto. Você também tem a opção de responder aos botões **menu**, **reproduzir/pausar**, para **cima**, **para baixo, para**a **esquerda** e para a **direita** .
5. Em seguida, conecte uma **ação** do **reconhecedor de gestos de toque** e chame-a `TouchSurfaceClicked`:

    [![](remote-bluetooth-images/storyboard03.png "An Action from the Tap Gesture Recognizer")](remote-bluetooth-images/storyboard03.png#lightbox)
6. Salve as alterações e retorne ao Visual Studio para Mac.

Edite o arquivo do controlador de exibição (exemplo `FirstViewController.cs`) e adicione o seguinte código para lidar com o gesto que está sendo disparado:

```csharp
using System;
using UIKit;

namespace tvRemote
{
    public partial class FirstViewController : UIViewController
    {
        ...

        #region Custom Actions
        partial void TouchSurfaceClicked (Foundation.NSObject sender) {
            // Handle click here
            ...
        }
        #endregion
    }
}
```

Para obter mais informações sobre como trabalhar com storyboards, consulte nossa [Guia de início rápido Olá, tvOS](~/ios/tvos/get-started/hello-tvos.md). Especificamente, a seção [criando a interface do usuário](~/ios/tvos/get-started/hello-tvos.md#Creating-the-User-Interface) e [escrevendo o código com as seções de ações e saídas](~/ios/tvos/get-started/hello-tvos.md#Writing-the-Code) .

<a name="Gestures-and-Code" />

## <a name="gestures-and-code"></a>Gestos e código

Opcionalmente, você pode criar gestos diretamente no C# código e adicioná-los às exibições na sua interface do usuário. Por exemplo, para adicionar uma série de reconhecedores de gestos de passar, edite seu controlador de exibição e adicione o seguinte código:

```csharp
using System;
using UIKit;

namespace tvRemote
{
    public partial class SecondViewController : UIViewController
    {
        #region Constructors
        public SecondViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Wire-up gestures
            var upGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Up";
                ButtonLabel.Text = "Swiped Up";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Up
            };
            this.View.AddGestureRecognizer (upGesture);

            var downGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Down";
                ButtonLabel.Text = "Swiped Down";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Down
            };
            this.View.AddGestureRecognizer (downGesture);

            var leftGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Left";
                ButtonLabel.Text = "Swiped Left";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Left
            };
            this.View.AddGestureRecognizer (leftGesture);

            var rightGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Right";
                ButtonLabel.Text = "Swiped Right";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Right
            };
            this.View.AddGestureRecognizer (rightGesture);
        }
        #endregion
    }
}
```

<a name="Low-Level-Event-Handling" />

## <a name="low-level-event-handling"></a>Manipulação de eventos de baixo nível

Se você estiver criando um tipo personalizado com base em `UIKit` em seu aplicativo Xamarin. tvOS (por exemplo `UIView`), também terá a capacidade de fornecer manipulação de baixo nível de pressionamento de botão por meio de eventos de `UIPress`.

Um evento `UIPress` é tvOS o que um evento de `UITouch` é para iOS, exceto `UIPress` retorna informações sobre pressionamentos de botão no Siri remoto ou em outros dispositivos Bluetooth conectados (como um controlador de jogo). `UIPress` eventos descrevem o botão que está sendo pressionado e seu estado (iniciado, cancelado, alterado ou encerrado).

Para botões analógicos em dispositivos como controladores de jogos Bluetooth, `UIPress` também retorna a quantidade de força que está sendo aplicada ao botão. A propriedade `Type` do evento `UIPress` define qual botão físico mudou de estado, enquanto o restante das propriedades descreve a alteração que ocorreu.

O código a seguir mostra um exemplo de tratamento de eventos de `UIPress` de baixo nível para um `UIView`:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvRemote
{
    public partial class EventView : UIView
    {
        #region Computed Properties
        public override bool CanBecomeFocused {
            get {
                return true;
            }
        }
        #endregion

        #region
        public EventView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void PressesBegan (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesBegan (presses, evt);

            foreach (UIPress press in presses) {
                // Was the Touch Surface clicked?
                if (press.Type == UIPressType.Select) {
                    BackgroundColor = UIColor.Red;
                }
            }
        }

        public override void PressesCancelled (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesCancelled (presses, evt);

            foreach (UIPress press in presses) {
                // Was the Touch Surface clicked?
                if (press.Type == UIPressType.Select) {
                    BackgroundColor = UIColor.Clear;
                }
            }
        }

        public override void PressesChanged (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesChanged (presses, evt);
        }

        public override void PressesEnded (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesEnded (presses, evt);

            foreach (UIPress press in presses) {
                // Was the Touch Surface clicked?
                if (press.Type == UIPressType.Select) {
                    BackgroundColor = UIColor.Clear;
                }
            }
        }
        #endregion
    }
}
```

Assim como ocorre com eventos `UITouch`, se você precisar implementar qualquer uma das substituições de evento `UIPress`, deverá implementar todas as quatro.

<a name="Bluetooth-Game-Controllers" />

## <a name="bluetooth-game-controllers"></a>Controladores de jogos Bluetooth

Além do Siri remoto padrão que acompanha o Apple TV, os controladores de jogos Bluetooth de terceiros (acessórios MFI) podem ser emparelhados com a Apple TV e usados para controlar seu aplicativo Xamarin. tvOS.

[![](remote-bluetooth-images/game01.png "Bluetooth Game Controllers")](remote-bluetooth-images/game01.png#lightbox)

Os controladores de jogo podem ser usados para aprimorar jogos e fornecer uma noção de imersão em um jogo. Eles também podem ser usados para controlar a interface padrão da Apple TV para que o uso não precise alternar entre o remoto e o controlador.

> [!IMPORTANT]
> Controladores de jogos Bluetooth são uma compra opcional que os usuários finais podem fazer, seu aplicativo não pode forçar o usuário a comprar um. Se seu aplicativo der suporte a controladores de jogos, ele também deverá oferecer suporte ao Siri remoto para que o jogo seja utilizável por todos os usuários da Apple TV.

Um controlador de jogo tem os seguintes recursos e usos esperados em seu aplicativo tvOS:

|Recurso|Uso geral do aplicativo|Uso do aplicativo de jogo|
|---|---|---|
|**Painel D**|Navega pelos elementos da interface do usuário (altera o foco).|Depende do jogo.|
|**A**|Ativa o item selecionado (em foco).|Executa a função de botão primário e confirma as ações da caixa de diálogo.|
|**B**|Retorna à tela anterior ou sai para a tela inicial se estiver na tela principal do aplicativo.|Executa a função de botão secundário ou retorna à tela anterior.|
|**X**|Inicia a reprodução de mídia ou pausa/retoma a reprodução.|Depende do jogo.|
|**S**|n/d|Depende do jogo.|
|**Menu**|Retorna à tela anterior ou sai para a tela inicial se estiver na tela principal do aplicativo.|Pause/retome o jogo, retorna à tela anterior ou sai para a tela inicial se estiver na tela principal do aplicativo.|
|**Botão à esquerda**|Navega para a esquerda.|Depende do jogo.|
|**Gatilho esquerdo**|Navega para a esquerda.|Depende do jogo.|
|**Botão do ressalto direito**|Navega para a direita.|Depende do jogo.|
|**Gatilho à direita**|Navega para a direita|Depende do jogo.|
|**Thumbstick esquerdo**|Navega pelos elementos da interface do usuário (altera o foco).|Depende do jogo.|
|**Thumbstick à direita**|n/d|Depende do jogo.|

A Apple fornece as seguintes sugestões para trabalhar com controladores de jogo:

- **Confirmar conexões do controlador de jogo** – seu aplicativo tvOS pode ser iniciado e interrompido a qualquer momento pelo usuário final. Você sempre deve verificar a presença de um controlador de jogo em horários de início ou de ativação do aplicativo e tomar medidas conforme necessário.
- **Verifique se seu aplicativo funciona em controladores de jogo e remotos Siri** – não exija que os usuários alternem entre o Siri remoto e um controlador de jogo para usar seu aplicativo. Teste seu aplicativo com frequência com ambos os tipos de controladores, garantindo que tudo seja fácil de navegar e funcione conforme o esperado.
- **Forneça uma maneira** de pressionar o botão de menu para voltar sempre para a tela anterior. Se o usuário estiver na tela principal do aplicativo, o botão de menu deverá retorná-los para a tela inicial da Apple TV. Durante o jogo, o botão de menu deve exibir um alerta dando ao usuário a capacidade de pausar/retomar o jogo ou retornar ao menu principal.

<a name="Working-with-Game-Controllers" />

## <a name="working-with-game-controllers"></a>Trabalhando com controladores de jogo

Conforme mencionado acima, além do Siri remoto padrão que acompanha a Apple TV, o usuário pode opcionalmente anexar um terceiro, feito para controladores de jogos Bluetooth do iOS (acessórios MFI) e usá-lo para controlar seu aplicativo Xamarin. tvOS.

Se seu aplicativo exigir entrada de controlador de baixo nível, você poderá usar a [estrutura de Game Controller](https://developer.apple.com/library/prerelease/tvos/documentation/ServicesDiscovery/Conceptual/GameControllerPG/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013276) da Apple, que tem as seguintes modificações para tvOS:

- O perfil do micro Game Controller (`GCMicroGamepad`) foi adicionado para direcionar o Siri remoto.
- A nova classe `GCEventViewController` pode ser usada para rotear eventos do controlador de jogo por meio de seu aplicativo. Consulte a seção [determinando a entrada do controlador de jogo](#determining-game-controller-input) abaixo para obter mais detalhes.

<a name="Game-Controller-Support-Requirements" />

### <a name="game-controller-support-requirements"></a>Requisitos de suporte do controlador de jogo

A Apple tem vários requisitos específicos que devem ser atendidos se seu aplicativo Xamarin. tvOS oferecer suporte a controladores de jogos:

- **Você deve dar suporte ao Siri Remote** – você deve sempre dar suporte ao Siri remoto. Seu jogo não pode exigir que um controlador de jogo de terceiros seja reproduzido.
- **Você deve dar suporte ao layout de controle estendido** – todos os controladores de jogos tvOS são não Formfitting, controladores estendidos.
- Os **jogos devem ser reproduzidos com controladores** autônomos – se seu aplicativo oferecer suporte a um controlador de jogo estendido, ele deverá ser reproduzido exclusivamente com esse controlador de jogo.
- **Você deve dar suporte ao botão reproduzir/pausar** – durante o jogo, se o usuário pressionar o botão reproduzir/pausar, você deverá exibir um alerta dando ao usuário a capacidade de pausar/retomar o jogo ou retornar ao menu principal.

<a name="Enabling-Game-Controller-Support" />

### <a name="enabling-game-controller-support"></a>Habilitando o suporte ao controlador de jogos

Para habilitar o suporte ao controlador de jogos em seu aplicativo Xamarin. tvOS, clique duas vezes no arquivo `Info.plist` no **Gerenciador de soluções** para abri-lo para edição:

[![](remote-bluetooth-images/game02.png "The Info.plist editor")](remote-bluetooth-images/game02.png#lightbox)

Na seção **controlador de jogo** , marque **habilitar controladores de jogo**e, em seguida, verifique todos os tipos de controlador de jogos que terão suporte do aplicativo.

<a name="Using-the-Siri-Remote-as-a-Game-Controller" />

### <a name="using-the-siri-remote-as-a-game-controller"></a>Usando o Siri Remote como um controlador de jogo

O Siri remoto que acompanha a Apple TV pode ser usado como um controlador de jogo limitado. Como outros controladores de jogo, ele aparece na estrutura do controlador de jogo como um objeto `GCController` e dá suporte aos perfis `GCMotion` e `GCMicroGamepad`.

O Siri Remote tem as seguintes características ao ser usado como um controlador de jogo:

- A superfície de toque pode ser usada como um teclado D que fornece dados de entrada analógicos.
- O controle remoto pode ser usado em uma orientação retrato ou paisagem e seu aplicativo decide se o objeto de perfil deve inverter os dados de entrada automaticamente.
- Clicar na superfície de toque funciona como pressionar o botão **A** em um controlador de jogo.
- O botão reproduzir/pausar atua como o botão **X** em um controlador de jogo.
- O botão de menu deve exibir um alerta fornecendo ao usuário a capacidade de pausar/retomar o jogo ou retornar ao menu principal.

<a name="Summary" />

### <a name="determining-game-controller-input"></a>Determinando a entrada do controlador de jogos

Ao contrário do iOS, em que os eventos do Game Controller podem ser recebidos em paralelo com eventos de toque, o tvOS processa todos os eventos de nível baixo para fornecer eventos de `UIKit` de alto nível. Como resultado, se você precisar de acesso aos eventos do controlador de jogos de nível baixo, será necessário desativar o comportamento padrão do `UIKit`.

No tvOS, quando você deseja processar a entrada do controlador de jogo diretamente, você precisa usar um `GCEventViewController` (ou uma subclasse) para exibir a interface do usuário do jogo. Sempre que um `GCEventViewController` for o *primeiro Respondente*, a entrada do controlador de jogo será capturada e entregue ao seu aplicativo por meio da estrutura do controlador de jogo.

Você pode usar a propriedade `UserInteractionEnabled` da classe `GCEventViewController` para alternar como os eventos são processados e manipulados.

Para obter informações sobre como implementar o suporte ao controlador de jogo, consulte a seção [trabalhando com controladores de jogos](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/WorkingwithGameControllers.html) da Apple no [Guia de programação do aplicativo para o TvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/index.html) e o guia de programação do [Game Controller](https://developer.apple.com/library/prerelease/tvos/documentation/ServicesDiscovery/Conceptual/GameControllerPG/Introduction/Introduction.html).

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou o novo Siri Remote que acompanha o Apple TV, gestos de superfície de toque e botões remotos Siri. Em seguida, ele abordou o trabalho com gestos e storyboards, gestos e código e eventos de baixo nível. Por fim, se discutida trabalhando com controladores de jogo.

## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
