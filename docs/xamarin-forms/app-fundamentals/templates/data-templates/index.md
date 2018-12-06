---
title: Modelos de dados do Xamarin.Forms
description: Um DataTemplate é usado para especificar a aparência dos dados em controles compatíveis e geralmente é associado aos dados a serem exibidos.
ms.prod: xamarin
ms.assetid: 838F4BDB-B719-457F-8633-27E9B267A2A0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 771ae22c3e28a4fce758bbfd6a3bd63bafb75e53
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994965"
---
# <a name="xamarinforms-data-templates"></a>Modelos de dados do Xamarin.Forms

_Um DataTemplate é usado para especificar a aparência dos dados em controles compatíveis e geralmente é associado aos dados a serem exibidos._

## <a name="introductionintroductionmd"></a>[Introdução](introduction.md)

Os modelos de dados do Xamarin.Forms fornecem a capacidade de definir a apresentação de dados em controles compatíveis. Este artigo fornece uma introdução aos modelos de dados, examinando o motivo pelo qual eles são necessários.

## <a name="creating-a-datatemplatecreatingmd"></a>[Criando um DataTemplate](creating.md)

Modelos de dados podem ser criados embutidos, em um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) ou de um tipo personalizado ou um tipo de célula apropriado do Xamarin.Forms. Um modelo embutido deve ser usado se não há nenhuma necessidade de reutilizar o modelo de dados em outro lugar. Como alternativa, um modelo de dados pode ser reutilizado, definindo-o como um tipo personalizado ou como um recurso de aplicativo de nível de controle, de página ou de aplicativo.

## <a name="creating-a-datatemplateselectorselectormd"></a>[Criando um DataTemplateSelector](selector.md)

Um [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) pode ser usado para escolher um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) em tempo de execução com base no valor de uma propriedade associada a dados. Isso permite que várias instâncias de `DataTemplate` sejam aplicadas ao mesmo tipo de objeto, para personalizar a aparência de objetos específicos. Este artigo demonstra como criar e consumir um `DataTemplateSelector`.


## <a name="related-links"></a>Links relacionados

- [Modelos de dados (amostra)](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplates/)
