---
title: Siri remoto e controladores de Bluetooth
description: Este artigo aborda o suporte os novos controladores de jogo Siri remoto e Bluetooth em seus aplicativos Xamarin.tvOS.
ms.topic: article
ms.prod: xamarin
ms.assetid: BDB9894A-236B-424B-9032-ACD12A6C5720
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: ca3dd71c3da316e467d8c388efbbded3d9778bf0
ms.sourcegitcommit: 5fc1c4d17cd9c755604092cf7ff038a6358f8646
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/17/2018
---
# <a name="siri-remote-and-bluetooth-controllers"></a>Siri remoto e controladores de Bluetooth

_Este artigo aborda o suporte os novos controladores de jogo Siri remoto e Bluetooth em seus aplicativos Xamarin.tvOS._


Os usuários do seu aplicativo Xamarin.tvOS não interagirá com sua interface diretamente como com iOS onde eles tocar imagens na tela do dispositivo, mas indiretamente de entre o espaço usando o [Siri remoto](#The-Siri-Remote).

Se seu aplicativo for um jogo, você poderá opcionalmente criar no suporte para 3ª parte, feita para iOS (MFI) [controladores de jogo Bluetooth](#Bluetooth-Game-Controllers) em seu aplicativo também.

[![](remote-bluetooth-images/intro01.png "O Bluetooth remoto e o controlador de jogo")](remote-bluetooth-images/intro01.png#lightbox)

Este artigo descreve o [Siri remoto](#The-Siri-Remote), [gestos de toque de superfície](#Touch-Surface-Gestures) e [botões remoto Siri](#Siri-Remote-Buttons) e mostra como trabalhar com eles via [gestos e Storyboards](#Gestures-and-Storyboards), [gestos e código](#Gestures-and-Code) e [manipulação de eventos de nível baixo](#Low-Level-Event-Handling). Finalmente, ele discute [trabalhando com os controladores de jogo](#Working-with-Game-Controllers) em um aplicativo Xamarin.tvOS.

<a name="The-Siri-Remote" />

## <a name="the-siri-remote"></a>Siri remoto

A maneira principal que os usuários interagirá com o Apple TV e seu aplicativo Xamarin.tvOS, é remoto Siri incluído. Apple projetado remoto para ligar a distância entre o usuário no sofá e a interface do usuário Apple TV exibido na sala na tela de TV.

Seu desafio como um desenvolvedor de aplicativo tvOS é criar uma interface do usuário rápida e fácil de usar visualmente atraentes que aproveita a superfície de toque de Siri Remote, o acelerômetro, giroscópio e botões.

[![](remote-bluetooth-images/remote01.png "Siri remoto")](remote-bluetooth-images/remote01.png#lightbox)

Siri remoto tem os seguintes recursos e usos esperados dentro de seu aplicativo tvOS:

|Recurso|Uso do aplicativo geral|Uso do aplicativo de jogo|
|---|---|---|
|**Superfície de toque**<br />Passe o dedo para navegar, pressionar para selecionar e mantenha menus contextuais.|**Passe o dedo/toque**<br />Navegação da interface do usuário entre os itens de controle.<br /><br />**Clique em**<br />Ativa o item (em foco) selecionado.|**Passe o dedo/toque**<br />Depende do projeto de jogo e pode ser usado como um bloco de D tocando as bordas.<br /><br />**Clique em**<br />Execute a função do botão principal.|
|**Menu**<br />Pressione para retornar à tela anterior ou menu.|Retorna a tela anterior e sai à tela de início para Apple TV de tela de aplicativo principal.|Pausar e retomar o jogo, para retornar à tela anterior e fecha a tela inicial para Apple TV na tela principal do aplicativo.|
|**Siri/pesquisa**<br />Em países com Siri, pressione e segure para controle de voz, em todos os outros países, exibe a tela de pesquisa.|N/D|N/D|
|**Play/Pause**<br />Reproduzir e pausar mídia ou fornece uma função secundária em aplicativos.|Inicia a reprodução de mídia e pausar ou retomar a reprodução.|Executa a função do botão secundário ou ignora o vídeo de Introdução (se existir).|
|**Início**<br />Pressione para retornar à tela inicial, clique duas vezes para exibir os aplicativos em execução, pressione e segure a suspensão do dispositivo.|N/D|N/D|
|**Volume**<br />Controles anexado volume equipamentos de áudio/vídeo.|N/D|N/D|

<a name="Touch-Surface-Gestures" />

## <a name="touch-surface-gestures"></a>Superfície gestos de toque

Superfície de toque de Siri Remote é capaz de detectar uma variedade de gestos desse tipo que você pode responder a em seu aplicativo Xamarin.tvOS:

|Passe o dedo|Clique em|Toque em|
|---|---|---|
|![](remote-bluetooth-images/Gesture01.png)|![](remote-bluetooth-images/Gesture02.png)|![](remote-bluetooth-images/Gesture03.png)|
|Move a seleção (foco) entre os elementos de interface do usuário na tela (para cima, para baixo à esquerda, à direita). Passar o dedo pode ser usado para percorrer as listas grandes de conteúdo rapidamente usando inércia.|Ativa o item selecionado (em foco) ou age como o botão principal em um jogo. Clicando e mantendo podem ativar menus contextuais ou funções secundárias.|Pouco tocando a superfície tocam as bordas age como botões direcional em uma direcional, movendo o foco para cima, para baixo, para a esquerda ou direita dependendo da área tocado. Dependendo do aplicativo, pode ser usado para revelar controles ocultos.|

Apple fornece as seguintes sugestões para trabalhar com gestos de toque superfície:

* **Diferenciar entre cliques e toques** -clicando é uma ação intencional pelo usuário e é adequado para seleção, ativação e o botão principal de um jogo. Tocar é mais sutil e deve ser usada com cautela, porque o usuário geralmente está mantendo remoto Siri em suas mãos e acidentalmente pode ativar um evento de toque facilmente.
* **Não redefina gestos padrão** -o usuário tem uma expectativa gestos específicos executa ações específicas, você não deve redefinir o significado ou a função desses gestos em seu aplicativo. A única exceção é um aplicativo de jogo durante os jogos ativo.
* **Definir novos gestos moderadamente** -novamente, o usuário tem uma expectativa gestos específicos executa ações específicas. Evite definir gestos personalizados para executar ações padrão. E, novamente, jogos são a exceção mais comum onde gestos personalizados podem adicionar divertido, envolvente play para o jogo.
* **Se apropriado, responder a toca direcional** - pouco tocando no canto Bordas da superfície de toque serão reagir como um direcional em um foco de movimentação do controlador de jogo ou a direção para cima, para baixo, esquerda ou direita. Se apropriado, você deve responder a esses gestos em seu aplicativo ou um jogo.

<a name="Siri-Remote-Buttons" />

## <a name="siri-remote-buttons"></a>Botões de Siri remoto

Além de gestos na superfície de toque, seu aplicativo pode responder ao usuário clicando na superfície de toque ou pressionando o botão Executar/Pausar. Se você estiver acessando Siri remota usando a estrutura de controlador de jogo, você também pode detectar o pressionamento do botão de Menu. 

Além disso, pressionamentos de botão de menu podem ser detectados com um reconhecedor de gestos padrão `UIKit` elementos. Se você interceptar o pressionamento do botão de Menu, você será responsável por fechando a exibição e o controlador de exibição atual e retornar ao anterior.

> [!IMPORTANT]
> Você deve **sempre** atribuir uma função ao botão Play/Pause no controle remoto. Ter um botão não funcionais pode tornar seu aplicativo a ser interrompido para o usuário final. Se você não tiver uma função válida para este botão, atribua a mesma função que o botão principal (clique na superfície de toque).




<a name="Gestures-and-Storyboards" />

## <a name="gestures-and-storyboards"></a>Gestos e Storyboards

A maneira mais fácil trabalhar com remoto Siri em seu aplicativo Xamarin.tvOS é adicionar reconhecedores de gestos para modos de exibição no Designer de Interface.

Para adicionar um reconhecedor de gestos, faça o seguinte:

1. No **Solution Explorer**, clique duas vezes o `Main.storyboard` de arquivo e abra-o para o Designer de Interface de edição.
2. Arraste um **reconhecedor de gestos de toque** do **biblioteca** e solte-o no modo de exibição: 

    [![](remote-bluetooth-images/storyboard01.png "Um reconhecedor de gestos de toque")](remote-bluetooth-images/storyboard01.png#lightbox)
3. Verificar **selecione** no **botão** seção o **Inspetor de atributo**: 

    [![](remote-bluetooth-images/storyboard02.png "Verifique a seleção")](remote-bluetooth-images/storyboard02.png#lightbox)
4. **Selecione** significa o gesto responde para o usuário clicando o **superfície Touch** remoto o Siri. Você também tem a opção de responder ao **Menu**, **executar/pausar**, **backup**, **para baixo**, **esquerda** e **Direita** botões.
5. Em seguida, conectar um **ação** do **reconhecedor de gestos de toque** e chamá-lo `TouchSurfaceClicked`: 

    [![](remote-bluetooth-images/storyboard03.png "Uma ação do reconhecedor de gestos de toque")](remote-bluetooth-images/storyboard03.png#lightbox)
6. Salve suas alterações e retornar ao Visual Studio para Mac.

Editar seu controlador de exibição (exemplo `FirstViewController.cs`) e adicione o seguinte código para manipular o gesto sendo acionado:

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

Para obter mais informações sobre como trabalhar com Storyboards, consulte nosso [Hello, tvOS Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md). Especificamente o [criar a Interface do usuário](~/ios/tvos/get-started/hello-tvos.md#Creating-the-User-Interface) e [escrever o código com tomadas e ações](~/ios/tvos/get-started/hello-tvos.md#Writing-the-Code) seções.

<a name="Gestures-and-Code" />

## <a name="gestures-and-code"></a>Gestos e código

Opcionalmente, você pode criar gestos diretamente no código do c# e adicioná-las a exibições na Interface do usuário. Por exemplo, para adicionar uma série de identificadores de gesto de passar, edite seu controlador de exibição e adicione o seguinte código:

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

## <a name="low-level-event-handling"></a>Manipulação de eventos de nível inferior

Se você estiver criando um tipo personalizado com base em `UIKit` em seu aplicativo Xamarin.tvOS (por exemplo `UIView`), você também tem a capacidade de fornecer tratamento de nível baixo de pressionamento do botão via `UIPress` eventos. 

Um `UIPress` evento é para tvOS um `UITouch` evento é para iOS, exceto `UIPress` retorna informações sobre o botão pressiona o Siri remoto ou outro conectado dispositivos Bluetooth (como um controlador de jogo). `UIPress` eventos descrevem o pressionamento do botão e seu estado (Began, cancelado, alterado ou concluído). 

Botões de analógico em dispositivos como controladores de jogo Bluetooth, `UIPress` também retorna a quantidade de força que está sendo aplicada ao botão. O `Type` propriedade o `UIPress` evento define qual botão físico tem estado alterado, enquanto o restante das propriedades descrevem a alteração que ocorreu.

O código a seguir mostra um exemplo de tratamento de baixo nível `UIPress` eventos para um `UIView`:

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

Assim como acontece com `UITouch` eventos, se você precisa implementar qualquer uma da `UIPress` substituições de evento, você deve implementar todos os quatro.

<a name="Bluetooth-Game-Controllers" />

## <a name="bluetooth-game-controllers"></a>Controladores de jogo de Bluetooth

Além de remoto Siri padrão que é fornecido com o Apple TV, 3ª parte, feita para iOS controladores de jogo Bluetooth (MFI) podem ser combinados com a Apple TV e usados para controlar o seu aplicativo Xamarin.tvOS.

[![](remote-bluetooth-images/game01.png "Controladores de jogo de Bluetooth")](remote-bluetooth-images/game01.png#lightbox)

Controladores de jogo podem ser usados para aprimorar a jogos e fornecer uma noção de imersão em um jogo. Eles também podem ser usados para controlar a interface padrão do Apple TV, o uso precisa alternar entre o controle remoto e o controlador.

> [!IMPORTANT]
> Os controladores de jogo Bluetooth são uma compra opcional que os usuários finais podem fazer, seu aplicativo não é possível forçar o usuário a comprar um. Se seu aplicativo dá suporte a controladores de jogo também deve suportar Siri remoto para que o jogo seja utilizável por todos os usuários da Apple TV.

Um controlador de jogo tem os seguintes recursos e usos esperados dentro de seu aplicativo tvOS:

|Recurso|Uso do aplicativo geral|Uso do aplicativo de jogo|
|---|---|---|
|**D-Pad**|Navega por meio de elementos de interface do usuário (altera o foco).|Depende de jogo.|
|**A**|Ativa o item (em foco) selecionado.|Executa a função do botão principal e confirma as ações de caixa de diálogo.|
|**B**|Retorna a tela anterior ou para a tela inicial é encerrado se na tela principal do aplicativo.|Executa a função do botão secundário ou retorna para a tela anterior.|
|**X**|Inicia a reprodução de mídia ou pausar/reinicia a reprodução.|Depende de jogo.|
|**Y**|N/D|Depende de jogo.|
|**Menu**|Retorna a tela anterior ou para a tela inicial é encerrado se na tela principal do aplicativo.|Pausar/retomar jogos, para retornar à tela anterior ou saídas para a tela inicial se na tela principal do aplicativo.|
|**Botão do lado esquerdo**|Navega esquerda.|Depende de jogo.|
|**Gatilho esquerdo**|Navega esquerda.|Depende de jogo.|
|**Botão do lado direito**|Navega direita.|Depende de jogo.|
|**Gatilho direito**|Navega direita|Depende de jogo.|
|**Analógico esquerdo**|Navega por meio de elementos de interface do usuário (altera o foco).|Depende de jogo.|
|**Direcional direita**|N/D|Depende de jogo.|

Apple fornece as seguintes sugestões para trabalhar com os controladores de jogo:

- **Confirme as conexões do controlador de jogo** -seu aplicativo tvOS pode ser iniciado e interrompido a qualquer momento pelo usuário final. Sempre verifique a presença de um controlador de jogo no início do aplicativo ou tempos de ativos e tomar as medidas necessárias.
- **Verifique se o aplicativo funciona em Siri remoto e os controladores de jogo** -não exigir que os usuários alternar entre Siri remoto e um controlador de jogo para usar seu aplicativo. Teste seu aplicativo frequentemente com os dois tipos de controladores, garantindo que tudo é fácil navegar e funciona como esperado.
- **Fornecer uma maneira volta** -pressionando o botão de Menu deve sempre retornar para a tela anterior. Se o usuário estiver na tela do aplicativo principal, no botão de Menu deve retorná-los à tela de início para Apple TV. Durante um jogo, o botão de Menu deve exibir um alerta dar ao usuário a capacidade de pausar ou retomar jogos ou retornar ao menu principal.

<a name="Working-with-Game-Controllers" />

## <a name="working-with-game-controllers"></a>Trabalhando com controladores de jogo

Como mencionado acima, além do padrão Siri remoto que é fornecido com o Apple TV, o usuário pode opcionalmente anexar um 3ª parte, feita para controladores de jogo de Bluetooth do iOS (MFI) e usá-lo para controlar seu aplicativo Xamarin.tvOS.

Se seu aplicativo necessário entrada do controlador de baixo nível, você pode usar da Apple [Framework do controlador de jogo](https://developer.apple.com/library/prerelease/tvos/documentation/ServicesDiscovery/Conceptual/GameControllerPG/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013276) que tem as seguintes modificações para tvOS:

- O perfil do controlador de jogo Micro (`GCMicroGamepad`) foi adicionado ao destino remoto Siri.
- O novo `GCEventViewController` classe pode ser usada para encaminhar eventos de controlador de jogo por meio de seu aplicativo. Consulte o [determinando entrada do controlador de jogo](#Determining-Game-Controller-Input) seção abaixo para obter mais detalhes.

<a name="Game-Controller-Support-Requirements" />

### <a name="game-controller-support-requirements"></a>Requisitos de suporte do controlador de jogo

Apple tem vários requisitos específicos que devem ser atendidos se seu aplicativo Xamarin.tvOS dá suporte a controladores de jogo:

- **Você deve dar suporte remoto Siri** -você sempre deve dar suporte remoto Siri. O jogo não pode solicitar uma parte 3 de controlador de jogo ser reproduzidos.
- **Você deve dar suporte a Layout de controle estendido** -tvOS todos os controladores de jogo são não formfitting estendidos controladores.
- **Jogos devem ser Playable com controladores stand-alone** -se seu aplicativo dá suporte a um controlador de jogo estendido, devem ser reproduzido somente com esse controlador de jogo.
- **Você deve dar suporte a no botão Play/Pause** -durante um jogo, se o usuário pressionar o botão executar/pausar, você deve exibir um alerta dar ao usuário a capacidade de pausar ou retomar jogos ou retornar ao menu principal.

<a name="Enabling-Game-Controller-Support" />

### <a name="enabling-game-controller-support"></a>Habilitar o suporte de controlador de jogo

Para habilitar o suporte de controlador de jogo em seu aplicativo Xamarin.tvOS, clique duas vezes o `Info.plist` arquivo o **Gerenciador de soluções** para abri-lo para edição:

[![](remote-bluetooth-images/game02.png "O editor de info. plist")](remote-bluetooth-images/game02.png#lightbox)

Sob o **jogo controlador** seção, marque-a por **ativar os controladores de jogo**, verifique todos os tipos de controlador de jogo que terão suporte pelo aplicativo.

<a name="Using-the-Siri-Remote-as-a-Game-Controller" />

### <a name="using-the-siri-remote-as-a-game-controller"></a>Usando o Siri remoto como um controlador de jogo

Siri remota que vêm com o Apple TV pode ser usada como um controlador de jogo limitado. Como outros controladores de jogo, ele é exibido na estrutura do controlador de jogo como um `GCController` objeto e oferece suporte a `GCMotion` e o `GCMicroGamepad` perfis.

Siri remoto tem as seguintes características quando usado como um controlador de jogo:

- A superfície de toque pode ser usada como um bloco de D que fornece dados de entrada analógicos.
- O controle remoto pode ser usado na orientação paisagem ou um retrato e seu aplicativo decide se o objeto de perfil deve inverter automaticamente os dados de entrada.
- Clique na superfície de toque atua como pressionar o botão **um** em um controlador de jogo.
- O botão Executar/Pausar age como botão **X** em um controlador de jogo.
- Botão de Menu deverá exibir um alerta dar ao usuário a capacidade de pausar ou retomar jogos ou retornar ao menu principal.

<a name="Summary" />

### <a name="determining-game-controller-input"></a>Determinando a entrada do controlador de jogo

Ao contrário do iOS onde os eventos de controlador de jogo podem ser recebidos em paralelo com eventos de toque, tvOS processa todos os eventos de nível baixo para oferecer um alto nível `UIKit` eventos. Como resultado, se você precisar acessar os eventos de controlador de jogo de baixo níveis, você precisará desativar `UIKit`do comportamento padrão.

Em tvOS, quando você deseja processar a entrada do controlador de jogo diretamente, você precisa usar um `GCEventViewController` (ou uma subclasse) para exibir o jogo da Interface do usuário. Sempre que um `GCEventViewController` é o *resposta*, entrada de controlador de jogo será capturada e entregue ao seu aplicativo por meio da estrutura de controlador de jogo.

Você pode usar o `UserInteractionEnabled` propriedade o `GCEventViewController` classe para alternar como os eventos são processados e tratados.

Para obter informações sobre como implementar o suporte ao controlador de jogo, consulte da Apple [trabalhando com os controladores de jogo](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/WorkingwithGameControllers.html) seção o [guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/index.html) e [o controlador de jogo Guia de programação](https://developer.apple.com/library/prerelease/tvos/documentation/ServicesDiscovery/Conceptual/GameControllerPG/Introduction/Introduction.html).

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou remoto Siri novo que é fornecido com o Apple TV, gestos de toque de superfície e botões Siri remoto. Em seguida, ele coberto trabalhar com gestos e Storyboards, gestos e código e eventos de nível inferior. Por fim, se abordado trabalhando com os controladores de jogo.



## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guias de Interface Humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
