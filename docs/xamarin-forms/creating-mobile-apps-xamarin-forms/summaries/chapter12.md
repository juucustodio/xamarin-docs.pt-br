---
title: ''
description: ''
Creating Mobile Apps with Xamarin.Forms: Summary of Chapter 12. Styles''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 22f7272751c30bed350fe7aada8d5dacf0acc809
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136780"
---
# <a name="summary-of-chapter-12-styles"></a>Resumo do capítulo 12. Estilos

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)

No Xamarin.Forms , os estilos permitem que várias exibições compartilhem uma coleção de configurações de propriedade. Isso reduz a marcação e habilita a manutenção de temas visuais consistentes.

Os estilos são quase sempre definidos e consumidos na marcação. Um objeto do tipo [`Style`](xref:Xamarin.Forms.Style) é instanciado em um dicionário de recursos e, em seguida, definido como a [`Style`](xref:Xamarin.Forms.NavigableElement.Style) propriedade de um elemento visual usando uma `StaticResource` `DynamicResource` extensão de marcação ou.

## <a name="the-basic-style"></a>O estilo básico

Um `Style` requer que [`TargetType`](xref:Xamarin.Forms.Style.TargetType) seja definido como o tipo do objeto visual ao qual se aplica. Quando uma `Style` é instanciada em um dicionário de recursos (como é comum), ela também requer um `x:Key` atributo.

O `Style` tem uma propriedade content do tipo [`Setters`](xref:Xamarin.Forms.Style.Setters) , que é uma coleção de [`Setter`](xref:Xamarin.Forms.Setter) objetos. Cada `Setter` um associa um [`Property`](xref:Xamarin.Forms.Setter.Property) com um [`Value`](xref:Xamarin.Forms.Setter.Value) .

Em XAML, a `Property` configuração é o nome de uma propriedade CLR (como a `Text` propriedade de `Button` ), mas a propriedade com estilo deve ser apoiada por uma propriedade vinculável. Além disso, a propriedade deve ser definida na classe indicada pela `TargetType` configuração ou herdada por essa classe.

Você pode especificar a `Value` configuração usando o elemento Property `<Setter.Value>` . Isso permite que você defina `Value` como um objeto que não pode ser expresso em uma cadeia de caracteres de texto ou em um objeto `OnPlatform` , ou em um objeto instanciado usando `x:Arguments` ou `x:FactoryMethod` . A `Value` propriedade também pode ser definida com uma `StaticResource` expressão para outro item no dicionário.

O programa [**basicstyle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyle) demonstra a sintaxe básica e mostra como fazer referência a `Style` com uma `StaticResource` extensão de marcação:

[![Captura de tela tripla de estilo básico](images/ch12fg01-small.png "Estilos básicos")](images/ch12fg01-large.png#lightbox "Estilos básicos")

O `Style` objeto e qualquer objeto criado no `Style` objeto como uma `Value` configuração são compartilhados entre todas as exibições que fazem referência a isso `Style` . O `Style` não pode conter nada que não possa ser compartilhado, como um `View` derivativo.

Não é possível definir manipuladores de eventos em um `Style` . A `GestureRecognizers` propriedade não pode ser definida em um `Style` porque não é apoiada por uma propriedade vinculável.

## <a name="styles-in-code"></a>Estilos no código

Embora não seja comum, você pode instanciar e inicializar `Style` objetos no código. Isso é demonstrado pelo exemplo de [**BasicStyleCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyleCode) .

## <a name="style-inheritance"></a>Herança de estilo

`Style`tem uma [`BasedOn`](xref:Xamarin.Forms.Style.BasedOn) propriedade que você pode definir para uma `StaticResource` extensão de marcação fazendo referência a outro estilo. Isso permite que os estilos herdem de estilos anteriores e adicionem ou substituam as configurações de propriedade. O exemplo [**StyleInheritance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleInheritance) demonstra isso.

Se `Style2` é baseado em `Style1` , o `TargetType` de `Style2` deve ser igual `Style1` ou derivado de `Style1` . O dicionário de recursos no qual `Style1` é armazenado deve ser o mesmo dicionário de recursos do `Style2` ou um dicionário de recursos mais alto na árvore visual.

## <a name="implicit-styles"></a>Estilos implícitos

Se um `Style` em um dicionário de recursos não tiver uma `x:Key` configuração de atributo, ele será atribuído automaticamente a uma chave de dicionário e o `Style` objeto se tornará um *estilo implícito*. Uma exibição sem uma `Style` configuração e cujo tipo corresponde `TargetType` exatamente a esse estilo, como demonstra o exemplo [**implícitostyle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/ImplicitStyle) .

Um estilo implícito pode derivar de um `Style` com uma `x:Key` configuração, mas não o contrário. Não é possível referenciar explicitamente um estilo implícito.

Você pode implementar três tipos de hierarquia com estilos e `BasedOn` :

- De estilos definidos no `Application` e para `Page` baixo até estilos definidos em layouts inferiores na árvore visual.
- De estilos definidos para classes base, como `VisualElement` e `View` para estilos definidos para classes específicas.
- De estilos com chaves de dicionário explícitas para estilos implícitos.

Essas hierarquias são demonstradas no exemplo [**StyleHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleHierarchy) .

## <a name="dynamic-styles"></a>Estilos dinâmicos

Um estilo em um dicionário de recursos pode ser referenciado em `DynamicResource` vez de `StaticResource` . Isso torna o estilo um *estilo dinâmico*. Se esse estilo for substituído no dicionário de recursos por outro estilo com a mesma chave, as exibições que fazem referência a esse estilo `DynamicResource` serão alteradas automaticamente. Além disso, a ausência de uma entrada de dicionário com a chave especificada fará com que o `StaticResource` gere uma exceção, mas não `DynamicResource` .

Você pode usar essa técnica para alterar dinamicamente estilos ou temas, como demonstra o exemplo de [**DynamicStyles**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynamicStyles) .

No entanto, você não pode definir a `BasedOn` propriedade como uma `DynamicResource` extensão de composição porque `BasedOn` não tem o suporte de uma propriedade vinculável. Para derivar um estilo dinamicamente, não defina `BasedOn` . Em vez disso, defina a [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) propriedade como a chave de dicionário do estilo do qual você deseja derivar. O exemplo [**DynamicStylesInheritance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynaStylesInh) demonstra essa técnica.

## <a name="device-styles"></a>Estilos de dispositivo

A [`Device.Styles`](xref:Xamarin.Forms.Device.Styles) classe aninhada define doze campos somente leitura estáticos para seis estilos com um `TargetType` de `Label` que você pode usar para tipos comuns de uso de texto.

Seis desses campos são do tipo `Style` que você pode definir diretamente para uma `Style` propriedade no código:

- [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle)
- [`TitleStyle`](xref:Xamarin.Forms.Device.Styles.TitleStyle)
- [`SubtitleStyle`](xref:Xamarin.Forms.Device.Styles.SubtitleStyle)
- [`CaptionStyle`](xref:Xamarin.Forms.Device.Styles.CaptionStyle)
- [`ListItemTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyle)
- [`ListItemDetailTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyle)

Os outros seis campos são do tipo `string` e podem ser usados como chaves de dicionário para estilos dinâmicos:

- [`BodyStyleKey`](xref:Xamarin.Forms.Device.Styles.BodyStyleKey)igual a "BodyStyle"
- [`TitleStyleKey`](xref:Xamarin.Forms.Device.Styles.TitleStyleKey)igual a "TitleStyle"
- [`SubtitleStyleKey`](xref:Xamarin.Forms.Device.Styles.SubtitleStyleKey)igual a "subtítulostyle"
- [`CaptionStyleKey`](xref:Xamarin.Forms.Device.Styles.CaptionStyleKey)igual a "Captionstyle"
- [`ListItemTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyleKey)igual a "ListItemTextStyle"
- [`ListItemDetailTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyleKey)igual a "ListItemDetailTextStyle"

Esses estilos são ilustrados pelo exemplo de [**DeviceStylesList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DeviceStylesList) .

## <a name="related-links"></a>Links relacionados

- [Capítulo 12 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch12-Apr2016.pdf)
- [Capítulo 12 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)
- [Estilos](~/xamarin-forms/user-interface/styles/index.md)
