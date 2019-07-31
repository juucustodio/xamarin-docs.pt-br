---
title: Consumir um serviço Web ASP.NET (ASMX)
description: Este artigo demonstra como consumir um serviço ASMX SOAP de um aplicativo xamarin. Forms.
ms.prod: xamarin
ms.assetid: D5533964-5528-4D35-9C2B-FAFB632472AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/02/2019
ms.openlocfilehash: 124cffaf827ebeb8109f3cd4ac4dfd2701e43f9c
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656652"
---
# <a name="consume-an-aspnet-web-service-asmx"></a>Consumir um serviço Web ASP.NET (ASMX)

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todoasmx)

_ASMX fornece a capacidade de criar serviços web que enviam mensagens usando o SOAP Simple Object Access Protocol (). SOAP é um protocolo independente de plataforma e linguagem para criar e acessar serviços web. Os consumidores de um serviço ASMX não precisam saber nada sobre a plataforma, um modelo de objeto ou uma linguagem de programação usada para implementar o serviço. Eles só precisam entender como enviar e receber mensagens SOAP. Este artigo demonstra como consumir um serviço ASMX SOAP de um aplicativo xamarin. Forms._

Uma mensagem SOAP é um documento XML que contém os seguintes elementos:

- Um elemento raiz chamado *Envelope* que identifica o documento XML como uma mensagem SOAP.
- Um recurso opcional *cabeçalho* elemento que contém informações específicas do aplicativo, como dados de autenticação. Se o *cabeçalho* elemento está presente ele deve ser o primeiro elemento filho do *Envelope* elemento.
- Obrigatória *corpo* elemento que contém a mensagem SOAP para o destinatário.
- Um recurso opcional *falha* elemento que é usado para indicar as mensagens de erro. Se o *falhas* elemento estiver presente, ele deve ser um elemento filho do *corpo* elemento.

SOAP pode operar em vários protocolos de transporte, incluindo HTTP, SMTP, TCP e UDP. No entanto, um serviço ASMX só pode operar sobre HTTP. A plataforma Xamarin dá suporte a implementações padrão de SOAP 1.1 via HTTP, e isso inclui suporte para muitas das configurações de serviço padrão do ASMX.

Este exemplo inclui os aplicativos móveis que são executados em dispositivos físicos ou emulados e um serviço ASMX que fornece métodos para obter, adicionar, editar e excluir dados. Quando os aplicativos móveis são executados, eles se conectam ao serviço ASMX hospedado localmente, conforme mostrado na seguinte captura de tela:

![](asmx-images/portal.png "Aplicativo de exemplo")

> [!NOTE]
> No iOS 9 e superior, a segurança de transporte de aplicativo (ATS) impõe conexões seguras entre recursos da internet (como o servidor de back-end do aplicativo) e o aplicativo, impedindo assim a divulgação acidental de informações confidenciais. Desde que o ATS é habilitado por padrão em aplicativos criados para o iOS 9, todas as conexões serão sujeitos a requisitos de segurança ATS. Se as conexões não atender a esses requisitos, eles falharão com uma exceção.
> ATS poderá ser aceito de se ele não é possível usar o `HTTPS` de protocolo e proteger a comunicação para recursos da internet. Isso pode ser feito atualizando o aplicativo **Info. plist** arquivo. Para obter mais informações, consulte [segurança de transporte de aplicativo](~/ios/app-fundamentals/ats.md).

## <a name="consume-the-web-service"></a>Consumir o serviço Web

O serviço ASMX oferece as seguintes operações:

|Operação|Descrição|Parâmetros|
|--- |--- |--- |
|GetTodoItems|Obter uma lista de itens pendentes|
|CreateTodoItem|Criar um novo item pendente|Um XML serializado TodoItem|
|EditTodoItem|Atualizar um item pendente|Um XML serializado TodoItem|
|DeleteTodoItem|Excluir um item pendente|Um XML serializado TodoItem|

Para obter mais informações sobre o modelo de dados usado no aplicativo, consulte [modelagem de dados](~/xamarin-forms/data-cloud/web-services/introduction.md).

## <a name="create-the-todoservice-proxy"></a>Criar o proxy TodoService

Uma classe proxy, chamada `TodoService`, estende `SoapHttpClientProtocol` e fornece métodos para se comunicar com o serviço ASMX por http. O proxy é gerado adicionando uma referência Web a cada projeto específico da plataforma no Visual Studio 2019 ou no Visual Studio 2017. A referência Web gera métodos e eventos para cada ação definida no documento WSDL (Web Services Description Language) do serviço.

Por exemplo, a `GetTodoItems` ação de serviço resulta em `GetTodoItemsAsync` um método e `GetTodoItemsCompleted` um evento no proxy. O método gerado tem um tipo de retorno void e invoca a `GetTodoItems` ação na classe pai `SoapHttpClientProtocol` . Quando o método chamado recebe uma resposta do serviço, ele dispara o `GetTodoItemsCompleted` evento e fornece os dados de resposta dentro da Propriedade do `Result` evento.

## <a name="create-the-isoapservice-implementation"></a>Criar a implementação de ISoapService

Para habilitar o projeto de plataforma cruzada e compartilhada para trabalhar com o serviço, o exemplo define `ISoapService` a interface, que segue o [modelo de programação assíncrona de tarefa no C# ](/dotnet/csharp/programming-guide/concepts/async/). Cada plataforma implementa o `ISoapService` para expor o proxy específico da plataforma. O exemplo usa `TaskCompletionSource` objetos para expor o proxy como uma interface assíncrona de tarefa. Detalhes sobre como `TaskCompletionSource` usar são encontrados nas implementações de cada tipo de ação nas seções a seguir.

O exemplo `SoapService`:

1. Instancia o `TodoService` como uma instância de nível de classe
1. Cria uma coleção chamada `Items` para armazenar `TodoItem` objetos
1. Especifica um ponto de extremidade personalizado para `Url` a propriedade opcional no`TodoService`

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

Esse método cria uma nova `ASMService.TodoItem` instância e define cada propriedade para a propriedade idêntica `TodoItem` da instância.

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

Esse método recupera os dados do tipo gerado `TodoItem` por proxy e os define na `TodoItem` instância recém-criada.

### <a name="retrieve-data"></a>Recuperar dados

A `ISoapService` interface espera que `RefreshDataAsync` o método retorne `Task` um com a coleção de itens. No entanto `TodoService.GetTodoItemsAsync` , o método retorna void. Para satisfazer o padrão de interface, você deve `GetTodoItemsAsync`chamar, aguardar `GetTodoItemsCompleted` até que o evento seja acionado e preencher a coleção. Isso permite que você retorne uma coleção válida para a interface do usuário.

O exemplo a seguir cria um `TaskCompletionSource`novo, inicia a chamada assíncrona `RefreshDataAsync` no método e aguarda o `Task` fornecido pelo `TaskCompletionSource`. Quando o `TodoService_GetTodoItemsCompleted` manipulador de eventos é invocado, ele `Items` popula a coleção e `TaskCompletionSource`atualiza:

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

Para obter mais informações, consulte [modelo de programação assíncrona](/dotnet/standard/asynchronous-programming-patterns/asynchronous-programming-model-apm) e [tpl e programação assíncrona de .NET Framework tradicional](/dotnet/standard/parallel-programming/tpl-and-traditional-async-programming).

### <a name="create-or-edit-data"></a>Criar ou editar dados

Ao criar ou editar dados, você deve implementar o `ISoapService.SaveTodoItemAsync` método. Esse método detecta se o `TodoItem` é um item novo ou atualizado e chama o método apropriado `todoService` no objeto. Os `CreateTodoItemCompleted` manipuladores de eventos e `EditTodoItemCompleted` também devem ser implementados para que você `todoService` saiba quando o recebeu uma resposta do serviço ASMX (eles podem ser combinados em um único manipulador, pois eles executam a mesma operação). O exemplo a seguir demonstra a interface e as implementações do manipulador de eventos `TaskCompletionSource` , bem como o objeto usado para operar de forma assíncrona:

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

A exclusão de dados requer uma implementação semelhante. Defina um `TaskCompletionSource`, implemente um manipulador de eventos e `ISoapService.DeleteTodoItemAsync` o método:

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

## <a name="test-the-web-service"></a>Testar o serviço Web

O teste de dispositivos físicos ou emulados com um serviço hospedado localmente requer configuração personalizada do IIS, endereços de ponto de extremidade e regras de firewall. Para obter mais detalhes sobre como configurar seu ambiente para teste, consulte Configurar o [acesso remoto para IIS Express](wcf.md#configure-remote-access-to-iis-express). A única diferença entre testar o WCF e o ASMX é o número da porta do TodoService.

## <a name="related-links"></a>Links relacionados

- [TodoASMX (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todoasmx)
- [IAsyncResult](https://docs.microsoft.com/dotnet/api/system.iasyncresult)
