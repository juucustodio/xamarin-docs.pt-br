---
title: Modos de exibição de estrutura de tópicos no xamarin. Mac
description: Este artigo aborda o trabalho com modos de exibição de estrutura de tópicos em um aplicativo xamarin. Mac. Ele descreve a criar e manter modos de exibição de estrutura de tópicos no Interface Builder e Xcode e como trabalhar com eles por meio de programação.
ms.prod: xamarin
ms.assetid: 043248EE-11DA-4E96-83A3-08824A4F2E01
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: a202dd32bcde5cf91cce3f597c9ea7c17f4adbcd
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113556"
---
# <a name="outline-views-in-xamarinmac"></a>Modos de exibição de estrutura de tópicos no xamarin. Mac

_Este artigo aborda o trabalho com modos de exibição de estrutura de tópicos em um aplicativo xamarin. Mac. Ele descreve a criar e manter modos de exibição de estrutura de tópicos no Interface Builder e Xcode e como trabalhar com eles por meio de programação._

Ao trabalhar com c# e .NET em um aplicativo xamarin. Mac, você tem acesso à mesma estrutura de tópicos de exibições que um desenvolvedor que trabalha em *Objective-C* e *Xcode* faz. Como o xamarin. Mac se integra diretamente com Xcode, você pode usar do Xcode _construtor de Interface_ para criar e manter seus modos de exibição de estrutura de tópicos (ou, opcionalmente, criá-los diretamente em código c#).

Uma exibição de estrutura de tópicos é um tipo de tabela que permite ao usuário expandir ou recolher linhas de dados hierárquicos. Como uma exibição de tabela, uma exibição de estrutura de tópicos exibe dados de um conjunto de itens relacionados, com linhas que representam itens individuais e colunas que representam os atributos desses itens. Ao contrário de uma exibição de tabela, não são itens em uma exibição de estrutura de tópicos em uma lista simples, eles são organizados em uma hierarquia, como arquivos e pastas em um disco rígido.

[![](outline-view-images/populate03.png "Um execução do aplicativo de exemplo")](outline-view-images/populate03.png#lightbox)

Neste artigo, abordaremos os fundamentos de trabalhar com os modos de exibição em um aplicativo xamarin. Mac. É altamente recomendável que você trabalhe por meio de [Hello, Mac](~/mac/get-started/hello-mac.md) pela primeira vez, especificamente o artigo a [Introdução ao Interface Builder e Xcode](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [saídas e ações](~/mac/get-started/hello-mac.md#outlets-and-actions) seções, como ela aborda os principais conceitos e técnicas que usaremos neste artigo.

Talvez você queira dar uma olhada o [classes expondo c# / métodos para Objective-C](~/mac/internals/how-it-works.md) seção o [recursos internos de xamarin. Mac](~/mac/internals/how-it-works.md) documentar Além disso, ele explica o `Register` e `Export` comandos usado para transmissão-up suas classes de c# a objetos de Objective-C e elementos de interface do usuário.

<a name="Introduction_to_Outline_Views" />

## <a name="introduction-to-outline-views"></a>Introdução aos modos de exibição de estrutura de tópicos

Uma exibição de estrutura de tópicos é um tipo de tabela que permite ao usuário expandir ou recolher linhas de dados hierárquicos. Como uma exibição de tabela, uma exibição de estrutura de tópicos exibe dados de um conjunto de itens relacionados, com linhas que representam itens individuais e colunas que representam os atributos desses itens. Ao contrário de uma exibição de tabela, não são itens em uma exibição de estrutura de tópicos em uma lista simples, eles são organizados em uma hierarquia, como arquivos e pastas em um disco rígido.

Se um item em uma exibição de estrutura de tópicos contém outros itens, podem ser expandido ou recolhido pelo usuário. Um item expansível exibe um triângulo de abertura, que aponta para a direita quando o item é recolhido e aponta para baixo quando o item é expandido. Clicar no triângulo divulgação faz com que o item expandir ou recolher.

O modo de exibição de estrutura de tópicos (`NSOutlineView`) é uma subclasse da exibição de tabela (`NSTableView`) e como tal, herda grande parte de seu comportamento de sua classe pai. Como resultado, muitas operações com suporte de uma exibição de tabela, como selecionar linhas ou colunas, reposicionar colunas arrastando os cabeçalhos de coluna, etc., também têm suporte por uma exibição de estrutura de tópicos. Um aplicativo xamarin. MAC tem controle sobre esses recursos e parâmetros do modo de exibição de estrutura de tópicos (seja no código ou no construtor de Interface) pode ser configurados para permitir ou impedir que determinadas operações.

Uma exibição de estrutura de tópicos não armazena seus próprios dados, em vez disso, ele se baseia em uma fonte de dados (`NSOutlineViewDataSource`) para fornecer linhas e colunas necessárias em uma base conforme necessário.

Comportamento de uma exibição estrutura de tópicos pode ser personalizado, fornecendo uma subclasse do delegado de modo de exibição de estrutura de tópicos (`NSOutlineViewDelegate`) para dar suporte ao gerenciamento de coluna de estrutura de tópicos, digite para selecionar a funcionalidade, seleção de linha e de edição, acompanhamento personalizado e exibições personalizadas para um indivíduo colunas e linhas.

Uma vez que uma exibição de estrutura de tópicos compartilha grande parte da funcionalidade e seu comportamento com uma exibição de tabela, talvez você queira passar por nossos [modos de exibição de tabela](~/mac/user-interface/table-view.md) documentação antes de continuar com este artigo.

<a name="Creating_and_Maintaining_Outline_Views_in_Xcode" />

## <a name="creating-and-maintaining-outline-views-in-xcode"></a>Criar e manter modos de exibição de estrutura de tópicos no Xcode

Quando você cria um novo aplicativo xamarin. Mac Cocoa, você obtém uma janela de padrão em branco, por padrão. Esse windows é definido em um `.storyboard` arquivo incluído automaticamente no projeto. Para editar seu design do windows, nos **Gerenciador de soluções**, clique duas vezes o `Main.storyboard` arquivo:

[![](outline-view-images/edit01.png "Selecionando o storyboard principal")](outline-view-images/edit01.png#lightbox)

Isso abrirá o design de janela no Interface Builder do Xcode:

[![](outline-view-images/edit02.png "Editando a interface do usuário no Xcode")](outline-view-images/edit02.png#lightbox)

Tipo de `outline` para o **Inspetor de biblioteca** caixa de pesquisa para torná-lo mais fácil de encontrar os controles de exibição de estrutura de tópicos:

[![](outline-view-images/edit03.png "Selecionar uma exibição de estrutura de tópicos da biblioteca")](outline-view-images/edit03.png#lightbox)

Arraste um modo de exibição de estrutura de tópicos para o controlador de exibição na **Editor de Interface**, torná-lo a preencher a área de conteúdo do controlador de exibição e defina-a onde ele é reduzido e cresce com a janela na **Editor de restrição de**:

[![](outline-view-images/edit04.png "As restrições de edição")](outline-view-images/edit04.png#lightbox)

Selecione a exibição de estrutura de tópicos na **hierarquia de Interface** e as propriedades a seguir estão disponíveis na **Inspetor de atributo**:

[![](outline-view-images/edit05.png "O Inspetor de atributos")](outline-view-images/edit05.png#lightbox)

- **Coluna de estrutura de tópicos** -a coluna da tabela na qual os dados hierárquicos são exibidos.
- **Coluna de estrutura de tópicos de salvamento automático** - se `true`, a coluna de estrutura de tópicos será automaticamente salvo e restaurada entre as execuções do aplicativo.
- **Recuo** -a quantidade de recuo de colunas em um item expandido.
- **Recuo segue células** - se `true`, a marca de recuo será recuada, juntamente com as células.
- **Salvar itens expandida** - se `true`, o estado expandido/recolhido dos itens será automaticamente salvo e restaurado entre as execuções do aplicativo.
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
> A menos que você está mantendo um aplicativo xamarin. Mac herdado, `NSView` modos de exibição de estrutura de tópicos com base deve ser usados pela `NSCell` com base em exibições de tabela. `NSCell` é considerado herdados e não podem ter suporte no futuro.

Selecione uma coluna da tabela a **hierarquia de Interface** e as propriedades a seguir estão disponíveis na **Inspetor de atributo**:

[![](outline-view-images/edit06.png "O Inspetor de atributos")](outline-view-images/edit06.png#lightbox)

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

[![](outline-view-images/edit07.png "O Inspetor de atributos")](outline-view-images/edit07.png#lightbox)

Essas são todas as propriedades de uma exibição padrão. Você também tem a opção de redimensionar as linhas para essa coluna aqui.

Selecione uma célula de exibição de tabela (por padrão, esse é um `NSTextField`) na **hierarquia da Interface** e as propriedades a seguir estão disponíveis no **Inspetor de atributo**:

[![](outline-view-images/edit08.png "O Inspetor de atributos")](outline-view-images/edit08.png#lightbox)

Você terá todas as propriedades de um campo de texto padrão para definir aqui. Por padrão, um campo de texto padrão é usado para exibir dados de uma célula em uma coluna.

Selecione um modo de exibição de célula de tabela (`NSTableFieldCell`) na **hierarquia da Interface** e as propriedades a seguir estão disponíveis no **Inspetor de atributo**:

[![](outline-view-images/edit09.png "O Inspetor de atributos")](outline-view-images/edit09.png#lightbox)

As configurações mais importantes aqui são:

- **Layout** – selecione como as células nesta coluna são apresentadas.
- **Usa o modo de linha única** - se `true`, a célula está limitada a uma única linha.
- **Largura de Layout de tempo de execução do primeiro** - se `true`, a célula preferirá a largura definida para ele (manualmente ou automaticamente) quando ele é exibido na primeira vez em que o aplicativo é executado.
- **Ação** -controla quando a edição **ação** é enviada para a célula.
- **Comportamento** -define se uma célula é selecionável ou editável.
- **Rich Text** - se `true`, a célula pode exibir o texto formatado e com estilo.
- **Desfazer** - se `true`, a célula assume a responsabilidade para ele é desfazer o comportamento.

Selecione o modo de exibição de célula de tabela (`NSTableFieldCell`) na parte inferior de uma coluna da tabela o **hierarquia da Interface**:

[![](outline-view-images/edit11.png "Selecionar a exibição de célula de tabela")](outline-view-images/edit10.png#lightbox)

Isso permite que você edite a exibição de célula da tabela usada como base _padrão_ para todas as células criadas para a coluna especificada.

<a name="Adding_Actions_and_Outlets" />

### <a name="adding-actions-and-outlets"></a>Adicionar ações e as saídas

Assim como qualquer outro controle de interface do usuário do Cocoa, é preciso expor nossa exibição de estrutura de tópicos e colunas e células de código c# usando **ações** e **saídas** (com base na funcionalidade exigida).

O processo é o mesmo para qualquer elemento de estrutura de tópicos que desejemos expor:

1. Alterne para o **Editor assistente** e certifique-se de que o `ViewController.h` arquivo é selecionado: 

    [![](outline-view-images/edit11.png "Selecionar o arquivo. h correto")](outline-view-images/edit11.png#lightbox)
2. Selecione o modo de exibição de estrutura de tópicos do **hierarquia de Interface**, CTRL + clique e arraste para o `ViewController.h` arquivo.
3. Criar uma **tomada** para o modo de exibição de estrutura de tópicos chamado `ProductOutline`: 

    [![](outline-view-images/edit13.png "Configurando uma saída")](outline-view-images/edit13.png#lightbox)
4. Crie **tomadas** para as colunas de tabelas também chamado `ProductColumn` e `DetailsColumn`: 

    [![](outline-view-images/edit14.png "Configurando uma saída")](outline-view-images/edit14.png#lightbox)
5. Salvar as alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Em seguida, vamos escrever a exibição de código alguns dados para a estrutura de tópicos quando o aplicativo é executado.

<a name="Populating_the_Table_View" />

## <a name="populating-the-outline-view"></a>Preencher a exibição de estrutura de tópicos

Com nosso modo de exibição de estrutura de tópicos projetados no construtor de Interface e expostos por meio de um **tomada**, em seguida, precisamos criar o código c# para preenchê-lo.

Primeiro, vamos criar um novo `Product` classe para manter as informações para as linhas individuais e grupos de produtos sub. No **Gerenciador de soluções**, clique com botão direito no projeto e selecione **Add** > **novo arquivo...** Selecione **gerais** > **classe vazia**, digite `Product` para o **nome** e clique no **New** botão:

[![](outline-view-images/populate01.png "Criando uma classe vazia")](outline-view-images/populate01.png#lightbox)

Verifique o `Product.cs` arquivo são semelhantes ao seguinte:

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

Em seguida, precisamos criar uma subclasse de `NSOutlineDataSource` para fornecer os dados para nossa estrutura de tópicos, conforme necessário. No **Gerenciador de soluções**, clique com botão direito no projeto e selecione **Add** > **novo arquivo...** Selecione **gerais** > **classe vazia**, digite `ProductOutlineDataSource` para o **nome** e clique no **New** botão.

Editar o `ProductTableDataSource.cs` de arquivo e torná-lo semelhante ao seguinte:

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

Essa classe tem armazenamento para os itens da exibição da nossa estrutura de tópicos e substitui o `GetChildrenCount` para retornar o número de linhas na tabela. O `GetChild` retorna um item pai ou filho específico (conforme solicitado pelo modo de exibição de estrutura de tópicos) e o `ItemExpandable` define o item especificado como um pai ou filho.

Por fim, precisamos criar uma subclasse de `NSOutlineDelegate` para fornecer o comportamento para nossa estrutura de tópicos. No **Gerenciador de soluções**, clique com botão direito no projeto e selecione **Add** > **novo arquivo...** Selecione **gerais** > **classe vazia**, digite `ProductOutlineDelegate` para o **nome** e clique no **New** botão.

Editar o `ProductOutlineDelegate.cs` de arquivo e torná-lo semelhante ao seguinte:

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

Quando criamos uma instância das `ProductOutlineDelegate`, também passamos em uma instância da `ProductOutlineDataSource` que fornece os dados para a estrutura de tópicos. O `GetView` método é responsável por retornar uma exibição (dados) para exibir na célula de uma linha e coluna determinada. Se possível, uma exibição existente será reutilizada para exibi-la, se não deve ser criado um novo modo de exibição.

Para preencher a estrutura de tópicos, vamos editar o `MainWindow.cs` do arquivo e verifique o `AwakeFromNib` método são semelhantes ao seguinte:

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

Se executarmos o aplicativo, o seguinte é exibido:

[![](outline-view-images/populate02.png "Modo de exibição recolhido")](outline-view-images/populate02.png#lightbox)

Se podemos expandir um nó no modo de exibição de estrutura de tópicos, ele se parecerá com o seguinte:

[![](outline-view-images/populate03.png "O modo de exibição expandido")](outline-view-images/populate03.png#lightbox)

<a name="Sorting_by_Column" />

## <a name="sorting-by-column"></a>Classificar por coluna

Vamos permitir que o usuário classificar os dados na estrutura de tópicos clicando em um cabeçalho de coluna. Primeiro, clique duas vezes o `Main.storyboard` arquivo para abri-lo para edição no Interface Builder. Selecione o `Product` coluna, digite `Title` para o **chave de classificação**, `compare:` para o **seletor** e selecione `Ascending` para o **ordem**:

[![](outline-view-images/sort01.png "Definindo a ordem de chave de classificação")](outline-view-images/sort01.png#lightbox)

Salve suas alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Agora vamos editar o `ProductOutlineDataSource.cs` arquivo e adicione os seguintes métodos:

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

O `Sort` método nos permitem classificar os dados na fonte de dados com base em um determinado `Product` campo de classe em uma ordem crescente ou decrescente. Substituído `SortDescriptorsChanged` método será chamado sempre que o uso de clicar em um título de coluna. Ele será passado a **chave** valor que definimos no Interface Builder e a ordem de classificação para aquela coluna.

Se executar o aplicativo e clique nos cabeçalhos de coluna, as linhas serão classificadas por essa coluna:

[![](outline-view-images/sort02.png "Exemplo de saída classificada")](outline-view-images/sort02.png#lightbox)

<a name="Row_Selection" />

## <a name="row-selection"></a>Seleção de linha

Se você deseja permitir que o usuário seleciona uma única linha, clique duas vezes o `Main.storyboard` arquivo para abri-lo para edição no Interface Builder. Selecione a exibição de estrutura de tópicos na **hierarquia de Interface** e desmarque o **vários** caixa de seleção no **Inspetor de atributo**:

[![](outline-view-images/select01.png "O Inspetor de atributos")](outline-view-images/select01.png#lightbox)

Salve suas alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.


Em seguida, edite o `ProductOutlineDelegate.cs` arquivo e adicione o seguinte método:

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

Isso permitirá que o usuário selecione qualquer linha única na exibição de estrutura de tópicos. Retornar `false` para o `ShouldSelectItem` para qualquer item que você não deseja que o usuário seja capaz de selecionar ou `false` para cada item, se não quiser que o usuário seja capaz de selecionar todos os itens.

<a name="Multiple_Row_Selection" />

## <a name="multiple-row-selection"></a>Seleção de várias linhas

Se você deseja permitir que o usuário selecionar uma várias linhas, clique duas vezes o `Main.storyboard` arquivo para abri-lo para edição no Interface Builder. Selecione a exibição de estrutura de tópicos na **hierarquia de Interface** e verifique o **vários** caixa de seleção no **Inspetor de atributo**:

[![](outline-view-images/select02.png "O Inspetor de atributos")](outline-view-images/select02.png#lightbox)

Salve suas alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.


Em seguida, edite o `ProductOutlineDelegate.cs` arquivo e adicione o seguinte método:

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

Isso permitirá que o usuário selecione qualquer linha única na exibição de estrutura de tópicos. Retornar `false` para o `ShouldSelectRow` para qualquer item que você não deseja que o usuário seja capaz de selecionar ou `false` para cada item, se não quiser que o usuário seja capaz de selecionar todos os itens.

<a name="Type_to_Select_Row" />

## <a name="type-to-select-row"></a>Tipo para selecionar linha

Se você quiser permitir que o usuário digita um caractere com o modo de exibição de estrutura de tópicos selecionados e selecione a primeira linha que tem esse caractere, clique duas vezes o `Main.storyboard` arquivo para abri-lo para edição no Interface Builder. Selecione a exibição de estrutura de tópicos na **hierarquia de Interface** e verifique o **Selecionar tipo** caixa de seleção no **Inspetor de atributo**:

[![](outline-view-images/type01.png "O tipo de linha de edição")](outline-view-images/type01.png#lightbox)

Salve suas alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Agora vamos editar o `ProductOutlineDelegate.cs` arquivo e adicione o seguinte método:

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

O `GetNextTypeSelectMatch` leva a determinada `searchString` e retorna o item da primeira `Product` que contém essa cadeia de caracteres do `Title`.

<a name="Reordering_Columns" />

## <a name="reordering-columns"></a>Reordenar colunas

Se você quiser permitir que o usuário arraste reordenar colunas na exibição de estrutura de tópicos, clique duas vezes o `Main.storyboard` arquivo para abri-lo para edição no Interface Builder. Selecione a exibição de estrutura de tópicos na **hierarquia de Interface** e verifique o **reordenação** caixa de seleção no **Inspetor de atributo**:

[![](outline-view-images/reorder01.png "O Inspetor de atributos")](outline-view-images/reorder01.png#lightbox)

Se podemos fornecer um valor para o **Autosave** propriedade e verifique se o **informações de coluna** campo, qualquer alteração que podemos fazer layout da tabela serão salvas automaticamente para nós e restaurado na próxima vez que o aplicativo é executado.

Salve suas alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Agora vamos editar o `ProductOutlineDelegate.cs` arquivo e adicione o seguinte método:

```csharp
public override bool ShouldReorder (NSOutlineView outlineView, nint columnIndex, nint newColumnIndex)
{
    return true;
}
```

O `ShouldReorder` método deverá retornar `true` para qualquer coluna que ele deseja conceder permissão para ser reordenada, arrastar para o `newColumnIndex`, caso contrário retorne `false`;

Se executarmos o aplicativo, é possível arrastar cabeçalhos de coluna em torno de reordenar nossas colunas:

[![](outline-view-images/reorder02.png "Exemplo de reordenação de colunas")](outline-view-images/reorder02.png#lightbox)

<a name="Editing_Cells" />

## <a name="editing-cells"></a>Edição de células

Se você quiser permitir que o usuário edite os valores de uma determinada célula, edite o `ProductOutlineDelegate.cs` do arquivo e altere o `GetViewForItem` método da seguinte maneira:

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

Agora se executarmos o aplicativo, o usuário pode editar as células na exibição de tabela:

[![](outline-view-images/editing01.png "Um exemplo de edição de células")](outline-view-images/editing01.png#lightbox)

<a name="Using_Images_in_Outline_Views" />

## <a name="using-images-in-outline-views"></a>Usando imagens em modos de exibição de estrutura de tópicos

Para incluir uma imagem como parte da célula em uma `NSOutlineView`, você precisará alterar como os dados são retornados pela exibição de estrutura de tópicos `NSTableViewDelegate's` `GetView` método a ser usado um `NSTableCellView` em vez de típico `NSTextField`. Por exemplo:

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

Para obter mais informações, consulte o [usando imagens com modos de exibição de estrutura de tópicos](~/mac/app-fundamentals/image.md) seção do nosso [trabalhando com imagem](~/mac/app-fundamentals/image.md) documentação.

<a name="Data_Binding_Outline_Views" />

## <a name="data-binding-outline-views"></a>Modos de exibição de estrutura de tópicos de associação de dados

Usando técnicas de codificação de chave-valor e associação de dados em seu aplicativo xamarin. Mac, você pode diminuir consideravelmente a quantidade de código que você precisa para escrever e manter para popular e trabalhar com elementos de interface do usuário. Você também tem a vantagem de dissociar ainda mais seus dados de backup (_modelo de dados_) da sua frente terminar a Interface do usuário (_Model-View-Controller_), gerando mais fáceis de manter aplicativos mais flexíveis Design.

Codificação de chave-valor (KVC) é um mecanismo para acessar propriedades de um objeto indiretamente, usando métodos de acessador ou chaves (especialmente formatadas cadeias de caracteres) para identificar as propriedades em vez de acessá-los por meio de variáveis de instância (`get/set`). Com a implementação de acessadores em conformidade com a codificação de chave-valor em seu aplicativo xamarin. Mac, você obtém acesso a outros recursos do macOS como observando de chave-valor (KVO), associação de dados, dados principais, associações de Cocoa e ScriptableType.

Para obter mais informações, consulte o [associação de dados de exibição de estrutura de tópicos](~/mac/app-fundamentals/databinding.md#Outline_View_Data_Binding) seção do nosso [vinculação de dados e a codificação de chave-valor](~/mac/app-fundamentals/databinding.md) documentação.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo apresentou uma visão detalhada de como trabalhar com os modos de exibição em um aplicativo xamarin. Mac. Podemos viu os diferentes tipos e usos de modos de exibição de estrutura de tópicos, como criar e manter modos de exibição de estrutura de tópicos no Interface Builder do Xcode e como trabalhar com os modos de exibição no código c#.

## <a name="related-links"></a>Links relacionados

- [MacOutlines (amostra)](https://developer.xamarin.com/samples/mac/MacOutlines/)
- [MacImages (amostra)](https://developer.xamarin.com/samples/mac/MacImages/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Modos de exibição de tabela](~/mac/user-interface/table-view.md)
- [Listas de origem](~/mac/user-interface/source-list.md)
- [Associação de dados e a codificação de chave-valor](~/mac/app-fundamentals/databinding.md)
- [Diretrizes de interface humana do OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introdução aos modos de exibição de estrutura de tópicos](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/OutlineView/OutlineView.html#//apple_ref/doc/uid/10000023i)
- [NSOutlineView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSOutlineView_Class/index.html#//apple_ref/doc/uid/TP40004079)
- [NSOutlineViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSOutlineViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004175)
- [NSOutlineViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSOutlineViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008609)
