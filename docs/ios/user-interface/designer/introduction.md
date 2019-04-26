---
title: Noções básicas do Designer do iOS
description: Este guia apresenta o Designer do Xamarin para iOS. Ele demonstra como usar o Designer do iOS para dispor visualmente os controles, como acessar esses controles no código e como editar as propriedades.
ms.prod: xamarin
ms.assetid: E7045E41-0DEF-416B-BCDB-52502350F61C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 01/31/2018
ms.openlocfilehash: 2c6409410ecba7df8559c07c3231f5e493c98897
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61079623"
---
# <a name="ios-designer-basics"></a>Noções básicas do Designer do iOS

_Este guia apresenta o Designer do Xamarin para iOS. Ele demonstra como usar o Designer do iOS para dispor visualmente os controles, como acessar esses controles no código e como editar as propriedades._

O Designer do Xamarin para iOS é um designer de interface visual semelhante à Interface Builder do Xcode e o Designer do Android. Alguns dos seus diversos recursos incluem integração perfeita com o Visual Studio para Windows e Mac, edição arrastar-e-soltar, uma interface para configuração de manipuladores de eventos e a capacidade de processar controles personalizados.

## <a name="requirements"></a>Requisitos

O iOS Designer está disponível no Visual Studio para Mac e Visual Studio 2017 e posterior no Windows. No Visual Studio para Windows, o iOS Designer requer uma conexão a um host de build do Mac configurado corretamente, embora o Xcode não precisa estar em execução.

Este guia pressupõe uma familiaridade com o conteúdo abordados os [guias de Introdução ao](~/ios/get-started/index.md).

<a name="how-it-works" />

## <a name="how-the-ios-designer-works"></a>Como funciona o Designer do iOS

Esta seção descreve como o Designer do iOS facilita a criação de uma interface do usuário e conectá-lo ao código.

O Designer do iOS permite aos desenvolvedores criar visualmente a interface do usuário do aplicativo. Conforme descrito na [Introdução ao Storyboards](~/ios/user-interface/storyboards/index.md) guia, um storyboard descreve as telas (controladores de exibição) que compõem um aplicativo, os elementos de interface (exibições) colocados nesses controladores de exibição e o fluxo geral de navegação do aplicativo . 

Um controlador de exibição tem duas partes: uma representação visual no iOS Designer e uma classe c# associada:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Um controlador de exibição no Designer do iOS](introduction-images/1-storyboardwithviewcontroller-vsmac.png "um controlador de exibição no Designer do iOS")](introduction-images/1-storyboardwithviewcontroller-vsmac-large.png#lightbox)

[![O código para um controlador de exibição](introduction-images/2-viewcontrollercode-vsmac.png "o código para um controlador de exibição")](introduction-images/2-viewcontrollercode-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Um controlador de exibição no Designer do iOS](introduction-images/1-storyboardwithviewcontroller-vs.png "um controlador de exibição no Designer do iOS")](introduction-images/1-storyboardwithviewcontroller-vs-large.png#lightbox)

[![O código para um controlador de exibição](introduction-images/2-viewcontrollercode-vs.png "o código para um controlador de exibição")](introduction-images/2-viewcontrollercode-vs-large.png#lightbox)

-----

Em seu estado padrão, um controlador de exibição não fornece nenhuma funcionalidade; ele deve ser preenchido com controles. Esses controles são colocados no modo de exibição do controlador de exibição, a área retangular que contém todo o conteúdo da tela. A maioria dos controladores de exibição contêm controles comuns como botões, rótulos e campos de texto, conforme ilustrado na seguinte captura de tela, que mostra um controlador de exibição que contém um botão: 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Um controlador de exibição que contém um botão](introduction-images/3-viewcontrollerwithbutton-vsmac.png "um controlador de exibição que contém um botão")](introduction-images/3-viewcontrollerwithbutton-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Um controlador de exibição que contém um botão](introduction-images/3-viewcontrollerwithbutton-vs.png "um controlador de exibição que contém um botão")](introduction-images/3-viewcontrollerwithbutton-vs-large.png#lightbox)

-----

Alguns controles, como rótulos que contém texto estático, podem ser adicionados para o controlador de exibição e sozinho. No entanto, mais frequência, os controles devem ser personalizados por meio de programação. Por exemplo, o botão adicionado acima deve fazer algo quando tocada, portanto, deve ser adicionado a um manipulador de eventos no código.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Para acessar e manipular o botão no código, ele deve ter um identificador exclusivo. Fornecer um identificador exclusivo, selecionando o botão, abrindo o **painel de propriedades**e definindo seus **nome** campo com um valor como "SubmitButton":

[![Definir o nome de um botão no painel de propriedades](introduction-images/4-settingbuttonname-vsmac.png "definindo o nome de um botão no painel de propriedades")](introduction-images/4-settingbuttonname-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Para acessar e manipular o botão no código, ele deve ter um identificador exclusivo. Fornecer um identificador exclusivo, selecionando o botão, abrindo o **janela de propriedades**e definindo seu **nome** campo com um valor como "SubmitButton":

[![Definindo o nome de um botão na janela de propriedades](introduction-images/4-settingbuttonname-vs.png "definindo o nome de um botão na janela Propriedades")](introduction-images/4-settingbuttonname-vs-large.png#lightbox)

-----

Agora que o botão tem um nome, ele pode ser acessado no código. Mas como isso funciona?

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

No **painel de soluções**, a navegação para **ViewController.cs** e clique no indicador divulgação revela que o controlador de exibição `ViewController` spans de definição de classe dois arquivos, cada um deles contém uma [classe parcial](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods) definição:

[![Os dois arquivos que compõem a classe ViewController: ViewController.cs e ViewController.designer.cs](introduction-images/5-twoviewcontrollerfiles-vsmac.png "os dois arquivos que compõem a classe ViewController: ViewController.cs e ViewController.designer.cs")](introduction-images/5-twoviewcontrollerfiles-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

No **Gerenciador de soluções**, a navegação para **ViewController.cs** e clique no indicador divulgação revela que o controlador de exibição `ViewController` definição de classe abrange dois arquivos, cada um dos que contém um [classe parcial](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods) definição:

[![Os dois arquivos que compõem a classe ViewController: ViewController.cs e ViewController.designer.cs](introduction-images/5-twoviewcontrollerfiles-vs.png "os dois arquivos que compõem a classe ViewController: ViewController.cs e ViewController.designer.cs")](introduction-images/5-twoviewcontrollerfiles-vs-large.png#lightbox)

-----

- **ViewController.cs** deve ser preenchido com código personalizado relacionado ao `ViewController` classe. Nesse arquivo, o `ViewController` classe pode responder a iOS vários métodos de ciclo de vida do controlador de exibição, personalizar a interface do usuário e responder a entradas do usuário como botão toca.

- **ViewController.designer.cs** é um arquivo gerado, criado pelo Designer para mapear a interface construídos visualmente para o código do iOS. Uma vez que as alterações feitas neste arquivo serão substituídas, não deve ser modificado. Declarações de propriedade neste arquivo tornam possível para o código na `ViewController` de classe para o acesso, por **nome**, controla o conjunto de backup no Designer do iOS. Abrindo **ViewController.designer.cs** revela o código a seguir:

```csharp
namespace Designer
{
    [Register ("ViewController")]
    partial class ViewController
    {
        [Outlet]
        [GeneratedCode ("iOS Designer", "1.0")]
        UIKit.UIButton SubmitButton { get; set; }

        void ReleaseDesignerOutlets ()
        {
            if (SubmitButton != null) {
                SubmitButton.Dispose ();
                SubmitButton = null;
            }
        }
    }
}
```

O `SubmitButton` declaração de propriedade se conecta a todo o `ViewController` classe - não apenas o **ViewController.designer.cs** arquivo – para o botão definido no storyboard. Uma vez que **ViewController.cs** define a parte do `ViewController` classe, ele tem acesso ao `SubmitButton`.

Captura de tela a seguir ilustra que o IntelliSense agora reconhece o `SubmitButton` de referência no **ViewController.cs**:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![IntelliSense reconhecendo a referência SubmitButton](introduction-images/6-submitbuttonintellisense-vsmac.png "IntelliSense reconhecendo a referência SubmitButton")](introduction-images/6-submitbuttonintellisense-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![IntelliSense reconhecendo a referência SubmitButton](introduction-images/6-submitbuttonintellisense-vs.png "IntelliSense reconhecendo a referência SubmitButton")](introduction-images/6-submitbuttonintellisense-vs-large.png#lightbox)

-----

Esta seção demonstrou como criar um botão no iOS Designer e acessar esse botão no código.

O restante deste documento fornece uma visão adicional do Designer do iOS.

## <a name="ios-designer-basics"></a>Noções básicas do Designer do iOS

Esta seção apresenta as partes do Designer do iOS e fornece um tour de seus recursos.

### <a name="launching-the-ios-designer"></a>Iniciar o Designer do iOS

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Projetos do xamarin. IOS criados com o Visual Studio para Mac incluem um storyboard. Para exibir o conteúdo de um storyboard, duas vezes no arquivo storyboard a **painel de soluções**:

[![Um storyboard estiver aberto no Designer do iOS](introduction-images/7-storyboardopen-vsmac.png "um storyboard estiver aberto no Designer do iOS")](introduction-images/7-storyboardopen-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

A maioria dos projetos de xamarin. IOS criados com o Visual Studio incluem um storyboard. Para exibir o conteúdo de um storyboard, duas vezes no arquivo storyboard a **Gerenciador de soluções**:

[![Um storyboard estiver aberto no Designer do iOS](introduction-images/7-storyboardopen-vs.png "um storyboard estiver aberto no Designer do iOS")](introduction-images/7-storyboardopen-vs-large.png#lightbox)

-----

<a name="iOS_Designer_features"/>

### <a name="ios-designer-features"></a>recursos do Designer do iOS

O Designer do iOS tem seis seções principais:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Seções do Designer do iOS](introduction-images/8-sixpartsofiosdesigner-vsmac.png "seções do Designer do iOS")](introduction-images/8-sixpartsofiosdesigner-vsmac-large.png#lightbox)

1. **Superfície de design** – espaço de trabalho principal do Designer do iOS. Mostra a área do documento, ele permite a construção visual de interfaces do usuário.
2. **Barra de ferramentas de restrições** – permite a alternância entre o quadro de edição de modo e o modo de edição de restrição, duas maneiras diferentes para posicionar elementos em uma interface do usuário.
3. **Caixa de ferramentas** – lista de controladores, objetos, controles, modos de exibição de dados, reconhecedores de gestos, windows e as barras que podem ser arrastados para a superfície de design e adicionadas a uma interface do usuário.
4. **Painel de propriedades** – mostra as propriedades para o controle selecionado, incluindo a identidade, os estilos visuais, acessibilidade, layout e comportamento.
5. **Estrutura de tópicos de documento** – mostra a árvore de controles que compõem o layout para a interface que está sendo editado. Clicar em um item na árvore de seleciona-lo no Designer do iOS e mostra suas propriedades na **painel de propriedades**. Isso é útil para selecionar um controle específico em uma interface do usuário aninhadas.
6. **Parte inferior da barra de ferramentas** – contém opções para alterar como o Designer do iOS exibe o arquivo de storyboard ou. XIB, incluindo o dispositivo, a orientação e zoom.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Seções do Designer do iOS](introduction-images/8-sixpartsofiosdesigner-vs.png "seções do Designer do iOS")](introduction-images/8-sixpartsofiosdesigner-vs-large.png#lightbox)

1. **Superfície de design** – espaço de trabalho principal do Designer do iOS. Mostra a área do documento, ele permite a construção visual de interfaces do usuário.
2. **Barra de ferramentas de restrições** – permite a alternância entre o quadro de edição de modo e o modo de edição de restrição, duas maneiras diferentes para posicionar elementos em uma interface do usuário.
3. **Caixa de ferramentas** – lista de controladores, objetos, controles, modos de exibição de dados, reconhecedores de gestos, windows e as barras que podem ser arrastados para a superfície de design e adicionadas a uma interface do usuário.
4. **Janela propriedades** – mostra as propriedades para o controle selecionado, incluindo a identidade, os estilos visuais, acessibilidade, layout e comportamento.
5. **Estrutura de tópicos de documento** – mostra a árvore de controles que compõem o layout para a interface que está sendo editado. Clicar em um item na árvore de seleciona-lo no Designer do iOS e mostra suas propriedades na **janela de propriedades**. Isso é útil para selecionar um controle específico em uma interface do usuário aninhadas.
6. **Parte inferior da barra de ferramentas** – contém opções para alterar como o Designer do iOS exibe o arquivo de storyboard ou. XIB, incluindo o dispositivo, a orientação e zoom.

-----

### <a name="design-workflow"></a>Design de fluxo de trabalho

#### <a name="adding-a-control-to-the-interface"></a>Adicionando um controle para a interface

Para adicionar um controle a uma interface, arraste-o para o **caixa de ferramentas** e solte-o na superfície de design. Adicionando ou um controle de posicionamento, diretrizes verticais e horizontais realçam as posições de layout usados como centro vertical, centro horizontal e margens:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)
 
![Na superfície de design, diretrizes de realçar posições do layout usados](introduction-images/9-layoutguides-vsmac.png "na superfície de design, diretrizes de realçar posições do layout usados")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Na superfície de design, diretrizes de realçar posições do layout usados](introduction-images/9-layoutguides-vs.png "na superfície de design, diretrizes de realçar posições do layout usados")

-----

A linha pontilhada azul no exemplo acima fornece uma orientação de alinhamento visual de centro horizontal para ajudar com o posicionamento de botão.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

#### <a name="context-menu-commands"></a>Comandos de menu de contexto

Um menu de contexto está disponível na superfície de design e nos **Document Outline**. Esse menu fornece comandos para o controle selecionado e seu pai, que é útil ao trabalhar com modos de exibição em uma hierarquia aninhada:

[![O menu de contexto na superfície de design](introduction-images/10-contextmenudesignsurface-vsmac.png "o menu de contexto na superfície de design")](introduction-images/10-contextmenudesignsurface-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

-----

### <a name="constraints-toolbar"></a>Barra de ferramentas de restrições

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)
 
[![A barra de ferramentas de restrições](introduction-images/11-constraintstoolbar-vsmac.png "a barra de ferramentas de restrições")](introduction-images/11-constraintstoolbar-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![A barra de ferramentas de restrições](introduction-images/11-constraintstoolbar-vs.png "a barra de ferramentas de restrições")](introduction-images/11-constraintstoolbar-vs-large.png#lightbox)

-----

A barra de ferramentas de restrições foi atualizada e agora consiste em dois controles: o modo de edição de quadro / Ativar/desativar modo e as restrições de atualização de edição de restrição / quadros botão Atualizar.

#### <a name="frame-editing-mode--constraint-editing-mode-toggle"></a>Modo de edição de quadro / Ativar/desativar modo de edição de restrição

Nas versões anteriores do Designer do iOS, clicando em um modo de exibição estiver selecionado na superfície de design alternado entre o quadro de edição de modo e o modo de edição de restrição. Agora, um controle de alternância na barra de ferramentas de restrições alterna entre esses modos de edição.

- Modo de edição de quadro:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Botão de modo de edição de quadro](introduction-images/12a-frameeditingmode-vsmac.png "botão de modo de edição de quadro")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Botão de modo de edição de quadro](introduction-images/12a-frameeditingmode-vs.png "botão de modo de edição de quadro")

-----

- Modo de edição de restrição:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Botão de modo de edição de restrição](introduction-images/12b-constrainteditingmode-vsmac.png "botão de modo de edição de restrição")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Botão de modo de edição de restrição](introduction-images/12b-constrainteditingmode-vs.png "botão de modo de edição de restrição")

-----

#### <a name="update-constraints--update-frames-button"></a>Atualizar restrições / quadros botão Atualizar

As restrições de atualização / atualização quadros botão fica à direita do quadro de modo de edição / ativar/desativar modo de edição de restrição.

- No modo de edição de quadro, este botão ajusta os quadros de quaisquer elementos selecionados para corresponder a suas restrições.
- No modo de edição de restrição, este botão ajusta as restrições de quaisquer elementos selecionados para corresponder a seus quadros.

### <a name="bottom-toolbar"></a>Barra de ferramentas inferior

Barra de ferramentas inferior fornece uma maneira de selecionar o dispositivo, a orientação e zoom usado para exibir um arquivo de storyboard ou. XIB no Designer do iOS:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Barra de ferramentas inferior, usada para selecionar um dispositivo e a orientação para a superfície de design](introduction-images/13-bottomtoolbar-vsmac.png "barra de ferramentas inferior, usada para selecionar um dispositivo e a orientação para a superfície de design")](introduction-images/13-bottomtoolbar-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Barra de ferramentas inferior, usada para selecionar um dispositivo e a orientação para a superfície de design](introduction-images/13-bottomtoolbar-vs.png "barra de ferramentas inferior, usada para selecionar um dispositivo e a orientação para a superfície de design")](introduction-images/13-bottomtoolbar-vs-large.png#lightbox)

-----

#### <a name="device-and-orientation"></a>Dispositivo e a orientação

Quando expandido, a barra de ferramentas inferior exibe todos os dispositivos, orientações e/ou adaptações aplicáveis ao documento atual. Clicando neles altera o modo de exibição na superfície de design. 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Barra de ferramentas inferior é expandido para mostrar os dispositivos e orientações](introduction-images/14-bottomtoolbarexpanded-vsmac.png "barra de ferramentas inferior, expandida para mostrar os dispositivos e orientações")](introduction-images/14-bottomtoolbarexpanded-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Barra de ferramentas inferior é expandido para mostrar os dispositivos e orientações](introduction-images/14-bottomtoolbarexpanded-vs.png "barra de ferramentas inferior, expandida para mostrar os dispositivos e orientações")](introduction-images/14-bottomtoolbarexpanded-vs-large.png#lightbox)

-----

Observe que a seleção de um dispositivo e orientação altera apenas como o Designer do iOS visualiza o design. Independentemente da seleção atual, recém-adicionados restrições são aplicadas a todos os dispositivos e orientações, a menos que o **Editar características** botão foi usado para especifique de outra forma.

Quando [classes de tamanho](~/ios/user-interface/storyboards/unified-storyboards.md#size-classes) são [habilitada](~/ios/user-interface/storyboards/unified-storyboards.md#enabling-size-classes), o **Editar características** botão será exibido na barra de ferramentas inferior expandido.  Clicar a **Editar características** botão exibe opções para criar uma variação de interface com base no tamanho classe representada pelo dispositivo selecionado e a orientação. Considere os exemplos a seguir:

- Se **iPhone sar** / **retrato**, é selecionada, o pop-over fornecerá opções para criar uma variação de interface para a largura compact, a classe de tamanho regular de altura. 
- Se **iPad Pro 9,7 pol.** / **paisagem** / **tela inteira** é selecionada, o pop-over fornecerá opções para criar uma variação de interface para a largura regular, a classe de tamanho regular de altura.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Barra de ferramentas inferior seja usada para variar a uma interface por classe de tamanho](introduction-images/15-edittraitsbutton-vsmac.png "barra de ferramentas inferior seja usada para variar a uma interface por classe de tamanho")](introduction-images/15-edittraitsbutton-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Barra de ferramentas inferior seja usada para variar a uma interface por classe de tamanho](introduction-images/15-edittraitsbutton-vs.png "barra de ferramentas inferior seja usada para variar a uma interface por classe de tamanho")](introduction-images/15-edittraitsbutton-vs-large.png#lightbox)

-----

#### <a name="zoom-controls"></a>Controles de zoom

A superfície de design oferece suporte a aplicação de zoom por meio de vários controles:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)
 
![Os controles de zoom na barra de ferramentas inferior](introduction-images/16-zoomcontrols-vsmac.png "os controles de zoom na barra de ferramentas inferior")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Os controles de zoom na barra de ferramentas inferior](introduction-images/16-zoomcontrols-vs.png "os controles de zoom na barra de ferramentas inferior")

-----

Os controles incluem o seguinte:

1. Aplicar zoom para ajustar
2. Reduzir
3. Ampliar
4. Tamanho real (o tamanho de pixel de 1:1)

Esses controles de ajustar o zoom na superfície de design. Elas não afetam a interface do usuário do aplicativo em tempo de execução.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

### <a name="properties-pad"></a>Painel de propriedades

Use o **painel de propriedades** para editar a identidade, os estilos visuais, acessibilidade e comportamento de um controle. Captura de tela a seguir ilustra a **painel de propriedades** opções para um botão:

[![O painel de propriedades para um botão](introduction-images/17-buttonpropertiespad-vsmac.png "o painel de propriedades para um botão")](introduction-images/17-buttonpropertiespad-vsmac-large.png#lightbox)
#### <a name="properties-pad-sections"></a>Seções do painel de propriedades

O **painel de propriedades** contém três seções:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

### <a name="properties-window"></a>Janela Propriedades

Use o **janela de propriedades** para editar a identidade, os estilos visuais, acessibilidade e comportamento de um controle. Captura de tela a seguir ilustra a **janela de propriedades** opções para um botão:

[![A janela Propriedades de um botão](introduction-images/17-buttonpropertieswindow-vs.png "a janela de propriedades de um botão")](introduction-images/17-buttonpropertieswindow-vs-large.png#lightbox)

#### <a name="properties-window-sections"></a>Seções da janela de propriedades

O **janela de propriedades** contém três seções:

-----

1.  **Widget** – as principais propriedades do controle, como nome, classe, propriedades de estilo, etc. Propriedades para gerenciar o conteúdo do controle são colocadas em geral aqui.
2.  **Layout** – propriedades que controlam a posição e tamanho do controle, incluindo restrições e quadros, estão listadas aqui.
3.  **Eventos** – eventos e manipuladores de eventos são especificados aqui. Útil para lidar com eventos, como toque, toque, arraste, etc. Eventos também podem ser tratados diretamente no código.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

#### <a name="editing-properties-in-the-properties-pad"></a>Editar as propriedades no painel de propriedades

Além da edição visual na superfície de design, o Designer do iOS dá suporte a editar as propriedades na **painel de propriedades**. A alteração de propriedades disponíveis com base no controle selecionado, conforme ilustrado pela capturas de tela abaixo:

[![Botão de propriedades](introduction-images/18a-buttonpropertiespad-vsmac.png "botão Propriedades")](introduction-images/18a-buttonpropertiespad-vsmac-large.png#lightbox)

[![Exibir propriedades do controlador](introduction-images/18b-viewcontrollerpropertiespad-vsmac.png "exibir propriedades do controlador")](introduction-images/18b-viewcontrollerpropertiespad-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

#### <a name="editing-properties-in-the-properties-window"></a>Editar as propriedades na janela Propriedades

Além da edição visual na superfície de design, o Designer do iOS dá suporte a editar as propriedades na **janela de propriedades**. A alteração de propriedades disponíveis com base no controle selecionado, conforme ilustrado pela capturas de tela abaixo:

[![Botão de propriedades](introduction-images/18a-buttonpropertieswindow-vs.png "botão Propriedades")](introduction-images/18a-buttonpropertieswindow-vs-large.png#lightbox)

[![Exibir propriedades do controlador](introduction-images/18b-viewcontrollerpropertieswindow-vs.png "exibir propriedades do controlador")](introduction-images/18b-viewcontrollerpropertieswindow-vs-large.png#lightbox)

-----

> [!IMPORTANT]
> A seção de identidade do painel de propriedades agora mostra uma **módulo** campo. É necessário preencher esta seção só ao interoperar com classes Swift. Usá-lo para inserir um nome de módulo para classes de Swift, que são ajudaria.

#### <a name="default-values"></a>Valores padrão

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Muitas propriedades na **painel de propriedades** mostrar nenhum valor ou um valor padrão. No entanto, o código do aplicativo ainda pode modificar esses valores. O **painel de propriedades** não mostra os valores definidos no código.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Muitas propriedades na **janela de propriedades** mostrar nenhum valor ou um valor padrão. No entanto, o código do aplicativo ainda pode modificar esses valores. O **janela de propriedades** não mostra os valores definidos no código.

-----

#### <a name="event-handlers"></a>Manipuladores de eventos

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Para especificar os manipuladores de eventos personalizados para vários eventos, use o **eventos** guia o **painel de propriedades**. Por exemplo, na captura de tela abaixo, uma `HandleClick` método lida com o botão **Touch dentro** eventos:

[![O painel de propriedades, com um manipulador de eventos definida para um botão](introduction-images/19-buttonpropertiespadevents-vsmac.png "o painel de propriedades, com um manipulador de eventos definida para um botão")](introduction-images/19-buttonpropertiespadevents-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Para especificar os manipuladores de eventos personalizados para vários eventos, use o **eventos** guia o **janela propriedades**. Por exemplo, na captura de tela abaixo, uma `HandleClick` método lida com o botão **Touch dentro** eventos:

[![A janela de propriedades, com um manipulador de eventos definida para um botão](introduction-images/19-buttonpropertieswindowevents-vs.png "a janela de propriedades, com um manipulador de eventos definida para um botão")](introduction-images/19-buttonpropertieswindowevents-vs-large.png#lightbox)

-----

Depois que um manipulador de eventos tiver sido especificado, um método de mesmo nome deve ser adicionado à classe do controlador de exibição correspondente. Caso contrário, um `unrecognized selector` exceção ocorrerá quando o botão é tocado:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Uma exceção de seletor não reconhecido](introduction-images/20-unrecognizedselector-vsmac.png "uma exceção de seletor não reconhecido")](introduction-images/20-unrecognizedselector-vsmac-large.png#lightbox)

Observe que, após um manipulador de eventos tiver sido especificado na **painel de propriedades**, o iOS Designer imediatamente abrirá o arquivo de código correspondente e oferecer para inserir a declaração de método. 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Uma exceção de seletor não reconhecido](introduction-images/20-unrecognizedselector-vs.png "uma exceção de seletor não reconhecido")](introduction-images/20-unrecognizedselector-vs-large.png#lightbox)

-----

Para obter um exemplo que usa os manipuladores de eventos personalizados, consulte o [Hello, iOS Getting Started Guide](~/ios/get-started/hello-ios/index.md).

### <a name="outline-view"></a>Modo de exibição de estrutura de tópicos

O Designer do iOS também pode exibir a hierarquia da interface de controles como uma estrutura de tópicos. A estrutura de tópicos está disponível selecionando o **Document Outline** guia, conforme mostrado abaixo:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![A estrutura de tópicos do documento](introduction-images/21-buttonoutlineview-vsmac.png "a estrutura de tópicos do documento")](introduction-images/21-buttonoutlineview-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![A estrutura de tópicos do documento](introduction-images/21-buttonoutlineview-vs.png "a estrutura de tópicos do documento")](introduction-images/21-buttonoutlineview-vs-large.png#lightbox)

-----

O controle selecionado no modo de exibição de estrutura de tópicos permaneça em sincronizado com o controle selecionado na superfície de design.  Esse recurso é útil para selecionar um item de uma hierarquia de interface profundamente aninhadas.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="revert-to-xcode"></a>Reverter para o Xcode

É possível usar o Designer do iOS e Xcode Interface Builder alternadamente. Para abrir um storyboard ou um arquivo. XIB no Interface Builder do Xcode, clique com botão direito no arquivo e selecione **abrir com > Interface Builder do Xcode**, conforme ilustrado pela captura de tela abaixo:

[![Abrir um storyboard no Xcode Interface Builder](introduction-images/22-openinxcodeinterfacebuilder-vsmac.png "abrir um storyboard no Interface Builder do Xcode")](introduction-images/22-openinxcodeinterfacebuilder-vsmac-large.png#lightbox)

Depois de fazer as edições no Xcode Interface Builder, salve o arquivo e retorne ao Visual Studio para Mac. As alterações serão sincronizadas com o projeto xamarin. IOS.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="revert-to-xcode"></a>Reverter para o Xcode

É possível usar o Designer do iOS e Xcode Interface Builder alternadamente, mas o Interface Builder do Xcode só está disponível no Mac. Para abrir um arquivo de storyboard ou. XIB no Interface Builder do Xcode no Mac, abra a solução que contém o projeto xamarin. IOS [Visual Studio para Mac](/visualstudio/mac/), clique com botão direito no arquivo e selecione **abrir com > Interface do Xcode Construtor**, conforme ilustrado pela captura de tela abaixo:

[![Abrir um storyboard no Xcode Interface Builder](introduction-images/22-openinxcodeinterfacebuilder-vsmac.png "abrir um storyboard no Interface Builder do Xcode")](introduction-images/22-openinxcodeinterfacebuilder-vsmac-large.png#lightbox)

Depois de fazer as edições no Xcode Interface Builder, salve o arquivo e retorne ao Visual Studio para Mac. As alterações serão sincronizadas com o projeto xamarin. IOS.

-----

## <a name="xib-support"></a>suporte. XIB

O Designer do iOS dá suporte à criação, edição e gerenciamento de arquivos. XIB. Esses são arquivos XML que exibições representar única e personalizados que podem ser adicionados à hierarquia de exibição do aplicativo. Um arquivo. XIB geralmente representa a interface para um único modo de exibição ou uma tela em um aplicativo, enquanto que um storyboard representa várias telas e as transições entre eles.

Há muitas opiniões sobre qual solução – arquivos. XIB, storyboards ou código – funciona melhor para criar e manter uma interface do usuário. Na realidade, há uma solução perfeita e sempre vale a pena considerar a melhor ferramenta para o trabalho em questão. Dito isso, os arquivos. XIB são geralmente mais eficientes quando usado para representar uma exibição personalizada necessária em vários locais em um aplicativo, como uma célula de exibição de tabela personalizados. 

Mais documentação sobre como usar arquivos. XIB pode ser encontrada em receitas a seguir:

- [Usando o modelo de exibição. XIB](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/using_the_ios_view_xib_template)
- [Criando um TableViewCell personalizado usando um. XIB](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/tables/custom-tableviewcell)
- [Criando uma tela de inicialização usando um. XIB](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/launchscreen-xib)

Para obter mais informações sobre o uso de storyboards, consulte o [Introdução ao Storyboards](~/ios/user-interface/storyboards/index.md).

Isso e outro guias de Designer do iOS referem-se ao uso de storyboards como a abordagem padrão para a criação de interfaces do usuário, como o xamarin. IOS a maioria dos novos modelos de projeto fornecem um storyboard por padrão.

## <a name="summary"></a>Resumo

Este guia forneceu uma introdução ao iOS Designer, que descrevem seus recursos e as ferramentas que ele oferece para a criação de interfaces do usuário lindas de estrutura de tópicos.

## <a name="related-links"></a>Links relacionados

- [Introdução ao Storyboards](~/ios/user-interface/storyboards/index.md)
- [iOS projetáveis passo a passo de controles](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Hello, iOS](~/ios/get-started/hello-ios/index.md)
- [Multitela Hello, iOS](~/ios/get-started/hello-ios-multiscreen/index.md)
- [Visão geral do Designer Android](~/android/user-interface/android-designer/index.md)
- [Classes e métodos parciais](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)
- [Mergulhar no Designer de Xamarin para iOS - 2014 evoluir (vídeo)](https://www.youtube.com/watch?v=W4H9uLjoEjM)
- [Usando o Designer do iOS para criar uma tela de inicialização (vídeo)](https://university.xamarin.com/lightninglectures/using-the-ios-designer-to-create-a-launch-screen)
