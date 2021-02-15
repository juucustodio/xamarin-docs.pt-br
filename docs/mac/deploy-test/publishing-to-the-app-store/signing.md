---
title: Assinar aplicativos Xamarin.Mac com uma ID de Desenvolvedor
description: Este documento descreve como assinar um aplicativo Xamarin.Mac com uma ID de desenvolvedor para que ele possa ser distribuído fora da Mac App Store. Ele aborda as opções de assinatura de código e de criação.
ms.prod: xamarin
ms.assetid: cf7b733b-e08f-4f56-a233-264b29ee4c97
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 7e2d676b577e835bafe5cdd8d6bc48229a1a1a06
ms.sourcegitcommit: 513feb0e07558766e3de4a898e53d56b27c20559
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98697573"
---
# <a name="signing-xamarinmac-apps-with-a-developer-id"></a>Assinar aplicativos Xamarin.Mac com uma ID de Desenvolvedor

Se o desenvolvedor planeja distribuir um aplicativo diretamente aos usuários macOS, a Apple recomenda que a assinatura de código seja feita com sua ID de desenvolvedor para que ele possa ser instalado em sistemas macOS com o **GateKeeper** habilitado. Se o aplicativo não tiver sido assinado, o **GateKeeper** impedirá que os usuários instalem uma mensagem de alerta (eles podem ignorar essa restrição mantendo pressionada a tecla Control ao iniciar).

Leia mais sobre [ID de desenvolvedor e GateKeeper](https://developer.apple.com/developer-id/) e [Distribuindo fora da Mac App Store](https://developer.apple.com/library/content/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html) no site da Apple.

## <a name="code-signing-options"></a>Opções de assinatura de código

Para compilar um aplicativo para implantação diretamente no conjunto de usuários (NÃO por meio da Mac App Store), defina as **Configurações de Assinatura** para usar a **ID de Desenvolvedor**. Certifique-se de editar a configuração **Versão**.

 [![As opções de assinatura do Mac](signing-images/config02.png)](signing-images/config02.png#lightbox)

## <a name="build"></a>Build

Antes de compilar, verifique se a configuração correta foi selecionada e selecione para criar um pacote de instalação nas configurações do **Build do Mac**:

[![As opções de build](signing-images/config03.png)](signing-images/config03.png#lightbox)

Ao criar o aplicativo, o desenvolvedor será solicitado a usar ambos os certificados:

 [![Captura de tela mostra a caixa de diálogo permitir acesso para codesign.](signing-images/image57.png)](signing-images/image57.png#lightbox)

 [![Captura de tela mostra a caixa de diálogo permitir acesso para compilação do produto.](signing-images/image58.png)](signing-images/image58.png#lightbox)

Depois de compilar o aplicativo, o desenvolvedor pode clicar com o botão direito do mouse no projeto e escolher **Abrir Pasta Contendo** para localizar o arquivo do pacote (no diretório `bin/Release`). Esse arquivo de pacote inclui um instalador para o aplicativo, para que ele possa ser distribuído a qualquer usuário do macOS para instalação.

 [![Selecionando o pacote do aplicativo no Finder](signing-images/image59.png)](signing-images/image59.png#lightbox)

## <a name="related-links"></a>Links relacionados

- [Instalação](~//mac/get-started/installation.md)
- [Olá, exemplo de Mac](~//mac/get-started/hello-mac.md)
- [Distribua aplicativos na Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [Guia de ferramentas: assinatura de código do aplicativo](https://developer.apple.com/library/mac/#documentation/ToolsLanguages/Conceptual/OSXWorkflowGuide/CodeSigning/CodeSigning.html)
- [ID de Desenvolvedor e GateKeeper](https://developer.apple.com/developer-id/)
