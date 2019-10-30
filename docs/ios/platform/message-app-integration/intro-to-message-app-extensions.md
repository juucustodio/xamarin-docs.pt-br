---
title: Noções básicas da extensão do aplicativo de mensagens no Xamarin. iOS
description: Este artigo mostra como incluir uma extensão de aplicativo de mensagem em uma solução Xamarin. iOS que se integra ao aplicativo de mensagens e apresenta a nova funcionalidade para o usuário.
ms.prod: xamarin
ms.assetid: 0CFB494C-376C-449D-B714-9E82644F9DA3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/02/2017
ms.openlocfilehash: 51a89533390eb1be8c1f36e0121229fb5a942279
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031670"
---
# <a name="message-app-extension-basics-in-xamarinios"></a>Noções básicas da extensão do aplicativo de mensagens no Xamarin. iOS

_Este artigo mostra como incluir uma extensão de aplicativo de mensagem em uma solução Xamarin. iOS que se integra ao aplicativo de mensagens e apresenta a nova funcionalidade para o usuário._

Novo no iOS 10, uma extensão de aplicativo de mensagem se integra ao aplicativo de **mensagens** e apresenta uma nova funcionalidade ao usuário. A extensão pode enviar texto, adesivos, arquivos de mídia e mensagens interativas.

## <a name="about-message-app-extensions"></a>Sobre extensões de aplicativo de mensagens

Como mencionado acima, uma extensão de aplicativo de mensagem se integra ao aplicativo de **mensagens** e apresenta uma nova funcionalidade ao usuário. A extensão pode enviar texto, adesivos, arquivos de mídia e mensagens interativas. Dois tipos de extensão de aplicativo de mensagem estão disponíveis:

- **Pacotes de adesivo** -contém uma coleção de adesivos que o usuário pode adicionar a uma mensagem. Os pacotes adesivos podem ser criados sem escrever nenhum código.
- **aplicativo IMessage** – pode apresentar uma interface do usuário personalizada no aplicativo mensagens para selecionar adesivos, inserir texto, incluir arquivos de mídia (com conversões de tipo opcionais) e criar, editar e enviar mensagens de interação.

As extensões de aplicativos de mensagens fornecem três tipos de conteúdo principais:

- **Mensagens interativas** – são um tipo de conteúdo de mensagem personalizada que um aplicativo gera, quando o usuário toca na mensagem, o aplicativo será iniciado em primeiro plano.
- **Adesivos** -são imagens geradas pelo aplicativo que podem ser incluídas nas mensagens enviadas entre os usuários.
- **Outro conteúdo com suporte** -o aplicativo pode fornecer conteúdo como fotos, vídeos, texto ou links para qualquer outro tipo de conteúdo que sempre tenha sido suportado pelo aplicativo de mensagens.

Novo no iOS 10, o aplicativo de mensagens agora inclui sua própria loja de aplicativos interna dedicada. Todos os aplicativos que incluem extensões de aplicativos de mensagens serão exibidos e promovidos nesse armazenamento. A gaveta de aplicativos de novas mensagens exibirá todos os aplicativos que foram baixados da loja de aplicativos de mensagens para fornecer acesso rápido aos usuários.

Também novidade no iOS 10, a Apple adicionou a atribuição de aplicativo embutido que permite ao usuário descobrir facilmente um aplicativo. Por exemplo, se um usuário enviar conteúdo para outro de um aplicativo que o segundo usuário não tenha instalado (como um adesivo, por exemplo), o nome do aplicativo de envio será listado sob o conteúdo no histórico de mensagens. Se o usuário tocar no nome do aplicativo, a mensagem App Store será aberta e o aplicativo selecionado na loja.

As extensões de aplicativos de mensagens são semelhantes aos aplicativos iOS existentes que o desenvolvedor está familiarizado com a criação e terão acesso a todas as estruturas e recursos padrão de um aplicativo iOS padrão. Por exemplo:

- Eles têm acesso à compra no aplicativo.
- Eles têm acesso ao Apple Pay.
- Eles têm acesso ao hardware do dispositivo, como a câmera.

Extensões de aplicativos de mensagens só têm suporte no iOS 10, no entanto, o conteúdo que essas extensões envia são visíveis em dispositivos watchOS e macOS. A nova _página recentes_ adicionada ao watchOS 3 exibirá adesivos recentes que foram enviados do telefone, incluindo aqueles de extensões de aplicativos de mensagem, e permitem que o usuário envie esses adesivos da inspeção.

## <a name="about-the-messages-framework"></a>Sobre a estrutura de mensagens

Novo no iOS 10, a estrutura de mensagens fornece a interface entre a extensão de aplicativos de mensagem e o aplicativo de mensagem no dispositivo iOS do usuário. Quando o usuário inicia um aplicativo de dentro do aplicativo messages, essa estrutura permite que o aplicativo seja descoberto e fornece os dados e o contexto necessários para o layout de sua interface do usuário.

Depois que o aplicativo é iniciado, o usuário interage com ele para criar um novo conteúdo a ser compartilhado por meio de uma mensagem. Em seguida, o aplicativo usa a estrutura messages para transferir o conteúdo recém-criado para o aplicativo messages para processamento.

A estrutura de mensagens e as extensões de aplicativos de mensagens são criadas com base nas tecnologias de extensões de aplicativo iOS pré-existentes. Para obter mais informações sobre extensões de aplicativo, consulte o [Guia de programação de extensões de aplicativo](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)da Apple.

Ao contrário de outros pontos de extensão que a Apple forneceu em todo o sistema, o desenvolvedor não precisa fornecer um aplicativo host para suas extensões de aplicativos de mensagens, pois o próprio aplicativo de mensagens está agindo como o contêiner. No entanto, o desenvolvedor tem a opção de incluir a extensão de aplicativos de mensagem dentro de um aplicativo iOS novo ou existente e enviá-lo junto com o pacote.

Se as extensões de aplicativos de mensagem estiverem incluídas no grupo de um aplicativo iOS, o ícone do aplicativo será exibido na tela inicial do dispositivo e na gaveta do aplicativo de mensagem de dentro do aplicativo mensagens. Se não estiver incluído em um pacote de aplicativo, a extensão de aplicativos de mensagem será exibida somente na gaveta do aplicativo de mensagens.

Mesmo que as extensões de aplicativos de mensagem não estejam incluídas em um pacote de aplicativo de host, o desenvolvedor precisará fornecer um ícone de aplicativo no grupo da extensão de aplicativos de mensagem, pois esse é o ícone que será exibido em outras partes do sistema, como a gaveta do aplicativo de mensagens ou as configurações , para a extensão.

## <a name="about-stickers"></a>Sobre adesivos

Os adesivos criados pela Apple como uma nova maneira de iMessage os usuários a se comunicarem, permitindo que os adesivos sejam enviados embutidos como qualquer outro conteúdo de mensagem ou possam ser anexados a bolhas de mensagem anteriores dentro da conversa.

O que são adesivos?

- São imagens fornecidas pela extensão de aplicativos de mensagem.
- Eles podem ser imagens animadas ou estáticas.
- Eles fornecem uma nova maneira de compartilhar conteúdo de imagem de dentro de um aplicativo.

Há duas maneiras de criar adesivos:

1. Uma mensagem do pacote de adesivo extensões de aplicativos podem ser criadas de dentro do Xcode sem incluir nenhum código. Tudo o que é necessário são os ativos para os adesivos e os ícones do aplicativo.
2. Criando uma extensão de aplicativos de mensagem padrão que fornece adesivos do código por meio da estrutura de mensagens.

### <a name="creating-sticker-packs"></a>Criando pacotes adesivos

Os pacotes adesivos são criados a partir de um modelo especial dentro do Xcode e simplesmente fornecem um conjunto estático de ativos de imagem que podem ser usados como adesivos. Conforme mencionado acima, eles não exigem nenhum código, o desenvolvedor simplesmente arrasta os arquivos de imagem para a pasta do pacote de adesivo dentro do catálogo de ativos adesivos.

Para que uma imagem seja incluída em um pacote de adesivo, ela deve atender aos seguintes requisitos:

- As imagens devem estar em um formato PNG, APNG, GIF ou JPEG. A Apple sugere usar apenas os formatos PNG e APNG ao fornecer ativos adesivos.
- Os adesivos animados dão suporte apenas aos formatos APNG e GIF.
- As imagens adesivas devem fornecer um plano de fundo transparente, pois elas podem ser colocadas sobre bolhas de mensagem na conversa pelo usuário.
- Os arquivos de imagem individuais devem ser inferiores a 500 KB.
- As imagens não podem ser menores do que 100x100 pontos ou maiores que 206 x 206 pontos.

> [!IMPORTANT]
> As imagens adesivas devem ser sempre fornecidas na resolução de `@3x` no intervalo de 300 x 300 a 618 x 618 pixel. O sistema irá gerar automaticamente as versões `@2x` e `@1x` em tempo de execução, conforme necessário.

A Apple sugere testar os ativos de imagem de adesivo em vários planos de fundo coloridos diferentes (como branco, preto, vermelho, amarelo e colorido) e em fotos, para garantir que eles tenham a melhor aparência em todas as situações possíveis.

Os pacotes adesivos podem fornecer adesivos em um dos três tamanhos disponíveis:

- **Pequenos** -100 x 100 pontos.
- **Médio** -136 x 136 pontos. Esse é o tamanho padrão.
- **Grandes** -206 x 206 pontos.

Use o Inspetor de atributos do Xcode para definir o tamanho do pacote de adesivo inteiro e fornecer apenas ativos de imagem que correspondam ao tamanho solicitado, para obter os melhores resultados no navegador adesivo dentro do aplicativo mensagens.

Para obter mais informações, consulte nosso aplicativo [Ice Builder](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-icecreambuilder) e a referência de [mensagens](https://developer.apple.com/reference/messages)da Apple.

## <a name="creating-a-custom-sticker-experience"></a>Criando uma experiência de adesivo personalizada

Se o aplicativo exigir mais controle ou flexibilidade do que o fornecido por um pacote de adesivo, ele poderá incluir uma extensão de aplicativo de mensagem e fornecer os adesivos por meio da estrutura de mensagens para uma experiência de adesivo personalizada.

Quais são os benefícios de criar uma experiência de adesivo personalizada?

1. Permite que o aplicativo personalize como os adesivos são exibidos para os usuários do aplicativo. Por exemplo, para apresentar adesivos em um formato diferente do layout de grade padrão ou em um plano de fundo colorido diferente.
2. Permite que os adesivos sejam criados dinamicamente a partir do código, em vez de serem incluídos como ativos de imagem estática.
3. Permite que ativos de imagem de adesivo sejam baixados dinamicamente do servidor Web do desenvolvedor sem a necessidade de liberar uma nova versão para a loja de aplicativos.
4. Permite o acesso da câmera do dispositivo para criar adesivos rapidamente.
5. Permite compras no aplicativo para que o usuário possa comprar mais adesivos de dentro do aplicativo.

Para criar uma experiência de adesivo personalizada, faça o seguinte:

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Iniciar Visual Studio para Mac.
2. Abra a solução para adicionar uma extensão de aplicativo de mensagem.
3. Selecione **extensões** do **Ios** >  > **extensão IMessage** e clique no botão **Avançar** :

    [![](intro-to-message-app-extensions-images/message01.png "Select iMessage Extension")](intro-to-message-app-extensions-images/message01.png#lightbox)
4. Insira um **nome de extensão** e clique no botão **Avançar** :

    [![](intro-to-message-app-extensions-images/message02.png "Enter an Extension Name")](intro-to-message-app-extensions-images/message02.png#lightbox)
5. Clique no botão **criar** para criar a extensão:

    [![](intro-to-message-app-extensions-images/message03.png "Click the Create button")](intro-to-message-app-extensions-images/message03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Inicie o Visual Studio.
2. Abra a solução para adicionar uma extensão de aplicativo de mensagem.
3. Selecione **extensões do ios > extensão IMessage (Ios)** e clique no botão **Avançar** :

    [![selecionar a extensão iMessage (iOS)](intro-to-message-app-extensions-images/message01.w157-sml.png)](intro-to-message-app-extensions-images/message01.w157.png#lightbox)

4. Insira um **nome** e clique no botão **OK**

-----

Por padrão, o arquivo de `MessagesViewController.cs` será adicionado à solução. Esse é o ponto de entrada principal na extensão e é herdado da classe `MSMessageAppViewController`.

A estrutura de mensagens fornece classes para apresentar os adesivos disponíveis para o usuário:

- `MSStickerBrowserViewController`-controla a exibição na qual os adesivos serão apresentados. Ele também está em conformidade com a interface `IMSStickerBrowserViewDataSource` para retornar a contagem de adesivos e o adesivo de um determinado índice de navegador.
- `MSStickerBrowserView`-esta é a exibição na qual os adesivos disponíveis serão exibidos.
- `MSStickerSize`-decide os tamanhos de célula individuais para a grade de adesivos apresentados no modo de exibição de navegador.

### <a name="creating-a-custom-sticker-browser"></a>Criando um navegador adesivo personalizado

O desenvolvedor pode personalizar ainda mais a experiência do adesivo para o usuário, fornecendo um navegador adesivo personalizado (`MSMessageAppBrowserViewController`) na extensão do aplicativo de mensagem. O navegador adesivo personalizado altera como os adesivos são apresentados ao usuário quando eles estão selecionando um adesivo para incluir no fluxo de mensagens.

Faça o seguinte:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Na **painel de soluções**, clique com o botão direito do mouse no nome do projeto da extensão e selecione **Adicionar** > **novo arquivo...**  > **Ios | Apple Watch** > **controlador de interface**.
2. Insira `StickerBrowserViewController` para o **nome** e clique no botão **novo** :

    [![](intro-to-message-app-extensions-images/browser01.png "Enter StickerBrowserViewController for the Name")](intro-to-message-app-extensions-images/browser01.png#lightbox)
3. Abra o arquivo `StickerBrowserViewController.cs` para edição.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Na **Gerenciador de soluções**, clique com o botão direito do mouse no nome do projeto da extensão e selecione **Adicionar** > **novo arquivo...**  > **Ios | Apple Watch** > **controlador de interface**.
2. Insira `StickerBrowserViewController` para o **nome** e clique no botão **novo** :

    [![](intro-to-message-app-extensions-images/browser01.w157-sml.png "Enter StickerBrowserViewController for the Name")](intro-to-message-app-extensions-images/browser01.w157.png#lightbox)
3. Abra o arquivo `StickerBrowserViewController.cs` para edição.

-----

Faça com que o `StickerBrowserViewController.cs` seja semelhante ao seguinte:

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Messages;
using Foundation;

namespace MonkeyStickers
{
    public partial class StickerBrowserViewController : MSStickerBrowserViewController
    {
        #region Computed Properties
        public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
        #endregion

        #region Constructors
        public StickerBrowserViewController (MSStickerSize stickerSize) : base (stickerSize)
        {
        }
        #endregion

        #region Private Methods
        private void CreateSticker (string assetName, string localizedDescription)
        {

            // Get path to asset
            var path = NSBundle.MainBundle.PathForResource (assetName, "png");
            if (path == null) {
                Console.WriteLine ("Couldn't create sticker {0}.", assetName);
                return;
            }

            // Build new sticker
            var stickerURL = new NSUrl (path);
            NSError error = null;
            var sticker = new MSSticker (stickerURL, localizedDescription, out error);
            if (error == null) {
                // Add to collection
                Stickers.Add (sticker);
            } else {
                // Report error
                Console.WriteLine ("Error, couldn't create sticker {0}: {1}", assetName, error);
            }
        }

        private void LoadStickers ()
        {

            // Load sticker assets from disk
            CreateSticker ("canada", "Canada Sticker");
            CreateSticker ("clouds", "Clouds Sticker");
            ...
            CreateSticker ("tree", "Tree Sticker");
        }
        #endregion

        #region Public Methods
        public void ChangeBackgroundColor (UIColor color)
        {
            StickerBrowserView.BackgroundColor = color;

        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            LoadStickers ();
        }

        public override nint GetNumberOfStickers (MSStickerBrowserView stickerBrowserView)
        {
            return Stickers.Count;
        }

        public override MSSticker GetSticker (MSStickerBrowserView stickerBrowserView, nint index)
        {
            return Stickers[(int)index];
        }
        #endregion
    }
}
```

Dê uma olhada no código acima em detalhes. Ele cria o armazenamento para os adesivos que a extensão fornece:

```csharp
public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
```

E substitui dois métodos da classe `MSStickerBrowserViewController` para fornecer dados para o navegador deste armazenamento de dados:

```csharp
public override nint GetNumberOfStickers (MSStickerBrowserView stickerBrowserView)
{
    return Stickers.Count;
}

public override MSSticker GetSticker (MSStickerBrowserView stickerBrowserView, nint index)
{
    return Stickers[(int)index];
}
```

O método `CreateSticker` Obtém o caminho de um ativo de imagem do pacote da extensão e o usa para criar uma nova instância de um `MSSticker` desse ativo, que ele adiciona à coleção:

```csharp
private void CreateSticker (string assetName, string localizedDescription)
{

    // Get path to asset
    var path = NSBundle.MainBundle.PathForResource (assetName, "png");
    if (path == null) {
        Console.WriteLine ("Couldn't create sticker {0}.", assetName);
        return;
    }

    // Build new sticker
    var stickerURL = new NSUrl (path);
    NSError error = null;
    var sticker = new MSSticker (stickerURL, localizedDescription, out error);
    if (error == null) {
        // Add to collection
        Stickers.Add (sticker);
    } else {
        // Report error
        Console.WriteLine ("Error, couldn't create sticker {0}: {1}", assetName, error);
    }
}
```

O método `LoadSticker` é chamado de `ViewDidLoad` para criar um adesivo a partir do ativo de imagem nomeada (incluído no pacote do aplicativo) e adicioná-lo à coleção de adesivos.

Para implementar o navegador adesivo personalizado, edite o arquivo `MessagesViewController.cs` e faça com que ele se pareça com o seguinte:

```csharp
using System;
using UIKit;
using Messages;

namespace MonkeyStickers
{
    public partial class MessagesViewController : MSMessagesAppViewController
    {
        #region Computed Properties
        public StickerBrowserViewController BrowserViewController { get; set;}
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Create new browser and configure it
            BrowserViewController = new StickerBrowserViewController (MSStickerSize.Regular);
            BrowserViewController.View.Frame = View.Frame;
            BrowserViewController.ChangeBackgroundColor (UIColor.Gray);

            // Add to view
            AddChildViewController (BrowserViewController);
            BrowserViewController.DidMoveToParentViewController (this);
            View.AddSubview (BrowserViewController.View);
        }
        #endregion
    }
}
```

Dando uma olhada nesse código em detalhes, ele cria o armazenamento para o navegador personalizado:

```csharp
public StickerBrowserViewController BrowserViewController { get; set;}
```

E, no método `ViewDidLoad`, ele instancia e configura um novo navegador:

```csharp
// Create new browser and configure it
BrowserViewController = new StickerBrowserViewController (MSStickerSize.Regular);
BrowserViewController.View.Frame = View.Frame;
BrowserViewController.ChangeBackgroundColor (UIColor.Gray);
```

Em seguida, ele adiciona o navegador à exibição para exibi-lo:

```csharp
// Add to view
AddChildViewController (BrowserViewController);
BrowserViewController.DidMoveToParentViewController (this);
View.AddSubview (BrowserViewController.View);
```

### <a name="further-sticker-customization"></a>Personalização adicional do adesivo

A personalização mais adesiva é possível com a inclusão de apenas duas classes na extensão do aplicativo de mensagem:

- `MSStickerView`
- `MSSticker`

Usando os métodos acima, a extensão pode dar suporte à seleção de adesivo que não depende do método de navegador adesivo padrão. Além disso, a exibição adesivo pode ser alternada entre dois modos de exibição diferentes:

- **Compact** -esse é o modo padrão em que a exibição adesivo ocupa os 25% inferiores da exibição de mensagem.
- **Expandido** -a exibição adesivo preenche a exibição de mensagem inteira.

Essa exibição de adesivo pode ser alternada entre esses modos de forma programática ou manual pelo usuário.

Veja o exemplo a seguir de como tratar a opção entre os dois modos de exibição diferentes. Dois controladores de exibição diferentes serão necessários para cada Estado. O `StickerBrowserViewController` lida com a exibição **compacta** e é semelhante ao seguinte:

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Messages;
using Foundation;

namespace MessageExtension
{
    public partial class StickerBrowserViewController : MSStickerBrowserViewController
    {
        #region Computed Properties
        public MessagesViewController MessagesAppViewController { get; set; }
        public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
        #endregion

        #region Constructors
        public StickerBrowserViewController (MessagesViewController messagesAppViewController, MSStickerSize stickerSize) : base (stickerSize)
        {
            // Initialize
            this.MessagesAppViewController = messagesAppViewController;
        }
        #endregion

        #region Private Methods
        private void CreateSticker (string assetName, string localizedDescription)
        {

            // Get path to asset
            var path = NSBundle.MainBundle.PathForResource (assetName, "png");
            if (path == null) {
                Console.WriteLine ("Couldn't create sticker {0}.", assetName);
                return;
            }

            // Build new sticker
            var stickerURL = new NSUrl (path);
            NSError error = null;
            var sticker = new MSSticker (stickerURL, localizedDescription, out error);
            if (error == null) {
                // Add to collection
                Stickers.Add (sticker);
            } else {
                // Report error
                Console.WriteLine ("Error, couldn't create sticker {0}: {1}", assetName, error);
            }
        }

        private void LoadStickers ()
        {

            // Load sticker assets from disk
            CreateSticker ("add", "Add New Sticker");
            CreateSticker ("canada", "Canada Sticker");
            CreateSticker ("clouds", "Clouds Sticker");
            CreateSticker ("tree", "Tree Sticker");
        }
        #endregion

        #region Public Methods
        public void ChangeBackgroundColor (UIColor color)
        {
            StickerBrowserView.BackgroundColor = color;

        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            LoadStickers ();

        }

        public override nint GetNumberOfStickers (MSStickerBrowserView stickerBrowserView)
        {
            return Stickers.Count;
        }

        public override MSSticker GetSticker (MSStickerBrowserView stickerBrowserView, nint index)
        {
            // Wanting to add a new sticker?
            if (index == 0) {
                // Yes, ask controller to present add sticker interface
                MessagesAppViewController.AddNewSticker ();
                return null;
            } else {
                // No, return existing sticker
                return Stickers [(int)index];
            }
        }
        #endregion
    }
}
```

O `AddStickerViewController` manipulará o modo de exibição de adesivo **expandido** e será semelhante ao seguinte:

```csharp
using System;
using Foundation;
using UIKit;
using Messages;

namespace MessageExtension
{
    public class AddStickerViewController : UIViewController
    {
        #region Computed Properties
        public MessagesViewController MessagesAppViewController { get; set;}
        public MSSticker NewSticker { get; set;}
        #endregion

        #region Constructors
        public AddStickerViewController (MessagesViewController messagesAppViewController)
        {
            // Initialize
            this.MessagesAppViewController = messagesAppViewController;
        }
        #endregion

        #region Override Method
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Build interface to create new sticker
            var cancelButton = new UIButton (UIButtonType.RoundedRect);
            cancelButton.TouchDown += (sender, e) => {
                // Cancel add new sticker
                MessagesAppViewController.CancelAddNewSticker ();
            };
            View.AddSubview (cancelButton);

            var doneButton = new UIButton (UIButtonType.RoundedRect);
            doneButton.TouchDown += (sender, e) => {
                // Add new sticker to collection
                MessagesAppViewController.AddStickerToCollection (NewSticker);
            };
            View.AddSubview (doneButton);

            ...
        }
        #endregion
    }
}
```

O `MessageViewController` implementa esses controladores de exibição para orientar o estado solicitado:

```csharp
using System;
using UIKit;
using Messages;

namespace MessageExtension
{
    public partial class MessagesViewController : MSMessagesAppViewController
    {
        #region Computed Properties
        public bool IsAddingSticker { get; set;}
        public StickerBrowserViewController BrowserViewController { get; set; }
        public AddStickerViewController AddStickerController { get; set;}
        #endregion

        #region Constructors
        protected MessagesViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Public Methods
        public void PresentStickerBrowser ()
        {
            // Is the Add sticker view being displayed?
            if (IsAddingSticker) {
                // Yes, remove it from view
                AddStickerController.RemoveFromParentViewController ();
                AddStickerController.View.RemoveFromSuperview ();
            }

            // Add to view
            AddChildViewController (BrowserViewController);
            BrowserViewController.DidMoveToParentViewController (this);
            View.AddSubview (BrowserViewController.View);

            // Save mode
            IsAddingSticker = false;
        }

        public void PresentAddSticker ()
        {
            // Is the sticker browser being displayed?
            if (!IsAddingSticker) {
                // Yes, remove it from view
                BrowserViewController.RemoveFromParentViewController ();
                BrowserViewController.View.RemoveFromSuperview ();
            }

            // Add to view
            AddChildViewController (AddStickerController);
            AddStickerController.DidMoveToParentViewController (this);
            View.AddSubview (AddStickerController.View);

            // Save mode
            IsAddingSticker = true;
        }

        public void AddNewSticker ()
        {
            // Switch to expanded view mode
            Request (MSMessagesAppPresentationStyle.Expanded);
        }

        public void CancelAddNewSticker ()
        {
            // Switch to compact view mode
            Request (MSMessagesAppPresentationStyle.Compact);
        }

        public void AddStickerToCollection (MSSticker sticker)
        {
            // Add sticker to collection
            BrowserViewController.Stickers.Add (sticker);

            // Switch to compact view mode
            Request (MSMessagesAppPresentationStyle.Compact);
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Create new browser and configure it
            BrowserViewController = new StickerBrowserViewController (this, MSStickerSize.Regular);
            BrowserViewController.View.Frame = View.Frame;
            BrowserViewController.ChangeBackgroundColor (UIColor.Gray);

            // Create new Add controller and configure it as well
            AddStickerController = new AddStickerViewController (this);
            AddStickerController.View.Frame = View.Frame;

            // Initially present the sticker browser
            PresentStickerBrowser ();
        }

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
        #endregion
    }
}
```

Quando o usuário solicita a adição de um novo adesivo à sua coleção disponível, um novo `AddStickerViewController` torna-se o controlador visível e a exibição adesivo entra no modo de exibição **expandido** :

```csharp
// Switch to expanded view mode
Request (MSMessagesAppPresentationStyle.Expanded);
```

Quando o usuário escolhe um adesivo para adicionar, ele é adicionado à sua coleção disponível e a exibição **compacta** é solicitada:

```csharp
public void AddStickerToCollection (MSSticker sticker)
{
    // Add sticker to collection
    BrowserViewController.Stickers.Add (sticker);

    // Switch to compact view mode
    Request (MSMessagesAppPresentationStyle.Compact);
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

## <a name="summary"></a>Resumo

Este artigo abordou inclui uma extensão de aplicativo de mensagem em uma solução Xamarin. iOS que se integra ao aplicativo de **mensagens** e apresenta novas funcionalidades para o usuário. Ele abordou o uso da extensão para enviar texto, adesivos, arquivos de mídia e mensagens interativas.

## <a name="related-links"></a>Links relacionados

- [Construtor de sorvetes (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-icecreambuilder)
- [Referência de mensagens](https://developer.apple.com/reference/messages)
- [Guia de programação de extensão de aplicativo](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)
