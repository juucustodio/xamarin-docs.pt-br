---
title: "Associação de solução de problemas"
description: "Este guia descreve o que fazer se você tiver dificuldade para associação a uma biblioteca Objective-C."
ms.topic: article
ms.prod: xamarin
ms.assetid: 7C65A55C-71FA-46C5-A1B4-955B82559844
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 10/19/2016
ms.openlocfilehash: 2db7fe30f05224f6b74b4d2189606da59946bda0
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="binding-troubleshooting"></a>Associação de solução de problemas

Algumas dicas para solucionar problemas de associações para macOS (anteriormente conhecida como OS X) APIs em Xamarin.Mac.

## <a name="missing-bindings"></a>Associações ausentes

Enquanto Xamarin.Mac aborda grande parte das APIs Apple, às vezes, talvez seja necessário chamar alguns API Apple que não tem uma associação ainda. Em outros casos, você precisa chamar terceiros C/Objective-C que ele fora do escopo das associações Xamarin.Mac.

Se você estiver lidando com uma API da Apple, a primeira etapa é informar o Xamarin que está atingindo uma seção da API que não temos cobertura para ainda. [Registrar um bug](#reporting-bugs) observando a API ausente. Usamos os relatórios de clientes para priorizar quais APIs trabalhamos em próximo. Além disso, se você tiver uma licença corporativa e essa falta de uma associação está bloqueando o andamento, siga as instruções em [suporte](http://xamarin.com/support) para emitir um ticket. Não é possível prometemos uma associação, mas em alguns casos isso pode ser um trabalho ao redor.

Depois de notificar Xamarin (se aplicável) de sua associação ausente, a próxima etapa é considerar a associação por conta própria. Temos um guia completo [aqui](~/cross-platform/macios/binding/overview.md) e alguns documentos não oficiais [aqui](http://brendanzagaeski.appspot.com/xamarin/0002.html) para quebra automática Objective-C associações manualmente. Se você estiver chamando uma API de C, você pode usar o mecanismo do # de P/Invoke, documentação é [aqui](http://www.mono-project.com/docs/advanced/pinvoke/).

Se você optar por trabalhar na associação, lembre-se que erros na associação podem produzir todos os tipos de falhas interessantes no tempo de execução nativo. Em particular, você deve ter muito cuidado que sua assinatura no c# corresponde à assinatura nativo no número de argumentos e o tamanho de cada argumento. Falha ao fazer isso pode danificar a memória e/ou a pilha e você poderia falhar imediatamente ou em algum momento arbitrário no futuro ou dados corrompidos.

## <a name="argument-exceptions-when-passing-null-to-a-binding"></a>Exceções de argumentos ao passar nulo para uma associação

Enquanto trabalha Xamarin para fornecer alta qualidade e associações bem testadas para as APIs da Apple, às vezes, erros e bugs slip em. Problema de longe mais comuns que você pode executar em é uma API lançando `ArgumentNullException` ao passar nulo quando a API subjacente aceita `nil`. Os arquivos de cabeçalho nativo definindo a API geralmente não fornecem informações suficientes no qual APIs aceitam nil e qual falhará se você passá-lo no.

Se você tiver um caso onde passando `null` lança um `ArgumentNullException` , mas você acha que ele deve funcionar, siga estas etapas:

1. Verifique a documentação da Apple e/ou exemplos para ver se você pode encontrar uma prova que ele aceite `nil`. Se você estiver familiarizado com Objective-C, você pode escrever um programa de teste pequena para verificá-lo.
2. [Registrar um bug](#reporting-bugs).
3. Você pode solucionar o erro? Se você pode evitar a chamar a API com `null`, uma verificação de nula simple em torno de chamadas pode ser uma solução fácil.
4. No entanto, algumas APIs exigem passar null para desativar ou desabilitar alguns recursos. Nesses casos, você pode contornar o problema fazendo com que o navegador de assembly (consulte [localizando o membro c# de seletor determinado](~/mac/app-fundamentals/mac-apis.md#finding_selector)), copiando a associação e a remoção da verificação de nulos. Certifique-se de registrar um bug (etapa 2) se você fizer isso, pois a associação copiada não receberá atualizações e correções que fazemos em Xamarin.Mac e isso deve ser considerado uma solução curto prazo.

<a name="reporting-bugs"/>

## <a name="reporting-bugs"></a>Relatório de bugs

Seus comentários são importantes para nós. Se você encontrar problemas com Xamarin.Mac:

- Verifique o [Xamarin.Mac fóruns](https://forums.xamarin.com/categories/mac)
- Pesquisa o [repositório do problema](https://github.com/xamarin/xamarin-macios/issues) 
- Antes de alternar para problemas do GitHub, Xamarin problemas foram controlados na [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi). Procure lá para correspondência de problemas.
- Se você não encontrar um problema de correspondência, envie um novo problema de [repositório de problema do GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

Problemas do GitHub são todos os públicos. Não é possível ocultar comentários ou anexos. 

Inclua tanto o seguinte como possíveis:

- Um exemplo simple de reproduzir o problema. Isso é **inestimável** sempre que possível. 
- O rastreamento de pilha completa da falha.
- O código c# ao redor da falha. 
