---
title: Xamarin.Forms Inicialização e configuração do mapa
description: O Xamarin.Forms . O pacote NuGet de mapas é necessário para usar a funcionalidade de mapas em um aplicativo. Além disso, o acesso ao local do usuário exige que as permissões de localização tenham sido concedidas ao aplicativo.
ms.prod: xamarin
ms.assetid: 59CD1344-8248-406C-9144-0C8A67141E5B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/07/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3da0223bf72e4de60cc50be2562a0fdbd279f52e
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91559734"
---
# <a name="no-locxamarinforms-map-initialization-and-configuration"></a>Xamarin.Forms Inicialização e configuração do mapa

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

O [`Map`](xref:Xamarin.Forms.Maps.Map) controle usa o controle de mapa nativo em cada plataforma. Isso fornece uma experiência de mapas rápida e familiar para os usuários, mas significa que algumas etapas de configuração são necessárias para aderir a cada uma das plataformas requisitos de API.

## <a name="map-initialization"></a>Inicialização do mapa

O [`Map`](xref:Xamarin.Forms.Maps.Map) controle é fornecido pelo [ Xamarin.Forms . Mapeia](https://www.nuget.org/packages/Xamarin.Forms.Maps/) o pacote NuGet, que deve ser adicionado a todos os projetos na solução.

Depois de instalar o [ Xamarin.Forms . Mapeia](https://www.nuget.org/packages/Xamarin.Forms.Maps/) o pacote NuGet, ele deve ser inicializado em cada projeto de plataforma.

No iOS, isso deve ocorrer em **AppDelegate.cs** invocando o `Xamarin.FormsMaps.Init` método *após* o `Xamarin.Forms.Forms.Init` método:

```csharp
Xamarin.FormsMaps.Init();
```

No Android, isso deve ocorrer em **MainActivity.cs** invocando o `Xamarin.FormsMaps.Init` método *após* o `Xamarin.Forms.Forms.Init` método:

```csharp
Xamarin.FormsMaps.Init(this, savedInstanceState);
```

Na Plataforma Universal do Windows (UWP), isso deve ocorrer em **MainPage.XAML.cs** invocando o `Xamarin.FormsMaps.Init` método do `MainPage` Construtor:

```csharp
Xamarin.FormsMaps.Init("INSERT_AUTHENTICATION_TOKEN_HERE");
```

Para obter informações sobre o token de autenticação necessário no UWP, consulte [plataforma universal do Windows](#universal-windows-platform).

Depois que o pacote NuGet tiver sido adicionado e o método de inicialização chamado dentro de cada aplicativo, as `Xamarin.Forms.Maps` APIs poderão ser usadas no projeto de código compartilhado.

## <a name="platform-configuration"></a>Configuração da plataforma

A configuração adicional é necessária no Android e no Plataforma Universal do Windows (UWP) antes que o mapa seja exibido. Além disso, no iOS, no Android e no UWP, o acesso ao local do usuário exige que as permissões de localização tenham sido concedidas ao aplicativo.

### <a name="ios"></a>iOS

Exibir e interagir com um mapa no iOS não requer nenhuma configuração adicional. No entanto, para acessar os serviços de localização, você deve definir as seguintes chaves em **info. plist**:

- iOS 11 e posterior
  - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) – para usar serviços de localização quando o aplicativo está em uso
  - [`NSLocationAlwaysAndWhenInUseUsageDescription`](https://developer.apple.com/documentation/bundleresources/information_property_list/nslocationalwaysandwheninuseusagedescription) – para usar serviços de localização em todos os momentos
- iOS 10 e anterior
  - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) – para usar serviços de localização quando o aplicativo está em uso
  - [`NSLocationAlwaysUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18) – para usar serviços de localização em todos os momentos    

Para dar suporte ao iOS 11 e anteriores, você pode incluir todas as três chaves: `NSLocationWhenInUseUsageDescription` , `NSLocationAlwaysAndWhenInUseUsageDescription` e `NSLocationAlwaysUsageDescription` .

A representação XML para essas chaves no **info. plist** é mostrada abaixo. Você deve atualizar os `string` valores para refletir como seu aplicativo está usando as informações de localização:

```xml
<key>NSLocationAlwaysUsageDescription</key>
<string>Can we use your location at all times?</string>
<key>NSLocationWhenInUseUsageDescription</key>
<string>Can we use your location when your application is being used?</string>
<key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
<string>Can we use your location at all times?</string>
```

As entradas **info. plist** também podem ser adicionadas na exibição de **código-fonte** durante a edição do arquivo **info. plist** :

![Info. plist para iOS 8](setup-images/ios8-map-permissions.png "Entradas do info. plist necessárias do iOS 8")

Um prompt é exibido quando o aplicativo tenta acessar o local do usuário, solicitando acesso:

[![Captura de tela da solicitação de permissão de localização no iOS](setup-images/permission-ios.png "solicitação de permissão do iOS")](setup-images/permission-ios-large.png#lightbox "solicitação de permissão do iOS")

### <a name="android"></a>Android

O processo de configuração para exibir e interagir com um mapa no Android é:

1. Obtenha uma chave de API do Google Maps e adicione-a ao manifesto.
1. Especifique o número de versão dos serviços de Google Play no manifesto.
1. Especifique o requisito para a biblioteca herdada do Apache HTTP no manifesto.
1. adicional Especifique a permissão de WRITE_EXTERNAL_STORAGE no manifesto.
1. adicional Especifique as permissões de local no manifesto.
1. adicional Solicitar permissões de local de tempo de execução na `MainActivity` classe.

Para obter um exemplo de um arquivo de manifesto configurado corretamente, consulte [AndroidManifest.xml](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithMaps/WorkingWithMaps/WorkingWithMaps.Android/Properties/AndroidManifest.xml) do aplicativo de exemplo.

#### <a name="get-a-google-maps-api-key"></a>Obter uma chave de API do Google Maps

Para usar a [API do Google Maps](https://developers.google.com/maps/documentation/android/) no Android, você deve gerar uma chave de API. Para fazer isso, siga as instruções em [obtendo uma chave de API do Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).

Depois de obter uma chave de API, ela deve ser adicionada dentro do `<application>` elemento do arquivo de **propriedades/AndroidManifest.xml** :

```xml
<application ...>
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="PASTE-YOUR-API-KEY-HERE" />
</application>
```

Isso incorpora a chave de API ao manifesto. Sem uma chave de API válida [`Map`](xref:Xamarin.Forms.Maps.Map) , o controle exibirá uma grade em branco.

> [!NOTE]
> `com.google.android.geo.API_KEY` é o nome de metadados recomendado para a chave de API. Para compatibilidade com versões anteriores, o `com.google.android.maps.v2.API_KEY` nome de metadados pode ser usado, mas permite apenas a autenticação para a API do Maps do Android v2.

Para que seu APK acesse o Google Maps, você deve incluir impressões digitais e nomes de pacote do SHA-1 para cada repositório de chaves (depuração e versão) que você usa para assinar seu APK. Por exemplo, se você usar um computador para depuração e outro computador para gerar a versão APK, deverá incluir a impressão digital do certificado SHA-1 do repositório de chaves de depuração do primeiro computador e a impressão digital do certificado SHA-1 do repositório de chaves de lançamento do segundo computador. Lembre-se também de editar as credenciais de chave se o **nome do pacote** do aplicativo for alterado. Consulte [obtendo uma chave de API do Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).

#### <a name="specify-the-google-play-services-version-number"></a>Especificar o número de versão dos serviços de Google Play

Adicione a seguinte declaração dentro do `<application>` elemento de **AndroidManifest.xml**:

```xml
<meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" />
```

Isso incorpora a versão do Google Play Services com a qual o aplicativo foi compilado, no manifesto.

#### <a name="specify-the-requirement-for-the-apache-http-legacy-library"></a>Especifique o requisito para a biblioteca herdada do Apache HTTP

Se seu Xamarin.Forms aplicativo for destinado à API 28 ou superior, você deverá adicionar a seguinte declaração dentro do `<application>` elemento de **AndroidManifest.xml**:

```xml
<uses-library android:name="org.apache.http.legacy" android:required="false" />    
```

Isso informa o aplicativo para usar a biblioteca de cliente http do Apache, que foi removida do `bootclasspath` no Android 9.

#### <a name="specify-the-write_external_storage-permission"></a>Especificar a permissão de WRITE_EXTERNAL_STORAGE

Se o aplicativo for direcionado à API 22 ou inferior, poderá ser necessário adicionar a `WRITE_EXTERNAL_STORAGE` permissão ao manifesto, como um filho do `<manifest>` elemento:

```xml
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

Isso não será necessário se o aplicativo for direcionado para a API 23 ou superior.

#### <a name="specify-location-permissions"></a>Especificar permissões de local

Se seu aplicativo precisar acessar o local do usuário, você deverá solicitar permissão adicionando as `ACCESS_COARSE_LOCATION` `ACCESS_FINE_LOCATION` permissões ou ao manifesto (ou ambos), como um filho do `<manifest>` elemento:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="com.companyname.myapp">
  ...
  <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
  <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
</manifest>
```

A `ACCESS_COARSE_LOCATION` permissão permite que a API use dados de WiFi ou móveis, ou ambos, para determinar o local do dispositivo. As `ACCESS_FINE_LOCATION` permissões permitem que a API use os dados de GPS (GPS), WiFi ou Mobile para determinar um local preciso o máximo possível.

Como alternativa, essas permissões podem ser habilitadas usando o editor de manifesto para adicionar as seguintes permissões:

- `AccessCoarseLocation`
- `AccessFineLocation`

Eles são mostrados na captura de tela abaixo:

![Permissões necessárias para Android](setup-images/android-map-permissions.png "Permissões necessárias para Android")

#### <a name="request-runtime-location-permissions"></a>Solicitar permissões de local do tempo de execução

Se o seu aplicativo for destinado à API 23 ou posterior e precisar acessar o local do usuário, ele deverá verificar se ele tem a permissão necessária em tempo de execução e solicitá-lo se ele não o tiver. Isso pode ser feito da seguinte maneira:

1. Na `MainActivity` classe, adicione os seguintes campos:

    ```csharp
    const int RequestLocationId = 0;

    readonly string[] LocationPermissions =
    {
        Manifest.Permission.AccessCoarseLocation,
        Manifest.Permission.AccessFineLocation
    };
    ```

1. Na `MainActivity` classe, adicione a seguinte `OnStart` substituição:

    ```csharp
    protected override void OnStart()
    {
        base.OnStart();

        if ((int)Build.VERSION.SdkInt >= 23)
        {
            if (CheckSelfPermission(Manifest.Permission.AccessFineLocation) != Permission.Granted)
            {
                RequestPermissions(LocationPermissions, RequestLocationId);
            }
            else
            {
                // Permissions already granted - display a message.
            }
        }
    }
    ```

    Desde que o aplicativo esteja direcionado para a API 23 ou superior, esse código executa uma verificação de permissão de tempo de execução para a `AccessFineLocation` permissão. Se a permissão não tiver sido concedida, uma solicitação de permissão será feita chamando o `RequestPermissions` método.

1. Na `MainActivity` classe, adicione a seguinte `OnRequestPermissionsResult` substituição:

    ```csharp
    public override void OnRequestPermissionsResult(int requestCode, string[] permissions, [GeneratedEnum] Permission[] grantResults)
    {
        if (requestCode == RequestLocationId)
        {
            if ((grantResults.Length == 1) && (grantResults[0] == (int)Permission.Granted))
                // Permissions granted - display a message.
            else
                // Permissions denied - display a message.
        }
        else
        {
            base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
        }
    }
    ```

    Essa substituição manipula o resultado da solicitação de permissão.

O efeito geral desse código é que, quando o aplicativo solicita o local do usuário, a caixa de diálogo a seguir é exibida, que solicita permissão:

[![Captura de tela de solicitação de permissão de localização no Android](setup-images/permission-android.png "Solicitação de permissão do Android")](setup-images/permission-android-large.png#lightbox "Solicitação de permissão do Android")

### <a name="universal-windows-platform"></a>Plataforma Universal do Windows

No UWP, seu aplicativo deve ser autenticado antes de poder exibir um mapa e consumir serviços de mapa. Para autenticar seu aplicativo, você deve especificar uma chave de autenticação do Maps. Para obter mais informações, consulte [solicitar uma chave de autenticação do Maps](/windows/uwp/maps-and-location/authentication-key). O token de autenticação deve ser especificado na `FormsMaps.Init("AUTHORIZATION_TOKEN")` chamada do método, para autenticar o aplicativo com o Bing Maps.

> [!NOTE]
> No UWP, para usar serviços de mapa como geocodificação, você também deve definir a `MapService.ServiceToken` propriedade como o valor da chave de autenticação. Isso pode ser feito com a seguinte linha de código: `Windows.Services.Maps.MapService.ServiceToken = "INSERT_AUTH_TOKEN_HERE";` .

Além disso, se seu aplicativo precisar acessar o local do usuário, você deverá habilitar a capacidade de localização no manifesto do pacote. Isso pode ser feito da seguinte maneira:

1. No **Gerenciador de Soluções**, clique duas vezes sobre **package.appxmanifest** e selecione a guia **Funcionalidades**.
1. Na lista **Recursos**, marque a caixa de **Local**. Isso adiciona a `location` funcionalidade do dispositivo ao arquivo de manifesto do pacote.

    ```xml
    <Capabilities>
      <!-- DeviceCapability elements must follow Capability elements (if present) -->
      <DeviceCapability Name="location"/>
    </Capabilities>
    ```

#### <a name="release-builds"></a>Builds de versão

As compilações da versão UWP usam compilação nativa do .NET para compilar o aplicativo diretamente no código nativo. No entanto, uma consequência disso é que o renderizador do [`Map`](xref:Xamarin.Forms.Maps.Map) controle em UWP pode ser vinculado fora do executável. Isso pode ser corrigido usando uma sobrecarga específica de UWP do `Forms.Init` método no **app.XAML.cs**:

```csharp
var assembliesToInclude = new [] { typeof(Xamarin.Forms.Maps.UWP.MapRenderer).GetTypeInfo().Assembly };
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
```

Esse código passa o assembly no qual a `Xamarin.Forms.Maps.UWP.MapRenderer` classe reside, para o `Forms.Init` método. Isso garante que o assembly não esteja vinculado ao executável pelo processo de compilação nativa do .NET.

> [!IMPORTANT]
> A falha em fazer isso fará com que o [`Map`](xref:Xamarin.Forms.Maps.Map) controle não apareça ao executar uma compilação de versão.

## <a name="related-links"></a>Links relacionados

- [Exemplo de mapas](/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Xamarin.Forms. Mapeia Pins](~/xamarin-forms/user-interface/map/pins.md).
- [API de mapas](xref:Xamarin.Forms.Maps)
- [Mapear renderizador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md)