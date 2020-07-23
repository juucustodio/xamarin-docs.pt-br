---
title: Introdução ao iOS 9
description: Este artigo apresenta todas as APIs e recursos novos e modificados disponíveis no iOS 9 para desenvolvedores do Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 4D71BBD9-B948-4B59-9AF5-F199C51CBEB3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: bfa2a74bf9cd30fb7b9888ecd7339a80fe472c9e
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86939081"
---
# <a name="introduction-to-ios-9"></a>Introdução ao iOS 9

_Este artigo apresenta todas as APIs e recursos novos e modificados disponíveis no iOS 9 para desenvolvedores do Xamarin. iOS._

![O logotipo do iOS 9](images/ios9-sml.png)

A Apple adicionou várias novas APIs e serviços no iOS 9, juntamente com vários aprimoramentos nos recursos existentes.

## <a name="3d-touch"></a>3D Touch

Novidade no iOS 9 e no iPhone 6s e iPhone 6s Plus, o 3D Touch adiciona gestos sensíveis à pressão aos seus aplicativos iOS. Com o 3D Touch, um aplicativo do iPhone agora consegue não apenas dizer que o usuário está tocando na tela do dispositivo; ele também pode determinar a quantidade de pressão que o usuário está exercendo e responder aos diferentes níveis de pressão.

o Touch 3D fornece os seguintes recursos para seu aplicativo:

- **Sensibilidade à pressão** – os aplicativos agora podem medir o quão difícil ou claro o usuário está tocando na tela e tirar proveito dessas informações. Por exemplo, um aplicativo de pintura pode tornar uma linha mais espessa ou mais fina com base em quão difícil o usuário está tocando na tela.
- **Peek e pop** -seu aplicativo agora pode permitir que o usuário interaja com seus dados sem precisar navegar fora do contexto atual. Ao pressionar o Hard na tela, eles podem *inspecionar* o item em que estão interessados (como visualizar uma mensagem). Ao pressionar com mais dificuldade, eles podem se *Mostrar* ao item.
- **Ações rápidas** – imagine ações rápidas, como os menus contextuais que podem ser retirados quando um usuário clica com o botão direito do mouse em um item em um aplicativo de área de trabalho. Usando ações rápidas, você pode adicionar atalhos comuns, rápidos e fáceis de acessar a funções em seu aplicativo por meio do ícone de tela inicial no dispositivo iOS.

Para saber mais, consulte nosso [introdução ao guia de toque 3D](~/ios/platform/3d-touch.md) .

## <a name="app-transport-security"></a>Segurança do transporte de aplicativo

Novo no iOS 9, a ATS (segurança de transporte de aplicativo) impõe conexões seguras entre os recursos da Internet (como o servidor back-end do aplicativo) e seu aplicativo. O ATS garante que todas as comunicações com a Internet estejam em conformidade com as práticas recomendadas de conexão, impedindo a divulgação acidental de informações confidenciais diretamente por meio de seu aplicativo ou de uma biblioteca que esteja consumindo.

Como o ATS está habilitado por padrão em aplicativos criados para iOS 9 e OS X 10,11 (El Capitan), todas as conexões que usam [NSUrlConnection](xref:Foundation.NSUrlConnection), [CFUrl](xref:CoreFoundation.CFUrl) ou [NSUrlSession](xref:Foundation.NSUrlSession) estarão sujeitas a requisitos de segurança de ATS. Se suas conexões não atenderem a esses requisitos, elas falharão com uma exceção.

Para saber mais sobre o ATS, consulte nosso guia de [segurança do transporte de aplicativo](~/ios/app-fundamentals/ats.md) .

<a name="multitasking"></a>

## <a name="multitasking-for-ipad"></a>Multitarefa para iPad

Com o iOS 9, a Apple adicionou suporte multitarefa para executar dois aplicativos ao mesmo tempo em um hardware de iPad específico. Como resultado, seus aplicativos do Xamarin. iOS não podem mais pressupor que eles são o único aplicativo em execução em um determinado momento ou que têm acesso à tela inteira ou aos recursos do dispositivo.

Há suporte para multitarefas para iPad por meio dos seguintes recursos:

- **Deslizar** para frente – permite que o usuário execute temporariamente um segundo aplicativo Ios em um painel de deslizamento (no lado direito ou esquerdo da tela com base na direção da linguagem) que abrange aproximadamente 25% do aplicativo principal em execução no momento. O deslizamento está disponível apenas em um iPad pro, iPad Air, iPad Air 2, iPad mini 2, iPad mini 3 ou iPad mini 4.
- **Exibição dividida** -em hardware do iPad com suporte (iPad Air 2, iPad mini 4 e iPad pro somente), o usuário pode escolher um segundo aplicativo e executá-lo lado a lado com o aplicativo em execução no momento em um modo de divisão de tela. O usuário pode controlar a porcentagem da tela principal ocupada por cada aplicativo.
- **Imagem no Picture** -for apps que reproduzem conteúdo de vídeo, o vídeo agora pode ser reproduzido em uma janela móvel e redimensionável que flutua sobre os outros aplicativos em execução no momento no dispositivo IOS. O usuário tem controle total sobre o tamanho e a posição desta janela. A imagem na imagem está disponível apenas em um iPad pro, iPad Air, iPad Air 2, iPad mini 2, iPad mini 3 ou iPad mini 4.

Para saber mais sobre as novas capacidades de multitarefa do iOS 9, consulte nosso guia [multitarefa para iPad](~/ios/platform/multitasking.md) .

## <a name="new-contacts-and-contacts-ui-frameworks"></a>Novas estruturas de interface do usuário de contatos e contatos

Com a introdução do iOS 9, a Apple lançou duas novas estruturas, [Contacts](xref:Contacts) e [ContactsUI](xref:ContactsUI), que substituem o catálogo de endereços existente e as estruturas de interface do usuário do catálogo de endereços usadas pelo Ios 8 e anteriores.

Essas estruturas novas e orientadas a objeto fornecem o seguinte:

- **Contatos** – fornece acesso ao Xamarin. Ios às informações de contato do usuário. Como a maioria dos aplicativos só requer acesso somente leitura, essa estrutura foi otimizada para acesso somente leitura de thread seguro.
- **ContactsUI** – fornece elementos da interface do usuário do Xamarin. Ios para exibir, editar, selecionar e criar contatos em dispositivos IOS.

Para obter mais informações, consulte a documentação [da interface do usuário contatos e contatos](~/ios/platform/contacts.md) .

## <a name="new-search-apis"></a>Novas APIs de pesquisa

A pesquisa foi expandida no iOS 9 para fornecer excelentes maneiras novas de acessar informações dentro do seu aplicativo Xamarin. iOS. Usando as novas APIs de pesquisa, você pode tornar o conteúdo do aplicativo pesquisável por meio de resultados da pesquisa do Spotlight e do Safari, lembretes e sugestões de entrega e Siri. Isso permite que os usuários acessem rapidamente as atividades e informações mais profundas em seu aplicativo.

Além disso, as novas APIs de pesquisa facilitam a integração da pesquisa em seu aplicativo sem a experiência de implementação anterior da pesquisa. Por isso, a Apple alega que normalmente leva algumas horas para fazer com que o conteúdo de um aplicativo iOS 9 seja pesquisado universalmente usando a pesquisa de aplicativos.

Para obter mais informações, consulte nossa documentação de [aprimoramentos de pesquisa](~/ios/platform/search/index.md) .

## <a name="new-stack-view"></a>Nova exibição de pilha

O controle de exibição de pilha ([UIStackView](xref:UIKit.UIStackView) aproveita o poder do layout automático e as classes de tamanho para gerenciar uma pilha de subexibições (horizontalmente ou verticalmente) que respondem dinamicamente à orientação e ao tamanho da tela do dispositivo IOS.

Usando o controle de exibição de pilha, a quantidade de trabalho necessária para o layout de uma interface do usuário é bastante reduzida. O layout de todas as subexibições anexadas a uma exibição de pilha é gerenciado automaticamente com base nas propriedades definidas pelo desenvolvedor, como eixo, distribuição, alinhamento e espaçamento.

Para obter mais informações, consulte nossa documentação [de introdução à exibição de pilha](~/ios/user-interface/controls/uistackview.md) .

## <a name="collection-view-changes"></a>Alterações de exibição de coleção

No iOS 9, o modo de exibição de coleção ([UICollectionView](xref:UIKit.UICollectionView) agora dá suporte à reordenação de itens prontos para uso, adicionando um novo reconhecedor de gestos padrão e vários novos métodos de suporte.

Usando esses novos métodos, você pode implementar facilmente o recurso de arrastar para reordenar no modo de exibição de coleção e ter a opção de personalizar a aparência dos itens durante qualquer estágio do processo de reordenação.

Para saber mais sobre as alterações de exibição de coleção para o iOS 9, consulte nosso guia de [alterações de exibição de coleção](~/ios/user-interface/controls/uicollectionview.md) .

## <a name="game-enhancements"></a>Aprimoramentos de jogos

Com o iOS 9, a Apple fez várias melhorias tecnológicas nas APIs de jogos que facilitam a implementação de gráficos e áudio de jogos em seu aplicativo Xamarin. iOS. Isso inclui a facilidade de desenvolvimento por meio de estruturas de alto nível e o aproveitamento do poder da GPU do dispositivo iOS para melhorar a velocidade e os recursos gráficos com melhorias de nível baixo.

Isso inclui GameplayKit, ReplayKit, modelo de e/s, MetalKit e sombreadores de desempenho de metal juntamente com novos recursos aprimorados de metal, SceneKit e SpriteKit.

Para obter mais informações, consulte a documentação de [aprimoramentos de jogos](~/ios/platform/gaming/index.md) .

## <a name="homekit-framework-changes"></a>Alterações na estrutura do HomeKit

A estrutura [homekit](xref:HomeKit) , introduzida no Ios 8, fornece a capacidade de configurar e controlar vários acessórios habilitados para homekit (como luzes automatizadas, bloqueios de porta e abridores de porta de garagem) de um aplicativo Xamarin. Ios. Além de ser fácil de configurar e configurar, os acessórios HomeKit podem ser controlados por meio de comandos Siri falados.

No iOS 9, a Apple facilitou a instalação, expandiu os tipos de acessórios com suporte e forneceu mais interações de acessório (como controlar um acessório remotamente por meio do iCloud).

Para obter mais informações, consulte nossa [introdução ao homekit](~/ios/platform/homekit.md), [aplicativo de exemplo do HomeKitIntro Ios](https://docs.microsoft.com/samples/xamarin/ios-samples/homekit-homekitintro) e a documentação do [homekit](https://developer.apple.com/homekit/) da Apple.

## <a name="handoff-framework-changes"></a>Alterações da estrutura de entrega

A entrega (também conhecida como continuidade) foi introduzida pela Apple no iOS 8 e no OS X Yosemite (10,10) como uma maneira para o usuário iniciar uma atividade em um de seus dispositivos (iOS ou Mac) e continuar a mesma atividade em outro de seus dispositivos (conforme identificado pela conta do iCloud do usuário).

A entrega foi expandida no iOS 9 para oferecer também suporte a novos recursos de pesquisa aprimorados. Para obter mais informações, consulte nossa documentação de [aprimoramentos de pesquisa](~/ios/platform/search/index.md) . Para obter mais informações sobre como usar a entrega, consulte nossa [introdução à documentação de entrega](~/ios/platform/handoff.md) .

## <a name="new-extension-points"></a>Novos pontos de extensão

No iOS 8, a Apple introduziu extensões — bibliotecas que são apresentadas pelo sistema operacional em contextos padrão, como no centro de notificações, quando o usuário solicita um teclado ou quando está editando uma foto.

Com o iOS 9, a Apple está estendendo o suporte à extensão fornecendo vários novos _pontos de extensão_ que definem políticas de uso e fornecem APIs para trabalhar em uma determinada área da seguinte maneira:

- **Novo ponto de extensão de unidade de áudio** – Use esse ponto de extensão para fornecer efeitos de áudio, instrumentos musicais, geradores de som, etc. para uso em outros aplicativos de host de unidade de áudio (como GarageBand). Esse ponto de extensão também permite que você venda _unidades de áudio_ (plug-ins de áudio) na loja de aplicativos.
- **Novo ponto de extensão de manutenção de índice** — Use esse ponto de extensão para dar suporte à reindexação de dados de aplicativo sem a necessidade de uma reinicialização do aplicativo.
- **Novos pontos de extensão de rede** (eles exigem permissão especial da Apple):
  - **Extensão do provedor de proxy de aplicativo** — Use esse ponto de extensão para implementar um proxy de rede transparente do lado do cliente.
  - **Filtrar provedor de dados/extensão do provedor de controle de filtro** -use estes pontos de extensão para implementar a filtragem de conteúdo de rede dinâmica no dispositivo.
  - **Extensão do provedor de túnel de pacotes** — Use esse ponto de extensão para implementar um cliente de protocolo de encapsulamento VPN personalizado.
- **Novos pontos de extensão do Safari**:
  - **Extensão de bloqueio de conteúdo** — Use esse ponto de extensão para definir uma lista de conteúdo bloqueado que não será exibida quando o usuário estiver navegando na Web.
  - **Extensão de links compartilhados** — Use este ponto de extensão para habilitar a exibição do conteúdo do aplicativo nos links compartilhados do Safari.

Para obter mais informações, consulte nossa [introdução às extensões e a](~/ios/platform/extensions.md) documentação do [Guia de programação de extensões de aplicativo](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214) da Apple.

## <a name="keychain-enhancements"></a>Aprimoramentos do conjunto de chaves

No iOS 9, a Apple aprimorou o conjunto de chaves para fornecer um novo tipo de chave de criptografia para as opções proteger enclave e mais proteção de item da seguinte maneira:

- Uma nova restrição de ID de toque que invalida itens de conjunto de chaves quando o banco de dados de impressão digital é modificado.
- Novas restrições que permitem a criação de entradas da lista de controle de acesso com apenas a ID de toque ou senha.
- Um novo contexto de autenticação que permite invocar a autenticação separada de `SecItem` chamadas.
- Entropia da lista de controle de acesso (usando a opção de senha do aplicativo) para criptografia de item do conjunto de chaves fornecido pelo aplicativo.
- Suporte para gerar e usar chaves dentro do enclave seguro (por meio do `kSecAttrTokenIDSecureEnclave` atributo).

Para obter mais informações, consulte [ID de toque e ID de face no Xamarin. Ios](~/ios/platform/touch-id-face-id.md).

## <a name="right-to-left-language-support"></a>Suporte de idiomas da direita para a esquerda

No iOS 9, a Apple tornou uma interface do usuário invertida mais fácil do que nunca, fornecendo suporte completo para idiomas da direita para a esquerda. Isso inclui o seguinte:

- Os controles de [UIKit](xref:UIKit) padrão serão automaticamente invertidos da direita para a esquerda com base nas configurações de idioma e localidade dos dispositivos IOS.
- A classe [UIView](xref:UIKit.UIView) fornece atributos que permitem que você defina como um determinado modo de exibição deve aparecer quando invertido da direita para a esquerda.
- A capacidade de virar uma imagem programaticamente usando a propriedade [FlipsForRightToLeftLayoutDirection](xref:UIKit.UIImage.FlipsForRightToLeftLayoutDirection) da classe [UIImage](xref:UIKit.UIImage) .

Para obter mais informações, consulte a documentação de [idiomas da direita para a esquerda com suporte](https://developer.apple.com/library/prerelease/ios/documentation/MacOSX/Conceptual/BPInternational/SupportingRight-To-LeftLanguages/SupportingRight-To-LeftLanguages.html#//apple_ref/doc/uid/10000171i-CH17) da Apple.

## <a name="additional-framework-changes"></a>Alterações de estrutura adicionais

Além das principais alterações que abordamos acima, a Apple fez modificações e melhorias em várias estruturas existentes para o iOS 9, incluindo as seguintes:

- Estrutura do AV Foundation
- Estrutura AVKit
- Estrutura CloudKit
- Estrutura de base
- Estrutura de entrega
- Estrutura HealthKit
- Estrutura HomeKit
- Estrutura de autenticação local
- Estrutura MapKit
- Estrutura PassKit
- Estrutura de serviços do Safari
- Estrutura UIKit

Para obter mais informações, consulte nossa documentação [adicional de alterações na estrutura do IOS 9](~/ios/platform/introduction-to-ios9/additional-framework-changes.md) .

## <a name="deprecated-apis-and-functions"></a>APIs e funções preteridas

A Apple preteriu as seguintes APIs e funções no iOS 9:

- **Catálogo de endereços & interface do usuário do catálogo de endereços** -essas APIs foram substituídas pelas estruturas de interface do usuário contato e contato. Para obter mais informações, consulte a documentação [da interface do usuário contatos e contatos](~/ios/platform/contacts.md) .
- **CBCentralManager** -os `RetrievePeripherals` `RetrieveConnectedPeripherals` métodos e da `CBCentralManager` classe foram removidos no Ios 9. Chamar esses métodos fará com que um aplicativo falhe ao emparelhar um acessório ou iniciar o aplicativo.
- **FetchAllChanges** -a `FetchAllChanges` da `CKFetchRecordChangesOperation` classe foi depreciada e será removida no Ios 9.
- **Player de mídia** -a estrutura do Media Player foi preterida no Ios 9. Em vez disso, use as APIs AVKit ou AV Foundation.

Para obter uma lista completa de substituições de API específicas, consulte a documentação de [diffs da API do iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/iOS90APIDiffs/index.html#//apple_ref/doc/uid/TP40016222) da Apple.

## <a name="ios-9-sample-apps"></a>Aplicativos de exemplo do iOS 9

Temos alguns [exemplos específicos do IOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9) para começar:

- [AstroLayout](https://github.com/xamarin/monotouch-samples/tree/master/ios9/AstroLayout)
- [CollectionView](https://github.com/xamarin/monotouch-samples/tree/master/ios9/CollectionView)
- [MetalPerformanceShadersHelloWorld](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-metalperformanceshadershelloworld)
- [MusicMotion](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-musicmotion)
- [Fotoprogresso](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-photoprogress)
- [SegueCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-seguecatalog)
- [StackView](https://github.com/xamarin/monotouch-samples/tree/master/ios9/StackView)
- [StickyCorners](https://github.com/xamarin/monotouch-samples/tree/master/ios9/StickyCorners)

Confira também as partes do iOS desses exemplos (versões do Companion Mac OS X chegando!):

- [AgentsCatalog](https://github.com/xamarin/mac-ios-samples/tree/master/AgentsCatalog)
- [MetalKitEssentials](https://github.com/xamarin/mac-ios-samples/tree/master/MetalKitEssentials)

## <a name="related-links"></a>Links Relacionados

- [Amostras do iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [Introdução ao toque 3D](~/ios/platform/3d-touch.md)
- [Segurança do transporte de aplicativo](~/ios/app-fundamentals/ats.md)
- [Multitarefa para iPad](~/ios/platform/multitasking.md)
- [Interface do usuário de contatos e contatos](~/ios/platform/contacts.md)
- [Novas APIs de pesquisa](~/ios/platform/search/index.md)
- [Introdução à exibição de pilha](~/ios/user-interface/controls/uistackview.md)
- [Alterações de exibição de coleção](~/ios/user-interface/controls/uicollectionview.md)
- [Aprimoramentos de jogos](~/ios/platform/gaming/index.md)
- [Introdução ao HomeKit](~/ios/platform/homekit.md)
- [Introdução à entrega](~/ios/platform/handoff.md)
- [Alterações de estrutura adicionais do iOS 9](~/ios/platform/introduction-to-ios9/additional-framework-changes.md)
- [Solução de problemas](~/ios/platform/introduction-to-ios9/troubleshooting.md)
- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [O que há de novo no iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Atualizando seus aplicativos Xamarin. iOS para iOS9 (vídeo)](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
