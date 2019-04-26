---
title: Introdução ao iOS 9
description: Este artigo apresenta todas as APIs e recursos disponíveis no iOS 9 novos e modificados para desenvolvedores do xamarin. IOS.
ms.prod: xamarin
ms.assetid: 4D71BBD9-B948-4B59-9AF5-F199C51CBEB3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: d0ad25a1ecff7262b9b4b41a5f9d73a5931bbd1c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60946219"
---
# <a name="introduction-to-ios-9"></a>Introdução ao iOS 9

_Este artigo apresenta todas as APIs e recursos disponíveis no iOS 9 novos e modificados para desenvolvedores do xamarin. IOS._

![](images/ios9-sml.png "O logotipo do iOS 9")

Apple adicionou várias novas APIs e serviços no iOS 9, junto com muitos aprimoramentos em recursos existentes.

## <a name="3d-touch"></a>3D Touch

Novo no iOS 9 e o iPhone 6s e iPhone 6s Plus, 3D Touch adiciona gestos confidenciais de pressão para seus aplicativos iOS. Com o 3D Touch, um aplicativo de iPhone agora é capaz de não apenas dizer que o usuário toca a tela do dispositivo, pode também sentido quanto o usuário é exercendo pressão de e responder aos níveis de pressão diferentes.

3D Touch oferece os seguintes recursos para seu aplicativo:

- **Sensibilidade à pressão** – aplicativos agora podem medir como rígida ou luz o usuário está tocando a tela e aproveite essa informação. Por exemplo, um aplicativo de pintura pode fazer uma linha mais espessa ou mais finos com base em como o usuário está tocando a tela.
- **Inspecionar e Pop** -seu aplicativo agora pode permitir ao usuário interagir com seus dados sem precisar navegar fora de seu contexto atual. Ao pressionar um disco rígido na tela, eles podem *inspecionar* no item que ele está interessado (como visualizar uma mensagem). Pressionando mais difícil, eles podem *Pop* para o item.
- **Ações rápidas** -pensar de ações rápidas, como os menus contextuais que podem ser removidos-up quando um usuário clica com o botão direito em um item em um aplicativo da área de trabalho. Usando ações rápidas, você pode adicionar comuns, rápido e fácil aos atalhos de acesso a funções em seu aplicativo do ícone de tela inicial no dispositivo iOS.

Para obter mais informações, consulte nosso [Introdução ao 3D Touch](~/ios/platform/3d-touch.md) guia.

## <a name="app-transport-security"></a>Segurança do transporte de aplicativo

Novo para o iOS 9, a segurança de transporte de aplicativo (ATS) impõe conexões seguras entre recursos da internet (como o servidor de back-end do aplicativo) e seu aplicativo. ATS garante que todas as comunicações de internet está em conformidade para proteger a conexão as práticas recomendadas, impedindo assim a divulgação acidental de informações confidenciais diretamente por meio de seu aplicativo ou uma biblioteca que ele está consumindo.

Uma vez que o ATS é habilitado por padrão nos aplicativos criados para o iOS 9 e OS X 10.11 (El Capitan), todas as conexões usando [NSUrlConnection](xref:Foundation.NSUrlConnection), [CFUrl](xref:CoreFoundation.CFUrl) ou [NSUrlSession](xref:Foundation.NSUrlSession) estará sujeito Requisitos de segurança ATS. Se suas conexões não atenderem a esses requisitos, ele falhará com uma exceção.

Para obter mais informações sobre o ATS, consulte nosso [segurança de transporte de aplicativo](~/ios/app-fundamentals/ats.md) guia.

<a name="multitasking" />

## <a name="multitasking-for-ipad"></a>Multitarefa para iPad

Com o iOS 9, a Apple adicionou suporte de multitarefa para dois aplicativos em execução ao mesmo tempo em um hardware específico iPad. Como resultado, seus aplicativos xamarin. IOS não podem assumir que eles são o único aplicativo em execução em um determinado momento ou que eles têm acesso para a tela inteira ou os recursos do dispositivo.

Multitarefa para iPad é suportada pelos seguintes recursos:

- **Slide sobre** -permite que o usuário executar temporariamente um segundo aplicativo do iOS em um slide do painel (seja no lado esquerdo ou direito da tela com base na direção de idioma) que cobre aproximadamente 25% do aplicativo principal em execução no momento. Deslizar sobre está disponível somente em um iPad Pro, iPad Air, iPad Air 2, iPad 2 Mini, iPad Mini 3 ou 4 Mini do iPad.
- **Modo de exibição dividido** -no hardware do iPad com suporte (iPad Air 2, iPad Mini 4 e iPad Pro somente), o usuário pode escolher um segundo aplicativo e executá-lo lado a lado com o aplicativo em execução no momento em um modo de tela de divisão. O usuário pode controlar o percentual da tela principal que cada aplicativo ocupa.
- **A imagem na imagem** – para aplicativos que reproduzir conteúdo de vídeo, o vídeo pode agora ser reproduzido em uma janela redimensionável e móvel que flutua sobre os outros aplicativos em execução no momento no dispositivo iOS. O usuário tem controle total sobre o tamanho e a posição dessa janela. A imagem na imagem está disponível somente em um iPad Pro, iPad Air, iPad Air 2, iPad 2 Mini, iPad Mini 3 ou 4 Mini do iPad.

Para obter mais informações sobre as novas capacidades de multitarefa do iOS 9, consulte nosso [multitarefa para iPad](~/ios/platform/multitasking.md) guia.

## <a name="new-contacts-and-contacts-ui-frameworks"></a>Novos contatos e estruturas de interface do usuário de contatos

Com a introdução do iOS 9, a Apple lançou duas novas estruturas [contatos](xref:Contacts) e [ContactsUI](xref:ContactsUI), que substitui o catálogo de endereços existente e estruturas do catálogo de endereço da interface do usuário usados pelo iOS 8 e versões anteriores.

Essas estruturas novas orientada a objeto, fornecem o seguinte:

- **Contatos** – xamarin. IOS fornece acesso às informações de contato do usuário. Como a maioria dos aplicativos exigem apenas o acesso somente leitura, essa estrutura foi otimizada para acesso de thread-safe, somente leitura.
- **ContactsUI** – elementos fornece interface do usuário de xamarin. IOS para exibir, editar, selecione e crie contatos em dispositivos iOS.

Para obter mais informações, consulte nosso [contatos e interface do usuário de contatos](~/ios/platform/contacts.md) documentação.


## <a name="new-search-apis"></a>Novas APIs de pesquisa

Pesquisa foi expandida no iOS 9 para fornecer novas formas de acessar as informações dentro de seu aplicativo xamarin. IOS. Usando as novas APIs de pesquisa, é possível tornar conteúdo seu aplicativo pesquisável por meio de destaque e Safari resultados da pesquisa, entrega e lembretes Siri e sugestões. Isso permite que os usuários acessem rapidamente atividades e informações profundas dentro de seu aplicativo.

Além disso, as novas APIs de pesquisa de tornar mais fácil integrar a pesquisa em seu aplicativo sem experiência de implementação de pesquisa anterior. Por isso, a Apple declarações que normalmente leva algumas horas para tornar o conteúdo de um aplicativo iOS 9 universalmente pesquisáveis usando a pesquisa do aplicativo.

Para obter mais informações, consulte nosso [aperfeiçoamentos feitos na pesquisa](~/ios/platform/search/index.md) documentação.

## <a name="new-stack-view"></a>Novo modo de exibição de pilha

O controle de exibição de pilha ([UIStackView](xref:UIKit.UIStackView) aproveita o poder do Layout automático e Classes de tamanho para gerenciar uma pilha de subexibições (horizontal ou verticalmente) que responde dinamicamente ao tamanho de tela e a orientação do dispositivo iOS.

Usando o controle de exibição de pilha, a quantidade de trabalho necessário para layout de que uma interface do usuário é bastante reduzida. O layout de todas as subexibições anexado a uma exibição de pilha são gerenciados automaticamente com base nas propriedades de desenvolvedor definido como eixo, distribuição, alinhamento e espaçamento.

Para obter mais informações, consulte nosso [Introdução à exibição de pilha](~/ios/user-interface/controls/uistackview.md) documentação.


## <a name="collection-view-changes"></a>Alterações de exibição de coleção

No iOS 9, a exibição de coleção ([UICollectionView](xref:UIKit.UICollectionView) agora dá suporte a arrasta a reorganização de itens fora da caixa, adicionando um reconhecedor de gestos padrão novo e vários novos métodos de suporte.

Usando esses novos métodos, você pode facilmente implementar o arrastar para reordenar no modo de exibição de coleção e tem a opção de personalizar a aparência de itens durante qualquer estágio do processo de reordenação.

Para obter mais informações sobre as alterações de exibição de coleção para iOS 9, consulte nosso [alterações de exibição de coleção](~/ios/user-interface/controls/uicollectionview.md) guia.

## <a name="game-enhancements"></a>Aprimoramentos de jogo

Com o iOS 9, Apple fez várias melhorias tecnológicas para as APIs de jogos que tornam mais fácil de implementar os gráficos de jogo e áudio em seu aplicativo xamarin. IOS. Isso inclui tanto a facilidade de desenvolvimento por meio de estruturas de alto nível e aproveitando o poder de GPU do dispositivo iOS para maior velocidade e capacidades gráficas com melhorias de nível baixo.

Isso inclui GameplayKit, ReplayKit, e/s do modelo, MetalKit e sombreadores de desempenho do sistema operacional, juntamente com recursos novos e aprimorados de Metal, SceneKit e SpriteKit.

Para obter mais informações, consulte nosso [aprimoramentos de jogo](~/ios/platform/gaming/index.md) documentação.

## <a name="homekit-framework-changes"></a>Alterações de estrutura HomeKit

O [HomeKit](xref:HomeKit) estrutura, introduzida no iOS 8, fornece a capacidade de configurar e controlar várias Acessórios do HomeKit habilitado (por exemplo, luzes automatizadas, bloqueios de porta e portas de garagens garagem) de um aplicativo xamarin. IOS. Além de ser fácil de instalar e configurar, Acessórios do HomeKit podem ser controlados por meio de comandos falados do Siri.

No iOS 9, Apple tem mais fácil instalação, expandido de tipos de Acessórios, suporte e fornecidas mais interações Acessórios (como controlar um acessório remotamente por meio do iCloud).

Para obter mais informações, consulte nosso [Introdução ao HomeKit](~/ios/platform/homekit.md), [aplicativo de exemplo do iOS HomeKitIntro](https://developer.xamarin.com/samples/monotouch/HomeKit/HomeKitIntro/) da Apple [HomeKit](https://developer.apple.com/homekit/) documentação.

## <a name="handoff-framework-changes"></a>Alterações de estrutura de entrega

Entrega (também conhecido como continuidade) foi introduzida pela Apple no iOS 8 e OS X Yosemite (10.10) como uma maneira para o usuário iniciar uma atividade em um dos seus dispositivos (iOS ou Mac) e continuar mesma que a atividade em outro dispositivo (conforme identificado pelo iClou do usuário 1!d conta).

Entrega foi expandida no iOS 9 para também dar suporte a novos e aprimorados recursos de pesquisa. Para obter mais informações, consulte nosso [aperfeiçoamentos feitos na pesquisa](~/ios/platform/search/index.md) documentação. Para obter mais informações sobre como usar a entrega, consulte nosso [Introdução à entrega](~/ios/platform/handoff.md) documentação.

## <a name="new-extension-points"></a>Novos pontos de extensão

No iOS 8, a Apple introduziu extensões — as bibliotecas que são apresentadas pelo sistema operacional em contextos padrão, como no Centro de notificações, quando o usuário solicita um teclado, ou quando eles estão editando uma foto.

Com o iOS 9, Apple está estendendo o suporte à extensão, fornecendo vários novos _pontos de extensão_ que definir políticas de uso e fornece APIs para trabalhar dentro de uma determinada área da seguinte maneira:

- **Novo ponto de extensão de unidade de áudio** – Use esse ponto de extensão para fornecer os efeitos de áudio, instrumentos musicais, geradores de som, etc. para uso em outros aplicativos de host de unidade de áudio (como GarageBand). Esse ponto de extensão também permite que você venda _unidades de áudio_ (plug-ins áudio) em que a App Store.
- **Novo ponto de extensão de manutenção de índice** — Use esse ponto de extensão para oferecer suporte a reindexação de dados do aplicativo sem a necessidade de reiniciar um aplicativo.
- **Novos pontos de extensão de rede** (eles necessitam de permissão especial da Apple):
    - **Extensão do provedor de Proxy de aplicativo** — Use esse ponto de extensão para implementar um proxy personalizado rede transparente do lado do cliente.
    - **Provedor de dados de filtro / extensão do provedor de controle de filtro** -usar esses pontos de extensão para implementar a dinâmica de rede de filtragem de conteúdo no dispositivo.
    - **Extensão do provedor de encapsulamento de pacote** — Use esse ponto de extensão para implementar uma VPN personalizada do cliente do protocolo de túnel.
- **Novos pontos de extensão do Safari**:
    - **Bloqueio de extensão de conteúdo** — Use esse ponto de extensão para definir uma lista de conteúdo bloqueado não será exibida quando o usuário está navegando na web.
    - **Compartilhado a extensão de Links** — Use esse ponto de extensão para habilitar a exibição de conteúdo do seu aplicativo nos Links de compartilhados do Safari.

Para obter mais informações, consulte nosso [Introdução às extensões](~/ios/platform/extensions.md) da Apple [guia de programação de extensão de aplicativo](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214) documentação.

## <a name="keychain-enhancements"></a>Aprimoramentos do conjunto de chaves

No iOS 9, a Apple aprimorou o conjunto de chaves para fornecer um novo tipo de chave de criptografia para o Enclave seguro e mais opções de proteção do item da seguinte maneira:

- Uma nova restrição de ID de toque que invalida os itens do conjunto de chaves quando o banco de dados de impressão digital é modificado.
- Novas restrições que permitem a criação de entradas da lista de controle de acesso com a ID de toque ou senha apenas.
- Um novo contexto de autenticação que permite que você invoque autenticação separada do `SecItem` chamadas.
- Acesse a entropia de lista de controle (usando a opção de senha de aplicativo) para criptografia de item de conjunto de chaves fornecido pelo aplicativo.
- Suporte para gerar e usar chaves dentro o enclave seguro (por meio de `kSecAttrTokenIDSecureEnclave` atributo).

Para obter mais informações, consulte nosso [Introdução ao Touch ID](~/ios/platform/touchid.md) documentação.


## <a name="right-to-left-language-support"></a>Suporte a idiomas da direita para esquerda

No iOS 9, a Apple fez apresentar uma interface do usuário invertida mais fácil do que nunca, fornecendo suporte completo para idiomas da direita para esquerda. Isso inclui o seguinte:

- Standard [UIKit](xref:UIKit) controles automaticamente inverterá direita para a esquerda com base nas configurações de localidade e idioma de dispositivos do iOS.
- O [UIView](xref:UIKit.UIView) classe fornece atributos que permitem que você defina como um determinado modo de exibição deve aparecer quando invertida à direita para esquerda.
- A capacidade de inverter uma imagem por meio de programação usando o [FlipsForRightToLeftLayoutDirection](xref:UIKit.UIImage.FlipsForRightToLeftLayoutDirection) propriedade da [UIImage](xref:UIKit.UIImage) classe.

Para obter mais informações, consulte da Apple [idiomas da direita para esquerda Supporting](https://developer.apple.com/library/prerelease/ios/documentation/MacOSX/Conceptual/BPInternational/SupportingRight-To-LeftLanguages/SupportingRight-To-LeftLanguages.html#//apple_ref/doc/uid/10000171i-CH17) documentação.



## <a name="additional-framework-changes"></a>Alterações de estrutura adicionais

Além das principais alterações que tenhamos abordado acima, a Apple fez modificações e melhorias nas várias estruturas existentes para o iOS 9, incluindo o seguinte:

- Estrutura da Base de AV
- Estrutura AVKit
- Estrutura do CloudKit
- Estrutura Foundation
- Estrutura de entrega
- Estrutura HealthKit
- Estrutura HomeKit
- Estrutura de autenticação local
- Estrutura MapKit
- PassKit Framework
- Estrutura de serviços do Safari
- Estrutura UIKit do

Para obter mais informações, consulte nosso [alterações de estrutura adicionais do iOS 9](~/ios/platform/introduction-to-ios9/additional-framework-changes.md) documentação.

## <a name="deprecated-apis-and-functions"></a>Funções e APIs preteridas

A Apple preteriu a APIs e funções no iOS 9 a seguir:

- **Livro e a interface do usuário do catálogo de endereços de endereço** -essas APIs foram substituídas por estruturas de contato e de contato da interface do usuário. Para obter mais informações, consulte nosso [contatos e interface do usuário de contatos](~/ios/platform/contacts.md) documentação.
- **CBCentralManager** - o `RetrievePeripherals` e `RetrieveConnectedPeripherals` métodos do `CBCentralManager` classe foram removidos no iOS 9. Chamar esses métodos fará com que um aplicativo falha ao emparelhar um acessório ou na inicialização do aplicativo.
- **FetchAllChanges** - o `FetchAllChanges` da `CKFetchRecordChangesOperation` classe foi depreciado e será removido no iOS 9.
- **O Player de mídia** -framework o Player de mídia foi preterido no iOS 9. Use AVKit ou AV Foundation APIs.

Para obter uma lista completa de reprovações de API específicas, consulte da Apple [iOS 9.0 diferenças de API](https://developer.apple.com/library/prerelease/ios/releasenotes/General/iOS90APIDiffs/index.html#//apple_ref/doc/uid/TP40016222) documentação.

## <a name="ios-9-sample-apps"></a>Aplicativos de exemplo do iOS 9

Temos algumas [amostras do iOS 9 específicas](https://developer.xamarin.com/samples/ios/iOS9/) para começar a usar:

- [AstroLayout](https://github.com/xamarin/monotouch-samples/tree/master/ios9/AstroLayout)
- [CollectionView](https://github.com/xamarin/monotouch-samples/tree/master/ios9/CollectionView)
- [MetalPerformanceShadersHelloWorld](https://developer.xamarin.com/samples/monotouch/ios9/MetalPerformanceShadersHelloWorld/)
- [MusicMotion](https://developer.xamarin.com/samples/monotouch/ios9/MusicMotion/)
- [PhotoProgress](https://developer.xamarin.com/samples/monotouch/ios9/PhotoProgress/)
- [SegueCatalog](https://developer.xamarin.com/samples/monotouch/ios9/SegueCatalog/)
- [StackView](https://github.com/xamarin/monotouch-samples/tree/master/ios9/StackView)
- [StickyCorners](https://github.com/xamarin/monotouch-samples/tree/master/ios9/StickyCorners)

Confira também as partes do iOS desses exemplos (complementar Mac OS X versões chegando!):

- [AgentsCatalog](https://github.com/xamarin/mac-ios-samples/tree/master/AgentsCatalog)
- [MetalKitEssentials](https://github.com/xamarin/mac-ios-samples/tree/master/MetalKitEssentials)



## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
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
- [Atualizar seus aplicativos xamarin. IOS para iOS9 (vídeo)](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
