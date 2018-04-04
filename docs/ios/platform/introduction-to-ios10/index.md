---
title: Introdução ao iOS 10
description: Este artigo apresenta todas as APIs e recursos disponíveis no iOS 10 novos e modificados para desenvolvedores do xamarin.
ms.prod: xamarin
ms.assetid: FB91DFFE-CF5E-4253-92CB-78A6371259D9
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/29/2017
ms.openlocfilehash: c3bee0f15016394005a67e98cd8435e6d63b3ac6
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-ios-10"></a>Introdução ao iOS 10

_Este artigo apresenta todas as APIs e recursos disponíveis no iOS 10 novos e modificados para desenvolvedores do xamarin._

## <a name="introducing-ios-10"></a>Introdução ao iOS 10

Com o novo iOS 10 SDK, Apple inclui novas APIs e serviços que permitem que o desenvolvedor criar novas categorias de aplicativos e recursos. Um aplicativo iOS agora pode estender os aplicativos de mensagens, Siri, telefone e mapas para fornecer funcionalidades avançadas atraente para o usuário final que não estavam disponível anteriormente.

Para obter mais informações sobre iOS 10, consulte da Apple [iOS + aplicativos](https://developer.apple.com/ios/) documentação.

## <a name="whats-new-in-ios-10"></a>O que há de novo no iOS 10

Apple adicionou várias novas APIs e serviços no iOS 10 junto com muitas melhorias em recursos existentes, incluindo:


## <a name="adapting-to-the-true-tone-display"></a>Adaptando a exibição tom True

Tecnologia de exibição True de tom da Apple usa o sensor de luz ambiente em um dispositivo iOS para ajustar dinamicamente a cor e a intensidade da exibição para coincidir com as condições de iluminação atuais. iOS 10 fornece as novas [UIWhitePointAdaptivityStyle](https://developer.apple.com/library/prerelease/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW31) chave que pode ser adicionado para o aplicativo `Info.plist` de arquivo e controla como True tom aplica-se a mudança de cor padrão. 

Os valores a seguir estão disponíveis:

- `UIWhitePointAdaptivityStyleStandard` **Padrão** -usar o adaptivity ponto branco padrão.
- `UIWhitePointAdaptivityStyleReading` – Usado para aplicativos voltados para leitura.
- `UIWhitePointAdaptivityStyleGame` – Usado para aplicativos voltados para o jogo.
- `UIWhitePointAdaptivityStyleVideo` – Usado para aplicativos centrados em vídeo.
- `UIWhitePointAdaptivityStylePhoto` – Usado para aplicativos centrados em fotografia onde fidelidade de cor é mais importante do que o ponto de branco ambiental ajustes.

<a name="app-extensions" />

## <a name="app-extensions"></a>Extensões de aplicativo

Apple forneceu vários novos pontos de extensão de aplicativo no iOS 10:

- Diretório de chamada
- Tentativas de interface do usuário e de tentativas
- Mensagens
- Conteúdo da notificação
- Serviços de notificação
- Pacote de etiqueta

Além disso, 3º extensões de aplicativo do teclado de ter os seguintes aprimoramentos:

- O novo `DocumentInputMode` propriedade o `UITextDocumentProxy` classe pode determinar o idioma de entrada de um documento e permitir a extensão de teclado alinhar com o idioma.
- O novo `HandleInputModeList` método permite que a extensão de teclado exibir o menu de seletor de teclado do sistema em resposta à chave do globo sendo tocado.

Para obter mais informações, consulte nosso [Introdução a extensões](~/ios/platform/extensions.md), [integração do aplicativo de mensagens](~/ios/platform/message-app-integration/index.md), [Introdução sugestões pró-ativo](~/ios/platform/search/proactive-suggestions.md), [ Introdução ao SiriKit](~/ios/platform/sirikit/index.md), [Introdução a notificações de usuário](~/ios/platform/user-notifications/index.md) da Apple [guia de programação de extensão do aplicativo](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214).

## <a name="app-search-enhancements"></a>Aprimoramentos da pesquisa do aplicativo

Destaque de núcleo no iOS 10 oferece vários aprimoramentos à pesquisa do aplicativo, como:

- **Crowdsourced Link profundo popularidade (com a privacidade diferencial)** -fornece uma maneira de promover o conteúdo do aplicativo com vínculo profundo nos resultados da pesquisa.
- **Pesquisar no aplicativo** -usar o novo `CSSearchQuery` classe para fornecer capacidade de pesquisa de destaque no aplicativo semelhante a como funcionam os aplicativos de email, mensagens e anotações.
- **Pesquisar continuação** - permite que um usuário iniciar uma pesquisa em destaque ou Safari e, em seguida, abrir um aplicativo e continuar a pesquisa.
- **Visualização de resultados de validação** -da Apple [ferramenta de validação de API de pesquisa do aplicativo](https://search.developer.apple.com/appsearch-validation-tool) agora exibe uma representação visual de marcação de um site e vinculando profundo quando preforming testes.
- **Imagem de aplicativo de compartilhamento de mensagem** -permite populares imagens no aplicativo fornecidas para o compartilhamento de mensagens (por meio de uma extensão de aplicativo de mensagem) apareçam na pesquisa do Spotlight.

Para obter mais informações, consulte nosso [aprimoramentos de pesquisa do aplicativo](~/ios/platform/search/app-search-enhancements.md) guia.

## <a name="apple-pay-enhancements"></a>Aprimoramentos de pagamento da Apple

Apple fez várias melhorias para pagamento da Apple iOS 10 que permitem que o usuário faça pagamentos seguros de sites e por meio da interação com Siri e mapas.

Com o iOS 10, várias novas APIs foram adicionadas que funcionam com iOS e watchOS para dar suporte a redes de pagamento dinâmico e um novo ambiente de teste de área restrita.

Além disso, a estrutura de PassKit foi expandida para dar suporte a pagamento da Apple fora do `UIKit` e permitir que os emissores de cartão apresentar seus cartões de dentro de seus aplicativos.

Para obter mais informações, consulte nosso [Apple pagar aprimoramentos](~/ios/platform/apple-pay.md) guia.

## <a name="alternate-app-icons"></a>Ícones de aplicativo alternativo

Apple tenha adicionado vários aprimoramentos para o iOS 10.3 que permitem que um aplicativo gerenciar seu ícone:

 - `ApplicationIconBadgeNumber` -Obtém ou define a notificação do ícone de aplicativo no Springboard.
 - `SupportsAlternateIcons` -Se `true` o aplicativo tem um conjunto alternativo de ícones.
 - `AlternateIconName` -Retorna o nome do ícone alternativo selecionado no momento ou `null` se usando o ícone do primário.
 - `SetAlternameIconName` -Use esse método para alternar o ícone do aplicativo para o ícone alternativo fornecido.

Para obter mais informações, consulte nosso [alternativo ícones de aplicativo](~/ios/app-fundamentals/images-icons/alternate-app-icons.md) guia.


## <a name="introduction-to-callkit"></a>Introdução ao CallKit

A nova API CallKit no iOS 10 fornece uma maneira para aplicativos VOIP integrar o iPhone da interface do usuário e fornecer uma interface familiar e experiência do usuário final. Com essa API, os usuários podem exibir e interagir com chamadas VOIP de tela de bloqueio do dispositivo iOS e gerenciar contatos usando o aplicativo de telefone **Favoritos** e **recentes** modos de exibição.

Além disso, a API de CallKit fornece a capacidade de criar extensões de aplicativo que pode associar um número de telefone com um nome (ID de chamadas) ou diga o sistema quando um número deve ser bloqueado (chamada de bloqueio).

Para obter mais informações, consulte nosso [Introdução ao Callkit](~/ios/platform/callkit.md) guia.

## <a name="message-app-integration"></a>Integração do aplicativo de mensagens

iOS 10 permite a inclusão de uma extensão de aplicativo de mensagem na solução xamarin que integra o **mensagens** aplicativo e apresenta novas funcionalidades para o usuário. A extensão pode enviar mensagens de texto, adesivos, arquivos de mídia e interativas. Dois tipos de mensagem da extensão do aplicativo estão disponíveis:

- **Pacotes de etiqueta** -contém uma coleção de selos que o usuário pode adicionar a uma mensagem. Pacotes de etiqueta podem ser criados sem escrever nenhum código.
- **iMessage aplicativo** -pode apresentar uma Interface de usuário personalizada dentro do aplicativo de mensagens para selecionar adesivo, inserindo texto, incluindo arquivos de mídia (com conversões de tipo opcional) e criação, edição e enviar mensagens de interação.

Para obter mais informações, consulte nosso [integração do aplicativo de mensagens](~/ios/platform/message-app-integration/index.md) guia.

## <a name="news-publisher-enhancements"></a>Aprimoramentos do Editor de notícias

Com iOS 10, Apple irá permitir que qualquer pessoa revistas principais e de novas organizações bloggers e editores independentes para se inscrever e produto e fornecer conteúdo para o aplicativo de notícias da Apple. Para obter mais informações, consulte da Apple [notícias recursos](https://newsresources.apple.com/) documentação.

## <a name="providing-haptic-feedback"></a>Fornecendo comentários Haptic

No iPhone e iPhone 7 7 +, Apple inclui novas respostas haptics que fornecem maneiras adicionais de fisicamente envolver o usuário. Use as novas opções de comentários táteis para chamar a atenção do usuário e reforçar suas ações.

Vários elementos de interface do usuário internos já fornecem comentários haptic como selecionadores, comutadores e os controles deslizantes. iOS 10 agora inclui a capacidade de disparar programaticamente haptics usando uma subclasse concreta do `UIFeedbackGenerator` classe.

Para obter mais informações, consulte nosso [fornecendo comentários Haptic](~/ios/user-interface/ios-ui/haptic-feedback.md) guia.

## <a name="proactive-suggestions"></a>Sugestões proativos

iOS 10 apresenta novas maneiras de um contrato a um aplicativo, permitindo que o sistema de forma proativa apresentar informações úteis automaticamente para o usuário em momentos apropriados. Assim como o iOS 9 fornecido a capacidade de adicionar a busca detalhada para o aplicativo com destaque, entrega e sugestões de Siri iOS 10, que um aplicativo pode expor a funcionalidade que pode ser apresentada ao usuário pelo sistema de dentro dos seguintes locais:

- O seletor de exibição do aplicativo
- A tela de bloqueio
- CarPlay
- Mapas
- Interações de Siri
- Sugestões de QuickType 

Um aplicativo expõe essa funcionalidade no sistema usando uma coleção de tecnologias como [NSUserActivity](https://developer.xamarin.com/api/type/Foundation.NSUserActivity/), marcação de web, destaque de núcleo, MapKit, reprodutor de mídia e UIKit.

Para obter mais informações, consulte nosso [Introdução sugestões pró-ativo](~/ios/platform/search/proactive-suggestions.md) guia.

## <a name="request-app-review"></a>Solicitação de revisão de aplicativo

Novo para o iOS 10.3, o `RequestReview()` método permite que um aplicativo iOS pedir ao usuário para classificar ou examiná-la. Embora esse método pode ser chamado a qualquer momento em que faz sentido na experiência do usuário, o processo de revisão é controlado e tratado pela política da loja de aplicativos. Como resultado, esse método pode ou não pode exibir um alerta e nunca deve ser chamado em resposta a uma ação do usuário, como ao tocar em um botão.

Para obter mais informações, consulte nosso [solicitação de revisão de aplicativo](~/ios/platform/request-app-review.md) guia.

## <a name="security-and-privacy-enhancements"></a>Segurança e privacidade aprimoramentos

Apple fez diversos aprimoramentos de segurança e privacidade no iOS 10 que podem ajudar o desenvolvedor de melhorar a segurança dos seus aplicativos e garantir a privacidade do usuário final.

Como resultado, os aplicativos em execução no iOS 10 (ou posterior) estaticamente devem declarar sua intenção para acessar informações do usuário ou recursos específicos, inserindo uma ou mais chaves específica de privacidade em seus `Info.plist` arquivos explicam para o usuário por que o aplicativo deseja acessar.

Para obter mais informações, consulte nosso [segurança e privacidade aprimoramentos](~/ios/app-fundamentals/security-privacy.md) guia.

## <a name="sirikit"></a>SiriKit

Novo para iOS 10, SiriKit permite que um aplicativo xamarin fornecer os serviços que são acessíveis para o usuário usando Siri em um dispositivo iOS. Essa funcionalidade é fornecida em um ou mais da extensão do aplicativo usando o novo **tentativas** e **propósitos de interface do usuário** estruturas.

SiriKit oferece suporte a domínios de serviço a seguir:

- Áudio ou vídeo chamada.
- Reserva uma jornada.
- Gerenciando exercícios.
- Sistema de mensagens.
- Pesquisando fotos.
- Enviar ou receber pagamentos.

Quando o usuário faz uma solicitação de Siri que envolvem um dos serviços de extensão do aplicativo, SiriKit envia a extensão de um **intenção** objeto que descreve a solicitação do usuário junto com os dados de suporte. A extensão do aplicativo, em seguida, gera apropriada **resposta** de objeto para o determinado **intenção**, detalhando como a extensão pode lidar com a solicitação.

Siri normalmente manipula todas as interações do usuário, a extensão do aplicativo pode usar o **intenção de interface do usuário** framework para apresentar um personalizado, Avançado, Interface do usuário apresentando o aplicativo da identidade visual e informações adicionais.

Para obter mais informações, consulte nosso [Introdução ao SiriKit](~/ios/platform/sirikit/index.md) guia.

## <a name="speech-recognition"></a>Reconhecimento de fala

iOS 10 inclui uma nova API de fala que permite que o aplicativo dá suporte ao reconhecimento de fala contínua e transcrever da fala (fluxos de áudio ao vivo ou gravados) em texto.

Como o reconhecimento de fala requer a transmissão e o armazenamento temporário de dados em servidores da Apple, o aplicativo _deve_ solicitar a permissão do usuário para executar o reconhecimento, incluindo o `NSSpeechRecognitionUsageDescription` chave em seu `Info.plist` arquivo e chamar o `SFSpeechRecognizer.RequestAutorization` método.

Para obter mais informações, consulte nosso [Introdução ao reconhecimento de fala](~/ios/platform/speech.md) guia.

## <a name="user-notifications"></a>Notificações de usuário

Novo para o iOS 10, a notificação do usuário framework permite a entrega e a manipulação de notificações de locais e remotas. Usando essa estrutura, o aplicativo ou extensão do aplicativo pode agendar a entrega de notificações locais, especificando um conjunto de condições, como local ou a hora do dia.

Além disso, o aplicativo ou extensão pode receber (e potencialmente modificar) locais e remotas notificações como elas são entregues para o dispositivo iOS do usuário.

A nova estrutura de interface do usuário de notificação de usuário permite que o aplicativo ou extensão do aplicativo para personalizar a aparência de notificações de locais e remotas quando eles são apresentados ao usuário.

Para obter mais informações, consulte nosso [Framework de notificações de usuário](~/ios/platform/user-notifications/index.md) guia.

## <a name="video-subscriber-account"></a>Conta do assinante de vídeo

Novo para iOS 10, a estrutura de conta do assinante de vídeo permite que aplicativos que suporte autenticado streaming ou vídeo sob demanda para autenticar com seu provedor de cabo ou satélite de TV, usando uma experiência de Single-Sign-in para o usuário final.

## <a name="wide-color"></a>Cores de largura

iOS 10 estende o suporte para formatos de pixel do intervalo estendido e espaços de toda a gama de cores em todo o sistema, incluindo estruturas, como gráficos de núcleo, imagem principal, sistema operacional e AVFoundation. Suporte para dispositivos com telas de cores de largura é facilitado mais fornecendo esse comportamento em toda a pilha do gráfico inteiro.

Além disso, [UIKit](https://developer.xamarin.com/api/namespace/UIKit/) foi modificado para funcionar no novo estendida **sRGB** espaço de cor, tornando mais fácil misturar cores gamas de cor ampla sem perda de desempenho significativa.

Apple oferece as seguintes práticas recomendadas ao trabalhar com cores amplas:

- [UIColor](https://developer.xamarin.com/api/type/UIKit.UIColor/) agora usa o sRGB espaço de cores e não poderá mais clamp valores para o `0.0` para `1.0` intervalo. Se o aplicativo utiliza o comportamento de fixação anterior, ele precisará ser modificada para iOS 10.
- O ambiente de desenho será configurado para o espaço de cor sRGB ao executar personalizado `UIView` desenhando em um iPad Pro.
- Se o aplicativo executa processamento personalizado de `UIImages`, use o novo [UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer) classe para especificar o uso de formatos de intervalo estendido ou intervalo padrão.
- Ao usar uma API de nível inferior, como gráficos de núcleo ou sistema operacional para fornecer processamento de imagem, o desenvolvedor deve usar um intervalo estendido cor espaço e pixel formato que oferece suporte a valores de ponto flutuante de 16 bits. Quando necessário, o desenvolvedor precisa clamp manualmente os valores de componente de cor.
- Gráficos de núcleo, imagem principal e sombreadores de desempenho do sistema operacional fornecem novos métodos para converter entre os espaços de duas cores.

Para obter mais informações, consulte nosso [Introdução à cor ampla](~/ios/platform/wide-color.md) guia.

## <a name="widget-enhancements"></a>Aprimoramentos de widget

Apple introduziu várias melhorias para o sistema de Widget para garantir que os widgets uma aparência excelentes em qualquer plano de fundo que existe no iOS a nova tela de bloqueio 10. O [NotificationCenterVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1613917-notificationcentervibrancyeffect) propriedade foi preterida e foi substituída com o novo [WidgetPrimaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771278-widgetprimaryvibrancyeffect) ou [WidgetSecondaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771277-widgetsecondaryvibrancyeffect) propriedades. Além disso, widgets agora contém um [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) propriedade que permite ao desenvolvedor descrever a quantidade de conteúdo está disponível e permite que o usuário expandir e recolher o conteúdo.

Para obter mais informações, consulte nosso [pesquisa e aprimoramentos de Widget de tela início](~/ios/platform/search/widgets.md) guia.

## <a name="additional-framework-changes"></a>Alterações de estrutura adicionais

Além das alterações de estrutura principais e adições listadas acima, a Apple fez muitas alterações de estrutura secundárias adicionais no iOS 10.

Para obter mais informações, consulte nosso [alterações adicionais do Framework](~/ios/platform/introduction-to-ios10/additional-framework-changes.md) guia.

## <a name="deprecated-apis"></a>APIs obsoletas

As seguintes APIs foram substituídas no iOS 10:

- O `CKDiscoverAllContactsOperation`, `CKDiscoveredUserInfo`, `CKDiscoverUserInfosOperation` e `CKFetchRecordChangesOperation` classes foram substituídas no CloudKit para iOS 10. Use o [CKDiscoverAllUserIdentitiesOperation](https://developer.xamarin.com/api/type/CloudKit.CKDiscoverUserIdentitiesOperation/), [CKUserIdentity](https://developer.xamarin.com/api/type/CloudKit.CKUserIdentity/) e [CKFetchRecordZoneChangesOperation](https://developer.xamarin.com/api/type/CloudKit.CKFetchRecordZoneChangesOperation/) classes (que oferece suporte a compartilhamento de registros) em vez disso.
- Vários [CKSubscription](https://developer.apple.com/reference/cloudkit/cksubscription) APIs (como assinaturas com base em consulta e zona) foram preteridos. Use o [CKRecordZoneSubscription](https://developer.xamarin.com/api/type/CloudKit.CKRecordZoneSubscription/) e [CKQuerySubscription](https://developer.xamarin.com/api/type/CloudKit.CKQuerySubscription/) APIs em vez disso.
- [NSPersistentStoreCoordnator](https://developer.xamarin.com/api/type/CoreData.NSPersistentStoreCoordinator/) símbolos relacionados ao conteúdo onipresente foram preteridos.
- `ADBannerView`, `ADInterstitialAd` e relacionados símbolos no [UIViewController](https://developer.xamarin.com/api/type/UIKit.UIViewController/) classe foram preteridos.
- [SKUniform](https://developer.apple.com/reference/spritekit/skuniform) símbolos relacionados a valores de ponto flutuante foram preteridos.
- O `UILocalNotification`, `UIMutableUserNotificationAction`, `UIMutableUserNotificationCategory`, `UIUserNotificationAction`, `UIUserNotificationCategory` e `UIUserNotificationSettings` classes de UIKit foram preteridos. Use o [notificações do usuário](#User-Notifications) framework em vez disso.
- O `HandleActionForLocalNotification`, `HandleActionForRemoteNotification`, `DidReceiveLocalNotification` e `DidReceiveRemoteNotification` WatchKit métodos foram substituídos. Use o `HandleActionForNotification` e `DidReceiveNotification` métodos em vez disso.
- O `DidReceiveLocalNotification` e `DidReceiveRemoteNotification` métodos do [WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate) foram preteridos. Criar uma instância do [UNUserNotificationCenterDelegate](https://developer.apple.com/reference/usernotifications/unusernotificationcenterdelegate) que implementa os métodos apropriados e atribuí-la para o `Delegate` propriedade o [UNUserNotificationCenter](https://developer.apple.com/reference/usernotifications/unusernotificationcenter) objeto.
- O **Game Center aplicativo** foi preterido e removidos do iOS. Se o aplicativo usa GameKit, ele _deve_ apresentar sua própria interface para exibir os recursos de GameKit, como tabelas, etc.

Consulte da Apple [iOS 9.3 às diferenças de API do iOS 10.0](https://developer.apple.com/library/prerelease/content/releasenotes/General/iOS10APIDiffs/index.html) documentação para obter uma lista completa de substituições.



## <a name="related-links"></a>Links relacionados

- [Exemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [O que há de novo no iOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewIniOS/Articles/iOS10.html#//apple_ref/doc/uid/TP40017084-SW1)
