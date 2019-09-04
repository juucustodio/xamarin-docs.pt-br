---
title: Criador de perfil do Xamarin
description: Este guia explora os principais recursos do Xamarin Profiler. Ele examina os profileres, a criação de perfil e quando eles devem ser usados e em um fluxo de trabalho padrão para a criação de perfil de aplicativos Xamarin.
ms.prod: xamarin
ms.assetid: 3247fcee-6acc-470d-ab87-c1c511d67363
author: lobrien
ms.author: laobri
ms.date: 06/03/2018
ms.openlocfilehash: b62c8453049a1e014cd75cbee562c8f1a2c16c31
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227889"
---
# <a name="xamarin-profiler"></a>Criador de perfil do Xamarin

_Este guia explora os principais recursos do Xamarin Profiler. Ele examina os profileres, a criação de perfil e quando eles devem ser usados e em um fluxo de trabalho padrão para a criação de perfil de aplicativos Xamarin._

O sucesso de um aplicativo depende da experiência do usuário final. Como desenvolvedor, você pode ter implementado alguns recursos realmente impressionantes em seu aplicativo, mas se o aplicativo estiver lento ou cheio de falhas, o usuário provavelmente se livrará dele.

Historicamente, o mono apresentou um criador de perfil de linha de comando poderoso para coletar informações sobre programas em execução no tempo de execução mono chamado de [log](https://www.mono-project.com/docs/debug+profile/profile/profiler/)Profiler mono. O Xamarin Profiler uma interface gráfica para o criador de perfil de log do mono e dá suporte à criação de perfil de aplicativos Android, iOS, tvOS e Mac em aplicativos Mac e Android, iOS e tvOS no Windows.

O Xamarin Profiler tem um número de instrumentos disponíveis para criação de perfil — alocações, ciclos e criador de perfil de tempo. Este guia explora o que esses instrumentos medem e como eles analisam seu aplicativo e esclarecem o significado dos dados apresentados em cada tela.

Este guia examina cenários comuns de criação de perfil e apresenta o criador de perfil como uma ferramenta para ajudar a analisar e otimizar aplicativos iOS e Android.

## <a name="download-and-install"></a>Baixar e instalar

> [!NOTE]
> Você precisará ser um assinante [Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/compare/) para desbloquear esse recurso em qualquer Visual Studio Enterprise no Windows ou Visual Studio para Mac em um Mac.

O Xamarin Profiler é um aplicativo autônomo e é integrado ao Visual Studio para Mac e ao Visual Studio para habilitar a criação de perfil no IDE.

Baixe o pacote de instalação da sua plataforma:

- [**macOS**](https://dl.xamarin.com/profiler/profiler-mac.pkg)
- [**Windows**](https://dl.xamarin.com/profiler/profiler-windows.msi)

Depois de baixado, inicie o instalador para adicionar o Xamarin Profiler ao seu sistema.

## <a name="profilers-and-profiling"></a>Profileres e criação de perfil

A criação de perfil é uma etapa importante e geralmente ignorada no desenvolvimento de aplicativos. A criação de perfil é uma forma de **análise dinâmica do programa** – ela analisa o programa enquanto ele está em execução e em uso. Um criador de perfil é uma ferramenta Data Mining que coleta informações sobre a complexidade do tempo, o uso de métodos específicos e a memória que está sendo alocada. Um criador de perfil permite que você faça drill-through e analise essas métricas para identificar áreas problemáticas no código.

Ao projetar e desenvolver um aplicativo, é importante não otimizar prematuramente; ou seja, gastar tempo desenvolvendo seu código em áreas que raramente serão acessadas. Esse é o poder da criação de perfil. Um criador de perfil fornece informações sobre as partes mais usadas de sua base de código e ajuda a localizar áreas em que você deve gastar tempo fazendo melhorias. Os desenvolvedores devem tomar cuidado para entender onde a maior parte do tempo é gasto em seu aplicativo e como a memória é usada pelo seu aplicativo.

A criação de perfil é útil em todos os tipos de desenvolvimento, mas é especialmente crucial no desenvolvimento móvel. O código não otimizado é muito mais perceptível em plataformas móveis do que em computadores desktop, e o sucesso do seu aplicativo depende de um código bonito e otimizado que é executado com eficiência.

## <a name="xamarin-profiler"></a>Criador de perfil do Xamarin

O Xamarin Profiler fornece aos desenvolvedores uma maneira de criar o perfil de aplicativos de dentro do Visual Studio para Mac ou do Visual Studio. O criador de perfil coleta e exibe informações sobre o aplicativo, que podem ser usadas pelo desenvolvedor para analisar o comportamento de um aplicativo. Há várias maneiras diferentes de criar o perfil de um aplicativo com o Xamarin Profiler, ou seja, a criação de perfis de memória e a amostragem estatística. Elas são realizadas por meio das alocações e dos instrumentos do time Profiler, respectivamente.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Atualmente, o Xamarin Profiler pode ser usado para testar aplicativos Xamarin. iOS, Xamarin. Android e Xamarin. Mac no Mac (via Visual Studio para Mac). O criador de perfil é um processo separado do IDE e, assim, além de iniciar a partir de Visual Studio para Mac, ele pode ser usado como um aplicativo autônomo para examinar. `.mlpd` exe e os arquivos que foram produzidos do [criador de perfil de log do mono](https://www.mono-project.com/docs/debug+profile/profile/profiler/).

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Atualmente, o Xamarin Profiler pode ser usado para testar aplicativos Xamarin. Android no Windows (por meio do Visual Studio e Visual Studio para Mac). O criador de perfil é um processo separado do IDE e, assim, além de iniciar do Visual Studio, ele pode ser usado como um aplicativo autônomo para examinar. exe e `.mlpd` os arquivos que foram produzidos no log profiler do [mono](https://www.mono-project.com/docs/debug+profile/profile/profiler/).

-----

<a name="Profiler_Support" />

## <a name="profiler-support"></a>Suporte do criador de perfil

O suporte para o Xamarin Profiler está disponível nas seguintes plataformas:

- Visual Studio para Mac (macOS, com licença Enterprise)
  - Android
    - Dispositivo e emulador
  - iOS
    - Dispositivo e simulador
  - tvOS (não há suporte para o instrumento de tempo)
    - Dispositivo e simulador
  - Mac

- Visual Studio (somente versão **Enterprise** )
  - Android
    - Dispositivo e emulador
  - iOS [experimental]
    - Dispositivo e simulador
  - tvOS
    - Dispositivo e simulador

Observe que você **só** pode criar o perfil de configurações de **depuração** .

## <a name="profiler-basics"></a>Noções básicas do criador de perfil

Esta seção apresenta as partes do Xamarin Profiler e o tour pelos seus recursos.

### <a name="allow-profiling-in-your-app"></a>Permitir a criação de perfil em seu aplicativo

Antes de poder criar o perfil do seu aplicativo com êxito, você precisará permitir a criação de perfil nas opções de projeto do aplicativo.

- iOS:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

  **Compilar > depuração do iOS > habilitar a criação de perfil**

  ![Caixa de diálogo opções do iOS no Visual Studio para Mac](images/ios-options-mac.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  **Propriedades > Build do iOS > habilitar a criação de perfil**

  ![Caixa de diálogo de opções do iOS no Visual Studio](images/ios-project-options-vs.png)

-----

- Android:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

  **Compilar > > de depuração do Android habilitar instrumentação do desenvolvedor**

  ![Caixa de diálogo opções do Android no Visual Studio para Mac](images/android-project-options.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  **Compilar > > de depuração do Android habilitar instrumentação do desenvolvedor**

  ![Caixa de diálogo opções do Android no Visual Studio para Mac](images/android-project-options-vs-sml.png)

-----

### <a name="launching-the-profiler"></a>Iniciando o criador de perfil

O Xamarin Profiler pode ser iniciado no IDE quando você estiver criando perfis para seu aplicativo iOS ou Android, ou como um aplicativo autônomo.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

#### <a name="launching-from-visual-studio-for-mac"></a>Iniciando do Visual Studio para Mac

1. Primeiro, verifique se seu aplicativo foi carregado no Visual Studio para Mac e selecione a configuração de depuração (padrão).
2. Navegue para **executar > iniciar a criação de perfil**em Visual Studio para Mac ou **analisar > Xamarin Profiler** no Visual Studio para abrir o criador de perfil, conforme demonstrado no diagrama a seguir:

  ![Iniciando o criador de perfil do Visual Studio para Mac](images/start-profiling-xs.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

#### <a name="launching-from-visual-studio"></a>Iniciando do Visual Studio

1. Primeiro, verifique se o aplicativo foi carregado no Visual Studio e selecione a configuração de depuração (padrão), conforme especificado acima.
2. Navegue para **analisar > Xamarin Profiler** no Visual Studio, para abrir o criador de perfil, conforme demonstrado no diagrama a seguir:

![Iniciando o criador de perfil no Visual Studio](images/start-profiling-vs.png)

-----

Se os itens de menu não forem exibidos, consulte o [Guia de solução de problemas](~/tools/profiler/troubleshooting.md).

Isso inicia o criador de perfil e inicia automaticamente a criação de perfil do aplicativo.

O criador de perfil pode ser usado para medir a memória e o desempenho. Ele consegue isso por meio das alocações e dos instrumentos do criador de perfil, que serão explorados detalhadamente na próxima seção.

#### <a name="saving-and-loading-profiler-sessions"></a>Salvando e carregando sessões do criador de perfil

Para salvar uma sessão de criação de perfil a qualquer momento, escolha **arquivo > salvar como...** na barra de menus do criador de perfil. Isso salva o arquivo no formato _MLPD_ , um formato especial e altamente compactado para a criação de perfil de dados.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Após a instalação, o Xamarin Profiler pode ser encontrado na pasta aplicativos, conforme ilustrado na captura de tela abaixo:

![Abrir o criador de perfil autônomo do Mac](images/applications.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Depois de instalado, o aplicativo Xamarin Profiler pode ser encontrado no diretório do aplicativo:

![Abrir o criador de perfil autônomo do Windows](images/applications-vs.png)

-----

Você pode carregar arquivos *. MLPD* no criador de perfil abrindo o aplicativo autônomo, selecionando **escolher destino** e carregando o arquivo.

Para obter mais informações, consulte [gerando arquivos. MLPD](~/tools/profiler/troubleshooting.md#gen_mlpd).

## <a name="profiler-features"></a>Recursos do criador de perfil

O Xamarin Profiler é composto de cinco seções, conforme ilustrado abaixo:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Seções do criador de perfil no Visual Studio para Mac](images/profiler-mac-sml.png)](images/profiler-mac.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Seções do criador de perfil no Visual Studio](images/profiler-vs.png)](images/profiler-vs.png#lightbox)

-----

- **Barra de ferramentas** – localizada na parte superior do criador de perfil, isso oferece opções para iniciar/parar a criação de perfil, selecionar um processo de destino, exibir o tempo de execução do aplicativo e selecionar as exibições divididas que compõem o aplicativo Profiler.
- **Lista de instrumentos** – lista todos os instrumentos carregados para a sessão de criação de perfil.
- **Gráfico** de plotagem – esses gráficos se relacionam horizontalmente aos instrumentos relevantes na lista de instrumentos. Um controle deslizante (mostrado abaixo do time Profiler) pode ser usado para alterar a escala.
- **Área de detalhes do instrumento** -contém dados exibidos pela exibição selecionada do instrumento atual. Veremos essas exibições mais detalhadamente na seção abaixo.
- **Exibição de Inspetor** – contém seções que podem ser selecionadas pelo controle segmentado. As seções são dependentes do instrumento selecionado e incluem: Definições de configuração, estatísticas, informações de rastreamento de pilha e caminho para raízes.

### <a name="allocations"></a>Alocações

O instrumento de alocações fornece informações detalhadas sobre objetos no aplicativo enquanto eles estão sendo criados e coletados como lixo.

Na parte superior do criador de perfil está o gráfico de alocações, que exibe a quantidade de memória alocada em intervalos regulares durante a criação de perfil. Atualmente, o grafo de alocações é o número total de alocações e não o tamanho do heap nesse ponto no tempo. De certa forma, ele nunca ficará inativo, ele só aumentará. Isso inclui objetos alocados na pilha. Dependendo da versão de tempo de execução usada, o gráfico pode parecer diferente – mesmo para o mesmo aplicativo.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Instrumento de alocações](images/allocations1.png)](images/allocations1.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Instrumento de alocações](images/allocations1-vs.png)](images/allocations1-vs.png#lightbox)

-----

Há diferentes exibições de dados no instrumento de alocações, que permite aos desenvolvedores analisar como seu aplicativo está usando e liberando memória. Essas exibições são descritas abaixo:

- **Alocações** – exibe uma lista de todas as alocações e as agrupa por nome de classe. Isso fornece uma ótima visão geral das classes e métodos que estão sendo usados, com que frequência eles são usados e o tamanho coletivo das classes usadas. Clicar duas vezes em uma classe mostrará a memória alocada: 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

  [![Guia alocações](images/allocations3.png)](images/allocations3.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![Guia alocações](images/allocations2-vs.png)](images/allocations2-vs.png#lightbox)

-----

A exibição de inspetor para alocações fornece opções para filtrar e agrupar objetos, fornecer estatísticas sobre memória alocada e as principais alocações, bem como exibições para rastreamento de pilha e caminho para raiz.

- **Árvore de chamadas** – exibe toda a árvore de chamadas de todos os threads no aplicativo e inclui informações sobre a memória alocada em cada nó. Quando um elemento é selecionado na lista, todos os nós irmãos aparecerão em cinza. Você pode expandir a árvore ou clicar duas vezes no elemento para fazer uma busca detalhada dele. Ao exibir essa exibição de dados, a exibição do Inspetor de configurações de exibição pode ser usada para alterar a maneira como ela é apresentada. Atualmente, há duas opções:
    1. **Árvore de chamada invertida** – considera o rastreamento de pilha de cima para baixo. Essa é uma opção de exibição conveniente, pois indica os métodos mais profundos em que a CPU está gastando seu tempo.
    2. **Separar por thread** – essa opção organiza a árvore de chamadas por thread.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

  [![Guia de árvore de chamada](images/allocations2.png)](images/allocations2.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![Guia de árvore de chamada](images/allocations3-vs.png)](images/allocations3-vs.png#lightbox)

-----

- **Instantâneos** – esse painel exibe informações sobre instantâneos de memória. Para gerá-los durante a criação de perfil de um aplicativo ativo, clique no botão _câmera_ na barra de ferramentas em cada ponto em que você gostaria de ver qual memória é retida e liberada. Em seguida, você pode clicar em cada instantâneo para explorar o que está acontecendo nos bastidores. Observe que os instantâneos só podem ser obtidos durante a criação de perfil ao vivo de um aplicativo. 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

  [![Guia instantâneos](images/allocations4.png)](images/allocations4.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![Guia instantâneos](images/allocations4-vs.png)](images/allocations4-vs.png#lightbox)

-----

### <a name="time-profiler"></a>Time Profiler

O instrumento do criador de perfil de tempo mede exatamente quanto tempo é gasto em cada método de um aplicativo. O aplicativo é pausado em intervalos regulares e um rastreamento de pilha é executado em cada thread ativo. Cada linha na área de detalhes do instrumento mostra o caminho de execução que foi seguido.

O gráfico de plotagem, conforme mostrado na captura de tela abaixo, exibe o número de amostras recebidas pelo aplicativo conforme ele é executado:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Instrumento do criador de perfil de tempo](images/time1.png)](images/time1.png#lightbox) 

[![Instrumento do criador de perfil de tempo – lista de exemplos](images/time3.png)](images/time3.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Instrumento do criador de perfil de tempo](images/time1-vs.png)](images/time1-vs.png#lightbox) 

[![Instrumento do criador de perfil de tempo – lista de exemplos](images/time3-vs.png)](images/time3-vs.png#lightbox) 

-----

- **Árvore de chamadas** – mostra a quantidade de tempo gasto em cada método:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

  [![Instrumento do criador de perfil de tempo – árvore de chamadas](images/time2.png)](images/time2.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![Instrumento do criador de perfil de tempo – árvore de chamadas](images/time2-vs.png)](images/time2-vs.png#lightbox) 

-----

### <a name="cycles"></a>Ciclos

Com o uso do C# e F# do código gerenciado, ele pode ser bastante comum e, infelizmente, é muito fácil criar referências a objetos que nunca serão descartados. Esse instrumento permite que você identifique esses objetos e exiba os ciclos referenciados em seu aplicativo.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Instrumento de ciclos](images/cycles.m751-sml.png)](images/cycles.m751.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Instrumento de ciclos](images/cycles-vs-sml.png)](images/cycles-vs.png#lightbox) 

-----

## <a name="profiling-applications"></a>Aplicativos de criação de perfil

No momento, somente as configurações de depuração padrão podem ser Profiles.

Se você criar o perfil de um aplicativo com qualquer outra configuração, receberá a seguinte caixa de diálogo de mensagem:


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Diálogo de erro de criação de perfil](images/image001.png)](images/image001.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Diálogo de erro de criação de perfil](images/image1vs.png)](images/image1vs.png#lightbox) 

-----

Selecione **Atualizar** para continuar.

### <a name="sgen-garbage-collector-and-profiling"></a>Coleta de lixo e criação de perfil do SGen

O coletor de lixo do [SGen](https://www.mono-project.com/docs/advanced/garbage-collector/sgen/) é usado para todas as plataformas do Xamarin.

SGen é um GC de geração, que aloca objetos de um aplicativo em três heaps — Nursery, heap principal e o espaço de objeto grande. Isso permite a execução Speedier da coleta de lixo. O SGen atualmente é o GC padrão para aplicativos unificados do Xamarin. Android e Xamarin. iOS.

Aplicativo Xamarin. iOS usando o API Clássica usou o Boehm GC – um coletor de lixo conservador e não de geração. Como é conservador, é menos provável que a memória disponível seja liberada, o que pode levar a resultados imprecisos ao usar o criador de perfil. Por esse motivo, o instrumento de alocações não pode ser usado com o coletor de lixo Boehm.

Enquanto você receberá uma caixa de diálogo de mensagem se seu aplicativo usa o Boehm GC, o Xamarin não recomenda alternar o aplicativo iOS existente que usa o Boehm para o SGen sem análise cuidadosa e teste completo. O Xamarin também não recomenda mudar para o SGen para criação de perfil e, em seguida, voltar, pois esses resultados não fornecerão parâmetros de comparação precisos de uso de memória.

Para obter mais informações sobre o gerenciamento de memória, consulte o guia de [práticas recomendadas de memória e desempenho](~/cross-platform/deploy-test/memory-perf-best-practices.md) .

## <a name="summary"></a>Resumo

Neste guia, examinamos o que é a criação de perfil e como é vantajoso para o desenvolvedor. Em seguida, apresentamos o Xamarin Profiler, fornecendo algum histórico e informações sobre como ele funciona. Por fim, vimos os recursos do Xamarin Profiler e exploramos os instrumentos de alocações e de criador de perfil de tempo.

## <a name="related-links"></a>Links relacionados

- [Práticas recomendadas de memória e desempenho](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Notas de Versão](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/profiler/preview/index.md)
