---
title: Como habilitar o Intellisense em arquivos .axml Android?
ms.topic: article
ms.prod: xamarin
ms.assetid: 84850CB2-1CE2-4D3F-BD01-6B3B033F5A4C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: c68ec5a6d369e8673c8764e01943e513489775b0
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="how-do-i-enable-intellisense-in-android-axml-files"></a>Como habilitar o Intellisense em arquivos .axml Android?

Android Intellisense no Visual Studio precisa de dois arquivos de esquema XML para funcionar adequadamente. Para localizar esses arquivos, vá para esta pasta:

`C:\Program Files (x86)\Xamarin Studio\AddIns\MonoDevelop.MonoDroid\schemas`*

* (Anteriormente: `C:\Program Files (x86)\MSBuild\Xamarin\Android`)

Dentro, você encontrará dois arquivos. xsd:

1. android-layout-xml.xsd
2. schemas.android.com.apk.res.android.xsd

O Visual Studio manterá todos os esquemas XML dentro da respectiva pasta:

`C:\Program Files (x86)\Microsoft Visual Studio 12.0\Xml\Schemas`

Você pode optar por mover esses dois arquivos. xsd no local acima, ou simplesmente adicionar esses esquemas dentro do Visual Studio. Você pode, em seguida, "Adicionar" esses esquemas dentro do Visual Studio por meio de **XML > esquemas > Adicionar** caixa de diálogo.






