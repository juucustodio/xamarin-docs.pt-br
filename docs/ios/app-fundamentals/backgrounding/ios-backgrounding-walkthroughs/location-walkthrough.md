---
title: Passo a passo - usando o local do plano de fundo
ms.topic: article
ms.prod: xamarin
ms.assetid: F8EEA0FD-5614-47FE-ADAC-80A5BCA6EB5F
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: ba460bee067162f8e42f84f230f93cb1cf98ba98
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="walkthrough---using-background-location"></a>Passo a passo - usando o local do plano de fundo

Neste exemplo, vamos criar um iOS aplicativo local que imprime as informações sobre nosso local atual: latitude, longitude e outros parâmetros para a tela. Este aplicativo demonstrará como executar corretamente atualizações local enquanto o aplicativo está ativo ou Backgrounded.

Este passo a passo explica alguma chave backgrounding conceitos, incluindo Registrando um aplicativo como um aplicativo necessário em segundo plano, suspender as atualizações da interface do usuário quando o aplicativo é backgrounded e trabalhando com o `WillEnterBackground` e `WillEnterForeground` `AppDelegate` métodos .

## <a name="application-set-up"></a>Configurar do aplicativo


1. Primeiro, crie um novo **iOS > aplicativo > único aplicativo de exibição (c#)**. Chamá-lo _local_ e certifique-se de que foram selecionados iPad e iPhone.

1. Um aplicativo local se qualifica como um aplicativo em segundo plano necessário no iOS. Registre o aplicativo como um aplicativo local, editando o **Info. plist** arquivo de projeto.

    No Gerenciador de soluções, clique duas vezes no **Info. plist** arquivo para abri-lo e role até o final da lista. Marque ambos o **habilitar modos de segundo plano** e **local atualizações** caixas de seleção.


    No Visual Studio para Mac, ele se parecerá com algo assim:

    [![](location-walkthrough-images/image7.png "Marque os modos de plano de fundo habilitar e as caixas de seleção de local de atualizações")](location-walkthrough-images/image7.png)

    No Visual Studio, **Info. plist** precisa ser atualizado manualmente adicionando o par chave/valor a seguir:

        ```csharp
        <key>UIBackgroundModes</key>
        <array>
            <string>location</string>
        </array>
        ```

1. Agora que o aplicativo está registrado, ele pode obter dados de local do dispositivo. No iOS, o `CLLocationManager` classe é usada para acessar informações de local e pode gerar eventos que fornece atualizações do local.

1. No código, crie uma nova classe chamada `LocationManager` que fornece um único local para várias telas e código para assinar as atualizações de local. No `LocationManager` classe, tornar uma instância do `CLLocationManager` chamado `LocMgr`:

```csharp
        public class LocationManager
        {
          protected CLLocationManager locMgr;

          public LocationManager (){
            this.locMgr = new CLLocationManager();
            this.locMgr.PausesLocationUpdatesAutomatically = false;

            // iOS 8 has additional permissions requirements
            if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
              locMgr.RequestAlwaysAuthorization (); // works in background
              //locMgr.RequestWhenInUseAuthorization (); // only in foreground
            }

            if (UIDevice.CurrentDevice.CheckSystemVersion (9, 0)) {
               locMgr.AllowsBackgroundLocationUpdates = true;
            }
          }

          public CLLocationManager LocMgr{
            get { return this.locMgr; }
          }
        }
```

    The code above sets a number of properties and permissions on the [CLLocationManager](https://developer.xamarin.com/api/type/CoreLocation.CLLocationManager/) class:

    - `PausesLocationUpdatesAutomatically` – Este é um valor booleano que pode ser definido dependendo se o sistema é permitido para pausar atualizações local. Em alguns dispositivos padrão é `true`, que pode fazer com que o dispositivo parar de receber atualizações do local após cerca de 15 minutos de plano de fundo.
    - `RequestAlwaysAuthorization` -Você deve passar esse método para fornecer o aplicativo ao usuário a opção para permitir que o local para ser acessado em segundo plano. `RequestWhenInUseAuthorization` também pode ser passado se você deseja dar ao usuário a opção para permitir que o local para ser acessado apenas quando o aplicativo estiver em primeiro plano.
    - `AllowsBackgroundLocationUpdates` – Esta é uma propriedade booleana, introduzida no iOS 9 que podem ser definidas para permitir que um aplicativo receber atualizações do local quando suspenso.

    > [!IMPORTANT]
> **Aviso**: iOS 8 (e superior) também requer uma entrada de **Info. plist** arquivo para mostrar ao usuário como parte da solicitação de autorização.

1. Adicione uma chave `NSLocationAlwaysUsageDescription` ou `NSLocationWhenInUseUsageDescription` com uma cadeia de caracteres que será exibida para o usuário no alerta que solicita acesso a dados local.

1. iOS 9 requer que, ao usar `AllowsBackgroundLocationUpdates` o **Info. plist** inclui a chave `UIBackgroundModes` com o valor `location`. Se você concluiu a etapa 2 deste passo a passo, isso deve já foi em seu arquivo Info. plist.


1. Dentro de `LocationManager` classe, crie um método chamado `StartLocationUpdates` com o código a seguir. Este código mostra como para começar a receber atualizações do local do `CLLocationManager`:

    ```csharp
        if (CLLocationManager.LocationServicesEnabled) {
          //set the desired accuracy, in meters
          LocMgr.DesiredAccuracy = 1;
          LocMgr.LocationsUpdated += (object sender, CLLocationsUpdatedEventArgs e) =>
          {
              // fire our custom Location Updated event
              LocationUpdated (this, new LocationUpdatedEventArgs (e.Locations [e.Locations.Length - 1]));
          };
          LocMgr.StartUpdatingLocation();
        }
        ```

    There are several important things happening in this method. First, we perform a check to see if the application has access to location data on the device. We verify this by calling `LocationServicesEnabled` on the `CLLocationManager`. This method will return **false** if the user has denied the application access to location information.

1. Next, tell the location manager how often to update. `CLLocationManager` provides many options for filtering and configuring location data, including the frequency of updates. In this example, set the `DesiredAccuracy` to update whenever the location changes by a meter. For more information on configuring location update frequency and other preferences, refer to the [CLLocationManager Class Reference](http://developer.apple.com/library/ios/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html) in the Apple documentation.

1. Finally, call `StartUpdatingLocation` on the `CLLocationManager` instance. This tells the location manager to get an initial fix on the current location, and to start sending updates

So far, the location manager has been created, configured with the kinds of data we want to receive, and has determined the initial location. Now the code needs to render the location data to the user interface. We can do this with a custom event that takes a `CLLocation` as an argument:

```csharp
// event for the location changing
public event EventHandler<LocationUpdatedEventArgs>LocationUpdated = delegate { };
```

A próxima etapa é se inscrever para atualizações de local do `CLLocationManager`e gerar personalizado `LocationUpdated` quando novos dados de local se torna disponíveis, passando o local como um argumento de evento. Para fazer isso, crie uma nova classe **LocationUpdateEventArgs.cs**. Esse código pode ser acessado no aplicativo principal e retorna o local do dispositivo quando o evento é gerado:

```csharp
public class LocationUpdatedEventArgs : EventArgs
{
    CLLocation location;

    public LocationUpdatedEventArgs(CLLocation location)
    {
       this.location = location;
    }

    public CLLocation Location
    {
       get { return location; }
    }
}
```

## <a name="user-interface"></a>Interface do Usuário

1. Use o designer de iOS para a tela exibirá informações sobre o local de compilação. Clique duas vezes no **Main.storyboard** arquivo para começar.

    No storyboard, arraste vários rótulos para a tela como espaços reservados para as informações de local. Neste exemplo, há rótulos para latitude, longitude, altitude, curso e velocidade.

    O layout deve ser semelhante ao seguinte:

    ![](location-walkthrough-images/image8.png "Um exemplo de layout de interface do usuário no Designer de iOS")

1. No painel de soluções, clique duas vezes o `ViewController.cs` de arquivo e editá-lo para criar uma nova instância da chamada e LocationManager `StartLocationUpdates`nele.
  Altere o código para a seguinte aparência:

        #region Computed Properties
        public static bool UserInterfaceIdiomIsPhone {
                    get { return UIDevice.CurrentDevice.UserInterfaceIdiom == UIUserInterfaceIdiom.Phone; }
                }

        public static LocationManager Manager { get; set;}
        #endregion

        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        // As soon as the app is done launching, begin generating location updates in the location manager
            Manager = new LocationManager();
            Manager.StartLocationUpdates();
        }

        #endregion

    Isso iniciará as atualizações de local na inicialização do aplicativo, embora nenhum dado será exibido.

1. Agora que as atualizações de local são recebidas, atualize a tela com as informações de local. O método a seguir obtém o local de nossa `LocationUpdated` eventos e os exibe na interface de usuário:

        #region Public Methods
        public void HandleLocationChanged (object sender, LocationUpdatedEventArgs e)
        {
            // Handle foreground updates
            CLLocation location = e.Location;

            LblAltitude.Text = location.Altitude + " meters";
            LblLongitude.Text = location.Coordinate.Longitude.ToString ();
            LblLatitude.Text = location.Coordinate.Latitude.ToString ();
            LblCourse.Text = location.Course.ToString ();
            LblSpeed.Text = location.Speed.ToString ();

            Console.WriteLine ("foreground updated");
        }

        #endregion


Ainda é necessário assinar o `LocationUpdated` evento no nosso AppDelegate e chame o novo método para atualizar a interface do usuário. Adicione o seguinte código em `ViewDidLoad,` logo após o `StartLocationUpdates` chamar:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // It is better to handle this with notifications, so that the UI updates
    // resume when the application re-enters the foreground!
    Manager.LocationUpdated += HandleLocationChanged;

}
```


Agora, quando o aplicativo é executado, ele deve ser semelhante este:

[![](location-walkthrough-images/image5.png "Executar um aplicativo de exemplo")](location-walkthrough-images/image5.png)

## <a name="handling-active-and-background-states"></a>Tratando os estados ativo e em segundo plano

1. O aplicativo está gerando a saída de atualizações local enquanto ele está em primeiro plano e ativo. Para demonstrar o que acontece quando o aplicativo entra no plano de fundo, substituir o `AppDelegate` alterações de estado de métodos que acompanham o aplicativo para que o aplicativo grava no console quando ela faz a transição entre o primeiro e o segundo plano:

        public override void DidEnterBackground (UIApplication application)
        {
          Console.WriteLine ("App entering background state.");
        }

        public override void WillEnterForeground (UIApplication application)
        {
          Console.WriteLine ("App will enter foreground");
        }

    Adicione o seguinte código no `LocationManager` imprimir continuamente o local atualizado dados para a saída do aplicativo, para verificar as informações de localização ainda estão disponíveis em segundo plano:

        public class LocationManager
        {
          public LocationManager ()
          {
            ...
            LocationUpdated += PrintLocation;
          }
          ...

          //This will keep going in the background and the foreground
          public void PrintLocation (object sender, LocationUpdatedEventArgs e) {
            CLLocation location = e.Location;
            Console.WriteLine ("Altitude: " + location.Altitude + " meters");
            Console.WriteLine ("Longitude: " + location.Coordinate.Longitude);
            Console.WriteLine ("Latitude: " + location.Coordinate.Latitude);
            Console.WriteLine ("Course: " + location.Course);
            Console.WriteLine ("Speed: " + location.Speed);
          }
        }

1. Há um problema restante com o código: tentativa de atualizar a interface do usuário quando o aplicativo é backgrounded causa iOS será terminará-lo. Quando o aplicativo entra em segundo plano, o código precisa cancelar a assinatura de atualizações de local e pare de atualizar a interface do usuário.

    iOS fornece notificações quando o aplicativo está prestes a transição para um aplicativo em diferente estados. Nesse caso, é possível assinar o `ObserveDidEnterBackground` notificação.

    O trecho de código a seguir mostra como usar uma notificação para informar o modo de exibição quando interromper as atualizações da interface do usuário. Isso vai entrar no `ViewDidLoad`:

        UIApplication.Notifications.ObserveDidEnterBackground ((sender, args) => {
          Manager.LocationUpdated -= HandleLocationChanged;
        });

    Quando o aplicativo é executado, a saída será parecida com isto:

    ![](location-walkthrough-images/image6.png "Exemplo de saída do local no console do")

1. O aplicativo imprime as atualizações de local para a tela ao operar em primeiro plano e continua a imprimir os dados na janela de saída do aplicativo durante a operação em segundo plano.

Apenas uma questão pendente permanece: tela inicia atualizações da interface do usuário quando o aplicativo é carregado pela primeira vez, mas não tem como saber quando o aplicativo novamente entrou em primeiro plano. Se o aplicativo backgrounded é levado de volta em primeiro plano, as atualizações da interface do usuário não retomado.

Para corrigir isso, aninhe uma chamada para iniciar as atualizações da interface do usuário dentro de outra notificação, que será acionado quando o aplicativo entra no estado ativo:

```csharp
UIApplication.Notifications.ObserveDidBecomeActive ((sender, args) => {
  Manager.LocationUpdated += HandleLocationChanged;
});
```

Agora a interface do usuário será iniciado quando o aplicativo é iniciado pela primeira vez, e continuar atualizando o aplicativo a qualquer momento volta a ficar em primeiro plano de atualização.

Neste passo a passo, criamos um aplicativo iOS com bom comportamento, com reconhecimento de plano de fundo que imprime os dados de local para a tela e a janela de saída do aplicativo.


## <a name="related-links"></a>Links relacionados

- [Local (parte 4) (amostra)](https://developer.xamarin.com/samples/monotouch/Location/)
- [Referência de Framework do local principal](https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CoreLocation_Framework/_index.html)
