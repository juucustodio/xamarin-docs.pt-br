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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "67650450"
---
# <a name="xamarinforms-dependencyservice"></a>Xamarin.Forms DependencyService

## <a name="introduction"></a>[Introdução](introduction.md)

A [`DependencyService`](xref:Xamarin.Forms.DependencyService) classe é um localizador de serviço que permite que os aplicativos Xamarin.Forms invoque a funcionalidade da plataforma nativa a partir de código compartilhado.

## <a name="registration-and-resolution"></a>[Registro e resolução](registration-and-resolution.md)

As implementações da plataforma [`DependencyService`](xref:Xamarin.Forms.DependencyService)devem ser registradas com o , e depois resolvidas a partir de código compartilhado para invocá-los.

## <a name="picking-a-photo-from-the-library"></a>[Como escolher uma foto da biblioteca de imagens](photo-picker.md)

Este artigo explica como usar a [`DependencyService`](xref:Xamarin.Forms.DependencyService) classe Xamarin.Forms para escolher uma foto na biblioteca de imagens do telefone.
