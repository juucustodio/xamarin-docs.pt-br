---
title: Resumo do capítulo 11. A infraestrutura vinculável
description: 'Criando aplicativos móveis com Xamarin.Forms : Resumo do capítulo 11. A infraestrutura vinculável'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 34671C48-0ED4-4B76-A33D-D6505390DC5B
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e2858d0606cf9c5c97a3457b5b29f620e7da2bad
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375129"
---
# <a name="summary-of-chapter-11-the-bindable-infrastructure"></a>Resumo do capítulo 11. A infraestrutura vinculável

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)

> [!NOTE]
> Este livro foi publicado na Primavera de 2016 e não foi atualizado desde então. Há muito no livro que permanece valioso, mas alguns materiais estão desatualizados e alguns tópicos não estão mais totalmente corretos ou completos.

Cada programador de C# está familiarizado com *as propriedades* do c#. As propriedades contêm um acessador *set* e/ou um acessador *Get* . Geralmente, eles são chamados de *Propriedades CLR* para o Common Language Runtime.

Xamarin.Forms define uma definição de propriedade avançada chamada uma *propriedade vinculável* encapsulada pela [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) classe e com suporte da [`BindableObject`](xref:Xamarin.Forms.BindableObject) classe. Essas classes são relacionadas, mas bem distintas: a `BindableProperty` é usada para definir a própria propriedade; `BindableObject` é como se `object` fosse uma classe base para classes que definem propriedades vinculáveis.

## <a name="the-no-locxamarinforms-class-hierarchy"></a>A Xamarin.Forms hierarquia de classe

O exemplo [**ClassHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/ClassHierarchy) usa reflexão para exibir uma hierarquia de classe de Xamarin.Forms e demonstrar a função crucial desempenhada por `BindableObject` nessa hierarquia. `BindableObject` deriva de `Object` e é a classe pai [`Element`](xref:Xamarin.Forms.Element) da qual [`VisualElement`](xref:Xamarin.Forms.VisualElement) deriva. Essa é a classe pai para [`Page`](xref:Xamarin.Forms.Page) e [`View`](xref:Xamarin.Forms.View) , que é a classe pai para [`Layout`](xref:Xamarin.Forms.Layout) :

[![Captura de tela tripla de compartilhamento de hierarquia de classe](images/ch11fg01-small.png "Compartilhamento de hierarquia de classes")](images/ch11fg01-large.png#lightbox "Compartilhamento de hierarquia de classes")

## <a name="a-peek-into-bindableobject-and-bindableproperty"></a>Uma inspeção em Bindobject e Vinculproperty

As classes que derivam de `BindableObject` muitas propriedades CLR são consideradas "apoiadas por" propriedades vinculáveis. Por exemplo, a [`Text`](xref:Xamarin.Forms.Label.Text) propriedade da `Label` classe é uma propriedade CLR, mas a `Label` classe também define um campo somente leitura estático público denominado [`TextProperty`](xref:Xamarin.Forms.Label.TextProperty) do tipo `BindableProperty` .

Um aplicativo pode definir ou obter a `Text` propriedade de `Label` normalmente, ou o aplicativo pode definir o `Text` chamando [ `SetValue` ] (xref: Xamarin.Forms . Acopláble. SetValue ( Xamarin.Forms . "Vinculproperty, System. Object)" definido por `BindableObject` um `Label.TextProperty` argumento. Da mesma forma, um aplicativo pode obter o valor da `Text` propriedade chamando [ `GetValue` ] (xref: Xamarin.Forms . Acopláble. GetValue ( Xamarin.Forms . Vinculproperty)), novamente com um `Label.TextProperty` argumento. Isso é demonstrado pelo exemplo de [**PropertySettings**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PropertySettings) .

Na verdade, a `Text` propriedade CLR é totalmente implementada usando os `SetValue` `GetValue` métodos e definidos por `BindableObject` em conjunto com a `Label.TextProperty` propriedade estática.

`BindableObject` e `BindableProperty` fornecem suporte para:

- Atribuindo valores padrão de propriedades
- Armazenando seus valores atuais
- Fornecendo mecanismos para validação de valores de propriedade
- Manter a consistência entre as propriedades relacionadas em uma única classe
- Respondendo às alterações de propriedade
- Disparando notificações quando uma propriedade está prestes a ser alterada ou foi alterada
- Suporte à associação de dados
- Estilos de suporte
- Suporte a recursos dinâmicos

Sempre que uma propriedade que é apoiada por uma propriedade vinculável é alterada, `BindableObject` o aciona um [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) evento que identifica a propriedade que foi alterada. Esse evento não é acionado quando a propriedade é definida com o mesmo valor.

Algumas propriedades não são apoiadas por propriedades vinculáveis e algumas Xamarin.Forms classes &mdash; como `Span` &mdash; não derivam de `BindableObject` . Somente uma classe derivada de `BindableObject` pode dar suporte a propriedades vinculáveis `BindableObject` , pois define os `SetValue` `GetValue` métodos e.

Como `Span` o não deriva de `BindableObject` , nenhuma de suas propriedades &mdash; , como, `Text` &mdash; é apoiada por uma propriedade vinculável. É por isso que uma `DynamicResource` configuração na `Text` propriedade de `Span` gera uma exceção no exemplo de [**DynamicVsStatic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic) no capítulo anterior. O exemplo [**DynamicVsStaticCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/DynamicVsStaticCode) demonstra como definir recursos dinâmicos no código usando o [ `SetDynamicResource` ] (xref: Xamarin.Forms . Element. SetDynamicResource ( Xamarin.Forms . O método vinculproperty, System. String) definido por `Element` . O primeiro argumento é um objeto do tipo `BindableProperty` .

Da mesma forma, o [ `SetBinding` ] (xref: Xamarin.Forms . Acopláble. SetBinding ( Xamarin.Forms . Vinculproperty, Xamarin.Forms . BindingBase)) método definido por `BindableObject` tem um primeiro argumento do tipo `BindableProperty` .

## <a name="defining-bindable-properties"></a>Definindo propriedades vinculáveis

Você pode definir suas próprias propriedades vinculáveis usando o static [ `BindableProperty.Create` ] (xref: Xamarin.Forms . Vinculproperty. Create (System. String, System. Type, System. Type, System. Object, Xamarin.Forms . BindingMode, Xamarin.Forms . Vinculproperty. ValidateValueDelegate, Xamarin.Forms . Vinculproperty. BindingPropertyChangedDelegate, Xamarin.Forms . Vinculproperty. BindingPropertyChangingDelegate, Xamarin.Forms . Vinculproperty. CoerceValueDelegate, Xamarin.Forms . Vinculproperty. CreateDefaultValueDelegate)) para criar um campo de somente leitura estático do tipo `BindableProperty` .

Isso é demonstrado na [`AltLabel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AltLabel.cs) classe na biblioteca [**Xamarin.Forms book. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) . A classe deriva de `Label` e permite que você especifique um tamanho de fonte em pontos. Ele é demonstrado no exemplo de [**PointSizedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PointSizedText) .

São necessários quatro argumentos do `BindableProperty.Create` método:

- `propertyName`: o nome de texto da propriedade (o mesmo que o nome da propriedade CLR)
- `returnType`: o tipo da propriedade CLR
- `declaringType`: o tipo da classe que declara a propriedade
- `defaultValue`: o valor padrão da propriedade

Como `defaultValue` é do tipo `object` , o compilador deve ser capaz de determinar o tipo do valor padrão. Por exemplo, se o `returnType` for `double` , `defaultValue` deve ser definido como algo como 0,0 em vez de apenas 0, ou a incompatibilidade de tipo disparará uma exceção em tempo de execução.

Também é muito comum que uma propriedade vinculável inclua:

- `propertyChanged`: um método estático chamado quando a propriedade muda de valor. O primeiro argumento é a instância da classe cuja propriedade foi alterada.

Os outros argumentos para `BindableProperty.Create` não são tão comuns:

- `defaultBindingMode`: usado em conexão com a associação de dados (conforme discutido no [**capítulo 16. Vinculação de dados**](chapter16.md))
- `validateValue`: um retorno de chamada para verificar um valor válido
- `propertyChanging`: um retorno de chamada para indicar quando a propriedade está prestes a ser alterada
- `coerceValue`: um retorno de chamada para forçar um valor definido para outro valor
- `defaultValueCreate`: um retorno de chamada para criar um valor padrão que não pode ser compartilhado entre instâncias da classe (por exemplo, uma coleção)

### <a name="the-read-only-bindable-property"></a>A propriedade vinculável somente leitura

Uma propriedade vinculável pode ser somente leitura. A criação de uma propriedade vinculável somente leitura requer a chamada do método estático [ `BindableProperty.CreateReadOnly` ] (xref: Xamarin.Forms . Associável. createreadonly (System. String, System. Type, System. Type, System. Object, Xamarin.Forms . BindingMode, Xamarin.Forms . Vinculproperty. ValidateValueDelegate, Xamarin.Forms . Vinculproperty. BindingPropertyChangedDelegate, Xamarin.Forms . Vinculproperty. BindingPropertyChangingDelegate, Xamarin.Forms . Vinculproperty. CoerceValueDelegate, Xamarin.Forms . Vinculproperty. CreateDefaultValueDelegate) para definir um campo de tipo somente leitura estático privado [`BindablePropertyKey`](xref:Xamarin.Forms.BindablePropertyKey) .

Em seguida, defina o acessador de propriedade CLR `set` como `private` para chamar um [ `SetValue` ] (xref: Xamarin.Forms . Acopláble. SetValue ( Xamarin.Forms . BindablePropertyKey, System. Object)) sobrecarga com o `BindablePropertyKey` objeto. Isso impede que a propriedade seja definida fora da classe.

Isso é demonstrado na [`CountedLabel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CountedLabel.cs) classe usada no exemplo  [**BaskervillesCount**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/BaskervillesCount) .

## <a name="related-links"></a>Links relacionados

- [Capítulo 11 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch11-Apr2016.pdf)
- [Capítulo 11 exemplos](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)
- [Propriedades vinculáveis](~/xamarin-forms/xaml/bindable-properties.md)
