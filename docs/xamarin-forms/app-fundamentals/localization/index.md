---
title: Localização do xamarin. Forms
description: A estrutura interna de localização do .NET pode ser usada para criar aplicativos multilíngues de plataforma cruzada com xamarin. Forms.
ms.prod: xamarin
ms.assetid: 97BF843B-BDAA-4CEA-8189-6DB54B291D7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/13/2018
ms.openlocfilehash: 29624eeccc8542b3296774f6b77480b664bee478
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="localization"></a>Localização

_A estrutura interna de localização do .NET pode ser usada para criar aplicativos multilíngues de plataforma cruzada com xamarin. Forms._

## <a name="string-and-image-localizationtextmd"></a>[Cadeia de caracteres e a localização da imagem](text.md)

O mecanismo interno para localização de usos de aplicativos .NET [arquivos RESX](http://msdn.microsoft.com/library/ekyft91f(v=vs.90).aspx) e as classes de `System.Resources` e `System.Globalization` namespaces. Arquivos RESX que contém cadeias de caracteres traduzidas são inseridos no assembly xamarin. Forms, juntamente com uma classe gerada pelo compilador que fornece acesso fortemente tipado para as traduções. O texto traduzido, em seguida, pode ser recuperado no código.

## <a name="right-to-left-localizationright-to-leftmd"></a>[Localização da direita para esquerda](right-to-left.md)

Direção do fluxo é a direção na qual os elementos de interface do usuário na página são verificados pelos olhos. Localização da direita para esquerda adiciona suporte para a direção do fluxo da direita para a esquerda para aplicativos xamarin. Forms.
