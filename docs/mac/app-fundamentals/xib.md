---
title: arquivos. XIB no xamarin. Mac
description: Este artigo aborda o trabalho com arquivos. XIB criados no construtor de Interface do Xcode para criar e manter interfaces de usuário para um aplicativo xamarin. Mac.
ms.prod: xamarin
ms.assetid: 6AF3D216-448D-4B2D-9026-74E4FFF5923A
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 45eeee745b133646aef0f775bc879fa6a5d867c7
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61062451"
---
# <a name="xib-files-in-xamarinmac"></a>arquivos. XIB no xamarin. Mac

_Este artigo aborda o trabalho com arquivos. XIB criados no construtor de Interface do Xcode para criar e manter interfaces de usuário para um aplicativo xamarin. Mac._

> [!NOTE]
> É a maneira preferencial para criar uma interface do usuário para um aplicativo xamarin. Mac com storyboards. Esta documentação foi deixada em vigor por razões históricas e para trabalhar com projetos mais antigos do xamarin. Mac. Para obter mais informações, consulte nosso [Introdução ao Storyboards](~/mac/platform/storyboards/index.md) documentação.

## <a name="overview"></a>Visão geral

Ao trabalhar com C# e do .NET em um aplicativo xamarin. Mac, você tem acesso para os mesmos elementos de interface do usuário e as ferramentas de que um desenvolvedor que trabalha *Objective-C* e *Xcode* faz. Como o xamarin. Mac se integra diretamente com Xcode, você pode usar do Xcode _construtor de Interface_ para criar e manter suas interfaces de usuário (ou, opcionalmente, criá-los diretamente em código c#).

Um arquivo. XIB é usado pelo macOS para definir os elementos da interface do usuário do seu aplicativo (por exemplo, Menus, Windows, exibições, rótulos, campos de texto) que são criadas e mantidas graficamente no Interface Builder do Xcode.

[![Um exemplo de aplicativo em execução](xib-images/intro01.png "um exemplo de aplicativo em execução")](xib-images/intro01-large.png#lightbox)

Neste artigo, abordaremos os fundamentos de trabalhar com arquivos. XIB em um aplicativo xamarin. Mac. É altamente recomendável que você trabalhe por meio de [Hello, Mac](~/mac/get-started/hello-mac.md) artigo primeiro, pois ele abrange conceitos-chave e técnicas que usaremos neste artigo.

Talvez você queira dar uma olhada o [classes expondo c# / métodos para Objective-C](~/mac/internals/how-it-works.md) seção o [recursos internos de xamarin. Mac](~/mac/internals/how-it-works.md) documentar Além disso, ele explica o `Register` e `Export` atributos usado para conectar suas classes de c# a objetos de Objective-C e da interface do usuário elementos.


## <a name="introduction-to-xcode-and-interface-builder"></a>Introdução ao Interface Builder e ao Xcode

Como parte do Xcode, a Apple criou uma ferramenta chamada Interface Builder, que permite que você crie sua Interface do usuário visualmente em um designer. Xamarin. Mac integra-se harmoniosamente ao Interface Builder, permitindo que você crie sua interface do usuário com as mesmas ferramentas que usuários Objective-C.


### <a name="components-of-xcode"></a>Componentes do Xcode

Quando você abre um arquivo. XIB no Xcode do Visual Studio para Mac, ele abre com uma **navegador de projeto** à esquerda, o **hierarquia de Interface** e **Editor de Interface** no meio e um **propriedades e utilitários** seção à direita:

[![Os componentes da UI Xcode](xib-images/xcode03.png "os componentes da UI do Xcode")](xib-images/xcode03-large.png#lightbox)

Vamos dar uma olhada no que cada um deles Xcode seções faz e como você irá usá-los para criar a interface para o seu aplicativo xamarin. Mac.


#### <a name="project-navigation"></a>Navegação do projeto

Quando você abre um arquivo. XIB para edição no Xcode, o Visual Studio para Mac cria um arquivo de projeto do Xcode em segundo plano para comunicar alterações entre si e o Xcode. Posteriormente, quando você alterna para o Visual Studio para Mac do Xcode, todas as alterações feitas neste projeto são sincronizadas com o seu projeto xamarin. Mac pelo Visual Studio para Mac.

O **navegação do projeto** seção permite que você navegue entre todos os arquivos que compõem este _shim_ projeto Xcode. Normalmente, você se interessarão nos arquivos. XIB nessa lista, como **MainMenu.xib** e **MainWindow.xib**.


#### <a name="interface-hierarchy"></a>Hierarquia de interface

O **hierarquia de Interface** seção permite que você acesse facilmente várias propriedades importantes da Interface do usuário, como seu **espaços reservados** e principal **janela**. Você também pode usar esta seção para acessar os elementos individuais (exibições) que fazem backup de sua interface do usuário e a ajustar a maneira como são aninhados arrastando-os pelo dentro da hierarquia.


#### <a name="interface-editor"></a>Editor de interface

O **Editor de Interface** seção fornece a superfície em que você graficamente layout sua Interface do usuário. Você poderá arrastar os elementos do **biblioteca** seção o **propriedades e utilitários** seção para criar o design. Conforme você adiciona elementos de interface do usuário (exibições) para a superfície de design, eles serão adicionados para o **hierarquia de Interface** seção na ordem em que aparecem na **Editor de Interface**.


#### <a name="properties--utilities"></a>Propriedades e utilitários

O **propriedades e utilitários** seção é dividida em duas seções principais, trabalharemos com, **Properties** (também chamada de inspetores) e o **biblioteca**:

![O Inspetor](xib-images/xcode04.png "Inspetor")

Inicialmente esta seção está quase vazia, no entanto se você selecionar um elemento na **Editor de Interface** ou **hierarquia de Interface**, o **propriedades** seção será preenchida informações sobre as propriedades que você pode ajustar e um determinado elemento.

Na seção **Propriedades** há 8 diferentes *guias de inspetor*, conforme mostrado na ilustração a seguir:

[![Uma visão geral de todos os inspetores](xib-images/xcode05.png "uma visão geral de todos os inspetores")](xib-images/xcode05-large.png#lightbox)

Da esquerda para a direita, essas guias são:

-   **Inspetor de Arquivo** – o Inspetor de Arquivo mostra informações do arquivo, como o nome do arquivo e o local do arquivo Xib que está sendo editado.
-   **Ajuda Rápida** – a guia Ajuda Rápida fornece ajuda contextual com base no que está selecionado no Xcode.
-   **Inspetor de Identidade** – o Inspetor de Identidade fornece informações sobre a exibição/controle selecionado.
-   **Inspetor de atributos** – o Inspetor de atributos permite que você personalize vários atributos da exibição/controle selecionado.
-   **Inspetor de tamanho** – o Inspetor de tamanho permite que você controle o tamanho e o comportamento da exibição/controle selecionado.
-   **Inspetor de conexões** – o Inspetor de conexões mostra as conexões de saída e a ação dos controles selecionados. Vamos examinar saídas e ações daqui a pouco.
-   **Inspetor de associações** – o Inspetor de associações permite que você configure controles para que seus valores são automaticamente ligados aos modelos de dados.
-   **Exibir o Inspetor de efeitos** – o Inspetor de efeitos de exibição permite que você especifique efeitos nos controles, como animações.

No **biblioteca** seção, você pode encontrar controles e objetos para colocar graficamente no designer para criam sua interface do usuário:

![Um exemplo de como o Inspetor de biblioteca](xib-images/xcode06.png "um exemplo de como o Inspetor de biblioteca")

Agora que você está familiarizado com o IDE do Xcode e Interface Builder, vamos dar uma olhada em como usá-lo para criar uma interface do usuário.


## <a name="creating-and-maintaining-windows-in-xcode"></a>Criação e manutenção do windows no Xcode

O método preferencial para a criação de Interface de um aplicativo xamarin. MAC do usuário é com Storyboards (consulte nosso [Introdução ao Storyboards](~/mac/platform/storyboards/index.md) documentação para obter mais informações) e, como resultado, qualquer novo projeto é iniciado no xamarin. Mac será Por padrão, usam Storyboards.

Para alternar para usar um. XIB com base da interface do usuário, faça o seguinte:

1. Abra o Visual Studio para Mac e iniciar um novo projeto do xamarin. Mac.
2. No **painel de soluções**, clique com botão direito no projeto e selecione **Add** > **novo arquivo...**
3. Selecione **Mac** > **controlador Windows**:

    ![Adicionando um novo controlador de janela](xib-images/setup00.png "adicionando um novo controlador de janela")
4. Insira `MainWindow` para o nome e clique o **New** botão:

    ![Adicionando uma nova janela principal](xib-images/setup01.png "adicionando uma nova janela principal")
5. Clique com botão direito no projeto novamente e selecione **Add** > **novo arquivo...**
6. Selecione **Mac** > **Menu principal**:

    ![Adicionando um novo Menu principal](xib-images/setup02.png "adicionando um novo Menu principal")
7. Deixe o nome como `MainMenu` e clique em de **New** botão.
8. No **painel de soluções** selecionar a **Main. Storyboard** do arquivo, clique com botão direito e selecione **remover**:

    ![Selecionando o storyboard principal](xib-images/setup03.png "selecionando o storyboard principal")
9. Na caixa de diálogo Remover, clique o **excluir** botão:

    ![Confirmar a exclusão](xib-images/setup04.png "confirmar a exclusão")
10. No **painel de soluções**, clique duas vezes o **Info. plist** arquivo para abri-lo para edição.
11. Selecione `MainMenu` do **Interface principal** lista suspensa:

    [![Configuração do menu principal](xib-images/setup05.png "definindo o menu principal")](xib-images/setup05-large.png#lightbox)
12. No **painel de soluções**, clique duas vezes o **MainMenu.xib** arquivo para abri-lo para edição no Interface Builder do Xcode.
13. No **Inspetor de biblioteca**, digite `object` no campo de pesquisa, em seguida, arraste um novo **objeto** na superfície de design:

    [![O menu principal de edição](xib-images/setup06.png "editando o menu principal")](xib-images/setup06-large.png#lightbox)
14. No **Inspetor de identidade**, insira `AppDelegate` para o **classe**:

    [![Selecionando o representante do aplicativo](xib-images/setup07.png "selecionando o representante do aplicativo")](xib-images/setup07-large.png#lightbox)
15. Selecione **o proprietário do arquivo** da **hierarquia de Interface**, alterne para o **Inspetor de Conexão** e arraste uma linha do delegado a ser o `AppDelegate` **Objeto** acabou de adicionar ao projeto:

    [![Conectando o representante do aplicativo](xib-images/setup08.png "conectando o representante do aplicativo")](xib-images/setup08-large.png#lightbox)
16. Salve as alterações e retorne ao Visual Studio para Mac.

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

Agora, a janela do aplicativo principal é definida em um **. XIB** arquivo incluído automaticamente no projeto ao adicionar um controlador da janela. Para editar seu design do windows, nos **painel de soluções**, clique duas vezes o **MainWindow.xib** arquivo:

![Selecionando o arquivo MainWindow.xib](xib-images/edit01.png "selecionando o arquivo MainWindow.xib")

Isso abrirá o design de janela no Interface Builder do Xcode:

[![Editando o MainWindow.xib](xib-images/edit02.png "editando o MainWindow.xib")](xib-images/edit02-large.png#lightbox)


### <a name="standard-window-workflow"></a>Fluxo de trabalho de janela padrão

Para qualquer janela que você criar e trabalhar com seu aplicativo xamarin. Mac, o processo é basicamente o mesmo:

1. Para o novo windows que não são o padrão adicionado automaticamente ao seu projeto, adicione uma nova definição de janela para o projeto.
2. Clique duas vezes no arquivo. XIB para abrir o design de janela para edição no Interface Builder do Xcode.
3. Definir propriedades de qualquer janela necessária na **Inspetor de atributo** e o **Inspetor de tamanho**.
4. Arraste os controles necessários para compilar sua interface e configurá-los de **Inspetor de atributo**.
5. Use o **Inspetor de tamanho** para lidar com o redimensionamento para elementos de interface do usuário.
6. Expor elementos de interface do usuário da janela para C# código por meio de saídas e ações.
7. Salve suas alterações e voltar para o Visual Studio para Mac sincronizar com o Xcode.


### <a name="designing-a-window-layout"></a>Criação de um layout de janela

O processo para dispor de uma Interface do usuário no construtor de Interface é basicamente o mesmo para todos os elementos que você adicionar:

1. Localizar o controle desejado na **Inspetor de biblioteca** e arraste-o para o **Editor de Interface** e posicioná-lo.
2. Definir propriedades de qualquer janela necessária na **Inspetor de atributo**.
3. Use o **Inspetor de tamanho** para lidar com o redimensionamento para elementos de interface do usuário.
4. Se você estiver usando uma classe personalizada, defina-o **Inspetor de identidade**.
5. Apresentar os elementos de interface do usuário para C# código por meio de saídas e ações.
6. Salve suas alterações e voltar para o Visual Studio para Mac sincronizar com o Xcode.

Por exemplo:

1. No Xcode, arraste um **botão de ação** da **seção Biblioteca**:

    [![Selecionando um botão da biblioteca](xib-images/xcode07.png "selecionando um botão da biblioteca")](xib-images/xcode07-large.png#lightbox)
2. Solte o botão sobre o **janela** na **Editor de Interface**:

    [![Adicionando um botão à janela](xib-images/xcode08.png "adicionando um botão à janela")](xib-images/xcode08-large.png#lightbox)
3. Clique na propriedade **Título** no **Inspetor de atributos** e altere o título do botão para `Click Me`:

    ![Definir os atributos do botão](xib-images/xcode09.png "definindo os atributos do botão")
4. Arraste um **rótulo** da **seção Biblioteca**:

    [![Selecionando um rótulo na biblioteca](xib-images/xcode10.png "selecionando um rótulo na biblioteca")](xib-images/xcode10-large.png#lightbox)
5. Solte o rótulo na **janela** ao lado do botão no **Editor de interface**:

    [![Adicionando um rótulo para a janela](xib-images/xcode11.png "adicionando um rótulo para a janela")](xib-images/xcode11-large.png#lightbox)
6. Pegue a alça direita no rótulo e arraste-a até que ela esteja perto da borda da janela:

    [![Redimensionando o rótulo](xib-images/xcode12.png "redimensionando o rótulo")](xib-images/xcode12-large.png#lightbox)
7. Com o rótulo ainda selecionado na **Editor de Interface**, alterne para o **Inspetor de tamanho**:

    ![Selecionando o Inspetor de tamanho](xib-images/xcode13.png "selecionando o Inspetor de tamanho")
8. No **caixa dimensionando** clique no **Dim colchete de vermelho** à direita e o **Dim seta Horizontal de vermelho** no Centro de:

    ![Editar as propriedades de dimensionamento automático](xib-images/xcode14.png "editando as propriedades de dimensionamento automático")
9. Isso garante que o rótulo será ampliada para aumentar ou reduzir conforme a janela é redimensionada no aplicativo em execução. O **vermelho colchetes** e a parte superior e esquerda das **dimensionando caixa** caixa informar o rótulo ficar preso às suas X e Y locais.
10. Salve suas alterações à Interface do usuário

Conforme você foram redimensionar e mover controles, você deve ter notado que o Interface Builder fornece dicas úteis de ajuste que se baseiam [diretrizes de Interface humana dos X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). Essas diretrizes ajudarão você a criar aplicativos de alta qualidade que terão uma aparência familiar para os usuários do Mac.

Se você olhar o **hierarquia de Interface** seção, observe como o layout e a hierarquia dos elementos que compõem nossa Interface do usuário são exibidos:

![Selecionando um elemento na hierarquia de Interface](xib-images/xcode15.png "selecionando um elemento na hierarquia de Interface")

A partir daqui, você pode selecionar itens para editar ou arrastar para reordenar os elementos de interface do usuário se necessário. Por exemplo, se um elemento de interface do usuário estava sendo coberto por outro elemento, você poderia arrastá-lo até a parte inferior da lista para torná-lo o item superior na janela.

Para obter mais informações sobre como trabalhar com o Windows em um aplicativo xamarin. Mac, consulte nosso [Windows](~/mac/user-interface/window.md) documentação.


## <a name="exposing-ui-elements-to-c-code"></a>Exposição dos elementos de interface do usuário ao C# código

Quando você terminar de dispor a aparência da interface do usuário no construtor de Interface, você precisará expor elementos da interface do usuário para que eles possam ser acessados de C# código. Para fazer isso, você estará usando saídas e ações.


### <a name="setting-a-custom-main-window-controller"></a>Configurar um controlador de janela principal personalizada

Para poder criar saídas e ações para expor elementos de interface do usuário ao código c#, o aplicativo xamarin. Mac precisará estar usando um controlador de janela personalizada.

Faça o seguinte:

1. Abra o Storyboard do aplicativo no Interface Builder do Xcode.
2. Selecione o `NSWindowController` na superfície de Design.
3. Alterne para o **Inspetor de identidade** exiba e insira `WindowController` como o **nome da classe**:

    [![Editar o nome da classe](xib-images/windowcontroller01.png "editando o nome de classe")](xib-images/windowcontroller01-large.png#lightbox)
4. Salve suas alterações e retorne ao Visual Studio para Mac para sincronizar.
5. Um **WindowController.cs** arquivo será adicionado ao seu projeto na **painel de soluções** no Visual Studio para Mac:

    ![O novo nome de classe no Visual Studio para Mac](xib-images/windowcontroller02.png "o novo nome de classe no Visual Studio para Mac")
6. Reabra o Storyboard no Interface Builder do Xcode.
7. O **WindowController.h** arquivo estará disponível para uso:

    [![O arquivo. h correspondente no Xcode](xib-images/windowcontroller03.png "o arquivo. h correspondente no Xcode")](xib-images/windowcontroller03-large.png#lightbox)


### <a name="outlets-and-actions"></a>Saídas e ações

Quais são saídas e ações? Na programação tradicional de interface do usuário do .NET, um controle na interface do usuário é exposto automaticamente como uma propriedade quando ele é adicionado. As coisas funcionam de modo diferente no Mac, simplesmente adicionar um controle a um modo de exibição não o torna acessível ao código. O desenvolvedor deve expor explicitamente o elemento de interface do usuário ao código. Para fazer isso, Apple nos dá duas opções:

-  **Saídas** – saídas são análogas às propriedades. Se você conectar um controle a uma tomada, ele é exposto ao seu código por meio de uma propriedade, portanto, você pode fazer coisas como anexar manipuladores de eventos, chamar métodos nele, etc.
-  **Ações** – ações são análogas ao padrão de comando no WPF. Por exemplo, quando uma ação é executada em um controle, digamos que um clique de botão, o controle chamará automaticamente um método em seu código. Ações são poderosas e conveniente, porque você pode conectar muitos controles à mesma ação.

No Xcode, saídas e ações são adicionadas diretamente ao código *arrastando o controle*. Mais especificamente, isso significa que para criar um tomada ou uma ação, você escolha qual elemento de controle que você gostaria de adicionar uma ação ou tomada, mantenha pressionada a **controle** botão no teclado e, em seguida, arrastará o controle diretamente para seu código.

Para desenvolvedores do xamarin. Mac, isso significa que você arrastar arquivos stub do Objective-C que correspondem do C# arquivo onde você deseja criar a ação ou o. O Visual Studio para Mac criou um arquivo chamado **MainWindow.h** como parte do projeto do Xcode do shim gerado para usar o Interface Builder:

[![Um exemplo de um arquivo. h no Xcode](xib-images/xcode16.png "um exemplo de um arquivo. h no Xcode")](xib-images/xcode16-large.png#lightbox)

Esse arquivo. h de stub espelha o **MainWindow.designer.cs** que é automaticamente adicionado a um projeto xamarin. Mac quando um novo `NSWindow` é criado. Esse arquivo será usado para sincronizar as alterações feitas pelo Interface Builder e é onde podemos criará suas saídas e ações para que os elementos de interface do usuário são expostos ao C# código.


#### <a name="adding-an-outlet"></a>Adicionando uma saída

Com uma compreensão básica dos quais são saídas e ações, vamos dar uma olhada na criação de uma tomada para expor um elemento de interface do usuário para seu C# código.

Faça o seguinte:

1. No Xcode, no canto direito superior da tela, clique no botão com o **círculo duplo** para abrir o **Editor assistente**:

    [![Selecionando o Editor assistente](xib-images/outlet01.png "selecionando o Editor Assistente")](xib-images/outlet01-large.png#lightbox)
2. O Xcode mudará para um modo de exibição de divisão com o **Editor de interface** em um lado e um **Editor de código** no outro.
3. Observe que Xcode selecionou automaticamente o **MainWindowController.m** arquivo na **Editor de códigos**, que é incorreto. Se você se lembra de nossa discussão sobre o que são saídas e ações acima, é necessário ter o **MainWindow.h** selecionado.
4. Na parte superior dos **Editor de códigos** clique no **vínculo automático** e selecione o **MainWindow.h** arquivo:

    [![Selecionar o arquivo. h correto](xib-images/outlet02.png "selecionando o arquivo. h correto")](xib-images/outlet02-large.png#lightbox)
5. O Xcode agora deve estar com o arquivo correto selecionado:

    [![O arquivo correto selecionado](xib-images/outlet03.png "selecionado arquivo correto")](xib-images/outlet03-large.png#lightbox)
6. **A última etapa era muito importante.** Se você não tiver selecionado arquivo correto, você não poderá criar saídas e ações ou eles serão expostos à classe errada em C#!
7. No **Editor de Interface**, mantenha pressionada a **controle** tecla no teclado e clique e arraste o rótulo que criamos acima para o editor de código logo abaixo o `@interface MainWindow : NSWindow { }` código:

    [![Arrastando para criar uma nova saída](xib-images/outlet04.png "arrastando para criar uma nova saída")](xib-images/outlet04-large.png#lightbox)
8. Uma caixa de diálogo será exibida. Deixe o **Conexão** definido como tomada e insira `ClickedLabel` para o **nome**:

    [![Definindo as propriedades de tomada](xib-images/outlet05.png "definindo as propriedades de saída")](xib-images/outlet05-large.png#lightbox)
9. Clique o **Connect** botão para criar a saída:

    ![A saída concluída](xib-images/outlet06.png "a tomada concluída")
10. Salve as alterações no arquivo.


#### <a name="adding-an-action"></a>Adicionando uma ação

Em seguida, vamos dar uma olhada na criação de uma ação para expor uma interação do usuário com o elemento de interface do usuário para seu C# código.

Faça o seguinte:

1. Verifique se estamos ainda na **Editor assistente** e o **MainWindow.h** arquivo ficará visível no **Editor de códigos**.
2. No **Editor de Interface**, mantenha pressionada a **controle** tecla no teclado e clique e arraste o botão que criamos acima para o editor de código logo abaixo o `@property (assign) IBOutlet NSTextField *ClickedLabel;` código:

    [![Arrastando para criar uma ação](xib-images/action01.png "arrastando para criar uma ação")](xib-images/action01-large.png#lightbox)
3. Alterar o **Conexão** tipo de ação:

    [![Selecione um tipo de ação](xib-images/action02.png "selecionar um tipo de ação")](xib-images/action02-large.png#lightbox)
4. Digite `ClickedButton` como o **Nome**:

    [![Configurar a ação](xib-images/action03.png "Configurando a ação")](xib-images/action03-large.png#lightbox)
5. Clique o **Connect** botão para criar a ação:

    ![A ação concluída](xib-images/action04.png "a ação concluída")
6. Salve as alterações no arquivo.

Com sua Interface de usuário conectada e exposta ao C# de código, retorne ao Visual Studio para Mac e deixe-o sincronizar as alterações do Xcode e Interface Builder.


### <a name="writing-the-code"></a>Escrevendo o código

Com sua Interface do usuário criada e seus elementos expostos ao código pelas saídas e ações, você está pronto para escrever o código para dar vida a seu programa. Por exemplo, abra o **MainWindow.cs** arquivo para edição clicando nele duas vezes na **painel de soluções**:

[![O arquivo MainWindow.cs](xib-images/code01.png "MainWindow.cs o arquivo")](xib-images/code01-large.png#lightbox)

E adicione o seguinte código para o `MainWindow` classe para trabalhar com a tomada de exemplo que você criou acima:

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

Observe que o `NSLabel` é acessado no C# pelo nome do direct que você atribuiu no Xcode, quando você criou sua tomada no Xcode, nesse caso, ele é chamado `ClickedLabel`. Você pode acessar qualquer método ou propriedade do objeto exposto da mesma maneira que faria com qualquer normal C# classe.

> [!IMPORTANT]
> Você precisa usar `AwakeFromNib`, em vez de outro método como `Initialize`, porque `AwakeFromNib` é chamado _depois_ o sistema operacional ter carregado e instanciado a Interface do usuário do arquivo. XIB. Se você tentou acessar o controle de rótulo antes do arquivo. XIB ter sido totalmente carregado e instanciado, você obteria um `NullReferenceException` erro porque o controle de rótulo não seria criado ainda.

Em seguida, adicione a seguinte classe parcial para o `MainWindow` classe:

```csharp
partial void ClickedButton (Foundation.NSObject sender) {

    // Update counter and label
    ClickedLabel.StringValue = string.Format("The button has been clicked {0} time{1}.",++numberOfTimesClicked, (numberOfTimesClicked < 2) ? "" : "s");
}
```

Esse código é anexado à ação que você criou no Xcode e Interface Builder e será chamado sempre que o usuário clica no botão.

Alguns elementos de interface do usuário automaticamente criaram em ações, por exemplo, os itens na barra de menus padrão, como o **abra...**  item de menu (`openDocument:`). No **painel de soluções**, clique duas vezes o **AppDelegate.cs** arquivo para abri-lo para edição e adicione o seguinte código abaixo a `DidFinishLaunching` método:

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

A linha chave aqui é `[Export ("openDocument:")]`, ele informa `NSMenu` que o **AppDelegate** tem um método `void OpenDialog (NSObject sender)` que responde ao `openDocument:` ação.

Para obter mais informações sobre como trabalhar com Menus, consulte nosso [Menus](~/mac/user-interface/menu.md) documentação.


## <a name="synchronizing-changes-with-xcode"></a>Sincronizando alterações com Xcode

Quando você alterna para o Visual Studio para Mac do Xcode, quaisquer alterações feitas no Xcode serão sincronizadas automaticamente com o seu projeto xamarin. Mac.

Se você selecionar o **MainWindow.designer.cs** na **painel de soluções** você poderá ver como nossa tomada e ação foram conectadas nosso C# código:

[![Sincronizando alterações com Xcode](xib-images/sync01.png "sincronizando alterações com Xcode")](xib-images/sync01-large.png#lightbox)

Observe como as duas definições na **MainWindow.designer.cs** arquivo:

```csharp
[Outlet]
AppKit.NSTextField ClickedLabel { get; set; }

[Action ("ClickedButton:")]
partial void ClickedButton (Foundation.NSObject sender);
```

Alinhar com as definições na **MainWindow.h** arquivo no Xcode:

```objc
@property (assign) IBOutlet NSTextField *ClickedLabel;
- (IBAction)ClickedButton:(id)sender;
```

Como você pode ver, o Visual Studio para Mac escuta as alterações no arquivo. h e sincroniza automaticamente essas alterações no respectivo **. designer.cs** arquivo expô-los ao seu aplicativo. Você pode perceber que também **MainWindow.designer.cs** é uma classe parcial, para que o Visual Studio para Mac não precise modificar **MainWindow.cs** que substituiria as alterações feitas à classe.

Você normalmente nunca precisará abrir o **MainWindow.designer.cs** por conta própria, ele foi apresentado aqui apenas para fins educacionais.

> [!IMPORTANT]
> Na maioria das situações, o Visual Studio para Mac será automaticamente as alterações feitas no Xcode e sincronizá-los ao seu projeto xamarin. Mac. Na improvável ocorrência de que a sincronização não aconteça automaticamente, retorne para o Xcode e depois novamente para o Visual Studio para Mac. Isso normalmente dará início a um ciclo de sincronização.


## <a name="adding-a-new-window-to-a-project"></a>Adicionando uma nova janela a um projeto

Além da janela do documento principal, talvez seja necessário exibir outros tipos de janelas para o usuário, como preferências ou painéis do Inspetor de um aplicativo xamarin. Mac. Ao adicionar uma nova janela do seu projeto você sempre deve usar o **janela do Cocoa com controlador** opção, pois isso faz com que o processo de carregamento a janela da. XIB mais fácil de arquivo.

Para adicionar uma nova janela, faça o seguinte:

1. No **painel de soluções**, clique com botão direito no projeto e selecione **Add** > **novo arquivo...** .
2. Na caixa de diálogo Novo arquivo, selecione **xamarin. Mac** > **janela do Cocoa com controlador**:

    ![Adicionando um novo controlador de janela](xib-images/new01.png "adicionando um novo controlador de janela")
3. Digite `PreferencesWindow` para o **Nome** e clique no botão **Novo**.
4. Clique duas vezes o **PreferencesWindow.xib** arquivo para abri-lo para edição no Interface Builder:

    [![Editando a janela no Xcode](xib-images/new02.png "editando a janela no Xcode")](xib-images/new02-large.png#lightbox)
5. Sua interface de design:

    [![Criando layout do windows](xib-images/new03.png "projetar o layout do windows")](xib-images/new03-large.png#lightbox)
6. Salve suas alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Adicione o seguinte código para **AppDelegate.cs** para exibir a nova janela:

```csharp
[Export("applicationPreferences:")]
void ShowPreferences (NSObject sender)
{
    var preferences = new PreferencesWindowController ();
    preferences.Window.MakeKeyAndOrderFront (this);
}
```

O `var preferences = new PreferencesWindowController ();` linha cria uma nova instância do controlador de janela que carrega a janela do arquivo. XIB e descompacta-lo. O `preferences.Window.MakeKeyAndOrderFront (this);` linha exibe a nova janela para o usuário.

Se você executar o código e selecione o **preferências...**  do **Menu aplicativo**, a janela será exibida:

![Executando o aplicativo de exemplo](xib-images/new04.png "executando o aplicativo de exemplo")

Para obter mais informações sobre como trabalhar com o Windows em um aplicativo xamarin. Mac, consulte nosso [Windows](~/mac/user-interface/window.md) documentação.


## <a name="adding-a-new-view-to-a-project"></a>Adicionando um novo modo de exibição a um projeto

Há ocasiões em que é mais fácil para decompor o design da sua janela em diversos arquivos. XIB mais gerenciáveis. Por exemplo, como alternar o conteúdo da janela principal, ao selecionar um item da barra de ferramentas em uma **janela Preferências** ou alternando conteúdo em resposta a um **lista origem** seleção.

Ao adicionar um novo modo de exibição ao seu projeto você sempre deve usar o **exibição do Cocoa com controlador** opção, pois isso faz com que o processo de carregar o modo de exibição dos. XIB mais fácil de arquivo.

Para adicionar uma nova exibição, faça o seguinte:

1. No **painel de soluções**, clique com botão direito no projeto e selecione **Add** > **novo arquivo...** .
2. Na caixa de diálogo Novo arquivo, selecione **xamarin. Mac** > **exibição do Cocoa com controlador**:

    ![Adicionando um novo modo de exibição](xib-images/view01.png "adicionando uma nova exibição")
3. Digite `SubviewTable` para o **Nome** e clique no botão **Novo**.
4. Clique duas vezes o **SubviewTable.xib** arquivo para abri-lo para edição no Interface Builder e projetar a Interface do usuário:

    [![Criando o novo modo de exibição no Xcode](xib-images/view02.png "criando o novo modo de exibição no Xcode")](xib-images/view02-large.png#lightbox)
5. Conectar-se de quaisquer ações necessárias e as saídas.
6. Salve suas alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Próxima edição do **SubviewTable.cs** e adicione o seguinte código para o **AwakeFromNib** arquivo para popular o novo modo de exibição quando ele for carregado:

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

Adicione uma enumeração para o projeto para controlar qual exibição está sendo exibidas no momento. Por exemplo, **SubviewType.cs**:

```csharp
public enum SubviewType
{
    None,
    TableView,
    OutlineView,
    ImageView
}
```

Edite o arquivo. XIB, da janela que será consumindo o modo de exibição e exibi-la. Adicionar um **modo de exibição personalizado** que atuará como o contêiner para o modo de exibição depois que ele é carregado na memória pelo C# de código e expô-lo a uma tomada chamada `ViewContainer`:

[![Criando a saída necessária](xib-images/view03.png "criando a saída necessária")](xib-images/view03-large.png#lightbox)

Salve suas alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Em seguida, edite o arquivo. cs da janela que exibirá a nova exibição (por exemplo, **MainWindow.cs**) e adicione o seguinte código:

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

Quando precisamos mostrar uma nova exibição carregada de um arquivo. XIB no contêiner da janela (a **modo de exibição personalizado** adicionadas acima), esse código manipula a remoção de qualquer modo de exibição existente e trocando-os para uma nova. Ele procura para vê-lo, que você já tem um modo de exibição, se assim ela a remove da tela. Em seguida, leva a exibição que foi passada em (como carregado de um controlador de exibição) é redimensionado para caber na área de conteúdo e o adiciona ao conteúdo para exibição.

Para exibir uma nova exibição, use o seguinte código:

```csharp
DisplaySubview(new SubviewTableController(), SubviewType.TableView);
```

Isso cria uma nova instância do controlador de exibição para a nova exibição a ser exibido, define seu tipo (conforme especificado pela enumeração adicionada ao projeto) e usa o `DisplaySubview` adicionado à classe da janela para exibir o modo de exibição do método. Por exemplo:

[![Executando o aplicativo de exemplo](xib-images/view04.png "executando o aplicativo de exemplo")](xib-images/view04-large.png#lightbox)

Para obter mais informações sobre como trabalhar com o Windows em um aplicativo xamarin. Mac, consulte nosso [Windows](~/mac/user-interface/window.md) e [caixas de diálogo](~/mac/user-interface/dialog.md) documentação.


## <a name="summary"></a>Resumo

Este artigo apresentou uma visão detalhada de como trabalhar com arquivos. XIB em um aplicativo xamarin. Mac. Vimos os diferentes tipos e os usos dos arquivos. XIB para criar a Interface do usuário do seu aplicativo, como criar e manter. XIB arquivos do Xcode Interface Builder e como trabalhar com arquivos. XIB no C# código.


## <a name="related-links"></a>Links relacionados

- [MacImages (amostra)](https://developer.xamarin.com/samples/mac/MacImages/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Windows](~/mac/user-interface/window.md)
- [Menus](~/mac/user-interface/menu.md)
- [Caixas de diálogo](~/mac/user-interface/dialog.md)
- [Trabalhando com imagens](~/mac/app-fundamentals/image.md)
- [Diretrizes de Interface Humana do macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
