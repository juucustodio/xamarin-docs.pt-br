---
title: Gerenciando fragmentos
ms.prod: xamarin
ms.assetid: 02C5E8F0-32EF-4FD9-DC8B-04650E20722C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/07/2018
ms.openlocfilehash: 3733ed37abe9604e77529db4864f601d2b473280
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "73027326"
---
# <a name="managing-fragments"></a>Gerenciando fragmentos

Para ajudar no gerenciamento de fragmentos, o Android fornece a classe `FragmentManager`. Cada atividade tem uma instância de `Android.App.FragmentManager` que encontrará ou alterará dinamicamente seus fragmentos. Cada conjunto dessas alterações é conhecido como uma *transação*e é executado usando uma das APIs contidas na classe `Android.App.FragmentTransation`, que é gerenciada pelo `FragmentManager`. Uma atividade pode iniciar uma transação como esta:

```csharp
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
```

Essas alterações nos fragmentos são executadas na instância de `FragmentTransaction` usando métodos como `Add()`, `Remove(),` e `Replace().` as alterações são aplicadas usando `Commit()`. As alterações em uma transação não são executadas imediatamente.
Em vez disso, eles são agendados para serem executados no thread da interface do usuário da atividade assim que possível.

O exemplo a seguir mostra como adicionar um fragmento a um contêiner existente:

```csharp
// Create a new fragment and a transaction.
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
DetailsFragment aDifferentDetailsFrag = new DetailsFragment();

// The fragment will have the ID of Resource.Id.fragment_container.
fragmentTx.Add(Resource.Id.fragment_container, aDifferentDetailsFrag);

// Commit the transaction.
fragmentTx.Commit();
```

Se uma transação for confirmada depois que `Activity.OnSaveInstanceState()` for chamado, uma exceção será lançada. Isso acontece porque, quando a atividade salva seu estado, o Android também salva o estado de quaisquer fragmentos hospedados. Se qualquer transação de fragmento for confirmada após esse ponto, o estado dessas transações será perdido quando a atividade for restaurada.

É possível salvar as transações de fragmento na [pilha voltar](https://developer.android.com/guide/topics/fundamentals/tasks-and-back-stack.html) da atividade fazendo uma chamada para `FragmentTransaction.AddToBackStack()`. Isso permite que o usuário navegue para trás por meio de alterações de fragmento quando o botão **voltar** é pressionado. Sem uma chamada para esse método, os fragmentos removidos serão destruídos e não estarão disponíveis se o usuário navegar de volta pela atividade.

O exemplo a seguir mostra como usar o método `AddToBackStack` de uma `FragmentTransaction` para substituir um fragmento, preservando o estado do primeiro fragmento na pilha voltar:

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

O *fragmentomanager* conhece todos os fragmentos anexados a uma atividade e fornece dois métodos para ajudar a encontrar esses fragmentos:

- **FindFragmentById** &ndash; esse método encontrará um fragmento usando a ID que foi especificada no arquivo de layout ou a ID do contêiner quando o fragmento foi adicionado como parte de uma transação.

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
