---
title: Nos bastidores em Xamarin.Mac
description: Links este documento para várias guias que descrevem o funcionamento interno de Xamarin.Mac. Discutem documentos vinculados à frente de compilação, a arquitetura de Xamarin.Mac e o registrador de Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 84974D75-0CCE-4455-AA38-00DE68AE33B6
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 11/10/2017
ms.openlocfilehash: c940252a675c38247d2c5bb374b9c30237222bda
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792483"
---
# <a name="under-the-hood-in-xamarinmac"></a>Nos bastidores em Xamarin.Mac

## <a name="ahead-of-time-compilation-aotaotmd"></a>[Em frente de compilação (AOT)](aot.md)

Em frente (AOT) de tempo de compilação é uma técnica de otimização poderosa para melhorar o desempenho de inicialização. No entanto, isso também afeta o tempo de compilação, o tamanho do aplicativo e a execução do programa de maneiras profundas, por isso é útil entender como ele funciona.

## <a name="mac-architecturearchitecturemd"></a>[Arquitetura do Mac](architecture.md)

Relação do Xamarin.Mac para Objective-C, incluindo conceitos como a compilação, seletores, registradores, abrir o aplicativo e o gerador.

## <a name="xamarinmac-registrarregistrarmd"></a>[Registrador Xamarin.Mac](registrar.md)

Xamarin.Mac preenche a lacuna entre o mundo gerenciado e o tempo de execução do Cocoa, permitindo que as classes gerenciadas chamar classes não gerenciadas de Objective-C e ser chamado quando ocorrem eventos. O trabalho necessário para realizar essa "mágica" é tratado pelo registrador, mas entender o que está acontecendo "nos bastidores" às vezes pode ser útil.
