---
title: Trabalhando com listas de propriedades no xamarin. IOS
description: Este documento apresenta o Visual Studio para o editor de lista (. plist) de propriedade gráfica e avançados do Mac para trabalhar com info. plist e Entitlements. plist. Ele ilustra Definindo ícones e imagens de inicialização para aplicativos iOS de dentro do Visual Studio para Mac.
ms.prod: xamarin
ms.assetid: 5E687043-0443-377C-9A12-9C5A05958646
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 7056f7beb623bee32c767a3f2827efa6eb2a6136
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118793"
---
# <a name="working-with-property-lists-in-xamarinios"></a>Trabalhando com listas de propriedades no xamarin. IOS

_Este documento apresenta o Visual Studio para o editor de lista (. plist) de propriedade gráfica e avançados do Mac para trabalhar com info. plist e Entitlements. plist. Ele ilustra Definindo ícones e imagens de inicialização para aplicativos iOS de dentro do Visual Studio para Mac._

O Visual Studio para Mac apresenta um editor. plist gráfica que facilita a edição de propriedades de aplicativo e dos recursos mais fácil. O Visual Studio para Mac tem dois .plists - `Info.plist` para editar as propriedades do aplicativo e ícones, e `Entitlements.plist` para gerenciar recursos de aplicativo. Este guia apresenta o Info.plists e fornece uma visão geral de como trabalhar com eles no Visual Studio para Mac. Para obter informações sobre Entitlements. plist, consulte o [trabalhando com direitos](~/ios/deploy-test/provisioning/entitlements.md) guia.

## <a name="infoplist"></a>Info.plist

A lista de propriedades de informações ( `Info.plist`) é um arquivo de iOS necessárias que fornece informações sobre a configuração do aplicativo no sistema. Personalizada do Visual Studio para Mac `Info.plist` três painéis controlados pelas guias na parte inferior esquerdas da janela do editor de recursos do editor:

 [![](property-lists-images/tabs.png "As guias do editor de info. plist na parte inferior esquerda da janela do editor")](property-lists-images/tabs.png#lightbox)

Cada painel controles propriedades diferentes, conforme descrito a seguir:

-  **Painel do aplicativo** – uma interface gráfica para definir propriedades de aplicativo comuns, bem como ícones e inicie imagens; especificar backgrounding modos e integração de mapas.
-  **Advanced painel** -painel avançado é o lugar para especificar tipos de documento com suporte, UTIs e tipos de URL.
-  **Painel de fonte** -painel de código-fonte controla propriedades menos comuns, bem como propriedades personalizadas para o aplicativo.


As próximas três seções investigar os recursos de cada painel mais detalhadamente.

## <a name="application-panel"></a>Painel do aplicativo

O Visual Studio para Mac apresenta uma interface gráfica para edição comuns `Info.plist` entradas para um aplicativo:

1.  Propriedades do aplicativo
1.  Tipos de dispositivos com suporte
1.  Orientações de suporte para cada tipo de dispositivo
1.  Barra de estilo e a cor de status
1.  Ícones e telas de inicialização
1.  Mapas e modos de segundo plano


Eles são descritos mais detalhadamente nas próximas seções.

 <a name="iOS_Application_Target" />


### <a name="ios-application-target"></a>Destino do aplicativo iOS

Esta seção contém informações importantes que descrevem seu aplicativo.
O **identificador** armazenados aqui deve corresponder ao identificador de pacote que é inserida no iTunes Connect (para aplicativos da App Store) e também na lista de IDs de aplicativo do Portal de provisionamento do iOS e certificados de desenvolvimento e distribuição.

 [![](property-lists-images/image24.png "Destino do aplicativo iOS")](property-lists-images/image24.png#lightbox)

### <a name="device-deployment"></a>Implantação do dispositivo

 [![](property-lists-images/deployment.png "Implantação do dispositivo")](property-lists-images/deployment.png#lightbox)

O dispositivo **implantação** seções de informações são exibidas de forma seletiva, dependendo da seleção na **dispositivos** lista suspensa na **aplicativo de destino** seção acima. O **Interface principal** suspensa é definida como **MainStoryboard** em aplicativos orientados a Storyboard. Se a interface do usuário é inteiramente escrita em código, em seguida, isso pode ser deixado em branco.

### <a name="supported-device-orientations"></a>Orientações de dispositivo com suporte

 **Suporte de orientações do dispositivo** controla como o aplicativo responde à rotação do dispositivo. É muito comum para aplicativos do iPhone/iPad dar suporte a apenas **retrato**, ou tudo, mas **cabeça**. Geralmente, todos os aplicativos de iPad, exceto os jogos devem dar suporte a todas as orientações.

### <a name="status-bar-styles"></a>Estilos de barra de status

O **estilos de barra de Status** seção é uma interface gráfica para a edição de um aplicativo `UIStatusBarStyle`:

 [![](property-lists-images/status.png "Estilos de barra de status")](property-lists-images/status.png#lightbox)

 <a name="Icons" />


### <a name="icons-launch-images-and-itunes-artwork"></a>Ícones, imagens de inicialização e arte do iTunes

Informações sobre como usar os ícones, imagens e arte final em seu arquivo Info. plist podem ser encontradas na [trabalhando com imagens](~/ios/app-fundamentals/images-icons/index.md) guia.




### <a name="maps-integration-and-background-modes"></a>Integração com mapas e modos de segundo plano

O `Info.plist` contém seções especiais para especificar a integração com mapas e modos de backgrounding. Escolher as opções que você deseja dar suporte, você adicionará as propriedades necessárias para seu aplicativo para você.

 [![](property-lists-images/maps.png "Integração de mapas")](property-lists-images/maps.png#lightbox)

Para obter mais informações sobre como trabalhar com mapas, consulte o Xamarin [mapas do iOS](~/ios/user-interface/controls/ios-maps/index.md) guia.

 [![](property-lists-images/bging.png "Modos de segundo plano")](property-lists-images/bging.png#lightbox)

Para obter mais informações sobre modos de segundo plano, consulte o Xamarin [Backgrounding no iOS](~/ios/app-fundamentals/backgrounding/introduction-to-backgrounding-in-ios.md) guia.

## <a name="advanced-panel"></a>Painel avançado

O painel avançado controla os tipos de documento e os esquemas de URL que o aplicativo dá suporte.

 [![](property-lists-images/image34.png "Painel avançado")](property-lists-images/image34.png#lightbox)

 <a name="Document_Types" />


## <a name="document-types"></a>Tipos de documento

Para aplicativos que dão suporte à abertura de tipos específicos de arquivos, o iOS fornece a `CFBundleDocumentTypes` chave. Se quisermos que nosso aplicativo para dar suporte a determinados tipos de arquivo conhecidos – por exemplo, PDFs - adicionaríamos o valor PDF para a chave. Esta seção fornece uma maneira conveniente para inserir os dados que será armazenada na `CFBundleDocumentTypes` principais no `Info.plist` arquivo.

Consulte a documentação sobre [registrando o arquivo de tipos de seu aplicativo dá suporte](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html) para obter detalhes sobre como configurar esses valores.

## <a name="utis"></a>UTIs

Às vezes, um aplicativo precisa dar suporte à abertura de um tipo de arquivo personalizado. Por exemplo, podemos querer abrir arquivos de imagem com uma extensão personalizada *.xam*. Para especificar um tipo de arquivo personalizado, vamos criar um personalizado UTI - identificador de tipo Universal - usando o `UIExportedTypeDeclarations` chave. Captura de tela abaixo ilustra como criar um personalizado UTI para a extensão .xam:

 [![](property-lists-images/uti.png "UTIs do Editor")](property-lists-images/uti.png#lightbox)

UTIs do tipo exportado apenas como especificar UTIs personalizados específicos para seu aplicativo, o *importado UTIs do tipo* ( `UIImportedTypeDeclarations` chave) especificar tipos personalizados com suporte, mas não pertencentes a seu aplicativo.

Para obter mais informações sobre como usar UTIs personalizados, consulte da Apple [registrando o arquivo de tipos de seu aplicativo dá suporte](https://developer.apple.com/library/ios/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_declare/understand_utis_declare.html#//apple_ref/doc/uid/TP40001319-CH204-SW1) guia.

## <a name="custom-urls"></a>URLs personalizadas

Um nome de esquema de URL (também chamado de protocolo) é a primeira parte da URL. Por exemplo, `http://` e `https://` são comuns esquemas de URL. Você tem a opção de criação de um esquema de URL personalizado para seu aplicativo. Esquemas de URL personalizadas são usadas para se comunicar e enviar dados e para trás com outros aplicativos. Captura de tela a seguir ilustra a criação de um novo esquema de URL personalizado chamado `monkeys://`:

 [![](property-lists-images/url.png "URLs personalizadas")](property-lists-images/url.png#lightbox)



Para obter mais informações sobre como implementar esquemas de URL personalizadas, consulte da Apple [seção deste guia implementar esquemas de URL personalizada](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedAppTricks/AdvancedAppTricks.html)

## <a name="source-panel"></a>Painel de fonte

O **fonte** guia o `Info.plist` arquivo permite que os valores personalizados a ser adicionado ou editado. O Visual Studio para Mac fornece uma lista das propriedades mais comuns:

 [![](property-lists-images/image31.png "Adicionando uma nova propriedade de uma lista suspensa")](property-lists-images/image31.png#lightbox)

Para propriedades conhecidas do Visual Studio para Mac será uma lista de valores válidos, conforme ilustrado pela captura de tela a seguir:

 [![](property-lists-images/image32.png "Selecione um valor de uma lista de valores conhecidos")](property-lists-images/image32.png#lightbox)

O Visual Studio para Mac também detecta o tipo de propriedade, conforme mostrado:

 [![](property-lists-images/image33.png "Os tipos de propriedades disponíveis")](property-lists-images/image33.png#lightbox)

Examine da Apple [recursos relacionados do aplicativo](http://developer.apple.com/library/ios/#DOCUMENTATION/iPhone/Conceptual/iPhoneOSProgrammingGuide/App-RelatedResources/App-RelatedResources.html) links para saber mais sobre as propriedades opcionais.

 <a name="Entitlements" />

## <a name="summary"></a>Resumo

Este artigo demonstrou usando os editores. plist gráfica e avançado para editar configurações de aplicativo comuns, bem como para especificar os ícones e imagens de inicialização. Ele também introduziu o `Entitlements.plist` para adicionar e gerenciar recursos de aplicativo.


## <a name="related-links"></a>Links relacionados

- [IDE](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide)
- [Recursos relacionados do aplicativo](http://developer.apple.com/library/ios/#DOCUMENTATION/iPhone/Conceptual/iPhoneOSProgrammingGuide/App-RelatedResources/App-RelatedResources.html)
- [Registrando o arquivo de tipos de suporte do aplicativo](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html)
- [Implementar esquemas de URL personalizada](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedAppTricks/AdvancedAppTricks.html)
- [Referência de formato de catálogo ativo](https://developer.apple.com/library/archive/documentation/Xcode/Reference/xcode_ref-Asset_Catalog_Format/index.html#//apple_ref/doc/uid/TP40015170-CH18-SW1)
