---
title: Por que o Visual Studio não inclui meu projeto de biblioteca referenciado no build?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: b9009db8-e716-43aa-b40e-6f28a8eb1b82
author: asb3993
ms.author: amburns
ms.date: 12/02/2016
ms.openlocfilehash: d7aeac2f433e8fdf231f5887f1537f15e2bd1976
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61341302"
---
# <a name="why-doesnt-visual-studio-include-my-referenced-library-project-in-my-build"></a>Por que o Visual Studio não inclui meu projeto de biblioteca referenciado no build?

O Visual Studio usa o **Configuration Manager** para determinar quais projetos em uma solução são incluídos automaticamente em determinada configuração de build ou implantação.

Alguns modelos gerados com um projeto de biblioteca referenciada já terão a biblioteca referenciada incluída na configuração; caso contrário, ela precisará ser definida manualmente.

## <a name="how-to-use-the-configuration-manager"></a>Como usar o Configuration Manager

1. Abra **Compilar > Configuration Manager**
2. Selecione a configuração para personalizar, por exemplo, **depurar | iPhone**
3. Marque as caixas de seleção para os projetos que deseja incluir.

> [!NOTE]
> Acinzentados caixas são manipuladas automaticamente e não necessitam alteração.

Screencast dessas etapas: [http://screencast.com/t/zLoQOpEn](http://screencast.com/t/zLoQOpEn)
