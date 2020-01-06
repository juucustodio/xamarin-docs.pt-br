---
title: Agrupamento para Mac App Store
description: Este documento descreve como agrupar um aplicativo Xamarin.Mac para publicação na Mac App Store. Ele aborda as opções de assinatura de código e de criação.
ms.prod: xamarin
ms.assetid: 00a36d7c-937d-4657-bf6a-0de9684b8f94
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 7d2cd650dd22cd64d506d5d17a5ae0c34b44fc2b
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487172"
---
# <a name="bundling-for-the-mac-app-store"></a>Agrupamento para Mac App Store

Esta seção descreve as noções básicas da criação de um aplicativo para lançamento no Mac App Store usando o Visual Studio para Mac. Com base nos recursos adicionais (como acesso iCloud e notificações por push), outras instalações, que vão além do escopo deste artigo, podem ser necessárias.

> [!NOTE]
> Antes de iniciar esta seção, o desenvolvedor deve ter criado um perfil de provisionamento de produção para compilar para a Mac App Store. Consulte as [instruções de perfil](profiles.md) para criar os perfis de provisionamento necessários.

## <a name="code-signing-options"></a>Opções de assinatura de código

Altere **Configuração** para **Versão** antes de atualizar as opções de assinatura de código e empacotamento. O desenvolvedor precisa garantir que usem a **identidade** e o perfil de provisionamento criados acima quando assinarem o aplicativo para lançamento na App Store.

[![editar as opções de assinatura de código](bundling-images/sign.png)](bundling-images/sign-large.png#lightbox)

Verifique se a opção de criar um pacote do instalador foi marcada nas configurações de **Build do Mac**:

[![Editando as opções de compilação](bundling-images/build.png "Editando as opções de compilação")](bundling-images/build-large.png#lightbox)

## <a name="build"></a>{1&gt;Compilação&lt;1}

Antes de compilar, verifique se a configuração **Versão** foi selecionada. Quando o desenvolvedor cria o aplicativo, ele será solicitado _duas vezes_ (para usar os certificados do aplicativo e do instalador):

![Permitir que o aplicativo use o certificado será exibido duas vezes](bundling-images/perms02.png)

Depois que o aplicativo tiver sido criado, o desenvolvedor poderá clicar com o botão direito do mouse no projeto e escolher **revelar no Finder** para localizar o arquivo de pacote (no diretório `bin/Release/AppStore` no exemplo mostrado abaixo).  Este arquivo de pacote inclui um instalador para o aplicativo que pode ser enviado para a Apple para inclusão na Mac App Store.

> [!div class="mx-imgBorder"]
> ![selecionando o pacote de compilação no Finder](bundling-images/path.png)

## <a name="related-links"></a>Links relacionados

- [Instalação](/visualstudio/mac/installation/)
- [Amostra do Hello, Mac](~/mac/get-started/hello-mac.md)
- [Distribua aplicativos na Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [ID de Desenvolvedor e GateKeeper](https://developer.apple.com/resources/developer-id/)
