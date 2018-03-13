---
title: Trabalhando com listas de propriedades
description: "Este documento apresenta o Visual Studio para o editor de lista (. plist) de propriedade gráfica e avançados do Mac para trabalhar com info. plist e Entitlements.plist. Ele ilustra os ícones de configuração e imagens de inicialização para aplicativos do iOS do Visual Studio para Mac."
ms.topic: article
ms.prod: xamarin
ms.assetid: 5E687043-0443-377C-9A12-9C5A05958646
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 778e70f6817b71e5910aa85425d46261dfe9c803
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="working-with-property-lists"></a>Trabalhando com listas de propriedades

_Este documento apresenta o Visual Studio para o editor de lista (. plist) de propriedade gráfica e avançados do Mac para trabalhar com info. plist e Entitlements.plist. Ele ilustra os ícones de configuração e imagens de inicialização para aplicativos do iOS do Visual Studio para Mac._

O Visual Studio para Mac apresenta um editor. plist gráficas que facilita a edição das propriedades do aplicativo e os recursos mais fácil. O Visual Studio para Mac tem dois .plists - `Info.plist` para editar propriedades do aplicativo e ícones, e `Entitlements.plist` para gerenciar recursos de aplicativo. Este guia apresenta o Info.plists e fornece uma visão geral de como trabalhar com eles no Visual Studio para Mac. Para obter informações sobre Entitlements.plist, consulte o [trabalhando com direitos](~/ios/deploy-test/provisioning/entitlements.md) guia.

## <a name="infoplist"></a>Info.plist

A lista de propriedades de informações ( `Info.plist`) é um arquivo de iOS necessários que fornece informações sobre a configuração do aplicativo no sistema. O Visual Studio para Mac do personalizado `Info.plist` três painéis controlados pelas guias na parte inferior esquerdas da janela do editor de recursos do editor:

 [![](property-lists-images/tabs.png "O editor de info. plist guias na parte inferior esquerda da janela do editor")](property-lists-images/tabs.png#lightbox)

Cada painel controles propriedades diferentes, conforme descrito a seguir:

-  **Painel do aplicativo** -uma interface gráfica para definir propriedades do aplicativo, bem como ícones e inicialização comuns imagens; especificar mapas de integração e backgrounding modos.
-  **Advanced painel** -painel avançado é o local para especificar os tipos de documento com suporte, UTIs e os tipos de URL.
-  **Painel de origem** -painel origem controla propriedades menos comuns, bem como propriedades personalizadas para o aplicativo.


As próximas três seções investigue os recursos de cada painel mais detalhadamente.

## <a name="application-panel"></a>Painel do aplicativo

O Visual Studio para Mac apresenta uma interface gráfica para edição comuns `Info.plist` entradas para um aplicativo:

1.  Propriedades do aplicativo
1.  Tipos de dispositivos com suporte
1.  Suporte de orientações para cada tipo de dispositivo
1.  Barra de estilo e cor de status
1.  Ícones e telas de inicialização
1.  Mapas e modos de segundo plano


Eles são descritos mais detalhadamente nas próximas seções.

 <a name="iOS_Application_Target" />


### <a name="ios-application-target"></a>Destino do aplicativo do iOS

Esta seção contém informações importantes que descrevem seu aplicativo.
O **identificador** armazenados aqui deve coincidir com o identificador de pacote que é inserida na iTunes Connect (para aplicativos da Windows Store) e também na lista de IDs de aplicativo de Portal de provisionamento do iOS e certificados de desenvolvimento e distribuição.

 [![](property-lists-images/image24.png "Destino do aplicativo do iOS")](property-lists-images/image24.png#lightbox)

### <a name="device-deployment"></a>Implantação de dispositivo

 [![](property-lists-images/deployment.png "Implantação de dispositivo")](property-lists-images/deployment.png#lightbox)

O dispositivo **implantação** seções de informações são exibidas seletivamente, dependendo da seleção no **dispositivos** suspensa no **aplicativo destino** seção acima. O **Interface principal** suspenso é definido como **MainStoryboard** em aplicativos voltados para o Storyboard. Se a interface do usuário inteiramente escrita em código, em seguida, isso pode ser deixado em branco.

### <a name="supported-device-orientations"></a>Orientações de dispositivos com suporte

 **Suporte de orientações do dispositivo** controla como o aplicativo responde a rotação de dispositivos. É muito comum para aplicativos iPhone/iPad dar suporte a apenas **retrato**, ou tudo, mas **cabeça**. Geralmente todos os aplicativos de iPad exceto jogos devem oferecer suporte a todas as orientações.

### <a name="status-bar-styles"></a>Estilos de barra de status

O **estilos de barra de Status** seção é uma interface gráfica para a edição de um aplicativo `UIStatusBarStyle`:

 [![](property-lists-images/status.png "Estilos de barra de status")](property-lists-images/status.png#lightbox)

 <a name="Icons" />


### <a name="icons-launch-images-and-itunes-artwork"></a>Ícones, imagens de inicialização e iTunes arte final

Informações sobre como usar ícones, imagens e arte final no arquivo Info. plist podem ser encontradas no [trabalhando com imagens](~/ios/app-fundamentals/images-icons/index.md) guia.




### <a name="maps-integration-and-background-modes"></a>Integração de mapas e modos de segundo plano

O `Info.plist` contém seções especiais para especificar mapas de integração e backgrounding modos. Escolher as opções que você deseja dar suporte, você adicionará as propriedades necessárias para seu aplicativo para você.

 [![](property-lists-images/maps.png "Integração de mapas")](property-lists-images/maps.png#lightbox)

Para obter mais informações sobre como trabalhar com mapas, consulte o Xamarin [iOS mapas](~/ios/user-interface/controls/ios-maps/index.md) guia.

 [![](property-lists-images/bging.png "Modos de segundo plano")](property-lists-images/bging.png#lightbox)

Para obter mais informações sobre modos de segundo plano, consulte o Xamarin [Backgrounding no iOS](~/ios/app-fundamentals/backgrounding/introduction-to-backgrounding-in-ios.md) guia.

## <a name="advanced-panel"></a>Painel avançado

O painel avançado controla os tipos de documento e esquemas de URL compatível com o aplicativo.

 [![](property-lists-images/image34.png "Painel avançado")](property-lists-images/image34.png#lightbox)

 <a name="Document_Types" />


## <a name="document-types"></a>Tipos de documento

Para aplicativos que oferecem suporte à abertura de tipos específicos de arquivos, o iOS fornece o `CFBundleDocumentTypes` chave. Se quisermos nosso aplicativo para oferecer suporte a determinados tipos de arquivo conhecidos - por exemplo PDFs - estamos adicionaria o valor PDF para a chave. Esta seção fornece uma maneira conveniente para inserir os dados que será armazenada na `CFBundleDocumentTypes` chave no `Info.plist` arquivo.

Consulte a documentação em [registrar o arquivo de tipos de seu aplicativo suporta](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html) para obter detalhes sobre como configurar esses valores.

## <a name="utis"></a>UTIs

Às vezes, um aplicativo deve oferecer suporte à abertura de um tipo de arquivo personalizado. Por exemplo, podemos querer abrir arquivos de imagem com uma extensão personalizada *.xam*. Para especificar um tipo de arquivo personalizado, vamos criar um utilitário - identificador de tipo Universal - personalizado usando o `UIExportedTypeDeclarations` chave. Captura de tela abaixo mostra como criar um utilitário personalizado para a extensão .xam:

 [![](property-lists-images/uti.png "Editor de UTIs")](property-lists-images/uti.png#lightbox)

Tipo exportado apenas como UTIs especificar UTIs personalizados específicos para seu aplicativo, o *importado tipo UTIs* ( `UIImportedTypeDeclarations` chave) especifique tipos personalizados com suporte, mas não pertencente ao seu aplicativo.

Para obter mais informações sobre como usar UTIs personalizados, consulte da Apple [registrar arquivo tipos de seu aplicativo suporta](https://developer.apple.com/library/ios/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_declare/understand_utis_declare.html#//apple_ref/doc/uid/TP40001319-CH204-SW1) guia.

## <a name="custom-urls"></a>URLs personalizados

Um nome de esquema de URL (também chamado de protocolo) é a primeira parte da URL. Por exemplo, `http://` e `https://` são os esquemas de URL comuns. Você tem a opção de criar um esquema de URL personalizado para seu aplicativo. Os esquemas de URL personalizados são usados para se comunicar e enviar dados e para trás com outros aplicativos. Captura de tela a seguir ilustra a criação de um novo esquema de URL personalizado chamado `monkeys://`:

 [![](property-lists-images/url.png "URLs personalizados")](property-lists-images/url.png#lightbox)



Para obter mais informações sobre a implementação de esquemas de URL personalizadas, consulte da Apple [seção de implementação de esquemas de URL personalizada deste guia](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedAppTricks/AdvancedAppTricks.html)

## <a name="source-panel"></a>Painel de fonte

O **fonte** guia o `Info.plist` arquivo permite que os valores personalizados ser adicionado ou editado. O Visual Studio para Mac fornece uma lista das propriedades mais comuns:

 [![](property-lists-images/image31.png "Adicionar uma nova propriedade de uma lista suspensa")](property-lists-images/image31.png#lightbox)

Para propriedades conhecidas Visual Studio para Mac será uma lista de valores válidos, conforme ilustrado na captura de tela a seguir:

 [![](property-lists-images/image32.png "Selecione um valor de uma lista de valores conhecidos")](property-lists-images/image32.png#lightbox)

O Visual Studio para Mac também detecta o tipo de propriedade, conforme mostrado:

 [![](property-lists-images/image33.png "Os tipos de propriedades disponíveis")](property-lists-images/image33.png#lightbox)

Examine da Apple [recursos relacionados do aplicativo](http://developer.apple.com/library/ios/#DOCUMENTATION/iPhone/Conceptual/iPhoneOSProgrammingGuide/App-RelatedResources/App-RelatedResources.html) links para informações adicionais sobre as propriedades opcionais.

 <a name="Entitlements" />

## <a name="summary"></a>Resumo

Este artigo demonstrou usando os editores. plist gráfica e avançado para editar configurações de aplicativo comuns, bem como para especificar os ícones e imagens de inicialização. Ele também introduziu o `Entitlements.plist` para adicionar e gerenciar recursos de aplicativo.


## <a name="related-links"></a>Links relacionados

- [IDE](https://developer.xamarin.com/recipes/cross-platform/ide)
- [Recursos relacionados do aplicativo](http://developer.apple.com/library/ios/#DOCUMENTATION/iPhone/Conceptual/iPhoneOSProgrammingGuide/App-RelatedResources/App-RelatedResources.html)
- [Registrar o arquivo de tipos de seu aplicativo suporta](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html)
- [Implementando os esquemas de URL personalizado](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedAppTricks/AdvancedAppTricks.html)
- [Sobre catálogos de ativo](https://developer.apple.com/library/ioshttps://developer.xamarin.com/recipes/xcode_help-image_catalog-1.0/Recipe.html)
