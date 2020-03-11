---
title: Resumo do Capítulo 11. A infraestrutura associável
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do Capítulo 11. A infraestrutura associável'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 34671C48-0ED4-4B76-A33D-D6505390DC5B
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: f9e3326c0f55469cfa84a019a674679d82dfc007
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "61334279"
---
# <a name="summary-of-chapter-11-the-bindable-infrastructure"></a>Resumo do Capítulo 11. A infraestrutura associável

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)

Cada C# programador está familiarizado com C# *as propriedades*. As propriedades contêm um acessador *set* e/ou um acessador *Get* . Geralmente, eles são chamados de *Propriedades CLR* para o Common Language Runtime.

O Xamarin. Forms define uma definição de propriedade avançada chamada de *propriedade vinculável* encapsulada pela classe [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) e com suporte da classe [`BindableObject`](xref:Xamarin.Forms.BindableObject) . Essas classes são relacionadas, mas bem distintas: a `BindableProperty` é usada para definir a propriedade em si; `BindableObject` é como `object`, pois é uma classe base para classes que definem propriedades vinculáveis.

## <a name="the-xamarinforms-class-hierarchy"></a>A hierarquia de classes xamarin. Forms

O exemplo [**ClassHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/ClassHierarchy) usa a reflexão para exibir uma hierarquia de classe do Xamarin. Forms e demonstra a função crucial desempenhada por `BindableObject` nessa hierarquia. `BindableObject` deriva de `Object` e é a classe pai a ser [`Element`](xref:Xamarin.Forms.Element) da qual [`VisualElement`](xref:Xamarin.Forms.VisualElement) deriva. Essa é a classe pai para [`Page`](xref:Xamarin.Forms.Page) e [`View`](xref:Xamarin.Forms.View), que é a classe pai a [`Layout`](xref:Xamarin.Forms.Layout):

[![Captura de tela tripla de compartilhamento de hierarquia de classe](images/ch11fg01-small.png "Compartilhamento de hierarquia de classes")](images/ch11fg01-large.png#lightbox "Compartilhamento de hierarquia de classes")

## <a name="a-peek-into-bindableobject-and-bindableproperty"></a>Uma espiada no BindableObject e BindableProperty

Nas classes que derivam de `BindableObject` muitas propriedades CLR são consideradas "apoiadas por" propriedades vinculáveis. Por exemplo, a propriedade [`Text`](xref:Xamarin.Forms.Label.Text) da classe `Label` é uma propriedade CLR, mas a classe `Label` também define um campo somente leitura estático público chamado [`TextProperty`](xref:Xamarin.Forms.Label.TextProperty) do tipo `BindableProperty`.

Um aplicativo pode definir ou obter a propriedade `Text` de `Label` normalmente, ou o aplicativo pode definir o `Text` chamando o método [`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) definido por `BindableObject` com um argumento `Label.TextProperty`. Da mesma forma, um aplicativo pode obter o valor da propriedade `Text` chamando o método [`GetValue`](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty)) , novamente com um argumento `Label.TextProperty`. Isso é demonstrado pelo exemplo de [**PropertySettings**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PropertySettings) .

Na verdade, a propriedade `Text` CLR é totalmente implementada usando os métodos `SetValue` e `GetValue` definidos pelo `BindableObject` em conjunto com a propriedade estática `Label.TextProperty`.

`BindableObject` e `BindableProperty` oferecem suporte para:

- Fornecendo valores padrão de propriedades
- Armazenando seus valores atuais
- Fornece mecanismos para validar valores de propriedade
- Manter a consistência entre as propriedades relacionadas em uma única classe
- Responder às alterações de propriedade
- Disparando notificações quando uma propriedade está prestes a ser alterada ou foi alterada
- Suporte a vinculação de dados
- Suporte a estilos
- Suporte a recursos dinâmicos

Sempre que uma propriedade que é apoiada por uma propriedade vinculável é alterada, `BindableObject` aciona um evento [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) identificando a propriedade que foi alterada. Esse evento não é acionado quando a propriedade é definida com o mesmo valor.

Algumas propriedades não são apoiadas por propriedades vinculáveis e algumas classes Xamarin. Forms &mdash; como `Span` &mdash; não derivam de `BindableObject`. Somente uma classe derivada de `BindableObject` pode dar suporte a propriedades vinculáveis porque `BindableObject` define os métodos `SetValue` e `GetValue`.

Como `Span` não deriva de `BindableObject`, nenhuma de suas propriedades &mdash; como `Text` &mdash; são apoiadas por uma propriedade vinculável. É por isso que uma configuração de `DynamicResource` na propriedade `Text` de `Span` gera uma exceção no exemplo de [**DynamicVsStatic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic) no capítulo anterior. O exemplo [**DynamicVsStaticCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/DynamicVsStaticCode) demonstra como definir recursos dinâmicos no código usando o método [`SetDynamicResource`](xref:Xamarin.Forms.Element.SetDynamicResource(Xamarin.Forms.BindableProperty,System.String)) definido por `Element`. O primeiro argumento é um objeto do tipo `BindableProperty`.

Da mesma forma, o método de [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) definido por `BindableObject` tem um primeiro argumento do tipo `BindableProperty`.

## <a name="defining-bindable-properties"></a>Definindo propriedades vinculáveis

Você pode definir suas próprias propriedades vinculáveis usando o método estático [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) para criar um campo somente leitura estático do tipo `BindableProperty`.

Isso é demonstrado na classe [`AltLabel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AltLabel.cs) na biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) . A classe deriva de `Label` e permite que você especifique um tamanho de fonte em pontos. Ele é demonstrado no exemplo de [**PointSizedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PointSizedText) .

São necessários quatro argumentos do método `BindableProperty.Create`:

- `propertyName`: o nome de texto da propriedade (o mesmo que o nome da propriedade CLR)
- `returnType`: o tipo da propriedade CLR
- `declaringType`: o tipo da classe que declara a propriedade
- `defaultValue`: o valor padrão da propriedade

Como `defaultValue` é do tipo `object`, o compilador deve ser capaz de determinar o tipo do valor padrão. Por exemplo, se o `returnType` for `double`, o `defaultValue` deverá ser definido como algo como 0,0 em vez de apenas 0, ou a incompatibilidade de tipo disparará uma exceção em tempo de execução.

Também é muito comum para uma propriedade associável incluem:

- `propertyChanged`: um método estático chamado quando a propriedade muda de valor. O primeiro argumento é a instância da classe cuja propriedade foi alterada.

Os outros argumentos para `BindableProperty.Create` não são tão comuns:

- `defaultBindingMode`: usado em conexão com a associação de dados (conforme discutido no [**capítulo 16. Vinculação de dados**](chapter16.md))
- `validateValue`: um retorno de chamada para verificar um valor válido
- `propertyChanging`: um retorno de chamada para indicar quando a propriedade está prestes a ser alterada
- `coerceValue`: um retorno de chamada para forçar um valor definido para outro valor
- `defaultValueCreate`: um retorno de chamada para criar um valor padrão que não pode ser compartilhado entre instâncias da classe (por exemplo, uma coleção)

### <a name="the-read-only-bindable-property"></a>A propriedade associável somente leitura

Uma propriedade associável pode ser somente leitura. A criação de uma propriedade vinculável somente leitura requer chamar o método estático [`BindableProperty.CreateReadOnly`](xref:Xamarin.Forms.BindableProperty.CreateReadOnly(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) para definir um campo somente leitura estático privado do tipo [`BindablePropertyKey`](xref:Xamarin.Forms.BindablePropertyKey).

Em seguida, defina a propriedade CLR `set` o acessador como `private` para chamar uma sobrecarga de [`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindablePropertyKey,System.Object)) com o objeto `BindablePropertyKey`. Isso impede que a propriedade sendo definida fora da classe.

Isso é demonstrado na classe [`CountedLabel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CountedLabel.cs) usada no exemplo [**BaskervillesCount**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/BaskervillesCount) .

## <a name="related-links"></a>Links relacionados

- [Capítulo 11 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch11-Apr2016.pdf)
- [Capítulo 11 exemplos](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)
- [Propriedades vinculáveis](~/xamarin-forms/xaml/bindable-properties.md)
