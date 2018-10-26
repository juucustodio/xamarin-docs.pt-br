---
title: Nos bastidores no xamarin. Mac
description: Este documento leva a vários guias que descrevem o funcionamento interno do xamarin. Mac. Discutem documentos vinculados à frente de compilação de tempo, a arquitetura de xamarin. Mac e o registrador de xamarin. Mac.
ms.prod: xamarin
ms.assetid: 84974D75-0CCE-4455-AA38-00DE68AE33B6
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 11/10/2017
ms.openlocfilehash: 872f26febf3abbe4d659773d2bf2d27348c64513
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118767"
---
# <a name="under-the-hood-in-xamarinmac"></a>Nos bastidores no xamarin. Mac

## <a name="ahead-of-time-compilation-aotaotmd"></a>[Mais do que a compilação de time (AOT)](aot.md)

Em frente de tempo (AOT) a compilação é uma técnica de otimização poderosos para melhorar o desempenho de inicialização. No entanto, isso também afeta seu tempo de compilação, o tamanho do aplicativo e a execução do programa de maneiras profundas, portanto, vale a pena entender como ele funciona.

## <a name="mac-architecturearchitecturemd"></a>[Arquitetura do Mac](architecture.md)

Relação do xamarin. Mac para Objective-C, incluindo conceitos como compilação, seletores, registradores, inicialização do aplicativo e o gerador.

## <a name="xamarinmac-registrarregistrarmd"></a>[Registrador de xamarin. Mac](registrar.md)

Xamarin. Mac preenche a lacuna entre o mundo gerenciado e o tempo de execução do Cocoa, permitindo que as classes gerenciadas chamar a classes não gerenciadas de Objective-C e ser chamado novamente quando ocorrem eventos. O trabalho necessário para realizar essa "mágica" é tratado pelo registrador, mas entender o que está acontecendo "nos bastidores" às vezes pode ser útil.
