---
title: Matemática 2D com CocosSharp
description: Este guia aborda matemática 2D para desenvolvimento de jogos. Ele usa CocosSharp para mostrar como executar tarefas comuns de desenvolvimento de jogos e explica a matemática por trás dessas tarefas.
ms.prod: xamarin
ms.assetid: 5C241AB4-F97E-4B61-B93C-F5D307BCD517
author: conceptdev
ms.author: crdun
ms.date: 03/27/2017
ms.openlocfilehash: ac84d5b28b0f211dccb1697a4b3dbbc9cedf81e9
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670140"
---
# <a name="2d-math-with-cocossharp"></a>Matemática 2D com CocosSharp

_Este guia aborda matemática 2D para desenvolvimento de jogos. Ele usa CocosSharp para mostrar como executar tarefas comuns de desenvolvimento de jogos e explica a matemática por trás dessas tarefas._

Para posicionar e mover objetos com o código é uma parte importante do desenvolvimento de jogos de todos os tamanhos. Posicionando e movendo exigem o uso de matemática, se um jogo requer a movimentação de um objeto ao longo de uma linha reta, ou o uso de trigonometria para rotação. Este documento abordará os seguintes tópicos:

 - Velocidade
 - Aceleração
 - Girando objetos CocosSharp
 - Usando a rotação com velocidade

Os desenvolvedores que não têm um plano de fundo de matemática forte, ou quem tiver esquecidas esses tópicos do estudante, não precisa se preocupar – este documento travará conceitos em partes com bites de tamanho e acompanhará explicações teóricas com exemplos práticos. Em resumo, este artigo irá responder à pergunta de aluno de matemática antigos: "Quando eu na verdade, precisará usar tudo isso?"


## <a name="requirements"></a>Requisitos

Embora este documento se concentra principalmente no lado matemático do CocosSharp, exemplos de código supor que o trabalho com objetos de herança de formulário `CCNode`. Além disso, desde `CCNode` não inclui valores de velocidade e aceleração, o código supõe que trabalho com entidades que fornecem valores como VelocityX, VelocityY, AccelerationX e AccelerationY. Para obter mais informações sobre entidades, consulte nosso passo a passo sobre [entidades em CocosSharp](~/graphics-games/cocossharp/entities.md).


## <a name="velocity"></a>Velocidade

Os desenvolvedores de jogos usam o termo *velocidade* para descrever como um objeto está se movendo – especificamente rapidez algo está se movendo e a direção que ele está se movendo. 

Velocidade é definida usando dois tipos de unidades: uma unidade de posição e uma unidade de tempo. Por exemplo, a velocidade do carro é definida como milhas por hora ou quilômetros por hora. Desenvolvedores de jogos geralmente uso pixels por segundo para definir quão rápido é um objeto se move. Por exemplo, um marcador pode mover a uma velocidade de 300 pixels por segundo. Ou seja, se um marcador está se movendo em 300 pixels por segundo, em seguida, ele será tiver movido 600 unidades em dois segundos e unidades de 900 em três segundos e assim por diante. De modo geral, o valor de velocidade *adiciona* para a posição de um objeto (conforme veremos abaixo).

Embora usamos velocidade para explicar as unidades de velocidade, a velocidade de termo geralmente se refere ao valor independente da direção, enquanto a velocidade de termo refere-se a velocidade e direção. Portanto, a atribuição de velocidade do marcador (supondo que o marcador é uma classe que inclui as propriedades necessárias) pode ser assim:


```csharp
// This bullet is not moving horizontally, so set VelocityX to 0:
bulletInstance.VelocityX = 0;
// Positive Y is "up" so move the bullet up 300 units per second:
bulletInstance.VelocityY = 300;
```


### <a name="implementing-velocity"></a>Implementando a velocidade

CocosSharp não implementa a velocidade, portanto, serão necessário implementar sua própria lógica de movimentação de objetos que exigem a movimentação. Novos desenvolvedores de jogos, velocidade de implementação geralmente cometem o de tornar sua velocidade depende da taxa de quadros. Ou seja, o seguinte *implementação incorreta* parecerá fornecer resultados corretos, mas se baseará na taxa de quadros do jogo:

```csharp
// VelocityX and VelocityY will be added every time this code executes
this.PositionX += this.VelocityX;
this.PositionY += this.VelocityY;
```

Se o jogo é executada em uma maior taxa de quadros (por exemplo, 60 quadros por segundo, em vez de 30 quadros por segundo), o objeto aparecerá mover mais rapidamente do que se em execução em uma taxa de quadros mais lenta. Da mesma forma, se o jogo é capaz de processar quadros no alto de uma taxa de quadros (que pode ser causada por processos em segundo plano usando os recursos do dispositivo), o jogo aparecerá reduzir a velocidade.

Para justificar isso, velocidade geralmente é implementada usando um valor de tempo. Por exemplo, se o `seconds` variável representa o número (ou fração) de segundos desde que foi aplicada a última velocidade de tempo, em seguida, o código a seguir resultaria no objeto de ter de movimentação consistente independentemente da taxa de quadros:

```csharp
// VelocityX and VelocityY will be added every time this code executes
this.PositionX += this.VelocityX * seconds;
this.PositionY += this.VelocityY * seconds;
```

Considere a possibilidade de que um jogo que é executado em uma taxa de quadros inferior atualizará a posição de seus objetos menos frequentemente. Portanto, cada atualização resultará nos objetos movendo ainda mais do que se o jogo estava atualizando com mais frequência. O `seconds` valor contas para isso, por quanto tempo se passou desde a última atualização de emissão de relatórios.

Para obter um exemplo de como adicionar a movimentação com base em hora, consulte [essa receita que abrangem o tempo com base em movimento](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/game_development/time_based_movement).


### <a name="calculating-positions-using-velocity"></a>Calculando posições usando velocidade

Velocidade pode ser usada para fazer previsões sobre onde um objeto será depois que um valor de tempo passa, ou para ajudar a ajustar o comportamento dos objetos sem a necessidade de executar o jogo. Por exemplo, um desenvolvedor que está implementando a movimentação de um marcador disparado precisa definir a velocidade do marcador após ele ser instanciado. O tamanho da tela pode ser usado para fornecer uma base para a configuração de velocidade. Ou seja, se o desenvolvedor sabe que o marcador deve mover a altura da tela em 2 segundos, a velocidade deve ser definida como a altura da tela dividida por 2. Se a tela de 800 pixels de altura, em seguida, velocidade do marcador seria definida como 400 (que é 800/2).

Da mesma forma, lógica do jogo talvez seja necessário calcular quanto tempo levará para alcançar um destino considerando sua velocidade um objeto. Isso pode ser calculado pela divisão a distância de viagem pela velocidade do objeto viagens. Por exemplo, o código a seguir mostra como atribuir texto a um rótulo que exibe o tempo até que um míssil alcança o destino:


```csharp
// We'll assume only the X axis for this example
float distanceX = target.PositionX - missile.PositionX;

float secondsToReachTarget = distanceX / missile.VelocityX;

label.Text = secondsToReachTarget + " seconds to reach target"; 
```


## <a name="acceleration"></a>Aceleração

*Aceleração* é um conceito comum no desenvolvimento de jogos, e ele compartilha muitas semelhanças com velocidade. Aceleração quantifica se um objeto é acelerar ou diminuir (como o valor de velocidade muda ao longo do tempo). Aceleração *adiciona* a velocidade, assim como a velocidade adiciona à posição. Aplicativos comuns de aceleração incluem um navio espaço acionar seus thrusters, um carro, acelerando e gravidade. 

Semelhante a velocidade, aceleração é definida em uma posição e a unidade de tempo; No entanto, unidade de tempo da aceleração é conhecida como um *quadrados* unidade, que reflete como a aceleração é definida matematicamente. Ou seja, a aceleração de jogo geralmente é medida em *pixels por segundo ao quadrado*.

Se um objeto tem uma aceleração X de 10 unidades por segundo ao quadrado, que significa que sua velocidade terão um aumento de 10 a cada segundo. Se a partir de uma paralisação, depois que um segundo será migrando em 10 unidades por segundo, depois de dois segundos 20 unidades por segundo, e assim por diante.

Aceleração em duas dimensões requer um componente de X e Y, portanto, ele poderá ser atribuído da seguinte maneira:


```csharp
// No horizontal acceleration:
icicle.AccelerationX = 0;
// Simulate gravity with Y acceleration. Negative Y is down, so assign a negative value:
icicle.AccelerationY = -50; 
```


### <a name="acceleration-vs-deceleration"></a>Aceleração e desaceleração

Embora, às vezes, são diferenciados e aceleração e desaceleração em fala corriqueira, não há nenhuma diferença técnica entre os dois. A gravidade é uma força que resulta em aceleração. Se um objeto for lançado para cima, em seguida, a gravidade retardará-lo (desacelerando), mas depois que o objeto foi interrompido sobe e está se atrasando na mesma direção como gravidade, em seguida, a gravidade é acelerando a ele (acelerando). Conforme mostrado abaixo, a aplicação de uma aceleração é o mesmo se ele está sendo aplicado na mesma direção ou o oposto direção de movimento. 


### <a name="implementing-acceleration"></a>Implementação de aceleração

Aceleração é semelhante a velocidade ao implementar – não é implementada automaticamente pelo CocosSharp e aceleração baseada em tempo é a implementação desejada (em vez de aceleração de quadro). Portanto, uma implementação simples de aceleração (juntamente com a velocidade) pode parecer com:

```csharp
this.VelocityX += this.AccelerationX * seconds;
this.VelocityY += this.AccelerationY * seconds;
this.PositionX += this.VelocityX * seconds;
this.PositionY += this.VelocityY * seconds;
```

O código acima é o que é conhecido como um *aproximação linear* para implementação de aceleração. Efetivamente, ele implementa aceleração com um grau muito parecido de precisão, mas não é um modelo perfeitamente preciso de aceleração. Ele é incluído acima para ajudar a explicar o conceito de como a aceleração é implementada.

A implementação a seguir é um aplicativo matematicamente preciso de aceleração e velocidade:


```csharp
float halfSecondsSquared = (seconds * seconds) / 2.0f;

this.PositionX += 
    this.Velocity.X * seconds + this.AccelerationX * halfSecondsSquared;
this.PositionY += 
    this.Velocity.Y * seconds + this.AccelerationY * halfSecondsSquared;

this.VelocityX += this.AccelerationX * seconds;
this.VelocityY += this.AccelerationY * seconds; 
```

A diferença mais óbvia para o código acima é o `halfSecondsSquared` variável e seu uso para aplicar a aceleração de posição. A matemática razão para isso está além do escopo deste tutorial, mas os desenvolvedores interessados na matemática por trás disso podem encontrar mais informações no [esta discussão sobre como integrar a aceleração.](http://www.cliffsnotes.com/math/calculus/calculus/integration/distance-velocity-and-acceleration)

O impacto prático da `halfSecondSquare` é que a aceleração comporte matematicamente com precisão e previsível, independentemente da taxa de quadros. A aproximação linear de aceleração é sujeito a taxa de quadros – quanto menor a taxa de quadros cai fica menos precisos de aproximação. Usando `halfSecondsSquared` garantias que código irá se comportar da mesma, independentemente da taxa de quadros.


## <a name="angles-and-rotation"></a>Ângulos e rotação

Objetos visuais, como `CCSprite` oferecem suporte a rotação por meio de um `Rotation` variável. Isso pode ser atribuído a um valor para definir a rotação em graus. Por exemplo, o código a seguir mostra como girar um `CCSprite` instância:


```csharp
CCSprite unrotatedSprite = new CCSprite("star.png");
unrotatedSprite.IsAntialiased = false;
unrotatedSprite.PositionX = 100;
unrotatedSprite.PositionY = 100;
this.AddChild (unrotatedSprite);

CCSprite rotatedSprite = new CCSprite("star.png");
rotatedSprite.IsAntialiased = false;
// This sprite is moved to the right so it doesn’t overlap the first
rotatedSprite.PositionX = 130;
rotatedSprite.PositionY = 100;
rotatedSprite.Rotation = 45;
this.AddChild (rotatedSprite); 
```

O resultado é o seguinte:

![](math-images/image1.png "Isso resulta nesta captura de tela")

Observe que a rotação é 45 graus no sentido horário (que, por razões históricas, é o oposto de como a rotação é aplicada matematicamente):

![](math-images/image2.png "Observe que a rotação é 45 graus no sentido horário que, por razões históricas é o oposto de como a rotação é aplicada matematicamente")

Em geral pode ser visualizada rotação para CocosSharp e matemática regular da seguinte maneira:

![](math-images/image3.png "Em geral a rotação para CocosSharp e matemática regular pode ser visualizada como este")

![](math-images/image4.png "Em geral a rotação para CocosSharp e matemática regular pode ser visualizada como este")

Essa distinção é importante porque o `System.Math` classe usa a rotação no sentido anti-horário, portanto, os desenvolvedores familiarizados com essa classe precisam Inverter ângulos ao trabalhar com `CCNode` instâncias.

Devemos observar que os diagramas acima exibam rotação em graus; No entanto, algumas funções matemáticas (como as funções na `System.Math` namespace) esperar e retornar valores no *radianos* em vez de graus. Vamos examinar como converter entre os tipos de dois unidade um pouco mais adiante neste guia.


### <a name="rotating-to-face-a-direction"></a>Girar para uma direção

Como mostrado acima, `CCSprite` podem ser girados usando o `Rotation` propriedade. O `Rotation` propriedade é fornecida pelo `CCNode` (a classe base para `CCSprite`), que significa que a rotação pode ser aplicada a entidades que herdam de `CCNode` também. 

Alguns jogos requerem objetos a ser girado para que eles enfrentam um destino. Os exemplos incluem um inimigo controlados por computador atirando em um destino de player ou um navio espaço voando até o ponto em que o usuário está tocando a tela. No entanto, um valor de rotação deve primeiro ser calculado com base em como o local da entidade que está sendo girada e o local de destino para enfrentar.

Esse processo requer um número de etapas:

 - Identificando os *deslocamento* do destino. Deslocamento refere-se à distância entre a entidade de rotação e a entidade de destino X e Y.
 - Calcular o ângulo do deslocamento por meio da função de trigonometria arco tangente (explicada em detalhes abaixo).
 - Ajustando-se para uma diferença entre 0 graus apontando para a direita e a direção que a entidade girar aponta quando não invertido.
 - Ajustando para a diferença entre a rotação de matemática (no sentido anti-horário) e a rotação de CocosSharp (no sentido horário).

A função a seguir (supostamente gravados em uma entidade) gira a entidade para um destino:


```csharp
// This function assumes that it is contained in a CCNode-inheriting object
public void FacePoint(float targetX, float targetY)
{
    // Calculate the offset - the target's position relative to "this"
    float xOffset = targetX - this.PositionX;
    float yOffset = targetY - this.PositionY;

    // Make sure the target isn't the same point as "this". If so,
    // then rotation cannot be calculated.
    if (targetX != this.PositionX || targetY != this.Position.Y)
    {

        // Call Atan2 to get the radians representing the angle from 
        // "this" to the target
        float radiansToTarget = (float)System.Math.Atan2 (yOffset, xOffset);

        // Since CCNode uses degrees for its rotation, we need to convert
        // from radians
        float degreesToTarget = CCMathHelper.ToDegrees (radiansToTarget);

        // The direction that the entity faces when unrotated. In this case
        // the entity is facing "up", which is 90 degrees 
        const float forwardAngle = 90;

        // Adjust the angle we want to rotate by subtracting the
        // forward angle.
        float adjustedForDirecitonFacing = degreesToTarget - forwardAngle;

        // Invert the angle since CocosSharp uses clockwise rotation
        float cocosSharpAngle = adjustedForDirecitonFacing * -1;

        // Finally assign the rotation
        this.Rotation = rotation = cocosSharpAngle;
    }
} 
```

O código acima poderia ser usado para girar uma entidade para que ela fique voltada para o ponto em que o usuário está tocando a tela, da seguinte maneira:


```csharp
private void HandleInput(System.Collections.Generic.List<CCTouch> touches, CCEvent touchEvent)
{
    if(touches.Count > 0)
    {
        CCTouch firstTouch = touches[0];
        FacePoint (firstTouch.Location.X, firstTouch.Location.Y);
    }
} 
```

Esse código resulta no seguinte comportamento:

![](math-images/image5.gif "Esse código resulta nesse comportamento")

#### <a name="using-atan2-to-convert-offsets-to-angles"></a>Usando Atan2 para converter deslocamentos de ângulos

`System.Math.Atan2` pode ser usado para converter um deslocamento em um ângulo. O nome da função `Atan2` o arco tangente de função trigonométrica é proveniente. O sufixo "2", essa função diferencia do padrão `Atan` função, que corresponde a estritamente o comportamento de matemático do arco tangente. Arco tangente é uma função que retorna um valor entre -90 e + 90 graus (ou o equivalente em radianos). Muitos aplicativos, incluindo jogos de computador, geralmente exigem um 360 graus completa de valores, portanto, o `Math` classe inclui `Atan2` para atender a essa necessidade.

Observe que o código acima passa o parâmetro Y em primeiro lugar, em seguida, o parâmetro de X, ao chamar o `Atan2` método. Isso é com versões anteriores do X, Y ordenação das coordenadas de posição normal. Para obter mais informações [consulte os documentos Atan2](https://msdn.microsoft.com/library/system.math.atan2(v=vs.110).aspx).

Também vale a pena observar que o valor retornado de `Atan2` é em radianos, que é usada para medir os ângulos de outra unidade. Este guia não abordará os detalhes de radianos, mas lembre-se de que todas as funções trigonométricas o `System.Math` namespace utilizarem radianos, portanto, quaisquer valores devem ser convertidos em graus, antes de serem usados em objetos de CocosSharp. Encontre mais informações sobre radianos [na página do Wikipedia em radianos](https://en.wikipedia.org/wiki/Radian).

#### <a name="forward-angle"></a>Ângulo de encaminhamento

Uma vez a `FacePoint` método converte o ângulo em radianos, ele define uma `forwardAngle` valor. Esse valor representa o ângulo no qual a entidade está voltada ao seu valor de rotação é igual a 0. Neste exemplo, vamos supor que a entidade está voltada para cima, que é 90 graus ao usar uma rotação de matemática (em vez de rotação de CocosSharp). Usamos a rotação de matemática aqui, pois estamos ainda não tiver invertido a rotação para CocosSharp.

A seguir mostra que uma entidade com um `forwardAngle` de 90 graus, pode parecer com:

![](math-images/image6.png "Isso mostra o que pode ser a aparência de uma entidade com um forwardAngle de 90 graus")


### <a name="angled-velocity"></a>Velocidade angular

Até agora examinamos como converter um deslocamento em um ângulo. Esta seção vai de outra forma – pega um ângulo e o converte em X e Y de valores. Exemplos comuns incluem um carro mover a direção na qual ele é voltado para a ou um navio espaço acertar um marcador que move na direção em que a nave está enfrentando. 

Conceitualmente, a velocidade pode ser calculada pela primeiro definindo a velocidade desejada ao cancelamento girado, em seguida, girando essa velocidade para o ângulo que é voltado para a uma entidade. Para ajudar a explicar esse conceito, velocity (e aceleração) podem ser visualizados como um 2-dimensional *vetor* (que normalmente é desenhado como uma seta). Um vetor para um valor de velocidade com X = 100 e Y = 0 pode ser visualizado da seguinte maneira:

![](math-images/image7.png "Um vetor para um valor de velocidade com X = 100 e Y = 0 pode ser visualizado da seguinte maneira")

Esse vetor pode ser girado para resultar em uma velocidade de novo. Por exemplo, girar o vetor em 45 graus (usando a rotação no sentido anti-horário) resulta no seguinte:

![](math-images/image8.png "Girando o vetor em 45 graus usando resultados de rotação no sentido anti-horário neste")

Felizmente, velocidade, a aceleração e a posição até mesma podem todos ser girados com o mesmo código, independentemente de como os valores são aplicados. A seguinte função de uso geral pode ser usada para girar um vetor por um valor de rotação de CocosSharp:


```csharp
// Rotates the argument vector by degrees specified by
// cocosSharpDegrees. In other words, the rotation
// value is expected to be clockwise.
// The vector parameter is modified, so it is both an in and out value
void RotateVector(ref CCVector2 vector, float cocosSharpDegrees)
{
    // Invert the rotation to get degrees as is normally
    // used in math (counterclockwise)
    float mathDegrees = -cocosSharpDegrees;

    // Convert the degrees to radians, as the System.Math
    // object expects arguments in radians
    float radians = CCMathHelper.ToRadians (mathDegrees);

    // Calculate the "up" and "right" vectors. This is essentially
    // a 2x2 matrix that we'll use to rotate the vector
    float xAxisXComponent = (float)System.Math.Cos (radians);
    float xAxisYComponent = (float)System.Math.Sin (radians);
    float yAxisXComponent = (float)System.Math.Cos (radians + CCMathHelper.Pi / 2.0f);
    float yAxisYComponent = (float)System.Math.Sin (radians + CCMathHelper.Pi / 2.0f);

    // Store the original vector values which will be used
    // below to perform the final operation of rotation.
    float originalX = vector.X;
    float originalY = vector.Y;

    // Use the axis values calculated above (the matrix values)
    // to rotate and assign the vector.
    vector.X = originalX * xAxisXComponent + originalY * yAxisXComponent;
    vector.Y = originalX * xAxisYComponent + originalY * yAxisYComponent;
} 
```

Uma compreensão completa da `RotateVector` método requer estar familiarizado com as funções trigonométricas e o coseno juntamente com alguns álgebra linear, que está além do escopo deste artigo. No entanto, uma vez implementado o `RotateVector` método pode ser usado para girar qualquer vetor, incluindo um vetor de velocidade. Por exemplo, o código a seguir pode ser acionado um marcador em uma direção especificada pelo `rotation` valor:


```csharp
// Create a Bullet instance
Bullet newBullet = new Bullet();

// Define the velocity of the bullet when 
// rotation is 0
CCVector2 velocity = new CCVector2 (0, 100);

// Modify the velocity according to rotation
RotateVector (ref velocity, rotation);

// Assign the newBullet's velocity using the
// rotated vector
newBullet.VelocityX = velocity.X;
newBullet.VelocityY = velocity.Y;

// Set the bullet's rotation so it faces
// the direction that it's flying
newBullet.Rotation = rotation; 
```

Esse código pode produzir algo como:

![](math-images/image9.png "Esse código pode produzir algo parecido com esta captura de tela")


## <a name="summary"></a>Resumo

Este guia aborda conceitos matemáticos comuns no desenvolvimento de jogos 2D. Ele mostra como atribuir e implementar a velocidade e aceleração e aborda como girar objetos e vetores de movimentação em qualquer direção.
