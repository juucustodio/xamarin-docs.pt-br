---
title: Consumir um serviço Web do ASP.NET (ASMX)
description: Este artigo demonstra como consumir um serviço ASMX SOAP de um aplicativo xamarin. Forms.
ms.prod: xamarin
ms.assetid: D5533964-5528-4D35-9C2B-FAFB632472AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/02/2019
ms.openlocfilehash: 71fb2b4742a66a560541febc9dbe87aed503da4c
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59690271"
---
# <a name="consuming-an-aspnet-web-service-asmx"></a>Consumir um serviço Web ASP.NET (ASMX)

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoASMX/)

_ASMX fornece a capacidade de criar serviços web que enviam mensagens usando o SOAP Simple Object Access Protocol (). SOAP é um protocolo independente de plataforma e linguagem para criar e acessar serviços web. Os consumidores de um serviço ASMX não precisam saber nada sobre a plataforma, um modelo de objeto ou uma linguagem de programação usada para implementar o serviço. Eles só precisam entender como enviar e receber mensagens SOAP. Este artigo demonstra como consumir um serviço ASMX SOAP de um aplicativo xamarin. Forms._

Uma mensagem SOAP é um documento XML que contém os seguintes elementos:

- Um elemento raiz chamado *Envelope* que identifica o documento XML como uma mensagem SOAP.
- Um recurso opcional *cabeçalho* elemento que contém informações específicas do aplicativo, como dados de autenticação. Se o *cabeçalho* elemento está presente ele deve ser o primeiro elemento filho do *Envelope* elemento.
- Obrigatória *corpo* elemento que contém a mensagem SOAP para o destinatário.
- Um recurso opcional *falha* elemento que é usado para indicar as mensagens de erro. Se o *falhas* elemento estiver presente, ele deve ser um elemento filho do *corpo* elemento.

SOAP pode operar em vários protocolos de transporte, incluindo HTTP, SMTP, TCP e UDP. No entanto, um serviço ASMX só pode operar sobre HTTP. A plataforma Xamarin dá suporte a implementações padrão de SOAP 1.1 via HTTP, e isso inclui suporte para muitas das configurações de serviço padrão do ASMX.

Este exemplo inclui os aplicativos móveis que são executados em dispositivos físicos ou emulados e um serviço ASMX que fornece métodos para obter, adicionar, editar e excluir dados. Quando os aplicativos móveis são executados, eles se conectam ao serviço ASMX hospedado localmente conforme mostrado na seguinte captura de tela:

![](asmx-images/portal.png "Aplicativo de exemplo")

> [!NOTE]
> No iOS 9 e superior, a segurança de transporte de aplicativo (ATS) impõe conexões seguras entre recursos da internet (como o servidor de back-end do aplicativo) e o aplicativo, impedindo assim a divulgação acidental de informações confidenciais. Desde que o ATS é habilitado por padrão em aplicativos criados para o iOS 9, todas as conexões serão sujeitos a requisitos de segurança ATS. Se as conexões não atender a esses requisitos, eles falharão com uma exceção.
> ATS poderá ser aceito de se ele não é possível usar o `HTTPS` de protocolo e proteger a comunicação para recursos da internet. Isso pode ser feito atualizando o aplicativo **Info. plist** arquivo. Para obter mais informações, consulte [segurança de transporte de aplicativo](~/ios/app-fundamentals/ats.md).

## <a name="consume-the-web-service"></a>Consumir o serviço web

O serviço ASMX oferece as seguintes operações:

|Operação|Descrição|Parâmetros|
|--- |--- |--- |
|GetTodoItems|Obter uma lista de itens pendentes|
|CreateTodoItem|Criar um novo item pendente|Um XML serializado TodoItem|
|EditTodoItem|Atualizar um item pendente|Um XML serializado TodoItem|
|DeleteTodoItem|Excluir um item pendente|Um XML serializado TodoItem|

Para obter mais informações sobre o modelo de dados usado no aplicativo, consulte [modelagem de dados](~/xamarin-forms/data-cloud/walkthrough.md).

## <a name="create-the-todoservice-proxy"></a>Criar o proxy TodoService

Uma classe de proxy, chamada `TodoService`, estende `SoapHttpClientProtocol` e fornece métodos para se comunicar com o serviço ASMX em HTTP. O proxy é gerado, adicionando uma referência web para cada projeto específico da plataforma no 2019 do Visual Studio ou Visual Studio 2017. A referência web gera métodos e eventos para cada ação definida no documento de descrição linguagem WSDL (Web Services) do serviço.

Por exemplo, o `GetTodoItems` ação de serviço resulta em um `GetTodoItemsAsync` método e um `GetTodoItemsCompleted` eventos no proxy. O método gerado tem um tipo de retorno void e invoca o `GetTodoItems` ação pai `SoapHttpClientProtocol` classe. Quando o método invocado recebe uma resposta do serviço, ele aciona o `GetTodoItemsCompleted` evento e fornece os dados de resposta dentro do evento `Result` propriedade.

## <a name="create-the-isoapservice-implementation"></a>Criar a implementação de ISoapService

Para habilitar o projeto compartilhado e de plataforma cruzada trabalhar com o serviço, o exemplo define o `ISoapService` da interface, que segue o [modelo de programação assíncrona de tarefa em C# ](/dotnet/csharp/programming-guide/concepts/async/). Cada plataforma implementa o `ISoapService` para expor o proxy específico da plataforma. O exemplo usa `TaskCompletionSource` objetos para expor o proxy como uma interface de tarefa assíncrona. Detalhes sobre como usar `TaskCompletionSource` são encontrados nas implementações de cada tipo de ação nas seções a seguir.

O exemplo `SoapService`:

1. Cria uma instância de `TodoService` como uma instância de nível de classe
1. Cria uma coleção chamada `Items` para armazenar `TodoItem` objetos
1. Especifica um ponto de extremidade personalizado para opcional `Url` propriedade sobre a `TodoService`

```csharp
public class SoapService : ISoapService
{
    ASMXService.TodoService todoService;
    public List<TodoItem> Items { get; private set; } = new List<TodoItem>();

    public SoapService ()
    {
        todoService = new ASMXService.TodoService ();
        todoService.Url = Constants.SoapUrl;
        ...
    }
}
```

### <a name="create-data-transfer-objects"></a>Criar objetos de transferência de dados

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

Esse método cria uma nova `ASMService.TodoItem` da instância e define cada propriedade para a propriedade idêntica do `TodoItem` instância.

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

Esse método recupera os dados do proxy gerado `TodoItem` de tipo e define-a no recém-criado `TodoItem` instância.

### <a name="retrieve-data"></a>Recuperar dados

O `ISoapService` interface espera que o `RefreshDataAsync` método retorne um `Task` com a coleção de itens. No entanto, o `TodoService.GetTodoItemsAsync` método retorna void. Para satisfazer o padrão de interface, você deve chamar `GetTodoItemsAsync`, aguarde até que o `GetTodoItemsCompleted` evento seja acionado e popular a coleção. Isso permite que você retorne uma coleção válida para a interface do usuário.

O exemplo a seguir cria um novo `TaskCompletionSource`, inicia a chamada assíncrona na `RefreshDataAsync` método e aguarda o `Task` fornecidos pelo `TaskCompletionSource`. Quando o `TodoService_GetTodoItemsCompleted` manipulador de eventos é invocado ele preenche os `Items` coleção e atualizações a `TaskCompletionSource`:

```csharp
public class SoapService : ISoapService
{
    TaskCompletionSource<bool> getRequestComplete = null;
    ...

    public SoapService()
    {
        ...
        todoService.GetTodoItemsCompleted += TodoService_GetTodoItemsCompleted;
    }

    public async Task<List<TodoItem>> RefreshDataAsync()
    {
        getRequestComplete = new TaskCompletionSource<bool>();
        todoService.GetTodoItemsAsync();
        await getRequestComplete.Task;
        return Items;
    }

    private void TodoService_GetTodoItemsCompleted(object sender, ASMXService.GetTodoItemsCompletedEventArgs e)
    {
        try
        {
            getRequestComplete = getRequestComplete ?? new TaskCompletionSource<bool>();

            Items = new List<TodoItem>();
            foreach (var item in e.Result)
            {
                Items.Add(FromASMXServiceTodoItem(item));
            }
            getRequestComplete?.TrySetResult(true);
        }
        catch (Exception ex)
        {
            Debug.WriteLine(@"\t\tERROR {0}", ex.Message);
        }
    }

    ...
}
```

Para obter mais informações, consulte [modelo de programação assíncrona](/dotnet/standard/asynchronous-programming-patterns/asynchronous-programming-model-apm) e [.NET Framework programação assíncrona tradicional e TPL](/dotnet/standard/parallel-programming/tpl-and-traditional-async-programming).

### <a name="create-or-edit-data"></a>Criar ou editar dados

Quando você cria ou edita dados, você deve implementar o `ISoapService.SaveTodoItemAsync` método. Este método detecta se o `TodoItem` é um item novo ou atualizado e chama o método apropriado no `todoService` objeto. O `CreateTodoItemCompleted` e `EditTodoItemCompleted` manipuladores de eventos devem ser implementados para que você saiba quando o `todoService` recebeu uma resposta do serviço ASMX (eles podem ser combinados em um único manipulador porque elas executam a mesma operação). O exemplo a seguir demonstra as implementações de manipulador de interface e eventos, bem como o `TaskCompletionSource` objeto usado para operar de maneira assíncrona:

```csharp
public class SoapService : ISoapService
{
    TaskCompletionSource<bool> saveRequestComplete = null;
    ...

    public SoapService()
    {
        ...
        todoService.CreateTodoItemCompleted += TodoService_SaveTodoItemCompleted;
        todoService.EditTodoItemCompleted += TodoService_SaveTodoItemCompleted;
    }

    public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
    {
        try
        {
            var todoItem = ToASMXServiceTodoItem(item);
            saveRequestComplete = new TaskCompletionSource<bool>();
            if (isNewItem)
            {
                todoService.CreateTodoItemAsync(todoItem);
            }
            else
            {
                todoService.EditTodoItemAsync(todoItem);
            }
            await saveRequestComplete.Task;
        }
        catch (SoapException se)
        {
            Debug.WriteLine("\t\t{0}", se.Message);
        }
        catch (Exception ex)
        {
            Debug.WriteLine("\t\tERROR {0}", ex.Message);
        }
    }

    private void TodoService_SaveTodoItemCompleted(object sender, System.ComponentModel.AsyncCompletedEventArgs e)
    {
        saveRequestComplete?.TrySetResult(true);
    }

    ...
}
```

### <a name="delete-data"></a>Excluir dados

A exclusão de dados requer uma implementação semelhante. Definir um `TaskCompletionSource`, implemente um manipulador de eventos e o `ISoapService.DeleteTodoItemAsync` método:

```csharp
public class SoapService : ISoapService
{
    TaskCompletionSource<bool> deleteRequestComplete = null;
    ...

    public SoapService()
    {
        ...
        todoService.DeleteTodoItemCompleted += TodoService_DeleteTodoItemCompleted;
    }

    public async Task DeleteTodoItemAsync (string id)
    {
        try
        {
            deleteRequestComplete = new TaskCompletionSource<bool>();
            todoService.DeleteTodoItemAsync(id);
            await deleteRequestComplete.Task;
        }
        catch (SoapException se)
        {
            Debug.WriteLine("\t\t{0}", se.Message);
        }
        catch (Exception ex)
        {
            Debug.WriteLine("\t\tERROR {0}", ex.Message);
        }
    }

    private void TodoService_DeleteTodoItemCompleted(object sender, System.ComponentModel.AsyncCompletedEventArgs e)
    {
        deleteRequestComplete?.TrySetResult(true);
    }

    ...
}
```

## <a name="test-the-web-service"></a>Testar o serviço web

Teste físicos ou emulados dispositivos com um serviço hospedado localmente requer a configuração personalizada do IIS, endereços de ponto de extremidade e regras de firewall para estar em vigor. Para obter mais detalhes sobre como configurar seu ambiente de teste, consulte o [configurar o acesso remoto para o IIS Express](wcf.md#configure-remote-access-to-iis-express). A única diferença entre o teste de WCF e ASMX é o número da porta do TodoService.

## <a name="related-links"></a>Links relacionados

- [TodoASMX (amostra)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoASMX/)
- [IAsyncResult](https://docs.microsoft.com/dotnet/api/system.iasyncresult)
