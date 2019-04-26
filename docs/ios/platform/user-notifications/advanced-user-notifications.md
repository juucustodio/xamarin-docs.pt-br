---
title: Notificações de usuário avançadas no xamarin. IOS
description: Este artigo analisa profundamente a estrutura de notificações do usuário, introduzida no iOS 10. Ele aborda as notificações do usuário, a interface de notificação do usuário, anexos de mídia, interfaces do usuário personalizadas e muito mais.
ms.prod: xamarin
ms.assetid: 4E0C60AE-6F54-4098-8FA0-AADF9AC86805
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/03/2018
ms.openlocfilehash: 4472654064812142e3281374754ace0042b542bf
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61089153"
---
# <a name="advanced-user-notifications-in-xamarinios"></a>Notificações de usuário avançadas no xamarin. IOS

Novo no iOS 10, a notificação do usuário framework permite a entrega e a manipulação de notificações locais e remotas. Usando essa estrutura, um aplicativo ou extensão do aplicativo pode agendar a entrega de notificações locais, especificando um conjunto de condições, como local ou a hora do dia.

## <a name="about-user-notifications"></a>Sobre notificações de usuário

A nova estrutura de notificação do usuário permite a entrega e a manipulação de notificações locais e remotas. Usando essa estrutura, um aplicativo ou extensão do aplicativo pode agendar a entrega de notificações locais, especificando um conjunto de condições, como local ou a hora do dia.

Além disso, o aplicativo ou a extensão pode receber (e potencialmente modificar) notificações locais e remotas conforme elas são entregues ao dispositivo iOS do usuário.

A nova estrutura de interface do usuário de notificação de usuário permite que um aplicativo ou extensão do aplicativo para personalizar a aparência de notificações locais e remotas quando eles são apresentados ao usuário.

Essa estrutura fornece as seguintes maneiras que um aplicativo pode enviar notificações a um usuário:

- **Alertas Visual** – em que a notificação rola para baixo da parte superior da tela como uma faixa.
- **Som e vibrações** -pode ser associado uma notificação.
- **Notificações de ícone do aplicativo** - onde o ícone do aplicativo exibe uma notificação mostrando que o novo conteúdo está disponível. Como o número de mensagens de email não lido.

Além disso, dependendo do contexto do usuário atual, há diferentes maneiras que uma notificação será exibida:

- Se o dispositivo seja desbloqueado, a notificação será rolar para baixo da parte superior da tela como uma faixa.
- Se o dispositivo estiver bloqueado, a notificação será exibida na tela de bloqueio do usuário.
- Se o usuário perdeu uma notificação, podem abrir o Centro de notificação e exibir qualquer disponível, notificações de espera.

Um aplicativo xamarin. IOS tem dois tipos de notificações de usuário que é capaz de enviar:

- **Notificações locais** -elas são enviadas por aplicativos instalados localmente no dispositivo do usuário.
- **Notificações remotas** – são enviadas de um servidor remoto e o apresentado ao usuário ou dispara uma atualização do plano de fundo do conteúdo do aplicativo.

Para obter mais informações, consulte nosso [notificações do usuário aprimorada](~/ios/platform/user-notifications/enhanced-user-notifications.md) documentação.

## <a name="the-new-user-notification-interface"></a>A nova interface de notificação do usuário

Notificações do usuário no iOS 10 são apresentadas com um novo design de interface do usuário que fornece mais conteúdo, como título, subtítulo e anexos de mídia opcional que pode ser apresentada na tela de bloqueio, como uma faixa na parte superior do dispositivo ou no Centro de notificação.

Não importa onde é exibida uma notificação de usuário no iOS 10, ela é apresentada com a mesma aparência e com os mesmos recursos e funcionalidade.

No iOS 8, a Apple introduziu notificações acionáveis em que o desenvolvedor poderia anexar ações personalizadas em uma notificação e permitir que o usuário agir em uma notificação sem precisar iniciar o aplicativo. No iOS 9, a Apple aprimorado notificações acionáveis com resposta rápida, que permite ao usuário responder a uma notificação com a entrada de texto.

Como as notificações do usuário são uma parte mais integrante da experiência do usuário no iOS 10, Apple tem ampliado ainda mais notificações acionáveis para dar suporte a toque 3D, em que o usuário pressiona uma notificação e uma interface do usuário personalizada é a exibição para fornecer a interação sofisticada com a notificação.

Quando a interface do usuário personalizada do usuário notificação é exibida, se o usuário interage com quaisquer ações anexadas à notificação, a interface do usuário personalizado pode ser atualizado instantaneamente para fornecer comentários sobre o que mudou.

Novo para o iOS 10, a API de interface do usuário de notificação do usuário permite que um aplicativo xamarin. IOS facilmente tirar proveito desses novos recursos da interface do usuário de notificação do usuário.

## <a name="adding-media-attachments"></a>Adição de anexos de mídia

Um dos itens mais comuns que são compartilhados entre os usuários é fotos, portanto, o iOS 10 adicionou a capacidade de anexar um Item de mídia (por exemplo, uma foto) diretamente para uma notificação, onde ele será apresentada e prontamente disponíveis para o usuário com o restante dos Conté da notificação NT.

No entanto, devido a tamanhos envolvidos no envio até mesmo uma imagem pequena, anexá-lo a uma carga de notificação remoto se torna impraticável. Para lidar com essa situação, o desenvolvedor pode usar a nova extensão de serviço no iOS 10 para baixar a imagem de outra fonte (como um repositório de dados do CloudKit) e anexá-lo para o conteúdo da notificação antes de ser exibido ao usuário.

Para obter uma notificação remota a ser modificada por uma extensão de serviço, sua carga deve ser marcada como mutável. Por exemplo:

```csharp
{
    aps : {
        alert : "New Photo Available",
        mutable-content: 1
    },
    my-attachment : "https://example.com/photo.jpg"
}
```

Examine a visão geral do processo a seguir:

[![](advanced-user-notifications-images/extension02.png "Adicionando o processo de anexos de mídia")](advanced-user-notifications-images/extension02.png#lightbox)

Depois que a notificação remota é entregue ao dispositivo (por meio de APNs), a extensão de serviço, em seguida, pode baixar a imagem necessária por meio de qualquer meio desejado (por exemplo, um `NSURLSession`) e depois de receber a imagem, ele pode modificar o conteúdo da notificação e exibir -o para o usuário.

Este é um exemplo de como esse processo pode ser tratado no código:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;

namespace MonkeyNotification
{
    public class NotificationService : UNNotificationServiceExtension
    {
        #region Constructors
        public NotificationService ()
        {
        }
        #endregion

        #region Override Methods
        public override void DidReceiveNotificationRequest (UNNotificationRequest request, Action<UNNotificationContent> contentHandler)
        {
            // Get file URL
            var attachementPath = request.Content.UserInfo.ObjectForKey (new NSString ("my-attachment"));
            var url = new NSUrl (attachementPath.ToString ());

            // Download the file
            var localURL = new NSUrl ("PathToLocalCopy");

            // Create attachment
            var attachmentID = "image";
            var options = new UNNotificationAttachmentOptions ();
            NSError err;
            var attachment = UNNotificationAttachment.FromIdentifier (attachmentID, localURL, options , out err);

            // Modify contents
            var content = request.Content.MutableCopy() as UNMutableNotificationContent;
            content.Attachments = new UNNotificationAttachment [] { attachment };

            // Display notification
            contentHandler (content);
        }

        public override void TimeWillExpire ()
        {
            // Handle service timing out
        }
        #endregion
    }
}
```

Quando a notificação é recebida de APNs, o endereço personalizado da imagem é ler o conteúdo e arquivo é baixado do servidor. Em seguida, um `UNNotificationAttachement` é criado com uma ID exclusiva e o local da imagem (como um `NSUrl`). Uma cópia mutável do conteúdo da notificação é criada e anexos de mídia são adicionados. Por fim, a notificação é exibida ao usuário chamando o `contentHandler`.

Após a adição de um anexo a uma notificação, o sistema tem sobre a movimentação e gerenciamento do arquivo.

Além de notificações remotas apresentado acima, os anexos de mídia também têm suporte de notificações locais, em que o `UNNotificationAttachement` é criado e anexado à notificação junto com seu conteúdo.

Anexos de mídia de imagens de dar suporte a notificação no iOS 10 (estáticos e GIFs), áudio ou vídeo e o sistema exibirá automaticamente a interface do usuário personalizada correto para cada um desses tipos de anexos quando a notificação é apresentada ao usuário.

> [!NOTE]
> Tome cuidado para otimizar os dois ao tamanho da mídia e o tempo necessário para baixar a mídia do servidor remoto (ou para montar a mídia para notificações locais) como o sistema impõe limites estritos para ambos, ao executar a extensão de serviço do aplicativo. Por exemplo, considere o envio de uma versão reduzida da imagem ou um pequeno clipe de um vídeo a serem apresentados na notificação.

## <a name="creating-custom-user-interfaces"></a>Criando interfaces do usuário personalizadas

Para criar uma Interface de usuário personalizada para suas notificações de usuário, o desenvolvedor precisa adicionar uma extensão de conteúdo de notificação (novo para o iOS 10) a solução do aplicativo.

A extensão de conteúdo de notificação permite que o desenvolvedor adicione seus próprios modos de exibição na interface do usuário de notificação e desenhar qualquer conteúdo que eles querem. Começando com o iOS 12, extensões de conteúdo de notificação oferecem suporte a controles de interface do usuário interativos, como botões e controles deslizantes. Para obter mais informações, consulte o [notificações interativas no iOS 12](~/ios/platform/introduction-to-ios12/notifications/interactive.md) documentação.

Para dar suporte à interação do usuário com uma notificação de usuário, ações personalizadas deve ser criadas, registradas no sistema e anexadas à notificação antes de ele é agendado com o sistema. A extensão de conteúdo de notificação será chamada para manipular o processamento dessas ações. Consulte a [trabalhar com ações de notificação](~/ios/platform/user-notifications/enhanced-user-notifications.md) seção o [notificações do usuário aprimorada](~/ios/platform/user-notifications/enhanced-user-notifications.md) documento para obter mais detalhes sobre ações personalizadas.

Quando uma notificação de usuário com uma interface do usuário personalizada é apresentada ao usuário, ele terá os seguintes elementos:

[![](advanced-user-notifications-images/customui01.png "Uma notificação de usuário com um elementos personalizados da interface do usuário")](advanced-user-notifications-images/customui01.png#lightbox)

Se o usuário interage com as ações personalizadas (apresentada abaixo a notificação), a Interface do usuário pode ser atualizada para fornecer os comentários do usuário como o que aconteceu quando invocados uma determinada ação.

### <a name="adding-a-notification-content-extension"></a>Adicionando uma extensão de conteúdo de notificação

Para implementar uma interface de usuário de notificação de usuário personalizada em um aplicativo xamarin. IOS, faça o seguinte:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Abra a solução do aplicativo no Visual Studio para Mac.
2. Clique com botão direito no nome da solução nos **painel de soluções** e selecione **Add** > **adicionar novo projeto**.
3. Selecione **iOS** > **extensões** > **extensões de conteúdo de notificação** e clique no **próxima** botão: 

    [![](advanced-user-notifications-images/notify01.png "Selecionar extensões de conteúdo de notificação")](advanced-user-notifications-images/notify01.png#lightbox)
4. Insira um **nome** para a extensão e clique no **próxima** botão: 

    [![](advanced-user-notifications-images/notify02.png "Insira um nome para a extensão")](advanced-user-notifications-images/notify02.png#lightbox)
5. Ajustar a **nome do projeto** e/ou **nome da solução** se necessário e clique no **criar** botão: 

    [![](advanced-user-notifications-images/notify03.png "Ajustar o nome do projeto e/ou o nome da solução")](advanced-user-notifications-images/notify03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Abra a solução do aplicativo no Visual Studio para Mac.
2. Clique com botão direito no nome da solução nos **Gerenciador de soluções** e selecione **Adicionar > Novo projeto...** .
3. Selecione **Visual C# > extensões do iOS > extensão de conteúdo de notificação**:

    [![](advanced-user-notifications-images/notify01.w157-sml.png "Selecionar extensões de conteúdo de notificação")](advanced-user-notifications-images/notify01.w157.png#lightbox)
4. Insira um **nome** para a extensão e clique no **Okey** botão.

-----

Quando a extensão de conteúdo de notificação é adicionada à solução, três arquivos serão criados no projeto da extensão:

1. `NotificationViewController.cs` -Este é o controlador de exibição principal para a extensão de conteúdo de notificação.
2. `MainInterface.storyboard` -Em que o desenvolvedor apresenta a interface do usuário visível para a extensão de conteúdo de notificação no Designer do iOS.
3. `Info.plist` -Controla a configuração da extensão de conteúdo de notificação.

O padrão `NotificationViewController.cs` arquivo é semelhante ao seguinte:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;


namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

        }
        #endregion
    }
}
```

O `DidReceiveNotification` método é chamado quando a notificação é expandida pelo usuário para que a extensão de conteúdo de notificação pode preencher a interface do usuário personalizada com o conteúdo do `UNNotification`. No exemplo acima, foi adicionado um rótulo para o modo de exibição, exposto ao código com o nome `label` e é usado para exibir o corpo da notificação.

### <a name="setting-the-notification-content-extensions-categories"></a>Definindo categorias da extensão de conteúdo de notificação

O sistema precisa ser informado sobre como localizar a extensão de conteúdo de notificação do aplicativo com base em categorias específicas que ele responda às. Faça o seguinte:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Clique duas vezes a extensão `Info.plist` arquivo o **painel de soluções** para abri-lo para edição.
2. Alterne para o **origem** modo de exibição.
3. Expanda o `NSExtension` chave.
4. Adicione a `UNNotificationExtensionCategory` chave como tipo **cadeia de caracteres** com o valor da categoria de extensão pertence a (neste exemplo ' convite de evento): 

    [![](advanced-user-notifications-images/customui02.png "Adicionar a chave UNNotificationExtensionCategory")](advanced-user-notifications-images/customui02.png#lightbox)
5. Salve as alterações.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Clique duas vezes a extensão `Info.plist` arquivo o **Gerenciador de soluções** para abri-lo para edição.
3. Expanda o `NSExtension` chave.
4. Adicione a `UNNotificationExtensionCategory` chave como tipo **cadeia de caracteres** com o valor da categoria de extensão pertence a (neste exemplo ' convite de evento): 

    [![](advanced-user-notifications-images/customui02w.png "Adicionar a chave UNNotificationExtensionCategory")](advanced-user-notifications-images/customui02w.png#lightbox)
5. Salve as alterações.

-----

Categorias de extensão de conteúdo de notificação (`UNNotificationExtensionCategory`) usam os mesmos valores de categoria são usados para registrar as ações de notificação. A situação em que o aplicativo usará a mesma interface do usuário para várias categorias, alterne o `UNNotificationExtensionCategory` para o tipo **matriz** e forneça todas as categorias necessárias. Por exemplo:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](advanced-user-notifications-images/customui03.png "Categorias de extensão de conteúdo de notificação")](advanced-user-notifications-images/customui03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](advanced-user-notifications-images/customui03w.png "Categorias de extensão de conteúdo de notificação")](advanced-user-notifications-images/customui03w.png#lightbox)

-----

### <a name="hiding-the-default-notification-content"></a>Ocultar o conteúdo da notificação padrão

A situação em que a interface do usuário de notificação personalizada exibirá o mesmo conteúdo como o padrão de notificação (título, subtítulo e corpo automaticamente exibida na parte inferior da interface do usuário de notificação), essas informações padrão podem ser ocultadas, adicionando a `UNNotificationExtensionDefaultContentHidden`principais para o `NSExtensionAttributes` chave como tipo **booliano** com um valor de `YES` na extensão de `Info.plist` arquivo:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](advanced-user-notifications-images/customui04.png "Localizando informações sobre o padrão")](advanced-user-notifications-images/customui04.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](advanced-user-notifications-images/customui04w.png "Localizando informações sobre o padrão")](advanced-user-notifications-images/customui04w.png#lightbox)

-----

### <a name="designing-the-custom-ui"></a>Projetando a interface do usuário personalizada

Para criar a interface do usuário personalizada da extensão de conteúdo de notificação, clique duas vezes o `MainInterface.storyboard` arquivo para abri-lo para edição no iOS Designer, arraste os elementos que você precisa para criar a interface desejada (como `UILabels` e `UIImageViews`).

> [!NOTE]
> A partir de 12 de iOS, uma extensão de conteúdo de notificação pode incluir controles interativos, como botões e campos de texto. Para obter mais informações, consulte o [notificações interativas no iOS 12](~/ios/platform/introduction-to-ios12/notifications/interactive.md) documentação.

Depois que a interface do usuário foram disposto e os controles necessários exposto a C# código, abra o `NotificationViewController.cs` para edição e modifique o `DidReceiveNotification` para popular a interface do usuário quando o usuário expande a notificação. Por exemplo:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;


namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = content.UserInfo ["location"].ToString ();
            if (location != null) {
                Event.Location.Text = location;
            }

        }
        #endregion
    }
}
```

### <a name="setting-the-content-area-size"></a>Definindo o tamanho da área de conteúdo

Para ajustar o tamanho da área de conteúdo exibido para o usuário, o código a seguir é a configuração de `PreferredContentSize` propriedade no `ViewDidLoad` método para o tamanho desejado. Esse tamanho também pode ser ajustado por meio da aplicação de restrições para o modo de exibição no Designer do iOS, ela é deixada para o desenvolvedor para escolher o método que funciona melhor para eles.

Como começar a notificação de sistema já está em execução antes da notificação de extensão de conteúdo é invocado, a área de conteúdo completo em tamanho e ser animado até o tamanho solicitado quando apresentado ao usuário.

Para eliminar esse efeito, edite o `Info.plist` arquivo para a extensão e defina o `UNNotificationExtensionInitialContentSizeRatio` chave da `NSExtensionAttributes` chave para o tipo **número** com um valor que representa a taxa de desejado. Por exemplo:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](advanced-user-notifications-images/customui05.png "A chave UNNotificationExtensionInitialContentSizeRatio")](advanced-user-notifications-images/customui05.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](advanced-user-notifications-images/customui05w.png "A chave UNNotificationExtensionInitialContentSizeRatio")](advanced-user-notifications-images/customui05w.png#lightbox)

-----

### <a name="using-media-attachments-in-custom-ui"></a>Usando anexos de mídia na interface do usuário personalizada

Como anexos de mídia (como visto na [adicionar anexos de mídia](#adding-media-attachments) seção acima) fazem parte da carga de notificação, pode ser acessadas e exibidos na extensão de conteúdo de notificação assim como seriam no padrão Notificação de interface do usuário.

Por exemplo, se a interface de usuário personalizada acima incluído um `UIImageView` que foi exposto a C# de código, o seguinte código pode ser usado para preenchê-lo com o anexo de mídia:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;

namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = content.UserInfo ["location"].ToString ();
            if (location != null) {
                Event.Location.Text = location;
            }

            // Get Media Attachment
            if (content.Attachements.Length > 1) {
                var attachment = content.Attachments [0];
                if (attachment.Url.StartAccessingSecurityScopedResource ()) {
                    EventImage.Image = UIImage.FromFile (attachment.Url.Path);
                    attachment.Url.StopAccessingSecurityScopedResource ();
                }
            }
        }
        #endregion
    }
}
```

Como o anexo de mídia é gerenciado pelo sistema, ele é fora da área de segurança do aplicativo. A extensão deve informar o sistema que ele deseja acesso ao arquivo chamando o `StartAccessingSecurityScopedResource` método. Quando a extensão é feita com o arquivo, ele precisa chamar o `StopAccessingSecurityScopedResource` para liberar sua conexão.

### <a name="adding-custom-actions-to-a-custom-ui"></a>Adicionar ações personalizadas para uma interface do usuário personalizada

Botões de ação personalizada podem ser usados para adicionar interatividade a uma interface do usuário de notificação personalizada. Consulte a [trabalhar com ações de notificação](~/ios/platform/user-notifications/enhanced-user-notifications.md) seção o [notificações do usuário aprimorada](~/ios/platform/user-notifications/enhanced-user-notifications.md) documento para obter mais detalhes sobre ações personalizadas.

Além de ações personalizadas, a extensão de conteúdo de notificação pode responder a também as seguintes ações internas:

- **Ação padrão** -isso é quando o usuário toca uma notificação para abrir o aplicativo e exibir os detalhes da notificação determinado.
- **Ação de ignorar** – essa ação é enviada para o aplicativo quando o usuário o descarte de uma determinada notificação.

Extensões de conteúdo de notificação também tem a capacidade de atualizar sua interface do usuário quando o usuário chama uma das ações personalizadas, como mostrar uma data como aceita quando o usuário toca a **Accept** botão de ação personalizada. Além disso, as extensões de conteúdo de notificação pode informar ao sistema para atrasar a exoneração da interface do usuário de notificação para que o usuário possa ver o efeito de sua ação antes que a notificação seja fechada.

Isso é feito com a implementação de uma segunda versão do `DidReceiveNotification` método que inclui um manipulador de conclusão. Por exemplo:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;
using CoreGraphics;

namespace myApp {
    public class NotificationViewController : UIViewController, UNNotificationContentExtension {

        public override void ViewDidLoad() {
            base.ViewDidLoad();

            // Adjust the size of the content area
            var size = View.Bounds.Size
            PreferredContentSize = new CGSize(size.Width, size.Width/2);
        }

        public void DidReceiveNotification(UNNotification notification) {

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = Content.UserInfo["location"] as string;
            if (location != null) {
                Event.Location.Text = location;
            }

            // Get Media Attachment
            if (content.Attachements.Length > 1) {
                var attachment = content.Attachments[0];
                if (attachment.Url.StartAccessingSecurityScopedResource()) {
                    EventImage.Image = UIImage.FromFile(attachment.Url.Path);
                    attachment.Url.StopAccessingSecurityScopedResource();
                }
            }
        }

        [Export ("didReceiveNotificationResponse:completionHandler:")]
        public void DidReceiveNotification (UNNotificationResponse response, Action<UNNotificationContentExtensionResponseOption> completionHandler)
        {

            // Update UI when the user interacts with the
            // Notification
            Server.PostEventResponse += (response) {
                // Take action based on the response
                switch(response.ActionIdentifier){
                case "accept":
                    EventResponse.Text = "Going!";
                    EventResponse.TextColor = UIColor.Green;
                    break;
                case "decline":
                    EventResponse.Text = "Not Going.";
                    EventResponse.TextColor = UIColor.Red;
                    break;
                }

                // Close Notification
                completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
            };
        }
    }
}
```

Adicionando o `Server.PostEventResponse` manipulador para o `DidReceiveNotification` método o conteúdo da extensão de notificação, a extensão *deve* lidar com todas as ações personalizadas. A extensão também pode encaminhar as ações personalizadas para o aplicativo contém alterando o `UNNotificationContentExtensionResponseOption`. Por exemplo:

```csharp
// Close Notification
completionHandler (UNNotificationContentExtensionResponseOption.DismissAndForwardAction);
```

### <a name="working-with-the-text-input-action-in-custom-ui"></a>Trabalhando com a ação de entrada de texto na interface do usuário personalizada

Dependendo do design do aplicativo e a notificação, pode haver vezes em que exigem que o usuário insira texto para a notificação (como responder a uma mensagem). Uma extensão de conteúdo de notificação tem acesso à ação de entrada de texto interno exatamente como faz uma notificação padrão.

Por exemplo:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;


namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Computed Properties
        // Allow to take input
        public override bool CanBecomeFirstResponder {
            get { return true; }
        }

        // Return the custom created text input view with the
        // required buttons and return here
        public override UIView InputAccessoryView {
            get { return InputView; }
        }
        #endregion

        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Private Methods
        private UNNotificationCategory MakeExtensionCategory ()
        {

            // Create Accept Action
            ...

            // Create decline Action
            ...

            // Create Text Input Action
            var commentID = "comment";
            var commentTitle = "Comment";
            var textInputButtonTitle = "Send";
            var textInputPlaceholder = "Enter comment here...";
            var commentAction = UNTextInputNotificationAction.FromIdentifier (commentID, commentTitle, UNNotificationActionOptions.None, textInputButtonTitle, textInputPlaceholder);

            // Create category
            var categoryID = "event-invite";
            var actions = new UNNotificationAction [] { acceptAction, declineAction, commentAction };
            var intentIDs = new string [] { };
            var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.None);

            // Return new category
            return category;

        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = content.UserInfo ["location"].ToString ();
            if (location != null) {
                Event.Location.Text = location;
            }

            // Get Media Attachment
            if (content.Attachements.Length > 1) {
                var attachment = content.Attachments [0];
                if (attachment.Url.StartAccessingSecurityScopedResource ()) {
                    EventImage.Image = UIImage.FromFile (attachment.Url.Path);
                    attachment.Url.StopAccessingSecurityScopedResource ();
                }
            }
        }

        [Export ("didReceiveNotificationResponse:completionHandler:")]
        public void DidReceiveNotification (UNNotificationResponse response, Action<UNNotificationContentExtensionResponseOption> completionHandler)
        {

            // Is text input?
            if (response is UNTextInputNotificationResponse) {
                var textResponse = response as UNTextInputNotificationResponse;
                Server.Send (textResponse.UserText, () => {
                    // Close Notification
                    completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
                });
            }

            // Update UI when the user interacts with the
            // Notification
            Server.PostEventResponse += (response) {
                // Take action based on the response
                switch (response.ActionIdentifier) {
                case "accept":
                    EventResponse.Text = "Going!";
                    EventResponse.TextColor = UIColor.Green;
                    break;
                case "decline":
                    EventResponse.Text = "Not Going.";
                    EventResponse.TextColor = UIColor.Red;
                    break;
                }

                // Close Notification
                completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
            };
        }
        #endregion
    }
}
```

Esse código cria uma nova ação de entrada de texto e o adiciona à categoria de extensão (no `MakeExtensionCategory`) método. No `DidReceive` substituir o método, ele lida com o usuário insira texto com o código a seguir:

```csharp
// Is text input?
if (response is UNTextInputNotificationResponse) {
    var textResponse = response as UNTextInputNotificationResponse;
    Server.Send (textResponse.UserText, () => {
        // Close Notification
        completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
    });
}
```

Se o design chama para adicionar botões personalizados para o campo de entrada de texto, adicione o seguinte código para incluí-los:

```csharp
// Allow to take input
public override bool CanBecomeFirstResponder {
    get {return true;}
}

// Return the custom created text input view with the
// required buttons and return here
public override UIView InputAccessoryView {
    get {return InputView;}
}
```

Quando a ação de comentário é disparada pelo usuário, o controlador de exibição e o campo de entrada de texto personalizado precisam ser ativados:

```csharp
// Update UI when the user interacts with the
// Notification
Server.PostEventResponse += (response) {
    // Take action based on the response
    switch(response.ActionIdentifier){
    ...
    case "comment":
        BecomeFirstResponder();
        TextField.BecomeFirstResponder();
        break;
    }

    // Close Notification
    completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);

};
```

## <a name="summary"></a>Resumo

Este artigo apresentou uma visão avançada usando a nova estrutura de notificação do usuário em um aplicativo xamarin. IOS. Ele abordou a adição de anexos de mídia para o Local e remoto de notificação e coberto usando a nova IU de notificação do usuário para criar interfaces do usuário personalizadas de notificação.

## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Referência de estrutura Usernotifications](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [Guia de programação de notificação local e remota](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/Introduction.html)
