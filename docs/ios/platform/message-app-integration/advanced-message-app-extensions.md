---
title: Extensões de aplicativo de mensagens avançadas
description: Este artigo mostra técnicas avançadas para trabalhar com extensões de aplicativo de mensagem em uma solução de xamarin que integra o aplicativo de mensagens e apresenta novos recursos para o usuário.
ms.prod: xamarin
ms.assetid: 394A1FDA-AF70-4493-9B2C-4CFE4BE791B6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: cd2cabf98c83bba7502e8533e482713a9c43f67a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="advanced-message-app-extensions"></a>Extensões de aplicativo de mensagens avançadas

_Este artigo mostra técnicas avançadas para trabalhar com extensões de aplicativo de mensagem em uma solução de xamarin que integra o aplicativo de mensagens e apresenta novos recursos para o usuário._


Novo para iOS 10, uma mensagem da extensão do aplicativo integra o **mensagens** aplicativo e apresenta novas funcionalidades para o usuário. A extensão pode enviar mensagens de texto, adesivos, arquivos de mídia e interativas.

## <a name="about-message-app-extensions"></a>Sobre extensões de aplicativo de mensagem

Como mencionado acima, uma mensagem da extensão do aplicativo integra o **mensagens** aplicativo e apresenta novas funcionalidades para o usuário. A extensão pode enviar mensagens de texto, adesivos, arquivos de mídia e interativas. Dois tipos de mensagem da extensão do aplicativo estão disponíveis:

- **Pacotes de etiqueta** -contém uma coleção de selos que o usuário pode adicionar a uma mensagem. Pacotes de etiqueta podem ser criados sem escrever nenhum código.
- **iMessage aplicativo** -pode apresentar uma Interface de usuário personalizada dentro do aplicativo de mensagens para selecionar adesivo, inserindo texto, incluindo arquivos de mídia (com conversões de tipo opcional) e criação, edição e enviar mensagens de interação.

Extensões de aplicativos de mensagens fornece três tipos de conteúdo principais:

- **Mensagens interativas** -são um tipo de conteúdo da mensagem personalizada que gera um aplicativo, quando o usuário toca na mensagem, o aplicativo será iniciado em primeiro plano.
- **Adesivo** -são imagens geradas pelo aplicativo que pode ser incluído nas mensagens enviadas entre os usuários. Consulte nossa [sorvete construtor](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/) aplicativo de exemplo para um exemplo de implementação de um aplicativo de etiqueta com o pacote.
- **Outros tipos de conteúdo com suporte** -o aplicativo poderá fornecer o conteúdo, como fotos, vídeos, texto ou links do tipo que sempre tem suporte pelo aplicativo de mensagens.

Novo para iOS 10, o aplicativo de mensagem agora inclui seu próprio armazenamento de aplicativo dedicado e internos. Todos os aplicativos que incluem as extensões de aplicativos de mensagem serão exibidos e promovidos no repositório. A gaveta de aplicativo de mensagens novas exibirá todos os aplicativos que foram baixados da loja de aplicativos de mensagens para fornecer acesso rápido aos usuários.

Também novo no iOS 10, Apple adicionou Inline App Attribution que permite ao usuário descobrir facilmente um aplicativo. Por exemplo, se um usuário envia o conteúdo para outro em um aplicativo que o usuário 2º não tiver instalado (como uma etiqueta por exemplo), o nome do aplicativo envio será listado no conteúdo no histórico de mensagens. Se o usuário toca o aplicativo nome, a mensagem App Store, é possível abrir e o aplicativo selecionado no repositório.

Extensões de aplicativos de mensagens são semelhantes aos aplicativos do iOS que o desenvolvedor é familiar para a criação e terão acesso a todas as estruturas padrão e os recursos de um aplicativo iOS padrão existente. Por exemplo:

- Eles têm acesso a compra no aplicativo.
- Eles têm acesso ao pagamento da Apple.
- Eles têm acesso ao dispositivo de hardware, como a câmera.

Somente há suporte para extensões de aplicativos de mensagens no iOS 10, no entanto, o conteúdo que enviam essas extensões é visível em dispositivos watchOS e macOS. O novo _Recents Page_ adicionado ao watchOS 3, exibirá adesivos recentes que foram enviados do telefone, incluindo aqueles em extensões de aplicativos de mensagem, e permitir que o usuário enviar esse adesivo de inspeção.

## <a name="about-interactive-messages"></a>Sobre mensagens interativas

Mensagens interativas apresentam uma bolha de mensagem personalizado e são fornecidas por uma extensão de aplicativo de mensagem. Eles permitem que o usuário criar mensagem interativa conteúdo, insira-o no campo de entrada de mensagem e enviá-lo.

[![](advanced-message-app-extensions-images/interactive01.png "Criando o conteúdo da mensagem interativa")](advanced-message-app-extensions-images/interactive01.png#lightbox)

O usuário pode responder a uma mensagem interativa tocando suas bolhas de mensagem no histórico de mensagens para carregar a extensão do aplicativo de mensagem que o criou. A extensão será iniciado em tela inteira e permitir que o usuário para compor uma resposta e enviará de volta para o usuário de origem.

[![](advanced-message-app-extensions-images/interactive02.png "A extensão iniciado em tela inteira")](advanced-message-app-extensions-images/interactive02.png#lightbox)


Os tópicos a seguir serão abordados em detalhes a seguir:

- Visão geral de API de mensagens
- O ciclo de vida de extensão
- Compor uma mensagem
- Enviar uma mensagem

## <a name="messages-api-overview"></a>Visão geral de API de mensagens

Quando invocado pelo usuário, uma extensão do aplicativo de mensagem será exibida na parte inferior do histórico de mensagens no modo de exibição compact:

[![](advanced-message-app-extensions-images/interactive03.png "Visão geral de API de mensagens")](advanced-message-app-extensions-images/interactive03.png#lightbox)

1. O `MSMessageAppViewController` objeto na extensão de aplicativo de mensagem é a classe principal que é chamada quando a extensão modo de exibição para o usuário.
2. A conversa é apresentada ao usuário como um `MSConversation` a instância do objeto.
3. O `MSMessage` classe representa um determinado bolhas de mensagem na conversa.
4. `MSSession` Controla como uma mensagem é enviada.
5. `MSMessageTemplateLayout` Controla como a mensagem é exibida

## <a name="the-extension-lifecycle"></a>O ciclo de vida de extensão

Dê uma olhada no processo de extensão do aplicativo uma mensagem se torne ativa:

[![](advanced-message-app-extensions-images/interactive04.png "O processo de extensão do aplicativo uma mensagem se torne ativa")](advanced-message-app-extensions-images/interactive04.png#lightbox)

1. Quando uma extensão é iniciada (por exemplo, da gaveta de aplicativo), o aplicativo de mensagem iniciará um processo.
2. O `DidBecomeActive` método é chamado e passado um `MSConversation` que representa a conversa da qual a mensagem da extensão do aplicativo está em execução no.
3. Como a extensão baseia fora de um `UIViewController` ambos `ViewWillAppear` e `ViewDidAppear` são chamados.

Em seguida, dê uma olhada no processo de uma extensão de aplicativo de mensagem se torne desativado:

[![](advanced-message-app-extensions-images/interactive05.png "O processo de uma extensão de aplicativo de mensagem se torne desativado")](advanced-message-app-extensions-images/interactive05.png#lightbox)

1. Quando a mensagem da extensão do aplicativo está sendo desativada, o `ViewWillDisappear` método será chamado primeiro.
2. Em seguida, o `ViewDidDisappear` método será chamado.
3. O `WillResignActive` método é chamado e passado um `MSConversation` que representa a conversa da qual a mensagem da extensão do aplicativo está em execução no. Neste ponto, a conexão entre o aplicativo de mensagens e a extensão está prestes a ser liberado.
4. Em algum momento posterior, o processo foi encerrado, o aplicativo de mensagens.

Como uma extensão é um processo de vida curta, ela será encerrada agressivamente pelo sistema para conservar energia da bateria e processamento. O desenvolvedor deve manter isso em mente ao projetar e implementar uma extensão de aplicativo de mensagem.

## <a name="composing-a-message"></a>Compor uma mensagem

Depois que a mensagem da extensão do aplicativo está em execução em um processo e exibiu sua Interface do usuário, o código a seguir pode ser usado para compor uma nova mensagem:

```csharp
MSMessage ComposeMessage (IceCream iceCream, string caption, MSSession session = null)
{
    var components = new NSUrlComponents {
        QueryItems = iceCream.QueryItems
    };

    var layout = new MSMessageTemplateLayout {
        Image = iceCream.RenderSticker (true),
        Caption = caption
    };

    var message = new MSMessage (session ?? new MSSession()) {
        Url = components.Url,
        Layout = layout
    };

    return message;
}
```

Esse código cria um novo `MSMessage` e define várias propriedades (como `Url`). Enquanto a mensagem só pode ser criada no iOS, podem ser enviado para iOS e macOS a ser exibido.

Se o usuário clicar no balão de mensagem na conversa em macOS, o Mac tentará abrir o endereço especificado na URL no navegador da web. Como resultado, o site do desenvolvedor deve ser capaz de exibir algumas representação da mensagem no navegador da web em macOS com base em máquinas.

O `AccessibilityLabel` por leitores de tela, a propriedade é usada para ler o texto da conversa para o usuário. O `Layout` propriedade especifica como a mensagem será exibida, atualmente apenas o `MSMessageTemplateLayout` é suportado e é semelhante ao seguinte:

[![](advanced-message-app-extensions-images/interactive06.png "O modelo de MSMessageTemplateLayout")](advanced-message-app-extensions-images/interactive06.png#lightbox)

O `Image` propriedade o `MSMessageTemplateLayout` fornece o conteúdo do corpo principal do MessageBubble na tela. O `MediaFileUrl` propriedade também fornece o conteúdo do corpo da mensagem de bolha, mas permite que o conteúdo que não é suportado pelo `UIImage` (como um arquivo de vídeo que seria um loop em segundo plano). Se o `Image` e `MediaFileUrl` propriedades são fornecidas, o `Image` propriedade terá precedência. O `MediaFileUrl` suporta PNG, JPEG, GIF de vídeo (em qualquer formato que possa ser reproduzido pela estrutura de Player de mídia) formatos de mídia.

O tamanho da mídia recomendado é 300 x 300 pixels na resolução 3 x. Ativos ligeiramente maiores e menores também são aceitos e Apple sugere alguns tamanhos diferentes para obter os melhores resultados de teste. O aplicativo de mensagem será suspenso exemplo e dimensionar esta mídia conforme necessário.

Quando os ativos são enviados para o receptor, qualquer mídia anexada será automaticamente transcodificado pelo aplicativo mensagens para otimizá-los de transferência de redes. Por isso, Apple desencoraja incluindo texto na mídia que é anexado à mensagem porque a mídia será reduzida e compactadas para transmissão, possivelmente deixando o texto ilegível.

O `ImageTitle` e `ImageSubtitle` propriedades fornecem uma descrição para a mídia que é apresentada na mensagem bolha. Essas propriedades serão enviadas como texto para o dispositivo de recebimento em que eles serão nitidez renderizados no canto inferior esquerdo da imagem.

O `Caption`, `SubCaption`, `TrailingCaption` e `TrailingSubcaption` propriedades descrevem a imagem ainda mais e serão renderizadas em uma seção abaixo da imagem. Definir todas essas propriedades para `null` criará uma bolha de mensagem sem a área de legenda:

[![](advanced-message-app-extensions-images/interactive07.png "Uma bolha de mensagem sem a área de legenda")](advanced-message-app-extensions-images/interactive07.png#lightbox)

A última coisa a observar é que o aplicativo de mensagens desenhará o ícone da extensão do aplicativo a mensagem no canto superior esquerdo da bolha de mensagem.

## <a name="sending-a-message"></a>Enviar uma mensagem

Uma vez um `MSMessage` foi composto, o código a seguir pode ser usado para enviar a ele:

```csharp
public void SendMessage (MSMessage message)
{
    // Insert the message into the conversation
    ActiveConversation.InsertMessage (message, (error) => {
        // Did the message send successfully?
        if (error == null) {
            // Handle successful send
        } else {
            // Report Error
            Console.WriteLine ("Error: {0}", error);
        }
    };

}
```

O `ActiveConversation` propriedade o `MSMessagesAppViewController` armazenará a conversa atual que a extensão do aplicativo de mensagens foi iniciada no.

Chamar o `InsertMessage` do `MSConversation` para incluir a mensagem na conversa e tratar os erros que possam surgir. Se a mensagem está incluída com êxito, a bolha de mensagem será exibida no campo de entrada.

Além disso, a extensão poderá enviar diferentes tipos de dados para a conversa como:

- **texto** - `ActiveConversation.InsertText ("Message", (error) => {...});`
- **Anexos** - `ActiveConversation.InsertAttachment (new NSUrl ("path"), "filename", (error) => {...});`
- **Adesivo**  -  `ActiveConversation.InsertSticker (sticker, (obj) => {...});` onde `sticker` é um `MSSticker`.

Depois que o novo conteúdo estiver no campo de entrada, o usuário é capaz de enviar a mensagem ao tocar em azul **enviar** botão (assim como qualquer mensagem típica). Não é possível para a extensão do aplicativo de mensagem enviar automaticamente o conteúdo, esse processo está totalmente sob o controle do usuário.

## <a name="handling-the-compact-and-expanded-modes"></a>Tratando os modos Compact e expandidos

Extensão do aplicativo uma mensagem pode ser exibida em um dos dois modos de exibição diferentes:

[![](advanced-message-app-extensions-images/interactive08.png "Uma extensão de aplicativo de mensagem exibida em dois modos de exibição diferentes: Compact & expandido")](advanced-message-app-extensions-images/interactive08.png#lightbox)

- **Compact** -este é o modo padrão onde a mensagem da extensão do aplicativo se ocupa a parte inferior 25% da exibição de mensagem. No modo compacto, o aplicativo não tem acesso ao teclado, rolagem horizontal ou passe o dedo para reconhecedores de gestos. O aplicativo tem acesso ao campo de entrada e chamadas para `InsertMessage` instantaneamente será exibida para o usuário existe.
- **Expandido** -a extensão do aplicativo mensagem preenche a exibição de mensagem inteira. Ele não tem acesso ao campo de entrada, mas tem acesso ao teclado, rolagem horizontal e passe o dedo para reconhecedores de gestos.

Extensão do aplicativo uma mensagem pode ser alternada entre esses modos programaticamente ou manualmente pelo usuário a qualquer momento e deve ser instantaneamente responder a qualquer modo de exibição Altera modo.

Veja o exemplo a seguir de lidar com a alternância entre os dois modos de exibição diferente. Dois controladores de exibição diferentes serão necessários para cada estado. O `StickerBrowserViewController` identificadores de **Compact** exibição e o `AddStickerViewController` tratará o **expandido** exibição:

```csharp
using System;

using Messages;
using Foundation;
using UIKit;

namespace MessagesExtension {
    public partial class MessagesViewController : MSMessagesAppViewController, IIceCreamsViewControllerDelegate, IBuildIceCreamViewControllerDelegate {
        public MessagesViewController (IntPtr handle) : base (handle)
        {
        }

        public override void WillBecomeActive (MSConversation conversation)
        {
            base.WillBecomeActive (conversation);

            // Present the view controller appropriate for the conversation and presentation style.
            PresentViewController (conversation, PresentationStyle);
        }

        public override void WillTransition (MSMessagesAppPresentationStyle presentationStyle)
        {
            var conversation = ActiveConversation;
            if (conversation == null)
                throw new Exception ("Expected an active converstation");

            // Present the view controller appropriate for the conversation and presentation style.
            PresentViewController (conversation, presentationStyle);
        }

        void PresentViewController (MSConversation conversation, MSMessagesAppPresentationStyle presentationStyle)
        {
            // Determine the controller to present.
            UIViewController controller;

            if (presentationStyle == MSMessagesAppPresentationStyle.Compact) {
                // Show a list of previously created ice creams.
                controller = InstantiateIceCreamsController ();
            } else {
                var iceCream = new IceCream (conversation.SelectedMessage);
                controller = iceCream.IsComplete ? InstantiateCompletedIceCreamController (iceCream) : InstantiateBuildIceCreamController (iceCream);
            }

            foreach (var child in ChildViewControllers) {
                child.WillMoveToParentViewController (null);
                child.View.RemoveFromSuperview ();
                child.RemoveFromParentViewController ();
            }

            AddChildViewController (controller);
            controller.View.Frame = View.Bounds;
            controller.View.TranslatesAutoresizingMaskIntoConstraints = false;
            View.AddSubview (controller.View);

            controller.View.LeftAnchor.ConstraintEqualTo (View.LeftAnchor).Active = true;
            controller.View.RightAnchor.ConstraintEqualTo (View.RightAnchor).Active = true;
            controller.View.TopAnchor.ConstraintEqualTo (View.TopAnchor).Active = true;
            controller.View.BottomAnchor.ConstraintEqualTo (View.BottomAnchor).Active = true;

            controller.DidMoveToParentViewController (this);
        }

        UIViewController InstantiateIceCreamsController ()
        {
            // Instantiate a `IceCreamsViewController` and present it.
            var controller = Storyboard.InstantiateViewController (IceCreamsViewController.StoryboardIdentifier) as IceCreamsViewController;
            if (controller == null)
                throw new Exception ("Unable to instantiate an IceCreamsViewController from the storyboard");

            controller.Builder = this;
            return controller;
        }

        UIViewController InstantiateBuildIceCreamController (IceCream iceCream)
        {
            // Instantiate a `BuildIceCreamViewController` and present it.
            var controller = Storyboard.InstantiateViewController (BuildIceCreamViewController.StoryboardIdentifier) as BuildIceCreamViewController;
            if (controller == null)
                throw new Exception ("Unable to instantiate an BuildIceCreamViewController from the storyboard");

            controller.IceCream = iceCream;
            controller.Builder = this;

            return controller;
        }

        public UIViewController InstantiateCompletedIceCreamController (IceCream iceCream)
        {
            // Instantiate a `BuildIceCreamViewController` and present it.
            var controller = Storyboard.InstantiateViewController (CompletedIceCreamViewController.StoryboardIdentifier) as CompletedIceCreamViewController;
            if (controller == null)
                throw new Exception ("Unable to instantiate an CompletedIceCreamViewController from the storyboard");

            controller.IceCream = iceCream;
            return controller;
        }

        public void DidSelectAdd (IceCreamsViewController controller)
        {
            Request (MSMessagesAppPresentationStyle.Expanded);
        }

        public void Build (BuildIceCreamViewController controller, IceCreamPart iceCreamPart)
        {
            var conversation = ActiveConversation;
            if (conversation == null)
                throw new Exception ("Expected a conversation");

            var iceCream = controller.IceCream;
            if (iceCream == null)
                throw new Exception ("Expected the controller to be displaying an ice cream");

            string messageCaption = string.Empty;
            var b = iceCreamPart as Base;
            var s = iceCreamPart as Scoops;
            var t = iceCreamPart as Topping;

            if (b != null) {
                iceCream.Base = b;
                messageCaption = "Let's build an ice cream";
            } else if (s != null) {
                iceCream.Scoops = s;
                messageCaption = "I added some scoops";
            } else if (t != null) {
                iceCream.Topping = t;
                messageCaption = "Our finished ice cream";
            } else {
                throw new Exception ("Unexpected type of ice cream part selected.");
            }

            // Create a new message with the same session as any currently selected message.
            var message = ComposeMessage (iceCream, messageCaption, conversation.SelectedMessage?.Session);

            // Add the message to the conversation.
            conversation.InsertMessage (message, null);

            // If the ice cream is complete, save it in the history.
            if (iceCream.IsComplete) {
                var history = IceCreamHistory.Load ();
                history.Append (iceCream);
                history.Save ();
            }

            Dismiss ();
        }

        MSMessage ComposeMessage (IceCream iceCream, string caption, MSSession session = null)
        {
            var components = new NSUrlComponents {
                QueryItems = iceCream.QueryItems
            };

            var layout = new MSMessageTemplateLayout {
                Image = iceCream.RenderSticker (true),
                Caption = caption
            };

            var message = new MSMessage (session ?? new MSSession()) {
                Url = components.Url,
                Layout = layout
            };

            return message;
        }
    }
}
```

O `DidTransition` método é substituído para manipular a alternância entre os dois modos:

```csharp
public override void DidTransition (MSMessagesAppPresentationStyle presentationStyle)
{
    base.DidTransition (presentationStyle);

    // Take action based on style
    switch (presentationStyle) {
    case MSMessagesAppPresentationStyle.Compact:
        PresentStickerBrowser ();
        break;
    case MSMessagesAppPresentationStyle.Expanded:
        PresentAddSticker ();
        break;
    }
}
```

Opcionalmente, o aplicativo poderia ter usado o `WillTransition` método para lidar com a alteração do modo de exibição antes que ela é apresentada ao usuário (como é feito no exemplo Icecream construtor acima). Para obter mais informações, consulte nosso [outra personalização de etiqueta](~/ios/platform/message-app-integration/intro-to-message-app-extensions.md) documentação.

## <a name="replying-to-a-message"></a>Responder a uma mensagem

Há dois casos que uma mensagem da extensão do aplicativo precisa lidar ao responder a uma mensagem:

[![](advanced-message-app-extensions-images/interactive09.png "A extensão do aplicativo de mensagem nos modos ativo e inativo")](advanced-message-app-extensions-images/interactive09.png#lightbox)

- **Extensão está inativo** -há um bolhas de mensagem da extensão do aplicativo a mensagem na transcrição a mensagem de que o usuário pode tocar para ativar as extensões e continuar a conversa interativa.
- **Extensão está ativo** -o usuário pode tocar mensagem bolha a extensão aplicativo de mensagem na transcrição de mensagem para entrar no modo de exibição expandido e continuar o processo interativo de onde parou.

### <a name="the-extension-is-inactive"></a>A extensão está inativo

Quando uma bolha de mensagem é tocada pelo usuário na transcrição de mensagem e a mensagem da extensão do aplicativo está inativa, acontecerá o seguinte processo:

[![](advanced-message-app-extensions-images/interactive10.png "Tratando uma bolha de mensagem inativa")](advanced-message-app-extensions-images/interactive10.png#lightbox)

1. O usuário toca bolhas de mensagem da extensão.
2. Quando uma extensão é iniciada, o aplicativo de mensagem iniciará um processo.
3. O `DidBecomeActive` método é chamado e passado um `MSConversation` que representa a conversa da qual a mensagem da extensão do aplicativo está em execução no.
4. Como a extensão baseia fora de um `UIViewController` ambos `ViewWillAppear` e `ViewDidAppear` são chamados.

Quando o processo for concluído, verá a mensagem da extensão do aplicativo no modo de exibição expandido.

### <a name="the-extension-is-active"></a>A extensão está ativo

Quando uma bolha de mensagem é tocada pelo usuário na transcrição de mensagem e a extensão do aplicativo de mensagens está ativa, ocorrerá o seguinte processo:

[![](advanced-message-app-extensions-images/interactive11.png "Tratando uma bolha de mensagem ativa")](advanced-message-app-extensions-images/interactive11.png#lightbox)

1. O usuário toca bolhas de mensagem da extensão.
2. Porque a mensagem da extensão do aplicativo já está ativa, o `WillTransition` método o `MSMessagesAppViewController` é chamado para lidar com a troca do CD para o modo de exibição expandido.
3. O `DidSelectMessage` método o `MSMessagesAppViewController` é chamado e passado a `MSMessage` e `MSConversation` que pertence a bolha de mensagem.
4. O `DidTransition` método o `MSMessagesAppViewController` é chamado para lidar com a troca do CD para o modo de exibição expandido.

Novamente, quando o processo for concluído, a extensão do aplicativo de mensagens será apresentada no modo de exibição expandido.

### <a name="accessing-the-selected-message"></a>Acessando a mensagem selecionada

Em ambos os casos, quando o usuário toca uma bolha de mensagem que pertencem a extensão do aplicativo a mensagem, ela precisará acessar o `MSMessage` que foi tocado usando o `SelectedMessage` propriedade o `MSConversation`.

Por exemplo:

```csharp
using System;
using Foundation;
using Messages;
using UIKit;


namespace MessageExtension
{
    public partial class MessagesViewController : MSMessagesAppViewController
    {
        ...

        #region Override Methods
        public override void DidSelectMessage (MSMessage message, MSConversation conversation)
        {
            base.DidSelectMessage (message, conversation);

            // Get selected message
            var selected = conversation.SelectedMessage;

            // Present the user interface to continue editing
            // the conversation
            ...
        }
        #endregion
    }
}
```

A mensagem selecionada deve ser mostrada na interface de usuário da extensão do aplicativo a mensagem e o usuário deve ter permissão para compor uma resposta.

## <a name="removing-partially-completed-messages"></a>Removendo parcialmente concluída mensagens

No processo de envio diferentes etapas de uma conversa interativa entre o usuário dois na conversa, as bolhas de mensagem parcialmente concluída pode começar a sobrecarregar a transcrição de mensagem:

[![](advanced-message-app-extensions-images/interactive12.png "As bolhas de mensagem parcialmente concluída pode encher a transcrição de mensagem")](advanced-message-app-extensions-images/interactive12.png#lightbox)

Em vez disso, a mensagem da extensão do aplicativo deve recolher as bolhas de mensagem anterior em um comentário sucinto na transcrição a mensagem:

[![](advanced-message-app-extensions-images/interactive13.png "Recolhendo as bolhas de mensagem anterior na transcrição de mensagem")](advanced-message-app-extensions-images/interactive13.png#lightbox)

Isso é tratado usando um `MSSession` para recolher todas as etapas existentes. Portanto, o `DidSelectMessage` método do `MSMessagesAppViewController` classe pode ser modificada para a seguinte aparência:

```csharp
public override void DidSelectMessage (MSMessage message, MSConversation conversation)
{
    base.DidSelectMessage (message, conversation);

    MSSession session;
    var summary = "";

    // Get selected message
    var selected = conversation.SelectedMessage;

    // Does the selected message already have a session?
    if (selected.Session == null) {
        // No, create a new session
        session = new MSSession ();
        summary = "Let's build an ice cream";
    } else {
        // Yes, use the existing session
        session = selected.Session;
        summary = "I added some scoops";
    }

    // Create an instance of the message being composed
    var existingMessage = new MSMessage (session);
    message.SummaryText = summary;
    ...

    // Present the user interface to continue editing
    // the conversation
    ...
}
```

Se a mensagem selecionada já tem uma saída `MSSession`, ele é usado ou um novo `MSSession` é criado. O `SummaryText` propriedade o `MSMessage` é usado para adicionar uma legenda para as etapas anteriores recolhidas. Se o `SummaryText` está definida como `null`, as etapas anteriores na conversa serão completamente removidas da transcrição de conversa.

## <a name="advanced-message-api-features"></a>Recursos da API de mensagem avançados

Com os recursos básicos da API de mensagem nova abordadas detalhadamente acima, em seguida examine alguns dos recursos mais avançados que Apple criada no framework.

Há vários outros métodos de substituição no primeiro, o `MSMessagesAppViewController` classe que fornecem mais acesso a conversa:

- `DidStartSendingMessage` -Isso é chamado quando o usuário toca no botão Enviar. Isso não significa que a mensagem foi realmente entrega para o destinatário, apenas se o processo de envio foi iniciado.
- `DidCancelSendingMessage` -Isso acontece quando o usuário toca o *X* botão no canto superior direito da bolha mensagem na conversa transcrição.
- `DidReceiveMessage` -Este método é chamado quando a mensagem da extensão do aplicativo estiver ativa uma uma nova mensagem foi recebida de um dos participantes da conversa.

### <a name="group-conversations"></a>Grupo de conversas

Extensão do aplicativo uma mensagem pode ser usada enquanto os usuários estão envolvidos no grupo de conversas (com pessoas de 3 ou mais) e isso precisa ser levado em consideração ao projetar e implementar uma extensão de aplicativo de mensagem.

Observe a seguinte interação em uma conversa de grupo com três usuários:

[![](advanced-message-app-extensions-images/interactive14.png "Interação em uma conversa de grupo com três usuários")](advanced-message-app-extensions-images/interactive14.png#lightbox)

1. O usuário 1 envia um grupo interativo mensagem solicitando que o usuário 2 e 3 do usuário para escolher um sabor burger.
2. O usuário 2 escolhe tomatoes.
3. O usuário 3 escolhe Conservas.
4. As opções do usuário 2 e 3 usuário chegarem volta para o usuário 1 quase simultaneamente. Como resultado, a escolha do usuário 2 será recolhida em uma linha de resumo e não está disponível. Neste caso, pode ter também foi invertido, com a opção do usuário 2 sendo mostrado e 3 do usuário do que está sendo recolhido.

Em ambos os casos, esse comportamento é indesejado como usuário 1 deve ser capaz de acessar as opções de usuário 2 e 3 de usuário. Para lidar com essa situação, Apple está sugerindo que a extensão do aplicativo de mensagens armazena o estado da mensagem na nuvem e usar a propriedade URL do `MSMessage` (que é enviado entre os usuários) para acessar esse estado.

Quando o usuário envia uma mensagem, um token de sessão é gerado e enviado para a nuvem com o estado atual da mensagem. Quando um usuário toca em uma bolha de mensagem na transcrição a conversa, o token de sessão é usado para recuperar o estado da sessão atual da nuvem.

### <a name="sender-identifiers"></a>Identificadores do remetente

Para discutir acessando o identificador do remetente de uma mensagem, veja o exemplo de uma conversa em grupo acima:

[![](advanced-message-app-extensions-images/interactive15.png "Envio de identificadores de conversa em grupo")](advanced-message-app-extensions-images/interactive15.png#lightbox)

1. Novamente, o usuário 1 envia um grupo interativo mensagem solicitando que o usuário 2 e 3 do usuário para escolher um sabor burger.
2. O usuário 3 escolhe Conservas.
3. Escolha do usuário do 3 chega volta para o usuário 1 e 2 do usuário ainda não respondeu.
4. Como Apple é muito preocupado com a privacidade do usuário, a extensão do aplicativo de mensagem só conhece um identificador exclusivo (como um `NSUUID`) que é atribuído a cada participante na conversa. No dispositivo local, apenas o identificador do usuário atual é conhecido.
5. O `MSMessage` tem um `SenderIdentifier` propriedade que corresponda a um usuário na lista de participante conhecida para a extensão.
6. Cada dispositivo de usuário tem sua própria cópia da lista de participante onde novamente, apenas o identificador do usuário local é conhecido.
7. Quando uma mensagem é enviada, seu `SenderIdentifier` propriedade também é conhecida para que seja o usuário local.

Os identificadores de remetente pode ser usados das seguintes maneiras:

- Observando a lista de participante a extensão pode obter o número de usuários na conversa.
- Quando a extensão recebe uma mensagem de um usuário, ele pode manter controle do identificador do remetente. Se receber outra mensagem com o mesmo identificador de remetente, a extensão sabe que é do mesmo usuário.
- Eles podem ser usados para ajudar a identificar um usuário específico na conversa.

O identificador do remetente pode ser usado em qualquer um dos campos de texto de `MSMessageTemplateLayout` , prefixando-o com um sinal de cifrão (`$`). Por exemplo:

```csharp
// Pass along the sender identifier
var layout = new MSMessageTemplateLayout()
layout.Caption = string.format("${0} wants pickles.",Conversation.LocalParticipantIdentifier.UuidString);
```

Quando o aplicativo de mensagens exibe uma bolha de mensagem com esse tipo de formatação, ele substituirá o `$uuid...` com o nome de contato do participante na conversa que enviou a mensagem.

O identificador do remetente é exclusivo em cada dispositivo, para que analisar o diagrama acima, observe dispositivo do usuário 1 e do usuário 3 tem um identificador de remetente exclusivo diferente para cada participante na conversa.

Os identificadores de remetente têm o escopo para a instalação da extensão do aplicativo a mensagem. Então se um usuário desinstala e reinstala a extensão do aplicativo de mensagem novos identificadores de remetente serão gerados para a nova instalação.

Para acessar os identificadores de remetente, a extensão pode usar o código a seguir:

```csharp
public override void DidStartSendingMessage (MSMessage message, MSConversation conversation)
{
    base.DidStartSendingMessage (message, conversation);

    // Get the sender's participant ID
    var senderID = message.SenderParticipantIdentifier;

    // Get the local participant ID
    var localID = conversation.LocalParticipantIdentifier;

    // Get the remote participant IDs
    var remoteIDs = conversation.RemoteParticipantIdentifiers;
}
```

## <a name="supported-platforms"></a>Plataformas compatíveis

As mensagens interativo gerado pela extensão de aplicativo mensagem será entregue nas seguintes plataformas Apple:

- watchOS 3
- macOS Sierra
- iOS 10

As três plataformas, iOS 10 permitirá que o usuário gerar uma mensagem interativa. Em macOS serra, se o usuário clica em uma bolha de mensagem interativa, a URL anexados para o `MSMessage` será aberto no Safari e uma representação da mensagem deve ser exibida lá.

Em watchOS, o aplicativo de mensagens pode entregar uma mensagem interativa para um dispositivo iOS anexado onde o usuário pode compor uma resposta.

A nova API de mensagens tem suporte para fallback se a mensagem interativa é recebida em plataformas mais antigas do Apple:

- watchOS 2 +
- OS X 10.11 +
- iOS 9 +

Elas serão enviadas em um formato de fallback como duas mensagens separadas:

- Um será a imagem, conforme fornecido pelo modelo de Layout.
- O outro será a URL como fornecido a `MSMessage`.

## <a name="summary"></a>Resumo

Este artigo apresentou técnicas avançadas para trabalhar com extensões de aplicativo de mensagem em uma solução de xamarin que integra o **mensagens** funcionalidade presente para o usuário e o aplicativo.


## <a name="related-links"></a>Links relacionados

- [Construtor de sorvete (exemplo)](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/)
- [Referência de mensagens](https://developer.apple.com/reference/messages)
- [Guia de programação de extensão do aplicativo](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)
