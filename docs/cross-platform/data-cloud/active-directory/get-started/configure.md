---
title: Etapa 2. Configurar o acesso ao serviço para o aplicativo móvel
description: Este documento descreve como fornecer um aplicativo Xamarin com acesso a um aplicativo do Azure protegido por Azure Active Directory.
ms.prod: xamarin
ms.assetid: 8A14A457-F72E-4B08-B4B6-801F7619F893
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: eeac7b0b70b2f11304a374de7522f28d4bcad6c6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016662"
---
# <a name="step-2-configure-service-access-for-mobile-application"></a>Etapa 2. Configurar o acesso ao serviço para o aplicativo móvel

Sempre que qualquer recurso, por exemplo, aplicativo Web, serviço Web, etc. precisa ser protegido pelo Azure Active Directory, ele precisa ser registrado. Todos os aplicativos ou serviços seguros podem ser vistos na guia **aplicativos** . Aqui você pode selecionar o aplicativo que precisa ser acessado do aplicativo móvel e conceder acesso a ele.

1. Na guia **Configurar** , localize **a seção permissões para outros aplicativos** :

   ![](configure-images/2.1-configure.png "On the Configure tab, locate permissions to other applications section")

2. Clique no botão **Adicionar aplicativo** . Na próxima tela pop-up, você deverá ver a lista de todos os aplicativos que são protegidos pelo Azure Active Directory. Selecione os aplicativos que precisam ser acessados do aplicativo móvel.

   ![](configure-images/2.2-add-application.png "Select the applications that needs to be accessed from the mobile application")

3. Depois de selecionar o aplicativo, mais uma vez, selecione o aplicativo recém-adicionado na seção **permissões para outros aplicativos** e forneça os direitos apropriados.

   ![](configure-images/2.3-permissions.png "After selecting the application, once again select the newly-added application in permissions to other   applications section and give appropriate rights")

4. Por fim, **salve** a configuração. Esses serviços agora devem estar disponíveis em aplicativos móveis!

## <a name="related-links"></a>Links relacionados

- [Exemplo do Microsoft NativeClient](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
