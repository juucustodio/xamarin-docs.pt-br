---
title: Xamarin.Forms DependencyService
description: A classe DependencyService do Xamarin.Forms é um localizador de serviço que permite que os aplicativos Xamarin.Forms invoquem a funcionalidade de plataforma nativa de código compartilhado.
ms.prod: xamarin
ms.assetid: 403479F2-6751-41F2-ADCE-3AF595062FE4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/05/2019
ms.openlocfilehash: ea259d1ee9dc4a94322c38b3e96bee654197bb87
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650450"
---
# <a name="xamarinforms-dependencyservice"></a>Xamarin.Forms DependencyService

## <a name="introductionintroductionmd"></a>[Introdução](introduction.md)

A classe [`DependencyService`](xref:Xamarin.Forms.DependencyService) é um localizador de serviço que permite que os aplicativos Xamarin.Forms invoquem a funcionalidade de plataforma nativa de um código compartilhado.

## <a name="registration-and-resolutionregistration-and-resolutionmd"></a>[Registro e resolução](registration-and-resolution.md)

As implementações de plataforma devem ser registradas no [`DependencyService`](xref:Xamarin.Forms.DependencyService) e então resolvidas de um código compartilhado para invocá-las.

## <a name="picking-a-photo-from-the-libraryphoto-pickermd"></a>[Como escolher uma foto da biblioteca de imagens](photo-picker.md)

Este artigo explica como usar a classe [`DependencyService`](xref:Xamarin.Forms.DependencyService) do Xamarin.Forms para escolher uma foto da biblioteca de imagens do telefone.
