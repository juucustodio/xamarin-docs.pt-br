---
title: Introdução aos modelos de controle do Xamarin.Forms
description: Os modelos de controle do Xamarin.Forms fornecem a capacidade de tematizar e tematizar novamente páginas de aplicativo em tempo de execução. Este artigo fornece uma introdução a modelos de controle.
ms.prod: xamarin
ms.assetid: 8B8E2360-6531-44A3-A7C8-9A8808DE9B86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 676523e461737d7820278ca8c319794d3347088d
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70289799"
---
# <a name="introduction-to-xamarinforms-control-templates"></a>Introdução aos modelos de controle do Xamarin.Forms

_Os modelos de controle do Xamarin.Forms fornecem a capacidade de tematizar e tematizar novamente páginas de aplicativo em tempo de execução. Este artigo fornece uma introdução a modelos de controle._

Os controles têm propriedades diferentes, tais como `BackgroundColor` e `TextColor`, que podem definir aspectos da aparência do controle. Essas propriedades podem ser definidas usando [estilos](~/xamarin-forms/user-interface/styles/index.md), que pode ser alterado em tempo de execução para implementar temas básicos. No entanto, os estilos não mantêm uma separação clara entre a aparência de uma página e seu conteúdo e as alterações que podem ser feitas ao definir essas propriedades são limitadas.

Os modelos de controle fornecem uma separação clara entre a aparência de uma página e seu conteúdo, permitindo, portanto, a criação de páginas cujo tema pode ser definido facilmente. Por exemplo, um aplicativo pode conter modelos de controle de nível de aplicativo que fornecem um tema claro e um escuro. Cada [`ContentPage`](xref:Xamarin.Forms.ContentPage) no aplicativo pode ser tematizado aplicando um dos modelos de controle sem alterar o conteúdo que está sendo exibido por cada página. Além disso, os temas fornecidos pelos modelos de controle não estão limitados a alterar as propriedades de controles. Eles também podem alterar os controles usados para implementar o tema.

## <a name="creating-a-controltemplate"></a>Criando um ControlTemplate

Um [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) especifica a aparência de uma página ou de uma exibição e contém um layout raiz e, dentro do layout, os controles que implementam o modelo. Normalmente, um `ControlTemplate` utilizará um [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) para marcar onde o conteúdo a ser exibido pela página ou pela exibição será exibido. A página ou a exibição que consome o `ControlTemplate` definirá o conteúdo a ser exibido pelo `ContentPresenter`. O diagrama a seguir ilustra um `ControlTemplate` para uma página que contém um número de controles, incluindo um `ContentPresenter` marcado por um retângulo azul:

![](introduction-images/control-template.png "Modelo de controle para uma página")

Um [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) pode ser aplicado aos seguintes tipos definindo suas propriedades `ControlTemplate`:

- [`ContentPage`](xref:Xamarin.Forms.ContentPage)
- [`ContentView`](xref:Xamarin.Forms.ContentView)
- [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage)
- [`TemplatedView`](xref:Xamarin.Forms.TemplatedView)

Quando um [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) é criado e atribuído a esses tipos, qualquer aparência existente é substituída pela aparência definida no `ControlTemplate`. Ademais, além de definir a aparência usando a propriedade `ControlTemplate`, os modelos de controle também podem ser aplicados usando estilos para expandir ainda mais a capacidade do tema.

> [!NOTE]
> *O que são os tipos `TemplatedPage` e `TemplatedView`?* `TemplatedPage` é a classe base para `ContentPage` e é o tipo de página mais básico fornecido pelo Xamarin.Forms. Diferentemente de `ContentPage`, `TemplatedPage` não tem uma propriedade `Content`. Portanto, o conteúdo não pode ser diretamente adicionado a uma instância `TemplatedPage`. Em vez disso, o conteúdo é adicionado, definindo o modelo de controle para a instância `TemplatedPage`. De maneira semelhante, `TemplatedView` é a classe base para `ContentView`. Diferentemente de `ContentView`, `TemplatedView` não tem uma propriedade `Content`. Portanto, o conteúdo não pode ser diretamente adicionado a uma instância `TemplatedView`. Em vez disso, o conteúdo é adicionado, definindo o modelo de controle para a instância `TemplatedView`.

Os modelos de controle podem ser criados em XAML e em C#:

- Os modelos de controle criados em XAML são definidos em um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) atribuído à coleção [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) de uma página ou mais normalmente à coleção [`Resources`](xref:Xamarin.Forms.Application.Resources) do aplicativo.
- Normalmente, os modelos de controle criados em C# são definidos na classe da página ou em uma classe que pode ser acessada globalmente.

Escolher onde definir uma instância [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) afeta onde ela pode ser usada:

- As instâncias [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) definidas no nível da página só podem ser aplicadas a ela.
- As instâncias [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) definidas no nível do aplicativo podem ser aplicadas às páginas em todo o aplicativo.

Os modelos de controle mais baixos na hierarquia de exibição têm precedência sobre os definidos na parte de cima. Por exemplo, um [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) denominado `DarkTheme` definido no nível da página tem precedência sobre um modelo com o mesmo nome definido no nível do aplicativo. Portanto, um modelo de controle que define um tema a ser aplicado a cada página em um aplicativo deve ser definido no nível do aplicativo.


## <a name="related-links"></a>Links relacionados

- [Estilos](~/xamarin-forms/user-interface/styles/index.md)
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentPresenter](xref:Xamarin.Forms.ContentPresenter)
