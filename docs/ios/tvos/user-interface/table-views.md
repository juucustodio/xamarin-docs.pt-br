---
title: Trabalhando com exibições de tabela tvOS no Xamarin
description: Este artigo aborda a criação e o trabalho com exibições de tabela e controladores de exibição de tabela dentro de um aplicativo Xamarin. tvOS.
ms.prod: xamarin
ms.assetid: D8F80FA9-6400-4DB7-AFC9-A28A54AD04E8
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: 10f065d6e7b1cacb217cb510c57707662a196664
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768563"
---
# <a name="working-with-tvos-table-views-in-xamarin"></a>Trabalhando com exibições de tabela tvOS no Xamarin

_Este artigo aborda a criação e o trabalho com exibições de tabela e controladores de exibição de tabela dentro de um aplicativo Xamarin. tvOS._

No tvOS, uma exibição de tabela é apresentada como uma única coluna de linhas de rolagem que pode, opcionalmente, ser organizada em grupos ou seções. Os modos de exibição de tabela devem ser usados quando você precisa exibir uma grande quantidade de dados com eficiência para o usuário, de forma clara para entender.

Exibições de tabela normalmente são exibidas em um lado de um [modo de exibição de divisão](~/ios/tvos/user-interface/split-views.md) como navegação, com os detalhes do item selecionado exibido no lado oposto:

[![](table-views-images/intro01.png "Exibição de tabela de exemplo")](table-views-images/intro01.png#lightbox)

<a name="About-Table-Views" />

## <a name="about-table-views"></a>Sobre exibições de tabela

Um `UITableView` exibe uma única coluna de linhas roláveis como uma lista hierárquica de informações que podem, opcionalmente, ser organizadas em grupos ou seções: 

[![](table-views-images/table01.png "Um item selecionado")](table-views-images/table01.png#lightbox)

A Apple tem as seguintes sugestões para trabalhar com tabelas:

- **Lembre-se de largura** – tente ajustar o saldo correto em suas larguras de tabela. Se a tabela for muito grande, pode ser difícil verificar a partir de uma distância e pode retirar da área de conteúdo disponível. Se a tabela for muito estreita, ela poderá fazer com que as informações sejam truncadas ou encapsuladas, novamente isso pode ser difícil para o usuário de ler na sala.
- **Mostre o conteúdo da tabela rapidamente** -para obter grandes listas de dados, carregue o conteúdo lentamente e comece a mostrar as informações assim que a tabela for apresentada ao usuário. Se a tabela levar muito tempo para carregar, o usuário poderá perder seu interesse em seu aplicativo ou acreditar que está bloqueado.
- **Informar o usuário sobre cargas de conteúdo longos** – se um tempo de carregamento de tabela longo for inevitável, apresente uma [barra de progresso ou um indicador de atividade](~/ios/tvos/user-interface/progress-indicators.md) para que eles saibam que o aplicativo não foi bloqueado.

<a name="Table-Cell-Types" />

## <a name="table-view-cell-types"></a>Tipos de célula de exibição de tabela

Um `UITableViewCell` é usado para representar as linhas individuais de dados na exibição de tabela. A Apple definiu vários tipos de célula de tabela padrão:

- **Padrão** -esse tipo apresenta uma imagem de opção no lado esquerdo da célula e no título alinhado à esquerda à direita. 
- **Subtítulo** -esse tipo apresenta um título alinhado à esquerda na primeira linha e um subtítulo menor alinhado à esquerda na próxima linha.
- **Valor 1** -este tipo apresenta um título alinhado à esquerda com um subtítulo com cor mais clara, alinhado à direita na mesma linha.
- **Valor 2** -esse tipo apresenta um título alinhado à direita com um subtítulo com cor mais clara, alinhado à esquerda na mesma linha.

Todos os tipos de célula de exibição de tabela padrão também dão suporte a elementos gráficos, como indicadores de divulgação ou marcas de seleção. 

Além disso, você pode definir um tipo de célula de exibição de tabela **personalizada** e apresentar uma _célula de protótipo_, que você cria no designer de interface ou por meio de código.

A Apple tem as seguintes sugestões para trabalhar com células de exibição de tabela:

- **Evitar recorte de texto** – Mantenha as linhas individuais de texto de forma abreviada para que elas não acabem truncadas. Palavras ou frases truncadas são difíceis para o usuário analisar de toda a sala.
- **Considere o estado de linha focalizado** – como uma linha se torna maior, com mais cantos arredondados quando em foco, você precisa testar a aparência da célula em todos os Estados. Imagens ou texto podem ser recortados ou parecer incorretos no estado focalizado.
- **Usar tabelas editáveis com moderação** , mover ou excluir linhas de tabela é mais demorado em tvOS do que Ios. Você precisará decidir com cuidado se esse recurso será adicionado ou distraido do seu aplicativo tvOS.
- **Crie tipos de célula personalizados quando apropriado** – embora os tipos de célula de exibição de tabela internos sejam ótimos para muitas situações, considere a criação de tipos de células personalizadas para informações não padrão para fornecer maior controle e apresentar melhor as informações para o usuário.

<a name="Working-With-Table-Views" />

## <a name="working-with-table-views"></a>Trabalhando com exibições de tabela

A maneira mais fácil de trabalhar com exibições de tabela em um aplicativo Xamarin. tvOS é criar e modificar sua aparência no designer de interface.

Para começar, faça o seguinte:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Em Visual Studio para Mac, inicie um novo projeto de aplicativo do tvOS e selecione**aplicativo de exibição única** do**aplicativo** >  **tvOS** > e clique no botão **Avançar** : 

    [![](table-views-images/table02.png "Selecionar aplicativo de modo de exibição único")](table-views-images/table02.png#lightbox)
1. Insira um **nome** para o aplicativo e clique em **Avançar**: 

    [![](table-views-images/table03.png "Insira um nome para o aplicativo")](table-views-images/table03.png#lightbox)
1. Ajuste o **nome do projeto** e o **nome da solução** ou aceite os padrões e clique no botão **criar** para criar a nova solução: 

    [![](table-views-images/table04.png "O nome do projeto e o nome da solução")](table-views-images/table04.png#lightbox)
1. No **painel de soluções**, clique duas vezes no `Main.storyboard` arquivo para abri-lo no designer do IOS: 

    [![](table-views-images/table05.png "O arquivo Main. Storyboard")](table-views-images/table05.png#lightbox)
1. Selecione e exclua o **controlador de exibição padrão**: 

    [![](table-views-images/table06.png "Selecionar e excluir o controlador de exibição padrão")](table-views-images/table06.png#lightbox)
1. Selecione um **controlador de exibição de divisão** na **caixa de ferramentas** e arraste-o para a design Surface.
1. Por padrão, você obterá um [modo de exibição de divisão](~/ios/tvos/user-interface/split-views.md) com um **controlador de exibição de navegação** e um controlador de exibição de **tabela** no lado esquerdo e um **controlador de exibição** no lado direito. Este é o uso sugerido pela Apple de uma exibição de tabela no tvOS: 

    [![](table-views-images/table08.png "Adicionar uma exibição de divisão")](table-views-images/table08.png#lightbox)
1. Você precisará selecionar todas as partes do modo de exibição de tabela e atribuir a ela um **nome de classe** personalizado na guia **widget** do **Gerenciador de propriedades** para que você possa acessá-la posteriormente no C# código. Por exemplo, o **controlador de exibição de tabela**: 

    [![](table-views-images/table09.png "Atribuir um nome de classe")](table-views-images/table09.png#lightbox)
1. Certifique-se de criar uma classe personalizada para o **controlador de exibição de tabela**, a exibição de **tabela** e quaisquer células de **protótipo**. Visual Studio para Mac adicionará as classes personalizadas à árvore do projeto conforme elas forem criadas: 

    [![](table-views-images/table10.png "As classes personalizadas na árvore do projeto")](table-views-images/table10.png#lightbox)
1. Em seguida, selecione o modo de exibição de tabela no Design Surface e ajuste as propriedades conforme necessário. Como o número de **células de protótipo** e o **estilo** (simples ou agrupado): 

    [![](table-views-images/table11.png "A guia do widget")](table-views-images/table11.png#lightbox)
1. Para cada **célula de protótipo**, selecione-a e atribua **um identificador** exclusivo na guia **widget** do **Gerenciador de propriedades**. Essa etapa é _muito importante_ , pois você precisará desse identificador posteriormente quando preencher a tabela. Por exemplo `AttrCell`: 

    [![](table-views-images/table12.png "A guia do widget")](table-views-images/table12.png#lightbox)
1. Você também pode selecionar para apresentar a célula como um dos [tipos de célula de exibição de tabela padrão](#table-view-cell-types) por meio da lista suspensa **estilo** ou defini-la como **personalizada** e usar o design Surface para definir o layout da célula arrastando em outros widgets da interface do usuário a partir da **caixa de ferramentas**: 

    [![](table-views-images/table13.png "O layout da célula")](table-views-images/table13.png#lightbox)
1. Atribua um **nome** exclusivo a cada elemento da interface do usuário no design de célula do protótipo na guia **widget** do **Gerenciador de propriedades** para que você possa C# acessá-los mais tarde no código: 

    [![](table-views-images/table14.png "Atribuir um nome")](table-views-images/table14.png#lightbox)
1. Repita a etapa acima para todas as células de protótipo na exibição de tabela.
1. Em seguida, atribua classes personalizadas ao restante do design da interface do usuário, layout da exibição de detalhes e atribua **nomes** exclusivos a cada elemento da interface do usuário na exibição de detalhes para C# que você possa acessá-los também. Por exemplo: 

    [![](table-views-images/table15.png "O layout da interface do usuário")](table-views-images/table15.png#lightbox)
1. Salve as alterações no storyboard.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. No Visual Studio, inicie um novo projeto de aplicativo do tvOS e selecione**aplicativo de exibição única** do **tvOS** > e insira um nome para seu aplicativo. Clique no botão **OK** para criar uma nova solução: 

    [![](table-views-images/table02-vs.png "Selecionar aplicativo de modo de exibição único")](table-views-images/table02-vs.png#lightbox)
1. No **Gerenciador de soluções**, clique duas vezes no `Main.storyboard` arquivo para abri-lo no designer do IOS: 

    [![](table-views-images/table05-vs.png "O arquivo Main. Storyboard")](table-views-images/table05-vs.png#lightbox)
1. Selecione e exclua o **controlador de exibição padrão**: 

    [![](table-views-images/table06-vs.png "Selecionar e excluir o controlador de exibição padrão")](table-views-images/table06-vs.png#lightbox)
1. Selecione um **controlador de exibição de divisão** na **caixa de ferramentas** e arraste-o para a design Surface: 

    [![](table-views-images/table07-vs.png "Um controlador de exibição de divisão")](table-views-images/table07-vs.png#lightbox)
1. Por padrão, você obterá um [modo de exibição de divisão](~/ios/tvos/user-interface/split-views.md) com um **controlador de exibição de navegação** e um controlador de exibição de **tabela** no lado esquerdo e um **controlador de exibição** no lado direito. Este é o uso sugerido pela Apple de uma exibição de tabela no tvOS: 

    [![](table-views-images/table08-vs.png "Layout da interface do usuário")](table-views-images/table08-vs.png#lightbox)
1. Você precisará selecionar todas as partes do modo de exibição de tabela e atribuir a ela um **nome de classe** personalizado na guia **widget** do **Gerenciador de propriedades** para que você possa acessá-la posteriormente no C# código. Por exemplo, o **controlador de exibição de tabela**: 

    [![](table-views-images/table09-vs.png "A guia do widget")](table-views-images/table09-vs.png#lightbox)
1. Certifique-se de criar uma classe personalizada para o **controlador de exibição de tabela**, a exibição de **tabela** e quaisquer células de **protótipo**. Visual Studio para Mac adicionará as classes personalizadas à árvore do projeto conforme elas forem criadas: 

    [![](table-views-images/table10-vs.png "As classes personalizadas na árvore do projeto")](table-views-images/table10-vs.png#lightbox)
1. Em seguida, selecione o modo de exibição de tabela no Design Surface e ajuste as propriedades conforme necessário. Como o número de **células de protótipo** e o **estilo** (simples ou agrupado): 

    [![](table-views-images/table11-vs.png "A guia do widget")](table-views-images/table11-vs.png#lightbox)
1. Para cada **célula de protótipo**, selecione-a e atribua **um identificador** exclusivo na guia **widget** do **Gerenciador de propriedades**. Essa etapa é _muito importante_ , pois você precisará desse identificador posteriormente quando preencher a tabela. Por exemplo `AttrCell`: 

    [![](table-views-images/table12-vs.png "Atribuir um identificador")](table-views-images/table12-vs.png#lightbox)
1. Você também pode selecionar para apresentar a célula como um dos [tipos de célula de exibição de tabela padrão](#table-view-cell-types) por meio da lista suspensa **estilo** ou defini-la como **personalizada** e usar o design Surface para definir o layout da célula arrastando em outros widgets da interface do usuário a partir da **caixa de ferramentas**: 

    [![](table-views-images/table13-vs.png "O menu suspenso estilo")](table-views-images/table13-vs.png#lightbox)
1. Atribua um **nome** exclusivo a cada elemento da interface do usuário no design de célula do protótipo na guia **widget** do **Gerenciador de propriedades** para que você possa C# acessá-los mais tarde no código: 

    [![](table-views-images/table14-vs.png "A guia do widget")](table-views-images/table14-vs.png#lightbox)
1. Repita a etapa acima para todas as células de protótipo na exibição de tabela.
1. Em seguida, atribua classes personalizadas ao restante do design da interface do usuário, layout da exibição de detalhes e atribua **nomes** exclusivos a cada elemento da interface do usuário na exibição de detalhes para C# que você possa acessá-los também. Por exemplo: 

    [![](table-views-images/table15.png "O layout da interface do usuário")](table-views-images/table15.png#lightbox)
1. Salve as alterações no storyboard.

-----

<a name="Designing-a-Data-Model" />

## <a name="designing-a-data-model"></a>Criando um modelo de dados

Para tornar o trabalho com as informações que a exibição de tabela exibirá mais facilmente e facilitar a apresentação de informações detalhadas (como o usuário seleciona ou realça linhas no modo de exibição de tabela), crie uma classe ou classes personalizadas para atuar como o modelo de dados para as informações apresentadas .

Veja o exemplo de um aplicativo de reserva de viagem que contém uma lista de **cidades**, cada uma contendo uma lista exclusiva de **Attractions** que o usuário pode selecionar. O usuário poderá marcar um atração como um *favorito*, selecionar para obter *instruções* para um atração e *reservar um vôo* para uma determinada cidade.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Para criar o modelo de dados para um **atração**, clique com o botão direito do mouse no nome do projeto na **painel de soluções** e selecione **Adicionar** > **novo arquivo...** . Insira `AttractionInformation` para o **nome** e clique no botão **novo** : 

[![](table-views-images/data01.png "Insira AttractionInformation para o nome")](table-views-images/data01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Para criar o modelo de dados para um **atração**, clique com o botão direito do mouse no nome do projeto na **Gerenciador de soluções** e selecione **Adicionar** > **novo item...** . Selecione **classe** e insira `AttractionInformation` para o **nome** e clique no botão **Adicionar** : 

[![](table-views-images/data01-vs.png "Selecione classe e insira AttractionInformation para o nome")](table-views-images/data01-vs.png#lightbox)

-----

Edite `AttractionInformation.cs` o arquivo e faça com que ele se pareça com o seguinte:

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

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Em seguida, clique com o botão direito do mouse no nome do projeto na **painel de soluções** novamente e selecione **Adicionar** > **novo arquivo...** . Insira `CityInformation` para o **nome** e clique no botão **novo** : 

[![](table-views-images/data02.png "Insira CityInformation para o nome")](table-views-images/data02.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Em seguida, clique com o botão direito do mouse no nome do projeto na **Gerenciador de soluções** novamente e selecione **Adicionar** > **novo item...** . Insira `CityInformation` para o **nome** e clique no botão **Adicionar** : 

[![](table-views-images/data02-vs.png "Insira CityInformation para o nome")](table-views-images/data02-vs.png#lightbox)

-----

Edite `CityInformation.cs` o arquivo e faça com que ele se pareça com o seguinte:

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

Essa classe contém todas as informações sobre uma **cidade**de destino, uma coleção de **Attractions** para essa cidade e fornece dois métodos auxiliares`AddAttraction`() para facilitar a adição de Attractions à cidade.

<a name="The-Table-Data-Source" />

## <a name="the-table-view-data-source"></a>A fonte de dados de exibição de tabela

Cada exibição de tabela requer uma fonte de`UITableViewDataSource`dados () para fornecer os dados para a tabela e gerar as linhas necessárias conforme exigido pelo modo de exibição de tabela.

Para obter o exemplo fornecido acima, clique com o botão direito do mouse no nome do projeto na **Gerenciador de soluções**, selecione **Adicionar** > **novo arquivo...** e chame- `AttractionTableDatasource` o e clique no botão **novo** para criar. Em seguida, edite o `AttractionTableDatasource.cs` arquivo e faça com que ele se pareça com o seguinte:

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

Primeiro, definimos uma constante para conter o identificador exclusivo da célula do protótipo (esse é o mesmo identificador atribuído no designer de interface acima), adicionamos um atalho de volta ao controlador de exibição de tabela e criamos o armazenamento para nossos dados:

```csharp
const string CellID = "AttrCell";
public AttractionTableViewController Controller { get; set;}
public List<CityInformation> Cities { get; set;}
```

Em seguida, salvamos o controlador de exibição de tabela, criamos e populamos nossa fonte de dados (usando os modelos de dados definidos acima) quando a classe é criada:

```csharp
public AttractionTableDatasource (AttractionTableViewController controller)
{
    // Initialize
    this.Controller = controller;
    this.Cities = new List<CityInformation> ();
    PopulateCities ();
}
```

Por exemplo, o `PopulateCities` método simplesmente cria objetos de modelo de dados na memória, no entanto, eles poderiam ser facilmente lidos de um serviço Web ou banco de dado em um aplicativo real:

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

Para exibições de tabela com estilo **sem formatação** , sempre retorne 1.

O `RowsInSection` método retorna o número de linhas na seção atual:

```csharp
public override nint RowsInSection (UITableView tableView, nint section)
{
    // Return the number of attractions in the given city
    return Cities [(int)section].Attractions.Count;
}
```

Novamente, para exibições de tabela **simples** , retorne o número total de itens na fonte de dados.

O `TitleForHeader` método retorna o título para a seção determinada:

```csharp
public override string TitleForHeader (UITableView tableView, nint section)
{
    // Get the name of the current city
    return Cities [(int)section].Name;
}
```

Para um tipo de exibição de tabela **simples** , deixe o título`""`em branco ().

Por fim, quando solicitado pelo modo de exibição de tabela, crie e popule uma `GetCell` célula de protótipo usando o método: 

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

Para saber mais sobre como trabalhar com `UITableViewDatasource`um, confira a documentação do [UITableViewDatasource](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40006941) da Apple.

<a name="The-Table-View-Delegate" />

## <a name="the-table-view-delegate"></a>O delegado de exibição de tabela

Cada exibição de tabela requer um delegado`UITableViewDelegate`() para responder à interação do usuário ou a outros eventos do sistema na tabela.

Para obter o exemplo fornecido acima, clique com o botão direito do mouse no nome do projeto na **Gerenciador de soluções**, selecione **Adicionar** > **novo arquivo...** e chame- `AttractionTableDelegate` o e clique no botão **novo** para criar. Em seguida, edite o `AttractionTableDelegate.cs` arquivo e faça com que ele se pareça com o seguinte:

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

Primeiro, criamos um atalho para o controlador de exibição de tabela quando a classe é criada:

```csharp
public AttractionTableViewController Controller { get; set;}
...

public AttractionTableDelegate (AttractionTableViewController controller)
{
    // Initialize
    this.Controller = controller;
}
```

Em seguida, quando uma linha é selecionada (o usuário clica na superfície de toque da Apple Remote), desejamos marcar o **atração** representado pela linha selecionada como um favorito:

```csharp
public override void RowSelected (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    var attraction = Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row];
    attraction.IsFavorite = (!attraction.IsFavorite);

    // Update UI
    Controller.TableView.ReloadData ();
}
```

Em seguida, quando o usuário realça uma linha (dando-lhe foco usando a superfície de toque remoto da Apple), desejamos apresentar os detalhes do **atração** representado por essa linha na seção de detalhes de nosso controlador de exibição de divisão:

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

O `CanFocusRow` método é chamado para cada linha que está prestes a ficar em foco na exibição de tabela. Retornar `true` se a linha puder ser focalizada, caso `false`contrário, retornará. No caso deste exemplo, criamos um evento personalizado `AttractionHighlighted` que será gerado em cada linha à medida que receber o foco.

Para saber mais sobre como trabalhar com `UITableViewDelegate`um, confira a documentação do [UITableViewDelegate](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40006942) da Apple.

<a name="The-Table-View-Cell" />

## <a name="the-table-view-cell"></a>A célula de exibição de tabela

Para cada célula de protótipo que você adicionou ao modo de exibição de tabela no designer de interface, você também criou uma instância personalizada da célula de`UITableViewCell`exibição de tabela () para permitir que você preencha a nova célula (linha) conforme ela é criada.

Para o aplicativo de exemplo, clique duas vezes `AttractionTableCell.cs` no arquivo para abri-lo para edição e fazê-lo parecer o seguinte:

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

Essa classe fornece armazenamento para o objeto de modelo de dados`AttractionInformation` atração (conforme definido acima) exibido na linha determinada:

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

O `UpdateUI` método popula os widgets da interface do usuário (que foram adicionados ao protótipo da célula no designer de interface), conforme necessário:

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

Para saber mais sobre como trabalhar com `UITableViewCell`um, confira a documentação do [UITableViewCell](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewCell_Class/index.html#//apple_ref/doc/uid/TP40006938) da Apple.

<a name="The-Table-View-Controller" />

## <a name="the-table-view-controller"></a>O controlador de exibição de tabela

Um controlador de exibição de`UITableViewController`tabela () gerencia uma exibição de tabela que foi adicionada a um storyboard por meio do designer de interface.

Para o aplicativo de exemplo, clique duas vezes `AttractionTableViewController.cs` no arquivo para abri-lo para edição e fazê-lo parecer o seguinte:

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

Vamos examinar mais de perto essa classe. Primeiro, criamos atalhos para facilitar o acesso ao modo de exibição `DataSource` de tabela e `TableDelegate`ao. Usaremos essas informações posteriormente para se comunicar entre a exibição de tabela no lado esquerdo do modo de exibição de divisão e a exibição de detalhes à direita.

Por fim, quando a exibição de tabela é carregada na memória, criamos instâncias `AttractionTableDatasource` do `AttractionTableDelegate` e (ambas criadas acima) e as anexamos à exibição de tabela.

Para saber mais sobre como trabalhar com `UITableViewController`um, confira a documentação do [UITableViewController](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewController_Class/index.html#//apple_ref/doc/uid/TP40007523) da Apple.

<a name="Pulling-it-All-Together" />

## <a name="pulling-it-all-together"></a>Reunindo tudo isso

Conforme indicado no início deste documento, as exibições de tabela são normalmente exibidas em um lado de um [modo de exibição de divisão](~/ios/tvos/user-interface/split-views.md) como navegação, com os detalhes do item selecionado exibido no lado oposto. Por exemplo: 

[![](table-views-images/intro01.png "Execução do aplicativo de exemplo")](table-views-images/intro01.png#lightbox)

Como esse é um padrão padrão no tvOS, vamos examinar as etapas finais para reunir tudo e fazer com que os lados esquerdo e direito da exibição de divisão interajam entre si.

<a name="The-Detail-View" />

### <a name="the-detail-view"></a>A exibição de detalhes

Para o exemplo do aplicativo de viagem apresentado acima, uma classe personalizada (`AttractionViewController`) é definida para o controlador de exibição padrão apresentado no lado direito do modo de exibição de divisão como o modo de exibição de detalhes:

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

Aqui, fornecemos o **atração** (`AttractionInformation`) sendo exibido como uma propriedade e criamos um `UpdateUI` método que popula os widgets da interface do usuário adicionados à exibição no designer de interface.

Também definimos um atalho de volta para o controlador de exibição de`SplitView`divisão () que usaremos para comunicar as alterações de volta para o modo`AcctractionTableView`de exibição de tabela ().

Por fim, as ações personalizadas (eventos) foram adicionadas `UIButton` às três instâncias criadas no designer de interface, que permitem ao usuário marcar um atração como um _favorito_, obter _instruções_ para um atração e _reservar um vôo_ para um determinado azul.

<a name="The-Navigation-View-Controller" />

### <a name="the-navigation-view-controller"></a>O controlador de exibição de navegação

Como o controlador de exibição de tabela está aninhado em um controlador de exibição de navegação no lado esquerdo do modo de exibição de divisão, o controlador de exibição`MasterNavigationController`de navegação foi atribuído a uma classe personalizada () no designer de interface e definido da seguinte maneira:

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

Novamente, essa classe apenas define alguns atalhos para facilitar a comunicação entre os dois lados do controlador de exibição de divisão:

- `SplitView`-É um link para o controlador de exibição de`MainSpiltViewController`divisão () ao qual o controlador de exibição de navegação pertence.
- `TableController`-Obtém o controlador de exibição de`AttractionTableViewController`tabela () que é apresentado como o modo de exibição superior no controlador de exibição de navegação.

<a name="The-Split-View-Controller" />

### <a name="the-split-view-controller"></a>O controlador de exibição de divisão

Como o controlador de exibição de divisão é a base de nosso aplicativo, criamos uma classe`MasterSplitViewController`personalizada () para ele no designer de interface e o definimos da seguinte maneira:

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

Primeiro, criamos atalhos para o lado de **detalhes** do modo de exibição`AttractionViewController`de divisão () e para o`MasterNavigationController`lado **mestre** (). Mais uma vez, isso facilita a comunicação entre os dois lados mais tarde.

Em seguida, quando o modo de exibição de divisão é carregado na memória, anexamos o controlador de exibição de divisão a ambos os lados do modo de exibição de divisão e respondemos ao usuário`AttractionHighlighted`realçando um atração no modo de exibição de tabela () exibindo o novo atração no lado de **detalhes** de o modo de exibição de divisão.

Consulte o aplicativo de exemplo [tvTables](https://docs.microsoft.com/samples/xamarin/ios-samples/tvos-tvtable) para obter uma implementação completa de exibições de tabela dentro de uma exibição de divisão.

## <a name="table-views-in-detail"></a>Exibições de tabela em detalhes

Como o tvOS é baseado no iOS, as exibições de tabela e os controladores de exibição de tabela são projetados e se comportam de maneira semelhante. Para obter informações mais detalhadas sobre como trabalhar com a exibição de tabela em um aplicativo do Xamarin, consulte nossa documentação do iOS [trabalhando com tabelas e células](~/ios/user-interface/controls/tables/index.md) .

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou a criação e o trabalho com exibições de tabela dentro de um aplicativo Xamarin. tvOS. E apresentamos um exemplo de como trabalhar com uma exibição de tabela dentro de um modo de exibição de divisão, que é o uso típico de uma exibição de tabela em um aplicativo tvOS.

## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [UITableViewController](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewController_Class/index.html#//apple_ref/doc/uid/TP40007523)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
