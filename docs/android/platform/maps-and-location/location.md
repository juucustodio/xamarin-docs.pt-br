---
title: "Serviços de localização"
description: "Este guia apresenta o reconhecimento de local em aplicativos Android e ilustra como obter o local do usuário usando a API do serviço local do Android, bem como o provedor local mescladas disponível com a API de serviços do Google local."
ms.topic: article
ms.prod: xamarin
ms.assetid: 0008682B-6CEF-0C1D-3200-56ECF58F5D3C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: beb690fe495d142bb4b0424ad752101fc46da590
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="location-services"></a>Serviços de localização

_Este guia apresenta o reconhecimento de local em aplicativos Android e ilustra como obter o local do usuário usando a API do serviço local do Android, bem como o provedor local mescladas disponível com a API de serviços do Google local._

## <a name="location-services-overview"></a>Visão geral dos serviços de localização

Android fornece acesso a várias tecnologias de local, como local de torre de célula, Wi-Fi e GPS. Os detalhes de cada tecnologia de local são abstraídos por meio de *provedores local*, permitindo que os aplicativos obter os locais da mesma maneira, independentemente do provedor usado. Este guia apresenta o provedor local mescladas, uma parte dos serviços de reproduzir Google, que determina a melhor maneira de obter o local dos dispositivos com base em quais provedores estão disponíveis e como o dispositivo está sendo usado de forma inteligente. API de serviço local Android e mostra como se comunicar com o local do sistema de serviço usando um `LocationManager`. A segunda parte do guia explora a API de serviços de localização Android usando o `LocationManager`.
 
Como um regra geral, os aplicativos devem preferir para usar o provedor local mescladas, retornando mais antiga API do Android local de serviço somente quando necessário.

## <a name="location-fundamentals"></a>Conceitos básicos de localização

No Android, não importa o que você escolher para trabalhar com dados de localização de API, vários conceitos permanecem os mesmos. Esta seção apresenta os provedores de local e permissões de local.

### <a name="location-providers"></a>Provedores de local

Várias tecnologias são usadas internamente para identificar o local do usuário. O hardware usado depende do tipo de *provedor local* selecionado para o trabalho de coleta de dados. Android usa três provedores de local:

-   **Provedor de GPS** &ndash; GPS fornece o local mais preciso, usa mais energia e funciona melhor em áreas externas. Esse provedor usa uma combinação de GPS e assistido GPS ([aGPS](http://en.wikipedia.org/wiki/Assisted_GPS)), que retorna dados GPS coletados pelo torres de celulares.

-   **Provedor de rede** &ndash; fornece uma combinação de dados de Wi-Fi e celulares, incluindo aGPS dados coletados pelo torres de célula. Ele usa menos energia do que o provedor de GPS, mas retorna dados de local de precisão variável.

-   **Provedor passivo** &ndash; uma opção "acumulada" usando provedores solicitados por outros aplicativos ou serviços para gerar dados de localização em um aplicativo. Isso é menos confiável mas a opção de economia de energia ideal para aplicativos que não exigem atualizações de constante local para trabalhar.

Provedores de local não estão sempre disponíveis. Por exemplo, queremos usar GPS em nosso aplicativo, mas GPS poderá ser desativada nas configurações ou o dispositivo talvez não tenha GPS em todos os. Se um provedor específico não estiver disponível, escolher o provedor pode retornar `null`.

### <a name="location-permissions"></a>Permissões de local

Um aplicativo com reconhecimento de local precisa acessar sensores do hardware do dispositivo para receber dados de celular, Wi-Fi e GPS. O acesso é controlado por meio de permissões apropriadas no Android de manifesto do aplicativo.
Há duas permissões &ndash; dependendo requisitos do aplicativo e sua opção de API, você deseja permitir que um:

-   `ACCESS_FINE_LOCATION` &ndash; Permite que um aplicativo acesse GPS.
    Necessário para o *provedor GPS* e *provedor passivo* opções (*provedor passivo precisa de permissão para acessar dados GPS coletados por outro aplicativo ou serviço*). Permissão opcional para o *provedor de rede*.

-   `ACCESS_COARSE_LOCATION` &ndash; Permite que um aplicativo de acesso ao local de Wi-Fi e de celular. Necessário para *provedor de rede* se `ACCESS_FINE_LOCATION` não está definido.

Para aplicativos que se destinam a API versão 21 (Android 5.0 pirulito) ou superior, você pode habilitar `ACCESS_FINE_LOCATION` e ainda executados em dispositivos que não têm hardware GPS. Se seu aplicativo requer hardware GPS, você deve adicionar explicitamente um `android.hardware.location.gps` `uses-feature` elemento para o manifesto do Android. Para obter mais informações, consulte o Android [usa o recurso](https://developer.android.com/guide/topics/manifest/uses-feature-element.html) referência de elemento.

Para definir as permissões, expanda o **propriedades** pasta o **solução preenchimento** e clique duas vezes em **AndroidManifest.xml**. As permissões serão listadas na **permissões necessárias**:

[![Captura de tela das configurações do Android necessárias permissões de manifesto](location-images/location-01-xs.png)](location-images/location-01-xs.png)

Definição de uma dessas permissões informa Android que seu aplicativo precisa de permissão do usuário para acessar os provedores de local. Dispositivos que executar API nível 22 (Android 5.1) ou inferior solicitará ao usuário conceder essas permissões a cada vez que o aplicativo está instalado. Em dispositivos que executam API nível 23 (Android 6.0) ou superior, o aplicativo deve executar uma verificação de permissão de tempo de execução antes de fazer uma solicitação do provedor local. 

> [!NOTE]
>Observação: Configuração `ACCESS_FINE_LOCATION` implica em acesso a dados local grosso e bem. Você nunca deve definir as permissões, somente o *mínimo* permissão requer que seu aplicativo funcione.

Este trecho de código é um exemplo de como verificar se um aplicativo tem permissão para o `ACCESS_FINE_LOCATION` permissão:

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

Aplicativos devem ser tolerante a falhas do cenário em que o usuário não conceder permissão (ou revogou a permissão) e ter uma maneira de lidar normalmente com essa situação. Consulte o [guia permissões](~/android/app-fundamentals/permissions.md) para obter mais detalhes sobre a implementação de permissão de tempo de execução de verificações em xamarin.


## <a name="using-the-fused-location-provider"></a>Usando o provedor local mescladas

O provedor de mescladas local é o modo preferido para aplicativos do Android receber atualizações do local do dispositivo porque ele selecionará com eficiência o provedor local durante o tempo de execução para fornecer as melhores informações de local de uma maneira eficiente de bateria. Por exemplo, um usuário andando em ao ar livre obtém o melhor local ler com GPS. Se o usuário orienta em locais fechados, onde GPS funciona mal (se), o provedor local mescladas pode alternar automaticamente para Wi-Fi, que funciona melhor em locais fechados.
 
A API de provedor mescladas local fornece uma variedade de outras ferramentas para capacitar os aplicativos com reconhecimento de local, incluindo o isolamento geográfico e monitoramento de atividades. Nesta seção, vamos nos concentrar Noções básicas sobre como configurar o `LocationClient`, estabelecendo provedores e obter o local do usuário.

O provedor local mescladas faz parte do [Google executar serviços](http://developer.android.com/google/play-services/index.html). O pacote de serviços do Google reproduzir deve ser instalado e configurado corretamente no aplicativo para a API de provedor local mescladas para trabalhar, e o dispositivo deve ter o APK Google reproduzir Services instalado.

Antes de um xamarin aplicativo pode usar o provedor local mescladas, ela deve adicionar o **Xamarin.GooglePlayServices.Maps** ao projeto.

### <a name="checking-if-google-play-services-is-installed"></a>Verificando se os serviços do Google reproduzir está instalado

Ocorrerá uma será xamarin falhar se tentar usar o provedor de local mescladas quando Google reproduzir Services não está instalado (ou desatualizado), em seguida, uma exceção de tempo de execução.  Se os serviços do Google reproduzir não estiver instalado, então, o aplicativo deve retornar para o serviço de localização Android discutidos acima. Se os serviços do Google reproduzir está desatualizado, o aplicativo pode exibir uma mensagem para o usuário que está solicitando que eles para atualizar a versão instalada do Google executar serviços.

Este trecho de código é um exemplo de como uma atividade Android programaticamente verificar se Google reproduzir Services está instalado:

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

Para interagir com o provedor local mescladas, um aplicativo xamarin deve ter uma instância do `FusedLocationProviderClient`. Essa classe expõe os métodos necessários para se inscrever para atualizações de local e para recuperar o último local conhecido do dispositivo.

O `OnCreate` método de uma atividade é um local adequado para obter uma referência para o `FusedLocationProviderClient`, conforme demonstrado no seguinte trecho de código:

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

O `FusedLocationProviderClient.GetLastLocationAsync()` método fornece uma maneira simple e sem bloqueio para um aplicativo xamarin obter rapidamente o último local conhecido do dispositivo com sobrecarga mínima de codificação.

Este trecho de código mostra como usar o `GetLastLocationAsync` método para recuperar o local do dispositivo:

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

### <a name="subscribing-to-location-updates"></a>Inscrever-se ao local de atualizações

Um aplicativo xamarin também pode assinar atualizações do local do provedor mescladas local usando o `FusedLocationProviderClient.RequestLocationUpdatesAsync` método, como mostra este trecho de código:

```csharp
await fusedLocationProviderClient.RequestLocationUpdatesAsync(locationRequest, locationCallback);
```
Esse método usa dois parâmetros:

-   **`Android.Gms.Location.LocationRequest`** &ndash; Um `LocationRequest` objeto é como um aplicativo xamarin passa os parâmetros como o provedor local mescladas deve funcionar. O `LocationRequest` contém informações tais como solicitações frequentes devem ser feitas ou importantes como uma atualização local precisa deve ser. Por exemplo, uma solicitação de local importante fará com que o dispositivo use o GPS e, consequentemente, mais energia, ao determinar o local. Este trecho de código mostra como criar um `LocationRequest` para um local com alta precisão, verificar aproximadamente a cada cinco minutos para uma atualização local (mas não antes de dois minutos entre as solicitações). O provedor local mescladas usará um `LocationRequest` como orientação para a qual provedor local a ser usado durante a tentativa de determinar o local do dispositivo:

    ```csharp
    LocationRequest locationRequest = new LocationRequest()
                                      .SetPriority(LocationRequest.PriorityHighAccuracy)
                                      .SetInterval(60 * 1000 * 5)
                                      .SetFastestInterval(60 * 1000 * 2);
    ```
                                          

-   **`Android.Gms.Location.LocationCallback`** &ndash; Para receber atualizações do local, um aplicativo xamarin deve subclasse a `LocationProvider` classe abstrata. Essa classe exposta dois métodos que pode ser invocado pelo provedor local mescladas para atualizar o aplicativo com informações de localização. Isso será discutido em mais detalhes abaixo.

Para notificar um aplicativo xamarin de uma atualização local, o provedor local mescladas invocará o `LocationCallBack.OnLocationResult(LocationResult result)`. O `Android.Gms.Location.LocationResult` parâmetro conterá as informações de local de atualização.

Quando o provedor local mescladas detecta uma alteração na disponibilidade de dados local, ele chamará o `LocationProvider.OnLocationAvaibility(LocationAvailability
locationAvailability)` método. Se o `LocationAvailability.IsLocationAvailable` propriedade retorna `true`, em seguida, é possível pressupor que os resultados de local do dispositivo relatado pelo `OnLocationResult` são mais precisas e como atualizado conforme exigido pelo `LocationRequest`. Se `IsLocationAvailable` é false, nenhum resultado local será retornado pela `OnLocationResult`.

Este trecho de código é um exemplo da implementação do `LocationCallback` objeto:

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

## <a name="using-the-android-location-service-api"></a>Usando a API de serviço local Android

O serviço de localização Android é uma API mais antiga para o uso de informações de localização no Android. Dados de local são coletados por sensores de hardware e coletados por um serviço de sistema, que pode é acessado no aplicativo com um `LocationManager` classe e um `ILocationListener`.

O serviço de localização é ideal para aplicativos que devem ser executados em dispositivos que não têm Google executar serviços instalados.

O serviço de localização é um tipo especial de [Service](http://developer.android.com/guide/components/services.html) gerenciadas pelo sistema. Um serviço do sistema interage com o hardware do dispositivo e está sempre em execução. Para aproveitar as atualizações do local em nosso aplicativo, estamos assinarem atualizações do local do serviço de localização de sistema usando um `LocationManager` e um `RequestLocationUpdates` chamar.

Para obter a localização do usuário usando o serviço de localização Android envolve várias etapas:

1.  Obtenha uma referência para o `LocationManager` service.
2.  Implementar o `ILocationListener` interface e o identificador de eventos quando muda o local.
3.  Use o `LocationManager` para atualizações de local de solicitação para um provedor especificado. O `ILocationListener` da etapa anterior será usado para receber retornos de chamada do `LocationManager`.
4.  Interrompa as atualizações de local quando o aplicativo não é mais apropriado receber atualizações.

### <a name="location-manager"></a>Gerenciador de localidade

Podemos acessar o serviço do sistema local com uma instância do `LocationManager` classe. `LocationManager` é uma classe especial que permite interagir com o serviço local do sistema e chamar métodos nela. Um aplicativo pode obter uma referência para o `LocationManager` chamando `GetSystemService` e passando um tipo de serviço, conforme mostrado abaixo:

```csharp
LocationManager locationManager = (LocationManager) GetSystemService(Context.LocationService);
```

`OnCreate` é um bom lugar para obter uma referência para o `LocationManager`.
É recomendável manter o `LocationManager` como uma variável de classe, para que podemos pode chamá-lo em vários pontos no ciclo de vida da atividade.

### <a name="request-location-updates-from-the-locationmanager"></a>Solicitar atualizações de local do LocationManager

Depois que o aplicativo tem uma referência para o `LocationManager`, ele precisa saber o `LocationManager` o tipo de informações de local que são necessárias e a frequência com que informações são atualizado. Faça isso chamando `RequestionLocationUpdates` no `LocationManager` objeto e passar alguns critérios para atualizações e um retorno de chamada que receberão as atualizações de local. Esse retorno de chamada é um tipo que deve implementar o `ILocationListener` interface (descrito em mais detalhes posteriormente neste guia).

O `RequestionLocationUpdates` método informa o local do sistema de serviço que seu aplicativo gostaria de começar a receber atualizações do local. Esse método permite que você especifique o provedor, bem como os limites de tempo e a distância para controlar a frequência de atualização. Por exemplo, o método a seguir abaixo local solicitações atualizações do provedor local de GPS cada 2000 milissegundos, e somente quando o local de alterações mais de 1 medido:

```csharp
// For this example, this method is part of a class that implements ILocationListener, described below
locationManager.RequestLocationUpdates(LocationManager.GpsProvider, 2000, 1, this);
```

Um aplicativo deve solicitar atualizações local somente com a frequência necessária para o aplicativo para o bom desempenho. Isso preserva a vida útil da bateria e cria uma melhor experiência do usuário.

### <a name="responding-to-updates-from-the-locationmanager"></a>Responder a atualizações do LocationManager

Quando um aplicativo solicitar atualizações do `LocationManager`, ela pode receber informações do serviço implementando o [ `ILocationListener` ](https://developer.xamarin.com/api/type/Android.Locations.ILocationListener/) interface. Essa interface fornece quatro métodos de escuta para o local de serviço e o provedor local, `OnLocationChanged`. O sistema irá chamar `OnLocationChanged` quando muda o local do usuário suficiente para se qualificar como uma alteração local de acordo com os critérios definidos ao solicitar atualizações de local. 

O código a seguir mostra os métodos de `ILocationListener` interface:

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

### <a name="unsubscribing-to-locationmanager-updates"></a>Cancelar a assinatura para atualizações de LocationManager

Para conservar os recursos do sistema, um aplicativo deve cancelar a assinatura para atualizações de local assim que possível. O `RemoveUpdates` método informa o `LocationManager` para parar de enviar atualizações para o nosso aplicativo.  Por exemplo, uma atividade pode chamar `RemoveUpdates` no `OnPause` método para que podemos conservar energia se um aplicativo não precisa de atualizações local enquanto sua atividade não está na tela:

```csharp
protected override void OnPause ()
{
    base.OnPause ();
    locationManager.RemoveUpdates (this);
}
```

Se seu aplicativo precisa obter atualizações do local no plano de fundo, convém criar um serviço personalizado que assina o serviço local do sistema. Consulte o [Backgrounding com serviços Android](~/android/app-fundamentals/services/index.md) guia para obter mais informações.

### <a name="determining-the-best-location-provider-for-the-locationmanager"></a>Determinar o melhor provedor local para o LocationManager

O aplicativo acima define GPS como o provedor local. No entanto, GPS podem não estar disponíveis em todos os casos, como se o dispositivo está em locais fechados ou não tem um receptor GPS. Se esse for o caso, o resultado será um `null` retorno para o provedor.

Para obter o seu aplicativo funcione quando GPS não estiver disponível, use o `GetBestProvider` método para solicitar o provedor local disponível (suporte para o dispositivo e usuário habilitado) melhor ao iniciar o aplicativo. Em vez de passar um provedor específico, você pode informar `GetBestProvider` os requisitos para o provedor - como a precisão e ligue - a com um [ `Criteria` objeto](https://developer.xamarin.com/api/type/Android.Locations.Criteria/). `GetBestProvider` Retorna o provedor melhor para os critérios especificados.

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
>  Se o usuário tiver desabilitado a todos os provedores de local, `GetBestProvider` retornará `null`. Para ver como esse código funciona em um dispositivo real, certifique-se de habilitar GPS, Wi-Fi e redes de celulares em **as configurações do Google > local > modo** conforme mostrado nesta captura de tela:

[![Tela de modo do local de configurações em um telefone Android](location-images/location-02.png)](location-images/location-02.png)

Captura de tela abaixo demonstra local aplicativo em execução usando `GetBestProvider`:

[![Aplicativo GetBestProvider exibindo latitude, longitude e provedor](location-images/location-03.png)](location-images/location-03.png)

Tenha em mente que `GetBestProvider` não altera o provedor dinamicamente. Em vez disso, ele determina o provedor mais uma vez durante o ciclo de vida da atividade. Se o status do provedor for alterada após ter sido definido, o aplicativo exigirá código adicional no `ILocationListener` métodos &ndash; `OnProviderEnabled`, `OnProviderDisabled`, e `OnStatusChanged` &ndash; para lidar com todas as possibilidades relacionadas para o opção de provedor.

## <a name="summary"></a>Resumo

Este guia coberto obter o local do usuário usando o serviço de localização Android e o provedor local mescladas da API de serviços do Google local.


## <a name="related-links"></a>Links relacionados

- [Local (exemplo)](https://developer.xamarin.com/samples/Location/)
- [FusedLocationProvider (exemplo)](https://developer.xamarin.com/samples/FusedLocationProvider/)
- [Serviços do Google Play](http://developer.android.com/google/play-services/index.html)
- [Classe de critérios](https://developer.xamarin.com/api/type/Android.Locations.Criteria/)
- [Classe LocationManager](https://developer.xamarin.com/api/type/Android.Locations.LocationManager/)
- [Classe LocationListener](https://developer.xamarin.com/api/type/Android.Locations.ILocationListener/)
- [LocationClient API](http://developer.android.com/reference/com/google/android/gms/location/LocationClient.html)
- [LocationListener API](http://developer.android.com/reference/com/google/android/gms/location/LocationListener.html)
- [LocationRequest API](https://developer.android.com/reference/com/google/android/gms/location/LocationRequest.html)
