---
title: Localização do Xamarin.Forms
description: A estrutura de localização interna do .NET pode ser usada para criar aplicativos multilíngues multiplataforma com o Xamarin.Forms. Texto e imagens podem ser localizados e os aplicativos podem dar suporte a uma direção de fluxo da direita para esquerda.
ms.prod: xamarin
ms.assetid: 97BF843B-BDAA-4CEA-8189-6DB54B291D7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2018
ms.openlocfilehash: 71033e935a2d3a4be88dbcc5d975938771484640
ms.sourcegitcommit: b60a37587aad8a0bfa8a522d88d22fa672002443
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285567"
---
# <a name="xamarinforms-localization"></a>Localização do Xamarin.Forms

_A estrutura de localização interna do .NET pode ser usada para criar aplicativos multilíngues multiplataforma com o Xamarin.Forms._

## <a name="string-and-image-localizationtextmd"></a>[Localização de cadeia de caracteres e imagem](text.md)

O mecanismo interno para localização de usos de aplicativos .NET [arquivos RESX](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps#resources-in-resx-files) e as classes nos namespaces `System.Resources` e `System.Globalization`. Arquivos RESX que contêm cadeias de caracteres traduzidas são inseridos no assembly do Xamarin.Forms, junto com uma classe gerada pelo compilador que fornece acesso fortemente tipado para as traduções. O texto traduzido pode ser então recuperado no código.

## <a name="right-to-left-localizationright-to-leftmd"></a>[Localização da direita para esquerda](right-to-left.md)

A direção do fluxo é a direção na qual os elementos de interface do usuário na página são detectados pelos olhos. A localização da direita para esquerda adiciona suporte para a direção do fluxo da direita para esquerda para aplicativos Xamarin.Forms.
