---
title: Nos bastidores
description: Um inspecionar o funcionamento interno do Xamarin.Mac
ms.topic: article
ms.prod: xamarin
ms.assetid: 84974D75-0CCE-4455-AA38-00DE68AE33B6
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 11/10/2017
ms.openlocfilehash: 2ba3ffb421dc64bba7df1e10a40125f14365f29e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="under-the-hood"></a>Nos bastidores

_Um inspecionar o funcionamento interno do Xamarin.Mac_

## <a name="ahead-of-time-compilation-aotaotmd"></a>[Em frente de compilação (AOT)](aot.md)

Em frente (AOT) de tempo de compilação é uma técnica de otimização poderosa para melhorar o desempenho de inicialização. No entanto, isso também afeta o tempo de compilação, o tamanho do aplicativo e a execução do programa de maneiras profundas, por isso é útil entender como ele funciona.

## <a name="mac-architecturearchitecturemd"></a>[Arquitetura do Mac](architecture.md)

Relação do Xamarin.Mac para Objective-C, incluindo conceitos como a compilação, seletores, registradores, abrir o aplicativo e o gerador.

## <a name="xamarinmac-registrarregistrarmd"></a>[Registrador Xamarin.Mac](registrar.md)

Xamarin.Mac preenche a lacuna entre o mundo gerenciado e o tempo de execução do Cocoa, permitindo que as classes gerenciadas chamar classes não gerenciadas de Objective-C e ser chamado quando ocorrem eventos. O trabalho necessário para realizar essa "mágica" é tratado pelo registrador, mas entender o que está acontecendo "nos bastidores" às vezes pode ser útil.
