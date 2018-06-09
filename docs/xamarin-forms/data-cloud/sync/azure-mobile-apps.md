---
title: Sincronização de dados Offline com aplicativos móveis do Azure
description: Este artigo explica como adicionar a funcionalidade de sincronização offline para um aplicativo xamarin. Forms que consome um back-end os aplicativos móveis do Azure.
ms.prod: xamarin
ms.assetid: DBB343B0-2709-4C20-A669-5522B9956D9B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/02/2017
ms.openlocfilehash: e8b0eeeb4f0033fccd7a61b4acb286bb8457e6c2
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243591"
---
# <a name="synchronizing-offline-data-with-azure-mobile-apps"></a>Sincronização de dados Offline com aplicativos móveis do Azure

_Sincronização offline permite que os usuários interagem com um aplicativo móvel, exibir, adicionar ou modificar dados, mesmo que não tem uma conexão de rede. As alterações são armazenadas em um banco de dados local e depois que o dispositivo estiver online, as alterações podem ser sincronizadas com a instância os aplicativos móveis do Azure. Este artigo explica como adicionar a funcionalidade de sincronização offline para um aplicativo xamarin. Forms._

## <a name="overview"></a>Visão geral

O [cliente SDK do Azure Mobile](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) fornece o `IMobileServiceTable` interface, que representa as operações que podem ser executadas em tabelas armazenadas na instância os aplicativos móveis do Azure. Essas operações conecte-se diretamente à instância de aplicativos do Azure Mobile e falharão se o dispositivo móvel não tenha uma conexão de rede.

Para dar suporte à sincronização offline, o cliente SDK do Azure Mobile dá suporte a *sincronizar tabelas*, que são fornecidas pelo `IMobileServiceSyncTable` interface. Essa interface fornece o mesmo criar, ler, atualizar, operações de exclusão (CRUD) como o `IMobileServiceTable` interface, mas as operações de ler ou gravar em um repositório local. O armazenamento local não é preenchido com novos dados da instância os aplicativos móveis do Azure até que haja uma chamada para *pull* dados. Da mesma forma, os dados não serão enviados para a instância de aplicativos do Azure Mobile até que haja uma chamada para *push* alterações locais.

Sincronização offline também inclui suporte para a detecção de conflitos quando o mesmo registro é alterado no armazenamento local e na instância os aplicativos móveis do Azure e resolução de conflitos personalizado. Conflitos podem ser manuseados no armazenamento local ou na instância os aplicativos móveis do Azure.

Para obter mais informações sobre a sincronização offline, consulte [sincronização de dados Offline em aplicativos móveis do Azure](/azure/app-service-mobile/app-service-mobile-offline-data-sync/) e [habilitar sincronização offline para seu aplicativo móvel do xamarin. Forms](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-offline-data/).

## <a name="setup"></a>Configuração

O processo de integração de sincronização offline entre um aplicativo xamarin. Forms e uma instância de aplicativos do Azure Mobile é o seguinte:

1. Crie uma instância dos aplicativos móveis do Azure. Para obter mais informações, consulte [consumindo um aplicativo do Azure Mobile](~/xamarin-forms/data-cloud/consuming/azure.md).
1. Adicionar o [Microsoft.Azure.Mobile.Client.SQLiteStore](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/) pacote NuGet para todos os projetos na solução xamarin. Forms.
1. (Opcional) Habilite a autenticação na instância os aplicativos móveis do Azure e o aplicativo xamarin. Forms. Para obter mais informações, consulte [autenticando usuários com aplicativos móveis do Azure](~/xamarin-forms/data-cloud/authentication/azure.md).

A seção a seguir fornece instruções de instalação adicionais para configurar projetos do Windows UWP (plataforma Universal) para usar o pacote Microsoft.Azure.Mobile.Client.SQLiteStore NuGet. Nenhuma configuração adicional é necessária para usar o pacote Microsoft.Azure.Mobile.Client.SQLiteStore NuGet no iOS e Android.

### <a name="universal-windows-platform"></a>Plataforma Universal do Windows

Para usar SQLite no Windows UWP (plataforma Universal), siga estas etapas:

1. Instalar o [SQLite para a plataforma Universal do Windows](http://sqlite.org/2016/sqlite-uwp-3120200.vsix) extensão do Visual Studio em seu ambiente de desenvolvimento.
1. No projeto UWP no Visual Studio, clique com botão direito **referências > Adicionar referência**, navegue até **extensões** e adicione o **SQLite para plataforma Universal do Windows** e  **Visual C++ 2015 Runtime para aplicativos de plataforma Universal do Windows** extensões para o projeto UWP.

## <a name="initializing-the-local-store"></a>Inicializando o repositório Local

O armazenamento local deve ser inicializado antes de quaisquer operações de tabela de sincronização podem ser executadas. Isso é obtido do projeto de biblioteca de classe portátil (PCL) da solução xamarin. Forms:

```csharp
using Microsoft.WindowsAzure.MobileServices;
using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
using Microsoft.WindowsAzure.MobileServices.Sync;

namespace TodoAzure
{
    public partial class TodoItemManager
    {
        static TodoItemManager defaultInstance = new TodoItemManager();
        IMobileServiceClient client;
        IMobileServiceSyncTable<TodoItem> todoTable;

        private TodoItemManager()
        {
            this.client = new MobileServiceClient(Constants.ApplicationURL);
            var store = new MobileServiceSQLiteStore("localstore.db");
            store.DefineTable<TodoItem>();
            this.client.SyncContext.InitializeAsync(store);
            this.todoTable = client.GetSyncTable<TodoItem>();
        }
        ...
  }
}
```

Novo banco de dados SQLite local é criado o `MobileServiceSQLiteStore` de classe, desde que ela ainda não existir. Em seguida, o `DefineTable<T>` método cria uma tabela no armazenamento local que corresponda aos campos no `TodoItem` tipo, desde que ela ainda não existir.

Um *o contexto de sincronização* está associado com um `MobileServiceClient` instância e rastreia as alterações feitas com tabelas de sincronização. O contexto de sincronização mantém uma fila que mantém uma lista ordenada de operações de criar, atualizar e excluir (CUD) que será enviada para a instância do Azure Mobile aplicativos mais tarde. O `IMobileServiceSyncContext.InitializeAsync()` método é usado para associar o repositório local com o contexto de sincronização.

O `todoTable` campo é um `IMobileServiceSyncTable`, e, portanto, todas as operações CRUD usam o armazenamento local.

## <a name="performing-synchronization"></a>Executar sincronização

O armazenamento local é sincronizado com os aplicativos móveis do Azure instância quando o `SyncAsync` método é chamado:

```csharp
public async Task SyncAsync()
{
  ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

  try
  {
    await this.client.SyncContext.PushAsync();

    // The first parameter is a query name that is used internally by the client SDK to implement incremental sync.
    // Use a different query name for each unique query in your program.
    await this.todoTable.PullAsync("allTodoItems", this.todoTable.CreateQuery());
  }
  catch (MobileServicePushFailedException exc)
  {
    if (exc.PushResult != null)
    {
      syncErrors = exc.PushResult.Errors;
    }
  }

  // Simple error/conflict handling.
  if (syncErrors != null)
  {
    foreach (var error in syncErrors)
    {
      if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
      {
        // Update failed, revert to server's copy
        await error.CancelAndUpdateItemAsync(error.Result);
      }
      else
      {
        // Discard local change
        await error.CancelAndDiscardItemAsync();
      }

      Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.", error.TableName, error.Item["id"]);
    }
  }
}
```

O `IMobileServiceSyncTable.PushAsync` método opera o contexto de sincronização, em vez de uma tabela específica e envia todas as alterações CUD desde o último envio por push.

Pull é executado pelo `IMobileServiceSyncTable.PullAsync` método em uma única tabela. O primeiro parâmetro para o `PullAsync` método é um nome de consulta que é usado apenas no dispositivo móvel. Fornecendo os resultados de nome o SDK do Azure Mobile cliente executar uma consulta não nulo um *sincronização incremental*, onde cada vez que uma operação de recepção retorna resultados, a última `updatedAt` timestamp dos resultados é armazenado no local tabelas do sistema. Operações de recebimento subsequentes, em seguida, recuperam somente registros após esse carimbo de hora. Como alternativa, *sincronização completa* pode ser obtida, passando `null` como o nome da consulta, o que resulta em todos os registros estão sendo recuperados em cada operação de recepção. Após qualquer operação de sincronização, os dados recebidos são inseridos no repositório local.

> [!NOTE]
> Se uma recepção é executada em uma tabela que tem as atualizações locais pendentes, o pull primeiro executará um envio por push no contexto de sincronização. Isso minimiza conflitos entre as alterações que já estão na fila e novos dados da instância os aplicativos móveis do Azure.

O `SyncAsync` método também inclui uma implementação básica para tratar os conflitos quando o mesmo registro é alterado no armazenamento local e na instância os aplicativos móveis do Azure. Quando o conflito é que os dados foram atualizados no repositório local e na instância os aplicativos móveis do Azure, o `SyncAsync` método atualiza os dados no armazenamento local dos dados armazenados na instância os aplicativos móveis do Azure. Quando qualquer outro conflito ocorre, o `SyncAsync` método descarta as alterações locais. Ele trata o cenário em que uma alteração local existe para dados que foi excluídos da instância os aplicativos móveis do Azure.

Em um aplicativo de produção, os desenvolvedores devem escrever um personalizado `IMobileServiceSyncHandler` implementação de manipulação de conflitos é adequado para seu caso de uso. Para obter mais informações, consulte [uso de simultaneidade otimista para resolução de conflitos](https://azure.microsoft.com/documentation/articles/app-service-mobile-dotnet-how-to-use-client-library/#optimisticconcurrency) no portal do Azure, e [mergulho no suporte a offline no SDK de cliente gerenciado](https://blogs.msdn.microsoft.com/azuremobile/2014/04/07/deep-dive-on-the-offline-support-in-the-managed-client-sdk/) blogs do MSDN.

## <a name="purging-data"></a>Limpeza de dados

Tabelas no armazenamento local podem ser apagadas de dados com o `IMobileServiceSyncTable.PurgeAsync` método. Este método dá suporte a cenários como a remoção de dados obsoletos e não exige um aplicativo. Por exemplo, o aplicativo de exemplo exibe apenas `TodoItem` instâncias que não estão concluídas. Portanto, itens concluídos não precisam ser armazenados localmente. Limpar itens concluídos do armazenamento local pode ser feito da seguinte maneira:

```csharp
await todoTable.PurgeAsync(todoTable.Where(item => item.Done));
```

Uma chamada para `PurgeAsync` também irá disparar uma operação de envio. Portanto, todos os itens que são marcados como concluídos localmente serão enviados à instância os aplicativos móveis do Azure antes de serem removidos do armazenamento local. No entanto, se houver operações pendentes sincronização com a instância os aplicativos móveis do Azure, a limpeza lançará um `InvalidOperationException` , a menos que o `force` parâmetro está definido como `true`. Uma estratégia alternativa é examinar o `IMobileServiceSyncContext.PendingOperations` propriedade, que retorna o número de operações que ainda não foram enviados por push para a instância os aplicativos móveis do Azure e executar a limpeza apenas se a propriedade é zero pendentes.

> [!NOTE]
> Invocar `PurgeAsync` com o `force` parâmetro definido como `true` perderá todas as alterações pendentes.

## <a name="initiating-synchronization"></a>Sincronização inicial

O aplicativo de exemplo, o `SyncAsync` método é invocado por meio de `TodoList.OnAppearing` método:

```csharp
protected override async void OnAppearing()
{
  base.OnAppearing();

  // Set syncItems to true to synchronize the data on startup when running in offline mode
  await RefreshItems(true, syncItems: true);
}
```

Isso significa que o aplicativo tentar sincronizar com a instância os aplicativos móveis do Azure quando ele for iniciado.

Além disso, sincronização pode ser iniciada no iOS e Android usando pull para atualizar a lista de dados e as plataformas do Windows usando o **sincronização** botão na interface do usuário. Para obter mais informações, consulte [puxe para atualizar](~/xamarin-forms/user-interface/listview/interactivity.md#Pull_to_Refresh).

## <a name="summary"></a>Resumo

Este artigo explicou como adicionar a funcionalidade de sincronização offline para um aplicativo xamarin. Forms. Sincronização offline permite que os usuários interagem com um aplicativo móvel, exibir, adicionar ou modificar dados, mesmo que não tem uma conexão de rede. As alterações são armazenadas em um banco de dados local e depois que o dispositivo estiver online, as alterações podem ser sincronizadas com a instância os aplicativos móveis do Azure.


## <a name="related-links"></a>Links relacionados

- [TodoAzureAuthOfflineSync (sample)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthOfflineSync/)
- [Consumo de um aplicativo móvel do Azure](~/xamarin-forms/data-cloud/consuming/azure.md)
- [Autenticação de usuários com aplicativos móveis do Azure](~/xamarin-forms/data-cloud/authentication/azure.md)
- [SDK de cliente móvel do Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [MobileServiceClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx)
