---
title: Conceitos avançados e internos
description: Arquitetura subjacente atrás de xamarin e do design de API.
ms.prod: xamarin
ms.assetid: CC6A0D52-E9FA-4270-B3FA-84660621D6D5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/21/2018
ms.openlocfilehash: 79e61db4c27a2d29b4ee0a9d39f2d25ea5d93303
ms.sourcegitcommit: 9f8e7393019791bbd6af4fefaa24a1602adabb4e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/23/2018
ms.locfileid: "34458348"
---
# <a name="advanced-concepts-and-internals"></a>Conceitos avançados e internos

_Esta seção contém tópicos que explicam as limitações do xamarin, arquitetura e design de API. Além disso, ele inclui tópicos que explicam os assemblies que estão disponíveis no xamarin e sua implementação de coleta de lixo. Porque é xamarin [código-fonte aberto](https://github.com/xamarin/xamarin-android), também é possível entender o funcionamento interno do xamarin examinando seu código-fonte._


##  <a name="architectureandroidinternalsarchitecturemd"></a>[Arquitetura](~/android/internals/architecture.md)

Este artigo explica a arquitetura subjacente por trás de um aplicativo xamarin. Ele explica como xamarin aplicativos executados dentro de um ambiente de execução Mono junto com o tempo de execução Android Máquina Virtual e explica conceitos chave Android Callable Wrappers e gerenciados Callable Wrappers. 



##  <a name="api-designandroidinternalsapi-designmd"></a>[Design de API](~/android/internals/api-design.md)

Além das bibliotecas de classes Base que fazem parte do Mono principais, xamarin fornecido com associações para várias APIs Android permitir que os desenvolvedores criem aplicativos Android nativos com Mono.

No núcleo do xamarin existe é um mecanismo de interoperabilidade que world pontes c# com o mundo de Java e fornece aos desenvolvedores com acesso às APIs do Java de c# ou outras linguagens .NET.



##  <a name="assembliescross-platforminternalsavailable-assembliesmd"></a>[Assemblies](~/cross-platform/internals/available-assemblies.md)

Xamarin vem com vários assemblies. Assim como o Silverlight é um subconjunto estendido dos assemblies do .NET da área de trabalho, xamarin também é um subconjunto estendido de vários Silverlight e área de trabalho assemblies do .NET. 

