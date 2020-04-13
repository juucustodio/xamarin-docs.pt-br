---
title: Serviços de localização no Android
description: Este guia introduz a conscientização de localização em aplicativos Android e ilustra como obter a localização do usuário usando a API do Serviço de Localização do Android, bem como o provedor de localização fundido disponível com a API do Google Location Services.
ms.prod: xamarin
ms.assetid: 0008682B-6CEF-0C1D-3200-56ECF58F5D3C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/22/2018
ms.openlocfilehash: 0fc74ae2307ffd14f8c52515c93993a51455997a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "80805956"
---
# <a name="location-services-on-android"></a>Serviços de localização no Android

_Este guia introduz a conscientização de localização em aplicativos Android e ilustra como obter a localização do usuário usando a API do Serviço de Localização do Android, bem como o provedor de localização fundido disponível com a API do Google Location Services._

O Android fornece acesso a várias tecnologias de localização, como localização de torres de celular, Wi-Fi e GPS. Os detalhes de cada tecnologia de localização são abstratos através de provedores de *localização,* permitindo que os aplicativos obtenham locais da mesma forma, independentemente do provedor utilizado. Este guia apresenta o provedor de localização fundido, uma parte do Google Play Services, que determina inteligentemente a melhor maneira de obter a localização dos dispositivos com base em quais provedores estão disponíveis e como o dispositivo está sendo usado. Android Location Service API e mostra como se `LocationManager`comunicar com o serviço de localização do sistema usando um . A segunda parte do guia explora a API `LocationManager`de serviços de localização do Android usando o .

Como regra geral, os aplicativos devem preferir usar o provedor de localização fundido, recuando a API mais antiga do Serviço de Localização do Android somente quando necessário.

## <a name="location-fundamentals"></a>Fundamentos de localização

No Android, não importa qual API você escolha para trabalhar com dados de localização, vários conceitos permanecem os mesmos. Esta seção introduz provedores de localização e permissões relacionadas a localização.

### <a name="location-providers"></a>Provedores de localização

Várias tecnologias são usadas internamente para identificar a localização do usuário. O hardware utilizado depende do tipo de provedor de *localização* selecionado para o trabalho de coleta de dados. O Android usa três provedores de localização:

- **Gps Provider** &ndash; GPS dá a localização mais precisa, usa mais energia e funciona melhor ao ar livre. Este provedor usa uma combinação de GPS e GPS assistido[(aGPS),](https://en.wikipedia.org/wiki/Assisted_GPS)que retorna dados de GPS coletados por torres celulares.

- **O Provedor de** &ndash; Rede fornece uma combinação de dados WiFi e Cellular, incluindo dados aGPS coletados por torres de celular. Ele usa menos energia do que o Provedor GPS, mas retorna dados de localização de precisão variável.

- **Provedor** &ndash; passivo Uma opção de "piggyback" usando provedores solicitados por outros aplicativos ou Serviços para gerar dados de localização em um aplicativo. Esta é uma opção menos confiável, mas de economia de energia ideal para aplicativos que não requerem atualizações constantes de localização para funcionar.

Os provedores de localização nem sempre estão disponíveis. Por exemplo, podemos querer usar GPS para nossa aplicação, mas o GPS pode ser desligado em Configurações, ou o dispositivo pode não ter GPS em tudo. Se um provedor específico não estiver disponível, a escolha desse provedor pode retornar `null`.

### <a name="location-permissions"></a>Permissões de localização

Um aplicativo com reconhecimento de localização precisa acessar os sensores de hardware de um dispositivo para receber dados GPS, Wi-Fi e celulares. O acesso é controlado através de permissões apropriadas no Manifesto Android do aplicativo.
Existem duas permissões disponíveis &ndash; dependendo dos requisitos do seu aplicativo e sua escolha de API, você vai querer permitir uma:

- `ACCESS_FINE_LOCATION`&ndash; Permite que um aplicativo acesse o GPS.
    Necessário para as opções *de Provedor GPS* e Provedor *Passivo* ( Provedor Passivo precisa*de permissão para acessar dados gps coletados por outro aplicativo ou serviço*). Permissão opcional para o *Provedor de Rede*.

- `ACCESS_COARSE_LOCATION`&ndash; Permite que um aplicativo acesse a localização celular e Wi-Fi. Necessário para o `ACCESS_FINE_LOCATION` Provedor de *Rede* se não estiver definido.

Para aplicativos que visam a versão 21 da API (Android 5.0 Lollipop) ou superior, você pode habilitar `ACCESS_FINE_LOCATION` e ainda executar em dispositivos que não possuem hardware GPS. Se o seu aplicativo precisar de hardware `android.hardware.location.gps` `uses-feature` GPS, você deve adicionar explicitamente um elemento ao Manifesto do Android. Para obter mais informações, consulte a referência do elemento [uso-recurso](https://developer.android.com/guide/topics/manifest/uses-feature-element.html) do Android.

Para definir as permissões, expanda a pasta **Propriedades** no **Bloco de Soluções** e clique duas vezes em **AndroidManifest.xml**. As permissões serão listadas em **Permissões Necessárias:**

[![Captura de tela das configurações de permissões necessárias do Manifesto do Android](location-images/location-01-xs.png)](location-images/location-01-xs.png#lightbox)

A configuração de qualquer uma dessas permissões informa ao Android que seu aplicativo precisa de permissão do usuário para acessar os provedores de localização. Dispositivos que executam a API nível 22 (Android 5.1) ou inferior pedirão ao usuário que conceda essas permissões cada vez que o aplicativo for instalado. Em dispositivos que executam a API nível 23 (Android 6.0) ou superior, o aplicativo deve realizar uma verificação de permissão de tempo de execução antes de fazer uma solicitação do provedor de localização. 

> [!NOTE]
>Nota: `ACCESS_FINE_LOCATION` A configuração implica acesso a dados de localização grosseiros e finos. Você nunca deve ter que definir ambas as permissões, apenas a permissão *mínima* que seu aplicativo precisa para funcionar.

Este trecho é um exemplo de como verificar se `ACCESS_FINE_LOCATION` um aplicativo tem permissão para a permissão:

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

Os aplicativos devem ser tolerantes com o cenário em que o usuário não concederá permissão (ou revogou a permissão) e terá uma maneira de lidar graciosamente com essa situação. Consulte o [guia Permissões](~/android/app-fundamentals/permissions.md) para obter mais detalhes sobre a implementação de verificações de permissão em tempo de execução no Xamarin.Android.

## <a name="using-the-fused-location-provider"></a>Usando o provedor de localização fundido

O provedor de localização fundido é a maneira preferida para que os aplicativos Android recebam atualizações de localização do dispositivo, pois selecionará eficientemente o provedor de localização durante o tempo de execução para fornecer as melhores informações de localização de forma eficiente. Por exemplo, um usuário andando ao ar livre obtém a melhor leitura de localização com GPS. Se o usuário então andar dentro de casa, onde o GPS funciona mal (se em tudo), o provedor de localização fundido pode mudar automaticamente para WiFi, que funciona melhor dentro de casa.

A API do provedor de localização fundida fornece uma variedade de outras ferramentas para capacitar aplicativos com reconhecimento de localização, incluindo geofencing e monitoramento de atividades. Nesta seção, vamos focar no básico de configurar `LocationClient`os provedores e obter a localização do usuário.

O provedor de localização fundido faz parte do [Google Play Services](https://developer.android.com/google/play-services/index.html).
O pacote do Google Play Services deve ser instalado e configurado corretamente no aplicativo para que a API do provedor de localização fundida funcione, e o dispositivo deve ter o APK do Google Play Services instalado.

Antes que um aplicativo Xamarin.Android possa usar o provedor de localização fundido, ele deve adicionar o pacote **Xamarin.GooglePlayServices.Localização** ao projeto. Além disso, `using` as seguintes instruções devem ser adicionadas a quaisquer arquivos de origem que remetem às classes descritas abaixo:

```csharp
using Android.Gms.Common;
using Android.Gms.Location;
```

### <a name="checking-if-google-play-services-is-installed"></a>Verificando se o Google Play Services está instalado

Um Xamarin.Android falhará se tentar usar o provedor de localização fundido quando o Google Play Services não estiver instalado (ou desatualizado), então uma exceção de tempo de execução ocorreria.  Se o Google Play Services não estiver instalado, o aplicativo deve voltar ao Serviço de Localização do Android discutido acima. Se o Google Play Services estiver desatualizado, então o aplicativo pode exibir uma mensagem para o usuário pedindo que atualize a versão instalada do Google Play Services.

Este trecho é um exemplo de como uma atividade do Android pode verificar programáticamente se o Google Play Services está instalado:

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

### <a name="fusedlocationproviderclient"></a>FusedLocalizaçãoProvedordeclienteCliente

Para interagir com o provedor de localização fundido, um aplicativo Xamarin.Android deve ter uma instância do `FusedLocationProviderClient`. Esta classe expõe os métodos necessários para assinar atualizações de localização e recuperar a última localização conhecida do dispositivo.

O `OnCreate` método de uma Atividade é um local `FusedLocationProviderClient`adequado para obter uma referência ao , como demonstrado no seguinte trecho de código:

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

### <a name="getting-the-last-known-location"></a>Obtendo a última localização conhecida

O `FusedLocationProviderClient.GetLastLocationAsync()` método fornece uma maneira simples e não bloqueada para um aplicativo Xamarin.Android obter rapidamente a última localização conhecida do dispositivo com sobrecarga de codificação mínima.

Este trecho mostra como usar `GetLastLocationAsync` o método para recuperar a localização do dispositivo:

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

### <a name="subscribing-to-location-updates"></a>Subscrevendo atualizações de localização

Um aplicativo Xamarin.Android também pode assinar atualizações de localização `FusedLocationProviderClient.RequestLocationUpdatesAsync` do provedor de localização fundido usando o método, como mostrado neste trecho de código:

```csharp
await fusedLocationProviderClient.RequestLocationUpdatesAsync(locationRequest, locationCallback);
```

Este método requer dois parâmetros:

- **`Android.Gms.Location.LocationRequest`**&ndash; Um `LocationRequest` objeto é como um aplicativo Xamarin.Android passa os parâmetros sobre como o provedor de localização fundido deve funcionar. As `LocationRequest` informações de base, como a freqüência das solicitações devem ser feitas ou a importância de uma atualização precisa de localização. Por exemplo, uma solicitação de localização importante fará com que o dispositivo use o GPS e, consequentemente, mais energia, ao determinar a localização. Este trecho de código mostra `LocationRequest` como criar um para um local com alta precisão, verificando aproximadamente a cada cinco minutos para uma atualização de localização (mas não mais cedo do que dois minutos entre as solicitações). O provedor de localização `LocationRequest` fundido usará uma orientação para qual provedor de localização usar ao tentar determinar a localização do dispositivo:

    ```csharp
    LocationRequest locationRequest = new LocationRequest()
                                      .SetPriority(LocationRequest.PriorityHighAccuracy)
                                      .SetInterval(60 * 1000 * 5)
                                      .SetFastestInterval(60 * 1000 * 2);
    ```

- **`Android.Gms.Location.LocationCallback`**&ndash; Para receber atualizações de localização, um aplicativo Xamarin.Android deve subclasse a `LocationProvider` classe abstrata. Esta classe expôs dois métodos que talvez invocados pelo provedor de localização fundido para atualizar o aplicativo com informações de localização. Isso será discutido com mais detalhes abaixo.

Para notificar um aplicativo Xamarin.Android de uma atualização de `LocationCallBack.OnLocationResult(LocationResult result)`localização, o provedor de localização fundido invocará o . O `Android.Gms.Location.LocationResult` parâmetro conterá as informações de localização da atualização.

Quando o provedor de localização fundido detectar uma alteração na disponibilidade `LocationProvider.OnLocationAvailability(LocationAvailability
locationAvailability)` de dados de localização, ele chamará o método. Se `LocationAvailability.IsLocationAvailable` a `true`propriedade retornar, pode-se supor `OnLocationResult` que os resultados de localização do `LocationRequest`dispositivo relatados são tão precisos e atualizados quanto exigido pelo . Se `IsLocationAvailable` for falso, então nenhum resultado `OnLocationResult`de local será devolvido por .

Este trecho de código é uma `LocationCallback` implementação amostral do objeto:

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

## <a name="using-the-android-location-service-api"></a>Usando a API do Serviço de Localização do Android

O Android Location Service é uma API mais antiga para usar informações de localização no Android. Os dados de localização são coletados por sensores de hardware e `LocationManager` coletados `ILocationListener`por um serviço de sistema, que é acessado no aplicativo com uma classe e um .

O Serviço de Localização é o mais adequado para aplicativos que devem ser executados em dispositivos que não possuem o Google Play Services instalado.

O Serviço de Localização é um tipo especial de [Serviço](https://developer.android.com/guide/components/services.html) gerenciado pelo Sistema. Um Serviço de Sistema interage com o hardware do dispositivo e está sempre em execução. Para aproveitar as atualizações de localização em nosso aplicativo, assinaremos `LocationManager` atualizações `RequestLocationUpdates` de localização do serviço de localização do sistema usando uma chamada e uma chamada.

Para obter a localização do usuário usando o Android Location Service envolve várias etapas:

1. Obter uma referência `LocationManager` para o serviço.
2. Implemente `ILocationListener` a interface e manuseie eventos quando o local mudar.
3. Use `LocationManager` o para solicitar atualizações de localização para um provedor especificado. A `ILocationListener` etapa anterior será usada para receber retornos de chamada do `LocationManager`.
4. Interrompa as atualizações de localização quando o aplicativo não é mais apropriado receber atualizações.

### <a name="location-manager"></a>Gerenciador de localização

Podemos acessar o serviço de localização `LocationManager` do sistema com uma instância da classe. `LocationManager`é uma classe especial que nos permite interagir com o serviço de localização do sistema e métodos de chamada sobre ele. Um aplicativo pode obter `LocationManager` uma `GetSystemService` referência ao chamado e passando em um tipo de Serviço, conforme mostrado abaixo:

```csharp
LocationManager locationManager = (LocationManager) GetSystemService(Context.LocationService);
```

`OnCreate`é um bom lugar para `LocationManager`obter uma referência ao .
É uma boa idéia manter `LocationManager` a variável como classe, para que possamos chamá-la em vários pontos do ciclo de vida da Atividade.

### <a name="request-location-updates-from-the-locationmanager"></a>Solicitar atualizações de localização do LocationManager

Uma vez que o `LocationManager`aplicativo tenha uma `LocationManager` referência ao , ele precisa dizer o tipo de informações de localização que são necessárias e com que frequência essas informações devem ser atualizadas. Faça isso `RequestLocationUpdates` chamando `LocationManager` o objeto e passando alguns critérios para atualizações e um retorno de chamada que receberá as atualizações de localização. Este retorno de chamada é `ILocationListener` um tipo que deve implementar a interface (descrita com mais detalhes mais tarde neste guia).

O `RequestLocationUpdates` método informa ao Serviço de Localização do sistema que seu aplicativo gostaria de começar a receber atualizações de localização. Este método permite especificar o provedor, bem como os limites de tempo e distância para controlar a frequência de atualização. Por exemplo, o método abaixo solicita atualizações de localização do provedor de localização GPS a cada 2000 milissegundos, e somente quando o local muda mais de 1 metro:

```csharp
// For this example, this method is part of a class that implements ILocationListener, described below
locationManager.RequestLocationUpdates(LocationManager.GpsProvider, 2000, 1, this);
```

Um aplicativo deve solicitar atualizações de localização apenas quantas vezes for necessário para que o aplicativo seja bem-estar. Isso preserva a vida útil da bateria e cria uma melhor experiência para o usuário.

### <a name="responding-to-updates-from-the-locationmanager"></a>Respondendo às atualizações do LocationManager

Uma vez que um aplicativo `LocationManager`tenha solicitado atualizações do , ele [`ILocationListener`](xref:Android.Locations.ILocationListener) pode receber informações do Serviço implementando a interface. Esta interface fornece quatro métodos para ouvir o `OnLocationChanged`serviço de localização e o provedor de localização, . O Sistema `OnLocationChanged` ligará quando a localização do usuário mudar o suficiente para se qualificar como uma alteração de local de acordo com os Critérios definidos ao solicitar atualizações de localização. 

O código a seguir mostra `ILocationListener` os métodos na interface:

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

### <a name="unsubscribing-to-locationmanager-updates"></a>Não assinar atualizações do LocationManager

Para conservar os recursos do sistema, um aplicativo deve cancelar a inscrição para atualizações de localização o mais rápido possível. O `RemoveUpdates` método `LocationManager` diz para parar de enviar atualizações para o nosso aplicativo.  Como exemplo, uma atividade `RemoveUpdates` pode `OnPause` chamar o método para que possamos conservar energia se um aplicativo não precisar de atualizações de localização enquanto sua atividade não estiver na tela:

```csharp
protected override void OnPause ()
{
    base.OnPause ();
    locationManager.RemoveUpdates (this);
}
```

Se o aplicativo precisar receber atualizações de localização enquanto estiver em segundo plano, você deseja criar um serviço personalizado que se inscreva no serviço de localização do sistema. Consulte o [guia Backgrounding with Android Services](~/android/app-fundamentals/services/index.md) para obter mais informações.

### <a name="determining-the-best-location-provider-for-the-locationmanager"></a>Determinando o melhor provedor de localização para o LocationManager

O aplicativo acima define o GPS como o provedor de localização. No entanto, o GPS pode não estar disponível em todos os casos, como se o dispositivo estiver dentro de casa ou não tiver um receptor GPS. Se este for o caso, `null` o resultado é um retorno para o Provedor.

Para que seu aplicativo funcione quando o `GetBestProvider` GPS não estiver disponível, você usa o método para solicitar o melhor provedor de localização disponível (suportado por dispositivos e habilitado pelo usuário) no lançamento do aplicativo. Em vez de passar em um `GetBestProvider` provedor específico, você pode dizer os requisitos para o provedor - como precisão e potência - com um [ `Criteria` objeto](xref:Android.Locations.Criteria). `GetBestProvider`retorna o melhor provedor para os critérios dados.

O código a seguir mostra como obter o melhor provedor disponível e usá-lo ao solicitar atualizações de localização:

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
> Se o usuário tiver desabilitado todos os provedores de localização, `GetBestProvider` retornará `null`. Para ver como esse código funciona em um dispositivo real, certifique-se de ativar redes GPS, Wi-Fi e celulares no **Google Configurações > Modo de localização >** como mostrado nesta captura de tela:
>
> [![Configurações Tela do modo de localização em um telefone Android](location-images/location-02.png)](location-images/location-02.png#lightbox)
>
> A captura de tela abaixo `GetBestProvider`demonstra o aplicativo de localização em execução usando:
>
> [![Aplicativo GetBestProvider exibindo latitude, longitude e provedor](location-images/location-03.png)](location-images/location-03.png#lightbox)
>
> Tenha em `GetBestProvider` mente que isso não muda o provedor dinamicamente. Em vez disso, ele determina o melhor provedor disponível uma vez durante o ciclo de vida da Atividade. Se o status do provedor mudar após o seu conjunto, &ndash; `OnProviderEnabled`o `OnProviderDisabled`aplicativo `OnStatusChanged` &ndash; exigirá código adicional nos `ILocationListener` métodos e lidará com todas as possibilidades relacionadas ao switch do provedor.

## <a name="summary"></a>Resumo

Este guia cobria a obtenção da localização do usuário usando o Serviço de Localização Android e o provedor de localização fundido da API do Google Location Services.

## <a name="related-links"></a>Links relacionados

- [Localização (amostra)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/location)
- [Provedor de localização fundido (amostra)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fusedlocationprovider)
- [Google Play Services](https://developer.android.com/google/play-services/index.html)
- [Classe de Critérios](xref:Android.Locations.Criteria)
- [Classe LocationManager](xref:Android.Locations.LocationManager)
- [Classe Localizador de Localização](xref:Android.Locations.ILocationListener)
- [API do cliente de localização](https://developer.android.com/reference/com/google/android/gms/location/LocationClient.html)
- [LocationListener API](https://developer.android.com/reference/com/google/android/gms/location/LocationListener.html)
- [API de solicitação de localização](https://developer.android.com/reference/com/google/android/gms/location/LocationRequest.html)
