---
title: Storyboards unificadas no xamarin. IOS
description: Este documento descreve storyboards unificadas no xamarin. IOS. Storyboards unificadas permitem aos desenvolvedores oferecer suporte a vários tamanhos de tela com a definição de uma única interface.
ms.prod: xamarin
ms.assetid: F6F70374-FC2A-4401-A712-A16D0F9B340F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 26aeaa3d230a5c104014edd899b8d9231ced31e9
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108516"
---
# <a name="unified-storyboards-in-xamarinios"></a>Storyboards unificadas no xamarin. IOS

iOS 8 inclui um mecanismo de novo, mais simples de usar para criar a interface do usuário — o storyboard unificado. Com um storyboard único para abranger todos os tamanhos de tela de um hardware diferente, rápidas e responsivos exibições podem ser criadas em um "design-uma vez, use-muitos" estilo.

Como o desenvolvedor não precisa mais criar um storyboard separado e específico para dispositivos iPhone e iPad, eles têm a flexibilidade para criar aplicativos com uma interface comum e, em seguida, personalizar essa interface para classes de tamanho diferente. Dessa forma, um aplicativo pode ser adaptado para os pontos fortes de cada fator forma e cada interface de usuário pode ser ajustada para fornecer a melhor experiência.

<a name="size-classes" />

## <a name="size-classes"></a>Classes de tamanho

Antes do iOS 8, o desenvolvedor usado `UIInterfaceOrientation` e `UIInterfaceIdiom` para diferenciar entre os modos retrato e paisagem e entre os dispositivos iPhone e iPad. No iOS8, orientação e o dispositivo é determinada usando *Classes de tamanho*.

Dispositivos que são definidos pelas Classes de tamanho, em eixos horizontais e verticais, e há dois tipos de classes de tamanho no iOS 8:

-  **Regular** – isso é para um tamanho de tela grande (como um iPad) ou um gadget que dá a impressão de um tamanho grande (como um `UIScrollView`
-  **Compact** – isso é para dispositivos menores (por exemplo, um iPhone). Esse tamanho leva em consideração a orientação do dispositivo.


Se os dois conceitos são usados juntos, o resultado é uma grade de 2 x 2 que define os diferentes tamanhos de possíveis que podem ser usados em ambos os as orientações diferentes, como visto no diagrama a seguir:

 [![](unified-storyboards-images/sizeclassgrid.png "Uma grade de 2 x 2 que define os diferentes tamanhos de possíveis que podem ser usados em orientações normais e de CD")](unified-storyboards-images/sizeclassgrid.png#lightbox)

O desenvolvedor pode criar um controlador de exibição que usa qualquer um dos quatro possibilidades que resultariam em layouts diferentes (como visto nos gráficos acima).

### <a name="ipad-size-classes"></a>Classes de tamanho do iPad

O iPad, devido ao tamanho, tem um **regulares** tamanho para ambas as orientações de classe.

 [![](unified-storyboards-images/image1.png "Classes de tamanho do iPad")](unified-storyboards-images/image1.png#lightbox)


### <a name="iphone-size-classes"></a>Classes de tamanho do iPhone

O iPhone tem classes de tamanho diferente com base na orientação do dispositivo:

 [![](unified-storyboards-images/iphonesizeclasses.png "Classes de tamanho do iPhone")](unified-storyboards-images/iphonesizeclasses.png#lightbox)

-  Quando o dispositivo estiver no modo retrato, a tela tem um **compact** classe horizontalmente e **regulares** verticalmente
-  Quando o dispositivo estiver no modo paisagem, as classes de tela são revertidas de modo retrato.

### <a name="iphone-6-plus-size-classes"></a>Classes de tamanho além iPhone 6

Os tamanhos são o mesmo que a iPhones anteriores quando na orientação retrato, mas diferentes no cenário:

[![](unified-storyboards-images/iphone6sizeclasses.png "Classes de tamanho além iPhone 6")](unified-storyboards-images/iphone6sizeclasses.png#lightbox)

Porque o iPhone 6 Plus tem uma tela grande o suficiente, é capaz de ter uma classe de tamanho Regular largura no modo paisagem.

### <a name="support-for-a-new-screen-scale"></a>Suporte para uma nova tela de escala

O iPhone 6 Plus usa uma nova tela de Retina HD com um fator de escala da tela de 3.0 (três vezes a original iPhone resolução da tela). Para fornecer a melhor experiência possível nesses dispositivos, inclua arte novo projetada para essa escala da tela. No Xcode 6 e superior, o ativo de catálogos podem incluir imagens em 1 x, 2 x e tamanhos de x 3; Basta adicionar os novos ativos de imagem e o iOS escolherá os ativos corretos quando em execução em um iPhone 6 Plus.

A imagem de comportamento no iOS de carregamento também reconhece um `@3x` sufixo em arquivos de imagem. Por exemplo, se o desenvolvedor inclui um ativo de imagem (com resoluções diferentes) no pacote do aplicativo com os seguintes nomes de arquivo: `MonkeyIcon.png`, `MonkeyIcon@2x.png`, e `MonkeyIcon@3x.png`. No iPhone 6 Plus o `MonkeyIcon@3x.png` imagem será usada automaticamente se o desenvolvedor carrega uma imagem usando o seguinte código:

```csharp
UIImage icon = UIImage.FromFile("MonkeyImage.png");
```
Ou se eles atribuem a imagem a um elemento de interface do usuário usando o iOS Designer como `MonkeyIcon.png`, o `MonkeyIcon@3x.png` será usado, automaticamente, no iPhone 6 Plus.

<a name="dynamic-launch-screens" />

### <a name="dynamic-launch-screens"></a>Telas de inicialização dinâmica

O arquivo de tela de inicialização é exibido como uma tela inicial, enquanto um aplicativo iOS está sendo lançada para fornecer comentários ao usuário que o aplicativo é, na verdade, iniciando-up. Antes do iOS 8, o desenvolvedor teria que incluem vários `Default.png` ativos para cada dispositivo tipo, orientação e resolução de tela que o aplicativo seria executado em de imagem.

Novo para o iOS 8, o desenvolvedor pode criar um único atômico `.xib` arquivo no Xcode que usa Classes de tamanho e o Layout automático para criar um *tela de inicialização dinâmica* que funcionará para cada dispositivo, resolução e orientação. Isso não só reduz a quantidade de trabalho necessário do desenvolvedor para criar e manter todos os ativos de imagem necessário, mas reduz o tamanho do pacote de instaladas do aplicativo.

## <a name="traits"></a>Características

Atributos são propriedades que podem ser usadas para determinar como um layout muda à medida que as alterações de seu ambiente. Eles consistem em um conjunto de propriedades (o `HorizontalSizeClass` e `VerticalSizeClass` com base em `UIUserInterfaceSizeClass`), bem como o idioma de interface ( `UIUserInterfaceIdiom`) e a escala de exibição.

Todos os estados acima são encapsulados em um contêiner que Apple se refere a como uma coleção de característica de ( `UITraitCollection`), que contém não apenas as propriedades, mas seus valores também.

## <a name="trait-environment"></a>Ambiente de característica

Ambientes de característica são uma nova interface no iOS 8 e são capazes de retornar uma coleção de característica para os seguintes objetos:

-  Telas ( `UIScreens` ).
-  Windows ( `UIWindows` ).
-  Exibir controladores ( `UIViewController` ).
-  Modos de exibição ( `UIView` ).
-  Controlador de apresentação ( `UIPresentationController` ).


O desenvolvedor usa a coleção de característica retornada por um ambiente de característica para determinar como uma interface do usuário deve ser disposta.

Todos os ambientes de característica de tornar uma hierarquia, como visto no diagrama a seguir:

 [![](unified-storyboards-images/viewhierarchy.png "O diagrama de hierarquia de ambientes de característica")](unified-storyboards-images/viewhierarchy.png#lightbox)

A coleção de característica que cada um dos ambientes de característica acima tem fará o fluxo, por padrão, o ambiente do filho do pai.

Além de obter a coleção de característica atual, o ambiente de característica tem um `TraitCollectionDidChange` método, que pode ser substituído nas subclasses de modo de exibição ou controlador de exibição. O desenvolvedor pode usar esse método para modificar qualquer um dos elementos da interface do usuário que dependem de características quando essas características foram alterados.

## <a name="typical-trait-collections"></a>Coleções de característica típico

Esta seção abordará os tipos comuns de coleções de característica que o usuário terá ao trabalhar com iOS 8.

A seguir é uma coleção de característica comum que o desenvolvedor pode ver em um iPhone:

|Propriedade|Valor|
|--- |--- |
|`HorizontalSizeClass`|Compactar|
|`VerticalSizeClass`|Normal|
|`UserInterfaceIdom`|Telefone|
|`DisplayScale`|2.0|

O conjunto acima representaria uma totalmente característica coleção qualificado, pois ela tem valores para todas as suas propriedades de característica.

Também é possível ter uma coleção de característica que faltam alguns de seus valores (que Apple se refere a como *Unspecified*):

|Propriedade|Valor|
|--- |--- |
|`HorizontalSizeClass`|Compactar|
|`VerticalSizeClass`|Não especificado|
|`UserInterfaceIdom`|Não especificado|
|`DisplayScale`|Não especificado|

Em geral, no entanto, quando o desenvolvedor solicita o ambiente de característica de sua coleção de característica, ele retornará uma coleção totalmente qualificada como visto no exemplo acima.

Se um ambiente de característica (como uma exibição ou controlador de exibição) não está dentro da hierarquia de exibição atual, o desenvolvedor pode voltar valores não especificados para uma ou mais das propriedades de característica.

O desenvolvedor também terá uma coleção de característica parcialmente qualificado se eles usam um dos métodos de criação fornecidos pela Apple, como `UITraitCollection.FromHorizontalSizeClass`, para criar uma nova coleção.

Uma operação que pode ser executada em várias coleções de característica é comparando--los uns aos outros, que envolve solicitando uma característica de coleção se ele contém o outro. O que significa *confinamento* é que, para qualquer característica especificada na segunda coleção, o valor deve corresponder exatamente com o valor na primeira coleção.

Para testar duas características, use o `Contains` método da `UITraitCollection` passando o valor da característica a ser testado.

O desenvolvedor pode realizar as comparações manualmente no código para determinar como modos de exibição de layout ou controladores de exibição. No entanto, `UIKit` usa esse método internamente para fornecer algumas das suas funcionalidades, como o Proxy de aparência, por exemplo.

## <a name="appearance-proxy"></a>Proxy de aparência

O Proxy de aparência foi introduzido em versões anteriores do iOS para permitir que os desenvolvedores personalizar as propriedades de suas exibições. Ele foi estendido no iOS 8 para dar suporte a coleções de característica.

Proxies de aparência agora incluem um novo método, `AppearanceForTraitCollection`, que retorna um novo Proxy de aparência para a determinada coleção de característica que foi passado. Todas as personalizações que o desenvolvedor realiza em que aparência Proxy só terão efeito em exibições que estão em conformidade com a coleção especificada de característica.

Geralmente, o desenvolvedor passará em uma coleção de característica parcialmente especificado para o `AppearanceForTraitCollection` método, como aquele que acabou de especificar um Horizontal tamanho da classe do Compact, para que eles podem personalizar qualquer modo de exibição no aplicativo que é compacto horizontalmente.

## <a name="uiimage"></a>UIImage

Outra classe de Apple adicionou a característica de coleção a ser se `UIImage`. No passado, o desenvolvedor tinha que especificar uma @1X e @2x versão de qualquer ativo de gráficos de bitmap que eles estavam indo para incluir no aplicativo (por exemplo, um ícone).

iOS 8 foi expandido para permitir que o desenvolvedor incluir várias versões de uma imagem em um catálogo de imagens com base em uma coleção de característica. Por exemplo, o desenvolvedor pode incluir uma imagem menor para trabalhar com uma classe de característica Compact e uma imagem completa de um tamanho para qualquer outra coleção.

Quando uma das imagens é usada dentro de um `UIImageView` classe, o modo de exibição de imagem exibirá automaticamente a versão correta da imagem para sua coleção de característica. Se o ambiente de característica for alterado (por exemplo, o usuário alternar o dispositivo de retrato para paisagem), o modo de exibição de imagem selecionará automaticamente o novo tamanho da imagem para coincidir com a nova coleção de característica e alterar seu tamanho para coincidir com a versão atual da imagem que está sendo exibido.

## <a name="uiimageasset"></a>UIImageAsset

Apple adicionou uma nova classe para o iOS 8 chamada `UIImageAsset` para dar ao desenvolvedor ainda mais controle sobre a seleção de imagem.

Um ativo de imagem conclui todas as versões diferentes de uma imagem e permite que o desenvolvedor pedir uma imagem específica que corresponde a uma coleção de característica que foi passado. Podem ser adicionadas ou removidas de um ativo de imagem, imagens do sistema em funcionamento.

Para obter mais informações sobre ativos de imagem, consulte da Apple [UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset) documentação.

## <a name="combining-trait-collections"></a>Combinando coleções de característica

Outra função que um desenvolvedor pode executar em coleções de característica é somar dois que resultam na coleção combinada, em que os valores não especificados de uma coleção são substituídos pelos valores especificados em um segundo. Isso é feito usando o `FromTraitsFromCollections` método da `UITraitCollection` classe.

Como mencionado acima, se qualquer uma das características não é especificado em uma das coleções de característica e é especificado em outro, o valor será definido para a versão especificada. No entanto, se houver várias versões de um determinado valor especificado, o valor da última coleção de característica será o valor que é usado.

## <a name="adaptive-view-controllers"></a>Controladores de exibição adaptável

Esta seção abordará os detalhes de como o modo de exibição e controladores de exibição do iOS adotaram os conceitos de Classes de tamanho e características automaticamente ser mais adaptável em aplicativos do desenvolvedor.

### <a name="split-view-controller"></a>Controlador de exibição de divisão

Uma das classes do controlador de exibição que foi alterado no iOS 8 é o `UISplitViewController` classe. No passado, o desenvolvedor geralmente usa um controlador de exibição de divisão na versão do aplicativo de iPad e, em seguida, eles teriam de fornecer uma versão completamente diferente da sua hierarquia de exibição para a versão do aplicativo para iPhone.

No iOS 8, o `UISplitViewController` classe está disponível em ambas as plataformas (iPhone e iPad), que permite ao desenvolvedor criar uma hierarquia de controlador de exibição que funcionará para iPhone e iPad.

Quando um iPhone está em paisagem, o controlador de exibição de divisão apresentará seu modos de exibição lado a lado, como faria ao que está sendo exibido em um iPad.

### <a name="overriding-traits"></a>Características de substituição

Ambientes de característica em cascata do recipiente pai até os contêineres filho, como no gráfico a seguir, mostrando um controlador de exibição de divisão em um iPad na orientação paisagem:

 [![](unified-storyboards-images/cascadingclasses01.png "Um controlador de exibição de divisão em um iPad na orientação paisagem")](unified-storyboards-images/cascadingclasses01.png#lightbox)

Como o iPad tem uma classe de tamanho Regular em ambas as orientações horizontais e verticais, o modo divisão exibirá os modos de exibição mestre e de detalhes.

Em um iPhone, em que a classe de tamanho é compacta em ambas as orientações, o controlador de exibição de divisão exibe apenas a exibição de detalhes, conforme mostrado abaixo:

 [![](unified-storyboards-images/cascadingclasses02.png "O controlador de exibição de divisão exibe apenas a exibição de detalhes")](unified-storyboards-images/cascadingclasses02.png#lightbox)

Em um aplicativo em que o desenvolvedor deseja exibir o modo de exibição mestre e de detalhes em um iPhone na orientação paisagem, o desenvolvedor deve inserir um contêiner pai para o controlador de exibição de divisão e substituir a coleção de característica. Conforme mostrado na imagem abaixo:

 [![](unified-storyboards-images/cascadingclasses03.png "O desenvolvedor deve inserir um contêiner pai para o controlador de exibição de divisão e substituir a coleção de característica")](unified-storyboards-images/cascadingclasses03.png#lightbox)

Um `UIView` é definido como o pai do controlador de exibição de divisão e o `SetOverrideTraitCollection` método é chamado no modo de exibição, passando uma nova coleção de característica e direcionando o controlador de exibição de divisão. A nova coleção de característica substitui o `HorizontalSizeClass`, configurando-a como `Regular`, de modo que o controlador de exibição de divisão exibirá os modos de exibição mestre e de detalhes em um iPhone na orientação paisagem.

Observe que o `VerticalSizeClass` foi definida como `unspecified`, que permite que a nova coleção de característica a ser adicionado à coleção de característica no pai, resultando em um `Compact VerticalSizeClass` para o controlador de exibição de divisão de filho.

### <a name="trait-changes"></a>Alterações de característica

Esta seção apresentará, em detalhes, como as coleções de característica de transição quando o ambiente de característica é alterado. Por exemplo, quando o dispositivo for girado de retrato para paisagem.

 [![](unified-storyboards-images/traittransitions01.png "O retrato para paisagem visão geral de alterações de característica")](unified-storyboards-images/traittransitions01.png#lightbox)

Primeiro, o iOS 8 faz algumas configurações para se preparar para a transição entrar em vigor. Em seguida, o sistema anima a transição de estado. Por fim, o iOS 8 limpa qualquer necessárias durante a transição de estados temporários.

iOS 8 fornece vários retornos de chamada que o desenvolvedor pode usar para participar da alteração de característica, conforme mostrado na tabela a seguir:

|Fase|retorno de chamada|Descrição|
|--- |--- |--- |
|Configuração|<ul><li>`WillTransitionToTraitCollection`</li><li>`TraitCollectionDidChange`</li></ul>|<ul><li>Esse método é chamado no início de uma alteração de característica antes de uma coleção de característica é definida para o novo valor.</li><li>O método é chamado quando o valor da característica de coleção foi alterada, mas antes que qualquer animação que ocorra.</li></ul>|
|Animação|`WillTransitionToTraitCollection`|O coordenador de transição que é passado para esse método tem um `AnimateAlongside` propriedade que permite que o desenvolvedor adicione animações que serão executadas junto com as animações de padrão.|
|Limpeza|`WillTransitionToTraitCollection`|Fornece um método para os desenvolvedores incluam seu próprio código de limpeza após a transição ocorre.|

O `WillTransitionToTraitCollection` método é ótimo para controladores de exibição de animação, juntamente com as alterações da coleção de característica. O `WillTransitionToTraitCollection` método só está disponível nos controladores de exibição ( `UIViewController`) e não em outros ambientes de característica, como `UIViews`.

O `TraitCollectionDidChange` é ótimo para trabalhar com o `UIView` classe, em que o desenvolvedor deseja atualizar a interface do usuário, como as características estão mudando.

### <a name="collapsing-the-split-view-controllers"></a>Os controladores de exibição de divisão de recolhimento

Agora vamos dar uma olhada mais próxima no que acontece quando um controlador de exibição de divisão recolhe um duas colunas para uma exibição de uma coluna. Como parte dessa alteração, há dois processos que precisam ocorrer:

-  Por padrão, o controlador de exibição de divisão será usar o controlador de exibição primário como o modo de exibição após o recolhimento. O desenvolvedor pode substituir esse comportamento, substituindo o `GetPrimaryViewControllerForCollapsingSplitViewController` método da `UISplitViewControllerDelegate` e fornecendo qualquer controlador de exibição que deseja exibir no estado recolhido.
-  O controlador de exibição secundário tem a obter mesclado com o controlador de exibição primário. Geralmente, o desenvolvedor não precisa realizar nenhuma ação para esta etapa; o controlador de exibição de divisão inclui a manipulação automática dessa fase com base no dispositivo de hardware. No entanto, pode haver alguns casos especiais onde o desenvolvedor deve interagir com essa alteração. Chamar o `CollapseSecondViewController` método da `UISplitViewControllerDelegate` permite que o controlador de exibição mestre a ser exibida quando o recolhimento ocorrerá, em vez da exibição de detalhes.


### <a name="expanding-the-split-view-controller"></a>Expandindo o controlador de exibição de divisão

Agora vamos dar uma olhada mais próxima no que acontece quando um controlador de exibição de divisão for expandido de um estado recolhido. Mais uma vez, há duas etapas que precisam ocorrer:

-  Primeiro, defina o novo controlador de exibição primário. Por padrão, o controlador de exibição de divisão automaticamente usará o controlador de exibição principal do modo de exibição recolhido. Novamente, o desenvolvedor pode substituir esse comportamento usando o `GetPrimaryViewControllerForExpandingSplitViewController` método da `UISplitViewControllerDelegate` .
-  Depois que o controlador de exibição primário foi escolhido, o controlador de exibição secundária devem ser recriado. Novamente, o controlador de exibição de divisão inclui a manipulação automática dessa fase com base no dispositivo de hardware. O desenvolvedor pode substituir esse comportamento, chamando o `SeparateSecondaryViewController` método da `UISplitViewControllerDelegate` .


Em um controlador de exibição de divisão, o controlador de exibição primário desempenha um papel na expansão e recolhimento dos modos de exibição com a implementação de `CollapseSecondViewController` e `SeparateSecondaryViewController` métodos do `UISplitViewControllerDelegate`. `UINavigationController` implementa esses métodos para enviar por push e pop o controlador de exibição secundário automaticamente.

### <a name="showing-view-controllers"></a>Controladores de exibição mostrando

Outra alteração que fez Apple IOS 8 é da forma que o desenvolvedor mostra controladores de exibição. No passado, se o aplicativo tinha um controlador de exibição de folha (por exemplo, um controlador de exibição de tabela) e o desenvolvedor mostrou um diferente (por exemplo, em resposta a um toque de usuário em uma célula), alcançam o aplicativo por meio da hierarquia de controlador para o Controlador de exibição de navegação e chamar o `PushViewController` método nele para exibir o novo modo de exibição.

Isso apresentado um acoplamento entre o controlador de navegação e o que ele estava em execução no ambiente. No iOS 8, Apple desacoplou isso fornecendo dois novos métodos:

-  `ShowViewController` – Se adapta para exibir o novo controlador de exibição com base em seu ambiente. Por exemplo, em um `UINavigationController` ele simplesmente empurra o novo modo de exibição para a pilha. Em um controlador de exibição de divisão, o novo controlador de exibição será apresentado no lado esquerdo como o novo controlador de exibição primário. Se nenhum controlador de exibição do contêiner estiver presente, o novo modo de exibição será exibido como um controlador de exibição modais.
-  `ShowDetailViewController` – Funciona de maneira semelhante ao `ShowViewController`, mas é implementado em um controlador de exibição de divisão para substituir o modo de exibição de detalhes com o novo controlador de exibição que está sendo passado. Se o controlador de exibição de divisão é recolhido (como pode ser visto em um aplicativo de iPhone), a chamada será redirecionada para o `ShowViewController` método e o novo modo de exibição serão mostrado como o controlador de exibição primário. Novamente, se nenhum controlador de exibição do contêiner estiver presente, o novo modo de exibição será exibido como um controlador de exibição modais.


Esses métodos funcionam, começando com o controlador de exibição da folha e percorrer a hierarquia de exibição até que ele encontrar o controlador de exibição do contêiner correto para manipular a exibição da nova exibição.

Os desenvolvedores podem implementar `ShowViewController` e `ShowDetailViewController` em seus próprios controladores de exibição personalizado para obter o mesmo automatizada de funcionalidade que `UINavigationController` e `UISplitViewController` fornece.

### <a name="how-it-works"></a>Como ele funciona

Nesta seção, vamos será dar uma olhada em como esses métodos são realmente implementados no iOS 8. Primeiro vamos dar uma olhada no novo `GetTargetForAction` método:

 [![](unified-storyboards-images/gettargetforaction.png "O novo método GetTargetForAction")](unified-storyboards-images/gettargetforaction.png#lightbox)

Esse método percorre a cadeia de hierarquia até que o controlador de exibição do contêiner correto seja encontrado. Por exemplo:

1.  Se um `ShowViewController` método é chamado, o primeiro controlador de exibição da cadeia que implementa este método é o controlador de navegação, para que ele é usado como o pai do novo modo de exibição.
1.  Se um `ShowDetailViewController` método foi chamado em vez disso, o controlador de exibição de divisão é o primeiro controlador de exibição de implementá-la, para que ele é usado como o pai.


O `GetTargetForAction` método opera Localizando um controlador de exibição que implementa uma determinada ação e, em seguida, solicitando que esse controlador de exibição se desejam receber essa ação. Como esse método é público, os desenvolvedores podem criar seus próprios métodos personalizados que funcionam exatamente como internos em `ShowViewController` e `ShowDetailViewController` métodos.

## <a name="adaptive-presentation"></a>Apresentação adaptável

No iOS 8, a Apple fez apresentações de pop-over ( `UIPopoverPresentationController`) adaptável também. Portanto, um controlador de exibição de apresentação de pop-over automaticamente apresentará uma exibição pop-over normal em uma classe de tamanho Regular, mas ele exibir tela inteira em uma classe de tamanho compacto horizontalmente (como em um iPhone).

Para acomodar as alterações no sistema unificado de storyboard, um novo objeto de controlador foi criado para gerenciar os controladores de exibição apresentada — `UIPresentationController`. Esse controlador é criado desde o momento em que o controlador de exibição é apresentado até que ele seja liberado. Como ele é uma classe de gerenciamento, ele pode ser considerado uma superclasse sobre o controlador de exibição como ele responde às alterações de dispositivo que afetam o controlador de exibição (como orientação) que serão então back alimentado no controlador de exibição controla o controlador de apresentação.

Quando o desenvolvedor apresenta um controlador de exibição usando o `PresentViewController` método, o gerenciamento do processo de apresentação é entregue à `UIKit`. UIKit manipula (entre outras coisas), o controlador correto para o estilo que está sendo criado, com a única exceção quando um controlador de exibição tem o estilo definido como `UIModalPresentationCustom`. Aqui, o aplicativo pode fornecer é o próprio PresentationController em vez de usar o `UIKit` controlador.

### <a name="custom-presentation-styles"></a>Estilos de apresentação personalizada

Com um estilo de apresentação personalizada, os desenvolvedores têm a opção de usar um controlador de apresentação personalizada. Esse controlador personalizado pode ser usado para modificar a aparência e comportamento do modo de exibição, ele é allied para.

<a name="size-classes"/>

## <a name="working-with-size-classes"></a>Trabalhando com Classes de tamanho

O projeto do Xamarin fotos adaptável que acompanha este artigo fornece um exemplo prático de como usar Classes de tamanho e adaptável controladores de exibição em um aplicativo de Interface unificada do iOS 8.

Enquanto o aplicativo cria sua interface do usuário completamente do código, em vez de usar o Designer do IOS e criar um Storyboard Unificação, as mesmas técnicas se aplicam. Neste artigo, mostraremos como usar as Classes de tamanho com um Storyboard unificado e o iOS Designer em um aplicativo Xamarin.

Agora vamos examinar mais detalhadamente como o projeto de fotos adaptável é implementar vários recursos de classe de tamanho no iOS 8 para criar um aplicativo adaptável.

### <a name="adapting-to-trait-environment-changes"></a>Adaptar a alterações de ambiente de característica

Ao executar o aplicativo de fotos adaptável em um iPhone, quando o usuário gira o dispositivo de retrato para paisagem, o controlador de exibição de divisão exibirá o modo de exibição mestre e de detalhes:

 [![](unified-storyboards-images/rotation.png "O controlador de exibição de divisão exibirá os dois o mestre e exibição de detalhes, como visto aqui")](unified-storyboards-images/rotation.png#lightbox)

Isso é feito substituindo o `UpdateConstraintsForTraitCollection` método de controlador de exibição e ajuste as restrições com base no valor da `VerticalSizeClass`. Por exemplo:

```csharp
public void UpdateConstraintsForTraitCollection (UITraitCollection collection)
{
    var views = NSDictionary.FromObjectsAndKeys (
        new object[] { TopLayoutGuide, ImageView, NameLabel, ConversationsLabel, PhotosLabel },
        new object[] { "topLayoutGuide", "imageView", "nameLabel", "conversationsLabel", "photosLabel" }
    );

    var newConstraints = new List<NSLayoutConstraint> ();
    if (collection.VerticalSizeClass == UIUserInterfaceSizeClass.Compact) {
        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|[imageView]-[nameLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("[imageView]-[conversationsLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("[imageView]-[photosLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("V:|[topLayoutGuide]-[nameLabel]-[conversationsLabel]-[photosLabel]",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("V:|[topLayoutGuide][imageView]|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.Add (NSLayoutConstraint.Create (ImageView, NSLayoutAttribute.Width, NSLayoutRelation.Equal,
            View, NSLayoutAttribute.Width, 0.5f, 0.0f));
    } else {
        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|[imageView]|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|-[nameLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|-[conversationsLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|-[photosLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("V:[topLayoutGuide]-[nameLabel]-[conversationsLabel]-[photosLabel]-20-[imageView]|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));
    }

    if (constraints != null)
        View.RemoveConstraints (constraints.ToArray ());

    constraints = newConstraints;
    View.AddConstraints (constraints.ToArray ());
}
```

### <a name="adding-transition-animations"></a>Adicionar animações de transição

Quando o controlador de exibição de divisão em fotos adaptável aplicativo vai de recolhidos para expandido, animações são adicionadas para as animações padrão, substituindo o `WillTransitionToTraitCollection` método do controlador de exibição. Por exemplo:

```csharp
public override void WillTransitionToTraitCollection (UITraitCollection traitCollection, IUIViewControllerTransitionCoordinator coordinator)
{
    base.WillTransitionToTraitCollection (traitCollection, coordinator);
    coordinator.AnimateAlongsideTransition ((UIViewControllerTransitionCoordinatorContext) => {
        UpdateConstraintsForTraitCollection (traitCollection);
        View.SetNeedsLayout ();
    }, (UIViewControllerTransitionCoordinatorContext) => {
    });
}
```

### <a name="overriding-the-trait-environment"></a>Substituindo o ambiente de característica

Como mostrado acima, o aplicativo de fotos adaptável força o controlador de exibição de divisão para exibir os detalhes e os modos de exibição mestres quando o dispositivo iPhone estiver no modo de exibição de paisagem.

Isso foi feito usando o código a seguir no controlador de exibição:

```csharp
private UITraitCollection forcedTraitCollection = new UITraitCollection ();
...

public UITraitCollection ForcedTraitCollection {
    get {
        return forcedTraitCollection;
    }

    set {
        if (value != forcedTraitCollection) {
            forcedTraitCollection = value;
            UpdateForcedTraitCollection ();
        }
    }
}
...

public override void ViewWillTransitionToSize (SizeF toSize, IUIViewControllerTransitionCoordinator coordinator)
{
    ForcedTraitCollection = toSize.Width > 320.0f ?
         UITraitCollection.FromHorizontalSizeClass (UIUserInterfaceSizeClass.Regular) :
         new UITraitCollection ();

    base.ViewWillTransitionToSize (toSize, coordinator);
}

public void UpdateForcedTraitCollection ()
{
    SetOverrideTraitCollection (forcedTraitCollection, viewController);
}
```

### <a name="expanding-and-collapsing-the-split-view-controller"></a>Expandindo e recolhendo o controlador de exibição de divisão

Próxima vamos examinar como o comportamento de expansão e recolhimento do controlador de exibição de divisão foi implementado no Xamarin. No `AppDelegate`, quando o controlador de exibição de divisão é criado, seu delegado é atribuído para lidar com essas alterações:

```csharp
public class SplitViewControllerDelegate : UISplitViewControllerDelegate
{
    public override bool CollapseSecondViewController (UISplitViewController splitViewController,
        UIViewController secondaryViewController, UIViewController primaryViewController)
    {
        AAPLPhoto photo = ((CustomViewController)secondaryViewController).Aapl_containedPhoto (null);
        if (photo == null) {
            return true;
        }

        if (primaryViewController.GetType () == typeof(CustomNavigationController)) {
            var viewControllers = new List<UIViewController> ();
            foreach (var controller in ((UINavigationController)primaryViewController).ViewControllers) {
                var type = controller.GetType ();
                MethodInfo method = type.GetMethod ("Aapl_containsPhoto");

                if ((bool)method.Invoke (controller, new object[] { null })) {
                    viewControllers.Add (controller);
                }
            }

            ((UINavigationController)primaryViewController).ViewControllers = viewControllers.ToArray<UIViewController> ();
        }

        return false;
    }

    public override UIViewController SeparateSecondaryViewController (UISplitViewController splitViewController,
        UIViewController primaryViewController)
    {
        if (primaryViewController.GetType () == typeof(CustomNavigationController)) {
            foreach (var controller in ((CustomNavigationController)primaryViewController).ViewControllers) {
                var type = controller.GetType ();
                MethodInfo method = type.GetMethod ("Aapl_containedPhoto");

                if (method.Invoke (controller, new object[] { null }) != null) {
                    return null;
                }
            }
        }

        return new AAPLEmptyViewController ();
    }
}
```

O `SeparateSecondaryViewController` método testes para ver se uma foto está sendo exibida e age com base no estado. Se nenhuma foto é que está sendo mostrado ele recolhe o controlador de exibição secundária para que o controlador de exibição mestre seja exibido.

O `CollapseSecondViewController` método é usado ao expandir o controlador de exibição de divisão para ver se qualquer fotos existirem na pilha, se assim ele recolhe volta para esse modo de exibição.

### <a name="moving-between-view-controllers"></a>Movendo entre controladores de exibição

Em seguida, vamos dar uma olhada em como o aplicativo de fotos adaptável se move entre controladores de exibição. No `AAPLConversationViewController` classe quando o usuário seleciona uma célula da tabela, o `ShowDetailViewController` método é chamado para mostrar a exibição de detalhes:

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    var photo = PhotoForIndexPath (indexPath);
    var controller = new AAPLPhotoViewController ();
    controller.Photo = photo;

    int photoNumber = indexPath.Row + 1;
    int photoCount = (int)Conversation.Photos.Count;
    controller.Title = string.Format ("{0} of {1}", photoNumber, photoCount);
    ShowDetailViewController (controller, this);
}
```

### <a name="displaying-disclosure-indicators"></a>Exibir indicadores de divulgação

No aplicativo fotos adaptável, existem diversos lugares em que os indicadores de divulgação são ocultas ou mostrados com base nas alterações no ambiente de característica. Isso é manipulado pelo código a seguir:

```csharp
public bool Aapl_willShowingViewControllerPushWithSender ()
{
    var selector = new Selector ("Aapl_willShowingViewControllerPushWithSender");
    var target = this.GetTargetViewControllerForAction (selector, this);

    if (target != null) {
        var type = target.GetType ();
        MethodInfo method = type.GetMethod ("Aapl_willShowingDetailViewControllerPushWithSender");
        return (bool)method.Invoke (target, new object[] { });
    } else {
        return false;
    }
}

public bool Aapl_willShowingDetailViewControllerPushWithSender ()
{
    var selector = new Selector ("Aapl_willShowingDetailViewControllerPushWithSender");
    var target = this.GetTargetViewControllerForAction (selector, this);

    if (target != null) {
        var type = target.GetType ();
        MethodInfo method = type.GetMethod ("Aapl_willShowingDetailViewControllerPushWithSender");
        return (bool)method.Invoke (target, new object[] { });
    } else {
        return false;
    }
}
```

Eles são implementados usando o `GetTargetViewControllerForAction` método discutido detalhadamente acima.

Quando um controlador de exibição de tabela está exibindo dados, ele usa os métodos implementados acima para ver ou não um envio por push vai acontecer e se deseja ou não exibir ou ocultar o indicador de divulgação de acordo:

```csharp
public override void WillDisplay (UITableView tableView, UITableViewCell cell, NSIndexPath indexPath)
{
    bool pushes = ShouldShowConversationViewForIndexPath (indexPath) ?
         Aapl_willShowingViewControllerPushWithSender () :
         Aapl_willShowingDetailViewControllerPushWithSender ();

    cell.Accessory = pushes ? UITableViewCellAccessory.DisclosureIndicator : UITableViewCellAccessory.None;
    var conversation = ConversationForIndexPath (indexPath);
    cell.TextLabel.Text = conversation.Name;
}
```

### <a name="new-showdetailtargetdidchangenotification-type"></a>Novo `ShowDetailTargetDidChangeNotification` tipo

Apple tiver adicionado um novo tipo de notificação para trabalhar com Classes de tamanho e ambientes de característica de dentro de um controlador de exibição de divisão, `ShowDetailTargetDidChangeNotification`. Essa notificação é enviada sempre que o destino de exibição de detalhes para um controlador de exibição de divisão é alterado, como quando o controlador expande ou recolhe.

O aplicativo de fotos adaptável usa essa notificação para atualizar o estado do indicador de divulgação de quando o controlador de exibição de detalhes é alterado:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    TableView.RegisterClassForCellReuse (typeof(UITableViewCell), AAPLListTableViewControllerCellIdentifier);
    NSNotificationCenter.DefaultCenter.AddObserver (this, new Selector ("showDetailTargetDidChange:"),
        UIViewController.ShowDetailTargetDidChangeNotification, null);
    ClearsSelectionOnViewWillAppear = false;
}
```

Examinar um perto o aplicativo de fotos adaptável para ver todas as maneiras em que as Classes de tamanho, coleções de característica e adaptável controladores de exibição podem ser usado para criar facilmente um aplicativo unificado no xamarin. IOS.

## <a name="unified-storyboards"></a>Storyboards unificadas

Novo para iOS 8, Storyboards unificados permitem ao desenvolvedor criar um, unificado que pode ser exibido em dispositivos iPhone e iPad direcionando várias Classes de tamanho do arquivo do storyboard. Ao usar Storyboards unificados, o desenvolvedor escreve menos código específico da interface do usuário e tem apenas uma interface design para criar e manter.

Os principais benefícios dos Storyboards unificados são:

-  Use o mesmo arquivo de storyboard para iPhone e iPad.
-  Implante com versões anteriores ao iOS 6 e iOS 7.
-  Visualize o layout para diferentes dispositivos, orientações e versões do sistema operacional tudo a partir de dentro do Designer do iOS Xamarin.

Esse recurso tem suporte total no Visual Studio para Mac

<a name="enabling-size-classes" />

### <a name="enabling-size-classes"></a>Habilitar Classes de tamanho

Por padrão, qualquer novo projeto do xamarin. IOS será nos classes de tamanho. Para usar as Classes de tamanho e adaptável Segues dentro de um storyboard de um projeto mais antigo, ele deve primeiro ser convertido no formato Xcode 6 Unified Storyboard de dentro do Designer do iOS.

Para fazer isso abra o Storyboard a ser convertido no iOS Designer e verifique se o **Classes de tamanho de uso** caixa de seleção:

 [![](unified-storyboards-images/sizeclass01.png "A caixa de seleção Usar Classes de tamanho")](unified-storyboards-images/sizeclass01.png#lightbox)

O Designer do iOS confirmará que o desenvolvedor deseja converter o formato do storyboard para usar as Classes de tamanho:

 [![](unified-storyboards-images/sizeclass02.png "O uso de alerta de Classes de tamanho")](unified-storyboards-images/sizeclass02.png#lightbox)

> [!IMPORTANT]
> Layout automático também deve ser verificado para Classes de tamanho funcionar corretamente.

### <a name="generic-device-types"></a>Tipos de dispositivo genérico

Depois que o storyboard foi convertido para usar as Classes de tamanho, ela será reexibida na superfície de Design e o **exibir como** dispositivo será genérico:

 [![](unified-storyboards-images/sizeclass03.png "Exibir como um tipo de dispositivo genérico")](unified-storyboards-images/sizeclass03.png#lightbox)

Quando o tipo de dispositivo genérico é selecionado, todos os controladores de exibição serão redimensionados para um quadrado de 600 x 600. Esse quadrado representa os tamanhos de qualquer largura e a qualquer altura. Quando o iOS Designer está nesse modo, todas as edições serão aplicadas a todas as Classes de tamanho.

O desenvolvedor também tem a opção de exibir a superfície de design como um iPhone:

 [![](unified-storyboards-images/sizeclass04.png "Exibindo a superfície de design como um iPhone")](unified-storyboards-images/sizeclass04.png#lightbox)

Ou, visualizando-o como um iPad:

 [![](unified-storyboards-images/sizeclass05.png "Exibindo a superfície de design como um iPad")](unified-storyboards-images/sizeclass05.png#lightbox)

### <a name="select-a-size-class"></a>Selecione uma classe de tamanho

O botão de seletor de classe de tamanho está localizado no canto superior esquerdo da superfície de Design (quase exibir como lista suspensa). Ele permite que o desenvolvedor selecione quais Classes de tamanho estão sendo editados atualmente:

 [![](unified-storyboards-images/sizeclass06.png "Selecione uma classe de tamanho")](unified-storyboards-images/sizeclass06.png#lightbox)

O seletor de apresenta a seleção de classe de tamanho como uma grade de 3x3. Cada um dos quadrados na grade representa uma combinação de uma classe de largura e uma classe de altura. O quadrado central seleciona a classe de qualquer tamanho de altura Width/Any (que é a exibição padrão para um Storyboard unificado). Quando esse quadrado é selecionado, o desenvolvedor está editando o layout padrão, que é herdado por todas as outras configurações.

O quadrado no canto superior esquerdo da grade representa a classe de tamanho de altura de CD/largura Compact:

 [![](unified-storyboards-images/sizeclass07.png "A classe de tamanho de altura Compact largura/CD")](unified-storyboards-images/sizeclass07.png#lightbox)

Esse modo corresponde a um iPhone na orientação paisagem. O quadrado no canto inferior direito da grade representa a largura/Regular altura tamanho classe Regular, que representa um iPad:

 [![](unified-storyboards-images/sizeclass08.png "A classe de tamanho Regular largura/Regular altura")](unified-storyboards-images/sizeclass08.png#lightbox)

Para editar o layout de um iPhone na orientação retrato, selecione o quadrado no canto inferior esquerdo. Isso representa a classe de tamanho Compact Regular de largura/altura:

 [![](unified-storyboards-images/sizeclass09.png "A classe de tamanho de altura Compact largura/normal")](unified-storyboards-images/sizeclass09.png#lightbox)

Clique no quadrado para selecioná-lo e a superfície de Design será alterar o tamanho dos controladores de exibição para corresponder à nova seleção:

 [![](unified-storyboards-images/sizeclass10.png "A superfície de Design será alterar o tamanho dos controladores de exibição para corresponder à nova seleção, conforme mostrado")](unified-storyboards-images/sizeclass10.png#lightbox)

Consulte a seção de classe de tamanho deste artigo para obter mais informações sobre Classes de tamanho e como eles afetam o layout para iPhones e iPads.

### <a name="adaptive-segue-types"></a>Tipos de Segue adaptável

Se o desenvolvedor usou storyboards antes, eles estarão familiarizados com os tipos de segue existente de **enviar por Push**, **Modal** e **pop-over**. Quando as Classes de tamanho estão habilitadas em um arquivo de Storyboard Unificação, os seguintes adaptável Segue tipos (que correspondem à nova API de controlador de exibição discutida acima) são disponibilizados: **mostram** e **Mostrar detalhe** .

> [!IMPORTANT]
> Quando as Classes de tamanho estão habilitadas, todos os existentes segues irá ser convertida para os novos tipos.

Veja o exemplo de um iOS 8 aplicativo que usa um Storyboard unificada com um controlador de exibição de divisão que tem um menu de navegação simples de jogo no modo de exibição mestre. Se o usuário clica em um botão de menu, o controlador de exibição do item selecionado deve ser mostrada na seção de detalhes do controlador de exibição de divisão quando em execução em um iPad. Em um iPhone, controlador de exibição do item deve ser enviado para a pilha de navegação.

Para obter esse efeito, no iOS Designer Ctrl + clique no botão e arraste uma linha para o controlador de exibição a ser exibido. Quando o botão do mouse é liberado, selecione `Show Detail` no menu suspenso de tipo de Segue:

 [![](unified-storyboards-images/segue01.png "Selecione Mostrar detalhe no menu suspenso de tipo de Segue")](unified-storyboards-images/segue01.png#lightbox)

O segue novo será criado entre o botão e o controlador de exibição. Agora execute o aplicativo no simulador do iPhone e o Menu principal será exibido:

 [![](unified-storyboards-images/segue02.png "O Menu principal")](unified-storyboards-images/segue02.png#lightbox)

Clique no **jogo selecione** botão e o controlador de exibição do item serão enviado para a pilha de navegação:

 [![](unified-storyboards-images/segue03.png "Os controlador de exibição de itens serão enviados para a pilha de navegação, conforme mostrado")](unified-storyboards-images/segue03.png#lightbox)

Pare o simulador do iPhone e executar o aplicativo no simulador iPad. Alterne para a orientação paisagem e principal menu é exibido novamente:

 [![](unified-storyboards-images/segue04.png "O menu principal exibido")](unified-storyboards-images/segue04.png#lightbox)

Novamente, clique no **jogo selecione** botão e o controlador de exibição do item é mostrado na seção de detalhes do controlador de exibição de divisão:

 [![](unified-storyboards-images/segue05.png "Os itens do controlador de exibição mostrado na seção de detalhes do controlador de exibição de divisão")](unified-storyboards-images/segue05.png#lightbox)

### <a name="excluding-an-element-from-a-size-class"></a>Excluindo um elemento de uma classe de tamanho

Há ocasiões em que um determinado elemento (por exemplo, um modo de exibição, controle ou uma restrição) não é necessário dentro de uma classe de tamanho específico. Para excluir um elemento de uma classe de tamanho, selecione o item desejado para excluir o **superfície de Design**. Role para baixo do **Property Explorer** e clique no **engrenagem** menu suspenso. Selecione a combinação de **largura** e **altura** para excluir o item de:

[![](unified-storyboards-images/exclude-a.png "Selecione a combinação da largura e altura")](unified-storyboards-images/exclude-a.png#lightbox)

Uma nova *caso de exclusão* será adicionado ao elemento na parte inferior da **Property Explorer**. Em seguida, desmarque a **instalado** caixa de seleção de uma determinada classe de tamanho:

[![](unified-storyboards-images/exclude-b.png "Desmarque a caixa de seleção instalado")](unified-storyboards-images/exclude-b.png#lightbox)

Alternar a superfície de Design para a largura e altura que o item foi excluído da, ele foi removido de determinada classe de tamanho, mas não o design de interface do usuário inteiro:

 [![](unified-storyboards-images/exclude02.png "Alternar a superfície de Design para a largura e altura que o item foi excluído da")](unified-storyboards-images/exclude02.png#lightbox)

Alternar de volta para a classe de tamanho de qualquer altura de Width/Any e o elemento ainda está em vigor:

 [![](unified-storyboards-images/exclude03.png "Alternar de volta para a classe de tamanho de altura de Width/Any Any")](unified-storyboards-images/exclude03.png#lightbox)

Quando o aplicativo é executado no simulador iPad, o elemento é exibido:

 [![](unified-storyboards-images/exclude04.png "O elemento mostrado quando o aplicativo em execução no iPad Simulator")](unified-storyboards-images/exclude04.png#lightbox)

E quando o aplicativo é executado no simulador do iPhone, o elemento está ausente:

 [![](unified-storyboards-images/exclude05.png "O elemento ausente quando o aplicativo em execução no simulador do iPhone")](unified-storyboards-images/exclude05.png#lightbox)

Para remover um caso de exclusão de um elemento, basta selecionar o elemento na **superfície de Design**, role até a parte inferior da **Property Explorer** e clique no **-** botão ao lado do caso para remover.

Para ver uma implementação de Storyboards unificados, examine o `UnifiedStoryboard` Xamarin iOS 8 aplicativo anexado a este documento de exemplo.

## <a name="dynamic-launch-screens"></a>Telas de inicialização dinâmica

O arquivo de tela de inicialização é exibido como uma tela inicial, enquanto um aplicativo iOS está sendo lançada para fornecer comentários ao usuário que o aplicativo é, na verdade, iniciando-up. Antes do iOS 8, o desenvolvedor teria que incluem vários `Default.png` ativos para cada dispositivo tipo, orientação e resolução de tela que o aplicativo seria executado em de imagem. Por exemplo, `Default@2x.png`, `Default-Landscape@2x~ipad.png`, `Default-Portrait@2x~ipad.png`, etc.

Acrescentando novo iPhone 6 e iPhone 6 Plus dispositivos (e o Apple Watch futura) com todos os dispositivos iPad e iPhone existente, isso representa uma grande variedade de tamanhos variados, orientações e resoluções do `Default.png` ativos de imagem de tela de inicialização que devem ser criadas e mantidas. Além disso, esses arquivos podem ser muito grandes e serão "inchar" o pacote de aplicativo do produto, aumentando a quantidade de tempo necessário para baixar o aplicativo da iTunes Store do aplicativo (possivelmente, impedindo que ela poder ser entregues em uma rede de celular) e aumentar a quantidade de armazenamento necessário no dispositivo do usuário final.

Novo para o iOS 8, o desenvolvedor pode criar um único atômico `.xib` arquivo no Xcode que usa Classes de tamanho e o Layout automático para criar um *tela de inicialização dinâmica* que funcionará para cada dispositivo, resolução e orientação. Isso não só reduz a quantidade de trabalho necessário do desenvolvedor para criar e manter todos os ativos de imagem necessário, mas reduz o tamanho do pacote de instaladas do aplicativo.


Telas de inicialização dinâmica tem as seguintes limitações e considerações:

 - Use apenas `UIKit` classes.
 - Usar uma exibição de raiz única que é um `UIView` ou `UIViewController` objeto.
 - Não faça nenhuma conexão com o código do aplicativo (não adicionam **ações** ou **saídas**).
 - Não adicione `UIWebView` objetos.
 - Não use a quaisquer classes personalizadas.
 - Não use atributos de tempo de execução.

Com as diretrizes acima em mente, vamos dar uma olhada na adição de uma tela de inicialização dinâmica para um projeto existente do Xamarin iOS 8.

Faça o seguinte:

1. Abra **Visual Studio para Mac** e carregar o **solução** para adicionar a tela de inicialização dinâmica para.
2. No **Gerenciador de soluções**, com o botão direito do `MainStoryboard.storyboard` do arquivo e selecione **abrir com** > **Xcode Interface Builder**:

    [![](unified-storyboards-images/dls01.png "Abrir com o Xcode Interface Builder")](unified-storyboards-images/dls01.png#lightbox)
3. No Xcode, selecione **arquivo** > **New** > **arquivo...** :

    [![](unified-storyboards-images/dls02.png "Selecione o arquivo / novo")](unified-storyboards-images/dls02.png#lightbox)
4. Selecione **iOS** > **Interface do usuário** > **tela de inicialização** e clique no **próxima** botão:

    [![](unified-storyboards-images/dls03.png "Selecionar o iOS / Interface do usuário / tela de inicialização")](unified-storyboards-images/dls03.png#lightbox)
5. Nomeie o arquivo `LaunchScreen.xib` e clique em de **criar** botão:

    [![](unified-storyboards-images/dls04.png "Nomeie o arquivo LaunchScreen.xib")](unified-storyboards-images/dls04.png#lightbox)
6. Edite o design da tela inicial, adição de elementos gráficos e usando restrições de Layout para posicioná-los para determinado dispositivos, orientações e tamanhos de tela:

    [![](unified-storyboards-images/dls05.png "Editando o design da tela inicial")](unified-storyboards-images/dls05.png#lightbox)
7. Salve as alterações em `LaunchScreen.xib`.
8. Selecione o **aplicativos de destino** e o **geral** guia:

    [![](unified-storyboards-images/dls06.png "Selecione o destino de aplicativos e na guia Geral")](unified-storyboards-images/dls06.png#lightbox)
9. Clique o **escolher Info. plist** botão, selecione o `Info.plist` para o aplicativo Xamarin e clique o **escolher** botão:

    [![](unified-storyboards-images/dls07.png "Selecione o Info. plist para o aplicativo Xamarin")](unified-storyboards-images/dls07.png#lightbox)
10. No **ícones de aplicativo e imagens de inicialização** seção, abra o **inicie o arquivo de tela** lista suspensa e escolha o `LaunchScreen.xib` criado acima:

    [![](unified-storyboards-images/dls08.png "Escolha o LaunchScreen.xib")](unified-storyboards-images/dls08.png#lightbox)
11. Salve as alterações no arquivo e retorne ao Visual Studio para Mac.
12. Aguarde até que o Visual Studio para Mac para concluir a sincronização de alterações com Xcode.
13. No **Gerenciador de soluções**, clique duas vezes no **recursos** pasta e selecione **Add** > **adicionar arquivos...** :

    [![](unified-storyboards-images/dls09.png "Selecione Adicionar / adicionar arquivos...")](unified-storyboards-images/dls09.png#lightbox)
14. Selecione o `LaunchScreen.xib` arquivo criado acima e clique em de **aberto** botão:

    [![](unified-storyboards-images/dls10.png "Selecione o arquivo LaunchScreen.xib")](unified-storyboards-images/dls10.png#lightbox)
15. Compile o aplicativo.

### <a name="testing-the-dynamic-launch-screen"></a>Testando a tela de inicialização dinâmica

No Visual Studio para Mac, selecione o simulador do iPhone 4 Retina e executar o aplicativo. Tela de inicialização dinâmica será exibida no formato correto e orientação:

[![](unified-storyboards-images/dls11.png "A tela de inicialização dinâmica exibido na orientação vertical")](unified-storyboards-images/dls11.png#lightbox)

Interromper o aplicativo no Visual Studio para Mac e selecione um dispositivo do iPad iOS 8. Execute o aplicativo e a tela de inicialização será formatada corretamente para esse dispositivo e a orientação:

[![](unified-storyboards-images/dls12.png "A tela de inicialização dinâmica exibido na orientação horizontal")](unified-storyboards-images/dls12.png#lightbox)

Retorne ao Visual Studio para Mac e parar a execução do aplicativo.

### <a name="working-with-ios-7"></a>Trabalhar com iOS 7

Para manter a compatibilidade com versões anteriores com iOS 7, apenas incluir normais `Default.png` ativos como de costume no aplicativo iOS 8 de imagem. iOS retornará para o comportamento anterior e usá-los como a tela de inicialização durante a execução em um dispositivo iOS 7.

Para ver uma implementação de uma tela de inicialização dinâmica no Xamarin, examine os [telas de inicialização dinâmica](https://developer.xamarin.com/samples/monotouch/ios8/DynamicLaunchScreen/) aplicativo iOS 8 anexado a este documento de exemplo.

## <a name="summary"></a>Resumo

Este artigo apresentou em Classes de tamanho e como eles afetam o layout em dispositivos iPhone e iPad. Ele discutiu como características, ambientes de característica e coleções de característica de trabalham com Classes de tamanho para criar Interfaces de Unificação. Demorou breve visão adaptável controladores de exibição e como eles funcionam com as Classes de tamanho dentro de Interfaces de Unificação. Ela examinou Implementando Classes de tamanho e Interfaces de Unificação de mensagens totalmente a partir do C# código dentro de um aplicativo Xamarin iOS 8.

Por fim, este artigo abordou os conceitos básicos da criação de Storyboards unificados com o Designer que funciona em dispositivos iOS do Xamarin iOS e criação de um único, tela de inicialização dinâmica que será exibido como a tela de inicialização em todos os dispositivos iOS 8.

## <a name="related-links"></a>Links relacionados

- [Fotos adaptáveis (amostra)](https://developer.xamarin.com/samples/monotouch/ios8/AdaptivePhotos/)
- [Exemplo de StoryboardIntro](https://developer.xamarin.com/samples/monotouch/StoryboardIntro/)
- [Telas de inicialização dinâmica (amostra)](https://developer.xamarin.com/samples/monotouch/ios8/DynamicLaunchScreen/)
- [Introdução ao iOS 8](~/ios/platform/introduction-to-ios8.md)
- [Layouts dinâmicos em iOS8 - 2014 evoluir (vídeo)](http://youtu.be/f3mMGlS-lM4)
- [UIPresentationController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPresentationController_class/)
- [UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset)
