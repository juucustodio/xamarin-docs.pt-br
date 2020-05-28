---
title: Xamarin.FormsGerenciador de estado visual
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
ms.custom: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 13dd0a3f5d665e2232e7e6e12edac7cf117dd0ca
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84127277"
---
# <a name="xamarinforms-visual-state-manager"></a>Xamarin.FormsGerenciador de estado visual

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)

_Use o Gerenciador de estado visual para fazer alterações em elementos XAML com base em estados visuais definidos a partir do código._

O VSM (Visual State Manager) fornece uma maneira estruturada de fazer alterações visuais na interface do usuário a partir do código. Na maioria dos casos, a interface do usuário do aplicativo é definida em XAML, e esse XAML inclui marcação que descreve como o Gerenciador de estado visual afeta os elementos visuais da interface do usuário.

O VSM apresenta o conceito de _Estados visuais_. Uma Xamarin.Forms exibição como uma `Button` pode ter várias aparências visuais diferentes dependendo de seu estado subjacente &mdash; , seja ela desabilitada ou pressionada ou com foco de entrada. Esses são os Estados do botão.

Os Estados visuais são coletados em _grupos de Estados visuais_. Todos os Estados visuais dentro de um grupo de estado visual são mutuamente exclusivos. Os Estados visuais e os grupos de Estados visuais são identificados por cadeias de caracteres de texto simples.

O Xamarin.Forms Gerenciador de estado visual define um grupo de estado visual chamado "CommonStates" com os seguintes estados visuais:

- "Normal"
- Desabilitado
- Foco
- Selecione

Esse grupo de estado visual tem suporte para todas as classes que derivam de [`VisualElement`](xref:Xamarin.Forms.VisualElement) , que é a classe base para [`View`](xref:Xamarin.Forms.View) e [`Page`](xref:Xamarin.Forms.Page) .

Você também pode definir seus próprios grupos de estado visual e Estados visuais, como este artigo demonstrará.

> [!NOTE]
> Xamarin.Formsos desenvolvedores familiarizados com [gatilhos](~/xamarin-forms/app-fundamentals/triggers.md) estão cientes de que os gatilhos também podem fazer alterações em visuais na interface do usuário com base nas alterações nas propriedades de uma exibição ou no acionamento de eventos. No entanto, o uso de gatilhos para lidar com várias combinações dessas alterações pode se tornar bastante confuso. Historicamente, o Visual State Manager foi introduzido em ambientes baseados em XAML do Windows para aliviar a confusão resultante de combinações de Estados visuais. Com o VSM, os Estados visuais dentro de um grupo de estado visual são sempre mutuamente exclusivos. A qualquer momento, apenas um estado em cada grupo é o estado atual.

## <a name="common-states"></a>Estados comuns

O Gerenciador de estado visual permite que você inclua marcação em seu arquivo XAML, que pode alterar a aparência visual de uma exibição se a exibição for normal ou desabilitada, ou tiver o foco de entrada. Eles são conhecidos como _Estados comuns_.

Por exemplo, suponha que você tenha uma `Entry` exibição em sua página e queira que a aparência visual do `Entry` seja alterada das seguintes maneiras:

- O `Entry` deve ter um plano de fundo rosa quando o `Entry` estiver desabilitado.
- O `Entry` deve ter um plano de fundo de verde-limão normalmente.
- O `Entry` deve expandir para duas vezes sua altura normal quando tiver o foco de entrada.

Você pode anexar a marcação VSM a uma exibição individual ou pode defini-la em um estilo se ela se aplicar a várias exibições. As próximas duas seções descrevem essas abordagens.

### <a name="vsm-markup-on-a-view"></a>Marcação VSM em uma exibição

Para anexar a marcação VSM a uma `Entry` exibição, primeiro separe as `Entry` marcas de início e término:

```xaml
<Entry FontSize="18">

</Entry>
```

Ele recebe um tamanho de fonte explícito, pois um dos Estados usará a `FontSize` propriedade para dobrar o tamanho do texto no `Entry` .

Em seguida, insira `VisualStateManager.VisualStateGroups` as marcas entre essas marcas:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>

    </VisualStateManager.VisualStateGroups>
</Entry>
```

[`VisualStateGroups`](xref:Xamarin.Forms.VisualStateManager.VisualStateGroupsProperty)é uma propriedade vinculável associada definida pela [`VisualStateManager`](xref:Xamarin.Forms.VisualStateManager) classe. (Para obter mais informações sobre as propriedades vinculáveis anexadas, consulte o artigo [Propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md).) É assim que a `VisualStateGroups` propriedade é anexada ao `Entry` objeto.

A `VisualStateGroups` propriedade é do tipo [`VisualStateGroupList`](xref:Xamarin.Forms.VisualStateGroupList) , que é uma coleção de [`VisualStateGroup`](xref:Xamarin.Forms.VisualStateGroup) objetos. Dentro das `VisualStateManager.VisualStateGroups` marcas, insira um par de `VisualStateGroup` marcas para cada grupo de Estados visuais que você deseja incluir:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

Observe que a `VisualStateGroup` marca tem um `x:Name` atributo que indica o nome do grupo. A `VisualStateGroup` classe define uma `Name` propriedade que você pode usar em vez disso:

```xaml
<VisualStateGroup Name="CommonStates">
```

Você pode usar o `x:Name` ou `Name` o, mas não ambos, no mesmo elemento.

A `VisualStateGroup` classe define uma propriedade chamada [`States`](xref:Xamarin.Forms.VisualStateGroup.States) , que é uma coleção de [`VisualState`](xref:Xamarin.Forms.VisualState) objetos. `States`é a _Propriedade Content_ de `VisualStateGroups` , portanto, você pode incluir as `VisualState` marcas diretamente entre as `VisualStateGroup` marcas. (As propriedades de conteúdo são discutidas no artigo [sintaxe XAML essencial](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md#content-properties).)

A próxima etapa é incluir um par de marcas para cada estado visual nesse grupo. Elas também podem ser identificadas usando `x:Name` ou `Name` :

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

`VisualState`define uma propriedade chamada [`Setters`](xref:Xamarin.Forms.VisualState.Setters) , que é uma coleção de [`Setter`](xref:Xamarin.Forms.Setter) objetos. Esses são os mesmos `Setter` objetos que você usa em um [`Style`](xref:Xamarin.Forms.Style) objeto.

`Setters`_não_ é a propriedade de conteúdo de `VisualState` , portanto, é necessário incluir marcas de elemento de propriedade para a `Setters` Propriedade:

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

Agora você pode inserir um ou mais `Setter` objetos entre cada par de `Setters` marcas. Estes são os `Setter` objetos que definem os Estados visuais descritos anteriormente:

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

Cada `Setter` marca indica o valor de uma propriedade específica quando esse estado é atual. Qualquer propriedade referenciada por um `Setter` objeto deve ser apoiada por uma propriedade vinculável.

A marcação semelhante a esta é a base da página **VSM na exibição** no programa de exemplo **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** . A página inclui três `Entry` exibições, mas apenas a segunda tem a marcação VSM anexada a ela:

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

Observe que o segundo `Entry` também tem um `DataTrigger` como parte de sua `Trigger` coleção. Isso faz com que o seja `Entry` desabilitado até que algo seja digitado na terceira `Entry` . Aqui está a página na inicialização em execução no iOS, no Android e no Plataforma Universal do Windows (UWP):

[![VSM na exibição: desabilitado](vsm-images/VsmOnViewDisabled.png "VSM na exibição-desabilitado")](vsm-images/VsmOnViewDisabled-Large.png#lightbox)

O estado do visual atual é "desabilitado" para que o plano de fundo da segunda `Entry` seja rosa nas telas do IOS e do Android. A implementação de UWP do `Entry` não permite definir a cor do plano de fundo quando o `Entry` está desabilitado.

Quando você insere um texto na terceira `Entry` , o segundo `Entry` alterna para o estado "normal" e o plano de fundo agora é verde-limão:

[![VSM na exibição: normal](vsm-images/VsmOnViewNormal.png "VSM na exibição-normal")](vsm-images/VsmOnViewNormal-Large.png#lightbox)

Quando você toca no segundo `Entry` , ele obtém o foco de entrada. Ele alterna para o estado "focado" e expande para duas vezes sua altura:

[![VSM na exibição: focado](vsm-images/VsmOnViewFocused.png "VSM no modo de exibição focado")](vsm-images/VsmOnViewFocused-Large.png#lightbox)

Observe que o `Entry` não retém o plano de fundo de verde-limão quando Obtém o foco de entrada. Como o Gerenciador de estado visual alterna entre os Estados visuais, as propriedades definidas pelo estado anterior são desdefinidas. Tenha em mente que os Estados visuais são mutuamente exclusivos. O estado "normal" não significa apenas que o `Entry` está habilitado. Isso significa que o `Entry` está habilitado e não tem foco de entrada.

Se você quiser que o `Entry` tenha um plano de fundo de verde-limão no estado "focado", adicione outro `Setter` ao estado do Visual:

```xaml
<VisualState x:Name="Focused">
    <VisualState.Setters>
        <Setter Property="FontSize" Value="36" />
        <Setter Property="BackgroundColor" Value="Lime" />
    </VisualState.Setters>
</VisualState>
```

Para `Setter` que esses objetos funcionem corretamente, um `VisualStateGroup` deve conter `VisualState` objetos para todos os Estados nesse grupo. Se houver um estado visual que não tenha nenhum `Setter` objeto, inclua-o assim mesmo como uma marca vazia:

```xaml
<VisualState x:Name="Normal" />
```

### <a name="visual-state-manager-markup-in-a-style"></a>Marcação do Gerenciador de estado visual em um estilo

Geralmente, é necessário compartilhar a mesma marcação do Gerenciador de estado visual entre duas ou mais exibições. Nesse caso, você desejará colocar a marcação em uma `Style` definição.

Aqui está o implícito existente `Style` para os `Entry` elementos na página **VSM na exibição** :

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
</Style>
```

Adicione `Setter` marcas para a `VisualStateManager.VisualStateGroups` propriedade vinculável anexada:

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">

    </Setter>
</Style>
```

A propriedade de conteúdo para o `Setter` é `Value` , portanto, o valor da `Value` propriedade pode ser especificado diretamente dentro dessas marcas. Essa propriedade é do tipo `VisualStateGroupList` :

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

Agora, todas as `Entry` exibições nesta página respondem da mesma maneira aos seus Estados visuais. Observe também que o estado "focado" agora inclui um segundo `Setter` que fornece cada `Entry` plano de fundo de verde-limão também quando tem foco de entrada:

[![VSM em estilo](vsm-images/VsmInStyle.png "VSM em estilo")](vsm-images/VsmInStyle-Large.png#lightbox)

## <a name="visual-states-in-xamarinforms"></a>Estados visuais noXamarin.Forms

A tabela a seguir lista os Estados visuais que são definidos em Xamarin.Forms :

| Classe | Estados | Mais informações |
| ----- | ---
Título: ' Xamarin.Forms Gerenciador de estado visual ' Descrição: MS. Prod: MS. AssetID: MS. Technology: MS. Custom: autor: MS. Author: MS. Date: no-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

--- | título do---: ' Xamarin.Forms Gerenciador de estado visual ' Descrição: MS. Prod: MS. AssetID: MS. Technology: MS. Custom: autor: MS. Author: MS. Date: no-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
Título: ' Xamarin.Forms Gerenciador de estado visual ' Descrição: MS. Prod: MS. AssetID: MS. Technology: MS. Custom: autor: MS. Author: MS. Date: no-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
Título: ' Xamarin.Forms Gerenciador de estado visual ' Descrição: MS. Prod: MS. AssetID: MS. Technology: MS. Custom: autor: MS. Author: MS. Date: no-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
Título: ' Xamarin.Forms Gerenciador de estado visual ' Descrição: MS. Prod: MS. AssetID: MS. Technology: MS. Custom: autor: MS. Author: MS. Date: no-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
Título: ' Xamarin.Forms Gerenciador de estado visual ' Descrição: MS. Prod: MS. AssetID: MS. Technology: MS. Custom: autor: MS. Author: MS. Date: no-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
Título: ' Xamarin.Forms Gerenciador de estado visual ' Descrição: MS. Prod: MS. AssetID: MS. Technology: MS. Custom: autor: MS. Author: MS. Date: no-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-------- | | `Button` | `Pressed` |  [Estados visuais de botão](~/xamarin-forms/user-interface/button.md#button-visual-states) | | `CheckBox` | `IsChecked` |  [Estados visuais da caixa de seleção](~/xamarin-forms/user-interface/checkbox.md#checkbox-visual-states) | | `CarouselView`  |  `DefaultItem` , `CurrentItem` , `PreviousItem` , `NextItem`  |  [Estados visuais CarouselView](~/xamarin-forms/user-interface/carouselview/interaction.md#define-visual-states) | `ImageButton` |  |  `Pressed`  |  [Estados visuais de ImageButton](~/xamarin-forms/user-interface/imagebutton.md#imagebutton-visual-states) | | `RadioButton` | `IsChecked` |  [Estados visuais de RadioButton](~/xamarin-forms/user-interface/radiobutton.md#radiobutton-visual-states) | | `VisualElement`  |  `Normal` , `Disabled` , `Focused` , `Selected`  |  [Estados comuns](#common-states) |

Cada um desses Estados pode ser acessado por meio do grupo de Estados visuais denominado `CommonStates` .

Além disso, o `CollectionView` implementa o `Selected` estado. Para obter mais informações, consulte [alterar a cor do item selecionado](~/xamarin-forms/user-interface/collectionview/selection.md#change-selected-item-color).

## <a name="set-state-on-multiple-elements"></a>Definir estado em vários elementos

Nos exemplos anteriores, os Estados visuais eram anexados e operados em elementos únicos. No entanto, também é possível criar estados visuais que são anexados a um único elemento, mas que definem propriedades em outros elementos dentro do mesmo escopo. Isso evita a repetição de Estados visuais em cada elemento em que os Estados operam.

O [`Setter`](xref:Xamarin.Forms.Setter) tipo tem uma `TargetName` propriedade, do tipo `string` , que representa o elemento de destino que o `Setter` para um estado visual manipulará. Quando a `TargetName` propriedade é definida, o `Setter` define o `Property` do elemento definido em `TargetName` como `Value` :

```xaml
<Setter TargetName="label"
        Property="Label.TextColor"
        Value="Red" />
```

Neste exemplo, um `Label` nome terá `label` sua `TextColor` propriedade definida como `Red` . Ao definir a `TargetName` propriedade, você deve especificar o caminho completo para a propriedade em `Property` . Portanto, para definir a `TextColor` propriedade em um `Label` , `Property` é especificado como `Label.TextColor` .

> [!NOTE]
> Qualquer propriedade referenciada por um `Setter` objeto deve ser apoiada por uma propriedade vinculável.

A página **VSM with setter TargetName** no exemplo **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** mostra como definir o estado em vários elementos, a partir de um único grupo de estado visual. O arquivo XAML consiste em uma `StackLayout` contendo um `Label` elemento, um `Entry` e um `Button` :

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

A marcação VSM está anexada ao `StackLayout` . Há dois Estados mutuamente exclusivos, chamados "normal" e "pressionado", com cada Estado contendo `VisualState` marcas.

O estado "normal" está ativo quando o `Button` não é pressionado e uma resposta à pergunta pode ser inserida:

[![VSM setter TargetName: estado normal](vsm-images/VsmSetterTargetNameNormal.png "VSM setter TargetName-normal")](vsm-images/VsmSetterTargetNameNormal-Large.png#lightbox)

O estado "pressionado" torna-se ativo quando o `Button` é pressionado:

[![VSM setter TargetName: estado pressionado](vsm-images/VsmSetterTargetNamePressed.png "VSM setter TargetName-pressionado")](vsm-images/VsmSetterTargetNamePressed-Large.png#lightbox)

O "pressionado" `VisualState` especifica que quando o `Button` for pressionado, sua `Scale` propriedade será alterada do valor padrão de 1 para 0,8. Além disso, o `Entry` nome terá `entry` sua `Text` propriedade definida como Paris. Portanto, o resultado é que quando o `Button` é pressionado, ele é redimensionado para ser ligeiramente menor e o `Entry` exibe Paris. Em seguida, quando o `Button` é liberado, ele é redimensionado para o valor padrão de 1 e `Entry` exibe qualquer texto inserido anteriormente.

> [!IMPORTANT]
> No momento, não há suporte para caminhos de propriedade em `Setter` elementos que especificam a `TargetName` propriedade.

## <a name="define-your-own-visual-states"></a>Definir seus próprios Estados visuais

Cada classe derivada de `VisualElement` dá suporte aos Estados comuns "normal", "focado" e "desabilitado". Além disso, a `CollectionView` classe dá suporte ao estado "selecionado". Internamente, a [`VisualElement`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/VisualElement.cs) classe detecta quando está se tornando habilitada ou desabilitada, ou focada ou desfocada, e chama o [] estático [ `VisualStateManager.GoToState` ] (xref: Xamarin.Forms . VisualStateManager. GoTostate ( Xamarin.Forms . Método visualelement, System. String):

```csharp
VisualStateManager.GoToState(this, "Focused");
```

Esse é o único código do Gerenciador de estado visual que você encontrará na `VisualElement` classe. Como `GoToState` o é chamado para cada objeto com base em cada classe derivada de `VisualElement` , você pode usar o Gerenciador de estado visual com qualquer `VisualElement` objeto para responder a essas alterações.

Curiosamente, o nome do grupo de estado visual "CommonStates" não é explicitamente referenciado em `VisualElement` . O nome do grupo não faz parte da API para o Gerenciador de estado visual. Em um dos dois programas de exemplo mostrados até agora, você pode alterar o nome do grupo de "CommonStates" para qualquer outra coisa e o programa continuará funcionando. O nome do grupo é meramente uma descrição geral dos Estados nesse grupo. Ele é compreendido implicitamente que os Estados visuais em qualquer grupo são mutuamente exclusivos: um estado e apenas um estado é atual a qualquer momento.

Se você quiser implementar seus próprios Estados visuais, precisará chamar a `VisualStateManager.GoToState` partir do código. Geralmente, você fará essa chamada a partir do arquivo code-behind da sua classe Page.

A página **validação do VSM** no exemplo de **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** mostra como usar o Gerenciador de estado visual em conexão com a validação de entrada. O arquivo XAML consiste em um `StackLayout` contendo dois `Label` elementos, um `Entry` e um `Button` :

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

A marcação VSM está anexada ao `StackLayout` (nomeado `stackLayout` ). Há dois Estados mutuamente exclusivos, denominados "válido" e "inválido", com cada Estado contendo `VisualState` marcas.

Se o `Entry` não contiver um número de telefone válido, o estado atual será "inválido" e, portanto, o `Entry` terá um plano de fundo rosa, o segundo `Label` será visível e o `Button` será desabilitado:

[![Validação de VSM: estado inválido](vsm-images/VsmValidationInvalid.png "Validação de VSM-inválida")](vsm-images/VsmValidationInvalid-Large.png#lightbox)

Quando um número de telefone válido é inserido, o estado atual se torna "válido". O `Entry` Obtém um plano de fundo de verde-limão, o segundo `Label` desaparece e o `Button` agora está habilitado:

[![Validação de VSM: estado válido](vsm-images/VsmValidationValid.png "Validação de VSM-válida")](vsm-images/VsmValidationValid-Large.png#lightbox)

O arquivo code-behind é responsável por manipular o `TextChanged` evento do `Entry` . O manipulador usa uma expressão regular para determinar se a cadeia de caracteres de entrada é válida ou não. O método no arquivo code-behind chamado `GoToState` chama o método estático `VisualStateManager.GoToState` para `stackLayout` :

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

Observe também que o `GoToState` método é chamado a partir do construtor para inicializar o estado. Sempre deve haver um estado atual. Mas em qualquer lugar no código há qualquer referência ao nome do grupo de estado visual, embora ele seja referenciado no XAML como "ValidationStates" para fins de clareza.

Observe que o arquivo code-behind precisa apenas assumir a conta do objeto na página que define os Estados visuais e chamar `VisualStateManager.GoToState` esse objeto. Isso ocorre porque ambos os Estados visuais têm como destino vários objetos na página.

Você pode se perguntar: se o arquivo code-behind deve fazer referência ao objeto na página que define os Estados visuais, por que não é possível que o arquivo code-behind simplesmente acesse esse e outros objetos diretamente? Certamente poderia. No entanto, a vantagem de usar o VSM é que você pode controlar como os elementos visuais reagem para um estado diferente inteiramente no XAML, o que mantém todo o design da interface do usuário em um único local. Isso evita a configuração da aparência visual acessando elementos visuais diretamente do code-behind.

## <a name="visual-state-triggers"></a>Gatilhos de estado visual

Os Estados visuais dão suporte a gatilhos de estado, que são um grupo especializado de gatilhos que definem as condições sob as quais um [`VisualState`](xref:Xamarin.Forms.VisualState) deve ser aplicado.

Os gatilhos de estado são adicionados à [`StateTriggers`](xref:Xamarin.Forms.VisualState.StateTriggers) coleção de um [`VisualState`](xref:Xamarin.Forms.VisualState) . Essa coleção pode conter um único gatilho de estado ou vários gatilhos de estado. Um [`VisualState`](xref:Xamarin.Forms.VisualState) será aplicado quando qualquer gatilho de estado na coleção estiver ativo.

Ao usar gatilhos de estado para controlar os Estados visuais, Xamarin.Forms o usa as seguintes regras de precedência para determinar qual gatilho (e correspondente [`VisualState`](xref:Xamarin.Forms.VisualState) ) estará ativo:

1. Qualquer gatilho derivado de [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) .
1. Um [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) ativado devido à [`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowWidth) condição que está sendo atendida.
1. Um [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) ativado devido à [`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight) condição que está sendo atendida.

Se vários gatilhos estiverem ativos simultaneamente (por exemplo, dois gatilhos personalizados), o primeiro gatilho declarado na marcação terá precedência.

Para obter mais informações sobre gatilhos de estado, consulte [gatilhos de estado](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers).

<a name="adaptive-layout" />

## <a name="use-the-visual-state-manager-for-adaptive-layout"></a>Usar o Gerenciador de estado visual para layout adaptável

Um Xamarin.Forms aplicativo em execução em um telefone geralmente pode ser exibido em uma taxa de proporção retrato ou paisagem, e um Xamarin.Forms programa em execução na área de trabalho pode ser redimensionado para assumir muitos tamanhos e taxas de proporção diferentes. Um aplicativo bem projetado pode exibir seu conteúdo de forma diferente para esses vários fatores forma de página ou janela.

Essa técnica às vezes é conhecida como _layout adaptável_. Como o layout adaptável apenas envolve os visuais de um programa, ele é um aplicativo ideal do Gerenciador de estado visual.

Um exemplo simples é um aplicativo que exibe uma pequena coleção de botões que afetam o conteúdo do aplicativo. No modo retrato, esses botões podem ser exibidos em uma linha horizontal na parte superior da página:

[![Layout adaptável do VSM: retrato](vsm-images/VsmAdaptiveLayoutPortrait.png "Layout adaptável do VSM-retrato")](vsm-images/VsmAdaptiveLayoutPortrait-Large.png#lightbox)

No modo paisagem, a matriz de botões pode ser movida para um lado e exibida em uma coluna:

[![Layout adaptável do VSM: paisagem](vsm-images/VsmAdaptiveLayoutLandscape.png "Layout adaptável do VSM-paisagem")](vsm-images/VsmAdaptiveLayoutLandscape-Large.png#lightbox)

De cima para baixo, o programa está em execução no Plataforma Universal do Windows, no Android e no iOS.

A página de **layout adaptável do VSM** no exemplo de [VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos) define um grupo chamado "OrientationStates" com dois estados visuais chamados "retrato" e "paisagem". (Uma abordagem mais complexa pode ser baseada em várias larguras de página ou janela diferentes.)

A marcação VSM ocorre em quatro locais no arquivo XAML. O `StackLayout` nome `mainStack` contém o menu e o conteúdo, que é um `Image` elemento. Isso `StackLayout` deve ter uma orientação vertical no modo retrato e uma orientação horizontal no modo paisagem:

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

O `ScrollView` nome interno `menuScroll` e o `StackLayout` nomeado `menuStack` implementam o menu de botões. A orientação desses layouts é oposta a `mainStack` . O menu deve ser horizontal no modo retrato e vertical no modo paisagem.

A quarta seção da marcação VSM está em um estilo implícito para os próprios botões. Essa marcação define `VerticalOptions` , `HorizontalOptions` e `Margin` Propriedades específicas para as orientações retrato e paisagem.

O arquivo code-behind define a `BindingContext` propriedade de `menuStack` para implementar o `Button` comando e também anexa um manipulador ao `SizeChanged` evento da página:

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

O `SizeChanged` manipulador chama `VisualStateManager.GoToState` os dois `StackLayout` elementos e `ScrollView` , em seguida, percorre os filhos de `menuStack` para chamar `VisualStateManager.GoToState` os `Button` elementos.

Pode parecer que o arquivo code-behind pode tratar as alterações de orientação mais diretamente definindo as propriedades de elementos no arquivo XAML, mas o Gerenciador de estado visual é definitivamente uma abordagem mais estruturada. Todos os visuais são mantidos no arquivo XAML, onde se tornam mais fáceis de examinar, manter e modificar.

## <a name="visual-state-manager-with-xamarinuniversity"></a>Gerenciador de estado visual com Xamarin. University

> [!VIDEO https://youtube.com/embed/qhUHbVP5mIQ]

**Xamarin.Formsvídeo do Gerenciador de estado visual 3,0**

## <a name="related-links"></a>Links relacionados

- [VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)
- [Gatilhos de estado](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers)
