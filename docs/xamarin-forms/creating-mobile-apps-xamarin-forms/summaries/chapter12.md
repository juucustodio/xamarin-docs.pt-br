---
title: Resumo do capítulo 12. Estilos
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 12. Estilos'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3EAE6BDC-8EFB-464B-A87B-1C35B8387BB3
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 408f171a3c7c690b700f7be21a3dcaff503467d9
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "65926916"
---
# <a name="summary-of-chapter-12-styles"></a>Resumo do capítulo 12. Estilos

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)

No xamarin. Forms, os estilos permitem vários modos de exibição compartilhar uma coleção de configurações de propriedade. Isso reduz a marcação e permite que temas visuais consistentes de manutenção.

Estilos de quase sempre são definidos e consumidos na marcação. Um objeto do tipo [`Style`](xref:Xamarin.Forms.Style) é instanciado em um dicionário de recursos e, em seguida, definido como a propriedade [`Style`](xref:Xamarin.Forms.NavigableElement.Style) de um elemento visual usando uma `StaticResource` ou `DynamicResource` extensão de marcação.

## <a name="the-basic-style"></a>O estilo básico

Uma `Style` requer que seu [`TargetType`](xref:Xamarin.Forms.Style.TargetType) seja definido como o tipo do objeto visual ao qual se aplica. Quando um `Style` é instanciado em um dicionário de recursos (como é comum), ele também requer um atributo `x:Key`.

O `Style` tem uma propriedade content do tipo [`Setters`](xref:Xamarin.Forms.Style.Setters), que é uma coleção de objetos [`Setter`](xref:Xamarin.Forms.Setter) . Cada `Setter` associa um [`Property`](xref:Xamarin.Forms.Setter.Property) a um [`Value`](xref:Xamarin.Forms.Setter.Value).

No XAML, a configuração de `Property` é o nome de uma propriedade CLR (como a propriedade `Text` de `Button`), mas a propriedade com estilo deve ser apoiada por uma propriedade vinculável. Além disso, a propriedade deve ser definida na classe indicada pela configuração `TargetType` ou herdada por essa classe.

Você pode especificar a configuração de `Value` usando o elemento de propriedade `<Setter.Value>`. Isso permite que você defina `Value` a um objeto que não pode ser expresso em uma cadeia de caracteres de texto ou a um objeto `OnPlatform` ou a um objeto instanciado usando `x:Arguments` ou `x:FactoryMethod`. A propriedade `Value` também pode ser definida com uma expressão `StaticResource` para outro item no dicionário.

O programa [**basicstyle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyle) demonstra a sintaxe básica e mostra como fazer referência à `Style` com uma extensão de marcação `StaticResource`:

[![Captura de tela tripla de estilo básico](images/ch12fg01-small.png "Estilos básicos")](images/ch12fg01-large.png#lightbox "Estilos básicos")

O objeto `Style` e qualquer objeto criado no objeto `Style` como uma configuração `Value` são compartilhados entre todas as exibições que fazem referência a esse `Style`. O `Style` não pode conter nada que não possa ser compartilhado, como um derivativo de `View`.

Não é possível definir manipuladores de eventos em um `Style`. A propriedade `GestureRecognizers` não pode ser definida em um `Style` porque não é apoiada por uma propriedade vinculável.

## <a name="styles-in-code"></a>Estilos de código

Embora não seja comum, você pode instanciar e inicializar `Style` objetos no código. Isso é demonstrado pelo exemplo de [**BasicStyleCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyleCode) .

## <a name="style-inheritance"></a>Herança de estilo

`Style` tem uma propriedade [`BasedOn`](xref:Xamarin.Forms.Style.BasedOn) que você pode definir como uma `StaticResource` extensão de marcação fazendo referência a outro estilo. Isso permite que os estilos de herdar de estilos anteriores e adicionar ou substituir as configurações de propriedade. O exemplo [**StyleInheritance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleInheritance) demonstra isso.

Se `Style2` for baseado em `Style1`, a `TargetType` de `Style2` deverá ser a mesma `Style1` ou derivada de `Style1`. O dicionário de recursos no qual `Style1` é armazenado deve ser o mesmo dicionário de recursos que `Style2` ou um dicionário de recursos mais alto na árvore visual.

## <a name="implicit-styles"></a>Estilos implícitos

Se uma `Style` em um dicionário de recursos não tiver uma configuração de atributo `x:Key`, ela será atribuída automaticamente a uma chave de dicionário e o objeto `Style` se tornará um *estilo implícito*. Uma exibição sem uma configuração de `Style` e cujo tipo corresponde ao `TargetType` exatamente encontrará esse estilo, como demonstra o exemplo de Type [**implícito**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/ImplicitStyle) .

Um estilo implícito pode derivar de um `Style` com uma configuração de `x:Key`, mas não o contrário. Você não pode referenciar explicitamente um estilo implícito.

Você pode implementar três tipos de hierarquia com estilos e `BasedOn`:

- Dos estilos definidos na `Application` e `Page` até os estilos definidos nos layouts inferiores na árvore visual.
- De estilos definidos para classes base, como `VisualElement` e `View` a estilos definidos para classes específicas.
- De estilos com chaves de dicionário explícita para estilos implícitos.

Essas hierarquias são demonstradas no exemplo [**StyleHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleHierarchy) .

## <a name="dynamic-styles"></a>Estilos dinâmicos

Um estilo em um dicionário de recursos pode ser referenciado por `DynamicResource` em vez de `StaticResource`. Isso torna o estilo um *estilo dinâmico*. Se esse estilo for substituído no dicionário de recursos por outro estilo com a mesma chave, as exibições que fazem referência a esse estilo com `DynamicResource` serão alteradas automaticamente. Além disso, a ausência de uma entrada de dicionário com a chave especificada fará com que `StaticResource` gere uma exceção, mas não `DynamicResource`.

Você pode usar essa técnica para alterar dinamicamente estilos ou temas, como demonstra o exemplo de [**DynamicStyles**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynamicStyles) .

No entanto, você não pode definir a propriedade `BasedOn` como uma extensão de composição de `DynamicResource` porque `BasedOn` não é apoiado por uma propriedade vinculável. Para derivar um estilo dinamicamente, não defina `BasedOn`. Em vez disso, defina a propriedade [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) como a chave de dicionário do estilo do qual você deseja derivar. O exemplo [**DynamicStylesInheritance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynaStylesInh) demonstra essa técnica.

## <a name="device-styles"></a>Estilos de dispositivo

A classe aninhada [`Device.Styles`](xref:Xamarin.Forms.Device.Styles) define doze campos somente leitura estáticos para seis estilos com uma `TargetType` de `Label` que você pode usar para tipos comuns de uso de texto.

Seis desses campos são do tipo `Style` que você pode definir diretamente para uma propriedade `Style` no código:

- [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle)
- [`TitleStyle`](xref:Xamarin.Forms.Device.Styles.TitleStyle)
- [`SubtitleStyle`](xref:Xamarin.Forms.Device.Styles.SubtitleStyle)
- [`CaptionStyle`](xref:Xamarin.Forms.Device.Styles.CaptionStyle)
- [`ListItemTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyle)
- [`ListItemDetailTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyle)

Os outros seis campos são do tipo `string` e podem ser usados como chaves de dicionário para estilos dinâmicos:

- [`BodyStyleKey`](xref:Xamarin.Forms.Device.Styles.BodyStyleKey) igual a "BodyStyle"
- [`TitleStyleKey`](xref:Xamarin.Forms.Device.Styles.TitleStyleKey) igual a "TitleStyle"
- [`SubtitleStyleKey`](xref:Xamarin.Forms.Device.Styles.SubtitleStyleKey) igual a "subtítulostyle"
- [`CaptionStyleKey`](xref:Xamarin.Forms.Device.Styles.CaptionStyleKey) igual a "captionstyle"
- [`ListItemTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyleKey) igual a "ListItemTextStyle"
- [`ListItemDetailTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyleKey) igual a "ListItemDetailTextStyle"

Esses estilos são ilustrados pelo exemplo de [**DeviceStylesList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DeviceStylesList) .

## <a name="related-links"></a>Links relacionados

- [Capítulo 12 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch12-Apr2016.pdf)
- [Capítulo 12 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)
- [Estilos](~/xamarin-forms/user-interface/styles/index.md)
