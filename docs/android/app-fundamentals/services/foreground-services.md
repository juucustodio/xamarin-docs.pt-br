---
title: Serviços em primeiro plano
ms.prod: xamarin
ms.assetid: C10FD999-7A91-4708-B642-0C1B0901BD24
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/19/2018
ms.openlocfilehash: 6f3427641ba4ace3b640fcc970fd33f55087a9c8
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2019
ms.locfileid: "68644105"
---
# <a name="foreground-services"></a>Serviços em primeiro plano

Um serviço de primeiro plano é um tipo especial de serviço associado ou de um serviço iniciado. Ocasionalmente, os serviços executarão tarefas que os usuários devem conhecer ativamente, esses serviços são conhecidos como _serviços em primeiro plano_. Um exemplo de um serviço de primeiro plano é um aplicativo que está fornecendo ao usuário instruções enquanto conduz ou percorrendo. Mesmo que o aplicativo esteja em segundo plano, ainda é importante que o serviço tenha recursos suficientes para funcionar corretamente e que o usuário tenha uma maneira rápida e prática de acessar o aplicativo. Para um aplicativo Android, isso significa que um serviço de primeiro plano deve receber uma prioridade mais alta do que um serviço "regular" e um `Notification` serviço de primeiro plano deve fornecer um que o Android exibirá contanto que o serviço esteja em execução.

Para iniciar um serviço em primeiro plano, o aplicativo deve despachar uma intenção que informa ao Android para iniciar o serviço. Em seguida, o serviço deve se registrar como um serviço de primeiro plano com o Android. Os aplicativos que estão em execução no Android 8,0 (ou superior) devem `Context.StartForegroundService` usar o método para iniciar o serviço, enquanto os aplicativos que estão sendo executados em dispositivos com uma versão mais antiga do Android devem usar`Context.StartService`

Esse C# método de extensão é um exemplo de como iniciar um serviço em primeiro plano. No Android 8,0 e superior, ele usará `StartForegroundService` o método, caso contrário `StartService` , o método mais antigo será usado.

```csharp
public static void StartForegroundServiceCompat<T>(this Context context, Bundle args = null) where T : Service
{
    var intent = new Intent(context, typeof(T));
    if (args != null) 
    {
        intent.PutExtras(args);
    }

    if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.O)
    {
        context.StartForegroundService(intent);
    }
    else
    {
        context.StartService(intent);
    }
}
```

## <a name="registering-as-a-foreground-service"></a>Registrando como um serviço de primeiro plano

Depois que um serviço de primeiro plano é iniciado, ele deve se registrar no Android invocando o [`StartForeground`](xref:Android.App.Service.StartForeground*). Se o serviço for iniciado com o `Service.StartForegroundService` método, mas não se registrar, o Android interromperá o serviço e sinalizará o aplicativo como não responsivo.

`StartForeground`usa dois parâmetros, ambos obrigatórios:

- Um valor inteiro que é exclusivo dentro do aplicativo para identificar o serviço.
- Um `Notification` objeto que o Android exibirá na barra de status, contanto que o serviço esteja em execução.

O Android exibirá a notificação na barra de status durante o tempo em que o serviço estiver em execução. A notificação, no mínimo, fornecerá uma indicação visual para o usuário em que o serviço está sendo executado. O ideal é que a notificação forneça ao usuário um atalho para o aplicativo ou, possivelmente, alguns botões de ação para controlar o aplicativo. Um exemplo disso é um player &ndash; de música que a notificação exibida pode ter botões para pausar/reproduzir música, rebobinar para a música anterior ou para ir para a próxima música. 

Este trecho de código é um exemplo de registro de um serviço como um serviço em primeiro plano:   

```csharp
// This is any integer value unique to the application.
public const int SERVICE_RUNNING_NOTIFICATION_ID = 10000;

public override StartCommandResult OnStartCommand(Intent intent, StartCommandFlags flags, int startId)
{
    // Code not directly related to publishing the notification has been omitted for clarity.
    // Normally, this method would hold the code to be run when the service is started.

    var notification = new Notification.Builder(this)
        .SetContentTitle(Resources.GetString(Resource.String.app_name))
        .SetContentText(Resources.GetString(Resource.String.notification_text))
        .SetSmallIcon(Resource.Drawable.ic_stat_name)
        .SetContentIntent(BuildIntentToShowMainActivity())
        .SetOngoing(true)
        .AddAction(BuildRestartTimerAction())
        .AddAction(BuildStopServiceAction())
        .Build();

    // Enlist this instance of the service as a foreground service
    StartForeground(SERVICE_RUNNING_NOTIFICATION_ID, notification);
}
```

A notificação anterior exibirá uma notificação da barra de status semelhante à seguinte:

![Imagem mostrando a notificação na barra de status](foreground-services-images/foreground-services-01.png "Imagem mostrando a notificação na barra de status")

Esta captura de tela mostra a notificação expandida na bandeja de notificação com duas ações que permitem ao usuário controlar o serviço:

![Imagem mostrando a notificação expandida](foreground-services-images/foreground-services-02.png "Imagem mostrando a notificação expandida.")

Mais informações sobre notificações estão disponíveis na seção [notificações locais](~/android/app-fundamentals/notifications/local-notifications.md) do guia de [notificações do Android](~/android/app-fundamentals/notifications/index.md) .

## <a name="unregistering-as-a-foreground-service"></a>Cancelando o registro como um serviço de primeiro plano

Um serviço pode se deslistar como um serviço de primeiro plano chamando o `StopForeground`método. `StopForeground`o não interromperá o serviço, mas removerá o ícone de notificação e sinalizará o Android de que esse serviço pode ser desligado, se necessário.

A notificação da barra de status exibida também pode ser removida passando `true` para o método: 

```csharp
StopForeground(true);
```

Se o serviço for interrompido com uma chamada para `StopSelf` ou `StopService`, a notificação da barra de status será removida.

## <a name="related-links"></a>Links relacionados

- [Android.App.Service](xref:Android.App.Service)
- [Android.App.Service.StartForeground](xref:Android.App.Service.StartForeground*)
- [Notificações locais](~/android/app-fundamentals/notifications/local-notifications.md)
- [ForegroundServiceDemo (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-servicesamples-foregroundservicedemo)
