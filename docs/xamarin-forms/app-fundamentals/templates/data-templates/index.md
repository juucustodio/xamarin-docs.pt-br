---
title: Xamarin.FormsModelos de dados
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e0961fad18ccd961a5b84b2a5535bca70781dd8d
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136117"
---
# <a name="xamarinforms-data-templates"></a>Xamarin.FormsModelos de dados

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)

_Um DataTemplate é usado para especificar a aparência dos dados em controles compatíveis e geralmente é associado aos dados a serem exibidos._

## <a name="introduction"></a>[Introdução](introduction.md)

Xamarin.Formsos modelos de dados fornecem a capacidade de definir a apresentação de dados em controles com suporte. Este artigo fornece uma introdução aos modelos de dados, examinando o motivo pelo qual eles são necessários.

## <a name="creating-a-datatemplate"></a>[Criando um DataTemplate](creating.md)

Os modelos de dados podem ser criados embutidos, em um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) ou de um tipo personalizado ou de um Xamarin.Forms tipo de célula apropriado. Um modelo embutido deve ser usado caso não haja necessidade de reutilizar o modelo de dados em outro lugar. Como alternativa, um modelo de dados pode ser reutilizado, definindo-o como um tipo personalizado ou como um recurso de aplicativo de nível de controle, de página ou de aplicativo.

## <a name="creating-a-datatemplateselector"></a>[Criando um DataTemplateSelector](selector.md)

Um [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) pode ser usado para escolher um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) em tempo de execução com base no valor de uma propriedade associada a dados. Isso permite que várias instâncias de `DataTemplate` sejam aplicadas ao mesmo tipo de objeto, para personalizar a aparência de objetos específicos. Este artigo demonstra como criar e consumir um `DataTemplateSelector`.

## <a name="related-links"></a>Links relacionados

- [Modelos de dados (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)
