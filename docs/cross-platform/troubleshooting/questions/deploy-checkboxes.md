---
title: Implantar caixas de seleção desabilitadas no Configuration Manager
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: aaf675cd-d885-4dac-9754-77dbcaea3be9
author: conceptdev
ms.author: crdun
ms.date: 12/02/2016
ms.openlocfilehash: 82ff1a684ffad75a301f0db6b0f8e3116be6746d
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70285061"
---
# <a name="deploy-checkboxes-disabled-in-configuration-manager"></a>Implantar caixas de seleção desabilitadas no Configuration Manager

Já que os projetos Xamarin 3.5, Xamarin.iOS eram implantados automaticamente sempre que você pressionava o botão de barra de ferramentas **Iniciar** ou selecionava o item de menu **Depurar > Iniciar depuração**. Você ainda precisará definir o projeto de aplicativo Xamarin. iOS desejado como o **projeto de inicialização** antes de executar qualquer um desses comandos.

Por isso, as caixas de seleção **Implantar** são intencionalmente desabilitadas no Configuration Manager do Visual Studio para projetos do Xamarin.iOS:

![](deploy-checkboxes-images/configuration.png "O Visual Studio Configuration Manager mostrando a caixa de seleção ' implantar ' desabilitada para um projeto Xamarin. iOS no Xamarin 3,5")

Essa alteração elimina um erro que pode aparecer em versões mais antigas do Xamarin (versão 3.3 e anterior) quando o projeto do aplicativo Xamarin.iOS não era definido para implantação:

![](deploy-checkboxes-images/error.png "Caixa de diálogo de erro: O projeto iPhoneApp1 precisa ser implantado antes que possa ser iniciado. Verifique se o projeto está selecionado para ser implantado na solução Configuration Manager.")
