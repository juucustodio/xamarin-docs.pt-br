---
title: Comportamentos de xamarin. Forms
description: Xamarin. Forms comportamentos são criados derivando o comportamento ou comportamento<T> classe. Este artigo demonstra como criar e consumir comportamentos xamarin. Forms.
ms.prod: xamarin
ms.assetid: 300C16FE-A7E0-445B-9099-8E93ABB6F73D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 2848b554d2dbd6d3d69ae864846247b3612d64e6
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="xamarinforms-behaviors"></a>Comportamentos de xamarin. Forms

_Xamarin. Forms comportamentos são criados derivando o comportamento ou comportamento<T> classe. Este artigo demonstra como criar e consumir comportamentos xamarin. Forms._

## <a name="overview"></a>Visão geral

O processo de criação de um comportamento xamarin. Forms é da seguinte maneira:

1. Crie uma classe que herda a [ `Behavior` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/) ou [ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/) classe, onde `T` é o tipo de controle para o qual o comportamento deve ser aplicados.
1. Substituir o [ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/) método para executar qualquer configuração necessária.
1. Substituir o [ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/) método para executar qualquer limpeza exigida.
1. Implemente a funcionalidade básica do comportamento.

Isso resulta na estrutura de mostrado no exemplo de código a seguir:

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

O [ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/) método será acionado imediatamente após o comportamento está anexado a um controle. Este método recebe uma referência para o controle para o qual ele está anexado e pode ser usado para registrar manipuladores de eventos ou executar outra instalação necessária para oferecer suporte à funcionalidade de comportamento. Por exemplo, você pode assinar um evento em um controle. A funcionalidade de comportamento, em seguida, poderia ser implementada no manipulador de eventos para o evento.

O [ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/) método é acionado quando o comportamento é removido do controle. Este método recebe uma referência para o controle para o qual ele está anexado e é usado para realizar qualquer limpeza exigida. Por exemplo, você pode cancelar a assinatura de um evento em um controle para impedir vazamentos de memória.

O comportamento pode ser consumido por anexá-lo para o [ `Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/) coleção do controle apropriado.

## <a name="creating-a-xamarinforms-behavior"></a>Criando um comportamento xamarin. Forms

O aplicativo de exemplo demonstra um `NumericValidationBehavior`, que realça o valor inserido pelo usuário em um [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controlar em vermelho, se não for um `double`. O comportamento é mostrado no exemplo de código a seguir:

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

O `NumericValidationBehavior` deriva o [ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/) classe, onde `T` é um [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/). O [ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/) método registra um manipulador de eventos para o [ `TextChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.TextChanged/) evento, com o [ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/) método eliminação registrando o `TextChanged`perdas de evento para impedir que a memória. A funcionalidade básica do comportamento é fornecida pelo `OnEntryTextChanged` método, que analisa o valor inserido pelo usuário para o `Entry`e define o [ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.TextColor/) propriedade para vermelho se o valor não é um `double`.

> [!NOTE]
> Xamarin. Forms não define o `BindingContext` de um comportamento, porque comportamentos podem ser compartilhados e aplicados a vários controles através de estilos.

## <a name="consuming-a-xamarinforms-behavior"></a>Consumindo um comportamento xamarin. Forms

Cada controle xamarin. Forms tem um [ `Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/) coleta, ao qual um ou mais comportamentos podem ser adicionados, conforme demonstrado no exemplo de código XAML a seguir:

```xaml
<Entry Placeholder="Enter a System.Double">
    <Entry.Behaviors>
        <local:NumericValidationBehavior />
    </Entry.Behaviors>
</Entry>
```

O equivalente [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) c# é mostrado no exemplo de código a seguir:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
entry.Behaviors.Add (new NumericValidationBehavior ());
```

Em tempo de execução o comportamento responderá a interação com o controle, de acordo com a implementação de comportamento. As capturas de tela a seguir demonstram o comportamento respondendo a entrada inválida:

[![](creating-images/screenshots-sml.png "Exemplo de aplicativo com o comportamento do xamarin. Forms")](creating-images/screenshots.png#lightbox "exemplo de aplicativo com o comportamento do xamarin. Forms")

> [!NOTE]
> Comportamentos são gravados para um tipo de controle específicos (ou uma superclasse que pode ser aplicadas a muitos controles) e só devem ser adicionados a um controle compatível. Tentativa de anexar um comportamento a um controle incompatível resultará em uma exceção sendo lançada.

### <a name="consuming-a-xamarinforms-behavior-with-a-style"></a>Consumindo um comportamento xamarin. Forms com um estilo

Comportamentos também podem ser consumidos por um estilo explícito ou implícito. No entanto, a criação de um estilo que define o [ `Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/) propriedade de um controle não é possível porque a propriedade é somente leitura. A solução é adicionar uma propriedade anexada à classe de comportamento que controla adicionando e removendo o comportamento. O processo é o seguinte:

1. Adicione uma propriedade anexada à classe de comportamento que será usado para controlar a adição ou remoção de comportamento para o controle para o qual o comportamento será anexado. Certifique-se de que a propriedade anexada registra um `propertyChanged` delegado que será executado quando o valor da propriedade é alterado.
1. Criar um `static` getter e setter para a propriedade anexada.
1. Implementar a lógica de `propertyChanged` delegado para adicionar e remover o comportamento.

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

O `NumericValidationBehavior` classe contém uma propriedade anexada denominada `AttachBehavior` com um `static` getter e setter, que controla a adição ou remoção de comportamento para o controle para o qual ele será anexado. Isso anexado propriedade registra o `OnAttachBehaviorChanged` método que será executado quando o valor da propriedade é alterado. Este método adiciona ou remove o comportamento para o controle com base no valor da `AttachBehavior` propriedade anexada.

O seguinte exemplo de código mostra uma *explícita* de estilo para o `NumericValidationBehavior` que usa o `AttachBehavior` conectado à propriedade e que podem ser aplicado a [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controles:

```xaml
<Style x:Key="NumericValidationStyle" TargetType="Entry">
    <Style.Setters>
        <Setter Property="local:NumericValidationBehavior.AttachBehavior" Value="true" />
    </Style.Setters>
</Style>
```

O [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) pode ser aplicado a um [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controle definindo seu [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propriedade para a `Style` instância usando o `StaticResource` extensão de marcação, como demonstrado no exemplo de código a seguir:

```xaml
<Entry Placeholder="Enter a System.Double" Style="{StaticResource NumericValidationStyle}">
```

Para obter mais informações sobre estilos, consulte [estilos](~/xamarin-forms/user-interface/styles/index.md).

> [!NOTE]
> Embora você possa adicionar propriedades vinculáveis para um comportamento que está definida ou consultado em XAML, se você criar comportamentos que têm o estado não devem ser compartilhadas entre os controles em um `Style` em um `ResourceDictionary`.

### <a name="removing-a-behavior-from-a-control"></a>Removendo um comportamento de um controle

O [ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/) método é acionado quando um comportamento é removido de um controle e é usado para realizar qualquer limpeza exigida como cancelar a assinatura de um evento para evitar um vazamento de memória. No entanto, comportamentos não são implicitamente removidos dos controles, a menos que o controle [ `Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/) coleta for modificada por um `Remove` ou `Clear` método. O exemplo de código a seguir demonstra a remoção de um comportamento específico de um controle `Behaviors` coleção:

```csharp
var toRemove = entry.Behaviors.FirstOrDefault (b => b is NumericValidationBehavior);
if (toRemove != null) {
    entry.Behaviors.Remove (toRemove);
}
```

Do como alternativa, o controle [ `Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/) coleção pode ser limpa, conforme demonstrado no exemplo de código a seguir:

```csharp
entry.Behaviors.Clear();
```

Além disso, observe que comportamentos não são implicitamente removidos dos controles quando páginas são disparadas da pilha de navegação. Em vez disso, eles devem ser removidos explicitamente antes de páginas fora do escopo.

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar e consumir comportamentos xamarin. Forms. Xamarin. Forms comportamentos são criados derivando de [ `Behavior` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/) ou [ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/) classe.


## <a name="related-links"></a>Links relacionados

- [Comportamento de xamarin. Forms (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/numericvalidationbehavior/)
- [Comportamento do xamarin. Forms aplicado com um estilo (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/numericvalidationbehaviorstyle/)
- [Comportamento](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/)
- [Comportamento<T>](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)
