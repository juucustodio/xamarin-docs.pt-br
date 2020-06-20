---
title: Xamarin.FormsDependencyService
description: A Xamarin.Forms classe DependencyService é um localizador de serviço que permite que Xamarin.Forms os aplicativos invoquem a funcionalidade de plataforma nativa do código compartilhado.
ms.prod: xamarin
ms.assetid: 403479F2-6751-41F2-ADCE-3AF595062FE4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/05/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 126e2d2373bad923fe1d66fe355ad811c15fbe4f
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138366"
---
# <a name="xamarinforms-dependencyservice"></a>Xamarin.FormsDependencyService

## <a name="introduction"></a>[Introdução](introduction.md)

A [`DependencyService`](xref:Xamarin.Forms.DependencyService) classe é um localizador de serviço que permite que Xamarin.Forms os aplicativos invoquem a funcionalidade de plataforma nativa do código compartilhado.

## <a name="registration-and-resolution"></a>[Registro e resolução](registration-and-resolution.md)

Implementações de plataforma devem ser registradas com o [`DependencyService`](xref:Xamarin.Forms.DependencyService) e, em seguida, resolvidas do código compartilhado para chamá-las.

## <a name="picking-a-photo-from-the-library"></a>[Como escolher uma foto da biblioteca de imagens](photo-picker.md)

Este artigo explica como usar a Xamarin.Forms [`DependencyService`](xref:Xamarin.Forms.DependencyService) classe para escolher uma foto da biblioteca de imagens do telefone.
