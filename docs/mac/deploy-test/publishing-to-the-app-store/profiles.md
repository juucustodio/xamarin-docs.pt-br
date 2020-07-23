---
title: Perfis de provisionamento para aplicativos Xamarin.Mac
description: Esse guia o orienta na criação dos Perfis de Provisionamento que serão necessários para publicar um aplicativo Xamarin.Mac.
ms.prod: xamarin
ms.assetid: bdff6c32-f7e3-4a97-a093-dbda48be8227
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 04/12/2017
ms.openlocfilehash: 0bde2ee6451b7160ac7c1655e705984e53c82ff4
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86939003"
---
# <a name="provisioning-profiles-for-xamarinmac-apps"></a>Perfis de provisionamento para aplicativos Xamarin.Mac

Os perfis de provisionamento permitem que um desenvolvedor incorpore vários recursos específicos (como iCloud e notificações por Push) do macOS (anteriormente conhecido como o Mac OS X) em seus aplicativos Xamarin.Mac. Eles devem criar, baixar e instalar um Perfil de Provisionamento do Mac para cada aplicativo que estão desenvolvendo que usa esses recursos.

[![O Portal de Provisionamento da Apple](profiles-images/certif13.png)](profiles-images/certif13.png#lightbox)

## <a name="development-provisioning-profile"></a>Perfil de provisionamento de desenvolvimento

Um Perfil de Provisionamento de Desenvolvimento permite que um aplicativo destinado à Mac App Store seja testado nos computadores específicos que foram configurados no perfil. Isso é particularmente relevante quando estiver usando recursos do macOS como iCloud e Notificações por Push.

> [!NOTE]
> O desenvolvedor já deve ter criado um Certificado de Desenvolvimento do Mac antes de conseguir criar um Perfil de Provisionamento de Desenvolvimento. Conclua os detalhes, conforme mostrado nessa captura de tela, para gerar um **Perfil de Provisionamento de Desenvolvimento** que pode ser usado para criar compilações. Deve haver um Certificado Válido de Desenvolvimento do Mac disponível para seleção na caixa **Certificado** e, pelo menos, um sistema registrado para teste.

Faça o seguinte:

1. Selecione o tipo de Perfil de Provisionamento a ser criado e clique no botão **Continuar**:

    [![Selecionar o tipo de perfil](profiles-images/certif14.png)](profiles-images/certif14.png#lightbox)
2. Selecione a ID do aplicativo para o qual será criado o perfil e clique no botão **Continuar**:

    [![Selecionar a ID do aplicativo](profiles-images/certif15.png)](profiles-images/certif15.png#lightbox)
3. Selecione a ID de desenvolvedor usada para assinar o perfil e clique em **Continuar**:

    [![Selecionar a ID de desenvolvedor](profiles-images/certif16.png)](profiles-images/certif16.png#lightbox)
4. Selecione os computadores nos quais esse perfil pode ser usado e clique em **Continuar**:

    [![Selecionar os computadores permitidos](profiles-images/certif17.png)](profiles-images/certif17.png#lightbox)
5. Agora, insira um **Nome do Perfil** e clique no botão **Gerar**:

    [![Gerar o perfil](profiles-images/certif18.png)](profiles-images/certif18.png#lightbox)
6. Clique no botão **Download** para baixar o novo perfil:

    [![Baixar o perfil](profiles-images/certif19.png)](profiles-images/certif19.png#lightbox)
7. Os perfis de provisionamento de desenvolvimento são instalados no painel de Preferências de Perfis das **Preferências do Sistema** do aplicativo do Mac:

    [![Instalar o perfil](profiles-images/certif20.png)](profiles-images/certif20.png#lightbox)
8. O painel de Preferências de Perfil mostrará todos os perfis instalados:

    [![Mostrar todos os perfis instalados](profiles-images/image47.png)](profiles-images/image47.png#lightbox)
9. O perfil também aparecerá no **Utilitário de Certificado de Desenvolvedor**, caso precise ser baixado novamente:

    [![O Utilitário de Certificado do Desenvolvedor](profiles-images/image48.png)](profiles-images/image48.png#lightbox)

Será necessário criar um novo Perfil de Provisionamento de Desenvolvimento para cada novo aplicativo ou quando um novo computador for adicionado para ser usado em teste.

## <a name="production-provisioning-profile"></a>Perfil de provisionamento de produção

Os perfis de provisionamento de produção são necessários para compilar um pacote para ser enviado à Mac App Store.

Faça o seguinte:

1. Selecione o tipo de perfil a ser criado e clique no botão **Continuar**:

    [![Selecionar o tipo de perfil](profiles-images/certif21.png)](profiles-images/certif21.png#lightbox)
2. Selecione a ID do aplicativo para o qual será criado o perfil e clique no botão **Continuar**:

    [![Selecionar a ID do aplicativo](profiles-images/certif15.png)](profiles-images/certif15.png#lightbox)
3. Selecione a ID da empresa para assinar o perfil e clique no botão **Continuar**:

    [![Selecionar a ID da empresa](profiles-images/certif23.png)](profiles-images/certif23.png#lightbox)
4. Insira um **Nome do perfil** e clique no botão **Gerar**:

    [![Gerar o perfil](profiles-images/certif24.png)](profiles-images/certif24.png#lightbox)
5. Clique em **Download** para obter o arquivo de perfil de provisionamento (extensão `.provisionprofile`):

    [![Baixar o perfil](profiles-images/certif25.png)](profiles-images/certif25.png#lightbox)
6. Arraste-o até o **Organizador do Xcode** ou clique duas vezes nele para instalar. Dessa forma, o perfil aparecerá no Organizador do Xcode:

    [![Instalar o perfil](profiles-images/image51.png)](profiles-images/image51.png#lightbox)
7. O perfil de provisionamento também aparecerá na lista:

    [![Mostrar os perfis instalados](profiles-images/certif26.png)](profiles-images/certif26.png#lightbox)

Sempre que o desenvolvedor mudar os recursos que estão sendo usados por uma ID do aplicativo (por exemplo, habilitar o iCloud ou notificações por push), deverá recriar os perfis de provisionamento para aquela ID do aplicativo.

## <a name="related-links"></a>Links relacionados

- [Instalação](~//mac/get-started/installation.md)
- [Olá, exemplo de Mac](~//mac/get-started/hello-mac.md)
- [Distribua aplicativos na Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [Guia de ferramentas: assinatura de código do aplicativo](https://developer.apple.com/library/mac/#documentation/ToolsLanguages/Conceptual/OSXWorkflowGuide/CodeSigning/CodeSigning.html)
- [ID de Desenvolvedor e GateKeeper](https://developer.apple.com/developer-id/)
