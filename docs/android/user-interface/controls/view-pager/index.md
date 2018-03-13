---
title: ViewPager
description: "ViewPager é um Gerenciador de layout que permite que você implemente gestual navegação. Navegação gestual permite que o usuário passe o dedo para esquerda e direita para percorrer páginas de dados. Este guia explica como implementar navegação gestual com ViewPager, com e sem fragmentos. Ele também descreve como adicionar indicadores de página usando PagerTitleStrip e PagerTabStrip."
ms.topic: article
ms.prod: xamarin
ms.assetid: D42896C0-DE7C-4818-B171-CB2D5E5DD46A
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 5e2f93eea970a15df03b00cc962ca7482624973d
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="viewpager"></a>ViewPager

_ViewPager é um Gerenciador de layout que permite que você implemente gestual navegação. Navegação gestual permite que o usuário passe o dedo para esquerda e direita para percorrer páginas de dados. Este guia explica como implementar navegação gestual com ViewPager, com e sem fragmentos. Ele também descreve como adicionar indicadores de página usando PagerTitleStrip e PagerTabStrip._

 
## <a name="overview"></a>Visão geral

Um cenário comum no desenvolvimento de aplicativos é a necessidade de fornecer aos usuários gestual navegação entre modos de exibição de irmão. Nessa abordagem, o usuário obtém esquerda ou direita para acessar as páginas de conteúdo (por exemplo, em uma apresentação de slides ou um Assistente de instalação). Você pode criar essas exibições passe o dedo usando o `ViewPager` widget, disponível em [biblioteca de suporte Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). O `ViewPager` é um widget de layout, composto de vários modos de exibição filho onde cada exibição filho constitui uma página no layout: 

[![Aplicativo de capturas de tela de TreePager com exemplo de passe o dedo horizontal](images/01-intro-sml.png)](images/01-intro.png#lightbox)

Normalmente, `ViewPager` é usada em conjunto com [fragmentos](https://developer.xamarin.com/guides/android/platform_features/fragments/); no entanto, há algumas situações em que você talvez queira usar `ViewPager` sem a complexidade adicional de `Fragment`s.

`ViewPager` usa um padrão de adaptador para fornecê-la com os modos de exibição para exibir. O adaptador usado aqui é conceitualmente semelhante à utilizada pela [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) &ndash; você fornecer uma implementação de `PagerAdapter` para gerar as páginas que o `ViewPager` exibe para o usuário. As páginas exibidas por `ViewPager` pode ser `View`s ou `Fragment`s. Quando `View`s são exibidos, das adaptador subclasses Android `PagerAdapter` classe base. Se `Fragment`s são exibidos, das adaptador subclasses Android `FragmentPagerAdapter`. Também inclui a biblioteca de suporte do Android `FragmentPagerAdapter` (uma subclasse de `PagerAdapter`) para ajudá-lo com os detalhes de conexão `Fragment`s aos dados. 

Este guia demonstra as duas abordagens: 

-   Em [Viewpager com exibições](~/android/user-interface/controls/view-pager/viewpager-and-views.md), um [TreePager](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager/) aplicativo é desenvolvido para demonstrar como usar `ViewPager` para exibir os modos de exibição de um catálogo de árvore (uma galeria de imagens de decídua e verde). 
    `PagerTabStrip`  e `PagerTitleStrip` são usados para exibir títulos de navegação de página.

-   Em [Viewpager com fragmentos](~/android/user-interface/controls/view-pager/viewpager-and-fragments.md), um pouco mais complexo [FlashCardPager](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager/) aplicativo é desenvolvido para demonstrar como usar `ViewPager` com `Fragment`s para compilar um aplicativo que apresenta problemas de matemática como fichas e responde à entrada do usuário. 


## <a name="requirements"></a>Requisitos

Para usar `ViewPager` no seu projeto de aplicativo, você deve instalar o [biblioteca de suporte Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) pacote. Para obter mais informações sobre como instalar pacotes do NuGet, consulte [passo a passo: incluindo um NuGet em seu projeto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough). 

 
## <a name="architecture"></a>Arquitetura

Três componentes são usados para implementar navegação gestual com `ViewPager`:

-   ViewPager
-   Adaptador
-   Indicador de pager

Cada um desses componentes é resumida abaixo.



### <a name="viewpager"></a>ViewPager

`ViewPager` é um Gerenciador de layout que exibe uma coleção de `View`s por vez. Seu trabalho é detectar gesto de passar do usuário e navegue até a exibição anterior ou seguinte, conforme apropriado. Por exemplo, a captura de tela abaixo demonstra um `ViewPager` fazer a transição de uma imagem para o próximo em resposta a um gesto do usuário: 

[![Close-up de TreePager aplicativo exibindo uma transição entre modos de exibição](images/02-transition-sml.png)](images/02-transition.png#lightbox)


### <a name="adapter"></a>Adaptador

`ViewPager` extrai os dados de um *adaptador*. Trabalho do adaptador é criar o `View`s exibido pelo `ViewPager`, fornecendo a eles conforme necessário. O diagrama a seguir ilustra esse conceito &ndash; o adaptador cria e preenche `View`s e fornece-los para o `ViewPager`. Como o `ViewPager` detecta gestos de toque do usuário, ele solicita que o adaptador para fornecer apropriada `View` para exibir: 

[![Diagrama que ilustra como o adaptador se conecta imagens e nomes para o ViewPager](images/03-adapter-sml.png)](images/03-adapter.png#lightbox)

Nesse exemplo específico, cada `View` é construído a partir de uma imagem de árvore e um nome de árvore antes de passar para o `ViewPager`. 



### <a name="pager-indicator"></a>Indicador de pager

`ViewPager` pode ser usado para exibir um grande conjunto de dados (por exemplo, uma galeria de imagens pode conter centenas de imagens). Para ajudar o usuário a navegação por grandes conjuntos de dados, `ViewPager` geralmente é acompanhado por um *indicador de pager* que exibe uma cadeia de caracteres. A cadeia de caracteres pode ser simplesmente a posição do modo de exibição atual no conjunto de dados, uma legenda ou o título da imagem. 

Há dois modos de exibição que podem produzir essas informações de navegação para você: `PagerTabStrip` e `PagerTitleStrip.` cada exibe uma cadeia de caracteres na parte superior de um `ViewPager`, e cada um obtém seus dados a partir o `ViewPager`do adaptador para que ele sempre permanece em sincronia com o exibição `View`. A diferença entre eles é que `PagerTabStrip` inclui um indicador visual para a cadeia de caracteres "atual" ao `PagerTitleStrip` não (como mostrado nessas capturas de tela): 

[![Capturas de tela do aplicativo TreePager com PagerTitleStrip e PagerTabStrip](images/04-comparison-sml.png)](images/04-comparison.png#lightbox)

Este guia demonstra como immplement `ViewPager`, adaptador e componentes de aplicativo do indicador e integrá-los para oferecer suporte à navegação gestual. 



## <a name="related-links"></a>Links relacionados

- [TreePager (exemplo)](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager)
- [FlashCardPager (exemplo)](https://developer.xamarin.com/samples/monodroid/UserInterface/FlashCardPager)
