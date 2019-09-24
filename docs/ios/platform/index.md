---
title: Visão geral dos recursos da plataforma iOS
description: Este documento contém links para vários guias que descrevem os recursos introduzidos em várias versões do iOS e outros recursos da plataforma iOS.
ms.prod: xamarin
ms.assetid: 9F6A27E5-8A87-ADE2-D1EF-5684E7B8C999
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/25/2018
ms.openlocfilehash: c6385ff193c54fdab8f252c757cad810751b3f08
ms.sourcegitcommit: 09bc69d7119a04684c9e804c5cb113b8b1bb7dfc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71206295"
---
# <a name="ios-platform-features-overview"></a>Visão geral dos recursos da plataforma iOS

Esta página lista as versões recentes do iOS, além de destacar algumas das estruturas da Apple que você pode acessar com o Xamarin. iOS.

## <a name="ios-releases"></a>versões do iOS

|  |  |
|-------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Introdução ao iOS 13](~/ios/platform/ios13/index.md) | Este documento descreve o Xamarin. iOS 13.|
| [Introdução ao iOS 12](~/ios/platform/introduction-to-ios12/index.md) | Este documento descreve os recursos do iOS 12 disponíveis para uso durante a criação de aplicativos Xamarin. iOS.|
| [Introdução ao iOS 11](~/ios/platform/introduction-to-ios11/index.md) | Este documento descreve os recursos novos e atualizados no iOS 11 e no Xcode 9, como ARKit, Core ML, Core NFC, drag e drop, MapKit, PDFKit, SiriKit e Vision. Ele é vinculado a guias que descrevem como usar esses recursos com o Xamarin. iOS. |
| [Introdução ao iOS 10](~/ios/platform/introduction-to-ios10/index.md) | o iOS 10 inclui várias novas APIs e serviços que permitem desenvolver aplicativos com novos recursos e funcionalidades. Com o iOS 10, os aplicativos têm novas capacidades, como estender mapas, mensagens, telefone e Siri. Esta seção mostra o comos para aproveitar esses recursos em um aplicativo Xamarin. iOS. |
| [Introdução ao iOS 9](~/ios/platform/introduction-to-ios9/index.md)   | Esta seção define as alterações feitas no iOS 9 ao atualizar do iOS 8 e como usar esses recursos em um aplicativo Xamarin. iOS. |
| [Introdução ao iOS 8](~/ios/platform/introduction-to-ios8.md)         | o iOS 8 fez um grande número de alterações no sistema operacional do iOS 7. Aqui, mostramos o que eles são e como usá-los. |
| [Introdução ao iOS 7](~/ios/platform/introduction-to-ios7/index.md)   | Sobre as principais novas APIs introduzidas no iOS 7, incluindo as transições do controlador de exibição, aprimoramentos para animações UIView, UIKit Dynamics e kit de texto. |
| [Introdução ao iOS 6](~/ios/platform/introduction-to-ios6/index.md)   | Explicações dos recursos introduzidos no iOS 6, incluindo exibições de coleção, kit de passagem, kit de eventos e a estrutura social. |

## <a name="apple-payiosplatformapple-paymd"></a>[Apple Pay](~/ios/platform/apple-pay.md)

Apple Pay foi introduzido junto com o iOS 8, permitindo que os usuários paguem por bens físicos, como comida, entretenimento e associações por meio de seus dispositivos iOS. Ele está disponível no iPhone 6 e no iPhone 6 Plus e também pode ser emparelhado com o Apple Watch para compras na loja. Quando usado em um iPhone, ele usa o Touch ID como uma maneira de confirmar e autorizar transações no cartão de crédito ou débito de um usuário.

## <a name="callkitiosplatformcallkitmd"></a>[CallKit](~/ios/platform/callkit.md)

A nova API do CallKit no iOS 10 fornece uma maneira para que os aplicativos de VOIP se integrem à interface do usuário do iPhone e forneçam uma interface familiar e uma experiência para os usuários finais. Com essa API, os usuários podem exibir e interagir com chamadas de VOIP da tela de bloqueio do dispositivo iOS e gerenciar contatos usando as exibições de **favoritos** e **recentes** do aplicativo de telefone.

## <a name="contacts-and-contactsuiiosplatformcontactsmd"></a>[Contatos e ContactsUI](~/ios/platform/contacts.md)

Com a introdução do IOS 9, a Apple lançou duas novas estruturas `Contacts` e `ContactsUI`, que substituem o catálogo de endereços existente e as estruturas de interface do usuário do catálogo de endereços usadas pelo Ios 8 e versões anteriores.

## <a name="document-pickeriosplatformdocument-pickermd"></a>[Seletor de documentos](~/ios/platform/document-picker.md)

O seletor de documento permite que os documentos sejam compartilhados entre aplicativos. Esses documentos podem ser armazenados no iCloud ou em um diretório de aplicativo diferente. Os documentos são compartilhados por meio do conjunto de [extensões de provedor de documento](~/ios/platform/extensions.md) que o usuário instalou em seu dispositivo.

## <a name="eventkitiosplatformeventkitmd"></a>[EventKit](~/ios/platform/eventkit.md)

o iOS tem dois aplicativos relacionados a calendário internos: o aplicativo de calendário e o aplicativo de lembretes. É bastante simples entender como o aplicativo de calendário gerencia dados de calendário, mas o aplicativo lembretes é menos óbvio. Os lembretes podem realmente ter datas associadas a eles em termos de quando eles estão vencidos, quando eles são concluídos etc. Dessa forma, o iOS armazena todos os dados de calendário, sejam eles eventos de calendário ou lembretes, em um local, chamado de banco de dados de *calendário*.

## <a name="ios-extensionsiosplatformextensionsmd"></a>[extensões do iOS](~/ios/platform/extensions.md)

As extensões, como introduzidas no Ios 8, `UIViewControllers` são especializadas que são apresentadas por Ios dentro de contextos padrão, como no **centro de notificações**, como tipos de teclado personalizados solicitados pelo usuário para executar entradas especializadas ou outros contextos como editar uma foto onde a extensão pode fornecer filtros de efeito especiais.

## <a name="graphics-and-animation-in-iosiosplatformgraphics-animation-iosindexmd"></a>[Gráficos e animação no iOS](~/ios/platform/graphics-animation-ios/index.md)

Os gráficos e a animação no iOS abrangem os principais conceitos gráficos no iOS, como CoreImage, gráficos principais e animações de núcleo.

## <a name="handoffiosplatformhandoffmd"></a>[Handoff](~/ios/platform/handoff.md)

A Apple introduziu a entrega no iOS 8 e no OS X Yosemite (10,10) para fornecer um mecanismo comum para o usuário transferir atividades iniciadas em um de seus dispositivos para outro dispositivo que executa o mesmo aplicativo ou outro aplicativo que ofereça suporte à mesma atividade.

## <a name="healthkitiosplatformhealthkitmd"></a>[HealthKit](~/ios/platform/healthkit.md)

O kit de integridade fornece um repositório de dados seguro para as informações relacionadas à integridade do usuário. Os aplicativos do kit de integridade podem, com a permissão explícita do usuário, a leitura e a gravação nesse repositório de dados e recebem notificações quando é adicionado um dado pertinente. Os aplicativos podem apresentar os dados, ou o usuário pode usar o aplicativo de integridade fornecido pela Apple para exibir um painel de todos os seus dados.

## <a name="homekitiosplatformhomekitmd"></a>[HomeKit](~/ios/platform/homekit.md)

A Apple introduziu o HomeKit no iOS 8 para fornecer uma estrutura comum para descobrir e se comunicar com dispositivos de automação doméstica na página inicial de um usuário. O HomeKit fornece uma plataforma comum para configurar dispositivos e configurar ações para controlá-los.

## <a name="in-app-purchasingiosplatformin-app-purchasingindexmd"></a>[Compras no aplicativo](~/ios/platform/in-app-purchasing/index.md)

os aplicativos iOS podem vender produtos ou serviços digitais usando o StoreKit – um conjunto de APIs fornecidas pelo iOS que se comunicam com os servidores da Apple para conduzir transações financeiras com o usuário por meio de sua ID Apple. As APIs StoreKit se preocupam principalmente com a recuperação de informações do produto e a condução de transações – não há nenhum componente de interface do usuário. Os aplicativos que implementam a compra no aplicativo devem criar sua própria interface do usuário e acompanhar os itens comprados com código personalizado para fornecer os produtos ou serviços necessários ao usuário.

## <a name="ios-gaming-apisiosplatformgamingindexmd"></a>[APIs de jogos do iOS](~/ios/platform/gaming/index.md)

A Apple fez várias melhorias tecnológicas nas APIs de jogos no iOS 9 que facilitam a implementação de gráficos e áudio de jogos em um aplicativo Xamarin. iOS. Isso inclui a facilidade de desenvolvimento por meio de estruturas de alto nível e o aproveitamento do poder da GPU do dispositivo iOS para melhorar a velocidade e os recursos gráficos.

## <a name="message-app-integrationiosplatformmessage-app-integrationindexmd"></a>[Integração de aplicativos de mensagens](~/ios/platform/message-app-integration/index.md)

Novo no iOS 10, uma extensão de aplicativo de mensagem se integra ao aplicativo de **mensagens** e apresenta uma nova funcionalidade ao usuário. A extensão pode enviar texto, adesivos, arquivos de mídia e mensagens interativas.

## <a name="multitasking-for-ipadiosplatformmultitaskingmd"></a>[Multitarefa para iPad](~/ios/platform/multitasking.md)

o iOS 9 adiciona suporte multitarefa para executar dois aplicativos ao mesmo tempo em um hardware de iPad específico. Há suporte para multitarefas para iPad por meio dos seguintes recursos: Deslizar, dividir modo de exibição & imagem no Picture.

## <a name="passkitiosplatformpasskitmd"></a>[PassKit](~/ios/platform/passkit.md)

Passbook é um aplicativo para iPhones e toques do iPod com o iOS 6. Ele armazena e exibe códigos de barras e outras informações para vincular transações de clientes em seu telefone com o ' mundo real '. As passagens são geradas por comerciantes e enviadas ao cliente por email, URLs ou de dentro do próprio aplicativo iOS do comerciante. O Passbook armazena e organiza todos os passos em um telefone e exibe lembretes de aprovação na tela de bloqueio dependendo da data/hora ou do local do dispositivo.

Este documento apresenta o Passbook, usando a API do kit Pass com Xamarin. iOS e discute como implementar passagens no servidor.

## <a name="photokitiosplatformphotokitmd"></a>[PhotoKit](~/ios/platform/photokit.md)

O Photo kit é uma nova estrutura que permite que os aplicativos consultem a biblioteca de imagens do sistema e criem interfaces de usuário personalizadas para exibir e modificar seu conteúdo. Ele inclui várias classes que representam ativos de imagem e vídeo, bem como coleções de ativos, como álbuns e pastas.

## <a name="request-app-reviewiosplatformrequest-app-reviewmd"></a>[Solicitar revisão do aplicativo](~/ios/platform/request-app-review.md)

Novo no Ios 10,3, o `RequestReview()` método permite que um aplicativo IOS peça ao usuário para classificá-lo ou examiná-lo. Quando esse método é chamado em um aplicativo de envio que o usuário instalou da loja de aplicativos, o iOS 10 manipulará todo o processo de classificação e revisão para o desenvolvedor. Como esse processo é regido pela política da loja de aplicativos, um alerta pode ou não ser exibido.

## <a name="search-apisiosplatformsearchindexmd"></a>[APIs de pesquisa](~/ios/platform/search/index.md)

A pesquisa foi expandida no iOS 9 para fornecer excelentes maneiras novas de acessar informações e recursos em um aplicativo Xamarin. iOS. Usando as novas APIs de pesquisa de aplicativo, o conteúdo do aplicativo é tornado pesquisável por meio de resultados da pesquisa do Spotlight e do Safari, lembretes e sugestões de entrega e Siri. Isso permite que os usuários acessem rapidamente as atividades e as informações em seu aplicativo.

## <a name="sirikitiosplatformsirikitindexmd"></a>[SiriKit](~/ios/platform/sirikit/index.md)

Novo no iOS 10, o SiriKit permite que um aplicativo iOS forneça serviços que podem ser acessados pelo usuário usando o Siri e o aplicativo Maps em um dispositivo iOS usando extensões de aplicativo e **as novas estruturas** de **interface do usuário** de tentativas e intenções.

## <a name="social-frameworkiosplatformsocial-frameworkmd"></a>[Estrutura social](~/ios/platform/social-framework.md)

A estrutura social fornece uma API unificada para interagir com redes sociais, incluindo o _Twitter_ e o _Facebook_, bem como _SinaWeibo_ para usuários na China.

## <a name="speech-recognitioniosplatformspeechmd"></a>[Reconhecimento de fala](~/ios/platform/speech.md)

o iOS 10 inclui um novo Speech API que permite ao aplicativo dar suporte ao reconhecimento de fala contínuo e transcrever a fala (a partir de fluxos de áudio dinâmicos ou gravados) em texto.

## <a name="textkitiosplatformtextkitmd"></a>[TextKit](~/ios/platform/textkit.md)

O kit de texto é uma nova API que oferece recursos avançados de layout de texto e renderização. Ele se baseia na estrutura de texto principal de nível baixo, mas é muito mais fácil de usar do que o texto principal.

## <a name="3d-touchiosplatform3d-touchmd"></a>[3D Touch](~/ios/platform/3d-touch.md)

Este artigo fornecerá e apresentará uma introdução ao uso das novas APIs de toque 3D para adicionar gestos sensíveis à pressão aos seus aplicativos do Xamarin. iOS que estão em execução nos novos dispositivos iPhone 6s e iPhone 6s Plus.

## <a name="touch-idiosplatformtouchidmd"></a>[ID de Toque](~/ios/platform/touchid.md)

A ID de toque foi introduzida no iOS 7 como um meio de autenticar o usuário-semelhante a uma senha. No entanto, ele estava limitado a desbloquear o dispositivo, usando a loja de aplicativos, usando o iTunes e Autenticando apenas o conjunto de chaves do iCloud.

## <a name="user-notificationsiosplatformuser-notificationsindexmd"></a>[Notificações do usuário](~/ios/platform/user-notifications/index.md)

Novo no iOS 10, a estrutura de notificação de usuário permite a entrega e a manipulação de notificações locais e remotas. Usando essa estrutura, a extensão do aplicativo ou do aplicativo pode agendar a entrega de notificações locais, especificando um conjunto de condições, como local ou hora do dia.

## <a name="wide-coloriosplatformwide-colormd"></a>[Cores amplas](~/ios/platform/wide-color.md)

o iOS 10 e o macOS Sierra aprimoram o suporte para formatos de pixel de intervalo estendido e espaços de cores de ampla gama em todo o sistema, incluindo estruturas como gráficos principais, imagem principal, metal e AVFoundation. O suporte para dispositivos com monitores largos de cores é mais facilitou, fornecendo esse comportamento em toda a pilha gráfica.

## <a name="binding-objective-cbinding-objective-cindexmd"></a>[Associação do Objective-C](binding-objective-c/index.md)

Ao trabalhar no iOS, você pode encontrar casos em que deseja consumir uma biblioteca de Objective-C de terceiros. Nessas situações, você pode usar os projetos de associação do MonoTouch para C# criar uma ligação com as bibliotecas Objective-C nativas. O projeto usa as mesmas ferramentas que usamos para colocar as APIs do iOS C#. Este documento descreve como associar APIs de Objective-C.

## <a name="referencing-native-librariesnative-interopmd"></a>[Referenciando bibliotecas nativas](native-interop.md)

O Xamarin. iOS dá suporte à vinculação com bibliotecas C nativas e bibliotecas objec-C. Este documento discute como vincular suas bibliotecas nativas do C ao seu projeto do Xamarin. iOS.

## <a name="embedded-frameworksembedded-frameworksmd"></a>[Estruturas incorporadas](embedded-frameworks.md)

Explica como inserir estruturas de usuário Objective-C em aplicativos Xamarin. iOS.
