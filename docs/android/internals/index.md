---
title: Conceitos avançados e recursos internos
description: Arquitetura subjacente por trás do design de API do xamarin. Android e de TI.
ms.prod: xamarin
ms.assetid: CC6A0D52-E9FA-4270-B3FA-84660621D6D5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/21/2018
ms.openlocfilehash: f5844dd4340afa0596219a33ed1e479a0dbcfa76
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114165"
---
# <a name="advanced-concepts-and-internals"></a>Conceitos avançados e recursos internos

_Esta seção contém tópicos que explicam a arquitetura, design de API e as limitações do xamarin. Android. Além disso, ele inclui tópicos que explicam sua implementação da coleta de lixo e os assemblies que estão disponíveis no xamarin. Android. Porque é xamarin. Android [livre](https://github.com/xamarin/xamarin-android), também é possível entender o funcionamento interno do xamarin. Android examinando seu código-fonte._


##  <a name="architectureandroidinternalsarchitecturemd"></a>[Arquitetura](~/android/internals/architecture.md)

Este artigo explica a arquitetura subjacente por trás de um aplicativo xamarin. Android. Ele explica como os aplicativos xamarin. Android são executados dentro de um ambiente de execução Mono junto com o com o tempo de execução do Android Máquina Virtual e explica conceitos chave como Callable Wrappers do Android e gerenciados Callable Wrappers. 



##  <a name="api-designandroidinternalsapi-designmd"></a>[Design de API](~/android/internals/api-design.md)

Além das principais bibliotecas de classes Base que fazem parte do Mono, xamarin. Android é fornecido com associações para várias APIs do Android permitir aos desenvolvedores criar aplicativos nativos Android com o Mono.

A essência do xamarin. Android existe é um mecanismo de interoperabilidade nesse mundo pontes de c# com o mundo Java e fornece aos desenvolvedores acesso às APIs do Java do c# ou outras linguagens .NET.



##  <a name="assembliescross-platforminternalsavailable-assembliesmd"></a>[Assemblies](~/cross-platform/internals/available-assemblies.md)

Xamarin. Android é fornecido com vários assemblies. Assim como o Silverlight é um subconjunto estendido dos assemblies do .NET da área de trabalho, xamarin. Android também é um subconjunto estendido de vários Silverlight e assemblies do .NET da área de trabalho. 

