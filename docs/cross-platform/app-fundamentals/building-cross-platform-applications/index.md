---
title: Criando aplicativos de plataforma cruzada
description: Em um resumo mais seis partes, esta seção discute como criar aplicativos usando a plataforma de desenvolvimento do Xamarin – de Noções básicas sobre o funcionamento do Xamarin para criar aplicativos móveis e, em seguida, testar e implantar para as várias lojas de aplicativos.
ms.prod: xamarin
ms.assetid: 442FC40A-84DD-A218-0D15-EAD86594B6D7
author: asb3993
ms.author: amburns
ms.date: 01/28/2016
ms.openlocfilehash: 3966b731531d617f105583210334a23071a6802b
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34780168"
---
# <a name="building-cross-platform-applications"></a>Criando aplicativos de plataforma cruzada

Há duas opções para compartilhar código entre aplicativos móveis de plataforma cruzada: projetos de ativo compartilhado e bibliotecas de classes portáteis. Essas opções são [discutidos aqui](~/cross-platform/app-fundamentals/code-sharing.md); obter mais informações sobre [bibliotecas de classes portáteis](~/cross-platform/app-fundamentals/pcl.md) e [projetos compartilhados](~/cross-platform/app-fundamentals/shared-projects.md) também está disponível.

<a name="Sections" />

 [Visão geral](~/cross-platform/app-fundamentals/building-cross-platform-applications/overview.md)

 [Parte 1 – Noções básicas sobre a plataforma Xamarin](~/cross-platform/app-fundamentals/building-cross-platform-applications/understanding-the-xamarin-mobile-platform.md)

 [Parte 2 – arquitetura](~/cross-platform/app-fundamentals/building-cross-platform-applications/architecture.md)

 [Parte 3 – configurar uma solução de plataforma cruzada do Xamarin](~/cross-platform/app-fundamentals/building-cross-platform-applications/setting-up-a-xamarin-cross-platform-solution.md)

 [Parte 4 – lidar com várias plataformas](~/cross-platform/app-fundamentals/building-cross-platform-applications/platform-divergence-abstraction-divergent-implementation.md)

 [Parte 5 – código prático estratégias de compartilhamento](~/cross-platform/app-fundamentals/building-cross-platform-applications/practical-code-sharing-strategies.md)

 [Parte 6 – Teste e aprovações da App Store](~/cross-platform/app-fundamentals/building-cross-platform-applications/testing-and-app-store-approvals.md)

 <a name="Cross-Platform_Mobile_Application_Case_Studies" />

## <a name="case-studies"></a>Estudos de caso

Os princípios descritos neste documento são colocados em prática no aplicativo de exemplo *Tasky*, bem como [pré-criadas aplicativos](https://xamarin.com/prebuilt) como [Xamarin CRM](https://xamarin.com/prebuilt/#xamarincrm).

 <a name="Tasky" />

### <a name="tasky"></a>Tasky

Tasky é um aplicativo de lista de tarefas simples para iOS, Android e Windows Phone.
Ele demonstra os conceitos básicos da criação de um aplicativo de plataforma cruzada com Xamarin e usa um banco de dados local do SQLite.

 [![lista tasky](images/iphone-list-sml.png)](images/iphone-list.png#lightbox) [ ![tasky lista](images/iphone-list-sml.png)](images/iphone-list.png#lightbox)

Leitura de [Tasky estudo de caso](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md).

## <a name="summary"></a>Resumo

Esta seção apresenta as ferramentas de desenvolvimento de aplicativo do Xamarin e discute como criar aplicativos que usam múltiplas plataformas móveis.

Ele abrange uma arquitetura em camadas que o código estruturas para reutilização em várias plataformas e descreve os padrões de software diferentes que podem ser usados na arquitetura.

São fornecidos exemplos de funções de aplicativo comuns (como operações de arquivo e de rede) e como eles podem ser criados de uma maneira de plataforma cruzada.

Finalmente, ele brevemente discute testes e fornece referências a um estudo de caso que coloca esses princípios em ação.

## <a name="related-links"></a>Links relacionados

- [Opções de compartilhamento de código](~/cross-platform/app-fundamentals/code-sharing.md)
- [Estudo de caso: Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)
- [Aplicativo de exemplo tasky (github)](https://developer.xamarin.com/samples/mobile/TaskyPortable/)
- [Desenvolvimento de aplicativos móveis de Xamarin: Plataforma cruzada c# e conceitos básicos do xamarin. Forms](http://www.amazon.com/Xamarin-Mobile-Application-Development-Cross-Platform/dp/1484202155/))
- [Desenvolvimento móvel com o c# por Greg Shackles (o ' Reilly)](http://shop.oreilly.com/product/0636920024002.do)
- [Professional desenvolvimento móvel em plataforma cruzada em c#, Scott Olson, João Hunter, Ben Horgen, Kenny Goers (Wrox)](http://www.wiley.com/WileyCDA/WileyTitle/productCd-1118157702.html)
