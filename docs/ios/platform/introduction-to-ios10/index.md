---
title: Introdução ao iOS 10
description: Este artigo apresenta todas as APIs e recursos disponíveis no iOS 10 novos e modificados para desenvolvedores do xamarin. IOS.
ms.prod: xamarin
ms.assetid: FB91DFFE-CF5E-4253-92CB-78A6371259D9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/29/2017
ms.openlocfilehash: 2d1e0df95b2665f7e3b33a901271b11e1c243b1b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123552"
---
# <a name="introduction-to-ios-10"></a>Introdução ao iOS 10

_Este artigo apresenta todas as APIs e recursos disponíveis no iOS 10 novos e modificados para desenvolvedores do xamarin. IOS._

## <a name="introducing-ios-10"></a>Introdução ao iOS 10

Com o novo iOS SDK 10, Apple incluiu novas APIs e serviços que permitem ao desenvolvedor criar novas categorias de aplicativos e recursos. Um aplicativo iOS agora pode estender os aplicativos de mensagens, Siri, telefone e mapas para fornecer a funcionalidade de avançados e envolvente para o usuário final que não estavam disponível anteriormente.

Para obter mais informações sobre o iOS 10, consulte da Apple [iOS + aplicativos](https://developer.apple.com/ios/) documentação.

## <a name="whats-new-in-ios-10"></a>O que há de novo no iOS 10

Apple adicionou várias novas APIs e serviços no iOS 10 junto com muitos aprimoramentos aos recursos existentes, incluindo:


## <a name="adapting-to-the-true-tone-display"></a>Adaptação de acordo com a exibição de tom True

Tecnologia de exibição True de tom da Apple usa o sensor de luz ambiente em um dispositivo iOS para ajustar dinamicamente a cor e a intensidade da tela para coincidir com as condições de iluminação atuais. iOS 10 fornece as novas [UIWhitePointAdaptivityStyle](https://developer.apple.com/library/prerelease/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW31) chave que pode ser adicionado para o aplicativo `Info.plist` de arquivo e controla como o tom True se aplica a mudança de cor padrão. 

Os valores a seguir estão disponíveis:

- `UIWhitePointAdaptivityStyleStandard` **Padrão** -usar a adaptabilidade ponto branco padrão.
- `UIWhitePointAdaptivityStyleReading` – Usado para aplicativos voltados a leitura.
- `UIWhitePointAdaptivityStyleGame` – Usado para aplicativos focados no jogo.
- `UIWhitePointAdaptivityStyleVideo` – Usado para aplicativos focados no vídeo.
- `UIWhitePointAdaptivityStylePhoto` – Usado para aplicativos voltados a fotografia onde a fidelidade de cor é mais importante do que o ponto branco ambientais ajustes.

<a name="app-extensions" />

## <a name="app-extensions"></a>Extensões de aplicativo

Apple forneceu vários novos pontos de extensão de aplicativo no iOS 10:

- Diretório de chamada
- As intenções e propósitos de interface do usuário
- Mensagens
- Conteúdo da notificação
- Serviços de notificação
- Pacote de adesivo

Além disso, 3º extensões de aplicativo de teclado de terceiros têm os seguintes aprimoramentos:

- O novo `DocumentInputMode` propriedade do `UITextDocumentProxy` classe pode determinar o idioma de entrada de um documento e permitir a extensão de teclado alinhar com esse idioma.
- O novo `HandleInputModeList` método permite que a extensão de teclado exiba o menu de seletor de teclado do sistema em resposta à chave de todo mundo sendo tocado.

Para obter mais informações, consulte nosso [Introdução às extensões](~/ios/platform/extensions.md), [integração de aplicativos de mensagem](~/ios/platform/message-app-integration/index.md), [Introdução ao sugestões proativas](~/ios/platform/search/proactive-suggestions.md), [ Introdução ao SiriKit](~/ios/platform/sirikit/index.md), [Introdução a notificações de usuário](~/ios/platform/user-notifications/index.md) da Apple [guia de programação de extensão do aplicativo](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214).

## <a name="app-search-enhancements"></a>Aperfeiçoamentos feitos na pesquisa do aplicativo

Destaque principal no iOS 10 oferece vários aprimoramentos para o aplicativo de pesquisa, como:

- **Popularidade do Link profundo de crowdsourcing (com a privacidade diferencial)** -fornece uma maneira de promover o conteúdo do aplicativo com vínculo profundo nos resultados da pesquisa.
- **No aplicativo pesquisando** -usar o novo `CSSearchQuery` classe para fornecer capacidade de pesquisa de destaque no aplicativo semelhante a como funcionam os aplicativos de email, mensagens e anotações.
- **Pesquisar continuação** - permite que um usuário iniciar uma pesquisa em destaque ou Safari e, em seguida, abrir um aplicativo e continuar essa pesquisa.
- **Visualização de resultados de validação** – da Apple [ferramenta de validação de API de pesquisa do aplicativo](https://search.developer.apple.com/appsearch-validation-tool) agora exibe uma representação visual de marcação de um site e vinculação profunda ao realizar testes.
- **Aplicativo de compartilhamento de imagem da mensagem** -permite populares imagens no aplicativo fornecidas para o compartilhamento de mensagens (por meio de uma extensão de aplicativo de mensagem) para aparecer em pesquisas de destaque.

Para obter mais informações, consulte nosso [aprimoramentos de aplicativo de pesquisa](~/ios/platform/search/app-search-enhancements.md) guia.

## <a name="apple-pay-enhancements"></a>Aprimoramentos do Apple Pay

Apple fez várias melhorias para Apple Pay no iOS 10 que permitem ao usuário fazer pagamentos seguros de sites e por meio de interação com Siri e mapas.

Com o iOS 10, diversas APIs novas foram adicionadas que funcionam com o iOS e watchOS para dar suporte a redes de pagamento dinâmico e um novo ambiente de teste de área restrita.

Além disso, PassKit framework foi expandido para dar suporte a Apple Pay fora do `UIKit` e permitir que os emissores de cartão apresentar seus cartões de dentro de seus aplicativos.

Para obter mais informações, consulte nosso [aprimoramentos de pagamento de Apple](~/ios/platform/apple-pay.md) guia.

## <a name="alternate-app-icons"></a>Ícones de aplicativos alternativos

Apple adicionou várias melhorias para o iOS 10.3 que permitem que um aplicativo gerenciar seu ícone:

 - `ApplicationIconBadgeNumber` -Obtém ou define o selo do ícone do aplicativo em do Springboard.
 - `SupportsAlternateIcons` -Se `true` o aplicativo tem um conjunto alternativo de ícones.
 - `AlternateIconName` -Retorna o nome do ícone alternativo selecionado no momento ou `null` se usando o ícone principal.
 - `SetAlternameIconName` -Use esse método para alternar o ícone do aplicativo para o ícone alternativo especificado.

Para obter mais informações, consulte nosso [ícones de aplicativos alternativo](~/ios/app-fundamentals/images-icons/alternate-app-icons.md) guia.


## <a name="introduction-to-callkit"></a>Introdução ao CallKit

A nova API de CallKit no iOS 10 fornece uma maneira para aplicativos VOIP integrar com o iPhone da interface do usuário e fornecer uma interface familiar e experiência para o usuário final. Com essa API, os usuários podem exibir e interagir com chamadas VOIP de tela de bloqueio do dispositivo iOS e gerenciar contatos usando o aplicativo de telefone **Favoritos** e **recentes** modos de exibição.

Além disso, a API de CallKit fornece a capacidade de criar extensões de aplicativo que pode associar um número de telefone com um nome (ID do chamador) ou informar ao sistema quando um número deve ser bloqueado (chamada de bloqueio).

Para obter mais informações, consulte nosso [Introdução ao Callkit](~/ios/platform/callkit.md) guia.

## <a name="message-app-integration"></a>Integração de aplicativos de mensagem

iOS 10 permite a inclusão de uma extensão de aplicativo de mensagem na solução xamarin. IOS que se integra com o **mensagens** aplicativo e apresenta novas funcionalidades para o usuário. A extensão pode enviar mensagens de texto, adesivos, arquivos de mídia e interativas. Dois tipos de mensagem da extensão do aplicativo estão disponíveis:

- **Pacotes de adesivo** -contém uma coleção de adesivos que o usuário pode adicionar a uma mensagem. Pacotes de adesivo podem ser criados sem escrever nenhum código.
- **iMessage aplicativo** -pode apresentar uma Interface de usuário personalizada dentro do aplicativo de mensagens para selecionar os adesivos, inserir texto, incluindo arquivos de mídia (com conversões de tipo opcional) e criando, editando e enviando mensagens de interação.

Para obter mais informações, consulte nosso [integração de aplicativos de mensagem](~/ios/platform/message-app-integration/index.md) guia.

## <a name="news-publisher-enhancements"></a>Aprimoramentos do Editor de notícias

Com o iOS 10, Apple será permitir que qualquer pessoa principais revistas e novas organizações bloggers e editores independentes para se inscrever e produto e entregar conteúdo para o aplicativo Apple News. Para obter mais informações, consulte da Apple [notícias recursos](https://newsresources.apple.com/) documentação.

## <a name="providing-haptic-feedback"></a>Fornecer comentários Hápticos

No iPhone 7 e iPhone 7 Além disso, o Apple incluiu novas respostas haptics que fornecem maneiras adicionais para envolver fisicamente o usuário. Use as novas opções de comentários táteis para obter a atenção do usuário e reforçar suas ações.

Vários elementos de interface do usuário internos já fornecem comentários hápticos como selecionadores, comutadores e os controles deslizantes. iOS 10 agora adiciona a capacidade de disparar programaticamente haptics usando uma subclasse concreta do `UIFeedbackGenerator` classe.

Para obter mais informações, consulte nosso [fornecer comentários Hápticos](~/ios/user-interface/ios-ui/haptic-feedback.md) guia.

## <a name="proactive-suggestions"></a>Sugestões proativas

iOS 10 apresenta novas maneiras de condução contrato a um aplicativo, permitindo que o sistema de forma proativa apresentam informações úteis automaticamente para o usuário em momentos apropriados. Assim como o iOS 9 fornecidos a capacidade de adicionar a pesquisa aprofundada ao aplicativo usando o destaque, entrega e sugestões de Siri com iOS 10, que um aplicativo pode expor a funcionalidade que pode ser apresentada ao usuário pelo sistema de dentro dos seguintes locais:

- O seletor de aplicativo
- A tela de bloqueio
- CarPlay
- Mapas
- Interações de Siri
- Sugestões de QuickType 

Um aplicativo expõe essa funcionalidade no sistema usando uma coleção de tecnologias, como [NSUserActivity](https://developer.xamarin.com/api/type/Foundation.NSUserActivity/), marcação de web, destaque principal, MapKit, Media Player e UIKit.

Para obter mais informações, consulte nosso [Introdução ao sugestões proativas](~/ios/platform/search/proactive-suggestions.md) guia.

## <a name="request-app-review"></a>Solicitação de revisão de aplicativo

Novo para o iOS 10.3, o `RequestReview()` método permite que um aplicativo iOS solicitar que o usuário avalie ou revisá-lo. Embora esse método pode ser chamado a qualquer momento em que faz sentido na experiência do usuário, o processo de revisão é regido e manipulado pela política da App Store. Como resultado, esse método pode ou não pode exibir um alerta e nunca deve ser chamado em resposta a uma ação do usuário, como tocar em um botão.

Para obter mais informações, consulte nosso [solicitar revisão de aplicativo](~/ios/platform/request-app-review.md) guia.

## <a name="security-and-privacy-enhancements"></a>Aperfeiçoamentos de segurança e privacidade

Apple fez várias melhorias de segurança e privacidade no iOS 10 que ajudarão o desenvolvedor melhorar a segurança dos seus aplicativos e garantir a privacidade do usuário final.

Como resultado, os aplicativos que executam o iOS 10 (ou posterior) estaticamente devem declarar sua intenção para acessar informações do usuário ou recursos específicos, inserindo uma ou mais chaves específicas de privacidade em seus `Info.plist` arquivos explicam ao usuário por que o aplicativo deseja acessar.

Para obter mais informações, consulte nosso [aperfeiçoamentos de segurança e privacidade](~/ios/app-fundamentals/security-privacy.md) guia.

## <a name="sirikit"></a>SiriKit

Novo para o iOS 10, o SiriKit permite que um aplicativo xamarin. IOS fornecer serviços que são acessíveis para o usuário usando o Siri em um dispositivo iOS. Essa funcionalidade é fornecida em um ou mais a extensão do aplicativo usando o novo **intenções** e **IU de Intents** estruturas.

SiriKit dá suporte aos seguintes domínios do serviço:

- Chamada de vídeo ou de áudio.
- Uma jornada de reserva.
- Gerenciando exercícios físicos.
- Sistema de mensagens.
- Pesquisando fotos.
- Enviar ou receber pagamentos.

Quando o usuário faz uma solicitação do Siri que envolvem um dos serviços de aplicativo da extensão, SiriKit envia a extensão de um **intenção** objeto que descreve a solicitação do usuário, juntamente com quaisquer dados de suporte. A extensão de aplicativo, em seguida, gera apropriado **resposta** do objeto para o determinado **intenção**, detalhando como a extensão pode lidar com a solicitação.

Embora Siri geralmente lida com toda a interação de usuário, a extensão de aplicativo pode usar o **intenção de interface do usuário do** framework para apresentar uma rica e personalizado Interface do usuário que contam com a identidade visual do aplicativo e informações adicionais.

Para obter mais informações, consulte nosso [Introdução ao SiriKit](~/ios/platform/sirikit/index.md) guia.

## <a name="speech-recognition"></a>Reconhecimento de fala

iOS 10 inclui uma nova API de fala que permite que o aplicativo para dar suporte a reconhecimento de fala contínua e transcrição de fala (de fluxos de áudio ao vivo ou gravados) em texto.

Porque a transmissão e o armazenamento temporário de dados em servidores da Apple, o aplicativo requer que o reconhecimento de fala _deve_ solicitar permissão do usuário para realizar o reconhecimento, incluindo o `NSSpeechRecognitionUsageDescription` chave no seu `Info.plist` arquivo e chamar o `SFSpeechRecognizer.RequestAutorization` método.

Para obter mais informações, consulte nosso [Introdução ao reconhecimento de fala](~/ios/platform/speech.md) guia.

## <a name="user-notifications"></a>Notificações do usuário

Novo no iOS 10, a notificação do usuário framework permite a entrega e a manipulação de notificações locais e remotas. Usando essa estrutura, o aplicativo ou extensão do aplicativo pode agendar a entrega de notificações locais, especificando um conjunto de condições, como local ou a hora do dia.

Além disso, o aplicativo ou a extensão pode receber (e potencialmente modificar) notificações locais e remotas conforme elas são entregues ao dispositivo iOS do usuário.

A nova estrutura de interface do usuário de notificação de usuário permite que o aplicativo ou extensão do aplicativo para personalizar a aparência de notificações locais e remotas quando eles são apresentados ao usuário.

Para obter mais informações, consulte nosso [estrutura de notificações de usuário](~/ios/platform/user-notifications/index.md) guia.

## <a name="video-subscriber-account"></a>Conta de assinante de vídeo

Novo para iOS 10, a estrutura de conta de assinante de vídeo permite que os aplicativos que suporte autenticado streaming ou vídeo sob demanda para autenticar com seu provedor de TV cabo ou satélite usando uma experiência de logon único para o usuário final.

## <a name="wide-color"></a>Cores amplas

iOS 10 estende o suporte para formatos de pixel de intervalo estendido e espaços de cores de toda a gama de cores em todo o sistema, incluindo estruturas como elementos gráficos principais, imagem principal, sistema operacional e AVFoundation. Suporte para dispositivos com telas de cores adicional é facilitado por fornecendo esse comportamento em toda a pilha inteira de gráficos.

Além disso, [UIKit](https://developer.xamarin.com/api/namespace/UIKit/) tiver sido modificado funcionar no novo estendido **sRGB** espaço de cor, que facilita a combinação de cores em gamas de cores amplas sem perda de desempenho significativa.

A Apple oferece as seguintes práticas recomendadas ao trabalhar com cores amplas:

- [UIColor](https://developer.xamarin.com/api/type/UIKit.UIColor/) agora usa o sRGB espaço de cores e não poderá mais clamp valores para o `0.0` para `1.0` intervalo. Se o aplicativo utiliza o comportamento de clamp anterior, ele precisará ser modificado para iOS 10.
- O ambiente de desenho será configurado para o espaço de cor sRGB ao executar personalizado `UIView` desenhando em um iPad Pro.
- Se o aplicativo executa o processamento personalizado de `UIImages`, use a nova [UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer) classe para especificar o uso de formatos de intervalo estendido ou o intervalo padrão.
- Ao usar uma API de nível inferior, como elementos gráficos principais ou Bare Metal para fornecer processamento de imagem, o desenvolvedor deve usar uma extensa variedade espaço e pixel formato de cor que dá suporte a valores de ponto flutuante de 16 bits. Quando for necessário, o desenvolvedor precisará clamp manualmente os valores de componente de cor.
- Elementos gráficos principais, imagem principal e sombreadores de desempenho do sistema operacional fornecem novos métodos para converter entre os espaços de duas cores.

Para obter mais informações, consulte nosso [Introdução a cores amplas](~/ios/platform/wide-color.md) guia.

## <a name="widget-enhancements"></a>Aprimoramentos de widget

Apple introduziu vários aprimoramentos para o sistema de Widget para garantir que os widgets combinam bem com qualquer plano de fundo que existe no iOS nova tela de bloqueio 10. O [NotificationCenterVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1613917-notificationcentervibrancyeffect) propriedade foi preterida e foi substituída com o novo [WidgetPrimaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771278-widgetprimaryvibrancyeffect) ou [WidgetSecondaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771277-widgetsecondaryvibrancyeffect) propriedades. Além disso, widgets agora contêm uma [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) propriedade que permite ao desenvolvedor descrever a quantidade de conteúdo está disponível e permite que o usuário expandir e recolher o conteúdo.

Para obter mais informações, consulte nosso [pesquisa e aprimoramentos de Widget de tela início](~/ios/platform/search/widgets.md) guia.

## <a name="additional-framework-changes"></a>Alterações de estrutura adicionais

Além das alterações de estrutura principais e adições listadas acima, Apple fez muitas alterações de estrutura secundárias adicionais no iOS 10.

Para obter mais informações, consulte nosso [alterações adicionais do Framework](~/ios/platform/introduction-to-ios10/additional-framework-changes.md) guia.

## <a name="deprecated-apis"></a>APIs obsoletas

As seguintes APIs foram preteridas no iOS 10:

- O `CKDiscoverAllContactsOperation`, `CKDiscoveredUserInfo`, `CKDiscoverUserInfosOperation` e `CKFetchRecordChangesOperation` classes foram substituídas no CloudKit do iOS 10. Use o [CKDiscoverAllUserIdentitiesOperation](https://developer.xamarin.com/api/type/CloudKit.CKDiscoverUserIdentitiesOperation/), [CKUserIdentity](https://developer.xamarin.com/api/type/CloudKit.CKUserIdentity/) e [CKFetchRecordZoneChangesOperation](https://developer.xamarin.com/api/type/CloudKit.CKFetchRecordZoneChangesOperation/) classes (que dá suporte a compartilhamento de registro) em vez disso.
- Vários [CKSubscription](https://developer.apple.com/reference/cloudkit/cksubscription) APIs (como as assinaturas de baseados em consulta e zona) foram preteridas. Use o [CKRecordZoneSubscription](https://developer.xamarin.com/api/type/CloudKit.CKRecordZoneSubscription/) e [CKQuerySubscription](https://developer.xamarin.com/api/type/CloudKit.CKQuerySubscription/) APIs em vez disso.
- [NSPersistentStoreCoordnator](https://developer.xamarin.com/api/type/CoreData.NSPersistentStoreCoordinator/) símbolos relacionados ao conteúdo onipresente foram preteridos.
- `ADBannerView`, `ADInterstitialAd` e relacionadas a símbolos em de [UIViewController](https://developer.xamarin.com/api/type/UIKit.UIViewController/) classe foram preteridos.
- [SKUniform](https://developer.apple.com/reference/spritekit/skuniform) símbolos relacionados a valores de ponto flutuante foram preteridos.
- O `UILocalNotification`, `UIMutableUserNotificationAction`, `UIMutableUserNotificationCategory`, `UIUserNotificationAction`, `UIUserNotificationCategory` e `UIUserNotificationSettings` classes de UIKit foram preteridas. Use o [notificações do usuário](#User-Notifications) framework em vez disso.
- O `HandleActionForLocalNotification`, `HandleActionForRemoteNotification`, `DidReceiveLocalNotification` e `DidReceiveRemoteNotification` WatchKit métodos foram preteridos. Use o `HandleActionForNotification` e `DidReceiveNotification` métodos em vez disso.
- O `DidReceiveLocalNotification` e `DidReceiveRemoteNotification` métodos das [WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate) foram preteridos. Criar uma instância de [UNUserNotificationCenterDelegate](https://developer.apple.com/reference/usernotifications/unusernotificationcenterdelegate) que implementa os métodos apropriados e atribuí-lo para o `Delegate` propriedade do [UNUserNotificationCenter](https://developer.apple.com/reference/usernotifications/unusernotificationcenter) objeto.
- O **Game Center aplicativo** foi preterido e removido do iOS. Se o aplicativo usa GameKit, ele _deve_ apresentar sua própria interface para exibir os recursos de GameKit como placares de líderes, etc.

Consulte da Apple [iOS 9.3 às diferenças de API do iOS 10.0](https://developer.apple.com/library/prerelease/content/releasenotes/General/iOS10APIDiffs/index.html) documentação para obter uma lista completa de substituições.



## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [O que há de novo no iOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewIniOS/Articles/iOS10.html#//apple_ref/doc/uid/TP40017084-SW1)
