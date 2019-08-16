---
title: MDocArchiveToMsxDocConverter.exe não encontrado rver.BaseCommand.OnRequest
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F5AC6AC4-0E7C-4746-A7CF-872F0E75AFF4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: af90baf6d7b94973a76bcf391f2cb28c45340602
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528155"
---
# <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequest"></a>MDocArchiveToMsxDocConverter.exe não encontrado rver.BaseCommand.OnRequest

> [!IMPORTANT]
> Esse problema foi resolvido nas versões recentes do Xamarin. No entanto, se o problema ocorrer na versão mais recente do software, registre um [novo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) com suas informações completas de controle de versão e saída de log de compilação completa.


## <a name="error-message"></a>Mensagem de erro

Esse erro pode aparecer no *log do servidor Mac* no Visual Studio:

```
Error: /Developer/MonoTouch/usr/share/doc/MonoTouch/MDocArchiveToMsxDocConverter.exe not found
 rver.BaseCommand.OnRequest (System.Net.HttpListenerContext context, System.Object commandRequestState) [0x00000] in <filename unknown>:0
  at Mtb.Server.Listener.OnRequest (System.Object state) [0x00000] in <filename unknown>:0
```

Há dois problemas separados nesta mensagem:

1. `Error: /Developer/MonoTouch/usr/share/doc/MonoTouch/MDocArchiveToMsxDocConverter.exe not found`

    Esse erro é inofensivo, mas também é enganoso. Ele [será removido](https://bugzilla.xamarin.com/show_bug.cgi?id=21667) em uma versão futura.

2. `rver.BaseCommand.OnRequest (System.Net.HttpListenerContext context …`

    Esse erro é o verdadeiro problema. Infelizmente, devido a uma [limitação](https://bugzilla.xamarin.com/show_bug.cgi?id=22080) , esse rastreamento de pilhade exceção está incompleto. Se você observar um rastreamento de pilha incompleto como esse no log do servidor Mac, poderá verificar `~/Library/Logs/Xamarin/MonoTouchVS/mtbserver.log` o arquivo no host de Build do Mac para encontrar o rastreamento de pilha completo.
