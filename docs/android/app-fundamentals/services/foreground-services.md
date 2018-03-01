---
title: "Serviços de primeiro plano"
ms.topic: article
ms.prod: xamarin
ms.assetid: C10FD999-7A91-4708-B642-0C1B0901BD24
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 857c7fe47ad5fa0f50fce95672bc8ffbf6771dc9
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="foreground-services"></a>Serviços de primeiro plano

Alguns serviços são executar algumas tarefas que os usuários estejam cientes ativamente, esses serviços são conhecidos como _serviços de primeiro plano_. Um exemplo de um serviço de primeiro plano é um aplicativo que está fornecendo o usuário com direções ao dirigir ou andar. Mesmo se o aplicativo estiver em segundo plano, é importante que o serviço tem recursos suficientes para funcionar corretamente e se o usuário tem uma maneira rápida e conveniente para acessar o aplicativo. Para um aplicativo do Android, isso significa que um serviço de primeiro plano deve receber uma prioridade maior do que um serviço "normal" e um serviço de primeiro plano deve fornecer um `Notification` que Android será exibido como o serviço está em execução.
 
Um serviço de primeiro plano é criado e iniciado assim como qualquer outro serviço. Quando o serviço está sendo iniciado, ele será registrado com Android como um serviço de primeiro plano.
 
Este guia discutir as etapas adicionais que devem ser executadas para registrar um serviço de primeiro plano e parar o serviço quando estiver pronto.

## <a name="registering-as-a-foreground-service"></a>Registrar como um serviço de primeiro plano

Um serviço de primeiro plano é um tipo especial de um serviço vinculado ou um serviço iniciado. O serviço depois de iniciada, chama o [ `StartForeground` ](https://developer.xamarin.com/api/member/Android.App.Service.StartForeground/p/System.Int32/Android.App.Notification/) método para se registrar com Android como um serviço de primeiro plano.   

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

Se o serviço é interrompido com uma chamada para `StopSelf` ou `StopService`, em seguida, a notificação da barra de status será removida da mesma forma.


## <a name="related-links"></a>Links relacionados

- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service/)
- [Android.App.Service.StartForegrond](https://developer.xamarin.com/api/member/Android.App.Service.StartForeground/p/System.Int32/Android.App.Notification/)
- [Notificações de locais](~/android/app-fundamentals/notifications/local-notifications.md)
- [ForegroundServiceDemo (sample)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/ForegroundServiceDemo/)
