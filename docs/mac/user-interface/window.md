---
title: Windows no xamarin. Mac
description: Este artigo aborda o trabalho com janelas e painéis em um aplicativo xamarin. Mac. Ele descreve a criação de janelas e painéis no Xcode e Interface Builder, carregando-os de arquivos. XIB e storyboards e trabalhar com eles por meio de programação.
ms.prod: xamarin
ms.assetid: 4F6C67E9-BBFF-44F7-B29E-AB47D7F44287
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: ec907e71074a97bd5d1714e79dd504013f5c8a4b
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2018
ms.locfileid: "51526969"
---
# <a name="windows-in-xamarinmac"></a>Windows no xamarin. Mac

_Este artigo aborda o trabalho com janelas e painéis em um aplicativo xamarin. Mac. Ele descreve a criação de janelas e painéis no Xcode e Interface Builder, carregando-os de arquivos. XIB e storyboards e trabalhar com eles por meio de programação._

Ao trabalhar com C# e do .NET em um aplicativo xamarin. Mac, você tem acesso para o mesmo Windows e painéis que um desenvolvedor que trabalha *Objective-C* e *Xcode* faz. Como o xamarin. Mac se integra diretamente com Xcode, você pode usar do Xcode _construtor de Interface_ para criar e manter o Windows e os painéis (ou, opcionalmente, criá-los diretamente em código c#).

Com base na sua finalidade, um aplicativo xamarin. Mac pode apresentar um ou mais Windows na tela para gerenciar e coordenar as informações que ele exibe e funciona com. As principais funções de uma janela são:

1. Para fornecer uma área na qual controles e modos de exibição podem ser colocados e gerenciados.
2. Para aceitar e responder a eventos em resposta à interação do usuário com o teclado e mouse.

Windows podem ser usado em um estado sem janela restrita (por exemplo, um editor de texto que pode ter vários documentos abertos ao mesmo tempo) ou Modal (por exemplo, uma caixa de diálogo de exportação deve ser descartada antes que o aplicativo possa continuar).

Painéis são um tipo especial de janela (uma subclasse da base de `NSWindow` classe), que normalmente atender a uma função auxiliar em um aplicativo, como janelas de utilitário como inspetores de formato de texto e o seletor de cores do sistema.

[![](window-images/intro01.png "Edição de uma janela no Xcode")](window-images/intro01.png#lightbox)

Neste artigo, abordaremos os fundamentos de trabalhar com o Windows e painéis em um aplicativo xamarin. Mac. É altamente recomendável que você trabalhe por meio de [Hello, Mac](~/mac/get-started/hello-mac.md) pela primeira vez, especificamente o artigo a [Introdução ao Interface Builder e Xcode](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [saídas e ações](~/mac/get-started/hello-mac.md#outlets-and-actions) seções, como ela aborda os principais conceitos e técnicas que usaremos neste artigo.

Talvez você queira dar uma olhada o [classes expondo c# / métodos para Objective-C](~/mac/internals/how-it-works.md) seção o [recursos internos de xamarin. Mac](~/mac/internals/how-it-works.md) documentar Além disso, ele explica o `Register` e `Export` comandos usado para transmissão-up suas classes de c# a objetos de Objective-C e elementos de interface do usuário.

<a name="Introduction_to_Windows" />

## <a name="introduction-to-windows"></a>Introdução ao Windows

Como mencionado acima, uma janela fornece uma área na qual controles e modos de exibição podem ser colocados e gerenciados e responde a eventos com base na interação do usuário (por meio do teclado ou mouse).

De acordo com a Apple, há cinco tipos de principais do Windows em um aplicativo do macOS:

- **Janela de documento** -uma janela de documento contém dados de usuário com base em arquivo, como uma planilha ou um documento de texto.
- **Janela do aplicativo** -uma janela do aplicativo é a janela principal de um aplicativo que não é baseada em documento (como o aplicativo de calendário em um Mac).
- **Painel** - um painel flutua sobre outras janelas e fornece ferramentas ou controles que os usuários podem trabalhar com enquanto documentos estiverem abrir. Em alguns casos, um painel pode ser translúcido (por exemplo, ao trabalhar com gráficos grandes).
- **Caixa de diálogo** -uma caixa de diálogo é exibida em resposta a uma ação do usuário e geralmente fornece formas como os usuários podem concluir a ação. Uma caixa de diálogo requer uma resposta do usuário antes de ser fechado. (Consulte [trabalhando com caixas de diálogo](~/mac/user-interface/dialog.md))
- **Alertas** -um alerta é um tipo especial de caixa de diálogo que aparece quando um problema sério ocorre (por exemplo, um erro) ou como um aviso (por exemplo, se preparando para excluir um arquivo). Como um alerta é uma caixa de diálogo, ele também requer uma resposta do usuário antes de ser fechado. (Consulte [trabalhando com alertas](~/mac/user-interface/alert.md))

Para obter mais informações, consulte o [sobre o Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1) seção da Apple [diretrizes de Interface humana dos X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Main_Key_and_Inactive_Windows" />

### <a name="main-key-and-inactive-windows"></a>Principal, a chave e o Windows inativos

O Windows em um aplicativo xamarin. Mac pode parecer e se comportam de maneira diferente com base em como o usuário atualmente está interagindo com eles. O principal documento ou janela de aplicativo que está sendo foco de atenção do usuário é chamada de _janela principal do_. Na maioria dos casos essa janela também serão os _janela chave_ (a janela que atualmente está aceitando a entrada do usuário). Mas isso nem sempre é o caso, por exemplo, um seletor de cor pode ser aberta e ser a janela de chave que o usuário está interagindo com para alterar o estado de um item na janela do documento (que ainda seria a janela principal).

O principal e a chave do Windows (se forem separados) são sempre ativa, _inativos Windows_ são janelas abertas que não estão em primeiro plano. Por exemplo, um aplicativo de editor de texto pode ter mais de um documento aberto ao mesmo tempo, somente a janela principal seria ativa, todos os outros seria inativos. 

Para obter mais informações, consulte o [sobre o Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1) seção da Apple [diretrizes de Interface humana dos X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Naming_Windows" />

### <a name="naming-windows"></a>Nomenclatura do Windows

Uma janela pode exibir uma barra de título e quando o título é exibido, é normalmente o nome do aplicativo, o nome do documento que está sendo trabalhado ou a função de janela (por exemplo, o Inspetor). Alguns aplicativos não exibem uma barra de título, pois eles são reconhecidos pela visão e não funcionam com documentos.

Apple sugere as diretrizes a seguir:

- Use o nome do aplicativo para o título de uma janela principal, não do documento. 
- Nome de uma nova janela de documento `untitled`. Para o novo documento, não acrescentar um número para o título (como `untitled 1`). Se o usuário cria outro novo documento antes de salvar e inclinadas a primeira, chamar essa janela `untitled 2`, `untitled 3`, etc.

Para obter mais informações, consulte o [Windows nomenclatura](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowNaming.html#//apple_ref/doc/uid/20000957-CH35-SW1) seção da Apple [diretrizes de Interface humana dos X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Full-Screen_Windows" />

### <a name="full-screen-windows"></a>Windows em tela inteira

No macOS, janela de um aplicativo pode acessar a ocultação de tela inteira tudo, incluindo a barra de menus do aplicativo (que pode ser reveladas, movendo o cursor na parte superior da tela) para fornecer distração livre interação com ele é o conteúdo.

A Apple sugere as diretrizes a seguir:

- Determine se faz sentido para uma janela acessar a tela inteira. Aplicativos que fornecem as breves interações (como uma calculadora) não devem fornecer um modo de tela inteira.
- Mostre barra de ferramentas se a tarefa em tela inteira exige a ele. Normalmente, a barra de ferramentas está oculta enquanto estiver no modo de tela inteira.
- A janela de tela inteira deve ter todos os usuários de recursos necessário concluir a tarefa.
- Se possível, evite a interação do localizador enquanto o usuário está em uma janela de tela inteira.
- Aproveite o espaço de tela maior sem mudando o foco da tarefa principal.

Para obter mais informações, consulte o [Windows em tela inteira](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/FullScreen.html#//apple_ref/doc/uid/20000957-CH61-SW1) seção da Apple [diretrizes de Interface humana dos X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Panels" />

### <a name="panels"></a>Painéis

Um painel é um auxiliar que contém controles e as opções que afetam o documento ativo ou a seleção (como o seletor de cores do sistema):

[![](window-images/panel01.png "Um painel de cores")](window-images/panel01.png#lightbox)

Painéis podem ser _específicos do aplicativo_ ou _todo o sistema_. Painéis específicos do aplicativo flutuar ao longo da parte superior do aplicativo janelas de documento e desaparecem quando o aplicativo está em segundo plano. Painéis no âmbito do sistema (como o **fontes** painel), float, na parte superior de todas as janelas abertas, independentemente do aplicativo. 

A Apple sugere as diretrizes a seguir:

- Em geral, use um painel padrão, transparent painéis só devem ser usados com moderação e para tarefas graficamente intensivas.
- Considere o uso de um painel para dar aos usuários acesso fácil a controles importantes ou informações que afetam diretamente a sua tarefa.
- Ocultar e Mostrar painéis conforme necessário.
- Painéis sempre devem incluir a barra de título.
- Painéis não devem incluir um botão de minimizar Active Directory.

#### <a name="inspectors"></a>Inspetores

Os aplicativos mais modernos do macOS apresentam controles auxiliares e opções que afetam o documento ativo ou a seleção como _Inspectors_ que fazem parte da janela principal (como o **páginas** aplicativo mostrado abaixo), em vez de usar o painel Windows:

[![](window-images/panel02.png "Um Inspetor de exemplo")](window-images/panel02.png#lightbox)

Para obter mais informações, consulte o [painéis](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowPanels.html#//apple_ref/doc/uid/20000957-CH42-SW1) seção da Apple [diretrizes de Interface humana dos X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) e em nosso [MacInspector](https://developer.xamarin.com/samples/mac/MacInspector/) aplicativo de exemplo para uma implementação completa de um **Inspector Interface** em um aplicativo xamarin. Mac.

<a name="Creating_and_Maintaining_Windows_in_Xcode" />

## <a name="creating-and-maintaining-windows-in-xcode"></a>Criar e manter o Windows no Xcode

Quando você cria um novo aplicativo xamarin. Mac Cocoa, você obtém uma janela de padrão em branco, por padrão. Esse windows é definido em um `.storyboard` arquivo incluído automaticamente no projeto. Para editar seu design do windows, nos **Gerenciador de soluções**, clique duas vezes o `Main.storyboard` arquivo:

[![](window-images/edit01.png "Selecionando o storyboard principal")](window-images/edit01.png#lightbox)

Isso abrirá o design de janela no Interface Builder do Xcode:

[![](window-images/edit02.png "Editando a interface do usuário no Xcode")](window-images/edit02.png#lightbox)

No **Inspetor de atributo**, há várias propriedades que você pode usar para definir e controlar sua janela:

- **Título** -este é o texto que será exibido na barra de título da janela.
- **Salvamento automático** -este é o _chave_ que será usado para a janela da ID quando sua posição e as configurações são salvas automaticamente.
- **Barra de título** -faz a janela exibir uma barra de título.
- **Título e barra de ferramentas unificadas** - se a janela inclui uma barra de ferramentas será parte da barra de título.
- **Total de exibição de conteúdo em tamanho** -permite que a área de conteúdo da janela para estar sob a barra de título.
- **Sombra** -a janela tem uma sombra.
- **Texturizado** -texturizadas windows pode usar efeitos (como valorize) e podem ser movidos, arrastando-se em qualquer lugar no corpo.
- **Fechar** -a janela tem um botão Fechar.
- **Minimizar** -a janela tem um botão Minimizar.
- **Redimensionar** -a janela tem um controle de redimensionamento.
- **Botão de barra de ferramentas** -a janela tem um botão de barra de ferramentas Mostrar/ocultar.
- **Restauráveis** -é a posição e configurações automaticamente salvo e restaurado da janela.
- **Visível em inicie** -é a janela mostrada automaticamente quando o `.xib` arquivo é carregado.
- **Ocultar ao desativar** -a janela é ocultada quando o aplicativo entra em segundo plano.
- **Versão quando fechado** -é a janela removida da memória quando ele é fechado.
- **Sempre exibir dicas de ferramenta** -são as dicas de ferramenta exibidas constantemente.
- **Recalcula o Loop de modo de exibição** -é a ordem de exibição recalculada antes que a janela é desenhada.
- **Espaços**, **exposição** e **ciclo** -todos definem como a janela se comporta nesses ambientes do macOS. 
- **Tela inteira** -determina se essa janela pode entrar no modo de tela inteira. 
- **Animação** -controla o tipo de animação disponível para a janela.
- **Aparência** -controla a aparência da janela. No momento, há apenas uma aparência, azul-piscina.

Consulte da Apple [Introdução ao Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1) e [NSWindow](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSWindow_Class/index.html#//apple_ref/occ/cl/NSWindow) documentação para obter mais detalhes.

<a name="Setting_the_Default_Size_and_Location" />

### <a name="setting-the-default-size-and-location"></a>Definindo o tamanho e local padrão

Para definir a posição inicial da sua janela e controlar seu tamanho, alterne para o **Inspetor de tamanho**:

[![](window-images/edit07.png "O tamanho e local padrão")](window-images/edit07.png#lightbox)

A partir daqui, você pode definir o tamanho inicial da janela, dê a ele um tamanho mínimo e máximo, definir o local inicial na tela e controlar as bordas ao redor da janela.

<a name="Setting-a-Custom-Main-Window-Controller" />

### <a name="setting-a-custom-main-window-controller"></a>Configurar um controlador de janela principal personalizada

Para poder criar saídas e ações para expor elementos de interface do usuário ao código c#, o aplicativo xamarin. Mac precisará estar usando um controlador de janela personalizada.

Faça o seguinte:

1. Abra o Storyboard do aplicativo no Interface Builder do Xcode.
2. Selecione o `NSWindowController` na superfície de Design.
3. Alterne para o **Inspetor de identidade** exiba e insira `WindowController` como o **nome da classe**: 

    [![](window-images/windowcontroller01.png "Configurando o nome de classe")](window-images/windowcontroller01.png#lightbox)
4. Salve suas alterações e retorne ao Visual Studio para Mac para sincronizar.
5. Um `WindowController.cs` arquivo será adicionado ao seu projeto na **Gerenciador de soluções** no Visual Studio para Mac: 

    [![](window-images/windowcontroller02.png "Selecionar o controlador do windows")](window-images/windowcontroller02.png#lightbox)
6. Reabra o Storyboard no Interface Builder do Xcode.
7. O `WindowController.h` arquivo estará disponível para uso: 

    [![](window-images/windowcontroller03.png "Editando o arquivo WindowController.h")](window-images/windowcontroller03.png#lightbox)

<a name="Adding_UI_Elements" />

### <a name="adding-ui-elements"></a>Adicionando elementos de interface do usuário

Para definir o conteúdo de uma janela, arraste os controles do **Inspetor de biblioteca** até a **Editor de Interface**. Consulte nosso [Introdução ao Interface Builder e Xcode](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) documentação para obter mais informações sobre como usar o Interface Builder para criar e ativar os controles.

Por exemplo, vamos arrastar uma barra de ferramentas a **Inspetor de biblioteca** para a janela na **Editor de Interface**:

[![](window-images/edit03.png "Selecionando uma barra de ferramentas da biblioteca")](window-images/edit03.png#lightbox)

Em seguida, arraste uma **exibição de texto** e dimensione-o para preencher a área sob a barra de ferramentas:

[![](window-images/edit04.png "Adicionando uma exibição de texto")](window-images/edit04.png#lightbox)

Como queremos que o **exibição de texto** para reduzir e crescer conforme as alterações de tamanho da janela, vamos mudar para o **Editor de restrição de** e adicione as seguintes restrições:

[![](window-images/edit05.png "Restrições de edição")](window-images/edit05.png#lightbox)

Clicando o para **I vermelhas** na parte superior do editor e clicar em **adicionar 4 restrições**, estamos informando o modo de exibição de texto para continuar com o determinado X, Y coordenadas e aumentar ou reduzir horizontalmente e verticalmente como a janela é redimensionada.

Por fim, vamos expor os **exibição de texto** para o código usando uma **tomada** (certificando-se de selecionar o `ViewController.h` arquivo):

[![](window-images/edit06.png "Configurando uma saída")](window-images/edit06.png#lightbox)

Salve suas alterações e voltar para o Visual Studio para Mac sincronizar com o Xcode.

Para obter mais informações sobre como trabalhar com **tomadas** e **ações**, consulte nosso [tomada e ação](~/mac/get-started/hello-mac.md#outlets-and-actions) documentação.

<a name="Standard_Window_Workflow" />

### <a name="standard-window-workflow"></a>Fluxo de trabalho de janela padrão

Para qualquer janela que você criar e trabalhar com seu aplicativo xamarin. Mac, o processo é basicamente o mesmo que o que estamos apenas ter feito acima:

1. Para o novo windows que não são o padrão adicionado automaticamente ao seu projeto, adicione uma nova definição de janela para o projeto. Isso será discutido em detalhes abaixo.
2. Clique duas vezes o `Main.storyboard` arquivo para abrir o design de janela para edição no Interface Builder do Xcode.
3. Arraste uma nova janela para o design da Interface do usuário e a janela de gancho na janela principal usando _transições_ (para obter mais informações, consulte o [transições](~/mac/platform/storyboards/indepth.md#Segues) seção do nosso [trabalhar com Storyboards](~/mac/platform/storyboards/indepth.md) documentação).
3. Definir propriedades de qualquer janela necessária na **Inspetor de atributo** e o **Inspetor de tamanho**.
4. Arraste os controles necessários para compilar sua interface e configurá-los de **Inspetor de atributo**.
5. Use o **Inspetor de tamanho** para lidar com o redimensionamento para elementos de interface do usuário.
6. Expor elementos de interface do usuário da janela ao código c# por meio **tomadas** e **ações**.
7. Salve suas alterações e voltar para o Visual Studio para Mac sincronizar com o Xcode.

Agora que temos uma janela básica criada, vamos examinar os processos típicos um xamarin. Mac aplicativo faz ao trabalhar com o windows. 

<a name="Displaying_the_Default_Window" />

## <a name="displaying-the-default-window"></a>Exiba a janela padrão

Por padrão, um novo aplicativo xamarin. Mac automaticamente exibirá a janela definida no `MainWindow.xib` arquivo quando ele é iniciado:

[![](window-images/display01.png "Uma janela de exemplo em execução")](window-images/display01.png#lightbox)

Uma vez que modificamos o design dessa janela acima, agora ele inclui uma barra de ferramentas padrão e **exibição de texto** controle. A seguinte seção no `Info.plist` arquivo é responsável por exibir essa janela:

[![](window-images/display00.png "Edição de info. plist")](window-images/display00.png#lightbox)

O **Interface principal** dropdown é usado para selecionar o Storyboard que será usado como o interface do usuário do aplicativo principal (nesse caso, `Main.storyboard`).

Um controlador de exibição é adicionado automaticamente ao projeto para controlar o que Windows principal que é exibido (juntamente com sua exibição primária). Ela é definida na `ViewController.cs` registrados e serão anexados ao **o proprietário do arquivo** no construtor de Interface no **Inspetor de identidade**:

[![](window-images/display02.png "Definir o proprietário do arquivo")](window-images/display02.png#lightbox)

Para nossa janela, gostaríamos que ele tenha um título de `untitled` quando ela abre primeiro vamos substituição a `ViewWillAppear` método o `ViewController.cs` para se parecer com o seguinte:

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set Window Title
    this.View.Window.Title = "untitled";
}
``` 

> [!NOTE]
> Definimos o valor da janela do `Title` propriedade em de `ViewWillAppear` método em vez do `ViewDidLoad` método porque, enquanto o modo de exibição pode ser carregado na memória, ele não seja ainda totalmente instanciado. Se tentar acessar o `Title` propriedade no `ViewDidLoad` método, teríamos uma `null` exceção, pois a janela ainda não foi construída e com fio-up à propriedade ainda.

<a name="Programmatically_Closing_a_Window" />

## <a name="programmatically-closing-a-window"></a>Programaticamente, fechar uma janela

Pode haver ocasiões em que você deseja fechar uma janela em um aplicativo xamarin. MAC de forma programática, exceto ter o usuário clique a janela **feche** botão ou usando um item de menu. macOS fornece duas maneiras diferentes para fechar um `NSWindow` por meio de programação: `PerformClose` e `Close`.

<a name="PerformClose" />

### <a name="performclose"></a>PerformClose

Chamar o `PerformClose` método de um `NSWindow` simula o usuário clicar na janela **fechar** botão momentaneamente realçando o botão e, em seguida, fechar a janela.

Se o aplicativo implementa o `NSWindow`do `WillClose` evento, ele será gerado antes que a janela é fechada. Se o evento retorna `false`, em seguida, a janela não será fechada. Se a janela não tem um **fechar** botão ou não pode ser fechada por qualquer motivo, o sistema operacional emitirá o som de alerta.

Por exemplo:

```csharp
MyWindow.PerformClose(this);
```

Tentar fechar o `MyWindow` `NSWindow` instância. Se tiver êxito, a janela será fechada, caso contrário o som de alerta será emitido e o precisará permanecer aberta.

<a name="Close" />

### <a name="close"></a>Fechar

Chamar o `Close` método de um `NSWindow` não simula o usuário clicar na janela **fechar** botão momentaneamente realçando o botão, ele simplesmente fecha a janela.

Uma janela não precisa ser visível a ser fechado e um `NSWindowWillCloseNotification` notificação será postada no Centro de notificação padrão para a janela que está sendo fechada.

O `Close` método difere de duas maneiras importantes do `PerformClose` método:

1. Não tente acionar o `WillClose` eventos.
2. Ele não simula o usuário clicando o **fechar** botão por momentaneamente realçando o botão.

Por exemplo:

```csharp
MyWindow.Close();
```

Seria para fechar o `MyWindow` `NSWindow` instância.

<a name="Modified-Windows-Content" />

## <a name="modified-windows-content"></a>Conteúdo modificado do Windows

No macOS, Apple forneceu uma maneira de informar ao usuário que o conteúdo de uma janela (`NSWindow`) foi modificado pelo usuário e precisa ser salvo. Se a janela contém conteúdo modificado, um pequeno ponto preto será exibido no seu **fechar** widget:

[![](window-images/close01.png "Uma janela com o marcador modificado")](window-images/close01.png#lightbox)

Se o usuário tenta fechar a janela ou sair do aplicativo Mac enquanto houver não salvas altera ao conteúdo da janela, você deve apresentar uma [caixa de diálogo](~/mac/user-interface/dialog.md) ou [folha Modal](~/mac/user-interface/dialog.md) e permitir que o usuário salvar suas alterações primeiro:

[![](window-images/close02.png "Salvar folha que está sendo mostrada quando a janela é fechada")](window-images/close02.png#lightbox)

### <a name="marking-a-window-as-modified"></a>Marcando uma janela como modificado

Para marcar uma janela como tendo modificado conteúdo, use o seguinte código:

```csharp
// Mark Window content as modified
Window.DocumentEdited = true;
```

E, depois que a alteração tiver sido salvo, limpar o sinalizador modificado usando:

```csharp
// Mark Window content as not modified
Window.DocumentEdited = false;
```

### <a name="saving-changes-before-closing-a-window"></a>Salvar as alterações antes de fechar uma janela

Para assistir a para o usuário fechar uma janela e permitindo que eles salvar o conteúdo modificado com antecedência, você precisará criar uma subclasse de `NSWindowDelegate` e substituir seu `WindowShouldClose` método. Por exemplo:

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

Use o código a seguir para anexar a uma instância desse delegado à sua janela:

```csharp
// Set delegate
Window.Delegate = new EditorWindowDelegate(Window);
```

### <a name="saving-changes-before-closing-the-app"></a>Salvar as alterações antes de fechar o aplicativo

Por fim, o App Xamarin.Mac deve verificar para ver se qualquer um dos seus Windows contêm o conteúdo modificado e permitir que o usuário salvar as alterações antes de sair. Para fazer isso, edite sua `AppDelegate.cs` do arquivo, substituir o `ApplicationShouldTerminate` método e torná-lo semelhante ao seguinte:

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

<a name="Working_with_Multiple_Windows" />

## <a name="working-with-multiple-windows"></a>Trabalhando com vários Windows

A maioria dos aplicativos do Mac de documento com base pode editar vários documentos ao mesmo tempo. Por exemplo, um editor de texto pode ter vários arquivos de texto aberto para edição ao mesmo tempo. Por padrão, nosso novo aplicativo xamarin. MAC tem um **arquivo** menu com um **New** item automaticamente com fio-up para o `newDocument:` **ação**.

Vamos ativar esse novo item e permitir que o usuário abra várias cópias de nossa janela principal para editar vários documentos ao mesmo tempo.

Vamos Editar nosso `AppDelegate.cs` arquivo e adicione o seguinte a propriedade computada:

```csharp
public int UntitledWindowCount { get; set;} =1;
```

Usaremos isso para controlar o número de arquivos não salvos, portanto, damos comentários ao usuário (de acordo com as diretrizes da Apple, conforme discutido acima).

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

Esse código cria uma nova versão do nosso controlador da janela, carrega a nova janela, torna o principal e a janela de chave e define-o título. Agora, se vamos executar nosso aplicativo e selecionar **New** da **arquivo** menu uma nova janela do editor será aberta e exibida:

[![](window-images/display04.png "Foi adicionada a uma nova janela sem título")](window-images/display04.png#lightbox)

Se abrimos o **Windows** menu, você pode ver o aplicativo é automaticamente de controle e manipulação nossas janelas abertas:

[![](window-images/display05.png "Menu do windows")](window-images/display05.png#lightbox)

Para obter mais informações sobre como trabalhar com Menus em um aplicativo xamarin. Mac, consulte nosso [trabalhando com Menus](~/mac/user-interface/menu.md) documentação.

<a name="Getting_the_Currently_Active_Window" />

### <a name="getting-the-currently-active-window"></a>Introdução a janela ativa no momento

Em um aplicativo xamarin. MAC que pode abrir várias janelas (documentos), há vezes quando você precisa obter a janela superior atual (a janela de chave). O código a seguir retornará a janela de chave:

```csharp
var window = NSApplication.SharedApplication.KeyWindow;
```

Ele pode ser chamado em qualquer classe ou método que precisa para acessar a janela atual, key. Se nenhuma janela estiver aberta, ele retornará `null`.

<a name="Accessing-All-App-Windows" />

### <a name="accessing-all-app-windows"></a>Acessando todos os Windows do aplicativo

Pode haver ocasiões em que você precisa para acessar todas as janelas que seu aplicativo xamarin. MAC tem atualmente aberto. Por exemplo ver se um arquivo que o usuário deseja abrir ainda estiver aberto em uma janela existente.

O `NSApplication.SharedApplication` mantém uma `Windows` propriedade que contém uma matriz de todas as janelas abertas em seu aplicativo. Você pode iterar sobre essa matriz para acessar todas as janelas abertas do aplicativo. Por exemplo:

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

No código de exemplo, podemos está convertendo cada janela retornada personalizadas `ViewController` classe no nosso aplicativo e a testar o valor de um personalizado `Path` propriedade com o caminho de um arquivo que o usuário deseja abrir. Se o arquivo já estiver aberto, traz a janela para a frente.

<a name="Adjusting_the_Window_Size_in_Code" />

## <a name="adjusting-the-window-size-in-code"></a>Ajustando o tamanho da janela no código

Há horários em que o aplicativo precisa para redimensionar a janela no código. Para redimensionar e reposicionar uma janela, ajustá-lo do `Frame` propriedade. Ao ajustar o tamanho da janela, você geralmente precisará também ajustar sua origem, para manter a janela no mesmo local devido ao sistema de coordenadas do macOS.

Ao contrário do iOS em que o canto superior esquerdo representa (0,0), o macOS usa um sistema de coordenadas matemáticos em que o canto inferior esquerdo da tela representa (0,0). No iOS as coordenadas aumentam à medida que você mover para baixo em direção à direita. No macOS, as coordenadas de aumentam no valor para cima à direita. 

O exemplo de código a seguir redimensiona uma janela:

```csharp
nfloat y = 0;

// Calculate new origin
y = Frame.Y - (768 - Frame.Height);

// Resize and position window
CGRect frame = new CGRect (Frame.X, y, 1024, 768);
SetFrame (frame, true);
```

> [!IMPORTANT]
> Quando você ajustar um tamanho de windows e o local no código, você precisa certificar-se de que você respeite os tamanhos mínimo e máximo que você definiu no Interface Builder. Isso não serão automaticamente respeitado e você poderá deixar a janela maior ou menor do que esses limites.

<a name="Monitoring-Window-Size-Changes" />

## <a name="monitoring-window-size-changes"></a>Monitoramento de alterações de tamanho de janela

Pode haver ocasiões em que você precisará monitorar alterações no tamanho da janela dentro de seu aplicativo xamarin. Mac. Por exemplo, para redesenhar o conteúdo de acordo com o novo tamanho.

Para monitorar as alterações de tamanho, primeiro certifique-se de que você tenha atribuído uma classe personalizada para o controlador de janela no Interface Builder do Xcode. Por exemplo, `MasterWindowController` a seguir:

[![](window-images/resize01.png "O Inspetor de identidade")](window-images/resize01.png#lightbox)

Em seguida, edite a classe do controlador de janela personalizados e o monitor de `DidResize` evento na janela do controlador para ser notificado sobre alterações de tamanho ao vivo. Por exemplo:

```csharp
public override void WindowDidLoad ()
{
    base.WindowDidLoad ();

    Window.DidResize += (sender, e) => {
        // Do something as the window is being live resized
    };
}
```

Opcionalmente, você pode usar o `DidEndLiveResize` evento a ser notificado somente depois que o usuário termina de alterar o tamanho da janela. Por exemplo:

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

<a name="Setting_a_Window’s_Title_and_Represented_File" />

## <a name="setting-a-windows-title-and-represented-file"></a>Definir uma janela do título e representado arquivo

Ao trabalhar com o windows que representam documentos `NSWindow` tem uma `DocumentEdited` propriedade que, se definida como `true` exibe um pequeno ponto no botão Fechar para dar ao usuário uma indicação de que o arquivo foi modificado e deve ser salvo antes de ser fechado.

Vamos Editar nosso `ViewController.cs` de arquivo e faça as seguintes alterações:

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

Também estamos monitorando o `WillClose` evento na janela e verificar o estado do `DocumentEdited` propriedade. Se for `true` precisamos fornecer ao usuário a capacidade de salvar as alterações no arquivo. Se podemos executar nosso aplicativo e digite algum texto, o ponto será exibido:

[![](window-images/file01.png "Uma janela alterada")](window-images/file01.png#lightbox)

Se você tentar fechar a janela, obtemos um alerta:

[![](window-images/file02.png "Exibindo um salvamento caixa de diálogo")](window-images/file02.png#lightbox)

Se estamos carregando um documento de um arquivo podemos definir o título da janela para o arquivo nome usando o `window.SetTitleWithRepresentedFilename (Path.GetFileName(path));` método (considerando que `path` é uma cadeia de caracteres que representa o arquivo que está sendo aberto). Além disso, podemos definir a URL do arquivo usando o `window.RepresentedUrl = url;` método.

Se a URL está apontando para um tipo de arquivo conhecido pelo sistema operacional, seu ícone será exibido na barra de título. Se o usuário clica no ícone, o caminho para o arquivo será mostrado.

Vamos editar o `AppDelegate.cs` arquivo e adicione o seguinte método:

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

Agora, se executarmos nosso aplicativo, selecione **abra...**  do **arquivo** menu, selecione um arquivo de texto dos **abrir** caixa de diálogo caixa e abri-lo:

[![](window-images/file03.png "Uma caixa de diálogo Abrir")](window-images/file03.png#lightbox)

O arquivo será exibido e o título será definido com o ícone do arquivo:

[![](window-images/file04.png "O conteúdo de um arquivo carregado")](window-images/file04.png#lightbox)

<a name="Adding_a_New_Window_to_a_Project" />

## <a name="adding-a-new-window-to-a-project"></a>Adicionando uma nova janela a um projeto

Além da janela do documento principal, talvez seja necessário exibir outros tipos de janelas para o usuário, como preferências ou painéis do Inspetor de um aplicativo xamarin. Mac.

Para adicionar uma nova janela, faça o seguinte:

1. No **Gerenciador de soluções**, clique duas vezes o `Main.storyboard` arquivo para abri-lo para edição no Interface Builder do Xcode.
2. Arraste um novo **controlador da janela** da **biblioteca** e solte-a no **superfície de Design**:

    [![](window-images/new01.png "Selecionando um novo controlador de janela na biblioteca")](window-images/new01.png#lightbox)
3. No **Inspetor de identidade**, insira `PreferencesWindow` para o **ID do Storyboard**: 

    [![](window-images/new02.png "Definir a ID do storyboard")](window-images/new02.png#lightbox)
5. Sua interface de design: 

    [![](window-images/new03.png "Projetando a interface do usuário")](window-images/new03.png#lightbox)
6. Abra o Menu de aplicativo (`MacWindows`), selecione **preferências...** , CTRL + clique e arraste para a nova janela: 

    [![](window-images/new05.png "Criando um segue")](window-images/new05.png#lightbox)
7. Selecione **Mostrar** no menu pop-up.
6. Salve suas alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Se vamos executar o código e selecionar o **preferências...**  do **Menu aplicativo**, a janela será exibida:

[![](window-images/new04.png "Um menu de preferências de exemplo")](window-images/new04.png#lightbox)

<a name="Working_with_Panels" />

## <a name="working-with-panels"></a>Trabalhando com painéis

Conforme mencionado no início deste artigo, um painel flutua sobre outras janelas e fornece as ferramentas ou controles que os usuários podem trabalhar com enquanto documentos estiverem abertos. 

Assim como qualquer outro tipo de janela que você cria e trabalhar com em seu aplicativo xamarin. Mac, o processo é basicamente o mesmo:

1. Adicione uma nova definição de janela para o projeto.
2. Clique duas vezes o `.xib` arquivo para abrir o design de janela para edição no Interface Builder do Xcode.
2. Definir propriedades de qualquer janela necessária na **Inspetor de atributo** e o **Inspetor de tamanho**.
4. Arraste os controles necessários para compilar sua interface e configurá-los de **Inspetor de atributo**.
5. Use o **Inspetor de tamanho** para lidar com o redimensionamento para elementos de interface do usuário.
6. Expor elementos de interface do usuário da janela ao código c# por meio **tomadas** e **ações**.
7. Salve suas alterações e voltar para o Visual Studio para Mac sincronizar com o Xcode.

No **Inspetor de atributo**, você tem as seguintes opções específicas para painéis:

[![](window-images/panel03.png "O Inspetor de atributos")](window-images/panel03.png#lightbox)

- **Estilo** -permitem que você ajuste o estilo do painel de: painel Regular (parecidas com uma janela padrão), o painel do utilitário (tem uma barra de título menor), painel HUD (é translúcida e a barra de título é parte do plano de fundo).
- **Ativando não** -determina no painel se torna a janela de chave.
- **Documentar Modal** -se documento Modal, o painel só flutua sobre janelas do aplicativo, caso contrário ela flutua acima de tudo.


Para adicionar um novo painel, faça o seguinte:

1. No **Gerenciador de soluções**, clique com botão direito no projeto e selecione **Add** > **novo arquivo...** .
2. Na caixa de diálogo Novo arquivo, selecione **xamarin. Mac** > **janela do Cocoa com controlador**:

    [![](window-images/panels00.png "Adicionando um novo controlador de janela")](window-images/panels00.png#lightbox)
3. Digite `DocumentPanel` para o **Nome** e clique no botão **Novo**.
4. Clique duas vezes o `DocumentPanel.xib` arquivo para abri-lo para edição no Interface Builder: 

    [![](window-images/new02.png "O painel de edição")](window-images/new02.png#lightbox)
5. Excluir a janela existente e arraste um painel do **Inspetor de biblioteca** na **Editor de Interface**: 

    [![](window-images/panels01.png "Excluindo a janela existente")](window-images/panels01.png#lightbox)
6. O painel de gancho até a **o proprietário do arquivo** - **janela** - **tomada**: 

    [![](window-images/panels02.png "Arrastando para ligar o painel")](window-images/panels02.png#lightbox)
7. Alterne para o **Inspetor de identidade** e defina a classe do painel para `DocumentPanel`: 

    [![](window-images/panels03.png "Classe do painel de configuração")](window-images/panels03.png#lightbox)
6. Salve suas alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.
7. Editar o `DocumentPanel.cs` de arquivo e altere a definição de classe para o seguinte: 

    `public partial class DocumentPanel : NSPanel`
8. Salve as alterações no arquivo.

Editar o `AppDelegate.cs` do arquivo e verifique o `DidFinishLaunching` método são semelhantes ao seguinte:

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
        // Display panel
    var panel = new DocumentPanelController ();
    panel.Window.MakeKeyAndOrderFront (this);
}

```

Se executarmos nosso aplicativo, o painel será exibido:

[![](window-images/panels04.png "O painel em um aplicativo em execução")](window-images/panels04.png#lightbox)

> [!IMPORTANT]
> Painel Windows foram substituídos pela Apple e deve ser substituídos por **Inspetor de Interfaces**. Para obter um exemplo completo de criação de um **Inspector** em um aplicativo xamarin. Mac, consulte nosso [MacInspector](https://developer.xamarin.com/samples/mac/MacInspector/) aplicativo de exemplo.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo apresentou uma visão detalhada de como trabalhar com o Windows e painéis em um aplicativo xamarin. Mac. Podemos viu os diferentes tipos e usos de Windows e painéis, como criar e manter o Windows e painéis no Interface Builder do Xcode e como trabalhar com o Windows e painéis em código c#.

## <a name="related-links"></a>Links relacionados

- [MacWindows (amostra)](https://developer.xamarin.com/samples/mac/MacWindows/)
- [MacInspector (amostra)](https://developer.xamarin.com/samples/mac/MacInspector/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabalhando com Menus](~/mac/user-interface/menu.md)
- [Diretrizes de interface humana do OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introdução ao Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
