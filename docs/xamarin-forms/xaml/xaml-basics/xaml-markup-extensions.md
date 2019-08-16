---
title: Parte 3. Extensões de marcação XAML
description: Extensões de marcação XAML constituem um recurso importante no XAML que permitem que as propriedades sejam definidas para objetos ou valores que são referenciados indiretamente de outras fontes.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F4A37564-B18B-42FF-B841-9A1949895AB6
author: davidbritch
ms.author: dabritch
ms.date: 03/27/2018
ms.openlocfilehash: 0ce718bf0380c47033129755d6ca9d40c1136e2c
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69529272"
---
# <a name="part-3-xaml-markup-extensions"></a>Parte 3. Extensões de marcação XAML

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_Extensões de marcação XAML constituem um recurso importante no XAML que permitem que as propriedades sejam definidas para objetos ou valores que são referenciados indiretamente de outras fontes. Extensões de marcação XAML são particularmente importantes para compartilhamento de objetos e fazendo referência constantes usadas em todo um aplicativo, mas que encontrar seu utilitário maior de associações de dados._

## <a name="xaml-markup-extensions"></a>Extensões de marcação XAML

Em geral, você deve usar XAML para definir propriedades de um objeto como valores explícitos, como uma cadeia de caracteres, um número, um membro de enumeração ou uma cadeia de caracteres que é convertida em um valor em segundo plano.

Às vezes, no entanto, as propriedades em vez disso, devem fazer referência a valores definidos em algum lugar de outra ou que podem exigir um pouco processamento pelo código em tempo de execução. Para esses fins, XAML *extensões de marcação* estão disponíveis.

Essas extensões de marcação XAML não são extensões do XML. XAML é XML inteiramente legal. Eles são chamados "extensões", porque eles são apoiados por código nas classes que implementam `IMarkupExtension`. Você pode escrever suas próprias extensões de marcação personalizada.

Em muitos casos, as extensões de marcação XAML são instantaneamente reconhecíveis nos arquivos XAML porque eles aparecem como configurações de atributo delimitadas por chaves: {e}, mas, às vezes, extensões de marcação aparecem na marcação como elementos convencionais.

## <a name="shared-resources"></a>Recursos compartilhados

Algumas páginas XAML contêm vários modos de exibição com propriedades definidas para os mesmos valores. Por exemplo, várias das configurações de propriedade para esses `Button` objetos forem os mesmos:

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

Se uma dessas propriedades precisa ser alterado, talvez você prefira fazer a alteração apenas uma vez, em vez de três vezes. Se esse fosse o código, você provavelmente usarão constantes e os objetos estáticos somente leitura para ajudar a manter esses valores, consistente e fácil de modificar.

No XAML, uma solução popular é armazenar esses valores ou objetos em um *dicionário de recursos*. O `VisualElement` classe define uma propriedade chamada `Resources` do tipo `ResourceDictionary`, que é um dicionário com chaves de tipo `string` e valores do tipo `object`. Você pode colocar os objetos nesse Dictionary e, em seguida, referenciá-los a partir de marcação, tudo em XAML.

Para usar um dicionário de recursos em uma página, inclua um par de `Resources` marcas de elemento de propriedade. É mais conveniente para colocá-las na parte superior da página:

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

Agora os objetos e valores de vários tipos podem ser adicionados ao dicionário de recursos. Esses tipos devem ser instanciáveis. Eles não podem ser classes abstratas, por exemplo. Esses tipos também devem ter um construtor público sem parâmetros. Cada item requer uma chave de dicionário especificada com o `x:Key` atributo. Por exemplo:

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

Esses dois itens são valores do tipo estrutura `LayoutOptions`e cada um tem uma chave exclusiva e uma ou duas propriedades definidas. No código e marcação, é muito mais comum usar os campos estáticos de `LayoutOptions`, mas aqui é mais conveniente definir as propriedades.

Agora, é necessário definir as `HorizontalOptions` e `VerticalOptions` propriedades desses botões para esses recursos, e isso é feito com o `StaticResource` extensão de marcação XAML:

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="3"
        Rotation="-15"
        TextColor="Red"
        FontSize="24" />
```

O `StaticResource` extensão de marcação é sempre delimitada com chaves e inclui a chave do dicionário.

O nome `StaticResource` distingue de `DynamicResource`, que o xamarin. Forms também oferece suporte. `DynamicResource` é para as chaves de dicionário associadas aos valores que podem ser alterados durante o tempo de execução, enquanto `StaticResource` acessa os elementos do dicionário de apenas uma vez quando os elementos na página são construídos.

Para o `BorderWidth` propriedade, é necessário armazenar um valor double no dicionário. XAML convenientemente define marcas para tipos de dados comuns, como `x:Double` e `x:Int32`:

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

Você não precisa colocá-lo em três linhas. Essa entrada de dicionário para o ângulo de rotação usa apenas uma linha para cima:

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

Esses dois recursos podem ser referenciados da mesma forma como o `LayoutOptions` valores:

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="Red"
        FontSize="24" />
```

Para recursos do tipo `Color`, você pode usar as mesmas representações de cadeia de caracteres que você usa ao atribuir diretamente atributos desses tipos. Conversores de tipo são invocados quando o recurso é criado. Aqui está um recurso do tipo `Color`:

```xaml
<Color x:Key="textColor">Red</Color>
```

Programas com frequência, o conjunto de um `FontSize` propriedade para um membro do `NamedSize` enumeração como `Large`. O `FontSizeConverter` classe funciona nos bastidores para convertê-lo em um valor dependente de plataforma usando o `Device.GetNamedSized` método. No entanto, ao definir um recurso de tamanho da fonte, faz mais sentido usar um valor numérico, mostrado aqui como um `x:Double` tipo:

```xaml
<x:Double x:Key="fontSize">24</x:Double>
```

Agora todas as propriedades, exceto `Text` são definidos pelas configurações de recursos:

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="{StaticResource textColor}"
        FontSize="{StaticResource fontSize}" />
```

Também é possível usar `OnPlatform` no dicionário de recursos para definir valores diferentes para as plataformas. Aqui está como um `OnPlatform` objeto pode ser parte do dicionário de recursos para as cores de texto diferente:

```xaml
<OnPlatform x:Key="textColor"
            x:TypeArguments="Color">
    <On Platform="iOS" Value="Red" />
    <On Platform="Android" Value="Aqua" />
    <On Platform="UWP" Value="#80FF80" />
</OnPlatform>
```

Observe que `OnPlatform` obtém tanto um `x:Key` porque ele é um objeto no dicionário de atributo e um `x:TypeArguments` atributo porque é uma classe genérica. O `iOS`, `Android`, e `UWP` atributos são convertidos em `Color` valores quando o objeto é inicializado.

Aqui está o arquivo XAML completo final com três botões acessando seis valores compartilhados:

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

Verifique se as capturas de tela, o estilo consistentes e o estilo de dependente de plataforma:

[![](xaml-markup-extensions-images/sharedresources.png "Controles com estilo")](xaml-markup-extensions-images/sharedresources-large.png#lightbox "controles com estilo")

Embora seja mais comum para definir a `Resources` coleção na parte superior da página, tenha em mente que o `Resources` propriedade é definida por `VisualElement`, e você pode ter `Resources` coleções em outros elementos na página. Por exemplo, tente adicionar um para o `StackLayout` neste exemplo:

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

Você descobrirá que a cor do texto dos botões agora é azul. Basicamente, sempre que o analisador XAML encontra uma `StaticResource` extensão de marcação, ele pesquisa a árvore visual e usa a primeira `ResourceDictionary` encontra contendo essa chave.

Um dos tipos mais comuns de objetos armazenados em dicionários de recursos é o xamarin. Forms `Style`, que define uma coleção de configurações de propriedade. Estilos são discutidos neste artigo [estilos](~/xamarin-forms/user-interface/styles/index.md).

Às vezes, os desenvolvedores de novos no XAML esteja se perguntando se eles podem colocar um elemento visual, como `Label` ou `Button` em um `ResourceDictionary`. Embora seja certamente possível, ele não faz muito sentido. A finalidade de `ResourceDictionary` é compartilhar objetos. Um elemento visual não pode ser compartilhado. A mesma instância não pode aparecer duas vezes em uma única página.

## <a name="the-xstatic-markup-extension"></a>A extensão de marcação X:Static

Apesar das semelhanças de seus nomes `x:Static` e `StaticResource` são muito diferentes. `StaticResource` Retorna um objeto de um dicionário de recursos enquanto `x:Static` acessa um dos seguintes:

- um campo estático público
- uma propriedade estática pública
- um campo constante público
- um membro de enumeração.

O `StaticResource` extensão de marcação é suportada por implementações de XAML que definem um dicionário de recursos, enquanto `x:Static` é uma parte intrínseca de XAML, como o `x` revela de prefixo.

Aqui estão alguns exemplos que demonstram como `x:Static` pode referenciar explicitamente os campos estáticos e membros de enumeração:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="{x:Static LayoutOptions.Start}"
       HorizontalTextAlignment="{x:Static TextAlignment.Center}"
       TextColor="{x:Static Color.Aqua}" />
```

Até agora, isso não seja muito impressionante. Mas o `x:Static` extensão de marcação pode também fazer referência a campos estáticos ou propriedades de seu próprio código. Por exemplo, aqui está um `AppConstants` classe que contém alguns campos estáticos que você talvez queira usar em várias páginas em um aplicativo:

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

Para fazer referência os campos estáticos dessa classe no arquivo XAML, você precisará de uma forma de indicar dentro do arquivo XAML em que esse arquivo está localizado. Você pode fazer isso com uma declaração de namespace XML.

Lembre-se de que os arquivos XAML criados como parte do modelo de XAML de xamarin. Forms padrão contêm duas declarações de namespace XML: um para acesso de classes xamarin. Forms e outro para fazer referência a marcas e atributos intrínsecos ao XAML:

```csharp
xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
```

Você precisará de declarações de namespace XML adicionais para acessar outras classes. Cada declaração de namespace XML adicional define um novo prefixo. Para acessar classes locais para a biblioteca .NET Standard de aplicativo compartilhado, como `AppConstants`, os programadores XAML geralmente usam o prefixo `local`. A declaração de namespace deve indicar o nome do namespace CLR (Common Language Runtime), também conhecido como o nome de namespace de .NET, que é o nome que aparece em um C# `namespace` definição ou em um `using` diretiva:

```csharp
xmlns:local="clr-namespace:XamlSamples"
```

Você também pode definir as declarações de namespace XML para namespaces do .NET em qualquer assembly que faz referência a biblioteca .NET Standard. Por exemplo, aqui está uma `sys` prefixo para o .NET standard `System` namespace, que está no **mscorlib** assembly, que representava uma vez "Microsoft Common Library do objeto de tempo de execução", mas agora significa "Multilanguage padrão Objeto em tempo de execução biblioteca comum." Como esse é outro assembly, você deve também especificar o nome do assembly, neste caso **mscorlib**:

```csharp
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

Observe que a palavra-chave `clr-namespace` é seguido por dois-pontos e, em seguida, o nome do namespace .NET, seguido por um ponto e vírgula, a palavra-chave `assembly`, um sinal de igual e o nome do assembly.

Sim, seguido por dois pontos `clr-namespace` , mas o sinal de igual segue `assembly`. A sintaxe foi definida dessa forma deliberadamente: A maioria das declarações de namespace XML fazem referência a um URI que inicia um `http`nome de esquema de URI, como, que é sempre seguido por dois-pontos. O `clr-namespace` parte dessa cadeia de caracteres destina-se para simular que a convenção.

As duas declarações de namespace esses estão incluídas na **StaticConstantsPage** exemplo. Observe que o `BoxView` dimensões são definidas como `Math.PI` e `Math.E`, mas em escala por um fator de 100:

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

O tamanho do resultante `BoxView` em relação à tela é dependente de plataforma:

 [![](xaml-markup-extensions-images/staticconstants.png "Controles usando a extensão de marcação X:Static")](xaml-markup-extensions-images/staticconstants-large.png#lightbox "controles usando a extensão de marcação X:Static")

## <a name="other-standard-markup-extensions"></a>Outras extensões de marcação padrão

Várias extensões de marcação são intrínsecas para XAML e com suporte em arquivos XAML de xamarin. Forms. Alguns deles não são usadas com muita frequência, mas são essenciais quando precisar delas:

- Se uma propriedade tiver um não - `null` valor por padrão, mas você deseja defini-lo como `null`, defina-a como o `{x:Null}` extensão de marcação.
- Se uma propriedade é do tipo `Type`, você pode atribuí-la a um `Type` usando a extensão de marcação do objeto `{x:Type someClass}`.
- Você pode definir matrizes em XAML usando o `x:Array` extensão de marcação. Esta extensão de marcação tem um atributo obrigatório chamado `Type` que indica o tipo dos elementos na matriz.
- O `Binding` extensão de marcação é discutida em [parte 4. Noções básicas de vinculação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

## <a name="the-constraintexpression-markup-extension"></a>A extensão de marcação ConstraintExpression

Extensões de marcação podem ter propriedades, mas elas não são definidas como atributos XML. Em uma extensão de marcação, as configurações de propriedade são separadas por vírgulas e sem aspas aparecer entre chaves.

Isso pode ser ilustrado com a extensão de marcação do xamarin. Forms denominada `ConstraintExpression`, que é usado com o `RelativeLayout` classe. Você pode especificar o local ou o tamanho de um modo de exibição filho como uma constante, ou em relação a um pai ou de outro modo de exibição nomeado. A sintaxe do `ConstraintExpression` permite que você defina a posição ou o tamanho de um modo de exibição usando um `Factor` vezes, uma propriedade de outro modo de exibição, além de uma `Constant`. Algo mais complexo do que isso requer código.

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

Talvez a lição mais importante que você deve seguir neste exemplo seja a sintaxe da extensão de marcação: Nenhuma aspa deve aparecer dentro das chaves de uma extensão de marcação. Ao digitar a extensão de marcação em um arquivo XAML, é natural deseja colocar os valores das propriedades entre aspas. Resista à tentação!

Aqui está o programa em execução:

[![](xaml-markup-extensions-images/relativelayout.png "Usando restrições de Layout relativa")](xaml-markup-extensions-images/relativelayout-large.png#lightbox "usando restrições de Layout relativo")

## <a name="summary"></a>Resumo

As extensões de marcação XAML mostradas aqui oferecem suporte importante para arquivos XAML. Mas talvez seja a extensão de marcação XAML mais valiosa `Binding`, que será abordado na próxima parte desta série, [parte 4. Noções básicas de vinculação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).



## <a name="related-links"></a>Links relacionados

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Parte 1. Introdução ao XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 2. Sintaxe essencial de XAML](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Parte 4. Conceitos básicos da associação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Parte 5. De associação de dados a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
