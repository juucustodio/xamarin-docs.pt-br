---
title: Xamarin.Forms Índices
description: O Xamarin.Forms RadioButton é um tipo de botão que permite aos usuários selecionar uma opção de um conjunto. Cada opção é representada por um botão de opção e você só pode selecionar um botão de opção em um grupo.
ms.prod: xamarin
ms.assetid: E2AA40E0-69A5-41DF-BFC4-C151CA657451
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/02/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f1f36329c36dfae3f2ca0754ecbb867520d4c530
ms.sourcegitcommit: bd8ce0ae64698246db92a6c4396983290dc54a22
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99427740"
---
# <a name="xamarinforms-radiobutton"></a>Xamarin.Forms Índices

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-radiobuttondemos/)

O Xamarin.Forms [`RadioButton`](xref:Xamarin.Forms.RadioButton) é um tipo de botão que permite aos usuários selecionar uma opção de um conjunto. Cada opção é representada por um botão de opção e você só pode selecionar um botão de opção em um grupo. Por padrão, cada um [`RadioButton`](xref:Xamarin.Forms.RadioButton) exibe texto:

![Captura de tela de botões de opção padrão](radiobutton-images/radiobuttons-default.png "Botões de opção padrão")

No entanto, em algumas plataformas, a [`RadioButton`](xref:Xamarin.Forms.RadioButton) pode exibir um [`View`](xref:Xamarin.Forms.View) e, em todas as plataformas, a aparência de cada um `RadioButton` pode ser redefinida com um [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) :

![Captura de tela de botões de opção redefinidos](radiobutton-images/radiobuttons-controltemplate.png "Botões de opção redefinidos")

O [`RadioButton`](xref:Xamarin.Forms.RadioButton) controle define as seguintes propriedades:

- `Content`, do tipo `object` , que define o `string` ou [`View`](xref:Xamarin.Forms.View) a ser exibido pelo `RadioButton` .
- `IsChecked`, do tipo `bool` , que define se o `RadioButton` está marcado. Essa propriedade usa uma `TwoWay` associação e tem um valor padrão de `false` .
- `GroupName`, do tipo `string` , que define o nome que especifica quais `RadioButton` controles são mutuamente exclusivos. Essa propriedade tem um valor padrão de `null` .
- `Value`, do tipo `object` , que define um valor exclusivo opcional associado ao `RadioButton` .
- `BorderColor`, do tipo [`Color`](xref:Xamarin.Forms.Color) , que define a cor do traço da borda.
- `BorderWidth`, do tipo `double` , que define a largura da `RadioButton` borda.
- `CharacterSpacing`, do tipo `double` , que define o espaçamento entre os caracteres de qualquer texto exibido.
- `CornerRadius`, do tipo `int` , que define o raio do canto do `RadioButton` .
- `FontAttributes`, do tipo [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) , que determina o estilo de texto.
- `FontFamily`, do tipo `string` , que define a família de fontes.
- `FontSize`, do tipo `double` , que define o tamanho da fonte.
- `TextColor`, do tipo [`Color`](xref:Xamarin.Forms.Color) , que define a cor de qualquer texto exibido.
- `TextTransform`, do tipo `TextTransform` , que define a capitalização de qualquer texto exibido.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

O [`RadioButton`](xref:Xamarin.Forms.RadioButton) controle também define um `CheckedChanged` evento que é disparado quando a `IsChecked` propriedade é alterada, seja por meio de manipulação de usuário ou programática. O `CheckedChangedEventArgs` objeto que acompanha o `CheckedChanged` evento tem uma única propriedade chamada `Value` , do tipo `bool` . Quando o evento é acionado, o valor da `CheckedChangedEventArgs.Value` propriedade é definido como o novo valor da `IsChecked` propriedade.

[`RadioButton`](xref:Xamarin.Forms.RadioButton) o agrupamento pode ser gerenciado pela `RadioButtonGroup` classe, que define as seguintes propriedades anexadas:

-   `GroupName`, do tipo `string` , que define o nome do grupo para `RadioButton` objetos em um `Layout<View>` .
- `SelectedValue`, do tipo `object` , que representa o valor do objeto marcado `RadioButton` em um `Layout<View>` grupo. Essa propriedade anexada usa uma `TwoWay` associação por padrão.

Para obter mais informações sobre a `GroupName` Propriedade anexada, consulte [agrupar botões de opção](#group-radiobuttons). Para obter mais informações sobre a `SelectedValue` Propriedade anexada, consulte [responder a alterações de estado de RadioButton](#respond-to-radiobutton-state-changes).

## <a name="create-radiobuttons"></a>Criar RadioButtons

A aparência de um [`RadioButton`](xref:Xamarin.Forms.RadioButton) é definida pelo tipo de dados atribuído à `RadioButton.Content` Propriedade:

- Quando a `RadioButton.Content` propriedade for atribuída a `string` , ela será exibida em cada plataforma, alinhada horizontalmente ao lado do círculo do botão de opção.
- Quando o `RadioButton.Content` for atribuído a [`View`](xref:Xamarin.Forms.View) , ele será exibido em plataformas com suporte (Ios, UWP), enquanto as plataformas sem suporte irão fazer fallback para uma representação de cadeia de caracteres do `View` objeto (Android). Em ambos os casos, o conteúdo é exibido horizontalmente alinhado ao lado do círculo do botão de opção.
- Quando um [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) é aplicado a um [`RadioButton`](xref:Xamarin.Forms.RadioButton) , um [`View`](xref:Xamarin.Forms.View) pode ser atribuído à `RadioButton.Content` propriedade em todas as plataformas. Para obter mais informações, consulte [redefinição da aparência do botão de opção](#redefine-radiobutton-appearance).

### <a name="display-string-based-content"></a>Exibir conteúdo baseado em cadeia de caracteres

Um [`RadioButton`](xref:Xamarin.Forms.RadioButton) exibe texto quando a `Content` propriedade é atribuída a `string` :

```xaml
<StackLayout>
    <Label Text="What's your favorite animal?" />
    <RadioButton Content="Cat" />
    <RadioButton Content="Dog" />
    <RadioButton Content="Elephant" />
    <RadioButton Content="Monkey"
                 IsChecked="true" />
</StackLayout>
```

Neste exemplo, os [`RadioButton`](xref:Xamarin.Forms.RadioButton) objetos são agrupados implicitamente dentro do mesmo contêiner pai. Esse XAML resulta na aparência mostrada nas seguintes capturas de tela:

![Captura de tela de botões de opção baseados em texto](radiobutton-images/radiobuttons-text.png "Botões de opção baseados em texto")

### <a name="display-arbitrary-content"></a>Exibir conteúdo arbitrário

No iOS e UWP, um [`RadioButton`](xref:Xamarin.Forms.RadioButton) pode exibir conteúdo arbitrário quando a `Content` propriedade é atribuída a [`View`](xref:Xamarin.Forms.View) :

```xaml
<StackLayout>
    <Label Text="What's your favorite animal?" />
    <RadioButton>
        <RadioButton.Content>
            <Image Source="cat.png" />
        </RadioButton.Content>
    </RadioButton>
    <RadioButton>
        <RadioButton.Content>
            <Image Source="dog.png" />
        </RadioButton.Content>
    </RadioButton>
    <RadioButton>
        <RadioButton.Content>
            <Image Source="elephant.png" />
        </RadioButton.Content>
    </RadioButton>
    <RadioButton>
        <RadioButton.Content>
            <Image Source="monkey.png" />
        </RadioButton.Content>
    </RadioButton>
</StackLayout>
```

Neste exemplo, os [`RadioButton`](xref:Xamarin.Forms.RadioButton) objetos são agrupados implicitamente dentro do mesmo contêiner pai. Esse XAML resulta na aparência mostrada nas seguintes capturas de tela:

![Captura de tela de botões de opção baseados em exibição](radiobutton-images/radiobuttons-view.png "Botões de opção baseados em exibição")

No Android, os [`RadioButton`](xref:Xamarin.Forms.RadioButton) objetos exibirão uma representação baseada em cadeia de caracteres do [`View`](xref:Xamarin.Forms.View) objeto que foi definido como conteúdo:

![Captura de tela do botão de opção baseado em exibição no Android](radiobutton-images/radiobutton-view-android.png "Botão de opção baseado em exibição no Android")

> [!NOTE]
> Quando um [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) é aplicado a um [`RadioButton`](xref:Xamarin.Forms.RadioButton) , um [`View`](xref:Xamarin.Forms.View) pode ser atribuído à `RadioButton.Content` propriedade em todas as plataformas. Para obter mais informações, consulte [redefinição da aparência do botão de opção](#redefine-radiobutton-appearance).

## <a name="associate-values-with-radiobuttons"></a>Associar valores a RadioButtons

Cada [`RadioButton`](xref:Xamarin.Forms.RadioButton) objeto tem uma `Value` propriedade, do tipo `object` , que define um valor exclusivo opcional para associar ao botão de opção. Isso permite que o valor de a `RadioButton` seja diferente para seu conteúdo e é particularmente útil quando os `RadioButton` objetos estão exibindo [`View`](xref:Xamarin.Forms.View) objetos.

O XAML a seguir mostra a configuração das `Content` `Value` Propriedades e em cada [`RadioButton`](xref:Xamarin.Forms.RadioButton) objeto:

```xaml
<StackLayout>
    <Label Text="What's your favorite animal?" />
    <RadioButton Value="Cat">
        <RadioButton.Content>
            <Image Source="cat.png" />
        </RadioButton.Content>
    </RadioButton>
    <RadioButton Value="Dog">
        <RadioButton.Content>
            <Image Source="dog.png" />
        </RadioButton.Content>
    </RadioButton>
    <RadioButton Value="Elephant">
        <RadioButton.Content>
            <Image Source="elephant.png" />
        </RadioButton.Content>
    </RadioButton>
    <RadioButton Value="Monkey">
        <RadioButton.Content>
            <Image Source="monkey.png" />
        </RadioButton.Content>
    </RadioButton>
</StackLayout>
```

Neste exemplo, cada [`RadioButton`](xref:Xamarin.Forms.RadioButton) um tem um [`Image`](xref:Xamarin.Forms.Image) como seu conteúdo, enquanto também define um valor baseado em cadeia de caracteres. Isso permite que o valor do botão de opção selecionado seja facilmente identificado.

## <a name="group-radiobuttons"></a>Botões de opção de grupo

Os botões de opção funcionam em grupos, e há três abordagens para agrupar botões de opção:

- Coloque-os dentro do mesmo contêiner pai. Isso é conhecido como agrupamento *implícito* .
- Defina a `GroupName` propriedade em cada botão de opção no grupo para o mesmo valor. Isso é conhecido como agrupamento *explícito* .
- Defina a `RadioButtonGroup.GroupName` Propriedade anexada em um contêiner pai que, por sua vez, define a `GroupName` propriedade de todos os [`RadioButton`](xref:Xamarin.Forms.RadioButton) objetos no contêiner. Isso também é conhecido como agrupamento *explícito* .

> [!IMPORTANT]
> [`RadioButton`](xref:Xamarin.Forms.RadioButton) os objetos não precisam pertencer ao mesmo pai a ser agrupado. Eles são mutuamente exclusivos, desde que compartilhem um nome de grupo.

### <a name="explicit-grouping-with-the-groupname-property"></a>Agrupamento explícito com a propriedade GroupName

O exemplo XAML a seguir mostra o Agrupamento explícito de [`RadioButton`](xref:Xamarin.Forms.RadioButton) objetos definindo suas `GroupName` Propriedades:

```xaml
<Label Text="What's your favorite color?" />
<RadioButton Content="Red"
             GroupName="colors" />
<RadioButton Content="Green"
             GroupName="colors" />
<RadioButton Content="Blue"
             GroupName="colors" />
<RadioButton Content="Other"
             GroupName="colors" />
```

Neste exemplo, cada um [`RadioButton`](xref:Xamarin.Forms.RadioButton) é mutuamente exclusivo porque compartilha o mesmo `GroupName` valor.

### <a name="explicit-grouping-with-the-radiobuttongroupgroupname-attached-property"></a>Agrupamento explícito com a propriedade anexada do grupo de botões. GroupName

A `RadioButtonGroup` classe define uma `GroupName` Propriedade anexada, do tipo `string` , que pode ser definida em um `Layout<View>` objeto. Isso permite que qualquer layout seja transformado em um grupo de botões de opção:

```xaml
<StackLayout RadioButtonGroup.GroupName="colors">
    <Label Text="What's your favorite color?" />
    <RadioButton Content="Red" />
    <RadioButton Content="Green" />
    <RadioButton Content="Blue" />
    <RadioButton Content="Other" />
</StackLayout>
```

Neste exemplo, cada um [`RadioButton`](xref:Xamarin.Forms.RadioButton) em [`StackLayout`](xref:Xamarin.Forms.StackLayout) terá sua `GroupName` propriedade definida como `fruits` e será mutuamente exclusivo.

> [!NOTE]
> Quando um `Layout<View` objeto> que define a `RadioButtonGroup.GroupName` Propriedade anexada contiver um [`RadioButton`](xref:Xamarin.Forms.RadioButton) que define sua `GroupName` propriedade, o valor da `RadioButton.GroupName` Propriedade terá precedência.

## <a name="respond-to-radiobutton-state-changes"></a>Responder às alterações de estado do botão de opção

Um botão de opção tem dois estados: marcado ou desmarcado. Quando um botão de opção está marcado, sua `IsChecked` propriedade é `true` . Quando um botão de opção é desmarcado, sua `IsChecked` propriedade é `false` . Um botão de opção pode ser limpo tocando outro botão de opção no mesmo grupo, mas não pode ser apagado tocando novamente. No entanto, você pode desmarcar um botão de opção programaticamente, configurando a propriedade `IsChecked` dele como `false`.

### <a name="respond-to-an-event-firing"></a>Responder a um acionamento de eventos

Quando a `IsChecked` propriedade é alterada, seja por meio de manipulação de usuário ou programática, o `CheckedChanged` evento é acionado. Um manipulador de eventos para esse evento pode ser registrado para responder à alteração:

```xaml
<RadioButton Content="Red"
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

O `sender` argumento é o [`RadioButton`](xref:Xamarin.Forms.RadioButton) responsável por esse evento. Você pode usar isso para acessar o [`RadioButton`](xref:Xamarin.Forms.RadioButton) objeto ou para distinguir entre vários `RadioButton` objetos que compartilham o mesmo `CheckedChanged` manipulador de eventos.

### <a name="respond-to-a-property-change"></a>Responder a uma alteração de propriedade

A `RadioButtonGroup` classe define uma `SelectedValue` Propriedade anexada, do tipo `object` , que pode ser definida em um `Layout<View>` objeto. Essa propriedade anexada representa o valor do marcado [`RadioButton`](xref:Xamarin.Forms.RadioButton) dentro de um grupo definido em um layout.

Quando a `IsChecked` propriedade é alterada, seja por meio de manipulação de usuário ou programática, a `RadioButtonGroup.SelectedValue` Propriedade anexada também é alterada. Portanto, a `RadioButtonGroup.SelectedValue` Propriedade anexada pode ser vinculada a dados a uma propriedade que armazena a seleção do usuário:

```xaml
<StackLayout RadioButtonGroup.GroupName="{Binding GroupName}"
             RadioButtonGroup.SelectedValue="{Binding Selection}">
    <Label Text="What's your favorite animal?" />
    <RadioButton Content="Cat"
                 Value="Cat" />
    <RadioButton Content="Dog"
                 Value="Dog" />
    <RadioButton Content="Elephant"
                 Value="Elephant" />
    <RadioButton Content="Monkey"
                 Value="Monkey"/>
    <Label x:Name="animalLabel">
        <Label.FormattedText>
            <FormattedString>
                <Span Text="You have chosen:" />
                <Span Text="{Binding Selection}" />
            </FormattedString>
        </Label.FormattedText>
    </Label>
</StackLayout>
```

Neste exemplo, o valor da `RadioButtonGroup.GroupName` Propriedade anexada é definido pela `GroupName` propriedade no contexto de associação. Da mesma forma, o valor da `RadioButtonGroup.SelectedValue` Propriedade anexada é definido pela `Selection` propriedade no contexto de associação. Além disso, a `Selection` propriedade é atualizada para a `Value` propriedade de Checked [`RadioButton`](xref:Xamarin.Forms.RadioButton) .

## <a name="radiobutton-visual-states"></a>Estados visuais de RadioButton

[`RadioButton`](xref:Xamarin.Forms.RadioButton) os objetos têm `Checked` e os `Unchecked` Estados visuais que podem ser usados para iniciar uma alteração visual quando um `RadioButton` está marcado ou desmarcado.

O exemplo de XAML a seguir mostra como definir um estado visual para `Checked` os `Unchecked` Estados e:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <Style TargetType="RadioButton">
            <Setter Property="VisualStateManager.VisualStateGroups">
                <VisualStateGroupList>
                    <VisualStateGroup x:Name="CheckedStates">
                        <VisualState x:Name="Checked">
                            <VisualState.Setters>
                                <Setter Property="TextColor"
                                        Value="Green" />
                                <Setter Property="Opacity"
                                        Value="1" />
                            </VisualState.Setters>
                        </VisualState>
                        <VisualState x:Name="Unchecked">
                            <VisualState.Setters>
                                <Setter Property="TextColor"
                                        Value="Red" />
                                <Setter Property="Opacity"
                                        Value="0.5" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateGroupList>
            </Setter>
        </Style>
    </ContentPage.Resources>
    <StackLayout>
        <Label Text="What's your favorite mode of transport?" />
        <RadioButton Content="Car" />
        <RadioButton Content="Bike" />
        <RadioButton Content="Train" />
        <RadioButton Content="Walking" />
    </StackLayout>
</ContentPage>
```

Neste exemplo, os objetos de [`Style`](xref:Xamarin.Forms.Style) destinos implícitos [`RadioButton`](xref:Xamarin.Forms.RadioButton) . O `Checked` [`VisualState`](xref:Xamarin.Forms.VisualState) especifica que, quando um `RadioButton` estiver marcado, sua `TextColor` propriedade será definida como verde com um `Opacity` valor de 1. O `Unchecked` `VisualState` especifica que quando um `RadioButton` estiver em um estado desmarcado, sua `TextColor` propriedade será definida como vermelho com um `Opacity` valor de 0,5. Portanto, o efeito geral é que quando um `RadioButton` está desmarcado, ele é vermelho e parcialmente transparente e fica verde sem transparência quando é marcado:

![Captura de tela dos Estados visuais de RadioButton](radiobutton-images/radiobuttons-visualstates.png "Estados visuais de RadioButton")

Para obter mais informações sobre os Estados visuais, consulte [ Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="redefine-radiobutton-appearance"></a>Redefinir a aparência do botão de opção

Por padrão, os [`RadioButton`](xref:Xamarin.Forms.RadioButton) objetos usam renderizadores de plataforma para utilizar controles nativos em plataformas com suporte. No entanto, a `RadioButton` estrutura visual pode ser redefinida com um [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) , para que `RadioButton` os objetos tenham uma aparência idêntica em todas as plataformas. Isso é possível porque a [`RadioButton`](xref:Xamarin.Forms.RadioButton) classe herda da [`TemplatedView`](xref:Xamarin.Forms.TemplatedView) classe.

O XAML a seguir mostra um [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) que pode ser usado para redefinir a estrutura visual de [`RadioButton`](xref:Xamarin.Forms.RadioButton) objetos:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <ControlTemplate x:Key="RadioButtonTemplate">
            <Frame BorderColor="#F3F2F1"
                   BackgroundColor="#F3F2F1"
                   HasShadow="False"
                   HeightRequest="100"
                   WidthRequest="100"
                   HorizontalOptions="Start"
                   VerticalOptions="Start"
                   Padding="0">
                <VisualStateManager.VisualStateGroups>
                    <VisualStateGroupList>
                        <VisualStateGroup x:Name="CheckedStates">
                            <VisualState x:Name="Checked">
                                <VisualState.Setters>
                                    <Setter Property="BorderColor"
                                            Value="#FF3300" />
                                    <Setter TargetName="check"
                                            Property="Opacity"
                                            Value="1" />
                                </VisualState.Setters>
                            </VisualState>
                            <VisualState x:Name="Unchecked">
                                <VisualState.Setters>
                                    <Setter Property="BackgroundColor"
                                            Value="#F3F2F1" />
                                    <Setter Property="BorderColor"
                                            Value="#F3F2F1" />
                                    <Setter TargetName="check"
                                            Property="Opacity"
                                            Value="0" />
                                </VisualState.Setters>
                            </VisualState>
                        </VisualStateGroup>
                    </VisualStateGroupList>
                </VisualStateManager.VisualStateGroups>
                <Grid Margin="4"
                      WidthRequest="100">
                    <Grid WidthRequest="18"
                          HeightRequest="18"
                          HorizontalOptions="End"
                          VerticalOptions="Start">
                        <Ellipse Stroke="Blue"
                                 Fill="White"
                                 WidthRequest="16"
                                 HeightRequest="16"
                                 HorizontalOptions="Center"
                                 VerticalOptions="Center" />
                        <Ellipse x:Name="check"
                                 Fill="Blue"
                                 WidthRequest="8"
                                 HeightRequest="8"
                                 HorizontalOptions="Center"
                                 VerticalOptions="Center" />
                    </Grid>
                    <ContentPresenter />
                </Grid>
            </Frame>
        </ControlTemplate>

        <Style TargetType="RadioButton">
            <Setter Property="ControlTemplate"
                    Value="{StaticResource RadioButtonTemplate}" />
        </Style>
    </ContentPage.Resources>
    <!-- Page content -->
</ContentPage>
```

Neste exemplo, o elemento raiz do [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) é um [`Frame`](xref:Xamarin.Forms.Frame) objeto que define e os `Checked` `Unchecked` Estados visuais. O `Frame` objeto usa uma combinação de [`Grid`](xref:Xamarin.Forms.Grid) [`Ellipse`](xref:Xamarin.Forms.Shapes.Ellipse) objetos, e [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) para definir a estrutura visual de um [`RadioButton`](xref:Xamarin.Forms.RadioButton) . O exemplo também inclui um estilo *implícito* que atribuirá o `RadioButtonTemplate` à `ControlTemplate` propriedade de qualquer `RadioButton` objeto na página.

> [!NOTE]
> O [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) objeto marca o local na estrutura visual onde o [`RadioButton`](xref:Xamarin.Forms.RadioButton) conteúdo será exibido.

O XAML a seguir mostra os [`RadioButton`](xref:Xamarin.Forms.RadioButton) objetos que consomem o [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) por meio do estilo *implícito* :

```xaml
<StackLayout>
    <Label Text="What's your favorite animal?" />
    <StackLayout RadioButtonGroup.GroupName="animals"
                 Orientation="Horizontal">
        <RadioButton Value="Cat">
            <RadioButton.Content>
                <StackLayout>
                    <Image Source="cat.png"
                           HorizontalOptions="Center"
                           VerticalOptions="CenterAndExpand" />
                    <Label Text="Cat"
                           HorizontalOptions="Center"
                           VerticalOptions="End" />
                </StackLayout>
            </RadioButton.Content>
        </RadioButton>
        <RadioButton Value="Dog">
            <RadioButton.Content>
                <StackLayout>
                    <Image Source="dog.png"
                           HorizontalOptions="Center"
                           VerticalOptions="CenterAndExpand" />
                    <Label Text="Dog"
                           HorizontalOptions="Center"
                           VerticalOptions="End" />
                </StackLayout>
            </RadioButton.Content>
        </RadioButton>
        <RadioButton Value="Elephant">
            <RadioButton.Content>
                <StackLayout>
                    <Image Source="elephant.png"
                           HorizontalOptions="Center"
                           VerticalOptions="CenterAndExpand" />
                    <Label Text="Elephant"
                           HorizontalOptions="Center"
                           VerticalOptions="End" />
                </StackLayout>
            </RadioButton.Content>
        </RadioButton>
        <RadioButton Value="Monkey">
            <RadioButton.Content>
                <StackLayout>
                    <Image Source="monkey.png"
                           HorizontalOptions="Center"
                           VerticalOptions="CenterAndExpand" />
                    <Label Text="Monkey"
                           HorizontalOptions="Center"
                           VerticalOptions="End" />
                </StackLayout>
            </RadioButton.Content>
        </RadioButton>
    </StackLayout>
</StackLayout>
```

Neste exemplo, a estrutura visual definida para cada [`RadioButton`](xref:Xamarin.Forms.RadioButton) é substituída pela estrutura visual definida no [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) e, portanto, em tempo de execução, os objetos no `ControlTemplate` tornam-se parte da árvore visual para cada um `RadioButton` . Além disso, o conteúdo de cada um `RadioButton` é substituído [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) pelo definido no modelo de controle. Isso resulta na seguinte `RadioButton` aparência:

![Captura de tela de botões de opção de modelo](radiobutton-images/radiobuttons-templated.png "Botões de opção de modelo")

Para obter mais informações sobre modelos de controle, consulte [ Xamarin.Forms modelos de controle](~/xamarin-forms/app-fundamentals/templates/control-template.md).

## <a name="disable-a-radiobutton"></a>Desabilitar um botão de opção

Às vezes, um aplicativo entra em um estado em que uma está `RadioButton` sendo verificada não é uma operação válida. Nesses casos, o `RadioButton` pode ser desabilitado definindo sua `IsEnabled` propriedade como `false` .

## <a name="related-links"></a>Links relacionados

- [Demonstrações de RadioButton (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-radiobuttondemos/)
- [Xamarin.Forms Button](~/xamarin-forms/user-interface/button.md)
- [Xamarin.Forms Gerenciador de estado visual](~/xamarin-forms/user-interface/visual-state-manager.md)
