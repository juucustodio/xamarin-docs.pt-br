---
title: "Trabalhando com modos de exibição de tabela"
description: "Este artigo aborda criando e trabalhando com modos de exibição de tabela e controladores de exibição de tabela dentro de um aplicativo Xamarin.tvOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: D8F80FA9-6400-4DB7-AFC9-A28A54AD04E8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: af562ac03f2cd5f293f99c7509000499ad5deaa4
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="working-with-table-views"></a>Trabalhando com modos de exibição de tabela

_Este artigo aborda criando e trabalhando com modos de exibição de tabela e controladores de exibição de tabela dentro de um aplicativo Xamarin.tvOS._

TvOS, uma exibição de tabela é apresentada como uma única coluna de linhas que podem opcionalmente ser organizadas em grupos de seções de rolagem. Exibições de tabela devem ser usadas quando você precisa exibir uma grande quantidade de dados com eficiência para o usuário, em uma clara para compreender a maneira.

Modos de exibição de tabela geralmente são exibidos em um dos lados de uma [modo divisão](~/ios/tvos/user-interface/split-views.md) como navegação com os detalhes do item selecionado exibido no lado oposto:

[![](table-views-images/intro01.png "Modo de exibição de tabela de exemplo")](table-views-images/intro01.png#lightbox)

<a name="About-Table-Views" />

## <a name="about-table-views"></a>Sobre modos de exibição de tabela

Um `UITableView` exibe uma única coluna de linhas roláveis como uma lista hierárquica de informações que podem opcionalmente ser organizadas em grupos de seções: 

[![](table-views-images/table01.png "Um item selecionado")](table-views-images/table01.png#lightbox)

Apple tem as seguintes sugestões para trabalhar com tabelas:

- **Estar ciente de largura** -tentar obter o equilíbrio correto em suas larguras de tabela. Se a tabela é muito grande, ele pode ser difícil de varredura de uma distância e poderá levar fora da área de conteúdo disponível. Se a tabela for muito estreita, pode fazer com que as informações sejam truncados ou wrap, novamente isso pode ser difícil para o usuário leiam na sala.
- **Mostrar tabela conteúdo rapidamente** - para listas grandes de dados, lento, carregar o conteúdo e Iniciar mostrando informações assim que a tabela é apresentada ao usuário. Se a tabela demora para carregar, o usuário poderá perder o interesse em seu aplicativo ou pense que ele está bloqueado para cima.
- **Informar o usuário de muito conteúdo cargas** - se um tempo de carregamento da tabela longa é inevitável, apresente um [barra de progresso ou indicador de atividade](~/ios/tvos/user-interface/progress-indicators.md) para que eles saibam o aplicativo ainda não bloqueados.

<a name="Table-Cell-Types" />

## <a name="table-view-cell-types"></a>Tipos de célula de exibição de tabela

A `UITableViewCell` é usada para representar as linhas individuais de dados no modo de exibição de tabela. Apple definiu vários tipos de célula de tabela padrão:

- **Padrão** - isso apresenta tipo imagem de uma opção no lado esquerdo da célula e título alinhado à esquerda, à direita. 
- **Subtítulo** - isso apresenta tipo um título alinhado à esquerda na primeira linha e um menor alinhado à esquerda subtítulo na próxima linha.
- **O valor 1** -este tipo apresenta um título alinhado à esquerda com um subtítulo colorido mais claras, alinhado à direita na mesma linha.
- **O valor 2** -este tipo apresenta um título alinhado à direita com um subtítulo colorido mais claras, alinhado à esquerda na mesma linha.

Todos os tipos de célula de exibição de tabela padrão também oferecem suporte a elementos gráficos, como indicadores de divulgação de informações ou marcas de seleção. 

Além disso, você pode definir um **personalizado** tipo de célula de exibição de tabela e apresentar um _protótipo célula_, que você crie no Designer de Interface ou por meio de código.

Apple tem as seguintes sugestões para trabalhar com células do modo de exibição de tabela:

- **Evite texto recorte** -manter as linhas individuais de texto curto para que eles não fim truncado. Truncado palavras ou frases serão difícil para o usuário analisar de dentro de uma sala.
- **Considere o estado de linha focalizada** – porque uma linha se torna maior, com mais arredondado cantos em foco, você precisa testar a aparência da célula em todos os estados. Texto ou imagens podem se tornar cortados ou examinar o estado de focalizada incorreto.
- **Usar tabelas editável moderadamente** -mover ou excluir linhas da tabela é mais demorado em tvOS de iOS. Você precisa decidir cuidadosamente se esse recurso adicionará ou distrair de seu aplicativo tvOS.
- **Criar personalizado célula tipos onde apropriado** - enquanto os tipos internos de célula de exibição de tabela são ótimos para muitas situações, considere a criação de tipos de célula personalizados para obter informações não padrão para fornecer maior controle e melhor apresentar as informações para o usuário.

<a name="Working-With-Table-Views" />

## <a name="working-with-table-views"></a>Trabalhando com modos de exibição de tabela

É a maneira mais fácil trabalhar com exibições de tabela em um aplicativo de Xamarin.tvOS criar e modificar sua aparência no Designer de Interface.

Para começar, faça o seguinte:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)
    
1. No Visual Studio para Mac, inicie um novo projeto de aplicativo tvOS e selecione **tvOS** > **aplicativo** > **único aplicativo de exibição** e clique no  **Próxima** botão: 

    [![](table-views-images/table02.png "Selecione o aplicativo de modo único")](table-views-images/table02.png#lightbox)
1. Insira um **nome** para o aplicativo e clique **próximo**: 

    [![](table-views-images/table03.png "Insira um nome para o aplicativo")](table-views-images/table03.png#lightbox)
1. O ajuste de **nome do projeto** e **nome da solução** ou aceite os padrões e clique no **criar** botão para criar a nova solução: 

    [![](table-views-images/table04.png "O nome do projeto e solução")](table-views-images/table04.png#lightbox)
1. No **solução preenchimento**, clique duas vezes o `Main.storyboard` arquivo para abri-lo no Designer de iOS: 

    [![](table-views-images/table05.png "O arquivo Main.storyboard")](table-views-images/table05.png#lightbox)
1. Selecione e exclua o **padrão View-Controller**: 

    [![](table-views-images/table06.png "Selecione e exclua o controlador de exibição padrão")](table-views-images/table06.png#lightbox)
1. Selecione um **divisão View Controller** do **caixa de ferramentas** e arraste-o para a superfície de Design.
1. Por padrão, você obterá um [modo divisão](~/ios/tvos/user-interface/split-views.md) com um **controlador de exibição de navegação** e um **tabela View Controller** no lado esquerdo e um **View Controller** no lado direito. Este é o uso sugerido da Apple de uma exibição de tabela em tvOS: 

    [![](table-views-images/table08.png "Adicionar um modo de exibição de divisão")](table-views-images/table08.png#lightbox)
1. Você precisará selecionar todas as partes da exibição de tabela e atribuir a ele um personalizado **nome da classe** no **Widget** guia do **propriedades Explorer** para que você pode acessá-lo mais tarde em c# código. Por exemplo, o **tabela View Controller**: 

    [![](table-views-images/table09.png "Atribua um nome de classe")](table-views-images/table09.png#lightbox)
1. Certifique-se de que você crie uma classe personalizada para o **controlador de exibição de tabela**, o **tabela exibição** e qualquer **protótipo células**. O Visual Studio para Mac adicionará as classes personalizadas para a árvore de projeto conforme eles são criados: 

    [![](table-views-images/table10.png "As classes personalizadas na árvore do projeto")](table-views-images/table10.png#lightbox)
1. Em seguida, selecione o modo de exibição de tabela na superfície de Design e ajuste suas propriedades, conforme necessário. Como o número de **protótipo células** e **estilo** (simples ou agrupado): 

    [![](table-views-images/table11.png "A guia de widget")](table-views-images/table11.png#lightbox)
1. Para cada **protótipo célula**, selecione-o e atribuir uma única **identificador** no **Widget** guia do **propriedades Explorer**. Esta etapa é _muito importante_ como esse identificador será necessário mais tarde ao popular a tabela. Por exemplo `AttrCell`: 

    [![](table-views-images/table12.png "A guia de Widget")](table-views-images/table12.png#lightbox)
1. Você também pode selecionar para apresentar a célula como uma da [tipos de célula de exibição de tabela padrão](#Table-View-Cell-Types) via o **estilo** suspensa ou defina-a como **personalizado** e use a superfície de Design para a célula de layout arrastando-os em outros widgets de interface do usuário do **caixa de ferramentas**: 

    [![](table-views-images/table13.png "O layout da célula")](table-views-images/table13.png#lightbox)
1. Atribuir uma única **nome** para cada elemento de interface do usuário no design da célula de protótipo no **Widget** guia do **propriedades Explorer** para que você pode acessá-los mais tarde no código do c#: 

    [![](table-views-images/table14.png "Atribua um nome")](table-views-images/table14.png#lightbox)
1. Repita a etapa acima para todas as células de protótipo na exibição de tabela.
1. Em seguida, atribuir classes personalizadas para o restante do seu design de interface do usuário, a exibição de detalhes de layout e atribuir exclusivo **nomes** para cada elemento de interface do usuário nos detalhes da exibição para que você possa acessá-los em c#, bem. Por exemplo: 

    [![](table-views-images/table15.png "O layout da interface do usuário")](table-views-images/table15.png#lightbox)
1. Salve suas alterações para o Storyboard.
    
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
1. No Visual Studio, iniciar um novo projeto de aplicativo tvOS e selecione **tvOS** > **único aplicativo de exibição** e digite um nome para seu aplicativo. Clique o **Okey** botão para criar uma nova solução: 

    [![](table-views-images/table02-vs.png "Selecione o aplicativo de modo único")](table-views-images/table02-vs.png#lightbox)
1. No **Solution Explorer**, clique duas vezes o `Main.storyboard` arquivo para abri-lo no Designer do iOS: 

    [![](table-views-images/table05-vs.png "O arquivo Main.storyboard")](table-views-images/table05-vs.png#lightbox)
1. Selecione e exclua o **padrão View-Controller**: 

    [![](table-views-images/table06-vs.png "Selecione e exclua o controlador de exibição padrão")](table-views-images/table06-vs.png#lightbox)
1. Selecione um **divisão View Controller** do **caixa de ferramentas** e arraste-o para a superfície de Design: 

    [![](table-views-images/table07-vs.png "Um controlador de exibição de divisão")](table-views-images/table07-vs.png#lightbox)
1. Por padrão, você obterá um [modo divisão](~/ios/tvos/user-interface/split-views.md) com um **controlador de exibição de navegação** e um **tabela View Controller** no lado esquerdo e um **View Controller** no lado direito. Este é o uso sugerido da Apple de uma exibição de tabela em tvOS: 

    [![](table-views-images/table08-vs.png "Layout da interface do usuário")](table-views-images/table08-vs.png#lightbox)
1. Você precisará selecionar todas as partes da exibição de tabela e atribuir a ele um personalizado **nome da classe** no **Widget** guia do **propriedades Explorer** para que você pode acessá-lo mais tarde em c# código. Por exemplo, o **tabela View Controller**: 

    [![](table-views-images/table09-vs.png "A guia de Widget")](table-views-images/table09-vs.png#lightbox)
1. Certifique-se de que você crie uma classe personalizada para o **controlador de exibição de tabela**, o **tabela exibição** e qualquer **protótipo células**. O Visual Studio para Mac adicionará as classes personalizadas para a árvore de projeto conforme eles são criados: 

    [![](table-views-images/table10-vs.png "As classes personalizadas na árvore do projeto")](table-views-images/table10-vs.png#lightbox)
1. Em seguida, selecione o modo de exibição de tabela na superfície de Design e ajuste suas propriedades, conforme necessário. Como o número de **protótipo células** e **estilo** (simples ou agrupado): 

    [![](table-views-images/table11-vs.png "A guia de Widget")](table-views-images/table11-vs.png#lightbox)
1. Para cada **protótipo célula**, selecione-o e atribuir uma única **identificador** no **Widget** guia do **propriedades Explorer**. Esta etapa é _muito importante_ como esse identificador será necessário mais tarde ao popular a tabela. Por exemplo `AttrCell`: 

    [![](table-views-images/table12-vs.png "Atribua um identificador")](table-views-images/table12-vs.png#lightbox)
1. Você também pode selecionar para apresentar a célula como uma da [tipos de célula de exibição de tabela padrão](#Table-View-Cell-Types) via o **estilo** suspensa ou defina-a como **personalizado** e use a superfície de Design para a célula de layout arrastando-os em outros widgets de interface do usuário do **caixa de ferramentas**: 

    [![](table-views-images/table13-vs.png "Menu suspenso de estilo")](table-views-images/table13-vs.png#lightbox)
1. Atribuir uma única **nome** para cada elemento de interface do usuário no design da célula de protótipo no **Widget** guia do **propriedades Explorer** para que você pode acessá-los mais tarde no código do c#: 

    [![](table-views-images/table14-vs.png "A guia de Widget")](table-views-images/table14-vs.png#lightbox)
1. Repita a etapa acima para todas as células de protótipo na exibição de tabela.
1. Em seguida, atribuir classes personalizadas para o restante do seu design de interface do usuário, a exibição de detalhes de layout e atribuir exclusivo **nomes** para cada elemento de interface do usuário nos detalhes da exibição para que você possa acessá-los em c#, bem. Por exemplo: 

    [![](table-views-images/table15.png "O Layout da interface do usuário")](table-views-images/table15.png#lightbox)
1. Salve suas alterações para o Storyboard.
    
-----

<a name="Designing-a-Data-Model" />

## <a name="designing-a-data-model"></a>Criando um modelo de dados

Para tornar o trabalho com as informações que o modo de exibição de tabela exibirá mais fácil e facilitar a apresentação de informações (como o usuário seleciona ou realça as linhas no modo de exibição de tabela), crie uma classe personalizada ou classes para atuar como o modelo de dados para obter as informações apresentadas .

Veja o exemplo de um aplicativo de reservas de viagem que contém uma lista de **cidades**, cada um que contém uma lista exclusiva de **atrações** que o usuário pode selecionar. O usuário poderá marcar um aumento de como um *Favoritos*, selecione obter *direções* para um aumento e *programar um voo* para uma determinada cidade.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Para criar o modelo de dados para um **atração**, com o botão direito no nome do projeto no **solução preenchimento** e selecione **adicionar** > **novo arquivo...** . Digite `AttractionInformation` para o **nome** e clique no **novo** botão: 

[![](table-views-images/data01.png "Digite AttractionInformation para o nome")](table-views-images/data01.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para criar o modelo de dados para um **atração**, com o botão direito no nome do projeto no **Solution Explorer** e selecione **adicionar** > **Novo Item ...** . Selecione **classe** e digite `AttractionInformation` para o **nome** e clique no **adicionar** botão: 

[![](table-views-images/data01-vs.png "Selecione a classe e digite AttractionInformation para o nome")](table-views-images/data01-vs.png#lightbox)

-----

Editar o `AttractionInformation.cs` de arquivo e torná-lo a aparência a seguir:

```csharp
using System;
using Foundation;

namespace tvTable
{
    public class AttractionInformation : NSObject
    {
        #region Computed Properties
        public CityInformation City { get; set;}
        public string Name { get; set;}
        public string Description { get; set;}
        public string ImageName { get; set;}
        public bool IsFavorite { get; set;}
        public bool AddDirections { get; set;}
        #endregion

        #region Constructors
        public AttractionInformation (string name, string description, string imageName)
        {
            // Initialize
            this.Name = name;
            this.Description = description;
            this.ImageName = imageName;
        }
        #endregion
    }
}
```

Essa classe fornece as propriedades para armazenar as informações sobre um determinado **atração**.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Em seguida, clique no nome do projeto no **solução preenchimento** novamente e selecione **adicionar** > **novo arquivo...** . Digite `CityInformation` para o **nome** e clique no **novo** botão: 

[![](table-views-images/data02.png "Digite CityInformation para o nome")](table-views-images/data02.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Em seguida, clique no nome do projeto no **Solution Explorer** novamente e selecione **adicionar** > **Novo Item...** . Digite `CityInformation` para o **nome** e clique no **adicionar** botão: 

[![](table-views-images/data02-vs.png "Digite CityInformation para o nome")](table-views-images/data02-vs.png#lightbox)

-----

Editar o `CityInformation.cs` de arquivo e torná-lo a aparência a seguir:

```csharp
using System;
using System.Collections.Generic;
using Foundation;

namespace tvTable
{
    public class CityInformation : NSObject
    {
        #region Computed Properties
        public string Name { get; set; }
        public List<AttractionInformation> Attractions { get; set;}
        public bool FlightBooked { get; set;}
        #endregion

        #region Constructors
        public CityInformation (string name)
        {
            // Initialize
            this.Name = name;
            this.Attractions = new List<AttractionInformation> ();
        }
        #endregion

        #region Public Methods
        public void AddAttraction (AttractionInformation attraction)
        {
            // Mark as belonging to this city
            attraction.City = this;

            // Add to collection
            Attractions.Add (attraction);
        }

        public void AddAttraction (string name, string description, string imageName)
        {
            // Create attraction
            var attraction = new AttractionInformation (name, description, imageName);

            // Mark as belonging to this city
            attraction.City = this;

            // Add to collection
            Attractions.Add (attraction);
        }
        #endregion
    }
}
```

Essa classe contém todas as informações sobre um destino **City**, uma coleção de **atrações** da cidade e fornece dois métodos auxiliares (`AddAttraction`) para tornar mais fácil adicionar atrações para o Cidade.

<a name="The-Table-Data-Source" />

## <a name="the-table-view-data-source"></a>A fonte de dados de exibição de tabela

Cada modo de exibição de tabela exige uma fonte de dados (`UITableViewDataSource`) fornecer os dados para a tabela e gerar as linhas necessárias como exigido pela exibição de tabela.

No exemplo acima, clique no nome do projeto no **Solution Explorer**, selecione **adicionar** > **novo arquivo...**  e chamá-lo `AttractionTableDatasource` e clique no **novo** botão a ser criado. Em seguida, edite o `AttractionTableDatasource.cs` de arquivo e torná-lo a aparência a seguir:

```csharp
using System;
using System.Collections.Generic;
using UIKit;

namespace tvTable
{
    public class AttractionTableDatasource : UITableViewDataSource
    {
        #region Constants
        const string CellID = "AttrCell";
        #endregion

        #region Computed Properties
        public AttractionTableViewController Controller { get; set;}
        public List<CityInformation> Cities { get; set;}
        #endregion

        #region Constructors
        public AttractionTableDatasource (AttractionTableViewController controller)
        {
            // Initialize
            this.Controller = controller;
            this.Cities = new List<CityInformation> ();
            PopulateCities ();
        }
        #endregion

        #region Public Methods
        public void PopulateCities ()
        {
            // Clear existing
            Cities.Clear ();

            // Define cities and attractions
            var Paris = new CityInformation ("Paris");
            Paris.AddAttraction ("Eiffel Tower", "Is a wrought iron lattice tower on the Champ de Mars in Paris, France.", "EiffelTower");
            Paris.AddAttraction ("Musée du Louvre", "is one of the world's largest museums and a historic monument in Paris, France.", "Louvre");
            Paris.AddAttraction ("Moulin Rouge", "French for 'Red Mill', is a cabaret in Paris, France.", "MoulinRouge");
            Paris.AddAttraction ("La Seine", "Is a 777-kilometre long river and an important commercial waterway within the Paris Basin.", "RiverSeine");
            Cities.Add (Paris);

            var SanFran = new CityInformation ("San Francisco");
            SanFran.AddAttraction ("Alcatraz Island", "Is located in the San Francisco Bay, 1.25 miles (2.01 km) offshore from San Francisco.", "Alcatraz");
            SanFran.AddAttraction ("Golden Gate Bridge", "Is a suspension bridge spanning the Golden Gate strait between San Francisco Bay and the Pacific Ocean", "GoldenGateBridge");
            SanFran.AddAttraction ("San Francisco", "Is the cultural, commercial, and financial center of Northern California.", "SanFrancisco");
            SanFran.AddAttraction ("Telegraph Hill", "Is primarily a residential area, much quieter than adjoining North Beach.", "TelegraphHill");
            Cities.Add (SanFran);

            var Houston = new CityInformation ("Houston");
            Houston.AddAttraction ("City Hall", "It was constructed in 1938-1939, and is located in Downtown Houston.", "CityHall");
            Houston.AddAttraction ("Houston", "Is the most populous city in Texas and the fourth most populous city in the US.", "Houston");
            Houston.AddAttraction ("Texas Longhorn", "Is a breed of cattle known for its characteristic horns, which can extend to over 6 ft.", "LonghornCattle");
            Houston.AddAttraction ("Saturn V Rocket", "was an American human-rated expendable rocket used by NASA between 1966 and 1973.", "Rocket");
            Cities.Add (Houston);
        }
        #endregion

        #region Override Methods
        public override UITableViewCell GetCell (UITableView tableView, Foundation.NSIndexPath indexPath)
        {
            // Get cell
            var cell = tableView.DequeueReusableCell (CellID) as AttractionTableCell;

            // Populate cell
            cell.Attraction = Cities [indexPath.Section].Attractions [indexPath.Row];

            // Return new cell
            return cell;
        }

        public override nint NumberOfSections (UITableView tableView)
        {
            // Return number of cities
            return Cities.Count;
        }

        public override nint RowsInSection (UITableView tableView, nint section)
        {
            // Return the number of attractions in the given city
            return Cities [(int)section].Attractions.Count;
        }

        public override string TitleForHeader (UITableView tableView, nint section)
        {
            // Get the name of the current city
            return Cities [(int)section].Name;
        }
        #endregion
    }
}
```

Vamos dar uma olhada em algumas seções da classe em detalhes.

Primeiro, adicionamos uma constante para manter o identificador exclusivo da célula protótipo (esse é o mesmo identificador atribuído no Designer de Interface acima), um atalho para o controlador de exibição de tabela e armazenamento criado para nossos dados:

```csharp
const string CellID = "AttrCell";
public AttractionTableViewController Controller { get; set;}
public List<CityInformation> Cities { get; set;}
```

Em seguida, é salvar o controlador de exibição de tabela, e em seguida, criar e popular nossa fonte de dados (usando os modelos de dados definida acima) quando a classe é criada:

```csharp
public AttractionTableDatasource (AttractionTableViewController controller)
{
    // Initialize
    this.Controller = controller;
    this.Cities = new List<CityInformation> ();
    PopulateCities ();
}
```

Por exemplo, o `PopulateCities` método simplesmente cria objetos de modelo de dados na memória, mas eles podem ser lido facilmente de um serviço web ou de banco de dados em um aplicativo real:

```csharp
public void PopulateCities ()
{
    // Clear existing
    Cities.Clear ();

    // Define cities and attractions
    var Paris = new CityInformation ("Paris");
    Paris.AddAttraction ("Eiffel Tower", "Is a wrought iron lattice tower on the Champ de Mars in Paris, France.", "EiffelTower");
    ...
}
```

O `NumberOfSections` método retorna o número de seções na tabela:

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    // Return number of cities
    return Cities.Count;
}
```

Para **simples** com o estilo de modos de exibição de tabela, sempre retornará 1.

O `RowsInSection` método retorna o número de linhas na seção atual:

```csharp
public override nint RowsInSection (UITableView tableView, nint section)
{
    // Return the number of attractions in the given city
    return Cities [(int)section].Attractions.Count;
}
```

Novamente, para **simples** modos de exibição de tabela, retorna o número total de itens na fonte de dados.

O `TitleForHeader` método retorna o título para uma determinada seção:

```csharp
public override string TitleForHeader (UITableView tableView, nint section)
{
    // Get the name of the current city
    return Cities [(int)section].Name;
}
```

Para uma **simples** exibição da tabela de tipo, deixe o título em branco (`""`).

Finalmente, quando solicitado pelo modo de exibição de tabela, criar e preencher uma célula de protótipo usando o `GetCell` método: 

```csharp
public override UITableViewCell GetCell (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    // Get cell
    var cell = tableView.DequeueReusableCell (CellID) as AttractionTableCell;

    // Populate cell
    cell.Attraction = Cities [indexPath.Section].Attractions [indexPath.Row];

    // Return new cell
    return cell;
}
```

Para obter mais informações sobre como trabalhar com um `UITableViewDatasource`, consulte da Apple [UITableViewDatasource](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40006941) documentação.

<a name="The-Table-View-Delegate" />

## <a name="the-table-view-delegate"></a>O delegado de modo de exibição de tabela

Cada modo de exibição de tabela requer um delegado (`UITableViewDelegate`) para responder à interação do usuário ou outros eventos do sistema na tabela.

No exemplo acima, clique no nome do projeto no **Solution Explorer**, selecione **adicionar** > **novo arquivo...**  e chamá-lo `AttractionTableDelegate` e clique no **novo** botão a ser criado. Em seguida, edite o `AttractionTableDelegate.cs` de arquivo e torná-lo a aparência a seguir:

```csharp
using System;
using System.Collections.Generic;
using UIKit;

namespace tvTable
{
    public class AttractionTableDelegate : UITableViewDelegate
    {
        #region Computed Properties
        public AttractionTableViewController Controller { get; set;}
        #endregion

        #region Constructors
        public AttractionTableDelegate (AttractionTableViewController controller)
        {
            // Initializw
            this.Controller = controller;
        }
        #endregion

        #region Override Methods
        public override void RowSelected (UITableView tableView, Foundation.NSIndexPath indexPath)
        {
            var attraction = Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row];
            attraction.IsFavorite = (!attraction.IsFavorite);

            // Update UI
            Controller.TableView.ReloadData ();
        }

        public override bool CanFocusRow (UITableView tableView, Foundation.NSIndexPath indexPath)
        {
            // Inform caller of highlight change
            RaiseAttractionHighlighted (Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row]);
            return true;
        }
        #endregion

        #region Events
        public delegate void AttractionHighlightedDelegate (AttractionInformation attraction);
        public event AttractionHighlightedDelegate AttractionHighlighted;

        internal void RaiseAttractionHighlighted (AttractionInformation attraction)
        {
            // Inform caller
            if (this.AttractionHighlighted != null) this.AttractionHighlighted (attraction);
        }
        #endregion
    }
}
```

Vamos dar uma olhada em várias seções dessa classe em detalhes.

Primeiro, vamos criar um atalho para o controlador de exibição de tabela quando a classe é criada:

```csharp
public AttractionTableViewController Controller { get; set;}
...

public AttractionTableDelegate (AttractionTableViewController controller)
{
    // Initialize
    this.Controller = controller;
}
```

Em seguida, quando uma linha é selecionada (o usuário clica na superfície de toque de remoto a Apple) deseja marcar o **atração** representado pela linha selecionada como um favorito:

```csharp
public override void RowSelected (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    var attraction = Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row];
    attraction.IsFavorite = (!attraction.IsFavorite);

    // Update UI
    Controller.TableView.ReloadData ();
}
```

Em seguida, quando o usuário realça uma linha (, dando a ele foco usando a superfície de toque remoto Apple) que desejamos apresentar os detalhes do **atração** representados por aquela linha na seção detalhes do nosso controlador de exibição de divisão:

```csharp
public override bool CanFocusRow (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    // Inform caller of highlight change
    RaiseAttractionHighlighted (Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row]);
    return true;
}
...

public delegate void AttractionHighlightedDelegate (AttractionInformation attraction);
public event AttractionHighlightedDelegate AttractionHighlighted;

internal void RaiseAttractionHighlighted (AttractionInformation attraction)
{
    // Inform caller
    if (this.AttractionHighlighted != null) this.AttractionHighlighted (attraction);
}
```

O `CanFocusRow` método é chamado para cada linha que está prestes a receber o foco no modo de exibição de tabela. Retornar `true` se a linha pode receber foco, ou retornar `false`. No caso do exemplo, criamos um personalizado `AttractionHighlighted` evento será gerado em cada linha, ele recebe o foco.

Para obter mais informações sobre como trabalhar com um `UITableViewDelegate`, consulte da Apple [UITableViewDelegate](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40006942) documentação.

<a name="The-Table-View-Cell" />

## <a name="the-table-view-cell"></a>A célula de exibição de tabela

Para cada célula de protótipo que você adicionou à exibição de tabela no Designer de Interface, você também criou uma instância personalizada da célula de exibição de tabela (`UITableViewCell`) para permitir que você preencher a nova célula (linha) que ele é criado.

Para o aplicativo de exemplo, clique duas vezes o `AttractionTableCell.cs` arquivo para abri-lo para edição e torná-lo a aparência a seguir:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class AttractionTableCell : UITableViewCell
    {
        #region Private Variables
        private AttractionInformation _attraction = null;
        #endregion

        #region Computed Properties
        public AttractionInformation Attraction {
            get { return _attraction; }
            set {
                _attraction = value;
                UpdateUI ();
            }
        }
        #endregion

        #region Constructors
        public AttractionTableCell (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Private Methods
        private void UpdateUI ()
        {
            // Trap all errors
            try {
                Title.Text = Attraction.Name;
                Favorite.Hidden = (!Attraction.IsFavorite);
            } catch {
                // Since the UI might not be fully loaded, ignore
                // all errors at this point
            }
        }
        #endregion
    }
}
```

Essa classe fornece armazenamento para o objeto de modelo de dados de atração (`AttractionInformation` conforme definido acima) exibido na linha determinada:

```csharp
private AttractionInformation _attraction = null;
...

public AttractionInformation Attraction {
    get { return _attraction; }
    set {
        _attraction = value;
        UpdateUI ();
    }
}
```

O `UpdateUI` método popula os Widgets de interface do usuário (que foram adicionados ao protótipo da célula no Designer de Interface) conforme necessário:

```csharp
private void UpdateUI ()
{
    // Trap all errors
    try {
        Title.Text = Attraction.Name;
        Favorite.Hidden = (!Attraction.IsFavorite);
    } catch {
        // Since the UI might not be fully loaded, ignore
        // all errors at this point
    }
}
```

Para obter mais informações sobre como trabalhar com um `UITableViewCell`, consulte da Apple [UITableViewCell](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewCell_Class/index.html#//apple_ref/doc/uid/TP40006938) documentação.

<a name="The-Table-View-Controller" />

## <a name="the-table-view-controller"></a>O controlador de exibição de tabela

Um controlador de exibição de tabela (`UITableViewController`) gerencia um modo de exibição de tabela que tenha sido adicionado a um Storyboard via a Interface de Designer.

Para o aplicativo de exemplo, clique duas vezes o `AttractionTableViewController.cs` arquivo para abri-lo para edição e torná-lo a aparência a seguir:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class AttractionTableViewController : UITableViewController
    {
        #region Computed Properties
        public AttractionTableDatasource Datasource {
            get { return TableView.DataSource as AttractionTableDatasource; }
        }

        public AttractionTableDelegate TableDelegate {
            get { return TableView.Delegate as AttractionTableDelegate; }
        }
        #endregion

        #region Constructors
        public AttractionTableViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Setup table
            TableView.DataSource = new AttractionTableDatasource (this);
            TableView.Delegate = new AttractionTableDelegate (this);
            TableView.ReloadData ();
        }
        #endregion
    }
}
```

Vamos examinar mais detalhadamente essa classe. Primeiro, criar atalhos para tornar mais fácil acessar o modo de exibição de tabela `DataSource` e `TableDelegate`. Usaremos as mais tarde para comunicação entre o modo de exibição de tabela no lado esquerdo do modo divisão e a exibição de detalhes no canto direito.

Finalmente, quando o modo de exibição de tabela é carregado na memória, podemos criar instâncias da `AttractionTableDatasource` e `AttractionTableDelegate` (ambos criado acima) e anexá-los para o modo de exibição de tabela.

Para obter mais informações sobre como trabalhar com um `UITableViewController`, consulte da Apple [UITableViewController](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewController_Class/index.html#//apple_ref/doc/uid/TP40007523) documentação.

<a name="Pulling-it-All-Together" />

## <a name="pulling-it-all-together"></a>Unindo-todos

Conforme mencionado no início deste documento, modos de exibição de tabela geralmente são exibidos em um dos lados de uma [modo divisão](~/ios/tvos/user-interface/split-views.md) como navegação com os detalhes do item selecionado exibido no lado oposto. Por exemplo: 

[![](table-views-images/intro01.png "Aplicativo de exemplo executar")](table-views-images/intro01.png#lightbox)

Como esse é um padrão em tvOS, vamos analisar as etapas finais para reunir tudo e ter os lados esquerdos e direito do modo divisão interagem entre si.

<a name="The-Detail-View" />

### <a name="the-detail-view"></a>O modo de exibição de detalhes

No exemplo do aplicativo viagem apresentados a seguir, uma classe personalizada (`AttractionViewController`) é definido para o controlador de exibição padrão apresentado no lado direito da exibição de divisão, como a exibição de detalhes:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class AttractionViewController : UIViewController
    {
        #region Private Variables
        private AttractionInformation _attraction = null;
        #endregion

        #region Computed Properties
        public AttractionInformation Attraction {
            get { return _attraction; }
            set {
                _attraction = value;
                UpdateUI ();
            }
        }

        public MasterSplitView SplitView { get; set;}
        #endregion

        #region Constructors
        public AttractionViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Public Methods
        public void UpdateUI ()
        {
            // Trap all errors
            try {
                City.Text = Attraction.City.Name;
                Title.Text = Attraction.Name;
                SubTitle.Text = Attraction.Description;

                IsFlighBooked.Hidden = (!Attraction.City.FlightBooked);
                IsFavorite.Hidden = (!Attraction.IsFavorite);
                IsDirections.Hidden = (!Attraction.AddDirections);
                BackgroundImage.Image = UIImage.FromBundle (Attraction.ImageName);
                AttractionImage.Image = BackgroundImage.Image;
            } catch {
                // Since the UI might not be fully loaded, ignore
                // all errors at this point
            }
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Ensure the UI Updates
            UpdateUI ();
        }
        #endregion

        #region Actions
        partial void BookFlight (NSObject sender)
        {
            // Ask user to book flight
            AlertViewController.PresentOKCancelAlert ("Book Flight",
                                                      string.Format ("Would you like to book a flight to {0}?", Attraction.City.Name),
                                                      this,
                                                      (ok) => {
                Attraction.City.FlightBooked = ok;
                IsFlighBooked.Hidden = (!Attraction.City.FlightBooked);
            });
        }

        partial void GetDirections (NSObject sender)
        {
            // Ask user to add directions
            AlertViewController.PresentOKCancelAlert ("Add Directions",
                                                     string.Format ("Would you like to add directions to {0} to you itinerary?", Attraction.Name),
                                                     this,
                                                     (ok) => {
                                                         Attraction.AddDirections = ok;
                                                         IsDirections.Hidden = (!Attraction.AddDirections);
                                                     });
        }

        partial void MarkFavorite (NSObject sender)
        {
            // Flip favorite state
            Attraction.IsFavorite = (!Attraction.IsFavorite);
            IsFavorite.Hidden = (!Attraction.IsFavorite);

            // Reload table
            SplitView.Master.TableController.TableView.ReloadData ();
        }
        #endregion
    }
}
```

Aqui, fornecemos o **atração** (`AttractionInformation`) que estão sendo exibidos como uma propriedade e criado um `UpdateUI` método que preenche os Widgets de interface do usuário é adicionado ao modo de exibição no Designer de Interface.

Também definimos um atalho para o controlador de exibição de divisão (`SplitView`) que usaremos para comunicar-se altera de volta para o modo de exibição de tabela (`AcctractionTableView`).

Por fim, ações personalizadas (eventos) foram adicionadas para os três `UIButton` instâncias criadas no Designer de Interface, que permitem que o usuário marcar um aumento de como um _Favoritos_, obter _direções_ para um aumento e _programar um voo_ para uma determinada cidade.

<a name="The-Navigation-View-Controller" />

### <a name="the-navigation-view-controller"></a>O controlador de exibição de navegação

Porque o controlador de exibição de tabela é aninhado em um controlador de exibição de navegação no lado esquerdo do modo divisão, o controlador de exibição de navegação foi atribuído a uma classe personalizada (`MasterNavigationController`) no Designer de Interface e definido da seguinte maneira:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class MasterNavigationController : UINavigationController
    {
        #region Computed Properties
        public MasterSplitView SplitView { get; set;}
        public AttractionTableViewController TableController {
            get { return TopViewController as AttractionTableViewController; }
        }
        #endregion

        #region Constructors
        public MasterNavigationController (IntPtr handle) : base (handle)
        {
        }
        #endregion
    }
}
```

Novamente, esta classe define apenas alguns atalhos para facilitar a comunicação entre os dois lados do controlador de exibição de divisão:

* `SplitView` -É um link para o controlador de exibição de divisão (`MainSpiltViewController`) que o controlador de exibição de navegação pertence.
* `TableController` -Obtém o controlador de exibição de tabela (`AttractionTableViewController`) que é apresentado como o modo de exibição no controlador de exibição de navegação superior.

<a name="The-Split-View-Controller" />

### <a name="the-split-view-controller"></a>O controlador de exibição de divisão

Como o controlador de exibição de divisão é a base do nosso aplicativo, criamos uma classe personalizada (`MasterSplitViewController`) para ele no Designer de Interface e definiu da seguinte maneira:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class MasterSplitView : UISplitViewController
    {
        #region Computed Properties
        public AttractionViewController Details {
            get { return ViewControllers [1] as AttractionViewController; }
        }

        public MasterNavigationController Master {
            get { return ViewControllers [0] as MasterNavigationController; }
        }
        #endregion

        #region Constructors
        public MasterSplitView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            Master.SplitView = this;
            Details.SplitView = this;

            // Wire-up events
            Master.TableController.TableDelegate.AttractionHighlighted += (attraction) => {
                // Display new attraction
                Details.Attraction = attraction;
            };
        }
        #endregion
    }
}
```

Primeiro, podemos criar atalhos para o **detalhes** lado do modo divisão (`AttractionViewController`) e o **mestre** lado (`MasterNavigationController`). Novamente, isso facilita a comunicação entre os dois lados mais tarde.

Em seguida, quando o modo divisão é carregado na memória, podemos anexar o controlador de exibição de divisão para ambos os lados do modo divisão e responder ao usuário realce um aumento na exibição de tabela (`AttractionHighlighted`), exibindo o aumento de novo no **detalhes**  lado do modo divisão.

Consulte o [tvTables](https://developer.xamarin.com/samples/monotouch/tvos/tvTable/) aplicativo de exemplo para uma implantação completa de modos de exibição de tabela dentro de um modo de exibição de divisão.

## <a name="table-views-in-detail"></a>Modos de exibição de tabela com detalhes

Desde tvOS baseia-se do iOS, modos de exibição de tabela e controladores de exibição de tabela são criados e se comportam de maneira semelhante. Para obter mais informações sobre como trabalhar com o modo de exibição de tabela em um aplicativo Xamarin, consulte nosso iOS [trabalhando com tabelas e células](~/ios/user-interface/controls/tables/index.md) documentação.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou criando e trabalhando com modos de exibição de tabela dentro de um aplicativo Xamarin.tvOS. E tenha apresentado um exemplo de como trabalhar com uma exibição de tabela dentro de uma exibição de divisão, que é o uso típico de uma exibição de tabela em um aplicativo tvOS.



## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [UITableViewController](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewController_Class/index.html#//apple_ref/doc/uid/TP40007523)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guias de Interface Humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
