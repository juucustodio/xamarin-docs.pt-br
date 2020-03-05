---
title: Parte 5 – Estratégias práticas de compartilhamento de código
description: Este documento discute estratégias de cenários, como bancos de dados, acesso a arquivos, operações de rede e o código assíncrono de compartilhamento de código prático.
ms.prod: xamarin
ms.assetid: 328D042A-FF78-A7B6-1574-B5AF49A1AADB
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 0e37e138607fb0e00fbdc463ac7c53facf81395d
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292775"
---
# <a name="part-5---practical-code-sharing-strategies"></a>Parte 5 – Estratégias práticas de compartilhamento de código

Esta seção fornece exemplos de como compartilhar o código para cenários comuns de aplicativo.

## <a name="data-layer"></a>Camada de dados

A camada de dados consiste em um mecanismo de armazenamento e métodos para ler e gravar informações. Para fins de compatibilidade de plataforma cruzada, flexibilidade e desempenho do SQLite, o mecanismo de banco de dados é recomendado para aplicativos de plataforma cruzada do Xamarin.
Ele é executado em uma ampla variedade de plataformas, incluindo Windows, Android, iOS e Mac.

### <a name="sqlite"></a>SQLite

O SQLite é uma implementação de banco de dados do código-fonte aberto. A origem e a documentação podem ser encontradas em [SQLite.org](https://www.sqlite.org/). O suporte do SQLite está disponível em cada plataforma móvel:

- **Ios** – interno ao sistema operacional.
- **Android** – integrado ao sistema operacional desde o Android 2,2 (nível de API 10).
- **Windows** – consulte a [extensão do SQLite para plataforma universal do Windows](https://visualstudiogallery.msdn.microsoft.com/4913e7d5-96c9-4dde-a1a1-69820d615936).

Mesmo com o mecanismo de banco de dados disponível em todas as plataformas, os métodos nativos para acessar o banco de dados são diferentes. Tanto iOS e Android oferecem APIs internas para acessar o SQLite que poderia ser usado no xamarin. IOS ou xamarin. Android, no entanto, usar os métodos nativos do SDK oferece sem capacidade de compartilhamento de código (que não seja talvez as consultas SQL em si, supondo que eles são armazenados como cadeias de caracteres) . Para obter detalhes sobre a funcionalidade de banco de dados nativa, pesquise `CoreData` na classe `SQLiteOpenHelper` do iOS ou Android; como essas opções não são de plataforma cruzada, elas estão além do escopo deste documento.

### <a name="adonet"></a>ADO.NET

O Xamarin. iOS e o Xamarin. Android dão suporte a `System.Data` e `Mono.Data.Sqlite` (consulte a [documentação](~/ios/data-cloud/system.data.md) do Xamarin. Ios para obter mais informações).
Esses namespaces permite que você escreva código ADO.NET que funciona em ambas as plataformas. Edite as referências do projeto para incluir `System.Data.dll` e `Mono.Data.Sqlite.dll` e adicione-as usando instruções ao seu código:

```csharp
using System.Data;
using Mono.Data.Sqlite;
```

Em seguida, o código de exemplo a seguir funcionará:

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

As implementações reais do ADO.NET, obviamente, seriam divididas em diferentes métodos e classes (neste exemplo é para fins de demonstração).

### <a name="sqlite-net--cross-platform-orm"></a>SQLite-NET – ORM de plataforma cruzada

Tentativas de um ORM (ou um mapeador relacional de objeto) simplificar o armazenamento de dados modelados de classes. Em vez de manualmente escrever consultas SQL que criar tabelas ou SELECT, INSERT e DELETE dados extraídos da manualmente campos de classe e propriedades, um ORM adiciona uma camada de código que faz isso para você. Usando a reflexão para examinar a estrutura de suas classes, um ORM automaticamente pode criar tabelas e colunas que correspondem a uma classe e geram consultas para ler e gravar os dados. Isso permite que o código do aplicativo simplesmente enviar e recuperar instâncias de objeto para o ORM, que cuida de todas as operações de SQL nos bastidores.

SQLite-NET atua como um simples ORM que permitirá que você salvar e recuperar suas classes no SQLite. Ele oculta a complexidade de cruzada plataforma SQLite acesso com uma combinação de diretivas de compilador e outros truques.

Recursos do NET SQLite:

- Tabelas são definidas pela adição de atributos para classes de modelo.
- Uma instância de banco de dados é representada por uma subclasse de `SQLiteConnection`, a classe principal na biblioteca SQLite-net.
- Dados podem ser inseridos, consultado e excluídas usando objetos. Nenhuma instrução SQL é necessárias (embora você pode escrever instruções SQL, se necessário).
- Consultas básicas de Linq podem ser executadas nas coleções retornadas pelo SQLite-NET.

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

Consulte o estudo de caso de código-fonte para obter exemplos completos.

## <a name="file-access"></a>Acesso a arquivos

Acesso a arquivo é determinado ser uma parte essencial de qualquer aplicativo. Exemplos comuns de arquivos que podem fazer parte de uma aplicativo incluem:

- Arquivos de banco de dados SQLite.
- Dados gerados pelo usuário (texto, imagens, som, vídeo).
- Dados baixados para armazenar em cache (imagens, html ou arquivos PDF).

### <a name="systemio-direct-access"></a>Acesso direto de System.IO

O Xamarin. iOS e o Xamarin. Android permitem o acesso ao sistema de arquivos usando classes no namespace `System.IO`.

Cada plataforma tem restrições de acesso diferentes que devem ser levadas em consideração:

- executar aplicativos iOS em uma área restrita com acesso de sistema de arquivos muito restrito. Apple ainda mais determina como você deve usar o sistema de arquivos especificando determinados locais que são submetidos a backup (e outros que não são). Consulte o guia [trabalhando com o sistema de arquivos no Xamarin. Ios](~/ios/app-fundamentals/file-system.md) para obter mais detalhes.
- Android também restringe o acesso a determinados diretórios relacionados ao aplicativo, mas ele também dá suporte a mídia externa (por exemplo. Cartões SD) e acessar dados compartilhados.
- O Windows Phone 8 (Silverlight) não permite acesso direto a arquivos – os arquivos só podem ser manipulados usando `IsolatedStorage`.
- Os projetos UWP Windows 8.1 WinRT e Windows 10 oferecem apenas operações de arquivo assíncrono por meio de APIs `Windows.Storage`, que são diferentes das outras plataformas.

#### <a name="example-for-ios-and-android"></a>Exemplo de iOS e Android

Abaixo está um exemplo trivial que gravações e leituras de um arquivo de texto.
O uso de `Environment.GetFolderPath` permite que o mesmo código seja executado no iOS e no Android, que retornam um diretório válido com base em suas convenções de sistema de arquivos.

```csharp
string filePath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "MyFile.txt");
System.IO.File.WriteAllText (filePath, "Contents of text file");
Console.WriteLine (System.IO.File.ReadAllText (filePath));
```

Consulte o Xamarin. iOS [trabalhando com o documento do sistema de arquivos](~/ios/app-fundamentals/file-system.md) para obter mais informações sobre a funcionalidade de sistema de arquivos específica do Ios. Ao escrever o código de acesso do arquivo de plataforma cruzada, lembre-se de que alguns sistemas de arquivo diferenciam maiusculas de minúsculas e tem separadores de diretório diferente. É recomendável sempre usar o mesmo uso de maiúsculas e minúsculas para nomes de arquivos e o método `Path.Combine()` ao construir caminhos de arquivo ou diretório.

### <a name="windowsstorage-for-windows-8-and-windows-10"></a>Storage do Windows 8 e Windows 10

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

### <a name="isolated-storage-on-windows-phone-7--8-silverlight"></a>Armazenamento isolado no Windows Phone 7 e 8 (Silverlight)

Armazenamento isolado é uma API comum para salvar e carregar arquivos em todas as plataformas mais antigas do Windows Phone, Android e iOS.

É o mecanismo padrão para acesso a arquivos no Windows Phone (Silverlight) que foi implementado no xamarin. IOS e xamarin. Android para permitir que o código de acesso a arquivos comuns a serem gravados. A classe `System.IO.IsolatedStorage` pode ser referenciada em todas as três plataformas em um [projeto compartilhado](~/cross-platform/app-fundamentals/shared-projects.md).

Consulte [visão geral do armazenamento isolado para obter Windows Phone](https://msdn.microsoft.com/library/windowsphone/develop/ff402541(v=vs.105).aspx) para obter mais informações.

As APIs de armazenamento isolado não estão disponíveis em [bibliotecas de classes portáteis](~/cross-platform/app-fundamentals/pcl.md). Uma alternativa para o PCL é o [NuGet do PCLStorage](https://pclstorage.codeplex.com/)

### <a name="cross-platform-file-access-in-pcls"></a>Acesso a arquivos de plataforma cruzada em PCLs

Também há um NuGet- [PCLStorage](https://www.nuget.org/packages/PCLStorage/) compatível com PCL – que orienta o acesso a arquivos entre plataformas para plataformas com suporte para Xamarin e as APIs do Windows mais recentes.

## <a name="network-operations"></a>Operações de rede

Maioria dos aplicativos móveis terá o componente de rede, por exemplo:

- Download de imagens, áudio e vídeos (por exemplo. miniaturas, fotos, música).
- Baixando documentos (por exemplo. HTML, PDF).
- Carregando dados do usuário (por exemplo, fotos ou texto).
- Acessando serviços web ou por terceiros 3ª APIs (incluindo SOAP, XML ou JSON).

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

Dispositivos móveis operam sob uma variedade de condições de rede da Wi-Fi rápida ou conexões de 4g para áreas de recepção de má qualidade e links lentos de dados de borda. Por isso, é uma boa prática para detectar se a rede está disponível e se assim, que tipo de rede estiver disponível, antes de tentar se conectar a servidores remotos.

Ações que um aplicativo móvel pode levar essas situações incluem:

- Se a rede estiver disponível, avise o usuário. Se eles tem desabilitado-lo manualmente (por exemplo. Modo avião ou desativando Wi-Fi), em seguida, eles podem resolver o problema.
- Se a conexão 3G, aplicativos podem ter um comportamento diferente (por exemplo, Apple não permite aplicativos maiores que 20Mb a ser baixado mais de 3G). Aplicativos poderiam usar essas informações para avisar o usuário sobre download excessiva vezes ao recuperar arquivos grandes.
- Mesmo se a rede estiver disponível, é uma boa prática para verificar a conectividade com o servidor de destino antes de iniciar outras solicitações. Isso impedirá a operações de rede do aplicativo atinjam o tempo limite repetidamente e também permitem que uma mensagem de erro mais informativa a ser exibida ao usuário.

## <a name="webservices"></a>Serviços Web

Consulte nossa documentação sobre como [trabalhar com serviços Web](~/cross-platform/data-cloud/web-services/index.md), que aborda o acesso a pontos de extremidade REST, SOAP e WCF usando o Xamarin. Ios. É possível a criação manual da web para atender às solicitações e analisar as respostas, no entanto, há bibliotecas disponíveis para tornar isso muito mais simples, incluindo Azure, RestSharp e ServiceStack. Até mesmo básicas operações de WCF podem ser acessadas em aplicativos Xamarin.

### <a name="azure"></a>Azure

Microsoft Azure é uma plataforma de nuvem que fornece uma ampla variedade de serviços para aplicativos móveis, incluindo notificações por push e sincronização e armazenamento de dados.

Visite [Azure.Microsoft.com](https://azure.microsoft.com/) para testá-lo gratuitamente.

### <a name="restsharp"></a>RestSharp

RestSharp é uma biblioteca .NET que pode ser incluída em aplicativos móveis para fornecer um cliente REST que simplifica o acesso a serviços da web. Ele ajuda, fornecendo uma API simples para solicitar dados e analisar a resposta REST. RestSharp pode ser útil

O [site do RestSharp](http://restsharp.org/) contém [documentação](https://github.com/restsharp/RestSharp/wiki) sobre como implementar um cliente REST usando o RestSharp.
O RestSharp fornece exemplos de Xamarin. iOS e Xamarin. Android no [GitHub](https://github.com/restsharp/RestSharp/).

Também há um trecho de código do Xamarin. iOS em nossa [documentação de serviços Web](~/cross-platform/data-cloud/web-services/index.md).

 <a name="ServiceStack" />

### <a name="servicestack"></a>ServiceStack

Ao contrário de RestSharp, o ServiceStack é uma solução de servidor para hospedar um serviço web, bem como uma biblioteca de cliente que pode ser implementada em aplicativos móveis para acessar esses serviços.

O [site do perstack](http://servicestack.net/) explica a finalidade do projeto e links para documentos e exemplos de código. Os exemplos incluem uma implementação completa do lado do servidor de um serviço web, bem como vários aplicativos do lado do cliente que podem acessá-lo.

### <a name="wcf"></a>WCF

Ferramentas do Xamarin podem ajudá-lo a consumir alguns serviços do Windows Communication Foundation (WCF). Em geral, o Xamarin dá suporte ao mesmo subconjunto de lado do cliente do WCF é fornecido com o runtime do Silverlight. Isso inclui as implementações mais comuns de codificação e protocolo do WCF: mensagens SOAP codificadas por texto sobre o protocolo de transporte HTTP usando o `BasicHttpBinding`.

Devido ao tamanho e da complexidade da estrutura do WCF, pode haver implementações de serviço atuais e futuros que estiverem fora do escopo com suporte pelo domínio do subconjunto de cliente do Xamarin. Além disso, o suporte do WCF requer o uso das ferramentas disponíveis somente em um ambiente do Windows para gerar o proxy.

 <a name="Threading" />

## <a name="threading"></a>Threading

Capacidade de resposta do aplicativo é importante para aplicativos móveis – os usuários esperam aplicativos para carregar e executar rapidamente. Uma tela 'congelada' que para de aceitar a entrada do usuário será exibido indicar que o aplicativo falhou, portanto, é importante não bloqueia o thread de interface do usuário com chamadas de bloqueio de execução longa, como solicitações de rede ou de operações lentas de locais (como descompactar um arquivo). Em particular, o processo de inicialização não deve conter tarefas de longa execução – todas as plataformas móveis finalizará a um aplicativo que demora muito para carregar.

Isso significa que sua interface do usuário deve implementar um indicador de progresso' ' ou 'utilizável' caso contrário, interface do usuário que é rápido exibir e tarefas assíncronas para executar operações em segundo plano. Executar tarefas em segundo plano requer o uso de threads, que significa que as necessidades de tarefas em segundo plano uma maneira de se comunicar com o thread principal para indicar o progresso ou quando eles foram concluídos.

 <a name="Parallel_Task_Library" />

### <a name="parallel-task-library"></a>Biblioteca de tarefas paralelas

As tarefas criadas com a biblioteca paralela de tarefas podem executar de forma assíncrona e retornar em seu thread de chamada, tornando-os muito útil para disparar operações demoradas sem bloquear a interface do usuário.

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

A chave é `TaskScheduler.FromCurrentSynchronizationContext()` que reutilizará o SynchronizationContext. Current do thread que chama o método (aqui o thread principal que está executando `MainThreadMethod`) como uma maneira de realizar o marshaling de chamadas para esse thread. Isso significa que se o método for chamado no thread da interface do usuário, ele executará a operação de `ContinueWith` de volta no thread da interface do usuário.

Se o código está iniciando as tarefas de outros threads, use o seguinte padrão para criar uma referência para o thread de interface do usuário e a tarefa ainda pode chamar de volta a ele:

```csharp
static Context uiContext = TaskScheduler.FromCurrentSynchronizationContext();
```

 <a name="Invoking_on_the_UI_Thread" />

### <a name="invoking-on-the-ui-thread"></a>Invocar no Thread da interface do usuário

Para o código que não utilizam a biblioteca paralela de tarefas, cada plataforma tem sua própria sintaxe para operações de marshaling para o thread de interface do usuário:

- **Ios** – `owner.BeginInvokeOnMainThread(new NSAction(action))`
- **Android** – `owner.RunOnUiThread(action)`
- **Xamarin. Forms** – `Device.BeginInvokeOnMainThread(action)`
- **Windows** – `Deployment.Current.Dispatcher.BeginInvoke(action)`

IOS e Android sintaxe requer uma classe de 'context' esteja disponível, que significa que o código precisa passar esse objeto para todos os métodos que exigem um retorno de chamada no thread da interface do usuário.

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

## <a name="platform-and-device-capabilities-and-degradation"></a>Plataforma e recursos do dispositivo e degradação

Mais exemplos específicos de lidar com diferentes capacidades são fornecidos na documentação de recursos da plataforma. Ele lida com a detecção de recursos diferentes e como reduzir gradualmente um aplicativo para fornecer uma boa experiência do usuário, mesmo quando o aplicativo não pode operar para todo o seu potencial.
