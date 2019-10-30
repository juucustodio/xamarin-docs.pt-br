---
title: Exibições de estrutura de tópicos no Xamarin. Mac
description: Este artigo aborda como trabalhar com exibições de estrutura de tópicos em um aplicativo Xamarin. Mac. Ele descreve como criar e manter exibições de contorno no Xcode e Interface Builder e trabalhar com elas de forma programática.
ms.prod: xamarin
ms.assetid: 043248EE-11DA-4E96-83A3-08824A4F2E01
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: dd710f13d9324ce31e08641f214241e0433fe809
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73004339"
---
# <a name="outline-views-in-xamarinmac"></a>Exibições de estrutura de tópicos no Xamarin. Mac

_Este artigo aborda como trabalhar com exibições de estrutura de tópicos em um aplicativo Xamarin. Mac. Ele descreve como criar e manter exibições de contorno no Xcode e Interface Builder e trabalhar com elas de forma programática._

Ao trabalhar com C# o e o .net em um aplicativo Xamarin. Mac, você tem acesso às mesmas exibições de estrutura de tópicos que um desenvolvedor trabalhando no *Objective-C e no* *Xcode* . Como o Xamarin. Mac se integra diretamente com o Xcode, você pode usar o _interface Builder_ do Xcode para criar e manter suas exibições de estrutura de tópicos ( C# ou, opcionalmente, criá-las diretamente no código).

Um modo de exibição de estrutura de tópicos é um tipo de tabela que permite ao usuário expandir ou recolher linhas de dados hierárquicos. Como uma exibição de tabela, um modo de exibição de estrutura de tópicos exibe dados para um conjunto de itens relacionados, com linhas que representam itens individuais e colunas que representam os atributos desses itens. Ao contrário de uma exibição de tabela, os itens em um modo de exibição de estrutura de tópicos não estão em uma lista simples, eles são organizados em uma hierarquia, como arquivos e pastas em um disco rígido.

[![](outline-view-images/populate03.png "An example app run")](outline-view-images/populate03.png#lightbox)

Neste artigo, abordaremos as noções básicas de como trabalhar com exibições de estrutura de tópicos em um aplicativo Xamarin. Mac. É altamente recomendável que você trabalhe pelo artigo [Hello, Mac](~/mac/get-started/hello-mac.md) primeiro, especificamente a [introdução às seções Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [ações](~/mac/get-started/hello-mac.md#outlets-and-actions) , pois ela aborda os principais conceitos e técnicas que usaremos em Este artigo.

Talvez você queira dar uma olhada na seção [ C# expondo classes/métodos para Objective-C](~/mac/internals/how-it-works.md) do documento [interno do Xamarin. Mac](~/mac/internals/how-it-works.md) também, explica os comandos`Register`e`Export`usados para conectar suas C# classes ao Objective-C objetos e elementos de interface do usuário.

<a name="Introduction_to_Outline_Views" />

## <a name="introduction-to-outline-views"></a>Introdução aos modos de exibição de estrutura de tópicos

Um modo de exibição de estrutura de tópicos é um tipo de tabela que permite ao usuário expandir ou recolher linhas de dados hierárquicos. Como uma exibição de tabela, um modo de exibição de estrutura de tópicos exibe dados para um conjunto de itens relacionados, com linhas que representam itens individuais e colunas que representam os atributos desses itens. Ao contrário de uma exibição de tabela, os itens em um modo de exibição de estrutura de tópicos não estão em uma lista simples, eles são organizados em uma hierarquia, como arquivos e pastas em um disco rígido.

Se um item em um modo de exibição de estrutura de tópicos contiver outros itens, ele poderá ser expandido ou recolhido pelo usuário. Um item expansível exibe um triângulo de divulgação, que aponta para a direita quando o item é recolhido e aponta para baixo quando o item é expandido. Clicar no triângulo de divulgação faz com que o item seja expandido ou recolhido.

O modo de exibição de estrutura de tópicos (`NSOutlineView`) é uma subclasse da exibição de tabela (`NSTableView`) e, dessa forma, herda grande parte de seu comportamento de sua classe pai. Como resultado, muitas operações com suporte em uma exibição de tabela, como selecionar linhas ou colunas, reposicionar colunas arrastando cabeçalhos de coluna, etc., também são suportadas por um modo de exibição de estrutura de tópicos. Um aplicativo Xamarin. Mac tem controle desses recursos e pode configurar os parâmetros da exibição de estrutura de tópicos (no código ou Interface Builder) para permitir ou impedir determinadas operações.

Um modo de exibição de estrutura de tópicos não armazena seus próprios dados, em vez disso, ele se baseia em uma fonte de dados (`NSOutlineViewDataSource`) para fornecer as linhas e colunas necessárias, conforme necessário.

O comportamento de uma exibição de estrutura de tópicos pode ser personalizado fornecendo uma subclasse do delegado da exibição de estrutura de tópicos (`NSOutlineViewDelegate`) para dar suporte ao gerenciamento de coluna de estrutura de tópicos, digitar para selecionar funcionalidade, seleção de linha e edição, acompanhamento personalizado e exibições personalizadas para colunas individuais linhas e.

Como uma exibição de estrutura de tópicos compartilha grande parte do comportamento e da funcionalidade com uma exibição de tabela, talvez você queira percorrer nossa documentação de [exibições de tabela](~/mac/user-interface/table-view.md) antes de continuar com este artigo.

<a name="Creating_and_Maintaining_Outline_Views_in_Xcode" />

## <a name="creating-and-maintaining-outline-views-in-xcode"></a>Criando e mantendo exibições de contorno no Xcode

Ao criar um novo aplicativo Xamarin. Mac Cocoa, você obtém uma janela padrão em branco, por padrão. Essas janelas são definidas em um arquivo `.storyboard` incluído automaticamente no projeto. Para editar seu design do Windows, na **Gerenciador de soluções**, clique duas vezes no arquivo `Main.storyboard`:

[![](outline-view-images/edit01.png "Selecting the main storyboard")](outline-view-images/edit01.png#lightbox)

Isso abrirá o design da janela na Interface Builder do Xcode:

[![](outline-view-images/edit02.png "Editing the UI in Xcode")](outline-view-images/edit02.png#lightbox)

Digite `outline` na caixa de pesquisa **do Inspetor de biblioteca** para facilitar a localização dos controles da exibição de estrutura de tópicos:

[![](outline-view-images/edit03.png "Selecting an Outline View from the Library")](outline-view-images/edit03.png#lightbox)

Arraste um modo de exibição de estrutura de tópicos para o controlador de exibição no **Editor de interface**, faça com que ele preencha a área de conteúdo do controlador de exibição e defina-o para o local em que ele é reduzido e cresce com a janela no **Editor de restrição**:

[![](outline-view-images/edit04.png "Editing the constraints")](outline-view-images/edit04.png#lightbox)

Selecione o modo de exibição de estrutura de tópicos na **hierarquia de interface** e as seguintes propriedades estão disponíveis no **Inspetor de atributo**:

[![](outline-view-images/edit05.png "The Attribute Inspector")](outline-view-images/edit05.png#lightbox)

- **Coluna de estrutura de tópicos** -a coluna de tabela na qual os dados hierárquicos são exibidos.
- **Coluna de estrutura de tópicos de salvamento automático** – se `true`, a coluna de estrutura de tópicos será automaticamente salva e restaurada entre as execuções do aplicativo.
- **Recuo** -o valor para recuar colunas em um item expandido.
- O **recuo segue as células** -se `true`, o sinal de recuo será recuado junto com as células.
- **Salvar itens expandidos** -se `true`, o estado expandido/recolhido dos itens será automaticamente salvo e restaurado entre as execuções do aplicativo.
- **Modo de conteúdo** – permite que você use modos de exibição (`NSView`) ou células (`NSCell`) para exibir os dados nas linhas e colunas. A partir do macOS 10,7, você deve usar exibições.
- **Flutua linhas do grupo** -se `true`, a exibição de tabela desenhará células agrupadas como se elas estivessem flutuantes.
- **Colunas** – define o número de colunas exibidas.
- **Cabeçalhos** -se `true`, as colunas terão cabeçalhos.
- **Reordenando** -se `true`, o usuário poderá arrastar para reordenar as colunas na tabela.
- **Redimensionamento** – se `true`, o usuário poderá arrastar cabeçalhos de coluna para redimensionar colunas.
- **Dimensionamento de coluna** – controla como a tabela dimensionará automaticamente as colunas.
- **Highlight** -controla o tipo de realce que a tabela usa quando uma célula é selecionada.
- **Linhas alternativas** -se `true`, qualquer outra linha terá uma cor de plano de fundo diferente.
- **Grade horizontal** – seleciona o tipo de borda desenhada entre as células horizontalmente.
- **Grade vertical** – seleciona o tipo de borda desenhada entre as células verticalmente.
- **Cor da grade** – define a cor da borda da célula.
- **Plano** de fundo – define a cor do plano de fundo da célula.
- **Seleção** – permitir que você controle como o usuário pode selecionar células na tabela como:
  - **Multiple** -If `true`, o usuário pode selecionar várias linhas e colunas.
  - **Coluna** -se `true`, o usuário pode selecionar colunas.
  - **Digite Select** -If `true`, o usuário pode digitar um caractere para selecionar uma linha.
  - **Empty** -se `true`, o usuário não precisa selecionar uma linha ou coluna, a tabela não permite nenhuma seleção.
- **AutoSalvar** – o nome no qual o formato das tabelas é salvo automaticamente.
- **Informações da coluna** -se `true`, a ordem e a largura das colunas serão salvas automaticamente.
- **Quebras de linha** – selecione como a célula lida com quebras de linha.
- **Trunca a última linha visível** -se `true`, a célula será truncada nos dados não podem se ajustar dentro de seus limites.

> [!IMPORTANT]
> A menos que você esteja mantendo um aplicativo Xamarin. Mac herdado, as exibições de estrutura de tópicos baseadas em `NSView` devem ser usadas em exibições de tabela baseadas em `NSCell`. `NSCell` é considerado herdado e pode não ter suporte no futuro.

Selecione uma coluna de tabela na **hierarquia de interface** e as seguintes propriedades estão disponíveis no **Inspetor de atributo**:

[![](outline-view-images/edit06.png "The Attribute Inspector")](outline-view-images/edit06.png#lightbox)

- **Título** – define o título da coluna.
- **Alinhamento** – defina o alinhamento do texto dentro das células.
- **Fonte do título** – seleciona a fonte do texto do cabeçalho da célula.
- **Chave de classificação** -é a chave usada para classificar dados na coluna. Deixe em branco se o usuário não puder classificar esta coluna.
- **Selector** – a **ação** usada para executar a classificação. Deixe em branco se o usuário não puder classificar esta coluna.
- **Order** – a ordem de classificação para os dados das colunas.
- **Redimensionamento** – seleciona o tipo de redimensionamento para a coluna.
- **Editable** -se `true`, o usuário pode editar células em uma tabela baseada em célula.
- **Hidden** -se `true`, a coluna ficará oculta.

Você também pode redimensionar a coluna arrastando sua alça (verticalmente centralizada no lado direito da coluna) à esquerda ou à direita.

Vamos selecionar cada coluna em nosso modo de exibição de tabela e dar à primeira coluna um **título** de `Product` e a segunda `Details`.

Selecione uma exibição de célula de tabela (`NSTableViewCell`) na **hierarquia de interface** e as seguintes propriedades estão disponíveis no Inspetor de **atributo**:

[![](outline-view-images/edit07.png "The Attribute Inspector")](outline-view-images/edit07.png#lightbox)

Essas são todas as propriedades de uma exibição padrão. Você também tem a opção de redimensionar as linhas desta coluna aqui.

Selecione uma célula de exibição de tabela (por padrão, essa é uma `NSTextField`) na **hierarquia de interface** e as seguintes propriedades estão disponíveis no Inspetor de **atributo**:

[![](outline-view-images/edit08.png "The Attribute Inspector")](outline-view-images/edit08.png#lightbox)

Você terá todas as propriedades de um campo de texto padrão para definir aqui. Por padrão, um campo de texto padrão é usado para exibir dados de uma célula em uma coluna.

Selecione uma exibição de célula de tabela (`NSTableFieldCell`) na **hierarquia de interface** e as seguintes propriedades estão disponíveis no Inspetor de **atributo**:

[![](outline-view-images/edit09.png "The Attribute Inspector")](outline-view-images/edit09.png#lightbox)

As configurações mais importantes aqui são:

- **Layout** – selecione como as células nesta coluna são dispostos.
- **Usa o modo de linha única** -se `true`, a célula será limitada a uma única linha.
- **Primeira largura do layout de tempo de execução** -se `true`, a célula preferirá a largura definida para ela (manual ou automaticamente) quando ela for exibida na primeira vez em que o aplicativo for executado.
- **Ação** -controla quando a **ação** de edição é enviada para a célula.
- **Comportamento** – define se uma célula é selecionável ou editável.
- **Rich Text** – se `true`, a célula poderá exibir texto formatado e com estilo.
- **Undo** -se `true`, a célula assumirá a responsabilidade pelo comportamento de desfazer.

Selecione o modo de exibição de célula de tabela (`NSTableFieldCell`) na parte inferior de uma coluna de tabela na **hierarquia de interface**:

[![](outline-view-images/edit11.png "Selecting the table cell view")](outline-view-images/edit10.png#lightbox)

Isso permite que você edite a exibição de célula de tabela usada como _padrão_ de base para todas as células criadas para a coluna especificada.

<a name="Adding_Actions_and_Outlets" />

### <a name="adding-actions-and-outlets"></a>Adicionando ações e saídas

Assim como qualquer outro controle de interface do usuário do Cocoa, precisamos expor nossa exibição de estrutura de tópicos e suas C# colunas e células para codificar usando **ações** e **saídas** (com base na funcionalidade necessária).

O processo é o mesmo para qualquer elemento de exibição de estrutura de tópicos que desejamos expor:

1. Alterne para o **Editor do assistente** e verifique se o arquivo `ViewController.h` está selecionado:

    [![](outline-view-images/edit11.png "Selecting the correct .h file")](outline-view-images/edit11.png#lightbox)
2. Selecione o modo de exibição de estrutura de tópicos na **hierarquia de interface**, clique com o botão de controle e arraste para o arquivo `ViewController.h`.
3. Crie uma **tomada** para o modo de exibição de estrutura de tópicos chamado `ProductOutline`:

    [![](outline-view-images/edit13.png "Configuring an Outlet")](outline-view-images/edit13.png#lightbox)
4. Crie **saídas** para as colunas de tabelas também chamadas `ProductColumn` e `DetailsColumn`:

    [![](outline-view-images/edit14.png "Configuring an Outlet")](outline-view-images/edit14.png#lightbox)
5. Salve as alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Em seguida, escreveremos o código para exibir alguns dados para a estrutura de tópicos quando o aplicativo for executado.

<a name="Populating_the_Table_View" />

## <a name="populating-the-outline-view"></a>Preenchendo a exibição de estrutura de tópicos

Com nossa exibição de estrutura de tópicos projetada em Interface Builder e exposta por meio de uma **tomada**, em C# seguida, precisamos criar o código para preenchê-lo.

Primeiro, vamos criar uma nova classe `Product` para manter as informações das linhas individuais e grupos de subprodutos. Na **Gerenciador de soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar** > **novo arquivo...** Selecione **geral** > **classe vazia**, digite `Product` para o **nome** e clique no botão **novo** :

[![](outline-view-images/populate01.png "Creating an empty class")](outline-view-images/populate01.png#lightbox)

Faça com que o arquivo de `Product.cs` se pareça com o seguinte:

```csharp
using System;
using Foundation;
using System.Collections.Generic;

namespace MacOutlines
{
    public class Product : NSObject
    {
        #region Public Variables
        public List<Product> Products = new List<Product>();
        #endregion

        #region Computed Properties
        public string Title { get; set;} = "";
        public string Description { get; set;} = "";
        public bool IsProductGroup {
            get { return (Products.Count > 0); }
        }
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

Em seguida, precisamos criar uma subclasse de `NSOutlineDataSource` para fornecer os dados para nossa estrutura de tópicos conforme solicitado. Na **Gerenciador de soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar** > **novo arquivo...** Selecione **geral** > **classe vazia**, digite `ProductOutlineDataSource` para o **nome** e clique no botão **novo** .

Edite o arquivo de `ProductTableDataSource.cs` e faça com que ele se pareça com o seguinte:

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacOutlines
{
    public class ProductOutlineDataSource : NSOutlineViewDataSource
    {
        #region Public Variables
        public List<Product> Products = new List<Product>();
        #endregion

        #region Constructors
        public ProductOutlineDataSource ()
        {
        }
        #endregion

        #region Override Methods
        public override nint GetChildrenCount (NSOutlineView outlineView, NSObject item)
        {
            if (item == null) {
                return Products.Count;
            } else {
                return ((Product)item).Products.Count;
            }

        }

        public override NSObject GetChild (NSOutlineView outlineView, nint childIndex, NSObject item)
        {
            if (item == null) {
                return Products [childIndex];
            } else {
                return ((Product)item).Products [childIndex];
            }

        }

        public override bool ItemExpandable (NSOutlineView outlineView, NSObject item)
        {
            if (item == null) {
                return Products [0].IsProductGroup;
            } else {
                return ((Product)item).IsProductGroup;
            }

        }
        #endregion
    }
}
```

Essa classe tem armazenamento para os itens de nossa exibição de estrutura de tópicos e substitui o `GetChildrenCount` para retornar o número de linhas na tabela. O `GetChild` retorna um item pai ou filho específico (conforme solicitado pelo modo de exibição de estrutura de tópicos) e o `ItemExpandable` define o item especificado como um pai ou filho.

Finalmente, precisamos criar uma subclasse de `NSOutlineDelegate` para fornecer o comportamento para nossa estrutura de tópicos. Na **Gerenciador de soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar** > **novo arquivo...** Selecione **geral** > **classe vazia**, digite `ProductOutlineDelegate` para o **nome** e clique no botão **novo** .

Edite o arquivo de `ProductOutlineDelegate.cs` e faça com que ele se pareça com o seguinte:

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacOutlines
{
    public class ProductOutlineDelegate : NSOutlineViewDelegate
    {
        #region Constants
        private const string CellIdentifier = "ProdCell";
        #endregion

        #region Private Variables
        private ProductOutlineDataSource DataSource;
        #endregion

        #region Constructors
        public ProductOutlineDelegate (ProductOutlineDataSource datasource)
        {
            this.DataSource = datasource;
        }
        #endregion

        #region Override Methods

        public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
            // This pattern allows you reuse existing views when they are no-longer in use.
            // If the returned view is null, you instance up a new view
            // If a non-null view is returned, you modify it enough to reflect the new data
            NSTextField view = (NSTextField)outlineView.MakeView (CellIdentifier, this);
            if (view == null) {
                view = new NSTextField ();
                view.Identifier = CellIdentifier;
                view.BackgroundColor = NSColor.Clear;
                view.Bordered = false;
                view.Selectable = false;
                view.Editable = false;
            }

            // Cast item
            var product = item as Product;

            // Setup view based on the column selected
            switch (tableColumn.Title) {
            case "Product":
                view.StringValue = product.Title;
                break;
            case "Details":
                view.StringValue = product.Description;
                break;
            }

            return view;
        }
        #endregion
    }
}
```

Quando criamos uma instância do `ProductOutlineDelegate`, também passamos uma instância do `ProductOutlineDataSource` que fornece os dados para o contorno. O método `GetView` é responsável por retornar uma exibição (dados) para exibir a célula de uma coluna e uma linha. Se possível, uma exibição existente será reutilizada para exibir a célula, se não for necessário criar uma nova exibição.

Para preencher o contorno, vamos editar o arquivo de `MainWindow.cs` e fazer com que o método `AwakeFromNib` seja semelhante ao seguinte:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Create data source and populate
    var DataSource = new ProductOutlineDataSource ();

    var Vegetables = new Product ("Vegetables", "Greens and Other Produce");
    Vegetables.Products.Add (new Product ("Cabbage", "Brassica oleracea - Leaves, axillary buds, stems, flowerheads"));
    Vegetables.Products.Add (new Product ("Turnip", "Brassica rapa - Tubers, leaves"));
    Vegetables.Products.Add (new Product ("Radish", "Raphanus sativus - Roots, leaves, seed pods, seed oil, sprouting"));
    Vegetables.Products.Add (new Product ("Carrot", "Daucus carota - Root tubers"));
    DataSource.Products.Add (Vegetables);

    var Fruits = new Product ("Fruits", "Fruit is a part of a flowering plant that derives from specific tissues of the flower");
    Fruits.Products.Add (new Product ("Grape", "True Berry"));
    Fruits.Products.Add (new Product ("Cucumber", "Pepo"));
    Fruits.Products.Add (new Product ("Orange", "Hesperidium"));
    Fruits.Products.Add (new Product ("Blackberry", "Aggregate fruit"));
    DataSource.Products.Add (Fruits);

    var Meats = new Product ("Meats", "Lean Cuts");
    Meats.Products.Add (new Product ("Beef", "Cow"));
    Meats.Products.Add (new Product ("Pork", "Pig"));
    Meats.Products.Add (new Product ("Veal", "Young Cow"));
    DataSource.Products.Add (Meats);

    // Populate the outline
    ProductOutline.DataSource = DataSource;
    ProductOutline.Delegate = new ProductOutlineDelegate (DataSource);

}
```

Se executarmos o aplicativo, o seguinte será exibido:

[![](outline-view-images/populate02.png "The collapsed view")](outline-view-images/populate02.png#lightbox)

Se expandirmos um nó no modo de exibição de estrutura de tópicos, ele será semelhante ao seguinte:

[![](outline-view-images/populate03.png "The expanded view")](outline-view-images/populate03.png#lightbox)

<a name="Sorting_by_Column" />

## <a name="sorting-by-column"></a>Classificando por coluna

Vamos permitir que o usuário classifique os dados no contorno clicando em um cabeçalho de coluna. Primeiro, clique duas vezes no arquivo `Main.storyboard` para abri-lo para edição no Interface Builder. Selecione a coluna `Product`, insira `Title` para a **chave de classificação**, `compare:` para o **seletor** e selecione `Ascending` para a **ordem**:

[![](outline-view-images/sort01.png "Setting the sort key order")](outline-view-images/sort01.png#lightbox)

Salve as alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Agora, vamos editar o arquivo `ProductOutlineDataSource.cs` e adicionar os seguintes métodos:

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
    }
}

public override void SortDescriptorsChanged (NSOutlineView outlineView, NSSortDescriptor[] oldDescriptors)
{
    // Sort the data
    Sort (oldDescriptors [0].Key, oldDescriptors [0].Ascending);
    outlineView.ReloadData ();
}
```

O método `Sort` nos permite classificar os dados na fonte de dados com base em um determinado campo de classe `Product` em ordem crescente ou decrescente. O método de `SortDescriptorsChanged` substituído será chamado toda vez que o uso for clicado em um título de coluna. Ele passará o valor de **chave** que definimos em interface Builder e a ordem de classificação para essa coluna.

Se executarmos o aplicativo e clicarmos nos cabeçalhos de coluna, as linhas serão classificadas por essa coluna:

[![](outline-view-images/sort02.png "Example of sorted output")](outline-view-images/sort02.png#lightbox)

<a name="Row_Selection" />

## <a name="row-selection"></a>Seleção de linha

Se você quiser permitir que o usuário selecione uma única linha, clique duas vezes no arquivo `Main.storyboard` para abri-lo para edição no Interface Builder. Selecione o modo de exibição de estrutura de tópicos na **hierarquia de interface** e desmarque a caixa de seleção **vários** no **Inspetor de atributo**:

[![](outline-view-images/select01.png "The Attribute Inspector")](outline-view-images/select01.png#lightbox)

Salve as alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Em seguida, edite o arquivo `ProductOutlineDelegate.cs` e adicione o seguinte método:

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

Isso permitirá que o usuário selecione uma única linha no modo de exibição de estrutura de tópicos. Retorne `false` para o `ShouldSelectItem` de qualquer item que você não queira que o usuário possa selecionar ou `false` para cada item se não quiser que o usuário seja capaz de selecionar os itens.

<a name="Multiple_Row_Selection" />

## <a name="multiple-row-selection"></a>Seleção de várias linhas

Se você quiser permitir que o usuário selecione várias linhas, clique duas vezes no arquivo `Main.storyboard` para abri-lo para edição no Interface Builder. Selecione o modo de exibição de estrutura de tópicos na **hierarquia de interface** e marque a caixa de seleção **vários** no **Inspetor de atributo**:

[![](outline-view-images/select02.png "The Attribute Inspector")](outline-view-images/select02.png#lightbox)

Salve as alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Em seguida, edite o arquivo `ProductOutlineDelegate.cs` e adicione o seguinte método:

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

Isso permitirá que o usuário selecione uma única linha no modo de exibição de estrutura de tópicos. Retorne `false` para o `ShouldSelectRow` de qualquer item que você não queira que o usuário possa selecionar ou `false` para cada item se não quiser que o usuário seja capaz de selecionar os itens.

<a name="Type_to_Select_Row" />

## <a name="type-to-select-row"></a>Digite para selecionar a linha

Se você quiser permitir que o usuário digite um caractere com o modo de exibição de estrutura de tópicos selecionado e selecione a primeira linha que tem esse caractere, clique duas vezes no arquivo de `Main.storyboard` para abri-lo para edição no Interface Builder. Selecione o modo de exibição de estrutura de tópicos na **hierarquia de interface** e marque a caixa de seleção **tipo selecionar** no **Inspetor de atributo**:

[![](outline-view-images/type01.png "Editing the row type")](outline-view-images/type01.png#lightbox)

Salve as alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Agora, vamos editar o arquivo `ProductOutlineDelegate.cs` e adicionar o seguinte método:

```csharp
public override NSObject GetNextTypeSelectMatch (NSOutlineView outlineView, NSObject startItem, NSObject endItem, string searchString)
{
    foreach(Product product in DataSource.Products) {
        if (product.Title.Contains (searchString)) {
            return product;
        }
    }

    // Not found
    return null;
}
```

O método `GetNextTypeSelectMatch` usa o `searchString` fornecido e retorna o item da primeira `Product` que tem essa cadeia de caracteres no `Title`.

<a name="Reordering_Columns" />

## <a name="reordering-columns"></a>Reordenando colunas

Se você quiser permitir que o usuário Arraste colunas de reordenação no modo de exibição de estrutura de tópicos, clique duas vezes no arquivo de `Main.storyboard` para abri-lo para edição no Interface Builder. Selecione o modo de exibição de estrutura de tópicos na **hierarquia de interface** e marque a caixa de seleção **Reordenar** no **Inspetor de atributo**:

[![](outline-view-images/reorder01.png "The Attribute Inspector")](outline-view-images/reorder01.png#lightbox)

Se fornecermos um valor para a **Propriedade** autosave e verificar o campo de **informações da coluna** , as alterações feitas no layout da tabela serão salvas automaticamente para nós e restauradas na próxima vez em que o aplicativo for executado.

Salve as alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Agora, vamos editar o arquivo `ProductOutlineDelegate.cs` e adicionar o seguinte método:

```csharp
public override bool ShouldReorder (NSOutlineView outlineView, nint columnIndex, nint newColumnIndex)
{
    return true;
}
```

O método `ShouldReorder` deve retornar `true` para qualquer coluna que queira permitir que seja arrastado reordenado para a `newColumnIndex`, outra coisa retorna `false`;

Se executarmos o aplicativo, podemos arrastar cabeçalhos de coluna para reordenar nossas colunas:

[![](outline-view-images/reorder02.png "Example of reordering columns")](outline-view-images/reorder02.png#lightbox)

<a name="Editing_Cells" />

## <a name="editing-cells"></a>Editando células

Se você quiser permitir que o usuário edite os valores de uma determinada célula, edite o arquivo `ProductOutlineDelegate.cs` e altere o método `GetViewForItem` da seguinte maneira:

```csharp
public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
    // Cast item
    var product = item as Product;

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTextField view = (NSTextField)outlineView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTextField ();
        view.Identifier = tableColumn.Title;
        view.BackgroundColor = NSColor.Clear;
        view.Bordered = false;
        view.Selectable = false;
        view.Editable = !product.IsProductGroup;
    }

    // Tag view
    view.Tag = outlineView.RowForItem (item);

    // Allow for edit
    view.EditingEnded += (sender, e) => {

        // Grab product
        var prod = outlineView.ItemAtRow(view.Tag) as Product;

        // Take action based on type
        switch(view.Identifier) {
        case "Product":
            prod.Title = view.StringValue;
            break;
        case "Details":
            prod.Description = view.StringValue;
            break;
        }
    };

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.StringValue = product.Title;
        break;
    case "Details":
        view.StringValue = product.Description;
        break;
    }

    return view;
}
```

Agora, se executarmos o aplicativo, o usuário poderá editar as células na exibição de tabela:

[![](outline-view-images/editing01.png "An example of editing cells")](outline-view-images/editing01.png#lightbox)

<a name="Using_Images_in_Outline_Views" />

## <a name="using-images-in-outline-views"></a>Usando imagens em exibições de estrutura de tópicos

Para incluir uma imagem como parte da célula em um `NSOutlineView`, você precisará alterar a forma como os dados são retornados pelo método `NSTableViewDelegate's` `GetView` do modo de exibição de estrutura de tópicos para usar um `NSTableCellView` em vez do `NSTextField` típico. Por exemplo:

```csharp
public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
    // Cast item
    var product = item as Product;

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)outlineView.MakeView (tableColumn.Title, this);
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
        view.TextField.Editable = !product.IsProductGroup;
    }

    // Tag view
    view.TextField.Tag = outlineView.RowForItem (item);

    // Allow for edit
    view.TextField.EditingEnded += (sender, e) => {

        // Grab product
        var prod = outlineView.ItemAtRow(view.Tag) as Product;

        // Take action based on type
        switch(view.Identifier) {
        case "Product":
            prod.Title = view.TextField.StringValue;
            break;
        case "Details":
            prod.Description = view.TextField.StringValue;
            break;
        }
    };

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed (product.IsProductGroup ? "tags.png" : "tag.png");
        view.TextField.StringValue = product.Title;
        break;
    case "Details":
        view.TextField.StringValue = product.Description;
        break;
    }

    return view;
}
```

Para obter mais informações, consulte a seção [usando imagens com exibições de estrutura de tópicos](~/mac/app-fundamentals/image.md) de nossa documentação de [trabalho com imagem](~/mac/app-fundamentals/image.md) .

<a name="Data_Binding_Outline_Views" />

## <a name="data-binding-outline-views"></a>Exibições da Associação de dados

Usando a codificação de valor chave e técnicas de vinculação de dados em seu aplicativo Xamarin. Mac, você pode diminuir muito a quantidade de código que você precisa escrever e manter para popular e trabalhar com elementos de interface do usuário. Você também tem o benefício de desacoplar ainda mais os dados de backup (_modelo de dados_) da sua interface do usuário de front-end (_Model-View-Controller_), levando a um design de aplicativo mais fácil de manter e mais flexível.

A KVC (codificação de valor chave) é um mecanismo para acessar as propriedades de um objeto indiretamente, usando chaves (cadeias de caracteres formatadas especialmente) para identificar Propriedades em vez de acessá-las por meio de variáveis de instância ou métodos de acessador (`get/set`). Ao implementar acessadores compatíveis com codificação de chave-valor em seu aplicativo Xamarin. Mac, você obterá acesso a outros recursos do macOS, como KVO (chave-valor, observação), vinculação de dados, dados principais, associações de Cocoa e scripts.

Para obter mais informações, consulte a seção [estrutura de dados de exibição de tópicos](~/mac/app-fundamentals/databinding.md#Outline_View_Data_Binding) de nossa vinculação de dados e documentação de [codificação de chave-valor](~/mac/app-fundamentals/databinding.md) .

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo deu uma visão detalhada de como trabalhar com exibições de estrutura de tópicos em um aplicativo Xamarin. Mac. Vimos os diferentes tipos e usos dos modos de exibição de estrutura de tópicos, como criar e manter exibições de contorno no Interface Builder do Xcode e como C# trabalhar com exibições de estrutura de tópicos no código.

## <a name="related-links"></a>Links relacionados

- [MacOutlines (exemplo)](https://docs.microsoft.com/samples/xamarin/mac-samples/macoutlines)
- [MacImages (amostra)](https://docs.microsoft.com/samples/xamarin/mac-samples/macimages)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Modos de exibição de tabela](~/mac/user-interface/table-view.md)
- [Listas de origem](~/mac/user-interface/source-list.md)
- [Associação de dados e a codificação de chave-valor](~/mac/app-fundamentals/databinding.md)
- [Diretrizes de interface humana do OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introdução aos modos de exibição de estrutura de tópicos](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/OutlineView/OutlineView.html#//apple_ref/doc/uid/10000023i)
- [NSOutlineView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSOutlineView_Class/index.html#//apple_ref/doc/uid/TP40004079)
- [NSOutlineViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSOutlineViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004175)
- [NSOutlineViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSOutlineViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008609)
