---
title: Criador de perfil do Xamarin
description: Este guia explora os principais recursos do criador de perfil Xamarin. Ele procure no criadores de perfil, a criação de perfil e quando elas devem ser usadas e em um fluxo de trabalho padrão Xamarin aplicativos de criação de perfil.
ms.prod: xamarin
ms.assetid: 3247fcee-6acc-470d-ab87-c1c511d67363
author: topgenorth
ms.author: toopge
ms.date: 06/03/2018
ms.openlocfilehash: 8882cb9cd84940e12865a730f75e36ecbaf9b6f0
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2018
ms.locfileid: "37066670"
---
# <a name="xamarin-profiler"></a>Criador de perfil do Xamarin

_Este guia explora os principais recursos do criador de perfil Xamarin. Ele procure no criadores de perfil, a criação de perfil e quando elas devem ser usadas e em um fluxo de trabalho padrão Xamarin aplicativos de criação de perfil._

Êxito do aplicativo depende de experiência do usuário final. Como um desenvolvedor pode implementar alguns recursos realmente impressionantes em seu aplicativo, mas se o aplicativo está lento ou total de falhas, o usuário será provavelmente eliminá-lo.

Historicamente, Mono apresentasse um criador de perfil de linha de comando poderosa para reunir informações sobre programas em execução no tempo de execução Mono chamada a [criador de perfil do log Mono](http://www.mono-project.com/docs/debug+profile/profile/profiler/). O criador de perfil do Xamarin uma interface gráfica para o criador de perfil do log Mono e dá suporte à criação de perfil tvOS aplicativos no Windows, iOS, tvOS e aplicativos do Mac no Mac e Android, iOS e Android.

O criador de perfil do Xamarin tem um número de instrumentos disponíveis para criação de perfil — alocações, ciclos e criador de perfil de tempo. Este guia explora o esses instrumentos medem e como eles analisam seu aplicativo e explica o significado dos dados apresentados em cada tela.

Este guia examina os cenários comuns de criação de perfil e apresenta o criador de perfil como uma ferramenta para ajudar a analisar e otimizar aplicativos iOS e Android.

## <a name="download-and-install"></a>Baixar e instalar

> [!NOTE]
> Você precisará ser um [Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/compare/) assinante para desbloquear esse recurso em um Visual Studio Enterprise no Windows ou o Visual Studio para Mac em um Mac.

O criador de perfil do Xamarin é um aplicativo autônomo e está integrado com o Visual Studio para Mac e o Visual Studio habilitar a criação de perfil de dentro do IDE.

Baixe o pacote de instalação para sua plataforma:

- [**macOS**](https://dl.xamarin.com/profiler/profiler-mac.pkg)
- [**Windows**](https://dl.xamarin.com/profiler/profiler-windows.msi)

Após o download, inicie o instalador para adicionar o criador de perfil Xamarin ao seu sistema.

## <a name="profilers-and-profiling"></a>Criadores de perfis e criação de perfil

Criação de perfil é uma etapa importante e frequentemente desconsiderada no desenvolvimento de aplicativos. Criação de perfil é uma forma de **análise dinâmica programa** -ele analisa o programa enquanto ele está sendo executado e em uso. Um criador de perfil é uma ferramenta de mineração de dados que coleta informações sobre a complexidade de tempo, o uso de métodos particulares e a memória alocada. Um criador de perfil permite que você analise profundidade e analisar essas métricas para identificar áreas problemáticas em código.

Ao projetar e desenvolver um aplicativo, é importante não otimizar prematuramente; ou seja, gastar tempo desenvolvendo seu código em áreas que serão acessados raramente. Essa é a capacidade de criação de perfil. Um criador de perfil fornece ajuda a localizar áreas em que você deve gastar melhorias dificulta o tempo e uma compreensão mais usados partes de sua base de código. Os desenvolvedores tenha o cuidado de entender onde a maioria do tempo é gasto em seu aplicativo e como a memória é usada pelo seu aplicativo.

Criação de perfil é útil em todos os tipos de desenvolvimento, mas é especialmente importante em desenvolvimento móvel. Código não otimizado é mais perceptível em plataformas que em computadores desktop e o sucesso do seu aplicativo depende do código lindo e de otimização que é executada com eficiência.

## <a name="xamarin-profiler"></a>Criador de perfil do Xamarin

O criador de perfil do Xamarin fornece aos desenvolvedores uma maneira de aplicativos de perfil do Visual Studio para Mac ou o Visual Studio. O criador de perfil coleta e exibe informações sobre o aplicativo, o que pode ser usado pelo desenvolvedor para analisar o comportamento do aplicativo. Há várias maneiras diferentes de criar o perfil de um aplicativo com o criador de perfil do Xamarin, ou seja, a criação de perfil de memória e amostragem estatística. Esses são executadas por meio de alocações e o criador de perfil de tempo instrumenta respectivamente.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Atualmente, o criador de perfil do Xamarin pode ser usado para testar aplicativos xamarin, xamarin e Xamarin.Mac em Mac (por meio do Visual Studio para Mac). O criador de perfil é um processo separado a partir do IDE e, assim, além iniciando no Visual Studio para Mac, ele pode ser usado como um aplicativo autônomo para examinar .exe e `.mlpd` arquivos que foram produzidos do [criador de perfil do log mono](http://www.mono-project.com/docs/debug+profile/profile/profiler/).

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Atualmente, o criador de perfil do Xamarin pode ser usado para testar aplicativos xamarin no Windows (por meio do Visual Studio e o Visual Studio para Mac). O criador de perfil é um processo separado a partir do IDE e, assim, além iniciando no Visual Studio, ele pode ser usado como um aplicativo autônomo para examinar .exe e `.mlpd` arquivos que foram produzidos do [criador de perfil do log mono](http://www.mono-project.com/docs/debug+profile/profile/profiler/) .

-----

<a name="Profiler_Support" />

## <a name="profiler-support"></a>Suporte do criador de perfil

Suporte para o criador de perfil do Xamarin está disponível nas seguintes plataformas:

 - O Visual Studio para Mac (macOS, com licença Enterprise)
    - Android
        - Dispositivo e do emulador
    - iOS
        - Dispositivo e do simulador
    - tvOS (não há suporte para o instrumento de tempo)
        - Dispositivo e do simulador
    - Mac

 - O Visual Studio (apenas **Enterprise** versão)
    - Android
        - Dispositivo e do emulador
    - iOS [Experimental]
        - Dispositivo e do simulador
    - tvOS
        - Dispositivo e do simulador

Observe que você pode **somente** perfil **depurar** configurações.

## <a name="profiler-basics"></a>Noções básicas sobre o criador de perfil

Esta seção apresenta as partes do criador de perfil Xamarin e anda seus recursos.

### <a name="allow-profiling-in-your-app"></a>Permitir a criação de perfil em seu aplicativo

Antes de com êxito, você pode analisar seu aplicativo, você precisará permitir a criação de perfil nas opções de projeto do aplicativo.

 - iOS:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

  **Compilação > iOS depuração > Habilitar criação de perfil**

  ![](images/ios-options-mac.png "caixa de diálogo de opções no Visual Studio para Mac do iOS")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

  **Propriedades > iOS compilação > Habilitar criação de perfil**

  ![](images/ios-project-options-vs.png "caixa de diálogo de opções no Visual Studio do iOS")

-----

 - Android:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

  **Compilação > depuração Android > Habilitar a instrumentação do desenvolvedor**

  ![Caixa de diálogo Opções do Android no Visual Studio para Mac](images/android-project-options.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

  **Compilação > depuração Android > Habilitar a instrumentação do desenvolvedor**

  ![Caixa de diálogo Opções do Android no Visual Studio para Mac](images/android-project-options-vs-sml.png)

-----

### <a name="launching-the-profiler"></a>Iniciar o criador de perfil

O criador de perfil do Xamarin pode ser iniciado de seu IDE quando estiver criando o perfil de seu aplicativo iOS ou Android, ou como um aplicativo autônomo.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

#### <a name="launching-from-visual-studio-for-mac"></a>Iniciando do Visual Studio para Mac

1. Primeiro, certifique-se de que seu aplicativo seja carregado no Visual Studio para Mac e selecione a configuração de depuração (padrão).
2. Navegue até **Executar > Iniciar criação de perfil**no Visual Studio para Mac, ou **analisar > Xamarin Profiler** no Visual Studio, para abrir o criador de perfil, conforme mostrado no diagrama a seguir:

  ![](images/start-profiling-xs.png "Iniciar o criador de perfil do Visual Studio para Mac")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

#### <a name="launching-from-visual-studio"></a>Iniciando do Visual Studio

1.  Primeiro, certifique-se de que seu aplicativo seja carregado no Visual Studio e selecione a configuração de depuração (padrão), conforme especificado acima.
2.  Navegue até **analisar > Xamarin Profiler** no Visual Studio, para abrir o criador de perfil, conforme mostrado no diagrama a seguir:

![Iniciar o criador de perfil do Visual Studio](images/start-profiling-vs.png)

-----

Se os itens de menu não aparecer, consulte o [guia de solução de problemas](~/tools/profiler/troubleshooting.md).

Isso inicia o criador de perfil e inicia automaticamente o aplicativo de criação de perfil.

O criador de perfil pode ser usado para medir o desempenho e memória. Ele realiza isso por meio de alocações e o criador de perfil de tempo de instrumentos, qual exploraremos em detalhes na próxima seção.

#### <a name="saving-and-loading-profiler-sessions"></a>Salvando e carregando sessões do criador de perfil

Para salvar uma sessão de criação de perfil a qualquer momento, escolha **arquivo > Salvar como...**  na barra de menus do criador de perfil. Isso salva o arquivo em _mlpd_ formato, um formato altamente compactado especial para criação de perfil de dados.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Depois que tiver instalado o criador de perfil do Xamarin podem ser encontrada na sua pasta de aplicativos, conforme ilustrado na captura de tela abaixo:

![](images/applications.png "Abrir autônomo criador de perfil do Mac")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Depois que tiver instalado o aplicativo Xamarin Profiler pode ser encontrado no diretório de aplicativo:

![](images/applications-vs.png "Abrir autônomo criador de perfil do Windows ")

-----

Você pode carregar *.mlpd* arquivos para o criador de perfil, abra o aplicativo autônomo, selecionando **Escolher destino** e carregar o arquivo.

Para obter mais informações, consulte [gerar arquivos de .mlpd ](~/tools/profiler/troubleshooting.md#gen_mlpd).

## <a name="profiler-features"></a>Recursos do criador de perfil

O criador de perfil do Xamarin é composta de cinco seções, conforme ilustrado abaixo:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![](images/profiler-mac-sml.png "Seções do criador de perfil do Visual Studio para Mac")](images/profiler-mac.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](images/profiler-vs.png "Seções do criador de perfil do Visual Studio")](images/profiler-vs.png#lightbox)

-----

- **Barra de ferramentas** – localizado na parte superior do criador de perfil, isso oferece opções para iniciar/parar criação de perfil, selecione um processo de destino, exibir o tempo de execução do aplicativo e selecione os modos de exibição de divisão que compõem o aplicativo do criador de perfil.
- **Lista de instrumentar** – lista todos os instrumentos carregados para a sessão de criação de perfil.
- **Plotar gráfico** – esses gráficos horizontalmente se relacionam os instrumentos relevantes na lista de instrumento. Um controle deslizante (mostrado abaixo do criador de perfil de tempo) pode ser usado para alterar a escala.
- **Área de detalhes de instrumentar** -contém dados que estão sendo exibidos pela exibição selecionada do instrumento atual. Vamos examinar esses modos de exibição mais detalhadamente na seção a seguir.
- **Exibição do Inspetor** – isso contém seções que podem ser selecionadas pelo controle segmentado. As seções são dependentes do instrumento selecionado e inclui: definições de configuração, estatísticas, informações de rastreamento de pilha e o caminho para a raiz.

### <a name="allocations"></a>Alocações

Instrumento de alocações fornece informações detalhadas sobre objetos do aplicativo que estão sendo criados e coletado como lixo.

Na parte superior do criador de perfil é o gráfico de alocações, que exibe a quantidade de memória alocada em intervalos regulares durante a criação de perfil. Atualmente o gráfico de alocações é o número total de alocações e não o tamanho do heap no momento. De certa forma, nunca ficará inativo, ele só aumenta. Isso inclui objetos alocados na pilha. Dependendo da versão de tempo de execução usada, o gráfico pode parecer diferente – mesmo para o mesmo aplicativo.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![](images/allocations1.png "Instrumento de alocações")](images/allocations1.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](images/allocations1-vs.png "Instrumento de alocações")](images/allocations1-vs.png#lightbox)

-----

Há exibições de dados diferentes em instrumento de alocações, que permitem aos desenvolvedores analisar como seu aplicativo está usando e liberar memória. Essas exibições são descritas abaixo:

 -   **Alocações** – exibe uma lista de todas as alocações e agrupa pelo nome da classe. Isso fornece uma ótima visão de geral de classes e métodos que estão sendo usados, a frequência com que eles são usados e o tamanho coletivo das classes usadas. Clicando duas vezes em uma classe mostrará a memória alocada: 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

  [![](images/allocations3.png "Guia de alocações")](images/allocations3.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

  [![](images/allocations2-vs.png "Guia de alocações")](images/allocations2-vs.png#lightbox)

-----

A exibição de inspetor para alocações fornece opções de filtragem e agrupamento de objetos, fornecendo estatísticas de memória alocada e as alocações superiores, bem como modos de exibição de rastreamento de pilha e o caminho para a raiz.

 -   **Árvore de chamadas** – exibe a árvore de chamadas inteira de todos os threads do aplicativo e inclui informações sobre a memória alocada em cada nó. Quando um elemento é selecionado na lista, todos os nós irmãos aparecerá cinza. Você pode expandir a árvore ou clique duas vezes o elemento para fazer drill down nele. Ao exibir este modo de exibição de dados, a Inspetor de configurações de exibição pode ser usada para alterar a forma como ela é apresentada. Atualmente, há duas opções:
    1.  **Árvore de chamadas de invertido** – isso considera o rastreamento de pilha de cima para baixo. Essa é uma opção conveniente exibição pois indica os métodos mais profundos onde a CPU tem sido gastando seu tempo.
    2.  **Separado por thread** – essa opção organiza a árvore de chamadas por thread.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

  [![](images/allocations2.png "Guia árvore de chamadas")](images/allocations2.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

  [![](images/allocations3-vs.png "Guia árvore de chamadas")](images/allocations3-vs.png#lightbox)

-----

 -   **Instantâneos** – esse painel exibe informações sobre instantâneos de memória. Para gerá-los durante a criação de perfil de um aplicativo em tempo real, clique no _câmera_ botão na barra de ferramentas em cada ponto que você gostaria de ver que a memória é retida e liberada. Você pode clicar em cada instantâneo para explorar o que está acontecendo nos bastidores. Observe que só podem ser criados instantâneos quando ao vivo de um aplicativo de criação de perfil. 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

  [![](images/allocations4.png "Guia de instantâneos")](images/allocations4.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

  [![](images/allocations4-vs.png "Guia de instantâneos")](images/allocations4-vs.png#lightbox)

-----

### <a name="time-profiler"></a>Criador de perfil de tempo

Instrumento de criador de perfil de tempo mede exatamente quanto tempo é gasto em cada método de um aplicativo. O aplicativo está em pausa em intervalos regulares e um rastreamento de pilha é executado em cada thread ativo. Cada linha na área de detalhes de instrumento mostra o caminho de execução que foi visitado.

O gráfico, conforme mostrado na captura de tela abaixo, exibe o número de amostras recebidas pelo aplicativo conforme ele é executado:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Instrumento de criador de perfil de tempo](images/time1.png)](images/time1.png#lightbox) 

[![Instrumento de criador de perfil de tempo – lista de exemplos](images/time3.png)](images/time3.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Instrumento de criador de perfil de tempo](images/time1-vs.png)](images/time1-vs.png#lightbox) 

[![Instrumento de criador de perfil de tempo – lista de exemplos](images/time3-vs.png)](images/time3-vs.png#lightbox) 

-----

- **Árvore de chamadas** – mostra o período de tempo gasto em cada método:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

  [![](images/time2.png "Instrumento de criador de perfil de tempo – árvore de chamadas")](images/time2.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

  [![](images/time2-vs.png "Instrumento de criador de perfil de tempo – árvore de chamadas")](images/time2-vs.png#lightbox) 

-----

### <a name="cycles"></a>Ciclos

Com o uso de c# e código gerenciado do F #, ele pode ser bastante comum e Infelizmente muito fácil criar referências para objetos que nunca serão descartados. Este instrumento permite que você identifique os objetos e exibir os ciclos referenciados em seu aplicativo.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Ciclos de instrumento](images/cycles.m751-sml.png)](images/cycles.m751.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Ciclos de instrumento](images/cycles-vs-sml.png)](images/cycles-vs.png#lightbox) 

-----

## <a name="profiling-applications"></a>Aplicativos de criação de perfil

Atualmente, apenas as configurações de depuração padrão podem ser atribuídas.

Se você criar o perfil de um aplicativo com qualquer outra configuração, você verá a seguinte caixa de diálogo de mensagem:


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Caixa de diálogo de erro de criação de perfil](images/image001.png)](images/image001.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](images/image1vs.png "Caixa de diálogo de erro de criação de perfil")](images/image1vs.png#lightbox) 

-----

Selecione **atualização** para continuar.

### <a name="sgen-garbage-collector-and-profiling"></a>Coletor de lixo SGen e criação de perfil

O [SGen](http://www.mono-project.com/docs/advanced/garbage-collector/sgen/) coletor de lixo é usado para todas as plataformas de Xamarin.

SGen é um GC gerações, que aloca objetos de um aplicativo em três heaps — infantis, Heap principal e o espaço de objeto grande. Isso permite que a execução mais rápida de coleta de lixo. SGen é atualmente o GC padrão para xamarin e aplicativos xamarin unificado.

Aplicativo xamarin usando a API clássica usado o GC Boehm – um coletor de lixo não gerações, conservadora. Como é conservadora, é menos provável liberar a memória disponível, o que pode levar a resultados imprecisos ao usar o criador de perfil. Por esse motivo, o instrumento de alocações não pode ser usado com o coletor de lixo Boehm.

Enquanto você será solicitado com uma caixa de diálogo de mensagem, se seu aplicativo usa o GC Boehm, Xamarin não recomenda a troca do aplicativo iOS existente que usam Boehm para SGen sem pesquisa cuidadosa e teste completo. Xamarin também não recomenda a troca para SGen para criação de perfil e alternando de volta, como esses resultados não fornecerá precisas avaliações de desempenho de utilização de memória.

Para obter mais informações sobre gerenciamento de memória, consulte o [memória e práticas recomendadas de desempenho](~/cross-platform/deploy-test/memory-perf-best-practices.md) guia.

## <a name="summary"></a>Resumo

Este guia vimos que criação de perfil é e como ele é vantajoso para o desenvolvedor. Em seguida, apresentamos o criador de perfil do Xamarin, fornecer algumas informações de histórico e em funcionamento. Finalmente, percorrido os recursos do criador de perfil Xamarin e explorou as alocações e instrumentos do criador de perfil de tempo.

## <a name="related-links"></a>Links relacionados

- [Práticas recomendadas de memória e desempenho](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Notas de Versão](https://developer.xamarin.com/releases/profiler/preview/)
