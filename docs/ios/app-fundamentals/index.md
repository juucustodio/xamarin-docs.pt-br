---
title: Conceitos básicos do aplicativo Xamarin. iOS
description: Este documento contém links para vários guias que descrevem conceitos fundamentais para o desenvolvimento do Xamarin. iOS, como segurança de transporte de aplicativo, plano de fundo, eventos e Threading.
ms.prod: xamarin
ms.assetid: 608403AE-B09F-4D9C-8F59-F9DE9F0B1CF1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/21/2017
ms.openlocfilehash: 0ccdde29183645b93831b7261909714f9baf3fa4
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73010022"
---
# <a name="xamarinios-application-fundamentals"></a>Conceitos básicos do aplicativo Xamarin. iOS

Esta seção fornece um guia sobre algumas das tarefas de coisas mais comuns ou conceitos que os desenvolvedores precisam saber ao desenvolver aplicativos Xamarin. iOS (anteriormente MonoTouch).

## <a name="accessibilityiosapp-fundamentalsaccessibilitymd"></a>[Acessibilidade](~/ios/app-fundamentals/accessibility.md)

Este documento descreve várias APIs e ferramentas que podem ser usadas para ajudar a criar aplicativos acessíveis a tantos usuários quanto possível.

## <a name="app-transport-securityiosapp-fundamentalsatsmd"></a>[Segurança de transporte de aplicativo](~/ios/app-fundamentals/ats.md)

Este artigo apresentará as alterações de segurança que a segurança de transporte de aplicativo impõe em um aplicativo iOS 9 e o que isso significa para seus projetos do Xamarin. iOS, que abordará as opções de configuração do ATS e abordará como recusar a ATS, se necessário. Como o ATS está habilitado por padrão, as conexões de Internet não seguras gerarão uma exceção nos aplicativos do iOS 9 (a menos que você tenha explicitamente permitido).

## <a name="backgroundingiosapp-fundamentalsbackgroundingindexmd"></a>[Backgrounding](~/ios/app-fundamentals/backgrounding/index.md)

O processamento em segundo plano ou o plano de fundo é o processo de permitir que aplicativos executem tarefas em segundo plano enquanto outro aplicativo está em execução em primeiro plano. Este guia serve como uma introdução ao processamento em segundo plano no iOS.

## <a name="creating-ios-applications-in-codeiosapp-fundamentalsios-code-onlymd"></a>[Criando aplicativos iOS no código](~/ios/app-fundamentals/ios-code-only.md)

Este artigo examina como criar aplicativos iOS inteiramente no código usando o Visual Studio e o Visual Studio para Mac. Ele mostra como começar de um modelo de projeto vazio para fazer uma tela de aplicativo em um controlador, criando uma hierarquia de modos de exibição de UIKit. Depois, aborda como criar modos de exibição personalizados que podem ser carregados em um controlador.

## <a name="exception-marshalingiosplatformexception-marshalingmd"></a>[Marshaling de exceção](~/ios/platform/exception-marshaling.md)

Descreve como o Objective-C e as exceções gerenciadas são empacotados entre quadros nativos e gerenciados.

## <a name="events-protocols-and-delegatesiosapp-fundamentalsdelegates-protocols-and-eventsmd"></a>[Eventos, protocolos e delegados](~/ios/app-fundamentals/delegates-protocols-and-events.md)

Este artigo apresenta as principais tecnologias do iOS usadas para receber retornos de chamada e para preencher controles de interface do usuário com dados. Essas tecnologias são eventos, protocolos e delegados; Este artigo explica o que cada um deles é e como cada um deles C#é usado. Ele demonstra como o Xamarin. iOS usa os controles do iOS para expor eventos .NET conhecidos, além de como o Xamarin. iOS fornece suporte para conceitos de Objective-C, como protocolos e delegados (delegados de Objective C# -c não devem ser confundidos com delegados). Este artigo também fornece exemplos que mostram como os protocolos são usados como a base para delegados Objective-C e em cenários não delegados.

## <a name="working-with-the-file-systemiosapp-fundamentalsfile-systemmd"></a>[Trabalhando com o sistema de arquivos](~/ios/app-fundamentals/file-system.md)

O Xamarin. iOS pode usar as mesmas classes System.IO para trabalhar com arquivos e diretórios no iOS que você usaria em qualquer aplicativo .NET. No entanto, apesar das classes e métodos familiares, o iOS implementa algumas restrições nos arquivos que podem ser criados ou acessados e também fornece recursos especiais para determinados diretórios. Este artigo descreve essas restrições e recursos e demonstra como o acesso ao arquivo funciona em um aplicativo Xamarin. iOS.

## <a name="working-with-imagesiosapp-fundamentalsimages-iconsindexmd"></a>[Trabalhando com imagens](~/ios/app-fundamentals/images-icons/index.md)

Este artigo examina como usar imagens no Xamarin. iOS, as imagens de suporte a aplicativos (como ícones, carregar imagens etc.) e imagens em aplicativos (como imagens aplicadas a controles). Ele também aborda como usar Visual Studio para Mac para incorporar imagens e como interagir com imagens do código.

## <a name="localizationiosapp-fundamentalslocalizationindexmd"></a>[Localização](~/ios/app-fundamentals/localization/index.md)

Este guia aborda a adição de codificações a um aplicativo Xamarin. iOS para dar suporte à internacionalização.

## <a name="working-with-property-listsiosapp-fundamentalsindexmd"></a>[Trabalhando com listas de propriedades](~/ios/app-fundamentals/index.md)

Este documento apresenta o editor de lista de Propriedades (. plist) gráfico e avançado da Visual Studio para Mac para trabalhar com o info. plist e o intitulors. plist. Ele ilustra a definição de ícones e a inicialização de imagens para o aplicativo iOS e demonstra a especificação de recursos de aplicativo (direitos) de dentro de Visual Studio para Mac.

## <a name="working-with-security-and-privacyiosapp-fundamentalssecurity-privacymd"></a>[Trabalhando com segurança e privacidade](~/ios/app-fundamentals/security-privacy.md)

A Apple fez vários aprimoramentos na segurança e na privacidade no iOS 10 (e superior) que ajudarão o desenvolvedor a melhorar a segurança de seus aplicativos e garantir a privacidade do usuário final. Este artigo abordará a implementação desses recursos em um aplicativo Xamarin. iOS.

## <a name="threadingiosapp-fundamentalsthreadingmd"></a>[Threading](~/ios/app-fundamentals/threading.md)

Este artigo aborda o Threading em um aplicativo Xamarin. iOS e fala um pouco sobre o pool de threads do .NET, aplicativos responsivos e coleta de lixo.

## <a name="touchiosapp-fundamentalstouchindexmd"></a>[Toque](~/ios/app-fundamentals/touch/index.md)

As telas de toque em muitos dos dispositivos atuais permitem que os usuários interajam de forma rápida e eficiente com os dispositivos de maneira natural e intuitiva. Essa interação não é limitada apenas à detecção simples de toque – também é possível usar gestos. Por exemplo, o gesto de pinçar para zoom é um exemplo muito comum disso – ao pinçar uma parte da tela com dois dedos, o usuário pode ampliar ou reduzir. Este guia examina toque e gestos no iOS.

## <a name="working-with-user-defaultsiosapp-fundamentalsuser-defaultsmd"></a>[Trabalhando com padrões de usuário](~/ios/app-fundamentals/user-defaults.md)

A classe `NSUserDefaults` fornece uma maneira para que aplicativos e extensões do iOS interajam programaticamente com o sistema padrão de todo o sistema. Usando o sistema de padrões, o usuário pode configurar o comportamento ou o estilo de um aplicativo para atender às suas preferências (com base no design do aplicativo). Por exemplo, para apresentar dados em medidas métricas vs Imperial ou selecione um determinado tema da interface do usuário.
