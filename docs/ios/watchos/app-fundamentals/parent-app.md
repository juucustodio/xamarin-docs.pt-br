---
title: Trabalhando com o aplicativo pai watchOS no Xamarin
description: Este documento descreve como trabalhar com um aplicativo pai watchOS no Xamarin. Ele aborda extensões de aplicativo watchOS, aplicativos iOS, armazenamento compartilhado e muito mais.
ms.prod: xamarin
ms.assetid: 9AD29833-E9CC-41A3-95D2-8A655FF0B511
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 49f2bdf63c286464073308cd1f17239692aa2395
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84567327"
---
# <a name="working-with-the-watchos-parent-application-in-xamarin"></a>Trabalhando com o aplicativo pai watchOS no Xamarin

Há diferentes maneiras de se comunicar entre o aplicativo Watch e o aplicativo iOS em que ele é agrupado:

- Observe que os aplicativos podem [executar código](#run-code) no aplicativo pai no iPhone.

- As extensões de inspeção podem [compartilhar um local de armazenamento](#shared-storage) com o aplicativo pai do iPhone.

- Use a entrega para passar dados de uma notificação para o aplicativo Watch, enviando o usuário para um controlador de interface específico no aplicativo.

O aplicativo pai às vezes também é chamado de aplicativo de contêiner.

## <a name="run-code"></a>Executar código

Esses dois exemplos demonstram como usar `WCSession` o para executar código e enviar mensagens entre um aplicativo de inspeção e o iPhone emparelhado:

- [Assista à conectividade](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchconnectivity/)
- [SimpleWatchConnectivity](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-simplewatchconnectivity/) 

## <a name="shared-storage"></a>Armazenamento compartilhado

Se você configurar um [grupo de aplicativos](~/ios/watchos/app-fundamentals/app-groups.md) , as extensões do IOS 8 (incluindo extensões de inspeção) poderão compartilhar dados com o aplicativo pai.

### <a name="nsuserdefaults"></a>NSUserDefaults

O código a seguir pode ser escrito na extensão do aplicativo de inspeção e no aplicativo pai do iPhone para que eles possam fazer referência a um conjunto comum de `NSUserDefaults` :

```csharp
NSUserDefaults shared = new NSUserDefaults(
        "group.com.your-company.watchstuff",
        NSUserDefaultsType.SuiteName);

// set values
shared.SetInt (2, "count");
shared.Synchronize ();

// get values
shared.Synchronize ();
var count = shared.IntForKey ("count");
```

<a name="files"></a>

### <a name="files"></a>Arquivos

O aplicativo iOS e a extensão Watch também podem compartilhar arquivos usando um caminho de arquivo comum.

```csharp
var FileManager = new NSFileManager ();
var appGroupContainer =
            FileManager.GetContainerUrl ("group.com.your-company.watchstuff");
var appGroupContainerPath = appGroupContainer.Path;
Console.WriteLine ("agcpath: " + appGroupContainerPath);
// use the path to create and update files
```

Observação: se o caminho for `null` , verifique a [configuração do grupo de aplicativos](~/ios/watchos/app-fundamentals/app-groups.md) para garantir que os perfis de provisionamento tenham sido configurados corretamente e tenham sido baixados/instalados no computador de desenvolvimento.

Para obter mais informações, consulte a documentação de [recursos do grupo de aplicativos](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) .

## <a name="related-links"></a>Links relacionados

- [Referência do WKInterfaceController da Apple](https://developer.apple.com/library/prerelease/ios/documentation/WatchKit/Reference/WKInterfaceController_class/index.html#//apple_ref/occ/clm/WKInterfaceController/openParentApplication:reply:)
- [Dados de compartilhamento da Apple com seu aplicativo recipiente](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)
