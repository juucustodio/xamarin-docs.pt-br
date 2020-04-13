---
title: Agrupamento para Mac App Store
description: Este documento descreve como agrupar um aplicativo Xamarin.Mac para publicação na Mac App Store. Ele aborda as opções de assinatura de código e de criação.
ms.prod: xamarin
ms.assetid: 00a36d7c-937d-4657-bf6a-0de9684b8f94
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: f4d38bb66a34257c1e0a27c5fbbfe16f59743e83
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "76725500"
---
# <a name="bundling-for-the-mac-app-store"></a>Agrupamento para Mac App Store

Esta seção descreve as noções básicas da criação de um aplicativo para lançamento no Mac App Store usando o Visual Studio para Mac. Com base nos recursos adicionais (como acesso iCloud e notificações por push), outras instalações, que vão além do escopo deste artigo, podem ser necessárias.

> [!NOTE]
> Antes de iniciar esta seção, o desenvolvedor deve ter criado um perfil de provisionamento de produção para compilar para a Mac App Store. Consulte as [instruções de perfil](profiles.md) para criar os perfis de provisionamento necessários.

## <a name="code-signing-options"></a>Opções de assinatura de código

Altere **Configuração** para **Versão** antes de atualizar as opções de assinatura de código e empacotamento. O desenvolvedor precisa garantir que usem a **identidade** e o perfil de provisionamento criados acima quando assinarem o aplicativo para lançamento na App Store.

[![Edição das opções de assinatura de código](bundling-images/sign.png)](bundling-images/sign-large.png#lightbox)

Verifique se a opção de criar um pacote do instalador foi marcada nas configurações de **Build do Mac**:

[![Editar as opções de build](bundling-images/build.png "Editar as opções de build")](bundling-images/build-large.png#lightbox)

## <a name="build"></a>Build

Antes de compilar, verifique se a configuração **Versão** foi selecionada. Quando o desenvolvedor compila o aplicativo, ele é solicitado _duas vezes_ (para usar o certificado do aplicativo e o certificado de instalação):

![Permitir que o aplicativo use o certificado, será exibido duas vezes](bundling-images/perms02.png)

Depois que o aplicativo tiver sido compilado, o desenvolvedor poderá clicar com o botão direito do mouse no projeto e escolher **Revelar no Localizador** para localizar o arquivo do pacote (no diretório `bin/Release/AppStore` no exemplo mostrado abaixo).  Este arquivo de pacote inclui um instalador para o aplicativo que pode ser enviado para a Apple para inclusão na Mac App Store.

> [!div class="mx-imgBorder"]
> ![Selecionar o pacote de build no Finder](bundling-images/path.png)

## <a name="related-links"></a>Links relacionados

- [Instalação](/visualstudio/mac/installation/)
- [Olá, amostra mac](~/mac/get-started/hello-mac.md)
- [Distribua aplicativos na Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [ID de Desenvolvedor e GateKeeper](https://developer.apple.com/developer-id/)
