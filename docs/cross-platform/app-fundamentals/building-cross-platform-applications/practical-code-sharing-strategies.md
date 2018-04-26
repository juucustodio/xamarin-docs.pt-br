---
title: Parte 5 - compartilhamento estratégias de código prático
ms.prod: xamarin
ms.assetid: 328D042A-FF78-A7B6-1574-B5AF49A1AADB
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 6f6b88bf29e94a221b2ef58b3299348eb08d33fa
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2018
---
# <a name="part-5---practical-code-sharing-strategies"></a>Parte 5 - compartilhamento estratégias de código prático

Esta seção fornece exemplos de código para cenários comuns de aplicativo de compartilhamento.



## <a name="data-layer"></a>Camada de dados

A camada de dados consiste em um mecanismo de armazenamento e os métodos para ler e gravar informações. Para fins de compatibilidade de plataforma cruzada, flexibilidade e desempenho do SQLite mecanismo de banco de dados é recomendado para aplicativos de plataforma cruzada do Xamarin.
Ele é executado em uma ampla variedade de plataformas, incluindo Windows, Android, iOS e Mac.


### <a name="sqlite"></a>SQLite

SQLite é uma implementação de banco de dados do código-fonte aberto. A documentação e código-fonte podem ser encontrados em [SQLite.org](http://www.sqlite.org/). SQLite suporte está disponível em cada plataforma móvel:

-  **iOS** – interna do sistema operacional.
- **Android** – interna do sistema operacional desde Android 2.2 (API nível 10).
- **Windows** – consulte a [SQLite para extensão de plataforma Universal do Windows](https://visualstudiogallery.msdn.microsoft.com/4913e7d5-96c9-4dde-a1a1-69820d615936).


Mesmo com o mecanismo de banco de dados disponível em todas as plataformas, os métodos nativos para acessar o banco de dados são diferentes. Ambas as iOS e Android oferecem internos APIs para acessar SQLite que pode ser usado de xamarin ou xamarin, no entanto, usar os métodos nativos do SDK oferece sem capacidade de compartilhamento de código (diferente de talvez as consultas SQL, supondo que eles são armazenados como cadeias de caracteres) . Para obter detalhes sobre a pesquisa de funcionalidade de banco de dados nativo para `CoreData` no iOS ou do Android `SQLiteOpenHelper` classe; porque essas opções não estão entre plataformas estão fora do escopo deste documento.



### <a name="adonet"></a>ADO.NET

Suporte de xamarin e xamarin `System.Data` e `Mono.Data.Sqlite` (consulte o xamarin [documentação](~/ios/data-cloud/system.data.md) para obter mais informações).
Esses namespaces permite que você escreva código ADO.NET que funciona em ambas as plataformas. Editar as referências do projeto para incluir `System.Data.dll` e `Mono.Data.Sqlite.dll` e adicioná-las usando as instruções no seu código:

```csharp
using System.Data;
using Mono.Data.Sqlite;
```

O código de exemplo a seguir funcionará:

```csharp
string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "items.db3");
bool exists = File.Exists (dbPath);
if (!exists)
    SqliteConnection.CreateFile (dbPath);
var connection = new SqliteConnection ("Data Source=" + dbPath);
connection.Open ();
if (!exists) {
    // This is the first time the app has run and/or that we need the DB.
    // Copy a "template" DB from your assets, or programmatically create one like this:
    var commands = new[]{
        "CREATE TABLE [Items] (Key ntext, Value ntext);",
        "INSERT INTO [Items] ([Key], [Value]) VALUES ('sample', 'text')"
    };
    foreach (var command in commands) {
        using (var c = connection.CreateCommand ()) {
            c.CommandText = command;
            c.ExecuteNonQuery ();
        }
    }
}
// use `connection`... here, we'll just append the contents to a TextView
using (var contents = connection.CreateCommand ()) {
    contents.CommandText = "SELECT [Key], [Value] from [Items]";
    var r = contents.ExecuteReader ();
    while (r.Read ())
        Console.Write("\n\tKey={0}; Value={1}",
                r ["Key"].ToString (),
                r ["Value"].ToString ());
}
connection.Close ();
```

As implementações reais do ADO.NET obviamente serão divididas em diferentes métodos e classes (neste exemplo é para fins de demonstração).



### <a name="sqlite-net--cross-platform-orm"></a>SQLite-NET – ORM de plataforma cruzada

Um ORM (ou mapeador relacional de objetos) tenta simplificam o armazenamento de dados modelados de classes. Em vez de manualmente escrever consultas SQL que criar tabelas ou SELECT, INSERT e DELETE dados extraídos manualmente da classe campos e propriedades, um ORM adiciona uma camada de código que faz isso para você. Usando a reflexão para examinar a estrutura de suas classes, um ORM automaticamente pode criar tabelas e colunas que correspondem a uma classe e geram consultas para ler e gravar os dados. Isso permite que o código do aplicativo simplesmente enviar e recuperar instâncias de objeto para ORM, que cuida de todas as operações de SQL nos bastidores.

SQLite-NET atua como um simples ORM que permitirá que você salvar e recuperar suas classes no SQLite. Ele oculta a complexidade de cruzada plataforma SQLite acesso com uma combinação de diretivas de compilador e outros truques.

Recursos do SQLite NET:

-  Tabelas são definidas pela adição de atributos para classes de modelo.
-  Uma instância de banco de dados é representada por uma subclasse de `SQLiteConnection` , a classe principal da biblioteca de rede SQLite.
-  Podem ser inseridos dados consultados e excluídas usando objetos. Nenhuma instrução SQL é necessárias (embora você pode escrever instruções SQL, se necessário).
-  Consultas básicas do Linq podem ser executadas nas coleções retornadas pelo SQLite-NET.


O código-fonte e a documentação para SQLite NET está disponível em [SQLite-Net no github](https://github.com/praeclarum/sqlite-net) e foi implementado em ambos os estudos de caso. Um exemplo simples de código NET SQLite (da *Tasky Pro* estudo de caso) é mostrado abaixo.

Primeiro, a `TodoItem` classe usa atributos para definir um campo para ser uma chave primária do banco de dados:

```csharp
public class TodoItem : IBusinessEntity
{
    public TodoItem () {}
    [PrimaryKey, AutoIncrement]
    public int ID { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
}
```

Isso permite que um `TodoItem` tabela a ser criada com a seguinte linha de código (e nenhuma instrução SQL) em um `SQLiteConnection` instância:

```csharp
CreateTable<TodoItem> ();
```

Dados da tabela também podem ser manipulados com outros métodos no `SQLiteConnection` (novamente, sem a necessidade de instruções SQL):

```csharp
Insert (TodoItem); // 'task' is an instance with data populated in its properties
Update (TodoItem); // Primary Key field must be populated for Update to work
Table<TodoItem>.ToList(); // returns all rows in a collection
```

Consulte o código-fonte estudo de caso para obter exemplos de conclusão.



## <a name="file-access"></a>Acesso a arquivos

Acesso a arquivo é determinado a ser uma parte importante de qualquer aplicativo. Exemplos comuns de arquivos que podem ser parte de um aplicativo são:

-  Arquivos de banco de dados SQLite.
-  Dados gerados por usuários (texto, imagens, som, vídeo).
-  Dados baixados para o cache (imagens, html ou arquivos PDF).




### <a name="systemio-direct-access"></a>Acesso direto System.IO

Xamarin e xamarin permitem acesso de sistema de arquivos usando classes no `System.IO` namespace.

Cada plataforma tem restrições de acesso diferentes que devem ser levadas em consideração:

-  aplicativos do iOS são executados em uma área restrita com acesso de sistema de arquivos muito restrito. Apple mais determina como você deve usar o sistema de arquivos especificando determinados locais que são submetidos a backup (e outros não). Consulte o [trabalhando com o sistema de arquivos do xamarin](~/ios/app-fundamentals/file-system.md) guia para obter mais detalhes.
-  Android também restringe o acesso a determinados diretórios relacionados ao aplicativo, mas ele também dá suporte a mídia externa (por exemplo. Cartões SD) e acessar dados compartilhados.
-  Windows Phone 8 (Silverlight) não permitem acesso direto ao arquivo – arquivos só podem ser manipulados usando `IsolatedStorage`.
-  Projetos WinRT do Windows 8.1 e Windows 10 UWP oferecem somente operações de arquivo assíncrono via `Windows.Storage` APIs, que são diferentes de outras plataformas.

#### <a name="example-for-ios-and-android"></a>Exemplo de iOS e Android

Um exemplo simples que grava e lê um arquivo de texto é mostrado abaixo.
Usando `Environment.GetFolderPath` permite que o mesmo código ser executado no iOS e Android, que retornam um diretório válido com base nas suas convenções do sistema de arquivos.

```csharp
string filePath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "MyFile.txt");
System.IO.File.WriteAllText (filePath, "Contents of text file");
Console.WriteLine (System.IO.ReadAllText (filePath));
```

Consulte o xamarin [trabalhando com o sistema de arquivos](~/ios/app-fundamentals/file-system.md) documento para obter mais informações sobre a funcionalidade do sistema de arquivos específico do iOS. Ao escrever o código de acesso do arquivo de plataforma cruzada, lembre-se de que alguns sistemas de arquivo diferenciam maiusculas de minúsculas e têm os separadores de outro diretório. É recomendável sempre usar a mesmo de maiusculas e minúsculas para nomes de arquivo e o `Path.Combine()` método ao construir caminhos de arquivo ou diretório.



### <a name="windowsstorage-for-windows-8-and-windows-10"></a>Windows.Storage para Windows 8 e Windows 10

O *criação de aplicativos móveis com o xamarin. Forms* [catálogo](https://developer.xamarin.com/r/xamarin-forms/book/)
[capítulo 20. Async e e/s de arquivo](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf) inclui [exemplos para Windows 8.1 e Windows 10](https://github.com/xamarin/xamarin-forms-book-preview-2/tree/master/Chapter20).

Usando um [ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md) é possível ler e arquivo arquivos nessas plataformas usando as APIs com suporte:

```csharp
StorageFolder localFolder = ApplicationData.Current.LocalFolder;
IStorageFile storageFile = await localFolder.CreateFileAsync("MyFile.txt",
                                        CreationCollisionOption.ReplaceExisting);
await FileIO.WriteTextAsync(storageFile, "Contents of text file");
```

Consulte o [capítulo catálogo](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf) para obter mais detalhes.


<a name="Isolated_Storage" />

### <a name="isolated-storage-on-windows-phone-7--8-silverlight"></a>Armazenamento isolado no Windows Phone 7 e 8 (Silverlight)

Armazenamento isolado é uma API para salvar e carregar arquivos em todas as plataformas mais antigas do Windows Phone, Android e iOS.

É o mecanismo padrão para acesso a arquivos no Windows Phone (Silverlight) que foi implementado no xamarin e o xamarin para permitir que o código de acesso a arquivos comuns a serem gravados. O `System.IO.IsolatedStorage` classe pode ser referenciada em todas as plataformas de três em um [projeto compartilhado](~/cross-platform/app-fundamentals/shared-projects.md).

Consulte o [visão geral de armazenamento isolado para de Windows Phone](http://msdn.microsoft.com/library/windowsphone/develop/ff402541(v=vs.105).aspx) para obter mais informações.

As APIs de armazenamento isolado não estão disponíveis em [bibliotecas de classes portáteis](~/cross-platform/app-fundamentals/pcl.md). É uma alternativa para PCL o [PCLStorage NuGet](https://pclstorage.codeplex.com/)



### <a name="cross-platform-file-access-in-pcls"></a>Acesso a arquivos de plataforma cruzada em PCLs

Também há um Nuget compatível com PCL – [PCLStorage](https://www.nuget.org/packages/PCLStorage/) – esse acesso a arquivos de plataforma cruzada instalações para plataformas com suporte de Xamarin e APIs do Windows mais recente.


## <a name="network-operations"></a>Operações de rede

Maioria dos aplicativos móveis terá o componente de rede, por exemplo:

-  Download de imagens, áudio e vídeo (por exemplo. miniaturas, fotos, música).
-  Download de documentos (por exemplo. HTML, PDF).
-  Carregando dados do usuário (por exemplo, fotos ou texto).
-  Acessando serviços web ou 3ª parte APIs (incluindo SOAP, XML ou JSON).


O .NET Framework fornece algumas classes diferentes para acessar recursos de rede: `HttpClient`, `WebClient`, e `HttpWebRequest`.

### <a name="httpclient"></a>HttpClient

O `HttpClient` classe no `System.Net.Http` namespace está disponível na maioria das plataformas Windows, xamarin e xamarin. Há um [Nuget de biblioteca de cliente do Microsoft HTTP](https://www.nuget.org/packages/Microsoft.Net.Http/) que pode ser usado para trazer essa API para bibliotecas de classes portáteis (e Windows Phone 8 Silverlight).

```csharp
var client = new HttpClient();
var request = new HttpRequestMessage(HttpMethod.Get, "https://xamarin.com");
var response = await myClient.SendAsync(request);
```

### <a name="webclient"></a>Clientes web

O `WebClient` classe fornece uma API simples para recuperar dados remotos de servidores remotos.

Operações universais do Windows Platofrm *deve* ser assíncrono, embora xamarin e xamarin suportam para operações síncronas (o que podem ser feitas em threads em segundo plano).

O código para um simple assíncrona `WebClient` operação:

```csharp
var webClient = new WebClient ();
webClient.DownloadStringCompleted += (sender, e) =>
{
    var resultString = e.Result;
    // do something with downloaded string, do UI interaction on main thread
};
webClient.Encoding = System.Text.Encoding.UTF8;
webClient.DownloadStringAsync (new Uri ("http://some-server.com/file.xml"));
```

 `WebClient` também tem `DownloadFileCompleted` e `DownloadFileAsync` para recuperar dados binários.

<a name="HttpWebRequest" />

### <a name="httpwebrequest"></a>HttpWebRequest

`HttpWebRequest` oferece mais personalização que `WebClient` e como resultado, requer mais código para usar.

O código para um simples síncrono `HttpWebRequest` operação:

```csharp
var request = HttpWebRequest.Create(@"http://some-server.com/file.xml ");
request.ContentType = "text/xml";
request.Method = "GET";
using (HttpWebResponse response = request.GetResponse() as HttpWebResponse)
{
    if (response.StatusCode != HttpStatusCode.OK)
        Console.WriteLine("Error fetching data. Server returned status code: {0}", response.StatusCode);
    using (StreamReader reader = new StreamReader(response.GetResponseStream()))
    {
        var content = reader.ReadToEnd();
        // do something with downloaded string, do UI interaction on main thread
    }
}
```

Há um exemplo em nosso [documentação de serviços Web](~/cross-platform/data-cloud/web-services/index.md).

 <a name="Reachability" />


### <a name="reachability"></a>Acessibilidade

Dispositivos móveis operam sob uma variedade de condições de rede da Wi-Fi rápida ou conexões 4g para áreas de recepção baixa e links lentos de dados de borda. Por isso, é uma boa prática para detectar se a rede está disponível e se assim, o tipo de rede estiver disponível, antes de tentar se conectar a servidores remotos.

Um aplicativo móvel pode levar nessas situações de ações incluem:

-  Se a rede estiver indisponível, avise o usuário. Se eles tem desabilitado-lo manualmente (por exemplo. Modo avião ou desativar Wi-Fi), em seguida, eles podem resolver o problema.
-  Se a conexão for 3G, aplicativos podem se comportar de maneira diferente (por exemplo, Apple não permitir que os aplicativos maiores que 20Mb a ser baixado em 3G). Aplicativos podem usar essas informações para avisar o usuário sobre o download excessiva quando recuperar arquivos grandes.
-  Mesmo se a rede estiver disponível, é uma boa prática para verificar a conectividade com o servidor de destino antes de iniciar outras solicitações. Isso impedirá a operações de rede do aplicativo do tempo limite repetidamente e também permitem que uma mensagem de erro mais informativa a ser exibida para o usuário.


Há um [xamarin exemplo](https://github.com/xamarin/monotouch-samples/tree/master/ReachabilitySample) disponível (que é baseado da Apple [código de exemplo de acessibilidade](http://developer.apple.com/library/ios/#samplecode/Reachability/Introduction/Intro.html) ) para ajudar a detectar a disponibilidade de rede.


## <a name="webservices"></a>WebServices

Consulte nossa documentação sobre [trabalhar com serviços Web](~/cross-platform/data-cloud/web-services/index.md), que abrange acessando REST, SOAP e WCF pontos de extremidade usando o xamarin. É possível criar solicitações de serviço de web e analisar as respostas, no entanto, há bibliotecas disponíveis para tornar isso muito mais simples, incluindo Azure, RestSharp e ServiceStack. Operações mesmo básicas do WCF podem ser acessadas em aplicativos Xamarin.

### <a name="azure"></a>Azure

Microsoft Azure é uma plataforma de nuvem que fornece uma ampla variedade de serviços para aplicativos móveis, incluindo armazenamento de dados, sincronização e notificações por push.

Visite [azure.microsoft.com](https://azure.microsoft.com/) para testá-lo gratuitamente.

### <a name="restsharp"></a>RestSharp

RestSharp é uma biblioteca .NET que pode ser incluída em aplicativos móveis para fornecer um cliente REST que simplifica o acesso a serviços web. Ele ajuda, fornecendo uma API simples para solicitar dados e analisar a resposta REST. RestSharp pode ser útil

O [RestSharp site](http://restsharp.org/) contém [documentação](https://github.com/restsharp/RestSharp/wiki) sobre como implementar um cliente REST usando RestSharp.
RestSharp fornece exemplos de xamarin e xamarin [github](https://github.com/restsharp/RestSharp/).

Também é um trecho de código xamarin em nosso [documentação de serviços Web](~/cross-platform/data-cloud/web-services/index.md).

 <a name="ServiceStack" />


### <a name="servicestack"></a>ServiceStack

Ao contrário de RestSharp, ServiceStack é uma solução de servidor para hospedar um serviço web, bem como uma biblioteca de cliente que pode ser implementada em aplicativos móveis para acessar esses serviços.

O [ServiceStack site](http://servicestack.net/) explica a finalidade do projeto e links para documentos e amostras de códigos. Os exemplos incluem uma implementação de servidor completa de um serviço web, bem como vários aplicativos cliente que podem acessá-lo.

Há um [xamarin exemplo](http://www.servicestack.net/monotouch/remote-info/) no site da ServiceStack e um trecho de código em nosso [documentação de serviços Web](~/cross-platform/data-cloud/web-services/index.md).


### <a name="wcf"></a>WCF

Ferramentas Xamarin podem ajudá-lo a consumir alguns serviços do Windows Communication Foundation (WCF). Em geral, Xamarin suporta o mesmo subconjunto de cliente do WCF que acompanha o runtime do Silverlight. Isso inclui as implementações de codificação e o protocolo mais comuns do WCF: texto codificado mensagens SOAP sobre HTTP usando o protocolo de transporte de `BasicHttpBinding`.

Devido ao tamanho e complexidade da estrutura do WCF, pode haver implementações de serviço atuais e futuras que corresponderá fora do escopo com suporte pelo domínio do cliente subconjunto do Xamarin. Além disso, o suporte do WCF requer o uso de ferramentas disponíveis somente em um ambiente do Windows para gerar o proxy.

 <a name="Threading" />


## <a name="threading"></a>Threading

Capacidade de resposta do aplicativo é importante para aplicativos móveis, os usuários esperam que os aplicativos para carregar e executar rapidamente. Uma tela de 'congelada' para de aceitar a entrada do usuário será exibida para indicar que o aplicativo falhou, portanto, é importante não associa o thread de interface do usuário com chamadas de bloqueio de longa execução, como solicitações de rede ou lenta operações locais (como descompactar um arquivo). Em particular, o processo de inicialização não deve conter tarefas de longa execução – todas as plataformas móveis finalizará a um aplicativo que demora muito para carregar.

Isso significa que a interface do usuário deve implementar um indicador de progresso' ' ou 'utilizável' caso contrário, interface do usuário que é rápido exibir e tarefas assíncronas para executar operações em segundo plano. Executar tarefas em segundo plano requer o uso de threads, que significa que as necessidades de tarefas em segundo plano uma maneira de se comunicar com o thread principal para indicar o progresso ou quando tiver concluído.

 <a name="Parallel_Task_Library" />


### <a name="parallel-task-library"></a>Biblioteca de tarefas paralelas

Tarefas criadas com a biblioteca paralela de tarefas podem executar de forma assíncrona e retorno sobre o thread de chamada, facilitando muito útil para disparar operações demoradas sem bloquear a interface do usuário.

Uma operação de tarefa paralela simples pode ter esta aparência:

```csharp
using System.Threading.Tasks;
void MainThreadMethod ()
{
    Task.Factory.StartNew (() => wc.DownloadString ("http://...")).ContinueWith (
        t => label.Text = t.Result, TaskScheduler.FromCurrentSynchronizationContext()
    );
}
```

A chave é `TaskScheduler.FromCurrentSynchronizationContext()` que reutilizará o SynchronizationContext. Current do thread de chamada do método (aqui o thread principal que está executando `MainThreadMethod`) como uma maneira de realizar marshaling de chamadas de retorno para esse segmento. Isso significa que se o método é chamado no thread da interface do usuário, ele será executado o `ContinueWith` operação de volta no thread da interface do usuário.

Se o código está iniciando tarefas de outros threads, use o seguinte padrão para criar uma referência para o thread de interface do usuário e a tarefa ainda pode chamar novamente para ele:

```csharp
static Context uiContext = TaskScheduler.FromCurrentSynchronizationContext();
```

 <a name="Invoking_on_the_UI_Thread" />


### <a name="invoking-on-the-ui-thread"></a>Chamada no Thread da interface do usuário

Código que não utilizam a biblioteca de tarefas paralelas, de cada plataforma tem seu próprio sintaxe para operações de empacotamento para o thread de interface do usuário:

-  **iOS** – `owner.BeginInvokeOnMainThread(new NSAction(action))`
-  **Android** – `owner.RunOnUiThread(action)`
-  **Xamarin. Forms** – `Device.BeginInvokeOnMainThread(action)`
-  **Windows** – `Deployment.Current.Dispatcher.BeginInvoke(action)`



Sintaxe do Android e iOS requer uma classe de 'contexto' esteja disponível, que significa que o código precisa passar esse objeto para todos os métodos que exigem um retorno de chamada no thread da interface do usuário.

Para fazer chamadas de threads de interface do usuário no código compartilhado, execute o [IDispatchOnUIThread exemplo](http://www.slideshare.net/follesoe/cross-platform-mobile-apps-using-net) (gentilmente de [ @follesoe ](http://jonas.follesoe.no/)). Declare e um programa para um `IDispatchOnUIThread` de interface no código compartilhado e, em seguida, implementar as classes específicas da plataforma, conforme mostrado aqui:

```csharp
// program to the interface in shared code
public interface IDispatchOnUIThread {
    void Invoke (Action action);
}
// iOS
public class DispatchAdapter : IDispatchOnUIThread {
    public readonly NSObject owner;
    public DispatchAdapter (NSObject owner) {
        this.owner = owner;
    }
    public void Invoke (Action action) {
        owner.BeginInvokeOnMainThread(new NSAction(action));
    }
}
// Android
public class DispatchAdapter : IDispatchOnUIThread {
    public readonly Activity owner;
    public DispatchAdapter (Activity owner) {
        this.owner = owner;
    }
    public void Invoke (Action action) {
        owner.RunOnUiThread(action);
    }
}
// WP7
public class DispatchAdapter : IDispatchOnUIThread {
    public void Invoke (Action action) {
        Deployment.Current.Dispatcher.BeginInvoke(action);
    }
}
```

Os desenvolvedores do xamarin. Forms devem usar [ `Device.BeginInvokeOnMainThread` ](~/xamarin-forms/platform/device.md#Device_BeginInvokeOnMainThread) no código comum (projetos compartilhados ou PCL).

 <a name="Platform_and_Device_Capabilities_and_Degradation" />


## <a name="platform-and-device-capabilities-and-degradation"></a>Plataforma e recursos do dispositivo e degradação

Mais exemplos específicos de lidar com diferentes capacidades são fornecidos na documentação de recursos de plataforma. Ele lida com detecção de recursos diferentes e como degradar normalmente um aplicativo para fornecer uma boa experiência do usuário, mesmo quando o aplicativo não pode operar em todo o seu potencial.
