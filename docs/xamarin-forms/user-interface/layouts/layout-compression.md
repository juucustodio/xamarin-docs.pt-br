---
title: "Compactação de Layout"
description: "Compactação de layout remove especificados layouts da árvore visual em uma tentativa de melhorar o desempenho de renderização da página. Este artigo explica como habilitar a compactação de layout e os benefícios que ela pode colocar."
ms.topic: article
ms.prod: xamarin
ms.assetid: da9e1b26-9d31-4762-94c3-4039f306b7f2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/13/2017
ms.openlocfilehash: e03acbbac737bffd21ee3b592ab017d227f822ad
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="layout-compression"></a>Compactação de Layout

_Compactação de layout remove especificados layouts da árvore visual em uma tentativa de melhorar o desempenho de renderização da página. Este artigo explica como habilitar a compactação de layout e os benefícios que ela pode colocar._

## <a name="overview"></a>Visão geral

Xamarin. Forms executa layout usando duas séries de chamadas de método recursiva:

- Layout começa na parte superior da árvore visual de uma página, e ele passa todas as ramificações da árvore visual para abranger todos os elementos visuais em uma página. Elementos que são pais de outros elementos são responsáveis por dimensionamento e posicionamento de seus filhos em relação a mesmos.
- Invalidação é o processo pelo qual uma alteração em um elemento em uma página dispara um novo ciclo de layout. Elementos são considerados inválidos quando eles não tem mais o tamanho correto ou a posição. Cada elemento na árvore visual que possui filhos é alertado sempre que um de seus filhos tamanhos é alterado. Portanto, uma alteração no tamanho de um elemento na árvore visual pode causar alterações de ondulação a árvore.

Para obter mais informações sobre como o xamarin. Forms executa o layout, consulte [criar um Layout personalizado](~/xamarin-forms/user-interface/layouts/custom.md).

O resultado do processo de layout é uma hierarquia de controles nativos. No entanto, essa hierarquia inclui renderizadores de contêiner adicionais e wrappers para renderizadores de plataforma, aumentando ainda mais a exibir hierarquia de aninhamento. Quanto maior o nível de aninhamento, maior a quantidade de trabalho xamarin. Forms precisa executar para exibir uma página. Para layouts complexos, a hierarquia de exibição pode ser profundo e abrangente, com vários níveis de aninhamento.

Por exemplo, considere o seguinte botão do aplicativo de exemplo para fazer logon no Facebook:

![](layout-compression-images/facebook-button.png "Botão do Facebook")

Esse botão é especificado como um controle personalizado com a hierarquia de exibição XAML a seguir:

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

A hierarquia de exibição aninhada resultante pode ser examinada com [Xamarin Inspetor](~/tools/inspector/index.md). No Android, a hierarquia de exibição aninhada contém 17 exibições:

![](layout-compression-images/no-compression.png "Exibir hierarquia de botão do Facebook")

Compactação de layout, que está disponível para xamarin. Forms aplicativos nas plataformas Android e iOS, tem como objetivo para mesclar a exibição de aninhamento removendo especificados layouts da árvore visual, o que pode melhorar o desempenho de renderização da página. O benefício de desempenho que é fornecido varia dependendo da complexidade de uma página, a versão do sistema operacional que está sendo usado e o dispositivo no qual o aplicativo está sendo executado. No entanto, os maiores ganhos de desempenho serão observados em versões mais antigas.

> [!NOTE]
> Embora este artigo enfoca os resultados da aplicação de compactação de layout no Android, é igualmente aplicável para iOS.

## <a name="layout-compression"></a>Compactação de Layout

Em XAML, a compactação de layout pode ser habilitada definindo o `CompressedLayout.IsHeadless` anexado a propriedade `true` em uma classe de layout:

```xaml
<StackLayout CompressedLayout.IsHeadless="true">
  ...
</StackLayout>   
```

Como alternativa, ela pode ser habilitada em c#, especificando a instância do layout como o primeiro argumento para o `CompressedLayout.SetIsHeadless` método:

```csharp
CompressedLayout.SetIsHeadless(stackLayout, true);
```

> [!IMPORTANT]
> Como a compactação de layout remove um layout da árvore visual, não é adequado para layouts que tem uma aparência visual ou que obter entrada por toque. Portanto, layouts que definir [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/) propriedades (como [ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/), [ `IsVisible` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsVisible/), [ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/), [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/), [ `TranslationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/) e [ `TranslationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/)) ou aceitar gestos não são candidatos para layout compactação. No entanto, a habilitação da compactação de layout em um layout que define as propriedades de aparência visual ou que aceita gestos, não resultará em um erro de compilação ou tempo de execução. Em vez disso, será aplicada a compactação de layout e propriedades de aparência visual e reconhecimento de gesto, serão recusadas.

Para o botão do Facebook, compactação de layout pode ser habilitada nas classes de três layout:

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

![](layout-compression-images/layout-compression.png "Exibir hierarquia de botão do Facebook com compactação de Layout")

Em comparação com a hierarquia de exibição aninhada de 17 exibições, isso representa uma redução no número de modos de exibição de 17%. Apesar dessa redução pode aparecer insignificante, a redução de exibição em uma página inteira pode ser mais significativa.

### <a name="fast-renderers"></a>Renderizadores Rápidos

Processadores rápidos reduzem os custos de renderização de controles xamarin. Forms no Android e inflação mesclar a hierarquia resultante de modo nativo. Isso aprimora o desempenho criando menos objetos, que por sua vez, resulta em uma árvore visual menos complexa e menos uso de memória. Para obter mais informações sobre renderizadores rápidas, consulte [processadores rápidos](~/xamarin-forms/internals/fast-renderers.md).

Para o botão do Facebook no aplicativo de amostra, combinar renderizadores rápidos e compactação de layout produz uma hierarquia de exibição aninhada de 8 exibições:

![](layout-compression-images/layout-compression-with-fast-renderers.png "Exibir hierarquia de botão do Facebook com compactação de Layout e processadores rápidos")

Em comparação com a hierarquia de exibição aninhada de 17 exibições, isso representa uma redução de 52%.

O aplicativo de exemplo contém uma página extraída de um aplicativo real. Sem compactação de layout e renderizadores rápidos, a página produz uma hierarquia aninhada de exibição de 130 exibições no Android. Habilitando renderizadores rápidos e compactação de layout em classes de layout apropriada reduz a hierarquia de exibição aninhada a 70 modos de exibição, uma redução de 46%.

## <a name="summary"></a>Resumo

Compactação de layout remove especificados layouts da árvore visual em uma tentativa de melhorar o desempenho de renderização da página. O benefício de desempenho que isso oferece varia dependendo da complexidade de uma página, da versão do sistema operacional que está sendo usado e do dispositivo no qual o aplicativo está sendo executado. No entanto, os maiores ganhos de desempenho serão observados em versões mais antigas.


## <a name="related-links"></a>Links relacionados

- [Criar um layout personalizado](~/xamarin-forms/user-interface/layouts/custom.md)
- [Renderizadores Rápidos](~/xamarin-forms/internals/fast-renderers.md)
- [LayoutCompression (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/layoutcompression/)
