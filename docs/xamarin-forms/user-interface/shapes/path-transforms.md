---
title: 'Xamarin.Forms Formas: transformações de caminho'
description: Uma Xamarin.Forms transformação define como transformar um objeto de caminho de um espaço de coordenadas para outro espaço de coordenadas.
ms.prod: xamarin
ms.assetid: 07DE3D66-1820-4642-BDDF-84146D40C99D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/02/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6bc61eadb40a66c278955936e15158f34709fd18
ms.sourcegitcommit: aeefe667156ca8d3ee2ee21d9bec0fa629589e33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90928647"
---
# <a name="no-locxamarinforms-shapes-path-transforms"></a>Xamarin.Forms Formas: transformações de caminho

![API de pré-lançamento](~/media/shared/preview.png)

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

Um `Transform` define como transformar um `Path` objeto de um espaço de coordenadas em outro espaço de coordenadas. Quando uma transformação é aplicada a um `Path` objeto, ela altera como o objeto é renderizado na interface do usuário.

As transformações podem ser categorizadas em quatro classificações gerais: rotação, dimensionamento, distorção e tradução. Xamarin.Forms define uma classe para cada uma dessas classificações de transformação:

- `RotateTransform`, que gira um `Path` por um especificado `Angle` .
- `ScaleTransform`, que dimensiona um `Path` objeto por especificado `ScaleX` e por `ScaleY` valores.
- `SkewTransform`, que inclina um `Path` objeto por especificado `AngleX` e `AngleY` valores.
- `TranslateTransform`, que move um `Path` objeto por `X` valores especificados e `Y` .

Xamarin.Forms também fornece as seguintes classes para criar transformações mais complexas:

- `TransformGroup`, que representa uma transformação composta composta por vários objetos de transformação.
- `CompositeTransform`, que aplica várias operações de transformação a um `Path` objeto.
- `MatrixTransform`, que cria transformações personalizadas que não são fornecidas pelas outras classes de transformação.

Todas essas classes derivam da `Transform` classe, que define uma `Value` Propriedade do tipo `Matrix` , que representa a transformação atual como um `Matrix` objeto. Essa propriedade é apoiada por um [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objeto, o que significa que ele pode ser o destino de associações de dados e com estilo. Para obter mais informações sobre a `Matrix` estrutura, consulte [transformar matriz](#transform-matrix).

Para aplicar uma transformação a um `Path` , crie uma classe de transformação e defina-a como o valor da `Path.RenderTransform` propriedade.

## <a name="rotation-transform"></a>Transformação de rotação

Uma transformação girar gira um `Path` objeto no sentido horário sobre um ponto especificado em um sistema de coordenadas x-y 2D.

A `RotateTransform` classe, que deriva da `Transform` classe, define as seguintes propriedades:

- `Angle`, do tipo `double` , representa o ângulo, em graus, da rotação horária. O valor padrão dessa propriedade é 0,0.
- `CenterX`, do tipo `double` representa a coordenada x do ponto central de rotação. O valor padrão dessa propriedade é 0,0.
- `CenterY`, do tipo `double` representa a coordenada y do ponto central de rotação. O valor padrão dessa propriedade é 0,0.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

As `CenterX` `CenterY` Propriedades e especificam o ponto sobre o qual o `Path` objeto é girado. Esse ponto central é expresso no espaço de coordenadas do objeto que é transformado. Por padrão, a rotação é aplicada a (0, 0), que é o canto superior esquerdo do `Path` objeto.

O exemplo a seguir mostra como girar um `Path` objeto:

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <RotateTransform CenterX="0"
                         CenterY="0"
                         Angle="45" />
    </Path.RenderTransform>
</Path>
```

Neste exemplo, o `Path` objeto é girado 45 graus sobre seu canto superior esquerdo.

## <a name="scale-transform"></a>Transformação de escala

Uma transformação de escala dimensiona um `Path` objeto no sistema de coordenadas 2D x-y.

A `ScaleTransform` classe, que deriva da `Transform` classe, define as seguintes propriedades:

- `ScaleX`, do tipo `double` , que representa o fator de escala do eixo x. O valor padrão dessa propriedade é 1,0.
- `ScaleY`, do tipo `double` , que representa o fator de escala do eixo y. O valor padrão dessa propriedade é 1,0.
- `CenterX`, do tipo `double` , que representa a coordenada x do ponto central dessa transformação. O valor padrão dessa propriedade é 0,0.
- `CenterY`, do tipo `double` , que representa a coordenada y do ponto central dessa transformação. O valor padrão dessa propriedade é 0,0.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

O valor de `ScaleX` e `ScaleY` tem um grande impacto sobre o dimensionamento resultante:

- Os valores entre 0 e 1 diminuem a largura e a altura do objeto dimensionado.
- Valores maiores que 1 aumentam a largura e a altura do objeto dimensionado.
- Os valores de 1 indicam que o objeto não está dimensionado.
- Valores negativos ingiram o objeto de escala horizontal e verticalmente.
- Os valores entre 0 e-1 invertem o objeto de escala e diminuem sua largura e altura.
- Valores menores que-1 invertem o objeto e aumentam sua largura e altura.
- Os valores de-1 invertem o objeto dimensionado, mas não alteram seu tamanho horizontal ou vertical.

As `CenterX` `CenterY` Propriedades e especificam o ponto sobre o qual o `Path` objeto é dimensionado. Esse ponto central é expresso no espaço de coordenadas do objeto que é transformado. Por padrão, o dimensionamento é aplicado a (0, 0), que é o canto superior esquerdo do `Path` objeto. Isso tem o efeito de mover o `Path` objeto e fazê-lo parecer maior, porque ao aplicar uma transformação, você altera o espaço de coordenadas no qual o `Path` objeto reside.

O exemplo a seguir mostra como dimensionar um `Path` objeto:

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <ScaleTransform CenterX="0"
                        CenterY="0"
                        ScaleX="1.5"
                        ScaleY="1.5" />
    </Path.RenderTransform>
</Path>
```

Neste exemplo, o `Path` objeto é dimensionado para 1,5 vezes o tamanho.

## <a name="skew-transform"></a>Transformação de distorção

Uma transformação de distorção inclina um `Path` objeto no sistema de coordenadas 2D x-y e é útil para criar a ilusão de profundidade 3D em um objeto 2D.

A `SkewTransform` classe, que deriva da `Transform` classe, define as seguintes propriedades:

- `AngleX`, do tipo `double` , que representa o ângulo de inclinação do eixo x, que é medido em graus no sentido anti-horário do eixo y. O valor padrão dessa propriedade é 0,0.
- `AngleY`, do tipo `double` , que representa o ângulo de inclinação do eixo y, que é medido em graus no sentido anti-horário a partir do eixo x. O valor padrão dessa propriedade é 0,0.
- `CenterX`, do tipo `double` , que representa a coordenada x do centro de transformação. O valor padrão dessa propriedade é 0,0.
- `CenterY`, do tipo `double` , que representa a coordenada y do centro de transformação. O valor padrão dessa propriedade é 0,0.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

Para prever o efeito de uma transformação de distorção, considere que o `AngleX` distorce valores do eixo x em relação ao sistema de coordenadas original. Portanto, para um `AngleX` de 30, o eixo y gira 30 graus pela origem e distorce os valores em x por 30 graus dessa origem. Da mesma forma, um `AngleY` de 30 inclina os valores y do `Path` objeto por 30 graus da origem.

> [!NOTE]
> Para distorcer um `Path` objeto no local, defina as `CenterX` `CenterY` Propriedades e como o ponto central do objeto.

O exemplo a seguir mostra como distorcer um `Path` objeto:

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <SkewTransform CenterX="0"
                       CenterY="0"
                       AngleX="45"
                       AngleY="0" />
    </Path.RenderTransform>
</Path>
```

Neste exemplo, uma skew horizontal de 45 graus é aplicada ao `Path` objeto, de um ponto central de (0, 0).

## <a name="translate-transform"></a>Converter transformação

Uma transformação de conversão move um objeto no sistema de coordenadas x-y 2D.

A `TranslateTransform` classe, que deriva da `Transform` classe, define as seguintes propriedades:

- `X`, do tipo `double` , que representa a distância a ser movida ao longo do eixo x. O valor padrão dessa propriedade é 0,0.
- `Y`, do tipo `double` , que representa a distância a ser movida ao longo do eixo y. O valor padrão dessa propriedade é 0,0.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

`X`Valores negativos movem um objeto para a esquerda, enquanto valores positivos movem um objeto para a direita. `Y`Valores negativos movem um objeto para cima, enquanto valores positivos movem um objeto para baixo.

O exemplo a seguir mostra como converter um `Path` objeto:

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <TranslateTransform X="50"
                            Y="50" />
    </Path.RenderTransform>
</Path>
```

Neste exemplo, o `Path` objeto é movido 50 unidades independentes do dispositivo para a direita e 50 as unidades independentes do dispositivo.

## <a name="multiple-transforms"></a>Várias transformações

Xamarin.Forms tem duas classes que dão suporte à aplicação de várias transformações em um `Path` objeto. São eles `TransformGroup` , e `CompositeTransform` . Um `TransformGroup` executa transformações em qualquer ordem desejada, enquanto um `CompositeTransform` executa transformações em uma ordem específica.

### <a name="transform-groups"></a>Transformar grupos

Grupos de transformação representam transformações compostas compostas de vários `Transform` objetos.

A `TransformGroup` classe, que deriva da `Transform` classe, define uma `Children` propriedade, do tipo `TransformCollection` , que representa uma coleção de `Transform` objetos. Essa propriedade é apoiada por um [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objeto, o que significa que ele pode ser o destino de associações de dados e com estilo.

A ordem das transformações é importante em uma transformação composta que usa a `TransformGroup` classe. Por exemplo, girar, ajustar a escala e mover terá um resultado diferente de mover, girar e ajustar a escala. Uma ordem de motivo é significativa é que as transformações como rotação e dimensionamento são executadas em relação à origem do sistema de coordenadas. Dimensionar um objeto que é centralizado na origem produz um resultado diferente para dimensionar um objeto que foi movido para fora da origem. Da mesma forma, girar um objeto centralizado na origem produz um resultado diferente de girar um objeto movido para fora da origem.

O exemplo a seguir mostra como executar uma transformação composta usando a `TransformGroup` classe:

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <TransformGroup>
            <ScaleTransform ScaleX="1.5"
                            ScaleY="1.5" />
            <RotateTransform Angle="45" />
        </TransformGroup>
    </Path.RenderTransform>
</Path>
```

Neste exemplo, o `Path` objeto é dimensionado para 1,5 vezes seu tamanho e, em seguida, girado por 45 graus.

## <a name="composite-transforms"></a>Transformações de composição

Uma transformação composta aplica várias transformações a um objeto.

A `CompositeTransform` classe, que deriva da `Transform` classe, define as seguintes propriedades:

- `CenterX`, do tipo `double` , que representa a coordenada x do ponto central dessa transformação. O valor padrão dessa propriedade é 0,0.
- `CenterY`, do tipo `double` , que representa a coordenada y do ponto central dessa transformação. O valor padrão dessa propriedade é 0,0.
- `ScaleX`, do tipo `double` , que representa o fator de escala do eixo x. O valor padrão dessa propriedade é 1,0.
- `ScaleY`, do tipo `double` , que representa o fator de escala do eixo y. O valor padrão dessa propriedade é 1,0.
- `SkewX`, do tipo `double` , que representa o ângulo de inclinação do eixo x, que é medido em graus no sentido anti-horário do eixo y. O valor padrão dessa propriedade é 0,0.
- `SkewY`, do tipo `double` , que representa o ângulo de inclinação do eixo y, que é medido em graus no sentido anti-horário a partir do eixo x. O valor padrão dessa propriedade é 0,0.
- `Rotation`, do tipo `double` , representa o ângulo, em graus, da rotação horária. O valor padrão dessa propriedade é 0,0.
- `TranslateX`, do tipo `double` , que representa a distância a ser movida ao longo do eixo x. O valor padrão dessa propriedade é 0,0.
- `TranslateY`, do tipo `double` , que representa a distância a ser movida ao longo do eixo y. O valor padrão dessa propriedade é 0,0.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

Um `CompositeTransform` aplica transformações nesta ordem:

1. Escala ( `ScaleX` e `ScaleY` ).
1. Skew ( `SkewX` e `SkewY` ).
1. Girar ( `Rotation` ).
1. Translate ( `TranslateX` , `TranslateY` ).

Se você quiser aplicar várias transformações a um objeto em uma ordem diferente, deverá criar um `TransformGroup` e inserir as transformações em sua ordem pretendida.

> [!IMPORTANT]
> Um `CompositeTransform` usa os mesmos pontos centrais `CenterX` e `CenterY` , para todas as transformações. Se você quiser especificar diferentes pontos centrais por transformação, use um `TransformGroup` ,

O exemplo a seguir mostra como executar uma transformação composta usando a `CompositeTransform` classe:

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <CompositeTransform ScaleX="1.5"
                            ScaleY="1.5"
                            Rotation="45"
                            TranslateX="50"
                            TranslateY="50" />
    </Path.RenderTransform>
</Path>
```

Neste exemplo, o `Path` objeto é dimensionado para 1,5 vezes seu tamanho, girado por 45 graus e, em seguida, traduzido por 50 unidades independentes de dispositivo.

## <a name="transform-matrix"></a>Transformar matriz

Uma transformação pode ser descrita em termos de matriz de transformação afim de 3x3, que executa transformações em espaço 2D. Essa matriz 3x3 é representada pela `Matrix` struct, que é uma coleção de três linhas e três colunas de `double` valores.

A `Matrix` estrutura define as seguintes propriedades:

- `Determinant`, do tipo `double` , que obtém o determinante da matriz.
- `HasInverse`, do tipo `bool` , que indica se a matriz é invertível.
- `Identity`, do tipo `Matrix` , que obtém uma matriz de identidade.
- `HasIdentity`, do tipo `bool` , que indica se a matriz é uma matriz de identidade.
- `M11`, do tipo `double` , que representa o valor da primeira linha e a primeira coluna da matriz.
- `M12`, do tipo `double` , que representa o valor da primeira linha e segunda coluna da matriz.
- `M21`, do tipo `double` , que representa o valor da segunda linha e a primeira coluna da matriz.
- `M22`, do tipo `double` , que representa o valor da segunda linha e segunda coluna da matriz.
- `OffsetX`, do tipo `double` , que representa o valor da terceira linha e da primeira coluna da matriz.
- `OffsetY`, do tipo `double` , que representa o valor da terceira linha e segunda coluna da matriz.

As `OffsetX` `OffsetY` Propriedades e são tão nomeadas porque especificam o valor para converter o espaço de coordenadas ao longo do eixo x e eixo y, respectivamente.

Além disso, a `Matrix` estrutura expõe uma série de métodos que podem ser usados para manipular os valores de matriz, `Append` incluindo `Invert` , `Multiply` , `Prepend` e muitos outros.

A tabela a seguir mostra a estrutura de uma Xamarin.Forms matriz:

:::row:::
    :::column:::
        M11
    :::column-end:::
    :::column:::
        M12
    :::column-end:::
    :::column:::
        0,0
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        M21
    :::column-end:::
    :::column:::
        M22
    :::column-end:::
    :::column:::
        0,0
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        OffsetX
    :::column-end:::
    :::column:::
        OffsetY
    :::column-end:::
    :::column:::
        1,0
    :::column-end:::
:::row-end:::

> [!NOTE]
> Uma matriz de transformação afim tem sua coluna final igual a (0, 0, 1), portanto, somente os membros nas duas primeiras colunas precisam ser especificados.

Ao manipular valores de matriz, você pode girar, dimensionar, inclinar e converter `Path` objetos. Por exemplo, se você alterar o `OffsetX` valor para 100, poderá usá-lo mover um `Path` objeto 100 unidades independentes de dispositivo ao longo do eixo x. Se você alterar o `M22` valor para 3, poderá usá-lo para alongar um `Path` objeto para três vezes sua altura atual. Se você alterar os dois valores, moverá o `Path` objeto 100 unidades independentes de dispositivo ao longo do eixo x e ampliará sua altura por um fator de 3. Além disso, as matrizes de transformação afim podem ser multiplicadas para formar qualquer número de transformações lineares, como rotação e distorção, seguida de tradução.

## <a name="custom-transforms"></a>Transformações personalizadas

A `MatrixTransform` classe, que deriva da `Transform` classe, define uma `Matrix` propriedade, do tipo `Matrix` , que representa a matriz que define a transformação. Essa propriedade é apoiada por um [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objeto, o que significa que ele pode ser o destino de associações de dados e com estilo.

Qualquer transformação que você possa descrever com um `TranslateTransform` `ScaleTransform` objeto,, `RotateTransform` ou `SkewTransform` pode ser descrita igualmente por um `MatrixTransform` . No entanto, as `TranslateTransform` `ScaleTransform` classes,, `RotateTransform` e `SkewTransform` são mais fáceis de conceituar do que definir os componentes de vetor em um `Matrix` . Portanto, a `MatrixTransform` classe é normalmente usada para criar transformações personalizadas que não são fornecidas pelas `RotateTransform` `ScaleTransform` classes,, `SkewTransform` ou `TranslateTransform` .

O exemplo a seguir mostra como transformar um `Path` objeto usando um `MatrixTransform` :

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <MatrixTransform>
            <MatrixTransform.Matrix>
                <!-- M11 stretches, M12 skews -->
                <Matrix OffsetX="10"
                        OffsetY="100"
                        M11="1.5"
                        M12="1" />
            </MatrixTransform.Matrix>
        </MatrixTransform>
    </Path.RenderTransform>
</Path>
```

Neste exemplo, o `Path` objeto é alongado, inclinado e deslocado nas dimensões X e Y.

Como alternativa, isso pode ser escrito em um formulário simplificado que usa um conversor de tipo interno do Xamarin.Forms :

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <MatrixTransform Matrix="1.5,1,0,1,10,100" />
    </Path.RenderTransform>
</Path>
```

Neste exemplo, a `Matrix` propriedade é especificada como uma cadeia de caracteres delimitada por vírgula que consiste em seis membros:,,,,, `M11` `M12` `M21` `M22` `OffsetX` `OffsetY` . Embora os membros sejam delimitados por vírgulas neste exemplo, eles também podem ser delimitados por um ou mais espaços.

Além disso, o exemplo anterior pode ser simplificado ainda mais, especificando os mesmos seis membros que o valor da `RenderTransform` Propriedade:

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      RenderTransform="1.5 1 0 1 10 100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z" />
```

## <a name="related-links"></a>Links relacionados

- [ShapeDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms Existentes](index.md)
