---
title: O Gerenciador de estado visual do Xamarin. Forms
description: Use o Gerenciador de estado visual para fazer alterações em elementos XAML com base em estados visuais definidos a partir do código.
ms.prod: xamarin
ms.assetid: 17296F14-640D-484B-A24C-A4E9B7013E4F
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 228501172ede71204c64e1efe1673ce92be424ea
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "68656050"
---
# <a name="the-xamarinforms-visual-state-manager"></a>O Gerenciador de estado visual do Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)

_Use o Gerenciador de estado visual para fazer alterações em elementos XAML com base em estados visuais definidos a partir do código._

O VSM (Visual State Manager) é novo no Xamarin. Forms 3,0. O VSM fornece uma maneira estruturada de fazer alterações visuais na interface do usuário a partir do código. Na maioria dos casos, a interface do usuário do aplicativo é definida em XAML, e esse XAML inclui marcação que descreve como o Gerenciador de estado visual afeta os elementos visuais da interface do usuário.

O VSM apresenta o conceito de _Estados visuais_. Uma exibição do Xamarin. Forms, como uma `Button` pode ter várias aparências visuais diferentes dependendo de seu estado subjacente &mdash; se ela está desabilitada ou pressionada ou se tem foco de entrada. Esses são os Estados do botão.

Os Estados visuais são coletados em _grupos de Estados visuais_. Todos os Estados visuais dentro de um grupo de estado visual são mutuamente exclusivos. Os Estados visuais e os grupos de Estados visuais são identificados por cadeias de caracteres de texto simples.

O Gerenciador de estado visual do Xamarin. Forms define um grupo de estado visual chamado "CommonStates" com três estados visuais:

- "Normal"
- Desabilitado
- Foco

Esse grupo de estado visual tem suporte para todas as classes que derivam de [`VisualElement`](xref:Xamarin.Forms.VisualElement), que é a classe base para [`View`](xref:Xamarin.Forms.View) e [`Page`](xref:Xamarin.Forms.Page). 

Você também pode definir seus próprios grupos de estado visual e Estados visuais, como este artigo demonstrará.

> [!NOTE]
> Os desenvolvedores do Xamarin. Forms familiarizados com [gatilhos](~/xamarin-forms/app-fundamentals/triggers.md) estão cientes de que os gatilhos também podem fazer alterações em visuais na interface do usuário com base nas alterações nas propriedades de uma exibição ou no acionamento de eventos. No entanto, o uso de gatilhos para lidar com várias combinações dessas alterações pode se tornar bastante confuso. Historicamente, o Visual State Manager foi introduzido em ambientes baseados em XAML do Windows para aliviar a confusão resultante de combinações de Estados visuais. Com o VSM, os Estados visuais dentro de um grupo de estado visual são sempre mutuamente exclusivos. A qualquer momento, apenas um estado em cada grupo é o estado atual.

## <a name="the-common-states"></a>Os Estados comuns

O Gerenciador de estado visual permite que você inclua seções no arquivo XAML que podem alterar a aparência visual de uma exibição se a exibição for normal ou desabilitada ou tiver o foco de entrada. Eles são conhecidos como _Estados comuns_.

Por exemplo, suponha que você tenha uma exibição `Entry` em sua página e queira que a aparência visual da `Entry` seja alterada das seguintes maneiras:

- O `Entry` deve ter um plano de fundo rosa quando o `Entry` estiver desabilitado.
- O `Entry` deve ter um plano de fundo de verde-limão normalmente.
- A `Entry` deve expandir para duas vezes sua altura normal quando ela tem o foco de entrada.

Você pode anexar a marcação VSM a uma exibição individual ou pode defini-la em um estilo se ela se aplicar a várias exibições. As próximas duas seções descrevem essas abordagens.

### <a name="vsm-markup-on-a-view"></a>Marcação VSM em uma exibição

Para anexar a marcação VSM a uma exibição `Entry`, primeiro separe as `Entry` nas marcas inicial e final:

```xaml
<Entry FontSize="18">

</Entry>
```

Ele recebe um tamanho de fonte explícito, pois um dos Estados usará a propriedade `FontSize` para dobrar o tamanho do texto na `Entry`.

Em seguida, insira `VisualStateManager.VisualStateGroups` marcas entre essas marcas:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>

    </VisualStateManager.VisualStateGroups>
</Entry>
```

[`VisualStateGroups`](xref:Xamarin.Forms.VisualStateManager.VisualStateGroupsProperty) é uma propriedade vinculável associada definida pela classe [`VisualStateManager`](xref:Xamarin.Forms.VisualStateManager) . (Para obter mais informações sobre as propriedades vinculáveis anexadas, consulte o artigo [Propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md).) É assim que a propriedade `VisualStateGroups` é anexada ao objeto `Entry`.

A propriedade `VisualStateGroups` é do tipo [`VisualStateGroupList`](xref:Xamarin.Forms.VisualStateGroupList), que é uma coleção de objetos [`VisualStateGroup`](xref:Xamarin.Forms.VisualStateGroup) . Dentro das marcas de `VisualStateManager.VisualStateGroups`, insira um par de marcas de `VisualStateGroup` para cada grupo de Estados visuais que você deseja incluir:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

Observe que a marca `VisualStateGroup` tem um atributo `x:Name` indicando o nome do grupo. A classe `VisualStateGroup` define uma propriedade `Name` que você pode usar em vez disso:

```xaml
<VisualStateGroup Name="CommonStates">
```

Você pode usar o `x:Name` ou `Name` mas não ambos no mesmo elemento.

A classe `VisualStateGroup` define uma propriedade chamada [`States`](xref:Xamarin.Forms.VisualStateGroup.States), que é uma coleção de objetos [`VisualState`](xref:Xamarin.Forms.VisualState) . `States` é a _Propriedade Content_ de `VisualStateGroups` para que você possa incluir as marcas de `VisualState` diretamente entre as marcas de `VisualStateGroup`. (As propriedades de conteúdo são discutidas no artigo [sintaxe XAML essencial](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md#content-properties).)

A próxima etapa é incluir um par de marcas para cada estado visual nesse grupo. Elas também podem ser identificadas usando `x:Name` ou `Name`:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">

            </VisualState>

            <VisualState x:Name="Focused">

            </VisualState>

            <VisualState x:Name="Disabled">

            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

`VisualState` define uma propriedade chamada [`Setters`](xref:Xamarin.Forms.VisualState.Setters), que é uma coleção de objetos [`Setter`](xref:Xamarin.Forms.Setter) . Esses são os mesmos objetos `Setter` que você usa em um objeto [`Style`](xref:Xamarin.Forms.Style) .

`Setters` _não_ é a propriedade content de `VisualState`, portanto, é necessário incluir marcas de elemento de propriedade para a propriedade `Setters`:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>

                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Focused">
                <VisualState.Setters>
    
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Disabled">
                <VisualState.Setters>

                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

Agora você pode inserir um ou mais objetos `Setter` entre cada par de marcas `Setters`. Estes são os objetos `Setter` que definem os Estados visuais descritos anteriormente:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Lime" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Focused">
                <VisualState.Setters>
                    <Setter Property="FontSize" Value="36" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Disabled">
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Pink" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

Cada marca de `Setter` indica o valor de uma propriedade específica quando esse estado é atual. Qualquer propriedade referenciada por um objeto `Setter` deve ser apoiada por uma propriedade vinculável.

A marcação semelhante a esta é a base da página **VSM na exibição** no programa de exemplo **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** . A página inclui três exibições de `Entry`, mas apenas a segunda tem a marcação VSM anexada a ela:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:VsmDemos"
             x:Class="VsmDemos.MainPage"
             Title="VSM Demos">

    <StackLayout>
        <StackLayout.Resources>
            <Style TargetType="Entry">
                <Setter Property="Margin" Value="20, 0" />
                <Setter Property="FontSize" Value="18" />
            </Style>

            <Style TargetType="Label">
                <Setter Property="Margin" Value="20, 30, 20, 0" />
                <Setter Property="FontSize" Value="Large" />
            </Style>
        </StackLayout.Resources>

        <Label Text="Normal Entry:" />

        <Entry />

        <Label Text="Entry with VSM: " />

        <Entry>
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup x:Name="CommonStates">
                    
                    <VisualState x:Name="Normal">
                        <VisualState.Setters>
                            <Setter Property="BackgroundColor" Value="Lime" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState x:Name="Focused">
                        <VisualState.Setters>
                            <Setter Property="FontSize" Value="36" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState x:Name="Disabled">
                        <VisualState.Setters>
                            <Setter Property="BackgroundColor" Value="Pink" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>

            <Entry.Triggers>
                <DataTrigger TargetType="Entry"
                             Binding="{Binding Source={x:Reference entry3},
                                               Path=Text.Length}"
                             Value="0">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </Entry.Triggers>
        </Entry>

        <Label Text="Entry to enable 2nd Entry:" />

        <Entry x:Name="entry3"
               Text=""
               Placeholder="Type something to enable 2nd Entry" />
    </StackLayout>
</ContentPage>
```

Observe que a segunda `Entry` também tem uma `DataTrigger` como parte de sua coleção de `Trigger`. Isso faz com que o `Entry` seja desabilitado até que algo seja digitado na terceira `Entry`. Aqui está a página na inicialização em execução no iOS, no Android e no Plataforma Universal do Windows (UWP):

[![VSM na exibição: desabilitado](vsm-images/VsmOnViewDisabled.png "VSM na exibição-desabilitado")](vsm-images/VsmOnViewDisabled-Large.png#lightbox)

O estado do visual atual é "desabilitado" para que o plano de fundo da segunda `Entry` seja rosa nas telas do iOS e do Android. A implementação do UWP de `Entry` não permite definir a cor do plano de fundo quando o `Entry` está desabilitado. 

Quando você insere um texto na terceira `Entry`, a segunda `Entry` muda para o estado "normal" e o plano de fundo agora é verde-limão:

[![VSM na exibição: normal](vsm-images/VsmOnViewNormal.png "VSM na exibição-normal")](vsm-images/VsmOnViewNormal-Large.png#lightbox)

Quando você toca na segunda `Entry`, ele obtém o foco de entrada. Ele alterna para o estado "focado" e expande para duas vezes sua altura:

[![VSM na exibição: focado](vsm-images/VsmOnViewFocused.png "VSM no modo de exibição focado")](vsm-images/VsmOnViewFocused-Large.png#lightbox)

Observe que o `Entry` não retém o plano de fundo de verde-limão quando Obtém o foco de entrada. Como o Gerenciador de estado visual alterna entre os Estados visuais, as propriedades definidas pelo estado anterior são desdefinidas. Tenha em mente que os Estados visuais são mutuamente exclusivos. O estado "normal" não significa apenas que o `Entry` está habilitado. Isso significa que a `Entry` está habilitada e não tem foco de entrada. 

Se você quiser que o `Entry` tenha um plano de fundo de verde-limão no estado "focado", adicione outra `Setter` a esse estado visual:

```xaml
<VisualState x:Name="Focused">
    <VisualState.Setters>
        <Setter Property="FontSize" Value="36" />
        <Setter Property="BackgroundColor" Value="Lime" />
    </VisualState.Setters>
</VisualState>
```

Para que esses `Setter` objetos funcionem corretamente, um `VisualStateGroup` deve conter `VisualState` objetos para todos os Estados nesse grupo. Se houver um estado visual que não tenha nenhum objeto de `Setter`, inclua assim mesmo como uma marca vazia:

```xaml
<VisualState x:Name="Normal" />
``` 

### <a name="visual-state-manager-markup-in-a-style"></a>Marcação do Gerenciador de estado visual em um estilo

Geralmente, é necessário compartilhar a mesma marcação do Gerenciador de estado visual entre duas ou mais exibições. Nesse caso, você desejará colocar a marcação em uma definição de `Style`.

Aqui está o `Style` implícito existente para os elementos `Entry` na página **VSM na exibição** :

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
</Style> 
```

Adicione marcas de `Setter` para a propriedade vinculável `VisualStateManager.VisualStateGroups` anexada:

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">

    </Setter>
</Style> 
```

A propriedade de conteúdo para `Setter` é `Value`, portanto, o valor da propriedade `Value` pode ser especificado diretamente dentro dessas marcas. Essa propriedade é do tipo `VisualStateGroupList`:

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>

        </VisualStateGroupList>
    </Setter>
</Style> 
```

Dentro dessas marcas, você pode incluir um ou mais objetos `VisualStateGroup`:

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup x:Name="CommonStates">

            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style> 
```

O restante da marcação VSM é o mesmo que antes.

Aqui está o **VSM na** página de estilo mostrando a marcação VSM completa:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmInStylePage"
             Title="VSM in Style">
    <StackLayout>
        <StackLayout.Resources>
            <Style TargetType="Entry">
                <Setter Property="Margin" Value="20, 0" />
                <Setter Property="FontSize" Value="18" />
                <Setter Property="VisualStateManager.VisualStateGroups">
                    <VisualStateGroupList>
                        <VisualStateGroup x:Name="CommonStates">

                            <VisualState x:Name="Normal">
                                <VisualState.Setters>
                                    <Setter Property="BackgroundColor" Value="Lime" />
                                </VisualState.Setters>
                            </VisualState>

                            <VisualState x:Name="Focused">
                                <VisualState.Setters>
                                    <Setter Property="FontSize" Value="36" />
                                    <Setter Property="BackgroundColor" Value="Lime" />
                                </VisualState.Setters>
                            </VisualState>

                            <VisualState x:Name="Disabled">
                                <VisualState.Setters>
                                    <Setter Property="BackgroundColor" Value="Pink" />
                                </VisualState.Setters>
                            </VisualState>
                        </VisualStateGroup>
                    </VisualStateGroupList>
                </Setter>
            </Style>

            <Style TargetType="Label">
                <Setter Property="Margin" Value="20, 30, 20, 0" />
                <Setter Property="FontSize" Value="Large" />
            </Style>
        </StackLayout.Resources>

        <Label Text="Normal Entry:" />

        <Entry />
        
        <Label Text="Entry with VSM: " />

        <Entry>
            <Entry.Triggers>
                <DataTrigger TargetType="Entry"
                             Binding="{Binding Source={x:Reference entry3},
                                               Path=Text.Length}"
                             Value="0">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </Entry.Triggers>
        </Entry>

        <Label Text="Entry to enable 2nd Entry:" />

        <Entry x:Name="entry3"
               Text=""
               Placeholder="Type something to enable 2nd Entry" />
    </StackLayout>
</ContentPage>
```

Agora, todos os `Entry` modos de exibição nesta página respondem da mesma forma aos seus Estados visuais. Observe também que o estado "focado" agora inclui um segundo `Setter` que fornece a cada `Entry` uma experiência de fundo de verde-limão também quando tem foco de entrada:

[![VSM em estilo](vsm-images/VsmInStyle.png "VSM em estilo")](vsm-images/VsmInStyle-Large.png#lightbox)

## <a name="defining-your-own-visual-states"></a>Definindo seus próprios Estados visuais

Todas as classes que derivam de `VisualElement` dão suporte aos três Estados comuns "normal", "com foco" e "desabilitado". Internamente, a classe [`VisualElement`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/VisualElement.cs) detecta quando está se tornando habilitada ou desabilitada, ou focada ou desfocada, e chama o método estático de [`VisualStateManager.GoToState`](xref:Xamarin.Forms.VisualStateManager.GoToState(Xamarin.Forms.VisualElement,System.String)) :

```csharp
VisualStateManager.GoToState(this, "Focused");
```

Esse é o único código do Gerenciador de estado visual que você encontrará na classe `VisualElement`. Como `GoToState` é chamado para cada objeto com base em todas as classes que derivam de `VisualElement`, você pode usar o Gerenciador de estado visual com qualquer objeto `VisualElement` para responder a essas alterações.

Curiosamente, o nome do grupo de estado visual "CommonStates" não é explicitamente referenciado em `VisualElement`. O nome do grupo não faz parte da API para o Gerenciador de estado visual. Em um dos dois programas de exemplo mostrados até agora, você pode alterar o nome do grupo de "CommonStates" para qualquer outra coisa e o programa continuará funcionando. O nome do grupo é meramente uma descrição geral dos Estados nesse grupo. Ele é compreendido implicitamente que os Estados visuais em qualquer grupo são mutuamente exclusivos: um estado e apenas um estado é atual a qualquer momento.

Se você quiser implementar seus próprios Estados visuais, precisará chamar `VisualStateManager.GoToState` do código. Geralmente, você fará essa chamada a partir do arquivo code-behind da sua classe Page.

A página **validação do VSM** no exemplo de **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** mostra como usar o Gerenciador de estado visual em conexão com a validação de entrada. O arquivo XAML consiste em dois elementos `Label`, um `Entry` e `Button`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmValidationPage"
             Title="VSM Validation">
    <StackLayout Padding="10, 10">
        
        <Label Text="Enter a U.S. phone number:"
               FontSize="Large" />

        <Entry Placeholder="555-555-5555"
               FontSize="Large"
               Margin="30, 0, 0, 0"
               TextChanged="OnTextChanged" />

        <Label x:Name="helpLabel"
               Text="Phone number must be of the form 555-555-5555, and not begin with a 0 or 1">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ValidityStates">
                    
                    <VisualState Name="Valid">
                        <VisualState.Setters>
                            <Setter Property="TextColor" Value="Transparent" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState Name="Invalid" />
                    
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </Label>

        <Button x:Name="submitButton"
                Text="Submit"
                FontSize="Large"
                Margin="0, 20"
                VerticalOptions="Center"
                HorizontalOptions="Center">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ValidityStates">
                    
                    <VisualState Name="Valid" />

                    <VisualState Name="Invalid">
                        <VisualState.Setters>
                            <Setter Property="IsEnabled" Value="False" />
                        </VisualState.Setters>
                    </VisualState>
                    
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </Button>
    </StackLayout>
</ContentPage>
```

A marcação VSM é anexada à segunda `Label` (chamada `helpLabel`) e à `Button` (chamada `submitButton`). Há dois Estados mutuamente exclusivos, denominados "válido" e "inválido". Observe que cada um dos dois grupos "ValidationState" contém marcas de `VisualState` para "válido" e "inválido", embora um deles esteja vazio em cada caso. 

Se o `Entry` não contiver um número de telefone válido, o estado atual será "inválido" e, portanto, a segunda `Label` ficará visível e o `Button` será desabilitado:

[![Validação de VSM: estado inválido](vsm-images/VsmValidationInvalid.png "Validação de VSM-inválida")](vsm-images/VsmValidationInvalid-Large.png#lightbox)

Quando um número de telefone válido é inserido, o estado atual se torna "válido". A segunda `Entry` desaparece e a `Button` agora está habilitada:

[![Validação de VSM: estado válido](vsm-images/VsmValidationValid.png "Validação de VSM-válida")](vsm-images/VsmValidationValid-Large.png#lightbox)

O arquivo code-behind é reponsible para manipular o evento de `TextChanged` da `Entry`. O manipulador usa uma expressão regular para determinar se a cadeia de caracteres de entrada é válida ou não. O método no arquivo code-behind chamado `GoToState` chama o método estático `VisualStateManager.GoToState` para `helpLabel` e `submitButton`:

```csharp
public partial class VsmValidationPage : ContentPage
{
    public VsmValidationPage ()
    {
        InitializeComponent ();

        GoToState(false);
    }

    void OnTextChanged(object sender, TextChangedEventArgs args)
    {
        bool isValid = Regex.IsMatch(args.NewTextValue, @"^[2-9]\d{2}-\d{3}-\d{4}$");
        GoToState(isValid);
    }

    void GoToState(bool isValid)
    {
        string visualState = isValid ? "Valid" : "Invalid";
        VisualStateManager.GoToState(helpLabel, visualState);
        VisualStateManager.GoToState(submitButton, visualState);
    }
}
```

Observe também que o método `GoToState` é chamado a partir do construtor para inicializar o estado. Sempre deve haver um estado atual. Mas em qualquer lugar no código há qualquer referência ao nome do grupo de estado visual, embora ele seja referenciado no XAML como "ValidationStates" para fins de clareza. 

Observe que o arquivo code-behind deve assumir a conta de cada objeto na página que é afetada por esses Estados visuais e chamar `VisualStateManager.GoToState` para cada um desses objetos. Neste exemplo, são apenas dois objetos (o `Label` e o `Button`), mas podem ser vários outros.

Você pode imaginar: se o arquivo code-behind deve fazer referência a cada objeto na página que é afetada por esses Estados visuais, por que não é possível que o arquivo code-behind simplesmente acesse os objetos diretamente? Certamente poderia. No entanto, a vantagem de usar o VSM é que você pode controlar como os elementos visuais reagem para um estado diferente inteiramente no XAML, o que mantém todo o design da interface do usuário em um único local. Isso evita a configuração da aparência visual acessando elementos visuais diretamente do code-behind.

Pode ser tentador considerar a derivação de uma classe de `Entry` e, talvez, definir uma propriedade que pode ser definida como uma função de validação externa. A classe que deriva de `Entry` pode então chamar o método `VisualStateManager.GoToState`. Esse esquema funcionaria bem, mas somente se o `Entry` fosse o único objeto afetado pelos diferentes Estados visuais. Neste exemplo, um `Label` e um `Button` também são afetados. Não há nenhuma maneira de marcação VSM anexada a um `Entry` para controlar outros objetos na página e não há nenhuma maneira de marcação VSM anexada a esses outros objetos para fazer referência a uma alteração no estado visual de outro objeto.

<a name="adaptive-layout" />

## <a name="using-the-visual-state-manager-for-adaptive-layout"></a>Usando o Gerenciador de estado visual para layout adaptável

Um aplicativo Xamarin. Forms em execução em um telefone geralmente pode ser exibido em uma taxa de proporção retrato ou paisagem, e um programa Xamarin. Forms em execução na área de trabalho pode ser redimensionado para assumir muitos tamanhos e proporções de proporção diferentes. Um aplicativo bem projetado pode exibir seu conteúdo de forma diferente para esses vários fatores forma de página ou janela. 

Essa técnica às vezes é conhecida como _layout adaptável_. Como o layout adaptável apenas envolve os visuais de um programa, ele é um aplicativo ideal do Gerenciador de estado visual.

Um exemplo simples é um aplicativo que exibe uma pequena coleção de botões que afetam o conteúdo do aplicativo. No modo retrato, esses botões podem ser exibidos em uma linha horizontal na parte superior da página:

[![Layout adaptável do VSM: retrato](vsm-images/VsmAdaptiveLayoutPortrait.png "Layout adaptável do VSM-retrato")](vsm-images/VsmAdaptiveLayoutPortrait-Large.png#lightbox)

No modo paisagem, a matriz de botões pode ser movida para um lado e exibida em uma coluna:

[![Layout adaptável do VSM: paisagem](vsm-images/VsmAdaptiveLayoutLandscape.png "Layout adaptável do VSM-paisagem")](vsm-images/VsmAdaptiveLayoutLandscape-Large.png#lightbox)

De cima para baixo, o programa está em execução no Plataforma Universal do Windows, no Android e no iOS.

A página de **layout adaptável do VSM** no exemplo de [VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos) define um grupo chamado "OrientationStates" com dois estados visuais chamados "retrato" e "paisagem". (Uma abordagem mais complexa pode ser baseada em várias larguras de página ou janela diferentes.) 

A marcação VSM ocorre em quatro locais no arquivo XAML. O `StackLayout` nomeado `mainStack` contém o menu e o conteúdo, que é um elemento `Image`. Este `StackLayout` deve ter uma orientação vertical no modo retrato e uma orientação horizontal no modo paisagem:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmAdaptiveLayoutPage"
             Title="VSM Adaptive Layout">

    <StackLayout x:Name="mainStack">
        <VisualStateManager.VisualStateGroups>
            <VisualStateGroup Name="OrientationStates">

                <VisualState Name="Portrait">
                    <VisualState.Setters>
                        <Setter Property="Orientation" Value="Vertical" />
                    </VisualState.Setters>
                </VisualState>

                <VisualState Name="Landscape">
                    <VisualState.Setters>
                        <Setter Property="Orientation" Value="Horizontal" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateManager.VisualStateGroups>
        
        <ScrollView x:Name="menuScroll">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="OrientationStates">
                    
                    <VisualState Name="Portrait">
                        <VisualState.Setters>
                            <Setter Property="Orientation" Value="Horizontal" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState Name="Landscape">
                        <VisualState.Setters>
                            <Setter Property="Orientation" Value="Vertical" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
            
            <StackLayout x:Name="menuStack">
                <VisualStateManager.VisualStateGroups>
                    <VisualStateGroup Name="OrientationStates">

                        <VisualState Name="Portrait">
                            <VisualState.Setters>
                                <Setter Property="Orientation" Value="Horizontal" />
                            </VisualState.Setters>
                        </VisualState>

                        <VisualState Name="Landscape">
                            <VisualState.Setters>
                                <Setter Property="Orientation" Value="Vertical" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateManager.VisualStateGroups>

                <StackLayout.Resources>
                    <Style TargetType="Button">
                        <Setter Property="VisualStateManager.VisualStateGroups">
                            <VisualStateGroupList>
                                <VisualStateGroup Name="OrientationStates">

                                    <VisualState Name="Portrait">
                                        <VisualState.Setters>
                                            <Setter Property="HorizontalOptions" Value="CenterAndExpand" />
                                            <Setter Property="Margin" Value="10, 5" />
                                        </VisualState.Setters>
                                    </VisualState>

                                    <VisualState Name="Landscape">
                                        <VisualState.Setters>
                                            <Setter Property="VerticalOptions" Value="CenterAndExpand" />
                                            <Setter Property="HorizontalOptions" Value="Center" />
                                            <Setter Property="Margin" Value="10" />
                                        </VisualState.Setters>
                                    </VisualState>
                                </VisualStateGroup>
                            </VisualStateGroupList>
                        </Setter>
                    </Style>
                </StackLayout.Resources>

                <Button Text="Banana"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="Banana.jpg" />
                
                <Button Text="Face Palm"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="FacePalm.jpg" />
                
                <Button Text="Monkey"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="monkey.png" />
                
                <Button Text="Seated Monkey"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="SeatedMonkey.jpg" />
            </StackLayout>
        </ScrollView>

        <Image x:Name="image"
               VerticalOptions="FillAndExpand"
               HorizontalOptions="FillAndExpand" />
    </StackLayout>
</ContentPage>
```

O `ScrollView` interno chamado `menuScroll` e o `StackLayout` nomeado `menuStack` implementam o menu de botões. A orientação desses layouts é oposta à `mainStack`. O menu deve ser horizontal no modo retrato e vertical no modo paisagem.

A quarta seção da marcação VSM está em um estilo implícito para os próprios botões. Essa marcação define as propriedades `VerticalOptions`, `HorizontalOptions` e `Margin` específicas para as orientações de portait e paisagem.

O arquivo code-behind define a propriedade `BindingContext` de `menuStack` para implementar `Button` comando e também anexa um manipulador ao evento `SizeChanged` da página:

```csharp
public partial class VsmAdaptiveLayoutPage : ContentPage
{
    public VsmAdaptiveLayoutPage ()
    {
        InitializeComponent ();

        SizeChanged += (sender, args) =>
        {
            string visualState = Width > Height ? "Landscape" : "Portrait";
            VisualStateManager.GoToState(mainStack, visualState);
            VisualStateManager.GoToState(menuScroll, visualState);
            VisualStateManager.GoToState(menuStack, visualState);

            foreach (View child in menuStack.Children)
            {
                VisualStateManager.GoToState(child, visualState);
            }
        };

        SelectedCommand = new Command<string>((filename) =>
        {
            image.Source = ImageSource.FromResource("VsmDemos.Images." + filename);
        });

        menuStack.BindingContext = this;
    }

    public ICommand SelectedCommand { private set; get; }
}
```

O manipulador de `SizeChanged` chama `VisualStateManager.GoToState` para os dois elementos `StackLayout` e `ScrollView` e, em seguida, percorre os filhos de `menuStack` para chamar `VisualStateManager.GoToState` para os elementos `Button`.

Pode parecer que o arquivo code-behind pode tratar as alterações de orientação mais diretamente definindo as propriedades de elementos no arquivo XAML, mas o Gerenciador de estado visual é definitivamente uma abordagem mais estruturada. Todos os visuais são mantidos no arquivo XAML, onde se tornam mais fáceis de examinar, manter e modificar.

## <a name="visual-state-manager-with-xamarinuniversity"></a>Gerenciador de estado visual com Xamarin. University

> [!VIDEO https://youtube.com/embed/qhUHbVP5mIQ]

**Vídeo do Gerenciador de estado visual do Xamarin. Forms 3,0**

## <a name="related-links"></a>Links relacionados

- [VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)
