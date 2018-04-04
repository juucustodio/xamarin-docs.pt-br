---
title: Serviços de primeiro plano
ms.prod: xamarin
ms.assetid: C10FD999-7A91-4708-B642-0C1B0901BD24
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 03/19/2018
ms.openlocfilehash: 3088fa4b5cfa21ac57533ef331ffcc15414e14b4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="foreground-services"></a>Serviços de primeiro plano

Um serviço de primeiro plano é um tipo especial de um serviço vinculado ou um serviço iniciado. Ocasionalmente, esses serviços serviços executará tarefas de usuários devem estar atento ativamente, são conhecidos como _serviços de primeiro plano_. Um exemplo de um serviço de primeiro plano é um aplicativo que está fornecendo o usuário com direções ao dirigir ou andar. Mesmo se o aplicativo estiver em segundo plano, é importante que o serviço tem recursos suficientes para funcionar corretamente e se o usuário tem uma maneira rápida e conveniente para acessar o aplicativo. Para um aplicativo do Android, isso significa que um serviço de primeiro plano deve receber uma prioridade maior do que um serviço "normal" e um serviço de primeiro plano deve fornecer um `Notification` que Android será exibido como o serviço está em execução.
 
Para iniciar um serviço de primeiro plano, o aplicativo deve distribuir uma intenção que informa ao Android para iniciar o serviço. Em seguida, o serviço deve se registrar como um serviço de primeiro plano com Android. Aplicativos que estão em execução no Android 8.0 (ou superior) devem usar o `Context.StartForegroundService` método para iniciar o serviço, enquanto os aplicativos que são executados em dispositivos com uma versão mais antiga do Android deve usar `Context.StartService`

Esse método de extensão do c# é um exemplo de como iniciar um serviço de primeiro plano. No Android 8.0 e posteriores usarão o `StartForegroundService` método, caso contrário, o antigo `StartService` método será usado.  

```csharp
public static void StartForegroundServiceComapt<T>(this Context context, Bundle args = null) where T : Service
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

## <a name="registering-as-a-foreground-service"></a>Registrar como um serviço de primeiro plano

Depois que um serviço de primeiro plano foi iniciado, ele deve se registrar com o Android invocando o [ `StartForeground` ](https://developer.xamarin.com/api/member/Android.App.Service.StartForeground/p/System.Int32/Android.App.Notification/). Se o serviço foi iniciado com a `Service.StartForegroundService` método, mas não se registrou, em seguida, Android interromperá o serviço e sinalizador o aplicativo como sem resposta.

`StartForeground` utiliza dois parâmetros, que são obrigatórias:
 
* Um valor inteiro que é exclusivo dentro do aplicativo para identificar o serviço.
* Um `Notification` objeto Android será exibido na barra de status para desde que o serviço está em execução.

Android exibirá a notificação na barra de status para desde que o serviço está em execução. A notificação, no mínimo, fornecerá uma indicação visual para o usuário que o serviço está em execução. Idealmente, a notificação deve fornecer ao usuário um atalho para o aplicativo ou possivelmente alguns botões de ação para controlar o aplicativo. Um exemplo disso é um player de música &ndash; a notificação de que é exibida pode ter botões para pausar/executar música, rebobinar a música anterior, ou para ignorar a próxima música. 

Este trecho de código é um exemplo de registro de um serviço como um serviço de primeiro plano:   

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

A notificação anterior será exibida uma notificação da barra de status que que é semelhante ao seguinte:

![Imagem mostrando a notificação na barra de status](foreground-services-images/foreground-services-01.png "imagem mostrando a notificação na barra de status")

Esta captura de tela mostra a notificação expandida na bandeja de notificação com duas ações que permitem ao usuário controlar o serviço:

![Imagem mostrando a notificação expandida](foreground-services-images/foreground-services-02.png "imagem mostrando a notificação expandida.")

Para obter mais informações sobre notificações estão disponíveis na [notificações Local](~/android/app-fundamentals/notifications/local-notifications.md) seção o [notificações do Android](~/android/app-fundamentals/notifications/index.md) guia.

## <a name="unregistering-as-a-foreground-service"></a>Cancelando o registro como um serviço de primeiro plano

Um serviço pode cancelar própria lista como um serviço de primeiro plano chamando o método `StopForeground`. `StopForeground` não irá parar o serviço, mas ele removerá o ícone de notificação e sinaliza Android que esse serviço pode ser desligado se necessário.

Também pode ser removida de notificação da barra de status que é exibida ao passar `true` para o método: 

```csharp
StopForeground(true);
```

Se o serviço é interrompido com uma chamada para `StopSelf` ou `StopService`, a notificação da barra de status será removida.

## <a name="related-links"></a>Links relacionados

- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service/)
- [Android.App.Service.StartForegrond](https://developer.xamarin.com/api/member/Android.App.Service.StartForeground/p/System.Int32/Android.App.Notification/)
- [Notificações locais](~/android/app-fundamentals/notifications/local-notifications.md)
- [ForegroundServiceDemo (sample)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/ForegroundServiceDemo/)
