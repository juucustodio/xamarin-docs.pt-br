---
title: Seletor
description: "O modo de exibição de seletor é um controle para selecionar um item de texto de uma lista de dados."
ms.topic: article
ms.prod: xamarin
ms.assetid: D4815A4B-104B-4294-951B-BD8F2EC33C86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/11/2017
ms.openlocfilehash: edc724eb73b314c0accd3e8775b9b26b6eac16d9
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="picker"></a>Seletor

_O modo de exibição de seletor é um controle para selecionar um item de texto de uma lista de dados._

Um [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) exibe uma lista curta de itens, na qual o usuário pode selecionar. No entanto, um [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) não mostra todos os dados quando ela é exibida pela primeira vez. Em vez disso, o valor de seu [ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Title/) propriedade é mostrada como um espaço reservado nas plataformas Android e iOS:

[![](images/picker-initial.png "Seletor de exibição de inicial")](images/picker-initial-large.png "inicial seletor de exibição")

Quando o [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) ganhos foco, seus dados é exibido e o usuário pode selecionar um item:

[![](images/picker-selection.png "Selecionando um Item de seletor")](images/picker-selection-large.png "seletor para selecionar um Item")

Após a seleção, o item selecionado é exibido pelo [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/):

![](images/picker-after-selection.png "Seletor de após a seleção")

Existem duas técnicas para popular um [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) com dados:

- Definindo o [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/) propriedade aos dados a serem exibidos. Essa é a técnica recomendada, que foi introduzida no xamarin. Forms 2.3.4. Para obter mais informações, consulte [definindo a propriedade de ItemsSource do seletor](populating-itemssource.md).
- Adicionando dados a ser exibida para o [ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/) coleção. Essa técnica foi o processo original para popular um [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) com dados. Para obter mais informações, consulte [adicionando dados à coleção de itens do seletor](populating-items.md).


## <a name="related-links"></a>Links relacionados

- [Seletor](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)
