---
title: Gerenciando fragmentos
ms.prod: xamarin
ms.assetid: 02C5E8F0-32EF-4FD9-DC8B-04650E20722C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/07/2018
ms.openlocfilehash: 3733ed37abe9604e77529db4864f601d2b473280
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027326"
---
# <a name="managing-fragments"></a>Gerenciando fragmentos

To help with managing Fragments, Android provides the `FragmentManager` class. Each Activity has an instance of `Android.App.FragmentManager` that will find or dynamically change its Fragments. Each set of these changes is known as a *transaction*, and is performed by using one of the APIs contained in the class `Android.App.FragmentTransation`, which is managed by the `FragmentManager`. An Activity may start a transaction like this:

```csharp
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
```

These changes to the Fragments are performed in the `FragmentTransaction` instance by using methods such as `Add()`, `Remove(),` and `Replace().` The changes are then applied by using `Commit()`. The changes in a transaction are not performed immediately.
Instead, they are scheduled to run on the Activity's UI thread as soon as possible.

The following example shows how to add a Fragment to an existing container:

```csharp
// Create a new fragment and a transaction.
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
DetailsFragment aDifferentDetailsFrag = new DetailsFragment();

// The fragment will have the ID of Resource.Id.fragment_container.
fragmentTx.Add(Resource.Id.fragment_container, aDifferentDetailsFrag);

// Commit the transaction.
fragmentTx.Commit();
```

If a transaction is committed after `Activity.OnSaveInstanceState()` is called, an exception will be thrown. This happens because when the Activity saves its state, Android also saves the state of any hosted Fragments. If any Fragment transactions are committed after this point, the state of these transactions will be lost when the Activity is restored.

It's possible to save the Fragment transactions to the Activity's [back stack](https://developer.android.com/guide/topics/fundamentals/tasks-and-back-stack.html) by making a call to `FragmentTransaction.AddToBackStack()`. This allows the user to navigate backwards through Fragment changes when the **Back** button is pressed. Without a call to this method, Fragments that are removed will be destroyed and will be unavailable if the user navigates back through the Activity.

The following example shows how to use the `AddToBackStack` method of a `FragmentTransaction` to replace one Fragment, while preserving the state of the first Fragment on the back stack:

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

## <a name="communicating-with-fragments"></a>Communicating with Fragments

The *FragmentManager* knows about all of the Fragments that are attached to an Activity and provides two methods to help find these Fragments:

- **FindFragmentById** &ndash; This method will find a Fragment   by using the ID that was specified in the layout file or the container   ID when the Fragment was added as part of a transaction.

- **FindFragmentByTag** &ndash; esse método é usado para localizar um fragmento que tem uma marca que foi fornecida no arquivo de layout ou que foi adicionada em uma transação.

Os fragmentos e as atividades fazem referência à `FragmentManager`, portanto, as mesmas técnicas são usadas para fazer a comunicação entre elas. Um aplicativo pode encontrar um fragmento de referência usando um desses dois métodos, converter essa referência para o tipo apropriado e, em seguida, chamar diretamente os métodos no fragmento. O trecho a seguir fornece um exemplo:

Também é possível que a atividade use o `FragmentManager` para localizar fragmentos:

```csharp
var emailList = FragmentManager.FindFragmentById<EmailListFragment>(Resource.Id.email_list_fragment);
emailList.SomeCustomMethod(parameter1, parameter2);
```

### <a name="communicating-with-the-activity"></a>Comunicando-se com a atividade

É possível que um fragmento use a propriedade `Fragment.Activity` para referenciar seu host. Ao converter a atividade para um tipo mais específico, é possível que uma atividade Chame métodos e propriedades em seu host, conforme mostrado no exemplo a seguir:

```csharp
var myActivity = (MyActivity) this.Activity;
myActivity.SomeCustomMethod();
```
