---
title: Modos de exibição de tabela no xamarin. Mac
description: Este artigo aborda o trabalho com modos de exibição de tabela em um aplicativo xamarin. Mac. Ele descreve criando modos de exibição de tabela no Xcode e Interface Builder e interagir com eles no código.
ms.prod: xamarin
ms.assetid: 3B55B858-4769-4331-966A-7F53B3B7C720
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 9a8c55c8b4ff3fbd515aad8bf45c52a0b549af9f
ms.sourcegitcommit: b60a37587aad8a0bfa8a522d88d22fa672002443
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285593"
---
# <a name="table-views-in-xamarinmac"></a>Modos de exibição de tabela no xamarin. Mac

_Este artigo aborda o trabalho com modos de exibição de tabela em um aplicativo xamarin. Mac. Ele descreve criando modos de exibição de tabela no Xcode e Interface Builder e interagir com eles no código._

Ao trabalhar com c# e .NET em um aplicativo xamarin. Mac, você tem acesso à mesma tabela que exibe um desenvolvedor que trabalha em *Objective-C* e *Xcode* faz. Como o xamarin. Mac se integra diretamente com Xcode, você pode usar do Xcode _construtor de Interface_ para criar e manter seus modos de exibição de tabela (ou, opcionalmente, criá-los diretamente em código c#).

Uma exibição de tabela exibe dados em um formato de tabela que contém uma ou mais colunas de informações em várias linhas. Com base no tipo de exibição de tabela que está sendo criado, o usuário pode classificar por coluna, reorganizar as colunas, adicionar colunas, remover colunas ou editar os dados contidos dentro da tabela.

[![](table-view-images/intro01.png "Um exemplo de tabela")](table-view-images/intro01.png#lightbox)

Neste artigo, abordaremos os fundamentos de trabalhar com modos de exibição de tabela em um aplicativo xamarin. Mac. É altamente recomendável que você trabalhe por meio de [Hello, Mac](~/mac/get-started/hello-mac.md) pela primeira vez, especificamente o artigo a [Introdução ao Interface Builder e Xcode](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [saídas e ações](~/mac/get-started/hello-mac.md#outlets-and-actions) seções, como ela aborda os principais conceitos e técnicas que usaremos neste artigo.

Talvez você queira dar uma olhada o [classes expondo c# / métodos para Objective-C](~/mac/internals/how-it-works.md) seção o [recursos internos de xamarin. Mac](~/mac/internals/how-it-works.md) documentar Além disso, ele explica o `Register` e `Export` comandos usado para transmissão-up suas classes de c# a objetos de Objective-C e elementos de interface do usuário.

<a name="Introduction_to_Table_Views" />

## <a name="introduction-to-table-views"></a>Introdução aos modos de exibição de tabela

Uma exibição de tabela exibe dados em um formato de tabela que contém uma ou mais colunas de informações em várias linhas. Modos de exibição de tabela são exibidos dentro de modos de exibição de rolagem (`NSScrollView`) e a partir do macOS 10.7, você pode usar qualquer `NSView` em vez de células (`NSCell`) para exibir linhas e colunas. Dito isso, você ainda pode usar `NSCell` no entanto, você geralmente vai subclasse `NSTableCellView` e criar seus personalizadas linhas e colunas.

Uma exibição de tabela não armazena seus próprios dados, em vez disso, ele se baseia em uma fonte de dados (`NSTableViewDataSource`) para fornecer linhas e colunas necessárias em uma base conforme necessário.

Comportamento de exibição de tabela pode ser personalizado, fornecendo uma subclasse do delegado de modo de exibição de tabela (`NSTableViewDelegate`) para dar suporte ao gerenciamento de coluna de tabela, digite para selecionar a funcionalidade, seleção de linha e de edição, acompanhamento personalizado e modos de exibição personalizados para colunas individuais e linhas.

Ao criar modos de exibição de tabela, a Apple sugere o seguinte:

* Permitir que o usuário classificar a tabela clicando em um cabeçalho de coluna.
* Crie cabeçalhos de coluna são substantivos e frases substantivadas curto que descrevem os dados que está sendo mostrados nessa coluna.

Para obter mais informações, consulte o [modos de exibição de conteúdo](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsView.html#//apple_ref/doc/uid/20000957-CH52-SW1) seção da Apple [diretrizes de Interface humana dos X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/).

<a name="Creating-and-Maintaining-Table-Views-in-Xcode" />

## <a name="creating-and-maintaining-table-views-in-xcode"></a>Criar e manter modos de exibição de tabela no Xcode

Quando você cria um novo aplicativo xamarin. Mac Cocoa, você obtém uma janela de padrão em branco, por padrão. Esse windows é definido em um `.storyboard` arquivo incluído automaticamente no projeto. Para editar seu design do windows, nos **Gerenciador de soluções**, clique duas vezes o `Main.storyboard` arquivo:

[![](table-view-images/edit01.png "Selecionando o storyboard principal")](table-view-images/edit01.png#lightbox)

Isso abrirá o design de janela no Interface Builder do Xcode:

[![](table-view-images/edit02.png "Editando a interface do usuário no Xcode")](table-view-images/edit02.png#lightbox)

Tipo de `table` para o **Inspetor de biblioteca** caixa de pesquisa para torná-lo mais fácil de encontrar os controles de exibição de tabela:

[![](table-view-images/edit03.png "Selecionar uma exibição de tabela da biblioteca")](table-view-images/edit03.png#lightbox)

Arraste uma exibição de tabela para o controlador de exibição na **Editor de Interface**, torná-lo a preencher a área de conteúdo do controlador de exibição e defina-a onde ele é reduzido e cresce com a janela na **Editor de restrição de**:

[![](table-view-images/edit04.png "Restrições de edição")](table-view-images/edit04.png#lightbox)

Selecione a exibição de tabela na **hierarquia de Interface** e as propriedades a seguir estão disponíveis na **Inspetor de atributo**:

[![](table-view-images/edit05.png "O Inspetor de atributos")](table-view-images/edit05.png#lightbox)

- **Modo de conteúdo** -permite que você use qualquer um dos modos de exibição (`NSView`) ou células (`NSCell`) para exibir os dados nas linhas e colunas. Começando com o macOS 10.7, você deve usar modos de exibição.
- **Flutua agrupar linhas** - se `true`, o modo de exibição de tabela desenhará células agrupadas como se eles são flutuantes.
- **Colunas** -define o número de colunas exibidas.
- **Cabeçalhos** - se `true`, as colunas têm cabeçalhos.
- **Reordenação** - se `true`, o usuário será capaz de arraste reorganizar as colunas na tabela.
- **Redimensionamento** - se `true`, o usuário poderá arrastar cabeçalhos de coluna para redimensionar colunas.
- **Dimensionamento de coluna** -controla como a tabela serão automaticamente as colunas de tamanho.
- **Realçar** -controles, o tipo de realce a tabela usa quando uma célula é selecionada.
- **Alternar linhas** - se `true`, em hipótese alguma outra linha terá uma cor de plano de fundo diferente.
- **Grade horizontal** -seleciona o tipo de borda desenhada entre as células horizontalmente.
- **Grade vertical** -seleciona o tipo de borda desenhada entre as células verticalmente.
- **Cor da grade** -define a cor de borda da célula.
- **Plano de fundo** -define a cor de plano de fundo da célula.
- **Seleção** -permitem que você controle como o usuário pode selecionar células na tabela, como:
    - **Vários** - se `true`, o usuário pode selecionar várias linhas e colunas.
    - **Coluna** - se `true`, o usuário pode selecionar colunas.
    - **Tipo, selecione** - se `true`, o usuário pode digitar um caractere para selecionar uma linha.
    - **Vazio** - se `true`, o usuário não é necessário selecionar uma linha ou coluna, a tabela permite para nenhuma seleção em todos os.
- **Salvamento automático** -o nome que o formato de tabelas é automaticamente salvar em.
- **Informações de coluna** - se `true`, a ordem e a largura das colunas serão salvas automaticamente.
- **Quebras de linha** – selecione como a célula lida com quebras de linha.
- **Trunca a última linha visível** - se `true`, a célula será truncada no dados podem não se ajustarem dentro dos limites de TI.

> [!IMPORTANT]
> A menos que você está mantendo um aplicativo xamarin. Mac herdado, `NSView` modos de exibição de tabela com base deve ser usados pela `NSCell` com base em exibições de tabela. `NSCell` é considerado herdados e não podem ter suporte no futuro.

Selecione uma coluna da tabela a **hierarquia de Interface** e as propriedades a seguir estão disponíveis na **Inspetor de atributo**:

[![](table-view-images/edit06.png "O Inspetor de atributos")](table-view-images/edit06.png#lightbox)

- **Título** -define o título da coluna.
- **Alinhamento** -definir o alinhamento do texto dentro das células.
- **Fonte do título** -seleciona a fonte para o texto do cabeçalho da célula.
- **Chave de classificação** -é a chave usada para classificar os dados na coluna. Deixe em branco se o usuário não pode classificar essa coluna.
- **Seletor** -é o **ação** usado para executar a classificação. Deixe em branco se o usuário não pode classificar essa coluna.
- **Ordem** -é a ordem de classificação para os dados de colunas.
- **Redimensionamento** -seleciona o tipo de redimensionamento para a coluna.
- **Editável** - se `true`, o usuário pode editar as células em uma tabela de célula com base.
- **Oculto** - se `true`, a coluna está oculta.

Você também pode redimensionar a coluna, arrastando sua alça (centralizado verticalmente no lado direito da coluna) à esquerda ou à direita.

Vamos selecionar a cada coluna na nossa exibição de tabela e dê a primeira coluna uma **Title** dos `Product` e o segundo `Details`.

Selecione um modo de exibição de célula de tabela (`NSTableViewCell`) na **hierarquia da Interface** e as propriedades a seguir estão disponíveis no **Inspetor de atributo**:

[![](table-view-images/edit07.png "O Inspetor de atributos")](table-view-images/edit07.png#lightbox)

Essas são todas as propriedades de uma exibição padrão. Você também tem a opção de redimensionar as linhas para essa coluna aqui.

Selecione uma célula de exibição de tabela (por padrão, esse é um `NSTextField`) na **hierarquia da Interface** e as propriedades a seguir estão disponíveis no **Inspetor de atributo**:

[![](table-view-images/edit08.png "O Inspetor de atributos")](table-view-images/edit08.png#lightbox)

Você terá todas as propriedades de um campo de texto padrão para definir aqui. Por padrão, um campo de texto padrão é usado para exibir dados de uma célula em uma coluna.

Selecione um modo de exibição de célula de tabela (`NSTableFieldCell`) na **hierarquia da Interface** e as propriedades a seguir estão disponíveis no **Inspetor de atributo**:

[![](table-view-images/edit09.png "O Inspetor de atributos")](table-view-images/edit09.png#lightbox)

As configurações mais importantes aqui são:

- **Layout** – selecione como as células nesta coluna são apresentadas.
- **Usa o modo de linha única** - se `true`, a célula está limitada a uma única linha.
- **Largura de Layout de tempo de execução do primeiro** - se `true`, a célula preferirá a largura definida para ele (manualmente ou automaticamente) quando ele é exibido na primeira vez em que o aplicativo é executado.
- **Ação** -controla quando a edição **ação** é enviada para a célula.
- **Comportamento** -define se uma célula é selecionável ou editável.
- **Rich Text** - se `true`, a célula pode exibir o texto formatado e com estilo.
- **Desfazer** - se `true`, a célula assume a responsabilidade para ele é desfazer o comportamento.

Selecione o modo de exibição de célula de tabela (`NSTableFieldCell`) na parte inferior de uma coluna da tabela o **hierarquia da Interface**:

[![](table-view-images/edit10.png "Selecionar a exibição de célula de tabela")](table-view-images/edit10.png#lightbox)

Isso permite que você edite a exibição de célula da tabela usada como base _padrão_ para todas as células criadas para a coluna especificada.

<a name="Adding_Actions_and_Outlets" />

### <a name="adding-actions-and-outlets"></a>Adicionar ações e as saídas

Assim como qualquer outro controle de interface do usuário do Cocoa, é preciso expor nossa exibição de tabela e colunas e células de código c# usando **ações** e **saídas** (com base na funcionalidade exigida).

O processo é o mesmo para qualquer elemento de exibição de tabela que desejemos expor:

1. Alterne para o **Editor assistente** e certifique-se de que o `ViewController.h` arquivo é selecionado: 

    [![](table-view-images/edit11.png "O Editor Assistente")](table-view-images/edit11.png#lightbox)
2. Selecione o modo de exibição de tabela das **hierarquia de Interface**, CTRL + clique e arraste para o `ViewController.h` arquivo.
3. Criar uma **tomada** para o modo de exibição de tabela chamado `ProductTable`: 

    [![](table-view-images/edit13.png "Configurando uma saída")](table-view-images/edit13.png#lightbox)
4. Crie **tomadas** para as colunas de tabelas também chamado `ProductColumn` e `DetailsColumn`: 

    [![](table-view-images/edit14.png "Configurando uma saída")](table-view-images/edit14.png#lightbox)
5. Salvar as alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Em seguida, vamos escrever a exibição de código alguns dados para a tabela quando o aplicativo é executado.

<a name="Populating_the_Table_View" />

## <a name="populating-the-table-view"></a>Preencher a exibição de tabela

Com nosso modo de exibição de tabela criado no construtor de Interface e expostos por meio de um **tomada**, em seguida, precisamos criar o código c# para preenchê-lo.

Primeiro, vamos criar um novo `Product` classe para manter as informações para as linhas individuais. No **Gerenciador de soluções**, clique com botão direito no projeto e selecione **Add** > **novo arquivo...** Selecione **gerais** > **classe vazia**, digite `Product` para o **nome** e clique no **New** botão:

[![](table-view-images/populate01.png "Criando uma classe vazia")](table-view-images/populate01.png#lightbox)

Verifique o `Product.cs` arquivo são semelhantes ao seguinte:

```csharp
using System;

namespace MacTables
{
    public class Product
    {
        #region Computed Propoperties
        public string Title { get; set;} = "";
        public string Description { get; set;} = "";
        #endregion

        #region Constructors
        public Product ()
        {
        }

        public Product (string title, string description)
        {
            this.Title = title;
            this.Description = description;
        }
        #endregion
    }
}

```

Em seguida, precisamos criar uma subclasse de `NSTableDataSource` para fornecer os dados para nossa tabela conforme necessário. No **Gerenciador de soluções**, clique com botão direito no projeto e selecione **Add** > **novo arquivo...** Selecione **gerais** > **classe vazia**, digite `ProductTableDataSource` para o **nome** e clique no **New** botão.

Editar o `ProductTableDataSource.cs` de arquivo e torná-lo semelhante ao seguinte:

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacTables
{
    public class ProductTableDataSource : NSTableViewDataSource
    {
        #region Public Variables
        public List<Product> Products = new List<Product>();
        #endregion

        #region Constructors
        public ProductTableDataSource ()
        {
        }
        #endregion

        #region Override Methods
        public override nint GetRowCount (NSTableView tableView)
        {
            return Products.Count;
        }
        #endregion
    }
}

```

Essa classe tem armazenamento para itens de exibição de nossa tabela e substitui o `GetRowCount` para retornar o número de linhas na tabela.

Por fim, precisamos criar uma subclasse de `NSTableDelegate` para fornecer o comportamento para nossa tabela. No **Gerenciador de soluções**, clique com botão direito no projeto e selecione **Add** > **novo arquivo...** Selecione **gerais** > **classe vazia**, digite `ProductTableDelegate` para o **nome** e clique no **New** botão.

Editar o `ProductTableDelegate.cs` de arquivo e torná-lo semelhante ao seguinte:

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacTables
{
    public class ProductTableDelegate: NSTableViewDelegate
    {
        #region Constants 
        private const string CellIdentifier = "ProdCell";
        #endregion

        #region Private Variables
        private ProductTableDataSource DataSource;
        #endregion

        #region Constructors
        public ProductTableDelegate (ProductTableDataSource datasource)
        {
            this.DataSource = datasource;
        }
        #endregion

        #region Override Methods
        public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
        {
            // This pattern allows you reuse existing views when they are no-longer in use.
            // If the returned view is null, you instance up a new view
            // If a non-null view is returned, you modify it enough to reflect the new data
            NSTextField view = (NSTextField)tableView.MakeView (CellIdentifier, this);
            if (view == null) {
                view = new NSTextField ();
                view.Identifier = CellIdentifier;
                view.BackgroundColor = NSColor.Clear;
                view.Bordered = false;
                view.Selectable = false;
                view.Editable = false;
            }

            // Setup view based on the column selected
            switch (tableColumn.Title) {
            case "Product":
                view.StringValue = DataSource.Products [(int)row].Title;
                break;
            case "Details":
                view.StringValue = DataSource.Products [(int)row].Description;
                break;
            }

            return view;
        }
        #endregion
    }
}
```

Quando criamos uma instância das `ProductTableDelegate`, também passamos em uma instância da `ProductTableDataSource` que fornece os dados da tabela. O `GetViewForItem` método é responsável por retornar uma exibição (dados) para exibir na célula de uma linha e coluna determinada. Se possível, uma exibição existente será reutilizada para exibi-la, se não deve ser criado um novo modo de exibição.

Para popular a tabela, vamos editar o `ViewController.cs` do arquivo e verifique o `AwakeFromNib` método são semelhantes ao seguinte:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Create the Product Table Data Source and populate it
    var DataSource = new ProductTableDataSource ();
    DataSource.Products.Add (new Product ("Xamarin.iOS", "Allows you to develop native iOS Applications in C#"));
    DataSource.Products.Add (new Product ("Xamarin.Android", "Allows you to develop native Android Applications in C#"));
    DataSource.Products.Add (new Product ("Xamarin.Mac", "Allows you to develop Mac native Applications in C#"));

    // Populate the Product Table
    ProductTable.DataSource = DataSource;
    ProductTable.Delegate = new ProductTableDelegate (DataSource);
}
```

Se executarmos o aplicativo, o seguinte é exibido:

[![](table-view-images/populate02.png "Execução de um aplicativo de exemplo")](table-view-images/populate02.png#lightbox)

<a name="Sorting_by_Column" />

## <a name="sorting-by-column"></a>Classificar por coluna

Vamos permitir que o usuário classificar os dados na tabela clicando em um cabeçalho de coluna. Primeiro, clique duas vezes o `Main.storyboard` arquivo para abri-lo para edição no Interface Builder. Selecione o `Product` coluna, digite `Title` para o **chave de classificação**, `compare:` para o **seletor** e selecione `Ascending` para o **ordem**:

[![](table-view-images/sort01.png "Definindo a chave de classificação")](table-view-images/sort01.png#lightbox)

Selecione o `Details` coluna, digite `Description` para o **chave de classificação**, `compare:` para o **seletor** e selecione `Ascending` para o **ordem**:

[![](table-view-images/sort02.png "Definindo a chave de classificação")](table-view-images/sort02.png#lightbox)

Salve suas alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Agora vamos editar o `ProductTableDataSource.cs` arquivo e adicione os seguintes métodos:

```csharp
public void Sort(string key, bool ascending) {

    // Take action based on key
    switch (key) {
    case "Title":
        if (ascending) {
            Products.Sort ((x, y) => x.Title.CompareTo (y.Title));
        } else {
            Products.Sort ((x, y) => -1 * x.Title.CompareTo (y.Title));
        }
        break;
    case "Description":
        if (ascending) {
            Products.Sort ((x, y) => x.Description.CompareTo (y.Description));
        } else {
            Products.Sort ((x, y) => -1 * x.Description.CompareTo (y.Description));
        }
        break;
    }

}

public override void SortDescriptorsChanged (NSTableView tableView, NSSortDescriptor[] oldDescriptors)
{
    // Sort the data
    if (oldDescriptors.Length > 0) {
        // Update sort
        Sort (oldDescriptors [0].Key, oldDescriptors [0].Ascending);
    } else {
        // Grab current descriptors and update sort
        NSSortDescriptor[] tbSort = tableView.SortDescriptors; 
        Sort (tbSort[0].Key, tbSort[0].Ascending); 
    }
            
    // Refresh table
    tableView.ReloadData ();
}
```

O `Sort` método nos permitem classificar os dados na fonte de dados com base em um determinado `Product` campo de classe em uma ordem crescente ou decrescente. Substituído `SortDescriptorsChanged` método será chamado sempre que o uso de clicar em um título de coluna. Ele será passado a **chave** valor que definimos no Interface Builder e a ordem de classificação para aquela coluna.

Se executar o aplicativo e clique nos cabeçalhos de coluna, as linhas serão classificadas por essa coluna:

[![](table-view-images/sort03.png "Um execução do aplicativo de exemplo")](table-view-images/sort03.png#lightbox)

<a name="Row_Selection" />

## <a name="row-selection"></a>Seleção de linha

Se você deseja permitir que o usuário seleciona uma única linha, clique duas vezes o `Main.storyboard` arquivo para abri-lo para edição no Interface Builder. Selecione a exibição de tabela na **hierarquia de Interface** e desmarque o **vários** caixa de seleção no **Inspetor de atributo**:

[![](table-view-images/select01.png "O Inspetor de atributos")](table-view-images/select01.png#lightbox)

Salve suas alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.


Em seguida, edite o `ProductTableDelegate.cs` arquivo e adicione o seguinte método:

```csharp
public override bool ShouldSelectRow (NSTableView tableView, nint row)
{
    return true;
}
```

Isso permitirá que o usuário selecione qualquer linha única na exibição de tabela. Retornar `false` para o `ShouldSelectRow` para qualquer linha que você não deseja que o usuário seja capaz de selecionar ou `false` para todas as linhas se não quiser que o usuário seja capaz de selecionar todas as linhas.

O modo de exibição de tabela (`NSTableView`) contém os seguintes métodos para trabalhar com a seleção de linha:

- `DeselectRow(nint)` -Anula a seleção de determinada linha na tabela.
- `SelectRow(nint,bool)` – Seleciona a linha determinada. Passar `false` para o segundo parâmetro para selecionar apenas uma linha por vez.
- `SelectedRow` -Retorna a linha atual selecionada na tabela.
- `IsRowSelected(nint)` -Retorna `true` se determinada linha está selecionada.

<a name="Multiple_Row_Selection" />

## <a name="multiple-row-selection"></a>Seleção de várias linhas

Se você deseja permitir que o usuário selecionar uma várias linhas, clique duas vezes o `Main.storyboard` arquivo para abri-lo para edição no Interface Builder. Selecione a exibição de tabela na **hierarquia de Interface** e verifique o **vários** caixa de seleção no **Inspetor de atributo**:

[![](table-view-images/select02.png "O Inspetor de atributos")](table-view-images/select02.png#lightbox)

Salve suas alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.


Em seguida, edite o `ProductTableDelegate.cs` arquivo e adicione o seguinte método:

```csharp
public override bool ShouldSelectRow (NSTableView tableView, nint row)
{
    return true;
}
```

Isso permitirá que o usuário selecione qualquer linha única na exibição de tabela. Retornar `false` para o `ShouldSelectRow` para qualquer linha que você não deseja que o usuário seja capaz de selecionar ou `false` para todas as linhas se não quiser que o usuário seja capaz de selecionar todas as linhas.

O modo de exibição de tabela (`NSTableView`) contém os seguintes métodos para trabalhar com a seleção de linha:

- `DeselectAll(NSObject)` -Anula a seleção de todas as linhas na tabela. Use `this` para o primeiro parâmetro enviar no objeto de fazer a seleção. 
- `DeselectRow(nint)` -Anula a seleção de determinada linha na tabela.
- `SelectAll(NSobject)` – Seleciona todas as linhas na tabela. Use `this` para o primeiro parâmetro enviar no objeto de fazer a seleção.
- `SelectRow(nint,bool)` – Seleciona a linha determinada. Passar `false` para o segundo parâmetro, desmarque a seleção e selecione apenas uma única linha, passar `true` para estender a seleção e incluir essa linha.
- `SelectRows(NSIndexSet,bool)` – Seleciona o determinado conjunto de linhas. Passar `false` para o segundo parâmetro, desmarque a seleção e selecione somente um essas linhas, passar `true` para estender a seleção e incluir essas linhas.
- `SelectedRow` -Retorna a linha atual selecionada na tabela.
- `SelectedRows` -Retorna um `NSIndexSet` que contém os índices das linhas selecionadas.
- `SelectedRowCount` -Retorna o número de linhas selecionadas.
- `IsRowSelected(nint)` -Retorna `true` se determinada linha está selecionada.

<a name="Type_to_Select_Row" />

## <a name="type-to-select-row"></a>Tipo para selecionar linha

Se você quiser permitir que o usuário digita um caractere com o modo de exibição de tabela selecionado e selecione a primeira linha que tem esse caractere, clique duas vezes o `Main.storyboard` arquivo para abri-lo para edição no Interface Builder. Selecione a exibição de tabela na **hierarquia de Interface** e verifique o **Selecionar tipo** caixa de seleção no **Inspetor de atributo**:

[![](table-view-images/type01.png "Definindo o tipo de seleção")](table-view-images/type01.png#lightbox)

Salve suas alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Agora vamos editar o `ProductTableDelegate.cs` arquivo e adicione o seguinte método:

```csharp
public override nint GetNextTypeSelectMatch (NSTableView tableView, nint startRow, nint endRow, string searchString)
{
    nint row = 0;
    foreach(Product product in DataSource.Products) {
        if (product.Title.Contains(searchString)) return row;

        // Increment row counter
        ++row;
    }

    // If not found select the first row
    return 0;
}
```

O `GetNextTypeSelectMatch` leva a determinada `searchString` e retorna a linha da primeira `Product` que contém essa cadeia de caracteres do `Title`.

Se executar o aplicativo e um caractere de tipo, uma linha está selecionada:

[![](table-view-images/type02.png "Execução de um aplicativo de exemplo")](table-view-images/type02.png#lightbox)

<a name="Reordering_Columns" />

## <a name="reordering-columns"></a>Reordenar colunas

Se você quiser permitir que o usuário arraste reordenar colunas na exibição de tabela, clique duas vezes o `Main.storyboard` arquivo para abri-lo para edição no Interface Builder. Selecione a exibição de tabela na **hierarquia de Interface** e verifique o **reordenação** caixa de seleção no **Inspetor de atributo**:

[![](table-view-images/reorder01.png "O Inspetor de atributos")](table-view-images/reorder01.png#lightbox)

Se podemos fornecer um valor para o **Autosave** propriedade e verifique se o **informações de coluna** campo, qualquer alteração que podemos fazer layout da tabela serão salvas automaticamente para nós e restaurado na próxima vez que o aplicativo é executado.

Salve suas alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Agora vamos editar o `ProductTableDelegate.cs` arquivo e adicione o seguinte método:

```csharp
public override bool ShouldReorder (NSTableView tableView, nint columnIndex, nint newColumnIndex)
{
    return true;
}
```

O `ShouldReorder` método deverá retornar `true` para qualquer coluna que ele deseja conceder permissão para ser reordenada, arrastar para o `newColumnIndex`, caso contrário retorne `false`;

Se executarmos o aplicativo, é possível arrastar cabeçalhos de coluna em torno de reordenar nossas colunas:

[![](table-view-images/reorder02.png "Um exemplo de como as colunas reordenadas")](table-view-images/reorder02.png#lightbox)

<a name="Editing_Cells" />

## <a name="editing-cells"></a>Edição de células

Se você quiser permitir que o usuário edite os valores de uma determinada célula, edite o `ProductTableDelegate.cs` do arquivo e altere o `GetViewForItem` método da seguinte maneira:

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{
    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTextField view = (NSTextField)tableView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTextField ();
        view.Identifier = tableColumn.Title;
        view.BackgroundColor = NSColor.Clear;
        view.Bordered = false;
        view.Selectable = false;
        view.Editable = true;

        view.EditingEnded += (sender, e) => {
                    
            // Take action based on type
            switch(view.Identifier) {
            case "Product":
                DataSource.Products [(int)view.Tag].Title = view.StringValue;
                break;
            case "Details":
                DataSource.Products [(int)view.Tag].Description = view.StringValue;
                break; 
            }
        };
    }

    // Tag view
    view.Tag = row;

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.StringValue = DataSource.Products [(int)row].Title;
        break;
    case "Details":
        view.StringValue = DataSource.Products [(int)row].Description;
        break;
    }

    return view;
}
```

Agora se executarmos o aplicativo, o usuário pode editar as células na exibição de tabela:

[![](table-view-images/editing01.png "Um exemplo de uma célula de edição")](table-view-images/editing01.png#lightbox)

<a name="Using_Images_in_Table_Views" />

## <a name="using-images-in-table-views"></a>Usando imagens em modos de exibição de tabela

Para incluir uma imagem como parte da célula em uma `NSTableView`, você precisará alterar como os dados são retornados pela exibição de tabela `NSTableViewDelegate's` `GetViewForItem` método a ser usado um `NSTableCellView` em vez de típico `NSTextField`. Por exemplo:

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)tableView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTableCellView ();
        if (tableColumn.Title == "Product") {
            view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
            view.AddSubview (view.ImageView);
            view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
        } else {
            view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
        }
        view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
        view.AddSubview (view.TextField);
        view.Identifier = tableColumn.Title;
        view.TextField.BackgroundColor = NSColor.Clear;
        view.TextField.Bordered = false;
        view.TextField.Selectable = false;
        view.TextField.Editable = true;

        view.TextField.EditingEnded += (sender, e) => {

            // Take action based on type
            switch(view.Identifier) {
            case "Product":
                DataSource.Products [(int)view.TextField.Tag].Title = view.TextField.StringValue;
                break;
            case "Details":
                DataSource.Products [(int)view.TextField.Tag].Description = view.TextField.StringValue;
                break; 
            }
        };
    }

    // Tag view
    view.TextField.Tag = row;

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed ("tags.png");
        view.TextField.StringValue = DataSource.Products [(int)row].Title;
        break;
    case "Details":
        view.TextField.StringValue = DataSource.Products [(int)row].Description;
        break;
    }

    return view;
}
```

Para obter mais informações, consulte o [usando imagens com modos de exibição de tabela](~/mac/app-fundamentals/image.md) seção do nosso [trabalhando com imagem](~/mac/app-fundamentals/image.md) documentação.

<a name="Adding-a-Delete-Button-to-a-Row" />

## <a name="adding-a-delete-button-to-a-row"></a>Adicionando um botão Excluir em uma linha

Com base nos requisitos do seu aplicativo, pode haver ocasiões em que você precisa fornecer um botão de ação para cada linha na tabela. Como um exemplo disso, vamos expandir o exemplo de exibição de tabela criado acima para incluir um **excluir** botão em cada linha.

Primeiro, edite o `Main.storyboard` no Interface Builder do Xcode, selecione o modo de exibição de tabela e aumentar o número de colunas para três (3). Em seguida, alterar o **Title** da nova coluna para `Action`:

[![](table-view-images/delete01.png "Editar o nome da coluna")](table-view-images/delete01.png#lightbox)

Salve as alterações ao Storyboard e retorne ao Visual Studio para Mac sincronizar as alterações.

Em seguida, edite o `ViewController.cs` arquivo e adicione o seguinte método público:

```csharp
public void ReloadTable ()
{
    ProductTable.ReloadData ();
}
```

No mesmo arquivo, modifique a criação de um novo delegado de modo de exibição de tabela dentro de `ViewDidLoad` método da seguinte maneira:

```csharp
// Populate the Product Table
ProductTable.DataSource = DataSource;
ProductTable.Delegate = new ProductTableDelegate (this, DataSource);
```

Agora, edite o `ProductTableDelegate.cs` arquivo incluem uma conexão privada para o controlador de exibição e aproveitar o controlador como um parâmetro ao criar uma nova instância do delegado:

```csharp
#region Private Variables
private ProductTableDataSource DataSource;
private ViewController Controller;
#endregion

#region Constructors
public ProductTableDelegate (ViewController controller, ProductTableDataSource datasource)
{
    this.Controller = controller;
    this.DataSource = datasource;
}
#endregion
```

Em seguida, adicione o seguinte método privado à classe:

```csharp
private void ConfigureTextField (NSTableCellView view, nint row)
{
    // Add to view
    view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
    view.AddSubview (view.TextField);

    // Configure
    view.TextField.BackgroundColor = NSColor.Clear;
    view.TextField.Bordered = false;
    view.TextField.Selectable = false;
    view.TextField.Editable = true;

    // Wireup events
    view.TextField.EditingEnded += (sender, e) => {

        // Take action based on type
        switch (view.Identifier) {
        case "Product":
            DataSource.Products [(int)view.TextField.Tag].Title = view.TextField.StringValue;
            break;
        case "Details":
            DataSource.Products [(int)view.TextField.Tag].Description = view.TextField.StringValue;
            break;
        }
    };

    // Tag view
    view.TextField.Tag = row;
}
```

Isso leva a todas as configurações de exibição de texto que anteriormente estavam sendo feitas na `GetViewForItem` método e os coloca em um único local pode ser chamado (desde a última coluna da tabela não inclui uma exibição de texto, mas um botão).

Por fim, edite o `GetViewForItem` método e torná-lo semelhante ao seguinte:

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)tableView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTableCellView ();

        // Configure the view
        view.Identifier = tableColumn.Title;

        // Take action based on title
        switch (tableColumn.Title) {
        case "Product":
            view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
            view.AddSubview (view.ImageView);
            view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
            ConfigureTextField (view, row);
            break;
        case "Details":
            view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
            ConfigureTextField (view, row);
            break;
        case "Action":
            // Create new button
            var button = new NSButton (new CGRect (0, 0, 81, 16));
            button.SetButtonType (NSButtonType.MomentaryPushIn);
            button.Title = "Delete";
            button.Tag = row;

            // Wireup events
            button.Activated += (sender, e) => {
                // Get button and product
                var btn = sender as NSButton;
                var product = DataSource.Products [(int)btn.Tag];

                // Configure alert
                var alert = new NSAlert () {
                    AlertStyle = NSAlertStyle.Informational,
                    InformativeText = $"Are you sure you want to delete {product.Title}? This operation cannot be undone.",
                    MessageText = $"Delete {product.Title}?",
                };
                alert.AddButton ("Cancel");
                alert.AddButton ("Delete");
                alert.BeginSheetForResponse (Controller.View.Window, (result) => {
                    // Should we delete the requested row?
                    if (result == 1001) {
                        // Remove the given row from the dataset
                        DataSource.Products.RemoveAt((int)btn.Tag);
                        Controller.ReloadTable ();
                    }
                });
            };

            // Add to view
            view.AddSubview (button);
            break;
        }

    }

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed ("tag.png");
        view.TextField.StringValue = DataSource.Products [(int)row].Title;
        view.TextField.Tag = row;
        break;
    case "Details":
        view.TextField.StringValue = DataSource.Products [(int)row].Description;
        view.TextField.Tag = row;
        break;
    case "Action":
        foreach (NSView subview in view.Subviews) {
            var btn = subview as NSButton;
            if (btn != null) {
                btn.Tag = row;
            }
        }
        break;
    }

    return view;
}
```

Vamos examinar várias seções desse código em mais detalhes. Primeiro, se um novo `NSTableViewCell` está sendo criado a ação é obtido com base no nome da coluna. Para as duas primeiras colunas (**produto** e **detalhes**), o novo `ConfigureTextField` método é chamado.

Para o **ação** coluna, um novo `NSButton` é criado e adicionado à célula como uma exibição de sub-rotina:

```csharp
// Create new button
var button = new NSButton (new CGRect (0, 0, 81, 16));
button.SetButtonType (NSButtonType.MomentaryPushIn);
button.Title = "Delete";
button.Tag = row;
...

// Add to view
view.AddSubview (button);
```

O botão `Tag` propriedade é usada para conter o número da linha que está sendo processado atualmente. Esse número será usado mais tarde quando o usuário solicita uma linha a ser excluída no botão de `Activated` eventos:

```csharp
// Wireup events
button.Activated += (sender, e) => {
    // Get button and product
    var btn = sender as NSButton;
    var product = DataSource.Products [(int)btn.Tag];

    // Configure alert
    var alert = new NSAlert () {
        AlertStyle = NSAlertStyle.Informational,
        InformativeText = $"Are you sure you want to delete {product.Title}? This operation cannot be undone.",
        MessageText = $"Delete {product.Title}?",
    };
    alert.AddButton ("Cancel");
    alert.AddButton ("Delete");
    alert.BeginSheetForResponse (Controller.View.Window, (result) => {
        // Should we delete the requested row?
        if (result == 1001) {
            // Remove the given row from the dataset
            DataSource.Products.RemoveAt((int)btn.Tag);
            Controller.ReloadTable ();
        }
    });
};
```

No início do manipulador de eventos, obtemos o botão e o produto que está na linha determinada tabela. Em seguida, um alerta é apresentado ao usuário confirmando a exclusão de linha. Se o usuário optar por excluir a linha, a linha determinada é removida da fonte de dados e a tabela será recarregada:

```csharp
// Remove the given row from the dataset
DataSource.Products.RemoveAt((int)btn.Tag);
Controller.ReloadTable ();
```

Por fim, se a célula de exibição de tabela está sendo reutilizada em vez de que está sendo criado novo, o código a seguir configura com base na coluna que está sendo processada:

```csharp
// Setup view based on the column selected
switch (tableColumn.Title) {
case "Product":
    view.ImageView.Image = NSImage.ImageNamed ("tag.png");
    view.TextField.StringValue = DataSource.Products [(int)row].Title;
    view.TextField.Tag = row;
    break;
case "Details":
    view.TextField.StringValue = DataSource.Products [(int)row].Description;
    view.TextField.Tag = row;
    break;
case "Action":
    foreach (NSView subview in view.Subviews) {
        var btn = subview as NSButton;
        if (btn != null) {
            btn.Tag = row;
        }
    }
    break;
}

```

Para o **ação** coluna, todas as exibições de sub-rotina são verificadas até que o `NSButton` for encontrado, ele é `Tag` propriedade é atualizada para apontar para a linha atual.

Com essas alterações realizadas, quando o aplicativo é executado cada linha terá um **excluir** botão:

[![](table-view-images/delete02.png "O modo de exibição de tabela com os botões de exclusão")](table-view-images/delete02.png#lightbox)

Quando o usuário clica em um **excluir** botão, um alerta será exibido solicitando que exclua a linha determinada:

[![](table-view-images/delete03.png "Um alerta de linha de exclusão")](table-view-images/delete03.png#lightbox)

Se o usuário escolhe delete, a linha será removida e a tabela será redesenhada:

[![](table-view-images/delete04.png "A tabela depois que a linha é excluída.")](table-view-images/delete04.png#lightbox)

<a name="Data_Binding_Table_Views" />

## <a name="data-binding-table-views"></a>Exibições de tabela de associação de dados

Usando técnicas de codificação de chave-valor e associação de dados em seu aplicativo xamarin. Mac, você pode diminuir consideravelmente a quantidade de código que você precisa para escrever e manter para popular e trabalhar com elementos de interface do usuário. Você também tem a vantagem de dissociar ainda mais seus dados de backup (_modelo de dados_) da sua frente terminar a Interface do usuário (_Model-View-Controller_), gerando mais fáceis de manter aplicativos mais flexíveis Design.

Codificação de chave-valor (KVC) é um mecanismo para acessar propriedades de um objeto indiretamente, usando métodos de acessador ou chaves (especialmente formatadas cadeias de caracteres) para identificar as propriedades em vez de acessá-los por meio de variáveis de instância (`get/set`). Com a implementação de acessadores em conformidade com a codificação de chave-valor em seu aplicativo xamarin. Mac, você obtém acesso a outros recursos do macOS como observando de chave-valor (KVO), associação de dados, dados principais, associações de Cocoa e ScriptableType.

Para obter mais informações, consulte o [associação de dados de exibição de tabela](~/mac/app-fundamentals/databinding.md#Table_View_Data_Binding) seção do nosso [vinculação de dados e a codificação de chave-valor](~/mac/app-fundamentals/databinding.md) documentação.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo apresentou uma visão detalhada de como trabalhar com modos de exibição de tabela em um aplicativo xamarin. Mac. Podemos viu os diferentes tipos e usos de modos de exibição de tabela, como criar e manter modos de exibição de tabela no Interface Builder do Xcode e como trabalhar com modos de exibição de tabela em código c#.

## <a name="related-links"></a>Links relacionados

- [MacTables (amostra)](https://developer.xamarin.com/samples/mac/MacTables/)
- [MacImages (amostra)](https://developer.xamarin.com/samples/mac/MacImages/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Modos de exibição de estrutura de tópicos](~/mac/user-interface/outline-view.md)
- [Listas de origem](~/mac/user-interface/source-list.md)
- [Associação de dados e a codificação de chave-valor](~/mac/app-fundamentals/databinding.md)
- [Diretrizes de interface humana do OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [NSTableView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSTableView_Class/index.html#//apple_ref/doc/uid/TP40004125)
- [NSTableViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSTableViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008622)
- [NSTableViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSTableDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004178)
