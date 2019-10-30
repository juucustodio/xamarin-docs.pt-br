---
title: Etapa 1. Registrar um aplicativo para usar Azure Active Directory
description: Este documento descreve como registrar um aplicativo do Azure com o Azure Active Directory para que ele possa ser acessado com segurança por clientes móveis.
ms.prod: xamarin
ms.assetid: 0B17991A-4573-4F6C-9E86-D4B9D1A47E4D
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: c3b93ec4fa4ec2ffad9db26414c2453ba7281974
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016659"
---
# <a name="step-1-register-an-app-to-use-azure-active-directory"></a>Etapa 1. Registrar um aplicativo para usar Azure Active Directory

1. Navegue até [WindowsAzure.com](https://manage.windowsazure.com) e faça logon com sua conta da Microsoft ou conta da organização no portal do Azure. Se você não tiver uma assinatura do Azure, poderá obter uma avaliação do [Azure.com](https://www.azure.com)

2. Depois de entrar, vá para a seção **Active Directory** (1) e escolha o diretório no qual você deseja registrar o aplicativo (2)

   [![](register-images/01.-active-directory-in-azure-portal-sml.jpg "section and choose the directory where you want to register the application")](register-images/01.-active-directory-in-azure-portal.jpg#lightbox)

3. Clique em **Adicionar** para criar um novo aplicativo e, em seguida, selecione **Adicionar um aplicativo que minha organização está desenvolvendo**

   [![](register-images/02.-add-new-application-sml.jpg "Add an application my organization is developing")](register-images/02.-add-new-application.jpg#lightbox)

4. Na próxima tela, dê um nome ao seu aplicativo (por exemplo, XAM-DEMONSTRAÇÃO).
   Certifique-se de selecionar **aplicativo cliente nativo** como o tipo de aplicativo.

   ![](register-images/03.-app-name.jpg "Make sure you select Native Client Application as the type of application")

5. Na tela final, forneça um URI de*redirecionamento* * que seja exclusivo para seu aplicativo, pois ele retornará a esse URI quando a autenticação for concluída.

   ![](register-images/04.-app-redirect.jpg "On the final screen, provide a Redirect URI that is unique to your application as it will return to this URI when   authentication is complete")

6. Depois que o aplicativo for criado, navegue até a guia **Configurar** . Anote a **ID do cliente** que usaremos em nosso aplicativo mais tarde. Além disso, nessa tela, você pode dar ao seu aplicativo móvel acesso a Active Directory ou adicionar outro aplicativo, como a API Web ou o Office365, que pode ser usado pelo aplicativo móvel quando a autenticação for concluída.

   ![](register-images/05.-configure.jpg "Also, on this screen you can give your mobile application access to Active Directory or add another application like Web API or Office365")

## <a name="related-links"></a>Links relacionados

- [Exemplo do Microsoft NativeClient](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
