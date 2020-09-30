---
title: Xamarin.Forms Comutador
description: A Xamarin.Forms opção é um tipo de botão que pode ser manipulado pelo usuário para alternar entre os Estados ligado e desligado. Este artigo explica como usar a classe switch para exibir um elemento de interface do usuário de alternância.
ms.prod: xamarin
ms.assetId: B2F9CC65-481B-4323-8E77-C6BE29C90DE9
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 05/19/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 94f77fd70fee595efd341ff7372828b12661442d
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91561723"
---
# <a name="no-locxamarinforms-switch"></a>Xamarin.Forms Comutador

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)

O Xamarin.Forms [`Switch`](xref:Xamarin.Forms.Switch) controle é um botão de alternância horizontal que pode ser manipulado pelo usuário para alternar entre os Estados ligado e desligado, que são representados por um `boolean` valor. A `Switch` classe é herdada de [`View`](xref:Xamarin.Forms.View) .

As capturas de tela a seguir mostram um `Switch` controle em seus Estados de alternância **on** e **off** no Ios e no Android:

![Captura de tela de interruptores em Estados de ligado e off, no iOS e no Android](switch-images/switch-states-default.png "Comutadores no iOS e no Android")

O `Switch` controle define as seguintes propriedades:

- [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) é um `boolean` valor que indica se o `Switch` está **ativado**.
- [`OnColor`](xref:Xamarin.Forms.Switch.OnColor) é um `Color` que afeta como o `Switch` é renderizado no estado alternado ou **ativado**.
- `ThumbColor` é o `Color` do switch Thumb.

Essas propriedades são apoiadas por um [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objeto, o que significa que o `Switch` pode ser estilizado e ser o destino de associações de dados.

O `Switch` controle define um `Toggled` evento que é acionado quando a `IsToggled` propriedade é alterada, seja por meio da manipulação do usuário ou quando um aplicativo define a `IsToggled` propriedade. O `ToggledEventArgs` objeto que acompanha o `Toggled` evento tem uma única propriedade chamada `Value` , do tipo `bool` . Quando o evento é acionado, o valor da `Value` propriedade reflete o novo valor da `IsToggled` propriedade.

## <a name="create-a-switch"></a>Criar uma opção

Um `Switch` pode ser instanciado em XAML. Sua `IsToggled` propriedade pode ser definida para alternar o `Switch` . Por padrão, a `IsToggled` propriedade é `false` . O exemplo a seguir mostra como criar uma instância de `Switch` em XAML com o `IsToggled` conjunto de propriedades opcional:

```xaml
<Switch IsToggled="true"/>
```

Um `Switch` também pode ser criado no código:

```csharp
Switch switchControl = new Switch { IsToggled = true };
```

## <a name="switch-appearance"></a>Alternar aparência

Além das propriedades [`Switch`](xref:Xamarin.Forms.Switch) herdadas da [`View`](xref:Xamarin.Forms.View) classe, `Switch` também define `OnColor` e `ThumbColor` Propriedades. A `OnColor` propriedade pode ser definida para definir a `Switch` cor quando ela é alternada para seu estado **on** e a `ThumbColor` propriedade pode ser definida para definir o `Color` do switch Thumb. O exemplo a seguir mostra como criar uma instância de `Switch` em XAML com essas propriedades definidas:

```xaml
<Switch OnColor="Orange"
        ThumbColor="Green" />
```

As propriedades também podem ser definidas ao criar um `Switch` no código:

```csharp
Switch switch = new Switch { OnColor = Color.Orange, ThumbColor = Color.Green };
```

A captura de tela a seguir mostra o `Switch` em seus Estados de alternância **liga** e **desliga** , com as `OnColor` `ThumbColor` Propriedades e definidas:

![Captura de tela de interruptores em Estados de ligado e off, no iOS e no Android](switch-images/switch-states-colors.png "Comutadores no iOS e no Android")

## <a name="respond-to-a-switch-state-change"></a>Responder a uma alteração de estado de comutador

Quando a `IsToggled` propriedade é alterada, seja por meio da manipulação do usuário ou quando um aplicativo define a `IsToggled` propriedade, o `Toggled` evento é acionado. Um manipulador de eventos para esse evento pode ser registrado para responder à alteração:

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

O `Toggled` manipulador de eventos pode ser eliminado usando a vinculação de dados e gatilhos para responder a uma `Switch` alteração de Estados de alternância.

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

Neste exemplo, o [`Label`](xref:Xamarin.Forms.Label) usa uma expressão de associação em um `DataTrigger` para monitorar a `IsToggled` Propriedade do `Switch` nome `styleSwitch` . Quando essa propriedade se torna `true` , `FontAttributes` as `FontSize` Propriedades e de `Label` são alteradas. Quando a `IsToggled` propriedade retorna para `false` , as `FontAttributes` `FontSize` Propriedades e de `Label` são redefinidas para o estado inicial.

Para obter informações sobre gatilhos, consulte [ Xamarin.Forms gatilhos](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="switch-visual-states"></a>Alternar estados visuais

[`Switch`](xref:Xamarin.Forms.Switch) tem `On` e os `Off` Estados visuais que podem ser usados para iniciar uma alteração visual quando a [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) propriedade é alterada.

O exemplo de XAML a seguir mostra como definir estados visuais para `On` os `Off` Estados e:

```xaml
<Switch IsToggled="True">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="On">
                <VisualState.Setters>
                    <Setter Property="ThumbColor"
                            Value="MediumSpringGreen" />
                </VisualState.Setters>
            </VisualState>
            <VisualState x:Name="Off">
                <VisualState.Setters>
                    <Setter Property="ThumbColor"
                            Value="Red" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Switch>
```

Neste exemplo, o `On` [`VisualState`](xref:Xamarin.Forms.VisualState) especifica que, quando a [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) propriedade for `true` , a `ThumbColor` propriedade será definida como verde de mola média. O `Off` `VisualState` especifica que, quando a `IsToggled` propriedade for `false` , a `ThumbColor` propriedade será definida como vermelho. Portanto, o efeito geral é que, quando o `Switch` está em uma posição desligada, ele é vermelho e seu Thumb é verde de mola média quando o `Switch` está em uma posição em diante:

![Captura de tela de switch em VisualState, no Ios e no Android](switch-images/on-visualstate.png "Alternar em VisualState") 
 ![Captura de tela de switch off VisualState, no Ios e no Android](switch-images/off-visualstate.png "Desligar VisualState")

Para obter mais informações sobre os Estados visuais, consulte [ Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="disable-a-switch"></a>Desabilitar uma opção

Um aplicativo pode inserir um estado em que o `Switch` que está sendo alternado não é uma operação válida. Nesses casos, o `Switch` pode ser desabilitado definindo sua `IsEnabled` propriedade como `false` . Isso impedirá que os usuários possam manipular o `Switch` .

## <a name="related-links"></a>Links relacionados

- [Alternar demonstrações](/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)
- [Xamarin.Forms Gatilhos](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.Forms Gerenciador de estado visual](~/xamarin-forms/user-interface/visual-state-manager.md)