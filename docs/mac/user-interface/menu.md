---
title: Menus no Xamarin. Mac
description: Este artigo aborda como trabalhar com menus em um aplicativo Xamarin. Mac. Ele descreve como criar e manter menus e itens de menu no Xcode e Interface Builder e trabalhar com eles de forma programática.
ms.prod: xamarin
ms.assetid: 5D367F8E-3A76-4995-8A89-488530FAD802
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 7cca5be2ea13deb17b27e5452df389a998c6eb09
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73026178"
---
# <a name="menus-in-xamarinmac"></a>Menus no Xamarin. Mac

_Este artigo aborda como trabalhar com menus em um aplicativo Xamarin. Mac. Ele descreve como criar e manter menus e itens de menu no Xcode e Interface Builder e trabalhar com eles de forma programática._

Ao trabalhar com C# o e o .net em um aplicativo Xamarin. Mac, você tem acesso aos mesmos menus do Cocoa que um desenvolvedor trabalhando no Objective-C e no Xcode. Como o Xamarin. Mac se integra diretamente com o Xcode, você pode usar o Interface Builder do Xcode para criar e manter suas barras de menus, menus e itens de menu (ou, opcionalmente, criá-los diretamente no C# código).

Os menus são parte integrante da experiência do usuário de um aplicativo Mac e normalmente aparecem em várias partes da interface do usuário:

- **Barra de menus do aplicativo** – esse é o menu principal que aparece na parte superior da tela para cada aplicativo Mac.
- **Menus contextuais** -aparecem quando o usuário clica com o botão direito do mouse ou clica em um item em uma janela.
- **A barra de status** -é a área no lado mais à direita da barra de menus do aplicativo que aparece na parte superior da tela (à esquerda do relógio da barra de menus) e aumenta à esquerda à medida que os itens são adicionados a ela.
- **Menu de encaixe** – o menu para cada aplicativo no Dock que aparece quando o usuário clica com o botão direito do mouse ou clica no ícone do aplicativo, ou quando o usuário clica no ícone e mantém o botão do mouse pressionado.
- **Botão pop-up e listas suspensas** – um botão pop-up exibe um item selecionado e apresenta uma lista de opções para selecionar quando clicado pelo usuário. Uma lista suspensa é um tipo de botão pop-up geralmente usado para selecionar comandos específicos para o contexto da tarefa atual. Ambos podem aparecer em qualquer lugar em uma janela.

[![Um menu de exemplo](menu-images/intro01.png "Um menu de exemplo")](menu-images/intro01-large.png#lightbox)

Neste artigo, abordaremos as noções básicas de como trabalhar com barras de menu Cocoa, menus e itens de menu em um aplicativo Xamarin. Mac. É altamente recomendável que você trabalhe pelo artigo [Hello, Mac](~/mac/get-started/hello-mac.md) primeiro, especificamente a [introdução às seções Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [ações](~/mac/get-started/hello-mac.md#outlets-and-actions) , pois ela aborda os principais conceitos e técnicas que usaremos em Este artigo.

Talvez você queira dar uma olhada na seção [ C# expondo classes/métodos para Objective-C](~/mac/internals/how-it-works.md) do documento [interno do Xamarin. Mac](~/mac/internals/how-it-works.md) também, explica o `Register` e `Export` atributos usados para conectar suas C# classes ao Objective-C objetos e elementos de interface do usuário.

## <a name="the-applications-menu-bar"></a>A barra de menus do aplicativo 

Ao contrário dos aplicativos em execução no sistema operacional Windows em que cada janela pode ter sua própria barra de menus anexada a ele, cada aplicativo em execução no macOS tem uma única barra de menus que é executada na parte superior da tela que é usada para cada janela do aplicativo:

[![Uma barra de menus](menu-images/appmenu01.png "Uma barra de menus")](menu-images/appmenu01-large.png#lightbox)

Os itens nessa barra de menus são ativados ou desativados com base no contexto atual ou no estado do aplicativo e sua interface do usuário em um determinado momento. Por exemplo: se o usuário selecionar um campo de texto, os itens no menu **Editar** serão habilitados, como **copiar** e **recortar**.

De acordo com a Apple e, por padrão, todos os aplicativos macOS têm um conjunto padrão de menus e itens de menu que aparecem na barra de menus do aplicativo:

- **Menu da Apple** – esse menu fornece acesso a itens em todo o sistema que estão disponíveis para o usuário em todos os momentos, independentemente de qual aplicativo está sendo executado. Esses itens não podem ser modificados pelo desenvolvedor.
- **Menu do aplicativo** – esse menu exibe o nome do aplicativo em negrito e ajuda o usuário a identificar qual aplicativo está em execução no momento. Ele contém itens que se aplicam ao aplicativo como um todo e não a um determinado documento ou processo, como sair do aplicativo.
- **Menu arquivo** -itens usados para criar, abrir ou salvar documentos com os quais seu aplicativo funciona. Se o seu aplicativo não for baseado em documento, esse menu poderá ser renomeado ou removido.
- **Menu Editar** – mantém comandos como **recortar**, **copiar**e **colar** , que são usados para editar ou modificar elementos na interface do usuário do aplicativo.
- **Menu Formatar** – se o aplicativo funcionar com texto, esse menu conterá comandos para ajustar a formatação desse texto.
- **Menu Exibir** – mantém comandos que afetam o modo como o conteúdo é exibido (exibido) na interface do usuário do aplicativo.
- **Menus específicos do aplicativo** -esses são todos os menus que são específicos para seu aplicativo (como um menu de indicadores para um navegador da Web). Eles devem aparecer entre os menus de **exibição** e de **janela** na barra.
- **Menu janela** – contém comandos para trabalhar com o Windows em seu aplicativo, bem como uma lista de janelas abertas atuais.
- **Menu ajuda** – se seu aplicativo fornece ajuda na tela, o menu ajuda deve ser o menu mais à direita na barra. 

Para obter mais informações sobre a barra de menus do aplicativo e os menus padrão e itens de menu, consulte as [diretrizes de interface humana](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/)da Apple.

### <a name="the-default-application-menu-bar"></a>A barra de menus do aplicativo padrão

Sempre que você cria um novo projeto do Xamarin. Mac, você obtém automaticamente uma barra de menus padrão do aplicativo que tem os itens típicos que um aplicativo macOS normalmente teria (conforme discutido na seção acima). A barra de menus padrão do seu aplicativo é definida no arquivo **Main. Storyboard** (juntamente com o restante da interface do usuário do seu aplicativo) no projeto no **painel de soluções**:  

![Selecione o storyboard principal](menu-images/appmenu02.png "Selecione o storyboard principal")

Clique duas vezes no arquivo **Main. Storyboard** para abri-lo para edição no interface Builder do Xcode e você verá a interface do editor de menus:

[![Editando a interface do usuário no Xcode](menu-images/defaultbar01.png "Editando a interface do usuário no Xcode")](menu-images/defaultbar01-large.png#lightbox)

Aqui, podemos clicar em itens como o item de menu **abrir** no menu **arquivo** e editar ou ajustar suas propriedades no **Inspetor de atributos**:

[![Editando os atributos de um menu](menu-images/defaultbar02.png "Editando os atributos de um menu")](menu-images/defaultbar02-large.png#lightbox)

Vamos adicionar, editar e excluir menus e itens posteriormente neste artigo. Por enquanto, queremos apenas ver quais menus e itens de menu estão disponíveis por padrão e como eles foram expostos automaticamente ao código por meio de um conjunto de impedimentos e ações predefinidos (para obter mais informações, consulte nossa documentação [de ações e saídas](~/mac/get-started/hello-mac.md#outlets-and-actions) ).

Por exemplo, se clicarmos no **Inspetor de conexão** para o item de menu **aberto** , podemos ver que ele é conectado automaticamente à ação de `openDocument:`: 

[![Exibindo a ação anexada](menu-images/defaultbar03.png "Exibindo a ação anexada")](menu-images/defaultbar03-large.png#lightbox)

Se você selecionar o **primeiro Respondente** na **hierarquia de interface** e rolar para baixo no **Inspetor de conexão**, você verá a definição da ação de `openDocument:` à qual o item de menu **aberto** está anexado (juntamente com vários outros ações padrão para o aplicativo que são e que não são automaticamente conectados a controles):

[![Exibindo todas as ações anexadas](menu-images/defaultbar04.png "Exibindo todas as ações anexadas")](menu-images/defaultbar04-large.png#lightbox) 

Por que isso é importante? Na próxima seção, verá como essas ações definidas automaticamente funcionam com outros elementos da interface do usuário do Cocoa para habilitar e desabilitar automaticamente os itens de menu, bem como fornecer funcionalidade interna para os itens.

Posteriormente, usaremos essas ações internas para habilitar e Desabilitar itens do código e fornecer nossa própria funcionalidade quando eles forem selecionados.

<a name="Built-In_Menu_Functionality" />

### <a name="built-in-menu-functionality"></a>Funcionalidade de menu interna

Se você fosse executar um aplicativo Xamarin. Mac recém-criado antes de adicionar qualquer item ou código de interface do usuário, observará que alguns itens são conectados automaticamente e habilitados para você (com funcionalidade total interna), como o item de **encerramento** noMenu do aplicativo:

![Um item de menu habilitado](menu-images/appmenu03.png "Um item de menu habilitado")

Enquanto outros itens de menu, como **recortar**, **copiar**e **colar** não são:

![Itens de menu desabilitados](menu-images/appmenu04.png "Itens de menu desabilitados")

Vamos parar o aplicativo e clicar duas vezes no arquivo **Main. Storyboard** no **painel de soluções** para abri-lo para edição no interface Builder do Xcode. Em seguida, arraste uma **exibição de texto** da **biblioteca** para o controlador de exibição da janela no editor de **interface**:

[![Selecionando uma exibição de texto da biblioteca](menu-images/appmenu05.png "Selecionando uma exibição de texto da biblioteca")](menu-images/appmenu05-large.png#lightbox)

No **Editor de restrições** , vamos fixar a exibição de texto nas bordas da janela e defini-la onde ela aumenta e diminui com a janela clicando em todas as quatro transmissões vermelhas na parte superior do editor e clicando no botão **Adicionar restrições** :

[![Editando o restrições](menu-images/appmenu06.png "Editando o restrições")](menu-images/appmenu06-large.png#lightbox)

Salve as alterações no design da interface do usuário e volte para a Visual Studio para Mac para sincronizar as alterações com seu projeto Xamarin. Mac. Agora, inicie o aplicativo, digite algum texto na exibição de texto, selecione-o e abra o menu **Editar** :

![Os itens de menu são automaticamente habilitados/desabilitados](menu-images/appmenu07.png "Os itens de menu são automaticamente habilitados/desabilitados")

Observe como os itens **recortar**, **copiar**e **colar** são automaticamente habilitados e totalmente funcionais, tudo sem escrever uma única linha de código. 

O que está acontecendo aqui? Lembre-se das ações internas de predefinição que vêm conectadas aos itens de menu padrão (como apresentado acima), a maioria dos elementos da interface do usuário Cocoa que fazem parte do macOS tem ganchos internos para ações específicas (como `copy:`). Assim, quando eles são adicionados a uma janela, ativa e selecionada, o item de menu correspondente ou os itens anexados a essa ação são automaticamente habilitados. Se o usuário selecionar esse item de menu, a funcionalidade interna ao elemento de interface do usuário será chamada e executada, tudo isso sem intervenção do desenvolvedor.

### <a name="enabling-and-disabling-menus-and-items"></a>Habilitando e desabilitando menus e itens

Por padrão, toda vez que um evento de usuário ocorre, `NSMenu` habilita e desabilita automaticamente cada menu visível e item de menu com base no contexto do aplicativo. Há três maneiras de habilitar/desabilitar um item:

- **Habilitação de menu automático** – um item de menu será habilitado se `NSMenu` puder encontrar um objeto apropriado que responda à ação para a qual o item está conectado. Por exemplo, a exibição de texto acima que tinha um gancho interno para a ação de `copy:`.
- **Ações personalizadas e validateMenuItem:** -para qualquer item de menu associado a uma [ação personalizada de controlador de janela ou exibição](#Working-with-Custom-Window-Actions), você pode adicionar a ação de `validateMenuItem:` e habilitar ou desabilitar manualmente os itens de menu.
- **Habilitação de menu manual** -você define manualmente a propriedade `Enabled` de cada `NSMenuItem` para habilitar ou desabilitar cada item em um menu individualmente.

Para escolher um sistema, defina a propriedade `AutoEnablesItems` de um `NSMenu`. `true` é automático (o comportamento padrão) e `false` é manual. 

> [!IMPORTANT]
> Se você optar por usar o menu manual habilitando, nenhum dos itens de menu, mesmo aqueles controlados por classes AppKit como `NSTextView`, serão atualizados automaticamente. Você será responsável por habilitar e desabilitar todos os itens manualmente no código.

#### <a name="using-validatemenuitem"></a>Usando validateMenuItem

Como mencionado acima, para qualquer item de menu associado a uma [janela ou ação personalizada de controlador de exibição](#Working-with-Custom-Window-Actions), você pode adicionar a ação de `validateMenuItem:` e habilitar ou desabilitar manualmente os itens de menu.

No exemplo a seguir, a propriedade `Tag` será usada para decidir o tipo de item de menu que será habilitado/desabilitado pela ação de `validateMenuItem:` com base no estado do texto selecionado em uma `NSTextView`. A propriedade `Tag` foi definida em Interface Builder para cada item de menu:

![Configurando a propriedade Tag](menu-images/validate01.png "Configurando a propriedade Tag")

E o seguinte código adicionado ao controlador de exibição:

```csharp
[Action("validateMenuItem:")]
public bool ValidateMenuItem (NSMenuItem item) {

    // Take action based on the menu item type
    // (As specified in its Tag)
    switch (item.Tag) {
    case 1:
        // Wrap menu items should only be available if
        // a range of text is selected
        return (TextEditor.SelectedRange.Length > 0);
    case 2:
        // Quote menu items should only be available if
        // a range is NOT selected.
        return (TextEditor.SelectedRange.Length == 0);
    }

    return true;
}
```

Quando esse código é executado e nenhum texto é selecionado na `NSTextView`, os dois itens de menu de encapsulamento são desabilitados (mesmo que sejam conectados a ações no controlador de exibição):

![Mostrando itens desabilitados](menu-images/validate02.png "Mostrando itens desabilitados")

Se uma seção de texto for selecionada e o menu for reaberto, os dois itens de menu encapsulado estarão disponíveis:

![Mostrando itens habilitados](menu-images/validate03.png "Mostrando itens habilitados")

## <a name="enabling-and-responding-to-menu-items-in-code"></a>Habilitando e respondendo a itens de menu no código

Como vimos acima, apenas adicionando elementos específicos da interface do usuário do Cocoa ao nosso design de interface de usuário (como um campo de texto), vários dos itens de menu padrão serão habilitados e funcionarão automaticamente, sem precisar escrever nenhum código. Em seguida, vamos examinar como adicionar nosso C# próprio código ao nosso projeto Xamarin. Mac para habilitar um item de menu e fornecer funcionalidade quando o usuário o selecionar.

Por exemplo, digamos que queremos que o usuário seja capaz de usar o item **aberto** no menu **arquivo** para selecionar uma pasta. Como queremos que essa seja uma função de todo o aplicativo e não se limitasse a uma janela de exceção ou a um elemento de interface do usuário, vamos adicionar o código para lidar com o nosso representante de aplicativo.

No **painel de soluções**, clique duas vezes no arquivo `AppDelegate.CS` para abri-lo para edição:

![Selecionando o representante do aplicativo](menu-images/appmenu08.png "Selecionando o representante do aplicativo")

Adicione o seguinte código abaixo do método `DidFinishLaunching`:

```csharp
[Export ("openDocument:")]
void OpenDialog (NSObject sender)
{
    var dlg = NSOpenPanel.OpenPanel;
    dlg.CanChooseFiles = false;
    dlg.CanChooseDirectories = true;

    if (dlg.RunModal () == 1) {
        var alert = new NSAlert () {
            AlertStyle = NSAlertStyle.Informational,
            InformativeText = "At this point we should do something with the folder that the user just selected in the Open File Dialog box...",
            MessageText = "Folder Selected"
        };
        alert.RunModal ();
    }
}
```

Vamos executar o aplicativo agora e abrir o menu **arquivo** : 

![O menu arquivo](menu-images/appmenu09.png "O menu arquivo")

Observe que o item de menu **abrir** agora está habilitado. Se selecionarmos, a caixa de diálogo abrir será exibida:

![Uma caixa de diálogo aberta](menu-images/appmenu10.png "Uma caixa de diálogo aberta")

Se clicarmos no botão **abrir** , nossa mensagem de alerta será exibida:

![Uma mensagem de diálogo de exemplo](menu-images/appmenu11.png "Uma mensagem de diálogo de exemplo")

A principal linha aqui foi `[Export ("openDocument:")]`, ela diz `NSMenu` que nosso **AppDelegate** tem um método `void OpenDialog (NSObject sender)` que responde à ação de `openDocument:`. Se você se lembrar de acima, o item de menu **abrir** será automaticamente conectado a essa ação por padrão no interface Builder:

[![Exibindo as ações anexadas](menu-images/defaultbar03.png "Exibindo as ações anexadas")](menu-images/defaultbar03-large.png#lightbox)

Em seguida, vamos examinar como criar nosso próprio menu, itens de menu e ações e responder a eles no código.

### <a name="working-with-the-open-recent-menu"></a>Trabalhando com o menu abrir recente

Por padrão, o menu **arquivo** contém um item **recente aberto** que mantém o controle dos últimos vários arquivos que o usuário abriu com seu aplicativo. Se você estiver criando um aplicativo Xamarin. Mac baseado em `NSDocument`, esse menu será manipulado para você automaticamente. Para qualquer outro tipo de aplicativo Xamarin. Mac, você será responsável por gerenciar e responder a esse item de menu manualmente.

Para manipular manualmente o menu **abrir recente** , primeiro você precisará informá-lo de que um novo arquivo foi aberto ou salvo usando o seguinte:

```csharp
// Add document to the Open Recent menu
NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);
```

Embora seu aplicativo não esteja usando `NSDocuments`, você ainda usa o `NSDocumentController` para manter o menu **abrir recente** enviando um `NSUrl` com o local do arquivo para o método `NoteNewRecentDocumentURL` da `SharedDocumentController`.

Em seguida, você precisa substituir o método `OpenFile` do delegado do aplicativo para abrir qualquer arquivo que o usuário selecionar no menu **abrir recente** . Por exemplo:

```csharp
public override bool OpenFile (NSApplication sender, string filename)
{
    // Trap all errors
    try {
        filename = filename.Replace (" ", "%20");
        var url = new NSUrl ("file://"+filename);
        return OpenFile(url);
    } catch {
        return false;
    }
}
```

Retornar `true` se o arquivo puder ser aberto, caso contrário, retornará `false` e um aviso interno será exibido ao usuário que não pôde ser aberto.

Como o nome de arquivo e o caminho retornados do menu **abrir recente** podem incluir um espaço, é necessário escapar corretamente esse caractere antes de criar um `NSUrl` ou obteremos um erro. Fazemos isso com o código a seguir:

```csharp
filename = filename.Replace (" ", "%20");
```

Por fim, criamos um `NSUrl` que aponta para o arquivo e usamos um método auxiliar no delegado do aplicativo para abrir uma nova janela e carregar o arquivo nele:

```csharp
var url = new NSUrl ("file://"+filename);
return OpenFile(url);
```

Para efetuar pull de tudo, vamos dar uma olhada em uma implementação de exemplo em um arquivo **AppDelegate.cs** :

```csharp
using AppKit;
using Foundation;
using System.IO;
using System;

namespace MacHyperlink
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public int NewWindowNumber { get; set;} = -1;
        #endregion

        #region Constructors
        public AppDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }

        public override bool OpenFile (NSApplication sender, string filename)
        {
            // Trap all errors
            try {
                filename = filename.Replace (" ", "%20");
                var url = new NSUrl ("file://"+filename);
                return OpenFile(url);
            } catch {
                return false;
            }
        }
        #endregion

        #region Private Methods
        private bool OpenFile(NSUrl url) {
            var good = false;

            // Trap all errors
            try {
                var path = url.Path;

                // Is the file already open?
                for(int n=0; n<NSApplication.SharedApplication.Windows.Length; ++n) {
                    var content = NSApplication.SharedApplication.Windows[n].ContentViewController as ViewController;
                    if (content != null && path == content.FilePath) {
                        // Bring window to front
                        NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
                        return true;
                    }
                }

                // Get new window
                var storyboard = NSStoryboard.FromName ("Main", null);
                var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

                // Display
                controller.ShowWindow(this);

                // Load the text into the window
                var viewController = controller.Window.ContentViewController as ViewController;
                viewController.Text = File.ReadAllText(path);
                viewController.SetLanguageFromPath(path);
                viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
                viewController.View.Window.RepresentedUrl = url;

                // Add document to the Open Recent menu
                NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);

                // Make as successful
                good = true;
            } catch {
                // Mark as bad file on error
                good = false;
            }

            // Return results
            return good;
        }
        #endregion

        #region actions
        [Export ("openDocument:")]
        void OpenDialog (NSObject sender)
        {
            var dlg = NSOpenPanel.OpenPanel;
            dlg.CanChooseFiles = true;
            dlg.CanChooseDirectories = false;

            if (dlg.RunModal () == 1) {
                // Nab the first file
                var url = dlg.Urls [0];

                if (url != null) {
                    // Open the document in a new window
                    OpenFile (url);
                }
            }
        }
        #endregion
    }
}
```

Com base nos requisitos do seu aplicativo, talvez você não queira que o usuário abra o mesmo arquivo em mais de uma janela ao mesmo tempo. Em nosso aplicativo de exemplo, se o usuário escolher um arquivo que já esteja aberto (a partir da abertura **recente** ou **aberta..** itens de menu), a janela que contém o arquivo é trazida para a frente.

Para fazer isso, usamos o seguinte código em nosso método auxiliar:

```csharp
var path = url.Path;

// Is the file already open?
for(int n=0; n<NSApplication.SharedApplication.Windows.Length; ++n) {
    var content = NSApplication.SharedApplication.Windows[n].ContentViewController as ViewController;
    if (content != null && path == content.FilePath) {
        // Bring window to front
        NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
        return true;
    }
}
```

Nós criamos nossa classe `ViewController` para manter o caminho para o arquivo em sua propriedade `Path`. Em seguida, executamos um loop em todas as janelas abertas no aplicativo. Se o arquivo já estiver aberto em uma das janelas, ele será levado para a frente de todas as outras janelas usando:

```csharp
NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
```

Se nenhuma correspondência for encontrada, uma nova janela será aberta com o arquivo carregado e o arquivo será anotado no menu **abrir recente** :

```csharp
// Get new window
var storyboard = NSStoryboard.FromName ("Main", null);
var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

// Display
controller.ShowWindow(this);

// Load the text into the window
var viewController = controller.Window.ContentViewController as ViewController;
viewController.Text = File.ReadAllText(path);
viewController.SetLanguageFromPath(path);
viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
viewController.View.Window.RepresentedUrl = url;

// Add document to the Open Recent menu
NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);
```

<a name="Working-with-Custom-Window-Actions" />

### <a name="working-with-custom-window-actions"></a>Trabalhando com ações de janela personalizadas

Assim como as ações internas do **primeiro Respondente** que vêm previamente com os itens de menu padrão, você pode criar novas ações personalizadas e conectá-las a itens de menu no interface Builder.

Primeiro, defina uma ação personalizada em um dos controladores de janela do aplicativo. Por exemplo:

```csharp
[Action("defineKeyword:")]
public void defineKeyword (NSObject sender) {
    // Preform some action when the menu is selected
    Console.WriteLine ("Request to define keyword");
}
```

Em seguida, clique duas vezes no arquivo de storyboard do aplicativo no **painel de soluções** para abri-lo para edição no interface Builder do Xcode. Selecione o **primeiro Respondente** na **cena do aplicativo**e, em seguida, alterne para o **Inspetor de atributos**:

![O Inspetor de atributos](menu-images/action01.png "O Inspetor de atributos")

Clique no botão **+** na parte inferior do **Inspetor de atributos** para adicionar uma nova ação personalizada:

![Adicionando uma nova ação](menu-images/action02.png "Adicionando uma nova ação")

Dê a ele o mesmo nome que a ação personalizada que você criou em seu controlador de janela:

![Editando o nome da ação](menu-images/action03.png "Editando o nome da ação")

Controle-clique e arraste de um item de menu para o **primeiro Respondente** na **cena do aplicativo**. Na lista pop-up, selecione a nova ação que você acabou de criar (`defineKeyword:` neste exemplo):

![Anexando uma ação](menu-images/action04.png "Anexando uma ação")

Salve as alterações no storyboard e retorne a Visual Studio para Mac para sincronizar as alterações. Se você executar o aplicativo, o item de menu ao qual você conectou a ação personalizada será automaticamente habilitado/desabilitado (com base na janela com a ação aberta) e a seleção do item de menu será acionada na ação:

[![Testando a nova ação](menu-images/action05.png "Testando a nova ação")](menu-images/action05-large.png#lightbox)

<a name="Adding,_Editing_and_Deleting_Menus" />

### <a name="adding-editing-and-deleting-menus"></a>Adicionando, editando e excluindo menus

Como vimos nas seções anteriores, um aplicativo Xamarin. Mac vem com um número predefinido de menus e itens de menu padrão que os controles de interface do usuário específicos serão ativados automaticamente e responderão. Também vimos como adicionar código ao nosso aplicativo que também habilitará e responderá a esses itens padrão.

Nesta seção, examinaremos a remoção de itens de menu que não são necessários, a reorganização de menus e a adição de novos menus, itens de menu e ações.

Clique duas vezes no arquivo **Main. Storyboard** no **painel de soluções** para abri-lo para edição:

[![Editando a interface do usuário no Xcode](menu-images/maint01.png "Editando a interface do usuário no Xcode")](menu-images/maint01-large.png#lightbox)

Para nosso aplicativo Xamarin. Mac específico, não vamos usar o menu de **exibição** padrão, portanto, vamos removê-lo. Na **hierarquia de interface** , selecione o item de menu **Exibir** que faz parte da barra de menus principal:

![Selecionando o item de menu Exibir](menu-images/maint02.png "Selecionando o item de menu Exibir")

Pressione Delete ou Backspace para excluir o menu. Em seguida, não vamos usar todos os itens no menu **Formatar** e desejamos mover os itens que vamos usar de baixo para baixo nos menus. Na **hierarquia de interface** , selecione os seguintes itens de menu:

![Realçando vários itens](menu-images/maint03.png "Realçando vários itens")

Arraste os itens sob o **menu** pai do submenu onde eles estão atualmente:

[![Arrastando itens de menu para o menu pai](menu-images/maint04.png "Arrastando itens de menu para o menu pai")](menu-images/maint04-large.png#lightbox)

Agora, o menu deve ter a seguinte aparência:

[![Os itens no novo local](menu-images/maint05.png "Os itens no novo local")](menu-images/maint05-large.png#lightbox)

Em seguida, vamos arrastar o submenu de **texto** de baixo para baixo no menu **Formatar** e colocá-lo na barra de menus principal entre os menus de **formato** e **janela** :

[![O menu de texto](menu-images/maint06.png "O menu de texto")](menu-images/maint06-large.png#lightbox)

Vamos voltar para o menu **Formatar** e excluir o item de submenu **fonte** . Em seguida, selecione o menu **Formatar** e renomeie-o como "fonte":

[![O menu fonte](menu-images/maint07.png "O menu fonte")](menu-images/maint07-large.png#lightbox)

Em seguida, vamos criar um menu personalizado de frases predefinidas que serão acrescentadas automaticamente ao texto na exibição de texto quando forem selecionadas. Na caixa de pesquisa na parte inferior, no **Inspetor de biblioteca** , digite "menu". Isso facilitará a localização e o trabalho com todos os elementos de interface do usuário do menu:

![O Inspetor de biblioteca](menu-images/maint08.png "O Inspetor de biblioteca")

Agora, vamos fazer o seguinte para criar nosso menu:

1. Arraste um **item de menu** do **Inspetor de biblioteca** para a barra de menus entre os menus de **texto** e de **janela** : 

    ![Selecionando um novo item de menu na biblioteca](menu-images/maint10.png "Selecionando um novo item de menu na biblioteca")
2. Renomeie o item "frases": 

    [![Definindo o nome do menu](menu-images/maint09.png "Definindo o nome do menu")](menu-images/maint09-large.png#lightbox)
3. Em seguida, arraste um **menu** do **Inspetor de biblioteca**: 

    ![Selecionando um menu da biblioteca](menu-images/maint11.png "Selecionando um menu da biblioteca")
4. Solte o **menu** no novo **item de menu** que acabamos de criar e altere seu nome para "frases": 

    [![Editando o nome do menu](menu-images/maint12.png "Editando o nome do menu")](menu-images/maint12-large.png#lightbox)
5. Agora, vamos renomear os três **itens de menu** padrão "address", "Date" e "Greeting": 

    [![O menu de frases](menu-images/maint13.png "O menu de frases")](menu-images/maint13-large.png#lightbox)
6. Vamos adicionar um quarto **item de menu** arrastando um **item de menu** do **Inspetor de biblioteca** e chamando-o "Signature": 

    [![Editando o nome do item de menu](menu-images/maint14.png "Editando o nome do item de menu")](menu-images/maint14-large.png#lightbox)
7. Salve as alterações na barra de menus.

Agora, vamos criar um conjunto de ações personalizadas para que nossos novos itens de menu sejam expostos C# ao código. No Xcode, vamos alternar para o modo de exibição do **Assistente** :

[![Criando as ações necessárias](menu-images/maint15.png "Criando as ações necessárias")](menu-images/maint15-large.png#lightbox)

Vamos fazer o seguinte:

1. Controle-arraste do item de menu de **endereço** para o arquivo **AppDelegate. h** .
2. Alterne o tipo de **conexão** para **ação**: 

    [![Selecionando o tipo de ação](menu-images/maint17.png "Selecionando o tipo de ação")](menu-images/maint17-large.png#lightbox)
3. Insira um **nome** de "phraseAddress" e pressione o botão **conectar** para criar a nova ação: 

    [![Configurando a ação](menu-images/maint18.png "Configurando a ação")](menu-images/maint18-large.png#lightbox)
4. Repita as etapas acima para os itens de menu **Data**, **saudação**e **assinatura** : 

    [![As ações concluídas](menu-images/maint19.png "As ações concluídas")](menu-images/maint19-large.png#lightbox)
5. Salve as alterações na barra de menus.

Em seguida, precisamos criar uma tomada para nossa exibição de texto para que possamos ajustar seu conteúdo do código. Selecione o arquivo **ViewController. h** no **Editor do assistente** e crie uma nova tomada chamada `documentText`:

[![Criando uma tomada](menu-images/maint20.png "Criando uma tomada")](menu-images/maint20-large.png#lightbox)

Volte para Visual Studio para Mac para sincronizar as alterações do Xcode. Em seguida, edite o arquivo **ViewController.cs** e faça parecer com o seguinte:

```csharp
using System;

using AppKit;
using Foundation;

namespace MacMenus
{
    public partial class ViewController : NSViewController
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        public override NSObject RepresentedObject {
            get {
                return base.RepresentedObject;
            }
            set {
                base.RepresentedObject = value;
                // Update the view, if already loaded.
            }
        }

        public string Text {
            get { return documentText.Value; }
            set { documentText.Value = value; }
        } 
        #endregion

        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any additional setup after loading the view.
        }

        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            App.textEditor = this;
        }

        public override void ViewWillDisappear ()
        {
            base.ViewDidDisappear ();

            App.textEditor = null;
        }
        #endregion
    }
}
```

Isso expõe o texto da nossa exibição de texto fora da classe `ViewController` e informa o delegado do aplicativo quando a janela Obtém ou perde o foco. Agora edite o arquivo **AppDelegate.cs** e faça com que ele se pareça com o seguinte:

```csharp
using AppKit;
using Foundation;
using System;

namespace MacMenus
{
    [Register ("AppDelegate")]
    public partial class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public ViewController textEditor { get; set;} = null;
        #endregion

        #region Constructors
        public AppDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }
        #endregion

        #region Custom actions
        [Export ("openDocument:")]
        void OpenDialog (NSObject sender)
        {
            var dlg = NSOpenPanel.OpenPanel;
            dlg.CanChooseFiles = false;
            dlg.CanChooseDirectories = true;

            if (dlg.RunModal () == 1) {
                var alert = new NSAlert () {
                    AlertStyle = NSAlertStyle.Informational,
                    InformativeText = "At this point we should do something with the folder that the user just selected in the Open File Dialog box...",
                    MessageText = "Folder Selected"
                };
                alert.RunModal ();
            }
        }

        partial void phrasesAddress (Foundation.NSObject sender) {

            textEditor.Text += "Xamarin HQ\n394 Pacific Ave, 4th Floor\nSan Francisco CA 94111\n\n";
        }

        partial void phrasesDate (Foundation.NSObject sender) {

            textEditor.Text += DateTime.Now.ToString("D");
        }

        partial void phrasesGreeting (Foundation.NSObject sender) {

            textEditor.Text += "Dear Sirs,\n\n";
        }

        partial void phrasesSignature (Foundation.NSObject sender) {

            textEditor.Text += "Sincerely,\n\nKevin Mullins\nXamarin,Inc.\n";
        }
        #endregion
    }
}
```

Aqui, fizemos a `AppDelegate` uma classe parcial para que possamos usar as ações e saídas que definimos em Interface Builder. Também expõemos um `textEditor` para controlar qual janela está em foco no momento.

Os métodos a seguir são usados para lidar com nosso menu personalizado e itens de menu:

```csharp
partial void phrasesAddress (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += "Xamarin HQ\n394 Pacific Ave, 4th Floor\nSan Francisco CA 94111\n\n";
}

partial void phrasesDate (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += DateTime.Now.ToString("D");
}

partial void phrasesGreeting (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += "Dear Sirs,\n\n";
}

partial void phrasesSignature (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += "Sincerely,\n\nKevin Mullins\nXamarin,Inc.\n";
}
```

Agora, se executarmos nosso aplicativo, todos os itens no menu de **frases** estarão ativos e adicionaremos a frase atribuir à exibição de texto quando selecionado:

![Um exemplo do aplicativo em execução](menu-images/maint21.png "Um exemplo do aplicativo em execução")

Agora que temos as noções básicas de como trabalhar com a barra de menus do aplicativo, vamos examinar a criação de um menu contextual personalizado.

### <a name="creating-menus-from-code"></a>Criando menus a partir de código

Além de criar menus e itens de menu com o Interface Builder do Xcode, pode haver ocasiões em que um aplicativo Xamarin. Mac precisa criar, modificar ou remover um menu, um submenu ou um item de menu do código.

No exemplo a seguir, uma classe é criada para armazenar as informações sobre os itens de menu e os subsites que serão criados dinamicamente em tempo real:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace AppKit.TextKit.Formatter
{
    public class LanguageFormatCommand : NSObject
    {
        #region Computed Properties
        public string Title { get; set; } = "";
        public string Prefix { get; set; } = "";
        public string Postfix { get; set; } = "";
        public List<LanguageFormatCommand> SubCommands { get; set; } = new List<LanguageFormatCommand>();
        #endregion

        #region Constructors
        public LanguageFormatCommand () {

        }

        public LanguageFormatCommand (string title)
        {
            // Initialize
            this.Title = title;
        }

        public LanguageFormatCommand (string title, string prefix)
        {
            // Initialize
            this.Title = title;
            this.Prefix = prefix;
        }

        public LanguageFormatCommand (string title, string prefix, string postfix)
        {
            // Initialize
            this.Title = title;
            this.Prefix = prefix;
            this.Postfix = postfix;
        }
        #endregion
    }
}
```

#### <a name="adding-menus-and-items"></a>Adicionando menus e itens

Com essa classe definida, a rotina a seguir analisará uma coleção de `LanguageFormatCommand`objects e criará recursivamente novos menus e itens de menu acrescentando-os à parte inferior do menu existente (criado no Interface Builder) que foi passado:

```csharp
private void AssembleMenu(NSMenu menu, List<LanguageFormatCommand> commands) {
    NSMenuItem menuItem;

    // Add any formatting commands to the Formatting menu
    foreach (LanguageFormatCommand command in commands) {
        // Add separator or item?
        if (command.Title == "") {
            menuItem = NSMenuItem.SeparatorItem;
        } else {
            menuItem = new NSMenuItem (command.Title);

            // Submenu?
            if (command.SubCommands.Count > 0) {
                // Yes, populate submenu
                menuItem.Submenu = new NSMenu (command.Title);
                AssembleMenu (menuItem.Submenu, command.SubCommands);
            } else {
                // No, add normal menu item
                menuItem.Activated += (sender, e) => {
                    // Apply the command on the selected text
                    TextEditor.PerformFormattingCommand (command);
                };
            }
        }
        menu.AddItem (menuItem);
    }
}
``` 

Para qualquer `LanguageFormatCommand` objeto que tenha uma propriedade de `Title` em branco, essa rotina cria um **item de menu separador** (uma linha cinza fina) entre seções de menu:

```csharp
menuItem = NSMenuItem.SeparatorItem;
```

Se um título for fornecido, um novo item de menu com esse título será criado:

```csharp
menuItem = new NSMenuItem (command.Title);
``` 

Se o objeto `LanguageFormatCommand` contiver objetos `LanguageFormatCommand` filhos, um submenu será criado e o método `AssembleMenu` será chamado recursivamente para criar esse menu:

```csharp
menuItem.Submenu = new NSMenu (command.Title);
AssembleMenu (menuItem.Submenu, command.SubCommands);
```

Para qualquer novo item de menu que não tenha subsites, o código é adicionado para manipular o item de menu que está sendo selecionado pelo usuário:

```csharp
menuItem.Activated += (sender, e) => {
    // Do something when the menu item is selected
    ...
};
```

#### <a name="testing-the-menu-creation"></a>Testando a criação do menu

Com todo o código acima em vigor, se a seguinte coleção de objetos de `LanguageFormatCommand` foi criada:

```csharp
// Define formatting commands
FormattingCommands.Add(new LanguageFormatCommand("Strong","**","**"));
FormattingCommands.Add(new LanguageFormatCommand("Emphasize","_","_"));
FormattingCommands.Add(new LanguageFormatCommand("Inline Code","`","`"));
FormattingCommands.Add(new LanguageFormatCommand("Code Block","```\n","\n```"));
FormattingCommands.Add(new LanguageFormatCommand("Comment","<!--","-->"));
FormattingCommands.Add (new LanguageFormatCommand ());
FormattingCommands.Add(new LanguageFormatCommand("Unordered List","* "));
FormattingCommands.Add(new LanguageFormatCommand("Ordered List","1. "));
FormattingCommands.Add(new LanguageFormatCommand("Block Quote","> "));
FormattingCommands.Add (new LanguageFormatCommand ());

var Headings = new LanguageFormatCommand ("Headings");
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 1","# "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 2","## "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 3","### "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 4","#### "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 5","##### "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 6","###### "));
FormattingCommands.Add (Headings);

FormattingCommands.Add(new LanguageFormatCommand ());
FormattingCommands.Add(new LanguageFormatCommand("Link","[","]()"));
FormattingCommands.Add(new LanguageFormatCommand("Image","![](",")"));
FormattingCommands.Add(new LanguageFormatCommand("Image Link","[![](",")](LinkImageHere)"));
```

E essa coleção passada para a função `AssembleMenu` (com o menu **Formatar** definido como base), os seguintes menus dinâmicos e itens de menu seriam criados:

![Os novos itens de menu no aplicativo em execução](menu-images/dynamic01.png "Os novos itens de menu no aplicativo em execução")

#### <a name="removing-menus-and-items"></a>Removendo menus e itens

Se precisar remover qualquer menu ou item de menu da interface do usuário do aplicativo, você poderá usar o método `RemoveItemAt` da classe `NSMenu` simplesmente fornecendo a ele o índice baseado em zero do item a ser removido.

Por exemplo, para remover os menus e itens de menu criados pela rotina acima, você pode usar o seguinte código:

```csharp
public void UnpopulateFormattingMenu(NSMenu menu) {

    // Remove any additional items
    for (int n = (int)menu.Count - 1; n > 4; --n) {
        menu.RemoveItemAt (n);
    }
}
```

No caso do código acima, os primeiros quatro itens de menu são criados no Interface Builder do Xcode e estão ausentes disponíveis no aplicativo, portanto, eles não são removidos dinamicamente.

<a name="Contextual_Menus" />

## <a name="contextual-menus"></a>Menus contextuais

Os menus contextuais aparecem quando o usuário clica com o botão direito do mouse ou clica em um item em uma janela. Por padrão, vários dos elementos da interface do usuário criados no macOS já têm menus contextuais anexados a eles (como a exibição de texto). No entanto, pode haver ocasiões em que desejamos criar nossos próprios menus contextuais personalizados para um elemento de interface do usuário que adicionamos a uma janela.

Vamos editar nosso arquivo **. Storyboard principal** no Xcode e adicionar uma janela de **janela** ao nosso design, definir sua **classe** como "NSPanel" no **Inspetor de identidade**, adicionar um novo item de **Assistente** ao menu **janela** e anexá-lo ao novo janela usando um **show transição**:

[![Configurando o tipo transição](menu-images/context01.png "Configurando o tipo transição")](menu-images/context01-large.png#lightbox)

Vamos fazer o seguinte:

1. Arraste um **rótulo** do **Inspetor de biblioteca** para a janela do **painel** e defina seu texto como "Propriedade": 

    [![Editando o valor do rótulo](menu-images/context03.png "Editando o valor do rótulo")](menu-images/context03-large.png#lightbox)
2. Em seguida, arraste um **menu** do **Inspetor de biblioteca** para o controlador de exibição na hierarquia de exibição e renomeie os três itens de menu padrão **documento**, **texto** e **fonte**:

    [![Os itens de menu necessários](menu-images/context02.png "Os itens de menu necessários")](menu-images/context02-large.png#lightbox)
3. Agora, arraste o botão de controle do **rótulo da propriedade** para o **menu**:

    [![Arrastando para criar um transição](menu-images/context04.png "Arrastando para criar um transição")](menu-images/context04-large.png#lightbox)
4. Na caixa de diálogo pop-up, selecione **menu**: 

    ![Configurando o tipo transição](menu-images/context05.png "Configurando o tipo transição")
5. No **Inspetor de identidade**, defina a classe do controlador de exibição como "PanelViewController": 

    [![Definindo a classe transição](menu-images/context10.png "Definindo a classe transição")](menu-images/context10-large.png#lightbox)
6. Volte para Visual Studio para Mac para sincronizar e, em seguida, retorne para Interface Builder.
7. Alterne para o **Editor do assistente** e selecione o arquivo **PanelViewController. h** .
8. Crie uma ação para o item de menu de **documento** chamado `propertyDocument`: 

    [![Configurando a ação](menu-images/context06.png "Configurando a ação")](menu-images/context06-large.png#lightbox)
9. Repita a criação de ações para os itens de menu restantes: 

    [![As ações necessárias](menu-images/context07.png "As ações necessárias")](menu-images/context07-large.png#lightbox)
10. Por fim, crie uma tomada para o **rótulo de propriedade** chamado `propertyLabel`: 

    [![Configurando a tomada](menu-images/context08.png "Configurando a tomada")](menu-images/context08-large.png#lightbox)
11. Salve as alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Edite o arquivo **PanelViewController.cs** e adicione o seguinte código:

```csharp
partial void propertyDocument (Foundation.NSObject sender) {
    propertyLabel.StringValue = "Document";
}

partial void propertyFont (Foundation.NSObject sender) {
    propertyLabel.StringValue = "Font";
}

partial void propertyText (Foundation.NSObject sender) {
    propertyLabel.StringValue = "Text";
}
```

Agora, se executarmos o aplicativo e clicarmos com o botão direito do mouse no rótulo da propriedade no painel, veremos nosso menu contextual personalizado. Se selecionarmos e item no menu, o valor do rótulo será alterado:

![O menu contextual em execução](menu-images/context09.png "O menu contextual em execução")

Em seguida, vamos examinar a criação de menus da barra de status.

## <a name="status-bar-menus"></a>Menus da barra de status

Os menus da barra de status exibem uma coleção de itens de menu de status que fornecem interação com ou comentários para o usuário, como um menu ou uma imagem que reflete o estado de um aplicativo. O menu da barra de status de um aplicativo é habilitado e ativo mesmo que o aplicativo esteja em execução em segundo plano. A barra de status em todo o sistema reside no lado direito da barra de menus do aplicativo e é a única barra de status disponível atualmente no macOS.

Vamos editar nosso arquivo **AppDelegate.cs** e fazer com que o método `DidFinishLaunching` se pareça com o seguinte:

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    // Create a status bar menu
    NSStatusBar statusBar = NSStatusBar.SystemStatusBar;

    var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);
    item.Title = "Text";
    item.HighlightMode = true;
    item.Menu = new NSMenu ("Text");

    var address = new NSMenuItem ("Address");
    address.Activated += (sender, e) => {
        PhraseAddress(address);
    };
    item.Menu.AddItem (address);

    var date = new NSMenuItem ("Date");
    date.Activated += (sender, e) => {
        PhraseDate(date);
    };
    item.Menu.AddItem (date);

    var greeting = new NSMenuItem ("Greeting");
    greeting.Activated += (sender, e) => {
        PhraseGreeting(greeting);
    };
    item.Menu.AddItem (greeting);

    var signature = new NSMenuItem ("Signature");
    signature.Activated += (sender, e) => {
        PhraseSignature(signature);
    };
    item.Menu.AddItem (signature);
}
```

`NSStatusBar statusBar = NSStatusBar.SystemStatusBar;` nos dá acesso à barra de status de todo o sistema. `var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);` cria um novo item da barra de status. A partir daí, criamos um menu e um número de itens de menu e anexamos o menu ao item da barra de status que acabamos de criar. 

Se executarmos o aplicativo, o novo item da barra de status será exibido. Selecionar um item no menu irá alterar o texto na exibição de texto: 

![O menu da barra de status em execução](menu-images/statusbar01.png "O menu da barra de status em execução")

Em seguida, vamos examinar a criação de itens de menu de encaixe personalizados.

## <a name="custom-dock-menus"></a>Menus de encaixe personalizados

O menu Dock aparece para o aplicativo Mac quando o usuário clica com o botão direito do mouse ou clica no ícone do aplicativo no Dock:

![Um menu de encaixe personalizado](menu-images/dock01.png "Um menu de encaixe personalizado")

Vamos criar um menu de encaixe personalizado para nosso aplicativo fazendo o seguinte:

1. Em Visual Studio para Mac, clique com o botão direito do mouse no projeto do aplicativo e selecione **adicionar**  > **novo arquivo...** Na caixa de diálogo novo arquivo, selecione **Xamarin. Mac**  > **definição de interface vazia**, use "DockMenu" para o **nome** e clique no botão **novo** para criar o novo arquivo **DockMenu. xib** :

    ![Adicionando uma definição de interface vazia](menu-images/dock02.png "Adicionando uma definição de interface vazia")
2. No **painel de soluções**, clique duas vezes no arquivo **DockMenu. xib** para abri-lo para edição no Xcode. Crie um novo **menu** com os seguintes itens: **endereço**, **Data**, **saudação**e **assinatura** 

    [![Definindo a interface do usuário](menu-images/dock03.png "Definindo a interface do usuário")](menu-images/dock03-large.png#lightbox)
3. Em seguida, vamos conectar nossos novos itens de menu às nossas ações existentes que criamos para nosso menu personalizado na seção [adicionando, editando e excluindo menus](#Adding,_Editing_and_Deleting_Menus) acima. Alterne para o **Inspetor de conexão** e selecione o **primeiro Respondente** na **hierarquia de interface**. Role para baixo e localize a ação `phraseAddress:`. Arraste uma linha do círculo nessa ação para o item de menu de **endereço** :

    [![Arrastando para conectar uma ação](menu-images/dock04.png "Arrastando para conectar uma ação")](menu-images/dock04-large.png#lightbox)
4. Repita para todos os outros itens de menu anexando-os às ações correspondentes: 

    [![As ações necessárias](menu-images/dock05.png "As ações necessárias")](menu-images/dock05-large.png#lightbox)
5. Em seguida, selecione o **aplicativo** na **hierarquia de interface**. No **Inspetor de conexão**, arraste uma linha do círculo na tomada `dockMenu` para o menu que acabamos de criar:

    [![Arrastando a conexão com a tomada](menu-images/dock06.png "Arrastando a conexão com a tomada")](menu-images/dock06-large.png#lightbox)
6. Salve suas alterações e volte para Visual Studio para Mac para sincronizar com o Xcode.
7. Clique duas vezes no arquivo **info. plist** para abri-lo para edição: 

    [![Editando o arquivo info. plist](menu-images/dock07.png "Editando o arquivo info. plist")](menu-images/dock07-large.png#lightbox)
8. Clique na guia **origem** na parte inferior da tela: 

    [![Selecionando a exibição de origem](menu-images/dock08.png "Selecionando a exibição de origem")](menu-images/dock08-large.png#lightbox)
9. Clique em **Adicionar nova entrada**, clique no botão de adição verde, defina o nome da propriedade como "AppleDockMenu" e o valor como "DockMenu" (o nome do nosso novo arquivo. xib sem a extensão): 

    [![Adicionando o item DockMenu](menu-images/dock09.png "Adicionando o item DockMenu")](menu-images/dock09-large.png#lightbox)

Agora, se executarmos nosso aplicativo e clicarmos com o botão direito do mouse em seu ícone no Dock, nossos novos itens de menu serão exibidos:

![Um exemplo do menu Dock em execução](menu-images/dock10.png "Um exemplo do menu Dock em execução")

Se selecionarmos um dos itens personalizados no menu, o texto em nossa exibição de texto será modificado.

<a name="Pop-up_Menus_and_Pull-Down_Lists" />

## <a name="pop-up-button-and-pull-down-lists"></a>Listas de botões pop-up e suspensos

Um botão pop-up exibe um item selecionado e apresenta uma lista de opções a serem selecionadas quando clicado pelo usuário. Uma lista suspensa é um tipo de botão pop-up geralmente usado para selecionar comandos específicos para o contexto da tarefa atual. Ambos podem aparecer em qualquer lugar em uma janela.

Vamos criar um botão pop-up personalizado para nosso aplicativo fazendo o seguinte:

1. Edite o arquivo **Main. Storyboard** no Xcode e arraste um **botão pop-up** do **Inspetor de biblioteca** para a janela do **painel** que criamos na seção de [menus contextuais](#Contextual_Menus) : 

    [![Adicionando um botão pop-up](menu-images/popup01.png "Adicionando um botão pop-up")](menu-images/popup01-large.png#lightbox)
2. Adicionar um novo item de menu e definir os títulos dos itens no pop-up para: **endereço**, **Data**, **saudação**e **assinatura** 

    [![Configurando os itens de menu](menu-images/popup02.png "Configurando os itens de menu")](menu-images/popup02-large.png#lightbox)
3. Em seguida, vamos conectar nossos novos itens de menu às ações existentes que criamos para nosso menu personalizado na seção [adicionando, editando e excluindo menus](#Adding,_Editing_and_Deleting_Menus) acima. Alterne para o **Inspetor de conexão** e selecione o **primeiro Respondente** na **hierarquia de interface**. Role para baixo e localize a ação `phraseAddress:`. Arraste uma linha do círculo nessa ação para o item de menu de **endereço** : 

    [![Arrastando para conectar uma ação](menu-images/popup03.png "Arrastando para conectar uma ação")](menu-images/popup03-large.png#lightbox)
4. Repita para todos os outros itens de menu anexando-os às ações correspondentes: 

    [![Todas as ações necessárias](menu-images/popup04.png "Todas as ações necessárias")](menu-images/popup04-large.png#lightbox)
5. Salve suas alterações e volte para Visual Studio para Mac para sincronizar com o Xcode.

Agora, se executarmos nosso aplicativo e selecionarmos um item do pop-up, o texto em nossa exibição de texto será alterado:

![Um exemplo de pop-up em execução](menu-images/popup05.png "Um exemplo de pop-up em execução")

Você pode criar e trabalhar com listas suspensas exatamente da mesma maneira que os botões pop-up. Em vez de anexar a uma ação existente, você pode criar suas próprias ações personalizadas, exatamente como fizemos para nosso menu contextual na seção de [menus contextuais](#Contextual_Menus) .

## <a name="summary"></a>Resumo

Este artigo deu uma visão detalhada de como trabalhar com menus e itens de menu em um aplicativo Xamarin. Mac. Primeiro, examinamos a barra de menus do aplicativo e examinamos a criação de menus contextuais. em seguida, examinamos os menus de barra de status e os menus de encaixe personalizados. Por fim, abordamos os menus pop-up e as listas suspensas.

## <a name="related-links"></a>Links relacionados

- [MacMenus (exemplo)](https://docs.microsoft.com/samples/xamarin/mac-samples/macmenus)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Diretrizes de interface humana – menus](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/)
- [Introdução aos menus do aplicativo e listas pop-up](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/MenuList/MenuList.html)
