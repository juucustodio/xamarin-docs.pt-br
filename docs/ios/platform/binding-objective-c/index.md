---
title: Associação de bibliotecas do iOS
description: Como criar bibliotecas nativas do iOS (e CocoaPods) acessível em aplicativos Xamarin.
ms.prod: xamarin
ms.assetid: EBDC50DC-B44B-4003-AB2B-1EEB868A5E01
ms.technology: xamarin-ios
ms.custom: xamu-video
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 268900c7ab7b317b0b20f4c1ead2360fd6f9bbf0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="binding-ios-libraries"></a>Associação de bibliotecas do iOS

_Como criar bibliotecas nativas do iOS (e CocoaPods) acessível em aplicativos Xamarin._

Siga estes links para saber mais sobre associação de bibliotecas Objective-C e CocoaPods para xamarin e Xamarin.Mac:

- [**Visão geral de** ](~/cross-platform/macios/binding/overview.md) -
  descreve como funciona a associação.
- [**Associação de bibliotecas Objective-C** ](~/cross-platform/macios/binding/objective-c-libraries.md) -
  obter instruções sobre como associar a bibliotecas Objective-C para uso em projetos do Xamarin.
- [**Guia de referência de definição de tipo** ](~/cross-platform/macios/binding/binding-types-reference.md) -
  descreve todos os atributos disponíveis para autores de associação para orientar o processo de geração de associação.

## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

Objetivo Sharpie é uma ferramenta de linha de comando para inicializar a primeira passagem de uma associação.
Funciona analisando os arquivos de cabeçalho de uma biblioteca nativa para mapear a API pública para o [associação definição](~/cross-platform/macios/binding/objective-c-libraries.md) (um processo caso contrário, é feito manualmente). Objetivo Sharpie não cria uma associação por si só, mas isso pode ajudar você a começar!

Objetivo 3.0 de Sharpie introduziu a capacidade de associar Cocoapods diretamente!

## <a name="walkthrough---binding-an-ios-objective-c-librarywalkthroughmd"></a>[Instruções passo a passo - a associação de uma biblioteca de Objective-C do iOS](walkthrough.md)

Esta página fornece instruções passo a passo de criação de um projeto de vinculação do iOS usando o código-fonte aberto [ **InfColorPicker** ](https://github.com/InfinitApps/InfColorPicker) projeto Objective-C como um exemplo. O **InfColorPicker** biblioteca fornece um controlador de exibição reutilizáveis que permitem que o usuário selecione uma cor com base em sua representação HSB, fazer a seleção de cor mais amigável.
Objetivo Sharpie será usado para ajudar no processo de associação.

## <a name="xamarin-university-lightning-lecture"></a>Xamarin University raios palestra

> [!VIDEO https://youtube.com/embed/ZUoPLcmnf1o]

**iOS associações em C/C++, pelo [University Xamarin](https://university.xamarin.com/)**

## <a name="related-links"></a>Links relacionados

- [Associação do Objective-C](~/cross-platform/macios/binding/index.md)
- [Associação de Mac](~/mac/platform/binding.md)
