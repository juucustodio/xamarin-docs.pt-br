---
title: Windows no Xamarin. Mac
description: Este artigo aborda como trabalhar com janelas e painéis em um aplicativo Xamarin. Mac. Ele descreve a criação de janelas e painéis no Xcode e Interface Builder, carregando-os de storyboards e arquivos. xib e trabalhando com eles de forma programática.
ms.prod: xamarin
ms.assetid: 4F6C67E9-BBFF-44F7-B29E-AB47D7F44287
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 2cacbf5640da60a7d084bf4ab113ef62fad097d8
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436391"
---
# <a name="windows-in-xamarinmac"></a>Windows no Xamarin. Mac

_Este artigo aborda como trabalhar com janelas e painéis em um aplicativo Xamarin. Mac. Ele descreve a criação de janelas e painéis no Xcode e Interface Builder, carregando-os de storyboards e arquivos. xib e trabalhando com eles de forma programática._

Ao trabalhar com C# e .NET em um aplicativo Xamarin. Mac, você tem acesso às mesmas janelas e painéis que um desenvolvedor trabalhando no *Objective-C* e no *Xcode* . Como o Xamarin. Mac se integra diretamente com o Xcode, você pode usar o _interface Builder_ do Xcode para criar e manter suas janelas e painéis (ou, opcionalmente, criá-los diretamente no código C#).

Com base em sua finalidade, um aplicativo Xamarin. Mac pode apresentar uma ou mais janelas na tela para gerenciar e coordenar as informações que ele exibe e trabalha com. As principais funções de uma janela são:

1. Para fornecer uma área na qual modos de exibição e controles podem ser colocados e gerenciados.
2. Para aceitar e responder a eventos em resposta à interação do usuário com o teclado e o mouse.

O Windows pode ser usado em um estado sem janela restrita (como um editor de texto que pode ter vários documentos abertos ao mesmo tempo) ou modal (como uma caixa de diálogo de exportação que deve ser ignorada antes que o aplicativo possa continuar).

Os painéis são um tipo especial de janela (uma subclasse da classe base `NSWindow` ), que normalmente atendem a uma função auxiliar em um aplicativo, como janelas do utilitário, como inspetores de formato de texto e seletor de cores do sistema.

[![Editando uma janela no Xcode](window-images/intro01.png)](window-images/intro01.png#lightbox)

Neste artigo, abordaremos as noções básicas de como trabalhar com janelas e painéis em um aplicativo Xamarin. Mac. É altamente recomendável que você trabalhe pelo artigo [Hello, Mac](~/mac/get-started/hello-mac.md) primeiro, especificamente a [introdução às seções Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [ações](~/mac/get-started/hello-mac.md#outlets-and-actions) , pois ela aborda os principais conceitos e técnicas que usaremos neste artigo.

Talvez você queira dar uma olhada na seção [expondo classes/métodos C# para Objective-C](~/mac/internals/how-it-works.md) do documento [interno do Xamarin. Mac](~/mac/internals/how-it-works.md) também, explica os `Register` `Export` comandos e usados para conectar suas classes C# a objetos Objective-C e elementos de interface do usuário.

## <a name="introduction-to-windows"></a>Introdução ao Windows

Como mencionado acima, uma janela fornece uma área na qual modos de exibição e controles podem ser colocados e gerenciados e responde a eventos com base na interação do usuário (por meio de teclado ou mouse).

De acordo com a Apple, há cinco tipos principais de janelas em um aplicativo macOS:

- **Janela de documento** – uma janela de documento contém dados de usuário baseados em arquivo, como uma planilha ou um documento de texto.
- **Janela do aplicativo** – uma janela de aplicativo é a janela principal de um aplicativo que não é baseado em documento (como o aplicativo de calendário em um Mac).
- **Painel** -um painel flutua acima de outras janelas e fornece ferramentas ou controles com os quais os usuários podem trabalhar enquanto os documentos estão abertos. Em alguns casos, um painel pode ser translúcida (como ao trabalhar com gráficos grandes).
- **Caixa de diálogo** -uma caixa de diálogo é exibida em resposta a uma ação do usuário e normalmente fornece maneiras como os usuários podem concluir a ação. Uma caixa de diálogo requer uma resposta do usuário antes que possa ser fechada. (Consulte [trabalhando com caixas de diálogo](~/mac/user-interface/dialog.md))
- **Alertas** – um alerta é um tipo especial de caixa de diálogo que aparece quando ocorre um problema sério (como um erro) ou como um aviso (como a preparação para excluir um arquivo). Como um alerta é uma caixa de diálogo, ele também requer uma resposta do usuário para que possa ser fechado. (Consulte [trabalhando com alertas](~/mac/user-interface/alert.md))

Para obter mais informações, consulte a seção [sobre o Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1) dos [temas de design do MacOS](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/)da Apple.

### <a name="main-key-and-inactive-windows"></a>Janelas principal, chave e inativas

O Windows em um aplicativo Xamarin. Mac pode parecer e se comportar de forma diferente com base em como o usuário está interagindo atualmente com eles. A janela do documento ou do aplicativo principal que está em foco no momento da atenção do usuário é chamada de _janela principal_. Na maioria das instâncias, essa janela também será a _janela de chave_ (a janela que está atualmente aceitando a entrada do usuário). Mas isso nem sempre é o caso, por exemplo, um seletor de cor pode ser aberto e ser a janela de chave com a qual o usuário está interagindo para alterar o estado de um item na janela do documento (que ainda seria a janela principal).

As janelas principal e chave (se forem separadas) são sempre ativas, _janelas inativas_ são janelas abertas que não estão em primeiro plano. Por exemplo, um aplicativo de editor de texto poderia ter mais de um documento aberto por vez, somente a janela principal estaria ativa, todos os outros seriam inativos. 

Para obter mais informações, consulte a seção [sobre o Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1) dos [temas de design do MacOS](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/)da Apple.

### <a name="naming-windows"></a>Nomeando janelas

Uma janela pode exibir uma barra de título e, quando o título é exibido, geralmente é o nome do aplicativo, o nome do documento que está sendo trabalhado ou a função da janela (como, por exemplo, Inspector). Alguns aplicativos não exibem uma barra de título porque podem ser reconhecidos por visão e não funcionam com documentos.

A Apple sugere as seguintes diretrizes:

- Use o nome do aplicativo para o título de uma janela principal, que não seja de documento. 
- Nomear uma nova janela de documento `untitled` . Para o primeiro novo documento, não acrescente um número ao título (como `untitled 1` ). Se o usuário criar outro documento novo antes de salvar e retítuloar o primeiro, chame essa janela `untitled 2` , `untitled 3` etc.

Para obter mais informações, consulte a seção [nomenclatura do Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowNaming.html#//apple_ref/doc/uid/20000957-CH35-SW1) dos [temas de design do MacOS](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/)da Apple.

### <a name="full-screen-windows"></a>Janelas de tela inteira

No macOS, a janela de um aplicativo pode ser exibida em tela inteira, ocultando tudo, incluindo a barra de menus do aplicativo (que pode ser revelada movendo o cursor para a parte superior da tela) para fornecer uma interação livre de distrações com seu conteúdo.

A Apple sugere as seguintes diretrizes:

- Determine se faz sentido que uma janela fique em tela inteira. Os aplicativos que fornecem interações breves (como uma calculadora) não devem fornecer um modo de tela inteira.
- Mostrar a barra de ferramentas se a tarefa de tela inteira exigir. Normalmente, a barra de ferramentas fica oculta no modo de tela inteira.
- A janela de tela inteira deve ter todos os recursos que os usuários precisam para concluir a tarefa.
- Se possível, evite a interação do Finder enquanto o usuário estiver em uma janela de tela inteira.
- Aproveite o aumento de espaço na tela sem deslocar o foco para fora da tarefa principal.

Para obter mais informações, consulte a seção [Windows de tela inteira](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/FullScreen.html#//apple_ref/doc/uid/20000957-CH61-SW1) dos [temas de design do MacOS](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/)da Apple.

### <a name="panels"></a>Painéis

Um painel é uma janela auxiliar que contém controles e opções que afetam o documento ativo ou a seleção (como o seletor de cores do sistema):

[![Um painel de cores](window-images/panel01.png)](window-images/panel01.png#lightbox)

Os painéis podem ser _específicos do aplicativo ou de_ _todo_o setor. Os painéis específicos do aplicativo flutuam sobre a parte superior das janelas de documentos do aplicativo e desaparecem quando o aplicativo está em segundo plano. Painéis de todo o nível (como o painel **fontes** ), flutuar sobre todas as janelas abertas, independentemente do aplicativo. 

A Apple sugere as seguintes diretrizes:

- Em geral, use um painel padrão, os painéis transparentes devem ser usados com moderação e para tarefas graficamente intensivas.
- Considere usar um painel para fornecer aos usuários acesso fácil a controles importantes ou informações que afetam diretamente sua tarefa.
- Ocultar e mostrar painéis conforme necessário.
- Os painéis devem sempre incluir a barra de título.
- Os painéis não devem incluir um botão minimizar ativo.

#### <a name="inspectors"></a>Inspector

A maioria dos aplicativos macOS modernos apresenta controles auxiliares e opções que afetam o documento ativo ou seleção como _inspetores_ que fazem parte da janela principal (como o aplicativo de **páginas** mostrado abaixo), em vez de usar janelas de painel:

[![Um inspetor de exemplo](window-images/panel02.png)](window-images/panel02.png#lightbox)

Para obter mais informações, consulte a seção [painéis](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowPanels.html#//apple_ref/doc/uid/20000957-CH42-SW1) dos [temas de design do MacOS](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/) da Apple e nosso aplicativo de exemplo [MacInspector](/samples/xamarin/mac-samples/macinspector) para obter uma implementação completa de uma **interface de Inspetor** em um aplicativo Xamarin. Mac.

## <a name="creating-and-maintaining-windows-in-xcode"></a>Criando e mantendo janelas no Xcode

Ao criar um novo aplicativo Xamarin. Mac Cocoa, você obtém uma janela padrão em branco, por padrão. Essas janelas são definidas em um `.storyboard` arquivo incluído automaticamente no projeto. Para editar o design do Windows, na **Gerenciador de soluções**, clique duas vezes no `Main.storyboard` arquivo:

[![Selecionando o storyboard principal](window-images/edit01.png)](window-images/edit01.png#lightbox)

Isso abrirá o design da janela na Interface Builder do Xcode:

[![Editando a interface do usuário no Xcode](window-images/edit02.png)](window-images/edit02.png#lightbox)

No **Inspetor de atributo**, há várias propriedades que você pode usar para definir e controlar sua janela:

- **Título** -este é o texto que será exibido na TitleBar da janela.
- **Salvamento automático** – essa é a _chave_ que será usada para ID da janela quando for a posição e as configurações forem salvas automaticamente.
- **Barra de título** – a janela exibe uma barra de título.
- **Título e barra de ferramentas unificados** – se a janela incluir uma barra de ferramentas, ela deverá ser parte da barra de título.
- **Exibição de conteúdo de tamanho completo** – permite que a área de conteúdo da janela fique sob a barra de título.
- **Sombra** – a janela tem uma sombra.
- As janelas com textura **texturizada** podem usar efeitos (como Vibrancy) e podem ser movidas arrastando-se em qualquer lugar no corpo.
- **Close** -a janela tem um botão fechar.
- **Minimizar** – a janela tem um botão minimizar.
- **Redimensionar** – a janela tem um controle de redimensionamento.
- **Botão da barra de ferramentas** – a janela tem um botão de barra de ferramentas de ocultar/mostrar.
- **Restaurável** -é a posição da janela e as configurações salvas e restauradas automaticamente.
- **Visível na inicialização** – a janela é mostrada automaticamente quando o `.xib` arquivo é carregado.
- **Ocultar ao desativar** – a janela é ocultada quando o aplicativo entra no plano de fundo.
- **Liberar quando fechado** – a janela é limpa da memória quando é fechada.
- **Sempre exibir dicas** de ferramenta – as dicas de ferramenta são exibidas constantemente.
- **Recalcula o loop de exibição** – a ordem de exibição é recalculada antes que a janela seja desenhada.
- **Espaços**, **exposição** e de **ciclo** -todos definem como a janela se comporta nesses ambientes MacOS. 
- **Tela inteira** – determina se essa janela pode entrar no modo de tela inteira. 
- **Animação** – controla o tipo de animação disponível para a janela.
- **Aparência** – controla a aparência da janela. Por enquanto, há apenas uma aparência, azul-piscina.

Consulte [a introdução](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1) da Apple à documentação do Windows e do [nsmenuitem](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSWindow_Class/index.html#//apple_ref/occ/cl/NSWindow) para obter mais detalhes.

### <a name="setting-the-default-size-and-location"></a>Definindo o tamanho e o local padrão

Para definir a posição inicial da janela e controlar seu tamanho, alterne para o **Inspetor de tamanho**:

[![O tamanho e o local padrão](window-images/edit07.png)](window-images/edit07.png#lightbox)

A partir daqui, você pode definir o tamanho inicial da janela, dar a ela um tamanho mínimo e máximo, definir o local inicial na tela e controlar as bordas em torno da janela.

### <a name="setting-a-custom-main-window-controller"></a>Configurando um controlador de janela principal personalizado

Para poder criar saídas e ações para expor elementos da interface do usuário para o código C#, o aplicativo Xamarin. Mac precisará usar um controlador de janela personalizado.

Faça o seguinte:

1. Abra o storyboard do aplicativo no Interface Builder do Xcode.
2. Selecione o `NSWindowController` no design Surface.
3. Alterne para a exibição do **Inspetor de identidade** e insira `WindowController` como o **nome da classe**: 

    [![Definindo o nome da classe](window-images/windowcontroller01.png)](window-images/windowcontroller01.png#lightbox)
4. Salve as alterações e retorne ao Visual Studio para Mac para sincronização.
5. Um `WindowController.cs` arquivo será adicionado ao seu projeto no **Gerenciador de Soluções** no Visual Studio para Mac: 

    [![Selecionando o controlador do Windows](window-images/windowcontroller02.png)](window-images/windowcontroller02.png#lightbox)
6. Reabra o storyboard no Interface Builder do Xcode.
7. O `WindowController.h` arquivo estará disponível para uso: 

    [![Editando o arquivo WindowController. h](window-images/windowcontroller03.png)](window-images/windowcontroller03.png#lightbox)

### <a name="adding-ui-elements"></a>Adicionando elementos de interface do usuário

Para definir o conteúdo de uma janela, arraste os controles do **Inspetor de biblioteca** para o **Editor de interface**. Consulte nossa [introdução ao Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) documentação para obter mais informações sobre como usar interface Builder para criar e habilitar controles.

Como exemplo, vamos arrastar uma barra de ferramentas do **Inspetor de biblioteca** para a janela no **Editor de interface**:

[![Selecionando uma barra de ferramentas da biblioteca](window-images/edit03.png)](window-images/edit03.png#lightbox)

Em seguida, arraste em uma **exibição de texto** e dimensione-a para preencher a área sob a barra de ferramentas:

[![Adicionando uma exibição de texto](window-images/edit04.png)](window-images/edit04.png#lightbox)

Como queremos que a **exibição de texto** seja reduzida e cresça conforme o tamanho da janela é alterado, vamos alternar para o **Editor de restrição** e adicionar as seguintes restrições:

[![Restrições de edição](window-images/edit05.png)](window-images/edit05.png#lightbox)

Ao clicar nas quatro **transmissões vermelhas** na parte superior do editor e clicar em **Adicionar 4 restrições**, estamos dizendo ao modo de exibição de texto para que as coordenadas X, Y sejam aumentadas ou reduzidas horizontal e verticalmente conforme a janela é redimensionada.

Por fim, exponha a **exibição de texto** ao código usando uma **tomada** (selecionando o `ViewController.h` arquivo):

[![Configurando uma tomada](window-images/edit06.png)](window-images/edit06.png#lightbox)

Salve suas alterações e volte para Visual Studio para Mac para sincronizar com o Xcode.

Para obter mais informações sobre como trabalhar com **saídas** e **ações**, consulte nossa documentação [de tomada e ação](~/mac/get-started/hello-mac.md#outlets-and-actions) .

### <a name="standard-window-workflow"></a>Fluxo de trabalho de janela padrão

Para qualquer janela que você criar e trabalhar com em seu aplicativo Xamarin. Mac, o processo é basicamente o mesmo que acabamos de fazer acima:

1. Para novas janelas que não são o padrão adicionado automaticamente ao seu projeto, adicione uma nova definição de janela ao projeto. Isso será discutido em detalhes abaixo.
1. Clique duas vezes no `Main.storyboard` arquivo para abrir o design da janela para edição no interface Builder do Xcode.
1. Arraste uma nova janela para o design da interface do usuário e conecte a janela à janela principal usando _continuações_ (para obter mais informações, consulte a seção [continuações](~/mac/platform/storyboards/indepth.md#Segues) da documentação [trabalhando com storyboards](~/mac/platform/storyboards/indepth.md) ).
1. Defina todas as propriedades de janela necessárias no **Inspetor de atributo** e no Inspetor de **tamanho**.
1. Arraste os controles necessários para criar sua interface e configurá-los no **Inspetor de atributo**.
1. Use o **Inspetor de tamanho** para lidar com o redimensionamento dos elementos da interface do usuário.
1. Expor os elementos da interface do usuário da janela para código C# por meio de **saídas** e **ações**.
1. Salve suas alterações e volte para Visual Studio para Mac para sincronizar com o Xcode.

Agora que temos uma janela básica criada, veremos os processos típicos que um aplicativo Xamarin. Mac faz ao trabalhar com o Windows. 

## <a name="displaying-the-default-window"></a>Exibindo a janela padrão

Por padrão, um novo aplicativo Xamarin. Mac exibirá automaticamente a janela definida no `MainWindow.xib` arquivo quando ele for iniciado:

[![Uma janela de exemplo em execução](window-images/display01.png)](window-images/display01.png#lightbox)

Como modificamos o design dessa janela acima, ela agora inclui uma barra de ferramentas padrão e um controle de **exibição de texto** . A seção a seguir no `Info.plist` arquivo é responsável por exibir esta janela:

[![Editando o info. plist](window-images/display00.png)](window-images/display00.png#lightbox)

A lista suspensa da **interface principal** é usada para selecionar o storyboard que será usado como a interface do usuário do aplicativo principal (nesse caso `Main.storyboard` ).

Um controlador de exibição é adicionado automaticamente ao projeto para controlar as janelas principais que são exibidas (juntamente com sua exibição primária). Ele é definido no `ViewController.cs` arquivo e anexado ao **proprietário do arquivo** em interface Builder no **Inspetor de identidade**:

[![Definindo o proprietário do arquivo](window-images/display02.png)](window-images/display02.png#lightbox)

Para nossa janela, gostaríamos de ter um título de `untitled` quando ele for aberto pela primeira vez, então vamos substituir o `ViewWillAppear` método no `ViewController.cs` para se parecer com o seguinte:

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set Window Title
    this.View.Window.Title = "untitled";
}
```    

> [!NOTE]
> A propriedade da janela `Title` é definida no `ViewWillAppear` método em vez do `ViewDidLoad` método porque, embora a exibição possa ser carregada na memória, ela ainda não está totalmente instanciada. Acessar a `Title` propriedade no método obteremos `ViewDidLoad` uma `null` exceção, já que a janela ainda não foi construída e conectada à propriedade.

## <a name="programmatically-closing-a-window"></a>Fechando programaticamente uma janela

Pode haver ocasiões em que você deseja fechar programaticamente uma janela em um aplicativo Xamarin. Mac, além de ter o usuário clicado no botão **fechar** da janela ou usando um item de menu. o macOS fornece duas maneiras diferentes de fechar um `NSWindow` programaticamente: `PerformClose` e `Close` .

### <a name="performclose"></a>PerformClose

Chamar o `PerformClose` método de um `NSWindow` simula o usuário clicando no botão **Fechar** da janela, destacando momentaneamente o botão e fechando a janela.

Se o aplicativo implementar o `NSWindow` `WillClose` evento, ele será gerado antes que a janela seja fechada. Se o evento retornar `false` , a janela não será fechada. Se a janela não tiver um botão **fechar** ou não puder ser fechada por algum motivo, o sistema operacional emitirá o som do alerta.

Por exemplo:

```csharp
MyWindow.PerformClose(this);
```

Tentaria fechar a `MyWindow` `NSWindow` instância. Se tiver êxito, a janela será fechada, caso contrário, o som do alerta será emitido e o permanecerá aberto.

### <a name="close"></a>Fechar

Chamar o `Close` método de um não `NSWindow` simula o usuário clicando no botão **Fechar** da janela, destacando momentaneamente o botão, ele simplesmente fecha a janela.

Uma janela não precisa estar visível para ser fechada e uma `NSWindowWillCloseNotification` notificação será postada no centro de notificações padrão para a janela que está sendo fechada.

O `Close` método difere de duas maneiras importantes do `PerformClose` método:

1. Ele não tenta gerar o `WillClose` evento.
2. Ele não simula o usuário clicando no botão **fechar** , destacando momentaneamente o botão.

Por exemplo:

```csharp
MyWindow.Close();
```

Seria possível fechar a `MyWindow` `NSWindow` instância.

## <a name="modified-windows-content"></a>Conteúdo do Windows modificado

No macOS, a Apple forneceu uma maneira de informar ao usuário que o conteúdo de uma janela ( `NSWindow` ) foi modificado pelo usuário e precisa ser salvo. Se a janela contiver conteúdo modificado, um pequeno ponto preto será exibido no widget de **fechamento** :

[![Uma janela com o marcador modificado](window-images/close01.png)](window-images/close01.png#lightbox)

Se o usuário tentar fechar a janela ou sair do aplicativo Mac enquanto houver alterações não salvas no conteúdo da janela, você deverá apresentar uma [caixa de diálogo](~/mac/user-interface/dialog.md) ou uma [planilha modal](~/mac/user-interface/dialog.md) e permitir que o usuário salve suas alterações primeiro:

[![Uma planilha de salvamento que está sendo mostrada quando a janela é fechada](window-images/close02.png)](window-images/close02.png#lightbox)

### <a name="marking-a-window-as-modified"></a>Marcando uma janela como modificada

Para marcar uma janela como tendo conteúdo modificado, use o seguinte código:

```csharp
// Mark Window content as modified
Window.DocumentEdited = true;
```

E, depois que a alteração tiver sido salva, limpe o sinalizador modificado usando:

```csharp
// Mark Window content as not modified
Window.DocumentEdited = false;
```

### <a name="saving-changes-before-closing-a-window"></a>Salvando alterações antes de fechar uma janela

Para ver o usuário fechando uma janela e permitindo que ele salve o conteúdo modificado antecipadamente, você precisará criar uma subclasse de `NSWindowDelegate` e substituir seu `WindowShouldClose` método. Por exemplo:

```csharp
using System;
using AppKit;
using System.IO;
using Foundation;

namespace SourceWriter
{
    public class EditorWindowDelegate : NSWindowDelegate
    {
        #region Computed Properties
        public NSWindow Window { get; set;}
        #endregion

        #region constructors
        public EditorWindowDelegate (NSWindow window)
        {
            // Initialize
            this.Window = window;

        }
        #endregion

        #region Override Methods
        public override bool WindowShouldClose (Foundation.NSObject sender)
        {
            // is the window dirty?
            if (Window.DocumentEdited) {
                var alert = new NSAlert () {
                    AlertStyle = NSAlertStyle.Critical,
                    InformativeText = "Save changes to document before closing window?",
                    MessageText = "Save Document",
                };
                alert.AddButton ("Save");
                alert.AddButton ("Lose Changes");
                alert.AddButton ("Cancel");
                var result = alert.RunSheetModal (Window);

                // Take action based on result
                switch (result) {
                case 1000:
                    // Grab controller
                    var viewController = Window.ContentViewController as ViewController;

                    // Already saved?
                    if (Window.RepresentedUrl != null) {
                        var path = Window.RepresentedUrl.Path;

                        // Save changes to file
                        File.WriteAllText (path, viewController.Text);
                        return true;
                    } else {
                        var dlg = new NSSavePanel ();
                        dlg.Title = "Save Document";
                        dlg.BeginSheet (Window, (rslt) => {
                            // File selected?
                            if (rslt == 1) {
                                var path = dlg.Url.Path;
                                File.WriteAllText (path, viewController.Text);
                                Window.DocumentEdited = false;
                                viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
                                viewController.View.Window.RepresentedUrl = dlg.Url;
                                Window.Close();
                            }
                        });
                        return true;
                    }
                    return false;
                case 1001:
                    // Lose Changes
                    return true;
                case 1002:
                    // Cancel
                    return false;
                }
            }

            return true;
        }
        #endregion
    }
}
```

Use o código a seguir para anexar uma instância desse delegado à janela:

```csharp
// Set delegate
Window.Delegate = new EditorWindowDelegate(Window);
```

### <a name="saving-changes-before-closing-the-app"></a>Salvando alterações antes de fechar o aplicativo

Por fim, seu aplicativo Xamarin. Mac deve verificar se alguma de suas janelas contém conteúdo modificado e permitir que o usuário salve as alterações antes de sair. Para fazer isso, edite o `AppDelegate.cs` arquivo, substitua o `ApplicationShouldTerminate` método e faça com que ele se pareça com o seguinte:

```csharp
public override NSApplicationTerminateReply ApplicationShouldTerminate (NSApplication sender)
{
    // See if any window needs to be saved first
    foreach (NSWindow window in NSApplication.SharedApplication.Windows) {
        if (window.Delegate != null && !window.Delegate.WindowShouldClose (this)) {
            // Did the window terminate the close?
            return NSApplicationTerminateReply.Cancel;
        }
    }

    // Allow normal termination
    return NSApplicationTerminateReply.Now;
}
```

## <a name="working-with-multiple-windows"></a>Trabalhando com várias janelas

A maioria dos aplicativos Mac baseados em documentos pode editar vários documentos ao mesmo tempo. Por exemplo, um editor de texto pode ter vários arquivos de texto abertos para edição ao mesmo tempo. Por padrão, um novo aplicativo Xamarin. Mac tem um menu **arquivo** com um **novo** item automaticamente conectado à `newDocument:` **ação**.

O código a seguir ativará esse novo item e permitirá que o usuário abra várias cópias da janela principal para editar vários documentos ao mesmo tempo.

Edite o `AppDelegate.cs` arquivo e adicione a seguinte Propriedade computada:

```csharp
public int UntitledWindowCount { get; set;} =1;
```

Use isso para acompanhar o número de arquivos não salvos para que possamos fornecer comentários ao usuário (de acordo com as diretrizes da Apple, conforme discutido acima).

Em seguida, adicione o seguinte método:

```csharp
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

Esse código cria uma nova versão do nosso controlador de janela, carrega a nova janela, transforma-a na janela principal e chave e a define como título. Agora, se executarmos nosso aplicativo e selecionarmos **novo** no menu **arquivo** , uma nova janela do editor será aberta e exibida:

[![Uma nova janela sem título foi adicionada](window-images/display04.png)](window-images/display04.png#lightbox)

Se abrirmos o menu do **Windows** , você poderá ver que o aplicativo está rastreando e manipulando automaticamente nossas janelas abertas:

[![O menu do Windows](window-images/display05.png)](window-images/display05.png#lightbox)

Para obter mais informações sobre como trabalhar com menus em um aplicativo Xamarin. Mac, consulte [a documentação trabalhando com menus](~/mac/user-interface/menu.md) .

### <a name="getting-the-currently-active-window"></a>Obtendo a janela ativa no momento

Em um aplicativo Xamarin. Mac que pode abrir várias janelas (documentos), há ocasiões em que você precisará obter a janela atual, na extremidade superior (a janela de chave). O seguinte código retornará a janela de chave:

```csharp
var window = NSApplication.SharedApplication.KeyWindow;
```

Ele pode ser chamado em qualquer classe ou método que precise acessar a janela de chave atual. Se nenhuma janela estiver aberta no momento, ela retornará `null` .

### <a name="accessing-all-app-windows"></a>Acessando todas as janelas de aplicativo

Pode haver ocasiões em que você precisa acessar todas as janelas que seu aplicativo Xamarin. Mac abriu atualmente. Por exemplo, para ver se um arquivo que o usuário deseja abrir já está aberto em uma janela de saída.

O `NSApplication.SharedApplication` mantém uma `Windows` propriedade que contém uma matriz de todas as janelas abertas em seu aplicativo. Você pode iterar nessa matriz para acessar todas as janelas atuais do aplicativo. Por exemplo:

```csharp
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

No código de exemplo, estamos convertendo cada janela retornada para a `ViewController` classe personalizada em nosso aplicativo e testando o valor de uma `Path` propriedade personalizada em relação ao caminho de um arquivo que o usuário deseja abrir. Se o arquivo já estiver aberto, estamos trazendo essa janela para a frente.

## <a name="adjusting-the-window-size-in-code"></a>Ajustando o tamanho da janela no código

Há ocasiões em que o aplicativo precisa redimensionar uma janela no código. Para redimensionar e reposicionar uma janela, você ajusta sua `Frame` propriedade. Ao ajustar o tamanho de uma janela, normalmente você também precisa ajustar sua origem, para manter a janela no mesmo local devido ao sistema de coordenadas do macOS.

Ao contrário do iOS, no qual o canto superior esquerdo representa (0, 0), o macOS usa um sistema de coordenadas matemáticos onde o canto inferior esquerdo da tela representa (0, 0). No iOS, as coordenadas aumentam à medida que você avança para a direita. No macOS, as coordenadas aumentam em valor para a direita. 

O seguinte código de exemplo redimensiona uma janela:

```csharp
nfloat y = 0;

// Calculate new origin
y = Frame.Y - (768 - Frame.Height);

// Resize and position window
CGRect frame = new CGRect (Frame.X, y, 1024, 768);
SetFrame (frame, true);
```

> [!IMPORTANT]
> Ao ajustar um tamanho e um local do Windows no código, você precisa garantir que os tamanhos mínimo e máximo definidos no Interface Builder. Isso não será automaticamente liquidado e você poderá tornar a janela maior ou menor que esses limites.

## <a name="monitoring-window-size-changes"></a>Alterações no tamanho da janela de monitoramento

Pode haver ocasiões em que você precisa monitorar as alterações no tamanho da janela dentro do seu aplicativo Xamarin. Mac. Por exemplo, para redesenhar conteúdo para se ajustar ao novo tamanho.

Para monitorar alterações de tamanho, primeiro verifique se você atribuiu uma classe personalizada para o controlador de janela no Interface Builder do Xcode. Por exemplo, `MasterWindowController` no seguinte:

[![O Inspetor de identidade](window-images/resize01.png)](window-images/resize01.png#lightbox)

Em seguida, edite a classe personalizada do controlador de janela e monitore o `DidResize` evento na janela do controlador para ser notificado de alterações de tamanho ao vivo. Por exemplo:

```csharp
public override void WindowDidLoad ()
{
    base.WindowDidLoad ();

    Window.DidResize += (sender, e) => {
        // Do something as the window is being live resized
    };
}
```

Opcionalmente, você pode usar o `DidEndLiveResize` evento para ser notificado somente depois que o usuário terminar de alterar o tamanho da janela. Por exemplo:

```csharp
public override void WindowDidLoad ()
{
    base.WindowDidLoad ();

        Window.DidEndLiveResize += (sender, e) => {
        // Do something after the user's finished resizing
        // the window
    };
}
```

## <a name="setting-a-windows-title-and-represented-file"></a>Definindo o título de uma janela e um arquivo representado

Ao trabalhar com janelas que representam documentos, `NSWindow` o tem uma `DocumentEdited` propriedade que, se definida para `true` exibir um pequeno ponto no botão fechar, para dar ao usuário uma indicação de que o arquivo foi modificado e deve ser salvo antes de fechar.

Vamos editar nosso `ViewController.cs` arquivo e fazer as seguintes alterações:

```csharp
public bool DocumentEdited {
    get { return View.Window.DocumentEdited; }
    set { View.Window.DocumentEdited = value; }
}
...

public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set Window Title
    this.View.Window.Title = "untitled";

    View.Window.WillClose += (sender, e) => {
        // is the window dirty?
        if (DocumentEdited) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to give the user the ability to save the document here...",
                MessageText = "Save Document",
            };
            alert.RunModal ();
        }
    };
}

public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Show when the document is edited
    DocumentEditor.TextDidChange += (sender, e) => {
        // Mark the document as dirty
        DocumentEdited = true;
    };

    // Overriding this delegate is required to monitor the TextDidChange event
    DocumentEditor.ShouldChangeTextInRanges += (NSTextView view, NSValue[] values, string[] replacements) => {
        return true;
    };

}
```

Também estamos monitorando o `WillClose` evento na janela e verificando o estado da `DocumentEdited` propriedade. Se for `true` necessário dar ao usuário a capacidade de salvar as alterações no arquivo. Se executarmos nosso aplicativo e inserirmos algum texto, o ponto será exibido:

[![Uma janela alterada](window-images/file01.png)](window-images/file01.png#lightbox)

Se você tentar fechar a janela, receberá um alerta:

[![Exibindo uma caixa de diálogo salvar](window-images/file02.png)](window-images/file02.png#lightbox)

Se você estiver carregando um documento de um arquivo, defina o título da janela como o nome do arquivo usando o `window.SetTitleWithRepresentedFilename (Path.GetFileName(path));` método (dado que `path` é uma cadeia de caracteres que representa o arquivo que está sendo aberto). Além disso, você pode definir a URL do arquivo usando o `window.RepresentedUrl = url;` método.

Se a URL estiver apontando para um tipo de arquivo conhecido pelo sistema operacional, seu ícone será exibido na barra de título. Se o usuário clicar com o botão direito do mouse no ícone, o caminho para o arquivo será mostrado.

Edite o `AppDelegate.cs` arquivo e adicione o seguinte método:

```csharp
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
            var path = url.Path;

            // Get new window
            var storyboard = NSStoryboard.FromName ("Main", null);
            var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

            // Display
            controller.ShowWindow(this);

            // Load the text into the window
            var viewController = controller.Window.ContentViewController as ViewController;
            viewController.Text = File.ReadAllText(path);
                    viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
            viewController.View.Window.RepresentedUrl = url;

        }
    }
}
```

Agora, se executarmos nosso aplicativo, selecione **abrir...** no menu **arquivo** , selecione um arquivo de texto na caixa de diálogo **abrir** e abra-o:

[![Uma caixa de diálogo aberta](window-images/file03.png)](window-images/file03.png#lightbox)

O arquivo será exibido e o título será definido com o ícone do arquivo:

[![O conteúdo de um arquivo carregado](window-images/file04.png)](window-images/file04.png#lightbox)

## <a name="adding-a-new-window-to-a-project"></a>Adicionando uma nova janela a um projeto

Além da janela principal do documento, um aplicativo Xamarin. Mac pode precisar exibir outros tipos de janelas para o usuário, como preferências ou painéis de inspetores.

Para adicionar uma nova janela, faça o seguinte:

1. No **Gerenciador de soluções**, clique duas vezes no `Main.storyboard` arquivo para abri-lo para edição no interface Builder do Xcode.
2. Arraste um novo **controlador de janela** da **biblioteca** e solte-o na **design Surface**:

    [![Selecionando um novo controlador de janela na biblioteca](window-images/new01.png)](window-images/new01.png#lightbox)
3. No **Inspetor de identidade**, insira `PreferencesWindow` para a **ID do storyboard**: 

    [![Definindo a ID do storyboard](window-images/new02.png)](window-images/new02.png#lightbox)
4. Projete sua interface: 

    [![Como projetar a interface do usuário](window-images/new03.png)](window-images/new03.png#lightbox)
5. Abra o menu do aplicativo ( `MacWindows` ), selecione **preferências...**, clique com o botão de controle e arraste para a nova janela: 

    [![Criando um transição](window-images/new05.png)](window-images/new05.png#lightbox)
6. Selecione **Mostrar** no menu pop-up.
7. Salve as alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Se executarmos o código e selecionarmos as **preferências...** no **menu do aplicativo**, a janela será exibida:

[![Um menu de preferências de exemplo](window-images/new04.png)](window-images/new04.png#lightbox)

## <a name="working-with-panels"></a>Trabalhando com painéis

Conforme mencionado no início deste artigo, um painel flutua acima de outras janelas e fornece ferramentas ou controles com os quais os usuários podem trabalhar enquanto os documentos estão abertos. 

Assim como qualquer outro tipo de janela que você cria e trabalha em seu aplicativo Xamarin. Mac, o processo é basicamente o mesmo:

1. Adicione uma nova definição de janela ao projeto.
2. Clique duas vezes no `.xib` arquivo para abrir o design da janela para edição no interface Builder do Xcode.
3. Defina todas as propriedades de janela necessárias no **Inspetor de atributo** e no Inspetor de **tamanho**.
4. Arraste os controles necessários para criar sua interface e configurá-los no **Inspetor de atributo**.
5. Use o **Inspetor de tamanho** para lidar com o redimensionamento dos elementos da interface do usuário.
6. Expor os elementos da interface do usuário da janela para código C# por meio de **saídas** e **ações**.
7. Salve suas alterações e volte para Visual Studio para Mac para sincronizar com o Xcode.

No **Inspetor de atributo**, você tem as seguintes opções específicas para os painéis:

[![O Inspetor de atributo](window-images/panel03.png)](window-images/panel03.png#lightbox)

- **Estilo** -permite que você ajuste o estilo do painel de: painel regular (parece uma janela padrão), painel do utilitário (tem uma barra de título menor), painel HUD (é translúcida e a barra de título faz parte do plano de fundo).
- **Não ativa** -determina no painel se torna a janela de chave.
- **Documento modal** – se o documento for modal, o painel só flutuará acima das janelas do aplicativo, caso contrário, ele flutuará acima de todos.

Para adicionar um novo painel, faça o seguinte:

1. Na **Gerenciador de soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar**  >  **novo arquivo...**.
2. Na caixa de diálogo novo arquivo, selecione janela **Xamarin. Mac**  >  **Cocoa com controlador**:

    [![Adicionando um novo controlador de janela](window-images/panels00.png)](window-images/panels00.png#lightbox)

3. Digite `DocumentPanel` para o **Nome** e clique no botão **Novo**.
4. Clique duas vezes no `DocumentPanel.xib` arquivo para abri-lo para edição no interface Builder: 

    [![Editando o painel](window-images/new02.png)](window-images/new02.png#lightbox)

5. Exclua a janela existente e arraste um painel do **Inspetor de biblioteca** no **Editor de interface**: 

    [![Excluindo a janela existente](window-images/panels01.png)](window-images/panels01.png#lightbox)

6. Conecte o painel à tomada da **janela do proprietário do arquivo**  -  **window**  -  **Outlet**: 

    [![Arrastando para conectar o painel](window-images/panels02.png)](window-images/panels02.png#lightbox)

7. Alterne para o **Inspetor de identidade** e defina a classe do painel como `DocumentPanel` : 

    [![Definindo a classe do painel](window-images/panels03.png)](window-images/panels03.png#lightbox)

8. Salve as alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.
9. Edite o `DocumentPanel.cs` arquivo e altere a definição de classe para o seguinte: 

    `public partial class DocumentPanel : NSPanel`

10. Salve as alterações no arquivo.

Edite o `AppDelegate.cs` arquivo e faça com que o `DidFinishLaunching` método se pareça com o seguinte:

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
        // Display panel
    var panel = new DocumentPanelController ();
    panel.Window.MakeKeyAndOrderFront (this);
}

```

Se executarmos nosso aplicativo, o painel será exibido:

[![O painel em um aplicativo em execução](window-images/panels04.png)](window-images/panels04.png#lightbox)

> [!IMPORTANT]
> As janelas de painel foram preteridas pela Apple e devem ser substituídas por **interfaces de Inspetor**. Para obter um exemplo completo de criação de um **Inspetor** em um aplicativo Xamarin. Mac, consulte nosso aplicativo de exemplo [MacInspector](/samples/xamarin/mac-samples/macinspector) .

## <a name="summary"></a>Resumo

Este artigo deu uma visão detalhada de como trabalhar com janelas e painéis em um aplicativo Xamarin. Mac. Vimos os diferentes tipos e usos de janelas e painéis, como criar e manter janelas e painéis no Interface Builder do Xcode e como trabalhar com janelas e painéis em código C#.

## <a name="related-links"></a>Links relacionados

- [MacWindows (exemplo)](/samples/xamarin/mac-samples/macwindows)
- [MacInspector (exemplo)](/samples/xamarin/mac-samples/macinspector)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabalhando com menus](~/mac/user-interface/menu.md)
- [Apple (temas de design do macOS)](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/)
- [Windows, painéis e telas (Apple)](https://developer.apple.com/documentation/appkit/windows_panels_and_screens)