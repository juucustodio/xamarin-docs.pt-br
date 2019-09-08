---
title: Comportamentos anexados
description: Comportamentos anexados são classes estáticas com uma ou mais propriedades anexadas. Este artigo demonstra como criar e consumir comportamentos anexados.
ms.prod: xamarin
ms.assetid: ECEE6AEC-44FA-4AF7-BAD0-88C6EE48422E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: ab39c79c59855c9f78184614176b1658ee0e29b2
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772107"
---
# <a name="attached-behaviors"></a>Comportamentos anexados

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-attachednumericvalidationbehavior)

_Comportamentos anexados são classes estáticas com uma ou mais propriedades anexadas. Este artigo demonstra como criar e consumir comportamentos anexados._

## <a name="overview"></a>Visão geral

Uma propriedade anexada é um tipo especial de propriedade associável. Elas são definidas em uma classe, mas são anexadas a outros objetos e reconhecíveis no XAML como atributos que contêm uma classe e um nome de propriedade separados por um ponto.

Uma propriedade anexada pode definir um delegado `propertyChanged` que será executado quando o valor da propriedade for alterado, por exemplo, quando a propriedade for definida em um controle. Quando o delegado `propertyChanged` é executado, é passada a ele uma referência ao controle a que ele está sendo anexado, bem como parâmetros que contêm os valores antigo e novo da propriedade. Esse delegado pode ser usado para adicionar uma nova funcionalidade ao controle a que a propriedade está anexada manipulando a referência que é passada, da seguinte maneira:

1. O delegado `propertyChanged` converte a referência do controle, que é recebida como um [`BindableObject`](xref:Xamarin.Forms.BindableObject), para o tipo de comportamento que o controle deve melhorar.
1. O delegado `propertyChanged` modifica as propriedades do controle, chama os métodos do controle ou registra manipuladores de eventos para eventos expostos pelo controle, a fim de implementar a funcionalidade do comportamento de núcleo.

Um problema com comportamentos anexados é que eles são definidos em uma classe `static`, com propriedades `static` e métodos. Isso dificulta a criação de comportamentos anexados com estado. Além disso, os comportamentos do Xamarin.Forms substituíram os comportamentos anexados como a abordagem preferencial para a construção de comportamentos. Para obter mais informações sobre comportamentos no Xamarin.Forms, confira [Comportamentos do Xamarin.Forms](~/xamarin-forms/app-fundamentals/behaviors/creating.md) e [Comportamentos reutilizáveis](~/xamarin-forms/app-fundamentals/behaviors/reusable/index.md).

## <a name="creating-an-attached-behavior"></a>Criando um comportamento anexado

O aplicativo de exemplo demonstra um `NumericValidationBehavior`, que destaca o valor inserido pelo usuário em um controle [`Entry`](xref:Xamarin.Forms.Entry) em vermelho, caso ele não seja um `double`. Esse comportamento é mostrado no exemplo de código a seguir:

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

A classe `NumericValidationBehavior` contém uma propriedade anexada chamada `AttachBehavior`, com um getter e setter `static`, que controla a adição ou remoção do comportamento do controle a que ele será anexado. Essa propriedade anexada registra o método `OnAttachBehaviorChanged` que será executado quando o valor da propriedade for alterado. Esse método registra ou cancela o registra de um manipulador de eventos para o evento [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged), com base no valor da propriedade anexada `AttachBehavior`. A funcionalidade núcleo do comportamento é fornecida pelo método `OnEntryTextChanged`, que analisa o valor inserido no [`Entry`](xref:Xamarin.Forms.Entry) pelo usuário e define a propriedade `TextColor` como vermelha caso o valor não seja um `double`.

## <a name="consuming-an-attached-behavior"></a>Consumindo um comportamento anexado

A classe `NumericValidationBehavior` pode ser consumida adicionando a propriedade anexada `AttachBehavior` a um controle [`Entry`](xref:Xamarin.Forms.Entry), conforme demonstrado no exemplo de código XAML a seguir:

```xaml
<ContentPage ... xmlns:local="clr-namespace:WorkingWithBehaviors;assembly=WorkingWithBehaviors" ...>
    ...
    <Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="true" />
    ...
</ContentPage>
```

O [`Entry`](xref:Xamarin.Forms.Entry) equivalente em C# é mostrado no exemplo de código a seguir:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, true);
```

Em tempo de execução, o comportamento responderá à interação com o controle, de acordo com a implementação do comportamento. As capturas de tela a seguir demonstram o comportamento anexado respondendo a uma entrada inválida:

[![](attached-images/screenshots-sml.png "Aplicativo de exemplo com comportamento anexado")](attached-images/screenshots.png#lightbox "Aplicativo de exemplo com comportamento anexado")

> [!NOTE]
> Comportamentos anexados são escritos para um tipo de controle específico (ou uma superclasse que pode ser aplicada a muitos controles) e só devem ser adicionados a um controle compatível. Tentar anexar um comportamento a um controle incompatível resultará em um comportamento desconhecido e depende da implementação do comportamento.

### <a name="removing-an-attached-behavior-from-a-control"></a>Removendo um comportamento anexado de um controle

A classe `NumericValidationBehavior` pode ser removida de um controle definindo a propriedade anexada `AttachBehavior` como `false`, conforme demonstrado no exemplo de código XAML a seguir:

```xaml
<Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="false" />
```

O [`Entry`](xref:Xamarin.Forms.Entry) equivalente em C# é mostrado no exemplo de código a seguir:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, false);
```

No tempo de execução, o método `OnAttachBehaviorChanged` será executado quando o valor da propriedade anexada `AttachBehavior` for definida como `false`. O método `OnAttachBehaviorChanged`, então, cancelará o registro do manipulador de eventos para o evento [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged), garantindo que o comportamento não seja executado conforme o usuário interagir com o controle.

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar e consumir comportamentos anexados. Comportamentos anexados são classes `static` com uma ou mais propriedades anexadas.

## <a name="related-links"></a>Links relacionados

- [Comportamentos anexados (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-attachednumericvalidationbehavior)
