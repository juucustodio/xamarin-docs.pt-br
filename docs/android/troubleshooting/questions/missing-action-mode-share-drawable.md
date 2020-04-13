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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "75728103"
---
# <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawable"></a>Android.Support.v7.AppCompat – Não encontramos um recurso correspondente ao nome fornecido: attr 'android:actionModeShareDrawable'

1. Certifique-se de baixar os extras mais recentes, bem como o SDK android 5.0 (API 21) através do Android SDK Manager.

2. Certifique-se de que está compilando seu aplicativo com compilaçãoSdkVersion definida como 21. Você também pode definir o TargetSdkVersion como 21.

3. Se você precisar de uma versão anterior, como a API 19, baixe a respectiva versão encontrada na página NuGet:

[https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)

> [!NOTE]
> Se você instalar isso manualmente via Console de gerenciador de pacotes, certifique-se de que você também instale a mesma versão do Xamarin.Android.Support.v4

[https://www.nuget.org/packages/Xamarin.Android.Support.v4/](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)

Referência de estouro de pilha:[https://stackoverflow.com/questions/26431676/appcompat-v721-0-0-no-resource-found-that-matches-the-given-name-attr-andro](https://stackoverflow.com/questions/26431676/appcompat-v721-0-0-no-resource-found-that-matches-the-given-name-attr-andro)

## <a name="see-also"></a>Consulte Também

- [Quais pacotes de SDK do Android eu devo instalar?](~/android/troubleshooting/questions/install-android-sdk-packages.md)
