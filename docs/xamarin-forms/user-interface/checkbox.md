---
title: Caixa de seleção Xamarin. Forms
description: A caixa de seleção Xamarin. Forms é um tipo de botão que pode ser marcado ou vazio. Quando uma caixa de seleção é marcada, ela é considerada como ativada. Quando uma caixa de seleção está vazia, ela é considerada como off.
ms.prod: xamarin
ms.assetid: B8B9268B-BCB8-42B9-B08C-C0F22C137238
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/11/2019
ms.openlocfilehash: f78ca9d2cf7a9e57b81c5d923c64b36a7982c4b0
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739153"
---
# <a name="xamarinforms-checkbox"></a>Caixa de seleção Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos/)

O Xamarin. Forms `CheckBox` é um tipo de botão que pode ser marcado ou vazio. Quando uma caixa de seleção é marcada, ela é considerada como ativada. Quando uma caixa de seleção está vazia, ela é considerada como off.

`CheckBox`define uma `bool` propriedade chamada `IsChecked`, que indica se o `CheckBox` está marcado. Essa propriedade também é apoiada por um [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objeto, o que significa que ele pode ser estilizado e ser o destino de associações de dados.

> [!NOTE]
> A `IsChecked` propriedade vinculável tem um modo de associação [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay)padrão de.

`CheckBox`define um `CheckedChanged` evento que é disparado `IsChecked` quando a propriedade é alterada, seja por meio da manipulação do usuário `IsChecked` ou quando um aplicativo define a propriedade. O `CheckedChangedEventArgs` objeto que acompanha o `CheckedChanged` evento tem uma única propriedade chamada `Value`, do tipo `bool`. Quando o evento é acionado, o valor da `Value` propriedade é definido como o novo valor `IsChecked` da propriedade.

## <a name="create-a-checkbox"></a>Criar uma caixa de seleção

O exemplo a seguir mostra como instanciar um `CheckBox` no XAML:

```xaml
<CheckBox />
```

Esse XAML resulta na aparência mostrada nas seguintes capturas de tela:

![Captura de tela de uma caixa de seleção vazia no Ios e no Android](checkbox-images/checkbox-empty.png "Caixa de seleção vazia")

Por padrão, o `CheckBox` está vazio. O `CheckBox` pode ser verificado pela manipulação do usuário ou definindo a `IsChecked` Propriedade como `true`:

```xaml
<CheckBox IsChecked="true" />
```

Esse XAML resulta na aparência mostrada nas seguintes capturas de tela:

![Captura de tela de uma caixa de seleção marcada no Ios e no Android](checkbox-images/checkbox-checked.png "Caixa de seleção marcada")

Como alternativa, um `CheckBox` pode ser criado no código:

```csharp
CheckBox checkBox = new CheckBox { IsChecked = true };
```

## <a name="respond-to-a-checkbox-changing-state"></a>Responder a um estado de alteração de caixa de seleção

Quando a `IsChecked` propriedade é alterada, seja por meio da manipulação do usuário ou quando `IsChecked` um aplicativo define `CheckedChanged` a propriedade, o evento é acionado. Um manipulador de eventos para esse evento pode ser registrado para responder à alteração:

```xaml
<CheckBox CheckedChanged="OnCheckBoxCheckedChanged" />
```

O arquivo code-behind contém o manipulador para o `CheckedChanged` evento:

```csharp
void OnCheckBoxCheckedChanged(object sender, CheckedChangedEventArgs e)
{
    // Perform required operation after examining e.Value
}
```

O `sender` argumento é o `CheckBox` responsável por esse evento. Você pode usar isso para acessar o `CheckBox` objeto, ou para distinguir entre vários `CheckBox` objetos que compartilham o mesmo `CheckedChanged` eventos.

Como alternativa, um manipulador de eventos para `CheckedChanged` o evento pode ser registrado no código:

```csharp
CheckBox checkBox = new CheckBox { ... };
checkBox.CheckedChanged += (sender, e) =>
{
    // Perform required operation after examining e.Value
};
```

## <a name="data-bind-a-checkbox"></a>Associação de dados a uma caixa de seleção

O `CheckedChanged` manipulador de eventos pode ser eliminado usando a vinculação de dados e os gatilhos para responder a um `CheckBox` sendo marcado ou vazio:

```xaml
<CheckBox x:Name="checkBox" />
<Label Text="Lorem ipsum dolor sit amet, elit rutrum, enim hendrerit augue vitae praesent sed non, lorem aenean quis praesent pede.">
    <Label.Triggers>
        <DataTrigger TargetType="Label"
                     Binding="{Binding Source={x:Reference checkBox}, Path=IsChecked}"
                     Value="true">
            <Setter Property="FontAttributes"
                    Value="Italic, Bold" />
            <Setter Property="FontSize"
                    Value="Large" />
        </DataTrigger>
    </Label.Triggers>
</Label>
```

Neste exemplo, o [`Label`](xref:Xamarin.Forms.Label) usa uma expressão de associação em um gatilho de dados para monitorar a `IsChecked` Propriedade do `CheckBox`. Quando essa propriedade se `true`torna, `FontAttributes` as `FontSize` Propriedades e da `Label` alteração. Quando a `IsChecked` propriedade retorna para `false`, `FontAttributes` aspropriedades`FontSize` e de sãoredefinidasparaoestadoinicial.`Label`

Nas capturas de tela a seguir, a captura de [`Label`](xref:Xamarin.Forms.Label) tela do IOS `CheckBox` mostra a formatação quando o está vazio, enquanto `Label` a captura de `CheckBox` tela do Android mostra a formatação quando o está marcado:

[ ![Captura de tela de uma caixa de seleção de associação de dados, na](checkbox-images/checkbox-databinding.png "caixa de seleção Associação de dados") Ios e Android] (checkbox-images/checkbox-databinding-large.png#lightbox "Caixa de seleção Associação de dados")

Para obter mais informações sobre gatilhos, consulte gatilhos do [Xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="disable-a-checkbox"></a>Desabilitar uma caixa de seleção

Às vezes, um aplicativo entra em um `CheckBox` estado em que uma está sendo verificada não é uma operação válida. Nesses casos, o `CheckBox` pode ser desabilitado definindo sua `IsEnabled` Propriedade como `false`.

## <a name="checkbox-appearance"></a>Aparência da caixa de seleção

Além das propriedades que `CheckBox` herdam [`View`](xref:Xamarin.Forms.View) da classe, `CheckBox` também definem [`Color`](xref:Xamarin.Forms.Color)uma `Color` propriedade que define sua cor como:

```xaml
<CheckBox Color="Red" />
```

As capturas de tela a seguir mostram uma `CheckBox` série de objetos verificados, onde `Color` cada objeto tem sua propriedade [`Color`](xref:Xamarin.Forms.Color)definida como um diferente:

![Captura de tela de caixas de seleção coloridas em Ios e Android](checkbox-images/checkbox-colors.png "Caixa de seleção colorida")

## <a name="checkbox-visual-states"></a>Estados visuais da caixa de seleção

`CheckBox`tem um `IsChecked` [`VisualState`](xref:Xamarin.Forms.VisualState) que pode ser usado para iniciar `CheckBox` uma alteração visual no quando ele se torna marcado.

O exemplo XAML a seguir mostra como definir um estado visual para o `IsChecked` estado:

```xaml
<CheckBox ...>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="Color"
                            Value="Red" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="IsChecked">
                <VisualState.Setters>
                    <Setter Property="Color"
                            Value="Green" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</CheckBox>
```

`IsChecked` Neste exemplo, o [`VisualState`](xref:Xamarin.Forms.VisualState) especifica que quando o `CheckBox` estiver marcado, sua `Color` propriedade será definida como verde. O `Normal` `CheckBox` especifica que quando o estiver em um estado normal, sua `Color` propriedade será definida como vermelho. `VisualState` Portanto, o efeito geral é que o `CheckBox` é vermelho quando está vazio e verde quando é marcado.

Para obter mais informações sobre os Estados visuais, consulte [Gerenciador de estado visual do Xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Links relacionados

- [Demonstrações da caixa de seleção (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos/)
- [Gatilhos do Xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md)
- [Gerenciador de estado visual do Xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
