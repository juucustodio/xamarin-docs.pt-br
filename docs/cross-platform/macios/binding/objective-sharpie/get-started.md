---
title: "Guia de Introdução"
ms.topic: article
ms.prod: xamarin
ms.assetid: 577512BF-1A90-41E5-89DE-9E056C478678
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 10/11/2017
ms.openlocfilehash: 01c390af08e59f3b10888a183df7fa6758c2609c
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="getting-started"></a>Guia de Introdução

<style type="text/css"> azul .terminal {cor: rgb(10,96,254);} .terminal verde {cor: rgb(12,156,26);} .terminal magenta {cor: rgb(152,12,103);} </style>


> [!IMPORTANT]
> **Aviso:** Sharpie objetivo é uma ferramenta para desenvolvedores do Xamarin experientes com conhecimento avançado de Objective-C (e, por extensão, C). Antes de tentar associar uma biblioteca Objective-C, você deve ter um conhecimento sólido de como criar a biblioteca nativa na linha de comando (e uma boa compreensão de como funciona a biblioteca nativa).

<a name="installing" />

# <a name="installing-objective-sharpie"></a>Instalando o objetivo Sharpie

Objetivo Sharpie atualmente é uma ferramenta de linha de comando autônoma para Mac OS X 10.10 e mais recente e é _não é um produto totalmente compatível com Xamarin_. Ele só deve ser usado por desenvolvedores avançados para ajudar a criar um projeto de associação para um 3ª parte biblioteca Objective-C.

Sharpie objetivo pode ser baixado como um instalador de pacote X do sistema operacional padrão.
Execute o instalador e siga todas as instruções na tela do Assistente de instalação:

- **Versão atual: 3.4**
  - [Baixe a versão mais recente](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie.pkg)
  - [Fórum de anúncio](https://forums.xamarin.com/discussion/104800/objective-sharpie-3-4)

> 💡 **Dica:** usar o `sharpie update` comando para atualizar para a versão mais recente.

# <a name="basic-walkthrough"></a>Instruções passo a passo básico

Objetivo Sharpie é uma ferramenta de linha de comando fornecido por Xamarin que ajuda a criar as definições de necessária para associar uma biblioteca de Objective-C 3ª parte para c#.
Mesmo ao usar Sharpie objetivo, o desenvolvedor *será* precisa modificar os arquivos gerados após a conclusão da Sharpie objetivo para solucionar problemas que não podem ser tratados automaticamente pela ferramenta.

Sempre que possível, o objetivo Sharpie será anotar APIs com que ele tem alguma dúvida sobre como vincular corretamente (muitas construções em código nativo são ambíguas).
Essas anotações aparecerá como [ `[Verify]` atributos](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md).

A saída do objetivo Sharpie é um par de arquivos - [ `ApiDefinition.cs` e `StructsAndEnums.cs` ](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) -que pode ser usado para criar um projeto de associação que compila em uma biblioteca que você pode usar em aplicativos Xamarin.

> [!IMPORTANT]
> Objetivo Sharpie vem com um **principais** regra para o uso correto: você deve absolutamente passar os argumentos de linha de comando de compilador clang correto para garantir a análise adequada. Isso ocorre porque o Sharpie de objetivo de fase de análise é simplesmente uma ferramenta [implementados com relação a libtooling clang API](http://clang.llvm.org/docs/LibTooling.html).

Isso significa que o objetivo Sharpie tem todo o poder do Clang (o compilador C/objetivo-C/C++ que realmente compila a biblioteca nativa, que você deve vincular) e todo o seu conhecimento interno sobre os arquivos de cabeçalho para a associação.
Em vez de converter analisada [AST](http://en.wikipedia.org/wiki/Abstract_syntax_tree) para o código de objeto, objetivo Sharpie converte o AST para um c# associação "scaffold" adequado para a entrada para o `bmac` e `btouch` Xamarin ferramentas de associação.

Se objetivo Sharpie erro durante a análise, isso significa que clang com erros out durante sua análise fase tentando construir a AST e você precisa descobrir o motivo.

**NOVO!** tentativas de versão 3.0 abordar algumas dessa complexidade, dando suporte a projetos Xcode diretamente. Se uma biblioteca nativa de um projeto válido do Xcode objetivo Sharpie pode avaliar o projeto para um destino especificado e a configuração para deduzir os sinalizadores de compilador e arquivos de cabeçalho de entrada necessário.

Se nenhum projeto Xcode estiver disponível, você precisará estar mais familiarizados com o projeto ao deduzir os arquivos de cabeçalho de entrada corretos, caminhos de pesquisa do arquivo de cabeçalho e outros sinalizadores de compilador necessário. É importante observar que os sinalizadores de compilador usados para criar a biblioteca nativa são os mesmos que deve ser passado para o objetivo Sharpie. Este é um processo mais manual e que exigem um pouco de familiaridade com a compilação de código nativo na linha de comando com a cadeia de ferramentas Clang.

**NOVO!** versão 3.0 também apresenta uma ferramenta para facilmente associação [CocoaPods](https://cocoapods.org) por meio de `sharpie pod` comando.
Se estiver interessado na biblioteca está disponível como um CocoaPod, é recomendável que iniciar tentando associar CocoaPod com objetivo Sharpie (em vez de tentar associar diretamente em relação à fonte).