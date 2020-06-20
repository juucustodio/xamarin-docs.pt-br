---
title: Criação de extensões de marcação do XAML
description: Este artigo explica como definir suas próprias extensões de Xamarin.Forms marcação XAML personalizadas. Uma extensão de marcação XAML é uma classe que implementa a interface IMarkupExtension ou IMarkupExtension <T> .
ms.prod: xamarin
ms.assetid: 797C1EF9-1C8E-4208-8610-9B79CCF17D46
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 878ebcaa5249261afac2776a9e7cf47c0c047135
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84130527"
---
# <a name="creating-xaml-markup-extensions"></a>Criação de extensões de marcação do XAML

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

No nível de programação, uma extensão de marcação XAML é uma classe que implementa [`IMarkupExtension`](xref:Xamarin.Forms.Xaml.IMarkupExtension) a [`IMarkupExtension<T>`](xref:Xamarin.Forms.Xaml.IMarkupExtension`1) interface ou. Você pode explorar o código-fonte das extensões de marcação padrão descritas abaixo no [diretório **MarkupExtensions** ](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions) do Xamarin.Forms repositório github.

Também é possível definir suas próprias extensões de marcação XAML personalizadas derivando de `IMarkupExtension` ou `IMarkupExtension<T>` . Use o formulário genérico se a extensão de marcação obtiver um valor de um tipo específico. Esse é o caso com várias extensões de Xamarin.Forms marcação:

- `TypeExtension`deriva de`IMarkupExtension<Type>`
- `ArrayExtension`deriva de`IMarkupExtension<Array>`
- `DynamicResourceExtension`deriva de`IMarkupExtension<DynamicResource>`
- `BindingExtension`deriva de`IMarkupExtension<BindingBase>`
- `ConstraintExpression`deriva de`IMarkupExtension<Constraint>`

As duas `IMarkupExtension` interfaces definem apenas um método, denominado `ProvideValue` :

```csharp
public interface IMarkupExtension
{
    object ProvideValue(IServiceProvider serviceProvider);
}

public interface IMarkupExtension<out T> : IMarkupExtension
{
    new T ProvideValue(IServiceProvider serviceProvider);
}
```

Como `IMarkupExtension<T>` deriva de `IMarkupExtension` e inclui a `new` palavra-chave on `ProvideValue` , ele contém os dois `ProvideValue` métodos.

Com muita frequência, as extensões de marcação XAML definem propriedades que contribuem para o valor de retorno. (A exceção óbvia é `NullExtension` , na qual `ProvideValue` simplesmente retorna `null` .) O `ProvideValue` método tem um único argumento do tipo `IServiceProvider` que será discutido posteriormente neste artigo.

## <a name="a-markup-extension-for-specifying-color"></a>Uma extensão de marcação para especificar a cor

A seguinte extensão de marcação XAML permite que você construa um `Color` valor usando componentes de matiz, saturação e luminosidade. Ele define quatro propriedades para os quatro componentes da cor, incluindo um componente alfa que é inicializado como 1. A classe deriva de `IMarkupExtension<Color>` para indicar um `Color` valor de retorno:

```csharp
public class HslColorExtension : IMarkupExtension<Color>
{
    public double H { set; get; }

    public double S { set; get; }

    public double L { set; get; }

    public double A { set; get; } = 1.0;

    public Color ProvideValue(IServiceProvider serviceProvider)
    {
        return Color.FromHsla(H, S, L, A);
    }

    object IMarkupExtension.ProvideValue(IServiceProvider serviceProvider)
    {
        return (this as IMarkupExtension<Color>).ProvideValue(serviceProvider);
    }
}
```

Como `IMarkupExtension<T>` deriva de `IMarkupExtension` , a classe deve conter dois `ProvideValue` métodos, um que retorna `Color` e outro que retorna `object` , mas o segundo método pode simplesmente chamar o primeiro método.

A página **demonstração de cor HSL** mostra uma variedade de maneiras que `HslColorExtension` podem aparecer em um arquivo XAML para especificar a cor de um `BoxView` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.HslColorDemoPage"
             Title="HSL Color Demo">

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="BoxView">
                <Setter Property="WidthRequest" Value="80" />
                <Setter Property="HeightRequest" Value="80" />
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <BoxView>
            <BoxView.Color>
                <local:HslColorExtension H="0" S="1" L="0.5" A="1" />
            </BoxView.Color>
        </BoxView>

        <BoxView>
            <BoxView.Color>
                <local:HslColor H="0.33" S="1" L="0.5" />
            </BoxView.Color>
        </BoxView>

        <BoxView Color="{local:HslColorExtension H=0.67, S=1, L=0.5}" />

        <BoxView Color="{local:HslColor H=0, S=0, L=0.5}" />

        <BoxView Color="{local:HslColor A=0.5}" />
    </StackLayout>
</ContentPage>
```

Observe que quando `HslColorExtension` é uma marca XML, as quatro propriedades são definidas como atributos, mas quando aparece entre chaves, as quatro propriedades são separadas por vírgulas sem aspas. Os valores padrão para `H` , `S` , e `L` são 0, e o valor padrão de `A` é 1, portanto, essas propriedades podem ser omitidas se você quiser que elas sejam definidas como valores padrão. O último exemplo mostra um exemplo em que a luminosidade é 0, que normalmente resulta em preto, mas o canal alfa é 0,5 e, portanto, é metade transparente e aparece cinza em relação ao plano de fundo branco da página:

[![Demonstração da cor HSL](creating-images/hslcolordemo-small.png "Demonstração da cor HSL")](creating-images/hslcolordemo-large.png#lightbox "Demonstração da cor HSL")

## <a name="a-markup-extension-for-accessing-bitmaps"></a>Uma extensão de marcação para acessar bitmaps

O argumento para `ProvideValue` é um objeto que implementa a [`IServiceProvider`](xref:System.IServiceProvider) interface, que é definida no namespace do .NET `System` . Essa interface tem um membro, um método chamado `GetService` com um `Type` argumento.

A `ImageResourceExtension` classe mostrada abaixo mostra um possível uso de `IServiceProvider` e `GetService` para obter um `IXmlLineInfoProvider` objeto que pode fornecer informações de linha e caractere indicando onde um determinado erro foi detectado. Nesse caso, uma exceção é gerada quando a `Source` propriedade não foi definida:

```csharp
[ContentProperty("Source")]
class ImageResourceExtension : IMarkupExtension<ImageSource>
{
    public string Source { set; get; }

    public ImageSource ProvideValue(IServiceProvider serviceProvider)
    {
        if (String.IsNullOrEmpty(Source))
        {
            IXmlLineInfoProvider lineInfoProvider = serviceProvider.GetService(typeof(IXmlLineInfoProvider)) as IXmlLineInfoProvider;
            IXmlLineInfo lineInfo = (lineInfoProvider != null) ? lineInfoProvider.XmlLineInfo : new XmlLineInfo();
            throw new XamlParseException("ImageResourceExtension requires Source property to be set", lineInfo);
        }

        string assemblyName = GetType().GetTypeInfo().Assembly.GetName().Name;
        return ImageSource.FromResource(assemblyName + "." + Source, typeof(ImageResourceExtension).GetTypeInfo().Assembly);
    }

    object IMarkupExtension.ProvideValue(IServiceProvider serviceProvider)
    {
        return (this as IMarkupExtension<ImageSource>).ProvideValue(serviceProvider);
    }
}
```

`ImageResourceExtension`é útil quando um arquivo XAML precisa acessar um arquivo de imagem armazenado como um recurso inserido no projeto de biblioteca .NET Standard. Ele usa a `Source` propriedade para chamar o `ImageSource.FromResource` método estático. Esse método requer um nome de recurso totalmente qualificado, que consiste no nome do assembly, no nome da pasta, e no nome de arquivo separado por pontos. O segundo argumento para o `ImageSource.FromResource` método fornece o nome do assembly e só é necessário para Builds de versão em UWP. Independentemente, `ImageSource.FromResource` deve ser chamado a partir do assembly que contém o bitmap, o que significa que essa extensão de recurso XAML não pode fazer parte de uma biblioteca externa, a menos que as imagens também estejam nessa biblioteca. (Consulte o artigo [**imagens inseridas**](~/xamarin-forms/user-interface/images.md#embedded-images) para obter mais informações sobre como acessar os bitmaps armazenados como recursos incorporados.)

Embora o `ImageResourceExtension` exija que a `Source` propriedade seja definida, a `Source` propriedade é indicada em um atributo como a propriedade content da classe. Isso significa que a `Source=` parte da expressão entre chaves pode ser omitida. Na página **demonstração de recurso de imagem** , os `Image` elementos buscam duas imagens usando o nome da pasta e o nome de arquivo separado por pontos:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.ImageResourceDemoPage"
             Title="Image Resource Demo">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Image Source="{local:ImageResource Images.SeatedMonkey.jpg}"
               Grid.Row="0" />

        <Image Source="{local:ImageResource Images.FacePalm.jpg}"
               Grid.Row="1" />

    </Grid>
</ContentPage>
```

Este é o programa em execução:

[![Demonstração do recurso de imagem](creating-images/imageresourcedemo-small.png "Demonstração do recurso de imagem")](creating-images/imageresourcedemo-large.png#lightbox "Demonstração do recurso de imagem")

## <a name="service-providers"></a>Provedores de serviço

Usando o `IServiceProvider` argumento para `ProvideValue` , as extensões de marcação XAML podem obter acesso a informações úteis sobre o arquivo XAML no qual estão sendo usadas. Mas para usar o `IServiceProvider` argumento com êxito, você precisa saber quais tipos de serviços estão disponíveis em contextos específicos. A melhor maneira de entender esse recurso é estudar o código-fonte das extensões de marcação XAML existentes na [pasta **MarkupExtensions** ](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions) no Xamarin.Forms repositório no github. Lembre-se de que alguns tipos de serviços são internos ao Xamarin.Forms .

Em algumas extensões de marcação XAML, esse serviço pode ser útil:

```csharp
 IProvideValueTarget provideValueTarget = serviceProvider.GetService(typeof(IProvideValueTarget)) as IProvideValueTarget;
```

A `IProvideValueTarget` interface define duas propriedades, `TargetObject` e `TargetProperty` . Quando essas informações são obtidas na `ImageResourceExtension` classe, `TargetObject` é o `Image` e `TargetProperty` é um `BindableProperty` objeto para a `Source` propriedade de `Image` . Essa é a propriedade na qual a extensão de marcação XAML foi definida.

A `GetService` chamada com um argumento de `typeof(IProvideValueTarget)` realmente retorna um objeto do tipo `SimpleValueTargetProvider` , que é definido no `Xamarin.Forms.Xaml.Internals` namespace. Se você converter o valor de retorno de `GetService` para esse tipo, também poderá acessar uma `ParentObjects` propriedade, que é uma matriz que contém o `Image` elemento, o `Grid` pai e o `ImageResourceDemoPage` pai do `Grid` .

## <a name="conclusion"></a>Conclusão

As extensões de marcação XAML desempenham uma função vital em XAML, estendendo a capacidade de definir atributos de uma variedade de fontes. Além disso, se as extensões de marcação XAML existentes não fornecerem exatamente o que você precisa, você também poderá escrever suas próprias.

## <a name="related-links"></a>Links relacionados

- [Extensões de marcação (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [Capítulo de extensões de marcação XAML do Xamarin.Forms livro](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
