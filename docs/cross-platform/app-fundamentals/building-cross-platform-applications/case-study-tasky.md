---
title: 'Estudo de caso do aplicativo de plataforma cruzada: Tasky'
description: Este documento descreve como o aplicativo de exemplo Tasky Portable foi projetado e criado como um aplicativo móvel de plataforma cruzada. Ele aborda o aplicativo requisitos, interface, modelo de dados, funcionalidade principal, implementação e muito mais.
ms.prod: xamarin
ms.assetid: B581B2D0-9890-C383-C654-0B0E12DAD5A6
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 23deddae61452d532a87c51cc1ec3bc53eb91c9f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61278879"
---
# <a name="cross-platform-app-case-study-tasky"></a>Estudo de caso do aplicativo de plataforma cruzada: Tasky

*Tasky* *portátil* é um aplicativo de lista de tarefas pendentes simples. Este documento discute como ele foi projetado e criado, seguindo as diretrizes do [criando aplicativos de plataforma cruzada](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) documento. A discussão abrange as seguintes áreas:

<a name="Design_Process" />

## <a name="design-process"></a>Processo de design

É recomendável criar um de roteiro para o que você quer atingir antes de iniciar a codificação. Isso é especialmente verdadeiro para o desenvolvimento de plataforma cruzada, em que você está criando a funcionalidade que será exposta de várias maneiras. Começando com uma ideia clara de como o que você está compilando economiza tempo e esforço posteriormente no ciclo de desenvolvimento.

 <a name="Requirements" />

### <a name="requirements"></a>Requisitos

A primeira etapa na criação de um aplicativo é identificar os recursos desejados. Eles podem ser metas de alto nível ou são detalhadas casos de uso. Tasky tem requisitos funcionais simples:

 -  Exibir uma lista de tarefas
 -  Adicionar, editar e excluir tarefas
 -  Definir status da tarefa para 'concluído'

Você deve considerar o uso de recursos específicos à plataforma.  Pode Tasky tirar proveito de delimitação de iOS ou blocos dinâmicos do Windows Phone? Mesmo se você não usar recursos específicos da plataforma na primeira versão, você deve planejar com antecedência garantir que seus negócios e as camadas de dados podem acomodá-los.

 <a name="User_Interface_Design" />

### <a name="user-interface-design"></a>Design da interface do usuário

Comece com um design de alto nível que pode ser implementado em todas as plataformas de destino. Tome cuidado para restrições de interface do usuário do Observação específicas de plataforma. Por exemplo, um `TabBarController` no iOS podem exibir mais de cinco botões, enquanto o equivalente do Windows Phone pode exibir até quatro.
Desenhe o fluxo de tela usando a ferramenta de sua escolha (papel funciona).

 [![](case-study-tasky-images/taskydesign.png "Desenhar o fluxo de tela usando a ferramenta de funciona de papel sua escolha")](case-study-tasky-images/taskydesign.png#lightbox)

 <a name="Data_Model" />

### <a name="data-model"></a>Modelo de dados

Saber quais dados precisam ser armazenados ajudará a determinar qual mecanismo de persistência a ser usado. Ver [acesso a dados multiplataforma](~/cross-platform/app-fundamentals/index.md) para obter informações sobre os mecanismos de armazenamento disponível e a Ajuda para decidir entre eles. Para este projeto, estaremos usando SQLite.NET.

Tasky precisa armazenar três propriedades para cada TaskItem:

 -  **Nome** – cadeia de caracteres
 -  **Notas de** – cadeia de caracteres
 -  **Feito** -booliano

 <a name="Core_Functionality" />

### <a name="core-functionality"></a>Funcionalidade principal

Considere a API que a interface do usuário precisam consumir para atender aos requisitos. Uma lista de tarefas requer as seguintes funções:

 -   **Listar todas as tarefas** – para exibir a lista de tela principal de todas as tarefas disponíveis
 -  **Obtenha uma tarefa** – quando uma linha de tarefa é atingida
 -  **Salvar uma tarefa** – quando uma tarefa é editada
 -  **Excluir uma tarefa** – quando uma tarefa é excluída
 -  **Criar tarefa vazia** – quando uma nova tarefa é criada.

Para atingir a reutilização de código, essa API deve ser implementada uma vez na *biblioteca de classes portátil*.

 <a name="Implementation" />

### <a name="implementation"></a>Implementação

Depois que o design do aplicativo foi acordado, considere como ele pode ser implementado como um aplicativo de plataforma cruzada. Isso se tornará a arquitetura do aplicativo. As diretrizes a seguir a [criando aplicativos de plataforma cruzada](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) documento, o código do aplicativo deve ser interrompido para baixo nas seguintes partes:

 -   **Código comum** – um projeto comum que contém o código novamente utilizável para armazenar os dados de tarefa; expor uma classe de modelo e uma API para gerenciar o salvamento e carregamento de dados.
 -   **Código específico da plataforma** – projetos específicos da plataforma que implementam uma interface do usuário nativa para cada sistema operacional, utilizando o código comum como 'back-end'.

 [![](case-study-tasky-images/taskypro-architecture.png "Projetos específicos da plataforma implementam uma interface do usuário nativa para cada sistema operacional, utilizando o código comum como o back-end")](case-study-tasky-images/taskypro-architecture.png#lightbox)

Essas duas partes são descritos nas seções a seguir.

 <a name="Common_(PCL)_Code" />

## <a name="common-pcl-code"></a>Código comum de (PCL)

Tasky Portable usa a estratégia de biblioteca de classes portátil para compartilhar código comum. Consulte a [opções de compartilhamento de código](~/cross-platform/app-fundamentals/code-sharing.md) documento para obter uma descrição das opções de compartilhamento de código.

Todo o código comum, incluindo a camada de acesso a dados, o código de banco de dados e contratos, é colocado no projeto da biblioteca.

O projeto PCL completo é ilustrado abaixo. Todo o código na biblioteca portátil é compatível com cada plataforma de destino. Quando implantado, cada aplicativo nativo fará referência a essa biblioteca.

![](case-study-tasky-images/portable-project.png "Quando implantado, cada aplicativo nativo fará referência a essa biblioteca")

O diagrama de classe a seguir mostra as classes agrupadas por camada. O `SQLiteConnection` classe é um código clichê do pacote Sqlite-NET. O restante das classes são o código personalizado para Tasky. O `TaskItemManager` e `TaskItem` classes representam a API que é exposta aos aplicativos específicos da plataforma.

 [![](case-study-tasky-images/classdiagram-core.png "As classes TaskItemManager e TaskItem representam a API que é exposta aos aplicativos específicos da plataforma")](case-study-tasky-images/classdiagram-core.png#lightbox)

Usando namespaces para separar as camadas ajuda a gerenciar referências entre cada camada. Os projetos específicos da plataforma só será necessário incluir um `using` instrução para a camada de negócios. A camada de acesso a dados e a camada de dados devem ser encapsulada pela API que é exposta pelo `TaskItemManager` na camada de negócios.

 <a name="References" />

### <a name="references"></a>Referências

Bibliotecas de classes portáteis precisam ser usado em várias plataformas, cada um com níveis variados de suporte para recursos de plataforma e estrutura. Por causa disso, há limitações no qual pacotes e bibliotecas do framework podem ser usadas. Por exemplo, xamarin. IOS não dá suporte a c# `dynamic` palavra-chave, portanto, uma biblioteca de classes portátil não é possível usar qualquer pacote que depende de código dinâmico, mesmo que esse código funciona em Android. O Visual Studio para Mac impedirá a adição de pacotes incompatíveis e referências, mas você vai querer ter limitações em mente a evitar surpresas posteriormente.

Observação: Você verá que seus projetos fazem referência a bibliotecas do framework que você nunca usou. Essas referências são incluídas como parte dos modelos de projeto Xamarin. Quando os aplicativos são compilados, o processo de vinculação removerá código sem referência, portanto, embora `System.Xml` foi referenciado, ele não será incluído no aplicativo final porque não estamos usando quaisquer funções de Xml.

 <a name="Data_Layer_(DL)" />

### <a name="data-layer-dl"></a>Camada de dados (DL)

A camada de dados contém o código que faz o armazenamento físico de dados – para um banco de dados, arquivos simples ou outro mecanismo. A camada de dados Tasky consiste em duas partes: o SQLite-NET-library e o código personalizado adicionado conectá-lo.

Tasky se baseia no pacote nuget Sqlite-net (publicado por Frank Kreuger) para inserir código SQLite-NET que fornece uma interface de banco de dados de mapeamento relacional de objeto (ORM). O `TaskItemDatabase` classe herda de `SQLiteConnection` e adiciona o necessária Create, Read, Update, os métodos de exclusão (CRUD) para ler e gravar dados em SQLite. É uma implementação de texto clichê simples de métodos CRUD genéricos que podem ser reutilizadas em outros projetos.

O `TaskItemDatabase` é um singleton, garantindo que todo o acesso ocorre em relação a mesma instância. Um bloqueio é usado para evitar o acesso simultâneo de vários threads.

 <a name="SQLite_on_WIndows_Phone" />

#### <a name="sqlite-on-windows-phone"></a>SQLite no Windows Phone

Ao mesmo tempo, iOS e Android que ambos são fornecidas com o SQLite como parte do sistema operacional, Windows Phone não inclui um mecanismo de banco de dados compatível. Para compartilhar código entre todas as três plataformas é necessária uma versão de telefone nativo do Windows do SQLite. Ver [trabalhando com um banco de dados Local](~/xamarin-forms/app-fundamentals/databases.md) para obter mais informações sobre como configurar seu projeto do Windows Phone para Sqlite.

 <a name="Using_an_Interface_to_Generalize_Data_Access" />

#### <a name="using-an-interface-to-generalize-data-access"></a>Usando uma Interface para generalizar o acesso a dados

A camada de dados usa uma dependência `BL.Contracts.IBusinessIdentity` para que ele possa implementar métodos de acesso de dados abstrato que exigem uma chave primária. Qualquer classe de camada de negócios que implementa a interface, em seguida, pode ser persistido na camada de dados.

A interface especifica apenas uma propriedade de inteiro para atuar como a chave primária:

```csharp
public interface IBusinessEntity {
    int ID { get; set; }
}
```

A classe base implementa a interface e adiciona os atributos do SQLite-NET para marcá-la como uma chave primária de incremento automático. Qualquer classe na camada de negócios que implementa essa classe base, em seguida, pode ser persistente na camada de dados:

```csharp
public abstract class BusinessEntityBase : IBusinessEntity {
    public BusinessEntityBase () {}
    [PrimaryKey, AutoIncrement]
    public int ID { get; set; }
}
```

Um exemplo de como os métodos genéricos na camada de dados que usam a interface é que isso `GetItem<T>` método:

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

Um [bloqueio](https://msdn.microsoft.com/library/c5kehkcz(v=vs.100).aspx) é implementada dentro do `TaskItemDatabase` classe para evitar o acesso simultâneo ao banco de dados. Isso é para garantir o acesso simultâneo de diversos threads é serializado (caso contrário, um componente de interface do usuário pode tentar ler o banco de dados ao mesmo tempo em que ele está sendo atualizado por um thread em segundo plano). Um exemplo de como o bloqueio é implementado é mostrado aqui:

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

A maioria do código de camada de dados poderia ser reutilizada em outros projetos. O código específico do aplicativo apenas na camada de é o `CreateTable<TaskItem>` chamar no `TaskItemDatabase` construtor.

 <a name="Data_Access_Layer_(DAL)" />

### <a name="data-access-layer-dal"></a>Camada de acesso a dados (DAL)

O `TaskItemRepository` classe encapsula o mecanismo de armazenamento de dados com uma API fortemente tipada que permite `TaskItem` objetos a serem criados, excluídos, recuperados e atualizados.

 <a name="Using_Conditional_Compilation" />

#### <a name="using-conditional-compilation"></a>Usando a compilação condicional

A classe usa a compilação condicional para definir o local do arquivo - este é um exemplo de implementação de divergência de plataforma. A propriedade que retorna o caminho é compilado em um código diferente em cada plataforma. As diretivas de compilador específicos da plataforma e código são mostradas aqui:

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
path>/Documents/TaskDB.db3" para Android ou apenas "TaskDB.db3" para Windows Phone.

### <a name="business-layer-bl"></a>Camada de negócios (BL)

A camada de negócios implementa as classes de modelo e uma fachada para gerenciá-los.
Em Tasky o modelo é o `TaskItem` classe e `TaskItemManager` implementa o padrão de fachada para fornecer uma API para gerenciar `TaskItems`.

 <a name="Façade" />

#### <a name="faade"></a>Façade

 `TaskItemManager` encapsula o `DAL.TaskItemRepository` para fornecer Get, salvar e excluir os métodos que serão referenciados pelo aplicativo e camadas de interface do usuário.

Regras de negócios e a lógica seriam colocados aqui se necessária – por exemplo qualquer regra de validação deve ser atendida antes que um objeto é salvo.

 <a name="API_for_Platform-Specific_Code" />

### <a name="api-for-platform-specific-code"></a>API para o código específico da plataforma

Depois que o código comum ter sido gravado, a interface do usuário deve ser criada para coletar e exibir os dados apresentados por ele. O `TaskItemManager` classe implementa o padrão de fachada para fornecer uma API simples para o código do aplicativo para acessar.

O código escrito em cada projeto específico da plataforma geralmente será rigidamente acoplado ao SDK nativo desse dispositivo e acessar apenas o código comum usando a API definida pelo `TaskItemManager`. Isso inclui os métodos e negócios classes ele expõe, tais como `TaskItem`.

Imagens não são compartilhadas entre plataformas mas adicionadas independentemente para cada projeto. Isso é importante porque cada plataforma lida com imagens de maneira diferente, usando as resoluções, diretórios e nomes de arquivo diferentes.

As seções restantes discutem os detalhes de implementação específica da plataforma da interface do usuário Tasky.

 <a name="iOS_App" />

## <a name="ios-app"></a>Aplicativo iOS

Há apenas algumas poucas classes necessárias para implementar o aplicativo Tasky usando o projeto PCL comum para armazenar e recuperar dados do iOS. O projeto xamarin. IOS de iOS completo é mostrado abaixo:

 ![](case-study-tasky-images/taskyios-solution.png "projeto do iOS é mostrado aqui")

As classes são mostradas neste diagrama, agrupados em camadas.

 [![](case-study-tasky-images/classdiagram-android.png "As classes são mostradas neste diagrama, agrupados em camadas")](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References" />

### <a name="references"></a>Referências

O aplicativo para iOS faz referência a bibliotecas do SDK específica da plataforma – por exemplo. Xamarin. IOS e MonoTouch-1.

Ele também deve fazer referência a `TaskyPortableLibrary` projeto PCL.
A lista de referências é mostrada aqui:

 ![](case-study-tasky-images/taskyios-references.png "A lista de referências é mostrada aqui")

A camada de aplicativo e a camada de Interface do usuário são implementados neste projeto usando essas referências.

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>Camada de aplicativo (AL)

A camada de aplicativo contém classes específicas da plataforma necessárias para 'associar' os objetos expostos pelo PCL na interface do usuário. O aplicativo específico do iOS tem duas classes para ajudar a exibir tarefas:

 -   **EditingSource** – essa classe é usada para associar as listas de tarefas para a interface do usuário. Porque `MonoTouch.Dialog` foi usado para a lista de tarefas, precisamos implementar esse auxiliar para habilitar a funcionalidade de passar o dedo para exclusão no `UITableView` . Passe o dedo para exclusão é comum no iOS, mas não Android ou Windows Phone, para que o projeto específico do iOS é o único que a implementa.
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

Observe que o `OnTap` atributos exigem um nome de método – esses métodos devem existir na classe onde o `MonoTouch.Dialog.BindingContext` é criado (nesse caso, o `HomeScreen` classe discutido na próxima seção).

 <a name="User_Interface_Layer_(UI)" />

### <a name="user-interface-layer-ui"></a>Camada de Interface do usuário (IU)

A camada de Interface do usuário consiste as seguintes classes:

1.   **AppDelegate** – contém as chamadas para a API de aparência para as fontes e cores usadas no aplicativo de estilo. Tasky é um aplicativo simples para que não há nenhuma outra tarefa de inicialização em execução no `FinishedLaunching` .
2.   **As telas** – as subclasses de `UIViewController` que definem cada tela e seu comportamento. Telas de unir a interface do usuário com as classes de camada de aplicativo e a API comum ( `TaskItemManager` ). Neste exemplo, as telas são criadas em código, mas podem eles tenham sido desenvolvidos usando o Interface Builder do Xcode ou o designer de storyboard.
3.   **Imagens** – elementos visuais são uma parte importante de todos os aplicativos. Tasky tem tela e o ícone de imagens de abertura, que devem ser fornecidas em regular e resolução Retina para iOS.

 <a name="Home_Screen" />

#### <a name="home-screen"></a>Tela inicial

A tela inicial é um `MonoTouch.Dialog` tela que exibe uma lista de tarefas do banco de dados SQLite. Ela herda `DialogViewController` e implementa o código para definir o `Root` para conter uma coleção de `TaskItem` objetos para exibição.

 [![](case-study-tasky-images/ios-taskylist.png "Ela herda DialogViewController e implementa o código para definir a raiz para conter uma coleção de objetos TaskItem para exibição")](case-study-tasky-images/ios-taskylist.png#lightbox)

Os dois métodos principais relacionados a exibir e interagir com a lista de tarefas são:

1.   **PopulateTable** – usa a camada de negócios `TaskManager.GetTasks` método para recuperar uma coleção de `TaskItem` objetos a serem exibidos.
2.   **Selecionado** – quando uma linha é tocada, exibe a tarefa em uma nova tela.

 <a name="Task_Details_Screen" />

#### <a name="task-details-screen"></a>Tela de detalhes da tarefa

Detalhes da tarefa é uma tela de entrada que permite que as tarefas a ser editado ou excluído.

Usa tasky `MonoTouch.Dialog`da API de reflexão para exibir a tela, portanto, não há nenhum `UIViewController` implementação. Em vez disso, o `HomeScreen` cria uma instância de classe e exibe uma `DialogViewController` usando o `TaskDialog` classe da camada de aplicativo.

Nesta captura de tela mostra uma tela vazia que demonstra a `Entry` definir o texto de marca d'água em de atributo o **nome** e **notas** campos:

 [![](case-study-tasky-images/ios-taskydetail.png "Esta captura de tela mostra uma tela vazia que demonstra o atributo de entrada, definindo o texto de marca d'água nos campos nome e notas")](case-study-tasky-images/ios-taskydetail.png#lightbox)

A funcionalidade do **detalhes da tarefa** tela (por exemplo, salvar ou excluir uma tarefa) deve ser implementada na `HomeScreen` classe, porque isso é onde o `MonoTouch.Dialog.BindingContext` é criado. O seguinte `HomeScreen` métodos dão suporte a tela de detalhes da tarefa:

1.   **ShowTaskDetails** – cria um `MonoTouch.Dialog.BindingContext` para renderizar uma tela. Ele cria a tela de entrada usando a reflexão para recuperar os nomes de propriedade e tipos do `TaskDialog` classe. Informações adicionais, como o texto de marca d'água para as caixas de entrada, são implementadas com os atributos nas propriedades.
2.   **SaveTask** – esse método é referenciado em de `TaskDialog` classe por meio de um `OnTap` atributo. Ele é chamado quando **salve** é pressionada e usa um `MonoTouch.Dialog.BindingContext` para recuperar os dados inseridos pelo usuário antes de salvar as alterações usando `TaskItemManager` .
3.   **DeleteTask** – esse método é referenciado em de `TaskDialog` classe por meio de um `OnTap` atributo. Ele usa `TaskItemManager` para excluir os dados usando a chave primária (propriedade de ID).

 <a name="Android_App" />

## <a name="android-app"></a>Aplicativo Android

O projeto xamarin. Android completo é mostrado abaixo:

 ![](case-study-tasky-images/taskyandroid-solution.png "Projeto do Android é mostrado aqui")

Diagrama de classe, com as classes agrupadas por camada:

 [![](case-study-tasky-images/classdiagram-android.png "Diagrama de classe, com as classes agrupadas por camada")](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References" />

### <a name="references"></a>Referências

O projeto de aplicativo do Android deve fazer referência a assembly específico da plataforma xamarin. Android para classes de acesso do SDK do Android.

Ele também deve referenciar o projeto PCL (por exemplo. TaskyPortableLibrary) para acessar o código da camada de dados e de negócios comum.

 ![](case-study-tasky-images/taskyandroid-references.png "TaskyPortableLibrary para acessar o código da camada de dados e de negócios comuns")

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>Camada de aplicativo (AL)

Semelhante à versão do iOS que vimos anteriormente, a camada de aplicativo no Android versão contém classes específicas da plataforma necessárias para os objetos expostos pelos principais na interface do usuário de 'associar'.

 **TaskListAdapter** – para exibir uma lista<T> de objetos, precisamos implementar um adaptador para exibir os objetos personalizados em um `ListView`. O adaptador controla qual layout será usado para cada item na lista – nesse caso, o código usa um layout interno Android `SimpleListItemChecked`.

 <a name="User_Interface_(UI)" />

### <a name="user-interface-ui"></a>Interface do usuário (IU)

Camada de Interface do usuário do aplicativo Android é uma combinação de código e marcação XML.

 -   **Recursos/Layout** – layouts de tela e a linha da célula implementado como arquivos AXML de design. O AXML pode ser escrito à mão, ou horizontal apresentados visualmente usando o Designer de interface do usuário do Xamarin para Android.
 -   **Recursos/Drawable** – imagens (ícones) e o botão personalizado.
 -   **As telas** – subclasses de atividade que definem cada tela e seu comportamento. Vincula a interface do usuário com as classes de camada de aplicativo e a API comum (`TaskItemManager`).

 <a name="Home_Screen" />

#### <a name="home-screen"></a>Tela inicial

A tela inicial consiste em uma subclasse de atividade `HomeScreen` e o `HomeScreen.axml` arquivo que define o layout (a posição da lista de botão e tarefas). A tela terá esta aparência:

 [![](case-study-tasky-images/android-taskylist.png "A tela tenha esta aparência")](case-study-tasky-images/android-taskylist.png#lightbox)

O código a tela inicial define os manipuladores para clicar no botão e clicar nos itens na lista, bem como popular a lista no `OnResume` método (de modo que ele reflete as alterações feitas na tela de detalhes de tarefa). Os dados são carregados usando a camada de negócios `TaskItemManager` e o `TaskListAdapter` da camada de aplicativo.

 <a name="Task_Details_Screen" />

#### <a name="task-details-screen"></a>Tela de detalhes da tarefa

A tela de detalhes da tarefa também consiste em um `Activity` subclasse e um arquivo de layout AXML. O layout determina o local dos controles de entrada e o C# classe define o comportamento para carregar e salvar `TaskItem` objetos.

 [![](case-study-tasky-images/android-taskydetail.png "A classe define o comportamento para carregar e salvar objetos TaskItem")](case-study-tasky-images/android-taskydetail.png#lightbox)

Todas as referências à biblioteca de PCL são feitas por meio de `TaskItemManager` classe.

 <a name="Windows_Phone_App" />

## <a name="windows-phone-app"></a>Aplicativo do Windows Phone
O projeto completo do Windows Phone:

 ![](case-study-tasky-images/taskywp7-solution.png "Aplicativo do Windows Phone o projeto completo do Windows Phone")

O diagrama a seguir apresenta as classes agrupadas em camadas:

 [![](case-study-tasky-images/classdiagram-wp7.png "Esse diagrama apresenta as classes agrupadas em camadas")](case-study-tasky-images/classdiagram-wp7.png#lightbox)

 <a name="References" />

### <a name="references"></a>Referências

O projeto específico da plataforma deve fazer referência a bibliotecas necessárias específicas da plataforma (como `Microsoft.Phone` e `System.Windows`) para criar um aplicativo válido do Windows Phone.

Ele também deve referenciar o projeto PCL (por exemplo. `TaskyPortableLibrary`) para utilizar o `TaskItem` classe e o banco de dados.

 ![](case-study-tasky-images/taskywp7-references.png "TaskyPortableLibrary para utilizar a classe de TaskItem e o banco de dados")

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>Camada de aplicativo (AL)

Novamente, assim como acontece com as versões do Android e iOS, a camada de aplicativo consiste nos elementos de não-visuais que ajudam a associar dados à interface do usuário.

 <a name="ViewModels" />

#### <a name="viewmodels"></a>ViewModels

ViewModels encapsular os dados de PCL ( `TaskItemManager`) e a apresenta na forma que pode ser consumida por vinculação de dados/XAML do Silverlight. Este é um exemplo de comportamento específicos da plataforma (como discutido no documento de aplicativos de plataforma cruzada).

 <a name="User_Interface_(UI)" />

### <a name="user-interface-ui"></a>Interface do usuário (IU)

XAML tem um recurso exclusivo de vinculação de dados que pode ser declarado na marcação e reduzir a quantidade de código necessária para exibir os objetos:

1.   **Páginas** – arquivos XAML e seu code-behind definem a interface do usuário e os ViewModels e o projeto PCL para exibir e coletar dados de referência.
2.   **Imagens** – imagens de tela, o plano de fundo e o ícone de abertura são uma parte importante da interface do usuário.

 <a name="MainPage" />

#### <a name="mainpage"></a>MainPage

Usa a classe MainPage usa o `TaskListViewModel` para exibir dados usando os recursos de vinculação de dados do XAML. A página `DataContext` é definido como o modelo de exibição, que é populado assincronamente. O `{Binding}` sintaxe em que o XAML determina como os dados são exibidos.

 <a name="TaskDetailsPage" />

#### <a name="taskdetailspage"></a>TaskDetailsPage

Cada tarefa é exibida associando o `TaskViewModel` para o XAML definido no TaskDetailsPage.xaml. Os dados de tarefa são recuperados por meio de `TaskItemManager` na camada de negócios.

 <a name="Results" />

## <a name="results"></a>Resultados

Os aplicativos resultantes se parecer com isso em cada plataforma:

 <a name="iOS" />

#### <a name="ios"></a>iOS

O aplicativo usa o design de interface de usuário padrão do iOS, como o botão 'Adicionar' que está sendo posicionado na barra de navegação e usando o interno **mais (+)** ícone. Ele também usa o padrão `UINavigationController` botão 'Voltar' comportamento e dá suporte a 'passar o dedo para excluir' na tabela.

 [![](case-study-tasky-images/ios-taskylist.png "Ele também usa o comportamento do botão Voltar de UINavigationController padrão e suporta passe o dedo para exclusão na tabela")](case-study-tasky-images/ios-taskylist.png#lightbox) [![](case-study-tasky-images/ios-taskylist.png "também usa o padrão UINavigationController fazer o comportamento do botão e dá suporte a passe o dedo para exclusão na tabela")](case-study-tasky-images/ios-taskylist.png#lightbox)

 <a name="Android" />

#### <a name="android"></a>Android

O aplicativo do Android usa controles internos, incluindo o layout interno para linhas que exigem um 'tick' exibida. O comportamento de voltar de hardware/sistema há suporte para além de um botão na tela de fundo.

 [![](case-study-tasky-images/android-taskylist.png "O comportamento de voltar de hardware/sistema tem suporte, além de um botão na tela de fundo")](case-study-tasky-images/android-taskylist.png#lightbox)[![](case-study-tasky-images/android-taskylist.png "o comportamento de voltar de hardware/sistema há suporte para além na tela botão Voltar")](case-study-tasky-images/android-taskylist.png#lightbox)

 <a name="Windows_Phone" />

#### <a name="windows-phone"></a>Windows Phone

O aplicativo do Windows Phone usa o layout padrão, preenchendo a barra de aplicativos na parte inferior da tela, em vez de uma barra de navegação na parte superior.

 [![](case-study-tasky-images/wp-taskylist.png "O aplicativo do Windows Phone usa o layout padrão, preenchendo a barra de aplicativos na parte inferior da tela, em vez de uma barra de navegação na parte superior")](case-study-tasky-images/wp-taskylist.png#lightbox) [![](case-study-tasky-images/wp-taskylist.png "do Windows Phone o aplicativo usa o padrão layout, preenchendo a barra de aplicativos na parte inferior da tela, em vez de uma barra de navegação na parte superior")](case-study-tasky-images/wp-taskylist.png#lightbox)

 <a name="Summary" />

## <a name="summary"></a>Resumo

Este documento fornece uma explicação detalhada de como os princípios de design de aplicativo em camadas foram aplicados a um aplicativo simples para facilitar a reutilização de código entre as três plataformas móveis: iOS, Android e Windows Phone.

Ele tem descreveu o processo usado para projetar as camadas do aplicativo e discutido qual código &amp; funcionalidade foi implementada em cada camada.

O código pode ser baixado em [github](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable).

## <a name="related-links"></a>Links relacionados

- [Criando aplicativos de plataforma cruzada (documento principal)](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [Aplicativo de exemplo portátil tasky (github)](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)
