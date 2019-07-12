---
title: 'Por que meu aplicativo iOS 9 falha com: System.Exception: Falha ao realizar marshaling do objeto Objective-C?'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8805ABEC-48D4-4CCB-A226-3A5B2ECE4BF0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: 3dbb4d9132d5d94e4533704730e95002b5aec0be
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832265"
---
# <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-object"></a>Por que meu aplicativo iOS 9 falha com: System.Exception: Falha ao realizar marshaling do objeto Objective-C?

Você verá um erro neste formato:

> System.Exception: Falha ao realizar marshaling do objeto Objective-C... Não foi possível localizar uma instância gerenciada existente para esse objeto...

Alterações de API no iOS 9 exigem que um construtor de retorno de chamada usado ao chamar código não gerenciado, como a API básica agora espera que ele. Use a linha a seguir para adicionar o construtor de retorno de chamada à classe: 

`public foo (IntPtr handle) : base (handle)` 

### <a name="next-steps"></a>Próximas etapas

Para obter mais assistência, entre em contato conosco ou se esse problema permanece mesmo após utilizando as informações acima, consulte [quais opções de suporte estão disponíveis para o Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obter informações sobre opções de contato, sugestões, e também como um novo bug de arquivo se necessário. 
