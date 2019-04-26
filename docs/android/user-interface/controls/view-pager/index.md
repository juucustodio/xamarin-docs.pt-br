---
title: ViewPager
description: ViewPager é um Gerenciador de layout que permite que você implemente gestual navegação. Navegação gestual permite que o usuário passe o dedo para esquerda e direita para percorrer as páginas de dados. Este guia explica como implementar a navegação gestual com ViewPager, com e sem fragmentos. Ele também descreve como adicionar indicadores da página usando PagerTitleStrip e PagerTabStrip.
ms.prod: xamarin
ms.assetid: D42896C0-DE7C-4818-B171-CB2D5E5DD46A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: bb9795eb1e77a48b01556c553ae19613d6ab6de6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61267559"
---
# <a name="viewpager"></a>ViewPager

_ViewPager é um Gerenciador de layout que permite que você implemente gestual navegação. Navegação gestual permite que o usuário passe o dedo para esquerda e direita para percorrer as páginas de dados. Este guia explica como implementar a navegação gestual com ViewPager, com e sem fragmentos. Ele também descreve como adicionar indicadores da página usando PagerTitleStrip e PagerTabStrip._

 
## <a name="overview"></a>Visão geral

Um cenário comum no desenvolvimento de aplicativos é a necessidade de fornecer aos usuários gestual navegação entre exibições de irmão. Nessa abordagem, o usuário passa o dedo esquerda ou direita para acessar as páginas de conteúdo (por exemplo, em um Assistente de instalação ou uma apresentação de slide). Você pode criar esses modos de exibição de passar o dedo usando o `ViewPager` widget, disponível no [biblioteca de suporte ao Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). O `ViewPager` é um widget de layout composto de vários modos de exibição filho em que cada exibição filho constitui uma página no layout: 

[![Aplicativo de capturas de tela de TreePager com exemplo de passar o dedo horizontal](images/01-intro-sml.png)](images/01-intro.png#lightbox)

Normalmente, `ViewPager` é usado em conjunto com [fragmentos](https://developer.xamarin.com/guides/android/platform_features/fragments/); no entanto, há algumas situações em que você talvez queira usar `ViewPager` sem a complexidade adicional de `Fragment`s.

`ViewPager` usa um padrão de adaptador para fornecê-lo com os modos de exibição para exibir. O adaptador usado aqui é conceitualmente semelhante à utilizada pela [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) &ndash; fornece uma implementação da `PagerAdapter` para gerar as páginas que o `ViewPager` exibe ao usuário. As páginas exibidas pelo `ViewPager` pode ser `View`s ou `Fragment`s. Quando `View`s são exibidos, das adaptador subclasses Android `PagerAdapter` classe base. Se `Fragment`s são exibidos, das adaptador subclasses Android `FragmentPagerAdapter`. A biblioteca de suporte do Android também inclui `FragmentPagerAdapter` (uma subclasse de `PagerAdapter`) para ajudar com os detalhes de conexão `Fragment`s aos dados. 

Este guia demonstra as duas abordagens: 

-   Na [Viewpager com modos de exibição](~/android/user-interface/controls/view-pager/viewpager-and-views.md), um [TreePager](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager/) aplicativo é desenvolvido para demonstrar como usar `ViewPager` para mostrar as exibições de um catálogo de árvore (uma galeria de imagens de árvores decídua e sempre-verde). 
    `PagerTabStrip`  e `PagerTitleStrip` são usados para exibir títulos de navegação de página.

-   Na [Viewpager com fragmentos](~/android/user-interface/controls/view-pager/viewpager-and-fragments.md), um pouco mais complexa [FlashCardPager](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager/) aplicativo é desenvolvido para demonstrar como usar `ViewPager` com `Fragment`s para compilar um aplicativo que apresenta problemas de matemática como cartões flash e responde à entrada do usuário. 


## <a name="requirements"></a>Requisitos

Para usar `ViewPager` em seu projeto de aplicativo, você deve instalar o [biblioteca de suporte ao Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) pacote. Para obter mais informações sobre como instalar pacotes do NuGet, consulte [passo a passo: Incluindo um NuGet em seu projeto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough). 

 
## <a name="architecture"></a>Arquitetura

Três componentes são usados para implementar a navegação gestual com `ViewPager`:

-   ViewPager
-   Adaptador
-   Indicador de pager

Cada um desses componentes está resumida abaixo.



### <a name="viewpager"></a>ViewPager

`ViewPager` é um Gerenciador de layout que exibe uma coleção de `View`s por vez. Sua tarefa é detectar o gesto de passar o dedo do usuário e navegue até a exibição anterior ou seguinte, conforme apropriado. Por exemplo, a captura de tela abaixo demonstra um `ViewPager` fazendo a transição de uma imagem para o próximo em resposta a um gesto do usuário: 

[![Aplicativo de Close-up de TreePager exibindo uma transição entre os modos de exibição](images/02-transition-sml.png)](images/02-transition.png#lightbox)


### <a name="adapter"></a>Adaptador

`ViewPager` efetua pull de seus dados de um *adaptador*. Função do adaptador é criar o `View`s exibido pelo `ViewPager`, fornecendo a eles conforme necessário. O diagrama a seguir ilustra esse conceito &ndash; o adaptador cria e popula `View`s e fornece-os para o `ViewPager`. Como o `ViewPager` detecta os gestos de passar o dedo do usuário, ele solicita que o adaptador para fornecer apropriado `View` para exibir: 

[![Diagrama ilustrando como o adaptador se conecta imagens e nomes para o ViewPager](images/03-adapter-sml.png)](images/03-adapter.png#lightbox)

Nesse exemplo específico, cada `View` é construído a partir de uma imagem de árvore e um nome de árvore antes de ser passado para o `ViewPager`. 



### <a name="pager-indicator"></a>Indicador de pager

`ViewPager` pode ser usado para exibir um grande conjunto de dados (por exemplo, uma galeria de imagens pode conter centenas de imagens). Para ajudar o usuário navegue grandes conjuntos de dados `ViewPager` geralmente é acompanhado por um *indicador de pager* que exibe uma cadeia de caracteres. A cadeia de caracteres pode ser o título da imagem, uma legenda ou simplesmente a posição da exibição atual dentro do conjunto de dados. 

Há dois modos de exibição que podem produzir essas informações de navegação para você: `PagerTabStrip` e `PagerTitleStrip.` cada exibe uma cadeia de caracteres na parte superior de uma `ViewPager`, e cada um recebe seus dados do `ViewPager`do adaptador para que sempre que ele permaneça em sincronia com o atualmente exibido `View`. A diferença entre eles é que `PagerTabStrip` inclui um indicador visual para a cadeia de caracteres "atual" enquanto `PagerTitleStrip` não (como mostrado nessas capturas de tela): 

[![Capturas de tela do aplicativo TreePager com PagerTitleStrip e PagerTabStrip](images/04-comparison-sml.png)](images/04-comparison.png#lightbox)

Este guia demonstra como immplement `ViewPager`, adaptador e componentes de aplicativo do indicador e integrá-los para dar suporte à navegação gestual. 



## <a name="related-links"></a>Links relacionados

- [TreePager (amostra)](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager)
- [FlashCardPager (amostra)](https://developer.xamarin.com/samples/monodroid/UserInterface/FlashCardPager)
