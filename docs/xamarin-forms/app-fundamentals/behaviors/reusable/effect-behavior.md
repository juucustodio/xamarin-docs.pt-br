---
title: EffectBehavior reutilizável
description: Os comportamentos são uma abordagem útil para adicionar um efeito a um controle, removendo o código de texto clichê de tratamento de efeito dos arquivos code-behind. Este artigo demonstra como criar e consumir um comportamento de Xamarin.Forms para adicionar um efeito a um controle.
ms.prod: xamarin
ms.assetid: A909B24D-960A-4023-AFF6-4B9256C55ADD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: ca03dce3bd39664a07b7bf56d22d7c2e000e931f
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771991"
---
# <a name="reusable-effectbehavior"></a>EffectBehavior reutilizável

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-effectbehavior)

_Os comportamentos são uma abordagem útil para adicionar um efeito a um controle, removendo o código de manuseio do efeito da placa da caldeira de arquivos de código atrás. Este artigo demonstra a criação e o consumo de um comportamento Xamarin.Forms para adicionar um efeito a um controle._

## <a name="overview"></a>Visão geral

A `EffectBehavior` classe é um Xamarin.Forms personalizado reutilizável que adiciona uma [`Effect`](xref:Xamarin.Forms.Effect) instância a um controle quando `Effect` o comportamento é anexado ao controle e remove a instância quando o comportamento é desvinculado do controle.

As seguintes propriedades de comportamento precisam ser definidas para que o comportamento seja usado:

- **Grupo** – o valor do atributo [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) para a classe de efeito.
- **Name** – o valor do atributo [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) para a classe de efeito.

Para obter mais informações sobre efeitos, confira [Efeitos](~/xamarin-forms/app-fundamentals/effects/index.md).

> [!NOTE]
> O `EffectBehavior` é uma classe personalizada que pode estar localizada no [exemplo de Comportamento de efeito](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-effectbehavior), e não faz parte do Xamarin.Forms.

## <a name="creating-the-behavior"></a>Criação do comportamento

A `EffectBehavior` classe deriva [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) da classe, `T` [`View`](xref:Xamarin.Forms.View)onde é um . Isso significa que a classe `EffectBehavior` pode ser anexada a qualquer controle do Xamarin.Forms.

### <a name="implementing-bindable-properties"></a>Implementação de propriedades associáveis

A `EffectBehavior` classe define [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) duas instâncias, que [`Effect`](xref:Xamarin.Forms.Effect) são usadas para adicionar um a um controle quando o comportamento é anexado ao controle. Essas propriedades são mostradas no seguinte exemplo de código:

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

Quando `EffectBehavior` o valor `Group` é consumido, a propriedade [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) deve ser definida para o valor do atributo para o efeito. Além disso, `Name` a propriedade deve ser [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) definida no valor do atributo para a classe de efeito.

### <a name="implementing-the-overrides"></a>Implementação de substituições

A `EffectBehavior` classe substitui [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) os métodos [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) e métodos da classe, como mostrado no exemplo de código a seguir:

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

O [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) método executa a `AddEffect` configuração chamando o método, passando no controle anexado como parâmetro. O [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) método realiza a `RemoveEffect` limpeza chamando o método, passando no controle anexado como parâmetro.

### <a name="implementing-the-behavior-functionality"></a>Implementação da funcionalidade de comportamento

O objetivo do comportamento é [`Effect`](xref:Xamarin.Forms.Effect) adicionar `Group` o `Name` definido nas propriedades e a um controle quando `Effect` o comportamento é anexado ao controle, e remover o quando o comportamento é desvinculado do controle. A principal funcionalidade de comportamento é mostrada no seguinte exemplo de código:

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

O método `AddEffect` é executado em resposta ao `EffectBehavior` ser anexado a um controle e ele recebe o controle anexado como um parâmetro. O método então adiciona o efeito recuperado [`Effects`](xref:Xamarin.Forms.Element.Effects) à coleção do controle. O método `RemoveEffect` é executado em resposta ao `EffectBehavior` ser desanexado de um controle e ele recebe o controle anexado como um parâmetro. O método então remove o efeito [`Effects`](xref:Xamarin.Forms.Element.Effects) da coleção do controle.

O `GetEffect` método [`Effect.Resolve`](xref:Xamarin.Forms.Effect.Resolve(System.String)) usa o [`Effect`](xref:Xamarin.Forms.Effect)método para recuperar o . O efeito é localizado por meio de uma concatenação dos valores de propriedade `Group` e `Name`. Se uma plataforma não fornecer o efeito, o método `Effect.Resolve` retornará um valor não `null`.

## <a name="consuming-the-behavior"></a>Consumo do comportamento

A `EffectBehavior` classe pode ser [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) anexada à coleção de um controle, conforme demonstrado no seguinte exemplo de código XAML:

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

As `Group` `Name` propriedades do comportamento são definidas [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) para [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) os valores e atributos para a classe de efeito em cada projeto específico da plataforma.

No tempo de execução, quando [`Label`](xref:Xamarin.Forms.Label) o comportamento `Xamarin.LabelShadowEffect` é anexado ao [`Effects`](xref:Xamarin.Forms.Element.Effects) controle, o será adicionado à coleção do controle. Isso faz com que uma sombra seja adicionada ao texto exibido pelo controle `Label`, conforme mostrado nas capturas de tela seguir:

![](effect-behavior-images/screenshots.png "Sample Application with EffectsBehavior")

A vantagem de usar esse comportamento para adicionar e remover efeitos de controles é que o código de manipulação de efeito clichê pode ser removido de arquivos code-behind.

## <a name="summary"></a>Resumo

Este artigo demonstrou o uso de um comportamento para adicionar um efeito a um controle. A `EffectBehavior` classe é um Xamarin.Forms personalizado reutilizável que adiciona uma [`Effect`](xref:Xamarin.Forms.Effect) instância a um controle quando `Effect` o comportamento é anexado ao controle e remove a instância quando o comportamento é desvinculado do controle.

## <a name="related-links"></a>Links relacionados

- [Efeitos](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Comportamento de efeito (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-effectbehavior)
- [Comportamento](xref:Xamarin.Forms.Behavior)
- [Comportamento&lt;T&gt;](xref:Xamarin.Forms.Behavior`1)
