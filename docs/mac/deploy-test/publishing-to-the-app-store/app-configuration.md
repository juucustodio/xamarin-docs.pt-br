---
title: "Configuração do aplicativo Mac"
description: "Este guia explica como configurar um aplicativo Xamarin.Mac para publicação."
ms.topic: article
ms.prod: xamarin
ms.assetid: fea66a34-1581-4cd6-b714-3fbff215a542
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/12/2017
ms.openlocfilehash: 8f9294c10f8d3287a2985ede9aadf84ce663c38a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="mac-app-configuration"></a>Configuração do aplicativo Mac

_Este guia explica como configurar um aplicativo Xamarin.Mac para publicação._


## <a name="mac-app-configuration"></a>Configuração do aplicativo Mac

Clique com botão direito do mouse no projeto do aplicativo Mac no Visual Studio para Mac e escolha **Opções**.


### <a name="application-settings"></a>Configurações de aplicativo

Para alterar as configurações de aplicativo de um aplicativo Xamarin.Mac, clique duas vezes no arquivo **Info.plist** no **Painel de Soluções**:

![Selecionar o arquivo Info.plist](app-configuration-images/config04.png "Selecionar o arquivo Info.plist")

Isso exibirá as opções disponíveis para o aplicativo:

 [![Editar o arquivo Info.plist](app-configuration-images/config01.png "Editar o arquivo Info.plist")](app-configuration-images/config01-large.png)

Aplicativos Mac em execução criados com Xamarin.Mac têm os seguintes requisitos do sistema:

- Um computador Mac executando o Mac OS X 10.7 ou posterior.


### <a name="signing-settings"></a>Configurações de assinatura

A seção **Assinatura do Mac** da caixa de diálogo **Opções de Projeto** permite que o desenvolvedor assine um aplicativo Xamarin.Mac para teste, para versão própria ou por meio da Apple App Store:

[![O editor de Assinatura do Mac](app-configuration-images/config02.png "A janela de Assinatura do Mac")](app-configuration-images/config02-large.png)

Aqui, selecione a Identidade, o Perfil de provisionamento e quaisquer direitos personalizados usados para assinar o aplicativo quando ele é compilado. O desenvolvedor pode, opcionalmente, assinar o instalador usado para instalar o aplicativo em outro Mac.


### <a name="build-settings"></a>Configurações de build

A seção **Build do Mac** da caixa de diálogo **Opções de Projeto** permite que o desenvolvedor selecione a arquitetura de um aplicativo Xamarin.Mac, para controlar a qual versão do macOS o aplicativo dará suporte e, opcionalmente, para criar um pacote de instalação quando o aplicativo é compilado com êxito:

 [![Editar as configurações de build](app-configuration-images/config03.png "Editar as configurações de build")](app-configuration-images/config03-large.png)


## <a name="related-links"></a>Links relacionados

- [Instalação](/visualstudio/mac/installation/)
- [Amostra do Hello, Mac](~/mac/get-started/hello-mac.md)
- [Distribua aplicativos na Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [ID de Desenvolvedor e GateKeeper](https://developer.apple.com/resources/developer-id/)
