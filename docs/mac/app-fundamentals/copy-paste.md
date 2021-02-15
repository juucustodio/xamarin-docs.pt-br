---
title: Copiar e colar no Xamarin. Mac
description: Este artigo aborda como trabalhar com a área de trabalho para fornecer copiar e colar em um aplicativo Xamarin. Mac. Ele mostra como trabalhar com tipos de dados padrão que podem ser compartilhados entre vários aplicativos e como dar suporte a dados personalizados em um determinado aplicativo.
ms.prod: xamarin
ms.assetid: 7E9C99FB-B7B4-4C48-B20F-84CB48543083
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: e85a273c75fd09672c6c75738adcdc576705af09
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433983"
---
# <a name="copy-and-paste-in-xamarinmac"></a>Copiar e colar no Xamarin. Mac

_Este artigo aborda como trabalhar com a área de trabalho para fornecer copiar e colar em um aplicativo Xamarin. Mac. Ele mostra como trabalhar com tipos de dados padrão que podem ser compartilhados entre vários aplicativos e como dar suporte a dados personalizados em um determinado aplicativo._

## <a name="overview"></a>Visão geral

Ao trabalhar com C# e .NET em um aplicativo Xamarin. Mac, você tem acesso ao mesmo suporte de área de trabalho (copiar e colar) que um desenvolvedor trabalhando no Objective-C tem.

Neste artigo, abordaremos as duas maneiras principais de usar a área de entrada em um aplicativo Xamarin. Mac:

1. **Tipos de dados padrão** -como as operações de área de ti normalmente são executadas entre dois aplicativos não relacionados, nenhum aplicativo sabe os tipos de dados aos quais o outro dá suporte. Para maximizar o potencial de compartilhamento, a área de colagem pode manter várias representações de um determinado item (usando um conjunto padrão de tipos de dados comuns), permitindo que o aplicativo de consumo escolha a versão mais adequada para suas necessidades.
2. **Dados personalizados** -para dar suporte à cópia e colagem de dados complexos no Xamarin. Mac, você pode definir um tipo de dados personalizado que será manipulado pela área de ti. Por exemplo, um aplicativo de desenho vetorial que permite ao usuário copiar e colar formas complexas que são compostas por vários tipos de dados e pontos.

[![Exemplo do aplicativo em execução](copy-paste-images/intro01.png "Exemplo do aplicativo em execução")](copy-paste-images/intro01-large.png#lightbox)

Neste artigo, abordaremos as noções básicas de como trabalhar com a área de trabalho em um aplicativo Xamarin. Mac para dar suporte a operações de copiar e colar. É altamente recomendável que você trabalhe pelo artigo [Hello, Mac](~/mac/get-started/hello-mac.md) primeiro, especificamente a [introdução às seções Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [ações](~/mac/get-started/hello-mac.md#outlets-and-actions) , pois ela aborda os principais conceitos e técnicas que usaremos neste artigo.

Talvez você queira dar uma olhada na seção [expondo classes/métodos C# para Objective-C](~/mac/internals/how-it-works.md) do documento [interno do Xamarin. Mac](~/mac/internals/how-it-works.md) também, explica os `Register` `Export` atributos e usados para conectar suas classes C# a objetos Objective-C e elementos de interface do usuário.

## <a name="getting-started-with-the-pasteboard"></a>Introdução à área de ti

A área de colagem apresenta um mecanismo padronizado para a troca de dados em um determinado aplicativo ou entre aplicativos. O uso típico para uma área de armazenamento em um aplicativo Xamarin. Mac é manipular operações de copiar e colar, no entanto, também há suporte para várias outras operações (como arrastar & soltar e Serviços de Aplicativos).

Para tirar o fundo rapidamente, vamos começar com uma introdução simples e prática de usar o pasteboards em um aplicativo Xamarin. Mac. Posteriormente, forneceremos uma explicação detalhada de como a área de trabalhos funciona e os métodos usados.

Para este exemplo, criaremos um aplicativo baseado em documento simples que gerencia uma janela que contém uma exibição de imagem. O usuário poderá copiar e colar imagens entre documentos no aplicativo e para ou de outros aplicativos ou várias janelas dentro do mesmo aplicativo.

### <a name="creating-the-xamarin-project"></a>Criando o projeto do Xamarin

Primeiro, vamos criar um novo documento com base no aplicativo Xamarin. Mac ao qual adicionaremos suporte para copiar e colar.

Faça o seguinte:

1. Inicie Visual Studio para Mac e clique no link **novo projeto...** .
2. Selecione aplicativo **Mac**  >  **App**  >  **Cocoa app**e, em seguida, clique no botão **Avançar** : 

    [![Criando um novo projeto de aplicativo Cocoa](copy-paste-images/sample01.png "Criando um novo projeto de aplicativo Cocoa")](copy-paste-images/sample01-large.png#lightbox)
3. Insira `MacCopyPaste` para o **nome do projeto** e mantenha tudo o mais como padrão. Clique em Avançar: 

    [![Definindo o nome do projeto](copy-paste-images/sample01a.png "Definindo o nome do projeto")](copy-paste-images/sample01a-large.png#lightbox)

4. Clique no botão **criar** : 

    [![Confirmando as novas configurações do projeto](copy-paste-images/sample02.png "Confirmando as novas configurações do projeto")](copy-paste-images/sample02-large.png#lightbox)

### <a name="add-an-nsdocument"></a>Adicionar um NSDocument

Em seguida, adicionaremos `NSDocument` uma classe personalizada que atuará como o armazenamento em segundo plano para a interface do usuário do aplicativo. Ele conterá uma única exibição de imagem e saberá como copiar uma imagem do modo de exibição para a área de entrada padrão e como tirar uma imagem da área de colagem padrão e exibi-la na exibição de imagem.

Clique com o botão direito do mouse no projeto Xamarin. Mac na **painel de soluções** e selecione **Adicionar**  >  **novo arquivo..**:

![Adicionando um NSDocument ao projeto](copy-paste-images/sample03.png "Adicionando um NSDocument ao projeto")

Digite `ImageDocument` para o **Nome** e clique no botão **Novo**. Edite a classe **ImageDocument.cs** e faça com que ela fique parecida com a seguinte:

```csharp
using System;
using AppKit;
using Foundation;
using ObjCRuntime;

namespace MacCopyPaste
{
    [Register("ImageDocument")]
    public class ImageDocument : NSDocument
    {
        #region Computed Properties
        public NSImageView ImageView {get; set;}

        public ImageInfo Info { get; set; } = new ImageInfo();

        public bool ImageAvailableOnPasteboard {
            get {
                // Initialize the pasteboard
                NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
                Class [] classArray  = { new Class ("NSImage") };

                // Check to see if an image is on the pasteboard
                return pasteboard.CanReadObjectForClasses (classArray, null);
            }
        }
        #endregion

        #region Constructor
        public ImageDocument ()
        {
        }
        #endregion

        #region Public Methods
        [Export("CopyImage:")]
        public void CopyImage(NSObject sender) {

            // Grab the current image
            var image = ImageView.Image;

            // Anything to process?
            if (image != null) {
                // Get the standard pasteboard
                var pasteboard = NSPasteboard.GeneralPasteboard;

                // Empty the current contents
                pasteboard.ClearContents();

                // Add the current image to the pasteboard
                pasteboard.WriteObjects (new NSImage[] {image});

                // Save the custom data class to the pastebaord
                pasteboard.WriteObjects (new ImageInfo[] { Info });

                // Using a Pasteboard Item
                NSPasteboardItem item = new NSPasteboardItem();
                string[] writableTypes = {"public.text"};

                // Add a data provier to the item
                ImageInfoDataProvider dataProvider = new ImageInfoDataProvider (Info.Name, Info.ImageType);
                var ok = item.SetDataProviderForTypes (dataProvider, writableTypes);

                // Save to pasteboard
                if (ok) {
                    pasteboard.WriteObjects (new NSPasteboardItem[] { item });
                }
            }

        }

        [Export("PasteImage:")]
        public void PasteImage(NSObject sender) {

            // Initialize the pasteboard
            NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
            Class [] classArray  = { new Class ("NSImage") };

            bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
            if (ok) {
                // Read the image off of the pasteboard
                NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
                NSImage image = (NSImage)objectsToPaste[0];

                // Display the new image
                ImageView.Image = image;
            }

            Class [] classArray2 = { new Class ("ImageInfo") };
            ok = pasteboard.CanReadObjectForClasses (classArray2, null);
            if (ok) {
                // Read the image off of the pasteboard
                NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
                ImageInfo info = (ImageInfo)objectsToPaste[0];

            }

        }
        #endregion
    }
}
```

Vamos dar uma olhada em alguns dos códigos em detalhes abaixo.

O código a seguir fornece uma propriedade para testar a existência de dados de imagem na área de ti padrão, se uma imagem estiver disponível, se `true` for retornada `false` :

```csharp
public bool ImageAvailableOnPasteboard {
    get {
        // Initialize the pasteboard
        NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
        Class [] classArray  = { new Class ("NSImage") };

        // Check to see if an image is on the pasteboard
        return pasteboard.CanReadObjectForClasses (classArray, null);
    }
}
```

O código a seguir copia uma imagem da exibição de imagem anexada para a área de ti padrão:

```csharp
[Export("CopyImage:")]
public void CopyImage(NSObject sender) {

    // Grab the current image
    var image = ImageView.Image;

    // Anything to process?
    if (image != null) {
        // Get the standard pasteboard
        var pasteboard = NSPasteboard.GeneralPasteboard;

        // Empty the current contents
        pasteboard.ClearContents();

        // Add the current image to the pasteboard
        pasteboard.WriteObjects (new NSImage[] {image});

        // Save the custom data class to the pastebaord
        pasteboard.WriteObjects (new ImageInfo[] { Info });

        // Using a Pasteboard Item
        NSPasteboardItem item = new NSPasteboardItem();
        string[] writableTypes = {"public.text"};

        // Add a data provider to the item
        ImageInfoDataProvider dataProvider = new ImageInfoDataProvider (Info.Name, Info.ImageType);
        var ok = item.SetDataProviderForTypes (dataProvider, writableTypes);

        // Save to pasteboard
        if (ok) {
            pasteboard.WriteObjects (new NSPasteboardItem[] { item });
        }
    }

}
```

E o código a seguir cola uma imagem da área de entrada padrão e a exibe na exibição de imagem anexada (se a área de entrada contiver uma imagem válida):

```csharp
[Export("PasteImage:")]
public void PasteImage(NSObject sender) {

    // Initialize the pasteboard
    NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
    Class [] classArray  = { new Class ("NSImage") };

    bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
        NSImage image = (NSImage)objectsToPaste[0];

        // Display the new image
        ImageView.Image = image;
    }

    Class [] classArray2 = { new Class ("ImageInfo") };
    ok = pasteboard.CanReadObjectForClasses (classArray2, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
        ImageInfo info = (ImageInfo)objectsToPaste[0]
    }
}
```

Com este documento em vigor, criaremos a interface do usuário para o aplicativo Xamarin. Mac.

### <a name="building-the-user-interface"></a>Criando a interface do usuário

Clique duas vezes no arquivo **Main. Storyboard** para abri-lo no Xcode. Em seguida, adicione uma barra de ferramentas e uma imagem bem e configure-as da seguinte maneira:

[![Editando a barra de ferramentas](copy-paste-images/sample04.png "Editando a barra de ferramentas")](copy-paste-images/sample04-large.png#lightbox)

Adicione um **item da barra de ferramentas** copiar e colar imagem no lado esquerdo da barra de ferramentas. Vamos usá-las como atalhos para copiar e colar no menu Editar. Em seguida, adicione quatro **itens da barra de ferramentas de imagem** no lado direito da barra de ferramentas. Vamos usá-las para popular a imagem com algumas imagens padrão.

Para obter mais informações sobre como trabalhar com barras de ferramentas, consulte nossa documentação de [barras de ferramentas](~/mac/user-interface/toolbar.md) .

Em seguida, vamos expor as seguintes saídas e ações para nossos itens da barra de ferramentas e a imagem bem:

[![Criando saídas e ações](copy-paste-images/sample05.png "Criando saídas e ações")](copy-paste-images/sample05-large.png#lightbox)

Para obter mais informações sobre como trabalhar com saídas e ações, consulte a seção [saídas e ações](~/mac/get-started/hello-mac.md#outlets-and-actions) da nossa documentação do [Hello, do Mac](~/mac/get-started/hello-mac.md) .

### <a name="enabling-the-user-interface"></a>Habilitando a interface do usuário

Com a nossa interface do usuário criada no Xcode e nosso elemento de interface de usuário exposto por meio de saídas e ações, precisamos adicionar o código para habilitar a interface do usuário. Clique duas vezes no arquivo **ImageWindow.cs** na **painel de soluções** e faça com que ele se pareça com o seguinte:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacCopyPaste
{
    public partial class ImageWindow : NSWindow
    {
        #region Private Variables
        ImageDocument document;
        #endregion

        #region Computed Properties
        [Export ("Document")]
        public ImageDocument Document {
            get {
                return document;
            }
            set {
                WillChangeValue ("Document");
                document = value;
                DidChangeValue ("Document");
            }
        }

        public ViewController ImageViewController {
            get { return ContentViewController as ViewController; }
        }

        public NSImage Image {
            get {
                return ImageViewController.Image;
            }
            set {
                ImageViewController.Image = value;
            }
        }
        #endregion

        #region Constructor
        public ImageWindow (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Create a new document instance
            Document = new ImageDocument ();

            // Attach to image view
            Document.ImageView = ImageViewController.ContentView;
        }
        #endregion

        #region Public Methods
        public void CopyImage (NSObject sender)
        {
            Document.CopyImage (sender);
        }

        public void PasteImage (NSObject sender)
        {
            Document.PasteImage (sender);
        }

        public void ImageOne (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image01.jpg");

            // Set image info
            Document.Info.Name = "city";
            Document.Info.ImageType = "jpg";
        }

        public void ImageTwo (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image02.jpg");

            // Set image info
            Document.Info.Name = "theater";
            Document.Info.ImageType = "jpg";
        }

        public void ImageThree (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image03.jpg");

            // Set image info
            Document.Info.Name = "keyboard";
            Document.Info.ImageType = "jpg";
        }

        public void ImageFour (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image04.jpg");

            // Set image info
            Document.Info.Name = "trees";
            Document.Info.ImageType = "jpg";
        }
        #endregion
    }
}
```

Vamos dar uma olhada neste código em detalhes abaixo.

Primeiro, expomos uma instância da `ImageDocument` classe que criamos acima:

```csharp
private ImageDocument _document;
...

[Export ("Document")]
public ImageDocument Document {
    get { return _document; }
    set {
        WillChangeValue ("Document");
        _document = value;
        DidChangeValue ("Document");
    }
}
```

Usando `Export` o, `WillChangeValue` e `DidChangeValue` temos configurado a `Document` propriedade para permitir a codificação de dados e o código de chave no Xcode.

Também expõemos a imagem da imagem, bem adicionada à nossa interface do usuário no Xcode com a seguinte propriedade:

```csharp
public ViewController ImageViewController {
    get { return ContentViewController as ViewController; }
}

public NSImage Image {
    get {
        return ImageViewController.Image;
    }
    set {
        ImageViewController.Image = value;
    }
}
```

Quando a janela principal é carregada e exibida, criamos uma instância da nossa `ImageDocument` classe e anexamos o compartimento da imagem da interface do usuário a ela com o seguinte código:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Create a new document instance
    Document = new ImageDocument ();

    // Attach to image view
    Document.ImageView = ImageViewController.ContentView;
}
```

Por fim, em resposta ao usuário clicando nos itens da barra de ferramentas copiar e colar, chamamos a instância da `ImageDocument` classe para fazer o trabalho real:

```csharp
partial void CopyImage (NSObject sender) {
    Document.CopyImage(sender);
}

partial void PasteImage (Foundation.NSObject sender) {
    Document.PasteImage(sender);
}
```

### <a name="enabling-the-file-and-edit-menus"></a>Habilitando os menus arquivo e editar

A última coisa que precisamos fazer é habilitar o **novo** item de menu no menu **arquivo** (para criar novas instâncias de nossa janela principal) e habilitar os itens de menu **recortar**, **copiar** e **colar** no menu **Editar** .

Para habilitar o **novo** item de menu, edite o arquivo **AppDelegate.cs** e adicione o seguinte código:

```csharp
public int UntitledWindowCount { get; set;} =1;
...

[Export ("newDocument:")]
void NewDocument (NSObject sender) {
    // Get new window
    var storyboard = NSStoryboard.FromName ("Main", null);
    var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

    // Display
    controller.ShowWindow(this);

    // Set the title
    controller.Window.Title = (++UntitledWindowCount == 1) ? "untitled" : string.Format ("untitled {0}", UntitledWindowCount);
}
```

Para obter mais informações, consulte a seção [trabalhando com vários Windows](~/mac/user-interface/window.md) de nossa documentação do [Windows](~/mac/user-interface/window.md) .

Para habilitar os itens de menu **recortar**, **copiar** e **colar** , edite o arquivo **AppDelegate.cs** e adicione o seguinte código:

```csharp
[Export("copy:")]
void CopyImage (NSObject sender)
{
    // Get the main window
    var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;

    // Anything to do?
    if (window == null)
        return;

    // Copy the image to the clipboard
    window.Document.CopyImage (sender);
}

[Export("cut:")]
void CutImage (NSObject sender)
{
    // Get the main window
    var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;

    // Anything to do?
    if (window == null)
        return;

    // Copy the image to the clipboard
    window.Document.CopyImage (sender);

    // Clear the existing image
    window.Image = null;
}

[Export("paste:")]
void PasteImage (NSObject sender)
{
    // Get the main window
    var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;

    // Anything to do?
    if (window == null)
        return;

    // Paste the image from the clipboard
    window.Document.PasteImage (sender);
}
```

Para cada item de menu, obtemos a janela de chave atual, de nível superior e a convertemos em nossa `ImageWindow` classe:

```csharp
var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;
```

A partir daí, chamamos a `ImageDocument` instância da classe dessa janela para manipular as ações de copiar e colar. Por exemplo: 

```csharp
window.Document.CopyImage (sender);
```

Só queremos que os itens de menu **recortar**, **copiar** e **colar** fiquem acessíveis se houver dados de imagem na área de transferência padrão ou na imagem bem da janela ativa atual.

Vamos adicionar um arquivo **EditMenuDelegate.cs** ao projeto Xamarin. Mac e fazê-lo parecer o seguinte:

```csharp
using System;
using AppKit;

namespace MacCopyPaste
{
    public class EditMenuDelegate : NSMenuDelegate
    {
        #region Override Methods
        public override void MenuWillHighlightItem (NSMenu menu, NSMenuItem item)
        {
        }

        public override void NeedsUpdate (NSMenu menu)
        {
            // Get list of menu items
            NSMenuItem[] Items = menu.ItemArray ();

            // Get the key window and determine if the required images are available
            var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;
            var hasImage = (window != null) && (window.Image != null);
            var hasImageOnPasteboard = (window != null) && window.Document.ImageAvailableOnPasteboard;

            // Process every item in the menu
            foreach(NSMenuItem item in Items) {
                // Take action based on the menu title
                switch (item.Title) {
                case "Cut":
                case "Copy":
                case "Delete":
                    // Only enable if there is an image in the view
                    item.Enabled = hasImage;
                    break;
                case "Paste":
                    // Only enable if there is an image on the pasteboard
                    item.Enabled = hasImageOnPasteboard;
                    break;
                default:
                    // Only enable the item if it has a sub menu
                    item.Enabled = item.HasSubmenu;
                    break;
                }
            }
        }
        #endregion
    }
}
```

Novamente, obtemos a janela atual e a mais alta e usamos sua `ImageDocument` instância de classe para ver se os dados de imagem necessários existem. Em seguida, usamos o `MenuWillHighlightItem` método para habilitar ou desabilitar cada item com base nesse estado.

Edite o arquivo **AppDelegate.cs** e faça com que o `DidFinishLaunching` método se pareça com o seguinte:

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    // Disable automatic item enabling on the Edit menu
    EditMenu.AutoEnablesItems = false;
    EditMenu.Delegate = new EditMenuDelegate ();
}
```

Primeiro, desativamos a habilitação e desabilitação automática de itens de menu no menu Editar. Em seguida, anexamos uma instância da `EditMenuDelegate` classe que criamos acima.

Para obter mais informações, consulte a documentação de nossos [menus](~/mac/user-interface/menu.md) .

### <a name="testing-the-app"></a>Testando o aplicativo

Com tudo em vigor, estamos prontos para testar o aplicativo. Compile e execute o aplicativo e a interface principal é exibida:

![Executando o aplicativo](copy-paste-images/run01.png "Executando o aplicativo")

Se você abrir o menu Editar, observe que **recortar**, **copiar** e **colar** estão desabilitados porque não há nenhuma imagem na imagem bem ou na área de transferência padrão:

![Abrindo o menu Editar](copy-paste-images/run02.png "Abrindo o menu Editar")

Se você adicionar uma imagem à imagem bem e reabrir o menu Editar, os itens agora serão habilitados:

![A exibição dos itens do menu Editar está habilitada](copy-paste-images/run03.png "A exibição dos itens do menu Editar está habilitada")

Se você copiar a imagem e selecionar **novo** no menu arquivo, poderá colar essa imagem na nova janela:

![Colando uma imagem em uma nova janela](copy-paste-images/run04.png "Colando uma imagem em uma nova janela")

Nas seções a seguir, vamos examinar detalhadamente como trabalhar com a área de trabalho em um aplicativo Xamarin. Mac.

## <a name="about-the-pasteboard"></a>Sobre a área de ti

No macOS (anteriormente conhecido como OS X), a área de ( `NSPasteboard` ) fornece suporte para vários processos de servidor, como copiar & colar, arrastar & soltar e serviços de aplicativos. Nas seções a seguir, examinaremos com mais detalhes vários conceitos principais da área de entrada.

### <a name="what-is-a-pasteboard"></a>O que é uma área de ti?

A `NSPasteboard` classe fornece um mecanismo padronizado para troca de informações entre aplicativos ou dentro de um determinado aplicativo. A principal função de uma área de colagem é o tratamento de operações de copiar e colar:

1. Quando o usuário seleciona um item em um aplicativo e usa o item de menu **recortar** ou **copiar** , uma ou mais representações do item selecionado são colocadas na área de transferência.
2. Quando o usuário usa o item de menu **colar** (dentro do mesmo aplicativo ou outro), a versão dos dados que ele pode manipular é copiada da área de os e adicionada ao aplicativo.

As utilizações de área de armazenamento menos óbvias incluem localizar, arrastar, arrastar e soltar e operações de serviços de aplicativos:

- Quando o usuário inicia uma operação de arrastar, os dados de arrastar são copiados para a área de ti. Se a operação de arrastar terminar com uma queda em outro aplicativo, esse aplicativo copiará os dados da área de armazenamento.
- Para serviços de tradução, os dados a serem traduzidos são copiados para a área de transferência pelo aplicativo solicitante. O serviço de aplicativo recupera os dados da área de transferência, faz a tradução e cola os dados de volta na área de transferência.

Em sua forma mais simples, pasteboards são usados para mover dados dentro de um determinado aplicativo ou entre aplicativos e, portanto, existem em uma área de memória global especial fora do processo do aplicativo. Embora os conceitos do pasteboards sejam facilmente compreendidos, há vários detalhes mais complexos que devem ser considerados. Eles serão abordados em detalhes abaixo.

### <a name="named-pasteboards"></a>Chamado pasteboards

Uma área de ti pode ser pública ou privada e pode ser usada para uma variedade de finalidades em um aplicativo ou entre vários aplicativos. o macOS fornece vários pasteboards padrão, cada um com um uso específico e bem definido:

- `NSGeneralPboard` -A área de transferência padrão para operações de **recortar**, **copiar** e **colar** .
- `NSRulerPboard` – Dá suporte a operações de **recortar**, **copiar** e **colar** em **réguas**.
- `NSFontPboard` – Dá suporte a operações de **recortar**, **copiar** e **colar** em `NSFont` objetos.
- `NSFindPboard` – Dá suporte a painéis de localização específicos do aplicativo que podem compartilhar texto de pesquisa.
- `NSDragPboard` – Dá suporte a operações de **arrastar & soltar** .

Na maioria das situações, você usará um dos pasteboards definidos pelo sistema. Mas pode haver situações que exijam que você crie seu próprio pasteboards. Nessas situações, você pode usar o `FromName (string name)` método da `NSPasteboard` classe para criar uma área de dados personalizada com o nome fornecido.

Opcionalmente, você pode chamar o `CreateWithUniqueName` método da `NSPasteboard` classe para criar uma área de colagem nomeada exclusivamente.

### <a name="pasteboard-items"></a>Itens da área de ti

Cada parte dos dados que um aplicativo grava em uma área de trabalho é considerada um _item da área_ de trabalho e uma área de trabalho pode conter vários itens ao mesmo tempo. Dessa forma, um aplicativo pode gravar várias versões dos dados que estão sendo copiados para uma área de ti (por exemplo, texto sem formatação e texto formatado) e o aplicativo de recuperação pode ler apenas os dados que ele pode processar (como somente texto sem formatação).

### <a name="data-representations-and-uniform-type-identifiers"></a>Representações de dados e identificadores de tipo uniformes

Normalmente, as operações da área de ti ocorrem entre dois (ou mais) aplicativos que não têm conhecimento um do outro ou os tipos de dados que cada um pode manipular. Conforme indicado na seção acima, para maximizar o potencial de compartilhamento de informações, uma área de colagem pode manter várias representações dos dados que estão sendo copiados e colados.

Cada representação é identificada por meio de um identificador de tipo uniforme (UTI), que não é nada mais do que uma cadeia de caracteres simples que identifica exclusivamente o tipo de data que está sendo apresentado (para obter mais informações, consulte a documentação de [visão geral dos identificadores de tipo uniforme](https://developer.apple.com/library/prerelease/mac/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_intro/understand_utis_intro.html#//apple_ref/doc/uid/TP40001319) da Apple). 

Se você estiver criando um tipo de dados personalizado (por exemplo, um objeto de desenho em um aplicativo de desenho vetorial), poderá criar seu próprio UTI para identificá-lo exclusivamente em operações de copiar e colar.

Quando um aplicativo se prepara para colar os dados copiados de uma área de ti, ele deve encontrar a representação que melhor se adapta às suas capacidades (se houver). Normalmente, esse será o tipo mais rico disponível (por exemplo, texto formatado para um aplicativo de processamento de texto), voltando para os formulários mais simples disponíveis conforme necessário (texto sem formatação para um editor de texto simples).

<a name="Promised_Data"></a>

### <a name="promised-data"></a>Dados prometidos

Em termos gerais, você deve fornecer tantas representações dos dados sendo copiados quanto possível para maximizar o compartilhamento entre aplicativos. No entanto, devido a restrições de tempo ou de memória, pode ser impraticável realmente gravar cada tipo de dados na área de colagem.

Nessa situação, você pode colocar a primeira representação de dados na área de ti e o aplicativo de recebimento pode solicitar uma representação diferente, que pode ser gerada imediatamente antes da operação de colagem.

Quando você coloca o item inicial na área de entrada, você especifica que uma ou mais das outras representações disponíveis são fornecidas por um objeto que está de acordo com a `NSPasteboardItemDataProvider` interface. Esses objetos fornecerão as representações adicionais sob demanda, conforme solicitado pelo aplicativo de recebimento.

### <a name="change-count"></a>Contagem de alterações

Cada área de colagem mantém uma _contagem de alteração_ que aumenta cada vez que um novo proprietário é declarado. Um aplicativo pode determinar se o conteúdo da área de ti foi alterado desde a última vez que o examinou verificando o valor da contagem de alterações.

Use os `ChangeCount` `ClearContents` métodos e da `NSPasteboard` classe para modificar a contagem de alteração de uma determinada área de dados.

## <a name="copying-data-to-a-pasteboard"></a>Copiando dados para uma área de ti

Você executa uma operação de cópia acessando primeiro uma área de ti, limpando qualquer conteúdo existente e gravando quantas representações dos dados forem necessárias para a área de apresentação.

Por exemplo:

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Empty the current contents
pasteboard.ClearContents();

// Add the current image to the pasteboard
pasteboard.WriteObjects (new NSImage[] {image});
```

Normalmente, você estará apenas escrevendo para a área de trabalho geral, como fizemos no exemplo acima. Qualquer objeto que você enviar para o `WriteObjects` método *deve* estar de acordo com a `INSPasteboardWriting` interface. Várias classes internas (como,,,, `NSString` `NSImage` `NSURL` `NSColor` `NSAttributedString` e `NSPasteboardItem` ) estão automaticamente em conformidade com essa interface.

Se você estiver escrevendo uma classe de dados personalizada na área de ti, ela deverá estar em conformidade com a `INSPasteboardWriting` interface ou ser encapsulada em uma instância da `NSPasteboardItem` classe (consulte a seção [tipos de dados personalizados](#Custom_Data_Types) abaixo).

## <a name="reading-data-from-a-pasteboard"></a>Lendo dados de uma área de ti

Conforme mencionado acima, para maximizar o potencial de compartilhamento de dados entre aplicativos, várias representações dos dados copiados podem ser gravadas na área de ti. Cabe ao aplicativo de recebimento selecionar a versão mais rica possível para seus recursos (se existir).

### <a name="simple-paste-operation"></a>Operação de colar simples

Você lê os dados da área de ti usando o `ReadObjectsForClasses` método. Será necessário dois parâmetros:

1. Uma matriz de `NSObject` tipos de classe com base que você deseja ler na área de colagem. Você deve solicitar isso com o tipo de dados mais desejado primeiro, com os tipos restantes em decrescente preferência.
2. Um dicionário que contém restrições adicionais (como limitar a tipos de conteúdo de URL específicos) ou um dicionário vazio se nenhuma restrição adicional for necessária.

O método retorna uma matriz de itens que atendem aos critérios que passamos e, portanto, contém, no máximo, o mesmo número de tipos de dados que são solicitados. também é possível que nenhum dos tipos solicitados esteja presente e uma matriz vazia será retornada.

Por exemplo, o código a seguir verifica se existe um `NSImage` existente na área de ti geral e o exibe em uma imagem bem se ela:

```csharp
[Export("PasteImage:")]
public void PasteImage(NSObject sender) {

    // Initialize the pasteboard
    NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
    Class [] classArray  = { new Class ("NSImage") };

    bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
        NSImage image = (NSImage)objectsToPaste[0];

        // Display the new image
        ImageView.Image = image;
    }

    Class [] classArray2 = { new Class ("ImageInfo") };
    ok = pasteboard.CanReadObjectForClasses (classArray2, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
        ImageInfo info = (ImageInfo)objectsToPaste[0];
            }
}
```

### <a name="requesting-multiple-data-types"></a>Solicitando vários tipos de dados

Com base no tipo de aplicativo Xamarin. Mac que está sendo criado, pode ser capaz de lidar com várias representações dos dados que estão sendo colados. Nessa situação, há dois cenários para recuperar dados da área de ti:

1. Faça uma única chamada para o `ReadObjectsForClasses` método e forneça uma matriz de todas as representações que você deseja (na ordem preferida).
2. Faça várias chamadas para o `ReadObjectsForClasses` método solicitando uma matriz de tipos diferente a cada vez.

Consulte a seção **operação de colagem simples** acima para obter mais detalhes sobre como recuperar dados de uma área de ti.

### <a name="checking-for-existing-data-types"></a>Verificando tipos de dados existentes

Há ocasiões em que você pode querer verificar se uma área de dados contém uma determinada representação, sem realmente ler os dados da área de folga (como habilitar o item de menu **colar** somente quando houver dados válidos).

Chame o `CanReadObjectForClasses` método da área de colagem para ver se ele contém um determinado tipo.

Por exemplo, o código a seguir determina se a área de ti geral contém uma `NSImage` instância:

```csharp
public bool ImageAvailableOnPasteboard {
    get {
        // Initialize the pasteboard
        NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
        Class [] classArray  = { new Class ("NSImage") };

        // Check to see if an image is on the pasteboard
        return pasteboard.CanReadObjectForClasses (classArray, null);
    }
}
```

### <a name="reading-urls-from-the-pasteboard"></a>Lendo URLs na área de ti

Com base na função de um determinado aplicativo Xamarin. Mac, pode ser necessário ler URLs de uma área de dados, mas somente se eles atenderem a um determinado conjunto de critérios (como apontar para arquivos ou URLs de um tipo de dados específico). Nessa situação, você pode especificar critérios de pesquisa adicionais usando o segundo parâmetro dos `CanReadObjectForClasses` métodos ou `ReadObjectsForClasses` .

<a name="Custom_Data_Types"></a>

## <a name="custom-data-types"></a>Tipos de dados personalizados

Há ocasiões em que você precisará salvar seus próprios tipos personalizados na área de colagem de um aplicativo Xamarin. Mac. Por exemplo, um aplicativo de desenho de vetor que permite ao usuário copiar e colar objetos de desenho.

Nessa situação, você precisará criar sua classe personalizada de dados para que ela seja herdada `NSObject` e esteja de acordo com algumas interfaces ( `INSCoding` `INSPasteboardWriting` e `INSPasteboardReading` ). Opcionalmente, você pode usar um `NSPasteboardItem` para encapsular os dados a serem copiados ou colados.

Essas duas opções serão abordadas em detalhes abaixo.

### <a name="using-a-custom-class"></a>Usando uma classe personalizada

Nesta seção, vamos expandir o aplicativo de exemplo simples que criamos no início deste documento e adicionando uma classe personalizada para rastrear informações sobre a imagem que estamos copiando e colando entre o Windows.

Adicione uma nova classe ao projeto e chame-a de **IMAGEINFO.cs**. Edite o arquivo e faça com que ele se pareça com o seguinte:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacCopyPaste
{
    [Register("ImageInfo")]
    public class ImageInfo : NSObject, INSCoding, INSPasteboardWriting, INSPasteboardReading
    {
        #region Computed Properties
        [Export("name")]
        public string Name { get; set; }

        [Export("imageType")]
        public string ImageType { get; set; }
        #endregion

        #region Constructors
        [Export ("init")]
        public ImageInfo ()
        {
        }
        
        public ImageInfo (IntPtr p) : base (p)
        {
        }

        [Export ("initWithCoder:")]
        public ImageInfo(NSCoder decoder) {

            // Decode data
            NSString name = decoder.DecodeObject("name") as NSString;
            NSString type = decoder.DecodeObject("imageType") as NSString;

            // Save data
            Name = name.ToString();
            ImageType = type.ToString ();
        }
        #endregion

        #region Public Methods
        [Export ("encodeWithCoder:")]
        public void EncodeTo (NSCoder encoder) {

            // Encode data
            encoder.Encode(new NSString(Name),"name");
            encoder.Encode(new NSString(ImageType),"imageType");
        }

        [Export ("writableTypesForPasteboard:")]
        public virtual string[] GetWritableTypesForPasteboard (NSPasteboard pasteboard) {
            string[] writableTypes = {"com.xamarin.image-info", "public.text"};
            return writableTypes;
        }

        [Export ("pasteboardPropertyListForType:")]
        public virtual NSObject GetPasteboardPropertyListForType (string type) {

            // Take action based on the requested type
            switch (type) {
            case "com.xamarin.image-info":
                return NSKeyedArchiver.ArchivedDataWithRootObject(this);
            case "public.text":
                return new NSString(string.Format("{0}.{1}", Name, ImageType));
            }

            // Failure, return null
            return null;
        }

        [Export ("readableTypesForPasteboard:")]
        public static string[] GetReadableTypesForPasteboard (NSPasteboard pasteboard){
            string[] readableTypes = {"com.xamarin.image-info", "public.text"};
            return readableTypes;
        }

        [Export ("readingOptionsForType:pasteboard:")]
        public static NSPasteboardReadingOptions GetReadingOptionsForType (string type, NSPasteboard pasteboard) {

            // Take action based on the requested type
            switch (type) {
            case "com.xamarin.image-info":
                return NSPasteboardReadingOptions.AsKeyedArchive;
            case "public.text":
                return NSPasteboardReadingOptions.AsString;
            }

            // Default to property list
            return NSPasteboardReadingOptions.AsPropertyList;
        }

        [Export ("initWithPasteboardPropertyList:ofType:")]
        public NSObject InitWithPasteboardPropertyList (NSObject propertyList, string type) {

            // Take action based on the requested type
            switch (type) {
            case "com.xamarin.image-info":
                return new ImageInfo();
            case "public.text":
                return new ImageInfo();
            }

            // Failure, return null
            return null;
        }
        #endregion
    }
}
    
```

Nas seções a seguir, vamos dar uma olhada detalhada nessa classe.

#### <a name="inheritance-and-interfaces"></a>Herança e interfaces

Antes que uma classe de dados personalizada possa ser gravada ou lida em uma área de ti, ela deve estar em conformidade com as `INSPastebaordWriting` `INSPasteboardReading` interfaces e. Além disso, ele deve herdar de `NSObject` e também estar em conformidade com a `INSCoding` interface:

```csharp
[Register("ImageInfo")]
public class ImageInfo : NSObject, INSCoding, INSPasteboardWriting, INSPasteboardReading
...
```

A classe também deve ser exposta a Objective-C usando a `Register` diretiva e deve expor quaisquer propriedades ou métodos necessários usando o `Export` . Por exemplo:

```csharp
[Export("name")]
public string Name { get; set; }

[Export("imageType")]
public string ImageType { get; set; }
```

Estamos expondo os dois campos de dados que essa classe conterá: o nome da imagem e seu tipo (jpg, png, etc.). 

Para obter mais informações, consulte a seção [expondo classes/métodos C# para Objective-C](~/mac/internals/how-it-works.md) da documentação [interna do Xamarin. Mac](~/mac/internals/how-it-works.md) , ele explica `Register` os `Export` atributos e usados para conectar suas classes C# a objetos Objective-C e elementos de interface do usuário.

#### <a name="constructors"></a>Construtores

Dois construtores (adequadamente expostos a Objective-C) serão necessários para nossa classe de dados personalizada para que possam ser lidos de uma área de ti:

```csharp
[Export ("init")]
public ImageInfo ()
{
}

[Export ("initWithCoder:")]
public ImageInfo(NSCoder decoder) {

    // Decode data
    NSString name = decoder.DecodeObject("name") as NSString;
    NSString type = decoder.DecodeObject("imageType") as NSString;

    // Save data
    Name = name.ToString();
    ImageType = type.ToString ();
}
```

Primeiro, expõemos o construtor _vazio_ no método Objective-C padrão de `init` .

Em seguida, expõemos um `NSCoding` Construtor compatível que será usado para criar uma nova instância do objeto da área de colagem ao colar sob o nome exportado de `initWithCoder` .

Esse construtor usa um `NSCoder` (conforme criado por um `NSKeyedArchiver` quando gravado na área de dados), extrai os dados emparelhados de chave/valor e salva-os nos campos de propriedade da classe data.

#### <a name="writing-to-the-pasteboard"></a>Gravando na área de colagem

Ao estar em conformidade com a `INSPasteboardWriting` interface, precisamos expor dois métodos e, opcionalmente, um terceiro método, para que a classe possa ser gravada na área de ti.

Primeiro, precisamos dizer à área de os quais representações de tipo de dados para as quais a classe personalizada pode ser gravada:

```csharp
[Export ("writableTypesForPasteboard:")]
public virtual string[] GetWritableTypesForPasteboard (NSPasteboard pasteboard) {
    string[] writableTypes = {"com.xamarin.image-info", "public.text"};
    return writableTypes;
}
```

Cada representação é identificada por meio de um identificador de tipo uniforme (UTI), que não é nada mais do que uma cadeia de caracteres simples que identifica exclusivamente o tipo de dados apresentado (para obter mais informações, consulte a documentação de [visão geral dos identificadores de tipo uniforme](https://developer.apple.com/library/prerelease/mac/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_intro/understand_utis_intro.html#//apple_ref/doc/uid/TP40001319) da Apple).

Para nosso formato personalizado, estamos criando nosso próprio UTI: "com. xamarin. Image-Info" (Observe que está em notação inversa, exatamente como um identificador de aplicativo). Nossa classe também é capaz de gravar uma cadeia de caracteres padrão na área de ( `public.text` ). 

Em seguida, precisamos criar o objeto no formato solicitado que realmente é gravado na área de entrada:

```csharp
[Export ("pasteboardPropertyListForType:")]
public virtual NSObject GetPasteboardPropertyListForType (string type) {

    // Take action based on the requested type
    switch (type) {
    case "com.xamarin.image-info":
        return NSKeyedArchiver.ArchivedDataWithRootObject(this);
    case "public.text":
        return new NSString(string.Format("{0}.{1}", Name, ImageType));
    }

    // Failure, return null
    return null;
}
```

Para o `public.text` tipo, estamos retornando um objeto simples e formatado `NSString` . Para o `com.xamarin.image-info` tipo personalizado, estamos usando um `NSKeyedArchiver` e a `NSCoder` interface para codificar a classe de dados personalizada para um arquivo de chave/valor emparelhado. Precisaremos implementar o método a seguir para realmente lidar com a codificação:

```csharp
[Export ("encodeWithCoder:")]
public void EncodeTo (NSCoder encoder) {

    // Encode data
    encoder.Encode(new NSString(Name),"name");
    encoder.Encode(new NSString(ImageType),"imageType");
}
```

Os pares chave/valor individuais são gravados no codificador e serão decodificados usando o segundo construtor que adicionamos acima.

Opcionalmente, podemos incluir o seguinte método para definir qualquer opção ao gravar dados na área de ti:

```csharp
[Export ("writingOptionsForType:pasteboard:"), CompilerGenerated]
public virtual NSPasteboardWritingOptions GetWritingOptionsForType (string type, NSPasteboard pasteboard) {
    return NSPasteboardWritingOptions.WritingPromised;
}
```

Atualmente, apenas a `WritingPromised` opção está disponível e deve ser usada quando um determinado tipo é prometido apenas e não é gravado na área de dados. Para obter mais informações, consulte a seção [dados prometidos](#Promised_Data) acima.

Com esses métodos em vigor, o código a seguir pode ser usado para escrever nossa classe personalizada na área de entrada:

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Empty the current contents
pasteboard.ClearContents();

// Add info to the pasteboard
pasteboard.WriteObjects (new ImageInfo[] { Info });
```

#### <a name="reading-from-the-pasteboard"></a>Lendo na área de ti

Ao estar em conformidade com a `INSPasteboardReading` interface, precisamos expor três métodos para que a classe de dados personalizada possa ser lida na área de colagem.

Primeiro, precisamos informar à área de os quais representações de tipo de dados que a classe personalizada pode ler da área de transferência:

```csharp
[Export ("readableTypesForPasteboard:")]
public static string[] GetReadableTypesForPasteboard (NSPasteboard pasteboard){
    string[] readableTypes = {"com.xamarin.image-info", "public.text"};
    return readableTypes;
}
```

Novamente, elas são definidas como UTIs simples e são os mesmos tipos que definimos na seção **escrevendo na área de** ti acima.

Em seguida, precisamos dizer à área de ti _como_ cada um dos tipos de UTI será lido usando o seguinte método:

```csharp
[Export ("readingOptionsForType:pasteboard:")]
public static NSPasteboardReadingOptions GetReadingOptionsForType (string type, NSPasteboard pasteboard) {

    // Take action based on the requested type
    switch (type) {
    case "com.xamarin.image-info":
        return NSPasteboardReadingOptions.AsKeyedArchive;
    case "public.text":
        return NSPasteboardReadingOptions.AsString;
    }

    // Default to property list
    return NSPasteboardReadingOptions.AsPropertyList;
}
```

Para o `com.xamarin.image-info` tipo, estamos dizendo à área de colagem para decodificar o par de chave/valor que criamos com o `NSKeyedArchiver` ao gravar a classe na área de os chamando o `initWithCoder:` Construtor que adicionamos à classe.

Finalmente, precisamos adicionar o seguinte método para ler as outras representações de dados do UTI da área de ti:

```csharp
[Export ("initWithPasteboardPropertyList:ofType:")]
public NSObject InitWithPasteboardPropertyList (NSObject propertyList, string type) {

    // Take action based on the requested type
    switch (type) {
    case "public.text":
        return new ImageInfo();
    }

    // Failure, return null
    return null;
}
```

Com todos esses métodos em vigor, a classe de dados personalizada pode ser lida na área de entrada usando o seguinte código:

```csharp
// Initialize the pasteboard
NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
var classArrayPtrs = new [] { Class.GetHandle (typeof(ImageInfo)) };
NSArray classArray = NSArray.FromIntPtrs (classArrayPtrs);

// NOTE: Sending messages directly to the base Objective-C API because of this defect:
// https://bugzilla.xamarin.com/show_bug.cgi?id=31760
// Check to see if image info is on the pasteboard
ok = bool_objc_msgSend_IntPtr_IntPtr (pasteboard.Handle, Selector.GetHandle ("canReadObjectForClasses:options:"), classArray.Handle, IntPtr.Zero);

if (ok) {
    // Read the image off of the pasteboard
    NSObject [] objectsToPaste = NSArray.ArrayFromHandle<Foundation.NSObject>(IntPtr_objc_msgSend_IntPtr_IntPtr (pasteboard.Handle, Selector.GetHandle ("readObjectsForClasses:options:"), classArray.Handle, IntPtr.Zero));
    ImageInfo info = (ImageInfo)objectsToPaste[0];
}
```

### <a name="using-a-nspasteboarditem"></a>Usando um NSPasteboardItem

Pode haver ocasiões em que você precisa escrever itens personalizados na área de dados que não garantem a criação de uma classe personalizada ou que você queira fornecer dados em um formato comum, somente conforme necessário. Para essas situações, você pode usar um `NSPasteboardItem` .

Um `NSPasteboardItem` fornece controle refinado sobre os dados que são gravados na área de ti e é projetado para acesso temporário-ele deve ser descartado depois de ser gravado na área de dados.

#### <a name="writing-data"></a>Gravação de dados

Para gravar seus dados personalizados em um `NSPasteboardItem` , você precisará fornecer um personalizado `NSPasteboardItemDataProvider` . Adicione uma nova classe ao projeto e chame-a de **ImageInfoDataProvider.cs**. Edite o arquivo e faça com que ele se pareça com o seguinte:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacCopyPaste
{
    [Register("ImageInfoDataProvider")]
    public class ImageInfoDataProvider : NSPasteboardItemDataProvider
    {
        #region Computed Properties
        public string Name { get; set;}
        public string ImageType { get; set;}
        #endregion

        #region Constructors
        [Export ("init")]
        public ImageInfoDataProvider ()
        {
        }

        public ImageInfoDataProvider (string name, string imageType)
        {
            // Initialize
            this.Name = name;
            this.ImageType = imageType;
        }

        protected ImageInfoDataProvider (NSObjectFlag t){
        }

        protected internal ImageInfoDataProvider (IntPtr handle){

        }
        #endregion

        #region Override Methods
        [Export ("pasteboardFinishedWithDataProvider:")]
        public override void FinishedWithDataProvider (NSPasteboard pasteboard)
        {
            
        }

        [Export ("pasteboard:item:provideDataForType:")]
        public override void ProvideDataForType (NSPasteboard pasteboard, NSPasteboardItem item, string type)
        {

            // Take action based on the type
            switch (type) {
            case "public.text":
                // Encode the data to string 
                item.SetStringForType(string.Format("{0}.{1}", Name, ImageType),type);
                break;
            }

        }
        #endregion
    }
}
```

Como fizemos com a classe de dados personalizada, precisamos usar as `Register` `Export` diretivas e para expô-la ao Objective-C. A classe deve herdar `NSPasteboardItemDataProvider` e deve implementar os `FinishedWithDataProvider` `ProvideDataForType` métodos e.

Use o `ProvideDataForType` método para fornecer os dados que serão encapsulados no da `NSPasteboardItem` seguinte maneira:

```csharp
[Export ("pasteboard:item:provideDataForType:")]
public override void ProvideDataForType (NSPasteboard pasteboard, NSPasteboardItem item, string type)
{

    // Take action based on the type
    switch (type) {
    case "public.text":
        // Encode the data to string 
        item.SetStringForType(string.Format("{0}.{1}", Name, ImageType),type);
        break;
    }

}
```

Nesse caso, estamos armazenando duas partes de informações sobre nossa imagem (Name e ImageType) e gravando-as em uma cadeia de caracteres simples ( `public.text` ).

Digite gravar os dados na área de colagem, use o seguinte código:

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Using a Pasteboard Item
NSPasteboardItem item = new NSPasteboardItem();
string[] writableTypes = {"public.text"};

// Add a data provider to the item
ImageInfoDataProvider dataProvider = new ImageInfoDataProvider (Info.Name, Info.ImageType);
var ok = item.SetDataProviderForTypes (dataProvider, writableTypes);

// Save to pasteboard
if (ok) {
    pasteboard.WriteObjects (new NSPasteboardItem[] { item });
}
```

#### <a name="reading-data"></a>Lendo dados

Para ler os dados de volta da área de ti, use o seguinte código:

```csharp
// Initialize the pasteboard
NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
Class [] classArray  = { new Class ("NSImage") };

bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
if (ok) {
    // Read the image off of the pasteboard
    NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
    NSImage image = (NSImage)objectsToPaste[0];

    // Do something with data
    ...
}
            
Class [] classArray2 = { new Class ("ImageInfo") };
ok = pasteboard.CanReadObjectForClasses (classArray2, null);
if (ok) {
    // Read the image off of the pasteboard
    NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
    
    // Do something with data
    ...
}
```

## <a name="summary"></a>Resumo

Este artigo deu uma visão detalhada de como trabalhar com a área de trabalho em um aplicativo Xamarin. Mac para dar suporte a operações de cópia e colagem. Primeiro, ele introduziu um exemplo simples para familiarizar-se com as operações padrão do pasteboards. Em seguida, precisou de uma visão detalhada da área de dados e de como ler e gravar os dados dela. Por fim, ele examinou o uso de um tipo de dados personalizado para dar suporte à cópia e colagem de tipos de dados complexos em um aplicativo.

## <a name="related-links"></a>Links Relacionados

- [MacCopyPaste (exemplo)](/samples/xamarin/mac-samples/maccopypaste)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Guia de programação da área de ti](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/PasteboardGuide106/Articles/pbGettingStarted.html)
- [Diretrizes de Interface Humana do macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)