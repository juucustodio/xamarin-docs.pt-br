---
title: Entidades em CocosSharp
description: O padrão de entidade é uma maneira eficiente para organizar o código de jogo. Ele melhora a legibilidade, o que torna o código mais fácil de manter e aproveita a funcionalidade interna de pai/filho.
ms.prod: xamarin
ms.assetid: 1D3261CE-AC96-4296-8A53-A76A42B927A8
author: conceptdev
ms.author: crdun
ms.date: 03/27/2017
ms.openlocfilehash: 6445d595c9d8ca47e187fdcd158cd5a801a96407
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103199"
---
# <a name="entities-in-cocossharp"></a>Entidades em CocosSharp

_O padrão de entidade é uma maneira eficiente para organizar o código de jogo. Ele melhora a legibilidade, o que torna o código mais fácil de manter e aproveita a funcionalidade interna de pai/filho._

O padrão de entidade pode melhorar os esforços de um desenvolvedor com CocosSharp toda a organização de código aprimorada. Este passo a passo será um exemplo prático que mostra como criar duas entidades – uma entidade de remessa e uma entidade de marcador. Essas entidades serão objetos independentes, o que significa que uma vez instanciado eles automaticamente serão renderizados e executará a lógica de movimentação de conforme apropriado para seu tipo. 

Este guia aborda os seguintes tópicos:

 - Introdução às entidades de jogos
 - Geral vs. tipos de entidade específico
 - Configuração do projeto
 - Criando classes de entidade
 - Adição de instâncias de entidade para o `GameLayer`
 - Reagindo a lógica de entidade no `GameLayer`

O jogo concluído terá esta aparência:

![](entities-images/image1.png "O jogo concluído terá esta aparência")


## <a name="introduction-to-game-entities"></a>Introdução às entidades de jogos

Entidades de jogo são classes que definem objetos que precisam de lógica de renderização, colisão, física ou inteligência artificial. Felizmente, as entidades presentes na base de código de um jogo geralmente correspondem os objetos conceituais em um jogo. Quando isso for verdadeiro, identificar as entidades necessárias em um jogo pode ser obtido mais facilmente. 

Por exemplo, um espaço com tema [acertar isso mesmo jogo](http://en.wikipedia.org/wiki/Shoot_%27em_up) pode incluir as seguintes entidades:

 - `PlayerShip`
 - `EnemyShip`
 - `PlayerBullet`
 - `EnemyBullet`
 - `CollectableItem`
 - `HUD`
 - `Environment`

Essas entidades seria suas próprias classes no jogo, e cada instância requer pouca ou nenhuma configuração além da instanciação.


## <a name="general-vs-specific-entity-types"></a>Geral vs. tipos de entidade específico

Uma das primeiras perguntas enfrentadas pelos desenvolvedores de jogos usando um sistema de entidade é quanto para generalizar suas entidades. As implementações mais específicas definiria classes para cada tipo de entidade, mesmo se eles diferem por algumas características. Sistemas mais gerais serão combinar grupos de entidades em uma classe e instâncias para serem personalizados.

Por exemplo, podemos imaginar um jogo de espaço que define as classes a seguir:

 - `PlayerDogfighter`
 - `PlayerBomber`
 - `EnemyMissileShip`
 - `EnemyLaserShip`

Uma abordagem mais generalizado seria criar uma única classe para vem do player e uma única classe para navios inimigos, que poderia ser configurado para dar suporte a tipos de remessa diferentes. Personalização pode incluir a imagem para carregar, qual tipo de entidades de marcador para criar quando fotografar, os coeficientes de movimentação e lógica de IA para o inimigo é fornecido. Nesse caso, a lista de entidades pode ser reduzida para:

 - `PlayerShip`
 - `EnemyShip`

É claro, esses tipos de entidade podem ser ainda mais generalizados, permitindo que a personalização por instância para controlar o movimento. Instâncias de remessa do Player seriam lido da entrada, enquanto instâncias ship inimigo podem executar a lógica de inteligência Artificial. Isso significa que as entidades podem ser generalizadas ainda mais em uma única classe:

 - `Ship`

A generalização pode continuar ainda – um jogo pode usar uma única classe base para todas as entidades. Essa classe única, o que pode ser chamado `GameEntity`, seria a classe usada para cada instância de entidade em todo o jogo, incluindo entidades que não é fornecido como marcadores e itens de coleção (geradores de energia).

Isso geral a maioria dos sistemas é conhecido como um *sistema de componente*. No sistema, entidades de jogos podem ter componentes individuais como física, AI, ou renderizar componentes adicionados para personalizar a aparência e comportamento. Sistemas puros baseadas em componentes permitem flexibilidade ultimate e podem evitar problemas causados pelo uso de herança, como cadeias de herança complexa. Assim como acontece com outras generalizações efetiva de componentes do sistema podem ser difícil de configurar e podem reduzir a expressividade do código.

O nível de generalização usado depende de muitas considerações, incluindo:

 - Tamanho de jogos – jogos menores podem se dar criar classes específicas, enquanto os jogos de maiores podem ser difícil de gerenciar com um grande número de classes.
 - Dados de desenvolvimento – orientado por jogos que dependem de dados (imagens, modelos 3D e arquivos de dados como JSON ou XML) podem se beneficiar necessidade generalizada de tipos de entidade e configurando as especificidades com base nos dados. Isso é especialmente importação para jogos que espere para adicionar o novo conteúdo durante o desenvolvimento ou depois que o jogo foi lançado.
 - Padrões de mecanismo de jogos – alguns mecanismos de jogos recomendamos o uso de componentes do sistema enquanto outras permitem que os desenvolvedores a decidir como organizar entidades. CocosSharp não exige o uso de um sistema de componente, portanto, os desenvolvedores são livres para implementar qualquer tipo de entidade. 

Para simplificar, usaremos uma abordagem específica baseada em classe com uma única entidade de remessa e de marcador para este tutorial.


## <a name="project-setup"></a>Configuração do projeto

Antes de começarmos a implementar nosso entidades, precisamos criar um projeto. Usaremos os modelos de projeto de CocosSharp para simplificar a criação do projeto. [Verifique esta postagem](http://forums.xamarin.com/discussion/26822/cocossharp-project-templates-for-xamarin-studio) para obter informações sobre como criar um projeto de CocosSharp do Visual Studio para modelos de Mac. O restante deste guia usará o nome do projeto **EntityProject**.

Depois de criar nosso projeto, definiremos a resolução do nosso jogo para ser executado em x 320 a 480. Para fazer isso, chame `CCScene.SetDefaultDesignResolution` no `GameAppDelegate.ApplicationDidFinishLaunching` método da seguinte maneira:


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    ...

    // New code for resolution setting:
    CCScene.SetDefaultDesignResolution(480, 320, CCSceneResolutionPolicy.ShowAll);
    
    CCScene scene = new CCScene (mainWindow);
    GameLayer gameLayer = new GameLayer ();

    scene.AddChild (gameLayer);
    mainWindow.RunWithScene (scene);
} 
```

Para obter mais informações sobre como lidar com CocosSharp resoluções, consulte nosso [guia de manipular várias resoluções no CocosSharp](~/graphics-games/cocossharp/resolutions.md).


## <a name="adding-content-to-the-project"></a>Adicionando conteúdo ao projeto

Quando nosso projeto tiver sido criado, vamos adicionar os arquivos contidos no [este arquivo zip conteúdo](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Entities.zip?raw=true). Para fazer isso, baixe o arquivo zip e descompacte-o. Adicionar ambos **ship.png** e **bullet.png** para o **conteúdo** pasta. O **conteúdo** pasta estarão dentro de **ativos** pasta no Android e será na raiz do projeto no iOS. Depois de adicionado, deveremos ver ambos os arquivos na **conteúdo** pasta:

![](entities-images/image2.png "Depois de adicionado, ambos os arquivos devem estar na pasta de conteúdo")


## <a name="creating-the-ship-entity"></a>Criar a entidade de remessa

O `Ship` classe será a primeira de entidade do nosso jogo. Para adicionar um `Ship` classe, primeiro crie uma pasta chamada **entidades** no nível raiz do projeto. Adicione uma nova classe na **entidades** pasta chamada `Ship`:

![](entities-images/image3.png "Adicione uma nova classe na pasta entidades chamada Ship")

A primeira alteração que faremos nosso `Ship` classe é para permitir que ela herde os `CCNode` classe. `CCNode` como serve como a classe base para classes comuns de CocosSharp `CCSprite` e `CCLayer`e nos oferece a seguinte funcionalidade:

 - `Position` propriedade para mover a nave pela tela.
 - `Children` propriedade para adicionar um `CCSprite.`
 - `Parent` propriedade, que pode ser usada para anexar `Ship` instâncias de si `CCNodes`. Nós não o utilizarem esse recurso neste tutorial. jogos maiores geralmente aproveitam os anexar entidades outros `CCNodes`. 
 - `AddEventListener` método para responder a entradas para mover a nave.
 - `Schedule` método para marcadores de solução.

Também adicionaremos uma `CCSprite` da instância para que o navio pode ser visto na tela:


```csharp
using System;
using CocosSharp;

namespace EntityProject
{
    public class Ship : CCNode
    {
        CCSprite sprite;

        public Ship () : base()
        {
            sprite = new CCSprite ("ship.png");
            // Center the Sprite in this entity to simplify
            // centering the Ship when it is instantiated
            sprite.AnchorPoint = CCPoint.AnchorMiddle;
            this.AddChild(sprite);
        }
    }
}
```

Em seguida, adicionaremos a nave para nosso `GameLayer` para vê-lo no nosso jogo:


```csharp
public class GameLayer : CCLayer
{
    Ship ship;

    public GameLayer ()
    {
        ship = new Ship ();
        ship.PositionX = 240;
        ship.PositionY = 50;
        this.AddChild (ship);
    } 
    ...
```

Se executarmos nosso jogo que agora veremos nossa entidade de remessa:

![](entities-images/image4.png "Ao executar o jogo, a entidade de remessa será exibida.")


### <a name="why-inherit-from-ccnode-instead-of-ccsprite"></a>Por que herdam de CCNode em vez de CCSprite?

Neste ponto nossos `Ship` classe é um wrapper simples para um `CCSprite` instância. Uma vez que `CCSprite` também herda `CCNode`, podemos pode ter herdado diretamente de `CCSprite`, seria ter reduzido o código no `Ship.cs`. Além disso, herdando diretamente `CCSprite` reduz o número de objetos na memória e pode melhorar o desempenho, fazendo a árvore de dependência menores.

Apesar desses benefícios, estamos herdado de `CCNode` para ocultar alguns do `CCSprite` propriedades de cada instância. Por exemplo, o `Texture` propriedade não deve ser modificada fora do `Ship` de classe e herdando `CCNode` permite ocultar essa propriedade. Os membros públicos de nosso entidades se tornar especialmente importantes conforme cresce um jogo e os desenvolvedores adicionais são adicionados a uma equipe.


## <a name="adding-input-to-the-ship"></a>Adicionando entrada para a remessa

Agora que nosso nave está visível na tela adicionaremos entrada. Nossa abordagem será semelhante na abordagem a [guia BouncingGame](~/graphics-games/cocossharp/bouncing-game.md), exceto que estamos colocando o código de movimentação na `Ship` classe em vez de em que o contém `CCLayer` ou `CCScene`.

Adicione o código ao `Ship` para dar suporte ao movê-lo para onde quer que o usuário toca a tela:


```csharp
public class Ship : CCNode
{
    CCSprite sprite;

    CCEventListenerTouchAllAtOnce touchListener;

    public Ship () : base()
    {
        sprite = new CCSprite ("ship.png");
        // Center the Sprite in this entity to simplify
        // centering the Ship on screen
        sprite.AnchorPoint = CCPoint.AnchorMiddle;
        this.AddChild(sprite);

        touchListener = new CCEventListenerTouchAllAtOnce();
        touchListener.OnTouchesMoved = HandleInput;
        AddEventListener(touchListener, this);

    }

    private void HandleInput(System.Collections.Generic.List<CCTouch> touches, CCEvent touchEvent)
    {
        if(touches.Count > 0)
        {
            CCTouch firstTouch = touches[0];

            this.PositionX = firstTouch.Location.X;
            this.PositionY = firstTouch.Location.Y;
        } 
    }
} 
```

Muitos envie-os para cima jogos implementar uma velocidade máxima, imitando movimentação tradicional baseadas no controlador. Dito isso, simplesmente, implementaremos movimentação imediata para manter nosso código mais curto.


## <a name="creating-the-bullet-entity"></a>Criar a entidade do marcador

A segunda entidade no nosso jogo simple é uma entidade para a exibição de marcadores. Assim como o `Ship` entidade, o `Bullet` entidade conterá um `CCSprite` para que ele apareça na tela. A lógica de movimentação é diferente em que ele não depende de entrada do usuário para a movimentação de; em vez disso, `Bullet` moverá instâncias em uma linha reta usando propriedades de velocidade.

Primeiro, vamos adicionar um novo arquivo de classe ao nosso **entidades** pasta e chamá-lo **marcador**:

![](entities-images/image5.png "Adicione um novo arquivo de classe para a pasta de entidades e chamá-lo de marcador")

Depois de adicionado, modificaremos o `Bullet.cs` de código da seguinte maneira:


```csharp
using System;
using CocosSharp;

namespace EntityProject
{
    public class Bullet : CCNode
    {
        CCSprite sprite;

        public float VelocityX
        {
            get;
            set;
        }

        public float VelocityY
        {
            get;
            set;
        }

        public Bullet () : base()
        {
            sprite = new CCSprite ("bullet.png");
            // Making the Sprite be centered makes
            // positioning easier.
            sprite.AnchorPoint = CCPoint.AnchorMiddle;
            this.AddChild(sprite);

            this.Schedule (ApplyVelocity);
        }

        void ApplyVelocity(float time)
        {
            PositionX += VelocityX * time;
            PositionY += VelocityY * time;
        }
    }
} 
```

Além de alterar o arquivo usado para o `CCSprite` à `bullet.png`, o código na `ApplyVelocity` inclui a lógica de movimentação que se baseia em dois coeficientes: `VelocityX` e `VelocityY`.

O `Schedule` método permite a adição de delegados a ser chamado a cada quadro. Nesse caso, estamos adicionando o `ApplyVelocity` método para que nosso marcador move acordo com seus valores de velocidade. O `Schedule` leva um `Action<float>`, onde o parâmetro float Especifica a quantidade de tempo (em segundos) desde o último quadro, que usamos para implementar a transferência com base no tempo. Desde a hora em valor é medido em segundos, em seguida, nossos valores de velocidade representam movimentação no *pixels por segundo*.


## <a name="adding-bullets-to-gamelayer"></a>Adicionando marcadores para GameLayer

Antes de adicionar qualquer `Bullet` instâncias ao nosso jogo, faremos um contêiner, especificamente um `List<Bullet>`. Modificar o `GameLayer` para que ela inclua uma lista com marcadores:


```csharp
    public class GameLayer : CCLayer
    {
        Ship ship;
        List<Bullet> bullets;

        public GameLayer ()
        {
            ship = new Ship ();
            ship.PositionX = 240;
            ship.PositionY = 50;
            this.AddChild (ship);

            bullets = new List<Bullet> ();
        }
        ... 
```

Em seguida, precisamos preencher o `Bullet` lista. A lógica para quando criar uma `Bullet` devem estar contidos em de `Ship` entidade, mas o `GameLayer` é responsável por armazenar a lista com marcadores. Usamos o padrão de fábrica para permitir que o `Ship` entidade criar `Bullet` instâncias. Esta fábrica exporá um evento que o `GameLayer` pode manipular. 

Para fazer isso primeiro, vamos adicionar uma pasta para nosso projeto chamado **fábricas**, e, em seguida, adicione uma nova classe chamada `BulletFactory`:

![](entities-images/image6.png "Adicionar uma pasta ao projeto chamado fábricas e, em seguida, adicione uma nova classe chamada BulletFactory")

Em seguida, implementaremos o `BulletFactory` classe singleton:


```csharp
using System;

namespace EntityProject
{
    public class BulletFactory
    {
        static Lazy<BulletFactory> self = 
            new Lazy<BulletFactory>(()=>new BulletFactory());

        // simple singleton implementation
        public static BulletFactory Self
        {
            get
            {
                return self.Value;
            }
        }

        public event Action<Bullet> BulletCreated;

        private BulletFactory()
        {

        }

        public Bullet CreateNew()
        {
            Bullet newBullet = new Bullet ();

            if (BulletCreated != null)
            {
                BulletCreated (newBullet);
            }

            return newBullet;
        }
    }
} 
```

O `Ship` entidade manipulará criando `Bullet` instâncias – especificamente, ele manipulará a frequência com que `Bullet` instâncias devem ser criadas (ou seja, a frequência com que o marcador é acionado), sua posição e sua velocidade.

Modificar a `Ship` construtor da entidade para adicionar uma nova `Schedule` chamar e, em seguida, implementar esse método da seguinte maneira:


```csharp
...
public Ship () : base()
{
    sprite = new CCSprite ("ship.png");
    // Center the Sprite in this entity to simplify
    // centering the Ship on screen
    sprite.AnchorPoint = CCPoint.AnchorMiddle;
    this.AddChild(sprite);

    touchListener = new CCEventListenerTouchAllAtOnce();
    touchListener.OnTouchesMoved = HandleInput;
    AddEventListener(touchListener, this);

    Schedule (FireBullet, interval: 0.5f);

}

void FireBullet(float unusedValue)
{
    Bullet newBullet = BulletFactory.Self.CreateNew ();
    newBullet.Position = this.Position;
    newBullet.VelocityY = 100;
} 
...
```

A última etapa é lidar com a criação de novos `Bullet` instâncias de `GameLayer` código. Adicionar um manipulador de eventos para o `BulletCreated` eventos que adiciona o recém-criado `Bullet` às listas apropriadas:


```csharp
...
public GameLayer ()
{
    ship = new Ship ();
    ship.PositionX = 240;
    ship.PositionY = 50;
    this.AddChild (ship);

    bullets = new List<Bullet> ();
    BulletFactory.Self.BulletCreated += HandleBulletCreated;
}

void HandleBulletCreated(Bullet newBullet)
{
    AddChild (newBullet);
    bullets.Add (newBullet);
}
... 
```

Agora podemos executar o jogo e consulte a `Ship` acertar `Bullet` instâncias:

![](entities-images/image1.png "Executar o jogo e a nave será ser instâncias de marcador de solução")


## <a name="why-gamelayer-has-ship-and-bullets-members"></a>Por que GameLayer tem membros de remessa e marcadores

Nossos `GameLayer` classe define dois campos para manter referências a nossas instâncias de entidade (`ship` e `bullets`), mas não faz nada com eles. Além disso, as entidades são responsáveis por seu próprio comportamento, como a movimentação e a solução. Então por que adicionamos `ship` e `bullets` campos `GameLayer`?

O motivo que adicionamos esses membros é que uma implementação de jogo completo exigiria lógica no `GameLayer` para a interação entre as diferentes entidades. Por exemplo, este jogo pode ser ainda mais desenvolvido para incluir os inimigos que podem ser destruídos pelo player. Esses inimigos deverá estar contidos em um `List` no `GameLayer`e a lógica para testar se `Bullet` instâncias entrem em conflito com inimigos seriam feitos no `GameLayer` também. Em outras palavras, o `GameLayer` é a raiz *proprietário* da entidade de todas as instâncias e é responsável por interações entre as instâncias de entidade.


## <a name="bullet-destruction-considerations"></a>Considerações de destruição de marcador

Nosso jogo atualmente não tem o código para destruir `Bullet` instâncias. Cada `Bullet` instância tem lógica para mover na tela, mas ainda não adicionamos nenhum código para destruir qualquer fora da tela `Bullet` instâncias.

Além disso, a destruição de `Bullet` instâncias não podem pertencer em `GameLayer`. Por exemplo, em vez de que está sendo destruído quando fora da tela, o `Bullet` entidade pode ter lógica para se autodestrua após um determinado período de tempo. Nesse caso, o `Bullet` precisa de uma maneira de comunicar-se de que ele deve ser destruído para o `GameLayer`, parecido com o `Ship` entidade comunicados para o `GameLayer` que um novo `Bullet` foi criado por meio o `BulletFactory`.

A solução mais simples é expandir a responsabilidade da classe de fábrica para dar suporte a destruição. Em seguida, a fábrica pode ser notificada de uma instância de entidade que está sendo destruída, que pode ser manipulada por outros objetos, como o `GameLayer` removendo a instância da entidade de suas listas. 

## <a name="summary"></a>Resumo

Este guia mostra como criar entidades de CocosSharp herdando a `CCNode` classe. Essas entidades são objetos independentes, tratamento de criação de seus próprios elementos visuais e lógica personalizada. Este guia designa o código pertence dentro de uma entidade (movimentação e a criação de outras entidades) do código pertence no contêiner de entidade raiz (colisão e outra lógica de interação de entidade).

## <a name="related-links"></a>Links relacionados

- [Documentação da API do CocosSharp](https://developer.xamarin.com/api/namespace/CocosSharp/)
- [Zip de conteúdo](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Entities.zip?raw=true)
