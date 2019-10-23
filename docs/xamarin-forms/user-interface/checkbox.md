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
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "68739153"
---
# <a name="xamarinforms-checkbox"></a>Caixa de seleção Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos/)

O `CheckBox` Xamarin. Forms é um tipo de botão que pode ser marcado ou vazio. Quando uma caixa de seleção é marcada, ela é considerada como ativada. Quando uma caixa de seleção está vazia, ela é considerada como off.

`CheckBox` define uma propriedade `bool` chamada `IsChecked`, que indica se a `CheckBox` está marcada. Essa propriedade também tem o suporte de um objeto [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , o que significa que ele pode ser estilizado e ser o destino de associações de dados.

> [!NOTE]
> A propriedade `IsChecked` vinculável tem um modo de associação padrão de [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay).

`CheckBox` define um evento de `CheckedChanged` que é disparado quando a propriedade `IsChecked` é alterada, seja pela manipulação do usuário ou quando um aplicativo define a propriedade `IsChecked`. O objeto `CheckedChangedEventArgs` que acompanha o evento `CheckedChanged` tem uma única propriedade denominada `Value`, do tipo `bool`. Quando o evento é acionado, o valor da propriedade `Value` é definido como o novo valor da propriedade `IsChecked`.

## <a name="create-a-checkbox"></a>Criar uma caixa de seleção

O exemplo a seguir mostra como criar uma instância de um `CheckBox` em XAML:

```xaml
<CheckBox />
```

Esse XAML resulta na aparência mostrada nas seguintes capturas de tela:

![Captura de tela de uma caixa de seleção vazia no iOS e no Android](checkbox-images/checkbox-empty.png "Caixa de seleção vazia")

Por padrão, o `CheckBox` está vazio. O `CheckBox` pode ser verificado pela manipulação do usuário ou definindo a propriedade `IsChecked` como `true`:

```xaml
<CheckBox IsChecked="true" />
```

Esse XAML resulta na aparência mostrada nas seguintes capturas de tela:

![Captura de tela de uma caixa de seleção marcada no iOS e no Android](checkbox-images/checkbox-checked.png "Caixa de seleção marcada")

Como alternativa, um `CheckBox` pode ser criado no código:

```csharp
CheckBox checkBox = new CheckBox { IsChecked = true };
```

## <a name="respond-to-a-checkbox-changing-state"></a>Responder a um estado de alteração de caixa de seleção

Quando a propriedade `IsChecked` é alterada, seja pela manipulação do usuário ou quando um aplicativo define a propriedade `IsChecked`, o evento `CheckedChanged` é disparado. Um manipulador de eventos para esse evento pode ser registrado para responder à alteração:

```xaml
<CheckBox CheckedChanged="OnCheckBoxCheckedChanged" />
```

O arquivo code-behind contém o manipulador para o evento `CheckedChanged`:

```csharp
void OnCheckBoxCheckedChanged(object sender, CheckedChangedEventArgs e)
{
    // Perform required operation after examining e.Value
}
```

O argumento `sender` é o `CheckBox` responsável por esse evento. Você pode usar isso para acessar o objeto `CheckBox` ou para distinguir entre vários objetos `CheckBox` que compartilham o mesmo evento `CheckedChanged`.

Como alternativa, um manipulador de eventos para o evento `CheckedChanged` pode ser registrado no código:

```csharp
CheckBox checkBox = new CheckBox { ... };
checkBox.CheckedChanged += (sender, e) =>
{
    // Perform required operation after examining e.Value
};
```

## <a name="data-bind-a-checkbox"></a>Associação de dados a uma caixa de seleção

O manipulador de eventos `CheckedChanged` pode ser eliminado usando a vinculação de dados e os gatilhos para responder a um `CheckBox` que está sendo marcado ou vazio:

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

Neste exemplo, o [`Label`](xref:Xamarin.Forms.Label) usa uma expressão de associação em um gatilho de dados para monitorar a propriedade `IsChecked` do `CheckBox`. Quando essa propriedade se torna `true`, as propriedades `FontAttributes` e `FontSize` do `Label` mudam. Quando a propriedade `IsChecked` retorna para `false`, as propriedades `FontAttributes` e `FontSize` da `Label` são redefinidas para o estado inicial.

Nas capturas de tela a seguir, a captura de tela do iOS mostra a formatação [`Label`](xref:Xamarin.Forms.Label) quando o `CheckBox` está vazio, enquanto a captura de tela do Android mostra a formatação `Label` quando o `CheckBox` está marcado:

[![Captura de tela de uma caixa de seleção de associação de dados, no iOS e no Android](checkbox-images/checkbox-databinding.png "Caixa de seleção Associação de dados")](checkbox-images/checkbox-databinding-large.png#lightbox "Caixa de seleção Associação de dados")

Para obter mais informações sobre gatilhos, consulte [gatilhos do Xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="disable-a-checkbox"></a>Desabilitar uma caixa de seleção

Às vezes, um aplicativo entra em um estado em que um `CheckBox` que está sendo verificado não é uma operação válida. Nesses casos, o `CheckBox` pode ser desabilitado definindo sua propriedade `IsEnabled` como `false`.

## <a name="checkbox-appearance"></a>Aparência da caixa de seleção

Além das propriedades que `CheckBox` herda da classe [`View`](xref:Xamarin.Forms.View) , `CheckBox` também define uma propriedade `Color` que define sua cor como uma [`Color`](xref:Xamarin.Forms.Color):

```xaml
<CheckBox Color="Red" />
```

As capturas de tela a seguir mostram uma série de objetos `CheckBox` verificados, onde cada objeto tem sua propriedade `Color` definida como uma [`Color`](xref:Xamarin.Forms.Color)diferente:

![Captura de tela de caixas de seleção coloridas em iOS e Android](checkbox-images/checkbox-colors.png "Caixa de seleção colorida")

## <a name="checkbox-visual-states"></a>Estados visuais da caixa de seleção

`CheckBox` tem um [`VisualState`](xref:Xamarin.Forms.VisualState) `IsChecked` que pode ser usado para iniciar uma alteração visual no `CheckBox` quando ele se torna marcado.

O exemplo de XAML a seguir mostra como definir um estado visual para o estado de `IsChecked`:

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

Neste exemplo, a `IsChecked` [`VisualState`](xref:Xamarin.Forms.VisualState) especifica que, quando a `CheckBox` estiver marcada, sua propriedade `Color` será definida como verde. O `Normal` `VisualState` especifica que quando o `CheckBox` estiver em um estado normal, sua propriedade `Color` será definida como vermelho. Portanto, o efeito geral é que o `CheckBox` é vermelho quando está vazio e verde quando é marcado.

Para obter mais informações sobre os Estados visuais, consulte [Gerenciador de estado visual do Xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Links relacionados

- [Demonstrações da caixa de seleção (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos/)
- [Gatilhos do Xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md)
- [Gerenciador de estado visual do Xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
