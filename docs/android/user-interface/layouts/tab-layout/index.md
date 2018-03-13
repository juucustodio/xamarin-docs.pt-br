---
title: Layouts com guias
description: "Uma visão geral dos Layouts com guias no Android"
ms.topic: article
ms.prod: xamarin
ms.assetid: 1CFF590A-AC86-C3B3-36CA-A70248BC7F97
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 08/23/2017
ms.openlocfilehash: 02a425c8276524accc088b53c1099e7c2e28d828
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="tabbed-layouts"></a>Layouts com guias


## <a name="overview"></a>Visão geral

Guias são um padrão de interface de usuário populares em aplicativos móveis devido à sua simplicidade e facilidade de uso. Eles fornecem uma maneira fácil e consistente para navegar entre várias telas em um aplicativo. Android tem várias APIs para interfaces com guias: 

-   **TabHost** &ndash; essa é a API original para a criação de interfaces de usuário com guias. Um `TabHost` é adicionado a um layout de widget e atua como um contêiner para exibições com guias. Esta API foi preterida desde e seu uso não é recomendado. 

-   **Barra de ação** &ndash; isso faz parte de um novo conjunto de API que foi introduzido no Android 3.0 (API nível 11) com o objetivo de fornecer uma consistente interface de alternância de exibição e navegação. Ela volta portada para Android 2.2 (API nível 8) com o [biblioteca de suporte Android v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/). 

-   **PagerTabStrip** &ndash; indica as páginas de atual, próxima e anterior de um `ViewPager`. `ViewPager` está disponível apenas via [biblioteca de suporte Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/).
     Para obter mais informações sobre `PagerTabStrip`, consulte [ViewPager](~/android/user-interface/controls/view-pager/index.md).

-   **Barra de ferramentas** &ndash; `Toolbar` é um componente de barra de ação mais flexíveis e mais recentes que substitui `ActionBar`. `Toolbar` está disponível no Android pirulito de 5.0 ou posterior, e também está disponível para versões mais antigas do Android por meio de [biblioteca de suporte Android v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) pacote NuGet. 
    `Toolbar` no momento é o componente de barra de ação recomendada para usar em aplicativos Android.
    Para obter mais informações, consulte [barra de ferramentas](~/android/user-interface/controls/tool-bar/index.md). 


Essas APIs são visualmente muito diferentes e não são compatíveis entre si. A seguir mostra de imagem de tela `TabHost` e `ActionBar` lado a lado: 

![Capturas de tela de TabHost à esquerda e a barra de ação à direita](images/image01.png)

Essas APIs incompatíveis existem devido a alterações significativas na interface do usuário desde 3.0 Android (API nível 11). Uma dessas alterações de interface do usuário foi o [ação padrão de design da barra](http://www.androidpatterns.com/uap_pattern/action-bar), um padrão se destina a fornecer acesso fácil a funcionalidade de navegação e a chave em um aplicativo. O `ActionBar` API foi introduzida para dar suporte a esse padrão. 

O `ActionBar` API é mais simples e possivelmente fornece uma melhor experiência do usuário. Ele portado novamente para Android 2.2 e é a opção preferida para aplicativos xamarin. 

O `TabHost` API é compatível com todas as versões do Android, mas requer mais esforço para usar e não é consistente com o atual [Android diretrizes de interface do usuário](http://developer.android.com/design/index.html). Os desenvolvedores são recomendados de usar essa API e devem favorecer a barra de ação mais recente para seus aplicativos xamarin. 



## <a name="actionbarsherlock"></a>ActionBarSherlock

Antes de backported para Android 2.2, os desenvolvedores que desejavam a aparência mais recente da API de barra de ação, mas podem usar uma biblioteca de terceiros, a API de barra de ação [ActionBarSherlock](http://actionbarsherlock.com). ActionBarSherlock é que uma extensão da biblioteca de suporte do Android projetado para backport o padrão de design de barra de ação para Android 2. x. Quando em execução no Android 3.0 ou superior, ActionBarSherlock automaticamente usará nativo `ActionBar` API fornecida pelo Android. Versões mais antigas do Android usará uma implementação personalizada que imitarão a aparência do mais recente `ActionBar` API. O [ActionBarSherlock componente](https://www.nuget.org/packages/xamstore-XamarinActionBarSherlock/) torna mais fácil adicionar ActionBarSherlock a um aplicativo xamarin. 



## <a name="related-links"></a>Links relacionados

- [Visão geral de TabHost](tab-host.md)
- [TabHost passo a passo](~/android/user-interface/layouts/tab-layout/creating-a-tabbed-ui.md)
- [ActionBar](http://developer.android.com/guide/topics/ui/actionbar.html)
- [Pacote do NuGet do suporte do Android biblioteca v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
- [biblioteca de appcompat v7](http://developer.android.com/tools/support-library/features.html#v7-appcompat)
