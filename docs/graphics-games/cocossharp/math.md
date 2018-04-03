---
title: Matemática 2D com CocosSharp
description: Este guia abrange 2D matemática para desenvolvimento de jogos. Ele usa CocosSharp para mostrar como executar tarefas comuns de desenvolvimento de jogos e explica a matemática por trás essas tarefas.
ms.topic: article
ms.prod: xamarin
ms.assetid: 5C241AB4-F97E-4B61-B93C-F5D307BCD517
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: 484bd8b19f2c51dac57a46a1ef93610ed5e13419
ms.sourcegitcommit: 4f1b508caa8e7b6ccf85d167ea700a5d28b0347e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2018
---
# <a name="2d-math-with-cocossharp"></a>Matemática 2D com CocosSharp

_Este guia abrange 2D matemática para desenvolvimento de jogos. Ele usa CocosSharp para mostrar como executar tarefas comuns de desenvolvimento de jogos e explica a matemática por trás essas tarefas._

Para posicionar e mover objetos com o código é uma parte principal do desenvolvimento de jogos de todos os tamanhos. Posicionando e movendo requerem o uso de cálculos, se um jogo requer a movimentação de um objeto ao longo de uma linha reta, ou o uso de trigonometria de rotação. Este documento aborda os seguintes tópicos:

 - Velocidade
 - Aceleração
 - Rotação de objetos CocosSharp
 - Usando a rotação com velocidade

Os desenvolvedores que não têm um plano de fundo de matemática forte ou que têm esquecidas esses tópicos da escola, não precisam se preocupar – este documento travará conceitos em partes de tamanho de bytes e acompanhará explicações teóricas com exemplos práticos. Em resumo, este artigo será responder à pergunta de aluno de matemática antigo: "Quando eu realmente precisará usar essas coisas?"


## <a name="requirements"></a>Requisitos

Embora este documento se concentra principalmente no lado matemático de CocosSharp, exemplos de código assuma trabalhar com objetos de herança de formulário `CCNode`. Além disso, desde `CCNode` não incluir valores para velocidade e aceleração, o código supõe que trabalhar com entidades que fornecem valores como VelocityX, VelocityY, AccelerationX e AccelerationY. Para obter mais informações sobre entidades, consulte nosso passo a passo sobre [entidades em CocosSharp](~/graphics-games/cocossharp/entities.md).


## <a name="velocity"></a>Velocidade

Os desenvolvedores de jogos usam o termo *velocidade* para descrever como um objeto é movido – especificamente rapidez algo é movido e a direção que ele está se movendo. 

Velocidade é definida usando dois tipos de unidades: uma unidade de posição e uma unidade de tempo. Por exemplo, a velocidade do carro é definida como milhas por hora ou quilômetros por hora. Desenvolvedores de jogos move geralmente use pixels por segundo para definir a rapidez com que um objeto. Por exemplo, um marcador pode mover em uma velocidade de 300 pixels por segundo. Ou seja, se um marcador está se movendo em 300 pixels por segundo, em seguida, ele será tiver movido 600 unidades em dois segundos e 900 unidades em três segundos e assim por diante. Geralmente, o valor de velocidade *adiciona* para a posição de um objeto (como veremos abaixo).

Embora usamos velocidade para explicar as unidades de velocidade, a velocidade de termo geralmente se refere a um valor independente da direção, enquanto a velocidade de termo se refere a velocidade e a direção. Portanto, a atribuição de velocidade do marcador (supondo que o marcador é uma classe que inclui as propriedades necessárias) pode se parecer com isso:


```csharp
// This bullet is not moving horizontally, so set VelocityX to 0:
bulletInstance.VelocityX = 0;
// Positive Y is "up" so move the bullet up 300 units per second:
bulletInstance.VelocityY = 300;
```


### <a name="implementing-velocity"></a>Implementação de velocidade

CocosSharp não implementa velocidade, portanto, os objetos necessitando de movimentação precisa implementar sua própria lógica de movimentação. Novos desenvolvedores jogos implementando velocidade geralmente cometer o erro de fazer sua velocidade dependente de taxa de quadros. Ou seja, o seguinte *implementação incorreta* parece fornecer resultados corretos, mas se baseará na taxa de quadros do jogo:

```csharp
// VelocityX and VelocityY will be added every time this code executes
this.PositionX += this.VelocityX;
this.PositionY += this.VelocityY;
```

Se o jogo é executada em uma maior taxa de quadros (como 60 quadros por segundo, em vez de 30 quadros por segundo), o objeto será exibida mover mais rapidamente do que se em execução em uma taxa de quadros mais lenta. Da mesma forma, se o jogo é incapaz de processar quadros em como alta de uma taxa de quadros (que pode ser causada por processos em segundo plano usando os recursos do dispositivo), o jogo aparecerá lentas.

Para levar isso em conta, velocidade geralmente é implementada usando um valor de tempo. Por exemplo, se o `seconds` variável representa o número (ou a fração) de segundos desde a última velocidade de tempo foi aplicada, em seguida, o código a seguir pode resultar no objeto com movimentação consistente, independentemente da taxa de quadros:

```csharp
// VelocityX and VelocityY will be added every time this code executes
this.PositionX += this.VelocityX * seconds;
this.PositionY += this.VelocityY * seconds;
```

Considere a possibilidade de que um jogo que é executado em uma taxa de quadros inferior atualizará a posição de seus objetos menos frequentemente. Portanto, cada atualização resultará em objetos movendo mais do que se o jogo estava atualizando com mais frequência. O `seconds` valor contas para isso, por quanto tempo se passou desde a última atualização de emissão de relatórios.

Para obter um exemplo de como adicionar a movimentação de tempo, consulte [essa receita que abrangem tempo com base em movimento](https://developer.xamarin.com/recipes/cross-platform/game_development/time_based_movement/).


### <a name="calculating-positions-using-velocity"></a>Calculando posições usando velocidade

Velocidade pode ser usada para fazer previsões sobre onde um objeto será depois que um valor de tempo passa, ou para ajudar a ajustar o comportamento de objetos sem a necessidade de executar o jogo. Por exemplo, um desenvolvedor que está implementando a movimentação de um marcador acionado precisa definir a velocidade do marcador após ele ser instanciado. O tamanho de tela pode ser usado para fornecer uma base para a configuração de velocidade. Ou seja, se o desenvolvedor sabe que o marcador deve mover a altura da tela em 2 segundos, a velocidade deve ser definida como a altura da tela dividida por 2. Se a tela de 800 pixels de altura, velocidade do marcador seria definida como 400 (que é 800/2).

Da mesma forma, lógica de jogos pode precisar calcular quanto tempo levará para alcançar um destino considerando sua velocidade um objeto. Isso pode ser calculado pela divisão a distância passe pela velocidade do objeto viagens. Por exemplo, o código a seguir mostra como atribuir texto para um rótulo que exibe o tempo até que um mísseis alcance seu destino:


```csharp
// We'll assume only the X axis for this example
float distanceX = target.PositionX - missile.PositionX;

float secondsToReachTarget = distanceX / missile.VelocityX;

label.Text = secondsToReachTarget + " seconds to reach target"; 
```


## <a name="acceleration"></a>Aceleração

*Aceleração* é um conceito comuns no desenvolvimento de jogos e compartilha muitas semelhanças com velocidade. Aceleração quantifica se um objeto é acelerar ou diminuir (como o valor de velocidade alterado ao longo do tempo). Aceleração *adiciona* a velocidade, como velocidade adiciona para posicionar. Aplicativos comuns de aceleração incluem gravidade, um carro acelerar e um envio de espaço acionamento seus thrusters. 

Semelhante a velocidade, aceleração é definida em uma posição e uma unidade de tempo; No entanto, unidade de tempo de aceleração é conhecida como um *quadrados* unidade, que reflete como aceleração é definida matematicamente. Ou seja, a aceleração de jogo geralmente é medida em *pixels por segundo quadrado*.

Se um objeto tiver uma aceleração X de 10 unidades por segundo quadrado, que significa que sua velocidade aumentam em 10 a cada segundo. Se a partir uma paralisação, depois que um segundo será móvel 10 unidades por segundo, depois de dois segundos 20 unidades por segundo, e assim por diante.

Aceleração em duas dimensões requer um componente de X e Y, portanto, pode ser atribuído da seguinte maneira:


```csharp
// No horizontal acceleration:
icicle.AccelerationX = 0;
// Simulate gravity with Y acceleration. Negative Y is down, so assign a negative value:
icicle.AccelerationY = -50; 
```


### <a name="acceleration-vs-deceleration"></a>Aceleração versus desaceleração

Embora a aceleração e desaceleração, às vezes, são diferenciados na fala de cada dia, não há nenhuma diferença técnica entre os dois. Gravidade é uma força que resulta em aceleração. Se um objeto é gerado para cima, em seguida, gravidade retardará ele (desacelerando), mas depois que o objeto foi interrompido alpinismo e é incluída na mesma direção gravidade, em seguida, gravidade é acelerá-lo (acelerando). Conforme mostrado abaixo, o aplicativo de uma aceleração é o mesmo se ele está sendo aplicado na mesma direção ou oposto direção de movimento. 


### <a name="implementing-acceleration"></a>Implementação de aceleração

Aceleração é semelhante à velocidade ao implementar – não é implementado automaticamente pelo CocosSharp e aceleração de tempo é a implementação desejada (em vez de aceleração de quadro). Portanto, uma implementação simples aceleração (junto com a velocidade) pode se parecer com:

```csharp
this.VelocityX += this.AccelerationX * seconds;
this.VelocityY += this.AccelerationY * seconds;
this.PositionX += this.VelocityX * seconds;
this.PositionY += this.VelocityY * seconds;
```

O código acima é o que é conhecido como um *aproximação linear* para implementação de aceleração. Efetivamente, ele implementa aceleração com bastante fechar grau de precisão, mas não é um modelo preciso perfeitamente de aceleração. Ele é incluído acima para ajudar a explicar o conceito de como a aceleração é implementada.

A implementação a seguir é um aplicativo matematicamente preciso de aceleração e velocidade:


```csharp
float halfSecondsSquared = (seconds * seconds) / 2.0f;

this.PositionX += 
    this.Velocity.X * seconds + this.AccelerationX * halfSecondsSquared;
this.PositionY += 
    this.Velocity.Y * seconds + this.AccelerationY * halfSecondsSquared;

this.VelocityX += this.AccelerationX * seconds;
this.VelocityY += this.AccelerationY * seconds; 
```

A diferença mais óbvia para o código acima é o `halfSecondsSquared` variável e seu uso para aplicar a aceleração de posição. O motivo matemático está além do escopo deste tutorial, mas os desenvolvedores interessados em matemática por trás disso podem encontrar mais informações em [esta discussão sobre a integração de aceleração.](http://www.cliffsnotes.com/math/calculus/calculus/integration/distance-velocity-and-acceleration)

O impacto prático de `halfSecondSquare` é que a aceleração comporte matematicamente com precisão e previsível, independentemente da taxa de quadros. A aproximação linear de aceleração está sujeito a taxa de quadros – quanto menor a taxa de quadros cai fica menos precisas da aproximação. Usando `halfSecondsSquared` garante que o código comporte o mesmo, independentemente da taxa de quadros.


## <a name="angles-and-rotation"></a>Rotação e ângulos

Visual, como objetos `CCSprite` oferecem suporte a rotação por meio de um `Rotation` variável. Isso pode ser atribuído a um valor para definir a rotação em graus. Por exemplo, o código a seguir mostra como girar uma `CCSprite` instância:


```csharp
CCSprite unrotatedSprite = new CCSprite("star.png");
unrotatedSprite.IsAntialiased = false;
unrotatedSprite.PositionX = 100;
unrotatedSprite.PositionY = 100;
this.AddChild (unrotatedSprite);

CCSprite rotatedSprite = new CCSprite("star.png");
rotatedSprite.IsAntialiased = false;
// This sprite is moved to the right so it doesn’t overlap the first
rotatedSprite.PositionX = 130;
rotatedSprite.PositionY = 100;
rotatedSprite.Rotation = 45;
this.AddChild (rotatedSprite); 
```

O resultado é o seguinte:

![](math-images/image1.png "Isso resulta nesta captura de tela")

Observe que a rotação é 45 graus no sentido horário (que, por razões históricas, é o oposto da como rotação é aplicada matematicamente):

![](math-images/image2.png "Observe que a rotação é 45 graus no sentido horário por razões históricas sendo o oposto do como rotação é aplicada matematicamente")

Em geral rotação para CocosSharp e matemática regular pode ser visualizada como segue:

![](math-images/image3.png "Em geral a rotação do CocosSharp e matemática regular pode ser visualizada assim")

![](math-images/image4.png "Em geral a rotação do CocosSharp e matemática regular pode ser visualizada assim")

Essa distinção é importante porque o `System.Math` classe usa a rotação no sentido anti-horário, portanto, os desenvolvedores familiarizados com essa classe precisam Inverter ângulos ao trabalhar com `CCNode` instâncias.

Devemos observar que os diagramas acima exibam rotação em graus; No entanto, algumas funções matemáticas (como as funções no `System.Math` namespace) esperar e retornar valores em *radianos* em vez de graus. Vamos examinar como converter entre os tipos de unidade de dois posteriormente neste guia.


### <a name="rotating-to-face-a-direction"></a>Girando para enfrentar uma direção

Como mostrado acima, `CCSprite` podem ser girados usando o `Rotation` propriedade. O `Rotation` propriedade é fornecida pelo `CCNode` (a classe base para `CCSprite`), que significa a rotação pode ser aplicada a entidades que herdam de `CCNode` também. 

Alguns jogos requerem objetos a ser girada para que eles enfrentam um destino. Os exemplos incluem um inimigo computador controlado acertar em um destino de player ou um envio de espaço pilotando até o ponto em que o usuário é tocar na tela. No entanto, um valor de rotação deve primeiro ser calculado com base no local da entidade que está sendo girada e o local de destino para enfrentar.

Esse processo requer várias etapas:

 - Identificando o *deslocamento* do destino. Deslocamento é a distância de X e Y entre a entidade a rotação e a entidade de destino.
 - Calculando o ângulo do deslocamento por meio da função arco tangente trigonométrica (explicada em detalhes abaixo).
 - Ajuste de uma diferença entre 0 graus apontando para a direita e a direção que a entidade de rotação aponta quando não girado.
 - Ajustando a diferença entre a rotação de matemática (no sentido anti-horário) e a rotação de CocosSharp (no sentido horário).

A função a seguir (supostamente gravados em uma entidade) gira a entidade para um destino:


```csharp
// This function assumes that it is contained in a CCNode-inheriting object
public void FacePoint(float targetX, float targetY)
{
    // Calculate the offset - the target's position relative to "this"
    float xOffset = targetX - this.PositionX;
    float yOffset = targetY - this.PositionY;

    // Make sure the target isn't the same point as "this". If so,
    // then rotation cannot be calculated.
    if (targetX != this.PositionX || targetY != this.Position.Y)
    {

        // Call Atan2 to get the radians representing the angle from 
        // "this" to the target
        float radiansToTarget = (float)System.Math.Atan2 (yOffset, xOffset);

        // Since CCNode uses degrees for its rotation, we need to convert
        // from radians
        float degreesToTarget = CCMathHelper.ToDegrees (radiansToTarget);

        // The direction that the entity faces when unrotated. In this case
        // the entity is facing "up", which is 90 degrees 
        const float forwardAngle = 90;

        // Adjust the angle we want to rotate by subtracting the
        // forward angle.
        float adjustedForDirecitonFacing = degreesToTarget - forwardAngle;

        // Invert the angle since CocosSharp uses clockwise rotation
        float cocosSharpAngle = adjustedForDirecitonFacing * -1;

        // Finally assign the rotation
        this.Rotation = rotation = cocosSharpAngle;
    }
} 
```

O código acima pode ser usado para girar uma entidade, de forma que ele fica disponível para o ponto em que o usuário é tocar na tela, da seguinte maneira:


```csharp
private void HandleInput(System.Collections.Generic.List<CCTouch> touches, CCEvent touchEvent)
{
    if(touches.Count > 0)
    {
        CCTouch firstTouch = touches[0];
        FacePoint (firstTouch.Location.X, firstTouch.Location.Y);
    }
} 
```

Esse código resulta no seguinte comportamento:

![](math-images/image5.gif "Esse código resulta nesse comportamento")

#### <a name="using-atan2-to-convert-offsets-to-angles"></a>Usar Atan2 para converter deslocamentos ângulos

`System.Math.Atan2` pode ser usado para converter um deslocamento em um ângulo. O nome da função `Atan2` vêm o arco tangente de função trigonométrica. O sufixo "2", essa função diferencia do padrão `Atan` função, que corresponde a estritamente o comportamento de matemático de arco tangente. Arco tangente é uma função que retorna um valor entre -90 e + 90 graus (ou equivalente em radianos). Muitos aplicativos, incluindo jogos, geralmente exigem um total de 360 graus de valores, portanto, o `Math` classe inclui `Atan2` para atender a essa necessidade.

Observe que o código acima passa o parâmetro Y primeiro, em seguida, o parâmetro X, ao chamar o `Atan2` método. Isso é com versões anteriores do X, Y ordenação de coordenadas de posição normal. Para obter mais informações [consulte a documentação de Atan2](https://msdn.microsoft.com/en-us/library/system.math.atan2(v=vs.110).aspx).

Também vale a pena observar que o valor de retorno de `Atan2` é em radianos, que é usada para medir ângulos de outra unidade. Este guia não abrange os detalhes de radianos, mas tenha em mente que todas as funções trigonométricas o `System.Math` namespace usar radianos, portanto, todos os valores devem ser convertidos em graus antes de serem usados em objetos CocosSharp. Para obter mais informações em radianos podem ser encontradas [na página do Wikipedia radianos](http://en.wikipedia.org/wiki/Radian).

#### <a name="forward-angle"></a>Ângulo de encaminhamento

Uma vez o `FacePoint` método converte o ângulo em radianos, ela define um `forwardAngle` valor. Esse valor representa o ângulo no qual a entidade está enfrentando quando seu valor de rotação é igual a 0. Neste exemplo, vamos supor que a entidade é voltado para cima, que é 90 graus ao usar uma rotação de matemática (em vez de rotação CocosSharp). Usamos a rotação de matemática aqui porque ainda estamos ainda não invertido a rotação de CocosSharp.

A seguir mostra que uma entidade com uma `forwardAngle` de 90 graus pode parecer com:

![](math-images/image6.png "Isso mostra o que pode ser a aparência de uma entidade com uma forwardAngle de 90 graus")


### <a name="angled-velocity"></a>Velocidade angular

Até agora, examinamos como converter um deslocamento em um ângulo. Esta seção vai de outra forma – pega um ângulo e a converte em X e Y de valores. Exemplos comuns incluem um carro movendo na direção na qual ele está enfrentando ou um envio de espaço como solucionar um marcador que move na direção em que o envio está enfrentando. 

Conceitualmente, a velocidade pode ser calculada pelo primeiro definir a velocidade desejada quando não girada, em seguida, girando essa velocidade para o ângulo de uma entidade está enfrentando. Para ajudar a explicar esse conceito, velocidade (e aceleração) podem ser visualizados como um 2-dimensional *vetor* (que normalmente é desenhada como uma seta). Um vetor para um valor de velocidade com X = 100 e Y = 0 pode ser visualizado da seguinte maneira:

![](math-images/image7.png "Um vetor para um valor de velocidade com X = 100 e Y = 0 pode ser visualizado da seguinte maneira")

Esse vetor pode ser girado para resultar em uma velocidade de novo. Por exemplo, girar o vetor de 45 graus (usando a rotação no sentido anti-horário) resulta no seguinte:

![](math-images/image8.png "Girando o vetor de 45 graus usando resultados de rotação no sentido anti-horário neste")

Felizmente, velocidade, a aceleração e a posição mesmo podem todos ser girados com o mesmo código, independentemente de como os valores são aplicados. A função para fins gerais a seguir pode ser usada para girar um vetor por um valor de rotação CocosSharp:


```csharp
// Rotates the argument vector by degrees specified by
// cocosSharpDegrees. In other words, the rotation
// value is expected to be clockwise.
// The vector parameter is modified, so it is both an in and out value
void RotateVector(ref CCVector2 vector, float cocosSharpDegrees)
{
    // Invert the rotation to get degrees as is normally
    // used in math (counterclockwise)
    float mathDegrees = -cocosSharpDegrees;

    // Convert the degrees to radians, as the System.Math
    // object expects arguments in radians
    float radians = CCMathHelper.ToRadians (mathDegrees);

    // Calculate the "up" and "right" vectors. This is essentially
    // a 2x2 matrix that we'll use to rotate the vector
    float xAxisXComponent = (float)System.Math.Cos (radians);
    float xAxisYComponent = (float)System.Math.Sin (radians);
    float yAxisXComponent = (float)System.Math.Cos (radians + CCMathHelper.Pi / 2.0f);
    float yAxisYComponent = (float)System.Math.Sin (radians + CCMathHelper.Pi / 2.0f);

    // Store the original vector values which will be used
    // below to perform the final operation of rotation.
    float originalX = vector.X;
    float originalY = vector.Y;

    // Use the axis values calculated above (the matrix values)
    // to rotate and assign the vector.
    vector.X = originalX * xAxisXComponent + originalY * yAxisXComponent;
    vector.Y = originalX * xAxisYComponent + originalY * yAxisYComponent;
} 
```

Uma compreensão completa do `RotateVector` método requer a familiarização com as funções trigonométricas e o coseno juntamente com alguns álgebra linear, que está além do escopo deste artigo. No entanto, uma vez implementado o `RotateVector` método pode ser usado para girar qualquer vetor, incluindo um vetor de velocidade. Por exemplo, o código a seguir pode disparar um marcador em uma direção especificada pelo `rotation` valor:


```csharp
// Create a Bullet instance
Bullet newBullet = new Bullet();

// Define the velocity of the bullet when 
// rotation is 0
CCVector2 velocity = new CCVector2 (0, 100);

// Modify the velocity according to rotation
RotateVector (ref velocity, rotation);

// Assign the newBullet's velocity using the
// rotated vector
newBullet.VelocityX = velocity.X;
newBullet.VelocityY = velocity.Y;

// Set the bullet's rotation so it faces
// the direction that it's flying
newBullet.Rotation = rotation; 
```

Esse código pode produzir algo como:

![](math-images/image9.png "Esse código pode produzir algo parecido com esta captura de tela")


## <a name="summary"></a>Resumo

Este guia aborda os conceitos de matemáticos comuns no desenvolvimento de jogos 2D. Ele mostra como atribuir e implementar a velocidade e a aceleração e aborda como girar objetos e vetores de movimentação em qualquer direção.
