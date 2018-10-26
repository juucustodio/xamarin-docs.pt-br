---
title: Conceitos básicos de aplicativo do xamarin. IOS
description: Este documento leva a vários guias que descrevem conceitos fundamentais para o desenvolvimento do xamarin. IOS, como a segurança de transporte de aplicativo, backgrounding, eventos e threading.
ms.prod: xamarin
ms.assetid: 608403AE-B09F-4D9C-8F59-F9DE9F0B1CF1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/21/2017
ms.openlocfilehash: a40227454b597578ff1c1c247b326e523c23493b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110466"
---
# <a name="xamarinios-application-fundamentals"></a>Conceitos básicos de aplicativo do xamarin. IOS

Esta seção fornece um guia sobre algumas das tarefas de coisas ou conceitos que os desenvolvedores precisam estar atento ao desenvolver aplicativos xamarin. IOS (anteriormente conhecido como MonoTouch) mais comuns.

## <a name="accessibilityiosapp-fundamentalsaccessibilitymd"></a>[Acessibilidade](~/ios/app-fundamentals/accessibility.md)

Este documento descreve as várias APIs e ferramentas que podem ser usadas para ajudar a criar aplicativos que são acessíveis para tantos usuários quanto possível.

## <a name="app-transport-securityiosapp-fundamentalsatsmd"></a>[Segurança de transporte de aplicativo](~/ios/app-fundamentals/ats.md)

Este artigo apresenta as alterações de segurança que impõe a segurança de transporte de aplicativo em um aplicativo iOS 9 e o que isso significa para seus projetos do xamarin. IOS, ele abordará as opções de configuração de ATS e abordará como recusar ATS, se necessário. Como ATS está habilitado por padrão, todas as conexões não seguras internet irá gerar uma exceção em aplicativos do iOS 9 (a menos que você explicitamente permitido-lo).

## <a name="backgroundingiosapp-fundamentalsbackgroundingindexmd"></a>[Backgrounding](~/ios/app-fundamentals/backgrounding/index.md)

Plano de fundo de processamento ou backgrounding é o processo de permitir que aplicativos executar tarefas em segundo plano, enquanto outro aplicativo está em execução em primeiro plano. Este guia serve como uma introdução ao processamento no iOS em segundo plano.

## <a name="creating-ios-applications-in-codeiosapp-fundamentalsios-code-onlymd"></a>[Criando aplicativos iOS no código](~/ios/app-fundamentals/ios-code-only.md)

Este artigo examina como criar aplicativos iOS inteiramente no código usando o Visual Studio e o Visual Studio para Mac. Ele mostra como começar de um modelo de projeto vazio para fazer uma tela de aplicativo em um controlador, criando uma hierarquia de modos de exibição de UIKit. Depois, aborda como criar modos de exibição personalizados que podem ser carregados em um controlador.

## <a name="events-protocols-and-delegatesiosapp-fundamentalsdelegates-protocols-and-eventsmd"></a>[Eventos, protocolos e delegados](~/ios/app-fundamentals/delegates-protocols-and-events.md)

Este artigo apresenta as tecnologias de chave iOS usadas para receber retornos de chamada e para popular os controles de interface do usuário com os dados. Essas tecnologias são eventos, protocolos e delegados; Este artigo explica o que cada um deles é e como cada um é usada em c#. Ele demonstra como o xamarin. IOS usa controles de iOS para expor familiares eventos do .NET, bem como a xamarin. IOS fornece suporte para conceitos de Objective-C, como protocolos e delegados (delegados de Objective-C não devem ser confundidos com delegados c#). Este artigo também fornece exemplos que mostram como protocolos são usados como base para delegados de Objective-C e em cenários de não delegado.

## <a name="working-with-the-file-systemiosapp-fundamentalsfile-systemmd"></a>[Trabalhando com o sistema de arquivos](~/ios/app-fundamentals/file-system.md)

Xamarin. IOS podem usar as mesmas classes System.IO para trabalhar com arquivos e diretórios no iOS que você usaria em qualquer aplicativo .NET. No entanto, apesar dos métodos e classes familiares, iOS implementa algumas restrições sobre os arquivos que podem ser acessados ou criados e também fornece recursos especiais para determinados diretórios. Este artigo descreve essas restrições e os recursos e demonstra como funciona o acesso de arquivo em um aplicativo xamarin. IOS.

## <a name="working-with-imagesiosapp-fundamentalsimages-iconsindexmd"></a>[Trabalhando com imagens](~/ios/app-fundamentals/images-icons/index.md)

Este artigo examina como usar imagens no xamarin. IOS, imagens de suporte do aplicativo (por exemplo, ícones, carregamento de imagens, etc.) e imagens em aplicativos (como imagens aplicados aos controles). Ele também aborda como usar o Visual Studio para Mac para incorporar imagens, bem como interagir com as imagens do código.

## <a name="localizationiosapp-fundamentalslocalizationindexmd"></a>[Localização](~/ios/app-fundamentals/localization/index.md)

Este guia aborda a adição de codificações para um aplicativo xamarin. IOS para dar suporte à internacionalização.

## <a name="working-with-property-listsiosapp-fundamentalsindexmd"></a>[Trabalhando com listas de propriedades](~/ios/app-fundamentals/index.md)

Este documento apresenta o Visual Studio para o editor de lista (. plist) de propriedade gráfica e avançados do Mac para trabalhar com info. plist e Entitlements. plist. Ele ilustra Definindo ícones e imagens de aplicativo iOS de inicialização e demonstra as capacidades do aplicativo especificando (direitos) de dentro do Visual Studio para Mac.

## <a name="working-with-security-and-privacyiosapp-fundamentalssecurity-privacymd"></a>[Trabalhando com segurança e privacidade](~/ios/app-fundamentals/security-privacy.md)

Apple fez várias melhorias de segurança e privacidade no iOS 10 (e superior) que ajudarão o desenvolvedor melhorar a segurança dos seus aplicativos e garantir a privacidade do usuário final. Este artigo abordará a implementação desses recursos em um aplicativo xamarin. IOS.

## <a name="threadingiosapp-fundamentalsthreadingmd"></a>[Threading](~/ios/app-fundamentals/threading.md)

Este artigo aborda o threading em um aplicativo xamarin. IOS e fala um pouco sobre o pool de threads do .NET, aplicativos responsivos e coleta de lixo.

## <a name="touchiosapp-fundamentalstouchindexmd"></a>[Toque](~/ios/app-fundamentals/touch/index.md)

Telas sensíveis ao toque em muitos dos dispositivos atuais permitem aos usuários rapidamente e com eficiência interagir com dispositivos de uma maneira natural e intuitiva. Essa interação não se limita apenas a detecção de toque simples – é possível usar gestos bem. Por exemplo, o gesto de pinçar para ampliar é um exemplo muito comum disso – por apertar uma parte da tela com dois dedos, que o usuário pode ampliar ou reduzir. Este guia examina o toque e gestos no iOS.

## <a name="working-with-user-defaultsiosapp-fundamentalsuser-defaultsmd"></a>[Trabalhando com os padrões do usuário](~/ios/app-fundamentals/user-defaults.md)

O `NSUserDefaults` classe fornece uma maneira para iOS, aplicativos e extensões para interagir programaticamente com o sistema de padrão de todo o sistema. Ao usar o sistema usa como padrão, o usuário pode configurar o comportamento de um aplicativo ou definição de estilo para atender às suas preferências (com base no design do aplicativo). Por exemplo, para apresentar dados da métrica vs Imperial medidas ou selecione um tema da interface do usuário fornecida.
