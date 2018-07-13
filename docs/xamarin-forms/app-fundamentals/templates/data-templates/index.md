---
title: Modelos de dados do xamarin. Forms
description: Um DataTemplate é usado para especificar a aparência dos dados em controles com suporte e geralmente é associado aos dados a ser exibido.
ms.prod: xamarin
ms.assetid: 838F4BDB-B719-457F-8633-27E9B267A2A0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 771ae22c3e28a4fce758bbfd6a3bd63bafb75e53
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994965"
---
# <a name="xamarinforms-data-templates"></a>Modelos de dados do xamarin. Forms

_Um DataTemplate é usado para especificar a aparência dos dados em controles com suporte e geralmente é associado aos dados a ser exibido._

## <a name="introductionintroductionmd"></a>[Introdução](introduction.md)

Modelos de dados do xamarin. Forms fornecem a capacidade de definir a apresentação dos dados em controles com suporte. Este artigo fornece uma introdução aos modelos de dados, examinando o motivo pelo qual eles são necessários.

## <a name="creating-a-datatemplatecreatingmd"></a>[Criando um DataTemplate](creating.md)

Modelos de dados podem ser criados em linha, em uma [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), ou de um tipo personalizado ou um tipo de célula do xamarin. Forms apropriado. Um modelo embutido deve ser usado se não houver nenhuma necessidade de reutilizar o modelo de dados em outro lugar. Como alternativa, um modelo de dados pode ser reutilizado, definindo-a como um tipo personalizado, ou como um recurso de nível de página ou o nível de aplicativo de nível de controle.

## <a name="creating-a-datatemplateselectorselectormd"></a>[Criando um DataTemplateSelector](selector.md)

Um [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) pode ser usada para escolher uma [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) em tempo de execução com base no valor de uma propriedade de associação de dados. Isso permite que vários `DataTemplate` instâncias a serem aplicadas para o mesmo tipo de objeto, para personalizar a aparência de determinados objetos. Este artigo demonstra como criar e consumir um `DataTemplateSelector`.


## <a name="related-links"></a>Links relacionados

- [Modelos de dados (amostra)](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplates/)
