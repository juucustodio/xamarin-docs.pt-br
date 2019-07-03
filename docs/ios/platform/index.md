---
title: Visão geral dos recursos de plataforma do iOS
description: Este documento leva a vários guias que descrevem os recursos introduzidos em várias versões do iOS e outros recursos da plataforma iOS.
ms.prod: xamarin
ms.assetid: 9F6A27E5-8A87-ADE2-D1EF-5684E7B8C999
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/25/2018
ms.openlocfilehash: 6703e922a628504e0afdcf56533d74741131581a
ms.sourcegitcommit: a6ba6ed086bcde4f52fb05f83c59c68e8aa5e436
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540152"
---
# <a name="ios-platform-features-overview"></a>Visão geral dos recursos de plataforma do iOS

Esta lista de página versões do iOS recentes, bem como realce de algumas das estruturas da Apple, você pode acessar com xamarin. IOS.

## <a name="ios-releases"></a>versões do iOS

|  |  |
|-------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Introdução ao iOS 13 de visualização](~/ios/platform/ios13/index.md) | Este documento descreve a versão prévia do xamarin. IOS 13.|
| [Introdução ao iOS 12](~/ios/platform/introduction-to-ios12/index.md) | Este documento descreve os recursos de 12 de iOS disponíveis para uso ao criar aplicativos xamarin. IOS.|
| [Introdução ao iOS 11](~/ios/platform/introduction-to-ios11/index.md) | Este documento descreve os recursos novos e atualizados no iOS 11 e o Xcode 9, como ARKit, Core ML, Core NFC, arrastar e soltar, MapKit, PDFKit, SiriKit e visão. Ele vincula às guias que descrevem como usar esses recursos com o xamarin. IOS. |
| [Introdução ao iOS 10](~/ios/platform/introduction-to-ios10/index.md) | iOS 10 inclui várias novas APIs e serviços que permitem que você desenvolva aplicativos com novos recursos e funcionalidades. Com o iOS 10, os aplicativos têm novas habilidades como a extensão de mapas, mensagens, telefone e Siri. Esta seção mostra como tirar proveito desses recursos em um aplicativo xamarin. IOS. |
| [Introdução ao iOS 9](~/ios/platform/introduction-to-ios9/index.md)   | Esta seção define as alterações feitas no iOS 9 durante a atualização do iOS 8 e como usar esses recursos em um aplicativo xamarin. IOS. |
| [Introdução ao iOS 8](~/ios/platform/introduction-to-ios8.md)         | iOS 8 fez um grande número de alterações para o sistema operacional do iOS 7. Aqui, vamos mostrar o que são e como usá-los. |
| [Introdução ao iOS 7](~/ios/platform/introduction-to-ios7/index.md)   | Sobre as principais APIs novo introduzidas no iOS 7, incluindo controlador de exibição faz a transição, aprimoramentos UIView animações, UIKit Dynamics e Kit de texto. |
| [Introdução ao iOS 6](~/ios/platform/introduction-to-ios6/index.md)   | Explicações sobre os recursos introduzidos no iOS 6, incluindo exibições de coleção, passar Kit, Kit de evento e a estrutura Social. |

## <a name="apple-payiosplatformapple-paymd"></a>[Apple Pay](~/ios/platform/apple-pay.md)

Apple Pay foi introduzido junto com o iOS 8, permitindo que os usuários paguem produtos físicos, como a comida, entretenimento e as associações por meio de seus dispositivos iOS. Ele está disponível no iPhone 6 e iPhone 6 Plus e também pode ser emparelhado com o Apple Watch para todas as compras na store. Quando usado em um iPhone, ele usa a ID de toque como uma maneira de confirmar e autorizar transações de cartão de débito ou de crédito de um usuário.

## <a name="callkitiosplatformcallkitmd"></a>[CallKit](~/ios/platform/callkit.md)

A nova API de CallKit no iOS 10 fornece uma maneira para aplicativos VOIP integrar com o iPhone da interface do usuário e fornecer uma interface familiar e experiência para o usuário final. Com essa API os usuários podem exibir e interagir com chamadas VOIP de tela de bloqueio do dispositivo iOS e gerenciar contatos usando o aplicativo de telefone **Favoritos** e **recentes** modos de exibição.

## <a name="contacts-and-contactsuiiosplatformcontactsmd"></a>[Contatos e ContactsUI](~/ios/platform/contacts.md)

Com a introdução do iOS 9, a Apple lançou duas novas estruturas `Contacts` e `ContactsUI`, que substituir o existente catálogo de endereços e estruturas do catálogo de endereço da interface do usuário usados pelo iOS 8 e versões anteriores.

## <a name="document-pickeriosplatformdocument-pickermd"></a>[Seletor de documentos](~/ios/platform/document-picker.md)

O seletor de documento permite que os documentos sejam compartilhados entre aplicativos. Esses documentos podem ser armazenados no iCloud ou no diretório do aplicativo um diferente. Documentos são compartilhados por meio do conjunto de [extensões de provedor de documento](~/ios/platform/extensions.md) o usuário instalou em seu dispositivo.

## <a name="eventkitiosplatformeventkitmd"></a>[EventKit](~/ios/platform/eventkit.md)

iOS tem dois aplicativos relacionados ao calendário internos: o aplicativo de calendário e o aplicativo de lembretes. É simples o suficiente entender como o aplicativo de calendário gerencia dados de calendário, mas o aplicativo de lembretes é menos óbvio. Lembretes, na verdade, podem ter datas associadas a eles em termos de quando eles são devido, quando estiver concluídas, etc. Como tal, o iOS armazena todos os dados de calendário, seja em eventos de calendário ou lembretes, em um único local, chamado de *banco de dados de calendário*.

## <a name="ios-extensionsiosplatformextensionsmd"></a>[extensões do iOS](~/ios/platform/extensions.md)

Extensões, conforme apresentada no iOS 8, são especializadas `UIViewControllers` que são apresentados pelo iOS dentro do padrão de contextos, como dentro de **Central de notificações**, como especializada de tipos de teclado personalizado solicitados pelo usuário para executar entrada ou em outros contextos, como edição de uma foto em que a extensão pode fornecer filtros de efeito especial.

## <a name="graphics-and-animation-in-iosiosplatformgraphics-animation-iosindexmd"></a>[Gráficos e animação no iOS](~/ios/platform/graphics-animation-ios/index.md)

Gráficos e animação no iOS aborda os principais conceitos de gráficos no iOS, como CoreImage, Core gráficos e animação principal.

## <a name="handoffiosplatformhandoffmd"></a>[Handoff](~/ios/platform/handoff.md)

Para outro dispositivo executando o mesmo aplicativo ou outro aplicativo compatível com a mesma atividade a Apple introduziu entrega no iOS 8 e nos X Yosemite (10.10) para fornecer um mecanismo comum para o usuário transferir das atividades iniciadas em um dos seus dispositivos.

## <a name="healthkitiosplatformhealthkitmd"></a>[HealthKit](~/ios/platform/healthkit.md)

Kit de integridade fornece um repositório de dados seguro para as informações do usuário relacionados à integridade. Aplicativos de Kit de integridade podem, com a permissão do usuário explícito, leitura e gravação para esse repositório de dados e receber notificações quando os dados pertinentes são adicionados. Os aplicativos podem apresentar os dados ou usuário pode usar o aplicativo de integridade fornecido da Apple para exibir um painel de todos os seus dados.

## <a name="homekitiosplatformhomekitmd"></a>[HomeKit](~/ios/platform/homekit.md)

No iOS 8 para fornecer uma estrutura comum para descoberta e comunicação com dispositivos de automação residencial na página inicial de um usuário, a Apple introduziu HomeKit. HomeKit fornece uma plataforma comum para configurar dispositivos e configurando ações controlá-las.

## <a name="in-app-purchasingiosplatformin-app-purchasingindexmd"></a>[Compras no aplicativo](~/ios/platform/in-app-purchasing/index.md)

aplicativos do iOS podem vender produtos digitais ou serviços que usam StoreKit – um conjunto de APIs fornecidos pelo iOS que se comunicam com servidores da Apple para conduzir transações financeiras com o usuário por meio de sua ID da Apple. As APIs StoreKit estão principalmente preocupados com Recuperando informações de produto e conduzir transações – não há nenhum componente de interface do usuário. Aplicativos que implementam a compras no aplicativo devem criar sua própria interface do usuário e acompanhar os itens comprados com código personalizado para fornecer os serviços ou produtos necessários para o usuário.

## <a name="ios-gaming-apisiosplatformgamingindexmd"></a>[APIs de jogos de iOS](~/ios/platform/gaming/index.md)

Apple fez várias melhorias para as APIs de jogos em iOS 9 tecnológicas que tornam mais fácil de implementar os gráficos de jogo e áudio em um aplicativo xamarin. IOS. Isso inclui tanto a facilidade de desenvolvimento por meio de estruturas de alto nível e aproveitando o poder de GPU do dispositivo iOS para maior velocidade e capacidades de gráfico.

## <a name="message-app-integrationiosplatformmessage-app-integrationindexmd"></a>[Integração de aplicativos de mensagem](~/ios/platform/message-app-integration/index.md)

Novo para o iOS 10, uma mensagem da extensão do aplicativo se integra com o **mensagens** aplicativo e apresenta novas funcionalidades para o usuário. A extensão pode enviar mensagens de texto, adesivos, arquivos de mídia e interativas.

## <a name="multitasking-for-ipadiosplatformmultitaskingmd"></a>[Multitarefa para iPad](~/ios/platform/multitasking.md)

iOS 9 adiciona suporte de multitarefa para dois aplicativos em execução ao mesmo tempo em um hardware específico iPad. Multitarefa para iPad é suportada pelos seguintes recursos: Slide sobre, modo de exibição de divisão e Picture in Picture.

## <a name="passkitiosplatformpasskitmd"></a>[PassKit](~/ios/platform/passkit.md)

Caderneta é um aplicativo para iPhones e iPod toca com iOS 6. Ele armazena e exibe códigos de barras e outras informações para vincular as transações do cliente em seu telefone com "mundo real". Passa é geradas por comerciantes e enviada ao cliente por email, URLs ou de dentro um aplicativo do iOS do comerciante. Caderneta armazena e organiza todos os passos de em um telefone e exibe os lembretes de passagem na tela de bloqueio, dependendo da data/hora ou o local do dispositivo.

Este documento apresenta Passbook, usando a API de Kit passar com xamarin. IOS e discute como implementar passa em seu servidor.

## <a name="photokitiosplatformphotokitmd"></a>[PhotoKit](~/ios/platform/photokit.md)

Kit de fotos é uma nova estrutura que permite aos aplicativos consultar a biblioteca de imagens do sistema e criar interfaces do usuário personalizadas para exibir e modificar seu conteúdo. Ele inclui um número de classes que representam os ativos de vídeo e imagem, bem como coleções de ativos, como pastas e álbuns.

## <a name="request-app-reviewiosplatformrequest-app-reviewmd"></a>[Solicitação de revisão de aplicativo](~/ios/platform/request-app-review.md)

Novo para o iOS 10.3, o `RequestReview()` método permite que um aplicativo iOS solicitar que o usuário avalie ou revisá-lo. Quando este método é chamado em um aplicativo de envio que o usuário instalado da Store do aplicativo, o iOS 10 lidar com a classificação de inteira e processo de revisão para o desenvolvedor. Porque esse processo é controlado por política da App Store, um alerta pode ou não pode ser exibido.

## <a name="search-apisiosplatformsearchindexmd"></a>[APIs de pesquisa](~/ios/platform/search/index.md)

Pesquisa foi expandida no iOS 9 para fornecer novas formas de acessar informações e recursos dentro de um aplicativo xamarin. IOS. Conteúdo do aplicativo usando as novas APIs de pesquisa do aplicativo, é feito pesquisável por meio de destaque e Safari resultados da pesquisa, entrega e lembretes Siri e sugestões. Isso permite que os usuários acessem rapidamente atividades e informações profundas dentro de seu aplicativo.

## <a name="sirikitiosplatformsirikitindexmd"></a>[SiriKit](~/ios/platform/sirikit/index.md)

Novo para o iOS 10, o SiriKit permite que um aplicativo iOS fornecer serviços que são acessíveis para o usuário usando o Siri e o aplicativo de mapas em um dispositivo iOS usando extensões de aplicativo e o novo **intenções** e **IU de Intents** estruturas.

## <a name="social-frameworkiosplatformsocial-frameworkmd"></a>[Estrutura social](~/ios/platform/social-framework.md)

A estrutura Social fornece uma API unificada para interagir com redes sociais incluindo _Twitter_ e _Facebook_, bem como _SinaWeibo_ para os usuários na China.

## <a name="speech-recognitioniosplatformspeechmd"></a>[Reconhecimento de fala](~/ios/platform/speech.md)

iOS 10 inclui uma nova API de fala que permite que o aplicativo para dar suporte a reconhecimento de fala contínua e transcrição de fala (de fluxos de áudio ao vivo ou gravados) em texto.

## <a name="textkitiosplatformtextkitmd"></a>[TextKit](~/ios/platform/textkit.md)

Kit de texto é uma nova API que oferece recursos de layout e renderização de texto avançada. Ele é criado sobre o estrutura do texto principal de nível baixo, mas é muito mais fácil de usar do que o texto principal.

## <a name="3d-touchiosplatform3d-touchmd"></a>[3D Touch](~/ios/platform/3d-touch.md)

Este artigo fornecerá e introdução ao uso as novas APIs de toque 3D para adicionar os gestos de pressão confidenciais aos seus aplicativos xamarin. IOS que estão em execução no novo iPhone 6s e iPhone 6s Plus dispositivos.

## <a name="touch-idiosplatformtouchidmd"></a>[ID de Toque](~/ios/platform/touchid.md)

ID de toque foi introduzido no iOS 7 como um meio de autenticação do usuário – semelhante a uma senha. No entanto, ele era limitado para desbloquear o dispositivo, usando o App Store, usando o iTunes e autenticar o conjunto de chaves do iCloud apenas.

## <a name="user-notificationsiosplatformuser-notificationsindexmd"></a>[Notificações do usuário](~/ios/platform/user-notifications/index.md)

Novo no iOS 10, a notificação do usuário framework permite a entrega e a manipulação de notificações locais e remotas. Usando essa estrutura, o aplicativo ou extensão do aplicativo pode agendar a entrega de notificações locais, especificando um conjunto de condições, como local ou a hora do dia.

## <a name="wide-coloriosplatformwide-colormd"></a>[Cores amplas](~/ios/platform/wide-color.md)

iOS 10 e o macOS Sierra aprimora o suporte para formatos de pixel de intervalo estendido e espaços de cores de toda a gama de cores em todo o sistema, incluindo estruturas como elementos gráficos principais, imagem principal, sistema operacional e AVFoundation. Suporte para dispositivos com telas de cores adicional é facilitado por fornecendo esse comportamento em toda a pilha inteira de gráficos.

## <a name="binding-objective-cbinding-objective-cindexmd"></a>[Associação do Objective-C](binding-objective-c/index.md)

Ao trabalhar no iOS, você poderá encontrar casos em que você deseja consumir uma biblioteca do Objective-C de terceiros. Nessas situações, você pode usar os projetos de associação do MonoTouch para criar uma associação do c# para as bibliotecas de Objective-C nativas. O projeto usa as mesmas ferramentas que usamos para trazer as APIs do iOS para C#. Este documento descreve como vincular as APIs do Objective-C.

## <a name="referencing-native-librariesnative-interopmd"></a>[Referenciando as bibliotecas nativas](native-interop.md)

Xamarin. IOS dá suporte à vinculação com bibliotecas de C nativas e bibliotecas de Objective-C. Este documento aborda como vincular suas bibliotecas C nativas com o seu projeto xamarin. IOS.

## <a name="embedded-frameworksembedded-frameworksmd"></a>[Estruturas incorporadas](embedded-frameworks.md)

Explica como incorporar estruturas de usuário do Objective-C em aplicativos xamarin. IOS.
