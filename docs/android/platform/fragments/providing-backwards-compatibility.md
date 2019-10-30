---
title: Fornecendo compatibilidade com versões anteriores com o pacote de suporte do Android
ms.prod: xamarin
ms.assetid: 7511D2F8-2B4F-4200-C74E-E967153B2E8D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/12/2017
ms.openlocfilehash: c32d666da1347b947c55209ed7c7ec31a97a70e0
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027293"
---
# <a name="providing-backwards-compatibility-with-the-android-support-package"></a>Fornecendo compatibilidade com versões anteriores com o pacote de suporte do Android

A utilidade dos fragmentos seria limitada sem compatibilidade com versões anteriores do Android 3,0 (nível da API 11). Para fornecer esse recurso, o Google introduziu a [biblioteca de suporte](https://developer.android.com/sdk/compatibility-library.html) (originalmente chamada de *biblioteca de compatibilidade do Android* quando foi lançada), que reporta algumas das APIs de versões mais recentes do Android para versões mais antigas do Android. É o pacote de suporte do Android que habilita dispositivos que executam o Android 1,6 (API nível 4) para Android 2.3.3. (Nível de API 10).

> [!NOTE]
> Somente os `ListFragment` e os `DialogFragment` estão disponíveis por meio do pacote de suporte do Android. Nenhuma das outras subclasses de fragmento, como a `PreferenceFragment,`, tem suporte no pacote de suporte do Android. Eles não funcionarão em aplicativos anteriores ao Android 3,0. 

## <a name="adding-the-support-package"></a>Adicionando o pacote de suporte

O pacote de suporte do Android não é adicionado automaticamente a um aplicativo Xamarin. Android. O Xamarin fornece o [pacote NuGet da biblioteca de suporte do Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) para simplificar a adição das bibliotecas de suporte a um aplicativo Xamarin. Android. Para incluir os pacotes de suporte em seu aplicativo Xamarin. Android, inclua o componente de [suporte do Android na biblioteca v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) em seu projeto Xamarin. Android, conforme ilustrado na seguinte captura de tela: 

[![captura de tela do pacote v4 de biblioteca de suporte do Android que está sendo adicionado ao projeto](providing-backwards-compatibility-images/02-sml.png)](providing-backwards-compatibility-images/02.png#lightbox)

Depois que essas etapas forem executadas, será possível usar fragmentos em versões anteriores do Android. As APIs de fragmento funcionarão da mesma forma agora nestas versões anteriores, com as seguintes exceções: 

- **Altere a versão mínima do Android** &ndash; o aplicativo não precisar mais ter como destino o Android 3,0 ou superior, conforme mostrado abaixo: 

    [![captura de tela do destino do Android mínimo que está sendo definido no manifesto do Android](providing-backwards-compatibility-images/03-sml.png)](providing-backwards-compatibility-images/03.png#lightbox)

- **Estender FragmentActivity** &ndash; as atividades que estão hospedando fragmentos agora devem herdar de `Android.Support.V4.App.FragmentActivity` e não de `Android.App.Activity`. 

- Os **namespaces de atualização** &ndash; classes que herdam de `Android.App.Fragment` agora devem herdar de `Android.Support.V4.App.Fragment`. Remova a instrução using "`using Android.App;`" na parte superior do arquivo de código-fonte e substitua-a por "`using Android.Support.V4.App`". 

- **Use SupportFragmentManager** &ndash; `Android.Support.V4.App.FragmentActivity` expõe uma propriedade `SupportingFragmentManager` que deve ser usada para obter uma referência à `FragmentManager`. Por exemplo: 

```csharp
FragmentTransaction fragmentTx = this.SupportingFragmentManager.BeginTransaction();
DetailsFragment detailsFrag = new DetailsFragment();
fragmentTx.Add(Resource.Id.fragment_container, detailsFrag);
fragmentTx.Commit();
```

Com essas alterações em vigor, será possível executar um aplicativo baseado em fragmento no Android 1,6 ou 2. x, bem como no Honeycomb e no sanduíche de sorvete. 

## <a name="related-links"></a>Links relacionados

- [NuGet v4 de biblioteca de suporte do Android](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
