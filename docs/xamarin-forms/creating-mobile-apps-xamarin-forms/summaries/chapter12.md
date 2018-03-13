---
title: "Resumo do capítulo 12. Estilos"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3EAE6BDC-8EFB-464B-A87B-1C35B8387BB3
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 9563bc811250038e8932067280a8e5292a379077
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="summary-of-chapter-12-styles"></a>Resumo do capítulo 12. Estilos

Em xamarin. Forms, estilos permitem vários modos de exibição compartilhar uma coleção de configurações de propriedade. Isso reduz a marcação e permite manter consistentes temas visuais.

Estilos quase sempre são definidos e consumidos na marcação. Um objeto do tipo [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) é instanciado em um dicionário de recursos e, em seguida, defina como o [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propriedade de um elemento visual usando um `StaticResource` ou `DyanamicResource` marcação extensão.

## <a name="the-basic-style"></a>O estilo básico

Um `Style` requer que seu [ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/) ser definido como o tipo do objeto visual aplica-se a. Quando um `Style` é instanciado em um dicionário de recursos (como é comum) também requer um `x:Key` atributo.

O `Style` tem uma propriedade de conteúdo do tipo [ `Setters` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.Setters/), que é uma coleção de [ `Setter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/) objetos. Cada `Setter` associa um [ `Property` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Setter.Property/) com um [ `Value` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Setter.Value/).

Em XAML o `Property` configuração é o nome de uma propriedade CLR (como o `Text` propriedade `Button`), mas a propriedade de estilo deve ser feita por uma propriedade associável. Além disso, a propriedade deve ser definida na classe indicada pelo `TargetType` configuração ou herdadas por essa classe.

Você pode especificar o `Value` configuração usando o elemento de propriedade `<Setter.Value>`. Isso permite que você defina `Value` a um objeto que não pode ser expressa em uma cadeia de caracteres de texto, ou para um `OnPlatform` de objeto ou a um objeto instanciado usando `x:Arguments` ou `x:FactoryMethod`. O `Value` propriedade também pode ser definida com um `StaticResource` expressão para outro item no dicionário.

O [ **BasicStyle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyle) demonstra a sintaxe básica de programa e mostra como referenciar o `Style` com um `StaticResource` extensão de marcação:

[![Captura de tela tripla de estilo básico](images/ch12fg01-small.png "estilos básicos")](images/ch12fg01-large.png#lightbox "estilos básicos")

O `Style` objeto e qualquer objeto criado no `Style` de objeto como um `Value` configuração são compartilhadas entre todas as exibições que referenciam que `Style`. O `Style` não pode conter qualquer coisa que não podem ser compartilhados, como um `View` derivado.

Não não possível definir manipuladores de eventos um `Style`. O `GestureRecognizers` propriedade não pode ser definida um `Style` porque uma propriedade ligável não tem suporte.

## <a name="styles-in-code"></a>Estilos no código

Embora não seja comum, você pode criar uma instância e inicializar `Style` objetos no código. Isso é demonstrado pelo [ **BasicStyleCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyleCode) exemplo.

## <a name="style-inheritance"></a>Herança de estilo

`Style` tem um [ `BasedOn` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BasedOn/) propriedade que pode ser definida para um `StaticResource` fazendo referência a outro estilo de extensão de marcação. Isso permite que os estilos herdar de estilos anteriores e adicionar ou substituir as configurações de propriedade. O [ **StyleInheritance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleInheritance) exemplo demonstra isso.

Se `Style2` baseia-se em `Style1`, o `TargetType` de `Style2` deve ser o mesmo que `Style1` ou derivado de `Style1`. O dicionário de recursos no qual `Style1` é armazenado deve ser o mesmo dicionário de recurso como `Style2` ou um dicionário de recursos superior na árvore visual.

## <a name="implicit-styles"></a>Estilos implícitos

Se um `Style` em um recurso de dicionário não tem um `x:Key` atributo de configuração, ele é atribuído a uma chave de dicionário automaticamente e o `Style` objeto se torna um *estilo implícito*. Um modo de exibição sem um `Style` configuração e cujo tipo corresponda a `TargetType` exatamente encontrará esse estilo, como o [ **ImplicitStyle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/ImplicitStyle) demonstra.

Um estilo implícito pode derivar de um `Style` com um `x:Key` configuração, mas não o oposto. Você não pode fazer referência explícita um estilo implícito.

Você pode implementar os três tipos de hierarquia com estilos e `BasedOn`:

- Estilos definidos no `Application` e `Page` até os estilos definidos na parte inferior da árvore visual de layouts.
- Estilos definidos para as classes base como `VisualElement` e `View` para os estilos definidos para classes específicas.
- Estilos com chaves de dicionário explícita para estilos implícitos.

Essas hierarquias são demonstradas no [ **StyleHierarchy** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleHierarchy) exemplo.

## <a name="dynamic-styles"></a>Estilos dinâmicos

Um estilo de um dicionário de recursos pode ser referenciado por `DynamicResource` em vez de `StaticResource`. Isso faz com que o estilo de um *estilos dinâmica*. Se esse estilo for substituído no dicionário de recurso por outro estilo com a mesma chave, as exibições que referenciam esse estilo com `DynamicResource` alterar automaticamente. Além disso, a ausência de uma entrada de dicionário com a chave especificada fará com que `StaticResource` para gerar uma exceção, mas não `DynamicResource`.

Você pode usar essa técnica para alterar dinamicamente estilo ou temas como o [ **DynamicStyles** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynamicStyles) demonstra.

No entanto, não é possível definir o `BasedOn` propriedade para um `DynamicResource` extensão composição porque `BasedOn` não tem o apoio de uma propriedade ligável. Para derivar um estilo dinamicamente, não defina `BasedOn`. Em vez disso, defina o [ `BaseResourceKey` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BaseResourceKey/) propriedade para a chave de dicionário do estilo a ser derivam. O [ **DynamicStylesInheritance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynaStylesInh) demonstra essa técnica.

## <a name="device-styles"></a>Estilos de dispositivo

O [ `Device.Styles` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Device+Styles/) classe aninhada define doze campos estáticos somente leitura para seis estilos com um `TargetType` de `Label` que você pode usar para tipos comuns de usos de texto.

Seis esses campos são do tipo `Style` que você pode definir diretamente a um `Style` propriedade no código:

- [`BodyStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.BodyStyle/)
- [`TitleStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.TitleStyle/)
- [`SubtitleStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.SubtitleStyle/)
- [`CaptionStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.CaptionStyle/)
- [`ListItemTextStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.ListItemTextStyle/)
- [`ListItemDetailTextStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.ListItemDetailTextStyle/)

Seis campos são do tipo `string` e podem ser usadas como chaves de dicionário de estilos dinâmicas:

- [`BodyStyleKey`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.BodyStyleKey/) igual a "BodyStyle"
- [`TitleStyleKey`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.TitleStyleKey/) igual a "TitleStyle"
- [`SubtitleStyleKey`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.SubtitleStyleKey/) igual a "SubtitleStyle"
- [`CaptionStyleKey`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.CaptionStyleKey/) igual a "CaptionStyle"
- [`ListItemTextStyleKey`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.ListItemTextStyleKey/) igual a "ListItemTextStyle"
- [`ListItemDetailTextStyleKey`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.ListItemDetailTextStyleKey/) igual a "ListItemDetailTextStyle"

Esses estilos são ilustrados pelo [ **DeviceStylesList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DeviceStylesList) exemplo.



## <a name="related-links"></a>Links relacionados

- [Texto completo do capítulo 12 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch12-Apr2016.pdf)
- [Exemplos de capítulo 12](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)
- [Estilos](~/xamarin-forms/user-interface/styles/index.md)
