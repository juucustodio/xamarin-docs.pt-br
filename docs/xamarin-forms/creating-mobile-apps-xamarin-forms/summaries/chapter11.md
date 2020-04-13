---
title: Resumo do Capítulo 11. A infra-estrutura vinculável
description: 'Criando aplicativos móveis com Xamarin.Forms: Resumo do Capítulo 11. A infra-estrutura vinculável'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 34671C48-0ED4-4B76-A33D-D6505390DC5B
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: f9e3326c0f55469cfa84a019a674679d82dfc007
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "61334279"
---
# <a name="summary-of-chapter-11-the-bindable-infrastructure"></a>Resumo do Capítulo 11. A infra-estrutura vinculável

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)

Todo programador C# está familiarizado com *as propriedades*C# . As propriedades contêm um acessório *definido* e/ou um acessório *get.* Eles são frequentemente chamados *de propriedades CLR* para o Tempo de Execução da Linguagem Comum.

Xamarin.Forms define uma definição de propriedade aprimorada chamada *propriedade vinculável* encapsulada pela [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) classe e suportada pela [`BindableObject`](xref:Xamarin.Forms.BindableObject) classe. Essas classes estão relacionadas, `BindableProperty` mas bastante distintas: A é usada para definir a propriedade em si; `BindableObject` é `object` como em que é uma classe base para classes que definem propriedades vinculáveis.

## <a name="the-xamarinforms-class-hierarchy"></a>A hierarquia de classe Xamarin.Forms

A amostra [**ClassHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/ClassHierarchy) usa a reflexão para exibir uma hierarquia de classe `BindableObject` de Xamarin.Forms e demonstrar o papel crucial desempenhado por nesta hierarquia. `BindableObject`deriva e `Object` é a classe [`Element`](xref:Xamarin.Forms.Element) dos [`VisualElement`](xref:Xamarin.Forms.VisualElement) pais para a qual deriva. Esta é a [`Page`](xref:Xamarin.Forms.Page) classe [`View`](xref:Xamarin.Forms.View)dos pais para [`Layout`](xref:Xamarin.Forms.Layout)e , que é a classe dos pais para :

[![Captura de tela tripla do compartilhamento de hierarquia de classes](images/ch11fg01-small.png "Compartilhamento de hierarquia de classes")](images/ch11fg01-large.png#lightbox "Compartilhamento de hierarquia de classes")

## <a name="a-peek-into-bindableobject-and-bindableproperty"></a>Uma espiada em BindableObject e BindableProperty

Nas classes que `BindableObject` derivam de muitas propriedades CLR são ditos como "apoiados" propriedades vinculáveis. Por exemplo, [`Text`](xref:Xamarin.Forms.Label.Text) a `Label` propriedade da classe é uma `Label` propriedade CLR, mas a classe [`TextProperty`](xref:Xamarin.Forms.Label.TextProperty) também `BindableProperty`define um campo público só de leitura estática chamado de tipo .

Um aplicativo pode definir `Text` ou `Label` obter a propriedade de `Text` normalmente, ou o aplicativo pode definir o chamando o [`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) método definido por `BindableObject` um `Label.TextProperty` argumento. Da mesma forma, um aplicativo `Text` pode obter [`GetValue`](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty)) o valor do `Label.TextProperty` imóvel chamando o método, novamente com um argumento. Isso é demonstrado pela amostra [**PropertySettings.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PropertySettings)

De fato, a `Text` propriedade CLR `SetValue` é `GetValue` inteiramente `BindableObject` implementada usando `Label.TextProperty` os métodos definidos em conjunto com a propriedade estática.

`BindableObject`e `BindableProperty` fornecer suporte para:

- Dando valores padrão de propriedades
- Armazenando seus valores atuais
- Fornecendo mecanismos para validação de valores de propriedade
- Mantendo a consistência entre as propriedades relacionadas em uma única classe
- Respondendo a alterações de propriedade
- Acionando notificações quando uma propriedade está prestes a mudar ou mudou
- Suporte à vinculação de dados
- Estilos de suporte
- Suporte a recursos dinâmicos

Sempre que uma propriedade que é apoiada `BindableObject` por [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) uma propriedade vinculável muda, dispara um evento identificando a propriedade que mudou. Este evento não é acionado quando a propriedade é definida para o mesmo valor.

Algumas propriedades não são apoiadas por propriedades vinculáveis, &mdash; e `Span` &mdash; algumas classes `BindableObject`xamarin.forms, como não derivam de . Apenas uma classe que `BindableObject` deriva pode suportar `BindableObject` propriedades `SetValue` vinculáveis porque define os métodos. `GetValue`

Porque `Span` não deriva `BindableObject`de , &mdash; nenhuma `Text` &mdash; de suas propriedades, como são apoiados por uma propriedade vinculável. É por `DynamicResource` isso que `Text` uma `Span` configuração na propriedade de levanta uma exceção na amostra [**DynamicVsStatic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic) no capítulo anterior. A amostra [**DynamicVsStaticCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/DynamicVsStaticCode) demonstra como definir um [`SetDynamicResource`](xref:Xamarin.Forms.Element.SetDynamicResource(Xamarin.Forms.BindableProperty,System.String)) recurso dinâmico `Element`em código usando o método definido por . O primeiro argumento é `BindableProperty`um objeto de tipo .

Da mesma [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) forma, `BindableObject` o método definido `BindableProperty`por tem um primeiro argumento de tipo .

## <a name="defining-bindable-properties"></a>Definindo propriedades vinculáveis

Você pode definir suas próprias [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) propriedades vinculáveis usando o método `BindableProperty`estático para criar um campo de tipo somente de leitura estática .

Isso é demonstrado [`AltLabel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AltLabel.cs) na classe na biblioteca [**Xamarin.FormsBook.Toolkit.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) A classe deriva `Label` e permite que você especifique um tamanho de fonte em pontos. É demonstrado na amostra [**PointSizedText.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PointSizedText)

Quatro argumentos `BindableProperty.Create` do método são necessários:

- `propertyName`: o nome do texto da propriedade (o mesmo que o nome da propriedade CLR)
- `returnType`: o tipo de propriedade clr
- `declaringType`: o tipo de classe declarando a propriedade
- `defaultValue`: o valor padrão da propriedade

Por `defaultValue` ser `object`do tipo, o compilador deve ser capaz de determinar o tipo de valor padrão. Por exemplo, `returnType` se `double`o `defaultValue` é , o deve ser definido para algo como 0.0 em vez de apenas 0, ou a incompatibilidade de tipo desencadeará uma exceção em tempo de execução.

Também é muito comum que uma propriedade vinculável inclua:

- `propertyChanged`: um método estático chamado quando a propriedade muda de valor. O primeiro argumento é a instância da classe cuja propriedade foi alterada.

Os outros argumentos não `BindableProperty.Create` são tão comuns:

- `defaultBindingMode`: usado em conexão com a vinculação de dados (conforme discutido no [**capítulo 16. Vinculação de dados**](chapter16.md))
- `validateValue`: um retorno de chamada para verificar um valor válido
- `propertyChanging`: um retorno de chamada para indicar quando a propriedade está prestes a mudar
- `coerceValue`: um retorno de chamada para coagir um valor definido para outro valor
- `defaultValueCreate`: um retorno de chamada para criar um valor padrão que não pode ser compartilhado entre instâncias da classe (por exemplo, uma coleção)

### <a name="the-read-only-bindable-property"></a>A propriedade somente leitura evinculável

Uma propriedade vinculável pode ser somente leitura. Criar uma propriedade vinculável somente leitura [`BindableProperty.CreateReadOnly`](xref:Xamarin.Forms.BindableProperty.CreateReadOnly(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) requer chamar o método estático [`BindablePropertyKey`](xref:Xamarin.Forms.BindablePropertyKey)para definir um campo de leitura estática privado do tipo .

Em seguida, defina `set` o accesor de propriedade CLR como `private` para chamar uma [`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindablePropertyKey,System.Object)) sobrecarga com o `BindablePropertyKey` objeto. Isso impede que a propriedade seja definida fora da classe.

Isso é demonstrado [`CountedLabel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CountedLabel.cs) na classe usada na amostra [**BaskervillesCount.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/BaskervillesCount)

## <a name="related-links"></a>Links relacionados

- [Capítulo 11 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch11-Apr2016.pdf)
- [Capítulo 11 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)
- [Propriedades vinculáveis](~/xamarin-forms/xaml/bindable-properties.md)
