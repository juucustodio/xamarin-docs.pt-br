---
title: Associar bibliotecas de Mac para xamarin. Mac
description: Este documento leva a guias que descrevem como trabalhar com associações do Objective-C em um aplicativo xamarin. Mac, incluindo Sharpie objetivo e o código de exemplo.
ms.prod: xamarin
ms.assetid: 521707CD-79D3-488A-84CB-A37EBF93AC94
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 01/13/2017
ms.openlocfilehash: fde21b2056d56cbf1c4768b287e29f559390f500
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107294"
---
# <a name="binding-mac-libraries-for-xamarinmac"></a>Associar bibliotecas de Mac para xamarin. Mac

Siga estes links para saber mais sobre a associação de bibliotecas de Objective-C em xamarin. Mac:

- [**Visão geral** ](~/cross-platform/macios/binding/overview.md) -
  descreve como funciona a associação.
- [**Associação de bibliotecas de Objective-C** ](~/cross-platform/macios/binding/objective-c-libraries.md) -
  obter instruções sobre como associar bibliotecas Objective-C para uso em projetos do Xamarin.
- [**Guia de referência de definição de tipo** ](~/cross-platform/macios/binding/binding-types-reference.md) -
  descreve todos os atributos disponíveis para autores de associação para orientar o processo de geração de associação.

## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

Objetivo Sharpie é uma ferramenta de linha de comando para ajudar a inicializar a primeira passagem de uma associação.
Ele funciona analisando os arquivos de cabeçalho de uma biblioteca nativa para mapear a API pública para o [definição de associação](~/cross-platform/macios/binding/binding-types-reference.md) (um processo caso contrário, é feito manualmente). Objetivo Sharpie não cria uma associação por si só, mas ele pode ajudar você a começar!

## <a name="examples"></a>Exemplos

Consulte a [exemplo de XMBindingExample Mac](https://github.com/xamarin/mac-samples/tree/master/XMBindingExample) para aprender a criar uma associação de Mac usando projetos de associação.

## <a name="related-links"></a>Links relacionados

- [Associação do Objective-C](~/cross-platform/macios/binding/index.md)
- [Associação de bibliotecas do iOS](~/ios/platform/binding-objective-c/index.md)
