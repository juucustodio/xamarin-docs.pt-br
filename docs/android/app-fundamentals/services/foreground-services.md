---
title: Serviços de primeiro plano
ms.prod: xamarin
ms.assetid: C10FD999-7A91-4708-B642-0C1B0901BD24
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/19/2018
ms.openlocfilehash: df917896f901060a5518076afa859d34a03f4d6d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108347"
---
# <a name="foreground-services"></a>Serviços de primeiro plano

Um serviço de primeiro plano é um tipo especial de um serviço vinculado ou um serviço iniciado. Ocasionalmente, esses serviços serviços irá executar as tarefas que os usuários devem estar atento ativamente, são conhecidos como _serviços em primeiro plano_. Um exemplo de um serviço de primeiro plano é um aplicativo que está fornecendo o usuário com direções ao dirigir ou andar. Mesmo se o aplicativo está em segundo plano, ele ainda é importante que o serviço tem recursos suficientes para funcionar corretamente e se o usuário tem uma maneira rápida e útil para acessar o aplicativo. Para um aplicativo Android, isso significa que um serviço de primeiro plano deve receber uma prioridade maior que um serviço "normal" e um serviço de primeiro plano deve fornecer um `Notification` que Android exibirá desde que o serviço está em execução.
 
Para iniciar um serviço de primeiro plano, o aplicativo deve despachar uma intenção que informa ao Android para iniciar o serviço. Em seguida, o serviço deve se registrar como um serviço de primeiro plano com o Android. Aplicativos que estão em execução no Android 8.0 (ou superior) devem usar o `Context.StartForegroundService` método para iniciar o serviço, enquanto os aplicativos que são executados em dispositivos com uma versão mais antiga do Android devem usar `Context.StartService`

Esse método de extensão do c# é um exemplo de como iniciar um serviço de primeiro plano. No Android 8.0 e superior usará o `StartForegroundService` método, caso contrário, o mais antigo `StartService` método será usado.  

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

Depois que um serviço de primeiro plano for iniciado, ele deve ser registrado com o Android invocando o [ `StartForeground` ](https://developer.xamarin.com/api/member/Android.App.Service.StartForeground/p/System.Int32/Android.App.Notification/). Se o serviço é iniciado com o `Service.StartForegroundService` método, mas não se registrou, em seguida, o Android interromperá o serviço e sinalizar o aplicativo como não responsivo.

`StartForeground` usa dois parâmetros, sendo que ambos são obrigatórios:
 
* Um valor inteiro que é exclusivo dentro do aplicativo para identificar o serviço.
* Um `Notification` objeto Android será exibido na barra de status para desde que o serviço está em execução.

Android exibirá a notificação na barra de status para desde que o serviço está em execução. A notificação, no mínimo, fornecerá uma indicação visual para o usuário que o serviço está em execução. O ideal é que a notificação deve fornecer ao usuário um atalho para o aplicativo ou, possivelmente, alguns botões de ação para controlar o aplicativo. Um exemplo disso é um player de música &ndash; a notificação que é exibido pode ter botões para pausar/reproduzir música, para retroceder a música anterior, ou para ignorar a próxima música. 

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

A notificação anterior será exibida uma notificação da barra de status é semelhante ao seguinte:

![Imagem mostrando a notificação na barra de status](foreground-services-images/foreground-services-01.png "imagem mostrando a notificação na barra de status")

Esta captura de tela mostra a notificação expandida na bandeja de notificação com duas ações que permitem ao usuário controlar o serviço:

![Imagem mostrando a notificação expandida](foreground-services-images/foreground-services-02.png "imagem mostrando a notificação expandida.")

Para obter mais informações sobre notificações estão disponíveis na [notificações locais](~/android/app-fundamentals/notifications/local-notifications.md) seção o [notificações do Android](~/android/app-fundamentals/notifications/index.md) guia.

## <a name="unregistering-as-a-foreground-service"></a>Cancelando o registro como um serviço de primeiro plano

Um serviço pode desprovisionar listar em si como um serviço de primeiro plano, chamando o método `StopForeground`. `StopForeground` não irá parar o serviço, mas ela removerá o ícone de notificação e sinais de Android que esse serviço pode ser desligado se necessário.

A notificação da barra de status é exibida também pode ser removida, passando `true` para o método: 

```csharp
StopForeground(true);
```

Se o serviço é interrompido com uma chamada para `StopSelf` ou `StopService`, a notificação da barra de status será removida.

## <a name="related-links"></a>Links relacionados

- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service/)
- [Android.App.Service.StartForeground](https://developer.xamarin.com/api/member/Android.App.Service.StartForeground/p/System.Int32/Android.App.Notification/)
- [Notificações locais](~/android/app-fundamentals/notifications/local-notifications.md)
- [ForegroundServiceDemo (amostra)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/ForegroundServiceDemo/)
