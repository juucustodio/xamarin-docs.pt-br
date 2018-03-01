---
title: "Implantação e Teste"
description: Dispositivos de teste e carregando a loja de aplicativos
ms.topic: article
ms.prod: xamarin
ms.assetid: 98257399-E9B3-4BAB-9204-0E89117DEA6D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: fdd4311072efd5571724fbe00d12a96921054fa2
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="deployment-and-testing"></a>Implantação e Teste

## <a name="deployment-checklist"></a>Lista de verificação de implantação

Se você estiver implantando em um teste de inspecionar ou carregando a loja de aplicativos, você precisa concluir as etapas nesta página:

- No **Centro de desenvolvimento do iOS**:
  - [IDs de aplicativo](#App_IDs) foram criados.
  - [Grupos de aplicativos](#App_Groups) configurado (se necessário).
  - [*Distribuição* perfis de provisionamento](#Provisioning_Profiles) criado.

- Em sua solução:

  - Verifique se o [IDs de lote e as referências do projeto](~/ios/watchos/get-started/installation.md) são definidos.
  - Verifique os ícones estão [configurado corretamente](~/ios/watchos/app-fundamentals/icons.md).
  - Verifique se a correspondência de números de versão do pacote em todos os projetos.
  - Configurar o **Entitlements.plist** para grupos de aplicativos (se necessário).

* Em seguida, siga as instruções para:
  - [Implantar um Apple Watch para teste](~/ios/watchos/deploy-test/device.md), ou
  - [Carregue a loja de aplicativos](~/ios/watchos/deploy-test/appstore.md).


## <a name="app-ids"></a>IDs de aplicativo

Como discutido o [instruções de instalação](~/ios/watchos/get-started/installation.md), todos os três projetos em um aplicativo de inspeção tem relacionados IDs do pacote, como:

- Xamarin unificada projeto- `com.xamarin.WatchKitCatalog`
- Projeto de extensão WatchKit- `com.xamarin.WatchKitCatalog.watchkitextension`
- Projeto de aplicativo de inspeção- `com.xamarin.WatchKitCatalog.watchkitapp`

Todos os três projetos exigem uma correspondência provisionamento de perfil de distribuição, usando explicitamente IDs de aplicativo para cada um, ou um caractere curinga ID do aplicativo.

### <a name="explicit-app-ids"></a>IDs de aplicativo explícita

Criar um **ID do aplicativo** para a ID de pacote de cada cada projeto (que terá a seguinte aparência no Centro de desenvolvimento do iOS):

![As IDs de lote do Centro de desenvolvimento do iOS](images/appids-specific-sml.png)

Ao criar ou configurar IDs de aplicativo, lembre-se de habilitar os recursos específicos do seu aplicativo requer. Isso pode incluir as notificações por push e grupos de aplicativos.

Você precisará criar um perfil de provisionamento de distribuição para cada ID. App

### <a name="wildcard-app-id"></a>ID do aplicativo de curinga

Como alternativa, você pode criar um curinga **ID do aplicativo** que corresponde a todos os três projetos como `com.xamarin.*`.

Observe que alguns recursos não podem ser usados com um caractere curinga ID do aplicativo (como notificações por push). Se seu aplicativo exigir esses recursos, você deve criar explícita IDs de aplicativo.

Para distribuição, você só precisará criar um perfil de provisionamento de distribuição para o curinga de ID do aplicativo.

<a name="app-groups" />

## <a name="app-groups"></a>Grupos de Aplicativos

Você pode usar um grupo de aplicativos para compartilhar dados entre seu aplicativo iOS e a extensão de inspeção. Certifique-se de que sua solução tem:

- Configurado o **aplicativo grupo** no Portal do desenvolvedor Apple **certificados, identificadores e perfis** seção.

- Habilitado **grupos de aplicativos** (e fornecidos a **ID de grupo do aplicativo**) em *ambos* o aplicativo iOS e a extensão de inspeção **ID do aplicativo** e  **Entitlements.plist**.

### <a name="certificates-identifiers--profiles"></a>Certificados, identificadores e perfis

Para usar um grupo de aplicativo, crie uma entrada de **grupos de aplicativos** tela. No exemplo a seguir, o grupo é denominado com o mesmo estilo inversa de DNS que é comumente usado para as IDs de aplicativo, mas com o `group.` prefixo (que é necessário):

![O identificador](images/appgroups-new-sml.png)

O grupo de aplicativos será exibida na lista:

![A lista de identificador](images/appgroups-setup-sml.png)

Quando o grupo é criado, ele pode ser referenciado em seu **ID do aplicativo** configuração. Lembre-se de incluir os iOS aplicativo e observar extensão **IDs de aplicativo**.

![Consifurations disponíveis](images/appgroups-sml.png)

Fazer **não** habilitar grupos de aplicativos na ID de aplicativo do Apple Watch. Não é necessário para ser habilitado no relógio.

### <a name="entitlementsplist"></a>Entitlements.plist

Alguns recursos do aplicativo (por exemplo. Grupos de aplicativos) exigem que você defina seus direitos.
Clique duas vezes para editar o **Entitlements.plist** arquivo nesses projetos:

- projeto de aplicativo do iOS
- Projeto de extensão de inspeção

.![O editor de Entitlements.plist](images/entitlements-plist-sml.png)

Fazer **não** habilitar direitos no projeto de aplicativo de inspeção. Não é necessário para ser habilitado no relógio.



## <a name="related-links"></a>Links relacionados

- [Guia da Apple WatchKit envio](https://developer.apple.com/app-store/watch/)
