---
title: "Introdução ao iOS 8"
description: "Com o iOS 8, Apple forneceu uma infinidade de novas estruturas e APIs para excite e encantem e desenvolvedores. Neste guia introduzir essas novas APIs e ver como o iOS 8 pode se beneficiar desenvolvedores e usuários."
ms.topic: article
ms.prod: xamarin
ms.assetid: 33AD66C0-3743-49FE-9DCE-88ED3A16BA63
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/14/2017
ms.openlocfilehash: 8a4fabd5cc63434950f4646336b06676f6eb915b
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="introduction-to-ios-8"></a>Introdução ao iOS 8

_Com o iOS 8, Apple forneceu uma infinidade de novas estruturas e APIs para excite e encantem e desenvolvedores. Neste guia introduzir essas novas APIs e ver como o iOS 8 pode se beneficiar desenvolvedores e usuários._

iOS 7 visualmente alterado a interface de usuário do iOS inteira de quais usuários e desenvolvedores tinham conhecem, diretamente do iPhone primeiro sistema operacional. IOS 8 continua com isso, fornecendo muitas estruturas para desenvolvedores, que permite aos usuários controlar quase todos os aspectos de sua vida reta no seu iPhone. Por exemplo integridade e adequação podem ser analisados com *HealthKit*, senhas são obsoleta com o uso de autenticação biométrica *LocalAuthentication*, *extensões de aplicativo*abrir um canal de comunicação entre os aplicativos de terceiros 3ª, e *HomeKit* permite que a capacidade de transformar casa em uma página inicial do futuro. 

Se iOS 7 sobre satisfação dos usuários, o iOS 8 enfoca satisfação dos desenvolvedores com uma grande variedade dessas novas ferramentas saborosos. 

Este guia apresenta as novas APIs para desenvolvedores do xamarin.  

Também há algumas APIs que foram substituídos no iOS 8, que são detalhadas no final deste documento.

## <a name="requirements"></a>Requisitos

A seguir é necessários para criar aplicativos iOS 8 no Visual Studio para Mac:

- **Xcode 7 e iOS 8 ou mais recente** – Xcode e APIs do iOS mais recente da Apple precisa ser instalado e configurado no computador do desenvolvedor.
- **O Visual Studio para Mac** – a versão mais recente do Visual Studio para Mac deve ser instalada e configurada no dispositivo do usuário.
- **iOS 8 dispositivo ou simulador** – um dispositivo iOS executando a versão mais recente do iOS 8 para teste.

## <a name="home-and-leisure"></a>Página inicial e quiser

iOS 8 ajudou para introduzir firmemente Apple e o dispositivo iOS diretamente no centro da sua casa com o uso de HomeKit e HealthKit. Nesta seção, examinaremos como essas novas estruturas de trabalho e como eles podem ser integrados em seu aplicativo xamarin.

## <a name="homekit"></a>HomeKit

Controlar seus dispositivos no seu iPhone não é um novo aplicativo de tecnologia. Muitos produtos home conectado podem ser controlados por meio de um aplicativo iOS. No entanto HomeKit agora leva isso um passo adiante, promovendo um protocolo comum para dispositivos de automação inicial e ao disponibilizar uma API pública para determinados fabricantes, como iHome, Philips e Honeywell. Para o usuário, isso significa que possam controlar quase todos os aspectos de sua home diretamente de dentro de um aplicativo. É irrelevante para que eles saibam que eles estão usando uma lâmpada Philips matiz ou um alarme de aninhamento. Os usuários também podem encadear vários processos inicial inteligentes juntas em "Nos bastidores".

Com HomeKit, aplicativos de terceiros e Siri podem descobrir Acessórios e adicioná-los ao seu banco de dados de configuração de base pessoal, editar e agir sobre dados e se comunicar com seus serviços para executar uma ação e Acessórios.

### <a name="configuration"></a>Configuração

O diagrama a seguir mostra a hierarquia básica da configuração de Acessórios HomeKit:

![](introduction-to-ios8-images/image1.png "Este diagrama mostra a hierarquia básica da configuração de Acessórios HomeKit")
 
Para começar a usar HomeKit, os desenvolvedores precisam Certifique-se de que o seu perfil de provisionamento tem o serviço HomeKit selecionado. Apple também fornece aos desenvolvedores um suplemento de simulador HomeKit para Xcode. Isso pode ser encontrado na [central do desenvolvedor Apple](https://developer.apple.com/downloads/index.action), em `Hardware IO Tools for Xcode`. 

Para obter mais informações, consulte nosso [HomeKit](~/ios/platform/homekit.md) guia.

## <a name="healthkit"></a>HealthKit

HealthKit é uma estrutura introduzida no iOS 8 que fornece um repositório de dados seguro, centralizado e coordenado para informações relacionadas à integridade. O sistema operacional garante a privacidade e segurança de informações de integridade e, com o aplicativo de integridade, um painel para o usuário. Com a permissão do usuário, os aplicativos podem ler e gravar uma ampla variedade de informações de integridade.

Para obter mais informações sobre como usar isso em seu aplicativo xamarin, consulte o [Introdução ao HealthKit](~/ios/platform/healthkit.md) guia.

## <a name="extending-iphone-functionality"></a>Estendendo a funcionalidade do iPhone
Com iOS8, os desenvolvedores que estão sendo recebem muito mais controle sobre quem pode usar seu aplicativo, maior capacidade e para abrir mais comunicação entre aplicativos de terceiros. Recursos como o seletor de documento e extensões de aplicativo abrem um mundo de possibilidades de como os aplicativos podem ser usados no ecossistema da Apple.

### <a name="app-extensions"></a>Extensões de aplicativo
Extensões de aplicativo, para simplificar muito, são uma maneira para aplicativos de terceiros para se comunicar uns com os outros. Para manter os padrões de alta segurança e para sustentar a integridade dos aplicativos em modo seguro, essa comunicação não acontece diretamente entre aplicativos. Em vez disso, ela é executada por um *extensão* no meio.

A primeira etapa na criação de uma extensão de aplicativo é definir o ponto de extensão correto — isso é importante garantir o comportamento e a disponibilidade das APIs corretas. Para criar uma extensão do aplicativo no Visual Studio para Mac, adicione-a um aplicativo existente adicionando um novo projeto à solução.

No **novo projeto** caixa de diálogo navegar até **c#** > **iOS** > **API unificada**  >   **Extensões**, conforme ilustrado na captura de tela abaixo:

![](introduction-to-ios8-images/image2.png "Criando uma nova extensão")
 
A caixa de diálogo Novo projeto fornece sete novos modelos de projeto para criar extensões de aplicativo e são discutidas abaixo. Observe que muitas das extensões se relacionam com outros novas APIs no iOS, como o seletor de documento:

- **Ação** – Isso permite aos desenvolvedores criar botões de ação personalizada exclusivo permitindo que os usuários executa certas tarefas
- **Teclado personalizado** – Isso permite que desenvolvedores adicionem ao intervalo de integrada Apple teclados, adicionar seus próprios personalizado. O teclado popular, Swype utiliza para trazer o teclado para iOS.
- **Seletor de documento** – contém um controlador de exibição de seletor de documento que permite que os usuários acessem os arquivos fora da área de segurança do aplicativo.
- **Provedor de seletor de arquivo de documento** – isso fornece armazenamento seguro de arquivos usando o seletor de documento.
- **Edição de foto** – esta expande os filtros e ferramentas já fornecidas pela Apple com o aplicativo de fotos para dar aos usuários mais controle e mais opções ao editar suas fotos de edição.
- **Hoje** – isso fornece aplicativos a capacidade de exibir widgets na seção atual do Centro de notificação.

Para obter mais informações sobre como usar extensões de aplicativo Xamarin, consulte o [Introdução a extensões de aplicativo](~/ios/platform/extensions.md) guia.

### <a name="touch-id"></a>ID de toque

ID de toque foi introduzido no iOS 7 como um meio de autenticação do usuário — semelhante a uma senha. No entanto, era limitado para desbloquear o dispositivo, usando o armazenamento de aplicativo, usando iTunes e autenticar o conjunto de chaves do iCloud somente 

Agora, há duas maneiras de usar Touch ID como um mecanismo de autenticação em aplicativos do iOS 8 usando a API de autenticação Local. No momento não é possível usar a autenticação Local para autenticar remotamente.

Em primeiro lugar, ele ajuda os serviços de conjunto de chaves existentes com o uso de novo conjunto de chaves controle listas de acesso (ACLs). Dados do conjunto de chaves podem ser desbloqueados com a autenticação bem-sucedida de uma impressão digital de usuários.

Em segundo lugar, LocalAuthentication fornece dois métodos para autenticar seu aplicativo localmente. Os desenvolvedores devem usar `CanEvaluatePolicy` para determinar se o dispositivo é capaz de aceitar o Touch ID e, em seguida, `EvaluatePolicy` para iniciar a operação de autenticação.

Para obter mais informações sobre a ID de toque e para saber como integrá-lo em um aplicativo xamarin, consulte o [introdução para Touch ID](~/ios/platform/touchid.md) guias.

### <a name="document-picker"></a>Seletor de documento

Documento seletor funciona com uma unidade de iCloud de usuários para permitir que o usuário abrir arquivos que foram criados em um aplicativo diferente, importam e manipulá-los e exportação-los novamente. Isso cria um fluxo de trabalho intuitivo e, portanto, uma melhor experiência, para que os usuários. Sincronizando o iCloud vai mais — todas as alterações feitas em um aplicativo também irá refletir consistentemente em todos os seus dispositivos.

Para saber mais sobre o seletor de documento mais detalhadamente e para saber como integrá-lo em um aplicativo xamarin, consulte o [introdução para o seletor de documento](~/ios/platform/document-picker.md) guia.

### <a name="handoff"></a>Entrega

Entrega, que é parte do recurso de continuidade maior, entra em uma etapa adicional para integrar OS X e iOS. Isso inclui o AirDrop de plataforma cruzada, a capacidade de receber chamadas do iPhone, SMS sobre melhorias no compartilhamento de Internet por meio do iPhone, iPad e Mac.

Entrega funciona com iOS 8 e Yosemite e requer uma conta do iCloud estar conectado a todos os dispositivos diferentes que deseja usar. Ele deve funcionar com aplicativos pré-instalados mais de Apple, inclusive Safari, iWork, mapas, calendários e contatos.

Para obter mais informações, consulte nosso [entrega](~/ios/platform/handoff.md) guia.

## <a name="unified-storyboards"></a>Storyboards unificadas
iOS 8 inclui um novo mais simples usar o mecanismo para criar a interface do usuário — o storyboard unificado. Com um único storyboard para abranger todos os tamanhos de tela de hardware diferente, rápidos quanto os modos de exibição podem ser criados em um estilo true "Projete uma vez, use muitos".

Antes de iOS8, os desenvolvedores usavam `UIInterfaceOrientation` para distinguir entre os modos retrato e paisagem, e `UIInterfaceIdiom` para distinguir entre dispositivos iOS. Em iOS8 não é necessário criar storyboards separadas para os dispositivos iPhone e iPad, orientação e dispositivo são determinados usando *Classes de tamanho*.

Cada dispositivo é definido por uma classe de tamanho, na vertical e o eixo horizontal, e há dois tipos de classes de tamanho no iOS 8:

- **Regular** -isso é para um tamanho de tela grande (como um iPad) ou um gadget que lhe dá a impressão de um tamanho grande (como uma UIScrollView
- **Compact** -isso é para dispositivos menores (como o iPhone). Esse tamanho leva em conta a orientação do dispositivo.

Se os dois conceitos são usados juntos, o resultado é uma grade de 2 x 2 que define os tamanhos diferentes possíveis que podem ser usados em ambos os as orientações diferentes, como visto no diagrama a seguir:

![](introduction-to-ios8-images/image3.png "Um diagrama representando a grade de 2 x 2 define os tamanhos diferentes possíveis que podem ser usados em ambas as orientações a diferentes")
 
Para obter mais informações sobre classes de tamanho, consulte o [Introdução ao Unified Storyboards](~/ios/user-interface/storyboards/unified-storyboards.md).

## <a name="photo-kit"></a>Kit de fotos
Kit de fotografia é uma nova estrutura que permite que aplicativos consultar a biblioteca de imagens do sistema e criar interfaces do usuário personalizadas para exibir e modificar seu conteúdo. Ele inclui um número de classes que representam os ativos de vídeo e imagem, bem como coleções de ativos como álbuns e pastas.

Para obter mais informações, consulte nosso [PhotoKit](~/ios/platform/photokit.md) guia.

## <a name="games"></a>Jogos

<a name="scenekit" />

### <a name="scene-kit"></a>Kit de cena

Kit de cena é um API que simplifica o trabalho com gráficos 3D do gráfico de cena 3D. Ele foi introduzido nos X 10.8 e agora chegou iOS 8. Com o Kit de cena Criando visualizações 3D envolventes e jogos casuais de 3D não requer experiência em OpenGL. Aproveitando os conceitos de gráfico de cena comuns, Kit de cena abstrai a complexidade de OpenGL e OpenGL ES, facilitando muito 3D de adicionar conteúdo a um aplicativo. No entanto, se você for um especialista OpenGL, o Kit de cena tem excelente suporte para associação diretamente com OpenGL também. Ele também inclui vários recursos que complementam os gráficos 3D, como física e se integra muito bem com várias outras estruturas de Apple, como principais de animação, imagem principal e entidade gráfica Kit.

Para obter mais informações, consulte nosso [SceneKit](~/ios/platform/gaming/scenekit.md) documentação.

<a name="spritekit" />

### <a name="sprite-kit"></a>Kit de entidade gráfica

Kit de entidade gráfica, a estrutura de jogos 2D da Apple, tem alguns novos recursos interessantes do iOS 8 e OS X Yosemite. Isso inclui a integração com o Kit de cena, suporte de sombreador, iluminação, sombras, restrições, geração de mapa normal e aprimoramentos física. Em particular, os novos recursos de física facilitam muito a adicionar efeitos realistas para um jogo.

Para obter mais informações, consulte nosso [SpriteKit](~/ios/platform/gaming/spritekit.md) documentação.

## <a name="other-changes"></a>Outras alterações
Bem como as principais alterações na iOS 8 descritos acima, a Apple atualizou adicionalmente muitas estruturas existentes. Eles são detalhados abaixo:

- **[Imagem de núcleo](https://developer.apple.com/library/prerelease/ios/documentation/GraphicsImaging/Reference/CoreImagingRef/index.html#//apple_ref/doc/uid/TP40001171)**  – Apple expandiu com sua estrutura de processamento de imagem adicionando um melhor suporte para a detecção de regiões retangulares e códigos QR dentro de imagens. Mike Bluestein explora isso em seu blog postagem intitulada [detecção de imagem no iOS 8](http://blog.xamarin.com/image-detection-in-ios-8/)

## <a name="deprecated-apis"></a>APIs obsoletas
Com todas as melhorias feitas no iOS 8, um número de APIs tenha substituídos. Algumas delas são detalhadas abaixo.

- **[UIApplication](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplication_Class/index.html#//apple_ref/occ/cl/UIApplication)**  – os métodos e propriedades usadas para registrar notificações remotas tenham sido substituídos. Estas são registerForRemoteNotificationTypes e enabledRemoteNotificationTypes.
- **[UIViewController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/cl/UIViewController)**  – características e classes de tamanho substituíram os métodos e propriedades usados para descrever a orientação de interface. Consulte o [Introdução ao Unified Storyboards](~/ios/user-interface/storyboards/unified-storyboards.md) para obter mais informações sobre como usá-los.

- **[UISearchDisplayController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UISearchDisplayController_Class/index.html#//apple_ref/occ/cl/UISearchDisplayController)**  – isso foi substituído por UISearchController iOS8.

## <a name="summary"></a>Resumo
Neste artigo vimos alguns dos novos recursos introduzidos pela Apple no iOS 8.



## <a name="related-links"></a>Links relacionados

- [UIKitEnhancements (exemplo)](https://developer.xamarin.com/samples/monotouch/ios8/UIKitEnhancements)
- [Introdução a extensões de aplicativo](~/ios/platform/extensions.md)
- [Introdução ao CloudKit](~/ios/data-cloud/intro-to-cloudkit.md)
- [Introdução ao seletor de documento](~/ios/platform/document-picker.md)
- [Introdução ao HealthKit](~/ios/platform/healthkit.md)
- [Introdução aos controles de câmera Manual](~/ios/user-interface/controls/intro-to-manual-camera-controls.md)
- [Introdução ao Touch ID](~/ios/platform/touchid.md)
- [Introdução ao Storyboards unificadas](~/ios/user-interface/storyboards/unified-storyboards.md)
