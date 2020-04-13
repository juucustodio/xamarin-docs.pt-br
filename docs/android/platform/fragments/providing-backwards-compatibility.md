---
title: Fornecendo retrocompatibilidade com o pacote de suporte para Android
ms.prod: xamarin
ms.assetid: 7511D2F8-2B4F-4200-C74E-E967153B2E8D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/12/2017
ms.openlocfilehash: c32d666da1347b947c55209ed7c7ec31a97a70e0
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027293"
---
# <a name="providing-backwards-compatibility-with-the-android-support-package"></a>Fornecendo retrocompatibilidade com o pacote de suporte para Android

A utilidade dos Fragmentos seria limitada sem retrocompatibilidade com dispositivos pré-Android 3.0 (API Nível 11). Para fornecer esse recurso, o Google introduziu a [Biblioteca de Suporte](https://developer.android.com/sdk/compatibility-library.html) (originalmente chamada de Biblioteca de Compatibilidade *androides* quando foi lançada) que retrocede algumas das APIs de versões mais recentes do Android para versões mais antigas do Android. É o Pacote de Suporte para Android que permite dispositivos que executam o Android 1.6 (Nível 4 da API) para o Android 2.3.3. (API nível 10).

> [!NOTE]
> Apenas `ListFragment` o `DialogFragment` e o estão disponíveis através do Pacote de Suporte para Android. Nenhuma das outras subclasses fragmentadas, como as `PreferenceFragment,` suportadas no Pacote de Suporte para Android. Eles não funcionarão em aplicativos pré-Android 3.0. 

## <a name="adding-the-support-package"></a>Adicionando o pacote de suporte

O Pacote de Suporte para Android não é adicionado automaticamente a um aplicativo Xamarin.Android. A Xamarin fornece o [pacote V4 NuGet da Biblioteca de Suporte para Android](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) para simplificar a adição das bibliotecas de suporte a um aplicativo Xamarin.Android. Para incluir os pacotes de suporte em seu aplicativo Xamarin.Android, inclua o componente [V4 da Biblioteca de Suporte android](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) no seu projeto Xamarin.Android, conforme ilustrado na captura de tela a seguir: 

[![Captura de tela do pacote v4 da Biblioteca de Suporte android a ser adicionado ao projeto](providing-backwards-compatibility-images/02-sml.png)](providing-backwards-compatibility-images/02.png#lightbox)

Depois que essas etapas foram executadas, torna-se possível usar Fragmentos em versões anteriores do Android. As APIs de fragmento funcionarão da mesma forma agora nestas versões anteriores, com as seguintes exceções: 

- **Alterar a versão mínima do** &ndash; Android O aplicativo não precisa mais ter como alvo o Android 3.0 ou superior, como mostrado abaixo: 

    [![Captura de tela do alvo mínimo do Android sendo definido no Android Manifest](providing-backwards-compatibility-images/03-sml.png)](providing-backwards-compatibility-images/03.png#lightbox)

- **Estender a atividade fragmentada** &ndash; As atividades `Android.Support.V4.App.FragmentActivity` que hospedam fragmentos devem agora herdar de , e não de `Android.App.Activity` . 

- **Atualizar namespaces** &ndash; Classes `Android.App.Fragment` que herdarem agora devem herdar de `Android.Support.V4.App.Fragment` . Remova a declaração de uso " `using Android.App;` " na parte `using Android.Support.V4.App` superior do arquivo de código fonte e substitua-a por " ". 

- **O Use SupportFragmentManager** &ndash; `Android.Support.V4.App.FragmentActivity` expõe uma `SupportingFragmentManager` propriedade que deve `FragmentManager` ser usada para obter uma referência ao . Por exemplo: 

```csharp
FragmentTransaction fragmentTx = this.SupportingFragmentManager.BeginTransaction();
DetailsFragment detailsFrag = new DetailsFragment();
fragmentTx.Add(Resource.Id.fragment_container, detailsFrag);
fragmentTx.Commit();
```

Com essas mudanças em vigor, será possível executar um aplicativo baseado em Fragmento no Android 1.6 ou 2.x, bem como no Honeycomb e Ice Cream Sandwich. 

## <a name="related-links"></a>Links relacionados

- [Biblioteca de suporte para Android v4 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
