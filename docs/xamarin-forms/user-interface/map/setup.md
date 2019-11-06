---
title: Inicialização e configuração do mapa do Xamarin. Forms
description: O pacote NuGet Xamarin. Forms. Maps é necessário para usar a funcionalidade de mapas em um aplicativo. Além disso, o acesso ao local do usuário exige que as permissões de localização tenham sido concedidas ao aplicativo.
ms.prod: xamarin
ms.assetid: 59CD1344-8248-406C-9144-0C8A67141E5B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/06/2019
ms.openlocfilehash: 038ff27907573c1fe15516f6f4caf26d0892ab9f
ms.sourcegitcommit: 283810340de5310f63ef7c3e4b266fe9dc2ffcaf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73662336"
---
# <a name="xamarinforms-map-initialization-and-configuration"></a>Inicialização e configuração do mapa do Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

O controle de [`Map`](xref:Xamarin.Forms.Maps.Map) usa o controle de mapa nativo em cada plataforma. Isso fornece uma experiência de mapas rápida e familiar para os usuários, mas significa que algumas etapas de configuração são necessárias para aderir a cada uma das plataformas requisitos de API.

## <a name="map-initialization"></a>Inicialização do mapa

O controle [`Map`](xref:Xamarin.Forms.Maps.Map) é fornecido pelo pacote NuGet [Xamarin. Forms. Maps](https://www.nuget.org/packages/Xamarin.Forms.Maps/) , que deve ser adicionado a todos os projetos na solução.

Depois de instalar o pacote NuGet do [Xamarin. Forms. Maps](https://www.nuget.org/packages/Xamarin.Forms.Maps/) , ele deve ser inicializado em cada projeto de plataforma.

No iOS, isso deve ocorrer em **AppDelegate.cs** invocando o método `Xamarin.FormsMaps.Init` *após* o método `Xamarin.Forms.Forms.Init`:

```csharp
Xamarin.FormsMaps.Init();
```

No Android, isso deve ocorrer em **MainActivity.cs** invocando o método `Xamarin.FormsMaps.Init` *após* o método `Xamarin.Forms.Forms.Init`:

```csharp
Xamarin.FormsMaps.Init(this, savedInstanceState);
```

Na Plataforma Universal do Windows (UWP), isso deve ocorrer em **MainPage.XAML.cs** invocando o método `Xamarin.FormsMaps.Init` do Construtor `MainPage`:

```csharp
Xamarin.FormsMaps.Init("INSERT_AUTHENTICATION_TOKEN_HERE");
```

Para obter informações sobre o token de autenticação necessário no UWP, consulte [plataforma universal do Windows](#universal-windows-platform).

Depois que o pacote NuGet tiver sido adicionado e o método de inicialização chamado dentro de cada aplicativo, `Xamarin.Forms.Maps` APIs poderão ser usadas no projeto de código compartilhado.

## <a name="platform-configuration"></a>Configuração da plataforma

A configuração adicional é necessária no Android e no Plataforma Universal do Windows (UWP) antes que o mapa seja exibido. Além disso, no iOS, no Android e no UWP, o acesso ao local do usuário exige que as permissões de localização tenham sido concedidas ao aplicativo.

### <a name="ios"></a>iOS

Exibir e interagir com um mapa no iOS não requer nenhuma configuração adicional. No entanto, para acessar os serviços de localização, você deve definir as seguintes chaves em **info. plist**:

- iOS 11 e posterior
  - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) – para usar serviços de localização quando o aplicativo está em uso
  - [`NSLocationAlwaysAndWhenInUseUsageDescription`](https://developer.apple.com/documentation/corelocation/choosing_the_authorization_level_for_location_services/requesting_always_authorization?language=objc) – para usar serviços de localização em todos os momentos
- iOS 10 e anterior
  - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) – para usar serviços de localização quando o aplicativo está em uso
  - [`NSLocationAlwaysUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18) – para usar serviços de localização em todos os momentos    

Para dar suporte ao iOS 11 e anteriores, você pode incluir todas as três chaves: `NSLocationWhenInUseUsageDescription`, `NSLocationAlwaysAndWhenInUseUsageDescription` e `NSLocationAlwaysUsageDescription`.

A representação XML para essas chaves no **info. plist** é mostrada abaixo. Você deve atualizar os valores de `string` para refletir como seu aplicativo está usando as informações de local:

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
1. adicional Especifique a permissão WRITE_EXTERNAL_STORAGE no manifesto.
1. adicional Especifique as permissões de local no manifesto.
1. adicional Solicitar permissões de local de tempo de execução na classe `MainActivity`.

Para obter um exemplo de um arquivo de manifesto configurado corretamente, consulte [AndroidManifest. xml](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithMaps/WorkingWithMaps/WorkingWithMaps.Android/Properties/AndroidManifest.xml) do aplicativo de exemplo.

#### <a name="get-a-google-maps-api-key"></a>Obter uma chave de API do Google Maps

Para usar a [API do Google Maps](https://developers.google.com/maps/documentation/android/) no Android, você deve gerar uma chave de API. Para fazer isso, siga as instruções em [obtendo uma chave de API do Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).

Depois de obter uma chave de API, ela deve ser adicionada dentro do elemento `<application>` do arquivo **Properties/AndroidManifest. xml** :

```xml
<application ...>
    <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="PASTE-YOUR-API-KEY-HERE" />
</application>
```

Isso incorpora a chave de API ao manifesto. Sem uma chave de API válida, o controle de [`Map`](xref:Xamarin.Forms.Maps.Map) exibirá uma grade em branco.

> [!NOTE]
> Observe que, para que seu APK acesse o Google Maps, você deve incluir impressões digitais e nomes de pacote do SHA-1 para cada repositório de chaves (depuração e versão) que você usa para assinar seu APK. Por exemplo, se você usar um computador para depuração e outro computador para gerar a versão APK, deverá incluir a impressão digital do certificado SHA-1 do repositório de chaves de depuração do primeiro computador e a impressão digital do certificado SHA-1 do repositório de chaves de lançamento do o segundo computador. Lembre-se também de editar as credenciais de chave se o **nome do pacote** do aplicativo for alterado. Consulte [obtendo uma chave de API do Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).

#### <a name="specify-the-google-play-services-version-number"></a>Especificar o número de versão dos serviços de Google Play

Adicione a seguinte declaração dentro do elemento `<application>` de **AndroidManifest. xml**:

```xml
<meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" />
```

Isso incorpora a versão do Google Play Services com a qual o aplicativo foi compilado, no manifesto.

#### <a name="specify-the-requirement-for-the-apache-http-legacy-library"></a>Especifique o requisito para a biblioteca herdada do Apache HTTP

Se o aplicativo Xamarin. Forms for direcionado à API 28 ou superior, você deverá adicionar a seguinte declaração dentro do elemento `<application>` de **AndroidManifest. xml**:

```xml
<uses-library android:name="org.apache.http.legacy" android:required="false" />    
```

Isso informa o aplicativo para usar a biblioteca de cliente http do Apache, que foi removida do `bootclasspath` no Android 9.

#### <a name="specify-the-write_external_storage-permission"></a>Especificar a permissão WRITE_EXTERNAL_STORAGE

Se o aplicativo for direcionado à API 22 ou inferior, poderá ser necessário adicionar a permissão `WRITE_EXTERNAL_STORAGE` ao manifesto, como um filho do elemento `<manifest>`:

```xml
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

Isso não será necessário se o aplicativo for direcionado para a API 23 ou superior.

#### <a name="specify-location-permissions"></a>Especificar permissões de local

Se seu aplicativo precisar acessar o local do usuário, você deverá solicitar permissão adicionando as permissões `ACCESS_COARSE_LOCATION` ou `ACCESS_FINE_LOCATION` ao manifesto (ou ambos), como um filho do elemento `<manifest>`:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="com.companyname.myapp">
  ...
  <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
  <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
</manifest>
```

A permissão `ACCESS_COARSE_LOCATION` permite que a API use dados de WiFi ou móveis, ou ambos, para determinar o local do dispositivo. As permissões de `ACCESS_FINE_LOCATION` permitem que a API use os dados de GPS (GPS), WiFi ou Mobile para determinar um local preciso o mais exato possível.

Como alternativa, essas permissões podem ser habilitadas usando o editor de manifesto para adicionar as seguintes permissões:

- `AccessCoarseLocation`
- `AccessFineLocation`

Eles são mostrados na captura de tela abaixo:

![Permissões necessárias para Android](setup-images/android-map-permissions.png "Permissões necessárias para Android")

#### <a name="request-runtime-location-permissions"></a>Solicitar permissões de local do tempo de execução

Se o seu aplicativo for destinado à API 23 ou posterior e precisar acessar o local do usuário, ele deverá verificar se ele tem a permissão necessária em tempo de execução e solicitá-lo se ele não o tiver. Isso pode ser feito da seguinte maneira:

1. Na classe `MainActivity`, adicione os seguintes campos:

    ```csharp
    const int RequestLocationId = 0;

    readonly string[] LocationPermissions =
    {
        Manifest.Permission.AccessCoarseLocation,
        Manifest.Permission.AccessFineLocation
    };
    ```

1. Na classe `MainActivity`, adicione a seguinte substituição de `OnStart`:

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

    Desde que o aplicativo esteja direcionado para a API 23 ou superior, esse código executa uma verificação de permissão de tempo de execução para a permissão de `AccessFineLocation`. Se a permissão não tiver sido concedida, uma solicitação de permissão será feita chamando o método `RequestPermissions`.

1. Na classe `MainActivity`, adicione a seguinte substituição de `OnRequestPermissionsResult`:

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

No UWP, seu aplicativo deve ser autenticado antes de poder exibir um mapa e consumir serviços de mapa. Para autenticar seu aplicativo, você deve especificar uma chave de autenticação do Maps. Para obter mais informações, consulte [solicitar uma chave de autenticação do Maps](/windows/uwp/maps-and-location/authentication-key). O token de autenticação deve ser especificado na chamada do método `FormsMaps.Init("AUTHORIZATION_TOKEN")`, para autenticar o aplicativo com o Bing Maps.

Além disso, se seu aplicativo precisar acessar o local do usuário, você deverá habilitar a capacidade de localização no manifesto do pacote. Isso pode ser feito da seguinte maneira:

1. Em **Gerenciador de soluções**, clique duas vezes em **Package. appxmanifest** e selecione a guia **recursos** .
1. Na lista de **recursos** , marque a caixa para **local**. Isso adiciona a funcionalidade de dispositivo `location` ao arquivo de manifesto do pacote.

    ```xml
    <Capabilities>
      <!-- DeviceCapability elements must follow Capability elements (if present) -->
      <DeviceCapability Name="location"/>
    </Capabilities>
    ```

#### <a name="release-builds"></a>Builds de versão

As compilações da versão UWP usam compilação nativa do .NET para compilar o aplicativo diretamente no código nativo. No entanto, uma consequência disso é que o renderizador do controle de [`Map`](xref:Xamarin.Forms.Maps.Map) no UWP pode ser vinculado fora do executável. Isso pode ser corrigido usando uma sobrecarga específica de UWP do método `Forms.Init` em **app.XAML.cs**:

```csharp
var assembliesToInclude = new [] { typeof(Xamarin.Forms.Maps.UWP.MapRenderer).GetTypeInfo().Assembly };
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
```

Esse código passa o assembly no qual reside a classe `Xamarin.Forms.Maps.UWP.MapRenderer`, para o método `Forms.Init`. Isso garante que o assembly não esteja vinculado ao executável pelo processo de compilação nativa do .NET.

> [!IMPORTANT]
> Se você não fizer isso, o controle de [`Map`](xref:Xamarin.Forms.Maps.Map) não aparecerá ao executar uma compilação de versão.

## <a name="related-links"></a>Links relacionados

- [Exemplo de mapas](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Pins do Xamarin. Forms. Maps](~/xamarin-forms/user-interface/map/pins.md).
- [API de mapas](xref:Xamarin.Forms.Maps)
- [Mapear renderizador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
