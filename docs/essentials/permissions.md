---
title: 'Xamarin. Essentials: permissões'
description: Este documento descreve a classe de permissões no Xamarin. Essentials, que fornece a capacidade de verificar e solicitar permissões de tempo de execução.
ms.assetid: 34062D84-3E55-4AF7-A688-8551068B1E57
author: jamesmontemagno
ms.author: jamont
ms.custom: video
ms.date: 01/06/2020
ms.openlocfilehash: fbce02300363c3ec68c35c11afb25342f06f4be1
ms.sourcegitcommit: 83cf2a4d99546751c6394510a463a2b2a8bf75b8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83150076"
---
# <a name="xamarinessentials-permissions"></a>Xamarin. Essentials: permissões

A classe **Permissions** fornece a capacidade de verificar e solicitar permissões de tempo de execução.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

[!include[](~/essentials/includes/android-permissions.md)]

## <a name="using-permissions"></a>Usando permissões

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

## <a name="checking-permissions"></a>Verificando permissões

Para verificar o status atual de uma permissão, use o `CheckStatusAsync` método junto com a permissão específica para obter o status para.

```csharp
var status = await Permissions.CheckStatusAsync<Permissions.LocationWhenInUse>();
```

Um `PermissionException` será gerado se a permissão necessária não for declarada.

É melhor verificar o status da permissão antes de solicitá-la. Cada sistema operacional retornará um estado padrão diferente se o usuário nunca tiver sido solicitado. iOS retorna `Unknown` , enquanto outros retornam `Denied` .

## <a name="requesting-permissions"></a>Solicitando permissões

Para solicitar uma permissão dos usuários, use o `RequestAsync` método junto com a permissão específica para solicitar. Se o usuário concedeu permissão anteriormente e não o revogou, esse método retornará `Granted` imediatamente e não exibirá uma caixa de diálogo.

```csharp
var status = await Permissions.RequestAsync<Permissions.LocationWhenInUse>();
```

Um `PermissionException` será gerado se a permissão necessária não for declarada.

Observe que, em algumas plataformas, uma solicitação de permissão só pode ser ativada uma única vez. Solicitações adicionais devem ser manipuladas pelo desenvolvedor para verificar se uma permissão está no `Denied` estado e solicitar que o usuário a ative manualmente.

## <a name="permission-status"></a>Status da permissão

Ao usar `CheckStatusAsync` `RequestAsync` o ou um `PermissionStatus` será retornado que pode ser usado para determinar as próximas etapas:

* Desconhecido-a permissão está em um estado desconhecido
* Negado-o usuário negou a solicitação de permissão
* Desabilitado-o recurso está desabilitado no dispositivo
* Concedido-o usuário concedeu permissão ou é concedido automaticamente
* Restrito-em um estado restrito

## <a name="available-permissions"></a>Permissões disponíveis

O Xamarin. Essentials tenta abstrair o máximo de permissões possível. No entanto, cada sistema operacional tem um conjunto diferente de permissões de tempo de execução. Além disso, há diferenças ao fornecer uma única API para algumas permissões. Aqui está um guia para as permissões disponíveis no momento:

Guia de ícones:

* ![Suporte completo](~/media/shared/yes.png "suporte completo") -com suporte
* ![Sem suporte](~/media/shared/no.png "Sem suporte ou obrigatório") -sem suporte/obrigatório

| Permissão | Android | iOS | UWP | watchOS | tvOS | Tizen |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---:
| CalendarRead   | ![Com suporte para Android](~/media/shared/yes.png "Com suporte para Android") | ![iOS com suporte](~/media/shared/yes.png "iOS com suporte") | ![UWP sem suporte](~/media/shared/no.png "UWP sem suporte") | ![watchOS com suporte](~/media/shared/yes.png "watchOS com suporte") | ![tvOS não tem suporte](~/media/shared/no.png "tvOS não tem suporte") | ![Tizen não tem suporte](~/media/shared/no.png "Tizen não tem suporte") |
| CalendarWrite | ![Com suporte para Android](~/media/shared/yes.png "Com suporte para Android") | ![iOS com suporte](~/media/shared/yes.png "iOS com suporte") | ![UWP sem suporte](~/media/shared/no.png "UWP sem suporte") | ![watchOS com suporte](~/media/shared/yes.png "watchOS com suporte") | ![tvOS não tem suporte](~/media/shared/no.png "tvOS não tem suporte") | ![Tizen não tem suporte](~/media/shared/no.png "Tizen não tem suporte") |
| Câmera | ![Com suporte para Android](~/media/shared/yes.png "Com suporte para Android") | ![iOS com suporte](~/media/shared/yes.png "iOS com suporte") | ![UWP sem suporte](~/media/shared/no.png "UWP sem suporte") | ![watchOS não tem suporte](~/media/shared/no.png "watchOS não tem suporte") | ![tvOS não tem suporte](~/media/shared/no.png "tvOS não tem suporte") | ![Tizen com suporte](~/media/shared/yes.png "Tizen com suporte") |
| ContactsRead | ![Com suporte para Android](~/media/shared/yes.png "Com suporte para Android") | ![iOS com suporte](~/media/shared/yes.png "iOS com suporte") | ![UWP com suporte](~/media/shared/yes.png "UWP com suporte") | ![watchOS não tem suporte](~/media/shared/no.png "watchOS não tem suporte") | ![tvOS não tem suporte](~/media/shared/no.png "tvOS não tem suporte") | ![Tizen não tem suporte](~/media/shared/no.png "Tizen não tem suporte") |
| ContactsWrite | ![Com suporte para Android](~/media/shared/yes.png "Com suporte para Android") | ![iOS com suporte](~/media/shared/yes.png "iOS com suporte") | ![UWP com suporte](~/media/shared/yes.png "UWP com suporte") | ![watchOS não tem suporte](~/media/shared/no.png "watchOS não tem suporte") | ![tvOS não tem suporte](~/media/shared/no.png "tvOS não tem suporte") | ![Tizen não tem suporte](~/media/shared/no.png "Tizen não tem suporte") |
| Lanterna | ![Com suporte para Android](~/media/shared/yes.png "Com suporte para Android") | ![iOS sem suporte](~/media/shared/no.png "iOS sem suporte") | ![UWP sem suporte](~/media/shared/no.png "UWP sem suporte") | ![watchOS não tem suporte](~/media/shared/no.png "watchOS não tem suporte") | ![tvOS não tem suporte](~/media/shared/no.png "tvOS não tem suporte") | ![Tizen com suporte](~/media/shared/yes.png "Tizen com suporte") |
| LocationWhenInUse | ![Com suporte para Android](~/media/shared/yes.png "Com suporte para Android") | ![iOS com suporte](~/media/shared/yes.png "iOS com suporte") | ![UWP com suporte](~/media/shared/yes.png "UWP com suporte") | ![watchOS com suporte](~/media/shared/yes.png "watchOS com suporte") | ![tvOS com suporte](~/media/shared/yes.png "tvOS com suporte")  | ![Tizen com suporte](~/media/shared/yes.png "Tizen com suporte") |
| LocationAlways | ![Com suporte para Android](~/media/shared/yes.png "Com suporte para Android") | ![iOS com suporte](~/media/shared/yes.png "iOS com suporte") | ![UWP com suporte](~/media/shared/yes.png "UWP com suporte") | ![watchOS com suporte](~/media/shared/yes.png "watchOS com suporte") | ![tvOS não tem suporte](~/media/shared/no.png "tvOS não tem suporte") | ![Tizen com suporte](~/media/shared/yes.png "Tizen com suporte") |
| Mídia | ![Android sem suporte](~/media/shared/no.png "Android sem suporte") | ![iOS com suporte](~/media/shared/yes.png "iOS com suporte") | ![UWP sem suporte](~/media/shared/no.png "UWP sem suporte") | ![watchOS não tem suporte](~/media/shared/no.png "watchOS não tem suporte") | ![tvOS não tem suporte](~/media/shared/no.png "tvOS não tem suporte") | ![Tizen não tem suporte](~/media/shared/no.png "Tizen não tem suporte") |
| Microfone | ![Com suporte para Android](~/media/shared/yes.png "Com suporte para Android") | ![iOS com suporte](~/media/shared/yes.png "iOS com suporte") | ![UWP com suporte](~/media/shared/yes.png "UWP com suporte") | ![watchOS não tem suporte](~/media/shared/no.png "watchOS não tem suporte") | ![tvOS não tem suporte](~/media/shared/no.png "tvOS não tem suporte") | ![Tizen com suporte](~/media/shared/yes.png "Tizen com suporte") |
| Telefone | ![Com suporte para Android](~/media/shared/yes.png "Com suporte para Android") | ![iOS com suporte](~/media/shared/yes.png "iOS com suporte") | ![UWP sem suporte](~/media/shared/no.png "UWP sem suporte") | ![watchOS não tem suporte](~/media/shared/no.png "watchOS não tem suporte") | ![tvOS não tem suporte](~/media/shared/no.png "tvOS não tem suporte") | ![Tizen não tem suporte](~/media/shared/no.png "Tizen não tem suporte") |
| Fotos | ![Android sem suporte](~/media/shared/no.png "Android sem suporte") | ![iOS com suporte](~/media/shared/yes.png "iOS com suporte") | ![UWP sem suporte](~/media/shared/no.png "UWP sem suporte") | ![watchOS não tem suporte](~/media/shared/no.png "watchOS não tem suporte") | ![tvOS com suporte](~/media/shared/yes.png "tvOS com suporte") | ![Tizen não tem suporte](~/media/shared/no.png "Tizen não tem suporte") |
| Lembretes | ![Android sem suporte](~/media/shared/no.png "Android sem suporte") | ![iOS com suporte](~/media/shared/yes.png "iOS com suporte") | ![UWP sem suporte](~/media/shared/no.png "UWP sem suporte") | ![watchOS com suporte](~/media/shared/yes.png "watchOS com suporte") | ![tvOS não tem suporte](~/media/shared/no.png "tvOS não tem suporte") | ![Tizen não tem suporte](~/media/shared/no.png "Tizen não tem suporte") |
| Sensores | ![Com suporte para Android](~/media/shared/yes.png "Com suporte para Android") | ![iOS com suporte](~/media/shared/yes.png "iOS com suporte") | ![UWP com suporte](~/media/shared/yes.png "UWP com suporte") | ![watchOS com suporte](~/media/shared/yes.png "watchOS com suporte") | ![tvOS não tem suporte](~/media/shared/no.png "tvOS não tem suporte") | ![Tizen não tem suporte](~/media/shared/no.png "Tizen não tem suporte") |
| SMS | ![Com suporte para Android](~/media/shared/yes.png "Com suporte para Android") | ![iOS com suporte](~/media/shared/yes.png "iOS com suporte") | ![UWP sem suporte](~/media/shared/no.png "UWP sem suporte") | ![watchOS não tem suporte](~/media/shared/no.png "watchOS não tem suporte") | ![tvOS não tem suporte](~/media/shared/no.png "tvOS não tem suporte") | ![Tizen não tem suporte](~/media/shared/no.png "Tizen não tem suporte") |
| Speech | ![Com suporte para Android](~/media/shared/yes.png "Com suporte para Android") | ![iOS com suporte](~/media/shared/yes.png "iOS com suporte") | ![UWP sem suporte](~/media/shared/no.png "UWP sem suporte") | ![watchOS não tem suporte](~/media/shared/no.png "watchOS não tem suporte") | ![tvOS não tem suporte](~/media/shared/no.png "tvOS não tem suporte") | ![Tizen não tem suporte](~/media/shared/no.png "Tizen não tem suporte") |
| StorageRead | ![Com suporte para Android](~/media/shared/yes.png "Com suporte para Android") | ![iOS sem suporte](~/media/shared/no.png "iOS sem suporte") | ![UWP sem suporte](~/media/shared/no.png "UWP sem suporte") | ![watchOS não tem suporte](~/media/shared/no.png "watchOS não tem suporte") | ![tvOS não tem suporte](~/media/shared/no.png "tvOS não tem suporte") | ![Tizen não tem suporte](~/media/shared/no.png "Tizen não tem suporte") |
| StorageWrite | ![Com suporte para Android](~/media/shared/yes.png "Com suporte para Android") | ![iOS sem suporte](~/media/shared/no.png "iOS sem suporte") | ![UWP sem suporte](~/media/shared/no.png "UWP sem suporte") | ![watchOS não tem suporte](~/media/shared/no.png "watchOS não tem suporte") | ![tvOS não tem suporte](~/media/shared/no.png "tvOS não tem suporte") | ![Tizen não tem suporte](~/media/shared/no.png "Tizen não tem suporte") |

Se uma permissão for marcada como ![sem suporte](~/media/shared/no.png "sem suporte") , ela sempre retornará `Granted` quando for marcada ou solicitada.

## <a name="general-usage"></a>Uso geral
Aqui está um padrão de uso geral para lidar com permissões.

```csharp
public async Task<PermissionStatus> CheckAndRequestLocationPermission()
{
    var status = await Permissions.CheckStatusAsync<Permissions.LocationWhenInUse>();
    if (status != PermissionStatus.Granted)
    {
        status = await Permissions.RequestAsync<Permissions.LocationWhenInUse>();
    }

    // Additionally could prompt the user to turn on in settings

    return status;
}
```

Cada tipo de permissão pode ter uma instância dele criada para que os métodos possam ser chamados diretamente.

```csharp
public async Task GetLocationAsync()
{
    var status = await CheckAndRequestPermissionAsync(new Permissions.LocationWhenInUse());
    if (status != PermissionStatus.Granted)
    {
        // Notify user permission was denied
        return;
    }

    var location = await Geolocation.GetLocationAsync();
}

public async Task<PermissionStatus> CheckAndRequestPermissionAsync<T>(T permission)
            where T : BasePermission
{
    var status = await permission.CheckStatusAsync();
    if (status != PermissionStatus.Granted)
    {
        status = await permission.RequestAsync();
    }

    return status;
}
```

## <a name="extending-permissions"></a>Estendendo permissões

A API de permissões foi criada para ser flexível e extensível para aplicativos que exigem validação ou permissões adicionais que não estão incluídas no Xamarin. Essentials. Crie uma nova classe que herda de `BasePermission` e implemente os métodos abstratos necessários. Então

```csharp
public class MyPermission : BasePermission
{
    // This method checks if current status of the permission
    public override Task<PermissionStatus> CheckStatusAsync()
    {
        throw new System.NotImplementedException();
    }

    // This method is optional and a PermissionException is often thrown if a permission is not declared
    public override void EnsureDeclared()
    {
        throw new System.NotImplementedException();
    }

    // Requests the user to accept or deny a permission
    public override Task<PermissionStatus> RequestAsync()
    {
        throw new System.NotImplementedException();
    }
}
```

Ao implementar uma permissão em uma plataforma específica, a `BasePlatformPermission` classe pode ser herdada de. Isso fornece métodos auxiliares de plataforma adicionais para verificar automaticamente as declarações. Isso pode ajudar ao criar permissões personalizadas para fazer o agrupamento. Por exemplo, você pode solicitar o acesso de leitura e gravação ao armazenamento no Android usando a seguinte permissão personalizada.

Crie uma nova permissão em seu projeto do qual você está chamando permissões.

```csharp
public partial class ReadWriteStoragePermission  : Xamarin.Essentials.Permissions.BasePlatformPermission
{

}
```

Em seu projeto Android, estenda a permissão com permissões que você gostaria de solicitar.

```csharp
public partial class ReadWriteStoragePermission : Xamarin.Essentials.Permissions.BasePlatformPermission
{
    public override (string androidPermission, bool isRuntime)[] RequiredPermissions => new List<(string androidPermission, bool isRuntime)>
    {
        (Android.Manifest.Permission.ReadExternalStorage, true),
        (Android.Manifest.Permission.WriteExternalStorage, true)
    }.ToArray();
}
```

Em seguida, você pode chamar sua nova permissão da lógica compartilhada.

```csharp
await Permissions.RequestAsync<ReadWriteStoragePermission>();
```

## <a name="platform-implementation-specifics"></a>Particularidades de implementação da plataforma

# <a name="android"></a>[Android](#tab/android)

As permissões devem ter os atributos correspondentes definidos no arquivo de manifesto do Android.

Leia mais sobre as [permissões na documentação do Xamarin. Android](https://docs.microsoft.com/xamarin/android/app-fundamentals/permissions) .

# <a name="ios"></a>[iOS](#tab/ios)

As permissões devem ter uma cadeia de caracteres correspondente no `Info.plist` arquivo. Uma vez que uma permissão é solicitada e um pop-up não será mais exibido se você solicitar a permissão uma segunda vez. Você deve solicitar que o usuário ajuste manualmente a configuração na tela de configurações de aplicativos no iOS.

Leia mais sobre a documentação de [recursos de privacidade e segurança do IOS](https://docs.microsoft.com/xamarin/ios/app-fundamentals/security-privacy) .

# <a name="uwp"></a>[UWP](#tab/uwp)

As permissões devem ter recursos de correspondência declarados no manifesto do pacote.

Leia mais sobre a documentação da [declaração de capacidade do aplicativo](https://docs.microsoft.com/windows/uwp/packaging/app-capability-declarations) .

--------------

## <a name="api"></a>API

- [Código-fonte de permissões](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Permissions)
- [Documentação da API de permissões](xref:Xamarin.Essentials.Permissions)


## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Permissions-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
