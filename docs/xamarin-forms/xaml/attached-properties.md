---
title: Propriedades anexadas
description: Este artigo fornece uma introdução às propriedades anexadas e demonstra como criar e consumi-los.
ms.prod: xamarin
ms.assetid: 6E9DCDC3-A0E4-46A6-BAA9-4FEB6DF8A5A8
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 06/02/2016
ms.openlocfilehash: e0ecff37eaf615321c7fcdce35e334db89ae631a
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245892"
---
# <a name="attached-properties"></a>Propriedades anexadas

_Uma propriedade anexada é um tipo especial de propriedade associável, definido em uma classe, mas anexado a outros objetos e reconhecível em XAML, como um atributo que contém uma classe e um nome de propriedade separado por um ponto. Este artigo fornece uma introdução às propriedades anexadas e demonstra como criar e consumi-los._

## <a name="overview"></a>Visão geral

Anexado propriedades ativar um objeto para atribuir um valor para uma propriedade que não define sua própria classe. Por exemplo, podem usar elementos de filho anexado propriedades para informar o seu elemento pai de como eles devem ser apresentados na interface do usuário. O [ `Grid` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/) controle permite que a linha e coluna de um filho seja especificado definindo a `Grid.Row` e `Grid.Column` propriedades anexadas. `Grid.Row` e `Grid.Column` são propriedades anexadas porque eles são definidos em elementos que são filhos de um `Grid`, em vez de no `Grid` em si.

Propriedades vinculáveis devem ser implementadas como propriedades anexadas nos seguintes cenários:

- Quando há necessidade de ter um mecanismo de configuração de propriedade disponível para as classes que a definição de classe.
- Quando a classe representa um serviço que precisa ser facilmente integrado com as outras classes.

Para obter mais informações sobre as propriedades vinculáveis, consulte [propriedades vinculáveis](~/xamarin-forms/xaml/bindable-properties.md).

## <a name="creating-and-consuming-an-attached-property"></a>Criação e uso de uma propriedade anexada

O processo de criação de uma propriedade anexada é o seguinte:

1. Criar um [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) instância com uma da [ `CreateAttached` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.CreateAttached/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/) sobrecargas do método.
1. Fornecer `static` `Get` *PropertyName* e `Set` *PropertyName* métodos como acessadores para a propriedade anexada.

### <a name="creating-a-property"></a>Criar uma propriedade

Ao criar uma propriedade anexada para uso em outros tipos, a classe onde a propriedade é criada tem derivar [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/). No entanto, o *destino* acessadores de propriedade deve ser do ou derivar do [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/).

Uma propriedade anexada pode ser criada, declarando um `public static readonly` propriedade do tipo [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/). A propriedade associável deve ser definida para o valor retornado de uma da [ `BindableProperty.CreateAttached` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.CreateAttached/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/) sobrecargas do método. A declaração deve estar dentro do corpo da classe de propriedade, mas fora de qualquer definição de membros.

O código a seguir mostra um exemplo de uma propriedade anexada:

```csharp
public static readonly BindableProperty HasShadowProperty =
  BindableProperty.CreateAttached ("HasShadow", typeof(bool), typeof(ShadowEffect), false);
```

Isso cria uma propriedade anexada denominada `HasShadow`, do tipo `bool`. A propriedade pertence a `ShadowEffect` classe e tem um valor padrão de `false`. A convenção de nomenclatura para propriedades anexadas é que o identificador de propriedade anexada deve corresponder ao nome de propriedade especificado no `CreateAttached` método com "Property" anexada a ele. Portanto, no exemplo acima, o identificador de propriedade anexada é `HasShadowProperty`.

Para obter mais informações sobre como criar propriedades vinculáveis, incluindo os parâmetros que podem ser especificados durante a criação, consulte [criação e uso de uma propriedade ligável](~/xamarin-forms/xaml/bindable-properties.md#consuming-bindable-property).

### <a name="creating-accessors"></a>Criação de acessadores

Estático `Get` *PropertyName* e `Set` *PropertyName* métodos são necessários como acessadores para a propriedade anexada, caso contrário, o sistema de propriedade será possível usar o propriedade anexada. O `Get` *PropertyName* acessador deve estar de acordo com a seguinte assinatura:

```csharp
public static valueType GetPropertyName(BindableObject target)
```

O `Get` *PropertyName* acessador deve retornar o valor contido no correspondente `BindableProperty` campo para a propriedade anexada. Isso pode ser conseguido chamando o [ `GetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.GetValue/p/Xamarin.Forms.BindableProperty/) método, passando o identificador de propriedade ligável no qual obter o valor e, em seguida, converter o valor resultante para o tipo exigido.

O `Set` *PropertyName* acessador deve estar de acordo com a seguinte assinatura:

```csharp
public static void SetPropertyName(BindableObject target, valueType value)
```

O `Set` *PropertyName* acessador deve definir o valor correspondente `BindableProperty` campo para a propriedade anexada. Isso pode ser conseguido chamando o [ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/) método, passando o identificador de propriedade ligável no qual definir o valor e o valor a ser definido.

Para ambos os acessadores de *destino* objeto deve ser do ou derivar do [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/).

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

Quando uma propriedade anexada tiver sido criada, ele pode ser consumido de código ou XAML. Em XAML, isso é obtido declarando um namespace com um prefixo, com a declaração de namespace que indica o nome do namespace Common Language Runtime (CLR) e, opcionalmente, um nome de assembly. Para obter mais informações, consulte [Namespaces XAML](~/xamarin-forms/xaml/namespaces.md).

O exemplo de código a seguir demonstra um namespace XAML para um tipo personalizado que contém uma propriedade anexada, que é definida dentro do mesmo assembly como o código do aplicativo que faz referência o tipo personalizado:

```xaml
<ContentPage ... xmlns:local="clr-namespace:EffectsDemo" ...>
  ...
</ContentPage>
```

A declaração de namespace é usada ao definir a propriedade anexada em um controle específico, como demonstrado no exemplo de código XAML a seguir:

```xaml
<Label Text="Label Shadow Effect" local:ShadowEffect.HasShadow="true" />
```

O código C# equivalente é mostrado no exemplo de código a seguir:

```csharp
var label = new Label { Text = "Label Shadow Effect" };
ShadowEffect.SetHasShadow (label, true);
```

### <a name="consuming-an-attached-property-with-a-style"></a>Uma propriedade anexada com um estilo de consumo

Propriedades anexadas também podem ser adicionadas a um controle por um estilo. Mostra o exemplo seguinte do código XAML um *explícita* estilo que usa o `HasShadow` anexado a propriedade, o que pode ser aplicada a [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) controles:

```xaml
<Style x:Key="ShadowEffectStyle" TargetType="Label">
  <Style.Setters>
    <Setter Property="local:ShadowEffect.HasShadow" Value="true" />
  </Style.Setters>
</Style>
```

O [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) pode ser aplicado a um [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) definindo seu [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propriedade para a `Style` instância usando o `StaticResource`extensão de marcação, como demonstrado no exemplo de código a seguir:

```xaml
<Label Text="Label Shadow Effect" Style="{StaticResource ShadowEffectStyle}" />
```

Para obter mais informações sobre estilos, consulte [estilos](~/xamarin-forms/user-interface/styles/index.md).

## <a name="advanced-scenarios"></a>Cenários avançados

Ao criar uma propriedade anexada, há um número de parâmetros opcionais que podem ser definidas para habilitar cenários avançados de propriedade anexada. Isso inclui a detecção de alterações de propriedade, validando valores de propriedade e forçar o uso de valores de propriedade. Para obter mais informações, consulte [cenários avançados](~/xamarin-forms/xaml/bindable-properties.md#advanced).

## <a name="summary"></a>Resumo

Este artigo apresentou uma introdução para propriedades anexadas e demonstrou como criar e consumi-los. Uma propriedade anexada é um tipo especial de propriedade associável, definida em uma classe, mas reconhecível em XAML e anexado a outros objetos como atributos que contêm uma classe e um nome de propriedade separado por um ponto.


## <a name="related-links"></a>Links relacionados

- [Propriedades vinculáveis](~/xamarin-forms/xaml/bindable-properties.md)
- [Namespaces XAML](~/xamarin-forms/xaml/namespaces.md)
- [Efeito de sombra (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/effects/shadoweffect/)
- [BindableProperty](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)
- [BindableObject](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)
