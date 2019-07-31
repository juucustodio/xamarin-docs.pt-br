---
title: Serviços de localização no Android
description: Este guia apresenta o reconhecimento de local em aplicativos Android e ilustra como obter o local do usuário usando a API do serviço de localização do Android, bem como o provedor de localização com fusível disponível com o Google API dos Serviços de Localização.
ms.prod: xamarin
ms.assetid: 0008682B-6CEF-0C1D-3200-56ECF58F5D3C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/22/2018
ms.openlocfilehash: b44bb52dc69aae1d3d058a1eae7c3be13ec5dc53
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68643346"
---
# <a name="location-services-on-android"></a>Serviços de localização no Android

_Este guia apresenta o reconhecimento de local em aplicativos Android e ilustra como obter o local do usuário usando a API do serviço de localização do Android, bem como o provedor de localização com fusível disponível com o Google API dos Serviços de Localização._

O Android fornece acesso a várias tecnologias de localização, como o local da torre de célula, Wi-Fi e GPS. Os detalhes de cada tecnologia de localização são abstrados por meio de *provedores de localização*, permitindo que os aplicativos obtenham locais da mesma forma, independentemente do provedor usado. Este guia apresenta o provedor de localização com fusível, uma parte do Google Play Services, que determina de forma inteligente a melhor maneira de obter o local dos dispositivos com base em quais provedores estão disponíveis e como o dispositivo está sendo usado. API do serviço de localização do Android e mostra como se comunicar com o serviço de `LocationManager`localização do sistema usando um. A segunda parte do guia explora o API dos Serviços de Localização do Android usando o `LocationManager`.

Como regra geral, os aplicativos devem preferir usar o provedor de localização com fusível, voltando a antiga API de serviço de localização do Android somente quando necessário.

## <a name="location-fundamentals"></a>Conceitos básicos de local

No Android, independentemente da API que você escolher para trabalhar com dados de localização, vários conceitos permanecem os mesmos. Esta seção apresenta provedores de localização e permissões relacionadas ao local.

### <a name="location-providers"></a>Provedores de localização

Várias tecnologias são usadas internamente para identificar o local do usuário. O hardware usado depende do tipo de *provedor de localização* selecionado para o trabalho de coleta de dados. O Android usa três provedores de local:

-   **Provedor de GPS** &ndash; O GPS fornece o local mais preciso, usa a mais potência e funciona melhor em todos os locais. Esse provedor usa uma combinação de GPS e[AGPS](https://en.wikipedia.org/wiki/Assisted_GPS)(GPS assistido), que retorna dados de GPS coletados pelo celular Towers.

-   **Provedor de rede** &ndash; Fornece uma combinação de dados Wi-Fi e celular, incluindo dados AGPS coletados pela célula Towers. Ele usa menos energia do que o provedor GPS, mas retorna dados de localização de precisão variável.

-   **Provedor passivo** &ndash; Uma opção "acumular" usando provedores solicitados por outros aplicativos ou serviços para gerar dados de localização em um aplicativo. Essa é uma opção menos confiável, mas de economia de energia ideal para aplicativos que não exigem atualizações de local constantes para funcionar.

Os provedores de localização nem sempre estão disponíveis. Por exemplo, talvez queiramos usar o GPS para nosso aplicativo, mas o GPS pode ser desativado nas configurações ou o dispositivo pode não ter nenhum GPS. Se um provedor específico não estiver disponível, escolher esse provedor poderá retornar `null`.

### <a name="location-permissions"></a>Permissões de local

Um aplicativo com reconhecimento de local precisa acessar os sensores de hardware de um dispositivo para receber dados de GPS, Wi-Fi e celular. O acesso é controlado por meio de permissões apropriadas no manifesto do Android do aplicativo.
Há duas permissões disponíveis &ndash; , dependendo dos requisitos do seu aplicativo e de sua escolha de API, você desejará permitir uma:

-   `ACCESS_FINE_LOCATION`&ndash; Permite que um aplicativo acesse o GPS.
    Necessário para o *provedor GPS* e as opções do *provedor passivo* (o*provedor passivo precisa de permissão para acessar os dados do GPS coletados por outro aplicativo ou serviço*). Permissão opcional para o *provedor de rede*.

-   `ACCESS_COARSE_LOCATION`&ndash; Permite que um aplicativo acesse a rede celular e o local Wi-Fi. Necessário para o provedor de `ACCESS_FINE_LOCATION` *rede* se não estiver definido.

Para aplicativos que são de API de destino versão 21 (Android 5,0 pirulito) ou superior, `ACCESS_FINE_LOCATION` você pode habilitar e ainda executar em dispositivos que não têm hardware de GPS. Se seu aplicativo requer hardware GPS, você deve adicionar explicitamente um `android.hardware.location.gps` `uses-feature` elemento ao manifesto do Android. Para obter mais informações, consulte a referência do elemento [use-Feature](https://developer.android.com/guide/topics/manifest/uses-feature-element.html) do Android.

Para definir as permissões, expanda a pasta **Propriedades** no **painel de soluções** e clique duas vezes em **AndroidManifest. xml**. As permissões serão listadas em **permissões necessárias**:

[![Captura de tela das configurações de permissões necessárias do manifesto do Android](location-images/location-01-xs.png)](location-images/location-01-xs.png#lightbox)

A configuração de qualquer uma dessas permissões informa ao Android que seu aplicativo precisa de permissão do usuário para acessar os provedores de localização. Os dispositivos que executam o nível de API 22 (Android 5,1) ou inferior solicitarão que o usuário conceda essas permissões sempre que o aplicativo for instalado. Em dispositivos que executam o nível de API 23 (Android 6,0) ou superior, o aplicativo deve executar uma verificação de permissão em tempo de execução antes de fazer uma solicitação do provedor de localização. 

> [!NOTE]
>Observação: A `ACCESS_FINE_LOCATION` configuração implica o acesso a dados de localização muito grande e fino. Você nunca deve definir ambas as permissões, apenas a permissão *mínima* que seu aplicativo precisa para funcionar.

Este trecho de código é um exemplo de como verificar se um aplicativo tem permissão para `ACCESS_FINE_LOCATION` a permissão:

```csharp
 if (ContextCompat.CheckSelfPermission(this, Manifest.Permission.AccessFineLocation) == Permission.Granted)
{
    StartRequestingLocationUpdates();
    isRequestingLocationUpdates = true;
}
else
{
    // The app does not have permission ACCESS_FINE_LOCATION 
}
```

Os aplicativos devem ser tolerantes ao cenário em que o usuário não concederá permissão (ou revogou a permissão) e terá uma maneira de lidar com essa situação normalmente. Consulte o [Guia de permissões](~/android/app-fundamentals/permissions.md) para obter mais detalhes sobre como implementar verificações de permissão em tempo de execução no Xamarin. Android.


## <a name="using-the-fused-location-provider"></a>Usando o provedor de localização com fusível

O provedor de localização com fusível é a maneira preferida para aplicativos Android receberem atualizações de local do dispositivo, pois ele selecionará com eficiência o provedor de localização durante o tempo de execução para fornecer as melhores informações de localização de uma maneira eficiente de bateria. Por exemplo, um usuário percorrendo todo o mundo Obtém a melhor leitura de local com GPS. Se o usuário, em seguida, orientar as inportas, onde o GPS funciona mal (se houver), o provedor de localização com fusível pode alternar automaticamente para WiFi, o que funciona melhor.
 
A API do provedor de localização com fusível fornece uma variedade de outras ferramentas para capacitar aplicativos com reconhecimento de local, incluindo monitoramento de atividades e isolamentos geográficas. Nesta seção, vamos nos concentrar nos conceitos básicos da configuração do, no estabelecimento de `LocationClient`provedores e na obtenção do local do usuário.

O provedor de localização com fusível faz parte do [Google Play Services](https://developer.android.com/google/play-services/index.html).
O pacote de Google Play Services deve ser instalado e configurado corretamente no aplicativo para que a API do provedor de localização com fusível funcione e o dispositivo deve ter o Google Play Services APK instalado.

Antes que um aplicativo Xamarin. Android possa usar o provedor de localização com fusível, ele deve adicionar o pacote **Xamarin. GooglePlayServices. Maps** ao projeto. Além disso, as instruções `using` a seguir devem ser adicionadas a qualquer arquivo de origem que referencie as classes descritas abaixo:

```csharp
using Android.Gms.Common;
using Android.Gms.Location;
```

### <a name="checking-if-google-play-services-is-installed"></a>Verificando se Google Play Services está instalado

Um Xamarin. Android falhará se tentar usar o provedor de localização com fusível quando Google Play Services não estiver instalado (ou desatualizado), ocorrerá uma exceção de tempo de execução.  Se Google Play Services não estiver instalado, o aplicativo deverá retornar ao serviço de localização do Android discutido acima. Se Google Play Services estiver desatualizado, o aplicativo poderá exibir uma mensagem para o usuário solicitando que ele atualize a versão instalada do Google Play Services.

Este trecho de código é um exemplo de como uma atividade do Android pode verificar programaticamente se Google Play Services está instalada:

```csharp
bool IsGooglePlayServicesInstalled()
{
    var queryResult = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(this);
    if (queryResult == ConnectionResult.Success)
    {
        Log.Info("MainActivity", "Google Play Services is installed on this device.");
        return true;
    }

    if (GoogleApiAvailability.Instance.IsUserResolvableError(queryResult))
    {
        // Check if there is a way the user can resolve the issue
        var errorString = GoogleApiAvailability.Instance.GetErrorString(queryResult);
        Log.Error("MainActivity", "There is a problem with Google Play Services on this device: {0} - {1}",
                  queryResult, errorString);

        // Alternately, display the error to the user.
    }

    return false;
}
```

### <a name="fusedlocationproviderclient"></a>FusedLocationProviderClient

Para interagir com o provedor de localização com fusível, um aplicativo Xamarin. Android deve ter uma instância `FusedLocationProviderClient`do. Essa classe expõe os métodos necessários para assinar atualizações de local e recuperar o último local conhecido do dispositivo.

O `OnCreate` método de uma atividade é um local adequado para obter uma referência `FusedLocationProviderClient`ao, conforme demonstrado no seguinte trecho de código:

```csharp
public class MainActivity: AppCompatActivity
{
    FusedLocationProviderClient fusedLocationProviderClient;

    protected override void OnCreate(Bundle bundle) 
    {
        fusedLocationProviderClient = LocationServices.GetFusedLocationProviderClient(this);
    }
}
```

### <a name="getting-the-last-known-location"></a>Obtendo o último local conhecido

O `FusedLocationProviderClient.GetLastLocationAsync()` método fornece uma maneira simples e sem bloqueio para um aplicativo Xamarin. Android para obter rapidamente o último local conhecido do dispositivo com sobrecarga de codificação mínima.

Este trecho de código mostra como usar `GetLastLocationAsync` o método para recuperar o local do dispositivo:

```csharp
async Task GetLastLocationFromDevice()
{
    // This method assumes that the necessary run-time permission checks have succeeded.
    getLastLocationButton.SetText(Resource.String.getting_last_location);
    Android.Locations.Location location = await fusedLocationProviderClient.GetLastLocationAsync();

    if (location == null)
    {
        // Seldom happens, but should code that handles this scenario
    }
    else
    {
        // Do something with the location 
        Log.Debug("Sample", "The latitude is " + location.Latitude);
    }
}
```

### <a name="subscribing-to-location-updates"></a>Inscrevendo-se nas atualizações de local

Um aplicativo Xamarin. Android também pode assinar atualizações de local do provedor de localização com fusível usando `FusedLocationProviderClient.RequestLocationUpdatesAsync` o método, conforme mostrado neste trecho de código:

```csharp
await fusedLocationProviderClient.RequestLocationUpdatesAsync(locationRequest, locationCallback);
```

Esse método usa dois parâmetros:

-   **`Android.Gms.Location.LocationRequest`** &ndash; Um`LocationRequest` objeto é como um aplicativo Xamarin. Android passa os parâmetros de como o provedor de localização com fusível deve funcionar. O `LocationRequest` contém informações como as solicitações frequentes que devem ser feitas ou a importância de uma atualização de local precisa. Por exemplo, uma solicitação de localização importante fará com que o dispositivo use o GPS e, consequentemente, mais energia, ao determinar o local. Este trecho de código mostra como criar um `LocationRequest` para um local com alta precisão, verificando aproximadamente a cada cinco minutos para uma atualização de local (mas não antes de dois minutos entre solicitações). O provedor de localização com fusível usará um `LocationRequest` as diretrizes para qual provedor de localização usar ao tentar determinar o local do dispositivo:

    ```csharp
    LocationRequest locationRequest = new LocationRequest()
                                      .SetPriority(LocationRequest.PriorityHighAccuracy)
                                      .SetInterval(60 * 1000 * 5)
                                      .SetFastestInterval(60 * 1000 * 2);
    ```

-   **`Android.Gms.Location.LocationCallback`** Para receber atualizações de local, um aplicativo Xamarin. Android deve subclassificar a `LocationProvider` classe abstrata. &ndash; Essa classe expôs dois métodos que talvez sejam invocados pelo provedor de localização com fusível para atualizar o aplicativo com informações de localização. Isso será discutido em mais detalhes abaixo.

Para notificar um aplicativo Xamarin. Android de uma atualização de local, o provedor de localização com `LocationCallBack.OnLocationResult(LocationResult result)`fusível invocará o. O `Android.Gms.Location.LocationResult` parâmetro conterá as informações de local de atualização.

Quando o provedor de localização com fusível detecta uma alteração na disponibilidade dos dados do local, ele chamará o `LocationProvider.OnLocationAvailability(LocationAvailability
locationAvailability)` método. Se a `LocationAvailability.IsLocationAvailable` Propriedade retornar `true`, pode ser pressuposto que os resultados da localização do dispositivo relatados pelo `OnLocationResult` são tão precisos quanto atualizados, conforme exigido pelo `LocationRequest`. Se `IsLocationAvailable` for false, nenhum resultado de localização será retornado por `OnLocationResult`.

Este trecho de código é uma implementação de exemplo `LocationCallback` do objeto:

```csharp
public class FusedLocationProviderCallback : LocationCallback
{
    readonly MainActivity activity;

    public FusedLocationProviderCallback(MainActivity activity)
    {
        this.activity = activity;
    }

    public override void OnLocationAvailability(LocationAvailability locationAvailability)
    {
        Log.Debug("FusedLocationProviderSample", "IsLocationAvailable: {0}",locationAvailability.IsLocationAvailable);
    }

    public override void OnLocationResult(LocationResult result)
    {
        if (result.Locations.Any())
        {
            var location = result.Locations.First();
            Log.Debug("Sample", "The latitude is :" + location.Latitude);
        }
        else
        {
            // No locations to work with.
        }
    }
}
```

## <a name="using-the-android-location-service-api"></a>Usando a API do serviço de localização do Android

O serviço de localização do Android é uma API mais antiga para usar informações de localização no Android. Os dados de localização são coletados por sensores de hardware e coletados por um serviço do sistema, que é `LocationManager` acessado `ILocationListener`no aplicativo com uma classe e um.

O serviço de localização é mais adequado para aplicativos que devem ser executados em dispositivos que não têm Google Play Services instalado.

O serviço de localização é um tipo especial de [serviço](https://developer.android.com/guide/components/services.html) gerenciado pelo sistema. Um serviço do sistema interage com o hardware do dispositivo e está sempre em execução. Para tocar em atualizações de local em nosso aplicativo, assinaremos as atualizações de local do serviço de localização do sistema `LocationManager` usando um `RequestLocationUpdates` e uma chamada.

Para obter o local do usuário usando o serviço de localização do Android envolve várias etapas:

1.  Obtenha uma referência para o `LocationManager` serviço.
2.  Implemente `ILocationListener` a interface e manipule eventos quando o local for alterado.
3.  Use o `LocationManager` para solicitar atualizações de local para um provedor especificado. O `ILocationListener` da etapa anterior será usado para receber retornos `LocationManager`de chamada do.
4.  Pare as atualizações de local quando o aplicativo não for mais apropriado para receber atualizações.

### <a name="location-manager"></a>Gerenciador de localização

Podemos acessar o serviço de localização do sistema com uma instância da `LocationManager` classe. `LocationManager`é uma classe especial que nos permite interagir com os métodos de serviço de localização do sistema e chamar. Um aplicativo pode obter uma referência para o `LocationManager` chamando `GetSystemService` e passando um tipo de serviço, conforme mostrado abaixo:

```csharp
LocationManager locationManager = (LocationManager) GetSystemService(Context.LocationService);
```

`OnCreate`é um bom local para obter uma referência para o `LocationManager`.
É uma boa ideia manter a `LocationManager` como uma variável de classe, para que possamos chamá-la em vários pontos no ciclo de vida da atividade.

### <a name="request-location-updates-from-the-locationmanager"></a>Solicitar atualizações de local do Localmanager

Depois que o aplicativo tem uma referência ao `LocationManager`, ele precisa informar ao `LocationManager` tipo de informações de local que são necessárias e com que frequência essas informações devem ser atualizadas. Faça isso chamando `RequestLocationUpdates` `LocationManager` no objeto e passando alguns critérios para atualizações e um retorno de chamada que receberá as atualizações de local. Esse retorno de chamada é um tipo que deve `ILocationListener` implementar a interface (descrita em mais detalhes posteriormente neste guia).

O `RequestLocationUpdates` método informa ao serviço de localização do sistema que seu aplicativo gostaria de iniciar o recebimento de atualizações de local. Esse método permite que você especifique o provedor, bem como limites de tempo e distância para controlar a frequência de atualização. Por exemplo, o método abaixo solicita atualizações de local do provedor de localização GPS a cada 2000 milissegundos e somente quando o local muda mais de 1 metre:

```csharp
// For this example, this method is part of a class that implements ILocationListener, described below
locationManager.RequestLocationUpdates(LocationManager.GpsProvider, 2000, 1, this);
```

Um aplicativo deve solicitar atualizações de local somente com a frequência necessária para que o aplicativo tenha um bom desempenho. Isso preserva a vida útil da bateria e cria uma experiência melhor para o usuário.

### <a name="responding-to-updates-from-the-locationmanager"></a>Respondendo a atualizações do Localmanager

Depois que um aplicativo solicita atualizações do `LocationManager`, ele pode receber informações do serviço implementando a [`ILocationListener`](xref:Android.Locations.ILocationListener) interface. Essa interface fornece quatro métodos para escutar o serviço de localização e o provedor de localização `OnLocationChanged`,. O sistema chamará `OnLocationChanged` quando o local do usuário for alterado o suficiente para ser qualificado como uma alteração de local de acordo com o conjunto de critérios ao solicitar atualizações de local. 

O código a seguir mostra os métodos na `ILocationListener` interface:

```csharp
public class MainActivity : AppCompatActivity, ILocationListener
{
    TextView latitude;
    TextView longitude;
    
    public void OnLocationChanged (Location location)
    {
        // called when the location has been updated.
    }
    
    public OnProviderDisabled(string locationProvider)
    {
        // called when the user disables the provider
    }
    
    public OnProviderEnabled(string locationProvider)
    {
        // called when the user enables the provider
    }
    
    public OnStatusChanged(string locationProvider, Availability status, Bundle extras)
    {
        // called when the status of the provider changes (there are a variety of reasons for this)
    }
}
```

### <a name="unsubscribing-to-locationmanager-updates"></a>Cancelando a desalocação para atualizações de local

Para conservar os recursos do sistema, um aplicativo deve cancelar a assinatura de atualizações de local assim que possível. O `RemoveUpdates` método informa ao `LocationManager` para interromper o envio de atualizações para nosso aplicativo.  Por exemplo, uma atividade pode chamar `RemoveUpdates` `OnPause` no método para que possamos conservar energia se um aplicativo não precisar de atualizações de local enquanto sua atividade não estiver na tela:

```csharp
protected override void OnPause ()
{
    base.OnPause ();
    locationManager.RemoveUpdates (this);
}
```

Se seu aplicativo precisar obter atualizações de local enquanto estiver em segundo plano, você desejará criar um serviço personalizado que assina o serviço de localização do sistema. Consulte o guia de [plano de fundo com os serviços Android](~/android/app-fundamentals/services/index.md) para obter mais informações.

### <a name="determining-the-best-location-provider-for-the-locationmanager"></a>Determinando o melhor provedor de localização para o Localmanager

O aplicativo acima define o GPS como o provedor de localização. No entanto, o GPS pode não estar disponível em todos os casos, como se o dispositivo é inportado ou não tem um receptor GPS. Se esse for o caso, o resultado será um `null` retorno para o provedor.

Para que seu aplicativo funcione quando o GPS não estiver disponível, use o `GetBestProvider` método para solicitar o provedor de local de melhor disponibilidade (com suporte do dispositivo e habilitado pelo usuário) na inicialização do aplicativo. Em vez de passar em um provedor específico, você pode `GetBestProvider` informar os requisitos para o provedor, como precisão e energia com um [ `Criteria` objeto](xref:Android.Locations.Criteria). `GetBestProvider`Retorna o melhor provedor para os critérios especificados.

O código a seguir mostra como obter o melhor provedor disponível e usá-lo ao solicitar atualizações de local:

```csharp
Criteria locationCriteria = new Criteria();   
locationCriteria.Accuracy = Accuracy.Coarse;
locationCriteria.PowerRequirement = Power.Medium;

locationProvider = locationManager.GetBestProvider(locationCriteria, true);

if(locationProvider != null)
{
    locationManager.RequestLocationUpdates (locationProvider, 2000, 1, this);
}
else
{
    Log.Info(tag, "No location providers available");
}
```

> [!NOTE]
>  Se o usuário tiver desabilitado todos os provedores `GetBestProvider` de local `null`, o retornará. Para ver como esse código funciona em um dispositivo real, certifique-se de habilitar GPS, Wi-Fi e redes de celular em **configurações do Google > modo de > de local** , conforme mostrado nesta captura de tela:

[![Tela do modo de localização de configurações em um telefone Android](location-images/location-02.png)](location-images/location-02.png#lightbox)

A captura de tela abaixo demonstra o aplicativo de `GetBestProvider`localização em execução usando:

[![Aplicativo getbestprovider exibindo latitude, longitude e provedor](location-images/location-03.png)](location-images/location-03.png#lightbox)

Tenha em mente que `GetBestProvider` não altera o provedor dinamicamente. Em vez disso, ele determina o melhor provedor disponível uma vez durante o ciclo de vida da atividade. Se o status do provedor for alterado depois de definido, `ILocationListener` o aplicativo exigirá código adicional nos métodos &ndash; &ndash; `OnProviderEnabled`, `OnProviderDisabled`e `OnStatusChanged` para manipular cada possibilidade relacionada ao comutador do provedor.

## <a name="summary"></a>Resumo

Este guia abordou a obtenção do local do usuário usando o serviço de localização do Android e o provedor de localização com fusível do Google API dos Serviços de Localização.

## <a name="related-links"></a>Links relacionados

- [Local (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/location)
- [FusedLocationProvider (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fusedlocationprovider)
- [Google Play Services](https://developer.android.com/google/play-services/index.html)
- [Classe de critérios](xref:Android.Locations.Criteria)
- [Classe localmanager](xref:Android.Locations.LocationManager)
- [Classe LocationListener](xref:Android.Locations.ILocationListener)
- [API LocationClient](https://developer.android.com/reference/com/google/android/gms/location/LocationClient.html)
- [API LocationListener](https://developer.android.com/reference/com/google/android/gms/location/LocationListener.html)
- [API LocationRequest](https://developer.android.com/reference/com/google/android/gms/location/LocationRequest.html)
