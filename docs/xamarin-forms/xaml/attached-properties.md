---
title: Propriedades anexadas
description: Este artigo fornece uma introdução para propriedades anexadas e demonstra como criar e consumi-los.
ms.prod: xamarin
ms.assetid: 6E9DCDC3-A0E4-46A6-BAA9-4FEB6DF8A5A8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/02/2016
ms.openlocfilehash: 130f4e3efdd7b3792f6458ad28fc85654e6e38c8
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60857426"
---
# <a name="attached-properties"></a>Propriedades anexadas

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/effects/shadoweffect/)

_Uma propriedade anexada é um tipo especial de propriedade associável, definido em uma classe, mas anexado a outros objetos e reconhecível no XAML como um atributo que contém uma classe e um nome de propriedade separados por um período. Este artigo fornece uma introdução para propriedades anexadas e demonstra como criar e consumi-los._

## <a name="overview"></a>Visão geral

Anexado a habilitação de propriedades para atribuir um valor para uma propriedade de sua própria classe não define um objeto. Por exemplo, filho, elementos podem usar propriedades para informar o usuário de seu elemento pai como devem ser apresentados na interface do usuário anexadas. O [ `Grid` ](xref:Xamarin.Forms.Grid) controle permite que a linha e coluna de um filho seja especificado definindo a `Grid.Row` e `Grid.Column` propriedades anexadas. `Grid.Row` e `Grid.Column` são propriedades anexadas, porque eles são definidos em elementos que são filhos de um `Grid`, em vez de no `Grid` em si.

Propriedades vinculáveis devem ser implementadas como propriedades anexadas nos seguintes cenários:

- Quando é necessário ter um mecanismo de configuração de propriedade disponível para classes diferentes da definição de classe.
- Quando a classe representa um serviço que precisa ser facilmente integrados com outras classes.

Para obter mais informações sobre propriedades vinculáveis, consulte [propriedades vinculáveis](~/xamarin-forms/xaml/bindable-properties.md).

## <a name="creating-and-consuming-an-attached-property"></a>Criar e consumir uma propriedade anexada

O processo para a criação de uma propriedade anexada é da seguinte maneira:

1. Criar uma [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) instância com um dos [ `CreateAttached` ](xref:Xamarin.Forms.BindableProperty.CreateAttached*) sobrecargas de método.
1. Fornecer `static` `Get` *PropertyName* e `Set` *PropertyName* métodos como acessadores para a propriedade anexada.

### <a name="creating-a-property"></a>Criação de uma propriedade

Ao criar uma propriedade anexada para uso em outros tipos, a classe em que a propriedade é criada não precisa derivar [ `BindableObject` ](xref:Xamarin.Forms.BindableObject). No entanto, o *alvo* propriedade para acessadores deve ser do ou derivar dele [ `BindableObject` ](xref:Xamarin.Forms.BindableObject).

Uma propriedade anexada pode ser criada, declarando uma `public static readonly` propriedade do tipo [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty). A propriedade associável deve ser definida para o valor retornado de um dos [ `BindableProperty.CreateAttached` ](xref:Xamarin.Forms.BindableProperty.CreateAttached(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) sobrecargas de método. A declaração deve ser dentro do corpo da classe proprietária, mas fora das definições de membro.

O código a seguir mostra um exemplo de uma propriedade anexada:

```csharp
public static readonly BindableProperty HasShadowProperty =
  BindableProperty.CreateAttached ("HasShadow", typeof(bool), typeof(ShadowEffect), false);
```

Isso cria uma propriedade anexada nomeada `HasShadow`, do tipo `bool`. A propriedade pertencente a `ShadowEffect` de classe e tem um valor padrão de `false`. A convenção de nomenclatura para propriedades anexadas é que o identificador da propriedade anexada deve corresponder ao nome de propriedade especificado no `CreateAttached` método, com "Propriedade" acrescentada a ela. Portanto, no exemplo acima, o identificador da propriedade anexada é `HasShadowProperty`.

Para obter mais informações sobre como criar propriedades vinculáveis, incluindo os parâmetros que podem ser especificados durante a criação, consulte [criando e consumindo uma propriedade vinculável](~/xamarin-forms/xaml/bindable-properties.md#consuming-bindable-property).

### <a name="creating-accessors"></a>Criação de acessadores

Estática `Get` *PropertyName* e `Set` *PropertyName* métodos são necessários como acessadores para a propriedade anexada, caso contrário, o sistema de propriedades não poderá usar o propriedade anexada. O `Get` *PropertyName* acessador deve estar de acordo com a seguinte assinatura:

```csharp
public static valueType GetPropertyName(BindableObject target)
```

O `Get` *PropertyName* acessador deve retornar o valor contido no correspondente `BindableProperty` campo para a propriedade anexada. Isso pode ser obtido chamando o [ `GetValue` ](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty)) método, passando o identificador de propriedade associável no qual obter o valor e, em seguida, converter o valor resultante para o tipo solicitado.

O `Set` *PropertyName* acessador deve estar de acordo com a seguinte assinatura:

```csharp
public static void SetPropertyName(BindableObject target, valueType value)
```

O `Set` *PropertyName* acessador deve definir o valor correspondente `BindableProperty` campo para a propriedade anexada. Isso pode ser obtido chamando o [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) método, passando o identificador de propriedade associável no qual definir o valor e o valor a ser definido.

Para ambos os acessadores, o *alvo* objeto deve ser de, ou derivar dele [ `BindableObject` ](xref:Xamarin.Forms.BindableObject).

O exemplo de código a seguir mostra os acessadores para o `HasShadow` propriedade anexada:

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

### <a name="consuming-an-attached-property"></a>Consumindo uma propriedade anexada

Quando uma propriedade anexada tiver sido criada, ele pode ser consumido de XAML ou código. No XAML, isso é feito declarando um namespace com um prefixo, com a declaração de namespace que indica o nome do namespace de Common Language Runtime (CLR) e, opcionalmente, um nome de assembly. Para obter mais informações, consulte [Namespaces de XAML](~/xamarin-forms/xaml/namespaces.md).

O exemplo de código a seguir demonstra um namespace XAML para um tipo personalizado que contém uma propriedade anexada, que é definida dentro do mesmo assembly como o código do aplicativo que está fazendo referência ao tipo personalizado:

```xaml
<ContentPage ... xmlns:local="clr-namespace:EffectsDemo" ...>
  ...
</ContentPage>
```

A declaração de namespace é usada ao configurar a propriedade anexada em um controle específico, como demonstrado no exemplo de código XAML a seguir:

```xaml
<Label Text="Label Shadow Effect" local:ShadowEffect.HasShadow="true" />
```

O código C# equivalente é mostrado no exemplo de código a seguir:

```csharp
var label = new Label { Text = "Label Shadow Effect" };
ShadowEffect.SetHasShadow (label, true);
```

### <a name="consuming-an-attached-property-with-a-style"></a>Consumindo uma propriedade anexada com um estilo

Propriedades anexadas também podem ser adicionadas a um controle por um estilo. Mostra o exemplo de código XAML abaixo uma *explícita* estilo que usa o `HasShadow` anexados a propriedade, que pode ser aplicada a [ `Label` ](xref:Xamarin.Forms.Label) controles:

```xaml
<Style x:Key="ShadowEffectStyle" TargetType="Label">
  <Style.Setters>
    <Setter Property="local:ShadowEffect.HasShadow" Value="true" />
  </Style.Setters>
</Style>
```

O [ `Style` ](xref:Xamarin.Forms.Style) pode ser aplicado a um [ `Label` ](xref:Xamarin.Forms.Label) definindo seu [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) propriedade para o `Style` instância usando o `StaticResource`extensão de marcação, conforme demonstrado no exemplo de código a seguir:

```xaml
<Label Text="Label Shadow Effect" Style="{StaticResource ShadowEffectStyle}" />
```

Para obter mais informações sobre estilos, consulte [estilos](~/xamarin-forms/user-interface/styles/index.md).

## <a name="advanced-scenarios"></a>Cenários avançados

Ao criar uma propriedade anexada, há um número de parâmetros opcionais que podem ser definidas para habilitar cenários avançados de propriedade anexada. Isso inclui a detecção de alterações de propriedade, validando valores de propriedade e coerção de valores de propriedade. Para obter mais informações, consulte [cenários avançados de](~/xamarin-forms/xaml/bindable-properties.md#advanced).

## <a name="summary"></a>Resumo

Este artigo fornece uma introdução para propriedades anexadas e demonstrou como criar e consumi-los. Uma propriedade anexada é um tipo especial de propriedade associável, definido em uma classe, mas anexado a outros objetos e reconhecível no XAML como atributos que contêm uma classe e um nome de propriedade separados por um período.


## <a name="related-links"></a>Links relacionados

- [Propriedades vinculáveis](~/xamarin-forms/xaml/bindable-properties.md)
- [Namespaces XAML](~/xamarin-forms/xaml/namespaces.md)
- [Efeito de sombra (amostra)](https://developer.xamarin.com/samples/xamarin-forms/effects/shadoweffect/)
- [BindableProperty](xref:Xamarin.Forms.BindableProperty)
- [BindableObject](xref:Xamarin.Forms.BindableObject)
