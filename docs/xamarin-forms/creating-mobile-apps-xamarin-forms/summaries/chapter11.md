---
title: "Resumo do Capítulo 11. A infraestrutura vinculável"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 34671C48-0ED4-4B76-A33D-D6505390DC5B
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 6e0f1abf04695dfb5348b631a9fbdbd2c81bc431
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="summary-of-chapter-11-the-bindable-infrastructure"></a>Resumo do Capítulo 11. A infraestrutura vinculável

Cada programador de c# esteja familiarizado com c# *propriedades*. Propriedades contêm uma *definir* acessador e/ou um *obter* acessador. Eles são chamados de *propriedades CLR* para o Common Language Runtime.

Xamarin. Forms define uma definição de propriedade avançado chamada um *propriedade associável* encapsulado pelo [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) classe e suporte a [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)classe. Essas classes são relacionadas, mas bem distintos: O `BindableProperty` é usado para definir a propriedade em si; `BindableObject` é como `object` porque é uma classe base para classes que definem as propriedades vinculáveis.

## <a name="the-xamarinforms-class-hierarchy"></a>A hierarquia de classe xamarin. Forms

O [ **ClassHierarchy** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/ClassHierarchy) usa o reflexo para exibir uma hierarquia de classe de xamarin. Forms e demonstrar o papel fundamental representado pela `BindableObject` nesta hierarquia. `BindableObject` deriva `Object` e é a classe pai [ `Element` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Element/) do qual [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/) deriva. Esta é a classe pai para [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) e [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/), que é a classe pai [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/):

[![Captura de tela tripla da hierarquia de classe de compartilhamento](images/ch11fg01-small.png "compartilhamento da hierarquia de classe")](images/ch11fg01-large.png#lightbox "compartilhamento da hierarquia de classe")

## <a name="a-peek-into-bindableobject-and-bindableproperty"></a>Um pico em BindableObject e BindableProperty

Nas classes que derivam de `BindableObject` são consideradas "feita por" propriedades vinculáveis muitas propriedades CLR. Por exemplo, o [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) propriedade do `Label` classe é uma propriedade CLR, mas o `Label` classe também define um campo estático público somente leitura chamado [ `TextProperty` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextProperty/) de tipo `BindableProperty`.

Um aplicativo pode definir ou obter o `Text` propriedade `Label` normalmente, ou o aplicativo pode definir o `Text` chamando o [ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/) método definido pelo `BindableObject` com um `Label.TextProperty` argumento. Da mesma forma, um aplicativo pode obter o valor de `Text` propriedade chamando o [ `GetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.GetValue/p/Xamarin.Forms.BindableProperty/) método, novamente com um `Label.TextProperty` argumento. Isso é demonstrado pelo [ **PropertySettings** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PropertySettings) exemplo.

Na verdade, o `Text` propriedade CLR é totalmente implementada usando o `SetValue` e `GetValue` métodos definidos pelo `BindableObject` em conjunto com o `Label.TextProperty` propriedade estática.

`BindableObject` e `BindableProperty` oferecem suporte para:

- Fornecendo valores padrão de propriedades
- Armazenar os valores atuais
- Fornecer mecanismos para validar valores de propriedade
- Manter a consistência entre as propriedades relacionadas em uma única classe
- Responder a alterações de propriedade
- Disparando notificações quando uma propriedade está prestes a alterar ou foi alterada
- Suporte a associação de dados
- Estilos de suporte
- Suporte a recursos dinâmicos

Sempre que uma propriedade que é apoiada por uma alteração de propriedade associável, `BindableObject` dispara uma [ `PropertyChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.BindableObject.PropertyChanged/) evento identifica a propriedade que foi alterada. Esse evento não é acionado quando a propriedade é definida com o mesmo valor.

Algumas propriedades não contam com propriedades vinculáveis e algumas classes xamarin. Forms & #x 2014; como `Span` & #x 2014; não derivam `BindableObject`. Uma classe que deriva de `BindableObject` pode oferecer suporte a propriedades vinculáveis porque `BindableObject` define o `SetValue` e `GetValue` métodos.

Porque `Span` não deriva de `BindableObject`, nenhuma de suas propriedades & #x 2014; por exemplo, `Text` & #x 2014; contam com uma propriedade ligável. É por isso que uma `DynamicResource` definição no `Text` propriedade de `Span` gera uma exceção no [ **DynamicVsStatic** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic) exemplo no capítulo anterior. O [ **DynamicVsStaticCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/DynamicVsStaticCode) demonstra como definir um recursos dinâmicos no código usando o [ `SetDynamicResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Element.SetDynamicResource/p/Xamarin.Forms.BindableProperty/System.String/) método definido pelo `Element`. O primeiro argumento é um objeto do tipo `BindableProperty`.

Da mesma forma, o [ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/) método definido pelo `BindableObject` tem um primeiro argumento do tipo `BindableProperty`.

## <a name="defining-bindable-properties"></a>Definindo propriedades vinculáveis

Você pode definir suas próprias propriedades vinculáveis usando estático [ `BindableProperty.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.Create/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/) um pouco mais curto ou método [ `BindableProperty.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.Create/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/) sobrecarga para criar um campo somente leitura estático do tipo `BindableProperty`.

Isso é demonstrado no [ `AltLabel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AltLabel.cs) classe no [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca. A classe derivada de `Label` e permite que você especifique um tamanho de fonte em pontos. Ele é demonstrado no [ **PointSizedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PointSizedText) exemplo.

Quatro argumentos a `BindableProperty.Create` método são necessários:

- `propertyName`: o nome de texto da propriedade (o mesmo que o nome da propriedade CLR)
- `returnType`: o tipo da propriedade CLR
- `declaringType`: o tipo da classe declarar a propriedade
- `defaultValue`: o valor da propriedade padrão

Porque `defaultValue` é do tipo `object`, o compilador deve ser capaz de determinar o tipo do valor padrão. Por exemplo, se o `returnType` é `double`, o `defaultValue` deve ser definida para algo como 0.0, em vez de apenas 0, ou a incompatibilidade de tipo irão disparar uma exceção em tempo de execução.

Também é muito comum para uma propriedade ligável incluem:

- `propertyChanged`: um método estático chamado quando a propriedade altera o valor. O primeiro argumento é a instância da classe cuja propriedade foi alterada.

Os outros argumentos `BindableProperty.Create` não são tão comuns:

- `defaultBindingMode`: usado em conjunto com a associação de dados (como discutido na [ **capítulo 16. Associação de dados**](chapter16.md))
- `validateValue`: um retorno de chamada para verificar se há um valor válido
- `propertyChanging`: um retorno de chamada para indicar quando a propriedade está prestes a ser alterada
- `coerceValue`: um retorno de chamada para forçar um valor de conjunto para outro valor
- `defaultValueCreate`: um retorno de chamada para criar um valor padrão que não pode ser compartilhado entre instâncias da classe (por exemplo, uma coleção)

### <a name="the-read-only-bindable-property"></a>A propriedade associável somente leitura

Uma propriedade ligável pode ser somente leitura. A criação de uma propriedade ligável somente leitura requer chamando o método estático [ `BindableProperty.CreateReadOnly` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.CreateReadOnly/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/) ou um menor [ `BindableProperty.CreateReadOnly` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.CreateReadOnly/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/) para definir um privada campo estático de somente leitura do tipo [ `BindablePropertyKey` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindablePropertyKey/).

Em seguida, defina a propriedade CLR `set` accesor como `private` para chamar um [ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindablePropertyKey/System.Object/) sobrecarga com a `BindablePropertyKey` objeto. Isso impede que a propriedade sendo definida fora da classe.

Isso é demonstrado no [ `CountedLabel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CountedLabel.cs) classe usada no [ **BaskervillesCount** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/BaskervillesCount) exemplo.



## <a name="related-links"></a>Links relacionados

- [Texto completo do Capítulo 11 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch11-Apr2016.pdf)
- [Exemplos de capítulo 11](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)
