---
title: Xamarin.Mac à frente de compilação
description: À frente das considerações e compensações de compilação de tempo (AOT)
ms.prod: xamarin
ms.assetid: 38B8A017-5A58-429C-A6E9-9860A1DCEF63
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 11/10/2017
ms.openlocfilehash: d6be1d6e4a3cccef827862fb1fd54b202b0ce5fd
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="xamarinmac-ahead-of-time-compilation"></a>Xamarin.Mac à frente de compilação

## <a name="overview"></a>Visão geral

Em frente (AOT) de tempo de compilação é uma técnica de otimização poderosa para melhorar o desempenho de inicialização. No entanto, isso também afeta o tempo de compilação, o tamanho do aplicativo e a execução do programa de maneiras profundas. Para entender as compensações impõe, vamos nos aprofundar um pouco em compilação e execução de um aplicativo.

Código gravado nas linguagens gerenciadas, como c# e F #, é compilado em uma representação intermediária chamada IL. Essa IL, armazenado em seus assemblies de biblioteca e o programa, é relativamente compacto e portátil entre arquiteturas de processador. IL, no entanto, é apenas um intermediário de conjunto de instruções e, em algum momento que IL precisará ser convertido em código de computador específico ao processador.

Há dois pontos no qual esse processamento pode ser feito:

- **Just-in-time (JIT)** – durante a inicialização e a execução do seu aplicativo IL é compilado na memória para o código de máquina.
- **Antecipada de tempo (AOT)** – durante a compilação de IL é compilado e gravado em bibliotecas nativas e armazenado em seu pacote de aplicativo.

Cada opção tem um número de vantagens e desvantagens:

- **JIT**
  - **Tempo de inicialização** – compilação JIT deve ser feita na inicialização. Para a maioria dos aplicativos, isso é de 100 ms, mas para aplicativos grandes esse tempo pode ser muito mais.
  - **Execução** – código como o JIT pode ser otimizado para o processador específico que está sendo usado, um pouco melhor código pode ser gerado. Na maioria dos aplicativos trata alguns pontos percentuais mais rapidamente no máximo.
- **AOT**
  - **Tempo de inicialização** – carregar dylibs pré-compilado é significativamente mais rápido que assemblies JIT.
  - **Espaço em disco** – essas dylibs Entretanto pode levar uma quantidade significativa de espaço em disco. Dependendo de quais conjuntos são AOTed, pode duas vezes ou mais o tamanho da parte do código do seu aplicativo.
  - **Tempo de compilação** – compilação AOT é significativamente mais lenta que JIT e diminuirá compilações usá-lo. Essa diminuição pode variar de segundos até um minuto ou mais, dependendo do tamanho e o número de assemblies compilados.
  - **Ofuscação** – como o IL, que é muito mais fácil de reverter a engenharia que código de máquina, não é necessariamente necessário ele pode ser eliminado para ajudar a ofuscar o código confidencial. Isso requer a opção descrevem abaixo "híbrida".

## <a name="enabling-aot"></a>Habilitando AOT

Opções de AOT serão adicionadas ao painel de compilação Mac em uma atualização futura. Até lá, a habilitação do AOT requer passando um argumento de linha de comando por meio do campo "argumentos adicionais mmp" na compilação de Mac. As opções são as seguintes:


      --aot[=VALUE]          Specify assemblies that should be AOT compiled
                               - none - No AOT (default)
                               - all - Every assembly in MonoBundle
                               - core - Xamarin.Mac, System, mscorlib
                               - sdk - Xamarin.Mac.dll and BCL assemblies
                               - |hybrid after option enables hybrid AOT which
                               allows IL stripping but is slower (only valid
                               for 'all')
                                - Individual files can be included for AOT via +
                               FileName.dll and excluded via -FileName.dll

                               Examples:
                                 --aot:all,-MyAssembly.dll
                                 --aot:core,+MyOtherAssembly.dll,-mscorlib.dll



## <a name="hybrid-aot"></a>Híbrido AOT

Durante a execução de um aplicativo macOS o tempo de execução padrão é usando o código de máquina carregados a partir de bibliotecas nativas produzidas pela compilação AOT. No entanto, há algumas áreas do código como trampolines, onde compilação JIT pode produzir resultados significativamente mais otimizados. Isso exige que o IL de assemblies gerenciados para estar disponível. No iOS, os aplicativos são impedidos de qualquer uso da compilação JIT; Esses seção de código são AOT compilado também.

A opção híbrida instrui o compilador a ambos os compilação esses seção (como iOS) mas também para supõem que o IL não estará disponível em tempo de execução. Essa IL, em seguida, pode ser removido após a compilação. Conforme observado acima, o tempo de execução será forçado a usar menos otimizadas rotinas em alguns locais.

## <a name="further-considerations"></a>Considerações adicionais

As consequências negativas da escala AOT com os tamanhos e o número de módulos (assemblies) processados. Completa [framework de destino](~/mac/platform/target-framework.md) de exemplo contém um significativamente maior biblioteca BCL (Base Class) que moderno e, portanto, AOT vai demorar significativamente mais tempo e produzir pacotes maiores. Isso é composto por incompatibilidade do framework de destino completo com a vinculação, que extrai o código não utilizado. Considere mover seu aplicativo moderno e habilitar a vinculação para obter melhores resultados.

Um benefício adicional de AOT vem com interações aprimoradas com depuração e criação de perfil toolchains nativo. Desde que a grande maioria da Base de código será compilada antecipadamente, terão nomes de função e símbolos que são mais fáceis de ler em relatórios de falha nativo, criação de perfil e depuração. Funções JIT gerado não têm esses nomes e geralmente aparecem como deslocamentos hexadecimais sem nome que são muito difíceis de resolver.
