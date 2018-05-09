---
title: Notificações de usuário avançado
description: Este artigo usa uma análise mais profunda sobre a nova estrutura de notificações de usuário e como aproveitar ao máximo-lo em um aplicativo xamarin.
ms.prod: xamarin
ms.assetid: 4E0C60AE-6F54-4098-8FA0-AADF9AC86805
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/03/2018
ms.openlocfilehash: bd8a95afc5bdd5aed958913d63f9b6cfe853677e
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="advanced-user-notifications"></a>Notificações de usuário avançado

_Este artigo usa uma análise mais profunda sobre a nova estrutura de notificações de usuário e como aproveitar ao máximo-lo em um aplicativo xamarin._

Novo para o iOS 10, a notificação do usuário framework permite a entrega e a manipulação de notificações de locais e remotas. Usando essa estrutura, um aplicativo ou extensão do aplicativo pode agendar a entrega de notificações locais, especificando um conjunto de condições, como local ou a hora do dia.

## <a name="about-user-notifications"></a>Sobre notificações de usuário

Permite que a nova estrutura de notificação do usuário para a entrega e a manipulação de notificações de locais e remotas. Usando essa estrutura, um aplicativo ou extensão do aplicativo pode agendar a entrega de notificações locais, especificando um conjunto de condições, como local ou a hora do dia.

Além disso, o aplicativo ou extensão pode receber (e potencialmente modificar) locais e remotas notificações como elas são entregues para o dispositivo iOS do usuário.

A nova estrutura de interface do usuário de notificação de usuário permite que um aplicativo ou extensão do aplicativo para personalizar a aparência de notificações de locais e remotas quando eles são apresentados ao usuário.

Essa estrutura fornece as seguintes maneiras em que um aplicativo pode fornecer notificações a um usuário:

- **Alertas Visual** - onde a notificação rola para baixo da parte superior da tela como uma faixa.
- **Som e vibrações** -pode ser associado uma notificação.
- **Notificações de ícone do aplicativo** - onde o ícone do aplicativo exibe uma notificação mostrando que o novo conteúdo está disponível. Como o número de mensagens de email não lido.

Além disso, dependendo do contexto do usuário atual, há maneiras diferentes de uma notificação será exibida:

- Se o dispositivo é bloqueado, a notificação será rolar para baixo da parte superior da tela como uma faixa.
- Se o dispositivo estiver bloqueado, a notificação será exibida na tela de bloqueio do usuário.
- Se o usuário foi perdido uma notificação, podem abrir o Centro de notificação e exibir qualquer disponível, notificações de espera.

Um aplicativo xamarin tem dois tipos de notificações de usuário que é capaz de enviar:

- **Notificações locais** -esses são enviados por aplicativos instalados localmente no dispositivo do usuário.
- **Notificações remotas** - enviadas de um servidor remoto e o apresentadas ao usuário ou dispara uma atualização em segundo plano do conteúdo do aplicativo.

Para obter mais informações, consulte nosso [notificações de usuário aprimorada](~/ios/platform/user-notifications/enhanced-user-notifications.md) documentação.

## <a name="the-new-user-notification-interface"></a>A nova Interface de notificação do usuário

Notificações de usuário no iOS 10 são apresentadas com um novo design de interface do usuário que fornece mais conteúdo, como um título, subtítulo e anexos de mídia opcional que pode ser apresentada na tela de bloqueio, como uma faixa na parte superior do dispositivo ou no Centro de notificação.

Não importa onde a notificação do usuário é exibida em iOS 10, ela é apresentada com a mesma aparência e com os mesmos recursos e funcionalidade.

No iOS 8, a Apple introduziu acionáveis notificações onde o desenvolvedor pode anexar ações personalizadas para uma notificação e permitir que o usuário agir em uma notificação sem a necessidade de iniciar o aplicativo. No iOS 9, Apple aprimorado acionáveis notificações com resposta rápida que permite ao usuário responder a uma notificação com entrada de texto.

Como notificações de usuário são uma parte integrante mais a experiência do usuário no iOS 10, Apple mais expandiu acionáveis notificações para dar suporte ao 3D Touch, onde o usuário pressiona uma notificação de e uma interface de usuário personalizada é a exibição para fornecer interação avançada com a notificação.

Quando a interface de usuário de notificação do usuário personalizada é exibida, se o usuário interage com qualquer ação anexada à notificação, a interface do usuário personalizada pode ser atualizado imediatamente para fornecer comentários sobre o que foi alterado.

Novo para iOS 10, a API de interface do usuário de notificação do usuário permite que um aplicativo xamarin facilmente aproveitar esses novos recursos da interface do usuário de notificação do usuário.

## <a name="adding-media-attachments"></a>Adicionar anexos de mídia

Um dos itens mais comuns que são compartilhados entre os usuários é fotos, para que iOS 10 adicionadas a capacidade de anexar um Item de mídia (por exemplo, uma foto) diretamente para uma notificação, onde ele será apresentado e prontamente disponíveis para o usuário junto com o restante do Conté da notificação NT.

No entanto, os tamanhos envolvidos no envio devido a até mesmo uma imagem pequena, anexá-lo a uma carga de notificação remoto será impraticável. Para lidar com essa situação, o desenvolvedor pode usar a nova extensão de serviço no iOS 10 para baixar a imagem de outra origem (como um repositório de dados CloudKit) e anexá-lo para o conteúdo da notificação antes de ser exibido para o usuário.

Para uma notificação remoto a ser modificado por uma extensão de serviço, sua carga deve ser marcada como mutável. Por exemplo:

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

Depois que a notificação remoto é entregue ao dispositivo (por meio de APNs), a extensão do serviço, em seguida, pode baixar a imagem necessária por meio de qualquer meio desejado (como uma `NSURLSession`) e depois de receber a imagem, ele pode modificar o conteúdo da notificação e exibir -o para o usuário.

Este é um exemplo de como esse processo pode ser manipulado no código:

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

Quando a notificação é recebida de APNs, o endereço personalizado da imagem é leitura do conteúdo e o arquivo é baixado do servidor. Em seguida, um `UNNotificationAttachement` é criado com uma ID exclusiva e o local da imagem (como um `NSUrl`). Uma cópia mutável de conteúdo a notificação é criada e os anexos de mídia são adicionados. Por fim, a notificação é exibida para o usuário chamando o `contentHandler`.

Após a adição de um anexo em uma notificação, o sistema assumirá a movimentação e o gerenciamento do arquivo.

As notificações remoto apresentada acima, além de anexos de mídia também têm suporte das notificações Local, onde o `UNNotificationAttachement` é criado e anexado à notificação junto com seu conteúdo.

Suporte a notificação no iOS 10 anexos de mídia de imagens (estático e GIFs), áudio, vídeo e o sistema exibirá automaticamente a interface do usuário personalizada correto para cada um desses tipos de anexos quando a notificação é apresentada ao usuário.

> [!NOTE]
> Tome cuidado para otimizar o tamanho do media e o tempo necessário para baixar a mídia do servidor remoto (ou para montar a mídia para notificações Local) como o sistema impõe limites rígidos tanto ao executar extensão de serviço do aplicativo. Por exemplo, considere a possibilidade de enviar uma versão reduzida da imagem ou um pequeno clipe de um vídeo para ser apresentado na notificação.

## <a name="creating-custom-user-interfaces"></a>Criando Interfaces de usuário personalizada

Para criar uma Interface de usuário personalizada para suas notificações de usuário, o desenvolvedor precisa adicionar uma extensão de conteúdo de notificação (novo para o iOS 10) à solução do aplicativo.

A extensão de conteúdo de notificação permite que o desenvolvedor adicionar seus próprios modos de exibição para a interface do usuário de notificação e desenhar qualquer conteúdo que desejarem. No entanto, os widgets de interface do usuário interativas (como botões ou campos de texto) são **não** com suporte a interface do usuário de notificação e nunca deve ser adicionado a um design de interface do usuário personalizado.

Para dar suporte à interação do usuário com a notificação do usuário, ações personalizadas deve ser criadas, registradas com o sistema e anexadas à notificação antes que ela esteja agendada com o sistema. A extensão de conteúdo de notificação será chamada para manipular o processamento dessas ações. Consulte o [trabalhando com ações de notificação](~/ios/platform/user-notifications/enhanced-user-notifications.md) seção o [notificações de usuário aprimorada](~/ios/platform/user-notifications/enhanced-user-notifications.md) documento para obter mais detalhes sobre as ações personalizadas.

Quando uma notificação de usuário com uma interface de usuário personalizada é apresentada ao usuário, ele terá os seguintes elementos:

[![](advanced-user-notifications-images/customui01.png "A notificação do usuário com um elemento de interface de usuário personalizada")](advanced-user-notifications-images/customui01.png#lightbox)

Se o usuário interage com as ações personalizadas (apresentados a seguir, a notificação), a Interface do usuário pode ser atualizada para fornecer os comentários de usuário como o que aconteceu quando invocada a uma determinada ação.

### <a name="adding-a-notification-content-extension"></a>Adicionar uma extensão de conteúdo de notificação

Para implementar uma interface de usuário de notificação de usuário personalizada em um aplicativo xamarin, faça o seguinte:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Abra a solução do aplicativo no Visual Studio para Mac.
2. Com o botão direito no nome da solução no **solução preenchimento** e selecione **adicionar** > **adicionar novo projeto**.
3. Selecione **iOS** > **extensões** > **extensões de conteúdo de notificação** e clique no **próximo** botão: 

    [![](advanced-user-notifications-images/notify01.png "Selecione as extensões de conteúdo de notificação")](advanced-user-notifications-images/notify01.png#lightbox)
4. Insira um **nome** para a extensão e clique no **próximo** botão: 

    [![](advanced-user-notifications-images/notify02.png "Insira um nome para a extensão")](advanced-user-notifications-images/notify02.png#lightbox)
5. Ajustar o **nome do projeto** e/ou **nome da solução** se necessário e clique no **criar** botão: 

    [![](advanced-user-notifications-images/notify03.png "Ajustar o nome do projeto e/ou o nome da solução")](advanced-user-notifications-images/notify03.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Abra a solução do aplicativo no Visual Studio para Mac.
2. Clique com botão direito no nome da solução no **Solution Explorer** e selecione **Adicionar > Novo projeto...** .
3. Selecione **Visual C# > extensões do iOS > extensão de conteúdo de notificação**:

    [![](advanced-user-notifications-images/notify01.w157-sml.png "Selecione as extensões de conteúdo de notificação")](advanced-user-notifications-images/notify01.w157.png#lightbox)
4. Insira um **nome** para a extensão e clique no **Okey** botão.

-----

Quando a extensão de conteúdo de notificação é adicionada à solução, três arquivos serão criados no projeto de extensão:

1. `NotificationViewController.cs` -Este é o controlador de modo de exibição principal para a extensão de conteúdo de notificação.
2. `MainInterface.storyboard` -Em que o desenvolvedor apresenta a interface do usuário visível para a extensão de conteúdo de notificação no Designer de iOS.
3. `Info.plist` -Controla a configuração da extensão de conteúdo de notificação.

O padrão `NotificationViewController.cs` arquivo parecido com o seguinte:

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

O `DidReceiveNotification` método é chamado quando a notificação é expandida pelo usuário para que a extensão de conteúdo de notificação pode preencher a interface do usuário personalizada com o conteúdo a `UNNotification`. No exemplo acima, foi adicionado um rótulo para a exibição, exposta ao código com o nome `label` e é usado para exibir o corpo da notificação.

### <a name="setting-the-notification-content-extensions-categories"></a>Configuração categorias da extensão de conteúdo notificação

O sistema precisa ser informado sobre como localizar a extensão de conteúdo de notificação do aplicativo com base em categorias específicas que ele responda às. Faça o seguinte:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Clique duas vezes a extensão `Info.plist` de arquivos no **solução preenchimento** para abri-lo para edição.
2. Alterne para o **fonte** exibição.
3. Expanda o `NSExtension` chave.
4. Adicionar o `UNNotificationExtensionCategory` chave como tipo **cadeia de caracteres** com o valor da categoria de extensão pertence (neste exemplo ' evento convite): 

    [![](advanced-user-notifications-images/customui02.png "Adicionar a chave UNNotificationExtensionCategory")](advanced-user-notifications-images/customui02.png#lightbox)
5. Salve as alterações.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Clique duas vezes a extensão `Info.plist` arquivo o **Solution Explorer** para abri-lo para edição.
3. Expanda o `NSExtension` chave.
4. Adicionar o `UNNotificationExtensionCategory` chave como tipo **cadeia de caracteres** com o valor da categoria de extensão pertence (neste exemplo ' evento convite): 

    [![](advanced-user-notifications-images/customui02w.png "Adicionar a chave UNNotificationExtensionCategory")](advanced-user-notifications-images/customui02w.png#lightbox)
5. Salve as alterações.

-----

Categorias de extensão de conteúdo de notificação (`UNNotificationExtensionCategory`) usam os mesmos valores de categoria são usados para registrar ações de notificação. Em uma situação em que o aplicativo usará a mesma interface do usuário para várias categorias, alterne o `UNNotificationExtensionCategory` para o tipo **matriz** e forneça todas as categorias necessárias. Por exemplo:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![](advanced-user-notifications-images/customui03.png "Categorias de extensão de conteúdo de notificação")](advanced-user-notifications-images/customui03.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](advanced-user-notifications-images/customui03w.png "Categorias de extensão de conteúdo de notificação")](advanced-user-notifications-images/customui03w.png#lightbox)

-----

### <a name="hiding-the-default-notification-content"></a>Ocultando o conteúdo padrão da notificação

Em uma situação em que a interface do usuário de notificação personalizada exibirá o mesmo conteúdo como o padrão de notificação (título, subtítulo e corpo automaticamente exibida na parte inferior da interface do usuário de notificação), essas informações padrão podem ser ocultadas, adicionando o `UNNotificationExtensionDefaultContentHidden`chave para o `NSExtensionAttributes` chave como tipo **booliano** com um valor de `YES` na extensão de `Info.plist` arquivo:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![](advanced-user-notifications-images/customui04.png "Localizando informações sobre o padrão")](advanced-user-notifications-images/customui04.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](advanced-user-notifications-images/customui04w.png "Localizando informações sobre o padrão")](advanced-user-notifications-images/customui04w.png#lightbox)

-----

### <a name="designing-the-custom-ui"></a>Criando a interface do usuário personalizada

Para criar a interface do usuário personalizada da extensão de conteúdo de notificação, clique duas vezes o `MainInterface.storyboard` arquivo para abri-lo para edição no iOS Designer, arraste os elementos que você precisa criar a interface desejada (como `UILabels` e `UIImageViews`).

> [!NOTE]
> A interface do usuário de notificação _não_ suporte controles interativos, como campos de texto ou botões em uma extensão de conteúdo de notificação. Enquanto eles podem ser adicionados para o storyboard, o usuário não poderá interagir com eles. Para adicionar a interação do usuário para uma interface de usuário de notificação personalizada, use ações personalizadas.

Depois que a interface do usuário foi disposto e os controles necessários expostos ao código c#, abra o `NotificationViewController.cs` para edição e modificar o `DidReceiveNotification` para popular a interface do usuário quando o usuário expande a notificação. Por exemplo:

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

Para ajustar o tamanho da área de conteúdo exibido para o usuário, o código a seguir é definir o `PreferredContentSize` propriedade o `ViewDidLoad` método para o tamanho desejado. Esse tamanho também pode ser ajustado aplicando restrições para o modo de exibição no Designer do iOS, ele será deixado ao desenvolvedor para escolher o método que funciona melhor para eles.

Porque a sistema já está em execução antes da notificação de extensão de conteúdo é chamado, a área de conteúdo de notificação inicialmente completo em tamanho e animado até o tamanho solicitado quando apresentadas ao usuário.

Para eliminar esse efeito, edite o `Info.plist` arquivo para a extensão e defina o `UNNotificationExtensionInitialContentSizeRatio` chave do `NSExtensionAttributes` chave para o tipo **número** com um valor que representa a taxa desejada. Por exemplo:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![](advanced-user-notifications-images/customui05.png "A chave UNNotificationExtensionInitialContentSizeRatio")](advanced-user-notifications-images/customui05.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](advanced-user-notifications-images/customui05w.png "A chave UNNotificationExtensionInitialContentSizeRatio")](advanced-user-notifications-images/customui05w.png#lightbox)

-----

### <a name="using-media-attachments-in-custom-ui"></a>Usando mídia anexos em personalizado da interface do usuário

Como anexos de mídia (como visto no [adicionar anexos de mídia](#Adding-Media-Attachments) seção acima) são parte da carga de notificação, eles podem ser acessados e exibidos na extensão de conteúdo de notificação, assim como seriam no padrão Notificação de interface do usuário.

Por exemplo, se a interface de usuário personalizada acima incluído um `UIImageView` que foi exposta ao código c#, o código a seguir pode ser usado para preenchê-la de com o anexo de mídia:

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

Como o anexo de mídia é gerenciado pelo sistema, é fora da área de segurança do aplicativo. A extensão precisa informar ao sistema que ele deseja acessar o arquivo chamando o `StartAccessingSecurityScopedResource` método. Quando a extensão é feita com o arquivo, é necessário chamar o `StopAccessingSecurityScopedResource` para liberar sua conexão.

### <a name="adding-custom-actions-to-a-custom-ui"></a>Adicionar ações personalizadas para uma interface de usuário personalizada

Como mencionado acima, a interface do usuário de notificação não oferece suporte a interação do usuário para que controles, como botões ou campos de texto não são suportados no design da interface do usuário.

Em vez disso, o mecanismo de ações personalizadas padrão é usado para adicionar interatividade a uma interface de usuário de notificação personalizada. Consulte o [trabalhando com ações de notificação](~/ios/platform/user-notifications/enhanced-user-notifications.md) seção o [notificações de usuário aprimorada](~/ios/platform/user-notifications/enhanced-user-notifications.md) documento para obter mais detalhes sobre as ações personalizadas.

Além de ações personalizadas, a extensão de conteúdo de notificação pode responder a também as seguintes ações internas:

- **Ação padrão** -isso é quando o usuário toca uma notificação para abrir o aplicativo e exibir os detalhes da notificação de determinado.
- **Ação de ignorar** -esta ação é enviada ao aplicativo quando o usuário ignorar uma notificação determinada.

Extensões de conteúdo de notificação também tem a capacidade de atualizar sua interface do usuário quando o usuário chama uma das ações personalizadas, como mostrando uma data que aceita quando o usuário toca o **aceitar** botão de ação personalizada. Além disso, as extensões de conteúdo de notificação pode informar ao sistema para atrasar a demissão da interface do usuário de notificação para que o usuário possa ver o efeito de suas ações antes que a notificação seja fechada.

Isso é feito com a implementação de uma segunda versão o `DidReceiveNotification` método que inclui um processador de conclusão. Por exemplo:

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

Adicionando o `Server.PostEventResponse` manipulador para o `DidReceiveNotification` método de extensão conteúdo notificação, a extensão *deve* lidar com todas as ações personalizadas. A extensão também pode encaminhar as ações personalizadas para o aplicativo que contém alterando o `UNNotificationContentExtensionResponseOption`. Por exemplo:

```csharp
// Close Notification
completionHandler (UNNotificationContentExtensionResponseOption.DismissAndForwardAction);
```

### <a name="working-with-the-text-input-action-in-custom-ui"></a>Trabalhando com a ação de entrada de texto em personalizado da interface do usuário

Dependendo do design do aplicativo e a notificação, pode haver vezes em que exigem que o usuário insira texto para a notificação (como responder a uma mensagem). Uma extensão de conteúdo de notificação tem acesso à ação de entrada de texto interno apenas como uma notificação padrão.

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

Esse código cria uma nova ação de entrada de texto e o adiciona à categoria de extensão (no `MakeExtensionCategory`) método. No `DidReceive` substituir o método, ele trata o usuário inserir texto com o código a seguir:

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

Se o design de chamadas para adicionar botões personalizados para o campo de entrada de texto, adicione o seguinte código para incluí-los:

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

Quando a ação de comentário é disparada pelo usuário, o controlador de exibição e o campo de entrada de texto personalizados precisam ser ativados:

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

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo obteve uma olhada avançada usando a nova estrutura de notificação do usuário em um aplicativo xamarin. Ele coberto adicionar anexos de mídia para o Local e remoto de notificação e coberto usando a nova interface de notificação do usuário para criar interfaces de usuário de notificação personalizada.


## <a name="related-links"></a>Links relacionados

- [Exemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Referência UserNotifications Framework](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [Guia de programação de notificação de locais e remotos](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/Introduction.html)
