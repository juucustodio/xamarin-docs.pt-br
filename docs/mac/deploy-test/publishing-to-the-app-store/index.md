---
title: Publicando aplicativos Xamarin.Mac na Mac App Store
description: Este documento descreve como implantar um aplicativo Xamarin.Mac com o Visual Studio para Mac. Ele explica como configurar uma Conta de Desenvolvedor do Mac, como criar os certificados para assinatura de código e como usá-los para compilar aplicativos Mac que possam ser distribuídos diretamente ou por meio da Mac App Store.
ms.prod: xamarin
ms.assetid: D26C5E54-EAD2-5487-264D-4263AEA1EBF2
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: 84a35389276fed031f6bea116c1e4f37a63469a7
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769864"
---
# <a name="publishing-xamarinmac-apps-to-the-mac-app-store"></a>Publicando aplicativos Xamarin.Mac na Mac App Store

## <a name="overview"></a>Visão geral

Os aplicativos Xamarin.Mac podem ser distribuídos de duas maneiras diferentes:

- **ID de Desenvolvedor** – Os aplicativos assinados com uma de ID de Desenvolvedor podem ser distribuídos fora da App Store, mas são reconhecidos pelo GateKeeper e têm instalação permitida.
- **Mac App Store** – Os aplicativos devem ter um pacote de instalação e o aplicativo e o instalador devem ser assinados, para o envio para a Mac App Store.

Este documento explica como usar o Visual Studio para Mac e o Xcode para configurar uma conta de Desenvolvedor da Apple e configurar um projeto Xamarin.Mac para cada tipo de implantação.

## <a name="mac-developer-program"></a>Programa de Desenvolvedores do Mac

Ao ingressar no [Programa de Desenvolvedores do Mac](https://developer.apple.com/devcenter/mac/) será oferecido ao desenvolvedor uma opção para participar como uma Pessoa Física ou como uma Empresa, conforme mostrado na captura de tela abaixo:

[![O Portal do Desenvolvedor da Apple](images/image1.png "O Portal do Desenvolvedor da Apple")](images/image1-large.png#lightbox)

Escolha o tipo de registro correto para a sua situação.

> [!NOTE]
> As escolhas feitas aqui afetarão a forma como algumas telas aparecem ao configurar uma conta de desenvolvedor. As descrições e capturas de tela neste documento são feitas da perspectiva de uma conta de desenvolvedor de **Pessoa Física**. Em uma **Empresa**, algumas opções só estarão disponíveis para usuários **Administradores de Equipe**.

### <a name="certificates-and-identifiersmacdeploy-testpublishing-to-the-app-storecertificates-identifiersmd"></a>[Certificados e Identificadores](~/mac/deploy-test/publishing-to-the-app-store/certificates-identifiers.md)

Esse guia o orienta durante a criação dos Certificados e Identificadores que serão necessários para publicar um aplicativo Xamarin.Mac.

### <a name="create-provisioning-profilemacdeploy-testpublishing-to-the-app-storeprofilesmd"></a>[Criar Perfil de Provisionamento](~/mac/deploy-test/publishing-to-the-app-store/profiles.md)

Esse guia o orienta na criação dos Perfis de Provisionamento que serão necessários para publicar um aplicativo Xamarin.Mac.

### <a name="mac-app-configurationmacdeploy-testpublishing-to-the-app-storeapp-configurationmd"></a>[Configuração do aplicativo Mac](~/mac/deploy-test/publishing-to-the-app-store/app-configuration.md)

Este guia explica como configurar um aplicativo Xamarin.Mac para publicação.

### <a name="sign-with-developer-idmacdeploy-testpublishing-to-the-app-storesigningmd"></a>[Entrar com a ID de desenvolvedor](~/mac/deploy-test/publishing-to-the-app-store/signing.md)

Este guia explica como assinar um aplicativo Xamarin.Mac com a ID de Desenvolvedor para publicação.

### <a name="bundle-for-mac-app-storemacdeploy-testpublishing-to-the-app-storebundlingmd"></a>[Pacote para Mac App Store](~/mac/deploy-test/publishing-to-the-app-store/bundling.md)

Este guia ensina como agrupar um aplicativo Xamarin.Mac para publicação na Mac App Store.

### <a name="upload-to-mac-app-storemacdeploy-testpublishing-to-the-app-storeuploadingmd"></a>[Carregar na Mac App Store](~/mac/deploy-test/publishing-to-the-app-store/uploading.md)

Este guia ensina como fazer upload de aplicativos Xamarin.Mac para publicação na Mac App Store.

## <a name="related-links"></a>Links relacionados

- [Instalação](/visualstudio/mac/installation/)
- [Amostra do Hello, Mac](~/mac/get-started/hello-mac.md)
- [ID de Desenvolvedor e GateKeeper](https://developer.apple.com/resources/developer-id/)
