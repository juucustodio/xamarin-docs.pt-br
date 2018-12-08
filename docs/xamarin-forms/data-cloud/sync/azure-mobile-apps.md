---
title: Sincronizar dados Offline com aplicativos móveis do Azure
description: Este artigo explica como adicionar funcionalidade de sincronização offline para um aplicativo xamarin. Forms que consome um back-end de aplicativos móveis do Azure.
ms.prod: xamarin
ms.assetid: DBB343B0-2709-4C20-A669-5522B9956D9B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/02/2017
ms.openlocfilehash: 6080b4dc152558d6f532399cee7424670c588c28
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53058172"
---
# <a name="synchronizing-offline-data-with-azure-mobile-apps"></a>Sincronizar dados Offline com aplicativos móveis do Azure

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthOfflineSync/)

_Sincronização offline permite que os usuários interajam com um aplicativo móvel, exibindo, adicionando ou modificando dados, mesmo que não tem uma conexão de rede. As alterações são armazenadas em um banco de dados local e, depois que o dispositivo estiver online, as alterações podem ser sincronizadas com a instância de aplicativos móveis do Azure. Este artigo explica como adicionar funcionalidade de sincronização offline para um aplicativo xamarin. Forms._

## <a name="overview"></a>Visão geral

O [SDK do Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) fornece o `IMobileServiceTable` interface, que representa as operações que podem ser executadas em tabelas armazenadas na instância de aplicativos móveis do Azure. Essas operações conectam diretamente à instância de aplicativos móveis do Azure e falharão se o dispositivo móvel não tiver uma conexão de rede.

Para dar suporte à sincronização offline, o SDK do Azure Mobile Client oferece suporte a *tabelas de sincronização*, que são fornecidas pelo `IMobileServiceSyncTable` interface. Essa interface fornece o mesmo Create, Read, Update, operações de exclusão (CRUD) como o `IMobileServiceTable` interface, mas as operações de ler ou gravar em um repositório local. O armazenamento local não é preenchido com novos dados da instância de aplicativos móveis do Azure até que haja uma chamada para *pull* dados. Da mesma forma, os dados não serão enviados para a instância de aplicativos móveis do Azure até que haja uma chamada para *push* alterações locais.

Sincronização offline também inclui suporte para detectar conflitos quando o mesmo registro é alterado no repositório local e na instância de aplicativos móveis do Azure e a resolução de conflitos personalizado. Conflitos podem ser manuseados no armazenamento local ou na instância de aplicativos móveis do Azure.

Para obter mais informações sobre a sincronização offline, consulte [sincronização de dados Offline nos aplicativos móveis do Azure](/azure/app-service-mobile/app-service-mobile-offline-data-sync/) e [habilitar sincronização offline para seu aplicativo móvel do xamarin. Forms](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-offline-data/).

## <a name="setup"></a>Configuração

O processo para a integração de sincronização offline entre um aplicativo xamarin. Forms e uma instância de aplicativos móveis do Azure é da seguinte maneira:

1. Crie uma instância de aplicativos móveis do Azure. Para obter mais informações, consulte [consumir o aplicativo móvel do Azure](~/xamarin-forms/data-cloud/consuming/azure.md).
1. Adicione a [sqlitestore](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/) pacote do NuGet para todos os projetos na solução xamarin. Forms.
1. (Opcional) Habilite a autenticação na instância de aplicativos móveis do Azure e o aplicativo xamarin. Forms. Para obter mais informações, consulte [autenticando usuários com aplicativos móveis do Azure](~/xamarin-forms/data-cloud/authentication/azure.md).

A seção a seguir fornece instruções de configuração adicionais para configurar projetos de plataforma Universal do Windows (UWP) para usar o pacote NuGet do sqlitestore. Nenhuma configuração adicional é necessária para usar o pacote NuGet do sqlitestore no iOS e Android.

### <a name="universal-windows-platform"></a>Plataforma Universal do Windows

Para usar o SQLite no Universal Windows Platform (UWP), siga estas etapas:

1. Instalar o [SQLite para plataforma Universal do Windows](http://sqlite.org/2016/sqlite-uwp-3120200.vsix) extensão do Visual Studio em seu ambiente de desenvolvimento.
1. No projeto UWP no Visual Studio, clique com botão direito **referências > Adicionar referência**, navegue até **extensões** e adicione o **SQLite para plataforma Universal do Windows** e  **Visual C++ 2015 Runtime para aplicativos da plataforma Windows Universal** extensões para o projeto UWP.

## <a name="initializing-the-local-store"></a>Inicializando o Store Local

O armazenamento local deve ser inicializado antes que qualquer operação de tabela de sincronização pode ser executadas. Isso é feito no projeto de biblioteca de classe portátil (PCL) da solução xamarin. Forms:

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

Novo banco de dados SQLite local é criado pelo `MobileServiceSQLiteStore` de classe, desde que ele ainda não existir. Em seguida, o `DefineTable<T>` método cria uma tabela no repositório local que corresponde aos campos no `TodoItem` de tipo, desde que ele ainda não existir.

Um *contexto de sincronização* está associado com um `MobileServiceClient` instância e rastreia as alterações são feitas com tabelas de sincronização. O contexto de sincronização mantém uma fila que mantém uma lista ordenada de operações criar, atualizar e excluir (CUD) que será enviada para a instância de aplicativos móveis do Azure mais tarde. O `IMobileServiceSyncContext.InitializeAsync()` método é usado para associar o repositório local com o contexto de sincronização.

O `todoTable` campo é um `IMobileServiceSyncTable`, e, portanto, todas as operações de CRUD usam o armazenamento local.

## <a name="performing-synchronization"></a>Execução da sincronização

O armazenamento local é sincronizado com o Azure Mobile Apps instância quando o `SyncAsync` método é invocado:

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

O `IMobileServiceSyncTable.PushAsync` método opera no contexto de sincronização, em vez de uma tabela específica e envia todas as alterações CUD desde o último envio por push.

Pull é executado pelo `IMobileServiceSyncTable.PullAsync` método em uma única tabela. O primeiro parâmetro para o `PullAsync` método é um nome de consulta que é usado apenas no dispositivo móvel. Fornecendo uma consulta não nulo os resultados de nome em que o SDK do Mobile Client do Azure que executa um *sincronização incremental*, onde cada vez que uma operação de recepção retorna os resultados, a versão mais recente `updatedAt` timestamp dos resultados é armazenado no local tabelas do sistema. Operações de pull subsequentes, em seguida, recuperam somente registros após esse carimbo de hora. Como alternativa, *sincronização completa* pode ser feito passando `null` como o nome da consulta, o que resulta em todos os registros que estão sendo recuperados em cada operação de recepção. Após qualquer operação de sincronização, os dados recebidos são inseridos no repositório local.

> [!NOTE]
> Se um pull é executado em uma tabela que tenha atualizações locais pendentes, o pull primeiro executará um envio por push no contexto de sincronização. Isso minimiza os conflitos entre as alterações que já estão na fila e novos dados da instância de aplicativos móveis do Azure.

O `SyncAsync` método também inclui uma implementação básica para lidar com conflitos quando o mesmo registro é alterado no repositório local e na instância de aplicativos móveis do Azure. Quando o conflito é que os dados tiverem sido atualizados no repositório local e na instância de aplicativos móveis do Azure, o `SyncAsync` método atualiza os dados no repositório local dos dados armazenados na instância de aplicativos móveis do Azure. Quando ocorre qualquer conflito de outro, o `SyncAsync` método descarta a alteração local. Ele lida com o cenário em que uma alteração local existe para os dados que foi excluídos da instância de aplicativos móveis do Azure.

Em um aplicativo de produção, os desenvolvedores devem escrever um personalizado `IMobileServiceSyncHandler` implementação de manipulação de conflitos que é adequada ao seu caso de uso. Para obter mais informações, consulte [usar concorrência otimista para resolução de conflitos](https://azure.microsoft.com/documentation/articles/app-service-mobile-dotnet-how-to-use-client-library/#optimisticconcurrency) no portal do Azure, e [aprofundando-se sobre o suporte offline no SDK do cliente gerenciado](https://blogs.msdn.microsoft.com/azuremobile/2014/04/07/deep-dive-on-the-offline-support-in-the-managed-client-sdk/) em blogs do MSDN.

## <a name="purging-data"></a>Limpando dados

Tabelas no armazenamento local podem ser apagadas de dados com o `IMobileServiceSyncTable.PurgeAsync` método. Esse método dá suporte a cenários, como remover dados obsoletos que não exige um aplicativo. Por exemplo, o aplicativo de exemplo exibe apenas `TodoItem` instâncias que não são concluídas. Portanto, itens concluídos não precisam mais ser armazenados localmente. Limpar itens concluídos do repositório local pode ser feito da seguinte maneira:

```csharp
await todoTable.PurgeAsync(todoTable.Where(item => item.Done));
```

Uma chamada para `PurgeAsync` também dispara uma operação de push. Portanto, todos os itens que são marcados como concluídos localmente serão enviados para a instância de aplicativos móveis do Azure antes de serem removidos do repositório local. No entanto, se houver operações pendentes de sincronização com a instância de aplicativos móveis do Azure, a limpeza lançará uma `InvalidOperationException` , a menos que o `force` parâmetro for definido como `true`. Uma estratégia alternativa é examinar o `IMobileServiceSyncContext.PendingOperations` propriedade, que retorna o número de operações que ainda não foi enviado para a instância de aplicativos móveis do Azure e executar a limpeza apenas se a propriedade é zero pendentes.

> [!NOTE]
> Invocando `PurgeAsync` com o `force` parâmetro definido como `true` perderá todas as alterações pendentes.

## <a name="initiating-synchronization"></a>Iniciando sincronização

No aplicativo de exemplo, o `SyncAsync` método é invocado por meio de `TodoList.OnAppearing` método:

```csharp
protected override async void OnAppearing()
{
  base.OnAppearing();

  // Set syncItems to true to synchronize the data on startup when running in offline mode
  await RefreshItems(true, syncItems: true);
}
```

Isso significa que o aplicativo tentará sincronizar com a instância de aplicativos móveis do Azure quando ele é iniciado.

Além disso, sincronização poderá ser iniciada no iOS e Android usando o pull para atualizar a lista de dados e nas plataformas Windows usando o **sincronização** botão na interface do usuário. Para obter mais informações, consulte [efetue Pull para atualizar](~/xamarin-forms/user-interface/listview/interactivity.md#Pull_to_Refresh).

## <a name="summary"></a>Resumo

Este artigo explicou como adicionar funcionalidade de sincronização offline para um aplicativo xamarin. Forms. Sincronização offline permite que os usuários interajam com um aplicativo móvel, exibindo, adicionando ou modificando dados, mesmo que não tem uma conexão de rede. As alterações são armazenadas em um banco de dados local e, depois que o dispositivo estiver online, as alterações podem ser sincronizadas com a instância de aplicativos móveis do Azure.


## <a name="related-links"></a>Links relacionados

- [TodoAzureAuthOfflineSync (sample)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthOfflineSync/)
- [Consumir um aplicativo móvel do Azure](~/xamarin-forms/data-cloud/consuming/azure.md)
- [Autenticar usuários com aplicativos móveis do Azure](~/xamarin-forms/data-cloud/authentication/azure.md)
- [SDK de cliente móvel do Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [MobileServiceClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx)
