---
Título: Xamarin.Forms Descrição de "fallbacks de associação": "Este artigo explica como tornar as associações mais robustas definindo valores de fallback que serão usados se a associação falhar."
MS. Prod: xamarin MS. AssetID: 637ACD9D-3E5D-4014-86DE-A77D1FEF238A MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 08/16/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-binding-fallbacks"></a>Xamarin.FormsFallbacks de associação

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

Às vezes, as associações de dados falham porque a origem da associação não pode ser resolvida ou porque a associação tem êxito, mas retorna um valor `null`. Embora esses cenários possam ser manipulados com conversores de valor ou outro código adicional, as associações de dados poderão se tornar mais robustas com a definição de valores de fallback a serem usados se o processo de associação falhar. Isso pode ser feito definindo as [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) Propriedades e [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) em uma expressão de associação. Como essas propriedades residem na [`BindingBase`](xref:Xamarin.Forms.BindingBase) classe, elas podem ser usadas com associações, associações compiladas e com a `Binding` extensão de marcação.

> [!NOTE]
> O uso das [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) Propriedades e em uma expressão de associação é opcional.

## <a name="defining-a-fallback-value"></a>Definindo um valor de fallback

A [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) propriedade permite que um valor de fallback seja definido, que será usado quando a *origem* da associação não puder ser resolvida. Um cenário comum para definir essa propriedade é ao fazer a associação a propriedades de origem que podem não existir em todos os objetos em uma coleção associada de tipos heterogêneos.

A página **MonkeyDetail** ilustra a definição da propriedade [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue):

```xaml
<Label Text="{Binding Population, FallbackValue='Population size unknown'}"
       ... />   
```

A associação em [`Label`](xref:Xamarin.Forms.Label) define um [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) valor que será definido no destino se a origem da associação não puder ser resolvida. Portanto, o valor definido pela propriedade `FallbackValue` será exibido se a propriedade `Population` não existir no objeto associado. Observe que aqui o valor da propriedade `FallbackValue` é delimitado por caracteres de aspas simples (apóstrofo).

Em vez de definir [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) valores de propriedade embutidos, é recomendável defini-los como recursos em um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . A vantagem dessa abordagem é que esses valores são definidos uma única vez em uma só localização e são mais facilmente localizáveis. Em seguida, os recursos podem ser recuperados usando a extensão de marcação `StaticResource`:

```xaml
<Label Text="{Binding Population, FallbackValue={StaticResource populationUnknown}}"
       ... />  
```

> [!NOTE]
> Não é possível definir a propriedade `FallbackValue` com uma expressão de associação.

Este é o programa em execução:

![Associação de FallbackValue](binding-fallbacks-images/bindingunavailable-detail-cropped.png "Associação de FallbackValue")

Quando a `FallbackValue` propriedade não está definida em uma expressão de associação e o caminho de associação ou parte do caminho não é resolvido, [`BindableProperty.DefaultValue`](xref:Xamarin.Forms.BindableProperty.DefaultValue) é definido no destino. No entanto, quando a propriedade `FallbackValue` está definida e o caminho de associação ou parte do caminho não está resolvido, o valor da propriedade de valor `FallbackValue` é definido no destino. Portanto, na página **MonkeyDetail**, o [`Label`](xref:Xamarin.Forms.Label) exibe "Tamanho da população desconhecido", porque o objeto associado não tem uma propriedade `Population`.

> [!IMPORTANT]
> Um conversor de valor definido não é executado em uma expressão de associação quando a [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) propriedade é definida.

## <a name="defining-a-null-replacement-value"></a>Definindo um valor de substituição nulo

A [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) propriedade permite que um valor de substituição seja definido, que será usado quando a *origem* da associação for resolvida, mas o valor for `null` . Um cenário comum para definir essa propriedade é ao associar as propriedades de origem que podem ser `null` em uma coleção associada.

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

As associações no [`Image`](xref:Xamarin.Forms.Image) e [`Label`](xref:Xamarin.Forms.Label) ambos definem [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) valores que serão aplicados se o caminho de associação retornar `null` . Portanto, os valores definidos pelas propriedades `TargetNullValue` serão exibidos para todos os objetos na coleção em que as propriedades `ImageUrl` e `Location` não estiverem definidas. Observe que aqui os valores da propriedade `TargetNullValue` são delimitados por caracteres de aspas simples (apóstrofo).

Em vez de definir [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) valores de propriedade embutidos, é recomendável defini-los como recursos em um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . A vantagem dessa abordagem é que esses valores são definidos uma única vez em uma só localização e são mais facilmente localizáveis. Em seguida, os recursos podem ser recuperados usando a extensão de marcação `StaticResource`:

```xaml
<Image Source="{Binding ImageUrl, TargetNullValue={StaticResource fallbackImageUrl}}"
       ... />
<Label Text="{Binding Location, TargetNullValue={StaticResource locationUnknown}}"
       ... />
```

> [!NOTE]
> Não é possível definir a propriedade `TargetNullValue` com uma expressão de associação.

Este é o programa em execução:

[![Associação de TargetNullValue](binding-fallbacks-images/bindingunavailable-small.png "Associação de TargetNullValue")](binding-fallbacks-images/bindingunavailable-large.png#lightbox "Associação de TargetNullValue")

Quando a propriedade `TargetNullValue` não estiver definida em uma expressão de associação, um valor de origem igual a `null` será convertido se um conversor de valor for definido, formatado se um `StringFormat` for definido e o resultado será então definido no destino. No entanto, quando a propriedade `TargetNullValue` for definida, um valor de origem igual a `null` será convertido se um conversor de valor for definido e se ele ainda for `null` após a conversão, o valor da propriedade `TargetNullValue` será definido no destino.

> [!IMPORTANT]
> A formatação de cadeia de caracteres não é aplicada em uma expressão de associação quando a propriedade `TargetNullValue` está definida.

## <a name="related-links"></a>Links relacionados

- [Demonstrações de associação de dados (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)
