---
title: Depuração de vários processos
ms.topic: article
ms.prod: xamarin
ms.assetid: 852F8AB1-F9E2-4126-9C8A-12500315C599
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/24/2017
ms.openlocfilehash: 7bc230b43d0b50746a6076ffb2633e7fad0283d9
ms.sourcegitcommit: 20ca85ff638dbe3a85e601b5eb09b2f95bda2807
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
---
# <a name="multi-process-debugging"></a>Depuração de vários processos

É muito comum que as soluções modernas desenvolvidas no Visual Studio para Mac tenham vários projetos com diferentes plataformas de destino. Por exemplo, uma solução pode ter um projeto de aplicativo móvel que se baseia nos dados fornecidos por um projeto de serviço Web. Ao desenvolver essa solução, o desenvolvedor pode precisar que os dois projetos sejam executados simultaneamente para solucionar erros. A partir da [versão Cycle 9 do Xamarin](https://releases.xamarin.com/stable-release-cycle-9/), o Visual Studio para Mac consegue depurar vários processos que estejam sendo executados ao mesmo tempo. Isso possibilita definir pontos de interrupção, inspecionar variáveis e exibir threads em mais de um projeto em execução. Isso é conhecido como _depuração de vários processos_. 

Este guia discutirá algumas das alterações feitas no Visual Studio para Mac para dar suporte à depuração de vários processos, como configurar soluções para depurar vários processos e como anexar a processos existentes com o Visual Studio para Mac.

## <a name="requirements"></a>Requisitos

Depurar vários processos de exige o Visual Studio para Mac.

## <a name="ide-changes"></a>Alterações no IDE

Para ajudar os desenvolvedores com a depuração de vários processos, o Visual Studio para Mac passou por algumas alterações em sua interface do usuário. O Visual Studio para Mac tem uma **Barra de ferramentas de depuração** atualizada e uma nova seção **Configuração de Soluções** na pasta **Opções da Solução**. Além disso, o **Painel Threads** agora exibirá os processos em execução e os threads de cada processo. O Visual Studio para Mac também exibirá vários painéis de depuração, um para cada processo, para certas ações, como **Saída do Aplicativo**.

### <a name="solution-configurations"></a>Configurações da solução

Por padrão, o Visual Studio para Mac exibirá um projeto individual na área **Configuração da Solução** da barra de ferramentas de depuração. Quando uma sessão de depuração é iniciada, esse é o projeto que o Visual Studio para Mac iniciará e ao qual anexará o depurador.

Para iniciar e depurar vários processos no Visual Studio para Mac, é necessário criar uma _configuração da solução_. Uma configuração da solução descreve quais projetos em uma solução devem ser incluídos quando uma sessão de depuração é iniciada com um clique do botão **Iniciar** ou quando &#8984;&#8617; (**Cmd-Enter**) é pressionado. A captura de tela a seguir é um exemplo de uma solução do Visual Studio para Mac que tem várias configurações da solução:

![](multi-process-debugging-images/mpd01-xs.png "Uma solução com várias configurações")

### <a name="parts-of-the-debug-toolbar"></a>Partes da barra de ferramentas de depuração

A barra de ferramentas de depuração foi alterada para permitir que uma configuração da solução seja selecionada por meio de um menu pop-up. Esta captura de tela mostra as partes da barra de ferramentas de depuração:

![](multi-process-debugging-images/mpd02-xs.png "As partes da barra de ferramentas de depuração")

1. **Configuração da Solução** – É possível definir a configuração da solução clicando na configuração da solução na barra de ferramentas de depuração e selecionando a configuração no menu pop-up:

    ![](multi-process-debugging-images/mpd03-xs.png "Um pop-up de amostra com configurações da solução")

2. **Destino de Build** – Identifica o destino de build para os projetos. Isso permanece inalterado em relação às versões anteriores do Visual Studio para Mac.
3. **Destinos de Dispositivo** – Seleciona os dispositivos nos quais a solução será executada. É possível identificar um dispositivo ou emulador separado para cada projeto:

    ![](multi-process-debugging-images/mpd04-xs.png "Pop-up mostrando os dispositivos de um projeto")

### <a name="multiple-debug-pads"></a>Vários painéis de depuração

Quando a configuração de várias soluções for iniciada, alguns dos painéis do Visual Studio para Mac serão exibidos várias vezes, uma para cada processo. Por exemplo, a captura de tela a seguir mostra dois painéis **Saída do Aplicativo** para uma solução que está executando dois projetos:

![](multi-process-debugging-images/mpd05-xs.png "Painel de Saída para uma configuração da solução")

### <a name="multiple-processes-and-the-active-thread"></a>Vários processos e o _Thread Ativo_

Quando um ponto de interrupção for encontrado em um processo, esse processo pausará a execução, enquanto os outros processos continuarão em execução. Em um cenário de um único processo, o Visual Studio para Mac pode facilmente exibir informações como threads, variáveis locais, saída do aplicativo em um único conjunto de painéis. No entanto, quando há vários processos com diversos pontos de interrupção e, possivelmente, vários threads, pode ser muito complicado para o desenvolvedor lidar com as informações de uma sessão de depuração que esteja tentando exibir todas as informações de todos os threads (e processos) ao mesmo tempo.

Para resolver esse problema, o Visual Studio para Mac somente exibirá as informações de um thread por vez, isso é conhecido como o _thread ativo_. O primeiro thread que faz uma pausa em um ponto de interrupção é considerado o _thread ativo_. O thread ativo é aquele que é o foco de atenção do desenvolvedor. Os comandos de depuração como **Step Over** &#8679;&#8984;O (Shift-Cmd-O), serão emitidos para o thread ativo.

O **Painel de Threads** exibirá informações de todos os processos e threads que estão sob inspeção na configuração da solução e fornecerá dicas visuais sobre o que é o thread ativo:

![](multi-process-debugging-images/mpd06-xs.png "Painel de threads para uma configuração da solução")

Os threads são agrupados pelo processo que os está hospedando. O nome do projeto e a ID do thread ativo serão exibidos em negrito e uma seta apontando para a direita será exibida na medianiz ao lado do thread ativo. Na captura de tela anterior, o **thread n° 1** na **ID do processo 48703** (**FirstProject**) é o thread ativo.

Ao depurar vários processos, é possível mudar o thread ativo para ver informações de depuração desse processo (ou thread) usando o **Painel de Threads**. Para mudar o thread ativo, selecione o thread desejado no **Painel de Threads** e, em seguida, clique duas vezes nele.

#### <a name="stepping-through-code-when-multiple-projects-are-stopped"></a>Percorrendo o código quando vários projetos são interrompidos

Quando dois (ou mais) projetos tiverem pontos de interrupção, o Visual Studio para Mac pausará todos os processos. Só é possível **Step Over** código no thread ativo. O outro processo estará em pausa até que uma alteração de escopo possibilite que o depurador mude o foco do thread ativo. Por exemplo, considere a seguinte captura de tela do Visual Studio para Mac depurando dois projetos:

![](multi-process-debugging-images/mpd09-xs.png  "Visual Studio para Mac depurando dois projetos")

Nessa tela, cada solução tem seu próprio ponto de interrupção. Quando a depuração começa, o primeiro ponto de interrupção a ser encontrado está na **linha 10** de `MainClass` no **SecondProject**. Como ambos os projetos têm pontos de interrupção, os dois processos são interrompidos. Depois que o ponto de interrupção é encontrado, cada invocação do **Step Over** fará com que o Visual Studio para Mac execute step over no código do thread ativo.

A ação de percorrer o código está limitada ao thread ativo, portanto, o Visual Studio para Mac percorrerá uma linha de código por vez, enquanto o outro processo ainda estiver em pausa.

Usando a captura de tela anterior como um exemplo, quando o loop de `for` for concluído, o Visual Studio para Mac permitirá que o **FirstProject** seja executado até que o ponto de interrupção na **linha 11** em `MainClass` seja encontrado. Para cada comando **Step Over**, o depurador avança linha por linha no **FirstProject**, até que os algoritmos de heurística internos do Visual Studio para Mac retornem o thread ativo para o **SecondProject**.

Se apenas um dos projetos tiver um ponto de interrupção definido, somente esse processo estará em pausa. O outro projeto continuará em execução até que esteja em pausa pelo desenvolvedor ou que um ponto de interrupção seja adicionado.

### <a name="pausing-and-resuming-a-processes"></a>Pausando e retomando um processo

É possível pausar ou retomar um processo clicando com o botão direito do mouse no processo e selecionando **Pausar** ou **Retomar** no menu de contexto:

![](multi-process-debugging-images/mpd08-xs.png "Pausar ou retomar no painel de Threads")

A aparência da barra de ferramentas de depuração será alterada dependendo do estado dos projetos que estão sendo depurados. Quando vários projetos estiverem em execução, a barra de ferramentas de depuração exibirá os botões **Pausar** e **Retomar** quando houver pelo menos um projeto em execução e um projeto em pausa:

![](multi-process-debugging-images/mpd07-xs.png  "Barra de ferramentas de depuração")

Clicar no botão **Pausar** na **Barra de ferramentas de depuração** pausará todos os processos que estão sendo depurados, enquanto clicar nos botões **Retomar** retomará todos os processos em pausa.

### <a name="debugging-a-second-project"></a>Depurando um segundo projeto

Também é possível depurar um segundo projeto depois que o primeiro projeto for iniciado pelo Visual Studio para Mac. Depois que o primeiro projeto for iniciado, **Clique com o botão direito do mouse* no projeto no **Painel da Solução** e selecione **Iniciar Depuração de Item**:

![](multi-process-debugging-images/mpd13-xs.png  "Iniciar Depuração de Item")

## <a name="creating-a-solution-configuration"></a>Criando uma configuração da solução

A _configuração da solução_ informa ao Visual Studio para Mac qual projeto deve ser executado quando uma sessão de depuração é iniciada com o botão **Iniciar**. Pode haver mais de uma configuração de solução por solução. Isso torna possível especificar quais projetos são executados ao depurar o projeto.

Para criar uma nova configuração da solução no Xamaring Studio:

1. Abra a caixa de diálogo **Opções da Solução** no Visual Studio para Mac e selecione **Executar > Configurações**:

    ![](multi-process-debugging-images/mpd10-xs.png "Configuração da Solução na caixa de diálogo Opções da Solução")

2. Clique no botão **Novo**, insira o nome da nova configuração da solução e clique em **Criar**. A nova configuração da solução será exibida na janela **Configurações**:

    ![](multi-process-debugging-images/mpd11-xs.png  "Nomeando uma nova configuração da solução")

3. Selecione a nova configuração de execução na lista de configurações. A caixa de diálogo **Opções da Solução** exibirá cada projeto na solução. Marque cada projeto que deverá ser iniciado quando uma sessão de depuração for iniciada:

    ![](multi-process-debugging-images/mpd12-xs.png "Selecionando o projeto a ser iniciado")

Agora, a configuração da solução **MultipleProjects** será exibida na **Barra de ferramentas de depuração**, permitindo que o desenvolvedor depure os dois projetos simultaneamente.

## <a name="summary"></a>Resumo

Este guia discutiu a depuração de vários processos no Visual Studio para Mac. Ele abordou algumas das alterações no IDE para dar suporte à depuração de vários processos e descreveu alguns dos comportamentos associados.

## <a name="related-links"></a>Links relacionados

- [Notas de versão do Xamarin Cycle 9](https://releases.xamarin.com/stable-release-cycle-9/)
