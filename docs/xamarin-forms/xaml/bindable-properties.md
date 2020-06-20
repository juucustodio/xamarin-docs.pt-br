---
title: Xamarin.FormsPropriedades vinculáveis
description: Este artigo fornece uma introdução às propriedades vinculáveis e demonstra como criá-las e consumi-las.
ms.prod: xamarin
ms.assetid: 1EE869D8-6FE1-45CA-A0AD-26EC7D032AD7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 33b3763075b64ea8af615465825313a527d20db2
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138171"
---
# <a name="xamarinforms-bindable-properties"></a>Xamarin.FormsPropriedades vinculáveis

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)

As propriedades vinculáveis estendem a funcionalidade da propriedade CLR ao fazer o backup de uma propriedade com um [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) tipo, em vez de fazer backup de uma propriedade com um campo. A finalidade das propriedades vinculáveis é fornecer um sistema de propriedades que ofereça suporte a vinculação de dados, estilos, modelos e valores definidos por meio de relações pai-filho. Além disso, as propriedades vinculáveis podem fornecer valores padrão, validação de valores de propriedade e retornos de chamada que monitoram alterações de propriedade.

As propriedades devem ser implementadas como propriedades vinculáveis para dar suporte a um ou mais dos seguintes recursos:

- Agindo como uma propriedade de *destino* válida para associação de dados.
- Definindo a propriedade por meio de um [estilo](~/xamarin-forms/user-interface/styles/index.md).
- Fornecer um valor de propriedade padrão diferente do padrão para o tipo da propriedade.
- Validando o valor da propriedade.
- Monitorando alterações de propriedade.

Exemplos de Xamarin.Forms propriedades vinculáveis incluem [`Label.Text`](xref:Xamarin.Forms.Label.Text) , [`Button.BorderRadius`](xref:Xamarin.Forms.Button.BorderRadius) e [`StackLayout.Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) . Cada propriedade vinculável tem um `public static readonly` campo correspondente do tipo [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) que é exposto na mesma classe e que é o identificador da propriedade vinculável. Por exemplo, o identificador de propriedade vinculável correspondente para a `Label.Text` propriedade é [`Label.TextProperty`](xref:Xamarin.Forms.Label.TextProperty) .

## <a name="create-a-bindable-property"></a>Criar uma propriedade vinculável

O processo para criar uma propriedade vinculável é o seguinte:

1. Crie uma [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) instância com uma das [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create*) sobrecargas de método.
1. Defina acessadores de propriedade para a [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) instância.

Todas as [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) instâncias devem ser criadas no thread da interface do usuário. Isso significa que apenas o código executado no thread da interface do usuário pode obter ou definir o valor de uma propriedade vinculável. No entanto, `BindableProperty` as instâncias podem ser acessadas de outros threads pelo marshaling para o thread da interface do usuário com o [`Device.BeginInvokeOnMainThread`](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) método.

### <a name="create-a-property"></a>Criar uma propriedade

Para criar uma `BindableProperty` instância, a classe que a contém deve derivar da [`BindableObject`](xref:Xamarin.Forms.BindableObject) classe. No entanto, a `BindableObject` classe é alta na hierarquia de classe, portanto, a maioria das classes usadas para funcionalidade de interface do usuário dão suporte a propriedades vinculáveis.

Uma propriedade vinculável pode ser criada declarando-se uma `public static readonly` Propriedade do tipo [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) . A propriedade vinculável deve ser definida como o valor retornado de um dos [ `BindableProperty.Create` ] (xref: Xamarin.Forms . Vinculproperty. Create (System. String, System. Type, System. Type, System. Object, Xamarin.Forms . BindingMode, Xamarin.Forms . Vinculproperty. ValidateValueDelegate, Xamarin.Forms . Vinculproperty. BindingPropertyChangedDelegate, Xamarin.Forms . Vinculproperty. BindingPropertyChangingDelegate, Xamarin.Forms . Vinculproperty. CoerceValueDelegate, Xamarin.Forms . O método acopláproperty. CreateDefaultValueDelegate)) é sobrecarregado. A declaração deve estar dentro do corpo da [`BindableObject`](xref:Xamarin.Forms.BindableObject) classe derivada, mas fora de qualquer definição de membro.

No mínimo, um identificador deve ser especificado ao criar um [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , juntamente com os seguintes parâmetros:

- O nome do [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) .
- O tipo da propriedade.
- O tipo do objeto proprietário.
- O valor padrão para a propriedade. Isso garante que a propriedade sempre retorna um valor padrão específico quando ela é desdefinida e pode ser diferente do valor padrão para o tipo da propriedade. O valor padrão será restaurado quando [ `ClearValue` ] (xref: Xamarin.Forms . Acopláble. ClearValue ( Xamarin.Forms . Vinculproperty)) é chamado na propriedade vinculável.

O código a seguir mostra um exemplo de uma propriedade vinculável, com um identificador e valores para os quatro parâmetros necessários:

```csharp
public static readonly BindableProperty EventNameProperty =
  BindableProperty.Create ("EventName", typeof(string), typeof(EventToCommandBehavior), null);
```

Isso cria uma [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) instância chamada `EventName` , do tipo `string` . A propriedade pertence à `EventToCommandBehavior` classe e tem um valor padrão de `null` . A Convenção de nomenclatura para propriedades vinculáveis é que o identificador de propriedade vinculável deve corresponder ao nome de propriedade especificado no `Create` método, com "Property" acrescentado a ele. Portanto, no exemplo acima, o identificador de propriedade vinculável é `EventNameProperty` .

Opcionalmente, ao criar uma [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) instância, os seguintes parâmetros podem ser especificados:

- O modo de associação. Isso é usado para especificar a direção na qual as alterações de valor de propriedade serão propagadas. No modo de associação padrão, as alterações serão propagadas da *origem* para o *destino*.
- Um delegado de validação que será invocado quando o valor da propriedade for definido. Para obter mais informações, consulte [validação de retornos de chamada](#validation-callbacks).
- Um delegado de propriedade alterado que será invocado quando o valor da propriedade for alterado. Para obter mais informações, consulte [detectar alterações de propriedade](#detect-property-changes).
- Uma propriedade que altera o delegado que será invocado quando o valor da propriedade for alterado. Esse delegado tem a mesma assinatura que o delegado da propriedade alterada.
- Um delegado de valor forçado que será invocado quando o valor da propriedade for alterado. Para obter mais informações, consulte [retornos de chamada de valor forçado](#coerce-value-callbacks).
- Um `Func` que é usado para inicializar um valor de propriedade padrão. Para obter mais informações, consulte [criar um valor padrão com um Func](#create-a-default-value-with-a-func).

### <a name="create-accessors"></a>Criar acessadores

Acessadores de propriedade são necessários para usar a sintaxe de propriedade para acessar uma propriedade vinculável. O `Get` acessador deve retornar o valor contido na propriedade ligável correspondente. Isso pode ser obtido chamando [ `GetValue` ] (xref: Xamarin.Forms . Acopláble. GetValue ( Xamarin.Forms . Vinculproperty)), passando o identificador de propriedade vinculável no qual obter o valor e, em seguida, convertendo o resultado para o tipo necessário. O `Set` acessador deve definir o valor da propriedade vinculável correspondente. Isso pode ser obtido chamando [ `SetValue` ] (xref: Xamarin.Forms . Acopláble. SetValue ( Xamarin.Forms . Vinculproperty, System. Object), passando o identificador de propriedade vinculável no qual definir o valor e o valor a ser definido.

O exemplo de código a seguir mostra os acessadores para a `EventName` propriedade vinculável:

```csharp
public string EventName
{
  get { return (string)GetValue (EventNameProperty); }
  set { SetValue (EventNameProperty, value); }
}
```

## <a name="consume-a-bindable-property"></a>Consumir uma propriedade vinculável

Depois que uma propriedade vinculável tiver sido criada, ela poderá ser consumida do XAML ou do código. Em XAML, isso é feito declarando um namespace com um prefixo, com a declaração de namespace indicando o nome do namespace CLR e, opcionalmente, um nome de assembly. Para obter mais informações, consulte [XAML namespaces](~/xamarin-forms/xaml/namespaces.md).

O exemplo de código a seguir demonstra um namespace XAML para um tipo personalizado que contém uma propriedade vinculável, que é definida dentro do mesmo assembly que o código do aplicativo que faz referência ao tipo personalizado:

```xaml
<ContentPage ... xmlns:local="clr-namespace:EventToCommandBehavior" ...>
  ...
</ContentPage>
```

A declaração de namespace é usada ao definir a `EventName` propriedade vinculável, conforme demonstrado no exemplo de código XAML a seguir:

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

Ao criar uma [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) instância, há vários parâmetros opcionais que podem ser definidos para habilitar cenários de propriedade vinculáveis avançados. Esta seção explora esses cenários.

### <a name="detect-property-changes"></a>Detectar alterações de propriedade

Um `static` método de retorno de chamada de propriedade alterada pode ser registrado com uma propriedade vinculável especificando o `propertyChanged` parâmetro para [ `BindableProperty.Create` ] (xref: Xamarin.Forms . Vinculproperty. Create (System. String, System. Type, System. Type, System. Object, Xamarin.Forms . BindingMode, Xamarin.Forms . Vinculproperty. ValidateValueDelegate, Xamarin.Forms . Vinculproperty. BindingPropertyChangedDelegate, Xamarin.Forms . Vinculproperty. BindingPropertyChangingDelegate, Xamarin.Forms . Vinculproperty. CoerceValueDelegate, Xamarin.Forms . O método vinculproperty. CreateDefaultValueDelegate)). O método de retorno de chamada especificado será invocado quando o valor da propriedade vinculável for alterado.

O exemplo de código a seguir mostra como a `EventName` propriedade vinculável registra o `OnEventNameChanged` método como um método de retorno de chamada de propriedade alterada:

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

No método de retorno de chamada Property-Changed, o [`BindableObject`](xref:Xamarin.Forms.BindableObject) parâmetro é usado para indicar qual instância da classe proprietária relatou uma alteração e os valores dos dois `object` parâmetros representam os valores antigos e novos da propriedade vinculável.

### <a name="validation-callbacks"></a>Retornos de chamada de validação

Um `static` método de retorno de chamada de validação pode ser registrado com uma propriedade vinculável especificando o `validateValue` parâmetro para [ `BindableProperty.Create` ] (xref: Xamarin.Forms . Vinculproperty. Create (System. String, System. Type, System. Type, System. Object, Xamarin.Forms . BindingMode, Xamarin.Forms . Vinculproperty. ValidateValueDelegate, Xamarin.Forms . Vinculproperty. BindingPropertyChangedDelegate, Xamarin.Forms . Vinculproperty. BindingPropertyChangingDelegate, Xamarin.Forms . Vinculproperty. CoerceValueDelegate, Xamarin.Forms . O método vinculproperty. CreateDefaultValueDelegate)). O método de retorno de chamada especificado será invocado quando o valor da propriedade vinculável for definido.

O exemplo de código a seguir mostra como a `Angle` propriedade vinculável registra o `IsValidValue` método como um método de retorno de chamada de validação:

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

Os retornos de chamada de validação são fornecidos com um valor e devem retornar `true` se o valor for válido para a propriedade, caso contrário `false` . Uma exceção será gerada se um retorno de chamada de validação retornar `false` , que deve ser tratado pelo desenvolvedor. Um uso típico de um método de retorno de chamada de validação é restringir os valores de inteiros ou duplos quando a propriedade vinculável é definida. Por exemplo, o `IsValidValue` método verifica se o valor da propriedade é um `double` dentro do intervalo de 0 a 360.

### <a name="coerce-value-callbacks"></a>Valores de retorno de chamada de valor forçado

Um `static` método de retorno de chamada de valor forçado pode ser registrado com uma propriedade vinculável especificando o `coerceValue` parâmetro para [ `BindableProperty.Create` ] (xref: Xamarin.Forms . Vinculproperty. Create (System. String, System. Type, System. Type, System. Object, Xamarin.Forms . BindingMode, Xamarin.Forms . Vinculproperty. ValidateValueDelegate, Xamarin.Forms . Vinculproperty. BindingPropertyChangedDelegate, Xamarin.Forms . Vinculproperty. BindingPropertyChangingDelegate, Xamarin.Forms . Vinculproperty. CoerceValueDelegate, Xamarin.Forms . O método vinculproperty. CreateDefaultValueDelegate)). O método de retorno de chamada especificado será invocado quando o valor da propriedade vinculável for alterado.

> [!IMPORTANT]
> O `BindableObject` tipo tem um `CoerceValue` método que pode ser chamado para forçar uma reavaliação do valor de seu `BindableProperty` argumento, invocando seu retorno de chamada de valor forçado.

Os retornos de chamada de valor forçado são usados para forçar uma reavaliação de uma propriedade vinculável quando o valor da propriedade é alterado. Por exemplo, um retorno de chamada de valor forçado pode ser usado para garantir que o valor de uma propriedade vinculável não seja maior que o valor de outra propriedade vinculável.

O exemplo de código a seguir mostra como a `Angle` propriedade vinculável registra o `CoerceAngle` método como um método de retorno de chamada de valor forçado:

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

O `CoerceAngle` método verifica o valor da `MaximumAngle` propriedade e, se o `Angle` valor da propriedade for maior que ele, ele forçará o valor para o `MaximumAngle` valor da propriedade. Além disso, quando a `MaximumAngle` propriedade altera o retorno de chamada de valor forçado é invocada na `Angle` propriedade chamando o `CoerceValue` método.

### <a name="create-a-default-value-with-a-func"></a>Criar um valor padrão com um Func

Um `Func` pode ser usado para inicializar o valor padrão de uma propriedade vinculável, conforme demonstrado no exemplo de código a seguir:

```csharp
public static readonly BindableProperty SizeProperty =
  BindableProperty.Create ("Size", typeof(double), typeof(HomePage), 0.0,
  defaultValueCreator: bindable => Device.GetNamedSize (NamedSize.Large, (Label)bindable));
```

O `defaultValueCreator` parâmetro é definido como um `Func` que invoca o [ `Device.GetNamedSize` ] (xref: Xamarin.Forms . Device. GetNamedSize ( Xamarin.Forms . NamedSize, System. Type)) para retornar um `double` que representa o tamanho nomeado da fonte usada em uma [`Label`](xref:Xamarin.Forms.Label) na plataforma nativa.

## <a name="related-links"></a>Links relacionados

- [Namespaces XAML](~/xamarin-forms/xaml/namespaces.md)
- [Comportamento do evento para o comando (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)
- [Retorno de chamada de validação (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-validationcallback)
- [Retorno de chamada de valor forçado (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-coercevaluecallback)
- [API vinculproperty](xref:Xamarin.Forms.BindableProperty)
- [API vinculobject](xref:Xamarin.Forms.BindableObject)
