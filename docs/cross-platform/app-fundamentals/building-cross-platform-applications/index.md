---
title: Compilando aplicativos multiplataforma
description: Esta seção aborda, em um resumo, mais seis partes, como criar aplicativos usando a plataforma de desenvolvimento do Xamarin – desde entender como o Xamarin funciona para criar aplicativos móveis e, em seguida, testar e implantar em várias lojas de aplicativos.
ms.prod: xamarin
ms.assetid: 442FC40A-84DD-A218-0D15-EAD86594B6D7
author: conceptdev
ms.author: crdun
ms.date: 01/28/2016
ms.openlocfilehash: 2c630e58ff5a1221ec9e95099df4781041ecf2b4
ms.sourcegitcommit: dad4dfcd194b63ec9e903363351b6d9e543d4888
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2019
ms.locfileid: "70288050"
---
# <a name="building-cross-platform-applications"></a>Compilando aplicativos multiplataforma

Há duas opções para compartilhar código entre aplicativos móveis de plataforma cruzada: Projetos de ativos compartilhados e bibliotecas de classes portáteis. Essas opções são [discutidas aqui](~/cross-platform/app-fundamentals/code-sharing.md); mais informações sobre [bibliotecas de classes portáteis](~/cross-platform/app-fundamentals/pcl.md) e [projetos compartilhados](~/cross-platform/app-fundamentals/shared-projects.md) também estão disponíveis.

<a name="Sections" />

 [Visão geral](~/cross-platform/app-fundamentals/building-cross-platform-applications/overview.md)

 [Parte 1 – noções básicas sobre a plataforma do Xamarin Mobile](~/cross-platform/app-fundamentals/building-cross-platform-applications/understanding-the-xamarin-mobile-platform.md)

 [Parte 2 – arquitetura](~/cross-platform/app-fundamentals/building-cross-platform-applications/architecture.md)

 [Parte 3 – Configurando uma solução de plataforma cruzada do Xamarin](~/cross-platform/app-fundamentals/building-cross-platform-applications/setting-up-a-xamarin-cross-platform-solution.md)

 [Parte 4 – lidando com várias plataformas](~/cross-platform/app-fundamentals/building-cross-platform-applications/platform-divergence-abstraction-divergent-implementation.md)

 [Parte 5 – estratégias de compartilhamento de código práticas](~/cross-platform/app-fundamentals/building-cross-platform-applications/practical-code-sharing-strategies.md)

 [Parte 6 – Teste e aprovações da App Store](~/cross-platform/app-fundamentals/building-cross-platform-applications/testing-and-app-store-approvals.md)

 <a name="Cross-Platform_Mobile_Application_Case_Studies" />

## <a name="case-studies"></a>Estudos de caso

Os princípios descritos neste documento são colocados em prática na *tarefa*de aplicativo de exemplo, bem como [aplicativos pré-criados](https://xamarin.com/prebuilt) como o [Xamarin CRM](https://xamarin.com/prebuilt/#xamarincrm).

 <a name="Tasky" />

### <a name="tasky"></a>Tasky

A tarefa é um aplicativo simples de lista de tarefas pendentes para iOS, Android e Windows Phone.
Ele demonstra as noções básicas de criação de um aplicativo de plataforma cruzada com o Xamarin e usa um banco de dados SQLite local.

 [![lista de tasky](images/iphone-list-sml.png)](images/iphone-list.png#lightbox) [![de lista de tasky](images/iphone-list-sml.png)](images/iphone-list.png#lightbox)

Leia o [estudo de caso de tarefa](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md).

## <a name="summary"></a>Resumo

Esta seção apresenta as ferramentas de desenvolvimento de aplicativos do Xamarin e discute como criar aplicativos direcionados a várias plataformas móveis.

Ele abrange uma arquitetura em camadas que estrutura o código para reutilização em várias plataformas e descreve padrões de software diferentes que podem ser usados nessa arquitetura.

Os exemplos são fornecidos por funções de aplicativo comuns (como operações de arquivo e rede) e como eles podem ser criados em uma forma de plataforma cruzada.

Por fim, ele aborda brevemente os testes e fornece referências a um estudo de caso que coloca esses princípios em ação.

## <a name="related-links"></a>Links relacionados

- [Opções de código de compartilhamento](~/cross-platform/app-fundamentals/code-sharing.md)
- [Estudo de caso: @No__t_0 de tarefas
- [Aplicativo de exemplo de tarefa (github)](https://docs.microsoft.com/samples/xamarin/mobile-samples/taskyportable/)
- Desenvolvimento de aplicativos móveis [Xamarin: Plataforma C# cruzada e fundamentos do Xamarin. Forms (Amazon) ](http://www.amazon.com/Xamarin-Mobile-Application-Development-Cross-Platform/dp/1484202155/)
- [Desenvolvimento móvel com C# por Greg Shackles (o ' Reilly)](http://shop.oreilly.com/product/0636920024002.do)
- [Desenvolvimento móvel de plataforma cruzada profissional C# em por Scott Olson, John Hunter, Ben Horgen, Kenny goers (Wrox)](http://www.wrox.com/WileyCDA/WroxTitle/Professional-Cross-Platform-Mobile-Development-in-C-.productCd-1118157702.html)
