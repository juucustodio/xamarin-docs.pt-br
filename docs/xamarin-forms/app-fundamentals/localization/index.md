---
title: Xamarin.Forms Localizar
description: A estrutura de localização interna do .NET pode ser usada para criar aplicativos multilíngües entre plataformas com o Xamarin.Forms . Texto e imagens podem ser localizados e os aplicativos podem dar suporte a uma direção de fluxo da direita para esquerda.
ms.prod: xamarin
ms.assetid: 97BF843B-BDAA-4CEA-8189-6DB54B291D7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5dbcc63eb162454845b78fc24a3beb0ca8296453
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91558148"
---
# <a name="no-locxamarinforms-localization"></a>Xamarin.Forms Localizar

_A estrutura de localização interna do .NET pode ser usada para criar aplicativos multilíngües entre plataformas com o Xamarin.Forms ._

## <a name="no-locxamarinforms-string-and-image-localization"></a>[Xamarin.Forms Localização de cadeia de caracteres e imagem](text.md)

O mecanismo interno para localização de usos de aplicativos .NET [arquivos RESX](/dotnet/framework/resources/creating-resource-files-for-desktop-apps#resources-in-resx-files) e as classes nos namespaces `System.Resources` e `System.Globalization`. Os arquivos RESX que contêm cadeias de caracteres traduzidas são inseridos no Xamarin.Forms assembly, juntamente com uma classe gerada pelo compilador que fornece acesso fortemente tipado às traduções. O texto traduzido pode ser então recuperado no código.

## <a name="right-to-left-localization"></a>[Localização da direita para a esquerda](right-to-left.md)

A direção do fluxo é a direção na qual os elementos de interface do usuário na página são detectados pelos olhos. A localização da direita para a esquerda adiciona suporte à direção de fluxo da direita para a esquerda para Xamarin.Forms aplicativos.