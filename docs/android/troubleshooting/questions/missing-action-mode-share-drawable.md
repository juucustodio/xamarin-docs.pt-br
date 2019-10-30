---
title: "Android.Support.v7.AppCompat – Não encontramos um recurso correspondente ao nome fornecido: attr 'android:actionModeShareDrawable'"
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5814069C-FC43-41DE-B5A5-024D05E59929
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 4e544368d8fe2ab6316f9d3c79ff392f2bd83f09
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73026808"
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

## <a name="see-also"></a>Consulte também

- [Quais pacotes de SDK do Android eu devo instalar?](~/android/troubleshooting/questions/install-android-sdk-packages.md)
