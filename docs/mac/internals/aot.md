---
title: Xamarin. Mac à frente de compilação de tempo
description: Este documento descreve à frente de compilação de tempo no xamarin. Mac. Compara a compilação de AOT para a compilação JIT, explica como habilitar a AOT e examina o AOT híbrido.
ms.prod: xamarin
ms.assetid: 38B8A017-5A58-429C-A6E9-9860A1DCEF63
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 11/10/2017
ms.openlocfilehash: e155a394afd68d9970ee32785f6d0aeda6e2d129
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117247"
---
# <a name="xamarinmac-ahead-of-time-compilation"></a>Xamarin. Mac à frente de compilação de tempo

## <a name="overview"></a>Visão geral

Em frente de tempo (AOT) a compilação é uma técnica de otimização poderosos para melhorar o desempenho de inicialização. No entanto, isso também afeta seu tempo de compilação, o tamanho do aplicativo e a execução do programa maneiras profundas. Para entender as vantagens e desvantagens que ele impõe, vamos nos aprofundar um pouco sobre a compilação e execução de um aplicativo.

Códigos escritos em linguagens, gerenciadas, como C# e F#, é compilado em uma representação intermediária chamada de IL. Essa IL, armazenado em seus assemblies de biblioteca e o programa, é relativamente compacta e portáteis entre arquiteturas de processador. IL, no entanto, é apenas um intermediário de conjunto de instruções e, em algum momento em que o IL precisará ser traduzido em código de computador específico para o processador.

Há dois pontos em que esse processamento pode ser feito:

- **Just-in-time (JIT)** – durante a inicialização e execução de seu aplicativo a IL é compilada na memória para o código de máquina.
- **Antecipada de tempo (AOT)** – durante a compilação de IL é compilado e escrito em bibliotecas nativas e armazenado dentro de seu pacote de aplicativo.

Cada opção tem um número de vantagens e desvantagens:

- **JIT**
  - **Tempo de inicialização** – a compilação JIT deve ser feita na inicialização. Para a maioria dos aplicativos, isso é na ordem de 100 ms, mas para aplicativos grandes, esse tempo pode ser significativamente mais.
  - **Execução** – código como o JIT pode ser otimizado para o processador específico que está sendo usado, o código um pouco melhor pode ser gerado. Na maioria dos aplicativos trata de alguns pontos percentuais mais rapidamente no máximo.
- **AOT**
  - **Tempo de inicialização** – carregar dylibs na pré-compilado é significativamente mais rápida do que os assemblies JIT.
  - **Espaço em disco** – esses dylibs na podem levar uma quantidade significativa de espaço em disco no entanto. Dependendo de quais assemblies são AOTed, pode duas vezes ou mais o tamanho da parte do código do seu aplicativo.
  - **Tempo de compilação** – compilação AOT é significativamente mais lenta que JIT e diminuirá compilações usá-lo. Essa diminuição pode variar de segundos até um minuto ou mais, dependendo do tamanho e número de assemblies compilados.
  - **Ofuscação** – como IL, que é muito mais fácil de reverter a engenharia de que o código de máquina, não é necessariamente obrigatório ele pode ser eliminado para ajudar o ofuscar o código confidencial. Isso requer a opção descrevem abaixo "híbrida".

## <a name="enabling-aot"></a>Habilitar a AOT

Opções de AOT serão adicionadas ao painel de Build do Mac em uma atualização futura. Até lá, habilitar a AOT requer passando um argumento de linha de comando por meio do campo "argumentos mmp adicionais" no Build do Mac. As opções são as seguintes:


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



## <a name="hybrid-aot"></a>AOT híbrido

Durante a execução de um aplicativo do macOS o tempo de execução padrão é usando o código de máquina carregados a partir de bibliotecas nativas produzidas pela compilação AOT. No entanto, há algumas áreas do código como trampolines, em que compilação JIT pode produzir resultados significativamente mais otimizados. Isso exige que o IL de assemblies gerenciados estar disponível. No iOS, os aplicativos são restritos de qualquer uso da compilação JIT; Esses seção de código são AOT compilado também.

Opção híbrida instrui o compilador a ambas as compilação esses seção (como o iOS), mas também para supõem que o IL não estará disponível em tempo de execução. Em seguida, pode ser eliminado essa IL após a compilação. Conforme observado acima, o tempo de execução será forçado a usar menos rotinas otimizadas em alguns locais.

## <a name="further-considerations"></a>Considerações adicionais

As consequências negativas de escala AOT com os tamanhos e o número de assemblies processados. Completo [estrutura de destino](~/mac/platform/target-framework.md) de exemplo contém um significativamente maior biblioteca BCL (Base Class) que moderno e, portanto, AOT vai demorar significativamente mais tempo e produzir pacotes maiores. Isso é complicado por incompatibilidade do framework de destino completo com a vinculação, que remove código não utilizado. Considere a possibilidade de mover seu aplicativo para vinculação moderna e habilitação para obter os melhores resultados.

Um benefício adicional de AOT vem com interações aprimoradas com depuração nativa e cadeias de ferramentas de criação de perfil. Uma vez que a grande maioria da Base de código será compilada antes do tempo, terão nomes de função e símbolos que são mais fáceis de ler dentro de relatórios de falhas nativas, perfis e depuração. Funções JIT gerado não têm esses nomes e geralmente aparecem como deslocamentos hexadecimais sem nome que são muito difíceis de resolver.
