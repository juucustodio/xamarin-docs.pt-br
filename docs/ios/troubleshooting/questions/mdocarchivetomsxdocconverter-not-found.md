---
title: "MDocArchiveToMsxDocConverter.exe não encontrado servidor &. BaseCommand.OnRequest"
ms.topic: article
ms.prod: xamarin
ms.assetid: F5AC6AC4-0E7C-4746-A7CF-872F0E75AFF4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 1e49c270d5836379d60f50ec72960ddc83bfbba4
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2018
---
# <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequest"></a>MDocArchiveToMsxDocConverter.exe não encontrado servidor &. BaseCommand.OnRequest

> [!IMPORTANT]
> Esse problema foi resolvido em versões recentes do Xamarin. No entanto, se o problema ocorre na versão mais recente do software, envie um [novo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) com o controle de versão completo completo e informações de log de saída de compilação.


## <a name="error-message"></a>Mensagem de erro

Esse erro pode aparecer no *Mac servidor Log* no Visual Studio:

```
Error: /Developer/MonoTouch/usr/share/doc/MonoTouch/MDocArchiveToMsxDocConverter.exe not found
 rver.BaseCommand.OnRequest (System.Net.HttpListenerContext context, System.Object commandRequestState) [0x00000] in <filename unknown>:0
  at Mtb.Server.Listener.OnRequest (System.Object state) [0x00000] in <filename unknown>:0
```

Há 2 problemas separados esta mensagem:

1.  `Error: /Developer/MonoTouch/usr/share/doc/MonoTouch/MDocArchiveToMsxDocConverter.exe not found`

    Esse erro é inofensivo, mas ele também está incorreta. Ele [será removido](https://bugzilla.xamarin.com/show_bug.cgi?id=21667) em uma versão futura.

2.  `rver.BaseCommand.OnRequest (System.Net.HttpListenerContext context …`

    Esse erro é o problema real. Infelizmente, devido a um [limitação](https://bugzilla.xamarin.com/show_bug.cgi?id=22080) este rastreamento de pilha de exceção é *incompleta*. Se você notar um rastreamento de pilha incompleta como esse no Log de servidor do Mac, você pode verificar o `~/Library/Logs/Xamarin/MonoTouchVS/mtbserver.log` arquivo no host de compilação de Mac para localizar o rastreamento de pilha completa.
