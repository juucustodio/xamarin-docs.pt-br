---
title: System. Exception AMDeviceNotificationSubscribe retornado...
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7E4ACC7E-F4FB-46C1-8837-C7FBAAFB2DC7
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 257e0c14de3c23825b6abe6601c25438db81c58e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30776476"
---
# <a name="systemexception-amdevicenotificationsubscribe-returned-"></a>System. Exception AMDeviceNotificationSubscribe retornado...

> [!IMPORTANT]
> Esse problema foi resolvido em versões recentes do Xamarin. No entanto, se o problema ocorre na versão mais recente do software, envie um [novo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) com o controle de versão completo completo e informações de log de saída de compilação.


## <a name="fix"></a>Correção

1.  Eliminar a `usbmuxd` processar de forma que o sistema reiniciará:

    ```csharp
    sudo killall -QUIT usbmuxd
    ```

2.  Se isso não resolver o problema, reinicialize o Mac.

## <a name="error-message"></a>Mensagem de erro

```csharp
Exception: Exception type: System.Exception
AMDeviceNotificationSubscribe returned: 3892314211
  at Xamarin.MacDev.IPhoneDeviceManager.SubscribeToNotifications () [0x00000] in <filename unknown="">:0
  at Xamarin.MacDev.IPhoneDeviceManager.StartWatching (Boolean useOwnRunloop) [0x00000] in <filename unknown="">:0
  at Mtb.Server.DeviceListener.Start () [0x00000] in <filename unknown="">:0
  at Mtb.Application.MainClass.RunDeviceListener () [0x00000] in <filename unknown="">:0
  at Mtb.Application.MainClass.Main (System.String[] args) [0x00000] in <filename unknown="">:0
```

Essa mensagem pode aparecer em uma caixa de diálogo de erro ao iniciar o Visual Studio para Mac, ou no `mtbserver.log` arquivo no aplicativo Host de Build xamarin (**xamarin criar Host > Exibir Log do Host de compilação**).

Observe que esse é um problema comum. Se o Visual Studio está tendo problemas para se conectar ao host de compilação de Mac, há outros erros que têm mais probabilidade de aparecer no `mtbserver.log` arquivo.

### <a name="errors-in-systemlog"></a>Erros no system.log

Erro alguns casos, os dois seguintes mensagens também podem aparecer várias vezes em `/var/log/system.log`:

```csharp
17:17:11.369 usbmuxd[55040]: dnssd_clientstub ConnectToServer: socket failed 24 Too many open files
17:17:11.369 com.apple.usbmuxd[55040]: _BrowseReplyReceivedCallback Error doing DNSServiceResolve(): -65539
```

## <a name="additional-information"></a>Informações adicionais

Uma estimativa a causa raiz do erro é que o OS X serviços do sistema responsáveis por relatar informações de dispositivo e do simulador de iOS podem em casos raros, insira um estado inesperado. Xamarin não é possível interagir corretamente com os serviços do sistema nesse estado. Reinicializar o computador reinicia os serviços do sistema e resolve o problema.

Com base em erros de `system.log` parece que esse problema pode estar relacionado a Bon jour (`mDNSResponder`). Alterando entre diferentes redes Wi-Fi parece aumentar as chances de atingir o problema.

## <a name="references"></a>Referências

*   [Erro 11789 - MonoTouch.MobileDevice.MobileDeviceException: AMDeviceNotificationSubscribe retornado: 0xe8000063 [RESOLVIDO SEM_RESPOSTA]](https://bugzilla.xamarin.com/show_bug.cgi?id=11789)
