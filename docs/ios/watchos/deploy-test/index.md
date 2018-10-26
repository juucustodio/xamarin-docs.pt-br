---
title: Implantando e testando aplicativos watchOS com Xamarin
description: Este documento descreve como implantar e testar aplicativos watchOS criados com o Xamarin. Ele fornece uma lista de verificação de implantação, discute explícito e o aplicativo curinga IDs e examina o grupos de aplicativos.
ms.prod: xamarin
ms.assetid: 98257399-E9B3-4BAB-9204-0E89117DEA6D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: a0738b03c4fa0ad975b872307bb17f387b1c5fd5
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120951"
---
# <a name="deploying-and-testing-watchos-apps-with-xamarin"></a>Implantando e testando aplicativos watchOS com Xamarin

## <a name="deployment-checklist"></a>Lista de verificação de implantação

Se você estiver implantando em um teste de inspeção ou carregar para a App Store, você precisará concluir as etapas nesta página:

- No **Centro de desenvolvimento do iOS**:
  - [IDs do aplicativo](#App_IDs) foram criados.
  - [Grupos de aplicativos](#App_Groups) configurada (se necessário).
  - Perfis de provisionamento de distribuição criado

- Em sua solução:

  - Verifique se o [referências de projeto e as IDs do lote](~/ios/watchos/get-started/installation.md) são definidos.
  - Verifique os ícones estão [configurado corretamente](~/ios/watchos/app-fundamentals/icons.md).
  - Verifique se a correspondência de números de versão do pacote em todos os projetos.
  - Configurar o **Entitlements. plist** para grupos de aplicativos (se necessário).

* Em seguida, siga as instruções para:
  - [Implantar em um Apple Watch para teste](~/ios/watchos/deploy-test/device.md), ou
  - [Carregar para o aplicativo Store](~/ios/watchos/deploy-test/appstore.md).

<a name="App_IDs"/>

## <a name="app-ids"></a>IDs de aplicativo

Conforme discutido na [instruções de instalação](~/ios/watchos/get-started/installation.md), todos os três projetos em um aplicativo de inspeção relacionaram as IDs do lote, tais como:

- Projeto do xamarin. IOS unificado- `com.xamarin.WatchKitCatalog`
- Projeto de extensão WatchKit- `com.xamarin.WatchKitCatalog.watchkitextension`
- Projeto de aplicativo Watch- `com.xamarin.WatchKitCatalog.watchkitapp`

Todos os três projetos exigem uma correspondência provisionamento de perfil de distribuição, usando explicitamente IDs do aplicativo para cada um, ou um caractere curinga a ID do aplicativo.

### <a name="explicit-app-ids"></a>IDs de aplicativo explícita

Criar uma **ID do aplicativo** para a ID de pacote de cada cada projeto (que terá esta aparência no Centro de desenvolvimento iOS):

![As IDs de pacote no Centro de desenvolvimento do iOS](images/appids-specific-sml.png)

Ao criar ou configurar as IDs do aplicativo, lembre-se de habilitar os recursos específicos do que seu aplicativo requer. Isso pode incluir grupos de aplicativos e de notificações por push.

Você precisará criar um perfil de provisionamento de distribuição para cada ID. App

### <a name="wildcard-app-id"></a>ID do aplicativo curinga

Como alternativa, você pode criar um caractere curinga **ID do aplicativo** que corresponde a todos os três projetos, tais como `com.xamarin.*`.

Observe que alguns recursos não podem ser usados com um caractere curinga ID do aplicativo (como notificações por push). Se seu aplicativo exigir esses recursos, você deve criar IDs do aplicativo explícitas.

Para distribuição, você só precisará criar um perfil de provisionamento de distribuição para o curinga de ID do aplicativo.

<a name="App_Groups" />

## <a name="app-groups"></a>Grupos de Aplicativos

Você pode usar um grupo de aplicativos para compartilhar dados entre seu aplicativo iOS e a extensão de inspeção. Você deve garantir que sua solução tem:

- Configurado o **grupo de aplicativos** no Portal do desenvolvedor Apple **certificados, identificadores e perfis** seção.

- Habilitada **grupos de aplicativos** (e fornecidos a **ID de grupo do aplicativo**) na *ambos* o aplicativo do iOS e a extensão de inspeção **ID do aplicativo** e  **Entitlements. plist**.

### <a name="certificates-identifiers--profiles"></a>Certificados, identificadores e perfis

Para usar um grupo de aplicativos, crie uma entrada na **grupos de aplicativos** tela. No exemplo a seguir, o grupo é denominado com o mesmo estilo de DNS reverso é comumente usado para as IDs do aplicativo, mas com o `group.` prefixo (que é necessário):

![O identificador](images/appgroups-new-sml.png)

O grupo de aplicativos, em seguida, será exibida na lista:

![A lista de identificador](images/appgroups-setup-sml.png)

Depois que o grupo é criado, ele pode ser referenciado em seu **ID do aplicativo** configuração. Lembre-se de incluí-lo o iOS App e extensão de inspeção **IDs do aplicativo**.

![Consifurations disponíveis](images/appgroups-sml.png)

Fazer **não** habilitar grupos de aplicativos na ID de aplicativo do Apple Watch. Não é necessário para ser habilitado no watch.

### <a name="entitlementsplist"></a>Entitlements.plist

Alguns recursos do aplicativo (por exemplo. Grupos de aplicativos) exigem que você defina seus direitos.
Clique duas vezes para editar a **Entitlements. plist** arquivo nesses projetos:

- projeto de aplicativo iOS
- Projeto de extensão de inspeção

.![O editor de Entitlements. plist](images/entitlements-plist-sml.png)

Fazer **não** habilitar direitos no projeto de aplicativo de inspeção. Não é necessário para ser habilitado no watch.

## <a name="related-links"></a>Links relacionados

- [Guia de envio de WatchKit da Apple](https://developer.apple.com/app-store/watch/)
