---
title: "Versões anteriores fornece compatibilidade com o pacote de suporte do Android"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7511D2F8-2B4F-4200-C74E-E967153B2E8D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/12/2017
ms.openlocfilehash: f09aae1445cfcf9f4225af3de37b65ebb5a1b6b2
ms.sourcegitcommit: 028936cd2fe547963c1cf82343c3ee16f658089a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2018
---
# <a name="providing-backwards-compatibility-with-the-android-support-package"></a>Versões anteriores fornece compatibilidade com o pacote de suporte do Android

A utilidade dos fragmentos deve ser limitado sem compatibilidade com previamente Android dispositivos (API nível 11) 3.0 com versões anteriores. Para fornecer esse recurso, introduzido Google o [biblioteca de suporte](http://developer.android.com/sdk/compatibility-library.html) (chamado originalmente o *biblioteca compatibilidade Android* quando ele foi lançado) qual backports algumas das APIs de versões mais recentes do Android para versões mais antigas do Android. É o pacote de suporte Android que permite que dispositivos que executam o Android 1.6 (API nível 4) 2.3.3 Android. (API nível 10).

> [!NOTE]
> Somente o `ListFragment` e `DialogFragment` estão disponíveis por meio do pacote de suporte Android. Nenhuma das outras fragmento subclasses, como o `PreferenceFragment,` têm suporte no pacote de suporte do Android. Elas não funcionarão em 3.0 aplicativos previamente Android. 


## <a name="adding-the-support-package"></a>Adicionar o pacote de suporte

O pacote de suporte Android não é automaticamente adicionado a um aplicativo xamarin. Xamarin fornece o [pacote do NuGet biblioteca de suporte Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) para simplificar a adição das bibliotecas de suporte a um aplicativo xamarin. Para incluir os pacotes de suporte para o xamarin aplicativo incluir o [biblioteca de suporte Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) componente em seu projeto de xamarin, conforme ilustrado na captura de tela a seguir: 

[![Pacote de v4 de captura de tela de biblioteca de suporte Android que está sendo adicionado ao projeto](providing-backwards-compatibility-images/02-sml.png)](providing-backwards-compatibility-images/02.png#lightbox)

Depois de executar essas etapas, é possível usar os fragmentos em versões anteriores do Android. As APIs de fragmento funcionará o agora mesmo nessas versões anteriores, com as seguintes exceções: 

-   **Alterar a versão mínima do Android** &ndash; o aplicativo não precisa mais destino Android 3.0 ou superior, conforme mostrado abaixo: 

    [![Captura de tela de mínimo Android destino definido no manifesto do Android](providing-backwards-compatibility-images/03-sml.png)](providing-backwards-compatibility-images/03.png#lightbox)

-   **Estender FragmentActivity** &ndash; as atividades que estão hospedando fragmentos agora deve herdar de `Android.Support.V4.App.FragmentActivity` e não de `Android.App.Activity` . 

-   **Atualizar Namespaces** &ndash; Classes que herdam de `Android.App.Fragment` agora deve herdar de `Android.Support.V4.App.Fragment` . Remover o uso instrução " `using Android.App;` " na parte superior do código-fonte de arquivo e substituí-lo por " `using Android.Support.V4.App` ". 

-   **Use SupportFragmentManager** &ndash; `Android.Support.V4.App.FragmentActivity` expõe um `SupportingFragmentManager` propriedade deve ser usada para obter uma referência para o `FragmentManager` . Por exemplo: 

```csharp
FragmentTransaction fragmentTx = this.SupportingFragmentManager.BeginTransaction();
DetailsFragment detailsFrag = new DetailsFragment();
fragmentTx.Add(Resource.Id.fragment_container, detailsFrag);
fragmentTx.Commit();
```

Com essas alterações, será possível executar um aplicativo baseado no fragmento no Android 1.6 ou 2. x, bem como em Honeycomb e Sandwich sorvete. 


## <a name="related-links"></a>Links relacionados

- [Suporte do Android biblioteca v4 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
