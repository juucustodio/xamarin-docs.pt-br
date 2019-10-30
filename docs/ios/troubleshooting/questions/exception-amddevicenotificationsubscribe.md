---
title: System.Exception AMDeviceNotificationSubscribe retornou ...
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7E4ACC7E-F4FB-46C1-8837-C7FBAAFB2DC7
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: e1633989fc9b85d969f464857ab763153aea2e7d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031109"
---
# <a name="systemexception-amdevicenotificationsubscribe-returned-"></a>System.Exception AMDeviceNotificationSubscribe retornou ...

> [!IMPORTANT]
> Esse problema foi resolvido nas versões recentes do Xamarin. No entanto, se o problema ocorrer na versão mais recente do software, registre um [novo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) com suas informações completas de controle de versão e saída de log de compilação completa.

## <a name="fix"></a>Correção

1. Encerre o processo de `usbmuxd` para que o sistema o reinicie:

    ```csharp
    sudo killall -QUIT usbmuxd
    ```

2. Se isso não resolver o problema, reinicialize o Mac.

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

Essa mensagem pode aparecer em uma caixa de diálogo de erro quando você inicia o Visual Studio para Mac pela primeira vez ou no arquivo de `mtbserver.log` no aplicativo de host de Build do Xamarin. iOS (**xamarin. Ios Build host > exibir log de host**de Build).

Observe que esse é um problema incomum. Se o Visual Studio estiver tendo problemas para se conectar ao host de Build do Mac, haverá outros erros que têm mais probabilidade de aparecer no arquivo de `mtbserver.log`.

### <a name="errors-in-systemlog"></a>Erros em System. log

Em alguns casos, as duas mensagens de erro a seguir também podem aparecer repetidamente em `/var/log/system.log`:

```csharp
17:17:11.369 usbmuxd[55040]: dnssd_clientstub ConnectToServer: socket failed 24 Too many open files
17:17:11.369 com.apple.usbmuxd[55040]: _BrowseReplyReceivedCallback Error doing DNSServiceResolve(): -65539
```

## <a name="additional-information"></a>Informações adicionais

Uma estimativa na causa raiz do erro é que os serviços do sistema do OS X responsáveis por relatar informações do dispositivo iOS e do simulador podem, em casos raros, entrar em um estado inesperado. O Xamarin não pode interagir corretamente com os serviços do sistema nesse estado. Reinicializar o computador reinicia os serviços do sistema e resolve o problema.

Com base nos erros de `system.log` parece que esse problema pode estar relacionado a Bonjour (`mDNSResponder`). A alteração entre redes WiFi diferentes parece aumentar as chances de atingir o problema.

## <a name="references"></a>Referências

* [Bug 11789-MonoTouch. MobileDevice. MobileDeviceException: AMDeviceNotificationSubscribe retornado: 0xe8000063 [resolvido NoResponse]](https://bugzilla.xamarin.com/show_bug.cgi?id=11789)
