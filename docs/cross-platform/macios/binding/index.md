---
title: "Associação Objective-C"
ms.topic: article
ms.prod: xamarin
ms.assetid: DBBAA086-BB0F-8161-DF44-632F4F5DFE5D
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 01/25/2016
ms.openlocfilehash: e836081d79d904e2d0952386e536eefdabe361e1
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="binding-objective-c"></a>Associação Objective-C

Esta seção inclui uma variedade de documentos que abranger Criando associações para bibliotecas Objective-C, portanto, eles podem ser chamados de aplicativos em c# criados com o xamarin ou Xamarin.Mac.

##  <a name="overviewcross-platformmaciosbindingoverviewmd"></a>[Visão geral](~/cross-platform/macios/binding/overview.md)

Este documento contém alguns dos recursos internos do como uma associação ocorre. É um documento avançado com algumas informações técnicas.

##  <a name="binding-objective-c-librariescross-platformmaciosbindingobjective-c-librariesmd"></a>[Associação de bibliotecas Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)

Este documento descreve o processo usado para criar c# associações de APIs de Objective-C e como os idiomas em Objective-C são mapeados para os idiomas usados em .NET.
Se você estiver associando apenas as APIs de C, você deve usar o mecanismo padrão do .NET para isso, a estrutura de P/Invoke.

##  <a name="binding-definition-reference-guidecross-platformmaciosbindingbinding-types-referencemd"></a>[Guia de referência de definição de associação](~/cross-platform/macios/binding/binding-types-reference.md)

Este é o guia de referência que descreve todos os atributos disponíveis para autores de associação para orientar o processo de geração de associação.


## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[Objetivo Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

Objetivo Sharpie é uma ferramenta de linha de comando para inicializar a primeira passagem de uma associação. Funciona analisando os arquivos de cabeçalho de uma biblioteca nativa para mapear a API pública para o [associação definição](~/cross-platform/macios/binding/objective-c-libraries.md) (um processo que também pode ser feito manualmente).

## <a name="ios"></a>iOS

O [página de associação do iOS](~/ios/platform/binding-objective-c/index.md) vinculado de volta para esses recursos comuns de associação, além para os exemplos a seguir.

### <a name="walkthrough-binding-an-objective-c-libraryiosplatformbinding-objective-cwalkthroughmd"></a>[Passo a passo: Associando uma biblioteca Objective-C](~/ios/platform/binding-objective-c/walkthrough.md)

Este artigo fornece instruções passo a passo de criação de um projeto de associação usando o código-fonte aberto [InfColorPicker](https://github.com/InfinitApps/InfColorPicker) projeto Objective-C como um exemplo. A biblioteca de InfColorPicker fornece um controlador de exibição reutilizáveis que permitem que o usuário selecione uma cor com base em sua representação HSB, fazer a seleção de cor mais amigável. Objetivo Sharpie será usado para ajudar no processo de associação.

### <a name="binding-sampleshttpsgithubcommonomonotouch-bindings"></a>[Exemplos de associação](https://github.com/mono/monotouch-bindings)

Uma coleção de associações de terceiros que pode ser usado uma referência ao criar novos projetos de associação.

## <a name="mac"></a>Mac

Historicamente [associação Mac](~/mac/platform/binding.md) foi processos manuais. Atualmente, há um [visualização baixável](https://forums.xamarin.com/discussion/59760/xamarin-mac-binding-project-preview) de suporte de projeto de vinculação de Mac para uma versão futura do Visual Studio para Mac.



## <a name="related-links"></a>Links relacionados

- [iOS associação](~/ios/platform/binding-objective-c/index.md)
- [Associação de Mac](~/mac/platform/binding.md)
