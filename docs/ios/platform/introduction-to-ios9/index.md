---
title: Introdução ao iOS 9
description: Este artigo apresenta todas as APIs e recursos disponíveis no iOS 9 novos e modificados para desenvolvedores do xamarin.
ms.prod: xamarin
ms.assetid: 4D71BBD9-B948-4B59-9AF5-F199C51CBEB3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 10ed9154b92e6f13dd71f83cf4fed47585dc795f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30781266"
---
# <a name="introduction-to-ios-9"></a>Introdução ao iOS 9

_Este artigo apresenta todas as APIs e recursos disponíveis no iOS 9 novos e modificados para desenvolvedores do xamarin._

![](images/ios9-sml.png "O logotipo do iOS 9")

Apple adicionou várias novas APIs e serviços no iOS 9 junto com muitas melhorias em recursos existentes.

## <a name="3d-touch"></a>Toque 3D

Novo para iOS 9 e o iPhone 6s e iPhone 6s Plus, 3D Touch adiciona gestos confidenciais pressão para seus aplicativos iOS. Com 3D Touch, um aplicativo de iPhone agora é capaz de dizer não apenas que o usuário estiver tocando a tela do dispositivo, ele pode também quanto o usuário é exercendo de pressão de detecção e responder aos níveis diferentes de pressão.

Toque 3D fornece os seguintes recursos para seu aplicativo:

- **Sensibilidade à pressão** - aplicativos agora podem medir como rígida ou leve o usuário estiver tocando a vantagem de tela e execute essas informações. Por exemplo, um aplicativo de pintura pode fazer uma linha mais espessa ou mais fino com base em como o usuário é tocar na tela.
- **Inspecionar e Pop** -seu aplicativo pode agora permitem que o usuário interaja com seus dados sem precisar navegar fora de seu contexto atual. Pressionando rígido na tela, eles podem *inspecionar* no item estão interessados (como visualizar uma mensagem). Pressionando mais difícil, eles podem *Pop* para o item.
- **Ações rápidas** -pensar de ações rápidas como os menus contextuais que podem ser exibido o quando um usuário clica com o botão direito em um item em um aplicativo de área de trabalho. Usando ações rápidas, você pode adicionar comuns, rápida e fácil para acesso atalhos para funções em seu aplicativo do ícone de tela inicial no dispositivo iOS.

Para obter mais informações, consulte nosso [Introdução ao 3D Touch](~/ios/platform/3d-touch.md) guia.

## <a name="app-transport-security"></a>Segurança de transporte do aplicativo

Novo para iOS 9, a segurança de transporte do aplicativo (ATS) impõe conexões seguras entre os recursos de internet (como o servidor de back-end do aplicativo) e seu aplicativo. ATS garante que todas as comunicações de internet esteja em conformidade para proteger a conexão práticas recomendadas, impedindo assim a divulgação acidental de informações confidenciais diretamente por meio de seu aplicativo ou uma biblioteca que está consumindo.

Como ATS é habilitada por padrão em aplicativos criados para o iOS 9 e OS X 10.11 (El Capitan), todas as conexões usando [NSUrlConnection](https://developer.xamarin.com/api/type/Foundation.NSUrlConnection/), [CFUrl](https://developer.xamarin.com/api/type/CoreFoundation.CFUrl/) ou [NSUrlSession](https://developer.xamarin.com/api/type/Foundation.NSUrlSession/) estará sujeito Requisitos de segurança ATS. Se as conexões não atender a esses requisitos, ele falhará com uma exceção.

Para obter mais informações sobre ATS, consulte nosso [segurança de transporte do aplicativo](~/ios/app-fundamentals/ats.md) guia.

<a name="multitasking" />

## <a name="multitasking-for-ipad"></a>Multitarefa para iPad

Com o iOS 9, Apple oferece suporte multitarefa dois aplicativos em execução ao mesmo tempo no iPad específico hardware adicional. Como resultado, seus aplicativos xamarin não podem assumir que eles são o aplicativo somente em execução em um determinado momento ou que eles têm acesso a tela inteira ou os recursos do dispositivo.

Multitarefa para iPad é suportada por meio dos seguintes recursos:

- **Slide sobre** -permite que o usuário executar um aplicativo iOS segundo temporariamente em um slide out painel (ou no lado esquerdo ou direito da tela com base na direção de idioma) que abrange aproximadamente 25% do aplicativo principal em execução no momento. Slide sobre só está disponível em um iPad Pro, iPad ar, iPad 2 ar, iPad 2 Mini, iPad Mini 3 ou iPad Mini 4.
- **Modo divisão** -em hardware de iPad com suporte (iPad 2 ar, iPad Mini 4 e iPad Pro somente), o usuário pode escolher um segundo aplicativo e executá-lo lado a lado com o aplicativo em execução no momento em um modo de tela de divisão. O usuário pode controlar o percentual da tela principal que ocupa de cada aplicativo.
- **Figura Figura** - para aplicativos que reproduzir conteúdo de vídeo, o vídeo pode ser executado em uma janela móvel e redimensionada que flutua sobre os outros aplicativos em execução no dispositivo iOS. O usuário tem controle total sobre o tamanho e a posição da janela. Imagem na imagem só está disponível em um iPad Pro, iPad ar, iPad 2 ar, iPad 2 Mini, iPad Mini 3 ou iPad Mini 4.

Para obter mais informações sobre a nova capacidade de multitarefa do iOS 9, consulte nosso [multitarefa para iPad](~/ios/platform/multitasking.md) guia.

## <a name="new-contacts-and-contacts-ui-frameworks"></a>Novos contatos e estruturas de interface do usuário de contatos

Com a introdução do iOS 9, Apple lançou duas novas estruturas, [contatos](https://developer.xamarin.com/api/namespace/Contacts/) e [ContactsUI](https://developer.xamarin.com/api/namespace/ContactsUI/), que substitui o catálogo de endereços e estruturas do catálogo de endereço da interface do usuário usados pelo iOS 8 e versões anteriores.

Essas estruturas de novo, orientada a objeto fornecem o seguinte:

- **Contatos** – xamarin fornece acesso às informações de contato do usuário. Como a maioria dos aplicativos exigem apenas acesso somente leitura, essa estrutura foi otimizada para acesso de thread safe, somente leitura.
- **ContactsUI** – elementos de interface de usuário fornece xamarin para exibir, editar, selecione e criar contatos em dispositivos iOS.

Para obter mais informações, consulte nosso [contatos e interface de usuário de contatos](~/ios/platform/contacts.md) documentação.


## <a name="new-search-apis"></a>Novas APIs de pesquisa

A pesquisa foi expandida no iOS 9 para fornecer excelentes maneiras de acessar informações dentro de seu aplicativo xamarin. Usando as novas APIs de pesquisa, você pode fazer conteúdo do aplicativo podem ser pesquisados por meio de destaque e Safari resultados da pesquisa, entrega e Siri lembretes e sugestões. Isso permite que os usuários acessem rapidamente informações profundas dentro de seu aplicativo e atividades.

Além disso, as novas APIs de pesquisa facilitam a integração de pesquisa em seu aplicativo sem experiência de implementação de pesquisa anterior. Por isso, a Apple declarações que geralmente leva apenas algumas horas para tornar o conteúdo de um aplicativo iOS 9 universalmente podem ser pesquisados usando a pesquisa do aplicativo.

Para obter mais informações, consulte nosso [aprimoramentos da pesquisa](~/ios/platform/search/index.md) documentação.

## <a name="new-stack-view"></a>Nova exibição da pilha

O controle de exibição de pilha ([UIStackView](https://developer.xamarin.com/api/type/UIKit.UIStackView/)) aproveita o poder do Layout automático e Classes de tamanho para gerenciar uma pilha de sub-visualizações (horizontal ou verticalmente) que responde dinamicamente ao tamanho de tela e a orientação do dispositivo iOS.

Usando o controle de exibição de pilha, a quantidade de trabalho necessário para o layout de que uma interface do usuário é bastante reduzida. O layout de todos os sub-visualizações anexado a um modo de exibição de pilha são gerenciados automaticamente com base nas propriedades de desenvolvedor definido como eixo, distribuição, alinhamento e espaçamento.

Para obter mais informações, consulte nosso [Introdução ao modo de exibição de pilha](~/ios/user-interface/controls/uistackview.md) documentação.


## <a name="collection-view-changes"></a>Alterações de exibição de coleção

No iOS 9, a exibição de coleção ([UICollectionView](https://developer.xamarin.com/api/type/UIKit.UICollectionView/)) agora oferece suporte a arrasta a reordenação de itens para fora da caixa, adicionando um novo reconhecedor de gestos padrão e vários novos métodos de suporte.

Usando esses novos métodos, você pode facilmente implementar arrastar para reordenar na exibição de coleção e tem a opção de personalizar a aparência de itens durante qualquer estágio do processo de reordenação.

Para obter mais informações sobre as alterações de exibição de coleção para iOS 9, consulte nosso [alterações de exibição de coleção](~/ios/user-interface/controls/uicollectionview.md) guia.

## <a name="game-enhancements"></a>Aprimoramentos de jogo

Com o iOS 9, Apple fez várias melhorias tecnológicas para as APIs de jogos que tornam mais fácil de implementar os gráficos de jogos e de áudio em seu aplicativo xamarin. Isso inclui os dois facilitar o desenvolvimento por meio de estruturas de alto nível e utilizando a capacidade de GPU do dispositivo iOS para maior velocidade e recursos de gráfico com aprimoramentos de baixo nível.

Isso inclui GameplayKit, ReplayKit, modelo de e/s, MetalKit e sombreadores de desempenho do sistema operacional junto com os novos e aprimorados recursos do sistema operacional, SceneKit e SpriteKit.

Para obter mais informações, consulte nosso [aprimoramentos de jogo](~/ios/platform/gaming/index.md) documentação.

## <a name="homekit-framework-changes"></a>Alterações de estrutura HomeKit

O [HomeKit](https://developer.xamarin.com/api/namespace/HomeKit/) framework, introduzido no iOS 8, fornece a capacidade de configurar e controlar vários acessórios de HomeKit habilitado (como luzes automatizadas, bloqueios de porta e portas de garagem garagens) de um aplicativo xamarin. Além de ser fácil de instalar e configurar, HomeKit Acessórios podem ser controlados por meio de comandos Siri falados.

No iOS 9, Apple tem facilitou a instalação, expandido de tipos de Acessórios tem suporte e fornecidas mais interações Acessórios (como controlar um acessório remotamente via iCloud).

Para obter mais informações, consulte nosso [Introdução ao HomeKit](~/ios/platform/homekit.md), [HomeKitIntro iOS aplicativo de exemplo](https://developer.xamarin.com/samples/monotouch/HomeKit/HomeKitIntro/) da Apple [HomeKit](https://developer.apple.com/homekit/) documentação.

## <a name="handoff-framework-changes"></a>Alterações de estrutura de entrega

Entrega (também conhecido como continuidade) foi introduzida pela Apple no iOS 8 e OS X Yosemite (10.10) como uma maneira para que o usuário iniciar uma atividade em um de seus dispositivos (iOS ou Mac) e continuar mesmo que a atividade em outro seus dispositivos (conforme identificado pelo iClou do usuário movidos conta).

Entrega foi expandida no iOS 9 para também dar suporte a novos e aprimorados recursos de pesquisa. Para obter mais informações, consulte nosso [aprimoramentos da pesquisa](~/ios/platform/search/index.md) documentação. Para obter mais informações sobre o uso de entrega, consulte nosso [Introdução à entrega](~/ios/platform/handoff.md) documentação.

## <a name="new-extension-points"></a>Novos pontos de extensão

No iOS 8, a Apple introduziu extensões — bibliotecas que são apresentadas pelo sistema operacional padrão contextos, como no Centro de notificação, quando o usuário solicita um teclado, ou quando ele estiver editando uma foto.

Com iOS 9, Apple amplia o suporte da extensão, fornecendo vários novos _pontos de extensão_ que definir políticas de uso e fornece APIs para trabalhar em uma determinada área da seguinte maneira:

- **Novo ponto de extensão de unidade de áudio** – usem este ponto de extensão para fornecer os efeitos de áudio, instrumentos musicais, geradores de som, etc. para uso em outros aplicativos de host de unidade de áudio (como GarageBand). Esse ponto de extensão também permite que a venda _unidades áudio_ (áudio plug-ins) na loja de aplicativos.
- **Novo ponto de extensão de manutenção de índice** — Use esse ponto de extensão para oferecer suporte a reindexação de dados do aplicativo sem a necessidade de uma reinicialização do aplicativo.
- **Novos pontos de extensão de rede** (eles exigem a permissão especial da Apple):
    - **Extensão do provedor de Proxy de aplicativo** — Use esse ponto de extensão para implementar um proxy de rede de cliente personalizada transparente.
    - **Provedor de dados de filtro / extensão do provedor de controle de filtro** -usar esses pontos de extensão para implementar a filtragem no dispositivo de conteúdo de rede dinâmica.
    - **Extensão do provedor de encapsulamento de pacote** — Use esse ponto de extensão para implementar um protocolo do lado do cliente de encapsulamento de VPN personalizado.
- **Novos pontos de extensão do Safari**:
    - **Bloqueio de extensão de conteúdo** — Use esse ponto de extensão para definir uma lista de conteúdo bloqueado não será exibida quando o usuário estiver navegando na web.
    - **Compartilhado Links extensão** — usem este ponto de extensão para permitir a exibição de conteúdo do aplicativo compartilhado Links do Safari.

Para obter mais informações, consulte nosso [Introdução a extensões](~/ios/platform/extensions.md) da Apple [guia de programação de extensão de aplicativo](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214) documentação.

## <a name="keychain-enhancements"></a>Aprimoramentos do conjunto de chaves

No iOS 9, a Apple aprimorou o conjunto de chaves para fornecer um novo tipo de chave de criptografia para o Enclave segura e mais opções de proteção do item da seguinte maneira:

- Uma nova restrição de ID de toque que invalida itens do conjunto de chaves quando o banco de dados de impressão digital é modificado.
- Novas restrições que permitem a criação de entradas de listas de controle de acesso com ID de toque ou senha apenas.
- Um novo contexto de autenticação que permite a invocação de autenticação separada do `SecItem` chamadas.
- Acesse entropia de lista de controle (usando a opção de senha de aplicativo) para criptografia de item de conjunto de chaves fornecido pelo aplicativo.
- Suporte para gerar e usar chaves dentro do enclave segura (por meio de `kSecAttrTokenIDSecureEnclave` atributo).

Para obter mais informações, consulte nosso [Introdução à ID de toque](~/ios/platform/touchid.md) documentação.


## <a name="right-to-left-language-support"></a>Suporte a idiomas da direita para esquerda

No iOS 9, a Apple fez apresentar uma interface do usuário invertidos mais fácil do que nunca, fornecendo suporte completo para idiomas da direita para esquerda. Isso inclui o seguinte:

- Padrão [UIKit](https://developer.xamarin.com/api/namespace/UIKit/) controles mudarão automaticamente com base nas configurações de idioma e localidade de dispositivos iOS direita para esquerda.
- O [UIView](https://developer.xamarin.com/api/type/UIKit.UIView/) classe fornece os atributos que permitem que você defina como um determinado modo de exibição deve ser exibidos quando invertida à direita para esquerda.
- A capacidade de inverter uma imagem de forma programática usando o [FlipsForRightToLeftLayoutDirection](https://developer.xamarin.com/api/property/UIKit.UIImage.FlipsForRightToLeftLayoutDirection/) propriedade o [UIImage](https://developer.xamarin.com/api/type/UIKit.UIImage/) classe.

Para obter mais informações, consulte da Apple [idiomas da direita para esquerda do suporte](https://developer.apple.com/library/prerelease/ios/documentation/MacOSX/Conceptual/BPInternational/SupportingRight-To-LeftLanguages/SupportingRight-To-LeftLanguages.html#//apple_ref/doc/uid/10000171i-CH17) documentação.



## <a name="additional-framework-changes"></a>Alterações de estrutura adicionais

Além das alterações principais que abordamos acima, a Apple fez modificações e melhorias para várias estruturas existentes para iOS 9, incluindo o seguinte:

- Estrutura AV Foundation
- Estrutura AVKit
- Estrutura CloudKit
- Estrutura de base
- Handoff Framework
- Estrutura HealthKit
- Estrutura HomeKit
- Estrutura de autenticação local
- Estrutura MapKit
- Estrutura PassKit
- Estrutura de serviços do Safari
- Estrutura UIKit

Para obter mais informações, consulte nosso [adicionais iOS 9 Framework alterações](~/ios/platform/introduction-to-ios9/additional-framework-changes.md) documentação.

## <a name="deprecated-apis-and-functions"></a>Funções e APIs obsoletas

Apple tem substituídas as seguintes APIs e funções no iOS 9:

- **Endereço livro e interface do usuário do catálogo de endereços** -essas APIs foram substituídos pelas estruturas de contato e interface de usuário do contato. Para obter mais informações, consulte nosso [contatos e interface de usuário de contatos](~/ios/platform/contacts.md) documentação.
- **CBCentralManager** - o `RetrievePeripherals` e `RetrieveConnectedPeripherals` métodos de `CBCentralManager` classe foram removidos do iOS 9. Chamar esses métodos fará com que um aplicativo falhar ao emparelhar um acessório ou na inicialização do aplicativo.
- **FetchAllChanges** - o `FetchAllChanges` do `CKFetchRecordChangesOperation` classe foi depreciada e será removida em iOS 9.
- **Media Player** -estrutura do Media Player foi preterido no iOS 9. Use AVKit ou AV Foundation APIs.

Para obter uma lista completa de substituições específicas de API, consulte da Apple [iOS 9.0 comparações API](https://developer.apple.com/library/prerelease/ios/releasenotes/General/iOS90APIDiffs/index.html#//apple_ref/doc/uid/TP40016222) documentação.

## <a name="ios-9-sample-apps"></a>Aplicativos de exemplo do iOS 9

Temos alguns [exemplos do iOS 9 específico](https://developer.xamarin.com/samples/ios/iOS9/) para começar:

- [AstroLayout](https://github.com/xamarin/monotouch-samples/tree/master/ios9/AstroLayout)
- [CollectionView](https://github.com/xamarin/monotouch-samples/tree/master/ios9/CollectionView)
- [MetalPerformanceShadersHelloWorld](https://developer.xamarin.com/samples/monotouch/ios9/MetalPerformanceShadersHelloWorld/)
- [MusicMotion](https://developer.xamarin.com/samples/monotouch/ios9/MusicMotion/)
- [PhotoProgress](https://developer.xamarin.com/samples/monotouch/ios9/PhotoProgress/)
- [SegueCatalog](https://developer.xamarin.com/samples/monotouch/ios9/SegueCatalog/)
- [StackView](https://github.com/xamarin/monotouch-samples/tree/master/ios9/StackView)
- [StickyCorners](https://github.com/xamarin/monotouch-samples/tree/master/ios9/StickyCorners)

Consulte também as partes do iOS desses exemplos (complementar Mac OS X versões vindo!):

- [AgentsCatalog](https://github.com/xamarin/mac-ios-samples/tree/master/AgentsCatalog)
- [MetalKitEssentials](https://github.com/xamarin/mac-ios-samples/tree/master/MetalKitEssentials)



## <a name="related-links"></a>Links relacionados

- [Exemplos do iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [Introdução ao 3D Touch](~/ios/platform/3d-touch.md)
- [Segurança de transporte de aplicativo](~/ios/app-fundamentals/ats.md)
- [Multitarefa para iPad](~/ios/platform/multitasking.md)
- [Contatos e contatos da interface do usuário](~/ios/platform/contacts.md)
- [Novas APIs de pesquisa](~/ios/platform/search/index.md)
- [Introdução ao modo de exibição de pilha](~/ios/user-interface/controls/uistackview.md)
- [Alterações de exibição de coleção](~/ios/user-interface/controls/uicollectionview.md)
- [Aprimoramentos de jogos](~/ios/platform/gaming/index.md)
- [Introdução ao HomeKit](~/ios/platform/homekit.md)
- [Introdução à entrega](~/ios/platform/handoff.md)
- [Alterações de estrutura adicionais do iOS 9](~/ios/platform/introduction-to-ios9/additional-framework-changes.md)
- [Solução de problemas](~/ios/platform/introduction-to-ios9/troubleshooting.md)
- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [O que há de novo no iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Atualizar seus aplicativos xamarin para iOS9 (vídeo)](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
