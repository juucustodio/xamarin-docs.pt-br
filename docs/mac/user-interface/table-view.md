---
title: Exibições de tabela no Xamarin. Mac
description: Este artigo aborda como trabalhar com exibições de tabela em um aplicativo Xamarin. Mac. Ele descreve a criação de exibições de tabela no Xcode e Interface Builder e interagir com elas no código.
ms.prod: xamarin
ms.assetid: 3B55B858-4769-4331-966A-7F53B3B7C720
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: 7b341ee5ee72c3a89ab14161862896585ed498fc
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772695"
---
# <a name="table-views-in-xamarinmac"></a>Exibições de tabela no Xamarin. Mac

_Este artigo aborda como trabalhar com exibições de tabela em um aplicativo Xamarin. Mac. Ele descreve a criação de exibições de tabela no Xcode e Interface Builder e interagir com elas no código._

Ao trabalhar com C# o e o .net em um aplicativo Xamarin. Mac, você tem acesso às mesmas exibições de tabela que um desenvolvedor trabalhando no *Objective-C e no* *Xcode* . Como o Xamarin. Mac se integra diretamente com o Xcode, você pode usar o _interface Builder_ do Xcode para criar e manter suas exibições de tabela (ou, C# opcionalmente, criá-las diretamente no código).

Uma exibição de tabela exibe dados em um formato tabular contendo uma ou mais colunas de informações em várias linhas. Com base no tipo de exibição de tabela que está sendo criada, o usuário pode classificar por coluna, reorganizar colunas, adicionar colunas, remover colunas ou editar os dados contidos na tabela.

[![](table-view-images/intro01.png "Uma tabela de exemplo")](table-view-images/intro01.png#lightbox)

Neste artigo, abordaremos as noções básicas de como trabalhar com exibições de tabela em um aplicativo Xamarin. Mac. É altamente recomendável que você trabalhe pelo artigo [Hello, Mac](~/mac/get-started/hello-mac.md) primeiro, especificamente a [introdução às seções Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [ações](~/mac/get-started/hello-mac.md#outlets-and-actions) , pois ela aborda os principais conceitos e técnicas que usaremos em Este artigo.

Talvez você queira dar uma olhada na seção [expondo C# classes/métodos para Objective-C](~/mac/internals/how-it-works.md) do documento [interno do Xamarin. Mac](~/mac/internals/how-it-works.md) também, explica os `Register` comandos e `Export` usados para conectar suas C# classes ao Objetos Objective-C e elementos de interface do usuário.

<a name="Introduction_to_Table_Views" />

## <a name="introduction-to-table-views"></a>Introdução às exibições de tabela

Uma exibição de tabela exibe dados em um formato tabular contendo uma ou mais colunas de informações em várias linhas. As exibições de tabela são exibidas dentro das`NSScrollView`exibições de rolagem () e a partir do `NSView` MacOS 10,7, você`NSCell`pode usar qualquer um em vez de células () para exibir linhas e colunas. Dito isso, você ainda pode usar `NSCell` o no entanto, normalmente criará uma subclasse `NSTableCellView` e criaremos suas linhas e colunas personalizadas.

Um modo de exibição de tabela não armazena seus próprios dados, em vez disso, ele se baseia em`NSTableViewDataSource`uma fonte de dados () para fornecer as linhas e colunas necessárias, de acordo com a necessidade.

O comportamento de uma exibição de tabela pode ser personalizado fornecendo uma subclasse do delegado de exibição de tabela`NSTableViewDelegate`() para oferecer suporte ao gerenciamento de coluna de tabela, tipo para selecionar funcionalidade, seleção de linha e edição, acompanhamento personalizado e exibições personalizadas para colunas individuais e as.

Ao criar exibições de tabela, a Apple sugere o seguinte:

- Permita que o usuário classifique a tabela clicando em um cabeçalho de coluna.
- Crie cabeçalhos de coluna que sejam substantivos ou frases substantivo curtas que descrevam os dados que estão sendo mostrados nessa coluna.

Para obter mais informações, consulte a seção [exibições de conteúdo](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsView.html#//apple_ref/doc/uid/20000957-CH52-SW1) das [diretrizes de interface humana do os X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)da Apple.

<a name="Creating-and-Maintaining-Table-Views-in-Xcode" />

## <a name="creating-and-maintaining-table-views-in-xcode"></a>Criando e mantendo exibições de tabela no Xcode

Ao criar um novo aplicativo Xamarin. Mac Cocoa, você obtém uma janela padrão em branco, por padrão. Essas janelas são definidas em um `.storyboard` arquivo incluído automaticamente no projeto. Para editar o design do Windows, na **Gerenciador de soluções**, clique duas vezes `Main.storyboard` no arquivo:

[![](table-view-images/edit01.png "Selecionando o storyboard principal")](table-view-images/edit01.png#lightbox)

Isso abrirá o design da janela na Interface Builder do Xcode:

[![](table-view-images/edit02.png "Editando a interface do usuário no Xcode")](table-view-images/edit02.png#lightbox)

Digite `table` na caixa de pesquisa **do Inspetor de biblioteca** para facilitar a localização dos controles de exibição de tabela:

[![](table-view-images/edit03.png "Selecionando uma exibição de tabela na biblioteca")](table-view-images/edit03.png#lightbox)

Arraste um modo de exibição de tabela para o controlador de exibição no **Editor de interface**, faça com que ele preencha a área de conteúdo do controlador de exibição e defina-o para o local em que ele é reduzido e cresce com a janela no **Editor de restrição**:

[![](table-view-images/edit04.png "Restrições de edição")](table-view-images/edit04.png#lightbox)

Selecione o modo de exibição de tabela na **hierarquia de interface** e as seguintes propriedades estão disponíveis no **Inspetor de atributo**:

[![](table-view-images/edit05.png "O Inspetor de atributo")](table-view-images/edit05.png#lightbox)

- **Modo de conteúdo** – permite que você use views`NSView`() ou Cells (`NSCell`) para exibir os dados nas linhas e colunas. A partir do macOS 10,7, você deve usar exibições.
- **Flutua linhas de grupo** – se `true`, a exibição de tabela desenhará células agrupadas como se elas estivessem flutuantes.
- **Colunas** – define o número de colunas exibidas.
- **Cabeçalhos** -se `true`, as colunas terão cabeçalhos.
- **Reordenando** -se `true`, o usuário poderá arrastar para reordenar as colunas na tabela.
- **Redimensionamento** – se `true`, o usuário poderá arrastar cabeçalhos de coluna para redimensionar colunas.
- **Dimensionamento de coluna** – controla como a tabela dimensionará automaticamente as colunas.
- **Highlight** -controla o tipo de realce que a tabela usa quando uma célula é selecionada.
- **Linhas alternativas** -se `true`, em qualquer outra linha terá uma cor de plano de fundo diferente.
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
- **Informações da coluna** - `true`se, a ordem e a largura das colunas serão salvas automaticamente.
- **Quebras de linha** – selecione como a célula lida com quebras de linha.
- **Trunca a última linha visível** -se `true`, a célula será truncada nos dados não puder se ajustar dentro de seus limites.

> [!IMPORTANT]
> A menos que você esteja mantendo um aplicativo Xamarin. Mac `NSView` herdado, as exibições de `NSCell` tabela baseadas devem ser usadas em exibições de tabela baseadas em. `NSCell`é considerado herdado e pode não ter suporte no futuro.

Selecione uma coluna de tabela na **hierarquia de interface** e as seguintes propriedades estão disponíveis no **Inspetor de atributo**:

[![](table-view-images/edit06.png "O Inspetor de atributo")](table-view-images/edit06.png#lightbox)

- **Título** – define o título da coluna.
- **Alinhamento** – defina o alinhamento do texto dentro das células.
- **Fonte do título** – seleciona a fonte do texto do cabeçalho da célula.
- **Chave de classificação** -é a chave usada para classificar dados na coluna. Deixe em branco se o usuário não puder classificar esta coluna.
- **Selector** – a **ação** usada para executar a classificação. Deixe em branco se o usuário não puder classificar esta coluna.
- **Order** – a ordem de classificação para os dados das colunas.
- **Redimensionamento** – seleciona o tipo de redimensionamento para a coluna.
- **Editable** -If `true`, o usuário pode editar células em uma tabela baseada em célula.
- **Hidden** -If `true`, a coluna fica oculta.

Você também pode redimensionar a coluna arrastando sua alça (verticalmente centralizada no lado direito da coluna) à esquerda ou à direita.

Vamos selecionar cada coluna em nosso modo de exibição de `Product` tabela e dar à primeira coluna um **título** `Details`e a segunda.

Selecione uma exibição de célula de`NSTableViewCell`tabela () na **hierarquia de interface** e as seguintes propriedades estão disponíveis no Inspetor de **atributo**:

[![](table-view-images/edit07.png "O Inspetor de atributo")](table-view-images/edit07.png#lightbox)

Essas são todas as propriedades de uma exibição padrão. Você também tem a opção de redimensionar as linhas desta coluna aqui.

Selecione uma célula de exibição de tabela (por padrão, isso `NSTextField`é um) na **hierarquia de interface** e as seguintes propriedades estão disponíveis no Inspetor de **atributo**:

[![](table-view-images/edit08.png "O Inspetor de atributo")](table-view-images/edit08.png#lightbox)

Você terá todas as propriedades de um campo de texto padrão para definir aqui. Por padrão, um campo de texto padrão é usado para exibir dados de uma célula em uma coluna.

Selecione uma exibição de célula de`NSTableFieldCell`tabela () na **hierarquia de interface** e as seguintes propriedades estão disponíveis no Inspetor de **atributo**:

[![](table-view-images/edit09.png "O Inspetor de atributo")](table-view-images/edit09.png#lightbox)

As configurações mais importantes aqui são:

- **Layout** – selecione como as células nesta coluna são dispostos.
- **Usa o modo de linha única** -se `true`, a célula é limitada a uma única linha.
- **Largura do primeiro layout** de tempo `true`de execução-se, a célula preferirá a largura definida para ela (manual ou automaticamente) quando ela for exibida na primeira vez em que o aplicativo for executado.
- **Ação** -controla quando a **ação** de edição é enviada para a célula.
- **Comportamento** – define se uma célula é selecionável ou editável.
- **Rich Text** – se `true`, a célula pode exibir texto formatado e com estilo.
- **Undo** -If `true`, a célula assume a responsabilidade pelo comportamento de desfazer.

Selecione a exibição de célula de`NSTableFieldCell`tabela () na parte inferior de uma coluna de tabela na **hierarquia de interface**:

[![](table-view-images/edit10.png "Selecionando a exibição de célula da tabela")](table-view-images/edit10.png#lightbox)

Isso permite que você edite a exibição de célula de tabela usada como _padrão_ de base para todas as células criadas para a coluna especificada.

<a name="Adding_Actions_and_Outlets" />

### <a name="adding-actions-and-outlets"></a>Adicionando ações e saídas

Assim como qualquer outro controle de interface do usuário do Cocoa, precisamos expor nossa exibição de tabela e suas colunas e C# células para codificar usando **ações** e **saídas** (com base na funcionalidade necessária).

O processo é o mesmo para qualquer elemento de exibição de tabela que desejamos expor:

1. Alterne para o **Editor do assistente** e verifique se `ViewController.h` o arquivo está selecionado: 

    [![](table-view-images/edit11.png "O editor do assistente")](table-view-images/edit11.png#lightbox)
2. Selecione o modo de exibição de tabela na **hierarquia de interface**, clique no botão de `ViewController.h` controle e arraste-o para o arquivo.
3. Crie uma **tomada** para a exibição de tabela `ProductTable`chamada: 

    [![](table-view-images/edit13.png "Configurando uma tomada")](table-view-images/edit13.png#lightbox)
4. Crie **saídas** para as colunas de tabelas também chamadas `ProductColumn` e: `DetailsColumn` 

    [![](table-view-images/edit14.png "Configurando uma tomada")](table-view-images/edit14.png#lightbox)
5. Salve as alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Em seguida, escreveremos o código para exibir alguns dados da tabela quando o aplicativo for executado.

<a name="Populating_the_Table_View" />

## <a name="populating-the-table-view"></a>Preenchendo a exibição de tabela

Com o modo de exibição de tabela criado em Interface Builder e exposto por meio de uma **tomada**, em C# seguida, precisamos criar o código para preenchê-lo.

Primeiro, vamos criar uma nova `Product` classe para manter as informações das linhas individuais. Na **Gerenciador de soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar** > **novo arquivo...** Selecione > **classe vazia**geral, insira `Product` para o **nome** e clique no botão **novo** :

[![](table-view-images/populate01.png "Criando uma classe vazia")](table-view-images/populate01.png#lightbox)

Faça com `Product.cs` que o arquivo se pareça com o seguinte:

```csharp
using System;

namespace MacTables
{
  public class Product
  {
    #region Computed Properties
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

Em seguida, precisamos criar uma subclasse de `NSTableDataSource` para fornecer os dados da nossa tabela conforme solicitado. Na **Gerenciador de soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar** > **novo arquivo...** Selecione > **classe vazia**geral, insira `ProductTableDataSource` para o **nome** e clique no botão **novo** .

Edite `ProductTableDataSource.cs` o arquivo e faça com que ele se pareça com o seguinte:

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

Essa classe tem armazenamento para os itens de exibição de tabela e substitui `GetRowCount` o para retornar o número de linhas na tabela.

Por fim, precisamos criar uma subclasse de `NSTableDelegate` para fornecer o comportamento da nossa tabela. Na **Gerenciador de soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar** > **novo arquivo...** Selecione > **classe vazia**geral, insira `ProductTableDelegate` para o **nome** e clique no botão **novo** .

Edite `ProductTableDelegate.cs` o arquivo e faça com que ele se pareça com o seguinte:

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

Quando criamos uma instância do `ProductTableDelegate`, também passamos uma instância `ProductTableDataSource` do que fornece os dados para a tabela. O `GetViewForItem` método é responsável por retornar uma exibição (dados) para exibir a célula de uma coluna e uma linha. Se possível, uma exibição existente será reutilizada para exibir a célula, se não for necessário criar uma nova exibição.

Para preencher a tabela, vamos editar o `ViewController.cs` arquivo e fazer com que o `AwakeFromNib` método fique semelhante ao seguinte:

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

Se executarmos o aplicativo, o seguinte será exibido:

[![](table-view-images/populate02.png "Uma execução de aplicativo de exemplo")](table-view-images/populate02.png#lightbox)

<a name="Sorting_by_Column" />

## <a name="sorting-by-column"></a>Classificando por coluna

Vamos permitir que o usuário classifique os dados na tabela clicando em um cabeçalho de coluna. Primeiro, clique duas vezes no `Main.storyboard` arquivo para abri-lo para edição no interface Builder. Selecione a `Product` coluna, insira `Title` para a **chave**de classificação `compare:` , para o **seletor** e selecione `Ascending` para a **ordem**:

[![](table-view-images/sort01.png "Definindo a chave de classificação")](table-view-images/sort01.png#lightbox)

Selecione a `Details` coluna, insira `Description` para a **chave**de classificação `compare:` , para o **seletor** e selecione `Ascending` para a **ordem**:

[![](table-view-images/sort02.png "Definindo a chave de classificação")](table-view-images/sort02.png#lightbox)

Salve as alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Agora, vamos editar o `ProductTableDataSource.cs` arquivo e adicionar os seguintes métodos:

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

O `Sort` método nos permite classificar os dados na fonte de dados com base em um determinado `Product` campo de classe em ordem crescente ou decrescente. O método `SortDescriptorsChanged` substituído será chamado toda vez que o uso for clicado em um título de coluna. Ele passará o valor de **chave** que definimos em interface Builder e a ordem de classificação para essa coluna.

Se executarmos o aplicativo e clicarmos nos cabeçalhos de coluna, as linhas serão classificadas por essa coluna:

[![](table-view-images/sort03.png "Uma execução de aplicativo de exemplo")](table-view-images/sort03.png#lightbox)

<a name="Row_Selection" />

## <a name="row-selection"></a>Seleção de linha

Se você quiser permitir que o usuário selecione uma única linha, clique duas vezes no `Main.storyboard` arquivo para abri-lo para edição no interface Builder. Selecione o modo de exibição de tabela na **hierarquia de interface** e desmarque a caixa de seleção **vários** no **Inspetor de atributo**:

[![](table-view-images/select01.png "O Inspetor de atributo")](table-view-images/select01.png#lightbox)

Salve as alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Em seguida, edite o `ProductTableDelegate.cs` arquivo e adicione o seguinte método:

```csharp
public override bool ShouldSelectRow (NSTableView tableView, nint row)
{
  return true;
}
```

Isso permitirá que o usuário selecione uma única linha no modo de exibição de tabela. Retorne `false` para `ShouldSelectRow` o para qualquer linha que você não deseja que o usuário possa selecionar ou `false` para cada linha se não quiser que o usuário seja capaz de selecionar linhas.

O modo de exibição`NSTableView`de tabela () contém os seguintes métodos para trabalhar com a seleção de linha:

- `DeselectRow(nint)`-Anula a seleção da linha determinada na tabela.
- `SelectRow(nint,bool)`-Seleciona a linha determinada. Passe `false` para o segundo parâmetro selecionar apenas uma linha por vez.
- `SelectedRow`-Retorna a linha atual selecionada na tabela.
- `IsRowSelected(nint)`-Retorna `true` se a linha fornecida está selecionada.

<a name="Multiple_Row_Selection" />

## <a name="multiple-row-selection"></a>Seleção de várias linhas

Se você quiser permitir que o usuário selecione várias linhas, clique duas vezes no `Main.storyboard` arquivo para abri-lo para edição no interface Builder. Selecione o modo de exibição de tabela na **hierarquia de interface** e marque a caixa de seleção **vários** no **Inspetor de atributo**:

[![](table-view-images/select02.png "O Inspetor de atributo")](table-view-images/select02.png#lightbox)

Salve as alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Em seguida, edite o `ProductTableDelegate.cs` arquivo e adicione o seguinte método:

```csharp
public override bool ShouldSelectRow (NSTableView tableView, nint row)
{
  return true;
}
```

Isso permitirá que o usuário selecione uma única linha no modo de exibição de tabela. Retorne `false` para `ShouldSelectRow` o para qualquer linha que você não deseja que o usuário possa selecionar ou `false` para cada linha se não quiser que o usuário seja capaz de selecionar linhas.

O modo de exibição`NSTableView`de tabela () contém os seguintes métodos para trabalhar com a seleção de linha:

- `DeselectAll(NSObject)`-Anula a seleção de todas as linhas na tabela. Use `this` para o primeiro parâmetro a ser enviado no objeto, fazendo a seleção. 
- `DeselectRow(nint)`-Anula a seleção da linha determinada na tabela.
- `SelectAll(NSobject)`– Seleciona todas as linhas na tabela. Use `this` para o primeiro parâmetro a ser enviado no objeto, fazendo a seleção.
- `SelectRow(nint,bool)`-Seleciona a linha determinada. Pass `false` para o segundo parâmetro Limpe a seleção e selecione apenas uma única linha, passe `true` para estender a seleção e inclua essa linha.
- `SelectRows(NSIndexSet,bool)`-Seleciona o conjunto de linhas fornecido. Pass `false` para o segundo parâmetro desmarque a seleção e selecione apenas uma dessas linhas, `true` passe para estender a seleção e inclua essas linhas.
- `SelectedRow`-Retorna a linha atual selecionada na tabela.
- `SelectedRows`-Retorna um `NSIndexSet` que contém os índices das linhas selecionadas.
- `SelectedRowCount`-Retorna o número de linhas selecionadas.
- `IsRowSelected(nint)`-Retorna `true` se a linha fornecida está selecionada.

<a name="Type_to_Select_Row" />

## <a name="type-to-select-row"></a>Digite para selecionar a linha

Se você quiser permitir que o usuário digite um caractere com o modo de exibição de tabela selecionado e selecione a primeira linha que tem esse caractere, clique duas `Main.storyboard` vezes no arquivo para abri-lo para edição no interface Builder. Selecione o modo de exibição de tabela na **hierarquia de interface** e marque a caixa de seleção **tipo selecionar** no **Inspetor de atributo**:

[![](table-view-images/type01.png "Definindo o tipo de seleção")](table-view-images/type01.png#lightbox)

Salve as alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Agora, vamos editar o `ProductTableDelegate.cs` arquivo e adicionar o seguinte método:

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

O `GetNextTypeSelectMatch` método usa o dado `searchString` e retorna a linha do primeiro `Product` que tem essa cadeia de caracteres `Title`.

Se executarmos o aplicativo e digitarmos um caractere, uma linha será selecionada:

[![](table-view-images/type02.png "Uma execução de aplicativo de exemplo")](table-view-images/type02.png#lightbox)

<a name="Reordering_Columns" />

## <a name="reordering-columns"></a>Reordenando colunas

Se você quiser permitir que o usuário Arraste colunas de reordenação na exibição de tabela, clique duas vezes `Main.storyboard` no arquivo para abri-lo para edição no interface Builder. Selecione o modo de exibição de tabela na **hierarquia de interface** e marque a caixa de seleção **Reordenar** no **Inspetor de atributo**:

[![](table-view-images/reorder01.png "O Inspetor de atributo")](table-view-images/reorder01.png#lightbox)

Se fornecermos um valor para a **Propriedade** autosave e verificar o campo de **informações da coluna** , as alterações feitas no layout da tabela serão salvas automaticamente para nós e restauradas na próxima vez em que o aplicativo for executado.

Salve as alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Agora, vamos editar o `ProductTableDelegate.cs` arquivo e adicionar o seguinte método:

```csharp
public override bool ShouldReorder (NSTableView tableView, nint columnIndex, nint newColumnIndex)
{
  return true;
}
```

O `ShouldReorder` método deve retornar `true` para qualquer coluna que queira permitir que seja arrastada reordenada para o `newColumnIndex`, caso contrário, `false`retorna;

Se executarmos o aplicativo, podemos arrastar cabeçalhos de coluna para reordenar nossas colunas:

[![](table-view-images/reorder02.png "Um exemplo das colunas reordenadas")](table-view-images/reorder02.png#lightbox)

<a name="Editing_Cells" />

## <a name="editing-cells"></a>Editando células

Se você quiser permitir que o usuário edite os valores de uma determinada célula, edite `ProductTableDelegate.cs` o arquivo e altere `GetViewForItem` o método da seguinte maneira:

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

Agora, se executarmos o aplicativo, o usuário poderá editar as células na exibição de tabela:

[![](table-view-images/editing01.png "Um exemplo de edição de uma célula")](table-view-images/editing01.png#lightbox)

<a name="Using_Images_in_Table_Views" />

## <a name="using-images-in-table-views"></a>Usando imagens em exibições de tabela

Para incluir uma imagem como parte `NSTableView`da célula em um, você precisará alterar a forma como os dados são retornados pelo método da `NSTableViewDelegate's` `GetViewForItem` exibição de tabela para usar um `NSTableCellView` em vez do típico `NSTextField`. Por exemplo:

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

Para obter mais informações, consulte a seção [usando imagens com exibições de tabela](~/mac/app-fundamentals/image.md) de nossa documentação de [trabalho com imagem](~/mac/app-fundamentals/image.md) .

<a name="Adding-a-Delete-Button-to-a-Row" />

## <a name="adding-a-delete-button-to-a-row"></a>Adicionando um botão de exclusão a uma linha

Com base nos requisitos do seu aplicativo, pode haver ocasiões em que você precisa fornecer um botão de ação para cada linha na tabela. Como exemplo, vamos expandir o exemplo de exibição de tabela criado acima para incluir um botão de **exclusão** em cada linha.

Primeiro, edite `Main.storyboard` o no interface Builder do Xcode, selecione o modo de exibição de tabela e aumente o número de colunas para três (3). Em seguida, altere o **título** da nova coluna para `Action`:

[![](table-view-images/delete01.png "Editando o nome da coluna")](table-view-images/delete01.png#lightbox)

Salve as alterações no storyboard e retorne a Visual Studio para Mac para sincronizar as alterações.

Em seguida, edite o `ViewController.cs` arquivo e adicione o seguinte método público:

```csharp
public void ReloadTable ()
{
  ProductTable.ReloadData ();
}
```

No mesmo arquivo, modifique a criação do novo delegado de exibição de tabela dentro do `ViewDidLoad` método da seguinte maneira:

```csharp
// Populate the Product Table
ProductTable.DataSource = DataSource;
ProductTable.Delegate = new ProductTableDelegate (this, DataSource);
```

Agora, edite `ProductTableDelegate.cs` o arquivo para incluir uma conexão privada para o controlador de exibição e para pegar o controlador como um parâmetro ao criar uma nova instância do delegado:

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

Em seguida, adicione o novo método privado a seguir à classe:

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

Isso usa todas as configurações de exibição de texto que estavam sendo feitas anteriormente no `GetViewForItem` método e as coloca em um único local que possa ser chamado (já que a última coluna da tabela não inclui uma exibição de texto, mas um botão).

Por fim, edite o `GetViewForItem` método e faça com que ele se pareça com o seguinte:

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

Vamos examinar várias seções desse código mais detalhadamente. Primeiro, se uma ação `NSTableViewCell` nova está sendo criada for executada com base no nome da coluna. Para as duas primeiras colunas (**produto** e **detalhes**), o novo `ConfigureTextField` método é chamado.

Para a coluna **Action** , um novo `NSButton` é criado e adicionado à célula como uma exibição sub:

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

A propriedade do `Tag` botão é usada para armazenar o número da linha que está sendo processada no momento. Esse número será usado posteriormente quando o usuário solicitar uma linha a ser excluída no evento do `Activated` botão:

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

No início do manipulador de eventos, obtemos o botão e o produto que está na linha da tabela especificada. Em seguida, um alerta é apresentado ao usuário confirmando a exclusão de linha. Se o usuário optar por excluir a linha, a linha determinada será removida da fonte de dados e a tabela será recarregada:

```csharp
// Remove the given row from the dataset
DataSource.Products.RemoveAt((int)btn.Tag);
Controller.ReloadTable ();
```

Por fim, se a célula de exibição de tabela estiver sendo reutilizada em vez de ser criada nova, o código a seguir a configurará com base na coluna que está sendo processada:

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

Para a coluna de **ação** , todas as subexibições são verificadas até que o seja `NSButton` encontrado `Tag` , então a propriedade é atualizada para apontar para a linha atual.

Com essas alterações em vigor, quando o aplicativo for executado, cada linha terá um botão de **exclusão** :

[![](table-view-images/delete02.png "O modo de exibição de tabela com botões de exclusão")](table-view-images/delete02.png#lightbox)

Quando o usuário clicar em um botão **excluir** , um alerta será exibido solicitando que ele exclua a linha determinada:

[![](table-view-images/delete03.png "Um alerta de excluir linha")](table-view-images/delete03.png#lightbox)

Se o usuário escolher excluir, a linha será removida e a tabela será redesenhada:

[![](table-view-images/delete04.png "A tabela após a linha ser excluída")](table-view-images/delete04.png#lightbox)

<a name="Data_Binding_Table_Views" />

## <a name="data-binding-table-views"></a>Exibições de tabela de associação de dados

Usando a codificação de valor chave e técnicas de vinculação de dados em seu aplicativo Xamarin. Mac, você pode diminuir muito a quantidade de código que você precisa escrever e manter para popular e trabalhar com elementos de interface do usuário. Você também tem o benefício de desacoplar ainda mais os dados de backup (_modelo de dados_) da sua interface do usuário de front-end (_Model-View-Controller_), levando a um design de aplicativo mais fácil de manter e mais flexível.

A KVC (codificação de valor chave) é um mecanismo para acessar as propriedades de um objeto indiretamente, usando chaves (cadeias de caracteres especialmente formatadas) para identificar Propriedades em vez de acessá-las por meio de variáveis de instância ou métodos de acessador (`get/set`). Ao implementar acessadores compatíveis com codificação de chave-valor em seu aplicativo Xamarin. Mac, você obterá acesso a outros recursos do macOS, como KVO (chave-valor, observação), vinculação de dados, dados principais, associações de Cocoa e scripts.

Para obter mais informações, consulte a seção de [vinculação de dados de exibição de tabela](~/mac/app-fundamentals/databinding.md#Table_View_Data_Binding) de nossa vinculação de [dados e a documentação de codificação de chave-valor](~/mac/app-fundamentals/databinding.md) .

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo deu uma visão detalhada de como trabalhar com exibições de tabela em um aplicativo Xamarin. Mac. Vimos os diferentes tipos e usos de exibições de tabela, como criar e manter exibições de tabela no Interface Builder do Xcode e como trabalhar com exibições C# de tabela no código.

## <a name="related-links"></a>Links relacionados

- [MacTables (exemplo)](https://docs.microsoft.com/samples/xamarin/mac-samples/mactables)
- [MacImages (amostra)](https://docs.microsoft.com/samples/xamarin/mac-samples/macimages)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Modos de exibição de estrutura de tópicos](~/mac/user-interface/outline-view.md)
- [Listas de origem](~/mac/user-interface/source-list.md)
- [Associação de dados e a codificação de chave-valor](~/mac/app-fundamentals/databinding.md)
- [Diretrizes de interface humana do OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [NSTableView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSTableView_Class/index.html#//apple_ref/doc/uid/TP40004125)
- [NSTableViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSTableViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008622)
- [NSTableViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSTableDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004178)
