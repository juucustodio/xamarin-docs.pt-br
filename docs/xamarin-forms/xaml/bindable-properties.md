---
title: Propriedades vinculáveis
description: Este artigo fornece uma introdução a propriedades associáveis e demonstra como criar e consumi-los.
ms.prod: xamarin
ms.assetid: 1EE869D8-6FE1-45CA-A0AD-26EC7D032AD7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/02/2016
ms.openlocfilehash: 0251d06ff68185990ed8b7acb46389c59c71a99a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075177"
---
# <a name="bindable-properties"></a>Propriedades vinculáveis

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/behaviors/eventtocommandbehavior/)

_No xamarin. Forms, a funcionalidade de propriedades comuns de runtime (CLR) de linguagem é estendida por propriedades associáveis. Uma propriedade associável é um tipo especial de propriedade, em que o valor da propriedade é controlado pelo sistema de propriedades de xamarin. Forms. Este artigo fornece uma introdução a propriedades associáveis e demonstra como criar e consumi-los._

## <a name="overview"></a>Visão geral

Propriedades vinculáveis estendem a funcionalidade de propriedade CLR, fazendo uma propriedade com um [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) tipo, em vez de uma propriedade com um campo de suporte. A finalidade de propriedades vinculáveis é fornecer um sistema de propriedade que dá suporte à vinculação de dados, estilos, modelos e valores definidos por meio de relações pai-filho. Além disso, propriedades vinculáveis podem fornecer valores padrão, validação de valores de propriedade e retornos de chamada que monitoram as alterações de propriedade.

As propriedades devem ser implementadas como propriedades vinculáveis para dar suporte a um ou mais dos seguintes recursos:

- Atuando como uma opção válida *destino* propriedade para associação de dados.
- Definindo a propriedade por meio de um [estilo](~/xamarin-forms/user-interface/styles/index.md).
- Fornecendo um valor de propriedade padrão que é diferente do padrão para o tipo da propriedade.
- Validando o valor da propriedade.
- Monitoramento de alterações de propriedade.

Exemplos de propriedades vinculáveis do xamarin. Forms [ `Label.Text` ](xref:Xamarin.Forms.Label.Text), [ `Button.BorderRadius` ](xref:Xamarin.Forms.Button.BorderRadius), e [ `StackLayout.Orientation` ](xref:Xamarin.Forms.StackLayout.Orientation). Cada propriedade associável tem um correspondente `public static readonly` propriedade do tipo [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) que é exposto na mesma classe e que é o identificador da propriedade associável. Por exemplo, o identificador a propriedade associável correspondente para o `Label.Text` é de propriedade [ `Label.TextProperty` ](xref:Xamarin.Forms.Label.TextProperty).

<a name="consuming-bindable-property" />

## <a name="creating-and-consuming-a-bindable-property"></a>Criar e consumir uma propriedade associável

O processo para a criação de uma propriedade associável é da seguinte maneira:

1. Criar uma [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) instância com um dos [ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create*) sobrecargas de método.
1. Definir acessadores de propriedade para o [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) instância.

Observe que todos os [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) instâncias devem ser criadas no thread da interface do usuário. Isso significa que somente o código que é executado no thread da interface do usuário pode obter ou definir o valor de uma propriedade associável. No entanto, `BindableProperty` instâncias podem ser acessadas de outros threads por marshaling para o thread de interface do usuário com o [ `Device.BeginInvokeOnMainThread` ](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) método.

### <a name="creating-a-property"></a>Criação de uma propriedade

Para criar uma `BindableProperty` instância, a classe recipiente deve derivar de [ `BindableObject` ](xref:Xamarin.Forms.BindableObject) classe. No entanto, o `BindableObject` classe é alta na hierarquia de classe, portanto, a maioria das classes usadas para propriedades vinculáveis do suporte ao usuário interface funcionalidade.

Uma propriedade vinculável pode ser criada, declarando uma `public static readonly` propriedade do tipo [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty). A propriedade associável deve ser definida para o valor retornado de um dos [ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) sobrecargas de método. A declaração deve ser dentro do corpo de [ `BindableObject` ](xref:Xamarin.Forms.BindableObject) derivado da classe, mas fora das definições de membro.

No mínimo, um identificador deve ser especificado durante a criação de um [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty), juntamente com os seguintes parâmetros:

- O nome da [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty).
- O tipo da propriedade.
- O tipo do objeto proprietário.
- O valor padrão para a propriedade. Isso garante que a propriedade sempre retorna um valor padrão específico quando ele não for definido, e pode ser diferente do valor padrão para o tipo da propriedade. O valor padrão será restaurado quando o [ `ClearValue` ](xref:Xamarin.Forms.BindableObject.ClearValue(Xamarin.Forms.BindableProperty)) método é chamado na propriedade associável.

O código a seguir mostra um exemplo de uma propriedade vinculável, com um identificador e os valores para os quatro parâmetros obrigatórios:

```csharp
public static readonly BindableProperty EventNameProperty =
  BindableProperty.Create ("EventName", typeof(string), typeof(EventToCommandBehavior), null);
```

Isso cria uma [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) instância denominada `EventName`, do tipo `string`. A propriedade pertencente a `EventToCommandBehavior` de classe e tem um valor padrão de `null`. A convenção de nomenclatura para propriedades vinculáveis é que o identificador de propriedade associável deve corresponder ao nome de propriedade especificado no `Create` método, com "Propriedade" acrescentada a ela. Portanto, no exemplo acima, o identificador de propriedade associável é `EventNameProperty`.

Opcionalmente, durante a criação de um [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) da instância, as seguintes parâmetros podem ser especificados:

- O modo de associação. Isso é usado para especificar a direção na qual as alterações de valor de propriedade serão propagadas. No modo de associação padrão, as alterações sejam propagadas do *fonte* para o *destino*.
- Um delegado de validação que será chamado quando o valor da propriedade é definido. Para obter mais informações, consulte [retornos de chamada de validação](#validation).
- Um delegado de alteração de propriedade que será chamado quando o valor da propriedade foi alterada. Para obter mais informações, consulte [detectando alterações de propriedade](#propertychanges).
- Uma propriedade alterando o delegado que será chamado quando o valor da propriedade será alterada. Esse delegado tem a mesma assinatura que o delegado de propriedade alterada.
- Um delegado de valor de retornos que será chamado quando o valor da propriedade foi alterada. Para obter mais informações, consulte [forçar retornos de chamada de valor](#coerce).
- Um `Func` que é usado para inicializar um valor de propriedade padrão. Para obter mais informações, consulte [criação de um valor padrão com um Func](#defaultfunc).

### <a name="creating-accessors"></a>Criação de acessadores

Acessadores de propriedade são necessários para usar a sintaxe de propriedade para acessar uma propriedade associável. O `Get` acessador deve retornar o valor contido na propriedade associável correspondente. Isso pode ser obtido chamando o [ `GetValue` ](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty)) método, passando o identificador de propriedade associável no qual obter o valor e, em seguida, convertendo os resultados para o tipo solicitado. O `Set` acessador deve definir o valor da propriedade associável correspondente. Isso pode ser obtido chamando o [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) método, passando o identificador de propriedade associável no qual definir o valor e o valor a ser definido.

O exemplo de código a seguir mostra os acessadores para o `EventName` propriedade associável:

```csharp
public string EventName {
  get { return (string)GetValue (EventNameProperty); }
  set { SetValue (EventNameProperty, value); }
}
```

### <a name="consuming-a-bindable-property"></a>Consumindo uma propriedade associável

Quando uma propriedade vinculável tiver sido criada, ele pode ser consumido de XAML ou código. No XAML, isso é feito declarando um namespace com um prefixo, com a declaração de namespace que indica o nome do namespace CLR e, opcionalmente, um nome de assembly. Para obter mais informações, consulte [Namespaces de XAML](~/xamarin-forms/xaml/namespaces.md).

O exemplo de código a seguir demonstra um namespace XAML para um tipo personalizado que contém uma propriedade vinculável, que é definida dentro do mesmo assembly como o código do aplicativo que está fazendo referência ao tipo personalizado:

```xaml
<ContentPage ... xmlns:local="clr-namespace:EventToCommandBehavior" ...>
  ...
</ContentPage>
```

A declaração de namespace é usada ao definir o `EventName` propriedade associável, conforme demonstrado no exemplo de código XAML a seguir:

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

Ao criar uma [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) da instância, há um número de parâmetros opcionais que podem ser definidas para habilitar cenários avançados de propriedade associável. Essa seção explora esses cenários.

<a name="propertychanges" />

### <a name="detecting-property-changes"></a>Detectando alterações de propriedade

Um `static` método de retorno de chamada de propriedade alterada pode ser registrado com uma propriedade vinculável, especificando as `propertyChanged` parâmetro para o [ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) método. O método de retorno de chamada especificado será invocado quando o valor da propriedade associável é alterada.

O seguinte exemplo de código mostra como o `EventName` registra a propriedade associável a `OnEventNameChanged` método como um método de retorno de chamada de propriedade alterada:

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

No método de retorno de chamada de propriedade alterada, o [ `BindableObject` ](xref:Xamarin.Forms.BindableObject) parâmetro é usado para indicar qual instância da classe proprietária reportou uma alteração e os valores dos dois `object` parâmetros representam os valores novos e antigos de a propriedade associável.

<a name="validation" />

### <a name="validation-callbacks"></a>Retornos de chamadas de validação

Um `static` método de retorno de chamada de validação pode ser registrado com uma propriedade vinculável, especificando as `validateValue` parâmetro para o [ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) método. O método de retorno de chamada especificado será chamado quando o valor da propriedade associável é definido.

O seguinte exemplo de código mostra como o `Angle` registra a propriedade associável a `IsValidValue` método como um método de retorno de chamada de validação:

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

Retornos de chamada de validação são fornecidos com um valor e deve retornar `true` se o valor é válido para a propriedade, caso contrário, `false`. Uma exceção será gerada se um retorno de chamada de validação retornar `false`, que deve ser tratado pelo desenvolvedor. Um uso típico de um método de retorno de chamada de validação é restringir os valores de números inteiros ou duplos quando a propriedade associável é definida. Por exemplo, o `IsValidValue` método verifica que o valor da propriedade é um `double` dentro do intervalo de 0 a 360.

<a name="coerce" />

### <a name="coerce-value-callbacks"></a>Forçar retornos de chamada de valor

Um `static` forçar o valor do método de retorno de chamada pode ser registrado com uma propriedade vinculável, especificando as `coerceValue` parâmetro para o [ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) método. O método de retorno de chamada especificado será invocado quando o valor da propriedade associável é alterada.

Forçar retornos de chamada são usados para forçar uma reavaliação de uma propriedade associável quando o valor da propriedade muda de valor. Por exemplo, um retorno de chamada de valor de retornos pode ser usado para garantir que o valor de uma propriedade associável não é maior que o valor de outra propriedade associável.

O seguinte exemplo de código mostra como o `Angle` registra a propriedade associável a `CoerceAngle` método como um método de retorno de chamada de valor retornos:

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

O `CoerceAngle` método verifica o valor da `MaximumAngle` propriedade e se o `Angle` o valor da propriedade é maior do que ele, ele força o valor para o `MaximumAngle` valor da propriedade.

<a name="defaultfunc" />

### <a name="creating-a-default-value-with-a-func"></a>Criação de um valor padrão com um Func

Um `Func` pode ser usado para inicializar o valor padrão de uma propriedade vinculável, conforme demonstrado no exemplo de código a seguir:

```csharp
public static readonly BindableProperty SizeProperty =
  BindableProperty.Create ("Size", typeof(double), typeof(HomePage), 0.0,
  defaultValueCreator: bindable => Device.GetNamedSize (NamedSize.Large, (Label)bindable));
```

O `defaultValueCreator` parâmetro é definido como um `Func` que invoca a [ `Device.GetNamedSize` ](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,System.Type)) método para retornar uma `double` que representa o tamanho nomeado para a fonte que é usada em um [ `Label` ](xref:Xamarin.Forms.Label) na plataforma nativa.

## <a name="summary"></a>Resumo

Este artigo fornece uma introdução para propriedades vinculáveis e demonstrou como criar e consumi-los. Uma propriedade associável é um tipo especial de propriedade, em que o valor da propriedade é controlado pelo sistema de propriedades de xamarin. Forms.


## <a name="related-links"></a>Links relacionados

- [Namespaces XAML](~/xamarin-forms/xaml/namespaces.md)
- [Eventos para comportamento de comando (amostra)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/eventtocommandbehavior/)
- [Retorno de chamada de validação (amostra)](https://developer.xamarin.com/samples/xamarin-forms/xaml/validationcallback/)
- [Impor o valor de retorno de chamada (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/xaml/coercevaluecallback/)
- [BindableProperty](xref:Xamarin.Forms.BindableProperty)
- [BindableObject](xref:Xamarin.Forms.BindableObject)
