---
title: Agrupamento para Mac App Store
description: Este documento descreve como agrupar um aplicativo Xamarin.Mac para publicação na Mac App Store. Ele aborda as opções de assinatura de código e de criação.
ms.prod: xamarin
ms.assetid: 00a36d7c-937d-4657-bf6a-0de9684b8f94
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 04ca9c98abbd97cd9e5d1f7694264b8316a7f151
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73001560"
---
# <a name="bundling-for-the-mac-app-store"></a>Agrupamento para Mac App Store

Esta seção descreve as noções básicas da criação de um aplicativo para lançamento no Mac App Store usando o Visual Studio para Mac. Com base nos recursos adicionais (como acesso iCloud e notificações por push), outras instalações, que vão além do escopo deste artigo, podem ser necessárias.

> [!NOTE]
> Antes de iniciar esta seção, o desenvolvedor deve ter criado um perfil de provisionamento de produção para compilar para a Mac App Store. Consulte as instruções anteriores neste documento sobre como criar os perfis de provisionamento necessários.

## <a name="code-signing-options"></a>Opções de assinatura de código

Altere **Configuração** para **Versão** antes de atualizar as opções de assinatura de código e empacotamento. O desenvolvedor precisa garantir que usem a **identidade** e o perfil de provisionamento criados acima quando assinarem o aplicativo para lançamento na App Store.

 [![Editando as opções de assinatura de código](bundling-images/config02.png "Editando as opções de assinatura de código")](bundling-images/config02-large.png#lightbox)

Verifique se a opção de criar um pacote do instalador foi marcada nas configurações de **Build do Mac**:

[![Editando as opções de compilação](bundling-images/config03.png "Editando as opções de compilação")](bundling-images/config03-large.png#lightbox)

## <a name="build"></a>Build

Antes de compilar, verifique se a configuração **Versão** foi selecionada. Quando o desenvolvedor compila o aplicativo, é solicitado a usar ambos os certificados:

 ![Permitindo que o aplicativo use o certificado](bundling-images/image62.png "Permitindo que o aplicativo use o certificado")

 ![Permitindo que o aplicativo use o certificado](bundling-images/image63.png "Permitindo que o aplicativo use o certificado")

Depois que o aplicativo é compilado, o desenvolvedor pode clicar com o botão direito no projeto e escolher **Abrir Pasta Recipiente** para localizar o arquivo do pacote (no diretório `bin/x86/AppStore` no exemplo mostrado abaixo).  Este arquivo de pacote inclui um instalador para o aplicativo que pode ser enviado para a Apple para inclusão na Mac App Store.

 ![Selecionando o pacote de compilação no localizador](bundling-images/image64.png "Selecionando o pacote de compilação no localizador")

## <a name="related-links"></a>Links relacionados

- [Instalação](/visualstudio/mac/installation/)
- [Amostra do Hello, Mac](~/mac/get-started/hello-mac.md)
- [Distribua aplicativos na Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [ID de Desenvolvedor e GateKeeper](https://developer.apple.com/resources/developer-id/)
