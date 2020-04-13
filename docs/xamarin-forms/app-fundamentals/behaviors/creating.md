---
title: Criar comportamentos do Xamarin.Forms
description: Os comportamentos do Xamarin.Forms são criados pela derivação da classe Behavior ou Behavior<T>. Este artigo demonstra como criar e consumir comportamentos do Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 300C16FE-A7E0-445B-9099-8E93ABB6F73D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 42ad56a7ae34bcef638ed25bea267dcabd21e20c
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "77131085"
---
# <a name="create-xamarinforms-behaviors"></a>Criar comportamentos do Xamarin.Forms

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehavior)

_Os comportamentos xamarin.forms são criados por derivada da classe Comportamento ou Comportamento&lt;T.&gt; Este artigo demonstra como criar e consumir comportamentos xamarin.Forms._

## <a name="overview"></a>Visão geral

O processo para criar um comportamento do Xamarin.Forms é o seguinte:

1. Crie uma classe que [`Behavior`](xref:Xamarin.Forms.Behavior) herde da classe ou, [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) onde `T` está o tipo de controle ao qual o comportamento deve ser aplicado.
1. Anular o [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) método para executar qualquer configuração necessária.
1. Anular o [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) método para realizar qualquer limpeza necessária.
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

O [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) método é acionado imediatamente após o comportamento ser anexado a um controle. Esse método recebe uma referência ao controle ao qual ele está anexado e pode ser usado para registrar manipuladores de eventos ou para realizar outra configuração necessária para dar suporte à funcionalidade do comportamento. Por exemplo, você pode assinar um evento em um controle. A funcionalidade do comportamento, então, seria implementada no manipulador do evento.

O [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) método é acionado quando o comportamento é removido do controle. Esse método recebe uma referência ao controle ao qual ele está anexado e é usado para executar qualquer limpeza necessária. Por exemplo, você poderia cancelar a assinatura de um evento em um controle para evitar perdas de memória.

O comportamento pode então ser consumido [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) anexando-o à coleta do controle apropriado.

## <a name="creating-a-xamarinforms-behavior"></a>Criando um comportamento do Xamarin.Forms

O aplicativo de `NumericValidationBehavior`amostra demonstra um , que destaca [`Entry`](xref:Xamarin.Forms.Entry) o valor inserido pelo usuário `double`em um controle em vermelho, se não for um . Esse comportamento é mostrado no exemplo de código a seguir:

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

O `NumericValidationBehavior` deriva da [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) classe, `T` onde [`Entry`](xref:Xamarin.Forms.Entry)é um . O [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) método registra um manipulador [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) de eventos [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) para o evento, com o método de descadastrar o `TextChanged` evento para evitar vazamentos de memória. A funcionalidade central do comportamento é `OnEntryTextChanged` fornecida pelo método, que analisa o valor `Entry`inserido pelo [`TextColor`](xref:Xamarin.Forms.InputView.TextColor) usuário no , e define `double`a propriedade como vermelha se o valor não for um .

> [!NOTE]
> O Xamarin.Forms não define o `BindingContext` de um comportamento, porque os comportamentos podem ser compartilhados e aplicados a vários controles por meio dos estilos.

## <a name="consuming-a-xamarinforms-behavior"></a>Consumindo um comportamento do Xamarin.Forms

Todo controle Xamarin.Forms [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) tem uma coleção, à qual um ou mais comportamentos podem ser adicionados, como demonstrado no seguinte exemplo de código XAML:

```xaml
<Entry Placeholder="Enter a System.Double">
    <Entry.Behaviors>
        <local:NumericValidationBehavior />
    </Entry.Behaviors>
</Entry>
```

O [`Entry`](xref:Xamarin.Forms.Entry) equivalente em C# é mostrado no seguinte exemplo de código:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
entry.Behaviors.Add (new NumericValidationBehavior ());
```

Em runtime, o comportamento responderá à interação com o controle, de acordo com a implementação do comportamento. As capturas de tela a seguir demonstram o comportamento respondendo a uma entrada inválida:

[![](creating-images/screenshots-sml.png "Sample Application with Xamarin.Forms Behavior")](creating-images/screenshots.png#lightbox "Sample Application with Xamarin.Forms Behavior")

> [!NOTE]
> Comportamentos são escritos para um tipo de controle específico (ou uma superclasse que pode ser aplicada a muitos controles) e só devem ser adicionados a um controle compatível. Tentar anexar um comportamento a um controle incompatível fará com que uma exceção seja lançada.

### <a name="consuming-a-xamarinforms-behavior-with-a-style"></a>Consumindo um comportamento do Xamarin.Forms com um estilo

Comportamentos também podem ser consumidos por um estilo explícito ou implícito. No entanto, criar [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) um estilo que define a propriedade de um controle não é possível porque a propriedade é somente leitura. A solução é adicionar uma propriedade anexada à classe do comportamento que controla a adição e a remoção do comportamento. O processo é o seguinte:

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

O [`Style`](xref:Xamarin.Forms.Style) pode ser aplicado [`Entry`](xref:Xamarin.Forms.Entry) a um [`Style`](xref:Xamarin.Forms.NavigableElement.Style) controle `Style` definindo `StaticResource` sua propriedade na instância usando a extensão de marcação, conforme demonstrado no exemplo de código a seguir:

```xaml
<Entry Placeholder="Enter a System.Double" Style="{StaticResource NumericValidationStyle}">
```

Para mais informações sobre estilos, confira [Estilos](~/xamarin-forms/user-interface/styles/index.md).

> [!NOTE]
> Embora seja possível adicionar propriedades vinculáveis a um comportamento definido ou consultado no XAML, se você criar comportamentos com estado, eles não deverão ser compartilhados entre os controles em um `Style` em um `ResourceDictionary`.

### <a name="removing-a-behavior-from-a-control"></a>Removendo um comportamento de um controle

O [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) método é acionado quando um comportamento é removido de um controle, e é usado para realizar qualquer limpeza necessária, como não subscrever de um evento para evitar um vazamento de memória. No entanto, os comportamentos não são implicitamente [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) removidos dos `Remove` `Clear` controles, a menos que a coleção do controle seja modificada por um ou método. O exemplo de código a seguir demonstra como remover um comportamento específico da coleção `Behaviors` de um controle:

```csharp
var toRemove = entry.Behaviors.FirstOrDefault (b => b is NumericValidationBehavior);
if (toRemove != null) {
    entry.Behaviors.Remove (toRemove);
}
```

Alternativamente, a coleção [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) do controle pode ser limpa, como demonstrado no exemplo do código a seguir:

```csharp
entry.Behaviors.Clear();
```

Além disso, observe que os comportamentos não serão removidos implicitamente dos controles quando páginas forem removidas da pilha de navegação. Em vez disso, eles devem ser removidos explicitamente antes que páginas saiam do escopo.

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar e consumir comportamentos do Xamarin.Forms. Os comportamentos do Xamarin.Forms são criados por derivada da [`Behavior`](xref:Xamarin.Forms.Behavior) classe ou [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) da classe.

## <a name="related-links"></a>Links relacionados

- [Comportamento do Xamarin.Forms (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehavior)
- [Comportamento de Xamarin.Forms aplicado com um estilo (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehaviorstyle)
- [Comportamento](xref:Xamarin.Forms.Behavior)
- [Comportamento\<T>](xref:Xamarin.Forms.Behavior`1)
