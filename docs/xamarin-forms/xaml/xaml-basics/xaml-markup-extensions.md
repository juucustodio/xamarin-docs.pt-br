---
title: Parte 3. Extensões de marcação do XAML
description: As extensões de marcação XAML constituem um recurso importante em XAML que permite que as propriedades sejam definidas para objetos ou valores que são referenciados indiretamente de outras fontes.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F4A37564-B18B-42FF-B841-9A1949895AB6
author: davidbritch
ms.author: dabritch
ms.date: 03/27/2018
ms.openlocfilehash: 1321f08cba4bf4cf23759ebb179a603b544ffc2e
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696648"
---
# <a name="part-3-xaml-markup-extensions"></a>Parte 3. Extensões de marcação do XAML

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_As extensões de marcação XAML constituem um recurso importante em XAML que permite que as propriedades sejam definidas para objetos ou valores que são referenciados indiretamente de outras fontes. As extensões de marcação XAML são particularmente importantes para compartilhar objetos e referenciar constantes usadas em um aplicativo, mas elas encontram seu maior utilitário em associações de dados._

## <a name="xaml-markup-extensions"></a>Extensões de marcação do XAML

Em geral, você usa XAML para definir propriedades de um objeto para valores explícitos, como uma cadeia de caracteres, um número, um membro de enumeração ou uma cadeia de caracteres que é convertida em um valor nos bastidores.

Às vezes, no entanto, as propriedades devem referenciar valores definidos em outro lugar ou que podem exigir um pequeno processamento por código em tempo de execução. Para essas finalidades, *as extensões de marcação* XAML estão disponíveis.

Essas extensões de marcação XAML não são extensões de XML. O XAML é totalmente legal XML. Elas são chamadas de "extensões" porque são apoiadas por código em classes que implementam `IMarkupExtension`. Você pode escrever suas próprias extensões de marcação personalizadas.

Em muitos casos, as extensões de marcação XAML são reconhecíveis instantaneamente em arquivos XAML porque aparecem como configurações de atributo delimitadas por chaves: {e}, mas às vezes as extensões de marcação aparecem na marcação como elementos convencionais.

## <a name="shared-resources"></a>Recursos compartilhados

Algumas páginas XAML contêm várias exibições com propriedades definidas com os mesmos valores. Por exemplo, muitas das configurações de propriedade para esses `Button` objetos são as mesmas:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <StackLayout>
        <Button Text="Do this!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="24" />

        <Button Text="Do that!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="24" />

        <Button Text="Do the other thing!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="24" />

    </StackLayout>
</ContentPage>
```

Se uma dessas propriedades precisar ser alterada, talvez você prefira fazer a alteração apenas uma vez, em vez de três vezes. Se esse fosse o código, você provavelmente estaria usando constantes e objetos somente leitura estáticos para ajudar a manter esses valores consistentes e fáceis de modificar.

Em XAML, uma solução popular é armazenar esses valores ou objetos em um *dicionário de recursos*. A classe `VisualElement` define uma propriedade chamada `Resources` do tipo `ResourceDictionary`, que é um dicionário com chaves do tipo `string` e valores do tipo `object`. Você pode colocar objetos nesse dicionário e, em seguida, referenciá-los de marcação, tudo em XAML.

Para usar um dicionário de recursos em uma página, inclua um par de `Resources` marcações de elemento de propriedade. É mais conveniente colocá-los na parte superior da página:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>

    </ContentPage.Resources>
    ...
</ContentPage>
```

Também é necessário incluir explicitamente `ResourceDictionary` marcas:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>
        <ResourceDictionary>

        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Agora, os objetos e valores de vários tipos podem ser adicionados ao dicionário de recursos. Esses tipos devem ser instanciáveis. Eles não podem ser classes abstratas, por exemplo. Esses tipos também devem ter um construtor público sem parâmetros. Cada item requer uma chave de dicionário especificada com o atributo `x:Key`. Por exemplo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>
        <ResourceDictionary>
            <LayoutOptions x:Key="horzOptions"
                           Alignment="Center" />

            <LayoutOptions x:Key="vertOptions"
                           Alignment="Center"
                           Expands="True" />
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Esses dois itens são valores do tipo de estrutura `LayoutOptions`, e cada um tem uma chave exclusiva e uma ou duas propriedades definidas. No código e na marcação, é muito mais comum usar os campos estáticos de `LayoutOptions`, mas aqui é mais conveniente definir as propriedades.

Agora é necessário definir as propriedades `HorizontalOptions` e `VerticalOptions` desses botões para esses recursos, e isso é feito com a extensão de marcação XAML `StaticResource`:

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="3"
        Rotation="-15"
        TextColor="Red"
        FontSize="24" />
```

A extensão de marcação de `StaticResource` sempre é delimitada por chaves e inclui a chave de dicionário.

O nome `StaticResource` o distingue de `DynamicResource`, a qual o Xamarin. Forms também dá suporte. `DynamicResource` é para chaves de dicionário associadas a valores que podem ser alterados durante o tempo de execução, enquanto `StaticResource` acessa elementos do dicionário apenas uma vez quando os elementos na página são construídos.

Para a propriedade `BorderWidth`, é necessário armazenar um duplo no dicionário. O XAML, convenientemente, define marcas para tipos de dados comuns como `x:Double` e `x:Int32`:

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <LayoutOptions x:Key="horzOptions"
                       Alignment="Center" />

        <LayoutOptions x:Key="vertOptions"
                       Alignment="Center"
                       Expands="True" />

        <x:Double x:Key="borderWidth">
            3
        </x:Double>
    </ResourceDictionary>
</ContentPage.Resources>
```

Você não precisa colocá-lo em três linhas. Esta entrada de dicionário para este ângulo de rotação ocupa apenas uma linha:

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <LayoutOptions x:Key="horzOptions"
                       Alignment="Center" />

        <LayoutOptions x:Key="vertOptions"
                       Alignment="Center"
                       Expands="True" />

         <x:Double x:Key="borderWidth">
            3
         </x:Double>

        <x:Double x:Key="rotationAngle">-15</x:Double>
    </ResourceDictionary>
</ContentPage.Resources>
```

Esses dois recursos podem ser referenciados da mesma maneira que os valores de `LayoutOptions`:

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="Red"
        FontSize="24" />
```

Para recursos do tipo `Color`, você pode usar as mesmas representações de cadeia de caracteres que você usa ao atribuir diretamente atributos desses tipos. Os conversores de tipo são invocados quando o recurso é criado. Aqui está um recurso do tipo `Color`:

```xaml
<Color x:Key="textColor">Red</Color>
```

Geralmente, os programas definem uma propriedade `FontSize` como um membro da enumeração `NamedSize`, como `Large`. A classe `FontSizeConverter` funciona nos bastidores para convertê-la em um valor dependente de plataforma usando o método `Device.GetNamedSized`. No entanto, ao definir um recurso de tamanho de fonte, faz mais sentido usar um valor numérico, mostrado aqui como um tipo de `x:Double`:

```xaml
<x:Double x:Key="fontSize">24</x:Double>
```

Agora, todas as propriedades, exceto `Text`, são definidas pelas configurações de recurso:

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="{StaticResource textColor}"
        FontSize="{StaticResource fontSize}" />
```

Também é possível usar `OnPlatform` no dicionário de recursos para definir valores diferentes para as plataformas. Veja como um objeto `OnPlatform` pode fazer parte do dicionário de recursos para cores de texto diferentes:

```xaml
<OnPlatform x:Key="textColor"
            x:TypeArguments="Color">
    <On Platform="iOS" Value="Red" />
    <On Platform="Android" Value="Aqua" />
    <On Platform="UWP" Value="#80FF80" />
</OnPlatform>
```

Observe que `OnPlatform` Obtém um atributo `x:Key` porque ele é um objeto no dicionário e um atributo `x:TypeArguments` porque ele é uma classe genérica. Os atributos `iOS`, `Android` e `UWP` são convertidos em valores de `Color` quando o objeto é inicializado.

Aqui está o arquivo XAML final completo com três botões acessando seis valores compartilhados:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>
        <ResourceDictionary>
            <LayoutOptions x:Key="horzOptions"
                           Alignment="Center" />

            <LayoutOptions x:Key="vertOptions"
                           Alignment="Center"
                           Expands="True" />

            <x:Double x:Key="borderWidth">3</x:Double>

            <x:Double x:Key="rotationAngle">-15</x:Double>

            <OnPlatform x:Key="textColor"
                        x:TypeArguments="Color">
                <On Platform="iOS" Value="Red" />
                <On Platform="Android" Value="Aqua" />
                <On Platform="UWP" Value="#80FF80" />
            </OnPlatform>

            <x:Double x:Key="fontSize">24</x:Double>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <Button Text="Do this!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize="{StaticResource fontSize}" />

        <Button Text="Do that!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize="{StaticResource fontSize}" />

        <Button Text="Do the other thing!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize="{StaticResource fontSize}" />

    </StackLayout>
</ContentPage>
```

As capturas de tela verificam o estilo consistente e o estilo dependente da plataforma:

[Controles de ![Styled](xaml-markup-extensions-images/sharedresources.png)](xaml-markup-extensions-images/sharedresources-large.png#lightbox)

Embora seja mais comum definir a coleção de `Resources` na parte superior da página, tenha em mente que a propriedade `Resources` é definida por `VisualElement`, e você pode ter `Resources` coleções em outros elementos na página. Por exemplo, tente adicionar um ao `StackLayout` neste exemplo:

```xaml
<StackLayout>
    <StackLayout.Resources>
        <ResourceDictionary>
            <Color x:Key="textColor">Blue</Color>
        </ResourceDictionary>
    </StackLayout.Resources>
    ...
</StackLayout>
```

Você descobrirá que a cor do texto dos botões agora é azul. Basicamente, sempre que o analisador XAML encontra uma `StaticResource` extensão de marcação, ele pesquisa a árvore visual e usa a primeira `ResourceDictionary` que ela encontra contendo essa chave.

Um dos tipos mais comuns de objetos armazenados em dicionários de recursos é o `Style` Xamarin. Forms, que define uma coleção de configurações de propriedade. Os estilos são discutidos nos [estilos](~/xamarin-forms/user-interface/styles/index.md)de artigo.

Às vezes, os desenvolvedores novidade no XAML se perguntam se eles podem colocar um elemento visual, como `Label` ou `Button` em uma `ResourceDictionary`. Embora seja certamente possível, isso não faz muito sentido. A finalidade do `ResourceDictionary` é compartilhar objetos. Um elemento visual não pode ser compartilhado. A mesma instância não pode aparecer duas vezes em uma única página.

## <a name="the-xstatic-markup-extension"></a>A extensão de marcação x:Static

Apesar das semelhanças de seus nomes, `x:Static` e `StaticResource` são muito diferentes. `StaticResource` retorna um objeto de um dicionário de recursos enquanto `x:Static` acessa uma das seguintes opções:

- um campo estático público
- uma propriedade estática pública
- um campo constante pública
- um membro de enumeração.

A extensão de marcação de `StaticResource` é suportada por implementações XAML que definem um dicionário de recursos, enquanto `x:Static` é uma parte intrínseca do XAML, como o prefixo de `x` revela.

Aqui estão alguns exemplos que demonstram como `x:Static` pode referenciar explicitamente os campos estáticos e membros de enumeração:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="{x:Static LayoutOptions.Start}"
       HorizontalTextAlignment="{x:Static TextAlignment.Center}"
       TextColor="{x:Static Color.Aqua}" />
```

Até agora, isso não é muito impressionante. Mas a extensão de marcação de `x:Static` também pode fazer referência a campos estáticos ou propriedades de seu próprio código. Por exemplo, aqui está uma classe `AppConstants` que contém alguns campos estáticos que você talvez queira usar em várias páginas em um aplicativo:

```csharp
using System;
using Xamarin.Forms;

namespace XamlSamples
{
    static class AppConstants
    {
        public static readonly Thickness PagePadding;

        public static readonly Font TitleFont;

        public static readonly Color BackgroundColor = Color.Aqua;

        public static readonly Color ForegroundColor = Color.Brown;

        static AppConstants()
        {
            switch (Device.RuntimePlatform)
            {
                case Device.iOS:
                    PagePadding = new Thickness(5, 20, 5, 0);
                    TitleFont = Font.SystemFontOfSize(35, FontAttributes.Bold);
                    break;

                case Device.Android:
                    PagePadding = new Thickness(5, 0, 5, 0);
                    TitleFont = Font.SystemFontOfSize(40, FontAttributes.Bold);
                    break;

                case Device.UWP:
                    PagePadding = new Thickness(5, 0, 5, 0);
                    TitleFont = Font.SystemFontOfSize(50, FontAttributes.Bold);
                    break;
            }
        }
    }
}
```

Para fazer referência aos campos estáticos dessa classe no arquivo XAML, você precisará de alguma forma para indicar dentro do arquivo XAML onde esse arquivo está localizado. Você faz isso com uma declaração de namespace XML.

Lembre-se de que os arquivos XAML criados como parte do modelo XAML Xamarin. Forms padrão contêm duas declarações de namespace XML: uma para acessar as classes Xamarin. Forms e outra para referenciar marcas e atributos intrínsecos ao XAML:

```csharp
xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
```

Você precisará de declarações de namespace XML adicionais para acessar outras classes. Cada declaração de namespace XML adicional define um novo prefixo. Para acessar classes locais para o aplicativo compartilhado .NET Standard biblioteca, como `AppConstants`, os programadores XAML geralmente usam o prefixo `local`. A declaração de namespace deve indicar o nome do namespace CLR (Common Language Runtime), também conhecido como o nome do namespace .NET, que é o nome que C# aparece em uma definição de `namespace` ou em uma diretiva `using`:

```csharp
xmlns:local="clr-namespace:XamlSamples"
```

Você também pode definir declarações de namespace XML para namespaces do .NET em qualquer assembly ao qual a biblioteca .NET Standard faz referência. Por exemplo, aqui está um prefixo de `sys` para o namespace do .NET `System` padrão, que está no assembly **mscorlib** , que, uma vez acima, é "biblioteca de tempo de execução de objeto comum da Microsoft", mas agora significa "biblioteca de tempo de execução de objeto comum padrão multilíngüe". Como esse é outro assembly, você também deve especificar o nome do assembly, neste caso, **mscorlib**:

```csharp
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

Observe que a palavra-chave `clr-namespace` é seguida por dois-pontos e, em seguida, o nome do namespace .NET, seguido por um ponto-e-vírgula, a palavra-chave `assembly`, um sinal de igual e o nome do assembly.

Sim, um duplo segue `clr-namespace`, mas o sinal de igual segue `assembly`. A sintaxe foi definida dessa forma deliberadamente: a maioria das declarações de namespace XML referenciam um URI que inicia um nome de esquema de URI, como `http`, que é sempre seguido por dois-pontos. A parte `clr-namespace` desta cadeia de caracteres é destinada a imitar essa convenção.

Ambas as declarações de namespace estão incluídas no exemplo de **StaticConstantsPage** . Observe que as dimensões de `BoxView` são definidas como `Math.PI` e `Math.E`, mas dimensionadas por um fator de 100:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             xmlns:sys="clr-namespace:System;assembly=mscorlib"
             x:Class="XamlSamples.StaticConstantsPage"
             Title="Static Constants Page"
             Padding="{x:Static local:AppConstants.PagePadding}">

    <StackLayout>
       <Label Text="Hello, XAML!"
              TextColor="{x:Static local:AppConstants.BackgroundColor}"
              BackgroundColor="{x:Static local:AppConstants.ForegroundColor}"
              Font="{x:Static local:AppConstants.TitleFont}"
              HorizontalOptions="Center" />

      <BoxView WidthRequest="{x:Static sys:Math.PI}"
               HeightRequest="{x:Static sys:Math.E}"
               Color="{x:Static local:AppConstants.ForegroundColor}"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand"
               Scale="100" />
    </StackLayout>
</ContentPage>
```

O tamanho do `BoxView` resultante em relação à tela depende da plataforma:

[![Controls usando a extensão de marcação x:Static](xaml-markup-extensions-images/staticconstants.png)](xaml-markup-extensions-images/staticconstants-large.png#lightbox)

## <a name="other-standard-markup-extensions"></a>Outras extensões de marcação padrão

Várias extensões de marcação são intrínsecas ao XAML e têm suporte em arquivos XAML Xamarin. Forms. Alguns deles não são usados com muita frequência, mas são essenciais quando você precisa deles:

- Se uma propriedade tiver um valor não `null` por padrão, mas você quiser defini-la como `null`, defina-a como a `{x:Null}` extensão de marcação.
- Se uma propriedade for do tipo `Type`, você poderá atribuí-la a um objeto `Type` usando a extensão de marcação `{x:Type someClass}`.
- Você pode definir matrizes em XAML usando a extensão de marcação `x:Array`. Essa extensão de marcação tem um atributo necessário chamado `Type` que indica o tipo dos elementos na matriz.
- A extensão de marcação de `Binding` é discutida na [parte 4. Noções básicas de ligação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).
- A extensão de marcação de `RelativeSource` é discutida em [associações relativas](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md).

## <a name="the-constraintexpression-markup-extension"></a>A extensão de marcação Constraintry

As extensões de marcação podem ter propriedades, mas não são definidas como atributos XML. Em uma extensão de marcação, as configurações de propriedade são separadas por vírgulas e nenhuma aspa aparece dentro das chaves.

Isso pode ser ilustrado com a extensão de marcação Xamarin. Forms chamada `ConstraintExpression`, que é usada com a classe `RelativeLayout`. Você pode especificar o local ou o tamanho de uma exibição filho como uma constante ou em relação a um pai ou outro modo de exibição nomeado. A sintaxe do `ConstraintExpression` permite que você defina a posição ou o tamanho de uma exibição usando uma `Factor` vezes uma propriedade de outra exibição, além de uma `Constant`. Qualquer coisa mais complexa do que isso requer código.

Aqui está um exemplo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.RelativeLayoutPage"
             Title="RelativeLayout Page">

    <RelativeLayout>

        <!-- Upper left -->
        <BoxView Color="Red"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}" />
        <!-- Upper right -->
        <BoxView Color="Green"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=1,
                                            Constant=-40}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}" />
        <!-- Lower left -->
        <BoxView Color="Blue"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=1,
                                            Constant=-40}" />
        <!-- Lower right -->
        <BoxView Color="Yellow"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=1,
                                            Constant=-40}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=1,
                                            Constant=-40}" />

        <!-- Centered and 1/3 width and height of parent -->
        <BoxView x:Name="oneThird"
                 Color="Red"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=0.33}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=0.33}"
                 RelativeLayout.WidthConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=0.33}"
                 RelativeLayout.HeightConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=0.33}"  />

        <!-- 1/3 width and height of previous -->
        <BoxView Color="Blue"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=X}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=Y}"
                 RelativeLayout.WidthConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=Width,
                                            Factor=0.33}"
                 RelativeLayout.HeightConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=Height,
                                            Factor=0.33}"  />
    </RelativeLayout>
</ContentPage>
```

Talvez a lição mais importante que você deve seguir neste exemplo seja a sintaxe da extensão de marcação: sem aspas deve aparecer dentro das chaves de uma extensão de marcação. Ao digitar a extensão de marcação em um arquivo XAML, é natural que você queira colocar os valores das propriedades entre aspas. Resistir à tentação!

Este é o programa em execução:

[Layout de ![Relative usando restrições](xaml-markup-extensions-images/relativelayout.png)](xaml-markup-extensions-images/relativelayout-large.png#lightbox)

## <a name="summary"></a>Resumo

As extensões de marcação XAML mostradas aqui fornecem suporte importante para arquivos XAML. Mas talvez a extensão de marcação XAML mais valiosa seja `Binding`, que é abordada na próxima parte desta série, [parte 4. Noções básicas de ligação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

## <a name="related-links"></a>Links relacionados

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Parte 1. Introdução com XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 2. Sintaxe XAML essencial](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Parte 4. Noções básicas de ligação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Parte 5. Da vinculação de dados com o MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
