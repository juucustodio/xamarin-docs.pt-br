---
title: Criando associações com nitidez objetiva
description: Esta seção fornece uma introdução à nitidez objetiva, ferramenta de linha de comando do Xamarin usada para automatizar o processo de criação de uma associação a uma biblioteca Objective-C
ms.prod: xamarin
ms.assetid: 9C0A932C-7601-4357-B3F7-62ABAC835019
author: conceptdev
ms.author: crdun
ms.date: 10/11/2017
ms.openlocfilehash: d5b9fa1edc09b831dbc69ab092dfb5270942e67a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70765705"
---
# <a name="creating-bindings-with-objective-sharpie"></a>Criando associações com nitidez objetiva

_Esta seção fornece uma introdução à nitidez objetiva, ferramenta de linha de comando do Xamarin usada para automatizar o processo de criação de uma associação a uma biblioteca Objective-C_

- [Histórico](#history) &  [de visão geral](#overview)
- [Introdução](get-started.md)
- [Ferramentas e comandos](tools.md)
- [Recursos](platform/index.md)
- [Exemplos](examples/index.md)
- [Concluir instruções](~/ios/platform/binding-objective-c/walkthrough.md)
- [Histórico de versões](releases.md)

## <a name="overview"></a>Visão geral

A Sharpde objetiva é uma ferramenta de linha de comando para ajudar a inicializar a primeira passagem de uma associação.
Ele funciona analisando os arquivos de cabeçalho de uma biblioteca nativa para mapear a API pública para a [definição de associação](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file) (um processo que anteriormente era feito manualmente).

A nitidez do objetivo usa arquivos de cabeçalho de análise Clang, portanto, a associação é tão exata e completa quanto possível. Isso pode reduzir muito o tempo e o esforço necessário para produzir uma associação de qualidade.

> [!IMPORTANT]
> A nitidez objetiva é uma ferramenta para desenvolvedores experientes do Xamarin com conhecimento avançado de Objective-C (e por extensão, C). Antes de tentar associar uma biblioteca Objective-C, você deve ter um conhecimento sólido de como criar a biblioteca nativa na linha de comando (e uma boa compreensão de como a biblioteca nativa funciona).

## <a name="history"></a>Histórico

Estamos evoluindo e usando a nitidez do objetivo internamente no Xamarin nos últimos três anos. Como uma comprovação do poder da nitidez objetiva, as APIs introduzidas no Xamarin. iOS e no Xamarin. Mac desde o iOS 8, Mac OS X 10,10 e watchOS 2,0 foram inicializadas inteiramente com a nitidez objetiva. O Xamarin depende muito da nitidez objetiva internamente para criar seus próprios produtos.

No entanto, a nitidez objetiva é uma ferramenta muito avançada que requer conhecimento avançado de Objective-C e C, como usar o compilador Clang na linha de comando e, em geral, como as bibliotecas nativas são reunidas. Por causa dessa barra alta, achamos que ter um assistente de GUI define as expectativas erradas e, como tal, a nitidez objetiva está disponível atualmente apenas como uma ferramenta de linha de comando.

## <a name="related-links"></a>Links relacionados

- [Download de nitidez do objetivo](https://aka.ms/objective-sharpie)
- [Passo a passo: Associando uma biblioteca Objective-C](~/ios/platform/binding-objective-c/walkthrough.md)
- [Associação de bibliotecas de Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Detalhes da Associação](~/cross-platform/macios/binding/overview.md)
- [Guia de referência de tipos de associação](~/cross-platform/macios/binding/binding-types-reference.md)
- [Xamarin para desenvolvedores de Objective-C](~/ios/get-started/objective-c-developers/index.md)
