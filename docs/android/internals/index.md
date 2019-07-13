---
title: Conceitos avançados e recursos internos
description: Arquitetura subjacente por trás do design de API do xamarin. Android e de TI.
ms.prod: xamarin
ms.assetid: CC6A0D52-E9FA-4270-B3FA-84660621D6D5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/21/2018
ms.openlocfilehash: ba804e9757fb1354c39177e6f7cb9c97ea1b8188
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864121"
---
# <a name="advanced-concepts-and-internals"></a>Conceitos avançados e recursos internos

_Esta seção contém tópicos que explicam a arquitetura, design de API e as limitações do xamarin. Android. Além disso, ele inclui tópicos que explicam sua implementação da coleta de lixo e os assemblies que estão disponíveis no xamarin. Android. Porque é xamarin. Android [livre](https://github.com/xamarin/xamarin-android), também é possível entender o funcionamento interno do xamarin. Android examinando seu código-fonte._


## <a name="architectureandroidinternalsarchitecturemd"></a>[Arquitetura](~/android/internals/architecture.md)

Este artigo explica a arquitetura subjacente por trás de um aplicativo xamarin. Android. Ele explica como os aplicativos xamarin. Android são executados dentro de um ambiente de execução Mono junto com o com o tempo de execução do Android Máquina Virtual e explica conceitos chave como Callable Wrappers do Android e gerenciados Callable Wrappers. 



## <a name="api-designandroidinternalsapi-designmd"></a>[Design de API](~/android/internals/api-design.md)

Além das principais bibliotecas de classes Base que fazem parte do Mono, xamarin. Android é fornecido com associações para várias APIs do Android permitir aos desenvolvedores criar aplicativos nativos Android com o Mono.

A essência do xamarin. Android existe é um mecanismo de interoperabilidade nesse mundo pontes de c# com o mundo Java e fornece aos desenvolvedores acesso às APIs do Java do c# ou outras linguagens .NET.



## <a name="assembliescross-platforminternalsavailable-assembliesmd"></a>[Assemblies](~/cross-platform/internals/available-assemblies.md)

Xamarin. Android é fornecido com vários assemblies. Assim como o Silverlight é um subconjunto estendido dos assemblies do .NET da área de trabalho, xamarin. Android também é um subconjunto estendido de vários Silverlight e assemblies do .NET da área de trabalho. 

