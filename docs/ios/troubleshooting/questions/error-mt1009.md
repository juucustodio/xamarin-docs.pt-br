---
title: 'Erro de MT1009: Não foi possível copiar o assembly'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F9FEDFF5-C84C-42B4-8F25-E34846E7315A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: b01701566cec88ad4a1493cf5ab9778bd38792b0
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511812"
---
# <a name="error-mt1009-could-not-copy-the-assembly"></a>Erro de MT1009: Não foi possível copiar o assembly

> [!IMPORTANT]
> Esse problema foi resolvido em versões recentes do Xamarin. iOS. No entanto, se o problema ocorrer na versão mais recente do software, registre um [novo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) com suas informações completas de controle de versão e saída de log de compilação completa.

Conforme descrito em nossas [notas de versão](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/xamarin.ios_7/xamarin.ios_7.2/index.md), esse é um problema conhecido no Xamarin. Ios 7.2.6. Esse problema ocorre devido a permissões de arquivo que precisam de privilégios mais altos quando o Xamarin. iOS é instalado com uma conta de usuário diferente, em seguida, a conta principal do desenvolvedor.

Para solucionar o problema, abra o terminal. app na estação de trabalho do Mac e execute o seguinte comando:

`sudo chmod 0644 /Developer/MonoTouch/usr/lib/mono/2.1/monotouch.dll.mdb`
