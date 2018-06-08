---
title: O Gerenciador de estado de Visual xamarin. Forms
description: Use o Gerenciador de estado Visual para fazer alterações em elementos XAML, com base nos Estados visuais definido no código.
ms.prod: xamarin
ms.assetid: 17296F14-640D-484B-A24C-A4E9B7013E4F
ms.technology: xamarin-forms
ms.custom: xamu-video
author: charlespetzold
ms.author: chape
ms.date: 05/07/2018
ms.openlocfilehash: 3c0330d8d6d07112350db007d0500d57c236dc24
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34848018"
---
# <a name="the-xamarinforms-visual-state-manager"></a>O Gerenciador de estado de Visual xamarin. Forms

_Use o Gerenciador de estado Visual para fazer alterações em elementos XAML, com base nos Estados visuais definido no código._

O Gerenciador de estado Visual (VSM) é nova no 3.0 xamarin. Forms. O VSM fornece uma maneira estruturada para fazer alterações visuais para a interface do usuário do código. Na maioria dos casos, a interface do usuário do aplicativo é definida em XAML, e este XAML inclui a marcação que descreve como o Gerenciador de estado Visual afeta os visuais da interface do usuário.

O VSM apresenta o conceito de _estados visuais_. Um modo de exibição do xamarin. Forms como uma `Button` pode ter várias aparências visual diferentes dependendo de seu estado subjacente &mdash; se ele estiver desabilitado, pressionado ou tem o foco de entrada. Esses são os estados do botão.

Estados visuais são coletados no _grupos de estado visual_. Todos os estados visuais dentro de um grupo de estado visual são mutuamente exclusivos. Os estados visuais e os grupos de estado visual são identificados por cadeias de caracteres de texto simples.

O Gerenciador de estado Visual xamarin. Forms define um grupo de estado visual chamado "CommonStates" com três estados visuais:

- "Normal"
- "Desabilitado"
- "Foco"

Esse grupo de estado visual tem suporte para todas as classes que derivam de [ `VisualElement` ](xref:Xamarin.Forms.VisualElement), que é a classe base para [ `View` ](xref:Xamarin.Forms.View) e [ `Page` ](xref:Xamarin.Forms.Page). 

Você também pode definir seus próprios grupos de estado visual e estados visuais, como este artigo demonstrará.

> [!NOTE]
> Xamarin. Forms desenvolvedores familiarizados com [gatilhos](~/xamarin-forms/app-fundamentals/triggers.md) estão cientes de que os gatilhos podem também fazer alterações visuais na interface do usuário com base em alterações nas propriedades de um modo de exibição ou o acionamento de eventos. No entanto, o uso de gatilhos para lidar com várias combinações dessas alterações pode se tornar muito confuso. Historicamente, o Gerenciador de estado Visual foi introduzido em ambientes baseados em XAML do Windows para minimizar a confusão resultante de combinações de estados visuais. Com o VSM, os estados visuais dentro de um grupo de estado visual sempre são mutuamente exclusivos. A qualquer momento, apenas um estado de cada grupo é o estado atual.

## <a name="the-common-states"></a>Os estados comuns

O Gerenciador de estado Visual permite que você inclua seções no arquivo XAML que pode alterar a aparência de uma exibição se a exibição é normal ou desabilitado ou se tiver o foco de entrada. Eles são conhecidos como o _estados comuns_.

Por exemplo, suponha que você tenha um `Entry` exibição na página, e você deseja que a aparência visual do `Entry` alterar das seguintes maneiras:

- O `Entry` devem ter uma rosa em segundo plano quando o `Entry` está desabilitado.
- O `Entry` devem ter um plano de fundo verde-limão normalmente.
- O `Entry` deve expandir para duas vezes sua altura normal quando ele tem foco de entrada.

Você pode anexar a marcação VSM a uma exibição individual, ou você pode defini-la em um estilo, se ela se aplica a vários modos de exibição. As próximas duas seções descrevem essas abordagens.

### <a name="vsm-markup-on-a-view"></a>Marcação VSM em uma exibição

Para anexar a marcação VSM para um `Entry` exibir, primeiro separar o `Entry` nas marcas de início e término:

```xaml
<Entry FontSize="18">

</Entry>
```

Recebe um tamanho de fonte explícito porque um dos Estados usará o `FontSize` propriedade para dobrar o tamanho do texto no `Entry`.

Em seguida, inserir `VisualStateManager.VisualStateGroups` marcas entre as marcas:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>

    </VisualStateManager.VisualStateGroups>
</Entry>
```

[`VisualStateGroups`](xref:Xamarin.Forms.VisualStateManager.VisualStateGroupsProperty) é uma propriedade associável anexada definida pelo [ `VisualStateManager` ](xref:Xamarin.Forms.VisualStateManager) classe. (Para obter mais informações sobre as propriedades vinculáveis anexadas, consulte o artigo [propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md).) Isso é como o `VisualStateGroups` propriedade está associada a `Entry` objeto.

O `VisualStateGroups` é de propriedade do tipo [ `VisualStateGroupList` ](xref:Xamarin.Forms.VisualStateGroupList), que é uma coleção de [ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup) objetos. Dentro de `VisualStateManager.VisualStateGroups` marcas, insira um par de `VisualStateGroup` marcas para cada grupo de estados visuais que deseja incluir:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

Observe que o `VisualStateGroup` marca tem um `x:Name` que indica o nome do grupo de atributo. O `VisualStateGroup` classe define um `Name` propriedade que você pode usar em vez disso:

```xaml
<VisualStateGroup Name="CommonStates">
```

Você pode usar o `x:Name` ou `Name` , mas não ambos no mesmo elemento.

O `VisualStateGroup` classe define uma propriedade chamada [ `States` ](xref:Xamarin.Forms.VisualStateGroup.States), que é uma coleção de [ `VisualState` ](xref:Xamarin.Forms.VisualState) objetos. `States` é o _propriedade de conteúdo_ de `VisualStateGroups` para que você possa incluir o `VisualState` marcas diretamente entre o `VisualStateGroup` marcas. (Conteúdo de propriedades são discutidas no artigo [essenciais de sintaxe XAML](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md#content-properties).)

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

`VisualState` define uma propriedade chamada [ `Setters` ](xref:Xamarin.Forms.VisualState.Setters), que é uma coleção de [ `Setter` ](xref:Xamarin.Forms.Setter) objetos. Essas são as mesmas `Setter` objetos que podem ser usados em uma [ `Style` ](xref:Xamarin.Forms.Style) objeto.

`Setters` é _não_ a propriedade content de `VisualState`, portanto, é necessário incluir marcas de elemento de propriedade para o `Setters` propriedade:

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

Agora você pode inserir um ou mais `Setter` objetos entre cada par de `Setters` marcas. Estes são os `Setter` objetos que definem os estados visuais descritos anteriormente:

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

Cada `Setter` marca indica o valor de uma determinada propriedade quando o estado atual. Qualquer propriedade referenciada por uma `Setter` objeto deve ser feito por uma propriedade associável.

Marcação semelhante a esta é a base para o **VSM na exibição** página o **[VsmDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VsmDemos/)** programa de exemplo. A página inclui três `Entry` modos de exibição, mas apenas o um segundo tem a marcação VSM anexada a ela:

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

Observe que a segunda `Entry` também tem um `DataTrigger` como parte de seu `Trigger` coleção. Isso faz com que o `Entry` será desabilitada até que algo é digitado para a terceira `Entry`. Aqui está a página na inicialização em execução no iOS, Android e o Windows UWP (plataforma Universal):

[![VSM no modo de exibição: desabilitado](vsm-images/VsmOnViewDisabled.png "VSM na exibição - desabilitada")](vsm-images/VsmOnViewDisabled-Large.png#lightbox)

O estado visual atual é "desabilitado" para que o plano de fundo da segunda `Entry` é rosa no iOS e Android telas. A implementação de UWP do `Entry` não permite a configuração do plano de fundo de cor quando o `Entry` está desabilitado. 

Quando você digita um texto à terceira `Entry`, a segunda `Entry` alterna para o estado de "Normal" e o plano de fundo agora está verde-limão:

[![VSM no modo de exibição: Normal](vsm-images/VsmOnViewNormal.png "VSM na exibição - normal")](vsm-images/VsmOnViewNormal-Large.png#lightbox)

Quando você toca o segundo `Entry`, ele obtém o foco de entrada. Ele alterna para o estado de "Focalizada" e se expande para duas vezes sua altura:

[![VSM no modo de exibição: foco](vsm-images/VsmOnViewFocused.png "VSM na exibição - focada")](vsm-images/VsmOnViewFocused-Large.png#lightbox)

Observe que o `Entry` não mantém o plano de fundo verde-limão quando ele recebe o foco de entrada. Como o Gerenciador de estado Visual alterna entre os estados visuais, as propriedades definidas pelo estado anterior são não definidas. Tenha em mente que os estados visuais são mutuamente exclusivos. O estado de "Normal" significa apenas que o `Entry` está habilitado. Isso significa que o `Entry` está habilitada e não tem o foco de entrada. 

Se você quiser que o `Entry` para ter um plano de fundo verde-limão no estado "Focalizada", adicione outro `Setter` para esse estado visual:

```xaml
<VisualState x:Name="Focused">
    <VisualState.Setters>
        <Setter Property="FontSize" Value="36" />
        <Setter Property="BackgroundColor" Value="Lime" />
    </VisualState.Setters>
</VisualState>
```

Para que eles `Setter` objetos funcione corretamente, um `VisualStateGroup` deve conter `VisualState` objetos para todos os estados nesse grupo. Se houver um estado visual que não tem nenhum `Setter` objetos, incluí-lo mesmo assim como uma marca vazia:

```xaml
<VisualState x:Name="Normal" />
``` 

### <a name="visual-state-manager-markup-in-a-style"></a>Marcação de Gerenciador de estado visual em um estilo

Geralmente é necessária para compartilhar a mesma marcação de Gerenciador de estado Visual entre dois ou mais modos de exibição. Nesse caso, você vai querer colocar a marcação em um `Style` definição.

Aqui está implícita existente `Style` para o `Entry` elementos o **VSM no modo de exibição** página:

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
</Style> 
```

Adicionar `Setter` marcas para o `VisualStateManager.VisualStateGroups` anexado a propriedade associável:

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">

    </Setter>
</Style> 
```

A propriedade de conteúdo para `Setter` é `Value`, portanto o valor de `Value` propriedade pode ser especificada diretamente dentro de marcas. Se a propriedade é do tipo `VisualStateGroupList`:

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

Aqui está o **VSM em estilo** página mostrando a marcação VSM concluída:

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

Agora tudo o `Entry` exibições nesta página respondem da mesma maneira para seus estados visuais. Observe também que o estado de "Focalizada" agora inclui um segundo `Setter` que dá a cada `Entry` um verde-limão em segundo plano também quando ele tem entrada foco:

[![VSM em estilo](vsm-images/VsmInStyle.png "VSM em estilo")](vsm-images/VsmInStyle-Large.png#lightbox)

## <a name="defining-your-own-visual-states"></a>Definindo seus próprios estados visuais

Cada classe que deriva de `VisualElement` oferece suporte a três estados comuns "Normal", "Foco" e "Desativado". Internamente, o [ `VisualElement` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/VisualElement.cs) classe detecta quando ele está se tornando habilitado ou desabilitado, ou focalizado ou foco e chama estático [ `VisualStateManager.GoToState` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualStateManager.GoToState/p/Xamarin.Forms.VisualElement/System.String/) método:

```csharp
VisualStateManager.GoToState(this, "Focused");
```

Este é o único código de Gerenciador de estado Visual que você encontrará o `VisualElement` classe. Porque `GoToState` é chamado para cada objeto com base em cada classe que deriva de `VisualElement`, você pode usar o Gerenciador de estado Visual com qualquer `VisualElement` objeto para responder a essas alterações.

Curiosamente, o nome do grupo de estado visual "CommonStates" não é explicitamente referenciado na `VisualElement`. O nome do grupo não é parte da API para o Gerenciador de estado Visual. Dentro de um a dois programa de exemplo mostrado até agora, você pode alterar o nome do grupo de "CommonStates" para qualquer outra coisa e o programa ainda funcionarão. O nome do grupo é simplesmente uma descrição geral dos Estados nesse grupo. É implicitamente entendido que os estados visuais em qualquer grupo são mutuamente exclusivos: um estado e apenas um estado é atual a qualquer momento.

Se você quiser implementar seus próprios estados visuais, você precisará chamar `VisualStateManager.GoToState` do código. Geralmente, você fará essa chamada do arquivo code-behind da sua classe de página.

O **VSM validação** página o **[VsmDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VsmDemos/)** exemplo mostra como usar o Gerenciador de estado Visual em conjunto com a validação de entrada. O arquivo XAML consiste em dois `Label` elementos, um `Entry`, e `Button`:

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

Marcação VSM está anexada à segunda `Label` (denominado `helpLabel`) e o `Button` (chamado `submitButton`). Há dois estados mutuamente exclusivas, denominados "Válido" e "Inválido". Observe que cada um dos dois grupos de "ValidationState" contém `VisualState` marcas para "Válido" e "Inválido", embora um deles está vazio em cada caso. 

Se o `Entry` não contém um número de telefone válido, em seguida, o estado atual é "Inválido" e, portanto, a segunda `Label` está visível e o `Button` está desabilitada:

[![Validação VSM: Estado inválido](vsm-images/VsmValidationInvalid.png "validação VSM - inválido")](vsm-images/VsmValidationInvalid-Large.png#lightbox)

Quando um número de telefone válido for inserido, o estado atual torna-se "Válido". A segunda `Entry` desaparece e o `Button` agora está habilitado:

[![Validação VSM: Estado válido](vsm-images/VsmValidationValid.png "validação VSM - válida")](vsm-images/VsmValidationValid-Large.png#lightbox)

O arquivo code-behind é reponsible para manipulação de `TextChanged` evento do `Entry`. O manipulador usa uma expressão regular para determinar se a cadeia de caracteres de entrada é válida ou não. O método em que o arquivo de code-behind chamado `GoToState` chama estático `VisualStateManager.GoToState` método para ambos `helpLabel` e `submitButton`:

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

Observe também que o `GoToState` método é chamado a partir do construtor para inicializar o estado. Sempre deve haver um estado atual. Mas isso no código há qualquer referência ao nome do grupo de estado visual, embora ela é referenciada em XAML, como "ValidationStates" para fins de esclarecimento. 

Observe que o arquivo code-behind deve levar em conta de cada objeto na página que é afetada por esses estados visuais e chamar `VisualStateManager.GoToState` para cada um desses objetos. Neste exemplo, ele é apenas dois objetos (o `Label` e `Button`), mas é possível que várias mais.

Você talvez esteja se perguntando: se o arquivo code-behind deve fazer referência a cada objeto na página que é afetado por esses estados visuais, por que não o arquivo code-behind simplesmente acessar os objetos diretamente? Certamente poderia. No entanto, a vantagem de usar o VSM é que você pode controlar elementos visuais como reagir a estado diferente inteiramente em XAML, que mantém todo o design de interface do usuário em um local. Isso evita a aparência visual de configuração acessando elementos visuais diretamente do code-behind.

Pode ser tentador considere derivando uma classe de `Entry` e talvez definindo uma propriedade que você pode definir uma função externa de validação. A classe que deriva de `Entry` , em seguida, pode chamar o `VisualStateManager.GoToState` método. Esse esquema funcionaria bem, mas apenas se o `Entry` foram somente objeto afetado pelos estados visuais diferentes. Neste exemplo, um `Label` e um `Button` também serão afetados. Não é possível para marcação VSM anexado a um `Entry` para controlar outros objetos na página e nenhuma maneira de marcação VSM anexado a esses outros objetos para fazer referência a uma alteração de estado visual de outro objeto.

<a name="adaptive-layout" />

## <a name="using-the-visual-state-manager-for-adaptive-layout"></a>Usando o Gerenciador de estado Visual para layout adaptável

Um xamarin. Forms aplicativo em execução em um telefone geralmente pode ser exibido em um retrato ou taxa de proporção de paisagem e um programa de xamarin. Forms em execução na área de trabalho pode ser redimensionada para assumir várias taxas de proporção e tamanhos diferentes. Um aplicativo bem projetado pode exibir seu conteúdo diferentes para esses vários fatores de formulário de página ou janela. 

Essa técnica às vezes é conhecida como _layout adaptável_. Como layout adaptável envolve apenas visuais de um programa, ele é um aplicativo ideal do Gerenciador de estado Visual.

Um exemplo simples é um aplicativo que exibe um pequeno conjunto de botões que afetam o conteúdo do aplicativo. No modo retrato, esses botões podem ser exibidos em uma linha horizontal na parte superior da página:

[![Layout de adaptável VSM: Retrato](vsm-images/VsmAdaptiveLayoutPortrait.png "layout de adaptável VSM - retrato")](vsm-images/VsmAdaptiveLayoutPortrait-Large.png#lightbox)

No modo paisagem, a matriz de botões pode ser movida para um lado e exibida em uma coluna:

[![Layout de adaptável VSM: Paisagem](vsm-images/VsmAdaptiveLayoutLandscape.png "layout de adaptável VSM - paisagem")](vsm-images/VsmAdaptiveLayoutLandscape-Large.png#lightbox)

De cima para baixo, o programa está em execução na plataforma Universal do Windows, Android e iOS.

O **Layout adaptável VSM** página o [VsmDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VsmDemos/) exemplo define um grupo chamado "OrientationStates" com dois estados visuais denominados "Portrait" e "Landscape". (Uma abordagem mais complexa pode ser baseada em várias diferentes larguras de página ou janela.) 

Marcação VSM ocorre em quatro locais no arquivo XAML. O `StackLayout` chamado `mainStack` contém o menu e o conteúdo, que é um `Image` elemento. Isso `StackLayout` devem ter uma orientação vertical no modo retrato e uma orientação horizontal no modo paisagem:

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

Interna `ScrollView` chamado `menuScroll` e `StackLayout` chamado `menuStack` implementar o menu de botões. A orientação desses layouts é o oposto do `mainStack`. O menu deve ser horizontal no modo retrato e vertical no modo paisagem.

A quarta seção de marcação VSM está em um estilo implícito dos botões de si mesmos. Essa marcação define `VerticalOptions`, `HorizontalOptions`, e `Margin` propriedades específicas para as orientações portait e paisagem.

Os conjuntos de arquivos por trás do código o `BindingContext` propriedade de `menuStack` para implementar `Button` ordenar e também anexa um manipulador para o `SizeChanged` eventos da página:

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

O `SizeChanged` chamadas de manipulador `VisualStateManager.GoToState` para os dois `StackLayout` e `ScrollView` elementos e, em seguida, percorre os filhos do `menuStack` chamar `VisualStateManager.GoToState` para o `Button` elementos.

Pode parecer que o arquivo code-behind pode manipular as alterações de orientação mais diretamente definindo propriedades de elementos no arquivo XAML, mas o Gerenciador de estado Visual é definitivamente uma abordagem mais estruturada. Todos os elementos visuais são mantidos no arquivo XAML, no qual eles se tornam mais fácil examinar, manutenção e modificar.

## <a name="visual-state-manager-with-xamarinuniversity"></a>Gerenciador de estado visual com Xamarin.University

> [!VIDEO https://youtube.com/embed/qhUHbVP5mIQ]

**Gerenciador de estado Visual xamarin. Forms 3.0, por [University Xamarin](https://university.xamarin.com/)**

## <a name="related-links"></a>Links relacionados

- [VsmDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VsmDemos/)

