---
title: 'Erro de tempo de execução: O assembly mscorlib. dll não foi encontrado ou não pôde ser carregado'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 1027E16C-2C14-4BB5-AAAB-342F3E28E22E
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: a1c6c5357c62d0421376930ea04c4755fae59d04
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769347"
---
# <a name="runtime-error-the-assembly-mscorlibdll-was-not-found-or-could-not-be-loaded"></a>Erro de tempo de execução: O assembly mscorlib. dll não foi encontrado ou não pôde ser carregado

```
<Warning>: The assembly mscorlib.dll was not found or could not be loaded.
<Warning>: It should have been installed in the `/Developer/MonoTouch/Source/monotouch/builds/install/target64/lib/mono/2.0/mscorlib.dll' directory.
<Warning>: Service exited with abnormal code: 1
```

Esse problema ocorre quando as pastas e `.monotouch-64` *ocultas* `.monotouch-32` estão ausentes `.xcarchive` na criação de assinatura/IPA, disparando o erro de tempo de execução.
