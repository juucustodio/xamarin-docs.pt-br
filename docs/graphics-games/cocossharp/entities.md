---
title: Entidades em CocosSharp
description: O padrão de entidade é uma maneira eficiente de organizar o código de jogo. Ele melhora a legibilidade, torna mais fácil de manter, o código e aproveita a funcionalidade interna de pai/filho.
ms.prod: xamarin
ms.assetid: 1D3261CE-AC96-4296-8A53-A76A42B927A8
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: 406c416e9bbecb4726793981ae347e0172ab0dbc
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="entities-in-cocossharp"></a>Entidades em CocosSharp

_O padrão de entidade é uma maneira eficiente de organizar o código de jogo. Ele melhora a legibilidade, torna mais fácil de manter, o código e aproveita a funcionalidade interna de pai/filho._

O padrão de entidade pode melhorar os esforços do desenvolvedor com CocosSharp por meio de organização aprimorada de código. Este passo a passo será um exemplo prático mostrando como criar duas entidades – uma entidade de remessa e uma entidade de marcador. Essas entidades serão objetos independentes, que significa que uma vez criada, eles serão renderizados automaticamente e executará a lógica de movimentação conforme apropriado para seu tipo. 

Este guia aborda os seguintes tópicos:

 - Introdução às entidades de jogos
 - Geral vs. tipos de entidade específico
 - Configuração de projeto
 - Criando classes de entidade
 - Adicionando instâncias de entidade para o `GameLayer`
 - Reagir a lógica de entidade no `GameLayer`

O jogo concluído será assim:

![](entities-images/image1.png "O jogo concluído se parecerá com")


## <a name="introduction-to-game-entities"></a>Introdução às entidades de jogos

Entidades de jogo são classes que definem objetos que precisam de lógica de processamento, colisão, física ou inteligência artificial. Felizmente, as entidades presentes na base de código do jogo geralmente correspondem os objetos conceituais em um jogo. Quando for verdadeiro, identificar as entidades necessárias em um jogo é mais fácil possível. 

Por exemplo, um espaço com tema [solucionar 'em o jogo](http://en.wikipedia.org/wiki/Shoot_%27em_up) pode incluir as seguintes entidades:

 - `PlayerShip`
 - `EnemyShip`
 - `PlayerBullet`
 - `EnemyBullet`
 - `CollectableItem`
 - `HUD`
 - `Environment`

Essas entidades seria suas próprias classes do jogo e cada instância exigem pouca ou nenhuma instalação além de instanciação.


## <a name="general-vs-specific-entity-types"></a>Geral vs. tipos de entidade específico

Uma das primeiras perguntas enfrentadas pelos desenvolvedores de jogos usando um sistema de entidade é quanto para generalizar suas entidades. Implementações mais específico define classes para cada tipo de entidade, mesmo se eles diferem por algumas características. Sistemas mais gerais combinar grupos de entidades em uma classe e permitir que instâncias de ser personalizado.

Por exemplo, pode imaginar um jogo de espaço que define as classes a seguir:

 - `PlayerDogfighter`
 - `PlayerBomber`
 - `EnemyMissileShip`
 - `EnemyLaserShip`

Uma abordagem mais generalizado seria criar uma única classe para entregas de player e uma única classe para inimigo fornecido, que pode ser configurado para dar suporte a tipos de remessa diferentes. Personalização pode incluir a imagem para carregar o tipo de entidades de marcador para criar ao fotografar coeficientes de movimentação e lógica de AI para o inimigo fornecido. Nesse caso, a lista de entidades pode ser reduzida para:

 - `PlayerShip`
 - `EnemyShip`

Obviamente, esses tipos de entidade podem ser ainda mais generalizados, permitindo que a personalização por instância para controlar a movimentação. Instâncias de envio do Player lê de entrada enquanto ship inimigo instâncias podem executar lógica de AI. Isso significa que as entidades podem ser generalizadas ainda mais em uma única classe:

 - `Ship`

A generalização pode continuar ainda – um jogo pode usar uma única classe base para todas as entidades. Esta classe único, que pode ser chamada `GameEntity`, seria a classe usada para cada instância de entidade em todo o jogo, inclusive entidades que não é fornecido como marcadores e itens de coleção (power-no-break).

Isso geral a maioria dos sistemas é conhecida como uma *sistema componente*. No sistema, jogos entidades podem ter componentes individuais, como física, AI, ou renderizar componentes adicionado para personalizar a aparência e comportamento. Puros sistemas baseado em componente habilitar máxima flexibilidade e podem evitar problemas causados pelo uso de herança, como cadeias de herança complexas. Assim como acontece com outros generalizações efetiva de componentes do sistema podem ser difícil de configurar e podem reduzir a expressividade de código.

O nível do generalização usado depende de várias considerações, incluindo:

 - Tamanho de jogo – jogos menores pode criar classes específicas, jogos maiores podem ser difíceis de gerenciar com um grande número de classes.
 - Dados de desenvolvimento – controlado por jogos que dependem de dados (imagens, modelos 3D e arquivos de dados como JSON ou XML) podem se beneficiar de ter generalizado tipos de entidade e configurar as especificações com base nos dados. Isso é especialmente importante para jogos que pretende adicionar o novo conteúdo durante o desenvolvimento ou após o lançamento do jogo.
 - Padrões de mecanismo de jogo – alguns mecanismos de jogos recomendamos o uso de componentes do sistema enquanto outros permitem que os desenvolvedores a decidir como organizar entidades. CocosSharp não requer o uso de um sistema de componente, para que os desenvolvedores estão livres para implementar qualquer tipo de entidade. 

Para simplificar, usaremos uma abordagem de baseado em classe específica com uma única entidade de remessa e marcadores para este tutorial.


## <a name="project-setup"></a>Configuração de projeto

Antes de começarmos a implementar nosso entidades, é preciso criar um projeto. Usaremos os modelos de projeto CocosSharp para simplificar a criação do projeto. [Verifique esta postagem](http://forums.xamarin.com/discussion/26822/cocossharp-project-templates-for-xamarin-studio) para obter informações sobre como criar um projeto de CocosSharp do Visual Studio para modelos de Mac. O restante deste guia usará o nome do projeto **EntityProject**.

Depois de criar nosso projeto vamos definir a resolução do nosso jogo para ser executado em 320 x 480. Para fazer isso, chame `CCScene.SetDefaultDesignResolution` no `GameAppDelegate.ApplicationDidFinishLaunching` método da seguinte maneira:


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    ...

    // New code for resolution setting:
    CCScene.SetDefaultDesignResolution(480, 320, CCSceneResolutionPolicy.ShowAll);
    
    CCScene scene = new CCScene (mainWindow);
    GameLayer gameLayer = new GameLayer ();

    scene.AddChild (gameLayer);
    mainWindow.RunWithScene (scene);
} 
```

Para obter mais informações sobre como lidar com CocosSharp resoluções, consulte nosso [guia tratamento várias resoluções no CocosSharp](~/graphics-games/cocossharp/resolutions.md).


## <a name="adding-content-to-the-project"></a>Adicionar conteúdo ao projeto

Quando nosso projeto tiver sido criado, vamos adicionar os arquivos contidos em [o arquivo zip conteúdo](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Entities.zip?raw=true). Para fazer isso, baixe o arquivo zip e descompacte-o. Adicione os **ship.png** e **bullet.png** para o **conteúdo** pasta. O **conteúdo** pasta será dentro do **ativos** pasta no Android e será a raiz do projeto em iOS. Depois de adicionado, veremos que ambos os arquivos no **conteúdo** pasta:

![](entities-images/image2.png "Depois de adicionado, ambos os arquivos devem estar na pasta de conteúdo")


## <a name="creating-the-ship-entity"></a>Criar a entidade de remessa

O `Ship` classe será a entidade de primeiro do nosso jogo. Para adicionar um `Ship` classe, primeiro crie uma pasta chamada **entidades** no nível raiz do projeto. Adicionar uma nova classe de **entidades** pasta chamada `Ship`:

![](entities-images/image3.png "Adicione uma nova classe na pasta entidades chamada de remessa")

A primeira alteração que faremos nosso `Ship` classe é permitir que ela herde o `CCNode` classe. `CCNode` serve como a classe base para classes comuns de CocosSharp como `CCSprite` e `CCLayer`e nos oferece a seguinte funcionalidade:

 - `Position` propriedade para mover a remessa pela tela.
 - `Children` propriedade para adicionar um `CCSprite.`
 - `Parent` propriedade, que pode ser usada para anexar `Ship` instâncias outros `CCNodes`. Não é possível usar esse recurso neste tutorial; jogos maiores geralmente aproveitam anexar entidades outros `CCNodes`. 
 - `AddEventListener` método para responder a entrada para mover a remessa.
 - `Schedule` método de solução de marcadores.

Nós adicionaremos um `CCSprite` para que navio pode ser visto na tela da instância:


```csharp
using System;
using CocosSharp;

namespace EntityProject
{
    public class Ship : CCNode
    {
        CCSprite sprite;

        public Ship () : base()
        {
            sprite = new CCSprite ("ship.png");
            // Center the Sprite in this entity to simplify
            // centering the Ship when it is instantiated
            sprite.AnchorPoint = CCPoint.AnchorMiddle;
            this.AddChild(sprite);
        }
    }
}
```

Em seguida, adicionaremos a remessa para nosso `GameLayer` para ser exibido em nosso jogo:


```csharp
public class GameLayer : CCLayer
{
    Ship ship;

    public GameLayer ()
    {
        ship = new Ship ();
        ship.PositionX = 240;
        ship.PositionY = 50;
        this.AddChild (ship);
    } 
    ...
```

Se executarmos nosso jogo que agora veremos nossa entidade de remessa:

![](entities-images/image4.png "Ao executar o jogo, será exibida a entidade de remessa")


### <a name="why-inherit-from-ccnode-instead-of-ccsprite"></a>Por que herdam CCNode em vez de CCSprite?

Neste ponto nosso `Ship` classe é um wrapper simple para um `CCSprite` instância. Como `CCSprite` também herda de `CCNode`, pode ter herdada diretamente de `CCSprite`, que seriam reduziu o código em `Ship.cs`. Além disso, herdando diretamente `CCSprite` reduz o número de objetos na memória e pode melhorar o desempenho, reduzir a árvore de dependência.

Apesar desses benefícios, que é herdado de `CCNode` para ocultar alguns do `CCSprite` propriedades de cada instância. Por exemplo, o `Texture` propriedade não deve ser modificada fora do `Ship` classe e herdando `CCNode` permite ocultar essa propriedade. Os membros públicos de nosso entidades tornam-se especialmente importantes conforme um jogo cresce e os desenvolvedores adicionais são adicionados a uma equipe.


## <a name="adding-input-to-the-ship"></a>Adicionando a entrada para a entrega

Agora que navio está visível na tela, estaremos adicionando entrada. Nossa abordagem será semelhante a abordagem a [BouncingGame guia](~/graphics-games/cocossharp/bouncing-game.md), exceto que estamos colocando o código de movimentação no `Ship` classe em vez de em que o contém `CCLayer` ou `CCScene`.

Adicione o código ao `Ship` para movê-lo para onde quer que o usuário é tocar na tela de suporte:


```csharp
public class Ship : CCNode
{
    CCSprite sprite;

    CCEventListenerTouchAllAtOnce touchListener;

    public Ship () : base()
    {
        sprite = new CCSprite ("ship.png");
        // Center the Sprite in this entity to simplify
        // centering the Ship on screen
        sprite.AnchorPoint = CCPoint.AnchorMiddle;
        this.AddChild(sprite);

        touchListener = new CCEventListenerTouchAllAtOnce();
        touchListener.OnTouchesMoved = HandleInput;
        AddEventListener(touchListener, this);

    }

    private void HandleInput(System.Collections.Generic.List<CCTouch> touches, CCEvent touchEvent)
    {
        if(touches.Count > 0)
        {
            CCTouch firstTouch = touches[0];

            this.PositionX = firstTouch.Location.X;
            this.PositionY = firstTouch.Location.Y;
        } 
    }
} 
```

Muitos solucionar 'em backup jogos implementar uma velocidade máxima, imitando movimentação tradicional baseadas em controlador. Dito isso, basta implementaremos movimentação imediata para manter o código mais curto.


## <a name="creating-the-bullet-entity"></a>Criar a entidade de marcador

A segunda entidade em nosso jogo simple é uma entidade para a exibição de marcadores. Assim como o `Ship` entidade, o `Bullet` entidade conterá um `CCSprite` para que ele apareça na tela. A lógica de movimentação é diferente em que ele não é dependente de entrada do usuário para a movimentação de; em vez disso, `Bullet` instâncias se movem em uma linha reta usando propriedades de velocidade.

Primeiro, vamos adicionar um novo arquivo de classe ao nosso **entidades** pasta e chamá-lo **marcador**:

![](entities-images/image5.png "Adicionar um novo arquivo de classe para a pasta de entidades e chamá-lo de marcador")

Depois de adicionado, modificaremos a `Bullet.cs` de código da seguinte maneira:


```csharp
using System;
using CocosSharp;

namespace EntityProject
{
    public class Bullet : CCNode
    {
        CCSprite sprite;

        public float VelocityX
        {
            get;
            set;
        }

        public float VelocityY
        {
            get;
            set;
        }

        public Bullet () : base()
        {
            sprite = new CCSprite ("bullet.png");
            // Making the Sprite be centered makes
            // positioning easier.
            sprite.AnchorPoint = CCPoint.AnchorMiddle;
            this.AddChild(sprite);

            this.Schedule (ApplyVelocity);
        }

        void ApplyVelocity(float time)
        {
            PositionX += VelocityX * time;
            PositionY += VelocityY * time;
        }
    }
} 
```

Além de alterar o arquivo usado para o `CCSprite` para `bullet.png`, o código em `ApplyVelocity` inclui a lógica de movimentação que se baseia em dois coeficientes: `VelocityX` e `VelocityY`.

O `Schedule` método permite adicionar delegados seja chamado a cada quadro. Nesse caso, estamos adicionando o `ApplyVelocity` método para que move nosso marcador de acordo com seus valores de velocidade. O `Schedule` leva um `Action<float>`, onde o parâmetro float Especifica a quantidade de tempo (em segundos) desde o último quadro, podemos usar para implementar a movimentação de tempo. Desde o tempo de valor é medido em segundos, em seguida, os valores de velocidade representam movimentação em *pixels por segundo*.


## <a name="adding-bullets-to-gamelayer"></a>Adicionando marcadores para GameLayer

Antes de adicionar qualquer `Bullet` instâncias ao nosso jogo faremos um contêiner, especificamente um `List<Bullet>`. Modificar o `GameLayer` para que ela inclua uma lista com marcadores:


```csharp
    public class GameLayer : CCLayer
    {
        Ship ship;
        List<Bullet> bullets;

        public GameLayer ()
        {
            ship = new Ship ();
            ship.PositionX = 240;
            ship.PositionY = 50;
            this.AddChild (ship);

            bullets = new List<Bullet> ();
        }
        ... 
```

Em seguida, será preciso preencher o `Bullet` lista. A lógica para quando criar um `Bullet` devem estar contidos no `Ship` entidade, mas o `GameLayer` é responsável por armazenar a lista com marcadores. Usaremos o padrão de fábrica para permitir que o `Ship` entidade criar `Bullet` instâncias. Esta fábrica expõe um evento que o `GameLayer` pode manipular. 

Para fazer isso primeiro, vamos adicionar uma pasta ao nosso projeto chamado **fábricas**, e, em seguida, adicione uma nova classe chamada `BulletFactory`:

![](entities-images/image6.png "Adicionar uma pasta para o projeto chamado fábricas e, em seguida, adicione uma nova classe chamada BulletFactory")

Em seguida, implementaremos o `BulletFactory` classe singleton:


```csharp
using System;

namespace EntityProject
{
    public class BulletFactory
    {
        static Lazy<BulletFactory> self = 
            new Lazy<BulletFactory>(()=>new BulletFactory());

        // simple singleton implementation
        public static BulletFactory Self
        {
            get
            {
                return self.Value;
            }
        }

        public event Action<Bullet> BulletCreated;

        private BulletFactory()
        {

        }

        public Bullet CreateNew()
        {
            Bullet newBullet = new Bullet ();

            if (BulletCreated != null)
            {
                BulletCreated (newBullet);
            }

            return newBullet;
        }
    }
} 
```

O `Ship` entidade tratará criando `Bullet` instâncias – especificamente, ele tratará frequência `Bullet` instâncias devem ser criadas (ou seja, quantas vezes o marcador é acionado), sua posição e sua velocidade.

Modificar o `Ship` construtor da entidade para adicionar uma nova `Schedule` chamar e, em seguida, implementar esse método da seguinte maneira:


```csharp
...
public Ship () : base()
{
    sprite = new CCSprite ("ship.png");
    // Center the Sprite in this entity to simplify
    // centering the Ship on screen
    sprite.AnchorPoint = CCPoint.AnchorMiddle;
    this.AddChild(sprite);

    touchListener = new CCEventListenerTouchAllAtOnce();
    touchListener.OnTouchesMoved = HandleInput;
    AddEventListener(touchListener, this);

    Schedule (FireBullet, interval: 0.5f);

}

void FireBullet(float unusedValue)
{
    Bullet newBullet = BulletFactory.Self.CreateNew ();
    newBullet.Position = this.Position;
    newBullet.VelocityY = 100;
} 
...
```

A última etapa é tratar a criação de novos `Bullet` instâncias de `GameLayer` código. Adicionar um manipulador de eventos para o `BulletCreated` eventos que adiciona recém-criado `Bullet` às listas de apropriado:


```csharp
...
public GameLayer ()
{
    ship = new Ship ();
    ship.PositionX = 240;
    ship.PositionY = 50;
    this.AddChild (ship);

    bullets = new List<Bullet> ();
    BulletFactory.Self.BulletCreated += HandleBulletCreated;
}

void HandleBulletCreated(Bullet newBullet)
{
    AddChild (newBullet);
    bullets.Add (newBullet);
}
... 
```

Agora podemos executar o jogo e consulte o `Ship` acertar `Bullet` instâncias:

![](entities-images/image1.png "Executar o jogo e o envio será ser acertar instâncias de marcador")


## <a name="why-gamelayer-has-ship-and-bullets-members"></a>Por que GameLayer tem membros de remessa e marcadores

Nosso `GameLayer` classe define dois campos para manter referências a instâncias da entidade (`ship` e `bullets`), mas não faz nada com eles. Além disso, a entidades são responsáveis por seu próprios comportamento como a movimentação e a solução. Então por que adicionamos `ship` e `bullets` campos `GameLayer`?

O motivo adicionamos esses membros é que exija lógica em uma implementação de jogo completo a `GameLayer` para a interação entre as diferentes entidades. Por exemplo, o jogo pode ser desenvolvido adicional para incluir inimigos que podem ser destruídos pelo player. Esses inimigos devem estar contidos em um `List` no `GameLayer`e lógica para testar se `Bullet` instâncias entrar em conflito com os inimigos seriam executados no `GameLayer` também. Em outras palavras, o `GameLayer` é a raiz *proprietário* da entidade de todas as instâncias e ele é responsável por interações entre instâncias de entidade.


## <a name="bullet-destruction-considerations"></a>Considerações de destruição de marcador

Nosso jogo atualmente não tem código para destruir `Bullet` instâncias. Cada `Bullet` instância tem lógica para mover na tela, mas ainda não adicionamos nenhum código para destruir qualquer fora da tela `Bullet` instâncias.

Além disso, a destruição do `Bullet` instâncias não podem estar na `GameLayer`. Por exemplo, em vez de destruição quando fora da tela, o `Bullet` entidade pode ter lógica para destruir o próprio após um determinado período de tempo. Nesse caso, o `Bullet` precisa de uma maneira de comunicar-se de que ele deve ser destruído para o `GameLayer`, parecido com o `Ship` entidade comunicadas a `GameLayer` que um novo `Bullet` foi criado por meio a `BulletFactory`.

A solução mais simples é expandir a responsabilidade da classe de fábrica para dar suporte a destruição. Em seguida, a fábrica pode ser notificada sobre uma instância de entidade que está sendo destruída, que pode ser tratada por outros objetos, como o `GameLayer` removendo a instância da entidade de sua lista. 

## <a name="summary"></a>Resumo

Este guia mostra como criar entidades CocosSharp, herdando a `CCNode` classe. Essas entidades são objetos independentes, tratamento de criação de seus próprios visuais e lógica personalizada. Este guia designa o código pertence dentro de uma entidade (movimentação e criação de outras entidades) do código que pertence o contêiner de entidade raiz (colisão e outra lógica de interação de entidade).

## <a name="related-links"></a>Links relacionados

- [Documentação da API do CocosSharp](https://developer.xamarin.com/api/namespace/CocosSharp/)
- [Conteúdo zip](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Entities.zip?raw=true)
