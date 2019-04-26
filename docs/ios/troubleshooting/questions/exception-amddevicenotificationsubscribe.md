---
title: System.Exception AMDeviceNotificationSubscribe retornou ...
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7E4ACC7E-F4FB-46C1-8837-C7FBAAFB2DC7
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 4fb0712366422e8810a2db60d40c3b85d9f4cd82
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61421938"
---
# <a name="systemexception-amdevicenotificationsubscribe-returned-"></a>System.Exception AMDeviceNotificationSubscribe retornou ...

> [!IMPORTANT]
> Esse problema foi resolvido em versões recentes do Xamarin. No entanto, se o problema ocorre na versão mais recente do software, envie uma [novo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) com o controle de versão completo completo e informações saída de log de compilação.


## <a name="fix"></a>Correção

1.  Eliminar o `usbmuxd` processar de forma que o sistema reiniciará:

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

Essa mensagem pode aparecer em uma caixa de diálogo de erro quando você começa a Visual Studio para Mac ou nos `mtbserver.log` arquivo no aplicativo Host de Build do xamarin. IOS (**Host de Build do xamarin. IOS > Exibir Log do Host de Build**).

Observe que esse é um problema incomum. Se o Visual Studio está tendo problemas para se conectar ao host de build do Mac, há outros erros que mais provavelmente aparecerão no `mtbserver.log` arquivo.

### <a name="errors-in-systemlog"></a>Erros no System

Erro alguns casos, os dois seguintes mensagens também podem aparecer várias vezes em `/var/log/system.log`:

```csharp
17:17:11.369 usbmuxd[55040]: dnssd_clientstub ConnectToServer: socket failed 24 Too many open files
17:17:11.369 com.apple.usbmuxd[55040]: _BrowseReplyReceivedCallback Error doing DNSServiceResolve(): -65539
```

## <a name="additional-information"></a>Informações adicionais

Uma estimativa a causa do erro é que o OS X, serviços do sistema responsáveis por relatar as informações de dispositivo e simulador iOS podem em casos raros entrar em um estado inesperado. Xamarin não é possível interagir corretamente com os serviços do sistema nesse estado. Reinicializar o computador reinicia os serviços do sistema e resolve o problema.

Com base nos erros a partir `system.log` parece que esse problema pode estar relacionado ao Bonjour (`mDNSResponder`). Alterando entre diferentes redes Wi-Fi parece aumentar as chances de atingir o problema.

## <a name="references"></a>Referências

*   [Bug 11789 - MonoTouch.MobileDevice.MobileDeviceException: AMDeviceNotificationSubscribe retornou: 0xe8000063 [RESOLVED NORESPONSE]](https://bugzilla.xamarin.com/show_bug.cgi?id=11789)
