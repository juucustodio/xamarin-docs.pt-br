---
title: Associação do Objective-C
description: Este documento fornece links para vários guias que descrevem como criar C# associações para código Objective-C, permitindo que os desenvolvedores consumam bibliotecas prontas para uso em aplicativos Xamarin.
ms.prod: xamarin
ms.assetid: DBBAA086-BB0F-8161-DF44-632F4F5DFE5D
author: davidortinau
ms.author: daortin
ms.date: 01/25/2016
ms.openlocfilehash: cc404f3995bd1f2940897f6cb84d57cea39ae44b
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016275"
---
# <a name="binding-objective-c"></a>Associação do Objective-C

Esta seção inclui uma variedade de documentos que abrangem a criação de associações para bibliotecas Objective-C, para que possam C# ser chamadas a partir de aplicativos criados com Xamarin. Ios ou Xamarin. Mac.

## <a name="overviewcross-platformmaciosbindingoverviewmd"></a>[Visão Geral](~/cross-platform/macios/binding/overview.md)

Este documento contém alguns dos elementos internos de como uma associação ocorre. É um documento avançado com algumas informações técnicas.

## <a name="binding-objective-c-librariescross-platformmaciosbindingobjective-c-librariesmd"></a>[Associação de bibliotecas de Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)

Este documento descreve o processo usado para criar C# associações de APIs Objective-c e como os idiomas em Objective-c são mapeados para os idiomas usados no .net.
Se você estiver associando apenas APIs C, deverá usar o mecanismo padrão do .NET para isso, a estrutura P/Invoke.

## <a name="binding-definition-reference-guidecross-platformmaciosbindingbinding-types-referencemd"></a>[Guia de referência de definição de associação](~/cross-platform/macios/binding/binding-types-reference.md)

Este é o guia de referência que descreve todos os atributos disponíveis para vincular autores para orientar o processo de geração de associação.

## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

A Sharpde objetiva é uma ferramenta de linha de comando para ajudar a inicializar a primeira passagem de uma associação. Ele funciona analisando os arquivos de cabeçalho de uma biblioteca nativa para mapear a API pública para a [definição de associação](~/cross-platform/macios/binding/objective-c-libraries.md) (um processo que também pode ser feito manualmente).

## <a name="ios"></a>iOS

A [página de associação do IOS](~/ios/platform/binding-objective-c/index.md) volta a esses recursos de associação comuns, além dos exemplos abaixo.

### <a name="walkthrough-binding-an-objective-c-libraryiosplatformbinding-objective-cwalkthroughmd"></a>[Walkthrough: associando uma biblioteca Objective-C](~/ios/platform/binding-objective-c/walkthrough.md)

Este artigo fornece uma explicação passo a passo de como criar um projeto de associação usando o projeto de [InfColorPicker](https://github.com/InfinitApps/InfColorPicker) de software livre Objective-C como um exemplo. A biblioteca InfColorPicker fornece um controlador de exibição reutilizável que permite ao usuário selecionar uma cor com base em sua representação HSB, tornando a seleção de cores mais amigável. A nitidez do objetivo será usada para auxiliar no processo de ligação.

### <a name="binding-sampleshttpsgithubcommonomonotouch-bindings"></a>[Exemplos de associação](https://github.com/mono/monotouch-bindings)

Uma coleção de associações de terceiros que podem ser usadas como referência ao criar novos projetos de associação.

## <a name="mac"></a>Mac

A [ligação de Mac](~/mac/platform/binding.md) historicamente tem sido um processo manual. Atualmente, há uma [Visualização baixável](https://forums.xamarin.com/discussion/59760/xamarin-mac-binding-project-preview) do suporte ao projeto de ligação Mac para uma versão futura do Visual Studio para Mac.

## <a name="related-links"></a>Links relacionados

- [Associação do iOS](~/ios/platform/binding-objective-c/index.md)
- [Associação de Mac](~/mac/platform/binding.md)
