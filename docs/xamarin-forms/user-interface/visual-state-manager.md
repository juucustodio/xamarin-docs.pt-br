---
title: Gerenciador de estado visual do Xamarin. Forms
description: Use o Gerenciador de estado Visual para fazer alterações em elementos XAML com base em estados visuais definido no código.
ms.prod: xamarin
ms.assetid: 17296F14-640D-484B-A24C-A4E9B7013E4F
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 02/21/2020
ms.openlocfilehash: 0149806f3ab3772bc206cea9540a989d997c817b
ms.sourcegitcommit: f43d5ecafd19cbc5cce39201916a83927a34617a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2020
ms.locfileid: "78292938"
---
# <a name="xamarinforms-visual-state-manager"></a>Gerenciador de estado visual do Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)

_Use o Gerenciador de estado visual para fazer alterações em elementos XAML com base em estados visuais definidos a partir do código._

O VSM (Visual State Manager) fornece uma maneira estruturada de fazer alterações visuais na interface do usuário a partir do código. Na maioria dos casos, a interface do usuário do aplicativo é definida em XAML, e este XAML inclui a marcação que descreve como o Gerenciador de estado Visual afeta os visuais da interface do usuário.

O VSM apresenta o conceito de _Estados visuais_. Uma exibição do Xamarin. Forms, como uma `Button` pode ter várias aparências visuais diferentes dependendo de seu estado subjacente &mdash; se ela está desabilitada ou pressionada ou se tem foco de entrada. Esses são os estados do botão.

Os Estados visuais são coletados em _grupos de Estados visuais_. Todos os estados visuais dentro de um grupo de estado visual são mutuamente exclusivos. Estados visuais e grupos de estado visual são identificados por cadeias de caracteres de texto simples.

O Gerenciador de estado visual do Xamarin. Forms define um grupo de estado visual chamado "CommonStates" com os seguintes estados visuais:

- "Normal"
- "Desabilitado"
- "Com foco"
- Selecione

Esse grupo de estado visual tem suporte para todas as classes que derivam de [`VisualElement`](xref:Xamarin.Forms.VisualElement), que é a classe base para [`View`](xref:Xamarin.Forms.View) e [`Page`](xref:Xamarin.Forms.Page).

Você também pode definir seus próprios grupos de estado visual e os estados visuais, como este artigo irá demonstrar.

> [!NOTE]
> Os desenvolvedores do Xamarin. Forms familiarizados com [gatilhos](~/xamarin-forms/app-fundamentals/triggers.md) estão cientes de que os gatilhos também podem fazer alterações em visuais na interface do usuário com base nas alterações nas propriedades de uma exibição ou no acionamento de eventos. No entanto, usando gatilhos para lidar com várias combinações dessas alterações pode se tornar bastante confuso. Historicamente, o Gerenciador de estado Visual foi introduzido em ambientes baseados em XAML do Windows para minimizar a confusão resultante de combinações de estados visuais. Os estados visuais dentro de um grupo de estado visual com o VSM, sempre são mutuamente exclusivos. A qualquer momento, apenas um estado em cada grupo é o estado atual.

## <a name="common-states"></a>Estados comuns

O Gerenciador de estado visual permite que você inclua marcação em seu arquivo XAML, que pode alterar a aparência visual de uma exibição se a exibição for normal ou desabilitada, ou tiver o foco de entrada. Eles são conhecidos como _Estados comuns_.

Por exemplo, suponha que você tenha uma exibição `Entry` em sua página e queira que a aparência visual da `Entry` seja alterada das seguintes maneiras:

- O `Entry` deve ter um plano de fundo rosa quando o `Entry` estiver desabilitado.
- O `Entry` deve ter um plano de fundo de verde-limão normalmente.
- A `Entry` deve expandir para duas vezes sua altura normal quando ela tem o foco de entrada.

Você pode anexar a marcação VSM a uma exibição individual, ou você pode defini-la em um estilo se apliquem a vários modos de exibição. As duas próximas seções descrevem essas abordagens.

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

Observe que a segunda `Entry` também tem uma `DataTrigger` como parte de sua coleção de `Trigger`. Isso faz com que o `Entry` seja desabilitado até que algo seja digitado na terceira `Entry`. Aqui está a página na inicialização em execução no iOS, Android e Universal Windows Platform (UWP):

[![VSM na exibição: desabilitado](vsm-images/VsmOnViewDisabled.png "VSM na exibição-desabilitado")](vsm-images/VsmOnViewDisabled-Large.png#lightbox)

O estado do visual atual é "desabilitado" para que o plano de fundo da segunda `Entry` seja rosa nas telas do iOS e do Android. A implementação do UWP de `Entry` não permite definir a cor do plano de fundo quando o `Entry` está desabilitado.

Quando você insere um texto na terceira `Entry`, a segunda `Entry` muda para o estado "normal" e o plano de fundo agora é verde-limão:

[![VSM na exibição: normal](vsm-images/VsmOnViewNormal.png "VSM na exibição-normal")](vsm-images/VsmOnViewNormal-Large.png#lightbox)

Quando você toca na segunda `Entry`, ele obtém o foco de entrada. Ele muda para o estado de "Focalizado" e se expande para duas vezes sua altura:

[![VSM na exibição: focado](vsm-images/VsmOnViewFocused.png "VSM no modo de exibição focado")](vsm-images/VsmOnViewFocused-Large.png#lightbox)

Observe que o `Entry` não retém o plano de fundo de verde-limão quando Obtém o foco de entrada. Como o Gerenciador de estado Visual alterna entre os estados visuais, as propriedades definidas pelo estado anterior são não definidas. Tenha em mente que os estados visuais são mutuamente exclusivos. O estado "normal" não significa apenas que o `Entry` está habilitado. Isso significa que a `Entry` está habilitada e não tem foco de entrada.

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

### <a name="visual-state-manager-markup-in-a-style"></a>Marcação de Gerenciador de estado visual em um estilo

Ele geralmente é necessário compartilhar a mesma marcação de Gerenciador de estado Visual entre dois ou mais exibições. Nesse caso, você desejará colocar a marcação em uma definição de `Style`.

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

O restante da marcação VSM é o mesmo de antes.

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

## <a name="visual-states-in-xamarinforms"></a>Estados visuais no Xamarin. Forms

A tabela a seguir lista os Estados visuais que são definidos no Xamarin. Forms:

| Classe | Estados | Mais informações |
| ----- | ------ | ---------------- |
| `Button` | `Pressed` | [Estados visuais de botão](~/xamarin-forms/user-interface/button.md#button-visual-states) |
| `CarouselView` | `DefaultItem`, `CurrentItem`, `PreviousItem`, `NextItem` | [Estados visuais do CarouselView](~/xamarin-forms/user-interface/carouselview/interaction.md#define-visual-states) |
| `ImageButton` | `Pressed` | [Estados visuais ImageButton](~/xamarin-forms/user-interface/imagebutton.md#imagebutton-visual-states) |
| `VisualElement` | `Normal`, `Disabled`, `Focused`, `Selected` | [Estados comuns](#common-states) |

Cada um desses Estados pode ser acessado por meio do grupo de estado visual chamado `CommonStates`.

Além disso, o `CollectionView` implementa o estado de `Selected`. Para obter mais informações, consulte [alterar a cor do item selecionado](~/xamarin-forms/user-interface/collectionview/selection.md#change-selected-item-color).

## <a name="set-state-on-multiple-elements"></a>Definir estado em vários elementos

Nos exemplos anteriores, os Estados visuais eram anexados e operados em elementos únicos. No entanto, também é possível criar estados visuais que são anexados a um único elemento, mas que definem propriedades em outros elementos dentro do mesmo escopo. Isso evita a repetição de Estados visuais em cada elemento em que os Estados operam.

O tipo de [`Setter`](xref:Xamarin.Forms.Setter) tem uma propriedade `TargetName`, do tipo `string`, que representa o elemento de destino que o `Setter` de um estado visual manipulará. Quando a propriedade `TargetName` é definida, a `Setter` define a `Property` do elemento definido em `TargetName` como `Value`:

```xaml
<Setter TargetName="label"
        Property="Label.TextColor"
        Value="Red" />
```

Neste exemplo, um `Label` nomeado `label` terá sua propriedade `TextColor` definida como `Red`. Ao definir a propriedade `TargetName`, você deve especificar o caminho completo para a propriedade em `Property`. Portanto, para definir a propriedade `TextColor` em uma `Label`, `Property` é especificado como `Label.TextColor`.

> [!NOTE]
> Qualquer propriedade referenciada por um objeto `Setter` deve ser apoiada por uma propriedade vinculável.

A página **VSM with setter TargetName** no exemplo **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** mostra como definir o estado em vários elementos, a partir de um único grupo de estado visual. O arquivo XAML consiste em um `StackLayout` que contém um elemento `Label`, um `Entry`e um `Button`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmSetterTargetNamePage"
             Title="VSM with Setter TargetName">
    <StackLayout Margin="10">
        <Label Text="What is the capital of France?" />
        <Entry x:Name="entry"
               Placeholder="Enter answer" />
        <Button Text="Reveal answer">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup x:Name="CommonStates">
                    <VisualState x:Name="Normal" />
                    <VisualState x:Name="Pressed">
                        <VisualState.Setters>
                            <Setter Property="Scale"
                                    Value="0.8" />
                            <Setter TargetName="entry"
                                    Property="Entry.Text"
                                    Value="Paris" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </Button>
    </StackLayout>
</ContentPage>
```

A marcação VSM é anexada à `StackLayout`. Há dois Estados mutuamente exclusivos, chamados "normal" e "pressionado", com cada Estado contendo marcas de `VisualState`.

O estado "normal" está ativo quando o `Button` não é pressionado e uma resposta à pergunta pode ser inserida:

[![VSM setter TargetName: estado normal](vsm-images/VsmSetterTargetNameNormal.png "VSM setter TargetName-normal")](vsm-images/VsmSetterTargetNameNormal-Large.png#lightbox)

O estado "pressionado" torna-se ativo quando o `Button` é pressionado:

[![VSM setter TargetName: estado pressionado](vsm-images/VsmSetterTargetNamePressed.png "VSM setter TargetName-pressionado")](vsm-images/VsmSetterTargetNamePressed-Large.png#lightbox)

O `VisualState` "pressionado" especifica que quando o `Button` for pressionado, sua propriedade `Scale` será alterada do valor padrão de 1 para 0,8. Além disso, o `Entry` nomeado `entry` terá sua propriedade `Text` definida como Paris. Portanto, o resultado é que quando a `Button` é pressionada, ela é redimensionada para ser ligeiramente menor e o `Entry` exibe Paris. Em seguida, quando o `Button` for liberado, ele será redimensionado para o valor padrão de 1 e o `Entry` exibirá qualquer texto inserido anteriormente.

> [!IMPORTANT]
> No momento, não há suporte para caminhos de propriedade em elementos `Setter` que especificam a propriedade `TargetName`.

## <a name="define-your-own-visual-states"></a>Definir seus próprios Estados visuais

Todas as classes que derivam de `VisualElement` dão suporte aos Estados comuns "normal", "com foco" e "desabilitado". Além disso, a classe `CollectionView` dá suporte ao estado "selecionado". Internamente, a classe [`VisualElement`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/VisualElement.cs) detecta quando está se tornando habilitada ou desabilitada, ou focada ou desfocada, e chama o método estático de [`VisualStateManager.GoToState`](xref:Xamarin.Forms.VisualStateManager.GoToState(Xamarin.Forms.VisualElement,System.String)) :

```csharp
VisualStateManager.GoToState(this, "Focused");
```

Esse é o único código do Gerenciador de estado visual que você encontrará na classe `VisualElement`. Como `GoToState` é chamado para cada objeto com base em todas as classes que derivam de `VisualElement`, você pode usar o Gerenciador de estado visual com qualquer objeto `VisualElement` para responder a essas alterações.

Curiosamente, o nome do grupo de estado visual "CommonStates" não é explicitamente referenciado em `VisualElement`. O nome do grupo não é parte da API para o Gerenciador de estado Visual. Dentro de um a dois programa de exemplo mostrado até agora, você pode alterar o nome do grupo de "CommonStates" para qualquer outra coisa, e o programa ainda funcionará. O nome do grupo é meramente uma descrição geral dos estados no grupo. É implicitamente entendido que os estados visuais em qualquer grupo são mutuamente exclusivos: uma e apenas um estado é atual a qualquer momento.

Se você quiser implementar seus próprios Estados visuais, precisará chamar `VisualStateManager.GoToState` do código. Geralmente, você fará essa chamada do arquivo code-behind da sua classe de página.

A página **validação do VSM** no exemplo de **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** mostra como usar o Gerenciador de estado visual em conexão com a validação de entrada. O arquivo XAML consiste em um `StackLayout` que contém dois elementos `Label`, um `Entry`e um `Button`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmValidationPage"
             Title="VSM Validation">
    <StackLayout x:Name="stackLayout"
                 Padding="10, 10">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ValidityStates">
                    <VisualState Name="Valid">
                        <VisualState.Setters>
                            <Setter TargetName="helpLabel"
                                    Property="Label.TextColor"
                                    Value="Transparent" />
                            <Setter TargetName="entry"
                                    Property="Entry.BackgroundColor"
                                    Value="Lime" />
                        </VisualState.Setters>
                    </VisualState>
                    <VisualState Name="Invalid">
                        <VisualState.Setters>
                            <Setter TargetName="entry"
                                    Property="Entry.BackgroundColor"
                                    Value="Pink" />
                            <Setter TargetName="submitButton"
                                    Property="Button.IsEnabled"
                                    Value="False" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        <Label Text="Enter a U.S. phone number:"
               FontSize="Large" />
        <Entry x:Name="entry"
               Placeholder="555-555-5555"
               FontSize="Large"
               Margin="30, 0, 0, 0"
               TextChanged="OnTextChanged" />
        <Label x:Name="helpLabel"
               Text="Phone number must be of the form 555-555-5555, and not begin with a 0 or 1" />
        <Button x:Name="submitButton"
                Text="Submit"
                FontSize="Large"
                Margin="0, 20"
                VerticalOptions="Center"
                HorizontalOptions="Center" />
    </StackLayout>
</ContentPage>
```

A marcação VSM é anexada à `StackLayout` (chamada `stackLayout`). Há dois Estados mutuamente exclusivos, denominados "válido" e "inválido", com cada Estado contendo marcas de `VisualState`.

Se o `Entry` não contiver um número de telefone válido, o estado atual será "inválido" e, portanto, o `Entry` terá um plano de fundo rosa, o segundo `Label` será visível e o `Button` será desabilitado:

[![Validação de VSM: estado inválido](vsm-images/VsmValidationInvalid.png "Validação de VSM-inválida")](vsm-images/VsmValidationInvalid-Large.png#lightbox)

Quando um número de telefone válido for inserido, em seguida, o estado atual se torna "Válido". O `Entry` Obtém um plano de fundo de verde-limão, o segundo `Label` desaparece e o `Button` agora está habilitado:

[![Validação de VSM: estado válido](vsm-images/VsmValidationValid.png "Validação de VSM-válida")](vsm-images/VsmValidationValid-Large.png#lightbox)

O arquivo code-behind é responsável por manipular o evento `TextChanged` da `Entry`. O manipulador usa uma expressão regular para determinar se a cadeia de caracteres de entrada é válida ou não. O método no arquivo code-behind chamado `GoToState` chama o método `VisualStateManager.GoToState` estático para `stackLayout`:

```csharp
public partial class VsmValidationPage : ContentPage
{
    public VsmValidationPage()
    {
        InitializeComponent();

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
        VisualStateManager.GoToState(stackLayout, visualState);
    }
}
```

Observe também que o método `GoToState` é chamado a partir do construtor para inicializar o estado. Sempre deve haver um estado atual. Mas não no código há qualquer referência ao nome do grupo de estados visuais, embora ela é referenciada no XAML como "ValidationStates" para fins de clareza.

Observe que o arquivo code-behind precisa apenas levar em conta o objeto na página que define os Estados visuais e chamar `VisualStateManager.GoToState` para esse objeto. Isso ocorre porque ambos os Estados visuais têm como destino vários objetos na página.

Você pode se perguntar: se o arquivo code-behind deve fazer referência ao objeto na página que define os Estados visuais, por que não é possível que o arquivo code-behind simplesmente acesse esse e outros objetos diretamente? Obviamente, foi possível. No entanto, a vantagem de usar o VSM é que você pode controlar os elementos visuais como reagir a estado diferente inteiramente em XAML, que mantém todos o design de interface do usuário em um único local. Isso evita a aparência visual de configuração por meio de acessar elementos visuais diretamente no code-behind.

## <a name="visual-state-triggers"></a>Gatilhos de estado visual

Os Estados visuais dão suporte a gatilhos de estado, que são um grupo especializado de gatilhos que definem as condições sob as quais um [`VisualState`](xref:Xamarin.Forms.VisualState) deve ser aplicado.

Os gatilhos de estado são adicionados à coleção de [`StateTriggers`](xref:Xamarin.Forms.VisualState.StateTriggers) de um [`VisualState`](xref:Xamarin.Forms.VisualState). Essa coleção pode conter um único gatilho de estado ou vários gatilhos de estado. Um [`VisualState`](xref:Xamarin.Forms.VisualState) será aplicado quando qualquer gatilho de estado na coleção estiver ativo.

Ao usar gatilhos de estado para controlar os Estados visuais, o Xamarin. Forms usa as seguintes regras de precedência para determinar qual gatilho (e [`VisualState`](xref:Xamarin.Forms.VisualState)correspondente) estará ativo:

1. Qualquer gatilho derivado de [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase).
1. Um [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) ativado devido à condição de [`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowWidth) ser atendida.
1. Um [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) ativado devido à condição de [`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight) ser atendida.

Se vários gatilhos estiverem ativos simultaneamente (por exemplo, dois gatilhos personalizados), o primeiro gatilho declarado na marcação terá precedência.

Para obter mais informações sobre gatilhos de estado, consulte [gatilhos de estado](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers).

<a name="adaptive-layout" />

## <a name="use-the-visual-state-manager-for-adaptive-layout"></a>Usar o Gerenciador de estado visual para layout adaptável

Um xamarin. Forms aplicativo em execução em um telefone geralmente pode ser exibido em um retrato ou paisagem taxa de proporção e um programa de xamarin. Forms em execução na área de trabalho pode ser redimensionada para assumir muitos tamanhos diferentes e taxas de proporção. Um aplicativo bem projetado pode exibir seu conteúdo diferentes para esses vários fatores de formulário de página ou janela.

Essa técnica às vezes é conhecida como _layout adaptável_. Como layout adaptável envolve apenas elementos visuais de um programa, ele é um aplicativo ideal do Gerenciador de estado Visual.

Um exemplo simples é um aplicativo que exibe uma pequena coleção de botões que afetam o conteúdo do aplicativo. No modo retrato, esses botões podem ser exibidos em uma linha horizontal na parte superior da página:

[![Layout adaptável do VSM: retrato](vsm-images/VsmAdaptiveLayoutPortrait.png "Layout adaptável do VSM-retrato")](vsm-images/VsmAdaptiveLayoutPortrait-Large.png#lightbox)

No modo paisagem, a matriz de botões pode ser movida para um lado e exibida em uma coluna:

[![Layout adaptável do VSM: paisagem](vsm-images/VsmAdaptiveLayoutLandscape.png "Layout adaptável do VSM-paisagem")](vsm-images/VsmAdaptiveLayoutLandscape-Large.png#lightbox)

De cima para baixo, o programa está em execução a plataforma Universal do Windows, Android e iOS.

A página de **layout adaptável do VSM** no exemplo de [VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos) define um grupo chamado "OrientationStates" com dois estados visuais chamados "retrato" e "paisagem". (Uma abordagem mais complexa pode ser baseada em várias larguras de página ou janela diferentes).

Marcação VSM ocorre em quatro lugares no arquivo XAML. O `StackLayout` nomeado `mainStack` contém o menu e o conteúdo, que é um elemento `Image`. Este `StackLayout` deve ter uma orientação vertical no modo retrato e uma orientação horizontal no modo paisagem:

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

A quarta seção de marcação VSM está em um estilo implícito para os botões em si. Essa marcação define as propriedades `VerticalOptions`, `HorizontalOptions`e `Margin` específicas para as orientações retrato e paisagem.

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

Pode parecer como se o arquivo code-behind pode lidar com alterações de orientação mais diretamente, definindo as propriedades dos elementos no arquivo XAML, mas o Gerenciador de estado Visual é definitivamente uma abordagem mais estruturada. Todos os elementos visuais são mantidos no arquivo XAML, onde eles se tornam mais fácil de examinar, manutenção e modificar.

## <a name="visual-state-manager-with-xamarinuniversity"></a>Gerenciador de estado visual com Xamarin.University

> [!VIDEO https://youtube.com/embed/qhUHbVP5mIQ]

**Vídeo do Gerenciador de estado visual do Xamarin. Forms 3,0**

## <a name="related-links"></a>Links relacionados

- [VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)
- [Gatilhos de estado](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers)
