---
title: Trabalhando com Storyboards
description: Compilando macOS interfaces de usuário com storyboards usando Xcode.
ms.prod: xamarin
ms.assetid: DF4DF7C2-DDD7-4A32-B375-5C5446301EC5
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 3b72affd9b101b0a139301fec9f2bed343310507
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="storyboards"></a>Storyboards

Um storyboard define todos da interface do usuário para um determinado aplicativo dividido em uma visão geral das funções de seus controladores de exibição. No construtor de Interface do Xcode, cada um desses controladores reside em seu próprio cena.

[![](indepth-images/intro01.png "Um storyboard no construtor de Interface do Xcode")](indepth-images/intro01.png#lightbox)

O storyboard é um arquivo de recurso (com as extensões de `.storyboard`) que obtém incluídos no pacote do aplicativo Xamarin.Mac quando ela é compilada e enviada. Para definir o Storyboard inicial para seu aplicativo, editá-lo do `Info.plist` de arquivo e selecione o **Interface principal** na lista suspensa: 

[![](indepth-images/sb01.png "O editor de info. plist")](indepth-images/sb01.png#lightbox)

<a name="Loading-from-Code" />

## <a name="loading-from-code"></a>Carregamento de código

Pode haver ocasiões em que você precisa carregar um Storyboard específico de código e criar um controlador de exibição manualmente. Você pode usar o código a seguir para executar esta ação:

```csharp
// Get new window
var storyboard = NSStoryboard.FromName ("Main", null);
var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

// Display
controller.ShowWindow(this);
```

O `FromName` carrega o arquivo de Storyboard com o nome especificado que foi incluído no pacote do aplicativo. O `InstantiateControllerWithIdentifier` cria uma instância do controlador de exibição com a identidade fornecida. Você pode definir a identidade no construtor de Interface do Xcode ao projetar a interface do usuário:

[![](indepth-images/sb02.png "Definir a ID do Storyboard")](indepth-images/sb02.png#lightbox)

Opcionalmente, você pode usar o `InstantiateInitialController` método para carregar o controlador de exibição que foi atribuído o controlador inicial no construtor de Interface:

[![](indepth-images/sb03.png "Configuração do controlador de inicial")](indepth-images/sb03.png#lightbox)

Ele é marcado pelo **ponto de entrada de Storyboard** e a seta de abrir terminou acima.

<a name="View-Controllers" />

## <a name="view-controllers"></a>Exibir controladores

Exibir controladores definem as relações entre um determinado modo de exibição de informações dentro de um aplicativo do Mac e o modelo de dados que fornece essas informações. Cada cena de nível superior no Storyboard representa um controlador de exibição de código do aplicativo Xamarin.Mac.

<a name="The-View-Controller-Lifecycle" />

### <a name="the-view-controller-lifecycle"></a>O ciclo de vida do controlador de exibição

Vários métodos novos foram adicionados para o `NSViewController` classe para dar suporte a Storyboards macOS. Mais importante são que os métodos de acompanhamento usam para responder ao ciclo de vida do modo de exibição que está sendo controlado pelo controlador de exibição fornecido:

- `ViewDidLoad` -Este método é chamado quando o modo de exibição é carregado do arquivo de Storyboard.
- `ViewWillAppear` -Este método é chamado antes do modo de exibição é exibido na tela.
- `ViewDidAppear` -Este método é chamado diretamente após a exibição foi mostrada na tela.
- `ViewWillDisappear` -Este método é chamado antes do modo de exibição é removido da tela.
- `ViewDidDisappear` -Este método é chamado diretamente após a exibição foi removida da tela.
- `UpdateViewConstraints` -Este método é chamado quando as restrições que definem uma exibição automática layout posição e tamanho precisam ser atualizadas.
- `ViewWillLayout` -Este método é chamado antes das sub-visualizações dessa exibição são apresentadas na tela.
- `ViewDidLayout` -Este método é chamado diretamente depois sub-visualizações do modo de exibição são apresentadas na tela.

<a name="The-Responder-Chain" />

### <a name="the-responder-chain"></a>A cadeia do Respondente

Além disso, `NSViewControllers` agora fazem parte da janela de _Respondente cadeia_:

[![](indepth-images/vc01.png "A cadeia do Respondente")](indepth-images/vc01.png#lightbox)

E como tal, são o com fio para receber e responder a eventos como Recortar, copiar e colar seleções de item de menu. Esse controlador de exibição durante a transmissão-up automática ocorre apenas em aplicativos em execução no macOS Serra (10.12) e maior.

<a name="Containment" />

### <a name="containment"></a>Confinamento

Storyboards, controladores de exibição (como o controlador de exibição de divisão e a guia View Controller) podem agora implementar _contenção_, de modo que eles podem "contém" outro sub controladores de exibição:

[![](indepth-images/vc02.png "Um exemplo de confinamento de controlador de exibição")](indepth-images/vc02.png#lightbox)

Exibir controladores de filho contêm métodos e propriedades para vinculá-los de volta para seu controlador de exibição pai e para trabalhar com a exibir e remover os modos de exibição da tela.

Todos os controladores de exibição de contêiner incorporados macOS têm um layout específico de Apple sugerem que você siga se criar seus próprios controladores de exibição de contêiner personalizado:

[![](indepth-images/vc03.png "O layout do controlador de exibição")](indepth-images/vc03.png#lightbox)

O controlador de exibição de coleção contém uma matriz de itens de exibição de coleção, que contêm um ou mais controladores de exibição que contêm seus próprios modos de exibição.

<a name="Segues" />

## <a name="segues"></a>Segues

Segues fornecem as relações entre todos nos bastidores que definem a interface de usuário do aplicativo. Se você estiver familiarizado com trabalhando em Storyboards no iOS, você sabe que Segues de iOS geralmente definem as transições entre modos de exibição de tela inteira. Isso é diferente do macOS, quando Segues geralmente define "[contenção](#Containment)", onde uma cena é o filho de um pai cena.

Em macOS, a maioria dos aplicativos tendem a suas exibições juntos dentro da mesma janela usando elementos de interface do usuário, como modos de exibição de divisão e guias de grupo. Ao contrário do iOS, onde modos de exibição precisam ser transferido e desativar a tela, devido a físico limitado exibe espaço.

<a name="Presentation-Segues" />

### <a name="presentation-segues"></a>Segues de apresentação

Considerando as tendências do macOS em direção à contenção, há situações em que _Segues apresentação_ forem usados, como janelas modais, modos de exibição de planilha e Popovers. tipos de macOS fornece atender internas a seguir:

- **Mostrar** -exibe o destino do que o Segue como uma janela não restrita. Por exemplo, use esse tipo de Segue para apresentar a outra instância de uma janela de documento em seu aplicativo.
- **Modal** -apresenta o destino de Segue como uma janela modal. Por exemplo, use esse tipo de Segue para apresentar a janela de preferências para seu aplicativo.
- **Folha de** -apresenta o destino do Segue como uma planilha anexado a janela pai. Por exemplo, use esse tipo de atender para apresentar um localizar e substituir folha.
- **Popover** -apresenta o destino do Segue como uma janela popover. Por exemplo, use este tipo de Segue para apresentar opções quando um elemento de interface do usuário é clicado pelo usuário.
- **Personalizado** -apresenta o destino do Segue usando um tipo de atender personalizado definido pelo desenvolvedor. Consulte o [criação personalizada Segues](#Creating-Custom-Segues) seção abaixo para obter mais detalhes.

Ao usar Segues de apresentação, você pode substituir o `PrepareForSegue` método do controlador de exibição pai para a apresentação de inicialização e variáveis e fornecer os dados para o controlador de exibição que está sendo exibida.

<a name="Triggered-Segues" />

### <a name="triggered-segues"></a>Disparado Segues

Disparado Segues permitem que você especifique Segues nomeado (por meio de seus **identificador** propriedade no construtor de Interface) e disparada por eventos, como o usuário clicar em um botão ou chamando o `PerformSegue` método no código:

```csharp
// Display the Scene defined by the given Segue ID
PerformSegue("MyNamedSegue", this);
``` 

A ID de atender é definida dentro Interface Builder do Xcode quando são dispor de interface de usuário do aplicativo:

[![](indepth-images/sg02.png "Inserir um nome de atender")](indepth-images/sg02.png#lightbox)

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

Opcionalmente, você pode substituir o `ShouldPerfromSegue` método e o controle ou não o Segue é realmente executada por meio de código c#. Para controladores de exibição manualmente apresentada, chame seus `DismissController` método para removê-los da exibição quando eles não são mais necessários.

<a name="Creating-Custom-Segues" />

### <a name="creating-custom-segues"></a>Criar personalizado Segues

Pode haver vezes quando seu aplicativo requer um tipo Segue não fornecido pelo Segues de compilação definido no macOS. Se esse for o caso, você pode criar um atender personalizados que podem ser atribuídos no construtor de Interface do Xcode ao definir o layout da interface do usuário do seu aplicativo.

Por exemplo, para criar um novo tipo Segue que substitui o controlador de exibição atual em uma janela (em vez de abrir o destino de cena em uma nova janela), podemos usar o código a seguir:

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

Algumas coisas a observar aqui:

- Estamos usando o `Register` atributo para expor essa classe para objetivo-C/macOS.
- Estamos está substituindo o `Perform` método realmente executar a ação de nosso Segue personalizado.
- Estamos substituindo a janela `ContentViewController` controlador com definida pelo destino (destino) de que o Segue.
- Estamos removendo o controlador de exibição original para liberar memória usando o `RemoveFromParentViewController` método.

Para usar esse novo tipo Segue no construtor de Interface do Xcode, é necessário compilar o aplicativo pela primeira vez, em seguida, alternar para Xcode e adicionar um novo Segue entre duas cenas. Definir o **estilo** para **personalizado** e **classe atender** para `ReplaceViewSegue` (o nome da nossa classe Segue personalizada):

[![](indepth-images/sg01.png "Definindo a classe Segue")](indepth-images/sg01.png#lightbox)

<a name="Triggered-Segues" />

## <a name="window-controllers"></a>Controladores de janela

Janela controladores contenham e controlam os diferentes tipos de janela que pode criar seu aplicativo macOS. Para Storyboards, eles têm os seguintes recursos:

1. Eles devem fornecer um controlador de exibição de conteúdo. Esse será o mesmo controlador de exibição de conteúdo que tenha o janela filho.
2. O `Storyboard` propriedade conterá o Storyboard que o controlador de janela foi carregado, caso contrário `null` se não foi carregado de um Storyboard.
3. Você pode chamar o `DismissController` método para fechar a janela determinada e removê-lo do modo de exibição.

Como exibir controladores, controladores de janela é implementam o `PerformSegue`, `PrepareForSegue` e `ShouldPerfromSegue` métodos e pode ser usado como a origem de uma operação Segue.

Janela controlador é responsável para os seguintes recursos de um aplicativo macOS:

- Eles gerenciam uma janela específica.
- Eles gerenciam a barra de título e a barra de ferramentas da janela (se disponível).
- Eles gerenciam o controlador de exibição de conteúdo para exibir o conteúdo da janela.

<a name="Gesture-Recognizers" />

## <a name="gesture-recognizers"></a>Reconhecedores de gestos

Reconhecedores de gestos para macOS são quase idênticas às contrapartes no iOS e permitir que o desenvolvedor adicione facilmente gestos (como clicar em um botão do mouse) para elementos de interface de usuário do aplicativo.

No entanto, onde gestos no iOS são determinados pelo design do aplicativo (por exemplo, tocar a tela com dois dedos), mais gestos em macOS são determinados pelo hardware.

Usando o gesto de identificadores, você pode reduzir significativamente a quantidade de código necessário para adicionar interações personalizadas a um item na interface de usuário. Como pode determinar automaticamente entre cliques duplos e único, clique e arraste eventos, etc.

Em vez de substituir o `MouseDown` evento em seu controlador de exibição, você deve usar um reconhecedor de gestos para manipular o evento de entrada do usuário ao trabalhar com Storyboards.

Os identificadores de gesto a seguir estão disponíveis no macOS:

- `NSClickGestureRecognizer` -Registre o mouse para baixo e eventos.
- `NSPanGestureRecognizer` -Registra botão do mouse, arraste e solte os eventos.
- `NSPressGestureRecognizer` -Registra segurando o botão do mouse para uma determinada quantidade de evento de tempo.
- `NSMagnificationGestureRecognizer` -Registra um evento de ampliação do hardware trackpad.
- `NSRotationGestureRecognizer` -Registra um evento de rotação do hardware trackpad.

<a name="Using-Storyboard-References" />

## <a name="using-storyboard-references"></a>Usando referências de Storyboard

Uma referência de Storyboard permite usar um design de Storyboard grande e complexo e dividi-lo em Storyboards menores do que obtenham referenciados do original, assim removendo remover a complexidade e fazer resultante individuais Storyboards mais fácil para design e Manter.

Além disso, uma referência de Storyboard pode fornecer um _âncora_ à cena outra no mesmo Storyboard ou uma cena específica em um diferente.

<a name="Referencing-an-External-Storyboard" />

### <a name="referencing-an-external-storyboard"></a>Referenciando um Storyboard externo

Para adicionar uma referência a um Storyboard externo, faça o seguinte:

1. No **Solution Explorer**, com o botão direito no nome do projeto e selecione **adicionar** > **novo arquivo...**   >  **Mac** > **Storyboard**. Insira um **nome** para o novo Storyboard e clique o **novo** botão: 

    [![](indepth-images/ref01.png "Adicionando um novo Storyboard")](indepth-images/ref01.png#lightbox)
2. No **Solution Explorer**, clique duas vezes o novo nome do Storyboard para abri-lo para edição no construtor de Interface do Xcode.
2. Crie o layout de cenas do Storyboard novo como você faria normalmente e salva as alterações: 

    [![](indepth-images/ref02.png "Criar a interface")](indepth-images/ref02.png#lightbox)
3. Alterne para o Storyboard que você pretende adicionar a referência no construtor de Interface.
4. Arraste um **referência de Storyboard** do **objeto biblioteca** na superfície de Design: 

    [![](indepth-images/ref03.png "Selecionar uma referência de Storyboard na biblioteca")](indepth-images/ref03.png#lightbox)
5. No **Inspetor de atributo**, selecione o nome do **Storyboard** criado anteriormente: 

    [![](indepth-images/ref04.png "Configurando a referência")](indepth-images/ref04.png#lightbox)
6. Controle, clique em um Widget de interface do usuário (como um botão) em uma cena existente e criar um novo Segue para a **Storyboard referência** que você acabou de criar.  No menu pop-up selecionar **Mostrar** para concluir o Segue: 

    [![](indepth-images/ref06.png "Definição do tipo Segue")](indepth-images/ref06.png#lightbox) 
8. Salve suas alterações para o Storyboard.
9. Retorne ao Visual Studio para Mac sincronizar suas alterações.

Quando o aplicativo é executado e o usuário clica no elemento de interface do usuário que você criou o Segue de controlador de janela inicial do Storyboard externo especificado na referência de Storyboard serão exibida.

<a name="Referencing-a-Specific-Scene-in-an-External-Storyboard" />

### <a name="referencing-a-specific-scene-in-an-external-storyboard"></a>Referenciando uma cena específica em um Storyboard externo

Para adicionar uma referência a uma cena específica um Storyboard externo (e não o controlador de janela inicial), faça o seguinte:

1. No **Solution Explorer**, clique duas vezes o Storyboard externo para abri-lo para edição no construtor de Interface do Xcode.
2. Adicionar uma nova cena e criar seu layout, como faria normalmente: 

    [![](indepth-images/ref07.png "Criando o layout no Xcode")](indepth-images/ref07.png#lightbox)
3. No **Inspetor de identidade**, insira um **Storyboard ID** janela do controlador da cena nova de: 

    [![](indepth-images/ref08.png "Definir a ID do Storyboard")](indepth-images/ref08.png#lightbox)
3. Abra o Storyboard que você pretende adicionar a referência no construtor de Interface.
4. Arraste um **referência de Storyboard** do **objeto biblioteca** na superfície de Design: 

    [![](indepth-images/ref03.png "Selecionar uma referência de Storyboard da biblioteca")](indepth-images/ref03.png#lightbox)
5. No **Inspetor de identidade**, selecione o nome do **Storyboard** e **ID de referência** (ID de Storyboard) da cena criado acima: 

    [![](indepth-images/ref09.png "Definir a ID de referência")](indepth-images/ref09.png#lightbox)
6. Controle, clique em um Widget de interface do usuário (como um botão) em uma cena existente e criar um novo Segue para a **Storyboard referência** que você acabou de criar. No menu pop-up selecionar **Mostrar** para concluir o Segue: 

    [![](indepth-images/ref06.png "Definição do tipo Segue")](indepth-images/ref06.png#lightbox) 
8. Salve suas alterações para o Storyboard.
9. Retorne ao Visual Studio para Mac sincronizar suas alterações.

Quando o aplicativo é executado e o usuário clica no elemento de interface do usuário que você criou o Segue de cena com o determinado **Storyboard ID** do Storyboard externo especificado na referência do Storyboard será exibida.

<a name="Referencing-a-Specific-Scene-in-the-Same-Storyboard" />

### <a name="referencing-a-specific-scene-in-the-same-storyboard"></a>Referenciando uma cena específica no mesmo Storyboard

Para adicionar uma referência a uma cena específica mesmo Storyboard, faça o seguinte:

1. No **Solution Explorer**, clique duas vezes o Storyboard para abri-lo para edição.
2. Adicionar uma nova cena e criar seu layout, como faria normalmente: 

    [![](indepth-images/ref11.png "Editando o storyboard no Xcode")](indepth-images/ref11.png#lightbox)
3. No **Inspetor de identidade**, insira um **Storyboard ID** janela do controlador da cena nova de: 

    [![](indepth-images/ref12.png "Definir a ID do Storyboard")](indepth-images/ref12.png#lightbox)
3. Arraste um **referência de Storyboard** do **caixa de ferramentas** na superfície de Design: 

    [![](indepth-images/ref03.png "Selecionar uma referência de Storyboard da biblioteca")](indepth-images/ref03.png#lightbox)
5. Em **Inspetor de atributo**, selecione **ID de referência** (ID de Storyboard) da cena criado acima: 

    [![](indepth-images/ref13.png "Definir a ID de referência")](indepth-images/ref13.png#lightbox)
6. Controle, clique em um Widget de interface do usuário (como um botão) em uma cena existente e criar um novo Segue para a **Storyboard referência** que você acabou de criar. No menu pop-up selecionar **Mostrar** para concluir o Segue: 

    [![](indepth-images/ref06.png "Selecionar o tipo Segue")](indepth-images/ref06.png#lightbox) 
8. Salve suas alterações para o Storyboard.
9. Retorne ao Visual Studio para Mac sincronizar suas alterações.

Quando o aplicativo é executado e o usuário clica no elemento de interface do usuário que você criou o Segue de cena com o determinado **Storyboard ID** no mesmo Storyboard especificado na referência do Storyboard será exibida.

<a name="Complex-Storyboard-Example" />

## <a name="complex-storyboard-example"></a>Exemplo de Storyboard complexo

Para obter um exemplo complexo de trabalhar com Storyboards em um aplicativo de Xamarin.Mac, consulte o [aplicativo de exemplo SourceWriter](https://developer.xamarin.com/samples/mac/SourceWriter/). SourceWriter é um editor de código-fonte simples que dá suporte para a conclusão de código e realce de sintaxe simples.

O código do SourceWriter foi totalmente comentado e, quando disponível, foram fornecidos links de tecnologias-chave ou métodos para informações relevantes na Documentação de Guias do Xamarin.Mac.

## <a name="related-links"></a>Links relacionados

- [MacStoryboard (exemplo)](https://developer.xamarin.com/samples/mac/MacStoryboard/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabalhando com o Windows](~/mac/user-interface/window.md)
- [Diretrizes de interface humana do OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introdução ao Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
