---
title: Xamarin.FormsLocalizar
description: A estrutura de localização interna do .NET pode ser usada para criar aplicativos multilíngües entre plataformas com o Xamarin.Forms . Texto e imagens podem ser localizados e os aplicativos podem dar suporte a uma direção de fluxo da direita para esquerda.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: dddb80e8fb683547d2bf6ba0b74e870fe240695c
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137560"
---
# <a name="xamarinforms-localization"></a>Xamarin.FormsLocalizar

_A estrutura de localização interna do .NET pode ser usada para criar aplicativos multilíngües entre plataformas com o Xamarin.Forms ._

## <a name="xamarinforms-string-and-image-localizationtextmd"></a>[Xamarin.FormsLocalização de cadeia de caracteres e imagem](text.md)

O mecanismo interno para localização de usos de aplicativos .NET [arquivos RESX](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps#resources-in-resx-files) e as classes nos namespaces `System.Resources` e `System.Globalization`. Os arquivos RESX que contêm cadeias de caracteres traduzidas são inseridos no Xamarin.Forms assembly, juntamente com uma classe gerada pelo compilador que fornece acesso fortemente tipado às traduções. O texto traduzido pode ser então recuperado no código.

## <a name="right-to-left-localization"></a>[Localização da direita para a esquerda](right-to-left.md)

A direção do fluxo é a direção na qual os elementos de interface do usuário na página são detectados pelos olhos. A localização da direita para a esquerda adiciona suporte à direção de fluxo da direita para a esquerda para Xamarin.Forms aplicativos.
