---
title: Propriedades vinculáveis do Xamarin. Forms
description: Este artigo fornece uma introdução a propriedades associáveis e demonstra como criar e consumi-los.
ms.prod: xamarin
ms.assetid: 1EE869D8-6FE1-45CA-A0AD-26EC7D032AD7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/16/2020
ms.openlocfilehash: 56583aa0df676ae55e1b283f1a8e151b69a13d28
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2020
ms.locfileid: "78292107"
---
# <a name="xamarinforms-bindable-properties"></a>Propriedades vinculáveis do Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)

As propriedades vinculáveis estendem a funcionalidade de propriedade CLR ao fazer o backup de uma propriedade com um tipo de [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , em vez de fazer backup de uma propriedade com um campo. A finalidade de propriedades vinculáveis é fornecer um sistema de propriedade que dá suporte à vinculação de dados, estilos, modelos e valores definidos por meio de relações pai-filho. Além disso, propriedades vinculáveis podem fornecer valores padrão, validação de valores de propriedade e retornos de chamada que monitoram as alterações de propriedade.

As propriedades devem ser implementadas como propriedades vinculáveis para dar suporte a um ou mais dos seguintes recursos:

- Agindo como uma propriedade de *destino* válida para associação de dados.
- Definindo a propriedade por meio de um [estilo](~/xamarin-forms/user-interface/styles/index.md).
- Fornecendo um valor de propriedade padrão que é diferente do padrão para o tipo da propriedade.
- Validando o valor da propriedade.
- Monitoramento de alterações de propriedade.

Exemplos de propriedades vinculáveis do Xamarin. Forms incluem [`Label.Text`](xref:Xamarin.Forms.Label.Text), [`Button.BorderRadius`](xref:Xamarin.Forms.Button.BorderRadius)e [`StackLayout.Orientation`](xref:Xamarin.Forms.StackLayout.Orientation). Cada propriedade vinculável tem uma propriedade de `public static readonly` correspondente do tipo [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) que é exposta na mesma classe e que é o identificador da propriedade vinculável. Por exemplo, o identificador de propriedade vinculável correspondente para a propriedade `Label.Text` é [`Label.TextProperty`](xref:Xamarin.Forms.Label.TextProperty).

## <a name="create-a-bindable-property"></a>Criar uma propriedade vinculável

O processo para a criação de uma propriedade associável é da seguinte maneira:

1. Crie uma instância de [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) com uma das sobrecargas de método [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create*) .
1. Defina acessadores de propriedade para a instância de [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) .

Todas as instâncias de [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) devem ser criadas no thread da interface do usuário. Isso significa que somente o código que é executado no thread da interface do usuário pode obter ou definir o valor de uma propriedade associável. No entanto, `BindableProperty` instâncias podem ser acessadas de outros threads pelo marshaling para o thread da interface do usuário com o método [`Device.BeginInvokeOnMainThread`](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) .

### <a name="create-a-property"></a>Criar uma propriedade

Para criar uma instância de `BindableProperty`, a classe que a contém deve derivar da classe [`BindableObject`](xref:Xamarin.Forms.BindableObject) . No entanto, a classe `BindableObject` é alta na hierarquia de classe, portanto, a maioria das classes usadas para funcionalidade de interface do usuário dão suporte a propriedades vinculáveis.

Uma propriedade vinculável pode ser criada declarando-se uma propriedade `public static readonly` do tipo [`BindableProperty`](xref:Xamarin.Forms.BindableProperty). A propriedade vinculável deve ser definida como o valor retornado de uma das sobrecargas do método [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) . A declaração deve estar dentro do corpo de [`BindableObject`](xref:Xamarin.Forms.BindableObject) classe derivada, mas fora de qualquer definição de membro.

No mínimo, um identificador deve ser especificado ao criar um [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), juntamente com os seguintes parâmetros:

- O nome do [`BindableProperty`](xref:Xamarin.Forms.BindableProperty).
- O tipo da propriedade.
- O tipo do objeto proprietário.
- O valor padrão para a propriedade. Isso garante que a propriedade sempre retorna um valor padrão específico quando ele não for definido, e pode ser diferente do valor padrão para o tipo da propriedade. O valor padrão será restaurado quando o método [`ClearValue`](xref:Xamarin.Forms.BindableObject.ClearValue(Xamarin.Forms.BindableProperty)) for chamado na propriedade vinculável.

O código a seguir mostra um exemplo de uma propriedade vinculável, com um identificador e os valores para os quatro parâmetros obrigatórios:

```csharp
public static readonly BindableProperty EventNameProperty =
  BindableProperty.Create ("EventName", typeof(string), typeof(EventToCommandBehavior), null);
```

Isso cria uma instância [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) chamada `EventName`, do tipo `string`. A propriedade pertence à classe `EventToCommandBehavior` e tem um valor padrão de `null`. A Convenção de nomenclatura para propriedades vinculáveis é que o identificador de propriedade vinculável deve corresponder ao nome de propriedade especificado no método `Create`, com "Property" acrescentado a ele. Portanto, no exemplo acima, o identificador de propriedade vinculável é `EventNameProperty`.

Opcionalmente, ao criar uma instância de [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , os seguintes parâmetros podem ser especificados:

- O modo de associação. Isso é usado para especificar a direção na qual as alterações de valor de propriedade serão propagadas. No modo de associação padrão, as alterações serão propagadas da *origem* para o *destino*.
- Um delegado de validação que será chamado quando o valor da propriedade é definido. Para obter mais informações, consulte [validação de retornos de chamada](#validation-callbacks).
- Um delegado de alteração de propriedade que será chamado quando o valor da propriedade foi alterada. Para obter mais informações, consulte [detectar alterações de propriedade](#detect-property-changes).
- Uma propriedade alterando o delegado que será chamado quando o valor da propriedade será alterada. Esse delegado tem a mesma assinatura que o delegado de propriedade alterada.
- Um delegado de valor de retornos que será chamado quando o valor da propriedade foi alterada. Para obter mais informações, consulte [retornos de chamada de valor forçado](#coerce-value-callbacks).
- Uma `Func` que é usada para inicializar um valor de propriedade padrão. Para obter mais informações, consulte [criar um valor padrão com um Func](#create-a-default-value-with-a-func).

### <a name="create-accessors"></a>Criar acessadores

Acessadores de propriedade são necessários para usar a sintaxe de propriedade para acessar uma propriedade associável. O acessador `Get` deve retornar o valor contido na propriedade ligável correspondente. Isso pode ser obtido chamando o método [`GetValue`](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty)) , passando o identificador de propriedade vinculável no qual obter o valor e, em seguida, convertendo o resultado para o tipo necessário. O acessador `Set` deve definir o valor da propriedade vinculável correspondente. Isso pode ser obtido chamando o método [`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) , passando o identificador de propriedade vinculável no qual definir o valor e o valor a ser definido.

O exemplo de código a seguir mostra os acessadores para a propriedade vinculável `EventName`:

```csharp
public string EventName
{
  get { return (string)GetValue (EventNameProperty); }
  set { SetValue (EventNameProperty, value); }
}
```

## <a name="consume-a-bindable-property"></a>Consumir uma propriedade vinculável

Quando uma propriedade vinculável tiver sido criada, ele pode ser consumido de XAML ou código. No XAML, isso é feito declarando um namespace com um prefixo, com a declaração de namespace que indica o nome do namespace CLR e, opcionalmente, um nome de assembly. Para obter mais informações, consulte [XAML namespaces](~/xamarin-forms/xaml/namespaces.md).

O exemplo de código a seguir demonstra um namespace XAML para um tipo personalizado que contém uma propriedade vinculável, que é definida dentro do mesmo assembly como o código do aplicativo que está fazendo referência ao tipo personalizado:

```xaml
<ContentPage ... xmlns:local="clr-namespace:EventToCommandBehavior" ...>
  ...
</ContentPage>
```

A declaração de namespace é usada ao definir a propriedade `EventName` vinculável, conforme demonstrado no exemplo de código XAML a seguir:

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
listView.Behaviors.Add (new EventToCommandBehavior
{
  EventName = "ItemSelected",
  ...
});
```

## <a name="advanced-scenarios"></a>Cenários avançados

Ao criar uma instância de [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , há vários parâmetros opcionais que podem ser definidos para habilitar cenários de propriedade vinculáveis avançados. Essa seção explora esses cenários.

### <a name="detect-property-changes"></a>Detectar alterações de propriedade

Um método de retorno de chamada de propriedade `static` alterado pode ser registrado com uma propriedade vinculável especificando o parâmetro `propertyChanged` para o método [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) . O método de retorno de chamada especificado será invocado quando o valor da propriedade associável é alterada.

O exemplo de código a seguir mostra como o `EventName` propriedade vinculável registra o método `OnEventNameChanged` como um método de retorno de chamada de propriedade alterada:

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

No método de retorno de chamada Property-Changed, o parâmetro [`BindableObject`](xref:Xamarin.Forms.BindableObject) é usado para indicar qual instância da classe proprietária relatou uma alteração e os valores dos dois parâmetros `object` representam os valores antigos e novos da propriedade vinculável.

### <a name="validation-callbacks"></a>Retornos de chamada de validação

Um método de retorno de chamada de validação de `static` pode ser registrado com uma propriedade vinculável especificando o parâmetro `validateValue` para o método [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) . O método de retorno de chamada especificado será chamado quando o valor da propriedade associável é definido.

O exemplo de código a seguir mostra como o `Angle` propriedade vinculável registra o método `IsValidValue` como um método de retorno de chamada de validação:

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

Os retornos de chamada de validação são fornecidos com um valor e devem retornar `true` se o valor for válido para a propriedade, caso contrário, `false`. Uma exceção será gerada se um retorno de chamada de validação retornar `false`, que deve ser tratado pelo desenvolvedor. Um uso típico de um método de retorno de chamada de validação é restringir os valores de números inteiros ou duplos quando a propriedade associável é definida. Por exemplo, o método `IsValidValue` verifica se o valor da propriedade é um `double` dentro do intervalo de 0 a 360.

### <a name="coerce-value-callbacks"></a>Valores de retorno de chamada de valor forçado

Um método de retorno de chamada de valor `static` forçado pode ser registrado com uma propriedade vinculável especificando o parâmetro `coerceValue` para o método [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) . O método de retorno de chamada especificado será invocado quando o valor da propriedade associável é alterada.

> [!IMPORTANT]
> O tipo de `BindableObject` tem um método `CoerceValue` que pode ser chamado para forçar uma reavaliação do valor de seu argumento `BindableProperty`, invocando seu retorno de chamada de valor forçado.

Forçar retornos de chamada são usados para forçar uma reavaliação de uma propriedade associável quando o valor da propriedade muda de valor. Por exemplo, um retorno de chamada de valor de retornos pode ser usado para garantir que o valor de uma propriedade associável não é maior que o valor de outra propriedade associável.

O exemplo de código a seguir mostra como o `Angle` propriedade vinculável registra o método `CoerceAngle` como um método de retorno de chamada de valor forçado:

```csharp
public static readonly BindableProperty AngleProperty = BindableProperty.Create (
  "Angle", typeof(double), typeof(HomePage), 0.0, coerceValue: CoerceAngle);
public static readonly BindableProperty MaximumAngleProperty = BindableProperty.Create (
  "MaximumAngle", typeof(double), typeof(HomePage), 360.0, propertyChanged: ForceCoerceValue);
...

static object CoerceAngle (BindableObject bindable, object value)
{
  var homePage = bindable as HomePage;
  double input = (double)value;

  if (input > homePage.MaximumAngle)
  {
    input = homePage.MaximumAngle;
  }
  return input;
}

static void ForceCoerceValue(BindableObject bindable, object oldValue, object newValue)
{
  bindable.CoerceValue(AngleProperty);
}
```

O método `CoerceAngle` verifica o valor da propriedade `MaximumAngle` e, se o valor da propriedade `Angle` for maior que ele, ele impõe o valor para o valor da propriedade `MaximumAngle`. Além disso, quando a propriedade `MaximumAngle` altera o retorno de chamada de valor forçado é invocada na propriedade `Angle` chamando o método `CoerceValue`.

### <a name="create-a-default-value-with-a-func"></a>Criar um valor padrão com um Func

Um `Func` pode ser usado para inicializar o valor padrão de uma propriedade vinculável, conforme demonstrado no exemplo de código a seguir:

```csharp
public static readonly BindableProperty SizeProperty =
  BindableProperty.Create ("Size", typeof(double), typeof(HomePage), 0.0,
  defaultValueCreator: bindable => Device.GetNamedSize (NamedSize.Large, (Label)bindable));
```

O parâmetro `defaultValueCreator` é definido como um `Func` que invoca o método [`Device.GetNamedSize`](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,System.Type)) para retornar um `double` que representa o tamanho nomeado da fonte usada em uma [`Label`](xref:Xamarin.Forms.Label) na plataforma nativa.

## <a name="related-links"></a>Links relacionados

- [Namespaces XAML](~/xamarin-forms/xaml/namespaces.md)
- [Comportamento do evento para o comando (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)
- [Retorno de chamada de validação (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-validationcallback)
- [Retorno de chamada de valor forçado (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-coercevaluecallback)
- [API vinculproperty](xref:Xamarin.Forms.BindableProperty)
- [API vinculobject](xref:Xamarin.Forms.BindableObject)
