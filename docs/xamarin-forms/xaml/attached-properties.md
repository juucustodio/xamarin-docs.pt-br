---
title: Propriedades Anexadas
description: Este artigo fornece uma introdução às propriedades anexadas e demonstra como criá-las e consumi-las.
ms.prod: xamarin
ms.assetid: 6E9DCDC3-A0E4-46A6-BAA9-4FEB6DF8A5A8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/02/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b3db63018bc8d927b9e9041c762b1989cfb17679
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374089"
---
# <a name="attached-properties"></a>Propriedades Anexadas

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/effects-shadoweffect)


As propriedades anexadas permitem que um objeto atribua um valor para uma propriedade que sua própria classe não define. Por exemplo, elementos filho podem usar propriedades anexadas para informar o elemento pai de como eles devem ser apresentados na interface do usuário. O [`Grid`](xref:Xamarin.Forms.Grid) controle permite que a linha e a coluna de um filho sejam especificadas definindo as `Grid.Row` `Grid.Column` Propriedades anexadas e. `Grid.Row` e `Grid.Column` são propriedades anexadas porque são definidas em elementos que são filhos de a `Grid` , e não por `Grid` si só.

As propriedades vinculáveis devem ser implementadas como propriedades anexadas nos seguintes cenários:

- Quando há a necessidade de ter um mecanismo de configuração de propriedade disponível para classes diferentes da classe de definição.
- Quando a classe representa um serviço que precisa ser facilmente integrado a outras classes.

Para obter mais informações sobre as propriedades vinculáveis, consulte [propriedades vinculáveis](~/xamarin-forms/xaml/bindable-properties.md).

## <a name="create-an-attached-property"></a>Criar uma propriedade anexada

O processo para criar uma propriedade anexada é o seguinte:

1. Crie uma [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) instância com uma das [`CreateAttached`](xref:Xamarin.Forms.BindableProperty.CreateAttached*) sobrecargas de método.
1. Forneça `static` `Get` os métodos *PropertyName* e `Set` *PropertyName* como acessadores para a propriedade anexada.

### <a name="create-a-property"></a>Criar uma propriedade

Ao criar uma propriedade anexada para uso em outros tipos, a classe na qual a propriedade é criada não precisa derivar de [`BindableObject`](xref:Xamarin.Forms.BindableObject) . No entanto, a propriedade de *destino* para acessadores deve ser de, ou derivar de, [`BindableObject`](xref:Xamarin.Forms.BindableObject) .

Uma propriedade anexada pode ser criada declarando-se uma `public static readonly` Propriedade do tipo [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) . A propriedade vinculável deve ser definida como o valor retornado de um dos [ `BindableProperty.CreateAttached` ] (xref: Xamarin.Forms . Associável. createanexado (System. String, System. Type, System. Type, System. Object, Xamarin.Forms . BindingMode, Xamarin.Forms . Vinculproperty. ValidateValueDelegate, Xamarin.Forms . Vinculproperty. BindingPropertyChangedDelegate, Xamarin.Forms . Vinculproperty. BindingPropertyChangingDelegate, Xamarin.Forms . Vinculproperty. CoerceValueDelegate, Xamarin.Forms . O método acopláproperty. CreateDefaultValueDelegate)) é sobrecarregado. A declaração deve estar dentro do corpo da classe proprietária, mas fora de qualquer definição de membro.

> [!IMPORTANT]
> A Convenção de nomenclatura para propriedades anexadas é que o identificador de Propriedade anexado deve corresponder ao nome de propriedade especificado no `CreateAttached` método, com "Property" acrescentado a ele.

O código a seguir mostra um exemplo de uma propriedade anexada:

```csharp
public static readonly BindableProperty HasShadowProperty =
  BindableProperty.CreateAttached ("HasShadow", typeof(bool), typeof(ShadowEffect), false);
```

Isso cria uma propriedade anexada chamada `HasShadowProperty` , do tipo `bool` . A propriedade pertence à `ShadowEffect` classe e tem um valor padrão de `false` .

Para obter mais informações sobre como criar propriedades vinculáveis, incluindo parâmetros que podem ser especificados durante a criação, consulte [criar uma propriedade vinculável](~/xamarin-forms/xaml/bindable-properties.md#consume-a-bindable-property).

### <a name="create-accessors"></a>Criar acessadores

`Get`Os métodos *PropertyName* e `Set` *PropertyName* estáticos são necessários como acessadores para a propriedade anexada, caso contrário, o sistema de propriedades não poderá usar a propriedade anexada. O `Get` acessador *PropertyName* deve estar em conformidade com a seguinte assinatura:

```csharp
public static valueType GetPropertyName(BindableObject target)
```

O `Get` acessador *PropertyName* deve retornar o valor contido no campo correspondente `BindableProperty` para a propriedade anexada. Isso pode ser obtido chamando [ `GetValue` ] (xref: Xamarin.Forms . Acopláble. GetValue ( Xamarin.Forms . Vinculproperty)), passando o identificador de propriedade vinculável no qual obter o valor e, em seguida, convertendo o valor resultante para o tipo necessário.

O `Set` acessador *PropertyName* deve estar em conformidade com a seguinte assinatura:

```csharp
public static void SetPropertyName(BindableObject target, valueType value)
```

O `Set` acessador *PropertyName* deve definir o valor do `BindableProperty` campo correspondente para a propriedade anexada. Isso pode ser obtido chamando [ `SetValue` ] (xref: Xamarin.Forms . Acopláble. SetValue ( Xamarin.Forms . Vinculproperty, System. Object), passando o identificador de propriedade vinculável no qual definir o valor e o valor a ser definido.

Para os dois acessadores, o objeto de *destino* deve ser de, ou derivar de, [`BindableObject`](xref:Xamarin.Forms.BindableObject) .

O exemplo de código a seguir mostra os acessadores da `HasShadow` Propriedade anexada:

```csharp
public static bool GetHasShadow (BindableObject view)
{
  return (bool)view.GetValue (HasShadowProperty);
}

public static void SetHasShadow (BindableObject view, bool value)
{
  view.SetValue (HasShadowProperty, value);
}
```

### <a name="consume-an-attached-property"></a>Consumir uma propriedade anexada

Depois que uma propriedade anexada tiver sido criada, ela poderá ser consumida do XAML ou do código. Em XAML, isso é feito declarando um namespace com um prefixo, com a declaração de namespace indicando o nome do namespace CLR (Common Language Runtime) e, opcionalmente, um nome de assembly. Para obter mais informações, consulte [XAML namespaces](~/xamarin-forms/xaml/namespaces.md).

O exemplo de código a seguir demonstra um namespace XAML para um tipo personalizado que contém uma propriedade anexada, que é definida dentro do mesmo assembly que o código do aplicativo que faz referência ao tipo personalizado:

```xaml
<ContentPage ... xmlns:local="clr-namespace:EffectsDemo" ...>
  ...
</ContentPage>
```

Em seguida, a declaração de namespace é usada ao definir a propriedade anexada em um controle específico, conforme demonstrado no exemplo de código XAML a seguir:

```xaml
<Label Text="Label Shadow Effect" local:ShadowEffect.HasShadow="true" />
```

O código C# equivalente é mostrado no exemplo de código a seguir:

```csharp
var label = new Label { Text = "Label Shadow Effect" };
ShadowEffect.SetHasShadow (label, true);
```

### <a name="consume-an-attached-property-with-a-style"></a>Consumir uma propriedade anexada com um estilo

As propriedades anexadas também podem ser adicionadas a um controle por um estilo. O exemplo de código XAML a seguir mostra um estilo *explícito* que usa a `HasShadow` Propriedade anexada, que pode ser aplicada a [`Label`](xref:Xamarin.Forms.Label) controles:

```xaml
<Style x:Key="ShadowEffectStyle" TargetType="Label">
  <Style.Setters>
    <Setter Property="local:ShadowEffect.HasShadow" Value="true" />
  </Style.Setters>
</Style>
```

O [`Style`](xref:Xamarin.Forms.Style) pode ser aplicado a um [`Label`](xref:Xamarin.Forms.Label) definindo sua [`Style`](xref:Xamarin.Forms.NavigableElement.Style) propriedade para a `Style` instância usando a `StaticResource` extensão de marcação, conforme demonstrado no exemplo de código a seguir:

```xaml
<Label Text="Label Shadow Effect" Style="{StaticResource ShadowEffectStyle}" />
```

Para mais informações sobre estilos, confira [Estilos](~/xamarin-forms/user-interface/styles/index.md).

## <a name="advanced-scenarios"></a>Cenários avançados

Ao criar uma propriedade anexada, há vários parâmetros opcionais que podem ser definidos para habilitar cenários de Propriedade anexado avançado. Isso inclui a detecção de alterações de propriedade, a validação de valores de propriedade e a coerção de valores de propriedade. Para obter mais informações, consulte [cenários avançados](~/xamarin-forms/xaml/bindable-properties.md#advanced-scenarios).

## <a name="related-links"></a>Links relacionados

- [Propriedades vinculáveis](~/xamarin-forms/xaml/bindable-properties.md)
- [Namespaces XAML](~/xamarin-forms/xaml/namespaces.md)
- [Efeito de sombra (amostra)](/samples/xamarin/xamarin-forms-samples/effects-shadoweffect)
- [API vinculproperty](xref:Xamarin.Forms.BindableProperty)
- [API vinculobject](xref:Xamarin.Forms.BindableObject)