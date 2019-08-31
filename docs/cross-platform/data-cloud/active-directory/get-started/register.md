---
title: Etapa 1. Registrar um aplicativo para usar Azure Active Directory
description: Este documento descreve como registrar um aplicativo do Azure com o Azure Active Directory para que ele possa ser acessado com segurança por clientes móveis.
ms.prod: xamarin
ms.assetid: 0B17991A-4573-4F6C-9E86-D4B9D1A47E4D
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 732c1ee241b39a4bb1422b8c27820631bf8c6b0c
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199055"
---
# <a name="step-1-register-an-app-to-use-azure-active-directory"></a>Etapa 1. Registrar um aplicativo para usar Azure Active Directory

1. Navegue até [WindowsAzure.com](https://manage.windowsazure.com) e faça logon com sua conta da Microsoft ou conta da organização no portal do Azure. Se você não tiver uma assinatura do Azure, poderá obter uma avaliação do [Azure.com](https://www.azure.com)

2. Depois de entrar, vá para a seção **Active Directory** (1) e escolha o diretório no qual você deseja registrar o aplicativo (2)

   [![](register-images/01.-active-directory-in-azure-portal-sml.jpg "e escolha o diretório no qual você deseja registrar o aplicativo")](register-images/01.-active-directory-in-azure-portal.jpg#lightbox)

3. Clique em **Adicionar** para criar um novo aplicativo e, em seguida, selecione **Adicionar um aplicativo que minha organização está desenvolvendo**

   [![](register-images/02.-add-new-application-sml.jpg "Adicionar um aplicativo que minha organização está desenvolvendo")](register-images/02.-add-new-application.jpg#lightbox)

4. Na próxima tela, dê um nome ao seu aplicativo (por exemplo, XAM-DEMONSTRAÇÃO).
   Certifique-se de selecionar **aplicativo cliente nativo** como o tipo de aplicativo.

   ![](register-images/03.-app-name.jpg "Certifique-se de selecionar aplicativo cliente nativo como o tipo de aplicativo")

5. Na tela final, forneça um*URI* de redirecionamento * que seja exclusivo para seu aplicativo, pois ele retornará a esse URI quando a autenticação for concluída.

   ![](register-images/04.-app-redirect.jpg "Na tela final, forneça um URI de redirecionamento que seja exclusivo para seu aplicativo, pois ele retornará a esse URI quando a autenticação for concluída")

6. Depois que o aplicativo for criado, navegue até a guia **Configurar** . Anote a **ID do cliente** que usaremos em nosso aplicativo mais tarde. Além disso, nessa tela, você pode dar ao seu aplicativo móvel acesso a Active Directory ou adicionar outro aplicativo, como a API Web ou o Office365, que pode ser usado pelo aplicativo móvel quando a autenticação for concluída.

   ![](register-images/05.-configure.jpg "Além disso, nesta tela, você pode dar acesso ao seu aplicativo móvel para Active Directory ou adicionar outro aplicativo como a API Web ou o Office365")



## <a name="related-links"></a>Links relacionados

- [Exemplo do Microsoft NativeClient](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
