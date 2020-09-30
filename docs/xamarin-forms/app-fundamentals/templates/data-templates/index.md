---
title: Xamarin.Forms Modelos de dados
description: Um DataTemplate é usado para especificar a aparência dos dados em controles compatíveis e geralmente é associado aos dados a serem exibidos.
ms.prod: xamarin
ms.assetid: 838F4BDB-B719-457F-8633-27E9B267A2A0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 459b918869bd3e2b5c1927d3fbcded6035226a58
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562581"
---
# <a name="no-locxamarinforms-data-templates"></a>Xamarin.Forms Modelos de dados

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)

_Um DataTemplate é usado para especificar a aparência dos dados em controles compatíveis e geralmente é associado aos dados a serem exibidos._

## <a name="introduction"></a>[Introdução](introduction.md)

Xamarin.Forms os modelos de dados fornecem a capacidade de definir a apresentação de dados em controles com suporte. Este artigo fornece uma introdução aos modelos de dados, examinando o motivo pelo qual eles são necessários.

## <a name="creating-a-datatemplate"></a>[Criando um DataTemplate](creating.md)

Os modelos de dados podem ser criados embutidos, em um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) ou de um tipo personalizado ou de um Xamarin.Forms tipo de célula apropriado. Um modelo embutido deve ser usado caso não haja necessidade de reutilizar o modelo de dados em outro lugar. Como alternativa, um modelo de dados pode ser reutilizado, definindo-o como um tipo personalizado ou como um recurso de aplicativo de nível de controle, de página ou de aplicativo.

## <a name="creating-a-datatemplateselector"></a>[Criando um DataTemplateSelector](selector.md)

Um [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) pode ser usado para escolher um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) em tempo de execução com base no valor de uma propriedade associada a dados. Isso permite que várias instâncias de `DataTemplate` sejam aplicadas ao mesmo tipo de objeto, para personalizar a aparência de objetos específicos. Este artigo demonstra como criar e consumir um `DataTemplateSelector`.

## <a name="related-links"></a>Links Relacionados

- [Modelos de dados (amostra)](/samples/xamarin/xamarin-forms-samples/templates-datatemplates)