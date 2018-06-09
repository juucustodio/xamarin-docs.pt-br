---
title: Consumo de um aplicativo móvel do Azure
description: Neste artigo, que só é aplicável a aplicativos móveis do Azure que usam um back-end node. js, explica como consultar, inserir, atualizar e excluir dados armazenados em uma tabela em uma instância dos aplicativos móveis do Azure.
ms.prod: xamarin
ms.assetid: 2B3EFD0A-2922-437D-B151-4B4DE46E2095
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 3cf27e48fe23b9a0f035689e55a72fcc706ab266
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241325"
---
# <a name="consuming-an-azure-mobile-app"></a>Consumo de um aplicativo móvel do Azure

_Aplicativos móveis do Azure permitem que você desenvolva aplicativos com o back-ends escalonável hospedado no serviço de aplicativo do Azure, com suporte para autenticação móvel, sincronização offline e notificações por push. Neste artigo, que só é aplicável a aplicativos móveis do Azure que usam um back-end node. js, explica como consultar, inserir, atualizar e excluir dados armazenados em uma tabela em uma instância dos aplicativos móveis do Azure._

Para obter informações sobre como criar uma instância do Azure Mobile aplicativos que pode ser consumida por xamarin. Forms, consulte [criar um aplicativo xamarin. Forms](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started/). Depois de seguir estas instruções, o aplicativo de exemplo para download pode ser configurado para consumir a instância de aplicativos do Azure Mobile definindo o `Constants.ApplicationURL` para a URL da instância os aplicativos móveis do Azure. Em seguida, quando o aplicativo de exemplo é executado ele se conectará à instância os aplicativos móveis do Azure, conforme mostrado na seguinte captura de tela:

![](azure-images/portal.png "Aplicativo de exemplo")

O acesso a aplicativos móveis do Azure é por meio de [cliente SDK do Azure Mobile](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/), e todas as conexões do aplicativo de exemplo xamarin. Forms no Azure são feitas por HTTPS.

> [!NOTE]
> No iOS 9 e superior, a segurança de transporte do aplicativo (ATS) impõe conexões seguras entre os recursos de internet (como o servidor de back-end do aplicativo) e o aplicativo, impedindo assim a divulgação acidental de informações confidenciais. Como ATS está habilitado por padrão em aplicativos criados para o iOS 9, todas as conexões serão sujeitos a requisitos de segurança ATS. Se as conexões não atender a esses requisitos, eles falham com uma exceção.
> ATS pode ser incluído em se não for possível usar o `HTTPS` de protocolo e proteger a comunicação para recursos da internet. Isso pode ser obtido pela atualização do aplicativo **Info. plist** arquivo. Para obter mais informações, consulte [segurança de transporte do aplicativo](~/ios/app-fundamentals/ats.md).

## <a name="consuming-an-azure-mobile-app-instance"></a>Consumir uma instância de aplicativo móvel do Azure

O [cliente SDK do Azure Mobile](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) fornece o `MobileServiceClient` classe, que é usado por um aplicativo xamarin. Forms para acessar a instância os aplicativos móveis do Azure, conforme mostrado no exemplo de código a seguir:

```csharp
IMobileServiceTable<TodoItem> todoTable;
MobileServiceClient client;

public TodoItemManager ()
{
  client = new MobileServiceClient (Constants.ApplicationURL);
  todoTable = client.GetTable<TodoItem> ();
}
```

Quando o `MobileServiceClient` instância é criada, uma URL de aplicativo deve ser especificada para identificar a instância os aplicativos móveis do Azure. Esse valor pode ser obtido para o aplicativo móvel no painel de [Portal do Microsoft Azure](https://portal.azure.com/).

Uma referência para o `TodoItem` tabela armazenada na instância do Azure os aplicativos móveis deve ser obtida antes de operações podem ser executadas na tabela. Isso é feito chamando a `GetTable` método o `MobileServiceClient` da instância, que retorna um `IMobileServiceTable<TodoItem>` referência.

### <a name="querying-data"></a>Consultar Dados

O conteúdo de uma tabela pode ser recuperado chamando o `IMobileServiceTable.ToEnumerableAsync` método que avalia a consulta e retorna os resultados de forma assíncrona. Dados também podem ser filtrados do lado do servidor, incluindo um `Where` cláusula na consulta. O `Where` cláusula aplica uma predicado para a consulta em relação à tabela de filtro de linha, conforme mostrado no exemplo de código a seguir:

```csharp
public async Task<ObservableCollection<TodoItem>> GetTodoItemsAsync (bool syncItems = false)
{
  ...
  IEnumerable<TodoItem> items = await todoTable
              .Where (todoItem => !todoItem.Done)
              .ToEnumerableAsync ();

  return new ObservableCollection<TodoItem> (items);
}
```

Esta consulta retorna todos os itens do `TodoItem` tabela cujos `Done` propriedade é igual a `false`. Os resultados da consulta, em seguida, são colocados em um `ObservableCollection` para exibição.

### <a name="inserting-data"></a>Inserindo dados

Ao inserir dados na instância os aplicativos móveis do Azure, novas colunas serão geradas automaticamente na tabela conforme necessário, fornecido a esse esquema dinâmico está habilitado na instância os aplicativos móveis do Azure. O `IMobileServiceTable.InsertAsync` método é usado para inserir uma nova linha de dados da tabela especificada, conforme mostrado no exemplo de código a seguir:

```csharp
public async Task SaveTaskAsync (TodoItem item)
{
  ...
  await todoTable.InsertAsync (item);
  ...
}
```

Ao fazer uma solicitação de inserção, uma ID não deve ser especificada nos dados que está sendo passados para a instância os aplicativos móveis do Azure. Se a solicitação de inserção contém uma ID de um `MobileServiceInvalidOperationException` será lançada.

Após o `InsertAsync` método é concluído, a ID de dados na instância os aplicativos móveis do Azure será preenchida na `TodoItem` instância no aplicativo xamarin. Forms.

### <a name="updating-data"></a>Atualizando dados

Ao atualizar dados na instância de aplicativos do Azure Mobile, novas colunas serão geradas automaticamente na tabela conforme necessário, fornecido a esse esquema dinâmico está habilitado na instância os aplicativos móveis do Azure. O `IMobileServiceTable.UpdateAsync` método é usado para atualizar os dados existentes com novas informações, como mostrado no exemplo de código a seguir:

```csharp
public async Task SaveTaskAsync (TodoItem item)
{
  ...
  await todoTable.UpdateAsync (item);
  ...
}
```

Ao fazer uma solicitação de atualização, uma ID deve ser especificada para que a instância de aplicativos do Azure Mobile possa identificar os dados a serem atualizadas. Esse valor de ID é armazenado no `TodoItem.ID` propriedade. Se a solicitação de atualização não contém uma ID não é possível para a instância os aplicativos móveis do Azure determinar os dados a ser atualizado e, portanto, um `MobileServiceInvalidOperationException` será lançada.

### <a name="deleting-data"></a>Excluindo dados

O `IMobileServiceTable.DeleteAsync` método é usado para excluir dados de uma tabela com os aplicativos móveis do Azure, conforme mostrado no exemplo de código a seguir:

```csharp
public async Task DeleteTaskAsync (TodoItem item)
{
  ...
  await todoTable.DeleteAsync(item);
  ...
}
```

Ao fazer uma solicitação de exclusão, uma ID deve ser especificada para que o aplicativo do Azure Mobile sinstance possa identificar os dados a ser excluído. Esse valor de ID é armazenado no `TodoItem.ID` propriedade. Se a solicitação de exclusão não contém uma ID, não é possível para a instância os aplicativos móveis do Azure determinar os dados a ser excluído e, portanto uma `MobileServiceInvalidOperationException` será lançada.

## <a name="summary"></a>Resumo

Este artigo explicou como usar o [cliente SDK do Azure Mobile](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) para consultar, inserir, atualizar e excluir dados armazenados em uma tabela em uma instância de aplicativos móveis do Azure. O SDK fornece o `MobileServiceClient` classe que é usado por um aplicativo xamarin. Forms para acessar a instância os aplicativos móveis do Azure.


## <a name="related-links"></a>Links relacionados

- [TodoAzure (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzure/)
- [Criar um aplicativo xamarin. Forms](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started/)
- [SDK de cliente móvel do Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [MobileServiceClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx)
