---
title: "Android.Support.v7.AppCompat – Não encontramos um recurso correspondente ao nome fornecido: attr 'android:actionModeShareDrawable'"
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5814069C-FC43-41DE-B5A5-024D05E59929
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: e688bd27d1116b2a77a12ccd6da29ea582053581
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "75728103"
---
# <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawable"></a>Android.Support.v7.AppCompat – Não encontramos um recurso correspondente ao nome fornecido: attr 'android:actionModeShareDrawable'

1. Certifique-se de baixar os extras mais recentes, bem como o SDK do Android 5,0 (API 21) por meio do Gerenciador de SDK do Android.

2. Verifique se você está compilando seu aplicativo com compileSdkVersion definido como 21. Opcionalmente, você pode definir o targetSdkVersion como 21 também.

3. Se você precisar de uma versão anterior, como a API 19, baixe a respectiva versão encontrada na página do NuGet:

[https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)

> [!NOTE]
> Se você instalar manualmente por meio do console do Gerenciador de pacotes, instale também a mesma versão do Xamarin. Android. support. v4

[https://www.nuget.org/packages/Xamarin.Android.Support.v4/](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)

Referência de Stack Overflow: [https://stackoverflow.com/questions/26431676/appcompat-v721-0-0-no-resource-found-that-matches-the-given-name-attr-andro](https://stackoverflow.com/questions/26431676/appcompat-v721-0-0-no-resource-found-that-matches-the-given-name-attr-andro)

## <a name="see-also"></a>Consulte Também

- [Quais pacotes de SDK do Android eu devo instalar?](~/android/troubleshooting/questions/install-android-sdk-packages.md)
