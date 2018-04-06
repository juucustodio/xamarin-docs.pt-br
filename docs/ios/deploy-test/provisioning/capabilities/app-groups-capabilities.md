---
title: Funcionalidades do Grupo de aplicativos
description: A adição de capacidades a um aplicativo geralmente requer uma configuração de provisionamento adicional. Este guia explica a configuração necessária para as funcionalidades do Grupo de aplicativos.
ms.prod: xamarin
ms.assetid: 0A61220B-BBAC-492B-9D3B-578986E64064
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/15/2017
ms.openlocfilehash: f0c6cb1a080e05cfa60af72c4140f7109e3c37b4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="app-group-capabilities"></a>Funcionalidades do Grupo de aplicativos

_A adição de funcionalidades a um aplicativo geralmente requer uma configuração de provisionamento adicional. Este guia explica a configuração necessária para as funcionalidades do Grupo de aplicativos._

Um grupo de aplicativos permite que diferentes aplicativos (ou um aplicativo e suas extensões) acessem um local de armazenamento de arquivo compartilhado. Grupos de aplicativo podem ser usados para dados como:

*   [Configurações do Apple Watch](~/ios/watchos/app-fundamentals/settings.md)
*   [NSUserDefaults compartilhado](~/ios/app-fundamentals/user-defaults.md)
*   [Arquivos compartilhados](~/ios/watchos/app-fundamentals/parent-app.md#files)

## <a name="configure-a-new-app-group"></a>Configurar um novo grupo de aplicativos

O local compartilhado é configurado usando um [Grupo de Aplicativos](https://developer.apple.com/library/content/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19), que é configurado na seção **Certificados, Identificadores e Perfis** na [Central de Desenvolvedores da Apple](https://developer.apple.com/account/). Esse valor também deve ser referenciado em cada projeto Entitlements.plist.

O grupo de aplicativos terá um identificador, que normalmente é a ID do lote com um prefixo group. Por exemplo, a ID do pacote `com.xamarin.WatchSettings` teria o grupo de aplicativos `group.com.xamarin.WatchSettings`.

Para criar um novo grupo de aplicativos, faça o seguinte:

1.  Visite a [Central de Desenvolvedores do iOS](https://developer.apple.com/account/) da Apple, abra sua **Conta** e faça logon.
2.  Selecione **Certificados, IDs e Perfis**.
3.  Em **Identificadores** selecione **Grupos de Aplicativos** e clique no botão **+** para criar um novo grupo.
4.  Insira um **Nome** e um **Identificador** para o novo grupo e clique no botão **Continuar**: 
   
    ![Adicionar detalhes do grupo de aplicativos](app-groups-capabilities-images/image52.png)

5.  Clique no botão **Register (Registrar)** para criar o grupo e em **Done (Concluído)** para retornar à lista de grupos de aplicativos registrados.

## <a name="configure-an-app-to-use-app-groups"></a>Configurar um aplicativo para usar grupo de aplicativos

Com o grupo de aplicativos criado, configure as IDs do aplicativo para que os aplicativos possam usá-la.

Faça o seguinte:

1.  Visite a [Central de Desenvolvedores do iOS](https://developer.apple.com/account/) da Apple e faça logon com uma conta de desenvolvedor da Apple.
2.  No menu **Recursos do Programa**, selecione **Certificados, IDs e Perfis**.
3.  Em **Identifiers (Identificadores)**, selecione **App IDs (IDs do Aplicativo)** e clique no botão **+** para criar uma nova ID.
4.  Insira um nome para a ID do aplicativo e dê a ela uma ID do aplicativo explícita.
5.  Em **Serviços de Aplicativos** habilite **Grupos de Aplicativos** e, em seguida, clique no botão Continuar:

    ![Adicionar Serviços de Aplicativos no grupo de aplicativos](app-groups-capabilities-images/image53.png)

6.  Verifique as configurações e clique no botão **Registrar** para criar a ID do aplicativo.
7.  Clique no botão **Done (Concluído)** para retornar à lista de IDs do aplicativo registradas.
8.  Selecione a ID do aplicativo recém-criada na lista e clique no botão **Edit (Editar)**:

    ![Selecionar a ID do aplicativo na lista](app-groups-capabilities-images/image54.png)

9.  No serviço **App Group (Grupo de Aplicativos)**, clique no botão **Edit (Editar)**:

    ![Selecionar a ID do aplicativo na lista](app-groups-capabilities-images/image55.png)

10. Selecione o grupo de aplicativo que foi criado anteriormente e clique no botão **Continue (Continuar)**:

    ![Adicionar grupo de aplicativos](app-groups-capabilities-images/image56.png)

11. Clique no botão **Assign (Atribuir)** e então no botão **Done (Concluído)** para retornar à lista de IDs do aplicativo registradas.
12. Repita essas etapas para todos os aplicativos (ou extensões) que usarão o grupo de aplicativos.

## <a name="next-steps"></a>Próximas etapas
 
A lista a seguir descreve as etapas adicionais que precisam ser executadas:

* Use o namespace do framework em seu aplicativo.
* Adicione os direitos necessários para seu aplicativo. As informações sobre os direitos necessários e como adicioná-los estão detalhadas no guia [Trabalhando com direitos](~/ios/deploy-test/provisioning/entitlements.md).
* Na **Assinatura de Pacote do iOS** do aplicativo, certifique-se de que **Direitos Personalizados** está definido como **Entitlements.plist**. Esta _não_ é a configuração padrão para builds de depuração e do simulador do iOS.

Se você encontrar problemas com serviços de aplicativos, consulte a seção [Solução de problemas](~/ios/deploy-test/provisioning/capabilities/index.md) do guia principal.