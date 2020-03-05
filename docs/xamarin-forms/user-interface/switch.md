---
title: Opção Xamarin. Forms
description: A opção Xamarin. Forms é um tipo de botão que pode ser manipulado pelo usuário para alternar entre os Estados ligado e desligado. Este artigo explica como usar a classe switch para exibir um elemento de interface do usuário de alternância.
ms.prod: xamarin
ms.assetId: B2F9CC65-481B-4323-8E77-C6BE29C90DE9
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/18/2019
ms.openlocfilehash: 88655aabdbd32db63aaf3330a18b0ad8105ea26c
ms.sourcegitcommit: b751605179bef8eee2df92cb484011a7dceb6fda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2020
ms.locfileid: "78291671"
---
# <a name="xamarinforms-switch"></a>Opção Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)

O controle [`Switch`](xref:Xamarin.Forms.Switch) Xamarin. Forms é um botão de alternância horizontal que pode ser manipulado pelo usuário para alternar entre os Estados ligado e desligado, que são representados por um valor de `boolean`. A classe de `Switch` herda de [`View`](xref:Xamarin.Forms.View).

As capturas de tela a seguir mostram um controle de `Switch` em seus Estados de alternância **on** e **off** no Ios e no Android:

![Captura de tela de interruptores em Estados de ligado e off, no iOS e no Android](switch-images/switch-states-default.png "Comutadores no iOS e no Android")

O controle `Switch` define as seguintes propriedades:

* [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) é um valor `boolean` que indica se o `Switch` está **ativado**.
* [`OnColor`](xref:Xamarin.Forms.Switch.OnColor) é um `Color` que afeta como o `Switch` é renderizado no estado alternado ou **ativado**.
* `ThumbColor` é a `Color` do switch Thumb.

Essas propriedades são apoiadas por um objeto [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , o que significa que a `Switch` pode ser estilizada e ser o destino de associações de dados.

O controle de `Switch` define um evento de `Toggled` que é disparado quando a propriedade `IsToggled` é alterada, seja pela manipulação do usuário ou quando um aplicativo define a propriedade `IsToggled`. O objeto `ToggledEventArgs` que acompanha o evento `Toggled` tem uma única propriedade denominada `Value`, do tipo `bool`. Quando o evento é acionado, o valor da propriedade `Value` reflete o novo valor da propriedade `IsToggled`.

## <a name="create-a-switch"></a>Criar uma opção

Um `Switch` pode ser instanciado em XAML. Sua propriedade `IsToggled` pode ser definida para ativar/desativar o `Switch`. Por padrão, a propriedade `IsToggled` é `false`. O exemplo a seguir mostra como criar uma instância de um `Switch` em XAML com a propriedade opcional `IsToggled` definida:

```xaml
<Switch IsToggled="true"/>
```

Um `Switch` também pode ser criado no código:

```csharp
Switch switchControl = new Switch { IsToggled = true };
```

## <a name="switch-appearance"></a>Alternar aparência

Além das propriedades que [`Switch`](xref:Xamarin.Forms.Switch) herda da classe [`View`](xref:Xamarin.Forms.View) , `Switch` também define `OnColor` e `ThumbColor` Propriedades. A propriedade `OnColor` pode ser definida para definir a cor de `Switch` quando ela é alternada para seu estado **on** e a propriedade `ThumbColor` pode ser definida para definir a `Color` do switch Thumb. O exemplo a seguir mostra como criar uma instância de um `Switch` em XAML com essas propriedades definidas:

```xaml
<Switch OnColor="Orange"
        ThumbColor="Green" />
```

As propriedades também podem ser definidas ao criar um `Switch` no código:

```csharp
Switch switch = new Switch { OnColor = Color.Orange, ThumbColor = Color.Green };
```

A captura de tela a seguir mostra o `Switch` em seus Estados de alternância **on** e **off** , com as propriedades `OnColor` e `ThumbColor` definidas:

![Captura de tela de interruptores em Estados de ligado e off, no iOS e no Android](switch-images/switch-states-colors.png "Comutadores no iOS e no Android")

## <a name="respond-to-a-switch-state-change"></a>Responder a uma alteração de estado de comutador

Quando a propriedade `IsToggled` é alterada, seja pela manipulação do usuário ou quando um aplicativo define a propriedade `IsToggled`, o evento `Toggled` é disparado. Um manipulador de eventos para esse evento pode ser registrado para responder à alteração:

```xaml
<Switch Toggled="OnToggled" />
```

O arquivo code-behind contém o manipulador para o evento `Toggled`:

```csharp
void OnToggled(object sender, ToggledEventArgs e)
{
    // Perform an action after examining e.Value
}
```

O argumento `sender` no manipulador de eventos é o `Switch` responsável por acionar esse evento. Você pode usar a propriedade `sender` para acessar o objeto `Switch` ou para distinguir entre vários objetos `Switch` que compartilham o mesmo manipulador de eventos `Toggled`.

O manipulador de eventos `Toggled` também pode ser atribuído no código:

```csharp
Switch switchControl = new Switch {...};
switchControl.Toggled += (sender, e) =>
{
    // Perform an action after examining e.Value
}
```

## <a name="data-bind-a-switch"></a>Associação de dados a um comutador

O manipulador de eventos `Toggled` pode ser eliminado usando a vinculação de dados e os gatilhos para responder a uma `Switch` alterar os Estados de alternância.

```xaml
<Switch x:Name="styleSwitch" />
<Label Text="Lorem ipsum dolor sit amet, elit rutrum, enim hendrerit augue vitae praesent sed non, lorem aenean quis praesent pede.">
    <Label.Triggers>
        <DataTrigger TargetType="Label"
                     Binding="{Binding Source={x:Reference styleSwitch}, Path=IsToggled}"
                     Value="true">
            <Setter Property="FontAttributes"
                    Value="Italic, Bold" />
            <Setter Property="FontSize"
                    Value="Large" />
        </DataTrigger>
    </Label.Triggers>
</Label>
```

Neste exemplo, o [`Label`](xref:Xamarin.Forms.Label) usa uma expressão de associação em uma `DataTrigger` para monitorar a propriedade `IsToggled` do `Switch` chamado `styleSwitch`. Quando essa propriedade se torna `true`, as propriedades `FontAttributes` e `FontSize` do `Label` são alteradas. Quando a propriedade `IsToggled` retorna para `false`, as propriedades `FontAttributes` e `FontSize` da `Label` são redefinidas para o estado inicial.

Para obter informações sobre gatilhos, consulte [gatilhos do Xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="disable-a-switch"></a>Desabilitar uma opção

Um aplicativo pode inserir um estado em que o `Switch` que está sendo alternado não é uma operação válida. Nesses casos, o `Switch` pode ser desabilitado definindo sua propriedade `IsEnabled` como `false`. Isso impedirá que os usuários possam manipular o `Switch`.

## <a name="related-links"></a>Links relacionados

* [Alternar demonstrações](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)
* [Gatilhos do Xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md)
