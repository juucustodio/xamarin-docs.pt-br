---
title: Comportamentos do xamarin. Forms
description: Comportamentos do xamarin. Forms são criados, derivando do comportamento ou comportamento<T> classe. Este artigo demonstra como criar e consumir os comportamentos do xamarin. Forms.
ms.prod: xamarin
ms.assetid: 300C16FE-A7E0-445B-9099-8E93ABB6F73D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 7e057567ec0bb72e9bcc016d4a9fef3af78a3ea1
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998889"
---
# <a name="xamarinforms-behaviors"></a>Comportamentos do xamarin. Forms

_Comportamentos do xamarin. Forms são criados, derivando do comportamento ou comportamento<T> classe. Este artigo demonstra como criar e consumir os comportamentos do xamarin. Forms._

## <a name="overview"></a>Visão geral

O processo para a criação de um comportamento de xamarin. Forms é da seguinte maneira:

1. Criar uma classe que herda de [ `Behavior` ](xref:Xamarin.Forms.Behavior) ou [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) classe, onde `T` é o tipo do controle ao qual o comportamento deve ser aplicada.
1. Substituir a [ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) método para executar qualquer configuração necessária.
1. Substituir a [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) método para executar qualquer limpeza necessária.
1. Implemente a funcionalidade básica do comportamento.

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

O [ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) método será acionado imediatamente após o comportamento é anexado a um controle. Esse método recebe uma referência para o controle ao qual ele está anexado e pode ser usado para registrar manipuladores de eventos ou executar outro programa de instalação necessários para oferecer suporte à funcionalidade de comportamento. Por exemplo, você pode assinar um evento em um controle. A funcionalidade de comportamento, em seguida, seria implementada no manipulador de eventos para o evento.

O [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) método é acionado quando o comportamento é removido do controle. Esse método recebe uma referência para o controle ao qual ele está anexado e é usado para executar qualquer limpeza necessária. Por exemplo, você pode cancelar a assinatura de um evento em um controle para evitar vazamentos de memória.

O comportamento pode ser consumido anexando-o para o [ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors) coleção do controle apropriado.

## <a name="creating-a-xamarinforms-behavior"></a>Criando um comportamento de xamarin. Forms

O aplicativo de exemplo demonstra um `NumericValidationBehavior`, que destaca o valor inserido pelo usuário em um [ `Entry` ](xref:Xamarin.Forms.Entry) controlar em vermelho, se não for um `double`. O comportamento é mostrado no exemplo de código a seguir:

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

O `NumericValidationBehavior` deriva de [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) classe, onde `T` é um [ `Entry` ](xref:Xamarin.Forms.Entry). O [ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) método registra um manipulador de eventos para o [ `TextChanged` ](xref:Xamarin.Forms.Entry.TextChanged) evento, com o [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) método cancelar o registro de `TextChanged`perdas de evento para impedir que a memória. A funcionalidade básica do comportamento é fornecida pelo `OnEntryTextChanged` método, que analisa o valor inserido pelo usuário para o `Entry`e define o [ `TextColor` ](xref:Xamarin.Forms.Entry.TextColor) propriedade para vermelho se o valor não é um `double`.

> [!NOTE]
> Xamarin. Forms não define o `BindingContext` de um comportamento, porque os comportamentos podem ser compartilhados e aplicados a vários controles por meio de estilos.

## <a name="consuming-a-xamarinforms-behavior"></a>Consumindo um comportamento de xamarin. Forms

Cada controle xamarin. Forms tem uma [ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors) coleta, à qual um ou mais comportamentos podem ser adicionados, conforme demonstrado no exemplo de código XAML a seguir:

```xaml
<Entry Placeholder="Enter a System.Double">
    <Entry.Behaviors>
        <local:NumericValidationBehavior />
    </Entry.Behaviors>
</Entry>
```

O equivalente [ `Entry` ](xref:Xamarin.Forms.Entry) em c# é mostrado no exemplo de código a seguir:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
entry.Behaviors.Add (new NumericValidationBehavior ());
```

Em tempo de execução o comportamento será responder à interação com o controle, acordo com a implementação de comportamento. As capturas de tela a seguir demonstram o comportamento de responder à entrada inválida:

[![](creating-images/screenshots-sml.png "Exemplo de aplicativo com o comportamento do xamarin. Forms")](creating-images/screenshots.png#lightbox "exemplo de aplicativo com o comportamento do xamarin. Forms")

> [!NOTE]
> Comportamentos são escritos para um tipo de controle específicos (ou uma superclasse que pode aplicar a muitos controles) e só devem ser adicionadas a um controle compatível. Tentativa de anexar um comportamento a um controle incompatível resultará em uma exceção sendo lançada.

### <a name="consuming-a-xamarinforms-behavior-with-a-style"></a>Consumindo um comportamento de xamarin. Forms com um estilo

Comportamentos também podem ser consumidos por um estilo explícito ou implícito. No entanto, a criação de um estilo que define a [ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors) propriedade de um controle não é possível porque a propriedade é somente leitura. A solução é adicionar uma propriedade anexada à classe de comportamento que controla adicionando e removendo o comportamento. O processo é da seguinte maneira:

1. Adicione uma propriedade anexada à classe de comportamento que será usado para controlar a adição ou remoção do comportamento para o controle ao qual o comportamento será anexado. Certifique-se de que a propriedade anexada registra um `propertyChanged` delegado que será executado quando o valor da propriedade é alterado.
1. Criar um `static` getter e setter para a propriedade anexada.
1. Implementar a lógica no `propertyChanged` delegado para adicionar e remover o comportamento.

O exemplo de código a seguir mostra uma propriedade anexada que controla a adição e remoção de `NumericValidationBehavior`:

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

O `NumericValidationBehavior` classe contém uma propriedade anexada nomeada `AttachBehavior` com um `static` getter e setter, que controla a adição ou remoção do comportamento para o controle ao qual ele será anexado. Isso anexado propriedade registra o `OnAttachBehaviorChanged` método que será executado quando o valor da propriedade é alterado. Esse método adiciona ou remove o comportamento para o controle, com base no valor da `AttachBehavior` propriedade anexada.

O seguinte exemplo de código mostra uma *explícita* de estilo para o `NumericValidationBehavior` que usa o `AttachBehavior` conectado à propriedade e que pode ser aplicado a [ `Entry` ](xref:Xamarin.Forms.Entry) controles:

```xaml
<Style x:Key="NumericValidationStyle" TargetType="Entry">
    <Style.Setters>
        <Setter Property="local:NumericValidationBehavior.AttachBehavior" Value="true" />
    </Style.Setters>
</Style>
```

O [ `Style` ](xref:Xamarin.Forms.Style) pode ser aplicado a um [ `Entry` ](xref:Xamarin.Forms.Entry) controle definindo sua [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) propriedade para o `Style` instância usando o `StaticResource` extensão de marcação, conforme demonstrado no exemplo de código a seguir:

```xaml
<Entry Placeholder="Enter a System.Double" Style="{StaticResource NumericValidationStyle}">
```

Para obter mais informações sobre estilos, consulte [estilos](~/xamarin-forms/user-interface/styles/index.md).

> [!NOTE]
> Embora seja possível adicionar propriedades vinculáveis para um comportamento que é definido ou consultadas no XAML, se você criar comportamentos que têm o estado que eles não devem ser compartilhados entre os controles em uma `Style` em um `ResourceDictionary`.

### <a name="removing-a-behavior-from-a-control"></a>Remover um comportamento de um controle

O [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) método é disparado quando um comportamento é removido de um controle e é usado para executar qualquer limpeza necessária, como cancelar a assinatura de um evento para evitar um vazamento de memória. No entanto, comportamentos não são implicitamente removidos dos controles, a menos que o controle [ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors) coleção é modificada por um `Remove` ou `Clear` método. O exemplo de código a seguir demonstra a remoção de um comportamento específico de um controle `Behaviors` coleção:

```csharp
var toRemove = entry.Behaviors.FirstOrDefault (b => b is NumericValidationBehavior);
if (toRemove != null) {
    entry.Behaviors.Remove (toRemove);
}
```

Como alternativa, do controle [ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors) coleção pode ser desmarcada, conforme demonstrado no exemplo de código a seguir:

```csharp
entry.Behaviors.Clear();
```

Além disso, observe que comportamentos não serão implicitamente removidos dos controles quando páginas são removidas da pilha de navegação. Em vez disso, eles devem ser removidos explicitamente antes de páginas que vão fora do escopo.

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar e consumir os comportamentos do xamarin. Forms. Comportamentos do xamarin. Forms são criados derivando de [ `Behavior` ](xref:Xamarin.Forms.Behavior) ou [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) classe.


## <a name="related-links"></a>Links relacionados

- [Comportamento do xamarin. Forms (amostra)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/numericvalidationbehavior/)
- [Comportamento de xamarin. Forms aplicado com um estilo (amostra)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/numericvalidationbehaviorstyle/)
- [Comportamento](xref:Xamarin.Forms.Behavior)
- [Comportamento<T>](xref:Xamarin.Forms.Behavior`1)
