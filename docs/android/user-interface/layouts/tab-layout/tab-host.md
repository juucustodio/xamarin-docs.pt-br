---
title: Guia Layout com TabHost
description: "Este artigo fornecerá uma visão geral de alto nível da a TabHost, uma API mais antiga usada para criar layouts com guias em um aplicativo xamarin."
ms.topic: article
ms.prod: xamarin
ms.assetid: 1CFF590A-AC86-C3B3-36CA-A70248BC7F97
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 10/25/2017
ms.openlocfilehash: ff61ca0a2bca466da3e33c93a17944915328b70c
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="tab-layout-with-tabhost"></a>Guia Layout com TabHost

_Este artigo fornecerá uma visão geral de alto nível da a TabHost, uma API mais antiga usada para criar layouts com guias em um aplicativo xamarin._

<a name="Overview" />

## <a name="overview"></a>Visão geral

> [!NOTE]
> **Observação:** `TabHost` é uma API antiga que foi substituída pelo Google. Os desenvolvedores são encorajados a criar aplicativos com guias usando o [barra de ação](~/android/user-interface/controls/action-bar.md). O `ActionBar` está disponível em todos os versão do Android. Ele foi introduzido no Android 3.0 (API nível 11) e volta foi movido para o Android 2.2 (API nível 8) e no Android 2.3 (API nível 10) no [V7 AppCompat biblioteca](http://developer.android.com/tools/support-library/features.html#v7-appcompat), que está disponível para xamarin por meio de [Xamarin Biblioteca de suporte do Android - V7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) pacote.

O `TabHost` é a mais antiga API original para criação de usuário com guias interfacesIt é mais adequada para aplicativos xamarin que deve oferecer suporte a Android 2.2 e 2.3 Android e não é possível usar **ActionBarSherlock**.
Os seguintes componentes de cinco são todos envolvidos com o `TabHost` API:

-  **TabActivity** &ndash; de exibição especializados que atua como um contêiner para um `TabHost` (descritas abaixo).

-  **TabHost** &ndash; este é um contêiner para a interface do usuário com guias. Hospeda dois filhos: uma lista de rótulos de guia e um `FrameLayout` que exibe o conteúdo da guia.

-  **TabWidget** &ndash; esse controle exibe uma lista de rótulos de guia, uma para cada guia o `TabHost` . Cada guia em um `TabHost` são representados por um `TabHost.TabSpec` objeto. Este objeto contém os metadados para cada guia. Quando o usuário seleciona uma guia, o `TabHost` responde à seleção exibindo a guia apropriada.

-  **FrameLayout** &ndash; uma interface do usuário com guias deve ter uma `FrameLayout` contido dentro de um `TabHost`. Ele é usado para exibir o conteúdo da guia.

-  **Atividades ou exibições** &ndash; quando uma guia é selecionada, ele exibe uma atividade ou um modo de exibição no `FrameLayout`.

O diagrama a seguir mostra como todos esses componentes se relacionam juntos:

![Diagrama que ilustra o Layout de quadro em TabWidget em TabHost](tab-host-images/image03.png)

O conteúdo do guia pode ser atividades ou modos de exibição. Modos de exibição são relativamente leve e simples, mas podem resultar em uma grande quantidade de código relacionado co-habitating na atividade. Isso resultará em uma baixo separação de preocupações e uma classe inchada difícil de manter. Por outro lado, as atividades exigem recursos do sistema, mas permitem uma abordagem mais modular com a lógica para cada guia encapsulada em sua própria classe distintos.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo explicou os componentes de alto níveis do antigos `TabHost` API para Android e como elas se relacionam juntos.



## <a name="related-links"></a>Links relacionados

- [ActionBar](http://developer.android.com/guide/topics/ui/actionbar.html)
- [TabHost](https://developer.xamarin.com/api/type/Android.Widget.TabHost/)
- [TabHost.TabSpec](https://developer.xamarin.com/api/type/Android.Widget.TabHost+TabSpec/)
- [TabWidget](https://developer.xamarin.com/api/type/Android.Widget.TabWidget/)
- [TabActivity](https://developer.xamarin.com/api/type/Android.App.TabActivity/)
- [ActionBar](http://developer.android.com/guide/topics/ui/actionbar.html)
- [Pacote do NuGet do suporte do Android biblioteca v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
- [biblioteca de appcompat v7](http://developer.android.com/tools/support-library/features.html#v7-appcompat)
