---
title: Copie e cole no xamarin. Mac
description: Este artigo aborda o trabalho com a área de trabalho para fornecer a copiar e colar em um aplicativo xamarin. Mac. Ele mostra como trabalhar com tipos de dados padrão que podem ser compartilhados entre vários aplicativos e como dar suporte a dados personalizados dentro de um determinado aplicativo.
ms.prod: xamarin
ms.assetid: 7E9C99FB-B7B4-4C48-B20F-84CB48543083
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: f9e05b6d16210021257fe3958966739e526aed18
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123179"
---
# <a name="copy-and-paste-in-xamarinmac"></a>Copie e cole no xamarin. Mac

_Este artigo aborda o trabalho com a área de trabalho para fornecer a copiar e colar em um aplicativo xamarin. Mac. Ele mostra como trabalhar com tipos de dados padrão que podem ser compartilhados entre vários aplicativos e como dar suporte a dados personalizados dentro de um determinado aplicativo._

## <a name="overview"></a>Visão geral

Ao trabalhar com c# e .NET em um aplicativo xamarin. Mac, você tem acesso para a mesma área de trabalho (copiar e colar) o suporte que tem um desenvolvedor que trabalha em Objective-C.

Neste artigo vamos abordar duas principais maneiras de se usar a área de trabalho em um aplicativo xamarin. Mac:

1. **Tipos de dados padrão** -uma vez que as operações de área de trabalho são normalmente realizadas entre dois aplicativos não relacionados, nenhum aplicativo sabe que os tipos de dados com suporte para o outro. Para maximizar o potencial de compartilhamento, a área de trabalho pode conter várias representações de um determinado item (usando um conjunto padrão de tipos de dados comuns), isso permite que o aplicativo de consumo escolher a versão que é mais adequada para suas necessidades.
2. **Dados personalizados** - para dar suporte a cópia e colagem de dados complexos dentro do seu xamarin. MAC que você pode definir um tipo de dados personalizados que é manipulado pela área de trabalho. Por exemplo, um aplicativo de desenho de vetor que permite ao usuário copiar e colar formas complexas que são compostas de vários tipos de dados e pontos.

[![Exemplo de aplicativo em execução](copy-paste-images/intro01.png "exemplo de aplicativo em execução")](copy-paste-images/intro01-large.png#lightbox)

Neste artigo, abordaremos os fundamentos de trabalhar com a área de trabalho em um aplicativo xamarin. Mac para dar suporte a copiar e colar operações. É altamente recomendável que você trabalhe por meio de [Hello, Mac](~/mac/get-started/hello-mac.md) pela primeira vez, especificamente o artigo a [Introdução ao Interface Builder e Xcode](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [saídas e ações](~/mac/get-started/hello-mac.md#outlets-and-actions) seções, como ela aborda os principais conceitos e técnicas que usaremos neste artigo.

Talvez você queira dar uma olhada o [classes expondo c# / métodos para Objective-C](~/mac/internals/how-it-works.md) seção o [recursos internos de xamarin. Mac](~/mac/internals/how-it-works.md) documentar Além disso, ele explica o `Register` e `Export` atributos usado para conectar suas classes de c# a objetos de Objective-C e da interface do usuário elementos.

## <a name="getting-started-with-the-pasteboard"></a>Introdução à área de trabalho

A área de trabalho apresenta um mecanismo padronizado para troca de dados em um determinado aplicativo ou entre aplicativos. O uso típico para uma área de trabalho em um aplicativo xamarin. MAC é lidar com copiar e colar operações, no entanto, também há suporte para um número de outras operações (por exemplo, arrastar e soltar e serviços do aplicativo).

Para começar a funcionar rapidamente, vamos começar com uma introdução simple e prática para usando áreas de trabalho em um aplicativo xamarin. Mac. Posteriormente, forneceremos uma explicação detalhada sobre como funciona a área de trabalho e os métodos usados.

Neste exemplo, estamos criando um aplicativo de documento simples com base em que gerencia uma janela que contém uma exibição de imagem. O usuário será capaz de copiar e colar imagens entre documentos no aplicativo e para ou de outros aplicativos ou várias janelas de dentro do mesmo aplicativo.

### <a name="creating-the-xamarin-project"></a>Criando o projeto do Xamarin

Primeiro, vamos criar um novo aplicativo xamarin. MAC de documento com base em que podemos será adição de uma cópia e cole o suporte para.

Faça o seguinte:

1. Inicie o Visual Studio para Mac e clique no **novo projeto...**  link.
2. Selecione **Mac** > **App** > **aplicativo Cocoa**, em seguida, clique o **próxima** botão: 

    [![Criar um novo projeto de aplicativo do Cocoa](copy-paste-images/sample01.png "criando um novo projeto de aplicativo Cocoa")](copy-paste-images/sample01-large.png#lightbox)
3. Insira `MacCopyPaste` para o **nome do projeto** e manter todo o resto como padrão. Clique em Avançar: 

    [![Configurando o nome do projeto](copy-paste-images/sample01a.png "definindo o nome do projeto")](copy-paste-images/sample01a-large.png#lightbox)

4. Clique o **criar** botão: 

    [![Confirmando as novas configurações de projeto](copy-paste-images/sample02.png "confirmando as novas configurações de projeto")](copy-paste-images/sample02-large.png#lightbox)

### <a name="add-an-nsdocument"></a>Adicionar um NSDocument

Em seguida, adicionaremos personalizado `NSDocument` classe que atuará como o armazenamento de plano de fundo para a interface do usuário do aplicativo. Ele conterá uma única exibição de imagem e saber como copiar uma imagem de exibição para a área de trabalho padrão e como capturar uma imagem da área de trabalho padrão e exibi-lo no modo de exibição de imagem.

Clique com botão direito no projeto xamarin. MAC na **painel de soluções** e selecione **Add** > **novo arquivo...** :

![Adicionando um NSDocument ao projeto](copy-paste-images/sample03.png "adicionando um NSDocument ao projeto")

Digite `ImageDocument` para o **Nome** e clique no botão **Novo**. Editar o **ImageDocument.cs** de classe e torná-lo semelhante ao seguinte:

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

O código a seguir fornece uma propriedade para testar a existência de dados de imagem na área de trabalho padrão, se uma imagem estiver disponível, `true` será retornado else `false`:

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

O código a seguir copia uma imagem do modo de exibição da imagem anexada na área de trabalho padrão:

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

E o código a seguir cola uma imagem de área de trabalho padrão e o exibe na exibição da imagem anexada (se a área de trabalho contém uma imagem válida):

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

Com este documento no lugar, vamos criar a interface do usuário para o aplicativo xamarin. Mac.

### <a name="building-the-user-interface"></a>Criando a interface do usuário

Clique duas vezes o **Main. Storyboard** arquivo para abri-lo no Xcode. Em seguida, adicione uma barra de ferramentas e uma imagem bem e configurá-los da seguinte maneira:

[![A barra de ferramentas de edição](copy-paste-images/sample04.png "a barra de ferramentas de edição")](copy-paste-images/sample04-large.png#lightbox)

Adicione uma cópia e cole **Item de barra de ferramentas de imagem** para o lado esquerdo da barra de ferramentas. Usaremos isso como atalhos de copiar e colar no menu Editar. Em seguida, adicione quatro **itens de barra de ferramentas de imagem** para o lado direito da barra de ferramentas. Vamos usar essas para preencher a imagem bem com algumas imagens padrão.

Para obter mais informações sobre como trabalhar com barras de ferramentas, consulte nosso [barras de ferramentas](~/mac/user-interface/toolbar.md) documentação.

Em seguida, vamos expor as saídas e ações para os itens da barra de ferramentas e a imagem a seguir também:

[![Criando saídas e ações](copy-paste-images/sample05.png "criando saídas e ações")](copy-paste-images/sample05-large.png#lightbox)

Para obter mais informações sobre como trabalhar com saídas e ações, consulte a [saídas e ações](~/mac/get-started/hello-mac.md#outlets-and-actions) seção do nosso [Hello, Mac](~/mac/get-started/hello-mac.md) documentação.

### <a name="enabling-the-user-interface"></a>Habilitando a interface do usuário

Com nossa interface de usuário criada no Xcode e o nosso elemento de interface do usuário expostos por meio de saídas e ações, precisamos adicionar o código para habilitar a interface do usuário. Clique duas vezes o **ImageWindow.cs** arquivo na **painel de soluções** e torná-lo semelhante ao seguinte:

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

Primeiro, vamos expor uma instância da `ImageDocument` classe que criamos acima:

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

Usando `Export`, `WillChangeValue` e `DidChangeValue`, configuramos o `Document` propriedade para permitir a codificação de chave-valor e associação de dados no Xcode.

Podemos também expor a imagem a partir da imagem que também adicionamos à nossa interface do usuário no Xcode com a seguinte propriedade:

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

Quando a janela principal é carregada e exibida, podemos criar uma instância do nosso `ImageDocument` de classe e anexar a imagem da interface do usuário também a ele com o código a seguir:

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

Por fim, em resposta ao usuário clicar nos itens da barra de ferramentas de copiar e colar, chamamos a instância da `ImageDocument` classe para fazer o trabalho real:

```csharp
partial void CopyImage (NSObject sender) {
    Document.CopyImage(sender);
}

partial void PasteImage (Foundation.NSObject sender) {
    Document.PasteImage(sender);
}
```

### <a name="enabling-the-file-and-edit-menus"></a>Habilitando os menus de arquivo e editar

A última coisa que precisamos fazer é habilitar o **New** item de menu a **arquivo** menu (para criar novas instâncias de nossa janela principal) e habilitar o **Recortar**, **cópia**  e **colar** itens de menu dos **editar** menu.

Para habilitar o **New** menu Editar item, o **AppDelegate.cs** arquivo e adicione o código a seguir:

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

Para obter mais informações, consulte o [trabalhando com o Windows vários](~/mac/user-interface/window.md) seção do nosso [Windows](~/mac/user-interface/window.md) documentação.

Para habilitar o **Recortar**, **cópia** e **colar** editar itens de menu, o **AppDelegate.cs** arquivo e adicione o código a seguir:

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

Para cada item de menu, obter a janela atual, mais alta, chave e convertê-lo em nosso `ImageWindow` classe:

```csharp
var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;
```

A partir daí, chamamos o `ImageDocument` instância da classe dessa janela para lidar com a cópia e ações de colar. Por exemplo: 

```csharp
window.Document.CopyImage (sender);
```

Como só queremos **Recortar**, **cópia** e **colar** itens de menu a ser acessado se não houver dados na área de trabalho padrão ou a imagem bem da janela ativa atual da imagem.

Vamos adicionar um **EditMenuDelegate.cs** arquivo ao projeto xamarin. Mac e torná-lo semelhante ao seguinte:

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

Novamente, podemos obter a janela atual, mais alta e usar seu `ImageDocument` instância da classe para ver se os dados de imagem necessário existem. Em seguida, usamos o `MenuWillHighlightItem` método para habilitar ou desabilitar cada item com base nesse estado.

Editar o **AppDelegate.cs** do arquivo e verifique o `DidFinishLaunching` método são semelhantes ao seguinte:
 
```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    // Disable automatic item enabling on the Edit menu
    EditMenu.AutoEnablesItems = false;
    EditMenu.Delegate = new EditMenuDelegate ();
}
```

Primeiro, Desabilitaremos automática habilitando e desabilitando dos itens de menu no menu Editar. Em seguida, podemos anexar a uma instância da `EditMenuDelegate` classe que criamos acima.

Para obter mais informações, consulte nosso [Menus](~/mac/user-interface/menu.md) documentação.

### <a name="testing-the-app"></a>Testar o aplicativo

Com tudo instalado, você está pronto para testar o aplicativo. Compilar e executar o aplicativo e a interface principal é exibida:

![Execução do aplicativo](copy-paste-images/run01.png "executando o aplicativo")

Observe que, se você abrir o menu Editar, **Recortar**, **cópia** e **colar** estão desabilitadas porque não há nenhuma imagem na imagem bem ou na área de trabalho padrão:

![Abrindo o menu Editar](copy-paste-images/run02.png "abrindo o menu Editar")

Se você adicionar uma imagem à imagem bem e reabrir o menu Editar, os itens agora serão habilitados:

![Mostrando os itens de menu de edição estão habilitadas](copy-paste-images/run03.png "mostrando os itens de menu de edição estão habilitados")

Se você copiar a imagem e selecione **New** no menu Arquivo, você pode colar essa imagem em nova janela:

![Colar uma imagem em uma nova janela](copy-paste-images/run04.png "colando uma imagem em uma nova janela")

Nas seções a seguir, vamos dar uma visão detalhada de como trabalhar com a área de trabalho em um aplicativo xamarin. Mac.

## <a name="about-the-pasteboard"></a>Sobre a área de trabalho

No macOS (anteriormente conhecido como o OS X) a área de trabalho (`NSPasteboard`) dão suporte para processos do servidor de vários serviços de aplicativos e, como copiar e colar, arrastar e soltar. Nas seções a seguir, vamos dar uma olhada nas várias principais conceitos de área de trabalho.

### <a name="what-is-a-pasteboard"></a>O que é uma área de trabalho?

O `NSPasteboard` classe fornece um mecanismo padronizado para a troca de informações entre aplicativos ou dentro de um determinado aplicativo. A função principal de uma área de trabalho é para lidar com operações de copiar e colar:

1. Quando o usuário seleciona um item em um aplicativo e usa o **Recortar** ou **cópia** item de menu, um ou mais representações do item selecionado são colocadas na área de trabalho.
2. Quando o usuário utiliza o **colar** item de menu (dentro do mesmo aplicativo ou um diferente), a versão dos dados que pode manipular é copiada da área de trabalho e adicionada ao aplicativo.

Usos de área de trabalho menos óbvios incluem find, arrastar, arrastar e soltar e as operações dos serviços de aplicativos:

- Quando o usuário inicia uma operação de arrastar, arrastar dados são copiados para a área de trabalho. Se a operação de arrastar termina com uma queda em outro aplicativo, o que o aplicativo copia os dados de área de trabalho.
- Serviços de tradução, os dados a ser traduzido são copiados para a área de trabalho pelo aplicativo solicitante. O serviço de aplicativo, recupera os dados da área de trabalho, faz a conversão, em seguida, cola os dados de volta na área de trabalho.

Em sua forma mais simples, áreas de trabalho são usadas para mover dados dentro de um determinado aplicativo ou entre aplicativos e, portanto, existem em uma área de memória global especial fora do processo do aplicativo. Embora os conceitos das áreas de trabalho são facilmente grasps, há vários detalhes mais complexos que devem ser consideradas. Eles serão abordados em detalhes abaixo.

### <a name="named-pasteboards"></a>Áreas de trabalho nomeadas

Uma área de trabalho pode ser público ou privado e pode ser usada para uma variedade de finalidades dentro de um aplicativo ou entre vários aplicativos. macOS fornece vários padrão áreas de trabalho, cada um com um uso específico, bem definido:

- `NSGeneralPboard` -A área de trabalho padrão para **Recortar**, **cópia** e **colar** operações.
- `NSRulerPboard` – Dá suporte a **Recortar**, **cópia** e **colar** operações nos **réguas**.
- `NSFontPboard` – Dá suporte a **Recortar**, **cópia** e **colar** operações em `NSFont` objetos.
- `NSFindPboard` – Dá suporte a específicos do aplicativo encontrar painéis que podem compartilhar o texto de pesquisa.
- `NSDragPboard` – Dá suporte a **arrastar e soltar** operações.

Para a maioria das situações, você usará uma das áreas de trabalho definidos pelo sistema. Mas talvez haja situações que exigem que você crie seus próprios áreas de trabalho. Nessas situações, você pode usar o `FromName (string name)` método da `NSPasteboard` classe para criar uma área de trabalho personalizada com o nome fornecido.

Opcionalmente, você pode chamar o `CreateWithUniqueName` método da `NSPasteboard` classe para criar uma área de trabalho exclusivamente nomeada.

### <a name="pasteboard-items"></a>Itens de área de trabalho

Cada parte dos dados que um aplicativo grava uma área de trabalho é considerado um _Item de área de trabalho_ e uma área de trabalho pode conter vários itens ao mesmo tempo. Dessa forma, um aplicativo pode gravar várias versões dos dados sendo copiados para uma área de trabalho (por exemplo, texto sem formatação e texto formatado) e o aplicativo ao recuperar podem ler somente os dados que ele possa processar (por exemplo, o texto sem formatação somente).

### <a name="data-representations-and-uniform-type-identifiers"></a>Identificadores de tipo de uniforme e representações de dados

Operações de área de trabalho normalmente levam aplicativos ocorre entre dois (ou mais) que têm nenhum conhecimento sobre uns aos outros ou os tipos de dados que cada um pode manipular. Conforme mencionado na seção acima, para maximizar o potencial para compartilhar informações, uma área de trabalho pode conter várias representações dos dados sendo copiados e colados.

Cada representação é identificada por meio de um Uniform tipo identificador UTI (), que é nada mais do que uma simple cadeia de caracteres que identifica exclusivamente o tipo de data que está sendo apresentado (para obter mais informações, consulte da Apple [uniforme visão geral de identificadores de tipo ](https://developer.apple.com/library/prerelease/mac/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_intro/understand_utis_intro.html#//apple_ref/doc/uid/TP40001319) documentação). 

Se você estiver criando um tipo de dados personalizado (por exemplo, um objeto de desenho em um aplicativo de desenho de vetor), você pode criar seu próprios UTI para identificá-lo na cópia exclusivamente e operações de colagem.

Quando um aplicativo se prepara para colar dados copiados da área de trabalho, ele deve localizar a representação que melhor atenda às suas capacidades (se houver alguma). Normalmente, esse será o tipo mais avançado disponível (por exemplo formatado texto para um aplicativo de processamento de texto), voltando para as formas mais simples disponíveis como necessário (texto sem formatação para um editor de texto simples).

<a name="Promised_Data" />

### <a name="promised-data"></a>Dados prometidos

Em termos gerais, você deve fornecer tantos representações dos dados sendo copiados possível para maximizar o compartilhamento entre aplicativos. No entanto, devido a restrições de tempo ou memória, talvez seja impossível escrever, na verdade, cada tipo de dados para a área de trabalho.

Nessa situação, você pode colocar a primeira representação de dados na área de trabalho e o aplicativo de recebimento pode solicitar uma representação diferente, que pode ser gerado no imediatamente antes da operação de colagem.

Quando você coloca o item inicial na área de trabalho, você especificará se uma ou mais das outras representações disponíveis são fornecidos por um objeto que está de acordo com o `NSPasteboardItemDataProvider` interface. Esses objetos fornecerá as representações extras sob demanda, conforme solicitado pelo aplicativo de recebimento.

### <a name="change-count"></a>Contagem de alteração

Cada área de trabalho mantém uma _contagem de alteração_ que é incrementada sempre que um novo proprietário é declarada. Um aplicativo pode determinar se o conteúdo da área de trabalho foram alterados desde a última vez que ela examinou verificando o valor de contagem de alteração.

Use o `ChangeCount` e `ClearContents` métodos do `NSPasteboard` classe para modificar a contagem de alteração da área de trabalho determinada.

## <a name="copying-data-to-a-pasteboard"></a>Copiando dados para uma área de trabalho

Você pode executar uma operação de cópia primeiro acessando uma área de trabalho, limpar qualquer conteúdo existente e escrever tantas representações dos dados conforme são necessárias para a área de trabalho.

Por exemplo:

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Empty the current contents
pasteboard.ClearContents();

// Add the current image to the pasteboard
pasteboard.WriteObjects (new NSImage[] {image});
```

Normalmente, você apenas escrevo para a área de trabalho geral, como fizemos no exemplo acima. Qualquer objeto que você enviar para o `WriteObjects` método *deve* estar de acordo com o `INSPasteboardWriting` interface. Várias, classes internas (como `NSString`, `NSImage`, `NSURL`, `NSColor`, `NSAttributedString`, e `NSPasteboardItem`) automaticamente em conformidade com essa interface.

Se você estiver escrevendo uma classe personalizada de dados para a área de trabalho ele deve estar em conformidade com o `INSPasteboardWriting` interface ou ser encapsuladas em uma instância da `NSPasteboardItem` classe (consulte a [tipos de dados personalizados](#Custom_Data_Types) seção abaixo).

## <a name="reading-data-from-a-pasteboard"></a>Ler os dados da área de trabalho

Como mencionado acima, para maximizar o potencial para compartilhar dados entre aplicativos, várias representações dos dados copiados podem ser gravadas para a área de trabalho. É até o aplicativo de recebimento para selecionar a versão mais completa possível para seus recursos (se houver alguma).

### <a name="simple-paste-operation"></a>Operação de colagem simples

Ler dados da área de trabalho usando o `ReadObjectsForClasses` método. Ela exigirá dois parâmetros:

1. Uma matriz de `NSObject` com base em tipos de classe que você deseja ler a partir da área de trabalho. Você deve solicitá-lo com o tipo de dados mais desejado em primeiro lugar, com quaisquer tipos restantes em decrescente de preferência.
2. Um dicionário que contém as restrições adicionais (por exemplo, limitando a tipos específicos de conteúdo de URL) ou um dicionário vazio se não há restrições adicionais são necessárias.

O método retorna uma matriz de itens que atendem aos critérios que são passados e, portanto, contém o mesmo número de tipos de dados que são solicitados no máximo. também é possível que haja nenhum dos tipos solicitados e uma matriz vazia será retornada.

Por exemplo, o código a seguir verifica se um `NSImage` existe na área de trabalho geral e o exibe em uma imagem bem se isso acontecer:

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

### <a name="requesting-multiple-data-types"></a>Solicitar vários tipos de dados

Com base no tipo de aplicativo xamarin. MAC que está sendo criado, ele pode ser capaz de lidar com várias representações dos dados que estão sendo colados. Nessa situação, há dois cenários para recuperar dados de área de trabalho:

1. Fazer uma chamada única para o `ReadObjectsForClasses` método e fornecendo uma matriz de todas as representações que desejar (na ordem preferencial).
2. Fazer várias chamadas para o `ReadObjectsForClasses` método solicitando uma matriz diferente dos tipos de cada vez.

Consulte a **simples operação de colagem** seção acima para obter mais detalhes sobre como recuperar dados de uma área de trabalho.

### <a name="checking-for-existing-data-types"></a>Verificação de tipos de dados existentes

Há momentos em que você talvez queira verificar se uma área de trabalho contém uma representação de dados fornecido sem realmente lê os dados da área de trabalho (como habilitar o **colar** item de menu apenas quando houver dados válidos).

Chamar o `CanReadObjectForClasses` método da área de trabalho para ver se ele contém um determinado tipo.

Por exemplo, o código a seguir determina se a área de trabalho geral contém um `NSImage` instância:

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

### <a name="reading-urls-from-the-pasteboard"></a>Lendo as urls de área de trabalho

Com base na função de um determinado aplicativo xamarin. Mac, pode ser necessária para ler as URLs de área de trabalho, mas somente se atenderem a um determinado conjunto de critérios (por exemplo, que aponta para arquivos ou URLs de um tipo de dados específico). Nessa situação, você pode especificar critérios de pesquisa adicionais usando o segundo parâmetro do `CanReadObjectForClasses` ou `ReadObjectsForClasses` métodos.

<a name="Custom_Data_Types" />

## <a name="custom-data-types"></a>Tipos de dados personalizados

Há vezes em que você precisa salvar seus próprios tipos personalizados para a área de trabalho de um aplicativo xamarin. Mac. Por exemplo, um vetor de aplicativo que permite ao usuário para copiar e colar objetos de desenho de desenho.

Nessa situação, você precisará criar sua classe personalizada de dados, de modo que ela herda `NSObject` e sigam algumas interfaces (`INSCoding`, `INSPasteboardWriting` e `INSPasteboardReading`). Opcionalmente, você pode usar um `NSPasteboardItem` para encapsular os dados a serem copiados ou colados.

Ambas as opções serão abordadas em detalhes abaixo.

### <a name="using-a-custom-class"></a>Usando uma classe personalizada

Nesta seção vamos ser expandindo o aplicativo de exemplo simples que criamos no início deste documento e adicionando uma classe personalizada para rastrear informações sobre a imagem que estamos copiando e colando entre as janelas.

Adicione uma nova classe ao projeto e chamá-lo **ImageInfo.cs**. Edite o arquivo e torná-lo a seguinte aparência:

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

Nas seções a seguir vamos dar uma visão detalhada dessa classe.

#### <a name="inheritance-and-interfaces"></a>Herança e interfaces

Antes de uma classe personalizada de dados pode ser gravada ou lidos de uma área de trabalho, ele deve estar em conformidade com o `INSPastebaordWriting` e `INSPasteboardReading` interfaces. Além disso, ele deve herdar de `NSObject` e também estar em conformidade com o `INSCoding` interface:

```csharp
[Register("ImageInfo")]
public class ImageInfo : NSObject, INSCoding, INSPasteboardWriting, INSPasteboardReading
...
```

A classe também deve ser exposta para Objective-C usando o `Register` diretiva e ela devem expor quaisquer propriedades necessárias ou métodos usando `Export`. Por exemplo:

```csharp
[Export("name")]
public string Name { get; set; }

[Export("imageType")]
public string ImageType { get; set; }
```

Estamos expondo os dois campos de dados que essa classe conterá - nome da imagem e seu tipo (jpg, png, etc.). 

Para obter mais informações, consulte o [classes expondo c# / métodos para Objective-C](~/mac/internals/how-it-works.md) seção o [recursos internos de xamarin. Mac](~/mac/internals/how-it-works.md) documentação, explica a `Register` e `Export` atributos usado para conectar suas classes de c# a objetos de Objective-C e da interface do usuário elementos.

#### <a name="constructors"></a>Construtores

Dois construtores (expostos de forma adequada para Objective-C) serão necessárias para nossa classe de dados personalizados para que ele possa ser lido de uma área de trabalho:

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

Primeiro, vamos expor os _vazio_ construtor sob o método de Objective-C padrão do `init`.

Em seguida, vamos expor uma `NSCoding` compatível com construtor que será usado para criar uma nova instância do objeto da área de trabalho ao colar abaixo do nome exportado do `initWithCoder`.

Este construtor aceita um `NSCoder` (conforme criado por um `NSKeyedArchiver` quando gravados para a área de trabalho), extrai os dados de par chave/valor e salva-o para os campos de propriedade da classe de dados.

#### <a name="writing-to-the-pasteboard"></a>Escrevendo para a área de trabalho

Conforme o `INSPasteboardWriting` interface, que é necessário para expor dois métodos e, opcionalmente, um terceiro método, para que a classe pode ser escrita para a área de trabalho.

Primeiro, precisamos dizer a área de trabalho qual tipo de dados representações de que a classe personalizada pode ser gravada em:

```csharp
[Export ("writableTypesForPasteboard:")]
public virtual string[] GetWritableTypesForPasteboard (NSPasteboard pasteboard) {
    string[] writableTypes = {"com.xamarin.image-info", "public.text"};
    return writableTypes;
}
```

Cada representação é identificada por meio de um Uniform tipo identificador UTI (), que é nada mais do que uma simple cadeia de caracteres que identifica exclusivamente o tipo de dados que está sendo apresentados (para obter mais informações, consulte da Apple [uniforme visão geral de identificadores de tipo ](https://developer.apple.com/library/prerelease/mac/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_intro/understand_utis_intro.html#//apple_ref/doc/uid/TP40001319) documentação).

Para nosso formato personalizado, estamos criando nosso próprio UTI: "com.xamarin.image-info" (Observe que está na notação inversa assim como um identificador de aplicativo). Nossa classe também é capaz de gravar uma cadeia de caracteres padrão para a área de trabalho (`public.text`). 

Em seguida, precisamos criar o objeto no formato solicitado realmente gravados para a área de trabalho:

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

Para o `public.text` tipo, estamos retornando um simples formatada `NSString` objeto. Para o personalizado `com.xamarin.image-info` tipo, estamos usando um `NSKeyedArchiver` e o `NSCoder` interface para codificar a classe de dados personalizado para um arquivo de par chave/valor. Precisamos implementar o seguinte método para manipular, na verdade, a codificação:

```csharp
[Export ("encodeWithCoder:")]
public void EncodeTo (NSCoder encoder) {

    // Encode data
    encoder.Encode(new NSString(Name),"name");
    encoder.Encode(new NSString(ImageType),"imageType");
}
```

Os pares chave/valor individuais são escritos para o codificador e serão decodificados usando o segundo construtor que adicionamos acima.

Opcionalmente, podemos incluir o seguinte método para definir as opções ao gravar dados para a área de trabalho:

```csharp
[Export ("writingOptionsForType:pasteboard:"), CompilerGenerated]
public virtual NSPasteboardWritingOptions GetWritingOptionsForType (string type, NSPasteboard pasteboard) {
    return NSPasteboardWritingOptions.WritingPromised;
}
```

Atualmente, apenas o `WritingPromised` opção está disponível e deve ser usada quando um determinado tipo é prometido apenas e não realmente gravado para a área de trabalho. Para obter mais informações, consulte o [dados prometidos](#Promised_Data) seção acima.

Com esses métodos em vigor, o código a seguir pode ser usado para gravar a nossa classe personalizada para a área de trabalho:

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Empty the current contents
pasteboard.ClearContents();

// Add info to the pasteboard
pasteboard.WriteObjects (new ImageInfo[] { Info });
```

#### <a name="reading-from-the-pasteboard"></a>Lendo da área de trabalho

Conforme o `INSPasteboardReading` interface, precisamos expõe três métodos para que a classe de dados personalizados pode ser lidos na área de trabalho.

Primeiro, precisamos dizer a área de trabalho qual tipo de dados representações de que a classe personalizada pode ler da área de transferência:

```csharp
[Export ("readableTypesForPasteboard:")]
public static string[] GetReadableTypesForPasteboard (NSPasteboard pasteboard){
    string[] readableTypes = {"com.xamarin.image-info", "public.text"};
    return readableTypes;
}
```

Novamente, eles são definidos como UTIs simples e são os mesmos tipos que definimos na **escrevendo para a área de trabalho** seção acima.

Em seguida, precisamos dizer à área de trabalho _como_ cada um dos tipos do UTI serão lidos usando o seguinte método:

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

Para o `com.xamarin.image-info` tipo, estamos dizendo a área de trabalho para decodificar o par chave/valor que criamos com o `NSKeyedArchiver` gravar quando a classe para a área de trabalho chamando o `initWithCoder:` construtor que adicionamos à classe.

Por fim, precisamos adicionar o seguinte método para ler as outras representações de dados UTI de área de trabalho:

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

Com todos esses métodos em vigor, a classe de dados personalizados pode ser lidos na área de trabalho usando o seguinte código:

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

Pode haver vezes quando você precisa gravar itens personalizados para a área de trabalho que não garantem a criação de uma classe personalizada ou deseja fornecer dados em um formato comum, conforme necessário. Nessas situações, você pode usar um `NSPasteboardItem`.

Um `NSPasteboardItem` fornece controle refinado sobre os dados que são gravados para a área de trabalho e são projetados para acesso temporário - ele deverá ser descartado após ele ter sido gravado para a área de trabalho.

#### <a name="writing-data"></a>Gravação de dados

Para gravar os dados personalizados para um `NSPasteboardItem` você precisará fornecer um personalizado `NSPasteboardItemDataProvider`. Adicione uma nova classe ao projeto e chamá-lo **ImageInfoDataProvider.cs**. Edite o arquivo e torná-lo a seguinte aparência:

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

Como fizemos com a classe de dados personalizados, precisamos usar o `Register` e `Export` diretivas para expô-lo para Objective-C. A classe deve herdar de `NSPasteboardItemDataProvider` e deve implementar a `FinishedWithDataProvider` e `ProvideDataForType` métodos.

Use o `ProvideDataForType` método para fornecer os dados que serão encapsulados no `NSPasteboardItem` da seguinte maneira:

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

Nesse caso, estamos armazenando os dois tipos de informação sobre nossa imagem (nome e ImageType) e gravando-os para uma cadeia de caracteres simple (`public.text`).

Tipo de gravar os dados para a área de trabalho, use o seguinte código:

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

#### <a name="reading-data"></a>Leitura de dados

Para ler os dados da área de trabalho, use o seguinte código:

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

Este artigo apresentou uma visão detalhada de como trabalhar com a área de trabalho em um aplicativo xamarin. Mac para dar suporte a copiar e colar operações. Primeiro, ele introduziu um exemplo simples para se familiarizar com as operações de áreas de trabalho padrão. Em seguida, levou a uma visão detalhada de área de trabalho e como ler e gravar dados dele. Por fim, ele examinou usando um tipo de dados personalizados para dar suporte a cópia e colagem dos tipos de dados complexos dentro de um aplicativo.



## <a name="related-links"></a>Links relacionados

- [MacCopyPaste (amostra)](https://developer.xamarin.com/samples/mac/MacCopyPaste/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Guia de programação em área de trabalho](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/PasteboardGuide106/Articles/pbGettingStarted.html)
- [Diretrizes de Interface humana do macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
