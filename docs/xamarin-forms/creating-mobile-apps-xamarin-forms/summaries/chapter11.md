---
title: Resumo do Capítulo 11. A infraestrutura associável
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do Capítulo 11. A infraestrutura associável'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 34671C48-0ED4-4B76-A33D-D6505390DC5B
author: charlespetzold
ms.author: chape
ms.date: 07/19/2018
ms.openlocfilehash: 9f3c077d7bae3557178236b81073afaf4892a272
ms.sourcegitcommit: 8555a4dd1a579b2206f86c867125ee20fbc3d264
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39156555"
---
# <a name="summary-of-chapter-11-the-bindable-infrastructure"></a>Resumo do Capítulo 11. A infraestrutura associável

Todo programador de c# está familiarizado com C# *propriedades*. Propriedades contêm uma *definir* acessador e/ou um *obter* acessador. Eles são chamados *propriedades CLR* para o Common Language Runtime.

Xamarin. Forms define uma definição de propriedade aprimorada chamada um *propriedade associável* encapsulado pelo [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) classe e compatíveis com o [ `BindableObject` ](xref:Xamarin.Forms.BindableObject)classe. Essas classes são bastante distintas mas relacionados: O `BindableProperty` é usado para definir a propriedade em si; `BindableObject` é como `object` é de uma classe base para classes que definem propriedades associáveis.

## <a name="the-xamarinforms-class-hierarchy"></a>A hierarquia de classes xamarin. Forms

O [ **ClassHierarchy** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/ClassHierarchy) exemplo usa a reflexão para exibir uma hierarquia de classes do xamarin. Forms e demonstrar o papel crucial desempenhado por `BindableObject` nesta hierarquia. `BindableObject` deriva `Object` e é a classe pai [ `Element` ](xref:Xamarin.Forms.Element) dos quais [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) deriva. Isso é a classe pai [ `Page` ](xref:Xamarin.Forms.Page) e [ `View` ](xref:Xamarin.Forms.View), que é a classe pai [ `Layout` ](xref:Xamarin.Forms.Layout):

[![Captura de tela tripla da hierarquia de classes de compartilhamento](images/ch11fg01-small.png "compartilhamento de hierarquia de classe")](images/ch11fg01-large.png#lightbox "compartilhamento da hierarquia de classe")

## <a name="a-peek-into-bindableobject-and-bindableproperty"></a>Uma espiada no BindableObject e BindableProperty

Nas classes que derivam de `BindableObject` muitas propriedades CLR devem ser "apoio" propriedades associáveis. Por exemplo, o [ `Text` ](xref:Xamarin.Forms.Label.Text) propriedade do `Label` classe é uma propriedade CLR, mas o `Label` classe também define um campo estático público somente leitura denominado [ `TextProperty` ](xref:Xamarin.Forms.Label.TextProperty) de tipo `BindableProperty`.

Um aplicativo pode definir ou obter o `Text` propriedade de `Label` normalmente, ou o aplicativo pode definir o `Text` chamando o [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) método definido pelo `BindableObject` com um `Label.TextProperty` argumento. Da mesma forma, um aplicativo pode obter o valor da `Text` propriedade chamando o [ `GetValue` ](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty)) método, novamente com um `Label.TextProperty` argumento. Isso é demonstrado pelo [ **PropertySettings** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PropertySettings) exemplo.

Na verdade, o `Text` propriedade CLR é totalmente implementada usando o `SetValue` e `GetValue` métodos definidos pelo `BindableObject` em conjunto com o `Label.TextProperty` propriedade estática.

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

Sempre que uma propriedade que é apoiada por uma propriedade associável é alterada, `BindableObject` dispara uma [ `PropertyChanged` ](xref:Xamarin.Forms.BindableObject.PropertyChanged) evento identifica a propriedade que foi alterada. Esse evento não é acionado quando a propriedade é definida com o mesmo valor.

Algumas propriedades não são apoiadas por propriedades vinculáveis e algumas classes xamarin. Forms &mdash; , como `Span` &mdash; não derivam de `BindableObject`. Uma classe que deriva de `BindableObject` pode dar suporte a propriedades associáveis porque `BindableObject` define o `SetValue` e `GetValue` métodos.

Porque `Span` não é derivado de `BindableObject`, nenhuma de suas propriedades &mdash; como `Text` &mdash; contam com uma propriedade associável. É por isso que uma `DynamicResource` definindo na `Text` propriedade de `Span` gera uma exceção no [ **DynamicVsStatic** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic) amostra no capítulo anterior. O [ **DynamicVsStaticCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/DynamicVsStaticCode) exemplo demonstra como definir os recursos dinâmicos no código usando o [ `SetDynamicResource` ](xref:Xamarin.Forms.Element.SetDynamicResource(Xamarin.Forms.BindableProperty,System.String)) método definido pelo `Element`. O primeiro argumento é um objeto do tipo `BindableProperty`.

Da mesma forma, o [ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) método definido pelo `BindableObject` tem um primeiro argumento do tipo `BindableProperty`.

## <a name="defining-bindable-properties"></a>Definindo propriedades vinculáveis

Você pode definir suas próprias propriedades vinculáveis usando estático [ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) método para criar um campo estático somente leitura do tipo `BindableProperty`.

Isso é demonstrado na [ `AltLabel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AltLabel.cs) classe os [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca. A classe deriva de `Label` e permite que você especifique um tamanho de fonte em pontos. Ele é demonstrado na [ **PointSizedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PointSizedText) exemplo.

Quatro argumentos do `BindableProperty.Create` método são necessários:

- `propertyName`: o nome de texto da propriedade (o mesmo que o nome da propriedade CLR)
- `returnType`: o tipo da propriedade CLR
- `declaringType`: o tipo da classe declarando a propriedade
- `defaultValue`: o valor da propriedade padrão

Porque `defaultValue` é do tipo `object`, o compilador deve ser capaz de determinar o tipo do valor padrão. Por exemplo, se o `returnType` está `double`, o `defaultValue` deve ser definido para algo como 0,0, em vez de apenas 0, ou a incompatibilidade de tipo irá disparar uma exceção em tempo de execução.

Também é muito comum para uma propriedade associável incluem:

- `propertyChanged`: um método estático chamado quando a propriedade de valor é alterado. O primeiro argumento é a instância da classe cuja propriedade foi alterada.

Os outros argumentos `BindableProperty.Create` não são tão comuns:

- `defaultBindingMode`: usado em conjunto com a vinculação de dados (conforme discutido em [ **capítulo 16. Associação de dados**](chapter16.md))
- `validateValue`: um retorno de chamada para verificar se há um valor válido
- `propertyChanging`: um retorno de chamada para indicar quando a propriedade está prestes a ser alterada
- `coerceValue`: um retorno de chamada para forçar um valor de conjunto para outro valor
- `defaultValueCreate`: um retorno de chamada para criar um valor padrão que não pode ser compartilhado entre instâncias da classe (por exemplo, uma coleção)

### <a name="the-read-only-bindable-property"></a>A propriedade associável somente leitura

Uma propriedade associável pode ser somente leitura. Criação de uma propriedade associável somente leitura requer chamando o método estático [ `BindableProperty.CreateReadOnly` ](xref:Xamarin.Forms.BindableProperty.CreateReadOnly(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) para definir um estático somente leitura campo particular do tipo [ `BindablePropertyKey` ](xref:Xamarin.Forms.BindablePropertyKey).

Em seguida, defina a propriedade CLR `set` accesor como `private` para chamar um [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindablePropertyKey,System.Object)) sobrecarga com o `BindablePropertyKey` objeto. Isso impede que a propriedade sendo definida fora da classe.

Isso é demonstrado na [ `CountedLabel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CountedLabel.cs) classe usada na [ **BaskervillesCount** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/BaskervillesCount) exemplo.

## <a name="related-links"></a>Links relacionados

- [Capítulo 11 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch11-Apr2016.pdf)
- [Exemplos do Capítulo 11](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)
- [Propriedades vinculáveis](~/xamarin-forms/xaml/bindable-properties.md)
