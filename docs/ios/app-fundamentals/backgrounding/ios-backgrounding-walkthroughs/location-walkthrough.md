---
title: Walkthrough-local do plano de fundo no Xamarin. iOS
description: Este documento fornece uma explicação sobre como usar informações de local em um aplicativo Xamarin. iOS em segundo plano. Ele descreve a instalação necessária, a interface do usuário e os Estados do aplicativo.
ms.prod: xamarin
ms.assetid: F8EEA0FD-5614-47FE-ADAC-80A5BCA6EB5F
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: b357a6e1531c8f587c8fc0f86e2b56d6fecc2923
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488316"
---
# <a name="walkthrough---background-location-in-xamarinios"></a>Walkthrough-local do plano de fundo no Xamarin. iOS

Neste exemplo, vamos criar um aplicativo de local do iOS que imprime informações sobre nosso local atual: latitude, longitude e outros parâmetros na tela. Este aplicativo demonstrará como executar corretamente as atualizações de local enquanto o aplicativo estiver ativo ou em segundo plano.

Este tutorial explica alguns conceitos importantes de plano de fundo, incluindo o registro de um aplicativo como um aplicativo necessário em segundo plano, a suspensão de atualizações da interface do usuário quando o aplicativo é em segundo plano e o trabalho com os métodos `WillEnterBackground` e `WillEnterForeground` `AppDelegate`.

## <a name="application-set-up"></a>Configuração do aplicativo

1. Primeiro, crie um novo **aplicativo iOS > > aplicativo de exibição únicaC#()** . Ligue para o _local_ e verifique se o iPad e o iPhone foram selecionados.

1. Um aplicativo de localização é qualificado como um aplicativo necessário em segundo plano no iOS. Registre o aplicativo como um aplicativo de local editando o arquivo **info. plist** para o projeto.

    Em Gerenciador de Soluções, clique duas vezes no arquivo **info. plist** para abri-lo e role até a parte inferior da lista. Marque as caixas de seleção **habilitar modos de segundo plano** e **atualizações de local** .

    No Visual Studio para Mac, ele será semelhante a este:

    [![](location-walkthrough-images/image7.png "Place a check by both the Enable Background Modes and the Location Updates checkboxes")](location-walkthrough-images/image7.png#lightbox)

    No Visual Studio, o **info. plist** precisa ser atualizado manualmente Adicionando o seguinte par de chave/valor:

    ```xml
    <key>UIBackgroundModes</key>
    <array>
      <string>location</string>
    </array>
    ```

1. Agora que o aplicativo está registrado, ele pode obter dados de localização do dispositivo. No iOS, a classe `CLLocationManager` é usada para acessar informações de local e pode gerar eventos que fornecem atualizações de local.

1. No código, crie uma nova classe chamada `LocationManager` que fornece um único local para várias telas e código para assinar atualizações de local. Na classe `LocationManager`, crie uma instância do `CLLocationManager` chamada `LocMgr`:

    ```csharp
    public class LocationManager
    {
        protected CLLocationManager locMgr;

        public LocationManager () {
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

        public CLLocationManager LocMgr {
            get { return this.locMgr; }
        }
    }
    ```

    O código acima define um número de propriedades e permissões na classe [CLLocationManager](xref:CoreLocation.CLLocationManager) :

    - `PausesLocationUpdatesAutomatically` – é um booliano que pode ser definido dependendo se o sistema tem permissão para pausar atualizações de local. Em algum dispositivo, ele usa como padrão `true`, o que pode fazer com que o dispositivo pare de obter atualizações de local de segundo plano após cerca de 15 minutos.
    - `RequestAlwaysAuthorization`-você deve passar esse método para dar ao usuário do aplicativo a opção de permitir que o local seja acessado em segundo plano. `RequestWhenInUseAuthorization` também pode ser passado se você quiser conceder ao usuário a opção de permitir que o local seja acessado somente quando o aplicativo estiver em primeiro plano.
    - `AllowsBackgroundLocationUpdates` – é uma propriedade booliana, introduzida no iOS 9, que pode ser definida para permitir que um aplicativo Receba atualizações de local quando suspenso.

    > [!IMPORTANT]
    > o iOS 8 (e superior) também requer uma entrada no arquivo **info. plist** para mostrar o usuário como parte da solicitação de autorização.

1. Adicione as chaves **info. plist** para os tipos de permissão que o aplicativo requer – `NSLocationAlwaysUsageDescription`, `NSLocationWhenInUseUsageDescription`e/ou `NSLocationAlwaysAndWhenInUseUsageDescription` – com uma cadeia de caracteres que será exibida para o usuário no alerta que solicita acesso a dados de local.

1. o iOS 9 requer que, ao usar `AllowsBackgroundLocationUpdates` o **info. plist** inclua o `UIBackgroundModes` de chave com o valor `location`. Se você concluiu a etapa 2 deste passo a passos, ele já deve estar em seu arquivo info. plist.

1. Dentro da classe `LocationManager`, crie um método chamado `StartLocationUpdates` com o código a seguir. Esse código mostra como começar a receber atualizações de local do `CLLocationManager`:

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

    Há várias coisas importantes que ocorrem neste método. Primeiro, executamos uma verificação para ver se o aplicativo tem acesso aos dados de localização no dispositivo. Verificamos isso chamando `LocationServicesEnabled` no `CLLocationManager`. Esse método retornará **false** se o usuário tiver negado o acesso do aplicativo às informações de localização.

1. Em seguida, diga ao Gerenciador de locais a frequência de atualização. `CLLocationManager` fornece muitas opções para filtrar e configurar dados de localização, incluindo a frequência de atualizações. Neste exemplo, defina o `DesiredAccuracy` a ser atualizado sempre que o local for alterado por um medidor. Para obter mais informações sobre como configurar a frequência de atualização de local e outras preferências, consulte a [referência de classe CLLocationManager](https://developer.apple.com/library/ios/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html) na documentação da Apple.

1. Por fim, chame `StartUpdatingLocation` na instância de `CLLocationManager`. Isso informa o Gerenciador de local para obter uma correção inicial no local atual e iniciar o envio de atualizações

Até agora, o Gerenciador de local foi criado, configurado com os tipos de dados que queremos receber e determinou o local inicial. Agora, o código precisa renderizar os dados de localização para a interface do usuário. Podemos fazer isso com um evento personalizado que usa um `CLLocation` como um argumento:

```csharp
// event for the location changing
public event EventHandler<LocationUpdatedEventArgs>LocationUpdated = delegate { };
```

A próxima etapa é assinar as atualizações de local do `CLLocationManager`e gerar o evento de `LocationUpdated` personalizado quando novos dados de localização ficarem disponíveis, passando o local como um argumento. Para fazer isso, crie uma nova classe **LocationUpdateEventArgs.cs**. Esse código pode ser acessado dentro do aplicativo principal e retorna o local do dispositivo quando o evento é gerado:

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

1. Use o designer do iOS para criar a tela que exibirá informações de localização. Clique duas vezes no arquivo **Main. Storyboard** para começar.

    No storyboard, arraste vários rótulos para a tela para atuar como espaços reservados para as informações de local. Neste exemplo, há rótulos para latitude, longitude, altitude, curso e velocidade.

    O layout deve ser semelhante ao seguinte:

    ![](location-walkthrough-images/image8.png "An example UI layout in the iOS Designer")

1. Na Painel de Soluções, clique duas vezes no arquivo de `ViewController.cs` e edite-o para criar uma nova instância do Localmanager e chame `StartLocationUpdates`nele.
  Altere o código para que fique semelhante ao seguinte:

    ```csharp
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
    ```

    Isso iniciará as atualizações de local na inicialização do aplicativo, embora nenhum dado seja exibido.

1. Agora que as atualizações de local foram recebidas, atualize a tela com as informações de localização. O método a seguir obtém o local do nosso evento de `LocationUpdated` e o mostra na interface do usuário:

    ```csharp
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
    ```

Ainda precisamos assinar o evento `LocationUpdated` em nosso AppDelegate e chamar o novo método para atualizar a interface do usuário. Adicione o seguinte código à `ViewDidLoad,` logo após a chamada `StartLocationUpdates`:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // It is better to handle this with notifications, so that the UI updates
    // resume when the application re-enters the foreground!
    Manager.LocationUpdated += HandleLocationChanged;

}
```

Agora, quando o aplicativo for executado, ele deverá ser semelhante a este:

[![](location-walkthrough-images/image5.png "An example app run")](location-walkthrough-images/image5.png#lightbox)

## <a name="handling-active-and-background-states"></a>Manipulando Estados ativos e em segundo plano

1. O aplicativo está gerando atualizações de local enquanto está em primeiro plano e ativo. Para demonstrar o que acontece quando o aplicativo entra no plano de fundo, substitua os métodos `AppDelegate` que controlam as alterações de estado do aplicativo para que o aplicativo grave no console quando ele faz a transição entre o primeiro plano e o plano de fundo:

    ```csharp
    public override void DidEnterBackground (UIApplication application)
    {
        Console.WriteLine ("App entering background state.");
    }

    public override void WillEnterForeground (UIApplication application)
    {
        Console.WriteLine ("App will enter foreground");
    }
    ```

    Adicione o seguinte código no `LocationManager` para imprimir continuamente os dados de localização atualizados na saída do aplicativo, para verificar se as informações de local ainda estão disponíveis em segundo plano:

    ```csharp
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
    ```

1. Há um problema restante com o código: a tentativa de atualizar a interface do usuário quando o aplicativo está em segundo plano fará com que o iOS o encerre. Quando o aplicativo entra em segundo plano, o código precisa cancelar a assinatura de atualizações de local e parar de atualizar a interface do usuário.

    o iOS nos fornece notificações quando o aplicativo está prestes a fazer a transição para um estado de aplicativo diferente. Nesse caso, podemos assinar a notificação de `ObserveDidEnterBackground`.

    O trecho de código a seguir mostra como usar uma notificação para permitir que a exibição saiba quando interromper as atualizações da interface do usuário. Isso entrará em `ViewDidLoad`:

    ```csharp
    UIApplication.Notifications.ObserveDidEnterBackground ((sender, args) => {
        Manager.LocationUpdated -= HandleLocationChanged;
    });
    ```

    Quando o aplicativo estiver em execução, a saída terá uma aparência semelhante a esta:

    ![](location-walkthrough-images/image6.png "Example of the location output in the console")

1. O aplicativo imprime as atualizações de local na tela ao operar em primeiro plano e continua a imprimir dados na janela de saída do aplicativo enquanto estiver operando em segundo plano.

Somente um problema pendente permanece: a tela inicia as atualizações da interface do usuário quando o aplicativo é carregado pela primeira vez, mas não tem como saber quando o aplicativo reinseriu o primeiro plano. Se o aplicativo em segundo plano for devolvido ao primeiro plano, as atualizações da interface do usuário não serão retomadas.

Para corrigir isso, aninhe uma chamada para iniciar atualizações da interface do usuário dentro de outra notificação, que será acionada quando o aplicativo entrar no estado ativo:

```csharp
UIApplication.Notifications.ObserveDidBecomeActive ((sender, args) => {
  Manager.LocationUpdated += HandleLocationChanged;
});
```

Agora, a interface do usuário começará a ser atualizada quando o aplicativo for iniciado pela primeira vez e retomará a atualização sempre que o aplicativo voltar ao primeiro plano.

Neste tutorial, criamos um aplicativo iOS com reconhecimento de plano de fundo bem comparado que imprime dados de localização na janela de saída do aplicativo e na tela.

## <a name="related-links"></a>Links Relacionados

- [Local (parte 4) (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/location)
- [Referência da estrutura de localização principal](https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CoreLocation_Framework/_index.html)
