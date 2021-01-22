---
title: arquivos. xib no Xamarin. Mac
description: Este artigo aborda como trabalhar com arquivos. xib criados no Interface Builder do Xcode para criar e manter interfaces de usuário para um aplicativo Xamarin. Mac.
ms.prod: xamarin
ms.assetid: 6AF3D216-448D-4B2D-9026-74E4FFF5923A
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: ec1005f3956498f1525c65b5d505f2f899aee2e2
ms.sourcegitcommit: 513feb0e07558766e3de4a898e53d56b27c20559
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98697664"
---
# <a name="xib-files-in-xamarinmac"></a>arquivos. xib no Xamarin. Mac

_Este artigo aborda como trabalhar com arquivos. xib criados no Interface Builder do Xcode para criar e manter interfaces de usuário para um aplicativo Xamarin. Mac._

> [!NOTE]
> A maneira preferida de criar uma interface do usuário para um aplicativo Xamarin. Mac é com storyboards. Esta documentação foi deixada em vigor por motivos históricos e para trabalhar com projetos anteriores do Xamarin. Mac. Para obter mais informações, consulte nossa [introdução à documentação de storyboards](~/mac/platform/storyboards/index.md) .

## <a name="overview"></a>Visão geral

Ao trabalhar com C# e .NET em um aplicativo Xamarin. Mac, você tem acesso aos mesmos elementos e ferramentas de interface do usuário que um desenvolvedor trabalhando no *Objective-C* e no *Xcode* . Como o Xamarin. Mac se integra diretamente com o Xcode, você pode usar o _interface Builder_ do Xcode para criar e manter suas interfaces de usuário (ou, opcionalmente, criá-las diretamente no código C#).

Um arquivo. xib é usado pelo macOS para definir elementos da interface do usuário do seu aplicativo (como menus, janelas, exibições, rótulos, campos de texto) que são criados e mantidos graficamente no Interface Builder do Xcode.

[![Um exemplo do aplicativo em execução](xib-images/intro01.png "Um exemplo do aplicativo em execução")](xib-images/intro01-large.png#lightbox)

Neste artigo, abordaremos as noções básicas de como trabalhar com arquivos. xib em um aplicativo Xamarin. Mac. É altamente recomendável que você trabalhe primeiro com o artigo [Hello, Mac](~/mac/get-started/hello-mac.md) , pois ele aborda os principais conceitos e técnicas que usaremos neste artigo.

Talvez você queira dar uma olhada na seção [expondo classes/métodos C# para Objective-C](~/mac/internals/how-it-works.md) do documento [interno do Xamarin. Mac](~/mac/internals/how-it-works.md) também, explica os `Register` `Export` atributos e usados para conectar suas classes C# a objetos Objective-C e elementos de interface do usuário.

## <a name="introduction-to-xcode-and-interface-builder"></a>Introdução ao Interface Builder e ao Xcode

Como parte do Xcode, a Apple criou uma ferramenta chamada Interface Builder, que permite criar sua interface do usuário visualmente em um designer. O Xamarin. Mac integra-se de Fluent com o Interface Builder, permitindo que você crie sua interface do usuário com as mesmas ferramentas que os usuários do Objective-C fazem.

### <a name="components-of-xcode"></a>Componentes do Xcode

Quando você abre um arquivo. xib no Xcode de Visual Studio para Mac, ele é aberto com um **navegador de projeto** à esquerda, a hierarquia de **interface** e o editor de **interface** no meio e uma seção **Propriedades & Utilities** à direita:

[![Os componentes da interface do usuário do Xcode](xib-images/xcode03.png "Os componentes da interface do usuário do Xcode")](xib-images/xcode03-large.png#lightbox)

Vamos dar uma olhada no que cada uma dessas seções do Xcode faz e como você as usará para criar a interface para seu aplicativo Xamarin. Mac.

#### <a name="project-navigation"></a>Navegação do projeto

Quando você abre um arquivo. xib para edição no Xcode, Visual Studio para Mac cria um arquivo de projeto do Xcode em segundo plano para comunicar as alterações entre si mesmo e o Xcode. Posteriormente, quando você voltar para Visual Studio para Mac do Xcode, todas as alterações feitas nesse projeto serão sincronizadas com o projeto Xamarin. Mac por Visual Studio para Mac.

A seção de **navegação do projeto** permite que você navegue entre todos os arquivos que compõem esse projeto de _correção_ do Xcode. Normalmente, você só terá interesse nos arquivos. xib desta lista, como **MainMenu. xib** e **MainWindow. xib**.

#### <a name="interface-hierarchy"></a>Hierarquia de interface

A seção **hierarquia de interface** permite que você acesse facilmente várias propriedades de chave da interface do usuário, como seus **espaços reservados** e **janela** principal. Você também pode usar esta seção para acessar os elementos individuais (exibições) que compõem a interface do usuário e ajustar a forma como eles são aninhados arrastando-os para dentro da hierarquia.

#### <a name="interface-editor"></a>Editor de interface

A seção **Editor de interface** fornece a superfície na qual você faz o layout gráfico da interface do usuário. Você arrastará elementos da seção **biblioteca** da seção **Propriedades & utilitários** para criar o design. À medida que você adiciona elementos de interface do usuário (exibições) à superfície de design, eles serão adicionados à seção **hierarquia de interface** na ordem em que aparecem no **Editor de interface**.

#### <a name="properties--utilities"></a>& utilitários de propriedades

A seção de **& de utilitários de propriedades** é dividida em duas seções principais com as quais trabalharemos, **Propriedades** (também chamadas de inspetores) e a **biblioteca**:

![O Inspetor de propriedades](xib-images/xcode04.png "O Inspetor de propriedades")

Inicialmente, esta seção está quase vazia. no entanto, se você selecionar um elemento no **Editor de interface** ou na **hierarquia de interface**, a seção **Propriedades** será preenchida com informações sobre o elemento e as propriedades fornecidas que você pode ajustar.

Na seção **Propriedades** há 8 diferentes *guias de inspetor*, conforme mostrado na ilustração a seguir:

[![Uma visão geral de todos os inspetores](xib-images/xcode05.png "Uma visão geral de todos os Inspetores")](xib-images/xcode05-large.png#lightbox)

Da esquerda para a direita, essas guias são:

- **Inspetor de Arquivo** – o Inspetor de Arquivo mostra informações do arquivo, como o nome do arquivo e o local do arquivo Xib que está sendo editado.
- **Ajuda Rápida** – a guia Ajuda Rápida fornece ajuda contextual com base no que está selecionado no Xcode.
- **Inspetor de Identidade** – o Inspetor de Identidade fornece informações sobre a exibição/controle selecionado.
- **Inspetor de atributos** – o Inspetor de atributos permite que você personalize vários atributos do controle/exibição selecionado.
- **Inspetor de tamanho** – o Inspetor de tamanho permite que você controle o tamanho e o comportamento de redimensionamento do controle/exibição selecionado.
- **Inspetor de conexões** – o Inspetor de conexões mostra a tomada e as conexões de ação dos controles selecionados. Vamos examinar saídas e ações em um momento.
- **Inspetor de associações** – o Inspetor de associações permite que você configure controles para que seus valores sejam associados automaticamente a modelos de dados.
- **Inspetor de exibição de efeitos** – o Inspetor de efeitos de exibição permite que você especifique efeitos sobre os controles, como animações.

Na seção **biblioteca** , você pode encontrar controles e objetos para colocá-los no designer a fim de criar graficamente sua interface do usuário:

![Um exemplo do Inspetor de biblioteca](xib-images/xcode06.png "Um exemplo do Inspetor de biblioteca")

Agora que você está familiarizado com o Interface Builder e o IDE do Xcode, vamos examinar o uso dele para criar uma interface do usuário.

## <a name="creating-and-maintaining-windows-in-xcode"></a>Criando e mantendo janelas no Xcode

O método preferencial para criar uma interface do usuário do aplicativo Xamarin. Mac é com storyboards (consulte [a documentação introdução aos storyboards](~/mac/platform/storyboards/index.md) para obter mais informações) e, como resultado, qualquer projeto novo iniciado no Xamarin. Mac usará storyboards por padrão.

Para alternar para o usando uma interface do usuário baseada em. xib, faça o seguinte:

1. Abra Visual Studio para Mac e inicie um novo projeto Xamarin. Mac.
2. Na **painel de soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar**  >  **novo arquivo...**
3. Selecione   >  **controlador do Windows** Mac:

    ![Adicionando um novo controlador de janela](xib-images/setup00.png "Adicionando um novo controlador de janela")
4. Insira `MainWindow` para o nome e clique no botão **novo** :

    ![Adicionando uma nova janela principal](xib-images/setup01.png "Adicionando uma nova janela principal")
5. Clique com o botão direito do mouse no projeto novamente e selecione **Adicionar**  >  **novo arquivo...**
6. Selecione o  >  **menu principal** do Mac:

    ![Adicionando um novo menu principal](xib-images/setup02.png "Adicionando um novo menu principal")
7. Deixe o nome como `MainMenu` e clique no botão **novo** .
8. No **painel de soluções** selecione o arquivo **Main. Storyboard** , clique com o botão direito do mouse e selecione **remover**:

    ![Selecionando o storyboard principal](xib-images/setup03.png "Selecionando o storyboard principal")
9. Na caixa de diálogo remover, clique no botão **excluir** :

    ![Confirmando a exclusão](xib-images/setup04.png "Confirmando a exclusão")
10. No **painel de soluções**, clique duas vezes no arquivo **info. plist** para abri-lo para edição.
11. Selecione `MainMenu` na lista suspensa **interface principal** :

    [![Configurando o menu principal](xib-images/setup05.png "Configurando o menu principal")](xib-images/setup05-large.png#lightbox)
12. No **painel de soluções**, clique duas vezes no arquivo **MainMenu. xib** para abri-lo para edição no interface Builder do Xcode.
13. No **Inspetor de biblioteca**, digite `object` o campo de pesquisa e, em seguida, arraste um novo **objeto** para a superfície de design:

    [![Editando o menu principal](xib-images/setup06.png "Editando o menu principal")](xib-images/setup06-large.png#lightbox)
14. No **Inspetor de identidade**, insira `AppDelegate` para a **classe**:

    [![Selecionando o representante do aplicativo](xib-images/setup07.png "Selecionando o representante do aplicativo")](xib-images/setup07-large.png#lightbox)
15. Selecione **proprietário do arquivo** na **hierarquia de interface**, alterne para o **Inspetor de conexão** e arraste uma linha do delegado para o `AppDelegate` **objeto** que acabou de ser adicionado ao projeto:

    [![Conectando o representante do aplicativo](xib-images/setup08.png "Conectando o representante do aplicativo")](xib-images/setup08-large.png#lightbox)
16. Salve as alterações e retorne a Visual Studio para Mac.

Com todas essas alterações em vigor, edite o arquivo **AppDelegate.cs** e faça com que ele se pareça com o seguinte:

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

Agora, a janela principal do aplicativo é definida em um arquivo **. xib** automaticamente incluído no projeto ao adicionar um controlador de janela. Para editar seu design do Windows, na **painel de soluções**, clique duas vezes no arquivo **MainWindow. xib** :

![Selecionando o arquivo MainWindow. xib](xib-images/edit01.png "Selecionando o arquivo MainWindow. xib")

Isso abrirá o design da janela na Interface Builder do Xcode:

[![Editando MainWindow. xib](xib-images/edit02.png "Editando MainWindow. xib")](xib-images/edit02-large.png#lightbox)

### <a name="standard-window-workflow"></a>Fluxo de trabalho de janela padrão

Para qualquer janela que você criar e trabalhar com o aplicativo Xamarin. Mac, o processo é basicamente o mesmo:

1. Para novas janelas que não são o padrão adicionado automaticamente ao seu projeto, adicione uma nova definição de janela ao projeto.
2. Clique duas vezes no arquivo. xib para abrir o design da janela para edição no Interface Builder do Xcode.
3. Defina todas as propriedades de janela necessárias no **Inspetor de atributo** e no Inspetor de **tamanho**.
4. Arraste os controles necessários para criar sua interface e configurá-los no **Inspetor de atributo**.
5. Use o **Inspetor de tamanho** para lidar com o redimensionamento dos elementos da interface do usuário.
6. Expor os elementos da interface do usuário da janela para código C# por meio de saídas e ações.
7. Salve suas alterações e volte para Visual Studio para Mac para sincronizar com o Xcode.

### <a name="designing-a-window-layout"></a>Criando um layout de janela

O processo para dispor uma interface do usuário no construtor de interface é basicamente o mesmo para todos os elementos que você adicionar:

1. Localize o controle desejado no **Inspetor de biblioteca** e arraste-o para o **Editor de interface** e posicione-o.
2. Defina todas as propriedades de janela necessárias no **Inspetor de atributo**.
3. Use o **Inspetor de tamanho** para lidar com o redimensionamento dos elementos da interface do usuário.
4. Se você estiver usando uma classe personalizada, defina-a no **Inspetor de identidade**.
5. Expor os elementos da interface do usuário para código C# por meio de saídas e ações.
6. Salve suas alterações e volte para Visual Studio para Mac para sincronizar com o Xcode.

Por exemplo:

1. No Xcode, arraste um **botão de ação** da **seção Biblioteca**:

    [![Selecionando um botão da biblioteca](xib-images/xcode07.png "Selecionando um botão da biblioteca")](xib-images/xcode07-large.png#lightbox)
2. Solte o botão na **janela** no editor de **interface**:

    [![Adicionando um botão à janela](xib-images/xcode08.png "Adicionando um botão à janela")](xib-images/xcode08-large.png#lightbox)
3. Clique na propriedade **Título** no **Inspetor de atributos** e altere o título do botão para `Click Me`:

    ![Definindo os atributos do botão](xib-images/xcode09.png "Definindo os atributos do botão")
4. Arraste um **rótulo** da **seção Biblioteca**:

    [![Selecionando um rótulo na biblioteca](xib-images/xcode10.png "Selecionando um rótulo na biblioteca")](xib-images/xcode10-large.png#lightbox)
5. Solte o rótulo na **janela** ao lado do botão no **Editor de interface**:

    [![Adicionando um rótulo à janela](xib-images/xcode11.png "Adicionando um rótulo à janela")](xib-images/xcode11-large.png#lightbox)
6. Pegue a alça direita no rótulo e arraste-a até que ela esteja perto da borda da janela:

    [![Redimensionando o rótulo](xib-images/xcode12.png "Redimensionando o rótulo")](xib-images/xcode12-large.png#lightbox)
7. Com o rótulo ainda selecionado no **Editor de interface**, alterne para o **Inspetor de tamanho**:

    ![Selecionando o Inspetor de tamanho](xib-images/xcode13.png "Selecionando o Inspetor de tamanho")
8. Na **caixa dimensionamento** automático, clique no **colchete vermelho Dim** à direita e na **seta horizontal vermelha Dim** no centro:

    ![Editando as propriedades de dimensionamento automático](xib-images/xcode14.png "Editando as propriedades de dimensionamento automático")
9. Isso garante que o rótulo será ampliado para aumentar e diminuir à medida que a janela for redimensionada no aplicativo em execução. Os **colchetes vermelhos** e a parte superior e esquerda da caixa da **caixa de dimensionamento** automático dizem ao rótulo que está preso a seus locais X e Y.
10. Salve as alterações na interface do usuário

Como você estava redimensionando e movendo controles, deve ter notado que Interface Builder fornece dicas de ajuste úteis baseadas nas diretrizes de [interface humana do os X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). Essas diretrizes ajudarão você a criar aplicativos de alta qualidade que terão uma aparência familiar para os usuários do Mac.

Se você examinar a seção **hierarquia de interface** , observe como o layout e a hierarquia dos elementos que compõem a interface do usuário são mostrados:

![Selecionando um elemento na Hierarquia de Interface](xib-images/xcode15.png "Selecionando um elemento na Hierarquia de Interface")

A partir daqui, você pode selecionar itens para editar ou arrastar para reordenar os elementos da interface do usuário, se necessário. Por exemplo, se um elemento de interface do usuário estava sendo coberto por outro elemento, você poderia arrastá-lo para a parte inferior da lista para torná-lo o item mais alto na janela.

Para obter mais informações sobre como trabalhar com o Windows em um aplicativo Xamarin. Mac, consulte a documentação do [Windows](~/mac/user-interface/window.md) .

## <a name="exposing-ui-elements-to-c-code"></a>Expondo elementos da interface do usuário para código C#

Depois de terminar de dispor a aparência da sua interface do usuário no Interface Builder, você precisará expor elementos da interface de usuário para que eles possam ser acessados a partir do código C#. Para fazer isso, você usará ações e saídas.

### <a name="setting-a-custom-main-window-controller"></a>Configurando um controlador de janela principal personalizado

Para poder criar saídas e ações para expor elementos da interface do usuário para o código C#, o aplicativo Xamarin. Mac precisará usar um controlador de janela personalizado.

Faça o seguinte:

1. Abra o storyboard do aplicativo no Interface Builder do Xcode.
2. Selecione o `NSWindowController` no design Surface.
3. Alterne para a exibição do **Inspetor de identidade** e insira `WindowController` como o **nome da classe**:

    [![Editando o nome da classe](xib-images/windowcontroller01.png "Editando o nome da classe")](xib-images/windowcontroller01-large.png#lightbox)
4. Salve as alterações e retorne ao Visual Studio para Mac para sincronização.
5. Um arquivo **WindowController.cs** será adicionado ao seu projeto no **painel de soluções** no Visual Studio para Mac:

    ![O nome da nova classe no Visual Studio para Mac](xib-images/windowcontroller02.png "O nome da nova classe no Visual Studio para Mac")
6. Reabra o storyboard no Interface Builder do Xcode.
7. O arquivo **WindowController. h** estará disponível para uso:

    [![O arquivo. h correspondente no Xcode](xib-images/windowcontroller03.png "O arquivo. h correspondente no Xcode")](xib-images/windowcontroller03-large.png#lightbox)

### <a name="outlets-and-actions"></a>Saídas e ações

O que são saídas e ações? Na programação tradicional de interface do usuário do .NET, um controle na interface do usuário é exposto automaticamente como uma propriedade quando ele é adicionado. As coisas funcionam de modo diferente no Mac, simplesmente adicionar um controle a um modo de exibição não o torna acessível ao código. O desenvolvedor deve expor explicitamente o elemento de interface do usuário ao código. Para fazer isso, a Apple nos dá duas opções:

- **Saídas** – saídas são análogas às propriedades. Se você conectar um controle a uma tomada, ele será exposto ao seu código por meio de uma propriedade, para que você possa fazer coisas como anexar manipuladores de eventos, chamar métodos nele, etc.
- **Ações** – ações são análogas ao padrão de comando no WPF. Por exemplo, quando uma ação é executada em um controle, digamos que um clique de botão, o controle chamará automaticamente um método em seu código. As ações são poderosas e convenientes, pois você pode conectar muitos controles à mesma ação.

No Xcode, saídas e ações são adicionadas diretamente no código por meio do *recurso de arrastar*. Mais especificamente, isso significa que, para criar uma tomada ou ação, você escolhe o elemento de controle que deseja adicionar uma tomada ou ação, mantém o botão de **controle** no teclado e arrasta esse controle diretamente para o seu código.

Para desenvolvedores do Xamarin. Mac, isso significa que você arrasta para os arquivos stub do Objective-C que correspondem ao arquivo C# no qual você deseja criar a tomada ou ação. Visual Studio para Mac criou um arquivo chamado **MainWindow. h** como parte do projeto do Shim Xcode gerado para usar o Interface Builder:

[![Um exemplo de um arquivo. h no Xcode](xib-images/xcode16.png "Um exemplo de um arquivo. h no Xcode")](xib-images/xcode16-large.png#lightbox)

Esse arquivo stub. h espelha o **MainWindow.designer.cs** que é adicionado automaticamente a um projeto Xamarin. Mac quando um novo `NSWindow` é criado. Esse arquivo será usado para sincronizar as alterações feitas por Interface Builder e é onde criaremos suas saídas e ações para que os elementos da interface do usuário sejam expostos ao código C#.

#### <a name="adding-an-outlet"></a>Adicionando uma tomada

Com um entendimento básico do que são saídas e ações, vamos dar uma olhada na criação de uma tomada para expor um elemento de interface do usuário para seu código C#.

Faça o seguinte:

1. No Xcode, no canto direito superior da tela, clique no botão com o **círculo duplo** para abrir o **Editor assistente**:

    [![Selecionando o editor do assistente](xib-images/outlet01.png "Selecionando o editor do assistente")](xib-images/outlet01-large.png#lightbox)
2. O Xcode mudará para um modo de exibição de divisão com o **Editor de interface** em um lado e um **Editor de código** no outro.
3. Observe que o Xcode selecionou automaticamente o arquivo **MainWindowController. m** no **Editor de códigos**, que está incorreto. Se você se lembrar da nossa discussão sobre o que as saídas e as ações estão acima, precisamos ter o **MainWindow. h** selecionado.
4. Na parte superior do **Editor de código** , clique no **link automático** e selecione o arquivo **MainWindow. h** :

    [![Selecionando o arquivo. h correto](xib-images/outlet02.png "Selecionando o arquivo. h correto")](xib-images/outlet02-large.png#lightbox)
5. O Xcode agora deve estar com o arquivo correto selecionado:

    [![O arquivo correto selecionado](xib-images/outlet03.png "O arquivo correto selecionado")](xib-images/outlet03-large.png#lightbox)
6. **A última etapa era muito importante.** Se você não tiver o arquivo correto selecionado, não poderá criar saídas e ações ou elas serão expostas à classe incorreta em C#!
7. No **Editor de interface**, mantenha pressionada a tecla **Control** no teclado e clique-arraste o rótulo que criamos acima para o editor de código logo abaixo do `@interface MainWindow : NSWindow { }` código:

    [![Arrastando para criar uma nova tomada](xib-images/outlet04.png "Arrastando para criar uma nova tomada")](xib-images/outlet04-large.png#lightbox)
8. Uma caixa de diálogo será exibida. Deixe a **conexão** definida para a tomada e insira `ClickedLabel` para o **nome**:

    [![Definindo as propriedades da tomada](xib-images/outlet05.png "Definindo as propriedades da tomada")](xib-images/outlet05-large.png#lightbox)
9. Clique no botão **conectar** para criar a tomada:

    ![A tomada concluída](xib-images/outlet06.png "A tomada concluída")
10. Salve as alterações no arquivo.

#### <a name="adding-an-action"></a>Adicionando uma ação

Em seguida, vamos dar uma olhada na criação de uma ação para expor uma interação do usuário com o elemento UI para seu código C#.

Faça o seguinte:

1. Verifique se ainda estamos no editor do **Assistente** e se o arquivo **MainWindow. h** está visível no **Editor de códigos**.
2. No **Editor de interface**, mantenha pressionada a tecla **Control** no teclado e clique-arraste o botão que criamos acima para o editor de código logo abaixo do `@property (assign) IBOutlet NSTextField *ClickedLabel;` código:

    [![Arrastando para criar uma ação](xib-images/action01.png "Arrastando para criar uma Ação")](xib-images/action01-large.png#lightbox)
3. Altere o tipo de **conexão** para ação:

    [![Selecionar um tipo de ação](xib-images/action02.png "Selecionar um tipo de ação")](xib-images/action02-large.png#lightbox)
4. Digite `ClickedButton` como o **Nome**:

    [![Configurando a ação](xib-images/action03.png "Configurando a ação")](xib-images/action03-large.png#lightbox)
5. Clique no botão **conectar** para criar a ação:

    ![A ação concluída](xib-images/action04.png "A ação concluída")
6. Salve as alterações no arquivo.

Com a interface do usuário conectada e exposta ao código C#, volte para Visual Studio para Mac e deixe que ele sincronize as alterações do Xcode e do Interface Builder.

### <a name="writing-the-code"></a>Escrevendo o código

Com a interface do usuário criada e seus elementos da interface do usuário expostos ao código por meio de saídas e ações, você está pronto para escrever o código para dar vida ao seu programa. Por exemplo, abra o arquivo **MainWindow.cs** para edição clicando duas vezes nele no **painel de soluções**:

[![O arquivo MainWindow.cs](xib-images/code01.png "O arquivo MainWindow.cs")](xib-images/code01-large.png#lightbox)

E adicione o seguinte código à `MainWindow` classe para trabalhar com a tomada de exemplo que você criou acima:

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

Observe que o `NSLabel` é acessado em C# pelo nome direto que você atribuiu a ele no Xcode quando você criou sua tomada no Xcode, nesse caso, ele é chamado `ClickedLabel` . Você pode acessar qualquer método ou Propriedade do objeto exposto da mesma maneira que faria com qualquer classe C# normal.

> [!IMPORTANT]
> Você precisa usar `AwakeFromNib` , em vez de outro método, como `Initialize` , porque `AwakeFromNib` é chamado _depois_ que o sistema operacional tiver carregado e instanciado a interface do usuário a partir do arquivo. xib. Se você tentou acessar o controle rótulo antes que o arquivo. xib tenha sido totalmente carregado e instanciado, você receberia um `NullReferenceException` erro porque o controle rótulo não seria criado ainda.

Em seguida, adicione a seguinte classe parcial à `MainWindow` classe:

```csharp
partial void ClickedButton (Foundation.NSObject sender) {

    // Update counter and label
    ClickedLabel.StringValue = string.Format("The button has been clicked {0} time{1}.",++numberOfTimesClicked, (numberOfTimesClicked < 2) ? "" : "s");
}
```

Esse código é anexado à ação que você criou no Xcode e Interface Builder e será chamado sempre que o usuário clicar no botão.

Alguns elementos da interface do usuário têm automaticamente ações internas, por exemplo, itens na barra de menus padrão, como o item de menu **abrir...** ( `openDocument:` ). No **painel de soluções**, clique duas vezes no arquivo **AppDelegate.cs** para abri-lo para edição e adicione o seguinte código abaixo do `DidFinishLaunching` método:

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

A principal linha aqui é `[Export ("openDocument:")]` que ela informa `NSMenu` que o **AppDelegate** tem um método `void OpenDialog (NSObject sender)` que responde à `openDocument:` ação.

Para obter mais informações sobre como trabalhar com menus, consulte a documentação de nossos [menus](~/mac/user-interface/menu.md) .

## <a name="synchronizing-changes-with-xcode"></a>Sincronizando alterações com Xcode

Quando você voltar para Visual Studio para Mac do Xcode, as alterações feitas no Xcode serão automaticamente sincronizadas com seu projeto Xamarin. Mac.

Se você selecionar o **MainWindow.designer.cs** no **painel de soluções** poderá ver como nossa tomada e ação foram conectadas em nosso código C#:

[![Sincronizando alterações com o Xcode](xib-images/sync01.png "Sincronizando alterações com o Xcode")](xib-images/sync01-large.png#lightbox)

Observe como as duas definições no arquivo **MainWindow.designer.cs** :

```csharp
[Outlet]
AppKit.NSTextField ClickedLabel { get; set; }

[Action ("ClickedButton:")]
partial void ClickedButton (Foundation.NSObject sender);
```

Alinhar com as definições no arquivo **MainWindow. h** no Xcode:

```objc
@property (assign) IBOutlet NSTextField *ClickedLabel;
- (IBAction)ClickedButton:(id)sender;
```

Como você pode ver, Visual Studio para Mac escuta as alterações no arquivo. h e, em seguida, sincroniza automaticamente essas alterações no respectivo arquivo **. designer.cs** para expô-las ao seu aplicativo. Você também pode observar que **MainWindow.designer.cs** é uma classe parcial, de modo que Visual Studio para Mac não precisa modificar **MainWindow.cs** que substituiria as alterações feitas na classe.

Normalmente, você nunca precisará abrir o **MainWindow.designer.cs** por conta própria, ele foi apresentado aqui apenas para fins educacionais.

> [!IMPORTANT]
> Na maioria das situações, Visual Studio para Mac verá automaticamente todas as alterações feitas no Xcode e as sincronizará com o projeto do Xamarin. Mac. Na improvável ocorrência de que a sincronização não aconteça automaticamente, retorne para o Xcode e depois novamente para o Visual Studio para Mac. Isso normalmente dará início a um ciclo de sincronização.

## <a name="adding-a-new-window-to-a-project"></a>Adicionando uma nova janela a um projeto

Além da janela principal do documento, um aplicativo Xamarin. Mac pode precisar exibir outros tipos de janelas para o usuário, como preferências ou painéis de inspetores. Ao adicionar uma nova janela ao seu projeto, você sempre deve usar a **janela Cocoa com** a opção Controller, pois isso torna o processo de carregar a janela do arquivo. xib mais fácil.

Para adicionar uma nova janela, faça o seguinte:

1. Na **painel de soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar**  >  **novo arquivo..**.
2. Na caixa de diálogo novo arquivo, selecione janela **Xamarin. Mac**  >  **Cocoa com controlador**:

    ![Adicionando um novo controlador de janela](xib-images/new01.png "Adicionando um novo controlador de janela")
3. Digite `PreferencesWindow` para o **Nome** e clique no botão **Novo**.
4. Clique duas vezes no arquivo **PreferencesWindow. xib** para abri-lo para edição no interface Builder:

    [![Editando a janela no Xcode](xib-images/new02.png "Editando a janela no Xcode")](xib-images/new02-large.png#lightbox)
5. Projete sua interface:

    [![Criando o layout do Windows](xib-images/new03.png "Criando o layout do Windows")](xib-images/new03-large.png#lightbox)
6. Salve as alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Adicione o seguinte código a **AppDelegate.cs** para exibir a nova janela:

```csharp
[Export("applicationPreferences:")]
void ShowPreferences (NSObject sender)
{
    var preferences = new PreferencesWindowController ();
    preferences.Window.MakeKeyAndOrderFront (this);
}
```

A `var preferences = new PreferencesWindowController ();` linha cria uma nova instância do controlador de janela que carrega a janela do arquivo. xib e a replana. A `preferences.Window.MakeKeyAndOrderFront (this);` linha exibe a nova janela para o usuário.

Se você executar o código e selecionar as **preferências...** no **menu do aplicativo**, a janela será exibida:

![Captura de tela mostra a janela Preferências exibida no menu do aplicativo.](xib-images/new04.png "Executando o aplicativo de exemplo")

Para obter mais informações sobre como trabalhar com o Windows em um aplicativo Xamarin. Mac, consulte a documentação do [Windows](~/mac/user-interface/window.md) .

## <a name="adding-a-new-view-to-a-project"></a>Adicionando uma nova exibição a um projeto

Há ocasiões em que é mais fácil dividir o design da janela em vários arquivos. xib mais gerenciáveis. Por exemplo, como alternar o conteúdo da janela principal ao selecionar um item da barra de ferramentas em uma **janela de preferências** ou alternar o conteúdo em resposta a uma seleção de lista de **origem** .

Ao adicionar uma nova exibição ao seu projeto, você sempre deve usar a opção **Cocoa View with Controller** , pois isso torna o processo de carregar o modo de exibição do arquivo. xib mais fácil.

Para adicionar uma nova exibição, faça o seguinte:

1. Na **painel de soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar**  >  **novo arquivo..**.
2. Na caixa de diálogo novo arquivo, selecione **Xamarin. Mac**  >  **Cocoa exibir com controlador**:

    ![Adicionando uma nova exibição](xib-images/view01.png "Adicionando uma nova exibição")
3. Digite `SubviewTable` para o **Nome** e clique no botão **Novo**.
4. Clique duas vezes no arquivo **subviewtable. xib** para abri-lo para edição no interface Builder e criar a interface do usuário:

    [![Criando a nova exibição no Xcode](xib-images/view02.png "Criando a nova exibição no Xcode")](xib-images/view02-large.png#lightbox)
5. Conecte todas as ações e saídas necessárias.
6. Salve as alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Em seguida, edite o **SubviewTable.cs** e adicione o seguinte código ao arquivo **AwakeFromNib** para popular a nova exibição quando ela for carregada:

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

Adicione um enum ao projeto para controlar qual exibição está sendo exibida no momento. Por exemplo, **SubviewType.cs**:

```csharp
public enum SubviewType
{
    None,
    TableView,
    OutlineView,
    ImageView
}
```

Edite o arquivo. xib da janela que irá consumir a exibição e exibi-la. Adicione uma **exibição personalizada** que atuará como o contêiner para o modo de exibição depois que ele for carregado na memória pelo código C# e expô-lo a uma tomada chamada `ViewContainer` :

[![Criando a tomada necessária](xib-images/view03.png "Criando a tomada necessária")](xib-images/view03-large.png#lightbox)

Salve as alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Em seguida, edite o arquivo. cs da janela que exibirá o novo modo de exibição (por exemplo, **MainWindow.cs**) e adicione o seguinte código:

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

Quando precisamos mostrar uma nova exibição carregada de um arquivo. xib no contêiner da janela (a **exibição personalizada** adicionada acima), esse código lida com a remoção de qualquer exibição existente e a permuta para o novo. Parece que você já tem um modo de exibição exibido, caso ele o remova da tela. Em seguida, ele usa o modo de exibição que foi passado (como carregado de um controlador de exibição) redimensiona-o para caber na área de conteúdo e o adiciona ao conteúdo para exibição.

Para exibir uma nova exibição, use o seguinte código:

```csharp
DisplaySubview(new SubviewTableController(), SubviewType.TableView);
```

Isso cria uma nova instância do controlador de exibição para a nova exibição a ser exibida, define seu tipo (conforme especificado pela enumeração adicionada ao projeto) e usa o `DisplaySubview` método adicionado à classe da janela para realmente exibir a exibição. Por exemplo:

[![Captura de tela mostra a exibição de tabela selecionada na janela trabalhando com imagens.](xib-images/view04.png "Executando o aplicativo de exemplo")](xib-images/view04-large.png#lightbox)

Para obter mais informações sobre como trabalhar com o Windows em um aplicativo Xamarin. Mac, consulte nossa documentação sobre [janelas](~/mac/user-interface/window.md) e [caixas de diálogo](~/mac/user-interface/dialog.md) .

## <a name="summary"></a>Resumo

Este artigo deu uma visão detalhada de como trabalhar com arquivos. xib em um aplicativo Xamarin. Mac. Vimos os diferentes tipos e usos dos arquivos. xib para criar a interface do usuário do aplicativo, como criar e manter arquivos. xib no Interface Builder do Xcode e como trabalhar com arquivos. xib no código C#.

## <a name="related-links"></a>Links Relacionados

- [MacImages (amostra)](/samples/xamarin/mac-samples/macimages)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Windows](~/mac/user-interface/window.md)
- [Menus](~/mac/user-interface/menu.md)
- [Diálogos](~/mac/user-interface/dialog.md)
- [Trabalhando com imagens](~/mac/app-fundamentals/image.md)
- [Diretrizes de Interface Humana do macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)