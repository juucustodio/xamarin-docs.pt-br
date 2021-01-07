---
title: Como fazer migrar meu aplicativo para Xamarin.Forms 5,0?
description: Como migrar seu aplicativo para o Xamarin.Forms 5,0, com foco no Android no UWP.
ms.assetid: AD04FEE9-B8F5-4CA5-AB31-EF1225867E4B
ms.prod: xamarin
ms.technology: xamarin-forms
ms.topic: troubleshooting
author: davidbritch
ms.author: dabritch
ms.date: 10/20/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8f93d20dac789abed57f8f41bf41778ad50a5fb5
ms.sourcegitcommit: 995ee23d93e08dceb8754cc6c682cd2f4594345b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972312"
---
# <a name="how-do-i-migrate-my-app-to-no-locxamarinforms-50"></a>Como fazer migrar meu aplicativo para Xamarin.Forms 5,0?

Xamarin.Forms 5,0 inclui as seguintes alterações significativas:

- `Expander` foi movido para o Xamarin Community Toolkit. Para obter mais informações, consulte [recursos movidos de Xamarin.Forms ](https://github.com/xamarin/XamarinCommunityToolkit/wiki/Features-moved-from-Xamarin.Forms).
- `MediaElement` foi movido para o Xamarin Community Toolkit. Para obter mais informações, consulte [recursos movidos de Xamarin.Forms ](https://github.com/xamarin/XamarinCommunityToolkit/wiki/Features-moved-from-Xamarin.Forms).
- As páginas de DataPage e os projetos associados foram removidos do Xamarin.Forms .
- `MasterDetailPage` foi renomeado para `FlyoutPage`. Da mesma forma, a `MasterBehavior` Enumeração foi renomeada para `FlyoutLayoutBehavior` .
- Referências a `UIWebView` foram removidas do Xamarin.Forms no Ios.
- O suporte para o Visual Studio 2017 foi removido.
- XFCorePostProcessor. Tasks foi removido. Este projeto injeta a IL para manter a Xamarin.Forms compatibilidade 2,5.

Além disso, os projetos do Android e do UWP criados com Xamarin.Forms 5,0 exigirão atualização.

## <a name="android"></a>Android

Os projetos do Android criados com Xamarin.Forms 5,0 exigem que você tenha instalado a plataforma AndroidX (Android 10,0) em seu ambiente de desenvolvimento. Isso pode ser feito com o Gerenciador de SDK do Android. Para obter mais informações sobre o AndroidX, consulte [migração Xamarin.Forms do AndroidX no ](~/xamarin-forms/platform/android/androidx-migration.md).

Em seguida, os projetos do Android precisarão de várias atualizações para serem criadas corretamente.

### <a name="minimum-targetframeworkversion"></a>TargetFrameworkVersion mínimo

Xamarin.Forms 5,0 requer uma versão de estrutura de destino mínima de 10,0 (AndroidX) para projetos Android. A versão da estrutura de destino pode ser definida no Visual Studio ou no arquivo Android. csproj:

```xml
<TargetFrameworkVersion>v10.0</TargetFrameworkVersion>
```

Um erro de compilação será produzido se esse requisito mínimo não for atendido:

```
error XF005: The $(TargetFrameworkVersion) for MyProject.Android (v9.0) is less than the minimum required $(TargetFrameworkVersion) for Xamarin.Forms (10.0). You need to increase the $(TargetFrameworkVersion) for MyProject.Android.
```

### <a name="minimum-targetsdkversion"></a>TargetSDKVersion mínimo

AndroidX exige que seu manifesto do Android defina o `targetSdkVersion` como 29 +:

```xml
<uses-sdk android:minSdkVersion="21" android:targetSdkVersion="29" />
```

Se você não fizer isso, isso fará com que o seja `targetSdkVersion` definido como `minSdkVersion` . Além disso, em algumas circunstâncias, um `Android.Views.InflateException` será produzido se o `targetSdkVersion` não estiver definido corretamente:

```
Android.View.InflateException has been thrown.

Binary XML file line #1 in com.companyname.myproject:layout/toolbar: Binary XML file line #1 in com.companyname.myproject:/layout/toolbar: Error inflating class android.support.v7.widget.Toolbar
```

> [!NOTE]
> No Visual Studio 2019, o manifesto do Android será atualizado automaticamente para especificar uma `targetSdkVersion` API 29 quando a versão da estrutura de destino for definida como v 10.0.

### <a name="automatic-migration-to-androidx"></a>Migração automática para o AndroidX

Se o seu projeto do Android fizer referência a qualquer biblioteca de suporte do Android, como dependências diretas ou transições, essas dependências e associações de biblioteca de suporte serão trocadas automaticamente com dependências AndroidX durante o processo de compilação. Para obter mais informações sobre a migração automática de AndroidX, consulte [migração automática no Xamarin.Forms ](~/xamarin-forms/platform/android/androidx-migration.md#automatic-migration-in-xamarinforms).

### <a name="manual-migration-to-androidx"></a>Migração manual para AndroidX

Se seu projeto do Android não tiver dependências diretas ou transitivas em bibliotecas de suporte do Android, mas ainda tentar consumir tipos de biblioteca de suporte por meio de código, você precisará migrar manualmente seu aplicativo para o AndroidX. Isso pode ser feito usando tipos AndroidX e removendo quaisquer arquivos AXML que você ainda não tenha personalizado.

> [!TIP]
> A migração manual para o AndroidX resultará no processo de compilação mais rápido para seu aplicativo.

#### <a name="use-androidx-types"></a>Usar tipos AndroidX

O AndroidX substitui as bibliotecas de suporte do Android e, portanto, todas as referências aos tipos de biblioteca de suporte do Android devem ser substituídas por referências a tipos AndroidX.

Isso pode ser feito atualizando suas `using` instruções para usar `AndroidX` namespaces, em vez de `Android.Support` namespaces. A tabela a seguir lista algumas das alterações de namespace comuns ao mudar das bibliotecas de suporte do Android para AndroidX:

| Namespace da biblioteca de suporte do Android | Namespace AndroidX |
| --- | --- |
| `Android.Support.V4.App` | `AndroidX.Core.App` |
| `Android.Support.V4.Content` | `AndroidX.Core.Content` |
| `Android.Support.V4.App` | `AndroidX.Fragment.App` |
| `Android.Support.V7.App` | `AndroidX.AppCompat.App` |
| `Android.Support.V7.Widget` | `AndroidX.AppCompat.Widget` |

Para obter uma lista completa de mapeamentos de classe de bibliotecas de suporte para AndroidX, consulte [suporte a mapeamentos de classe de biblioteca](https://developer.android.com/jetpack/androidx/migrate/class-mappings) em developer.Android.com.

#### <a name="remove-axml-files"></a>Remover arquivos AXML

Você deve excluir todos os arquivos AXML do seu projeto Android, desde que ele não use arquivos AXML personalizados. Após a exclusão, as seguintes linhas devem ser removidas da sua `MainActivity` classe:

```csharp
TabLayoutResource = Resource.Layout.Tabbar;
ToolbarResource = Resource.Layout.Toolbar;
```

> [!IMPORTANT]
> Os projetos do Android com arquivos AXML personalizados devem ser atualizados para que esses arquivos usem os tipos AndroidX.

## <a name="uwp"></a>UWP

Xamarin.Forms 5,0 recomenda uma versão de plataforma de destino de >= 10.0.18362.0 para projetos UWP. A versão da plataforma de destino pode ser definida no Visual Studio ou no arquivo UWP. csproj:

```xml
<TargetPlatformVersion Condition=" '$(TargetPlatformVersion)' == '' ">10.0.18362.0</TargetPlatformVersion>
```

Um aviso de compilação será produzido se seu projeto UWP usar uma versão de plataforma de destino inferior.

## <a name="related-links"></a>Links relacionados

- [Recursos movidos de Xamarin.Forms](https://github.com/xamarin/XamarinCommunityToolkit/wiki/Features-moved-from-Xamarin.Forms)
- [Migração do AndroidX no Xamarin.Forms](~/xamarin-forms/platform/android/androidx-migration.md)
