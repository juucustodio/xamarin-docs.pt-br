---
title: Fallbacks de associação do Xamarin.Forms
description: Este artigo explica como tornar as associações mais robustas definindo valores de fallback que serão usados se a associação falhar.
ms.prod: xamarin
ms.assetid: 637ACD9D-3E5D-4014-86DE-A77D1FEF238A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/16/2018
ms.openlocfilehash: 67fd8070ae36bdc1a90b8a33b25f13369d8d995d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "68650205"
---
# <a name="xamarinforms-binding-fallbacks"></a>Fallbacks de associação do Xamarin.Forms

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

Às vezes, as associações de dados falham porque a origem da associação não pode ser resolvida ou porque a associação tem êxito, mas retorna um valor `null`. Embora esses cenários possam ser manipulados com conversores de valor ou outro código adicional, as associações de dados poderão se tornar mais robustas com a definição de valores de fallback a serem usados se o processo de associação falhar. Isso pode ser feito [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) definindo as propriedades e propriedades em uma expressão vinculante. Como essas propriedades [`BindingBase`](xref:Xamarin.Forms.BindingBase) residem na classe, elas podem ser usadas com `Binding` amarras, vinculações compiladas e com a extensão de marcação.

> [!NOTE]
> O uso [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) das propriedades e em uma expressão de vinculação é opcional.

## <a name="defining-a-fallback-value"></a>Definindo um valor de fallback

A [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) propriedade permite que um valor de recuo seja definido que será usado quando a *fonte* de vinculação não puder ser resolvida. Um cenário comum para definir essa propriedade é ao fazer a associação a propriedades de origem que podem não existir em todos os objetos em uma coleção associada de tipos heterogêneos.

A página **MonkeyDetail** ilustra a definição da propriedade [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue):

```xaml
<Label Text="{Binding Population, FallbackValue='Population size unknown'}"
       ... />   
```

A vinculação [`Label`](xref:Xamarin.Forms.Label) no [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) define um valor que será definido no alvo se a fonte de vinculação não puder ser resolvida. Portanto, o valor definido pela propriedade `FallbackValue` será exibido se a propriedade `Population` não existir no objeto associado. Observe que aqui o valor da propriedade `FallbackValue` é delimitado por caracteres de aspas simples (apóstrofo).

Em vez [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) de definir os valores de propriedade em linha, recomenda-se defini-los como recursos em um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). A vantagem dessa abordagem é que esses valores são definidos uma única vez em uma só localização e são mais facilmente localizáveis. Em seguida, os recursos podem ser recuperados usando a extensão de marcação `StaticResource`:

```xaml
<Label Text="{Binding Population, FallbackValue={StaticResource populationUnknown}}"
       ... />  
```

> [!NOTE]
> Não é possível definir a propriedade `FallbackValue` com uma expressão de associação.

Este é o programa em execução:

![Vinculação de valor de recuo](binding-fallbacks-images/bindingunavailable-detail-cropped.png "Vinculação de valor de recuo")

Quando `FallbackValue` a propriedade não é definida em uma expressão de vinculação e [`BindableProperty.DefaultValue`](xref:Xamarin.Forms.BindableProperty.DefaultValue) o caminho de vinculação ou parte do caminho não é resolvido, é definido no alvo. No entanto, quando a propriedade `FallbackValue` está definida e o caminho de associação ou parte do caminho não está resolvido, o valor da propriedade de valor `FallbackValue` é definido no destino. Portanto, na página **MonkeyDetail**, o [`Label`](xref:Xamarin.Forms.Label) exibe "Tamanho da população desconhecido", porque o objeto associado não tem uma propriedade `Population`.

> [!IMPORTANT]
> Um conversor de valor definido não é [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) executado em uma expressão de vinculação quando a propriedade é definida.

## <a name="defining-a-null-replacement-value"></a>Definindo um valor de substituição nulo

A [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) propriedade permite definir um valor de substituição *source* que será usado quando `null`a fonte de vinculação for resolvida, mas o valor é . Um cenário comum para definir essa propriedade é ao associar as propriedades de origem que podem ser `null` em uma coleção associada.

A página **Monkeys** ilustra a definição da propriedade [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue):

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

As vinculações [`Image`](xref:Xamarin.Forms.Image) e [`Label`](xref:Xamarin.Forms.Label) ambas [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) definem valores que serão `null`aplicados se o caminho de vinculação retornar . Portanto, os valores definidos pelas propriedades `TargetNullValue` serão exibidos para todos os objetos na coleção em que as propriedades `ImageUrl` e `Location` não estiverem definidas. Observe que aqui os valores da propriedade `TargetNullValue` são delimitados por caracteres de aspas simples (apóstrofo).

Em vez [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) de definir os valores de propriedade em linha, recomenda-se defini-los como recursos em um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). A vantagem dessa abordagem é que esses valores são definidos uma única vez em uma só localização e são mais facilmente localizáveis. Em seguida, os recursos podem ser recuperados usando a extensão de marcação `StaticResource`:

```xaml
<Image Source="{Binding ImageUrl, TargetNullValue={StaticResource fallbackImageUrl}}"
       ... />
<Label Text="{Binding Location, TargetNullValue={StaticResource locationUnknown}}"
       ... />
```

> [!NOTE]
> Não é possível definir a propriedade `TargetNullValue` com uma expressão de associação.

Este é o programa em execução:

[![Vinculação de valor anulado de alvo](binding-fallbacks-images/bindingunavailable-small.png "Vinculação de valor anulado de alvo")](binding-fallbacks-images/bindingunavailable-large.png#lightbox "Vinculação de valor anulado de alvo")

Quando a propriedade `TargetNullValue` não estiver definida em uma expressão de associação, um valor de origem igual a `null` será convertido se um conversor de valor for definido, formatado se um `StringFormat` for definido e o resultado será então definido no destino. No entanto, quando a propriedade `TargetNullValue` for definida, um valor de origem igual a `null` será convertido se um conversor de valor for definido e se ele ainda for `null` após a conversão, o valor da propriedade `TargetNullValue` será definido no destino.

> [!IMPORTANT]
> A formatação de cadeia de caracteres não é aplicada em uma expressão de associação quando a propriedade `TargetNullValue` está definida.

## <a name="related-links"></a>Links relacionados

- [Demonstrações de associação de dados (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)
