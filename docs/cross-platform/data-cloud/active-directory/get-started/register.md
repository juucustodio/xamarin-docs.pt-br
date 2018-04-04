---
title: Etapa 1. Registrar um aplicativo para usar o Active Directory do Azure
ms.prod: xamarin
ms.assetid: 0B17991A-4573-4F6C-9E86-D4B9D1A47E4D
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: c9a44a35e91e6368522f8632e185bd8a554d8593
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="step-1-register-an-app-to-use-azure-active-directory"></a>Etapa 1. Registrar um aplicativo para usar o Active Directory do Azure

1. Navegue até [windowsazure.com](https://manage.windowsazure.com) e faça logon com sua conta de organização no Portal do Azure ou Account da Microsoft. Se você não tiver uma assinatura do Azure, você pode obter uma avaliação de [azure.com](http://www.azure.com)

2. Depois de entrar, vá para o **do Active Directory** seção (1) e escolha o diretório onde você deseja registrar o aplicativo (2)

  [ ![](register-images/01.-active-directory-in-azure-portal-sml.jpg "seção e escolha o diretório onde você deseja registrar o aplicativo")](register-images/01.-active-directory-in-azure-portal.jpg#lightbox)

3. Clique em **adicionar** para criar um novo aplicativo, em seguida, selecione **adicionar um aplicativo que minha organização esteja desenvolvendo**

  [ ![](register-images/02.-add-new-application-sml.jpg "Adicionar um aplicativo que minha organização esteja desenvolvendo")](register-images/02.-add-new-application.jpg#lightbox)

4. Na próxima tela, nomeie seu aplicativo (por exemplo. XAM-DEMO).
  Verifique se você selecionou **aplicativo cliente nativo** como o tipo de aplicativo.

  ![](register-images/03.-app-name.jpg "Certifique-se de que selecionar o aplicativo cliente nativo como o tipo de aplicativo")

5. Na tela final, forneça um **URI de redirecionamento* seja exclusivo para seu aplicativo como ele será retornada para esse URI quando a autenticação é concluída.

  ![](register-images/04.-app-redirect.jpg "Na tela final, forneça um URI de redirecionamento que é exclusivo para seu aplicativo como ele será retornada para esse URI quando a autenticação é concluída")

6. Quando o aplicativo é criado, navegue até o **configurar** guia. Anote o **ID do cliente** que usaremos nosso aplicativo mais tarde. Além disso, nessa tela, você pode conceder acesso ao seu aplicativo móvel para o Active Directory ou adicionar outro aplicativo, como a API da Web ou o Office 365, que pode ser usado pelo aplicativo móvel quando a autenticação é concluída.

    ![](register-images/05.-configure.jpg "Além disso, nessa tela você pode conceder acesso ao seu aplicativo móvel para o Active Directory ou adicionar outro aplicativo, como a API da Web ou o Office 365")



## <a name="related-links"></a>Links relacionados

- [Exemplo de NativeClient Microsoft](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
