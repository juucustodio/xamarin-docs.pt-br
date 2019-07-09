---
title: Comutador de xamarin. Forms
description: A opção de xamarin. Forms é um tipo de botão que pode ser manipulado pelo usuário para alternar entre ativar e desativar estados. Este artigo explica como usar a classe de opção para exibir um elemento de interface do usuário de alternância.
ms.prod: xamarin
ms.assetId: B2F9CC65-481B-4323-8E77-C6BE29C90DE9
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/03/2019
ms.openlocfilehash: ed2d41ea2d9add658d9f07469568a298cdf8de59
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67649689"
---
# <a name="xamarinforms-switch"></a>Comutador de xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/SwitchDemos)

O xamarin. Forms [ `Switch` ](xref:Xamarin.Forms.Switch) é um botão de alternância horizontal que pode ser manipulado pelo usuário para alternar entre ativar e desativar os estados, que são representados por um `boolean` valor. O `Switch` herda [ `View` ](xref:Xamarin.Forms.View).

A captura de tela a seguir mostra uma `Switch` controlar, no seu **na** e **logoff** alternar estados no iOS e Android:

![Captura de tela de comutadores no logon e logoff estados, no iOS e Android](switch-images/switch-states-default.png "alterna no iOS e Android")

O `Switch` controle define duas propriedades:

* [`OnColor`](xref:Xamarin.Forms.Switch.OnColor) é um `Color` que afeta como o `Switch` é renderizado em alternado, ou **em**, estado.
* [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) é um `boolean` valor que indica se o a `Switch` é **em**.

Essas propriedades são apoiadas por um [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objeto, o que significa que o `Switch` podem ser estilizados e ser o destino de vinculações de dados.

O `Switch` controle define uma `Toggled` evento que é disparado quando o `IsToggled` alterações de propriedade, por meio de manipulação de usuário ou quando um aplicativo define o `IsToggled` propriedade. O `ToggledEventArgs` objeto que acompanha o `Toggled` evento tem uma propriedade única chamada `Value`, do tipo `bool`. Quando o evento é acionado, o valor da `Value` propriedade reflete o novo valor da `IsToggled` propriedade.

## <a name="create-a-switch"></a>Criar um comutador

Um `Switch` pode ser instanciada em XAML. Sua `IsToggled` propriedade pode ser definida para alternar o `Switch`. Por padrão, o `IsToggled` é de propriedade `false`. O exemplo a seguir mostra como instanciar uma `Switch` no XAML com opcional `IsToggled` conjunto de propriedades:

```xaml
<Switch IsToggled="true"/>
```

Um ' Switch também pode ser criado no código:

```csharp
Switch switch = new Switch { IsToggled = true };
```

### <a name="switch-style-properties"></a>Propriedades de estilo do comutador

O `OnColor` propriedade pode ser definida para definir o `Switch` cor quando ele é alternado para seus **em** estado. O exemplo a seguir mostra como instanciar uma `Switch` no XAML com o `OnColor` conjunto de propriedades:

```xaml
<Switch OnColor="Orange" />
```

O `OnColor` propriedade também pode ser definida durante a criação de um `Switch` no código:

```csharp
Switch switch = new Switch { OnColor = Color.Orange };
```

A captura de tela a seguir mostra a `Switch` em seu **na** e **desativado** alternar estados, com o `OnColor` propriedade definida como `Color.Orange` no iOS e Android:

![Captura de tela de comutadores no logon e logoff estados, no iOS e Android](switch-images/switch-states-oncolor.png "alterna no iOS e Android")

## <a name="respond-to-a-switch-state-change"></a>Responder a uma alteração de estado do comutador

Quando o `IsToggled` alterações de propriedade, por meio de manipulação de usuário ou quando um aplicativo define o `IsToggled` propriedade, o `Toggled` evento é acionado. Um manipulador de eventos para esse evento pode ser registrado para responder à alteração:

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

O `sender` argumento no manipulador de eventos é o `Switch` responsável por disparar esse evento. Você pode usar o `sender` propriedade para acessar o `Switch` objeto, ou para distinguir entre vários `Switch` objetos que compartilham o mesmo `Toggled` manipulador de eventos.

O `Toggled` também pode ser atribuído no código do manipulador de eventos:

```csharp
Switch switch = new Switch {...};
switch.Toggled += (sender, e) =>
{
    // Perform an action after examining e.Value
}
```

## <a name="data-bind-a-switch"></a>Associar dados de um comutador

O `Toggled` manipulador de eventos pode ser eliminado usando vinculação de dados e gatilhos para responder a um `Switch` alterando estados de alternância.

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

Neste exemplo, o [ `Label` ](xref:Xamarin.Forms.Label) usa uma expressão de associação em um `DataTrigger` para monitorar o `IsToggled` propriedade do `Switch` chamado `styleSwitch`. Quando essa propriedade se torna `true`, o `FontAttributes` e `FontSize` propriedades do `Label` são alterados. Quando o `IsToggled` propriedade retorna à `false`, o `FontAttributes` e `FontSize` propriedades do `Label` serão redefinidos para seu estado inicial.

Para obter informações sobre gatilhos, consulte [xamarin. Forms gatilhos](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="disable-a-switch"></a>Desabilitar um comutador

Um aplicativo pode entrar em um estado em que o `Switch` que está sendo alternado não é uma operação válida. Nesses casos, o `Switch` pode ser desabilitado definindo seu `IsEnabled` propriedade `false`. Isso impedirá que os usuários sejam capazes de manipular o `Switch`.

## <a name="related-links"></a>Links relacionados

* [Demonstrações do comutador](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/SwitchDemos)
* [Gatilhos do xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md)