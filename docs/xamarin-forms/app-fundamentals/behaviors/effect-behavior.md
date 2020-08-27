---
title: EffectBehavior reutilizável
description: Os comportamentos são uma abordagem útil para adicionar um efeito a um controle, removendo o código de texto clichê de tratamento de efeito dos arquivos code-behind. Este artigo demonstra como criar e consumir um Xamarin.Forms comportamento para adicionar um efeito a um controle.
ms.prod: xamarin
ms.assetid: A909B24D-960A-4023-AFF6-4B9256C55ADD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7ecc094abf816f11dcb16d9eba7d715c8c4e4ab1
ms.sourcegitcommit: a003b036f6fb83818e2ecc9c72a641e3aeb373bd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88964810"
---
# <a name="reusable-effectbehavior"></a>EffectBehavior reutilizável

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-effectbehavior)

_Os comportamentos são uma abordagem útil para adicionar um efeito a um controle, removendo o código de manipulação de efeito de placa de apoio dos arquivos code-behind. Este artigo demonstra como criar e consumir um Xamarin.Forms comportamento para adicionar um efeito a um controle._

## <a name="overview"></a>Visão geral

A `EffectBehavior` classe é um comportamento personalizado reutilizável Xamarin.Forms que adiciona uma [`Effect`](xref:Xamarin.Forms.Effect) instância a um controle quando o comportamento é anexado ao controle e remove a `Effect` instância quando o comportamento é desanexado do controle.

As seguintes propriedades de comportamento precisam ser definidas para que o comportamento seja usado:

- **Grupo** – o valor do atributo [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) para a classe de efeito.
- **Name** – o valor do atributo [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) para a classe de efeito.

Para obter mais informações sobre efeitos, confira [Efeitos](~/xamarin-forms/app-fundamentals/effects/index.md).

> [!NOTE]
> O `EffectBehavior` é uma classe personalizada que pode ser localizada no [exemplo de comportamento de efeito](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-effectbehavior)e não faz parte do Xamarin.Forms .

## <a name="creating-the-behavior"></a>Criação do comportamento

A `EffectBehavior` classe deriva da [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) classe, onde `T` é um [`View`](xref:Xamarin.Forms.View) . Isso significa que a `EffectBehavior` classe pode ser anexada a qualquer Xamarin.Forms controle.

### <a name="implementing-bindable-properties"></a>Implementação de propriedades associáveis

A `EffectBehavior` classe define duas [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) instâncias, que são usadas para adicionar um [`Effect`](xref:Xamarin.Forms.Effect) a um controle quando o comportamento é anexado ao controle. Essas propriedades são mostradas no seguinte exemplo de código:

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

Quando o `EffectBehavior` for consumido, a `Group` Propriedade deverá ser definida como o valor do [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) atributo para o efeito. Além disso, a `Name` propriedade deve ser definida como o valor do [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) atributo para a classe Effect.

### <a name="implementing-the-overrides"></a>Implementação de substituições

A `EffectBehavior` classe substitui o [ `OnAttachedTo` ] (xref: Xamarin.Forms . Comportamento `1.OnAttachedTo(Xamarin.Forms.BindableObject)) and [` OnDetachingFrom `](xref:Xamarin.Forms.Behavior` 1. OnDetachingFrom ( Xamarin.Forms . Bindobject)) da [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) classe, conforme mostrado no exemplo de código a seguir:

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

O [ `OnAttachedTo` ] (xref: Xamarin.Forms . Comportamento `1.OnAttachedTo(Xamarin.Forms.BindableObject)) method performs setup by calling the ` addeffect ` method, passing in the attached control as a parameter. The [` OnDetachingFrom `](xref:Xamarin.Forms.Behavior` 1. OnDetachingFrom ( Xamarin.Forms . Acopláobject)) executa a limpeza chamando o `RemoveEffect` método, passando o controle anexado como um parâmetro.

### <a name="implementing-the-behavior-functionality"></a>Implementação da funcionalidade de comportamento

A finalidade do comportamento é adicionar o [`Effect`](xref:Xamarin.Forms.Effect) definido nas `Group` `Name` Propriedades e a um controle quando o comportamento é anexado ao controle e remover o `Effect` quando o comportamento é desanexado do controle. A principal funcionalidade de comportamento é mostrada no seguinte exemplo de código:

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

O método `AddEffect` é executado em resposta ao `EffectBehavior` ser anexado a um controle e ele recebe o controle anexado como um parâmetro. O método, em seguida, adiciona o efeito recuperado à [`Effects`](xref:Xamarin.Forms.Element.Effects) coleção do controle. O método `RemoveEffect` é executado em resposta ao `EffectBehavior` ser desanexado de um controle e ele recebe o controle anexado como um parâmetro. O método, em seguida, remove o efeito da [`Effects`](xref:Xamarin.Forms.Element.Effects) coleção do controle.

O `GetEffect` método usa o [`Effect.Resolve`](xref:Xamarin.Forms.Effect.Resolve(System.String)) método para recuperar o [`Effect`](xref:Xamarin.Forms.Effect) . O efeito é localizado por meio de uma concatenação dos valores de propriedade `Group` e `Name`. Se uma plataforma não fornecer o efeito, o método `Effect.Resolve` retornará um valor não `null`.

## <a name="consuming-the-behavior"></a>Consumo do comportamento

A `EffectBehavior` classe pode ser anexada à [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) coleção de um controle, conforme demonstrado no exemplo de código XAML a seguir:

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

As `Group` `Name` Propriedades e do comportamento são definidas para os valores dos [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) atributos e da [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) classe Effect em cada projeto específico da plataforma.

Em tempo de execução, quando o comportamento é anexado ao [`Label`](xref:Xamarin.Forms.Label) controle, o `Xamarin.LabelShadowEffect` será adicionado à coleção do controle [`Effects`](xref:Xamarin.Forms.Element.Effects) . Isso faz com que uma sombra seja adicionada ao texto exibido pelo controle `Label`, conforme mostrado nas capturas de tela seguir:

![Aplicativo de exemplo com EffectsBehavior](effect-behavior-images/screenshots.png)

A vantagem de usar esse comportamento para adicionar e remover efeitos de controles é que o código de manipulação de efeito clichê pode ser removido de arquivos code-behind.

## <a name="summary"></a>Resumo

Este artigo demonstrou o uso de um comportamento para adicionar um efeito a um controle. A `EffectBehavior` classe é um comportamento personalizado reutilizável Xamarin.Forms que adiciona uma [`Effect`](xref:Xamarin.Forms.Effect) instância a um controle quando o comportamento é anexado ao controle e remove a `Effect` instância quando o comportamento é desanexado do controle.

## <a name="related-links"></a>Links Relacionados

- [Efeitos](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Comportamento de efeito (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-effectbehavior)
- [Comportamento](xref:Xamarin.Forms.Behavior)
- [Comportamento &lt; T&gt;](xref:Xamarin.Forms.Behavior`1)
