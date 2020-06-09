---
title: Quando e como devo criar um relatório de bugs?
description: Este documento descreve quando, onde e como arquivar um relatório de bugs. Ele também fornece práticas recomendadas de relatório de bugs que permitem aos engenheiros diagnosticar melhor o problema.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8AD9CFBF-282A-4C1F-95E9-25F21141B052
author: davidortinau
ms.author: daortin
ms.date: 08/01/2018
ms.openlocfilehash: 0dfffe2b3363a2d5498c0b8628b382a4331b9269
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571448"
---
# <a name="when-and-how-should-i-file-a-bug-report"></a>Quando e como devo criar um relatório de bugs?

> [!TIP]
> Usar o item de menu **relatar um problema** no Visual Studio &ndash; , isso enviará informações de diagnóstico junto com o relatório de bugs para ajudar a resolver o problema.
>
> Há instruções detalhadas para o [visual studio 2019 ou o visual studio 2017](https://docs.microsoft.com/visualstudio/ide/how-to-report-a-problem-with-visual-studio) e o [Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/report-a-problem).
>
> Você pode pesquisar relatórios existentes no site da [comunidade de desenvolvedores do Visual Studio](https://developercommunity.visualstudio.com/) .

## <a name="file-a-bug-if"></a>Arquivar um bug se...

Você tem um conjunto de etapas que você considera que os engenheiros poderão usar para reproduzir um problema.

OU

Você pode descrever cuidadosamente os sintomas visíveis do problema, especialmente se você também pode descrever algumas circunstâncias precisas relacionadas ao problema. <sup> [[1]](#note-1)</sup>

## <a name="best-practices-to-help-address-bugs-quickly-and-efficiently"></a>Práticas recomendadas para ajudar a resolver bugs com rapidez e eficiência

1. <a name="ref-1"></a>Pesquise a [comunidade de desenvolvedores do Visual Studio](https://developercommunity.visualstudio.com/) e a Web para obter relatórios de bugs ou sugestões de uso existentes que possam resolver o problema diretamente. <sup>[[2]](#note-2)</sup><sup>[[3]](#note-3)</sup>

1. <a name="ref-2"></a>Descreva o problema da forma mais clara e concisa possível, incluindo uma descrição do que aconteceu e deveria acontecer.

1. <a name="ref-3"></a>Inclua quaisquer rastreamentos de pilha relevantes, texto de mensagem de erro ou logs de falha (se você usar o recurso **relatar um problema** , eles poderão ser incluídos automaticamente). <sup>[quatro](#note-4)</sup>

1. <a name="ref-4"></a>Anote todas as mensagens de erro importantes que aparecem em anexos de captura de tela como texto sem formatação.

1. <a name="ref-5"></a>Inclua um caso de teste pequeno e independente que reproduza o bug com o mínimo de código possível.  Se você não puder reproduzir o problema com um projeto novo (criado usando um dos modelos internos), envie um projeto que demonstre o problema e anexe-o ao relatório de bugs.  Torne o projeto de exemplo o mais simples possível antes de anexá-lo. <sup>[[5]](#note-5)</sup><sup>[[6]](#note-6)</sup>

1. <a name="ref-6"></a>Descreva o ambiente em que o bug foi encontrado, incluindo o sistema operacional e [as versões do Xamarin](~/cross-platform/troubleshooting/questions/version-logs.md) e quaisquer dependências.

## <a name="additional-details"></a>Detalhes adicionais

1. <a name="note-1"></a>[*^*](#ref-1)Idealmente, a descrição dos "sintomas visíveis" deve incluir detalhes suficientes para que outros clientes possam confirmar se estão vendo o mesmo problema (as mesmas mensagens de erro, a mesma degradação de desempenho, o mesmo rastreamento de pilha de uma falha _etc._). Para "circunstâncias precisas", um bom exemplo seria se você disser algo como: "normalmente, atingi o problema de 75% do tempo, mas se eu alterar essa coisa, posso evitar o problema completamente". Outro exemplo semelhante de uma "circunstância precisa" é se o downgrade para uma versão anterior do Xamarin parar o problema.

1. <a name="note-2"></a>[*^*](#ref-2)Como você poderia esperar, os trechos de texto de erro (ou qualquer outro texto descritivo com exclusividade) geralmente são os melhores termos de pesquisa. Se o relatório de bugs existente estiver incompleto, você poderá adicionar detalhes ou arquivar um novo relatório de bugs melhor.

1. <a name="note-3"></a>[*^*](#ref-3)Outra boa pergunta é se o mesmo problema foi relatado para aplicativos Java, Objective-C ou Swift. Nesse caso, o problema é, provavelmente, parte do Android ou do iOS em vez de fazer parte do Xamarin.

1. <a name="note-4"></a>[*^*](#ref-4)Alguns exemplos de informações a serem incluídas:

    1. Para erros que ocorrem durante a compilação de um projeto, inclua a [saída](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output) completa do Build de diagnóstico no relatório de bugs.

    1. Para erros que ocorrem ao criar ou depurar um projeto do iOS do Visual Studio, execute **ajuda > Xamarin > logs zip** depois de acessar o erro e incluir o arquivo. zip resultante no relatório de bugs.

    1. Para exceções ou falhas em aplicativos Android ou iOS, inclua os logs de [depuração relevantes para aplicativos xamarin. Android e xamarin. Ios](~/cross-platform/troubleshooting/questions/version-logs.md#debug-logs-for-xamarin-apps).

1. <a name="note-5"></a>[*^*](#ref-5)Se possível para seu problema específico, uma opção é recriar o problema adicionando um pequeno número de arquivos de sua solução original em uma solução totalmente nova. A equipe do Xamarin muitas vezes será capaz de investigar problemas mesmo em casos de teste maiores (supondo que as etapas para reproduzir sejam explicadas claramente), mas casos de teste mais simples dão a melhor chance de que o bug seja resolvido rapidamente.

1. <a name="note-6"></a>[*^*](#ref-6)Se _não_ for possível reproduzir o problema adicionando um pequeno número de arquivos a uma solução totalmente nova, você poderá compactar e anexar a pasta da solução inteira para seu aplicativo completo. Exclua as `bin` `obj` pastas,, `Components` e `packages` para tornar o arquivo zip menor. (O IDE e o processo de compilação geralmente restauram ou recriam o conteúdo dessas pastas conforme necessário.) Você também pode excluir quantos arquivos de código e de recursos do projeto desejar, desde que a solução resultante ainda demonstre o problema original.
