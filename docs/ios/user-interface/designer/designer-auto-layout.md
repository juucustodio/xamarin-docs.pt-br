---
title: "Layout automático com o Designer de Xamarin para iOS"
description: "Este guia apresenta novas restrições fluxo de trabalho disponível no Designer de Xamarin para iOS e iOS Layout automático."
ms.topic: article
ms.prod: xamarin
ms.assetid: CAC7A715-55BB-45E2-BB6D-2168D36D428F
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: ff19048504ee76db614306adebb71b7237139091
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2018
---
# <a name="auto-layout-with-the-xamarin-designer-for-ios"></a>Layout automático com o Designer de Xamarin para iOS

_Este guia apresenta novas restrições fluxo de trabalho disponível no Designer de Xamarin para iOS e iOS Layout automático._

Layout automático (também chamado de "adaptável layout") é uma abordagem de design responsivo. Ao contrário do sistema de layout de transição, onde o local de cada elemento é codificado para um ponto na tela, o Layout automático é sobre *relações* -as posições de elementos em relação a outros elementos na superfície de design. A essência do Layout automático é a ideia de restrições ou regras que definem o posicionamento de um elemento ou um conjunto de elementos no contexto de outros elementos na tela. Como os elementos não estão vinculados a uma posição específica na tela, restrições ajudam a criar um layout adaptável que parece bom em diferentes tamanhos de telas e orientações de dispositivo.

Neste guia, apresentamos restrições e como trabalhar com eles no Xamarin iOS Designer. Este guia não abrange trabalhar programaticamente com restrições. Para obter informações sobre como usar o Layout automático programaticamente, consulte o o [documentação da Apple](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html).

## <a name="requirements"></a>Requisitos

O Designer de Xamarin para iOS está disponível no Visual Studio para Mac no Visual Studio 2015 e 2017 no Windows.

Este guia pressupõe conhecimento dos componentes do Designer do [introdução para o Designer do iOS](~/ios/user-interface/designer/introduction.md) guia.

## <a name="introduction-to-constraints"></a>Introdução às restrições

Uma restrição é uma representação matemática da relação entre dois elementos na tela. A posição do elemento como uma relação de matemática que representa uma interface do usuário resolve vários problemas associados ao embutir o local de um elemento interface do usuário. Por exemplo, se foi colocar 20px um botão na parte inferior da tela no modo retrato, posição do botão seria fora da tela no modo paisagem. Para evitar isso, poderíamos definido uma restrição que coloca a borda inferior de 20px o botão na parte inferior do modo de exibição. A posição da borda do botão deve ser calculada como *button.bottom = view.bottom - 20px*, que poderia colocar 20px o botão na parte inferior do modo de exibição no modo retrato e paisagem. A capacidade de calcular o posicionamento com base em uma relação matemática é o que torna as restrições tão útil no design de interface do usuário.

Quando vamos definir uma restrição, podemos criar um `NSLayoutConstraint` objeto que usa como argumentos, os objetos a ser restringido e as propriedades ou *atributos*, que afetarão a restrição. No designer de iOS, os atributos incluem bordas, como o *esquerdo*, *direita*, *superior*, e *inferior* de um elemento. Eles também incluem atributos de tamanho, como *altura* e *largura*e o centro do ponto de localização, *centerX* e *centerY*. Por exemplo, quando adicionamos uma restrição na posição do limite esquerdo de dois botões, o Designer está gerando o código a seguir nos bastidores:

```csharp
View.AddConstraint (NSLayoutConstraint.Create (Button1, NSLayoutAttribute.Left, NSLayoutRelation.Equal, Button2, NSLayoutAttribute.Left, 1, 10));
```

A próxima seção aborda o trabalho com restrições, usando o Designer, incluindo a habilitação e desabilitação de Layout automático e usando a barra de ferramentas de restrições do iOS.

## <a name="enable-auto-layout"></a>Habilitar Layout automático

A configuração padrão do Designer de iOS tem o modo de restrição habilitado. No entanto, se você precisar habilitar ou desabilitá-lo manualmente, você pode fazer isso em duas etapas:

1.  Clique em uma área vazia na superfície de design. Isso anula a seleção de todos os elementos e exibe as propriedades para o documento de Storyboard.
1.  Marque ou desmarque o **Use Autolayout** caixa de seleção no painel de propriedade:

    ![](designer-auto-layout-images/image01.png "A caixa de seleção Usar Autolayout no painel de propriedade")


Por padrão, sem restrições são criadas ou visível na superfície do. Em vez disso, eles são automaticamente inferidos das informações do quadro no tempo de compilação. Para adicionar restrições, é preciso selecionar um elemento na superfície de design e adicionar restrições a ele. Podemos fazer que usando o **barra de ferramentas de restrição**.

## <a name="constraints-toolbar"></a>Barra de ferramentas de restrições

 [![](designer-auto-layout-images/toolbarnew.png "Os comandos de Menu de contexto")](designer-auto-layout-images/toolbarnew.png#lightbox)

A barra de ferramentas de restrições foi atualizada e agora consiste em duas partes principais:

- **Uma alternância de botão de modo de restrições**: anteriormente, você inseriu o modo de restrições, clique novamente em um modo de exibição selecionado na superfície de design. Agora, você deve usar este botão de alternância na barra de restrições:

  ![Alternar modos de restrições](designer-auto-layout-images/constraints.png)

- **Um botão "Restrições de atualização":** é importante observar que as alterações dependendo se você está no modo de edição de restrições.
  - No modo de edição de restrição esse botão ajusta as restrições para corresponder o quadro do elemento.
  - No modo de edição de quadro esse botão ajusta o quadro de elemento para coincidir com a posição que de restrições estão definindo.


## <a name="surface-based-constraint-editing"></a>Restrição baseada na superfície de edição

Na seção anterior, aprendemos adicionar restrições padrão e remover restrições, usando a barra de ferramentas de restrições. Para editar restrição ajustada mais, podemos pode interagir com restrições diretamente na superfície de design. Esta seção apresenta os fundamentos de restrição baseada na superfície de edição, incluindo controles, áreas para soltar e trabalhar com diferentes tipos de restrições de espaçamento de pin.

### <a name="creating-constraints"></a>Criação de restrições

A ferramenta de Designer do iOS oferece dois tipos de controles para manipular elementos na superfície de design. *Arrastando controles* e *espaçamento pin controles*, conforme ilustrado na imagem a seguir:

![controles de exibição](designer-auto-layout-images/controls.png)

Eles são alternados, selecionando o botão de modo de restrições na barra de restrições.

As alças em forma de T 4 em cada lado do elemento definem o *superior*, *direita*, *inferior*, e *esquerdo* bordas do elemento para um restrição. Definem as duas alças em forma de I à direita e inferior do elemento *altura* e *largura* restrição respectivamente. O quadrado médio lida com ambos *centerX* e *centerY* restrições.

Para criar uma restrição, selecione um identificador e arraste-o em algum lugar na superfície de design. Quando você inicia a operação de arrastar, uma série de linhas/caixas verdes será exibido na superfície de informando o que você pode restringir. Por exemplo, na captura de tela abaixo, podemos são restringir o lado superior do botão central:

 [![](designer-auto-layout-images/image07.png "Restringir o lado superior do botão intermediário")](designer-auto-layout-images/image07.png#lightbox)

Observe as três linhas verdes tracejadas entre os dois botões. As linhas verdes indicam *áreas para soltar*, ou os atributos de outros elementos para o qual é possível restringir. Na captura de tela acima, os outros dois botões oferecem 3 áreas para soltar vertical ( *inferior*, *centerY*, *superior*) para restringir o nosso botão. A linha tracejada verde na parte superior do modo de exibição significa que o controlador de exibição oferece uma restrição na parte superior do modo de exibição, e na caixa verde sólida significa que o controlador de exibição oferece uma restrição abaixo guia layout superior.

> [!IMPORTANT]
> Guias de layout são tipos especiais de destinos de restrição que nos permitem criar superior e restrições de inferior que levam em conta a presença de barras de sistema, como barras de status ou as barras de ferramentas. Um dos principais usos é ter um aplicativo compatível entre iOS 6 e iOS 7 desde que a versão mais recente tem o modo de exibição de contêiner estendendo abaixo da barra de status. Para obter mais informações sobre a guia de layout superior, consulte o [documentação da Apple](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/transitionguide/AppearanceCustomization.html#//apple_ref/doc/uid/TP40013174-CH15-SW2).



As próximas três seções apresentam trabalhar com diferentes tipos de restrições.

### <a name="size-constraints"></a>Restrições de tamanho

Com restrições de tamanho - *altura* e *largura* -você tem duas opções. A primeira opção é arraste a alça para restringir a um tamanho de elemento vizinho, conforme ilustrado pelo exemplo acima. A outra opção é clicar duas vezes o identificador para criar uma restrição de autoatendimento. Isso nos permite especificar um valor de tamanho da constante, conforme ilustrado na captura de tela abaixo:

 [![](designer-auto-layout-images/sizec.png "Arraste a alça para restringir a um tamanho de elemento vizinho, conforme ilustrado aqui")](designer-auto-layout-images/sizec.png#lightbox)

### <a name="center-constraints"></a>Restrições de Center

O identificador quadrado criará uma *centerX* ou *centerY* restrição, dependendo do contexto. Arraste a alça quadrada acenderá os outros elementos para oferecer ambas as áreas para soltar vertical e horizontal, conforme ilustrado na captura de tela abaixo:

 [![](designer-auto-layout-images/centerc.png "Restrições de Center")](designer-auto-layout-images/centerc.png#lightbox)

Se você escolher uma área para soltar vertical, um *centerY* restrição será criada. Se você escolher uma área para soltar horizontal, a restrição será baseada no *centerX*.

### <a name="combinational-constraints"></a>Restrições combinatório

Para criar o alinhamento e restrições de igualdade de tamanho entre dois elementos, você pode selecionar itens da barra de ferramentas superior para especificar - ordem - alinhamento horizontal, alinhamento vertical e igualdades de tamanho, conforme ilustrado na captura de tela abaixo:

 [![](designer-auto-layout-images/image06.png "Restrições combinatório")](designer-auto-layout-images/image06.png#lightbox)

### <a name="visualizing-and-editing-constraints"></a>Visualização e edição de restrições

Quando você adicionar uma restrição, ele será exibido na superfície de design como uma linha azul quando você seleciona um item:

 [![](designer-auto-layout-images/image09.png "Visualizando restrições")](designer-auto-layout-images/image09.png#lightbox)

Você pode selecionar uma restrição clicando em uma linha azul e editar os valores de restrição diretamente no painel de propriedade. Como alternativa, a duas vezes em uma linha azul abrirá um popover que permite que você edite os valores diretamente na superfície de design:

 [![](designer-auto-layout-images/image08.png "Restrições de edição")](designer-auto-layout-images/image08.png#lightbox)

## <a name="constraint-issues"></a>Problemas de restrição

Vários tipos de problemas podem surgir ao usar restrições:

-  **Restrições conflitantes** – isso ocorre quando várias restrições forçar o elemento com valores conflitantes para um atributo e o mecanismo de restrição não é possível reconciliá-los.
-  **Underconstrained itens** – propriedades de um elemento (local + tamanho) devem ser totalmente cobertas pelo seu conjunto de restrições e intrínsecos tamanhos para as restrições ser válido. Se esses valores são ambíguos, o item deve ser underconstrained.
-  **Localização errada do quadro** – isso ocorre quando o quadro de um elemento e seu conjunto de restrições de definem dois retângulos resultantes diferentes.


Esta seção aborda os três problemas listados acima e fornece detalhes sobre como lidar com eles.

### <a name="conflicting-constraints"></a>Restrições conflitantes

Restrições conflitantes são marcadas em vermelho e tem um símbolo de aviso. Focalizar os símbolos de aviso traz um popover com informações sobre o conflito:

 [![](designer-auto-layout-images/image11.png "Restrições conflitantes aviso")](designer-auto-layout-images/image11.png#lightbox)

### <a name="underconstrained-items"></a>Itens underconstrained

Underconstrained itens aparecem em laranja e disparam a aparência de um ícone de marcador laranja na barra de objeto de controlador de exibição:

 [![](designer-auto-layout-images/image02.png "Underconstrained itens aparecem em laranja")](designer-auto-layout-images/image02.png#lightbox)

Se você clicar no ícone marcador, você pode obter informações sobre os itens underconstrained na cena e resolver os problemas, ou totalmente restringindo-os ou removendo suas restrições, conforme ilustrado na captura de tela abaixo:

 [![](designer-auto-layout-images/image10.png "Corrigindo itens Underconstrained")](designer-auto-layout-images/image10.png#lightbox)

### <a name="frame-misplacement"></a>Localização errada do quadro

Localização errada do quadro usa o mesmo código de cor como underconstrained itens. O item sempre será renderizado na superfície de usando seu quadro nativo, mas no caso de uma localização errada de quadro um retângulo vermelho marca onde o item terminará quando o aplicativo é executado, conforme ilustrado na captura de tela abaixo:

 [![](designer-auto-layout-images/image05.png "Exemplo de modo de localização errada do quadro")](designer-auto-layout-images/image05.png#lightbox)

Para resolver erros de localização errada do quadro, selecione o **atualização quadros com base em restrições** botão da barra de ferramentas restrições (botão mais à direita):

 [![](designer-auto-layout-images/image03.png "Quadros de atualização com base no botão da barra de ferramentas de restrições")](designer-auto-layout-images/image03.png#lightbox)

Isso ajustará automaticamente o quadro de elemento para coincidir com as posições definidas pelos controles.

<a name="modifying-in-code" />

## <a name="modifying-constraints-in-code"></a>Modificando restrições em código

Com base nos requisitos de seu aplicativo, pode haver ocasiões em que você precisa para modificar uma restrição no código. Por exemplo, para redimensionar ou reposicionar a exibição de uma restrição está associada, para alterar a prioridade da restrição ou desativar uma restrição completamente.

Para acessar uma restrição no código, você deve primeiro expô-lo no Designer de iOS fazendo o seguinte:

1. Crie a restrição normal (usando qualquer um dos métodos listados acima).
2. No **Explorer de estrutura de tópicos de documento**, localizar a restrição desejada e selecione-o:

    [![](designer-auto-layout-images/modify01.png "O Gerenciador de estrutura de tópicos do documento")](designer-auto-layout-images/modify01.png#lightbox)
3. Em seguida, atribuir um **nome** para a restrição no **Widget** guia do **propriedades Explorer**:

    [![](designer-auto-layout-images/modify02.png "A guia de Widget")](designer-auto-layout-images/modify02.png#lightbox)
4. Salve as alterações.

Com as alterações acima em vigor, você pode acessar a restrição no código e modificar suas propriedades. Por exemplo, você pode usar o seguinte para definir a altura da exibição anexada como zero:

```csharp
ViewInfoHeight.Constant = 0;
```

Considerando a configuração a seguir para a restrição no iOS Designer:

[![](designer-auto-layout-images/modify03.png "Edição de uma restrição no Gerenciador de propriedade")](designer-auto-layout-images/modify03.png#lightbox)

### <a name="the-deferred-layout-pass"></a>A passagem de Layout adiada

Em vez de atualizar imediatamente o modo de exibição anexado em resposta a alterações de restrição, o mecanismo de Layout automático agenda um _adiado Layout Pass_ para o futuro próximo. Durante essa passagem adiada, não só é restrição do modo de exibição determinado atualizada, as restrições para cada modo de exibição da hierarquia são recalculados e atualizado para ajustá-las para o novo layout.

A qualquer momento, você pode agendar a passagem de Layout de seu próprio adiada chamando o `SetNeedsLayout` ou `SetNeedsUpdateConstraints` métodos do modo de exibição pai. 

A passagem de Layout adiado consiste em dois passos exclusivos por meio da hierarquia do modo de exibição:

- **A fase de atualização** -nessa passagem, o mecanismo de Layout automático atravessa a hierarquia do modo de exibição e invoca o `UpdateViewConstraints` método em todos os controladores de exibição e o `UpdateConstraints` método em todos os modos de exibição.
- **A passagem de Layout** - novamente, o mecanismo de Layout automático atravessa a hierarquia do modo de exibição, mas desta vez invoca o `ViewWillLayoutSubviews` método em todos os controladores de exibição e o `LayoutSubviews` método em todos os modos de exibição. O `LayoutSubviews` método atualizações do `Frame` propriedade de cada modo de exibição secundário com o retângulo calculado pelo mecanismo de Layout automático.

### <a name="animating-constraint-changes"></a>Alterações de restrição de animação

Além de modificar as propriedades de restrição, você pode usar animação de núcleo para animar alterações às restrições do modo de exibição. Por exemplo:

```csharp
UIView.BeginAnimations("OpenInfo");
UIView.SetAnimationDuration(1.0f);
ViewInfoHeight.Constant = 237;
View.LayoutIfNeeded();

//Execute Animation
UIView.CommitAnimations();
```

A chave aqui é chamar o `LayoutIfNeeded` método da exibição pai dentro do bloco de animação. Isso informa o modo de exibição para desenhar cada "frame" da alteração no tamanho ou local animado. Sem essa linha, o modo de exibição seria simplesmente ajustar-se para a versão final sem animação.

## <a name="summary"></a>Resumo

Este guia introduzido iOS automática (ou "adaptive") Layout e o conceito de restrições como representações matemáticas de relações entre os elementos na superfície de design. Ele descreveu como habilitar o Layout automático no designer de iOS, trabalhando com o **barra de ferramentas de restrições**e editar restrições individualmente na superfície de design. Em seguida, ele explicou como solucionar problemas comuns de restrições três. Finalmente, ele mostrou como modificar restrições no código.

## <a name="related-links"></a>Links relacionados

- [Introdução ao Storyboards](~/ios/user-interface/storyboards/index.md)
- [iOS projetáveis controles passo a passo](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Visão geral do Android Designer](~/android/user-interface/android-designer/index.md)
- [Restrições de programação](~/ios/user-interface/programmatic-layout-constraints.md)
- [Apple - guia de Layout automático](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/AutolayoutPG/Introduction/Introduction.html#/apple_ref/doc/uid/TP40010853-CH13-SW1)
