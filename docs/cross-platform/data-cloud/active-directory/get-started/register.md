---
title: Etapa 1. Registrar um aplicativo para usar Azure Active Directory
description: Este documento descreve como registrar um aplicativo do Azure com o Azure Active Directory para que ele possa ser acessado com segurança por clientes móveis.
ms.prod: xamarin
ms.assetid: 0B17991A-4573-4F6C-9E86-D4B9D1A47E4D
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 6b10b2fd489881292d95bbf79375a6488aa88a17
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86932531"
---
# <a name="step-1-register-an-app-to-use-azure-active-directory"></a>Etapa 1. Registrar um aplicativo para usar Azure Active Directory

1. Navegue até [WindowsAzure.com](https://manage.windowsazure.com) e faça logon com sua conta da Microsoft ou conta da organização no portal do Azure. Se você não tiver uma assinatura do Azure, poderá obter uma avaliação do [Azure.com](https://www.azure.com)

2. Depois de entrar, vá para a seção **Active Directory** (1) e escolha o diretório no qual você deseja registrar o aplicativo (2)

   [![e escolha o diretório no qual você deseja registrar o aplicativo](register-images/01.-active-directory-in-azure-portal-sml.jpg)](register-images/01.-active-directory-in-azure-portal.jpg#lightbox)

3. Clique em **Adicionar** para criar um novo aplicativo e, em seguida, selecione **Adicionar um aplicativo que minha organização está desenvolvendo**

   [![Adicionar um aplicativo que minha organização esteja desenvolvendo](register-images/02.-add-new-application-sml.jpg)](register-images/02.-add-new-application.jpg#lightbox)

4. Na próxima tela, dê um nome ao seu aplicativo (por exemplo, XAM-DEMONSTRAÇÃO).
   Certifique-se de selecionar **aplicativo cliente nativo** como o tipo de aplicativo.

   ![Certifique-se de selecionar aplicativo cliente nativo como o tipo de aplicativo](register-images/03.-app-name.jpg)

5. Na tela final, forneça um URI de*redirecionamento* * que seja exclusivo para seu aplicativo, pois ele retornará a esse URI quando a autenticação for concluída.

   ![Na tela final, forneça um URI de redirecionamento que seja exclusivo para seu aplicativo, pois ele retornará a esse URI quando a autenticação for concluída](register-images/04.-app-redirect.jpg)

6. Depois que o aplicativo for criado, navegue até a guia **Configurar** . Anote a **ID do cliente** que usaremos em nosso aplicativo mais tarde. Além disso, nessa tela, você pode dar ao seu aplicativo móvel acesso a Active Directory ou adicionar outro aplicativo, como a API Web ou o Office365, que pode ser usado pelo aplicativo móvel quando a autenticação for concluída.

   ![Além disso, nesta tela, você pode dar acesso ao seu aplicativo móvel para Active Directory ou adicionar outro aplicativo como a API Web ou o Office365](register-images/05.-configure.jpg)

## <a name="related-links"></a>Links Relacionados

- [Exemplo do Microsoft NativeClient](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
