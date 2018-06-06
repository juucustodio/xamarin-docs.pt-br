---
title: Associação de bibliotecas do iOS
description: Este documento descreve como criar c# associações para o código Objective-C, tornando possível consumir bibliotecas nativas e CocoaPods em um aplicativo xamarin.
ms.prod: xamarin
ms.assetid: EBDC50DC-B44B-4003-AB2B-1EEB868A5E01
ms.technology: xamarin-ios
ms.custom: xamu-video
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: b054595568a34616a01f2c3f3c7d85f968c3f1fa
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787156"
---
# <a name="binding-ios-libraries"></a>Associação de bibliotecas do iOS

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
