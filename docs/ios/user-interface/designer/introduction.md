---
title: Noções básicas do iOS designer
description: Este guia apresenta o Xamarin Designer para iOS. Ele demonstra como usar o designer do iOS para formatar visualmente os controles, como acessar esses controles no código e como editar propriedades.
ms.prod: xamarin
ms.assetid: E7045E41-0DEF-416B-BCDB-52502350F61C
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 01/31/2018
ms.openlocfilehash: f37905f483ad31d05276383718a3295d4a288f28
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70285875"
---
# <a name="ios-designer-basics"></a>Noções básicas do iOS designer

_Este guia apresenta o Xamarin Designer para iOS. Ele demonstra como usar o designer do iOS para formatar visualmente os controles, como acessar esses controles no código e como editar propriedades._

O Xamarin Designer para iOS é um designer de interface visual semelhante ao Interface Builder do Xcode e ao Designer Android. Alguns dos seus muitos recursos incluem a integração direta com o Visual Studio para Windows e Mac, edição do tipo "arrastar e soltar", uma interface para configurar manipuladores de eventos e a capacidade de renderizar controles personalizados.

## <a name="requirements"></a>Requisitos

O designer do iOS está disponível em Visual Studio para Mac e no Visual Studio 2017 e posterior no Windows. No Visual Studio para Windows, o designer do iOS requer uma conexão com um host de Build do Mac configurado corretamente, embora o Xcode não precise estar em execução.

Este guia pressupõe familiaridade com o conteúdo abordado nos [guias de introdução](~/ios/get-started/index.md).

<a name="how-it-works" />

## <a name="how-the-ios-designer-works"></a>Como funciona o designer do iOS

Esta seção descreve como o designer do iOS facilita a criação de uma interface do usuário e sua conexão ao código.

O designer do iOS permite que os desenvolvedores projetem visualmente a interface do usuário de um aplicativo. Conforme descrito no guia [introdução ao storyboards](~/ios/user-interface/storyboards/index.md) , um storyboard descreve as telas (controladores de exibição) que compõem um aplicativo, os elementos da interface (exibições) colocados nesses controladores de exibição e o fluxo de navegação geral do aplicativo. 

Um controlador de exibição tem duas partes: uma representação visual no designer do iOS e uma C# classe associada:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Um controlador de exibição no designer do IOS](introduction-images/1-storyboardwithviewcontroller-vsmac.png "Um controlador de exibição no designer do IOS")](introduction-images/1-storyboardwithviewcontroller-vsmac-large.png#lightbox)

[![O código para um controlador de exibição](introduction-images/2-viewcontrollercode-vsmac.png "O código para um controlador de exibição")](introduction-images/2-viewcontrollercode-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Um controlador de exibição no designer do IOS](introduction-images/1-storyboardwithviewcontroller-vs.png "Um controlador de exibição no designer do IOS")](introduction-images/1-storyboardwithviewcontroller-vs-large.png#lightbox)

[![O código para um controlador de exibição](introduction-images/2-viewcontrollercode-vs.png "O código para um controlador de exibição")](introduction-images/2-viewcontrollercode-vs-large.png#lightbox)

-----

Em seu estado padrão, um controlador de exibição não fornece nenhuma funcionalidade; Ele deve ser preenchido com controles. Esses controles são colocados na exibição do controlador de exibição, a área retangular que contém todo o conteúdo da tela. A maioria dos controladores de exibição contém controles comuns, como botões, rótulos e campos de texto, conforme ilustrado na captura de tela a seguir, que mostra um controlador de exibição contendo um botão: 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Um controlador de exibição que contém um botão](introduction-images/3-viewcontrollerwithbutton-vsmac.png "Um controlador de exibição que contém um botão")](introduction-images/3-viewcontrollerwithbutton-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Um controlador de exibição que contém um botão](introduction-images/3-viewcontrollerwithbutton-vs.png "Um controlador de exibição que contém um botão")](introduction-images/3-viewcontrollerwithbutton-vs-large.png#lightbox)

-----

Alguns controles, como rótulos que contêm texto estático, podem ser adicionados ao controlador de exibição e somente à esquerda. No entanto, com mais frequência do que não, os controles devem ser personalizados programaticamente. Por exemplo, o botão adicionado acima deve fazer algo quando tocado, portanto, um manipulador de eventos deve ser adicionado no código.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Para acessar e manipular o botão no código, ele deve ter um identificador exclusivo. Forneça um identificador exclusivo selecionando o botão, abrindo o **painel de propriedades**e definindo seu campo de **nome** como um valor como "SubmitButton":

[![Definindo o nome de um botão na painel de propriedades](introduction-images/4-settingbuttonname-vsmac.png "Definindo o nome de um botão na painel de propriedades")](introduction-images/4-settingbuttonname-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Para acessar e manipular o botão no código, ele deve ter um identificador exclusivo. Forneça um identificador exclusivo selecionando o botão, abrindo a **janela Propriedades**e definindo seu campo de **nome** como um valor como "SubmitButton":

[![Definindo o nome de um botão na janela Propriedades](introduction-images/4-settingbuttonname-vs.png "Definindo o nome de um botão na janela Propriedades")](introduction-images/4-settingbuttonname-vs-large.png#lightbox)

-----

Agora que o botão tem um nome, ele pode ser acessado no código. Mas como isso funciona?

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Na **painel de soluções**, navegar para **ViewController.cs** e clicar no indicador de divulgação revela que a definição de classe do `ViewController` controlador de exibição abrange dois arquivos, cada um contendo uma definição de [classe parcial](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods) :

[![Os dois arquivos que compõem a classe ViewController: ViewController.cs e ViewController.designer.cs](introduction-images/5-twoviewcontrollerfiles-vsmac.png "os dois arquivos que compõem a classe ViewController: ViewController.cs e ViewController.designer.cs")](introduction-images/5-twoviewcontrollerfiles-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Na **Gerenciador de soluções**, navegar para **ViewController.cs** e clicar no indicador de divulgação revela que a definição de classe do `ViewController` controlador de exibição abrange dois arquivos, cada um contendo uma [classe parcial](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods) defini

[![Os dois arquivos que compõem a classe ViewController: ViewController.cs e ViewController.designer.cs](introduction-images/5-twoviewcontrollerfiles-vs.png "os dois arquivos que compõem a classe ViewController: ViewController.cs e ViewController.designer.cs")](introduction-images/5-twoviewcontrollerfiles-vs-large.png#lightbox)

-----

- **ViewController.cs** deve ser preenchido com código personalizado relacionado à `ViewController` classe. Nesse arquivo, a `ViewController` classe pode responder a vários métodos de ciclo de vida do controlador de exibição do IOS, personalizar a interface do usuário e responder à entrada do usuários, como toques de botão.

- **ViewController.designer.cs** é um arquivo gerado, criado pelo Ios designer para mapear a interface visualmente construída para código. Como as alterações nesse arquivo serão substituídas, elas não devem ser modificadas. As declarações de propriedade nesse arquivo possibilitam que o `ViewController` código na classe acesse, por **nome**, os controles configurados no designer do Ios. Abrir o **ViewController.designer.cs** revela o seguinte código:

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

A `SubmitButton` declaração de propriedade conecta toda `ViewController` a classe, não apenas o arquivo **ViewController.designer.cs** , ao botão definido no storyboard. Como **ViewController.cs** define parte da `ViewController` classe, ele tem acesso a `SubmitButton`.

A captura de tela a seguir ilustra que o `SubmitButton` IntelliSense agora reconhece a referência em **ViewController.cs**:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![IntelliSense reconhecendo a referência de SubmitButton](introduction-images/6-submitbuttonintellisense-vsmac.png "IntelliSense reconhecendo a referência de SubmitButton")](introduction-images/6-submitbuttonintellisense-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![IntelliSense reconhecendo a referência de SubmitButton](introduction-images/6-submitbuttonintellisense-vs.png "IntelliSense reconhecendo a referência de SubmitButton")](introduction-images/6-submitbuttonintellisense-vs-large.png#lightbox)

-----

Esta seção demonstrou como criar um botão no designer do iOS e acessar esse botão no código.

O restante deste documento fornece uma visão geral adicional do designer do iOS.

## <a name="ios-designer-basics"></a>Noções básicas do iOS designer

Esta seção apresenta as partes do designer do iOS e fornece um tour pelos seus recursos.

### <a name="launching-the-ios-designer"></a>Iniciando o designer do iOS

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Os projetos do Xamarin. iOS criados com Visual Studio para Mac incluem um Storyboard. Para exibir o conteúdo de um storyboard, clique duas vezes no arquivo. Storyboard na **painel de soluções**:

[![Um storyboard aberto no designer do IOS](introduction-images/7-storyboardopen-vsmac.png "Um storyboard aberto no designer do IOS")](introduction-images/7-storyboardopen-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

A maioria dos projetos do Xamarin. iOS criados com o Visual Studio incluem um Storyboard. Para exibir o conteúdo de um storyboard, clique duas vezes no arquivo. Storyboard na **Gerenciador de soluções**:

[![Um storyboard aberto no designer do IOS](introduction-images/7-storyboardopen-vs.png "Um storyboard aberto no designer do IOS")](introduction-images/7-storyboardopen-vs-large.png#lightbox)

-----

<a name="iOS_Designer_features"/>

### <a name="ios-designer-features"></a>recursos do designer do iOS

O designer do iOS tem seis seções principais:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Seções do designer do IOS](introduction-images/8-sixpartsofiosdesigner-vsmac.png "Seções do designer do IOS")](introduction-images/8-sixpartsofiosdesigner-vsmac-large.png#lightbox)

1. **Design Surface** – o espaço de trabalho primário do designer do Ios. Mostrado na área do documento, ele permite a construção visual de interfaces do usuário.
2. **Barra de ferramentas de restrições** – permite alternar entre o modo de edição de quadro e o modo de edição de restrição, duas maneiras diferentes de posicionar elementos em uma interface do usuário.
3. **Caixa de ferramentas** – lista os controladores, objetos, controles, exibições de dados, reconhecedores de gesto, janelas e barras que podem ser arrastados para a superfície de design e adicionados a uma interface do usuário.
4. **Painel de propriedades** – mostra as propriedades do controle selecionado, incluindo identidade, estilos visuais, acessibilidade, layout e comportamento.
5. **Estrutura de tópicos do documento** – mostra a árvore de controles que compõem o layout da interface que está sendo editada. Clicar em um item na árvore o seleciona no designer do iOS e mostra suas propriedades no **painel de propriedades**. Isso é útil para selecionar um controle específico em uma interface de usuário profundamente aninhada.
6. **Barra de ferramentas inferior** – contém opções para alterar o modo como o designer do IOS exibe o arquivo. Storyboard ou. xib, incluindo o dispositivo, a orientação e o zoom.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Seções do designer do IOS](introduction-images/8-sixpartsofiosdesigner-vs.png "Seções do designer do IOS")](introduction-images/8-sixpartsofiosdesigner-vs-large.png#lightbox)

1. **Design Surface** – o espaço de trabalho primário do designer do Ios. Mostrado na área do documento, ele permite a construção visual de interfaces do usuário.
2. **Barra de ferramentas de restrições** – permite alternar entre o modo de edição de quadro e o modo de edição de restrição, duas maneiras diferentes de posicionar elementos em uma interface do usuário.
3. **Caixa de ferramentas** – lista os controladores, objetos, controles, exibições de dados, reconhecedores de gesto, janelas e barras que podem ser arrastados para a superfície de design e adicionados a uma interface do usuário.
4. **Janela Propriedades** – mostra as propriedades do controle selecionado, incluindo identidade, estilos visuais, acessibilidade, layout e comportamento.
5. **Estrutura de tópicos do documento** – mostra a árvore de controles que compõem o layout da interface que está sendo editada. Clicar em um item na árvore o seleciona no designer do iOS e mostra suas propriedades na **janela Propriedades**. Isso é útil para selecionar um controle específico em uma interface de usuário profundamente aninhada.
6. **Barra de ferramentas inferior** – contém opções para alterar o modo como o designer do IOS exibe o arquivo. Storyboard ou. xib, incluindo o dispositivo, a orientação e o zoom.

-----

### <a name="design-workflow"></a>Fluxo de trabalho de design

#### <a name="adding-a-control-to-the-interface"></a>Adicionando um controle à interface

Para adicionar um controle a uma interface, arraste-o da **caixa de ferramentas** e solte-o na superfície de design. Ao adicionar ou posicionar um controle, as diretrizes vertical e horizontal realçam as posições de layout mais usadas, como centro vertical, centro horizontal e margens:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)
 
![Na superfície de design, as diretrizes realçam as posições de layout usadas com frequência](introduction-images/9-layoutguides-vsmac.png "Na superfície de design, as diretrizes realçam as posições de layout usadas com frequência")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Na superfície de design, as diretrizes realçam as posições de layout usadas com frequência](introduction-images/9-layoutguides-vs.png "Na superfície de design, as diretrizes realçam as posições de layout usadas com frequência")

-----

A linha pontilhada azul no exemplo acima fornece uma orientação de alinhamento visual do centro horizontal para ajudar na colocação do botão.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

#### <a name="context-menu-commands"></a>Comandos do menu de contexto

Um menu de contexto está disponível na superfície de design e na **estrutura de tópicos do documento**. Este menu fornece comandos para o controle selecionado e seu pai, que é útil ao trabalhar com modos de exibição em uma hierarquia aninhada:

[![O menu de contexto na superfície de design](introduction-images/10-contextmenudesignsurface-vsmac.png "O menu de contexto na superfície de design")](introduction-images/10-contextmenudesignsurface-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

-----

### <a name="constraints-toolbar"></a>Barra de ferramentas restrições

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)
 
[![A barra de ferramentas restrições](introduction-images/11-constraintstoolbar-vsmac.png "A barra de ferramentas restrições")](introduction-images/11-constraintstoolbar-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![A barra de ferramentas restrições](introduction-images/11-constraintstoolbar-vs.png "A barra de ferramentas restrições")](introduction-images/11-constraintstoolbar-vs-large.png#lightbox)

-----

A barra de ferramentas de restrições foi atualizada e agora consiste em dois controles: o modo de edição de quadro/alternar modo de edição de restrição e o botão atualizar restrições/atualizar quadros.

#### <a name="frame-editing-mode--constraint-editing-mode-toggle"></a>Alternância de modo de edição de quadro/modo de edição de restrição

Nas versões anteriores do designer do iOS, clicar em uma exibição já selecionada na superfície de design é alternada entre o modo de edição de quadro e o modo de edição de restrição. Agora, um controle de alternância na barra de ferramentas de restrições alterna entre esses modos de edição.

- Modo de edição de quadro:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Botão modo de edição de quadro](introduction-images/12a-frameeditingmode-vsmac.png "Botão modo de edição de quadro")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Botão modo de edição de quadro](introduction-images/12a-frameeditingmode-vs.png "Botão modo de edição de quadro")

-----

- Modo de edição de restrição:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Botão modo de edição de restrição](introduction-images/12b-constrainteditingmode-vsmac.png "Botão modo de edição de restrição")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Botão modo de edição de restrição](introduction-images/12b-constrainteditingmode-vs.png "Botão modo de edição de restrição")

-----

#### <a name="update-constraints--update-frames-button"></a>Botão atualizar restrições/atualizar quadros

O botão atualizar restrições/atualizar quadros fica à direita da alternância modo de edição de quadro/modo de edição de restrição.

- No modo de edição de quadro, clicar nesse botão ajusta os quadros de quaisquer elementos selecionados para corresponder às suas restrições.
- No modo de edição de restrição, clicar nesse botão ajusta as restrições de quaisquer elementos selecionados para corresponder a seus quadros.

### <a name="bottom-toolbar"></a>Barra de ferramentas inferior

A barra de ferramentas inferior fornece uma maneira de selecionar o dispositivo, a orientação e o zoom usados para exibir um arquivo storyboard ou. xib no designer do iOS:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![A barra de ferramentas inferior, usada para selecionar um dispositivo e uma orientação para a superfície de design](introduction-images/13-bottomtoolbar-vsmac.png "A barra de ferramentas inferior, usada para selecionar um dispositivo e uma orientação para a superfície de design")](introduction-images/13-bottomtoolbar-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![A barra de ferramentas inferior, usada para selecionar um dispositivo e uma orientação para a superfície de design](introduction-images/13-bottomtoolbar-vs.png "A barra de ferramentas inferior, usada para selecionar um dispositivo e uma orientação para a superfície de design")](introduction-images/13-bottomtoolbar-vs-large.png#lightbox)

-----

#### <a name="device-and-orientation"></a>Dispositivo e orientação

Quando expandida, a barra de ferramentas inferior exibe todos os dispositivos, orientações e/ou adaptações aplicáveis ao documento atual. Clicar neles altera a exibição exibida na superfície de design. 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![A barra de ferramentas inferior, expandida para mostrar os dispositivos e orientações](introduction-images/14-bottomtoolbarexpanded-vsmac.png "A barra de ferramentas inferior, expandida para mostrar os dispositivos e orientações")](introduction-images/14-bottomtoolbarexpanded-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![A barra de ferramentas inferior, expandida para mostrar os dispositivos e orientações](introduction-images/14-bottomtoolbarexpanded-vs.png "A barra de ferramentas inferior, expandida para mostrar os dispositivos e orientações")](introduction-images/14-bottomtoolbarexpanded-vs-large.png#lightbox)

-----

Observe que a seleção de um dispositivo e orientação altera apenas a forma como o designer do iOS visualiza o design. Independentemente da seleção atual, as restrições adicionadas recentemente são aplicadas em todos os dispositivos e orientações, a menos que o botão **Editar características** tenha sido usado para especificar o contrário.

Quando as [classes de tamanho](~/ios/user-interface/storyboards/unified-storyboards.md#size-classes) estiverem [habilitadas](~/ios/user-interface/storyboards/unified-storyboards.md#enabling-size-classes), o botão **Editar características** será exibido na barra de ferramentas inferior expandida.  Clicar no botão **Editar características** exibe opções para criar uma variação de interface com base na classe de tamanho representada pelo dispositivo e orientação selecionados. Considere os exemplos a seguir:

- Se a opção **iPhone se** / **retrato**estiver selecionada, o popover fornecerá opções para criar uma variação de interface para a classe tamanho compacto de altura normal. 
- Se o **iPad Pro 9,7 "**  / **paisagem** / de**tela inteira** for selecionado, o popover fornecerá opções para criar uma variação de interface para a classe largura regular, tamanho normal de altura.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![A barra de ferramentas inferior que está sendo usada para variar uma interface por classe de tamanho](introduction-images/15-edittraitsbutton-vsmac.png "A barra de ferramentas inferior que está sendo usada para variar uma interface por classe de tamanho")](introduction-images/15-edittraitsbutton-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![A barra de ferramentas inferior que está sendo usada para variar uma interface por classe de tamanho](introduction-images/15-edittraitsbutton-vs.png "A barra de ferramentas inferior que está sendo usada para variar uma interface por classe de tamanho")](introduction-images/15-edittraitsbutton-vs-large.png#lightbox)

-----

#### <a name="zoom-controls"></a>Controles de zoom

A superfície de design dá suporte ao zoom por meio de vários controles:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)
 
![Os controles de zoom na barra de ferramentas inferior](introduction-images/16-zoomcontrols-vsmac.png "Os controles de zoom na barra de ferramentas inferior")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Os controles de zoom na barra de ferramentas inferior](introduction-images/16-zoomcontrols-vs.png "Os controles de zoom na barra de ferramentas inferior")

-----

Os controles incluem o seguinte:

1. Ajustar zoom
2. Reduzir
3. Ampliar
4. Tamanho real (tamanho de 1:1 pixels)

Esses controles ajustam o zoom na superfície de design. Eles não afetam a interface do usuário do aplicativo em tempo de execução.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

### <a name="properties-pad"></a>Painel de Propriedades

Use o **painel de propriedades** para editar a identidade, os estilos visuais, a acessibilidade e o comportamento de um controle. A captura de tela a seguir ilustra as opções de **painel de propriedades** para um botão:

[![O painel de propriedades para um botão](introduction-images/17-buttonpropertiespad-vsmac.png "O painel de propriedades para um botão")](introduction-images/17-buttonpropertiespad-vsmac-large.png#lightbox)
#### <a name="properties-pad-sections"></a>Painel de Propriedades seções

O **painel de propriedades** contém três seções:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

### <a name="properties-window"></a>Janela Propriedades

Use a **janela Propriedades** para editar a identidade, os estilos visuais, a acessibilidade e o comportamento de um controle. A captura de tela a seguir ilustra as opções da **janela Propriedades** de um botão:

[![A janela Propriedades de um botão](introduction-images/17-buttonpropertieswindow-vs.png "A janela Propriedades de um botão")](introduction-images/17-buttonpropertieswindow-vs-large.png#lightbox)

#### <a name="properties-window-sections"></a>Seções da janela Propriedades

A **janela Propriedades** contém três seções:

-----

1. **Widget** – as propriedades principais do controle, como nome, classe, propriedades de estilo, etc. As propriedades para gerenciar o conteúdo do controle geralmente são colocadas aqui.
2. **Layout** – as propriedades que acompanham a posição e o tamanho do controle, incluindo restrições e quadros, são listadas aqui.
3. **Eventos** – eventos e manipuladores de eventos são especificados aqui. Útil para lidar com eventos como toque, toque, arrastar, etc. Os eventos também podem ser tratados diretamente no código.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

#### <a name="editing-properties-in-the-properties-pad"></a>Editando propriedades no Painel de Propriedades

Além da edição visual na superfície de design, o designer do iOS dá suporte à edição de propriedades no **painel de propriedades**. As propriedades disponíveis são alteradas com base no controle selecionado, conforme ilustrado pelas capturas de tela abaixo:

[![Propriedades do botão](introduction-images/18a-buttonpropertiespad-vsmac.png "Propriedades do botão")](introduction-images/18a-buttonpropertiespad-vsmac-large.png#lightbox)

[![Exibir Propriedades do controlador](introduction-images/18b-viewcontrollerpropertiespad-vsmac.png "Exibir Propriedades do controlador")](introduction-images/18b-viewcontrollerpropertiespad-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

#### <a name="editing-properties-in-the-properties-window"></a>Editando propriedades na janela Propriedades

Além da edição visual na superfície de design, o designer do iOS dá suporte à edição de propriedades na **janela Propriedades**. As propriedades disponíveis são alteradas com base no controle selecionado, conforme ilustrado pelas capturas de tela abaixo:

[![Propriedades do botão](introduction-images/18a-buttonpropertieswindow-vs.png "Propriedades do botão")](introduction-images/18a-buttonpropertieswindow-vs-large.png#lightbox)

[![Exibir Propriedades do controlador](introduction-images/18b-viewcontrollerpropertieswindow-vs.png "Exibir Propriedades do controlador")](introduction-images/18b-viewcontrollerpropertieswindow-vs-large.png#lightbox)

-----

> [!IMPORTANT]
> A seção de identidade do Painel de Propriedades agora mostra um campo de **módulo** . É necessário preencher esta seção somente quando interoperar com classes Swift. Use-o para inserir um nome de módulo para classes Swift, que são namespaces.

#### <a name="default-values"></a>Valores padrão

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Muitas propriedades no **painel de propriedades** não mostram nenhum valor ou um valor padrão. No entanto, o código do aplicativo ainda pode modificar esses valores. O **painel de propriedades** não mostra valores definidos no código.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Muitas propriedades na **janela Propriedades** não mostram nenhum valor ou um valor padrão. No entanto, o código do aplicativo ainda pode modificar esses valores. A **janela Propriedades** não mostra valores definidos no código.

-----

#### <a name="event-handlers"></a>Manipuladores de eventos

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Para especificar manipuladores de eventos personalizados para vários eventos, use a guia **eventos** do **painel de propriedades**. Por exemplo, na captura de tela abaixo, `HandleClick` um método manipula o evento de **toque interno** do botão:

[![O painel de propriedades, com um manipulador de eventos definido para um botão](introduction-images/19-buttonpropertiespadevents-vsmac.png "O painel de propriedades, com um manipulador de eventos definido para um botão")](introduction-images/19-buttonpropertiespadevents-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Para especificar manipuladores de eventos personalizados para vários eventos, use a guia **eventos** da **janela Propriedades**. Por exemplo, na captura de tela abaixo, `HandleClick` um método manipula o evento de **toque interno** do botão:

[![A janela Propriedades, com um manipulador de eventos definido para um botão](introduction-images/19-buttonpropertieswindowevents-vs.png "A janela Propriedades, com um manipulador de eventos definido para um botão")](introduction-images/19-buttonpropertieswindowevents-vs-large.png#lightbox)

-----

Depois que um manipulador de eventos tiver sido especificado, um método de mesmo nome deverá ser adicionado à classe do controlador de exibição correspondente. Caso contrário, `unrecognized selector` ocorrerá uma exceção quando o botão for tocado:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Uma exceção de seletor não reconhecida](introduction-images/20-unrecognizedselector-vsmac.png "Uma exceção de seletor não reconhecida")](introduction-images/20-unrecognizedselector-vsmac-large.png#lightbox)

Observe que, depois que um manipulador de eventos tiver sido especificado no **painel de propriedades**, o designer do IOS abrirá imediatamente o arquivo de código correspondente e a oferta para inserir a declaração do método. 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Uma exceção de seletor não reconhecida](introduction-images/20-unrecognizedselector-vs.png "Uma exceção de seletor não reconhecida")](introduction-images/20-unrecognizedselector-vs-large.png#lightbox)

-----

Para obter um exemplo que usa manipuladores de eventos personalizados, consulte o [guia Olá, iOS introdução](~/ios/get-started/hello-ios/index.md).

### <a name="outline-view"></a>modo de exibição da Estrutura do Código

O designer do iOS também pode exibir a hierarquia de controles de uma interface como um contorno. A estrutura de tópicos está disponível selecionando a guia **estrutura de tópicos do documento** , conforme mostrado abaixo:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![A estrutura de tópicos do documento](introduction-images/21-buttonoutlineview-vsmac.png "A estrutura de tópicos do documento")](introduction-images/21-buttonoutlineview-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![A estrutura de tópicos do documento](introduction-images/21-buttonoutlineview-vs.png "A estrutura de tópicos do documento")](introduction-images/21-buttonoutlineview-vs-large.png#lightbox)

-----

O controle selecionado no modo de exibição de estrutura de tópicos permanece em sincronia com o controle selecionado na superfície de design.  Esse recurso é útil para selecionar um item de uma hierarquia de interface profundamente aninhada.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="revert-to-xcode"></a>Reverter para Xcode

É possível usar o designer do iOS e o Xcode Interface Builder intercambiáveis. Para abrir um storyboard ou um arquivo. xib no Xcode Interface Builder, clique com o botão direito do mouse no arquivo e selecione **abrir com > Xcode Interface Builder**, conforme ilustrado pela captura de tela abaixo:

[![Abrindo um storyboard no Xcode Interface Builder](introduction-images/22-openinxcodeinterfacebuilder-vsmac.png "Abrindo um storyboard no Xcode Interface Builder")](introduction-images/22-openinxcodeinterfacebuilder-vsmac-large.png#lightbox)

Depois de fazer edições no Interface Builder do Xcode, salve o arquivo e retorne ao Visual Studio para Mac. As alterações serão sincronizadas com o projeto Xamarin. iOS.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="revert-to-xcode"></a>Reverter para Xcode

É possível usar o designer do iOS e o Xcode Interface Builder de forma intercambiável, no entanto, o Xcode Interface Builder só está disponível no Mac. Para abrir um arquivo storyboard ou. xib no Xcode Interface Builder em um Mac, abra a solução que contém o projeto Xamarin. iOS no [Visual Studio para Mac](/visualstudio/mac/), clique com o botão direito do mouse no arquivo e selecione **abrir com > Xcode Interface Builder**, conforme ilustrado pela captura de tela abaixo:

[![Abrindo um storyboard no Xcode Interface Builder](introduction-images/22-openinxcodeinterfacebuilder-vsmac.png "Abrindo um storyboard no Xcode Interface Builder")](introduction-images/22-openinxcodeinterfacebuilder-vsmac-large.png#lightbox)

Depois de fazer edições no Interface Builder do Xcode, salve o arquivo e retorne ao Visual Studio para Mac. As alterações serão sincronizadas com o projeto Xamarin. iOS.

-----

## <a name="xib-support"></a>. suporte a XIB

O designer do iOS dá suporte à criação, edição e gerenciamento de arquivos. xib. Esses são arquivos XML que representar exibições únicas e personalizadas que podem ser adicionadas à hierarquia de exibição de um aplicativo. Um arquivo. xib geralmente representa a interface para uma única exibição ou tela em um aplicativo, enquanto um storyboard representa muitas telas e as transições entre elas.

Há muitas opiniões sobre qual solução –. xib arquivos, storyboards ou código – funciona melhor para criar e manter uma interface do usuário. Na realidade, não há uma solução perfeita, e sempre vale a pena considerar a melhor ferramenta para o trabalho em questão. Dito isso, os arquivos. xib são geralmente mais poderosos quando usados para representar uma exibição personalizada necessária em vários lugares em um aplicativo, como uma célula de exibição de tabela personalizada. 

Mais documentação sobre como usar arquivos. xib pode ser encontrada nas seguintes receitas:

- [Usando o modelo View. xib](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/using_the_ios_view_xib_template)
- [Criando um TableViewCell personalizado usando um. xib](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/tables/custom-tableviewcell)
- [Criando uma tela de inicialização usando um. xib](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/launchscreen-xib)

Para obter mais informações sobre o uso de storyboards, consulte a [introdução aos storyboards](~/ios/user-interface/storyboards/index.md).

Este e outros guias relacionados ao iOS designer referem-se ao uso de storyboards como a abordagem padrão para a criação de interfaces do usuário, já que a maioria dos modelos de projeto novos do Xamarin. iOS fornecem um storyboard por padrão.

## <a name="summary"></a>Resumo

Este guia forneceu uma introdução ao designer do iOS, descrevendo seus recursos e descrevendo as ferramentas que ele oferece para criar interfaces de usuário belas.

## <a name="related-links"></a>Links relacionados

- [Introdução ao Storyboards](~/ios/user-interface/storyboards/index.md)
- [Instruções de controles designáveis do iOS](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Hello, iOS](~/ios/get-started/hello-ios/index.md)
- [Multitela Hello, iOS](~/ios/get-started/hello-ios-multiscreen/index.md)
- [Visão geral de Designer Android](~/android/user-interface/android-designer/index.md)
- [Classes e métodos parciais](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)
- [Mergulhando no Xamarin Designer para iOS-evoluir 2014 (vídeo)](https://www.youtube.com/watch?v=W4H9uLjoEjM)
- [Usando o designer do iOS para criar uma tela de inicialização (vídeo)](https://university.xamarin.com/lightninglectures/using-the-ios-designer-to-create-a-launch-screen)
