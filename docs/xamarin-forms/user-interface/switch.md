---
title: Opção Xamarin. Forms
description: A opção Xamarin. Forms é um tipo de botão que pode ser manipulado pelo usuário para alternar entre os Estados ligado e desligado. Este artigo explica como usar a classe switch para exibir um elemento de interface do usuário de alternância.
ms.prod: xamarin
ms.assetId: B2F9CC65-481B-4323-8E77-C6BE29C90DE9
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/18/2019
ms.openlocfilehash: 1f2ef838287e32df5df42f73e4b43816d618552d
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69887883"
---
# <a name="xamarinforms-switch"></a>Opção Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)

O controle Xamarin. [`Switch`](xref:Xamarin.Forms.Switch) Forms é um botão de alternância horizontal que pode ser manipulado pelo usuário para alternar entre os Estados ligado e desligado, que são representados `boolean` por um valor. A `Switch` classe é herdada de [`View`](xref:Xamarin.Forms.View).

As capturas de tela a `Switch` seguir mostram um controle em seus Estados de alternância **on** e **off** no Ios e no Android:

![Captura de tela de interruptores em Estados de ligado e off, no Ios e no Android](switch-images/switch-states-default.png "Comutadores no Ios e no Android")

O `Switch` controle define duas propriedades:

* [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled)é um `boolean` valor que indica se o `Switch` está **ativado**.
* [`OnColor`](xref:Xamarin.Forms.Switch.OnColor)é um `Color` que afeta como o `Switch` é renderizado no estado alternado ou **ativado**.
* `ThumbColor`é o `Color` do switch Thumb.

Essas propriedades são apoiadas por um [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objeto, o que significa `Switch` que o pode ser estilizado e ser o destino de associações de dados.

O `Switch` controle define um `Toggled` evento que é acionado quando `IsToggled` a propriedade é alterada, seja por meio da manipulação do usuário ou `IsToggled` quando um aplicativo define a propriedade. O `ToggledEventArgs` objeto que acompanha o `Toggled` evento tem uma única propriedade chamada `Value`, do tipo `bool`. Quando o evento é acionado, o valor da `Value` propriedade reflete o novo valor `IsToggled` da propriedade.

## <a name="create-a-switch"></a>Criar uma opção

Um `Switch` pode ser instanciado em XAML. Sua `IsToggled` propriedade pode ser definida para alternar o `Switch`. Por padrão, a `IsToggled` propriedade é `false`. O exemplo a seguir mostra como criar uma `Switch` instância de em XAML com `IsToggled` o conjunto de propriedades opcional:

```xaml
<Switch IsToggled="true"/>
```

Um `Switch` também pode ser criado no código:

```csharp
Switch switchControl = new Switch { IsToggled = true };
```

## <a name="switch-appearance"></a>Alternar aparência

Além [`Switch`](xref:Xamarin.Forms.Switch) das propriedades herdadas `Switch` [`View`](xref:Xamarin.Forms.View) da classe, também define `OnColor` e `ThumbColor` Propriedades. A `OnColor` propriedade pode ser definida para definir a `Switch` cor quando ela é alternada para seu estado **on** e a `ThumbColor` propriedade pode ser definida para definir o `Color` do switch Thumb. O exemplo a seguir mostra como criar uma `Switch` instância de em XAML com essas propriedades definidas:

```xaml
<Switch OnColor="Orange"
        ThumbColor="Green" />
```

As propriedades também podem ser definidas ao criar um `Switch` no código:

```csharp
Switch switch = new Switch { OnColor = Color.Orange, ThumbColor = Color.Green };
```

A captura de tela a `Switch` seguir mostra o em seus Estados de alternância liga e `OnColor` **desliga** , com as propriedades e `ThumbColor` definidas:

![Captura de tela de interruptores em Estados de ligado e off, no Ios e no Android](switch-images/switch-states-colors.png "Comutadores no Ios e no Android")

## <a name="respond-to-a-switch-state-change"></a>Responder a uma alteração de estado de comutador

Quando a `IsToggled` propriedade é alterada, seja por meio da manipulação do usuário ou quando `IsToggled` um aplicativo define `Toggled` a propriedade, o evento é acionado. Um manipulador de eventos para esse evento pode ser registrado para responder à alteração:

```xaml
<Switch Toggled="OnToggled" />
```

O arquivo code-behind contém o manipulador para o `Toggled` evento:

```csharp
void OnToggled(object sender, ToggledEventArgs e)
{
    // Perform an action after examining e.Value
}
```

O `sender` argumento no manipulador de eventos é o `Switch` responsável por acionar este evento. Você pode usar a `sender` propriedade para acessar o `Switch` objeto ou para distinguir entre vários `Switch` objetos que compartilham o mesmo `Toggled` manipulador de eventos.

O `Toggled` manipulador de eventos também pode ser atribuído no código:

```csharp
Switch switchControl = new Switch {...};
switchControl.Toggled += (sender, e) =>
{
    // Perform an action after examining e.Value
}
```

## <a name="data-bind-a-switch"></a>Associação de dados a um comutador

O `Toggled` manipulador de eventos pode ser eliminado usando a vinculação de dados e gatilhos `Switch` para responder a uma alteração de Estados de alternância.

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

Neste exemplo, [`Label`](xref:Xamarin.Forms.Label) o usa uma expressão de associação em um `DataTrigger` para `Switch` monitorar a `IsToggled` Propriedade do nome `styleSwitch`. Quando essa propriedade se `true`torna, `FontAttributes` as `FontSize` Propriedades e de `Label` são alteradas. Quando a `IsToggled` propriedade retorna para `false`, `FontAttributes` aspropriedades`FontSize` e de sãoredefinidasparaoestadoinicial.`Label`

Para obter informações sobre gatilhos, consulte gatilhos do [Xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="disable-a-switch"></a>Desabilitar uma opção

Um aplicativo pode inserir um estado em que `Switch` o que está sendo alternado não é uma operação válida. Nesses casos, o `Switch` pode ser desabilitado definindo sua `IsEnabled` Propriedade como `false`. Isso impedirá que os usuários possam manipular o `Switch`.

## <a name="related-links"></a>Links relacionados

* [Alternar demonstrações](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)
* [Gatilhos do Xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md)
