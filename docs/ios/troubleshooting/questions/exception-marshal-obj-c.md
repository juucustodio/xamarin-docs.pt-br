---
title: 'Por que meu aplicativo iOS 9 falha com: System. Exception: falha ao realizar marshaling do objeto Objective-C?'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8805ABEC-48D4-4CCB-A226-3A5B2ECE4BF0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: 1bb67eaa884e523e96ef1015daaa6b959ea1512d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031105"
---
# <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-object"></a>Por que meu aplicativo iOS 9 falha com: System. Exception: falha ao realizar marshaling do objeto Objective-C?

Você pode ver um erro neste formulário:

> System. Exception: falha ao realizar marshaling do objeto Objective-C... Não foi possível encontrar uma instância gerenciada existente para este objeto...

As alterações de API no iOS 9 exigem que um construtor de retorno de chamada seja usado ao chamar código não gerenciado, pois a API subjacente agora o espera. Use a linha a seguir para adicionar o construtor de retorno de chamada à classe: 

`public foo (IntPtr handle) : base (handle)` 

### <a name="next-steps"></a>Próximas etapas

Para obter mais assistência, entrar em contato conosco ou, se esse problema permanecer mesmo depois de utilizar as informações acima, consulte [quais opções de suporte estão disponíveis para o Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obter informações sobre opções de contato, sugestões, bem como como arquivar um novo bug, se necessário . 
