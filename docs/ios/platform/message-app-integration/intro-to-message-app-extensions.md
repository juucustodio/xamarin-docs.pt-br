---
title: "Noções básicas de extensão do aplicativo de mensagem"
description: "Este artigo mostra como incluir uma extensão de aplicativo de mensagem em uma solução de xamarin que integra o aplicativo de mensagens e apresenta novos recursos para o usuário."
ms.topic: article
ms.prod: xamarin
ms.assetid: 0CFB494C-376C-449D-B714-9E82644F9DA3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 083920aba3c8dc83b157b591e194c43935dcc566
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2018
---
# <a name="message-app-extension-basics"></a>Noções básicas de extensão do aplicativo de mensagem

_Este artigo mostra como incluir uma extensão de aplicativo de mensagem em uma solução de xamarin que integra o aplicativo de mensagens e apresenta novos recursos para o usuário._

Novo para iOS 10, uma mensagem da extensão do aplicativo integra o **mensagens** aplicativo e apresenta novas funcionalidades para o usuário. A extensão pode enviar mensagens de texto, adesivos, arquivos de mídia e interativas.

## <a name="about-message-app-extensions"></a>Sobre extensões de aplicativo de mensagem

Como mencionado acima, uma mensagem da extensão do aplicativo integra o **mensagens** aplicativo e apresenta novas funcionalidades para o usuário. A extensão pode enviar mensagens de texto, adesivos, arquivos de mídia e interativas. Dois tipos de mensagem da extensão do aplicativo estão disponíveis:

- **Pacotes de etiqueta** -contém uma coleção de selos que o usuário pode adicionar a uma mensagem. Pacotes de etiqueta podem ser criados sem escrever nenhum código.
- **iMessage aplicativo** -pode apresentar uma Interface de usuário personalizada dentro do aplicativo de mensagens para selecionar adesivo, inserindo texto, incluindo arquivos de mídia (com conversões de tipo opcional) e criação, edição e enviar mensagens de interação.

Extensões de aplicativos de mensagens fornece três tipos de conteúdo principais:

- **Mensagens interativas** -são um tipo de conteúdo da mensagem personalizada que gera um aplicativo, quando o usuário toca na mensagem, o aplicativo será iniciado em primeiro plano.
- **Adesivo** -são imagens geradas pelo aplicativo que pode ser incluído nas mensagens enviadas entre os usuários.
- **Outros tipos de conteúdo com suporte** - o aplicativo poderá fornecer o conteúdo, como fotos, vídeos, texto ou tipos de links para qualquer outro conteúdo que sempre são suportados pelo aplicativo de mensagens.

Novo para iOS 10, o aplicativo de mensagem agora inclui seu próprio armazenamento de aplicativo dedicado e internos. Todos os aplicativos que incluem as extensões de aplicativos de mensagem serão exibidos e promovidos no repositório. A gaveta de aplicativo de mensagens novas exibirá todos os aplicativos que foram baixados da loja de aplicativos de mensagens para fornecer acesso rápido aos usuários.

Também novo no iOS 10, Apple adicionou Inline App Attribution que permite ao usuário descobrir facilmente um aplicativo. Por exemplo, se um usuário envia o conteúdo para outro em um aplicativo que o usuário 2º não tiver instalado (como uma etiqueta por exemplo), o nome do aplicativo envio será listado no conteúdo no histórico de mensagens. Se o usuário toca o aplicativo nome, a mensagem App Store, é possível abrir e o aplicativo selecionado no repositório.

Extensões de aplicativos de mensagens são semelhantes aos aplicativos do iOS que o desenvolvedor está familiarizado com a criação e terão acesso a todas as estruturas padrão e os recursos de um aplicativo iOS padrão existente. Por exemplo:

- Eles têm acesso a compra no aplicativo.
- Eles têm acesso ao pagamento da Apple.
- Eles têm acesso ao dispositivo de hardware, como a câmera.

Somente há suporte para extensões de aplicativos de mensagens no iOS 10, no entanto, o conteúdo que enviam essas extensões é visível em dispositivos watchOS e macOS. O novo _Recents Page_ adicionado ao watchOS 3, exibirá adesivos recentes que foram enviados do telefone, incluindo aqueles em extensões de aplicativos de mensagem, e permitir que o usuário enviar esse adesivo de inspeção.

## <a name="about-the-messages-framework"></a>Sobre a estrutura de mensagens

Novo para iOS 10, a estrutura de mensagens fornece a interface entre a extensão de aplicativos de mensagem e o aplicativo de mensagem no dispositivo do iOS do usuário. Quando o usuário inicia um aplicativo de dentro do aplicativo de mensagens, essa estrutura permite que o aplicativo a ser descoberto e fornece os dados e o contexto necessário para dispor sua interface do usuário.

Depois que o aplicativo é iniciado, o usuário interage com ele para criar um novo conteúdo para compartilhar por meio de uma mensagem. O aplicativo, em seguida, usa a estrutura de mensagens para transferir o conteúdo criado recentemente para o aplicativo de mensagens para processamento.

A estrutura de mensagens e extensões de aplicativos de mensagem criadas sobre tecnologias de extensões de aplicativo do iOS preexistente. Para obter mais informações sobre extensões de aplicativo, consulte da Apple [guia de programação de extensão de aplicativo](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214).

Ao contrário de outros pontos de extensão que Apple fornecido em todo o sistema, o desenvolvedor não precisa fornecer um aplicativo de host para suas extensões de aplicativos de mensagem desde que o aplicativo de mensagem em si está atuando como o contêiner. No entanto, o desenvolvedor tem a opção de incluindo a extensão de aplicativos de mensagens dentro de um aplicativo iOS novo ou existente e entrega junto com o pacote.

Se as extensões de aplicativos de mensagem está incluída no pacote de um aplicativo iOS, ícone do aplicativo será exibido na tela inicial do dispositivo e na mensagem de aplicativo gaveta de dentro do aplicativo de mensagens. Se não estiver incluído em um pacote de aplicativo, a extensão de aplicativos de mensagem somente será exibida na gaveta de aplicativo de mensagem.

Mesmo se as extensões de aplicativos de mensagens não está incluída em um pacote de aplicativo de host, o desenvolvedor precisa fornecer um ícone de aplicativo no pacote de extensão de aplicativos de mensagem, como esse é o ícone que será exibido em outras partes do sistema, como a gaveta de aplicativo de mensagens ou as configurações , para a extensão.

## <a name="about-stickers"></a>Sobre adesivo

Apple projetado adesivos como uma nova maneira para que os usuários iMessage se comunicar, permitindo que o adesivo envio embutido como qualquer outro conteúdo de mensagem ou eles podem ser anexados a bolhas de mensagem anterior dentro da conversa.

Quais são adesivo?

- Eles são imagens que fornece a extensão de aplicativos de mensagem.
- Eles podem ser imagens animadas ou estáticas.
- Eles fornecem uma nova maneira de compartilhar conteúdo de imagem de dentro de um aplicativo.

Há duas maneiras de criar adesivo:

1. Extensões de aplicativos de etiqueta com o pacote de mensagem podem ser criadas de dentro do Xcode sem incluir qualquer código. Tudo o que é necessário é ativos para o adesivo e os ícones de aplicativo.
2. Criando uma extensão de aplicativos de mensagem padrão que fornece o adesivo de código por meio da estrutura de mensagens.

### <a name="creating-sticker-packs"></a>Criando pacotes de etiqueta

Pacotes de etiqueta são criados a partir de um modelo especial dentro Xcode e basta fornecem um conjunto estático de ativos de imagem que pode ser usado como adesivos. Como mencionado acima, eles não exigem qualquer código, o desenvolvedor simplesmente arrasta os arquivos de imagem para a pasta de pacote adesivo dentro do catálogo de ativos de adesivo.

Para uma imagem a ser incluído em um pacote de etiqueta, ele deve atender aos seguintes requisitos:

- Imagens devem estar em um formato PNG, APNG, GIF ou JPEG. Apple sugere usando apenas os formatos PNG e APNG ao fornecimento de etiqueta.
- Adesivo animado só oferecem suporte os formatos APNG e GIF.
- Imagens de etiqueta devem fornecer um plano de fundo transparente desde que podem ser colocados em bolhas de mensagem na conversa pelo usuário.
- Os arquivos de imagem individual devem ser inferior a 500kb.
- Imagens não podem ser menor do que 100 x 100 pontos ou maior que aponta 206 x 206.

> [!IMPORTANT]
> Imagens de etiqueta sempre devem ser fornecidas no `@3x` resolução no intervalo de 300 x 300 para 618 x 618 pixel. O sistema gerará automaticamente o `@2x` e `@1x` versões em tempo de execução conforme necessário.

Apple sugere os ativos de imagem de etiqueta contra várias diferentes planos de fundo (por exemplo, branco, preto, vermelho, amarelo e vários coloridas) e fotos acima, para garantir que elas são a melhor em todas as situações possíveis de teste.

Pacotes de etiqueta podem fornecer adesivos em um dos três tamanhos disponíveis:

- **Pequeno** - 100 x 100 pontos.
- **Médio** - 136 x 136 pontos. Este é o tamanho padrão.
- **Grandes** - 206 x 206 pontos.

Use o Inspetor de atributos do Xcode para definir o tamanho para o pacote inteiro do selo e fornecem apenas ativos de imagem que correspondem ao tamanho solicitado, para obter melhores resultados no navegador adesivo dentro do aplicativo de mensagens.

Para obter mais informações, consulte nosso [sorvete construtor](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/) aplicativo e da Apple [referência de mensagens](https://developer.apple.com/reference/messages).

## <a name="creating-a-custom-sticker-experience"></a>Criando uma experiência de etiqueta personalizado

Se o aplicativo requer mais controle ou a flexibilidade que é fornecido por um pacote de etiqueta, ele pode incluir uma extensão de aplicativo de mensagem e fornecem o adesivo por meio da estrutura de mensagens para uma experiência de etiqueta personalizada.

Quais são os benefícios da criação de uma experiência de etiqueta personalizado?

1. Permite que o aplicativo personalizar como adesivos são exibidos para os usuários do aplicativo. Por exemplo, para adesivos presentes em um formato diferente de layout de grade padrão ou em um plano de fundo colorido diferente.
2. Permite adesivos seja criado dinamicamente no código em vez de ser incluído como ativos de imagem estática.
3. Permite que os ativos de imagem de etiqueta a dinamicamente serem baixados do servidor de web do desenvolvedor sem a necessidade de liberar uma nova versão para a loja de aplicativos.
4. Permite acesso de câmera do dispositivo para criar um adesivo no rapidamente.
5. Permite compras no aplicativo para que o usuário pode adquirir mais adesivo de dentro do aplicativo.

Para criar uma experiência de etiqueta personalizada, faça o seguinte:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Inicie o Visual Studio para Mac.
2. Abra a solução para adicionar uma extensão de aplicativo de mensagem. 
3. Selecione **iOS** > **extensões** > **iMessage extensão** e clique no **próximo** botão: 

    [![](intro-to-message-app-extensions-images/message01.png "Selecione iMessage extensão")](intro-to-message-app-extensions-images/message01.png#lightbox)
4. Insira um **nome de extensão** e clique no **próximo** botão: 

    [![](intro-to-message-app-extensions-images/message02.png "Insira um nome de extensão")](intro-to-message-app-extensions-images/message02.png#lightbox)
5. Clique o **criar** botão para criar a extensão: 

    [![](intro-to-message-app-extensions-images/message03.png "Clique no botão Criar")](intro-to-message-app-extensions-images/message03.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Inicie o Visual Studio.
2. Abra a solução para adicionar uma extensão de aplicativo de mensagem. 
3. Selecione **iOS** > **extensões** > **iMessage extensão** e clique no **próximo** botão: 

    [![](intro-to-message-app-extensions-images/message01w.png "Selecione iMessage extensão")](intro-to-message-app-extensions-images/message01.png#lightbox)
4. Insira um **nome de extensão** e clique no **Okey** botão

-----

Por padrão, o `MessagesViewController.cs` arquivo será adicionado à solução. Este é o ponto de entrada principal para a extensão e ele herda o `MSMessageAppViewController` classe.

A estrutura de mensagens fornece classes para apresentar adesivos disponíveis para o usuário:

- `MSStickerBrowserViewController` -Controla o modo de exibição que o adesivo será apresentado. Ele também está de acordo com o `IMSStickerBrowserViewDataSource` interface para retornar a contagem de etiqueta e na etiqueta de um índice de navegador específico.
- `MSStickerBrowserView` -Este é o modo de exibição que será exibido o adesivo disponível no.
- `MSStickerSize` -Decide os tamanhos de célula individual para a grade de selos apresentados na exibição do navegador.

### <a name="creating-a-custom-sticker-browser"></a>Criando um navegador de etiqueta personalizado

O desenvolvedor pode personalizar ainda mais a experiência de etiqueta para o usuário, fornecendo um navegador de etiqueta personalizada (`MSMessageAppBrowserViewController`) na extensão de aplicativo de mensagem. O navegador de etiqueta personalizado altera como adesivos são apresentados ao usuário quando ele estão selecionando um adesivo para incluir no fluxo de mensagens.

Faça o seguinte:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. No **solução preenchimento**, com o botão direito no nome do projeto de extensão e selecione **adicionar** > **novo arquivo...**   >  **iOS | Apple Watch** > **Interface controlador**.
2. Digite `StickerBrowserViewController` para o **nome** e clique no **novo** botão: 

    [![](intro-to-message-app-extensions-images/browser01.png "Digite StickerBrowserViewController para o nome")](intro-to-message-app-extensions-images/browser01.png#lightbox)
3. Abra o `StickerBrowserViewController.cs` arquivo para edição.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. No **Solution Explorer**, com o botão direito no nome do projeto de extensão e selecione **adicionar** > **novo arquivo...**   >  **iOS | Apple Watch** > **Interface controlador**.
2. Digite `StickerBrowserViewController` para o **nome** e clique no **novo** botão: 

    [![](intro-to-message-app-extensions-images/browser01w.png "Digite StickerBrowserViewController para o nome")](intro-to-message-app-extensions-images/browser01.png#lightbox)
3. Abra o `StickerBrowserViewController.cs` arquivo para edição.

-----

Verifique o `StickerBrowserViewController.cs` a seguinte aparência:

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

Observe o código acima em detalhes. Ele cria o armazenamento para o adesivo a extensão fornece:

```csharp
public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
```

E substitui os dois métodos de `MSStickerBrowserViewController` classe para fornecer dados para o navegador esse repositório de dados:

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

O `CreateSticker` método obtém o caminho de um ativo de imagem do pacote de extensão e usa-o para criar uma nova instância de um `MSSticker` do ativo, que adiciona à coleção:

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

O `LoadSticker` método é chamado de `ViewDidLoad` para criar uma etiqueta com o ativo de imagem nomeada (incluído no pacote do aplicativo) e adicioná-lo à coleção de adesivo.

Para implementar o navegador de etiqueta personalizada, edite o `MessagesViewController.cs` de arquivo e torná-lo a aparência a seguir:

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

Dê uma olhada esse código em detalhes, ele cria o armazenamento para o navegador personalizado:

```csharp
public StickerBrowserViewController BrowserViewController { get; set;}
```

E, no `ViewDidLoad` método, ele cria e configura um novo navegador:

```csharp
// Create new browser and configure it
BrowserViewController = new StickerBrowserViewController (MSStickerSize.Regular);
BrowserViewController.View.Frame = View.Frame;
BrowserViewController.ChangeBackgroundColor (UIColor.Gray);
```

Em seguida, ele adiciona o navegador para o modo de exibição para exibi-lo:

```csharp
// Add to view
AddChildViewController (BrowserViewController);
BrowserViewController.DidMoveToParentViewController (this);
View.AddSubview (BrowserViewController.View);
```

### <a name="further-sticker-customization"></a>Outra personalização de etiqueta

Outra personalização de etiqueta é possível incluindo apenas duas classes na extensão de aplicativo de mensagem:

- `MSStickerView`
- `MSSticker`

Usando os métodos acima, a extensão pode dar suporte a seleção de etiqueta que não contam com o método de etiqueta com o navegador padrão. Além disso, a exibição de etiqueta pode ser alternada entre dois modos de exibição diferentes:

- **Compact** -este é o modo padrão em que a exibição de etiqueta se ocupa a parte inferior 25% da exibição de mensagem.
- **Expandido** -o modo de exibição de etiqueta preenche a exibição de mensagem inteira.

Este modo de exibição de etiqueta pode ser alternado entre esses modos programaticamente ou manualmente pelo usuário.

Veja o exemplo a seguir de lidar com a alternância entre os dois modos de exibição diferente. Dois controladores de exibição diferentes serão necessários para cada estado. O `StickerBrowserViewController` identificadores de **Compact** exibição e parece com o seguinte:

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

O `AddStickerViewController` tratará o **expandido** etiqueta com o modo de exibição e a aparência semelhante à seguinte:

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

O `MessageViewController` implementa esses controladores de exibição para determinar o estado solicitado:

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

Quando o usuário solicita para adicionar um novo adesivo ao seu grupo disponível, um novo `AddStickerViewController` é feita, o controlador visível e o modo de exibição de etiqueta insere o **expandido** exibição:

```csharp
// Switch to expanded view mode
Request (MSMessagesAppPresentationStyle.Expanded);
```

Quando o usuário escolhe um adesivo para adicionar, ele é adicionado à sua coleção disponível e o **Compact** exibição é solicitada:

```csharp
public void AddStickerToCollection (MSSticker sticker)
{
    // Add sticker to collection
    BrowserViewController.Stickers.Add (sticker);

    // Switch to compact view mode
    Request (MSMessagesAppPresentationStyle.Compact);
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

## <a name="summary"></a>Resumo

Este artigo abordou incluir uma extensão de aplicativo de mensagem em uma solução de xamarin que integra o **mensagens** funcionalidade presente para o usuário e o aplicativo. Ele coberto usando a extensão para enviar mensagens de texto, adesivos, arquivos de mídia e interativas.



## <a name="related-links"></a>Links relacionados

- [Construtor de sorvete (exemplo)](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/)
- [Referência de mensagens](https://developer.apple.com/reference/messages)
- [Guia de programação de extensão do aplicativo](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)
