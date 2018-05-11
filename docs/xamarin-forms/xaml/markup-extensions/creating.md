---
title: Criando extensões de marcação XAML
description: Definir suas próprias extensões de marcação XAML personalizadas
ms.prod: xamarin
ms.assetid: 797C1EF9-1C8E-4208-8610-9B79CCF17D46
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 01/05/2018
ms.openlocfilehash: 3807ac6a91d3bf650922a01d9111dc34513d62b3
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="creating-xaml-markup-extensions"></a>Criando extensões de marcação XAML

No nível de programação, uma extensão de marcação XAML é uma classe que implementa o [ `IMarkupExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.IMarkupExtension/) ou [ `IMarkupExtension<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.IMarkupExtension%3CT%3E/) interface. Você pode explorar o código-fonte das extensões de marcação padrão descrito abaixo no [ **MarkupExtensions** diretório](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions) do repositório do GitHub xamarin. Forms. 

Também é possível definir suas próprias extensões de marcação XAML personalizados derivando de `IMarkupExtension` ou `IMarkupExtension<T>`. Se a extensão de marcação obtém um valor de um tipo específico, use o formulário genérico. Este é o caso com várias extensões de marcação do xamarin. Forms:

- `TypeExtension` derivado de `IMarkupExtension<Type>`
- `ArrayExtension` derivado de `IMarkupExtension<Array>`
- `DynamicResourceExtension` derivado de `IMarkupExtension<DynamicResource>`
- `BindingExtension` derivado de `IMarkupExtension<BindingBase>`
- `ConstraintExpression` derivado de `IMarkupExtension<Constraint>`

Os dois `IMarkupExtension` interfaces definir apenas um método cada, denominado `ProvideValue`: 

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

Como `IMarkupExtension<T>` deriva `IMarkupExtension` e inclui o `new` palavra-chave na `ProvideValue`, ele contém ambos os `ProvideValue` métodos.

Extensões de marcação XAML com frequência, definem as propriedades que contribuem para o valor de retorno. (A exceção óbvia é `NullExtension`, no qual `ProvideValue` simplesmente retorna `null`.) O `ProvideValue` método tem um único argumento de tipo `IServiceProvider` que serão discutidos neste artigo.

## <a name="a-markup-extension-for-specifying-color"></a>Uma extensão de marcação para especificar cores

A seguinte extensão de marcação XAML permite que você construa uma `Color` valor usando componentes de matiz, saturação e luminosidade. Ele define quatro propriedades para os quatro componentes de cor, inclusive um componente alfa que é inicializado como 1. A classe derivada de `IMarkupExtension<Color>` para indicar uma `Color` valor de retorno:

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

Porque `IMarkupExtension<T>` deriva `IMarkupExtension`, a classe deve conter dois `ProvideValue` métodos, que retorna `Color` e outra que retorna `object`, mas o segundo método pode simplesmente chamar o primeiro método.

O **demonstração de cor HSL** página mostra uma variedade de maneiras que `HslColorExtension` pode aparecer em um arquivo XAML para especificar a cor de um `BoxView`:

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

Observe que, quando `HslColorExtension` é uma marca XML, as quatro propriedades são definidas como atributos, mas quando ele aparece entre chaves, as quatro propriedades são separadas por vírgulas sem aspas. Os valores padrão para `H`, `S`, e `L` são 0 e o valor padrão de `A` é 1, portanto, essas propriedades podem ser omitidas se você deseja que eles são definidos como valores padrão. O último exemplo mostra um exemplo onde a luminosidade é 0, que normalmente resulta em preto, mas o canal alfa é 0,5, portanto, ele é metade transparente e aparece cinza no plano de fundo branco da página:

[![Demonstração de cor HSL](creating-images/hslcolordemo-small.png "demonstração de cor HSL")](creating-images/hslcolordemo-large.png#lightbox "demonstração de cor HSL")

## <a name="a-markup-extension-for-accessing-bitmaps"></a>Uma extensão de marcação para acessar os Bitmaps

O argumento `ProvideValue` é um objeto que implementa o [ `IServiceProvider` ](https://developer.xamarin.com/api/type/System.IServiceProvider/) interface, que é definido no .NET `System` namespace. Essa interface possui um membro, um método chamado `GetService` com um `Type` argumento. 

O `ImageResourceExtension` classe mostrado abaixo mostra um uso possíveis de `IServiceProvider` e `GetService` para obter um `IXmlLineInfoProvider` objeto que pode fornecer informações de linhas e de caracteres que indica onde um determinado erro foi detectado. Nesse caso, uma exceção é gerada quando o `Source` propriedade não foi definida:

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

        return ImageSource.FromResource(assemblyName + "." + Source);
    }

    object IMarkupExtension.ProvideValue(IServiceProvider serviceProvider)
    {
        return (this as IMarkupExtension<ImageSource>).ProvideValue(serviceProvider);
    }
}
```

`ImageResourceExtension` é útil quando um arquivo XAML precisa acessar um arquivo de imagem armazenado como um recurso incorporado no projeto da biblioteca .NET padrão. Ele usa o `Source` propriedade chamar estático `ImageSource.FromResource` método. Esse método requer um nome de recurso totalmente qualificado, que consiste o nome do assembly, o nome da pasta e o nome do arquivo separados por pontos. O `ImageResourceExtension` não precisa do nome de assembly parte porque ele obtém o nome do assembly usando a reflexão e adiciona-o para o `Source` propriedade. Independentemente, `ImageSource.FromResource` deve ser chamado a partir do assembly que contém o bitmap, o que significa que essa extensão de recurso XAML não pode ser parte de uma biblioteca externa, a menos que as imagens também estão na biblioteca. (Consulte o [ **imagens inseridas** ](~/xamarin-forms/user-interface/images.md#embedded_images) artigo para obter mais informações sobre como acessar bitmaps armazenados como recursos incorporados.) 

Embora `ImageResourceExtension` requer o `Source` propriedade a ser definida, o `Source` propriedade é indicada em um atributo, como a propriedade de conteúdo da classe. Isso significa que o `Source=` parte da expressão entre chaves pode ser omitido. No **demonstração de recurso de imagem** página, o `Image` elementos buscar duas imagens usando o nome de pasta e o nome do arquivo separados por pontos:

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

Aqui está o programa em execução em todas as três plataformas:

[![Demonstração de recurso de imagem](creating-images/imageresourcedemo-small.png "demonstração de recurso de imagem")](creating-images/imageresourcedemo-large.png#lightbox "demonstração de recurso de imagem")

## <a name="service-providers"></a>Provedores de serviços

Usando o `IServiceProvider` argumento `ProvideValue`, extensões de marcação XAML podem obter acesso a informações úteis sobre o arquivo XAML no qual está sendo usados. Mas, para usar o `IServiceProvider` argumento com êxito, você precisa saber que tipo de serviços estão disponíveis em contextos específicos. A melhor maneira de entender esse recurso é estudando o código-fonte das extensões de marcação XAML existentes no [ **MarkupExtensions** pasta](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions) no repositório no GitHub xamarin. Forms. Lembre-se de que alguns tipos de serviços são internos ao xamarin. Forms.

Em algumas extensões de marcação XAML, esse serviço pode ser útil:

```csharp
 IProvideValueTarget provideValueTarget = serviceProvider.GetService(typeof(IProvideValueTarget)) as IProvideValueTarget;
```

O `IProvideValueTarget` interface define duas propriedades, `TargetObject` e `TargetProperty`. Quando essas informações são obtidas no `ImageResourceExtension` classe `TargetObject` é o `Image` e `TargetProperty` é um `BindableProperty` de objeto para o `Source` propriedade de `Image`. Essa é a propriedade na qual a extensão de marcação XAML foi definida.

O `GetService` chamada com um argumento de `typeof(IProvideValueTarget)` , na verdade, retorna um objeto do tipo `SimpleValueTargetProvider`, que é definido no `Xamarin.Forms.Xaml.Internals` namespace. Se você converter o valor de retorno `GetService` para esse tipo, você também pode acessar um `ParentObjects` propriedade, que é uma matriz que contém o `Image` elemento, o `Grid` pai e o `ImageResourceDemoPage` pai do `Grid`.

## <a name="conclusion"></a>Conclusão

Extensões de marcação XAML desempenham um papel importante em XAML, estendendo a capacidade de definir os atributos de uma variedade de fontes. Além disso, se as extensões de marcação XAML existentes não fornecerem exatamente o que você precisa, você também pode escrever seus próprios. 


## <a name="related-links"></a>Links relacionados

- [Extensões de marcação (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/)
- [Capítulo de extensões de marcação XAML do catálogo xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
