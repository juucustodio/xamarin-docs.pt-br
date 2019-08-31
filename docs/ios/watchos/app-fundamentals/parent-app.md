---
title: Trabalhando com o aplicativo pai no Xamarin watchOS
description: Este documento descreve como trabalhar com um aplicativo do pai do watchOS no Xamarin. Ele aborda as extensões de aplicativo do WatchKit, aplicativos iOS, armazenamento compartilhado e muito mais.
ms.prod: xamarin
ms.assetid: 9AD29833-E9CC-41A3-95D2-8A655FF0B511
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 05dfb419834c2eee94f98d023df3a3fe8d6eee90
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2019
ms.locfileid: "70198122"
---
# <a name="working-with-the-watchos-parent-application-in-xamarin"></a>Trabalhando com o aplicativo pai no Xamarin watchOS

> [!IMPORTANT]
> Acessar o aplicativo pai usando os exemplos a seguir só funciona em aplicativos do watch watchOS 1.


Há diferentes maneiras de se comunicar entre o aplicativo de inspeção e o aplicativo iOS que ele é fornecido com:

- As extensões de inspeção podem [chamar um método](#code) em relação ao aplicativo pai que é executado em segundo plano no iPhone.

- Assista extensões can [compartilhar um local de armazenamento](#storage) com o aplicativo de iPhone pai.

- Usando a entrega para passar dados de uma visão geral ou de uma notificação para o aplicativo Watch, enviar o usuário para um controlador de interface específico no aplicativo.

O aplicativo pai, às vezes, também é conhecido como o aplicativo de contêiner.


<a name="code" />

## <a name="run-code"></a>Executar código

Comunicação entre uma extensão de inspeção e o aplicativo de iPhone pai é demonstrada na [GpsWatch exemplo](https://docs.microsoft.com/samples/xamarin/ios-samples/watchkit-gpswatch).
Sua extensão de inspeção pode solicitar o aplicativo do iOS pai fazer algum processamento em seu nome usando o `OpenParentApplication` método.

Isso é especialmente útil para tarefas em execução demorada (incluindo solicitações de rede) - somente o pai aplicativo iOS podem tirar proveito do processamento em segundo plano para concluir essas tarefas e salvar os dados recuperados em um local acessível para a extensão de inspeção.



### <a name="watch-kit-app-extension"></a>Assista a extensão do aplicativo de Kit

Chamar o `WKInterfaceController.OpenParentApplication` em sua extensão de aplicativo de inspeção. Ele retorna um `bool` que indica se a solicitação do método foi enviada com êxito ou não. Verifique as `error` parâmetro na resposta `Action` determinar se qualquer ocorreu durante o método em execução no aplicativo do iPhone.

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

Todas as chamadas de uma extensão de aplicativo de inspeção são roteadas por meio do aplicativo do iPhone `HandleWatchKitExtensionRequest` método.
Se você estiver fazendo solicitações diferentes no aplicativo de inspeção, esse método terá de consultar o `userInfo` dicionário para determinar como processar a solicitação.


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

Se você configurar uma [grupo de aplicativos](~/ios/watchos/app-fundamentals/app-groups.md) e extensões do iOS 8 (incluindo extensões de inspeção) podem compartilhar dados com o aplicativo pai.

<a name="nsuserdefaults" />

### <a name="nsuserdefaults"></a>NSUserDefaults

O código a seguir pode ser escrito na extensão de aplicativo de inspeção e o aplicativo de iPhone pai para que eles podem fazer referência a um conjunto comum de `NSUserDefaults`:

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

A extensão de aplicativo e a inspeção do iOS também pode compartilhar arquivos usando um caminho de arquivo comum.

```csharp
var FileManager = new NSFileManager ();
var appGroupContainer =
            FileManager.GetContainerUrl ("group.com.your-company.watchstuff");
var appGroupContainerPath = appGroupContainer.Path;
Console.WriteLine ("agcpath: " + appGroupContainerPath);
// use the path to create and update files
```

Observação: se o caminho estiver `null` , em seguida, verifique a [configuração do grupo de aplicativo](~/ios/watchos/app-fundamentals/app-groups.md) para garantir que os perfis de provisionamento foram configurados corretamente e tem sido baixado/instalado no computador de desenvolvimento.

Para obter mais informações, consulte o [recursos do grupo de aplicativo](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) documentação.

## <a name="wormholesharp"></a>WormHoleSharp

Um mecanismo de código-fonte aberto popular para watchOS 1 (com base em [MMWormHole](https://github.com/mutualmobile/MMWormhole)) para passar dados ou comandos entre o aplicativo pai e o aplicativo de inspeção.

Você pode configurar WormHole usando um grupo de aplicativos como este em seu aplicativo iOS e assista a extensão:

```csharp
// AppDelegate (iOS) or InterfaceController (watch extension)
Wormhole wormHole;
// ...
wormHole = new Wormhole ("group.com.your-company.watchstuff", "messageDir");
```

Baixe o C# versão [WormHoleSharp](https://github.com/Clancey/WormHoleSharp).



## <a name="related-links"></a>Links relacionados

- [GpsWatch (amostra)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [WormHoleSharp (amostra)](https://github.com/Clancey/WormHoleSharp)
- [Referência de WKInterfaceController da Apple](https://developer.apple.com/library/prerelease/ios/documentation/WatchKit/Reference/WKInterfaceController_class/index.html#//apple_ref/occ/clm/WKInterfaceController/openParentApplication:reply:)
- [Apple compartilhamento de dados com seu aplicativo contém](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)
