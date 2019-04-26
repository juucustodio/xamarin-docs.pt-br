---
title: Trabalhar com Storyboards no xamarin. Mac
description: Este documento descreve como trabalhar com storyboards no xamarin. Mac, examinando como carregá-los do código, o ciclo de vida do controlador de exibição, a cadeia de Respondente, segues, controladores de janela, reconhecedores de gestos e muito mais.
ms.prod: xamarin
ms.assetid: DF4DF7C2-DDD7-4A32-B375-5C5446301EC5
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 61d598f90747cf47b613012328f77b4bd8953a41
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61032849"
---
# <a name="working-with-storyboards-in-xamarinmac"></a>Trabalhar com Storyboards no xamarin. Mac

Um storyboard define todos da interface do usuário para um determinado aplicativo dividido em uma visão funcional de seus controladores de exibição. Interface Builder do Xcode, cada um desses controladores reside em sua própria cena.

[![](indepth-images/intro01.png "Um storyboard no Interface Builder do Xcode")](indepth-images/intro01.png#lightbox)

O storyboard é um arquivo de recurso (com as extensões de `.storyboard`) que é incluído no pacote do aplicativo xamarin. Mac quando ele é compilado e enviado. Para definir o Storyboard de partida para seu aplicativo, editá-lo do `Info.plist` do arquivo e selecione o **Interface principal** na lista suspensa: 

[![](indepth-images/sb01.png "O editor de info. plist")](indepth-images/sb01.png#lightbox)

<a name="Loading-from-Code" />

## <a name="loading-from-code"></a>Carregamento do código

Pode haver ocasiões em que você precisa carregar um Storyboard específico de código e criar um controlador de exibição manualmente. Você pode usar o código a seguir para executar esta ação:

```csharp
// Get new window
var storyboard = NSStoryboard.FromName ("Main", null);
var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

// Display
controller.ShowWindow(this);
```

O `FromName` carrega o arquivo de Storyboard com o nome fornecido que foi incluído no pacote do aplicativo. O `InstantiateControllerWithIdentifier` cria uma instância do controlador de exibição com a identidade fornecida. Você pode definir a identidade no Interface Builder do Xcode ao projetar a interface do usuário:

[![](indepth-images/sb02.png "Definir a ID do Storyboard")](indepth-images/sb02.png#lightbox)

Opcionalmente, você pode usar o `InstantiateInitialController` método para carregar o controlador de exibição que foi atribuído o controlador inicial no Interface Builder:

[![](indepth-images/sb03.png "Configuração do controlador de inicial")](indepth-images/sb03.png#lightbox)

Ele está marcado, o **ponto de entrada do Storyboard** e a seta de open terminou acima.

<a name="View-Controllers" />

## <a name="view-controllers"></a>Controladores de exibição

Controladores de exibição definem as relações entre um determinado modo de exibição de informações dentro de um aplicativo do Mac e o modelo de dados que fornece essas informações. Cada cena de nível superior no Storyboard representa um controlador de exibição no código do aplicativo xamarin. Mac.

<a name="The-View-Controller-Lifecycle" />

### <a name="the-view-controller-lifecycle"></a>O ciclo de vida do controlador de exibição

Vários novos métodos foram adicionados para o `NSViewController` classe para dar suporte a Storyboards no macOS. Mais importante é que são que os métodos de acompanhamento usam para responder ao ciclo de vida da exibição que está sendo controlado pelo controlador de exibição fornecido:

- `ViewDidLoad` -Esse método é chamado quando o modo de exibição é carregado do arquivo de Storyboard.
- `ViewWillAppear` -Esse método é chamado pouco antes que a exibição é exibida na tela.
- `ViewDidAppear` -Este método é chamado diretamente após a exibição foi exibida na tela.
- `ViewWillDisappear` -Esse método é chamado pouco antes que o modo de exibição é removido da tela.
- `ViewDidDisappear` -Este método é chamado diretamente após a exibição foi removida da tela.
- `UpdateViewConstraints` -Esse método é chamado quando as restrições que definem um modo de exibição auto layout posição e tamanho precisarão ser atualizados.
- `ViewWillLayout` -Este método é chamado pouco antes das subexibições dessa exibição são dispostas na tela.
- `ViewDidLayout` -Este método é chamado diretamente após as subexibições do modo de exibição são dispostas na tela.

<a name="The-Responder-Chain" />

### <a name="the-responder-chain"></a>A cadeia do Respondente

Além disso, `NSViewControllers` agora fazem parte da janela _Respondente cadeia_:

[![](indepth-images/vc01.png "A cadeia do Respondente")](indepth-images/vc01.png#lightbox)

E como tal, eles são com fio-up para receber e responder a eventos, como Recortar, copiar e colar seleções de item de menu. Esse controlador de exibição wire-up automático ocorre apenas em aplicativos em execução no macOS Sierra (10.12) e maior.

<a name="Containment" />

### <a name="containment"></a>Confinamento

No Storyboards, controladores de exibição (como o controlador de exibição de divisão e o controlador de exibição da guia) agora pode implementar _confinamento_, de modo que eles podem "contém" outro controladores de exibição sub:

[![](indepth-images/vc02.png "Um exemplo de confinamento do controlador de exibição")](indepth-images/vc02.png#lightbox)

Controladores de exibição filho contêm métodos e propriedades, associá-los de volta para seu controlador de exibição de pai e para trabalhar com exibindo e removendo exibições da tela.

Todos os controladores de exibição de contêiner criada no macOS tem um layout específico que a Apple sugere que você siga se criar seus próprios controladores de exibição personalizado do contêiner:

[![](indepth-images/vc03.png "O layout do controlador de exibição")](indepth-images/vc03.png#lightbox)

O controlador de exibição de coleção contém uma matriz de itens de exibição de coleção, cada qual contendo um ou mais controladores de exibição que contêm suas próprias exibições.

<a name="Segues" />

## <a name="segues"></a>Segues

Segues fornecem as relações entre todas as cenas que definem a interface de usuário do seu aplicativo. Se você estiver familiarizado com trabalhando em Storyboards no iOS, você sabe que Segues para iOS normalmente define transições entre modos de exibição de tela inteira. Isso é diferente do macOS, quando normalmente define transições "[confinamento](#Containment)", onde uma cena é o filho de uma cena do pai.

No macOS, a maioria dos aplicativos tendem a agrupar seus modos de exibição juntos na mesma janela usando elementos de interface do usuário, como modos de exibição de divisão e guias. Ao contrário do iOS, onde as exibições precisam ser transferidas e desativar a tela, devido a física limitada exiba espaço.

<a name="Presentation-Segues" />

### <a name="presentation-segues"></a>Segues de apresentação

Considerando as tendências do macOS em direção a contenção, há situações em que _apresentação Segues_ forem usados, como Windows Modal, modos de exibição da folha e Popovers. tipos de macOS fornece segue o interno a seguir:

- **Mostrar** -exibe o destino do que o Segue como uma janela não modal. Por exemplo, use esse tipo de Segue para apresentar outra instância de uma janela de documento no seu aplicativo.
- **Modal** -apresenta o destino do que o Segue como uma janela modal. Por exemplo, use esse tipo de Segue para apresentar a janela de preferências para seu aplicativo.
- **Folha** -apresenta o destino do que o Segue como uma planilha anexado a janela pai. Por exemplo, use esse tipo de segue para apresentar um localizar e substituir folha.
- **Pop-over** -apresenta o destino do que o Segue como em uma janela pop-over. Por exemplo, use este tipo de Segue para apresentar opções quando um elemento de interface do usuário é clicado pelo usuário.
- **Personalizado** -apresenta o destino do que o Segue usando um tipo de Segue personalizado definido pelo desenvolvedor. Consulte a [criação personalizada Segues](#Creating-Custom-Segues) seção abaixo para obter mais detalhes.

Ao usar Segues de apresentação, você pode substituir o `PrepareForSegue` método do controlador de exibição de pai para a apresentação para inicializar e variáveis e forneça todos os dados para o controlador de exibição que está sendo apresentado.

<a name="Triggered-Segues" />

### <a name="triggered-segues"></a>Disparado Segues

Transições disparadas permitem que você especifique transições nomeada (por meio de suas **identificador** propriedade no construtor de Interface) e fazer com que eles disparado por eventos, como o usuário clicar em um botão ou chamando o `PerformSegue` método no código:

```csharp
// Display the Scene defined by the given Segue ID
PerformSegue("MyNamedSegue", this);
``` 

A ID Segue é definida dentro Interface Builder do Xcode quando são dispostas a interface do usuário do aplicativo:

[![](indepth-images/sg02.png "Inserindo um Segue o nome")](indepth-images/sg02.png#lightbox)

No controlador de exibição que está atuando como a origem do que o Segue, você deve substituir o `PrepareForSegue` método e fazer qualquer inicialização necessária antes que o Segue é executado e o controlador de exibição especificado é exibido:

```csharp
public override void PrepareForSegue (NSStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    // Take action based on Segue ID
    switch (segue.Identifier) {
    case "MyNamedSegue":
        // Prepare for the segue to happen
        ...
        break;
    }
}
```

Opcionalmente, você pode substituir a `ShouldPerformSegue` método e o controle ou não o Segue, na verdade, é executado por meio do C# código. Para os controladores de exibição manualmente apresentada, chame seus `DismissController` método para removê-los da exibição quando eles não são mais necessários.

<a name="Creating-Custom-Segues" />

### <a name="creating-custom-segues"></a>Criação personalizada Segues

Pode haver vezes quando seu aplicativo requer um tipo de Segue não fornecido pelas transições de compilação definida no macOS. Se esse for o caso, você pode criar um Segue personalizados que podem ser atribuídos no Interface Builder do Xcode ao definir o layout da interface do usuário do seu aplicativo.

Por exemplo, para criar um novo tipo de Segue que substitui o controlador de exibição atual dentro de uma janela (em vez de abrir o destino cena em uma nova janela), podemos usar o código a seguir:

```csharp
using System;
using AppKit;
using Foundation;

namespace OnCardMac
{
    [Register("ReplaceViewSeque")]
    public class ReplaceViewSeque : NSStoryboardSegue
    {
        #region Constructors
        public ReplaceViewSeque() {

        }

        public ReplaceViewSeque (string identifier, NSObject sourceController, NSObject destinationController) : base(identifier,sourceController,destinationController) {

        }

        public ReplaceViewSeque (IntPtr handle) : base(handle) {
        }

        public ReplaceViewSeque (NSObjectFlag x) : base(x) {
        }
        #endregion

        #region Override Methods
        public override void Perform ()
        {
            // Cast the source and destination controllers
            var source = SourceController as NSViewController;
            var destination = DestinationController as NSViewController;

            // Swap the controllers
            source.View.Window.ContentViewController = destination;

            // Release memory
            source.RemoveFromParentViewController ();
        }
        #endregion

    }
        
}
```

Algumas coisas a serem observadas aqui:

- Estamos usando o `Register` atributo para expor essa classe para Objective-C/macOS.
- Estamos substituindo o `Perform` método para realmente executar a ação de nosso Segue personalizado.
- Estamos substituindo a janela `ContentViewController` controlador com aquele definido pelo destino (destino) de que o Segue.
- Estamos removendo o controlador de exibição original para liberar memória usando o `RemoveFromParentViewController` método.

Para usar esse novo tipo de Segue no Interface Builder do Xcode, é necessário compilar o aplicativo em primeiro lugar, em seguida, mude para o Xcode e adicione um novo Segue entre duas cenas. Defina a **estilo** para **personalizado** e o **classe Segue** para `ReplaceViewSegue` (o nome da nossa classe Segue personalizada):

[![](indepth-images/sg01.png "Definindo a classe Segue")](indepth-images/sg01.png#lightbox)

<a name="Triggered-Segues" />

## <a name="window-controllers"></a>Controladores de janela

Janela controladores contenham e controlam os diferentes tipos de janela que seu aplicativo macOS pode criar. Storyboards, eles têm os seguintes recursos:

1. Eles devem fornecer um controlador de exibição de conteúdo. Esse será o mesmo controlador de exibição de conteúdo que tenha o janela filho.
2. O `Storyboard` propriedade conterá o Storyboard que o controlador da janela foi carregado, caso contrário `null` se não foi carregado de um Storyboard.
3. Você pode chamar o `DismissController` método para fechar a janela de determinado e removê-lo do modo de exibição.

Assim como os controladores de exibição, controladores de janela implementam o `PerformSegue`, `PrepareForSegue` e o `ShouldPerformSegue` métodos e pode ser usado como a origem de uma operação Segue.

Controlador da janela é responsável para os seguintes recursos de um aplicativo do macOS:

- Eles gerenciam uma janela específica.
- Eles gerenciam a barra de título e a barra de ferramentas da janela (se disponível).
- Eles gerenciam o controlador de exibição de conteúdo para exibir o conteúdo da janela.

<a name="Gesture-Recognizers" />

## <a name="gesture-recognizers"></a>Reconhecedores de gestos

Reconhecedores de gestos para macOS são quase idênticas às suas contrapartes no iOS e permitir que o desenvolvedor adicione facilmente gestos (por exemplo, clicando em um botão do mouse) aos elementos na interface de usuário do seu aplicativo.

No entanto, em que gestos no iOS são determinados pelo design do aplicativo (por exemplo, tocar a tela com dois dedos), mais gestos no macOS são determinados pelo hardware.

Usando os reconhecedores de gestos, você pode reduzir significativamente a quantidade de código necessário para adicionar interações personalizadas a um item na interface do usuário. Como pode determinar automaticamente entre os cliques do double e únicos, clique e arraste a eventos, etc.

Em vez de substituir o `MouseDown` evento em seu controlador de exibição, você deve usar um reconhecedor de gestos para manipular o evento de entrada do usuário ao trabalhar com Storyboards.

Os identificadores de gesto a seguir estão disponíveis no macOS:

- `NSClickGestureRecognizer` -Registre o mouse para baixo e eventos.
- `NSPanGestureRecognizer` -Registros botão do mouse para baixo, arraste e solte os eventos.
- `NSPressGestureRecognizer` -Registra pressionando um botão do mouse em uma determinada quantidade de eventos de tempo.
- `NSMagnificationGestureRecognizer` -Registra um evento de ampliação do hardware trackpad.
- `NSRotationGestureRecognizer` -Registra um evento de rotação do hardware trackpad.

<a name="Using-Storyboard-References" />

## <a name="using-storyboard-references"></a>Usando referências de Storyboard

Uma referência de Storyboard permite que você usar um design de Storyboard grande e complexo e dividi-la em menores Storyboards que obterem referenciados a partir do original, portanto, remover a complexidade e tornando os Storyboards individuais resultantes mais fácil de desenvolver e manter.

Além disso, uma referência de Storyboard pode fornecer um _âncora_ à cena outro dentro do mesmo Storyboard ou uma cena específica em um diferente.

<a name="Referencing-an-External-Storyboard" />

### <a name="referencing-an-external-storyboard"></a>Fazendo referência a um Storyboard externo

Para adicionar uma referência a um Storyboard externo, faça o seguinte:

1. No **Gerenciador de soluções**, clique com botão direito no nome do projeto e selecione **Add** > **novo arquivo...**   >  **Mac** > **Storyboard**. Insira um **nome** para o novo Storyboard e clique o **New** botão: 

    [![](indepth-images/ref01.png "Adicionando um novo Storyboard")](indepth-images/ref01.png#lightbox)
2. No **Gerenciador de soluções**, clique duas vezes o novo nome do Storyboard para abri-lo para edição no Interface Builder do Xcode.
2. Como você normalmente faria e salva suas alterações de design o layout dos bastidores do novo Storyboard: 

    [![](indepth-images/ref02.png "Projetar a interface")](indepth-images/ref02.png#lightbox)
3. Alterne para o Storyboard que você pretende adicionar a referência no Interface Builder.
4. Arraste uma **referência do Storyboard** da **biblioteca de objeto** na superfície de Design: 

    [![](indepth-images/ref03.png "Selecionando uma referência de Storyboard na biblioteca")](indepth-images/ref03.png#lightbox)
5. No **Inspetor de atributo**, selecione o nome da **Storyboard** criado acima: 

    [![](indepth-images/ref04.png "Configurando a referência")](indepth-images/ref04.png#lightbox)
6. CTRL + clique em um Widget de interface do usuário (como um botão) em uma cena existente e criar um novo Segue para a **referência de Storyboard** que você acabou de criar.  No menu pop-up, selecione **Mostrar** para concluir o Segue: 

    [![](indepth-images/ref06.png "Definindo o tipo de Segue")](indepth-images/ref06.png#lightbox) 
8. Salve suas alterações para o Storyboard.
9. Retorne ao Visual Studio para Mac sincronizar suas alterações.

Quando o aplicativo é executado e o usuário clica no elemento de interface do usuário que você criou o Segue do, o controlador da janela inicial do Storyboard externo especificado na referência do Storyboard serão exibida.

<a name="Referencing-a-Specific-Scene-in-an-External-Storyboard" />

### <a name="referencing-a-specific-scene-in-an-external-storyboard"></a>Fazendo referência a uma cena específica em um Storyboard externo

Para adicionar uma referência a uma cena específica um Storyboard externo (e não o controlador da janela inicial), faça o seguinte:

1. No **Gerenciador de soluções**, clique duas vezes o Storyboard externo para abri-lo para edição no Interface Builder do Xcode.
2. Adicione uma nova cena e projetar seu layout, como faria normalmente: 

    [![](indepth-images/ref07.png "Projetar o layout no Xcode")](indepth-images/ref07.png#lightbox)
3. No **Inspetor de identidade**, insira um **ID do Storyboard** para o controlador da janela da nova cena: 

    [![](indepth-images/ref08.png "Definir a ID do Storyboard")](indepth-images/ref08.png#lightbox)
3. Abra o Storyboard que você pretende adicionar a referência no Interface Builder.
4. Arraste uma **referência do Storyboard** da **biblioteca de objeto** na superfície de Design: 

    [![](indepth-images/ref03.png "Selecionando uma Storyboard de referência da biblioteca")](indepth-images/ref03.png#lightbox)
5. No **Inspetor de identidade**, selecione o nome da **Storyboard** e o **ID de referência** (ID do Storyboard) da cena que você criou acima: 

    [![](indepth-images/ref09.png "Definindo a ID de referência")](indepth-images/ref09.png#lightbox)
6. CTRL + clique em um Widget de interface do usuário (como um botão) em uma cena existente e criar um novo Segue para a **referência de Storyboard** que você acabou de criar. No menu pop-up, selecione **Mostrar** para concluir o Segue: 

    [![](indepth-images/ref06.png "Definindo o tipo de Segue")](indepth-images/ref06.png#lightbox) 
8. Salve suas alterações para o Storyboard.
9. Retorne ao Visual Studio para Mac sincronizar suas alterações.

Quando o aplicativo é executado e o usuário clica no elemento de interface do usuário que você criou o Segue da cena com o determinado **ID do Storyboard** do Storyboard externo especificado na referência do Storyboard será exibida.

<a name="Referencing-a-Specific-Scene-in-the-Same-Storyboard" />

### <a name="referencing-a-specific-scene-in-the-same-storyboard"></a>Fazendo referência a uma cena específica no mesmo Storyboard

Para adicionar uma referência a uma cena específica o Storyboard mesmo, faça o seguinte:

1. No **Gerenciador de soluções**, clique duas vezes o Storyboard para abri-lo para edição.
2. Adicione uma nova cena e projetar seu layout, como faria normalmente: 

    [![](indepth-images/ref11.png "Editando o storyboard no Xcode")](indepth-images/ref11.png#lightbox)
3. No **Inspetor de identidade**, insira um **ID do Storyboard** para o controlador da janela da nova cena: 

    [![](indepth-images/ref12.png "Definir a ID do Storyboard")](indepth-images/ref12.png#lightbox)
3. Arraste uma **referência do Storyboard** da **caixa de ferramentas** na superfície de Design: 

    [![](indepth-images/ref03.png "Selecionando uma Storyboard de referência da biblioteca")](indepth-images/ref03.png#lightbox)
5. Na **Inspetor de atributo**, selecione **ID de referência** (ID do Storyboard) da cena que você criou acima: 

    [![](indepth-images/ref13.png "Definindo a ID de referência")](indepth-images/ref13.png#lightbox)
6. CTRL + clique em um Widget de interface do usuário (como um botão) em uma cena existente e criar um novo Segue para a **referência de Storyboard** que você acabou de criar. No menu pop-up, selecione **Mostrar** para concluir o Segue: 

    [![](indepth-images/ref06.png "Selecionar o tipo de Segue")](indepth-images/ref06.png#lightbox) 
8. Salve suas alterações para o Storyboard.
9. Retorne ao Visual Studio para Mac sincronizar suas alterações.

Quando o aplicativo é executado e o usuário clica no elemento de interface do usuário que você criou o Segue da cena com o determinado **ID do Storyboard** no mesmo Storyboard especificado na referência do Storyboard será exibida.

<a name="Complex-Storyboard-Example" />

## <a name="complex-storyboard-example"></a>Exemplo de Storyboard complexos

Para obter um exemplo complexo de como trabalhar com Storyboards em um aplicativo xamarin. Mac, consulte a [aplicativo de exemplo SourceWriter](https://developer.xamarin.com/samples/mac/SourceWriter/). SourceWriter é um editor de código-fonte simples que dá suporte para a conclusão de código e realce de sintaxe simples.

O código do SourceWriter foi totalmente comentado e, quando disponível, foram fornecidos links de tecnologias-chave ou métodos para informações relevantes na Documentação de Guias do Xamarin.Mac.

## <a name="related-links"></a>Links relacionados

- [MacStoryboard (amostra)](https://developer.xamarin.com/samples/mac/MacStoryboard/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabalhando com o Windows](~/mac/user-interface/window.md)
- [Diretrizes de interface humana do OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introdução ao Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
