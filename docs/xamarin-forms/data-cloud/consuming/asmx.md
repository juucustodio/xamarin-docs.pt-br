---
title: Consumir um serviço Web do ASP.NET (ASMX)
description: Este artigo demonstra como consumir um serviço ASMX SOAP de um aplicativo xamarin. Forms.
ms.prod: xamarin
ms.assetid: D5533964-5528-4D35-9C2B-FAFB632472AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 6ec8168a8da64dbf3dfeb805856a4d91c9ec78ca
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35242057"
---
# <a name="consuming-an-aspnet-web-service-asmx"></a>Consumir um serviço Web do ASP.NET (ASMX)

_ASMX fornece a capacidade de criar serviços web que enviam mensagens usando o protocolo de acesso de objeto simples (SOAP). SOAP é um protocolo independente de plataforma e independente de linguagem para criar e acessar serviços da web. Não é necessário que os consumidores de um serviço ASMX sabe nada sobre a plataforma, um modelo de objeto ou uma linguagem de programação usada para implementar o serviço. Eles só precisam entender como enviar e receber mensagens SOAP. Este artigo demonstra como consumir um serviço ASMX SOAP de um aplicativo xamarin. Forms._

Uma mensagem SOAP é um documento XML que contém os seguintes elementos:

- Um elemento raiz chamado *Envelope* que identifica o documento XML como uma mensagem SOAP.
- Um recurso opcional *cabeçalho* elemento que contém informações específicas do aplicativo, como dados de autenticação. Se o *cabeçalho* estiver presente ele deve ser o primeiro elemento filho do *Envelope* elemento.
- Necessário *corpo* elemento que contém a mensagem SOAP destinada para o destinatário.
- Um recurso opcional *falha* elemento que é usado para indicar as mensagens de erro. Se o *falha* estiver presente, ele deve ser um elemento filho do *corpo* elemento.

SOAP pode operar em vários protocolos de transporte, incluindo HTTP, SMTP, TCP e UDP. No entanto, um serviço ASMX só pode operar em HTTP. A plataforma Xamarin dá suporte a implementações padrão de SOAP 1.1 em HTTP, e isso inclui suporte para muitas das configurações de serviço ASMX padrão.

As instruções sobre como configurar o serviço ASMX podem ser encontradas no arquivo Leiame que acompanha o aplicativo de exemplo. No entanto, quando o aplicativo de exemplo é executado, ele se conectará a um serviço hospedado Xamarin ASMX que fornece acesso somente leitura aos dados, conforme mostrado na seguinte captura de tela:

![](asmx-images/portal.png "Aplicativo de exemplo")

> [!NOTE]
> No iOS 9 e superior, a segurança de transporte do aplicativo (ATS) impõe conexões seguras entre os recursos de internet (como o servidor de back-end do aplicativo) e o aplicativo, impedindo assim a divulgação acidental de informações confidenciais. Como ATS está habilitado por padrão em aplicativos criados para o iOS 9, todas as conexões serão sujeitos a requisitos de segurança ATS. Se as conexões não atender a esses requisitos, eles falham com uma exceção.
> ATS pode ser incluído em se não for possível usar o `HTTPS` de protocolo e proteger a comunicação para recursos da internet. Isso pode ser obtido pela atualização do aplicativo **Info. plist** arquivo. Para obter mais informações, consulte [segurança de transporte do aplicativo](~/ios/app-fundamentals/ats.md).

## <a name="consuming-the-web-service"></a>Consumindo o serviço Web

O serviço ASMX fornece as seguintes operações:

|Operação|Descrição|Parâmetros|
|--- |--- |--- |
|GetTodoItems|Obter uma lista de itens pendentes|
|CreateTodoItem|Criar um novo item de tarefas pendentes|Um XML serializado TodoItem|
|EditTodoItem|Atualizar um item pendente|Um XML serializado TodoItem|
|DeleteTodoItem|Excluir um item pendente|Um XML serializado TodoItem|

Para obter mais informações sobre o modelo de dados usado no aplicativo, consulte [modelagem de dados](~/xamarin-forms/data-cloud/walkthrough.md).

> [!NOTE]
> O aplicativo de exemplo consome o serviço hospedado Xamarin ASMX que fornece acesso somente leitura para o serviço web. Portanto, as operações que criar, atualizar e excluam dados não alterará os dados consumidos no aplicativo. No entanto, uma versão de núcleo do serviço ASMX está disponível na **TodoASMXService** pasta no aplicativo de exemplo que acompanha. Esta versão de núcleo do total do ASMX serviço permite criar, atualizar, ler e excluir o acesso aos dados.

Um *proxy* devem ser geradas para consumir o serviço ASMX, que permite que o aplicativo se conecte ao serviço. O proxy é construído por consumo metadados de serviço que define os métodos e a configuração de serviço associado. Esses metadados são expostos na forma de um documento WSDL Web Services Description Language () que é gerada pelo serviço da web. O proxy é criado adicionando uma referência da web para o serviço web para os projetos de plataforma específica.

As classes de proxy gerado fornecem métodos para consumir o serviço web que usam o padrão de design de modelo de programação assíncrona (APM). Nesse padrão de uma operação assíncrona é implementada como dois métodos chamados *BeginOperationName* e *EndOperationName*, que começam e terminam a operação assíncrona.

O *BeginOperationName* método inicia a operação assíncrona e retorna um objeto que implementa o `IAsyncResult` interface. Depois de chamar *BeginOperationName*, um aplicativo pode continuar a executar as instruções no thread de chamada, enquanto a operação assíncrona ocorre em um pool de threads.

Cada chamada para *BeginOperationName*, o aplicativo também deve chamar *EndOperationName* para obter os resultados da operação. O valor de retorno *EndOperationName* é do mesmo tipo retornado pelo método de serviço da web síncrono. Por exemplo, o `EndGetTodoItems` método retorna uma coleção de `TodoItem` instâncias. O *EndOperationName* método também inclui um `IAsyncResult` parâmetro deve ser definido para a instância retornada pela chamada correspondente para o *BeginOperationName* método.

O biblioteca paralela de tarefas (TPL) pode simplificar o processo de consumir um par de métodos begin/end APM encapsulando as operações assíncronas no mesmo `Task` objeto. Esse encapsulamento é fornecido por várias sobrecargas do `TaskFactory.FromAsync` método.

Para obter mais informações sobre o APM consulte [modelo de programação assíncrona](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) e [TPL e estrutura de programação assíncrona tradicional .NET](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) no MSDN.

### <a name="creating-the-todoservice-object"></a>Criar o objeto TodoService

Fornece a classe proxy gerada a `TodoService` classe, que é usada para se comunicar com o serviço ASMX via HTTP. Ele fornece funcionalidade para invocar métodos de serviço web, como operações assíncronas de um URI identificado instância de serviço. Para obter mais informações sobre as operações assíncronas, consulte [visão geral de suporte assíncrono](~/cross-platform/platform/async.md).

O `TodoService` instância é declarada no nível de classe para que o objeto reside para desde que o aplicativo precisa para consumir o serviço ASMX, conforme mostrado no exemplo de código a seguir:

```csharp
public class SoapService : ISoapService
{
  ASMXService.TodoService asmxService;
  ...

  public SoapService ()
  {
    asmxService = new ASMXService.TodoService (Constants.SoapUrl);
  }
  ...
}
```

O `TodoService` construtor aceita um parâmetro de cadeia de caracteres opcional que especifica a URL da instância do serviço ASMX. Isso permite que o aplicativo se conectar a diferentes instâncias do serviço ASMX, desde que há várias instâncias publicadas.

### <a name="creating-data-transfer-objects"></a>Criando objetos de transferência de dados

O aplicativo de exemplo usa o `TodoItem` classe para dados de modelo. Para armazenar um `TodoItem` item no serviço da web, ele deve primeiro ser convertido para o proxy gerado `TodoItem` tipo. Isso é feito usando o `ToASMXServiceTodoItem` método, conforme mostrado no exemplo de código a seguir:

```csharp
ASMXService.TodoItem ToASMXServiceTodoItem (TodoItem item)
{
  return new ASMXService.TodoItem {
    ID = item.ID,
    Name = item.Name,
    Notes = item.Notes,
    Done = item.Done
  };
}
```

Este método simplesmente cria um novo `ASMService.TodoItem` instância e define cada propriedade para a propriedade idêntica da `TodoItem` instância.

Da mesma forma, quando os dados são recuperados do serviço da web, ele deve ser convertido do proxy gerado `TodoItem` de tipo para um `TodoItem` instância. Isso é feito com o `FromASMXServiceTodoItem` método, conforme mostrado no exemplo de código a seguir:

```csharp
static TodoItem FromASMXServiceTodoItem (ASMXService.TodoItem item)
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

O `TodoService.BeginGetTodoItems` e `TodoService.EndGetTodoItems` métodos são usados para chamar o `GetTodoItems` operação fornecida pelo serviço da web. Esses métodos assíncronos são encapsulados em um `Task` do objeto, como mostrado no exemplo de código a seguir:

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync<ASMXService.TodoItem[]> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);

  foreach (var item in todoItems) {
    Items.Add (FromASMXServiceTodoItem (item));
  }
  ...
}
```

O `Task.Factory.FromAsync` método cria um `Task` que executa o `TodoService.EndGetTodoItems` método uma vez o `TodoService.BeginGetTodoItems` método é concluído, com o `null` parâmetro indicando que nenhum dado está sendo passado para o `BeginGetTodoItems` delegar. Por fim, o valor da `TaskCreationOptions` enumeração Especifica que o comportamento padrão para a criação e execução de tarefas deve ser usado.

O `TodoService.EndGetTodoItems` método retorna uma matriz de `ASMXService.TodoItem` instâncias que são convertidos em um `List` de `TodoItem` instâncias para exibição.

### <a name="creating-data"></a>Criação de dados

O `TodoService.BeginCreateTodoItem` e `TodoService.EndCreateTodoItem` métodos são usados para chamar o `CreateTodoItem` operação fornecida pelo serviço da web. Esses métodos assíncronos são encapsulados em um `Task` do objeto, como mostrado no exemplo de código a seguir:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  var todoItem = ToASMXServiceTodoItem (item);
  ...
  await Task.Factory.FromAsync (
    todoService.BeginCreateTodoItem,
    todoService.EndCreateTodoItem,
    todoItem,
    TaskCreationOptions.None);
  ...
}
```

O `Task.Factory.FromAsync` método cria um `Task` que executa o `TodoService.EndCreateTodoItem` método uma vez o `TodoService.BeginCreateTodoItem` método é concluído, com o `todoItem` parâmetro sendo os dados que são passados para o `BeginCreateTodoItem` delegado para especificar o `TodoItem` a ser criado pelo serviço da web. Por fim, o valor da `TaskCreationOptions` enumeração Especifica que o comportamento padrão para a criação e execução de tarefas deve ser usado.

Da web service gera um `SoapException` se ele falhar ao criar o `TodoItem`, que é tratado pelo aplicativo.

### <a name="updating-data"></a>Atualizando dados

O `TodoService.BeginEditTodoItem` e `TodoService.EndEditTodoItem` métodos são usados para chamar o `EditTodoItem` operação fornecida pelo serviço da web. Esses métodos assíncronos são encapsulados em um `Task` do objeto, como mostrado no exemplo de código a seguir:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  var todoItem = ToASMXServiceTodoItem (item);
  ...
  await Task.Factory.FromAsync (
    todoService.BeginEditTodoItem,
    todoService.EndEditTodoItem,
    todoItem,
    TaskCreationOptions.None);
  ...
}
```

O `Task.Factory.FromAsync` método cria um `Task` que executa o `TodoService.EndEditTodoItem` método uma vez o `TodoService.BeginCreateTodoItem` método é concluído, com o `todoItem` parâmetro sendo os dados que são passados para o `BeginEditTodoItem` delegado para especificar o `TodoItem` a ser atualizado pelo serviço da web. Por fim, o valor da `TaskCreationOptions` enumeração Especifica que o comportamento padrão para a criação e execução de tarefas deve ser usado.

Da web service gera um `SoapException` caso não consiga localizar ou atualizar o `TodoItem`, que é tratado pelo aplicativo.

### <a name="deleting-data"></a>Excluindo dados

O `TodoService.BeginDeleteTodoItem` e `TodoService.EndDeleteTodoItem` métodos são usados para chamar o `DeleteTodoItem` operação fornecida pelo serviço da web. Esses métodos assíncronos são encapsulados em um `Task` do objeto, como mostrado no exemplo de código a seguir:

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

O `Task.Factory.FromAsync` método cria um `Task` que executa o `TodoService.EndDeleteTodoItem` método uma vez o `TodoService.BeginDeleteTodoItem` método é concluído, com o `id` parâmetro sendo os dados que são passados para o `BeginDeleteTodoItem` delegado para especificar o `TodoItem` a ser excluído pelo serviço da web. Por fim, o valor da `TaskCreationOptions` enumeração Especifica que o comportamento padrão para a criação e execução de tarefas deve ser usado.

Da web service gera um `SoapException` caso não consiga localizar ou excluir o `TodoItem`, que é tratado pelo aplicativo.

## <a name="summary"></a>Resumo

Este artigo demonstrou como consumir um serviço da web ASMX de um aplicativo xamarin. Forms. ASMX fornece a capacidade de criar serviços web que enviam mensagens via HTTP usando SOAP. Não é necessário que os consumidores de um serviço ASMX sabe nada sobre a plataforma, um modelo de objeto ou uma linguagem de programação usada para implementar o serviço. Eles só precisam entender como enviar e receber mensagens SOAP.


## <a name="related-links"></a>Links relacionados

- [TodoASMX (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoASMX/)
- [IAsyncResult](https://msdn.microsoft.com/library/system.iasyncresult(v=vs.110).aspx)
