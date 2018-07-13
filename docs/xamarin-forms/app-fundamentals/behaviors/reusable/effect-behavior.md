---
title: EffectBehavior reutilizável
description: Os comportamentos são uma abordagem útil para adicionar um efeito a um controle, removendo o código de arquivos code-behind de tratamento de efeito de clichê clichê. Este artigo demonstra como usar um comportamento de xamarin. Forms para adicionar um efeito a um controle.
ms.prod: xamarin
ms.assetid: A909B24D-960A-4023-AFF6-4B9256C55ADD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 1ce7eda6f556041cbffc3793b00e8e2cba44d3d0
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995775"
---
# <a name="reusable-effectbehavior"></a>EffectBehavior reutilizável

_Os comportamentos são uma abordagem útil para adicionar um efeito a um controle, removendo o código de arquivos code-behind de tratamento de efeito de clichê clichê. Este artigo demonstra como usar um comportamento de xamarin. Forms para adicionar um efeito a um controle._

## <a name="overview"></a>Visão geral

O `EffectBehavior` classe é um comportamento personalizado reutilizável xamarin. Forms que adiciona um [ `Effect` ](xref:Xamarin.Forms.Effect) instância de um controle quando o comportamento é anexado ao controle e remove o `Effect` quando é o comportamento da instância desanexado do controle.

As seguintes propriedades de comportamento devem ser definidas para usar o comportamento:

- **Grupo** – o valor de [ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute) atributo para a classe de efeito.
- **Nome** – o valor de [ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute) atributo para a classe de efeito.

Para obter mais informações sobre os efeitos, consulte [efeitos](~/xamarin-forms/app-fundamentals/effects/index.md).

## <a name="creating-the-behavior"></a>Criando o comportamento

O `EffectBehavior` classe deriva de [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) classe, onde `T` é um [ `View` ](xref:Xamarin.Forms.View). Isso significa que o `EffectBehavior` classe pode ser anexado a qualquer controle do xamarin. Forms.

### <a name="implementing-bindable-properties"></a>Implementando propriedades vinculáveis

O `EffectBehavior` classe define dois [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) instâncias, que são usadas para adicionar um [ `Effect` ](xref:Xamarin.Forms.Effect) para um controle quando o comportamento é anexado ao controle. Essas propriedades são mostradas no exemplo de código a seguir:

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

Quando o `EffectBehavior` é consumido, o `Group` propriedade deve ser definida como o valor da [ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute) atributo para o efeito. Além disso, o `Name` propriedade deve ser definida como o valor da [ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute) atributo para a classe de efeito.

### <a name="implementing-the-overrides"></a>Implementando as substituições

O `EffectBehavior` substituições de classe de [ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) e [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) métodos do [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) de classe, conforme mostrado no código a seguir exemplo:

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

O [ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) método executa a instalação ao chamar o `AddEffect` método, passando o controle anexado como um parâmetro. O [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) método realiza a limpeza, chamando o `RemoveEffect` método, passando o controle anexado como um parâmetro.

### <a name="implementing-the-behavior-functionality"></a>A implementação da funcionalidade de comportamento

A finalidade do comportamento é adicionar o [ `Effect` ](xref:Xamarin.Forms.Effect) definidos no `Group` e `Name` propriedades para um controle quando o comportamento é anexado ao controle e remover o `Effect` quando o comportamento é desanexado do controle. A principal funcionalidade do comportamento é mostrada no exemplo de código a seguir:

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

O `AddEffect` método é executado em resposta ao `EffectBehavior` que está sendo anexado a um controle e ele recebe o controle anexado como um parâmetro. O método, em seguida, adiciona o efeito recuperado para o controle [ `Effects` ](xref:Xamarin.Forms.Element.Effects) coleção. O `RemoveEffect` método é executado em resposta ao `EffectBehavior` que está sendo desanexado de um controle e ele recebe o controle anexado como um parâmetro. O método, em seguida, remove o efeito do controle [ `Effects` ](xref:Xamarin.Forms.Element.Effects) coleção.

O `GetEffect` usa o [ `Effect.Resolve` ](xref:Xamarin.Forms.Effect.Resolve(System.String)) método para recuperar o [ `Effect` ](xref:Xamarin.Forms.Effect). O efeito é localizado por meio de uma concatenação do `Group` e `Name` valores de propriedade. Se uma plataforma não fornecer o efeito, a `Effect.Resolve` método será retornado um não -`null` valor.

## <a name="consuming-the-behavior"></a>Consumindo o comportamento

O `EffectBehavior` classe pode ser anexado ao [ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors) coleção de um controle, conforme demonstrado no exemplo de código XAML a seguir:

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

O `Group` e `Name` propriedades do comportamento são definidas para os valores da [ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute) e [ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute) atributos para a classe em vigor em cada plataforma específica projeto.

No tempo de execução quando o comportamento é anexado à [ `Label` ](xref:Xamarin.Forms.Label) controle, o `Xamarin.LabelShadowEffect` será adicionado para o controle [ `Effects` ](xref:Xamarin.Forms.Element.Effects) coleção. Isso resulta em uma sombra que está sendo adicionada ao texto exibido pelo `Label` controle, conforme mostrado nas capturas de tela seguir:

![](effect-behavior-images/screenshots.png "Aplicativo de exemplo com EffectsBehavior")

A vantagem de usar esse comportamento para adicionar e remover os efeitos de controles é que o código de manipulação de efeito clichê clichê pode ser removido da arquivos code-behind.

## <a name="summary"></a>Resumo

Este artigo demonstrou como usar um comportamento para adicionar um efeito a um controle. O `EffectBehavior` classe é um comportamento personalizado reutilizável xamarin. Forms que adiciona um [ `Effect` ](xref:Xamarin.Forms.Effect) instância de um controle quando o comportamento é anexado ao controle e remove o `Effect` quando é o comportamento da instância desanexado do controle.


## <a name="related-links"></a>Links relacionados

- [Efeitos](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Comportamento de efeito (amostra)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/effectbehavior/)
- [Comportamento](xref:Xamarin.Forms.Behavior)
- [Comportamento<T>](xref:Xamarin.Forms.Behavior`1)
