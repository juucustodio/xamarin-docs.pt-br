---
title: Implantar caixas de seleção desabilitadas no Configuration Manager
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: aaf675cd-d885-4dac-9754-77dbcaea3be9
author: davidortinau
ms.author: daortin
ms.date: 12/02/2016
ms.openlocfilehash: c3d0b8f2da971238b98253405f3b8fe08699ab56
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "86997209"
---
# <a name="deploy-checkboxes-disabled-in-configuration-manager"></a>Implantar caixas de seleção desabilitadas no Configuration Manager

Já que os projetos Xamarin 3.5, Xamarin.iOS eram implantados automaticamente sempre que você pressionava o botão de barra de ferramentas **Iniciar** ou selecionava o item de menu **Depurar > Iniciar depuração**. Você ainda precisará definir o projeto de aplicativo Xamarin. iOS desejado como o **projeto de inicialização** antes de executar qualquer um desses comandos.

Por isso, as caixas de seleção **Implantar** são intencionalmente desabilitadas no Configuration Manager do Visual Studio para projetos do Xamarin.iOS:

![O Visual Studio Configuration Manager mostrando a caixa de seleção implantar desabilitada para um projeto Xamarin. iOS no Xamarin 3,5](deploy-checkboxes-images/configuration.png)

Essa alteração elimina um erro que pode aparecer em versões mais antigas do Xamarin (versão 3.3 e anterior) quando o projeto do aplicativo Xamarin.iOS não era definido para implantação:

![Caixa de diálogo de erro: o projeto iPhoneApp1 precisa ser implantado antes de ser iniciado. Verifique se que o projeto está selecionado para ser implantado no Configuration Manager da solução.](deploy-checkboxes-images/error.png)
