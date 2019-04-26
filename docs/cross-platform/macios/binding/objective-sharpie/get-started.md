---
title: Introdução ao objetivo Sharpie
description: Este documento fornece uma visão geral do objetivo Sharpie, a ferramenta usada para automatizar a criação de C# associações a código Objective-C.
ms.prod: xamarin
ms.assetid: 577512BF-1A90-41E5-89DE-9E056C478678
author: asb3993
ms.author: amburns
ms.date: 10/11/2017
ms.openlocfilehash: c1831467ca0cbb4329a1e77fb355698f2d16cd6a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61199774"
---
# <a name="getting-started-with-objective-sharpie"></a>Introdução ao objetivo Sharpie

> [!IMPORTANT]
> Objetivo Sharpie é uma ferramenta para desenvolvedores experientes do Xamarin com conhecimento avançado de Objective-C (e, por extensão, C). Antes de tentar associar uma biblioteca Objective-C, você deve ter um conhecimento sólido sobre como criar a biblioteca nativa na linha de comando (e uma boa compreensão de como funciona a biblioteca nativa).

<a name="installing" />

## <a name="installing-objective-sharpie"></a>Instalando Sharpie objetivo

Objetivo Sharpie atualmente é uma ferramenta de linha de comando autônoma para o Mac OS X 10.10 e versões mais recentes e é _não é um produto Xamarin totalmente compatível com_. Ele só deve ser usado por desenvolvedores experientes para ajudá-lo na criação de um projeto de associação para uma 3ª biblioteca Objective-C de terceiros.

Objetivo Sharpie pode ser baixado como um instalador de pacote padrão dos X.
Execute o instalador e siga todos os prompts na tela do Assistente de instalação:

- **Versão atual: 3.4**
  - [Baixe a versão mais recente](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie.pkg)
  - [Comunicado de Fórum](https://forums.xamarin.com/discussion/104800/objective-sharpie-3-4)

> [!TIP]
> Use o `sharpie update` comando para atualizar para a versão mais recente.

## <a name="basic-walkthrough"></a>Instruções passo a passo básica

Objetivo Sharpie é uma ferramenta de linha de comando fornecida pelo Xamarin que ajuda a criar as definições necessárias para associar uma biblioteca de Objective-C de terceiros 3ª para C#.
Mesmo ao usar Sharpie objetivo, o desenvolvedor *serão* precisa modificar os arquivos gerados após a conclusão do objetivo Sharpie para solucionar problemas que não pôde ser tratados automaticamente pela ferramenta.

Sempre que possível, o objetivo Sharpie será anotar APIs com o qual ele tem alguma dúvida sobre como vincular corretamente (muitas construções em código nativo são ambíguas).
Essas anotações aparecerá como [ `[Verify]` atributos](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md).

A saída do objetivo Sharpie é um par de arquivos - [ `ApiDefinition.cs` e `StructsAndEnums.cs` ](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) -que pode ser usado para criar um projeto de associação que compila em uma biblioteca que você pode usar em aplicativos Xamarin.

> [!IMPORTANT]
> Objetivo Sharpie vem com uma **principais** regra para o uso correto: você absolutamente necessário transmitir a ela os argumentos de linha de comando do compilador clang corretos para garantir a análise adequada. Isso ocorre porque o Sharpie de objetivo de fase de análise é simplesmente uma ferramenta [implementados com relação o API do libtooling clang](http://clang.llvm.org/docs/LibTooling.html).

Isso significa que o objetivo Sharpie tem todo o poder do Clang (o compilador C/Objective-C/C++ que, na verdade, compila a biblioteca nativa, que você deve vincular) e todo o seu conhecimento interno dos arquivos de cabeçalho para a associação.
Em vez de converter o analisado [AST](https://en.wikipedia.org/wiki/Abstract_syntax_tree) para o código de objeto, objetivo Sharpie converte o AST para um C# associação "criar o scaffolding" adequado para a entrada para o `bmac` e `btouch` ferramentas de ligação do Xamarin.

Se objetivo Sharpie erros durante a análise, isso significa que clang apresentaram durante sua análise na fase de tentativa de construir a AST e você precisa descobrir o porquê.

**NOVO!** tentativas de versão 3.0 abordar algumas dessa complexidade, oferecendo suporte a projetos do Xcode diretamente. Se uma biblioteca nativa tem um projeto válido do Xcode, objetivo Sharpie pode avaliar o projeto para uma configuração para deduzir os arquivos de cabeçalho de entrada necessárias e os sinalizadores de compilador e de destino especificado.

Se nenhum projeto do Xcode estiver disponível, você precisará estar mais familiarizados com o projeto por deduzir os arquivos de cabeçalho de entrada corretos, caminhos de pesquisa do arquivo de cabeçalho e outros sinalizadores de compilador necessárias. É importante perceber que os sinalizadores de compilador usados para criar a biblioteca nativa são as mesmas que deve ser passado para o objetivo Sharpie. Isso é um processo mais manual e que exigem um pouco de familiaridade com a compilação de código nativo na linha de comando com a cadeia de ferramentas Clang.

**NOVO!** versão 3.0 também apresenta uma ferramenta para associar facilmente [CocoaPods](https://cocoapods.org) por meio de `sharpie pod` comando.
Se a biblioteca que você está interessado está disponível como um CocoaPod, recomendamos que você comece pela tentativa de associar o CocoaPod com objetivo Sharpie (em vez de tentar vincular com relação à fonte diretamente).

## <a name="related-links"></a>Links relacionados

- [Xamarin University curso: Compilando uma biblioteca de associações do Objective-C](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Xamarin University curso: Criar uma biblioteca de associações do Objective-C com objetivo Sharpie](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)