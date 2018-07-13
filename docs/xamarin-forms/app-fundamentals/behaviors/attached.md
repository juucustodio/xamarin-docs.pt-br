---
title: Comportamentos anexados
description: Comportamentos anexados são classes estáticas com um ou mais propriedades anexadas. Este artigo demonstra como criar e consumir comportamentos anexados.
ms.prod: xamarin
ms.assetid: ECEE6AEC-44FA-4AF7-BAD0-88C6EE48422E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 2c9bd9ad4e7572b9eae6f0073da8a2c8f1e7c9fc
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995340"
---
# <a name="attached-behaviors"></a>Comportamentos anexados

_Comportamentos anexados são classes estáticas com um ou mais propriedades anexadas. Este artigo demonstra como criar e consumir comportamentos anexados._

## <a name="overview"></a>Visão geral

Uma propriedade anexada é um tipo especial de propriedade associável. Eles são definidos em uma classe, mas anexados a outros objetos, e são reconhecidos no XAML como atributos que contêm uma classe e um nome de propriedade separados por um período.

Uma propriedade anexada pode definir um `propertyChanged` delegado que será executado quando o valor da propriedade é alterada, como quando a propriedade é definida em um controle. Quando o `propertyChanged` delegado é executado, ele passou uma referência para o controle no qual ele está anexado e parâmetros que contêm os valores antigo e novo da propriedade. Esse delegado pode ser usado para adicionar uma nova funcionalidade ao controle que a propriedade é anexada ao manipulando a referência que é transmitida, da seguinte maneira:

1. O `propertyChanged` delegado converterá a referência de controle, que é recebida como um [ `BindableObject` ](xref:Xamarin.Forms.BindableObject), para o tipo de controle que o comportamento é projetado para melhorar.
1. O `propertyChanged` delegado modifica as propriedades do controle, chama os métodos do controle ou registra manipuladores de eventos para eventos expostos pelo controle, para implementar a funcionalidade de comportamento de núcleo.

Um problema com comportamentos anexados é que eles são definidos em uma `static` classe, com `static` propriedades e métodos. Isso torna difícil criar comportamentos anexados que têm o estado. Além disso, os comportamentos do xamarin. Forms substituíram comportamentos anexados como a abordagem preferencial para a construção de comportamento. Para obter mais informações sobre os comportamentos do xamarin. Forms, consulte [comportamentos do xamarin. Forms](~/xamarin-forms/app-fundamentals/behaviors/creating.md) e [reutilizável comportamentos](~/xamarin-forms/app-fundamentals/behaviors/reusable/index.md).

## <a name="creating-an-attached-behavior"></a>Criando um comportamento anexado

O aplicativo de exemplo demonstra um `NumericValidationBehavior`, que destaca o valor inserido pelo usuário em um [ `Entry` ](xref:Xamarin.Forms.Entry) controlar em vermelho, se não for um `double`. O comportamento é mostrado no exemplo de código a seguir:

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

O `NumericValidationBehavior` classe contém uma propriedade anexada nomeada `AttachBehavior` com um `static` getter e setter, que controla a adição ou remoção do comportamento para o controle ao qual ele será anexado. Isso anexado propriedade registra o `OnAttachBehaviorChanged` método que será executado quando o valor da propriedade é alterado. Esse método registra ou desprovisionamento registra um manipulador de eventos para o [ `TextChanged` ](xref:Xamarin.Forms.Entry.TextChanged) evento, com base no valor da `AttachBehavior` propriedade anexada. A funcionalidade básica do comportamento é fornecida pelo `OnEntryTextChanged` método, que analisa o valor inserido na [ `Entry` ](xref:Xamarin.Forms.Entry) por usuário e define o `TextColor` propriedade para vermelho se o valor não é um `double`.

## <a name="consuming-an-attached-behavior"></a>Consumindo um comportamento anexado

O `NumericValidationBehavior` classe pode ser consumido, adicionando o `AttachBehavior` anexado à propriedade um [ `Entry` ](xref:Xamarin.Forms.Entry) controlar, conforme demonstrado no exemplo de código XAML a seguir:

```xaml
<ContentPage ... xmlns:local="clr-namespace:WorkingWithBehaviors;assembly=WorkingWithBehaviors" ...>
    ...
    <Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="true" />
    ...
</ContentPage>
```

O equivalente [ `Entry` ](xref:Xamarin.Forms.Entry) em c# é mostrado no exemplo de código a seguir:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, true);
```

Em tempo de execução, o comportamento será responder à interação com o controle, acordo com a implementação de comportamento. As capturas de tela a seguir demonstram o comportamento de anexados respondendo a uma entrada inválida:

[![](attached-images/screenshots-sml.png "Exemplo de aplicativo com comportamento anexado")](attached-images/screenshots.png#lightbox "exemplo de aplicativo com comportamento anexado")

> [!NOTE]
> Comportamentos anexados são escritos para um tipo de controle específicos (ou uma superclasse que pode aplicar a muitos controles) e só devem ser adicionadas a um controle compatível. Tentativa de anexar um comportamento a um controle incompatível resultará em comportamento desconhecido e depende da implementação de comportamento.

### <a name="removing-an-attached-behavior-from-a-control"></a>Remover um comportamento anexado de um controle

O `NumericValidationBehavior` classe pode ser removida de um controle definindo o `AttachBehavior` anexado à propriedade `false`, conforme demonstrado no exemplo de código XAML a seguir:

```xaml
<Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="false" />
```

O equivalente [ `Entry` ](xref:Xamarin.Forms.Entry) em c# é mostrado no exemplo de código a seguir:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, false);
```

Em tempo de execução, o `OnAttachBehaviorChanged` método será executado quando o valor da `AttachBehavior` propriedade anexada é definida como `false`. O `OnAttachBehaviorChanged` método será, em seguida, cancelar o registro do manipulador de eventos para o [ `TextChanged` ](xref:Xamarin.Forms.Entry.TextChanged) evento, garantindo que o comportamento não é executado conforme o usuário interage com o controle.

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar e consumir comportamentos anexados. Comportamentos anexados são `static` classes com um ou mais propriedades anexadas.


## <a name="related-links"></a>Links relacionados

- [Comportamentos anexados (amostra)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/attachednumericvalidationbehavior/)
