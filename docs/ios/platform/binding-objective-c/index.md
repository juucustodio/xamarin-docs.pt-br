---
title: Associação de bibliotecas do iOS
description: Este documento descreve como criar C# associações a código Objective-C, tornando possível consumir bibliotecas nativas e CocoaPods em um aplicativo xamarin. IOS.
ms.prod: xamarin
ms.assetid: EBDC50DC-B44B-4003-AB2B-1EEB868A5E01
ms.technology: xamarin-ios
ms.custom: xamu-video
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 8c4dcbe0baf74479e94f8663280e7654b4d58a9d
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2019
ms.locfileid: "64978163"
---
# <a name="binding-ios-libraries"></a>Associação de bibliotecas do iOS

Siga estes links para saber mais sobre a associação de bibliotecas de Objective-C e CocoaPods para xamarin. IOS e xamarin. Mac:

- [**Visão geral** ](~/cross-platform/macios/binding/overview.md) -
  descreve como funciona a associação.
- [**Associação de bibliotecas de Objective-C** ](~/cross-platform/macios/binding/objective-c-libraries.md) -
  obter instruções sobre como associar bibliotecas Objective-C para uso em projetos do Xamarin.
- [**Guia de referência de definição de tipo** ](~/cross-platform/macios/binding/binding-types-reference.md) -
  descreve todos os atributos disponíveis para autores de associação para orientar o processo de geração de associação.

## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

Objetivo Sharpie é uma ferramenta de linha de comando para ajudar a inicializar a primeira passagem de uma associação.
Ele funciona analisando os arquivos de cabeçalho de uma biblioteca nativa para mapear a API pública para o [definição de associação](~/cross-platform/macios/binding/objective-c-libraries.md) (um processo caso contrário, é feito manualmente). Objetivo Sharpie não cria uma associação por si só, mas ele pode ajudar você a começar!

Objetivo 3.0 de Sharpie introduziu a capacidade de associar o Cocoapods diretamente!

## <a name="walkthrough---binding-an-ios-objective-c-librarywalkthroughmd"></a>[Passo a passo: associando a um biblioteca Objective-C do iOS](walkthrough.md)

Esta página fornece uma explicação passo a passo da criação de um projeto de associação do iOS usando o código-fonte aberto [ **InfColorPicker** ](https://github.com/InfinitApps/InfColorPicker) projeto Objective-C como exemplo. O **InfColorPicker** biblioteca fornece um controlador de exibição reutilizáveis que permitem ao usuário selecionar uma cor com base em sua representação HSB, tornando mais fácil de usar a seleção de cor.
Objetivo Sharpie será usado para auxiliar no processo de associação.

## <a name="video"></a>Vídeo

> [!VIDEO https://youtube.com/embed/ZUoPLcmnf1o]

**iOS associações em C /C++ vídeo**

## <a name="related-links"></a>Links relacionados

- [Associação do Objective-C](~/cross-platform/macios/binding/index.md)
- [Mac Binding](~/mac/platform/binding.md)
