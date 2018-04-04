---
title: Propriedades vinculáveis
description: No xamarin. Forms, a funcionalidade das common language runtime (CLR) é estendida por propriedades vinculáveis. Uma propriedade ligável é um tipo especial de propriedade, em que o valor da propriedade é controlado pelo sistema de propriedade xamarin. Forms. Este artigo fornece uma introdução às propriedades vinculáveis e demonstra como criar e consumi-los.
ms.prod: xamarin
ms.assetid: 1EE869D8-6FE1-45CA-A0AD-26EC7D032AD7
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 06/02/2016
ms.openlocfilehash: 7e1d3c82036ef703014ae548a6719937e89d22f4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="bindable-properties"></a>Propriedades vinculáveis

_No xamarin. Forms, a funcionalidade das common language runtime (CLR) é estendida por propriedades vinculáveis. Uma propriedade ligável é um tipo especial de propriedade, em que o valor da propriedade é controlado pelo sistema de propriedade xamarin. Forms. Este artigo fornece uma introdução às propriedades vinculáveis e demonstra como criar e consumi-los._

## <a name="overview"></a>Visão geral

Propriedades vinculáveis estendem a funcionalidade de propriedade CLR fazendo uma propriedade com um [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) tipo, em vez de fazer uma propriedade com um campo. O objetivo das propriedades vinculáveis é fornecer um sistema de propriedade que dá suporte à associação de dados, estilos, modelos e os valores definidos por meio de relações pai-filho. Além disso, as propriedades vinculáveis podem fornecer valores padrão, validação de valores de propriedade e retornos de chamada que monitoram as alterações de propriedade.

Propriedades devem ser implementadas como propriedades vinculáveis para dar suporte a um ou mais dos seguintes recursos:

- Atua como uma opção válida *destino* propriedade de associação de dados.
- Definindo a propriedade por meio de um [estilo](~/xamarin-forms/user-interface/styles/index.md).
- Fornecendo um valor de propriedade padrão que é diferente do padrão para o tipo da propriedade.
- Validando o valor da propriedade.
- Monitoramento de alterações de propriedade.

Exemplos de propriedades vinculáveis xamarin. Forms [ `Label.Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/), [ `Button.BorderRadius` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.BorderRadius/), e [ `StackLayout.Orientation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.StackLayout.Orientation/). Cada propriedade associável tem um correspondente `public static readonly` propriedade do tipo [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) que é exposto na mesma classe e o que é o identificador da propriedade associável. Por exemplo, o identificador de propriedade associável de correspondente para o `Label.Text` é de propriedade [ `Label.TextProperty` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Label.TextProperty/).

<a name="consuming-bindable-property" />

## <a name="creating-and-consuming-a-bindable-property"></a>Criação e uso de uma propriedade ligável

O processo de criação de uma propriedade ligável é o seguinte:

1. Criar um [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) instância com uma da [ `BindableProperty.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.Create/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/) sobrecargas do método.
1. Definir acessadores de propriedade para o [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) instância.

Observe que todos os [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) instâncias devem ser criadas no thread da interface do usuário. Isso significa que somente o código que é executado no thread da interface do usuário pode obter ou definir o valor de uma propriedade associável. No entanto, `BindableProperty` instâncias podem ser acessadas de outros threads por marshaling para o thread de interface do usuário com o [ `Device.BeginInvokeOnMainThread` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.BeginInvokeOnMainThread/p/System.Action/) método.

### <a name="creating-a-property"></a>Criar uma propriedade

Para criar um `BindableProperty` instância, a classe continente deve derivar do [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/) classe. No entanto, a `BindableObject` classe é alto na hierarquia de classe, para a maioria das classes usadas para propriedades de suporte de funcionalidade interface de usuário associável.

Uma propriedade ligável pode ser criada, declarando um `public static readonly` propriedade do tipo [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/). A propriedade associável deve ser definida para o valor retornado de uma da [ `BindableProperty.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.Create/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/) sobrecargas do método. A declaração deve estar dentro do corpo de [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/) derivado da classe, mas fora de qualquer definição de membros.

No mínimo, um identificador deve ser especificado ao criar um [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/), juntamente com os seguintes parâmetros:

- O nome do [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/).
- O tipo da propriedade.
- O tipo do objeto proprietário.
- O valor padrão para a propriedade. Isso garante que a propriedade sempre retorna um valor padrão de determinado quando ele é removido, e pode ser diferente do valor padrão para o tipo da propriedade. O valor padrão será restaurado quando o [ `ClearValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.ClearValue/p/Xamarin.Forms.BindableProperty/) método é chamado na propriedade associável.

O código a seguir mostra um exemplo de uma propriedade ligável, com um identificador e valores para os quatro parâmetros obrigatórios:

```csharp
public static readonly BindableProperty EventNameProperty =
  BindableProperty.Create ("EventName", typeof(string), typeof(EventToCommandBehavior), null);
```

Isso cria uma [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) instância nomeada `EventName`, do tipo `string`. A propriedade pertence a `EventToCommandBehavior` classe e tem um valor padrão de `null`. A convenção de nomenclatura para as propriedades vinculáveis é que o identificador de propriedade associável deve corresponder ao nome de propriedade especificado no `Create` método com "Property" anexada a ele. Portanto, no exemplo acima, o identificador de propriedade associável é `EventNameProperty`.

Opcionalmente, ao criar um [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) instância, os seguintes parâmetros podem ser especificados:

- O modo de associação. Isso é usado para especificar a direção na qual as alterações de valor de propriedade serão propagadas. No modo de associação padrão, as alterações serão propagadas do *fonte* para o *destino*.
- Um delegado de validação que será chamado quando o valor da propriedade é definido. Para obter mais informações, consulte [retornos de chamada de validação](#validation).
- Um delegado de alteração de propriedade será invocado quando o valor da propriedade foi alterada. Para obter mais informações, consulte [detectar alterações de propriedade](#propertychanges).
- Uma propriedade alterando o representante será invocado quando o valor da propriedade será alterado. Este representante possui a mesma assinatura do representante de propriedade alterada.
- Um delegado de valor forçados que será chamado quando o valor da propriedade foi alterada. Para obter mais informações, consulte [impor o valor de retornos de chamada](#coerce).
- Um `Func` que é usado para inicializar um valor de propriedade padrão. Para obter mais informações, consulte [criação de um valor padrão com um Func](#defaultfunc).

### <a name="creating-accessors"></a>Criação de acessadores

Acessadores de propriedade são necessários para usar a sintaxe de propriedade para acessar uma propriedade ligável. O `Get` acessador deve retornar o valor contido na propriedade associável correspondente. Isso pode ser conseguido chamando o [ `GetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.GetValue/p/Xamarin.Forms.BindableProperty/) método, passando o identificador de propriedade ligável no qual obter o valor e, em seguida, converter o resultado para o tipo exigido. O `Set` acessador deve definir o valor da propriedade associável correspondente. Isso pode ser conseguido chamando o [ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/) método, passando o identificador de propriedade ligável no qual definir o valor e o valor a ser definido.

O exemplo de código a seguir mostra os acessadores para o `EventName` propriedade associável:

```csharp
public string EventName {
  get { return (string)GetValue (EventNameProperty); }
  set { SetValue (EventNameProperty, value); }
}
```

### <a name="consuming-a-bindable-property"></a>Uma propriedade ligável de consumo

Quando uma propriedade ligável tiver sido criada, ele pode ser consumido de código ou XAML. Em XAML, isso é obtido declarando um namespace com um prefixo, com a declaração de namespace que indica o nome do namespace CLR e, opcionalmente, um nome de assembly. Para obter mais informações, consulte [Namespaces XAML](~/xamarin-forms/xaml/namespaces.md).

O exemplo de código a seguir demonstra um namespace XAML para um tipo personalizado que contém uma propriedade ligável, que é definida dentro do mesmo assembly como o código do aplicativo que faz referência o tipo personalizado:

```xaml
<ContentPage ... xmlns:local="clr-namespace:EventToCommandBehavior" ...>
  ...
</ContentPage>
```

A declaração de namespace é usada durante a configuração do `EventName` propriedade associável, como demonstrado no exemplo de código XAML a seguir:

```xaml
<ListView ...>
  <ListView.Behaviors>
    <local:EventToCommandBehavior EventName="ItemSelected" ... />
  </ListView.Behaviors>
</ListView>
```

O código C# equivalente é mostrado no exemplo de código a seguir:

```csharp
var listView = new ListView ();
listView.Behaviors.Add (new EventToCommandBehavior {
  EventName = "ItemSelected",
  ...
});
```

<a name="advanced" />

## <a name="advanced-scenarios"></a>Cenários avançados

Ao criar um [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) da instância, há um número de parâmetros opcionais que podem ser definidas para habilitar cenários de propriedade associável avançada. Esta seção explora esses cenários.

<a name="propertychanges" />

### <a name="detecting-property-changes"></a>Detectando alterações de propriedade

Um `static` método de retorno de chamada de propriedade alterada pode ser registrado com uma propriedade ligável especificando o `propertyChanged` parâmetro para o [ `BindableProperty.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.Create/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/) método. O método de retorno de chamada especificada será invocado quando o valor da propriedade associável é alterado.

O seguinte exemplo de código mostra como o `EventName` propriedade associável registra o `OnEventNameChanged` método como um método de retorno de chamada de propriedade alterada:

```csharp
public static readonly BindableProperty EventNameProperty =
  BindableProperty.Create (
    "EventName", typeof(string), typeof(EventToCommandBehavior), null, propertyChanged: OnEventNameChanged);
...

static void OnEventNameChanged (BindableObject bindable, object oldValue, object newValue)
{
  // Property changed implementation goes here
}
```

No método de retorno de chamada de propriedade alterada, o [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/) parâmetro é usado para indicar qual instância da classe de propriedade reportou uma alteração e os valores dos dois `object` parâmetros representam os valores novos e antigos de a propriedade associável.

<a name="validation" />

### <a name="validation-callbacks"></a>Retornos de chamadas de validação

Um `static` método de retorno de chamada de validação pode ser registrado com uma propriedade ligável especificando o `validateValue` parâmetro para o [ `BindableProperty.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.Create/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/) método. O método de retorno de chamada especificada será invocado quando o valor da propriedade associável é definido.

O seguinte exemplo de código mostra como o `Angle` propriedade associável registra o `IsValidValue` método como um método de retorno de chamada de validação:

```csharp
public static readonly BindableProperty AngleProperty =
  BindableProperty.Create ("Angle", typeof(double), typeof(HomePage), 0.0, validateValue: IsValidValue);
...

static bool IsValidValue (BindableObject view, object value)
{
  double result;
  bool isDouble = double.TryParse (value.ToString (), out result);
  return (result >= 0 && result <= 360);
}
```

Retornos de chamada de validação são fornecidos com um valor e deve retornar `true` se o valor é válido para a propriedade, caso contrário, `false`. Uma exceção será gerada se um retorno de chamada de validação retorna `false`, que deve ser tratado pelo desenvolvedor. Um uso típico de um método de retorno de chamada de validação é restringir os valores de números inteiros ou duplos quando a propriedade associável está definida. Por exemplo, o `IsValidValue` método verifica se o valor da propriedade é um `double` dentro do intervalo de 0 a 360.

<a name="coerce" />

### <a name="coerce-value-callbacks"></a>Forçar retornos de chamada de valor

Um `static` forçar o valor do método de retorno de chamada pode ser registrado com uma propriedade ligável especificando o `coerceValue` parâmetro para o [ `BindableProperty.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.Create/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/) método. O método de retorno de chamada especificada será invocado quando o valor da propriedade associável é alterado.

Força o valor retornos de chamada são usados para forçar uma reavaliação de uma propriedade ligável quando o valor da propriedade é alterado. Por exemplo, um retorno de chamada forçados valor pode ser usado para garantir que o valor de uma propriedade ligável não é maior que o valor de outra propriedade associável.

O seguinte exemplo de código mostra como o `Angle` propriedade associável registra o `CoerceAngle` método como um método de retorno de chamada forçados valor:

```csharp
public static readonly BindableProperty AngleProperty = BindableProperty.Create (
  "Angle", typeof(double), typeof(HomePage), 0.0, coerceValue: CoerceAngle);
public static readonly BindableProperty MaximumAngleProperty = BindableProperty.Create (
  "MaximumAngle", typeof(double), typeof(HomePage), 360.0);
...

static object CoerceAngle (BindableObject bindable, object value)
{
  var homePage = bindable as HomePage;
  double input = (double)value;

  if (input > homePage.MaximumAngle) {
    input = homePage.MaximumAngle;
  }

  return input;
}
```

O `CoerceAngle` método verifica o valor da `MaximumAngle` propriedade e se o `Angle` valor da propriedade é maior do que ele, ele converte o valor para o `MaximumAngle` o valor da propriedade.

<a name="defaultfunc" />

### <a name="creating-a-default-value-with-a-func"></a>Criando um valor padrão com uma função

Um `Func` pode ser usado para inicializar o valor padrão de uma propriedade ligável, conforme demonstrado no exemplo de código a seguir:

```csharp
public static readonly BindableProperty SizeProperty =
  BindableProperty.Create ("Size", typeof(double), typeof(HomePage), 0.0,
  defaultValueCreator: bindable => Device.GetNamedSize (NamedSize.Large, (Label)bindable));
```

O `defaultValueCreator` parâmetro for definido como um `Func` que invoca o [ `Device.GetNamedSize` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.GetNamedSize/p/Xamarin.Forms.NamedSize/System.Type/) método para retornar um `double` que representa o tamanho nomeado para a fonte que é usada em uma [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) na plataforma nativo.

## <a name="summary"></a>Resumo

Este artigo apresentou uma introdução às propriedades vinculáveis e demonstrou como criar e consumi-los. Uma propriedade ligável é um tipo especial de propriedade, em que o valor da propriedade é controlado pelo sistema de propriedade xamarin. Forms.


## <a name="related-links"></a>Links relacionados

- [Namespaces XAML](~/xamarin-forms/xaml/namespaces.md)
- [Evento de comportamento do comando (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/eventtocommandbehavior/)
- [Retorno de chamada de validação (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/xaml/validationcallback/)
- [Impor o valor de retorno de chamada (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/xaml/coercevaluecallback/)
- [BindableProperty](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)
- [BindableObject](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)
