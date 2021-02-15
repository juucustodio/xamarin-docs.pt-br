---
title: Comportamentos anexados
description: Comportamentos anexados são classes estáticas com uma ou mais propriedades anexadas. Este artigo demonstra como criar e consumir comportamentos anexados.
ms.prod: xamarin
ms.assetid: ECEE6AEC-44FA-4AF7-BAD0-88C6EE48422E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d6c8154891f6ec295b7082dc6eebe46916d4e4e0
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368051"
---
# <a name="attached-behaviors"></a>Comportamentos anexados

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/behaviors-attachednumericvalidationbehavior)

_Os comportamentos anexados são classes estáticas com uma ou mais propriedades anexadas. Este artigo demonstra como criar e consumir comportamentos anexados._

## <a name="overview"></a>Visão geral

Uma propriedade anexada é um tipo especial de propriedade associável. Elas são definidas em uma classe, mas são anexadas a outros objetos e reconhecíveis no XAML como atributos que contêm uma classe e um nome de propriedade separados por um ponto.

Uma propriedade anexada pode definir um delegado `propertyChanged` que será executado quando o valor da propriedade for alterado, por exemplo, quando a propriedade for definida em um controle. Quando o delegado `propertyChanged` é executado, é passada a ele uma referência ao controle a que ele está sendo anexado, bem como parâmetros que contêm os valores antigo e novo da propriedade. Esse delegado pode ser usado para adicionar uma nova funcionalidade ao controle a que a propriedade está anexada manipulando a referência que é passada, da seguinte maneira:

1. O `propertyChanged` delegado converte a referência de controle, que é recebida como um [`BindableObject`](xref:Xamarin.Forms.BindableObject) , para o tipo de controle que o comportamento foi projetado para aprimorar.
1. O delegado `propertyChanged` modifica as propriedades do controle, chama os métodos do controle ou registra manipuladores de eventos para eventos expostos pelo controle, a fim de implementar a funcionalidade do comportamento de núcleo.

Um problema com comportamentos anexados é que eles são definidos em uma classe `static`, com propriedades `static` e métodos. Isso dificulta a criação de comportamentos anexados com estado. Além disso, Xamarin.Forms os comportamentos substituíram os comportamentos anexados como a abordagem preferida para a construção do comportamento. Para obter mais informações sobre Xamarin.Forms comportamentos, consulte [ Xamarin.Forms comportamentos](~/xamarin-forms/app-fundamentals/behaviors/creating.md).

## <a name="creating-an-attached-behavior"></a>Criando um comportamento anexado

O aplicativo de exemplo demonstra um `NumericValidationBehavior` , que realça o valor inserido pelo usuário em um [`Entry`](xref:Xamarin.Forms.Entry) controle em vermelho, se não for um `double` . Esse comportamento é mostrado no exemplo de código a seguir:

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

A classe `NumericValidationBehavior` contém uma propriedade anexada chamada `AttachBehavior`, com um getter e setter `static`, que controla a adição ou remoção do comportamento do controle a que ele será anexado. Essa propriedade anexada registra o método `OnAttachBehaviorChanged` que será executado quando o valor da propriedade for alterado. Esse método registra ou Desregistra um manipulador de eventos para o [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) evento, com base no valor da `AttachBehavior` Propriedade anexada. A funcionalidade principal do comportamento é fornecida pelo `OnEntryTextChanged` método, que analisa o valor inserido no [`Entry`](xref:Xamarin.Forms.Entry) pelo usuário e define a `TextColor` propriedade como vermelha se o valor não for um `double` .

## <a name="consuming-an-attached-behavior"></a>Consumindo um comportamento anexado

A `NumericValidationBehavior` classe pode ser consumida adicionando a `AttachBehavior` Propriedade anexada a um [`Entry`](xref:Xamarin.Forms.Entry) controle, conforme demonstrado no exemplo de código XAML a seguir:

```xaml
<ContentPage ... xmlns:local="clr-namespace:WorkingWithBehaviors;assembly=WorkingWithBehaviors" ...>
    ...
    <Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="true" />
    ...
</ContentPage>
```

O equivalente [`Entry`](xref:Xamarin.Forms.Entry) em C# é mostrado no seguinte exemplo de código:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, true);
```

Em runtime, o comportamento responderá à interação com o controle, de acordo com a implementação do comportamento. As capturas de tela a seguir demonstram o comportamento anexado respondendo a uma entrada inválida:

[![Aplicativo de Exemplo com Comportamento Anexado](attached-images/screenshots-sml.png)](attached-images/screenshots.png#lightbox "Aplicativo de Exemplo com Comportamento Anexado")

> [!NOTE]
> Comportamentos anexados são escritos para um tipo de controle específico (ou uma superclasse que pode ser aplicada a muitos controles) e só devem ser adicionados a um controle compatível. Tentar anexar um comportamento a um controle incompatível resultará em um comportamento desconhecido e depende da implementação do comportamento.

### <a name="removing-an-attached-behavior-from-a-control"></a>Removendo um comportamento anexado de um controle

A classe `NumericValidationBehavior` pode ser removida de um controle definindo a propriedade anexada `AttachBehavior` como `false`, conforme demonstrado no exemplo de código XAML a seguir:

```xaml
<Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="false" />
```

O equivalente [`Entry`](xref:Xamarin.Forms.Entry) em C# é mostrado no seguinte exemplo de código:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, false);
```

No runtime, o método `OnAttachBehaviorChanged` será executado quando o valor da propriedade anexada `AttachBehavior` for definida como `false`. O `OnAttachBehaviorChanged` método então desregistrará o manipulador de eventos para o [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) evento, garantindo que o comportamento não seja executado enquanto o usuário interage com o controle.

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar e consumir comportamentos anexados. Comportamentos anexados são classes `static` com uma ou mais propriedades anexadas.

## <a name="related-links"></a>Links Relacionados

- [Comportamentos anexados (amostra)](/samples/xamarin/xamarin-forms-samples/behaviors-attachednumericvalidationbehavior)