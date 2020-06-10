---
Título: "Resumo do capítulo 5. Lidando com tamanhos "Descrição:" Criando aplicativos móveis com Xamarin.Forms : Resumo do capítulo 5. Lidando com tamanhos "MS. Prod: xamarin MS. Technology: xamarin-Forms MS. AssetID: 486800E9-C09F-4B95-9AC2-C0F8FE563BCF autor: davidbritch MS. Author: dabritch MS. Date: 07/19/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="summary-of-chapter-5-dealing-with-sizes"></a>Resumo do capítulo 5. Lidar com tamanhos

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)

> [!NOTE]
> As observações nesta página indicam áreas em que Xamarin.Forms houve uma divergência do material apresentado no livro.

Vários tamanhos no Xamarin.Forms foram encontrados até agora:

- A altura da barra de status do iOS é 20
- O `BoxView` tem uma largura e uma altura padrão de 40
- O padrão `Padding` em a `Frame` é 20
- O padrão `Spacing` no `StackLayout` é 6
- O `Device.GetNamedSize` método retorna um tamanho de fonte numérico

Esses tamanhos não são pixels. Em vez disso, elas são unidades independentes de dispositivo reconhecidas independentemente por cada plataforma.

## <a name="pixels-points-dps-dips-and-dius"></a>Pixels, pontos, DPS, DIPs e DIUs

No início dos históricos do Apple Mac e do Microsoft Windows, os programadores trabalharam em unidades de pixels. No entanto, o advento das telas de resolução superior exigia uma abordagem mais virtualizada e abstrata para as coordenadas da tela. No mundo do Mac, os programadores trabalharam em unidades de *pontos*, tradicionalmente 1/72 polegadas, enquanto os desenvolvedores do Windows usavam DIUs ( *unidades independentes de dispositivo* ) com base em 1/96 polegadas.

Os dispositivos móveis, no entanto, geralmente são mantidos muito mais próximos da face e têm uma resolução mais alta do que as telas da área de trabalho, o que implica que uma densidade de pixel maior pode ser tolerada.

Os programadores visando dispositivos Apple iPhone e iPad continuam a funcionar em unidades de *pontos*, mas há 160 desses pontos em polegadas. Dependendo do dispositivo, pode haver 1, 2 ou 3 pixels até o ponto.

O Android é semelhante. Os programadores trabalham em unidades de pontos *independentes de densidade* (DPS) e a relação entre o DPS e os pixels é baseada no dps de 160 a polegada.

Windows phones e dispositivos móveis também estabeleceram fatores de dimensionamento que implicam algo perto de 160 unidades independentes de dispositivo para a polegada.

> [!NOTE]
> Xamarin.FormsNão dá mais suporte a qualquer telefone baseado no Windows ou dispositivo móvel.

Em resumo, um Xamarin.Forms programador que visa telefones e tablets pode assumir que todas as unidades de medida se baseiam no seguinte critério:

- 160 unidades para a polegada, equivalente a
- 64 unidades para o centímetro

As propriedades somente leitura [`Width`](xref:Xamarin.Forms.VisualElement.Width) e [`Height`](xref:Xamarin.Forms.VisualElement.Height) definidas por `VisualElement` têm valores de "imitação" padrão de &ndash; 1. Somente quando um elemento tiver sido dimensionado e acomodado no layout, essas propriedades refletirão o tamanho real do elemento em unidades independentes do dispositivo. Esse tamanho inclui qualquer `Padding` conjunto no elemento, mas não no `Margin` .

Um elemento visual aciona o [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged) evento quando seu `Width` ou `Height` foi alterado. O exemplo [**WhatSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/WhatSize) usa esse evento para exibir o tamanho da tela do programa.

## <a name="metrical-sizes"></a>Tamanhos de métricas

O [**MetricalBoxView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/MetricalBoxView) usa [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) e [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) para exibir uma `BoxView` polegada de altura e um centímetro de largura.

## <a name="estimated-font-sizes"></a>Tamanhos de fonte estimados

O exemplo de [**fontsizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FontSizes) mostra como usar a regra 160-unidades para a polegada para especificar tamanhos de fonte em unidades de pontos. A consistência visual entre as plataformas que usam essa técnica é melhor do que a `Device.GetNamedSize` .

## <a name="fitting-text-to-available-size"></a>Ajustando o texto ao tamanho disponível

É possível ajustar um bloco de texto dentro de um retângulo específico, calculando um `FontSize` dos `Label` seguintes critérios:

- O espaçamento de linha é de 120% do tamanho da fonte (130% nas plataformas do Windows).
- A largura média do caractere é de 50% do tamanho da fonte.

O exemplo [**EstimatedFontSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EstimatedFontSize) demonstra essa técnica. Este programa foi gravado antes da [`Margin`](xref:Xamarin.Forms.View.Margin) Propriedade estar disponível, portanto, ele usa um [`ContentView`](xref:Xamarin.Forms.ContentView) com uma [`Padding`](xref:Xamarin.Forms.Layout.Padding) configuração para simular uma margem.

[![Captura de tela tripla do tamanho estimado da fonte](images/ch05fg07-small.png "O texto se ajusta ao tamanho disponível")](images/ch05fg07-large.png#lightbox "O texto se ajusta ao tamanho disponível")

## <a name="a-fit-to-size-clock"></a>Um relógio ajustado para o tamanho

O exemplo [**FitToSizeClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FitToSizeClock) demonstra como usar o [`Device.StartTimer`](xref:Xamarin.Forms.Device.StartTimer(System.TimeSpan,System.Func{System.Boolean})) para iniciar um temporizador que notifica periodicamente o aplicativo quando é hora de atualizar o relógio. O tamanho da fonte é definido como um sexto da largura da página para tornar a exibição o mais grande possível.

## <a name="accessibility-issues"></a>Problemas de acessibilidade

O programa **EstimatedFontSize** e o programa **FitToSizeClock** contêm uma falha sutil: se o usuário alterar as configurações de acessibilidade do telefone no Android ou no Windows 10 Mobile, o programa não poderá mais estimar o quão grande o texto é renderizado com base no tamanho da fonte. O exemplo [**AccessibilityTest**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/AccessibilityTest) demonstra esse problema.

## <a name="empirically-fitting-text"></a>Texto de ajuste de empiricamente

Outra maneira de ajustar o texto a um retângulo é empiricamente calcular o tamanho do texto renderizado e ajustá-lo para cima ou para baixo. O programa no livro chama [`GetSizeRequest`](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double)) um elemento visual para obter o tamanho desejado do elemento. Esse método foi preterido e, em vez disso, os programas devem chamar [ `Measure` ] (xref: Xamarin.Forms . Visualelement. Measure (System. Double, System. Double, Xamarin.Forms . MeasureFlags)).

Para um `Label` , o primeiro argumento deve ser a largura do contêiner (para permitir o encapsulamento), enquanto o segundo argumento deve ser definido como `Double.PositiveInfinity` para tornar a altura irrestrita. O exemplo [**EmpiricalFontSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EmpiricalFontSize) demonstra essa técnica.

## <a name="related-links"></a>Links relacionados

- [Capítulo 5 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch05-Apr2016.pdf)
- [Exemplos do capítulo 5](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)
- [Capítulo 5 amostras de F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FS)
