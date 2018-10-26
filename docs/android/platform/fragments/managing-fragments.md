---
title: Gerenciar fragmentos
ms.prod: xamarin
ms.assetid: 02C5E8F0-32EF-4FD9-DC8B-04650E20722C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/07/2018
ms.openlocfilehash: 107877d0e92d3a46101812b78bc0b414c0fbb320
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105461"
---
# <a name="managing-fragments"></a>Gerenciar fragmentos

Para ajudar com o gerenciamento de fragmentos, o Android fornece o `FragmentManager` classe. Cada atividade tem uma instância de `Android.App.FragmentManager` que irá localizar ou alterar dinamicamente seus fragmentos. Cada conjunto dessas alterações é conhecido como um *transação*e é executado usando uma das APIs contidas na classe `Android.App.FragmentTransation`, que é gerenciado pelo `FragmentManager`. Uma atividade pode iniciar uma transação como este:

```csharp
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
```

Essas alterações para os fragmentos são realizadas na `FragmentTransaction` instância usando os métodos, como `Add()`, `Remove(),` e `Replace().` as alterações são aplicadas usando `Commit()`. As alterações em uma transação não são executadas imediatamente.
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

Se uma transação for confirmada depois `Activity.OnSaveInstanceState()` é chamado, uma exceção será gerada. Isso acontece porque quando a atividade salva seu estado, o Android também salva o estado de qualquer fragmentos hospedados. Se todas as transações de fragmento são confirmadas depois deste ponto, o estado dessas transações serão perdido quando a atividade é restaurada.

É possível salvar as transações de fragmento da atividade [pilha voltar](http://developer.android.com/guide/topics/fundamentals/tasks-and-back-stack.html) fazendo uma chamada para `FragmentTransaction.AddToBackStack()`. Isso permite ao usuário navegar para trás por fragmento é alterado quando o **volta** é pressionado. Sem uma chamada para esse método, os fragmentos são removidos serão destruídos e não estará disponíveis se o usuário navega de volta por meio da atividade.

O exemplo a seguir mostra como usar o `AddToBackStack` método de um `FragmentTransaction` para substituir um fragmento, enquanto preserva o estado do primeiro fragmento na pilha voltar:

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


## <a name="communicating-with-fragments"></a>Comunicando-se com fragmentos

O *FragmentManager* sabe sobre todos os fragmentos que estão anexados a uma atividade e fornece dois métodos para ajudar a encontrar esses fragmentos:

-   **FindFragmentById** &ndash; este método encontrará um fragmento usando a ID especificada no arquivo de layout ou a ID do contêiner quando o fragmento foi adicionado como parte de uma transação.

-   **FindFragmentByTag** &ndash; esse método é usado para localizar um fragmento que tem uma marca que foi fornecido no arquivo de layout ou que foi adicionado em uma transação.

Referência de fragmentos e atividades a `FragmentManager`, portanto, as mesmas técnicas são usadas para se comunicar e para trás entre eles. Um aplicativo pode encontrar uma referência do fragmento usando um destes dois métodos, converter essa referência para o tipo apropriado e, em seguida, chamar métodos diretamente no fragmento. O trecho a seguir fornece um exemplo:

Também é possível que a atividade para usar o `FragmentManager` para localizar os fragmentos:

```csharp
var emailList = FragmentManager.FindFragmentById<EmailListFragment>(Resource.Id.email_list_fragment);
emailList.SomeCustomMethod(parameter1, parameter2);
```


### <a name="communicating-with-the-activity"></a>Comunicando-se com a atividade

É possível que um fragmento usar o `Fragment.Activity` propriedade a referenciar seu host. Convertendo a atividade a um tipo mais específico, é possível que uma atividade para chamar métodos e propriedades em seu host, conforme mostrado no exemplo a seguir:

```csharp
var myActivity = (MyActivity) this.Activity;
myActivity.SomeCustomMethod();
```
