---
title: Menus no Xamarin.Mac
description: Este artigo aborda o trabalho com menus em um aplicativo Xamarin.Mac. Ele descreve a criar e manter menus e itens de menu no Xcode e o construtor de Interface e trabalhar com eles por meio de programação.
ms.prod: xamarin
ms.assetid: 5D367F8E-3A76-4995-8A89-488530FAD802
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: cb89d1df60bafe14dcc989666f0eeb5d757e4017
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792915"
---
# <a name="menus-in-xamarinmac"></a>Menus no Xamarin.Mac

_Este artigo aborda o trabalho com menus em um aplicativo Xamarin.Mac. Ele descreve a criar e manter menus e itens de menu no Xcode e o construtor de Interface e trabalhar com eles por meio de programação._

Ao trabalhar com c# e .NET em um aplicativo de Xamarin.Mac, você tem acesso aos menus Cocoa mesmo que um desenvolvedor trabalhando em Objective-C e Xcode. Porque Xamarin.Mac se integra diretamente com o Xcode, você pode usar o construtor de Interface do Xcode para criar e manter sua barras de menus, menus e itens de menu (ou, opcionalmente, criá-los diretamente no código do c#).

Menus são uma parte integrante da experiência do usuário do aplicativo do Mac e geralmente aparecem em várias partes da interface do usuário:

- **Barra de menus do aplicativo** -esse é o menu principal que aparece na parte superior da tela para cada aplicativo do Mac.
- **Menus contextuais** -elas aparecem quando o usuário clica ou um item em uma janela de cliques de controle.
- **A barra de status** -esta é a área na extrema direita da barra de menu do aplicativo que aparece na parte superior da tela (à esquerda do relógio da barra de menu) e aumenta à esquerda, como os itens são adicionados a ela.
- **Menu de encaixe** -menu para cada aplicativo no compartimento que aparece quando o usuário clica ou o ícone do aplicativo de cliques de controle, ou quando o usuário left-clicks o ícone e mantém o botão do mouse pressionado.
- **Botão pop-up e listas suspensas** -um botão pop-up exibe um item selecionado e apresenta uma lista de opções para selecionar quando clicado pelo usuário. Uma lista suspensa é um tipo de botão pop-up geralmente usado para selecionar comandos específicos para o contexto da tarefa atual. Ambos podem aparecer em qualquer lugar em uma janela.

[![Um menu de exemplo](menu-images/intro01.png "um menu de exemplo")](menu-images/intro01-large.png#lightbox)

Neste artigo, vamos abordar os fundamentos de trabalhar com itens de menu em um aplicativo Xamarin.Mac, menus e barras de menus Cocoa. É altamente recomendável que você leia o [Hello, Mac](~/mac/get-started/hello-mac.md) artigo primeiro, especificamente o [Introdução ao construtor da Interface e Xcode](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) e [tomadas e ações](~/mac/get-started/hello-mac.md#Outlets_and_Actions) seções, como ele aborda os principais conceitos e técnicas que será usado neste artigo.

Talvez você queira dar uma olhada a [classes expondo c# / métodos para Objective-C](~/mac/internals/how-it-works.md) seção o [Xamarin.Mac internos](~/mac/internals/how-it-works.md) de documento, ele explica o `Register` e `Export` atributos usado com a transmissão suas classes c# objetos Objective-C e elementos de interface do usuário.

## <a name="the-applications-menu-bar"></a>Barra de menus do aplicativo 

Diferentemente dos aplicativos em execução no sistema operacional Windows em que cada janela pode ter sua própria barra de menu anexada a ele, todos os aplicativos em execução no macOS tem uma barra de menus único que é executado na parte superior da tela que é usada para cada janela de aplicativo:

[![Uma barra de menus](menu-images/appmenu01.png "uma barra de menus")](menu-images/appmenu01-large.png#lightbox)

Itens nessa barra de menus estão ativadas ou desativadas com base no contexto atual ou o estado do aplicativo e sua interface de usuário a qualquer momento. Por exemplo: se o usuário seleciona um campo de texto, os itens na **editar** menu será fornecido habilitado como **cópia** e **Recortar**.

De acordo com a Apple e, por padrão, todos os aplicativos macOS têm um conjunto padrão de menus e itens de menu que aparecem na barra de menus do aplicativo:

- **Menu Apple** -esse menu fornece acesso ao sistema amplos itens que estão disponíveis para o usuário em todos os momentos, independentemente de qual aplicativo está em execução. Esses itens não podem ser modificados pelo desenvolvedor.
- **Menu de aplicativo** -esse menu exibe o nome do aplicativo em negrito e ajuda o usuário a identificar qual aplicativo está sendo executado. Ele contém itens que se aplicam ao aplicativo como um todo e não um determinado documento ou processo como encerrar o aplicativo.
- **Menu arquivo** - itens usados para criar, abrir ou salvar documentos que seu aplicativo funcione com. Se seu aplicativo não é baseado no documento, esse menu pode ser renomeado ou removido.
- **Menu Editar** -contém comandos como **Recortar**, **cópia**, e **colar** que são usados para editar ou modificar os elementos de interface do usuário do aplicativo.
- **Menu Formatar** - se o aplicativo funciona com texto, esse menu contém comandos para ajustar a formatação do texto.
- **Menu Exibir** -contém comandos que afetam como o conteúdo é exibido (exibido) na interface do usuário do aplicativo.
- **Menus específicos de aplicativo** -esses são todos os menus que são específicos para seu aplicativo (por exemplo, um menu de indicadores de um navegador da web). Elas devem aparecer entre o **exibição** e **janela** menus na barra.
- **Menu janela** -contém comandos para trabalhar com o windows em seu aplicativo, bem como uma lista de janelas abertas atuais.
- **Menu Ajuda** -se seu aplicativo fornece ajuda na tela, o menu de Ajuda deve ser o menu mais à direita da barra. 

Para obter mais informações sobre a barra de menus do aplicativo e menus padrão e itens de menu, consulte da Apple [diretrizes de Interface Humana](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/).

### <a name="the-default-application-menu-bar"></a>A barra de menus do aplicativo padrão

Sempre que você criar um novo projeto de Xamarin.Mac, você obtém automaticamente uma barra de menu de aplicativo padrão padrão, que contém os itens comuns que um aplicativo macOS teria normalmente (como discutido na seção acima). Barra de menus padrão do seu aplicativo é definida no **Main.storyboard** arquivo (junto com o restante da interface de usuário do aplicativo) no projeto no **solução preenchimento**:  

![Selecione o storyboard principal](menu-images/appmenu02.png "selecionar o storyboard principal")

Clique duas vezes o **Main.storyboard** arquivo para abri-lo para edição no construtor de Interface do Xcode e você verá a interface do editor de menu:

[![Editando a interface do usuário no Xcode](menu-images/defaultbar01.png "editando a interface do usuário no Xcode")](menu-images/defaultbar01-large.png#lightbox)

Aqui, pode clicar em itens, como o **abrir** item de menu no **arquivo** menu e editar ou ajustar suas propriedades no **atributos Inspetor**:

[![Edição de atributos do menu](menu-images/defaultbar02.png "editando os atributos do menu")](menu-images/defaultbar02-large.png#lightbox)

Vamos adicionar, editar e excluir menus e itens neste artigo. De agora estamos apenas deseja ver quais itens de menu e menus estão disponíveis por padrão e como eles foram automaticamente expostos para o código por meio de um conjunto de lojas predefinidas e ações (para obter mais informações, consulte nosso [tomadas e ações](~/mac/get-started/hello-mac.md#Outlets_and_Actions) documentação).

Por exemplo, se clicarmos no **Conexão Inspetor** para o **abrir** item de menu, podemos ver é automaticamente conectado até o `openDocument:` ação: 

[![Exibindo a ação anexada](menu-images/defaultbar03.png "exibindo a ação anexada")](menu-images/defaultbar03-large.png#lightbox)

Se você selecionar o **resposta** no **hierarquia Interface** e role para baixo no **Inspetor de Conexão**, e você verá a definição do `openDocument:` ação que o **abrir** item de menu está associado (junto com várias outras ações padrão para o aplicativo e não são conectadas automaticamente controles):

[![Exibindo anexadas todas as ações](menu-images/defaultbar04.png "exibindo todas as ações anexadas")](menu-images/defaultbar04-large.png#lightbox) 

Por que isso é importante? Na próxima seção verá como essas ações definidas automaticamente funcionam com outros elementos de interface do usuário Cocoa automaticamente habilitar e desabilitar itens de menu, bem como, fornece a funcionalidade interna para os itens.

Mais tarde usaremos essas ações internas para habilitar e desabilitar itens de código e fornecer nossa própria funcionalidade quando eles estiverem selecionados.

<a name="Built-In_Menu_Functionality" />

### <a name="built-in-menu-functionality"></a>Funcionalidade de menu interno

Se você executar um aplicativo Xamarin.Mac recém-criado antes de adicionar os itens de interface do usuário ou o código, você pode notar que alguns itens são automaticamente com fio-up e habilitados para você (com total funcionalidade interna automaticamente), como o **Quit** item o **aplicativo** menu:

![Um item de menu habilitado](menu-images/appmenu03.png "um item de menu habilitado")

Embora outros itens, como **Recortar**, **cópia**, e **colar** não são:

![Desabilitado itens de menu](menu-images/appmenu04.png "desabilitado itens de menu")

Vamos parar o aplicativo e clique duas vezes o **Main.storyboard** de arquivos no **preenchimento de solução** para abri-lo para edição no Xcode do construtor de Interface. Em seguida, arraste um **exibição de texto** do **biblioteca** no controlador de exibição da janela no **Editor de Interface**:

[![Selecionar um modo de exibição do texto da biblioteca](menu-images/appmenu05.png "selecionando uma exibição de texto da biblioteca")](menu-images/appmenu05-large.png#lightbox)

No **Editor de restrição de** vamos fixar a exibição de texto para bordas da janela e defini-lo onde ele aumenta e diminui com a janela clicando em todos os quatro vermelhos I-emissões de na parte superior do editor e o **adicionar restrições 4** botão:

[![As restrições de edição](menu-images/appmenu06.png "as restrições de edição")](menu-images/appmenu06-large.png#lightbox)

Salvar as alterações para o design de interface do usuário e retornar o Visual Studio para Mac para sincronizar as alterações com o seu projeto Xamarin.Mac. Iniciar o aplicativo, digite algum texto no modo de exibição de texto, selecione-o e abrir agora o **editar** menu:

![Os itens de menu são automaticamente ativado/desativado](menu-images/appmenu07.png "os itens de menu são automaticamente ativado/desativado")

Observe como o **Recortar**, **cópia**, e **colar** itens são automaticamente habilitados e completamente funcionais, tudo sem escrever uma única linha de código. 

O que está acontecendo aqui? Lembre-se internos predefinir ações que vêm com fio até os itens de menu padrão (conforme apresentado acima), a maioria dos elementos de interface de usuário da Cocoa que fazem parte do macOS tem internos ganchos para ações específicas (como `copy:`). Portanto quando são adicionados a uma janela ativa e selecionados, o item de menu correspondente ou itens associados a essa ação são habilitados automaticamente. Se o usuário selecionar esse item de menu, a funcionalidade criada no elemento de interface do usuário é chamada e executada, tudo sem a intervenção do desenvolvedor.

### <a name="enabling-and-disabling-menus-and-items"></a>Habilitando e desabilitando menus e itens

Por padrão, toda vez que ocorre um evento de usuário, `NSMenu` automaticamente habilita e desabilita a cada menu visível e o menu de item com base no contexto do aplicativo. Há três maneiras de habilitar/desabilitar um item:

- **Habilitar menu automático** -um item de menu estará habilitado se `NSMenu` pode encontrar um objeto apropriado que responde a ação que o item está com fio-até. Por exemplo, a exibição de texto acima que tinha um gancho interno para o `copy:` ação.
- **Ações personalizadas e validateMenuItem:** - para qualquer item de menu que está associado a um [janela ou a exibição de ação personalizada de controlador](#Working-with-Custom-Window-Actions), você pode adicionar o `validateMenuItem:` ação e habilitar ou desabilitar manualmente itens de menu.
- **Habilitar menu manual** -definir manualmente a `Enabled` propriedade de cada `NSMenuItem` para habilitar ou desabilitar a cada item em um menu individualmente.

Para escolher um sistema, definir o `AutoEnablesItems` propriedade de um `NSMenu`. `true` é automático (o comportamento padrão) e `false` é manual. 

> [!IMPORTANT]
> Se você optar por usar a habilitação do menu manual, nenhum menu itens, mesmo aqueles controlados por classes AppKit como `NSTextView`, são atualizadas automaticamente. Você será responsável por habilitar e desabilitar todos os itens manualmente no código.

#### <a name="using-validatemenuitem"></a>Usando validateMenuItem

Como mencionado anteriormente, para qualquer item de menu está associado a um [janela ou exibição controlador personalizado ação](#Working-with-Custom-Window-Actions), você pode adicionar o `validateMenuItem:` ação e habilitar ou desabilitar manualmente itens de menu.

No exemplo a seguir, o `Tag` propriedade será usada para decidir o tipo de item de menu que será ser habilitado/desabilitado pelo `validateMenuItem:` ação com base no estado do texto selecionado em um `NSTextView`. O `Tag` propriedade foi definida no construtor de Interface para cada item de menu:

![Definindo a propriedade de marca](menu-images/validate01.png "definindo a propriedade de marca")

E o código a seguir adicionado ao controlador de exibição:

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

Quando esse código é executado, e nenhum texto selecionado no `NSTextView`, os itens de menu dois wrap estão desabilitados (mesmo que eles são programados para ações no controlador de exibição):

![Mostrando desabilitado itens](menu-images/validate02.png "mostrando desabilitado itens")

Se uma seção de texto for selecionada e reabri-lo no menu, os itens de menu duas wrap estarão disponíveis:

![Mostrando habilitado itens](menu-images/validate03.png "mostrando habilitado itens")

## <a name="enabling-and-responding-to-menu-items-in-code"></a>Habilitando e responder aos itens de menu no código

Como vimos acima, apenas adicionando elementos de interface de usuário Cocoa específicos ao nosso design de interface do usuário (como um campo de texto), vários itens de menu padrão serão habilitados e funcionam automaticamente, sem precisar gravar qualquer código. Avançar Vejamos adicionando nosso próprio código c# para nosso projeto Xamarin.Mac para habilitar um item de menu e fornecer funcionalidade quando o usuário o seleciona.

Por exemplo, permitem que desejamos que o usuário seja capaz de usar o **abrir** item o **arquivo** menu para selecionar uma pasta. Como esta é uma função de nível de aplicativo e o não está limitado a uma janela de conceder ou elemento de interface do usuário, vamos adicionar o código para lidar com isso em nosso representante do aplicativo.

No **solução preenchimento**, clique duas vezes o `AppDelegate.CS` arquivo para abri-lo para edição:

![Selecionando o representante do aplicativo](menu-images/appmenu08.png "selecionando o representante do aplicativo")

Adicione o seguinte código abaixo o `DidFinishLaunching` método:

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

Observe que o **abrir** item de menu é ativado. Se selecionamos, será exibida a caixa de diálogo aberta:

![Uma caixa de diálogo Abrir](menu-images/appmenu10.png "uma caixa de diálogo Abrir")

Se clicarmos o **abrir** botão, nossa mensagem de alerta será exibida:

![Um exemplo de mensagem de caixa de diálogo](menu-images/appmenu11.png "uma mensagem de caixa de diálogo de exemplo")

A linha de chave aqui foi `[Export ("openDocument:")]`, informa `NSMenu` que nosso **AppDelegate** tem um método `void OpenDialog (NSObject sender)` que responde ao `openDocument:` ação. Se você vai se lembrar de acima, o **abrir** item de menu é automaticamente com fio-up para essa ação por padrão no construtor de Interface:

[![Exibindo as ações anexadas](menu-images/defaultbar03.png "exibindo as ações anexadas")](menu-images/defaultbar03-large.png#lightbox)

Avançar vamos examinar nosso próprio menu, itens de menu e ações de criação e responder a eles no código.

### <a name="working-with-the-open-recent-menu"></a>Trabalhando com o menu Abrir recente

Por padrão, o **arquivo** menu contém um **abrir recente** item que controla os último vários arquivos que o usuário abriu com seu aplicativo. Se você estiver criando um `NSDocument` com base em aplicativo Xamarin.Mac, esse menu será manipulado para você automaticamente. Para qualquer outro tipo de aplicativo Xamarin.Mac, você será responsável por gerenciar e responder a este item de menu manualmente.

Controlar manualmente o **abrir recente** menu, você primeiro precisará informá-lo de que um novo arquivo foi aberto ou salvo usando o seguinte:

```csharp
// Add document to the Open Recent menu
NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);
```

Mesmo que seu aplicativo não estiver usando `NSDocuments`, ainda é usar o `NSDocumentController` para manter o **abrir recente** menu enviando um `NSUrl` com o local do arquivo para o `NoteNewRecentDocumentURL` método do `SharedDocumentController`.

Em seguida, você precisa substituir o `OpenFile` método do representante de aplicativo para abrir qualquer arquivo que o usuário seleciona o **abrir recente** menu. Por exemplo:

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

Retornar `true` se o arquivo pode ser aberto, ou retornar `false` e será exibido um aviso interno para o usuário que o arquivo não pôde ser aberto.

Porque o nome de arquivo e o caminho retornado do **abrir recente** menu pode incluir um espaço, precisamos esse caractere de escape corretamente antes de criar um `NSUrl` ou, obterá um erro. Podemos fazer isso com o código a seguir:

```csharp
filename = filename.Replace (" ", "%20");
```

Por fim, podemos criar um `NSUrl` que aponta para o arquivo e use delegar um método auxiliar no aplicativo para abrir uma nova janela e carregar o arquivo nele:

```csharp
var url = new NSUrl ("file://"+filename);
return OpenFile(url);
```

Para reunir tudo, vamos dar uma olhada em um exemplo de implementação em um **appdelegate. CS** arquivo:

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

Com base nos requisitos de seu aplicativo, talvez não seja o usuário abrir o mesmo arquivo em mais de uma janela ao mesmo tempo. Em nosso aplicativo de exemplo, se o usuário escolhe um arquivo que já está aberto (da **abrir recente** ou **abrir...** itens de menu), a janela que contém o arquivo é colocada na frente.

Para fazer isso, usamos o seguinte código no nosso método auxiliar:

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

Criamos o nosso `ViewController` classe para conter o caminho para o arquivo no seu `Path` propriedade. Em seguida, faremos um loop através de todas as janelas abertas no momento no aplicativo. Se o arquivo já está aberto em uma das janelas, ele é colocado na frente de todas as outras janelas usando:

```csharp
NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
```

Se nenhuma correspondência for encontrada, uma nova janela é aberta com o arquivo carregado e o arquivo está disponível a **abrir recente** menu:

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

### <a name="working-with-custom-window-actions"></a>Trabalhando com ações de janela personalizados

Assim como o interno **resposta** ações que são itens de menu padrão, você pode criar novas ações personalizadas e conectá-los aos itens de menu no construtor de Interface.

Primeiro, defina uma ação personalizada em um dos controladores de janela do aplicativo. Por exemplo:

```csharp
[Action("defineKeyword:")]
public void defineKeyword (NSObject sender) {
    // Preform some action when the menu is selected
    Console.WriteLine ("Request to define keyword");
}
```

Em seguida, clique duas vezes no arquivo de storyboard do aplicativo no **solução preenchimento** para abri-lo para edição no Xcode do construtor de Interface. Selecione o **Respondente primeiro** sob o **cena do aplicativo**, alterne para o **Inspetor de atributos**:

![O Inspetor de atributos](menu-images/action01.png "Inspetor de atributos")

Clique o **+** botão na parte inferior da **atributos Inspetor** para adicionar uma nova ação personalizada:

![Adicionando uma nova ação](menu-images/action02.png "adicionando uma nova ação")

Dê a ele o mesmo nome que a ação personalizada que você criou em seu controlador de janela:

![O nome da ação de edição](menu-images/action03.png "editando o nome da ação")

Control, clique e arraste a partir de um item de menu para o **resposta** sob o **aplicativo cena**. Na lista pop-up, selecione a nova ação que você acabou de criar (`defineKeyword:` neste exemplo):

![Anexando uma ação](menu-images/action04.png "anexando uma ação")

Salvar as alterações para o storyboard e retornar ao Visual Studio para Mac sincronizar as alterações. Se você executar o aplicativo, o item de menu ao qual você se conectou a ação personalizada será automaticamente ser habilitado/desabilitado (com base na janela com a ação que está sendo aberto) e selecionando o item de menu disparará a ação:

[![Nova ação de teste](menu-images/action05.png "nova ação de teste")](menu-images/action05-large.png#lightbox)

<a name="Adding,_Editing_and_Deleting_Menus" />

### <a name="adding-editing-and-deleting-menus"></a>Adição, edição e exclusão de menus

Como vimos nas seções anteriores, um aplicativo Xamarin.Mac vem com um número predefinido de menus padrão e itens de menu que os controles de interface do usuário específicos automaticamente ativará e responder a. Também vimos como adicionar código ao nosso aplicativo que também habilitará e responder a esses itens padrão.

Nesta seção examinaremos removendo itens de menu que não é necessário, a reorganização de menus e adicionando novos menus, itens de menu e ações.

Clique duas vezes o **Main.storyboard** do arquivo no **solução preenchimento** para abri-lo para edição:

[![Editando a interface do usuário no Xcode](menu-images/maint01.png "editando a interface do usuário no Xcode")](menu-images/maint01-large.png#lightbox)

Para nosso aplicativo Xamarin.Mac específico não vamos usar o padrão **exibição** menu portanto vamos removê-lo. No **hierarquia Interface** selecionar o **exibição** item de menu que faz parte da barra de menu principal:

![Selecionando o item de menu de exibição](menu-images/maint02.png "selecionando o item de menu de exibição")

Pressione a tecla delete ou backspace para excluir no menu. Em seguida, não vamos usar todos os itens a **formato** menu e deseja mover os itens, vamos usar-os submenus. No **hierarquia Interface** selecionar os itens de menu a seguir:

![Realce de vários itens](menu-images/maint03.png "realce vários itens")

Arraste os itens sob o pai **Menu** do submenu, onde eles estão atualmente:

[![Arrastando os itens de menu ao menu pai](menu-images/maint04.png "arrastando os itens de menu ao menu pai")](menu-images/maint04-large.png#lightbox)

O menu agora deve parecer com:

[![Os itens no novo local](menu-images/maint05.png "os itens no novo local")](menu-images/maint05-large.png#lightbox)

Avançar vamos arrastar o **texto** submenu out no **formato** menu e colocá-lo na barra de menus principal entre o **formato** e **janela** menus:

[![O menu texto](menu-images/maint06.png "menu o texto")](menu-images/maint06-large.png#lightbox)

Vamos sob o **formato** menu e exclua o **fonte** item de submenu. Em seguida, selecione o **formato** menu e renomeie-a como "Fonte":

[![Menu fonte](menu-images/maint07.png "menu a fonte")](menu-images/maint07-large.png#lightbox)

Em seguida, vamos criar um menu personalizado de frases predefine automaticamente obter acrescentado ao texto no modo de exibição de texto quando eles estiverem selecionados. Na caixa de pesquisa na parte inferior no **biblioteca Inspetor** tipo no menu"." Isso tornará mais fácil de localizar e trabalhar com todos os elementos de interface do usuário do menu:

![O Inspetor de biblioteca](menu-images/maint08.png "Inspetor de biblioteca")

Agora vamos fazer o seguinte para criar nossa menu:

1. Arraste um **Item de Menu** do **biblioteca Inspetor** para a barra de menu entre o **texto** e **janela** menus: 

    ![Selecionando um novo item de menu na biblioteca](menu-images/maint10.png "selecionando um novo item de menu na biblioteca")
2. Renomear o item "Frases": 

    [![Configurar o nome do menu](menu-images/maint09.png "definindo o nome do menu")](menu-images/maint09-large.png#lightbox)
3. Em seguida arraste uma **Menu** do **Inspetor de biblioteca**: 

    ![Selecionar um menu da biblioteca](menu-images/maint11.png "selecionando um menu da biblioteca")
4. Em seguida, solte **Menu** no novo **Item de Menu** acabou de criar e alterar seu nome para "Frases": 

    [![Editar o nome do menu](menu-images/maint12.png "editando o nome do menu")](menu-images/maint12-large.png#lightbox)
5. Agora vamos renomear o padrão de três **itens de Menu** "Address", "Data" e "Saudação": 

    [![O menu de frases](menu-images/maint13.png "menu frases o")](menu-images/maint13-large.png#lightbox)
6. Vamos adicionar uma quarta **Item de Menu** arrastando um **Item de Menu** do **biblioteca Inspetor** e chamando-a "Assinatura": 

    [![Editar o nome do item de menu](menu-images/maint14.png "editando o nome do item de menu")](menu-images/maint14-large.png#lightbox)
7. Salve as alterações para a barra de menus.

Agora vamos criar um conjunto de ações personalizadas para que os novos itens de menu são expostos a código c#. No Xcode vamos alternar para o **assistente** exibição:

[![Criando as ações necessárias](menu-images/maint15.png "criando as ações necessárias")](menu-images/maint15-large.png#lightbox)

Vamos fazer o seguinte:

1. Arraste com controle do **endereço** item de menu para o **appdelegate. H** arquivo.
2. Opção de **Conexão** tipo **ação**: 

    [![Selecionar o tipo de ação](menu-images/maint17.png "selecionando o tipo de ação")](menu-images/maint17-large.png#lightbox)
3. Insira um **nome** de "phraseAddress" e pressione a **conectar** botão para criar a nova ação: 

    [![Configurar a ação de](menu-images/maint18.png "Configurando a ação")](menu-images/maint18-large.png#lightbox)
4. Repita as etapas acima para o **data**, **saudação**, e **assinatura** itens de menu: 

    [![As ações concluídas](menu-images/maint19.png "as ações concluídas")](menu-images/maint19-large.png#lightbox)
5. Salve as alterações para a barra de menus.

Em seguida, precisamos criar uma tomada para a visualização de texto para que podemos ajustar seu conteúdo do código. Selecione o **ViewController.h** arquivo o **Assistente de Editor** e criar uma nova loja chamada `documentText`:

[![Criando uma tomada](menu-images/maint20.png "criando uma tomada")](menu-images/maint20-large.png#lightbox)

Retorne ao Visual Studio para Mac sincronizar as alterações do Xcode. Em seguida edite o **ViewController.cs** de arquivo e torná-lo a aparência a seguir:

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

Isso expõe o texto da nossa visualização de texto fora do `ViewController` classe e informa o representante do aplicativo quando a janela recebe ou perde o foco. Agora, edite o **appdelegate. CS** de arquivo e torná-lo a aparência a seguir:

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

Aqui, fizemos a `AppDelegate` parciais de uma classe para que podemos usar as ações e as saídas que definimos no construtor de Interface. Podemos também expor um `textEditor` para controlar qual janela está atualmente em foco.

Os métodos a seguir são usados para lidar com nosso menu personalizados e itens de menu:

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

Agora, se executarmos nosso aplicativo, todos os itens no **frase** menu estará ativo e adicionará a frase dê à exibição de texto quando selecionado:

![Um exemplo de como o aplicativo em execução](menu-images/maint21.png "um exemplo de como o aplicativo em execução")

Agora que temos os fundamentos de trabalhar com a barra de menu do aplicativo para baixo, vamos criar um menu contextual personalizado.

### <a name="creating-menus-from-code"></a>Criando menus de código

Além de criar itens de menu e menus com o construtor de Interface do Xcode, pode haver vezes quando um aplicativo Xamarin.Mac precisa criar, modificar ou remover um menu, submenu ou item de menu do código.

No exemplo a seguir, uma classe é criada para armazenar as informações sobre os itens de menu e submenus serão dinamicamente criados durante a execução:

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

Com essa classe é definida, a rotina a seguir analisa uma coleção de `LanguageFormatCommand`objetos e recursivamente criar novos menus e itens de menu, anexando-los para a parte inferior do menu existente (criado no construtor de Interface) que foi passada em:

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

Para qualquer `LanguageFormatCommand` objeto que tem um espaço em branco `Title` Esta rotina de propriedade, cria um **item de menu do separador** (uma linha fina cinza) entre as seções de menu:

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

Para qualquer novo item de menu que não tenha submenus, o código é adicionado para manipular o item de menu selecionado pelo usuário:

```csharp
menuItem.Activated += (sender, e) => {
    // Do something when the menu item is selected
    ...
};
```

#### <a name="testing-the-menu-creation"></a>Testando a criação de menu

Com todo o código acima em vigor, se o seguinte conjunto de `LanguageFormatCommand` objetos foram criados:

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

E que coleta passado para o `AssembleMenu` função (com o **formato** Menu definir como a base), os seguintes menus dinâmicos e itens de menu pode ser criados:

![Os novos itens de menu no aplicativo em execução](menu-images/dynamic01.png "os novos itens de menu no aplicativo em execução")

#### <a name="removing-menus-and-items"></a>Removendo de menus e itens

Se você precisar remover qualquer item de menu ou menu de interface do usuário do aplicativo, você pode usar o `RemoveItemAt` método o `NSMenu` classe simplesmente, dando a ele o zero com base em índice do item a ser removido.

Por exemplo, para remover os menus e itens de menu criados pela rotina acima, você pode usar o código a seguir:

```csharp
public void UnpopulateFormattingMenu(NSMenu menu) {

    // Remove any additional items
    for (int n = (int)menu.Count - 1; n > 4; --n) {
        menu.RemoveItemAt (n);
    }
}
```

No caso do código acima, os primeiros itens de quatro menu são criados do Xcode construtor de Interface e materiais disponíveis no aplicativo, para que eles não são removidos dinamicamente.

<a name="Contextual_Menus" />

## <a name="contextual-menus"></a>Menus contextuais

Menus contextuais aparecem quando o usuário clica ou um item em uma janela de cliques de controle. Por padrão, vários dos elementos da interface do usuário incorporados macOS já têm menus contextuais anexadas a elas (como o modo de exibição de texto). No entanto, pode haver vezes quando quer criar nossos própria menus contextuais personalizados para um elemento de interface do usuário que foram adicionadas a uma janela.

Vamos Editar nossos **Main.storyboard** no Xcode e adicionar um **janela** janela para nosso projeto, defina seu **classe** para "NSPanel" no **Inspetor de identidade**, adicione um novo **assistente** item para o **janela** menu e anexá-lo para a nova janela usando um **Mostrar atender**:

[![Definição do tipo segue](menu-images/context01.png "definição do tipo segue")](menu-images/context01-large.png#lightbox)

Vamos fazer o seguinte:

1. Arraste um **rótulo** do **Inspetor de biblioteca** até o **painel** janela e defina o texto para "Property": 

    [![Editar o valor do rótulo](menu-images/context03.png "editar o valor do rótulo")](menu-images/context03-large.png#lightbox)
2. Em seguida arraste uma **Menu** do **biblioteca Inspetor** no controlador de exibição na hierarquia do modo de exibição e itens de menu padrão renomear os três **documento**, **texto**  e **fonte**:

    [![Os itens de menu necessário](menu-images/context02.png "os itens de menu necessária")](menu-images/context02-large.png#lightbox)
3. Agora arraste de controle do **rótulo de propriedade** até o **Menu**:

    [![Arrastar para criar um segue](menu-images/context04.png "arrastar para criar um segue")](menu-images/context04-large.png#lightbox)
4. Na caixa de diálogo pop-up, selecione **Menu**: 

    ![Definição do tipo segue](menu-images/context05.png "definição do tipo segue")
5. Do **Inspetor de identidade**, definir a classe de exibição do controlador para "PanelViewController": 

    [![Definindo a classe segue](menu-images/context10.png "definindo a classe segue")](menu-images/context10-large.png#lightbox)
6. Retorne ao Visual Studio para Mac sincronizar e retorne ao construtor da Interface.
7. Alterne para o **Assistente Editor** e selecione o **PanelViewController.h** arquivo.
8. Criar uma ação para o **documento** item de menu chamado `propertyDocument`: 

    [![Configurar a ação de](menu-images/context06.png "Configurando a ação")](menu-images/context06-large.png#lightbox)
9. Repita as ações de criação para os demais itens de menu: 

    [![As ações necessárias](menu-images/context07.png "as ações necessárias")](menu-images/context07-large.png#lightbox)
10. Finalmente, crie uma tomada para o **rótulo de propriedade** chamado `propertyLabel`: 

    [![Configurando a tomada de](menu-images/context08.png "Configurando a tomada")](menu-images/context08-large.png#lightbox)
11. Salve suas alterações e retornar ao Visual Studio para Mac sincronizar com o Xcode.

Editar o **PanelViewController.cs** de arquivo e adicione o seguinte código:

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

Agora se executar o aplicativo e clique no rótulo da propriedade no painel, veremos nosso menu contextual personalizado. Se selecionar e de item de menu, o valor do rótulo será alterado:

![O menu contextual executando](menu-images/context09.png "menu contextual em execução")

Avançar Vejamos Criando menus de barra de status.

## <a name="status-bar-menus"></a>Menus de barra de status

Menus de barra de status exibem uma coleção de itens de menu de status que fornecem a interação com ou comentários para o usuário, como um menu ou uma imagem refletir o estado do aplicativo. Menu da barra de status do aplicativo está habilitado e ativo, mesmo que o aplicativo é executado em segundo plano. A barra de status de todo o sistema reside no lado direito da barra de menu do aplicativo e é a barra de Status só está disponível no macOS.

Vamos Editar nossos **appdelegate. CS** de arquivo e verifique o `DidFinishLaunching` método aparência semelhante ao seguinte:

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

`NSStatusBar statusBar = NSStatusBar.SystemStatusBar;` nos dá acesso à barra de status de todo o sistema. `var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);` cria um novo item de barra de status. A partir daí, criar um menu e um número de itens de menu e anexe o menu para o item da barra de status que acabamos de criar. 

Se o aplicativo é executado, o novo item de barra de status será exibido. Selecionando um item do menu para alterar o texto no modo de exibição de texto: 

![No menu da barra de status em execução](menu-images/statusbar01.png "no menu da barra de status em execução")

Em seguida, vamos criar itens de menu de encaixe personalizado.

## <a name="custom-dock-menus"></a>Menus de encaixe personalizado

O menu de encaixe será exibida para que o aplicativo Mac quando o usuário clica ou ícone do aplicativo no compartimento de cliques de controle:

![Menu de encaixe um personalizado](menu-images/dock01.png "um personalizado menu de encaixe")

Vamos criar um menu de encaixe personalizado para o nosso aplicativo, faça o seguinte:

1. No Visual Studio para Mac, clique com botão direito no projeto do aplicativo e selecione **adicionar** > **novo arquivo...** Na caixa de diálogo Novo arquivo, selecione **Xamarin.Mac** > **definição de Interface vazia**, use "DockMenu" para o **nome** e clique no **novo**  botão para criar o novo **DockMenu.xib** arquivo:

    ![Adicionar uma definição de interface vazia](menu-images/dock02.png "adicionando uma definição de interface vazia")
2. No **solução preenchimento**, clique duas vezes o **DockMenu.xib** arquivo para abri-lo para edição no Xcode. Criar um novo **Menu** com os seguintes itens: **endereço**, **data**, **saudação**, e **assinatura** 

    [![Dispor a interface do usuário](menu-images/dock03.png "dispor a interface do usuário")](menu-images/dock03-large.png#lightbox)
3. Em seguida, vamos conectar novos itens de menu para nosso ações existentes que criamos para nosso menu personalizado no [adicionando, editando e excluindo Menus](#Adding,_Editing_and_Deleting_Menus) seção acima. Alternar para o **Inspetor de Conexão** e selecione o **resposta** no **hierarquia Interface**. Role para baixo e localize o `phraseAddress:` ação. Arraste uma linha do círculo, em que a ação de **endereço** item de menu:

    [![Arrastando para ligar uma ação](menu-images/dock04.png "arrastando para ligar uma ação")](menu-images/dock04-large.png#lightbox)
4. Repita para todos os outros itens de menu anexá-los aos seus ações correspondentes: 

    [![As ações necessárias](menu-images/dock05.png "as ações necessárias")](menu-images/dock05-large.png#lightbox)
5. Em seguida, selecione o **aplicativo** no **hierarquia Interface**. No **Inspetor de Conexão**, arraste uma linha de círculo a `dockMenu` tomada no menu que acabamos de criar:

    [![Arrastando a tomada de ligar](menu-images/dock06.png "arrastando a tomada de ligar")](menu-images/dock06-large.png#lightbox)
6. Salve suas alterações e retorne ao Visual Studio para Mac sincronizar com o Xcode.
7. Clique duas vezes o **Info. plist** arquivo para abri-lo para edição: 

    [![Editar o arquivo Info.plist](menu-images/dock07.png "Editar o arquivo Info.plist")](menu-images/dock07-large.png#lightbox)
8. Clique o **fonte** guia na parte inferior da tela: 

    [![Selecionar a exibição da fonte](menu-images/dock08.png "selecionando a exibição da fonte")](menu-images/dock08-large.png#lightbox)
9. Clique em **adicionar nova entrada**, clique o botão mais verde, defina o nome da propriedade "AppleDockMenu" e o valor para "DockMenu" (o nome do nosso novo arquivo .xib sem a extensão): 

    [![Adicionar o item DockMenu](menu-images/dock09.png "adicionar o item DockMenu")](menu-images/dock09-large.png#lightbox)

Agora, se executar o aplicativo e clique no ícone de encaixe, os novos itens de menu serão exibidos:

![Um exemplo do menu de encaixe executando](menu-images/dock10.png "um exemplo do menu de encaixe em execução")

Se selecionamos um dos itens personalizados no menu, o texto no modo de exibição nosso texto será modificado.

<a name="Pop-up_Menus_and_Pull-Down_Lists" />

## <a name="pop-up-button-and-pull-down-lists"></a>Botão pop-up e listas suspensas

Um botão de pop-up exibe um item selecionado e apresenta uma lista de opções para selecionar quando clicado pelo usuário. Uma lista suspensa é um tipo de botão pop-up geralmente usado para selecionar comandos específicos para o contexto da tarefa atual. Ambos podem aparecer em qualquer lugar em uma janela.

Vamos criar um botão de pop-up personalizado para o nosso aplicativo, faça o seguinte:

1. Editar o **Main.storyboard** arquivo no Xcode e arraste uma **botão pop-up** do **Inspetor de biblioteca** até o **painel** janela criada na o [Menus contextuais](#Contextual_Menus) seção: 

    [![Adicionar um botão de pop-up](menu-images/popup01.png "adicionando um botão de pop-up")](menu-images/popup01-large.png#lightbox)
2. Adicionar um novo item de menu e definir os títulos dos itens no pop-up para: **endereço**, **data**, **saudação**, e **assinatura** 

    [![Configurando os itens de menu](menu-images/popup02.png "Configurando os itens de menu")](menu-images/popup02-large.png#lightbox)
3. Em seguida, vamos conectar novos itens de menu para as ações existentes que criamos para nosso menu personalizado no [adicionando, editando e excluindo Menus](#Adding,_Editing_and_Deleting_Menus) seção acima. Alternar para o **Inspetor de Conexão** e selecione o **resposta** no **hierarquia Interface**. Role para baixo e localize o `phraseAddress:` ação. Arraste uma linha do círculo, em que a ação de **endereço** item de menu: 

    [![Arrastando para ligar uma ação](menu-images/popup03.png "arrastando para ligar uma ação")](menu-images/popup03-large.png#lightbox)
4. Repita para todos os outros itens de menu anexá-los aos seus ações correspondentes: 

    [![Todas as ações necessárias](menu-images/popup04.png "todas as ações necessárias")](menu-images/popup04-large.png#lightbox)
5. Salve suas alterações e retorne ao Visual Studio para Mac sincronizar com o Xcode.

Agora se executar o nosso aplicativo e selecione um item de pop-up, o texto no modo de exibição nosso texto será alterado:

![Um exemplo de pop-up do executando](menu-images/popup05.png "um exemplo de pop-up em execução")

Você pode criar e trabalhar com listas suspensas no exatamente da mesma maneira como os botões de pop-up. Em vez de anexar a ação existente, você pode criar suas próprias ações personalizadas como nosso menu contextual do [Menus contextuais](#Contextual_Menus) seção.

## <a name="summary"></a>Resumo

Este artigo obteve uma visão detalhada de como trabalhar com itens de menu em um aplicativo Xamarin.Mac e menus. Primeiro examinamos barra de menus do aplicativo, analisamos Criando menus contextuais, em seguida examinamos os menus de barra de status e personalizada encaixar menus. Por fim, abordamos menus pop-up e listas suspensas.


## <a name="related-links"></a>Links relacionados

- [MacMenus (exemplo)](https://developer.xamarin.com/samples/mac/MacMenus/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Diretrizes de Interface Humana - Menus](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/)
- [Introdução ao aplicativo Menus e listas pop-up](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/MenuList/MenuList.html)
