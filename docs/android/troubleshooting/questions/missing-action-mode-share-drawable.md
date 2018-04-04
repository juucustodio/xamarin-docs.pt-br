---
title: "Android.Support.v7.AppCompat - nenhum recurso encontrado que coincide com o nome fornecido: attr 'android: actionModeShareDrawable'"
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5814069C-FC43-41DE-B5A5-024D05E59929
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: 07655587642c3e1aa94d035e76f6f6758340546d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawable"></a>Android.Support.v7.AppCompat - nenhum recurso encontrado que coincide com o nome fornecido: attr 'android: actionModeShareDrawable'

1. Certifique-se de que baixar os mais recentes extras, bem como o Android 5.0 (API 21) SDK por meio do Gerenciador de SDK do Android.

2. Certifique-se de que você está compilando seu aplicativo com compileSdkVersion definido como 21. Você pode definir opcionalmente o targetSdkVersion como 21 também.

3. Se você precisar de uma versão anterior, como a API 19, baixe a versão do respectiva encontrada na página do Nuget:

[https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)

*Observação*: se você instalar manualmente isso por meio do Console do Gerenciador de pacotes, certifique-se de também instalar a mesma versão do Xamarin.Android.Support.v4

[https://www.nuget.org/packages/Xamarin.Android.Support.v4/](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)

Referência de estouro de pilha: [http://stackoverflow.com/questions/26431676/appcompat-v721-0-0-no-resource-found-that-matches-the-given-name-attr-andro](http://stackoverflow.com/questions/26431676/appcompat-v721-0-0-no-resource-found-that-matches-the-given-name-attr-andro)

## <a name="see-also"></a>Consulte também

- [Quais pacotes de SDK do Android eu devo instalar?](~/android/troubleshooting/questions/install-android-sdk-packages.md)

