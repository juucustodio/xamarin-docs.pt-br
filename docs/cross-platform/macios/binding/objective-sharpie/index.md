---
title: Criando associações com objetivo Sharpie
description: Esta seção fornece uma introdução ao objetivo Sharpie, ferramenta de linha de comando do Xamarin usada para automatizar o processo de criação de uma associação a uma biblioteca Objective-C
ms.prod: xamarin
ms.assetid: 9C0A932C-7601-4357-B3F7-62ABAC835019
author: asb3993
ms.author: amburns
ms.date: 10/11/2017
ms.openlocfilehash: cbe0be19e38892d06a79831d7974fbddcb71a9ac
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2019
ms.locfileid: "64978480"
---
# <a name="creating-bindings-with-objective-sharpie"></a>Criando associações com objetivo Sharpie

_Esta seção fornece uma introdução ao objetivo Sharpie, ferramenta de linha de comando do Xamarin usada para automatizar o processo de criação de uma associação a uma biblioteca Objective-C_

- [Visão geral](#overview) & [histórico](#history)
- [Introdução](get-started.md)
- [Ferramentas e comandos](tools.md)
- [Recursos](platform/index.md)
- [Exemplos](examples/index.md)
- [Passo a passo completo](~/ios/platform/binding-objective-c/walkthrough.md)
- [Histórico de versões](releases.md)

## <a name="overview"></a>Visão geral

Objetivo Sharpie é uma ferramenta de linha de comando para ajudar a inicializar a primeira passagem de uma associação.
Ele funciona analisando os arquivos de cabeçalho de uma biblioteca nativa para mapear a API pública para o [definição de associação](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file) (um processo que anteriormente era feito manualmente).

Objetivo Sharpie usa arquivos de cabeçalho de análise de Clang, portanto, a associação é tão exato e completo possível. Isso pode reduzir significativamente o tempo e esforço necessário para produzir uma associação de qualidade.

> [!IMPORTANT]
> Objetivo Sharpie é uma ferramenta para desenvolvedores experientes do Xamarin com conhecimento avançado de Objective-C (e, por extensão, C). Antes de tentar associar uma biblioteca Objective-C, você deve ter um conhecimento sólido sobre como criar a biblioteca nativa na linha de comando (e uma boa compreensão de como funciona a biblioteca nativa).

## <a name="history"></a>Histórico

Temos foi evoluindo e usando o objetivo Sharpie internamente no Xamarin nos últimos três anos. Como o poder do objetivo Sharpie prova, APIs introduzido no xamarin. IOS e xamarin. Mac desde o iOS 8, Mac OS X 10.10 e watchOS 2.0 foram ser inicializados inteiramente com objetivo Sharpie. Xamarin depende intensamente objetivo Sharpie internamente para a criação de seus próprios produtos.

No entanto, o objetivo Sharpie é uma ferramenta muito avançada que exige conhecimento avançado de Objective-C e C, como usar o compilador clang na linha de comando e bibliotecas nativas como geralmente são colocadas juntos. Devido a essa barra alta, percebemos que ter uma GUI assistente define as expectativas de errado e como tal, objetivo Sharpie está disponível apenas como uma ferramenta de linha de comando.

## <a name="related-links"></a>Links relacionados

- [Download do objetivo Sharpie](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie.pkg)
- [Passo a passo: Associação de uma biblioteca Objective-C](~/ios/platform/binding-objective-c/walkthrough.md)
- [Associação de bibliotecas de Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Detalhes da associação](~/cross-platform/macios/binding/overview.md)
- [Guia de referência de tipos de associação](~/cross-platform/macios/binding/binding-types-reference.md)
- [Xamarin para desenvolvedores de Objective-C](~/ios/get-started/objective-c-developers/index.md)

