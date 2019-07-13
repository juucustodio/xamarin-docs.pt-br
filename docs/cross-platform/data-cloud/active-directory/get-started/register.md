---
title: Etapa 1. Registrar um aplicativo para usar o Azure Active Directory
description: Este documento descreve como registrar um aplicativo do Azure com o Azure Active Directory para que possam ser acessado com segurança pelos clientes móveis.
ms.prod: xamarin
ms.assetid: 0B17991A-4573-4F6C-9E86-D4B9D1A47E4D
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 3e852e3e7ab3ac5db28052a47af1ebec4dbd2416
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864671"
---
# <a name="step-1-register-an-app-to-use-azure-active-directory"></a>Etapa 1. Registrar um aplicativo para usar o Azure Active Directory

1. Navegue até [windowsazure.com](https://manage.windowsazure.com) e faça logon com sua conta de organização no Portal do Azure ou Account da Microsoft. Se você não tiver uma assinatura do Azure, você pode obter uma avaliação de [azure.com](https://www.azure.com)

2. Depois de entrar, vá para o **do Active Directory** seção (1) e escolha o diretório onde você deseja registrar o aplicativo (2)

   [![](register-images/01.-active-directory-in-azure-portal-sml.jpg "seção e escolha o diretório onde você deseja registrar o aplicativo")](register-images/01.-active-directory-in-azure-portal.jpg#lightbox)

3. Clique em **Add** para criar um novo aplicativo, em seguida, selecione **adicionar um aplicativo que minha organização esteja desenvolvendo**

   [![](register-images/02.-add-new-application-sml.jpg "Adicionar um aplicativo que minha organização esteja desenvolvendo")](register-images/02.-add-new-application.jpg#lightbox)

4. Na próxima tela, nomeie seu aplicativo (por exemplo. XAM-DEMO).
   Verifique se você selecionou **aplicativo cliente nativo** como o tipo de aplicativo.

   ![](register-images/03.-app-name.jpg "Certifique-se de que selecionar o aplicativo cliente nativo como o tipo de aplicativo")

5. Na tela final, forneça um **URI de redirecionamento* que é exclusiva para seu aplicativo como ele retornarão para esse URI quando a autenticação é concluída.

   ![](register-images/04.-app-redirect.jpg "Na tela final, forneça um URI de redirecionamento que é exclusivo para seu aplicativo como ele retornarão para esse URI quando a autenticação é concluída")

6. Depois que o aplicativo é criado, navegue até a **configurar** guia. Anote o **ID do cliente** que usaremos em nosso aplicativo mais tarde. Além disso, nessa tela, você pode dar a seu aplicativo móvel o acesso ao Active Directory ou adicionar outro aplicativo, como a API da Web ou o Office 365, que pode ser usado pelo aplicativo móvel após a conclusão da autenticação.

     ![](register-images/05.-configure.jpg "Além disso, nessa tela você pode dar a seu aplicativo móvel o acesso ao Active Directory ou adicionar outro aplicativo, como a API da Web ou o Office 365")



## <a name="related-links"></a>Links relacionados

- [Exemplo de Microsoft NativeClient](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
