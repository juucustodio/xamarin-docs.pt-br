---
title: Noções básicas sobre a extensão do aplicativo da mensagem no xamarin. IOS
description: Este artigo mostra como incluir uma extensão de aplicativo de mensagem em uma solução do xamarin. IOS que se integra com o aplicativo de mensagens e apresenta a nova funcionalidade para o usuário.
ms.prod: xamarin
ms.assetid: 0CFB494C-376C-449D-B714-9E82644F9DA3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/02/2017
ms.openlocfilehash: 7bd4a87843852e940da96f688371ddbecbf7e0b4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61153800"
---
# <a name="message-app-extension-basics-in-xamarinios"></a>Noções básicas sobre a extensão do aplicativo da mensagem no xamarin. IOS

_Este artigo mostra como incluir uma extensão de aplicativo de mensagem em uma solução do xamarin. IOS que se integra com o aplicativo de mensagens e apresenta a nova funcionalidade para o usuário._

Novo para o iOS 10, uma mensagem da extensão do aplicativo se integra com o **mensagens** aplicativo e apresenta novas funcionalidades para o usuário. A extensão pode enviar mensagens de texto, adesivos, arquivos de mídia e interativas.

## <a name="about-message-app-extensions"></a>Sobre extensões de aplicativo de mensagem

Como mencionado acima, uma mensagem da extensão do aplicativo se integra com o **mensagens** aplicativo e apresenta novas funcionalidades para o usuário. A extensão pode enviar mensagens de texto, adesivos, arquivos de mídia e interativas. Dois tipos de mensagem da extensão do aplicativo estão disponíveis:

- **Pacotes de adesivo** -contém uma coleção de adesivos que o usuário pode adicionar a uma mensagem. Pacotes de adesivo podem ser criados sem escrever nenhum código.
- **iMessage aplicativo** -pode apresentar uma Interface de usuário personalizada dentro do aplicativo de mensagens para selecionar os adesivos, inserir texto, incluindo arquivos de mídia (com conversões de tipo opcional) e criando, editando e enviando mensagens de interação.

Extensões de aplicativos de mensagens fornecer três principais tipos de conteúdo:

- **Mensagens interativas** -são um tipo de conteúdo de mensagem personalizada que gera um aplicativo, quando o usuário toca na mensagem, o aplicativo será iniciado em primeiro plano.
- **Adesivos** -são imagens geradas pelo aplicativo que pode ser incluído nas mensagens enviadas entre usuários.
- **Outros tipos de conteúdo com suporte** – o aplicativo poderá fornecer o conteúdo, como fotos, vídeos, texto ou tipos de links para qualquer outro conteúdo que sempre são suportados pelo aplicativo de mensagens.

Novo para o iOS 10, o aplicativo de mensagem agora inclui seu próprio Store de aplicativo dedicado e internos. Todos os aplicativos que incluem as extensões de aplicativos de mensagem serão exibidos e promovidos neste repositório. A gaveta de aplicativo de mensagens novas exibirá todos os aplicativos que tiverem sido baixados de Store de aplicativo de mensagens para fornecer acesso rápido aos usuários.

Também novo no iOS 10, Apple adicionou Inline App Attribution que permite ao usuário descobrir facilmente um aplicativo. Por exemplo, se um usuário envia o conteúdo para outro de um aplicativo que o usuário 2º não tiver instalado (como um adesivo por exemplo), o nome do aplicativo de envio é listado sob o conteúdo no histórico de mensagens. Se o usuário toca o aplicativo nome, Store de aplicativo a mensagem ser abrimos e o aplicativo selecionado no repositório.

Extensões de aplicativos de mensagem são semelhantes aos aplicativos existentes do iOS que o desenvolvedor estiver familiarizado com a criação e eles terão acesso a todos os recursos de um aplicativo iOS padrão e estruturas padrão. Por exemplo:

- Eles têm acesso a compra no aplicativo.
- Eles têm acesso à Apple Pay.
- Eles têm acesso ao hardware do dispositivo, como a câmera.

Somente há suporte para extensões de aplicativos de mensagem no iOS 10, no entanto, o conteúdo que enviam essas extensões é visível em dispositivos watchOS e macOS. O novo _Recents Page_ adicionado ao watchOS 3, exibirá os adesivos recentes que foram enviados do telefone, incluindo aqueles de extensões de aplicativos de mensagem, e permitir que o usuário enviar os adesivos de inspeção.

## <a name="about-the-messages-framework"></a>Sobre a estrutura de mensagens

Novo para o iOS 10, a estrutura de mensagens fornece a interface entre a extensão de aplicativos de mensagem e o aplicativo de mensagem no dispositivo iOS do usuário. Quando o usuário inicia um aplicativo de dentro do aplicativo de mensagens, essa estrutura permite que o aplicativo a ser descoberto e fornece os dados e o contexto necessário para dispor de sua interface do usuário.

Depois que o aplicativo é iniciado, o usuário interage com ele para criar um novo conteúdo de compartilhamento por meio de uma mensagem. O aplicativo, em seguida, usa a estrutura de mensagens para transferir o conteúdo criado recentemente para o aplicativo de mensagens para processamento.

A estrutura de mensagens e a mensagem de extensões de aplicativos são criadas sobre tecnologias de extensões de aplicativo do iOS já existente. Para obter mais informações sobre extensões de aplicativo, consulte da Apple [guia de programação de extensão de aplicativo](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214).

Ao contrário de outros pontos de extensão que fornecido pela Apple em todo o sistema, o desenvolvedor não precisa fornecer um aplicativo de host para suas extensões de aplicativos de mensagem, pois o aplicativo de mensagem em si está agindo como o contêiner. No entanto, o desenvolvedor tem a opção de incluindo a extensão de aplicativos de mensagem dentro de um aplicativo iOS novo ou existente e enviá-lo junto com o pacote.

Se as extensões de aplicativos de mensagem está incluída no pacote de um aplicativo iOS, o ícone do aplicativo será exibido na tela inicial do dispositivo e na gaveta de aplicativo de mensagem de dentro do aplicativo mensagens. Se ele não está incluído em um pacote de aplicativo, a extensão de aplicativos de mensagens só aparecerão na gaveta de aplicativos de mensagem.

Mesmo se as extensões de aplicativos de mensagem não está incluída em um pacote de aplicativo de host, o desenvolvedor precisará fornecer um ícone de aplicativo no pacote de extensão de aplicativos de mensagem, pois esse é o ícone que será exibido em outras partes do sistema, como a gaveta de aplicativos de mensagem ou as configurações , para a extensão.

## <a name="about-stickers"></a>Sobre os adesivos

Apple projetado adesivos como uma nova maneira para os usuários do iMessage para se comunicar, permitindo que os adesivos a serem enviados embutido como qualquer outro conteúdo de mensagem ou eles podem ser anexados a anterior balões de mensagem dentro da conversa.

Quais são os adesivos?

- Eles são imagens que fornece a extensão de aplicativos de mensagem.
- Eles podem ser imagens animadas ou estáticas.
- Eles fornecem uma nova maneira de compartilhar conteúdo de imagem de dentro de um aplicativo.

Há duas maneiras de criar os adesivos:

1. Extensões de aplicativos um adesivo de pacote de mensagem podem ser criadas de dentro do Xcode sem incluir qualquer código. Tudo o que é necessário é que os ativos para os adesivos e ícones do aplicativo.
2. Criando uma extensão de aplicativos de mensagem padrão que fornece os adesivos de código por meio da estrutura de mensagens.

### <a name="creating-sticker-packs"></a>Criar pacotes de adesivo

Pacotes de adesivo são criados a partir de um modelo especial dentro do Xcode e simplesmente fornecem um conjunto estático de ativos de imagem que pode ser usado como adesivos. Como mencionado acima, eles não exigem qualquer código, o desenvolvedor simplesmente arrasta os arquivos de imagem na pasta do pacote de adesivo dentro do catálogo de ativos de adesivos.

Para uma imagem a ser incluído em um pacote de adesivo, ele deve atender aos seguintes requisitos:

- Imagens devem ser em um formato PNG, APNG, GIF ou JPEG. Apple sugere o uso apenas os formatos de PNG e APNG ao fornecer ativos de adesivo.
- Adesivos animados somente dão suporte os formatos APNG e GIF.
- Imagens de adesivo devem fornecer um plano de fundo transparente, pois pode ser colocados em balões de mensagem na conversa pelo usuário.
- Os arquivos de imagem individuais devem ser inferior a 500kb.
- Imagens não podem ser maior ou menor que 100 x 100 pontos que aponta de 206 x 206.

> [!IMPORTANT]
> Imagens de adesivo sempre devem ser fornecidas no `@3x` resolução no intervalo 300 x 300 para 618 x 618 pixel. O sistema gerará automaticamente o `@2x` e `@1x` versões em tempo de execução conforme necessário.

A Apple sugere testando os ativos de imagem de adesivo em relação a vários planos de fundo coloridos diferentes (como em branco, preto, vermelho, amarelo e multicoloridas) e fotos acima, para garantir que elas são o melhor em todas as situações possíveis.

Pacotes de adesivo podem fornecer os adesivos de uma das três tamanhos disponíveis:

- **Pequeno** – 100 x 100 pontos.
- **Médio** - 136 x 136 pontos. Esse é o tamanho padrão.
- **Grandes** - 206 x 206 pontos.

Use o Inspetor de atributos do Xcode para definir o tamanho para o pacote inteiro do adesivo e apenas fornecer ativos de imagem que corresponde ao tamanho solicitado, para obter melhores resultados no navegador adesivo dentro do aplicativo de mensagens.

Para obter mais informações, consulte nosso [construtor de sorvete](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/) aplicativo e do Apple [referência de mensagens](https://developer.apple.com/reference/messages).

## <a name="creating-a-custom-sticker-experience"></a>Criar uma experiência personalizada de adesivo

Se o aplicativo exigir mais controle ou a flexibilidade que é fornecido por um pacote de adesivo, pode incluir uma extensão de aplicativo de mensagem e fornecer os adesivos por meio da estrutura de mensagens para uma experiência de adesivo personalizado.

Quais são os benefícios da criação de uma experiência de adesivo personalizado?

1. Permite que o aplicativo personalizar como os adesivos são exibidos para os usuários do aplicativo. Por exemplo, para adesivos presentes em um formato que não seja o layout de grade padrão ou em um plano de fundo colorido diferente.
2. Permite que os adesivos seja criado dinamicamente no código em vez de ser incluído como ativos de imagem estática.
3. Permite que os ativos de imagem de adesivo dinamicamente ser baixado do servidor de web do desenvolvedor sem a necessidade de uma nova versão para a App Store.
4. Permite acesso da câmera do dispositivo para criar os adesivos em interrupções.
5. Permite a compras no aplicativo para que o usuário pode comprar mais adesivos de dentro do aplicativo.

Para criar uma experiência de adesivo personalizada, faça o seguinte:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Inicie o Visual Studio para Mac.
2. Abra a solução para adicionar uma extensão de aplicativo de mensagem. 
3. Selecione **iOS** > **extensões** > **extensão do iMessage** e clique no **próxima** botão: 

    [![](intro-to-message-app-extensions-images/message01.png "Selecione a extensão do iMessage")](intro-to-message-app-extensions-images/message01.png#lightbox)
4. Insira um **nome da extensão** e clique no **próxima** botão: 

    [![](intro-to-message-app-extensions-images/message02.png "Insira um nome de extensão")](intro-to-message-app-extensions-images/message02.png#lightbox)
5. Clique o **criar** botão para criar a extensão: 

    [![](intro-to-message-app-extensions-images/message03.png "Clique no botão Criar")](intro-to-message-app-extensions-images/message03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Inicie o Visual Studio.
2. Abra a solução para adicionar uma extensão de aplicativo de mensagem.
3. Selecione * * extensões do iOS > (iOS) da extensão do iMessage * * e clique no **próxima** botão:

    [![Selecione (iOS) da extensão do iMessage](intro-to-message-app-extensions-images/message01.w157-sml.png)](intro-to-message-app-extensions-images/message01.w157.png#lightbox)

4. Insira um **nome** e clique no **Okey** botão

-----

Por padrão, o `MessagesViewController.cs` arquivo será adicionado à solução. Esse é o ponto de entrada principal para a extensão e ela herda o `MSMessageAppViewController` classe.

A estrutura de mensagens fornece classes para apresentar os adesivos disponíveis para o usuário:

- `MSStickerBrowserViewController` -Controla a exibição que os adesivos serão apresentados no. Ele também está de acordo com o `IMSStickerBrowserViewDataSource` interface para retornar a contagem de adesivo e o adesivo de um índice de determinado navegador.
- `MSStickerBrowserView` -Este é o modo de exibição que os adesivos disponíveis serão exibidos no.
- `MSStickerSize` -Decide os tamanhos individuais de célula da grade de selos apresentados na exibição do navegador.

### <a name="creating-a-custom-sticker-browser"></a>Criação de um navegador personalizado adesivo

O desenvolvedor pode personalizar ainda mais a experiência de adesivo para o usuário, fornecendo um navegador de adesivo personalizado (`MSMessageAppBrowserViewController`) na extensão de aplicativo de mensagem. O navegador de adesivo personalizado altera como adesivos são apresentados ao usuário quando eles são selecionando um adesivo a serem incluídos no fluxo de mensagens.

Faça o seguinte:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. No **painel de soluções**, clique com botão direito no nome do projeto da extensão e selecione **Add** > **novo arquivo...**   >  **iOS | Apple Watch** > **controlador de Interface**.
2. Insira `StickerBrowserViewController` para o **nome** e clique no **New** botão: 

    [![](intro-to-message-app-extensions-images/browser01.png "Digite StickerBrowserViewController para o nome")](intro-to-message-app-extensions-images/browser01.png#lightbox)
3. Abra o `StickerBrowserViewController.cs` arquivo para edição.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. No **Gerenciador de soluções**, clique com botão direito no nome do projeto da extensão e selecione **Add** > **novo arquivo...**   >  **iOS | Apple Watch** > **controlador de Interface**.
2. Insira `StickerBrowserViewController` para o **nome** e clique no **New** botão: 

    [![](intro-to-message-app-extensions-images/browser01.w157-sml.png "Digite StickerBrowserViewController para o nome")](intro-to-message-app-extensions-images/browser01.w157.png#lightbox)
3. Abra o `StickerBrowserViewController.cs` arquivo para edição.

-----

Verifique o `StickerBrowserViewController.cs` semelhante ao seguinte:

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

Examine o código acima em detalhes. Ele cria o armazenamento para os adesivos com a extensão fornece:

```csharp
public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
```

E substitui os dois métodos o `MSStickerBrowserViewController` classe para fornecer dados para o navegador desse armazenamento de dados:

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

O `CreateSticker` método obtém o caminho de um ativo de imagem do pacote de extensão e usa-o para criar uma nova instância de um `MSSticker` desse ativo, o que ele adiciona à coleção:

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

O `LoadSticker` método é chamado de `ViewDidLoad` para criar uma etiqueta do ativo de imagem nomeada (incluído no pacote do aplicativo) e adicioná-lo à coleção de adesivos.

Para implementar o navegador de adesivo personalizado, edite o `MessagesViewController.cs` de arquivo e torná-lo semelhante ao seguinte:

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

Vamos analisar esse código detalhadamente, ele cria o armazenamento para o navegador personalizado:

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

### <a name="further-sticker-customization"></a>Outra personalização de adesivo

Personalização de adesivo adicional é possível incluindo apenas duas classes na extensão de aplicativo de mensagem:

- `MSStickerView`
- `MSSticker`

Usando os métodos acima, a extensão pode dar suporte a seleção de adesivo que não depende do método de etiqueta com o navegador padrão. Além disso, a exibição de adesivo pode ser alternada entre dois modos de exibição diferentes:

- **Compact** -isso é o modo padrão em que o modo de exibição de adesivo se ocupa a parte inferior 25% da exibição de mensagem.
- **Expandido** -o modo de exibição de adesivo preenche toda a exibição de mensagem.

Este modo de exibição de adesivo pode ser alternado entre esses modos de forma programática ou manualmente pelo usuário.

Vejamos o exemplo a seguir de lidar com a alternância entre os dois modos de exibição diferente. Dois controladores de exibição diferentes serão necessárias para cada estado. O `StickerBrowserViewController` manipula a **Compact** modo de exibição e se parece com a seguinte:

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

O `AddStickerViewController` manipulará as **expandido** etiqueta com o modo de exibição e a aparência semelhante à seguinte:

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

Quando o usuário solicita para adicionar um novo adesivo à sua coleção disponível, um novo `AddStickerViewController` é feita, o controlador visível e o modo de exibição de adesivo entra o **expandido** exibição:

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

Este artigo cobriu incluem uma extensão de aplicativo de mensagem em uma solução do xamarin. IOS que se integra com o **mensagens** presente novas funcionalidades para o usuário e aplicativo. Ele abordou usando a extensão para enviar mensagens de texto, adesivos, arquivos de mídia e interativas.



## <a name="related-links"></a>Links relacionados

- [Construtor de sorvete (amostra)](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/)
- [Referência de mensagens](https://developer.apple.com/reference/messages)
- [Guia de programação de extensão do aplicativo](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)
