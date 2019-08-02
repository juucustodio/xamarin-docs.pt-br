---
title: Trabalhando com storyboards no Xamarin. Mac
description: Este documento descreve como trabalhar com storyboards no Xamarin. Mac, examinando como carregá-los do código, o ciclo de vida do controlador de exibição, a cadeia de respondente, continuações, controladores de janela, reconhecedores de gesto e muito mais.
ms.prod: xamarin
ms.assetid: DF4DF7C2-DDD7-4A32-B375-5C5446301EC5
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 06d3127bbe7766a70efc570bd455a8d570548731
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68653779"
---
# <a name="working-with-storyboards-in-xamarinmac"></a>Trabalhando com storyboards no Xamarin. Mac

Um storyboard define toda a interface do usuário para um determinado aplicativo dividido em uma visão geral funcional de seus controladores de exibição. No Interface Builder do Xcode, cada um desses controladores reside em sua própria cena.

[![](indepth-images/intro01.png "Um storyboard no Interface Builder do Xcode")](indepth-images/intro01.png#lightbox)

O storyboard é um arquivo de recurso (com as extensões `.storyboard`do) que é incluído no pacote do aplicativo Xamarin. Mac quando ele é compilado e enviado. Para definir o storyboard inicial para seu aplicativo, edite `Info.plist` o arquivo e selecione a **interface principal** na caixa suspensa: 

[![](indepth-images/sb01.png "O editor info. plist")](indepth-images/sb01.png#lightbox)

<a name="Loading-from-Code" />

## <a name="loading-from-code"></a>Carregando do código

Pode haver ocasiões em que você precise carregar um storyboard específico do código e criar um controlador de exibição manualmente. Você pode usar o seguinte código para executar esta ação:

```csharp
// Get new window
var storyboard = NSStoryboard.FromName ("Main", null);
var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

// Display
controller.ShowWindow(this);
```

O `FromName` carrega o arquivo de storyboard com o nome fornecido que foi incluído no pacote do aplicativo. O `InstantiateControllerWithIdentifier` cria uma instância do controlador de exibição com a identidade fornecida. Você define a identidade no Interface Builder do Xcode ao criar a interface do usuário:

[![](indepth-images/sb02.png "Definindo a ID do storyboard")](indepth-images/sb02.png#lightbox)

Opcionalmente, você pode usar o `InstantiateInitialController` método para carregar o controlador de exibição ao qual foi atribuído o controlador inicial no interface Builder:

[![](indepth-images/sb03.png "Configurando o controlador inicial")](indepth-images/sb03.png#lightbox)

Ele é marcado pelo **ponto de entrada do storyboard** e pela seta aberta finalizada acima.

<a name="View-Controllers" />

## <a name="view-controllers"></a>Exibir controladores

Os controladores de exibição definem as relações entre uma determinada exibição de informações em um aplicativo Mac e o modelo de dados que fornece essas informações. Cada cena de nível superior no storyboard representa um controlador de exibição no código do aplicativo Xamarin. Mac.

<a name="The-View-Controller-Lifecycle" />

### <a name="the-view-controller-lifecycle"></a>O ciclo de vida do controlador de exibição

Vários novos métodos foram adicionados à `NSViewController` classe para dar suporte a storyboards no MacOS. O mais importante é que os métodos a seguir usam para responder ao ciclo de vida da exibição que está sendo controlada pelo controlador de exibição fornecido:

- `ViewDidLoad`-Esse método é chamado quando a exibição é carregada a partir do arquivo de storyboard.
- `ViewWillAppear`-Esse método é chamado logo antes da exibição ser exibida na tela.
- `ViewDidAppear`-Esse método é chamado diretamente depois que a exibição é exibida na tela.
- `ViewWillDisappear`-Esse método é chamado logo antes da exibição ser removida da tela.
- `ViewDidDisappear`-Esse método é chamado diretamente após a exibição ser removida da tela.
- `UpdateViewConstraints`-Esse método é chamado quando as restrições que definem uma exibição de posição e tamanho de layout automático precisam ser atualizadas.
- `ViewWillLayout`-Esse método é chamado logo antes de as subexibições dessa exibição serem colocadas na tela.
- `ViewDidLayout`-Esse método é chamado diretamente depois que as subexibições de exibição são dispostas na tela.

<a name="The-Responder-Chain" />

### <a name="the-responder-chain"></a>A cadeia de respondentes

Além disso `NSViewControllers` , agora fazem parte da cadeia de _respondentes_da janela:

[![](indepth-images/vc01.png "A cadeia de respondentes")](indepth-images/vc01.png#lightbox)

E, assim, eles são conectados para receber e responder a eventos, como seleções de item de menu recortar, copiar e colar. Essa conexão do controlador de exibição automática ocorre apenas em aplicativos em execução no macOS Sierra (10,12) e superior.

<a name="Containment" />

### <a name="containment"></a>Confinamento

Em storyboards, os controladores de exibição (como o controlador de exibição de divisão e o controlador de exibição deguia) agora podem implementar o confinamento, de modo que eles podem "conter" outros controladores de subexibição:

[![](indepth-images/vc02.png "Um exemplo de contenção do controlador de exibição")](indepth-images/vc02.png#lightbox)

Os controladores de exibição filho contêm métodos e propriedades para vinculá-los de volta ao controlador de exibição pai e para trabalhar com a exibição e a remoção de exibições da tela.

Todos os controladores de exibição de contêiner incorporados ao macOS têm um layout específico que a Apple sugere que você siga se criar seus próprios controladores de exibição de contêiner personalizados:

[![](indepth-images/vc03.png "O layout do controlador de exibição")](indepth-images/vc03.png#lightbox)

O controlador de exibição de coleção contém uma matriz de itens de exibição de coleção, cada um contendo um ou mais controladores de exibição que contêm suas próprias exibições.

<a name="Segues" />

## <a name="segues"></a>Continuações

Continuações fornecem as relações entre todos os bastidores que definem a interface do usuário do seu aplicativo. Se você estiver familiarizado com o trabalho em storyboards no iOS, saberá que continuações para iOS geralmente definem transições entre exibições de tela inteira. Isso difere do macOS, quando continuações geralmente define "[contenção](#Containment)", em que uma cena é o filho de uma cena pai.

No macOS, a maioria dos aplicativos tendem a agrupar suas exibições na mesma janela usando elementos de interface do usuário, como exibições divididas e guias. Ao contrário do iOS, em que as exibições precisam ser transferidas e desativadas na tela, devido ao espaço de exibição físico limitado.

<a name="Presentation-Segues" />

### <a name="presentation-segues"></a>Continuações de apresentação

Considerando o as tendências da macOS em relação à contenção, há situações em que as _continuações de apresentação_ são usadas, como janelas restritas, exibições de planilha e popovers. o macOS fornece os seguintes tipos internos de transição:

- **Show** – exibe o destino do transição como uma janela não modal. Por exemplo, use esse tipo de transição para apresentar outra instância de uma janela de documento em seu aplicativo.
- **Modal** -apresenta o destino do transição como uma janela modal. Por exemplo, use esse tipo de transição para apresentar a janela de preferências para seu aplicativo.
- **Planilha** -apresenta o destino do transição como uma planilha anexada à janela pai. Por exemplo, use esse tipo de transição para apresentar uma folha localizar e substituir.
- **Popover** -apresenta o destino do transição como em uma janela popover. Por exemplo, use este tipo de transição para apresentar opções quando um elemento de interface do usuário for clicado pelo usuário.
- **Personalizado** -apresenta o destino do transição usando um tipo transição personalizado definido pelo desenvolvedor. Consulte a seção [criando continuações personalizados](#Creating-Custom-Segues) abaixo para obter mais detalhes.

Ao usar a apresentação continuações, você pode substituir `PrepareForSegue` o método do controlador de exibição pai para que a apresentação seja inicializada e as variáveis e forneça todos os dados para o controlador de exibição que está sendo apresentado.

<a name="Triggered-Segues" />

### <a name="triggered-segues"></a>Continuações disparado

Continuações disparado permite que você especifique o chamado continuações (por meio de sua propriedade de **identificador** no interface Builder) e os tenha disparado por eventos, como o `PerformSegue` usuário clicando em um botão ou chamando o método no código:

```csharp
// Display the Scene defined by the given Segue ID
PerformSegue("MyNamedSegue", this);
``` 

A ID transição é definida dentro do Interface Builder do Xcode quando você está fazendo o layout da interface do usuário do aplicativo:

[![](indepth-images/sg02.png "Inserindo um nome de transição")](indepth-images/sg02.png#lightbox)

No controlador de exibição que está agindo como a origem do transição, você deve substituir o `PrepareForSegue` método e fazer qualquer inicialização necessária antes que o transição seja executado e o controlador de exibição especificado seja exibido:

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

Opcionalmente, você pode substituir o `ShouldPerformSegue` método e controlar se o transição é realmente executado por meio C# de código. Para os controladores de exibição apresentados manualmente, `DismissController` chame seu método para removê-los da exibição quando não forem mais necessários.

<a name="Creating-Custom-Segues" />

### <a name="creating-custom-segues"></a>Criando continuações personalizado

Pode haver ocasiões em que seu aplicativo requer um tipo de transição não fornecido pelo continuações de build-in definido no macOS. Se esse for o caso, você poderá criar um transição personalizado que pode ser atribuído no Interface Builder do Xcode ao dispor a interface do usuário do seu aplicativo.

Por exemplo, para criar um novo tipo transição que substitua o controlador de exibição atual dentro de uma janela (em vez de abrir a cena de destino em uma nova janela), podemos usar o seguinte código:

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

- Estamos usando o `Register` atributo para expor essa classe para Objective-C/MacOS.
- Estamos substituindo o `Perform` método para realmente executar a ação de nosso transição personalizado.
- Estamos substituindo o controlador da `ContentViewController` janela por um definido pelo destino (destino) do transição.
- Estamos removendo o controlador de exibição original para liberar memória usando o `RemoveFromParentViewController` método.

Para usar esse novo tipo de transição no Interface Builder do Xcode, precisamos compilar o aplicativo primeiro e, em seguida, alternar para o Xcode e adicionar um novo transição entre dois bastidores. Defina o **estilo** como **personalizado** e a **classe transição** como `ReplaceViewSegue` (o nome da nossa classe transição personalizada):

[![](indepth-images/sg01.png "Definindo a classe transição")](indepth-images/sg01.png#lightbox)

<a name="Triggered-Segues" />

## <a name="window-controllers"></a>Controladores de janela

Os controladores de janela contêm e controlam os diferentes tipos de janela que seu aplicativo macOS pode criar. Para storyboards, eles têm os seguintes recursos:

1. Eles devem fornecer um controlador de exibição de conteúdo. Esse será o mesmo controlador de exibição de conteúdo que a janela filho tem.
2. A `Storyboard` Propriedade conterá o storyboard do qual o controlador de janela foi carregado `null` , caso contrário, se não for carregado a partir de um Storyboard.
3. Você pode chamar o `DismissController` método para fechar a janela especificada e removê-la da exibição.

Assim como os controladores de exibição, os `PerformSegue`controladores `PrepareForSegue` de janela `ShouldPerformSegue` implementam o e os métodos e podem ser usados como a origem de uma operação transição.

O controlador de janela é responsável pelos seguintes recursos de um aplicativo macOS:

- Eles gerenciam uma janela específica.
- Eles gerenciam a barra de título e a barra de ferramentas da janela (se disponível).
- Eles gerenciam o controlador de exibição de conteúdo para exibir o conteúdo da janela.

<a name="Gesture-Recognizers" />

## <a name="gesture-recognizers"></a>Reconhecedores de gesto

Os reconhecedores de gestos para macOS são quase idênticos aos seus correspondentes no iOS e permitem que o desenvolvedor adicione gestos com facilidade (como clicar em um botão do mouse) a elementos na interface do usuário do seu aplicativo.

No entanto, onde os gestos no iOS são determinados pelo design do aplicativo (por exemplo, tocando na tela com dois dedos), a maioria dos gestos no macOS é determinada pelo hardware.

Usando reconhecedores de gestos, você pode reduzir bastante a quantidade de código necessária para adicionar interações personalizadas a um item na interface do usuário. Como eles podem determinar automaticamente entre cliques duplos e únicos, clique e arraste eventos, etc.

Em vez de substituir `MouseDown` o evento em seu controlador de exibição, você deve usar um reconhecedor de gesto para manipular o evento de entrada do usuário ao trabalhar com storyboards.

Os reconhecedores de gesto a seguir estão disponíveis no macOS:

- `NSClickGestureRecognizer`-Registrar eventos de mouse para baixo e para cima.
- `NSPanGestureRecognizer`-Registra os eventos de arrastar e soltar do botão do mouse.
- `NSPressGestureRecognizer`-Registra o pressionamento de um botão do mouse para um determinado período de tempo.
- `NSMagnificationGestureRecognizer`-Registra um evento de ampliação de hardware trackpad.
- `NSRotationGestureRecognizer`-Registra um evento de rotação de hardware trackpad.

<a name="Using-Storyboard-References" />

## <a name="using-storyboard-references"></a>Usando referências de storyboard

Uma referência de storyboard permite que você use um design de storyboard grande e complexo e divida-o em storyboards menores que são referenciados do original, removendo a complexidade e tornando os storyboards individuais resultantes mais fáceis de criar e manter.

Além disso, uma referência de storyboard pode fornecer uma _âncora_ para outra cena dentro do mesmo storyboard ou de uma cena específica em um diferente.

<a name="Referencing-an-External-Storyboard" />

### <a name="referencing-an-external-storyboard"></a>Fazendo referência a um storyboard externo

Para adicionar uma referência a um storyboard externo, faça o seguinte:

1. Na **Gerenciador de soluções**, clique com o botão direito do mouse no nome do projeto e selecione **Adicionar** > **novo arquivo...** Storyboard do **Mac** > .  >  Insira um **nome** para o novo storyboard e clique no botão **novo** : 

    [![](indepth-images/ref01.png "Adicionando um novo storyboard")](indepth-images/ref01.png#lightbox)
2. No **Gerenciador de soluções**, clique duas vezes no novo nome do storyboard para abri-lo para edição no interface Builder do Xcode.
3. Projete o layout das cenas do novo storyboard como faria normalmente e salve suas alterações: 

    [![](indepth-images/ref02.png "Criando a interface")](indepth-images/ref02.png#lightbox)
4. Alterne para o storyboard ao qual você vai adicionar a referência no Interface Builder.
5. Arraste uma **referência de storyboard** da **biblioteca de objetos** para a design Surface: 

    [![](indepth-images/ref03.png "Selecionando uma referência de storyboard na biblioteca")](indepth-images/ref03.png#lightbox)
6. No **Inspetor de atributo**, selecione o nome do **storyboard** que você criou acima: 

    [![](indepth-images/ref04.png "Configurando a referência")](indepth-images/ref04.png#lightbox)
7. Controle-clique em um widget de interface do usuário (como um botão) em uma cena existente e crie um novo transição para a **referência do storyboard** que você acabou de criar.  No menu pop-up, selecione **Mostrar** para concluir o transição: 

    [![](indepth-images/ref06.png "Configurando o tipo transição")](indepth-images/ref06.png#lightbox) 
8. Salve as alterações no storyboard.
9. Retorne para Visual Studio para Mac para sincronizar suas alterações.

Quando o aplicativo é executado e o usuário clica no elemento de interface do usuário do qual você criou o transição, o controlador de janela inicial do storyboard externo especificado na referência do storyboard será exibido.

<a name="Referencing-a-Specific-Scene-in-an-External-Storyboard" />

### <a name="referencing-a-specific-scene-in-an-external-storyboard"></a>Referenciando uma cena específica em um storyboard externo

Para adicionar uma referência a uma cena específica um storyboard externo (e não o controlador de janela inicial), faça o seguinte:

1. Na **Gerenciador de soluções**, clique duas vezes no storyboard externo para abri-lo para edição no interface Builder do Xcode.
2. Adicione uma nova cena e projete seu layout como faria normalmente: 

    [![](indepth-images/ref07.png "Criando o layout no Xcode")](indepth-images/ref07.png#lightbox)
3. No **Inspetor de identidade**, insira uma **ID de storyboard** para o controlador de janela da nova cena: 

    [![](indepth-images/ref08.png "Definindo a ID do storyboard")](indepth-images/ref08.png#lightbox)
4. Abra o storyboard ao qual você vai adicionar a referência em Interface Builder.
5. Arraste uma **referência de storyboard** da **biblioteca de objetos** para a design Surface: 

    [![](indepth-images/ref03.png "Selecionando uma referência de storyboard na biblioteca")](indepth-images/ref03.png#lightbox)
6. No **Inspetor de identidade**, selecione o nome do **storyboard** e a **ID de referência** (ID do storyboard) da cena que você criou acima: 

    [![](indepth-images/ref09.png "Definindo a ID de referência")](indepth-images/ref09.png#lightbox)
7. Controle-clique em um widget de interface do usuário (como um botão) em uma cena existente e crie um novo transição para a **referência do storyboard** que você acabou de criar. No menu pop-up, selecione **Mostrar** para concluir o transição: 

    [![](indepth-images/ref06.png "Configurando o tipo transição")](indepth-images/ref06.png#lightbox) 
8. Salve as alterações no storyboard.
9. Retorne para Visual Studio para Mac para sincronizar suas alterações.

Quando o aplicativo é executado e o usuário clica no elemento de interface do usuário do qual você criou o transição, a cena com a **ID de storyboard** fornecida do storyboard externo especificado na referência do storyboard será exibida.

<a name="Referencing-a-Specific-Scene-in-the-Same-Storyboard" />

### <a name="referencing-a-specific-scene-in-the-same-storyboard"></a>Fazendo referência a uma cena específica no mesmo storyboard

Para adicionar uma referência a uma cena específica do mesmo storyboard, faça o seguinte:

1. Na **Gerenciador de soluções**, clique duas vezes no storyboard para abri-lo para edição.
2. Adicione uma nova cena e projete seu layout como faria normalmente: 

    [![](indepth-images/ref11.png "Editando o storyboard no Xcode")](indepth-images/ref11.png#lightbox)
3. No **Inspetor de identidade**, insira uma **ID de storyboard** para o controlador de janela da nova cena: 

    [![](indepth-images/ref12.png "Definindo a ID do storyboard")](indepth-images/ref12.png#lightbox)
4. Arraste uma **referência de storyboard** da **caixa de ferramentas** para a design Surface: 

    [![](indepth-images/ref03.png "Selecionando uma referência de storyboard na biblioteca")](indepth-images/ref03.png#lightbox)
5. No **Inspetor de atributo**, selecione **ID de referência** (ID do storyboard) da cena que você criou acima: 

    [![](indepth-images/ref13.png "Definindo a ID de referência")](indepth-images/ref13.png#lightbox)
6. Controle-clique em um widget de interface do usuário (como um botão) em uma cena existente e crie um novo transição para a **referência do storyboard** que você acabou de criar. No menu pop-up, selecione **Mostrar** para concluir o transição: 

    [![](indepth-images/ref06.png "Selecionando o tipo transição")](indepth-images/ref06.png#lightbox) 
7. Salve as alterações no storyboard.
8. Retorne para Visual Studio para Mac para sincronizar suas alterações.

Quando o aplicativo é executado e o usuário clica no elemento da interface do usuário do qual você criou o transição, a cena com a **ID do storyboard** fornecida no mesmo storyboard especificado na referência do storyboard será exibida.

<a name="Complex-Storyboard-Example" />

## <a name="complex-storyboard-example"></a>Exemplo de storyboard complexo

Para obter um exemplo complexo de como trabalhar com storyboards em um aplicativo Xamarin. Mac, consulte o [aplicativo de exemplo SourceWriter](https://docs.microsoft.com/samples/xamarin/mac-samples/sourcewriter). SourceWriter é um editor de código-fonte simples que dá suporte para a conclusão de código e realce de sintaxe simples.

O código do SourceWriter foi totalmente comentado e, quando disponível, foram fornecidos links de tecnologias-chave ou métodos para informações relevantes na Documentação de Guias do Xamarin.Mac.

## <a name="related-links"></a>Links relacionados

- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabalhando com o Windows](~/mac/user-interface/window.md)
- [Diretrizes de interface humana do OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introdução ao Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
