---
title: Conceitos básicos de aplicativo do xamarin. Android
description: Principais conceitos de aplicativo
ms.prod: xamarin
ms.assetid: 935B8BFE-23B7-4239-5C87-F4A503B889CB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 3ed616340dbb6dd64d85998d84e340ec381f6b54
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864085"
---
# <a name="xamarinandroid-application-fundamentals"></a>Conceitos básicos de aplicativo do xamarin. Android

Esta seção fornece um guia sobre alguns dos mais comuns as tarefas de coisas ou conceitos que os desenvolvedores precisam estar atento ao desenvolvimento de aplicativos Android.

## <a name="accessibilityandroidapp-fundamentalsaccessibilitymd"></a>[Acessibilidade](~/android/app-fundamentals/accessibility.md)

Esta página descreve como usar APIs de acessibilidade do Android para criar aplicativos de acordo com o [lista de verificação de acessibilidade](~/cross-platform/app-fundamentals/accessibility.md).

## <a name="understanding-android-api-levelsandroidapp-fundamentalsandroid-api-levelsmd"></a>[Noções básicas sobre níveis de API do Android](~/android/app-fundamentals/android-api-levels.md)

Este guia descreve como o Android usa níveis de API para gerenciar a compatibilidade de aplicativo entre diferentes versões do Android, e explica como definir as configurações de projeto do xamarin. Android para implantar esses níveis de API em seu aplicativo. Além disso, este guia explica como escrever código de tempo de execução que lida com diferentes níveis de API, e ele fornece uma lista de referência de todos os níveis de API do Android, números de versão (por exemplo, o Android 8.0), nomes de código Android (como Oreo) e códigos de versão de compilação.



## <a name="resources-in-androidandroidapp-fundamentalsresources-in-androidindexmd"></a>[Recursos no Android](~/android/app-fundamentals/resources-in-android/index.md)

Este artigo apresenta o conceito de recursos do Android no xamarin. Android e documentos como usá-los. Ele aborda como usar recursos em seu aplicativo Android para dar suporte à localização do aplicativo e vários dispositivos, incluindo densidades e diferentes tamanhos de tela.




## <a name="activity-lifecycleandroidapp-fundamentalsactivity-lifecycleindexmd"></a>[Ciclo de vida da atividade](~/android/app-fundamentals/activity-lifecycle/index.md)

As atividades são um componente fundamental dos aplicativos do Android e eles podem existir em um número de estados diferentes. O ciclo de vida de atividade começa com a instanciação e termina com a destruição e inclui muitos estados entre os dois. Quando uma atividade muda de estado, o método de evento de ciclo de vida apropriado é chamado, notificando sobre a atividade de alteração de estado do iminente e permitindo que ele executar o código para adaptar-se a essa alteração. Este artigo examina o ciclo de vida de atividades e explica a responsabilidade que uma atividade tem durante cada uma dessas alterações de estado para ser parte de um aplicativo bem comportado e confiável.

## <a name="localizationandroidapp-fundamentalslocalizationmd"></a>[Localização](~/android/app-fundamentals/localization.md)

Este artigo explica como localizar um xamarin. Android em outros idiomas, traduzindo cadeias de caracteres e fornecer imagens alternativas.

## <a name="servicesandroidapp-fundamentalsservicesindexmd"></a>[Serviços](~/android/app-fundamentals/services/index.md)

Este artigo aborda os serviços do Android, que são componentes do Android que permitem que o trabalho a ser feito em segundo plano. Ele explica os diferentes cenários em que os serviços são adequados para e mostra como implementá-los para executar tarefas em segundo plano de execução longa, bem como para fornecer uma interface para chamadas de procedimento remoto.

## <a name="broadcast-receiversandroidapp-fundamentalsbroadcast-receiversmd"></a>[Receptores de difusão](~/android/app-fundamentals/broadcast-receivers.md)

Este guia aborda como criar e usar receptores de difusão, um componente do Android que responde a difusões de todo o sistema, no xamarin. Android.



## <a name="permissionsandroidapp-fundamentalspermissionsmd"></a>[Permissões](~/android/app-fundamentals/permissions.md)

Você pode usar o suporte de ferramentas embutido no Visual Studio para Mac ou Visual Studio para criar e adicionar permissões para o manifesto do Android. Este documento descreve como adicionar permissões no Visual Studio e Xamarin Studio.



## <a name="graphics-and-animationandroidapp-fundamentalsgraphics-and-animationmd"></a>[Gráficos e animação](~/android/app-fundamentals/graphics-and-animation.md)

Android fornece uma estrutura muito avançada e diversificada para dar suporte a animações e gráficos 2D. Este documento apresenta essas estruturas e discute como criar animações e elementos gráficos personalizados e usá-los em um aplicativo xamarin. Android.


## <a name="cpu-architecturesandroidapp-fundamentalscpu-architecturesmd"></a>[Arquiteturas de CPU](~/android/app-fundamentals/cpu-architectures.md)

Xamarin. Android dá suporte a várias arquiteturas de CPU, incluindo dispositivos de 32 bits e 64 bits. Este artigo explica como direcionar um aplicativo para um ou mais arquiteturas de CPU com suporte do Android.




## <a name="handling-rotationandroidapp-fundamentalshandling-rotationmd"></a>[Tratamento de rotação](~/android/app-fundamentals/handling-rotation.md)

Este artigo descreve como manipular as alterações de orientação do dispositivo no xamarin. Android. Ele aborda como trabalhar com o sistema de recurso do Android para carregar automaticamente os recursos para uma orientação de dispositivo específico, bem como manipular programaticamente a orientação muda. Em seguida, ele descreve técnicas para manter o estado quando um dispositivo é girado.



## <a name="android-audioandroidapp-fundamentalsandroid-audiomd"></a>[Android Audio](~/android/app-fundamentals/android-audio.md)

O sistema operacional Android fornece amplo suporte para multimídia, que abrange o áudio e vídeo. Este guia se concentra em áudio no Android e aborda a reprodução e gravação de áudio usando o player de áudio internos e classes de gravador, bem como a API de áudio de nível baixo. Ele também aborda o trabalho com eventos de áudio de difusão por outros aplicativos, para que os desenvolvedores podem criar aplicativos bem comportados.




## <a name="notificationsandroidapp-fundamentalsnotificationsindexmd"></a>[Notificações](~/android/app-fundamentals/notifications/index.md)

Esta seção explica como implementar notificações locais e remotas no xamarin. Android. Ele descreve os vários elementos de interface do usuário de uma notificação de Android e discute a API do envolvido com a criação e exibindo uma notificação. Para obter notificações remotas, tanto o Google Cloud Messaging e Firebase Cloud Messaging são explicados. Exemplos de código e instruções passo a passo de passo a passo são incluídos.



## <a name="touchandroidapp-fundamentalstouchindexmd"></a>[Toque](~/android/app-fundamentals/touch/index.md)

Esta seção explica que os conceitos e detalhes de implementação de gestos de toque no Android. APIs de toque são introduzidas e explicou seguidas de uma exploração dos reconhecedores de gestos.



## <a name="httpclient-stack-and-ssltlsandroidapp-fundamentalshttp-stackmd"></a>[Pilha de HttpClient e SSL/TLS](~/android/app-fundamentals/http-stack.md)

Esta seção explica os seletores de pilha de HttpClient e implementação SSL/TLS para Android. Essas configurações determinam a implementação de HttpClient e SSL/TLS que será usada por seus aplicativos xamarin. Android.


## <a name="writing-responsive-applicationswriting-responsive-appsmd"></a>[Escrevendo aplicativos responsivos](writing-responsive-apps.md)

Este artigo discute como usar o threading para manter um aplicativo xamarin. Android responsivo movendo as tarefas de longa execução logon em um thread em segundo plano.
