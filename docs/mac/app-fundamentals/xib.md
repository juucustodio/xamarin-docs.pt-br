---
title: arquivos de .xib
description: "Este artigo aborda o trabalho com arquivos .xib criados no construtor de Interface do Xcode para criar e manter as interfaces de usuário para um aplicativo Xamarin.Mac."
ms.topic: article
ms.prod: xamarin
ms.assetid: 6AF3D216-448D-4B2D-9026-74E4FFF5923A
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 92ca65409dd82806278885bb03efd7b04ab1827d
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/15/2018
---
# <a name="xib-files"></a>arquivos de .xib

_Este artigo aborda o trabalho com arquivos .xib criados no construtor de Interface do Xcode para criar e manter as interfaces de usuário para um aplicativo Xamarin.Mac._

> [!NOTE]
> É a melhor maneira de criar uma interface de usuário para um aplicativo Xamarin.Mac com storyboards. Esta documentação foi deixada no local por razões históricas e para trabalhar com projetos de Xamarin.Mac mais antigos. Para obter mais informações, consulte nosso [Introdução a Storyboards](~/mac/platform/storyboards/index.md) documentação.

## <a name="overview"></a>Visão geral

Ao trabalhar com c# e .NET em um aplicativo de Xamarin.Mac, você tem acesso para os mesmos elementos de interface de usuário e as ferramentas que um desenvolvedor trabalhando em *Objective-C* e *Xcode* does. Como Xamarin.Mac se integra diretamente com o Xcode, você pode usar do Xcode _Interface construtor_ para criar e manter suas interfaces de usuário (ou, opcionalmente, criá-los diretamente no código do c#).

Um arquivo .xib é usado pelo macOS para definir os elementos de interface do usuário do seu aplicativo (como Menus, Windows, exibições, rótulos, campos de texto) que são criadas e mantidas graficamente no construtor de Interface do Xcode.

[![Um exemplo de aplicativo em execução](xib-images/intro01.png "um exemplo de aplicativo em execução")](xib-images/intro01-large.png#lightbox)

Neste artigo, vamos abordar os fundamentos de trabalhar com arquivos .xib em um aplicativo Xamarin.Mac. É altamente recomendável que você leia o [Hello, Mac](~/mac/get-started/hello-mac.md) artigo primeiro, pois ele aborda os principais conceitos e técnicas que será usado neste artigo.

Talvez você queira dar uma olhada a [classes expondo c# / métodos para Objective-C](~/mac/internals/how-it-works.md) seção o [Xamarin.Mac internos](~/mac/internals/how-it-works.md) de documento, ele explica o `Register` e `Export` atributos usado para conectar as classes c# objetos Objective-C e a interface do usuário elementos.


## <a name="introduction-to-xcode-and-interface-builder"></a>Introdução ao Interface Builder e ao Xcode

Como parte do Xcode, Apple criou uma ferramenta chamada de construtor de Interface, que permite que você crie sua Interface de usuário visualmente em um designer. Xamarin.Mac fluently integra com o construtor de Interface, permitindo que você crie sua interface do usuário com as mesmas ferramentas que os usuários de Objective-C.


### <a name="components-of-xcode"></a>Componentes do Xcode

Quando você abre um arquivo .xib no Xcode do Visual Studio para Mac, ele abre com uma **navegador de projeto** à esquerda, o **hierarquia Interface** e **Editor de Interface** no meio e um **propriedades & utilitários** seção à direita:

[![Os componentes da UI Xcode](xib-images/xcode03.png "os componentes da UI Xcode")](xib-images/xcode03-large.png#lightbox)

Vamos dar uma olhada em que cada um desses Xcode seções faz e como eles serão ser usados para criar a interface para seu aplicativo Xamarin.Mac.


#### <a name="project-navigation"></a>Navegação de projeto

Quando você abre um arquivo de .xib para edição no Xcode, o Visual Studio para Mac cria um arquivo de projeto Xcode em segundo plano para comunicar alterações entre ele mesmo e Xcode. Posteriormente, quando você alternar para o Visual Studio para Mac do Xcode, todas as alterações feitas a este projeto são sincronizadas com o seu projeto Xamarin.Mac pelo Visual Studio para Mac.

O **projeto navegação** seção permite que você navegue entre todos os arquivos que compõem esse _shim_ projeto Xcode. Normalmente, você só estará interessado nos arquivos de .xib nesta lista, como **MainMenu.xib** e **MainWindow.xib**.


#### <a name="interface-hierarchy"></a>Hierarquia de interface

O **hierarquia Interface** seção permite que você acesse facilmente várias propriedades de chave da Interface do usuário, como seu **espaços reservados** e principal **janela**. Você também pode usar esta seção para acessar elementos individuais (views) que compõem sua interface de usuário e a ajustar a maneira que eles são aninhados arrastando-os dentro da hierarquia.


#### <a name="interface-editor"></a>Editor de interface

O **Editor de Interface** seção fornece a superfície no qual você graficamente layout sua Interface do usuário. Você poderá arrastar os elementos do **biblioteca** seção o **propriedades & utilitários** seção para criar o design. Conforme você adiciona elementos de interface do usuário (views) para a superfície de design, eles serão adicionados para o **hierarquia Interface** seção na ordem em que aparecem no **Editor de Interface**.


#### <a name="properties--utilities"></a>Propriedades e utilitários

O **propriedades & utilitários** seção é dividida em duas seções principais que trabalharemos com, **propriedades** (também chamado de inspetores) e o **biblioteca**:

![Inspetor de](xib-images/xcode04.png "Inspetor")

Inicialmente nesta seção está quase vazia, no entanto se você selecionar um elemento no **Editor de Interface** ou **hierarquia Interface**, o **propriedades** seção será preenchida informações sobre as propriedades que você pode ajustar e um determinado elemento.

Na seção **Propriedades** há 8 diferentes *guias de inspetor*, conforme mostrado na ilustração a seguir:

[![Uma visão geral de todos os inspetores](xib-images/xcode05.png "uma visão geral de todos os inspetores")](xib-images/xcode05-large.png#lightbox)

Da esquerda para a direita, essas guias são:

-   **Inspetor de Arquivo** – o Inspetor de Arquivo mostra informações do arquivo, como o nome do arquivo e o local do arquivo Xib que está sendo editado.
-   **Ajuda Rápida** – a guia Ajuda Rápida fornece ajuda contextual com base no que está selecionado no Xcode.
-   **Inspetor de Identidade** – o Inspetor de Identidade fornece informações sobre a exibição/controle selecionado.
-   **Atributos de Inspetor** – o Inspetor de atributos permite que você personalize vários atributos de controle/exibição selecionada.
-   **Inspetor de tamanho** – o Inspetor de tamanho permite controlar o tamanho e o comportamento do controle selecionado/modo de redimensionamento.
-   **Inspetor de conexões** – o Inspetor de conexões mostra as conexões de saída e a ação dos controles selecionados. Vamos examinar tomadas e ações em breve.
-   **Inspetor de associações** – o Inspetor de associações permite que você configure controles para que seus valores são vinculados automaticamente aos modelos de dados.
-   **Exibir o Inspetor de efeitos** – o Inspetor de efeitos de exibição permite que você especifique os efeitos nos controles, como animações.

No **biblioteca** seção, você pode encontrar controles e objetos para colocar graficamente ao designer para criam sua interface do usuário:

![Um exemplo do Inspetor de biblioteca](xib-images/xcode06.png "um exemplo do Inspetor de biblioteca")

Agora que você está familiarizado com o Xcode IDE e o construtor da Interface, vamos examinar usá-lo para criar uma interface do usuário.


## <a name="creating-and-maintaining-windows-in-xcode"></a>Criação e manutenção do windows no Xcode

É o método preferencial para a criação de Interface do usuário do aplicativo um Xamarin.Mac com Storyboards (consulte nossa [Introdução a Storyboards](~/mac/platform/storyboards/index.md) documentação para obter mais informações) e, como resultado, qualquer novo projeto iniciado em Xamarin.Mac será Use Storyboards por padrão.

Para alternar para o usando um .xib baseada em interface do usuário, faça o seguinte:

1. Abra o Visual Studio para Mac e iniciar um novo projeto de Xamarin.Mac.
2. No **solução preenchimento**, com o botão direito no projeto e selecione **adicionar** > **novo arquivo...**
3. Selecione **Mac** > **Windows controlador**:

    ![Adicionar um novo controlador de janela](xib-images/setup00.png "adicionando um novo controlador de janela")
4. Digite `MainWindow` para o nome e clique o **novo** botão:

    ![Adicionando uma nova janela principal](xib-images/setup01.png "adicionando uma nova janela principal")
5. Clique com botão direito no projeto novamente e selecione **adicionar** > **novo arquivo...**
6. Selecione **Mac** > **Menu principal**:

    ![Adicionando um novo Menu principal](xib-images/setup02.png "adicionando um novo Menu principal")
7. Deixe o nome como `MainMenu` e clique no **novo** botão.
8. No **solução preenchimento** selecionar o **Main.storyboard** de arquivos, clique com botão direito e selecione **remover**:

    ![Selecionando o storyboard principal](xib-images/setup03.png "selecionando o storyboard principal")
9. Na caixa de diálogo Remover, clique o **excluir** botão:

    ![Confirmar a exclusão](xib-images/setup04.png "confirmar a exclusão")
10. No **solução preenchimento**, clique duas vezes o **Info. plist** arquivo para abri-lo para edição.
11. Selecione `MainMenu` do **Interface principal** suspensa:

    [![Configuração do menu principal](xib-images/setup05.png "configuração do menu principal")](xib-images/setup05-large.png#lightbox)
12. No **solução preenchimento**, clique duas vezes o **MainMenu.xib** arquivo para abri-lo para edição no construtor de Interface do Xcode.
13. No **biblioteca Inspetor**, tipo `object` no campo de pesquisa, em seguida, arraste um novo **objeto** na superfície de design:

    [![Edição do menu principal](xib-images/setup06.png "edição do menu principal")](xib-images/setup06-large.png#lightbox)
14. No **Inspetor de identidade**, digite `AppDelegate` para o **classe**:

    [![Selecionando o representante do aplicativo](xib-images/setup07.png "selecionando o representante do aplicativo")](xib-images/setup07-large.png#lightbox)
15. Selecione **proprietário do arquivo** do **hierarquia de Interface**, alterne para o **Inspetor de Conexão** e arraste uma linha a partir do delegado para o `AppDelegate` **Objeto** acabou de adicionar ao projeto:

    [![Conectando o representante do aplicativo](xib-images/setup08.png "conectando o representante do aplicativo")](xib-images/setup08-large.png#lightbox)
16. Salvar as alterações e retornar ao Visual Studio para Mac.

Com todas essas alterações em vigor, edite o **AppDelegate.cs** de arquivo e torná-lo a aparência a seguir:

```csharp
using AppKit;
using Foundation;

namespace MacXib
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        public MainWindowController mainWindowController { get; set; }

        public AppDelegate ()
        {
        }

        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
            mainWindowController = new MainWindowController ();
            mainWindowController.Window.MakeKeyAndOrderFront (this);
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }
    }
}
```

Agora a janela do aplicativo principal é definida em um **.xib** arquivo incluído automaticamente no projeto ao adicionar um controlador de janela. Para editar o design do windows, no **solução preenchimento**, clique duas vezes o **MainWindow.xib** arquivo:

![Selecionando o arquivo MainWindow.xib](xib-images/edit01.png "selecionando o arquivo MainWindow.xib")

Isso abrirá o design de janela no construtor de Interface do Xcode:

[![Editando o MainWindow.xib](xib-images/edit02.png "editando o MainWindow.xib")](xib-images/edit02-large.png#lightbox)


### <a name="standard-window-workflow"></a>Fluxo de trabalho de janela padrão

Para qualquer janela que você criar e trabalhar com seu aplicativo Xamarin.Mac, o processo é basicamente o mesmo:

1. Para o novo windows que não são o padrão automaticamente adicionado ao seu projeto, adicione uma nova definição de janela para o projeto.
2. Clique duas vezes no arquivo de .xib para abrir o design de janela para edição no construtor de Interface do Xcode.
3. Definir as propriedades da janela necessária no **Inspetor de atributo** e **Inspetor de tamanho**.
4. Arraste os controles necessários para criar sua interface e configurá-los no **Inspetor de atributo**.
5. Use o **Inspetor de tamanho** para lidar com o redimensionamento para elementos de interface do usuário.
6. Expor elementos de interface do usuário da janela para o código c# por meio de saídas e ações.
7. Salve suas alterações e retorne ao Visual Studio para Mac sincronizar com o Xcode.


### <a name="designing-a-window-layout"></a>Criação de um layout de janela

O processo para dispor de uma Interface do usuário no construtor de Interface é basicamente o mesmo para todos os elementos que você adicionar:

1. Localizar o controle desejado no **Inspetor de biblioteca** e arraste até o **Editor de Interface** e posicione-o.
2. Definir as propriedades da janela necessária no **Inspetor de atributo**.
3. Use o **Inspetor de tamanho** para lidar com o redimensionamento para elementos de interface do usuário.
4. Se você estiver usando uma classe personalizada, defina-o **Inspetor de identidade**.
5. Expor os elementos de interface do usuário ao código c# por meio de saídas e ações.
6. Salve suas alterações e retorne ao Visual Studio para Mac sincronizar com o Xcode.

Por exemplo:

1. No Xcode, arraste um **botão de ação** da **seção Biblioteca**:

    [![Selecione um botão da biblioteca](xib-images/xcode07.png "selecionando um botão da biblioteca")](xib-images/xcode07-large.png#lightbox)
2. Solte o botão sobre o **janela** no **Editor de Interface**:

    [![Adicionar um botão à janela](xib-images/xcode08.png "adicionar um botão à janela")](xib-images/xcode08-large.png#lightbox)
3. Clique na propriedade **Título** no **Inspetor de atributos** e altere o título do botão para `Click Me`:

    ![Definir os atributos de botão](xib-images/xcode09.png "definindo os atributos de botão")
4. Arraste um **rótulo** da **seção Biblioteca**:

    [![Selecionando um rótulo na biblioteca](xib-images/xcode10.png "selecionando um rótulo na biblioteca")](xib-images/xcode10-large.png#lightbox)
5. Solte o rótulo na **janela** ao lado do botão no **Editor de interface**:

    [![Adicionar um rótulo para a janela](xib-images/xcode11.png "adicionar um rótulo para a janela")](xib-images/xcode11-large.png#lightbox)
6. Pegue a alça direita no rótulo e arraste-a até que ela esteja perto da borda da janela:

    [![Redimensionando o rótulo](xib-images/xcode12.png "redimensionando o rótulo")](xib-images/xcode12-large.png#lightbox)
7. Com o rótulo selecionado ainda no **Editor de Interface**, alterne para o **Inspetor de tamanho**:

    ![Selecionando o Inspetor de tamanho](xib-images/xcode13.png "selecionando o Inspetor de tamanho")
8. No **caixa dimensionando** clique o **Dim colchete de vermelho** à direita e o **Dim seta Horizontal de vermelho** no Centro de:

    ![Editar as propriedades de dimensionamento automático](xib-images/xcode14.png "editando as propriedades de dimensionamento automático")
9. Isso garante que o rótulo será ampliado para ampliar ou reduzir como a janela é redimensionada no aplicativo em execução. O **vermelho colchetes** e a parte superior e esquerda do **caixa dimensionando** caixa informar o rótulo a ser travar determinados X e Y locais.
10. Salvar as alterações para a Interface do usuário

Como você redimensionar e mover os controles, você deve ter notado que o construtor da Interface fornece dicas úteis snap se baseiam em [diretrizes de Interface humana do sistema operacional X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). Essas diretrizes ajudarão você a criar aplicativos de alta qualidade que terão uma aparência familiar para os usuários do Mac.

Se você examinar o **hierarquia Interface** seção, observe como o layout e a hierarquia dos elementos que compõem a nossa Interface do usuário são mostrados:

![Selecionando um elemento na hierarquia de Interface](xib-images/xcode15.png "selecionando um elemento na hierarquia de Interface")

Aqui você pode selecionar itens para editar ou arraste-a para reordenar os elementos de interface do usuário, se necessário. Por exemplo, se um elemento de interface do usuário estava sendo coberto por outro elemento, você poderia arrastá-lo para a parte inferior da lista para torná-lo o item mais alto na janela de.

Para obter mais informações sobre como trabalhar com o Windows em um aplicativo de Xamarin.Mac, consulte nosso [Windows](~/mac/user-interface/window.md) documentação.


## <a name="exposing-ui-elements-to-c-code"></a>Exposição de elementos de interface do usuário ao código c#

Depois de terminar de dispor a aparência da interface do usuário no construtor de Interface, você precisará expor elementos de interface do usuário para que eles possam ser acessados de código c#. Para fazer isso, você estará usando ações e saídas.


### <a name="setting-a-custom-main-window-controller"></a>Configurar um controlador personalizado janela principal

Para poder criar tomadas e ações para expor elementos de interface do usuário ao código c#, o aplicativo Xamarin.Mac precisará estar usando um controlador de janela personalizada.

Faça o seguinte:

1. Abra Storyboard do aplicativo no construtor de Interface do Xcode.
2. Selecione o `NSWindowController` na superfície de Design.
3. Alterne para o **Inspetor de identidade** exibir e insira `WindowController` como o **nome da classe**:

    [![Editar o nome da classe](xib-images/windowcontroller01.png "editando o nome da classe")](xib-images/windowcontroller01-large.png#lightbox)
4. Salve suas alterações e retornar ao Visual Studio para Mac sincronizar.
5. Um **WindowController.cs** arquivo será adicionado ao seu projeto no **solução preenchimento** no Visual Studio para Mac:

    ![O novo nome de classe no Visual Studio para Mac](xib-images/windowcontroller02.png "o novo nome de classe no Visual Studio para Mac")
6. Reabra o Storyboard no construtor de Interface do Xcode.
7. O **WindowController.h** arquivo estará disponível para uso:

    [![O arquivo. h correspondente no Xcode](xib-images/windowcontroller03.png "o arquivo. h correspondente no Xcode")](xib-images/windowcontroller03-large.png#lightbox)


### <a name="outlets-and-actions"></a>Tomadas e ações

Quais são tomadas e ações? Na programação tradicional de interface do usuário do .NET, um controle na interface do usuário é exposto automaticamente como uma propriedade quando ele é adicionado. As coisas funcionam de modo diferente no Mac, simplesmente adicionar um controle a um modo de exibição não o torna acessível ao código. O desenvolvedor deve expor explicitamente o elemento de interface do usuário ao código. Em ordem para fazer isso, Apple nos fornece duas opções:

-  **Saídas** – saídas são análogas às propriedades. Se você conectar um controle a uma tomada, ele é exposto ao seu código por meio de uma propriedade, então você pode fazer coisas como anexar manipuladores de eventos, chamar métodos em etc.
-  **Ações** – ações são análogas ao padrão de comando no WPF. Por exemplo, quando uma ação é executada em um controle, digamos que um clique de botão, o controle automaticamente chamará um método em seu código. Ações são eficientes e conveniente, porque você pode conectar muitos controles para a mesma ação.

No Xcode, saídas e as ações são adicionadas diretamente no código por meio de *arrastando o controle*. Mais especificamente, isso significa que para criar uma loja ou uma ação, escolha qual elemento de controle que você deseja adicionar uma loja ou uma ação, mantenha pressionada a **controle** botão no teclado e, em seguida, arraste o controle diretamente em seu código.

Para desenvolvedores de Xamarin.Mac, isso significa que você arrastar para os arquivos de stub Objective-C que correspondem ao arquivo c# onde você deseja criar a ação ou o. O Visual Studio para Mac criado um arquivo chamado **MainWindow.h** como parte do shim projeto Xcode gerado para usar o construtor de Interface:

[![Um exemplo de um arquivo. h no Xcode](xib-images/xcode16.png "um exemplo de um arquivo. h no Xcode")](xib-images/xcode16-large.png#lightbox)

Este arquivo stub de h reflete o **MainWindow.designer.cs** que é adicionado automaticamente a um projeto de Xamarin.Mac quando um novo `NSWindow` é criado. Esse arquivo será usado para sincronizar as alterações feitas pelo construtor de Interface e é onde criaremos suas saídas e ações para que os elementos de interface do usuário são expostos a código c#.


#### <a name="adding-an-outlet"></a>Adicionando uma tomada

Com uma compreensão básica dos quais são tomadas e ações, vamos criar uma tomada para expor um elemento de interface do usuário ao seu código c#.

Faça o seguinte:

1. No Xcode, no canto direito superior da tela, clique no botão com o **círculo duplo** para abrir o **Editor assistente**:

    [![Selecionar o Editor de assistente](xib-images/outlet01.png "selecionando o Assistente de Editor")](xib-images/outlet01-large.png#lightbox)
2. O Xcode mudará para um modo de exibição de divisão com o **Editor de interface** em um lado e um **Editor de código** no outro.
3. Observe que Xcode automaticamente separada o **MainWindowController.m** arquivo o **Editor de códigos**, que é incorreto. Se você se lembra de nossa discussão sobre o que são tomadas e ações acima, é preciso ter o **MainWindow.h** selecionado.
4. Na parte superior do **Editor de códigos** clique no **Link automático** e selecione o **MainWindow.h** arquivo:

    [![Selecionando o arquivo correto h](xib-images/outlet02.png "selecionando o arquivo correto. h")](xib-images/outlet02-large.png#lightbox)
5. O Xcode agora deve estar com o arquivo correto selecionado:

    [![O arquivo correto selecionado](xib-images/outlet03.png "o arquivo correto selecionado")](xib-images/outlet03-large.png#lightbox)
6. **A última etapa era muito importante.** Se você não tiver o arquivo correto selecionado, você não poderá criar tomadas e ações ou se eles serão expostos à classe errado em c#!
7. No **Editor de Interface**, mantenha pressionada a **controle** chave no teclado e clique e arraste o rótulo que criamos acima para o editor de código logo abaixo o `@interface MainWindow : NSWindow { }` código:

    [![Arrastar para criar uma nova loja](xib-images/outlet04.png "arrastar para criar uma nova loja")](xib-images/outlet04-large.png#lightbox)
8. Uma caixa de diálogo será exibida. Deixe o **Conexão** definida para a loja e inserir `ClickedLabel` para o **nome**:

    [![Definindo as propriedades de tomada](xib-images/outlet05.png "definindo as propriedades de saída")](xib-images/outlet05-large.png#lightbox)
9. Clique o **conectar** botão para criar a loja:

    ![A tomada concluída](xib-images/outlet06.png "a tomada concluída")
10. Salve as alterações no arquivo.


#### <a name="adding-an-action"></a>Adicionar uma ação

Em seguida, vamos criar uma ação para expor uma interação do usuário com o elemento de interface do usuário ao seu código c#.

Faça o seguinte:

1. Certifique-se de que estão na **Assistente de Editor** e **MainWindow.h** arquivo está visível no **Editor de código**.
2. No **Editor de Interface**, mantenha pressionada a **controle** chave no teclado e clique e arraste o botão que criamos acima para o editor de código logo abaixo de `@property (assign) IBOutlet NSTextField *ClickedLabel;` código:

    [![Arrastar para criar uma ação](xib-images/action01.png "arrastar para criar uma ação")](xib-images/action01-large.png#lightbox)
3. Alterar o **Conexão** tipo de ação:

    [![Selecione um tipo de ação](xib-images/action02.png "selecionar um tipo de ação")](xib-images/action02-large.png#lightbox)
4. Digite `ClickedButton` como o **Nome**:

    [![Configurar a ação de](xib-images/action03.png "Configurando a ação")](xib-images/action03-large.png#lightbox)
5. Clique o **conectar** botão para criar a ação:

    ![A ação concluída](xib-images/action04.png "a ação concluída")
6. Salve as alterações no arquivo.

Com sua Interface de usuário com fio-up e expostos ao código c#, alterne para o Visual Studio para Mac e deixá-lo a sincronizar as alterações do Xcode e o construtor de Interface.


### <a name="writing-the-code"></a>Escrevendo o código

Com a Interface do usuário criado e seus elementos de interface do usuário expostos ao código por meio de saídas e as ações, você está pronto para escrever o código para dar vida a seu programa. Por exemplo, abra o **MainWindow.cs** arquivo para edição, clique duas vezes no **solução preenchimento**:

[![O arquivo MainWindow.cs](xib-images/code01.png "MainWindow.cs o arquivo")](xib-images/code01-large.png#lightbox)

E adicione o seguinte código para o `MainWindow` classe para trabalhar com a tomada de exemplo criado acima:

```csharp
private int numberOfTimesClicked = 0;
...

public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Set the initial value for the label
    ClickedLabel.StringValue = "Button has not been clicked yet.";
}
```

Observe que o `NSLabel` é acessado no c# pelo nome da direto que você atribuiu no Xcode, quando você criou tomada no Xcode, nesse caso, ele é chamado `ClickedLabel`. Você pode acessar qualquer método ou propriedade do objeto exposto da mesma maneira que faria com qualquer classe c# normal.

> [!IMPORTANT]
> Você precisa usar `AwakeFromNib`, em vez de outro método, como `Initialize`, pois `AwakeFromNib` é chamado _depois_ carregados e instanciado a Interface do usuário do arquivo .xib o sistema operacional. Se você tentasse acessar o controle de rótulo antes do arquivo .xib foi totalmente carregada e instanciado, você receberia um `NullReferenceException` erro porque o controle de rótulo não seria criado ainda.

Em seguida, adicione a seguinte classe parcial para a `MainWindow` classe:

```csharp
partial void ClickedButton (Foundation.NSObject sender) {

    // Update counter and label
    ClickedLabel.StringValue = string.Format("The button has been clicked {0} time{1}.",++numberOfTimesClicked, (numberOfTimesClicked < 2) ? "" : "s");
}
```

Esse código anexa à ação que você criou no Xcode e o construtor de Interface e será chamado a qualquer momento em que o usuário clica no botão.

Alguns elementos de interface do usuário automaticamente criou em ações, por exemplo, os itens na barra de menus padrão, como o **abrir...**  item de menu (`openDocument:`). No **solução preenchimento**, clique duas vezes no **appdelegate. CS** arquivo para abri-lo para edição e adicione o seguinte código abaixo o `DidFinishLaunching` método:

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

A linha de chave aqui é `[Export ("openDocument:")]`, informa `NSMenu` que o **AppDelegate** tem um método `void OpenDialog (NSObject sender)` que responde a `openDocument:` ação.

Para obter mais informações sobre como trabalhar com Menus, consulte nosso [Menus](~/mac/user-interface/menu.md) documentação.


## <a name="synchronizing-changes-with-xcode"></a>Sincronizar as alterações com o Xcode

Quando você alternar para o Visual Studio para Mac do Xcode, as alterações feitas no Xcode automaticamente serão sincronizadas com o seu projeto Xamarin.Mac.

Se você selecionar o **MainWindow.designer.cs** no **solução preenchimento** você poderá ver como nosso tomada e a ação de conexão em nosso código do c#:

[![Alterações de sincronização com o Xcode](xib-images/sync01.png "sincronizar as alterações com o Xcode")](xib-images/sync01-large.png#lightbox)

Observe como as duas definições de **MainWindow.designer.cs** arquivo:

```csharp
[Outlet]
AppKit.NSTextField ClickedLabel { get; set; }

[Action ("ClickedButton:")]
partial void ClickedButton (Foundation.NSObject sender);
```

Alinhar com as definições de **MainWindow.h** arquivo no Xcode:

```objc
@property (assign) IBOutlet NSTextField *ClickedLabel;
- (IBAction)ClickedButton:(id)sender;
```

Como você pode ver, o Visual Studio para Mac escuta as alterações no arquivo. h e sincroniza automaticamente as alterações do respectivo **. designer.cs** arquivo expô-los ao seu aplicativo. Você também pode perceber que **MainWindow.designer.cs** é uma classe parcial, para que o Visual Studio para Mac não precisa modificar **MainWindow.cs** que substituiria as alterações feitas à classe.

Normalmente nunca será necessário abrir o **MainWindow.designer.cs** por conta própria, ele foi apresentado aqui apenas para fins educativos.

> [!IMPORTANT]
> Na maioria das situações, o Visual Studio para Mac automaticamente ver todas as alterações feitas no Xcode e sincronizá-los ao seu projeto Xamarin.Mac. Na improvável ocorrência de que a sincronização não aconteça automaticamente, retorne para o Xcode e depois novamente para o Visual Studio para Mac. Isso normalmente dará início a um ciclo de sincronização.


## <a name="adding-a-new-window-to-a-project"></a>Adicionando uma nova janela a um projeto

Além da janela do documento principal, talvez seja necessário um aplicativo Xamarin.Mac exibir outros tipos de windows para o usuário, como preferências ou painéis do Inspetor. Ao adicionar uma nova janela a seu projeto você sempre deve usar o **Cocoa janela com controlador** opção, pois isso faz com que o processo de carregar a janela do .xib mais fácil de arquivo.

Para adicionar uma nova janela, faça o seguinte:

1. No **solução preenchimento**, com o botão direito no projeto e selecione **adicionar** > **novo arquivo.** .
2. Na caixa de diálogo Novo arquivo, selecione **Xamarin.Mac** > **Cocoa janela com controlador**:

    ![Adicionar um novo controlador de janela](xib-images/new01.png "adicionando um novo controlador de janela")
3. Digite `PreferencesWindow` para o **Nome** e clique no botão **Novo**.
4. Clique duas vezes o **PreferencesWindow.xib** arquivo para abri-lo para edição no construtor de Interface:

    [![Editando a janela no Xcode](xib-images/new02.png "editando a janela no Xcode")](xib-images/new02-large.png#lightbox)
5. Sua interface de design:

    [![Criando o layout de windows](xib-images/new03.png "criando layout do windows")](xib-images/new03-large.png#lightbox)
6. Salve suas alterações e retornar ao Visual Studio para Mac sincronizar com o Xcode.

Adicione o seguinte código para **AppDelegate.cs** para exibir a nova janela:

```csharp
[Export("applicationPreferences:")]
void ShowPreferences (NSObject sender)
{
    var preferences = new PreferencesWindowController ();
    preferences.Window.MakeKeyAndOrderFront (this);
}
```

O `var preferences = new PreferencesWindowController ();` linha cria uma nova instância do controlador de janela que carrega a janela do arquivo .xib e inflação-lo. O `preferences.Window.MakeKeyAndOrderFront (this);` linha exibe a nova janela para o usuário.

Se você executar o código e selecione o **preferências...**  do **aplicativo Menu**, a janela será exibida:

![Executando o aplicativo de exemplo](xib-images/new04.png "executando o aplicativo de exemplo")

Para obter mais informações sobre como trabalhar com o Windows em um aplicativo de Xamarin.Mac, consulte nosso [Windows](~/mac/user-interface/window.md) documentação.


## <a name="adding-a-new-view-to-a-project"></a>Adicionando um novo modo de exibição para um projeto

Há ocasiões em que é mais fácil dividir o design da janela em diversos arquivos .xib mais gerenciáveis. Por exemplo, como alternar o conteúdo da janela principal, ao selecionar um item da barra de ferramentas em uma **janela Preferências** ou trocar o conteúdo em resposta a um **lista de origem** seleção.

Ao adicionar um novo modo de exibição para seu projeto você sempre deve usar o **Cocoa exibição com controlador** opção, pois isso faz com que o processo de carregar o modo de exibição do .xib mais fácil de arquivo.

Para adicionar um novo modo de exibição, faça o seguinte:

1. No **solução preenchimento**, com o botão direito no projeto e selecione **adicionar** > **novo arquivo.** .
2. Na caixa de diálogo Novo arquivo, selecione **Xamarin.Mac** > **Cocoa exibição com controlador**:

    ![Adicionando uma nova exibição](xib-images/view01.png "adicionando uma nova exibição")
3. Digite `SubviewTable` para o **Nome** e clique no botão **Novo**.
4. Clique duas vezes o **SubviewTable.xib** arquivo para abri-lo para edição no construtor de Interface e a Interface do usuário de Design:

    [![Criar o novo modo de exibição no Xcode](xib-images/view02.png "criar o novo modo de exibição no Xcode")](xib-images/view02-large.png#lightbox)
5. Conectar quaisquer ações necessárias e saídas.
6. Salve suas alterações e retornar ao Visual Studio para Mac sincronizar com o Xcode.

Em seguida edite o **SubviewTable.cs** e adicione o seguinte código para o **AwakeFromNib** arquivo para preencher o novo modo de exibição quando ele é carregado:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Create the Product Table Data Source and populate it
    var DataSource = new ProductTableDataSource ();
    DataSource.Products.Add (new Product ("Xamarin.iOS", "Allows you to develop native iOS Applications in C#"));
    DataSource.Products.Add (new Product ("Xamarin.Android", "Allows you to develop native Android Applications in C#"));
    DataSource.Products.Add (new Product ("Xamarin.Mac", "Allows you to develop Mac native Applications in C#"));
    DataSource.Sort ("Title", true);

    // Populate the Product Table
    ProductTable.DataSource = DataSource;
    ProductTable.Delegate = new ProductTableDelegate (DataSource);

    // Auto select the first row
    ProductTable.SelectRow (0, false);
}
```

Adicione um enum para o projeto para controlar qual modo de exibição está sendo exibidas no momento. Por exemplo, **SubviewType.cs**:

```csharp
public enum SubviewType
{
    None,
    TableView,
    OutlineView,
    ImageView
}
```

Edite o arquivo .xib da janela que será o modo de exibição de consumo e exibi-lo. Adicionar um **exibição personalizada** que atuará como o contêiner para o modo de exibição quando ele é carregado na memória pelo código do c# e exponha-lo a uma tomada chamado `ViewContainer`:

[![Criando a tomada necessária](xib-images/view03.png "criando a tomada necessária")](xib-images/view03-large.png#lightbox)

Salve suas alterações e retornar ao Visual Studio para Mac sincronizar com o Xcode.

Em seguida, edite o arquivo. cs da janela que deseja exibir o novo modo de exibição (por exemplo, **MainWindow.cs**) e adicione o seguinte código:

```csharp
private SubviewType ViewType = SubviewType.None;
private NSViewController SubviewController = null;
private NSView Subview = null;
...

private void DisplaySubview(NSViewController controller, SubviewType type) {

    // Is this view already displayed?
    if (ViewType == type) return;

    // Is there a view already being displayed?
    if (Subview != null) {
        // Yes, remove it from the view
        Subview.RemoveFromSuperview ();

        // Release memory
        Subview = null;
        SubviewController = null;
    }

    // Save values
    ViewType = type;
    SubviewController = controller;
    Subview = controller.View;

    // Define frame and display
    Subview.Frame = new CGRect (0, 0, ViewContainer.Frame.Width, ViewContainer.Frame.Height);
    ViewContainer.AddSubview (Subview);
}
```

Quando é necessário mostrar uma nova exibição carregado de um arquivo .xib no contêiner da janela (a **exibição personalizada** adicionadas acima), esse código, para tratar removendo qualquer exibição existente e trocando-os para uma nova. Parece para vê-lo, que você já tem um modo de exibição, se assim ele remove da tela. Em seguida leva o modo de exibição que foi passado em (como carregado de um controlador de exibição) é redimensionado para caber na área de conteúdo e o adiciona ao conteúdo para exibição.

Para exibir uma nova exibição, use o seguinte código:

```csharp
DisplaySubview(new SubviewTableController(), SubviewType.TableView);
```

Isso cria uma nova instância do controlador de exibição para a nova exibição a ser exibido, define seu tipo (como especificado pela enumeração adicionada ao projeto) e usa o `DisplaySubview` método adicionado à classe da janela, na verdade, a exibição. Por exemplo:

[![Executando o aplicativo de exemplo](xib-images/view04.png "executando o aplicativo de exemplo")](xib-images/view04-large.png#lightbox)

Para obter mais informações sobre como trabalhar com o Windows em um aplicativo de Xamarin.Mac, consulte nosso [Windows](~/mac/user-interface/window.md) e [caixas de diálogo](~/mac/user-interface/dialog.md) documentação.


## <a name="summary"></a>Resumo

Este artigo obteve uma visão detalhada de como trabalhar com arquivos .xib em um aplicativo Xamarin.Mac. Vimos os diferentes tipos e usa .xib arquivos para criar a Interface do usuário do seu aplicativo, como criar e manter .xib arquivos do Xcode Interface Builder e como trabalhar com arquivos de .xib no código c#.


## <a name="related-links"></a>Links relacionados

- [MacImages (amostra)](https://developer.xamarin.com/samples/mac/MacImages/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Windows](~/mac/user-interface/window.md)
- [Menus](~/mac/user-interface/menu.md)
- [Caixas de diálogo](~/mac/user-interface/dialog.md)
- [Trabalhando com imagens](~/mac/app-fundamentals/image.md)
- [macOS diretrizes de Interface Humana](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
