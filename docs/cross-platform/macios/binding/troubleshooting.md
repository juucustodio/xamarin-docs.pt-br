---
title: Solução de problemas de associação
description: Este guia descreve o que fazer se você tiver dificuldade para associação a uma biblioteca Objective-C. Em particular, ele aborda a associações ausentes, exceções de argumento ao passar nulo para uma associação e relatar bugs.
ms.prod: xamarin
ms.assetid: 7C65A55C-71FA-46C5-A1B4-955B82559844
author: asb3993
ms.author: amburns
ms.date: 10/19/2016
ms.openlocfilehash: fcdd712313becd1335479013f44886086dde7bff
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61261228"
---
# <a name="binding-troubleshooting"></a>Solução de problemas de associação

Algumas dicas para solução de problemas de associações para macOS (anteriormente conhecido como o OS X) APIs no xamarin. Mac.

## <a name="missing-bindings"></a>Associações ausentes

Enquanto o xamarin. Mac cobre grande parte das APIs da Apple, às vezes, talvez você precise chamar alguma API da Apple que não tem uma associação ainda. Em outros casos, você precisará chamar C/Objective-C de terceiros que ele fora do escopo das associações do xamarin. Mac.

Se você estiver lidando com uma API da Apple, a primeira etapa é informar Xamarin que você está atingindo uma seção da API que não há cobertura para ainda. [Registre um bug](#reporting-bugs) observar a API ausente. Podemos usar relatórios de clientes para priorizar quais APIs trabalhamos na próxima. Além disso, se você tiver uma licença corporativa e essa falta de uma associação está bloqueando o seu progresso, também siga as instruções em [suporte](http://xamarin.com/support) para emitir um ticket. Não podemos prometer uma associação, mas em alguns casos isso pode ser um trabalho ao redor.

Depois de notificar Xamarin (se aplicável) de sua associação ausente, a próxima etapa é considerar a associá-lo. Temos um guia completo [aqui](~/cross-platform/macios/binding/overview.md) e alguma documentação não oficial [aqui](http://brendanzagaeski.appspot.com/xamarin/0002.html) para quebra automática de associações do Objective-C manualmente. Se você estiver chamando uma API de C, você pode usar C#do mecanismo P/Invoke, a documentação está [aqui](https://www.mono-project.com/docs/advanced/pinvoke/).

Se você optar por trabalhar na associação por conta própria, esteja ciente de que os erros na associação podem produzir todos os tipos de falhas interessantes no tempo de execução nativo. Em particular, tenha muito cuidado que sua assinatura no C# corresponde à assinatura nativa no número de argumentos e o tamanho de cada argumento. Falha ao fazer isso pode corromper a memória e/ou a pilha e você poderia falhar imediatamente ou em algum ponto arbitrário no futuro ou dados corrompidos.

## <a name="argument-exceptions-when-passing-null-to-a-binding"></a>Exceções de argumento ao passar nulo para uma associação

Enquanto o Xamarin funciona para fornecer alta qualidade e bem testadas ligações para as APIs da Apple, às vezes, erros e bugs de guia no. De longe o mais provável que você pode encontrar é uma API lançando `ArgumentNullException` quando você passa nulo quando a API básica aceita `nil`. Os arquivos de cabeçalho nativo definindo a API geralmente não fornecem informações suficientes nos quais APIs aceitam nulo e que falhará se você passá-la.

Se você executar em um caso onde passando `null` lança um `ArgumentNullException` mas acho que ele deve funcionar, siga estas etapas:

1. Verifique a documentação da Apple e/ou exemplos para ver se você pode encontrar à prova de que ele aceite `nil`. Se você estiver familiarizado com Objective-C, você pode escrever um programa pequeno teste para verificá-lo.
2. [Registre um bug](#reporting-bugs).
3. Você pode solucionar o erro? Se você pode evitar chamar a API com `null`, uma verificação de nulos simple em torno de chamadas pode ser uma solução fácil alternativa.
4. No entanto, algumas APIs requerem a passagem de null para desativar ou desabilitar alguns recursos. Nesses casos, você pode contornar o problema, colocando o navegador de assembly (consulte [localizando o C# membro para um determinado seletor](~/mac/app-fundamentals/mac-apis.md#finding_selector)), copiando a associação e removendo a verificação de nula. Certifique-se de arquivar um bug (etapa 2), se você fizer isso, conforme sua associação copiada não receberão atualizações e correções que fazemos no xamarin. Mac, e isso deve ser considerado um trabalho de curto prazo em torno do.

<a name="reporting-bugs"/>

## <a name="reporting-bugs"></a>Relatório de bugs

Seus comentários são importantes para nós. Se você encontrar quaisquer problemas com o xamarin. Mac:

- Verifique os [Fóruns do Xamarin.Mac](https://forums.xamarin.com/categories/mac)
- Pesquise o [repositório de problemas](https://github.com/xamarin/xamarin-macios/issues) 
- Antes de mudar para problemas do GitHub, os problemas do Xamarin eram rastreados no [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi). Procure lá por problemas correspondentes.
- Se você não encontrar um problema correspondente, envie um novo problema no [repositório de problemas do GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

Os problemas do GitHub são todos públicos. Não é possível ocultar comentários ou anexos. 

Inclua tanto do seguinte quanto possível:

- Um exemplo simples reproduzindo o problema. Isso é **inestimável**, quando possível. 
- O rastreamento de pilha completo da falha.
- O código C# ao redor da falha. 

## <a name="related-links"></a>Links relacionados

- [Xamarin University curso: Compilando uma biblioteca de associações do Objective-C](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Xamarin University curso: Criar uma biblioteca de associações do Objective-C com objetivo Sharpie](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)
