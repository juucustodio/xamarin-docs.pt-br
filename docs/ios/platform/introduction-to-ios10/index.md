---
title: Introdução ao iOS 10
description: Este artigo apresenta todas as APIs e recursos novos e modificados disponíveis no iOS 10 para desenvolvedores do Xamarin. iOS.
ms.prod: xamarin
ms.assetid: FB91DFFE-CF5E-4253-92CB-78A6371259D9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: ce262faf2d79e6a2cc969df582446fdc2ec29bde
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032221"
---
# <a name="introduction-to-ios-10"></a>Introdução ao iOS 10

Com o novo SDK do iOS 10, a Apple incluiu novas APIs e serviços que permitem ao desenvolvedor criar novas categorias de aplicativos e recursos. Um aplicativo iOS agora pode estender as mensagens, Siri, telefone e mapas de aplicativos para fornecer funcionalidade avançada e envolvente para o usuário final que estava indisponível anteriormente.

Para obter mais informações sobre o iOS 10, consulte a documentação do [Ios + aplicativos](https://developer.apple.com/ios/) da Apple.

## <a name="whats-new-in-ios-10"></a>O que há de novo no iOS 10

A Apple adicionou várias novas APIs e serviços no iOS 10, juntamente com vários aprimoramentos nos recursos existentes, incluindo:

## <a name="adapting-to-the-true-tone-display"></a>Adaptando para a exibição de Tom verdadeiro

A tecnologia de vídeo de verdadeiro tom da Apple usa o sensor de luz ambiente em um dispositivo iOS para ajustar dinamicamente a cor e a intensidade da exibição para corresponder às condições de iluminação atuais. o iOS 10 fornece a nova chave [UIWhitePointAdaptivityStyle](https://developer.apple.com/library/prerelease/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW31) que pode ser adicionada ao arquivo de `Info.plist` do aplicativo e controla como o verdadeiro Tom aplica a mudança de cor padrão. 

Os seguintes valores estão disponíveis:

- `UIWhitePointAdaptivityStyleStandard` **padrão** -use o adaptivity de ponto branco padrão.
- `UIWhitePointAdaptivityStyleReading`-usado para aplicativos com foco em leitura.
- `UIWhitePointAdaptivityStyleGame`-usado para aplicativos focados em jogos.
- `UIWhitePointAdaptivityStyleVideo`-usado para aplicativos com foco em vídeo.
- `UIWhitePointAdaptivityStylePhoto`-usado para aplicativos focados em fotografia em que a fidelidade de cores é mais importante do que os ajustes ambientais de ponto de branco.

## <a name="app-extensions"></a>Extensões de aplicativo

A Apple forneceu vários novos pontos de extensão de aplicativo no iOS 10:

- Diretório de chamada
- Interface do usuário de tentativas e intenções
- Mensagens
- Conteúdo da notificação
- Notification Services
- Pacote de adesivo

Além disso, as extensões de aplicativo de teclado de terceiros têm os seguintes aprimoramentos:

- A nova propriedade `DocumentInputMode` da classe `UITextDocumentProxy` pode determinar o idioma de entrada de um documento e permitir que a extensão do teclado se alinhe com esse idioma.
- O novo método `HandleInputModeList` permite que a extensão do teclado exiba o menu do seletor de teclado do sistema em resposta à tecla do globo que está sendo tocado.

Para obter mais informações, consulte nossa [introdução às extensões](~/ios/platform/extensions.md), [integração de aplicativos de mensagens](~/ios/platform/message-app-integration/index.md), [introdução a sugestões proativas](~/ios/platform/search/proactive-suggestions.md), [introdução ao SiriKit](~/ios/platform/sirikit/index.md), [introdução às notificações de usuário](~/ios/platform/user-notifications/index.md) e à Apple [ Guia de programação de extensão de aplicativo](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214).

## <a name="app-search-enhancements"></a>Aperfeiçoamentos na pesquisa de aplicativo

O principal Spotlight no iOS 10 fornece vários aprimoramentos para a pesquisa de aplicativos, como:

- **Crowdsourcing a popularidade do link profundo (com privacidade diferencial)** – fornece uma maneira de promover o conteúdo do aplicativo vinculado em profundidade nos resultados da pesquisa.
- **Pesquisa no aplicativo** – use a nova classe `CSSearchQuery` para fornecer uma capacidade de pesquisa no aplicativo em destaque semelhante à forma como os aplicativos mail, messages e Notes funcionam.
- **Continuação da pesquisa** – permite que um usuário inicie uma pesquisa no Spotlight ou no Safari, em seguida, abra um aplicativo e continue a pesquisa.
- **Visualização dos resultados da validação** – a [ferramenta de validação da API de pesquisa de aplicativos](https://search.developer.apple.com/appsearch-validation-tool) da Apple agora exibe uma representação visual da marcação de um site e da vinculação profunda ao preformar testes.
- **Compartilhamento de imagem de aplicativo de mensagem** – permite que imagens populares no aplicativo fornecidas para compartilhamento em mensagens (por meio de uma extensão de aplicativo de mensagem) apareçam em pesquisas de destaque.

Para saber mais, consulte nosso guia de [aprimoramentos de pesquisa de aplicativo](~/ios/platform/search/app-search-enhancements.md) .

## <a name="apple-pay-enhancements"></a>Aprimoramentos de Apple Pay

A Apple fez vários aprimoramentos para Apple Pay no iOS 10 que permitem ao usuário fazer pagamentos seguros de sites e por meio de interação com siri e mapas.

Com o iOS 10, foram adicionadas várias novas APIs que funcionam com o iOS e o watchOS para dar suporte a redes de pagamento dinâmico e um novo ambiente de teste da área restrita.

Além disso, a estrutura PassKit foi expandida para dar suporte a Apple Pay fora do `UIKit` e para permitir que os emissores apresentem seus cartões de dentro de seus aplicativos.

Para saber mais, consulte nosso guia de [aprimoramentos de Apple Pay](~/ios/platform/apple-pay.md) .

## <a name="alternate-app-icons"></a>Ícones de aplicativos alternativos

A Apple adicionou vários aprimoramentos ao iOS 10,3 que permitem que um aplicativo Gerencie seu ícone:

- `ApplicationIconBadgeNumber`-Obtém ou define o emblema do ícone do aplicativo no Springboard.
- `SupportsAlternateIcons`-se `true` o aplicativo tem um conjunto alternativo de ícones.
- `AlternateIconName`-retorna o nome do ícone alternativo selecionado no momento ou `null` se estiver usando o ícone principal.
- `SetAlternameIconName`-Use esse método para alternar o ícone do aplicativo para o ícone alternativo fornecido.

Para saber mais, confira nosso guia de [ícones de aplicativos alternativos](~/ios/app-fundamentals/images-icons/alternate-app-icons.md) .

## <a name="introduction-to-callkit"></a>Introdução ao CallKit

A nova API do CallKit no iOS 10 fornece uma maneira para que os aplicativos de VOIP se integrem à interface do usuário do iPhone e forneçam uma interface familiar e uma experiência para os usuários finais. Com essa API, os usuários podem exibir e interagir com chamadas de VOIP da tela de bloqueio do dispositivo iOS e gerenciar contatos usando as exibições de **favoritos** e **recentes** do aplicativo de telefone.

Além disso, a API do CallKit fornece a capacidade de criar extensões de aplicativo que podem associar um número de telefone com um nome (ID do chamador) ou informar ao sistema quando um número deve ser bloqueado (bloqueio de chamada).

Para saber mais, consulte nossa [introdução ao guia de Callkit](~/ios/platform/callkit.md) .

## <a name="message-app-integration"></a>Integração do aplicativo de mensagens

o iOS 10 permite a inclusão de uma extensão de aplicativo de mensagem na solução Xamarin. iOS que se integra ao aplicativo de **mensagens** e apresenta novas funcionalidades para o usuário. A extensão pode enviar texto, adesivos, arquivos de mídia e mensagens interativas. Dois tipos de extensão de aplicativo de mensagem estão disponíveis:

- **Pacotes de adesivo** -contém uma coleção de adesivos que o usuário pode adicionar a uma mensagem. Os pacotes adesivos podem ser criados sem escrever nenhum código.
- **aplicativo IMessage** – pode apresentar uma interface do usuário personalizada no aplicativo mensagens para selecionar adesivos, inserir texto, incluir arquivos de mídia (com conversões de tipo opcionais) e criar, editar e enviar mensagens de interação.

Para saber mais, consulte nosso guia de [integração do aplicativo de mensagens](~/ios/platform/message-app-integration/index.md) .

## <a name="news-publisher-enhancements"></a>Aprimoramentos no Publicador de notícias

Com o iOS 10, a Apple permitirá que qualquer um das principais revistas e novas organizações para bloggers e editores independentes assinem e forneçam conteúdo para o aplicativo Apple News. Para saber mais, confira a documentação de [recursos de notícias](https://newsresources.apple.com/) da Apple.

## <a name="providing-haptic-feedback"></a>Como fornecer comentários hápticos

No iPhone 7 e no iPhone 7 Plus, a Apple incluiu novas respostas haptics que fornecem maneiras adicionais de envolver fisicamente o usuário. Use as novas opções de comentários do tactile para obter a atenção do usuário e reforçar suas ações.

Vários elementos internos da interface do usuário já fornecem comentários haptics, como seletores, interruptores e controles deslizantes. Agora, o iOS 10 adiciona a capacidade de programaticamente disparar haptics usando uma subclasse concreta da classe `UIFeedbackGenerator`.

Para saber mais, consulte nosso guia de [comentários sobre como fornecer Haptic](~/ios/user-interface/ios-ui/haptic-feedback.md) .

## <a name="proactive-suggestions"></a>Sugestões proativas

o iOS 10 apresenta novas maneiras de conduzir o envolvimento a um aplicativo, permitindo que o sistema apresente informações úteis de forma proativa automaticamente para o usuário em momentos apropriados. Assim como o iOS 9 fornecia a capacidade de adicionar uma pesquisa profunda ao aplicativo usando as sugestões de Spotlight, entrega e Siri, com o iOS 10, um aplicativo pode expor a funcionalidade que pode ser apresentada ao usuário pelo sistema de dentro dos seguintes locais:

- O alternador de aplicativo
- A tela de bloqueio
- CarPlay
- Mapas
- Interações de Siri
- Sugestões de apelidos

Um aplicativo expõe essa funcionalidade ao sistema usando uma coleção de tecnologias como [NSUserActivity](xref:Foundation.NSUserActivity), Web Markup, Core Spotlight, MapKit, Media Player e UIKit.

Para saber mais, consulte nosso guia [de introdução ao Proactive Suggestions](~/ios/platform/search/proactive-suggestions.md) .

## <a name="request-app-review"></a>Solicitação de revisão do aplicativo

Novo no iOS 10,3, o método `RequestReview()` permite que um aplicativo iOS peça ao usuário para classificá-lo ou examiná-lo. Embora esse método possa ser chamado em qualquer ponto em que faça sentido na experiência do usuário, o processo de revisão é regido e manipulado pela política da loja de aplicativos. Como resultado, esse método pode ou não exibir um alerta e nunca deve ser chamado em resposta a uma ação do usuário, como tocar em um botão.

Para saber mais, consulte nosso guia de [revisão do aplicativo de solicitação](~/ios/platform/request-app-review.md) .

## <a name="security-and-privacy-enhancements"></a>Aprimoramentos de segurança e privacidade

A Apple fez vários aprimoramentos na segurança e na privacidade no iOS 10, que ajudarão o desenvolvedor a melhorar a segurança de seus aplicativos e garantir a privacidade do usuário final.

Como resultado, os aplicativos executados no iOS 10 (ou posterior) devem declarar estaticamente sua intenção de acessar recursos específicos ou informações do usuário inserindo uma ou mais chaves de privacidade específicas em seus arquivos de `Info.plist` que explicam ao usuário por que o aplicativo deseja obter acesso.

Para saber mais, confira nosso guia de [aprimoramentos de segurança e privacidade](~/ios/app-fundamentals/security-privacy.md) .

## <a name="sirikit"></a>SiriKit

Novo no iOS 10, o SiriKit permite que um aplicativo Xamarin. iOS forneça serviços que são acessíveis para o usuário usando o Siri em um dispositivo iOS. Essa funcionalidade é fornecida em uma ou mais extensões de aplicativo usando as novas estruturas de **interface do usuário** de **tentativas** e intenções.

O SiriKit dá suporte aos seguintes domínios de serviço:

- Chamada de áudio ou vídeo.
- Reserva de um Ride.
- Gerenciando exercícios.
- Mensagens.
- Pesquisando fotos.
- Enviando ou recebendo pagamentos.

Quando o usuário faz uma solicitação de Siri envolvendo um dos serviços da extensão do aplicativo, o SiriKit envia a extensão um objeto de **intenção** que descreve a solicitação do usuário junto com quaisquer dados de suporte. Em seguida, a extensão do aplicativo gera o objeto de **resposta** apropriado para a **intenção**determinada, detalhando como a extensão pode lidar com a solicitação.

Embora Siri geralmente lide com toda a interação do usuário, a extensão do aplicativo pode usar a estrutura **da interface do** usuário da intenção para apresentar uma interface de usuário avançada e personalizada, apresentando a identidade visual do aplicativo e informações adicionais.

Para saber mais, consulte nossa [introdução ao guia de SiriKit](~/ios/platform/sirikit/index.md) .

## <a name="speech-recognition"></a>Reconhecimento de fala

o iOS 10 inclui um novo Speech API que permite ao aplicativo dar suporte ao reconhecimento de fala contínuo e transcrever a fala (a partir de fluxos de áudio dinâmicos ou gravados) em texto.

Como o reconhecimento de fala requer a transmissão e o armazenamento temporário de dados nos servidores da Apple, o aplicativo _deve_ solicitar a permissão do usuário para executar o reconhecimento, incluindo a chave de `NSSpeechRecognitionUsageDescription` em seu arquivo de `Info.plist` e chamar o `SFSpeechRecognizer.RequestAutorization` forma.

Para saber mais, consulte nossa [introdução ao guia de reconhecimento de fala](~/ios/platform/speech.md) .

## <a name="user-notifications"></a>Notificações do usuário

Novo no iOS 10, a estrutura de notificação de usuário permite a entrega e a manipulação de notificações locais e remotas. Usando essa estrutura, a extensão do aplicativo ou do aplicativo pode agendar a entrega de notificações locais, especificando um conjunto de condições, como local ou hora do dia.

Além disso, o aplicativo ou a extensão pode receber (e potencialmente modificar) as notificações locais e remotas à medida que elas são entregues ao dispositivo iOS do usuário.

A nova estrutura de IU de notificação do usuário permite que a extensão do aplicativo ou do aplicativo Personalize a aparência de notificações locais e remotas quando elas são apresentadas ao usuário.

Para obter mais informações, consulte nosso guia da [estrutura de notificações do usuário](~/ios/platform/user-notifications/index.md) .

## <a name="video-subscriber-account"></a>Conta do assinante de vídeo

Novo para iOS 10, a estrutura de conta do assinante de vídeo permite que aplicativos que dão suporte a streaming autenticado ou vídeo sob demanda sejam autenticados com seu cabo ou provedor de TV por satélite usando uma experiência de logon único para o usuário final.

## <a name="wide-color"></a>Cores amplas

o iOS 10 estende o suporte para formatos de pixel de intervalo estendido e espaços de cores de ampla gama em todo o sistema, incluindo estruturas como gráficos principais, imagem principal, metal e AVFoundation. O suporte para dispositivos com monitores largos de cores é mais facilitou, fornecendo esse comportamento em toda a pilha gráfica.

Além disso, o [UIKit](xref:UIKit) foi modificado para funcionar no novo colorspace do **sRGB** estendido, facilitando a combinação de cores em grandes gamas de cores sem perda significativa de desempenho.

A Apple oferece as seguintes práticas recomendadas ao trabalhar com cores amplas:

- O [UIColor](xref:UIKit.UIColor) agora usa o espaço de cores sRGB e não fixe mais valores para a `0.0` para `1.0` intervalo. Se o aplicativo depender do comportamento anterior do fixe, ele precisará ser modificado para iOS 10.
- O ambiente de desenho será configurado para o espaço de cores sRGB ao executar o desenho de `UIView` personalizado em um iPad pro.
- Se o aplicativo executar renderização personalizada de `UIImages`, use a nova classe [UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer) para especificar o uso dos formatos de intervalo estendido ou de intervalo padrão.
- Ao usar uma API de nível baixo, como gráficos principal ou metal, para fornecer processamento de imagens, o desenvolvedor deve usar um espaço de cores de intervalo estendido e um formato de pixel que dê suporte a valores de ponto flutuante de 16 bits. Quando necessário, o desenvolvedor terá que fixe manualmente os valores de componente de cor.
- Os principais gráficos, a imagem principal e os sombreadores de desempenho de metal fornecem novos métodos para conversão entre os dois espaços de cores.

Para obter mais informações, consulte nosso [introdução ao guia de cores largo](~/ios/platform/wide-color.md) .

## <a name="widget-enhancements"></a>Aprimoramentos do widget

A Apple introduziu vários aprimoramentos no sistema de widget para garantir que os widgets fiquem ótimos em qualquer plano de fundo que exista na nova tela de bloqueio do iOS 10. A propriedade [NotificationCenterVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1613917-notificationcentervibrancyeffect) foi preterida e foi substituída pelas novas propriedades [WidgetPrimaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771278-widgetprimaryvibrancyeffect) ou [WidgetSecondaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771277-widgetsecondaryvibrancyeffect) . Além disso, os widgets agora contêm uma propriedade [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) que permite que o desenvolvedor descreva a quantidade de conteúdo disponível e permite que o usuário expanda e recolha o conteúdo.

Para saber mais, consulte nosso guia de [aprimoramentos do widget de pesquisa e página inicial](~/ios/platform/search/widgets.md) .

## <a name="additional-framework-changes"></a>Alterações de estrutura adicionais

Além das principais alterações de estrutura e adições listadas acima, a Apple fez muitas alterações de estrutura secundárias adicionais no iOS 10.

Para saber mais, consulte nosso guia de [alterações de estrutura adicionais](~/ios/platform/introduction-to-ios10/additional-framework-changes.md) .

## <a name="deprecated-apis"></a>APIs obsoletas

As seguintes APIs foram preteridas no iOS 10:

- As classes `CKDiscoverAllContactsOperation`, `CKDiscoveredUserInfo`, `CKDiscoverUserInfosOperation` e `CKFetchRecordChangesOperation` foram preteridas no CloudKit para iOS 10. Use as classes [CKDiscoverAllUserIdentitiesOperation](xref:CloudKit.CKDiscoverUserIdentitiesOperation), [CKUserIdentity](xref:CloudKit.CKUserIdentity) e [CKFetchRecordZoneChangesOperation](xref:CloudKit.CKFetchRecordZoneChangesOperation) (que dão suporte ao compartilhamento de registros) em vez disso.
- Várias APIs [CKSubscription](https://developer.apple.com/reference/cloudkit/cksubscription) (como assinaturas baseadas em zona e com base em consulta) foram preteridas. Use as APIs [CKRecordZoneSubscription](xref:CloudKit.CKRecordZoneSubscription) e [CKQuerySubscription](xref:CloudKit.CKQuerySubscription) em vez disso.
- Os símbolos [NSPersistentStoreCoordinator](xref:CoreData.NSPersistentStoreCoordinator) relacionados ao conteúdo onipresente foram preteridos.
- `ADBannerView`, `ADInterstitialAd` e símbolos relacionados na classe [UIViewController](xref:UIKit.UIViewController) foram preteridos.
- Os símbolos [SKUniform](https://developer.apple.com/reference/spritekit/skuniform) relacionados a valores de ponto flutuante foram preteridos.
- As classes `UILocalNotification`, `UIMutableUserNotificationAction`, `UIMutableUserNotificationCategory`, `UIUserNotificationAction`, `UIUserNotificationCategory` e `UIUserNotificationSettings` de UIKit foram preteridas. Em vez disso, use a estrutura de [notificações do usuário](#user-notifications) .
- Os métodos `HandleActionForLocalNotification`, `HandleActionForRemoteNotification`, `DidReceiveLocalNotification` e `DidReceiveRemoteNotification` WatchKit foram preteridos. Em vez disso, use os métodos `HandleActionForNotification` e `DidReceiveNotification`.
- Os métodos `DidReceiveLocalNotification` e `DidReceiveRemoteNotification` do [WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate) foram preteridos. Crie uma instância de [UNUserNotificationCenterDelegate](https://developer.apple.com/reference/usernotifications/unusernotificationcenterdelegate) que implementa os métodos apropriados e atribua-o à propriedade `Delegate` do objeto [UNUserNotificationCenter](https://developer.apple.com/reference/usernotifications/unusernotificationcenter) .
- O **aplicativo Game Center** foi preterido e removido do Ios. Se o aplicativo usar GameKit, ele _deverá_ apresentar sua própria interface para exibir recursos de GameKit, como placares, etc.

Consulte a documentação de [diferenças da API do ios 9,3 para ios 10,0](https://developer.apple.com/library/prerelease/content/releasenotes/General/iOS10APIDiffs/index.html) da Apple para obter uma lista completa de reprovações.

## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
