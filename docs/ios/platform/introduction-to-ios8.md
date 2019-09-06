---
title: Introdução ao iOS 8
description: Com o iOS 8, a Apple forneceu uma infinidade de novas estruturas e APIs para excite e fascinam desenvolvedores. Neste guia, apresentaremos essas novas APIs e veremos como o iOS 8 pode beneficiar os desenvolvedores e os usuários.
ms.prod: xamarin
ms.assetid: 33AD66C0-3743-49FE-9DCE-88ED3A16BA63
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/14/2017
ms.openlocfilehash: 1b3c1af480db2c5642ef0b16aed795d2bb43290f
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292401"
---
# <a name="introduction-to-ios-8"></a>Introdução ao iOS 8

_Com o iOS 8, a Apple forneceu uma infinidade de novas estruturas e APIs para excite e fascinam desenvolvedores. Neste guia, apresentaremos essas novas APIs e veremos como o iOS 8 pode beneficiar os desenvolvedores e os usuários._

o iOS 7 alterou visualmente toda a interface do usuário do iOS do que os usuários e desenvolvedores esperam esperar, diretamente do primeiro sistema operacional do iPhone. O IOS 8 continua com isso fornecendo muitas estruturas para desenvolvedores, o que permite aos usuários controlar quase todos os aspectos de sua vida diretamente do seu iPhone. Por exemplo, a integridade e a adequação podem ser analisadas com *HealthKit*, as senhas são obsoleta com autenticação biométrica usando *LocalAuthentication*, *as extensões de aplicativo* abrem um canal de comunicação entre aplicativos de terceiros e  *O HomeKit* permite a capacidade de transformar sua casa em uma casa do futuro. 

Se o iOS 7 estava sobre a desativação dos usuários, o iOS 8 se concentra em desiluminar os desenvolvedores com uma ampla gama dessas novas ferramentas Tasty. 

Este guia apresenta as novas APIs para desenvolvedores do Xamarin. iOS.  

Também há algumas APIs que foram preteridas no iOS 8, que são detalhadas no final deste documento.

## <a name="requirements"></a>Requisitos

Os itens a seguir são necessários para criar aplicativos iOS 8 no Visual Studio para Mac:

- **Xcode 7 e Ios 8 ou mais recente** – as APIs do Xcode e do Ios mais recentes da Apple precisam ser instaladas e configuradas no computador do desenvolvedor.
- **Visual Studio para Mac** – a versão mais recente do Visual Studio para Mac deve ser instalada e configurada no dispositivo do usuário.
- **dispositivo IOS 8 ou simulador** – um dispositivo IOS executando a versão mais recente do IOS 8 para teste.

## <a name="home-and-leisure"></a>Casa e lazer

o iOS 8 ajudou a implantar a Apple firmemente e o dispositivo iOS diretamente no coração de sua casa por meio do uso de HomeKit e HealthKit. Nesta seção, veremos como essas novas estruturas funcionam e como elas podem ser integradas ao seu aplicativo Xamarin. iOS.

## <a name="homekit"></a>HomeKit

Controlar seus dispositivos de seu iPhone não é uma nova aplicação de tecnologia; muitos produtos domésticos conectados podem ser controlados por meio de um aplicativo iOS. No entanto, HomeKit agora leva isso um passo adiante, promovendo um protocolo comum para dispositivos de automação inicial e disponibilizando uma API pública para determinados fabricantes, como iHome, Philips e Honeywell. Para o usuário, isso significa que eles podem controlar quase todos os aspectos de sua casa diretamente de dentro de um aplicativo. É irrelevante que eles saibam que estão usando uma lâmpada de matiz Philips ou um alarme de aninhamento. Os usuários também podem encadear vários processos Smart Home juntos em "cenas".

Com o HomeKit, aplicativos de terceiros e Siri podem descobrir acessórios e adicioná-los ao seu banco de dados de configuração residencial pessoal, editar e agir sobre esses dados e se comunicar com acessórios e seus serviços para executar uma ação.

### <a name="configuration"></a>Configuração

O diagrama a seguir mostra a hierarquia básica da configuração dos acessórios do HomeKit:

![](introduction-to-ios8-images/image1.png "Este diagrama mostra a hierarquia básica da configuração de acessórios do HomeKit")
 
Para começar a usar o HomeKit, os desenvolvedores precisarão certificar-se de que seu perfil de provisionamento tenha o serviço HomeKit selecionado. A Apple também forneceu aos desenvolvedores um suplemento do HomeKit Simulator para o Xcode. Isso pode ser encontrado no [centro de desenvolvedores da Apple](https://developer.apple.com/downloads/index.action), `Hardware IO Tools for Xcode`em. 

Para obter mais informações, consulte nosso guia de [homekit](~/ios/platform/homekit.md) .

## <a name="healthkit"></a>HealthKit

O HealthKit é uma estrutura introduzida no iOS 8 que fornece um repositório de dados centralizado, coordenado e seguro para informações relacionadas à integridade. O sistema operacional garante a privacidade e a segurança das informações de integridade e, com o aplicativo de integridade, um painel para o usuário. Com a permissão do usuário, os aplicativos podem ler e gravar uma ampla variedade de informações de integridade.

Para obter mais informações sobre como usar isso em seu aplicativo Xamarin. iOS, consulte a [introdução ao](~/ios/platform/healthkit.md) guia de HealthKit.

## <a name="extending-iphone-functionality"></a>Estendendo a funcionalidade do iPhone
Com o iOS8, os desenvolvedores estão recebendo muito mais controle sobre quem pode usar seu aplicativo e maior capacidade para mais comunicação aberta entre aplicativos de terceiros. Recursos como extensões de aplicativo e seletor de documento abrem um mundo de possibilidades de como os aplicativos podem ser usados no ecossistema da Apple.

### <a name="app-extensions"></a>Extensões de aplicativo
As extensões de aplicativo, para simplificar, são uma maneira para que aplicativos de terceiros se comuniquem uns com os outros. Para manter os padrões de alta segurança e defender a integridade dos aplicativos em área restrita, essa comunicação não acontece diretamente entre os aplicativos. Em vez disso, ele é executado por uma *extensão* no meio.

A primeira etapa na criação de uma extensão de aplicativo é definir o ponto de extensão correto — isso é importante para garantir o comportamento e a disponibilidade das APIs corretas. Para criar uma extensão de aplicativo no Visual Studio para Mac, adicione-a a um aplicativo existente adicionando um novo projeto à sua solução.

Na caixa de diálogo **novo projeto** , **C#**  > navegue até **Ios** > **API unificada** > **extensões**, conforme ilustrado na captura de tela abaixo:

![](introduction-to-ios8-images/image2.png "Criando uma nova extensão")
 
A caixa de diálogo novo projeto fornece sete novos modelos de projeto para a criação de extensões de aplicativo e são discutidos abaixo. Observe que muitas das extensões se relacionam com outras novas APIs no iOS, como o seletor de documento:

- **Ação** – permite que os desenvolvedores criem botões de ação personalizados exclusivos, permitindo que os usuários executem determinadas tarefas
- **Teclado personalizado** – isso permite que os desenvolvedores adicionem à variedade de placas inseridas da Apple adicionando seu próprio personalizado. O teclado popular, Swype usa isso para trazer o teclado para o iOS.
- **Seletor de documento** – contém um controlador de exibição de seletor de documento que permite aos usuários acessar arquivos fora da área restrita do aplicativo.
- **Provedor de arquivos do seletor de documento** – fornece armazenamento seguro para arquivos usando o seletor de documento.
- **Edição de fotos** – isso se expande sobre os filtros e as ferramentas de edição já fornecidos pela Apple no aplicativo de fotos para dar aos usuários mais opções de controle e mais informações ao editar suas fotos.
- **Hoje** , isso oferece aos aplicativos a capacidade de exibir widgets na seção atual do centro de notificações.

Para obter mais informações sobre como usar extensões de aplicativo no Xamarin, consulte a [introdução ao guia de extensões de aplicativo](~/ios/platform/extensions.md) .

### <a name="touch-id"></a>ID de Toque

A ID de toque foi introduzida no iOS 7 como um meio de autenticar o usuário — semelhante a uma senha. No entanto, ele estava limitado a desbloquear o dispositivo, usando a loja de aplicativos, usando o iTunes e Autenticando apenas o conjunto de chaves do iCloud 

Agora há duas maneiras de usar o Touch ID como um mecanismo de autenticação em aplicativos iOS 8 usando a API de autenticação local. No momento, não é possível usar a autenticação local para autenticar remotamente.

Em primeiro lugar, ele ajuda os serviços de conjunto de chaves existentes por meio do uso de novas listas de controle de acesso (ACLs) do conjunto de chaves. Os dados do conjunto de chaves podem ser desbloqueados com a autenticação bem-sucedida de uma impressão digital dos usuários.

Em segundo lugar, o LocalAuthentication fornece dois métodos para autenticar seu aplicativo localmente. Os desenvolvedores devem `CanEvaluatePolicy` usar o para determinar se o dispositivo é capaz de aceitar a ID de `EvaluatePolicy` toque e, em seguida, iniciar a operação de autenticação.

Para obter mais informações sobre o Touch ID e saber como integrá-lo a um aplicativo Xamarin. iOS, consulte a [introdução aos guias touchid](~/ios/platform/touchid.md) .

### <a name="document-picker"></a>Seletor de documentos

O seletor de documento funciona com uma unidade iCloud de usuários para permitir que o usuário abra arquivos que foram criados em um aplicativo diferente, importe e manipule-os e exporte-os novamente. Isso cria um fluxo de trabalho intuitivo e, portanto, uma experiência muito melhor para os usuários. a sincronização do iCloud leva isso um pouco além – todas as alterações feitas em um aplicativo também serão refletidas consistentemente em todos os seus dispositivos.

Para saber mais sobre o seletor de documentos mais detalhadamente e para saber como integrá-lo a um aplicativo Xamarin. iOS, consulte a [introdução ao guia do seletor de documento](~/ios/platform/document-picker.md) .

### <a name="handoff"></a>Handoff

A entrega, que faz parte do recurso de continuidade maior, leva mais adiante em direção à integração do OS X e do iOS. Isso inclui o essoltar de plataforma cruzada, a capacidade de fazer chamadas do iPhone, SMS no iPad e no Mac e aprimoramentos no compartilhamento de Internet do seu iPhone.

A entrega funciona com o iOS 8 e o Yosemite e requer que uma conta do iCloud esteja conectada a todos os diferentes dispositivos que você deseja usar. Ele deve funcionar com a maioria dos aplicativos da Apple pré-instalados, incluindo Safari, iWork, mapas, calendários e contatos.

Para obter mais informações, consulte nosso guia de [entrega](~/ios/platform/handoff.md) .

## <a name="unified-storyboards"></a>Storyboards unificadas
o iOS 8 inclui um novo mecanismo mais simples de usar para criar a interface do usuário — o storyboard unificado. Com um único Storyboard para cobrir todos os diferentes tamanhos de tela de hardware, as exibições rápidas e responsivas podem ser criadas em um "design Once" de uma vez, usar muitos ".

Antes do iOS8, os desenvolvedores `UIInterfaceOrientation` usaram a distinção entre os modos retrato e paisagem `UIInterfaceIdiom` e para distinguir entre os dispositivos IOS. No iOS8, não é mais necessário criar storyboards separados para dispositivos iPhone e iPad – a orientação e o dispositivo são determinados usando *classes de tamanho*.

Cada dispositivo é definido por uma classe de tamanho, tanto na vertical quanto no eixo horizontal, e há dois tipos de classes de tamanho no iOS 8:

- **Regular** -isso é para um tamanho de tela grande (como um iPad) ou um gadget que dá a impressão de um tamanho grande (como um UIScrollView
- **Compact** -isso é para dispositivos menores (como o iPhone). Esse tamanho leva em consideração a orientação do dispositivo.

Se os dois conceitos forem usados juntos, o resultado será uma grade 2 x 2 que define os diferentes tamanhos possíveis que podem ser usados nas orientações diferentes, como visto no diagrama a seguir:

![](introduction-to-ios8-images/image3.png "Um diagrama que representa a grade 2 x 2 que define os diferentes tamanhos possíveis que podem ser usados nas orientações diferentes")
 
Para obter mais informações sobre classes de tamanho, consulte a [introdução aos storyboards unificados](~/ios/user-interface/storyboards/unified-storyboards.md).

## <a name="photo-kit"></a>Kit de fotos
O Photo kit é uma nova estrutura que permite que os aplicativos consultem a biblioteca de imagens do sistema e criem interfaces de usuário personalizadas para exibir e modificar seu conteúdo. Ele inclui várias classes que representam ativos de imagem e vídeo, bem como coleções de ativos, como álbuns e pastas.

Para obter mais informações, consulte nosso guia de [PhotoKit](~/ios/platform/photokit.md) .

## <a name="games"></a>Jogos

<a name="scenekit" />

### <a name="scene-kit"></a>Kit de cena

O Scene kit é uma API de grafo de cena 3D que simplifica o trabalho com gráficos 3D. Ele foi introduzido pela primeira vez no OS X 10,8 e chegou ao iOS 8. Com o kit de cena, a criação de visualizações 3D de imersão e jogos 3D casuais não requer experiência em OpenGL. Aproveitando os conceitos comuns de grafo de cena, o kit de cena abstrai as complexidades do OpenGL e do OpenGL ES, facilitando muito a adição de conteúdo 3D a um aplicativo. No entanto, se você for um especialista em OpenGL, o kit de cena também terá excelente suporte para ligar diretamente com OpenGL. Ele também inclui vários recursos que complementam gráficos 3D, como a física, e se integram muito bem com várias outras estruturas da Apple, como animação principal, imagem básica e kit de Sprite.

Para obter mais informações, consulte nossa documentação do [SceneKit](~/ios/platform/gaming/scenekit.md) .

<a name="spritekit" />

### <a name="sprite-kit"></a>Kit de Sprite

O sprite Kit, a estrutura de jogos 2D da Apple, tem alguns novos recursos interessantes no iOS 8 e no OS X Yosemite. Isso inclui a integração com o kit de cena, suporte a sombreador, iluminação, sombras, restrições, geração de mapa normal e aprimoramentos de física. Em particular, os novos recursos de física tornam muito fácil adicionar efeitos realistas a um jogo.

Para obter mais informações, consulte nossa documentação do [SpriteKit](~/ios/platform/gaming/spritekit.md) .

## <a name="other-changes"></a>Outras alterações
Além das principais alterações no iOS 8 descritas acima, a Apple também atualizou muitas estruturas existentes. Eles são detalhados abaixo:

- **[Imagem principal](https://developer.apple.com/library/prerelease/ios/documentation/GraphicsImaging/Reference/CoreImagingRef/index.html#//apple_ref/doc/uid/TP40001171)** – a Apple expandiu sua estrutura de processamento de imagens adicionando um suporte melhor à detecção de regiões retangulares e códigos QR dentro de imagens. Mike Bluestein explora isso em sua postagem de blog chamada [detecção de imagem com direito no Ios 8](https://blog.xamarin.com/image-detection-in-ios-8/)

## <a name="deprecated-apis"></a>APIs obsoletas
Com todos os aprimoramentos feitos no iOS 8, várias APIs foram preteridas. Alguns deles são detalhados abaixo.

- **[UIApplication](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplication_Class/index.html#//apple_ref/occ/cl/UIApplication)** – os métodos e as propriedades usados para registrar notificações remotas foram preteridos. Esses são registerForRemoteNotificationTypes e enabledRemoteNotificationTypes.
- **[UIViewController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/cl/UIViewController)** – as classes de características e de tamanho substituiram os métodos e as propriedades usados para descrever a orientação da interface. Consulte a [introdução aos storyboards unificados](~/ios/user-interface/storyboards/unified-storyboards.md) para obter mais informações sobre como usá-los.

- **[UISearchDisplayController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UISearchDisplayController_Class/index.html#//apple_ref/occ/cl/UISearchDisplayController)** – isso foi substituído por UISearchController no iOS8.

## <a name="summary"></a>Resumo
Neste artigo, examinamos alguns dos novos recursos introduzidos pela Apple no iOS 8.



## <a name="related-links"></a>Links relacionados

- [UIKitEnhancements (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-uikitenhancements)
- [Introdução às extensões de aplicativo](~/ios/platform/extensions.md)
- [Introdução ao CloudKit](~/ios/data-cloud/intro-to-cloudkit.md)
- [Introdução ao seletor de documento](~/ios/platform/document-picker.md)
- [Introdução ao HealthKit](~/ios/platform/healthkit.md)
- [Introdução aos controles manuais da câmera](~/ios/user-interface/controls/intro-to-manual-camera-controls.md)
- [Introdução ao Touchid](~/ios/platform/touchid.md)
- [Introdução aos storyboards unificados](~/ios/user-interface/storyboards/unified-storyboards.md)
