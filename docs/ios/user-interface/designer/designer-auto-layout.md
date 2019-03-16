---
title: Layout automático com o Designer do Xamarin para iOS
description: Este guia apresenta o Layout automático do iOS e descreve como usar o Designer do Xamarin para iOS para criar e editar usando restrições de layouts. Ele também aborda as restrições de modificação no código, animar alterações de restrição e muito mais.
ms.prod: xamarin
ms.assetid: CAC7A715-55BB-45E2-BB6D-2168D36D428F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 41a9ec90b4b734dde7a982ac3d4b2e7b2082321c
ms.sourcegitcommit: 650458de1d362cd7de174cacef7838f0e74426f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/15/2019
ms.locfileid: "58070860"
---
# <a name="auto-layout-with-the-xamarin-designer-for-ios"></a>Layout automático com o Designer do Xamarin para iOS

Layout automático (também chamado de "layout adaptável") é uma abordagem de design responsivo. Ao contrário do sistema de layout de transição, onde o local de cada elemento é embutido em código para um ponto na tela, o Layout automático é sobre *relações* -as posições de elementos em relação a outros elementos na superfície de design. No coração do Layout automático é a ideia de restrições ou as regras que definem o posicionamento de um elemento ou um conjunto de elementos no contexto de outros elementos na tela. Porque os elementos não estão vinculados a uma determinada posição na tela, restrições de ajudar a criar um layout adaptável que tenha aparência satisfatório em diferentes tamanhos de tela e orientações do dispositivo.

Neste guia, apresentamos as restrições e como trabalhar com eles no Designer do iOS Xamarin. Este guia não abrange trabalhar programaticamente com restrições. Para obter informações sobre como usar o Layout automático programaticamente, consulte o [documentação da Apple](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html).

## <a name="requirements"></a>Requisitos

O Designer de Xamarin para iOS está disponível no Visual Studio para Mac no Visual Studio 2017 e posterior no Windows.

Este guia pressupõe conhecimento dos componentes do Designer do [Introdução ao Designer do iOS](~/ios/user-interface/designer/introduction.md) guia.

## <a name="introduction-to-constraints"></a>Introdução às restrições

Uma restrição é uma representação matemática da relação entre dois elementos na tela. A posição do elemento como uma relação matemática que representa uma interface do usuário resolve vários problemas associados ao codificar o local de um elemento interface do usuário. Por exemplo, se fôssemos colocar 20px um botão na parte inferior da tela em modo retrato, posição do botão seria fora da tela em modo paisagem. Para evitar isso, poderíamos definido uma restrição que coloca a borda inferior do 20px botão na parte inferior do modo de exibição. A posição da borda do botão, em seguida, será calculada como *button.bottom = view.bottom - 20px*, que colocaria 20px o botão na parte inferior do modo de exibição no modo retrato e paisagem. A capacidade de calcular o posicionamento com base em uma relação matemática faz com que as restrições tão útil no design de interface do usuário.

Quando definimos uma restrição, podemos criar uma `NSLayoutConstraint` objeto que usa como argumentos, os objetos a ser restringido e as propriedades, ou *atributos*, que a restrição vai agir. No designer de iOS, os atributos incluem bordas, como o *esquerdo*, *à direita*, *superior*, e *inferior* de um elemento. Eles também incluem atributos de tamanho, como *altura* e *largura*e o local, de ponto central *centerX* e *centerY*. Por exemplo, quando adicionamos uma restrição na posição do limite esquerdo dos dois botões, o Designer está gerando o código a seguir nos bastidores:

```csharp
View.AddConstraint (NSLayoutConstraint.Create (Button1, NSLayoutAttribute.Left, NSLayoutRelation.Equal, Button2, NSLayoutAttribute.Left, 1, 10));
```

A próxima seção aborda o trabalho com restrições, usando o Designer, incluindo a habilitação e desabilitação de Layout automático e usando a barra de ferramentas de restrições do iOS.

## <a name="enable-auto-layout"></a>Habilitar o Layout automático

A configuração padrão do Designer de iOS tem o modo de restrição habilitado. No entanto, se você precisar habilitar ou desabilitá-lo manualmente, você pode fazer isso em duas etapas:

1.  Clique em uma área vazia na superfície de design. Isso anula a seleção de todos os elementos e exibe as propriedades para o documento de Storyboard.
1.  Marque ou desmarque a **Autolayout Use** caixa de seleção no painel de propriedade:

    ![](designer-auto-layout-images/image01.png "A caixa de seleção Usar Autolayout no painel de propriedade")


Por padrão, não há restrições são criadas ou visível na superfície de. Em vez disso, eles são automaticamente inferidos das informações do quadro no tempo de compilação. Para adicionar as restrições, é necessário selecionar um elemento na superfície de design e adicionar restrições a ele. Podemos fazer isso usando o **barra de ferramentas de restrição**.

## <a name="constraints-toolbar"></a>Barra de ferramentas de restrições

 [![](designer-auto-layout-images/toolbarnew.png "Os comandos de Menu de contexto")](designer-auto-layout-images/toolbarnew.png#lightbox)

A barra de ferramentas de restrições foi atualizada e agora consiste em duas partes principais:

- **Uma alternância de botão de modo de restrições**: Anteriormente, você inseriu o modo de restrições clicando novamente em um modo de exibição selecionado na superfície de design. Agora, você deve usar este botão de alternância na barra de restrições:

  ![alternância de modos de restrições](designer-auto-layout-images/constraints.png)

- **Um botão "Atualizar restrições":** É importante observar que as alterações, dependendo se você está no modo de edição de restrições.
  - No modo de edição de restrição esse botão ajusta as restrições para coincidir com o quadro de elemento.
  - No modo de edição de quadro esse botão ajusta o quadro de elemento para coincidir com a posição em que as restrições estão definindo.


## <a name="surface-based-constraint-editing"></a>Restrição baseada na superfície de edição

Na seção anterior, aprendemos a adicionar restrições padrão e remover restrições, usando a barra de ferramentas de restrições. Para obter mais ajustada restrição edição, podemos interagir com restrições diretamente na superfície de design. Esta seção apresenta os conceitos básicos de restrição baseada na superfície de edição, incluindo controles, áreas para soltar e trabalhar com diferentes tipos de restrições de espaçamento de pin.

### <a name="creating-constraints"></a>Criação de restrições

A ferramenta de Designer do iOS oferece dois tipos de controles para manipular elementos na superfície de design. *Arrastando os controles* e *fixar espaçamento controles*, conforme ilustrado na imagem a seguir:

![controles de exibição](designer-auto-layout-images/controls.png)

Eles são alternados, selecionando o botão de modo de restrições na barra de restrições.

Definem os identificadores em forma de T 4 em cada lado do elemento a *superior*, *à direita*, *inferior*, e *esquerdo* bordas do elemento para um restrição. Definem as duas alças em forma de I à direita e inferior do elemento *altura* e *largura* restrição, respectivamente. O quadrado central lida com ambos *centerX* e *centerY* restrições.

Para criar uma restrição, escolha um identificador e arraste-o em algum lugar na superfície de design. Quando você inicia a operação de arrastar, uma série de linhas/caixas verdes será exibido na superfície de informando o que você pode restringir. Por exemplo, na captura de tela abaixo, estamos estão restringindo o lado superior do que o botão do meio:

 [![](designer-auto-layout-images/image07.png "Restringindo o lado superior do que o botão do meio")](designer-auto-layout-images/image07.png#lightbox)

Observe as três linhas tracejadas verdes entre os dois botões. As linhas verdes indicam *áreas para soltar*, ou os atributos de outros elementos para o qual é possível restringir. Na captura de tela acima, os outros dois botões oferecem 3 áreas para soltar vertical ( *inferior*, *centerY*, *superior*) para restringir o nosso botão. A linha tracejada verde na parte superior do modo de exibição significa que o controlador de exibição oferece uma restrição na parte superior do modo de exibição e a caixa verde sólida significa que o controlador de exibição oferece uma restrição abaixo o guia de layout superior.

> [!IMPORTANT]
> Guias de layout são tipos especiais de destinos de restrição que nos permitem criar a parte superior e restrições inferior que levam em conta a presença das barras de sistema, como barras de status ou as barras de ferramentas. Um dos usos principais é ter um aplicativo compatível entre o iOS 6 e iOS 7, pois a versão mais recente tem o modo de exibição do contêiner estendendo abaixo da barra de status. Para obter mais informações sobre o guia de layout superior, consulte o [documentação da Apple](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/transitionguide/AppearanceCustomization.html#//apple_ref/doc/uid/TP40013174-CH15-SW2).



As próximas três seções apresentam trabalhar com diferentes tipos de restrições.

### <a name="size-constraints"></a>Restrições de tamanho

Com as restrições de tamanho - *altura* e *largura* -você tem duas opções. A primeira opção é arraste a alça para restringir o tamanho do elemento um vizinho, conforme ilustrado pelo exemplo anterior. A outra opção é clicar duas vezes a alça para criar uma restrição de autoatendimento. Isso nos permite especificar um valor de tamanho constante, conforme ilustrado pela captura de tela abaixo:

 [![](designer-auto-layout-images/sizec.png "Arraste a alça para restringir o tamanho do elemento um vizinho, conforme ilustrado aqui")](designer-auto-layout-images/sizec.png#lightbox)

### <a name="center-constraints"></a>Restrições de Center

A alça quadrada criará um *centerX* ou *centerY* restrição, dependendo do contexto. Arrastando a alça quadrada acenderão outros elementos para oferecer a ambas as áreas para soltar vertical e horizontal, conforme ilustrado pela captura de tela abaixo:

 [![](designer-auto-layout-images/centerc.png "Restrições de Center")](designer-auto-layout-images/centerc.png#lightbox)

Se você escolher uma área de soltar vertical, um *centerY* restrição será criada. Se você escolher uma área de soltar horizontal, a restrição se baseará *centerX*.

### <a name="combinational-constraints"></a>Restrições combinatório

Para criar o alinhamento e restrições de igualdade de tamanho entre dois elementos, você pode selecionar itens da barra de ferramentas superior para especificar - ordem - o alinhamento horizontal, alinhamento vertical e igualdades de tamanho, conforme ilustrado pela captura de tela abaixo:

 [![](designer-auto-layout-images/image06.png "Restrições combinatório")](designer-auto-layout-images/image06.png#lightbox)

### <a name="visualizing-and-editing-constraints"></a>Visualizar e editar restrições

Quando você adiciona uma restrição, ele será exibido na superfície de design como uma linha azul quando você seleciona um item:

 [![](designer-auto-layout-images/image09.png "Restrições de visualização")](designer-auto-layout-images/image09.png#lightbox)

Você pode selecionar uma restrição clicando em uma linha azul e editar os valores de restrição diretamente no painel de propriedade. Como alternativa, clicar duas vezes em uma linha azul serão exibida uma pop-over que permite que você edite os valores diretamente na superfície de design:

 [![](designer-auto-layout-images/image08.png "Restrições de edição")](designer-auto-layout-images/image08.png#lightbox)

## <a name="constraint-issues"></a>Problemas de restrição

Vários tipos de problemas podem surgir ao usar restrições:

-  **As restrições conflitantes** — isso ocorre quando várias restrições de forçar o elemento ter valores conflitantes para um atributo e o mecanismo de restrição não conseguir reconciliá-las.
-  **Underconstrained itens** — propriedades de um elemento (local + tamanho) devem ser totalmente cobertas pelo seu conjunto de restrições e tamanhos intrínseco para as restrições ser válido. Se esses valores são ambíguos, o item é dito como ser underconstrained.
-  **Quadro extravio** — isso ocorre quando o quadro de um elemento e seu conjunto de restrições definem dois retângulos resultantes diferentes.


Esta seção aborda os três problemas listados acima e fornece detalhes sobre como lidar com eles.

### <a name="conflicting-constraints"></a>Restrições conflitantes

As restrições conflitantes são marcadas em vermelho e tem um símbolo de aviso. Passar o mouse sobre os símbolos de aviso exibirá um pop-over com informações sobre o conflito:

 [![](designer-auto-layout-images/image11.png "As restrições conflitantes aviso")](designer-auto-layout-images/image11.png#lightbox)

### <a name="underconstrained-items"></a>Itens underconstrained

Underconstrained itens aparecem em laranja e disparam a aparência de um ícone de marcador laranja na barra de objeto do controlador de exibição:

 [![](designer-auto-layout-images/image02.png "Underconstrained itens aparecem em laranja")](designer-auto-layout-images/image02.png#lightbox)

Se você clicar no ícone marcador, você pode obter informações sobre itens underconstrained na cena e resolver os problemas, ambos totalmente restringindo-os ou removendo suas restrições, conforme ilustrado pela captura de tela abaixo:

 [![](designer-auto-layout-images/image10.png "Corrigindo Underconstrained itens")](designer-auto-layout-images/image10.png#lightbox)

### <a name="frame-misplacement"></a>Extravio do quadro

Extravio de quadro usa o mesmo código de cor como underconstrained itens. O item será renderizado sempre na superfície de usando seu quadro nativo, mas no caso de uma localização errada de frame um retângulo vermelho marcará onde o item terminarão quando o aplicativo é executado, conforme ilustrado pela captura de tela abaixo:

 [![](designer-auto-layout-images/image05.png "O modo de exibição do exemplo extravio do quadro")](designer-auto-layout-images/image05.png#lightbox)

Para resolver erros de extravio do quadro, selecione o **atualização de quadros com base nas restrições** botão da barra de ferramentas restrições (botão mais à direita):

 [![](designer-auto-layout-images/image03.png "Atualizar quadros com base no botão de barra de ferramentas de restrições")](designer-auto-layout-images/image03.png#lightbox)

Isso ajustará automaticamente o quadro de elemento para coincidir com as posições definidas por controles.

<a name="modifying-in-code" />

## <a name="modifying-constraints-in-code"></a>Modificando restrições no código

Com base nos requisitos do seu aplicativo, pode haver ocasiões em que você precisa para modificar uma restrição no código. Por exemplo, para redimensionar ou reposicionar a exibição de uma restrição está associada, para alterar a prioridade de uma restrição ou desativar uma restrição completamente.

Para acessar uma restrição no código, você primeiro precisará expô-lo no Designer do iOS fazendo o seguinte:

1. Crie a restrição como normal (usando qualquer um dos métodos listados acima).
2. No **Explorer de estrutura de tópicos do documento**, encontre a restrição desejada e selecione-o:

    [![](designer-auto-layout-images/modify01.png "O Gerenciador de estrutura de tópicos de documentos")](designer-auto-layout-images/modify01.png#lightbox)
3. Em seguida, atribua uma **nome** para a restrição na **Widget** guia dos **Gerenciador de propriedades**:

    [![](designer-auto-layout-images/modify02.png "Na guia de Widget")](designer-auto-layout-images/modify02.png#lightbox)
4. Salve as alterações.

Com as alterações acima em vigor, você pode acessar a restrição no código e modificar suas propriedades. Por exemplo, você pode usar o seguinte para definir a altura da exibição anexada a zero:

```csharp
ViewInfoHeight.Constant = 0;
```

Dada a seguinte configuração para a restrição no Designer do iOS:

[![](designer-auto-layout-images/modify03.png "Edição de uma restrição no Gerenciador de propriedade")](designer-auto-layout-images/modify03.png#lightbox)

### <a name="the-deferred-layout-pass"></a>A passagem de Layout adiada

Em vez de atualizar instantaneamente a exibição anexada em resposta às alterações de restrição, o mecanismo de Layout automático agenda uma _passagem de Layout adiado_ para o futuro próximo. Durante essa passagem adiada, não é apenas restrição da exibição determinada atualizada, as restrições para cada exibição na hierarquia são recalculadas e atualizado para se ajustar ao novo layout.

Em qualquer ponto, você pode agendar sua passagem de Layout adiada chamando o `SetNeedsLayout` ou `SetNeedsUpdateConstraints` métodos do modo de exibição pai. 

A passagem de Layout adiado consiste em duas passagens exclusivas por meio da hierarquia de exibição:

- **A passagem de atualização** -nessa passagem, o mecanismo de Layout automático atravessa a hierarquia de exibição e invoca o `UpdateViewConstraints` método em todos os controladores de exibição e o `UpdateConstraints` método em todas as exibições.
- **A passagem de Layout** - novamente, o mecanismo de Layout automático atravessa a hierarquia de exibição, mas desta vez invoca o `ViewWillLayoutSubviews` método em todos os controladores de exibição e o `LayoutSubviews` método em todas as exibições. O `LayoutSubviews` atualizações do método a `Frame` propriedade de cada modo de exibição secundário com o retângulo calculado pelo mecanismo de Layout automático.

### <a name="animating-constraint-changes"></a>Animando alterações de restrição

Além de modificar as propriedades da restrição, você pode usar a animação de núcleo para animar as alterações às restrições do modo de exibição. Por exemplo:

```csharp
UIView.BeginAnimations("OpenInfo");
UIView.SetAnimationDuration(1.0f);
ViewInfoHeight.Constant = 237;
View.LayoutIfNeeded();

//Execute Animation
UIView.CommitAnimations();
```

A chave aqui é chamar o `LayoutIfNeeded` método da exibição pai dentro do bloco de animação. Isso informa o modo de exibição para desenhar cada "frame" do local animado ou alteração do tamanho. Sem essa linha, o modo de exibição simplesmente se encaixaria para a versão final sem animação.

## <a name="summary"></a>Resumo

Este guia apresentou iOS automática (ou "adaptive") o conceito de restrições como representações de matemáticas de relações entre os elementos na superfície de design e de Layout. Ele descreveu como habilitar o Layout automático no designer de iOS, trabalhando com o **barra de ferramentas de restrições**e editar restrições individualmente na superfície de design. Em seguida, ele explicou como solucionar problemas comuns de restrições três. Por fim, ele mostrou como modificar restrições no código.

## <a name="related-links"></a>Links relacionados

- [Introdução ao Storyboards](~/ios/user-interface/storyboards/index.md)
- [iOS projetáveis passo a passo de controles](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Visão geral do Designer Android](~/android/user-interface/android-designer/index.md)
- [Restrições programáticas](~/ios/user-interface/programmatic-layout-constraints.md)
- [Apple – guia de Layout automático](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/AutolayoutPG/Introduction/Introduction.html#/apple_ref/doc/uid/TP40010853-CH13-SW1)
