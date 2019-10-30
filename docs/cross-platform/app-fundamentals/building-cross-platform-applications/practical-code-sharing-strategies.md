---
title: Parte 5 – Estratégias práticas de compartilhamento de código
description: Este documento discute estratégias práticas de compartilhamento de código para cenários como bancos de dados, acesso a arquivos, operações de rede e código assíncrono.
ms.prod: xamarin
ms.assetid: 328D042A-FF78-A7B6-1574-B5AF49A1AADB
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: fdc9fd6eac8c7b0c9ec91eb66b5d6723cda71006
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016834"
---
# <a name="part-5---practical-code-sharing-strategies"></a>Parte 5 – Estratégias práticas de compartilhamento de código

Esta seção fornece exemplos de como compartilhar código para cenários de aplicativos comuns.

## <a name="data-layer"></a>Camada de dados

A camada de dados consiste em um mecanismo de armazenamento e métodos para ler e gravar informações. Para obter desempenho, flexibilidade e compatibilidade entre plataformas, o mecanismo de banco de dados SQLite é recomendado para aplicativos de plataforma cruzada do Xamarin.
Ele é executado em uma ampla variedade de plataformas, incluindo Windows, Android, iOS e Mac.

### <a name="sqlite"></a>SQLite

O SQLite é uma implementação de banco de dados de código aberto. A origem e a documentação podem ser encontradas em [SQLite.org](https://www.sqlite.org/). O suporte do SQLite está disponível em cada plataforma móvel:

- **Ios** – interno ao sistema operacional.
- **Android** – integrado ao sistema operacional desde o Android 2,2 (nível de API 10).
- **Windows** – consulte a [extensão do SQLite para plataforma universal do Windows](https://visualstudiogallery.msdn.microsoft.com/4913e7d5-96c9-4dde-a1a1-69820d615936).

Mesmo com o mecanismo de banco de dados disponível em todas as plataformas, os métodos nativos para acessar o banco de dados são diferentes. O iOS e o Android oferecem APIs internas para acessar o SQLite que poderia ser usado no Xamarin. iOS ou Xamarin. Android, no entanto, usar os métodos nativos do SDK não oferece capacidade de compartilhar código (além de talvez as consultas SQL propriamente ditas, supondo que elas sejam armazenadas como cadeias de caracteres) . Para obter detalhes sobre a funcionalidade de banco de dados nativa, pesquise `CoreData` na classe `SQLiteOpenHelper` do iOS ou Android; como essas opções não são de plataforma cruzada, elas estão além do escopo deste documento.

### <a name="adonet"></a>ADO.NET

O Xamarin. iOS e o Xamarin. Android dão suporte a `System.Data` e `Mono.Data.Sqlite` (consulte a [documentação](~/ios/data-cloud/system.data.md) do Xamarin. Ios para obter mais informações).
O uso desses namespaces permite que você escreva o código ADO.NET que funciona em ambas as plataformas. Edite as referências do projeto para incluir `System.Data.dll` e `Mono.Data.Sqlite.dll` e adicione-as usando instruções ao seu código:

```csharp
using System.Data;
using Mono.Data.Sqlite;
```

Em seguida, o seguinte código de exemplo funcionará:

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

As implementações do mundo real de ADO.NET obviamente seriam divididas em diferentes métodos e classes (Este exemplo é apenas para fins de demonstração).

### <a name="sqlite-net--cross-platform-orm"></a>SQLite-NET – ORM de plataforma cruzada

Um ORM (ou um mapeador relacional de objeto) tenta simplificar o armazenamento de dados modelados em classes. Em vez de escrever manualmente consultas SQL que criam tabelas ou SELECIONAm, INSEREM e EXCLUem dados que são extraídos manualmente de campos de classe e propriedades, um ORM adiciona uma camada de código que faz isso para você. Usando a reflexão para examinar a estrutura de suas classes, um ORM pode criar automaticamente tabelas e colunas que correspondam a uma classe e gerar consultas para ler e gravar os dados. Isso permite que o código do aplicativo simplesmente envie e recupere instâncias de objeto para o ORM, o que cuida de todas as operações do SQL nos bastidores.

O SQLite-NET atua como um ORM simples que permitirá que você salve e recupere suas classes no SQLite. Ele oculta a complexidade do acesso do SQLite de plataforma cruzada com uma combinação de diretivas de compilador e outros truques.

Recursos do SQLite-NET:

- As tabelas são definidas adicionando atributos a classes de modelo.
- Uma instância de banco de dados é representada por uma subclasse de `SQLiteConnection`, a classe principal na biblioteca SQLite-net.
- Os dados podem ser inseridos, consultados e excluídos usando objetos. Nenhuma instrução SQL é necessária (embora seja possível escrever instruções SQL, se necessário).
- Consultas básicas do LINQ podem ser executadas nas coleções retornadas pelo SQLite-NET.

O código-fonte e a documentação do SQLite-NET estão disponíveis em [SQLite-net no GitHub](https://github.com/praeclarum/sqlite-net) e foram implementados em ambos os estudos de caso. Um exemplo simples de código SQLite-NET (do estudo de caso do *profissional da tarefa* ) é mostrado abaixo.

Primeiro, a classe `TodoItem` usa atributos para definir um campo para ser uma chave primária de banco de dados:

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

Isso permite que uma tabela de `TodoItem` seja criada com a seguinte linha de código (e nenhuma instrução SQL) em uma instância de `SQLiteConnection`:

```csharp
CreateTable<TodoItem> ();
```

Os dados na tabela também podem ser manipulados com outros métodos na `SQLiteConnection` (novamente, sem a necessidade de instruções SQL):

```csharp
Insert (TodoItem); // 'task' is an instance with data populated in its properties
Update (TodoItem); // Primary Key field must be populated for Update to work
Table<TodoItem>.ToList(); // returns all rows in a collection
```

Consulte o código-fonte do estudo de caso para obter exemplos completos.

## <a name="file-access"></a>Acesso a arquivos

O acesso ao arquivo é uma parte importante de qualquer aplicativo. Exemplos comuns de arquivos que podem ser parte de um aplicativo incluem:

- Arquivos de banco de dados SQLite.
- Dados gerados pelo usuário (texto, imagens, som, vídeo).
- Dados baixados para cache (imagens, arquivos HTML ou PDF).

### <a name="systemio-direct-access"></a>Acesso direto ao System.IO

O Xamarin. iOS e o Xamarin. Android permitem o acesso ao sistema de arquivos usando classes no namespace `System.IO`.

Cada plataforma tem restrições de acesso diferentes que devem ser levadas em consideração:

- os aplicativos iOS são executados em uma área restrita com acesso muito restrito ao sistema de arquivos. A Apple dita ainda mais como você deve usar o sistema de arquivos especificando determinados locais que são submetidos a backup (e outros que não são). Consulte o guia [trabalhando com o sistema de arquivos no Xamarin. Ios](~/ios/app-fundamentals/file-system.md) para obter mais detalhes.
- O Android também restringe o acesso a determinados diretórios relacionados ao aplicativo, mas também oferece suporte à mídia externa (por exemplo, Cartões SD) e acesso a dados compartilhados.
- O Windows Phone 8 (Silverlight) não permite acesso direto a arquivos – os arquivos só podem ser manipulados usando `IsolatedStorage`.
- Os projetos UWP Windows 8.1 WinRT e Windows 10 oferecem apenas operações de arquivo assíncrono por meio de APIs `Windows.Storage`, que são diferentes das outras plataformas.

#### <a name="example-for-ios-and-android"></a>Exemplo para iOS e Android

Um exemplo trivial que grava e lê um arquivo de texto é mostrado abaixo.
O uso de `Environment.GetFolderPath` permite que o mesmo código seja executado no iOS e no Android, que retornam um diretório válido com base em suas convenções de sistema de arquivos.

```csharp
string filePath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "MyFile.txt");
System.IO.File.WriteAllText (filePath, "Contents of text file");
Console.WriteLine (System.IO.ReadAllText (filePath));
```

Consulte o Xamarin. iOS [trabalhando com o documento do sistema de arquivos](~/ios/app-fundamentals/file-system.md) para obter mais informações sobre a funcionalidade de sistema de arquivos específica do Ios. Ao escrever código de acesso a arquivos entre plataformas, lembre-se de que alguns sistemas de arquivos diferenciam maiúsculas de minúsculas e têm separadores de diretório diferentes. É recomendável sempre usar o mesmo uso de maiúsculas e minúsculas para nomes de arquivos e o método `Path.Combine()` ao construir caminhos de arquivo ou diretório.

### <a name="windowsstorage-for-windows-8-and-windows-10"></a>Windows. Storage para Windows 8 e Windows 10

O [livro](https://developer.xamarin.com/r/xamarin-forms/book/) *criando aplicativos móveis com Xamarin. Forms*
[capítulo 20. Async e e/s de arquivo](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf) incluem [amostras para Windows 8.1 e Windows 10](https://github.com/xamarin/xamarin-forms-book-preview-2/tree/master/Chapter20).

Usando um [`DependencyService`](~/xamarin-forms/app-fundamentals/dependency-service/index.md) é possível ler e arquivos de arquivo nessas plataformas usando as APIs com suporte:

```csharp
StorageFolder localFolder = ApplicationData.Current.LocalFolder;
IStorageFile storageFile = await localFolder.CreateFileAsync("MyFile.txt",
                                        CreationCollisionOption.ReplaceExisting);
await FileIO.WriteTextAsync(storageFile, "Contents of text file");
```

Consulte o [capítulo de livros](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf) para obter mais detalhes.

<a name="Isolated_Storage" />

### <a name="isolated-storage-on-windows-phone-7--8-silverlight"></a>Armazenamento isolado no Windows Phone 7 & 8 (Silverlight)

O armazenamento isolado é uma API comum para salvar e carregar arquivos em todas as plataformas de Windows Phone iOS, Android e mais antigas.

É o mecanismo padrão de acesso a arquivos no Windows Phone (Silverlight) que foi implementado em Xamarin. iOS e Xamarin. Android para permitir que o código de acesso a arquivos comum seja gravado. A classe `System.IO.IsolatedStorage` pode ser referenciada em todas as três plataformas em um [projeto compartilhado](~/cross-platform/app-fundamentals/shared-projects.md).

Consulte [visão geral do armazenamento isolado para obter Windows Phone](https://msdn.microsoft.com/library/windowsphone/develop/ff402541(v=vs.105).aspx) para obter mais informações.

As APIs de armazenamento isolado não estão disponíveis em [bibliotecas de classes portáteis](~/cross-platform/app-fundamentals/pcl.md). Uma alternativa para o PCL é o [NuGet do PCLStorage](https://pclstorage.codeplex.com/)

### <a name="cross-platform-file-access-in-pcls"></a>Acesso a arquivos entre plataformas no PCLs

Também há um NuGet- [PCLStorage](https://www.nuget.org/packages/PCLStorage/) compatível com PCL – que orienta o acesso a arquivos entre plataformas para plataformas com suporte para Xamarin e as APIs do Windows mais recentes.

## <a name="network-operations"></a>Operações de rede

A maioria dos aplicativos móveis terá um componente de rede, por exemplo:

- Baixando imagens, vídeo e áudio (por exemplo, miniaturas, fotos, música).
- Baixando documentos (por exemplo, HTML, PDF).
- Carregando dados do usuário (como fotos ou texto).
- Acessando serviços Web ou APIs de terceiros (incluindo SOAP, XML ou JSON).

O .NET Framework fornece algumas classes diferentes para acessar recursos de rede: `HttpClient`, `WebClient`e `HttpWebRequest`.

### <a name="httpclient"></a>HttpClient

A classe `HttpClient` no namespace `System.Net.Http` está disponível no Xamarin. iOS, Xamarin. Android e na maioria das plataformas Windows. Há um [NuGet de biblioteca de cliente http da Microsoft](https://www.nuget.org/packages/Microsoft.Net.Http/) que pode ser usado para colocar essa API em bibliotecas de classes portáteis (e Windows Phone 8 Silverlight).

```csharp
var client = new HttpClient();
var request = new HttpRequestMessage(HttpMethod.Get, "https://xamarin.com");
var response = await myClient.SendAsync(request);
```

### <a name="webclient"></a>Clientes web

A classe `WebClient` fornece uma API simples para recuperar dados remotos de servidores remotos.

Plataforma Universal do Windows operações *devem* ser assíncronas, mesmo que o Xamarin. Ios e o Xamarin. Android ofereçam suporte a operações síncronas (o que pode ser feito em threads em segundo plano).

O código para uma operação simples de `WebClient` assíncrona é:

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

o `HttpWebRequest` oferece mais personalização do que `WebClient` e, como resultado, requer mais código a ser usado.

O código para uma operação simples de `HttpWebRequest` síncrona é:

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

Há um exemplo em nossa [documentação de serviços Web](~/cross-platform/data-cloud/web-services/index.md).

 <a name="Reachability" />

### <a name="reachability"></a>Acessibilidade

Os dispositivos móveis operam em uma variedade de condições de rede de conexões rápidas de Wi-Fi ou 4G para áreas de recepção deficiente e links de dados de borda lentos. Por isso, é uma boa prática detectar se a rede está disponível e, em caso afirmativo, que tipo de rede está disponível, antes de tentar se conectar a servidores remotos.

As ações que um aplicativo móvel pode tomar nessas situações incluem:

- Se a rede não estiver disponível, recomende o usuário. Se eles o desabilitaram manualmente (por exemplo, No modo avião ou desligando o Wi-Fi), eles podem resolver o problema.
- Se a conexão for 3G, os aplicativos podem se comportar de forma diferente (por exemplo, a Apple não permite que aplicativos maiores do que 20 MB sejam baixados por 3G). Os aplicativos podem usar essas informações para avisar o usuário sobre tempos de download excessivos ao recuperar arquivos grandes.
- Mesmo que a rede esteja disponível, é uma boa prática verificar a conectividade com o servidor de destino antes de iniciar outras solicitações. Isso impedirá que as operações de rede do aplicativo atinjam o tempo limite repetidamente e também permitirá que uma mensagem de erro mais informativa seja exibida ao usuário.

Há um [exemplo de Xamarin. Ios](https://github.com/xamarin/monotouch-samples/tree/master/ReachabilitySample) disponível (que é baseado no código de [exemplo de acessibilidade](https://developer.apple.com/library/ios/#samplecode/Reachability/Introduction/Intro.html) da Apple) para ajudar a detectar a disponibilidade da rede.

## <a name="webservices"></a>webServices

Consulte nossa documentação sobre como [trabalhar com serviços Web](~/cross-platform/data-cloud/web-services/index.md), que aborda o acesso a pontos de extremidade REST, SOAP e WCF usando o Xamarin. Ios. É possível criar solicitações de serviço da Web manualmente e analisar as respostas, no entanto, há bibliotecas disponíveis para tornar isso muito mais simples, incluindo o Azure, o RestSharp e o perstack. Até mesmo as operações básicas do WCF podem ser acessadas em aplicativos Xamarin.

### <a name="azure"></a>Azure

O Microsoft Azure é uma plataforma de nuvem que fornece uma ampla variedade de serviços para aplicativos móveis, incluindo armazenamento de dados e sincronização e notificações por push.

Visite [Azure.Microsoft.com](https://azure.microsoft.com/) para testá-lo gratuitamente.

### <a name="restsharp"></a>RestSharp

O RestSharp é uma biblioteca .NET que pode ser incluída em aplicativos móveis para fornecer um cliente REST que simplifica o acesso aos serviços da Web. Ele ajuda a fornecer uma API simples para solicitar dados e analisar a resposta REST. RestSharp pode ser útil

O [site do RestSharp](http://restsharp.org/) contém [documentação](https://github.com/restsharp/RestSharp/wiki) sobre como implementar um cliente REST usando o RestSharp.
O RestSharp fornece exemplos de Xamarin. iOS e Xamarin. Android no [GitHub](https://github.com/restsharp/RestSharp/).

Também há um trecho de código do Xamarin. iOS em nossa [documentação de serviços Web](~/cross-platform/data-cloud/web-services/index.md).

 <a name="ServiceStack" />

### <a name="servicestack"></a>Perstack

Ao contrário de RestSharp, o perstack é uma solução do lado do servidor para hospedar um serviço Web, bem como uma biblioteca de cliente que pode ser implementada em aplicativos móveis para acessar esses serviços.

O [site do perstack](http://servicestack.net/) explica a finalidade do projeto e links para documentos e exemplos de código. Os exemplos incluem uma implementação completa do lado do servidor de um serviço Web, bem como vários aplicativos do lado do cliente que podem acessá-lo.

Há um [exemplo do Xamarin. Ios](http://www.servicestack.net/monotouch/remote-info/) no site do enstack e um trecho de código em nossa [documentação de serviços Web](~/cross-platform/data-cloud/web-services/index.md).

### <a name="wcf"></a>WCF

As ferramentas do Xamarin podem ajudá-lo a consumir alguns serviços de Windows Communication Foundation (WCF). Em geral, o Xamarin dá suporte ao mesmo subconjunto do WCF do lado do cliente que é fornecido com o tempo de execução do Silverlight. Isso inclui as implementações mais comuns de codificação e protocolo do WCF: mensagens SOAP codificadas por texto sobre o protocolo de transporte HTTP usando o `BasicHttpBinding`.

Devido ao tamanho e à complexidade da estrutura do WCF, pode haver implementações de serviço atuais e futuras que ficarão fora do escopo com suporte do domínio de subconjunto do cliente do Xamarin. Além disso, o suporte do WCF requer o uso de ferramentas disponíveis somente em um ambiente do Windows para gerar o proxy.

 <a name="Threading" />

## <a name="threading"></a>Threading

A capacidade de resposta do aplicativo é importante para aplicativos móveis – os usuários esperam que os aplicativos sejam carregados e executados rapidamente. Uma tela "congelada" que para de aceitar a entrada do usuário aparecerá para indicar que o aplicativo falhou, portanto, é importante não vincular o thread da interface do usuário com chamadas de bloqueio de execução longa, como solicitações de rede ou operações locais lentas (como descompactar um arquivo). Em particular, o processo de inicialização não deve conter tarefas de longa execução – todas as plataformas móveis eliminarão um aplicativo que leva muito tempo para carregar.

Isso significa que a sua interface do usuário deve implementar uma interface "do indicador de progresso" ou de outra forma "utilizável" que seja rápida de exibir e tarefas assíncronas para executar operações em segundo plano. A execução de tarefas em segundo plano requer o uso de threads, o que significa que as tarefas em segundo plano precisam de uma maneira de se comunicar de volta com o thread principal para indicar o progresso ou quando eles foram concluídos.

 <a name="Parallel_Task_Library" />

### <a name="parallel-task-library"></a>Biblioteca de tarefas paralelas

As tarefas criadas com a biblioteca de tarefas paralelas podem ser executadas de forma assíncrona e retornadas em seu thread de chamada, tornando-as muito úteis para disparar operações de longa execução sem bloquear a interface do usuário.

Uma operação de tarefa paralela simples pode ser parecida com esta:

```csharp
using System.Threading.Tasks;
void MainThreadMethod ()
{
    Task.Factory.StartNew (() => wc.DownloadString ("http://...")).ContinueWith (
        t => label.Text = t.Result, TaskScheduler.FromCurrentSynchronizationContext()
    );
}
```

A chave é `TaskScheduler.FromCurrentSynchronizationContext()` que reutilizará o SynchronizationContext. Current do thread que chama o método (aqui o thread principal que está executando `MainThreadMethod`) como uma maneira de realizar o marshaling de chamadas para esse thread. Isso significa que se o método for chamado no thread da interface do usuário, ele executará a operação de `ContinueWith` de volta no thread da interface do usuário.

Se o código estiver iniciando tarefas de outros threads, use o padrão a seguir para criar uma referência ao thread da interface do usuário e a tarefa ainda poderá retornar a ela:

```csharp
static Context uiContext = TaskScheduler.FromCurrentSynchronizationContext();
```

 <a name="Invoking_on_the_UI_Thread" />

### <a name="invoking-on-the-ui-thread"></a>Invocando no thread de interface do usuário

Para o código que não utiliza a biblioteca de tarefas paralelas, cada plataforma tem sua própria sintaxe para realizar o marshaling de operações de volta para o thread da interface do usuário:

- **Ios** – `owner.BeginInvokeOnMainThread(new NSAction(action))`
- **Android** – `owner.RunOnUiThread(action)`
- **Xamarin. Forms** – `Device.BeginInvokeOnMainThread(action)`
- **Windows** – `Deployment.Current.Dispatcher.BeginInvoke(action)`

A sintaxe do iOS e do Android requer que uma classe ' Context ' esteja disponível, o que significa que o código precisa passar esse objeto para quaisquer métodos que exijam um retorno de chamada no thread da interface do usuário.

Para fazer chamadas de thread de interface do usuário em código compartilhado, siga o [exemplo de IDispatchOnUIThread](https://www.slideshare.net/follesoe/cross-platform-mobile-apps-using-net) (cortesia de [@follesoe](https://twitter.com/follesoe)). Declare e programe para uma interface `IDispatchOnUIThread` no código compartilhado e, em seguida, implemente as classes específicas da plataforma, conforme mostrado aqui:

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

Os desenvolvedores do Xamarin. Forms devem usar [`Device.BeginInvokeOnMainThread`](~/xamarin-forms/platform/device.md#interact-with-the-ui-from-background-threads) em código comum (projetos compartilhados ou PCL).

 <a name="Platform_and_Device_Capabilities_and_Degradation" />

## <a name="platform-and-device-capabilities-and-degradation"></a>Recursos e degradação da plataforma e do dispositivo

Mais exemplos específicos de como lidar com recursos diferentes são fornecidos na documentação de recursos da plataforma. Ele lida com a detecção de diferentes recursos e como degradar um aplicativo de forma tranqüila para fornecer uma boa experiência do usuário, mesmo quando o aplicativo não puder operar com todo o potencial.
