---
title: 'Por que meu aplicativo do iOS 9 falhar com: System. Exception: Falha ao realizar marshaling do objeto Objective-C?'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8805ABEC-48D4-4CCB-A226-3A5B2ECE4BF0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: f7382ac963249a3f3646a917d8700e3a12873ec9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30775969"
---
# <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-object"></a>Por que meu aplicativo do iOS 9 falhar com: System. Exception: Falha ao realizar marshaling do objeto Objective-C?

Você pode ver um erro deste formulário:

> System. Exception: Falha ao realizar marshaling do objeto Objective-C... Não foi possível localizar uma instância existente de gerenciado para este objeto...

Alterações de API no iOS 9 requerem que um construtor de retorno de chamada ser usado ao chamar código não gerenciado, como a API subjacente agora espera. Use a linha a seguir para adicionar o construtor de retorno de chamada para a classe: 

`public foo (IntPtr handle) : base (handle) ` 

### <a name="next-steps"></a>Próximas etapas

Para obter mais assistência, entre em contato conosco ou se esse problema permanece mesmo após utilizando as informações acima, consulte [quais opções de suporte estão disponíveis para Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obter informações sobre opções de contato, sugestões, bem como a registrar um bug de novo, se necessário. 
