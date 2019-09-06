---
title: Nos bastidores do Xamarin. Mac
description: Este documento contém links para vários guias que descrevem o funcionamento interno do Xamarin. Mac. Os documentos vinculados abordam antecipadamente a compilação do tempo, a arquitetura do Xamarin. Mac e o registrador Xamarin. Mac.
ms.prod: xamarin
ms.assetid: 84974D75-0CCE-4455-AA38-00DE68AE33B6
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 11/10/2017
ms.openlocfilehash: 51cf479ba07a769f5d7a875bb3f1203caef2ad0b
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290105"
---
# <a name="under-the-hood-in-xamarinmac"></a>Nos bastidores do Xamarin. Mac

## <a name="ahead-of-time-compilation-aotaotmd"></a>[AOT (compilação antecipada de tempo)](aot.md)

A compilação antecipada de tempo (AOT) é uma poderosa técnica de otimização para melhorar o desempenho de inicialização. No entanto, ele também afeta o tempo de compilação, o tamanho do aplicativo e a execução do programa de maneiras profundas, portanto, vale a pena entender como ele funciona.

## <a name="mac-architecturearchitecturemd"></a>[Arquitetura do Mac](architecture.md)

A relação do Xamarin. Mac com Objective-C, incluindo conceitos como compilação, seletores, registradores, inicialização do aplicativo e o gerador.

## <a name="xamarinmac-registrarregistrarmd"></a>[Registrador do Xamarin. Mac](registrar.md)

O Xamarin. Mac preenche a lacuna entre o mundo gerenciado e o tempo de execução do Cocoa, permitindo que classes gerenciadas chamem classes Objective-C não gerenciadas e sejam chamadas de volta quando ocorrerem eventos. O trabalho necessário para realizar essa "mágica" é tratado pelo registrador, mas entender o que está acontecendo "nos bastidores" pode, às vezes, ser útil.
