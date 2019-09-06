---
title: Implantando e testando aplicativos watchOS com o Xamarin
description: Este documento descreve como implantar e testar aplicativos watchOS criados com o Xamarin. Ele fornece uma lista de verificação de implantação, discute IDs de aplicativo curingas e explícitas e examina grupos de aplicativos.
ms.prod: xamarin
ms.assetid: 98257399-E9B3-4BAB-9204-0E89117DEA6D
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: af6d32808e2847c99c4f8a2d90e7dc2d3955ae03
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70284669"
---
# <a name="deploying-and-testing-watchos-apps-with-xamarin"></a>Implantando e testando aplicativos watchOS com o Xamarin

## <a name="deployment-checklist"></a>Lista de verificação de implantação

Se você estiver implantando em uma inspeção de teste ou carregando para a loja de aplicativos, precisará concluir as etapas nesta página:

- No **centro de desenvolvimento do IOS**:
  - As [IDs do aplicativo](#App_IDs) foram criadas.
  - [Grupos de aplicativos](#App_Groups) configurados (se necessário).
  - Perfis de provisionamento de distribuição criados

- Em sua solução:

  - Verifique se as [IDs de pacote e as referências de projeto](~/ios/watchos/get-started/installation.md) estão definidas.
  - Verifique se os ícones estão [configurados corretamente](~/ios/watchos/app-fundamentals/icons.md).
  - Verifique se os números de versão do pacote correspondem em todos os projetos.
  - Configure os **direitos. plist** para grupos de aplicativos (se necessário).

- Em seguida, siga as instruções para:
  - [Implantar em um Apple Watch para teste](~/ios/watchos/deploy-test/device.md)ou
  - [Carregue na loja de aplicativos](~/ios/watchos/deploy-test/appstore.md).

<a name="App_IDs"/>

## <a name="app-ids"></a>IDs de aplicativo

Conforme discutido nas [instruções de instalação](~/ios/watchos/get-started/installation.md), todos os três projetos em um aplicativo de inspeção têm IDs de pacote relacionadas, como:

- Projeto unificado do Xamarin. iOS-`com.xamarin.WatchKitCatalog`
- Projeto de extensão WatchKit-`com.xamarin.WatchKitCatalog.watchkitextension`
- Inspecionar projeto de aplicativo-`com.xamarin.WatchKitCatalog.watchkitapp`

Todos os três projetos exigem um perfil de provisionamento de distribuição correspondente, usando IDs de aplicativo explicitamente para cada um ou uma ID de aplicativo curinga.

### <a name="explicit-app-ids"></a>IDs explícitas do aplicativo

Crie uma **ID de aplicativo** para a ID de pacote de cada projeto (que terá a seguinte aparência no centro de desenvolvimento do IOS):

![As IDs de pacote no centro de desenvolvimento do iOS](images/appids-specific-sml.png)

Ao criar ou configurar IDs de aplicativo, lembre-se de habilitar os recursos específicos que seu aplicativo requer. Isso pode incluir notificações por push e grupos de aplicativos.

Será necessário criar um perfil de provisionamento de distribuição para cada ID do aplicativo.

### <a name="wildcard-app-id"></a>ID do aplicativo curinga

Como alternativa, você pode criar uma **ID de aplicativo** curinga que corresponda a todos os três projetos `com.xamarin.*`, como.

Observe que alguns recursos não podem ser usados com uma ID de aplicativo curinga (como notificações por push). Se seu aplicativo exigir esses recursos, você deverá criar IDs explícitas do aplicativo.

Para distribuição, você só precisará criar um perfil de provisionamento de distribuição para a ID do aplicativo curinga.

<a name="App_Groups" />

## <a name="app-groups"></a>Grupos de Aplicativos

Você pode usar um grupo de aplicativos para compartilhar dados entre seu aplicativo iOS e a extensão Watch. Você deve garantir que sua solução tenha:

- Configurado o **grupo de aplicativos** na seção certificados do portal do desenvolvedor Apple **, identificadores & perfis** .

- Os **grupos de aplicativos** habilitados (e forneceram a **ID do grupo de aplicativos**) no aplicativo Ios e a ID do **aplicativo** da extensão de inspeção e os **direitos. plist**.

### <a name="certificates-identifiers--profiles"></a>Certificados, identificadores & perfis

Para usar um grupo de aplicativos, crie uma entrada na tela **grupos de aplicativos** . No exemplo abaixo, o grupo é nomeado com o mesmo estilo de DNS reverso que é comumente usado para IDs de aplicativo, mas `group.` com o prefixo (que é necessário):

![O identificador](images/appgroups-new-sml.png)

O grupo de aplicativos será exibido na lista:

![A lista de identificadores](images/appgroups-setup-sml.png)

Depois que o grupo é criado, ele pode ser referenciado em sua configuração de **ID do aplicativo** . Lembre-se de incluir o aplicativo iOS e as **IDs do aplicativo**de extensão de inspeção.

![Configurações disponíveis](images/appgroups-sml.png)

Não **habilite** grupos de aplicativos na ID do aplicativo Apple Watch. Não é necessário habilitá-lo no próprio relógio.

### <a name="entitlementsplist"></a>Entitlements.plist

Alguns recursos de aplicativo (por exemplo, Grupos de aplicativos) exigem que você defina seus direitos.
Clique duas vezes para editar o arquivo **. plist de direitos** nesses projetos:

- projeto de aplicativo iOS
- Inspecionar projeto de extensão

.![O editor de direitos. plist](images/entitlements-plist-sml.png)

Não **habilite** direitos no projeto de aplicativo Watch. Não é necessário habilitá-lo no próprio relógio.

## <a name="related-links"></a>Links relacionados

- [Guia de envio do Apple WatchKit](https://developer.apple.com/app-store/watch/)
