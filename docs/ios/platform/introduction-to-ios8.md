---
title: Introdução ao iOS 8
description: Com o iOS 8, a Apple forneceu uma grande quantidade de novas estruturas e APIs para empolguem e encantem os desenvolvedores. Neste guia introduzir essas novas APIs e ver como o iOS 8 pode se beneficiar desenvolvedores e usuários.
ms.prod: xamarin
ms.assetid: 33AD66C0-3743-49FE-9DCE-88ED3A16BA63
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/14/2017
ms.openlocfilehash: 9299322eb20561444262c2b2ba87191d2bddcde4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61317596"
---
# <a name="introduction-to-ios-8"></a>Introdução ao iOS 8

_Com o iOS 8, a Apple forneceu uma grande quantidade de novas estruturas e APIs para empolguem e encantem os desenvolvedores. Neste guia introduzir essas novas APIs e ver como o iOS 8 pode se beneficiar desenvolvedores e usuários._

visualmente, o iOS 7 alterado a interface do usuário iOS inteira de quais usuários e desenvolvedores tinham se acostumaram diretamente do iPhone primeiro sistema operacional. IOS 8 continua com isso, fornecendo muitas estruturas para desenvolvedores, que permite aos usuários controlar quase todos os aspectos de sua vida diretamente do seu iPhone. Por exemplo integridade e adequação podem ser analisados com *HealthKit*, senhas são obsoleta com usando a autenticação biométrica *LocalAuthentication*, *extensões de aplicativo*abrir um canal de comunicação entre aplicativos de terceiros 3ª, e *HomeKit* permite que a capacidade de transformar sua casa em casa do futuro. 

Se iOS 7 era sobre encantar os usuários, o iOS 8 enfoca satisfazer aos desenvolvedores uma grande variedade dessas novas ferramentas saborosos. 

Este guia apresenta as novas APIs para desenvolvedores do xamarin. IOS.  

Há também algumas APIs que foram preteridas no iOS 8, que são detalhadas no final deste documento.

## <a name="requirements"></a>Requisitos

A seguir é necessárias para criar aplicativos iOS 8 no Visual Studio para Mac:

- **Xcode 7 e iOS 8 ou mais recente** – Xcode e APIs do iOS mais recente da Apple precisa ser instalado e configurado no computador do desenvolvedor.
- **O Visual Studio para Mac** – a versão mais recente do Visual Studio para Mac deve ser instalada e configurada no dispositivo do usuário.
- **iOS 8 dispositivo ou simulador** – um dispositivo iOS executando a versão mais recente do iOS 8 para teste.

## <a name="home-and-leisure"></a>Página inicial e Lazer

iOS 8 tenham ajudado a planta firmemente Apple e o dispositivo iOS diretamente para o coração da sua casa com o uso de HomeKit e HealthKit. Nesta seção, vamos examinar como os dois dessas novas estruturas funcionam e como pode ser integrados em seu aplicativo xamarin. IOS.

## <a name="homekit"></a>HomeKit

Controlar seus dispositivos do seu iPhone não é um novo aplicativo da tecnologia; Muitos produtos de conectado de casa podem ser controlados por meio de um aplicativo iOS. No entanto HomeKit agora leva isso um passo adiante, promovendo um protocolo comum para dispositivos de automação residencial e ao disponibilizar uma API pública para determinados fabricantes, como iHome, Philips e Honeywell. Para o usuário, isso significa que eles podem controlar quase todos os aspectos da sua casa diretamente de dentro de um aplicativo. Ele é irrelevante para que eles saibam que estão usando uma lâmpada Philips matiz, ou um alarme de aninhamento. Os usuários também podem encadear vários processos iniciais inteligentes juntos em "Cenas".

Com o HomeKit, Siri e aplicativos de terceiros podem descobrir Acessórios e adicioná-los ao seu banco de dados de configuração de base pessoal, editar e agir sobre esses dados e se comunicar com os acessórios e seus serviços para realizar uma ação.

### <a name="configuration"></a>Configuração

O diagrama a seguir mostra a hierarquia básica da configuração de Acessórios do HomeKit:

![](introduction-to-ios8-images/image1.png "Este diagrama mostra a hierarquia básica da configuração de Acessórios do HomeKit")
 
Para se familiarizar com o HomeKit, os desenvolvedores precisam garantir que seu perfil de provisionamento tem o serviço HomeKit selecionado. Apple também tem fornecido aos desenvolvedores um suplemento de simulador HomeKit para Xcode. Isso pode ser encontrado na [Central de desenvolvedores da Apple](https://developer.apple.com/downloads/index.action), em `Hardware IO Tools for Xcode`. 

Para obter mais informações, consulte nosso [HomeKit](~/ios/platform/homekit.md) guia.

## <a name="healthkit"></a>HealthKit

HealthKit é uma estrutura introduzida no iOS 8 que fornece um repositório de dados centralizado, coordenado e seguro para informações relacionadas à integridade. O sistema operacional garante a privacidade e segurança de informações de saúde e, com o aplicativo de integridade, um painel para o usuário. Com a permissão do usuário, os aplicativos podem ler e gravar uma ampla variedade de informações de integridade.

Para obter mais informações sobre como usar isso em seu aplicativo xamarin. IOS, consulte a [Introdução ao HealthKit](~/ios/platform/healthkit.md) guia.

## <a name="extending-iphone-functionality"></a>Estendendo a funcionalidade do iPhone
Com o iOS8, os desenvolvedores que estão sendo recebem muito mais controle sobre quem pode usar seu aplicativo, maior capacidade e para abrir mais comunicação entre aplicativos de terceiros. Recursos como o seletor de documento e extensões de aplicativo abrem um mundo de possibilidades para como os aplicativos podem ser usados no ecossistema da Apple.

### <a name="app-extensions"></a>Extensões de aplicativo
Extensões de aplicativo, simplificando bastante, são uma maneira para aplicativos de terceiros para se comunicar entre si. Para manter altos padrões de segurança e a ser defendido a integridade dos aplicativos na área restrita, essa comunicação não acontece diretamente entre aplicativos. Em vez disso, ela é executada por um *extensão* no meio.

A primeira etapa na criação de uma extensão de aplicativo é definir o ponto de extensão correto — isso é importante para garantir o comportamento e a disponibilidade das APIs corretas. Para criar uma extensão de aplicativo no Visual Studio para Mac, adicioná-lo a um aplicativo existente, adicionando um novo projeto à sua solução.

No **novo projeto** caixa de diálogo navegar para **C#**  >  **iOS** > **API unificada**  >  **Extensões**, conforme ilustrado na captura de tela abaixo:

![](introduction-to-ios8-images/image2.png "Criando uma nova extensão")
 
A caixa de diálogo Novo projeto fornece sete novos modelos de projeto para a criação de extensões de aplicativo e são discutidas abaixo. Observe que muitas das extensões estão relacionadas a outros novas APIs no iOS, como o seletor de documento:

- **Ação** – Isso permite aos desenvolvedores criar botões de ação personalizada exclusivo que permite que os usuários executa certas tarefas
- **Teclado personalizado** – Isso permite aos desenvolvedores adicionar ao intervalo de criado no Apple teclados, adicionando seu próprios um personalizado. O teclado popular, Swype usa isso para trazer o teclado para iOS.
- **Seletor de documentos** – isso contém um controlador de exibição do documento seletor que permite aos usuários acessar arquivos fora da área de segurança do aplicativo.
- **Provedor de arquivo do seletor de documento** – isso fornece armazenamento seguro para arquivos usando o seletor de documento.
- **Edição de fotos** – esta expande os filtros e as ferramentas já fornecidas pela Apple com o aplicativo de fotos para dar aos usuários mais controle e mais opções ao editar suas fotos de edição.
- **Hoje em dia** – isso dá aos aplicativos a capacidade de exibir widgets na seção de hoje do Centro de notificações.

Para obter mais informações sobre como usar extensões de aplicativo no Xamarin, consulte o [Introdução às extensões de aplicativo](~/ios/platform/extensions.md) guia.

### <a name="touch-id"></a>ID de Toque

ID de toque foi introduzido no iOS 7 como um meio de autenticação do usuário – semelhante a uma senha. No entanto, ele era limitado para desbloquear o dispositivo, usando o App Store, usando o iTunes e autenticar o conjunto de chaves do iCloud apenas 

Agora há duas maneiras de usar o Touch ID como um mecanismo de autenticação em aplicativos do iOS 8 usando a API de autenticação Local. Atualmente, não é possível usar a autenticação Local para autenticar remotamente.

Em primeiro lugar, ele ajuda os serviços existentes do conjunto de chaves com o uso de novo conjunto de chaves Access Control Lists (ACLs). Dados do conjunto de chaves podem ser desbloqueados com a autenticação bem-sucedida de uma impressão digital de usuários.

Em segundo lugar, LocalAuthentication fornece dois métodos para autenticar seu aplicativo localmente. Os desenvolvedores devem utilizar `CanEvaluatePolicy` para determinar se o dispositivo é capaz de aceitar o Touch ID e, em seguida, `EvaluatePolicy` para iniciar a operação de autenticação.

Para obter mais informações sobre a ID de toque e saber como integrá-las em um aplicativo xamarin. IOS, consulte a [Introdução ao TouchID](~/ios/platform/touchid.md) guias.

### <a name="document-picker"></a>Seletor de documentos

Funciona de seletor de documento com uma unidade de iCloud de usuários para permitir que o usuário abrir arquivos que foram criados em um aplicativo diferente, importam e manipulá-los e exportação-los volta novamente. Isso cria um fluxo de trabalho intuitivo e, portanto, uma experiência muito melhor, para que os usuários. Sincronizando iCloud leva isso um passo além, todas as alterações feitas em um aplicativo também irá refletir consistentemente em todos os seus dispositivos.

Para saber mais sobre o seletor de documento mais detalhadamente e saiba como integrá-lo a um aplicativo xamarin. IOS, consulte a [Introdução ao seletor de documento o](~/ios/platform/document-picker.md) guia.

### <a name="handoff"></a>Handoff

Entrega, que é parte do recurso de continuidade dos maior, leva a uma etapa em direção a integração dos X e iOS. Isso inclui o AirDrop de plataforma cruzada, a capacidade de receber chamadas do iPhone, SMS no iPad e Mac e aprimoramentos no compartilhamento do seu iPhone.

Entrega funciona com o iOS 8 e Yosemite e requer uma conta iCloud estar conectado a todos os dispositivos diferentes que você deseja usar. Ele deve funcionar com os aplicativos da Apple mais pré-instalado, incluindo o Safari, iWork, mapas, calendários e contatos.

Para obter mais informações, consulte nosso [entrega](~/ios/platform/handoff.md) guia.

## <a name="unified-storyboards"></a>Storyboards unificadas
iOS 8 inclui um novo mais simples de usar o mecanismo para criar a interface do usuário — o storyboard unificado. Com um storyboard único para abranger todos os tamanhos de tela de um hardware diferente, rápidas e responsivos exibições podem ser criadas em um estilo "design uma vez, usam muitas" true.

Antes de iOS8, os desenvolvedores usavam `UIInterfaceOrientation` para distinguir entre os modos retrato e paisagem, e `UIInterfaceIdiom` para distinguir entre dispositivos iOS. Em iOS8 não é necessário criar storyboards separadas para dispositivos iPhone e iPad, orientação e o dispositivo são determinados por meio *Classes de tamanho*.

Cada dispositivo é definido por uma classe de tamanho, na vertical e o eixo horizontal, e há dois tipos de classes de tamanho no iOS 8:

- **Regular** -isso é para um tamanho de tela grande (como um iPad) ou um gadget que dá a impressão de um tamanho grande (por exemplo, uma UIScrollView
- **Compact** -isso é para dispositivos menores (como o iPhone). Esse tamanho leva em consideração a orientação do dispositivo.

Se os dois conceitos são usados juntos, o resultado é uma grade de 2 x 2 que define os diferentes tamanhos de possíveis que podem ser usados em ambos os as orientações diferentes, como visto no diagrama a seguir:

![](introduction-to-ios8-images/image3.png "Um diagrama que representa a grade de 2 x 2 que define os diferentes tamanhos de possíveis que podem ser usados em ambas as orientações a diferentes")
 
Para obter mais informações sobre classes de tamanho, consulte o [Introdução ao Storyboards unificados](~/ios/user-interface/storyboards/unified-storyboards.md).

## <a name="photo-kit"></a>Kit de foto
Kit de fotos é uma nova estrutura que permite aos aplicativos consultar a biblioteca de imagens do sistema e criar interfaces do usuário personalizadas para exibir e modificar seu conteúdo. Ele inclui um número de classes que representam os ativos de vídeo e imagem, bem como coleções de ativos, como pastas e álbuns.

Para obter mais informações, consulte nosso [PhotoKit](~/ios/platform/photokit.md) guia.

## <a name="games"></a>Jogos

<a name="scenekit" />

### <a name="scene-kit"></a>Kit de cena

Kit de cena é uma API que simplifica o trabalho com gráficos 3D de grafo de cena 3D. Ele foi introduzido pela primeira vez nos X 10.8 e agora chegou ao iOS 8. Com o Kit de cena Criando visualizações 3D imersivas e casuais jogos 3D não requer experiência em OpenGL. Criando nos conceitos comuns de grafo de cena, Scene Kit abstrai as complexidades de OpenGL e OpenGL ES, tornando muito mais fácil adicionar 3D conteúdo a um aplicativo. No entanto, se você for um especialista do OpenGL, Scene Kit tem excelente suporte para juntar na diretamente com o OpenGL também. Ele também inclui vários recursos que complementam os gráficos 3D, como física e se integra muito bem com várias outras estruturas de Apple, como animação principal, a imagem principal e Sprite Kit.

Para obter mais informações, consulte nosso [SceneKit](~/ios/platform/gaming/scenekit.md) documentação.

<a name="spritekit" />

### <a name="sprite-kit"></a>Sprite Kit

Sprite Kit, a estrutura de jogos 2D da Apple, tem alguns novos recursos interessantes no iOS 8 e nos X Yosemite. Isso inclui a integração com Scene Kit, suporte de sombreador, iluminação, sombras, restrições, geração de mapa normal e aprimoramentos de física. Em particular, os novos recursos de física tornam muito fácil adicionar efeitos realistas para um jogo.

Para obter mais informações, consulte nosso [SpriteKit](~/ios/platform/gaming/spritekit.md) documentação.

## <a name="other-changes"></a>Outras alterações
Bem como as principais alterações no iOS 8 descritos acima, a Apple atualizou adicionalmente muitas estruturas existentes. Eles são detalhados abaixo:

- **[Imagem de núcleo](https://developer.apple.com/library/prerelease/ios/documentation/GraphicsImaging/Reference/CoreImagingRef/index.html#//apple_ref/doc/uid/TP40001171)**  – Apple expandiu com sua estrutura de processamento de imagem com a adição de um suporte melhor para a detecção de regiões retangulares, e os códigos QR dentro de imagens. Mike Bluestein explora isso em seu blog intitulada post [detecção de imagem no iOS 8](https://blog.xamarin.com/image-detection-in-ios-8/)

## <a name="deprecated-apis"></a>APIs obsoletas
Com todas as melhorias feitas no iOS 8, uma série de APIs tê preterido. Alguns deles são detalhadas abaixo.

- **[UIApplication](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplication_Class/index.html#//apple_ref/occ/cl/UIApplication)**  – os métodos e propriedades usadas para registrar notificações remotas tenham sido substituídos. Esses são registerForRemoteNotificationTypes e enabledRemoteNotificationTypes.
- **[UIViewController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/cl/UIViewController)**  – características e as classes de tamanho substituíram os métodos e propriedades usados para descrever a orientação de interface. Consulte a [Introdução ao Storyboards unificados](~/ios/user-interface/storyboards/unified-storyboards.md) para obter mais informações sobre como usá-los.

- **[UISearchDisplayController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UISearchDisplayController_Class/index.html#//apple_ref/occ/cl/UISearchDisplayController)**  – isso foi substituído pelo UISearchController em iOS8.

## <a name="summary"></a>Resumo
Neste artigo vimos alguns dos novos recursos introduzidos pela Apple no iOS 8.



## <a name="related-links"></a>Links relacionados

- [UIKitEnhancements (amostra)](https://developer.xamarin.com/samples/monotouch/ios8/UIKitEnhancements)
- [Introdução às extensões de aplicativo](~/ios/platform/extensions.md)
- [Introdução ao CloudKit](~/ios/data-cloud/intro-to-cloudkit.md)
- [Introdução ao seletor de documento](~/ios/platform/document-picker.md)
- [Introdução ao HealthKit](~/ios/platform/healthkit.md)
- [Introdução aos controles de câmera manuais](~/ios/user-interface/controls/intro-to-manual-camera-controls.md)
- [Introdução ao TouchID](~/ios/platform/touchid.md)
- [Introdução ao Storyboards unificadas](~/ios/user-interface/storyboards/unified-storyboards.md)
