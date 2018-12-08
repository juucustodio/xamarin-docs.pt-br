---
title: Compactação de Layout
description: Compactação de layout remove os layouts especificados da árvore visual em uma tentativa de melhorar o desempenho de renderização da página. Este artigo explica como habilitar a compactação de layout e os benefícios que ele pode trazer.
ms.prod: xamarin
ms.assetid: da9e1b26-9d31-4762-94c3-4039f306b7f2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/13/2017
ms.openlocfilehash: 4609593337cc0d003c5b35ef4570f11e27b14f7b
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53050394"
---
# <a name="layout-compression"></a>Compactação de Layout

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/userinterface/layoutcompression/)

_Compactação de layout remove os layouts especificados da árvore visual em uma tentativa de melhorar o desempenho de renderização da página. Este artigo explica como habilitar a compactação de layout e os benefícios que ele pode trazer._

## <a name="overview"></a>Visão geral

Xamarin. Forms executa usando duas séries de chamadas de método recursiva de layout:

- Layout começa na parte superior da árvore visual com uma página, e ele passa todas as ramificações da árvore visual para abranger todos os elementos visuais em uma página. Elementos que são pais de outros elementos são responsáveis por redimensionar e posicionar seus filhos em relação a mesmos.
- Invalidação de é o processo pelo qual uma alteração em um elemento em uma página dispara um novo ciclo de layout. Elementos são considerados inválidos quando eles não terão mais o tamanho correto ou a posição. Todos os elementos na árvore visual que tem filhos é alertado sempre que um de seus filhos altera tamanhos. Portanto, uma alteração no tamanho de um elemento na árvore visual pode causar alterações ripple acima na árvore.

Para obter mais informações sobre como o xamarin. Forms executa o layout, consulte [criação de um Layout personalizado](~/xamarin-forms/user-interface/layouts/custom.md).

O resultado do processo de layout é uma hierarquia de controles nativos. No entanto, essa hierarquia inclui os renderizadores de contêiner adicional e wrappers para renderizadores de plataforma, aumentando ainda mais a hierarquia de exibição de aninhamento. O maior o nível de aninhamento, maior a quantidade de trabalho que o xamarin. Forms deve executar para exibir uma página. Para layouts complexos, a hierarquia de exibição pode ser profundo e abrangente, com vários níveis de aninhamento.

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

A hierarquia de exibição aninhada resultante poderá ser examinada com [Xamarin Inspector](~/tools/inspector/index.md). No Android, a hierarquia de exibição aninhada contém exibições de 17:

![](layout-compression-images/no-compression.png "Hierarquia de exibição do botão do Facebook")

Compactação de layout, que está disponível para aplicativos xamarin. Forms nas plataformas Android e iOS, tem como objetivo para mesclar a exibição de aninhamento, removendo os layouts especificados da árvore visual, que pode melhorar o desempenho de renderização da página. O benefício de desempenho que é fornecido varia dependendo da complexidade de uma página, a versão do sistema operacional que está sendo usado e o dispositivo no qual o aplicativo está em execução. No entanto, os maiores ganhos de desempenho serão observados em versões mais antigas.

> [!NOTE]
> Embora este artigo se concentra nos resultados da aplicação de compactação de layout no Android, é igualmente aplicável para iOS.

## <a name="layout-compression"></a>Compactação de Layout

No XAML, a compactação de layout pode ser habilitada definindo o `CompressedLayout.IsHeadless` anexado à propriedade `true` em uma classe de layout:

```xaml
<StackLayout CompressedLayout.IsHeadless="true">
  ...
</StackLayout>   
```

Como alternativa, ele pode ser habilitado em c#, especificando a instância de layout como o primeiro argumento para o `CompressedLayout.SetIsHeadless` método:

```csharp
CompressedLayout.SetIsHeadless(stackLayout, true);
```

> [!IMPORTANT]
> Uma vez que a compactação de layout remove um layout da árvore visual, não é adequado para layouts que têm uma aparência visual, ou que obtêm entrada por toque. Portanto, layouts que defina [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) propriedades (como [ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor), [ `IsVisible` ](xref:Xamarin.Forms.VisualElement.IsVisible), [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation), [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale), [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX) e [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY) ou que aceitar gestos, não são candidatos para o layout compactação. No entanto, a habilitação da compactação de layout em um layout que define as propriedades de aparência visual, ou que aceita a gestos, não resultará em um erro de compilação ou tempo de execução. Em vez disso, compactação de layout será aplicada e as propriedades de aparência visual e reconhecimento de gesto, falharão silenciosamente.

Para o botão do Facebook, compactação de layout pode ser habilitada nas classes de layout de três:

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

![](layout-compression-images/layout-compression.png "Hierarquia de exibição para o botão do Facebook com compactação de Layout")

Em comparação com a hierarquia de exibição aninhada original das exibições de 17, isso representa uma redução no número de modos de exibição de 17%. Embora essa redução pode aparecer insignificante, a redução de modo de exibição ao longo de uma página inteira pode ser mais significativa.

### <a name="fast-renderers"></a>Renderizadores Rápidos

Renderizadores rápidos reduzem os custos de renderização de controles do xamarin. Forms no Android e inflação nivelando a hierarquia resultante de modo nativo. Isso aprimora o desempenho criando menos objetos, que por sua vez, resulta em uma árvore visual menos complexa e menos uso de memória. Para obter mais informações sobre renderizadores rápidos, consulte [os renderizadores rápidos](~/xamarin-forms/internals/fast-renderers.md).

Para o botão do Facebook no aplicativo de exemplo, a combinação de compactação de layout e os renderizadores rápidos produz uma hierarquia de exibição aninhada de exibições de 8:

![](layout-compression-images/layout-compression-with-fast-renderers.png "Hierarquia de exibição do botão do Facebook com compactação de Layout e os renderizadores rápidos")

Em comparação com a hierarquia de exibição aninhada original das exibições de 17, isso representa uma redução de 52%.

O aplicativo de exemplo contém uma página extraída de um aplicativo real. Sem compactação de layout e os renderizadores rápidos, a página produz uma hierarquia de exibição aninhada de 130 exibições no Android. Habilitando os renderizadores rápidos e compactação de layout em classes de layout adequado reduz a hierarquia de exibição aninhada a 70 modos de exibição, uma redução de 46%.

## <a name="summary"></a>Resumo

Compactação de layout remove os layouts especificados da árvore visual em uma tentativa de melhorar o desempenho de renderização da página. O benefício de desempenho que isso oferece varia dependendo da complexidade de uma página, da versão do sistema operacional que está sendo usado e do dispositivo no qual o aplicativo está sendo executado. No entanto, os maiores ganhos de desempenho serão observados em versões mais antigas.


## <a name="related-links"></a>Links relacionados

- [Criar um layout personalizado](~/xamarin-forms/user-interface/layouts/custom.md)
- [Renderizadores Rápidos](~/xamarin-forms/internals/fast-renderers.md)
- [LayoutCompression (amostra)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/layoutcompression/)
