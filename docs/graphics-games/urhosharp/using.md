---
title: Usando o UrhoSharp para criar um jogo 3D
description: Este documento fornece uma visão geral do UrhoSharp, descrevendo cenas, componentes, formas, câmeras, ações, entrada do usuário, som e muito mais.
ms.prod: xamarin
ms.assetid: D9BEAD83-1D9E-41C3-AD4B-3D87E13674A0
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: cb4e524977b53f1a17552298c509d43ccf460f08
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73011645"
---
# <a name="using-urhosharp-to-build-a-3d-game"></a>Usando o UrhoSharp para criar um jogo 3D

Antes de escrever seu primeiro jogo, você deseja se familiarizar com os conceitos básicos: como configurar sua cena, como carregar recursos (isso contém o seu trabalho artístico) e como criar interações simples para seu jogo.

<a name="scenenodescomponentsandcameras"/>

## <a name="scenes-nodes-components-and-cameras"></a>Cenas, nós, componentes e câmeras

O modelo de cena pode ser descrito como um grafo de cena baseado em componente. A cena consiste em uma hierarquia de nós de cena, a partir do nó raiz, que também representa toda a cena. Cada `Node` tem uma transformação 3D (posição, rotação e escala), um nome, uma ID, além de um número arbitrário de componentes.  Os componentes levam um nó à vida, eles podem fazer com que o adicione uma representação visual (`StaticModel`), eles podem emitir som (`SoundSource`), eles podem fornecer um limite de colisão e assim por diante.

Você pode criar seus bastidores e configurar nós usando o [Editor Urho](#urhoeditor)ou pode fazer coisas do seu C# código.  Neste documento, exploraremos as configurações que usam o código, pois ilustram os elementos necessários para que as coisas sejam exibidas na tela

Além de configurar sua cena, você precisa configurar um `Camera`, isso é o que determina o que será mostrado ao usuário.

### <a name="setting-up-your-scene"></a>Configurando sua cena

Normalmente, você criaria esse formulário o método Start:

```csharp
var scene = new Scene ();
// Create the Octree component to the scene. This is required before
// adding any drawable components, or else nothing will show up. The
// default octree volume will be from -1000, -1000, -1000) to
//(1000, 1000, 1000) in world coordinates; it is also legal to place
// objects outside the volume but their visibility can then not be
// checked in a hierarchically optimizing manner
scene.CreateComponent<Octree> ();
// Create a child scene node (at world origin) and a StaticModel
// component into it. Set the StaticModel to show a simple plane mesh
// with a "stone" material. Note that naming the scene nodes is
// optional. Scale the scene node larger (100 x 100 world units)
var planeNode = scene.CreateChild("Plane");
planeNode.Scale = new Vector3 (100, 1, 100);
var planeObject = planeNode.CreateComponent<StaticModel> ();
planeObject.Model = ResourceCache.GetModel ("Models/Plane.mdl");
planeObject.SetMaterial(ResourceCache.GetMaterial("Materials/StoneTiled.xml"));
```

### <a name="components"></a>Componentes

A renderização de objetos 3D, reprodução de som, física e atualizações de lógica com script são habilitadas criando componentes diferentes para os nós chamando `CreateComponent<T>()`.  Por exemplo, configure o nó e o componente de luz como este:

```csharp
// Create a directional light to the world so that we can see something. The
// light scene node's orientation controls the light direction; we will use
// the SetDirection() function which calculates the orientation from a forward
// direction vector.
// The light will use default settings (white light, no shadows)
var lightNode = scene.CreateChild("DirectionalLight");
lightNode.SetDirection (new Vector3(0.6f, -1.0f, 0.8f));
```

Nós criamos acima de um nó com o nome "`DirectionalLight`" e definimos uma direção para ele, mas nada mais.  Agora, podemos transformar o nó acima em um nó emissor claro, anexando um componente `Light` a ele, com `CreateComponent`:

```csharp
var light = lightNode.CreateComponent<Light>();
```

Os componentes criados no `Scene` em si têm uma função especial: para implementar a funcionalidade em toda a cena. Eles devem ser criados antes de todos os outros componentes e incluem o seguinte:

 `Octree`: implementa particionamento espacial e consultas de visibilidade aceleradas. Sem esses objetos 3D não podem ser renderizados.
 `PhysicsWorld`: implementa a simulação física. Componentes de física, como `RigidBody` ou `CollisionShape`, não podem funcionar corretamente sem isso.
 `DebugRenderer`: implementa a renderização de geometria de depuração.

Componentes comuns como `Light`, `Camera` ou `StaticModel`
Não deve ser criado diretamente no `Scene`, mas sim em nós filho.

A biblioteca vem com uma ampla variedade de componentes que você pode anexar a seus nós para levá-los à vida: elementos visíveis ao usuário (modelos), sons, corpos rígidos, formas de colisão, câmeras, fontes leves, emissores de partículas e muito mais.

### <a name="shapes"></a>Formas

Como uma conveniência, várias formas estão disponíveis como nós simples no namespace Urho. Shapes.  Isso inclui caixas, cones, cilindros e planos.

### <a name="camera-and-viewport"></a>Câmera e visor

Assim como a luz, as câmeras são componentes, portanto, você precisará anexar o componente a um nó, desta forma:

```csharp
var CameraNode = scene.CreateChild ("camera");
camera = CameraNode.CreateComponent<Camera>();
CameraNode.Position = new Vector3 (0, 5, 0);
```

Com isso, você criou uma câmera e colocou a câmera no mundo 3D, a próxima etapa é informar ao `Application` que essa é a câmera que você deseja usar, isso é feito com o seguinte código:

```csharp
Renderer.SetViewPort (0, new Viewport (Context, scene, camera, null))
```

E agora você deve ser capaz de ver os resultados da sua criação.

### <a name="identification-and-scene-hierarchy"></a>Identificação e hierarquia de cena

Ao contrário dos nós, os componentes não têm nomes; os componentes dentro do mesmo nó são identificados apenas pelo seu tipo e índice na lista de componentes do nó, que é preenchida na ordem de criação, por exemplo, você pode recuperar o componente `Light` fora do objeto `lightNode` acima da seguinte maneira:

```csharp
var myLight = lightNode.GetComponent<Light>();
```

Você também pode obter uma lista de todos os componentes recuperando a propriedade `Components` que retorna uma `IList<Component>` que você pode usar.

Quando criado, os nós e os componentes obtêm IDs de inteiros globais de cena. Eles podem ser consultados a partir da cena usando as funções `GetNode(uint id)` e `GetComponent(uint id)`. Isso é muito mais rápido do que, por exemplo, fazer consultas de nó de cena com base no nome recursivo.

Não há um conceito interno de um objeto de entidade ou de jogo; em vez disso, o programador deve decidir a hierarquia de nós e em quais nós colocá-las em qualquer lógica de script. Normalmente, os objetos de movimentação livre no mundo 3D seriam criados como filhos do nó raiz. Os nós podem ser criados com ou sem um nome usando `CreateChild`. A exclusividade dos nomes de nó não é imposta.

Sempre que houver uma composição hierárquica, é recomendável (e, na verdade, necessário, porque os componentes não têm suas próprias transformações 3D) para criar um nó filho.

Por exemplo, se um caractere estava segurando um objeto por sua mão, o objeto deve ter seu próprio nó, que seria pai do Bone à mão do caractere (também uma `Node`).  A exceção é a `CollisionShape`física, que pode ser offsettedda e girada individualmente em relação ao nó.

Observe que `Scene`própria transformação é intencionalmente ignorada como uma otimização ao calcular transformações derivadas mundiais de nós filho, portanto, alterá-la não tem nenhum efeito e deve ser deixada como está (posição na origem, sem rotação, sem dimensionamento).

`Scene` nós podem ser repais livremente. Os componentes em contraste sempre pertencem ao nó ao qual eles se conectaram e não podem ser movidos entre nós. Ambos os nós e componentes fornecem uma função `Remove` para fazer isso sem precisar passar pelo pai. Depois que o nó for removido, nenhuma operação no nó ou componente em questão será segura depois de chamar essa função.

Também é possível criar um `Node` que não pertença a uma cena. Isso é útil, por exemplo, com uma câmera que se move em uma cena que pode ser carregada ou salva, pois, em seguida, a câmera não será salva junto com a cena real e não será destruída quando a cena for carregada. No entanto, observe que criar geometria, física ou componentes de script para um nó desanexado e, em seguida, movê-lo para uma cena mais tarde fará com que esses componentes não funcionem corretamente.

### <a name="scene-updates"></a>Atualizações de cena

Uma cena cujas atualizações estão habilitadas (padrão) será atualizada automaticamente em cada iteração de loop principal.  O manipulador de eventos de `SceneUpdate` do aplicativo é invocado nele.

Nós e componentes podem ser excluídos da atualização de cena desabilitando-os, consulte `Enabled`.  O comportamento depende do componente específico, mas, por exemplo, desabilitar um componente desenhável também o torna invisível, ao mesmo tempo em que a desabilitação de um componente de origem de som o desativa. Se um nó estiver desabilitado, todos os seus componentes serão tratados como desabilitados, independentemente de seu próprio estado de Habilitação/desabilitação.

## <a name="adding-behavior-to-your-components"></a>Adicionando o comportamento aos seus componentes

A melhor maneira de estruturar seu jogo é tornar seu próprio componente que encapsula um ator ou elemento em seu jogo.  Isso torna o recurso autocontido, dos ativos usados para exibi-lo, ao seu comportamento.

A maneira mais simples de adicionar comportamento a um componente é usar ações, que são instruções que você pode colocar em fila e combiná- C# las com programação assíncrona.  Isso permite que o comportamento do seu componente seja de nível muito alto e torna mais simples entender o que está acontecendo.

Como alternativa, você pode controlar exatamente o que acontece com seu componente atualizando suas propriedades de componente em cada quadro (discutido na seção comportamento baseado em quadro).

### <a name="actions"></a>Ações

Você pode adicionar comportamento a nós com muito facilidade usando ações.  As ações podem alterar várias propriedades de nó e executá-las em um período de tempo ou repeti-las várias vezes com uma determinada curva de animação.

Por exemplo, considere um nó "nuvem" em sua cena, você pode esmaecer assim:

```csharp
await cloud.RunActionsAsync (new FadeOut (duration: 3))
```

As ações são objetos imutáveis, o que permite que você reutilize a ação para conduzir diferentes objetos.

Um idioma comum é criar uma ação que execute a operação inversa:

```csharp
var gotoExit = new MoveTo (duration: 3, position: exitLocation);
var return = gotoExit.Reverse ();
```

O exemplo a seguir esmaecerá o objeto por um período de três segundos.  Você também pode executar uma ação depois de outra, por exemplo, você pode primeiro mover a nuvem e, em seguida, ocultá-la:

```csharp
await cloud.RunActionsAsync (
    new MoveBy  (duration: 1.5f, position: new Vector3(0, 0, 15),
    new FadeOut (duration: 3));
```

Se você quiser que ambas as ações ocorram ao mesmo tempo, poderá usar a ação paralela e fornecer todas as ações que você deseja que sejam executadas em paralelo:

```csharp
  await cloud.RunActionsAsync (
    new Parallel (
      new MoveBy  (duration: 3, position: new Vector3(0, 0, 15),
      new FadeOut (duration: 3)));
```

No exemplo acima, a nuvem será movida e esmaeceda ao mesmo tempo.

Você observará que eles estão usando C#`await`, o que permite que você pense linearmente sobre o comportamento que você deseja obter.

### <a name="basic-actions"></a>Ações básicas

Essas são as ações com suporte no UrhoSharp:

- Movendo nós: `MoveTo`, `MoveBy`, `Place`, `BezierTo`, `BezierBy`, `JumpTo`, `JumpBy`
- Nós em rotação: `RotateTo`, `RotateBy`
- Dimensionando nós: `ScaleTo`, `ScaleBy`
- Nós de esmaecimento: `FadeIn`, `FadeTo`, `FadeOut`, `Hide`, `Blink`
- Tonalidade: `TintTo`, `TintBy`
- Instantâneos: `Hide`, `Show`, `Place`, `RemoveSelf`, `ToggleVisibility`
- Looping: `Repeat`, `RepeatForever`, `ReverseTime`

Outros recursos avançados incluem a combinação das ações de `Spawn` e `Sequence`.

### <a name="easing---controlling-the-speed-of-your-actions"></a>Facilitando o controle da velocidade de suas ações

A atenuação é uma maneira que direciona a maneira como a animação se desdobrará e pode tornar suas animações muito mais agradáveis.  Por padrão, suas ações terão um comportamento linear, por exemplo, uma ação `MoveTo` teria uma movimentação muito robótica.  Você pode encapsular suas ações em uma ação de atenuação para alterar o comportamento, por exemplo, uma que iniciaria lentamente a movimentação, acelerar e atingir o final (`EasyInOut`) lentamente.

Você faz isso encapsulando uma ação existente em uma ação de atenuação, por exemplo:

```csharp
await cloud.RunActionAsync (
   new EaseInOut (
     new MoveTo (duration: 3, position: new Vector (0,0,15)), rate:1))
```

Há muitos modos de atenuação, o gráfico a seguir mostra os vários tipos de atenuação e seu comportamento no valor do objeto que eles estão controlando no período de tempo, do início ao fim:

![Modos de atenuação](using-images/easing.png "Este gráfico mostra os vários tipos de atenuação e seu comportamento no valor do objeto que eles estão controlando durante o período de tempo")

### <a name="using-actions-and-async-code"></a>Usando ações e código assíncrono

Em sua subclasse de `Component`, você deve introduzir um método assíncrono que prepara o comportamento do componente e conduz a funcionalidade para ele.
Em seguida, você invocaria esse método C# usando a palavra-chave`await`de outra parte do seu programa, o método `Application.Start`ou em resposta a um usuário ou Story Point em seu aplicativo.

Por exemplo:

```csharp
class Robot : Component {
    public bool IsAlive;
    async void Launch ()
    {
        // Dress up our robot
        var cache = Application.ResourceCache;
        var model = node.CreateComponent<StaticModel>();
        model.Model = cache.GetModel ("robot.mdl"));
        model.SetMaterial (cache.GetMaterial ("robot.xml"));
        Node.SetScale (1);

        // Bring the robot into our scene
        await Node.RunActionsAsync(
            new MoveBy(duration: 0.6f, position: new Vector3(0, -2, 0)));
        // Make him move around to avoid the user fire
        MoveRandomly(minX: 1, maxX: 2, minY: -3, maxY: 3, duration: 1.5f);
        // And simultaneously have him shoot at the user
        StartShooting();
    }

    protected async void MoveRandomly (float minX, float maxX,
                                       float minY, float maxY,
                       float duration)
    {
        while (IsAlive){
            var moveAction = new MoveBy(duration,
                new Vector3(RandomHelper.NextRandom(minX, maxX),
                            RandomHelper.NextRandom(minY, maxY), 0));
            await Node.RunActionsAsync(moveAction, moveAction.Reverse());
        }
    }
    protected async void StartShooting()
    {
        while (IsAlive && Node.Components.Count > 0){
            foreach (var weapon in Node.Components.OfType<Weapon>()){
                await weapon.FireAsync(false);
                if (!IsAlive)
                    return;
            }
            await Node.RunActionsAsync(new DelayTime(0.1f));
        }
    }
}
```

No método `Launch` acima de três ações são iniciadas: o robô entra na cena, essa ação alterará o local do nó em um período de 0,6 segundos.  Como essa é uma opção assíncrona, isso ocorrerá simultaneamente como a próxima instrução, que é a chamada para `MoveRandomly`.  Esse método irá alterar a posição do robô em paralelo para um local aleatório.  Isso é feito executando duas ações compostas, a movimentação para um novo local e voltando para a posição original e repete isso, desde que o robô permaneça vivo.  E para tornar as coisas mais interessantes, o robô continuará a ser solucionado simultaneamente.  A captura só será iniciada a cada 0,1 segundos.

### <a name="frame-based-behavior-programming"></a>Programação de comportamento baseada em quadros

Se você quiser controlar o comportamento do seu componente em uma base quadro a quadro em vez de usar ações, o que você faria é substituir o método `OnUpdate` de sua subclasse `Component`.  Esse método é invocado uma vez por quadro e é invocado somente se você definir a propriedade ReceiveSceneUpdates como true.

O seguinte mostra como criar um componente `Rotator`, que é então anexado a um nó, o que faz com que o nó gire:

```csharp
class Rotator : Component {
    public Rotator()
    {
        ReceiveSceneUpdates = true;
    }
    public Vector3 RotationSpeed { get; set; }
    protected override void OnUpdate(float timeStep)
    {
        Node.Rotate(new Quaternion(
            RotationSpeed.X  timeStep,
            RotationSpeed.Y  timeStep,
            RotationSpeed.Z  timeStep),
            TransformSpace.Local);
    }
}
```

E é assim que você anexaria esse componente a um nó:

```csharp
Node boxNode = new Node();
var rotator = new Rotator() { RotationSpeed = rotationSpeed };
boxNode.AddComponent (rotator);
```

### <a name="combining-styles"></a>Combinando estilos

Você pode usar o modelo baseado em Async/Action para programar grande parte do comportamento, o que é ótimo para o estilo de programação acionar e esquecer, mas você também pode ajustar o comportamento do componente para também executar um código de atualização em cada quadro.

Por exemplo, na demonstração de SamplyGame, isso é usado na classe `Enemy` codifica que o comportamento básico usa ações, mas também garante que os componentes apontem para o usuário definindo a direção do nó com `Node.LookAt`:

```csharp
    protected override void OnUpdate(SceneUpdateEventArgs args)
    {
        Node.LookAt(
            new Vector3(0, -3, 0),
            new Vector3(0, 1, -1),
            TransformSpace.World);
        base.OnUpdate(args);
    }
```

## <a name="loading-and-saving-scenes"></a>Carregando e salvando cenas

Os bastidores podem ser carregados e salvos em formato XML; Consulte as funções `LoadXml` e `SaveXML`. Quando uma cena é carregada, todo o conteúdo existente nela (nós e componentes filho) é removido primeiro. Nós e componentes marcados como temporários com a propriedade `Temporary` não serão salvos. O serializador lida com todos os componentes e propriedades internos, mas não é inteligente o suficiente para lidar com propriedades e campos personalizados definidos em suas subclasses de componentes. No entanto, ele fornece dois métodos virtuais para isso:

 `OnSerialize` onde você pode registrar Estados personalizados para a serialização

 `OnDeserialized` onde você pode obter os Estados personalizados salvos.

Normalmente, um componente personalizado se parecerá com o seguinte:

```csharp
class MyComponent : Component {
    // Constructor needed for deserialization
    public MyComponent(IntPtr handle) : base(handle) { }
    public MyComponent() { }
    // user defined properties (managed state):
    public Quaternion MyRotation { get; set; }
    public string MyName { get; set; }

    public override void OnSerialize(IComponentSerializer ser)
    {
        // register our properties with their names as keys using nameof()
        ser.Serialize(nameof(MyRotation), MyRotation);
        ser.Serialize(nameof(MyName), MyName);
    }

    public override void OnDeserialize(IComponentDeserializer des)
    {
        MyRotation = des.Deserialize<Quaternion>(nameof(MyRotation));
        MyName = des.Deserialize<string>(nameof(MyName));
    }
    // called when the component is attached to some node
    public override void OnAttachedToNode()
    {
        var node = this.Node;
    }
}
```

### <a name="object-prefabs"></a>Pré-fabricados de objeto

Apenas carregar ou salvar cenas inteiras não é flexível o suficiente para jogos em que novos objetos precisam ser criados dinamicamente. Por outro lado, a criação de objetos complexos e a definição de suas propriedades no código também serão entediantes. Por esse motivo, também é possível salvar um nó de cena que incluirá seus nós filho, componentes e atributos. Posteriormente, eles podem ser facilmente carregados como um grupo.  Esse objeto salvo costuma ser chamado de pré-fabricado. Há três maneiras de fazer isso:

- No código chamando `Node.SaveXml` no nó
- No editor, selecionando o nó na janela hierarquia e escolhendo "salvar nó como" no menu "arquivo".
- Usando o comando "node" no `AssetImporter`, que salvará a hierarquia do nó de cena e todos os modelos contidos no ativo de entrada (por exemplo, um arquivo arquivo COLLADA)

Para instanciar o nó salvo em uma cena, chame `InstantiateXml`. O nó será criado como um filho da cena, mas poderá ser repai livremente depois disso. A posição e a rotação para colocar o nó precisam ser especificadas. O código a seguir demonstra como criar uma instância de um `Ninja.xm` pré-fabricado para uma cena com a posição e a rotação desejadas:

```csharp
var prefabPath = Path.Combine (FileSystem.ProgramDir,"Data/Objects/Ninja.xml");
using (var file = new File(Context, prefabPath, FileMode.Read))
{
    scene.InstantiateXml(file, desiredPos, desiredRotation,
        CreateMode.Replicated);
}
```

## <a name="events"></a>Eventos

UrhoObjects geram vários eventos, eles são exibidos como C# eventos em várias classes que os geram.  Além do C#modelo de evento baseado em, também é possível usar os métodos`SubscribeToXXX`que lhe permitirão assinar e manter um token de assinatura que você pode usar posteriormente para cancelar a assinatura.  A diferença é que o primeiro permitirá que vários chamadores se assinem, enquanto o segundo só permite um, mas permite que a abordagem de estilo lambda mais interessante seja usada e, ainda assim, permita uma remoção fácil da assinatura.  Eles são mutuamente exclusivos.

Ao assinar um evento, você deve fornecer um método que aceite um argumento com os argumentos de evento apropriados.

Por exemplo, é assim que você assina um evento de botão do mouse para baixo:

```csharp
public void override Start ()
{
    UI.MouseButtonDown += HandleMouseButtonDown;
}

void HandleMouseButtonDown(MouseButtonDownEventArgs args)
{
    Console.WriteLine ("button pressed");
}
```

Com estilo lambda:

```csharp
public void override Start ()
{
    UI.MouseButtonDown += args => {
        Console.WriteLine ("button pressed");
    };
}
```

Às vezes, você desejará parar de receber notificações para o evento, nesses casos, salvar o valor de retorno da chamada para `SubscribeTo` método e invocar o método de cancelamento de assinatura nele:

```csharp
Subscription mouseSub;

public void override Start ()
{
    mouseSub = UI.SubscribeToMouseButtonDown (args => {
    Console.WriteLine ("button pressed");
      mouseSub.Unsubscribe ();
    };
}
```

O parâmetro recebido pelo manipulador de eventos é uma classe de argumentos de evento fortemente tipada que será específica para cada evento e contém a carga do evento.

## <a name="responding-to-user-input"></a>Respondendo à entrada do usuário

Você pode assinar vários eventos, como pressionamentos de teclas, inscrevendo-se no evento e respondendo à entrada que está sendo entregue:

```csharp
Start ()
{
    UI.KeyDown += HandleKeyDown;
}

void HandleKeyDown (KeyDownEventArgs arg)
{
     switch (arg.Key){
     case Key.Esc: Engine.Exit (); return;
}
```

Mas, em muitos cenários, você deseja que os manipuladores de atualização de cena verifiquem o status atual das chaves quando elas estão sendo atualizadas e atualize seu código de acordo.  Por exemplo, o seguinte pode ser usado para atualizar o local da câmera com base na entrada do teclado:

```csharp
protected override void OnUpdate(float timeStep)
{
    Input input = Input;
    // Movement speed as world units per second
    const float moveSpeed = 4.0f;
    // Read WASD keys and move the camera scene node to the
    // corresponding direction if they are pressed
    if (input.GetKeyDown(Key.W))
        CameraNode.Translate(Vector3.UnitY  moveSpeed  timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.S))
        CameraNode.Translate(new Vector3(0.0f, -1.0f, 0.0f)  moveSpeed  timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.A))
        CameraNode.Translate(new Vector3(-1.0f, 0.0f, 0.0f)  moveSpeed  timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.D))
        CameraNode.Translate(Vector3.UnitX  moveSpeed  timeStep, TransformSpace.Local);
}
```

## <a name="resources-assets"></a>Recursos (ativos)

Os recursos incluem a maioria das coisas em UrhoSharp que são carregadas do armazenamento em massa durante a inicialização ou tempo de execução:

- `Animation`-usado para animações de esqueleto
- `Image`-representa as imagens armazenadas em uma variedade de formatos gráficos
- Modelos de `Model` 3D
- `Material`-materiais usados para renderizar modelos.
- `ParticleEffect`- [descreve](https://urho3d.github.io/documentation/1.4/_particles.html) como o emissor de partículas funciona, consulte "[partículas](#particles)" abaixo.
- `Shader`-sombreadores personalizados
- `Sound`-sons para reprodução, consulte "[som](#sound)" abaixo.
- técnicas de renderização de `Technique` material
- textura `Texture2D`-2D
- textura de `Texture3D`-3D
- textura de `TextureCube` cubo
- `XmlFile`

Eles são gerenciados e carregados pelo subsistema `ResourceCache` (disponível como `Application.ResourceCache`).

Os próprios recursos são identificados por seus caminhos de arquivo, em relação aos diretórios de recursos registrados ou aos arquivos de pacote. Por padrão, o mecanismo registra os diretórios de recursos `Data` e `CoreData`, ou os pacotes `Data.pak` e `CoreData.pak` se existirem.

Se o carregamento de um recurso falhar, um erro será registrado e uma referência nula será retornada.

O exemplo a seguir mostra uma maneira típica de buscar um recurso do cache de recursos.  Nesse caso, uma textura para um elemento de interface do usuário usa a propriedade `ResourceCache` da classe `Application`.

```csharp
healthBar.SetTexture(ResourceCache.GetTexture2D("Textures/HealthBarBorder.png"));
```

Os recursos também podem ser criados manualmente e armazenados no cache de recursos como se estivessem carregados do disco.

Os orçamentos de memória podem ser definidos por tipo de recurso: se os recursos consomem mais memória do que o permitido, os recursos mais antigos serão removidos do cache se não estiverem mais em uso. Por padrão, os orçamentos de memória são definidos como ilimitados.

### <a name="bringing-3d-models-and-images"></a>Trazendo imagens e modelos 3D

O Urho3D tenta usar formatos de arquivo existentes sempre que possível e definir formatos de arquivo personalizados somente quando for absolutamente necessário, como para modelos (. mdl) e para animações (. ani). Para esses tipos de ativos, o Urho fornece um conversor- [AssetImporter](https://urho3d.github.io/documentation/1.4/_tools.html) que pode consumir muitos formatos 3D populares, como FBX, Dae, 3ds e obj, etc.

Também há um suplemento útil para [https://github.com/reattiva/Urho3D-Blender](https://github.com/reattiva/Urho3D-Blender) do Blender que pode exportar seus ativos do Blender no formato adequado para Urho3D.

### <a name="background-loading-of-resources"></a>Carregamento em segundo plano de recursos

Normalmente, ao solicitar recursos usando um dos `ResourceCache`método `Get`, eles são carregados imediatamente no thread principal, o que pode levar vários milissegundos para todas as etapas necessárias (carregar arquivo do disco, analisar dados, carregar na GPU, se necessário) e pode Portanto, resultam em quedas de taxa de quadros.

Se você souber antecipadamente quais recursos você precisa, poderá solicitar que eles sejam carregados em um thread em segundo plano chamando `BackgroundLoadResource`. Você pode assinar o evento carregado de segundo plano do recurso usando o método `SubscribeToResourceBackgroundLoaded`. Ele saberá se o carregamento realmente foi um sucesso ou uma falha. Dependendo do recurso, apenas uma parte do processo de carregamento pode ser movida para um thread em segundo plano, por exemplo, a etapa de carregamento de GPU de acabamento sempre precisa ocorrer no thread principal. Observe que se você chamar um dos métodos de carregamento de recursos para um recurso que é enfileirado para carregamento em segundo plano, o thread principal será interrompido até que seu carregamento seja concluído.

A funcionalidade de carregamento de cena assíncrona `LoadAsync` e `LoadAsyncXML` tem a opção de carregar primeiro os recursos em segundo plano antes de prosseguir para carregar o conteúdo da cena. Ele também pode ser usado para carregar apenas os recursos sem modificar a cena, especificando o `LoadMode.ResourcesOnly`. Isso permite preparar um arquivo de pré-fabricado de cena ou de objeto para instanciação rápida.

Por fim, o tempo máximo (em milissegundos) gasto em cada quadro na conclusão de recursos carregados em segundo plano pode ser configurado definindo a propriedade `FinishBackgroundResourcesMs` no `ResourceCache`.

<a name="sound"/>

## <a name="sound"></a>Sonora

O som é uma parte importante do jogo e a estrutura UrhoSharp fornece uma maneira de reproduzir sons em seu jogo.  Você joga sons anexando um `SoundSource`
componente para um `Node` e, em seguida, reproduzir um arquivo nomeado de seus recursos.

É assim que isso é feito:

```csharp
var explosionNode = Scene.CreateChild();
var sound = explosionNode.CreateComponent<SoundSource>();
soundSource.Play(Application.ResourceCache.GetSound("Sounds/ding.wav"));
soundSource.Gain = 0.5f;
soundSource.AutoRemove = true;
```

<a name="particles"/>

## <a name="particles"></a>Partículas

As partículas fornecem uma maneira simples de adicionar alguns efeitos simples e baratos ao seu aplicativo.  Você pode consumir partículas armazenadas no formato PEX, usando ferramentas como [http://onebyonedesign.com/flash/particleeditor/](http://onebyonedesign.com/flash/particleeditor/).

As partículas são componentes que podem ser adicionados a um nó.  Você precisa chamar o método de `CreateComponent<ParticleEmitter2D>` do nó para criar a partícula e, em seguida, configurar a partícula definindo a Propriedade Effect como um efeito 2D que é carregado do cache de recursos.

Por exemplo, você pode invocar esse método em seu componente para mostrar algumas partículas que são renderizadas como uma explosão quando ele atinge:

```csharp
public async void Explode (Component target)
{
    // show a small explosion when the missile reaches an aircraft.
    var cache = Application.ResourceCache;
    var explosionNode = Scene.CreateChild();
    explosionNode.Position = target.Node.WorldPosition;
    explosionNode.SetScale(1f);
    var particle = explosionNode.CreateComponent<ParticleEmitter2D>();
    particle.Effect = cache.GetParticleEffect2D("explosion.pex");
    var scaleAction = new ScaleTo(0.5f, 0f);
    await explosionNode.RunActionsAsync(
        scaleAction, new DelayTime(0.5f));
    explosionNode.Remove();
}
```

O código acima criará um nó de explosão que é anexado ao seu componente atual, dentro deste nó de explosão, criamos um emissor de partícula 2D e o configuramos definindo a Propriedade Effect.  Executamos duas ações, uma que dimensiona o nó para ser menor e outro que o deixa com esse tamanho por 0,5 segundos.  Em seguida, removemos a explosão, que também remove o efeito de partícula da tela.

A partícula acima é renderizada desta forma ao usar uma textura de esfera:

![Partículas com uma textura de esfera](using-images/image-1.png "A partícula acima é renderizada desta forma ao usar uma textura de esfera")

E essa é a aparência se você usar uma textura de bloco:

![Partículas com uma textura de caixa](using-images/image-2.png "E isso é o que parece se usar uma textura de bloco")

## <a name="multi-threading-support"></a>Suporte a vários threads

UrhoSharp é uma única biblioteca threaded.  Isso significa que você não deve tentar invocar métodos no UrhoSharp a partir de um thread em segundo plano, ou você pode prejudicar o estado do aplicativo e, provavelmente, travar seu aplicativo.

Se você quiser executar algum código em segundo plano e, em seguida, atualizar os componentes do Urho na interface do usuário principal, poderá usar o `Application.InvokeOnMain(Action)`
método.  Além disso, você pode C# usar o Await e as APIs de tarefa do .net para garantir que o código seja executado no thread apropriado.

## <a name="urhoeditor"></a>UrhoEditor

Você pode baixar o editor de Urho para sua plataforma do [site do Urho](http://urho3d.github.io/), ir para downloads e escolher a versão mais recente.

## <a name="copyrights"></a>Direitos autorais

Esta documentação contém conteúdo original da Xamarin Inc, mas se desenha extensivamente a partir da documentação de software livre para o projeto Urho3D e contém capturas de tela do projeto Cocos2D.
