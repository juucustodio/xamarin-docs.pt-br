---
title: Fallbacks de associação do xamarin. Forms
description: Este artigo explica como fazer ligações mais robusto, definindo valores de retorno que serão usados se a associação falhar.
ms.prod: xamarin
ms.assetid: 637ACD9D-3E5D-4014-86DE-A77D1FEF238A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/16/2018
ms.openlocfilehash: 2a4b29df9148ce695f8f3ca5377e5848af1b775a
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171593"
---
# <a name="xamarinforms-binding-fallbacks"></a>Fallbacks de associação do xamarin. Forms

Às vezes, as associações de dados falharem, porque a origem da associação não pode ser resolvida ou porque a associação terá êxito, mas retorna um `null` valor. Enquanto esses cenários podem ser tratados com conversores de valor ou outro código adicional, associações de dados podem tornar mais robustas, definindo valores de fallback a ser usado se o processo de associação falhará. Isso pode ser feito definindo a [ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue) e [ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue) propriedades em uma expressão de associação. Como essas propriedades residem na [ `BindingBase` ](xref:Xamarin.Forms.BindingBase) classe, pode ser usados com associações, associações compiladas e com o `Binding` extensão de marcação.

> [!NOTE]
> Usar o [ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue) e [ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue) propriedades em uma expressão de associação é opcional.

## <a name="defining-a-fallback-value"></a>Definindo um valor de fallback

O [ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue) propriedade permite que um valor de fallback seja definido que será usado quando a associação *origem* não pode ser resolvido. Um cenário comum para definir essa propriedade é ao associar a propriedades de fonte que podem não existir em todos os objetos em uma coleção associada de tipos heterogêneos.

O **MonkeyDetail** página ilustra a configuração do [ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue) propriedade:

```xaml
<Label Text="{Binding Population, FallbackValue='Population size unknown'}"
       ... />   
```

A associação na [ `Label` ](xref:Xamarin.Forms.Label) define um [ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue) valor será definido no destino se a origem da associação não pode ser resolvida. Portanto, o valor definido pelos `FallbackValue` propriedade será exibida se o `Population` propriedade não existe no objeto ligado. Observe que aqui o `FallbackValue` valor da propriedade é delimitado por caracteres de aspas simples (apóstrofo).

Em vez de definir [ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue) valores de propriedade em linha, é recomendável defini-las como recursos em um [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary). A vantagem dessa abordagem é que esses valores são definidos uma vez em um único local e são mais facilmente localizáveis. Os recursos, em seguida, podem ser recuperados usando o `StaticResource` extensão de marcação:

```xaml
<Label Text="{Binding Population, FallbackValue={StaticResource populationUnknown}}"
       ... />  
```

> [!NOTE]
> Não é possível definir o `FallbackValue` propriedade com uma expressão de associação.

Aqui está o programa em execução:

![Associação de FallbackValue](binding-fallbacks-images/bindingunavailable-detail-cropped.png "FallbackValue associação")

Quando o `FallbackValue` propriedade não está definida em uma expressão de associação e o caminho de associação ou parte do caminho não for resolvido, [ `BindableProperty.DefaultValue` ](xref:Xamarin.Forms.BindableProperty.DefaultValue) é definido no destino. No entanto, quando o `FallbackValue` propriedade está definida e o caminho de associação ou parte do caminho não for resolvido, o valor da `FallbackValue` propriedade value é definida no destino. Portanto, nos **MonkeyDetail** página de [ `Label` ](xref:Xamarin.Forms.Label) exibe "Tamanho da população desconhecido", porque o objeto associado não tem um `Population` propriedade.

> [!IMPORTANT]
> Um conversor de valor definido não é executado em uma expressão de associação quando o [ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue) propriedade está definida.

## <a name="defining-a-null-replacement-value"></a>Definindo um valor de substituição de nulo

O [ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue) propriedade permite que um valor de substituição seja definido será usado quando a associação *fonte* for resolvido, mas o valor é `null`. Um cenário comum para definir essa propriedade é ao associar a propriedades de fonte que podem ser `null` em uma coleção associada.

O **Monkeys (Macacos)** página ilustra a configuração do [ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue) propriedade:

```xaml
<ListView ItemsSource="{Binding Monkeys}"
          ...>
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <Grid>
                    ...
                    <Image Source="{Binding ImageUrl, TargetNullValue='https://upload.wikimedia.org/wikipedia/commons/2/20/Point_d_interrogation.jpg'}"
                           ... />
                    ...
                    <Label Text="{Binding Location, TargetNullValue='Location unknown'}"
                           ... />
                </Grid>
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

As associações na [ `Image` ](xref:Xamarin.Forms.Image) e [ `Label` ](xref:Xamarin.Forms.Label) ambos definem [ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue) valores que serão aplicadas se o caminho de associação retorna `null`. Portanto, os valores definidos pela `TargetNullValue` propriedades serão exibidas para todos os objetos na coleção em que o `ImageUrl` e `Location` propriedades não estão definidas. Observe que aqui o `TargetNullValue` valores de propriedade são delimitados por caracteres de aspas simples (apóstrofo).

Em vez de definir [ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue) valores de propriedade em linha, é recomendável defini-las como recursos em um [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary). A vantagem dessa abordagem é que esses valores são definidos uma vez em um único local e são mais facilmente localizáveis. Os recursos, em seguida, podem ser recuperados usando o `StaticResource` extensão de marcação:

```xaml
<Image Source="{Binding ImageUrl, TargetNullValue={StaticResource fallbackImageUrl}}"
       ... />
<Label Text="{Binding Location, TargetNullValue={StaticResource locationUnknown}}"
       ... />
```

> [!NOTE]
> Não é possível definir o `TargetNullValue` propriedade com uma expressão de associação.

Aqui está o programa em execução:

[![Associação de TargetNullValue](binding-fallbacks-images/bindingunavailable-small.png "associação TargetNullValue")](binding-fallbacks-images/bindingunavailable-large.png#lightbox "TargetNullValue associação")

Quando o `TargetNullValue` propriedade não estiver definida em uma expressão de associação, um valor de source `null` será convertido se for definido um conversor de valor formatado se um `StringFormat` é definido, e o resultado é definido, em seguida, no destino. No entanto, quando o `TargetNullValue` propriedade for definida, um valor de source `null` será convertido se um conversor de valor for definido, e se ele ainda `null` após a conversão, o valor da `TargetNullValue` propriedade é definida no destino.

> [!IMPORTANT]
> Formatação de cadeia de caracteres não é aplicada em uma expressão de associação quando o `TargetNullValue` propriedade está definida.

## <a name="related-links"></a>Links relacionados

- [Demonstrações de associação de dados (amostra)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
