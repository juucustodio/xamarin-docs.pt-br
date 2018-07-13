---
title: Introdução aos modelos de controle do xamarin. Forms
description: Modelos de controle do xamarin. Forms fornecem a capacidade de tema com facilidade e re-theme páginas do aplicativo em tempo de execução. Este artigo fornece uma introdução aos modelos de controle.
ms.prod: xamarin
ms.assetid: 8B8E2360-6531-44A3-A7C8-9A8808DE9B86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 6b7a6c6d9c9c541e1d5e821fc2dac202e98bec62
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994419"
---
# <a name="introduction-to-xamarinforms-control-templates"></a>Introdução aos modelos de controle do xamarin. Forms

_Modelos de controle do xamarin. Forms fornecem a capacidade de tema com facilidade e re-theme páginas do aplicativo em tempo de execução. Este artigo fornece uma introdução aos modelos de controle._

Controles têm propriedades diferentes, tais como `BackgroundColor` e `TextColor`, que pode definir aspectos da aparência do controle. Essas propriedades podem ser definidas usando [estilos](~/xamarin-forms/user-interface/styles/index.md), que pode ser alterado em tempo de execução para implementar temas básicos. No entanto, estilos não mantém uma separação clara entre a aparência de uma página e seu conteúdo e as alterações que podem ser feitas ao definir essas propriedades são limitadas.

Modelos de controle fornecem uma separação clara entre a aparência de uma página e seu conteúdo, permitindo, portanto, a criação de páginas que podem ser facilmente com tema. Por exemplo, um aplicativo pode conter os modelos de controle de nível de aplicativo que fornecem um tema escuro e um tema claro. Cada [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) no aplicativo pode ser com tema aplicando um dos modelos de controle sem alterar o conteúdo que está sendo exibido em cada página. Além disso, os temas fornecidos pelos modelos de controle não estão limitados a alterar as propriedades de controles. Eles também podem alterar os controles usados para implementar o tema.

## <a name="creating-a-controltemplate"></a>Criando um ControlTemplate

Um [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) Especifica a aparência de uma página ou exibição e contém um layout de raiz e dentro do layout, os controles que implementam o modelo. Normalmente, uma `ControlTemplate` utilizará um [ `ContentPresenter` ](xref:Xamarin.Forms.ContentPresenter) para marcar onde o conteúdo a serem exibidos por página ou exibição aparecerá. A página ou exibição que consome os `ControlTemplate` , em seguida, definirá o conteúdo a ser exibido pelo `ContentPresenter`. O diagrama a seguir ilustra uma `ControlTemplate` para uma página que contém um número de controles, incluindo um `ContentPresenter` marcado por um retângulo azul:

![](introduction-images/control-template.png "Modelo de controle para uma página")

Um [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) pode ser aplicado aos seguintes tipos, definindo suas `ControlTemplate` propriedades:

- [`ContentPage`](xref:Xamarin.Forms.ContentPage)
- [`ContentView`](xref:Xamarin.Forms.ContentView)
- [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage)
- [`TemplatedView`](xref:Xamarin.Forms.TemplatedView)

Quando um [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) é criado e atribuído a esses tipos, qualquer aparência existente é substituída com a aparência definida na `ControlTemplate`. Além disso, além de definir a aparência usando o `ControlTemplate` propriedade, controle de modelos também podem ser aplicados usando estilos ainda mais expandir a capacidade de tema.

> [!NOTE]
>  *Quais são as `TemplatedPage` e `TemplatedView` tipos?* `TemplatedPage` é a classe base para `ContentPage`e é o tipo de página mais básico fornecido pelo xamarin. Forms. Diferentemente `ContentPage`, `TemplatedPage` não tem um `Content` propriedade. Portanto, o conteúdo não pode ser diretamente adicionado a um `TemplatedPage` instância. Em vez disso, o conteúdo é adicionado, definindo o modelo de controle para o `TemplatedPage` instância. Da mesma forma, `TemplatedView` é a classe base para `ContentView`. Diferentemente `ContentView`, `TemplatedView` não tem um `Content` propriedade. Portanto, o conteúdo não pode ser diretamente adicionado a um `TemplatedView` instância. Em vez disso, o conteúdo é adicionado, definindo o modelo de controle para o `TemplatedView` instância.

Modelos de controle podem ser criados em XAML e em c#:

- Modelos de controle criados em XAML são definidos em uma [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) que é atribuído ao [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) coleção de uma página ou normalmente para o [ `Resources` ](xref:Xamarin.Forms.Application.Resources) coleção do aplicativo.
- Modelos de controle criados em c# normalmente são definidos na classe da página, ou em uma classe que pode ser acessada globalmente.

Escolhendo onde definir um [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) impactos em que ele pode ser usado da instância:

- [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) instâncias definidas no nível de página só podem ser aplicadas à página.
- [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) instâncias definidas no nível do aplicativo podem ser aplicadas às páginas em todo o aplicativo.

Modelos de controle inferior na hierarquia de exibição têm precedência sobre aquelas definidas superior para cima. Por exemplo, uma [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) denominada `DarkTheme` que é definida no nível da página terá precedência sobre um modelo com o mesmo nome definido no nível do aplicativo. Portanto, um modelo de controle que define um tema a ser aplicado a cada página em um aplicativo deve ser definido no nível do aplicativo.


## <a name="related-links"></a>Links relacionados

- [Estilos](~/xamarin-forms/user-interface/styles/index.md)
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentPresenter](xref:Xamarin.Forms.ContentPresenter)
