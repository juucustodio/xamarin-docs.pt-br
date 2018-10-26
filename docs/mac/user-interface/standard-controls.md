---
title: Controles padrão no xamarin. Mac
description: Este artigo aborda o trabalho com controles AppKit padrão, como botões, rótulos, campos de texto, caixas de seleção e segmentados controles em um aplicativo xamarin. Mac. Ele descreve adicioná-los a uma interface com o Interface Builder e interagir com eles no código.
ms.prod: xamarin
ms.assetid: d2593883-d255-431f-9781-75f04d8cecea
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 9f5bdc9a79c514f0310d29b3d054fb7e9659d669
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123785"
---
# <a name="standard-controls-in-xamarinmac"></a>Controles padrão no xamarin. Mac

_Este artigo aborda o trabalho com controles AppKit padrão, como botões, rótulos, campos de texto, caixas de seleção e segmentados controles em um aplicativo xamarin. Mac. Ele descreve adicioná-los a uma interface com o Interface Builder e interagir com eles no código._

Ao trabalhar com c# e .NET em um aplicativo xamarin. Mac, você tem acesso à mesma AppKit controla o que um desenvolvedor que trabalha em *Objective-C* e *Xcode* faz. Como o xamarin. Mac se integra diretamente com Xcode, você pode usar do Xcode _construtor de Interface_ para criar e manter seus controles Appkit (ou, opcionalmente, criá-los diretamente em código c#).

Controles AppKit são os elementos de interface do usuário que são usados para criar a Interface do usuário do seu aplicativo xamarin. Mac. Eles consistem em elementos como botões, rótulos, campos de texto, caixas de seleção e controles segmentados e fazer com que ações instantâneas ou resultados visíveis quando um usuário manipule-los.

[![](standard-controls-images/intro01.png "A tela principal do aplicativo de exemplo")](standard-controls-images/intro01.png#lightbox)

Neste artigo, abordaremos os fundamentos de trabalhar com controles AppKit em um aplicativo xamarin. Mac. É altamente recomendável que você trabalhe por meio de [Hello, Mac](~/mac/get-started/hello-mac.md) pela primeira vez, especificamente o artigo a [Introdução ao Interface Builder e Xcode](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [saídas e ações](~/mac/get-started/hello-mac.md#outlets-and-actions) seções, como ela aborda os principais conceitos e técnicas que usaremos neste artigo.

Talvez você queira dar uma olhada o [classes expondo c# / métodos para Objective-C](~/mac/internals/how-it-works.md) seção o [recursos internos de xamarin. Mac](~/mac/internals/how-it-works.md) documentar Além disso, ele explica o `Register` e `Export` comandos usado para transmissão-up suas classes de c# a objetos de Objective-C e elementos de interface do usuário.

<a name="Introduction_to_Controls_and_Views" />

## <a name="introduction-to-controls-and-views"></a>Introdução aos controles e modos de exibição

macOS (anteriormente conhecido como o Mac OS X) fornece um conjunto padrão de controles de Interface do usuário por meio da estrutura AppKit. Eles consistem em elementos como botões, rótulos, campos de texto, caixas de seleção e controles segmentados e fazer com que ações instantâneas ou resultados visíveis quando um usuário manipule-los.

Todos os controles AppKit tem uma aparência padrão e internos que poderá ser adequada para a maioria dos usos, alguns especificam uma aparência alternativa para uso em uma área da janela do quadro ou em um _efeito de vibração_ contexto, tal como em uma área da barra lateral ou em um Widget de centro de notificações.

Apple sugere as diretrizes a seguir ao trabalhar com controles AppKit:

- Evite a mistura de tamanhos de controle na mesma exibição.
- Em geral, evite redimensionar controles verticalmente.
- Use a fonte do sistema e o tamanho do texto apropriado dentro de um controle.
- Use o adequada espaçamento entre controles.

Para obter mais informações, consulte pleas a [sobre controles e modos de exibição](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1) seção da Apple [diretrizes de Interface humana dos X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/).

<a name="Using_Controls_in_a_Window_Frame" />

### <a name="using-controls-in-a-window-frame"></a>Usando controles em um quadro de janela

Há um subconjunto de controles AppKit que incluem um estilo de exibição que lhes permite ser incluir em uma área da janela quadro. Para obter um exemplo, consulte a barra de ferramentas do aplicativo de email:

[![](standard-controls-images/mailapp.png "Um quadro de janela de Mac")](standard-controls-images/mailapp.png#lightbox)

- **Botão de textura de ida e volta** - uma `NSButton` com um estilo de `NSTexturedRoundedBezelStyle`.
- **Texturizado arredondado controle segmentado** - uma `NSSegmentedControl` com um estilo de `NSSegmentStyleTexturedRounded`.
- **Texturizado arredondado controle segmentado** - uma `NSSegmentedControl` com um estilo de `NSSegmentStyleSeparated`.
- **Menu pop-up texturizado de ida e volta** - uma `NSPopUpButton` com um estilo de `NSTexturedRoundedBezelStyle`.
- **Texturizado suspensa Menu de ida e volta** - uma `NSPopUpButton` com um estilo de `NSTexturedRoundedBezelStyle`.
- **Barra de pesquisa** - uma `NSSearchField`.

Apple sugere as diretrizes a seguir ao trabalhar com controles AppKit em um quadro de janela:

- Não use estilos de controle específico do quadro de janela no corpo da janela.
- Não use o corpo da janela controles ou estilos no quadro de janela.

Para obter mais informações, consulte pleas a [sobre controles e modos de exibição](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1) seção da Apple [diretrizes de Interface humana dos X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/).

<a name="Creating_a_User_Interface_in_Interface_Builder" />

## <a name="creating-a-user-interface-in-interface-builder"></a>Criando uma Interface do usuário no construtor de Interface

Quando você cria um novo aplicativo xamarin. Mac Cocoa, você obtém uma janela de padrão em branco, por padrão. Esse windows é definido em um `.storyboard` arquivo incluído automaticamente no projeto. Para editar seu design do windows, nos **Gerenciador de soluções**, clique duas vezes o `Main.storyboard` arquivo:

[![](standard-controls-images/edit01.png "Selecionando o Storyboard principal no Gerenciador de soluções")](standard-controls-images/edit01.png#lightbox)

Isso abrirá o design de janela no Interface Builder do Xcode:

[![](standard-controls-images/edit02.png "Editando o storyboard no Xcode")](standard-controls-images/edit02.png#lightbox)

Para criar sua Interface do usuário, você arrastará elementos de interface do usuário (controles AppKit) do **Inspetor de biblioteca** para o **Editor de Interface** no construtor de Interface. No exemplo a seguir, uma **exibição de divisão Vertical** controle tiver sido medicamentos da **Inspetor de biblioteca** e colocados na janela da **Editor de Interface**:

[![](standard-controls-images/edit03.png "Selecionar um modo de exibição de divisão da biblioteca")](standard-controls-images/edit03.png#lightbox)

Para obter mais informações sobre como criar uma Interface do usuário no Interface Builder, consulte nosso [Introdução ao Interface Builder e Xcode](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) documentação.

<a name="Sizing_and_Positioning" />

### <a name="sizing-and-positioning"></a>Dimensionamento e posicionamento

Depois que foi incluído um controle na Interface do usuário, use o **editor de restrição de** para definir seu local e tamanho digitando os valores manualmente e controlar como o controle é posicionado automaticamente e dimensionado quando a janela do pai ou uma exibição é redimensionada:

[![](standard-controls-images/edit04.png "Definir as restrições")](standard-controls-images/edit04.png#lightbox)

Use o **I vermelhas** em torno da **Autoresizing** caixa para _pen drive_ um controle para um local especificado (x, y). Por exemplo: 

[![](standard-controls-images/edit05.png "Edição de uma restrição")](standard-controls-images/edit05.png#lightbox)

Especifica que o controle selecionado (na **exibição de hierarquia** & **Editor de Interface**) será paralisada até o local superior e direito da janela ou exibição conforme ele é redimensionado ou movido. 

Outros elementos do editor de propriedades como altura e largura de controle:

[![](standard-controls-images/edit06.png "Definir a altura")](standard-controls-images/edit06.png#lightbox)

Você também pode controlar o alinhamento dos elementos com restrições, usando o **Editor de alinhamento**:

[![](standard-controls-images/edit07.png "O Editor de alinhamento")](standard-controls-images/edit07.png#lightbox)

> [!IMPORTANT]
> Ao contrário do iOS onde (0,0) é o canto superior esquerdo de canto da tela, no macOS (0,0) é o canto inferior esquerdo. Isso ocorre porque o macOS usa um sistema de coordenadas de matemático com os valores numéricos, aumentando de valor para cima e para a direita. Você precisa levar isso em consideração ao colocar controles AppKit em uma Interface do usuário.

<a name="Setting_a_Custom_Class" />

### <a name="setting-a-custom-class"></a>Definindo uma classe personalizada

Há vezes quando trabalhar com controles AppKit que você precisará subclasse e controle existente e crie sua própria versão personalizada da classe. Por exemplo, definindo uma versão personalizada da lista de origem:

```csharp
using System;
using AppKit;
using Foundation;

namespace AppKit
{
    [Register("SourceListView")]
    public class SourceListView : NSOutlineView
    {
        #region Computed Properties
        public SourceListDataSource Data {
            get {return (SourceListDataSource)this.DataSource; }
        }
        #endregion

        #region Constructors
        public SourceListView ()
        {

        }

        public SourceListView (IntPtr handle) : base(handle)
        {

        }

        public SourceListView (NSCoder coder) : base(coder)
        {

        }

        public SourceListView (NSObjectFlag t) : base(t)
        {

        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

        }
        #endregion

        #region Public Methods
        public void Initialize() {

            // Initialize this instance
            this.DataSource = new SourceListDataSource (this);
            this.Delegate = new SourceListDelegate (this);

        }

        public void AddItem(SourceListItem item) {
            if (Data != null) {
                Data.Items.Add (item);
            }
        }
        #endregion

        #region Events
        public delegate void ItemSelectedDelegate(SourceListItem item);
        public event ItemSelectedDelegate ItemSelected;

        internal void RaiseItemSelected(SourceListItem item) {
            // Inform caller
            if (this.ItemSelected != null) {
                this.ItemSelected (item);
            }
        }
        #endregion
    }
}
```

Em que o `[Register("SourceListView")]` instrução expõe o `SourceListView` classe para Objective-C é isso que pode ser usada no construtor de Interface. Para obter mais informações, consulte o [classes expondo c# / métodos para Objective-C](~/mac/internals/how-it-works.md) seção o [recursos internos de xamarin. Mac](~/mac/internals/how-it-works.md) de documento, ele explica a `Register` e `Export` comandos usados a transmissão-up suas classes de c# a objetos de Objective-C e elementos de interface do usuário.

Com o código acima em vigor, você pode arrastar um controle AppKit, do tipo base que você está estendendo, para a superfície de design (no exemplo a seguir, uma **lista de origem**), alterne para o **Inspetor de identidade** e defina o **classe personalizada** como o nome que é exposto ao Objective-C (exemplo `SourceListView`):

[![](standard-controls-images/edit10.png "Definindo uma classe personalizada no Xcode")](standard-controls-images/edit10.png#lightbox)

<a name="Exposing_Outlets_and_Actions" />

### <a name="exposing-outlets-and-actions"></a>Expondo saídas e ações

Antes de um controle AppKit pode ser acessado no código c#, ele precisa ser exposto como um **tomada** ou e **ação**. Para fazer isso selecione o controle especificado na **hierarquia de Interface** ou o **Editor de Interface** e alterne para o **Assistente de exibição** (certifique-se de que você tenha o `.h`da sua janela selecionada para edição):

[![](standard-controls-images/edit11.png "Selecionando o arquivo correto para editar")](standard-controls-images/edit11.png#lightbox)

Controle e arraste do controle AppKit até dê `.h` arquivo para iniciar a criação de um **tomada** ou **ação**:

[![](standard-controls-images/edit12.png "Arrastando para criar uma ação ou tomada")](standard-controls-images/edit12.png#lightbox)

Selecione o tipo de exposição para criar e fornecer a **tomada** ou **ação** um **nome**: 

[![](standard-controls-images/edit13.png "Configurando a ação ou o")](standard-controls-images/edit13.png#lightbox)


Para obter mais informações sobre como trabalhar com **tomadas** e **ações**, consulte a [saídas e ações](~/mac/get-started/hello-mac.md#outlets-and-actions) seção do nosso [introdução para o Xcode e Interface Construtor](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) documentação.

<a name="Synchronizing_Changes_with_Xcode" />

### <a name="synchronizing-changes-with-xcode"></a>Sincronizando alterações com o Xcode

Quando você alterna para o Visual Studio para Mac do Xcode, quaisquer alterações feitas no Xcode serão sincronizadas automaticamente com o seu projeto xamarin. Mac.

Se você selecionar o `SplitViewController.designer.cs` no **Gerenciador de soluções** você poderá ver como seu **tomada** e **ação** foram conectadas em nosso código c#:

[![](standard-controls-images/sync01.png "Sincronizando alterações com Xcode")](standard-controls-images/sync01.png#lightbox)

Observe como a definição no `SplitViewController.designer.cs` arquivo:

```csharp
[Outlet]
AppKit.NSSplitViewItem LeftController { get; set; }

[Outlet]
AppKit.NSSplitViewItem RightController { get; set; }

[Outlet]
AppKit.NSSplitView SplitView { get; set; }
```

Alinhar com a definição no `MainWindow.h` arquivo no Xcode:

```csharp
@interface SplitViewController : NSSplitViewController {
    NSSplitViewItem *_LeftController;
    NSSplitViewItem *_RightController;
    NSSplitView *_SplitView;
}

@property (nonatomic, retain) IBOutlet NSSplitViewItem *LeftController;

@property (nonatomic, retain) IBOutlet NSSplitViewItem *RightController;

@property (nonatomic, retain) IBOutlet NSSplitView *SplitView;
```

Como você pode ver, o Visual Studio para Mac escuta de alterações para o `.h` do arquivo e sincroniza automaticamente essas alterações no respectivo `.designer.cs` arquivo expô-los ao seu aplicativo. Você pode perceber que também `SplitViewController.designer.cs` é uma classe parcial, para que o Visual Studio para Mac não precise modificar `SplitViewController.cs ` que substituiria as alterações feitas à classe.

Você normalmente nunca precisará abrir o `SplitViewController.designer.cs` por conta própria, ele foi apresentado aqui apenas para fins educacionais.

> [!IMPORTANT]
> Na maioria das situações, o Visual Studio para Mac será automaticamente as alterações feitas no Xcode e sincronizá-los ao seu projeto xamarin. Mac. Na improvável ocorrência de que a sincronização não aconteça automaticamente, retorne para o Xcode e depois novamente para o Visual Studio para Mac. Isso normalmente dará início a um ciclo de sincronização.

<a name="Working_with_Buttons" />

## <a name="working-with-buttons"></a>Trabalhando com botões

AppKit fornece vários tipos de botão que pode ser usado em seu Design de Interface do usuário. Para obter mais informações, consulte o [botões](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsButtons.html#//apple_ref/doc/uid/20000957-CH48-SW1) seção da Apple [diretrizes de Interface humana dos X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/buttons01.png "Um exemplo dos tipos diferentes de botão")](standard-controls-images/buttons01.png#lightbox)

Se um botão foi exposto por meio de um **tomada**, o código a seguir irá responder a ele que está sendo pressionado:

```csharp
ButtonOutlet.Activated += (sender, e) => {
        FeedbackLabel.StringValue = "Button Outlet Pressed";
};
```

Para botões que tenham sido apresentadas pelos **ações**, um `public partial` método será automaticamente criado para você com o nome que você escolheu no Xcode. Para responder à **ação**, conclua o método na classe parcial que o **ação** foi definido no. Por exemplo:

```csharp
partial void ButtonAction (Foundation.NSObject sender) {
    // Do something in response to the Action
    FeedbackLabel.StringValue = "Button Action Pressed";
}
```

Para botões que têm um estado (como **na** e **Off**), o estado pode ser verificado ou definido com o `State` propriedade com base no `NSCellStateValue` enum. Por exemplo:

```csharp
DisclosureButton.Activated += (sender, e) => {
    LorumIpsum.Hidden = (DisclosureButton.State == NSCellStateValue.On);
};
```

Onde `NSCellStateValue` pode ser:

- **Em** - o botão é pressionado ou o controle é selecionado (por exemplo, um check-in de uma caixa de seleção).
- **Desativar** - não é enviada por push o botão ou o controle não está selecionado.
- **Misto** -uma mistura de **nos** e **logoff** estados.

<a name="Mark-a-Button-as-Default-and-Set-Key-Equivalent" />

### <a name="mark-a-button-as-default-and-set-key-equivalent"></a>Marcar um botão como padrão e defina a chave equivalente

Para qualquer botão que você adicionou a um design de interface do usuário, você pode marcar esse botão como o _padrão_ botão será ativado quando o usuário pressiona o **Enter/retorno** teclas no teclado. No macOS, esse botão recebe uma cor de plano de fundo azul por padrão.

Para definir um botão como padrão, selecione-o no Interface Builder do Xcode. Em seguida, nos **Inspetor de atributo**, selecione o **chave equivalente** campo e pressione a **Enter/retorno** chave:

[![](standard-controls-images/buttons03.png "Editando o equivalente de chave")](standard-controls-images/buttons03.png#lightbox)

Da mesma forma, você pode atribuir qualquer sequência de chave que pode ser usada para ativar o botão usando o teclado em vez de mouse. Por exemplo, por pressionando as teclas de comando C na imagem acima.

Quando o aplicativo é executado e a janela com o botão é a chave e com foco, se o usuário pressionar o comando-C, a ação do botão será ativada (como-se o usuário clicou no botão).

<a name="Working_with_Checkboxes_and_Radio_Buttons" />

## <a name="working-with-checkboxes-and-radio-buttons"></a>Trabalhando com caixas de seleção e botões de opção

AppKit fornece vários tipos de caixas de seleção e grupos de botões de opção que pode ser usado em seu Design de Interface do usuário. Para obter mais informações, consulte o [botões](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsButtons.html#//apple_ref/doc/uid/20000957-CH48-SW1) seção da Apple [diretrizes de Interface humana dos X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/buttons02.png "Um exemplo dos tipos disponíveis de caixa de seleção")](standard-controls-images/buttons02.png#lightbox)


Caixas de seleção e botões de opção (expostos por meio **tomadas**) têm um estado (como **na** e **Off**), o estado pode ser verificado ou definido com o `State` propriedade com base no `NSCellStateValue` enum. Por exemplo:

```csharp
AdjustTime.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Adjust Time: {0}",AdjustTime.State == NSCellStateValue.On);
};
```

Onde `NSCellStateValue` pode ser:

- **Em** - o botão é pressionado ou o controle é selecionado (por exemplo, um check-in de uma caixa de seleção).
- **Desativar** - não é enviada por push o botão ou o controle não está selecionado.
- **Misto** -uma mistura de **nos** e **logoff** estados.

Para selecionar um botão em um grupo de botões de opção, expor o botão de opção para selecionar como uma **tomada** e defina seu `State` propriedade. Por exemplo:

```csharp
partial void SelectCar (Foundation.NSObject sender) {
    TransportationCar.State = NSCellStateValue.On;
    FeedbackLabel.StringValue = "Car Selected";
}
```

Para obter uma coleção de botões de opção para atuar como um grupo e tratar automaticamente o estado selecionado, crie um novo **ação** e anexe cada botão no grupo a ele:

![](standard-controls-images/buttons04.png "Criando uma nova ação")

Em seguida, atribua um único `Tag` para cada botão de opção na **Inspetor de atributo**:

![](standard-controls-images/buttons05.png "Editando uma marca de botão de rádio")

Salve suas alterações e retorne ao Visual Studio para Mac, adicione o código para lidar com o **ação** que todos os botões de opção são anexados ao:

```csharp
partial void NumberChanged(Foundation.NSObject sender)
{
    var check = sender as NSButton;
    Console.WriteLine("Changed to {0}", check.Tag);
}
```

Você pode usar o `Tag` propriedade para ver qual botão de rádio foi selecionado.

<a name="Working_with_Menu_Controls" />

## <a name="working-with-menu-controls"></a>Trabalhando com controles de Menu

AppKit fornece vários tipos de controles de Menu que podem ser usados em seu Design de Interface do usuário. Para obter mais informações, consulte o [controles de Menu](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlswithMenus.html#//apple_ref/doc/uid/20000957-CH100-SW1) seção da Apple [diretrizes de Interface humana dos X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/menu01.png "Controles de menu de exemplo")](standard-controls-images/menu01.png#lightbox)

<a name="Providing-Menu-Control-Data" />

### <a name="providing-menu-control-data"></a>Fornece dados de controle de Menu

Os controles de Menu disponíveis para macOS pode ser definidos para preencher a lista suspensa de uma lista interna (que pode ser predefinida no construtor de Interface ou preenchida por meio de código) ou fornecendo sua própria fonte de dados personalizados e externos.

<a name="Working-with-Internal-Data" />

#### <a name="working-with-internal-data"></a>Trabalhando com dados internos

Além de definir os itens no Interface Builder, controles de Menu (como `NSComboBox`), forneça um conjunto completo de métodos que permitem que você adicionar, editar ou excluir os itens da lista interna que eles mantêm:

- `Add` -Adiciona um novo item ao final da lista.
- `GetItem` -Retorna o item no índice especificado.
- `Insert` -Insere um novo item na lista no local especificado.
- `IndexOf` -Retorna o índice do item fornecido.
- `Remove` -Remove o item especificado da lista.
- `RemoveAll` -Remove todos os itens da lista.
- `RemoveAt` -Remove o item no índice especificado.
- `Count` -Retorna o número de itens na lista.

> [!IMPORTANT]
> Se você estiver usando uma fonte de dados externo (`UsesDataSource = true`), chamar qualquer um dos métodos acima lançará uma exceção.

<a name="Working-with-an-External-Data-Source" />

#### <a name="working-with-an-external-data-source"></a>Trabalhando com uma fonte de dados externa

Em vez de usar os dados internos interno para fornecer as linhas para o seu controle de Menu, opcionalmente, você pode usar uma fonte de dados externa e fornecer seu próprio repositório de backup para os itens (por exemplo, um banco de dados SQLite).

Para trabalhar com uma fonte de dados externa, você criará uma instância da fonte de dados do controle do Menu (`NSComboBoxDataSource` por exemplo) e substituir os vários métodos para fornecer os dados necessários:

- `ItemCount` -Retorna o número de itens na lista.
- `ObjectValueForItem` -Retorna o valor do item para um determinado índice.
- `IndexOfItem` -Retorna o índice para o valor do item determinado.
- `CompletedString` -Retorna o primeiro valor correspondente do item para o valor do item parcialmente tipado. Esse método é chamado apenas se o preenchimento automático estiver habilitado (`Completes = true`).

Consulte a [bancos de dados e caixas de combinação](~/mac/app-fundamentals/databases.md#Databases-and-ComboBoxes) seção o [trabalhando com bancos de dados](~/mac/app-fundamentals/databases.md) documento para obter mais detalhes.

<a name="Adjusting-the-Lists-Appearance" />

### <a name="adjusting-the-lists-appearance"></a>Ajustando a aparência da lista

Os métodos a seguir estão disponíveis para ajustar a aparência do controle de Menu:

- `HasVerticalScroller` -Se `true`, o controle exibirá uma barra de rolagem vertical. 
- `VisibleItems` -Ajuste o número de itens exibidos quando o controle é aberto. O valor padrão é cinco (5).
- `IntercellSpacing` -Ajuste a quantidade de espaço em torno de um determinado item, fornecendo uma `NSSize` onde o `Width` Especifica as margens esquerdas e direita e o `Height` Especifica o espaço antes e depois de um item.
- `ItemHeight` -Especifica a altura de cada item na lista.

Para tipos de lista suspensa de `NSPopupButtons`, o primeiro Item de Menu fornece o título para o controle. Por exemplo: 

[![](standard-controls-images/menu02.png "Um controle de menu de exemplo")](standard-controls-images/menu02.png#lightbox)

Para alterar o título, expor este item como uma **tomada** e usar código semelhante ao seguinte:

```csharp
DropDownSelected.Title = "Item 1";
```

<a name="Manipulating-the-Selected-Items" />

### <a name="manipulating-the-selected-items"></a>Manipulando os itens selecionados

Os métodos e propriedades a seguir permitem que você manipule os itens selecionados na lista de controle de Menu:

- `SelectItem` – Seleciona o item no índice especificado.
- `Select` -Selecione o valor do item determinado.
- `DeselectItem` -Desmarca o item no índice especificado.
- `SelectedIndex` -Retorna o índice do item atualmente selecionado.
- `SelectedValue` -Retorna o valor do item atualmente selecionado.

Use o `ScrollItemAtIndexToTop` para apresentar o item no índice especificado na parte superior da lista e o `ScrollItemAtIndexToVisible` para rolar a lista até que o item no índice especificado está visível.

<a name="Responding to Events" />

### <a name="responding-to-events"></a>Respondendo a eventos

Controles de menu fornecem os seguintes eventos para responder à interação do usuário:

- `SelectionChanged` -É chamado quando o usuário tiver selecionado um valor na lista.
- `SelectionIsChanging` -É chamado antes do novo item selecionado pelo usuário torna-se a seleção ativa.
- `WillPopup` -É chamado antes que a lista suspensa de itens é exibida.
- `WillDismiss` -É chamado antes que a lista suspensa de itens seja fechada.

Para `NSComboBox` controles, eles incluem todos os eventos mesmos como o `NSTextField`, como o `Changed` evento que é chamado sempre que o usuário edita o valor do texto na caixa de combinação.

Opcionalmente, você pode responder a um itens de Menu de dados internos definidos no construtor de Interface que está sendo selecionada, anexando o item a ser um **ação** e usar código semelhante ao seguinte para responder às **ação** sendo disparado pelo usuário:

```csharp
partial void ItemOne (Foundation.NSObject sender) {
    DropDownSelected.Title = "Item 1";
    FeedbackLabel.StringValue = "Item One Selected";
}
```

Para obter mais informações sobre como trabalhar com Menus e controles de Menu, consulte nosso [Menus](~/mac/user-interface/menu.md) e [botão pop-up e listas suspensas](~/mac/user-interface/menu.md) documentação.

<a name="Working_with_Selection_Controls" />

## <a name="working-with-selection-controls"></a>Trabalhando com controles de seleção

AppKit fornece vários tipos de controles de seleção que pode ser usado em seu Design de Interface do usuário. Para obter mais informações, consulte o [controles de seleção](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsSelection.html#//apple_ref/doc/uid/20000957-CH49-SW1) seção da Apple [diretrizes de Interface humana dos X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/select01.png "Controles de seleção de exemplo")](standard-controls-images/select01.png#lightbox)

Há duas maneiras para controlar quando um controle de seleção tem a interação do usuário, expondo-o como uma **ação**. Por exemplo:

```csharp
partial void SegmentButtonPressed (Foundation.NSObject sender) {
    FeedbackLabel.StringValue = string.Format("Button {0} Pressed",SegmentButtons.SelectedSegment);
}
```

Ou anexando um **delegado** para o `Activated` eventos. Por exemplo:

```csharp
TickedSlider.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Stepper Value: {0:###}",TickedSlider.IntValue);
};
```

Para definir ou ler o valor de um controle de seleção, use o `IntValue` propriedade. Por exemplo:

```csharp
FeedbackLabel.StringValue = string.Format("Stepper Value: {0:###}",TickedSlider.IntValue);
```

Os controles de especialidade (como também cor e imagem bem) têm propriedades específicas para seus tipos de valor. Por exemplo:

```csharp
CollorWell.Color = NSColor.Red;
ImageWell.Image = NSImage.ImageNamed ("tag.png");

```

O `NSDatePicker` tem as seguintes propriedades para trabalhar diretamente com a data e hora:

- **DateValue** -o valor de data e hora atual como um `NSDate`.
- **Local** -o local do usuário como um `NSLocal`.
- **TimeInterval** -o valor de tempo como um `Double`.
- **Fuso horário** -fuso horário do usuário uma `NSTimeZone`.

<a name="Working_with_Indicator_Controls" />

## <a name="working-with-indicator-controls"></a>Trabalhando com controles de indicador

AppKit fornece vários tipos de controles de indicador que pode ser usado em seu Design de Interface do usuário. Para obter mais informações, consulte o [controles de indicador](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsIndicators.html#//apple_ref/doc/uid/20000957-CH50-SW1) seção da Apple [diretrizes de Interface humana dos X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/level01.png "Controles de indicador de exemplo")](standard-controls-images/level01.png#lightbox)

Há duas maneiras para controlar quando um controle de indicador tem interação do usuário, qualquer um, expondo-o como uma **ação** ou um **tomada** e anexando um **delegado** para o `Activated`eventos. Por exemplo:

```csharp
LevelIndicator.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Level: {0:###}",LevelIndicator.DoubleValue);
};
```

Para ler ou definir o valor do indicador de controle, use o `DoubleValue` propriedade. Por exemplo:

```csharp
FeedbackLabel.StringValue = string.Format("Rating: {0:###}",Rating.DoubleValue);
```

O indeterminado e indicadores de progresso assíncrono devem ser animada quando exibidos. Use o `StartAnimation` método para iniciar a animação quando eles são exibidos. Por exemplo:

```csharp
Indeterminate.StartAnimation (this);
AsyncProgress.StartAnimation (this);
```

Chamar o `StopAnimation` método interromperá a animação.

<a name="Working_with_Text_Controls" />

## <a name="working-with-text-controls"></a>Trabalhando com controles de texto

AppKit fornece vários tipos de controles de texto que pode ser usado em seu Design de Interface do usuário. Para obter mais informações, consulte o [controles de texto](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsText.html#//apple_ref/doc/uid/20000957-CH51-SW1) seção da Apple [diretrizes de Interface humana dos X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/text01.png "Controles de texto de exemplo")](standard-controls-images/text01.png#lightbox)

Para campos de texto (`NSTextField`), os eventos a seguir podem ser usados para controlar a interação do usuário:

- **Alterado** -é disparado sempre que o usuário altera o valor do campo. Por exemplo, cada caractere digitado.
- **EditingBegan** -é disparado quando o usuário seleciona o campo para edição.
- **EditingEnded** - quando o usuário pressiona a tecla Enter no campo ou sai do campo.

Use o `StringValue` propriedade para ler ou definir o valor do campo. Por exemplo:

```csharp
FeedbackLabel.StringValue = string.Format("User ID: {0}",UserField.StringValue);
```

Para os campos que exibem ou edite os valores numéricos, você pode usar o `IntValue` propriedade. Por exemplo:

```csharp
FeedbackLabel.StringValue = string.Format("Number: {0}",NumberField.IntValue);
```

Um `NSTextView` fornece um texto em destaque completo de edição e exibir a área com formatação interna. Como uma `NSTextField`, use o `StringValue` propriedade para ler ou definir o valor da área.

Para obter um exemplo de um exemplo complexo de como trabalhar com modos de exibição de texto em um aplicativo xamarin. Mac, consulte a [aplicativo de exemplo SourceWriter](https://developer.xamarin.com/samples/mac/SourceWriter/). SourceWriter é um editor de código-fonte simples que dá suporte para a conclusão de código e realce de sintaxe simples.

O código do SourceWriter foi totalmente comentado e, quando disponível, foram fornecidos links de tecnologias-chave ou métodos para informações relevantes na Documentação de Guias do Xamarin.Mac.

<a name="Working_with_Content_Views" />

## <a name="working-with-content-views"></a>Trabalhando com visualizações de conteúdo

AppKit fornece vários tipos de modos de exibição de conteúdo que pode ser usado em seu Design de Interface do usuário. Para obter mais informações, consulte o [modos de exibição de conteúdo](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsView.html#//apple_ref/doc/uid/20000957-CH52-SW1) seção da Apple [diretrizes de Interface humana dos X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/).

[![](standard-controls-images/content01.png "Um modo de exibição de conteúdo de exemplo")](standard-controls-images/content01.png#lightbox)

<a name="Popovers" />

### <a name="popovers"></a>Popovers

Um pop-over é um elemento de interface do usuário transitório que fornece a funcionalidade que está diretamente relacionada a um determinado um controle ou uma área na tela. Um pop-over flutua acima da janela que contém a área que está relacionado ao ou um controle e sua borda inclui uma seta para indicar o ponto do qual tenham surgido.

Para criar um pop-over, faça o seguinte:

1. Abra o `.storyboard` arquivo da janela que você deseja adicionar um pop-over para clicando duas vezes no **Gerenciador de soluções**
2. Arraste uma **exibir controlador** da **Inspetor de biblioteca** até o **Editor de Interface**: 

    [![](standard-controls-images/content02.png "Selecionar um controlador de exibição da biblioteca")](standard-controls-images/content02.png#lightbox)
4. Definir o tamanho e o layout do **modo de exibição personalizado**: 

    [![](standard-controls-images/content04.png "Edição do layout")](standard-controls-images/content04.png#lightbox)
5. CTRL + clique e arraste a origem do pop-up para o **controlador de exibição**: 

    [![](standard-controls-images/content05.png "Arrastando para criar um segue")](standard-controls-images/content05.png#lightbox)
6. Selecione **pop-over** no menu pop-up: 

    [![](standard-controls-images/content06.png "Definindo o tipo de segue")](standard-controls-images/content06.png#lightbox)
7. Salve suas alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

<a name="Tab_Views" />

### <a name="tab-views"></a>Modos de exibição da guia

Modos de exibição da guia consiste em uma lista de guias (que se parece semelhante a um controle segmentado) combinado com um conjunto de exibições que são chamados _painéis_. Quando o usuário seleciona uma nova guia, o painel que está anexado a ele será exibido. Cada painel contém seu próprio conjunto de controles.

Ao trabalhar com uma exibição de guia no Interface Builder do Xcode, use o **Inspetor de atributo** para definir o número de guias:

[![](standard-controls-images/content08.png "O número de guias de edição")](standard-controls-images/content08.png#lightbox)

Selecione cada guia na **hierarquia de Interface** para definir seu **título** e adicione os elementos de interface do usuário para seu **painel**:

[![](standard-controls-images/content09.png "Editando as guias no Xcode")](standard-controls-images/content09.png#lightbox)

<a name="Data_Binding_AppKit_Controls" />

## <a name="data-binding-appkit-controls"></a>Controles de AppKit de ligação de dados

Usando técnicas de codificação de chave-valor e associação de dados em seu aplicativo xamarin. Mac, você pode diminuir consideravelmente a quantidade de código que você precisa para escrever e manter para popular e trabalhar com elementos de interface do usuário. Você também tem a vantagem de dissociar ainda mais seus dados de backup (_modelo de dados_) da sua frente terminar a Interface do usuário (_Model-View-Controller_), gerando mais fáceis de manter aplicativos mais flexíveis Design.

Codificação de chave-valor (KVC) é um mecanismo para acessar propriedades de um objeto indiretamente, usando métodos de acessador ou chaves (especialmente formatadas cadeias de caracteres) para identificar as propriedades em vez de acessá-los por meio de variáveis de instância (`get/set`). Com a implementação de acessadores em conformidade com a codificação de chave-valor em seu aplicativo xamarin. Mac, você obtém acesso a outros recursos do macOS como observando de chave-valor (KVO), associação de dados, dados principais, associações de Cocoa e ScriptableType.

Para obter mais informações, consulte o [vinculação de dados simples](~/mac/app-fundamentals/databinding.md#Simple_Data_Binding) seção do nosso [vinculação de dados e a codificação de chave-valor](~/mac/app-fundamentals/databinding.md) documentação.


<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo apresentou uma visão detalhada de como trabalhar com controles AppKit padrão como botões, rótulos, campos de texto, caixas de seleção e controles segmentados em um aplicativo xamarin. Mac. Ele abordou adicioná-los para um Design de Interface do usuário no Interface Builder do Xcode, expô-las ao código por meio de saídas e ações e trabalhar com controles AppKit em código c#.

## <a name="related-links"></a>Links relacionados

- [MacControls (amostra)](https://developer.xamarin.com/samples/mac/MacControls/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Windows](~/mac/user-interface/window.md)
- [Associação de dados e a codificação de chave-valor](~/mac/app-fundamentals/databinding.md)
- [Diretrizes de interface humana do OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Sobre controles e modos de exibição](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1)
