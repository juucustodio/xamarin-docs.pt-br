---
title: "Modos de exibição de estrutura de tópicos"
description: "Este artigo aborda a trabalhar com modos de exibição de estrutura de tópicos em um aplicativo Xamarin.Mac. Ele descreve a criar e manter modos de exibição de estrutura de tópicos no Xcode e o construtor de Interface e trabalhar com eles por meio de programação."
ms.topic: article
ms.prod: xamarin
ms.assetid: 043248EE-11DA-4E96-83A3-08824A4F2E01
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: dbbd10af046c0a8421e06e675364f92405b2317f
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="outline-views"></a>Modos de exibição de estrutura de tópicos

_Este artigo aborda a trabalhar com modos de exibição de estrutura de tópicos em um aplicativo Xamarin.Mac. Ele descreve a criar e manter modos de exibição de estrutura de tópicos no Xcode e o construtor de Interface e trabalhar com eles por meio de programação._

Ao trabalhar com c# e .NET em um aplicativo de Xamarin.Mac, você tem acesso à mesma estrutura de tópicos exibições que um desenvolvedor trabalhando em *Objective-C* e *Xcode* does. Como Xamarin.Mac se integra diretamente com o Xcode, você pode usar do Xcode _Interface construtor_ para criar e manter seus modos de exibição de estrutura de tópicos (ou, opcionalmente, criá-los diretamente no código do c#).

Uma exibição de estrutura de tópicos é um tipo de tabela que permite que o usuário expandir ou recolher linhas de dados hierárquicos. Como um modo de exibição de tabela, um modo de exibição de estrutura de tópicos exibe dados de um conjunto de itens relacionados, com linhas que representam itens individuais e colunas que representam os atributos desses itens. Ao contrário de um modo de exibição de tabela, itens em uma exibição de estrutura de tópicos não estão em uma lista simples, eles são organizados em uma hierarquia, como arquivos e pastas em um disco rígido.

[![](outline-view-images/populate03.png "Executar um aplicativo de exemplo")](outline-view-images/populate03.png#lightbox)

Neste artigo, vamos abordar os fundamentos de trabalhar com modos de exibição de estrutura de tópicos em um aplicativo Xamarin.Mac. É altamente recomendável que você leia o [Hello, Mac](~/mac/get-started/hello-mac.md) artigo primeiro, especificamente o [Introdução ao construtor da Interface e Xcode](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) e [tomadas e ações](~/mac/get-started/hello-mac.md#Outlets_and_Actions) seções, como ele aborda os principais conceitos e técnicas que será usado neste artigo.

Talvez você queira dar uma olhada a [classes expondo c# / métodos para Objective-C](~/mac/internals/how-it-works.md) seção o [Xamarin.Mac internos](~/mac/internals/how-it-works.md) de documento, ele explica o `Register` e `Export` comandos usado com a transmissão suas classes c# objetos Objective-C e elementos de interface do usuário.

<a name="Introduction_to_Outline_Views" />

## <a name="introduction-to-outline-views"></a>Introdução aos modos de exibição de estrutura de tópicos

Uma exibição de estrutura de tópicos é um tipo de tabela que permite que o usuário expandir ou recolher linhas de dados hierárquicos. Como um modo de exibição de tabela, um modo de exibição de estrutura de tópicos exibe dados de um conjunto de itens relacionados, com linhas que representam itens individuais e colunas que representam os atributos desses itens. Ao contrário de um modo de exibição de tabela, itens em uma exibição de estrutura de tópicos não estão em uma lista simples, eles são organizados em uma hierarquia, como arquivos e pastas em um disco rígido.

Se um item em uma exibição de estrutura de tópicos contém outros itens, ele pode ser expandido ou recolhido pelo usuário. Um item expansível exibe um triângulo de abertura, que aponta para a direita quando o item é recolhido e aponta para baixo quando o item é expandido. Clicando no triângulo divulgação faz com que o item expandir ou recolher.

O modo de exibição de estrutura de tópicos (`NSOutlineView`) é uma subclasse da exibição de tabela (`NSTableView`) e como tal, herda muito de seu comportamento de sua classe pai. Como resultado, muitas operações com suporte por uma exibição de tabela, como a seleção de linhas ou colunas, reposicionar colunas arrastando os cabeçalhos de coluna, etc., também têm suporte por uma exibição de estrutura de tópicos. Um aplicativo Xamarin.Mac tem controle sobre esses recursos e pode configurar parâmetros do modo de exibição de estrutura de tópicos (seja em código ou o construtor de Interface) para permitir ou impedir que determinadas operações.

Uma exibição de estrutura de tópicos não armazenar os dados de sua própria, em vez disso, ele se baseia em uma fonte de dados (`NSOutlineViewDataSource`) para fornecer linhas e colunas necessárias em uma base conforme necessário.

Comportamento de uma exibição estrutura de tópicos pode ser personalizado, fornecendo uma subclasse do representante de exibição de estrutura de tópicos (`NSOutlineViewDelegate`) para dar suporte ao gerenciamento de coluna de estrutura de tópicos, digite para selecionar a funcionalidade, seleção de linha e edição, controle personalizado e modos de exibição personalizados para individuais colunas e linhas.

Como uma exibição de estrutura de tópicos compartilha grande parte da funcionalidade e seu comportamento com um modo de exibição de tabela, talvez você queira percorrer nosso [modos de exibição de tabela](~/mac/user-interface/table-view.md) documentação antes de continuar com este artigo.

<a name="Creating_and_Maintaining_Outline_Views_in_Xcode" />

## <a name="creating-and-maintaining-outline-views-in-xcode"></a>Criar e manter os modos de exibição de estrutura de tópicos no Xcode

Quando você cria um novo aplicativo de Xamarin.Mac Cocoa, obtém uma janela em branco, padrão por padrão. Esse windows é definido em um `.storyboard` arquivo incluído automaticamente no projeto. Para editar o design do windows, o **Gerenciador de soluções**, clique duas vezes o `Main.storyboard` arquivo:

[![](outline-view-images/edit01.png "Selecionando o storyboard principal")](outline-view-images/edit01.png#lightbox)

Isso abrirá o design de janela no construtor de Interface do Xcode:

[![](outline-view-images/edit02.png "Editando a interface do usuário no Xcode")](outline-view-images/edit02.png#lightbox)

Tipo `outline` para o **Inspetor de biblioteca** caixa de pesquisa para tornar mais fácil localizar os controles de exibição de estrutura de tópicos:

[![](outline-view-images/edit03.png "Selecionar um modo de exibição de estrutura de tópicos da biblioteca")](outline-view-images/edit03.png#lightbox)

Arraste um modo de exibição de estrutura de tópicos para o controlador de exibição no **Editor de Interface**, torná-lo a preencher a área de conteúdo do controlador de exibição e defina-o para onde ele for reduzido e cresce com a janela no **Editor de restrição de**:

[![](outline-view-images/edit04.png "As restrições de edição")](outline-view-images/edit04.png#lightbox)

Selecione o modo de exibição de estrutura de tópicos no **hierarquia Interface** e as propriedades a seguir estão disponíveis no **Inspetor de atributo**:

[![](outline-view-images/edit05.png "O Inspetor de atributo")](outline-view-images/edit05.png#lightbox)

- **Coluna de estrutura de tópicos** -a coluna da tabela na qual os dados hierárquicos são exibidos.
- **Coluna de estrutura de tópicos de salvamento automático** - se `true`, a coluna de estrutura de tópicos será automaticamente salvo e restaurada entre o aplicativo é executado.
- **Recuo** -a quantidade de recuo de colunas em um item expandido.
- **Recuo segue células** - se `true`, a marca de recuo será recuada junto com as células.
- **Salvar itens expandido** - se `true`, o estado recolhido/expandida dos itens serão automaticamente salvas e restaurado entre o aplicativo é executado.
- **Modo de conteúdo** -permite que você use qualquer um dos modos de exibição (`NSView`) ou células (`NSCell`) para exibir os dados em linhas e colunas. A partir do macOS 10.7, você deve usar modos de exibição.
- **Flutua agrupar linhas** - se `true`, o modo de exibição de tabela desenhará células agrupadas como se eles são flutuantes.
- **Colunas** -define o número de colunas exibidas.
- **Cabeçalhos** - se `true`, as colunas têm cabeçalhos.
- **Reordenando** - se `true`, o usuário poderá arrastar reordenar colunas na tabela.
- **Redimensionando** - se `true`, o usuário poderá arrastar os cabeçalhos de coluna para redimensionar colunas.
- **Dimensionamento de colunas** -controla como a tabela automaticamente colunas de tamanho.
- **Realçar** -controla o tipo de realce a tabela usa quando uma célula é selecionada.
- **Alternar linhas** - se `true`, nunca outra linha terá uma cor de plano de fundo diferentes.
- **Grade horizontal** -seleciona o tipo de borda desenhada entre as células horizontalmente.
- **Grade vertical** -seleciona o tipo de borda desenhada verticalmente entre as células.
- **Cor da grade** -define a cor da borda.
- **Plano de fundo** -define a cor de plano de fundo de célula.
- **Seleção de** -lhe permitem controlar como o usuário pode selecionar células na tabela como:
    - **Vários** - se `true`, o usuário pode selecionar várias linhas e colunas.
    - **Coluna** - se `true`, o usuário pode selecionar colunas.
    - **Digite selecionar** - se `true`, o usuário pode digitar um caractere para selecionar uma linha.
    - **Vazio** - se `true`, não é necessário que o usuário selecione uma linha ou coluna, a tabela permite para nenhuma seleção em todos os.
- **Salvamento automático** -o nome que o formato de tabelas é automaticamente salvar em.
- **Informações de coluna** - se `true`, a ordem e a largura das colunas serão salvas automaticamente.
- **Quebras de linha** - selecione como a célula lida com quebras de linha.
- **Trunca a última linha visível** - se `true`, a célula será truncada no dados podem não se ajustarem dentro dos limites de TI.

> [!IMPORTANT]
> A menos que você está mantendo um aplicativo herdado do Xamarin.Mac, `NSView` modos de exibição de estrutura de tópicos com base deve ser usados em `NSCell` com base em modos de exibição de tabela. `NSCell` é considerado herdados e não podem ter suporte no futuro.

Selecione uma coluna da tabela de **hierarquia de Interface** e as propriedades a seguir estão disponíveis no **Inspetor de atributo**:

[![](outline-view-images/edit06.png "O Inspetor de atributo")](outline-view-images/edit06.png#lightbox)

- **Título** -define o título da coluna.
- **Alinhamento** -definir o alinhamento do texto dentro das células.
- **Fonte do título** -seleciona a fonte do texto do cabeçalho da célula.
- **Chave de classificação** -é a chave usada para classificar os dados da coluna. Deixe em branco se o usuário não pode classificar essa coluna.
- **Seletor de** -é o **ação** usado para executar a classificação. Deixe em branco se o usuário não pode classificar essa coluna.
- **Ordem** -é a ordem de classificação para os dados de colunas.
- **Redimensionando** -seleciona o tipo de redimensionamento para a coluna.
- **Editável** - se `true`, o usuário pode editar as células em uma tabela de célula com base.
- **Oculto** - se `true`, a coluna está oculta.

Você também pode redimensionar a coluna arrastando o seu identificador (verticalmente centralizados direita da coluna) à esquerda ou à direita.

Vamos selecionar a cada coluna em nossa tabela de exibição e dê a primeira coluna um **título** de `Product` e a segunda `Details`.

Selecione um modo de exibição de célula de tabela (`NSTableViewCell`) no **hierarquia Interface** e as propriedades a seguir estão disponíveis no **Inspetor de atributo**:

[![](outline-view-images/edit07.png "O Inspetor de atributo")](outline-view-images/edit07.png#lightbox)

Essas são todas as propriedades de uma exibição padrão. Você também tem a opção de redimensionamento de linhas para essa coluna aqui.

Selecione uma célula de exibição de tabela (por padrão, esse é um `NSTextField`) no **hierarquia de Interface** e as propriedades a seguir estão disponíveis no **Inspetor de atributo**:

[![](outline-view-images/edit08.png "O Inspetor de atributo")](outline-view-images/edit08.png#lightbox)

Você terá todas as propriedades de um campo de texto padrão para definir aqui. Por padrão, um campo de texto padrão é usado para exibir dados de uma célula em uma coluna.

Selecione um modo de exibição de célula de tabela (`NSTableFieldCell`) no **hierarquia Interface** e as propriedades a seguir estão disponíveis no **Inspetor de atributo**:

[![](outline-view-images/edit09.png "O Inspetor de atributo")](outline-view-images/edit09.png#lightbox)

As configurações mais importantes são:

- **Layout** - selecione como as células nesta coluna são dispostas.
- **Usa o modo de linha única** - se `true`, a célula está limitada a uma única linha.
- **Largura de Layout de tempo de execução do primeiro** - se `true`, a célula prefiram a largura definida para ele (manual ou automaticamente) quando ele for exibido na primeira vez em que o aplicativo é executado.
- **Ação** -controla quando a edição **ação** é enviada para a célula.
- **Comportamento** -define se uma célula é editável ou selecionável.
- **Rich Text** - se `true`, a célula pode exibir texto formatado e estilo.
- **Desfazer** - se `true`, a célula assume a responsabilidade de é Desfazer comportamento.

Selecione o modo de exibição de célula de tabela (`NSTableFieldCell`) na parte inferior de uma coluna da tabela de **hierarquia Interface**:

[![](outline-view-images/edit11.png "Selecionar a exibição de célula de tabela")](outline-view-images/edit10.png#lightbox)

Isso permite que você edite a exibição de célula de tabela usada como a base de _padrão_ para todas as células criadas para a coluna especificada.

<a name="Adding_Actions_and_Outlets" />

### <a name="adding-actions-and-outlets"></a>Adicionar ações e saídas

Assim como qualquer outro controle Cocoa da interface do usuário, é necessário para expor o nosso modo de estrutura de tópicos e colunas e células ao uso de código c# **ações** e **tomadas** (com base na funcionalidade exigida).

O processo é o mesmo para qualquer elemento de estrutura de tópicos que você deseja expor:

1. Alterne para o **Assistente Editor** e certifique-se de que o `ViewController.h` arquivo está selecionado: 

    [![](outline-view-images/edit11.png "Selecionando o arquivo correto. h")](outline-view-images/edit11.png#lightbox)
2. Selecione o modo de exibição de estrutura de tópicos do **hierarquia Interface**, control, clique e arraste para o `ViewController.h` arquivo.
3. Criar um **tomada** para o modo de exibição de estrutura de tópicos chamada `ProductOutline`: 

    [![](outline-view-images/edit13.png "Configurando uma tomada")](outline-view-images/edit13.png#lightbox)
4. Criar **tomadas** para as colunas de tabelas também chamado `ProductColumn` e `DetailsColumn`: 

    [![](outline-view-images/edit14.png "Configurando uma tomada")](outline-view-images/edit14.png#lightbox)
5. Salve a você as alterações e retornar ao Visual Studio para Mac sincronizar com o Xcode.

Em seguida, vamos escrever a exibição de código alguns dados para a estrutura de tópicos quando o aplicativo é executado.

<a name="Populating_the_Table_View" />

## <a name="populating-the-outline-view"></a>Preencher o modo de exibição de estrutura de tópicos

Com nosso modo de exibição de estrutura de tópicos criados no construtor de Interface e expostos por meio de um **tomada**, em seguida, precisamos criar o código c# para preenchê-lo.

Primeiro, vamos criar um novo `Product` classe para armazenar as informações para as linhas e grupos de produtos sub. No **Solution Explorer**, clique com o botão direito e selecione **adicionar** > **novo arquivo...** Selecione **geral** > **classe vazia**, digite `Product` para o **nome** e clique no **novo** botão:

[![](outline-view-images/populate01.png "Criando uma classe vazia")](outline-view-images/populate01.png#lightbox)

Verifique o `Product.cs` arquivo aparência semelhante ao seguinte:

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

Em seguida, é preciso criar uma subclasse de `NSOutlineDataSource` para fornecer os dados para nossa estrutura de tópicos, conforme necessário. No **Solution Explorer**, clique com o botão direito e selecione **adicionar** > **novo arquivo...** Selecione **geral** > **classe vazia**, digite `ProductOutlineDataSource` para o **nome** e clique no **novo** botão.

Editar o `ProductTableDataSource.cs` de arquivo e torná-lo a aparência a seguir:

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

Essa classe tem armazenamento para itens de nosso da exibição de estrutura de tópicos e substitui o `GetChildrenCount` para retornar o número de linhas na tabela. O `GetChild` retorna um item pai ou filho específico (conforme solicitado pelo modo de exibição de estrutura de tópicos) e o `ItemExpandable` define o item especificado como um pai ou filho.

Por fim, é preciso criar uma subclasse de `NSOutlineDelegate` para fornecer o comportamento para nossa estrutura de tópicos. No **Solution Explorer**, clique com o botão direito e selecione **adicionar** > **novo arquivo...** Selecione **geral** > **classe vazia**, digite `ProductOutlineDelegate` para o **nome** e clique no **novo** botão.

Editar o `ProductOutlineDelegate.cs` de arquivo e torná-lo a aparência a seguir:

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

Quando criamos uma instância do `ProductOutlineDelegate`, também podemos passar em uma instância das `ProductOutlineDataSource` que fornece os dados para a estrutura de tópicos. O `GetView` método é responsável por retornar uma exibição (dados) para exibir a célula de uma coluna determinada e a linha. Se possível, uma exibição existente será reutilizada para exibir a célula, caso não deve ser criado um novo modo de exibição.

Para preencher a estrutura de tópicos, vamos editar o `MainWindow.cs` de arquivo e verifique o `AwakeFromNib` método aparência semelhante ao seguinte:

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

Se o aplicativo é executado, é exibido o seguinte:

[![](outline-view-images/populate02.png "O modo de exibição recolhido")](outline-view-images/populate02.png#lightbox)

Se, expanda um nó no modo de exibição de estrutura de tópicos, ele se parecerá com o seguinte:

[![](outline-view-images/populate03.png "O modo de exibição expandido")](outline-view-images/populate03.png#lightbox)

<a name="Sorting_by_Column" />

## <a name="sorting-by-column"></a>Classificar por coluna

Vamos permitir que o usuário classificar os dados na estrutura de tópicos clicando em um cabeçalho de coluna. Primeiro, clique duas vezes o `Main.storyboard` arquivo para abri-lo para edição no construtor de Interface. Selecione o `Product` coluna, digite `Title` para o **chave de classificação**, `compare:` para o **seletor** e selecione `Ascending` para o **ordem**:

[![](outline-view-images/sort01.png "Definindo a ordem de chave de classificação")](outline-view-images/sort01.png#lightbox)

Salve suas alterações e retornar ao Visual Studio para Mac sincronizar com o Xcode.

Agora vamos editar o `ProductOutlineDataSource.cs` de arquivo e adicione os seguintes métodos:

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

O `Sort` método nos permitem classificar os dados na fonte de dados com base em um determinado `Product` campo de classe em crescente ou decrescente. Substituído `SortDescriptorsChanged` método será chamado sempre que o uso clica em um título de coluna. Ela será transmitida a **chave** valor que são definidas no construtor de Interface e a ordem de classificação para a coluna.

Se executar o aplicativo e clique nos cabeçalhos de coluna, as linhas serão classificadas por essa coluna:

[![](outline-view-images/sort02.png "Exemplo de saída classificada")](outline-view-images/sort02.png#lightbox)

<a name="Row_Selection" />

## <a name="row-selection"></a>Seleção de linha

Se você deseja permitir que o usuário selecione uma única linha, clique duas vezes o `Main.storyboard` arquivo para abri-lo para edição no construtor de Interface. Selecione o modo de exibição de estrutura de tópicos no **hierarquia Interface** e desmarque o **vários** caixa de seleção no **Inspetor de atributo**:

[![](outline-view-images/select01.png "O Inspetor de atributo")](outline-view-images/select01.png#lightbox)

Salve suas alterações e retornar ao Visual Studio para Mac sincronizar com o Xcode.


Em seguida, edite o `ProductOutlineDelegate.cs` de arquivo e adicione o seguinte método:

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

Isso permitirá que o usuário selecione qualquer linha única na exibição de estrutura de tópicos. Retornar `false` para o `ShouldSelectItem` para qualquer item que você não deseja que o usuário seja capaz de selecionar ou `false` para cada item, se você não deseja que o usuário seja capaz de selecionar todos os itens.

<a name="Multiple_Row_Selection" />

## <a name="multiple-row-selection"></a>Seleção de várias linhas

Se você deseja permitir que o usuário selecionar um várias linhas, clique duas vezes o `Main.storyboard` arquivo para abri-lo para edição no construtor de Interface. Selecione o modo de exibição de estrutura de tópicos no **hierarquia Interface** e verifique o **vários** caixa de seleção no **Inspetor de atributo**:

[![](outline-view-images/select02.png "O Inspetor de atributo")](outline-view-images/select02.png#lightbox)

Salve suas alterações e retornar ao Visual Studio para Mac sincronizar com o Xcode.


Em seguida, edite o `ProductOutlineDelegate.cs` de arquivo e adicione o seguinte método:

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

Isso permitirá que o usuário selecione qualquer linha única na exibição de estrutura de tópicos. Retornar `false` para o `ShouldSelectRow` para qualquer item que você não deseja que o usuário seja capaz de selecionar ou `false` para cada item, se você não deseja que o usuário seja capaz de selecionar todos os itens.

<a name="Type_to_Select_Row" />

## <a name="type-to-select-row"></a>Tipo para selecionar a linha

Se você deseja permitir que o usuário digita um caractere com o modo de exibição de estrutura de tópicos selecionado e selecione a primeira linha com esse caractere, clique duas vezes o `Main.storyboard` arquivo para abri-lo para edição no construtor de Interface. Selecione a exibição de estrutura de tópicos no **hierarquia Interface** e verifique o **Selecionar tipo** caixa de seleção no **Inspetor de atributo**:

[![](outline-view-images/type01.png "O tipo de linha de edição")](outline-view-images/type01.png#lightbox)

Salve suas alterações e retornar ao Visual Studio para Mac sincronizar com o Xcode.

Agora vamos editar o `ProductOutlineDelegate.cs` de arquivo e adicione o seguinte método:

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

O `GetNextTypeSelectMatch` leva a determinado `searchString` e retorna o item do primeiro `Product` com essa cadeia de caracteres em seu `Title`.

<a name="Reordering_Columns" />

## <a name="reordering-columns"></a>Reordenar colunas

Se você quiser permitir que o usuário arrasta reordenar colunas na exibição de estrutura de tópicos, clique duas vezes o `Main.storyboard` arquivo para abri-lo para edição no construtor de Interface. Selecione o modo de exibição de estrutura de tópicos no **hierarquia Interface** e verifique o **reordenação** caixa de seleção no **Inspetor de atributo**:

[![](outline-view-images/reorder01.png "O Inspetor de atributo")](outline-view-images/reorder01.png#lightbox)

Se podemos fornecer um valor para o **salvamento automático** propriedade e verifique se o **informações de coluna** campo, quaisquer alterações que podemos fazer layout da tabela serão salvas automaticamente para nós e restaurados na próxima vez que o aplicativo é executado.

Salve suas alterações e retornar ao Visual Studio para Mac sincronizar com o Xcode.

Agora vamos editar o `ProductOutlineDelegate.cs` de arquivo e adicione o seguinte método:

```csharp
public override bool ShouldReorder (NSOutlineView outlineView, nint columnIndex, nint newColumnIndex)
{
    return true;
}
```

O `ShouldReorder` método deve retornar `true` para qualquer coluna que deseja que ele possa ser arraste reordenadas no `newColumnIndex`, caso contrário retorne `false`;

Se o aplicativo é executado, é possível arrastar os cabeçalhos de coluna em torno reordenar nossas colunas:

[![](outline-view-images/reorder02.png "Exemplo de reordenação de colunas")](outline-view-images/reorder02.png#lightbox)

<a name="Editing_Cells" />

## <a name="editing-cells"></a>Edição de células

Se você quiser permitir que o usuário edite os valores de uma determinada célula, edite o `ProductOutlineDelegate.cs` de arquivo e altere o `GetViewForItem` método da seguinte maneira:

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

Agora se o aplicativo é executado, o usuário pode editar as células no modo de exibição de tabela:

[![](outline-view-images/editing01.png "Um exemplo de edição de células")](outline-view-images/editing01.png#lightbox)

<a name="Using_Images_in_Outline_Views" />

## <a name="using-images-in-outline-views"></a>Usando imagens em modos de exibição de estrutura de tópicos

Para incluir uma imagem como parte da célula de uma `NSOutlineView`, você precisará alterar como os dados são retornados pela exibição de estrutura de tópicos `NSTableViewDelegate's` `GetView` método para usar um `NSTableCellView` em vez do típico `NSTextField`. Por exemplo:

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

Usando técnicas de codificação de chave-valor e associação de dados em seu aplicativo Xamarin.Mac, você pode diminuir significativamente a quantidade de código que você precisa escrever e manter para preencher e trabalhar com elementos de interface do usuário. Você também tem a vantagem de desligamento ainda mais seus dados de backup (_modelo de dados_) do seu front end Interface do usuário (_Model-View-Controller_), à esquerda para mais fáceis de manter, mais flexível de aplicativos Design.

Codificação de chave-valor (KVC) é um mecanismo para acessar propriedades de um objeto indiretamente, usando métodos de acessador ou chaves (especialmente formatadas cadeias de caracteres) para identificar as propriedades em vez de acessá-los por meio de variáveis de instância (`get/set`). Ao implementar acessadores compatível com codificação de chave-valor em seu aplicativo Xamarin.Mac, você obtém acesso a outros recursos de macOS como chave-valor observando (KVO), associação de dados, dados principais, associações de Cocoa e ScriptableType.

Para obter mais informações, consulte o [associação de dados de exibição de estrutura de tópicos](~/mac/app-fundamentals/databinding.md#Outline_View_Data_Binding) seção do nosso [associação de dados e a codificação de chave-valor](~/mac/app-fundamentals/databinding.md) documentação.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo obteve uma visão detalhada de como trabalhar com os modos de exibição em um aplicativo Xamarin.Mac. Estamos viu os diferentes tipos e usos de modos de exibição de estrutura de tópicos, como criar e manter os modos de exibição de estrutura de tópicos no construtor de Interface do Xcode e como trabalhar com modos de exibição de estrutura de tópicos no código c#.

## <a name="related-links"></a>Links relacionados

- [MacOutlines (exemplo)](https://developer.xamarin.com/samples/mac/MacOutlines/)
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
