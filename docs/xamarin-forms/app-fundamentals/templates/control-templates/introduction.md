---
title: "Introdução"
description: "Modelos de controle do xamarin. Forms fornecem a capacidade facilmente tema e re-tema páginas de aplicativo em tempo de execução. Este artigo fornece uma introdução aos modelos de controle."
ms.topic: article
ms.prod: xamarin
ms.assetid: 8B8E2360-6531-44A3-A7C8-9A8808DE9B86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: c3973b94168706e047ee5c312a8823503c9b0fd8
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="introduction"></a>Introdução

_Modelos de controle do xamarin. Forms fornecem a capacidade facilmente tema e re-tema páginas de aplicativo em tempo de execução. Este artigo fornece uma introdução aos modelos de controle._

Controles têm propriedades diferentes, como `BackgroundColor` e `TextColor`, que pode definir aspectos da aparência do controle. Essas propriedades podem ser definidas usando [estilos](~/xamarin-forms/user-interface/styles/index.md), que pode ser alterado em tempo de execução para implementar temas básico. No entanto, estilos de não mantêm uma separação clara entre a aparência de uma página e seu conteúdo, e as alterações que podem ser feitas ao definir essas propriedades são limitadas.

Modelos de controle de fornecem uma separação clara entre a aparência de uma página e seu conteúdo, permitindo, portanto, a criação de páginas que podem ser facilmente com tema. Por exemplo, um aplicativo pode conter os modelos de controle de nível de aplicativo que fornecem um tema escuro e um tema claro. Cada [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) no aplicativo pode ser com tema aplicando um dos modelos de controle sem alterar o conteúdo que está sendo exibido por cada página. Além disso, os temas fornecidos pelos modelos de controle não estão limitados a alterar as propriedades de controles. Eles também podem alterar os controles usados para implementar o tema.

## <a name="creating-a-controltemplate"></a>Criando um ControlTemplate

Um [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) Especifica a aparência de uma página ou modo de exibição e contém um layout de raiz e dentro do layout, os controles que implementam o modelo. Normalmente, um `ControlTemplate` utilizará um [ `ContentPresenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/) para marcar onde o conteúdo a ser exibido pelo modo de exibição ou página será exibida. A página ou o modo de exibição que consome o `ControlTemplate` , em seguida, definirá o conteúdo a ser exibido, o `ContentPresenter`. O diagrama a seguir ilustra uma `ControlTemplate` para uma página que contém um número de controles, incluindo um `ContentPresenter` marcado por um retângulo azul:

![](introduction-images/control-template.png "Modelo de controle para uma página")

Um [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) podem ser aplicadas aos seguintes tipos de definindo suas `ControlTemplate` propriedades:

- [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)
- [`ContentView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/)
- [`TemplatedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedPage/)
- [`TemplatedView`](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedView/)

Quando um [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) é criado e atribuído a esses tipos, qualquer aparência existente é substituída com a aparência definida no `ControlTemplate`. Além disso, bem como definir a aparência usando o `ControlTemplate` controle modelos também podem ser aplicados usando estilos para outra propriedade expandir a capacidade de tema.

> [!NOTE]
>  *Quais são as `TemplatedPage` e `TemplatedView` tipos?* `TemplatedPage` é a classe base para `ContentPage`e é o tipo mais básico de página fornecido pelo xamarin. Forms. Ao contrário de `ContentPage`, `TemplatedPage` não tem um `Content` propriedade. Portanto, o conteúdo não pode ser diretamente adicionado a um `TemplatedPage` instância. Em vez disso, o conteúdo é adicionado, definindo o modelo de controle para a `TemplatedPage` instância. Da mesma forma, `TemplatedView` é a classe base para `ContentView`. Ao contrário de `ContentView`, `TemplatedView` não tem um `Content` propriedade. Portanto, o conteúdo não pode ser diretamente adicionado a um `TemplatedView` instância. Em vez disso, o conteúdo é adicionado, definindo o modelo de controle para a `TemplatedView` instância.

Modelos de controle podem ser criados em XAML e no c#:

- Modelos de controle criados em XAML são definidos em um [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) que é atribuído ao [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) coleção de uma página ou normalmente para o [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.Resources/) coleção do aplicativo.
- Modelos de controle criados em c# costumam ser definidos na classe da página ou em uma classe que pode ser acessada globalmente.

Escolhendo onde definir um [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) instância impactos onde ele pode ser usado:

- [`ControlTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) instâncias definidas no nível de página só podem ser aplicadas à página.
- [`ControlTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) instâncias definidas no nível do aplicativo podem ser aplicadas às páginas em todo o aplicativo.

Inferior na hierarquia do modo de exibição de modelos de controle têm precedência sobre aquelas definidas superior para cima. Por exemplo, um [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) chamado `DarkTheme` que é definido no nível de página têm precedência sobre um modelo com o mesmo nome definido no nível do aplicativo. Portanto, um modelo de controle que define um tema a ser aplicado a cada página em um aplicativo deve ser definido no nível do aplicativo.


## <a name="related-links"></a>Links relacionados

- [Estilos](~/xamarin-forms/user-interface/styles/index.md)
- [ControlTemplate](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)
- [ContentPresenter](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/)
