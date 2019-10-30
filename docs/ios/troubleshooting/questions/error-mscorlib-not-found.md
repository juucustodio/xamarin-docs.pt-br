---
title: 'Erro de tempo de execução: o assembly mscorlib. dll não foi encontrado ou não pôde ser carregado'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 1027E16C-2C14-4BB5-AAAB-342F3E28E22E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: 1ca6aa8c08d51b91f6a31407328f8949062bbede
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031158"
---
# <a name="runtime-error-the-assembly-mscorlibdll-was-not-found-or-could-not-be-loaded"></a>Erro de tempo de execução: o assembly mscorlib. dll não foi encontrado ou não pôde ser carregado

```
<Warning>: The assembly mscorlib.dll was not found or could not be loaded.
<Warning>: It should have been installed in the `/Developer/MonoTouch/Source/monotouch/builds/install/target64/lib/mono/2.0/mscorlib.dll' directory.
<Warning>: Service exited with abnormal code: 1
```

Esse problema ocorre quando as pastas *ocultas* `.monotouch-32` e `.monotouch-64` estão faltando na `.xcarchive` para a criação de assinatura/IPA, disparando o erro de tempo de execução.
