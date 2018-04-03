---
title: Desenho de geometria com CCDrawNode
description: CCDrawNode fornece métodos para desenho primitivas de objetos, como linhas, círculos e triângulos.
ms.topic: article
ms.prod: xamarin
ms.assetid: 46A3C3CE-74CC-4A3A-AB05-B694AE182ADB
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/24/2017
ms.openlocfilehash: 5a2471981f2e88ff8af9a803ff8f5a99e5b9266f
ms.sourcegitcommit: 4f1b508caa8e7b6ccf85d167ea700a5d28b0347e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2018
---
# <a name="drawing-geometry-with-ccdrawnode"></a>Desenho de geometria com CCDrawNode

_`CCDrawNode` fornece métodos para desenho primitivas de objetos, como linhas, círculos e triângulos._

O `CCDrawNode` classe CocosSharp fornece vários métodos para desenhar formas geométricas comuns. Ele herda o `CCNode` de classe e geralmente é adicionado à `CCLayer` instâncias. Este guia aborda como usar `CCDrawNode` instâncias para executar processamento personalizado. Ele também fornece uma lista abrangente das funções de desenho disponível com capturas de tela e exemplos de código.


## <a name="creating-a-ccdrawnode"></a>Criando um CCDrawNode

O `CCDrawNode` classe pode ser usada para desenhar objetos geométricos como círculos, retângulos e linhas. Por exemplo, o exemplo de código a seguir mostra como criar um `CCDrawNode` instância que desenha um círculo em uma `CCLayer` a implementação da classe:


```csharp
public class GameLayer : CCLayer
{
    public GameLayer ()
    {
        var drawNode = new CCDrawNode ();
        this.AddChild (drawNode);
        // Origin is bottom-left of the screen. This moves
        // the drawNode 100 pixels to the right and 100 pixels up
        drawNode.PositionX = 100;
        drawNode.PositionY = 100;

        drawNode.DrawCircle (
            center: new CCPoint (0, 0),
            radius: 20,
            color: CCColor4B.White);

    }
} 
```

Esse código produz o seguinte círculo em tempo de execução:

![](ccdrawnode-images/image1.png "Esse código gera este círculo em tempo de execução")


## <a name="draw-method-details"></a>Detalhes de método Draw

Vamos dar uma olhada em alguns detalhes relacionados ao desenho com um `CCDrawNode`:


### <a name="draw-methods-positions-are-relative-to-the-ccdrawnode"></a>Posições de métodos de desenho estão em relação a CCDrawNode

Todos os métodos de desenho requerem o valor de pelo menos uma posição para o desenho. Esse valor de posição é relativo a `CCDrawNode` instância. Isso significa que o `CCDrawNode` tem uma posição, e todos os desenhar chamadas feitas a `CCDrawNode` também levar um ou mais valores de posição. Para ajudar a compreender como esses valores são combinados, vamos examinar alguns exemplos.

Primeiro vamos examinar o `DrawCircle` exemplo acima:


```csharp
...
drawNode.PositionX = 100;
drawNode.PositionY = 100;

drawNode.DrawCircle (center: new CCPoint (0, 0),
...
```

Nesse caso, o `CCDrawNode` está posicionado em (100,100), e o círculo desenhado em (0,0) relativo a `CCDrawNode`, resultando no círculo sendo centralizados 100 pixels até e à direita do canto inferior esquerdo da tela do jogo.

O `CCDrawNode` também podem ser posicionados na origem (parte inferior esquerda da tela), contar com o círculo de deslocamentos de:


```csharp
...
drawNode.PositionX = 0;
drawNode.PositionY = 0;

drawNode.DrawCircle (center: new CCPoint (50, 60),
...
```

O código acima resulta no centro do círculo 50 unidades (`drawNode.PositionX` + o `CCPoint.X`) à direita do lado esquerdo da tela e 60 (`drawNode.PositionY` + o `CCPoint.Y`) unidades acima da parte inferior da tela.

Depois que um método draw foi chamado, o objeto de desenho não pode ser modificado, a menos que o `CCDrawNode.Clear` método é chamado, portanto, qualquer reposicionamento precisa ser feito no `CCDrawNode` em si.

Objetos desenhados por `CCNodes` também são afetadas pelo `CCNode` da instância `Rotation` e `Scale` propriedades.


### <a name="draw-methods-do-not-need-to-be-called-every-frame"></a>Métodos de desenho não precisam ser chamado em cada quadro

Métodos de desenho precisam ser chamado apenas uma vez para criar um visual persistente. No exemplo acima, a chamada para `DrawCircle` no construtor do `GameLayer` – `DrawCircle` não precisa ser chamado a cada quadro para desenhar novamente o círculo quando a tela é atualizada.

Isso é diferente de métodos de desenho no MonoGame, que normalmente irá renderizar algo na tela para apenas um quadro e deve ser chamado a cada quadro.

Se os métodos de desenho são chamados de todos os quadros e eventualmente acumularão objetos dentro de chamada `CCDrawNode` instância, resultando em uma queda na taxa de quadros conforme mais objetos são desenhados.


### <a name="each-ccdrawnode-supports-multiple-draw-calls"></a>Cada CCDrawNode dá suporte a várias chamadas de desenho

`CCDrawNode` instâncias podem ser usadas para desenhar várias formas. Isso permite que os objetos de visual complexos ser encapsulada em um único objeto. Por exemplo, o código a seguir pode ser usado para processar vários círculos, com um `CCDrawNode`:


```csharp
for (int i = 0; i < 8; i++)
{
    drawNode.DrawCircle (
        center: new CCPoint (i*15, 0),
        radius: 20,
        color: CCColor4B.White);
} 
```

Isso resulta no gráfico a seguir:

![](ccdrawnode-images/image2.png "Isso resulta neste gráfico")


## <a name="draw-call-examples"></a>Exemplos de chamada de desenho

As chamadas de desenho a seguir estão disponíveis em `CCDrawNode`:

- [`DrawCatmullRom`](#drawcatmullrom)
- [`DrawCircle`](#drawcircle)
- [`DrawCubicBezier`](#drawcubicbezier)
- [`DrawEllipse`](#drawellipse)
- [`DrawLineList`](#drawlinelist)
- [`DrawPolygon`](#drawpolygon)
- [`DrawQuadBezier`](#drawquadbezier)
- [`DrawRect`](#drawrect)
- [`DrawSegment`](#drawsegment)
- [`DrawSolidArc`](#drawsolidarc)
- [`DrawSolidCircle`](#drawsolidcircle)
- [`DrawTriangleList`](#drawtrianglelist)


### <a name="drawcardinalspline"></a>DrawCardinalSpline

`DrawCardinalSpline` cria uma linha curva por meio de um número variável de pontos. 

O `config` parâmetro define quais pontos de spline sejam passados. O exemplo a seguir mostra um spline que passa por quatro pontos.

O `tension` parâmetro controla como sustenido ou round os pontos de spline aparecem. Um `tension` valor de 0 resultará em uma curva spline e um `tension` valor 1 resultará em um spline desenhada por linhas retas e bordas de disco rígidas.

Embora splines linhas curvas, CocosSharp desenha splines com linhas retas. O `segments` parâmetro controla quantos segmentos para usar para desenhar o spline. Um número maior resulta em uma curva suave spline às custas do desempenho. 

Exemplo de código:


```csharp
var splinePoints = new List<CCPoint> ();
splinePoints.Add (new CCPoint (0, 0));
splinePoints.Add (new CCPoint (50, 70));
splinePoints.Add (new CCPoint (0, 140));
splinePoints.Add (new CCPoint (100, 210));

drawNode.DrawCardinalSpline (
    config: splinePoints,
    tension: 0,
    segments: 64,
    color:CCColor4B.Red); 
```

![](ccdrawnode-images/image3.png "O parâmetro de segmentos controla quantos segmentos para usar para desenhar o spline")


### <a name="drawcatmullrom"></a>DrawCatmullRom

`DrawCatmullRom` cria uma linha curva por meio de um número variável de pontos, semelhante a `DrawCardinalLine`. Esse método não inclui um parâmetro de tensão.

Exemplo de código:

```csharp
var splinePoints = new List<CCPoint> ();
splinePoints.Add (new CCPoint (0, 0));
splinePoints.Add (new CCPoint (80, 90));
splinePoints.Add (new CCPoint (100, 0));
splinePoints.Add (new CCPoint (0, 130)); 

drawNode.DrawCatmullRom (
    points: splinePoints,
    segments: 64); 
```

![](ccdrawnode-images/image4.png "DrawCatmullRom cria uma linha curva por meio de um número variável de pontos, semelhantes a DrawCardinalLine")


### <a name="drawcircle"></a>DrawCircle

`DrawCircle` cria um perímetro de um círculo de um determinado `radius`.

Exemplo de código:

```csharp
drawNode.DrawCircle (
    center:new CCPoint (0, 0),
    radius:20,
    color:CCColor4B.Yellow); 
```

![](ccdrawnode-images/image5.png "DrawCircle cria um perímetro de um círculo de um determinado radius")


### <a name="drawcubicbezier"></a>DrawCubicBezier

`DrawCubicBezier` Desenha uma linha curva entre dois pontos, usando os pontos de controle para definir o caminho entre dois pontos.

Exemplo de código:

```csharp
drawNode.DrawCubicBezier (
    origin: new CCPoint (0, 0),
    control1: new CCPoint (50, 150),
    control2: new CCPoint (250, 150),
    destination: new CCPoint (170, 0),
    segments: 64,
    lineWidth: 1,
    color: CCColor4B.Green); 
```

 ![](ccdrawnode-images/image6.png "DrawCubicBezier desenha uma linha curva entre dois pontos")


### <a name="drawellipse"></a>DrawEllipse

`DrawEllipse` cria a estrutura de um *elipse*, que é conhecido como uma elipse (embora as duas não forem idênticas geometricamente). A forma da elipse pode ser definida por um `CCRect` instância.

Exemplo de código:


```csharp
drawNode.DrawEllipse (
    rect: new CCRect (0, 0, 130, 90),
    lineWidth: 2,
    color: CCColor4B.Gray); 
```

![](ccdrawnode-images/image8.png "DrawEllipse cria a estrutura de tópicos de uma elipse, que é conhecida como uma elipse")


### <a name="drawline"></a>DrawLine

`DrawLine` conecta-se aos pontos com uma linha de uma determinada largura. Esse método é semelhante ao `DrawSegment`, exceto que ele cria pontos de extremidade simples em vez de pontos de extremidade arredondados.

Exemplo de código:


```csharp
drawNode.DrawLine (
    from: new CCPoint (0, 0),
    to: new CCPoint (150, 30),
    lineWidth: 5,
    color:CCColor4B.Orange); 
```

![](ccdrawnode-images/image9.png "DrawLine conecta-se aos pontos com uma linha de uma determinada largura")


### <a name="drawlinelist"></a>DrawLineList

`DrawLineList` cria várias linhas conectando cada par de pontos especificado por um `CCV3F_C4B` matriz. O `CCV3F_C4B` struct contém valores de posição e cor. O `verts` parâmetro sempre deve conter um número par de pontos, como cada linha é definida por dois pontos.

Exemplo de código:


```csharp
CCV3F_C4B[] verts = new CCV3F_C4B[] {
    // First line:
    new CCV3F_C4B( new CCPoint(0,0), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(30,60), CCColor4B.White),
    // second line, will blend from white to red:
    new CCV3F_C4B( new CCPoint(60,0), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(120,120), CCColor4B.Red)
};

drawNode.DrawLineList (verts); 
```

 ![](ccdrawnode-images/image10.png "O parâmetro verts sempre deve conter um número par de pontos, como cada linha é definida por dois pontos")




### <a name="drawpolygon"></a>DrawPolygon

`DrawPolygon` cria um polígono preenchido com uma estrutura de tópicos de largura variável e cor.

Exemplo de código:


```csharp
CCPoint[] verts = new CCPoint[] {
    new CCPoint(0,0),
    new CCPoint(0, 100),
    new CCPoint(50, 150),
    new CCPoint(100, 100),
    new CCPoint(100, 0)
};

drawNode.DrawPolygon (verts,
    count: verts.Length,
    fillColor: CCColor4B.White,
    borderWidth: 5,
    borderColor: CCColor4B.Red,
    closePolygon: true); 
```

![](ccdrawnode-images/image11.png "DrawPolygon cria um polígono preenchido com uma estrutura de tópicos de largura variável e cor")


### <a name="drawquadbezier"></a>DrawQuadBezier

`DrawQuadBezier` conecta os dois pontos com uma linha. Ele se comporta da mesma forma que `DrawCubicBezier` , mas só oferece suporte a um único ponto de controle.

Exemplo de código:


```csharp
drawNode.DrawQuadBezier (
    origin:new CCPoint (0, 0),
    control:new CCPoint (200, 0),
    destination:new CCPoint (0, 300),
    segments:64,
    lineWidth:1,
    color:CCColor4B.White);
```

![](ccdrawnode-images/image12.png "DrawQuadBezier conecta os dois pontos com uma linha")


### <a name="drawrect"></a>DrawRect

`DrawRect` cria um retângulo preenchido com uma estrutura de tópicos de largura variável e cor.

Exemplo de código: 


```csharp
var shape = new CCRect (
    0, 0, 100, 200);
drawNode.DrawRect(shape,
    fillColor:CCColor4B.Blue,
    borderWidth: 4,
    borderColor:CCColor4B.White); 
```

![](ccdrawnode-images/image13.png "DrawRect cria um retângulo preenchido com uma estrutura de tópicos de largura variável e cor")


### <a name="drawsegment"></a>DrawSegment

`DrawSegment` conecta dois pontos com uma linha de largura variável e cor. Ele é semelhante a `DrawLine`, exceto que ele cria pontos de extremidade redondos, em vez de pontos de extremidade simples.

Exemplo de código:


```csharp
drawNode.DrawSegment (from: new CCPoint (0, 0),
    to: new CCPoint (100, 200),
    radius: 5,
    color:new CCColor4F(1,1,1,1)); 
```

![](ccdrawnode-images/image14.png "DrawSegment conecta os dois pontos com uma linha de cor e de largura variável")


### <a name="drawsolidarc"></a>DrawSolidArc

`DrawSolidArc` cria uma borda preenchido de uma determinada cor e radius.

Exemplo de código:


```csharp
drawNode.DrawSolidArc(
    pos:new CCPoint(100, 100),
    radius:200,
    startAngle:0,
    sweepAngle:CCMathHelper.Pi/2, // this is in radians, clockwise
    color:CCColor4B.White); 
```

![](ccdrawnode-images/image15.png "DrawSolidArc cria uma fatia preenchido de uma determinada cor e radius")


### <a name="drawsolidcircle"></a>DrawSolidCircle

`DrawCircle` cria um círculo preenchido de um determinado raio.

Exemplo de código:


```csharp
drawNode.DrawSolidCircle(
    pos: new CCPoint (100, 100),
    radius: 50,
    color: CCColor4B.Yellow); 
```

![](ccdrawnode-images/image16.png "DrawCircle cria um círculo preenchido de um determinado radius")


### <a name="drawtrianglelist"></a>DrawTriangleList

`DrawTriangleList` cria uma lista de triângulos. Cada triângulo é definido por três `CCV3F_C4B` instâncias em uma matriz. O número de vértices da matriz passado para o `verts` parâmetro deve ser um múltiplo de três. Observe que a única informação contida em `CCV3F_C4B` é a posição do verts e sua cor – o `DrawTriangleList` método não oferece suporte a desenho triângulos texturas.

Exemplo de código:


```csharp
CCV3F_C4B[] verts = new CCV3F_C4B[] {
    // First triangle:
    new CCV3F_C4B( new CCPoint(0,0), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(30,60), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(60,0), CCColor4B.White),
    // second triangle, each point has different colors:
    new CCV3F_C4B( new CCPoint(90,0), CCColor4B.Yellow),
    new CCV3F_C4B( new CCPoint(120,60), CCColor4B.Red),
    new CCV3F_C4B( new CCPoint(150,0), CCColor4B.Blue)
};

drawNode.DrawTriangleList (verts); 
```

![](ccdrawnode-images/image17.png "DrawTriangleList cria uma lista de triângulos")


## <a name="summary"></a>Resumo

Este guia explica como criar um `CCDrawNode` e executar o processamento baseado em primitivo. Ele fornece um exemplo de cada uma das chamadas de desenho.

## <a name="related-links"></a>Links relacionados

- [CCDrawNode API](https://developer.xamarin.com/api/type/CocosSharp.CCDrawNode/)
- [Exemplo completo](https://developer.xamarin.com/samples/mobile/CCDrawNode/)
