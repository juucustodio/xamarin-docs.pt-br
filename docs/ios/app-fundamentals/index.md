---
title: Conceitos básicos de aplicativo
description: Conceitos fundamentais do aplicativo
ms.prod: xamarin
ms.assetid: 608403AE-B09F-4D9C-8F59-F9DE9F0B1CF1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/21/2017
ms.openlocfilehash: d8dc1e25de527357fe6ad3ad1328a930e0e4dc70
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="application-fundamentals"></a>Conceitos básicos de aplicativo

Esta seção fornece um guia sobre alguns dos conceitos que os desenvolvedores precisam estar atento ao desenvolvimento de aplicativos xamarin (anteriormente conhecida como MonoTouch) ou tarefas de coisas mais comuns.

## <a name="app-transport-securityiosapp-fundamentalsatsmd"></a>[Segurança de transporte de aplicativo](~/ios/app-fundamentals/ats.md)

Este artigo apresenta as alterações de segurança que impõe a segurança de transporte do aplicativo em um aplicativo iOS 9 e o que isso significa para seus projetos xamarin, ele aborda as opções de configuração ATS e ele aborda como recusar ATS, se necessário. Como ATS está habilitado por padrão, todas as conexões da internet não segura gerará uma exceção em aplicativos do iOS 9 (a menos que você explicitamente permitido-lo).


## <a name="backgroundingiosapp-fundamentalsbackgroundingindexmd"></a>[Backgrounding](~/ios/app-fundamentals/backgrounding/index.md)

Plano de fundo de processamento ou backgrounding é o processo de permitir que aplicativos executar tarefas em segundo plano enquanto outro aplicativo está em execução em primeiro plano. Este guia serve como uma introdução ao processamento de iOS em segundo plano.


## <a name="events-protocols-and-delegatesiosapp-fundamentalsdelegates-protocols-and-eventsmd"></a>[Eventos, protocolos e delegados](~/ios/app-fundamentals/delegates-protocols-and-events.md)

Este artigo apresenta as tecnologias de iOS chave usadas para receber retornos de chamada e preencher os controles de interface de usuário com dados. Essas tecnologias são eventos, protocolos e delegados; Este artigo explica o que cada um deles for e como cada um é usada no c#. Ele demonstra como xamarin usa controles de iOS para expor familiares eventos de .NET, bem como xamarin oferece suporte para conceitos de Objective-C como protocolos e delegados (delegados Objective-C não devem ser confundidos com c# delegados). Este artigo também fornece exemplos que mostram como protocolos são usados como base para delegados Objective-C e em cenários de não delegado.

## <a name="threadingiosapp-fundamentalsthreadingmd"></a>[Threading](~/ios/app-fundamentals/threading.md)

Este artigo discute threading em um aplicativo xamarin e um pouco fala sobre o pool de threads do .NET, aplicativos responsivos e coleta de lixo.&nbsp;

## <a name="working-with-imagesiosapp-fundamentalsimages-iconsindexmd"></a>[Trabalhando com imagens](~/ios/app-fundamentals/images-icons/index.md)

Este artigo examina como usar imagens em xamarin, imagens de suporte do aplicativo (como ícones, carregamento de imagens, etc.) e imagens dentro de aplicativos (como imagens aplicadas aos controles). Ele também aborda como usar o Visual Studio para Mac para inserir imagens, bem como interagir com imagens do código.

## <a name="working-with-property-listsiosapp-fundamentalsindexmd"></a>[Trabalhando com listas de propriedades](~/ios/app-fundamentals/index.md)

Este documento apresenta o Visual Studio para o editor de lista (. plist) de propriedade gráfica e avançados do Mac para trabalhar com info. plist e Entitlements.plist. Ele ilustra os ícones de configuração e imagens de aplicativo do iOS de inicialização e demonstra especificando recursos de aplicativo (direitos) do Visual Studio para Mac.

## <a name="working-with-the-file-systemiosapp-fundamentalsfile-systemmd"></a>[Trabalhando com o sistema de arquivos](~/ios/app-fundamentals/file-system.md)

Xamarin pode usar as mesmas classes System.IO para trabalhar com arquivos e diretórios no iOS que você usaria em qualquer aplicativo .NET. No entanto, apesar dos métodos e classes familiares, iOS implementa algumas restrições sobre os arquivos que podem ser acessados ou criados e também fornece recursos especiais para determinados diretórios. Este artigo descreve essas restrições e recursos e demonstra como o acesso a arquivos funciona em um aplicativo xamarin.

## <a name="creating-ios-applications-in-codeiosapp-fundamentalsios-code-onlymd"></a>[Criando aplicativos iOS em código](~/ios/app-fundamentals/ios-code-only.md)

Este artigo examina como criar aplicativos iOS inteiramente no código usando o Visual Studio e o Visual Studio para Mac. Ele mostra como começar de um modelo de projeto vazio para fazer uma tela de aplicativo em um controlador, criando uma hierarquia de modos de exibição de UIKit. Depois, aborda como criar modos de exibição personalizados que podem ser carregados em um controlador.

## <a name="working-with-user-defaultsiosapp-fundamentalsuser-defaultsmd"></a>[Trabalhando com os padrões do usuário](~/ios/app-fundamentals/user-defaults.md)

O `NSUserDefaults` classe fornece uma maneira para iOS, aplicativos e extensões para interagir programaticamente com o sistema de padrão de todo o sistema. Usando o sistema de padrões, o usuário pode configurar o comportamento de um aplicativo ou de estilo para atender às suas preferências (com base no design do aplicativo). Por exemplo, para apresentar dados de métricas vs medidas imperiais ou selecionar um tema de interface do usuário especificado.

## <a name="touchiosapp-fundamentalstouchindexmd"></a>[Toque](~/ios/app-fundamentals/touch/index.md)

Telas sensíveis ao toque em muitos dos dispositivos atuais permitem que usuários rápida e eficientemente interagir com dispositivos de uma maneira natural e intuitiva. Essa interação não está limitada apenas a detecção de toque simples – é possível usar gestos também. Por exemplo, o gesto de pinçagem para aplicar zoom é um exemplo comum disso – por esmagamento uma parte da tela com dois dedos, que o usuário pode ampliar ou reduzir. Este guia examina toque e gestos em iOS.

## <a name="working-with-security-and-privacyiosapp-fundamentalssecurity-privacymd"></a>[Trabalhando com segurança e privacidade](~/ios/app-fundamentals/security-privacy.md)

Apple fez diversos aprimoramentos de segurança e privacidade no iOS 10 (e maior) que podem ajudar o desenvolvedor de melhorar a segurança dos seus aplicativos e garantir a privacidade do usuário final. Este artigo abordará implementar esses recursos em um aplicativo xamarin.

##  <a name="localizationiosapp-fundamentalslocalizationindexmd"></a>[Localização](~/ios/app-fundamentals/localization/index.md)

Este guia aborda a adição de codificações para um aplicativo xamarin para dar suporte à internacionalização.