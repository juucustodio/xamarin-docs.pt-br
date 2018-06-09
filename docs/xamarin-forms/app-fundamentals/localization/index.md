---
title: Localização do xamarin. Forms
description: A estrutura interna de localização do .NET pode ser usada para criar aplicativos multilíngues de plataforma cruzada com xamarin. Forms. Texto e imagens podem ser localizadas e aplicativos podem oferecer suporte a uma direção de fluxo da direita para esquerda.
ms.prod: xamarin
ms.assetid: 97BF843B-BDAA-4CEA-8189-6DB54B291D7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/13/2018
ms.openlocfilehash: 78731924324a1ddd34c0d197070699e2998c1513
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35240587"
---
# <a name="xamarinforms-localization"></a>Localização do xamarin. Forms

_A estrutura interna de localização do .NET pode ser usada para criar aplicativos multilíngues de plataforma cruzada com xamarin. Forms._

## <a name="string-and-image-localizationtextmd"></a>[Localização de cadeia de caracteres e imagem](text.md)

O mecanismo interno para localização de usos de aplicativos .NET [arquivos RESX](http://msdn.microsoft.com/library/ekyft91f(v=vs.90).aspx) e as classes de `System.Resources` e `System.Globalization` namespaces. Arquivos RESX que contém cadeias de caracteres traduzidas são inseridos no assembly xamarin. Forms, juntamente com uma classe gerada pelo compilador que fornece acesso fortemente tipado para as traduções. O texto traduzido, em seguida, pode ser recuperado no código.

## <a name="right-to-left-localizationright-to-leftmd"></a>[Localização da direita para esquerda](right-to-left.md)

Direção do fluxo é a direção na qual os elementos de interface do usuário na página são verificados pelos olhos. Localização da direita para esquerda adiciona suporte para a direção do fluxo da direita para a esquerda para aplicativos xamarin. Forms.
