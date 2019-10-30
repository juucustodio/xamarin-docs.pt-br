---
title: Trabalhando com listas de propriedades no Xamarin. iOS
description: Este documento apresenta o editor de lista de Propriedades (. plist) gráfico e avançado da Visual Studio para Mac para trabalhar com o info. plist e o intitulors. plist. Ele ilustra a definição de ícones e a inicialização de imagens para aplicativos iOS de dentro Visual Studio para Mac.
ms.prod: xamarin
ms.assetid: 5E687043-0443-377C-9A12-9C5A05958646
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 2060e0786b5401b44217318b647dfa7412f934f4
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73009875"
---
# <a name="working-with-property-lists-in-xamarinios"></a>Trabalhando com listas de propriedades no Xamarin. iOS

_Este documento apresenta o editor de lista de Propriedades (. plist) gráfico e avançado da Visual Studio para Mac para trabalhar com o info. plist e o intitulors. plist. Ele ilustra a definição de ícones e a inicialização de imagens para aplicativos iOS de dentro Visual Studio para Mac._

Visual Studio para Mac apresenta um editor gráfico. plist que facilita a edição de propriedades e recursos do aplicativo. O Visual Studio para Mac tem dois. plist-`Info.plist` para editar as propriedades e os ícones do aplicativo e `Entitlements.plist` para gerenciar os recursos do aplicativo. Este guia apresenta o info. plist e fornece uma visão geral de como trabalhar com eles no Visual Studio para Mac. Para obter informações sobre os direitos. plist, consulte o guia [trabalhando com direitos](~/ios/deploy-test/provisioning/entitlements.md) .

## <a name="infoplist"></a>Info.plist

A lista de propriedades de informações (`Info.plist`) é um arquivo iOS necessário que fornece informações sobre a configuração do aplicativo para o sistema. O editor de `Info.plist` personalizado do Visual Studio para Mac apresenta três painéis controlados por guias na parte inferior esquerda da janela do editor:

 [![](property-lists-images/tabs.png "The Info.plist editor tabs at the bottom left of the editor window")](property-lists-images/tabs.png#lightbox)

Cada painel controla propriedades diferentes, conforme descrito abaixo:

- **Painel de aplicativo** – uma interface gráfica para definir propriedades de aplicativo comuns, bem como ícones e imagens de inicialização; Especifique os modos de integração e de plano de fundo dos mapas.
- **Painel avançado** – o painel avançado é o local para especificar tipos de documento, UTIs e tipos de URL com suporte.
- **Painel de origem** – o painel de origem controla Propriedades menos comuns, bem como propriedades personalizadas para o aplicativo.

As próximas três seções investigam os recursos de cada painel mais detalhadamente.

## <a name="application-panel"></a>Painel do aplicativo

Visual Studio para Mac apresenta uma interface gráfica para editar entradas de `Info.plist` comuns de um aplicativo:

1. Propriedades do aplicativo
1. Tipos de dispositivo com suporte
1. Orientações de suporte para cada tipo de dispositivo
1. Estilo e cor da barra de status
1. Telas de ícones e de inicialização
1. Modos de mapas e de segundo plano

Eles são descritos mais detalhadamente nas próximas seções.

 <a name="iOS_Application_Target" />

### <a name="ios-application-target"></a>Destino do aplicativo iOS

Esta seção contém informações importantes que descrevem seu aplicativo.
O **identificador** armazenado aqui deve corresponder ao identificador de pacote que é inserido no iTunes Connect (para aplicativos da App Store) e também na lista de IDs de aplicativo do portal de provisionamento do IOS e nos certificados de desenvolvimento e distribuição.

 [![](property-lists-images/image24.png "iOS Application Target")](property-lists-images/image24.png#lightbox)

### <a name="device-deployment"></a>Implantação de dispositivo

 [![](property-lists-images/deployment.png "Device Deployment")](property-lists-images/deployment.png#lightbox)

As seções informações de **implantação** de dispositivo são exibidas seletivamente, dependendo da seleção no menu suspenso **dispositivos** na seção **destino do aplicativo** acima. A lista suspensa **interface principal** é definida como **MainStoryboard** em aplicativos controlados por Storyboard. Se a interface do usuário for inteiramente gravada no código, isso poderá ser deixado em branco.

### <a name="supported-device-orientations"></a>Orientações de dispositivo com suporte

 As **orientações de dispositivo com suporte** controlam como o aplicativo responde à rotação do dispositivo. É muito comum que os aplicativos iPhone/iPad ofereçam suporte apenas a **retrato**, ou tudo, menos de **cabeça para baixo**. Geralmente, todos os aplicativos do iPad, exceto jogos, devem dar suporte a todas as orientações.

### <a name="status-bar-styles"></a>Estilos da barra de status

A seção de **estilos da barra de status** é uma interface gráfica para editar o `UIStatusBarStyle`de um aplicativo:

 [![](property-lists-images/status.png "Status Bar Styles")](property-lists-images/status.png#lightbox)

 <a name="Icons" />

### <a name="icons-launch-images-and-itunes-artwork"></a>Ícones, imagens de inicialização e trabalho artístico do iTunes

Informações sobre como usar ícones, imagens e arte em seu arquivo info. plist podem ser encontradas no guia [trabalhando com imagens](~/ios/app-fundamentals/images-icons/index.md) .

### <a name="maps-integration-and-background-modes"></a>Modos de integração e de segundo plano do Maps

O `Info.plist` contém seções especiais para especificar os modos de integração e de plano de fundo dos mapas. Escolher as opções às quais você deseja dar suporte adicionará as propriedades necessárias ao seu aplicativo para você.

 [![](property-lists-images/maps.png "Maps Integration")](property-lists-images/maps.png#lightbox)

Para obter mais informações sobre como trabalhar com mapas, consulte o guia de mapas do Xamarin [Ios](~/ios/user-interface/controls/ios-maps/index.md) .

 [![](property-lists-images/bging.png "Background Modes")](property-lists-images/bging.png#lightbox)

Para obter mais informações sobre os modos de segundo plano, consulte a [formação em segundo plano](~/ios/app-fundamentals/backgrounding/introduction-to-backgrounding-in-ios.md) do Xamarin no guia do Ios.

## <a name="advanced-panel"></a>Painel avançado

O painel avançado controla os tipos de documento e esquemas de URL aos quais o aplicativo dá suporte.

 [![](property-lists-images/image34.png "Advanced Panel")](property-lists-images/image34.png#lightbox)

 <a name="Document_Types" />

## <a name="document-types"></a>Tipos de documento

Para aplicativos que dão suporte à abertura de tipos específicos de arquivos, o iOS fornece a chave de `CFBundleDocumentTypes`. Se quisermos que nosso aplicativo dê suporte a determinados tipos de arquivo conhecidos-por exemplo, PDFs, adicionaremos o valor de PDF à chave. Esta seção fornece uma maneira conveniente de inserir os dados que serão armazenados na chave de `CFBundleDocumentTypes` no arquivo de `Info.plist`.

Consulte a documentação sobre como [registrar os tipos de arquivo aos quais seu aplicativo dá suporte](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html) para obter detalhes sobre como configurar esses valores.

## <a name="utis"></a>UTIs

Às vezes, um aplicativo precisa dar suporte à abertura de um tipo de arquivo personalizado. Por exemplo, talvez queiramos abrir arquivos de imagem com uma extensão *. XAM*personalizada. Para especificar um tipo de arquivo personalizado, criaremos um identificador de tipo UTI-universal personalizado-usando a chave `UIExportedTypeDeclarations`. A captura de tela abaixo ilustra como criar um UTI personalizado para a extensão. XAM:

 [![](property-lists-images/uti.png "UTIs Editor")](property-lists-images/uti.png#lightbox)

Assim como o tipo exportado UTIs especifica UTIs personalizado específico para seu aplicativo, o *tipo importado UTIs* (chave de `UIImportedTypeDeclarations`) especificam tipos personalizados com suporte, mas não pertencentes ao seu aplicativo.

Para obter mais informações sobre como usar o UTIs personalizado, consulte [os tipos de arquivo de registro da Apple que seu aplicativo dá suporte](https://developer.apple.com/library/ios/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_declare/understand_utis_declare.html#//apple_ref/doc/uid/TP40001319-CH204-SW1) ao guia.

## <a name="custom-urls"></a>URLs personalizadas

Um nome de esquema de URL (também chamado de protocolo) é a primeira parte da URL. Por exemplo, `http://` e `https://` são esquemas de URL comuns. Você tem a opção de criar um esquema de URL personalizado para seu aplicativo. Os esquemas de URL personalizados são usados para se comunicar e enviar dados de volta e para outro com outros aplicativos. A captura de tela a seguir ilustra a criação de um novo esquema de URL personalizado chamado `monkeys://`:

 [![](property-lists-images/url.png "Custom URLs")](property-lists-images/url.png#lightbox)

Para obter mais informações sobre como implementar esquemas de URL personalizados, consulte a [seção implementando esquemas de URL personalizados da Apple deste guia](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedAppTricks/AdvancedAppTricks.html)

## <a name="source-panel"></a>Painel de origem

A guia **origem** do arquivo de `Info.plist` permite que valores personalizados sejam adicionados ou editados. Visual Studio para Mac fornece uma lista das propriedades mais comuns:

 [![](property-lists-images/image31.png "Adding a new property from a dropdown")](property-lists-images/image31.png#lightbox)

Para propriedades conhecidas Visual Studio para Mac será uma lista de valores válidos, conforme ilustrado pela seguinte captura de tela:

 [![](property-lists-images/image32.png "Select a value from a know value list")](property-lists-images/image32.png#lightbox)

Visual Studio para Mac também detecta o tipo de propriedade, conforme mostrado:

 [![](property-lists-images/image33.png "The available property types")](property-lists-images/image33.png#lightbox)

Examine os links de [recursos relacionados ao aplicativo](https://developer.apple.com/library/ios/#DOCUMENTATION/iPhone/Conceptual/iPhoneOSProgrammingGuide/App-RelatedResources/App-RelatedResources.html) da Apple para obter informações adicionais sobre propriedades opcionais.

 <a name="Entitlements" />

## <a name="summary"></a>Resumo

Este artigo demonstrou como usar os editores gráficos e avançados do. plist para editar configurações de aplicativo comuns, bem como para especificar ícones e imagens de inicialização. Ele também introduziu o `Entitlements.plist` para adicionar e gerenciar recursos de aplicativo.

## <a name="related-links"></a>Links relacionados

- [CONDUTOR](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide)
- [Recursos relacionados ao aplicativo](https://developer.apple.com/library/ios/#DOCUMENTATION/iPhone/Conceptual/iPhoneOSProgrammingGuide/App-RelatedResources/App-RelatedResources.html)
- [Registrando os tipos de arquivo com suporte no seu aplicativo](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html)
- [Implementando esquemas de URL personalizados](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedAppTricks/AdvancedAppTricks.html)
- [Referência de formato do catálogo de ativos](https://developer.apple.com/library/archive/documentation/Xcode/Reference/xcode_ref-Asset_Catalog_Format/index.html#//apple_ref/doc/uid/TP40015170-CH18-SW1)
