---
title: Por que o Visual Studio não inclui meu projeto de biblioteca referenciado no build?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: b9009db8-e716-43aa-b40e-6f28a8eb1b82
author: davidortinau
ms.author: daortin
ms.date: 12/02/2016
ms.openlocfilehash: aba2e9dacd930c6302a96a8daf929eadab485922
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73012749"
---
# <a name="why-doesnt-visual-studio-include-my-referenced-library-project-in-my-build"></a>Por que o Visual Studio não inclui meu projeto de biblioteca referenciado no build?

O Visual Studio usa o **Configuration Manager** para determinar quais projetos em uma solução são incluídos automaticamente em determinada configuração de build ou implantação.

Alguns modelos gerados com um projeto de biblioteca referenciada já terão a biblioteca referenciada incluída na configuração; caso contrário, ela precisará ser definida manualmente.

## <a name="how-to-use-the-configuration-manager"></a>Como usar o Configuration Manager

1. Abra **Compilar > Configuration Manager**
2. Selecione a configuração a ser personalizada, por exemplo, **debug | iPhone**
3. Marque as caixas de seleção para os projetos que deseja incluir.

> [!NOTE]
> As caixas esmaecidas são manipuladas automaticamente e não precisam de nenhuma alteração.

Screencast destas etapas: [https://screencast.com/t/zLoQOpEn](https://screencast.com/t/zLoQOpEn)
