---
title: Caixa de seleção do xamarin. Forms
description: A caixa de seleção do xamarin. Forms é um tipo de botão que pode ser feito check ou vazio. Quando uma caixa de seleção estiver marcada, ela é considerada em. Quando uma caixa de seleção estiver vazia, ele é considerado estar desligado.
ms.prod: xamarin
ms.assetid: B8B9268B-BCB8-42B9-B08C-C0F22C137238
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/11/2019
ms.openlocfilehash: 42631b1b67dc1d342e9f8666916604e68ee158d8
ms.sourcegitcommit: 0fd04ea3af7d6a6d6086525306523a5296eec0df
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67517915"
---
# <a name="xamarinforms-checkbox"></a>Caixa de seleção do xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CheckBoxDemos)

O xamarin. Forms `CheckBox` é um tipo de botão que pode ser verificado ou está vazio. Quando uma caixa de seleção estiver marcada, ela é considerada em. Quando uma caixa de seleção estiver vazia, ele é considerado estar desligado.

`CheckBox` define uma `bool` propriedade nomeada `IsChecked`, que indica se o `CheckBox` é verificada. Essa propriedade também é feita por um [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objeto, o que significa que podem ser estilizado e ser o destino de vinculações de dados.

> [!NOTE]
> O `IsChecked` propriedade associável tem um modo de associação padrão de [ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay).

`CheckBox` define uma `CheckedChanged` evento acionado quando o `IsChecked` alterações de propriedade, por meio de manipulação de usuário ou quando um aplicativo define o `IsChecked` propriedade. O `CheckedChangedEventArgs` objeto que acompanha o `CheckedChanged` evento tem uma propriedade única chamada `Value`, do tipo `bool`. Quando o evento é acionado, o valor da `Value` estiver definida como o novo valor da `IsChecked` propriedade.

## <a name="create-a-checkbox"></a>Criar uma caixa de seleção

O exemplo a seguir mostra como instanciar um `CheckBox` no XAML:

```xaml
<CheckBox />
```

Esse XAML resulta na exibição mostrada nas capturas de tela seguir:

![Captura de tela de uma caixa de seleção vazia no iOS e Android](checkbox-images/checkbox-empty.png "caixa de seleção vazia")

Por padrão, o `CheckBox` está vazio. O `CheckBox` pode ser verificada pela manipulação do usuário, ou definindo o `IsChecked` propriedade `true`:

```xaml
<CheckBox IsChecked="true" />
```

Esse XAML resulta na exibição mostrada nas capturas de tela seguir:

![Captura de tela da caixa de seleção marcada, no iOS e Android](checkbox-images/checkbox-checked.png "caixa de seleção marcada")

Como alternativa, um `CheckBox` podem ser criados no código:

```csharp
CheckBox checkBox = new CheckBox { IsChecked = true };
```

## <a name="respond-to-a-checkbox-changing-state"></a>Responder a uma caixa de seleção alteração de estado

Quando o `IsChecked` alterações de propriedade, por meio de manipulação de usuário ou quando um aplicativo define o `IsChecked` propriedade, o `CheckedChanged` evento é acionado. Um manipulador de eventos para esse evento pode ser registrado para responder à alteração:

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

Como alternativa, um manipulador de eventos para o `CheckedChanged` evento pode ser registrado no código:

```csharp
CheckBox checkBox = new CheckBox { ... };
checkBox.CheckedChanged += (sender, e) =>
{
    // Perform required operation after examining e.Value
};
```

## <a name="data-bind-a-checkbox"></a>Associar dados de uma caixa de seleção

O `CheckedChanged` manipulador de eventos pode ser eliminado usando vinculação de dados e gatilhos para responder a um `CheckBox` que está sendo verificado ou está vazio:

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

Neste exemplo, o [ `Label` ](xref:Xamarin.Forms.Label) usa uma expressão de associação em um gatilho de dados para monitorar os `IsChecked` propriedade do `CheckBox`. Quando essa propriedade se torna `true`, o `FontAttributes` e `FontSize` propriedades do `Label` alterar. Quando o `IsChecked` propriedade retorna à `false`, o `FontAttributes` e `FontSize` propriedades do `Label` serão redefinidos para seu estado inicial.

Em capturas de tela as seguir mostra a captura de tela do iOS a [ `Label` ](xref:Xamarin.Forms.Label) formatação quando o `CheckBox` estiver vazio, enquanto mostra a captura de tela Android a `Label` formatação quando o `CheckBox` é verificado:

[![Captura de tela de um dado associado a caixa de seleção no iOS e Android](checkbox-images/checkbox-databinding.png "dados associados a caixa de seleção")](checkbox-images/checkbox-databinding-large.png#lightbox "dados associados a caixa de seleção")

Para obter mais informações sobre gatilhos, consulte [xamarin. Forms gatilhos](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="disable-a-checkbox"></a>Desabilitar uma caixa de seleção

Às vezes, um aplicativo entra em um estado em que um `CheckBox` que está sendo verificado não é uma operação válida. Nesses casos, o `CheckBox` pode ser desabilitado definindo seu `IsEnabled` propriedade `false`.

## <a name="checkbox-appearance"></a>Aparência da caixa de seleção

Além das propriedades que `CheckBox` herda a [ `View` ](xref:Xamarin.Forms.View) classe, `CheckBox` também define uma `Color` propriedade que define sua cor como uma [ `Color` ](xref:Xamarin.Forms.Color):

```xaml
<CheckBox Color="Red" />
```

As capturas de tela a seguir mostram uma série de marcado `CheckBox` objetos, em que cada objeto tem seu `Color` propriedade definida como uma opção diferente [ `Color` ](xref:Xamarin.Forms.Color):

![Captura de tela de caixas de seleção coloridas, no iOS e Android](checkbox-images/checkbox-colors.png "caixa de seleção colorida")

## <a name="checkbox-visual-states"></a>Caixa de seleção estados visuais

`CheckBox` tem um `IsChecked` [ `VisualState` ](xref:Xamarin.Forms.VisualState) que pode ser usado para iniciar uma alteração visual para o `CheckBox` quando ele fica marcado.

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

Neste exemplo, o `IsChecked` [ `VisualState` ](xref:Xamarin.Forms.VisualState) Especifica que, quando o `CheckBox` estiver marcada, seu `Color` propriedade será definida como verde. O `Normal` `VisualState` Especifica que, quando o `CheckBox` está em um estado normal, seu `Color` propriedade será definida como vermelho. Portanto, o efeito geral é que o `CheckBox` seja vermelho quando ela estiver vazia e verde quando é verificado.

Para obter mais informações sobre estados visuais, consulte [Gerenciador de estado Visual do xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Links relacionados

- [Demonstrações de caixa de seleção (amostra)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CheckBoxDemos)
- [Gatilhos do xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin. Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md)
