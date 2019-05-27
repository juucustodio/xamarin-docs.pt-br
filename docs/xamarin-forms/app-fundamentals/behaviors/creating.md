---
title: Criar comportamentos do Xamarin.Forms
description: Os comportamentos do Xamarin.Forms são criados pela derivação da classe Behavior ou Behavior<T>. Este artigo demonstra como criar e consumir comportamentos do Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 300C16FE-A7E0-445B-9099-8E93ABB6F73D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 650c341ab4b599a4eaf0cab81ba6f9129699e15d
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65926032"
---
# <a name="create-xamarinforms-behaviors"></a>Criar comportamentos do Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/Behaviors/NumericValidationBehavior/)

_Os comportamentos do Xamarin.Forms são criados pela derivação da classe Behavior ou Behavior&lt;T&gt;. Este artigo demonstra como criar e consumir comportamentos do Xamarin.Forms._

## <a name="overview"></a>Visão geral

O processo para criar um comportamento do Xamarin.Forms é o seguinte:

1. Criar uma classe que herda da classe [`Behavior`](xref:Xamarin.Forms.Behavior) ou [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1), em que `T` é o tipo do controle ao qual o comportamento deve ser aplicado.
1. Substituir o método [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) para executar qualquer configuração necessária.
1. Substituir o método [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) para executar qualquer limpeza necessária.
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

O método [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) será acionado imediatamente após o comportamento ser anexado a um controle. Esse método recebe uma referência ao controle ao qual ele está anexado e pode ser usado para registrar manipuladores de eventos ou para realizar outra configuração necessária para dar suporte à funcionalidade do comportamento. Por exemplo, você pode assinar um evento em um controle. A funcionalidade do comportamento, então, seria implementada no manipulador do evento.

O método [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) é acionado quando o comportamento é removido do controle. Esse método recebe uma referência ao controle ao qual ele está anexado e é usado para executar qualquer limpeza necessária. Por exemplo, você poderia cancelar a assinatura de um evento em um controle para evitar perdas de memória.

O comportamento, em seguida, poderá ser consumido sendo anexado à coleção [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) do controle apropriado.

## <a name="creating-a-xamarinforms-behavior"></a>Criando um comportamento do Xamarin.Forms

O aplicativo de exemplo demonstra um `NumericValidationBehavior`, que destaca o valor inserido pelo usuário em um controle [`Entry`](xref:Xamarin.Forms.Entry) em vermelho, caso ele não seja um `double`. O comportamento é mostrado no exemplo de código a seguir:

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

O `NumericValidationBehavior` deriva da classe [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1), em que `T` é um [`Entry`](xref:Xamarin.Forms.Entry). O método [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) registra um manipulador de eventos para o evento [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged), com o método [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) cancelando o registro do evento `TextChanged` para evitar perdas de memória. A funcionalidade principal do comportamento é fornecida pelo método `OnEntryTextChanged`, que analisa o valor inserido pelo usuário no `Entry` e define a propriedade [`TextColor`](xref:Xamarin.Forms.Entry.TextColor) como vermelha caso o valor não seja um `double`.

> [!NOTE]
> O Xamarin.Forms não define o `BindingContext` de um comportamento, porque os comportamentos podem ser compartilhados e aplicados a vários controles por meio dos estilos.

## <a name="consuming-a-xamarinforms-behavior"></a>Consumindo um comportamento do Xamarin.Forms

Cada controle do Xamarin.Forms tem uma coleção [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors), à qual um ou mais comportamentos podem ser adicionados, conforme demonstrado no exemplo de código XAML a seguir:

```xaml
<Entry Placeholder="Enter a System.Double">
    <Entry.Behaviors>
        <local:NumericValidationBehavior />
    </Entry.Behaviors>
</Entry>
```

O [`Entry`](xref:Xamarin.Forms.Entry) equivalente em C# é mostrado no exemplo de código a seguir:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
entry.Behaviors.Add (new NumericValidationBehavior ());
```

Em tempo de execução, o comportamento responderá à interação com o controle, de acordo com a implementação do comportamento. As capturas de tela a seguir demonstram o comportamento respondendo a uma entrada inválida:

[![](creating-images/screenshots-sml.png "Aplicativo de exemplo com comportamento do Xamarin.Forms")](creating-images/screenshots.png#lightbox "Aplicativo de exemplo com comportamento do Xamarin.Forms")

> [!NOTE]
> Comportamentos são escritos para um tipo de controle específico (ou uma superclasse que pode ser aplicada a muitos controles) e só devem ser adicionados a um controle compatível. Tentar anexar um comportamento a um controle incompatível fará com que uma exceção seja lançada.

### <a name="consuming-a-xamarinforms-behavior-with-a-style"></a>Consumindo um comportamento do Xamarin.Forms com um estilo

Comportamentos também podem ser consumidos por um estilo explícito ou implícito. No entanto, não é possível criar um estilo que define a propriedade [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) de um controle porque a propriedade é somente leitura. A solução é adicionar uma propriedade anexada à classe do comportamento que controla a adição e a remoção do comportamento. O processo é o seguinte:

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

O [`Style`](xref:Xamarin.Forms.Style) pode ser aplicado a um controle [`Entry`](xref:Xamarin.Forms.Entry) definindo sua propriedade [`Style`](xref:Xamarin.Forms.NavigableElement.Style) para a instância de `Style` usando a extensão de marcação `StaticResource`, conforme demonstrado no exemplo de código a seguir:

```xaml
<Entry Placeholder="Enter a System.Double" Style="{StaticResource NumericValidationStyle}">
```

Para mais informações sobre estilos, confira [Estilos](~/xamarin-forms/user-interface/styles/index.md).

> [!NOTE]
> Embora seja possível adicionar propriedades vinculáveis a um comportamento definido ou consultado no XAML, se você criar comportamentos com estado, eles não deverão ser compartilhados entre os controles em um `Style` em um `ResourceDictionary`.

### <a name="removing-a-behavior-from-a-control"></a>Removendo um comportamento de um controle

O método [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) é acionado quando um comportamento é removido de um controle e é usado para executar qualquer limpeza necessária, como cancelar a assinatura de um evento para evitar uma perda de memória. No entanto, comportamentos não são removidos implicitamente dos controles a menos que a coleção [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) do controle seja modificada por um método `Remove` ou `Clear`. O exemplo de código a seguir demonstra como remover um comportamento específico da coleção `Behaviors` de um controle:

```csharp
var toRemove = entry.Behaviors.FirstOrDefault (b => b is NumericValidationBehavior);
if (toRemove != null) {
    entry.Behaviors.Remove (toRemove);
}
```

Como alternativa, a coleção [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) do controle pode ser limpa, conforme demonstrado no exemplo de código a seguir:

```csharp
entry.Behaviors.Clear();
```

Além disso, observe que os comportamentos não serão removidos implicitamente dos controles quando páginas forem removidas da pilha de navegação. Em vez disso, eles devem ser removidos explicitamente antes que páginas saiam do escopo.

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar e consumir comportamentos do Xamarin.Forms. Comportamentos do Xamarin.Forms são criados derivando da classe [`Behavior`](xref:Xamarin.Forms.Behavior) ou [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1).


## <a name="related-links"></a>Links relacionados

- [Comportamento do Xamarin.Forms (amostra)](https://developer.xamarin.com/samples/xamarin-forms/Behaviors/NumericValidationBehavior/)
- [Comportamento de Xamarin.Forms aplicado com um estilo (amostra)](https://developer.xamarin.com/samples/xamarin-forms/Behaviors/NumericValidationBehaviorStyle/)
- [Comportamento](xref:Xamarin.Forms.Behavior)
- [Comportamento<T>](xref:Xamarin.Forms.Behavior`1)
