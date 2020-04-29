---
title: Botão de opção Xamarin. Forms
description: O RadioButton do Xamarin. Forms é um tipo de botão que permite aos usuários selecionar uma opção de um conjunto. Cada opção é representada por um botão de opção e você só pode selecionar um botão de opção em um grupo.
ms.prod: xamarin
ms.assetid: E2AA40E0-69A5-41DF-BFC4-C151CA657451
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/13/2020
ms.openlocfilehash: 128ab4f6f00adaf86afc08eba37bcb81d3a04a90
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82532810"
---
# <a name="xamarinforms-radiobutton"></a>Botão de opção Xamarin. Forms

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-radiobuttondemos/)

O Xamarin. Forms `RadioButton` é um tipo de botão que permite aos usuários selecionar uma opção de um conjunto. Cada opção é representada por um botão de opção e você só pode selecionar um botão de opção em um grupo. A `RadioButton` classe herda da [`Button`](xref:Xamarin.Forms.Button) classe.

As capturas de tela `RadioButton` a seguir mostram objetos em seus Estados limpos e selecionados, em Ios e Android:

![Captura de tela dos botões de opção nos Estados selecionado e limpo, no iOS e no Android](radiobutton-images/radiobutton-states.png "Botões de opção no iOS e no Android")

> [!IMPORTANT]
> `RadioButton`o é experimental e só pode ser usado com a definição `RadioButton_Experimental` do sinalizador. Para obter mais informações, consulte [sinalizadores experimentais](~/xamarin-forms/internals/experimental-flags.md).

O `RadioButton` controle define as seguintes propriedades:

- `IsChecked`, do tipo `bool`, que define se o `RadioButton` está selecionado. Essa propriedade usa uma `TwoWay` associação e tem um valor padrão de `false`.
- `GroupName`, do tipo `string`, que define o nome que especifica quais `RadioButton` controles são mutuamente exclusivos. Essa propriedade tem um valor padrão de `null`.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

O `RadioButton` controle define um `CheckedChanged` evento que é acionado quando `IsChecked` a propriedade é alterada, seja por meio de manipulação de usuário ou programática. O `CheckedChangedEventArgs` objeto que acompanha o `CheckedChanged` evento tem uma única propriedade chamada `Value`, do tipo. `bool` Quando o evento é acionado, o valor da `Value` propriedade é definido como o novo valor da `IsChecked` propriedade.

Além disso, a `RadioButton` classe herda as seguintes propriedades normalmente usadas da [`Button`](xref:Xamarin.Forms.Button) classe:

- [`Command`](xref:Xamarin.Forms.Button.Command), do tipo `ICommand`, que é executado quando o `RadioButton` é selecionado.
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter), do tipo `object`, que é o parâmetro que é passado para o `Command`.
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes), do tipo [`FontAttributes`](xref:Xamarin.Forms.FontAttributes), que determina o estilo de texto.
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily), do tipo `string`, que define a família de fontes.
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize), do tipo `double`, que define o tamanho da fonte.
- [`Text`](xref:Xamarin.Forms.Button.Text), do tipo `string`, que define o texto a ser exibido.
- [`TextColor`](xref:Xamarin.Forms.Button.TextColor), do tipo [`Color`](xref:Xamarin.Forms.Color), que define a cor do texto exibido.

Para obter mais informações sobre [`Button`](xref:Xamarin.Forms.Button) o controle, consulte o [botão Xamarin. Forms](~/xamarin-forms/user-interface/button.md).

## <a name="create-radiobuttons"></a>Criar RadioButtons

O exemplo a seguir mostra como criar `RadioButton` uma instância de objetos em XAML:

```xaml
<StackLayout>
    <Label Text="What's your favorite animal?" />
    <RadioButton Text="Cat" />
    <RadioButton Text="Dog" />
    <RadioButton Text="Elephant" />
    <RadioButton Text="Monkey"
                 IsChecked="true" />
</StackLayout>
```

Neste exemplo, `RadioButton` os objetos são agrupados implicitamente dentro do mesmo contêiner pai. Esse XAML resulta na aparência mostrada nas seguintes capturas de tela:

![Captura de tela de RadioButtons agrupados implicitamente, no iOS e no Android](radiobutton-images/radiobuttons.png "Botões de opção agrupados implicitamente no iOS e no Android")

Como alternativa, `RadioButton` os objetos podem ser criados no código:

```csharp
StackLayout stackLayout = new StackLayout
{
    Children =
    {
        new Label { Text = "What's your favorite animal?" },
        new RadioButton { Text = "Cat" },
        new RadioButton { Text = "Dog" },
        new RadioButton { Text = "Elephant" },
        new RadioButton { Text = "Monkey", IsChecked = true }
    }
};
```

## <a name="group-radiobuttons"></a>Botões de opção de grupo

Os botões de opção funcionam em grupos e há duas abordagens para agrupar botões de opção:

- Colocando-os dentro do mesmo contêiner pai. Isso é conhecido como agrupamento implícito.
- Definindo a `GroupName` Propriedade em cada botão de opção com o mesmo valor. Isso é conhecido como Agrupamento explícito.

O exemplo XAML a seguir mostra o `RadioButton` Agrupamento explícito de objetos `GroupName` definindo suas propriedades:

```xaml
<Label Text="What's your favorite color?" />
<RadioButton Text="Red"
             TextColor="Red"
             GroupName="colors" />
<RadioButton Text="Green"
             TextColor="Green"
             GroupName="colors" />
<RadioButton Text="Blue"
             TextColor="Blue"
             GroupName="colors" />
<RadioButton Text="Other"
             GroupName="colors" />
```

Neste exemplo, cada um `RadioButton` é mutuamente exclusivo porque compartilha o mesmo `GroupName` valor. Esse XAML resulta na aparência mostrada nas seguintes capturas de tela:

![Captura de tela de botões de opção explicitamente agrupados, no iOS e no Android](radiobutton-images/grouped-radiobuttons.png "Botões de opção agrupados explicitamente no iOS e no Android")

## <a name="respond-to-a-radiobutton-state-change"></a>Responder a uma alteração de estado de RadioButton

Um botão de opção tem dois estados: marcado ou desmarcado. Quando um botão de opção é selecionado, `IsChecked` sua propriedade `true`é. Quando um botão de opção é apagado `IsChecked` , sua `false`propriedade é. Um botão de opção pode ser desmarcado clicando em outro botão de opção no mesmo grupo, mas não pode ser desmarcado com um novo clique nele. No entanto, você pode desmarcar um botão de `IsChecked` opção programaticamente definindo sua propriedade como `false`.

Quando a `IsChecked` propriedade é alterada, seja por meio de manipulação de usuário `CheckedChanged` ou programática, o evento é acionado. Um manipulador de eventos para esse evento pode ser registrado para responder à alteração:

```xaml
<RadioButton Text="Red"
             TextColor="Red"
             GroupName="colors"
             CheckedChanged="OnColorsRadioButtonCheckedChanged" />
```

O code-behind contém o manipulador para o `CheckedChanged` evento:

```csharp
void OnColorsRadioButtonCheckedChanged(object sender, CheckedChangedEventArgs e)
{
    // Perform required operation
}
```

O `sender` argumento é o `RadioButton` responsável por esse evento. Você pode usar isso para acessar o `RadioButton` objeto ou para distinguir entre vários `RadioButton` objetos que compartilham o mesmo `CheckedChanged` manipulador de eventos.

Como alternativa, um manipulador de eventos para `CheckedChanged` o evento pode ser registrado no código:

```csharp
RadioButton radioButton = new RadioButton { ... };
radioButton.CheckedChanged += (sender, e) =>
{
    // Perform required operation
};
```

> [!NOTE]
> Uma abordagem alternativa para responder a uma `RadioButton` alteração de estado é definir um `ICommand` e atribuí-lo à `RadioButton.Command` propriedade. Para obter mais informações, consulte [o botão: usando a interface de comando](~/xamarin-forms/user-interface/button.md#using-the-command-interface).

## <a name="radiobutton-visual-states"></a>Estados visuais de RadioButton

`RadioButton`tem um `IsChecked` [`VisualState`](xref:Xamarin.Forms.VisualState) que pode ser usado para iniciar uma alteração visual quando um `RadioButton` é selecionado.

O exemplo de XAML a seguir mostra como definir um estado visual para `IsChecked` o estado:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <Style TargetType="RadioButton">
            <Setter Property="VisualStateManager.VisualStateGroups">
                <VisualStateGroupList>
                    <VisualStateGroup x:Name="CommonStates">
                        <VisualState x:Name="Normal">
                            <VisualState.Setters>
                                <Setter Property="TextColor"
                                        Value="Red" />
                                <Setter Property="Opacity"
                                        Value="0.5" />
                            </VisualState.Setters>
                        </VisualState>
                        <VisualState x:Name="IsChecked">
                            <VisualState.Setters>
                                <Setter Property="TextColor"
                                        Value="Green" />
                                <Setter Property="Opacity"
                                        Value="1" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateGroupList>
            </Setter>
        </Style>
    </ContentPage.Resources>
    <StackLayout>
        <Label Text="What's your favorite mode of transport?" />
        <RadioButton Text="Car"
                     CheckedChanged="OnRadioButtonCheckedChanged" />
        <RadioButton Text="Bike"
                     CheckedChanged="OnRadioButtonCheckedChanged" />
        <RadioButton Text="Train"
                     CheckedChanged="OnRadioButtonCheckedChanged" />
        <RadioButton Text="Walking"
                     CheckedChanged="OnRadioButtonCheckedChanged" />
    </StackLayout>
</ContentPage>
```

Neste exemplo, os objetos de [`Style`](xref:Xamarin.Forms.Style) destinos `RadioButton` implícitos. O `IsChecked` [`VisualState`](xref:Xamarin.Forms.VisualState) especifica que quando um `RadioButton` é selecionado, sua `TextColor` propriedade será definida como verde com um `Opacity` valor de 1. O `Normal` `VisualState` especifica que quando um `RadioButton` estiver em um estado limpo, sua `TextColor` propriedade será definida como vermelho com um `Opacity` valor de 0,5. Portanto, o efeito geral é que quando um `RadioButton` é limpo, ele é vermelho e parcialmente transparente e fica verde sem transparência quando é selecionado:

![Captura de tela da aparência do RadioButton definida pelo estado visual, no iOS e no Android](radiobutton-images/ischecked-visualstate.png "Estados visuais de RadioButton no iOS e no Android")

Para obter mais informações sobre os Estados visuais, consulte [Gerenciador de estado visual do Xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="disable-a-radiobutton"></a>Desabilitar um botão de opção

Às vezes, um aplicativo entra em um `RadioButton` estado em que uma está sendo verificada não é uma operação válida. Nesses casos, o `RadioButton` pode ser desabilitado definindo sua `IsEnabled` Propriedade como. `false`

## <a name="related-links"></a>Links relacionados

- [Demonstrações de RadioButton (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-radiobuttondemos/)
- [Botão Xamarin. Forms](~/xamarin-forms/user-interface/button.md)
- [Gerenciador de estado visual do Xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
