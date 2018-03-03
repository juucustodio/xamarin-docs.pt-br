---
title: "Controles padrão"
description: "Este artigo aborda o trabalho com os controles AppKit padrão como botões, rótulos, campos de texto, caixas de seleção e segmentada controles em um aplicativo Xamarin.Mac. Ele descreve adicioná-los a uma interface com o construtor de Interface e interagir com eles no código."
ms.topic: article
ms.prod: xamarin
ms.assetid: d2593883-d255-431f-9781-75f04d8cecea
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: e6df7f9308285b87ff0f42b73c8404b375cbb0de
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="standard-controls"></a>Controles padrão

_Este artigo aborda o trabalho com os controles AppKit padrão como botões, rótulos, campos de texto, caixas de seleção e segmentada controles em um aplicativo Xamarin.Mac. Ele descreve adicioná-los a uma interface com o construtor de Interface e interagir com eles no código._

Ao trabalhar com c# e .NET em um aplicativo de Xamarin.Mac, você tem acesso à mesma AppKit controla que um desenvolvedor trabalhando em *Objective-C* e *Xcode* does. Como Xamarin.Mac se integra diretamente com o Xcode, você pode usar do Xcode _Interface construtor_ para criar e manter seus controles Appkit (ou, opcionalmente, criá-los diretamente no código do c#).

Controles de AppKit são os elementos de interface do usuário que são usados para criar a Interface do usuário do seu aplicativo Xamarin.Mac. Eles consistem em elementos como botões, rótulos, campos de texto, caixas de seleção e controles segmentados e causam ações instantâneas ou resultados visíveis quando um usuário manipula.

[ ![](standard-controls-images/intro01.png "A tela principal do aplicativo de exemplo")](standard-controls-images/intro01.png)

Neste artigo, vamos abordar os fundamentos de trabalhar com controles de AppKit em um aplicativo Xamarin.Mac. É altamente recomendável que você leia o [Hello, Mac](~/mac/get-started/hello-mac.md) artigo primeiro, especificamente o [Introdução ao construtor da Interface e Xcode](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) e [tomadas e ações](~/mac/get-started/hello-mac.md#Outlets_and_Actions) seções, como ele aborda os principais conceitos e técnicas que será usado neste artigo.

Talvez você queira dar uma olhada a [classes expondo c# / métodos para Objective-C](~/mac/internals/how-it-works.md) seção o [Xamarin.Mac internos](~/mac/internals/how-it-works.md) de documento, ele explica o `Register` e `Export` comandos usado com a transmissão suas classes c# objetos Objective-C e elementos de interface do usuário.

<a name="Introduction_to_Controls_and_Views" />

## <a name="introduction-to-controls-and-views"></a>Introdução aos controles e modos de exibição

macOS (anteriormente conhecido como o Mac OS X) fornece um conjunto padrão de controles de Interface do usuário por meio da estrutura de AppKit. Eles consistem em elementos como botões, rótulos, campos de texto, caixas de seleção e controles segmentados e causam ações instantâneas ou resultados visíveis quando um usuário manipula.

Todos os controles AppKit tem uma aparência padrão, interna, que poderá ser adequada para a maioria dos usos, alguns especificam uma aparência alternativa para uso em uma área de quadro de janela ou em um _vibração efeito_ contexto, tal como em uma área de barra lateral ou em um Widget de centro de notificação.

Apple sugerem as diretrizes a seguir ao trabalhar com controles de AppKit:

- Evite a combinação de tamanhos de controle na mesma exibição.
- Em geral, evite redimensionar controles verticalmente.
- Use a fonte do sistema e o tamanho do texto apropriado dentro de um controle.
- Use o espaçamento correto entre os controles.

Para obter mais informações, consulte pleas o [sobre controles e modos de exibição](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1) seção da Apple [diretrizes de Interface humana do sistema operacional X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/).

<a name="Using_Controls_in_a_Window_Frame" />

### <a name="using-controls-in-a-window-frame"></a>Usando controles em um quadro de janela

Há um subconjunto dos controles AppKit que incluem um estilo de exibição que permitirá a ser incluída no área de quadro da janela. Para obter um exemplo, consulte a barra de ferramentas do aplicativo de email:

[ ![](standard-controls-images/mailapp.png "Um quadro de janela de Mac")](standard-controls-images/mailapp.png)

- **Botão de textura de ida e volta** - um `NSButton` com um estilo de `NSTexturedRoundedBezelStyle`.
- **Textura arredondado segmentado controle** - um `NSSegmentedControl` com um estilo de `NSSegmentStyleTexturedRounded`.
- **Textura arredondado segmentado controle** - um `NSSegmentedControl` com um estilo de `NSSegmentStyleSeparated`.
- **Menu pop-up textura de ida e volta** - um `NSPopUpButton` com um estilo de `NSTexturedRoundedBezelStyle`.
- **Menu de lista suspensa de textura de ida e volta** - um `NSPopUpButton` com um estilo de `NSTexturedRoundedBezelStyle`.
- **Barra de pesquisa** - um `NSSearchField`.

Apple sugerem as diretrizes a seguir ao trabalhar com controles de AppKit em um quadro de janela:

- Não use os estilos de controle específicos do quadro de janela no corpo da janela.
- Não use os controles de janela corpo ou estilos no quadro de janela.

Para obter mais informações, consulte pleas o [sobre controles e modos de exibição](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1) seção da Apple [diretrizes de Interface humana do sistema operacional X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/).

<a name="Creating_a_User_Interface_in_Interface_Builder" />

## <a name="creating-a-user-interface-in-interface-builder"></a>Criando uma Interface do usuário no construtor de Interface

Quando você cria um novo aplicativo de Xamarin.Mac Cocoa, obtém uma janela em branco, padrão por padrão. Esse windows é definido em um `.storyboard` arquivo incluído automaticamente no projeto. Para editar o design do windows, o **Gerenciador de soluções**, clique duas vezes o `Main.storyboard` arquivo:

[ ![](standard-controls-images/edit01.png "Selecionando o Storyboard principal no Gerenciador de soluções")](standard-controls-images/edit01.png)

Isso abrirá o design de janela no construtor de Interface do Xcode:

[ ![](standard-controls-images/edit02.png "Editando o storyboard no Xcode")](standard-controls-images/edit02.png)

Para criar sua Interface de usuário, você poderá arrastar elementos de interface do usuário (AppKit controles) da **biblioteca Inspetor** para o **Editor de Interface** no construtor de Interface. No exemplo a seguir, uma **modo de divisão Vertical** controle foi droga do **biblioteca Inspetor** e colocados na janela do **Editor de Interface**:

[ ![](standard-controls-images/edit03.png "Selecionar um modo de divisão da biblioteca")](standard-controls-images/edit03.png)

Para obter mais informações sobre como criar uma Interface do usuário no construtor de Interface, consulte nosso [Introdução ao construtor da Interface e Xcode](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) documentação.

<a name="Sizing_and_Positioning" />

### <a name="sizing-and-positioning"></a>Redimensionar e posicionar

Depois de um controle foi incluído na Interface do usuário, use o **editor de restrição de** para definir seu local e tamanho inserindo valores manualmente e controlar como o controle é posicionado automaticamente e quando o tamanho da janela do pai ou exibição é redimensionado:

[ ![](standard-controls-images/edit04.png "Definir as restrições")](standard-controls-images/edit04.png)

Use o **vermelhos I-emissões** em torno do **Autoresizing** caixa para _cartão_ um controle em um local específico (x, y). Por exemplo: 

[ ![](standard-controls-images/edit05.png "Edição de uma restrição")](standard-controls-images/edit05.png)

Especifica que o controle selecionado (no **exibição de hierarquia** & **Editor de Interface**) será presa no local superior e direita da janela ou da exibição conforme ele é redimensionado ou movido. 

Outros elementos do editor de propriedades, como a altura e largura de controle:

[ ![](standard-controls-images/edit06.png "Definir a altura")](standard-controls-images/edit06.png)

Você também pode controlar o alinhamento dos elementos com restrições usando o **alinhamento Editor**:

[ ![](standard-controls-images/edit07.png "O Editor de alinhamento")](standard-controls-images/edit07.png)

> [!IMPORTANT]
> Ao contrário do iOS onde (0,0) é superior canto esquerdo da tela, em macOS (0,0) é o canto inferior esquerdo. Isso ocorre porque macOS usa um sistema de coordenadas de matemático com os valores de número para cima e para a direita o aumento no valor. Você precisa levar isso em consideração ao colocar controles AppKit em uma Interface de usuário.

<a name="Setting_a_Custom_Class" />

### <a name="setting-a-custom-class"></a>Definir uma classe personalizada

Há ocasiões ao trabalhar com controles de AppKit que você precisará subclasse e controle existente e crie a sua própria versão personalizada dessa classe. Por exemplo, definindo uma versão personalizada da lista de origem:

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

Onde o `[Register("SourceListView")]` instrução expõe o `SourceListView` classe para Objective-C é isso que pode ser usado no construtor de Interface. Para obter mais informações, consulte o [classes expondo c# / métodos para Objective-C](~/mac/internals/how-it-works.md) seção o [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) de documento, ele explica o `Register` e `Export` comandos usados com a transmissão suas classes c# objetos Objective-C e elementos de interface do usuário.

Com o código acima em vigor, você pode arrastar um controle AppKit, do tipo base que você estiver estendendo, para a superfície de design (no exemplo a seguir, uma **lista de origem**), alterne para o **Inspetor de identidade** e defina o **classe personalizada** para o nome exposto para Objective-C (exemplo `SourceListView`):

[ ![](standard-controls-images/edit10.png "Definir uma classe personalizada no Xcode")](standard-controls-images/edit10.png)

<a name="Exposing_Outlets_and_Actions" />

### <a name="exposing-outlets-and-actions"></a>Expondo tomadas e ações

Antes de um controle AppKit podem ser acessado no código c#, ele precisa ser exposto como um **tomada** ou e **ação**. Para fazer isso selecione o controle fornecido no **hierarquia Interface** ou **Editor de Interface** e alterne para o **Assistente de exibição** (certifique-se de que você tenha o `.h`da janela do selecionado para edição):

[ ![](standard-controls-images/edit11.png "Selecionar o arquivo correto para editar")](standard-controls-images/edit11.png)

Arraste de controle do controle AppKit para dê `.h` arquivo para começar a criar um **tomada** ou **ação**:

[ ![](standard-controls-images/edit12.png "Arrastar para criar uma ação tomada")](standard-controls-images/edit12.png)

Selecione o tipo de exposição a criar e fornecer o **tomada** ou **ação** um **nome**: 

[ ![](standard-controls-images/edit13.png "Configurando a ação ou o")](standard-controls-images/edit13.png)


Para obter mais informações sobre como trabalhar com **tomadas** e **ações**, consulte o [tomadas e ações](~/mac/get-started/hello-mac.md#Outlets_and_Actions) seção do nosso [Introdução à Interface e Xcode Construtor](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) documentação.

<a name="Synchronizing_Changes_with_Xcode" />

### <a name="synchronizing-changes-with-xcode"></a>Sincronizando alterações com o Xcode

Quando você alternar para o Visual Studio para Mac do Xcode, as alterações feitas no Xcode automaticamente serão sincronizadas com o seu projeto Xamarin.Mac.

Se você selecionar o `SplitViewController.designer.cs` no **Solution Explorer** você poderá ver como seu **tomada** e **ação** conexão em nosso código do c#:

[ ![](standard-controls-images/sync01.png "Sincronizar as alterações com o Xcode")](standard-controls-images/sync01.png)

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

Como você pode ver, o Visual Studio para Mac escuta para que as alterações a `.h` de arquivo e, em seguida, sincroniza automaticamente as alterações do respectivo `.designer.cs` arquivo expô-los ao seu aplicativo. Você também pode perceber que `SplitViewController.designer.cs` é uma classe parcial, para que o Visual Studio para Mac não precisa modificar `SplitViewController.cs ` que substituiria as alterações feitas à classe.

Normalmente nunca será necessário abrir o `SplitViewController.designer.cs` por conta própria, ele foi apresentado aqui apenas para fins educativos.

> [!IMPORTANT]
> Na maioria das situações, o Visual Studio para Mac automaticamente ver todas as alterações feitas no Xcode e sincronizá-los ao seu projeto Xamarin.Mac. Na improvável ocorrência de que a sincronização não aconteça automaticamente, retorne para o Xcode e depois novamente para o Visual Studio para Mac. Isso normalmente dará início a um ciclo de sincronização.

<a name="Working_with_Buttons" />

## <a name="working-with-buttons"></a>Trabalhando com botões

AppKit fornece vários tipos de botão que pode ser usado em seu Design de Interface do usuário. Para obter mais informações, consulte o [botões](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsButtons.html#//apple_ref/doc/uid/20000957-CH48-SW1) seção da Apple [diretrizes de Interface humana do sistema operacional X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[ ![](standard-controls-images/buttons01.png "Um exemplo dos tipos diferentes de botão")](standard-controls-images/buttons01.png)

Se um botão foi exposto por meio de um **tomada**, o código a seguir responde a ele sendo pressionado:

```csharp
ButtonOutlet.Activated += (sender, e) => {
        FeedbackLabel.StringValue = "Button Outlet Pressed";
};
```

Para os botões que foram expostos por meio de **ações**, um `public partial` método será automaticamente criado para você com o nome que você escolheu no Xcode. Para responder ao **ação**, conclua o método parcial na classe que o **ação** foi definido no. Por exemplo:

```csharp
partial void ButtonAction (Foundation.NSObject sender) {
    // Do something in response to the Action
    FeedbackLabel.StringValue = "Button Action Pressed";
}
```

Para os botões que têm um estado (como **em** e **Off**), o estado pode ser verificado ou definido com o `State` propriedade contra o `NSCellStateValue` enum. Por exemplo:

```csharp
DisclosureButton.Activated += (sender, e) => {
    LorumIpsum.Hidden = (DisclosureButton.State == NSCellStateValue.On);
};
```

Onde `NSCellStateValue` pode ser:

- **Em** - o botão está pressionado ou se o controle está selecionado (como uma seleção em uma caixa de seleção).
- **Desativar** - o botão não está pressionado ou se o controle não está selecionado.
- **Misto** -uma mistura de **na** e **Off** estados.

<a name="Mark-a-Button-as-Default-and-Set-Key-Equivalent" />

### <a name="mark-a-button-as-default-and-set-key-equivalent"></a>Marcar um botão como padrão e defina a chave equivalente

Para qualquer botão que você adicionou a um design de interface do usuário, você pode marcar esse botão como o _padrão_ botão será ativado quando o usuário pressiona o **Return/Enter** chave no teclado. Em macOS, este botão recebe uma cor de plano de fundo azul por padrão.

Para definir um botão como padrão, selecione-o no construtor de Interface do Xcode. Em seguida, no **Inspetor de atributo**, selecione o **chave equivalente** campo e pressione a **Return/Enter** chave:

[ ![](standard-controls-images/buttons03.png "Editando o equivalente de chave")](standard-controls-images/buttons03.png)

Igualmente, você pode atribuir qualquer sequência de chave que pode ser usada para ativar o botão usando o teclado, em vez do mouse. Por exemplo, por pressionando as teclas de comando-C na imagem acima.

Quando o aplicativo é executado e a janela com o botão é de chave e foco, se o usuário pressionar o comando-C, a ação do botão será ativada (como-se o usuário clicou no botão).

<a name="Working_with_Checkboxes_and_Radio_Buttons" />

## <a name="working-with-checkboxes-and-radio-buttons"></a>Trabalhando com caixas de seleção e botões de opção

AppKit fornece vários tipos de caixas de seleção e grupos de botões de opção que pode ser usado em seu Design de Interface do usuário. Para obter mais informações, consulte o [botões](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsButtons.html#//apple_ref/doc/uid/20000957-CH48-SW1) seção da Apple [diretrizes de Interface humana do sistema operacional X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[ ![](standard-controls-images/buttons02.png "Um exemplo dos tipos de caixa de seleção disponíveis")](standard-controls-images/buttons02.png)


Caixas de seleção e botões de opção (expostos por meio de **tomadas**) têm um estado (como **em** e **Off**), o estado pode ser verificado ou definido com o `State` propriedade com o `NSCellStateValue` enum. Por exemplo:

```csharp
AdjustTime.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Adjust Time: {0}",AdjustTime.State == NSCellStateValue.On);
};
```

Onde `NSCellStateValue` pode ser:

- **Em** - o botão está pressionado ou se o controle está selecionado (como uma seleção em uma caixa de seleção).
- **Desativar** - o botão não está pressionado ou se o controle não está selecionado.
- **Misto** -uma mistura de **na** e **Off** estados.

Para selecionar um botão em um grupo de botões de opção, expor o botão de opção para selecionar como um **tomada** e defina seu `State` propriedade. Por exemplo:

```csharp
partial void SelectCar (Foundation.NSObject sender) {
    TransportationCar.State = NSCellStateValue.On;
    FeedbackLabel.StringValue = "Car Selected";
}
```

Para obter uma coleção de botões de opção para atuar como um grupo e tratar automaticamente o estado selecionado, crie um novo **ação** e anexar cada botão no grupo a ela:

![](standard-controls-images/buttons04.png "Criando uma nova ação")

Em seguida, atribuir uma única `Tag` para cada botão de opção no **Inspetor de atributo**:

![](standard-controls-images/buttons05.png "Edição de uma marca de botão de opção")

Salve suas alterações e retornar ao Visual Studio para Mac, adicione o código para manipular o **ação** que todos os botões de opção são anexados ao:

```csharp
partial void NumberChanged(Foundation.NSObject sender)
{
    var check = sender as NSButton;
    Console.WriteLine("Changed to {0}", check.Tag);
}
```

Você pode usar o `Tag` propriedade para ver qual botão de opção foi selecionada.

<a name="Working_with_Menu_Controls" />

## <a name="working-with-menu-controls"></a>Trabalhando com controles de Menu

AppKit fornece vários tipos de controles de Menu que podem ser usados em seu Design de Interface do usuário. Para obter mais informações, consulte o [controles de Menu](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlswithMenus.html#//apple_ref/doc/uid/20000957-CH100-SW1) seção da Apple [diretrizes de Interface humana do sistema operacional X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[ ![](standard-controls-images/menu01.png "Controles de menu de exemplo")](standard-controls-images/menu01.png)

<a name="Providing-Menu-Control-Data" />

### <a name="providing-menu-control-data"></a>Fornece dados de controle de Menu

Os controles de Menu disponíveis para macOS pode ser definidos para preencher a lista suspensa de uma lista interna (que pode ser predefinida no construtor de Interface ou preenchida por meio de código) ou fornecendo sua própria fonte de dados personalizada, externa.

<a name="Working-with-Internal-Data" />

#### <a name="working-with-internal-data"></a>Trabalhando com dados internos

Além de definir os itens no construtor de Interface, controles de Menu (como `NSComboBox`), forneça um conjunto completo de métodos que permitem adicionar, editar ou excluir os itens da lista interna que mantêm:

- `Add` -Adiciona um novo item ao final da lista.
- `GetItem` -Retorna o item no índice especificado.
- `Insert` -Insere um novo item na lista no local especificado.
- `IndexOf` -Retorna o índice do item fornecido.
- `Remove` -Remove o item especificado da lista.
- `RemoveAll` -Remove todos os itens da lista.
- `RemoveAt` -Remove o item no índice especificado.
- `Count` -Retorna o número de itens na lista.

> [!IMPORTANT]
> Se você estiver usando uma fonte de dados externa (`UsesDataSource = true`), chamar qualquer um dos métodos acima lançará uma exceção.

<a name="Working-with-an-External-Data-Source" />

#### <a name="working-with-an-external-data-source"></a>Trabalhando com uma fonte de dados externa

Em vez de usar os dados internos interno para fornecer as linhas para o controle de Menu, opcionalmente, você pode usar uma fonte de dados externa e fornecer seu próprio repositório de backup para os itens (como um banco de dados SQLite).

Para trabalhar com uma fonte de dados externa, você criará uma instância do controle do Menu fonte de dados (`NSComboBoxDataSource` por exemplo) e vários métodos para fornecer os dados necessários de substituição:

- `ItemCount` -Retorna o número de itens na lista.
- `ObjectValueForItem` -Retorna o valor do item de um determinado índice.
- `IndexOfItem` -Retorna o índice para o valor do item oferecem.
- `CompletedString` -Retorna o primeiro valor correspondente do item para o valor do item parcialmente digitada. Este método é chamado somente se o preenchimento automático foi habilitado (`Completes = true`).

Consulte o [bancos de dados e caixas de combinação](~/mac/app-fundamentals/databases.md#Databases-and-ComboBoxes) seção o [trabalhando com bancos de dados](~/mac/app-fundamentals/databases.md) documento para obter mais detalhes.

<a name="Adjusting-the-Lists-Appearance" />

### <a name="adjusting-the-lists-appearance"></a>Ajustando a aparência da lista

Os métodos a seguir estão disponíveis para ajustar a aparência do controle de Menu:

- `HasVerticalScroller` -Se `true`, o controle exibirá uma barra de rolagem vertical. 
- `VisibleItems` -Ajuste o número de itens exibidos quando o controle é aberto. O valor padrão é cinco (5).
- `IntercellSpacing` -Ajustar a quantidade de espaço em torno de um determinado item, fornecendo um `NSSize` onde o `Width` Especifica as margens esquerdas e direita e o `Height` Especifica o espaço antes e depois de um item.
- `ItemHeight` -Especifica a altura de cada item na lista.

Para tipos de lista suspensa de `NSPopupButtons`, o primeiro Item de Menu fornece o título para o controle. Por exemplo: 

[ ![](standard-controls-images/menu02.png "Um controle de menu de exemplo")](standard-controls-images/menu02.png)

Para alterar o título, expor este item como uma **tomada** e use um código como o seguinte:

```csharp
DropDownSelected.Title = "Item 1";
```

<a name="Manipulating-the-Selected-Items" />

### <a name="manipulating-the-selected-items"></a>Manipulando os itens selecionados

Os seguintes métodos e propriedades permitem que você manipule os itens selecionados na lista de controle de Menu:

- `SelectItem` -Seleciona o item no índice especificado.
- `Select` -Selecione o valor do item determinado.
- `DeselectItem` -Desmarca o item no índice especificado.
- `SelectedIndex` -Retorna o índice do item atualmente selecionado.
- `SelectedValue` -Retorna o valor do item atualmente selecionado.

Use o `ScrollItemAtIndexToTop` para apresentar o item no índice especificado na parte superior da lista e o `ScrollItemAtIndexToVisible` para rolar a lista até que o item no índice especificado está visível.

<a name="Responding to Events" />

### <a name="responding-to-events"></a>Respondendo a eventos

Controles de menu fornecem os seguintes eventos para responder à interação do usuário:

- `SelectionChanged` -É chamado quando o usuário selecionou um valor da lista.
- `SelectionIsChanging` -É chamado antes que o novo item de usuário selecionado torna-se a seleção ativa.
- `WillPopup` -É chamado antes da exibição da lista suspensa de itens.
- `WillDismiss` -É chamado antes do fechamento de lista suspensa de itens.

Para `NSComboBox` controles, eles incluem todos os eventos mesmo como o `NSTextField`, como o `Changed` evento que é chamado sempre que o usuário edita o valor do texto na caixa de combinação.

Opcionalmente, você pode responder a um itens de Menu de dados interno definida no construtor de Interface, anexando o item a ser selecionado um **ação** e use um código como o seguinte para responder a **ação** sendo disparado pelo usuário:

```csharp
partial void ItemOne (Foundation.NSObject sender) {
    DropDownSelected.Title = "Item 1";
    FeedbackLabel.StringValue = "Item One Selected";
}
```

Para obter mais informações sobre como trabalhar com Menus e controles de Menu, consulte nosso [Menus](~/mac/user-interface/menu.md) e [botão pop-up e listas suspensas](~/mac/user-interface/menu.md) documentação.

<a name="Working_with_Selection_Controls" />

## <a name="working-with-selection-controls"></a>Trabalhando com controles de seleção

AppKit fornece vários tipos de controles de seleção que pode ser usado em seu Design de Interface do usuário. Para obter mais informações, consulte o [controles de seleção](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsSelection.html#//apple_ref/doc/uid/20000957-CH49-SW1) seção da Apple [diretrizes de Interface humana do sistema operacional X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[ ![](standard-controls-images/select01.png "Controles de seleção de exemplo")](standard-controls-images/select01.png)

Há duas maneiras para controlar quando um controle de seleção tem a interação do usuário, expondo-o como um **ação**. Por exemplo:

```csharp
partial void SegmentButtonPressed (Foundation.NSObject sender) {
    FeedbackLabel.StringValue = string.Format("Button {0} Pressed",SegmentButtons.SelectedSegment);
}
```

Ou anexando um **delegado** para o `Activated` evento. Por exemplo:

```csharp
TickedSlider.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Stepper Value: {0:###}",TickedSlider.IntValue);
};
```

Para definir ou ler o valor de um controle de seleção, use o `IntValue` propriedade. Por exemplo:

```csharp
FeedbackLabel.StringValue = string.Format("Stepper Value: {0:###}",TickedSlider.IntValue);
```

Os controles especiais (como também cor e imagem bem) têm propriedades específicas para seus tipos de valor. Por exemplo:

```csharp
CollorWell.Color = NSColor.Red;
ImageWell.Image = NSImage.ImageNamed ("tag.png");

```

O `NSDatePicker` tem as seguintes propriedades para trabalhar diretamente com a data e hora:

- **DateValue** -o valor de data e hora atual como um `NSDate`.
- **Local** -local do usuário como um `NSLocal`.
- **TimeInterval** -o valor de tempo como um `Double`.
- **Fuso horário** -fuso horário do usuário como um `NSTimeZone`.

<a name="Working_with_Indicator_Controls" />

## <a name="working-with-indicator-controls"></a>Trabalhando com controles de indicador

AppKit fornece vários tipos de controles de indicador que pode ser usado em seu Design de Interface do usuário. Para obter mais informações, consulte o [controles de indicador](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsIndicators.html#//apple_ref/doc/uid/20000957-CH50-SW1) seção da Apple [diretrizes de Interface humana do sistema operacional X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[ ![](standard-controls-images/level01.png "Controles de indicador de exemplo")](standard-controls-images/level01.png)

Há duas maneiras para controlar quando um controle de indicador tem interação do usuário, por expô-lo como um **ação** ou um **tomada** e anexar um **delegado** para o `Activated`evento. Por exemplo:

```csharp
LevelIndicator.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Level: {0:###}",LevelIndicator.DoubleValue);
};
```

Para ler ou definir o valor do indicador de controle, use o `DoubleValue` propriedade. Por exemplo:

```csharp
FeedbackLabel.StringValue = string.Format("Rating: {0:###}",Rating.DoubleValue);
```

O indeterminado e indicadores de progresso assíncrono devem ser animada quando exibido. Use o `StartAnimation` método para iniciar a animação quando elas são exibidas. Por exemplo:

```csharp
Indeterminate.StartAnimation (this);
AsyncProgress.StartAnimation (this);
```

Chamar o `StopAnimation` método irá parar a animação.

<a name="Working_with_Text_Controls" />

## <a name="working-with-text-controls"></a>Trabalhando com controles de texto

AppKit fornece vários tipos de controles de texto que pode ser usado em seu Design de Interface do usuário. Para obter mais informações, consulte o [controles de texto](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsText.html#//apple_ref/doc/uid/20000957-CH51-SW1) seção da Apple [diretrizes de Interface humana do sistema operacional X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[ ![](standard-controls-images/text01.png "Controles de texto de exemplo")](standard-controls-images/text01.png)

Para campos de texto (`NSTextField`), os eventos a seguir podem ser usados para controlar a interação do usuário:

- **Alterado** -é acionado sempre que o usuário altera o valor do campo. Por exemplo, cada caractere digitado.
- **EditingBegan** -é acionado quando o usuário seleciona o campo para edição.
- **EditingEnded** - quando o usuário pressiona a tecla Enter no campo ou deixa o campo.

Use o `StringValue` propriedade para ler ou definir o valor do campo. Por exemplo:

```csharp
FeedbackLabel.StringValue = string.Format("User ID: {0}",UserField.StringValue);
```

Para os campos que exibem ou editar os valores numéricos, você pode usar o `IntValue` propriedade. Por exemplo:

```csharp
FeedbackLabel.StringValue = string.Format("Number: {0}",NumberField.IntValue);
```

Um `NSTextView` fornece um texto em destaque completo editar e exibir a área com formatação internos. Como um `NSTextField`, use o `StringValue` propriedade para ler ou definir o valor da área.

Para obter um exemplo de um exemplo complexo de trabalhar com modos de exibição de texto em um aplicativo de Xamarin.Mac, consulte o [aplicativo de exemplo SourceWriter](https://developer.xamarin.com/samples/mac/SourceWriter/). SourceWriter é um editor de código-fonte simples que dá suporte para a conclusão de código e realce de sintaxe simples.

O código do SourceWriter foi totalmente comentado e, quando disponível, foram fornecidos links de tecnologias-chave ou métodos para informações relevantes na Documentação de Guias do Xamarin.Mac.

<a name="Working_with_Content_Views" />

## <a name="working-with-content-views"></a>Trabalhando com visualizações de conteúdo

AppKit fornece vários tipos de modos de exibição de conteúdo que pode ser usado em seu Design de Interface do usuário. Para obter mais informações, consulte o [modos de exibição de conteúdo](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsView.html#//apple_ref/doc/uid/20000957-CH52-SW1) seção da Apple [diretrizes de Interface humana do sistema operacional X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/).

[ ![](standard-controls-images/content01.png "Um modo de exibição de conteúdo de exemplo")](standard-controls-images/content01.png)

<a name="Popovers" />

### <a name="popovers"></a>Popovers

Um popover é um elemento de interface do usuário transitório que fornece a funcionalidade que está diretamente relacionada a um determinado um controle ou uma área na tela. Um popover flutua acima da janela que contém o controle ou a área que está relacionado ao e sua borda inclui uma seta para indicar o ponto do qual ele surgiu.

Para criar um popover, faça o seguinte:

1. Abra o `.storyboard` arquivo da janela que você deseja adicionar um popover para clicando duas vezes no **Gerenciador de soluções**
2. Arraste um **exibir controlador** do **Inspetor de biblioteca** até o **Editor de Interface**: 

    [ ![](standard-controls-images/content02.png "Selecionar um controlador de exibição da biblioteca")](standard-controls-images/content02.png)
4. Definir o tamanho e o layout do **exibição personalizada**: 

    [ ![](standard-controls-images/content04.png "Editando o layout")](standard-controls-images/content04.png)
5. CTRL + clique e arraste da origem de pop-up para o **View Controller**: 

    [ ![](standard-controls-images/content05.png "Arrastar para criar um segue")](standard-controls-images/content05.png)
6. Selecione **Popover** no menu pop-up: 

    [ ![](standard-controls-images/content06.png "Definição do tipo segue")](standard-controls-images/content06.png)
7. Salve suas alterações e retornar ao Visual Studio para Mac sincronizar com o Xcode.

<a name="Tab_Views" />

### <a name="tab-views"></a>Exibições de guia

Modos de exibição da guia consiste em uma lista de guia (que parece semelhante a um controle segmentado) combinado com um conjunto de exibições que são chamados _painéis_. Quando o usuário seleciona uma nova guia, o painel que está anexado a ele será exibido. Cada painel contém seu próprio conjunto de controles.

Ao trabalhar com uma exibição da guia no construtor de Interface do Xcode, use o **Inspetor de atributo** para definir o número de guias:

[ ![](standard-controls-images/content08.png "Editar o número de guias")](standard-controls-images/content08.png)

Selecione cada guia no **hierarquia Interface** para definir seu **título** e adicionar elementos de interface do usuário para sua **painel**:

[ ![](standard-controls-images/content09.png "Editando as guias no Xcode")](standard-controls-images/content09.png)

<a name="Data_Binding_AppKit_Controls" />

## <a name="data-binding-appkit-controls"></a>Controles de AppKit de associação de dados

Usando técnicas de codificação de chave-valor e associação de dados em seu aplicativo Xamarin.Mac, você pode diminuir significativamente a quantidade de código que você precisa escrever e manter para preencher e trabalhar com elementos de interface do usuário. Você também tem a vantagem de desligamento ainda mais seus dados de backup (_modelo de dados_) do seu front end Interface do usuário (_Model-View-Controller_), à esquerda para mais fáceis de manter, mais flexível de aplicativos Design.

Codificação de chave-valor (KVC) é um mecanismo para acessar propriedades de um objeto indiretamente, usando métodos de acessador ou chaves (especialmente formatadas cadeias de caracteres) para identificar as propriedades em vez de acessá-los por meio de variáveis de instância (`get/set`). Ao implementar acessadores compatível com codificação de chave-valor em seu aplicativo Xamarin.Mac, você obtém acesso a outros recursos de macOS como chave-valor observando (KVO), associação de dados, dados principais, associações de Cocoa e ScriptableType.

Para obter mais informações, consulte o [associação de dados simples](~/mac/app-fundamentals/databinding.md#Simple_Data_Binding) seção do nosso [associação de dados e a codificação de chave-valor](~/mac/app-fundamentals/databinding.md) documentação.


<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo obteve uma visão detalhada de como trabalhar com os controles AppKit padrão como botões, rótulos, campos de texto, caixas de seleção e segmentado controles em um aplicativo Xamarin.Mac. Ele coberto adicioná-los para um Design de Interface do usuário no construtor de Interface do Xcode, expondo-os para o código por meio de saídas e as ações e trabalhar com controles de AppKit em código c#.

## <a name="related-links"></a>Links relacionados

- [MacControls (exemplo)](https://developer.xamarin.com/samples/mac/MacControls/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Windows](~/mac/user-interface/window.md)
- [Associação de dados e a codificação de chave-valor](~/mac/app-fundamentals/databinding.md)
- [Diretrizes de Interface do sistema operacional X humanos](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Sobre controles e modos de exibição](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1)
