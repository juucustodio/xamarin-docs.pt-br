---
title: Siri remoto e controladores de Bluetooth para tvOS no Xamarin
description: Este artigo descreve como trabalhar com o Siri remoto e controladores de jogos do Bluetooth em aplicativos de tvOS gravados com Xamarin.
ms.prod: xamarin
ms.assetid: BDB9894A-236B-424B-9032-ACD12A6C5720
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 79022f7a454ea423fa3112a4c4ade2bcd471fbb8
ms.sourcegitcommit: 946ce514fd6575aa6b93ff24181e02a60b24b106
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2019
ms.locfileid: "58677944"
---
# <a name="siri-remote-and-bluetooth-controllers-for-tvos-in-xamarin"></a>Siri remoto e controladores de Bluetooth para tvOS no Xamarin

Usuários de seu aplicativo tvos não interagirá com sua interface diretamente como com o iOS onde eles tap imagens na tela do dispositivo, mas indiretamente em toda a sala usando o [Siri remoto](#The-Siri-Remote).

Se seu aplicativo é um jogo, você pode opcionalmente compilar no suporte para terceiros 3ª, feita para iOS (MFI) [controladores de jogos de Bluetooth](#Bluetooth-Game-Controllers) em seu aplicativo também.

[![](remote-bluetooth-images/intro01.png "O Bluetooth remoto e o controlador de jogo")](remote-bluetooth-images/intro01.png#lightbox)

Este artigo descreve o [Siri remoto](#The-Siri-Remote), [gestos de toque de superfície](#Touch-Surface-Gestures) e [botões de Siri remoto](#Siri-Remote-Buttons) e mostra como trabalhar com eles por meio de [gestos e Storyboards](#Gestures-and-Storyboards), [gestos e código](#Gestures-and-Code) e [tratamento de evento de baixo nível](#Low-Level-Event-Handling). Por fim, ele aborda [trabalhar com controladores de jogo](#Working-with-Game-Controllers) em um aplicativo xamarin. tvos.

<a name="The-Siri-Remote" />

## <a name="the-siri-remote"></a>Siri remoto

A principal maneira que os usuários serão interagindo com a Apple TV e seu aplicativo tvos, é por meio do Siri remoto incluído. Apple projetado remoto para acabar com a distância entre o usuário à frente no sofá e a interface do usuário da Apple TV exibido dentro de uma sala na tela da TV.

Seu desafio como um desenvolvedor de aplicativo tvOS é criar uma interface do usuário rápida, fácil de usar e visualmente atraentes que aproveita a superfície de toque de Siri Remote, acelerômetro, giroscópio e botões.

[![](remote-bluetooth-images/remote01.png "Siri remoto")](remote-bluetooth-images/remote01.png#lightbox)

Siri remoto tem os seguintes recursos e usos esperados dentro de seu aplicativo tvOS:

|Recurso|Uso do aplicativo geral|Uso do aplicativo de jogos|
|---|---|---|
|**Superfície de toque**<br />Passe o dedo para navegar, pressione a para selecionar e armazenar para menus contextuais.|**Passe o dedo/toque**<br />Navegação da interface do usuário entre os itens de controle.<br /><br />**Clique em**<br />Ativa o item selecionado do (em foco).|**Passe o dedo/toque**<br />Depende do projeto de jogo e pode ser usado como um direcional tocando nas bordas.<br /><br />**Clique em**<br />Execute a função do botão principal.|
|**Menu**<br />Pressione para retornar à tela anterior ou menu.|Retorna à tela anterior e sai para a tela inicial para Apple TV na tela principal do aplicativo.|Pausar e retomar o jogo, para retornar à tela anterior e fecha a tela inicial para Apple TV na tela principal do aplicativo.|
|**Siri/Search**<br />Em países com Siri, pressione e segure para controle de voz, em todos os outros países, exibe a tela de pesquisa.|N/D|N/D|
|**Reproduzir/Pausar**<br />Reproduzir e pausar mídia ou fornece uma função secundária em aplicativos.|Inicia a reprodução de mídia e pausar/retomar a reprodução.|Executa a função do botão secundário ou ignora o vídeo de Introdução (se existir).|
|**Início**<br />Pressione para retornar à tela inicial, clique duas vezes para exibir os aplicativos em execução, pressione e segure em suspensão do dispositivo.|N/D|N/D|
|**Volume**<br />Controles o volume de equipamento de áudio/vídeo anexado.|N/D|N/D|

<a name="Touch-Surface-Gestures" />

## <a name="touch-surface-gestures"></a>Superfície gestos de toque

Superfície de toque de Siri Remote é capaz de detectar uma variedade de gestos de dedo que você pode responder a em seu aplicativo tvos:

|Passe o dedo|Clique em|Toque em|
|---|---|---|
|![](remote-bluetooth-images/Gesture01.png)|![](remote-bluetooth-images/Gesture02.png)|![](remote-bluetooth-images/Gesture03.png)|
|Move a seleção (foco) entre os elementos de interface do usuário na tela (para cima, para baixo à esquerda, com o botão direito). Passar o dedo pode ser usado para percorrer grandes listas de conteúdo rapidamente usando inércia.|Ativa o item selecionado (em foco) ou funciona como o botão principal em um jogo. Clicando e segurando podem ativar funções secundárias ou menus contextuais.|Apenas superficialmente tocando a superfície de toque nas bordas atua como botões direcional em uma direcional, movendo o foco para cima para baixo, esquerda ou direita dependendo da área tocado. Dependendo do aplicativo, pode ser usado para revelar controles ocultos.|

A Apple fornece as sugestões a seguir para trabalhar com gestos de toque superfície:

* **Diferenciar entre os cliques e toques** -clicando é uma ação intencional pelo usuário e é adequado para seleção, ativação e o botão principal de um jogo. Um toque é mais sutil e deve ser usado com moderação, pois o usuário geralmente está mantendo o Siri remoto em suas mãos e acidentalmente pode ativar um evento de toque com facilidade.
* **Não redefinir gestos padrão** -o usuário tem uma expectativa de que os gestos específicos executará ações específicas, você não deve redefinir o significado ou a função desses gestos em seu aplicativo. A única exceção é um aplicativo de jogo durante os jogos Active Directory.
* **Definem novos gestos moderadamente** -novamente, o usuário tem uma expectativa de que os gestos específicos executará ações específicas. Você deve evitar definindo gestos personalizados para executar ações padrão. E, novamente, jogos são a exceção mais comum onde gestos personalizados podem adicionar play divertido e envolvente para o jogo.
* **Se apropriado, responder aos toques direcional** - levemente tocando no canto Bordas da superfície de toque serão reagir como um direcional em um foco de movimentação do controlador de jogo ou a direção para cima, para baixo, esquerda ou com o botão direito. Se apropriado, você deve responder a esses gestos em seu aplicativo ou jogo.

<a name="Siri-Remote-Buttons" />

## <a name="siri-remote-buttons"></a>Siri remoto botões

Além de gestos na superfície de toque, seu aplicativo pode responder ao usuário clicar na superfície de toque ou pressionando o botão Reproduzir/Pausar. Se você estiver acessando o Siri remoto usando a estrutura de controlador de jogo, você também pode detectar o pressionamento do botão de Menu. 

Além disso, os pressionamentos de botão de menu podem ser detectados usando um reconhecedor de gestos com padrão `UIKit` elementos. Se você interceptar o pressionamento do botão de Menu, você será responsável para fechar o modo de exibição atual e o controlador de exibição e retornar ao anterior.

> [!IMPORTANT]
> Você deve **sempre** atribuir uma função ao botão Reproduzir/Pausar no controle remoto. Ter um botão não funcional pode tornar seu aplicativo a ser interrompida para o usuário final. Se você não tiver uma função válida para este botão, atribua a mesma função que o botão principal (clique na superfície de toque).

<a name="Gestures-and-Storyboards" />

## <a name="gestures-and-storyboards"></a>Gestos e Storyboards

A maneira mais fácil trabalhar com o Siri remoto em seu aplicativo tvos é adicionar reconhecedores de gestos para seus modos de exibição no Designer de Interface.

Para adicionar um reconhecedor de gestos, faça o seguinte:

1. No **Gerenciador de soluções**, clique duas vezes o `Main.storyboard` de arquivo e abri-lo para o Designer de Interface de edição.
2. Arraste uma **reconhecedor de gestos de toque** da **biblioteca** e solte-o no modo de exibição: 

    [![](remote-bluetooth-images/storyboard01.png "Um reconhecedor de gestos de toque")](remote-bluetooth-images/storyboard01.png#lightbox)
3. Verifique **selecionar** na **botão** seção o **Inspetor de atributo**: 

    [![](remote-bluetooth-images/storyboard02.png "Verificar Select")](remote-bluetooth-images/storyboard02.png#lightbox)
4. **Selecione** significa que o gesto responde ao usuário clicar na **superfície Touch** no Siri remoto. Você também tem a opção de responder à **menus**, **Reproduzir/Pausar**, **backup**, **para baixo**, **esquerda** e **Direita** botões.
5. Em seguida, conectar uma **ação** da **reconhecedor de gestos de toque** e chamá-lo `TouchSurfaceClicked`: 

    [![](remote-bluetooth-images/storyboard03.png "Uma ação do reconhecedor de gestos de toque")](remote-bluetooth-images/storyboard03.png#lightbox)
6. Salve as alterações e retorne ao Visual Studio para Mac.

Editar seu controlador de exibição (exemplo `FirstViewController.cs`) de arquivo e adicione o seguinte código para manipular o gesto que está sendo disparado:

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

Para obter mais informações sobre como trabalhar com Storyboards, consulte nosso [Olá, guia de início rápido do tvOS](~/ios/tvos/get-started/hello-tvos.md). Especificamente o [criando a Interface do usuário](~/ios/tvos/get-started/hello-tvos.md#Creating-the-User-Interface) e [escrevendo o código com saídas e ações](~/ios/tvos/get-started/hello-tvos.md#Writing-the-Code) seções.

<a name="Gestures-and-Code" />

## <a name="gestures-and-code"></a>Gestos e código

Opcionalmente, você pode criar gestos diretamente no C# de código e adicioná-los a modos de exibição na Interface do usuário. Por exemplo, para adicionar uma série de reconhecedores de gestos de passar o dedo, edite seu controlador de exibição e adicione o seguinte código:

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

## <a name="low-level-event-handling"></a>Tratamento de evento de baixo nível

Se você estiver criando um tipo personalizado com base no `UIKit` em seu aplicativo tvos (por exemplo `UIView`), você também tem a capacidade de fornecer manipulação de nível baixo de pressionamento do botão por meio de `UIPress` eventos. 

Um `UIPress` evento é para tvOS uma `UITouch` evento é para o iOS, exceto `UIPress` retorna informações sobre o botão pressiona no Siri remoto ou outro anexado dispositivos Bluetooth (como um controlador de jogo). `UIPress` eventos descrevem o pressionamento do botão e seu estado (6ffc308f, cancelado, alterado ou concluído). 

Para botões analógico em dispositivos, como controladores de jogo do Bluetooth, `UIPress` também retorna a quantidade de força que está sendo aplicada ao botão. O `Type` propriedade do `UIPress` evento define qual botão físico tem estado alterado, enquanto o restante das propriedades descrevem a alteração ocorrida.

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

Assim como acontece com `UITouch` eventos, se você precisar implementar qualquer uma da `UIPress` substituições de evento, você deve implementar todos os quatro.

<a name="Bluetooth-Game-Controllers" />

## <a name="bluetooth-game-controllers"></a>Controladores de jogo de Bluetooth

Além de Siri remoto padrão que é fornecido com a Apple TV, 3ª parte, feita para iOS controladores de jogo de Bluetooth (MFI) pode ser emparelhado com o Apple TV e usados para controlar o seu aplicativo xamarin. tvos.

[![](remote-bluetooth-images/game01.png "Controladores de jogo de Bluetooth")](remote-bluetooth-images/game01.png#lightbox)

Controladores de jogos podem ser usados para aprimorar o jogo e fornecer um senso de imersão em um jogo. Eles também podem ser usados para controlar a interface de Apple TV padrão para que o uso não precise alternar entre o computador remoto e o controlador.

> [!IMPORTANT]
> Controladores de jogo de Bluetooth são uma compra adicional que os usuários finais podem fazer, seu aplicativo não pode forçar o usuário compra um. Se seu aplicativo dá suporte a controladores de jogos também deve suportar Siri remoto para que o jogo seja utilizável por todos os usuários de Apple TV.

Um controlador de jogo tem os seguintes recursos e usos esperados dentro de seu aplicativo tvOS:

|Recurso|Uso do aplicativo geral|Uso do aplicativo de jogos|
|---|---|---|
|**D-Pad**|Navega por meio de elementos de interface do usuário (altera o foco).|Depende do jogo.|
|**A**|Ativa o item selecionado (em foco).|Executa a função do botão principal e confirma as ações de caixa de diálogo.|
|**B**|Retorna à tela anterior ou sai para a tela inicial se na tela principal do aplicativo.|Executa a função do botão secundário ou retorna para a tela anterior.|
|**X**|Inicia a reprodução de mídia ou pausar/reinicia a reprodução.|Depende do jogo.|
|**Y**|N/D|Depende do jogo.|
|**Menu**|Retorna à tela anterior ou sai para a tela inicial se na tela principal do aplicativo.|Pausar/retomar o jogo, para retornar à tela anterior ou sai para a tela inicial se na tela principal do aplicativo.|
|**Botão esquerdo Shoulder**|Navega à esquerda.|Depende do jogo.|
|**Gatilho à esquerda**|Navega à esquerda.|Depende do jogo.|
|**Botão de direito Shoulder**|Navega à direita.|Depende do jogo.|
|**Gatilho certo**|Navega direita|Depende do jogo.|
|**Left Thumbstick**|Navega por meio de elementos de interface do usuário (altera o foco).|Depende do jogo.|
|**Alavanca direcional direita**|N/D|Depende do jogo.|

A Apple fornece as sugestões a seguir para trabalhar com controladores de jogo:

- **Confirme se as conexões de controlador de jogo** -seu aplicativo tvOS pode ser iniciado e interrompido a qualquer momento pelo usuário final. Você sempre deve verificar a presença de um controlador de jogo no início do aplicativo ou tempos de ativos e tomar as medidas necessárias.
- **Verifique se seu aplicativo funciona no Siri remoto e controladores de jogo** -não exigem que os usuários alternem entre Siri remoto e um controlador de jogo para usar seu aplicativo. Teste seu aplicativo frequentemente com ambos os tipos de controladores de garantir que tudo o que é fácil navegar e funciona conforme o esperado.
- **Fornecer uma maneira de volta** -pressionando o botão de Menu deve sempre retornar à tela anterior. Se o usuário estiver na tela do aplicativo principal, o botão de Menu deve retorná-los para a tela de início para Apple TV. Durante o jogo, o botão de Menu deve exibir um alerta, dando ao usuário a capacidade de pausar/retomar o jogo ou retornar ao menu principal.

<a name="Working-with-Game-Controllers" />

## <a name="working-with-game-controllers"></a>Trabalhar com controladores de jogos

Como mencionado acima, além dos padrão Siri remoto que é fornecido com a Apple TV, o usuário pode, opcionalmente, anexar um dia 3 de terceiros, feita para controladores de jogo do iOS (MFI) Bluetooth e usá-lo para controlar o aplicativo xamarin. tvos.

Se a entrada de baixo nível do controlador de necessária de seu aplicativo, você pode usa da Apple [estrutura de controlador de jogo](https://developer.apple.com/library/prerelease/tvos/documentation/ServicesDiscovery/Conceptual/GameControllerPG/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013276) que tem as seguintes modificações para tvOS:

- O perfil do controlador de jogo Micro (`GCMicroGamepad`) foi adicionado ao Siri remoto de destino.
- O novo `GCEventViewController` classe pode ser usado para encaminhar eventos de controlador de jogo por meio de seu aplicativo. Consulte a [determinando o jogo de controlador de entrada](#determining-game-controller-input) seção abaixo para obter mais detalhes.

<a name="Game-Controller-Support-Requirements" />

### <a name="game-controller-support-requirements"></a>Requisitos de suporte do controlador de jogo

Apple tem vários requisitos específicos que devem ser atendidos se o seu aplicativo tvos dá suporte a controladores de jogo:

- **Você deve dar suporte a Siri remoto** -você sempre deve dar suporte a Siri remoto. Seu jogo não é possível exigir uma 3ª parte controlador de jogo para ser reproduzidos.
- **Você deve dar suporte o Layout do controle estendido** -tvOS todos os controladores de jogo são não formfitting estendido controladores.
- **Jogos devem ser Playable com controladores stand-alone** -se seu aplicativo dá suporte a um controlador de jogo estendido, devem ser reproduzido exclusivamente com esse controlador de jogo.
- **Você deve dar suporte a botão Play/Pause** -durante o jogo, se o usuário pressiona o botão Reproduzir/Pausar, você deve exibir um alerta, dando ao usuário a capacidade de pausar/retomar jogabilidade ou retornar ao menu principal.

<a name="Enabling-Game-Controller-Support" />

### <a name="enabling-game-controller-support"></a>Habilitando o suporte de controlador de jogo

Para habilitar o suporte de controlador de jogo em seu aplicativo tvos, clique duas vezes o `Info.plist` do arquivo na **Gerenciador de soluções** para abri-lo para edição:

[![](remote-bluetooth-images/game02.png "O editor de info. plist")](remote-bluetooth-images/game02.png#lightbox)

Sob o **controlador de jogo** seção, colocar uma marca de seleção por **habilitar controladores de jogo**, em seguida, verifique todos os tipos de controlador de jogo que serão compatível com o aplicativo.

<a name="Using-the-Siri-Remote-as-a-Game-Controller" />

### <a name="using-the-siri-remote-as-a-game-controller"></a>Usar Siri remoto como um controlador de jogo

Siri remoto que vêm com a Apple TV pode ser usado como um controlador de jogo limitado. Assim como os outros controladores de jogo, ele aparece no Framework de controlador de jogo como um `GCController` objeto e dá suporte a `GCMotion` e o `GCMicroGamepad` perfis.

Siri remoto tem as seguintes características quando usado como um controlador de jogo:

- A superfície de toque pode ser usada como um bloco de D que fornece dados de entrada analógicos.
- O controle remoto pode ser usado na orientação de um retrato ou paisagem e seu aplicativo decide se o objeto de perfil deve inverter automaticamente os dados de entrada.
- Clicar na superfície de toque atua como pressionar o botão **um** em um controlador de jogo.
- O botão Play/Pause atua como o botão **X** em um controlador de jogo.
- O botão de Menu deve exibir um alerta, dando ao usuário a capacidade de pausar/retomar o jogo ou retornar ao menu principal.

<a name="Summary" />

### <a name="determining-game-controller-input"></a>Determinando a entrada do controlador de jogo

Ao contrário do iOS em que os eventos do controlador de jogo podem ser recebidos em paralelo com eventos de toque, tvOS processa todos os eventos de nível baixo para fornecer um alto nível `UIKit` eventos. Como resultado, se você precisar acessar os eventos de controlador de jogo de nível inferior, você precisará desativar `UIKit`do comportamento padrão.

Tvos, quando você deseja processar a entrada do controlador de jogo diretamente, você precisa usar um `GCEventViewController` (ou uma subclasse) para exibir o jogo da Interface do usuário. Sempre que um `GCEventViewController` é o *Respondente primeiro*, controlador de jogo de entrada será capturada e entregues ao aplicativo por meio da estrutura de controlador de jogo.

Você pode usar o `UserInteractionEnabled` propriedade do `GCEventViewController` classe a ser alternada como os eventos são processados e tratados.

Para obter informações sobre como implementar o suporte ao controlador de jogo, consulte da Apple [trabalhar com controladores de jogo](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/WorkingwithGameControllers.html) seção o [guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/index.html) e [controlador de jogo Guia de programação](https://developer.apple.com/library/prerelease/tvos/documentation/ServicesDiscovery/Conceptual/GameControllerPG/Introduction/Introduction.html).

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou a nova Siri remoto que é fornecido com a Apple TV, gestos de toque de superfície e botões de Siri remoto. Em seguida, ele abordou a trabalhar com gestos e Storyboards, gestos e código e eventos de nível baixo. Por fim, se abordado a trabalhar com controladores de jogo.



## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de Interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
