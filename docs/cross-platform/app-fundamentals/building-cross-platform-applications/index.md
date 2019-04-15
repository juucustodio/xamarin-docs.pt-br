---
title: Compilando aplicativos multiplataforma
description: Em um resumo mais de seis partes, esta seção discute como criar aplicativos usando a plataforma de desenvolvimento do Xamarin – de Noções básicas sobre o funcionamento do Xamarin para projetar aplicativos móveis e, em seguida, teste e implantação em várias lojas de aplicativos.
ms.prod: xamarin
ms.assetid: 442FC40A-84DD-A218-0D15-EAD86594B6D7
author: asb3993
ms.author: amburns
ms.date: 01/28/2016
ms.openlocfilehash: 683400e24844308769f0562552641216d45e7d11
ms.sourcegitcommit: 91a4fcb715506e18e8070bc89bf2cb14d079ad32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/15/2019
ms.locfileid: "59574787"
---
# <a name="building-cross-platform-applications"></a>Compilando aplicativos multiplataforma

Há duas opções para compartilhar código entre aplicativos móveis de plataforma cruzada: Projetos de ativos e bibliotecas de classes portáteis compartilhadas. Essas opções estão [discutido aqui](~/cross-platform/app-fundamentals/code-sharing.md); para obter mais informações sobre [bibliotecas de classes portáteis](~/cross-platform/app-fundamentals/pcl.md) e [projetos compartilhados](~/cross-platform/app-fundamentals/shared-projects.md) também está disponível.

<a name="Sections" />

 [Visão geral](~/cross-platform/app-fundamentals/building-cross-platform-applications/overview.md)

 [Parte 1 – Noções básicas sobre a plataforma Xamarin Mobile](~/cross-platform/app-fundamentals/building-cross-platform-applications/understanding-the-xamarin-mobile-platform.md)

 [Parte 2 – arquitetura](~/cross-platform/app-fundamentals/building-cross-platform-applications/architecture.md)

 [Parte 3 – configuração de uma solução de plataforma cruzada do Xamarin](~/cross-platform/app-fundamentals/building-cross-platform-applications/setting-up-a-xamarin-cross-platform-solution.md)

 [Parte 4 – lidar com várias plataformas](~/cross-platform/app-fundamentals/building-cross-platform-applications/platform-divergence-abstraction-divergent-implementation.md)

 [Parte 5 – estratégias de compartilhamento de código prático](~/cross-platform/app-fundamentals/building-cross-platform-applications/practical-code-sharing-strategies.md)

 [Parte 6 – Teste e aprovações da App Store](~/cross-platform/app-fundamentals/building-cross-platform-applications/testing-and-app-store-approvals.md)

 <a name="Cross-Platform_Mobile_Application_Case_Studies" />

## <a name="case-studies"></a>Estudos de caso

Os princípios descritos neste documento são colocados em prática no aplicativo de exemplo *Tasky*, bem como [pré-criadas aplicativos](https://xamarin.com/prebuilt) como [CRM Xamarin](https://xamarin.com/prebuilt/#xamarincrm).

 <a name="Tasky" />

### <a name="tasky"></a>Tasky

Tasky é um aplicativo de lista de tarefas pendentes simples para iOS, Android e Windows Phone.
Ele demonstra as Noções básicas da criação de um aplicativo de plataforma cruzada com Xamarin e usa um banco de dados SQLite local.

 [![tasky list](images/iphone-list-sml.png)](images/iphone-list.png#lightbox) [![tasky list](images/iphone-list-sml.png)](images/iphone-list.png#lightbox)

Leia as [estudo de caso Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md).

## <a name="summary"></a>Resumo

Esta seção apresenta as ferramentas de desenvolvimento de aplicativo do Xamarin e discute como compilar aplicativos direcionados a várias plataformas móveis.

Ele aborda uma arquitetura em camadas desse código de estruturas para reutilização em várias plataformas e descreve padrões de software diferentes que podem ser usadas dentro dessa arquitetura.

São fornecidos exemplos de funções de aplicativo comuns (como operações de arquivo e de rede) e como eles podem ser criados de uma maneira de plataforma cruzada.

Por fim, ele brevemente discute o teste e fornece referências a um estudo de caso que coloca esses princípios em ação.

## <a name="related-links"></a>Links relacionados

- [Opções de compartilhamento de código](~/cross-platform/app-fundamentals/code-sharing.md)
- [Estudo de caso: Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)
- [Aplicativo de exemplo tasky (github)](https://developer.xamarin.com/samples/mobile/TaskyPortable/)
- [Desenvolvimento de aplicativo móvel do Xamarin: Plataforma cruzada C# e os conceitos básicos do xamarin. Forms (Amazon)](http://www.amazon.com/Xamarin-Mobile-Application-Development-Cross-Platform/dp/1484202155/)
- [Desenvolvimento móvel com o C# por Greg Shackles (o ' Reilly)](http://shop.oreilly.com/product/0636920024002.do)
- [Desenvolvimento móvel multiplataforma profissional no C# por Scott Olson, John Hunter, Ben Horgen, Kenny Goers (Wrox)](http://www.wrox.com/WileyCDA/WroxTitle/Professional-Cross-Platform-Mobile-Development-in-C-.productCd-1118157702.html)
