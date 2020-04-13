---
title: 'Xamarin.Essentials: Permissões'
description: Este documento descreve a classe Permissões em Xamarin.Essentials, que fornece a capacidade de verificar e solicitar permissões de tempo de execução.
ms.assetid: 34062D84-3E55-4AF7-A688-8551068B1E57
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.openlocfilehash: 21f2079ace4adae6fd84d89426e5d66692af2a0a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78289803"
---
# <a name="xamarinessentials-permissions"></a>Xamarin.Essentials: Permissões

A classe **Permissões** fornece a capacidade de verificar e solicitar permissões de tempo de execução.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-permissions"></a>Usando permissões

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

## <a name="checking-permissions"></a>Verificando permissões

Para verificar o status atual de `CheckStatusAsync` uma permissão, use o método juntamente com a permissão específica para obter o status.

```csharp
var status = await Permissions.CheckStatusAsync<Permissions.LocationWhenInUse>();
```

A `PermissionException` é jogado se a permissão necessária não for declarada.

## <a name="requesting-permissions"></a>Solicitando permissões

Para solicitar uma permissão dos `RequestAsync` usuários, use o método juntamente com a permissão específica para solicitar. Se o usuário anteriormente concedeu permissão e não `Granted` a revogou, então este método retornará imediatamente e não exibirá uma caixa de diálogo. 

```csharp
var status = await Permissions.RequestAsync<Permissions.LocationWhenInUse>();
```

A `PermissionException` é jogado se a permissão necessária não for declarada. 

Observe que em algumas plataformas uma solicitação de permissão só pode ser ativada uma única vez. Outras solicitações devem ser tratadas pelo desenvolvedor para verificar se `Denied` uma permision está no estado e pedir ao usuário para atilá-lo manualmente.

## <a name="permission-status"></a>Status de permissão

Ao `CheckStatusAsync` usar `RequestAsync` `PermissionStatus` ou a será devolvido que será usado para determinar os próximos passos.

* Desconhecido - A permissão está em um estado desconhecido
* Negado - O usuário negou o pedido de permissão
* Desativado - O recurso está desativado no dispositivo
* Concedido - O usuário concedeu permissão ou é automaticamente concedido
* Restrito - Em estado restrito

## <a name="available-permissions"></a>Permissões disponíveis

Xamarin.Essentials tenta abstrair o maior número possível de permissões, no entanto cada sistema operacional tem um conjunto diferente de permissões de tempo de execução. Além disso, há diferenças em poder fornecer uma única API para algumas permissões. Aqui está um guia para as permissões disponíveis no momento:

Guia de ícones:

* ![Suporte completo](~/media/shared/yes.png "suporte completo") - Suportado
* ![Não suportado](~/media/shared/no.png "Não suportado ou necessário") - Não suportado/necessário

| Permissão | Android | iOS | UWP | watchOS | tvOS | Tizen |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: 
| CalendárioRead   | ![Android suportado](~/media/shared/yes.png "Android suportado") | ![Suporte para iOS](~/media/shared/yes.png "Suporte para iOS") | ![UWP não suportado](~/media/shared/no.png "UWP não suportado") | ![watchOS suportado](~/media/shared/yes.png "watchOS suportado") | ![tvOS não suportado](~/media/shared/no.png "tvOS não suportado") | ![Tizen não suportado](~/media/shared/no.png "Tizen não suportado") |
| CalendárioEscrever | ![Android suportado](~/media/shared/yes.png "Android suportado") | ![Suporte para iOS](~/media/shared/yes.png "Suporte para iOS") | ![UWP não suportado](~/media/shared/no.png "UWP não suportado") | ![watchOS suportado](~/media/shared/yes.png "watchOS suportado") | ![tvOS não suportado](~/media/shared/no.png "tvOS não suportado") | ![Tizen não suportado](~/media/shared/no.png "Tizen não suportado") |
| Câmera | ![Android suportado](~/media/shared/yes.png "Android suportado") | ![Suporte para iOS](~/media/shared/yes.png "Suporte para iOS") | ![UWP não suportado](~/media/shared/no.png "UWP não suportado") | ![watchOS não suportado](~/media/shared/no.png "watchOS não suportado") | ![tvOS não suportado](~/media/shared/no.png "tvOS não suportado") | ![Tizen apoiado](~/media/shared/yes.png "Tizen apoiado") |
| ContatosLeia | ![Android suportado](~/media/shared/yes.png "Android suportado") | ![Suporte para iOS](~/media/shared/yes.png "Suporte para iOS") | ![UWP suportado](~/media/shared/yes.png "UWP suportado") | ![watchOS não suportado](~/media/shared/no.png "watchOS não suportado") | ![tvOS não suportado](~/media/shared/no.png "tvOS não suportado") | ![Tizen não suportado](~/media/shared/no.png "Tizen não suportado") |
| ContatosEscrever | ![Android suportado](~/media/shared/yes.png "Android suportado") | ![Suporte para iOS](~/media/shared/yes.png "Suporte para iOS") | ![UWP suportado](~/media/shared/yes.png "UWP suportado") | ![watchOS não suportado](~/media/shared/no.png "watchOS não suportado") | ![tvOS não suportado](~/media/shared/no.png "tvOS não suportado") | ![Tizen não suportado](~/media/shared/no.png "Tizen não suportado") |
| Lanterna | ![Android suportado](~/media/shared/yes.png "Android suportado") | ![iOS não suportado](~/media/shared/no.png "iOS não suportado") | ![UWP não suportado](~/media/shared/no.png "UWP não suportado") | ![watchOS não suportado](~/media/shared/no.png "watchOS não suportado") | ![tvOS não suportado](~/media/shared/no.png "tvOS não suportado") | ![Tizen apoiado](~/media/shared/yes.png "Tizen apoiado") |
| LocalizaçãoWhenInuse | ![Android suportado](~/media/shared/yes.png "Android suportado") | ![Suporte para iOS](~/media/shared/yes.png "Suporte para iOS") | ![UWP suportado](~/media/shared/yes.png "UWP suportado") | ![watchOS suportado](~/media/shared/yes.png "watchOS suportado") | ![tvOS suportado](~/media/shared/yes.png "tvOS suportado")  | ![Tizen apoiado](~/media/shared/yes.png "Tizen apoiado") |
| LocalizaçãoSempre | ![Android suportado](~/media/shared/yes.png "Android suportado") | ![Suporte para iOS](~/media/shared/yes.png "Suporte para iOS") | ![UWP suportado](~/media/shared/yes.png "UWP suportado") | ![watchOS suportado](~/media/shared/yes.png "watchOS suportado") | ![tvOS não suportado](~/media/shared/no.png "tvOS não suportado") | ![Tizen apoiado](~/media/shared/yes.png "Tizen apoiado") |
| Mídia | ![Android não suportado](~/media/shared/no.png "Android não suportado") | ![Suporte para iOS](~/media/shared/yes.png "Suporte para iOS") | ![UWP não suportado](~/media/shared/no.png "UWP não suportado") | ![watchOS não suportado](~/media/shared/no.png "watchOS não suportado") | ![tvOS não suportado](~/media/shared/no.png "tvOS não suportado") | ![Tizen não suportado](~/media/shared/no.png "Tizen não suportado") |
| Microfone | ![Android suportado](~/media/shared/yes.png "Android suportado") | ![Suporte para iOS](~/media/shared/yes.png "Suporte para iOS") | ![UWP suportado](~/media/shared/yes.png "UWP suportado") | ![watchOS não suportado](~/media/shared/no.png "watchOS não suportado") | ![tvOS não suportado](~/media/shared/no.png "tvOS não suportado") | ![Tizen apoiado](~/media/shared/yes.png "Tizen apoiado") |
| Telefone | ![Android suportado](~/media/shared/yes.png "Android suportado") | ![Suporte para iOS](~/media/shared/yes.png "Suporte para iOS") | ![UWP não suportado](~/media/shared/no.png "UWP não suportado") | ![watchOS não suportado](~/media/shared/no.png "watchOS não suportado") | ![tvOS não suportado](~/media/shared/no.png "tvOS não suportado") | ![Tizen não suportado](~/media/shared/no.png "Tizen não suportado") |
| Fotos | ![Android não suportado](~/media/shared/no.png "Android não suportado") | ![Suporte para iOS](~/media/shared/yes.png "Suporte para iOS") | ![UWP não suportado](~/media/shared/no.png "UWP não suportado") | ![watchOS não suportado](~/media/shared/no.png "watchOS não suportado") | ![tvOS suportado](~/media/shared/yes.png "tvOS suportado") | ![Tizen não suportado](~/media/shared/no.png "Tizen não suportado") |
| Lembretes | ![Android não suportado](~/media/shared/no.png "Android não suportado") | ![Suporte para iOS](~/media/shared/yes.png "Suporte para iOS") | ![UWP não suportado](~/media/shared/no.png "UWP não suportado") | ![watchOS suportado](~/media/shared/yes.png "watchOS suportado") | ![tvOS não suportado](~/media/shared/no.png "tvOS não suportado") | ![Tizen não suportado](~/media/shared/no.png "Tizen não suportado") |
| Sensores | ![Android suportado](~/media/shared/yes.png "Android suportado") | ![Suporte para iOS](~/media/shared/yes.png "Suporte para iOS") | ![UWP suportado](~/media/shared/yes.png "UWP suportado") | ![watchOS suportado](~/media/shared/yes.png "watchOS suportado") | ![tvOS não suportado](~/media/shared/no.png "tvOS não suportado") | ![Tizen não suportado](~/media/shared/no.png "Tizen não suportado") |
| Sms | ![Android suportado](~/media/shared/yes.png "Android suportado") | ![Suporte para iOS](~/media/shared/yes.png "Suporte para iOS") | ![UWP não suportado](~/media/shared/no.png "UWP não suportado") | ![watchOS não suportado](~/media/shared/no.png "watchOS não suportado") | ![tvOS não suportado](~/media/shared/no.png "tvOS não suportado") | ![Tizen não suportado](~/media/shared/no.png "Tizen não suportado") |
| Fala | ![Android suportado](~/media/shared/yes.png "Android suportado") | ![Suporte para iOS](~/media/shared/yes.png "Suporte para iOS") | ![UWP não suportado](~/media/shared/no.png "UWP não suportado") | ![watchOS não suportado](~/media/shared/no.png "watchOS não suportado") | ![tvOS não suportado](~/media/shared/no.png "tvOS não suportado") | ![Tizen não suportado](~/media/shared/no.png "Tizen não suportado") |
| ArmazenamentoRead | ![Android suportado](~/media/shared/yes.png "Android suportado") | ![iOS não suportado](~/media/shared/no.png "iOS não suportado") | ![UWP não suportado](~/media/shared/no.png "UWP não suportado") | ![watchOS não suportado](~/media/shared/no.png "watchOS não suportado") | ![tvOS não suportado](~/media/shared/no.png "tvOS não suportado") | ![Tizen não suportado](~/media/shared/no.png "Tizen não suportado") |
| ArmazenamentoWrite | ![Android suportado](~/media/shared/yes.png "Android suportado") | ![iOS não suportado](~/media/shared/no.png "iOS não suportado") | ![UWP não suportado](~/media/shared/no.png "UWP não suportado") | ![watchOS não suportado](~/media/shared/no.png "watchOS não suportado") | ![tvOS não suportado](~/media/shared/no.png "tvOS não suportado") | ![Tizen não suportado](~/media/shared/no.png "Tizen não suportado") |

Se uma permissão estiver marcada como `Granted` ![não suportada,](~/media/shared/no.png "sem suporte") ela sempre retornará quando verificada ou solicitada.

## <a name="general-usage"></a>Uso geral
Aqui está um patter de uso geral para o manuseio de permissões.

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

Cada tipo de permissão pode ter uma instância de que os métodos podem ser chamados diretamente.

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

## <a name="extending-permissions"></a>Prorrogação de permissões

A API permissões foi criada para ser flexível e extensível para aplicativos que requerem validação adicional ou permissões que não estão incluídas no Xamarin.Essentials. Crie uma nova classe `BasePermission` que herde e implemente os métodos abstratos necessários. Então 

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

Ao implementar uma permissão em `BasePlatformPermission` uma plataforma específica, a classe pode ser herdada. Isso fornece métodos adicionais de ajuda à plataforma para verificar automaticamente as declarações.

## <a name="platform-implementation-specifics"></a>Particularidades de implementação da plataforma

# <a name="android"></a>[Android](#tab/android)

As permissões devem ter os atributos correspondentes definidos no arquivo Manifesto do Android.

Leia mais sobre as permissões na documentação [Xamarin.Android.](https://docs.microsoft.com/xamarin/android/app-fundamentals/permissions)

# <a name="ios"></a>[iOS](#tab/ios)

As permissões devem ter `Info.plist` uma seqüência correspondente no arquivo. Uma vez que uma permissão é solicitada e negada, um pop-up não aparecerá mais se você solicitar a permissão uma segunda vez. Você deve solicitar ao usuário que ajuste manualmente a configuração na tela de configurações de aplicativos no iOS.

Leia mais sobre a documentação dos Recursos de Segurança e Privacidade do [iOS.](https://docs.microsoft.com/xamarin/ios/app-fundamentals/security-privacy)

# <a name="uwp"></a>[UWP](#tab/uwp)

As permissões devem ter recursos de correspondência declarados no manifesto do pacote.

Leia mais na documentação da [Declaração de Capacidade](https://docs.microsoft.com/windows/uwp/packaging/app-capability-declarations) do Aplicativo.

--------------

## <a name="api"></a>API

- [Código-fonte de permissões](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Permissions)
- [Documentação de API de permissões](xref:Xamarin.Essentials.Permissions)

