---
title: Gerenciando fragmentos
ms.prod: xamarin
ms.assetid: 02C5E8F0-32EF-4FD9-DC8B-04650E20722C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/07/2018
ms.openlocfilehash: b39067b0cb1bbb344866761042db0125fd4a4dc5
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="managing-fragments"></a>Gerenciando fragmentos

Para ajudar a gerenciar os fragmentos, Android fornece o `FragmentManager` classe. Cada atividade tem uma instância de `Android.App.FragmentManager` que será encontrar ou alterar dinamicamente seus fragmentos. Cada conjunto dessas alterações é conhecido como um *transação*e é feita usando uma das APIs contidas na classe `Android.App.FragmentTransation`, que é gerenciado pelo `FragmentManager`. Uma atividade pode iniciar uma transação como este:

```csharp
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
```

Essas alterações para os fragmentos são executadas no `FragmentTransaction` instância usando métodos como `Add()`, `Remove(),` e `Replace().` as alterações são aplicadas usando `Commit()`. As alterações em uma transação não são realizadas imediatamente.
Em vez disso, eles são agendados para execução no thread de interface do usuário da atividade assim que possível.

O exemplo a seguir mostra como adicionar um fragmento para um contêiner existente:

```csharp
// Create a new fragment and a transaction.
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
DetailsFragment aDifferentDetailsFrag = new DetailsFragment();

// The fragment will have the ID of Resource.Id.fragment_container.
fragmentTx.Add(Resource.Id.fragment_container, aDifferentDetailsFrag);

// Commit the transaction.
fragmentTx.Commit();
```

Se uma transação for confirmada depois `Activity.OnSaveInstanceState()` é chamado, uma exceção será lançada. Isso ocorre porque quando a atividade salva seu estado, Android também salva o estado de qualquer fragmentos hospedados. Se as transações de fragmento são confirmadas depois deste ponto, o estado dessas transações serão perdido quando a atividade é restaurada.

É possível salvar as transações de fragmento da atividade [pilha voltar](http://developer.android.com/guide/topics/fundamentals/tasks-and-back-stack.html) fazendo uma chamada para `FragmentTransaction.AddToBackStack()`. Isso permite que o usuário navegar para trás por fragmento é alterado quando o **novamente** botão é pressionado. Sem uma chamada para esse método, os fragmentos são removidos serão destruídos e não estará disponíveis se o usuário navega através da atividade.

O exemplo a seguir mostra como usar o `AddToBackStack` método de um `FragmentTransaction` para substituir um fragmento, enquanto preserva o estado do fragmento primeiro na pilha voltar:

```csharp
// Create a new fragment and a transaction.
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
DetailsFragment aDifferentDetailsFrag = new DetailsFragment();

// Replace the fragment that is in the View fragment_container (if applicable).
fragmentTx.Replace(Resource.Id.fragment_container, aDifferentDetailsFrag);

// Add the transaction to the back stack.
fragmentTx.AddToBackStack(null);

// Commit the transaction.
fragmentTx.Commit();
```


## <a name="communicating-with-fragments"></a>Comunicação com fragmentos

O *FragmentManager* conhece todos os fragmentos que estão anexados a uma atividade e fornece dois métodos para ajudar a encontrar esses fragmentos:

-   **FindFragmentById** &ndash; esse método encontrará um fragmento usando a ID especificada no arquivo de layout ou a ID do contêiner quando o fragmento foi adicionado como parte de uma transação.

-   **FindFragmentByTag** &ndash; esse método é usado para localizar um fragmento que tem uma marca que foi fornecido no arquivo de layout ou que foi adicionado em uma transação.

Referência de fragmentos e atividades a `FragmentManager`, portanto, as mesmas técnicas usadas para comunicação e para trás entre eles. Um aplicativo pode localizar uma referência de fragmento usando um destes dois métodos, converter essa referência para o tipo apropriado e, em seguida, chamar diretamente métodos no fragmento. O trecho a seguir fornece um exemplo:

Também é possível para a atividade usar o `FragmentManager` para localizar fragmentos:

```csharp
var emailList = FragmentManager.FindFragmentById<EmailListFragment>(Resource.Id.email_list_fragment);
emailList.SomeCustomMethod(parameter1, parameter2);
```


### <a name="communicating-with-the-activity"></a>Comunicando-se com a atividade

É possível que um fragmento usar o `Fragment.Activity` propriedade para referenciar seu host. Convertendo a atividade de um tipo mais específico, é possível que uma atividade para chamar métodos e propriedades em seu host, conforme mostrado no exemplo a seguir:

```csharp
var myActivity = (MyActivity) this.Activity;
myActivity.SomeCustomMethod();
```
