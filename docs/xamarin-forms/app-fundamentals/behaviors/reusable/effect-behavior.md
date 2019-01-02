---
title: EffectBehavior reutilizável
description: Os comportamentos são uma abordagem útil para adicionar um efeito a um controle, removendo o código de texto clichê de tratamento de efeito dos arquivos code-behind. Este artigo demonstra como criar e consumir um comportamento de Xamarin.Forms para adicionar um efeito a um controle.
ms.prod: xamarin
ms.assetid: A909B24D-960A-4023-AFF6-4B9256C55ADD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 5e47799e704dfbe2c4088016d7055fc616215ea2
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53056458"
---
# <a name="reusable-effectbehavior"></a>EffectBehavior reutilizável

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/behaviors/effectbehavior/)

_Os comportamentos são uma abordagem útil para adicionar um efeito a um controle, removendo o código de texto clichê de tratamento de efeito dos arquivos code-behind. Este artigo demonstra como criar e consumir um comportamento de Xamarin.Forms para adicionar um efeito a um controle._

## <a name="overview"></a>Visão geral

A classe `EffectBehavior` é um comportamento personalizado do Xamarin.Forms reutilizável que adiciona uma instância [`Effect`](xref:Xamarin.Forms.Effect) a um controle quando o comportamento é anexado ao controle e remove a instância `Effect` quando o comportamento é desanexado do controle.

As seguintes propriedades de comportamento precisam ser definidas para que o comportamento seja usado:

- **Grupo** – o valor do atributo [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) para a classe de efeito.
- **Name** – o valor do atributo [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) para a classe de efeito.

Para obter mais informações sobre efeitos, confira [Efeitos](~/xamarin-forms/app-fundamentals/effects/index.md).

> [!NOTE]
> O `EffectBehavior` é uma classe personalizada que pode estar localizada no [exemplo de Comportamento de efeito](https://developer.xamarin.com/samples/xamarin-forms/behaviors/effectbehavior/), e não faz parte do Xamarin.Forms.

## <a name="creating-the-behavior"></a>Criação do comportamento

A classe `EffectBehavior` deriva da classe [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1), em que `T` é um [`View`](xref:Xamarin.Forms.View). Isso significa que a classe `EffectBehavior` pode ser anexada a qualquer controle do Xamarin.Forms.

### <a name="implementing-bindable-properties"></a>Implementação de propriedades associáveis

A classe `EffectBehavior` define duas instâncias [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), usadas para adicionar um [`Effect`](xref:Xamarin.Forms.Effect) a um controle quando o comportamento é anexado ao controle. Essas propriedades são mostradas no seguinte exemplo de código:

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

Quando o `EffectBehavior` é consumido, a propriedade `Group` deve ser definida como o valor do atributo [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) para o efeito. Além disso, a propriedade `Name` deve ser definida como o valor do atributo [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) para o efeito.

### <a name="implementing-the-overrides"></a>Implementação de substituições

A classe `EffectBehavior` substitui os métodos [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) e [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) da classe [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1), conforme mostrado no seguinte exemplo de código:

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

O método [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) realiza a instalação chamando o método `AddEffect`, passando o controle anexado como parâmetro. O método [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) realiza a limpeza chamando o método `RemoveEffect`, passando o controle anexado como um parâmetro.

### <a name="implementing-the-behavior-functionality"></a>Implementação da funcionalidade de comportamento

A finalidade do comportamento é adicionar o [`Effect`](xref:Xamarin.Forms.Effect) definido nas propriedades `Group` e `Name` a um controle quando o comportamento é anexado ao controle e remover o `Effect` quando o comportamento é desanexado do controle. A principal funcionalidade de comportamento é mostrada no seguinte exemplo de código:

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

O método `AddEffect` é executado em resposta ao `EffectBehavior` ser anexado a um controle e ele recebe o controle anexado como um parâmetro. O método, então, adiciona o efeito recuperado à coleção [`Effects`](xref:Xamarin.Forms.Element.Effects) do controle. O método `RemoveEffect` é executado em resposta ao `EffectBehavior` ser desanexado de um controle e ele recebe o controle anexado como um parâmetro. O método, então, remove o efeito da coleção [`Effects`](xref:Xamarin.Forms.Element.Effects) do controle.

O método `GetEffect` usa o método [`Effect.Resolve`](xref:Xamarin.Forms.Effect.Resolve(System.String)) para recuperar o [`Effect`](xref:Xamarin.Forms.Effect). O efeito é localizado por meio de uma concatenação dos valores de propriedade `Group` e `Name`. Se uma plataforma não fornecer o efeito, o método `Effect.Resolve` retornará um valor não `null`.

## <a name="consuming-the-behavior"></a>Consumo do comportamento

A classe `EffectBehavior` pode ser anexada à coleção [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) de um controle, conforme demonstrado no seguinte exemplo de código XAML:

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

As propriedades `Group` e `Name` do comportamento são definidas como os valores dos atributos [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) e [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) para a classe de efeito em cada projeto específico à plataforma.

No tempo de execução, quando o comportamento é anexado ao controle [`Label`](xref:Xamarin.Forms.Label), o `Xamarin.LabelShadowEffect` será anexado à coleção [`Effects`](xref:Xamarin.Forms.Element.Effects) do controle. Isso faz com que uma sombra seja adicionada ao texto exibido pelo controle `Label`, conforme mostrado nas capturas de tela seguir:

![](effect-behavior-images/screenshots.png "Aplicativo de exemplo com EffectsBehavior")

A vantagem de usar esse comportamento para adicionar e remover efeitos de controles é que o código de manipulação de efeito clichê pode ser removido de arquivos code-behind.

## <a name="summary"></a>Resumo

Este artigo demonstrou o uso de um comportamento para adicionar um efeito a um controle. A classe `EffectBehavior` é um comportamento personalizado do Xamarin.Forms reutilizável que adiciona uma instância [`Effect`](xref:Xamarin.Forms.Effect) a um controle quando o comportamento é anexado ao controle e remove a instância `Effect` quando o comportamento é desanexado do controle.


## <a name="related-links"></a>Links relacionados

- [Efeitos](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Comportamento de efeito (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/effectbehavior/)
- [Comportamento](xref:Xamarin.Forms.Behavior)
- [Comportamento<T>](xref:Xamarin.Forms.Behavior`1)
