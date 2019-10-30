---
title: Trabalhando com o aplicativo pai watchOS no Xamarin
description: Este documento descreve como trabalhar com um aplicativo pai watchOS no Xamarin. Ele aborda extensões de aplicativo WatchKit, aplicativos iOS, armazenamento compartilhado e muito mais.
ms.prod: xamarin
ms.assetid: 9AD29833-E9CC-41A3-95D2-8A655FF0B511
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 08637fe13b28565e7c6ab1c89b291c6db3b81025
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73001472"
---
# <a name="working-with-the-watchos-parent-application-in-xamarin"></a>Trabalhando com o aplicativo pai watchOS no Xamarin

> [!IMPORTANT]
> Acessar o aplicativo pai usando os exemplos abaixo só funciona em aplicativos watchOS 1 Watch.

Há diferentes maneiras de se comunicar entre o aplicativo Watch e o aplicativo iOS em que ele é agrupado:

- As extensões de inspeção podem [chamar um método](#code) em relação ao aplicativo pai que é executado em segundo plano no iPhone.

- As extensões de inspeção podem [compartilhar um local de armazenamento](#storage) com o aplicativo pai do iPhone.

- Usando a entrega para passar dados de uma visão geral ou de uma notificação para o aplicativo Watch, enviar o usuário para um controlador de interface específico no aplicativo.

O aplicativo pai às vezes também é chamado de aplicativo de contêiner.

<a name="code" />

## <a name="run-code"></a>Executar código

A comunicação entre uma extensão de inspeção e o aplicativo pai do iPhone é demonstrada no [exemplo GpsWatch](https://docs.microsoft.com/samples/xamarin/ios-samples/watchkit-gpswatch).
Sua extensão Watch pode solicitar que o aplicativo iOS pai faça algum processamento em seu nome usando o método `OpenParentApplication`.

Isso é especialmente útil para tarefas de longa execução (incluindo solicitações de rede) – somente o aplicativo iOS pai pode aproveitar o processamento em segundo plano para concluir essas tarefas e salvar os dados recuperados em um local acessível para a extensão Watch.

### <a name="watch-kit-app-extension"></a>Extensão do aplicativo kit de inspeção

Chame o `WKInterfaceController.OpenParentApplication` em sua extensão de aplicativo Watch. Ele retorna um `bool` que indica se a solicitação do método foi enviada com êxito ou não. Verifique o parâmetro `error` no `Action` de resposta para determinar se ocorreu algum erro durante o método em execução no aplicativo do iPhone.

```csharp
WKInterfaceController.OpenParentApplication (new NSDictionary (), (replyInfo, error) => {
    if(error != null) {
        Console.WriteLine (error);
        return;
    }
    Console.WriteLine ("parent app responded");
    // do something with replyInfo[] dictionary
});
```

### <a name="ios-app"></a>Aplicativo iOS

Todas as chamadas de uma extensão de aplicativo de inspeção são roteadas por meio do método de `HandleWatchKitExtensionRequest` do aplicativo do iPhone.
Se você estiver fazendo diferentes solicitações no aplicativo Watch, esse método precisará consultar o dicionário de `userInfo` para determinar como processar a solicitação.

```csharp
[Register ("AppDelegate")]
public partial class AppDelegate : UIApplicationDelegate
{
    // ... other AppDelegate methods
    public override void HandleWatchKitExtensionRequest
        (UIApplication application, NSDictionary userInfo, Action<NSDictionary> reply)
    {
        var status = 2;
        // do something in the background, and respond
        reply (new NSDictionary (
            "count", NSNumber.FromInt32 ((int)status),
            "value2", new NSString("some-info")
            ));
    }
}
```

<a name="storage" />

## <a name="shared-storage"></a>Armazenamento compartilhado

Se você configurar um [grupo de aplicativos](~/ios/watchos/app-fundamentals/app-groups.md) , as extensões do IOS 8 (incluindo extensões de inspeção) poderão compartilhar dados com o aplicativo pai.

<a name="nsuserdefaults" />

### <a name="nsuserdefaults"></a>NSUserDefaults

O código a seguir pode ser escrito na extensão do aplicativo de inspeção e no aplicativo pai do iPhone para que eles possam fazer referência a um conjunto comum de `NSUserDefaults`:

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

<a name="files" />

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

Observação: se o caminho for `null`, verifique a [configuração do grupo de aplicativos](~/ios/watchos/app-fundamentals/app-groups.md) para garantir que os perfis de provisionamento tenham sido configurados corretamente e tenham sido baixados/instalados no computador de desenvolvimento.

Para obter mais informações, consulte a documentação de [recursos do grupo de aplicativos](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) .

## <a name="wormholesharp"></a>WormHoleSharp

Um mecanismo de software livre popular para watchOS 1 (baseado em [MMWormHole](https://github.com/mutualmobile/MMWormhole)) para passar dados ou comandos entre o aplicativo pai e o aplicativo Watch.

Você pode configurar a fenda espacial usando um grupo de aplicativos como este em seu aplicativo iOS e a extensão de inspeção:

```csharp
// AppDelegate (iOS) or InterfaceController (watch extension)
Wormhole wormHole;
// ...
wormHole = new Wormhole ("group.com.your-company.watchstuff", "messageDir");
```

Baixe a C# versão [WormHoleSharp](https://github.com/Clancey/WormHoleSharp).

## <a name="related-links"></a>Links relacionados

- [GpsWatch (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [WormHoleSharp (exemplo)](https://github.com/Clancey/WormHoleSharp)
- [Referência do WKInterfaceController da Apple](https://developer.apple.com/library/prerelease/ios/documentation/WatchKit/Reference/WKInterfaceController_class/index.html#//apple_ref/occ/clm/WKInterfaceController/openParentApplication:reply:)
- [Dados de compartilhamento da Apple com seu aplicativo recipiente](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)
