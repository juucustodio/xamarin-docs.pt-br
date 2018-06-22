---
title: Conceitos básicos de aplicativo xamarin
description: Conceitos fundamentais do aplicativo
ms.prod: xamarin
ms.assetid: 935B8BFE-23B7-4239-5C87-F4A503B889CB
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: caa51fa0a70da1b799d56c706e6de974f61a14d9
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2018
ms.locfileid: "32436424"
---
# <a name="xamarinandroid-application-fundamentals"></a>Conceitos básicos de aplicativo xamarin

Esta seção fornece um guia sobre algumas das tarefas de coisas ou conceitos que os desenvolvedores precisam estar ciente ao desenvolver aplicativos do Android mais comuns.

## <a name="accessibilityandroidapp-fundamentalsaccessibilitymd"></a>[Acessibilidade](~/android/app-fundamentals/accessibility.md)

Esta página descreve como usar as APIs de acessibilidade Android para compilar aplicativos de acordo com o [lista de verificação de acessibilidade](~/cross-platform/app-fundamentals/accessibility.md).

##  <a name="understanding-android-api-levelsandroidapp-fundamentalsandroid-api-levelsmd"></a>[Noções básicas sobre níveis de API do Android](~/android/app-fundamentals/android-api-levels.md)

Este guia descreve como o Android usa níveis de API para gerenciar a compatibilidade do aplicativo em diferentes versões do Android e explica como definir as configurações de projeto xamarin para implantar esses níveis de API em seu aplicativo. Além disso, este guia explica como escrever código de tempo de execução que lida com diferentes níveis de API e fornece uma lista de referência de todos os níveis de API do Android, os números de versão (como 8.0 Android), Android nomes de código (como Oreos) e códigos de versão de compilação.



##  <a name="resources-in-androidandroidapp-fundamentalsresources-in-androidindexmd"></a>[Recursos no Android](~/android/app-fundamentals/resources-in-android/index.md)

Este artigo apresenta o conceito de recursos Android em documentos e xamarin como usá-los. Ele aborda como usar recursos em seu aplicativo do Android para oferecer suporte a localização do aplicativo e vários dispositivos, incluindo densidades e diferentes tamanhos de tela.




##  <a name="activity-lifecycleandroidapp-fundamentalsactivity-lifecycleindexmd"></a>[Ciclo de vida da atividade](~/android/app-fundamentals/activity-lifecycle/index.md)

Atividades são um componente fundamental dos aplicativos do Android e eles podem existir em vários estados diferentes. O ciclo de vida da atividade começa com instanciação e termina com a destruição e inclui muitos estados entre. Quando uma atividade muda de estado, o método de evento de ciclo de vida apropriado é chamado, notificando a atividade de alteração de estado iminente e permitindo que ele executar o código para adaptar-se a alteração. Este artigo examina o ciclo de vida de atividades e explica a responsabilidade se uma atividade tem durante cada uma dessas alterações de estado para ser parte de um aplicativo com bom comportamento e confiável.

##  <a name="localizationandroidapp-fundamentalslocalizationmd"></a>[Localização](~/android/app-fundamentals/localization.md)

Este artigo explica como localizar um xamarin em outros idiomas, traduzindo cadeias de caracteres e fornecer imagens alternativas.

## <a name="servicesandroidapp-fundamentalsservicesindexmd"></a>[Serviços](~/android/app-fundamentals/services/index.md)

Este artigo aborda os serviços Android, que são componentes do Android que permitem que o trabalho a ser feito em segundo plano. Ele explica os diferentes cenários em que os serviços são adequados para e mostra como implementá-los para executar tarefas em segundo plano de execução longa, bem como para fornecer uma interface para chamadas de procedimento remoto.

## <a name="broadcast-receiversandroidapp-fundamentalsbroadcast-receiversmd"></a>[Receptores de difusão](~/android/app-fundamentals/broadcast-receivers.md)

Este guia aborda como criar e usar receptores de difusão, um componente do Android que responde às transmissões de todo o sistema, em xamarin.



##  <a name="permissionsandroidapp-fundamentalspermissionsmd"></a>[Permissões](~/android/app-fundamentals/permissions.md)

Você pode usar o suporte de ferramentas incorporado ao Visual Studio para Mac ou o Visual Studio para criar e adicionar permissões para o manifesto do Android. Este documento descreve como adicionar permissões no Visual Studio e o Xamarin Studio.



##  <a name="graphics-and-animationandroidapp-fundamentalsgraphics-and-animationmd"></a>[Gráficos e animação](~/android/app-fundamentals/graphics-and-animation.md)

Android fornece uma estrutura muito diferentes e avançada para dar suporte a animações e gráficos 2D. Este documento apresenta estas estruturas e discute como criar animações e elementos gráficos personalizados e usá-los em um aplicativo xamarin.


##  <a name="cpu-architecturesandroidapp-fundamentalscpu-architecturesmd"></a>[Arquiteturas de CPU](~/android/app-fundamentals/cpu-architectures.md)

Xamarin dá suporte a várias arquiteturas de CPU, incluindo dispositivos de 32 bits e 64 bits. Este artigo explica como direcionar um aplicativo para um ou mais arquiteturas de CPU com suporte do Android.




##  <a name="handling-rotationandroidapp-fundamentalshandling-rotationmd"></a>[Tratamento de rotação](~/android/app-fundamentals/handling-rotation.md)

Este artigo descreve como manipular as alterações de orientação do dispositivo em xamarin. Ele aborda como trabalhar com o sistema de recursos do Android para carregar automaticamente os recursos para uma orientação de dispositivo específico, bem como manipular programaticamente a orientação é alterado. Em seguida, ele descreve técnicas para manter o estado quando um dispositivo for girado.



##  <a name="android-audioandroidapp-fundamentalsandroid-audiomd"></a>[Áudio Android](~/android/app-fundamentals/android-audio.md)

O sistema operacional Android fornece amplo suporte para multimídia, abrangendo o áudio e vídeo. Este guia se concentra em áudio no Android e abrange a reprodução e gravação de áudio usando o player de áudio interno e classes de gravador, bem como a API de nível baixo de áudio. Ele também aborda trabalhar com eventos de áudio difusão por outros aplicativos, para que os desenvolvedores podem criar aplicativos com bom comportamento.




##  <a name="notificationsandroidapp-fundamentalsnotificationsindexmd"></a>[Notificações](~/android/app-fundamentals/notifications/index.md)

Esta seção explica como implementar notificações de locais e remotas em xamarin. Descreve os vários elementos de interface do usuário de uma notificação de Android e discute a API do envolvidos com a criação e exibindo uma notificação. Para notificações remotas, são explicados Google Cloud Messaging tanto Firebase Cloud Messaging. Exemplos de código e instruções passo a passo passo a passo são incluídos.



##  <a name="touchandroidapp-fundamentalstouchindexmd"></a>[Toque](~/android/app-fundamentals/touch/index.md)

Esta seção explica que os conceitos e detalhes de implementação de gestos no Android. APIs de toque são introduzidas e explicados seguidos por uma exploração de reconhecedores de gestos.



##  <a name="httpclient-stack-and-ssltlsandroidapp-fundamentalshttp-stackmd"></a>[Pilha de HttpClient e SSL/TLS](~/android/app-fundamentals/http-stack.md)

Esta seção explica os seletores de pilha HttpClient e implementação de TLS/SSL para o Android. Essas configurações determinam a implementação de HttpClient e SSL/TLS que será usada por seus aplicativos xamarin.


##  <a name="writing-responsive-applicationswriting-responsive-appsmd"></a>[Gravando aplicativos responsivos](writing-responsive-apps.md)

Este artigo descreve como usar o threading manter um aplicativo xamarin responsiva movendo as tarefas de longa execução em um thread em segundo plano.