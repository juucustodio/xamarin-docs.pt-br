---
title: O xamarin. Forms Visual State Manager
description: Use o Gerenciador de estado Visual para fazer alterações em elementos XAML com base em estados visuais definido no código.
ms.prod: xamarin
ms.assetid: 17296F14-640D-484B-A24C-A4E9B7013E4F
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 228501172ede71204c64e1efe1673ce92be424ea
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2019
ms.locfileid: "68656050"
---
# <a name="the-xamarinforms-visual-state-manager"></a>O xamarin. Forms Visual State Manager

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)

_Use o Gerenciador de estado Visual para fazer alterações em elementos XAML com base em estados visuais definido no código._

O Gerenciador de VSM (Visual State) é nova no xamarin. Forms 3.0. O VSM fornece uma maneira estruturada para fazer alterações visual para a interface do usuário do código. Na maioria dos casos, a interface do usuário do aplicativo é definida em XAML, e este XAML inclui a marcação que descreve como o Gerenciador de estado Visual afeta os visuais da interface do usuário.

O VSM apresenta o conceito de _estados visuais_. Um modo de exibição do xamarin. Forms, como um `Button` pode ter várias aparências diferentes dependendo do seu estado subjacente &mdash; se ele estiver desabilitado, ou pressionado ou se tem o foco de entrada. Esses são os estados do botão.

Estados visuais são coletados no _grupos de estado visual_. Todos os estados visuais dentro de um grupo de estado visual são mutuamente exclusivos. Estados visuais e grupos de estado visual são identificados por cadeias de caracteres de texto simples.

O Gerenciador de estado Visual do xamarin. Forms define um grupo de estado visual chamado "CommonStates" com três estados visuais:

- "Normal"
- "Desabilitado"
- "Com foco"

Há suporte para esse grupo de estados visuais para todas as classes que derivam de [ `VisualElement` ](xref:Xamarin.Forms.VisualElement), que é a classe base para [ `View` ](xref:Xamarin.Forms.View) e [ `Page` ](xref:Xamarin.Forms.Page). 

Você também pode definir seus próprios grupos de estado visual e os estados visuais, como este artigo irá demonstrar.

> [!NOTE]
> Os desenvolvedores de xamarin. Forms familiarizados com [gatilhos](~/xamarin-forms/app-fundamentals/triggers.md) estão cientes de que os gatilhos também podem fazer alterações aos visuais na interface do usuário com base em alterações em Propriedades da exibição ou o acionamento de eventos. No entanto, usando gatilhos para lidar com várias combinações dessas alterações pode se tornar bastante confuso. Historicamente, o Gerenciador de estado Visual foi introduzido em ambientes baseados em XAML do Windows para minimizar a confusão resultante de combinações de estados visuais. Os estados visuais dentro de um grupo de estado visual com o VSM, sempre são mutuamente exclusivos. A qualquer momento, apenas um estado em cada grupo é o estado atual.

## <a name="the-common-states"></a>Os estados comuns

O Gerenciador de estado Visual permite que você inclua seções no arquivo XAML que pode alterar a aparência visual de um modo de exibição, se o modo de exibição é normal ou desabilitado ou se tiver o foco de entrada. Eles são conhecidos como o _estados comuns_.

Por exemplo, suponha que você tenha um `Entry` modo de exibição em sua página, e você deseja que a aparência visual do `Entry` alterar das seguintes maneiras:

- O `Entry` deve ter uma rosa em segundo plano quando o `Entry` está desabilitado.
- O `Entry` deve ter um plano de fundo verde-limão normalmente.
- O `Entry` deve expandir para duas vezes sua altura normal quando ele tem foco de entrada.

Você pode anexar a marcação VSM a uma exibição individual, ou você pode defini-la em um estilo se apliquem a vários modos de exibição. As duas próximas seções descrevem essas abordagens.

### <a name="vsm-markup-on-a-view"></a>Marcação VSM em uma exibição

Anexar marcação VSM para um `Entry` exibir, primeiro separe o `Entry` nas marcas de início e término:

```xaml
<Entry FontSize="18">

</Entry>
```

Isso tenha dado a um tamanho de fonte explícito porque um dos Estados usará o `FontSize` propriedade para dobrar o tamanho do texto no `Entry`.

Em seguida, inserir `VisualStateManager.VisualStateGroups` marcas entre essas marcas:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>

    </VisualStateManager.VisualStateGroups>
</Entry>
```

[`VisualStateGroups`](xref:Xamarin.Forms.VisualStateManager.VisualStateGroupsProperty) é uma propriedade associável anexada definida pelo [ `VisualStateManager` ](xref:Xamarin.Forms.VisualStateManager) classe. (Para obter mais informações sobre propriedades vinculáveis anexadas, consulte o artigo [propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md).) Isso é como o `VisualStateGroups` propriedade está anexada a `Entry` objeto.

O `VisualStateGroups` propriedade é do tipo [ `VisualStateGroupList` ](xref:Xamarin.Forms.VisualStateGroupList), que é uma coleção de [ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup) objetos. Dentro de `VisualStateManager.VisualStateGroups` marcas, inserir um par de `VisualStateGroup` marcas para cada grupo de estados visuais que deseja incluir:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

Observe que o `VisualStateGroup` marca tem um `x:Name` atributo que indica o nome do grupo. O `VisualStateGroup` classe define um `Name` propriedade que você pode usar em vez disso:

```xaml
<VisualStateGroup Name="CommonStates">
```

Você pode usar tanto `x:Name` ou `Name` , mas não ambos no mesmo elemento.

O `VisualStateGroup` classe define uma propriedade chamada [ `States` ](xref:Xamarin.Forms.VisualStateGroup.States), que é uma coleção de [ `VisualState` ](xref:Xamarin.Forms.VisualState) objetos. `States` é o _propriedade de conteúdo_ dos `VisualStateGroups` para que você possa incluir o `VisualState` marcas diretamente entre o `VisualStateGroup` marcas. (Conteúdo de propriedades são discutidas no artigo [sintaxe de XAML essencial](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md#content-properties).)

A próxima etapa é incluir um par de marcas para cada estado visual nesse grupo. Eles também podem ser identificados usando `x:Name` ou `Name`:

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

`VisualState` define uma propriedade chamada [ `Setters` ](xref:Xamarin.Forms.VisualState.Setters), que é uma coleção de [ `Setter` ](xref:Xamarin.Forms.Setter) objetos. Essas são as mesmas `Setter` objetos que podem ser usados em um [ `Style` ](xref:Xamarin.Forms.Style) objeto.

`Setters` está _não_ a propriedade content de `VisualState`, portanto, é necessário incluir marcas de elemento de propriedade para o `Setters` propriedade:

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

Agora você pode inserir um ou mais `Setter` objetos entre cada par de `Setters` marcas. Esses são os `Setter` objetos que definem os estados visuais descritos anteriormente:

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

Cada `Setter` marca indica o valor de uma propriedade específica quando esse estado é atual. Qualquer propriedade referenciada por uma `Setter` objeto deve ser feito por uma propriedade associável.

Marcação semelhante a esta é a base para o **VSM na exibição** página na **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** programa de exemplo. A página inclui três `Entry` modos de exibição, mas apenas um segundo tem a marcação VSM anexada a ele:

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

Observe que a segunda `Entry` também tem um `DataTrigger` como parte de seu `Trigger` coleção. Isso faz com que o `Entry` será desabilitada até que algo seja digitado à terceira `Entry`. Aqui está a página na inicialização em execução no iOS, Android e Universal Windows Platform (UWP):

[![VSM na exibição: VSM]desabilitado(vsm-images/VsmOnViewDisabled.png "no modo de exibição-desabilitado")](vsm-images/VsmOnViewDisabled-Large.png#lightbox)

O estado visual atual é "Disabled" para que o plano de fundo do segundo `Entry` é rosa em telas de Android e iOS. A implementação de UWP do `Entry` não permite definir o plano de fundo de cor quando o `Entry` está desabilitado. 

Quando você digitar um texto à terceira `Entry`, o segundo `Entry` alterna para o estado "Normal" e o plano de fundo agora está verde-limão:

[![VSM na exibição: VSM]normal(vsm-images/VsmOnViewNormal.png "na exibição-normal")](vsm-images/VsmOnViewNormal-Large.png#lightbox)

Quando você tocar o segundo `Entry`, ele obtém o foco de entrada. Ele muda para o estado de "Focalizado" e se expande para duas vezes sua altura:

[![VSM na exibição: VSM]focado(vsm-images/VsmOnViewFocused.png "no modo de exibição focado")](vsm-images/VsmOnViewFocused-Large.png#lightbox)

Observe que o `Entry` não retém o plano de fundo verde-limão quando ele recebe o foco de entrada. Como o Gerenciador de estado Visual alterna entre os estados visuais, as propriedades definidas pelo estado anterior são não definidas. Tenha em mente que os estados visuais são mutuamente exclusivos. O estado "Normal" não significa apenas que o `Entry` está habilitado. Isso significa que o `Entry` está habilitado e não tem o foco de entrada. 

Se você quiser que o `Entry` para ter um plano de fundo verde-limão em estado "Focalizado", adicione outro `Setter` para que o estado visual:

```xaml
<VisualState x:Name="Focused">
    <VisualState.Setters>
        <Setter Property="FontSize" Value="36" />
        <Setter Property="BackgroundColor" Value="Lime" />
    </VisualState.Setters>
</VisualState>
```

Para que eles `Setter` objetos funcione corretamente, um `VisualStateGroup` deve conter `VisualState` objetos para todos os estados no grupo. Se não houver um estado visual que não tem nenhum `Setter` objetos, incluí-lo mesmo assim como uma marca vazia:

```xaml
<VisualState x:Name="Normal" />
``` 

### <a name="visual-state-manager-markup-in-a-style"></a>Marcação de Gerenciador de estado visual em um estilo

Ele geralmente é necessário compartilhar a mesma marcação de Gerenciador de estado Visual entre dois ou mais exibições. Nesse caso, você desejará colocar a marcação em um `Style` definição.

Aqui está implícita existente `Style` para o `Entry` elementos na **VSM no modo de exibição** página:

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
</Style> 
```

Adicione `Setter` marcas para o `VisualStateManager.VisualStateGroups` propriedade associável anexada:

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">

    </Setter>
</Style> 
```

A propriedade de conteúdo para `Setter` está `Value`, portanto, o valor da `Value` propriedade pode ser especificada diretamente dentro de marcas. Se a propriedade é do tipo `VisualStateGroupList`:

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

Dentro dessas marcas, você pode incluir um ou mais `VisualStateGroup` objetos:

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

O restante da marcação VSM é o mesmo de antes.

Aqui está o **VSM em estilo** página mostrando a marcação VSM completa:

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

Agora todos os `Entry` exibições nesta página respondem da mesma maneira para seus estados visuais. Observe também que o estado "Focalizado" inclui agora uma segunda `Setter` que dá a cada `Entry` um verde-limão em segundo plano também quando ele tem o foco de entrada:

[![VSM em estilo](vsm-images/VsmInStyle.png "VSM no estilo")](vsm-images/VsmInStyle-Large.png#lightbox)

## <a name="defining-your-own-visual-states"></a>Definindo seus próprios estados visuais

Cada classe que deriva de `VisualElement` dá suporte a três estados comuns "Normal", "Voltada para" e "Disabled". Internamente, o [ `VisualElement` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/VisualElement.cs) classe detecta quando ele está se tornando habilitado ou desabilitado, ou focalizado ou sem foco e chama estático [ `VisualStateManager.GoToState` ](xref:Xamarin.Forms.VisualStateManager.GoToState(Xamarin.Forms.VisualElement,System.String)) método:

```csharp
VisualStateManager.GoToState(this, "Focused");
```

Isso é o único código do Gerenciador de estado Visual que você encontrará o `VisualElement` classe. Porque `GoToState` é chamado para cada objeto com base em cada classe que deriva de `VisualElement`, você pode usar o Gerenciador de estado Visual com qualquer `VisualElement` objeto para responder a essas alterações.

Curiosamente, o nome do grupo de estados visuais "CommonStates" não é explicitamente referenciado nos `VisualElement`. O nome do grupo não é parte da API para o Gerenciador de estado Visual. Dentro de um a dois programa de exemplo mostrado até agora, você pode alterar o nome do grupo de "CommonStates" para qualquer outra coisa, e o programa ainda funcionará. O nome do grupo é meramente uma descrição geral dos estados no grupo. Ele é compreendido implicitamente que os Estados visuais em qualquer grupo são mutuamente exclusivos: Um estado e apenas um estado é atual a qualquer momento.

Se você quiser implementar seus próprios estados visuais, você precisará chamar `VisualStateManager.GoToState` do código. Geralmente, você fará essa chamada do arquivo code-behind da sua classe de página.

O **VSM validação** página de **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** exemplo mostra como usar o Gerenciador de estado Visual em conexão com a validação de entrada. O arquivo XAML consiste em dois `Label` elementos, uma `Entry`, e `Button`:

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

Marcação VSM está anexada à segunda `Label` (chamado `helpLabel`) e o `Button` (chamado `submitButton`). Há dois estados mutuamente exclusivas, chamados de "Válido" e "Inválido". Observe que cada um dos dois grupos de "ValidationState" contém `VisualState` marcas para "Válido" e "Inválido", embora um deles está vazio em cada caso. 

Se o `Entry` não contém um número de telefone válido, em seguida, o estado atual é "Inválido" e, portanto, a segunda `Label` está visível e o `Button` está desabilitado:

[![Validação de VSM: ](vsm-images/VsmValidationInvalid.png "Validação de VSM de estado inválida-inválida")](vsm-images/VsmValidationInvalid-Large.png#lightbox)

Quando um número de telefone válido for inserido, em seguida, o estado atual se torna "Válido". A segunda `Entry` desaparece e a `Button` agora está habilitado:

[![Validação de VSM: ](vsm-images/VsmValidationValid.png "Validação de VSM de estado válida-válida")](vsm-images/VsmValidationValid-Large.png#lightbox)

O arquivo code-behind é responsável para a manipulação de `TextChanged` eventos do `Entry`. O manipulador usa uma expressão regular para determinar se a cadeia de caracteres de entrada é válida ou não. O método em que o arquivo code-behind chamado `GoToState` chama estático `VisualStateManager.GoToState` método para ambos `helpLabel` e `submitButton`:

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

Observe também que o `GoToState` método é chamado de construtor para inicializar o estado. Sempre deve haver um estado atual. Mas não no código há qualquer referência ao nome do grupo de estados visuais, embora ela é referenciada no XAML como "ValidationStates" para fins de clareza. 

Observe que o arquivo code-behind deve levar em conta todos os objetos na página que é afetada por esses estados visuais e chamar `VisualStateManager.GoToState` para cada um desses objetos. Neste exemplo, ele é apenas dois objetos (o `Label` e o `Button`), mas poderia ser vários mais.

Você pode estar imaginando: Se o arquivo code-behind deve fazer referência a cada objeto na página que é afetada por esses Estados visuais, por que não é possível que o arquivo code-behind simplesmente acesse os objetos diretamente? Obviamente, foi possível. No entanto, a vantagem de usar o VSM é que você pode controlar os elementos visuais como reagir a estado diferente inteiramente em XAML, que mantém todos o design de interface do usuário em um único local. Isso evita a aparência visual de configuração por meio de acessar elementos visuais diretamente no code-behind.

Pode ser tentador considere derivar uma classe de `Entry` e talvez definindo uma propriedade que você pode definir uma função externa de validação. A classe que deriva de `Entry` , em seguida, pode chamar o `VisualStateManager.GoToState` método. Esse esquema funcionaria bem, mas somente se o `Entry` foram o único objeto afetado pelos estados visuais diferentes. Neste exemplo, uma `Label` e um `Button` também são afetados. Não é possível para marcação VSM anexados a um `Entry` para controlar outros objetos na página e nenhuma maneira para marcação VSM anexados a esses outros objetos para fazer referência a uma alteração de estado visual de outro objeto.

<a name="adaptive-layout" />

## <a name="using-the-visual-state-manager-for-adaptive-layout"></a>Usando o Gerenciador de estado Visual para o layout adaptável

Um xamarin. Forms aplicativo em execução em um telefone geralmente pode ser exibido em um retrato ou paisagem taxa de proporção e um programa de xamarin. Forms em execução na área de trabalho pode ser redimensionada para assumir muitos tamanhos diferentes e taxas de proporção. Um aplicativo bem projetado pode exibir seu conteúdo diferentes para esses vários fatores de formulário de página ou janela. 

Às vezes, essa técnica é conhecida como _adaptável layout_. Como layout adaptável envolve apenas elementos visuais de um programa, ele é um aplicativo ideal do Gerenciador de estado Visual.

Um exemplo simples é um aplicativo que exibe uma pequena coleção de botões que afetam o conteúdo do aplicativo. No modo retrato, esses botões podem ser exibidos em uma linha horizontal na parte superior da página:

[![Layout adaptável do VSM: ](vsm-images/VsmAdaptiveLayoutPortrait.png "Layout adaptável do VSM de retrato – retrato")](vsm-images/VsmAdaptiveLayoutPortrait-Large.png#lightbox)

No modo paisagem, a matriz de botões pode ser movida para um lado e exibida em uma coluna:

[![Layout adaptável do VSM: ](vsm-images/VsmAdaptiveLayoutLandscape.png "Layout adaptável do VSM de paisagem-paisagem")](vsm-images/VsmAdaptiveLayoutLandscape-Large.png#lightbox)

De cima para baixo, o programa está em execução a plataforma Universal do Windows, Android e iOS.

O **Layout adaptável do VSM** página de [VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos) exemplo define um grupo chamado "OrientationStates" com dois estados visuais denominados "Retrato" e "Paisagem". (Uma abordagem mais complexa pode ser baseada em várias larguras de página ou janela diferentes). 

Marcação VSM ocorre em quatro lugares no arquivo XAML. O `StackLayout` nomeado `mainStack` contém o menu e o conteúdo, que é um `Image` elemento. Isso `StackLayout` deve ter uma orientação vertical no modo retrato e uma orientação horizontal no modo paisagem:

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

Interno `ScrollView` nomeado `menuScroll` e o `StackLayout` denominada `menuStack` implementar o menu de botões. A orientação desses layouts é o oposto de `mainStack`. O menu deve ser horizontal no modo retrato e vertical no modo paisagem.

A quarta seção de marcação VSM está em um estilo implícito para os botões em si. Essa marcação define `VerticalOptions`, `HorizontalOptions`, e `Margin` propriedades específicas para as orientações portait e paisagem.

Os conjuntos de arquivos de lógica de `BindingContext` propriedade de `menuStack` para implementar `Button` dos comandos e também anexa um manipulador para o `SizeChanged` eventos da página:

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

O `SizeChanged` chamadas do manipulador `VisualStateManager.GoToState` para os dois `StackLayout` e `ScrollView` elementos e, em seguida, executa um loop pelos filhos de `menuStack` chamar `VisualStateManager.GoToState` para o `Button` elementos.

Pode parecer como se o arquivo code-behind pode lidar com alterações de orientação mais diretamente, definindo as propriedades dos elementos no arquivo XAML, mas o Gerenciador de estado Visual é definitivamente uma abordagem mais estruturada. Todos os elementos visuais são mantidos no arquivo XAML, onde eles se tornam mais fácil de examinar, manutenção e modificar.

## <a name="visual-state-manager-with-xamarinuniversity"></a>Gerenciador de estado visual com Xamarin.University

> [!VIDEO https://youtube.com/embed/qhUHbVP5mIQ]

**Vídeo do Gerenciador de estado visual do Xamarin. Forms 3,0**

## <a name="related-links"></a>Links relacionados

- [VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)
