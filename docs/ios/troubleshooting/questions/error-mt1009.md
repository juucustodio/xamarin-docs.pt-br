---
title: "Erro MT1009: Não foi possível copiar o assembly"
ms.topic: article
ms.prod: xamarin
ms.assetid: F9FEDFF5-C84C-42B4-8F25-E34846E7315A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 030c54275ef384f4a020abaf79456705e8fac0d3
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="error-mt1009-could-not-copy-the-assembly"></a>Erro MT1009: Não foi possível copiar o assembly

> [!IMPORTANT]
> Esse problema foi resolvido em versões recentes do xamarin. No entanto, se o problema ocorre na versão mais recente do software, envie um [novo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) com o controle de versão completo completo e informações de log de saída de compilação.

Conforme descrito em nossa [notas de versão](https://developer.xamarin.com/releases/ios/xamarin.ios_7/xamarin.ios_7.2/), esse é um problema conhecido no xamarin 7.2.6. Esse problema ocorre devido a permissões de arquivos que precisam de privilégios mais altos quando xamarin é instalado com uma conta de usuário diferente, em seguida, conta principal do desenvolvedor.

Para solucionar o problema, abra o Terminal.app na estação de trabalho de Mac e execute o seguinte comando:

`sudo chmod 0644 /Developer/MonoTouch/usr/lib/mono/2.1/monotouch.dll.mdb`

