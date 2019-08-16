---
title: Storyboards unificados no Xamarin. iOS
description: Este documento descreve os storyboards unificados no Xamarin. iOS. Os storyboards unificados permitem que os desenvolvedores ofereçam suporte a vários tamanhos de tela com uma única definição de interface.
ms.prod: xamarin
ms.assetid: F6F70374-FC2A-4401-A712-A16D0F9B340F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 20010fb3704da54ae1e1133c25f332e8481a1b87
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528546"
---
# <a name="unified-storyboards-in-xamarinios"></a>Storyboards unificados no Xamarin. iOS

o iOS 8 inclui um mecanismo novo e mais simples de usar para criar a interface do usuário — o storyboard unificado. Com um único Storyboard para cobrir todos os diferentes tamanhos de tela de hardware, exibições rápidas e responsivas podem ser criadas em um estilo "design-Once, use-muitos".

Como o desenvolvedor não precisa mais criar um storyboard separado e específico para dispositivos iPhone e iPad, eles têm a flexibilidade de criar aplicativos com uma interface comum e, em seguida, personalizar essa interface para diferentes classes de tamanho. Dessa forma, um aplicativo pode ser adaptado para os pontos fortes de cada fator forma e cada interface do usuário pode ser ajustada para fornecer a melhor experiência.

<a name="size-classes" />

## <a name="size-classes"></a>Classes de tamanho

Antes do IOS 8, o desenvolvedor usava `UIInterfaceOrientation` e `UIInterfaceIdiom` a diferenciar entre os modos retrato e paisagem e entre os dispositivos iPhone e iPad. No iOS8, a orientação e o dispositivo são determinados com o uso de *classes de tamanho*.

Os dispositivos são definidos por classes de tamanho, em ambos os eixos vertical e horizontal, e há dois tipos de classes de tamanho no iOS 8:

- **Regular** – isso é para um tamanho de tela grande (como um iPad) ou um gadget que dá a impressão de um tamanho grande (como um`UIScrollView`
- **Compact** – é para dispositivos menores (como um iPhone). Esse tamanho leva em consideração a orientação do dispositivo.


Se os dois conceitos forem usados juntos, o resultado será uma grade 2 x 2 que define os diferentes tamanhos possíveis que podem ser usados nas orientações diferentes, como visto no diagrama a seguir:

 [![](unified-storyboards-images/sizeclassgrid.png "Uma grade 2 x 2 que define os diferentes tamanhos possíveis que podem ser usados em orientações regulares e compactas")](unified-storyboards-images/sizeclassgrid.png#lightbox)

O desenvolvedor pode criar um controlador de exibição que usa qualquer uma das quatro possibilidades que resultaria em layouts diferentes (como visto nos gráficos acima).

### <a name="ipad-size-classes"></a>Classes de tamanho do iPad

O iPad, devido ao tamanho, tem um tamanho de classe **regular** para ambas as orientações.

 [![](unified-storyboards-images/image1.png "Classes de tamanho do iPad")](unified-storyboards-images/image1.png#lightbox)


### <a name="iphone-size-classes"></a>Classes de tamanho do iPhone

O iPhone tem diferentes classes de tamanho com base na orientação do dispositivo:

 [![](unified-storyboards-images/iphonesizeclasses.png "Classes de tamanho do iPhone")](unified-storyboards-images/iphonesizeclasses.png#lightbox)

- Quando o dispositivo está no modo retrato, a tela tem uma classe de compactação horizontal e **regular** verticalmente
- Quando o dispositivo está no modo paisagem, as classes de tela são revertidas do modo retrato.

### <a name="iphone-6-plus-size-classes"></a>Classes de tamanho iPhone 6 Plus

Os tamanhos são os mesmos que os iPhones anteriores na orientação retrato, mas diferentes em paisagem:

[![](unified-storyboards-images/iphone6sizeclasses.png "Classes de tamanho iPhone 6 Plus")](unified-storyboards-images/iphone6sizeclasses.png#lightbox)

Como o iPhone 6 Plus tem uma tela grande o suficiente, ele é capaz de ter uma classe de tamanho de largura regular no modo paisagem.

### <a name="support-for-a-new-screen-scale"></a>Suporte para uma nova escala de tela

O iPhone 6 Plus usa uma nova exibição HD de retina com um fator de escala de tela de 3,0 (três vezes a resolução de tela original do iPhone). Para fornecer a melhor experiência possível nesses dispositivos, inclua um novo trabalho artístico criado para essa escala de tela. No Xcode 6 e superior, os catálogos de ativos podem incluir imagens em tamanhos 1x, 2x e 3x; Basta adicionar os ativos da nova imagem e o iOS escolherá os ativos corretos ao executar em um iPhone 6 Plus.

O comportamento de carregamento de imagem no Ios também `@3x` reconhece um sufixo em arquivos de imagem. Por exemplo, se o desenvolvedor incluir um ativo de imagem (em resoluções diferentes) no pacote do aplicativo com os seguintes nomes de `MonkeyIcon.png`arquivo `MonkeyIcon@2x.png`:, `MonkeyIcon@3x.png`e. No iPhone 6 mais, a `MonkeyIcon@3x.png` imagem será usada automaticamente se o desenvolvedor carregar uma imagem usando o código a seguir:

```csharp
UIImage icon = UIImage.FromFile("MonkeyImage.png");
```
Ou, se eles atribuirem a imagem a um elemento de interface do usuário `MonkeyIcon.png`usando o `MonkeyIcon@3x.png` designer do IOS como, o será usado novamente automaticamente no iPhone 6 Plus.

<a name="dynamic-launch-screens" />

### <a name="dynamic-launch-screens"></a>Telas de inicialização dinâmica

O arquivo da tela de inicialização é exibido como uma tela inicial enquanto um aplicativo iOS está sendo iniciado para fornecer comentários ao usuário de que o aplicativo está realmente sendo inicializado. Antes do IOS 8, o desenvolvedor teria que incluir vários `Default.png` ativos de imagem para cada tipo de dispositivo, orientação e resolução de tela em que o aplicativo estaria em execução.

Novo no Ios 8, o desenvolvedor pode criar um único arquivo atômico `.xib` no Xcode que usa o layout automático e classes de tamanho para criar uma *tela de inicialização dinâmica* que funcionará para cada dispositivo, resolução e orientação. Isso não apenas reduz a quantidade de trabalho necessária do desenvolvedor para criar e manter todos os ativos de imagem necessários, mas reduz o tamanho do grupo instalado do aplicativo.

## <a name="traits"></a>Características

As características são propriedades que podem ser usadas para determinar como um layout é alterado conforme seu ambiente é alterado. Eles consistem em um conjunto de Propriedades ( `HorizontalSizeClass` o `VerticalSizeClass` e baseado `UIUserInterfaceSizeClass`em), bem como o idioma da interface `UIUserInterfaceIdiom`() e a escala de exibição.

Todos os Estados acima são empacotados em um contêiner ao qual a Apple se refere como uma coleção de `UITraitCollection`características (), que contém não apenas as propriedades, mas também seus valores.

## <a name="trait-environment"></a>Ambiente de características

Ambientes de características são uma nova interface no iOS 8 e são capazes de retornar uma coleção de características para os seguintes objetos:

- Telas ( `UIScreens` ).
- Windows ( `UIWindows` ).
- Exibir controladores ( `UIViewController` ).
- Exibições `UIView` ().
- Controlador de apresentação `UIPresentationController` ().


O desenvolvedor usa a coleção de características retornada por um ambiente de características para determinar como uma interface do usuário deve ser disposta.

Todos os ambientes de características fazem uma hierarquia como visto no diagrama a seguir:

 [![](unified-storyboards-images/viewhierarchy.png "O diagrama hierárquico de ambientes de características")](unified-storyboards-images/viewhierarchy.png#lightbox)

A coleção de características que cada um dos ambientes de características acima fluirá, por padrão, do pai para o ambiente filho.

Além de obter a coleção de características atual, o ambiente de características tem `TraitCollectionDidChange` um método, que pode ser substituído nas subclasses View ou View Controller. O desenvolvedor pode usar esse método para modificar qualquer um dos elementos da interface do usuário que dependem de características quando essas características foram alteradas.

## <a name="typical-trait-collections"></a>Coleções de características típicas

Esta seção abordará os tipos típicos de coleções de características que o usuário vai experimentar ao trabalhar com o iOS 8.

Veja a seguir uma coleção de características típica que o desenvolvedor pode ver em um iPhone:

|Propriedade|Valor|
|--- |--- |
|`HorizontalSizeClass`|Compactá|
|`VerticalSizeClass`|Normal|
|`UserInterfaceIdom`|Telefone|
|`DisplayScale`|2.0|

O conjunto acima representaria uma coleção de características totalmente qualificada, pois ela tem valores para todas as suas propriedades de característica.

Também é possível ter uma coleção de características que não tenha alguns de seus valores (que a Apple se refere como *não especificado*):

|Propriedade|Valor|
|--- |--- |
|`HorizontalSizeClass`|Compactá|
|`VerticalSizeClass`|Não especificado|
|`UserInterfaceIdom`|Não especificado|
|`DisplayScale`|Não especificado|

No entanto, em geral, quando o desenvolvedor solicita o ambiente de características para sua coleção de características, ele retornará uma coleção totalmente qualificada, como visto no exemplo acima.

Se um ambiente de características (como um modo de exibição ou de exibição) não estiver dentro da hierarquia de exibição atual, o desenvolvedor poderá obter valores não especificados novamente para uma ou mais das propriedades de característica.

O desenvolvedor também receberá uma coleção de características parcialmente qualificada se usar um dos métodos de criação fornecidos pela Apple, `UITraitCollection.FromHorizontalSizeClass`como, para criar uma nova coleção.

Uma operação que pode ser executada em várias coleções de características é compará-las entre si, o que envolve a solicitação de uma coleção de características se ela contiver outra. O que significa *contenção* é que, para qualquer característica especificada na segunda coleção, o valor deve corresponder exatamente ao valor na primeira coleção.

Para testar duas características, use o `Contains` método `UITraitCollection` de passar o valor da característica a ser testada.

O desenvolvedor pode executar as comparações manualmente no código para determinar como fazer layout de exibições ou controladores de exibição. No entanto, `UIKit` o usa esse método internamente para fornecer algumas de suas funcionalidades, como no proxy de aparência, por exemplo.

## <a name="appearance-proxy"></a>Proxy de aparência

O proxy de aparência foi introduzido em versões anteriores do iOS para permitir que os desenvolvedores personalizem as propriedades de suas exibições. Ele foi estendido no iOS 8 para dar suporte a coleções de características.

Os proxies de aparência agora incluem um novo `AppearanceForTraitCollection`método,, que retorna um novo proxy de aparência para a coleção de características determinada que foi passada. Todas as personalizações que o desenvolvedor executa nesse proxy de aparência só terão efeito em exibições que estejam de acordo com a coleção de características especificada.

Geralmente, o desenvolvedor passará uma coleção de características parcialmente especificada para `AppearanceForTraitCollection` o método, como uma que acaba de especificar uma classe de tamanho horizontal de Compact, para que pudesse personalizar qualquer exibição no aplicativo que seja compactada horizontalmente.

## <a name="uiimage"></a>UIImage

Outra classe à qual a Apple adicionou a coleção de `UIImage`características é. No passado, o desenvolvedor tinha que especificar uma @1X versão @2x e um ativo gráfico de bitmap que eles vão incluir no aplicativo (como um ícone).

o iOS 8 foi expandido para permitir que o desenvolvedor inclua várias versões de uma imagem em um catálogo de imagens com base em uma coleção de características. Por exemplo, o desenvolvedor pode incluir uma imagem menor para trabalhar com uma classe de característica compacta e uma imagem de tamanho completo para qualquer outra coleção.

Quando uma das imagens é usada dentro de uma `UIImageView` classe, a exibição de imagem exibe automaticamente a versão correta da imagem para sua coleção de características. Se o ambiente de características mudar (como o usuário alternando o dispositivo de retrato para paisagem), a exibição de imagem selecionará automaticamente o novo tamanho da imagem para corresponder à nova coleção de características e alterará seu tamanho para corresponder à versão atual da imagem que está sendo visível.

## <a name="uiimageasset"></a>UIImageAsset

A Apple adicionou uma nova classe ao Ios 8 chamada `UIImageAsset` para dar ao desenvolvedor ainda mais controle sobre a seleção de imagens.

Um ativo de imagem encapsula todas as versões diferentes de uma imagem e permite que o desenvolvedor solicite uma imagem específica que corresponda a uma coleção de características que foi passada. As imagens podem ser adicionadas ou removidas de um ativo de imagem, imediatamente.

Para obter mais informações sobre ativos de imagem, consulte a documentação do [UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset) da Apple.

## <a name="combining-trait-collections"></a>Combinando coleções de características

Outra função que um desenvolvedor pode executar em coleções de características é adicionar duas juntas que resultarão na coleção combinada, em que os valores não especificados de uma coleção são substituídos pelos valores especificados em um segundo. Isso é feito usando o `FromTraitsFromCollections` método `UITraitCollection` da classe.

Como mencionado acima, se qualquer uma das características não for especificada em uma das coleções de características e for especificado em outro, o valor será definido como a versão especificada. No entanto, se houver várias versões de um determinado valor especificado, o valor da última coleção de características será o valor usado.

## <a name="adaptive-view-controllers"></a>Controladores de exibição adaptável

Esta seção abordará os detalhes de como os controladores de exibição e de exibição do iOS adotaram os conceitos de características e classes de tamanho para serem mais adaptáveis nos aplicativos do desenvolvedor.

### <a name="split-view-controller"></a>Controlador de exibição de divisão

Uma das classes do controlador de exibição que mudou mais no Ios 8 é a `UISplitViewController` classe. No passado, o desenvolvedor geralmente usaria um controlador de exibição de divisão na versão do iPad do aplicativo e, em seguida, teria que fornecer uma versão completamente diferente de sua hierarquia de exibição para a versão do iPhone do aplicativo.

No Ios 8, a `UISplitViewController` classe está disponível em ambas as plataformas (iPad e iPhone), que permite ao desenvolvedor criar uma hierarquia de controlador de exibição que funcionará para iPhone e iPad.

Quando um iPhone estiver em paisagem, o controlador de exibição de divisão apresentará suas exibições lado a lado, assim como faria quando fosse exibida em um iPad.

### <a name="overriding-traits"></a>Substituindo características

Ambientes de características em cascata do contêiner pai para os contêineres filho, como no gráfico a seguir, mostrando um controlador de exibição de divisão em um iPad na orientação paisagem:

 [![](unified-storyboards-images/cascadingclasses01.png "Um controlador de exibição de divisão em um iPad na orientação paisagem")](unified-storyboards-images/cascadingclasses01.png#lightbox)

Como o iPad tem uma classe de tamanho regular nas orientações horizontal e vertical, o modo de exibição de divisão exibirá as exibições mestre e de detalhes.

Em um iPhone, em que a classe Size é compactada em ambas as orientações, o controlador de exibição de divisão exibe apenas a exibição de detalhes, como mostrado abaixo:

 [![](unified-storyboards-images/cascadingclasses02.png "O controlador de exibição de divisão exibe apenas a exibição de detalhes")](unified-storyboards-images/cascadingclasses02.png#lightbox)

Em um aplicativo em que o desenvolvedor deseja exibir a exibição mestre e de detalhes em um iPhone na orientação paisagem, o desenvolvedor deve inserir um contêiner pai para o controlador de exibição de divisão e substituir a coleção de características. Como mostrado no gráfico abaixo:

 [![](unified-storyboards-images/cascadingclasses03.png "O desenvolvedor deve inserir um contêiner pai para o controlador de exibição de divisão e substituir a coleção de características")](unified-storyboards-images/cascadingclasses03.png#lightbox)

Um `UIView` é definido como o pai do controlador de exibição de divisão e `SetOverrideTraitCollection` o método é chamado na exibição passando em uma nova coleção de características e direcionando o controlador de exibição de divisão. A nova coleção de características substitui `HorizontalSizeClass`o, definindo- `Regular`o como, para que o controlador de exibição de divisão exiba as exibições mestre e de detalhes em um iPhone na orientação paisagem.

Observe que o `VerticalSizeClass` foi definido como `unspecified`, que permite que a nova coleção de características seja adicionada à coleção de características no pai, resultando em `Compact VerticalSizeClass` um para o controlador de exibição de divisão filho.

### <a name="trait-changes"></a>Alterações de características

Esta seção examinará, em detalhes, como as coleções de características são transferidas quando o ambiente de características muda. Por exemplo, quando o dispositivo é girado de retrato para paisagem.

 [![](unified-storyboards-images/traittransitions01.png "A orientação retrato para paisagem altera a visão geral")](unified-storyboards-images/traittransitions01.png#lightbox)

Primeiro, o iOS 8 faz algumas configurações para se preparar para que a transição ocorra. Em seguida, o sistema anima o estado de transição. Finalmente, o iOS 8 limpa todos os Estados temporários necessários durante a transição.

o iOS 8 fornece vários retornos de chamada que o desenvolvedor pode usar para participar da alteração de característica, como mostrado na tabela a seguir:

|Fase|Retorno|Descrição|
|--- |--- |--- |
|Configuração|<ul><li>`WillTransitionToTraitCollection`</li><li>`TraitCollectionDidChange`</li></ul>|<ul><li>Esse método é chamado no início de uma alteração de característica antes que uma coleção de características seja definida para o novo valor.</li><li>O método é chamado quando o valor da coleção de características é alterado, mas antes de qualquer animação ocorrer.</li></ul>|
|Animação|`WillTransitionToTraitCollection`|O coordenador de transição que é passado para esse método tem `AnimateAlongside` uma propriedade que permite ao desenvolvedor adicionar animações que serão executadas junto com as animações padrão.|
|Limpar|`WillTransitionToTraitCollection`|Fornece um método para que os desenvolvedores incluam seu próprio código de limpeza depois que a transição ocorre.|

O `WillTransitionToTraitCollection` método é ótimo para animar controladores de exibição junto com as alterações de coleção de características. O `WillTransitionToTraitCollection` método só está disponível em controladores de exibição `UIViewController`() e não em outros ambientes de características `UIViews`, como.

O `TraitCollectionDidChange` é ótimo para trabalhar com a `UIView` classe, em que o desenvolvedor deseja atualizar a interface do usuário à medida que as características estão mudando.

### <a name="collapsing-the-split-view-controllers"></a>Recolhendo os controladores de exibição de divisão

Agora vamos examinar mais de perto o que acontece quando um controlador de exibição de divisão recolhe de uma coluna de duas colunas para uma coluna. Como parte dessa alteração, há dois processos que precisam ocorrer:

- Por padrão, o controlador de exibição de divisão usará o controlador de exibição primário como a exibição depois que o recolhimento ocorrer. O desenvolvedor pode substituir esse comportamento substituindo o `GetPrimaryViewControllerForCollapsingSplitViewController` método `UISplitViewControllerDelegate` do e fornecendo qualquer controlador de exibição que desejam exibir no estado recolhido.
- O controlador de exibição secundário precisa ser mesclado no controlador de exibição primário. Geralmente, o desenvolvedor não precisa realizar nenhuma ação para esta etapa; o controlador de exibição de divisão inclui o tratamento automático desta fase com base no dispositivo de hardware. No entanto, pode haver alguns casos especiais em que o desenvolvedor vai querer interagir com essa alteração. Chamar o `CollapseSecondViewController` método `UISplitViewControllerDelegate` de permite que o controlador de exibição mestre seja exibido quando o recolhimento ocorre, em vez da exibição de detalhes.


### <a name="expanding-the-split-view-controller"></a>Expandindo o controlador de exibição de divisão

Agora vamos examinar mais de perto o que acontece quando um controlador de exibição de divisão é expandido de um estado recolhido. Mais uma vez, há dois estágios que precisam ocorrer:

- Primeiro, defina o novo controlador de exibição primário. Por padrão, o controlador de exibição de divisão usará automaticamente o controlador de exibição primário do modo de exibição recolhido. Novamente, o desenvolvedor pode substituir esse comportamento usando o `GetPrimaryViewControllerForExpandingSplitViewController` método `UISplitViewControllerDelegate` do.
- Depois que o controlador de exibição primário tiver sido escolhido, o controlador de exibição secundário deverá ser recriado. Novamente, o controlador de exibição de divisão inclui o tratamento automático desta fase com base no dispositivo de hardware. O desenvolvedor pode substituir esse comportamento chamando o `SeparateSecondaryViewController` método `UISplitViewControllerDelegate` do.


Em um controlador de exibição de divisão, o controlador de exibição primário desempenha uma parte na expansão e no recolhimento das exibições implementando `CollapseSecondViewController` os `SeparateSecondaryViewController` métodos e do `UISplitViewControllerDelegate`. `UINavigationController`implementa esses métodos para enviar e pop automaticamente o controlador de exibição secundário.

### <a name="showing-view-controllers"></a>Mostrando controladores de exibição

Outra alteração que a Apple fez no iOS 8 é a forma como o desenvolvedor mostra os controladores de exibição. No passado, se o aplicativo tivesse um controlador de exibição de folha (como um controlador de exibição de tabela) e o desenvolvedor mostrasse um diferente (por exemplo, em resposta ao usuário tocando em uma célula), o aplicativo voltaria para a hierarquia do controlador para o Controlador de exibição de navegação e `PushViewController` chame o método em relação a ele para exibir a nova exibição.

Isso apresentou um acoplamento muito rígido entre o controlador de navegação e o ambiente em que ele estava sendo executado. No iOS 8, a Apple o desacoplado fornecendo dois novos métodos:

- `ShowViewController`– Adapta-se para exibir o novo controlador de exibição com base em seu ambiente. Por exemplo, em um `UINavigationController` ele simplesmente envia por push a nova exibição para a pilha. Em um controlador de exibição de divisão, o novo controlador de exibição será apresentado no lado esquerdo como o novo controlador de exibição primário. Se nenhum controlador de exibição de contêiner estiver presente, o novo modo de exibição será exibido como um controlador de exibição modal.
- `ShowDetailViewController`– Funciona de maneira semelhante ao `ShowViewController`, mas é implementado em um controlador de exibição de divisão para substituir o modo de exibição de detalhes pelo novo controlador de exibição que está sendo passado. Se o controlador de exibição de divisão estiver recolhido (como pode ser visto em um aplicativo do iPhone), a chamada será redirecionada `ShowViewController` para o método e a nova exibição será mostrada como o controlador de exibição primário. Novamente, se nenhum controlador de exibição de contêiner estiver presente, o novo modo de exibição será exibido como um controlador de exibição modal.


Esses métodos funcionam iniciando no controlador de exibição de folha e orientam a hierarquia de exibição até encontrarem o controlador de exibição de contêiner correto para manipular a exibição da nova exibição.

Os desenvolvedores podem `ShowViewController` implementar `ShowDetailViewController` e em seus próprios controladores de exibição personalizados para obter a mesma funcionalidade `UINavigationController` automatizada que o e `UISplitViewController` o fornece.

### <a name="how-it-works"></a>Como funciona

Nesta seção, vamos dar uma olhada em como esses métodos são realmente implementados no iOS 8. Primeiro, vamos examinar o novo `GetTargetForAction` método:

 [![](unified-storyboards-images/gettargetforaction.png "O novo método GetTargetForAction")](unified-storyboards-images/gettargetforaction.png#lightbox)

Esse método percorre a cadeia de hierarquia até que o controlador de exibição de contêiner correto seja encontrado. Por exemplo:

1. Se um `ShowViewController` método for chamado, o primeiro controlador de exibição na cadeia que implementa esse método é o controlador de navegação, portanto, ele é usado como o pai da nova exibição.
1. Se um `ShowDetailViewController` método foi chamado em vez disso, o controlador de exibição de divisão é o primeiro controlador de exibição para implementá-lo, portanto, ele é usado como o pai.


O `GetTargetForAction` método funciona localizando um controlador de exibição que implementa uma determinada ação e, em seguida, solicitando o controlador de exibição se desejar receber essa ação. Como esse método é público, os desenvolvedores podem criar seus próprios métodos personalizados que funcionam exatamente como os métodos `ShowViewController` internos `ShowDetailViewController` e.

## <a name="adaptive-presentation"></a>Apresentação adaptável

No Ios 8, a Apple também fez a adaptação `UIPopoverPresentationController`de apresentações () popover. Portanto, um controlador de exibição de apresentação popover apresentará automaticamente uma exibição normal de popover em uma classe de tamanho regular, mas exibirá a tela inteira em uma classe de tamanho horizontalmente compacta (como em um iPhone).

Para acomodar as alterações no sistema de storyboard unificado, um novo objeto de controlador foi criado para gerenciar os controladores de `UIPresentationController`exibição apresentados —. Esse controlador é criado a partir do momento em que o controlador de exibição é apresentado até ser Descartado. Como é uma classe de gerenciamento, ela pode ser considerada uma superclasse no controlador de exibição, pois responde às alterações de dispositivo que afetam o controlador de exibição (como orientação) que, em seguida, são alimentadas de volta para o controlador de exibição, os controles do controlador de apresentação.

Quando o desenvolvedor apresenta um controlador de exibição usando `PresentViewController` o método, o gerenciamento do processo de apresentação é enviado `UIKit`para o. Identificadores UIKit (entre outras coisas) o controlador correto para o estilo que está sendo criado, com a única exceção sendo quando um controlador de exibição tem `UIModalPresentationCustom`o estilo definido como. Aqui, o aplicativo pode fornecer seu próprio PresentationController em vez de usar o `UIKit` controlador.

### <a name="custom-presentation-styles"></a>Estilos de apresentação personalizados

Com um estilo de apresentação personalizado, os desenvolvedores têm a opção de usar um controlador de apresentação personalizado. Esse controlador personalizado pode ser usado para modificar a aparência e o comportamento da exibição para a qual ele está Allied.

<a name="size-classes"/>

## <a name="working-with-size-classes"></a>Trabalhando com classes de tamanho

O projeto Xamarin de fotos adaptáveis que está incluído neste artigo fornece um exemplo funcional de como usar classes de tamanho e controladores de exibição adaptável em um aplicativo de interface unificada do iOS 8.

Embora o aplicativo Crie sua interface do usuário completamente do código, em vez de usar o designer do IOS e criar um storyboard unificado, as mesmas técnicas se aplicam. Posteriormente neste artigo, mostraremos como usar classes de tamanho com um storyboard unificado e o designer do iOS em um aplicativo Xamarin.

Agora vamos examinar mais de perto como o projeto de fotos adaptáveis está implementando vários dos recursos de classe de tamanho no iOS 8 para criar um aplicativo adaptável.

### <a name="adapting-to-trait-environment-changes"></a>Adaptando para alterações de ambiente de características

Ao executar o aplicativo de fotos adaptáveis em um iPhone, quando o usuário gira o dispositivo de retrato para paisagem, o controlador de exibição de divisão exibirá o mestre e a exibição de detalhes:

 [![](unified-storyboards-images/rotation.png "O controlador de exibição de divisão exibirá o mestre e a exibição de detalhes, como visto aqui")](unified-storyboards-images/rotation.png#lightbox)

Isso é feito substituindo o `UpdateConstraintsForTraitCollection` método do controlador de exibição e ajustando as restrições com base no valor `VerticalSizeClass`do. Por exemplo:

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

### <a name="adding-transition-animations"></a>Adicionando animações de transição

Quando o controlador de exibição de divisão no aplicativo de fotos adaptáveis passa de recolhido para expandido, as animações são adicionadas às animações `WillTransitionToTraitCollection` padrão substituindo o método do controlador de exibição. Por exemplo:

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

### <a name="overriding-the-trait-environment"></a>Substituindo o ambiente de características

Como mostrado acima, o aplicativo de fotos adaptáveis força o controlador de exibição de divisão a exibir os detalhes e as exibições mestras quando o dispositivo iPhone está no modo de exibição paisagem.

Isso foi feito usando o seguinte código no controlador de exibição:

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

Em seguida, vamos examinar como o comportamento de expansão e recolhimento do controlador de exibição de divisão foi implementado no Xamarin. `AppDelegate`No, quando o controlador de exibição de divisão é criado, seu delegado é atribuído para lidar com essas alterações:

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

O `SeparateSecondaryViewController` método testa para ver se uma foto está sendo exibida e executa ações com base nesse estado. Se nenhuma foto estiver sendo mostrada, ela recolherá o controlador de exibição secundário para que o controlador de exibição mestre seja exibido.

O `CollapseSecondViewController` método é usado ao expandir o controlador de exibição de divisão para ver se há alguma foto na pilha, se ela for recolhida novamente para essa exibição.

### <a name="moving-between-view-controllers"></a>Movendo entre os controladores de exibição

Em seguida, vamos dar uma olhada em como o aplicativo de fotos adaptáveis se move entre os controladores de exibição. Na classe quando o usuário seleciona uma célula da tabela, o `ShowDetailViewController` método é chamado para exibir a exibição de detalhes: `AAPLConversationViewController`

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

### <a name="displaying-disclosure-indicators"></a>Exibindo indicadores de divulgação

No aplicativo de foto adaptável, há vários lugares onde os indicadores de divulgação são ocultados ou mostrados com base nas alterações no ambiente de características. Isso é tratado com o seguinte código:

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

Eles são implementados usando `GetTargetViewControllerForAction` o método discutido em detalhes acima.

Quando um controlador de exibição de tabela está exibindo dados, ele usa os métodos implementados acima para ver se um envio por push ocorrerá ou não, e se deseja ou não exibir ou ocultar o indicador de divulgação de acordo:

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

A Apple adicionou um novo tipo de notificação para trabalhar com classes de tamanho e ambientes de características de dentro de um `ShowDetailTargetDidChangeNotification`controlador de exibição de divisão,. Essa notificação é enviada sempre que a exibição de detalhes de destino de um controlador de exibição de divisão é alterada, como quando o controlador se expande ou recolhe.

O aplicativo de fotos adaptáveis usa essa notificação para atualizar o estado do indicador de divulgação quando o controlador de exibição de detalhes muda:

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

Dê uma olhada mais de perto no aplicativo Adaptive photos para ver todas as maneiras pelas quais as classes de tamanho, as coleções de características e os controladores de exibição adaptável podem ser usados para criar facilmente um aplicativo unificado no Xamarin. iOS.

## <a name="unified-storyboards"></a>Storyboards unificadas

Novo no iOS 8, os storyboards unificados permitem que o desenvolvedor crie um arquivo de storyboard unificado que pode ser exibido em dispositivos iPhone e iPad direcionando várias classes de tamanho. Usando storyboards unificados, o desenvolvedor escreve menos código específico da interface do usuário e tem apenas um design de interface para criar e manter.

Os principais benefícios dos storyboards unificados são:

- Use o mesmo arquivo de storyboard para iPhone e iPad.
- Implantar versões anteriores para iOS 6 e iOS 7.
- Visualize o layout para diferentes dispositivos, orientações e versões de sistema operacional, tudo no designer do Xamarin iOS.

Esse recurso tem suporte total no Visual Studio para Mac

<a name="enabling-size-classes" />

### <a name="enabling-size-classes"></a>Habilitando classes de tamanho

Por padrão, qualquer novo projeto Xamarin. iOS irá dimensionar as classes. Para usar classes de tamanho e continuações adaptável dentro de um storyboard de um projeto mais antigo, primeiro ele deve ser convertido para o formato de storyboard unificado do Xcode 6 de dentro do designer do iOS.

Para fazer isso, abra o storyboard a ser convertido no designer do iOS e marque a caixa de seleção **usar classes de tamanho** :

 [![](unified-storyboards-images/sizeclass01.png "A caixa de seleção usar classes de tamanho")](unified-storyboards-images/sizeclass01.png#lightbox)

O designer do iOS confirmará que o desenvolvedor deseja converter o formato do storyboard para usar classes de tamanho:

 [![](unified-storyboards-images/sizeclass02.png "O alerta usar classes de tamanho")](unified-storyboards-images/sizeclass02.png#lightbox)

> [!IMPORTANT]
> O layout automático também deve ser verificado para que as classes de tamanho funcionem corretamente.

### <a name="generic-device-types"></a>Tipos de dispositivos genéricos

Depois que o storyboard tiver sido convertido para usar classes de tamanho, ele será exibido novamente no Design Surface e a **exibição como** dispositivo será genérica:

 [![](unified-storyboards-images/sizeclass03.png "Exibir como um tipo de dispositivo genérico")](unified-storyboards-images/sizeclass03.png#lightbox)

Quando o tipo de dispositivo genérico for selecionado, todos os controladores de exibição serão redimensionados para um quadrado 600 x 600. Esse quadrado representa tamanhos de qualquer largura e altura. Quando o designer do iOS estiver nesse modo, todas as edições serão aplicadas a todas as classes de tamanho.

O desenvolvedor também tem a opção de exibir a superfície de design como um iPhone:

 [![](unified-storyboards-images/sizeclass04.png "Exibindo a superfície de design como um iPhone")](unified-storyboards-images/sizeclass04.png#lightbox)

Ou exibi-lo como um iPad:

 [![](unified-storyboards-images/sizeclass05.png "Exibindo a superfície de design como um iPad")](unified-storyboards-images/sizeclass05.png#lightbox)

### <a name="select-a-size-class"></a>Selecionar uma classe de tamanho

O botão seletor de classe de tamanho está no canto superior esquerdo do Design Surface (próximo à exibição como lista suspensa). Ele permite que o desenvolvedor Selecione quais classes de tamanho estão sendo editadas no momento:

 [![](unified-storyboards-images/sizeclass06.png "Selecionar uma classe de tamanho")](unified-storyboards-images/sizeclass06.png#lightbox)

O seletor apresenta a seleção de classe de tamanho como uma grade de 3 x 3. Cada um dos quadrados na grade representa uma combinação de uma classe Width e uma classe Height. O quadrado central seleciona a classe qualquer largura/tamanho de altura (que é a exibição padrão para um storyboard unificado). Quando esse quadrado é selecionado, o desenvolvedor está editando o layout padrão, que é herdado por todas as outras configurações.

O quadrado no canto superior esquerdo da grade representa a classe de tamanho compacto da largura/altura do compacto:

 [![](unified-storyboards-images/sizeclass07.png "A classe de tamanho compacto/altura de dimensão")](unified-storyboards-images/sizeclass07.png#lightbox)

Esse modo corresponde a um iPhone na orientação paisagem. O quadrado no canto inferior direito da grade representa a classe de tamanho regular de largura/altura regular, que representa um iPad:

 [![](unified-storyboards-images/sizeclass08.png "A classe de tamanho regular de largura/altura regular")](unified-storyboards-images/sizeclass08.png#lightbox)

Para editar o layout de um iPhone na orientação retrato, selecione o quadrado no canto inferior esquerdo. Isso representa a classe de tamanho compacto/altura normal:

 [![](unified-storyboards-images/sizeclass09.png "A classe de tamanho compacto/altura normal")](unified-storyboards-images/sizeclass09.png#lightbox)

Clique no quadrado para selecioná-lo e o Design Surface alterará o tamanho dos controladores de exibição para corresponder à nova seleção:

 [![](unified-storyboards-images/sizeclass10.png "O Design Surface alterará o tamanho dos controladores de exibição para corresponder à nova seleção, conforme mostrado")](unified-storyboards-images/sizeclass10.png#lightbox)

Consulte a seção tamanho de classe deste artigo para obter mais informações sobre as classes de tamanho e como elas afetam o layout para iPhones e iPads.

### <a name="adaptive-segue-types"></a>Tipos de transição adaptável

Se o desenvolvedor tiver usado storyboards antes, ele estará familiarizado com os tipos de transição existentes de **Push**, **modal** e **popover**. Quando as classes de tamanho são habilitadas em um arquivo de storyboard unificado, os seguintes tipos de transição adaptáveis (que correspondem à nova API do controlador de exibição discutido acima) são disponibilizados: **Mostrar** e **Mostrar detalhes**.

> [!IMPORTANT]
> Quando as classes de tamanho estiverem habilitadas, qualquer continuações existente será convertido para os novos tipos.

Veja o exemplo de um aplicativo iOS 8 que usa um storyboard unificado com um controlador de exibição de divisão que tem um menu de navegação simples no modo de exibição mestre. Se o usuário clicar em um botão de menu, o controlador de exibição do item selecionado deverá ser mostrado na seção de detalhes do controlador de exibição de divisão ao ser executado em um iPad. Em um iPhone, o controlador de exibição do item deve ser enviado por push para a pilha de navegação.

Para obter esse efeito, no controle do designer do iOS, clique no botão e arraste uma linha para o controlador de exibição a ser exibido. Quando o botão do mouse for liberado, `Show Detail` selecione no menu pop-up tipo transição:

 [![](unified-storyboards-images/segue01.png "Selecione Mostrar detalhes no menu pop-up tipo de transição")](unified-storyboards-images/segue01.png#lightbox)

O novo transição será criado entre o botão e o controlador de exibição. Agora, execute o aplicativo no simulador do iPhone e o menu principal será exibido:

 [![](unified-storyboards-images/segue02.png "O menu principal")](unified-storyboards-images/segue02.png#lightbox)

Clique no botão **selecionar jogo** e o controlador de exibição do item será enviado por push para a pilha de navegação:

 [![](unified-storyboards-images/segue03.png "O controlador de exibição de itens será enviado para a pilha de navegação conforme mostrado")](unified-storyboards-images/segue03.png#lightbox)

Pare o simulador do iPhone e execute o aplicativo no simulador do iPad. Alterne para a orientação paisagem e o menu principal é exibido novamente:

 [![](unified-storyboards-images/segue04.png "O menu principal exibido")](unified-storyboards-images/segue04.png#lightbox)

Novamente, clique no botão **selecionar jogo** e o controlador de exibição do item será mostrado na seção de detalhes do controlador de exibição de divisão:

 [![](unified-storyboards-images/segue05.png "O controlador de exibição de itens mostrado na seção de detalhes do controlador de exibição de divisão")](unified-storyboards-images/segue05.png#lightbox)

### <a name="excluding-an-element-from-a-size-class"></a>Excluindo um elemento de uma classe de tamanho

Há ocasiões em que um determinado elemento (como uma exibição, controle ou uma restrição) não é necessário dentro de uma classe de tamanho específica. Para excluir um elemento de uma classe de tamanho, selecione o item desejado a ser excluído no **design Surface**. Role até a parte inferior do **Gerenciador de propriedades** e clique no menu suspenso de **engrenagem** . Selecione a combinação de **largura** e **altura** para a qual excluir o item:

[![](unified-storyboards-images/exclude-a.png "Selecione a combinação de largura e altura")](unified-storyboards-images/exclude-a.png#lightbox)

Um novo *caso de exclusão* será adicionado ao elemento na parte inferior do Gerenciador de **Propriedades**. Em seguida, desmarque a caixa de seleção **instalada** para a classe de tamanho determinada:

[![](unified-storyboards-images/exclude-b.png "Desmarque a caixa de seleção instalado")](unified-storyboards-images/exclude-b.png#lightbox)

Alterne o Design Surface para a largura e a altura da qual o item foi excluído, ele foi removido da classe de tamanho determinada, mas não do design de interface do usuário inteiro:

 [![](unified-storyboards-images/exclude02.png "Alternar o Design Surface para a largura e a altura da qual o item foi excluído")](unified-storyboards-images/exclude02.png#lightbox)

Alternar de volta para a classe any largura/qualquer altura e o elemento ainda está em vigor:

 [![](unified-storyboards-images/exclude03.png "Voltando para a classe any largura/qualquer altura de tamanho")](unified-storyboards-images/exclude03.png#lightbox)

Quando o aplicativo é executado no simulador de iPad, o elemento é exibido:

 [![](unified-storyboards-images/exclude04.png "O elemento mostrado quando o aplicativo em execução no simulador de iPad")](unified-storyboards-images/exclude04.png#lightbox)

E quando o aplicativo é executado no simulador do iPhone, o elemento está ausente:

 [![](unified-storyboards-images/exclude05.png "Elemento ausente quando o aplicativo em execução no simulador de iPhone")](unified-storyboards-images/exclude05.png#lightbox)

Para remover um caso de exclusão de um elemento, basta selecionar o elemento na **design Surface**, rolar até a parte inferior do **Gerenciador de propriedades** e clicar **-** no botão ao lado do caso a ser removido.

Para ver uma implementação de storyboards unificados, examine `UnifiedStoryboard` o aplicativo Xamarin Ios 8 de exemplo anexado a este documento.

## <a name="dynamic-launch-screens"></a>Telas de inicialização dinâmica

O arquivo da tela de inicialização é exibido como uma tela inicial enquanto um aplicativo iOS está sendo iniciado para fornecer comentários ao usuário de que o aplicativo está realmente sendo inicializado. Antes do IOS 8, o desenvolvedor teria que incluir vários `Default.png` ativos de imagem para cada tipo de dispositivo, orientação e resolução de tela em que o aplicativo estaria em execução. Por exemplo, `Default@2x.png` `Default-Landscape@2x~ipad.png`,,, etc`Default-Portrait@2x~ipad.png`.

A fatoração nos novos dispositivos iPhone 6 e iPhone 6 Plus (e o próximo Apple Watch) com todos os dispositivos iPhone e iPad existentes representa uma grande variedade de tamanhos, orientações e resoluções de ativos de imagem `Default.png` da tela de inicialização que devem ser criado e mantido. Além disso, esses arquivos podem ser muito grandes e "inchar" o pacote de aplicativos de entrega, aumentando a quantidade de tempo necessária para baixar o aplicativo da iTunes App Store (possivelmente mantendo a capacidade de ser entregue em uma rede de celular) e aumentar a quantidade de armazenamento necessária no dispositivo do usuário final.

Novo no Ios 8, o desenvolvedor pode criar um único arquivo atômico `.xib` no Xcode que usa o layout automático e classes de tamanho para criar uma *tela de inicialização dinâmica* que funcionará para cada dispositivo, resolução e orientação. Isso não apenas reduz a quantidade de trabalho necessária do desenvolvedor para criar e manter todos os ativos de imagem necessários, mas reduz consideravelmente o tamanho do grupo instalado do aplicativo.


As telas de inicialização dinâmica têm as seguintes limitações e considerações:

- Use somente `UIKit` classes.
- Use uma única exibição raiz que seja um `UIView` objeto `UIViewController` ou.
- Não faça nenhuma conexão com o código do aplicativo (não adicione **ações** ou **saídas**).
- Não adicione `UIWebView` objetos.
- Não use nenhuma classe personalizada.
- Não use atributos de tempo de execução.

Com as diretrizes acima em mente, vamos examinar a adição de uma tela de inicialização dinâmica a um projeto existente do Xamarin iOS 8.

Faça o seguinte:

1. Abra **Visual Studio para Mac** e carregue a **solução** para adicionar a tela de inicialização dinâmica ao.
2. Na **Gerenciador de soluções**, clique com o botão direito `MainStoryboard.storyboard` do mouse no arquivo e selecione **abrir com** > o**Xcode Interface Builder**:

    [![](unified-storyboards-images/dls01.png "Abrir com o Xcode Interface Builder")](unified-storyboards-images/dls01.png#lightbox)
3. No Xcode, selecione **arquivo** > **novo** > **arquivo...** :

    [![](unified-storyboards-images/dls02.png "Selecionar arquivo/novo")](unified-storyboards-images/dls02.png#lightbox)
4. Selecione**tela de inicialização** da**interface** > do usuário do **Ios** > e clique no botão **Avançar** :

    [![](unified-storyboards-images/dls03.png "Selecione a tela de inicialização/interface do usuário/iOS")](unified-storyboards-images/dls03.png#lightbox)
5. Nomeie o arquivo `LaunchScreen.xib` e clique no botão **criar** :

    [![](unified-storyboards-images/dls04.png "Nomeie o arquivo LaunchScreen. xib")](unified-storyboards-images/dls04.png#lightbox)
6. Edite o design da tela de inicialização adicionando elementos gráficos e usando restrições de layout para posicioná-los para os dispositivos, orientações e tamanhos de tela determinados:

    [![](unified-storyboards-images/dls05.png "Editando o design da tela de inicialização")](unified-storyboards-images/dls05.png#lightbox)
7. Salve as alterações em `LaunchScreen.xib`.
8. Selecione o **destino de aplicativos** e a guia **geral** :

    [![](unified-storyboards-images/dls06.png "Selecione o destino de aplicativos e a guia geral")](unified-storyboards-images/dls06.png#lightbox)
9. Clique no botão **escolher info. plist** , selecione o `Info.plist` para o aplicativo Xamarin e clique no botão **escolher** :

    [![](unified-storyboards-images/dls07.png "Selecione o info. plist para o aplicativo Xamarin")](unified-storyboards-images/dls07.png#lightbox)
10. Na seção **ícones de aplicativo e imagens de inicialização** , abra o menu suspenso **Iniciar arquivo** de tela `LaunchScreen.xib` e escolha o criado acima:

    [![](unified-storyboards-images/dls08.png "Escolha o LaunchScreen. xib")](unified-storyboards-images/dls08.png#lightbox)
11. Salve as alterações no arquivo e retorne a Visual Studio para Mac.
12. Aguarde Visual Studio para Mac concluir a sincronização das alterações com o Xcode.
13. Na **Gerenciador de soluções**, clique com o botão direito do mouse na pasta **recurso** e selecione **Adicionar** > **Adicionar arquivos...** :

    [![](unified-storyboards-images/dls09.png "Selecione Adicionar/Adicionar arquivos...")](unified-storyboards-images/dls09.png#lightbox)
14. Selecione o `LaunchScreen.xib` arquivo criado acima e clique no botão **abrir** :

    [![](unified-storyboards-images/dls10.png "Selecione o arquivo LaunchScreen. xib")](unified-storyboards-images/dls10.png#lightbox)
15. Compile o aplicativo.

### <a name="testing-the-dynamic-launch-screen"></a>Testando a tela de inicialização dinâmica

Em Visual Studio para Mac, selecione o simulador de retina do iPhone 4 e execute o aplicativo. A tela inicialização dinâmica será exibida no formato e na orientação corretas:

[![](unified-storyboards-images/dls11.png "A tela de inicialização dinâmica exibida na orientação vertical")](unified-storyboards-images/dls11.png#lightbox)

Pare o aplicativo em Visual Studio para Mac e selecione um dispositivo iPad iOS 8. Execute o aplicativo e a tela de inicialização será formatada corretamente para este dispositivo e orientação:

[![](unified-storyboards-images/dls12.png "A tela de inicialização dinâmica exibida na orientação horizontal")](unified-storyboards-images/dls12.png#lightbox)

Retorne para Visual Studio para Mac e interrompa a execução do aplicativo.

### <a name="working-with-ios-7"></a>Trabalhando com o iOS 7

Para manter a compatibilidade com versões anteriores do IOS 7, basta `Default.png` incluir os ativos de imagem usuais normalmente no aplicativo IOS 8. o iOS retornará ao comportamento anterior e usará esses arquivos como a tela de inicialização ao ser executado em um dispositivo iOS 7.

Para ver uma implementação de uma tela de inicialização dinâmica no Xamarin, examine as [telas de inicialização dinâmica](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-dynamiclaunchscreen) aplicativo de exemplo do IOS 8 anexado a este documento.

## <a name="summary"></a>Resumo

Este artigo resumiu rapidamente as classes de tamanho e como elas afetam o layout em dispositivos iPhone e iPad. Ele abordou como as características, os ambientes de características e as coleções de características funcionam com classes de tamanho para criar interfaces unificadas. Foi uma breve visão dos controladores de exibição adaptável e como eles funcionam com classes de tamanho dentro de interfaces unificadas. Ele examinou a implementação de classes de tamanho e interfaces C# unificadas completamente do código em um aplicativo Xamarin Ios 8.

Por fim, este artigo abordou os conceitos básicos da criação de storyboards unificados com o Xamarin iOS designer, que funcionará em dispositivos iOS e criará uma tela única de inicialização dinâmica que será exibida como a tela de inicialização em cada dispositivo iOS 8.

## <a name="related-links"></a>Links relacionados

- [Fotos adaptáveis (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-adaptivephotos)
- [Telas de inicialização dinâmica (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-dynamiclaunchscreen)
- [Introdução ao iOS 8](~/ios/platform/introduction-to-ios8.md)
- [Layouts dinâmicos no iOS8-Evolve 2014 (vídeo)](http://youtu.be/f3mMGlS-lM4)
- [UIPresentationController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPresentationController_class/)
- [UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset)
