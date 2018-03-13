---
title: "Criar serviços do Android"
description: "Este guia aborda os serviços de xamarin, que são componentes do Android que permitem que o trabalho a ser feito sem uma interface de usuário ativa. Os serviços normalmente são usados para tarefas que são executadas em segundo plano, como cálculos demorados, download de arquivos, reproduzir músicas e assim por diante. Ele explica os diferentes cenários em que os serviços são adequados para e mostra como implementá-los, tanto para executar tarefas em segundo plano de execução longa, bem como para fornecer uma interface para chamadas de procedimento remoto."
ms.topic: article
ms.prod: xamarin
ms.assetid: BA371A59-6F7A-F62A-02FC-28253504ACC9
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 02/16/2018
ms.openlocfilehash: 5dc1fb0fb02014e123b3a161394155bde725f288
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="creating-android-services"></a>Criar serviços do Android

_Este guia aborda os serviços de xamarin, que são componentes do Android que permitem que o trabalho a ser feito sem uma interface de usuário ativa. Os serviços normalmente são usados para tarefas que são executadas em segundo plano, como cálculos demorados, download de arquivos, reproduzir músicas e assim por diante. Ele explica os diferentes cenários em que os serviços são adequados para e mostra como implementá-los, tanto para executar tarefas em segundo plano de execução longa, bem como para fornecer uma interface para chamadas de procedimento remoto._

## <a name="android-services-overview"></a>Visão geral dos serviços de Android

Aplicativos móveis não são como os aplicativos de desktop. Áreas de trabalho têm grandes quantidades de recursos, como o estado real da tela, memória, espaço de armazenamento e uma fonte de alimentação conectado, não dispositivos móveis. Essas restrições forçar aplicativos móveis para se comportar de maneira diferente. Por exemplo, a tela pequena em um dispositivo móvel normalmente significa que apenas um aplicativo (ou seja, atividade) é visível no momento. Outras atividades são movidas para o plano de fundo e enviados por push para um estado suspenso em que eles não podem executar qualquer trabalho. No entanto, apenas porque é um aplicativo do Android no plano de fundo não significa que é impossível para o aplicativo continuar trabalhando. 

Aplicativos do Android são compostos de pelo menos quatro componentes principais: _atividades_, _difusão receptores_, _provedores de conteúdo_e _Serviços_. Atividades são a base de muitos aplicativos Android ótimos, porque eles fornecem a interface do usuário que permite que um usuário interage com o aplicativo. No entanto, quando se trata de execução simultânea ou trabalho em segundo plano, atividades nem sempre são a melhor opção.
 
O mecanismo principal para o trabalho em segundo plano no Android é o _service_. Um serviço Android é um componente que foi projetado para realizar algum trabalho sem uma interface do usuário. Um serviço pode baixar um arquivo, reproduzir música ou aplicar um filtro a uma imagem. Serviços também podem ser usados para comunicação entre processos (_IPC_) entre aplicativos do Android. Por exemplo um aplicativo do Android pode usar o serviço de player de música de outro aplicativo ou um aplicativo pode expor dados (como informações de contato da pessoa) para outros aplicativos por meio de um serviço. 

Serviços e a capacidade de executar o trabalho em segundo plano, serão essenciais para fornecer uma interface do usuário simples e flexível. Todos os aplicativos Android têm um _thread principal_ (também conhecido como um _thread de interface do usuário_) nos quais as atividades são executadas. Para manter o dispositivo de resposta, Android deve ser capaz de atualizar a interface do usuário em uma taxa de 60 quadros por segundo. Se um aplicativo do Android executa a quantidade de trabalho no thread principal, Android descartará quadros, que por sua vez faz com que a interface do usuário seja exibido um (também conhecido como _janky_). Isso significa que qualquer trabalho realizado no thread da interface do usuário deve ser concluída no período de tempo entre dois quadros, aproximadamente 16 milissegundos (1 segundo a cada 60 quadros). 

Para resolver esse problema, um desenvolvedor pode usar threads em uma atividade para executar um trabalho que bloquearia a interface do usuário. No entanto, isso pode causar problemas. É bem possível que o Android destruir e a recrie as várias instâncias da atividade. No entanto, o Android não destruirá os threads, o que podem resultar em perdas de memória automaticamente. Um exemplo perfeito é quando o [dispositivo for girado](~/android/app-fundamentals/handling-rotation.md) &ndash; Android tentará destrói a instância da atividade e, em seguida, recrie um novo:

![Quando o dispositivo gira, instância 1 é destruída e 2 da instância é criada](images/image-01.png)

Este é um vazamento de memória em potencial &ndash; o thread criado pela primeira instância da atividade ainda será executado. Se o thread tem uma referência para a primeira instância da atividade, isso impedirá Android lixo coletando o objeto. No entanto, a segunda instância da atividade ainda é criada (que por sua vez, pode criar um novo thread). Girar o dispositivo várias vezes em sucessão rápida pode esgotar a memória de RAM e forçar Android para encerrar o aplicativo inteiro para recuperar memória.

Como regra geral, se o trabalho a ser realizado deve sobreviver além de uma atividade, um serviço deve ser criado para realizar o trabalho. No entanto, se o trabalho só é aplicável no contexto de uma atividade, em seguida, criar um thread para executar o trabalho pode ser mais apropriado. Por exemplo, criar uma miniatura de uma foto que acabou de ser adicionada a um aplicativo da Galeria de fotos provavelmente deve ocorrer em um serviço. No entanto, um thread pode ser mais apropriado para executar algumas músicas que só devem ser ouvida enquanto uma atividade está em primeiro plano.

Trabalho em segundo plano pode ser dividido em duas amplas classificações:

1. **Tarefa de execução longa** &ndash; este é um trabalho que está em andamento até ser explicitamente interrompido. Um exemplo de um _tarefas de longa execução_ é um aplicativo que transmite música ou que deve monitorar dados coletados a partir de um sensor. Essas tarefas devem ser executadas mesmo que o aplicativo não tem nenhuma interface do usuário visível.
2. **Tarefas periódicas** &ndash; (também conhecido como um _trabalho_) uma tarefa periódica é aquele que é relativamente curta duração (alguns segundos) e é executado em um agendamento (ou seja, uma vez por dia de uma semana ou talvez apenas uma vez no próximos 60 segundos). Um exemplo disso é baixar um arquivo da internet ou gerar uma miniatura de uma imagem.

Há quatro tipos diferentes de serviços Android:

* **Associado serviço** &ndash; um _associado serviço_ é um serviço que tem algum outro componente (normalmente uma atividade) associado a ele. Um serviço vinculado fornece uma interface que permite que o componente associado e o serviço interaja com o outro. Uma vez que não há nenhum outro cliente associado ao serviço, Android desligará o serviço.

* **`IntentService`** &ndash; Um  _`IntentService`_  é uma subclasse especializada do `Service` classe que simplifica a criação de serviço e de uso. Um `IntentService` deve lidar com chamadas autônomas individuais. Ao contrário de um serviço, o qual pode simultaneamente várias chamadas, um `IntentService` é mais parecida com um _processador de fila de trabalho_ &ndash; trabalho na fila e um `IntentService` processa cada trabalho de um de cada vez em um thread de trabalho único. Normalmente, um`IntentService` não está associado a uma atividade ou um fragmento. 

* **Serviço iniciado** &ndash; um _serviço iniciado_ é um serviço que tenha sido iniciado por outro componente Android (como uma atividade) e é executado contínuo no plano de fundo até que algo explicitamente informa o serviço parar. Ao contrário de um serviço vinculado, um serviço iniciado não tem todos os clientes diretamente associados a ele. Por esse motivo, é importante projetar serviços iniciados para que eles podem ser reiniciados normalmente conforme necessário.

* **Serviço híbrido** &ndash; um _serviço híbrido_ é um serviço que tem as características de um _serviço iniciado_ e um _associado serviço_. Um serviço híbrido pode ser iniciado quando um componente associado a ele ou ela pode ser iniciada por algum evento. Um componente de cliente pode ou não pode ser associado ao serviço híbrido. Um serviço híbrido será manter em execução até que ele explicitamente deve parar ou até que não haja nenhum outro cliente associado a ele.

O tipo de serviço para usar depende muito requisitos do aplicativo. Como regra geral, um `IntentService` ou um serviço vinculado são suficientes para a maioria das tarefas que deve executar um aplicativo do Android, para preferência deve ser fornecida a um desses dois tipos de serviços. Um `IntentService` é uma boa opção para "todos" tarefas, como baixar um arquivo, enquanto um serviço vinculado seria adequado quando frequentes interações com uma atividade/fragmento é necessária. 

Embora a maioria dos serviços executados em segundo plano, há uma subcategoria especial conhecida como uma _serviço de primeiro plano_. Este é um serviço que recebe uma prioridade mais alta (em comparação comparada um serviço normal) para realizar algum trabalho para o usuário (como reproduzir música). 

Também é possível executar um serviço em seu próprio processo no mesmo dispositivo, às vezes, isso é conhecido como um _serviço remoto_ ou como um _fora do processo serviço_. Isso exige mais esforço para criar, mas pode ser útil para quando um aplicativo precisa compartilhar recursos com outros aplicativos e podem, em alguns casos, melhorar a experiência do usuário de um aplicativo. 

Cada um desses serviços tem suas próprias características e behaviours e então será abordada mais detalhadamente nos seus próprios guias.
