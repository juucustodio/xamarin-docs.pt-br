---
title: Introdução com nitidez objetiva
description: Este documento fornece uma visão geral de alto nível da nitidez do objetivo, a ferramenta usada para automatizar a criação de associações em C# para o código Objective-C.
ms.prod: xamarin
ms.assetid: 577512BF-1A90-41E5-89DE-9E056C478678
author: davidortinau
ms.author: daortin
ms.date: 10/11/2017
ms.openlocfilehash: 5bee8df72290cab3ed6d5c23fef6c2ae2f1a2559
ms.sourcegitcommit: d1980b2251999224e71c1289e4b4097595b7e261
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2020
ms.locfileid: "92928510"
---
# <a name="getting-started-with-objective-sharpie"></a>Introdução com nitidez objetiva

> [!IMPORTANT]
> A nitidez objetiva é uma ferramenta para desenvolvedores experientes do Xamarin com conhecimento avançado de Objective-C (e por extensão, C). Antes de tentar associar uma biblioteca Objective-C, você deve ter um conhecimento sólido de como criar a biblioteca nativa na linha de comando (e uma boa compreensão de como a biblioteca nativa funciona).

<a name="installing"></a>

## <a name="installing-objective-sharpie"></a>Instalando a nitidez do objetivo

A nitidez objetiva é atualmente uma ferramenta de linha de comando autônoma para o Mac OS X 10,10 e mais recente e _não é um produto Xamarin com suporte completo_ . Ele só deve ser usado por desenvolvedores avançados para auxiliar na criação de um projeto de associação a uma biblioteca de Objective-C de terceiros.

A nitidez do objetivo pode ser baixada como um instalador de pacote do OS X padrão.
Execute o instalador e siga todos os prompts na tela do assistente de instalação:

- **Versão atual: 3,4**
  - [Baixar a versão mais recente](https://aka.ms/objective-sharpie)
  - [Comunicado do fórum](https://forums.xamarin.com/discussion/104800/objective-sharpie-3-4)

> [!TIP]
> Use o `sharpie update` comando para atualizar para a versão mais recente.

## <a name="basic-walkthrough"></a>Explicação básica

A Sharpde objetiva é uma ferramenta de linha de comando fornecida pelo Xamarin que auxilia na criação das definições necessárias para associar uma biblioteca de Objective-C de terceiros ao C#.
Mesmo ao usar a nitidez do objetivo, o *desenvolvedor* precisará modificar os arquivos gerados após a conclusão do objetivo para resolver os problemas que não puderam ser manipulados automaticamente pela ferramenta.

Sempre que possível, a nitidez do objetivo anotará as APIs com as quais ele tem alguma dúvida sobre como ligar corretamente (muitas construções no código nativo são ambíguas).
Essas anotações serão exibidas como [ `[Verify]` atributos](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md).

A saída da nitidez do objetivo é um par de arquivos- [ `ApiDefinition.cs` e `StructsAndEnums.cs` ](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) -que pode ser usado para criar um projeto de associação que é compilado em uma biblioteca que você pode usar em aplicativos Xamarin.

> [!IMPORTANT]
> A nitidez do objetivo vem com uma regra **principal** para uso adequado: você deve fazer com que seja absolutamente necessário passar os argumentos de linha de comando do compilador Clang corretos para garantir a análise apropriada. Isso ocorre porque a fase de análise de nitidez objetiva é simplesmente uma ferramenta [implementada em relação à API Clang libtooling](https://clang.llvm.org/docs/LibTooling.html).

Isso significa que a nitidez objetiva tem todo o poder do Clang (o compilador C/Objecte-C/C++ que realmente compila a biblioteca nativa que você vincularia) e todo o seu conhecimento interno dos arquivos de cabeçalho para associação.
Em vez de traduzir a [AST](https://en.wikipedia.org/wiki/Abstract_syntax_tree) analisada para o código de objeto, a nitidez objetiva converte a AST em uma associação C# "Scaffold", adequada para entrada para as `bmac` ferramentas de associação do e `btouch` Xamarin.

Se os erros de nitidez objetivarem durante a análise, isso significa que o Clang erro durante sua fase de análise tentando construir a AST, e você precisa descobrir o porquê.

**Novo!** as tentativas da versão 3,0 abordam algumas dessas complexidades dando suporte diretamente a projetos do Xcode. Se uma biblioteca nativa tiver um projeto Xcode válido, a nitidez do objetivo poderá avaliar o projeto para um destino e uma configuração especificados para deduzir os arquivos de cabeçalho de entrada necessários e os sinalizadores do compilador.

Se nenhum projeto do Xcode estiver disponível, você precisará estar mais familiarizado com o projeto deduzindondo os arquivos de cabeçalho de entrada corretos, os caminhos de pesquisa de arquivo de cabeçalho e outros sinalizadores de compilador necessários. É importante perceber que os sinalizadores do compilador usados para criar a biblioteca nativa são os mesmos que devem ser passados para a nitidez objetiva. Esse é um processo mais manual e um que exige um pouco de familiaridade com a compilação de código nativo na linha de comando com o Clang ferramentas.

**Novo!** a versão 3,0 também apresenta uma ferramenta para associar facilmente [CocoaPods](https://cocoapods.org) por meio do `sharpie pod` comando.
Se a biblioteca em que você está interessado estiver disponível como um CocoaPod, recomendamos que você comece tentando associar o CocoaPod à nitidez do objetivo (em vez de tentar associar-se diretamente à fonte).
