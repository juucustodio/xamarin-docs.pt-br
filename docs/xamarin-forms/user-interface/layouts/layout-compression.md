---
Título: "compactação de layout" Descrição: "a compactação de layout remove layouts especificados da árvore visual em uma tentativa de melhorar o desempenho de renderização de página. Este artigo explica como habilitar a compactação de layout e os benefícios que ele pode trazer. "
MS. Prod: xamarin MS. AssetID: da9e1b26-9d31-4762-94c3-4039f306b7f2 MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 12/13/2017 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="layout-compression"></a>Compactação de Layout

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layoutcompression)

_A compactação de layout remove os layouts especificados da árvore visual em uma tentativa de melhorar o desempenho de renderização de página. Este artigo explica como habilitar a compactação de layout e os benefícios que ele pode trazer._

## <a name="overview"></a>Visão geral

Xamarin.Formsexecuta o layout usando duas séries de chamadas de método recursivos:

- O layout começa na parte superior da árvore visual com uma página e prossegue por todas as ramificações da árvore visual para abranger todos os elementos visuais em uma página. Elementos que são pais para outros elementos são responsáveis por dimensionar e posicionar seus filhos em relação a si mesmos.
- Invalidação é o processo pelo qual uma alteração em um elemento em uma página dispara um novo ciclo de layout. Os elementos são considerados inválidos quando não têm mais o tamanho ou a posição corretos. Cada elemento na árvore visual que tem filhos é alertado sempre que um de seus filhos muda de tamanho. Portanto, uma alteração no tamanho de um elemento na árvore visual pode causar alterações que propagam a árvore.

Para obter mais informações sobre como o Xamarin.Forms realiza o layout, consulte [criando um layout personalizado](~/xamarin-forms/user-interface/layouts/custom.md).

O resultado do processo de layout é uma hierarquia de controles nativos. No entanto, essa hierarquia inclui renderizadores de contêiner adicionais e wrappers para renderizadores de plataforma, o que torna ainda mais simples o aninhamento da hierarquia de exibição. Quanto mais profundo for o nível de aninhamento, maior será a quantidade de trabalho a ser Xamarin.Forms executada para exibir uma página. Para layouts complexos, a hierarquia de exibição pode ser profunda e ampla, com vários níveis de aninhamento.

Por exemplo, considere o seguinte botão do aplicativo de exemplo para fazer logon no Facebook:

![](layout-compression-images/facebook-button.png "Facebook Button")

Esse botão é especificado como um controle personalizado com a seguinte hierarquia de exibição XAML:

```xaml
<ContentView ...>
    <StackLayout>
        <StackLayout ...>
            <AbsoluteLayout ...>
                <Button ... />    
                <Image ... />
                <Image ... />
                <BoxView ... />
                <Label ... />
                <Button ... />
            </AbsoluteLayout>
        </StackLayout>
        <Label ... />
    </StackLayout>    
</ContentView>
```

A hierarquia de exibição aninhada resultante pode ser examinada com [Xamarin Inspector](~/tools/inspector/index.md). No Android, a hierarquia de exibição aninhada contém 17 exibições:

![](layout-compression-images/no-compression.png "View Hierarchy for Facebook Button")

A compactação de layout, que está disponível para Xamarin.Forms aplicativos nas plataformas Ios e Android, tem como objetivo mesclar o aninhamento da exibição removendo os layouts especificados da árvore visual, o que pode melhorar o desempenho da renderização de página. O benefício de desempenho que é entregue varia dependendo da complexidade de uma página, da versão do sistema operacional que está sendo usada e do dispositivo no qual o aplicativo está sendo executado. No entanto, os maiores ganhos de desempenho serão observados em versões mais antigas.

> [!NOTE]
> Embora este artigo se concentre nos resultados da aplicação da compactação de layout no Android, ele é igualmente aplicável ao iOS.

## <a name="layout-compression"></a>Compactação de Layout

Em XAML, a compactação de layout pode ser habilitada definindo a `CompressedLayout.IsHeadless` Propriedade anexada como `true` em uma classe de layout:

```xaml
<StackLayout CompressedLayout.IsHeadless="true">
  ...
</StackLayout>   
```

Como alternativa, ele pode ser habilitado em C# especificando a instância de layout como o primeiro argumento para o `CompressedLayout.SetIsHeadless` método:

```csharp
CompressedLayout.SetIsHeadless(stackLayout, true);
```

> [!IMPORTANT]
> Como a compactação de layout remove um layout da árvore visual, ela não é adequada para layouts que têm uma aparência visual ou que obtêm entrada por toque. Portanto, os layouts que definem [`VisualElement`](xref:Xamarin.Forms.VisualElement) Propriedades (como [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) ,, [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) , [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) e [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) ou que aceitam gestos não são candidatos à compactação de layout. No entanto, habilitar a compactação de layout em um layout que define as propriedades de aparência visual ou que aceita gestos não resultará em um erro de compilação ou tempo de execução. Em vez disso, a compactação de layout será aplicada e as propriedades de aparência visual e o reconhecimento de gesto falharão silenciosamente.

Para o botão do Facebook, a compactação de layout pode ser habilitada nas três classes de layout:

```xaml
<StackLayout CompressedLayout.IsHeadless="true">
    <StackLayout CompressedLayout.IsHeadless="true" ...>
        <AbsoluteLayout CompressedLayout.IsHeadless="true" ...>
            ...
        </AbsoluteLayout>
    </StackLayout>
    ...
</StackLayout>  
```

No Android, isso resulta em uma hierarquia de exibição aninhada de 14 exibições:

![](layout-compression-images/layout-compression.png "View Hierarchy for Facebook Button with Layout Compression")

Em comparação com a hierarquia de exibição aninhada original de 17 exibições, isso representa uma redução no número de exibições de 17%. Embora essa redução possa parecer insignificante, a redução da exibição em uma página inteira pode ser mais significativa.

### <a name="fast-renderers"></a>Renderizadores Rápidos

Renderizadores rápidos reduzem os custos de inflação e renderização de Xamarin.Forms controles no Android, nivelando a hierarquia de exibição nativa resultante. Isso melhora ainda mais o desempenho criando menos objetos, o que, por sua vez, resulta em uma árvore visual menos complexa e menor uso de memória. Para obter mais informações sobre renderizadores rápidos, consulte [renderizadores rápidos](~/xamarin-forms/internals/fast-renderers.md).

Para o botão do Facebook no aplicativo de exemplo, a combinação de compactação de layout e renderizadores rápidos produz uma hierarquia de exibição aninhada de 8 exibições:

![](layout-compression-images/layout-compression-with-fast-renderers.png "View Hierarchy for Facebook Button with Layout Compression and Fast Renderers")

Em comparação com a hierarquia de exibição aninhada original de 17 exibições, isso representa uma redução de 52%.

O aplicativo de exemplo contém uma página extraída de um aplicativo real. Sem a compactação de layout e os renderizadores rápidos, a página produz uma hierarquia de exibição aninhada de 130 exibições no Android. Habilitar renderizadores rápidos e compactação de layout em classes de layout apropriadas reduz a hierarquia de exibição aninhada para 70 exibições, uma redução de 46%.

## <a name="summary"></a>Resumo

A compactação de layout remove os layouts especificados da árvore visual em uma tentativa de melhorar o desempenho de renderização de página. O benefício de desempenho que isso oferece varia dependendo da complexidade de uma página, da versão do sistema operacional que está sendo usado e do dispositivo no qual o aplicativo está sendo executado. No entanto, os maiores ganhos de desempenho serão observados em versões mais antigas.

## <a name="related-links"></a>Links relacionados

- [Criar um layout personalizado](~/xamarin-forms/user-interface/layouts/custom.md)
- [Renderizadores Rápidos](~/xamarin-forms/internals/fast-renderers.md)
- [LayoutCompression (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layoutcompression)
