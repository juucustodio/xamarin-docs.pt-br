---
title: Modelos de dados do xamarin. Forms
description: Um DataTemplate usado para especificar a aparência dos dados em controles com suporte e normalmente associa aos dados a serem exibidos.
ms.prod: xamarin
ms.assetid: 838F4BDB-B719-457F-8633-27E9B267A2A0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: b461cb8be9dcf618b7a497b001186fde5f2b2aa1
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35239850"
---
# <a name="xamarinforms-data-templates"></a>Modelos de dados do xamarin. Forms

_Um DataTemplate usado para especificar a aparência dos dados em controles com suporte e normalmente associa aos dados a serem exibidos._

## <a name="introductionintroductionmd"></a>[Introdução](introduction.md)

Modelos de dados do xamarin. Forms fornecem a capacidade de definir a apresentação dos dados em controles com suporte. Este artigo fornece uma introdução aos modelos de dados, examinando o motivo pelo qual eles são necessários.

## <a name="creating-a-datatemplatecreatingmd"></a>[Criando um DataTemplate](creating.md)

Modelos de dados podem ser criados em linha, em uma [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/), ou de um tipo personalizado ou o tipo apropriado de célula xamarin. Forms. Um modelo embutida deve ser usado se não houver nenhuma necessidade de reutilizar o modelo de dados em outro lugar. Como alternativa, um modelo de dados pode ser reutilizado por defini-lo como um tipo personalizado, ou como um recurso de nível de página ou o nível de aplicativo de nível de controle.

## <a name="creating-a-datatemplateselectorselectormd"></a>[Criando um DataTemplateSelector](selector.md)

Um [ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/) pode ser usado para escolher um [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) em tempo de execução com base no valor de uma propriedade de associação de dados. Isso permite que vários `DataTemplate` instâncias a serem aplicadas para o mesmo tipo de objeto, para personalizar a aparência de determinados objetos. Este artigo demonstra como criar e consumir um `DataTemplateSelector`.


## <a name="related-links"></a>Links relacionados

- [Modelos de dados (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplates/)
