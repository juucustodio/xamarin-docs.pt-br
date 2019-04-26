---
title: MDocArchiveToMsxDocConverter.exe não encontrado rver.BaseCommand.OnRequest
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F5AC6AC4-0E7C-4746-A7CF-872F0E75AFF4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 0746174857f66843ef9a09429b6286f2efca90d6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61420531"
---
# <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequest"></a>MDocArchiveToMsxDocConverter.exe não encontrado rver.BaseCommand.OnRequest

> [!IMPORTANT]
> Esse problema foi resolvido em versões recentes do Xamarin. No entanto, se o problema ocorre na versão mais recente do software, envie uma [novo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) com o controle de versão completo completo e informações saída de log de compilação.


## <a name="error-message"></a>Mensagem de erro

Esse erro pode aparecer na *Log de servidor Mac* no Visual Studio:

```
Error: /Developer/MonoTouch/usr/share/doc/MonoTouch/MDocArchiveToMsxDocConverter.exe not found
 rver.BaseCommand.OnRequest (System.Net.HttpListenerContext context, System.Object commandRequestState) [0x00000] in <filename unknown>:0
  at Mtb.Server.Listener.OnRequest (System.Object state) [0x00000] in <filename unknown>:0
```

Há 2 problemas separados nessa mensagem:

1.  `Error: /Developer/MonoTouch/usr/share/doc/MonoTouch/MDocArchiveToMsxDocConverter.exe not found`

    Esse erro é inofensivo, mas ele também está incorreta. Ele [serão removidas](https://bugzilla.xamarin.com/show_bug.cgi?id=21667) em uma versão futura.

2.  `rver.BaseCommand.OnRequest (System.Net.HttpListenerContext context …`

    Esse erro é o problema real. Infelizmente, devido a um [limitação](https://bugzilla.xamarin.com/show_bug.cgi?id=22080) esse rastreamento de pilha de exceção *incompleta*. Se você observar um rastreamento de pilha incompleta como esta no Log do servidor Mac, você pode verificar o `~/Library/Logs/Xamarin/MonoTouchVS/mtbserver.log` arquivo no host de build do Mac para localizar o rastreamento de pilha completa.
