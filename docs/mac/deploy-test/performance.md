---
title: Desempenho do Xamarin.Mac
description: Este documento fornece algumas considerações de desempenho para aplicativos Xamarin.Mac. Ele aborda a estrutura de destino moderna, o vinculador, a AOT, os delegados, as APIs Cocoa para reutilização de modos de exibição e o código assíncrono.
ms.prod: xamarin
ms.assetid: 54B07DED-FDF2-49B2-A5FB-3A9357E65922
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 11/10/2017
ms.openlocfilehash: 48e5e71a9d4435852f72eb9be21f2e80cd124bfd
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291089"
---
# <a name="xamarinmac-performance"></a>Desempenho do Xamarin.Mac

## <a name="overview"></a>Visão geral

Aplicativos Xamarin.Mac são semelhantes a Xamarin.iOS e muitas das mesmas sugestões de desempenho são aplicáveis:

- [Desempenho do Xamarin.iOS](~/ios/deploy-test/performance.md)
- [Desempenho de plataforma cruzada](~/cross-platform/deploy-test/memory-perf-best-practices.md)

Mas há inúmeras sugestões específicas para macOS que podem ser úteis.

## <a name="prefer-modern-target-framework"></a>Preferir a estrutura de destino moderna

Há várias [Estruturas de Destino](~/mac/platform/target-framework.md) disponíveis para o aplicativo Xamarin.Mac com diferentes características de desempenho e recursos.

Quando for possível, prefira a Moderna e trabalhe com bibliotecas dependentes para adicionar suporte. Somente a Estrutura de Destino Moderna permite a vinculação, que pode reduzir drasticamente os tamanhos de assembly. Isso será importante principalmente no momento em que habilitar a AOT, uma vez que a compilação da AOT dos assemblies completos pode produzir pacotes finais grandes.

## <a name="enable-the-linker"></a>Habilitar o vinculador

O tempo de inicialização, tanto no carregamento quanto no JIT (Just In Time), é escalonado um pouco linearmente com o tamanho de seus binários finais. A maneira mais fácil de melhorar isso é removendo o código morto com o [vinculador](~/mac/deploy-test/linker.md).

Enquanto essa sugestão se aplica principalmente aos usuários da Estrutura de Destino Moderna, o uso da [Vinculação de Plataforma](~/mac/deploy-test/linker.md) também pode fornecer um aumento de desempenho limitado.

## <a name="enable-aot-when-appropriate"></a>Habilitar AOT quando apropriado

Outra faceta do desempenho de inicialização é a compilação JIT dos assemblies em código de computador. A compilação AOT (Ahead of Time) pode reduzir significativamente o tempo de inicialização, mas acompanha diversas compensações abordadas na [documentação de AOT](~/mac/internals/aot.md).

## <a name="ensure-performant-delegates"></a>Assegurar delegados com bom desempenho

Muitos aplicativos Xamarin.Mac são centralizados em torno de exibições do Cocoa como `NSCollectionView`, `NSOutlineView` ou `NSTableView`. Geralmente, essas exibições são alimentadas por classes `Delegate` e `DataSource` que você fornece ao Cocoa, respondendo perguntas sobre o que exibir.

Muitos desses pontos de entrada são invocados com frequência e, ocasionalmente, várias vezes por segundo durante a rolagem.

Verifique se que essas funções retornam valores que são calculados facilmente ou use as informações já armazenadas em cache, para impedir o bloqueio da interface do usuário.

## <a name="use-cocoa-provided-apis-for-reusing-views"></a>Usar APIs fornecidas pelo Cocoa para reutilização de exibições

Várias exibições do Cocoa que contêm muitas células ou exibições filhas (como `NSCollectionView`, `NSOutlineView` e `NSTableView`) fornecem APIs para criar e reutilizar as exibições. Essas APIs criam pools de itens compartilhados e evitam problemas de desempenho ao rolar pelas exibições rapidamente.

## <a name="use-async-and-do-not-block-the-ui"></a>Usar async e não bloquear a interface do usuário

Aplicativos da área de trabalho geralmente processam grandes quantidades de dados e é muito fácil bloquear o thread de interface do usuário esperando por uma operação síncrona.

Sempre que possível, use [async](~/cross-platform/platform/async.md) e threads para evitar o bloqueio da interface do usuário.

Para operações de execução longa, considere o uso de [NSProgressIndicator](https://docs.microsoft.com/samples/xamarin/mac-samples/progressbarexample) ou outras opções observadas no [HIG](https://developer.apple.com/macos/human-interface-guidelines/indicators/progress-indicators/) da Apple para notificar os usuários.


## <a name="related-links"></a>Links relacionados

- [Desempenho de plataforma cruzada](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Desempenho do Xamarin.iOS](~/ios/deploy-test/performance.md)
