---
title: Serviços de localização
description: Este guia apresenta o reconhecimento de local em aplicativos Android e ilustra como obter a localização do usuário usando a API do serviço local do Android, bem como o provedor de adição múltipla local disponível com a API de serviços de localização do Google.
ms.prod: xamarin
ms.assetid: 0008682B-6CEF-0C1D-3200-56ECF58F5D3C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/22/2018
ms.openlocfilehash: e3cfc9a345c8ab92b35ad428b550ec42de6312e5
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120288"
---
# <a name="location-services"></a>Serviços de localização

_Este guia apresenta o reconhecimento de local em aplicativos Android e ilustra como obter a localização do usuário usando a API do serviço local do Android, bem como o provedor de adição múltipla local disponível com a API de serviços de localização do Google._

## <a name="location-services-overview"></a>Visão geral dos serviços de localização

O Android fornece acesso a várias tecnologias de local, como local de torre de célula, Wi-Fi e GPS. Os detalhes de cada tecnologia de localização são abstraídos por meio *provedores de local*, permitindo que os aplicativos obter os locais da mesma forma, independentemente do provedor usado. Este guia apresenta o provedor de adição múltipla local, uma parte do Google Play Services, que determina a melhor maneira de obter o local dos dispositivos com base em quais provedores estão disponíveis e como o dispositivo está sendo usado de forma inteligente. API do serviço local do Android e mostra como se comunicar com o local do sistema de serviço usando um `LocationManager`. A segunda parte do guia explora o API do Android dos serviços de localização usando a `LocationManager`.
 
Como um regra geral, os aplicativos devem preferir usar o provedor local adição múltipla, recorrendo a mais antigo local de serviço API do Android somente quando necessário.

## <a name="location-fundamentals"></a>Conceitos básicos de localização

No Android, não importa qual API você escolher para trabalhar com dados de localização, vários conceitos permanecem os mesmos. Esta seção apresenta os provedores de local e permissões relacionadas a locais.

### <a name="location-providers"></a>Provedores de local

Várias tecnologias são usadas internamente para identificar a localização do usuário. O hardware usado depende do tipo de *localizador* selecionado para o trabalho de coleta de dados. Android usa três provedores de local:

-   **Provedor de GPS** &ndash; GPS fornece o local mais preciso, usa mais poder e funciona melhor em áreas externas. Este provedor usa uma combinação de GPS e GPS assistido ([aGPS](http://en.wikipedia.org/wiki/Assisted_GPS)), que retorna dados GPS coletados pelo torres de celulares.

-   **Provedor de rede** &ndash; fornece uma combinação de dados do Wi-Fi e celular, incluindo dados de aGPS coletados pelo torres de célula. Ele usa menos energia do que o provedor de GPS, mas retorna dados de localização de precisão variável.

-   **Provedor de passivo** &ndash; uma opção "acumulada" usando provedores solicitados por outros aplicativos ou serviços para gerar dados de localização em um aplicativo. Isso é menos confiável, mas a opção de economia de energia ideal para aplicativos que não exigem atualizações de constante local para trabalhar.

Provedores de local não estão sempre disponíveis. Por exemplo, queremos usar o GPS nosso aplicativo, mas GPS podem ser desativado nas configurações ou o dispositivo pode não ter GPS em todos os. Se um provedor específico não estiver disponível, escolher o provedor pode retornar `null`.

### <a name="location-permissions"></a>Permissões de localização

Um aplicativo com reconhecimento de local precisa acessar os sensores de hardware do dispositivo para receber dados de celular, Wi-Fi e GPS. O acesso é controlado por meio de permissões apropriadas no manifesto do Android do aplicativo.
Há duas permissões &ndash; dependendo das necessidades do seu aplicativo e sua escolha de API, você desejará permitir que um:

-   `ACCESS_FINE_LOCATION` &ndash; Permite acesso de aplicativo para GPS.
    Necessário para o *provedor GPS* e *provedor passivo* opções (*passivo provedor precisa de permissão para acessar dados GPS coletados por outro aplicativo ou serviço*). Permissão opcional para o *provedor de rede*.

-   `ACCESS_COARSE_LOCATION` &ndash; Permite acesso de aplicativo para celular e Wi-Fi local. Necessário para *provedor de rede* se `ACCESS_FINE_LOCATION` não está definido.

Para aplicativos que usam a API versão 21 (Android 5.0 Lollipop) ou superior, você pode habilitar `ACCESS_FINE_LOCATION` e ainda executados em dispositivos que não possuem hardware GPS. Se seu aplicativo requer hardware GPS, você deve adicionar explicitamente um `android.hardware.location.gps` `uses-feature` elemento para o manifesto do Android. Para obter mais informações, consulte o Android [usa o recurso](https://developer.android.com/guide/topics/manifest/uses-feature-element.html) referência de elemento.

Para definir as permissões, expanda o **propriedades** pasta o **painel de soluções** e clique duas vezes em **androidmanifest. XML**. As permissões serão listadas na **permissões necessárias**:

[![Captura de tela das configurações do Android necessárias permissões de manifesto](location-images/location-01-xs.png)](location-images/location-01-xs.png#lightbox)

Definição de uma dessas permissões informa ao Android que seu aplicativo precisa de permissão do usuário para acessar os provedores de local. Dispositivos que executar a API nível 22 (Android 5.1) ou inferior solicitará ao usuário conceder essas permissões a cada vez que o aplicativo está instalado. Em dispositivos que executam a API nível 23 (Android 6.0) ou superior, o aplicativo deve executar uma verificação de permissão de tempo de execução antes de fazer uma solicitação do localizador. 

> [!NOTE]
>Observação: A configuração `ACCESS_FINE_LOCATION` implica acesso a ambos os dados de localização aproximada e tudo bem. Você nunca precisará definir ambas as permissões, apenas o *mínimo* permissão ao seu aplicativo requer para funcionar.

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

Aplicativos devem ser tolerante a falhas do cenário em que o usuário não conceder a permissão (ou revogou a permissão) e ter uma maneira de lidar normalmente com essa situação. Consulte a [guia de permissões](~/android/app-fundamentals/permissions.md) para obter mais detalhes sobre a implementação de permissão de tempo de execução verifica no xamarin. Android.


## <a name="using-the-fused-location-provider"></a>Usando o provedor de adição múltipla local

O provedor de adição múltipla local é a maneira preferencial para aplicativos Android receber atualizações de local do dispositivo porque ele selecionará com eficiência o localizador do Windows durante o tempo de execução para fornecer as melhores informações de local de uma maneira eficiente de bateria. Por exemplo, um usuário andando ao ar livre obtém o melhor local lendo com GPS. Se os usuários se deslocam, em seguida, em locais fechados, onde o GPS funciona mal (se houver), o provedor de adição múltipla local pode alternar automaticamente para Wi-Fi, que funciona melhor em locais fechados.
 
A API do provedor de adição múltipla local fornece uma variedade de outras ferramentas para capacitar aplicativos com reconhecimento de local, incluindo isolamento geográfico e monitoramento de atividades. Nesta seção, vamos nos concentrar Noções básicas sobre como configurar o `LocationClient`, estabelecendo provedores e obter a localização do usuário.

O provedor de adição múltipla local faz parte do [Google Play Services](http://developer.android.com/google/play-services/index.html).
O pacote do Google Play Services deve ser instalado e configurado corretamente no aplicativo para a API do provedor de adição múltipla local para trabalhar, e o dispositivo deve ter o APK Google Play Services instalado.

Antes de um xamarin. Android aplicativo pode usar o provedor de adição múltipla local, ele deve adicionar o **Xamarin.GooglePlayServices.Maps** pacote ao projeto. Além disso, os seguintes `using` instruções devem ser adicionadas a quaisquer arquivos de origem que fazem referência as classes descritas abaixo:

```csharp
using Android.Gms.Common;
using Android.Gms.Location;
```

### <a name="checking-if-google-play-services-is-installed"></a>Verificando se o Google Play Services está instalado

Um será xamarin. Android falha se tentar usar o provedor de adição múltipla local quando o Google Play Services não está instalado (ou desatualizado) e em seguida, uma exceção de tempo de execução ocorreria.  Se Google Play Services não estiver instalado, em seguida, o aplicativo deve voltar para o serviço de localização Android discutidos acima. Se o Google Play Services está desatualizado, o aplicativo poderia exibir uma mensagem ao usuário solicitando que eles atualizem a versão instalada do Google Play Services.

Este trecho de código é um exemplo de como uma atividade do Android pode verificar programaticamente se o Google Play Services está instalado:

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

Para interagir com o provedor de adição múltipla local, um aplicativo xamarin. Android deve ter uma instância da `FusedLocationProviderClient`. Essa classe expõe os métodos necessários para assinar as atualizações de local e para recuperar o último local conhecido do dispositivo.

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

O `FusedLocationProviderClient.GetLastLocationAsync()` método fornece uma maneira simple e sem bloqueio para um aplicativo xamarin. Android obter rapidamente o último local conhecido do dispositivo com sobrecarga mínima de codificação.

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

Um aplicativo xamarin. Android também pode assinar atualizações de local do localizador adição múltipla usando o `FusedLocationProviderClient.RequestLocationUpdatesAsync` método, conforme mostrado neste trecho de código:

```csharp
await fusedLocationProviderClient.RequestLocationUpdatesAsync(locationRequest, locationCallback);
```
Esse método utiliza dois parâmetros:

-   **`Android.Gms.Location.LocationRequest`** &ndash; Um `LocationRequest` objeto é como um aplicativo xamarin. Android passa os parâmetros em como o provedor de adição múltipla local deve funcionar. O `LocationRequest` mantém informações tais como solicitações frequentes devem ser feitas ou importantes como uma atualização precisas de localização deve ser. Por exemplo, uma solicitação de localização importantes fará com que o dispositivo para usar o GPS e, consequentemente, mais energia, ao determinar o local. Este trecho de código mostra como criar um `LocationRequest` para um local com alta precisão, verificação de aproximadamente a cada cinco minutos, durante uma atualização local (mas não antes de dois minutos entre as solicitações). O provedor de adição múltipla local usará um `LocationRequest` como uma orientação para a qual provedor de localização será usado durante a tentativa de determinar o local do dispositivo:

    ```csharp
    LocationRequest locationRequest = new LocationRequest()
                                      .SetPriority(LocationRequest.PriorityHighAccuracy)
                                      .SetInterval(60 * 1000 * 5)
                                      .SetFastestInterval(60 * 1000 * 2);
    ```
                                          

-   **`Android.Gms.Location.LocationCallback`** &ndash; Para receber atualizações de local, um aplicativo xamarin. Android deve ter como subclasse o `LocationProvider` classe abstrata. Essa classe exposta dois métodos que talvez invocado pelo provedor de adição múltipla local para atualizar o aplicativo com informações de local. Isso será discutido em mais detalhes abaixo.

Para notificar um aplicativo xamarin. Android de uma atualização local, o provedor local adição múltipla invocará o `LocationCallBack.OnLocationResult(LocationResult result)`. O `Android.Gms.Location.LocationResult` parâmetro conterá as informações de local de atualização.

Quando o provedor de adição múltipla local detecta uma alteração na disponibilidade de dados local, ele chamará o `LocationProvider.OnLocationAvaibility(LocationAvailability
locationAvailability)` método. Se o `LocationAvailability.IsLocationAvailable` propriedade retorna `true`, em seguida, é possível pressupor que os resultados de localização do dispositivo relatado pelo `OnLocationResult` são tão precisas e tão atualizados conforme exigido pelo `LocationRequest`. Se `IsLocationAvailable` for false, nenhum resultado local será retornado por `OnLocationResult`.

Este trecho de código é uma implementação de exemplo de `LocationCallback` objeto:

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

## <a name="using-the-android-location-service-api"></a>Usando a API de serviço local do Android

O serviço de localização Android é uma API mais antiga para usar informações de localização no Android. Dados de localização são coletados pelos sensores de hardware e coletados por um serviço do sistema, que pode é acessado no aplicativo com um `LocationManager` classe e um `ILocationListener`.

O serviço de localização é mais adequado para aplicativos que devem ser executados em dispositivos que não têm o Google Play Services instalado.

O serviço de localização é um tipo especial de [serviço](http://developer.android.com/guide/components/services.html) gerenciada pelo sistema. Um serviço do sistema interage com o hardware do dispositivo e está sempre em execução. Para tocar nas atualizações do local em nosso aplicativo, inscreveremos para atualizações de local do serviço de localização de sistema usando um `LocationManager` e um `RequestLocationUpdates` chamar.

Para obter a localização do usuário usando o serviço de localização Android envolve várias etapas:

1.  Obtenha uma referência para o `LocationManager` service.
2.  Implementar o `ILocationListener` interface e o identificador de eventos quando o local é alterado.
3.  Use o `LocationManager` para atualizações da solicitação de local para um provedor especificado. O `ILocationListener` da etapa anterior será usado para receber retornos de chamada do `LocationManager`.
4.  Impedir que as atualizações de local quando o aplicativo não é mais apropriado receber atualizações.

### <a name="location-manager"></a>Gerenciador de localidade

Podemos acessar o serviço do sistema local com uma instância da `LocationManager` classe. `LocationManager` é uma classe especial que nos permite interagir com o serviço local do sistema e chamar métodos nela. Um aplicativo pode obter uma referência para o `LocationManager` chamando `GetSystemService` e passando um tipo de serviço, conforme mostrado abaixo:

```csharp
LocationManager locationManager = (LocationManager) GetSystemService(Context.LocationService);
```

`OnCreate` é um bom lugar para obter uma referência para o `LocationManager`.
É uma boa ideia manter o `LocationManager` como uma variável de classe, para que podemos pode chamá-lo em vários pontos no ciclo de vida de atividade.

### <a name="request-location-updates-from-the-locationmanager"></a>Atualizações da solicitação de local do LocationManager

Depois que o aplicativo tem uma referência para o `LocationManager`, ele precisa informar o `LocationManager` quais tipos de informações de local que são necessários e a frequência com que essa informação deve ser atualizada. Faça isso chamando `RequestionLocationUpdates` sobre o `LocationManager` objeto e passar alguns critérios para atualizações e um retorno de chamada que receberá as atualizações de local. Esse retorno de chamada é um tipo que deve implementar o `ILocationListener` interface (descrita em mais detalhes mais adiante neste guia).

O `RequestionLocationUpdates` método informa o local do sistema de serviço que seu aplicativo gostaria de começar a receber atualizações de local. Esse método permite que você especifique o provedor, bem como os limites de tempo e a distância para controlar a frequência de atualização. Por exemplo, o método a seguir abaixo local solicitações atualizações do provedor de localização de GPS cada 2000 milissegundos, e somente quando o local for alterado mais de 1 metre:

```csharp
// For this example, this method is part of a class that implements ILocationListener, described below
locationManager.RequestLocationUpdates(LocationManager.GpsProvider, 2000, 1, this);
```

Um aplicativo deve solicitar atualizações de local apenas quantas vezes for necessário para o aplicativo tem um bom desempenho. Isso preserva a vida útil da bateria e cria uma melhor experiência do usuário.

### <a name="responding-to-updates-from-the-locationmanager"></a>Respondendo a atualizações do LocationManager

Depois que um aplicativo solicitou que as atualizações do `LocationManager`, ele pode receber informações do serviço com a implementação de [ `ILocationListener` ](https://developer.xamarin.com/api/type/Android.Locations.ILocationListener/) interface. Essa interface fornece quatro métodos de escuta para o serviço local e o provedor de localização, `OnLocationChanged`. O sistema irá chamar `OnLocationChanged` quando a localização do usuário muda bastante para ser qualificado como uma alteração local de acordo com os critérios definidos ao solicitar atualizações de local. 

O código a seguir mostra os métodos no `ILocationListener` interface:

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

### <a name="unsubscribing-to-locationmanager-updates"></a>Cancelando a inscrição para atualizações de LocationManager

Para conservar os recursos do sistema, um aplicativo deve cancelar a assinatura de atualizações local assim que possível. O `RemoveUpdates` método informa o `LocationManager` pare de enviar atualizações para o nosso aplicativo.  Por exemplo, uma atividade pode chamar `RemoveUpdates` no `OnPause` método para que somos capazes de economizar energia se um aplicativo não precisa de atualizações local enquanto sua atividade não está na tela:

```csharp
protected override void OnPause ()
{
    base.OnPause ();
    locationManager.RemoveUpdates (this);
}
```

Se seu aplicativo precisar obter atualizações de local enquanto em segundo plano, você desejará criar um serviço personalizado que assina o serviço local do sistema. Consulte a [Backgrounding com serviços do Android](~/android/app-fundamentals/services/index.md) guia para obter mais informações.

### <a name="determining-the-best-location-provider-for-the-locationmanager"></a>Determinar o melhor provedor de local para o LocationManager

O aplicativo acima define GPS como o localizador do Windows. No entanto, GPS podem não estar disponíveis em todos os casos, como se o dispositivo for recinto fechado ou não tem um receptor GPS. Se esse for o caso, o resultado será um `null` retornado para o provedor.

Para obter seu aplicativo para trabalhar quando GPS não estiver disponível, você deve usar o `GetBestProvider` método pedir para o melhor provedor de local (com suporte do dispositivo e usuário habilitado) disponível na inicialização do aplicativo. Em vez de passar em um provedor específico, você pode informar `GetBestProvider` os requisitos para o provedor, como precisão e power - com uma [ `Criteria` objeto](https://developer.xamarin.com/api/type/Android.Locations.Criteria/). `GetBestProvider` Retorna o provedor a melhor para os critérios especificados.

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
>  Se o usuário desabilitou todos os provedores de localização `GetBestProvider` retornará `null`. Para ver como esse código funciona em um dispositivo real, certifique-se de habilitar em redes de celular, Wi-Fi e GPS **as configurações do Google > local > modo** conforme mostrado nesta captura de tela:

[![Tela de modo do local de configurações em um telefone Android](location-images/location-02.png)](location-images/location-02.png#lightbox)

Captura de tela abaixo demonstra o local de aplicativo em execução usando `GetBestProvider`:

[![Aplicativo GetBestProvider exibindo latitude, longitude e provedor](location-images/location-03.png)](location-images/location-03.png#lightbox)

Tenha em mente que `GetBestProvider` não altera dinamicamente o provedor. Em vez disso, ele determina o melhor provedor disponível uma vez durante o ciclo de vida de atividade. Se o status do provedor for alterada depois que ele tiver sido definido, o aplicativo exigir código adicional na `ILocationListener` métodos &ndash; `OnProviderEnabled`, `OnProviderDisabled`, e `OnStatusChanged` &ndash; para lidar com cada possibilidade relacionada para o opção de provedor.

## <a name="summary"></a>Resumo

Este guia abordou como obter a localização do usuário usando o serviço de localização Android e o provedor de adição múltipla local da API de serviços de localização do Google.


## <a name="related-links"></a>Links relacionados

- [Local (exemplo)](https://developer.xamarin.com/samples/Location/)
- [FusedLocationProvider (amostra)](https://developer.xamarin.com/samples/FusedLocationProvider/)
- [Google Play Services](http://developer.android.com/google/play-services/index.html)
- [Classe de critérios](https://developer.xamarin.com/api/type/Android.Locations.Criteria/)
- [Classe LocationManager](https://developer.xamarin.com/api/type/Android.Locations.LocationManager/)
- [Classe LocationListener](https://developer.xamarin.com/api/type/Android.Locations.ILocationListener/)
- [LocationClient API](http://developer.android.com/reference/com/google/android/gms/location/LocationClient.html)
- [LocationListener API](http://developer.android.com/reference/com/google/android/gms/location/LocationListener.html)
- [LocationRequest API](https://developer.android.com/reference/com/google/android/gms/location/LocationRequest.html)
