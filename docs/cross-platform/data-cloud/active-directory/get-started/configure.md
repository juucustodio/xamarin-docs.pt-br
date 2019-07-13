---
title: Etapa 2. Configurar o acesso ao serviço de aplicativo móvel
description: Este documento descreve como fornecer um aplicativo Xamarin com acesso a um aplicativo do Azure protegido pelo Azure Active Directory.
ms.prod: xamarin
ms.assetid: 8A14A457-F72E-4B08-B4B6-801F7619F893
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: e0015316b7be3462982ee0959862250c0c27dc74
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864653"
---
# <a name="step-2-configure-service-access-for-mobile-application"></a>Etapa 2. Configurar o acesso ao serviço de aplicativo móvel

Sempre que qualquer recurso, por exemplo, o aplicativo web, o serviço web, etc. precisa ser protegido pelo Azure Active Directory, ele precisa ser registrado. Todos os serviços ou aplicativos seguros que podem ser vistos sob **aplicativos** guia. Aqui você pode selecionar o aplicativo que precisa ser acessado de aplicativos móveis e conceder acesso a ele.

1. Sobre o **configurar** guia, localize **permissões para outros aplicativos** seção:

   ![](configure-images/2.1-configure.png "Na guia Configurar, localize a seção permissões para outros aplicativos")

2. Clique em **Adicionar aplicativo** botão. Na próxima tela pop-up, você verá a lista de todos os aplicativos que são protegidos pelo Azure Active Directory. Selecione os aplicativos que precisam ser acessados a partir do aplicativo móvel.

   ![](configure-images/2.2-add-application.png "Selecione os aplicativos que precisam ser acessados a partir do aplicativo móvel")

3. Depois de selecionar o aplicativo, selecione novamente o aplicativo recentemente adicionado no **permissões para outros aplicativos** seção e conceder os direitos apropriados.

   ![](configure-images/2.3-permissions.png "Depois de selecionar o aplicativo, mais uma vez selecione o aplicativo recentemente adicionado na seção permissões para outros aplicativos e conceder os direitos apropriados")

4. Por fim, **salvar** a configuração. Esses serviços agora devem estar disponíveis em aplicativos móveis!



## <a name="related-links"></a>Links relacionados

- [Exemplo de Microsoft NativeClient](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
