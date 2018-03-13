---
title: "Noções básicas de Designer do iOS"
description: "Este guia apresenta o Xamarin Designer para iOS. Ele demonstra como usar o Designer do iOS para visualmente dispor controles, como acessar esses controles no código e como editar propriedades."
ms.topic: article
ms.prod: xamarin
ms.assetid: E7045E41-0DEF-416B-BCDB-52502350F61C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 01/31/2018
ms.openlocfilehash: a2445e49005175f62e4d7cd8aadccb5f596177bf
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="ios-designer-basics"></a>Noções básicas de Designer do iOS

_Este guia apresenta o Xamarin Designer para iOS. Ele demonstra como usar o Designer do iOS para visualmente dispor controles, como acessar esses controles no código e como editar propriedades._

O Designer de Xamarin para iOS é um designer visual interface semelhante ao Interface Builder do Xcode e o Designer de Android. Alguns de seus diversos recursos incluem integração perfeita com o Visual Studio para Mac e Visual Studio 2015 e 2017, edição arrastar-e-soltar, uma interface para a configuração de manipuladores de eventos e a capacidade de renderizar controles personalizados.

## <a name="requirements"></a>Requisitos

O iOS Designer está disponível no Visual Studio para Mac e no Visual Studio 2015 e 2017 no Windows. No Visual Studio 2015 ou 2017, o Designer do iOS requer uma conexão para um host de compilação Mac configurado corretamente, embora Xcode não precisa estar em execução.

Este guia presume familiaridade com o conteúdo abordados o [guias de Introdução](~/ios/get-started/index.md).

<a name="how-it-works" />

## <a name="how-the-ios-designer-works"></a>Como funciona o Designer do iOS

Esta seção descreve como o Designer do iOS facilita a criação de uma interface de usuário e conectá-lo ao código.

O Designer do iOS permite aos desenvolvedores criar visualmente a interface do usuário do aplicativo. Conforme descrito no tópico a [Introdução a Storyboards](~/ios/user-interface/storyboards/index.md) guia, um storyboard descreve as telas (Exibir controladores) que compõem um aplicativo, os elementos de interface (views) inseridos nesses controladores de exibição e o fluxo geral de navegação do aplicativo . 

Um controlador de exibição tem duas partes: uma representação visual no Designer de iOS e uma classe associada do c#:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Um controlador de exibição no Designer de iOS](introduction-images/1-storyboardwithviewcontroller-vsmac.png "um controlador de exibição no Designer de iOS")](introduction-images/1-storyboardwithviewcontroller-vsmac-large.png#lightbox)

[![O código para um controlador de exibição](introduction-images/2-viewcontrollercode-vsmac.png "o código para um controlador de exibição")](introduction-images/2-viewcontrollercode-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Um controlador de exibição no Designer de iOS](introduction-images/1-storyboardwithviewcontroller-vs.png "um controlador de exibição no Designer de iOS")](introduction-images/1-storyboardwithviewcontroller-vs-large.png#lightbox)

[![O código para um controlador de exibição](introduction-images/2-viewcontrollercode-vs.png "o código para um controlador de exibição")](introduction-images/2-viewcontrollercode-vs-large.png#lightbox)

-----

Em seu estado padrão, um controlador de exibição não fornece nenhuma funcionalidade; ele deve ser preenchido com controles. Esses controles são colocados no modo de exibição do controlador, a área retangular que contém todo o conteúdo da tela. A maioria dos controladores de exibição conter controles comuns como botões e rótulos de campos de texto, conforme ilustrado na seguinte captura de tela, que mostra um controlador de exibição que contém um botão: 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Um controlador de exibição que contém um botão](introduction-images/3-viewcontrollerwithbutton-vsmac.png "um controlador de exibição que contém um botão")](introduction-images/3-viewcontrollerwithbutton-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Um controlador de exibição que contém um botão](introduction-images/3-viewcontrollerwithbutton-vs.png "um controlador de exibição que contém um botão")](introduction-images/3-viewcontrollerwithbutton-vs-large.png#lightbox)

-----

Alguns controles, como rótulos que contém texto estático, podem ser adicionados ao controlador de exibição e sozinho. No entanto, mais frequentemente que não, os controles devem ser personalizados programaticamente. Por exemplo, o botão adicionado acima deve fazer algo quando tocada, para que um manipulador de eventos deve ser adicionado no código.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Para acessar e manipular o botão no código, ele deve ter um identificador exclusivo. Forneça um identificador exclusivo, selecionando o botão, abrindo o **propriedades de preenchimento**e configuração de seu **nome** campo para um valor como "SubmitButton":

[![Definir o nome do botão no painel de propriedades](introduction-images/4-settingbuttonname-vsmac.png "definindo o nome do botão no painel de propriedades")](introduction-images/4-settingbuttonname-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para acessar e manipular o botão no código, ele deve ter um identificador exclusivo. Fornece um identificador exclusivo, selecionando o botão, abrindo o **janela propriedades**e a configuração de seu **nome** campo para um valor como "SubmitButton":

[![Definindo o nome do botão na janela de propriedades](introduction-images/4-settingbuttonname-vs.png "definindo o nome do botão na janela de propriedades")](introduction-images/4-settingbuttonname-vs-large.png#lightbox)

-----

Agora que o botão tem um nome, ele pode ser acessado no código. Mas como isso funciona?

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

No **solução preenchimento**, navegando para **ViewController.cs** e clicar no indicador de divulgação revela que o controlador de exibição `ViewController` intervalos de definição de classe dois arquivos, cada um deles contém uma [classe parcial](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods) definição:

[![Os dois arquivos que formam a classe ViewController: ViewController.cs e ViewController.designer.cs](introduction-images/5-twoviewcontrollerfiles-vsmac.png "os dois arquivos que formam a classe ViewController: ViewController.cs e ViewController.designer.cs")](introduction-images/5-twoviewcontrollerfiles-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

No **Solution Explorer**, navegando para **ViewController.cs** e clicar no indicador de divulgação revela que o controlador de exibição `ViewController` definição de classe abrange dois arquivos, cada um dos que contém um [classe parcial](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods) definição:

[![Os dois arquivos que formam a classe ViewController: ViewController.cs e ViewController.designer.cs](introduction-images/5-twoviewcontrollerfiles-vs.png "os dois arquivos que formam a classe ViewController: ViewController.cs e ViewController.designer.cs")](introduction-images/5-twoviewcontrollerfiles-vs-large.png#lightbox)

-----

- **ViewController.cs** devem ser preenchidos com código personalizado relacionado ao `ViewController` classe. Nesse arquivo, o `ViewController` classe pode responder a iOS vários métodos de ciclo de vida do controlador de exibição, personalizar a interface do usuário e responder a entrada, como toques de botão do usuário.

- **ViewController.designer.cs** é um arquivo gerado, criado pelo Designer para mapear a interface construída visualmente ao código do iOS. Desde que as alterações neste arquivo serão substituídas, não deve ser modificado. Declarações de propriedade neste arquivo tornam possível para o código no `ViewController` de classe para acessar, por **nome**, controla o conjunto de backup no Designer de iOS. Abrindo **ViewController.designer.cs** revela o código a seguir:

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

O `SubmitButton` declaração de propriedade se conecta a todo o `ViewController` classe - não apenas o **ViewController.designer.cs** arquivo – para o botão definido no storyboard. Como **ViewController.cs** define a parte do `ViewController` classe, ela tem acesso ao `SubmitButton`.

Captura de tela a seguir ilustra que o IntelliSense agora reconhece o `SubmitButton` referência em **ViewController.cs**:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![IntelliSense reconhecer a referência SubmitButton](introduction-images/6-submitbuttonintellisense-vsmac.png "IntelliSense reconhecer a referência SubmitButton")](introduction-images/6-submitbuttonintellisense-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![IntelliSense reconhecer a referência SubmitButton](introduction-images/6-submitbuttonintellisense-vs.png "IntelliSense reconhecer a referência SubmitButton")](introduction-images/6-submitbuttonintellisense-vs-large.png#lightbox)

-----

Esta seção demonstrou como criar um botão no Designer de iOS e acessar esse botão no código.

O restante deste documento fornece uma visão mais do iOS Designer.

## <a name="ios-designer-basics"></a>Noções básicas de Designer do iOS

Esta seção apresenta as partes do Designer de iOS e fornece um tour de seus recursos.

### <a name="launching-the-ios-designer"></a>Iniciar o Designer do iOS

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Xamarin projetos criados com o Visual Studio para Mac incluem um storyboard. Para exibir o conteúdo de um storyboard, duas vezes no arquivo .storyboard o **solução preenchimento**:

[![Abra um storyboard no iOS Designer](introduction-images/7-storyboardopen-vsmac.png "abrir um storyboard no Designer de iOS")](introduction-images/7-storyboardopen-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

A maioria dos projetos de xamarin criado com o Visual Studio 2015 ou 2017 incluem um storyboard. Para exibir o conteúdo de um storyboard, duas vezes no arquivo .storyboard o **Solution Explorer**:

[![Abra um storyboard no iOS Designer](introduction-images/7-storyboardopen-vs.png "abrir um storyboard no Designer de iOS")](introduction-images/7-storyboardopen-vs-large.png#lightbox)

-----

<a name="iOS_Designer_features"/>

### <a name="ios-designer-features"></a>recursos do Designer do iOS

O Designer do iOS tem seis seções principais:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Seções do iOS Designer](introduction-images/8-sixpartsofiosdesigner-vsmac.png "seções do iOS Designer")](introduction-images/8-sixpartsofiosdesigner-vsmac-large.png#lightbox)

1. **Superfície de design** – a área de trabalho principal do Designer do iOS. Mostra a área do documento, ele permite construção visual de interfaces do usuário.
2. **Barra de ferramentas de restrições** – permite alternar entre o quadro editando o modo e o modo de edição de restrição, duas maneiras diferentes para posicionar elementos em uma interface do usuário.
3. **Caixa de ferramentas** – lista de controladores, objetos, controles, modos de exibição de dados, reconhecedores de gestos, windows e barras que podem ser arrastados para a superfície de design e adicionado a uma interface de usuário.
4. **Painel de propriedades** – mostra as propriedades para o controle selecionado, incluindo identidade, estilos visuais, acessibilidade, layout e comportamento.
5. **Estrutura de tópicos de documento** – mostra a árvore de controles que compõem o layout para a interface que está sendo editado. Clicar em um item na árvore de seleciona-lo no Designer de iOS e mostra suas propriedades no **propriedades de preenchimento**. Isso é útil para selecionar um controle específico em uma interface de usuário aninhadas.
6. **Baixo da barra de ferramentas** – contém opções para alterar como o Designer do iOS exibe o arquivo .storyboard ou .xib, incluindo o dispositivo, orientação e zoom.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Seções do iOS Designer](introduction-images/8-sixpartsofiosdesigner-vs.png "seções do iOS Designer")](introduction-images/8-sixpartsofiosdesigner-vs-large.png#lightbox)

1. **Superfície de design** – a área de trabalho principal do Designer do iOS. Mostra a área do documento, ele permite construção visual de interfaces do usuário.
2. **Barra de ferramentas de restrições** – permite alternar entre o quadro editando o modo e o modo de edição de restrição, duas maneiras diferentes para posicionar elementos em uma interface do usuário.
3. **Caixa de ferramentas** – lista de controladores, objetos, controles, modos de exibição de dados, reconhecedores de gestos, windows e barras que podem ser arrastados para a superfície de design e adicionado a uma interface de usuário.
4. **Janela propriedades** – mostra as propriedades para o controle selecionado, incluindo identidade, estilos visuais, acessibilidade, layout e comportamento.
5. **Estrutura de tópicos de documento** – mostra a árvore de controles que compõem o layout para a interface que está sendo editado. Clicar em um item na árvore de seleciona-lo no Designer de iOS e mostra suas propriedades no **janela propriedades**. Isso é útil para selecionar um controle específico em uma interface de usuário aninhadas.
6. **Baixo da barra de ferramentas** – contém opções para alterar como o Designer do iOS exibe o arquivo .storyboard ou .xib, incluindo o dispositivo, orientação e zoom.

-----

### <a name="design-workflow"></a>Criar fluxo de trabalho

#### <a name="adding-a-control-to-the-interface"></a>Adicionando um controle para a interface

Para adicionar um controle a uma interface, arraste-o para o **caixa de ferramentas** e solte-o na superfície de design. Ao adicionar ou posicionamento de um controle, diretrizes verticais e horizontais realçam posições do layout comumente usados como margens, centro horizontal e vertical center:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)
 
![Na superfície de design, diretrizes realçar posições do layout usadas comumente](introduction-images/9-layoutguides-vsmac.png "na superfície de design, diretrizes realçar posições do layout comumente usados")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Na superfície de design, diretrizes realçar posições do layout usadas comumente](introduction-images/9-layoutguides-vs.png "na superfície de design, diretrizes realçar posições do layout comumente usados")

-----

A linha pontilhada azul no exemplo acima fornece uma orientação de alinhamento visual de centro horizontal para ajudá-lo com o posicionamento de botão.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

#### <a name="context-menu-commands"></a>Comandos de menu de contexto

Um menu de contexto está disponível na superfície de design e no **esboço de documento**. Esse menu fornece comandos para o controle selecionado e seu pai, que é útil ao trabalhar com exibições em uma hierarquia aninhada:

[![O menu de contexto na superfície de design](introduction-images/10-contextmenudesignsurface-vsmac.png "o menu de contexto na superfície de design")](introduction-images/10-contextmenudesignsurface-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

-----

### <a name="constraints-toolbar"></a>Barra de ferramentas de restrições

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)
 
[![A barra de ferramentas de restrições](introduction-images/11-constraintstoolbar-vsmac.png "a barra de ferramentas de restrições")](introduction-images/11-constraintstoolbar-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![A barra de ferramentas de restrições](introduction-images/11-constraintstoolbar-vs.png "a barra de ferramentas de restrições")](introduction-images/11-constraintstoolbar-vs-large.png#lightbox)

-----

A barra de ferramentas de restrições foi atualizada e agora consiste em dois controles: o modo de edição de quadro / restrição edição ativar/desativar modo e as restrições de atualização / atualização de botão de quadros.

#### <a name="frame-editing-mode--constraint-editing-mode-toggle"></a>Modo de edição de quadro / Ativar/desativar modo de edição de restrição

Nas versões anteriores do iOS Designer, clicando em um modo de exibição estiver selecionado na superfície de design alternado entre o quadro de edição de modo e o modo de edição de restrição. Agora, um controle de alternância na barra de ferramentas restrições alterna entre os modos de edição.

- Modo de edição de quadro:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Botão de modo de edição de quadro](introduction-images/12a-frameeditingmode-vsmac.png "botão modo de edição de quadro")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Botão de modo de edição de quadro](introduction-images/12a-frameeditingmode-vs.png "botão modo de edição de quadro")

-----

- Modo de edição de restrição:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Botão de modo de edição de restrição](introduction-images/12b-constrainteditingmode-vsmac.png "botão modo de edição de restrição")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Botão de modo de edição de restrição](introduction-images/12b-constrainteditingmode-vs.png "botão modo de edição de restrição")

-----

#### <a name="update-constraints--update-frames-button"></a>Restrições de atualização / atualização de botão de quadros

As restrições de atualização / atualização quadros botão fica à direita do quadro de modo de edição / ativar/desativar modo de edição de restrição.

- No modo de edição de quadro, este botão ajusta os quadros de qualquer elemento selecionado para corresponder a suas restrições.
- No modo de edição de restrição, este botão ajusta as restrições de elementos selecionados para corresponder a seus quadros.

### <a name="bottom-toolbar"></a>Barra de ferramentas inferior

Barra de ferramentas inferior fornece uma maneira de selecionar o dispositivo, orientação e zoom usado para exibir um arquivo de storyboard ou .xib no iOS Designer:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Barra de ferramentas inferior, usado para selecionar um dispositivo e orientação para a superfície de design](introduction-images/13-bottomtoolbar-vsmac.png "barra de ferramentas inferior, usado para selecionar um dispositivo e orientação para a superfície de design")](introduction-images/13-bottomtoolbar-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Barra de ferramentas inferior, usado para selecionar um dispositivo e orientação para a superfície de design](introduction-images/13-bottomtoolbar-vs.png "barra de ferramentas inferior, usado para selecionar um dispositivo e orientação para a superfície de design")](introduction-images/13-bottomtoolbar-vs-large.png#lightbox)

-----

#### <a name="device-and-orientation"></a>Dispositivo e orientação

Quando expandido, barra de ferramentas inferior exibe todos os dispositivos, orientações e/ou adaptações aplicáveis ao documento atual. Clicando-se neles altera o modo de exibição na superfície de design. 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Barra de ferramentas inferior, expandida para mostrar os dispositivos e orientações](introduction-images/14-bottomtoolbarexpanded-vsmac.png "barra de ferramentas inferior, expandida para mostrar os dispositivos e orientações")](introduction-images/14-bottomtoolbarexpanded-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Barra de ferramentas inferior, expandida para mostrar os dispositivos e orientações](introduction-images/14-bottomtoolbarexpanded-vs.png "barra de ferramentas inferior, expandida para mostrar os dispositivos e orientações")](introduction-images/14-bottomtoolbarexpanded-vs-large.png#lightbox)

-----

Observe que a seleção de um dispositivo e a orientação altera apenas como o Designer do iOS visualiza o design. Independentemente da seleção atual, recém-adicionado restrições são aplicadas em todos os dispositivos e orientações, a menos que o **Editar características** botão foi usado para especificar outra forma.

Quando [tamanho classes](~/ios/user-interface/storyboards/unified-storyboards.md#size-classes) são [habilitado](~/ios/user-interface/storyboards/unified-storyboards.md#enabling-size-classes), o **Editar características** botão será exibido na barra de ferramentas inferior expandido.  Clicando o **Editar características** botão exibe opções para criar uma variação de interface baseada na classe de tamanho representada pelo dispositivo selecionado e orientação. Considere os exemplos a seguir:

- Se **iPhone SE** / **retrato**, é selecionada, o popover fornecerá opções para criar uma variação de interface para a largura compact, classe de tamanho regular de altura. 
- Se **iPad Pro 9.7"** / **paisagem** / **tela inteira** é selecionada, o popover fornecerá opções para criar uma variação de interface para a largura regular, a classe de tamanho regular de altura.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Barra de ferramentas inferior que estão sendo usado para variar a uma interface por classe de tamanho](introduction-images/15-edittraitsbutton-vsmac.png "barra de ferramentas inferior que estão sendo usado para variar a uma interface por classe de tamanho")](introduction-images/15-edittraitsbutton-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Barra de ferramentas inferior que estão sendo usado para variar a uma interface por classe de tamanho](introduction-images/15-edittraitsbutton-vs.png "barra de ferramentas inferior que estão sendo usado para variar a uma interface por classe de tamanho")](introduction-images/15-edittraitsbutton-vs-large.png#lightbox)

-----

#### <a name="zoom-controls"></a>Controles de zoom

A superfície de design oferece suporte a aumentar o zoom por meio de vários controles:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)
 
![Os controles de zoom na barra de ferramentas inferior](introduction-images/16-zoomcontrols-vsmac.png "os controles de zoom na barra de ferramentas inferior")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Os controles de zoom na barra de ferramentas inferior](introduction-images/16-zoomcontrols-vs.png "os controles de zoom na barra de ferramentas inferior")

-----

Os controles incluem o seguinte:

1. Aplicar zoom para ajustar
2. Reduzir
3. Ampliar
4. Tamanho real (tamanho de pixel de 1:1)

Esses controles ajuste o zoom na superfície de design. Elas não afetam a interface do usuário do aplicativo em tempo de execução.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

### <a name="properties-pad"></a>Painel de propriedades

Use o **propriedades de preenchimento** para editar a identidade, estilos visuais, acessibilidade e comportamento de um controle. Captura de tela a seguir ilustra o **propriedades de preenchimento** opções para um botão:

[![Painel de propriedades de um botão](introduction-images/17-buttonpropertiespad-vsmac.png "o painel de propriedades para um botão")](introduction-images/17-buttonpropertiespad-vsmac-large.png#lightbox)
#### <a name="properties-pad-sections"></a>Seções do painel de propriedades

O **propriedades de preenchimento** contém três seções:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

### <a name="properties-window"></a>Janela Propriedades

Use o **janela propriedades** para editar a identidade, estilos visuais, acessibilidade e comportamento de um controle. Captura de tela a seguir ilustra o **janela propriedades** opções para um botão:

[![A janela Propriedades de um botão](introduction-images/17-buttonpropertieswindow-vs.png "a janela de propriedades de um botão")](introduction-images/17-buttonpropertieswindow-vs-large.png#lightbox)

#### <a name="properties-window-sections"></a>Seções de janela de propriedades

O **janela propriedades** contém três seções:

-----

1.  **Widget** – as principais propriedades do controle, como nome, classe, propriedades de estilo, etc. Propriedades de gerenciamento de conteúdo do controle geralmente são colocadas aqui.
2.  **Layout** – propriedades que controlam a posição e o tamanho do controle, inclusive restrições e quadros, são listadas aqui.
3.  **Eventos** – eventos e manipuladores de eventos são especificados aqui. Útil para tratar de eventos, como toque, toque, arraste, etc. Eventos também podem ser tratados diretamente no código.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

#### <a name="editing-properties-in-the-properties-pad"></a>Editar as propriedades no painel de propriedades

Além de edição visual na superfície de design, o Designer do iOS oferece suporte a edição das propriedades no **propriedades de preenchimento**. A alteração de propriedades disponíveis com base no controle selecionado, conforme ilustrado pela capturas de tela abaixo:

[![Botão propriedades](introduction-images/18a-buttonpropertiespad-vsmac.png "botão Propriedades")](introduction-images/18a-buttonpropertiespad-vsmac-large.png#lightbox)

[![Exibir propriedades do controlador](introduction-images/18b-viewcontrollerpropertiespad-vsmac.png "exibir propriedades do controlador")](introduction-images/18b-viewcontrollerpropertiespad-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

#### <a name="editing-properties-in-the-properties-window"></a>Editar as propriedades na janela Propriedades

Além de edição visual na superfície de design, o Designer do iOS oferece suporte a edição das propriedades no **janela propriedades**. A alteração de propriedades disponíveis com base no controle selecionado, conforme ilustrado pela capturas de tela abaixo:

[![Botão propriedades](introduction-images/18a-buttonpropertieswindow-vs.png "botão Propriedades")](introduction-images/18a-buttonpropertieswindow-vs-large.png#lightbox)

[![Exibir propriedades do controlador](introduction-images/18b-viewcontrollerpropertieswindow-vs.png "exibir propriedades do controlador")](introduction-images/18b-viewcontrollerpropertieswindow-vs-large.png#lightbox)

-----

> [!IMPORTANT]
> A seção de identidade do painel de propriedades agora mostra um **módulo** campo. É necessário preencher nesta seção somente ao interagir com classes ágil. Usado para inserir um nome de módulo para Swift classes, que são com namespace.

#### <a name="default-values"></a>Valores padrão

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Muitas propriedades no **propriedades de preenchimento** mostrar nenhum valor ou um valor padrão. No entanto, o código do aplicativo ainda pode modificar esses valores. O **propriedades de preenchimento** não mostra os valores definidos no código.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Muitas propriedades no **janela propriedades** mostrar nenhum valor ou um valor padrão. No entanto, o código do aplicativo ainda pode modificar esses valores. O **janela propriedades** não mostra os valores definidos no código.

-----

#### <a name="event-handlers"></a>Manipuladores de eventos

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Para especificar os manipuladores de eventos personalizados para vários eventos, use o **eventos** guia do **propriedades de preenchimento**. Por exemplo, na captura de tela abaixo, um `HandleClick` método lida com o botão **Touch dentro** evento:

[![O painel Propriedades, com um manipulador de eventos definida para um botão](introduction-images/19-buttonpropertiespadevents-vsmac.png "o painel de propriedades, com um manipulador de eventos definida para um botão")](introduction-images/19-buttonpropertiespadevents-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para especificar os manipuladores de eventos personalizados para vários eventos, use o **eventos** guia o **janela propriedades**. Por exemplo, na captura de tela abaixo, um `HandleClick` método lida com o botão **Touch dentro** evento:

[![A janela de propriedades, com um manipulador de eventos definida para um botão](introduction-images/19-buttonpropertieswindowevents-vs.png "a janela de propriedades, com um manipulador de eventos definida para um botão")](introduction-images/19-buttonpropertieswindowevents-vs-large.png#lightbox)

-----

Depois que um manipulador de eventos tiver sido especificado, um método de mesmo nome deve ser adicionado à classe de controlador de exibição correspondente. Caso contrário, um `unrecognized selector` exceção ocorrerá quando o botão é tocado:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Uma exceção não reconhecido seletor](introduction-images/20-unrecognizedselector-vsmac.png "uma exceção de seletor não reconhecido")](introduction-images/20-unrecognizedselector-vsmac-large.png#lightbox)

Observe que, após um manipulador de eventos foi especificado o **propriedades de preenchimento**, iOS Designer abrirá imediatamente o arquivo de código correspondente e oferecer para inserir a declaração de método. 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Uma exceção não reconhecido seletor](introduction-images/20-unrecognizedselector-vs.png "uma exceção de seletor não reconhecido")](introduction-images/20-unrecognizedselector-vs-large.png#lightbox)

-----

Para obter um exemplo que usa os manipuladores de eventos personalizados, consulte o [Hello, iOS Getting Started Guide](~/ios/get-started/hello-ios/index.md).

### <a name="outline-view"></a>Estrutura de tópicos

O Designer do iOS também pode exibir a hierarquia da interface de controles como uma estrutura de tópicos. A estrutura de tópicos está disponível selecionando o **esboço de documento** guia, conforme mostrado abaixo:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![A estrutura de tópicos do documento](introduction-images/21-buttonoutlineview-vsmac.png "a estrutura de tópicos do documento")](introduction-images/21-buttonoutlineview-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![A estrutura de tópicos do documento](introduction-images/21-buttonoutlineview-vs.png "a estrutura de tópicos do documento")](introduction-images/21-buttonoutlineview-vs-large.png#lightbox)

-----

O controle selecionado no modo de exibição de estrutura de tópicos permanece em sincronia com o controle selecionado na superfície de design.  Esse recurso é útil para selecionar um item de uma hierarquia aninhada de interface.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

## <a name="revert-to-xcode"></a>Reverter para Xcode

É possível usar o Designer e o construtor de Interface do Xcode do iOS alternadamente. Para abrir o arquivo .xib ou um storyboard no Xcode Interface Builder, clique com botão direito no arquivo e selecione **abrir com > Xcode Interface Builder**, conforme ilustrado na captura de tela abaixo:

[![Abrindo um storyboard no Xcode Interface Builder](introduction-images/22-openinxcodeinterfacebuilder-vsmac.png "abrindo um storyboard no Xcode Interface Builder")](introduction-images/22-openinxcodeinterfacebuilder-vsmac-large.png#lightbox)

Depois de fazer edições no Xcode Interface Builder, salve o arquivo e retornar ao Visual Studio para Mac. As alterações serão sincronizadas com o projeto xamarin.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

-----

## <a name="xib-support"></a>suporte de .xib

O Designer do iOS dá suporte à criação, edição e gerenciamento de arquivos .xib. Esses são arquivos XML que representar personalizado, único, modos de exibição que podem ser adicionado à hierarquia do modo de exibição do aplicativo. Um arquivo .xib geralmente representa a interface para um único modo de exibição ou uma tela em um aplicativo, enquanto um storyboard representa várias telas e as transições entre eles.

Há muitas opiniões sobre qual solução – arquivos .xib, storyboards ou código – funciona melhor para criar e manter uma interface do usuário. Na realidade, não há nenhuma solução perfeita e é sempre vale a pena considerar a melhor ferramenta para o trabalho em questão. Dito isso, os arquivos de .xib são geralmente mais eficientes quando usado para representar uma exibição personalizada necessária em vários locais em um aplicativo, como uma célula de exibição de tabela personalizados. 

Mais documentação sobre como usar arquivos .xib pode ser encontrada em receitas a seguir:

- [Usando o modelo de exibição de .xib](https://developer.xamarin.com/recipes/ios/general/templates/using_the_ios_view_xib_template/)
- [Criando um TableViewCell personalizado usando um .xib](https://developer.xamarin.com/recipes/ios/content_controls/tables/custom-tableviewcell/)
- [Criando uma tela iniciar usando um .xib](https://developer.xamarin.com/recipes/ios/general/templates/launchscreen-xib/)

Para obter mais informações sobre o uso de storyboards, consulte o [Introdução a Storyboards](~/ios/user-interface/storyboards/index.md).

Esse e outros guias de Designer do iOS referem-se ao uso de storyboards como o método padrão para a criação de interfaces do usuário, pois a maioria dos xamarin novos modelos de projeto fornecem um storyboard por padrão.

## <a name="summary"></a>Resumo

Este guia apresentou uma introdução para o Designer, que descreve os recursos e as ferramentas para a criação de interfaces de usuário lindos oferece de estrutura de tópicos do iOS.

## <a name="related-links"></a>Links relacionados

- [Introdução ao Storyboards](~/ios/user-interface/storyboards/index.md)
- [iOS projetáveis controles passo a passo](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Hello, iOS](~/ios/get-started/hello-ios/index.md)
- [Multitela Hello, iOS](~/ios/get-started/hello-ios-multiscreen/index.md)
- [Visão geral do Android Designer](~/android/user-interface/android-designer/index.md)
- [Classes e métodos parciais](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)
- [Se aprofundar no Xamarin Designer para iOS - 2014 evoluir (vídeo)](https://www.youtube.com/watch?v=W4H9uLjoEjM)
- [Usando o Designer do iOS para criar uma tela Iniciar (vídeo)](https://university.xamarin.com/lightninglectures/using-the-ios-designer-to-create-a-launch-screen)
