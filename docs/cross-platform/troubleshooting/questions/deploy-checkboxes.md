---
title: Implantar caixas de seleção desabilitadas no Configuration Manager
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: aaf675cd-d885-4dac-9754-77dbcaea3be9
author: davidortinau
ms.author: daortin
ms.date: 12/02/2016
ms.openlocfilehash: edf471f1d9a2ee4adc11f09e0c7b7ad3cf6f78f1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73014248"
---
# <a name="deploy-checkboxes-disabled-in-configuration-manager"></a>Implantar caixas de seleção desabilitadas no Configuration Manager

Já que os projetos Xamarin 3.5, Xamarin.iOS eram implantados automaticamente sempre que você pressionava o botão de barra de ferramentas **Iniciar** ou selecionava o item de menu **Depurar > Iniciar depuração**. Você ainda precisará definir o projeto de aplicativo Xamarin. iOS desejado como o **projeto de inicialização** antes de executar qualquer um desses comandos.

Por isso, as caixas de seleção **Implantar** são intencionalmente desabilitadas no Configuration Manager do Visual Studio para projetos do Xamarin.iOS:

![](deploy-checkboxes-images/configuration.png "Visual Studio Configuration Manager showing the 'Deploy' checkbox disabled for a Xamarin.iOS project in Xamarin 3.5")

Essa alteração elimina um erro que pode aparecer em versões mais antigas do Xamarin (versão 3.3 e anterior) quando o projeto do aplicativo Xamarin.iOS não era definido para implantação:

![](deploy-checkboxes-images/error.png "Error dialog: The project iPhoneApp1 needs to be deployed before it can be started. Verify the project is selected to be deployed in the Solution Configuration Manager.")
