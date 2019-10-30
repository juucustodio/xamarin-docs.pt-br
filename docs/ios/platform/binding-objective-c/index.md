---
title: Associando bibliotecas do iOS
description: Este documento descreve como criar C# associações para código Objective-C, possibilitando o consumo de bibliotecas nativas e CocoaPods em um aplicativo Xamarin. Ios.
ms.prod: xamarin
ms.assetid: EBDC50DC-B44B-4003-AB2B-1EEB868A5E01
ms.technology: xamarin-ios
ms.custom: xamu-video
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 61d1adfc997b34302f1f89f56653af906ca90135
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022219"
---
# <a name="binding-ios-libraries"></a>Associando bibliotecas do iOS

Siga estes links para saber mais sobre como associar bibliotecas Objective-C e CocoaPods para Xamarin. iOS e Xamarin. Mac:

- [**Visão geral**](~/cross-platform/macios/binding/overview.md) -
  descreve como funciona a associação.
- A [**Associação das bibliotecas Objective-c**](~/cross-platform/macios/binding/objective-c-libraries.md) -
  instruções sobre como associar bibliotecas de Objective-c para uso em projetos do Xamarin.
- [**Guia de referência de definição de tipo**](~/cross-platform/macios/binding/binding-types-reference.md) -
  descreve todos os atributos disponíveis para ligar autores para orientar o processo de geração de associação.

## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

A Sharpde objetiva é uma ferramenta de linha de comando para ajudar a inicializar a primeira passagem de uma associação.
Ele funciona analisando os arquivos de cabeçalho de uma biblioteca nativa para mapear a API pública para a [definição de associação](~/cross-platform/macios/binding/objective-c-libraries.md) (um processo que, de outra forma, é feito manualmente). A nitidez do objetivo não cria uma associação por si só, mas pode ajudá-lo a começar!

O objetivo de nitidez 3,0 introduziu a capacidade de ligar o Cocoapods diretamente!

## <a name="walkthrough---binding-an-ios-objective-c-librarywalkthroughmd"></a>[Passo-a-Binding de uma biblioteca de objetivo do iOS-C](walkthrough.md)

Esta página fornece uma explicação passo a passo de como criar um projeto de associação do iOS usando o projeto [**InfColorPicker**](https://github.com/InfinitApps/InfColorPicker) Objective de software livre-C como exemplo. A biblioteca **InfColorPicker** fornece um controlador de exibição reutilizável que permite ao usuário selecionar uma cor com base em sua representação HSB, tornando a seleção de cores mais amigável.
A nitidez do objetivo será usada para auxiliar no processo de ligação.

## <a name="video"></a>Vídeo

> [!VIDEO https://youtube.com/embed/ZUoPLcmnf1o]

**Associações do iOS em C/C++ vídeo**

## <a name="related-links"></a>Links relacionados

- [Associação do Objective-C](~/cross-platform/macios/binding/index.md)
- [Associação de Mac](~/mac/platform/binding.md)
