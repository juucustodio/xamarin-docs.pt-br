---
title: Introdução ao iOS 12
description: Este documento fornece uma descrição de alto nível de algumas APIs do iOS 12 para as quais a versão de C# visualização do Xamarin fornece associações.
ms.prod: xamarin
ms.assetid: 99EA7090-315D-493C-87D3-26AB73D9E1A9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/08/2018
ms.openlocfilehash: 39d954626bc9e789446e7f1deac67e2e0fca51c8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032001"
---
# <a name="introduction-to-ios-12"></a>Introdução ao iOS 12

Este documento fornece uma descrição de alto nível de algumas APIs do iOS 12 para as quais a versão de C# visualização do Xamarin fornece associações.

Para começar a criar aplicativos iOS 12 com o Xamarin, consulte o [Guia de introdução](get-started.md)

## <a name="arkit-2arkit2md"></a>[ARKit 2](arkit2.md)

ARKit é a estrutura de realidade aumentada incluída no iOS. O ARKit 2 permite que vários usuários interajam entre si em uma cena de realidade aumentada, possibilita a persistência de objetos no espaço e retorne a eles posteriormente e fornece reconhecimento de imagem 2D e rastreamento de objetos 3D. o iOS 12 também oferece uma visão rápida do AR, uma maneira de renderizar modelos usdz AR em seus aplicativos.

## <a name="siri-shortcutssiri-shortcutsmd"></a>[Atalhos do Siri](siri-shortcuts.md)

Os atalhos do Siri permitem que os desenvolvedores integrem mais profundamente seus aplicativos com o Siri. Com atalhos do Siri, os usuários podem usar comandos de voz para abrir conteúdo ou iniciar tarefas em segundo plano, ou podem iniciar essas mesmas tarefas por meio de atalhos que o Siri sugere na tela de bloqueio.

## <a name="core-ml-2coremlmd"></a>[Core ML 2](coreml.md)

O Core ML 2 reduz o tamanho do aplicativo por meio de quantificação de modelo e modelos flexíveis, melhora o desempenho do aplicativo com uma nova API de previsão de lote e usa modelos personalizados para dar suporte a avanços no aprendizado de máquina.

## <a name="notification-improvementsnotificationsindexmd"></a>[Aprimoramentos de notificação](notifications/index.md)

No iOS 12, as notificações agrupadas possibilitam apresentar notificações de usuário em agrupamentos relacionados ao aplicativo ou ao thread. O texto de resumo fornece mais informações sobre um grupo de notificações.

As extensões de conteúdo de notificação no iOS 12 permitem interfaces de usuário personalizadas e botões de ação dinâmicas.

## <a name="natural-language-frameworknatural-languagemd"></a>[Estrutura de linguagem natural](natural-language.md)

A estrutura de linguagem natural permite que os aplicativos executem vários tipos de análise de linguagem. Por exemplo, ele pode identificar partes de fala e determinar o idioma representado por um bloco de texto.

## <a name="vision-frameworkiosplatformintroduction-to-ios11visionmd"></a>[Estrutura de visão](~/ios/platform/introduction-to-ios11/vision.md)

A estrutura de visão inclui um detector facial aprimorado que pode detectar rostos em várias orientações. Além disso, as revisões de solicitação podem selecionar revisão de algoritmo do Vision Framework específica.

## <a name="photo-and-video-apis"></a>APIs de foto e vídeo

No iOS 12, a API de segmentação retrato retorna um efeito de retrato matte – uma máscara linear que delineia o primeiro plano do plano de fundo de uma imagem retrato e é útil na criação de vários efeitos de imagem. o iOS 12 também torna possível usar dados de profundidade da câmera TrueDepth para efeitos de vídeo em tempo real.

## <a name="passwords"></a>Suas

o iOS 12 torna mais fácil para os usuários e desenvolvedores trabalhar com senhas:

- O AutoPreenchimento de senha e senhas fortes automáticas possibilitam gerar, armazenar e usar senhas fortes automaticamente em aplicativos iOS ao se inscrever e fazer logon em um aplicativo.
- O preenchimento automático de código de segurança possibilita o uso de códigos de autenticação baseados em SMS sem recorte manual e colagem ou memorização.
- A classe `ASWebAuthenticationSession` simplifica o processo de trabalho com serviços de autenticação federada.
- As extensões do provedor de credenciais de preenchimento automático possibilitam que aplicativos de senha de terceiros forneçam nome de usuário e senhas aos campos de logon.

## <a name="healthkit-updates"></a>Atualizações do HealthKit

o iOS 11,3 introduziu [registros de integridade](https://www.apple.com/healthcare/health-records/), o que permite que os usuários baixem suas informações de registro de integridade de várias instituições de saúde e as exibam em seus dispositivos IOS. o iOS 12 adiciona APIs que permitem que aplicativos de terceiros acessem esses dados com segurança.

## <a name="imessage-app-presentation-contexts"></a>contextos de apresentação do aplicativo iMessage

No iOS 12, os aplicativos iMessage dão suporte a contextos de apresentação, que permitem que os aplicativos sejam executados como um aplicativo iMessage normal ou no contexto de um efeito de foto ou vídeo.

## <a name="network-framework"></a>Estrutura de rede

A estrutura de rede, a pilha de rede subjacente às APIs de `URLSession` comumente usadas em aplicativos iOS, agora está disponível como uma estrutura autônoma, facilitando o trabalho com TCP, UDP, TLS, IPv4/IPv6 e muito mais.

## <a name="carplay"></a>CarPlay

No iOS 12, aplicativos de terceiros podem entregar mapas e ativar instruções de navegação em CarPlay usando a nova estrutura CarPlay.

## <a name="deprecations"></a>Substituições

Com o iOS 12, a Apple preteriu:

- OpenGL ES, [incentivando os desenvolvedores](https://developer.apple.com/ios/whats-new/) a adotar o metal.
- [`UIWebView`](xref:UIKit.UIWebView), [em favor de `WKWebView`](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).
