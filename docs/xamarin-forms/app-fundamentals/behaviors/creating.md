---
title: Criar Xamarin.Forms comportamentos
description: Xamarin.Forms os comportamentos são criados pela derivação da classe de comportamento ou comportamento <T> . Este artigo demonstra como criar e consumir Xamarin.Forms comportamentos.
ms.prod: xamarin
ms.assetid: 300C16FE-A7E0-445B-9099-8E93ABB6F73D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f265d1da894b195402c91cbf9468a11837c53bcf
ms.sourcegitcommit: f6a2f07d2e689e0cfd01b30008d50c83c63fa70c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2020
ms.locfileid: "86933701"
---
# <a name="create-no-locxamarinforms-behaviors"></a>Criar Xamarin.Forms comportamentos

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehavior)

_Xamarin.Forms os comportamentos são criados pela derivação da classe de comportamento ou comportamento &lt; T &gt; . Este artigo demonstra como criar e consumir Xamarin.Forms comportamentos._

## <a name="overview"></a>Visão geral

O processo de criação de um Xamarin.Forms comportamento é o seguinte:

1. Crie uma classe que herda da [`Behavior`](xref:Xamarin.Forms.Behavior) classe ou [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) , em que `T` é o tipo do controle ao qual o comportamento deve ser aplicado.
1. Substitua o [ `OnAttachedTo` ] (xref: Xamarin.Forms . Behavior'1. onanexadoto ( Xamarin.Forms . Bindobject)) para executar qualquer configuração necessária.
1. Substitua o [ `OnDetachingFrom` ] (xref: Xamarin.Forms . Behavior'1. OnDetachingFrom ( Xamarin.Forms . Bindobject)) para executar qualquer limpeza necessária.
1. Implementar a funcionalidade principal do comportamento.

Isso resulta na estrutura mostrada no exemplo de código a seguir:

```csharp
public class CustomBehavior : Behavior<View>
{
    protected override void OnAttachedTo (View bindable)
    {
        base.OnAttachedTo (bindable);
        // Perform setup
    }

    protected override void OnDetachingFrom (View bindable)
    {
        base.OnDetachingFrom (bindable);
        // Perform clean up
    }

    // Behavior implementation
}
```

O [ `OnAttachedTo` ] (xref: Xamarin.Forms . Behavior'1. onanexadoto ( Xamarin.Forms . Acopláobject)) é acionado imediatamente após o comportamento ser anexado a um controle. Esse método recebe uma referência ao controle ao qual ele está anexado e pode ser usado para registrar manipuladores de eventos ou para realizar outra configuração necessária para dar suporte à funcionalidade do comportamento. Por exemplo, você pode assinar um evento em um controle. A funcionalidade do comportamento, então, seria implementada no manipulador do evento.

O [ `OnDetachingFrom` ] (xref: Xamarin.Forms . Behavior'1. OnDetachingFrom ( Xamarin.Forms . Acopláobject)) é acionado quando o comportamento é removido do controle. Esse método recebe uma referência ao controle ao qual ele está anexado e é usado para executar qualquer limpeza necessária. Por exemplo, você poderia cancelar a assinatura de um evento em um controle para evitar perdas de memória.

O comportamento pode então ser consumido anexando-o à [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) coleção do controle apropriado.

## <a name="creating-a-no-locxamarinforms-behavior"></a>Criando um Xamarin.Forms comportamento

O aplicativo de exemplo demonstra um `NumericValidationBehavior` , que realça o valor inserido pelo usuário em um [`Entry`](xref:Xamarin.Forms.Entry) controle em vermelho, se não for um `double` . Esse comportamento é mostrado no exemplo de código a seguir:

```csharp
public class NumericValidationBehavior : Behavior<Entry>
{
    protected override void OnAttachedTo(Entry entry)
    {
        entry.TextChanged += OnEntryTextChanged;
        base.OnAttachedTo(entry);
    }

    protected override void OnDetachingFrom(Entry entry)
    {
        entry.TextChanged -= OnEntryTextChanged;
        base.OnDetachingFrom(entry);
    }

    void OnEntryTextChanged(object sender, TextChangedEventArgs args)
    {
        double result;
        bool isValid = double.TryParse (args.NewTextValue, out result);
        ((Entry)sender).TextColor = isValid ? Color.Default : Color.Red;
    }
}
```

O `NumericValidationBehavior` deriva da [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) classe, em que `T` é um [`Entry`](xref:Xamarin.Forms.Entry) . O [ `OnAttachedTo` ] (xref: Xamarin.Forms . Comportamento `1.OnAttachedTo(Xamarin.Forms.BindableObject)) method registers an event handler for the [` TextChanged `](xref:Xamarin.Forms.InputView.TextChanged) event, with the [` OnDetachingFrom `](xref:Xamarin.Forms.Behavior` 1. OnDetachingFrom ( Xamarin.Forms . Bindobject)) do método de registro do `TextChanged` evento para evitar vazamentos de memória. A funcionalidade principal do comportamento é fornecida pelo `OnEntryTextChanged` método, que analisa o valor inserido pelo usuário no `Entry` e define a [`TextColor`](xref:Xamarin.Forms.InputView.TextColor) propriedade como vermelha se o valor não for um `double` .

> [!NOTE]
> Xamarin.Forms não define o `BindingContext` de um comportamento, porque os comportamentos podem ser compartilhados e aplicados a vários controles por meio de estilos.

## <a name="consuming-a-no-locxamarinforms-behavior"></a>Consumindo um Xamarin.Forms comportamento

Cada Xamarin.Forms controle tem uma [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) coleção, à qual um ou mais comportamentos podem ser adicionados, conforme demonstrado no exemplo de código XAML a seguir:

```xaml
<Entry Placeholder="Enter a System.Double">
    <Entry.Behaviors>
        <local:NumericValidationBehavior />
    </Entry.Behaviors>
</Entry>
```

O equivalente [`Entry`](xref:Xamarin.Forms.Entry) em C# é mostrado no seguinte exemplo de código:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
entry.Behaviors.Add (new NumericValidationBehavior ());
```

Em runtime, o comportamento responderá à interação com o controle, de acordo com a implementação do comportamento. As capturas de tela a seguir demonstram o comportamento respondendo a uma entrada inválida:

[![Aplicativo de exemplo com::: no-Loc (Xamarin. Forms)::: Behavior](creating-images/screenshots-sml.png)](creating-images/screenshots.png#lightbox "Aplicativo de exemplo com::: no-Loc (Xamarin. Forms)::: Behavior")

> [!NOTE]
> Comportamentos são escritos para um tipo de controle específico (ou uma superclasse que pode ser aplicada a muitos controles) e só devem ser adicionados a um controle compatível. Tentar anexar um comportamento a um controle incompatível fará com que uma exceção seja lançada.

### <a name="consuming-a-no-locxamarinforms-behavior-with-a-style"></a>Consumindo um Xamarin.Forms comportamento com um estilo

Comportamentos também podem ser consumidos por um estilo explícito ou implícito. No entanto, não é possível criar um estilo que defina a [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) propriedade de um controle porque a propriedade é somente leitura. A solução é adicionar uma propriedade anexada à classe do comportamento que controla a adição e a remoção do comportamento. O processo é o seguinte:

1. Adicione uma propriedade anexada à classe do comportamento que será usado para controlar a adição ou remoção do comportamento do controle a que o comportamento será anexado. Certifique-se de que a propriedade anexada registre um delegado `propertyChanged` que será executado quando o valor da propriedade for alterado.
1. Crie um getter e setter `static` para a propriedade anexada.
1. Implemente a lógica no delegado `propertyChanged` para adicionar e remover o comportamento.

O exemplo de código a seguir mostra uma propriedade anexada que controla a adição e a remoção de `NumericValidationBehavior`:

```csharp
public class NumericValidationBehavior : Behavior<Entry>
{
    public static readonly BindableProperty AttachBehaviorProperty =
        BindableProperty.CreateAttached ("AttachBehavior", typeof(bool), typeof(NumericValidationBehavior), false, propertyChanged: OnAttachBehaviorChanged);

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
            entry.Behaviors.Add (new NumericValidationBehavior ());
        } else {
            var toRemove = entry.Behaviors.FirstOrDefault (b => b is NumericValidationBehavior);
            if (toRemove != null) {
                entry.Behaviors.Remove (toRemove);
            }
        }
    }
    ...
}
```

A classe `NumericValidationBehavior` contém uma propriedade anexada chamada `AttachBehavior`, com um getter e setter `static`, que controla a adição ou remoção do comportamento do controle a que ele será anexado. Essa propriedade anexada registra o método `OnAttachBehaviorChanged` que será executado quando o valor da propriedade for alterado. Esse método adiciona ou remove o comportamento do controle com base no valor da propriedade anexada `AttachBehavior`.

O seguinte exemplo de código mostra um estilo *explícito* para o `NumericValidationBehavior` que usa a propriedade anexada `AttachBehavior` e que pode ser aplicado a controles [`Entry`](xref:Xamarin.Forms.Entry):

```xaml
<Style x:Key="NumericValidationStyle" TargetType="Entry">
    <Style.Setters>
        <Setter Property="local:NumericValidationBehavior.AttachBehavior" Value="true" />
    </Style.Setters>
</Style>
```

O [`Style`](xref:Xamarin.Forms.Style) pode ser aplicado a um [`Entry`](xref:Xamarin.Forms.Entry) controle definindo sua [`Style`](xref:Xamarin.Forms.NavigableElement.Style) propriedade para a `Style` instância usando a `StaticResource` extensão de marcação, conforme demonstrado no exemplo de código a seguir:

```xaml
<Entry Placeholder="Enter a System.Double" Style="{StaticResource NumericValidationStyle}">
```

Para mais informações sobre estilos, confira [Estilos](~/xamarin-forms/user-interface/styles/index.md).

> [!NOTE]
> Embora seja possível adicionar propriedades vinculáveis a um comportamento definido ou consultado no XAML, se você criar comportamentos com estado, eles não deverão ser compartilhados entre os controles em um `Style` em um `ResourceDictionary`.

### <a name="removing-a-behavior-from-a-control"></a>Removendo um comportamento de um controle

O [ `OnDetachingFrom` ] (xref: Xamarin.Forms . Os `1.OnDetachingFrom(Xamarin.Forms.BindableObject)) method is fired when a behavior is removed from a control, and is used to perform any required cleanup such as unsubscribing from an event to prevent a memory leak. However, behaviors are not implicitly removed from controls unless the control's [` comportamentos de comportamento `](xref:Xamarin.Forms.VisualElement.Behaviors) collection is modified by a ` removem a ` or ` coleção de comportamentos claros ` method. The following code example demonstrates removing a specific behavior from a control's ` :

```csharp
var toRemove = entry.Behaviors.FirstOrDefault (b => b is NumericValidationBehavior);
if (toRemove != null) {
    entry.Behaviors.Remove (toRemove);
}
```

Como alternativa, a coleção do controle [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) pode ser limpa, conforme demonstrado no exemplo de código a seguir:

```csharp
entry.Behaviors.Clear();
```

Além disso, observe que os comportamentos não serão removidos implicitamente dos controles quando páginas forem removidas da pilha de navegação. Em vez disso, eles devem ser removidos explicitamente antes que páginas saiam do escopo.

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar e consumir Xamarin.Forms comportamentos. Xamarin.Forms os comportamentos são criados pela derivação da [`Behavior`](xref:Xamarin.Forms.Behavior) [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) classe ou.

## <a name="related-links"></a>Links Relacionados

- [Xamarin.Forms Comportamento (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehavior)
- [Xamarin.Forms Comportamento aplicado com um estilo (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehaviorstyle)
- [Comportamento](xref:Xamarin.Forms.Behavior)
- [Comportamento\<T>](xref:Xamarin.Forms.Behavior`1)
