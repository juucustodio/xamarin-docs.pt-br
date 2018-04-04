---
title: Comportamentos anexados
description: Comportamentos anexados são classes static com uma ou mais propriedades anexadas. Este artigo demonstra como criar e consumir comportamentos anexados.
ms.prod: xamarin
ms.assetid: ECEE6AEC-44FA-4AF7-BAD0-88C6EE48422E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: af891ad1ff1389d5a48c6c47ba1914b8d4dfc20f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="attached-behaviors"></a>Comportamentos anexados

_Comportamentos anexados são classes static com uma ou mais propriedades anexadas. Este artigo demonstra como criar e consumir comportamentos anexados._

## <a name="overview"></a>Visão geral

Uma propriedade anexada é um tipo especial de propriedade associável. Eles são definidos em uma classe, mas anexados a outros objetos, e são reconhecidos no XAML como atributos que contêm uma classe e um nome de propriedade separado por um ponto.

Uma propriedade anexada pode definir um `propertyChanged` delegado que será executado quando o valor da propriedade for alterada, como quando a propriedade é definida em um controle. Quando o `propertyChanged` delegado é executado, ele passou uma referência para o controle no qual ele está anexado e parâmetros que contêm os valores antigos e novos da propriedade. Este delegado pode ser usado para adicionar novas funcionalidades para o controle que a propriedade é anexada a manipulando a referência que é transmitida, da seguinte maneira:

1. O `propertyChanged` delegado converte a referência de controle, que é recebida como um [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/), para o tipo de controle que o comportamento é projetado para melhorar.
1. O `propertyChanged` delegado modifica as propriedades do controle, métodos de chamadas de controle ou registra manipuladores de eventos para eventos expostos pelo controle, para implementar a funcionalidade principal do comportamento.

Um problema com comportamentos anexados é que eles são definidos em um `static` classe com `static` propriedades e métodos. Isso torna difícil criar comportamentos anexados que têm o estado. Além disso, xamarin. Forms comportamentos substituíram comportamentos anexados como a abordagem preferencial para a construção de comportamento. Para obter mais informações sobre comportamentos xamarin. Forms, consulte [xamarin. Forms comportamentos](~/xamarin-forms/app-fundamentals/behaviors/creating.md) e [reutilizável comportamentos](~/xamarin-forms/app-fundamentals/behaviors/reusable/index.md).

## <a name="creating-an-attached-behavior"></a>Criando um comportamento anexado

O aplicativo de exemplo demonstra um `NumericValidationBehavior`, que realça o valor inserido pelo usuário em um [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controlar em vermelho, se não for um `double`. O comportamento é mostrado no exemplo de código a seguir:

```csharp
public static class NumericValidationBehavior
{
    public static readonly BindableProperty AttachBehaviorProperty =
        BindableProperty.CreateAttached (
            "AttachBehavior",
            typeof(bool),
            typeof(NumericValidationBehavior),
            false,
            propertyChanged:OnAttachBehaviorChanged);

    public static bool GetAttachBehavior (BindableObject view)
    {
        return (bool)view.GetValue (AttachBehaviorProperty);
    }

    public static void SetAttachBehavior (BindableObject view, bool value)
    {
        view.SetValue (AttachBehaviorProperty, value);
    }

    static void OnAttachBehaviorChanged (BindableObject view, object oldValue, object newValue)
    {
        var entry = view as Entry;
        if (entry == null) {
            return;
        }

        bool attachBehavior = (bool)newValue;
        if (attachBehavior) {
            entry.TextChanged += OnEntryTextChanged;
        } else {
            entry.TextChanged -= OnEntryTextChanged;
        }
    }

    static void OnEntryTextChanged (object sender, TextChangedEventArgs args)
    {
        double result;
        bool isValid = double.TryParse (args.NewTextValue, out result);
        ((Entry)sender).TextColor = isValid ? Color.Default : Color.Red;
    }
}
```

O `NumericValidationBehavior` classe contém uma propriedade anexada denominada `AttachBehavior` com um `static` getter e setter, que controla a adição ou remoção de comportamento para o controle para o qual ele será anexado. Isso anexado propriedade registra o `OnAttachBehaviorChanged` método que será executado quando o valor da propriedade é alterado. Este método registra ou eliminação registra um manipulador de eventos para o [ `TextChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.TextChanged/) evento, com base no valor da `AttachBehavior` propriedade anexada. A funcionalidade básica do comportamento é fornecida pelo `OnEntryTextChanged` método, que analisa o valor inserido no [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) pelo usuário e define o `TextColor` propriedade para vermelho se o valor não é um `double`.

## <a name="consuming-an-attached-behavior"></a>Consumindo um comportamento anexado

O `NumericValidationBehavior` classe pode ser consumida pela adição a `AttachBehavior` anexado a propriedade para um [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controlar, como demonstrado no exemplo de código XAML a seguir:

```xaml
<ContentPage ... xmlns:local="clr-namespace:WorkingWithBehaviors;assembly=WorkingWithBehaviors" ...>
    ...
    <Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="true" />
    ...
</ContentPage>
```

O equivalente [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) c# é mostrado no exemplo de código a seguir:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, true);
```

Em tempo de execução, o comportamento responderá a interação com o controle, de acordo com a implementação de comportamento. As capturas de tela a seguir demonstram o comportamento de anexado respondendo a entrada inválida:

[![](attached-images/screenshots-sml.png "Exemplo de aplicativo com comportamento anexado")](attached-images/screenshots.png#lightbox "exemplo de aplicativo com comportamento anexado")

> [!NOTE]
> Comportamentos anexados são gravados para um tipo de controle específicos (ou uma superclasse que pode ser aplicadas a muitos controles) e só devem ser adicionados a um controle compatível. Tentativa de anexar um comportamento a um controle incompatível resultará em comportamento desconhecido e depende da implementação de comportamento.

### <a name="removing-an-attached-behavior-from-a-control"></a>Removendo um comportamento anexado de um controle

O `NumericValidationBehavior` classe pode ser removida de um controle definindo o `AttachBehavior` anexado propriedade `false`, conforme demonstrado no exemplo de código XAML a seguir:

```xaml
<Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="false" />
```

O equivalente [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) c# é mostrado no exemplo de código a seguir:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, false);
```

Em tempo de execução, o `OnAttachBehaviorChanged` método será executado quando o valor da `AttachBehavior` propriedade anexada é definida como `false`. O `OnAttachBehaviorChanged` método será, em seguida, cancelar o registro do manipulador de eventos para o [ `TextChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.TextChanged/) evento, garantindo que o comportamento não é executado como o usuário interage com o controle.

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar e consumir comportamentos anexados. Comportamentos anexados são `static` classes com uma ou mais propriedades anexadas.


## <a name="related-links"></a>Links relacionados

- [Comportamentos anexados (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/attachednumericvalidationbehavior/)
