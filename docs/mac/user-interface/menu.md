---
title: Menus no xamarin. Mac
description: Este artigo aborda o trabalho com menus em um aplicativo xamarin. Mac. Ele descreve criando e mantendo os menus e itens de menu no Xcode e Interface Builder, trabalhar com eles por meio de programação.
ms.prod: xamarin
ms.assetid: 5D367F8E-3A76-4995-8A89-488530FAD802
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: d12815c92c1f608a5df4b3869fe9a17cb604b47a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109673"
---
# <a name="menus-in-xamarinmac"></a>Menus no xamarin. Mac

_Este artigo aborda o trabalho com menus em um aplicativo xamarin. Mac. Ele descreve criando e mantendo os menus e itens de menu no Xcode e Interface Builder, trabalhar com eles por meio de programação._

Ao trabalhar com c# e .NET em um aplicativo xamarin. Mac, você tem acesso aos menus Cocoa mesmos que um desenvolvedor que trabalha em Objective-C e Xcode faz. Como o xamarin. Mac se integra diretamente com Xcode, você pode usar o Interface Builder do Xcode para criar e manter suas barras de menus, menus e itens de menu (ou, opcionalmente, criá-los diretamente em código c#).

Menus são uma parte integrante da experiência do usuário de um aplicativo Mac e normalmente aparecem em várias partes da interface do usuário:

- **Barra de menus do aplicativo** -esse é o menu principal que aparece na parte superior da tela para cada aplicativo do Mac.
- **Menus contextuais** -eles aparecem quando o usuário clica ou um item em uma janela de cliques de controle.
- **A barra de status** -esta é a área na extrema direita da barra de menu do aplicativo que aparece na parte superior da tela (à esquerda do relógio da barra do menu) e cresce à esquerda que itens são adicionados a ele.
- **Menu de encaixe** -menu para cada aplicativo no dock que aparece quando o usuário clica ou o ícone do aplicativo de controle de cliques ou quando o usuário left-clicks o ícone e mantém o botão do mouse pressionado.
- **Botão de pop-up e listas suspensas** -um botão de pop-up exibe um item selecionado e apresenta uma lista de opções para selecionar a partir de quando clicado pelo usuário. Uma lista suspensa é um tipo de botão pop-up, geralmente usado para selecionar comandos específicos ao contexto da tarefa atual. Ambos podem aparecer em qualquer lugar em uma janela.

[![Um menu de exemplo](menu-images/intro01.png "um menu de exemplo")](menu-images/intro01-large.png#lightbox)

Neste artigo, abordaremos os fundamentos de trabalhar com itens de menu em um aplicativo xamarin. Mac, menus e barras de menus Cocoa. É altamente recomendável que você trabalhe por meio de [Hello, Mac](~/mac/get-started/hello-mac.md) pela primeira vez, especificamente o artigo a [Introdução ao Interface Builder e Xcode](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [saídas e ações](~/mac/get-started/hello-mac.md#outlets-and-actions) seções, como ela aborda os principais conceitos e técnicas que usaremos neste artigo.

Talvez você queira dar uma olhada o [classes expondo c# / métodos para Objective-C](~/mac/internals/how-it-works.md) seção o [recursos internos de xamarin. Mac](~/mac/internals/how-it-works.md) documentar Além disso, ele explica o `Register` e `Export` atributos usado para transmissão-up suas classes de c# a objetos de Objective-C e elementos de interface do usuário.

## <a name="the-applications-menu-bar"></a>Barra de menus do aplicativo 

Diferentemente dos aplicativos em execução no sistema operacional Windows em que cada janela pode ter sua própria barra de menus anexada a ele, todos os aplicativos em execução no macOS tem uma barra de menus único que é executado na parte superior da tela que é usada para cada janela do aplicativo:

[![Uma barra de menus](menu-images/appmenu01.png "uma barra de menus")](menu-images/appmenu01-large.png#lightbox)

Itens nessa barra de menus ativados ou desativados com base no contexto atual ou estado do aplicativo e sua interface do usuário em um determinado momento. Por exemplo: se o usuário seleciona um campo de texto, os itens na **edite** menu será vem habilitado como **cópia** e **Recortar**.

De acordo com a Apple e, por padrão, todos os aplicativos macOS tem um conjunto padrão de menus e itens de menu que aparecem na barra de menus do aplicativo:

- **Menu Apple** -esse menu fornece acesso ao sistema amplos itens que estão disponíveis para o usuário em todos os momentos, independentemente de qual aplicativo está em execução. Esses itens não podem ser modificados pelo desenvolvedor.
- **Menu do aplicativo** -esse menu exibe o nome do aplicativo em negrito e ajuda o usuário a identificar qual aplicativo está em execução no momento. Ele contém itens que se aplicam ao aplicativo como um todo e não um determinado documento ou processo como sair do aplicativo.
- **Menu arquivo** - itens usados para criar, abrir ou salvar documentos que o aplicativo funciona com. Se seu aplicativo não é baseado no documento, esse menu pode ser renomeado ou removido.
- **Menu Editar** -contém comandos como **Recortar**, **cópia**, e **colar** que são usados para editar ou modificar os elementos na interface do usuário do aplicativo.
- **Menu Formatar** - se o aplicativo funciona com o texto, esse menu contém comandos para ajustar a formatação do texto.
- **Menu Exibir** -contém os comandos que afetam como o conteúdo é exibido (exibido) na interface do usuário do aplicativo.
- **Menus específicos de aplicativo** -esses são todos os menus que são específicos para seu aplicativo (por exemplo, um menu de indicadores para um navegador da web). Elas devem aparecer entre os **modo de exibição** e **janela** menus na barra de.
- **Menu janela** -contém comandos para trabalhar com o windows em seu aplicativo, bem como uma lista de janelas abertas atuais.
- **Menu Ajuda** -se seu aplicativo fornece ajuda na tela, o menu de Ajuda deve ser o menu de mais à direita da barra. 

Para obter mais informações sobre a barra de menus do aplicativo e os menus padrão e os itens de menu, consulte da Apple [diretrizes de Interface Humana](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/).

### <a name="the-default-application-menu-bar"></a>A barra de menus do aplicativo padrão

Sempre que você cria um novo projeto do xamarin. Mac, você obtém automaticamente uma barra de menus de aplicativo padrão padrão, que tem os itens típicos que um aplicativo macOS teria normalmente (conforme discutido na seção acima). Barra de menus padrão do seu aplicativo é definida na **Main. Storyboard** arquivo (juntamente com o restante da interface do usuário seu aplicativo) sob o projeto na **painel de soluções**:  

![Selecione o storyboard principal](menu-images/appmenu02.png "selecione o storyboard principal")

Clique duas vezes o **Main. Storyboard** arquivo para abri-lo para edição no Interface Builder do Xcode e você verá a interface do editor de menu:

[![Editando a interface do usuário no Xcode](menu-images/defaultbar01.png "editando a interface do usuário no Xcode")](menu-images/defaultbar01-large.png#lightbox)

Aqui podemos clicar em itens, como o **aberto** item de menu na **arquivo** menu e editar ou ajustar suas propriedades no **Inspetor de atributos**:

[![Atributos do menu de edição](menu-images/defaultbar02.png "editando os atributos do menu")](menu-images/defaultbar02-large.png#lightbox)

Vamos explorar adicionando, editando e excluindo menus e itens neste artigo. Agora apenas desejamos ver quais menus e itens de menu estão disponíveis por padrão e como eles foram automaticamente expostos ao código por meio de um conjunto de predefinidos saídas e ações (para obter mais informações, consulte nosso [saídas e ações](~/mac/get-started/hello-mac.md#outlets-and-actions) documentação).

Por exemplo, se clicarmos na **Inspetor de Conexão** para o **abra** item de menu, podemos ver tudo está automaticamente conectado até o `openDocument:` ação: 

[![Exibindo a ação anexada](menu-images/defaultbar03.png "exibindo a ação anexada")](menu-images/defaultbar03-large.png#lightbox)

Se você selecionar o **Respondente primeiro** na **hierarquia de Interface** e role para baixo no **Inspetor de Conexão**, e você verá a definição do `openDocument:` ação que o **abrir** item de menu está anexado a (juntamente com várias outras ações padrão para o aplicativo que são e não são conectadas automaticamente controles):

[![Exibindo anexadas todas as ações](menu-images/defaultbar04.png "exibindo todas as ações anexadas")](menu-images/defaultbar04-large.png#lightbox) 

Por que isso é importante? Na próxima seção será exibida como essas ações automaticamente definidas funcionam com outros elementos de interface de usuário do Cocoa para automaticamente habilitar e desabilitar itens de menu, bem como, fornecer uma funcionalidade interna para os itens.

Mais tarde que usaremos essas ações internas para habilitar e desabilitar itens de código e fornecer nossa própria funcionalidade quando eles estão selecionados.

<a name="Built-In_Menu_Functionality" />

### <a name="built-in-menu-functionality"></a>Funcionalidade do menu interno

Se você executar um aplicativo xamarin. Mac recém-criado antes de adicionar os itens de interface do usuário ou o código, você observará que alguns itens são automaticamente conectada e habilitadas para você (com total funcionalidade interna automaticamente), como o **Quit** item de **aplicativo** menu:

![Um item de menu habilitada](menu-images/appmenu03.png "um item de menu habilitado")

Embora outros itens de menu, como **Recortar**, **cópia**, e **colar** não são:

![Itens de menu desabilitados](menu-images/appmenu04.png "itens de menu desabilitados")

Vamos parar o aplicativo e clique duas vezes o **Main. Storyboard** arquivo na **painel de soluções** para abri-lo para edição no Xcode Interface Builder do. Em seguida, arraste uma **exibição de texto** da **biblioteca** no controlador de exibição da janela no **Editor de Interface**:

[![Selecionar uma exibição de texto da biblioteca](menu-images/appmenu05.png "selecionando uma exibição de texto da biblioteca")](menu-images/appmenu05-large.png#lightbox)

No **Editor de restrição** vamos fixar o modo de exibição de texto com bordas da janela e defini-lo em que ele aumenta e diminui com a janela clicando em todos os quatro I vermelhas na parte superior do editor e clicando o **adicionar 4 restrições** botão:

[![As restrições de edição](menu-images/appmenu06.png "as restrições de edição")](menu-images/appmenu06-large.png#lightbox)

Salve as alterações ao design da interface do usuário e mudar de volta do Visual Studio para Mac para sincronizar as alterações com o seu projeto xamarin. Mac. Agora iniciar o aplicativo, digite algum texto no modo de exibição de texto, selecione-o e abra o **editar** menu:

![Os itens de menu são automaticamente habilitados/desabilitados](menu-images/appmenu07.png "os itens de menu são automaticamente habilitados/desabilitados")

Observe como o **Recortar**, **cópia**, e **colar** itens estão automaticamente habilitados e totalmente funcional, tudo isso sem escrever uma única linha de código. 

O que está acontecendo aqui? Lembre-se internos predefinir as ações que vêm com fio até os itens de menu padrão (conforme apresentado acima), a maioria dos elementos de interface do usuário de Cocoa que fazem parte do macOS têm interno ganchos para ações específicas (como `copy:`). Portanto, quando eles são adicionados a uma janela ativa e selecionados, o item de menu correspondente ou itens anexados a essa ação são habilitados automaticamente. Se o usuário seleciona esse item de menu, a funcionalidade incorporada no elemento de interface do usuário é chamada e executada, tudo isso sem intervenção do desenvolvedor.

### <a name="enabling-and-disabling-menus-and-items"></a>Habilitando e desabilitando menus e itens

Por padrão, sempre que ocorre um evento de usuário, `NSMenu` automaticamente habilita e desabilita cada menu visível e o menu de item com base no contexto do aplicativo. Há três maneiras de habilitar/desabilitar um item:

- **Habilitação de menu automática** -um item de menu estará habilitado se `NSMenu` pode encontrar um objeto apropriado que responde a ação que o item é com fio-up para. Por exemplo, a exibição de texto acima que tinha um gancho interno para o `copy:` ação.
- **Ações personalizadas e validateMenuItem:** - para qualquer item de menu que é associado a um [janela ou exibição de ação personalizada de controlador](#Working-with-Custom-Window-Actions), você pode adicionar o `validateMenuItem:` ação e habilitar ou desabilitar manualmente itens de menu.
- **Habilitar menu manual** -definir manualmente as `Enabled` propriedade de cada `NSMenuItem` para habilitar ou desabilitar individualmente a cada item em um menu.

Para escolher um sistema, defina as `AutoEnablesItems` propriedade de um `NSMenu`. `true` é automático (o comportamento padrão) e `false` é manual. 

> [!IMPORTANT]
> Se você optar por usar a habilitação do menu manual, nenhum menu de itens, mesmo aqueles controlado por classes AppKit como `NSTextView`, são atualizados automaticamente. Você será responsável por habilitar e desabilitar todos os itens manualmente no código.

#### <a name="using-validatemenuitem"></a>Usando validateMenuItem

Conforme mencionado anteriormente, para qualquer item de menu que é associado a um [janela ou a ação de personalizada do controlador de exibição](#Working-with-Custom-Window-Actions), você pode adicionar o `validateMenuItem:` ação e habilitar ou desabilitar manualmente itens de menu.

No exemplo a seguir, o `Tag` propriedade será usada para decidir o tipo de item de menu que serão ser habilitado/desabilitado pelo `validateMenuItem:` ação com base no estado do texto selecionado em um `NSTextView`. O `Tag` propriedade foi definida no construtor de Interface para cada item de menu:

![Definindo a propriedade Tag](menu-images/validate01.png "definindo a propriedade Tag")

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

Quando esse código é executado, e nenhum texto estiver selecionado no `NSTextView`, os itens de menu de encapsulamento de duas são desabilitados (mesmo que eles são conectados às ações no controlador de exibição):

![Itens mostrando desabilitado](menu-images/validate02.png "mostrando desabilitado itens")

Se uma seção de texto é selecionada e o menu reaberto, os itens de menu de encapsulamento de dois estarão disponíveis:

![Itens mostrando habilitado](menu-images/validate03.png "mostrando habilitado itens")

## <a name="enabling-and-responding-to-menu-items-in-code"></a>Habilitando e responder aos itens de menu no código

Como temos visto acima, simplesmente adicionando elementos de interface de usuário Cocoa específicos ao nosso design de interface do usuário (como um campo de texto), vários dos itens de menu padrão serão habilitados e funcionam automaticamente, sem precisar escrever nenhum código. Próxima vamos dar uma olhada na adição de nosso próprio código do c# ao nosso projeto xamarin. Mac para habilitar um item de menu e fornecer funcionalidade quando o usuário seleciona a ele.

Por exemplo, digamos queremos que o usuário seja capaz de usar o **aberto** item o **arquivo** menu para selecionar uma pasta. Como esta é uma função de todo o aplicativo e o não limitado a um elemento de interface do usuário ou janela oferecem, vamos adicionar o código para lidar com isso para nosso representante de aplicativo.

No **painel de soluções**, clique duas vezes o `AppDelegate.CS` arquivo para abri-lo para edição:

![Selecionando o representante do aplicativo](menu-images/appmenu08.png "selecionando o representante do aplicativo")

Adicione o seguinte código abaixo a `DidFinishLaunching` método:

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

Vamos executar o aplicativo agora e abrir o **arquivo** menu: 

![No menu arquivo](menu-images/appmenu09.png "menu Arquivo")

Observe que o **abrir** item de menu agora está habilitado. Se selecionarmos, abra a caixa de diálogo será exibida:

![Uma caixa de diálogo aberta](menu-images/appmenu10.png "uma caixa de diálogo Abrir")

Se clicarmos a **abrir** botão, nossa mensagem de alerta será exibida:

![Um exemplo de mensagem de caixa de diálogo](menu-images/appmenu11.png "um exemplo de mensagem de caixa de diálogo")

Aqui a linha de chave foi `[Export ("openDocument:")]`, ele informa `NSMenu` que nosso **AppDelegate** tem um método `void OpenDialog (NSObject sender)` que responde ao `openDocument:` ação. Se você se lembrará acima, o **abrir** item de menu é automaticamente com fio-up para essa ação por padrão no Interface Builder:

[![Exibindo as ações anexadas](menu-images/defaultbar03.png "exibindo as ações anexadas")](menu-images/defaultbar03-large.png#lightbox)

Próxima Vejamos criando nosso próprio menu, itens de menu e as ações e responder a elas no código.

### <a name="working-with-the-open-recent-menu"></a>Trabalhando com o menu Abrir recente

Por padrão, o **arquivo** menu contém uma **abrir recente** item que controla os último vários arquivos que o usuário abriu com seu aplicativo. Se você estiver criando um `NSDocument` com base em aplicativo do xamarin. Mac, este menu será manipulado para você automaticamente. Para qualquer outro tipo de aplicativo xamarin. Mac, você será responsável por gerenciar e responder a este item de menu manualmente.

Para tratar manualmente os **abrir recente** menu, você primeiro precisará informar a ele um novo arquivo foi aberto ou salvo usando o seguinte:

```csharp
// Add document to the Open Recent menu
NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);
```

Mesmo que seu aplicativo não estiver usando `NSDocuments`, ainda é usar o `NSDocumentController` para manter o **abrir recente** menu enviando um `NSUrl` com o local do arquivo a ser o `NoteNewRecentDocumentURL` o método da `SharedDocumentController`.

Em seguida, você precisará substituir o `OpenFile` método de representante do aplicativo para abrir qualquer arquivo que o usuário seleciona o **abrir recente** menu. Por exemplo:

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

Retornar `true` se o arquivo pode ser aberto, caso contrário retorne `false` e será exibido um aviso interno para o usuário que não foi possível abrir o arquivo.

Porque o nome de arquivo e o caminho retornado do **abrir recente** menu, pode incluir um espaço, é preciso corretamente esse caractere de escape antes de criar um `NSUrl` ou obteremos um erro. Fazemos isso com o código a seguir:

```csharp
filename = filename.Replace (" ", "%20");
```

Por fim, criamos um `NSUrl` que aponta para o arquivo e o uso de um método auxiliar no aplicativo delegado para abrir uma nova janela e carregar o arquivo nele:

```csharp
var url = new NSUrl ("file://"+filename);
return OpenFile(url);
```

Para reunir tudo, vamos dar uma olhada em um exemplo de implementação em um **AppDelegate.cs** arquivo:

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

Com base nos requisitos do seu aplicativo, você não poderá abrir o mesmo arquivo em mais de uma janela ao mesmo tempo o usuário. Em nosso aplicativo de exemplo, se o usuário escolher um arquivo que já está aberto (a partir de **abrir recente** ou **abra...** itens de menu), a janela que contém o arquivo é colocada na frente.

Para fazer isso, usamos o seguinte código ao nosso método auxiliar:

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

Projetamos nossos `ViewController` classe para conter o caminho para o arquivo no seu `Path` propriedade. Em seguida, executamos um loop por meio de todas as janelas abertas no momento no aplicativo. Se o arquivo já está aberto em uma das janelas, ele será colocado na frente de todas as outras janelas usando:

```csharp
NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
```

Se nenhuma correspondência for encontrada, uma nova janela é aberta com o arquivo carregado e o arquivo está disponível de **abrir recente** menu:

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

### <a name="working-with-custom-window-actions"></a>Trabalhar com ações de janela personalizados

Assim como o interno **Respondente primeiro** ações que vêm com fio previamente para itens de menu padrão, você pode criar novas ações personalizadas e conectá-los a itens de menu no Interface Builder.

Primeiro, defina uma ação personalizada em um dos controladores de janela do seu aplicativo. Por exemplo:

```csharp
[Action("defineKeyword:")]
public void defineKeyword (NSObject sender) {
    // Preform some action when the menu is selected
    Console.WriteLine ("Request to define keyword");
}
```

Em seguida, clique duas vezes no arquivo de storyboard do aplicativo na **painel de soluções** para abri-lo para edição no Xcode Interface Builder do. Selecione o **Respondente primeiro** sob o **cena do aplicativo**, em seguida, alterne para o **Inspetor de atributos**:

![O Inspetor de atributos](menu-images/action01.png "o Inspetor de atributos")

Clique o **+** botão na parte inferior a **Inspetor de atributos** para adicionar uma nova ação personalizada:

![Adicionando uma nova ação](menu-images/action02.png "adicionando uma nova ação")

Dê a ele o mesmo nome que a ação personalizada que você criou em seu controlador de janela:

![O nome da ação de edição](menu-images/action03.png "editando o nome da ação")

CTRL + clique e arraste um item de menu para o **Respondente primeiro** sob o **cena aplicativo**. Na lista pop-up, selecione a nova ação que você acabou de criar (`defineKeyword:` neste exemplo):

![Anexando uma ação](menu-images/action04.png "anexando uma ação")

Salve as alterações ao storyboard e retorne ao Visual Studio para Mac sincronizar as alterações. Se você executar o aplicativo, o item de menu que você conectou a ação personalizada será automaticamente ser habilitado/desabilitado (com base na janela com a ação que está sendo aberto) e selecionando o item de menu disparará a ação:

[![Teste a nova ação](menu-images/action05.png "testar a nova ação")](menu-images/action05-large.png#lightbox)

<a name="Adding,_Editing_and_Deleting_Menus" />

### <a name="adding-editing-and-deleting-menus"></a>Adicionando, editando e excluindo menus

Como já vimos nas seções anteriores, um aplicativo xamarin. MAC é fornecido com um número predefinido de menus padrão e itens de menu que controles de interface do usuário específicos serão automaticamente ativar e responder a. Também vimos como adicionar código ao nosso aplicativo que também habilitará e responder a esses itens padrão.

Nesta seção vamos examinar removendo itens de menu que não é necessária, a reorganização de menus e adicionando novos menus, itens de menu e ações.

Clique duas vezes o **Main. Storyboard** arquivo na **painel de soluções** para abri-lo para edição:

[![Editando a interface do usuário no Xcode](menu-images/maint01.png "editando a interface do usuário no Xcode")](menu-images/maint01-large.png#lightbox)

Para nosso aplicativo xamarin. MAC específico não vamos usar o padrão **exibição** menu, portanto, vamos removê-lo. No **hierarquia de Interface** selecionar a **exibição** item de menu que é uma parte da barra de menus principal:

![Selecionando o item de menu do modo de exibição](menu-images/maint02.png "selecionando o item de menu do modo de exibição")

Pressione delete ou backspace para excluir o menu. Em seguida, não vamos usar todos os itens na **formato** menu e podemos deseja mover os itens, vamos usar em submenus. No **hierarquia de Interface** selecione os seguintes itens de menu:

![Realce de vários itens](menu-images/maint03.png "realce de vários itens")

Arraste os itens sob o pai **Menu** do submenu, onde atualmente estão:

[![Arrastando itens de menu ao menu pai](menu-images/maint04.png "arrastando itens de menu ao menu pai")](menu-images/maint04-large.png#lightbox)

O menu agora deve ser semelhante:

[![Os itens no novo local](menu-images/maint05.png "os itens no novo local")](menu-images/maint05-large.png#lightbox)

Próxima vamos arrastar o **texto** submenu horizontalmente na **formato** menu e coloque-o na barra de menu principal entre os **formato** e **janela** menus:

[![O menu de texto](menu-images/maint06.png "menu o texto")](menu-images/maint06-large.png#lightbox)

Vamos sob o **formato** menu e excluir os **fonte** item de submenu. Em seguida, selecione a **formato** menu e renomeie-a "Fonte":

[![O menu de fonte](menu-images/maint07.png "menu a fonte")](menu-images/maint07-large.png#lightbox)

Em seguida, vamos criar um menu personalizado de frases predefine automaticamente obter acrescentado ao texto no modo de exibição de texto quando eles estão selecionados. Na caixa de pesquisa na parte inferior na **Inspetor de biblioteca** tipo no "menu". Isso tornará mais fácil de localizar e trabalhar com todos os elementos de interface do usuário do menu:

![O Inspetor de biblioteca](menu-images/maint08.png "o Inspetor de biblioteca")

Agora vamos fazer o seguinte para criar nosso menu:

1. Arraste uma **Item de Menu** da **Inspetor de biblioteca** para a barra de menu entre o **texto** e **janela** menus: 

    ![Selecionando um item de menu novo na biblioteca](menu-images/maint10.png "selecionando um item de menu novo na biblioteca")
2. Renomear o item "Frases": 

    [![Configurando o nome do menu](menu-images/maint09.png "definindo o nome do menu")](menu-images/maint09-large.png#lightbox)
3. Em seguida arraste uma **Menu** da **Inspetor de biblioteca**: 

    ![Selecionando um menu da biblioteca](menu-images/maint11.png "selecionando um menu da biblioteca")
4. DROP, em seguida, **menus** na nova **Item de Menu** acabou de criar e altere seu nome para "Frases": 

    [![O nome do menu de edição](menu-images/maint12.png "editando o nome do menu")](menu-images/maint12-large.png#lightbox)
5. Agora vamos renomear o padrão de três **itens de Menu** "Address", "Data" e "Saudação": 

    [![O menu de frases](menu-images/maint13.png "menu a frases")](menu-images/maint13-large.png#lightbox)
6. Vamos adicionar uma quarta **Item de Menu** arrastando uma **Item de Menu** do **Inspetor de biblioteca** e chamando-a "Assinatura": 

    [![O nome do item de menu de edição](menu-images/maint14.png "editando o nome do item de menu")](menu-images/maint14-large.png#lightbox)
7. Salve as alterações à barra de menus.

Agora vamos criar um conjunto de ações personalizadas para que nossos novos itens de menu são expostos ao código c#. No Xcode vamos mudar para o **Assistant** exibição:

[![Criando as ações necessárias](menu-images/maint15.png "criando as ações necessárias")](menu-images/maint15-large.png#lightbox)

Vamos fazer o seguinte:

1. Arraste do controle a **endereço** item de menu para o **Appdelegate** arquivo.
2. Opção de **Conexão** de tipo para **ação**: 

    [![Selecionar o tipo de ação](menu-images/maint17.png "selecionando o tipo de ação")](menu-images/maint17-large.png#lightbox)
3. Insira um **nome** de "phraseAddress" e pressione a **Connect** botão para criar a nova ação: 

    [![Configurar a ação](menu-images/maint18.png "Configurando a ação")](menu-images/maint18-large.png#lightbox)
4. Repita as etapas acima para o **data**, **saudação**, e **assinatura** itens de menu: 

    [![As ações concluídas](menu-images/maint19.png "as ações concluídas")](menu-images/maint19-large.png#lightbox)
5. Salve as alterações à barra de menus.

Em seguida, precisamos criar uma saída para nossa exibição de texto para que possamos ajustar seu conteúdo do código. Selecione o **viewcontroller. H** arquivo na **Editor assistente** e criar uma nova saída chamada `documentText`:

[![Criação de uma tomada](menu-images/maint20.png "criando uma saída")](menu-images/maint20-large.png#lightbox)

Retorne ao Visual Studio para Mac sincronizar as alterações do Xcode. Em seguida editar as **ViewController.cs** de arquivo e torná-lo semelhante ao seguinte:

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

Isso expõe o texto da nossa exibição de texto fora do `ViewController` de classe e informa o representante do aplicativo quando a janela ganha ou perde o foco. Agora, edite a **AppDelegate.cs** de arquivo e torná-lo semelhante ao seguinte:

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

Aqui, fizemos o `AppDelegate` um parcial de classe para que podemos usar as ações e as saídas que definimos no Interface Builder. Também podemos expor um `textEditor` para controlar qual janela está em foco.

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

Agora, se executamos nosso aplicativo, todos os itens na **frase** menu estará ativo e adicionará a frase oferecem para o modo de exibição de texto quando selecionado:

![Um exemplo de como o aplicativo em execução](menu-images/maint21.png "um exemplo de como o aplicativo em execução")

Agora que temos os fundamentos de trabalhar com a barra de menus do aplicativo para baixo, vamos dar uma olhada na criação de um menu contextual personalizado.

### <a name="creating-menus-from-code"></a>Criando menus de código

Além de criar menus e itens de menu com o Interface Builder do Xcode, pode haver vezes quando um aplicativo xamarin. Mac precisa para criar, modificar ou remover um menu, submenu ou item de menu do código.

No exemplo a seguir, uma classe é criada para armazenar as informações sobre os itens de menu e submenus serão criados dinamicamente na hora:

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

Com essa classe é definida, a rotina a seguir analisa uma coleção de `LanguageFormatCommand`objetos recursivamente criar e novos menus e itens de menu, acrescentando-os na parte inferior do menu existente (criado no construtor de Interface) que foi passado:

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

Para qualquer `LanguageFormatCommand` objeto que tem um espaço em branco `Title` propriedade, essa rotina cria um **item de menu separador** (uma linha cinza fina) entre as seções de menu:

```csharp
menuItem = NSMenuItem.SeparatorItem;
```

Se for fornecido um título, um novo item de menu com esse título é criado:

```csharp
menuItem = new NSMenuItem (command.Title);
``` 

Se o `LanguageFormatCommand` filho do objeto contém `LanguageFormatCommand` objetos, um submenu é criado e o `AssembleMenu` método é chamado para criar esse menu de recursivamente:

```csharp
menuItem.Submenu = new NSMenu (command.Title);
AssembleMenu (menuItem.Submenu, command.SubCommands);
```

Para qualquer novo item de menu que não tem submenus, o código é adicionado ao manipular o item de menu selecionado pelo usuário:

```csharp
menuItem.Activated += (sender, e) => {
    // Do something when the menu item is selected
    ...
};
```

#### <a name="testing-the-menu-creation"></a>Testando a criação de menu

Com todo o código acima em vigor, se a seguinte coleção de `LanguageFormatCommand` objetos foram criados:

```csharp
// Define formatting commands
FormattingCommands.Add(new LanguageFormatCommand("Stong","**","**"));
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
FormattingCommands.Add(new LanguageFormatCommand("Image","![]("-")"));
FormattingCommands.Add(new LanguageFormatCommand("Image Link","[ ![]("-")](linkimagehere/index.md)"));
```

E que a coleção passada para o `AssembleMenu` função (com o **formato** Menu definir como a base), os seguintes menus dinâmicos e itens de menu seriam criados:

![Os novos itens de menu no aplicativo em execução](menu-images/dynamic01.png "os novos itens de menu no aplicativo em execução")

#### <a name="removing-menus-and-items"></a>Removendo de menus e itens

Se você precisar remover qualquer menu ou item de menu de interface do usuário do aplicativo, você pode usar o `RemoveItemAt` método da `NSMenu` classe simplesmente, dando a ela o zero com base em índice do item a ser removido.

Por exemplo, para remover os menus e itens de menu criados pela rotina acima, você pode usar o código a seguir:

```csharp
public void UnpopulateFormattingMenu(NSMenu menu) {

    // Remove any additional items
    for (int n = (int)menu.Count - 1; n > 4; --n) {
        menu.RemoveItemAt (n);
    }
}
```

No caso do código acima, os primeiros itens de quatro menu são criados no Interface Builder do Xcode e o materiais disponíveis no aplicativo, para que eles não são removidos dinamicamente.

<a name="Contextual_Menus" />

## <a name="contextual-menus"></a>Menus contextuais

Menus contextuais aparecem quando o usuário clica ou um item em uma janela de cliques de controle. Por padrão, vários dos elementos da interface do usuário incorporados macOS já têm menus contextuais anexadas a elas (por exemplo, a exibição de texto). No entanto, pode haver vezes em que desejaremos criar nossos próprios menus contextuais personalizados para um elemento de interface do usuário que adicionamos para uma janela.

Vamos Editar nosso **Main. Storyboard** arquivo no Xcode e adicione um **janela** janela para nosso projeto, defina seu **classe** para "NSPanel" no **doInspetordeidentidade**, adicione um novo **Assistant** do item para o **janela** menu e anexá-lo para a nova janela usando um **Mostrar Segue**:

[![Definindo o tipo de segue](menu-images/context01.png "definindo o tipo de segue")](menu-images/context01-large.png#lightbox)

Vamos fazer o seguinte:

1. Arraste uma **rótulo** da **Inspetor de biblioteca** até o **painel** janela e defina o texto para "Property": 

    [![Edição de valor do rótulo](menu-images/context03.png "editando o valor do rótulo")](menu-images/context03-large.png#lightbox)
2. Arraste em seguida uma **Menu** da **Inspetor de biblioteca** para o controlador de exibição na hierarquia de exibição e itens de menu padrão renomear os três **documento**, **texto**  e **fonte**:

    [![Os itens de menu necessária](menu-images/context02.png "os itens de menu necessária")](menu-images/context02-large.png#lightbox)
3. Agora de controle e arraste do **rótulo de propriedade** até a **Menu**:

    [![Arrastando para criar um segue](menu-images/context04.png "arrastando para criar um segue")](menu-images/context04-large.png#lightbox)
4. Na caixa de diálogo pop-up, selecione **Menu**: 

    ![Definindo o tipo de segue](menu-images/context05.png "definindo o tipo de segue")
5. Dos **Inspetor de identidade**, defina a classe do controlador de exibição para "PanelViewController": 

    [![Definindo a classe segue](menu-images/context10.png "definindo a classe segue")](menu-images/context10-large.png#lightbox)
6. Volte para o Visual Studio para Mac sincronizar e retorne ao construtor de Interface.
7. Alterne para o **Editor assistente** e selecione o **PanelViewController.h** arquivo.
8. Criar uma ação para o **documento** item de menu chamado `propertyDocument`: 

    [![Configurar a ação](menu-images/context06.png "Configurando a ação")](menu-images/context06-large.png#lightbox)
9. Repita ações de criação para os demais itens de menu: 

    [![As ações necessárias](menu-images/context07.png "as ações necessárias")](menu-images/context07-large.png#lightbox)
10. Por fim, criar uma saída para o **rótulo de propriedade** chamado `propertyLabel`: 

    [![Configurando a tomada](menu-images/context08.png "Configurando a saída")](menu-images/context08-large.png#lightbox)
11. Salve suas alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Editar o **PanelViewController.cs** arquivo e adicione o código a seguir:

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

Agora se podemos executar o aplicativo e com o botão direito no rótulo de propriedade no painel, veremos nosso menu contextual personalizado. Se selecionamos e de item de menu, o valor do rótulo será alterado:

![O menu contextual em execução](menu-images/context09.png "menu contextual em execução")

Próxima Vejamos Criando menus de barra de status.

## <a name="status-bar-menus"></a>Menus de barra de status

Menus de barra de status exibem uma coleção de itens de menu de status que fornecem a interação com ou comentários para o usuário, como um menu ou uma imagem refletindo o estado de um aplicativo. Menu da barra de status do aplicativo é habilitado e ativo, mesmo se o aplicativo é executado em segundo plano. A barra de status do sistema reside no lado direito da barra de menu do aplicativo e é a barra de Status só está disponível no macOS.

Vamos Editar nosso **AppDelegate.cs** do arquivo e verifique o `DidFinishLaunching` método são semelhantes ao seguinte:

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

`NSStatusBar statusBar = NSStatusBar.SystemStatusBar;` nos dá acesso à barra de status de todo o sistema. `var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);` cria um novo item de barra de status. A partir daí podemos criar um menu e um número de itens de menu e anexe o menu para o item da barra de status que acabamos de criar. 

Se executarmos o aplicativo, o novo item de barra de status será exibido. Selecionar um item de menu será alterado o texto no modo de exibição de texto: 

![No menu da barra de status em execução](menu-images/statusbar01.png "no menu da barra de status em execução")

Em seguida, vamos dar uma olhada na criação de itens de menu de encaixe personalizado.

## <a name="custom-dock-menus"></a>Menus de encaixe personalizado

O menu de encaixe é exibido para o aplicativo Mac quando o usuário clica ou o ícone do aplicativo no dock de cliques de controle:

![Menu de encaixe de um personalizado](menu-images/dock01.png "personalizado encaixar menu")

Vamos criar um menu de encaixe personalizado para nosso aplicativo fazendo o seguinte:

1. No Visual Studio para Mac, clique com botão direito no projeto do aplicativo e selecione **Add** > **novo arquivo...** Na caixa de diálogo Novo arquivo, selecione **xamarin. Mac** > **definição de Interface vazia**, use "DockMenu" para o **nome** e clique no **novo**  botão para criar o novo **DockMenu.xib** arquivo:

    ![Adicionando uma definição de interface vazia](menu-images/dock02.png "adicionando uma definição de interface vazia")
2. No **painel de soluções**, clique duas vezes o **DockMenu.xib** arquivo para abri-lo para edição no Xcode. Criar um novo **menus** com os seguintes itens: **endereço**, **data**, **saudação**, e **assinatura** 

    [![Dispor a interface do usuário](menu-images/dock03.png "dispor a interface do usuário")](menu-images/dock03-large.png#lightbox)
3. Em seguida, vamos nos conectar nossos novos itens de menu a nossas ações existentes que criamos para nosso menu personalizado na [adicionando, editando e excluindo Menus](#Adding,_Editing_and_Deleting_Menus) seção acima. Alterne para o **Inspetor de Conexão** e selecione o **Respondente primeiro** no **hierarquia de Interface**. Role para baixo e localize o `phraseAddress:` ação. Arraste uma linha do círculo em que a ação de **endereço** item de menu:

    [![Arrastando para ligar uma ação](menu-images/dock04.png "arrastando ligar uma ação")](menu-images/dock04-large.png#lightbox)
4. Repita para todos os outros itens de menu anexá-los para suas ações correspondentes: 

    [![As ações necessárias](menu-images/dock05.png "as ações necessárias")](menu-images/dock05-large.png#lightbox)
5. Em seguida, selecione a **Application** na **hierarquia de Interface**. No **Inspetor de Conexão**, arraste uma linha de círculo o `dockMenu` tomada para o menu que acabamos de criar:

    [![Arrastando a tomada de ligar](menu-images/dock06.png "arrastando a tomada de ligar")](menu-images/dock06-large.png#lightbox)
6. Salve suas alterações e voltar para o Visual Studio para Mac sincronizar com o Xcode.
7. Clique duas vezes o **Info. plist** arquivo para abri-lo para edição: 

    [![Editar o arquivo Info.plist](menu-images/dock07.png "Editar o arquivo Info.plist")](menu-images/dock07-large.png#lightbox)
8. Clique o **origem** guia na parte inferior da tela: 

    [![Selecionar exibição da fonte](menu-images/dock08.png "selecionando a exibição da fonte")](menu-images/dock08-large.png#lightbox)
9. Clique em **adicionar nova entrada**, clique o botão de adição verde, defina o nome da propriedade para "AppleDockMenu" e o valor para "DockMenu" (o nome do nosso novo arquivo. XIB sem a extensão): 

    [![Adicionar o item DockMenu](menu-images/dock09.png "adicionando o item DockMenu")](menu-images/dock09-large.png#lightbox)

Agora se podemos executar nosso aplicativo e clique duas vezes em seu ícone no Dock, nossos novos itens de menu serão exibidos:

![Um exemplo de como o menu de encaixe em execução](menu-images/dock10.png "um exemplo de como o menu de encaixe em execução")

Se selecionarmos um dos itens personalizados no menu, o texto na nossa exibição de texto será modificado.

<a name="Pop-up_Menus_and_Pull-Down_Lists" />

## <a name="pop-up-button-and-pull-down-lists"></a>Botão de pop-up e listas suspensas

Um botão de pop-up exibe um item selecionado e apresenta uma lista de opções para selecionar a partir de quando clicado pelo usuário. Uma lista suspensa é um tipo de botão pop-up, geralmente usado para selecionar comandos específicos ao contexto da tarefa atual. Ambos podem aparecer em qualquer lugar em uma janela.

Vamos criar um botão personalizado pop-up para nosso aplicativo fazendo o seguinte:

1. Editar o **Main. Storyboard** arquivo no Xcode e arraste uma **botão pop-up** do **Inspetor de biblioteca** até o **painel** janela que criamos na o [Menus contextuais](#Contextual_Menus) seção: 

    [![Adicionando um botão de pop-up](menu-images/popup01.png "adicionando um botão de pop-up")](menu-images/popup01-large.png#lightbox)
2. Adicionar um novo item de menu e definir os títulos dos itens no pop-up para: **endereço**, **data**, **saudação**, e **assinatura** 

    [![Configurando os itens de menu](menu-images/popup02.png "Configurando os itens de menu")](menu-images/popup02-large.png#lightbox)
3. Em seguida, vamos nos conectar nossos novos itens de menu para as ações existentes que criamos para nosso menu personalizado na [adicionando, editando e excluindo Menus](#Adding,_Editing_and_Deleting_Menus) seção acima. Alterne para o **Inspetor de Conexão** e selecione o **Respondente primeiro** no **hierarquia de Interface**. Role para baixo e localize o `phraseAddress:` ação. Arraste uma linha do círculo em que a ação de **endereço** item de menu: 

    [![Arrastando para ligar uma ação](menu-images/popup03.png "arrastando ligar uma ação")](menu-images/popup03-large.png#lightbox)
4. Repita para todos os outros itens de menu anexá-los para suas ações correspondentes: 

    [![Todas as ações necessárias](menu-images/popup04.png "todas as ações necessárias")](menu-images/popup04-large.png#lightbox)
5. Salve suas alterações e voltar para o Visual Studio para Mac sincronizar com o Xcode.

Agora se podemos executar nosso aplicativo e selecionar um item em pop-up, o texto na nossa exibição de texto será alterado:

![Um exemplo de pop-up em execução](menu-images/popup05.png "um exemplo de pop-up em execução")

Você pode criar e trabalhar com listas suspensas em exatamente da mesma maneira como os botões de pop-up. Em vez de anexar a ação existente, você pode criar suas próprias ações personalizadas exatamente como fizemos para nosso menu contextual na [Menus contextuais](#Contextual_Menus) seção.

## <a name="summary"></a>Resumo

Este artigo apresentou uma visão detalhada de como trabalhar com menus e itens de menu em um aplicativo xamarin. Mac. Primeiro, examinamos barra de menus do aplicativo, em seguida, analisamos Criando menus contextuais, em seguida, examinamos os menus de barra de status e menus de encaixe personalizado. Por fim, abordamos a menus pop-up e listas suspensas.


## <a name="related-links"></a>Links relacionados

- [MacMenus (amostra)](https://developer.xamarin.com/samples/mac/MacMenus/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Diretrizes de Interface Humana - Menus](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/)
- [Introdução ao aplicativo Menus e listas pop-up](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/MenuList/MenuList.html)
