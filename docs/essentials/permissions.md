---
title: 'Xamarin.Essentials: Permissões'
description: Este documento descreve a classe Permissions no Xamarin.Essentials, que fornece a habilidade de verificar e solicitar permissões de tempo de execução.
ms.assetid: 34062D84-3E55-4AF7-A688-8551068B1E57
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.openlocfilehash: 2b1413c6bce7fc4ce43a190cd25494c69dbadedb
ms.sourcegitcommit: ec62c7f28abc8e121656f1b93146657d90a4cab4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77555684"
---
# <a name="xamarinessentials-permissions"></a>Xamarin.Essentials: Permissões

A classe **Permissions** fornece a capacidade de verificar e solicitar permissões de tempo de execução.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-permissions"></a>Usar permissões

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

## <a name="checking-permissions"></a>Verificar permissões

Para verificar o status atual de uma permissão, use o método `CheckStatusAsync` com a permissão específica para obter o status.

```csharp
var status = await Permissions.CheckStatusAsync<Permissions.LocationWhenInUse>();
```

Um `PermissionException` será gerado se a permissão necessária não for declarada.

## <a name="requesting-permissions"></a>Solicitando permissões

Para solicitar uma permissão dos usuários, use o método `RequestAsync` com a permissão específica. Se o usuário concedeu a permissão anteriormente e não a revogou, esse método retornará `Granted` imediatamente e não exibirá uma caixa de diálogo. 

```csharp
var status = await Permissions.RequestAsync<Permissions.LocationWhenInUse>();
```

Um `PermissionException` será gerado se a permissão necessária não for declarada. 

Observe que, em algumas plataformas, a solicitação de permissão só pode ser ativada uma única vez. Solicitações adicionais devem ser tratadas pelo desenvolvedor para verificar se a permissão está no estado `Denied` e pedir ao usuário para ativá-la manualmente.

## <a name="permission-status"></a>Status da permissão

Ao usar `CheckStatusAsync` ou `RequestAsync`, será retornado `PermissionStatus`, que será usado para determinar as próximas etapas.

* Desconhecido – A permissão está em um estado desconhecido
* Negado – O usuário negou a solicitação de permissão
* Desativado – O recurso está desabilitado no dispositivo
* Concedido – O usuário concedeu permissão ou ela foi concedida automaticamente
* Restrito – Em um estado restrito

## <a name="available-permissions"></a>Permissões disponíveis

O Xamarin.Essentials tenta abstrair o máximo possível de permissões, mas cada sistema operacional tem um conjunto diferente de permissões de tempo de execução. Além disso, há diferenças na capacidade de fornecer uma única API para algumas permissões. Aqui está um guia para as permissões atualmente disponíveis:

Guia de ícones:

* ![Suporte Completo](~/media/shared/yes.png "suporte completo") – Com suporte
* ![Sem Suporte](~/media/shared/no.png "Incompatível ou não solicitado") – Incompatível/não solicitado

| Permissão | Android | iOS | UWP | watchOS | tvOS | Tizen |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: 
| CalendarRead   | ![Compatível com Android](~/media/shared/yes.png "Compatível com Android") | ![Compatível com iOS](~/media/shared/yes.png "Compatível com iOS") | ![Incompatível com UWP](~/media/shared/no.png "Incompatível com UWP") | ![Compatível com watchOS](~/media/shared/yes.png "Compatível com watchOS") | ![Incompatível com tvOS](~/media/shared/no.png "Incompatível com tvOS") | ![Incompatível com Tizen](~/media/shared/no.png "Incompatível com Tizen") |
| CalendarWrite | ![Compatível com Android](~/media/shared/yes.png "Compatível com Android") | ![Compatível com iOS](~/media/shared/yes.png "Compatível com iOS") | ![Incompatível com UWP](~/media/shared/no.png "Incompatível com UWP") | ![Compatível com watchOS](~/media/shared/yes.png "Compatível com watchOS") | ![Incompatível com tvOS](~/media/shared/no.png "Incompatível com tvOS") | ![Incompatível com Tizen](~/media/shared/no.png "Incompatível com Tizen") |
| Câmera | ![Compatível com Android](~/media/shared/yes.png "Compatível com Android") | ![Compatível com iOS](~/media/shared/yes.png "Compatível com iOS") | ![Incompatível com UWP](~/media/shared/no.png "Incompatível com UWP") | ![Incompatível com watchOS](~/media/shared/no.png "Incompatível com watchOS") | ![Incompatível com tvOS](~/media/shared/no.png "Incompatível com tvOS") | ![Compatível com Tizen](~/media/shared/yes.png "Compatível com Tizen") |
| ContactsRead | ![Compatível com Android](~/media/shared/yes.png "Compatível com Android") | ![Compatível com iOS](~/media/shared/yes.png "Compatível com iOS") | ![Compatível com UWP](~/media/shared/yes.png "Compatível com UWP") | ![Incompatível com watchOS](~/media/shared/no.png "Incompatível com watchOS") | ![Incompatível com tvOS](~/media/shared/no.png "Incompatível com tvOS") | ![Incompatível com Tizen](~/media/shared/no.png "Incompatível com Tizen") |
| ContactsWrite | ![Compatível com Android](~/media/shared/yes.png "Compatível com Android") | ![Compatível com iOS](~/media/shared/yes.png "Compatível com iOS") | ![Compatível com UWP](~/media/shared/yes.png "Compatível com UWP") | ![Incompatível com watchOS](~/media/shared/no.png "Incompatível com watchOS") | ![Incompatível com tvOS](~/media/shared/no.png "Incompatível com tvOS") | ![Incompatível com Tizen](~/media/shared/no.png "Incompatível com Tizen") |
| Lanterna | ![Compatível com Android](~/media/shared/yes.png "Compatível com Android") | ![Incompatível com iOS](~/media/shared/no.png "Incompatível com iOS") | ![Incompatível com UWP](~/media/shared/no.png "Incompatível com UWP") | ![Incompatível com watchOS](~/media/shared/no.png "Incompatível com watchOS") | ![Incompatível com tvOS](~/media/shared/no.png "Incompatível com tvOS") | ![Compatível com Tizen](~/media/shared/yes.png "Compatível com Tizen") |
| LocationWhenInUse | ![Compatível com Android](~/media/shared/yes.png "Compatível com Android") | ![Compatível com iOS](~/media/shared/yes.png "Compatível com iOS") | ![Compatível com UWP](~/media/shared/yes.png "Compatível com UWP") | ![Compatível com watchOS](~/media/shared/yes.png "Compatível com watchOS") | ![Compatível com tvOS](~/media/shared/yes.png "Compatível com tvOS")  | ![Compatível com Tizen](~/media/shared/yes.png "Compatível com Tizen") |
| LocationAlways | ![Compatível com Android](~/media/shared/yes.png "Compatível com Android") | ![Compatível com iOS](~/media/shared/yes.png "Compatível com iOS") | ![Compatível com UWP](~/media/shared/yes.png "Compatível com UWP") | ![Compatível com watchOS](~/media/shared/yes.png "Compatível com watchOS") | ![Incompatível com tvOS](~/media/shared/no.png "Incompatível com tvOS") | ![Compatível com Tizen](~/media/shared/yes.png "Compatível com Tizen") |
| Mídia | ![Incompatível com Android](~/media/shared/no.png "Incompatível com Android") | ![Compatível com iOS](~/media/shared/yes.png "Compatível com iOS") | ![Incompatível com UWP](~/media/shared/no.png "Incompatível com UWP") | ![Incompatível com watchOS](~/media/shared/no.png "Incompatível com watchOS") | ![Incompatível com tvOS](~/media/shared/no.png "Incompatível com tvOS") | ![Incompatível com Tizen](~/media/shared/no.png "Incompatível com Tizen") |
| Microphone | ![Compatível com Android](~/media/shared/yes.png "Compatível com Android") | ![Compatível com iOS](~/media/shared/yes.png "Compatível com iOS") | ![Compatível com UWP](~/media/shared/yes.png "Compatível com UWP") | ![Incompatível com watchOS](~/media/shared/no.png "Incompatível com watchOS") | ![Incompatível com tvOS](~/media/shared/no.png "Incompatível com tvOS") | ![Compatível com Tizen](~/media/shared/yes.png "Compatível com Tizen") |
| Telefone | ![Compatível com Android](~/media/shared/yes.png "Compatível com Android") | ![Compatível com iOS](~/media/shared/yes.png "Compatível com iOS") | ![Incompatível com UWP](~/media/shared/no.png "Incompatível com UWP") | ![Incompatível com watchOS](~/media/shared/no.png "Incompatível com watchOS") | ![Incompatível com tvOS](~/media/shared/no.png "Incompatível com tvOS") | ![Incompatível com Tizen](~/media/shared/no.png "Incompatível com Tizen") |
| Fotos | ![Incompatível com Android](~/media/shared/no.png "Incompatível com Android") | ![Compatível com iOS](~/media/shared/yes.png "Compatível com iOS") | ![Incompatível com UWP](~/media/shared/no.png "Incompatível com UWP") | ![Incompatível com watchOS](~/media/shared/no.png "Incompatível com watchOS") | ![Compatível com tvOS](~/media/shared/yes.png "Compatível com tvOS") | ![Incompatível com Tizen](~/media/shared/no.png "Incompatível com Tizen") |
| Lembretes | ![Incompatível com Android](~/media/shared/no.png "Incompatível com Android") | ![Compatível com iOS](~/media/shared/yes.png "Compatível com iOS") | ![Incompatível com UWP](~/media/shared/no.png "Incompatível com UWP") | ![Compatível com watchOS](~/media/shared/yes.png "Compatível com watchOS") | ![Incompatível com tvOS](~/media/shared/no.png "Incompatível com tvOS") | ![Incompatível com Tizen](~/media/shared/no.png "Incompatível com Tizen") |
| Sensores | ![Compatível com Android](~/media/shared/yes.png "Compatível com Android") | ![Compatível com iOS](~/media/shared/yes.png "Compatível com iOS") | ![Compatível com UWP](~/media/shared/yes.png "Compatível com UWP") | ![Compatível com watchOS](~/media/shared/yes.png "Compatível com watchOS") | ![Incompatível com tvOS](~/media/shared/no.png "Incompatível com tvOS") | ![Incompatível com Tizen](~/media/shared/no.png "Incompatível com Tizen") |
| SMS | ![Compatível com Android](~/media/shared/yes.png "Compatível com Android") | ![Compatível com iOS](~/media/shared/yes.png "Compatível com iOS") | ![Incompatível com UWP](~/media/shared/no.png "Incompatível com UWP") | ![Incompatível com watchOS](~/media/shared/no.png "Incompatível com watchOS") | ![Incompatível com tvOS](~/media/shared/no.png "Incompatível com tvOS") | ![Incompatível com Tizen](~/media/shared/no.png "Incompatível com Tizen") |
| Fala | ![Compatível com Android](~/media/shared/yes.png "Compatível com Android") | ![Compatível com iOS](~/media/shared/yes.png "Compatível com iOS") | ![Incompatível com UWP](~/media/shared/no.png "Incompatível com UWP") | ![Incompatível com watchOS](~/media/shared/no.png "Incompatível com watchOS") | ![Incompatível com tvOS](~/media/shared/no.png "Incompatível com tvOS") | ![Incompatível com Tizen](~/media/shared/no.png "Incompatível com Tizen") |
| StorageRead | ![Compatível com Android](~/media/shared/yes.png "Compatível com Android") | ![Incompatível com iOS](~/media/shared/no.png "Incompatível com iOS") | ![Incompatível com UWP](~/media/shared/no.png "Incompatível com UWP") | ![Incompatível com watchOS](~/media/shared/no.png "Incompatível com watchOS") | ![Incompatível com tvOS](~/media/shared/no.png "Incompatível com tvOS") | ![Incompatível com Tizen](~/media/shared/no.png "Incompatível com Tizen") |
| StorageWrite | ![Compatível com Android](~/media/shared/yes.png "Compatível com Android") | ![Incompatível com iOS](~/media/shared/no.png "Incompatível com iOS") | ![Incompatível com UWP](~/media/shared/no.png "Incompatível com UWP") | ![Incompatível com watchOS](~/media/shared/no.png "Incompatível com watchOS") | ![Incompatível com tvOS](~/media/shared/no.png "Incompatível com tvOS") | ![Incompatível com Tizen](~/media/shared/no.png "Incompatível com Tizen") |

Se uma permissão for marcada com ❌, sempre retornará `Granted` ao ser solicitada ou verificada.

## <a name="general-usage"></a>Uso geral
Aqui está um padrão de uso geral para manipular permissões.

```csharp
public async Task<PermissionStatus> CheckAndRequestPermissionAsync<TPermission>()
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

Cada tipo de permissão pode ter uma instância criada para que os métodos possam ser chamados diretamente.

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

## <a name="extending-permissions"></a>Estender Permissões

A API de Permissões foi criada para ser flexível e extensível para aplicativos que necessitem de validações ou permissões adicionais ou de permissões que não estejam incluídas no Xamarin.Essentials. Crie uma nova classe que herda de `BasePermission` e implemente os métodos de abstração necessários. Then 

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

Ao implementar uma permissão de uma plataforma específica, a classe `BasePlatformPermission` pode ser herdada. Isso fornece métodos auxiliares adicionais para a plataforma para verificar automaticamente as declarações.

## <a name="platform-implementation-specifics"></a>Particularidades de implementação da plataforma

# <a name="android"></a>[Android](#tab/android)

As permissões devem ter os atributos correspondentes definidos no arquivo de Manifesto do Android.

Leia mais na documentação de [Permissões no Xamarin.Android](https://docs.microsoft.com/xamarin/android/app-fundamentals/permissions).

# <a name="ios"></a>[iOS](#tab/ios)

As permissões devem ter uma cadeia de caracteres correspondente no arquivo `Info.plist`. Assim que uma permissão for solicitada e negada, uma janela pop-up não será mais exibida se você solicitar novamente a permissão. Em um prompt, você deve solicitar ao usuário para ajustar a configuração na tela de configurações de aplicativos do iOS.

Leia mais na documentação de [Recursos de segurança e privacidade do iOS](https://docs.microsoft.com/xamarin/ios/app-fundamentals/security-privacy).

# <a name="uwp"></a>[UWP](#tab/uwp)

As permissões devem ter recursos correspondentes declarados no manifesto do pacote.

Leia mais na documentação da [Declaração de funcionalidades de aplicativos](https://docs.microsoft.com/windows/uwp/packaging/app-capability-declarations).

--------------

## <a name="api"></a>API

- [Código-fonte de Permissions](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Permissions)
- [Documentação da API de Permissions](xref:Xamarin.Essentials.Permissions)

