---
title: "Android.Support.v7.AppCompat – nenhum recurso encontrado que corresponde ao nome fornecido: attr 'android: actionModeShareDrawable'"
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5814069C-FC43-41DE-B5A5-024D05E59929
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: fea681ac3b99abed09d3d3e745bd4bf6015970df
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112410"
---
# <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawable"></a>Android.Support.v7.AppCompat – nenhum recurso encontrado que corresponde ao nome fornecido: attr 'android: actionModeShareDrawable'

1. Certifique-se de que baixar os mais recentes extras, bem como o Android 5.0 SDK por meio do Gerenciador de SDK do Android (API 21).

2. Certifique-se de que você está compilando seu aplicativo com compileSdkVersion definido como 21. Você pode definir opcionalmente o targetSdkVersion para 21 também.

3. Se você precisar de uma versão anterior, como a API 19, faça o download da respectiva versão encontrada na página do Nuget:

[https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)

*Observação*: se você instalar manualmente isso por meio do Console do Gerenciador de pacotes, certifique-se de também instalar a mesma versão do Xamarin.Android.Support.v4

[https://www.nuget.org/packages/Xamarin.Android.Support.v4/](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)

Referência de estouro de pilha: [http://stackoverflow.com/questions/26431676/appcompat-v721-0-0-no-resource-found-that-matches-the-given-name-attr-andro](http://stackoverflow.com/questions/26431676/appcompat-v721-0-0-no-resource-found-that-matches-the-given-name-attr-andro)

## <a name="see-also"></a>Consulte também

- [Quais pacotes de SDK do Android eu devo instalar?](~/android/troubleshooting/questions/install-android-sdk-packages.md)

