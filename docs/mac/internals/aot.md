---
title: O Xamarin. Mac antes da compilação do tempo
description: Este documento descreve antecipadamente a compilação de tempo no Xamarin. Mac. Ele compara a compilação da AOT com a compilação JIT, explica como habilitar a AOT e examina a AOT híbrida.
ms.prod: xamarin
ms.assetid: 38B8A017-5A58-429C-A6E9-9860A1DCEF63
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 11/10/2017
ms.openlocfilehash: e1f1e2e1e5dbec7dc8f2310b3f9565d0bc209c00
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70283686"
---
# <a name="xamarinmac-ahead-of-time-compilation"></a>O Xamarin. Mac antes da compilação do tempo

## <a name="overview"></a>Visão geral

A compilação antecipada de tempo (AOT) é uma poderosa técnica de otimização para melhorar o desempenho de inicialização. No entanto, ele também afeta o tempo de compilação, o tamanho do aplicativo e a execução do programa de maneiras profundas. Para entender as compensações que ele impõe, vamos nos aprofundar um pouco na compilação e execução de um aplicativo.

O C# código escrito em linguagens gerenciadas, F#como e, é compilado em uma representação intermediária chamada Il. Esse IL, armazenado em seus assemblies de biblioteca e de programa, é relativamente compacto e portátil entre arquiteturas de processador. No entanto, o IL é apenas um conjunto intermediário de instruções e, em algum momento, o IL precisará ser convertido no código do computador específico do processador.

Há dois pontos em que esse processamento pode ser feito:

- **JIT (just in time)** – durante a inicialização e a execução do seu aplicativo, o Il é compilado na memória para o código do computador.
- À **frente do tempo (AOT)** – durante a compilação, a Il é compilada e gravada em bibliotecas nativas e armazenada em seu pacote de aplicativos.

Cada opção tem vários benefícios e compensações:

- **JIT**
  - **Tempo de inicialização** – a compilação JIT deve ser feita na inicialização. Para a maioria dos aplicativos, isso está na ordem de 100 ms, mas, para grandes aplicativos, esse tempo pode ser significativamente maior.
  - **Execução** – como o código JIT pode ser otimizado para o processador específico que está sendo usado, um código ligeiramente melhor pode ser gerado. Na maioria dos aplicativos, há alguns pontos percentuais mais rápidos no máximo.
- **AOT**
  - **Tempo de inicialização** – carregar o dylibs pré-compilado é significativamente mais rápido do que os assemblies JIT.
  - **Espaço em disco** – essas dylibs podem levar uma quantidade significativa de espaço em disco no entanto. Dependendo de quais assemblies são AOTed, ele pode dobrar ou mais o tamanho da parte do código do seu aplicativo.
  - **Tempo de compilação** – a compilação AOT é significativamente mais lenta e fará com que as compilações sejam lentas usando-a. Essa lentidão pode variar de segundos até um minuto ou mais, dependendo do tamanho e do número de assemblies compilados.
  - **Ofuscação** – como o Il, que é significativamente mais fácil de fazer engenharia reversa do que o código da máquina, não é necessariamente necessário que ele possa ser eliminado para ajudar a ofuscar o código confidencial. Isso exige que a opção "híbrida" seja descrita abaixo.

## <a name="enabling-aot"></a>Habilitando a AOT

As opções de AOT serão adicionadas ao painel de Build do Mac em uma atualização futura. Até lá, habilitar a AOT requer a passagem de um argumento de linha de comando por meio do campo "argumentos de MMP adicionais" na compilação do Mac. As opções são as seguintes:

```
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
```


## <a name="hybrid-aot"></a>AOT híbrida

Durante a execução de um aplicativo macOS, o tempo de execução usa o código do computador carregado das bibliotecas nativas produzidas pela compilação da AOT. No entanto, há algumas áreas de código, como trampolines, em que a compilação JIT pode produzir resultados significativamente mais otimizados. Isso requer que o IL dos assemblies gerenciados esteja disponível. No iOS, os aplicativos são restritos de qualquer uso da compilação JIT; Essa seção de código também é compilada pela AOT.

A opção híbrido instrui o compilador a compilar essas seções (como o iOS), mas também para pressupor que o IL não estará disponível em tempo de execução. Esse IL pode então ser extraído após a compilação. Conforme observado acima, o tempo de execução será forçado a usar rotinas menos otimizadas em alguns lugares.

## <a name="further-considerations"></a>Considerações adicionais

As consequências negativas da escala de AOT com os tamanhos e o número de assemblies processados. A [estrutura de destino](~/mac/platform/target-framework.md) completa, por exemplo, contém uma BCL (biblioteca de classes base) significativamente maior do que a moderna e, portanto, a AOT levará significativamente mais tempo e produzirá pacotes maiores. Isso é composto pela incompatibilidade da estrutura de destino completa com a vinculação, que retira o código não utilizado. Considere mover seu aplicativo para o moderno e habilitar a vinculação para obter os melhores resultados.

Um benefício adicional da AOT vem com interações aprimoradas com depuração nativa e criação de perfil de cadeias. Como uma grande maioria da base de código será compilada antecipadamente, ela terá nomes de função e símbolos que são mais fáceis de ler dentro de relatórios de falhas nativos, criação de perfil e depuração. As funções geradas por JIT não têm esses nomes e muitas vezes aparecem como deslocamentos hexadecimais sem nome que são muito difíceis de resolver.
