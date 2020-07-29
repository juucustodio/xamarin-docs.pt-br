---
title: Criando serviços Android
description: Este guia aborda os serviços Xamarin. Android, que são componentes do Android que permitem que o trabalho seja feito sem uma interface do usuário ativa. Os serviços são comumente usados para tarefas que são executadas em segundo plano, como cálculos de tempo demorado, download de arquivos, reprodução de música e assim por diante. Ele explica os diferentes cenários para os quais os serviços são adequados e mostra como implementá-los para executar tarefas em segundo plano de longa execução, bem como para fornecer uma interface para chamadas de procedimento remoto.
ms.prod: xamarin
ms.assetid: BA371A59-6F7A-F62A-02FC-28253504ACC9
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/19/2018
ms.openlocfilehash: a28376535128b247ed807a33d46167a0eb497a90
ms.sourcegitcommit: aac10ff2da8065913a93008dfb0a7490bdad48c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87172809"
---
# <a name="creating-android-services"></a>Criando serviços Android

_Este guia aborda os serviços Xamarin. Android, que são componentes do Android que permitem que o trabalho seja feito sem uma interface do usuário ativa. Os serviços são comumente usados para tarefas que são executadas em segundo plano, como cálculos de tempo demorado, download de arquivos, reprodução de música e assim por diante. Ele explica os diferentes cenários para os quais os serviços são adequados e mostra como implementá-los para executar tarefas em segundo plano de longa execução, bem como para fornecer uma interface para chamadas de procedimento remoto._

## <a name="android-services-overview"></a>Visão geral dos serviços Android

Os aplicativos móveis não são como aplicativos da área de trabalho. As áreas de trabalho têm grandes quantidades de recursos como espaço real da tela, memória, espaço de armazenamento e uma fonte de alimentação conectada, os dispositivos móveis não. Essas restrições forçam os aplicativos móveis a se comportarem de forma diferente. Por exemplo, a tela pequena em um dispositivo móvel normalmente significa que apenas um aplicativo (ou seja, atividade) está visível por vez. Outras atividades são movidas para o plano de fundo e enviadas para um estado suspenso onde não podem executar nenhum trabalho. No entanto, só porque um aplicativo Android está em segundo plano não significa que é impossível para o aplicativo continuar funcionando. 

Os aplicativos Android são compostos de pelo menos um dos quatro principais componentes a seguir: _atividades_, _receptores de transmissão_, _provedores de conteúdo_e _Serviços_. As atividades são a base de muitos aplicativos excelentes do Android, pois fornecem a interface do usuário que permite que um usuários interaja com o aplicativo. No entanto, quando se trata de executar trabalho simultâneo ou em segundo plano, as atividades nem sempre são a melhor opção.

O mecanismo principal de trabalho em segundo plano no Android é o _serviço_. Um serviço Android é um componente projetado para fazer algum trabalho sem uma interface do usuário. Um serviço pode baixar um arquivo, reproduzir música ou aplicar um filtro a uma imagem. Os serviços também podem ser usados para_IPC_(comunicação entre processos) entre aplicativos Android. Por exemplo, um aplicativo Android pode usar o serviço de player de música que é de outro aplicativo ou um aplicativo pode expor dados (como as informações de contato de uma pessoa) para outros aplicativos por meio de um serviço. 

Os serviços e sua capacidade de executar trabalho em segundo plano são cruciais para fornecer uma interface de usuário suave e fluida. Todos os aplicativos Android têm um _thread principal_ (também conhecido como um _thread de interface do usuário_) no qual as atividades são executadas. Para manter o dispositivo responsivo, o Android deve ser capaz de atualizar a interface do usuário na taxa de 60 quadros por segundo. Se um aplicativo Android executar muito trabalho no thread principal, o Android descartará os quadros, o que, por sua vez, faz com que a interface do usuário pareça Jerky (às vezes chamado de _Janky_). Isso significa que qualquer trabalho realizado no thread da interface do usuário deve ser concluído no período de tempo entre dois quadros, aproximadamente 16 milissegundos (1 segundo a cada 60 quadros). 

Para resolver essa preocupação, um desenvolvedor pode usar threads em uma atividade para executar algum trabalho que bloqueie a interface do usuário. No entanto, isso pode causar problemas. É muito possível que o Android destrua e recrie as várias instâncias da atividade. No entanto, o Android não destruirá automaticamente os threads, o que pode resultar em vazamentos de memória. Um exemplo primo disso é quando o [dispositivo é girado](~/android/app-fundamentals/handling-rotation.md) o &ndash; Android tentará destruir a instância da atividade e, em seguida, recriar uma nova:

![Quando o dispositivo gira, a instância 1 é destruída e a instância 2 é criada](images/image-01.png)

Esse é um potencial vazamento de memória que &ndash; o thread criado pela primeira instância da atividade ainda estará em execução. Se o thread tiver uma referência à primeira instância da atividade, isso impedirá que o Android colete o objeto de coleta de lixo. No entanto, a segunda instância da atividade ainda é criada (o que, por sua vez, pode criar um novo thread). A rotação do dispositivo várias vezes em uma rápida sucessão pode esgotar toda a RAM e forçar o Android a encerrar todo o aplicativo para recuperar memória.

Como regra geral, se o trabalho a ser executado deve sobreviver alémr uma atividade, um serviço deve ser criado para executar esse trabalho. No entanto, se o trabalho só for aplicável no contexto de uma atividade, a criação de um thread para executar o trabalho poderá ser mais apropriada. Por exemplo, criar uma miniatura para uma foto que acabou de ser adicionada a um aplicativo da Galeria de fotos provavelmente ocorreria em um serviço. No entanto, um thread pode ser mais apropriado para reproduzir algumas músicas que só devem ser ouvidos quando uma atividade estiver em primeiro plano.

O trabalho em segundo plano pode ser dividido em duas classificações amplas:

1. Tarefa de longa **execução** &ndash; Isso funciona em andamento até que seja interrompido explicitamente. Um exemplo de uma _tarefa de execução demorada_ é um aplicativo que transmite música ou que deve monitorar dados coletados de um sensor. Essas tarefas devem ser executadas, mesmo que o aplicativo não tenha nenhuma interface do usuário visível.
2. **Tarefas** &ndash; periódicas (às vezes chamado de _trabalho_) Uma tarefa periódica é aquela de duração relativamente curta (vários segundos) e é executada em uma agenda (ou seja, uma vez por dia por uma semana ou talvez apenas uma vez nos próximos 60 segundos). Um exemplo disso é baixar um arquivo da Internet ou gerar uma miniatura para uma imagem.

Há quatro tipos diferentes de serviços do Android:

* **Serviço associado** &ndash; Um _serviço associado_ é um serviço que tem algum outro componente (normalmente uma atividade) associado a ele. Um serviço associado fornece uma interface que permite que o componente ligado e o serviço interajam entre si. Quando não houver mais clientes ligados ao serviço, o Android encerrará o serviço. 

* **`IntentService`**&ndash;Uma _`IntentService`_ é uma subclasse especializada da `Service` classe que simplifica a criação e o uso de serviços. Um `IntentService` é destinado a lidar com chamadas autônomas individuais. Ao contrário de um serviço, que pode lidar simultaneamente com várias chamadas, um `IntentService` é mais parecido com um trabalho do _processador da fila de trabalho_ que &ndash; é colocado em fila e um `IntentService` processa cada trabalho, um de cada vez, em um único thread de trabalho. Normalmente, um `IntentService` não está associado a uma atividade ou a um fragmento. 

* **Serviço iniciado** &ndash; Um _serviço iniciado_ é um serviço que foi iniciado por algum outro componente do Android (como uma atividade) e é executado continuamente em segundo plano até que algo indique explicitamente o serviço para parar. Ao contrário de um serviço associado, um serviço iniciado não tem nenhum cliente associado diretamente a ele. Por esse motivo, é importante criar serviços iniciados para que eles possam ser reiniciados normalmente conforme necessário.

* **Serviço híbrido** &ndash; Um _serviço híbrido_ é um serviço que tem as características de um _serviço iniciado_ e um _serviço associado_. Um serviço híbrido pode ser iniciado pelo quando um componente é associado a ele ou pode ser iniciado por algum evento. Um componente de cliente pode ou não estar associado ao serviço híbrido. Um serviço híbrido continuará em execução até que seja explicitamente solicitado a parar ou até que não haja mais clientes associados a ele.

O tipo de serviço a ser usado depende muito dos requisitos do aplicativo. Como regra prática, um `IntentService` ou um serviço associado são suficientes para a maioria das tarefas que um aplicativo Android deve executar, portanto, a preferência deve ser dada a um desses dois tipos de serviços. Uma `IntentService` é uma boa opção para tarefas "One-shot", como baixar um arquivo, enquanto um serviço associado seria adequado quando interações frequentes com uma atividade/fragmento forem necessárias. 

Embora a maioria dos serviços seja executada em segundo plano, há uma subcategoria especial conhecida como _serviço de primeiro plano_. Esse é um serviço que recebe uma prioridade mais alta (em comparação com um serviço normal) para executar algum trabalho para o usuário (como tocar música). 

Também é possível executar um serviço em seu próprio processo no mesmo dispositivo, isso às vezes é chamado de _serviço remoto_ ou como um _serviço fora do processo_. Isso exige mais esforço para criar, mas pode ser útil para quando um aplicativo precisa compartilhar funcionalidades com outros aplicativos e pode, em alguns casos, melhorar a experiência do usuário de um aplicativo. 

### <a name="background-execution-limits-in-android-80"></a>Limites de execução em segundo plano no Android 8,0

A partir do Android 8,0 (API nível 26), um aplicativo Android não tem mais a capacidade de ser executado livremente em segundo plano. Quando em primeiro plano, um aplicativo pode iniciar e executar serviços sem restrição. Quando um aplicativo passa para o segundo plano, o Android concederá ao aplicativo um determinado período de tempo para iniciar e usar os serviços. Depois que esse tempo tiver decorrido, o aplicativo não poderá mais iniciar nenhum serviço e todos os serviços que foram iniciados serão encerrados. Neste ponto, não é possível que o aplicativo execute qualquer trabalho. O Android considera um aplicativo em primeiro plano se uma das seguintes condições forem atendidas:

* Há uma atividade visível (iniciada ou pausada).
* O aplicativo iniciou um serviço em primeiro plano.
* Outro aplicativo está em primeiro plano e está usando componentes de um aplicativo que seria, de outra forma, em segundo plano. Um exemplo disso é se o aplicativo A, que está em primeiro plano, está associado a um serviço fornecido pelo aplicativo B. o aplicativo B também seria considerado em primeiro plano e não encerrado pelo Android para estar em segundo plano.

Há algumas situações em que, embora um aplicativo esteja em segundo plano, o Android ativará o aplicativo e relaxará essas restrições por alguns minutos, permitindo que o aplicativo execute algum trabalho:

* Uma mensagem de nuvem firebase de alta prioridade é recebida pelo aplicativo.
* O aplicativo recebe uma difusão. 
* O aplicativo recebe e executa um `PendingIntent` em resposta a uma notificação.

Os aplicativos Xamarin. Android existentes podem precisar alterar a forma como executam trabalho em segundo plano para evitar problemas que possam surgir no Android 8,0. Aqui estão algumas alternativas práticas para um serviço Android:

* **Agendar trabalho para ser executado em segundo plano usando o Agendador de trabalhos do Android ou o [Dispatcher](~/android/platform/firebase-job-dispatcher.md) ** &ndash; do trabalho firebase Essas duas bibliotecas fornecem uma estrutura para que os aplicativos segregem o trabalho em segundo plano nos _trabalhos_, uma unidade discreta de trabalho. Os aplicativos podem agendar o trabalho com o sistema operacional juntamente com alguns critérios sobre quando o trabalho pode ser executado.
* **Iniciar o serviço em primeiro plano** &ndash; um serviço em primeiro plano é útil para quando o aplicativo deve executar alguma tarefa em segundo plano e o usuário pode precisar interagir periodicamente com essa tarefa. O serviço de primeiro plano exibirá uma notificação persistente para que o usuário esteja ciente de que o aplicativo está executando uma tarefa em segundo plano e também fornece uma maneira de monitorar ou interagir com a tarefa. Um exemplo disso seria um aplicativo de podcasting que está reproduzindo um podcast para o usuário ou talvez baixando um episódio de podcast para que possa ser aproveitado posteriormente. 
* **Usar uma mensagem de nuvem firebase de alta prioridade (FCM)** &ndash; Quando o Android recebe um FCM de alta prioridade para um aplicativo, ele permitirá que esse aplicativo execute serviços em segundo plano por um curto período de tempo. Essa seria uma boa alternativa para ter um serviço em segundo plano que sonda um aplicativo em segundo plano. 
* **Adiar trabalho para quando o aplicativo entrar no primeiro plano** &ndash; Se nenhuma das soluções anteriores for viável, os aplicativos deverão desenvolver sua própria maneira de pausar e retomar o trabalho quando o aplicativo chegar ao primeiro plano.

## <a name="related-links"></a>Links Relacionados

* [Limites de execução em segundo plano do Android Oreo](https://www.youtube.com/watch?v=Pumf_4yjTMc)
