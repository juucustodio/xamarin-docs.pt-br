---
title: O local do usuário não está funcionando no iOS 8
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9BE92C99-C9C5-427E-ADE4-789DF258BACE
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: 4b7f4239f97b7199f9b0eb7f1be9907a2c54cb0b
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70288100"
---
# <a name="user-location-not-working-in-ios-8"></a>O local do usuário não está funcionando no iOS 8

Em um editor de texto: Abra o info. plist e adicione o seguinte:

```xml
<key>NSLocationWhenInUseUsageDescription</key>
<string>You are about to use location!</string>

<key>NSLocationAlwaysUsageDescription</key>
<string>This will be called if location is used behind the scenes</string>
```

E, dentro do MainViewController.cs, você precisa chamar o seguinte:

```csharp
iPhoneLocationManager.RequestWhenInUseAuthorization ();
```

ESTENDI

```cs
if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
    iPhoneLocationManager.RequestWhenInUseAuthorization ();
    iPhoneLocationManager.LocationsUpdated += (object sender, CLLocationsUpdatedEventArgs e) => {
        UpdateLocation (mainScreen, e.Locations [e.Locations.Length - 1]);
        };
} else {
    // this won't be called on iOS 6 (deprecated)
    iPhoneLocationManager.UpdatedLocation += (object sender, CLLocationUpdatedEventArgs e) => {
        UpdateLocation (mainScreen, e.NewLocation);
        };
}
```
