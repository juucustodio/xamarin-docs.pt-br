---
title: Quando e como posso registrar um relatório de bugs?
description: Este documento descreve quando, onde e como em um arquivo de um relatório de bugs. Ele também fornece as práticas recomendadas que permitem que os engenheiros a melhor diagnosticar o problema de relatório de bugs.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8AD9CFBF-282A-4C1F-95E9-25F21141B052
author: asb3993
ms.author: amburns
ms.date: 06/05/2018
ms.openlocfilehash: b70fe29a79e099f1141c1295d907b48afaa2c3c7
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351600"
---
# <a name="when-and-how-should-i-file-a-bug-report"></a>Quando e como posso registrar um relatório de bugs?

Registre bugs no Rastreador de bugs de Bugzilla do Xamarin aqui: [ https://bugzilla.xamarin.com/enter_bug.cgi?classification=__all ](https://bugzilla.xamarin.com/enter_bug.cgi?classification=__all).

## <a name="file-a-bug-if"></a>Registre um bug se...

Você tem um conjunto de etapas que você acha que os engenheiros do Xamarin poderão usar para reproduzir um problema causado pelo Xamarin.

OU

Cuidadosamente, você pode descrever os visíveis sintomas do problema, especialmente se você também pode descrever algumas circunstâncias precisas relacionadas ao problema. <sup> [[1]](#note-1)</sup>


## <a name="best-practices-to-help-xamarin-address-bugs-quickly-and-efficiently"></a>Práticas recomendadas para ajudar os bugs de endereço de Xamarin rapidez e eficiência


1. <a name="ref-1" />Pesquisa [Bugzilla](https://bugzilla.xamarin.com/query.cgi?format=specific&amp;bug_status=__all__) e da web existentes e sugestões de uso que podem resolver o problema diretamente ou relatórios de bugs.<sup> [[2]](#note-2)</sup><sup>[[3]](#note-3)</sup>

1. <a name="ref-2" />Siga as [diretrizes de gravação do bug](https://bugzilla.xamarin.com/page.cgi?id=bug-writing.html) para descrever o problema conforme clara e concisa possível, incluindo uma descrição do que aconteceu e foi prevista para acontecer.

1. <a name="ref-3" />Incluir qualquer rastreamentos de pilha relevantes, o texto da mensagem de erro ou os logs de falha. <sup>[[4]](#note-4)</sup>

1. <a name="ref-4" />Anote qualquer mensagem de erro importante que aparecem em anexos de captura de tela muito como texto sem formatação.

1. <a name="ref-5" />Inclua um caso de teste pequeno e independente que reproduza o bug com como o mínimo possível de código.  Se você não conseguir reproduzir o problema com um novo projeto (criado usando um dos modelos internos), em seguida, compacte um projeto que demonstra o problema e anexá-lo ao relatório de bugs.  Verifique o projeto de exemplo mais simples possível antes de anexá-lo. <sup> [[5]](#note-5)</sup><sup>[[6]](#note-6)</sup>

1. <a name="ref-6" />Descreva o ambiente em que o bug foi encontrado, incluindo o sistema operacional e [versões do Xamarin](~/cross-platform/troubleshooting/questions/version-logs.md) e todas as dependências.

---

## <a name="additional-details"></a>Detalhes adicionais

1. <a name="note-1" />[*^*](#ref-1) O ideal é que a descrição dos "Sintomas visíveis" deve incluir detalhes suficientes para que outros clientes podem confirmar se eles estão vendo o mesmo problema (mesmo mensagens de erro, mesmo degradação do desempenho, rastreamento de pilha mesmo em caso de falha, _etc._ ). Para "precisas circunstâncias", um bom exemplo seria se você pode dizer algo como: "normalmente eu atingir o problema 75% do tempo, mas se eu mudar este uma coisa, em seguida, eu posso evitar o problema completamente." Outro exemplo semelhante de "circunstância preciso" é a se fazer o downgrade para uma versão anterior do Xamarin para o problema.

1. <a name="note-2" />[*^*](#ref-2) Como você esperaria, trechos de texto de erro (ou qualquer outro texto descritivo exclusivamente) são geralmente melhores termos de pesquisa. Se o relatório de bug existente estiver incompleto, são bem-vindo ao adicionar detalhes ou arquivo em um novo, melhor relatório de bugs.

1. <a name="note-3" />[*^*](#ref-3) Outra boa pergunta é se o mesmo problema foi relatado para qualquer Java, Objective-C ou Swift aplicativos. Nesse caso, o problema é muito provável que parte do Android ou iOS em si em vez de parte do Xamarin.

1. <a name="note-4" />[*^*](#ref-4) Alguns exemplos de informações a serem incluídas:

    1. Para erros que ocorrem ao compilar um projeto, inclua o completo [saída do build diagnóstico](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output) no relatório de bugs.
    
    1. Para erros que ocorrem ao compilar ou depurar um projeto do iOS do Visual Studio, execute _Ajuda > Xamarin > Logs de Zip_ depois de atingir o erro e incluir o arquivo. zip resultante no relatório de bugs.
    
    1. Para exceções ou falhas em aplicativos do Android ou iOS, inclua o relevantes "[depurar logs para aplicativos xamarin. Android e xamarin. IOS](~/cross-platform/troubleshooting/questions/version-logs.md#debug-logs-for-xamarin-apps)."

1. <a name="note-5" />[*^*](#ref-5) Se possível para o seu problema específico, uma opção excelente é recriar o problema adicionando um pequeno número de arquivos de sua solução original em uma solução totalmente nova. A equipe do Xamarin geralmente será capaz de investigar os problemas, mesmo em casos de teste maiores (supondo que as etapas para reproduzir são explicadas claramente), mas oferecem mais simples de casos de teste que a melhor chance de que o bug sejam resolvido rapidamente.


1. <a name="note-6" />[*^*](#ref-6) Se ele estiver _não_ possível reproduzir o problema adicionando um pequeno número de arquivos para uma solução totalmente nova, em seguida, você pode compactar e anexar a pasta toda a solução para seu aplicativo completo. Exclua o `bin`, `obj`, `Components`, e `packages` pastas para tornar o zip de arquivos menores. (O IDE e o processo de compilação serão geralmente restaurar ou recriar o conteúdo dessas pastas conforme necessário.) Você também pode excluir quantos recursos e código de arquivos do projeto como desejar, desde que a solução resultante ainda demonstra o problema original.

