---
title: Com versões anteriores fornece compatibilidade com o pacote de suporte do Android
ms.prod: xamarin
ms.assetid: 7511D2F8-2B4F-4200-C74E-E967153B2E8D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/12/2017
ms.openlocfilehash: 48ef40ce8560fd9fbb842dde70622d968591ab98
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61022217"
---
# <a name="providing-backwards-compatibility-with-the-android-support-package"></a>Com versões anteriores fornece compatibilidade com o pacote de suporte do Android

A utilidade dos fragmentos seria limitado sem compatibilidade com Android anterior dispositivos (API nível 11) 3.0 com versões anteriores. Para fornecer essa capacidade, o Google apresentou o [biblioteca de suporte](https://developer.android.com/sdk/compatibility-library.html) (originalmente chamado de *Android Compatibility Library* quando ele foi lançado) quais backports algumas das APIs de versões mais recentes do Android versões mais antigas do Android. É o pacote de suporte de Android que permite que os dispositivos que executam o Android 1.6 (API nível 4) para Android 2.3.3. (API nível 10).

> [!NOTE]
> Somente o `ListFragment` e o `DialogFragment` estão disponíveis por meio do pacote de suporte do Android. Nenhuma das outras fragmento subclasses, tais como o `PreferenceFragment,` têm suporte no pacote de suporte do Android. Elas não funcionarão em aplicativos do Android previamente 3.0. 


## <a name="adding-the-support-package"></a>Adicionando o pacote de suporte

Pacote de suporte do Android não é automaticamente adicionado a um aplicativo xamarin. Android. O Xamarin fornece o [pacote do NuGet do suporte a biblioteca do Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) para simplificar a adição as bibliotecas de suporte a um aplicativo xamarin. Android. Para incluir os pacotes de suporte para o xamarin. Android aplicativo incluir as [dar suporte a biblioteca do Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) componente em seu projeto xamarin. Android, conforme ilustrado na captura de tela a seguir: 

[![Pacote de v4 de captura de tela da biblioteca de suporte ao Android que está sendo adicionado ao projeto](providing-backwards-compatibility-images/02-sml.png)](providing-backwards-compatibility-images/02.png#lightbox)

Depois que essas etapas foram executadas, se torna possível usar os fragmentos em versões anteriores do Android. As APIs de fragmento funcionará o agora mesmo nessas versões anteriores, com as seguintes exceções: 

-   **Alterar a versão mínima do Android** &ndash; o aplicativo não precisa mais direcionar ao Android 3.0 ou superior, conforme mostrado abaixo: 

    [![Captura de tela de mínima do Android destino que está sendo definido no manifesto do Android](providing-backwards-compatibility-images/03-sml.png)](providing-backwards-compatibility-images/03.png#lightbox)

-   **Estender FragmentActivity** &ndash; as atividades que estão hospedando fragmentos agora devem herdar `Android.Support.V4.App.FragmentActivity` e não de `Android.App.Activity` . 

-   **Atualizar os Namespaces** &ndash; Classes que herdam `Android.App.Fragment` agora deve herdar de `Android.Support.V4.App.Fragment` . Remova o uso instrução " `using Android.App;` " na parte superior do código-fonte de arquivo e substituí-lo por " `using Android.Support.V4.App` ". 

-   **Use SupportFragmentManager** &ndash; `Android.Support.V4.App.FragmentActivity` expõe uma `SupportingFragmentManager` propriedade que deve ser usada para obter uma referência para o `FragmentManager` . Por exemplo: 

```csharp
FragmentTransaction fragmentTx = this.SupportingFragmentManager.BeginTransaction();
DetailsFragment detailsFrag = new DetailsFragment();
fragmentTx.Add(Resource.Id.fragment_container, detailsFrag);
fragmentTx.Commit();
```

Com essas alterações, será possível executar um aplicativo baseado no fragmento no Android 1.6 ou 2. x, bem como em Honeycomb e Ice Cream Sandwich. 


## <a name="related-links"></a>Links relacionados

- [NuGet da biblioteca de suporte do Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
