---
title: Introdução ao iOS 12
description: Este documento fornece uma descrição detalhada de algumas APIs do iOS 12 para visualização do Xamarin, qual versão fornece associações c#.
ms.prod: xamarin
ms.assetid: 99EA7090-315D-493C-87D3-26AB73D9E1A9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/08/2018
ms.openlocfilehash: 81375e8c66e5504604d0d4cb3be34afd58f4269d
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2018
ms.locfileid: "39615142"
---
# <a name="introduction-to-ios-12"></a>Introdução ao iOS 12

Este documento fornece uma descrição detalhada de algumas APIs do iOS 12 para visualização do Xamarin, qual versão fornece associações c#.

Para começar a criar aplicativos iOS 12 com Xamarin, consulte o [guia de Introdução](get-started.md)

## <a name="arkit-2arkit2md"></a>[ARKit 2](arkit2.md)

ARKit é a estrutura de realidade aumentada incluída com o iOS. ARKit 2 permite que vários usuários interajam entre si em uma cena de realidade aumentada, torna possível persistir objetos no espaço e retornar a elas em um momento posterior e fornece reconhecimento de imagens 2D e 3D e controle de reconhecimento de objeto. iOS 12 também fornece AR rápido olhar, uma maneira de renderizar usdz modelos de AR em seus aplicativos.

## <a name="siri-shortcutssiri-shortcutsmd"></a>[Atalhos da Siri](siri-shortcuts.md)

Siri atalhos permitem aos desenvolvedores mais profundamente integrar seus aplicativos com Siri. Com Siri atalhos, os usuários podem usar comandos de voz para abrir o conteúdo ou iniciar tarefas em segundo plano ou eles podem iniciar as mesmas tarefas por meio de atalhos que Siri sugere na tela de bloqueio.

## <a name="core-ml-2coremlmd"></a>[Core ML 2](coreml.md)

Core ML 2 reduz o tamanho do aplicativo por meio de quantização de modelo e modelos flexíveis, melhora o desempenho de aplicativos com uma nova API de previsão em lotes e usa modelos personalizados para dar suporte a avanços no aprendizado de máquina.

## <a name="notification-improvementsnotificationsindexmd"></a>[Aprimoramentos na notificação](notifications/index.md)

No iOS 12, notificações agrupadas possibilitam que as notificações de usuário presente no aplicativo ou agrupamentos de thread. Texto de resumo fornece mais informações sobre um grupo de notificação.

Permitem extensões de conteúdo de notificação no iOS 12 para interfaces do usuário personalizadas e botões de ação dinâmicos.

## <a name="natural-language-frameworknatural-languagemd"></a>[Estrutura de linguagem natural](natural-language.md)

A estrutura de linguagem Natural permite que os aplicativos executar vários tipos de análise de linguagem. Por exemplo, ele pode identificar partes da fala e determinar o idioma representado por um bloco de texto.

## <a name="vision-framework"></a>Estrutura Vision

A estrutura de visão inclui um detector de face aprimorados que pode detectar faces em várias orientações. Além disso, revisões de solicitação podem selecionar a revisão de algoritmo de framework visão específica.

## <a name="photo-and-video-apis"></a>Foto e as APIs do vídeo

No iOS 12, a segmentação de retrato API retorna um fosco de efeitos de retrato – uma máscara linear que delineia o primeiro plano do plano de fundo de uma imagem retrato e é útil para criar diversos efeitos de imagem. iOS 12 torna também é possível usar dados de profundidade da câmera TrueDepth para efeitos de vídeo em tempo real.

## <a name="passwords"></a>Senhas

iOS 12 torna mais fácil para os usuários e desenvolvedores trabalhem com senhas:

- Preenchimento automático de senha e senhas fortes automática tornam possível gerar, armazenar e usar senhas fortes em aplicativos do iOS ao inscrever-se e registro em log em um aplicativo automaticamente.
- Preenchimento automático de código de segurança torna possível usar códigos de autenticação com base em SMS sem corte manual e colá-lo ou memorização.
- O `ASWebAuthenticationSession` classe simplifica o processo de trabalhar com os serviços de autenticação federada.
- Extensões de provedor de credenciais de preenchimento automático possibilitam que os aplicativos de terceiros senha fornecer o nome de usuário e senhas para os campos de logon.

## <a name="healthkit-updates"></a>Atualizações de HealthKit

iOS 11.3 introduzido [registros de saúde](https://www.apple.com/healthcare/health-records/), que permite aos usuários baixar sua integridade registre informações de diversas instituições de saúde e exibi-lo em seus dispositivos iOS. iOS 12 adiciona APIs que permitem que aplicativos de terceiros acessem esses dados.

## <a name="imessage-app-presentation-contexts"></a>iMessage contextos de apresentação do aplicativo

No iOS 12, os aplicativos de iMessage dão suporte a contextos de apresentação, que permitem que os aplicativos executados como um aplicativo iMessage normal ou no contexto de uma foto ou um efeito de vídeo.

## <a name="network-framework"></a>Estrutura de rede

Pilha de rede de estrutura de rede subjacente a `URLSession` APIs comumente usadas em aplicativos do iOS, agora está disponível como uma estrutura autônoma, tornando mais fácil trabalhar com TCP, UDP, TLS, IPv4/IPv6 e muito mais.

## <a name="carplay"></a>CarPlay

No iOS 12, aplicativos de terceiros podem fornecer mapas e instruções de navegação de folheio de curva em CarPlay usando a nova estrutura de CarPlay.

## <a name="deprecations"></a>Substituições

Com o iOS 12, a Apple preteriu:

- OpenGL ES [encoraja os desenvolvedores](https://developer.apple.com/ios/whats-new/) adotar Bare Metal.
- [`UIWebView`](https://developer.xamarin.com/api/type/UIKit.UIWebView/), [em favor da `WKWebView` ](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).

## <a name="related-links"></a>Links relacionados

- [Prepare-se para iOS 12 (Apple)](https://developer.apple.com/ios/)
