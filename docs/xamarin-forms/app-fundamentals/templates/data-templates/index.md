---
title: Modelos de dados do Xamarin.Forms
description: Um DataTemplate é usado para especificar a aparência dos dados em controles compatíveis e geralmente é associado aos dados a serem exibidos.
ms.prod: xamarin
ms.assetid: 838F4BDB-B719-457F-8633-27E9B267A2A0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 5d130a6644af4e5831263c6de137513c021e0b6a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70760796"
---
# <a name="xamarinforms-data-templates"></a>Modelos de dados do Xamarin.Forms

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)

_Um DataTemplate é usado para especificar a aparência dos dados em controles compatíveis e geralmente é associado aos dados a serem exibidos._

## <a name="introduction"></a>[Introdução](introduction.md)

Os modelos de dados do Xamarin.Forms fornecem a capacidade de definir a apresentação de dados em controles compatíveis. Este artigo fornece uma introdução aos modelos de dados, examinando o motivo pelo qual eles são necessários.

## <a name="creating-a-datatemplate"></a>[Criando um DataTemplate](creating.md)

Os modelos de dados podem ser [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)criados inline, em um , ou a partir de um tipo personalizado ou tipo de célula Xamarin.Forms apropriado. Um modelo embutido deve ser usado caso não haja necessidade de reutilizar o modelo de dados em outro lugar. Como alternativa, um modelo de dados pode ser reutilizado, definindo-o como um tipo personalizado ou como um recurso de aplicativo de nível de controle, de página ou de aplicativo.

## <a name="creating-a-datatemplateselector"></a>[Criando um DataTemplateSelector](selector.md)

A [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) pode ser usado [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) para escolher um em tempo de execução com base no valor de uma propriedade vinculada a dados. Isso permite que várias instâncias de `DataTemplate` sejam aplicadas ao mesmo tipo de objeto, para personalizar a aparência de objetos específicos. Este artigo demonstra como criar e consumir um `DataTemplateSelector`.

## <a name="related-links"></a>Links relacionados

- [Modelos de dados (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)
