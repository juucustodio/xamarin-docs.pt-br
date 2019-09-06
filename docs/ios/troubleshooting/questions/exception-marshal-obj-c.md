---
title: 'Por que meu aplicativo iOS 9 falha com: System.Exception: Falha ao realizar marshaling do objeto Objective-C?'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8805ABEC-48D4-4CCB-A226-3A5B2ECE4BF0
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: 62a63dc5156d1acf9ad6ca15029978131c151726
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290467"
---
# <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-object"></a>Por que meu aplicativo iOS 9 falha com: System.Exception: Falha ao realizar marshaling do objeto Objective-C?

Você pode ver um erro neste formulário:

> System.Exception: Falha ao realizar marshaling do objeto Objective-C... Não foi possível encontrar uma instância gerenciada existente para este objeto...

As alterações de API no iOS 9 exigem que um construtor de retorno de chamada seja usado ao chamar código não gerenciado, pois a API subjacente agora o espera. Use a linha a seguir para adicionar o construtor de retorno de chamada à classe: 

`public foo (IntPtr handle) : base (handle)` 

### <a name="next-steps"></a>Próximas etapas

Para obter mais assistência, entrar em contato conosco ou, se esse problema permanecer mesmo depois de utilizar as informações acima, consulte [quais opções de suporte estão disponíveis para o Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obter informações sobre opções de contato, sugestões, bem como como arquivar um novo bug, se necessário . 
