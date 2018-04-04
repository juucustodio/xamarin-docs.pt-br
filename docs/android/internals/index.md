---
title: Conceitos avançados e internos
description: Arquitetura subjacente atrás de xamarin e do design de API.
ms.prod: xamarin
ms.assetid: CC6A0D52-E9FA-4270-B3FA-84660621D6D5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 10/13/2017
ms.openlocfilehash: 517e21f2decd0dabbd03d752f13831a891ad7138
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="advanced-concepts-and-internals"></a>Conceitos avançados e internos


##  <a name="architectureandroidinternalsarchitecturemd"></a>[Arquitetura](~/android/internals/architecture.md)

Este artigo explica a arquitetura subjacente por trás de um aplicativo xamarin. Ele explica como xamarin aplicativos executados dentro de um ambiente de execução Mono junto com o tempo de execução Android Máquina Virtual e explica conceitos chave Android Callable Wrappers e gerenciados Callable Wrappers. 



##  <a name="api-designandroidinternalsapi-designmd"></a>[Design de API](~/android/internals/api-design.md)

Além das bibliotecas de classes Base que fazem parte do Mono principais, xamarin fornecido com associações para várias APIs Android permitir que os desenvolvedores criem aplicativos Android nativos com Mono.

No núcleo do xamarin existe é um mecanismo de interoperabilidade que world pontes c# com o mundo de Java e fornece aos desenvolvedores com acesso às APIs do Java de c# ou outras linguagens .NET.



##  <a name="assembliescross-platforminternalsavailable-assembliesmd"></a>[Assemblies](~/cross-platform/internals/available-assemblies.md)

Xamarin vem com vários assemblies. Assim como o Silverlight é um subconjunto estendido dos assemblies do .NET da área de trabalho, xamarin também é um subconjunto estendido de vários Silverlight e área de trabalho assemblies do .NET. 

