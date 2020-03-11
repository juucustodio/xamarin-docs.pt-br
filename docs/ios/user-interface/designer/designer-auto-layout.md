---
title: Layout automático com o Xamarin Designer para iOS
description: Este guia apresenta o layout automático do iOS e descreve como usar o Xamarin Designer para iOS para criar e editar layouts usando restrições. Ele também aborda a modificação de restrições no código, a animação de alterações de restrição e muito mais.
ms.prod: xamarin
ms.assetid: CAC7A715-55BB-45E2-BB6D-2168D36D428F
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 35a8d3aeb00ac73f944712cb31f913f98bd3b6e8
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78919162"
---
# <a name="auto-layout-with-the-xamarin-designer-for-ios"></a>Layout automático com o Xamarin Designer para iOS

O layout automático (também chamado de "layout adaptável") é uma abordagem de design responsiva. Ao contrário do sistema de layout de transição, onde o local de cada elemento é embutido em código para um ponto na tela, o layout automático é sobre *relações* – as posições de elementos em relação a outros elementos na superfície de design. No coração do layout automático está a ideia de restrições ou regras que definem o posicionamento de um elemento ou conjunto de elementos no contexto de outros elementos na tela. Como os elementos não estão vinculados a uma posição específica na tela, as restrições ajudam a criar um layout adaptável que parece bom em diferentes tamanhos de tela e orientações de dispositivo.

Neste guia, apresentamos restrições e como trabalhar com eles no Xamarin iOS designer. Este guia não aborda o trabalho com restrições programaticamente. Para obter informações sobre como usar o layout automático programaticamente, consulte a [documentação da Apple](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html).

## <a name="requirements"></a>Requisitos

O Xamarin Designer para iOS está disponível em Visual Studio para Mac no Visual Studio 2017 e posterior no Windows.

Este guia pressupõe o conhecimento dos componentes do designer da [introdução ao guia do designer do IOS](~/ios/user-interface/designer/introduction.md) .

## <a name="introduction-to-constraints"></a>Introdução às restrições

Uma restrição é uma representação matemática da relação entre dois elementos na tela. A representação da posição de um elemento de interface do usuário como uma relação matemática resolve vários problemas associados ao código da localização de um elemento de interface do usuário. Por exemplo, se tivéssemos de colocar um botão 20px na parte inferior da tela no modo retrato, a posição do botão estará fora da tela no modo paisagem. Para evitar isso, poderíamos definir uma restrição que coloca a borda inferior do botão 20px na parte inferior da exibição. Em seguida, a posição da borda do botão seria calculada como *Button. Bottom = View. Bottom-20px*, que colocaria o botão 20px na parte inferior da exibição no modo retrato e paisagem. A capacidade de calcular o posicionamento com base em uma relação matemática é o que torna as restrições tão úteis no design da interface do usuário.

Quando definimos uma restrição, criamos um objeto `NSLayoutConstraint` que usa como argumentos os objetos a serem restritos e as propriedades, ou *atributos*, em que a restrição atuará. No designer do iOS, os atributos incluem bordas como a *esquerda*, *direita*, *superior*e *inferior* de um elemento. Eles também incluem atributos de tamanho, como *altura* e *largura*, e o local do ponto central, *CenterX* e *CenterY*. Por exemplo, quando adicionamos uma restrição na posição do limite esquerdo de dois botões, o designer está gerando o seguinte código nos bastidores:

```csharp
View.AddConstraint (NSLayoutConstraint.Create (Button1, NSLayoutAttribute.Left, NSLayoutRelation.Equal, Button2, NSLayoutAttribute.Left, 1, 10));
```

A próxima seção aborda como trabalhar com restrições usando o designer do iOS, incluindo habilitar e desabilitar o layout automático e usar a barra de ferramentas de restrições.

## <a name="enable-auto-layout"></a>Habilitar layout automático

A configuração padrão do designer do iOS tem o modo de restrição habilitado. No entanto, se você precisar habilitá-lo ou desabilitá-lo manualmente, poderá fazer isso em duas etapas:

1. Clique em um espaço vazio na superfície de design. Isso anula a seleção de qualquer elemento e exibe as propriedades do documento Storyboard.
1. Marque ou desmarque a caixa de seleção **usar AutoLayout** no painel de propriedades:

    ![](designer-auto-layout-images/image01.png "The Use Autolayout checkbox in the property panel")

Por padrão, nenhuma restrição é criada ou visível na superfície. Em vez disso, eles são inferidos automaticamente das informações do quadro no momento da compilação. Para adicionar restrições, precisamos selecionar um elemento na superfície de design e adicionar restrições a ele. Podemos fazer isso usando a **barra de ferramentas de restrição**.

## <a name="constraints-toolbar"></a>Barra de ferramentas restrições

 [![](designer-auto-layout-images/toolbarnew.png "The Context Menu Commands")](designer-auto-layout-images/toolbarnew.png#lightbox)

A barra de ferramentas de restrições foi atualizada e agora consiste em duas partes principais:

- **Uma alternância de botão de modo de restrições**: anteriormente, você inseriu o modo de restrições clicando novamente em uma exibição selecionada na superfície de design. Agora você deve usar esse botão de alternância na barra de restrições:

  ![alternância de modos de restrições](designer-auto-layout-images/constraints.png)

- **Um botão "atualizar restrições":** É importante observar que as alterações dependem de se você estiver no modo de edição de restrições.
  - No modo de edição de restrição, esse botão ajusta as restrições para corresponder ao quadro do elemento.
  - No modo de edição de quadro, esse botão ajusta o quadro do elemento para corresponder à posição que as restrições estão definindo.

## <a name="constraints-editing-popover"></a>Popover de edição de restrições

O pop-up do editor de restrições nos permite adicionar e atualizar as várias restrições de uma vez para uma exibição SELECT. Podemos criar várias restrições de espaçamento, taxa de proporção e alinhamento, como alinhar uma exibição às bordas esquerdas de duas exibições.

Para as restrições de edição no modo de exibição selecionado, clique nas reticências para exibir as restrições popover: ![edição popover](designer-auto-layout-images/constraints-popup.png)

Ao abrir o popover de restrições, ele exibe as restrições predefinidas na exibição. Podemos definir todas as restrições de espaçamento selecionando **todos os lados** da ComboBox no canto superior direito e selecionar **limpar tudo** para removê-los.

O **W** definirá Width e **H** definirá a restrição de altura. Ao verificar a **taxa de proporção**, a altura e a largura das exibições serão controladas em diferentes tamanhos de tela, a largura da exibição será usada como numerador para o ração e a altura como o denominador.

![espaçamento de restrições](designer-auto-layout-images/constraints-spacing.png)

Quatro caixas de combinação para restrições de espaçamento lista as exibições vizinhas para ancorar a restrição

## <a name="surface-based-constraint-editing"></a>Edição de restrição baseada em superfície

Para uma edição de restrição mais ajustada, podemos interagir com restrições diretamente na superfície de design. Esta seção apresenta os conceitos básicos da edição de restrição baseada em superfície, incluindo controles de espaçamento de PIN, áreas de soltar e trabalho com diferentes tipos de restrições.

### <a name="creating-constraints"></a>Criando restrições

A ferramenta de designer do iOS oferece dois tipos de controles para manipular elementos na superfície de design. *Arrastando controles* e *fixando controles de espaçamento*, conforme ilustrado na imagem a seguir:

![Exibir controles](designer-auto-layout-images/controls.png)

Eles são alternados selecionando o botão modo de restrições na barra restrições.

Os 4 identificadores em forma de T em cada lado do elemento definem as bordas *superior*, *direita*, *inferior*e *esquerda* do elemento para uma restrição. Os dois identificadores de formato cruzado à direita e à parte inferior do elemento definem a *altura* e a restrição de *largura* , respectivamente. O quadrado médio lida com as restrições *CenterX* e *CenterY* .

Para criar uma restrição, escolha um identificador e arraste-o para algum lugar na superfície de design. Quando você inicia a operação de arrastar, uma série de linhas/caixas verdes aparecerá na superfície informando o que você pode restringir. Por exemplo, na captura de tela abaixo, estamos restringindo o lado superior do botão do meio:

 [![](designer-auto-layout-images/image07.png "Constraining the top side of the middle button")](designer-auto-layout-images/image07.png#lightbox)

Observe as três linhas verdes tracejadas entre os outros dois botões. As linhas verdes indicam *áreas de soltar*ou os atributos de outros elementos aos quais podemos restringir. Na captura de tela acima, os outros dois botões oferecem 3 áreas verticais de soltar ( *inferior*, *Center*, *superior*) para restringir nosso botão. A linha verde tracejada na parte superior da exibição significa que o controlador de exibição oferece uma restrição na parte superior da exibição e a caixa verde sólida significa que o controlador de exibição oferece uma restrição abaixo do guia de layout superior.

> [!IMPORTANT]
> Guias de layout são tipos especiais de destinos de restrição que nos permitem criar restrições superiores e inferiores que levam em conta a presença de barras do sistema, como barras de status ou barras de ferramentas. Um dos principais usos é ter um aplicativo compatível entre o iOS 6 e o iOS 7, já que a versão mais recente tem a exibição de contêiner sendo estendida abaixo da barra de status. Para obter mais informações sobre o guia de layout superior, consulte a [documentação da Apple](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/transitionguide/AppearanceCustomization.html#//apple_ref/doc/uid/TP40013174-CH15-SW2).

As próximas três seções introduzem o trabalho com diferentes tipos de restrições.

### <a name="size-constraints"></a>Restrições de tamanho

Com as restrições de tamanho- *altura* e *largura* , você tem duas opções. A primeira opção é arrastar a alça para restringir a um tamanho de elemento vizinho, conforme ilustrado pelo exemplo acima. A outra opção é clicar duas vezes na alça para criar uma autorestrição. Isso nos permite especificar um valor de tamanho constante, conforme ilustrado pela captura de tela abaixo:

 [![](designer-auto-layout-images/sizec.png "Drag the handle to constrain to a neighbor element size, as illustrated here")](designer-auto-layout-images/sizec.png#lightbox)

### <a name="center-constraints"></a>Restrições do centro

O identificador quadrado criará uma restrição *CenterX* ou *CenterY* , dependendo do contexto. Arrastar o identificador quadrado acenderá os outros elementos para oferecer áreas de soltar vertical e horizontal, conforme ilustrado pela captura de tela abaixo:

 [![](designer-auto-layout-images/centerc.png "Center Constraints")](designer-auto-layout-images/centerc.png#lightbox)

Se você escolher uma área de soltar vertical, uma restrição de *Centro* será criada. Se você escolher uma área de remoção horizontal, a restrição será baseada em *CenterX*.

### <a name="combinational-constraints"></a>Restrições de combinação

Para criar restrições de igualdade de tamanho e alinhamento entre dois elementos, você pode selecionar itens de uma barra de ferramentas superior para especificar o alinhamento horizontal da ordem, o alinhamento vertical e o tamanho equalities, conforme ilustrado pela captura de tela abaixo:

 [![](designer-auto-layout-images/image06.png "Combinational Constraints")](designer-auto-layout-images/image06.png#lightbox)

### <a name="visualizing-and-editing-constraints"></a>Visualizando e editando restrições

Quando você adicionar uma restrição, ela será exibida na superfície de design como uma linha azul quando você selecionar um item:

 [![](designer-auto-layout-images/image09.png "Visualizing Constraints")](designer-auto-layout-images/image09.png#lightbox)

Você pode selecionar uma restrição clicando em uma linha azul e editando os valores de restrição diretamente no painel de propriedades. Como alternativa, clicar duas vezes em uma linha azul abrirá um popover que permite que você edite os valores diretamente na superfície de design:

 [![](designer-auto-layout-images/image08.png "Editing Constraints")](designer-auto-layout-images/image08.png#lightbox)

## <a name="constraint-issues"></a>Problemas de restrição

Vários tipos de problemas podem surgir ao usar restrições:

- **Restrições conflitantes** – isso ocorre quando várias restrições forçam o elemento a ter valores conflitantes para um atributo e o mecanismo de restrição não pode reconcilá-los.
- **Itens** subrestritos — as propriedades de um elemento (local + tamanho) devem ser totalmente cobertas por seu conjunto de restrições e tamanhos intrínsecos para que as restrições sejam válidas. Se esses valores forem ambíguos, o item será considerado como sendo restringido.
- **Incorreta de quadro** – isso ocorre quando o quadro de um elemento e seu conjunto de restrições definem dois retângulos resultantes diferentes.

Esta seção elabora os três problemas listados acima e fornece detalhes sobre como tratá-los.

### <a name="conflicting-constraints"></a>Restrições conflitantes

As restrições conflitantes são marcadas em vermelho e têm um símbolo de aviso. Focalizar os símbolos de aviso traz um popover com informações sobre o conflito:

 [![](designer-auto-layout-images/image11.png "Conflicting Constraints warning")](designer-auto-layout-images/image11.png#lightbox)

### <a name="underconstrained-items"></a>Itens de restrição

Os itens subrestritos aparecem em laranja e disparam a aparência de um ícone de marcador laranja na barra de objetos do controlador de exibição:

 [![](designer-auto-layout-images/image02.png "Underconstrained items appear in orange")](designer-auto-layout-images/image02.png#lightbox)

Se você clicar nesse ícone de marcador, poderá obter informações sobre itens subrestringidos na cena e resolver os problemas totalmente restringindo-os ou removendo suas restrições, conforme ilustrado pela captura de tela abaixo:

 [![](designer-auto-layout-images/image10.png "Fixing Underconstrained Items")](designer-auto-layout-images/image10.png#lightbox)

### <a name="frame-misplacement"></a>Incorreta de quadro

O incorreto de quadro usa o mesmo código de cor que os itens de restrição. O item sempre será renderizado na superfície usando seu quadro nativo, mas, no caso de um esboço de quadro, um retângulo vermelho marcará o local em que o item terminará quando o aplicativo for executado, conforme ilustrado pela captura de tela abaixo:

 [![](designer-auto-layout-images/image05.png "Sample Frame Misplacement view")](designer-auto-layout-images/image05.png#lightbox)

Para resolver erros de local de quadro, selecione o botão **Atualizar quadros com base nas restrições** da barra de ferramentas restrições (botão da extrema direita):

 [![](designer-auto-layout-images/image03.png "Update Frames based on Constraints toolbar button")](designer-auto-layout-images/image03.png#lightbox)

Isso ajustará automaticamente o quadro do elemento para corresponder às posições definidas pelos controles.

<a name="modifying-in-code" />

## <a name="modifying-constraints-in-code"></a>Modificando restrições no código

Com base nos requisitos do seu aplicativo, pode haver ocasiões em que você precisa modificar uma restrição no código. Por exemplo, para redimensionar ou reposicionar o modo de exibição ao qual uma restrição está anexada, para alterar a prioridade de uma restrição ou desativar completamente uma restrição.

Para acessar uma restrição no código, primeiro você precisa expô-lo no designer do iOS fazendo o seguinte:

1. Crie a restrição como normal (usando qualquer um dos métodos listados acima).
2. No **Gerenciador de estrutura de tópicos do documento**, localize a restrição desejada e selecione-a:

    [![](designer-auto-layout-images/modify01.png "The Document Outline Explorer")](designer-auto-layout-images/modify01.png#lightbox)
3. Em seguida, atribua um **nome** à restrição na guia **widget** do Gerenciador de **Propriedades**:

    [![](designer-auto-layout-images/modify02.png "The Widget Tab")](designer-auto-layout-images/modify02.png#lightbox)
4. Salve suas alterações.

Com as alterações acima em vigor, você pode acessar a restrição no código e modificar suas propriedades. Por exemplo, você pode usar o seguinte para definir a altura do modo de exibição anexado como zero:

```csharp
ViewInfoHeight.Constant = 0;
```

Dada a seguinte configuração para a restrição no designer do iOS:

[![](designer-auto-layout-images/modify03.png "Editing a Constraint in the Property Explorer")](designer-auto-layout-images/modify03.png#lightbox)

### <a name="the-deferred-layout-pass"></a>A passagem de layout adiada

Em vez de atualizar instantaneamente a exibição anexada em resposta às alterações de restrição, o mecanismo de layout automático agenda um _passo de layout adiado_ para o futuro próximo. Durante essa passagem adiada, não apenas a restrição de exibição fornecida é atualizada, as restrições para cada exibição na hierarquia são recalculadas e atualizadas para ajustar o novo layout.

A qualquer momento, você pode agendar sua própria passagem de layout adiado chamando os métodos `SetNeedsLayout` ou `SetNeedsUpdateConstraints` da exibição pai.

A passagem de layout adiada consiste em duas passagens exclusivas por meio da hierarquia de exibição:

- **A passagem de atualização** dessa passagem, o mecanismo de layout automático percorre a hierarquia de exibição e invoca o método `UpdateViewConstraints` em todos os controladores de exibição e o método `UpdateConstraints` em todas as exibições.
- **A passagem de layout** novamente, o mecanismo de layout automático atravessa a hierarquia de exibição, mas, desta vez, invoca o método `ViewWillLayoutSubviews` em todos os controladores de exibição e o método `LayoutSubviews` em todas as exibições. O método `LayoutSubviews` atualiza a propriedade `Frame` de cada subexibição com o retângulo calculado pelo mecanismo de layout automático.

### <a name="animating-constraint-changes"></a>Animando alterações de restrição

Além de modificar as propriedades de restrição, você pode usar a animação de núcleo para animar as alterações nas restrições de uma exibição. Por exemplo:

```csharp
UIView.BeginAnimations("OpenInfo");
UIView.SetAnimationDuration(1.0f);
ViewInfoHeight.Constant = 237;
View.LayoutIfNeeded();

//Execute Animation
UIView.CommitAnimations();
```

A chave aqui é chamar o método `LayoutIfNeeded` da exibição pai dentro do bloco de animação. Isso informa ao modo de exibição para desenhar cada "frame" do local animado ou da alteração de tamanho. Sem essa linha, a exibição simplesmente se ajustaria à versão final sem animação.

## <a name="summary"></a>Resumo

Este guia introduziu o layout automático (ou "adaptável") do iOS e o conceito de restrições como representações matemáticas de relações entre elementos na superfície de design. Ele descreveu como habilitar o layout automático no designer do iOS, trabalhando com a **barra de ferramentas de restrições**e editando restrições individualmente na superfície de design. Em seguida, explicamos como solucionar problemas de três restrições comuns. Por fim, ele mostrou como modificar restrições no código.

## <a name="related-links"></a>Links relacionados

- [Introdução ao Storyboards](~/ios/user-interface/storyboards/index.md)
- [Instruções de controles designáveis do iOS](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Visão geral de Designer Android](~/android/user-interface/android-designer/index.md)
- [Restrições programáticas](~/ios/user-interface/programmatic-layout-constraints.md)
