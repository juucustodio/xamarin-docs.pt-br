---
title: Etapa 2. Configurar o acesso ao serviço para o aplicativo móvel
description: Este documento descreve como fornecer um aplicativo Xamarin com acesso a um aplicativo do Azure protegido por Azure Active Directory.
ms.prod: xamarin
ms.assetid: 8A14A457-F72E-4B08-B4B6-801F7619F893
author: conceptdev
ms.author: crdun
ms.date: 03/23/2017
ms.openlocfilehash: ec5dd15ffb838d7062c8c769375289e7b07b24d2
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70766369"
---
# <a name="step-2-configure-service-access-for-mobile-application"></a>Etapa 2. Configurar o acesso ao serviço para o aplicativo móvel

Sempre que qualquer recurso, por exemplo, aplicativo Web, serviço Web, etc. precisa ser protegido pelo Azure Active Directory, ele precisa ser registrado. Todos os aplicativos ou serviços seguros podem ser vistos na guia **aplicativos** . Aqui você pode selecionar o aplicativo que precisa ser acessado do aplicativo móvel e conceder acesso a ele.

1. Na guia **Configurar** , localize **a seção permissões para outros aplicativos** :

   ![](configure-images/2.1-configure.png "Na guia Configurar, localize a seção permissões para outros aplicativos")

2. Clique no botão **Adicionar aplicativo** . Na próxima tela pop-up, você deverá ver a lista de todos os aplicativos que são protegidos pelo Azure Active Directory. Selecione os aplicativos que precisam ser acessados do aplicativo móvel.

   ![](configure-images/2.2-add-application.png "Selecione os aplicativos que precisam ser acessados do aplicativo móvel")

3. Depois de selecionar o aplicativo, mais uma vez, selecione o aplicativo recém-adicionado na seção **permissões para outros aplicativos** e forneça os direitos apropriados.

   ![](configure-images/2.3-permissions.png "Depois de selecionar o aplicativo, mais uma vez, selecione o aplicativo recém-adicionado na seção permissões para outros aplicativos e dê direitos apropriados")

4. Por fim, **salve** a configuração. Esses serviços agora devem estar disponíveis em aplicativos móveis!

## <a name="related-links"></a>Links relacionados

- [Exemplo do Microsoft NativeClient](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
