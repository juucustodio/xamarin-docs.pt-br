---
title: Associando bibliotecas do iOS
description: Este documento descreve como criar associações em C# para o código Objective-C, possibilitando o consumo de bibliotecas nativas e CocoaPods em um aplicativo Xamarin. iOS.
ms.prod: xamarin
ms.assetid: EBDC50DC-B44B-4003-AB2B-1EEB868A5E01
ms.technology: xamarin-ios
ms.custom: xamu-video
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 1e0342a41587b7479381ad763790227aba2ef414
ms.sourcegitcommit: a3f13a216fab4fc20a9adf343895b9d6a54634a5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85853092"
---
# <a name="binding-ios-libraries"></a>Associando bibliotecas do iOS

> [!IMPORTANT]
> No momento, estamos investigando o uso de associação personalizada na plataforma Xamarin. Faça [**esta pesquisa**](https://www.surveymonkey.com/r/KKBHNLT) para informar os futuros esforços de desenvolvimento.

Siga estes links para saber mais sobre como associar bibliotecas Objective-C e CocoaPods para Xamarin. iOS e Xamarin. Mac:

- [**Visão geral**](~/cross-platform/macios/binding/overview.md) -
   Descreve como funciona a associação.
- [**Associando bibliotecas**](~/cross-platform/macios/binding/objective-c-libraries.md) -
   de Objective-C Instruções sobre como associar bibliotecas Objective-C para uso em projetos do Xamarin.
- Guia de referência de [**definição de tipo**](~/cross-platform/macios/binding/binding-types-reference.md) -
   Descreve todos os atributos disponíveis para associar autores para orientar o processo de geração de associação.

## <a name="objective-sharpie"></a>[Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

A Sharpde objetiva é uma ferramenta de linha de comando para ajudar a inicializar a primeira passagem de uma associação.
Ele funciona analisando os arquivos de cabeçalho de uma biblioteca nativa para mapear a API pública para a [definição de associação](~/cross-platform/macios/binding/objective-c-libraries.md) (um processo que, de outra forma, é feito manualmente). A nitidez do objetivo não cria uma associação por si só, mas pode ajudá-lo a começar!

O objetivo de nitidez 3,0 introduziu a capacidade de ligar o Cocoapods diretamente!

## <a name="walkthrough---binding-an-ios-objective-c-library"></a>[Passo-a-Binding de uma biblioteca de objetivo do iOS-C](walkthrough.md)

Esta página fornece uma explicação passo a passo de como criar um projeto de associação do iOS usando o projeto [**InfColorPicker**](https://github.com/InfinitApps/InfColorPicker) Objective de software livre-C como exemplo. A biblioteca **InfColorPicker** fornece um controlador de exibição reutilizável que permite ao usuário selecionar uma cor com base em sua representação HSB, tornando a seleção de cores mais amigável.
A nitidez do objetivo será usada para auxiliar no processo de ligação.

## <a name="video"></a>Vídeo

> [!VIDEO https://youtube.com/embed/ZUoPLcmnf1o]

**Associações do iOS no vídeo C/C++**

## <a name="related-links"></a>Links Relacionados

- [Associação do Objective-C](~/cross-platform/macios/binding/index.md)
- [Associação de Mac](~/mac/platform/binding.md)
