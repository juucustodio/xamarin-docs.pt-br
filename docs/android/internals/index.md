---
title: Conceitos avançados e recursos internos
description: Arquitetura subjacente por trás do Xamarin. Android e seu design de API.
ms.prod: xamarin
ms.assetid: CC6A0D52-E9FA-4270-B3FA-84660621D6D5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/21/2018
ms.openlocfilehash: 4f860d493c5709e2f6c7f89e6f3a50981cf62dc3
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757819"
---
# <a name="advanced-concepts-and-internals"></a>Conceitos avançados e recursos internos

_Esta seção contém tópicos que explicam a arquitetura, o design de API e as limitações do Xamarin. Android. Além disso, ele inclui tópicos que explicam sua implementação de coleta de lixo e os assemblies que estão disponíveis no Xamarin. Android. Como o Xamarin. Android é de [código-fonte aberto](https://github.com/xamarin/xamarin-android), também é possível entender o funcionamento interno do Xamarin. Android examinando seu código-fonte._

## <a name="architectureandroidinternalsarchitecturemd"></a>[Arquitetura](~/android/internals/architecture.md)

Este artigo explica a arquitetura subjacente por trás de um aplicativo Xamarin. Android. Ele explica como os aplicativos Xamarin. Android são executados dentro de um ambiente de execução mono juntamente com a máquina virtual de tempo de execução do Android e explica esses conceitos importantes como invólucros que podem ser chamados pelo Android e wrappers callable. 

## <a name="api-designandroidinternalsapi-designmd"></a>[Design de API](~/android/internals/api-design.md)

Além das principais bibliotecas de classes base que fazem parte do mono, o Xamarin. Android é fornecido com associações para várias APIs do Android para permitir que os desenvolvedores criem aplicativos Android nativos com o mono.

No núcleo do Xamarin. Android há um mecanismo de interoperabilidade que une C# o mundo com o mundo Java e fornece aos desenvolvedores acesso às APIs Java de C# ou de outras linguagens .net.

## <a name="assembliescross-platforminternalsavailable-assembliesmd"></a>[Assemblies](~/cross-platform/internals/available-assemblies.md)

O Xamarin. Android é fornecido com vários assemblies. Assim como o Silverlight é um subconjunto estendido dos assemblies do .NET da área de trabalho, o Xamarin. Android também é um subconjunto estendido de vários assemblies do Silverlight e do .NET da área de trabalho. 
