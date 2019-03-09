---
title: Consumir um aplicativo móvel do Azure
description: Neste artigo, que só é aplicável aos aplicativos móveis do Azure que usam um back-end do Node. js, explica como consultar, inserir, atualizar e excluir os dados armazenados em uma tabela em uma instância de aplicativos móveis do Azure.
ms.prod: xamarin
ms.assetid: 2B3EFD0A-2922-437D-B151-4B4DE46E2095
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 8bd77ec4975fae3cc7245c5adc2b5ef18568b9e1
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57667575"
---
# <a name="consuming-an-azure-mobile-app"></a>Consumir um aplicativo móvel do Azure

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzure/)

_Aplicativos móveis do Azure permitem que você desenvolva aplicativos com back-ends dimensionável hospedado no serviço de aplicativo do Azure com suporte para autenticação móvel, sincronização offline e notificações por push. Neste artigo, que só é aplicável aos aplicativos móveis do Azure que usam um back-end do Node. js, explica como consultar, inserir, atualizar e excluir os dados armazenados em uma tabela em uma instância de aplicativos móveis do Azure._

> [!NOTE]
> Iniciando em 30 de junho, todos os novos aplicativos de móveis do Azure serão criados com o TLS 1.2 por padrão. Além disso, também é recomendado que os aplicativos móveis do Azure ser reconfigurados para usar o TLS 1.2. Para obter informações sobre como impor o TLS 1.2 no aplicativo móvel do Azure, consulte [versões impor o TLS](/azure/app-service/app-service-web-tutorial-custom-ssl#enforce-tls-versions). Para obter informações sobre como configurar projetos do Xamarin para usar TLS 1.2, consulte [Transport Layer Security (TLS) 1.2](~/cross-platform/app-fundamentals/transport-layer-security.md).

Para obter informações sobre como criar uma instância de aplicativos móveis do Azure que pode ser consumida por xamarin. Forms, consulte [criar um aplicativo xamarin. Forms](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started/). Depois de seguir essas instruções, o aplicativo de exemplo para download pode ser configurado para consumir a instância de aplicativos móveis do Azure, definindo o `Constants.ApplicationURL` para a URL da instância de aplicativos móveis do Azure. Em seguida, quando o aplicativo de exemplo for executado ele se conecte à instância de aplicativos móveis do Azure, conforme mostrado na seguinte captura de tela:

![](azure-images/portal.png "Aplicativo de exemplo")

Acesso a aplicativos móveis do Azure é por meio de [SDK do Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/), e todas as conexões do aplicativo de exemplo do xamarin. Forms para o Azure são feitas por HTTPS.

> [!NOTE]
> No iOS 9 e superior, a segurança de transporte de aplicativo (ATS) impõe conexões seguras entre recursos da internet (como o servidor de back-end do aplicativo) e o aplicativo, impedindo assim a divulgação acidental de informações confidenciais. Desde que o ATS é habilitado por padrão em aplicativos criados para o iOS 9, todas as conexões serão sujeitos a requisitos de segurança ATS. Se as conexões não atender a esses requisitos, eles falharão com uma exceção.
> ATS poderá ser aceito de se ele não é possível usar o `HTTPS` de protocolo e proteger a comunicação para recursos da internet. Isso pode ser feito atualizando o aplicativo **Info. plist** arquivo. Para obter mais informações, consulte [segurança de transporte de aplicativo](~/ios/app-fundamentals/ats.md).

## <a name="consuming-an-azure-mobile-app-instance"></a>Consumindo uma instância de aplicativo móvel do Azure

O [SDK do Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) fornece o `MobileServiceClient` classe, que é usado por um aplicativo xamarin. Forms para acessar a instância de aplicativos móveis do Azure, conforme mostrado no exemplo de código a seguir:

```csharp
IMobileServiceTable<TodoItem> todoTable;
MobileServiceClient client;

public TodoItemManager ()
{
  client = new MobileServiceClient (Constants.ApplicationURL);
  todoTable = client.GetTable<TodoItem> ();
}
```

Quando o `MobileServiceClient` instância é criada, uma URL de aplicativo deve ser especificada para identificar a instância de aplicativos móveis do Azure. Esse valor pode ser obtido a partir do painel para o aplicativo móvel na [Portal do Microsoft Azure](https://portal.azure.com/).

Uma referência para o `TodoItem` tabela armazenada na instância de aplicativos móveis do Azure deve ser obtida antes de operações podem ser executadas nessa tabela. Isso é feito chamando o `GetTable` método em de `MobileServiceClient` da instância, que retorna um `IMobileServiceTable<TodoItem>` referência.

### <a name="querying-data"></a>Consultar Dados

O conteúdo de uma tabela pode ser recuperado chamando o `IMobileServiceTable.ToEnumerableAsync` método que avalia a consulta e retorna os resultados de forma assíncrona. Dados também podem ser filtradas do lado do servidor, incluindo um `Where` cláusula na consulta. O `Where` cláusula aplica um predicado à consulta na tabela, a filtragem de linha conforme mostrado no exemplo de código a seguir:

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

Ao inserir dados na instância de aplicativos móveis do Azure, novas colunas serão geradas automaticamente na tabela conforme necessário, fornecido o esquema dinâmico está habilitado na instância de aplicativos móveis do Azure. O `IMobileServiceTable.InsertAsync` método é usado para inserir uma nova linha de dados na tabela especificada, conforme mostrado no exemplo de código a seguir:

```csharp
public async Task SaveTaskAsync (TodoItem item)
{
  ...
  await todoTable.InsertAsync (item);
  ...
}
```

Quando você faz uma solicitação de inserção, uma ID não deve ser especificada nos dados que está sendo passados para a instância de aplicativos móveis do Azure. Se a solicitação de inserção contém uma ID de um `MobileServiceInvalidOperationException` será lançada.

Após o `InsertAsync` método for concluído, a ID dos dados na instância de aplicativos móveis do Azure será preenchida na `TodoItem` instância no aplicativo xamarin. Forms.

### <a name="updating-data"></a>Atualizando dados

Ao atualizar dados na instância de aplicativos móveis do Azure, novas colunas serão geradas automaticamente na tabela conforme necessário, fornecido o esquema dinâmico está habilitado na instância de aplicativos móveis do Azure. O `IMobileServiceTable.UpdateAsync` método é usado para atualizar os dados existentes com novas informações, conforme mostrado no exemplo de código a seguir:

```csharp
public async Task SaveTaskAsync (TodoItem item)
{
  ...
  await todoTable.UpdateAsync (item);
  ...
}
```

Quando você faz uma solicitação de atualização, uma ID deve ser especificada para que a instância de aplicativos móveis do Azure possa identificar os dados a serem atualizados. Esse valor de ID é armazenado no `TodoItem.ID` propriedade. Se a solicitação de atualização não contém uma ID não é possível para a instância de aplicativos móveis do Azure determinar os dados a ser atualizado e, portanto, um `MobileServiceInvalidOperationException` será lançada.

### <a name="deleting-data"></a>Excluindo dados

O `IMobileServiceTable.DeleteAsync` método é usado para excluir dados de uma tabela de aplicativos móveis do Azure, conforme mostrado no exemplo de código a seguir:

```csharp
public async Task DeleteTaskAsync (TodoItem item)
{
  ...
  await todoTable.DeleteAsync(item);
  ...
}
```

Quando você faz uma solicitação de exclusão, uma ID deve ser especificada para que o aplicativo móvel do Azure sinstance possa identificar os dados a ser excluído. Esse valor de ID é armazenado no `TodoItem.ID` propriedade. Se a solicitação de exclusão não contém uma ID, não há nenhuma maneira para a instância de aplicativos móveis do Azure determinar os dados a serem excluídos e, portanto, um `MobileServiceInvalidOperationException` será lançada.

## <a name="summary"></a>Resumo

Este artigo explicou como usar o [SDK do Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) para consultar, inserir, atualizar e excluir os dados armazenados em uma tabela em uma instância de aplicativos móveis do Azure. O SDK fornece o `MobileServiceClient` classe que é usado por um aplicativo xamarin. Forms para acessar a instância de aplicativos móveis do Azure.


## <a name="related-links"></a>Links relacionados

- [TodoAzure (amostra)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzure/)
- [Criar um aplicativo xamarin. Forms](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started/)
- [SDK de cliente móvel do Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [MobileServiceClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx)
