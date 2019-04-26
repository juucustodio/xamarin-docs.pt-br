---
title: Extensões de aplicativo de mensagens avançadas no xamarin. IOS
description: Este artigo mostra técnicas avançadas para trabalhar com extensões de aplicativo de mensagem em uma solução do xamarin. IOS que se integra com o aplicativo de mensagens e apresenta a nova funcionalidade para o usuário.
ms.prod: xamarin
ms.assetid: 394A1FDA-AF70-4493-9B2C-4CFE4BE791B6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: baceb59116dd907918b34eca4f44293051190954
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61155475"
---
# <a name="advanced-message-app-extensions-in-xamarinios"></a>Extensões de aplicativo de mensagens avançadas no xamarin. IOS

_Este artigo mostra técnicas avançadas para trabalhar com extensões de aplicativo de mensagem em uma solução do xamarin. IOS que se integra com o aplicativo de mensagens e apresenta a nova funcionalidade para o usuário._


Novo para o iOS 10, uma mensagem da extensão do aplicativo se integra com o **mensagens** aplicativo e apresenta novas funcionalidades para o usuário. A extensão pode enviar mensagens de texto, adesivos, arquivos de mídia e interativas.

## <a name="about-message-app-extensions"></a>Sobre extensões de aplicativo de mensagem

Como mencionado acima, uma mensagem da extensão do aplicativo se integra com o **mensagens** aplicativo e apresenta novas funcionalidades para o usuário. A extensão pode enviar mensagens de texto, adesivos, arquivos de mídia e interativas. Dois tipos de mensagem da extensão do aplicativo estão disponíveis:

- **Pacotes de adesivo** -contém uma coleção de adesivos que o usuário pode adicionar a uma mensagem. Pacotes de adesivo podem ser criados sem escrever nenhum código.
- **iMessage aplicativo** -pode apresentar uma Interface de usuário personalizada dentro do aplicativo de mensagens para selecionar os adesivos, inserir texto, incluindo arquivos de mídia (com conversões de tipo opcional) e criando, editando e enviando mensagens de interação.

Extensões de aplicativos de mensagens fornecer três principais tipos de conteúdo:

- **Mensagens interativas** -são um tipo de conteúdo de mensagem personalizada que gera um aplicativo, quando o usuário toca na mensagem, o aplicativo será iniciado em primeiro plano.
- **Adesivos** -são imagens geradas pelo aplicativo que pode ser incluído nas mensagens enviadas entre usuários. Consulte nosso [construtor de sorvete](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/) aplicativo de exemplo para um exemplo de implementação de um aplicativo de etiqueta com o pacote.
- **Outros tipos de conteúdo com suporte** -ele pode fornecer conteúdo, como fotos, vídeos, texto ou links do tipo que sempre tem suporte pelo aplicativo de mensagens.

Novo para o iOS 10, o aplicativo de mensagem agora inclui seu próprio Store de aplicativo dedicado e internos. Todos os aplicativos que incluem as extensões de aplicativos de mensagem serão exibidos e promovidos neste repositório. A gaveta de aplicativo de mensagens novas exibirá todos os aplicativos que tiverem sido baixados de Store de aplicativo de mensagens para fornecer acesso rápido aos usuários.

Também novo no iOS 10, Apple adicionou Inline App Attribution que permite ao usuário descobrir facilmente um aplicativo. Por exemplo, se um usuário envia o conteúdo para outro de um aplicativo que o usuário 2º não tiver instalado (como um adesivo por exemplo), o nome do aplicativo de envio é listado sob o conteúdo no histórico de mensagens. Se o usuário toca o aplicativo nome, Store de aplicativo a mensagem ser abrimos e o aplicativo selecionado no repositório.

Extensões de aplicativos de mensagem são semelhantes aos aplicativos existentes do iOS que o desenvolvedor é familiar para criação e eles terão acesso a todos os recursos de um aplicativo iOS padrão e estruturas padrão. Por exemplo:

- Eles têm acesso a compra no aplicativo.
- Eles têm acesso à Apple Pay.
- Eles têm acesso ao hardware do dispositivo, como a câmera.

Somente há suporte para extensões de aplicativos de mensagem no iOS 10, no entanto, o conteúdo que enviam essas extensões é visível em dispositivos watchOS e macOS. O novo _Recents Page_ adicionado ao watchOS 3, exibirá os adesivos recentes que foram enviados do telefone, incluindo aqueles de extensões de aplicativos de mensagem, e permitir que o usuário enviar os adesivos de inspeção.

## <a name="about-interactive-messages"></a>Sobre mensagens interativas

Mensagens interativas apresentam uma bolha de mensagem personalizada e são fornecidas por uma extensão de aplicativo de mensagem. Eles permitem que o usuário crie interativa o conteúdo da mensagem, insira-o no campo de entrada de mensagem e enviá-lo.

[![](advanced-message-app-extensions-images/interactive01.png "Criação de conteúdo de mensagem interativa")](advanced-message-app-extensions-images/interactive01.png#lightbox)

O usuário pode responder a uma mensagem interativa tocando suas bolhas de mensagem no histórico de mensagens para carregar a extensão de aplicativo de mensagem que o criou. A extensão será iniciado em tela inteira e permitir que o usuário para compor uma resposta e a enviará de volta para o usuário de origem.

[![](advanced-message-app-extensions-images/interactive02.png "A extensão iniciado em tela inteira")](advanced-message-app-extensions-images/interactive02.png#lightbox)


Os seguintes tópicos serão abordados em detalhes abaixo:

- Visão geral da API de mensagens
- O ciclo de vida de extensão
- Redigir uma mensagem
- Enviar uma mensagem

## <a name="messages-api-overview"></a>Visão geral da API de mensagens

Quando invocado pelo usuário, uma extensão de aplicativo de mensagem será exibida na parte inferior do histórico de mensagens no modo de exibição compact:

[![](advanced-message-app-extensions-images/interactive03.png "Visão geral da API de mensagens")](advanced-message-app-extensions-images/interactive03.png#lightbox)

1. O `MSMessageAppViewController` objeto na extensão de aplicativo de mensagem é a classe principal que é chamada quando o modo de exibição da extensão é exibido ao usuário.
2. A conversa é apresentada ao usuário como um `MSConversation` instância do objeto.
3. O `MSMessage` classe representa uma determinada bolha de mensagem na conversa.
4. `MSSession` Controla como uma mensagem é enviada.
5. `MSMessageTemplateLayout` Controla como a mensagem é exibida

## <a name="the-extension-lifecycle"></a>O ciclo de vida de extensão

Examine o processo de uma extensão de aplicativo de mensagem de ativação:

[![](advanced-message-app-extensions-images/interactive04.png "O processo de uma extensão de aplicativo de mensagem de ativação")](advanced-message-app-extensions-images/interactive04.png#lightbox)

1. Quando uma extensão é iniciada (por exemplo, da gaveta de aplicativo), o aplicativo de mensagem iniciará um processo.
2. O `DidBecomeActive` método é chamado e passado um `MSConversation` que representa a conversa que a mensagem da extensão do aplicativo está em execução no.
3. Porque a extensão é baseada fora de um `UIViewController` ambos `ViewWillAppear` e `ViewDidAppear` são chamados.

Em seguida, examine o processo de uma extensão de aplicativo de mensagem se tornando desativado:

[![](advanced-message-app-extensions-images/interactive05.png "O processo de uma extensão de aplicativo de mensagem se tornando desativado")](advanced-message-app-extensions-images/interactive05.png#lightbox)

1. Quando a mensagem da extensão do aplicativo está sendo desativada, o `ViewWillDisappear` método será chamado pela primeira vez.
2. Em seguida, a `ViewDidDisappear` método será chamado.
3. O `WillResignActive` método é chamado e passado um `MSConversation` que representa a conversa que a mensagem da extensão do aplicativo está em execução no. Neste ponto, a conexão entre o aplicativo de mensagens e a extensão estiver prestes a ser lançada.
4. Em algum momento posterior, o processo foi encerrado, o aplicativo de mensagens.

Como uma extensão é um processo de curta duração, ela será encerrada agressivamente pelo sistema para conservar energia da bateria e o processamento. O desenvolvedor deve manter isso em mente ao projetar e implementar uma extensão de aplicativo de mensagem.

## <a name="composing-a-message"></a>Redigir uma mensagem

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

Esse código cria uma nova `MSMessage` e define várias propriedades (como `Url`). Enquanto a mensagem só pode ser criada no iOS, ele pode ser enviado para o iOS e macOS a ser exibido.

Se o usuário clicar no balão de mensagem na conversa no macOS, o Mac tentará abrir o endereço especificado na URL no navegador da web. Como resultado, o site do desenvolvedor deve ser capaz de Mostrar computadores baseados em alguma representação da mensagem no navegador da web no macOS.

O `AccessibilityLabel` pelos leitores de tela, a propriedade é usada para ler a transcrição da conversa para o usuário. O `Layout` propriedade especifica como a mensagem será exibida, no momento, somente o `MSMessageTemplateLayout` é suportado e é semelhante ao seguinte:

[![](advanced-message-app-extensions-images/interactive06.png "O modelo MSMessageTemplateLayout")](advanced-message-app-extensions-images/interactive06.png#lightbox)

O `Image` propriedade do `MSMessageTemplateLayout` fornece conteúdo para o corpo principal do MessageBubble na tela. O `MediaFileUrl` propriedade também fornece o conteúdo do corpo da mensagem de bolha, mas permite que o conteúdo que não é suportado pelo `UIImage` (como um arquivo de vídeo que seria um loop em segundo plano). Se ambos os `Image` e `MediaFileUrl` as propriedades são fornecidas, o `Image` propriedade terá precedência. O `MediaFileUrl` dá suporte a PNG, JPEG, GIF os vídeo (em qualquer formato que pode ser reproduzido pela estrutura de Player de mídia) formatos de mídia.

O tamanho da mídia recomendado é 300 x 300 pixels na resolução 3 x. Ativos ligeiramente maiores e menores também são aceitos e a Apple sugere o teste com alguns tamanhos diferentes para obter os melhores resultados. O aplicativo de mensagem será reduzir e escalar esta mídia conforme necessário.

Quando os ativos são enviados para o receptor, qualquer mídia anexada será automaticamente transcodificados pelo aplicativo de mensagens para otimizá-los de transferência sobre as redes. Por isso, Apple desencoraja incluindo texto na mídia que é anexado à mensagem porque a mídia será reduzida verticalmente e compactadas para transmissão, assim, potencialmente processar o texto ilegível.

O `ImageTitle` e `ImageSubtitle` propriedades fornecem uma descrição para a mídia que é apresentada na bolha de mensagem. Essas propriedades serão enviadas como texto para o dispositivo de recebimento onde eles serão nitidez renderizados no canto inferior esquerdo da imagem.

O `Caption`, `SubCaption`, `TrailingCaption` e `TrailingSubcaption` ainda mais as propriedades descrevem a imagem e serão renderizadas em uma seção abaixo da imagem. Definir todas essas propriedades para `null` criará uma bolha de mensagem sem área de legenda:

[![](advanced-message-app-extensions-images/interactive07.png "Uma bolha de mensagem sem área de legenda")](advanced-message-app-extensions-images/interactive07.png#lightbox)

A última coisa a observar é que o aplicativo de mensagens será desenhar o ícone da extensão de aplicativo de mensagem no canto superior esquerdo da bolha de mensagem.

## <a name="sending-a-message"></a>Enviar uma mensagem

Uma vez um `MSMessage` foi redigido, o código a seguir pode ser usado para enviar a ele:

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

O `ActiveConversation` propriedade do `MSMessagesAppViewController` manterá a conversa atual que a mensagem da extensão do aplicativo foi iniciada.

Chame o `InsertMessage` do `MSConversation` para incluir a mensagem na conversa e tratar os erros que possam surgir. Se a mensagem está incluída com êxito, a mensagem de bolha será exibida no campo de entrada.

Além disso, a extensão pode enviar diferentes tipos de dados para a conversa, como:

- **Texto** - `ActiveConversation.InsertText ("Message", (error) => {...});`
- **Anexos** - `ActiveConversation.InsertAttachment (new NSUrl ("path"), "filename", (error) => {...});`
- **Adesivos**  -  `ActiveConversation.InsertSticker (sticker, (obj) => {...});` onde `sticker` é um `MSSticker`.

Depois que o novo conteúdo estiver no campo de entrada, o usuário é capaz de enviar a mensagem, toque azul **enviar** botão (como se fossem qualquer mensagem típica). Não é possível para a extensão de aplicativo de mensagem enviar automaticamente o conteúdo, esse processo é totalmente sob o controle do usuário.

## <a name="handling-the-compact-and-expanded-modes"></a>Tratando os modos compactas e expandidos

Uma extensão de aplicativo de mensagem pode ser exibida em um dos dois modos de exibição diferentes:

[![](advanced-message-app-extensions-images/interactive08.png "Uma extensão de aplicativo de mensagem exibido em dois modos de exibição diferentes: Compact & expandido")](advanced-message-app-extensions-images/interactive08.png#lightbox)

- **Compact** -isso é o modo padrão em que a mensagem da extensão do aplicativo se ocupa a parte inferior 25% da exibição de mensagem. No modo compacto, o aplicativo não tem acesso ao teclado, a rolagem horizontal ou reconhecedores de gestos de passar o dedo. O aplicativo tem acesso ao campo de entrada e chamadas para `InsertMessage` instantaneamente será exibido para o usuário existe.
- **Expandido** -a extensão de aplicativo de mensagem preenche toda a exibição de mensagem. Ele não tem acesso ao campo de entrada, mas tem acesso ao teclado, a rolagem horizontal e reconhecedores de gestos de passar o dedo.

Uma extensão de aplicativo de mensagem pode ser alternada entre esses modos de forma programática ou manualmente pelo usuário a qualquer momento e deve ser instantaneamente responsivo a qualquer modo de exibição Altera modo.

Vejamos o exemplo a seguir de lidar com a alternância entre os dois modos de exibição diferente. Dois controladores de exibição diferentes serão necessárias para cada estado. O `StickerBrowserViewController` identificadores a **Compact** exibição e o `AddStickerViewController` manipulará as **expandido** exibição:

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

Opcionalmente, o aplicativo poderia ter usado o `WillTransition` método para lidar com a alteração do modo de exibição antes de serem apresentado ao usuário (como é feito no construtor de sorvete exemplo acima). Para obter mais informações, consulte nosso [personalização adicional de adesivo](~/ios/platform/message-app-integration/intro-to-message-app-extensions.md) documentação.

## <a name="replying-to-a-message"></a>Respondendo a uma mensagem

Há dois casos que uma extensão de aplicativo de mensagem precisa lidar ao responder a uma mensagem:

[![](advanced-message-app-extensions-images/interactive09.png "A extensão de aplicativo de mensagem nos modos ativo e inativo")](advanced-message-app-extensions-images/interactive09.png#lightbox)

- **Extensão for inativo** -há um dos balões de mensagens da extensão de aplicativo de mensagem na mensagem de transcrição que o usuário pode tocar para ativar as extensões e continuar a conversa interativa.
- **Extensão está ativo** -o usuário pode tocar a bolha de mensagem da extensão de aplicativo de mensagem na mensagem de transcrição para entrar no modo de exibição expandido e continuar o processo interativo a partir de onde parou.

### <a name="the-extension-is-inactive"></a>A extensão está inativo

Quando uma bolha de mensagem é tocada pelo usuário na transcrição de mensagem e a mensagem da extensão do aplicativo está inativa, acontecerá o seguinte processo:

[![](advanced-message-app-extensions-images/interactive10.png "Manipulando uma bolha de mensagem inativa")](advanced-message-app-extensions-images/interactive10.png#lightbox)

1. O usuário toca bolhas de mensagem da extensão.
2. Quando uma extensão é iniciada, o aplicativo de mensagem iniciará um processo.
3. O `DidBecomeActive` método é chamado e passado um `MSConversation` que representa a conversa que a mensagem da extensão do aplicativo está em execução no.
4. Porque a extensão é baseada fora de um `UIViewController` ambos `ViewWillAppear` e `ViewDidAppear` são chamados.

Quando o processo for concluído, a extensão de aplicativo de mensagens será apresentada no modo de exibição expandido.

### <a name="the-extension-is-active"></a>A extensão está ativo

Quando uma bolha de mensagem é tocada pelo usuário na transcrição de mensagem e a extensão de aplicativo de mensagem está ativa, acontecerá o seguinte processo:

[![](advanced-message-app-extensions-images/interactive11.png "Manipulando uma bolha de mensagem Active Directory")](advanced-message-app-extensions-images/interactive11.png#lightbox)

1. O usuário toca bolhas de mensagem da extensão.
2. Porque a extensão de aplicativo de mensagem já está ativa, o `WillTransition` método da `MSMessagesAppViewController` é chamado para manipular alternando do CD para o modo de exibição expandido.
3. O `DidSelectMessage` método da `MSMessagesAppViewController` é chamado e passado a `MSMessage` e `MSConversation` que pertence a bolha de mensagem.
4. O `DidTransition` método da `MSMessagesAppViewController` é chamado para manipular alternando do CD para o modo de exibição expandido.

Novamente, quando o processo for concluído, a extensão de aplicativo de mensagens será apresentada no modo de exibição expandido.

### <a name="accessing-the-selected-message"></a>Acessando a mensagem selecionada

Em ambos os casos, quando o usuário toca uma bolha de mensagem que pertencem à extensão de aplicativo de mensagem, ele precisará acessar o `MSMessage` que foi tocado usando o `SelectedMessage` propriedade do `MSConversation`.

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

A mensagem selecionada deve ser mostrada na interface de usuário da extensão de aplicativo de mensagem e o usuário deve ter permissão para compor uma resposta.

## <a name="removing-partially-completed-messages"></a>Removendo parcialmente concluída mensagens

No processo de envio diferentes etapas de uma conversa interativa entre o usuário duas na conversa, as bolhas de mensagem parcialmente concluído pode começar a sobrecarregar a transcrição de mensagem:

[![](advanced-message-app-extensions-images/interactive12.png "Os balões de mensagem parcialmente concluído pode encher a transcrição de mensagem")](advanced-message-app-extensions-images/interactive12.png#lightbox)

Em vez disso, a extensão de aplicativo de mensagem deve recolher as bolhas de mensagem anterior em um comentário sucinto na transcrição de mensagem:

[![](advanced-message-app-extensions-images/interactive13.png "Os balões de mensagem anterior na transcrição de mensagem de recolhimento")](advanced-message-app-extensions-images/interactive13.png#lightbox)

Isso é feito usando um `MSSession` para recolher todas as etapas existentes. Portanto, o `DidSelectMessage` método da `MSMessagesAppViewController` classe pode ser modificado para ser semelhante ao seguinte:

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

Se a mensagem selecionada já tem um saindo `MSSession`, ela é usada ou um novo `MSSession` é criado. O `SummaryText` propriedade do `MSMessage` é usado para adicionar uma legenda para as etapas anteriores recolhidas. Se o `SummaryText` estiver definida como `null`, as etapas anteriores na conversa serão completamente removidas da transcrição de conversa.

## <a name="advanced-message-api-features"></a>Recursos da API de mensagem avançados

Com os recursos básicos da nova API de mensagem abordados detalhadamente acima, em seguida examine alguns dos recursos mais avançados que a Apple incorporou o Framework.

Em primeiro lugar, há vários outros métodos de substituição no `MSMessagesAppViewController` classe que fornecem maior acesso a conversa:

- `DidStartSendingMessage` -Isso é chamado quando o usuário toca no botão de envio. Isso não significa que a mensagem foi realmente entrega para o destinatário, apenas que o processo de envio foi iniciado.
- `DidCancelSendingMessage` – Isso ocorre quando o usuário toca a *X* botão no canto superior direito da bolha de mensagem na conversa transcrição.
- `DidReceiveMessage` -Este método é chamado quando a extensão de aplicativo de mensagem está ativa um uma nova mensagem foi recebida de um dos participantes da conversa.

### <a name="group-conversations"></a>Conversas em grupo

Uma extensão de aplicativo de mensagem pode ser usada enquanto os usuários estão envolvidos em conversas do grupo (com 3 ou mais pessoas) e isso terá que ser levados em consideração ao projetar e implementar uma extensão de aplicativo de mensagem.

Examine a interação a seguir em uma conversa de grupo com três usuários:

[![](advanced-message-app-extensions-images/interactive14.png "Interação em uma conversa de grupo com três usuários")](advanced-message-app-extensions-images/interactive14.png#lightbox)

1. O usuário 1 envia um grupo de mensagem interativa solicitando que o usuário 2 e 3 de usuário para escolher um sabor burger.
2. O usuário 2 escolhe tomatoes.
3. O usuário 3 escolhe Conservas.
4. As opções de usuário 2 e do usuário 3 chegarem para 1 usuário quase simultaneamente. Como resultado, a escolha do usuário 2 será recolhida em uma linha de resumo e não está disponível. Esse caso poderia ter também foi invertido, com a escolha do usuário 2 que está sendo mostrado e 3 de usuário do que está sendo recolhido.

Em ambos os casos, esse comportamento é indesejado como usuário 1 deve ser capaz de acessar as opções do usuário 2 e do usuário 3. Para lidar com essa situação, Apple está sugerindo que a extensão de aplicativo de mensagens armazena o estado da mensagem na nuvem e usar a propriedade URL do `MSMessage` (que é enviado entre os usuários) para acessar esse estado.

Quando o usuário envia uma mensagem, um token de sessão é gerado e enviado por push para a nuvem com o estado atual da mensagem. Quando um usuário tocar em uma bolha de mensagem na conversa transcrição, o token de sessão é usado para recuperar o estado da sessão atual da nuvem.

### <a name="sender-identifiers"></a>Identificadores de remetente

Para discutir a acessar o identificador do remetente de uma mensagem, veja o exemplo de uma conversa de grupo fornecido acima:

[![](advanced-message-app-extensions-images/interactive15.png "Envio de identificadores de conversa de grupo")](advanced-message-app-extensions-images/interactive15.png#lightbox)

1. Novamente, o usuário 1 envia um grupo de mensagem interativa solicitando que o usuário 2 e 3 de usuário para escolher um sabor burger.
2. Usuário 3 escolhe Conservas.
3. Chega de escolha do usuário do 3 volta para o usuário 1 e 2 do usuário ainda não tiver respondido.
4. Como a Apple é muito preocupado com a privacidade do usuário, a extensão de aplicativo de mensagem só conhece um identificador exclusivo (como um `NSUUID`) que é atribuído a cada participante na conversa. No dispositivo local, somente o identificador do usuário atual é conhecido.
5. O `MSMessage` tem um `SenderIdentifier` propriedade que corresponde a um do usuário na lista de participantes conhecida para a extensão.
6. Cada dispositivo de usuário tem sua própria cópia da lista de participante novamente, no qual apenas o identificador do usuário local é conhecido.
7. Quando uma mensagem é enviada, sua `SenderIdentifier` propriedade também é conhecida por ser o usuário local.

Os identificadores de remetente pode ser usados das seguintes maneiras:

- Observando a lista de participantes a extensão pode obter o número de usuários na conversa.
- Quando a extensão recebe uma mensagem de um usuário, ela pode manter controle do identificador do remetente. Se ele recebe outra mensagem com o mesmo identificador de remetente, a extensão sabe que é do mesmo usuário.
- Eles podem ser usados para ajudar a identificar um usuário específico na conversa.

O identificador do remetente pode ser usado em qualquer um dos campos de texto a `MSMessageTemplateLayout` prefixando-lo com um sinal de cifrão (`$`). Por exemplo:

```csharp
// Pass along the sender identifier
var layout = new MSMessageTemplateLayout()
layout.Caption = string.format("${0} wants pickles.",Conversation.LocalParticipantIdentifier.UuidString);
```

Quando o aplicativo de mensagens exibe uma bolha de mensagem com esse tipo de formatação, ele substituirá o `$uuid...` com o nome de contato do participante na conversa que enviou a mensagem.

O identificador do remetente é exclusivo em cada dispositivo, portanto, ao examinar o diagrama acima novamente, observe que do dispositivo usuário e 1 dispositivo do usuário 3 tem um identificador de remetente exclusivo diferente para cada participante na conversa.

Os identificadores de remetente limitam-se para a instalação da extensão de aplicativo de mensagem. Portanto, se um usuário desinstala e reinstala a extensão de aplicativo de mensagem novos identificadores de remetente serão gerados para a nova instalação.

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

As mensagens interativas gerados por uma extensão de aplicativo de mensagem será entregue nas seguintes plataformas da Apple:

- watchOS 3
- macOS Sierra
- iOS 10

As três plataformas, iOS 10 permitirá que o usuário gere uma mensagem interativa. No macOS Sierra, se o usuário clica em uma bolha de mensagem interativa, a URL anexada ao `MSMessage` será aberto no Safari e uma representação da mensagem deve ser exibida.

No watchOS, o aplicativo de mensagens pode entrega uma mensagem interativa para um dispositivo iOS anexado em que o usuário pode compor uma resposta.

A nova API de mensagens tem suporte para fallback se a mensagem interativa é recebida em plataformas mais antigas da Apple:

- watchOS 2 +
- OS X 10.11 +
- iOS 9 +

Elas serão enviadas em um formato de fallback como duas mensagens separadas:

- Um será a imagem, conforme fornecido pelo modelo de Layout.
- O outro será a URL como fornecido a `MSMessage`.

## <a name="summary"></a>Resumo

Este artigo apresentou técnicas avançadas para trabalhar com extensões de aplicativo de mensagem em uma solução do xamarin. IOS que se integra com o **mensagens** presente novas funcionalidades para o usuário e aplicativo.


## <a name="related-links"></a>Links relacionados

- [Construtor de sorvete (amostra)](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/)
- [Referência de mensagens](https://developer.apple.com/reference/messages)
- [Guia de programação de extensão do aplicativo](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)
