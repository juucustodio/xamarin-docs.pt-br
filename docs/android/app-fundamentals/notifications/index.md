---
title: Notificações no xamarin. Android
ms.prod: xamarin
ms.assetid: 2E54F1D0-45F4-43A7-B3A3-4F483B7150CB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
---

# <a name="notifications-in-xamarinandroid"></a>Notificações no xamarin. Android


## <a name="overview"></a>Visão geral

Esta seção explica como implementar notificações no xamarin. Android. Ele descreve os vários elementos de interface do usuário de uma notificação de Android e discute a API do envolvido com a criação e exibindo uma notificação.


## <a name="sections"></a>Seções

### <a name="local-notifications-in-androidlocal-notificationsmd"></a>[Notificações locais no Android](local-notifications.md)

Esta seção explica como implementar notificações locais no xamarin. Android. Ele descreve os vários elementos de interface do usuário de uma notificação de Android e discutir a API do envolvido com a criação e exibindo uma notificação. 

### <a name="walkthrough---using-local-notifications-in-xamarinandroidlocal-notifications-walkthroughmd"></a>[Passo a passo: usar notificações locais no xamarin. Android](local-notifications-walkthrough.md)  
 
Este passo a passo aborda como usar notificações locais em um aplicativo xamarin. Android. Ele demonstra as Noções básicas de criação e publicação de uma notificação. Quando o usuário clica em uma notificação na gaveta de notificação ele inicia uma segunda atividade. 


## <a name="for-further-reading"></a>Para leitura adicional

[O firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) &ndash; Firebase Cloud Messaging (FCM) é um serviço que facilita o sistema de mensagens entre aplicativos móveis e aplicativos de servidor. O firebase Cloud Messaging pode ser usado para implementar notificações remotas (também chamadas de notificações por push) em aplicativos xamarin. Android.

[Notificações](https://developer.android.com/guide/topics/ui/notifiers/notifications.html) &ndash; tópico esse desenvolvedor Android é o guia definitivo para notificações do Android. Ele inclui um design de seção de considerações que ajuda você a projetar suas notificações, de modo que eles estão em conformidade com as diretrizes da interface do usuário do Android. Ele fornece informações complementares sobre navegação preserviing ao iniciar uma atividade, e explica como exibir o progresso em uma notificação e controle a reprodução de mídia na tela de bloqueio. 

[NotificationListenerService](https://developer.xamarin.com/api/type/Android.Service.Notification.NotificationListenerService/) &ndash; Android esse serviço torna possível para seu aplicativo escutar (e interagir com) todas as notificações são postados no dispositivo Android, não apenas as notificações de que seu aplicativo está registrado para receber. Observe que o usuário explicitamente deve conceder permissão ao seu aplicativo para que ele seja capaz de detectar notificações no dispositivo.





## <a name="related-links"></a>Links relacionados

- [Notificações locais (amostra)](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [Notificações remotas (amostra)](https://developer.xamarin.com/samples/monodroid/RemoteNotifications/)
