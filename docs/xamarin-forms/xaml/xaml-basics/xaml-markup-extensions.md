---
title: Parte 3. Extensões de marcação XAML
description: Extensões de marcação XAML constituem um recurso importante em XAML que permitem que propriedades sejam definidas para objetos ou valores que são referenciados indiretamente de outras fontes. Extensões de marcação XAML são particularmente importantes para o compartilhamento de objetos e referenciando constantes usadas em um aplicativo, mas que encontrar seu utilitário maior de associações de dados.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F4A37564-B18B-42FF-B841-9A1949895AB6
author: charlespetzold
ms.author: chape
ms.date: 3/27/2018
ms.openlocfilehash: c110223eae2bb06f64adf3e09977d97cc7b5d71b
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="part-3-xaml-markup-extensions"></a>Parte 3. Extensões de marcação XAML

_Extensões de marcação XAML constituem um recurso importante em XAML que permitem que propriedades sejam definidas para objetos ou valores que são referenciados indiretamente de outras fontes. Extensões de marcação XAML são particularmente importantes para o compartilhamento de objetos e referenciando constantes usadas em um aplicativo, mas que encontrar seu utilitário maior de associações de dados._

## <a name="xaml-markup-extensions"></a>Extensões de marcação XAML

Em geral, você deve usar XAML para definir propriedades de um objeto para valores explícitos, como uma cadeia de caracteres, um número, um membro de enumeração ou uma cadeia de caracteres que é convertida em um valor em segundo plano.

Às vezes, no entanto, propriedades em vez disso, devem fazer referência a valores definidos em outro lugar, ou que podem exigir um processamento pouco pelo código em tempo de execução. Para esses fins, XAML *extensões de marcação* estão disponíveis.

Essas extensões de marcação XAML não são extensões de XML. XAML é XML inteiramente legal. São chamados "extensões" porque elas contam com o código nas classes que implementam `IMarkupExtension`. Você pode escrever suas próprias extensões de marcação personalizadas.

Em muitos casos, as extensões de marcação XAML são instantaneamente reconhecíveis nos arquivos XAML porque eles aparecem como configurações de atributo delimitadas por chaves: {e}, mas, às vezes, as extensões de marcação são exibidos na marcação como elementos convencionais.

## <a name="shared-resources"></a>Recursos compartilhados

Algumas páginas XAML contenham várias exibições com propriedades definidas para os mesmos valores. Por exemplo, muitas das configurações de propriedade para essas `Button` objetos forem os mesmos:

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

Se uma dessas propriedades deve ser alterado, talvez você prefira a alteração apenas uma vez, em vez de três vezes. Se esse fosse o código, você provavelmente usarão constantes e objetos estáticos somente leitura para ajudar a manter esses valores consistentes e fáceis de modificar.

Em XAML, uma solução popular é armazenar esses valores ou objetos em um *dicionário de recurso*. O `VisualElement` classe define uma propriedade chamada `Resources` do tipo `ResourceDictionary`, que é um dicionário com chaves de tipo `string` e valores do tipo `object`. Você pode colocar objetos neste dicionário e, em seguida, fazer referência a eles, de marcação, todos em XAML.

Para usar um dicionário de recursos em uma página, inclua um par de `Resources` marcas de elemento de propriedade. É mais conveniente colocar esses na parte superior da página:

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

Agora os objetos e valores de vários tipos podem ser adicionados ao dicionário de recurso. Esses tipos devem ser podem ser instanciados. Eles não podem ser classes abstratas, por exemplo. Esses tipos também devem ter um construtor público sem parâmetros. Cada item requer uma chave de dicionário especificada com o `x:Key` atributo. Por exemplo:

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

Esses dois itens são valores do tipo de estrutura `LayoutOptions`e cada um tem uma chave exclusiva e uma ou duas propriedades definidas. No código e marcação, é muito mais comum para usar os campos estáticos de `LayoutOptions`, mas aqui é mais conveniente definir as propriedades.

Agora, é necessário definir o `HorizontalOptions` e `VerticalOptions` propriedades desses botões para esses recursos, e isso é feito com o `StaticResource` extensão de marcação XAML:

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="3"
        Rotation="-15"
        TextColor="Red"
        FontSize="24" />
```

O `StaticResource` extensão de marcação sempre é delimitada por chaves e inclui a chave de dicionário.

O nome `StaticResource` distingue de `DynamicResource`, que também suporta xamarin. Forms. `DynamicResource` para as chaves de dicionário associadas com os valores que podem ser alterados durante o tempo de execução, enquanto `StaticResource` acessa os elementos do dicionário de apenas uma vez quando os elementos na página são construídos.

Para o `BorderWidth` propriedade, é necessário armazenar um valor double no dicionário. XAML convenientemente define marcas para tipos de dados comuns como `x:Double` e `x:Int32`:

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

Não é necessário colocá-lo em três linhas. Essa entrada de dicionário para o ângulo de rotação tem apenas uma linha para cima:

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

Esses dois recursos podem ser referenciados da mesma maneira como o `LayoutOptions` valores:

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="Red"
        FontSize="24" />
```

Para recursos do tipo `Color`, você pode usar as mesmo representações de cadeia de caracteres que você usar ao atribuir diretamente atributos desses tipos. Conversores de tipo são invocados quando o recurso for criado. Este é um recurso do tipo `Color`:

```xaml
<Color x:Key="textColor">Red</Color>
```

Programas frequentemente, o conjunto de um `FontSize` propriedade a um membro do `NamedSize` enumeração como `Large`. O `FontSizeConverter` classe funciona nos bastidores para convertê-la em um valor dependente de plataforma usando o `Device.GetNamedSized` método. No entanto, ao definir um recurso de tamanho de fonte, faz mais sentido usar um valor numérico, mostrado aqui como uma `x:Double` tipo:

```xaml
<x:Double x:Key="fontSize">24</x:Double>
```

Agora todas as propriedades exceto `Text` são definidos pelas configurações de recursos:

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="{StaticResource textColor}"
        FontSize="{StaticResource fontSize}" />
```

Também é possível usar `OnPlatform` dentro do dicionário de recursos para definir valores diferentes para as plataformas. Aqui está como um `OnPlatform` objeto pode ser parte do dicionário de recursos para as cores de texto diferentes:

```xaml
<OnPlatform x:Key="textColor"
            x:TypeArguments="Color">
    <On Platform="iOS" Value="Red" />
    <On Platform="Android" Value="Aqua" />
    <On Platform="UWP" Value="#80FF80" />
</OnPlatform>
```

Observe que `OnPlatform` obtém ambos um `x:Key` porque ele é um objeto no dicionário de atributo e um `x:TypeArguments` atributo porque é uma classe genérica. O `iOS`, `Android`, e `UWP` atributos são convertidos em `Color` valores quando o objeto é inicializado.

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

            <x:String x:Key="fontSize">Large</x:String>
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

Verifique se as capturas de tela de estilo consistente e o estilo de dependente de plataforma:

[![](xaml-markup-extensions-images/sharedresources.png "Controles com estilo")](xaml-markup-extensions-images/sharedresources-large.png#lightbox "controles com estilo")

Embora seja mais comum para definir a `Resources` coleção na parte superior da página, tenha em mente que o `Resources` propriedade é definida por `VisualElement`, e você pode ter `Resources` coleções em outros elementos na página. Por exemplo, tente adicionar uma para o `StackLayout` neste exemplo:

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

Você descobrirá que a cor do texto dos botões agora é azul. Basicamente, sempre que o analisador XAML encontra um `StaticResource` extensão de marcação, ele procura na árvore visual e usa a primeira `ResourceDictionary` encontra que contém a chave.

Um dos tipos mais comuns de objetos armazenados em dicionários de recursos é o xamarin. Forms `Style`, que define uma coleção de configurações de propriedade. Estilos são discutidos no artigo [estilos](~/xamarin-forms/user-interface/styles/index.md).

Às vezes, os desenvolvedores de novos para XAML esteja se perguntando se colocar um elemento visual como `Label` ou `Button` em um `ResourceDictionary`. Embora seja certamente possível, não faz muito sentido. A finalidade de `ResourceDictionary` é compartilhar objetos. Um elemento visual não pode ser compartilhado. A mesma instância não pode aparecer duas vezes em uma única página.

## <a name="the-xstatic-markup-extension"></a>A extensão de marcação X:Static

Apesar das semelhanças de seus nomes, `x:Static` e `StaticResource` são muito diferentes. `StaticResource` Retorna um objeto de um dicionário de recurso ao `x:Static` acessa um dos seguintes:

- um campo estático público
- uma propriedade estática pública
- um campo constante público 
- um membro de enumeração. 

O `StaticResource` extensão de marcação é suportada por implementações de XAML que define um dicionário de recursos, enquanto `x:Static` é uma parte intrínseca de XAML, como o `x` revela do prefixo.

Aqui estão alguns exemplos que demonstram como `x:Static` pode fazer referência explícita campos estáticos e os membros da enumeração:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="{x:Static LayoutOptions.Start}"
       HorizontalTextAlignment="{x:Static TextAlignment.Center}"
       TextColor="{x:Static Color.Aqua}" />
```

Até agora, isso não é muito grande. Mas o `x:Static` extensão de marcação também pode referenciar campos estáticos ou propriedades de seu próprio código. Por exemplo, aqui está um `AppConstants` classe que contém alguns campos estáticos que você talvez queira usar em várias páginas em um aplicativo:

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

Para fazer referência os campos estáticos dessa classe no arquivo XAML, você precisará de uma forma de indicar dentro do arquivo XAML onde esse arquivo está localizado. Você pode fazer isso com uma declaração de namespace XML.

Lembre-se de que os arquivos XAML criados como parte do modelo de XAML xamarin. Forms padrão contém duas declarações de namespace XML: um para acessar classes xamarin. Forms e outro para fazer referência a marcas e atributos intrínsecos para XAML:

```csharp
xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
```

Você precisará de declarações de namespace XML adicionais para acessar outras classes. Cada declaração de namespace XML adicional define um novo prefixo. Para acessar classes locais para a biblioteca .NET padrão de aplicativo compartilhado, como `AppConstants`, os programadores do XAML geralmente usam o prefixo `local`. A declaração de namespace deve indicar o nome do namespace CLR (Common Language Runtime), também conhecido como o nome de namespace de .NET, que é o nome que aparece em c# `namespace` definição ou em um `using` diretiva:

```csharp
xmlns:local="clr-namespace:XamlSamples"
```

Você também pode definir as declarações de namespace XML para namespaces do .NET em qualquer assembly que faz referência a biblioteca do .NET padrão. Por exemplo, aqui está uma `sys` prefixo para o .NET padrão `System` namespace, que é o **mscorlib** assembly, que uma vez significava "Microsoft Common biblioteca de objetos em tempo de execução", mas agora significa "multilíngue padrão Objeto em tempo de execução biblioteca comum." Como esse é outro assembly, você deve também especificar o nome do assembly, nesse caso **mscorlib**:

```csharp
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

Observe que a palavra-chave `clr-namespace` é seguido por dois-pontos e, em seguida, o nome do namespace .NET, seguido por um ponto e vírgula, a palavra-chave `assembly`, um sinal de igual e o nome do assembly.

Sim, seguido por dois pontos `clr-namespace` mas o sinal de igual segue `assembly`. A sintaxe foi definida na forma deliberadamente: declarações de namespace XML mais fazer referência a um URI que começa um nome de esquema URI, como `http`, que sempre é seguido por dois-pontos. O `clr-namespace` parte da cadeia de caracteres destina-se para simular essa convenção.

Ambas as estas declarações de namespace estão incluídas no **StaticConstantsPage** exemplo. Observe que o `BoxView` dimensões são definidas para `Math.PI` e `Math.E`, mas em escala por um fator de 100:

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

O tamanho dos resultantes `BoxView` em relação à tela é dependente de plataforma:

 [![](xaml-markup-extensions-images/staticconstants.png "Controles usando a extensão de marcação X:Static")](xaml-markup-extensions-images/staticconstants-large.png#lightbox "controles usando a extensão de marcação X:Static")

## <a name="other-standard-markup-extensions"></a>Outras extensões de marcação padrão

Várias extensões de marcação são intrínsecas para XAML e tem suporte nos arquivos XAML xamarin. Forms. Algumas delas não são usadas com muita frequência, mas serão essenciais quando você precisar deles:

-  Se tiver uma propriedade não `null` valor por padrão, mas você deseja defini-lo como `null`, defina-a como o `{x:Null}` extensão de marcação.
-  Se uma propriedade é do tipo `Type`, você pode atribuí-lo para um `Type` usando a extensão de marcação do objeto `{x:Type someClass}`.
-  Você pode definir conjuntos em XAML usando o `x:Array` extensão de marcação. Esta extensão de marcação tem um atributo obrigatório chamado `Type` que indica o tipo dos elementos na matriz.
- O `Binding` extensão de marcação é abordada em [parte 4. Noções básicas de associação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

## <a name="the-constraintexpression-markup-extension"></a>A extensão de marcação ConstraintExpression

Extensões de marcação podem ter propriedades, mas elas não são definidas como atributos XML. Em uma extensão de marcação, as configurações de propriedade são separadas por vírgulas e sem aspas aparecem dentro de chaves.

Isso pode ser ilustrado com a extensão de marcação xamarin. Forms denominada `ConstraintExpression`, que é usada com a `RelativeLayout` classe. Você pode especificar o local ou o tamanho de uma exibição filho como uma constante ou em relação ao pai ou outro modo de exibição nomeado. A sintaxe do `ConstraintExpression` permite que você definir a posição ou o tamanho de um modo de exibição usando um `Factor` vezes uma propriedade de outro modo de exibição, além de uma `Constant`. Nada mais complexo do que requer código.

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

Talvez a lição mais importante que você deve executar este exemplo é a sintaxe de extensão de marcação: sem aspas devem aparecer dentro de chaves de uma extensão de marcação. Ao digitar a extensão de marcação em um arquivo XAML, é natural que você deseja colocar os valores das propriedades entre aspas. Resista a tentativa!

Aqui está o programa em execução:

[![](xaml-markup-extensions-images/relativelayout.png "Usando restrições de Layout relativo")](xaml-markup-extensions-images/relativelayout-large.png#lightbox "relativo Layout usando restrições")

## <a name="summary"></a>Resumo

As extensões de marcação XAML mostradas aqui oferecem suporte importante para arquivos XAML. Mas talvez a extensão de marcação XAML importante é `Binding`, que é abordado na próxima parte desta série, [parte 4. Noções básicas de associação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).



## <a name="related-links"></a>Links relacionados

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [Parte 1. Introdução ao XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 2. Sintaxe essencial de XAML](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Parte 4. Conceitos básicos da associação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Parte 5. De associação de dados para o modelo MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
