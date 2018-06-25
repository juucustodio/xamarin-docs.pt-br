---
title: Agrupamento para Mac App Store
description: Este documento descreve como agrupar um aplicativo Xamarin.Mac para publicação na Mac App Store. Ele aborda as opções de assinatura de código e de criação.
ms.prod: xamarin
ms.assetid: 00a36d7c-937d-4657-bf6a-0de9684b8f94
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 3aeda97fa4a5a63d51623335c0721026b2394d2f
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792054"
---
# <a name="bundling-for-the-mac-app-store"></a>Agrupamento para Mac App Store

Esta seção descreve as noções básicas da criação de um aplicativo para lançamento no Mac App Store usando o Visual Studio para Mac. Com base nos recursos adicionais (como acesso iCloud e notificações por push), outras instalações, que vão além do escopo deste artigo, podem ser necessárias.

> [!NOTE]
> Antes de iniciar esta seção, o desenvolvedor deve ter criado um perfil de provisionamento de produção para compilar para a Mac App Store. Consulte as instruções anteriores neste documento sobre como criar os perfis de provisionamento necessários.

## <a name="code-signing-options"></a>Opções de assinatura de código

Altere **Configuração** para **Versão** antes de atualizar as opções de assinatura de código e empacotamento. O desenvolvedor precisa garantir que usem a **identidade** e o perfil de provisionamento criados acima quando assinarem o aplicativo para lançamento na App Store.

 [![Editar as opções de assinatura de código](bundling-images/config02.png "Editar as opções de assinatura de código")](bundling-images/config02-large.png#lightbox)

Verifique se a opção de criar um pacote do instalador foi marcada nas configurações de **Build do Mac**:

[![Editar as opções de build](bundling-images/config03.png "Editar as opções de build")](bundling-images/config03-large.png#lightbox)

## <a name="build"></a>Build

Antes de compilar, verifique se a configuração **Versão** foi selecionada. Quando o desenvolvedor compila o aplicativo, é solicitado a usar ambos os certificados:

 ![Permitir que o aplicativo use o certificado](bundling-images/image62.png "Permitir que o aplicativo use o certificado")

 ![Permitir que o aplicativo use o certificado](bundling-images/image63.png "Permitir que o aplicativo use o certificado")

Depois que o aplicativo é compilado, o desenvolvedor pode clicar com o botão direito no projeto e escolher **Abrir Pasta Recipiente** para localizar o arquivo do pacote (no diretório `bin/x86/AppStore` no exemplo mostrado abaixo).  Este arquivo de pacote inclui um instalador para o aplicativo que pode ser enviado para a Apple para inclusão na Mac App Store.

 ![Selecionar o pacote de build no Localizador](bundling-images/image64.png "Selecionar o pacote de build no Localizador")


## <a name="related-links"></a>Links relacionados

- [Instalação](/visualstudio/mac/installation/)
- [Amostra do Hello, Mac](~/mac/get-started/hello-mac.md)
- [Distribua aplicativos na Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [ID de Desenvolvedor e GateKeeper](https://developer.apple.com/resources/developer-id/)
