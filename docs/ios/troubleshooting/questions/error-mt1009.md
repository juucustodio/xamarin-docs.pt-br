---
title: 'Erro MT1009: Não foi possível copiar o assembly'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F9FEDFF5-C84C-42B4-8F25-E34846E7315A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 4a67537cc53aeecf1b86d11dbf041cea79587dd2
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105384"
---
# <a name="error-mt1009-could-not-copy-the-assembly"></a>Erro MT1009: Não foi possível copiar o assembly

> [!IMPORTANT]
> Esse problema foi resolvido em versões recentes do xamarin. IOS. No entanto, se o problema ocorre na versão mais recente do software, envie uma [novo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) com o controle de versão completo completo e informações saída de log de compilação.

Conforme descrito em nossa [notas de versão](https://developer.xamarin.com/releases/ios/xamarin.ios_7/xamarin.ios_7.2/), esse é um problema conhecido no xamarin. IOS 7.2.6. Esse problema é devido à necessidade de privilégios mais altos quando o xamarin. IOS é instalado com uma conta de usuário diferente de permissões de arquivo, em seguida, conta de principal do desenvolvedor.

Para solucionar o problema, abra o terminal na estação de trabalho Mac e execute o seguinte comando:

`sudo chmod 0644 /Developer/MonoTouch/usr/lib/mono/2.1/monotouch.dll.mdb`

