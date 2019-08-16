---
title: Fornecendo compatibilidade com versões anteriores com o pacote de suporte do Android
ms.prod: xamarin
ms.assetid: 7511D2F8-2B4F-4200-C74E-E967153B2E8D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/12/2017
ms.openlocfilehash: f1f886f0e4c14f2da29342a2a651f91ed510fd25
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69524270"
---
# <a name="providing-backwards-compatibility-with-the-android-support-package"></a>Fornecendo compatibilidade com versões anteriores com o pacote de suporte do Android

A utilidade dos fragmentos seria limitada sem compatibilidade com versões anteriores do Android 3,0 (nível da API 11). Para fornecer esse recurso, o Google introduziu a [biblioteca de suporte](https://developer.android.com/sdk/compatibility-library.html) (originalmente chamada de *biblioteca de compatibilidade do Android* quando foi lançada), que reporta algumas das APIs de versões mais recentes do Android para versões mais antigas do Android. É o pacote de suporte do Android que habilita dispositivos que executam o Android 1,6 (API nível 4) para Android 2.3.3. (Nível de API 10).

> [!NOTE]
> Somente o `ListFragment` e o `DialogFragment` estão disponíveis por meio do pacote de suporte do Android. Nenhuma das outras subclasses de fragmento, como o `PreferenceFragment,` , tem suporte no pacote de suporte do Android. Eles não funcionarão em aplicativos anteriores ao Android 3,0. 


## <a name="adding-the-support-package"></a>Adicionando o pacote de suporte

O pacote de suporte do Android não é adicionado automaticamente a um aplicativo Xamarin. Android. O Xamarin fornece o [pacote NuGet da biblioteca de suporte do Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) para simplificar a adição das bibliotecas de suporte a um aplicativo Xamarin. Android. Para incluir os pacotes de suporte em seu aplicativo Xamarin. Android, inclua o componente de [suporte do Android na biblioteca v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) em seu projeto Xamarin. Android, conforme ilustrado na seguinte captura de tela: 

[![Captura de tela do pacote v4 de biblioteca de suporte do Android que está sendo adicionado ao projeto](providing-backwards-compatibility-images/02-sml.png)](providing-backwards-compatibility-images/02.png#lightbox)

Depois que essas etapas forem executadas, será possível usar fragmentos em versões anteriores do Android. As APIs de fragmento funcionarão da mesma forma agora nestas versões anteriores, com as seguintes exceções: 

- **Alterar a versão mínima do Android** &ndash; O aplicativo não precisa mais ter como destino o Android 3,0 ou superior, conforme mostrado abaixo: 

    [![Captura de tela do destino do Android mínimo que está sendo definido no manifesto do Android](providing-backwards-compatibility-images/03-sml.png)](providing-backwards-compatibility-images/03.png#lightbox)

- **Estender FragmentActivity** As atividades que estão hospedando fragmentos agora devem herdar de `Android.Support.V4.App.FragmentActivity` , e `Android.App.Activity` não de. &ndash; 

- **Atualizar namespaces** Classes que herdam `Android.App.Fragment` de devem agora herdar de `Android.Support.V4.App.Fragment`. &ndash; Remova a instrução using " `using Android.App;` " na parte superior do arquivo de código-fonte e substitua-a `using Android.Support.V4.App` por "". 

- **Usar SupportFragmentManager** expõe uma propriedade que deve ser usada para obter uma referência ao. `FragmentManager` &ndash; `Android.Support.V4.App.FragmentActivity` `SupportingFragmentManager` Por exemplo: 

```csharp
FragmentTransaction fragmentTx = this.SupportingFragmentManager.BeginTransaction();
DetailsFragment detailsFrag = new DetailsFragment();
fragmentTx.Add(Resource.Id.fragment_container, detailsFrag);
fragmentTx.Commit();
```

Com essas alterações em vigor, será possível executar um aplicativo baseado em fragmento no Android 1,6 ou 2. x, bem como no Honeycomb e no sanduíche de sorvete. 


## <a name="related-links"></a>Links relacionados

- [NuGet v4 de biblioteca de suporte do Android](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
