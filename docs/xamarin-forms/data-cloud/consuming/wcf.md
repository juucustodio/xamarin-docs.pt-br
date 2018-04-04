---
title: Consumir um serviço Web do Windows Communication Foundation (WCF)
description: O WCF é a estrutura unificada da Microsoft para criar aplicativos orientados a serviços. Ele permite aos desenvolvedores compilar aplicativos distribuídos seguros, confiáveis, transacionados e interoperáveis. Este artigo demonstra como consumir um serviço WCF simples protocolo (SOAP) de um aplicativo xamarin. Forms.
ms.prod: xamarin
ms.assetid: 5696FF04-EF21-4B7A-8C8B-26DE28B5C0AD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: c626008012ccdab2f8ed2c719b34a45471598d47
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="consuming-a-windows-communication-foundation-wcf-web-service"></a>Consumir um serviço Web do Windows Communication Foundation (WCF)

_O WCF é a estrutura unificada da Microsoft para criar aplicativos orientados a serviços. Ele permite aos desenvolvedores compilar aplicativos distribuídos seguros, confiáveis, transacionados e interoperáveis. Este artigo demonstra como consumir um serviço WCF simples protocolo (SOAP) de um aplicativo xamarin. Forms._

WCF descreve um serviço com uma variedade de diferentes contratos que incluem o seguinte:

- **Contratos de dados** – defina as estruturas de dados que formam a base para o conteúdo dentro de uma mensagem.
- **Contratos de mensagem** – compor mensagens de contratos de dados existente.
- **Contratos de falha** – permitem que as falhas de SOAP personalizadas seja especificada.
- **Contratos de serviço** – especifique as operações que oferecem suporte a serviços e as mensagens necessárias para interagir com cada operação. Eles também pode especificar qualquer comportamento personalizado falhas que pode ser associado a operações em cada serviço.

Há diferenças entre os serviços de Web do ASP.NET (ASMX) e WCF, mas é importante entender que o WCF suporta os mesmos recursos que fornece ASMX – mensagens SOAP sobre HTTP. Para obter mais informações sobre como consumir um serviço ASMX, consulte [consumindo ASP.NET Web Services (ASMX)](~/xamarin-forms/data-cloud/consuming/asmx.md).

Em geral, a plataforma de Xamarin aceita o mesmo subconjunto de cliente do WCF que acompanha o runtime do Silverlight. Isso inclui as implementações de codificação e o protocolo mais comuns do WCF — texto codificado mensagens SOAP sobre HTTP usando o protocolo de transporte de `BasicHttpBinding` classe. Além disso, o suporte do WCF requer o uso de ferramentas disponíveis somente em um ambiente do Windows para gerar o proxy.

As instruções sobre como configurar o serviço WCF podem ser encontradas no arquivo Leiame que acompanha o aplicativo de exemplo. No entanto, quando o aplicativo de exemplo é executado ele conectará a um serviço WCF hospedado Xamarin que fornece acesso somente leitura aos dados, conforme mostrado na seguinte captura de tela:

![](wcf-images/portal.png "Aplicativo de exemplo")

> [!NOTE]
> No iOS 9 e superior, a segurança de transporte do aplicativo (ATS) impõe conexões seguras entre os recursos de internet (como o servidor de back-end do aplicativo) e o aplicativo, impedindo assim a divulgação acidental de informações confidenciais. Como ATS está habilitado por padrão em aplicativos criados para o iOS 9, todas as conexões serão sujeitos a requisitos de segurança ATS. Se as conexões não atender a esses requisitos, eles falham com uma exceção.
> ATS pode ser incluído em se não for possível usar o `HTTPS` de protocolo e proteger a comunicação para recursos da internet. Isso pode ser obtido pela atualização do aplicativo **Info. plist** arquivo. Para obter mais informações, consulte [segurança de transporte do aplicativo](~/ios/app-fundamentals/ats.md).

## <a name="consuming-the-web-service"></a>Consumindo o serviço Web

O serviço WCF fornece as seguintes operações:

|Operação|Descrição|Parâmetros|
|--- |--- |--- |
|GetTodoItems|Obter uma lista de itens pendentes|
|CreateTodoItem|Criar um novo item de tarefas pendentes|Um XML serializado TodoItem|
|EditTodoItem|Atualizar um item de tarefas pendentes|Um XML serializado TodoItem|
|DeleteTodoItem|Excluir um item de tarefas pendentes|Um XML serializado TodoItem|

Para obter mais informações sobre o modelo de dados usado no aplicativo, consulte [modelagem de dados](~/xamarin-forms/data-cloud/walkthrough.md).

> [!NOTE]
> O aplicativo de exemplo consome o serviço WCF hospedado Xamarin que fornece acesso somente leitura para o serviço web. Portanto, as operações que criar, atualizar e excluam dados não alterará os dados consumidos no aplicativo. No entanto, uma versão de núcleo do serviço ASMX está disponível na **TodoWCFService** pasta no aplicativo de exemplo que acompanha. Nesta versão de núcleo do total do WCF serviço permite criar, atualizar, ler e excluir o acesso aos dados.

Um *proxy* devem ser geradas para consumir um serviço WCF, que permite que o aplicativo se conecte ao serviço. O proxy é construído por consumo metadados de serviço que define os métodos e a configuração de serviço associado. Esses metadados são expostos na forma de um documento WSDL Web Services Description Language () que é gerada pelo serviço da web. O proxy pode ser criado usando o provedor de referência de serviço Web do Microsoft WCF no Visual Studio de 2017 para adicionar uma referência de serviço para o serviço web a uma biblioteca do .NET padrão. Uma alternativa para criar o proxy usando o provedor de referência de serviço Web do Microsoft WCF no Visual Studio de 2017 é usar a ferramenta de utilitário de metadados do ServiceModel (svcutil.exe). Para obter mais informações, consulte [Ferramenta Utilitária de metadados ServiceModel (Svcutil.exe)](/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe/).

As classes de proxy gerado fornecem métodos para consumir os serviços web que usam o padrão de design de modelo de programação assíncrona (APM). Nesse padrão, uma operação assíncrona é implementada como dois métodos chamados *BeginOperationName* e *EndOperationName*, que começam e terminam a operação assíncrona.

O *BeginOperationName* método inicia a operação assíncrona e retorna um objeto que implementa o `IAsyncResult` interface. Depois de chamar *BeginOperationName*, um aplicativo pode continuar a executar as instruções no thread de chamada, enquanto a operação assíncrona ocorre em um pool de threads.

Cada chamada para *BeginOperationName*, o aplicativo também deve chamar *EndOperationName* para obter os resultados da operação. O valor de retorno *EndOperationName* é do mesmo tipo retornado pelo método de serviço da web síncrono. Por exemplo, o `EndGetTodoItems` método retorna uma coleção de `TodoItem` instâncias. O *EndOperationName* método também inclui um `IAsyncResult` parâmetro deve ser definido para a instância retornada pela chamada correspondente para o *BeginOperationName* método.

O biblioteca paralela de tarefas (TPL) pode simplificar o processo de consumir um par de métodos begin/end APM encapsulando as operações assíncronas no mesmo `Task` objeto. Esse encapsulamento é fornecido por várias sobrecargas do `TaskFactory.FromAsync` método.

Para obter mais informações sobre o APM consulte [modelo de programação assíncrona](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) e [TPL e estrutura de programação assíncrona tradicional .NET](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) no MSDN.

### <a name="creating-the-todoserviceclient-object"></a>Criar o objeto TodoServiceClient

Fornece a classe proxy gerada a `TodoServiceClient` classe, que é usada para se comunicar com o serviço WCF via HTTP. Ele fornece funcionalidade para invocar métodos de serviço web, como operações assíncronas de um URI identificado instância de serviço. Para obter mais informações sobre as operações assíncronas, consulte [visão geral de suporte assíncrono](~/cross-platform/platform/async.md).

O `TodoServiceClient` instância é declarada no nível de classe para que o objeto reside para desde que o aplicativo precisa para consumir o serviço WCF, conforme mostrado no exemplo de código a seguir:

```csharp
public class SoapService : ISoapService
{
  ITodoService todoService;
  ...

  public SoapService ()
  {
    todoService = new TodoServiceClient (
      new BasicHttpBinding (),
      new EndpointAddress (Constants.SoapUrl));
  }
  ...
}
```

O `TodoServiceClient` instância é configurada com informações e um endereço de ponto de extremidade de associação. Uma associação é usada para especificar o transporte, a codificação e detalhes de protocolo necessárias para aplicativos e serviços para se comunicar entre si. O `BasicHttpBinding` Especifica que as mensagens SOAP texto codificado serão enviadas através do protocolo de transporte HTTP. Especificação de um endereço de ponto de extremidade permite que o aplicativo se conectar a diferentes instâncias do serviço WCF, desde que há várias instâncias publicadas.

Para obter mais informações sobre como configurar a referência de serviço, consulte [Configurando a referência de serviço](~/cross-platform/data-cloud/web-services/index.md#wcf).

### <a name="creating-data-transfer-objects"></a>Criando objetos de transferência de dados

O aplicativo de exemplo usa o `TodoItem` classe para dados de modelo. Para armazenar um `TodoItem` item no serviço da web, ele deve primeiro ser convertido para o proxy gerado `TodoItem` tipo. Isso é feito usando o `ToWCFServiceTodoItem` método, conforme mostrado no exemplo de código a seguir:

```csharp
TodoWCFService.TodoItem ToWCFServiceTodoItem (TodoItem item)
{
  return new TodoWCFService.TodoItem {
    ID = item.ID,
    Name = item.Name,
    Notes = item.Notes,
    Done = item.Done
  };
}
```

Este método simplesmente cria um novo `TodoWCFService.TodoItem` instância e define cada propriedade para a propriedade idêntica da `TodoItem` instância.

Da mesma forma, quando os dados são recuperados do serviço da web, ele deve ser convertido do proxy gerado `TodoItem` de tipo para um `TodoItem` instância. Isso é feito com o `FromWCFServiceTodoItem` método, conforme mostrado no exemplo de código a seguir:

```csharp
static TodoItem FromWCFServiceTodoItem (TodoWCFService.TodoItem item)
{
  return new TodoItem {
    ID = item.ID,
    Name = item.Name,
    Notes = item.Notes,
    Done = item.Done
  };
}

```

Este método simplesmente recupera os dados do proxy gerado `TodoItem` de tipo e define-a no recém-criado `TodoItem` instância.

### <a name="retrieving-data"></a>Recuperando dados

O `TodoServiceClient.BeginGetTodoItems` e `TodoServiceClient.EndGetTodoItems` métodos são usados para chamar o `GetTodoItems` operação fornecida pelo serviço da web. Esses métodos assíncronos são encapsulados em um `Task` do objeto, como mostrado no exemplo de código a seguir:

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync <ObservableCollection<TodoWCFService.TodoItem>> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);

  foreach (var item in todoItems) {
    Items.Add (FromWCFServiceTodoItem (item));
  }
  ...
}
```

O `Task.Factory.FromAsync` método cria um `Task` que executa o `TodoServiceClient.EndGetTodoItems` método uma vez o `TodoServiceClient.BeginGetTodoItems` método é concluído, com o `null` parâmetro indicando que nenhum dado está sendo passado para o `BeginGetTodoItems` delegar. Por fim, o valor da `TaskCreationOptions` enumeração Especifica que o comportamento padrão para a criação e execução de tarefas deve ser usado.

O `TodoServiceClient.EndGetTodoItems` método retorna um `ObservableCollection` de `TodoWCFService.TodoItem` instâncias que são convertidos em um `List` de `TodoItem` instâncias para exibição.

### <a name="creating-data"></a>Criação de dados

O `TodoServiceClient.BeginCreateTodoItem` e `TodoServiceClient.EndCreateTodoItem` métodos são usados para chamar o `CreateTodoItem` operação fornecida pelo serviço da web. Esses métodos assíncronos são encapsulados em um `Task` do objeto, como mostrado no exemplo de código a seguir:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  var todoItem = ToWCFServiceTodoItem (item);
  ...
  await Task.Factory.FromAsync (
    todoService.BeginCreateTodoItem,
    todoService.EndCreateTodoItem,
    todoItem,
    TaskCreationOptions.None);
  ...
}
```

O `Task.Factory.FromAsync` método cria um `Task` que executa o `TodoServiceClient.EndCreateTodoItem` método uma vez o `TodoServiceClient.BeginCreateTodoItem` método é concluído, com o `todoItem` parâmetro sendo os dados que são passados para o `BeginCreateTodoItem` delegado para especificar o `TodoItem` a ser criado pelo serviço da web. Por fim, o valor da `TaskCreationOptions` enumeração Especifica que o comportamento padrão para a criação e execução de tarefas deve ser usado.

Da web service gera um `FaultException` se ele falhar ao criar o `TodoItem`, que é tratado pelo aplicativo.

### <a name="updating-data"></a>Atualizando dados

O `TodoServiceClient.BeginEditTodoItem` e `TodoServiceClient.EndEditTodoItem` métodos são usados para chamar o `EditTodoItem` operação fornecida pelo serviço da web. Esses métodos assíncronos são encapsulados em um `Task` do objeto, como mostrado no exemplo de código a seguir:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  var todoItem = ToWCFServiceTodoItem (item);
  ...
  await Task.Factory.FromAsync (
    todoService.BeginEditTodoItem,
    todoService.EndEditTodoItem,
    todoItem,
    TaskCreationOptions.None);
  ...
}
```

O `Task.Factory.FromAsync` método cria um `Task` que executa o `TodoServiceClient.EndEditTodoItem` método uma vez o `TodoServiceClient.BeginCreateTodoItem` método é concluído, com o `todoItem` parâmetro sendo os dados que são passados para o `BeginEditTodoItem` delegado para especificar o `TodoItem` a ser atualizado pelo serviço da web. Por fim, o valor da `TaskCreationOptions` enumeração Especifica que o comportamento padrão para a criação e execução de tarefas deve ser usado.

Da web service gera um `FaultException` caso não consiga localizar ou atualizar o `TodoItem`, que é tratado pelo aplicativo.

### <a name="deleting-data"></a>Excluindo dados

O `TodoServiceClient.BeginDeleteTodoItem` e `TodoServiceClient.EndDeleteTodoItem` métodos são usados para chamar o `DeleteTodoItem` operação fornecida pelo serviço da web. Esses métodos assíncronos são encapsulados em um `Task` do objeto, como mostrado no exemplo de código a seguir:

```csharp
public async Task DeleteTodoItemAsync (string id)
{
  ...
  await Task.Factory.FromAsync (
    todoService.BeginDeleteTodoItem,
    todoService.EndDeleteTodoItem,
    id,
    TaskCreationOptions.None);
  ...
}
```

O `Task.Factory.FromAsync` método cria um `Task` que executa o `TodoServiceClient.EndDeleteTodoItem` método uma vez o `TodoServiceClient.BeginDeleteTodoItem` método é concluído, com o `id` parâmetro sendo os dados que são passados para o `BeginDeleteTodoItem` delegado para especificar o `TodoItem` a ser excluído pelo serviço da web. Por fim, o valor da `TaskCreationOptions` enumeração Especifica que o comportamento padrão para a criação e execução de tarefas deve ser usado.

Da web service gera um `FaultException` caso não consiga localizar ou excluir o `TodoItem`, que é tratado pelo aplicativo.

## <a name="summary"></a>Resumo

Este artigo demonstrou como consumir um serviço WCF SOAP de um aplicativo xamarin. Forms. Em geral, a plataforma de Xamarin aceita o mesmo subconjunto de cliente do WCF que acompanha o runtime do Silverlight. Isso inclui as implementações de codificação e o protocolo mais comuns do WCF — texto codificado mensagens SOAP sobre HTTP usando o protocolo de transporte de `BasicHttpBinding` classe. Além disso, o suporte do WCF requer o uso de ferramentas disponíveis somente em um ambiente do Windows para gerar o proxy.


## <a name="related-links"></a>Links relacionados

- [TodoWCF (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoWCF/)
- [IAsyncResult](https://msdn.microsoft.com/library/system.iasyncresult(v=vs.110).aspx)
