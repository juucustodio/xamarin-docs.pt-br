---
title: Associação do Objective-C
description: Este documento fornece links para vários guias que descrevem como criar C# associações a código Objective-C, permitindo que os desenvolvedores consumir bibliotecas prontas para uso em aplicativos Xamarin.
ms.prod: xamarin
ms.assetid: DBBAA086-BB0F-8161-DF44-632F4F5DFE5D
author: asb3993
ms.author: amburns
ms.date: 01/25/2016
ms.openlocfilehash: 923ec41443feff67d851a0c2f3cba1b948aed580
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864410"
---
# <a name="binding-objective-c"></a>Associação do Objective-C

Esta seção inclui uma variedade de documentos que abordam as associações de criação para bibliotecas de Objective-C, para que eles podem ser chamados de C# aplicativos criados com xamarin. IOS ou xamarin. Mac.

## <a name="overviewcross-platformmaciosbindingoverviewmd"></a>[Visão geral](~/cross-platform/macios/binding/overview.md)

Este documento contém alguns dos recursos internos do como uma associação ocorre. Ele é um documento avançado com algumas informações técnicas.

## <a name="binding-objective-c-librariescross-platformmaciosbindingobjective-c-librariesmd"></a>[Associação de bibliotecas de Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)

Este documento descreve o processo usado para criar C# associações de APIs de Objective-C e como as linguagens em Objective-C são mapeadas para as expressões usadas no .NET.
Se você estiver associando apenas as APIs de C, você deve usar o mecanismo do .NET standard para isso, a estrutura de P/Invoke.

## <a name="binding-definition-reference-guidecross-platformmaciosbindingbinding-types-referencemd"></a>[Guia de referência de definição de associação](~/cross-platform/macios/binding/binding-types-reference.md)

Este é o guia de referência que descreve todos os atributos disponíveis para autores de associação para orientar o processo de geração de associação.


## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

Objetivo Sharpie é uma ferramenta de linha de comando para ajudar a inicializar a primeira passagem de uma associação. Ele funciona analisando os arquivos de cabeçalho de uma biblioteca nativa para mapear a API pública para o [definição de associação](~/cross-platform/macios/binding/objective-c-libraries.md) (um processo também pode ser feito manualmente).

## <a name="ios"></a>iOS

O [página de associação do iOS](~/ios/platform/binding-objective-c/index.md) vincula de volta a esses recursos comuns de associação, além para os exemplos a seguir.

### <a name="walkthrough-binding-an-objective-c-libraryiosplatformbinding-objective-cwalkthroughmd"></a>[Passo a passo: Associação de uma biblioteca Objective-C](~/ios/platform/binding-objective-c/walkthrough.md)

Este artigo fornece uma explicação passo a passo de criação de um projeto de associação usando o código-fonte aberto [InfColorPicker](https://github.com/InfinitApps/InfColorPicker) projeto Objective-C como exemplo. A biblioteca de InfColorPicker fornece um controlador de exibição reutilizáveis que permitem ao usuário selecionar uma cor com base em sua representação HSB, tornando mais fácil de usar a seleção de cor. Objetivo Sharpie será usado para auxiliar no processo de associação.

### <a name="binding-sampleshttpsgithubcommonomonotouch-bindings"></a>[Exemplos de associação](https://github.com/mono/monotouch-bindings)

Uma coleção de associações de terceiros que pode ser usado como referência ao criar novos projetos de associação.

## <a name="mac"></a>Mac

Historicamente [associação de Mac](~/mac/platform/binding.md) tem sido um processo muito manual. Atualmente, há uma [baixável visualização](https://forums.xamarin.com/discussion/59760/xamarin-mac-binding-project-preview) do suporte a projetos de associação do Mac para uma versão futura do Visual Studio para Mac.

## <a name="related-links"></a>Links relacionados

- [iOS de associação](~/ios/platform/binding-objective-c/index.md)
- [Mac Binding](~/mac/platform/binding.md)
