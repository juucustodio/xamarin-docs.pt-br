---
title: Quando e como eu arquivo um relatório de erros?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8AD9CFBF-282A-4C1F-95E9-25F21141B052
author: asb3993
ms.author: amburns
ms.openlocfilehash: d5471195b5051725b41b8f28b3959db362297669
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2018
---
# <a name="when-and-how-should-i-file-a-bug-report"></a>Quando e como eu arquivo um relatório de erros?


Registre bugs no Rastreador de bugs de Bugzilla do Xamarin aqui: [ https://bugzilla.xamarin.com/enter_bug.cgi?classification=__all ](https://bugzilla.xamarin.com/enter_bug.cgi?classification=__all).

## <a name="file-a-bug-if"></a>Registrar um bug se...


Você tem um conjunto de etapas que você acha que os engenheiros Xamarin serão capazes de usar para reproduzir um problema causado por Xamarin.

OU

Com cuidado, você pode descrever os sintomas visíveis do problema, especialmente se você também pode descrever algumas circunstâncias precisas relacionadas ao problema. <sup> [[1]](#note-1)</sup>


## <a name="best-practices-to-help-xamarin-address-bugs-quickly-and-efficiently"></a>Práticas recomendadas para ajudar os bugs de endereço Xamarin rápida e eficiente


1. <a name="ref-1" />Pesquisa [Bugzilla](https://bugzilla.xamarin.com/query.cgi?format=specific&amp;bug_status=__all__) e web existentes sugestões de uso que podem resolver o problema diretamente ou relatórios de bugs.<sup> [[2]](#note-2)</sup><sup>[[3]](#note-3)</sup>

1. <a name="ref-2" />Siga o [bug diretrizes de gravação](https://bugzilla.xamarin.com/page.cgi?id=bug-writing.html) para descrever o problema como claramente e forma concisa possíveis, incluindo uma descrição do que ocorreu e foi prevista para acontecer.

1. <a name="ref-3" />Incluir qualquer rastreamentos de pilha relevantes, o texto da mensagem de erro ou logs de falha. <sup>[[4]](#note-4)</sup>

1. <a name="ref-4" />Escreva quaisquer mensagens de erro importantes que aparecem na captura de tela anexos muito como texto sem formatação.

1. <a name="ref-5" />Inclua um caso de teste pequeno e independente que reproduz o erro com como código pequeno quanto possível.  Se você não pode reproduzir o problema com um novo projeto (criado usando um dos modelos internos), depois, compacte um projeto que demonstra o problema e anexá-lo ao relatório de erro.  Verifique o projeto de exemplo mais simples possível antes de anexá-lo. <sup> [[5]](#note-5)</sup><sup>[[6]](#note-6)</sup>

1. <a name="ref-6" />Descreva o ambiente em que o erro foi encontrado, incluindo o sistema operacional e [versões do Xamarin](~/cross-platform/troubleshooting/questions/version-logs.md) e todas as dependências.

---

## <a name="additional-details"></a>Detalhes adicionais

1. <a name="note-1" />[*^*](#ref-1) O ideal é a descrição dos "Sintomas visíveis" deve incluir detalhes suficientes para que outros clientes podem confirmar se ele estiver vendo o mesmo problema (mesmo mensagens de erro, mesmo degradação de desempenho, rastreamento de pilha mesmo em caso de falha, _etc._ ). "Preciso circunstâncias", um bom exemplo seria se você pode dizer algo como: "normalmente acerto o problema 75% do tempo, mas se alterar este algo, em seguida, pode evitar o problema completamente". Outro exemplo semelhante de "preciso circunstância" é se o downgrade para uma versão anterior do Xamarin para o problema.

1. <a name="note-2" />[*^*](#ref-2) Como se esperaria, trechos de texto de erro (ou qualquer outro texto descritivo exclusivamente) são geralmente as melhores condições de pesquisa. Se o relatório de bug existente estiver incompleto, são bem-vindo ao adicionar detalhes ou arquivo em um novo, melhor relatório de bugs.

1. <a name="note-3" />[*^*](#ref-3) Outra boa pergunta é se o mesmo problema foi relatado para qualquer Java Objective-C ou aplicativos ágil. Nesse caso, o problema é muito provável que parte do Android ou iOS em si, em vez de parte do Xamarin.

1. <a name="note-4" />[*^*](#ref-4) Alguns exemplos de informações a serem incluídas:

    1. Para erros que ocorrem ao compilar um projeto, inclua o completo [saída da compilação diagnóstico](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output) no relatório de erro.
    
    1. Para erros que ocorrem ao compilar ou depurar um projeto do iOS do Visual Studio, execute _Ajuda > Xamarin > Logs Zip_ depois de atingir o erro e incluir o arquivo. zip resultante no relatório de erro.
    
    1. Para exceções ou falhas em aplicativos do Android ou iOS, inclua o relevantes "[depurar logs de aplicativos xamarin e xamarin](~/cross-platform/troubleshooting/questions/version-logs.md#debug-logs-for-xamarin-apps)."

1. <a name="note-5" />[*^*](#ref-5) Se possível para o seu problema específico, uma excelente opção é recriar o problema adicionando um pequeno número de arquivos de sua solução original em uma nova solução. A equipe de Xamarin geralmente poderá investigar problemas mesmo em casos de teste maior (supondo que as etapas para reproduzir são explicadas claramente), mas oferecem mais simples de casos de teste que a melhor chance de que o tipo de problema será resolvido rapidamente.


1. <a name="note-6" />[*^*](#ref-6) Se for _não_ possível reproduzir o problema adicionando um pequeno número de arquivos para uma nova solução, você pode compactar e anexar a pasta de solução completa para seu aplicativo completo. Exclua o `bin`, `obj`, `Components`, e `packages` pastas para fazer o zip de arquivo menor. (O IDE e o processo de compilação serão geralmente restaurar ou recriar o conteúdo dessas pastas conforme necessário.) Você também pode excluir muitos como código e arquivos de recursos do projeto como desejar, desde que a solução resultante demonstra ainda o problema original.

