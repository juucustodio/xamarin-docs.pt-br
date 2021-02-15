---
title: Olá, Mac – passo a passo
description: Este documento demonstra como criar um aplicativo Xamarin.Mac e apresenta o Visual Studio para Mac, o Xcode e o Interface Builder. Ele discute a exposição de controles de interface do usuário ao código por meio de saídas e ações e ilustra como compilar, executar e testar um aplicativo Xamarin.Mac.
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 37D0E9E6-979B-7069-B3BE-C5F0AF99BA72
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 09/02/2018
ms.openlocfilehash: 49a4597aec37c1a9be550f62f4f97dd8fe7def08
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430621"
---
# <a name="hello-mac-walkthrough"></a>Olá, Mac – passo a passo

O Xamarin.Mac permite desenvolver aplicativos do Mac totalmente nativos em C# e .NET com as mesmas APIs do macOS, que são usadas no desenvolvimento em *Objective-C* ou *Swift*. Como o Xamarin.Mac se integra diretamente com o Xcode. O desenvolvedor pode usar o _Interface Builder_ do Xcode para criar as interfaces de usuário de um aplicativo ou, opcionalmente, criá-las diretamente em código C#.

Além disso, considerando que os aplicativos Xamarin.Mac são desenvolvidos em C# e .NET, o código pode ser compartilhado com aplicativos móveis Xamarin.iOS e Xamarin.Android, oferecendo simultaneamente uma experiência nativa em cada plataforma.

Este artigo apresentará os principais conceitos necessários para criar um aplicativo do Mac usando o Interface Builder do Xamarin.Mac, do Visual Studio para Mac e do Xcode e guiará você pelo processo de criação de um simples aplicativo **Hello, Mac**, que conta o número de vezes que um botão foi clicado:

[![Exemplo do aplicativo Hello, Mac em execução](hello-mac-images/run02-sml.png)](hello-mac-images/run02.png#lightbox)

Os seguintes conceitos serão abordados:

- **Visual Studio para Mac** – Introdução ao Visual Studio para Mac e como criar aplicativos Xamarin.Mac com ele.
- **Anatomia de um aplicativo de Xamarin.Mac** – de que um aplicativo Xamarin.Mac consiste.
- **Interface Builder do Xcode** – como usar o Interface Builder do Xcode para definir a interface do usuário de um aplicativo.
- **Saídas e ações** – como usar saídas e ações para conectar controles na interface do usuário.
- **Teste/implantação** – como executar e testar um aplicativo Xamarin.Mac.

## <a name="requirements"></a>Requisitos

O desenvolvimento de aplicativos Xamarin.Mac requer:

- Um computador Mac executando o macOS High Sierra (10.13) ou superior.
- [Xcode 10 ou superior](https://itunes.apple.com/us/app/xcode/id497799835?mt=12).
- A versão mais recente do [Xamarin.Mac e do Visual Studio para Mac](/visualstudio/mac/installation/).

Para executar um aplicativo criado com o Xamarin.Mac, você precisará do seguinte:

- Um computador Mac com macOS 10.7 ou posterior.

> [!WARNING]
> A próxima versão do Xamarin.Mac 4.8 dará suporte somente a macOS 10.9 ou posterior.
> As versões anteriores do Xamarin.Mac eram compatíveis com macOS 10.7 ou superior, mas essas versões mais antigas do macOS não têm infraestrutura TLS suficiente para dar suporte ao TLS 1.2. Para macOS 10.7 ou macOS 10.8, use o Xamarin.Mac 4.6 ou anterior.

## <a name="starting-a-new-xamarinmac-app-in-visual-studio-for-mac"></a>Iniciando um novo aplicativo Xamarin.Mac no Visual Studio para Mac

Como mencionado acima, este guia explica as etapas para criar um aplicativo Mac chamado `Hello_Mac` que adiciona um único botão e um rótulo para a janela principal. Quando o botão for clicado, o rótulo exibirá o número de vezes que isso ocorreu.

Para começar, execute uma destas etapas:

1. Inicie o Visual Studio para Mac:

    [![A interface de Visual Studio para Mac principal](hello-mac-images/setup01-sml.png)](hello-mac-images/setup01.png#lightbox)

2. Clique no botão **Novo Projeto...** para abrir a caixa de diálogo **Novo Projeto**, selecione **Mac** > **Aplicativo** > **Aplicativo Cocoa** e clique no botão **Avançar**:

    [![Selecionando um aplicativo Cocoa](hello-mac-images/setup02-sml.png)](hello-mac-images/setup02.png#lightbox)

3. Digite `Hello_Mac` para o **Nome do Aplicativo** e mantenha todo o restante como padrão. Clique em **Avançar**:

    [![Definindo o nome do aplicativo](hello-mac-images/setup03-sml.png)](hello-mac-images/setup03.png#lightbox)

4. Confirme o local do novo projeto no computador:

    [![Verificando os detalhes da nova solução](hello-mac-images/setup04-sml.png)](hello-mac-images/setup04.png#lightbox)

5. Selecione o botão **Criar**.

O Visual Studio para Mac criará o novo aplicativo Xamarin.Mac e exibirá os arquivos padrão que são adicionados à solução do aplicativo:

[![A nova exibição padrão da solução](hello-mac-images/project01-sml.png)](hello-mac-images/project01.png#lightbox)

O Visual Studio para Mac usa a mesma estrutura de **Solução** e **Projeto** que o Visual Studio 2019. Uma solução é um contêiner que pode conter um ou mais projetos; os projetos podem incluir aplicativos, bibliotecas de suporte, aplicativos de teste, etc. O **arquivo > novo** modelo de projeto cria uma solução e um projeto de aplicativo automaticamente.

## <a name="anatomy-of-a-xamarinmac-application"></a>Anatomia de um aplicativo Xamarin.Mac

A programação de aplicativos Xamarin.Mac é muito semelhante a trabalhar com o Xamarin.iOS. O iOS usa a estrutura CocoaTouch, que é uma versão reduzida do Cocoa, usado pelo Mac.

Examine os arquivos no projeto:

- **Main.cs** contém o ponto de entrada principal do aplicativo. Quando o aplicativo é iniciado, a classe `Main` contém o primeiro método que é executado.
- **AppDelegate.cs** contém a classe `AppDelegate` que é responsável por escutar eventos do sistema operacional.
- **Info.plist** contém as propriedades do aplicativo, como nome, ícones do aplicativo etc.
- **Entitlements.plist** contém os direitos do aplicativo e permite acesso a itens como suporte a iCloud e Área Restrita.
- **Main.storyboard** define a interface do usuário (Janelas e Menus) de um aplicativo e estabelece as interconexões entre Janelas via Segues. Storyboards são arquivos XML que contêm a definição de modos de exibição (elementos de interface do usuário). Esse arquivo pode ser criado e mantido pelo Interface Builder dentro do Xcode.
- **ViewController.cs** é o controlador da janela principal. Controladores serão abordados em detalhes em outro artigo, mas por enquanto, um controlador pode ser considerado o mecanismo principal de qualquer modo de exibição específico.
- **ViewController.designer.cs** contém o código de conexão que ajuda a integrar com a interface do usuário da tela principal.

Nas seções a seguir você dará uma olhada rápida em alguns desses arquivos. Posteriormente eles serão explorados mais detalhadamente, mas é uma boa ideia compreender seus fundamentos agora.

### <a name="maincs"></a>Main.cs

O arquivo **Main.cs** é muito simples. Ele contém um método estático `Main` que cria uma nova instância do aplicativo Xamarin.Mac e passa o nome da classe que manipulará eventos de SO, que nesse caso é a classe `AppDelegate`:

```csharp
using System;
using System.Drawing;
using Foundation;
using AppKit;
using ObjCRuntime;

namespace Hello_Mac
{
    class MainClass
    {
        static void Main (string[] args)
        {
            NSApplication.Init ();
            NSApplication.Main (args);
        }
    }
}
```

### <a name="appdelegatecs"></a>AppDelegate.cs

O `AppDelegate.cs` arquivo contém uma classe `AppDelegate`, que é responsável por criar janelas e escutar eventos do SO:

```csharp
using AppKit;
using Foundation;

namespace Hello_Mac
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        public AppDelegate ()
        {
        }

        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }
    }
}
```

Esse código é provavelmente é desconhecido, a menos que o desenvolvedor tenha criado um aplicativo iOS antes; no entanto, ele é bastante simples.

O `DidFinishLaunching` método é executado depois de o aplicativo ter sido instanciado e é responsável por realmente criar a janela do aplicativo e iniciar o processo de exibir o modo de exibição dentro dela.

O `WillTerminate` método será chamado quando o usuário ou o sistema tiver instanciado um desligamento do aplicativo. O desenvolvedor deve usar esse método para finalizar o aplicativo antes de encerrar (como salvar as preferências do usuário ou o tamanho e a localização da janela).

### <a name="viewcontrollercs"></a>ViewController.cs

Cocoa (e por derivação, CocoaTouch) usa o que é conhecido como o padrão *MVC* (Modelo – Exibição – Controlador). A declaração `ViewController` representa o objeto que controla a janela real do aplicativo. Em geral, para cada janela criada (e para muitas outras coisas dentro de janelas), há um controlador responsável pelo ciclo de vida da janela, como exibi-la, adicionar novos modos de exibição (controles) a ela etc.

A classe `ViewController` é o controlador da janela principal. O controlador é responsável pelo ciclo de vida da janela principal. Isso será examinado em detalhes mais tarde; por enquanto, dê agora uma rápida olhada nele:

```csharp
using System;

using AppKit;
using Foundation;

namespace Hello_Mac
{
    public partial class ViewController : NSViewController
    {
        public ViewController (IntPtr handle) : base (handle)
        {
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any additional setup after loading the view.
        }

        public override NSObject RepresentedObject {
            get {
                return base.RepresentedObject;
            }
            set {
                base.RepresentedObject = value;
                // Update the view, if already loaded.
            }
        }
    }
}
```

### <a name="viewcontrollerdesignercs"></a>ViewController.Designer.cs

O arquivo de designer da classe Janela Principal está inicialmente vazio, mas será automaticamente preenchido pelo Visual Studio para Mac porque a interface do usuário é criada com o Interface Builder do Xcode:

```csharp
// WARNING
//
// This file has been generated automatically by Visual Studio for Mac to store outlets and
// actions made in the UI designer. If it is removed, they will be lost.
// Manual changes to this file may not be handled correctly.
//
using Foundation;

namespace Hello_Mac
{
    [Register ("ViewController")]
    partial class ViewController
    {
        void ReleaseDesignerOutlets ()
        {
        }
    }
}
```

Os arquivos de designer não devem ser editados diretamente porque são gerenciados automaticamente pelo Visual Studio para Mac para fornecer o código de conexão que permite o acesso aos controles que foram adicionados a janelas ou modos de exibição no aplicativo.

Com o projeto de aplicativo Xamarin.Mac criado e uma compreensão básica de seus componentes, mude para o Xcode para criar a interface do usuário usando o Interface Builder.

### <a name="infoplist"></a>Info.plist

O arquivo `Info.plist` contém informações sobre o aplicativo Xamarin.Mac como seu **Nome** e **Identificador do Pacote**:

[![O editor do Visual Studio para Mac plist](hello-mac-images/infoplist01.png)](hello-mac-images/infoplist01.png#lightbox)

Também define o _Storyboard_ que será usado para exibir a interface do usuário para o aplicativo Xamarin.Mac na lista suspensa **Interface Principal**. No exemplo anterior, `Main` na lista suspensa está relacionado ao `Main.storyboard` na árvore fonte do projeto, no **Gerenciador de Soluções**. Ele também define os ícones do aplicativo, especificando o *Catálogo de Ativos* que os contém (neste caso, **AppIcons**).

### <a name="entitlementsplist"></a>Entitlements.plist

O aplicativo `Entitlements.plist` arquivo controla os direitos que o aplicativo Xamarin.Mac tem, como **Área Restrita** e **iCloud**:

[![O editor de direitos Visual Studio para Mac](hello-mac-images/entitlements01.png)](hello-mac-images/entitlements01.png#lightbox)

Para o exemplo Hello World, nenhum direito será necessário. A seção a seguir mostra como usar o Interface Builder do Xcode para editar o arquivo **Main.storyboard** e definir a IU do arquivo Xamarin.Mac.

## <a name="introduction-to-xcode-and-interface-builder"></a>Introdução ao Interface Builder e ao Xcode

Como parte do Xcode, a Apple criou uma ferramenta chamada Interface Builder, que permite que um desenvolvedor crie uma interface do usuário visualmente em um designer. O Xamarin.Mac integra-se harmoniosamente ao Interface Builder, permitindo que a interface do usuário seja criada com as mesmas ferramentas que usuários Objective-C.

Para começar, clique duas vezes no arquivo `Main.storyboard` no **Gerenciador de Soluções** para abri-lo para edição no Xcode e Interface Builder:

[![O arquivo. Storyboard principal no Gerenciador de Soluções](hello-mac-images/xcode01.png)](hello-mac-images/xcode01.png#lightbox)

Isso deve iniciar o Xcode e ter a aparência da seguinte captura de tela:

[![A exibição padrão do Xcode Interface Builder](hello-mac-images/xcode02.png)](hello-mac-images/xcode02.png#lightbox)

Antes de começar a projetar a interface, confira uma visão geral rápida do Xcode para orientá-lo sobre os principais recursos que serão usados.

> [!NOTE]
> O desenvolvedor não precisa usar o Xcode e o Interface Builder para criar a interface do usuário de um aplicativo Xamarin.Mac, pois a interface do usuário pode ser criada diretamente no código C#, mas isso está fora do escopo deste artigo. Para simplificar, ele usará o Interface Builder para criar a interface do usuário em todo o restante deste tutorial.

### <a name="components-of-xcode"></a>Componentes do Xcode

Quando abre um arquivo **.storyboard** no Xcode do Visual Studio para Mac, ele abre com um **Navegador de Projeto** à esquerda, a **Hierarquia de Interface** e o **Editor de Interface** no centro, e uma seção **Propriedades e Utilitários** à direita:

[![As várias seções de Interface Builder no Xcode](hello-mac-images/xcode03.png)](hello-mac-images/xcode03.png#lightbox)

Nas seções a seguir, dê uma olhada no que cada um desses recursos do Xcode faz e como usá-los para criar a interface para um aplicativo Xamarin.Mac.

### <a name="project-navigation"></a>Navegação do Projeto

Quando abre um arquivo **.storyboard** para edição no Xcode, o Visual Studio para Mac cria um *Arquivo de Projeto do Xcode* em segundo plano para comunicar alterações entre o próprio programa e o Xcode. Posteriormente, quando o desenvolvedor retorna do Xcode para o Visual Studio para Mac, alterações feitas neste projeto são sincronizadas com o projeto Xamarin.Mac pelo Visual Studio para Mac.

A seção de **navegação do projeto** permite que o desenvolvedor Navegue entre todos os arquivos que compõem esse projeto do _Shim do ajuste_ . Normalmente, eles se interessarão nos arquivos `.storyboard` nessa lista, por exemplo, `Main.storyboard`.

### <a name="interface-hierarchy"></a>Hierarquia de interface

A seção **hierarquia de interface** permite que o desenvolvedor acesse facilmente várias propriedades de chave da interface do usuário, como seus **espaços reservados** e **janela**principal. Esta seção pode ser usada para acessar os elementos individuais (exibições) que compõem a interface do usuário e ajustar a maneira como são aninhados arrastando-os pelo interior da hierarquia.

### <a name="interface-editor"></a>Editor de interface

A seção **Editor de interface** fornece a superfície na qual a interface do usuário é disposta graficamente. Arraste elementos da seção **biblioteca** da seção **Propriedades & utilitários** para criar o design. Conforme elementos de interface do usuário (exibições) são adicionados à área de design, eles serão adicionados à seção **Hierarquia de interface** na ordem em que aparecem no **Editor de interface**.

### <a name="properties--utilities"></a>Propriedades e utilitários

A seção **Propriedades e utilitários** está dividida em duas seções principais, **Propriedades** (também chamada de Inspetores) e a **Biblioteca**:

[![O Inspetor de propriedades](hello-mac-images/xcode04.png)](hello-mac-images/xcode04.png#lightbox)

Inicialmente esta seção está quase vazia, no entanto, se o desenvolvedor selecionar um elemento no **Editor de interface** ou **Hierarquia de interface**, a seção **Propriedades** será populada com informações sobre o elemento fornecido e as propriedades que elas podem ajustar.

Na seção **Propriedades** há oito *guias de inspetor* diferentes, conforme mostrado na ilustração a seguir:

[![Uma visão geral de todos os inspetores](hello-mac-images/xcode05.png)](hello-mac-images/xcode05.png#lightbox)

### <a name="properties--utility-types"></a>Tipos de propriedades e utilitários

Da esquerda para a direita, essas guias são:

- **Inspetor de Arquivo** – o Inspetor de Arquivo mostra informações do arquivo, como o nome do arquivo e o local do arquivo Xib que está sendo editado.
- **Ajuda Rápida** – a guia Ajuda Rápida fornece ajuda contextual com base no que está selecionado no Xcode.
- **Inspetor de Identidade** – o Inspetor de Identidade fornece informações sobre a exibição/controle selecionado.
- **Inspetor de Atributos** – o Inspetor de Atributos permite que o desenvolvedor personalize vários atributos da exibição/controle selecionado.
- **Inspetor de Tamanho** – o Inspetor de Tamanho permite que o desenvolvedor controle o tamanho e o comportamento da exibição/controle selecionado.
- **Inspetor de Conexões** – o Inspetor de Conexões mostra as conexões de **Saída** e **Ação** dos controles selecionados. As Saídas e as Ações serão discutidas abaixo em detalhes.
- **Inspetor de Associações** – o Inspetor de Associações permite que o desenvolvedor configure controles para que seus valores sejam associados automaticamente aos modelos de dados.
- **Inspetor de Efeitos de Exibição** – o Inspetor de Efeitos de Exibição permite que o desenvolvedor especifique efeitos nos controles, assim como animações.

Use a seção **Biblioteca** para encontrar controles e objetos para colocar no designer para compilar graficamente a interface do usuário:

[![O inspetor da biblioteca do Xcode](hello-mac-images/xcode06.png)](hello-mac-images/xcode06.png#lightbox)

## <a name="creating-the-interface"></a>Criando a Interface

Com os fundamentos básicos do IDE Xcode e do Interface Builder abordados, o desenvolvedor pode criar a interface do usuário para a exibição principal.

Para usar o Interface Builder, faça o seguinte:

1. No Xcode, arraste um **botão de ação** da **seção Biblioteca**:

    [![Selecionando um NSButton no Inspetor de biblioteca](hello-mac-images/xcode07.png)](hello-mac-images/xcode07.png#lightbox)

2. Solte o botão sobre a **exibição** (sob o **controlador da janela**) no **Editor de interface**:

    [![Adicionando um botão ao design da interface](hello-mac-images/xcode08.png)](hello-mac-images/xcode08.png#lightbox)

3. Clique na propriedade **Title**, no **Inspetor de Atributos**, e altere o título do botão para **Clique em mim**:

    [![Definindo as propriedades do botão](hello-mac-images/xcode09.png)](hello-mac-images/xcode09.png#lightbox)

4. Arraste um **rótulo** da **seção Biblioteca**:

    [![Selecionando um rótulo no Inspetor de biblioteca](hello-mac-images/xcode10.png)](hello-mac-images/xcode10.png#lightbox)

5. Solte o rótulo na **janela** ao lado do botão no **Editor de interface**:

    [![Adicionando um rótulo ao design da interface](hello-mac-images/xcode11.png)](hello-mac-images/xcode11.png#lightbox)

6. Pegue a alça direita no rótulo e arraste-a até que ela esteja perto da borda da janela:

    [![Redimensionando o rótulo](hello-mac-images/xcode12.png)](hello-mac-images/xcode12.png#lightbox)

7. Selecione o Botão recém-adicionado ao **Editor de Interface** e clique no ícone do **Editor de Restrições** na parte inferior da janela:

    [![Adicionando restrições ao botão](hello-mac-images/xcode13.png)](hello-mac-images/xcode13.png#lightbox)

8. Na parte superior do editor, clique nas **formas de I vermelhas** na parte superior e na esquerda. Como a janela é redimensionada, isso manterá o botão no mesmo local, no canto superior esquerdo da tela.

9. Em seguida, marque as caixas **Altura** e **Largura** e use os tamanhos padrão. Isso mantém o botão do mesmo tamanho quando a janela é redimensionada.

10. Clique no botão **Adicionar 4 Restrições** para adicionar as restrições e feche o editor.

11. Selecione o rótulo e clique no ícone do **Editor de restrições** novamente:

    [![Adicionando restrições ao rótulo](hello-mac-images/xcode14.png)](hello-mac-images/xcode14.png#lightbox)

12. Clicar nas **formas de I vermelhas** acima, à direita e à esquerda do **Editor de restrições** informa ao rótulo que este deve ficar preso às suas localizações X e Y e aumentar ou reduzir conforme a janela é redimensionada no aplicativo em execução.

13. Novamente, marque a caixa **Altura** e use o tamanho padrão, então clique no botão **Adicionar 4 Restrições** para adicionar as restrições e feche o editor.

14. Salve as alterações à interface do usuário.

Quando redimensionar e mover controles, observe que o Interface Builder fornece dicas úteis de ajuste com base nas [Diretrizes de Interface Humana do macOS](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/). Essas diretrizes ajudarão o desenvolvedor a criar aplicativos de alta qualidade que terão uma aparência e proporcionarão uma sensação familiares para os usuários do Mac.

Procure na seção **hierarquia de interface** para ver como o layout e a hierarquia dos elementos que compõem a interface do usuário são mostrados:

[![Selecionando um elemento na hierarquia de interface](hello-mac-images/xcode15.png)](hello-mac-images/xcode15.png#lightbox)

Aqui, o desenvolvedor pode selecionar itens para editar ou para arrastar a fim de reordenar os elementos de interface do usuário, se necessário. Por exemplo, se um elemento de interface do usuário estava sendo coberto por outro elemento, eles poderiam arrastá-lo até a parte inferior da lista para torná-lo o item mais alto na janela.

Com a interface de usuário criada, o desenvolvedor precisa expor os itens de interface do usuário de modo que o Xamarin.Mac possa acessá-los e interagir com eles em código C#. A próxima seção, **Saídas e ações**, mostra como fazer isso.

### <a name="outlets-and-actions"></a>Saídas e ações

Então, o que são **saídas** e **ações**? Na programação de interface do usuário tradicional do .NET, um controle na interface do usuário é exposto automaticamente como uma propriedade quando adicionado. As coisas funcionam de modo diferente no Mac, simplesmente adicionar um controle a um modo de exibição não o torna acessível ao código. O desenvolvedor deve expor explicitamente o elemento de interface do usuário ao código. Para fazer isso, a Apple oferece duas opções:

- **Saídas** – saídas são análogas às propriedades. Se o desenvolvedor conectar um controle a uma saída, ele será exposto ao código por meio de uma propriedade, de modo que eles possam fazer coisas como anexar manipuladores de eventos, chamar métodos nele, etc.
- **Ações** – ações são análogas ao padrão de comando no WPF. Por exemplo, quando uma ação for executada em um controle, por exemplo um clique de botão, o controle chamará automaticamente um método no código. Ações são poderosas e práticas, porque o desenvolvedor pode conectar muitos controles à mesma ação.

No Xcode, **saídas** e **ações** são adicionadas diretamente ao código *arrastando-se controles*. Mais especificamente, isso significa que, para criar uma **Saída** ou **Ação**, o desenvolvedor escolherá um elemento de controle ao qual adicionar uma **Saída** ou **Ação**, manterá pressionada a tecla **Control** no teclado e, em seguida, arrastará o controle diretamente para o código.

Para desenvolvedores de Xamarin.Mac, isso significa que o desenvolvedor arrastará arquivos stub que correspondam ao arquivo C# em que eles desejam criar a **saída** ou **ação** para dentro do Objective-C. O Visual Studio para Mac criou um arquivo chamado `ViewController.h` como parte do projeto do Xcode do shim gerado para usar o Interface Builder:

[![Exibindo fonte no Xcode](hello-mac-images/xcode16-sml.png)](hello-mac-images/xcode16.png#lightbox)

Esse arquivo stub `.h` espelha o `ViewController.designer.cs` que é automaticamente adicionado a um projeto Xamarin.Mac quando um novo `NSWindow` é criado. Esse arquivo será usado para sincronizar as alterações feitas pelo Interface Builder e é nele que as **saídas** e **ações** são criadas de modo que os elementos de interface do usuário sejam expostos ao código C#.

#### <a name="adding-an-outlet"></a>Adicionando uma saída

Com uma compreensão básica do que **saídas** e **ações** são, crie uma **saída** para expor o rótulo criado ao nosso código C#.

Faça o seguinte:

1. No Xcode, no canto direito superior da tela, clique no botão com o **círculo duplo** para abrir o **Editor assistente**:

    [![Exibindo o editor do assistente](hello-mac-images/outlet01.png)](hello-mac-images/outlet01.png#lightbox)

2. O Xcode mudará para um modo de exibição de divisão com o **Editor de interface** em um lado e um **Editor de código** no outro.

3. Observe que Xcode selecionou automaticamente o arquivo **ViewController.m** no **Editor de código**, o que é incorreto. Com base na discussão acima sobre o que são **saídas** e **ações**, o desenvolvedor precisará selecionar o **ViewController.h**.

4. Na parte superior do **Editor de código**, clique no **Vínculo Automático** e selecione o arquivo `ViewController.h`:

    [![Selecionando o arquivo correto](hello-mac-images/outlet02.png)](hello-mac-images/outlet02.png#lightbox)

5. O Xcode agora deve estar com o arquivo correto selecionado:

    [![Exibindo o arquivo ViewController. h](hello-mac-images/outlet03.png)](hello-mac-images/outlet03.png#lightbox)

6. **A última etapa era muito importante**: sem selecionar o arquivo correto, não é possível criar **Saídas** e **Ações** ou esses itens são expostos à classe incorreta no C#!

7. No **Editor de interface**, mantenha pressionada a tecla **Control** no teclado e clique e arraste o rótulo criado acima para o editor de código logo abaixo do código `@interface ViewController : NSViewController {}`:

    [![Arrastando para criar uma tomada](hello-mac-images/outlet04.png)](hello-mac-images/outlet04.png#lightbox)

8. Uma caixa de diálogo será exibida. Deixe a **Conexão** definida como **Saída** e digite `ClickedLabel` para o **Nome**:

    [![Definindo a tomada](hello-mac-images/outlet05.png)](hello-mac-images/outlet05.png#lightbox)

9. Clique no botão **Conectar** para criar a **saída**:

    [![Exibindo a tomada final](hello-mac-images/outlet06.png)](hello-mac-images/outlet06.png#lightbox)

10. Salve as alterações no arquivo.

#### <a name="adding-an-action"></a>Adicionando uma ação

Em seguida, exponha o botão ao código C#. Assim como o rótulo acima, o desenvolvedor pode conectar o botão a uma **saída**. Já que apenas queremos responder ao botão sendo clicado, use uma **ação** em vez disso.

Faça o seguinte:

1. Certifique-se de que o Xcode ainda está no **Editor assistente** e que o arquivo **ViewController.h** está visível no **Editor de código**.

2. No **Editor de interface**, mantenha pressionada a tecla **Control** no teclado e clique e arraste o botão criado acima para o editor de código logo abaixo do código `@property (assign) IBOutlet NSTextField *ClickedLabel;`:

    [![Arrastando para criar uma ação](hello-mac-images/action01.png)](hello-mac-images/action01.png#lightbox)

3. Alterar o tipo de **Conexão** para **Ação**:

    [![Definindo a ação](hello-mac-images/action02.png)](hello-mac-images/action02.png#lightbox)

4. Digite `ClickedButton` como o **Nome**:

    [![Nomeando a nova ação](hello-mac-images/action03.png)](hello-mac-images/action03.png#lightbox)

5. Clique no botão **Conectar** para criar a **ação**:

    [![Exibindo a ação final](hello-mac-images/action04.png)](hello-mac-images/action04.png#lightbox)

6. Salve as alterações no arquivo.

Com a interface do usuário conectada e exposta ao código C#, retorne para o Visual Studio para Mac e deixe-o sincronizar as alterações feitas no Xcode e no Interface Builder.

> [!NOTE]
> Provavelmente demorava muito para criar a interface do usuário e as **ações** e **saídas** para esse primeiro aplicativo, e pode parecer muito trabalho, mas muitos conceitos novos foram introduzidos e muito tempo era gasto cobrindo um novo aterramento. Após praticar por um tempo e trabalhar com o Interface Builder, essa interface e todas as suas **Saídas** e **Ações** podem ser criadas em apenas um ou dois minutos.

### <a name="synchronizing-changes-with-xcode"></a>Sincronizando alterações com o Xcode

Quando o desenvolvedor retorna do Xcode para o Visual Studio para Mac, alterações feitas no Xcode serão automaticamente sincronizadas com o projeto Xamarin.Mac.

Selecione o **ViewController.designer.cs** no **Gerenciador de Soluções** para ver como a **Saída** e a **Ação** foram conectadas ao código C#:

[![Sincronizando alterações com o Xcode](hello-mac-images/sync01-sml.png)](hello-mac-images/sync01.png#lightbox)

Observe como as duas definições no arquivo **ViewController.designer.cs**:

```csharp
[Outlet]
AppKit.NSTextField ClickedLabel { get; set; }

[Action ("ClickedButton:")]
partial void ClickedButton (Foundation.NSObject sender);
```

Se alinham às definições do arquivo `ViewController.h` no Xcode:

```objc
@property (assign) IBOutlet NSTextField *ClickedLabel;
- (IBAction)ClickedButton:(id)sender;
```

O Visual Studio para Mac escuta em busca de alterações no arquivo **.h** e sincroniza automaticamente essas alterações no respectivo arquivo **.designer.cs** para expô-las ao aplicativo. Observe que **ViewController.designer.cs** é uma classe parcial para que o Visual Studio para Mac não precise modificar **ViewController.cs**, o que substituiria todas as alterações feitas pelo desenvolvedor à classe.

Normalmente o desenvolvedor nunca precisará abrir o **ViewController.designer.cs**, ele foi apresentado aqui apenas para fins educacionais.

> [!NOTE]
> Na maioria das situações, o Visual Studio para Mac perceberá automaticamente as alterações feitas no Xcode e as sincronizará ao projeto Xamarin.Mac. Na improvável ocorrência de que a sincronização não aconteça automaticamente, retorne para o Xcode e depois novamente para o Visual Studio para Mac. Isso normalmente dará início a um ciclo de sincronização.

## <a name="writing-the-code"></a>Escrevendo o código

Com a interface do usuário criada e seus elementos expostos ao código pelas **Saídas** e **Ações**, estamos finalmente prontos para escrever o código para dar vida ao programa.

Para este aplicativo de exemplo, cada vez que o botão for clicado, o rótulo será atualizado para mostrar o número de vezes que ele foi clicado. Para fazer isso, abra o arquivo `ViewController.cs` para edição clicando nele duas vezes no **Gerenciador de Soluções**:

[![Exibindo o arquivo ViewController.cs no Visual Studio para Mac](hello-mac-images/code01-sml.png)](hello-mac-images/code01.png#lightbox)

Primeiro, crie uma variável de nível de classe na classe `ViewController` para acompanhar o número de cliques que ocorreram. Edite a definição de classe e torne-a semelhante ao seguinte:

```csharp
namespace Hello_Mac
{
    public partial class ViewController : NSViewController
    {
        private int numberOfTimesClicked = 0;
        ...
```

Em seguida, na mesma classe (`ViewController`), substitua o método `ViewDidLoad` e adicione algum código para definir a mensagem inicial para o rótulo:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Set the initial value for the label
    ClickedLabel.StringValue = "Button has not been clicked yet.";
}
```

Use `ViewDidLoad` , em vez de outro método, como `Initialize` , porque `ViewDidLoad` é chamado *depois* que o sistema operacional tiver carregado e instanciado a interface do usuário a partir do arquivo **. Storyboard** . Se o desenvolvedor tivesse tentado acessar o controle de rótulo antes do arquivo **.storyboard** ter sido totalmente carregado e instanciado, um erro `NullReferenceException` seria gerado, porque o controle de rótulo ainda não teria sido criado.

Em seguida, adicione o código para responder ao usuário que clica no botão. Adicione o método parcial a seguir à classe `ViewController`:

```csharp
partial void ClickedButton (Foundation.NSObject sender) {
    // Update counter and label
    ClickedLabel.StringValue = string.Format("The button has been clicked {0} time{1}.",++numberOfTimesClicked, (numberOfTimesClicked < 2) ? "" : "s");
}
```

Esse código é anexado à **ação** criada no Xcode e o Interface Builder será chamado sempre que o usuário clicar no botão.

## <a name="testing-the-application"></a>Testando o aplicativo

É hora de criar e executar o aplicativo para verificar se que ele é executado conforme o esperado. O desenvolvedor pode compilá-lo e executá-lo em uma única etapa ou pode compilá-lo sem executá-lo.

Sempre que um aplicativo é compilado, o desenvolvedor pode escolher o tipo de build desejado:

- **Depuração** – um build de depuração é compilado em um arquivo **.app** (aplicativo) com um montante de metadados extras, que permitem ao desenvolvedor depurar o que está acontecendo enquanto o aplicativo está em execução.
- **Versão** – um build de versão também cria um arquivo **.app**, mas este não inclui informações de depuração, então é menor e executa mais rapidamente.

O desenvolvedor pode selecionar o tipo de build do **Seletor de Configuração**, no canto superior esquerdo da tela do Visual Studio para Mac:

[![Selecionando uma compilação de depuração](hello-mac-images/run01-sml.png)](hello-mac-images/run01.png#lightbox)

## <a name="building-the-application"></a>Compilando o aplicativo

No caso deste exemplo desejamos apenas um build de depuração, portanto, certifique-se de que **Depuração** está selecionado. Compile o aplicativo primeiro pressionando **⌘B** ou, no menu **Compilar**, escolhendo **Compilar Todos**.

Se não houver erros, uma mensagem de **Build bem-sucedido** será exibida na barra de status do Visual Studio para Mac. Se houver erros, examine o projeto e certifique-se de que as etapas acima foram seguidas corretamente. Comece confirmando que o código (tanto no Xcode quanto no Visual Studio para Mac) corresponde ao código no tutorial.

## <a name="running-the-application"></a>Executando o aplicativo

Há três maneiras de executar o aplicativo:

- Pressione **⌘ + Enter**.
- No menu **Executar**, escolha **Depuração**.
- Clique no botão **Reproduzir** na barra de ferramentas do Visual Studio para Mac (imediatamente acima do **Gerenciador de Soluções**).

O aplicativo será compilado (se ainda não tiver sido), iniciará no modo de depuração e exibirá sua janela da interface principal:

[![Executando o aplicativo](hello-mac-images/run02-sml.png)](hello-mac-images/run02.png#lightbox)

Se o botão for clicado algumas vezes, o rótulo deverá ser atualizado com a contagem:

[![Mostrando os resultados de clicar no botão](hello-mac-images/run03-sml.png)](hello-mac-images/run03.png#lightbox)

## <a name="where-to-next"></a>Para onde ir em seguida

Após ter aprendido noções básicas de como trabalhar com um aplicativo Xamarin.Mac, confira os seguintes documentos para obter um entendimento mais aprofundado:

- [Introdução aos Storyboards](~/mac/platform/storyboards/index.md) – este artigo fornece uma introdução a como trabalhar com storyboards em um aplicativo Xamarin.Mac. Ele aborda a criação e manutenção da interface do usuário do aplicativo usando storyboards e o Interface Builder do Xcode.
- [Janelas](~/mac/user-interface/window.md) – este artigo aborda o trabalho com janelas e painéis em um aplicativo Xamarin.Mac. Ele aborda a criação e manutenção de janelas e painéis no Xcode e no Interface Builder, carregando janelas e painéis de arquivos .xib, usando janelas e respondendo a janelas em código C#.
- [Caixas de diálogo](~/mac/user-interface/dialog.md) – este artigo aborda o trabalho com caixas de diálogo e janelas modais em um aplicativo Xamarin.Mac. Ele aborda a criação e manutenção de janelas modais no construtor de Interface e Xcode, o trabalho com caixas de diálogo padrão, a exibição de janelas e resposta a elas em código c#.
- [Alertas](~/mac/user-interface/alert.md) – este artigo aborda o trabalho com alertas em um aplicativo Xamarin.Mac. Ele aborda a criação e exibição de alertas por código C# e a resposta aos alertas.
- [Menus](~/mac/user-interface/menu.md) – Menus são usados em várias partes da interface do usuário de um aplicativo Mac, desde o menu principal do aplicativo, na parte superior da tela, até os menus pop-up e contextuais que podem aparecer em qualquer lugar em uma janela. Menus são uma parte integrante da experiência do usuário de um aplicativo Mac. Este artigo aborda o trabalho com menus Cocoa em um aplicativo Xamarin.Mac.
- [Barras de ferramentas](~/mac/user-interface/toolbar.md) – este artigo aborda o trabalho com barras de ferramentas em um aplicativo Xamarin.Mac. Eles envolvem criação e manutenção, barras de ferramentas no Xcode e no Interface Builder, como expor os Itens da Barra de Ferramentas ao código usando Saídas e Ações, habilitar e desabilitar Itens da Barra de Ferramentas e responder a Itens da Barra de Ferramentas em código C#.
- [Exibições de tabela](~/mac/user-interface/table-view.md) – este artigo aborda o trabalho com exibições de tabela em um aplicativo Xamarin.Mac. Ele aborda as ações de criar e manter exibições de tabela no Interface Builder e Xcode, como expor os itens de exibição de tabela ao código usando saídas e ações, popular itens de exibição de tabela e, por fim, responder aos itens da exibição de tabela em código C#.
- [Modos de exibição de estrutura de tópicos](~/mac/user-interface/outline-view.md) – este artigo aborda o trabalho com modos de exibição de estrutura de tópicos em um aplicativo Xamarin.Mac. Ele aborda as ações de criar e manter modos de exibição de estrutura de tópicos no Interface Builder e Xcode, como expor os itens de modos de exibição de estrutura de tópicos ao código usando saídas e ações, popular itens de modos de exibição de estrutura de tópicos e, por fim, responder aos itens de modos de exibição de estrutura de tópicos em código C#.
- [Alertas](~/mac/user-interface/source-list.md) – este artigo aborda o trabalho com listas de origem em um aplicativo Xamarin.Mac. Ele aborda as ações de criar e manter listas de origem de tópicos no Interface Builder e Xcode, como expor os itens de listas de origem ao código usando saídas e ações, popular itens de listas de origem e, por fim, responder aos itens de listas de origem em código C#.
- [Modos de exibição de conjuntos](~/mac/user-interface/collection-view.md) – este artigo aborda o trabalho com modos de exibição de conjuntos em um aplicativo Xamarin.Mac. Ele aborda as ações de criar e manter modos de exibição de coleção no Interface Builder e Xcode, como expor os itens de modos de exibição de coleção ao código usando saídas e ações, popular modos de exibição de coleção e, por fim, responder aos modos de exibição de coleção em código C#.
- [Trabalhando com imagens](~/mac/app-fundamentals/image.md) – este artigo aborda o trabalho com imagens e ícones em um aplicativo Xamarin.Mac. Ele aborda a criação e manutenção das imagens necessárias para criar o ícone de um aplicativo e usar imagens tanto em código C# quanto no Interface Builder do Xcode.

A [Galeria de Amostras do Mac](/samples/browse/?products=xamarin&term=Xamarin.Mac) contém exemplos de código prontos para uso para ajudar na aprendizagem do Xamarin.Mac.

Um aplicativo Xamarin.Mac completo que inclui muitos dos recursos que o usuário espera encontrar em um aplicativo típico do Mac é o [Aplicativo de Exemplo SourceWriter](/samples/xamarin/mac-samples/sourcewriter). SourceWriter é um editor de código-fonte simples que dá suporte para a conclusão de código e realce de sintaxe simples.

O código do SourceWriter foi totalmente comentado e, quando disponível, foram fornecidos links de tecnologias-chave ou métodos para informações relevantes na documentação do Xamarin.Mac.

## <a name="summary"></a>Resumo

Este artigo abordou os conceitos básicos de um aplicativo Xamarin.Mac padrão. Ele abordou a criação de um novo aplicativo no Visual Studio para Mac, a concepção da interface do usuário no Xcode e Interface Builder, a exposição dos elementos da interface do usuário ao código C# usando **Saídas** e **Ações**, a adição de código para trabalhar com os elementos de interface do usuário e, por fim, a compilação e teste de um aplicativo Xamarin.Mac.

## <a name="related-links"></a>Links Relacionados

- [Hello, Mac (exemplo)](/samples/xamarin/mac-samples/hello-mac)
- [Diretrizes de Interface Humana do macOS](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/)