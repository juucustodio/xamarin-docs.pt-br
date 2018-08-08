---
title: Introdução ao iOS 12
description: Este documento fornece uma descrição detalhada de algumas APIs do iOS 12 para visualização do Xamarin, qual versão fornece associações c#.
ms.prod: xamarin
ms.assetid: 99EA7090-315D-493C-87D3-26AB73D9E1A9
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/08/2018
ms.openlocfilehash: 4e1249b7a9c1e9797cbc758c3bd1b83f87d47431
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615142"
---
# <a name="introduction-to-ios-12"></a>Introdução ao iOS 12

![Visualizar](~/media/shared/preview.png)

> [!WARNING]
> Suporte a 12 do Xamarin iOS está atualmente em visualização, o que significa que ele pode conter bugs, não é todos os recursos, e pode ser alterado. Usá-lo apenas para experimentação.

Este documento fornece uma descrição detalhada de algumas APIs do iOS 12 para visualização do Xamarin, qual versão fornece associações c#.

Para começar a criar aplicativos iOS 12 com Xamarin, dê uma olhada:

- O [guia de Introdução](get-started.md)
- A visualização de Xamarin [postagem no blog de versão](https://releases.xamarin.com/preview-release-xcode-10-beta-5/)

## <a name="arkit-2"></a>ARKit 2

ARKit é a estrutura de realidade aumentada incluída com o iOS. ARKit 2 permite que vários usuários interajam entre si em uma cena de realidade aumentada, torna possível persistir objetos no espaço e retornar a elas em um momento posterior e fornece reconhecimento de imagens 2D e 3D e controle de reconhecimento de objeto. iOS 12 também fornece AR rápido olhar, uma maneira de renderizar usdz modelos de AR em seus aplicativos.

## <a name="siri-shortcuts"></a>Atalhos da Siri

Siri atalhos permitem aos desenvolvedores mais profundamente integrar seus aplicativos com Siri. Com os atalhos de Siri, os usuários podem usar comandos de voz para abrir o conteúdo ou disparar tarefas em seus aplicativos. Siri aprenderá quando alguns atalhos estão mais probabilidade de ser usado e sugeri-los para o usuário por meio de notificações.

## <a name="core-ml-2"></a>Core ML 2

Core ML 2 reduz o tamanho do aplicativo por meio de quantização de modelo e modelos flexíveis, melhora o desempenho de aplicativos com uma nova API de previsão em lotes e usa modelos personalizados para dar suporte a avanços no aprendizado de máquina.

## <a name="notification-improvements"></a>Aprimoramentos na notificação

No iOS 12, notificações agrupadas possibilitam que as notificações de usuário presente no aplicativo ou agrupamentos de thread. Texto do resumo pode ser usado para fornecer mais informações sobre um grupo de notificação.

Permitem extensões de conteúdo de notificação no iOS 12 para interfaces do usuário personalizadas e ações dinâmicas. Esses recursos permitem experiências mais avançadas, mais relevantes em notificações de usuário.

## <a name="natural-language-framework"></a>Estrutura de linguagem natural

A estrutura de linguagem Natural permite que os aplicativos executar vários tipos de análise de linguagem. Por exemplo, ele pode ser usado para identificar partes da fala e determinar o idioma representado por um bloco de texto.

## <a name="carplay"></a>CarPlay

No iOS 12, aplicativos de terceiros podem fornecer mapas e instruções de navegação de folheio de curva em CarPlay usando a nova estrutura de CarPlay.

## <a name="automatic-strong-passwords"></a>Senhas fortes automática

iOS 12 irá sugerir e armazenar nomes de usuário e senhas fortes para aplicativos que contêm uma tela de conta de criar. As senhas sugeridas que podem ser geradas com base em um padrão, o formato de 20 caracteres ou com base nas regras de senha especificados pelo desenvolvedor. Esse recurso usa domínios associados e tipos de conteúdo especificados no novo nome de usuário e novos campos de senha.

## <a name="autofill-credential-provider-extensions"></a>Extensões de provedor de credenciais de preenchimento automático

Com o iOS 12, aplicativos do Gerenciador de senha de terceiros podem fornecer uma extensão para fornecer valores de nome de usuário e senha para campos de logon.

## <a name="healthkit-updates"></a>Atualizações de HealthKit

iOS 11.3 introduzido [registros de saúde](https://www.apple.com/healthcare/health-records/), que permite aos usuários baixar sua integridade registre informações de diversas instituições de saúde e exibi-lo em seus dispositivos iOS. iOS 12 adiciona APIs que permitem que aplicativos de terceiros acessem esses dados.

## <a name="imessage-app-presentation-contexts"></a>iMessage contextos de apresentação do aplicativo

No iOS 12, os aplicativos de iMessage dão suporte a contextos de apresentação, que permitem que os aplicativos executados como um aplicativo iMessage normal ou no contexto de uma foto ou um efeito de vídeo.

## <a name="vision-framework"></a>Estrutura Vision

A estrutura de visão inclui um detector de face aprimorados que pode detectar faces em várias orientações. Além disso, as revisões de solicitação agora podem ser usadas para selecionar uma revisão do algoritmo de framework visão específica.

## <a name="related-links"></a>Links relacionados

- [Prepare-se para iOS 12 (Apple)](https://developer.apple.com/ios/)
- [iOS 12 Preview (Apple)](https://www.apple.com/ios/ios-12-preview/)
- Versão prévia do Xamarin [postagem no blog de versão](https://releases.xamarin.com/preview-release-xcode-10-beta-5/)
