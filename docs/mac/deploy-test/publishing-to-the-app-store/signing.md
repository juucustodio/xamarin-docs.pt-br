---
title: Entrar com a ID de desenvolvedor
description: "Este guia explica como assinar um aplicativo Xamarin.Mac com a ID do Desenvolvedor para publicação."
ms.topic: article
ms.prod: xamarin
ms.assetid: cf7b733b-e08f-4f56-a233-264b29ee4c97
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 46d5527a33b82a795029f62900e782d644671f0d
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="sign-with-developer-id"></a>Entrar com a ID de desenvolvedor

Se o desenvolvedor planeja distribuir um aplicativo diretamente aos usuários macOS, a Apple recomenda que a assinatura de código seja feita com sua ID de desenvolvedor para que ele possa ser instalado em sistemas macOS com o **GateKeeper** habilitado. Se o aplicativo não tiver sido assinado, o **GateKeeper** impedirá que os usuários instalem uma mensagem de alerta (eles podem ignorar essa restrição mantendo pressionada a tecla Control ao iniciar).

Leia mais sobre [ID de desenvolvedor e GateKeeper](https://developer.apple.com/resources/developer-id/) e [Distribuindo fora da Mac App Store](https://developer.apple.com/library/content/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html) no site da Apple.

## <a name="code-signing-options"></a>Opções de assinatura de código

Para compilar um aplicativo para implantação diretamente no conjunto de usuários (NÃO por meio da Mac App Store), defina as **Configurações de Assinatura** para usar a **ID de Desenvolvedor**. Certifique-se de editar a configuração **Versão**.

 [![](signing-images/config02.png "As opções de assinatura do Mac")](signing-images/config02.png#lightbox)


## <a name="build"></a>Build

Antes de compilar, verifique se a configuração correta foi selecionada e selecione para criar um pacote de instalação nas configurações do **Build do Mac**:

[![](signing-images/config03.png "As opções de build")](signing-images/config03.png#lightbox)

Ao criar o aplicativo, o desenvolvedor será solicitado a usar ambos os certificados:

 [![](signing-images/image57.png "Permitindo acesso ao conjunto de chaves")](signing-images/image57.png#lightbox)

 [![](signing-images/image58.png "Permitindo acesso ao conjunto de chaves")](signing-images/image58.png#lightbox)

Depois de compilar o aplicativo, o desenvolvedor pode clicar com o botão direito do mouse no projeto e escolher **Abrir Pasta Contendo** para localizar o arquivo do pacote (no diretório `bin/Release`). Esse arquivo de pacote inclui um instalador para o aplicativo, para que ele possa ser distribuído a qualquer usuário do macOS para instalação.

 [![](signing-images/image59.png "Selecionando o pacote do aplicativo no Finder")](signing-images/image59.png#lightbox)

## <a name="related-links"></a>Links relacionados

- [Instalação](~//mac/get-started/installation.md)
- [Amostra do Hello, Mac](~//mac/get-started/hello-mac.md)
- [Distribua aplicativos na Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [Guia de ferramentas: assinatura de código do aplicativo](https://developer.apple.com/library/mac/#documentation/ToolsLanguages/Conceptual/OSXWorkflowGuide/CodeSigning/CodeSigning.html)
- [ID de Desenvolvedor e GateKeeper](https://developer.apple.com/resources/developer-id/)
