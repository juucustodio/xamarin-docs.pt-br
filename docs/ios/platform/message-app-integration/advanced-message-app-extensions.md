---
title: Extensões avançadas do aplicativo de mensagens no Xamarin. iOS
description: Este artigo mostra técnicas avançadas para trabalhar com extensões de aplicativo de mensagens em uma solução Xamarin. iOS que se integra ao aplicativo de mensagens e apresenta novas funcionalidades para o usuário.
ms.prod: xamarin
ms.assetid: 394A1FDA-AF70-4493-9B2C-4CFE4BE791B6
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: fb721e36a6b66b90e9660a1c7d5db9e5124e8715
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031729"
---
# <a name="advanced-message-app-extensions-in-xamarinios"></a>Extensões avançadas do aplicativo de mensagens no Xamarin. iOS

_Este artigo mostra técnicas avançadas para trabalhar com extensões de aplicativo de mensagens em uma solução Xamarin. iOS que se integra ao aplicativo de mensagens e apresenta novas funcionalidades para o usuário._

Novo no iOS 10, uma extensão de aplicativo de mensagem se integra ao aplicativo de **mensagens** e apresenta uma nova funcionalidade ao usuário. A extensão pode enviar texto, adesivos, arquivos de mídia e mensagens interativas.

## <a name="about-message-app-extensions"></a>Sobre extensões de aplicativo de mensagens

Como mencionado acima, uma extensão de aplicativo de mensagem se integra ao aplicativo de **mensagens** e apresenta uma nova funcionalidade ao usuário. A extensão pode enviar texto, adesivos, arquivos de mídia e mensagens interativas. Dois tipos de extensão de aplicativo de mensagem estão disponíveis:

- **Pacotes de adesivo** -contém uma coleção de adesivos que o usuário pode adicionar a uma mensagem. Os pacotes adesivos podem ser criados sem escrever nenhum código.
- **aplicativo IMessage** – pode apresentar uma interface do usuário personalizada no aplicativo mensagens para selecionar adesivos, inserir texto, incluir arquivos de mídia (com conversões de tipo opcionais) e criar, editar e enviar mensagens de interação.

As extensões de aplicativos de mensagens fornecem três tipos de conteúdo principais:

- **Mensagens interativas** – são um tipo de conteúdo de mensagem personalizada que um aplicativo gera, quando o usuário toca na mensagem, o aplicativo será iniciado em primeiro plano.
- **Adesivos** -são imagens geradas pelo aplicativo que podem ser incluídas nas mensagens enviadas entre os usuários. Veja nosso aplicativo de exemplo de [Ice de gelo](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-icecreambuilder) para obter um exemplo de implementação de um aplicativo de pacote de adesivo.
- **Outro conteúdo com suporte** -o aplicativo pode fornecer conteúdo como fotos, vídeos, texto ou links do tipo que sempre tenha sido suportado pelo aplicativo de mensagens.

Novo no iOS 10, o aplicativo de mensagens agora inclui sua própria loja de aplicativos interna dedicada. Todos os aplicativos que incluem extensões de aplicativos de mensagens serão exibidos e promovidos nesse armazenamento. A gaveta de aplicativos de novas mensagens exibirá todos os aplicativos que foram baixados da loja de aplicativos de mensagens para fornecer acesso rápido aos usuários.

Também novidade no iOS 10, a Apple adicionou a atribuição de aplicativo embutido que permite ao usuário descobrir facilmente um aplicativo. Por exemplo, se um usuário enviar conteúdo para outro de um aplicativo que o segundo usuário não tenha instalado (como um adesivo, por exemplo), o nome do aplicativo de envio será listado sob o conteúdo no histórico de mensagens. Se o usuário tocar no nome do aplicativo, a mensagem App Store será aberta e o aplicativo selecionado na loja.

As extensões de aplicativos de mensagens são semelhantes aos aplicativos iOS existentes que o desenvolvedor está familiarizado com a criação e terão acesso a todas as estruturas e recursos padrão de um aplicativo iOS padrão. Por exemplo:

- Eles têm acesso à compra no aplicativo.
- Eles têm acesso ao Apple Pay.
- Eles têm acesso ao hardware do dispositivo, como a câmera.

Extensões de aplicativos de mensagens só têm suporte no iOS 10, no entanto, o conteúdo que essas extensões envia são visíveis em dispositivos watchOS e macOS. A nova _página recentes_ adicionada ao watchOS 3 exibirá adesivos recentes que foram enviados do telefone, incluindo aqueles de extensões de aplicativos de mensagem, e permitem que o usuário envie esses adesivos da inspeção.

## <a name="about-interactive-messages"></a>Sobre mensagens interativas

As mensagens interativas apresentam uma bolha de mensagem personalizada e são fornecidas por uma extensão de aplicativo de mensagem. Eles permitem que o usuário crie conteúdo de mensagem interativa, insira-o no campo de entrada da mensagem e envie-o.

[![](advanced-message-app-extensions-images/interactive01.png "Creating Interactive Message Content")](advanced-message-app-extensions-images/interactive01.png#lightbox)

O usuário receptor pode responder a uma mensagem interativa tocando em sua bolha de mensagem no histórico de mensagens para carregar a extensão do aplicativo de mensagens que a criou. A extensão será iniciada em tela inteira e permitirá que o usuário redija uma resposta e envie-a de volta para o usuário de origem.

[![](advanced-message-app-extensions-images/interactive02.png "The Extension launched full-screen")](advanced-message-app-extensions-images/interactive02.png#lightbox)

Os tópicos a seguir serão abordados em detalhes abaixo:

- Visão geral da API de mensagens
- O ciclo de vida da extensão
- Redigindo uma mensagem
- Enviando uma mensagem

## <a name="messages-api-overview"></a>Visão geral da API de mensagens

Quando invocado pelo usuário, uma extensão do aplicativo de mensagem será exibida na parte inferior do histórico de mensagens no modo de exibição compacta:

[![](advanced-message-app-extensions-images/interactive03.png "Messages API Overview")](advanced-message-app-extensions-images/interactive03.png#lightbox)

1. O objeto `MSMessageAppViewController` na extensão do aplicativo de mensagem é a classe principal que é chamada quando a exibição da extensão é exibida para o usuário.
2. A conversa é apresentada ao usuário como uma instância de objeto `MSConversation`.
3. A classe `MSMessage` representa uma determinada bolha de mensagem na conversa.
4. `MSSession` controla como uma mensagem é enviada.
5. `MSMessageTemplateLayout` controla como a mensagem é exibida

## <a name="the-extension-lifecycle"></a>O ciclo de vida da extensão

Dê uma olhada no processo de uma extensão de aplicativo de mensagem ficando ativa:

[![](advanced-message-app-extensions-images/interactive04.png "The process of a Message App Extension becoming active")](advanced-message-app-extensions-images/interactive04.png#lightbox)

1. Quando uma extensão é iniciada (por exemplo, da gaveta do aplicativo), o aplicativo de mensagem inicia um processo.
2. O método `DidBecomeActive` é chamado e passado um `MSConversation` que representa a conversa em que a extensão do aplicativo de mensagem está sendo executada.
3. Como a extensão é baseada em um `UIViewController` `ViewWillAppear` e `ViewDidAppear` são chamados.

Em seguida, dê uma olhada no processo de uma extensão de aplicativo de mensagem ficando desativada:

[![](advanced-message-app-extensions-images/interactive05.png "The process of a Message App Extension becoming deactivated")](advanced-message-app-extensions-images/interactive05.png#lightbox)

1. Quando a extensão do aplicativo de mensagens estiver sendo desativada, o método `ViewWillDisappear` será chamado primeiro.
2. Em seguida, o método `ViewDidDisappear` será chamado.
3. O método `WillResignActive` é chamado e passado um `MSConversation` que representa a conversa em que a extensão do aplicativo de mensagem está sendo executada. Neste ponto, a conexão entre o aplicativo mensagens e a extensão está prestes a ser lançada.
4. Em algum momento posterior, o processo é encerrado pelo aplicativo mensagens.

Como uma extensão é um processo de curta duração, ela é encerrada agressivamente pelo sistema para conservar o processamento e a energia da bateria. O desenvolvedor deve ter isso em mente ao projetar e implementar uma extensão de aplicativo de mensagens.

## <a name="composing-a-message"></a>Redigindo uma mensagem

Depois que a extensão do aplicativo de mensagem estiver em execução em um processo e tiver exibido sua interface do usuário, o código a seguir poderá ser usado para compor uma nova mensagem:

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

Esse código cria uma nova `MSMessage` e define várias propriedades (como `Url`). Embora a mensagem só possa ser criada no iOS, ela pode ser enviada para o iOS e o macOS para serem exibidas.

Se o usuário clicar na bolha da mensagem na conversa no macOS, o Mac tentará abrir o endereço especificado na URL no navegador da Web. Como resultado, o site do desenvolvedor deve ser capaz de mostrar alguma representação da mensagem no navegador da Web em computadores baseados no macOS.

A propriedade `AccessibilityLabel` é usada pelos leitores de tela para ler a transcrição da conversa para o usuário. A propriedade `Layout` especifica como a mensagem será exibida, no momento, somente o `MSMessageTemplateLayout` tem suporte e é semelhante ao seguinte:

[![](advanced-message-app-extensions-images/interactive06.png "The MSMessageTemplateLayout template")](advanced-message-app-extensions-images/interactive06.png#lightbox)

A propriedade `Image` da `MSMessageTemplateLayout` fornece conteúdo para o corpo principal do MessageBubble na tela. A propriedade `MediaFileUrl` também fornece conteúdo para o corpo da bolha da mensagem, mas permite o conteúdo que não tem suporte do `UIImage` (como um arquivo de vídeo que poderia fazer um loop em segundo plano). Se as propriedades `Image` e `MediaFileUrl` forem fornecidas, a propriedade `Image` terá precedência. O `MediaFileUrl` dá suporte ao PNG, JPEG, GIF e vídeo (em qualquer formato que possa ser reproduzido pelos formatos de mídia do Media Player Framework).

O tamanho de mídia recomendado é de 300 x 300 pixels na resolução de 3x. Ativos ligeiramente maiores e menores também são aceitos e a Apple sugere testes com alguns tamanhos diferentes para obter os melhores resultados. O aplicativo de mensagens ficará inativo – exemplo e dimensione esta mídia conforme necessário.

Quando os ativos forem enviados ao receptor, qualquer mídia anexada será transcodificada automaticamente pelo aplicativo de mensagens para otimizá-los da transferência pelas redes. Por isso, a Apple não incentiva a inclusão de texto na mídia anexada à mensagem, pois a mídia será reduzida e compactada para transmissão, o que potencialmente renderiza o texto ilegível.

As propriedades `ImageTitle` e `ImageSubtitle` fornecem uma descrição para a mídia apresentada na bolha da mensagem. Essas propriedades serão enviadas como texto para o dispositivo receptor onde serão processadas com nitidez no canto inferior esquerdo da imagem.

As propriedades `Caption`, `SubCaption`, `TrailingCaption` e `TrailingSubcaption` descrevem ainda mais a imagem e serão renderizadas em uma seção abaixo da imagem. Definir todas essas propriedades como `null` criará uma bolha de mensagem sem a área de legenda:

[![](advanced-message-app-extensions-images/interactive07.png "A Message Bubble without the Caption Area")](advanced-message-app-extensions-images/interactive07.png#lightbox)

A última coisa a observar é que o aplicativo messages desenhará o ícone da extensão do aplicativo de mensagem no canto superior esquerdo da bolha da mensagem.

## <a name="sending-a-message"></a>Enviando uma mensagem

Depois que um `MSMessage` foi composto, o código a seguir pode ser usado para enviá-lo:

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

A propriedade `ActiveConversation` da `MSMessagesAppViewController` conterá a conversa atual na qual a extensão do aplicativo de mensagem foi iniciada.

Chame o `InsertMessage` da `MSConversation` para incluir a mensagem na conversa e manipular quaisquer erros que possam surgir. Se a mensagem for incluída com êxito, a bolha da mensagem será exibida no campo de entrada.

Além disso, a extensão pode enviar diferentes tipos de dados para a conversa, como:

- `ActiveConversation.InsertText ("Message", (error) => {...});` de - de **texto**
- **Anexos** - `ActiveConversation.InsertAttachment (new NSUrl ("path"), "filename", (error) => {...});`
- Os **adesivos** - `ActiveConversation.InsertSticker (sticker, (obj) => {...});` em que `sticker` é um `MSSticker`.

Depois que o novo conteúdo estiver no campo de entrada, o usuário poderá enviar a mensagem tocando no botão azul **Send** (da mesma forma que faria com qualquer mensagem típica). Não há como a extensão do aplicativo de mensagem enviar conteúdo automaticamente, esse processo está totalmente sob o controle do usuário.

## <a name="handling-the-compact-and-expanded-modes"></a>Manipulando os modos compacto e expandido

Uma extensão de aplicativo de mensagem pode ser exibida em um dos dois modos de exibição diferentes:

[![](advanced-message-app-extensions-images/interactive08.png "A Message App Extension displayed in two different view modes: Compact & Expanded")](advanced-message-app-extensions-images/interactive08.png#lightbox)

- **Compact** -esse é o modo padrão em que a extensão do aplicativo de mensagem ocupa os 25% inferiores da exibição de mensagem. No modo compacto, o aplicativo não tem acesso ao teclado, à rolagem horizontal ou aos reconhecedores de gestos de toque. O aplicativo tem acesso ao campo de entrada e as chamadas para `InsertMessage` serão exibidas instantaneamente para o usuário.
- **Expanded** – a extensão do aplicativo de mensagem preenche a exibição de mensagem inteira. Ele não tem acesso ao campo de entrada, mas tem acesso ao teclado, à rolagem horizontal e aos reconhecedores de gestos de toque.

Uma extensão de aplicativo de mensagem pode ser alternada entre esses modos de forma programática ou manual pelo usuário a qualquer momento e deve ser imediatamente responsiva a quaisquer alterações no modo de exibição.

Veja o exemplo a seguir de como tratar a opção entre os dois modos de exibição diferentes. Dois controladores de exibição diferentes serão necessários para cada Estado. O `StickerBrowserViewController` lida com a exibição **compacta** e a `AddStickerViewController` manipulará a exibição **expandida** :

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

O método `DidTransition` é substituído para manipular a alternância entre os dois modos:

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

Opcionalmente, o aplicativo pode ter usado o método `WillTransition` para manipular a alteração do modo de exibição antes de ser apresentado ao usuário (como é feito no exemplo do sorvete Builder acima). Para obter mais informações, consulte nossa documentação de [personalização mais adesiva](~/ios/platform/message-app-integration/intro-to-message-app-extensions.md) .

## <a name="replying-to-a-message"></a>Respondendo a uma mensagem

Há dois casos em que uma extensão de aplicativo de mensagem precisará lidar ao responder a uma mensagem:

[![](advanced-message-app-extensions-images/interactive09.png "The Message App Extension in the Inactive and Active modes")](advanced-message-app-extensions-images/interactive09.png#lightbox)

- A **extensão está inativa** -há uma das bolhas de mensagem da extensão do aplicativo de mensagem na transcrição da mensagem que o usuário pode tocar para ativar as extensões e continuar a conversa interativa.
- A **extensão está ativa** -o usuário pode tocar na bolha da mensagem da extensão do aplicativo de mensagem na transcrição da mensagem para entrar no modo de exibição expandido e continuar o processo interativo de onde parou.

### <a name="the-extension-is-inactive"></a>A extensão está inativa

Quando uma bolha de mensagem é tocada pelo usuário na transcrição da mensagem e a extensão do aplicativo de mensagem está inativa, o seguinte processo ocorrerá:

[![](advanced-message-app-extensions-images/interactive10.png "Handling an inactive Message Bubble")](advanced-message-app-extensions-images/interactive10.png#lightbox)

1. O usuário toca na bolha da mensagem da extensão.
2. Quando uma extensão é iniciada, o aplicativo de mensagem inicia um processo.
3. O método `DidBecomeActive` é chamado e passado um `MSConversation` que representa a conversa em que a extensão do aplicativo de mensagem está sendo executada.
4. Como a extensão é baseada em um `UIViewController` `ViewWillAppear` e `ViewDidAppear` são chamados.

Quando o processo for concluído, a extensão do aplicativo de mensagem será apresentada no modo de exibição expandido.

### <a name="the-extension-is-active"></a>A extensão está ativa

Quando uma bolha de mensagem é tocada pelo usuário na transcrição da mensagem e a extensão do aplicativo de mensagem está ativa, o seguinte processo ocorrerá:

[![](advanced-message-app-extensions-images/interactive11.png "Handling an active Message Bubble")](advanced-message-app-extensions-images/interactive11.png#lightbox)

1. O usuário toca na bolha da mensagem da extensão.
2. Como a extensão do aplicativo de mensagem já está ativa, o método `WillTransition` da `MSMessagesAppViewController` é chamado para lidar com a alternância do modo de exibição compacto para o expandido.
3. O método `DidSelectMessage` da `MSMessagesAppViewController` é chamado e passado à `MSMessage` e `MSConversation` ao qual a bolha da mensagem pertence.
4. O método `DidTransition` da `MSMessagesAppViewController` é chamado para lidar com a alternância do Compact para o modo de exibição expandido.

Novamente, quando o processo for concluído, a extensão do aplicativo de mensagem será apresentada no modo de exibição expandido.

### <a name="accessing-the-selected-message"></a>Acessando a mensagem selecionada

Em ambos os casos, quando o usuário toca em uma bolha de mensagem que pertence à extensão do aplicativo de mensagem, ele precisa obter acesso ao `MSMessage` que foi tocado usando a propriedade `SelectedMessage` da `MSConversation`.

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

A mensagem selecionada deve ser mostrada na interface do usuário da extensão do aplicativo de mensagem e o usuário deve ter permissão para compor uma resposta.

## <a name="removing-partially-completed-messages"></a>Removendo mensagens parcialmente concluídas

No processo de enviar as diferentes etapas de uma conversa interativa entre os dois usuários na conversa, as bolhas de mensagem parcialmente concluídas podem começar a obstruir a transcrição da mensagem:

[![](advanced-message-app-extensions-images/interactive12.png "The partially completed Message Bubbles can cluttering the Message Transcript")](advanced-message-app-extensions-images/interactive12.png#lightbox)

Em vez disso, a extensão do aplicativo de mensagem deve recolher as bolhas de mensagem anteriores em um comentário sucinto na transcrição da mensagem:

[![](advanced-message-app-extensions-images/interactive13.png "Collapsing the previous Message Bubbles in the Message Transcript")](advanced-message-app-extensions-images/interactive13.png#lightbox)

Isso é tratado usando um `MSSession` para recolher todas as etapas existentes. Portanto, o método `DidSelectMessage` da classe `MSMessagesAppViewController` pode ser modificado para ser semelhante ao seguinte:

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

Se a mensagem selecionada já tiver um `MSSession`de saída, ela será usada mais uma nova `MSSession` será criada. A propriedade `SummaryText` da `MSMessage` é usada para adicionar uma legenda às etapas anteriores recolhidas. Se a propriedade `SummaryText` for definida como `null`, as etapas anteriores na conversa serão completamente removidas da transcrição de conversa.

## <a name="advanced-message-api-features"></a>Recursos avançados da API de mensagens

Com os recursos básicos da nova API de mensagens abordada em detalhes acima, examine em seguida alguns dos recursos mais avançados que a Apple criou na estrutura.

Primeiro, há vários outros métodos de substituição na classe `MSMessagesAppViewController` que fornecem um acesso mais profundo à conversa:

- `DidStartSendingMessage`-isso é chamado quando o usuário toca no botão enviar. Isso não significa que a mensagem realmente foi entregue ao destinatário, apenas que o processo de envio foi iniciado.
- `DidCancelSendingMessage`-isso ocorre quando o usuário toca no botão *X* no canto superior direito da bolha da mensagem na transcrição da conversa.
- `DidReceiveMessage`-esse método é chamado quando a extensão do aplicativo de mensagem está ativa uma nova mensagem foi recebida de um dos participantes na conversa.

### <a name="group-conversations"></a>Conversas de grupo

Uma extensão de aplicativo de mensagem pode ser usada enquanto os usuários estão envolvidos em conversas de grupo (com 3 ou mais indivíduos) e isso terá que ser levado em consideração ao projetar e implementar uma extensão de aplicativo de mensagem.

Dê uma olhada na seguinte interação em uma conversa em grupo com três usuários:

[![](advanced-message-app-extensions-images/interactive14.png "Interaction in a group conversation with three users")](advanced-message-app-extensions-images/interactive14.png#lightbox)

1. O usuário 1 envia uma mensagem interativa de grupo solicitando que o usuário 2 e o usuário 3 Escolha um levar este hambúrguer de topo.
2. O usuário 2 escolhe Tomatoes.
3. O usuário 3 escolhe as seleções.
4. As opções do usuário 2 e do usuário 3 chegam ao usuário 1 quase ao mesmo tempo. Como resultado, a escolha do usuário 2 é recolhida em uma linha de resumo e não está disponível. Esse caso também poderia ter sido invertido, com a escolha do usuário 2 sendo mostrada e que o usuário 3 está sendo recolhido.

Em ambos os casos, esse comportamento não é desejado, pois o usuário 1 deve poder acessar as opções do usuário 2 e do usuário 3. Para lidar com essa situação, a Apple está sugerindo que a extensão do aplicativo de mensagem armazene o estado da mensagem na nuvem e use a propriedade URL da `MSMessage` (que é enviada entre os usuários) para acessar esse estado.

Quando o usuário envia uma mensagem, um token de sessão é gerado e enviado por push para a nuvem com o estado da mensagem atual. Quando um usuário toca em uma bolha de mensagem na transcrição de conversa, o token de sessão é usado para recuperar o estado de sessão atual da nuvem.

### <a name="sender-identifiers"></a>Identificadores de remetente

Para discutir o acesso ao identificador do remetente de uma mensagem, veja o exemplo de uma conversa de grupo acima:

[![](advanced-message-app-extensions-images/interactive15.png "Group conversation sending Identifiers")](advanced-message-app-extensions-images/interactive15.png#lightbox)

1. Novamente, o usuário 1 envia uma mensagem interativa de grupo solicitando que o usuário 2 e o usuário 3 Escolha um levar este hambúrguer de topo.
2. O usuário 3 escolhe as seleções.
3. A escolha do usuário 3 retornou para o usuário 1 e o usuário 2 ainda não respondeu.
4. Como a Apple está muito preocupada com a privacidade do usuário, a extensão do aplicativo de mensagem só sabe sobre um identificador exclusivo (como um `NSUUID`) que é atribuído a cada participante da conversa. No dispositivo local, somente o identificador do usuário atual é conhecido.
5. O `MSMessage` tem uma propriedade `SenderIdentifier` que corresponde a um dos usuários na lista de participantes conhecida pela extensão.
6. Cada dispositivo de usuário tem sua própria cópia da lista de participantes, na qual novamente, somente o identificador do usuário local é conhecido.
7. Quando uma mensagem é enviada, sua propriedade `SenderIdentifier` também é conhecida como sendo a do usuário local.

Os identificadores de remetente podem ser usados das seguintes maneiras:

- Ao examinar a lista de participantes, a extensão pode obter o número de usuários na conversa.
- Quando a extensão recebe uma mensagem de um usuário, ela pode controlar o identificador do remetente. Se receber outra mensagem com o mesmo identificador de remetente, a extensão saberá que ela é do mesmo usuário.
- Eles podem ser usados para ajudar a identificar um usuário específico na conversa.

O identificador do remetente pode ser usado em qualquer um dos campos de texto do `MSMessageTemplateLayout` prefixando-o com um sinal de cifrão (`$`). Por exemplo:

```csharp
// Pass along the sender identifier
var layout = new MSMessageTemplateLayout()
layout.Caption = string.format("${0} wants pickles.",Conversation.LocalParticipantIdentifier.UuidString);
```

Quando o aplicativo mensagens exibe uma bolha de mensagem com esse tipo de formatação, ele substituirá o `$uuid...` pelo nome de contato do participante na conversa que enviou a mensagem.

O identificador do remetente é exclusivo em cada dispositivo, portanto, olhando para o diagrama acima novamente, observe que o dispositivo do usuário 1 e do usuário 3 tem um identificador de remetente exclusivo diferente para cada participante na conversa.

Os identificadores de remetente têm como escopo a instalação da extensão do aplicativo de mensagem. Portanto, se um usuário desinstalar e reinstalar a extensão do aplicativo de mensagem, novos identificadores de remetente serão gerados para a nova instalação.

Para acessar os identificadores do remetente, a extensão pode usar o seguinte código:

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

As mensagens interativas geradas por uma extensão de aplicativo de mensagem serão entregues nas seguintes plataformas da Apple:

- watchOS 3
- macOS Sierra
- iOS 10

Das três plataformas, somente o iOS 10 permitirá que o usuário gere uma mensagem interativa. Em macOS Sierra, se o usuário clicar em uma bolha de mensagem interativa, a URL anexada ao `MSMessage` será aberta no Safari e uma representação da mensagem deverá ser exibida lá.

No watchOS, o aplicativo messages pode enviar uma mensagem interativa para um dispositivo iOS anexado, no qual o usuário pode redigir uma resposta.

A nova API de mensagens terá suporte para fallback se a mensagem interativa for recebida em plataformas Apple mais antigas:

- watchOS 2 +
- OS X 10,11 +
- iOS 9 +

Eles serão entregues em um formato de fallback como duas mensagens separadas:

- Uma será a imagem conforme fornecida pelo layout do modelo.
- O outro será a URL conforme fornecido no `MSMessage`.

## <a name="summary"></a>Resumo

Este artigo apresentou técnicas avançadas para trabalhar com extensões de aplicativo de mensagens em uma solução Xamarin. iOS que se integra ao aplicativo de **mensagens** e apresenta novas funcionalidades para o usuário.

## <a name="related-links"></a>Links relacionados

- [Construtor de sorvetes (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-icecreambuilder)
- [Referência de mensagens](https://developer.apple.com/reference/messages)
- [Guia de programação de extensão de aplicativo](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)
