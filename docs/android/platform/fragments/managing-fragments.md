---
title: Gerenciando fragmentos
ms.prod: xamarin
ms.assetid: 02C5E8F0-32EF-4FD9-DC8B-04650E20722C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/07/2018
ms.openlocfilehash: 3733ed37abe9604e77529db4864f601d2b473280
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027326"
---
# <a name="managing-fragments"></a>Gerenciando fragmentos

Para ajudar no gerenciamento de `FragmentManager` Fragmentos, o Android fornece a classe. Cada Atividade tem `Android.App.FragmentManager` uma instância que irá encontrar ou alterar dinamicamente seus Fragmentos. Cada conjunto dessas alterações é conhecido como *transação*, e é realizado `Android.App.FragmentTransation`usando uma das `FragmentManager`APIs contidas na classe, que é gerenciada pelo . Uma atividade pode iniciar uma transação como esta:

```csharp
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
```

Essas alterações nos Fragmentos `FragmentTransaction` são realizadas na `Add()`instância `Remove(),` `Replace().` usando métodos como , `Commit()`e as alterações são então aplicadas usando . As alterações em uma transação não são realizadas imediatamente.
Em vez disso, eles estão programados para serem executados no segmento de ida e rei da atividade o mais rápido possível.

O exemplo a seguir mostra como adicionar um Fragmento a um contêiner existente:

```csharp
// Create a new fragment and a transaction.
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
DetailsFragment aDifferentDetailsFrag = new DetailsFragment();

// The fragment will have the ID of Resource.Id.fragment_container.
fragmentTx.Add(Resource.Id.fragment_container, aDifferentDetailsFrag);

// Commit the transaction.
fragmentTx.Commit();
```

Se uma transação `Activity.OnSaveInstanceState()` for cometida depois de ser chamada, uma exceção será lançada. Isso acontece porque quando a Atividade salva seu estado, o Android também salva o estado de quaisquer Fragmentos hospedados. Se quaisquer transações fragmentadas forem cometidas após este ponto, o estado dessas transações será perdido quando a Atividade for restaurada.

É possível salvar as transações fragmentadas na [pilha](https://developer.android.com/guide/topics/fundamentals/tasks-and-back-stack.html) de costas `FragmentTransaction.AddToBackStack()`da Atividade fazendo uma chamada para . Isso permite que o usuário navegue para trás através das alterações de fragmento quando o botão **Voltar** é pressionado. Sem uma chamada para este método, fragmentos removidos serão destruídos e não estarão disponíveis se o usuário navegar de volta através da Atividade.

O exemplo a seguir `AddToBackStack` mostra como `FragmentTransaction` usar o método de a para substituir um Fragmento, preservando o estado do primeiro Fragmento na pilha traseira:

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

## <a name="communicating-with-fragments"></a>Comunicação com Fragmentos

O *FragmentManager* sabe sobre todos os fragmentos que estão conectados a uma Atividade e fornece dois métodos para ajudar a encontrar esses Fragmentos:

- **FindFragmentById** &ndash; Este método encontrará um Fragmento usando o ID especificado no arquivo de layout ou no ID do contêiner quando o Fragmento foi adicionado como parte de uma transação.

- **FindFragmentByTag** &ndash; Este método é usado para encontrar um Fragmento que tenha uma tag que foi fornecida no arquivo de layout ou que foi adicionada em uma transação.

Tanto fragmentos quanto `FragmentManager`atividades fazem referência ao , de modo que as mesmas técnicas são usadas para se comunicar entre eles. Um aplicativo pode encontrar um Fragmento de referência usando um desses dois métodos, lançar essa referência ao tipo apropriado e, em seguida, chamar diretamente métodos no Fragmento. O seguinte trecho fornece um exemplo:

Também é possível que a `FragmentManager` Atividade use o para encontrar Fragmentos:

```csharp
var emailList = FragmentManager.FindFragmentById<EmailListFragment>(Resource.Id.email_list_fragment);
emailList.SomeCustomMethod(parameter1, parameter2);
```

### <a name="communicating-with-the-activity"></a>Comunicação com a Atividade

É possível que um Fragmento `Fragment.Activity` use a propriedade para referenciar seu hospedeiro. Ao lançar a Atividade para um tipo mais específico, é possível que uma Atividade chame métodos e propriedades em seu host, como mostrado no exemplo a seguir:

```csharp
var myActivity = (MyActivity) this.Activity;
myActivity.SomeCustomMethod();
```
