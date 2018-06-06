---
title: 'Estudo de caso de aplicativos de plataforma cruzada: Tasky'
description: Este documento descreve como o aplicativo de exemplo Tasky portátil foi projetado e criado como um aplicativo móvel de plataforma cruzada. Ele aborda o aplicativo requisitos, interface, modelo de dados, funcionalidade básica, implementação e muito mais.
ms.prod: xamarin
ms.assetid: B581B2D0-9890-C383-C654-0B0E12DAD5A6
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 48650445d06ad3bc7ca6d4da84c9b8837f8a0f88
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782229"
---
# <a name="cross-platform-app-case-study-tasky"></a>Estudo de caso de aplicativos de plataforma cruzada: Tasky

*Tasky* *portátil* é um aplicativo de lista de tarefas simples. Este documento aborda como ele foi projetado e criado, seguindo a orientação do [criando aplicativos de plataforma cruzada](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) documento. A discussão abrange as seguintes áreas:

<a name="Design_Process" />

## <a name="design-process"></a>Processo de design

É recomendável criar um de mapa para o que você deseja atingir antes de iniciar a codificação. Isso é especialmente verdadeiro para o desenvolvimento de plataforma cruzada, onde você está criando a funcionalidade que será exposta de várias maneiras. Começando com uma ideia clara de que você está criando economiza tempo e esforço posteriormente no ciclo de desenvolvimento.

 <a name="Requirements" />

### <a name="requirements"></a>Requisitos

A primeira etapa na criação de um aplicativo é identificar os recursos desejados. Eles podem ser metas de alto nível ou são detalhadas casos de uso. Tasky tem requisitos funcionais simples:

 -  Exibir uma lista de tarefas
 -  Adicionar, editar e excluir tarefas
 -  Definir status da tarefa para 'done'

Você deve considerar o uso de recursos específicos de plataforma.  Tasky pode tirar vantagem do isolamento geográfico do iOS ou Windows Phone Live blocos? Mesmo se você não usar recursos específicos de plataforma na primeira versão, você deve planejar assegurar-se de que seus negócios e camadas de dados podem acomodá-los.

 <a name="User_Interface_Design" />

### <a name="user-interface-design"></a>Design da interface do usuário

Iniciar com um design de alto nível que pode ser implementado em todas as plataformas de destino. Tome cuidado para restrições de interface do usuário específico de plataforma Observação. Por exemplo, um `TabBarController` no iOS pode exibir mais de cinco botões, enquanto o equivalente do Windows Phone pode exibir até quatro.
Desenhe o tela-fluxo usando a ferramenta de sua escolha (papel funciona).

 [![](case-study-tasky-images/taskydesign.png "Desenhar o tela-fluxo usando a ferramenta de seus trabalhos de papel escolha")](case-study-tasky-images/taskydesign.png#lightbox)

 <a name="Data_Model" />

### <a name="data-model"></a>Modelo de dados

Saber quais dados precisam ser armazenados ajudam a determinar qual mecanismo de persistência a ser usado. Consulte [acesso a dados entre plataformas](~/cross-platform/app-fundamentals/index.md) para obter informações sobre os mecanismos de armazenamento disponível e ajudar a decidir entre eles. Para este projeto, usaremos SQLite.NET.

Tasky precisa armazenar três propriedades para cada TaskItem:

 -  **Nome** – cadeia de caracteres
 -  **Notas de** – cadeia de caracteres
 -  **Feito** – booliano

 <a name="Core_Functionality" />

### <a name="core-functionality"></a>Funcionalidade principal

Considere a API que a interface do usuário precisa consumir para atender aos requisitos. Uma lista de tarefas requer as seguintes funções:

 -   **Lista todas as tarefas** – para exibir a lista de tela principal de todas as tarefas disponíveis
 -  **Obter uma tarefa** – quando o usuário toca uma linha de tarefa
 -  **Salvar uma tarefa** – quando uma tarefa é editada
 -  **Excluir uma tarefa** – quando uma tarefa é excluída
 -  **Criar tarefa vazia** – quando uma nova tarefa é criada.

Para obter a reutilização de código, essa API deve ser implementada uma vez no *biblioteca de classes portátil*.

 <a name="Implementation" />

### <a name="implementation"></a>Implementação

Depois que o design do aplicativo foi acordado, considere como ele pode ser implementado como um aplicativo de plataforma cruzada. Isso tornará a arquitetura do aplicativo. As diretrizes a seguir o [criando aplicativos de plataforma cruzada](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) documento, o código do aplicativo deve ser interrompido para baixo nas seguintes partes:

 -   **Código comum** – um projeto comum que contém o código novamente utilizável para armazenar os dados da tarefa; expor uma classe de modelo e uma API para gerenciar o salvamento e carregamento de dados.
 -   **Código específico da plataforma** – projetos específicos de plataforma que implementa uma interface de usuário nativo para cada sistema operacional, usando o código comum como 'back-end'.

 [![](case-study-tasky-images/taskypro-architecture.png "Específico da plataforma projetos implementam uma interface de usuário nativo para cada sistema operacional, usando o código comum como back-end")](case-study-tasky-images/taskypro-architecture.png#lightbox)

Essas duas partes são descritos nas seções a seguir.

 <a name="Common_(PCL)_Code" />

## <a name="common-pcl-code"></a>Código comum de PCL)

Tasky portátil usa a estratégia de biblioteca de classes portátil para compartilhamento de código comum. Consulte o [opções de compartilhamento de código](~/cross-platform/app-fundamentals/code-sharing.md) documento para obter uma descrição das opções de compartilhamento de código.

Todo o código comum, incluindo a camada de acesso a dados, o código de banco de dados e contratos, é colocado no projeto da biblioteca.

O projeto PCL completo está ilustrado abaixo. Todo o código da biblioteca portátil é compatível com cada plataforma de destino. Quando implantado, cada aplicativo nativo fará referência a essa biblioteca.

![](case-study-tasky-images/portable-project.png "Quando implantado, cada aplicativo nativo fará referência a essa biblioteca")

O diagrama de classe a seguir mostra as classes agrupadas por camada. O `SQLiteConnection` classe é o código clichê do pacote Sqlite-NET. O restante das classes são o código personalizado para Tasky. O `TaskItemManager` e `TaskItem` classes representam a API que é exposta para os aplicativos de plataforma específica.

 [![](case-study-tasky-images/classdiagram-core.png "As classes TaskItemManager e TaskItem representam a API que é exposta aos aplicativos específicos de plataforma")](case-study-tasky-images/classdiagram-core.png#lightbox)

Usando namespaces para separar as camadas ajuda a gerenciar referências entre cada camada. Os projetos específicos de plataforma só será necessário incluir um `using` instrução para a camada de negócios. A camada de acesso a dados e a camada de dados devem ser encapsuladas pela API do que é exposta pelo `TaskItemManager` na camada de negócios.

 <a name="References" />

### <a name="references"></a>Referências

Bibliotecas de classes portáteis precisam ser usado em várias plataformas, cada um com vários níveis de suporte para recursos de plataforma e estrutura. Por isso, há limitações no qual pacotes e bibliotecas do framework podem ser usadas. Por exemplo, xamarin não oferece suporte a c# `dynamic` palavra-chave, para uma biblioteca de classes portátil não é possível usar qualquer pacote que depende de código dinâmico, mesmo que esse código funcione no Android. O Visual Studio para Mac impedirá a adicionando pacotes incompatíveis e referências, mas você desejará manter limitações em mente para evitar surpresas mais tarde.

Observação: Você verá que seus projetos de referenciam a bibliotecas do framework que você ainda não usado. Essas referências são incluídas como parte dos modelos de projeto Xamarin. Quando os aplicativos são compilados, o processo de vinculação removerá código sem referência, portanto, embora `System.Xml` foi referenciado, ele não será incluído no aplicativo final porque não estamos usando as funções de Xml.

 <a name="Data_Layer_(DL)" />

### <a name="data-layer-dl"></a>Camada de dados (DL)

A camada de dados contém o código que faz o armazenamento físico de dados – para um banco de dados, arquivos simples ou outro mecanismo. A camada de dados Tasky consiste em duas partes: a biblioteca de rede do SQLite e o código personalizado adicionado conectá-lo.

Tasky depende do pacote do nuget net Sqlite (publicado pelo Frank Kreuger) para inserir código SQLite NET que fornece uma interface de banco de dados de mapeamento relacional de objeto (ORM). O `TaskItemDatabase` classe herda de `SQLiteConnection` e adiciona o necessário criar, ler, atualizar, métodos de exclusão (CRUD) para ler e gravar dados SQLite. É uma implementação simples clichê de métodos CRUD genéricos que podem ser reutilizadas em outros projetos.

O `TaskItemDatabase` é um singleton, garantindo que todos os acessos ocorre com a mesma instância. Um bloqueio é usado para evitar o acesso simultâneo de vários threads.

 <a name="SQLite_on_WIndows_Phone" />

#### <a name="sqlite-on-windows-phone"></a>SQLite no Windows Phone

Ao iOS e Android que são fornecidos com SQLite como parte do sistema operacional, o Windows Phone não inclui um mecanismo de banco de dados compatível. Para compartilhar código em todas as plataformas de três uma versão do Windows phone nativo do SQLite é necessária. Consulte [trabalhando com um banco de dados Local](~/xamarin-forms/app-fundamentals/databases.md) para obter mais informações sobre como configurar seu projeto do Windows Phone para Sqlite.

 <a name="Using_an_Interface_to_Generalize_Data_Access" />

#### <a name="using-an-interface-to-generalize-data-access"></a>Usando uma Interface para generalizar o acesso a dados

A camada de dados leva uma dependência em `BL.Contracts.IBusinessIdentity` para que ela pode implementar métodos de acesso de dados abstrato que exigem uma chave primária. Qualquer classe de camada de negócios que implementa a interface, em seguida, pode ser persistente na camada de dados.

A interface especifica apenas uma propriedade de inteiro para atuar como a chave primária:

```csharp
public interface IBusinessEntity {
    int ID { get; set; }
}
```

A classe base implementa a interface e adiciona os atributos de NET SQLite para marcá-la como uma chave primária de incremento automático. Qualquer classe na camada de negócios que implementa essa classe base, em seguida, pode ser persistente na camada de dados:

```csharp
public abstract class BusinessEntityBase : IBusinessEntity {
    public BusinessEntityBase () {}
    [PrimaryKey, AutoIncrement]
    public int ID { get; set; }
}
```

Este é um exemplo de como os métodos genéricos na camada de dados que usam a interface `GetItem<T>` método:

```csharp
public T GetItem<T> (int id) where T : BL.Contracts.IBusinessEntity, new ()
{
    lock (locker) {
        return Table<T>().FirstOrDefault(x => x.ID == id);
    }
}
```

 <a name="Locking_to_prevent_Concurrent_Access" />

#### <a name="locking-to-prevent-concurrent-access"></a>O bloqueio para impedir o acesso simultâneo

Um [bloqueio](http://msdn.microsoft.com/library/c5kehkcz(v=vs.100).aspx) é implementado no `TaskItemDatabase` classe para evitar o acesso simultâneo ao banco de dados. Isso é para garantir o acesso simultâneo de diversos threads é serializado (caso contrário, um componente de interface do usuário pode tentar ler o banco de dados ao mesmo tempo em que um thread em segundo plano é atualizá-lo). Um exemplo de como o bloqueio é implementado é mostrado aqui:

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

Mais do que o código da camada de dados podem ser reutilizado em outros projetos. O código específico do aplicativo somente na camada é o `CreateTable<TaskItem>` chamar o `TaskItemDatabase` construtor.

 <a name="Data_Access_Layer_(DAL)" />

### <a name="data-access-layer-dal"></a>Camada de acesso a dados (DAL)

O `TaskItemRepository` classe encapsula o mecanismo de armazenamento de dados com uma API fortemente tipada que permite `TaskItem` objetos a serem criados, excluídos, recuperados e atualizados.

 <a name="Using_Conditional_Compilation" />

#### <a name="using-conditional-compilation"></a>Usando a compilação condicional

A classe usa a compilação condicional para definir o local do arquivo - este é um exemplo de implementação de divergência de plataforma. A propriedade que retorna o caminho é compilado em código diferente em cada plataforma. As diretivas de compilador específica da plataforma e código são mostradas aqui:

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

Dependendo da plataforma, a saída será "<app
path>/Library/TaskDB.db3" para iOS, "<app
path>/Documents/TaskDB.db3" para Android ou apenas "TaskDB.db3" para o Windows Phone.

### <a name="business-layer-bl"></a>Camada de negócios (BL)

A camada de negócios implementa as classes de modelo e uma fachada para gerenciá-los.
Tasky o modelo é o `TaskItem` classe e `TaskItemManager` implementa o padrão de fachada para fornecer uma API para gerenciar `TaskItems`.

 <a name="Façade" />

#### <a name="faade"></a>Fachada

 `TaskItemManager` encapsula o `DAL.TaskItemRepository` para fornecer Get, salvar e excluir os métodos que serão referenciados pelo aplicativo e camadas de interface do usuário.

Regras de negócios e a lógica seriam colocados aqui se necessária – por exemplo quaisquer regras de validação que devem ser atendidas antes de um objeto for salvo.

 <a name="API_for_Platform-Specific_Code" />

### <a name="api-for-platform-specific-code"></a>API de código específico da plataforma

Depois que o código comum ter sido gravado, a interface do usuário deve ser criada para coletar e exibir os dados apresentados por ele. O `TaskItemManager` classe implementa o padrão de fachada para fornecer uma API simples para o código do aplicativo para acessar.

O código escrito em cada projeto específico da plataforma geralmente será estreitamente acoplado ao SDK nativo do dispositivo e acessar apenas o código comum usando a API definida pelo `TaskItemManager`. Isso inclui os métodos e negócios classes ele expõe, tais como `TaskItem`.

Imagens não são compartilhadas entre plataformas mas adicionadas independentemente para cada projeto. Isso é importante porque cada plataforma lida com imagens diferente, usando nomes de arquivo diferentes, diretórios e resoluções.

As seções restantes discutem os detalhes de implementação específica de plataforma da interface do usuário Tasky.

 <a name="iOS_App" />

## <a name="ios-app"></a>Aplicativo iOS

Há apenas uma série de classes necessárias para implementar o aplicativo Tasky usando o projeto PCL comum para armazenar e recuperar dados do iOS. O projeto de xamarin iOS completa é mostrado abaixo:

 ![](case-study-tasky-images/taskyios-solution.png "projeto do iOS é mostrado aqui")

As classes são mostradas neste diagrama, agrupados em camadas.

 [![](case-study-tasky-images/classdiagram-android.png "As classes são mostradas neste diagrama, agrupados em camadas")](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References" />

### <a name="references"></a>Referências

O aplicativo do iOS faz referência a bibliotecas SDK de plataforma específica – por exemplo. Xamarin e MonoTouch.Dialog-1.

Ele também deve fazer referência a `TaskyPortableLibrary` projeto PCL.
A lista de referências é mostrada aqui:

 ![](case-study-tasky-images/taskyios-references.png "A lista de referências é mostrada aqui")

A camada de aplicativo e a camada de Interface do usuário são implementados neste projeto usando essas referências.

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>Camada de aplicativo (AL)

A camada de aplicativo contém classes específicas da plataforma necessárias para 'bind' os objetos expostos pelo PCL na interface do usuário. O aplicativo específico do iOS tem duas classes para ajudar a exibir tarefas:

 -   **EditingSource** – essa classe é usada para associar a lista de tarefas para a interface do usuário. Porque `MonoTouch.Dialog` foi usado para a lista de tarefas, é preciso implementar este auxiliar para habilitar a funcionalidade de passe o dedo para exclusão no `UITableView` . Passe o dedo para exclusão é comum em iOS, mas não Android ou Windows Phone, portanto o projeto específico do iOS é o único que implementa.
 -   **TaskDialog** – essa classe é usada para associar uma única tarefa na interface do usuário. Ele usa o `MonoTouch.Dialog` API de reflexão para 'wrap' o `TaskItem` objeto com uma classe que contém os atributos corretos para permitir que a tela de entrada a ser formatado corretamente.

O `TaskDialog` classe usa `MonoTouch.Dialog` atributos para criar uma tela com base nas propriedades de uma classe. A classe tem esta aparência:

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

Observe o `OnTap` atributos requerem um nome de método – esses métodos devem existir na classe onde o `MonoTouch.Dialog.BindingContext` é criado (nesse caso, a `HomeScreen` classe discutido na próxima seção).

 <a name="User_Interface_Layer_(UI)" />

### <a name="user-interface-layer-ui"></a>Camada de Interface do usuário (IU)

A camada de Interface do usuário consiste das seguintes classes:

1.   **AppDelegate** – contém as chamadas para a API de aparência para definir o estilo de fontes e cores usadas no aplicativo. Tasky é um aplicativo simples para que não haja nenhum outras tarefas de inicialização em execução no `FinishedLaunching` .
2.   **Telas** – subclasses de `UIViewController` que definem cada tela e seu comportamento. Telas de unir a interface do usuário com classes de camada de aplicativo e a API comuns ( `TaskItemManager` ). Neste exemplo, as telas são criadas em código, mas poderia foram criadas usando o construtor de Interface do Xcode ou o designer de storyboard.
3.   **Imagens** – elementos visuais são uma parte importante de cada aplicativo. Tasky tem a tela e o ícone de imagens de abertura, que devem ser fornecidas em regular e resolução de Retina para iOS.

 <a name="Home_Screen" />

#### <a name="home-screen"></a>Tela inicial

A tela de início é um `MonoTouch.Dialog` tela que exibe uma lista de tarefas do banco de dados SQLite. Ele herda de `DialogViewController` e implementa código para definir o `Root` contém uma coleção de `TaskItem` objetos para exibição.

 [![](case-study-tasky-images/ios-taskylist.png "Ele herda DialogViewController e implementa código para definir a raiz contém uma coleção de objetos TaskItem para exibição")](case-study-tasky-images/ios-taskylist.png#lightbox)

Os dois principais métodos relacionados a exibir e interagir com a lista de tarefas são:

1.   **PopulateTable** – usa a camada de negócios `TaskManager.GetTasks` método para recuperar uma coleção de `TaskItem` objetos a serem exibidos.
2.   **Selecionado** – quando uma linha é alterada, exibe a tarefa em uma nova tela.

 <a name="Task_Details_Screen" />

#### <a name="task-details-screen"></a>Tela de detalhes da tarefa

Detalhes da tarefa é uma tela de entrada que permite que as tarefas a ser editada ou excluída.

Usa tasky `MonoTouch.Dialog`da API de reflexão para exibir a tela, portanto, há nenhum `UIViewController` implementação. Em vez disso, o `HomeScreen` classe instancia e exibe um `DialogViewController` usando o `TaskDialog` classe da camada de aplicativo.

Esta captura de tela mostra uma tela vazia que demonstra o `Entry` atributo definindo o texto de marca d'água no **nome** e **notas** campos:

 [![](case-study-tasky-images/ios-taskydetail.png "Esta captura de tela mostra uma tela vazia que demonstra o atributo de entrada, definindo o texto de marca d'água nos campos nome e notas")](case-study-tasky-images/ios-taskydetail.png#lightbox)

A funcionalidade do **detalhes da tarefa** tela (por exemplo, salvar ou excluir uma tarefa) deve ser implementada no `HomeScreen` classe, porque esse é o local onde o `MonoTouch.Dialog.BindingContext` é criado. O seguinte `HomeScreen` métodos oferecem suporte a tela de detalhes da tarefa:

1.   **ShowTaskDetails** – cria um `MonoTouch.Dialog.BindingContext` para renderizar uma tela. Ele cria a tela de entrada usando a reflexão para recuperar nomes de propriedade e tipos do `TaskDialog` classe. Informações adicionais, como o texto de marca d'água para as caixas de entrada, são implementadas com os atributos nas propriedades.
2.   **SaveTask** – esse método é referenciado no `TaskDialog` classe por meio de um `OnTap` atributo. Ele é chamado quando **salvar** é pressionado e usa um `MonoTouch.Dialog.BindingContext` para recuperar os dados de entrada do usuário antes de salvar as alterações usando `TaskItemManager` .
3.   **DeleteTask** – esse método é referenciado no `TaskDialog` classe por meio de um `OnTap` atributo. Ele usa `TaskItemManager` para excluir os dados usando a chave primária (propriedade de ID).

 <a name="Android_App" />

## <a name="android-app"></a>Aplicativo do Android

O projeto completo do xamarin é mostrado abaixo:

 ![](case-study-tasky-images/taskyandroid-solution.png "Projeto Android mostrado aqui")

Diagrama de classe, com classes agrupadas por camada:

 [![](case-study-tasky-images/classdiagram-android.png "Diagrama de classe, com classes agrupadas por camada")](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References" />

### <a name="references"></a>Referências

O projeto de aplicativo do Android deve fazer referência a assembly xamarin específico da plataforma para classes de acesso do SDK do Android.

Ela também deve referenciar o projeto PCL (por exemplo. TaskyPortableLibrary) para acessar o código comum de camada de dados e de negócios.

 ![](case-study-tasky-images/taskyandroid-references.png "TaskyPortableLibrary para acessar o código comum de camada de dados e de negócios")

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>Camada de aplicativo (AL)

Semelhante para a versão do iOS que observamos anteriormente, a camada de aplicativo na versão do Android contém classes específicas da plataforma necessárias para 'bind' os objetos expostos por núcleo para a interface do usuário.

 **TaskListAdapter** – para exibir uma lista<T> de objetos, é preciso implementar um adaptador para exibir objetos personalizados em um `ListView`. O adaptador controla o layout é usado para cada item na lista – nesse caso, o código usa um layout interno Android `SimpleListItemChecked`.

 <a name="User_Interface_(UI)" />

### <a name="user-interface-ui"></a>Interface do usuário (IU)

Camada de Interface do usuário do aplicativo do Android é uma combinação de código e a marcação XML.

 -   **Recursos/Layout** – layouts de tela e a linha da célula implementado como arquivos AXML de design. O AXML pode ser escrito manualmente ou em apresentados visualmente usando o Designer de interface do usuário do Xamarin para Android.
 -   **Recursos/Drawable** – imagens (ícones) e o botão personalizado.
 -   **Telas** – subclasses de atividade que definem cada tela e seu comportamento. Une a interface do usuário com classes de camada de aplicativo e a API comuns (`TaskItemManager`).

 <a name="Home_Screen" />

#### <a name="home-screen"></a>Tela inicial

A tela inicial consiste em uma subclasse de atividade `HomeScreen` e `HomeScreen.axml` arquivo que define o layout (a posição da lista de tarefas e de botão). A tela tem esta aparência:

 [![](case-study-tasky-images/android-taskylist.png "A tela tem esta aparência")](case-study-tasky-images/android-taskylist.png#lightbox)

O código a tela inicial define os manipuladores para clicar no botão e clicar nos itens na lista, bem como popular a lista no `OnResume` método (de modo que ela reflete as alterações feitas na tela de detalhes da tarefa). Os dados são carregados usando a camada de negócios `TaskItemManager` e `TaskListAdapter` da camada de aplicativo.

 <a name="Task_Details_Screen" />

#### <a name="task-details-screen"></a>Tela de detalhes da tarefa

A tela de detalhes da tarefa também consiste em um `Activity` subclasse e um arquivo de layout AXML. O layout determina o local dos controles de entrada e a classe do c# define o comportamento para carregar e salvar `TaskItem` objetos.

 [![](case-study-tasky-images/android-taskydetail.png "A classe define o comportamento para carregar e salvar objetos TaskItem")](case-study-tasky-images/android-taskydetail.png#lightbox)

Todas as referências à biblioteca PCL são feitas por meio de `TaskItemManager` classe.

 <a name="Windows_Phone_App" />

## <a name="windows-phone-app"></a>Aplicativo do Windows Phone
O projeto completo do Windows Phone:

 ![](case-study-tasky-images/taskywp7-solution.png "Aplicativo do Windows Phone o projeto completo do Windows Phone")

O diagrama a seguir apresenta as classes agrupadas em camadas:

 [![](case-study-tasky-images/classdiagram-wp7.png "Este diagrama apresenta as classes agrupadas em camadas")](case-study-tasky-images/classdiagram-wp7.png#lightbox)

 <a name="References" />

### <a name="references"></a>Referências

O projeto específico da plataforma deve fazer referência as bibliotecas necessárias do específica da plataforma (como `Microsoft.Phone` e `System.Windows`) para criar um aplicativo do Windows Phone válido.

Ela também deve referenciar o projeto PCL (por exemplo. `TaskyPortableLibrary`) para utilizar o `TaskItem` classe e o banco de dados.

 ![](case-study-tasky-images/taskywp7-references.png "TaskyPortableLibrary para utilizar a classe TaskItem e o banco de dados")

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>Camada de aplicativo (AL)

Novamente, assim como acontece com as versões Android e iOS, a camada de aplicativo consiste nos elementos não visuais que ajudam a associar os dados para a interface do usuário.

 <a name="ViewModels" />

#### <a name="viewmodels"></a>ViewModels

ViewModels encapsular dados a partir de PCL ( `TaskItemManager`) e apresentados de forma que pode ser consumida pela associação de dados/o XAML do Silverlight. Este é um exemplo de comportamento específico de plataforma (como discutido no documento de aplicativos de plataforma cruzada).

 <a name="User_Interface_(UI)" />

### <a name="user-interface-ui"></a>Interface do usuário (IU)

XAML tem um único recurso de associação de dados que pode ser declarado na marcação e reduzir a quantidade de código necessário para exibir objetos:

1.   **Páginas** – arquivos XAML e o code-behind definem a interface do usuário e ViewModels e o projeto PCL para exibir e coletar dados de referência.
2.   **Imagens** – imagens de tela, o plano de fundo e o ícone de abertura são uma parte importante da interface do usuário.

 <a name="MainPage" />

#### <a name="mainpage"></a>MainPage

Os usos de classe MainPage o `TaskListViewModel` para exibir dados de uso de recursos de associação de dados do XAML. A página `DataContext` é definido como o modelo de exibição, que é populado assincronamente. O `{Binding}` sintaxe no XAML determina como os dados são exibidos.

 <a name="TaskDetailsPage" />

#### <a name="taskdetailspage"></a>TaskDetailsPage

Cada tarefa é exibida pela associação a `TaskViewModel` a XAML definido no TaskDetailsPage.xaml. Os dados de tarefa são recuperados por meio de `TaskItemManager` na camada de negócios.

 <a name="Results" />

## <a name="results"></a>Resultados

Os aplicativos resultantes se parecer com isso em cada plataforma:

 <a name="iOS" />

#### <a name="ios"></a>iOS

O aplicativo usa o design de interface de usuário padrão do iOS, como o botão 'Adicionar' sendo posicionado na barra de navegação e o uso interno **mais (+)** ícone. Ele também usa o padrão `UINavigationController` botão 'Voltar' comportamento e dá suporte a 'passe o dedo para excluir' na tabela.

 [![](case-study-tasky-images/ios-taskylist.png "Ele também usa o comportamento do botão Voltar de UINavigationController padrão e suporta passe o dedo para exclusão na tabela") ](case-study-tasky-images/ios-taskylist.png#lightbox) [ ![ ] (case-study-tasky-images/ios-taskylist.png "também usa o padrão UINavigationController fazer o comportamento do botão e dá suporte a passe o dedo para exclusão na tabela")](case-study-tasky-images/ios-taskylist.png#lightbox)

 <a name="Android" />

#### <a name="android"></a>Android

O aplicativo do Android usa controles internos, incluindo o layout interno para linhas que exigem uma 'Escala' exibida. O comportamento de voltar de hardware/sistema há suporte para além de um botão Voltar na tela.

 [![](case-study-tasky-images/android-taskylist.png "O comportamento de voltar de hardware/sistema tem suporte além de um botão Voltar na tela")](case-study-tasky-images/android-taskylist.png#lightbox)[![](case-study-tasky-images/android-taskylist.png "o comportamento de voltar de hardware/sistema há suporte além uma tela botão Voltar")](case-study-tasky-images/android-taskylist.png#lightbox)

 <a name="Windows_Phone" />

#### <a name="windows-phone"></a>Windows Phone

O aplicativo do Windows Phone usa o layout padrão, preenchendo a barra de aplicativos na parte inferior da tela, em vez de uma barra de navegação na parte superior.

 [![](case-study-tasky-images/wp-taskylist.png "O aplicativo do Windows Phone usa o layout padrão, preenchendo a barra de aplicativos na parte inferior da tela, em vez de uma barra de navegação na parte superior") ](case-study-tasky-images/wp-taskylist.png#lightbox) [ ![ ] (case-study-tasky-images/wp-taskylist.png "do Windows Phone o aplicativo usa o padrão layout, preenchendo a barra de aplicativos na parte inferior da tela, em vez de uma barra de navegação na parte superior")](case-study-tasky-images/wp-taskylist.png#lightbox)

 <a name="Summary" />

## <a name="summary"></a>Resumo

Este documento fornece uma explicação detalhada de como os princípios de design de aplicativo em camadas foram aplicados a um aplicativo simple para facilitar a reutilização de código para três plataformas móveis: iOS, Android e Windows Phone.

Descrito o processo usado para criar as camadas do aplicativo e discutido qual código &amp; funcionalidade foi implementada em cada camada.

O código pode ser baixado do [github](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable).

## <a name="related-links"></a>Links relacionados

- [Criando aplicativos de plataforma cruzada (documento principal)](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [Aplicativo de exemplo portátil tasky (github)](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)
