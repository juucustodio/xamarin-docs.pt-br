---
title: Windows
description: Este artigo aborda a trabalhar com janelas e painéis em um aplicativo Xamarin.Mac. Descreve Criando janelas e painéis em Xcode e construtor de Interface, carregá-las do storyboards e .xib arquivos e trabalhar com eles por meio de programação.
ms.prod: xamarin
ms.assetid: 4F6C67E9-BBFF-44F7-B29E-AB47D7F44287
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: f45bc69b74d98c7b9130f2caeaee91b184c38d87
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="windows"></a>Windows

_Este artigo aborda a trabalhar com janelas e painéis em um aplicativo Xamarin.Mac. Descreve Criando janelas e painéis em Xcode e construtor de Interface, carregá-las do storyboards e .xib arquivos e trabalhar com eles por meio de programação._

Ao trabalhar com c# e .NET em um aplicativo de Xamarin.Mac, você tem acesso para as mesmas janelas e painéis que um desenvolvedor trabalhando em *Objective-C* e *Xcode* does. Como Xamarin.Mac se integra diretamente com o Xcode, você pode usar do Xcode _Interface construtor_ para criar e manter o Windows e os painéis (ou, opcionalmente, criá-los diretamente no código do c#).

Com base na sua finalidade, um aplicativo Xamarin.Mac pode apresentar uma ou mais janelas na tela para gerenciar e coordenar as informações que ele exibe e funciona com. As principais funções de uma janela são:

1. Para fornecer uma área na qual controles e modos de exibição podem ser colocados e gerenciados.
2. Para aceitar e responder a eventos em resposta à interação do usuário com o teclado e mouse.

Windows pode ser usado em um estado sem janela restrita (como um editor de texto que pode ter vários documentos abertos simultaneamente) ou restrita (como uma caixa de diálogo de exportação que deve ser descartada antes que o aplicativo pode continuar).

Painéis são um tipo especial de janela (uma subclasse da base de `NSWindow` classe), que normalmente servem para uma função auxiliar em um aplicativo, como windows utilitário como inspetores de formato de texto e o seletor de cores do sistema.

[![](window-images/intro01.png "Edição de uma janela no Xcode")](window-images/intro01.png#lightbox)

Neste artigo, vamos abordar os fundamentos de trabalhar com janelas e painéis em um aplicativo Xamarin.Mac. É altamente recomendável que você leia o [Hello, Mac](~/mac/get-started/hello-mac.md) artigo primeiro, especificamente o [Introdução ao construtor da Interface e Xcode](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) e [tomadas e ações](~/mac/get-started/hello-mac.md#Outlets_and_Actions) seções, como ele aborda os principais conceitos e técnicas que será usado neste artigo.

Talvez você queira dar uma olhada a [classes expondo c# / métodos para Objective-C](~/mac/internals/how-it-works.md) seção o [Xamarin.Mac internos](~/mac/internals/how-it-works.md) de documento, ele explica o `Register` e `Export` comandos usado com a transmissão suas classes c# objetos Objective-C e elementos de interface do usuário.

<a name="Introduction_to_Windows" />

## <a name="introduction-to-windows"></a>Introdução ao Windows

Como mencionado acima, uma janela fornece uma área na qual controles e modos de exibição podem ser colocados e gerenciados e responde a eventos com base na interação do usuário (seja por meio de teclado ou mouse).

De acordo com a Apple, há cinco tipos principais do Windows em um aplicativo macOS:

- **Janela do documento** -uma janela de documento contém dados de usuário baseado em arquivo como uma planilha ou um documento de texto.
- **Janela de aplicativo** -uma janela do aplicativo é a janela principal de um aplicativo que não é baseado em documento (como o aplicativo de calendário em um Mac).
- **Painel** - um painel flutua sobre outras janelas e fornece ferramentas ou controles que os usuários podem trabalhar com enquanto documentos estiverem abrir. Em alguns casos, um painel pode ser transparente (por exemplo, ao trabalhar com gráficos grandes).
- **Caixa de diálogo** -uma caixa de diálogo aparece em resposta a uma ação do usuário e geralmente fornece aos usuários de maneiras podem concluir a ação. Uma caixa de diálogo requer uma resposta do usuário antes de ser encerrado. (Consulte [trabalhando com caixas de diálogo](~/mac/user-interface/dialog.md))
- **Alertas** -um alerta é um tipo especial de caixa de diálogo que aparece quando ocorre um problema sério (como um erro) ou como um aviso (como preparação para excluir um arquivo). Como um alerta é uma caixa de diálogo, ele também requer uma resposta do usuário antes de ser encerrado. (Consulte [trabalhando com alertas](~/mac/user-interface/alert.md))

Para obter mais informações, consulte o [sobre o Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1) seção da Apple [diretrizes de Interface Humana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Main_Key_and_Inactive_Windows" />

### <a name="main-key-and-inactive-windows"></a>Principal, a chave e o Windows inativo

Windows em um aplicativo Xamarin.Mac pode pesquisar e se comportam de maneira diferente com base em como o usuário está atualmente interagir com eles. Principal documento ou janela de aplicativo que é atualmente o foco de atenção do usuário é chamada de _janela principal_. Na maioria dos casos essa janela também será o _janela chave_ (a janela que atualmente está aceitando entrada do usuário). Mas isso nem sempre é o caso, por exemplo, um seletor de cores pode ser aberta e ser a janela de chave que o usuário está interagindo com para alterar o estado de um item na janela do documento (que ainda será a janela principal).

O principal e o Windows de chave (se forem separados) são sempre ativa, _Windows inativo_ são janelas abertas que não estão em primeiro plano. Por exemplo, um aplicativo de editor de texto pode ter mais de um documento aberto ao mesmo tempo, somente a janela principal estiver ativa, todos os outros seria inativos. 

Para obter mais informações, consulte o [sobre o Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1) seção da Apple [diretrizes de Interface Humana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Naming_Windows" />

### <a name="naming-windows"></a>Nomenclatura do Windows

Uma janela pode exibir uma barra de título e quando o título é exibido, é normalmente o nome do aplicativo, o nome do documento que está sendo trabalhado ou a função de janela (como Inspetor). Alguns aplicativos não exibem uma barra de título, porque eles são reconhecidos pelo visíveis e não funcionam com documentos.

Apple sugerem as diretrizes a seguir:

- Use o nome do aplicativo para o título de uma janela principal, não do documento. 
- Nome de uma nova janela de documento `untitled`. Para o novo documento, não acrescentar um número para o título (como `untitled 1`). Se o usuário criar outro novo documento antes de salvar e títulos primeiro, chame essa janela `untitled 2`, `untitled 3`, etc.

Para obter mais informações, consulte o [Windows nomenclatura](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowNaming.html#//apple_ref/doc/uid/20000957-CH35-SW1) seção da Apple [diretrizes de Interface Humana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Full-Screen_Windows" />

### <a name="full-screen-windows"></a>Janelas de tela inteira

Em macOS, janela de um aplicativo pode ir ocultação de tela inteira tudo, incluindo a barra de menus do aplicativo (que pode ser reveladas movendo o cursor para a parte superior da tela) para fornecer distração interação livre é de conteúdo.

Apple sugere as diretrizes a seguir:

- Determine se ele faz sentido para uma janela para exibir na tela inteira. Aplicativos que fornecem breves interações (como uma calculadora) não devem fornecer um modo de tela inteira.
- Mostre barra de ferramentas se a tarefa de tela inteira exija isso. Normalmente, a barra de ferramentas está oculto no modo de tela inteira.
- A janela de tela inteira deve ter todos os usuários de recursos necessário concluir a tarefa.
- Se possível, evite a interação de localizador enquanto o usuário está em uma janela de tela inteira.
- Aproveite o aumento do espaço da tela sem alternar o foco para fora da tarefa principal.

Para obter mais informações, consulte o [janelas de tela inteira](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/FullScreen.html#//apple_ref/doc/uid/20000957-CH61-SW1) seção da Apple [diretrizes de Interface Humana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Panels" />

### <a name="panels"></a>Painéis

Um painel é uma janela do auxiliar que contém os controles e opções que afetam o documento ativo ou a seleção (como o seletor de cores do sistema):

[![](window-images/panel01.png "Um painel de cores")](window-images/panel01.png#lightbox)

Painéis podem ser _específico do aplicativo_ ou _todo o sistema_. Painéis de específico do aplicativo float na parte superior do aplicativo janelas de documento e desaparecem quando o aplicativo está em segundo plano. Painéis de todo o sistema (como o **fontes** painel), float sobre todas as janelas abertas, independentemente do aplicativo. 

Apple sugere as diretrizes a seguir:

- Em geral, use um painel padrão, os painéis transparentes só devem ser usados com moderação e tarefas com muitos elementos gráficos.
- Considere o uso de um painel forneça aos usuários acesso fácil a informações que afeta diretamente a tarefa ou controles importantes.
- Ocultar e Mostrar painéis conforme necessário.
- Painéis sempre devem incluir a barra de título.
- Painéis não devem incluir um botão de minimização ativo.

#### <a name="inspectors"></a>inspetores

Os aplicativos mais modernos de macOS apresentam controles auxiliares e opções que afetam o documento ativo ou a seleção como _inspetores_ que fazem parte da janela principal (como o **páginas** aplicativo mostrado abaixo), em vez de usar o painel de Windows:

[![](window-images/panel02.png "Um Inspetor de exemplo")](window-images/panel02.png#lightbox)

Para obter mais informações, consulte o [painéis](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowPanels.html#//apple_ref/doc/uid/20000957-CH42-SW1) seção da Apple [diretrizes de Interface humana do sistema operacional X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) e nossa [MacInspector](https://developer.xamarin.com/samples/mac/MacInspector/) aplicativo de exemplo para uma implantação completa de um **Inspetor Interface** em um aplicativo Xamarin.Mac.

<a name="Creating_and_Maintaining_Windows_in_Xcode" />

## <a name="creating-and-maintaining-windows-in-xcode"></a>Criação e manutenção do Windows no Xcode

Quando você cria um novo aplicativo de Xamarin.Mac Cocoa, obtém uma janela em branco, padrão por padrão. Esse windows é definido em um `.storyboard` arquivo incluído automaticamente no projeto. Para editar o design do windows, o **Gerenciador de soluções**, clique duas vezes o `Main.storyboard` arquivo:

[![](window-images/edit01.png "Selecionando o storyboard principal")](window-images/edit01.png#lightbox)

Isso abrirá o design de janela no construtor de Interface do Xcode:

[![](window-images/edit02.png "Editando a interface do usuário no Xcode")](window-images/edit02.png#lightbox)

No **Inspetor de atributo**, há várias propriedades que você pode usar para definir e controlar sua janela:

- **Título** -este é o texto que será exibido na barra de título da janela.
- **Salvamento automático** -este é o _chave_ que será usado para a ID de janela quando sua posição e as configurações são salvas automaticamente.
- **Barra de título** -a janela exibir uma barra de título.
- **Unified título e a barra de ferramentas** - se a janela inclui uma barra de ferramentas, ele deve ser parte da barra de título.
- **Total de exibição de conteúdo em tamanho** -permite que a área de conteúdo da janela para estar sob a barra de título.
- **Sombra** -janela tem uma sombra.
- **Textura** -windows texturizadas pode usar efeitos (como valorize) e pode ser movidos pela arrastando em qualquer lugar em seu corpo.
- **Fechar** -janela tem um botão Fechar.
- **Minimizar** -janela tem um botão de minimização.
- **Redimensionar** -janela tem um controle de redimensionamento.
- **Botão de barra de ferramentas** -janela tem um botão Mostrar/ocultar.
- **Restauráveis** -é a janela posição e configurações automaticamente salvo e restaurado.
- **Visível no início** -é a janela automaticamente mostrada quando o `.xib` arquivo é carregado.
- **Ocultar ao desativar** -janela é ocultado quando o aplicativo entra em segundo plano.
- **Versão quando fechado** -é a janela removida da memória quando ele é fechado.
- **Sempre exibir dicas de ferramenta** -são as dicas de ferramentas constantemente exibidas.
- **Recalcula o Loop de exibição** -é a ordem de exibição recalculada antes que a janela é desenhada.
- **Espaços**, **Exposé** e **ciclo** -todos definem como a janela se comporta nesses ambientes macOS. 
- **Tela cheia** -determina se esta janela pode entrar no modo de tela inteira. 
- **Animação** -controla o tipo de animação disponível para a janela.
- **Aparência** -controla a aparência da janela. Agora, há apenas uma aparência, azul-piscina.

Consulte da Apple [Introdução ao Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1) e [NSWindow](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSWindow_Class/index.html#//apple_ref/occ/cl/NSWindow) documentação para obter mais detalhes.

<a name="Setting_the_Default_Size_and_Location" />

### <a name="setting-the-default-size-and-location"></a>Definindo o tamanho padrão e local

Para definir a posição inicial da janela e controlar seu tamanho, alterne para o **Inspetor de tamanho**:

[![](window-images/edit07.png "O tamanho padrão e o local")](window-images/edit07.png#lightbox)

Aqui você pode definir o tamanho inicial da janela, dê a ele um tamanho mínimo e máximo, o local inicial na tela e controlar as bordas em torno de janela.

<a name="Setting-a-Custom-Main-Window-Controller" />

### <a name="setting-a-custom-main-window-controller"></a>Configurar um controlador personalizado janela principal

Para poder criar tomadas e ações para expor elementos de interface do usuário ao código c#, o aplicativo Xamarin.Mac precisará estar usando um controlador de janela personalizada.

Faça o seguinte:

1. Abra Storyboard do aplicativo no construtor de Interface do Xcode.
2. Selecione o `NSWindowController` na superfície de Design.
3. Alterne para o **Inspetor de identidade** exibir e insira `WindowController` como o **nome da classe**: 

    [![](window-images/windowcontroller01.png "Configurar o nome de classe")](window-images/windowcontroller01.png#lightbox)
4. Salve suas alterações e retornar ao Visual Studio para Mac sincronizar.
5. Um `WindowController.cs` arquivo será adicionado ao seu projeto no **Solution Explorer** no Visual Studio para Mac: 

    [![](window-images/windowcontroller02.png "Selecionando o controlador do windows")](window-images/windowcontroller02.png#lightbox)
6. Reabra o Storyboard no construtor de Interface do Xcode.
7. O `WindowController.h` arquivo estará disponível para uso: 

    [![](window-images/windowcontroller03.png "Editar o arquivo WindowController.h")](window-images/windowcontroller03.png#lightbox)

<a name="Adding_UI_Elements" />

### <a name="adding-ui-elements"></a>A adição de elementos de interface do usuário

Para definir o conteúdo de uma janela, arraste os controles do **biblioteca Inspetor** até o **Editor de Interface**. Confira nosso [Introdução ao construtor da Interface e Xcode](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) documentação para obter mais informações sobre como usar o construtor da Interface para criar e ativar os controles.

Por exemplo, vamos arrastar uma barra de ferramentas a **Inspetor de biblioteca** para a janela no **Editor de Interface**:

[![](window-images/edit03.png "Selecionando uma barra de ferramentas da biblioteca")](window-images/edit03.png#lightbox)

Em seguida, arraste um **exibição de texto** e dimensione-o para preencher a área da barra de ferramentas:

[![](window-images/edit04.png "Adicionando uma exibição de texto")](window-images/edit04.png#lightbox)

Como queremos que o **exibição de texto** para reduzir e crescer à medida que as alterações de tamanho da janela, vamos alternar para o **Editor de restrição** e adicione as seguintes restrições:

[![](window-images/edit05.png "Restrições de edição")](window-images/edit05.png#lightbox)

Clicando o para **vermelhos I-emissões** na parte superior do editor e clicando em **adicionar restrições de 4**, dizemos a exibição de texto para continuar com o determinado X, Y coordenadas e aumentar ou reduzir horizontalmente e verticalmente como a janela é redimensionada.

Por fim, vamos expor o **exibição de texto** para o código usando um **tomada** (certificando-se de selecionar o `ViewController.h` arquivo):

[![](window-images/edit06.png "Configurando uma tomada")](window-images/edit06.png#lightbox)

Salve suas alterações e retorne ao Visual Studio para Mac sincronizar com o Xcode.

Para obter mais informações sobre como trabalhar com **tomadas** e **ações**, consulte nosso [tomada e ação](~/mac/get-started/hello-mac.md#Outlets_and_Actions) documentação.

<a name="Standard_Window_Workflow" />

### <a name="standard-window-workflow"></a>Fluxo de trabalho de janela padrão

Para qualquer janela que você criar e trabalhar com seu aplicativo Xamarin.Mac, o processo é basicamente o mesmo que o que podemos apenas ter feito acima:

1. Para o novo windows que não são o padrão automaticamente adicionado ao seu projeto, adicione uma nova definição de janela para o projeto. Isso será discutido em detalhes abaixo.
2. Clique duas vezes o `Main.storyboard` arquivo para abrir o design de janela para edição no construtor de Interface do Xcode.
3. Arraste uma nova janela para o design da Interface do usuário e conectar-se a janela na janela principal usando _Segues_ (para obter mais informações, consulte o [Segues](~/mac/platform/storyboards/indepth.md#Segues) seção do nosso [trabalhando com Storyboards](~/mac/platform/storyboards/indepth.md) documentação).
3. Definir as propriedades da janela necessária no **Inspetor de atributo** e **Inspetor de tamanho**.
4. Arraste os controles necessários para criar sua interface e configurá-los no **Inspetor de atributo**.
5. Use o **Inspetor de tamanho** para lidar com o redimensionamento para elementos de interface do usuário.
6. Expor elementos de interface do usuário da janela para o código c# via **tomadas** e **ações**.
7. Salve suas alterações e retorne ao Visual Studio para Mac sincronizar com o Xcode.

Agora que temos uma janela básica criada, vamos examinar os processos típicos um Xamarin.Mac aplicativo faz ao trabalhar com o windows. 

<a name="Displaying_the_Default_Window" />

## <a name="displaying-the-default-window"></a>Exibir a janela padrão

Por padrão, um novo aplicativo Xamarin.Mac automaticamente exibirá a janela definida no `MainWindow.xib` arquivo quando ele for iniciado:

[![](window-images/display01.png "Uma janela de exemplo")](window-images/display01.png#lightbox)

Como podemos modificou o design da janela acima, agora ele inclui uma barra de ferramentas padrão e **exibição de texto** controle. A seguinte seção de `Info.plist` arquivo é responsável por exibir essa janela:

[![](window-images/display00.png "Edição de info. plist")](window-images/display00.png#lightbox)

O **Interface principal** suspenso é usado para selecionar o Storyboard que será usado como o aplicativo principal da interface do usuário (neste caso `Main.storyboard`).

Um controlador de exibição é adicionado automaticamente ao projeto para controlar o que o Windows principal que é exibido (junto com sua exibição primária). Está definido no `ViewController.cs` de arquivo e anexado ao **proprietário do arquivo** no construtor de Interface no **Inspetor de identidade**:

[![](window-images/display02.png "Definir o proprietário do arquivo")](window-images/display02.png#lightbox)

Para nossa janela, gostaríamos de receber para que um título de `untitled` quando ele abre pela primeira vez vamos substituição a `ViewWillAppear` método o `ViewController.cs` para se parecer com o seguinte:

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set Window Title
    this.View.Window.Title = "untitled";
}
``` 

> [!NOTE]
> Estamos está definindo o valor da janela de `Title` propriedade no `ViewWillAppear` método em vez do `ViewDidLoad` método porque, enquanto o modo de exibição pode ser carregado na memória, ele não é ainda totalmente instanciado. Se tentar acessar o `Title` propriedade o `ViewDidLoad` método obteremos um `null` exceção desde que a janela ainda não foi construída e com fio-up para a propriedade ainda.

<a name="Programmatically_Closing_a_Window" />

## <a name="programmatically-closing-a-window"></a>Fechamento de uma janela programaticamente

Pode haver momentos em que você deseja fechar programaticamente uma janela em um aplicativo Xamarin.Mac, além de ter o usuário clicar da janela **fechar** botão ou usando um item de menu. macOS fornece duas maneiras diferentes para fechar um `NSWindow` programaticamente: `PerformClose` e `Close`.

<a name="PerformClose" />

### <a name="performclose"></a>PerformClose

Chamando o `PerformClose` método de um `NSWindow` simula o usuário clicar na janela **fechar** botão momentaneamente realçando o botão e, em seguida, fechar a janela.

Se o aplicativo implementa o `NSWindow`do `WillClose` evento, ele será gerado antes que a janela for fechada. Se o evento retorna `false`, em seguida, a janela não será fechada. Se a janela não tem um **fechar** botão ou não pode ser fechada por algum motivo, o sistema operacional emitirá o som de alerta.

Por exemplo:

```csharp
MyWindow.PerformClose(this);
```

Tentativa de fechar o `MyWindow` `NSWindow` instância. Se ela foi bem-sucedida, a janela será fechada, caso contrário o som de alerta será emitido e o será permanecerá aberto.

<a name="Close" />

### <a name="close"></a>Fechar

Chamando o `Close` método de um `NSWindow` não simula o usuário clicar na janela **fechar** botão momentaneamente realçando o botão, ele simplesmente fecha a janela.

Uma janela não precisa ser visível a ser fechado e um `NSWindowWillCloseNotification` notificação será lançada para o Centro de notificação padrão para a janela que está sendo fechada.

O `Close` método difere de duas maneiras importantes a `PerformClose` método:

1. Não tente gerar o `WillClose` evento.
2. Ele não simula o usuário clicando o **fechar** botão, realçando momentaneamente o botão.

Por exemplo:

```csharp
MyWindow.Close();
```

Seria para fechar o `MyWindow` `NSWindow` instância.

<a name="Modified-Windows-Content" />

## <a name="modified-windows-content"></a>Conteúdo do Windows modificado

Em macOS, Apple fornece uma forma para informar ao usuário que o conteúdo de uma janela (`NSWindow`) foi modificado pelo usuário e precisa ser salvo. Se a janela contém conteúdo modificado, um pequeno ponto preto será exibido no seu **fechar** widget:

[![](window-images/close01.png "Uma janela com o marcador modificado")](window-images/close01.png#lightbox)

Se o usuário tentar fechar a janela ou sair do aplicativo Mac enquanto houver não salvas para conteúdo da janela, deve apresentar um [caixa de diálogo](~/mac/user-interface/dialog.md) ou [folha Modal](~/mac/user-interface/dialog.md) e permitir que o usuário salvar suas alterações primeiro:

[![](window-images/close02.png "Salvar folha que está sendo mostrada quando a janela for fechada")](window-images/close02.png#lightbox)

### <a name="marking-a-window-as-modified"></a>Marcar uma janela como modificado

Para marcar uma janela como tendo modificou o conteúdo, use o seguinte código:

```csharp
// Mark Window content as modified
Window.DocumentEdited = true;
```

E, depois que a alteração tiver sido salvo, limpar o sinalizador modificados usando:

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
    public class EditorWidowDelegate : NSWindowDelegate
    {
        #region Computed Properties
        public NSWindow Window { get; set;}
        #endregion

        #region constructors
        public EditorWidowDelegate (NSWindow window)
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

                // Take action based on resu;t
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

Use o seguinte código para anexar uma instância deste delegado para sua janela:

```csharp
// Set delegate
Window.Delegate = new EditorWidowDelegate(Window);
```

### <a name="saving-changes-before-closing-the-app"></a>Salvar as alterações antes de fechar o aplicativo

Por fim, seu App Xamarin.Mac deve verificar se quaisquer janelas contêm conteúdo modificado e permitir que o usuário salvar as alterações antes de sair. Para fazer isso, edite o `AppDelegate.cs` de arquivo, substitua o `ApplicationShouldTerminate` método e torná-lo a aparência a seguir:

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

## <a name="working-with-multiple-windows"></a>Trabalhando com várias janelas

A maioria dos aplicativos do Mac documento baseado pode editar vários documentos ao mesmo tempo. Por exemplo, um editor de texto pode ter vários arquivos de texto aberto para edição ao mesmo tempo. Por padrão, nosso novo aplicativo Xamarin.Mac tem um **arquivo** menu com um **novo** item automaticamente com fio-até o `newDocument:` **ação**.

Vamos para ativar esse novo item e permitir que o usuário abra várias cópias de nossa janela principal para editar vários documentos de uma vez.

Vamos Editar nossos `AppDelegate.cs` e adicione o seguinte de propriedade calculados:

```csharp
public int UntitledWindowCount { get; set;} =1;
```

Usaremos isso para controlar o número de arquivos não salvos para que possamos dar comentários para o usuário (de acordo com as diretrizes da Apple, conforme descrito acima).

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

Esse código cria uma nova versão do controlador nossa janela, carrega a nova janela, torna o principal e a janela de chave e define-o título. Agora, se executar o nosso aplicativo e selecione **novo** do **arquivo** menu uma nova janela do editor será aberta e exibida:

[![](window-images/display04.png "Foi adicionada uma nova janela sem título")](window-images/display04.png#lightbox)

Se abrir o **Windows** menu, você pode ver o aplicativo é controle e manipulação nosso janelas abertas automaticamente:

[![](window-images/display05.png "No menu do Windows")](window-images/display05.png#lightbox)

Para obter mais informações sobre como trabalhar com Menus em um aplicativo de Xamarin.Mac, consulte nosso [trabalhando com Menus](~/mac/user-interface/menu.md) documentação.

<a name="Getting_the_Currently_Active_Window" />

### <a name="getting-the-currently-active-window"></a>Obtendo a janela ativa no momento

Em um aplicativo de Xamarin.Mac que pode abrir várias janelas (documentos), há ocasiões quando você precisará obter a janela atual, mais alta (a janela de chave). O código a seguir retornará a janela de chave:

```csharp
var window = NSApplication.SharedApplication.KeyWindow;
```

Ele pode ser chamado em qualquer classe ou método que precisa acessar a janela atual, key. Se nenhuma janela estiver aberta, ele retornará `null`.

<a name="Accessing-All-App-Windows" />

### <a name="accessing-all-app-windows"></a>Acessar todas as janelas de aplicativo

Pode haver momentos em que você precisa para acessar todas as janelas que seu aplicativo Xamarin.Mac tem atualmente aberto. Por exemplo ver se um arquivo que o usuário deseja abrir ainda estiver aberto em uma janela de saída.

O `NSApplication.SharedApplication` mantém um `Windows` propriedade que contém uma matriz de todas as janelas abertas em seu aplicativo. Você pode repetir essa matriz para acessar todos os windows atual do aplicativo. Por exemplo:

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

No código de exemplo são converter cada janela retornada personalizado `ViewController` classe em nosso aplicativo e a testar o valor de um personalizado `Path` propriedade com o caminho de um arquivo que o usuário deseja abrir. Se o arquivo já estiver aberto, traz a janela para a frente.

<a name="Adjusting_the_Window_Size_in_Code" />

## <a name="adjusting-the-window-size-in-code"></a>Ajustando o tamanho da janela de código

Há vezes quando o aplicativo precisar redimensionar uma janela de código. Para redimensionar e reposicionar uma janela, você ajustar do `Frame` propriedade. Ao ajustar o tamanho da janela, você geralmente precisa também ajustar sua origem, para manter a janela no mesmo local devido ao sistema de coordenadas do macOS.

Ao contrário do iOS onde o canto superior esquerdo representa (0,0), macOS usa um sistema de coordenadas matemáticos, onde o canto inferior esquerdo da tela representa (0,0). No iOS, as coordenadas aumentam conforme você mover para baixo para a direita. Em macOS, as coordenadas aumentam o valor para cima à direita. 

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
> Quando você ajusta um tamanho de windows e o local no código, você precisa certificar-se de que respeita os tamanhos mínimos e máximo que você definiu no construtor de Interface. Isso não serão automaticamente respeitado e você poderá deixar a janela maior ou menor do que esses limites.

<a name="Monitoring-Window-Size-Changes" />

## <a name="monitoring-window-size-changes"></a>Monitorando as alterações de tamanho de janela

Pode haver momentos em que você precisa monitorar as alterações no tamanho da janela dentro de seu aplicativo Xamarin.Mac. Por exemplo, para redesenha o conteúdo de acordo com o novo tamanho.

Para monitorar alterações de tamanho, verifique se você atribuiu uma classe personalizada para o controlador de janela no construtor de Interface do Xcode. Por exemplo, `MasterWindowController` a seguir:

[![](window-images/resize01.png "O Inspetor de identidade")](window-images/resize01.png#lightbox)

Em seguida, edite a classe de janela controlador personalizado e o monitor de `DidResize` evento na janela do controlador para ser notificado sobre alterações de tamanho em tempo real. Por exemplo:

```csharp
public override void WindowDidLoad ()
{
    base.WindowDidLoad ();

    Window.DidResize += (sender, e) => {
        // Do something as the window is being live resized
    };
}
```

Opcionalmente, você pode usar o `DidEndLiveResize` evento a ser notificado somente depois que o usuário tenha terminado de alterar o tamanho da janela. Por exemplo:

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

## <a name="setting-a-windows-title-and-represented-file"></a>Definindo uma janela do título e representado arquivo

Ao trabalhar com o windows que representam documentos, `NSWindow` tem um `DocumentEdited` propriedade que, se definida como `true` exibe um pequeno ponto no botão Fechar para dar ao usuário uma indicação de que o arquivo foi modificado e deve ser salvo antes de fechar.

Vamos Editar nossos `ViewController.cs` de arquivo e faça as seguintes alterações:

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

Também estamos monitorando o `WillClose` evento na janela e verificar o estado do `DocumentEdited` propriedade. Se for `true` é necessário dar ao usuário a capacidade de salvar as alterações no arquivo. Se executar o nosso aplicativo e digite algum texto, o ponto final será exibido:

[![](window-images/file01.png "Uma janela alterada")](window-images/file01.png#lightbox)

Se você tentar fechar a janela, obtemos um alerta:

[![](window-images/file02.png "Exibindo uma salvar caixa de diálogo")](window-images/file02.png#lightbox)

Se estamos carregando um documento de um arquivo podemos definir o título da janela para o arquivo nome usando o `window.SetTitleWithRepresentedFilename (Path.GetFileName(path));` método (considerando que `path` é uma cadeia de caracteres que representa o arquivo que está sendo aberto). Além disso, podemos definir a URL do arquivo usando o `window.RepresentedUrl = url;` método.

Se a URL está apontando para um tipo de arquivo conhecido pelo sistema operacional, seu ícone será exibido na barra de título. Se o usuário clica no ícone, o caminho para o arquivo será mostrado.

Vamos editar o `AppDelegate.cs` de arquivo e adicione o seguinte método:

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

Agora se executarmos nosso aplicativo, selecione **abrir...**  do **arquivo** menu, selecione um arquivo de texto do **abrir** caixa de diálogo caixa e abri-lo:

[![](window-images/file03.png "Uma caixa de diálogo Abrir")](window-images/file03.png#lightbox)

O arquivo será exibido e o título será definido com o ícone do arquivo:

[![](window-images/file04.png "O conteúdo de um arquivo carregado")](window-images/file04.png#lightbox)

<a name="Adding_a_New_Window_to_a_Project" />

## <a name="adding-a-new-window-to-a-project"></a>Adicionando uma nova janela a um projeto

Além da janela do documento principal, talvez seja necessário um aplicativo Xamarin.Mac exibir outros tipos de windows para o usuário, como preferências ou painéis do Inspetor.

Para adicionar uma nova janela, faça o seguinte:

1. No **Solution Explorer**, clique duas vezes o `Main.storyboard` arquivo para abri-lo para edição no construtor de Interface do Xcode.
2. Arraste um novo **janela controlador** do **biblioteca** e solte-o **superfície de Design**:

    [![](window-images/new01.png "Selecionando um novo controlador de janela na biblioteca")](window-images/new01.png#lightbox)
3. No **Inspetor de identidade**, digite `PreferencesWindow` para o **Storyboard ID**: 

    [![](window-images/new02.png "Definir a ID do storyboard")](window-images/new02.png#lightbox)
5. Sua interface de design: 

    [![](window-images/new03.png "Criando a interface do usuário")](window-images/new03.png#lightbox)
6. Abra o Menu de aplicativo (`MacWindows`), selecione **preferências...** , Control, clique e arraste para a nova janela: 

    [![](window-images/new05.png "Criando um segue")](window-images/new05.png#lightbox)
7. Selecione **Mostrar** no menu pop-up.
6. Salve suas alterações e retornar ao Visual Studio para Mac sincronizar com o Xcode.

Se executar o código e selecione o **preferências...**  do **aplicativo Menu**, a janela será exibida:

[![](window-images/new04.png "Um menu de preferências de exemplo")](window-images/new04.png#lightbox)

<a name="Working_with_Panels" />

## <a name="working-with-panels"></a>Trabalhando com painéis

Conforme mencionado no início deste artigo, um painel flutua sobre outras janelas e fornece ferramentas ou controles que os usuários podem trabalhar com enquanto documentos estiverem abertos. 

Assim como qualquer outro tipo de janela que você cria e trabalhar com seu aplicativo Xamarin.Mac, o processo é basicamente o mesmo:

1. Adicione uma nova definição de janela para o projeto.
2. Clique duas vezes o `.xib` arquivo para abrir o design de janela para edição no construtor de Interface do Xcode.
2. Definir as propriedades da janela necessária no **Inspetor de atributo** e **Inspetor de tamanho**.
4. Arraste os controles necessários para criar sua interface e configurá-los no **Inspetor de atributo**.
5. Use o **Inspetor de tamanho** para lidar com o redimensionamento para elementos de interface do usuário.
6. Expor elementos de interface do usuário da janela para o código c# via **tomadas** e **ações**.
7. Salve suas alterações e retorne ao Visual Studio para Mac sincronizar com o Xcode.

No **Inspetor de atributo**, você tem as seguintes opções específicas para painéis:

[![](window-images/panel03.png "O Inspetor de atributo")](window-images/panel03.png#lightbox)

- **Estilo** -que você possa ajustar o estilo do painel de: painel Regular (parece uma janela padrão), o painel do utilitário (tem uma barra de título menor), painel HUD (é transparente e a barra de título faz parte do plano de fundo).
- **Ativando não** -determina o painel torna-se a janela de chave.
- **Documento Modal** -se documento restrito, o painel apenas flutua sobre janelas do aplicativo, caso contrário ela flutua acima de tudo.


Para adicionar um novo painel, faça o seguinte:

1. No **Solution Explorer**, com o botão direito no projeto e selecione **adicionar** > **novo arquivo.** .
2. Na caixa de diálogo Novo arquivo, selecione **Xamarin.Mac** > **Cocoa janela com controlador**:

    [![](window-images/panels00.png "Adicionar um novo controlador de janela")](window-images/panels00.png#lightbox)
3. Digite `DocumentPanel` para o **Nome** e clique no botão **Novo**.
4. Clique duas vezes o `DocumentPanel.xib` arquivo para abri-lo para edição no construtor de Interface: 

    [![](window-images/new02.png "O painel de edição")](window-images/new02.png#lightbox)
5. Excluir a janela existente e arraste um painel do **Inspetor de biblioteca** no o **Editor de Interface**: 

    [![](window-images/panels01.png "Excluindo a janela existente")](window-images/panels01.png#lightbox)
6. Conectar-se com o painel até o **proprietário do arquivo*-**janela*- **tomada**: 

    [![](window-images/panels02.png "Arrastar para conexão ao painel")](window-images/panels02.png#lightbox)
7. Alterne para o **Inspetor de identidade** e definir a classe do painel `DocumentPanel`: 

    [![](window-images/panels03.png "Classe do painel de configuração")](window-images/panels03.png#lightbox)
6. Salve suas alterações e retornar ao Visual Studio para Mac sincronizar com o Xcode.
7. Editar o `DocumentPanel.cs` de arquivo e altere a definição de classe para o seguinte: 

    `public partial class DocumentPanel : NSPanel`
8. Salve as alterações no arquivo.

Editar o `AppDelegate.cs` de arquivo e verifique o `DidFinishLaunching` método aparência semelhante ao seguinte:

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
> Windows do painel foram substituídos pela Apple e deve ser substituídos pelo **Inspetor de Interfaces**. Para obter um exemplo completo de criação de um **Inspetor** em um aplicativo de Xamarin.Mac, consulte nosso [MacInspector](https://developer.xamarin.com/samples/mac/MacInspector/) aplicativo de exemplo.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo obteve uma visão detalhada de como trabalhar com janelas e painéis em um aplicativo Xamarin.Mac. Nós vimos os diferentes tipos e usa do Windows e painéis, como criar e manter janelas e painéis no construtor de Interface do Xcode e como trabalhar com janelas e painéis em código c#.

## <a name="related-links"></a>Links relacionados

- [MacWindows (exemplo)](https://developer.xamarin.com/samples/mac/MacWindows/)
- [MacInspector (exemplo)](https://developer.xamarin.com/samples/mac/MacInspector/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabalhando com Menus](~/mac/user-interface/menu.md)
- [Diretrizes de interface humana do OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introdução ao Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
