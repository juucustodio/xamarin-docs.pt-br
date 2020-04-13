---
title: Resumo do Capítulo 12. Estilos
description: 'Criando aplicativos móveis com Xamarin.Forms: Resumo do Capítulo 12. Estilos'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3EAE6BDC-8EFB-464B-A87B-1C35B8387BB3
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 408f171a3c7c690b700f7be21a3dcaff503467d9
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "65926916"
---
# <a name="summary-of-chapter-12-styles"></a>Resumo do Capítulo 12. Estilos

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)

No Xamarin.Forms, os estilos permitem que várias visualizações compartilhem uma coleção de configurações de propriedade. Isso reduz a marcação e permite manter temas visuais consistentes.

Estilos são quase sempre definidos e consumidos em marcação. Um objeto [`Style`](xref:Xamarin.Forms.Style) de tipo é instanciado em um [`Style`](xref:Xamarin.Forms.NavigableElement.Style) dicionário de recursos `StaticResource` e, em seguida, definido para a propriedade de um elemento visual usando uma extensão de marcação ou. `DynamicResource`

## <a name="the-basic-style"></a>O estilo básico

A `Style` requer [`TargetType`](xref:Xamarin.Forms.Style.TargetType) que ele seja definido para o tipo de objeto visual a que se aplica. Quando `Style` um é instanciado em um dicionário de recursos `x:Key` (como é comum) também requer um atributo.

O `Style` tem uma propriedade [`Setters`](xref:Xamarin.Forms.Style.Setters)de conteúdo do [`Setter`](xref:Xamarin.Forms.Setter) tipo, que é uma coleção de objetos. Cada `Setter` um [`Property`](xref:Xamarin.Forms.Setter.Property) associa [`Value`](xref:Xamarin.Forms.Setter.Value)um com um .

Em XAML, a `Property` configuração é o nome `Text` de `Button`uma propriedade CLR (como a propriedade de ) mas a propriedade estilizada deve ser apoiada por uma propriedade vinculável. Além disso, a propriedade deve ser definida `TargetType` na classe indicada pela configuração, ou herdada por essa classe.

Você pode `Value` especificar a `<Setter.Value>`configuração usando o elemento propriedade . Isso permite que `Value` você defina um objeto que não pode ser `OnPlatform` expresso em uma seqüência `x:Arguments` `x:FactoryMethod`de texto, ou em um objeto, ou em um objeto instanciado usando ou . A `Value` propriedade também pode `StaticResource` ser definida com uma expressão para outro item no dicionário.

O programa [**BasicStyle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyle) demonstra a sintaxe `Style` básica `StaticResource` e mostra como fazer referência ao com uma extensão de marcação:

[![Captura de tela tripla do estilo básico](images/ch12fg01-small.png "Estilos básicos")](images/ch12fg01-large.png#lightbox "Estilos básicos")

O `Style` objeto e qualquer objeto `Style` criado `Value` no objeto como uma configuração `Style`são compartilhados entre todas as exibições referentes a isso . O `Style` não pode conter nada que não `View` possa ser compartilhado, como uma derivada.

Os manipuladores de eventos `Style`não podem ser definidos em um . A `GestureRecognizers` propriedade não pode `Style` ser definida em um porque não é apoiada por uma propriedade vinculável.

## <a name="styles-in-code"></a>Estilos em código

Embora não seja comum, você pode `Style` instanciar e inicializar objetos em código. Isso é demonstrado pela amostra [**BasicStyleCode.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyleCode)

## <a name="style-inheritance"></a>Herança de estilo

`Style`tem [`BasedOn`](xref:Xamarin.Forms.Style.BasedOn) uma propriedade que você `StaticResource` pode definir para uma extensão de marcação fazendo referência a outro estilo. Isso permite que os estilos herdem de estilos anteriores e adicionem ou substituam as configurações de propriedade. A amostra [**StyleHer**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleInheritance) demonstra isso.

Se `Style2` for `Style1`baseado `TargetType` em `Style2` , o `Style1` de deve `Style1`ser o mesmo que ou derivado de . O dicionário de `Style1` recursos em que é `Style2` armazenado deve ser o mesmo dicionário de recursos ou um dicionário de recursos mais alto na árvore visual.

## <a name="implicit-styles"></a>Estilos implícitos

Se `Style` um dicionário de recursos `x:Key` não tiver uma configuração de atributo, `Style` ele será atribuído automaticamente a uma chave de dicionário, e o objeto se tornará um *estilo implícito*. Uma exibição `Style` sem uma `TargetType` configuração e cujo tipo corresponde exatamente encontrará esse estilo, como a amostra [**ImplicitStyle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/ImplicitStyle) demonstra.

Um estilo implícito `Style` pode `x:Key` derivar de um com uma configuração, mas não o contrário. Você não pode referenciar explicitamente um estilo implícito.

Você pode implementar três tipos de `BasedOn`hierarquia com estilos e:

- Desde estilos definidos no `Application` e `Page` até estilos definidos em layouts mais baixos na árvore visual.
- Desde estilos definidos para `VisualElement` `View` classes básicas como e estilos definidos para classes específicas.
- Desde estilos com teclas explícitas de dicionário até estilos implícitos.

Essas hierarquias são demonstradas na amostra [**StyleHierarchy.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleHierarchy)

## <a name="dynamic-styles"></a>Estilos dinâmicos

Um estilo em um dicionário de `DynamicResource` recursos `StaticResource`pode ser referenciado por em vez de . Isso faz do estilo um *estilo dinâmico.* Se esse estilo for substituído no dicionário de recursos por outro estilo com `DynamicResource` a mesma tecla, as visualizações referenciam esse estilo com mudança automática. Além disso, a ausência de uma entrada `StaticResource` de dicionário com `DynamicResource`a chave especificada fará com que a exceção seja uma exceção, mas não .

Você pode usar essa técnica para mudar dinamicamente o estilo ou os temas como a amostra [**DynamicStyles**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynamicStyles) demonstra.

No entanto, `BasedOn` você não `DynamicResource` pode `BasedOn` definir a propriedade como uma extensão de maquiagem porque não é apoiada por uma propriedade vinculável. Para derivar um estilo dinamicamente, não defina `BasedOn`. Em vez [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) disso, defina a propriedade para a tecla do dicionário do estilo que você deseja derivar. A amostra [**DynamicStylesInheritance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynaStylesInh) demonstra essa técnica.

## <a name="device-styles"></a>Estilos de dispositivo

A [`Device.Styles`](xref:Xamarin.Forms.Device.Styles) classe aninhada define doze campos somente de `TargetType` `Label` leitura estática para seis estilos com um dos quais você pode usar para tipos comuns de uso de texto.

Seis desses campos são `Style` do tipo que `Style` você pode definir diretamente para uma propriedade em código:

- [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle)
- [`TitleStyle`](xref:Xamarin.Forms.Device.Styles.TitleStyle)
- [`SubtitleStyle`](xref:Xamarin.Forms.Device.Styles.SubtitleStyle)
- [`CaptionStyle`](xref:Xamarin.Forms.Device.Styles.CaptionStyle)
- [`ListItemTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyle)
- [`ListItemDetailTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyle)

Os outros seis campos `string` são de tipo e podem ser usados como chaves de dicionário para estilos dinâmicos:

- [`BodyStyleKey`](xref:Xamarin.Forms.Device.Styles.BodyStyleKey)igual a "BodyStyle"
- [`TitleStyleKey`](xref:Xamarin.Forms.Device.Styles.TitleStyleKey)igual a "TitleStyle"
- [`SubtitleStyleKey`](xref:Xamarin.Forms.Device.Styles.SubtitleStyleKey)igual a "SubtitleStyle"
- [`CaptionStyleKey`](xref:Xamarin.Forms.Device.Styles.CaptionStyleKey)igual a "CaptionStyle"
- [`ListItemTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyleKey)igual a "ListItemTextStyle"
- [`ListItemDetailTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyleKey)igual a "ListItemDetailTextStyle"

Esses estilos são ilustrados pela amostra [**DeviceStylesList.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DeviceStylesList)

## <a name="related-links"></a>Links relacionados

- [Capítulo 12 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch12-Apr2016.pdf)
- [Capítulo 12 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)
- [Estilos](~/xamarin-forms/user-interface/styles/index.md)
