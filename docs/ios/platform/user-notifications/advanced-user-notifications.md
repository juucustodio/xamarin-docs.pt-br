---
title: Notificações de usuário avançadas no Xamarin. iOS
description: Este artigo analisa mais detalhadamente a estrutura de notificações do usuário, introduzida no iOS 10. Ele aborda as notificações do usuário, a interface de notificação do usuário, os anexos de mídia, as interfaces de usuário personalizadas e muito mais.
ms.prod: xamarin
ms.assetid: 4E0C60AE-6F54-4098-8FA0-AADF9AC86805
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/03/2018
ms.openlocfilehash: ce4614f7b21a42945a8541f2b18877e75a200f1a
ms.sourcegitcommit: f6a2f07d2e689e0cfd01b30008d50c83c63fa70c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89052767"
---
# <a name="advanced-user-notifications-in-xamarinios"></a>Notificações de usuário avançadas no Xamarin. iOS

Novo no iOS 10, a estrutura de notificação de usuário permite a entrega e a manipulação de notificações locais e remotas. Usando essa estrutura, uma extensão de aplicativo ou aplicativo pode agendar a entrega de notificações locais, especificando um conjunto de condições, como local ou hora do dia.

## <a name="about-user-notifications"></a>Sobre as notificações do usuário

A nova estrutura de notificação de usuário permite a entrega e a manipulação de notificações locais e remotas. Usando essa estrutura, uma extensão de aplicativo ou aplicativo pode agendar a entrega de notificações locais, especificando um conjunto de condições, como local ou hora do dia.

Além disso, o aplicativo ou a extensão pode receber (e potencialmente modificar) as notificações locais e remotas à medida que elas são entregues ao dispositivo iOS do usuário.

A nova estrutura de IU de notificação de usuário permite que uma extensão de aplicativo ou aplicativo Personalize a aparência de notificações locais e remotas quando elas são apresentadas ao usuário.

Essa estrutura fornece as seguintes maneiras em que um aplicativo pode entregar notificações a um usuário:

- **Alertas visuais** – onde a notificação é acumulada a partir da parte superior da tela como uma faixa.
- **Som e vibração** – podem ser associados a uma notificação.
- **Ícone do aplicativo notificações** -onde o ícone do aplicativo exibe uma notificação mostrando que o novo conteúdo está disponível. Como o número de mensagens de email não lidas.

Além disso, dependendo do contexto atual do usuário, há diferentes maneiras pelas quais uma notificação será apresentada:

- Se o dispositivo estiver desbloqueado, a notificação será acumulada a partir da parte superior da tela como uma faixa.
- Se o dispositivo estiver bloqueado, a notificação será exibida na tela de bloqueio do usuário.
- Se o usuário tiver perdido uma notificação, ele poderá abrir o centro de notificações e exibir todas as notificações disponíveis e aguardando.

Um aplicativo Xamarin. iOS tem dois tipos de notificações de usuário que ele pode enviar:

- **Notificações locais** – elas são enviadas por aplicativos instalados localmente no dispositivo de usuários.
- **Notificações remotas** – são enviadas de um servidor remoto e apresentadas ao usuário ou disparam uma atualização em segundo plano do conteúdo do aplicativo.

Para obter mais informações, consulte nossa documentação [avançada de notificações de usuário](~/ios/platform/user-notifications/enhanced-user-notifications.md) .

## <a name="the-new-user-notification-interface"></a>A nova interface de notificação de usuário

As notificações do usuário no iOS 10 são apresentadas com um novo design da interface do usuário que fornece mais conteúdo, como um título, subtítulo e anexos de mídia opcionais que podem ser apresentados na tela de bloqueio, como uma faixa na parte superior do dispositivo ou no centro de notificações.

Não importa onde uma notificação de usuário é exibida no iOS 10, ela é apresentada com a mesma aparência e com os mesmos recursos e funcionalidades.

No iOS 8, a Apple introduziu notificações acionáveis em que o desenvolvedor poderia anexar ações personalizadas a uma notificação e permitir que o usuário execute uma ação em uma notificação sem precisar iniciar o aplicativo. No iOS 9, a Apple aprimorou as notificações acionáveis com resposta rápida, que permite ao usuário responder a uma notificação com entrada de texto.

Como as notificações do usuário são uma parte mais integral da experiência do usuário no iOS 10, a Apple expandiu ainda mais as notificações acionáveis para dar suporte ao Touch 3D, onde o usuário pressiona uma notificação e uma interface de usuário personalizada é exibida para fornecer interação rica com a notificação.

Quando a interface do usuário de notificação de usuários personalizada é exibida, se o usuário interage com as ações anexadas à notificação, a interface do usuário personalizada pode ser atualizada instantaneamente para fornecer comentários sobre o que foi alterado.

Novo no iOS 10, a API da interface do usuário de notificação de usuários permite que um aplicativo Xamarin. iOS Aproveite facilmente esses novos recursos de IU de notificação do usuário.

## <a name="adding-media-attachments"></a>Adicionando anexos de mídia

Um dos itens mais comuns que são compartilhados entre os usuários são as fotos, portanto, o iOS 10 adicionou a capacidade de anexar um item de mídia (como uma foto) diretamente a uma notificação, onde ele será apresentado e prontamente disponível para o usuário junto com o restante do conteúdo da notificação.

No entanto, devido aos tamanhos envolvidos no envio de uma imagem pequena, anexá-la a uma carga de notificação remota torna-se impraticável. Para lidar com essa situação, o desenvolvedor pode usar a nova extensão de serviço no iOS 10 para baixar a imagem de outra fonte (como um armazenamento de dataCloudKit) e anexá-la ao conteúdo da notificação antes que ela seja exibida para o usuário.

Para que uma notificação remota seja modificada por uma extensão de serviço, sua carga deve ser marcada como mutável. Por exemplo:

```csharp
{
    aps : {
        alert : "New Photo Available",
        mutable-content: 1
    },
    my-attachment : "https://example.com/photo.jpg"
}
```

Veja a seguinte visão geral do processo:

[![Adicionando processo de anexos de mídia](advanced-user-notifications-images/extension02.png)](advanced-user-notifications-images/extension02.png#lightbox)

Depois que a notificação remota é entregue ao dispositivo (via APNs), a extensão de serviço pode então baixar a imagem necessária por meio de qualquer meio desejado (como um `NSURLSession` ) e, depois de receber a imagem, ela pode modificar o conteúdo da notificação e exibi-la para o usuário.

Veja a seguir um exemplo de como esse processo pode ser tratado no código:

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
        public NotificationService (IntPtr handle) : base(handle)
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

Quando a notificação é recebida do APNs, o endereço personalizado da imagem é lido do conteúdo e o arquivo é baixado do servidor. Em seguida, um `UNNotificationAttachement` é criado com uma ID exclusiva e a localização local da imagem (como um `NSUrl` ). Uma cópia mutável do conteúdo da notificação é criada e os anexos de mídia são adicionados. Por fim, a notificação é exibida para o usuário chamando o `contentHandler` .

Depois que um anexo tiver sido adicionado a uma notificação, o sistema assumirá a movimentação e o gerenciamento do arquivo.

Além das notificações remotas apresentadas acima, os anexos de mídia também têm suporte de notificações locais, em que o `UNNotificationAttachement` é criado e anexado à notificação junto com seu conteúdo.

A notificação no iOS 10 dá suporte a anexos de mídia de imagens (estáticos e GIFs), áudio ou vídeo, e o sistema exibirá automaticamente a interface do usuário personalizada correta para cada um desses tipos de anexos quando a notificação for apresentada a ele.

> [!NOTE]
> Deve-se ter cuidado para otimizar o tamanho da mídia e o tempo necessário para baixar a mídia do servidor remoto (ou para montar a mídia para notificações locais), pois o sistema impõe limites estritos para ambos ao executar a extensão de serviço do aplicativo. Por exemplo, considere enviar uma versão reduzida da imagem ou um pequeno clipe de um vídeo a ser apresentado na notificação.

## <a name="creating-custom-user-interfaces"></a>Criando interfaces de usuário personalizadas

Para criar uma interface do usuário personalizada para suas notificações de usuário, o desenvolvedor precisa adicionar uma extensão de conteúdo de notificação (nova no iOS 10) à solução do aplicativo.

A extensão de conteúdo de notificação permite que o desenvolvedor adicione suas próprias exibições à interface do usuário de notificação e desenhe qualquer conteúdo desejado. A partir do iOS 12, as extensões de conteúdo de notificação dão suporte a controles de interface do usuário interativa, como botões e controles deslizantes. Para obter mais informações, consulte as [notificações interativas na documentação do IOS 12](~/ios/platform/introduction-to-ios12/notifications/interactive.md) .

Para dar suporte à interação do usuário com uma notificação do usuário, as ações personalizadas devem ser criadas, registradas com o sistema e anexadas à notificação antes de serem agendadas com o sistema. A extensão de conteúdo de notificação será chamada para manipular o processamento dessas ações. Consulte a seção [trabalhando com ações de notificação](~/ios/platform/user-notifications/enhanced-user-notifications.md) do documento de [notificações de usuário aprimoradas](~/ios/platform/user-notifications/enhanced-user-notifications.md) para obter mais detalhes sobre as ações personalizadas.

Quando uma notificação de usuário com uma IU personalizada é apresentada ao usuário, ela terá os seguintes elementos:

[![Uma notificação de usuário com elementos personalizados da interface](advanced-user-notifications-images/customui01.png)](advanced-user-notifications-images/customui01.png#lightbox)

Se o usuário interage com as ações personalizadas (apresentadas abaixo da notificação), a interface do usuário pode ser atualizada para fornecer comentários ao usuário como o que aconteceu quando eles chamaram uma determinada ação.

### <a name="adding-a-notification-content-extension"></a>Adicionando uma extensão de conteúdo de notificação

Para implementar uma interface do usuário de notificação personalizada em um aplicativo Xamarin. iOS, faça o seguinte:

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Abra a solução do aplicativo no Visual Studio para Mac.
2. Clique com o botão direito do mouse no nome da solução na **painel de soluções** e selecione **Adicionar**  >  **Adicionar novo projeto**.
3. Selecione extensões do **Ios**  >  **Extensions**  >  **extensões de conteúdo de notificação** e clique no botão **Avançar** : 

    [![Selecionar extensões de conteúdo de notificação](advanced-user-notifications-images/notify01.png)](advanced-user-notifications-images/notify01.png#lightbox)
4. Insira um **nome** para a extensão e clique no botão **Avançar** : 

    [![Insira um nome para a extensão](advanced-user-notifications-images/notify02.png)](advanced-user-notifications-images/notify02.png#lightbox)
5. Ajuste o **nome do projeto** e/ou o **nome da solução** , se necessário, e clique no botão **criar** : 

    [![Ajustar o nome do projeto e/ou o nome da solução](advanced-user-notifications-images/notify03.png)](advanced-user-notifications-images/notify03.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Abra a solução do aplicativo no Visual Studio para Mac.
2. Clique com o botão direito do mouse no nome da solução na **Gerenciador de soluções** e selecione **Adicionar > novo projeto...**.
3. Selecione **as extensões do Visual C# > iOS > extensão de conteúdo de notificação**:

    [![Selecionar extensões de conteúdo de notificação](advanced-user-notifications-images/notify01.w157-sml.png)](advanced-user-notifications-images/notify01.w157.png#lightbox)
4. Insira um **nome** para a extensão e clique no botão **OK** .

-----

Quando a extensão de conteúdo de notificação for adicionada à solução, três arquivos serão criados no projeto da extensão:

1. `NotificationViewController.cs` -Este é o controlador de exibição principal para a extensão de conteúdo de notificação.
2. `MainInterface.storyboard` -Onde o desenvolvedor cria a interface do usuário visível para a extensão de conteúdo de notificação no designer do iOS.
3. `Info.plist` -Controla a configuração da extensão de conteúdo de notificação.

O `NotificationViewController.cs` arquivo padrão é semelhante ao seguinte:

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

O `DidReceiveNotification` método é chamado quando a notificação é expandida pelo usuário para que a extensão de conteúdo de notificação possa popular a interface do usuário personalizada com o conteúdo do `UNNotification` . Para o exemplo acima, um rótulo foi adicionado à exibição, exposto ao código com o nome `label` e é usado para exibir o corpo da notificação.

### <a name="setting-the-notification-content-extensions-categories"></a>Definindo as categorias da extensão do conteúdo de notificação

O sistema precisa ser informado sobre como localizar a extensão de conteúdo de notificação do aplicativo com base nas categorias específicas às quais ele responde. Faça o seguinte:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Clique duas vezes no arquivo da extensão `Info.plist` na **painel de soluções** para abri-lo para edição.
2. Alterne para a exibição **Origem**.
3. Expanda a `NSExtension` chave.
4. Adicione a `UNNotificationExtensionCategory` chave como tipo **cadeia de caracteres** com o valor da categoria à qual a extensão pertence (neste exemplo, ' evento-INVITE): 

    [![Adicionar a chave UNNotificationExtensionCategory](advanced-user-notifications-images/customui02.png)](advanced-user-notifications-images/customui02.png#lightbox)
5. Salve suas alterações.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Clique duas vezes no arquivo da extensão `Info.plist` na **Gerenciador de soluções** para abri-lo para edição.
2. Expanda a `NSExtension` chave.
3. Adicione a `UNNotificationExtensionCategory` chave como tipo **cadeia de caracteres** com o valor da categoria à qual a extensão pertence (neste exemplo, ' evento-INVITE): 

    [![Adicionar a chave UNNotificationExtensionCategory](advanced-user-notifications-images/customui02w.png)](advanced-user-notifications-images/customui02w.png#lightbox)
4. Salve suas alterações.

-----

As categorias de extensão de conteúdo de notificação ( `UNNotificationExtensionCategory` ) usam os mesmos valores de categoria usados para registrar ações de notificação. Na situação em que o aplicativo usará a mesma interface do usuário para várias categorias, alterne `UNNotificationExtensionCategory` para a **matriz** de tipos e forneça todas as categorias necessárias. Por exemplo:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Categorias de extensão de conteúdo de notificação](advanced-user-notifications-images/customui03.png)](advanced-user-notifications-images/customui03.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Categorias de extensão de conteúdo de notificação](advanced-user-notifications-images/customui03w.png)](advanced-user-notifications-images/customui03w.png#lightbox)

-----

### <a name="hiding-the-default-notification-content"></a>Ocultando o conteúdo de notificação padrão

Na situação em que a interface do usuário de notificação personalizada exibirá o mesmo conteúdo que a notificação padrão (título, subtítulo e corpo exibidos automaticamente na parte inferior da interface do usuário de notificação), essas informações padrão poderão ser ocultadas adicionando a `UNNotificationExtensionDefaultContentHidden` chave à `NSExtensionAttributes` chave como tipo **booliano** com um valor de `YES` no arquivo da extensão `Info.plist` :

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Localizando informações padrão](advanced-user-notifications-images/customui04.png)](advanced-user-notifications-images/customui04.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Localizando informações padrão](advanced-user-notifications-images/customui04w.png)](advanced-user-notifications-images/customui04w.png#lightbox)

-----

### <a name="designing-the-custom-ui"></a>Criando a interface do usuário personalizada

Para criar a interface de usuário personalizada da extensão de conteúdo de notificação, clique duas vezes no `MainInterface.storyboard` arquivo para abri-lo para edição no designer do IOS, arraste nos elementos que você precisa para criar a interface desejada (como `UILabels` e `UIImageViews` ).

> [!NOTE]
> A partir do iOS 12, uma extensão de conteúdo de notificação pode incluir controles interativos, como botões e campos de texto. Para obter mais informações, consulte as [notificações interativas na documentação do IOS 12](~/ios/platform/introduction-to-ios12/notifications/interactive.md) .

Depois que a interface do usuário tiver sido disposta e os controles necessários expostos ao código C#, abra o `NotificationViewController.cs` para edição e modifique o `DidReceiveNotification` método para popular a interface do usuário quando ele expandir a notificação. Por exemplo:

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

Para ajustar o tamanho da área de conteúdo exibida para o usuário, o código a seguir está definindo a `PreferredContentSize` propriedade no `ViewDidLoad` método para o tamanho desejado. Esse tamanho também pode ser ajustado com a aplicação de restrições à exibição no designer do iOS, é deixada para o desenvolvedor escolher o método que funciona melhor para eles.

Como o sistema de notificação já está em execução antes que a extensão de conteúdo de notificação seja invocada, a área de conteúdo será iniciada em tamanho completo e será animada para o tamanho solicitado quando for apresentada ao usuário.

Para eliminar esse efeito, edite o `Info.plist` arquivo para a extensão e defina a `UNNotificationExtensionInitialContentSizeRatio` chave da `NSExtensionAttributes` chave para o **número** de tipo com um valor que represente a razão desejada. Por exemplo:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![A chave UNNotificationExtensionInitialContentSizeRatio](advanced-user-notifications-images/customui05.png)](advanced-user-notifications-images/customui05.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![A chave UNNotificationExtensionInitialContentSizeRatio](advanced-user-notifications-images/customui05w.png)](advanced-user-notifications-images/customui05w.png#lightbox)

-----

### <a name="using-media-attachments-in-custom-ui"></a>Usando anexos de mídia na interface do usuário personalizada

Como os anexos de mídia (como visto na seção [adicionando anexos de mídia](#adding-media-attachments) acima) fazem parte da carga de notificação, eles podem ser acessados e exibidos na extensão de conteúdo de notificação, assim como seriam na interface do usuário de notificação padrão.

Por exemplo, se a interface do usuário personalizada acima incluía uma `UIImageView` que foi exposta ao código C#, o código a seguir poderia ser usado para preenchê-lo de com o anexo de mídia:

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

Como o anexo de mídia é gerenciado pelo sistema, ele está fora da área restrita do aplicativo. A extensão precisa informar ao sistema que ele deseja acessar o arquivo chamando o `StartAccessingSecurityScopedResource` método. Quando a extensão é feita com o arquivo, ele precisa chamar o `StopAccessingSecurityScopedResource` para liberar sua conexão.

### <a name="adding-custom-actions-to-a-custom-ui"></a>Adicionando ações personalizadas a uma interface do usuário personalizada

Botões de ação personalizados podem ser usados para adicionar interatividade a uma interface do usuário de notificação personalizada. Consulte a seção [trabalhando com ações de notificação](~/ios/platform/user-notifications/enhanced-user-notifications.md) do documento de [notificações de usuário aprimoradas](~/ios/platform/user-notifications/enhanced-user-notifications.md) para obter mais detalhes sobre as ações personalizadas.

Além das ações personalizadas, a extensão de conteúdo de notificação pode responder às seguintes ações internas também:

- **Ação padrão** – quando o usuário toca em uma notificação para abrir o aplicativo e exibir os detalhes da notificação determinada.
- **Ignorar ação** – essa ação é enviada para o aplicativo quando o usuário ignora uma determinada notificação.

As extensões de conteúdo de notificação também podem atualizar sua interface do usuário quando o usuário chama uma das ações personalizadas, como mostrar uma data como aceita quando o usuário toca no botão **aceitar** ação personalizada. Além disso, as extensões de conteúdo de notificação podem instruir o sistema a atrasar o descarte da interface do usuário de notificação para que o usuário possa ver o efeito de sua ação antes que a notificação seja fechada.

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

Ao adicionar o `Server.PostEventResponse` manipulador ao `DidReceiveNotification` método da extensão de conteúdo de notificação, a extensão *deve* lidar com todas as ações personalizadas. A extensão também pode encaminhar as ações personalizadas para o aplicativo que a contém, alterando o `UNNotificationContentExtensionResponseOption` . Por exemplo:

```csharp
// Close Notification
completionHandler (UNNotificationContentExtensionResponseOption.DismissAndForwardAction);
```

### <a name="working-with-the-text-input-action-in-custom-ui"></a>Trabalhando com a ação de entrada de texto na interface do usuário personalizada

Dependendo do design do aplicativo e da notificação, pode haver ocasiões que exijam que o usuário insira o texto na notificação (como responder a uma mensagem). Uma extensão de conteúdo de notificação tem acesso à ação de entrada de texto interna, assim como uma notificação padrão.

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

Esse código cria uma nova ação de entrada de texto e a adiciona à categoria da extensão (no `MakeExtensionCategory` método). No `DidReceive` método override, ele manipula o usuário inserindo texto com o seguinte código:

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

Se o design chama para adicionar botões personalizados ao campo de entrada de texto, adicione o seguinte código para incluí-los:

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

Este artigo adotou uma visão avançada do uso da nova estrutura de notificação de usuário em um aplicativo Xamarin. iOS. Ele abordou a adição de anexos de mídia a notificações locais e remotas e ele abordou usando a nova interface do usuário de notificação de usuários para criar UIs de notificação personalizadas.

## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
- [Referência da estrutura do usernotifications](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [Guia de programação de notificação local e remota](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/Introduction.html)
