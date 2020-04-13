---
title: Configuração do aplicativo Mac
description: Este documento descreve como configurar um aplicativo Xamarin.Mac para publicação. Ele aborda as configurações do aplicativo, as configurações de assinatura e as configurações de build.
ms.prod: xamarin
ms.assetid: fea66a34-1581-4cd6-b714-3fbff215a542
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 04/12/2017
ms.openlocfilehash: f008ac42bfffeda2a47ca30aa2991d91f990732f
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "76725032"
---
# <a name="mac-app-configuration"></a>Configuração do aplicativo Mac

## <a name="mac-app-configuration"></a>Configuração do aplicativo Mac

Clique com botão direito do mouse no projeto do aplicativo Mac no Visual Studio para Mac e escolha **Opções**.

### <a name="application-settings"></a>Configurações do aplicativo

Para alterar as configurações de aplicativo de um aplicativo Xamarin.Mac, clique duas vezes no arquivo **Info.plist** no **Painel de Soluções**:

![Selecionar o arquivo Info.plist](app-configuration-images/config04.png "Selecionar o arquivo Info.plist")

Isso exibirá as opções disponíveis para o aplicativo:

 [![Editar o arquivo Info.plist](app-configuration-images/config01.png "Editar o arquivo Info.plist")](app-configuration-images/config01-large.png#lightbox)

Aplicativos Mac em execução criados com Xamarin.Mac têm os seguintes requisitos do sistema:

- Um computador Mac executando o Mac OS X 10.7 ou posterior.

### <a name="signing-settings"></a>Configurações de assinatura

A seção **Assinatura do Mac** da caixa de diálogo **Opções de Projeto** permite que o desenvolvedor assine um aplicativo Xamarin.Mac para teste, para versão própria ou por meio da Apple App Store:

[![O editor de Assinatura do Mac](app-configuration-images/config02.png "A janela de Assinatura do Mac")](app-configuration-images/config02-large.png#lightbox)

Aqui, selecione a Identidade, o Perfil de provisionamento e quaisquer direitos personalizados usados para assinar o aplicativo quando ele é compilado. O desenvolvedor pode, opcionalmente, assinar o instalador usado para instalar o aplicativo em outro Mac.

### <a name="build-settings"></a>Configurações de build

A seção **Build do Mac** da caixa de diálogo **Opções de Projeto** permite que o desenvolvedor selecione a arquitetura de um aplicativo Xamarin.Mac, para controlar a qual versão do macOS o aplicativo dará suporte e, opcionalmente, para criar um pacote de instalação quando o aplicativo é compilado com êxito:

 [![Editar as configurações de build](app-configuration-images/config03.png "Editar as configurações de build")](app-configuration-images/config03-large.png#lightbox)

## <a name="related-links"></a>Links relacionados

- [Instalação](/visualstudio/mac/installation/)
- [Olá, amostra mac](~/mac/get-started/hello-mac.md)
- [Distribua aplicativos na Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [ID de Desenvolvedor e GateKeeper](https://developer.apple.com/developer-id/)
