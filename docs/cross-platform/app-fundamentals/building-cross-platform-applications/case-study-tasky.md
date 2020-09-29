---
title: 'Estudo de caso de aplicativo de plataforma cruzada: tarefa'
description: Este documento descreve como o aplicativo de exemplo portátil de tarefas foi projetado e criado como um aplicativo móvel de plataforma cruzada. Ele aborda os requisitos, a interface, o modelo de dados, a funcionalidade principal, a implementação e muito mais do aplicativo.
ms.prod: xamarin
ms.assetid: B581B2D0-9890-C383-C654-0B0E12DAD5A6
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 803b42cfcc27dc86b0d4bc78fc4745af5565e8cb
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457543"
---
# <a name="cross-platform-app-case-study-tasky"></a>Estudo de caso de aplicativo de plataforma cruzada: tarefa

A *tarefa* *portátil* é um aplicativo simples de lista de tarefas pendentes. Este documento discute como ele foi projetado e criado, seguindo as orientações do documento [criando aplicativos para várias plataformas](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) . A discussão aborda as seguintes áreas:

<a name="Design_Process"></a>

## <a name="design-process"></a>Processo de design

É aconselhável criar um mapa de estrada para o que você deseja obter antes de começar a codificar. Isso é especialmente verdadeiro para o desenvolvimento de plataforma cruzada, em que você está criando funcionalidades que serão expostas de várias maneiras. Começar com uma clara ideia do que você está criando economiza tempo e esforço posteriormente no ciclo de desenvolvimento.

 <a name="Requirements"></a>

### <a name="requirements"></a>Requisitos

A primeira etapa na criação de um aplicativo é identificar os recursos desejados. Elas podem ser metas de alto nível ou casos de uso detalhados. A tarefa tem requisitos funcionais diretos:

- Exibir uma lista de tarefas
- Adicionar, editar e excluir tarefas
- Definir o status de uma tarefa como ' done '

Você deve considerar o uso de recursos específicos da plataforma.  A tarefa pode aproveitar os blocos dinâmicos de isolamento de iOS ou Windows Phone? Mesmo que você não use recursos específicos da plataforma na primeira versão, planeje antecipadamente para garantir que seus negócios & camadas de dados possam acomodá-los.

 <a name="User_Interface_Design"></a>

### <a name="user-interface-design"></a>Design da interface do usuário

Comece com um design de alto nível que pode ser implementado nas plataformas de destino. Tome cuidado para observar as restrições da interface do usuário da plataforma específico. Por exemplo, um `TabBarController` no IOS pode exibir mais de cinco botões, enquanto o equivalente Windows Phone pode exibir até quatro.
Desenhe o fluxo de tela usando a ferramenta de sua escolha (papel funciona).

 [![Desenhe o fluxo de tela usando a ferramenta de seu papel de escolha](case-study-tasky-images/taskydesign.png)](case-study-tasky-images/taskydesign.png#lightbox)

 <a name="Data_Model"></a>

### <a name="data-model"></a>Modelo de dados

Saber quais dados precisam ser armazenados ajudará a determinar qual mecanismo de persistência será usado. Consulte [acesso a dados entre plataformas](~/cross-platform/app-fundamentals/index.md) para obter informações sobre os mecanismos de armazenamento disponíveis e ajudar a decidir entre eles. Para este projeto, vamos usar SQLite.NET.

A tarefa precisa armazenar três propriedades para cada ' TaskItem ':

- **Name** – Cadeia de caracteres
- **Observações** – cadeia de caracteres
- **Concluído** – booliano

 <a name="Core_Functionality"></a>

### <a name="core-functionality"></a>Funcionalidade principal

Considere a API que a interface do usuário precisará consumir para atender aos requisitos. Uma lista de tarefas pendentes requer as seguintes funções:

- **Listar todas as tarefas** – para exibir a lista de tela principal de todas as tarefas disponíveis
- **Obter uma tarefa** – quando uma linha de tarefa é tocada
- **Salvar uma tarefa** – quando uma tarefa é editada
- **Excluir uma tarefa** – quando uma tarefa é excluída
- **Criar tarefa vazia** – quando uma nova tarefa é criada

Para obter a reutilização de código, essa API deve ser implementada uma vez na *biblioteca de classes portátil*.

 <a name="Implementation"></a>

### <a name="implementation"></a>Implementação

Depois que o design do aplicativo tiver sido acordado, considere como ele pode ser implementado como um aplicativo de plataforma cruzada. Isso se tornará a arquitetura do aplicativo. Seguindo as orientações no documento [criando aplicativos entre plataformas](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) , o código do aplicativo deve ser dividido nas seguintes partes:

- **Código comum** – um projeto comum que contém código reutilizado para armazenar os dados da tarefa; expor uma classe de modelo e uma API para gerenciar o salvamento e o carregamento de dados.
- **Código específico da plataforma** – projetos específicos da plataforma que implementam uma interface do usuário nativa para cada sistema operacional, utilizando o código comum como o ' back-end '.

[![Os projetos específicos da plataforma implementam uma interface do usuário nativa para cada sistema operacional, utilizando o código comum como back-end](case-study-tasky-images/taskypro-architecture.png)](case-study-tasky-images/taskypro-architecture.png#lightbox)

Essas duas partes são descritas nas seções a seguir.

 <a name="Common_(PCL)_Code"></a>

## <a name="common-pcl-code"></a>Código comum (PCL)

A tarefa portátil usa a estratégia de biblioteca de classes portátil para compartilhar código comum. Consulte o documento [Opções de código de compartilhamento](~/cross-platform/app-fundamentals/code-sharing.md) para obter uma descrição das opções de compartilhamento de código.

Todo o código comum, incluindo a camada de acesso a dados, o código e os contratos dele, é colocado no projeto de biblioteca.

O projeto PCL completo é ilustrado abaixo. Todo o código na biblioteca portátil é compatível com cada plataforma de destino. Quando implantado, cada aplicativo nativo fará referência a essa biblioteca.

![Quando implantado, cada aplicativo nativo fará referência a essa biblioteca](case-study-tasky-images/portable-project.png)

O diagrama de classe abaixo mostra as classes agrupadas por camada. A `SQLiteConnection` classe é um código clichê do pacote SQLite-net. O restante das classes é o código personalizado para a tarefa. As `TaskItemManager` `TaskItem` classes e representam a API que é exposta aos aplicativos específicos da plataforma.

 [![As classes TaskItemmanager e TaskItem representam a API que é exposta aos aplicativos específicos da plataforma](case-study-tasky-images/classdiagram-core.png)](case-study-tasky-images/classdiagram-core.png#lightbox)

O uso de namespaces para separar as camadas ajuda a gerenciar referências entre cada camada. Os projetos específicos da plataforma devem precisar incluir apenas uma `using` instrução para a camada de negócios. A camada de acesso a dados e a camada de dados devem ser encapsuladas pela API que é exposta pelo `TaskItemManager` na camada de negócios.

 <a name="References"></a>

### <a name="references"></a>Referências

Bibliotecas de classes portáteis precisam ser utilizáveis em várias plataformas, cada uma com níveis variados de suporte para recursos de plataforma e estrutura. Por isso, há limitações em quais pacotes e bibliotecas de estrutura podem ser usadas. Por exemplo, o Xamarin. iOS não oferece suporte à `dynamic` palavra-chave c#, portanto, uma biblioteca de classes portátil não pode usar nenhum pacote que dependa de código dinâmico, mesmo que esse código funcione no Android. Visual Studio para Mac impedirá a adição de pacotes e referências incompatíveis, mas você desejará manter as limitações em mente para evitar surpresas posteriormente.

Observação: você verá que seus projetos fazem referência a bibliotecas de estrutura que você não usou. Essas referências são incluídas como parte dos modelos de projeto do Xamarin. Quando os aplicativos são compilados, o processo de vinculação removerá o código não referenciado, portanto, embora `System.Xml` tenha sido referenciado, ele não será incluído no aplicativo final porque não estamos usando nenhuma função XML.

 <a name="Data_Layer_(DL)"></a>

### <a name="data-layer-dl"></a>Camada de dados (DL)

A camada de dados contém o código que faz o armazenamento físico de dados – seja para um banco de dado, arquivos simples ou outro mecanismo. A camada de dados tarefa consiste em duas partes: a biblioteca SQLite-NET e o código personalizado adicionado para conectá-lo.

A tarefa depende do pacote de NuGet do SQLite-net (publicado por Frank Krueger) para inserir o código SQLite-NET que fornece uma interface de banco de dados de ORM (mapeamento relacional de objeto). A `TaskItemDatabase` classe herda de `SQLiteConnection` e adiciona os métodos CRUD (criar, ler, atualizar, excluir) necessários para ler e gravar dados no SQLite. É uma implementação padronizada simples de métodos CRUD genéricos que podem ser reutilizados em outros projetos.

O `TaskItemDatabase` é um singleton, garantindo que todo o acesso ocorra na mesma instância. Um bloqueio é usado para impedir o acesso simultâneo de vários threads.

 <a name="SQLite_on_WIndows_Phone"></a>

#### <a name="sqlite-on-windows-phone"></a>SQLite em Windows Phone

Embora o iOS e o Android sejam fornecidos com o SQLite como parte do sistema operacional, Windows Phone não inclui um mecanismo de banco de dados compatível. Para compartilhar o código entre todas as três plataformas, é necessário ter uma versão nativa do SQLite do Windows Phone. Consulte [trabalhando com um banco de dados local](~/xamarin-forms/data-cloud/data/databases.md) para obter mais informações sobre como configurar seu projeto de Windows Phone para SQLite.

 <a name="Using_an_Interface_to_Generalize_Data_Access"></a>

#### <a name="using-an-interface-to-generalize-data-access"></a>Usando uma interface para generalizar o acesso a dados

A camada de dados assume uma dependência `BL.Contracts.IBusinessIdentity` para que possa implementar métodos de acesso a dados abstratos que exigem uma chave primária. Qualquer classe de camada comercial que implementa a interface pode ser persistida na camada de dados.

A interface especifica apenas uma propriedade Integer para atuar como a chave primária:

```csharp
public interface IBusinessEntity {
    int ID { get; set; }
}
```

A classe base implementa a interface e adiciona os atributos SQLite-NET para marcá-lo como uma chave primária de incremento automático. Qualquer classe na camada de negócios que implementa essa classe base pode ser persistida na camada de dados:

```csharp
public abstract class BusinessEntityBase : IBusinessEntity {
    public BusinessEntityBase () {}
    [PrimaryKey, AutoIncrement]
    public int ID { get; set; }
}
```

Um exemplo dos métodos genéricos na camada de dados que usam a interface é esse `GetItem<T>` método:

```csharp
public T GetItem<T> (int id) where T : BL.Contracts.IBusinessEntity, new ()
{
    lock (locker) {
        return Table<T>().FirstOrDefault(x => x.ID == id);
    }
}
```

 <a name="Locking_to_prevent_Concurrent_Access"></a>

#### <a name="locking-to-prevent-concurrent-access"></a>Bloqueio para impedir o acesso simultâneo

Um [bloqueio](/previous-versions/visualstudio/visual-studio-2010/c5kehkcz(v=vs.100)) é implementado dentro da `TaskItemDatabase` classe para impedir o acesso simultâneo ao banco de dados. Isso é para garantir o acesso simultâneo de threads diferentes é serializado (caso contrário, um componente de interface do usuário pode tentar ler o banco de dados ao mesmo tempo em que um thread em segundo plano está atualizando). Um exemplo de como o bloqueio é implementado é mostrado aqui:

```csharp
static object locker = new object ();
public IEnumerable<T> GetItems<T> () where T : BL.Contracts.IBusinessEntity, new ()
{
    lock (locker) {
        return (from i in Table<T> () select i).ToList ();
    }
}
public T GetItem<T> (int id) where T : BL.Contracts.IBusinessEntity, new ()
{
    lock (locker) {
        return Table<T>().FirstOrDefault(x => x.ID == id);
    }
}
```

A maior parte do código da camada de dados pode ser reutilizada em outros projetos. O único código específico do aplicativo na camada é a `CreateTable<TaskItem>` chamada no `TaskItemDatabase` Construtor.

 <a name="Data_Access_Layer_(DAL)"></a>

### <a name="data-access-layer-dal"></a>Camada de acesso a dados (DAL)

A `TaskItemRepository` classe encapsula o mecanismo de armazenamento de dados com uma API fortemente tipada que permite que os `TaskItem` objetos sejam criados, excluídos, recuperados e atualizados.

 <a name="Using_Conditional_Compilation"></a>

#### <a name="using-conditional-compilation"></a>Usando a compilação condicional

A classe usa a compilação condicional para definir o local do arquivo – este é um exemplo de implementação de divergência de plataforma. A propriedade que retorna o caminho é compilada para código diferente em cada plataforma. As diretivas de compilador específicas do código e da plataforma são mostradas aqui:

```csharp
public static string DatabaseFilePath {
    get {
        var sqliteFilename = "TaskDB.db3";
#if SILVERLIGHT
        // Windows Phone expects a local path, not absolute
        var path = sqliteFilename;
#else
#if __ANDROID__
        // Just use whatever directory SpecialFolder.Personal returns
        string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); ;
#else
        // we need to put in /Library/ on iOS5.1+ to meet Apple's iCloud terms
        // (they don't want non-user-generated data in Documents)
        string documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
        string libraryPath = Path.Combine (documentsPath, "..", "Library"); // Library folder
#endif
        var path = Path.Combine (libraryPath, sqliteFilename);
                #endif
                return path;
    }
}
```

Dependendo da plataforma, a saída será " <app
path> /library/TaskDB.DB3" para IOS, " <app
path> /Documents/TaskDB.DB3" para Android ou apenas "TaskDB. DB3" para Windows Phone.

### <a name="business-layer-bl"></a>Camada de negócios (BL)

A camada de negócios implementa as classes de modelo e uma fachada para gerenciá-las.
Em tarefa, o modelo é a `TaskItem` classe e `TaskItemManager` implementa o padrão de fachada para fornecer uma API para gerenciar `TaskItems` .

 <a name="Façade"></a>

#### <a name="faade"></a>Fachada

 `TaskItemManager` encapsula o `DAL.TaskItemRepository` para fornecer os métodos get, Save e Delete que serão referenciados pelas camadas do aplicativo e da interface do usuário.

As regras de negócio e a lógica seriam colocadas aqui, se necessário, por exemplo, todas as regras de validação que devem ser satisfeitas antes que um objeto seja salvo.

 <a name="API_for_Platform-Specific_Code"></a>

### <a name="api-for-platform-specific-code"></a>API para código específico da plataforma

Depois que o código comum tiver sido escrito, a interface do usuário deverá ser criada para coletar e exibir os dados expostos por ele. A `TaskItemManager` classe implementa o padrão de fachada para fornecer uma API simples para o código do aplicativo acessar.

O código escrito em cada projeto específico da plataforma geralmente será rigidamente acoplado ao SDK nativo do dispositivo e acessará apenas o código comum usando a API definida pelo `TaskItemManager` . Isso inclui os métodos e as classes de negócios que ele expõe, como `TaskItem` .

As imagens não são compartilhadas entre plataformas, mas adicionadas de forma independente a cada projeto. Isso é importante porque cada plataforma manipula imagens de forma diferente, usando nomes de arquivo, diretórios e resoluções diferentes.

As seções restantes abordam os detalhes de implementação específicos da plataforma da interface do usuário.

 <a name="iOS_App"></a>

## <a name="ios-app"></a>Aplicativo iOS

Há apenas algumas classes necessárias para implementar o aplicativo de tarefa do iOS usando o projeto PCL comum para armazenar e recuperar dados. O projeto do Xamarin. iOS completo do iOS é mostrado abaixo:

 ![o projeto do iOS é mostrado aqui](case-study-tasky-images/taskyios-solution.png)

As classes são mostradas neste diagrama, agrupadas em camadas.

 [![As classes são mostradas neste diagrama, agrupadas em camadas](case-study-tasky-images/classdiagram-android.png)](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References"></a>

### <a name="references"></a>Referências

O aplicativo iOS faz referência às bibliotecas de SDK específicas da plataforma – por exemplo, Xamarin. iOS e MonoTouch. caixa de diálogo-1.

Ele também deve fazer referência ao `TaskyPortableLibrary` projeto PCL.
A lista de referências é mostrada aqui:

 ![A lista de referências é mostrada aqui](case-study-tasky-images/taskyios-references.png)

A camada de aplicativo e a camada de interface do usuário são implementadas neste projeto usando essas referências.

 <a name="Application_Layer_(AL)"></a>

### <a name="application-layer-al"></a>Camada de aplicativo (AL)

A camada de aplicativo contém classes específicas da plataforma necessárias para "associar" os objetos expostos pelo PCL à interface do usuário. O aplicativo específico para iOS tem duas classes para ajudar a exibir tarefas:

- **Editingion** – essa classe é usada para associar listas de tarefas à interface do usuário. Como `MonoTouch.Dialog` o foi usado para a lista de tarefas, precisamos implementar esse auxiliar para habilitar a funcionalidade de passar para a exclusão no  `UITableView` . Passar o dedo para exclusão é comum no iOS, mas não no Android ou Windows Phone, portanto, o projeto específico do iOS é o único que o implementa.
- **TaskDialog** – essa classe é usada para associar uma única tarefa à interface do usuário. Ele usa a `MonoTouch.Dialog` API de reflexão para "encapsular" o `TaskItem` objeto com uma classe que contém os atributos corretos para permitir que a tela de entrada seja formatada corretamente.

A `TaskDialog` classe usa `MonoTouch.Dialog` atributos para criar uma tela com base nas propriedades de uma classe. A classe é parecida com esta:

```csharp
public class TaskDialog {
    public TaskDialog (TaskItem task)
    {
        Name = task.Name;
        Notes = task.Notes;
        Done = task.Done;
    }
    [Entry("task name")]
    public string Name { get; set; }
    [Entry("other task info")]
    public string Notes { get; set; }
    [Entry("Done")]
    public bool Done { get; set; }
    [Section ("")]
    [OnTap ("SaveTask")]    // method in HomeScreen
    [Alignment (UITextAlignment.Center)]
    public string Save;
    [Section ("")]
    [OnTap ("DeleteTask")]  // method in HomeScreen
    [Alignment (UITextAlignment.Center)]
    public string Delete;
}
```

Observe que os `OnTap` atributos exigem um nome de método – esses métodos devem existir na classe em que o `MonoTouch.Dialog.BindingContext` é criado (nesse caso, a `HomeScreen` classe discutida na próxima seção).

 <a name="User_Interface_Layer_(UI)"></a>

### <a name="user-interface-layer-ui"></a>Camada da interface do usuário (IU)

A camada da interface do usuário consiste nas seguintes classes:

1. **AppDelegate** – contém chamadas para a API de aparência para estilizar as fontes e cores usadas no aplicativo. A tarefa é um aplicativo simples, portanto, não há outras tarefas de inicialização em execução no  `FinishedLaunching` .
2. **Telas** – subclasses de `UIViewController` que definem cada tela e seu comportamento. As telas reúnem a interface do usuário com classes de camada de aplicativo e a API comum ( `TaskItemManager` ). Neste exemplo, as telas são criadas no código, mas elas podem ter sido projetadas usando Interface Builder do Xcode ou o designer do storyboard.
3. **Imagens** – os elementos visuais são uma parte importante de cada aplicativo. A tarefa tem imagens de tela inicial e ícone, que para o iOS deve ser fornecida em resolução regular e retina.

 <a name="Home_Screen"></a>

#### <a name="home-screen"></a>Tela de Início

A tela inicial é uma `MonoTouch.Dialog` tela que exibe uma lista de tarefas do banco de dados SQLite. Ele herda de `DialogViewController` e implementa o código para definir o `Root` para conter uma coleção de `TaskItem` objetos para exibição.

 [![Ele herda de DialogViewController e implementa o código para definir a raiz para conter uma coleção de objetos TaskItem para exibição](case-study-tasky-images/ios-taskylist.png)](case-study-tasky-images/ios-taskylist.png#lightbox)

Os dois métodos principais relacionados à exibição e interação com a lista de tarefas são:

1. **Populable** – usa o método da camada de negócios  `TaskManager.GetTasks` para recuperar uma coleção de  `TaskItem` objetos a serem exibidos.
2. **Selecionado** – quando uma linha é tocada, o exibe a tarefa em uma nova tela.

 <a name="Task_Details_Screen"></a>

#### <a name="task-details-screen"></a>Tela de detalhes da tarefa

Detalhes da tarefa é uma tela de entrada que permite que as tarefas sejam editadas ou excluídas.

O taskal usa a `MonoTouch.Dialog` API de reflexão para exibir a tela, portanto, não há nenhuma `UIViewController` implementação. Em vez disso, a `HomeScreen` classe instancia e exibe um `DialogViewController` usando a `TaskDialog` classe da camada de aplicativo.

Esta captura de tela mostra uma exibição vazia que demonstra o `Entry` atributo definindo o texto da marca d' água nos campos **Name** e **Notes** :

 [![Esta captura de tela mostra uma exibição vazia que demonstra o atributo de entrada Configurando o texto da marca d' água nos campos nome e anotações](case-study-tasky-images/ios-taskydetail.png)](case-study-tasky-images/ios-taskydetail.png#lightbox)

A funcionalidade da tela de **detalhes da tarefa** (como salvar ou excluir uma tarefa) deve ser implementada na `HomeScreen` classe, pois é onde o `MonoTouch.Dialog.BindingContext` é criado. Os métodos a seguir `HomeScreen` dão suporte à tela de detalhes da tarefa:

1. **ShowTaskDetails** – cria um  `MonoTouch.Dialog.BindingContext` para renderizar uma tela. Ele cria a tela de entrada usando reflexão para recuperar os nomes e tipos de propriedade da  `TaskDialog` classe. Informações adicionais, como o texto de marca d' água para as caixas de entrada, são implementadas com atributos nas propriedades.
2. **SaveTask** – esse método é referenciado na  `TaskDialog` classe por meio de um  `OnTap` atributo. Ele é chamado quando  **Save** é pressionado e usa um  `MonoTouch.Dialog.BindingContext` para recuperar os dados inseridos pelo usuário antes de salvar as alterações usando  `TaskItemManager` .
3. **DeleteTask** – esse método é referenciado na  `TaskDialog` classe por meio de um  `OnTap` atributo. Ele usa  `TaskItemManager` para excluir os dados usando a chave primária (Propriedade ID).

 <a name="Android_App"></a>

## <a name="android-app"></a>Aplicativo do Android

O projeto Xamarin. Android completo é mostrado abaixo:

 ![O projeto do Android é mostrado aqui](case-study-tasky-images/taskyandroid-solution.png)

O diagrama de classe, com classes agrupadas por camada:

 [![O diagrama de classe, com classes agrupadas por camada](case-study-tasky-images/classdiagram-android.png)](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References"></a>

### <a name="references"></a>Referências

O projeto de aplicativo do Android deve fazer referência ao assembly Xamarin. Android específico da plataforma para acessar classes do SDK do Android.

Ele também deve fazer referência ao projeto PCL (por exemplo, TaskyPortableLibrary) para acessar os dados comuns e o código da camada de negócios.

 ![TaskyPortableLibrary para acessar os dados comuns e o código da camada de negócios](case-study-tasky-images/taskyandroid-references.png)

 <a name="Application_Layer_(AL)"></a>

### <a name="application-layer-al"></a>Camada de aplicativo (AL)

Semelhante à versão do iOS que vimos anteriormente, a camada de aplicativo na versão do Android contém classes específicas da plataforma necessárias para "associar" os objetos expostos pelo núcleo à interface do usuário.

 **TaskListAdapter** – para exibir uma lista \<T> de objetos, precisamos implementar um adaptador para exibir objetos personalizados em um `ListView` . O adaptador controla qual layout é usado para cada item na lista – nesse caso, o código usa um layout interno do Android `SimpleListItemChecked` .

 <a name="User_Interface_(UI)"></a>

### <a name="user-interface-ui"></a>Interface do Usuário (IU)

A camada de interface do usuário do aplicativo Android é uma combinação de código e marcação XML.

- **Recursos/layout** – layouts de tela e o design de célula de linha implementados como arquivos AXML. A AXML pode ser escrita manualmente ou disposta visualmente usando o designer de interface do usuário do Xamarin para Android.
- **Recursos/desenháveis** – imagens (ícones) e botão personalizado.
- **Telas** – subclasses de atividade que definem cada tela e seu comportamento. Une a interface do usuário com classes de camada de aplicativo e a API comum ( `TaskItemManager` ).

 <a name="Home_Screen"></a>

#### <a name="home-screen"></a>Tela de Início

A tela inicial consiste em uma subclasse de atividade `HomeScreen` e o `HomeScreen.axml` arquivo que define o layout (posição do botão e da lista de tarefas). A tela é parecida com esta:

 [![A tela é parecida com esta](case-study-tasky-images/android-taskylist.png)](case-study-tasky-images/android-taskylist.png#lightbox)

O código da tela inicial define os manipuladores para clicar no botão e clicar em itens na lista, bem como preencher a lista no `OnResume` método (para que ele reflita as alterações feitas na tela de detalhes da tarefa). Os dados são carregados usando a camada de negócios `TaskItemManager` e a `TaskListAdapter` da camada de aplicativo.

 <a name="Task_Details_Screen"></a>

#### <a name="task-details-screen"></a>Tela de detalhes da tarefa

A tela de detalhes da tarefa também consiste em uma `Activity` subclasse e um arquivo de layout AXML. O layout determina o local dos controles de entrada e a classe C# define o comportamento para carregar e salvar `TaskItem` objetos.

 [![A classe define o comportamento para carregar e salvar objetos TaskItem](case-study-tasky-images/android-taskydetail.png)](case-study-tasky-images/android-taskydetail.png#lightbox)

Todas as referências à biblioteca PCL são por meio da `TaskItemManager` classe.

 <a name="Windows_Phone_App"></a>

## <a name="windows-phone-app"></a>Windows Phone aplicativo
O projeto de Windows Phone completo:

 ![Windows Phone aplicativo o projeto Windows Phone completo](case-study-tasky-images/taskywp7-solution.png)

O diagrama a seguir apresenta as classes agrupadas em camadas:

 [![Este diagrama apresenta as classes agrupadas em camadas](case-study-tasky-images/classdiagram-wp7.png)](case-study-tasky-images/classdiagram-wp7.png#lightbox)

 <a name="References"></a>

### <a name="references"></a>Referências

O projeto específico da plataforma deve fazer referência às bibliotecas específicas da plataforma necessárias (como `Microsoft.Phone` e `System.Windows` ) para criar um aplicativo de Windows Phone válido.

Ele também deve fazer referência ao projeto PCL (por exemplo, `TaskyPortableLibrary`) para utilizar a `TaskItem` classe e o banco de dados.

 ![TaskyPortableLibrary utilizar a classe TaskItem e o banco de dados](case-study-tasky-images/taskywp7-references.png)

 <a name="Application_Layer_(AL)"></a>

### <a name="application-layer-al"></a>Camada de aplicativo (AL)

Novamente, assim como nas versões iOS e Android, a camada de aplicativo consiste em elementos não visuais que ajudam a associar dados à interface do usuário.

 <a name="ViewModels"></a>

#### <a name="viewmodels"></a>ViewModels

Os ViewModels encapsulam dados do PCL ( `TaskItemManager` ) e os apresentam de forma que podem ser consumidos pela ligação de dados do Silverlight/XAML. Este é um exemplo de comportamento específico da plataforma (conforme discutido no documento aplicativos de plataforma cruzada).

 <a name="User_Interface_(UI)"></a>

### <a name="user-interface-ui"></a>Interface do Usuário (IU)

O XAML tem um recurso de vinculação de dados exclusivo que pode ser declarado na marcação e reduzir a quantidade de código necessária para exibir objetos:

1. **Páginas** – arquivos XAML e seu code-behind definem a interface do usuário e referenciam os ViewModels e o projeto PCL para exibir e coletar dados.
2. **Imagens** – tela inicial, imagens de plano de fundo e ícone são uma parte importante da interface do usuário.

 <a name="MainPage"></a>

#### <a name="mainpage"></a>MainPage

A classe MainPage usa o `TaskListViewModel` para exibir dados usando os recursos de associação de dados do XAML. A página `DataContext` é definida como o modelo de exibição, que é preenchido de forma assíncrona. A `{Binding}` sintaxe no XAML determina como os dados são exibidos.

 <a name="TaskDetailsPage"></a>

#### <a name="taskdetailspage"></a>TaskDetailsPage

Cada tarefa é exibida ligando o `TaskViewModel` ao XAML definido no TaskDetailsPage. XAML. Os dados da tarefa são recuperados por meio do `TaskItemManager` na camada de negócios.

 <a name="Results"></a>

## <a name="results"></a>Resultados

Os aplicativos resultantes têm esta aparência em cada plataforma:

 <a name="iOS"></a>

### <a name="ios"></a>iOS

O aplicativo usa o design de interface do usuário padrão iOS, como o botão "Adicionar" posicionado na barra de navegação e usando o ícone de **adição (+)** interno. Ele também usa o `UINavigationController` comportamento de botão "voltar" padrão e dá suporte a "passar o dedo para exclusão" na tabela.

 [ ![ Ele também usa o comportamento padrão do botão voltar do UINavigationController e dá suporte ao passar o dedo para exclusão na tabela](case-study-tasky-images/ios-taskylist.png)](case-study-tasky-images/ios-taskylist.png#lightbox) . [ ![ ele também usa o comportamento padrão do botão voltar do UINavigationController e dá suporte a passar o dedo para exclusão na tabela](case-study-tasky-images/ios-taskylist.png)](case-study-tasky-images/ios-taskylist.png#lightbox)

 <a name="Android"></a>

### <a name="android"></a>Android

O aplicativo do Android usa controles internos, incluindo o layout interno para linhas que exigem um ' tique ' exibido. O comportamento de retorno de hardware/sistema é suportado além de um botão voltar na tela.

 [ ![ O comportamento de retorno de hardware/sistema é suportado além de um botão voltar na tela,](case-study-tasky-images/android-taskylist.png)](case-study-tasky-images/android-taskylist.png#lightbox)[ ![ o comportamento de back-hardware/sistema tem suporte além de um botão voltar na tela](case-study-tasky-images/android-taskylist.png)](case-study-tasky-images/android-taskylist.png#lightbox)

 <a name="Windows_Phone"></a>

### <a name="windows-phone"></a>Windows Phone

O aplicativo Windows Phone usa o layout padrão, preenchendo a barra de aplicativos na parte inferior da tela, em vez de uma barra de navegação na parte superior.

 [ ![ O aplicativo Windows Phone usa o layout padrão, preenchendo a barra de aplicativos na parte inferior da tela, em vez de uma barra de navegação na parte superior](case-study-tasky-images/wp-taskylist.png)](case-study-tasky-images/wp-taskylist.png#lightbox) [ ![ , o aplicativo Windows Phone usa o layout padrão, preenchendo a barra de aplicativos na parte inferior da tela, em vez de uma barra de navegação na parte superior](case-study-tasky-images/wp-taskylist.png)](case-study-tasky-images/wp-taskylist.png#lightbox)

 <a name="Summary"></a>

## <a name="summary"></a>Resumo

Este documento forneceu uma explicação detalhada de como os princípios de design de aplicativos em camadas foram aplicados a um aplicativo simples para facilitar o reutilização de código em três plataformas móveis: iOS, Android e Windows Phone.

Ele descreveu o processo usado para criar as camadas do aplicativo e discutiu qual &amp; funcionalidade de código foi implementada em cada camada.

O código pode ser baixado do [GitHub](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable).

## <a name="related-links"></a>Links Relacionados

- [Criando aplicativos de plataforma cruzada (documento principal)](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [Aplicativo de exemplo portátil para tarefas (github)](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)