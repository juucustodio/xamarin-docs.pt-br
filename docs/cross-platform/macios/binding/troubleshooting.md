---
title: Solução de problemas de associação
description: Este guia descreve o que fazer se você tiver dificuldade para associar uma biblioteca Objective-C. Em particular, ele aborda associações ausentes, exceções de argumento ao passar NULL para uma associação e relatar bugs.
ms.prod: xamarin
ms.assetid: 7C65A55C-71FA-46C5-A1B4-955B82559844
author: davidortinau
ms.author: daortin
ms.date: 10/19/2016
ms.openlocfilehash: fe4b9ade9e6e462c3472a8bb3bb8750ed6cac326
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73015886"
---
# <a name="binding-troubleshooting"></a>Solução de problemas de associação

Algumas dicas para solucionar problemas de associações a APIs macOS (anteriormente conhecidas como OS X) no Xamarin. Mac.

## <a name="missing-bindings"></a>Associações ausentes

Embora o Xamarin. Mac cubra grande parte das APIs da Apple, às vezes, talvez seja necessário chamar alguma API da Apple que ainda não tenha uma associação. Em outros casos, você precisa chamar o C/Objective de terceiros que está fora do escopo das associações do Xamarin. Mac.

Se você estiver lidando com uma API da Apple, a primeira etapa é permitir que o Xamarin saiba que você está atingindo uma seção da API que ainda não temos cobertura. [Arquivo um bug](#reporting-bugs) indicando a API ausente. Usamos relatórios de clientes para priorizar quais APIs trabalhamos em seguida. Além disso, se você tiver uma licença comercial ou empresarial e essa falta de uma associação estiver bloqueando seu progresso, também siga as instruções em [suporte](https://visualstudio.microsoft.com/vs/support/) para arquivar um tíquete. Não podemos prometer uma ligação, mas, em alguns casos, podemos obter uma solução alternativa.

Depois de notificar o Xamarin (se aplicável) de sua associação ausente, a próxima etapa é considerar sua associação por conta própria. Temos um guia completo [aqui](~/cross-platform/macios/binding/overview.md) e algumas documentações não oficiais [aqui](https://brendanzagaeski.appspot.com/xamarin/0002.html) para encapsular associações de Objective-C manualmente. Se você estiver chamando uma C API, você pode usar C#o mecanismo P/Invoke, a documentação está [aqui](https://www.mono-project.com/docs/advanced/pinvoke/).

Se você decidir trabalhar na associação por conta própria, lembre-se de que os erros na associação podem produzir todos os tipos de falhas interessantes no tempo de execução nativo. Em particular, seja muito cuidadoso que sua assinatura no C# corresponda à assinatura nativa em número de argumentos e ao tamanho de cada argumento. Não fazer isso pode corromper a memória e/ou a pilha, e você pode falhar imediatamente ou em algum ponto arbitrário nos dados futuros ou corrompidos.

## <a name="argument-exceptions-when-passing-null-to-a-binding"></a>Exceções de argumento ao passar NULL para uma associação

Embora o Xamarin funcione para fornecer associações de alta qualidade e bem testadas para as APIs da Apple, às vezes erros e erros são guiados no. De longe, o problema mais comum que você pode encontrar é uma API que gera `ArgumentNullException` quando você passa NULL quando a API subjacente aceita `nil`. Os arquivos de cabeçalho nativos que definem a API geralmente não fornecem informações suficientes nas quais as APIs aceitam nil e que falharão se você passá-la.

Se você se deparar com um caso em que a passagem de `null` gera um `ArgumentNullException` mas você acredita que ele deve funcionar, siga estas etapas:

1. Verifique a documentação e/ou exemplos da Apple para ver se você pode encontrar uma prova de que ela aceita `nil`. Se você estiver familiarizado com o Objective-C, poderá escrever um pequeno programa de teste para verificá-lo.
2. [Arquivo um bug](#reporting-bugs).
3. Você pode contornar o bug? Se você puder evitar chamar a API com `null`, uma verificação nula simples em volta das chamadas pode ser um trabalho fácil.
4. No entanto, algumas APIs exigem a passagem de NULL para desativar ou desabilitar alguns recursos. Nesses casos, você pode contornar o problema ao colocar o navegador de assembly (consulte [encontrando o C# membro para um determinado seletor](~/mac/app-fundamentals/mac-apis.md#finding_selector)), copiando a associação e removendo a verificação nula. Certifique-se de arquivar um bug (etapa 2) se você fizer isso, pois sua associação copiada não receberá atualizações e correções que fazemos no Xamarin. Mac, e isso deve ser considerado um trabalho a curto prazo.

<a name="reporting-bugs"/>

## <a name="reporting-bugs"></a>Relatando bugs

Seus comentários são importantes para nós. Se você encontrar problemas com o Xamarin. Mac:

- Verifique os [Fóruns do Xamarin.Mac](https://forums.xamarin.com/categories/mac)
- Pesquise o [repositório de problemas](https://github.com/xamarin/xamarin-macios/issues) 
- Antes de mudar para problemas do GitHub, os problemas do Xamarin eram rastreados no [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi). Procure lá por problemas correspondentes.
- Se você não encontrar um problema correspondente, envie um novo problema no [repositório de problemas do GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

Os problemas do GitHub são todos públicos. Não é possível ocultar comentários ou anexos. 

Inclua tanto do seguinte quanto possível:

- Um exemplo simples reproduzindo o problema. Isso é **inestimável**, quando possível. 
- O rastreamento de pilha completo da falha.
- O código C# ao redor da falha.
