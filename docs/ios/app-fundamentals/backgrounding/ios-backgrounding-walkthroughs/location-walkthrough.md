---
title: Passo a passo – local do plano de fundo no xamarin. IOS
description: Este documento fornece um passo a passo de como usar informações de localização em um aplicativo xamarin. IOS backgrounded. Ele descreve a instalação necessários, a interface do usuário e estados do aplicativo.
ms.prod: xamarin
ms.assetid: F8EEA0FD-5614-47FE-ADAC-80A5BCA6EB5F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: fa8a48e165764a449af4bc5414d2e66aecea8269
ms.sourcegitcommit: 495680e74c72e7c570e68cde95d3d3643b1fcc8a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58870138"
---
# <a name="walkthrough---background-location-in-xamarinios"></a>Passo a passo – local do plano de fundo no xamarin. IOS

Neste exemplo, vamos criar um iOS aplicativo local que imprime informações sobre nosso local atual: latitude, longitude e outros parâmetros para a tela. Este aplicativo será demonstram como executar corretamente as atualizações de local enquanto o aplicativo está ativo ou Backgrounded.

Este passo a passo explica alguma chave backgrounding conceitos, incluindo o registro de um aplicativo como um aplicativo necessário em segundo plano, a suspensão de atualizações da interface do usuário quando o aplicativo é colocado em segundo plano e trabalhar com o `WillEnterBackground` e `WillEnterForeground` `AppDelegate` métodos .

## <a name="application-set-up"></a>Configurar do aplicativo


1. Primeiro, crie uma nova **iOS > aplicativo > aplicativo de exibição única (C#)**. Chamá-lo _local_ e certifique-se de que o iPad e iPhone foi selecionadas.

1. Um aplicativo local se qualifica como um aplicativo necessário em segundo plano no iOS. Registrar o aplicativo como um aplicativo local por meio da edição do **Info. plist** arquivo para o projeto.

    No Gerenciador de soluções, clique duas vezes no **Info. plist** arquivo para abri-lo e role até a parte inferior da lista. Colocar uma marca de seleção por ambos os **habilitar modos de plano de fundo** e o **atualizações de local** caixas de seleção.

    No Visual Studio para Mac, ele terá aparência semelhante a esta:

    [![](location-walkthrough-images/image7.png "Colocar uma marca de seleção Habilitar modos de plano de fundo e as caixas de seleção de atualizações de local")](location-walkthrough-images/image7.png#lightbox)

    No Visual Studio, **Info. plist** precisa ser atualizado manualmente adicionando o par chave/valor a seguir:

    ```xml
    <key>UIBackgroundModes</key>
    <array>
      <string>location</string>
    </array>
    ```

1. Agora que o aplicativo está registrado, ele pode obter dados de localização do dispositivo. No iOS, o `CLLocationManager` classe é usada para acessar informações de local e pode gerar eventos que fornecem atualizações de local.

1. No código, crie uma nova classe chamada `LocationManager` que fornece um único lugar para várias telas e código para assinar as atualizações de local. No `LocationManager` classe, para tornar uma instância das `CLLocationManager` chamado `LocMgr`:

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

    O código acima define um número de propriedades e permissões sobre o [CLLocationManager](xref:CoreLocation.CLLocationManager) classe:

    - `PausesLocationUpdatesAutomatically` – Esse é um valor booleano que pode ser definido, dependendo se o sistema tem permissão para pausar as atualizações de local. Em alguns dispositivos padrão é `true`, que pode fazer com que o dispositivo parar de receber atualizações do local após cerca de 15 minutos de plano de fundo.
    - `RequestAlwaysAuthorization` -Você deve passar esse método para atribuir o usuário do aplicativo a opção para permitir que o local a ser acessado em segundo plano. `RequestWhenInUseAuthorization` também pode ser passado se você quiser dar ao usuário a opção para permitir que o local a ser acessado somente quando o aplicativo estiver em primeiro plano.
    - `AllowsBackgroundLocationUpdates` – Esta é uma propriedade booleana, introduzida no iOS 9 que podem ser definidas para permitir que um aplicativo receber atualizações de localização quando suspenso.

    > [!IMPORTANT]
    > iOS 8 (e superior) também requer uma entrada na **Info. plist** arquivo para mostrar ao usuário como parte da solicitação de autorização.

1. Adicionar uma chave `NSLocationAlwaysUsageDescription` ou `NSLocationWhenInUseUsageDescription` com uma cadeia de caracteres que será exibida para o usuário no alerta que solicita acesso a dados local.

1. iOS 9 requer que, ao usar `AllowsBackgroundLocationUpdates` as **Info. plist** inclui a chave `UIBackgroundModes` com o valor `location`. Se você tiver concluído a etapa 2 deste passo a passo, isso deve já esteve em seu arquivo Info. plist.


1. Dentro de `LocationManager` classe, crie um método chamado `StartLocationUpdates` com o código a seguir. Este código mostra como começar a receber atualizações de local do `CLLocationManager`:

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

    Há várias coisas importantes acontecendo neste método. Primeiro, podemos executar uma verificação para ver se o aplicativo tem acesso a dados locais no dispositivo. Podemos verificar isso chamando `LocationServicesEnabled` sobre o `CLLocationManager`. Esse método retornará **falsos** se o usuário negou o acesso de aplicativo para informações de localização.

1. Em seguida, informar ao Gerenciador de localização a frequência com que a atualização. `CLLocationManager` fornece muitas opções de filtragem e configurando os dados de local, incluindo a frequência de atualizações. Neste exemplo, defina o `DesiredAccuracy` atualizar sempre que o local for alterado por um medidor. Para obter mais informações sobre como configurar a frequência de atualização do local e outras preferências, consulte o [referência de classe CLLocationManager](https://developer.apple.com/library/ios/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html) na documentação da Apple.

1. Por fim, chame `StartUpdatingLocation` sobre o `CLLocationManager` instância. Isso informa ao Gerenciador de localizações para obter uma correção inicial sobre o local atual e iniciar o envio de atualizações

Até agora, o Gerenciador local tiver sido criado, configurado com os tipos de dados que desejamos receber, e determina o local inicial. Agora, o código precisa processar os dados de localização para a interface do usuário. Podemos fazer isso com um evento personalizado que usa um `CLLocation` como um argumento:

```csharp
// event for the location changing
public event EventHandler<LocationUpdatedEventArgs>LocationUpdated = delegate { };
```

A próxima etapa é assinar atualizações de local do `CLLocationManager`e geram personalizado `LocationUpdated` quando novos dados de localização se torna disponíveis, passando o local como um argumento de evento. Para fazer isso, crie uma nova classe **LocationUpdateEventArgs.cs**. Esse código é acessível dentro do aplicativo principal e retorna o local do dispositivo quando o evento é gerado:

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

1. Use o designer do iOS para criar a tela que exibirá informações de localização. Clique duas vezes no **Main. Storyboard** arquivo para começar.

    No storyboard, arraste vários rótulos para a tela para atuar como espaços reservados para as informações de localização. Neste exemplo, há rótulos para latitude, longitude, altitude, curso e velocidade.

    O layout deve ser semelhante ao seguinte:

    ![](location-walkthrough-images/image8.png "Um exemplo de layout da interface do usuário no iOS Designer")

1. No painel de soluções, clique duas vezes o `ViewController.cs` do arquivo e editá-lo para criar uma nova instância da chamada e LocationManager `StartLocationUpdates`nele.
  Altere o código para a seguinte aparência:

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

    Isso iniciará as atualizações de local na inicialização do aplicativo, embora nenhum dado será exibido.

1. Agora que as atualizações de local são recebidas, atualize a tela com as informações de localização. O método a seguir obtém o local do nosso `LocationUpdated` eventos e os exibe na interface do usuário:

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

Ainda assim será preciso assinar o `LocationUpdated` evento no nosso AppDelegate e chame o novo método para atualizar a interface do usuário. Adicione o seguinte código no `ViewDidLoad,` logo após o `StartLocationUpdates` chamar:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // It is better to handle this with notifications, so that the UI updates
    // resume when the application re-enters the foreground!
    Manager.LocationUpdated += HandleLocationChanged;

}
```


Agora, quando o aplicativo é executado, ele deve ser algo parecido com isto:

[![](location-walkthrough-images/image5.png "Um execução do aplicativo de exemplo")](location-walkthrough-images/image5.png#lightbox)

## <a name="handling-active-and-background-states"></a>Tratamento de estados ativo e em segundo plano

1. O aplicativo é saída de atualizações local enquanto ele está em primeiro plano e Active Directory. Para demonstrar o que acontece quando o aplicativo entra em segundo plano, substituir o `AppDelegate` alterações de estado de métodos que acompanham o aplicativo para que o aplicativo grava no console quando ele faz a transição entre o primeiro plano e o plano de fundo:

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

    Adicione o seguinte código no `LocationManager` continuamente imprimir local atualizado dados para a saída do aplicativo, para verificar as informações de localização ainda estão disponíveis no plano de fundo:

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

1. Há um problema restante com o código: tentativa de atualizar a interface do usuário quando o aplicativo é colocado em segundo plano será causa iOS encerrará-lo. Quando o aplicativo entra em segundo plano, o código precisa cancelar a assinatura de atualizações de local e param de atualizar a interface do usuário.

    iOS nos fornece notificações quando o aplicativo está prestes a fazer a transição para um aplicativo em diferente estados. Nesse caso, podemos pode assinar o `ObserveDidEnterBackground` notificação.

    O trecho de código a seguir mostra como usar uma notificação para informar o modo de exibição quando interromper as atualizações da interface do usuário. Isso vai entrar no `ViewDidLoad`:

    ```csharp
    UIApplication.Notifications.ObserveDidEnterBackground ((sender, args) => {
        Manager.LocationUpdated -= HandleLocationChanged;
    });
    ```

    Quando o aplicativo é executado, a saída será algo parecido com isso:

    ![](location-walkthrough-images/image6.png "Exemplo da saída local no console do")

1. O aplicativo imprime as atualizações de local na tela ao operar em primeiro plano e continua a imprimir os dados na janela de saída do aplicativo enquanto estiver operando em segundo plano.

Apenas um problema pendente permanece: tela inicia atualizações da interface do usuário quando o aplicativo é carregado pela primeira vez, mas ele não tem nenhuma maneira de saber quando o aplicativo entrou novamente em primeiro plano. Se o aplicativo backgrounded é trazido de volta para o primeiro plano, as atualizações da interface do usuário não serão retomada.

Para corrigir isso, aninhe uma chamada para iniciar as atualizações da interface do usuário dentro de outra notificação, que será disparado quando o aplicativo entra no estado ativo:

```csharp
UIApplication.Notifications.ObserveDidBecomeActive ((sender, args) => {
  Manager.LocationUpdated += HandleLocationChanged;
});
```

Agora a interface do usuário será iniciada a atualização quando o aplicativo é iniciado pela primeira vez, e continuar atualizando sempre que o aplicativo volta a entrar em primeiro plano.

Neste passo a passo, criamos um aplicativo iOS bem comportado, reconhecimento de plano de fundo que imprime os dados de local para a tela e janela de saída do aplicativo.


## <a name="related-links"></a>Links relacionados

- [Local (parte 4) (amostra)](https://developer.xamarin.com/samples/monotouch/Location/)
- [Referência de Framework Core local](https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CoreLocation_Framework/_index.html)
