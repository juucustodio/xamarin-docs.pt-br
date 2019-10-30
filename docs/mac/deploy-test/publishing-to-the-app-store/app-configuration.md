---
title: Configuração do aplicativo Mac
description: Este documento descreve como configurar um aplicativo Xamarin.Mac para publicação. Ele aborda as configurações do aplicativo, as configurações de assinatura e as configurações de build.
ms.prod: xamarin
ms.assetid: fea66a34-1581-4cd6-b714-3fbff215a542
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 04/12/2017
ms.openlocfilehash: 545c1cef26d3bbf85b490492347f4f63b42269a9
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021691"
---
# <a name="mac-app-configuration"></a>Configuração do aplicativo Mac

## <a name="mac-app-configuration"></a>Configuração do aplicativo Mac

Clique com botão direito do mouse no projeto do aplicativo Mac no Visual Studio para Mac e escolha **Opções**.

### <a name="application-settings"></a>Configurações de aplicativo

Para alterar as configurações de aplicativo de um aplicativo Xamarin.Mac, clique duas vezes no arquivo **Info.plist** no **Painel de Soluções**:

![Selecionando o arquivo info. plist](app-configuration-images/config04.png "Selecionando o arquivo info. plist")

Isso exibirá as opções disponíveis para o aplicativo:

 [![Editando o arquivo info. plist](app-configuration-images/config01.png "Editando o arquivo info. plist")](app-configuration-images/config01-large.png#lightbox)

Aplicativos Mac em execução criados com Xamarin.Mac têm os seguintes requisitos do sistema:

- Um computador Mac executando o Mac OS X 10.7 ou posterior.

### <a name="signing-settings"></a>Configurações de assinatura

A seção **Assinatura do Mac** da caixa de diálogo **Opções de Projeto** permite que o desenvolvedor assine um aplicativo Xamarin.Mac para teste, para versão própria ou por meio da Apple App Store:

[![O editor de assinatura do Mac](app-configuration-images/config02.png "A janela de assinatura do Mac")](app-configuration-images/config02-large.png#lightbox)

Aqui, selecione a Identidade, o Perfil de provisionamento e quaisquer direitos personalizados usados para assinar o aplicativo quando ele é compilado. O desenvolvedor pode, opcionalmente, assinar o instalador usado para instalar o aplicativo em outro Mac.

### <a name="build-settings"></a>Configurações de build

A seção **Build do Mac** da caixa de diálogo **Opções de Projeto** permite que o desenvolvedor selecione a arquitetura de um aplicativo Xamarin.Mac, para controlar a qual versão do macOS o aplicativo dará suporte e, opcionalmente, para criar um pacote de instalação quando o aplicativo é compilado com êxito:

 [![Editando as configurações de compilação](app-configuration-images/config03.png "Editando as configurações de compilação")](app-configuration-images/config03-large.png#lightbox)

## <a name="related-links"></a>Links relacionados

- [Instalação](/visualstudio/mac/installation/)
- [Amostra do Hello, Mac](~/mac/get-started/hello-mac.md)
- [Distribua aplicativos na Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [ID de Desenvolvedor e GateKeeper](https://developer.apple.com/resources/developer-id/)
