---
title: ViewPager
description: ViewPager é um Gerenciador de layout que permite implementar a navegação Gestural. A navegação Gestural permite que o usuário passe o dedo para a esquerda e para a direita para percorrer as páginas de dados. Este guia explica como implementar a navegação Gestural com o ViewPager, com e sem fragmentos. Ele também descreve como adicionar indicadores de página usando PagerTitleStrip e PagerTabStrip.
ms.prod: xamarin
ms.assetid: D42896C0-DE7C-4818-B171-CB2D5E5DD46A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 427ace2043f966b617a258b5f50fa42f943e707e
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457647"
---
# <a name="viewpager"></a>ViewPager

_ViewPager é um Gerenciador de layout que permite implementar a navegação Gestural. A navegação Gestural permite que o usuário passe o dedo para a esquerda e para a direita para percorrer as páginas de dados. Este guia explica como implementar a navegação Gestural com o ViewPager, com e sem fragmentos. Ele também descreve como adicionar indicadores de página usando PagerTitleStrip e PagerTabStrip._

## <a name="overview"></a>Visão geral

Um cenário comum no desenvolvimento de aplicativos é a necessidade de fornecer aos usuários navegação Gestural entre exibições irmãos. Nessa abordagem, o usuário passa o dedo para a esquerda ou direita para acessar páginas de conteúdo (por exemplo, em um assistente de instalação ou em uma apresentação de slides). Você pode criar essas exibições de passe do dedo usando o `ViewPager` widget, disponível na [biblioteca de suporte do Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). O `ViewPager` é um widget de layout composto por várias exibições filhas em que cada exibição filho constitui uma página no layout: 

[![Capturas de tela do aplicativo TreePager com o exemplo de deslize horizontal](images/01-intro-sml.png)](images/01-intro.png#lightbox)

Normalmente, `ViewPager` é usado em conjunto com [fragmentos](~/android/platform/fragments/index.md); no entanto, há algumas situações em que você pode querer usar `ViewPager` sem a complexidade adicional de `Fragment` s.

`ViewPager` usa um padrão de adaptador para fornecê-lo com as exibições a serem exibidas. O adaptador usado aqui é conceitualmente semelhante ao usado pelo [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) &ndash; você fornece uma implementação do `PagerAdapter` para gerar as páginas que o `ViewPager` exibe para o usuário. As páginas exibidas por `ViewPager` podem ser `View` s ou `Fragment` s. Quando `View` s são exibidos, o adaptador subclasses da `PagerAdapter` classe base do Android. Se `Fragment` s forem exibidos, o adaptador subclasses do Android `FragmentPagerAdapter` . A biblioteca de suporte do Android também inclui `FragmentPagerAdapter` (uma subclasse de `PagerAdapter` ) para ajudar com os detalhes da conexão dos s com os `Fragment` dados. 

Este guia demonstra as duas abordagens: 

- No [Viewpager com exibições](~/android/user-interface/controls/view-pager/viewpager-and-views.md), um aplicativo [TreePager](/samples/xamarin/monodroid-samples/userinterface-treepager) é desenvolvido para demonstrar como usar o `ViewPager` para exibir exibições de um catálogo de árvore (uma galeria de imagens das árvores decídua e verde). 
    `PagerTabStrip`  e `PagerTitleStrip` são usados para exibir títulos que ajudam na navegação da página.

- Em [Viewpager com fragmentos](~/android/user-interface/controls/view-pager/viewpager-and-fragments.md), um aplicativo [FlashCardPager](/samples/xamarin/monodroid-samples/userinterface-flashcardpager) um pouco mais complexo é desenvolvido para demonstrar como usar `ViewPager` com `Fragment` s para criar um aplicativo que apresenta problemas de matemática como cartões flash e responde à entrada do usuário. 

## <a name="requirements"></a>Requisitos

Para usar `ViewPager` em seu projeto de aplicativo, você deve instalar o pacote [v4 de suporte do Android da biblioteca](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) . Para obter mais informações sobre a instalação de pacotes do NuGet, consulte [Walkthrough: incluindo um NuGet em seu projeto](/visualstudio/mac/nuget-walkthrough). 

## <a name="architecture"></a>Arquitetura

Três componentes são usados para implementar a navegação Gestural com `ViewPager` :

- ViewPager
- Adaptador
- Indicador de pager

Cada um desses componentes é resumido abaixo.

### <a name="viewpager"></a>ViewPager

`ViewPager` é um Gerenciador de layout que exibe uma coleção de `View` s uma de cada vez. Seu trabalho é detectar o gesto de passar o dedo do usuário e navegar até a exibição seguinte ou anterior, conforme apropriado. Por exemplo, a captura de tela abaixo demonstra um `ViewPager` fazendo a transição de uma imagem para a próxima em resposta a um gesto de usuário: 

[![Closeup do aplicativo TreePager exibindo uma transição entre exibições](images/02-transition-sml.png)](images/02-transition.png#lightbox)

### <a name="adapter"></a>Adaptador

`ViewPager` efetua pull de seus dados de um *adaptador*. O trabalho do adaptador é criar os `View` s exibidos pelo `ViewPager` , fornecendo-os conforme necessário. O diagrama a seguir ilustra esse conceito que &ndash; o adaptador cria e popula `View` s e os fornece ao `ViewPager` . Como o `ViewPager` detecta os gestos de toque do usuário, ele solicita que o adaptador forneça a `View` exibição apropriada: 

[![Diagrama ilustrando como o adaptador conecta imagens e nomes ao ViewPager](images/03-adapter-sml.png)](images/03-adapter.png#lightbox)

Neste exemplo específico, cada `View` um é construído a partir de uma imagem de árvore e um nome de árvore antes de ser passado para o `ViewPager` . 

### <a name="pager-indicator"></a>Indicador de pager

`ViewPager` pode ser usado para exibir um grande conjunto de dados (por exemplo, uma galeria de imagens pode conter centenas de imagens). Para ajudar o usuário a navegar por conjuntos de dados grandes, `ViewPager` geralmente é acompanhado por um *indicador de pager* que exibe uma cadeia de caracteres. A cadeia de caracteres pode ser o título da imagem, uma legenda ou simplesmente a posição da exibição atual dentro do conjunto de dados. 

Há duas exibições que podem produzir essas informações de navegação para você: `PagerTabStrip` e `PagerTitleStrip.` cada uma exibe uma cadeia de caracteres na parte superior de um `ViewPager` , e cada uma recebe seus dados do `ViewPager` adaptador de forma que sempre permaneça em sincronia com o exibido no momento `View` . A diferença entre eles é que o `PagerTabStrip` inclui um indicador visual para a cadeia de caracteres "atual" enquanto não `PagerTitleStrip` (conforme mostrado nestas capturas de tela): 

[![Capturas de tela do aplicativo TreePager com PagerTitleStrip e PagerTabStrip](images/04-comparison-sml.png)](images/04-comparison.png#lightbox)

Este guia demonstra como immplement `ViewPager` , adaptador e componentes de aplicativo de indicador e integrá-los para dar suporte à navegação do Gestural. 

## <a name="related-links"></a>Links Relacionados

- [TreePager (exemplo)](/samples/xamarin/monodroid-samples/userinterface-treepager)
- [FlashCardPager (exemplo)](/samples/xamarin/monodroid-samples/userinterface-flashcardpager)