---
title: Controles padrão no Xamarin. Mac
description: Este artigo aborda como trabalhar com os controles padrão do AppKit, como botões, rótulos, campos de texto, caixas de seleção e controles segmentados em um aplicativo Xamarin. Mac. Ele descreve como adicioná-los a uma interface com Interface Builder e interagir com eles no código.
ms.prod: xamarin
ms.assetid: d2593883-d255-431f-9781-75f04d8cecea
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: 18ce7eb4dcd68596027f739579fd5a4b70497829
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772705"
---
# <a name="standard-controls-in-xamarinmac"></a>Controles padrão no Xamarin. Mac

_Este artigo aborda como trabalhar com os controles padrão do AppKit, como botões, rótulos, campos de texto, caixas de seleção e controles segmentados em um aplicativo Xamarin. Mac. Ele descreve como adicioná-los a uma interface com Interface Builder e interagir com eles no código._

Ao trabalhar com C# o e o .net em um aplicativo Xamarin. Mac, você tem acesso aos mesmos controles AppKit que um desenvolvedor trabalhando no *Objective-C* e no *Xcode* . Como o Xamarin. Mac se integra diretamente com o Xcode, você pode usar o _interface Builder_ do Xcode para criar e manter seus controles Appkit (ou, opcionalmente C# , criá-los diretamente no código).

Os controles AppKit são os elementos da interface do usuário que são usados para criar a interface para o aplicativo Xamarin. Mac. Eles consistem em elementos como botões, rótulos, campos de texto, caixas de seleção e controles segmentados e causam ações instantâneas ou resultados visíveis quando um usuário os manipula.

[![](standard-controls-images/intro01.png "A tela principal do aplicativo de exemplo")](standard-controls-images/intro01.png#lightbox)

Neste artigo, abordaremos as noções básicas de como trabalhar com controles AppKit em um aplicativo Xamarin. Mac. É altamente recomendável que você trabalhe pelo artigo [Hello, Mac](~/mac/get-started/hello-mac.md) primeiro, especificamente a [introdução às seções Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [ações](~/mac/get-started/hello-mac.md#outlets-and-actions) , pois ela aborda os principais conceitos e técnicas que usaremos em Este artigo.

Talvez você queira dar uma olhada na seção [expondo C# classes/métodos para Objective-C](~/mac/internals/how-it-works.md) do documento [interno do Xamarin. Mac](~/mac/internals/how-it-works.md) também, explica os `Register` comandos e `Export` usados para conectar suas C# classes ao Objetos Objective-C e elementos de interface do usuário.

<a name="Introduction_to_Controls_and_Views" />

## <a name="introduction-to-controls-and-views"></a>Introdução aos controles e modos de exibição

o macOS (anteriormente conhecido como Mac OS X) fornece um conjunto padrão de controles de interface do usuário por meio da estrutura AppKit. Eles consistem em elementos como botões, rótulos, campos de texto, caixas de seleção e controles segmentados e causam ações instantâneas ou resultados visíveis quando um usuário os manipula.

Todos os controles AppKit têm uma aparência padrão interna que será apropriada para a maioria dos usos, alguns especificam uma aparência alternativa para uso em uma área de quadro de janela ou em um contexto de _efeito de Vibrance_ , como em uma área da barra lateral ou em um centro de notificações Widget.

A Apple sugere as seguintes diretrizes ao trabalhar com controles AppKit:

- Evite misturar tamanhos de controle no mesmo modo de exibição.
- Em geral, Evite redimensionar os controles verticalmente.
- Use a fonte do sistema e o tamanho de texto apropriado dentro de um controle.
- Use o espaçamento adequado entre os controles.

Para obter mais informações, pleas consulte a seção [sobre controles e exibições](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1) das [diretrizes de interface humana do os X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)da Apple.

<a name="Using_Controls_in_a_Window_Frame" />

### <a name="using-controls-in-a-window-frame"></a>Usando controles em um quadro de janela

Há um subconjunto de controles AppKit que incluem um estilo de exibição que permite que eles sejam incluídos na área de um quadro da janela. Para obter um exemplo, consulte a barra de ferramentas do aplicativo de email:

[![](standard-controls-images/mailapp.png "Um quadro de janela Mac")](standard-controls-images/mailapp.png#lightbox)

- **Botão de textura arredondada** -A `NSButton` com um `NSTexturedRoundedBezelStyle`estilo de.
- **Controle segmentado com arredondado texturizado** -a `NSSegmentedControl` com um `NSSegmentStyleTexturedRounded`estilo de.
- **Controle segmentado com arredondado texturizado** -a `NSSegmentedControl` com um `NSSegmentStyleSeparated`estilo de.
- **Menu pop-up de textura arredondada** -A `NSPopUpButton` com um `NSTexturedRoundedBezelStyle`estilo de.
- **Menu suspenso de textura arredondada** -A `NSPopUpButton` com um estilo de `NSTexturedRoundedBezelStyle`.
- **Barra de pesquisa** - `NSSearchField`a.

A Apple sugere as seguintes diretrizes ao trabalhar com controles AppKit em um quadro de janela:

- Não use estilos de controle específicos de quadro de janela no corpo da janela.
- Não use controles de corpo de janela ou estilos no quadro de janela.

Para obter mais informações, pleas consulte a seção [sobre controles e exibições](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1) das [diretrizes de interface humana do os X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)da Apple.

<a name="Creating_a_User_Interface_in_Interface_Builder" />

## <a name="creating-a-user-interface-in-interface-builder"></a>Criando uma interface do usuário no Interface Builder

Ao criar um novo aplicativo Xamarin. Mac Cocoa, você obtém uma janela padrão em branco, por padrão. Essas janelas são definidas em um `.storyboard` arquivo incluído automaticamente no projeto. Para editar o design do Windows, na **Gerenciador de soluções**, clique duas vezes `Main.storyboard` no arquivo:

[![](standard-controls-images/edit01.png "Selecionando o storyboard principal no Gerenciador de Soluções")](standard-controls-images/edit01.png#lightbox)

Isso abrirá o design da janela na Interface Builder do Xcode:

[![](standard-controls-images/edit02.png "Editando o storyboard no Xcode")](standard-controls-images/edit02.png#lightbox)

Para criar sua interface do usuário, você arrastará elementos da interface de usuário (controles AppKit) do **Inspetor de biblioteca** para o **Editor de interface** no interface Builder. No exemplo a seguir, um controle de **exibição de divisão vertical** tem sido um medicamento do **Inspetor de biblioteca** e colocado na janela no **Editor de interface**:

[![](standard-controls-images/edit03.png "Selecionando uma exibição de divisão da biblioteca")](standard-controls-images/edit03.png#lightbox)

Para obter mais informações sobre como criar uma interface do usuário no Interface Builder, consulte nossa [introdução ao Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) documentação.

<a name="Sizing_and_Positioning" />

### <a name="sizing-and-positioning"></a>Dimensionamento e posicionamento

Depois que um controle tiver sido incluído na interface do usuário, use o **Editor de restrição** para definir seu local e tamanho inserindo valores manualmente e controle como o controle é posicionado automaticamente e dimensionado quando a janela ou exibição pai é redimensionada:

[![](standard-controls-images/edit04.png "Definindo as restrições")](standard-controls-images/edit04.png#lightbox)

Use as **transmissões vermelhas** em torno do exterior da caixa de **redimensionamento** para _fixar_ um controle em um determinado local (x, y). Por exemplo: 

[![](standard-controls-images/edit05.png "Editando uma restrição")](standard-controls-images/edit05.png#lightbox)

Especifica que o controle selecionado (no**Editor de interface**de **exibição** & de hierarquia) ficará preso no local superior e direito da janela ou exibição à medida que ele for redimensionado ou movido. 

Outros elementos das propriedades de controle do editor, como altura e largura:

[![](standard-controls-images/edit06.png "Definindo a altura")](standard-controls-images/edit06.png#lightbox)

Você também pode controlar o alinhamento dos elementos com restrições usando o **Editor de alinhamento**:

[![](standard-controls-images/edit07.png "O editor de alinhamento")](standard-controls-images/edit07.png#lightbox)

> [!IMPORTANT]
> Ao contrário do iOS, onde (0, 0) é o canto superior esquerdo da tela, no macOS (0, 0) é o canto inferior esquerdo. Isso ocorre porque o macOS usa um sistema de coordenadas matemáticas com os valores numéricos aumentando no valor para cima e à direita. Você precisa levar isso em consideração ao colocar os controles AppKit em uma interface do usuário.

<a name="Setting_a_Custom_Class" />

### <a name="setting-a-custom-class"></a>Definindo uma classe personalizada

Há ocasiões em que você trabalha com controles AppKit, que precisará de uma subclasse e controle existente e criar sua própria versão personalizada dessa classe. Por exemplo, definir uma versão personalizada da lista de origem:

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

Em que `[Register("SourceListView")]` a instrução expõe `SourceListView` a classe para Objective-C para que possa ser usada em interface Builder. Para obter mais informações, consulte a seção [ C# expondo classes/métodos para Objective-C](~/mac/internals/how-it-works.md) do documento [interno do Xamarin. Mac](~/mac/internals/how-it-works.md) , ele explica os `Register` comandos e `Export` usados para conectar suas C# classes ao Objetos Objective-C e elementos de interface do usuário.

Com o código acima em vigor, você pode arrastar um controle AppKit, do tipo base que você está estendendo, para a superfície de design (no exemplo abaixo, uma **lista de origem**), alterne para o Inspetor de **identidade** e defina a **classe personalizada** como o nome que você expôs ao Objective-C `SourceListView`(exemplo):

[![](standard-controls-images/edit10.png "Configurando uma classe personalizada no Xcode")](standard-controls-images/edit10.png#lightbox)

<a name="Exposing_Outlets_and_Actions" />

### <a name="exposing-outlets-and-actions"></a>Expondo saídas e ações

Antes que um controle AppKit possa ser acessado no C# código, ele precisa ser exposto como uma **tomada** ou **ação**. Para fazer isso, selecione o controle fornecido na **hierarquia de interface** ou no **Editor de interface** e alterne para o modo de exibição do **Assistente** (certifique `.h` -se de que você tenha o da sua janela selecionada para edição):

[![](standard-controls-images/edit11.png "Selecionando o arquivo correto a ser editado")](standard-controls-images/edit11.png#lightbox)

Controle-arraste do controle AppKit para o arquivo de `.h` permissão para começar a criar uma **tomada** ou **ação**:

[![](standard-controls-images/edit12.png "Arrastando para criar uma tomada ou ação")](standard-controls-images/edit12.png#lightbox)

Selecione o tipo de exposição a ser criado e forneça um **nome**para a **tomada** ou **ação** : 

[![](standard-controls-images/edit13.png "Configurando a tomada ou a ação")](standard-controls-images/edit13.png#lightbox)

Para obter mais informações sobre como trabalhar com **saídas** e **ações**, consulte a seção [saídas e ações](~/mac/get-started/hello-mac.md#outlets-and-actions) da nossa [introdução ao Xcode e à documentação Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) .

<a name="Synchronizing_Changes_with_Xcode" />

### <a name="synchronizing-changes-with-xcode"></a>Sincronizando alterações com o Xcode

Quando você voltar para Visual Studio para Mac do Xcode, as alterações feitas no Xcode serão automaticamente sincronizadas com seu projeto Xamarin. Mac.

Se você selecionar o `SplitViewController.designer.cs` no **Gerenciador de soluções** poderá ver como sua **tomada** e **ação** foram conectadas em nosso C# código:

[![](standard-controls-images/sync01.png "Sincronizando alterações com o Xcode")](standard-controls-images/sync01.png#lightbox)

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

Como você pode ver, Visual Studio para Mac escuta as alterações `.h` no arquivo e, em seguida, sincroniza automaticamente essas alterações no respectivo `.designer.cs` arquivo para expô-las ao seu aplicativo. Você também pode observar que `SplitViewController.designer.cs` é uma classe parcial, para que Visual Studio para Mac não precise modificar `SplitViewController.cs` , o que substituiria as alterações feitas na classe.

Normalmente, você nunca precisará abrir o `SplitViewController.designer.cs` mesmo, ele foi apresentado aqui apenas para fins educacionais.

> [!IMPORTANT]
> Na maioria das situações, Visual Studio para Mac verá automaticamente todas as alterações feitas no Xcode e as sincronizará com o projeto do Xamarin. Mac. Na improvável ocorrência de que a sincronização não aconteça automaticamente, retorne para o Xcode e depois novamente para o Visual Studio para Mac. Isso normalmente dará início a um ciclo de sincronização.

<a name="Working_with_Buttons" />

## <a name="working-with-buttons"></a>Trabalhando com botões

O AppKit fornece vários tipos de botão que podem ser usados no design da interface do usuário. Para obter mais informações, consulte a seção de [botões](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsButtons.html#//apple_ref/doc/uid/20000957-CH48-SW1) das [diretrizes de interface humana do os X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)da Apple. 

[![](standard-controls-images/buttons01.png "Um exemplo dos diferentes tipos de botão")](standard-controls-images/buttons01.png#lightbox)

Se um botão tiver sido exposto por meio de uma **tomada**, o código a seguir responderá a ele sendo pressionado:

```csharp
ButtonOutlet.Activated += (sender, e) => {
        FeedbackLabel.StringValue = "Button Outlet Pressed";
};
```

Para botões que foram expostos por meio de **ações**, `public partial` um método será criado automaticamente para você com o nome que você escolheu no Xcode. Para responder à **ação**, conclua o método parcial na classe na qual a **ação** foi definida. Por exemplo:

```csharp
partial void ButtonAction (Foundation.NSObject sender) {
    // Do something in response to the Action
    FeedbackLabel.StringValue = "Button Action Pressed";
}
```

Para botões que têm um estado (como **ativado** e **desativado**), o estado pode ser marcado ou definido com a `State` Propriedade em relação `NSCellStateValue` à enumeração. Por exemplo:

```csharp
DisclosureButton.Activated += (sender, e) => {
    LorumIpsum.Hidden = (DisclosureButton.State == NSCellStateValue.On);
};
```

Onde `NSCellStateValue` pode ser:

- **On** -o botão é enviado por Push ou o controle é selecionado (por exemplo, um check-in de uma caixa de seleção).
- **Off** -o botão não é enviado por Push ou o controle não está selecionado.
- **Mixed** -uma mistura de Estados **ligado** e **desligado** .

<a name="Mark-a-Button-as-Default-and-Set-Key-Equivalent" />

### <a name="mark-a-button-as-default-and-set-key-equivalent"></a>Marcar um botão como padrão e definir equivalente de chave

Para qualquer botão que você tenha adicionado a um design de interface do usuário, você pode marcar esse botão como o botão _padrão_ que será ativado quando o usuário pressionar a tecla **retornar/inserir** no teclado. No macOS, esse botão receberá uma cor de fundo azul por padrão.

Para definir um botão como padrão, selecione-o no Interface Builder do Xcode. Em seguida, no **Inspetor de atributo**, selecione o campo **equivalente de chave** e pressione a tecla de **retorno/inserção** :

[![](standard-controls-images/buttons03.png "Editando a chave equivalente")](standard-controls-images/buttons03.png#lightbox)

Igualmente, você pode atribuir qualquer sequência de chaves que possa ser usada para ativar o botão usando o teclado em vez do mouse. Por exemplo, pressionando as teclas Command-C na imagem acima.

Quando o aplicativo é executado e a janela com o botão é chave e focada, se o usuário pressionar Command-C, a ação do botão será ativada (como se o usuário tivesse clicado no botão).

<a name="Working_with_Checkboxes_and_Radio_Buttons" />

## <a name="working-with-checkboxes-and-radio-buttons"></a>Trabalhando com caixas de seleção e botões de opção

O AppKit fornece vários tipos de caixas de seleção e grupos de botões de opção que podem ser usados no design da interface do usuário. Para obter mais informações, consulte a seção de [botões](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsButtons.html#//apple_ref/doc/uid/20000957-CH48-SW1) das [diretrizes de interface humana do os X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)da Apple. 

[![](standard-controls-images/buttons02.png "Um exemplo dos tipos de caixa de seleção disponíveis")](standard-controls-images/buttons02.png#lightbox)

Caixas de seleção e botões de opção (expostos via **saídas**) têm um estado (como **ligado** e **desligado**), o estado pode ser marcado ou definido com `State` a propriedade em `NSCellStateValue` relação à enumeração. Por exemplo:

```csharp
AdjustTime.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Adjust Time: {0}",AdjustTime.State == NSCellStateValue.On);
};
```

Onde `NSCellStateValue` pode ser:

- **On** -o botão é enviado por Push ou o controle é selecionado (por exemplo, um check-in de uma caixa de seleção).
- **Off** -o botão não é enviado por Push ou o controle não está selecionado.
- **Mixed** -uma mistura de Estados **ligado** e **desligado** .

Para selecionar um botão em um grupo de botões de opção, expor o botão de opção para selecionar como uma tomada `State` e definir sua propriedade. Por exemplo:

```csharp
partial void SelectCar (Foundation.NSObject sender) {
    TransportationCar.State = NSCellStateValue.On;
    FeedbackLabel.StringValue = "Car Selected";
}
```

Para obter uma coleção de botões de opção para atuar como um grupo e lidar automaticamente com o estado selecionado, crie uma nova **ação** e anexe cada botão no grupo a ela:

![](standard-controls-images/buttons04.png "Criando uma nova ação")

Em seguida, atribua um `Tag` exclusivo a cada botão de opção no **Inspetor de atributo**:

![](standard-controls-images/buttons05.png "Editando uma marca de botão de opção")

Salve as alterações e retorne ao Visual Studio para Mac, adicione o código para manipular a **ação** à qual todos os botões de opção estão anexados:

```csharp
partial void NumberChanged(Foundation.NSObject sender)
{
    var check = sender as NSButton;
    Console.WriteLine("Changed to {0}", check.Tag);
}
```

Você pode usar a `Tag` propriedade para ver qual botão de opção foi selecionado.

<a name="Working_with_Menu_Controls" />

## <a name="working-with-menu-controls"></a>Trabalhando com controles de menu

O AppKit fornece vários tipos de controles de menu que podem ser usados no design da interface do usuário. Para obter mais informações, consulte a seção [controles de menu](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlswithMenus.html#//apple_ref/doc/uid/20000957-CH100-SW1) das diretrizes de [interface humana do os X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)da Apple. 

[![](standard-controls-images/menu01.png "Controles de menu de exemplo")](standard-controls-images/menu01.png#lightbox)

<a name="Providing-Menu-Control-Data" />

### <a name="providing-menu-control-data"></a>Fornecendo dados de controle de menu

Os controles de menu disponíveis para macOS podem ser definidos para popular a lista suspensa de uma lista interna (que pode ser predefinida em Interface Builder ou populada por meio de código) ou fornecendo sua própria fonte de dados personalizada e externa.

<a name="Working-with-Internal-Data" />

#### <a name="working-with-internal-data"></a>Trabalhando com dados internos

Além de definir itens em interface Builder, controles de menu ( `NSComboBox`como) fornecem um conjunto completo de métodos que permitem adicionar, editar ou excluir os itens da lista interna que eles mantêm:

- `Add`-Adiciona um novo item ao final da lista.
- `GetItem`-Retorna o item no índice fornecido.
- `Insert`-Insere um novo item na lista no local especificado.
- `IndexOf`-Retorna o índice do item especificado.
- `Remove`-Remove o item determinado da lista.
- `RemoveAll`-Remove todos os itens da lista.
- `RemoveAt`-Remove o item no índice fornecido.
- `Count`-Retorna o número de itens na lista.

> [!IMPORTANT]
> Se você estiver usando uma fonte de dados externa`UsesDataSource = true`(), chamar qualquer um dos métodos acima gerará uma exceção.

<a name="Working-with-an-External-Data-Source" />

#### <a name="working-with-an-external-data-source"></a>Trabalhando com uma fonte de dados externa

Em vez de usar os dados internos interno para fornecer as linhas para o controle de menu, você pode, opcionalmente, usar uma fonte de dados externa e fornecer seu próprio armazenamento de backup para os itens (como um banco de dados SQLite).

Para trabalhar com uma fonte de dados externa, você criará uma instância da fonte de dados do controle de`NSComboBoxDataSource` menu (por exemplo) e substituirá vários métodos para fornecer os dados necessários:

- `ItemCount`-Retorna o número de itens na lista.
- `ObjectValueForItem`-Retorna o valor do item para um determinado índice.
- `IndexOfItem`-Retorna o índice para o valor do item de atribuições.
- `CompletedString`-Retorna o primeiro valor de item correspondente para o valor de item parcialmente tipado. Esse método será chamado somente se o preenchimento automático tiver sido habilitado`Completes = true`().

Consulte a seção [bancos de dados e caixas de combinação](~/mac/app-fundamentals/databases.md#Databases-and-ComboBoxes) do documento [trabalhando com bancos de dados](~/mac/app-fundamentals/databases.md) para obter mais detalhes.

<a name="Adjusting-the-Lists-Appearance" />

### <a name="adjusting-the-lists-appearance"></a>Ajustando a aparência da lista

Os métodos a seguir estão disponíveis para ajustar a aparência do controle de menu:

- `HasVerticalScroller`-Se `true`, o controle exibirá uma barra de rolagem vertical. 
- `VisibleItems`-Ajustar o número de itens exibidos quando o controle é aberto. O valor padrão é cinco (5).
- `IntercellSpacing`-Ajuste a quantidade de espaço em um determinado item fornecendo um `NSSize` local onde o `Width` especifica as margens esquerda e `Height` direita e especifica o espaço antes e depois de um item.
- `ItemHeight`-Especifica a altura de cada item na lista.

Para tipos suspensos do `NSPopupButtons`, o primeiro item de menu fornece o título para o controle. Por exemplo: 

[![](standard-controls-images/menu02.png "Um controle de menu de exemplo")](standard-controls-images/menu02.png#lightbox)

Para alterar o título, expor este item como uma **tomada** e use um código semelhante ao seguinte:

```csharp
DropDownSelected.Title = "Item 1";
```

<a name="Manipulating-the-Selected-Items" />

### <a name="manipulating-the-selected-items"></a>Manipulando os itens selecionados

Os métodos e as propriedades a seguir permitem que você manipule os itens selecionados na lista do controle de menu:

- `SelectItem`-Seleciona o item no índice fornecido.
- `Select`-Selecione o valor do item fornecido.
- `DeselectItem`– Anula a seleção do item no índice fornecido.
- `SelectedIndex`-Retorna o índice do item selecionado no momento.
- `SelectedValue`-Retorna o valor do item selecionado no momento.

Use o `ScrollItemAtIndexToTop` para apresentar o item no índice especificado na parte superior da lista e a `ScrollItemAtIndexToVisible` para rolar até a lista até que o item no índice especificado esteja visível.

<a name="Responding to Events" />

### <a name="responding-to-events"></a>Respondendo a eventos

Os controles de menu fornecem os seguintes eventos para responder à interação do usuário:

- `SelectionChanged`-É chamado quando o usuário seleciona um valor da lista.
- `SelectionIsChanging`-É chamado antes que o novo item selecionado do usuário se torne a seleção ativa.
- `WillPopup`-É chamado antes que a lista suspensa de itens seja exibida.
- `WillDismiss`-É chamado antes que a lista suspensa de itens seja fechada.

Para `NSComboBox` controles, eles incluem todos os mesmos eventos que o `NSTextField`, como o `Changed` evento que é chamado sempre que o usuário edita o valor do texto na caixa de combinação.

Opcionalmente, você pode responder aos itens de menu de dados internos definidos em Interface Builder sendo selecionado anexando o item a uma **ação** e usar um código como o seguinte para responder à **ação** que está sendo disparada pelo usuário:

```csharp
partial void ItemOne (Foundation.NSObject sender) {
    DropDownSelected.Title = "Item 1";
    FeedbackLabel.StringValue = "Item One Selected";
}
```

Para obter mais informações sobre como trabalhar com menus e controles de menu, consulte nossos [menus](~/mac/user-interface/menu.md) e o [botão pop-up e a documentação de listas suspensas](~/mac/user-interface/menu.md) .

<a name="Working_with_Selection_Controls" />

## <a name="working-with-selection-controls"></a>Trabalhando com controles de seleção

O AppKit fornece vários tipos de controles de seleção que podem ser usados no design da interface do usuário. Para obter mais informações, consulte a seção [controles de seleção](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsSelection.html#//apple_ref/doc/uid/20000957-CH49-SW1) das diretrizes de [interface humana do os X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)da Apple. 

[![](standard-controls-images/select01.png "Controles de seleção de exemplo")](standard-controls-images/select01.png#lightbox)

Há duas maneiras de controlar quando um controle de seleção tem interação com o usuário, expondo-a como uma **ação**. Por exemplo:

```csharp
partial void SegmentButtonPressed (Foundation.NSObject sender) {
    FeedbackLabel.StringValue = string.Format("Button {0} Pressed",SegmentButtons.SelectedSegment);
}
```

Ou anexando um **delegado** ao `Activated` evento. Por exemplo:

```csharp
TickedSlider.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Stepper Value: {0:###}",TickedSlider.IntValue);
};
```

Para definir ou ler o valor de um controle de seleção, use `IntValue` a propriedade. Por exemplo:

```csharp
FeedbackLabel.StringValue = string.Format("Stepper Value: {0:###}",TickedSlider.IntValue);
```

Os controles de especialidade (como well Color e Image well) têm propriedades específicas para seus tipos de valor. Por exemplo:

```csharp
ColorWell.Color = NSColor.Red;
ImageWell.Image = NSImage.ImageNamed ("tag.png");

```

O `NSDatePicker` tem as seguintes propriedades para trabalhar diretamente com data e hora:

- **DateValue** -o valor de data e hora atual `NSDate`como um.
- **Local** -o local do usuário como um `NSLocal`.
- **TimeInterval** -o valor de tempo como `Double`um.
- **Timezone** -o fuso horário do usuário como um `NSTimeZone`.

<a name="Working_with_Indicator_Controls" />

## <a name="working-with-indicator-controls"></a>Trabalhando com controles de indicador

O AppKit fornece vários tipos de controles de indicador que podem ser usados no design da interface do usuário. Para obter mais informações, consulte a seção [controles de indicadores](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsIndicators.html#//apple_ref/doc/uid/20000957-CH50-SW1) das diretrizes de [interface humana do os X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)da Apple. 

[![](standard-controls-images/level01.png "Controles de indicadores de exemplo")](standard-controls-images/level01.png#lightbox)

Há duas maneiras de controlar quando um controle de indicador tem interação do usuário, expondo-o como uma **ação** ou uma **tomada** e anexando um **delegado** ao `Activated` evento. Por exemplo:

```csharp
LevelIndicator.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Level: {0:###}",LevelIndicator.DoubleValue);
};
```

Para ler ou definir o valor do controle de indicador, use a `DoubleValue` propriedade. Por exemplo:

```csharp
FeedbackLabel.StringValue = string.Format("Rating: {0:###}",Rating.DoubleValue);
```

Os indicadores de progresso indeterminado e assíncrono devem ser animados quando exibidos. Use o `StartAnimation` método para iniciar a animação quando elas forem exibidas. Por exemplo:

```csharp
Indeterminate.StartAnimation (this);
AsyncProgress.StartAnimation (this);
```

Chamar o `StopAnimation` método irá parar a animação.

<a name="Working_with_Text_Controls" />

## <a name="working-with-text-controls"></a>Trabalhando com controles de texto

O AppKit fornece vários tipos de controles de texto que podem ser usados no design da interface do usuário. Para obter mais informações, consulte a seção [controles de texto](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsText.html#//apple_ref/doc/uid/20000957-CH51-SW1) das diretrizes de [interface humana do os X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)da Apple. 

[![](standard-controls-images/text01.png "Controles de texto de exemplo")](standard-controls-images/text01.png#lightbox)

Para campos de texto`NSTextField`(), os seguintes eventos podem ser usados para rastrear a interação do usuário:

- **Alterado** -é acionado sempre que o usuário altera o valor do campo. Por exemplo, em cada caractere digitado.
- **EditingBegan** -é acionado quando o usuário seleciona o campo para edição.
- **EditingEnded** – quando o usuário pressiona a tecla Enter no campo ou deixa o campo.

Use a `StringValue` propriedade para ler ou definir o valor do campo. Por exemplo:

```csharp
FeedbackLabel.StringValue = string.Format("User ID: {0}",UserField.StringValue);
```

Para campos que exibem ou editam valores numéricos, você pode `IntValue` usar a propriedade. Por exemplo:

```csharp
FeedbackLabel.StringValue = string.Format("Number: {0}",NumberField.IntValue);
```

Um `NSTextView` fornece uma área de edição e exibição de texto completo com formatação interna. Como um `NSTextField`, use a `StringValue` propriedade para ler ou definir o valor da área.

Para obter um exemplo de um exemplo complexo de como trabalhar com exibições de texto em um aplicativo Xamarin. Mac, consulte o [aplicativo de exemplo SourceWriter](https://docs.microsoft.com/samples/xamarin/mac-samples/sourcewriter). SourceWriter é um editor de código-fonte simples que dá suporte para a conclusão de código e realce de sintaxe simples.

O código do SourceWriter foi totalmente comentado e, quando disponível, foram fornecidos links de tecnologias-chave ou métodos para informações relevantes na Documentação de Guias do Xamarin.Mac.

<a name="Working_with_Content_Views" />

## <a name="working-with-content-views"></a>Trabalhando com exibições de conteúdo

O AppKit fornece vários tipos de exibições de conteúdo que podem ser usados no design da interface do usuário. Para obter mais informações, consulte a seção [exibições de conteúdo](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsView.html#//apple_ref/doc/uid/20000957-CH52-SW1) das [diretrizes de interface humana do os X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)da Apple.

[![](standard-controls-images/content01.png "Um exemplo de exibição de conteúdo")](standard-controls-images/content01.png#lightbox)

<a name="Popovers" />

### <a name="popovers"></a>Popovers

Um popover é um elemento de interface do usuário transitório que fornece funcionalidade diretamente relacionada a um controle específico ou a uma área na tela. Um popover flutua acima da janela que contém o controle ou a área à qual ele está relacionado, e sua borda inclui uma seta para indicar o ponto do qual surgiu.

Para criar um popover, faça o seguinte:

1. Abra o `.storyboard` arquivo da janela à qual você deseja adicionar um popover clicando duas vezes nele no **Gerenciador de soluções**
2. Arraste um **controlador de exibição** do **Inspetor de biblioteca** para o **Editor de interface**: 

    [![](standard-controls-images/content02.png "Selecionando um controlador de exibição da biblioteca")](standard-controls-images/content02.png#lightbox)
3. Defina o tamanho e o layout da **exibição personalizada**: 

    [![](standard-controls-images/content04.png "Editando o layout")](standard-controls-images/content04.png#lightbox)
4. Clique no botão de controle e arraste da fonte do Popup para o **controlador de exibição**: 

    [![](standard-controls-images/content05.png "Arrastando para criar um transição")](standard-controls-images/content05.png#lightbox)
5. Selecione **popover** no menu pop-up: 

    [![](standard-controls-images/content06.png "Configurando o tipo transição")](standard-controls-images/content06.png#lightbox)
6. Salve as alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

<a name="Tab_Views" />

### <a name="tab-views"></a>Exibições de guias

As exibições de guia consistem em uma lista de guias (que é semelhante a um controle segmentado) combinada com um conjunto de exibições que são chamadas de _painéis_. Quando o usuário seleciona uma nova guia, o painel que é anexado a ela será exibido. Cada painel contém seu próprio conjunto de controles.

Ao trabalhar com uma exibição de guia no Interface Builder do Xcode, use o **Inspetor de atributo** para definir o número de guias:

[![](standard-controls-images/content08.png "Editando o número de guias")](standard-controls-images/content08.png#lightbox)

Selecione cada guia na **hierarquia de interface** para definir seu **título** e adicionar elementos de interface do usuário ao seu **painel**:

[![](standard-controls-images/content09.png "Editando as guias no Xcode")](standard-controls-images/content09.png#lightbox)

<a name="Data_Binding_AppKit_Controls" />

## <a name="data-binding-appkit-controls"></a>Controles AppKit de vinculação de dados

Usando a codificação de valor chave e técnicas de vinculação de dados em seu aplicativo Xamarin. Mac, você pode diminuir muito a quantidade de código que você precisa escrever e manter para popular e trabalhar com elementos de interface do usuário. Você também tem o benefício de desacoplar ainda mais os dados de backup (_modelo de dados_) da sua interface do usuário de front-end (_Model-View-Controller_), levando a um design de aplicativo mais fácil de manter e mais flexível.

A KVC (codificação de valor chave) é um mecanismo para acessar as propriedades de um objeto indiretamente, usando chaves (cadeias de caracteres especialmente formatadas) para identificar Propriedades em vez de acessá-las por meio de variáveis de instância ou métodos de acessador (`get/set`). Ao implementar acessadores compatíveis com codificação de chave-valor em seu aplicativo Xamarin. Mac, você obterá acesso a outros recursos do macOS, como KVO (chave-valor, observação), vinculação de dados, dados principais, associações de Cocoa e scripts.

Para obter mais informações, consulte a seção [vinculação de dados simples](~/mac/app-fundamentals/databinding.md#Simple_Data_Binding) de nossa [vinculação de dados e documentação de codificação de chave-valor](~/mac/app-fundamentals/databinding.md) .

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo fez uma visão detalhada do trabalho com os controles padrão do AppKit, como botões, rótulos, campos de texto, caixas de seleção e controles segmentados em um aplicativo Xamarin. Mac. Ele abordou adicioná-los a um design de interface do usuário na Interface Builder do Xcode, Expo-los a código por meio de saídas e ações e C# trabalhar com controles AppKit no código.

## <a name="related-links"></a>Links relacionados

- [MacControls (exemplo)](https://docs.microsoft.com/samples/xamarin/mac-samples/maccontrols)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Windows](~/mac/user-interface/window.md)
- [Associação de dados e a codificação de chave-valor](~/mac/app-fundamentals/databinding.md)
- [Diretrizes de interface humana do OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Sobre controles e exibições](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1)
