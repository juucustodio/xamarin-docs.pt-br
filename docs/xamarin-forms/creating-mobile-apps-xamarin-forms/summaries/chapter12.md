---
title: Resumo do capítulo 12. Estilos
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 12. Estilos'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3EAE6BDC-8EFB-464B-A87B-1C35B8387BB3
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: bb2cd1c97cc588923e0da1a8793f16445c111f0e
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53058841"
---
# <a name="summary-of-chapter-12-styles"></a>Resumo do capítulo 12. Estilos

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)

No xamarin. Forms, os estilos permitem vários modos de exibição compartilhar uma coleção de configurações de propriedade. Isso reduz a marcação e permite que temas visuais consistentes de manutenção.

Estilos de quase sempre são definidos e consumidos na marcação. Um objeto do tipo [ `Style` ](xref:Xamarin.Forms.Style) é instanciado em um dicionário de recursos e, em seguida, defina como o [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) propriedade de um elemento visual usando um `StaticResource` ou `DynamicResource` marcação extensão.

## <a name="the-basic-style"></a>O estilo básico

Um `Style` requer que seu [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) ser definido como o tipo do objeto visual, ele se aplica ao. Quando um `Style` é instanciado em um dicionário de recursos (como é comum) também requer um `x:Key` atributo.

O `Style` tem uma propriedade content do tipo [ `Setters` ](xref:Xamarin.Forms.Style.Setters), que é uma coleção de [ `Setter` ](xref:Xamarin.Forms.Setter) objetos. Cada `Setter` associa um [ `Property` ](xref:Xamarin.Forms.Setter.Property) com um [ `Value` ](xref:Xamarin.Forms.Setter.Value).

No XAML a `Property` configuração é o nome de uma propriedade CLR (como o `Text` propriedade de `Button`), mas a propriedade com estilo deve ser feita por uma propriedade associável. Além disso, a propriedade deve ser definida na classe indicada pela `TargetType` configuração ou herdadas por essa classe.

Você pode especificar o `Value` usando o elemento de propriedade de configuração `<Setter.Value>`. Isso permite que você defina `Value` a um objeto que não pode ser expressos em uma cadeia de caracteres de texto, ou como um `OnPlatform` do objeto ou a um objeto instanciado usando `x:Arguments` ou `x:FactoryMethod`. O `Value` propriedade também pode ser definida com um `StaticResource` expressão para outro item no dicionário.

O [ **BasicStyle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyle) demonstra a sintaxe básica de programa e mostra como fazer referência a `Style` com um `StaticResource` extensão de marcação:

[![Captura de tela tripla de estilo básico](images/ch12fg01-small.png "estilos básicos")](images/ch12fg01-large.png#lightbox "estilos básicos")

O `Style` objeto e qualquer objeto criado na `Style` do objeto como um `Value` configuração são compartilhados entre todas as exibições, fazendo referência a ela `Style`. O `Style` não pode conter qualquer coisa que não podem ser compartilhados, como um `View` derivado.

Manipuladores de eventos não podem ser definidos um `Style`. O `GestureRecognizers` propriedade não pode ser definida um `Style` porque ele não é feito por uma propriedade associável.

## <a name="styles-in-code"></a>Estilos de código

Embora não seja comum, você pode instanciar e inicializar `Style` objetos no código. Isso é demonstrado pelo [ **BasicStyleCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyleCode) exemplo.

## <a name="style-inheritance"></a>Herança de estilo

`Style` tem um [ `BasedOn` ](xref:Xamarin.Forms.Style.BasedOn) propriedade que pode ser definida para um `StaticResource` referenciando outro estilo de extensão de marcação. Isso permite que os estilos de herdar de estilos anteriores e adicionar ou substituir as configurações de propriedade. O [ **StyleInheritance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleInheritance) demonstra isso.

Se `Style2` se baseia `Style1`, o `TargetType` de `Style2` deve ser o mesmo que `Style1` ou derivado de `Style1`. O dicionário de recursos no qual `Style1` é armazenado deve ser o mesmo dicionário de recursos como `Style2` ou um dicionário de recursos superior na árvore visual.

## <a name="implicit-styles"></a>Estilos implícitos

Se um `Style` em um recurso de dicionário não tem um `x:Key` atributo de configuração, ele é atribuído a uma chave de dicionário automaticamente e o `Style` objeto se torna um *estilo implícito*. Um modo de exibição sem um `Style` configuração e cujo tipo corresponde a `TargetType` exatamente encontrará esse estilo, como o [ **ImplicitStyle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/ImplicitStyle) demonstra.

Um estilo implícito pode derivar de uma `Style` com um `x:Key` configuração, mas não o oposto. Você não pode referenciar explicitamente um estilo implícito.

Você pode implementar os três tipos de hierarquia com estilos e `BasedOn`:

- Os estilos definidos na `Application` e `Page` para baixo até os estilos definidos nos layouts inferiores na árvore visual.
- De estilos definidos para as classes base, como `VisualElement` e `View` para os estilos definidos para as classes específicas.
- De estilos com chaves de dicionário explícita para estilos implícitos.

Essas hierarquias são demonstradas a [ **StyleHierarchy** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleHierarchy) exemplo.

## <a name="dynamic-styles"></a>Estilos dinâmicos

Um estilo em um dicionário de recursos pode ser referenciado por `DynamicResource` em vez de `StaticResource`. Isso faz com que o estilo de um *estilos dinâmica*. Se esse estilo é substituído no dicionário de recursos por outro estilo com a mesma chave, os modos de exibição referenciando esse estilo com `DynamicResource` é alterada automaticamente. Além disso, a ausência de uma entrada no dicionário com a chave especificada fará com que `StaticResource` para gerar uma exceção, mas não `DynamicResource`.

Você pode usar essa técnica para alterar dinamicamente o estilo ou temas como o [ **DynamicStyles** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynamicStyles) demonstra.

No entanto, não é possível definir a `BasedOn` propriedade para um `DynamicResource` extensão de composição porque `BasedOn` não é apoiada por uma propriedade associável. Para derivar um estilo dinamicamente, não defina `BasedOn`. Em vez disso, defina as [ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey) propriedade para a chave do dicionário do estilo que você deseja derivar de. O [ **DynamicStylesInheritance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynaStylesInh) que demonstra essa técnica.

## <a name="device-styles"></a>Estilos de dispositivo

O [ `Device.Styles` ](xref:Xamarin.Forms.Device.Styles) classe aninhada define doze campos estáticos somente leitura para seis estilos com um `TargetType` de `Label` que você pode usar para tipos comuns de usos de texto.

Seis desses campos são do tipo `Style` que você pode definir diretamente a um `Style` propriedade no código:

- [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle)
- [`TitleStyle`](xref:Xamarin.Forms.Device.Styles.TitleStyle)
- [`SubtitleStyle`](xref:Xamarin.Forms.Device.Styles.SubtitleStyle)
- [`CaptionStyle`](xref:Xamarin.Forms.Device.Styles.CaptionStyle)
- [`ListItemTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyle)
- [`ListItemDetailTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyle)

Seis campos são do tipo `string` e podem ser usados como chaves de dicionário para estilos dinâmicos:

- [`BodyStyleKey`](xref:Xamarin.Forms.Device.Styles.BodyStyleKey) igual a "BodyStyle"
- [`TitleStyleKey`](xref:Xamarin.Forms.Device.Styles.TitleStyleKey) igual a "TitleStyle"
- [`SubtitleStyleKey`](xref:Xamarin.Forms.Device.Styles.SubtitleStyleKey) igual a "SubtitleStyle"
- [`CaptionStyleKey`](xref:Xamarin.Forms.Device.Styles.CaptionStyleKey) igual a "CaptionStyle"
- [`ListItemTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyleKey) igual a "ListItemTextStyle"
- [`ListItemDetailTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyleKey) igual a "ListItemDetailTextStyle"

Esses estilos são ilustrados pela [ **DeviceStylesList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DeviceStylesList) exemplo.

## <a name="related-links"></a>Links relacionados

- [Capítulo 12 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch12-Apr2016.pdf)
- [Exemplos do capítulo 12](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)
- [Estilos](~/xamarin-forms/user-interface/styles/index.md)
