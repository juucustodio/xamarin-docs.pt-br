---
title: Storyboards unificadas
description: "Storyboards unificados permitem iOS developer criar a interface do usuário com um storyboard único, em vez de vários storyboards, para cobrir o intervalo de expansão de tamanhos de tela do dispositivo. Este artigo foi projetado para dar uma visão mais profunda sobre a operação do storyboard unificada em xamarin."
ms.topic: article
ms.prod: xamarin
ms.assetid: F6F70374-FC2A-4401-A712-A16D0F9B340F
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 30a952bf0df4db34c749de3d6198877b7a9766b9
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="unified-storyboards"></a>Storyboards unificadas

_Storyboards unificados permitem iOS developer criar a interface do usuário com um storyboard único, em vez de vários storyboards, para cobrir o intervalo de expansão de tamanhos de tela do dispositivo. Este artigo foi projetado para dar uma visão mais profunda sobre a operação do storyboard unificada em xamarin._

iOS 8 inclui um mecanismo de novo, mais simples de usar para criar a interface do usuário — o storyboard unificado. Com um único storyboard para abranger todos os tamanhos de tela de hardware diferente, rápidas e ágeis exibições podem ser criadas em um "design-uma vez, use-muitos" estilo.

Como o desenvolvedor não precisa criar um storyboard específico e separado de dispositivos iPhone e iPad, eles têm a flexibilidade de criar aplicativos com uma interface comum e, em seguida, personalizar essa interface para classes de tamanho diferente. Dessa forma, um aplicativo pode ser adaptado para os pontos fortes de cada fator de forma e cada interface de usuário pode ser ajustada para proporcionar a melhor experiência.

<a name="size-classes" />

## <a name="size-classes"></a>Classes de tamanho

Antes do iOS 8, o desenvolvedor usava `UIInterfaceOrientation` e `UIInterfaceIdiom` para diferenciar entre os modos retrato e paisagem e entre os dispositivos iPhone e iPad. Em iOS8, orientação e o dispositivo é determinada usando *tamanho Classes*.

Dispositivos que são definidos pelas Classes de tamanho, no eixo vertical e horizontal, e há dois tipos de classes de tamanho no iOS 8:

-  **Regular** – isso é para um tamanho de tela grande (como um iPad) ou um gadget que lhe dá a impressão de um tamanho grande (como um `UIScrollView`
-  **Compact** – isso é para dispositivos menores (como um iPhone). Esse tamanho leva em conta a orientação do dispositivo.


Se os dois conceitos são usados juntos, o resultado é uma grade de 2 x 2 que define os tamanhos diferentes possíveis que podem ser usados em ambos os as orientações diferentes, como visto no diagrama a seguir:

 [![](unified-storyboards-images/sizeclassgrid.png "Uma grade de 2 x 2 que define os tamanhos diferentes possíveis que podem ser usados em orientações Regular e CD")](unified-storyboards-images/sizeclassgrid.png#lightbox)

O desenvolvedor pode criar um controlador de exibição que usa qualquer um dos quatro possibilidades que resultariam em layouts diferentes (como visto no gráfico acima).

### <a name="ipad-size-classes"></a>Classes de tamanho de iPad

O iPad, devido ao tamanho, tem um **regular** tamanho para ambas as orientações de classe.

 [![](unified-storyboards-images/image1.png "Classes de tamanho de iPad")](unified-storyboards-images/image1.png#lightbox)


### <a name="iphone-size-classes"></a>Classes de tamanho do iPhone

O iPhone tem as classes de tamanho diferente com base na orientação do dispositivo:

 [![](unified-storyboards-images/iphonesizeclasses.png "Classes de tamanho do iPhone")](unified-storyboards-images/iphonesizeclasses.png#lightbox)

-  Quando o dispositivo estiver no modo retrato, a tela tem um **compact** classe horizontalmente e **regular** verticalmente
-  Quando o dispositivo estiver no modo paisagem, as classes de tela são revertidas no modo retrato.

### <a name="iphone-6-plus-size-classes"></a>Classes de iPhone 6 Plus tamanho

Os tamanhos são os mesmos a iPhones anterior na orientação retrato, mas diferente no cenário:

[![](unified-storyboards-images/iphone6sizeclasses.png "Classes de iPhone 6 Plus tamanho")](unified-storyboards-images/iphone6sizeclasses.png#lightbox)

Como o iPhone 6 Plus tem uma tela grande o suficiente, pode ter uma classe de tamanho Regular largura no modo paisagem.

### <a name="support-for-a-new-screen-scale"></a>Suporte para uma nova tela de escala

O iPhone 6 Plus usa uma nova tela de Retina HD com um fator de escala de tela de 3.0 (três vezes a original iPhone resolução de tela). Para fornecer a melhor experiência possível nesses dispositivos, incluem nova arte projetado para essa escala da tela. No Xcode 6 e posterior, o ativo de catálogos podem incluir imagens em 1 x, x 2 e 3 tamanhos de x Basta adicionar o novo ativos de imagem e iOS escolherá os ativos corretos durante a execução em um iPhone 6 Plus.

A imagem de carregamento de comportamento no iOS também reconhece um `@3x` sufixo nos arquivos de imagem. Por exemplo, se o desenvolvedor inclui um ativo de imagem (em diferentes resoluções) no pacote do aplicativo com os seguintes nomes de arquivo: `MonkeyIcon.png`, `MonkeyIcon@2x.png`, e `MonkeyIcon@3x.png`. No iPhone 6 Plus o `MonkeyIcon@3x.png` imagem será usada automaticamente se o desenvolvedor carrega uma imagem usando o seguinte código:

```csharp
UIImage icon = UIImage.FromFile("MonkeyImage.png");
```
Ou se eles atribuir a imagem a um elemento de interface do usuário usando o iOS Designer como `MonkeyIcon.png`, o `MonkeyIcon@3x.png` será usado, automaticamente, no iPhone 6 Plus.

<a name="dynamic-launch-screens" />

### <a name="dynamic-launch-screens"></a>Telas de inicialização dinâmica

O arquivo de tela de início é exibido como uma tela inicial, enquanto um aplicativo iOS está iniciando para fornecer comentários para o usuário que o aplicativo é na verdade, iniciando o. Antes do iOS 8, o desenvolvedor precisa incluir vários `Default.png` ativos para cada dispositivo tipo, orientação e resolução de tela que o aplicativo seria executado em da imagem.

Novo para iOS 8, o desenvolvedor pode criar um único atômico `.xib` arquivo no Xcode que usa as Classes de tamanho e Layout automático para criar um *dinâmico da tela Iniciar* que funcionará para cada dispositivo, resolução e orientação. Isso não só reduz a quantidade de trabalho necessário do desenvolvedor para criar e manter todos os ativos de imagem necessário, mas reduz o tamanho do pacote instalado do aplicativo.

## <a name="traits"></a>Características

Características são propriedades que podem ser usadas para determinar como um layout muda à medida que suas alterações de ambiente. Eles consistem em um conjunto de propriedades (o `HorizontalSizeClass` e `VerticalSizeClass` com base em `UIUserInterfaceSizeClass`), bem como o idioma da interface ( `UIUserInterfaceIdiom`) e a escala da exibição.

Todos os estados acima são encapsulados em um contêiner que Apple se refere a como uma coleção de característica ( `UITraitCollection`), que contém não apenas as propriedades, mas seus valores também.

## <a name="trait-environment"></a>Ambiente de característica

Ambientes de característica são uma nova interface no iOS 8 e pode retornar uma coleção de característica para os seguintes objetos:

-  Telas ( `UIScreens` ).
-  Windows ( `UIWindows` ).
-  Exibir controladores ( `UIViewController` ).
-  Modos de exibição ( `UIView` ).
-  Controlador de apresentação ( `UIPresentationController` ).


O desenvolvedor usa a coleção de característica retornado por um ambiente de característica para determinar como uma interface do usuário deve ser apresentada.

Todos os ambientes de característica fazem uma hierarquia, como visto no diagrama a seguir:

 [![](unified-storyboards-images/viewhierarchy.png "O diagrama de hierarquia de ambientes de característica")](unified-storyboards-images/viewhierarchy.png#lightbox)

A coleção de característica com cada um dos ambientes característica acima fluirá, por padrão, o ambiente do filho do pai.

Além de obter a coleção de característica atual, o ambiente de característica tem um `TraitCollectionDidChange` método, que pode ser substituído em subclasses de exibição ou controlador de exibição. O desenvolvedor pode usar esse método para modificar qualquer um dos elementos da interface do usuário que dependem de características quando essas características foram alterados.

## <a name="typical-trait-collections"></a>Coleções de característica típico

Esta seção aborda os tipos típicos de coleções de característica que a experiência do usuário ao trabalhar com iOS 8.

O exemplo a seguir é uma coleção de característica típica que o desenvolvedor pode ver em um iPhone:

<table width="100%" border="1px">
<thead>
<tr>
    <td>Propriedade</td>
    <td>Valor</td>
</tr>
</thead>
<tbody>
<tr>
    <td><code>HorizontalSizeClass</code></td>
    <td>Compact</td>
</tr>
<tr>
    <td><code>VerticalSizeClass</code></td>
    <td>Normal</td>
</tr>
<tr>
    <td><code>UserInterfaceIdom</code></td>
    <td>Telefone</td>
</tr>
<tr>
    <td><code>DisplayScale</code></td>
    <td>2.0</td>
</tr>
</tbody>
</table>

O conjunto acima representariam um totalmente qualificado característica coleção porque tem valores para todas as suas propriedades de característica.

Também é possível ter um conjunto de características que está faltando em alguns de seus valores (que Apple se refere a como *Unspecified*):

<table width="100%" border="1px">
<thead>
<tr>
    <td>Propriedade</td>
    <td>Valor</td>
</tr>
</thead>
<tbody>
<tr>
    <td><code>HorizontalSizeClass</code></td>
    <td>Compact</td>
</tr>
<tr>
    <td><code>VerticalSizeClass</code></td>
    <td>{especificado}</td>
</tr>
<tr>
    <td><code>UserInterfaceIdom</code></td>
    <td>{especificado}</td>
</tr>
<tr>
    <td><code>DisplayScale</code></td>
    <td>{especificado}</td>
</tr>
</tbody>
</table>

Em geral, no entanto, o desenvolvedor solicita o ambiente de característica para sua coleção de característica, ela retornará um conjunto totalmente qualificado como visto no exemplo acima.

Se um ambiente de característica (como uma exibição ou View Controller) não está dentro da hierarquia de exibição atual, o desenvolvedor pode voltar valores não especificados para uma ou mais das propriedades de traço.

O desenvolvedor também obtém uma coleção de característica parcialmente qualificado se usarem um dos métodos de criação fornecidos pela Apple, como `UITraitCollection.FromHorizontalSizeClass`, para criar uma nova coleção.

Uma operação que pode ser executada em várias coleções de característica está comparando--los uns aos outros, que envolve solicitando uma coleção de característica se ele contém outro. O que significa *contenção* é que, para qualquer característica especificada na coleção de segundo, o valor deve corresponder exatamente com o valor na primeira coleção.

Para testar duas características usam o `Contains` método o `UITraitCollection` passando o valor de característica a ser testada.

O desenvolvedor pode realizar as comparações manualmente no código para determinar como modos de exibição de layout ou controladores de exibição. No entanto, `UIKit` usa esse método internamente para fornecer algumas das suas funcionalidades, como o Proxy de aparência, por exemplo.

## <a name="appearance-proxy"></a>Proxy de aparência

O Proxy de aparência foi introduzido em versões anteriores do iOS para permitir que os desenvolvedores personalizar as propriedades de seus modos de exibição. Ele foi estendido no iOS 8 para dar suporte a coleções de característica.

Aparência Proxies agora incluem um novo método, `AppearanceForTraitCollection`, que retorna um novo Proxy de aparência para a determinada coleção característica que foi passado no. Todas as personalizações que o desenvolvedor executa em que o Proxy de aparência só terão efeito em exibições que estão em conformidade à coleção de característica especificada.

Geralmente o desenvolvedor passará em uma coleção de característica parcialmente especificado para o `AppearanceForTraitCollection` método, como aquele especificado apenas uma Horizontal tamanho classe do Compact, para que eles podem personalizar qualquer modo de exibição do aplicativo que é compact horizontalmente.

## <a name="uiimage"></a>UIImage

Outra classe Apple adicionou a coleção de característica a é `UIImage`. No passado o desenvolvedor precisa especificar um @1X e @2x versão de qualquer ativo gráfico de bitmap que eles estavam indo para incluir no aplicativo (por exemplo, um ícone).

iOS 8 foi expandida para permitir que o desenvolvedor incluir várias versões de uma imagem em um catálogo de imagem com base em uma coleção de característica. Por exemplo, o desenvolvedor pode incluir uma imagem menor para trabalhar com uma classe de característica Compact e uma imagem de tamanho completa para qualquer outra coleção.

Quando uma das imagens é usada dentro de um `UIImageView` classe, o modo de exibição de imagem automaticamente exibirá a versão correta da imagem para sua coleção de característica. Se o ambiente de característica muda (por exemplo, o usuário alternar o dispositivo de retrato para paisagem), o modo de exibição de imagem selecionará automaticamente o novo tamanho da imagem para coincidir com a nova coleção de característica e alterar seu tamanho para coincidir com a versão atual da imagem que está sendo exibido.

## <a name="uiimageasset"></a>UIImageAsset

Apple adicionou uma nova classe para o iOS 8 chamada `UIImageAsset` para dar ao desenvolvedor ainda mais controle sobre a seleção da imagem.

Um ativo de imagem conclui todas as versões diferentes de uma imagem e permite que o desenvolvedor pedir uma imagem específica que corresponde a uma coleção de característica que foi passado no. Imagens podem ser adicionadas ou removidas de um ativo de imagem, durante a execução.

Para obter mais informações sobre ativos de imagem, consulte da Apple [UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset) documentação.

## <a name="combining-trait-collections"></a>Combinando coleções de característica

Outra função que um desenvolvedor pode executar em coleções de característica é somar dois que resultar na coleção combinada, em que os valores não especificados de uma coleção são substituídos pelos valores especificados em um segundo. Isso é feito usando o `FromTraitsFromCollections` método o `UITraitCollection` classe.

Como mencionado acima, se qualquer traços é especificado em uma das coleções característica e é especificado em outro, o valor será definido para a versão especificada. No entanto, se houver várias versões de um determinado valor especificado, o valor da última coleta de característica será o valor que é usado.


## <a name="adaptive-view-controllers"></a>Controladores de exibição adaptável

Esta seção aborda os detalhes de como os iOS exibição e exibir controladores de adotaram os conceitos de Classes de tamanho e características para ser mais adaptável em aplicativos do desenvolvedor automaticamente.

### <a name="split-view-controller"></a>Controlador de exibição de divisão

Uma das classes do controlador de exibição que foi alterado no iOS 8 é o `UISplitViewController` classe. No passado, o desenvolvedor geralmente usa um controlador de exibição de divisão na versão do aplicativo de iPad e, em seguida, que é preciso fornecer uma versão completamente diferente da sua hierarquia do modo de exibição para a versão do aplicativo do iPhone.

No iOS 8, o `UISplitViewController` classe está disponível em ambas as plataformas (iPhone e iPad), que permite ao desenvolvedor criar uma hierarquia de View Controller que funcionará para iPhone e iPad.

Quando um iPhone está no cenário, o controlador de exibição de divisão apresentará sua exibições-lado a lado, como faria ao que está sendo exibido em um iPad.

### <a name="overriding-traits"></a>Características de substituição

Ambientes de característica em cascata do contêiner pai até os contêineres filho, como o gráfico a seguir mostra uma divisão View Controller em um iPad na orientação paisagem:

 [![](unified-storyboards-images/cascadingclasses01.png "Um controlador de exibição de divisão em um iPad na orientação paisagem")](unified-storyboards-images/cascadingclasses01.png#lightbox)

Como o iPad tem uma classe de tamanho Regular no orientações horizontais e verticais, o modo divisão exibirá os modos de exibição mestre e de detalhes.

Em um iPhone, onde a classe de tamanho é compact em ambas as orientações, o controlador de exibição de divisão exibe apenas o modo de exibição de detalhes, conforme mostrado abaixo:

 [![](unified-storyboards-images/cascadingclasses02.png "O controlador de exibição de divisão exibe apenas o modo de exibição de detalhes")](unified-storyboards-images/cascadingclasses02.png#lightbox)

Em um aplicativo onde quer que o desenvolvedor de exibição mestre e detalhes em um iPhone na orientação paisagem, o desenvolvedor deve inserir um contêiner pai para o controlador de exibição de divisão e substituir a coleção de característica. Conforme visto no gráfico abaixo:

 [![](unified-storyboards-images/cascadingclasses03.png "O desenvolvedor deve inserir um contêiner pai para o controlador de exibição de divisão e substituir a coleção de característica")](unified-storyboards-images/cascadingclasses03.png#lightbox)

Um `UIView` é definido como o pai do controlador de exibição de divisão e `SetOverrideTraitCollection` método é chamado no modo de exibição passando um novo conjunto de características e direcionando o controlador de exibição de divisão. A nova coleção de característica substitui o `HorizontalSizeClass`, definindo-a como `Regular`, de modo que o controlador de exibição de divisão exibirá os modos de exibição mestre e de detalhes em um iPhone na orientação paisagem.

Observe que o `VerticalSizeClass` foi definida como `unspecified`, que permite que a nova coleção de característica a ser adicionado à coleção de característica do pai, resultando em um `Compact VerticalSizeClass` para o controlador de exibição de divisão de filho.

### <a name="trait-changes"></a>Alterações de característica

Esta seção apresentará, em detalhes, como as coleções de característica transição quando o ambiente de característica é alterado. Por exemplo, quando o dispositivo for girado de retrato para paisagem.

 [![](unified-storyboards-images/traittransitions01.png "Retrato para paisagem visão geral de alterações de característica")](unified-storyboards-images/traittransitions01.png#lightbox)

Primeiro, o iOS 8 faz algumas configurações para se preparar para a transição ocorrerá. Em seguida, o sistema anima a transição de estado. Por fim, o iOS 8 limpa qualquer estado temporário necessário durante a transição.

iOS 8 fornece vários retornos de chamada que o desenvolvedor pode usar para participar da alteração de característica conforme visto na tabela a seguir:

<table width="100%" border="1px">
<thead>
<tr>
    <td>Fase</td>
    <td>retorno de chamada</td>
    <td>Descrição</td>
</tr>
</thead>
<tbody>
<tr>
    <td>Configuração</td>
    <td>
        <ul>
        <li><code>WillTransitionToTraitCollection</code></li>
        <li><code>TraitCollectionDidChange</code></li>
        </ul>
    </td>
    <td>
        <ul>
        <li>Esse método é chamado no começo de uma alteração de característica antes de uma coleção de característica é definida para o novo valor.</li>
        <li>O método é chamado quando o valor da coleção característica é alterado, mas antes de qualquer animação ocorre.</li>
        </ul>
    </td>
</tr>
<tr>
    <td>Animação</td>
    <td>
        <ul>
        <li><code>WillTransitionToTraitCollection</code></li>
        </ul>
    </td>
    <td>
        <ul>
        <li>O coordenador de transição que é passada para este método tem um <code>AnimateAlongside</code> propriedade que permite ao desenvolvedor adicionar animações que serão executadas juntamente com as animações padrão.</li>
        </ul>
    </td>
</tr>
<tr>
    <td>Limpar</td>
    <td>
        <ul>
        <li><code>WillTransitionToTraitCollection</code></li>
        </ul>
    </td>
    <td>
        <ul>
        <li>Fornece um método para desenvolvedores incluir seu próprio código de limpeza após a transição ocorre.</li>
        </ul>
    </td>
</tr>
</tbody>
</table>

O `WillTransitionToTraitCollection` método é excelente para animar exibir controladores junto com as alterações da coleção de característica. O `WillTransitionToTraitCollection` método só está disponível em controladores de exibição ( `UIViewController`) e não em outros ambientes de característica, como `UIViews`.

O `TraitCollectionDidChange` é ideal para trabalhar com o `UIView` classe, onde quer que o desenvolvedor para atualizar a interface do usuário que estão alterando as características.

### <a name="collapsing-the-split-view-controllers"></a>Recolhendo os controladores de exibição de divisão

Agora vamos tomar examinar mais detalhadamente o que acontece quando um controlador de exibição de divisão recolhe um duas colunas em uma exibição de uma coluna. Como parte dessa alteração, há dois processos que precisam ocorrer:

-  Por padrão, o controlador de exibição de divisão usará o controlador primário exibição como o modo de exibição após o recolhimento. O desenvolvedor pode substituir esse comportamento, substituindo o `GetPrimaryViewControllerForCollapsingSplitViewController` método o `UISplitViewControllerDelegate` e fornecer qualquer controlador de exibição que deseja exibir no estado recolhido.
-  O controlador secundário de modo de exibição tem são mesclados em exibição controlador primário. Geralmente o desenvolvedor não precisa realizar qualquer ação para esta etapa; o controlador de exibição de divisão inclui a manipulação automática dessa fase com base no dispositivo de hardware. No entanto, pode haver alguns casos especiais em que o desenvolvedor deve interagir com essa alteração. Chamando o `CollapseSecondViewController` método o `UISplitViewControllerDelegate` permite que o controlador de modo de exibição mestre a ser exibida quando o recolhimento ocorre, em vez da exibição de detalhes.


### <a name="expanding-the-split-view-controller"></a>Expandindo o controlador de exibição de divisão

Agora vamos examinar mais próximo que ocorre quando um controlador de exibição de divisão for expandido de um estado recolhido. Uma vez, há duas etapas que precisam ocorrer:

-  Primeiro, defina o novo controlador de exibição principal. Por padrão, o controlador de exibição de divisão usará automaticamente o controlador primário do modo de exibição do modo de exibição recolhido. Novamente, o desenvolvedor pode substituir esse comportamento usando o `GetPrimaryViewControllerForExpandingSplitViewController` método o `UISplitViewControllerDelegate` .
-  Depois que o controlador primário do modo de exibição foi escolhido, o controlador secundário de modo de exibição devem ser recriado. Novamente, o controlador de exibição de divisão inclui a manipulação automática dessa fase com base no dispositivo de hardware. O desenvolvedor pode substituir esse comportamento, chamando o `SeparateSecondaryViewController` método o `UISplitViewControllerDelegate` .


Em um controlador de exibição de divisão, o controlador primário do modo de exibição desempenha uma função na expansão e recolhimento dos modos de exibição com a implementação de `CollapseSecondViewController` e `SeparateSecondaryViewController` métodos do `UISplitViewControllerDelegate`. `UINavigationController` implementa esses métodos para enviar por push e pop o controlador secundário exibição automaticamente.

### <a name="showing-view-controllers"></a>Controladores de exibição mostrando

Outra alteração feitas Apple iOS 8 é da forma que o desenvolvedor mostra controladores de exibição. No passado, se o aplicativo tinha um controlador de exibição de folha (como um controlador de exibição de tabela), e o desenvolvedor mostrava um diferente (por exemplo, em resposta a um toque de usuário em uma célula), o aplicativo alcançam novamente por meio da hierarquia de controlador para o Controlador de exibição de navegação e chamar o `PushViewController` método nele para exibir o novo modo de exibição.

Isso apresentado um acoplamento muito forte entre o controlador de navegação e o que ele estava sendo executado no ambiente. No iOS 8, Apple tem desacoplado isso fornecendo dois novos métodos:

-  `ShowViewController` – Se adapta para exibir o novo controlador de exibição com base em seu ambiente. Por exemplo, em um `UINavigationController` ele simplesmente envia o novo modo de exibição para a pilha. Em um controlador de exibição de divisão, o novo controlador de exibição será apresentado no lado esquerdo como o novo controlador de exibição principal. Se nenhum controlador de exibição do contêiner estiver presente, o novo modo de exibição será exibido como um controlador de exibição Modal.
-  `ShowDetailViewController` – Funciona de forma semelhante ao `ShowViewController`, mas é implementado em um controlador de exibição de divisão para substituir o modo de exibição de detalhes com o novo controlador de exibição está sendo transmitido. Se o controlador de exibição de divisão é recolhido (como pode ser visto em um aplicativo de iPhone), a chamada será redirecionada para o `ShowViewController` método e o novo modo de exibição serão mostrado como o controlador primário do modo de exibição. Novamente, se não há nenhum controlador de exibição do contêiner estiver presente, o novo modo de exibição será exibido como um controlador de exibição Modal.


Esses métodos funcionam iniciando no controlador de exibição de folha e percorrer a hierarquia de exibição até que ele encontrar o controlador de exibição à direita do contêiner para lidar com a exibição da nova exibição.

Os desenvolvedores podem implementar `ShowViewController` e `ShowDetailViewController` em seus próprios controladores de exibição personalizados para obter o mesmo automatizada funcionalidade que `UINavigationController` e `UISplitViewController` fornece.

### <a name="how-it-works"></a>Como ele funciona

Nesta seção, obtemos uma olhada em como esses métodos são realmente implementados no iOS 8. Primeiro vamos examinar o novo `GetTargetForAction` método:

 [![](unified-storyboards-images/gettargetforaction.png "O novo método GetTargetForAction")](unified-storyboards-images/gettargetforaction.png#lightbox)

Este método ajuda a cadeia de hierarquia até que o controlador de exibição de contêiner correto foi encontrado. Por exemplo:

1.  Se um `ShowViewController` método é chamado, o primeiro controlador de exibição da cadeia que implementa este método é o controlador de navegação, para que ele é usado como o pai do novo modo de exibição.
1.  Se um `ShowDetailViewController` método foi chamado em vez disso, o controlador de exibição de divisão é o primeiro controlador de exibição para implementá-lo, portanto, é usado como o pai.


O `GetTargetForAction` método opera Localizando um controlador de exibição que implementa uma determinada ação e, em seguida, perguntando se deseja receber essa ação esse controlador de exibição. Como esse método é público, os desenvolvedores podem criar seus próprios métodos personalizados de função assim como interna em `ShowViewController` e `ShowDetailViewController` métodos.

## <a name="adaptive-presentation"></a>Apresentação adaptável

No iOS 8, Apple fez Popover apresentações ( `UIPopoverPresentationController`) adaptável também. Para um controlador de exibição de apresentação de Popover automaticamente apresentará um modo de exibição normal Popover em uma classe de tamanho Regular, mas ele exibir tela inteira em uma classe de tamanho compact horizontalmente (como em um iPhone).

Para acomodar as alterações no sistema de storyboard unificada, foi criado um novo objeto de controlador para gerenciar os controladores de exibição apresentada — `UIPresentationController`. Esse controlador é criado desde o momento em que o controlador de exibição é apresentado até que seja descartada. Como é uma classe de gerenciamento, ele pode ser considerado uma superclasse sobre o controlador de exibição como responder a alterações de dispositivo que afetam o controlador de exibição (como orientação) que são então alimentado voltar ao controlador de exibição controla o controlador de apresentação.

Quando o desenvolvedor apresenta um controlador de exibição usando o `PresentViewController` método, o gerenciamento do processo de apresentação é entregue para `UIKit`. UIKit trata (entre outras coisas), o controlador correto para o estilo que está sendo criado, com a única exceção sendo quando um controlador de exibição tem o estilo definido como `UIModalPresentationCustom`. Aqui, o aplicativo pode fornecer é o próprio PresentationController em vez de usar o `UIKit` controlador.

### <a name="custom-presentation-styles"></a>Estilos de apresentação personalizada

Com um estilo de apresentação personalizada, os desenvolvedores têm a opção de usar um controlador personalizado de apresentação. Esse controlador personalizado pode ser usado para modificar a aparência e comportamento do modo de exibição, ele é allied para.

<a name="size-classes"/>

## <a name="working-with-size-classes"></a>Trabalhar com Classes de tamanho

O projeto de Xamarin fotos adaptável que acompanha este artigo fornece um exemplo de usando Classes de tamanho e adaptável exibir controladores em um aplicativo do iOS 8 Interface unificada.

Enquanto o aplicativo cria sua interface de usuário completamente do código, em vez de usar o Designer de IOS e criar um Storyboard unificado, as mesmas técnicas que se aplicam. Neste artigo, vamos mostrar como usar Classes de tamanho com um Storyboard unificado e o Designer em um aplicativo Xamarin iOS.

Agora vamos examinar mais detalhadamente como o projeto de fotos adaptável é implementam vários recursos de classe de tamanho no iOS 8 para criar um aplicativo adaptável.

### <a name="adapting-to-trait-environment-changes"></a>Adaptar a alterações de ambiente de característica

Ao executar o aplicativo de fotos adaptável em um iPhone, quando o usuário gira o dispositivo de retrato para paisagem, o controlador de exibição de divisão exibirá o modo de exibição mestre e detalhes:

 [![](unified-storyboards-images/rotation.png "O controlador de exibição de divisão exibirá ambos o mestre e exibição de detalhes, como visto aqui")](unified-storyboards-images/rotation.png#lightbox)

Isso é feito por meio da substituição de `UpdateConstraintsForTraitCollection` método do controlador de exibição e ajustar as restrições com base no valor da `VerticalSizeClass`. Por exemplo:

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

Quando o controlador de exibição de divisão em fotos adaptável aplicativo vai de recolhido para expandido, animações são adicionadas para as animações padrão, substituindo o `WillTransitionToTraitCollection` método do controlador de exibição. Por exemplo:

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

Como mostrado acima, o aplicativo de fotos adaptável força o controlador de exibição de divisão para exibir os detalhes e os modos de exibição mestres quando o dispositivo de iPhone estiver no modo de exibição paisagem.

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

Avançar vamos examinar como o comportamento de expansão e recolhimento de controlador de exibição de divisão foi implementado no Xamarin. No `AppDelegate`, quando o controlador de exibição de divisão é criado, seu delegado é atribuído para lidar com essas alterações:

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

O `SeparateSecondaryViewController` método testes para verificar se uma foto está sendo exibida e executará uma ação com base no estado. Se nenhuma foto estiver sendo mostrado ele recolhe o controlador secundário de modo de exibição para que o controlador de exibição mestre seja exibido.

O `CollapseSecondViewController` método é usado quando o controlador de exibição de divisão de expansão para ver se qualquer fotos existirem na pilha, se assim que ela reduz para esse modo de exibição.

### <a name="moving-between-view-controllers"></a>Movendo entre os controladores de exibição

Em seguida, vamos dar uma olhada em como o aplicativo de fotos adaptável se move entre os controladores de exibição. No `AAPLConversationViewController` classe quando o usuário seleciona uma célula da tabela, o `ShowDetailViewController` método é chamado para mostrar a exibição de detalhes:

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

### <a name="displaying-disclosure-indicators"></a>Exibindo indicadores de divulgação de informações

No aplicativo de foto adaptável existem diversos lugares onde divulgação indicadores estão ocultos ou mostrados com base em alterações no ambiente de característica. Isso é tratado com o código a seguir:

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

Eles são implementados usando o `GetTargetViewControllerForAction` método discutido em detalhes acima.

Quando um controlador de exibição de tabela está exibindo dados, ele usa os métodos implementados acima para ver ou não um envio por push vai acontecer e se deseja ou não exibir ou ocultar o indicador de divulgação adequadamente:

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

Apple tenha adicionado um novo tipo de notificação para trabalhar com Classes de tamanho e ambientes de característica de dentro de um controlador de exibição de divisão, `ShowDetailTargetDidChangeNotification`. Essa notificação é enviada sempre que altera o modo de exibição de detalhes para um controlador de exibição de divisão de destino, como quando o controlador expande ou recolhe.

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

Examinar o aplicativo de fotos adaptável para ver todas as maneiras que Classes de tamanho mais próximo, característica coleções e adaptável controladores de exibição podem ser usados para criar facilmente um aplicativo unificado no xamarin.

## <a name="unified-storyboards"></a>Storyboards unificadas

Novo para iOS 8, Storyboards unificado permitir que o desenvolvedor criar um, unificado arquivo de storyboard que pode ser exibido em dispositivos iPhone e iPad direcionando várias Classes de tamanho. Usando Storyboards unificado, o desenvolvedor grava menos código específico de interface do usuário e tem apenas uma interface de design para criar e manter.

Os principais benefícios do Unified Storyboards são:

-  Use o mesmo arquivo de storyboard para iPhone e iPad.
-  Implante com versões anteriores ao iOS 6 e iOS 7.
-  Visualize o layout para diferentes dispositivos, orientações e versões do sistema operacional tudo a partir de dentro do iOS Xamarin Designer.

Esse recurso tem suporte total no Visual Studio para Mac

<a name="enabling-size-classes" />

### <a name="enabling-size-classes"></a>Habilitar Classes de tamanho

Por padrão, qualquer novo projeto xamarin usará classes de tamanho. Para usar as Classes de tamanho e Segues adaptável dentro de um storyboard de um projeto mais antigo, ele deve primeiro ser convertido para o formato do Xcode 6 unificado Storyboard de dentro do Designer do iOS.

Para fazer isso abrir o Storyboard a ser convertido no iOS Designer e verifique se o **Use Classes de tamanho** caixa de seleção:

 [![](unified-storyboards-images/sizeclass01.png "A caixa de seleção Usar Classes de tamanho")](unified-storyboards-images/sizeclass01.png#lightbox)

O Designer do iOS confirmará que o desenvolvedor quiser converter o formato do storyboard para usar as Classes de tamanho:

 [![](unified-storyboards-images/sizeclass02.png "O uso de alerta de Classes de tamanho")](unified-storyboards-images/sizeclass02.png#lightbox)

> [!IMPORTANT]
> **Observação**: Layout automático também deve ser verificada para Classes de tamanho funcionar corretamente.

### <a name="generic-device-types"></a>Tipos de dispositivos genéricos

Depois que o storyboard foi convertido para usar Classes de tamanho, será reexibida na superfície de Design e o **exibição como** dispositivo será genérico:

 [![](unified-storyboards-images/sizeclass03.png "Exibir como um tipo de dispositivo genérico")](unified-storyboards-images/sizeclass03.png#lightbox)

Quando o tipo de dispositivo genérico é selecionado, todos os controladores de exibição serão redimensionados para um quadrado 600 x 600. Esse quadrado representa tamanhos de largura e a qualquer altura. Quando o iOS Designer está nesse modo, todas as edições serão aplicada a todas as Classes de tamanho.

O desenvolvedor também tem a opção de exibir a superfície de design como um iPhone:

 [![](unified-storyboards-images/sizeclass04.png "Exibindo a superfície de design como um iPhone")](unified-storyboards-images/sizeclass04.png#lightbox)

Ou exibi-lo como um iPad:

 [![](unified-storyboards-images/sizeclass05.png "Exibindo a superfície de design como um iPad")](unified-storyboards-images/sizeclass05.png#lightbox)

### <a name="select-a-size-class"></a>Selecione uma classe de tamanho

É o botão seletor de classe de tamanho no canto superior esquerdo da superfície de Design (perto do menu suspenso de exibição como). Ele permite que o desenvolvedor selecionar quais Classes de tamanho estão sendo editados atualmente:

 [![](unified-storyboards-images/sizeclass06.png "Selecione uma classe de tamanho")](unified-storyboards-images/sizeclass06.png#lightbox)

O seletor apresenta a seleção de tamanho de classe como uma grade 3 x 3. Cada um dos quadrados na grade representa uma combinação de uma classe de largura e uma classe de altura. O quadrado central seleciona a classe de qualquer tamanho de altura Width/Any (que é a exibição padrão para um Storyboard unificado). Quando esse quadrado é selecionado, o desenvolvedor está editando o layout padrão, que é herdado por todas as outras configurações.

O quadrado no canto superior esquerdo da grade representa a classe de tamanho de altura de largura/Compact Compact:

 [![](unified-storyboards-images/sizeclass07.png "A classe de tamanho de altura largura Compact/CD")](unified-storyboards-images/sizeclass07.png#lightbox)

Esse modo corresponde a um iPhone na orientação paisagem. O quadrado no canto inferior direito da grade representa a largura/Regular altura tamanho classe Regular, que representa um iPad:

 [![](unified-storyboards-images/sizeclass08.png "A classe de tamanho Regular largura/Regular altura")](unified-storyboards-images/sizeclass08.png#lightbox)

Para editar o layout de um iPhone na orientação retrato, selecione o quadrado no canto inferior esquerdo. Isso representa a classe de tamanho Compact normal de largura/altura:

 [![](unified-storyboards-images/sizeclass09.png "A classe de tamanho de altura largura Compact/Regular")](unified-storyboards-images/sizeclass09.png#lightbox)

Clique no quadrado para selecioná-la e a superfície de Design será alterado o tamanho dos controladores de exibição para corresponder à nova seleção:

 [![](unified-storyboards-images/sizeclass10.png "A superfície de Design será alterado o tamanho dos controladores de exibição para corresponder à nova seleção, conforme mostrado")](unified-storyboards-images/sizeclass10.png#lightbox)

Consulte a seção de tamanho de classe deste artigo para obter mais informações sobre Classes de tamanho e como eles afetam o layout para iPhones e iPads.

### <a name="adaptive-segue-types"></a>Tipos de atender adaptável

Se o desenvolvedor usou storyboards antes, eles estarão familiarizados com os tipos de segue existente de **Push**, **Modal** e **Popover**. Quando o tamanho de Classes estão habilitado em um arquivo de Storyboard unificado, os seguintes adaptável atender tipos (que correspondem a nova API de controlador de exibição discutida acima) são disponibilizados: **Mostrar** e **Mostrar detalhes** .

> [!IMPORTANT]
> **Observação**: Classes de tamanho quando estão habilitados, todos os existentes segues será ser convertidos nos tipos de novo.

Veja o exemplo de um iOS 8 aplicativo que usa um Storyboard unificada com um controlador de exibição de divisão que tem um menu de navegação de jogo simples no modo de exibição mestre. Se o usuário clica em um botão de menu, o controlador de exibição do item selecionado deve ser mostrado na seção detalhes do controlador de exibição de divisão quando em execução em um iPad. Em um iPhone, controlador de exibição do item deve ser enviado para a pilha de navegação.

Para obter esse efeito, no Designer de iOS control, clique no botão e arraste uma linha para o controlador de exibição a ser exibido. Quando o botão do mouse é liberado, selecione `Show Detail` no menu suspenso de tipo atender:

 [![](unified-storyboards-images/segue01.png "Selecione Mostrar detalhes no menu suspenso de tipo atender")](unified-storyboards-images/segue01.png#lightbox)

Segue o novo será criado entre o botão e o controlador de exibição. Agora execute o aplicativo no simulador de iPhone e o Menu principal será exibido:

 [![](unified-storyboards-images/segue02.png "O Menu principal")](unified-storyboards-images/segue02.png#lightbox)

Clique no **selecione jogo** botão e o controlador de exibição do item serão enviado para a pilha de navegação:

 [![](unified-storyboards-images/segue03.png "Os itens de exibição controlador serão enviados para a pilha de navegação, conforme mostrado")](unified-storyboards-images/segue03.png#lightbox)

Pare o simulador de iPhone e executar o aplicativo no simulador iPad. Alterne para a orientação de paisagem e principal menu é exibido novamente:

 [![](unified-storyboards-images/segue04.png "O menu principal exibido")](unified-storyboards-images/segue04.png#lightbox)

Novamente, clique no **selecione jogo** botão e o controlador de exibição do item é mostrado na seção detalhes do controlador de exibição de divisão:

 [![](unified-storyboards-images/segue05.png "Os itens de controlador de exibição mostrado na seção de detalhes do controlador de exibição de divisão")](unified-storyboards-images/segue05.png#lightbox)

### <a name="excluding-an-element-from-a-size-class"></a>Excluindo um elemento de uma classe de tamanho

Há ocasiões em que um determinado elemento (como uma exibição, controle ou uma restrição) não é necessário dentro de uma classe específica de tamanho. Para excluir um elemento de uma classe de tamanho, selecione o item desejado para excluir no **superfície de Design**. Role até a parte inferior da **propriedade Explorer** e clique no **engrenagem** menu suspenso. Selecione a combinação de **largura** e **altura** para excluir o item de:

[![](unified-storyboards-images/exclude-a.png "Selecione a combinação da largura e altura")](unified-storyboards-images/exclude-a.png#lightbox)

Um novo *exclusão caso* será adicionado ao elemento na parte inferior da **Explorer propriedade**. Em seguida, desmarque o **instalado** caixa de seleção de uma determinada classe de tamanho:

[![](unified-storyboards-images/exclude-b.png "Desmarque a caixa de seleção instalado")](unified-storyboards-images/exclude-b.png#lightbox)

Alternar a superfície de Design para a largura e altura que o item foi excluído da, ele foi removido de determinada classe de tamanho, mas não o design de interface de usuário inteiro:

 [![](unified-storyboards-images/exclude02.png "Alternar a superfície de Design para a largura e altura que o item foi excluído da")](unified-storyboards-images/exclude02.png#lightbox)

Alternar para a classe de tamanho de qualquer altura de Width/Any e o elemento ainda está em vigor:

 [![](unified-storyboards-images/exclude03.png "Alternando de volta para a classe de tamanho de qualquer altura de Width/Any")](unified-storyboards-images/exclude03.png#lightbox)

Quando o aplicativo é executado no iPad simulador, o elemento é exibido:

 [![](unified-storyboards-images/exclude04.png "O elemento mostrado quando o aplicativo em execução no simulador iPad")](unified-storyboards-images/exclude04.png#lightbox)

E, quando o aplicativo é executado no iPhone simulador, o elemento está ausente:

 [![](unified-storyboards-images/exclude05.png "O elemento ausente quando o aplicativo em execução no simulador de iPhone")](unified-storyboards-images/exclude05.png#lightbox)

Para remover um caso de exclusão de um elemento, basta selecionar o elemento no **superfície de Design**, role até a parte inferior da **propriedade Explorer** e clique no  **-** botão ao lado de caso para remover.

Para ver uma implementação de Storyboards unificado, examine o `UnifiedStoryboard` Xamarin iOS 8 aplicativo anexado a este documento de exemplo.

## <a name="dynamic-launch-screens"></a>Telas de inicialização dinâmica

O arquivo de tela de início é exibido como uma tela inicial, enquanto um aplicativo iOS está iniciando para fornecer comentários para o usuário que o aplicativo é na verdade, iniciando o. Antes do iOS 8, o desenvolvedor precisa incluir vários `Default.png` ativos para cada dispositivo tipo, orientação e resolução de tela que o aplicativo seria executado em da imagem. Por exemplo, `Default@2x.png`, `Default-Landscape@2x~ipad.png`, `Default-Portrait@2x~ipad.png`, etc.

Acrescentando novo iPhone 6 e iPhone 6 Plus dispositivos (e a Apple Watch futura) com todos os dispositivos iPad e iPhone existente, isso representa uma grande variedade de tamanhos variados, orientações e resoluções de `Default.png` ativos de imagem de tela de inicialização que devem ser criadas e mantidas. Além disso, esses arquivos podem ser muito grandes e serão "inchar" o pacote de aplicativo do produto, aumentar a quantidade de tempo necessário para baixar o aplicativo na iTunes App Store (possivelmente mantendo-possam ser entregues em uma rede de celular) e aumentar a quantidade de armazenamento necessário no dispositivo do usuário final.

Novo para iOS 8, o desenvolvedor pode criar um único atômico `.xib` arquivo no Xcode que usa as Classes de tamanho e Layout automático para criar um *dinâmico da tela Iniciar* que funcionará para cada dispositivo, resolução e orientação. Isso não só reduz a quantidade de trabalho necessário do desenvolvedor para criar e manter todos os ativos de imagem necessário, mas reduz o tamanho do pacote instalado do aplicativo.


Telas de inicialização dinâmica tem as seguintes limitações e considerações:

 - Use somente `UIKit` classes.
 - Usar uma exibição de raiz única que é um `UIView` ou `UIViewController` objeto.
 - Não faça todas as conexões com o código do aplicativo (não adicione **ações** ou **tomadas**).
 - Não adicione `UIWebView` objetos.
 - Não use todas as classes personalizadas.
 - Não use os atributos de tempo de execução.

Com as diretrizes acima em mente, vamos adicionar uma tela de inicialização dinâmica a um projeto existente do iOS 8 Xamarin.

Faça o seguinte:

1. Abra **Visual Studio para Mac** e carregar o **solução** para a tela Iniciar dinâmico para adicionar.
2. No **Solution Explorer**, com o botão direito do `MainStoryboard.storyboard` de arquivo e selecione **abrir com** > **Xcode Interface Builder**:

    [![](unified-storyboards-images/dls01.png "Abrir com o Xcode Interface construtor")](unified-storyboards-images/dls01.png#lightbox)
3. No Xcode, selecione **arquivo** > **novo** > **arquivo...** :

    [![](unified-storyboards-images/dls02.png "Selecione o arquivo / novo")](unified-storyboards-images/dls02.png#lightbox)
4. Selecione **iOS** > **Interface do usuário** > **tela Iniciar** e clique no **próximo** botão:

    [![](unified-storyboards-images/dls03.png "Selecionar o iOS / de Interface de usuário / tela Iniciar")](unified-storyboards-images/dls03.png#lightbox)
5. Nomeie o arquivo `LaunchScreen.xib` e clique no **criar** botão:

    [![](unified-storyboards-images/dls04.png "Nomeie o arquivo LaunchScreen.xib")](unified-storyboards-images/dls04.png#lightbox)
6. Edite o design da tela inicial, adicionar elementos gráficos e usando restrições de Layout para posicioná-los para os dispositivos determinados, orientações e tamanhos de tela:

    [![](unified-storyboards-images/dls05.png "Editando o design da tela inicial")](unified-storyboards-images/dls05.png#lightbox)
7. Salvar as alterações em `LaunchScreen.xib`.
8. Selecione o **aplicativos de destino** e **geral** guia:

    [![](unified-storyboards-images/dls06.png "Selecione o destino de aplicativos e na guia Geral")](unified-storyboards-images/dls06.png#lightbox)
9. Clique o **Info. plist escolha** botão, selecione o `Info.plist` para o aplicativo Xamarin e clique o **escolha** botão:

    [![](unified-storyboards-images/dls07.png "Selecione o Info. plist do aplicativo Xamarin")](unified-storyboards-images/dls07.png#lightbox)
10. No **ícones de aplicativo e imagens de inicialização** seção, abra o **iniciar tela arquivo** suspensa e escolha o `LaunchScreen.xib` criado acima:

    [![](unified-storyboards-images/dls08.png "Escolha o LaunchScreen.xib")](unified-storyboards-images/dls08.png#lightbox)
11. Salve as alterações no arquivo e retornar ao Visual Studio para Mac.
12. Aguarde até que o Visual Studio para Mac para concluir a sincronização de alterações com o Xcode.
13. No **Solution Explorer**, com o botão direito no **recurso** pasta e selecione **adicionar** > **adicionar arquivos...** :

    [![](unified-storyboards-images/dls09.png "Selecione Adicionar / adiciona arquivos...")](unified-storyboards-images/dls09.png#lightbox)
14. Selecione o `LaunchScreen.xib` arquivo criado anteriormente e clique no **abrir** botão:

    [![](unified-storyboards-images/dls10.png "Selecione o arquivo LaunchScreen.xib")](unified-storyboards-images/dls10.png#lightbox)
15. Compile o aplicativo.

### <a name="testing-the-dynamic-launch-screen"></a>Testando a tela inicial dinâmico

No Visual Studio para Mac, selecione o simulador de Retina iPhone 4 e executar o aplicativo. A tela de inicialização dinâmica será exibida no formato correto e orientação:

[![](unified-storyboards-images/dls11.png "A tela de inicialização dinâmica exibido na orientação vertical")](unified-storyboards-images/dls11.png#lightbox)

Interrompa o aplicativo no Visual Studio para Mac e selecione um dispositivo do iOS 8 iPad. Execute o aplicativo e a tela inicial será formatada corretamente para este dispositivo e a orientação:

[![](unified-storyboards-images/dls12.png "A tela de inicialização dinâmica exibido na orientação horizontal")](unified-storyboards-images/dls12.png#lightbox)

Retorne ao Visual Studio para Mac e parar a execução do aplicativo.

### <a name="working-with-ios-7"></a>Trabalhando com iOS 7

Para manter a compatibilidade com versões anteriores com iOS 7, simplesmente incluem o usual `Default.png` ativos como normal no aplicativo do iOS 8 da imagem. iOS retornará para o comportamento anterior e usá-los como a tela de inicialização durante a execução em um dispositivo iOS 7.

Para ver uma implementação de uma tela de inicialização dinâmica em Xamarin, examine o [dinâmico telas iniciar](https://developer.xamarin.com/samples/monotouch/ios8/DynamicLaunchScreen/) aplicativo iOS 8 anexado a este documento de exemplo.

## <a name="summary"></a>Resumo

Este artigo levou uma visão geral de Classes de tamanho e como eles afetam o layout em dispositivos iPhone e iPad. Ele discutiu como características, ambientes de característica e coleções de característica trabalham com Classes de tamanho para criar Interfaces unificado. Necessário examinar adaptável exibir controladores e como trabalhar com Classes de tamanho dentro unificado de Interfaces. Pesquisá-lo a implementar completamente unificado Interfaces e Classes de tamanho do código c# dentro de um aplicativo do iOS 8 Xamarin.

Por fim, este artigo abordadas as Noções básicas de criação Storyboards unificado com Xamarin iOS Designer que funcionarão em dispositivos iOS e criação de uma única tela Iniciar dinâmica que será exibido como a tela de inicialização em cada dispositivo iOS 8.

## <a name="related-links"></a>Links relacionados

- [Fotos adaptáveis (exemplo)](https://developer.xamarin.com/samples/monotouch/ios8/AdaptivePhotos/)
- [Exemplo de StoryboardIntro](https://developer.xamarin.com/samples/monotouch/StoryboardIntro/)
- [Telas de inicialização dinâmica (exemplo)](https://developer.xamarin.com/samples/monotouch/ios8/DynamicLaunchScreen/)
- [Introdução ao iOS 8](~/ios/platform/introduction-to-ios8.md)
- [Layouts dinâmicos no iOS8 - 2014 evoluir (vídeo)](http://youtu.be/f3mMGlS-lM4)
- [UIPresentationController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPresentationController_class/)
- [UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset)
