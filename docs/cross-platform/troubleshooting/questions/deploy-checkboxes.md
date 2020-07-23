---
title: Implantar caixas de seleção desabilitadas no Configuration Manager
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: aaf675cd-d885-4dac-9754-77dbcaea3be9
author: davidortinau
ms.author: daortin
ms.date: 12/02/2016
ms.openlocfilehash: 92fe421aee6b5add84a188468856172bf8c56d15
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86939276"
---
# <a name="deploy-checkboxes-disabled-in-configuration-manager"></a>Implantar caixas de seleção desabilitadas no Configuration Manager

Já que os projetos Xamarin 3.5, Xamarin.iOS eram implantados automaticamente sempre que você pressionava o botão de barra de ferramentas **Iniciar** ou selecionava o item de menu **Depurar > Iniciar depuração**. Você ainda precisará definir o projeto de aplicativo Xamarin. iOS desejado como o **projeto de inicialização** antes de executar qualquer um desses comandos.

Por isso, as caixas de seleção **Implantar** são intencionalmente desabilitadas no Configuration Manager do Visual Studio para projetos do Xamarin.iOS:

![](deploy-checkboxes-images/configuration.png "Visual Studio Configuration Manager showing the 'Deploy' checkbox disabled for a Xamarin.iOS project in Xamarin 3.5")

Essa alteração elimina um erro que pode aparecer em versões mais antigas do Xamarin (versão 3.3 e anterior) quando o projeto do aplicativo Xamarin.iOS não era definido para implantação:

![Caixa de diálogo de erro: o projeto iPhoneApp1 precisa ser implantado antes de ser iniciado. Verifique se que o projeto está selecionado para ser implantado no Configuration Manager da solução.](deploy-checkboxes-images/error.png)
