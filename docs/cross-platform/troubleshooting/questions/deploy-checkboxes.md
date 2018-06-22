---
title: Implantar caixas de seleção desabilitadas no Configuration Manager
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: aaf675cd-d885-4dac-9754-77dbcaea3be9
author: asb3993
ms.author: amburns
ms.date: 12/02/2016
ms.openlocfilehash: ab825ba4d28ca8768e5c633fc3779828638a498d
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2018
ms.locfileid: "33919508"
---
# <a name="deploy-checkboxes-disabled-in-configuration-manager"></a>Implantar caixas de seleção desabilitadas no Configuration Manager

Já que os projetos Xamarin 3.5, Xamarin.iOS eram implantados automaticamente sempre que você pressionava o botão de barra de ferramentas **Iniciar** ou selecionava o item de menu **Depurar > Iniciar depuração**. Você ainda precisará definir o projeto desejado do aplicativo Xamarin.iOS como o **Projeto de Inicialização** antes de executar um desses comandos.

Por isso, as caixas de seleção **Implantar** são intencionalmente desabilitadas no Configuration Manager do Visual Studio para projetos do Xamarin.iOS:

![](deploy-checkboxes-images/configuration.png "Gerenciador de configuração do Visual Studio mostra a caixa de seleção 'Implantar' desabilitada para um projeto de xamarin no Xamarin 3.5")

Essa alteração elimina um erro que pode aparecer em versões mais antigas do Xamarin (versão 3.3 e anterior) quando o projeto do aplicativo Xamarin.iOS não era definido para implantação:

![](deploy-checkboxes-images/error.png "Caixa de diálogo de erro: O projeto iPhoneApp1 precisa ser implantado antes que ele possa ser iniciado. Verifique se que o projeto está selecionado para ser implantado no Gerenciador de configuração.")
