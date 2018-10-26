---
title: Criador de perfil do Xamarin
description: Este guia explora os principais recursos do que o Xamarin Profiler. Ele procurar em criadores de perfil, a criação de perfil e quando deve ser usados e em um fluxo de trabalho padrão para a criação de perfil de aplicativos Xamarin.
ms.prod: xamarin
ms.assetid: 3247fcee-6acc-470d-ab87-c1c511d67363
author: lobrien
ms.author: laobri
ms.date: 06/03/2018
ms.openlocfilehash: 237ee1a39907f9ebf0eb88db9fff1fbdab691f5e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112581"
---
# <a name="xamarin-profiler"></a>Criador de perfil do Xamarin

_Este guia explora os principais recursos do que o Xamarin Profiler. Ele procurar em criadores de perfil, a criação de perfil e quando deve ser usados e em um fluxo de trabalho padrão para a criação de perfil de aplicativos Xamarin._

Êxito de um aplicativo depende da experiência do usuário final. Como desenvolvedor você poderia ter alguns recursos realmente impressionantes implementado em seu aplicativo, mas se o aplicativo está lento ou total de falhas, o usuário será provavelmente eliminá-lo.

Historicamente, Mono já Destaque um criador de perfil de linha de comando poderosa para reunir informações sobre programas em execução no tempo de execução Mono chamada a [criador de perfil de log Mono](http://www.mono-project.com/docs/debug+profile/profile/profiler/). O Xamarin Profiler uma interface gráfica do criador de perfil de log Mono e dá suporte à criação de perfil tvOS aplicativos no Windows, iOS, tvOS e aplicativos do Mac no Mac e Android, iOS e Android.

O Xamarin Profiler tem um número de instrumentos disponíveis para criação de perfil — alocações, ciclos e Profiler de tempo. Este guia explora esses instrumentos medem e como eles analisam seu aplicativo e explica o significado dos dados apresentados em cada tela.

Este guia examina os cenários comuns de criação de perfil e apresenta o criador de perfil como uma ferramenta para ajudar a analisar e otimizar aplicativos iOS e Android.

## <a name="download-and-install"></a>Baixe e instale

> [!NOTE]
> Você precisará ser um [Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/compare/) assinante para desbloquear esse recurso em qualquer Visual Studio Enterprise no Windows ou o Visual Studio para Mac em um Mac.

O Xamarin Profiler é um aplicativo autônomo e é integrado ao Visual Studio para Mac e Visual Studio habilitar a criação de perfil de dentro do IDE.

Baixe o pacote de instalação para sua plataforma:

- [**macOS**](https://dl.xamarin.com/profiler/profiler-mac.pkg)
- [**Windows**](https://dl.xamarin.com/profiler/profiler-windows.msi)

Após o download, inicie o instalador para adicionar o Xamarin Profiler ao seu sistema.

## <a name="profilers-and-profiling"></a>Os criadores de perfis e criação de perfil

Criação de perfil é uma etapa muitas vezes negligenciada e importante no desenvolvimento de aplicativos. Criação de perfil é uma forma de **análise de programa dinâmico** -ele analisa o programa enquanto ele está em execução e em uso. Um criador de perfil é uma ferramenta de mineração de dados que coleta informações sobre a complexidade de tempo, o uso de métodos particulares e a memória sendo alocada. Um criador de perfil permite que você analise profunda e analisar essas métricas para identificar áreas problemáticas em código.

Ao projetar e desenvolver um aplicativo, é importante não Otimize prematuramente; ou seja, gastar tempo desenvolvendo seu código em áreas que serão acessados raramente. Essa é a capacidade de criação de perfil. Um criador de perfil fornece insight mais comumente usados partes da sua base de código e ajuda a localizar áreas onde você deve gastar aprimoramentos de tempo fazendo. Os desenvolvedores devem ter cuidado para entender onde a maior parte do tempo é gasto em seu aplicativo e como a memória é usada pelo seu aplicativo.

Criação de perfil é útil para todos os tipos de desenvolvimento, mas é especialmente crucial no desenvolvimento móvel. Código não otimizado é mais perceptível em plataformas móveis que em computadores desktop e o sucesso do seu aplicativo depende do código de lindo e otimizado que é executado com eficiência.

## <a name="xamarin-profiler"></a>Criador de perfil do Xamarin

O Xamarin Profiler fornece aos desenvolvedores uma maneira para analisar aplicativos do Visual Studio para Mac ou Visual Studio. O criador de perfil coleta e exibe informações sobre o aplicativo, que pode ser usado pelo desenvolvedor para analisar o comportamento do aplicativo. Há várias maneiras diferentes de criar o perfil de um aplicativo com o Xamarin Profiler, ou seja, a criação de perfil de memória e amostragem estatística. Elas são executadas por meio das alocações e tempo Profiler instrumenta respectivamente.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Atualmente, o Xamarin Profiler podem ser usado para testar aplicativos xamarin. IOS, xamarin. Android e xamarin. Mac no Mac (por meio do Visual Studio para Mac). O criador de perfil é um processo separado do IDE e, então, além de iniciar a partir do Visual Studio para Mac, ele pode ser usado como um aplicativo autônomo para examinar .exe e `.mlpd` arquivos que foram produzidos no [criador de perfil de log mono](http://www.mono-project.com/docs/debug+profile/profile/profiler/).

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Atualmente, o Xamarin Profiler podem ser usado para testar aplicativos xamarin. Android no Windows (por meio do Visual Studio e Visual Studio para Mac). O criador de perfil é um processo separado do IDE e, então, além de iniciar a partir do Visual Studio, ele pode ser usado como um aplicativo autônomo para examinar .exe e `.mlpd` arquivos que foi produzidos a partir de [criador de perfil de log mono](http://www.mono-project.com/docs/debug+profile/profile/profiler/) .

-----

<a name="Profiler_Support" />

## <a name="profiler-support"></a>Suporte do Profiler

Suporte para o Xamarin Profiler está disponível nas seguintes plataformas:

 - O Visual Studio para Mac (macOS, com licença Enterprise)
    - Android
        - Dispositivo e o emulador
    - iOS
        - Dispositivo e simulador
    - tvOS (não há suporte para tempo de instrumento)
        - Dispositivo e simulador
    - Mac

 - Visual Studio (apenas **Enterprise** versão)
    - Android
        - Dispositivo e o emulador
    - iOS [Experimental]
        - Dispositivo e simulador
    - tvOS
        - Dispositivo e simulador

Observe que você pode **só** perfil **depurar** configurações.

## <a name="profiler-basics"></a>Noções básicas do Profiler

Esta seção apresenta as partes do que o Xamarin Profiler e faz um tour pelas seus recursos.

### <a name="allow-profiling-in-your-app"></a>Permitir a criação de perfil em seu aplicativo

Antes de com êxito, você pode analisar seu aplicativo, você precisa permitir a criação de perfil nas opções de projeto do aplicativo.

 - iOS:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

  **Build > depuração do iOS > Habilitar criação de perfil**

  ![](images/ios-options-mac.png "caixa de diálogo de opções no Visual Studio para Mac do iOS")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  **Propriedades > Build do iOS > Habilitar criação de perfil**

  ![](images/ios-project-options-vs.png "caixa de diálogo de opções no Visual Studio do iOS")

-----

 - Android:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

  **Build > depuração do Android > Habilitar instrumentação do desenvolvedor**

  ![Caixa de diálogo Opções do Android no Visual Studio para Mac](images/android-project-options.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  **Build > depuração do Android > Habilitar instrumentação do desenvolvedor**

  ![Caixa de diálogo Opções do Android no Visual Studio para Mac](images/android-project-options-vs-sml.png)

-----

### <a name="launching-the-profiler"></a>Iniciar o Profiler

O Xamarin Profiler pode ser iniciado em seu IDE quando estiver criando o perfil de seu aplicativo iOS ou Android, ou como um aplicativo autônomo.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

#### <a name="launching-from-visual-studio-for-mac"></a>Iniciando o Visual Studio para Mac

1. Primeiro, certifique-se de que seu aplicativo carregado no Visual Studio para Mac e selecione a configuração de depuração (padrão).
2. Navegue até **Executar > Iniciar criação de perfil**no Visual Studio para Mac, ou **analisar > Xamarin Profiler** no Visual Studio, para abrir o Profiler, conforme demonstrado no diagrama a seguir:

  ![](images/start-profiling-xs.png "Iniciar o Profiler do Visual Studio para Mac")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

#### <a name="launching-from-visual-studio"></a>Iniciando o Visual Studio

1.  Primeiro, verifique se você tiver o seu aplicativo carregado no Visual Studio e selecione a configuração de depuração (padrão), conforme especificado acima.
2.  Navegue até **analisar > Xamarin Profiler** no Visual Studio, para abrir o Profiler, conforme demonstrado no diagrama a seguir:

![Iniciar o Profiler do Visual Studio](images/start-profiling-vs.png)

-----

Se os itens de menu não aparecer, consulte o [guia de solução](~/tools/profiler/troubleshooting.md).

Isso inicia o Profiler e inicia automaticamente o aplicativo de criação de perfil.

O Profiler pode ser usado para medir o desempenho e memória. Ela consegue isso por meio do Profiler de tempo e alocações de instrumentos, o que exploraremos em detalhes na próxima seção.

#### <a name="saving-and-loading-profiler-sessions"></a>Salvando e carregando sessões do Profiler

Para salvar uma sessão de criação de perfil a qualquer momento, escolha **arquivo > Salvar como...**  na barra de menus do Profiler. Isso salva o arquivo no _mlpd_ formato, um formato altamente compactado especial para dados de criação de perfil.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Depois que tiver instalado o Xamarin Profiler podem ser encontrada em sua pasta de aplicativos, conforme ilustrado na captura de tela abaixo:

![](images/applications.png "Abra autônomo Profiler do Mac")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Depois que tiver instalado o aplicativo de Xamarin Profiler pode ser encontrado no diretório do aplicativo:

![](images/applications-vs.png "Abra autônomo Profiler do Windows ")

-----

Você pode carregar *.mlpd* arquivos para o Profiler, abrindo o aplicativo autônomo, selecionando **Escolher destino** e carregar o arquivo.

Para obter mais informações, consulte [gerando arquivos .mlpd ](~/tools/profiler/troubleshooting.md#gen_mlpd).

## <a name="profiler-features"></a>Recursos do Profiler

O Xamarin Profiler é composto por cinco seções, conforme ilustrado abaixo:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](images/profiler-mac-sml.png "Seções do Profiler no Visual Studio para Mac")](images/profiler-mac.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](images/profiler-vs.png "Seções do Profiler no Visual Studio")](images/profiler-vs.png#lightbox)

-----

- **Barra de ferramentas** – localizado na parte superior do criador de perfil, isso oferece opções para iniciar/parar criação de perfil, selecione um processo de destino, exibir o tempo de execução do aplicativo e selecione os modos de exibição de divisão que compõem o aplicativo do criador de perfil.
- **Lista de instrumentar** – essa opção lista todos os instrumentos carregados para a sessão de criação de perfil.
- **Plotar um gráfico** – esses gráficos horizontalmente se relacionam os instrumentos relevantes na lista de instrumento. Um controle deslizante (mostrado abaixo Profiler de tempo) pode ser usado para alterar a escala.
- **Área de detalhes de instrumentar** -contém dados que estão sendo exibidos pela exibição selecionada do instrumento atual. Vamos examinar esses modos de exibição mais detalhadamente na seção a seguir.
- **Modo de exibição do Inspetor** – isso contém seções que podem ser selecionadas pelo controle segmentado. As seções são dependentes no instrumento selecionado e inclui: definições de configuração, estatísticas, informações de rastreamento de pilha e o caminho para raízes.

### <a name="allocations"></a>Alocações

O instrumento de alocações fornece informações detalhadas sobre os objetos no aplicativo conforme eles são criados e coletados como lixo.

Na parte superior do criador de perfil é o gráfico de alocações, que exibe a quantidade de memória alocada em intervalos regulares durante a criação de perfil. Atualmente, o gráfico de alocações é o número total de alocações e não o tamanho do heap no momento. De certa forma, ele nunca ficará inativo, ele só aumenta. Isso inclui objetos alocados na pilha. Dependendo da versão de tempo de execução usado, o gráfico pode parecer diferente – até mesmo para o mesmo aplicativo.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](images/allocations1.png "Alocações de instrumento")](images/allocations1.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](images/allocations1-vs.png "Alocações de instrumento")](images/allocations1-vs.png#lightbox)

-----

Há exibições de dados diferentes no instrumento alocações, que permitem aos desenvolvedores analisar como seu aplicativo está usando e liberar memória. Essas exibições são descritas abaixo:

 -   **Alocações** – exibe uma lista de todas as alocações e agrupa-os pelo nome da classe. Isso fornece uma ótima visão geral de classes e métodos que estão sendo usados, a frequência com que eles são usados e o tamanho coletivo das classes usadas. Clicando duas vezes em uma classe mostrará a memória alocada: 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

  [![](images/allocations3.png "Guia de alocações")](images/allocations3.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![](images/allocations2-vs.png "Guia de alocações")](images/allocations2-vs.png#lightbox)

-----

Exibição do Inspetor para alocações fornece opções de filtragem e agrupamento de objetos, fornecendo estatísticas de memória alocada e as alocações principais, bem como modos de exibição para rastreamento de pilha e o caminho para a raiz.

 -   **Árvore de chamadas** – exibe a árvore de chamadas inteira de todos os threads no aplicativo e inclui informações sobre a memória alocada em cada nó. Quando um elemento for selecionado na lista, todos os nós irmãos aparecerá cinza. Você pode expandir a árvore ou clique duas vezes no elemento para fazer drill down nele. Ao exibir este modo de exibição de dados, a inspeção de configurações de exibição pode ser usada para alterar a forma como ela é apresentada. Atualmente, há duas opções:
    1.  **Invertida Call Tree** – isso considera o rastreamento de pilha de cima para baixo. Essa é uma opção de exibição conveniente pois indica os métodos mais profundos em que a CPU tem sido dedicando seu tempo.
    2.  **Separado por thread** – essa opção organiza a árvore de chamadas por thread.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

  [![](images/allocations2.png "Guia árvore de chamada")](images/allocations2.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![](images/allocations3-vs.png "Guia árvore de chamada")](images/allocations3-vs.png#lightbox)

-----

 -   **Instantâneos** – esse painel exibe informações sobre instantâneos de memória. Para gerá-los durante a criação de perfil de um aplicativo ao vivo, clique no _câmera_ botão na barra de ferramentas em cada ponto em que você gostaria de ver qual memória é mantida e liberada. Você pode clicar em cada instantâneo para explorar o que está acontecendo nos bastidores. Observe que só podem ser criados instantâneos quando ao vivo de um aplicativo de criação de perfil. 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

  [![](images/allocations4.png "Guia de instantâneos")](images/allocations4.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![](images/allocations4-vs.png "Guia de instantâneos")](images/allocations4-vs.png#lightbox)

-----

### <a name="time-profiler"></a>Profiler de tempo

O instrumento de Profiler de tempo mede exatamente quanto tempo é gasto em cada método de um aplicativo. O aplicativo está em pausa em intervalos regulares e um rastreamento de pilha é executado em cada thread ativo. Cada linha na área de detalhes de instrumento mostra o caminho de execução que foi visitado.

O gráfico de plotagem, conforme mostrado na captura de tela abaixo, exibe o número de amostras recebidas pelo aplicativo conforme ele é executado:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Tempo Profiler instrumento](images/time1.png)](images/time1.png#lightbox) 

[![Tempo Profiler instrumento – lista de exemplos](images/time3.png)](images/time3.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Tempo Profiler instrumento](images/time1-vs.png)](images/time1-vs.png#lightbox) 

[![Tempo Profiler instrumento – lista de exemplos](images/time3-vs.png)](images/time3-vs.png#lightbox) 

-----

- **Árvore de chamadas** – mostra o período de tempo gasto em cada método:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

  [![](images/time2.png "Tempo Profiler instrumento – árvore de chamadas")](images/time2.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![](images/time2-vs.png "Tempo Profiler instrumento – árvore de chamadas")](images/time2-vs.png#lightbox) 

-----

### <a name="cycles"></a>Ciclos

Com o uso de C# e F# código gerenciado, ele pode ser bastante comum e, infelizmente muito fácil criar referências para objetos que nunca serão descartados. Neste instrumento permite que você identifique esses objetos e exibir os ciclos de referência em seu aplicativo.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Ciclos de instrumento](images/cycles.m751-sml.png)](images/cycles.m751.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Ciclos de instrumento](images/cycles-vs-sml.png)](images/cycles-vs.png#lightbox) 

-----

## <a name="profiling-applications"></a>Criação de perfil de aplicativos

Atualmente, apenas as configurações de depuração padrão podem ser perfiladas.

Se você criar o perfil de um aplicativo com qualquer outra configuração, você verá a seguinte caixa de diálogo de mensagem:


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Caixa de diálogo Erro de criação de perfil](images/image001.png)](images/image001.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](images/image1vs.png "Caixa de diálogo Erro de criação de perfil")](images/image1vs.png#lightbox) 

-----

Selecione **atualização** para continuar.

### <a name="sgen-garbage-collector-and-profiling"></a>Coletor de lixo SGen e a criação de perfil

O [SGen](http://www.mono-project.com/docs/advanced/garbage-collector/sgen/) coletor de lixo é usado para todas as plataformas do Xamarin.

SGen é um GC gerativo, que aloca objetos de um aplicativo em três heaps — Berçário, Heap principal e o espaço de objeto grande. Isso permite a execução mais rápida de coleta de lixo. SGen é atualmente o GC padrão para xamarin. Android e os aplicativos unificados do xamarin. IOS.

Aplicativo xamarin. IOS usando a API clássica usado o GC Boehm – um coletor de lixo conservador, não geracional. Como é conservadora, é menos provável liberar a memória disponível, o que pode levar a resultados imprecisos ao usar o criador de perfil. Por esse motivo, o instrumento de alocações não pode ser usado com o coletor de lixo Boehm.

Enquanto você será solicitado com uma caixa de diálogo de mensagem, se seu aplicativo usa o GC Boehm, Xamarin não recomenda a alternância de aplicativo existente do iOS que usam Boehm para SGen sem pesquisa cuidadosa e teste completo. Xamarin também não recomendamos mudar para SGen para criação de perfil e alternando novamente, pois esses resultados não fornecerá precisas avaliações de desempenho de utilização de memória.

Para obter mais informações sobre o gerenciamento de memória, consulte o [memória e práticas recomendadas de desempenho](~/cross-platform/deploy-test/memory-perf-best-practices.md) guia.

## <a name="summary"></a>Resumo

Neste guia, nós examinamos quais criação de perfil é e como ele é vantajoso para o desenvolvedor. Em seguida, apresentamos o Profiler Xamarin, fornecer algumas informações de histórico e como isso funciona. Por fim, percorrido os recursos do que o Xamarin Profiler e explorou as alocações e tempo Profiler instrumentos.

## <a name="related-links"></a>Links relacionados

- [Práticas recomendadas de desempenho e memória](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Notas de Versão](https://developer.xamarin.com/releases/profiler/preview/)
