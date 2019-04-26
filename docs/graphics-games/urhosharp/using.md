---
title: Using UrhoSharp To Build A 3D Game
description: Este documento fornece uma visão geral do UrhoSharp, descrevendo as cenas, componentes, formas, câmeras, ações, entrada do usuário, som e muito mais.
ms.prod: xamarin
ms.assetid: D9BEAD83-1D9E-41C3-AD4B-3D87E13674A0
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 5e5c4f1545d39befde6574338ec4c1ca4037ad8b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61352064"
---
# <a name="using-urhosharp-to-build-a-3d-game"></a>Using UrhoSharp To Build A 3D Game

Antes de escrever seu primeiro jogo, você deseja Familiarize-se com as Noções básicas: como configurar sua cena, como carregar recursos (que contém sua arte final) e como criar interações simples para o seu jogo.

<a name="scenenodescomponentsandcameras"/>

## <a name="scenes-nodes-components-and-cameras"></a>Nos bastidores, nós, componentes e câmeras

O modelo de cena pode ser descrito como um grafo de cena baseadas em componentes. A cena consiste em uma hierarquia de nós da cena, começando a partir do nó raiz, que também representa a cena inteira. Cada [ `Node` ](https://developer.xamarin.com/api/type/Urho.Node/) tem uma transformação 3D (posição, rotação e escala), um nome, uma ID, além de um número arbitrário de componentes.  Componentes coloque um nó à vida, eles podem tornar a adicionar uma representação visual ([`StaticModel`](https://developer.xamarin.com/api/type/Urho.StaticModel)), eles podem emitir um som ([`SoundSource`](https://developer.xamarin.com/api/type/Urho.Audio.SoundSource)), eles podem fornecer um limite de colisão e assim por diante.

Você pode criar suas cenas e nós de instalação usando o [Urho Editor](#urhoeditor), ou você pode fazer coisas do seu código c#.  Neste documento, exploraremos o uso de código, definindo as configurações conforme elas ilustram os elementos necessários para colocar as coisas sejam exibidos na tela

Além de configurar sua cena, é preciso configurar uma [ `Camera` ](https://developer.xamarin.com/api/type/Urho.Camera/), isso é o que determina o que será obter mostrado ao usuário.

### <a name="setting-up-your-scene"></a>Como configurar sua cena

Você normalmente criaria este formulário seu método de início:

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

Renderizar objetos 3D, a reprodução de som, física e lógica de scripts atualizações são todos habilitadas com a criação de diferentes componentes em nós chamando [ `CreateComponent<T>()` ](https://developer.xamarin.com/api/member/Urho.Node.CreateComponent%3CT%3E/p/Urho.CreateMode/System.UInt32/).  Por exemplo, configuração de seu nó e o componente de luz como este:

```csharp
// Create a directional light to the world so that we can see something. The
// light scene node's orientation controls the light direction; we will use
// the SetDirection() function which calculates the orientation from a forward
// direction vector.
// The light will use default settings (white light, no shadows)
var lightNode = scene.CreateChild("DirectionalLight");
lightNode.SetDirection (new Vector3(0.6f, -1.0f, 0.8f));
```

Que criamos acima de um nó com o nome "`DirectionalLight`" e definir uma direção para ele, mas nada mais.  Agora, podemos pode transformar o nó acima em um nó de emissão de luz, anexando uma [ `Light` ](https://developer.xamarin.com/api/type/Urho.Light/) componente a ele, com `CreateComponent`:

```csharp
var light = lightNode.CreateComponent<Light>();
```

Os componentes criados para o `Scene` em si desempenham um papel especial: para implementar a funcionalidade de toda a cena. Eles devem ser criados antes de todos os outros componentes e incluem o seguinte:

* [`Octree`](https://developer.xamarin.com/api/type/Urho.Octree/): implementa o particionamento espacial e acelerada consultas de visibilidade. Sem esse 3D objetos não podem ser renderizados.
* [`PhysicsWorld`](https://developer.xamarin.com/api/type/Urho.Physics.PhysicsWorld/): implementa a simulação de física. Como os componentes de física [ `RigidBody` ](https://developer.xamarin.com/api/type/Urho.Physics.RigidBody/) ou [ `CollisionShape` ](https://developer.xamarin.com/api/type/Urho.Physics.CollisionShape/) pode não funcionar corretamente sem isso.
* [`DebugRenderer`](https://developer.xamarin.com/api/type/Urho.DebugRenderer/): implementa depurar a renderização de geometria.

Componentes comuns, como [ `Light` ](https://developer.xamarin.com/api/type/Urho.Light), [ `Camera` ](https://developer.xamarin.com/api/type/Urho.Camera) ou [`StaticModel`](https://developer.xamarin.com/api/type/Urho.StaticModel)
não devem ser criadas diretamente para o [ `Scene` ](https://developer.xamarin.com/api/type/Urho.Scene), mas em vez disso, em nós filho.

A biblioteca vem com uma ampla variedade de componentes que você pode anexar a seus nós para colocá-las de vida: elementos visíveis ao usuário (modelos), sons, corpos rígidos, formas de colisão, câmeras, fontes de luz, emissores de partículas e muito mais.

### <a name="shapes"></a>Formas

Como uma conveniência, várias formas estão disponíveis como nós simples no namespace Urho.Shapes.  Isso inclui caixas, esferas, cones, cilindros e planos.

### <a name="camera-and-viewport"></a>Câmera e o visor

Assim como a luz, câmeras são componentes, portanto, você precisará anexar o componente a um nó, isso é semelhante a esta:

```csharp
var CameraNode = scene.CreateChild ("camera");
camera = CameraNode.CreateComponent<Camera>();
CameraNode.Position = new Vector3 (0, 5, 0);
```

Com isso, você criou uma câmera e você tenha colocado a câmera no mundo 3D, a próxima etapa é informar o `Application` que se trata a câmera que você deseja usar, isso é feito com o código a seguir:

```csharp
Renderer.SetViewPort (0, new Viewport (Context, scene, camera, null))
```

E agora você deve ser capaz de ver os resultados da sua criação.

### <a name="identification-and-scene-hierarchy"></a>Hierarquia de identificação e a cena

Ao contrário de nós, os componentes não têm nomes; componentes dentro do mesmo nó somente são identificados por seu tipo e o índice na lista de componentes do nó, que será preenchida na ordem de criação, por exemplo, você pode recuperar o [ `Light` ](https://developer.xamarin.com/api/type/Urho.Light) componente do `lightNode` objeto acima como este:

```csharp
var myLight = lightNode.GetComponent<Light>();
```

Você também pode obter uma lista de todos os componentes, recuperando os [ `Components` ](https://developer.xamarin.com/api/property/Urho.Node.Components/) propriedade que retorna um `IList<Component>` que você pode usar.

Quando criado, nós e componentes de obtém IDs de inteiro global de cena. Eles podem ser consultados de cena usando as funções [ `GetNode(uint id)` ](https://developer.xamarin.com/api/member/Urho.Scene.GetNode/p/System.UInt32/) e [ `GetComponent(uint id)` ](https://developer.xamarin.com/api/member/Urho.Scene.GetComponent/p/System.UInt32/). Isso é muito mais rápido do que fazer por exemplo recursivo de consultas do nó de cena com base no nome.

Não há nenhum conceito interno de uma entidade ou um objeto do jogo; em vez disso, é responsabilidade do programador para decidir a hierarquia de nós e em quais nós colocar qualquer lógica de scripts. Normalmente, movendo a liberar objetos no mundo 3D seriam criados como filhos do nó raiz. Nós podem ser criados com ou sem um nome usando [ `CreateChild()` ](https://developer.xamarin.com/api/member/Urho.Node.CreateChild/p/System.String/Urho.CreateMode/System.UInt32/). Exclusividade dos nomes de nó não é imposta.

Sempre que houver alguns composição hierárquica, é recomendado (e de fato necessário, porque os componentes não têm suas próprias transformações 3D) para criar um nó filho.

Por exemplo se um caractere estava mantendo um objeto em sua mão, o objeto deve ter seu próprio nó, o que seria como pai para funções básicas de mão do caractere (também uma [ `Node` ](https://developer.xamarin.com/api/type/Urho.Node/)).  A exceção é a física [ `CollisionShape` ](https://developer.xamarin.com/api/type/Urho.Physics.CollisionShape), que pode ser offsetted e girado individualmente em relação ao nó.

Observe que [ `Scene` ](https://developer.xamarin.com/api/type/Urho.Node/)do proprietário transformação propositadamente será ignorada como uma otimização ao calcular as transformações de mundo derivado de nós filho, portanto, a alteração não tem nenhum efeito e deve ser deixado como está (posição na origem, nenhuma rotação Nenhum dimensionamento.)

[`Scene`](https://developer.xamarin.com/api/type/Urho.Node/) nós podem ser reassociados livremente. Por outro lado componentes pertencem sempre para o nó anexado a e não podem ser movidos entre nós. Nós e componentes fornecem uma [ `Remove()` ](https://developer.xamarin.com/api/member/Urho.Node.Remove()/) função para fazer isso sem ter que passar por meio do pai. Depois que o nó for removido, nenhuma operação no nó ou componente em questão é seguras depois de chamar essa função.

Também é possível criar um `Node` que não pertence a uma cena. Isso é útil por exemplo com uma câmera movendo em uma cena que pode ser carregada ou salvos, porque, em seguida, a câmera não serão salvos juntamente com a cena real e não será destruída quando a cena é carregada. No entanto, observe que Criando componentes de geometria, física ou script em um nó desanexado e, em seguida, movê-lo em uma cena posteriormente fará com que esses componentes não funcione corretamente.

### <a name="scene-updates"></a>Atualizações de cena

Uma cena cujas atualizações estão habilitadas (padrão) será atualizado automaticamente em cada iteração do loop principal.  O aplicativo [ `SceneUpdate` ](https://developer.xamarin.com/api/event/Urho.Scene.SceneUpdate/) manipulador de eventos é invocado.

Nós e os componentes podem ser excluídos da atualização de cena, desabilitá-las, consulte [ `Enabled` ](https://developer.xamarin.com/api/member/Urho.Node.Enabled).  O comportamento depende do componente específico, mas, por exemplo, um componente desenhável a desabilitação também torna invisível, enquanto a desabilitação de um componente de origem som retira a ele. Se um nó estiver desabilitado, todos os seus componentes são tratados como desabilitados, independentemente de seu próprio estado habilitar/desabilitar.

## <a name="adding-behavior-to-your-components"></a>Adicionando o comportamento para os seus componentes

A melhor maneira de estruturar seu jogo é fazer com que seu próprio componente que encapsulam um ator ou um elemento em seu jogo.  Isso faz com que o recurso autocontido, de que os ativos utilizados para exibi-lo, ao seu comportamento.

A maneira mais simples de Adicionar comportamento a um componente é usar as ações, que são instruções que você pode enfileirar e combinar isso com programação assíncrona em c#.  Isso permite que o comportamento de seu componente possa ser de nível muito alto e torna mais simples de entender o que está acontecendo.

Como alternativa, você pode controlar exatamente o que acontece ao seu componente Atualizando propriedades do componente em cada quadro (discutido na seção de comportamento baseado em quadro).

### <a name="actions"></a>Ações

Você pode adicionar comportamento para nós muito facilmente usando as ações.  Ações podem alterar várias propriedades de nó e executá-los em um período de tempo ou repeti-los várias vezes com uma curva de animação determinada.

Por exemplo, considere um nó de "nuvem" na sua cena, você pode fazer o fade-lo assim:

```csharp
await cloud.RunActionsAsync (new FadeOut (duration: 3))
```

Ações são objetos imutáveis, que permite que você reutilize a ação para impulsionar diferentes objetos.

Uma linguagem comum é criar uma ação que executa a operação inversa:

```csharp
var gotoExit = new MoveTo (duration: 3, position: exitLocation);
var return = gotoExit.Reverse ();
```

O exemplo a seguir seria esmaecer o objeto para você por um período de três segundos.  Você também pode executar uma ação após o outro, por exemplo, você poderia primeiro mover a nuvem e, em seguida, ocultá-lo:

```csharp
await cloud.RunActionsAsync (
    new MoveBy  (duration: 1.5f, position: new Vector3(0, 0, 15),
    new FadeOut (duration: 3));
```

Se você quiser que as duas ações ocorram ao mesmo tempo, pode usar a ação paralela e fornecer as ações que você deseja feitas em paralelo:

```csharp
  await cloud.RunActionsAsync (
    new Parallel (
      new MoveBy  (duration: 3, position: new Vector3(0, 0, 15),
      new FadeOut (duration: 3)));
```

No exemplo acima, a nuvem moverá e fade out ao mesmo tempo.

Você observará que eles estão usando a linguagem c# await, que permite que você pense linearmente sobre o comportamento que você deseja atingir.

### <a name="basic-actions"></a>Ações básicas

Estas são as ações com suporte no UrhoSharp:

* Movimentação de nós: [ `MoveTo` ](https://developer.xamarin.com/api/type/Urho.Actions.MoveTo), [ `MoveBy` ](https://developer.xamarin.com/api/type/Urho.Actions.MoveBy), [ `Place` ](https://developer.xamarin.com/api/type/Urho.Actions.Place), [ `BezierTo` ](https://developer.xamarin.com/api/type/Urho.Actions.BezierTo), [ `BezierBy` ](https://developer.xamarin.com/api/type/Urho.Actions.BezierBy) , [`JumpTo`](https://developer.xamarin.com/api/type/Urho.Actions.JumpTo), [`JumpBy`](https://developer.xamarin.com/api/type/Urho.Actions.JumpBy)
* Girando nós: [ `RotateTo` ](https://developer.xamarin.com/api/type/Urho.Actions.RotateTo), [`RotateBy`](https://developer.xamarin.com/api/type/Urho.Actions.RotateBy)
* O dimensionamento de nós: [ `ScaleTo` ](https://developer.xamarin.com/api/type/Urho.Actions.ScaleTo), [`ScaleBy`](https://developer.xamarin.com/api/type/Urho.Actions.ScaleBy)
* Nós de esmaecimento: [ `FadeIn` ](https://developer.xamarin.com/api/type/Urho.Actions.FadeIn), [ `FadeTo` ](https://developer.xamarin.com/api/type/Urho.Actions.FadeTo), [ `FadeOut` ](https://developer.xamarin.com/api/type/Urho.Actions.FadeOut), [ `Hide` ](https://developer.xamarin.com/api/type/Urho.Actions.Hide), [`Blink`](https://developer.xamarin.com/api/type/Urho.Actions.Blink)
* Tonalidade: [ `TintTo` ](https://developer.xamarin.com/api/type/Urho.Actions.TintTo), [`TintBy`](https://developer.xamarin.com/api/type/Urho.Actions.TintBy)
* Momentos: [ `Hide` ](https://developer.xamarin.com/api/type/Urho.Actions.Hide), [ `Show` ](https://developer.xamarin.com/api/type/Urho.Actions.Show), [ `Place` ](https://developer.xamarin.com/api/type/Urho.Actions.Place), [ `RemoveSelf` ](https://developer.xamarin.com/api/type/Urho.Actions.RemoveSelf), [`ToggleVisibility`](https://developer.xamarin.com/api/type/Urho.Actions.ToggleVisibility)
* Looping: [`Repeat`](https://developer.xamarin.com/api/type/Urho.Actions.Repeat), [`RepeatForever`](https://developer.xamarin.com/api/type/Urho.Actions.RepeatForever), [`ReverseTime`](https://developer.xamarin.com/api/type/Urho.Actions.ReverseTime)

Outros recursos avançados incluem a combinação da [ `Spawn` ](https://developer.xamarin.com/api/type/Urho.Actions.Spawn) e [ `Sequence` ](https://developer.xamarin.com/api/type/Urho.Actions.Sequence) ações.

### <a name="easing---controlling-the-speed-of-your-actions"></a>Atenuação - controlar a velocidade de suas ações

A atenuação é uma maneira que direciona a maneira que a animação será Desdobrar, e ele pode fazer muito mais agradável suas animações.  Por padrão suas ações terá um comportamento linear, por exemplo uma [ `MoveTo` ](https://developer.xamarin.com/api/type/Urho.Actions.MoveTo) ação teria uma movimentação muito robótica.  Você pode dispor suas ações em uma ação de suavização para alterar o comportamento, por exemplo, o que seria lentamente iniciar a movimentação, acelerar e lentamente chegar ao fim ([`EasyInOut`](https://developer.xamarin.com/api/type/Urho.Actions.EasyInOut)).

Você pode fazer isso, encapsulando uma ação existente em uma ação de easing, por exemplo:

```csharp
await cloud.RunActionAsync (
   new EaseInOut (
     new MoveTo (duration: 3, position: new Vector (0,0,15)), rate:1))
```

Há muitos modos de atenuação, o gráfico a seguir mostra os vários tipos de easing e seu comportamento no valor do objeto que estão controlando no período de tempo, do início ao fim:

![Modos de easing](using-images/easing.png "este gráfico mostra os vários tipos de easing e seu comportamento no valor do objeto que estão controlando no período de tempo")

### <a name="using-actions-and-async-code"></a>Usando as ações e o código assíncrono

No seu [ `Component` ](https://developer.xamarin.com/api/type/Urho.Component/) subclasse, você deverá apresentar um método assíncrono que prepara o comportamento do seu componente e a funcionalidade de discos para ele.
Em seguida, você faria para invocar esse método usando a linguagem c# `await` palavra-chave de outra parte do seu programa qualquer um dos seus `Application.Start` método ou em resposta a um ponto de usuário ou a história em seu aplicativo.

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

No `Launch` método acima três ações são iniciados: o robô entra em cena, esta ação irá alterar o local do nó em um período de 0,6 segundo.  Como essa é uma opção de async, isso ocorrerá simultaneamente como a próxima instrução que é a chamada para `MoveRandomly`.  Esse método irá alterar a posição do robô em paralelo em um local aleatório.  Isso é obtido executando duas ações compostas, o movimento a um novo local e voltando ao original posicionar e repita a operação, desde que o robô permaneça ativo.  E, para tornar as coisas mais interessantes, o robô será manter acertar simultaneamente.  A solução será iniciado somente cada 0,1 segundos.

### <a name="frame-based-behavior-programming"></a>Programação de comportamento baseado em quadro

Se você quiser controlar o comportamento do seu componente em uma base quadro a quadro, em vez de usar as ações, o que você faria é substituir a [ `OnUpdate` ](https://developer.xamarin.com/api/member/Urho.Component.OnUpdate) método de sua [ `Component` ](https://developer.xamarin.com/api/type/Urho.Component) subclasse.  Esse método é invocado uma vez por quadro e é invocado apenas se você definir a propriedade ReceiveSceneUpdates como true.

A seguir mostra como criar um `Rotator` componente, que é anexado a um nó, o que faz com que o nó girar:

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
            RotationSpeed.X * timeStep,
            RotationSpeed.Y * timeStep,
            RotationSpeed.Z * timeStep),
            TransformSpace.Local);
    }
}
```

E isso é como você faria anexar esse componente a um nó:

```csharp
Node boxNode = new Node();
var rotator = new Rotator() { RotationSpeed = rotationSpeed };
boxNode.AddComponent (rotator);
```

### <a name="combining-styles"></a>Combinando estilos

Você pode usar o modelo de async/ação com base para a programação de grande parte do comportamento que é excelente para disparar e esquecer o estilo de programação, mas você pode também ajustar seu comportamento de componente para executar também algum código de atualização em cada quadro.

Por exemplo, na demonstração SamplyGame isso é usado na `Enemy` classe codifica as ações de usos de comportamento básico, mas ela também garante que os componentes apontarão para o usuário, definindo a direção do nó com `Node.LookAt`:

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

## <a name="loading-and-saving-scenes"></a>Carregar e salvar as cenas

Cenas podem ser carregadas e salvos em formato XML; Consulte as funções [ `LoadXml` ](https://developer.xamarin.com/api/member/Urho.Scene.LoadXml) e [ `SaveXML()` ](https://developer.xamarin.com/api/member/Urho.Scene.SaveXml). Quando uma cena é carregada, todo o conteúdo existente nele (nós filho e componentes) é removido primeiro. Nós e componentes que são marcados como temporários com o `Temporary` propriedade não serão salvas. O serializador manipula todos os componentes internos e propriedades, mas não é inteligente o suficiente para lidar com as propriedades personalizadas e os campos definidos em suas subclasses de componente. No entanto, ele fornece dois métodos virtuais para isso:

* [`OnSerialize`](https://developer.xamarin.com/api/member/Urho.Component.OnSerialize) onde você pode registrá-lo estados personalizados para a serialização

* [`OnDeserialized`](https://developer.xamarin.com/api/member/Urho.Component.OnDeserialize) onde você pode obter seus estados personalizados salvos.

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

Apenas carregar ou salvar o plano inteiro não é flexível o suficiente para jogos em que novos objetos precisam ser criadas dinamicamente. Por outro lado, a criação de objetos complexos e definindo suas propriedades no código também será entediantes. Por esse motivo, também é possível salvar um nó da cena que incluirá seus nós filho, componentes e atributos. Eles convenientemente posteriormente podem ser carregados como um grupo.  Um objeto salvo é conhecido como um pré-fabricado. Há três maneiras de fazer isso:

- No código chamando [ `Node.SaveXml` ](https://developer.xamarin.com/api/member/Urho.Node.SaveXml) no nó
- No editor, selecionando o nó na janela de hierarquia e escolhendo "Salvar nó como" no menu "Arquivo".
- Usando o comando "nó" no `AssetImporter`, que salvará a hierarquia de nó da cena e quaisquer modelos contidos no ativo de entrada (por exemplo. um arquivo Collada)

Para criar uma instância do nó salvo em uma cena, chame [ `InstantiateXml()` ](https://developer.xamarin.com/api/member/Urho.Scene.InstantiateXml). O nó será criado como um filho da cena, mas pode ser reassociado livremente depois disso. Posição e rotação para colocar o nó precisam ser especificados. O código a seguir demonstra como criar uma instância de um pré-fabricado `Ninja.xm` a uma cena com a posição desejada e a rotação:

```csharp
var prefabPath = Path.Combine (FileSystem.ProgramDir,"Data/Objects/Ninja.xml");
using (var file = new File(Context, prefabPath, FileMode.Read))
{
    scene.InstantiateXml(file, desiredPos, desiredRotation,
        CreateMode.Replicated);
}
```

## <a name="events"></a>Eventos

UrhoObjects gerar uma série de eventos, elas são exibidas como c# eventos sobre as várias classes que geram eles.  Além do c#-modelo baseado em evento, também é possível usar um o `SubscribeToXXX` métodos que permitem que você assine e mantenha um token de assinatura que você pode usar posteriormente para cancelar a assinatura.  A diferença é que o primeiro permitirá muitos chamadores inscrever-se, enquanto o segundo é só permite um, mas permite o estilo lambda mais agradável abordagem a ser usada e ainda assim, permite a fácil remoção da assinatura.  Eles são mutuamente exclusivos.

Quando você assina um evento, você deve fornecer um método que usa um argumento com os argumentos de evento apropriado.

Por exemplo, isso é como você se inscreve em um botão do mouse para baixo de evento:

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

Com o estilo de lambda:

```csharp
public void override Start ()
{
    UI.MouseButtonDown += args => {
        Console.WriteLine ("button pressed");
    };
}
```

Às vezes, você desejará parar de receber notificações para o evento, nesses casos, salvar o valor de retorno da chamada para `SubscribeTo` método e invocar o método de cancelamento da assinatura:

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

O parâmetro recebido pelo manipulador de eventos é uma classe de argumentos de evento com rigidez de tipos que serão específicos para cada evento e contém a carga do evento.

## <a name="responding-to-user-input"></a>Responder à entrada do usuário

Você pode se inscrever para vários eventos, como pressionamentos de teclas para baixo por assinatura do evento e responder à entrada que está sendo entregue:

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

Mas, em muitos cenários, você deseja que seus manipuladores de atualização de cena para verificar o status atual das chaves quando eles estão sendo atualizados e atualize seu código adequadamente.  Por exemplo, a seguir pode ser usado para atualizar o local de câmera com base no teclado de entrada:

```csharp
protected override void OnUpdate(float timeStep)
{
    Input input = Input;
    // Movement speed as world units per second
    const float moveSpeed = 4.0f;
    // Read WASD keys and move the camera scene node to the
    // corresponding direction if they are pressed
    if (input.GetKeyDown(Key.W))
        CameraNode.Translate(Vector3.UnitY * moveSpeed * timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.S))
        CameraNode.Translate(new Vector3(0.0f, -1.0f, 0.0f) * moveSpeed * timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.A))
        CameraNode.Translate(new Vector3(-1.0f, 0.0f, 0.0f) * moveSpeed * timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.D))
        CameraNode.Translate(Vector3.UnitX * moveSpeed * timeStep, TransformSpace.Local);
}
```

## <a name="resources-assets"></a>Recursos (ativos)

Os recursos incluem a maioria das coisas UrhoSharp que são carregados do armazenamento em massa durante a inicialização ou o tempo de execução:

- [`Animation`](https://developer.xamarin.com/api/type/Urho.Animation/) – usado para animações estrutural
- [`Image`](https://developer.xamarin.com/api/type/Urho.Resources.Image) -representa imagens armazenadas em uma variedade de formatos de gráfico
- [`Model`](https://developer.xamarin.com/api/type/Urho.Model/) -Modelos 3D
- [`Material`](https://developer.xamarin.com/api/type/Urho.Material) – usados para renderizar os modelos de materiais.
- [`ParticleEffect`](https://developer.xamarin.com/api/type/Urho.ParticleEffect)- [Descreve](http://urho3d.github.io/documentation/1.4/_particles.html) como um emissor de partícula funciona, consulte "[partículas](#particles)" abaixo.
- [`Shader`](https://developer.xamarin.com/api/type/Urho.Shader) -sombreadores personalizados
- [`Sound`](https://developer.xamarin.com/api/type/Urho.Audio.Sound) -sons para reprodução, consulte "[som](#sound)" abaixo.
- [`Technique`](https://developer.xamarin.com/api/type/Urho.Technique/) -técnicas de renderização de material
- [`Texture2D`](https://developer.xamarin.com/api/type/Urho.Urho2D.Texture2D/) -Textura 2D
- [`Texture3D`](https://developer.xamarin.com/api/type/Urho.Texture3D/) -Textura 3D
- [`TextureCube`](https://developer.xamarin.com/api/type/Urho.TextureCube/) -O cubo de textura
- `XmlFile`

Eles são gerenciados e carregados pelo [ `ResourceCache` ](https://developer.xamarin.com/api/type/Urho.Resources.ResourceCache/) subsistema (disponível como [ `Application.ResourceCache` ](https://developer.xamarin.com/api/property/Urho.Application.ResourceCache/)).

Os próprios recursos são identificados por seus caminhos de arquivo, em relação a diretórios do recurso registrado ou arquivos de pacote. Por padrão, o mecanismo registra os diretórios de recursos `Data` e `CoreData`, ou os pacotes `Data.pak` e `CoreData.pak` se eles existirem.

Se houver falha ao carregar um recurso, um erro será registrado e uma referência nula é retornada.

O exemplo a seguir mostra uma maneira comum de busca de um recurso do cache do recurso.  Nesse caso, uma textura para um elemento de interface do usuário, isso usa a `ResourceCache` propriedade a partir de `Application` classe.

```csharp
healthBar.SetTexture(ResourceCache.GetTexture2D("Textures/HealthBarBorder.png"));
```

Recursos também podem criados manualmente e armazenados no cache do recurso, como se tivessem sido carregados do disco.

Orçamentos de memória podem ser definidos por tipo de recurso: se recursos consomem mais memória do que o permitido, os recursos mais antigos serão removidos do cache se não estiver em uso mais. Por padrão os orçamentos de memória estiverem definidos como ilimitados.

### <a name="bringing-3d-models-and-images"></a>Colocar imagens e modelos 3D

Urho3D tenta usar formatos de arquivo existentes sempre que possível e definir os formatos de arquivo personalizados somente quando absolutamente necessário, como para modelos (*.mdl) e para animações (*. ani). Para esses tipos de ativos, Urho fornece um conversor - [AssetImporter](http://urho3d.github.io/documentation/1.4/_tools.html) que podem consumir muitos formatos 3D populares como fbx dae, 3ds e obj, etc.

Também há um suplemento útil para Blender [ https://github.com/reattiva/Urho3D-Blender ](https://github.com/reattiva/Urho3D-Blender) que pode exportar seus ativos Blender no formato adequado para Urho3D.

### <a name="background-loading-of-resources"></a>Carregamento em segundo plano de recursos

Normalmente, quando a solicitação de recursos usando um dos `ResourceCache`do `Get` método, eles são carregados imediatamente no thread principal, que pode levar vários milissegundos para todas as etapas necessárias (carregar arquivo do disco, analisar dados, carregar no GPU, se necessário ) e, portanto, pode resultar em quedas de taxa de quadros.

Se você souber de antemão quais recursos você precisa, você pode solicitar a ser carregado em um thread em segundo plano, chamando `BackgroundLoadResource()`. Você pode assinar o evento de recurso carregado do plano de fundo usando o `SubscribeToResourceBackgroundLoaded` método. Ela informa se o carregamento, na verdade, foi um sucesso ou falha. Dependendo do recurso, somente uma parte do processo de carregamento pode ser movido para um thread em segundo plano, por exemplo a etapa de carregamento Concluindo a GPU sempre precisa acontecer no thread principal. Observe que, se você chamar um dos métodos para um recurso que está na fila de carregamento em segundo plano de carregamento de recursos, o thread principal ficará parada até que o carregamento for concluído.

A cena assíncrona funcionalidade de carregamento `LoadAsync()` e `LoadAsyncXML()` tem a opção de carregamento em segundo plano recursos primeiro antes de prosseguir para carregar o conteúdo da cena. Ele também pode ser usado para carregar apenas os recursos sem modificar a cena, especificando o `LoadMode.ResourcesOnly`. Assim, para preparar um arquivo cena ou objeto pré-fabricado para instanciação rápida.

Finalmente o tempo máximo (em milissegundos) gasto cada quadro em terminar plano de fundo recursos carregados podem ser configurados definindo a `FinishBackgroundResourcesMs` propriedade no `ResourceCache`.

<a name="sound"/>

## <a name="sound"></a>Som

Som é uma parte importante do jogo e a estrutura de UrhoSharp fornece uma maneira de reprodução de sons em seu jogo.  Reproduzir sons, anexando um [`SoundSource`](https://developer.xamarin.com/api/type/Urho.Audio.SoundSource/)
componente para um [ `Node` ](https://developer.xamarin.com/api/type/Urho.Node) e, em seguida, reproduzir um arquivo nomeado de recursos.

Isso é como isso é feito:

```csharp
var explosionNode = Scene.CreateChild();
var sound = explosionNode.CreateComponent<SoundSource>();
soundSource.Play(Application.ResourceCache.GetSound("Sounds/ding.wav"));
soundSource.Gain = 0.5f;
soundSource.AutoRemove = true;
```

<a name="particles"/>

## <a name="particles"></a>Partículas

Partículas fornecem uma maneira simple de adicionar alguns efeitos simples e econômica para seu aplicativo.  Você pode consumir partículas armazenadas no formato PEX, usando ferramentas como o [ http://onebyonedesign.com/flash/particleeditor/ ](http://onebyonedesign.com/flash/particleeditor/).

Partículas são componentes que podem ser adicionados a um nó.  Você precisa chamar o nó `CreateComponent<ParticleEmitter2D>` método para criar a partícula e, em seguida, configurar a partícula definindo a propriedade efeito para um efeito de 2D que é carregado do cache do recurso.

Por exemplo, você pode chamar esse método em seu componente para mostrar alguns partículas que são renderizadas como um detalhamento quando atinge:

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

O código acima criará um nó de detalhamento que é anexado ao componente atual, dentro deste nó de detalhamento criamos um emissor de partícula 2D e configurá-lo definindo a propriedade efeito.  Podemos executar duas ações, que dimensiona o nó para ser menor e um que deixa nesse tamanho por 0,5 segundos.  Em seguida, removemos o detalhamento, que também removerá o efeito de partículas na tela.

A partícula acima renderizado da seguinte forma ao usar uma textura de esfera:

![Partículas com uma textura de esfera](using-images/image-1.png "renderiza a partícula acima como este, ao usar uma textura de esfera")

E isso é o que aparece se você usar uma textura em blocos:

![Partículas com uma textura de caixa](using-images/image-2.png "e esse é o que parece se usando uma textura em blocos")

## <a name="multithreading-support"></a>Suporte de multithreading

UrhoSharp é uma biblioteca de thread única.  Isso significa que você não deve tentar invocar métodos em UrhoSharp de um thread em segundo plano, ou corromper o estado do aplicativo de risco e provavelmente a falha de seu aplicativo.

Se você quiser executar um código em segundo plano e, em seguida, atualize Urho componentes da interface do usuário principal, você pode usar o [`Application.InvokeOnMain(Action)`](https://developer.xamarin.com/api/member/Urho.Application.InvokeOnMain)
método.  Além disso, você pode usar c# await e o .NET APIs para garantir que o código é executado no thread adequado de tarefas.

## <a name="urhoeditor"></a>UrhoEditor

Você pode baixar o Editor Urho para sua plataforma do [Urho site](http://urho3d.github.io/), vá para Downloads e escolher a versão mais recente.

## <a name="copyrights"></a>Direitos autorais

Esta documentação contém o conteúdo original do Xamarin Inc, mas desenha amplamente na documentação do código-fonte aberto para o projeto Urho3D e contém as capturas de tela do projeto Cocos2D.
