---
title: Criando serviços do Android
description: Este guia aborda os serviços de xamarin. Android, que são componentes do Android que permitem que o trabalho a ser feito sem uma interface do usuário do Active Directory. Os serviços são muito usados para tarefas que são executadas em segundo plano, como cálculos demorados, download de arquivos, reproduzir música e assim por diante. Ele explica os diferentes cenários em que os serviços são adequados para e mostra como implementá-los tanto para executar tarefas em segundo plano de execução longa, bem como para fornecer uma interface para chamadas de procedimento remoto.
ms.prod: xamarin
ms.assetid: BA371A59-6F7A-F62A-02FC-28253504ACC9
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/19/2018
ms.openlocfilehash: 4ae86ca5fa47169bb5d78eb9d1116e419c23ed6d
ms.sourcegitcommit: 91a4fcb715506e18e8070bc89bf2cb14d079ad32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/15/2019
ms.locfileid: "59574800"
---
# <a name="creating-android-services"></a>Criando serviços do Android

_Este guia aborda os serviços de xamarin. Android, que são componentes do Android que permitem que o trabalho a ser feito sem uma interface do usuário do Active Directory. Os serviços são muito usados para tarefas que são executadas em segundo plano, como cálculos demorados, download de arquivos, reproduzir música e assim por diante. Ele explica os diferentes cenários em que os serviços são adequados para e mostra como implementá-los tanto para executar tarefas em segundo plano de execução longa, bem como para fornecer uma interface para chamadas de procedimento remoto._

## <a name="android-services-overview"></a>Visão geral de serviços do Android

Aplicativos móveis não são como os aplicativos da área de trabalho. Áreas de trabalho têm grandes quantidades de recursos, como o espaço na tela, memória, espaço de armazenamento e uma fonte de alimentação conectados, os dispositivos móveis não têm. Essas restrições forçar os aplicativos móveis para se comportar de forma diferente. Por exemplo, a tela pequena em um dispositivo móvel normalmente significa que apenas um aplicativo (ou seja, a atividade) é visível por vez. Outras atividades são movidas para o plano de fundo e enviados por push para um estado suspenso em que eles não podem executar qualquer trabalho. No entanto, só porque um aplicativo Android está em segundo plano não significa que é impossível para o aplicativo continue a funcionar. 

Aplicativos Android são compostos de pelo menos um dos quatro componentes principais: _Atividades_, _receptores de difusão_, _provedores de conteúdo_, e _serviços_. As atividades são a base de muitos ótimos aplicativos Android porque eles fornecem a interface do usuário que permite que um usuário interage com o aplicativo. No entanto, quando se trata de execução simultânea ou trabalho em segundo plano, as atividades nem sempre são a melhor opção.
 
O mecanismo principal para o trabalho em segundo plano no Android é o _serviço_. Um serviço do Android é um componente que é projetado para fazer algum trabalho sem uma interface do usuário. Um serviço pode baixar um arquivo, reproduzir música ou aplicar um filtro a uma imagem. Serviços também podem ser usados para comunicação entre processos (_IPC_) entre os aplicativos Android. Por exemplo um aplicativo Android pode usar o serviço de player de música é de outro aplicativo ou um aplicativo pode expor dados (como informações de contato da pessoa) para outros aplicativos através de um serviço. 

Serviços e sua capacidade de executar o trabalho em segundo plano, são cruciais para fornecer uma interface de usuário fluida e suave. Todos os aplicativos Android têm uma _thread principal_ (também conhecido como um _thread de interface do usuário_) em que as atividades são executadas. Para manter o dispositivo responsivo, Android deve ser capaz de atualizar a interface do usuário em uma taxa de 60 quadros por segundo. Se um aplicativo Android executa muito trabalho no thread principal, o Android descartará quadros, que por sua vez faz com que a interface do usuário apareça brusco (também chamados de _janky_). Isso significa que qualquer trabalho realizado no thread da interface do usuário deve ser concluídas no período de tempo entre dois quadros, aproximadamente 16 milissegundos (1 segundo a cada 60 quadros). 

Para solucionar esta questão, um desenvolvedor pode usar threads em uma atividade para executar algum trabalho que bloquearia a interface do usuário. No entanto, isso pode causar problemas. É bem possível que a Android destruir e recriar as várias instâncias da atividade. No entanto, o Android não destruirá os threads, o que pode resultar em vazamentos de memória automaticamente. Um exemplo perfeito disso é quando o [dispositivo for girado](~/android/app-fundamentals/handling-rotation.md) &ndash; Android irá tentar destruir a instância da atividade e, em seguida, recrie um novo:

![Quando o dispositivo gira, instância 1 é destruída e 2 da instância é criada](images/image-01.png)

Isso é uma potencial perda de memória &ndash; o thread criado pela primeira instância da atividade ainda será executado. Se o thread tem uma referência para a primeira instância da atividade, isso impedirá Android coletando o objeto de lixo. No entanto, a segunda instância da atividade ainda será criada (que por sua vez, pode criar um novo thread). Girar o dispositivo várias vezes em sucessão rápida pode esgotar a memória de RAM e forçar o Android para encerrar o aplicativo inteiro para recuperar a memória.

Como regra geral, se o trabalho seja realizado deve sobreviver além de uma atividade, um serviço deve ser criado para realizar o trabalho. No entanto, se o trabalho só é aplicável no contexto de uma atividade, em seguida, criar um thread para executar o trabalho pode ser mais apropriado. Por exemplo, criar uma miniatura de uma foto que acabou de ser adicionada a um aplicativo de galeria de fotos provavelmente deve ocorrer em um serviço. No entanto, um thread pode ser mais apropriado para tocar música que só deve ser ouvida enquanto uma atividade está em primeiro plano.

Trabalho em segundo plano pode ser dividido em duas classificações amplas:

1. **Tarefa de execução longa** &ndash; isso é um trabalho que está em andamento até ser parado explicitamente. Um exemplo de uma _tarefas de longa execução_ é um aplicativo que transmite música ou que deve monitorar os dados coletados de um sensor. Essas tarefas devem ser executadas, mesmo que o aplicativo não tenha nenhuma interface do usuário visível.
2. **Tarefas periódicas** &ndash; (também conhecido como um _trabalho_) uma tarefa periódica é aquele que é do relativamente curta duração (alguns segundos) e é executado em um agendamento (ou seja, uma vez por dia por uma semana ou talvez apenas uma vez no próximos 60 segundos). Um exemplo disso é baixar um arquivo da internet ou gerar uma miniatura de uma imagem.

Há quatro tipos diferentes de serviços do Android:

* **Vinculado a serviço** &ndash; um _associado serviço_ é um serviço que tem algum outro componente (normalmente uma atividade) associado a ele. Um serviço vinculado fornece uma interface que permite que o componente associado e o serviço para interagir entre si. Quando não houver nenhum outro cliente associado ao serviço, Android será desligado o serviço. 

* **`IntentService`** &ndash; Uma _`IntentService`_ é uma subclasse especializada do `Service` classe que simplifica a criação de serviços e uso. Um `IntentService` destina-se para lidar com chamadas individuais de autônomas. Ao contrário de um serviço, que simultaneamente pode manipular várias chamadas, um `IntentService` é mais parecido com um _processador de fila de trabalho_ &ndash; trabalho na fila e um `IntentService` processa cada trabalho de um por vez em um único thread de trabalho. Normalmente, um`IntentService` não está associado a uma atividade ou um fragmento. 

* **Serviço iniciado** &ndash; um _serviço iniciado_ é um serviço que foi iniciado por algum outro componente Android (por exemplo, uma atividade) e é executado continuamente em segundo plano, até algo explicitamente informa o serviço parar. Ao contrário de um serviço vinculado, um serviço iniciado não tem todos os clientes diretamente associados a ele. Por esse motivo, é importante projetar serviços iniciados para que eles podem ser normalmente reiniciados conforme necessário.

* **Serviço híbrido** &ndash; um _serviço híbrido_ é um serviço que tem as características de um _serviço iniciado_ e um _associado serviço_. Quando um componente é associado a ele ou ela pode ser iniciada por algum evento, um serviço híbrido pode ser iniciado por. Um componente de cliente pode ou não pode ser associado ao serviço híbrido. Um serviço híbrido será manter em execução até que explicitamente é informado para parar, ou até que não haja nenhum outro cliente associado a ele.

Qual tipo de serviço a ser usado é muito dependente de requisitos do aplicativo. Como regra geral, um `IntentService` ou um serviço vinculado são suficientes para a maioria das tarefas que deve executar um aplicativo do Android, portanto, a preferência deve ser fornecida a um desses dois tipos de serviços. Um `IntentService` é uma boa opção para "monoestável" tarefas, como baixar um arquivo, enquanto um serviço vinculado seria adequado quando interações frequentes com uma atividade/fragmento é necessária. 

Embora a maioria dos serviços executados em segundo plano, há uma subcategoria especial conhecida como uma _serviço de primeiro plano_. Esse é um serviço que recebe uma prioridade mais alta (em comparação comparada um serviço normal) para realizar algum trabalho para o usuário (como reproduzir música). 

Também é possível executar um serviço em seu próprio processo no mesmo dispositivo, às vezes, isso é conhecido como um _serviço remoto_ ou como um _out-of-process serviço_. Isso requer mais esforço para criar, mas pode ser útil para quando o aplicativo precisa compartilhar a funcionalidade com outros aplicativos e pode, em alguns casos, melhorar a experiência do usuário de um aplicativo. 

### <a name="background-execution-limits-in-android-80"></a>Limites de execução do plano de fundo no Android 8.0

Partir do Android 8.0 (API nível 26), um aplicativo do Android não tem a capacidade de executar livremente em segundo plano. Quando estiver em primeiro plano, um aplicativo pode iniciar e executar os serviços sem restrição. Quando um aplicativo move para o plano de fundo, Android, será concedido o aplicativo uma determinada quantidade de tempo para iniciar e usar os serviços. Depois de decorrido esse tempo, o aplicativo não poderá mais iniciar todos os serviços e todos os serviços foram iniciados serão encerrados. No momento não é possível que o aplicativo executar qualquer trabalho. Android considera um aplicativo esteja em primeiro plano se uma das seguintes condições forem atendida:

* Há uma atividade visível (iniciado ou pausado).
* O aplicativo foi iniciado de um serviço de primeiro plano.
* Outro aplicativo está em primeiro plano e está usando componentes de um aplicativo que seriam em segundo plano. Um exemplo disso é se um aplicativo, que está em primeiro plano, é associado a um serviço fornecido pelo aplicativo B. o aplicativo B, em seguida, também seria considerado em primeiro plano e não são encerradas pelo Android para estarem em segundo plano.

Há algumas situações em que, mesmo que um aplicativo está em segundo plano, Android será ativar o aplicativo e relaxar essas restrições por alguns minutos, permitindo que o aplicativo executar algum trabalho:
* Uma mensagem de nuvem Firebase de alta prioridade é recebida pelo aplicativo.
* O aplicativo recebe uma transmissão. 
* O aplicativo recebe e executa um `PendingIntent` em resposta a uma notificação.

Os aplicativos xamarin. Android existentes talvez precise alterar a forma como realizam o trabalho em segundo plano para evitar quaisquer problemas que possam surgir no Android 8.0. Aqui estão algumas alternativas práticas para um serviço do Android:

* **Agendar trabalhos para execução em segundo plano usando o Agendador de trabalho Android ou o [Dispatcher de trabalho do Firebase](~/android/platform/firebase-job-dispatcher.md)**  &ndash; essas duas bibliotecas fornecem uma estrutura para aplicativos para separar o trabalho de plano de fundo na _trabalhos_, uma unidade separada do trabalho. Aplicativos, em seguida, podem agendar o trabalho com o sistema operacional, juntamente com alguns critérios sobre quando o trabalho pode ser executado.
* **Inicie o serviço em primeiro plano** &ndash; um serviço de primeiro plano é útil para quando o aplicativo deve executar algumas tarefas em segundo plano e o usuário pode precisar interagir periodicamente com essa tarefa. O serviço de primeiro plano exibirá uma notificação persistente para que o usuário esteja ciente de que o aplicativo está executando uma tarefa em segundo plano e também fornece uma maneira de monitorar ou interagir com a tarefa. Um exemplo disso seria um aplicativo de podcasts reproduzindo um podcast ao usuário ou talvez baixando um episódio do podcast, de modo que ele pode ser aproveitado mais tarde. 
* **Usar uma mensagem FCM (Firebase Cloud) de alta prioridade** &ndash; Android quando recebe uma prioridade alta FCM para um aplicativo, ele permitirá que o aplicativo executar serviços em segundo plano por um curto período de tempo. Isso seria uma boa alternativa para ter um serviço em segundo plano que monitora um aplicativo em segundo plano. 
* **Adiar o trabalho para quando o aplicativo trata o primeiro plano** &ndash; se nenhuma das soluções anteriores são viáveis, aplicativos devem desenvolver sua própria maneira de pausar e retomar o trabalho quando o aplicativo trata o primeiro plano.

## <a name="related-links"></a>Links relacionados

* [Limites de execução de plano de fundo do Android Oreo](https://www.youtube.com/watch?v=Pumf_4yjTMc)
