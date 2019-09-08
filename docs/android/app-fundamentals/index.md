---
title: Conceitos básicos do aplicativo Xamarin. Android
description: Conceitos principais de aplicativos
ms.prod: xamarin
ms.assetid: 935B8BFE-23B7-4239-5C87-F4A503B889CB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: af7ba83b9026a91028f4ffa9894d564d5ff13eb8
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70755297"
---
# <a name="xamarinandroid-application-fundamentals"></a>Conceitos básicos do aplicativo Xamarin. Android

Esta seção fornece um guia sobre algumas das tarefas de coisas mais comuns ou conceitos que os desenvolvedores precisam conhecer ao desenvolver aplicativos Android.

## <a name="accessibilityandroidapp-fundamentalsaccessibilitymd"></a>[Acessibilidade](~/android/app-fundamentals/accessibility.md)

Esta página descreve como usar as APIs de acessibilidade do Android para compilar aplicativos de acordo com a [lista de verificação de acessibilidade](~/cross-platform/app-fundamentals/accessibility.md).

## <a name="understanding-android-api-levelsandroidapp-fundamentalsandroid-api-levelsmd"></a>[Entendendo os níveis da API do Android](~/android/app-fundamentals/android-api-levels.md)

Este guia descreve como o Android usa os níveis de API para gerenciar a compatibilidade de aplicativos em diferentes versões do Android e explica como definir as configurações do projeto Xamarin. Android para implantar esses níveis de API em seu aplicativo. Além disso, este guia explica como escrever código de tempo de execução que lida com diferentes níveis de API e fornece uma lista de referências de todos os níveis de API do Android, números de versão (como Android 8,0), nomes de código do Android (como Oreo) e códigos de versão de compilação.

## <a name="resources-in-androidandroidapp-fundamentalsresources-in-androidindexmd"></a>[Recursos no Android](~/android/app-fundamentals/resources-in-android/index.md)

Este artigo apresenta o conceito de recursos do Android no Xamarin. Android e documentos sobre como usá-los. Ele aborda como usar recursos em seu aplicativo Android para dar suporte à localização de aplicativos e vários dispositivos, incluindo diferentes tamanhos de tela e densidades.

## <a name="activity-lifecycleandroidapp-fundamentalsactivity-lifecycleindexmd"></a>[Ciclo de vida da atividade](~/android/app-fundamentals/activity-lifecycle/index.md)

As atividades são um bloco de construção fundamental de aplicativos Android e podem existir em vários Estados diferentes. O ciclo de vida da atividade começa com a instanciação e termina com a destruição e inclui muitos Estados no between. Quando uma atividade muda de estado, o método de evento do ciclo de vida apropriado é chamado, notificando a atividade da alteração de estado iminente e permitindo que ele execute o código para se adaptar a essa alteração. Este artigo examina o ciclo de vida das atividades e explica a responsabilidade de uma atividade durante cada uma dessas alterações de estado fazer parte de um aplicativo confiável e bem comparado.

## <a name="localizationandroidapp-fundamentalslocalizationmd"></a>[Localização](~/android/app-fundamentals/localization.md)

Este artigo explica como localizar um Xamarin. Android em outras linguagens traduzindo cadeias de caracteres e fornecendo imagens alternativas.

## <a name="servicesandroidapp-fundamentalsservicesindexmd"></a>[Serviços](~/android/app-fundamentals/services/index.md)

Este artigo aborda os serviços Android, que são componentes do Android que permitem que o trabalho seja feito em segundo plano. Ele explica os diferentes cenários para os quais os serviços são adequados e mostra como implementá-los para executar tarefas em segundo plano de longa execução, bem como fornecer uma interface para chamadas de procedimento remoto.

## <a name="broadcast-receiversandroidapp-fundamentalsbroadcast-receiversmd"></a>[Receptores de difusão](~/android/app-fundamentals/broadcast-receivers.md)

Este guia aborda como criar e usar receptores de difusão, um componente do Android que responde a difusões em todo o sistema, no Xamarin. Android.

## <a name="permissionsandroidapp-fundamentalspermissionsmd"></a>[Permissões](~/android/app-fundamentals/permissions.md)

Você pode usar o suporte de ferramentas interno do Visual Studio para Mac ou do Visual Studio para criar e adicionar permissões ao manifesto do Android. Este documento descreve como adicionar permissões no Visual Studio e Xamarin Studio.

## <a name="graphics-and-animationandroidapp-fundamentalsgraphics-and-animationmd"></a>[Gráficos e animação](~/android/app-fundamentals/graphics-and-animation.md)

O Android fornece uma estrutura muito rica e diversificada para dar suporte a gráficos 2D e animações. Este documento apresenta essas estruturas e discute como criar gráficos e animações personalizadas e usá-las em um aplicativo Xamarin. Android.

## <a name="cpu-architecturesandroidapp-fundamentalscpu-architecturesmd"></a>[Arquiteturas de CPU](~/android/app-fundamentals/cpu-architectures.md)

O Xamarin. Android dá suporte a várias arquiteturas de CPU, incluindo dispositivos de 32 bits e de 64 bits. Este artigo explica como direcionar um aplicativo para uma ou mais arquiteturas de CPU com suporte do Android.

## <a name="handling-rotationandroidapp-fundamentalshandling-rotationmd"></a>[Tratamento de rotação](~/android/app-fundamentals/handling-rotation.md)

Este artigo descreve como lidar com alterações de orientação do dispositivo no Xamarin. Android. Ele aborda como trabalhar com o sistema de recursos do Android para carregar automaticamente os recursos para uma determinada orientação do dispositivo, bem como para manipular alterações de orientação de forma programática. Em seguida, ele descreve as técnicas para manter o estado quando um dispositivo é girado.

## <a name="android-audioandroidapp-fundamentalsandroid-audiomd"></a>[Áudio do Android](~/android/app-fundamentals/android-audio.md)

O sistema operacional Android fornece amplo suporte para multimídia, abrangendo áudio e vídeo. Este guia enfoca o áudio no Android e aborda a reprodução e a gravação de áudio usando as classes internas de player de áudio e gravador, bem como a API de áudio de nível baixo. Ele também aborda o trabalho com a difusão de eventos de áudio por outros aplicativos, para que os desenvolvedores possam criar aplicativos bem comportados.

## <a name="notificationsandroidapp-fundamentalsnotificationsindexmd"></a>[Notificações](~/android/app-fundamentals/notifications/index.md)

Esta seção explica como implementar notificações locais e remotas no Xamarin. Android. Ele descreve os vários elementos da interface do usuário de uma notificação do Android e discute as APIs envolvidas na criação e exibição de uma notificação. Para notificações remotas, as mensagens de nuvem Google Cloud Messaging e firebase são explicadas. As instruções passo a passo e exemplos de código estão incluídos.

## <a name="touchandroidapp-fundamentalstouchindexmd"></a>[Toque](~/android/app-fundamentals/touch/index.md)

Esta seção explica os conceitos e os detalhes da implementação de gestos de toque no Android. As APIs de toque são introduzidas e explicadas seguidas por uma exploração de reconhecedores de gesto.

## <a name="httpclient-stack-and-ssltlsandroidapp-fundamentalshttp-stackmd"></a>[Pilha de HttpClient e SSL/TLS](~/android/app-fundamentals/http-stack.md)

Esta seção explica os seletores de implementação do HttpClient Stack e SSL/TLS para Android. Essas configurações determinam a implementação de HttpClient e SSL/TLS que será usada por seus aplicativos Xamarin. Android.

## <a name="writing-responsive-applicationswriting-responsive-appsmd"></a>[Escrevendo aplicativos responsivos](writing-responsive-apps.md)

Este artigo discute como usar Threading para manter um aplicativo Xamarin. Android responsivo movendo tarefas de longa execução para um thread em segundo plano.
