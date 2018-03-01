---
title: "Suporte Android pré-Honeycomb usando pacotes de suporte"
ms.topic: article
ms.prod: xamarin
ms.assetid: DACD0C14-5DDF-7BDE-6844-80550D301307
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: 9ffa3733908affb8a6f3cc5a1ae2e83c5a15f0f0
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="supporting-pre-honeycomb-android-using-support-packages"></a>Suporte Android pré-Honeycomb usando pacotes de suporte

O *pacote de suporte Android* consiste em bibliotecas de volta a porta alguns a nova API &ndash; como fragmentos &ndash; para versões mais antigas do Android. Assim, adicionando o pacote de suporte Android, fazermos nosso aplicativo em dispositivos Android 2.3, conforme mostrado pelo telas a seguir:

![Captura de tela de instruções passo a passo de fragmentos](supporting-pre-honeycomb-images/00.png)

![Captura de tela de atividade de detalhes](supporting-pre-honeycomb-images/01.png)

<a name="Adding_the_Support_Package" />

## <a name="adding-the-support-package"></a>Adicionar o pacote de suporte

O pacote de suporte Android não é automaticamente adicionado a um aplicativo xamarin. Xamarin fornece o [pacote do NuGet biblioteca de suporte Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) para simplificar a adição das bibliotecas de suporte a um aplicativo xamarin.
Para incluir os pacotes de suporte para o xamarin aplicativo incluir o [biblioteca de suporte Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) componente em seu projeto de xamarin, conforme ilustrado na captura de tela a seguir:

![Adição do pacote de biblioteca de suporte Android v4](supporting-pre-honeycomb-images/02.png)

Depois que o pacote tiver sido adicionado, altere a estrutura de destino para o Android 2.2 ou superior:

![Captura de tela de alteração do nível de API da estrutura de destino](supporting-pre-honeycomb-images/03.png)

Além disso, certifique-se de que a versão mínima do Android tem como destino o mesmo nível de API:

![Captura de tela de definir a versão do Android mínimo](supporting-pre-honeycomb-images/04.png)


<a name="Change_MainActivity_to_derive_from_FragmentActivity" />

### <a name="change-mainactivity-to-derive-from-fragmentactivity"></a>Alterar MainActivity derivar FragmentActivity

Qualquer atividade que usa fragmentos deve herdar de `Xamarin.Support.V4.App.FragmentActivity`. Essa classe é uma parte necessária do pacote de suporte, pois ela permite que os fragmentos para ser hospedado por atividades, independentemente da versão do Android. `MainActivity` requer apenas uma pequena alteração – ele agora deve herdar de `Android.Support.V4.App.FragmentActivity`:

```csharp
[Activity(Label = "Fragments Walkthrough", MainLauncher = true, Icon = "@drawable/launcher")]
public class MainActivity : Android.Support.V4.App.FragmentActivity
{
   protected override void OnCreate(Bundle bundle)
   {
       base.OnCreate(bundle);
       SetContentView(Resource.Layout.activity_main);
   }
}
```

<a name="Change_DetailsActivity_to_derive_from_FragmentActivity" />

### <a name="change-detailsactivity-to-derive-from-fragmentactivity"></a>Alterar DetailsActivity derivar FragmentActivity

`DetailsActivity` também deve ser alterada de um `Activity` para um `FragmentActivity`. Como `FragmentManager` é não é compatível com as versões de pré-lançamento Honeycomb do Android, o pacote de suporte Android inclui uma classe wrapper, `SupportFragmentManager`, que fornece compatibilidade com versões anteriores. Cada `FragmentActivity` tem um `SupportFragmentManager` propriedade e `DetailsActivity` é alterado para usar o `SupportFragmentManager` em vez do `FragmentManager`:

```csharp
[Activity(Label = "Details Activity")]
public class DetailsActivity : Android.Support.V4.App.FragmentActivity
{
   protected override void OnCreate(Bundle bundle)
   {
       base.OnCreate(bundle);
       var index = Intent.Extras.GetInt("index", 0);
       var details = DetailsFragment.NewInstance(index);
       var fragmentTransaction = SupportFragmentManager.BeginTransaction(); // Notice the change from FragmentManager to SupportFragmentManager
       fragmentTransaction.Add(Android.Resource.Id.Content, details);
       fragmentTransaction.Commit();
   }
}
```

Depois de concluída, essas alterações agora temos um aplicativo que pode ser executado no Android 1.6 e superior e que usa fragmentos para ajustar a nossa interface do usuário para o tamanho do nosso dispositivo de destino.


## <a name="related-links"></a>Links relacionados

- [Suporte a Android v4 de biblioteca](https://www.nuget.org/packages/Xamarin.Android.Support.v4)
