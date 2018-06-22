---
title: EffectBehavior reutilizável
description: Os comportamentos são uma abordagem útil para adicionar um efeito a um controle, removendo o efeito de placa clichê código de arquivos code-behind de manipulação. Este artigo demonstra como usar um comportamento xamarin. Forms para adicionar um efeito a um controle.
ms.prod: xamarin
ms.assetid: A909B24D-960A-4023-AFF6-4B9256C55ADD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: a1612d1e87f0e05c859babd93fd03ac9a5736b47
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30785049"
---
# <a name="reusable-effectbehavior"></a>EffectBehavior reutilizável

_Os comportamentos são uma abordagem útil para adicionar um efeito a um controle, removendo o efeito de placa clichê código de arquivos code-behind de manipulação. Este artigo demonstra como usar um comportamento xamarin. Forms para adicionar um efeito a um controle._

## <a name="overview"></a>Visão geral

O `EffectBehavior` classe é um comportamento personalizado do xamarin. Forms reutilizável que adiciona um [ `Effect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/) instância para um controle quando o comportamento é anexado ao controle e remove o `Effect` quando é o comportamento da instância desanexado do controle.

As seguintes propriedades de comportamento devem ser definidas para usar o comportamento:

- **Grupo** – o valor de [ `ResolutionGroupName` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResolutionGroupNameAttribute/) atributo para a classe do efeito.
- **Nome** – o valor de [ `ExportEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ExportEffectAttribute/) atributo para a classe do efeito.

Para obter mais informações sobre os efeitos, consulte [efeitos](~/xamarin-forms/app-fundamentals/effects/index.md).

## <a name="creating-the-behavior"></a>Criando o comportamento

O `EffectBehavior` classe deriva o [ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/) classe, onde `T` é um [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/). Isso significa que o `EffectBehavior` classe pode ser anexada a qualquer controle xamarin. Forms.

### <a name="implementing-bindable-properties"></a>Implementando propriedades vinculáveis

O `EffectBehavior` classe define dois [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) instâncias que são usadas para adicionar um [ `Effect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/) para um controle quando o comportamento é anexado ao controle. Essas propriedades são mostradas no exemplo de código a seguir:

```csharp
public class EffectBehavior : Behavior<View>
{
  public static readonly BindableProperty GroupProperty =
    BindableProperty.Create ("Group", typeof(string), typeof(EffectBehavior), null);
  public static readonly BindableProperty NameProperty =
    BindableProperty.Create ("Name", typeof(string), typeof(EffectBehavior), null);

  public string Group {
    get { return (string)GetValue (GroupProperty); }
    set { SetValue (GroupProperty, value); }
  }

  public string Name {
    get { return(string)GetValue (NameProperty); }
    set { SetValue (NameProperty, value); }
  }
  ...
}
```

Quando o `EffectBehavior` é consumido, o `Group` propriedade deve ser definida como o valor da [ `ResolutionGroupName` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResolutionGroupNameAttribute/) atributo para o efeito. Além disso, o `Name` propriedade deve ser definida como o valor da [ `ExportEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ExportEffectAttribute/) atributo para a classe do efeito.

### <a name="implementing-the-overrides"></a>Implementando as substituições

O `EffectBehavior` substituições de classe a [ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/) e [ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/) métodos do [ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/) classe, conforme mostrado no código a seguir exemplo:

```csharp
public class EffectBehavior : Behavior<View>
{
  ...
  protected override void OnAttachedTo (BindableObject bindable)
  {
    base.OnAttachedTo (bindable);
    AddEffect (bindable as View);
  }

  protected override void OnDetachingFrom (BindableObject bindable)
  {
    RemoveEffect (bindable as View);
    base.OnDetachingFrom (bindable);
  }
  ...
}
```

O [ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/) método executa a instalação ao chamar o `AddEffect` método, passando o controle anexado como um parâmetro. O [ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/) método realiza a limpeza chamando o `RemoveEffect` método, passando o controle anexado como um parâmetro.

### <a name="implementing-the-behavior-functionality"></a>A implementação da funcionalidade de comportamento

O objetivo do comportamento é adicionar a [ `Effect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/) definido no `Group` e `Name` propriedades para um controle quando o comportamento é anexado ao controle e remover o `Effect` quando o comportamento é desanexado do controle. A funcionalidade principal do comportamento é mostrada no exemplo de código a seguir:

```csharp
public class EffectBehavior : Behavior<View>
{
  ...
  void AddEffect (View view)
  {
    var effect = GetEffect ();
    if (effect != null) {
      view.Effects.Add (GetEffect ());
    }
  }

  void RemoveEffect (View view)
  {
    var effect = GetEffect ();
    if (effect != null) {
      view.Effects.Remove (GetEffect ());
    }
  }

  Effect GetEffect ()
  {
    if (!string.IsNullOrWhiteSpace (Group) && !string.IsNullOrWhiteSpace (Name)) {
      return Effect.Resolve (string.Format ("{0}.{1}", Group, Name));
    }
    return null;
  }
}
```

O `AddEffect` o método é executado em resposta ao `EffectBehavior` que está sendo anexado a um controle e recebe o controle anexado como um parâmetro. O método, em seguida, adiciona o efeito recuperado para o controle [ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/) coleção. O `RemoveEffect` o método é executado em resposta ao `EffectBehavior` sendo desanexado de um controle e recebe o controle anexado como um parâmetro. O método remove, em seguida, o efeito do controle de [ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/) coleção.

O `GetEffect` método usa o [ `Effect.Resolve` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Effect.Resolve/p/System.String/) método para recuperar o [ `Effect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/). O efeito é localizado por meio de uma concatenação do `Group` e `Name` valores de propriedade. Se uma plataforma não fornece o efeito de `Effect.Resolve` método retornará não`null` valor.

## <a name="consuming-the-behavior"></a>O comportamento de consumo

O `EffectBehavior` classe pode ser anexada ao [ `Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/) coleção de um controle, como demonstrado no exemplo de código XAML a seguir:

```xaml
<Label Text="Label Shadow Effect" ...>
  <Label.Behaviors>
    <local:EffectBehavior Group="Xamarin" Name="LabelShadowEffect" />
  </Label.Behaviors>
</Label>
```

O código C# equivalente é mostrado no exemplo de código a seguir:

```csharp
var label = new Label {
  Text = "Label Shadow Effect",
  ...
};
label.Behaviors.Add (new EffectBehavior {
  Group = "Xamarin",
  Name = "LabelShadowEffect"
});
```

O `Group` e `Name` propriedades do comportamento são definidas para os valores da [ `ResolutionGroupName` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResolutionGroupNameAttribute/) e [ `ExportEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ExportEffectAttribute/) atributos para a classe de efeito em cada plataforma específica projeto.

Em tempo de execução, quando o comportamento está anexado ao [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) controle, o `Xamarin.LabelShadowEffect` será adicionado ao controle de [ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/) coleção. Isso resulta em uma sombra que está sendo adicionada para o texto exibido, o `Label` controlar, como mostrado nas capturas de tela seguir:

![](effect-behavior-images/screenshots.png "Aplicativo de exemplo com EffectsBehavior")

A vantagem de usar esse comportamento para adicionar e remover efeitos de controles é que o código de tratamento de efeito de placa clichê pode ser removido da arquivos code-behind.

## <a name="summary"></a>Resumo

Este artigo demonstrou usando um comportamento para adicionar um efeito a um controle. O `EffectBehavior` classe é um comportamento personalizado do xamarin. Forms reutilizável que adiciona um [ `Effect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/) instância para um controle quando o comportamento é anexado ao controle e remove o `Effect` quando é o comportamento da instância desanexado do controle.


## <a name="related-links"></a>Links relacionados

- [Efeitos](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Comportamento de efeito (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/effectbehavior/)
- [Comportamento](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/)
- [Comportamento<T>](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)
