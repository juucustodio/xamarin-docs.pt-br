---
title: HomeKit no Xamarin. iOS
description: HomeKit é a estrutura da Apple para controlar dispositivos de automação inicial. Este artigo apresenta o HomeKit e aborda a configuração de acessórios de teste no simulador de acessório do HomeKit e a gravação de um aplicativo Xamarin. iOS simples para interagir com esses acessórios.
ms.prod: xamarin
ms.assetid: 90C0C553-916B-46B1-AD52-1E7332792283
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/22/2017
ms.openlocfilehash: a004ea539bab264f6a1418826be451ffb7d43d47
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70753041"
---
# <a name="homekit-in-xamarinios"></a>HomeKit no Xamarin. iOS

_HomeKit é a estrutura da Apple para controlar dispositivos de automação inicial. Este artigo apresenta o HomeKit e aborda a configuração de acessórios de teste no simulador de acessório do HomeKit e a gravação de um aplicativo Xamarin. iOS simples para interagir com esses acessórios._

[![](homekit-images/accessory01.png "Um aplicativo de exemplo HomeKit habilitado")](homekit-images/accessory01.png#lightbox)

A Apple introduziu o HomeKit no iOS 8 como uma maneira de integrar perfeitamente vários dispositivos de automação doméstica de uma variedade de fornecedores em uma única unidade coerente. Ao promover um protocolo comum para descobrir, configurar e controlar dispositivos de automação inicial, o HomeKit permite que dispositivos de fornecedores não relacionados trabalhem juntos, tudo sem os fornecedores individuais que têm de coordenar esforços.

Com o HomeKit, você pode criar um aplicativo Xamarin. iOS que controla qualquer dispositivo habilitado para HomeKit sem usar APIs ou aplicativos fornecidos pelo fornecedor. Usando o HomeKit, você pode fazer o seguinte:

- Descubra novos dispositivos de automação inicial habilitados para HomeKit e adicione-os a um banco de dados que será mantido em todos os dispositivos iOS do usuário.
- Configurar, configurar, exibir e controlar qualquer dispositivo no banco de _dados de configuração_do homekit Home.
- Comunique-se com qualquer dispositivo HomeKit pré-configurado e faça o comando para executar ações individuais ou trabalhar em conjunto, como ativar todas as luzes na cozinha.

Além de servir dispositivos no banco de dados de configuração inicial para aplicativos habilitados para HomeKit, o HomeKit fornece acesso a comandos de voz Siri. Devido a uma configuração de HomeKit configurada apropriadamente, o usuário pode emitir comandos de voz, como "Siri, ligar as luzes na sala de vida".

<a name="Home-Configuration-Database" />

## <a name="the-home-configuration-database"></a>O banco de dados de configuração inicial

HomeKit organiza todos os dispositivos de automação em um determinado local em uma coleção inicial. Essa coleção fornece uma maneira para o usuário agrupar seus dispositivos de automação inicial em unidades logicamente organizadas com rótulos significativos e legíveis.

A coleção inicial é armazenada em um banco de dados de configuração inicial que será submetido a backup e sincronizado automaticamente em todos os dispositivos iOS do usuário. O HomeKit fornece as seguintes classes para trabalhar com o banco de dados de configuração inicial:

- `HMHome`-Esse é o contêiner de nível superior que contém todas as informações e configurações de todos os dispositivos de automação inicial em um único local físico (por exemplo, uma residência de família única). O usuário pode ter mais de uma residência, como sua casa principal e uma casa de férias. Ou podem ter diferentes "casas" na mesma propriedade, como a casa principal e uma casa convidada pela garagem. De qualquer forma, pelo menos `HMHome` um objeto _deve_ ser configurado e armazenado antes que qualquer outra informação de homekit possa ser inserida.
- `HMRoom`-Embora opcional, um `HMRoom` permite que o usuário defina salas específicas dentro de uma página inicial`HMHome`(), como: Cozinha, banheiro, garagem ou sala de vida. O usuário pode agrupar todos os dispositivos de automação inicial em um local específico em sua casa em um `HMRoom` e agir sobre eles como uma unidade. Por exemplo, pedir ao Siri para desligar as luzes de garagem.
- `HMAccessory`-Representa um dispositivo de automação individual e físico HomeKit habilitado que foi instalado na residência do usuário (como um termostato inteligente). Cada `HMAccessory` um é atribuído a `HMRoom`um. Se o usuário não tiver configurado nenhuma sala, o HomeKit atribuirá acessórios a uma sala padrão especial.
- `HMService`-Representa um serviço fornecido por um determinado `HMAccessory`, como o estado ligado/desligado de uma luz ou sua cor (se houver suporte para a alteração de cores). Cada `HMAccessory` um pode ter mais de um serviço, como uma abertura de porta de garagem que também inclui uma luz. Além disso, um `HMAccessory` determinado pode ter serviços, como atualização de firmware, que estão fora do controle de usuário.
- `HMZone`– Permite que o usuário agrupe uma coleção de `HMRoom` objetos em zonas lógicas, como upescadas, cybercafé ou porão. Embora opcional, isso permite interações como pedir ao Siri para desativar toda a luz cybercafé.

<a name="Provisioning-a-HomeKit-App" />

## <a name="provisioning-a-homekit-app"></a>Provisionando um aplicativo HomeKit

Devido aos requisitos de segurança impostos pelo HomeKit, um aplicativo Xamarin. iOS que usa a estrutura HomeKit deve ser configurado corretamente no portal do desenvolvedor da Apple e no arquivo de projeto Xamarin. iOS.

Faça o seguinte:

1. Faça logon no [portal do desenvolvedor da Apple](https://developer.apple.com).
2. Clique em **certificados, identificadores & perfis**.
3. Se você ainda não tiver feito isso, clique em **identificadores** e crie uma ID para seu aplicativo (por `com.company.appname`exemplo,), caso contrário, edite sua ID existente.
4. Verifique se o serviço **homekit** foi verificado para a ID fornecida: 

    [![](homekit-images/provision01.png "Habilitar o serviço HomeKit para a ID fornecida")](homekit-images/provision01.png#lightbox)
5. Salve as alterações.
6. Clique em**desenvolvimento** de **perfis** > de provisionamento e crie um novo perfil de provisionamento de desenvolvimento para seu aplicativo: 

    [![](homekit-images/provision02.png "Criar um novo perfil de provisionamento de desenvolvimento para o aplicativo")](homekit-images/provision02.png#lightbox)
7. Baixe e instale o novo perfil de provisionamento ou use o Xcode para baixar e instalar o perfil.
8. Edite suas opções de projeto do Xamarin. iOS e verifique se você está usando o perfil de provisionamento que você acabou de criar: 

    [![](homekit-images/provision03.png "Selecionar perfil de provisionamento recém-criado")](homekit-images/provision03.png#lightbox)
9. Em seguida, edite seu arquivo **info. plist** e verifique se você está usando a ID do aplicativo que foi usada para criar o perfil de provisionamento: 

    [![](homekit-images/provision04.png "Definir a ID do aplicativo")](homekit-images/provision04.png#lightbox)
10. Por fim, edite seu arquivo **. plist de direitos** e verifique se o direito de **homekit** foi selecionado: 

    [![](homekit-images/provision05.png "Habilitar o direito de HomeKit")](homekit-images/provision05.png#lightbox)
11. Salve as alterações em todos os arquivos.

Com essas configurações em vigor, o aplicativo agora está pronto para acessar as APIs do HomeKit Framework. Para obter informações detalhadas sobre o provisionamento, consulte nosso [provisionamento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md) e [Provisionando seus guias de aplicativo](~/ios/get-started/installation/device-provisioning/index.md) .

> [!IMPORTANT]
> O teste de um aplicativo habilitado para HomeKit requer um dispositivo iOS real que tenha sido provisionado corretamente para desenvolvimento. HomeKit não pode ser testado a partir do simulador de iOS.

## <a name="the-homekit-accessory-simulator"></a>O simulador de acessório do HomeKit

Para fornecer uma maneira de testar todos os dispositivos e serviços de automação inicial, sem precisar ter um dispositivo físico, a Apple criou o _simulador de acessório homekit_. Usando esse simulador, você pode instalar e configurar dispositivos HomeKit virtuais.

### <a name="installing-the-simulator"></a>Instalando o simulador

A Apple fornece o simulador de acessório HomeKit como um download separado do Xcode, portanto, será necessário instalá-lo antes de continuar.

Faça o seguinte:

1. Em um navegador da Web, visite [downloads para desenvolvedores da Apple](https://developer.apple.com/download/more/?name=for%20Xcode)
2. Baixe as **ferramentas adicionais do Xcode xxx** (em que xxx é a versão do Xcode que você instalou): 

    [![](homekit-images/simulator01.png "Baixe as ferramentas adicionais para o Xcode")](homekit-images/simulator01.png#lightbox)
3. Abra a imagem do disco e instale as ferramentas no diretório de **aplicativos** .

Com o HomeKit acessório simulado instalado, os acessórios virtuais podem ser criados para teste.

### <a name="creating-virtual-accessories"></a>Criando acessórios virtuais

Para iniciar o simulador de acessório do HomeKit e criar alguns acessórios virtuais, faça o seguinte:

1. Na pasta aplicativos, inicie o simulador de acessório do HomeKit: 

    [![](homekit-images/simulator02.png "O simulador de acessório do HomeKit")](homekit-images/simulator02.png#lightbox)
2. Clique no **+** botão e selecione **novo acessório...** : 

    [![](homekit-images/simulator03.png "Adicionar um novo acessório")](homekit-images/simulator03.png#lightbox)
3. Preencha as informações sobre o novo acessório e clique no botão **concluir** : 

    [![](homekit-images/simulator04.png "Preencha as informações sobre o novo acessório")](homekit-images/simulator04.png#lightbox)
4. Clique em **Adicionar serviço..** e selecione um tipo de serviço na lista suspensa: 

    [![](homekit-images/simulator05.png "Selecione um tipo de serviço na lista suspensa")](homekit-images/simulator05.png#lightbox)
5. Forneça um **nome** para o serviço e clique no botão **concluir** : 

    [![](homekit-images/simulator06.png "Insira um nome para o serviço")](homekit-images/simulator06.png#lightbox)
6. Você pode fornecer características opcionais para um serviço clicando no botão **Adicionar característica** e definindo as configurações necessárias: 

    [![](homekit-images/simulator07.png "Definindo as configurações necessárias")](homekit-images/simulator07.png#lightbox)
7. Repita as etapas acima para criar um de cada tipo de dispositivo de automação inicial virtual ao qual o HomeKit dá suporte.

Com alguns exemplos de acessórios virtuais HomeKit criados e configurados, agora você pode consumir e controlar esses dispositivos do seu aplicativo Xamarin. iOS.

## <a name="configuring-the-infoplist-file"></a>Configurando o arquivo info. plist

Novo para IOS 10 (e superior), o desenvolvedor precisará adicionar a `NSHomeKitUsageDescription` chave ao arquivo do `Info.plist` aplicativo e fornecer uma cadeia de caracteres declarando por que o aplicativo deseja acessar o banco de dados homekit do usuário. Essa cadeia de caracteres será apresentada ao usuário na primeira vez em que executar o aplicativo:

[![](homekit-images/info01.png "A caixa de diálogo de permissão HomeKit")](homekit-images/info01.png#lightbox)

Para definir essa chave, faça o seguinte:

1. Clique `Info.plist` duas vezes no arquivo no **Gerenciador de soluções** para abri-lo para edição.
2. Na parte inferior da tela, alterne para o modo de exibição de **código-fonte** .
3. Adicione uma nova **entrada** à lista.
4. Na lista suspensa, selecione **privacidade – descrição do uso do homekit**: 

    [![](homekit-images/info02.png "Selecione privacidade – descrição do uso do HomeKit")](homekit-images/info02.png#lightbox)
5. Insira uma descrição para o motivo pelo qual o aplicativo deseja acessar o banco de dados HomeKit do usuário: 

    [![](homekit-images/info03.png "Insira uma descrição")](homekit-images/info03.png#lightbox)
6. Salve as alterações no arquivo.

> [!IMPORTANT]
> A falha ao definir `NSHomeKitUsageDescription` a chave `Info.plist` no arquivo fará com que o aplicativo _falhe silenciosamente_ (sendo fechado pelo sistema em tempo de execução) sem erro quando executado no Ios 10 (ou superior).

## <a name="connecting-to-homekit"></a>Conectando ao HomeKit

Para se comunicar com o homekit, seu aplicativo Xamarin. Ios precisa primeiro instanciar uma instância `HMHomeManager` da classe. O gerente inicial é o ponto de entrada central no HomeKit e é responsável por fornecer uma lista de residências disponíveis, atualizar e manter essa lista e retornar a _página inicial principal_do usuário.

O `HMHome` objeto contém todas as informações sobre uma página inicial, incluindo quaisquer salas, grupos ou zonas que possam conter, juntamente com os acessórios de automação inicial que foram instalados. Antes que qualquer operação possa ser executada no homekit, pelo menos `HMHome` um deve ser criado e atribuído como a página inicial principal.

Seu aplicativo é responsável por verificar se existe uma página inicial primária e criar e atribuir uma se não tiver.

### <a name="adding-a-home-manager"></a>Adicionando um gerente doméstico

Para adicionar o reconhecimento de HomeKit a um aplicativo Xamarin. iOS, edite o arquivo **AppDelegate.cs** para editá-lo e torná-lo semelhante ao seguinte:

```csharp
using HomeKit;
...

public HMHomeManager HomeManager { get; set; }
...

public override void FinishedLaunching (UIApplication application)
{
    // Attach to the Home Manager
    HomeManager = new HMHomeManager ();
    Console.WriteLine ("{0} Home(s) defined in the Home Manager", HomeManager.Homes.Count());

    // Wire-up Home Manager Events
    HomeManager.DidAddHome += (sender, e) => {
        Console.WriteLine("Manager Added Home: {0}",e.Home);
    };

    HomeManager.DidRemoveHome += (sender, e) => {
        Console.WriteLine("Manager Removed Home: {0}",e.Home);
    };
    HomeManager.DidUpdateHomes += (sender, e) => {
        Console.WriteLine("Manager Updated Homes");
    };
    HomeManager.DidUpdatePrimaryHome += (sender, e) => {
        Console.WriteLine("Manager Updated Primary Home");
    };
}
```

Quando o aplicativo for executado pela primeira vez, o usuário será perguntado se deseja permitir que ele acesse suas informações de HomeKit:

[![](homekit-images/home01.png "O usuário será perguntado se deseja permitir que ele acesse suas informações de HomeKit")](homekit-images/home01.png#lightbox)

Se o usuário responder **OK**, o aplicativo poderá trabalhar com seus acessórios do homekit, caso contrário, ele não será e as chamadas para homekit falharão com um erro.

Com o gerente doméstico em vigor, o aplicativo precisará ver se uma página inicial principal foi configurada e, caso contrário, fornecer uma maneira para o usuário criar e atribuir uma.

### <a name="accessing-the-primary-home"></a>Acessando a página inicial principal

Conforme mencionado acima, uma página inicial principal deve ser criada e configurada antes de o HomeKit estar disponível e é responsabilidade do aplicativo fornecer uma maneira para o usuário criar e atribuir uma página inicial primária, caso ainda não exista uma.

Quando seu aplicativo inicia pela primeira vez ou retorna do plano de fundo, ele precisa `DidUpdateHomes` monitorar o evento `HMHomeManager` da classe para verificar a existência de uma página inicial primária. Se não houver um, ele deverá fornecer uma interface para o usuário criar um.

O código a seguir pode ser adicionado a um controlador de exibição para verificar a página inicial principal:

```csharp
using HomeKit;
...

public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
...

// Wireup events
ThisApp.HomeManager.DidUpdateHomes += (sender, e) => {

    // Was a primary home found?
    if (ThisApp.HomeManager.PrimaryHome == null) {
        // Ask user to add a home
        PerformSegue("AddHomeSegue",this);
    }
};
```

Quando o gerente inicial faz uma conexão com o homekit, `DidUpdateHomes` o evento será disparado, todas as casas existentes serão carregadas na coleção de casas do gerente e a página inicial primária será carregada, se disponível.

### <a name="adding-a-primary-home"></a>Adicionando uma página inicial primária

Se a `PrimaryHome` `HMHomeManager` Propriedade do for `null` posterior a um `DidUpdateHomes` evento, você precisará fornecer uma maneira para o usuário criar e atribuir uma página inicial primária antes de continuar.

Normalmente, o aplicativo apresentará um formulário para que o usuário nomeie uma nova casa, que será passada para o gerente inicial para configurar como a página inicial principal. Para o aplicativo de exemplo **HomeKitIntro** , uma exibição modal foi criada no designer do IOS e chamada pelo `AddHomeSegue` transição da interface principal do aplicativo.

Ele fornece um campo de texto para que o usuário insira um nome para a nova página inicial e um botão para adicionar a página inicial. Quando o usuário toca no botão **Adicionar página inicial** , o código a seguir chama o gerente inicial para adicionar a página inicial:

```csharp
// Add new home to HomeKit
ThisApp.HomeManager.AddHome(HomeName.Text,(home,error) =>{
    // Did an error occur
    if (error!=null) {
        // Yes, inform user
        AlertView.PresentOKAlert("Add Home Error",string.Format("Error adding {0}: {1}",HomeName.Text,error.LocalizedDescription),this);
        return;
    }

    // Make the primary house
    ThisApp.HomeManager.UpdatePrimaryHome(home,(err) => {
        // Error?
        if (err!=null) {
            // Inform user of error
            AlertView.PresentOKAlert("Add Home Error",string.Format("Unable to make this the primary home: {0}",err.LocalizedDescription),this);
            return ;
        }
    });

    // Close the window when the home is created
    DismissViewController(true,null);
});
```

O `AddHome` método tentará criar uma nova página inicial e retorná-la à rotina de retorno de chamada fornecida. Se a `error` propriedade não `null`for, ocorreu um erro e ele deve ser apresentado ao usuário. Os erros mais comuns são causados por um nome residencial não exclusivo ou o gerente inicial não consegue se comunicar com o HomeKit.

Se a página inicial foi criada com êxito, você precisará chamar `UpdatePrimaryHome` o método para definir a nova página inicial como a página inicial principal. Novamente, se a `error` propriedade não `null`for, ocorreu um erro e ela deve ser apresentada ao usuário.

Você também deve monitorar os eventos `DidAddHome` e `DidRemoveHome` o Gerenciador doméstico e atualizar a interface do usuário do aplicativo, conforme necessário.

> [!IMPORTANT]
> O `AlertView.PresentOKAlert` método usado no código de exemplo acima é uma classe auxiliar no aplicativo HomeKitIntro que facilita o trabalho com os alertas do Ios.

## <a name="finding-new-accessories"></a>Localizando novos acessórios

Depois que uma página inicial principal tiver sido definida ou carregada do gerente inicial, seu aplicativo Xamarin. Ios poderá chamar `HMAccessoryBrowser` o para encontrar novos acessórios de automação inicial e adicioná-los a uma página inicial.

Chame o `StartSearchingForNewAccessories` método para começar a procurar novos acessórios e o `StopSearchingForNewAccessories` método quando terminar.

> [!IMPORTANT]
> `StartSearchingForNewAccessories`Não deve ser deixado em execução por longos períodos de tempo porque afetará negativamente a vida útil da bateria e o desempenho do dispositivo iOS. A Apple sugere `StopSearchingForNewAccessories` a chamada após um minuto ou apenas a pesquisa da interface do usuário do acessório de localização.

O `DidFindNewAccessory` evento será chamado quando novos acessórios forem descobertos e serão adicionados `DiscoveredAccessories` à lista no navegador acessório.

A `DiscoveredAccessories` lista conterá uma coleção de `HMAccessory` objetos que definem um dispositivo de automação inicial de homekit habilitado e seus serviços disponíveis, como luzes ou controle de porta de garagem.

Depois que o novo acessório for encontrado, ele deverá ser apresentado ao usuário e, portanto, poderá selecioná-lo e adicioná-lo a uma página inicial. Exemplo:

[![](homekit-images/accessory01.png "Encontrando um novo acessório")](homekit-images/accessory01.png#lightbox)

Chame o `AddAccessory` método para adicionar o acessório selecionado à coleção home. Por exemplo:

```csharp
// Add the requested accessory to the home
ThisApp.HomeManager.PrimaryHome.AddAccessory (_controller.AccessoryBrowser.DiscoveredAccessories [indexPath.Row], (err) => {
    // Did an error occur
    if (err !=null) {
        // Inform user of error
        AlertView.PresentOKAlert("Add Accessory Error",err.LocalizedDescription,_controller);
    }
});
```

Se a `err` propriedade não `null`for, ocorreu um erro e ele deve ser apresentado ao usuário. Caso contrário, será solicitado que o usuário insira o código de instalação do dispositivo a ser adicionado:

[![](homekit-images/accessory02.png "Insira o código de instalação do dispositivo a ser adicionado")](homekit-images/accessory02.png#lightbox)

No simulador de acessório do HomeKit, esse número pode ser encontrado no campo **código de instalação** :

[![](homekit-images/accessory03.png "O campo de código de instalação no simulador de acessório do HomeKit")](homekit-images/accessory03.png#lightbox)

Para os acessórios do HomeKit real, o código de instalação será impresso em um rótulo no próprio dispositivo, na caixa produto ou no manual do usuário do acessório.

Você deve monitorar o evento do `DidRemoveNewAccessory` navegador acessório e atualizar a interface do usuário para remover um acessório da lista disponível depois que o usuário o tiver adicionado à sua coleção inicial.

## <a name="working-with-accessories"></a>Trabalhando com acessórios

Depois de uma casa principal e foi estabelecida e os acessórios foram adicionados a ela, você pode apresentar uma lista de acessórios (e, opcionalmente, salas) para o usuário trabalhar.

O `HMRoom` objeto contém todas as informações sobre uma determinada sala e os acessórios que pertencem a ela. As salas podem, opcionalmente, ser organizadas em uma ou mais zonas. Um `HMZone` contém todas as informações sobre uma determinada zona e todas as salas que pertencem a ela.

Para este exemplo, vamos manter as coisas simples e trabalhar diretamente com os acessórios de uma casa, em vez de organizá-los em salas ou zonas.

O `HMHome` objeto contém uma lista de acessórios atribuídos que podem ser apresentados ao usuário em sua `Accessories` propriedade. Por exemplo:

[![](homekit-images/accessory04.png "Um acessório de exemplo")](homekit-images/accessory04.png#lightbox)

Formulário aqui, o usuário pode selecionar um acessório específico e trabalhar com os serviços que ele fornece.

## <a name="working-with-services"></a>Trabalhando com serviços

Quando o usuário interage com um determinado dispositivo de automação inicial do HomeKit habilitado, ele é geralmente por meio dos serviços que ele fornece. A `Services` propriedade `HMService` da classe contém uma coleção de objetos que definem os serviços que um dispositivo oferece. `HMAccessory`

Os serviços são coisas como luzes, termostatos conectados, aberturas de porta de garagem, comutadores ou bloqueios. Alguns dispositivos (como uma abertura de porta de garagem) fornecerão mais de um serviço, como uma luz e a capacidade de abrir ou fechar uma porta.

Além dos serviços específicos que um determinado acessório fornece, cada acessório contém um `Information Service` que define propriedades como seu nome, fabricante, modelo e número de série.

### <a name="accessory-service-types"></a>Tipos de serviço de acessório

Os seguintes tipos de serviço estão disponíveis por `HMServiceType` meio da enumeração:

- **AccessoryInformation** -fornece informações sobre o dispositivo de automação inicial (acessório) fornecido.
- **AirQualitySensor** -define um sensor de qualidade de ar.
- **Bateria** -define o estado da bateria de um acessório.
- **CarbonDioxideSensor** – define um sensor de dióxido de carbono.
- **CarbonMonoxideSensor** – define um sensor de monóxido de carbono.
- **ContactSensor** – define um sensor de contato (como uma janela que está sendo aberta ou fechada).
- **Porta** -define um sensor de estado de porta (como aberto ou fechado).
- **Ventilador** -define um ventilador controlado remotamente.
- **GarageDoorOpener** -define uma abertura de porta de garagem.
- **HumiditySensor** – define um sensor de umidade.
- **LeakSensor** -define um sensor de vazamento (como para um aquecedor de água quente ou máquina washing).
- **Lâmpada** – define uma luz autônoma ou uma luz que faz parte de outro acessório (como uma abertura de porta de garagem).
- **LightSensor** -define um sensor de luz.
- **LockManagement** – define um serviço que gerencia um bloqueio de porta automatizado.
- **LockMechanism** -define um bloqueio controlado remotamente (como um bloqueio de porta).
- **MotionSensor** -define um sensor de movimento.
- **OccupancySensor** -define um sensor de ocupação.
- **Tomada** -define uma tomada de parede controlada remotamente.
- **SecuritySystem** – define um sistema de segurança doméstico.
- **StatefulProgrammableSwitch** -define uma opção programável que permanece em um estado de acionamento uma vez disparada (como uma opção Flip).
- **StatelessProgrammableSwitch** -define uma opção programável que retorna ao estado inicial depois de ser disparado (como um botão de ação).
- **SmokeSensor** -define um sensor de fumaça.
- **Switch** -define uma opção on/off como uma opção de parede padrão.
- **Sensor** – define um sensor de temperatura.
- **Termostato** -define um termostato inteligente usado para controlar um sistema HVAC.
- **Janela** -define uma janela automatizada que cascata ser aberta ou fechada remotamente.
- **WindowCovering** – define uma janela controlada remotamente que abrange, como blinds que pode ser aberta ou fechada.

### <a name="displaying-service-information"></a>Exibindo informações de serviço

Depois de carregar um `HMAccessory` , você pode consultar os `HNService` objetos individuais que ele fornece e exibir essas informações para o usuário:

[![](homekit-images/accessory05.png "Exibindo informações de serviço")](homekit-images/accessory05.png#lightbox)

Você deve sempre verificar a `Reachable` propriedade de um `HMAccessory` antes de tentar trabalhar com ele. Um acessório pode estar inacessível o usuário não está dentro do intervalo do dispositivo ou se foi desconectado.

Depois que um serviço tiver sido selecionado, o usuário poderá exibir ou modificar uma ou mais características desse serviço para monitorar ou controlar um determinado dispositivo de automação inicial.

<a name="Working-with-Characteristics" />

## <a name="working-with-characteristics"></a>Trabalhando com características

Cada `HMService` objeto pode conter uma coleção de `HMCharacteristic` objetos que pode fornecer informações sobre o estado do serviço (como uma porta que está sendo aberta ou fechada) ou permitir que o usuário ajuste um estado (como definir a cor de uma luz).

`HMCharacteristic`o não só fornece informações sobre uma característica e seu estado, mas também fornece métodos para trabalhar com o estado por meio de`HMCharacteristisMetadata` _metadados de características_ (). Esses metadados podem fornecer Propriedades (como intervalos de valores mínimos e máximos) que são úteis ao exibir informações para o usuário ou permitir que eles modifiquem Estados.

A `HMCharacteristicType` enumeração fornece um conjunto de valores de metadados de características que podem ser definidos ou modificados da seguinte maneira:

- AdminOnlyAccess
- AirParticulateDensity
- AirParticulateSize
- Qualidade do
- AudioFeedback
- BatteryLevel
- Lo
- CarbonDioxideDetected
- CarbonDioxideLevel
- CarbonDioxidePeakLevel
- CarbonMonoxideDetected
- CarbonMonoxideLevel
- CarbonMonoxidePeakLevel
- Carregandostate
- Contatostate
- CoolingThreshold
- CurrentDoorState
- CurrentHeatingCooling
- CurrentHorizontalTilt
- CurrentLightLevel
- CurrentLockMechanismState
- CurrentPosition
- CurrentRelativeHumidity
- CurrentSecuritySystemState
- CurrentTemperature
- CurrentVerticalTilt
- FirmwareVersion
- HardwareVersion
- HeatingCoolingStatus
- HeatingThreshold
- HoldPosition
- Matiz
- Identificar
- InputEvent
- LeakDetected
- LockManagementAutoSecureTimeout
- LockManagementControlPoint
- LockMechanismLastKnownAction
- Logs
- Fabricante
- Modelo
- MotionDetected
- Nome
- ObstructionDetected
- OccupancyDetected
- OutletInUse
- OutputState
- Positionstate
- PowerState
- RotationDirection
- RotationSpeed
- saturação
- SerialNumber
- SmokeDetected
- SoftwareVersion
- StatusActive
- StatusFault
- StatusJammed
- StatusLowBattery
- StatusTampered
- TargetDoorState
- TargetHeatingCooling
- TargetHorizontalTilt
- TargetLockMechanismState
- TargetPosition
- TargetRelativeHumidity
- TargetSecuritySystemState
- TargetTemperature
- TargetVerticalTilt
- TemperatureUnits
- Version

### <a name="working-with-a-characteristics-value"></a>Trabalhando com o valor de uma característica

Para garantir que seu aplicativo tenha o estado mais recente de determinada característica, chame o `ReadValue` método `HMCharacteristic` da classe. Se a `err` propriedade não `null`for, um erro ocorreu e pode ou não ser apresentado ao usuário.

A propriedade da `Value` característica contém o estado atual da característica determinada como um `NSObject`e, como tal, não pode ser trabalhada diretamente C#no.

Para ler o valor, a seguinte classe auxiliar foi adicionada ao aplicativo de exemplo **HomeKitIntro** :

```csharp
using System;
using Foundation;
using System.Globalization;
using CoreGraphics;

namespace HomeKitIntro
{
    /// <summary>
    /// NS object converter is a helper class that helps to convert NSObjects into
    /// C# objects
    /// </summary>
    public static class NSObjectConverter
    {
        #region Static Methods
        /// <summary>
        /// Converts to an object.
        /// </summary>
        /// <returns>The object.</returns>
        /// <param name="nsO">Ns o.</param>
        /// <param name="targetType">Target type.</param>
        public static Object ToObject (NSObject nsO, Type targetType)
        {
            if (nsO is NSString) {
                return nsO.ToString ();
            }

            if (nsO is NSDate) {
                var nsDate = (NSDate)nsO;
                return DateTime.SpecifyKind ((DateTime)nsDate, DateTimeKind.Unspecified);
            }

            if (nsO is NSDecimalNumber) {
                return decimal.Parse (nsO.ToString (), CultureInfo.InvariantCulture);
            }

            if (nsO is NSNumber) {
                var x = (NSNumber)nsO;

                switch (Type.GetTypeCode (targetType)) {
                case TypeCode.Boolean:
                    return x.BoolValue;
                case TypeCode.Char:
                    return Convert.ToChar (x.ByteValue);
                case TypeCode.SByte:
                    return x.SByteValue;
                case TypeCode.Byte:
                    return x.ByteValue;
                case TypeCode.Int16:
                    return x.Int16Value;
                case TypeCode.UInt16:
                    return x.UInt16Value;
                case TypeCode.Int32:
                    return x.Int32Value;
                case TypeCode.UInt32:
                    return x.UInt32Value;
                case TypeCode.Int64:
                    return x.Int64Value;
                case TypeCode.UInt64:
                    return x.UInt64Value;
                case TypeCode.Single:
                    return x.FloatValue;
                case TypeCode.Double:
                    return x.DoubleValue;
                }
            }

            if (nsO is NSValue) {
                var v = (NSValue)nsO;

                if (targetType == typeof(IntPtr)) {
                    return v.PointerValue;
                }

                if (targetType == typeof(CGSize)) {
                    return v.SizeFValue;
                }

                if (targetType == typeof(CGRect)) {
                    return v.RectangleFValue;
                }

                if (targetType == typeof(CGPoint)) {
                    return v.PointFValue;
                }
            }

            return nsO;
        }

        /// <summary>
        /// Convert to string
        /// </summary>
        /// <returns>The string.</returns>
        /// <param name="nsO">Ns o.</param>
        public static string ToString(NSObject nsO) {
            return (string)ToObject (nsO, typeof(string));
        }

        /// <summary>
        /// Convert to date time
        /// </summary>
        /// <returns>The date time.</returns>
        /// <param name="nsO">Ns o.</param>
        public static DateTime ToDateTime(NSObject nsO){
            return (DateTime)ToObject (nsO, typeof(DateTime));
        }

        /// <summary>
        /// Convert to decimal number
        /// </summary>
        /// <returns>The decimal.</returns>
        /// <param name="nsO">Ns o.</param>
        public static decimal ToDecimal(NSObject nsO){
            return (decimal)ToObject (nsO, typeof(decimal));
        }

        /// <summary>
        /// Convert to boolean
        /// </summary>
        /// <returns><c>true</c>, if bool was toed, <c>false</c> otherwise.</returns>
        /// <param name="nsO">Ns o.</param>
        public static bool ToBool(NSObject nsO){
            return (bool)ToObject (nsO, typeof(bool));
        }

        /// <summary>
        /// Convert to character
        /// </summary>
        /// <returns>The char.</returns>
        /// <param name="nsO">Ns o.</param>
        public static char ToChar(NSObject nsO){
            return (char)ToObject (nsO, typeof(char));
        }

        /// <summary>
        /// Convert to integer
        /// </summary>
        /// <returns>The int.</returns>
        /// <param name="nsO">Ns o.</param>
        public static int ToInt(NSObject nsO){
            return (int)ToObject (nsO, typeof(int));
        }

        /// <summary>
        /// Convert to float
        /// </summary>
        /// <returns>The float.</returns>
        /// <param name="nsO">Ns o.</param>
        public static float ToFloat(NSObject nsO){
            return (float)ToObject (nsO, typeof(float));
        }

        /// <summary>
        /// Converts to double
        /// </summary>
        /// <returns>The double.</returns>
        /// <param name="nsO">Ns o.</param>
        public static double ToDouble(NSObject nsO){
            return (double)ToObject (nsO, typeof(double));
        }
        #endregion
    }
}
```

O `NSObjectConverter` é usado sempre que o aplicativo precisa ler o estado atual de uma característica. Por exemplo:

```csharp
var value = NSObjectConverter.ToFloat (characteristic.Value);
```

A linha acima converte o valor em um `float` que pode ser usado no código do Xamarin C# .

Para modificar um `HMCharacteristic`, chame seu `WriteValue` método e empacote o novo valor em uma `NSObject.FromObject` chamada. Por exemplo:

```csharp
Characteristic.WriteValue(NSObject.FromObject(value),(err) =>{
    // Was there an error?
    if (err!=null) {
        // Yes, inform user
        AlertView.PresentOKAlert("Update Error",err.LocalizedDescription,Controller);
    }
});
```

Se a `err` propriedade não `null`for, ocorreu um erro e deve ser apresentado ao usuário.

### <a name="testing-characteristic-value-changes"></a>Testando alterações de valor de característica

Ao trabalhar com `HMCharacteristics` acessórios simulados, as `Value` modificações na propriedade podem ser monitoradas dentro do simulador de acessório do homekit.

Com o aplicativo **HomeKitIntro** executado em hardware de dispositivo IOS real, as alterações no valor de uma característica devem ser vistas quase instantaneamente no simulador de acessório do homekit. Por exemplo, alterar o estado de uma luz no aplicativo iOS:

[![](homekit-images/test01.png "Alterando o estado de uma luz em um aplicativo iOS")](homekit-images/test01.png#lightbox)

Deve alterar o estado da luz no simulador de acessório do HomeKit. Se o valor não for alterado, verifique o estado da mensagem de erro ao gravar novos valores de características e verifique se o acessório ainda está acessível.

## <a name="advanced-homekit-features"></a>Recursos avançados do HomeKit

Este artigo abordou os recursos básicos necessários para trabalhar com acessórios do HomeKit em um aplicativo Xamarin. iOS. No entanto, há vários recursos avançados do HomeKit que não são abordados nesta introdução:

- **Salas** – os acessórios habilitados para homekit podem ser organizados opcionalmente em salas pelo usuário final. Isso permite que o HomeKit apresente acessórios de uma maneira que seja fácil de entender e trabalhar com o usuário. Para obter mais informações sobre como criar e manter salas, consulte a documentação do [HMRoom](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMRoom_Class/index.html#//apple_ref/occ/cl/HMRoom) da Apple.
- **Zonas** -as salas podem, opcionalmente, ser organizadas em zonas pelo usuário final. Uma zona refere-se a uma coleção de salas que o usuário pode tratar como uma única unidade. Por exemplo: Upescadas, cybercafé ou porão. Novamente, isso permite que o HomeKit apresente e trabalhe com acessórios de uma maneira que faça sentido para o usuário final. Para obter mais informações sobre como criar e manter zonas, consulte a documentação do [HMZone](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMZone_Class/index.html#//apple_ref/occ/cl/HMZone) da Apple.
- **Ações e conjuntos** de ações – ações modificam características do serviço de acessório e podem ser agrupadas em conjuntos. Os conjuntos de ações agem como scripts para controlar um grupo de acessórios e coordenar suas ações. Por exemplo, um script "assistir à TV" pode fechar o blinds, esmaecer as luzes e ligar a televisão e seu sistema de som. Para obter mais informações sobre como criar e manter ações e conjuntos de ação, consulte a documentação do [HMAction](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMAction_Class/index.html#//apple_ref/occ/cl/HMAction) e do [HMActionSet](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMActionSet_Class/index.html#//apple_ref/occ/cl/HMActionSet) da Apple.
- **Gatilhos** – um gatilho pode ativar um ou mais conjuntos de ações quando um determinado conjunto de condições for atendido. Por exemplo, ative a luz portch e bloqueie todas as portas externas quando ela ficar escura fora do. Para obter mais informações sobre como criar e manter gatilhos, consulte a documentação do [HMTrigger](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMTrigger_Class/index.html#//apple_ref/occ/cl/HMTrigger) da Apple.

Como esses recursos usam as mesmas técnicas apresentadas acima, eles devem ser fáceis de implementar seguindo o guia de [HomeKitDeveloper](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)da Apple, as [diretrizes de interface do usuário do homekit](https://developer.apple.com/homekit/ui-guidelines/) e a referência da estrutura do [homekit](https://developer.apple.com/library/ios/home_kit_framework_ref).

## <a name="homekit-app-review-guidelines"></a>Diretrizes de revisão do aplicativo HomeKit

Antes de enviar um aplicativo Xamarin. iOS habilitado para HomeKit para o iTunes Connect for Release na iTunes App Store, certifique-se de seguir as diretrizes da Apple para aplicativos habilitados para HomeKit:

- A finalidade principal do aplicativo _deve_ ser a automação inicial se estiver usando a estrutura homekit.
- O texto de marketing do aplicativo deve notificar os usuários de que o HomeKit está sendo usado e deve fornecer uma política de privacidade.
- A coleta de informações do usuário ou o uso do HomeKit para publicidade é estritamente proibido.

Para obter as diretrizes completas de revisão, consulte as [diretrizes de revisão da App Store](https://developer.apple.com/app-store/review/guidelines/)da Apple.

## <a name="whats-new-in-ios-9"></a>O que há de novo no iOS 9

A Apple fez as seguintes alterações e inclusões no HomeKit para iOS 9:

- **Manutenção de objetos existentes** – se um acessório existente for modificado, o gerente inicial`HMHomeManager`() informará sobre o item específico que foi modificado.
- **Identificadores persistentes** – todas as classes homekit relevantes agora `UniqueIdentifier` incluem uma propriedade para identificar exclusivamente um determinado item em aplicativos habilitados para homekit (ou instâncias do mesmo aplicativo).
- **Gerenciamento de usuários** – adicionado um controlador de exibição interno para fornecer ao gerenciamento de usuários os que têm acesso aos dispositivos homekit na página inicial do usuário primário.
- **Recursos do usuário** -os usuários do homekit agora têm um conjunto de privilégios que controlam quais funções eles são capazes de usar nos acessórios do homekit e do homekit habilitados. Seu aplicativo deve exibir apenas recursos relevantes para o usuário atual. Por exemplo, somente um administrador deve ser capaz de manter outros usuários.
- **Cenas predefinidas** -as cenas predefinidas foram criadas para quatro eventos comuns que ocorrem para o usuário homekit médio: Obter, sair, retornar, ir para cama. Essas cenas predefinidas não podem ser excluídas de uma página inicial.
- Os **bastidores e o Siri** -Siri têm suporte mais profundo para cenas no Ios 9 e podem reconhecer o nome de qualquer cena definida em homekit. Um usuário pode executar uma cena simplesmente falando com seu nome para Siri.
- **Categorias de acessório** – um conjunto de categorias predefinidas foi adicionado a todos os acessórios e ajuda a identificar o tipo de acessório que está sendo adicionado a um início ou trabalhado de dentro de seu aplicativo. Essas novas categorias estão disponíveis durante a configuração do acessório.
- **Apple Watch suporte** -homekit agora está disponível para watchOS e o Apple Watch poderá controlar os dispositivos habilitados para homekit sem um iPhone perto do relógio. O HomeKit para watchOS dá suporte aos seguintes recursos: Exibição de residências, controle de acessórios e execução de cenas.
- **Novo tipo de gatilho de evento** -além dos gatilhos de tipo de temporizador com suporte no Ios 8, o Ios 9 agora dá suporte a gatilhos de eventos com base no estado de acessório (como dados de sensor) ou geolocalização. Os gatilhos `NSPredicates` de evento usam para definir condições para sua execução.
- **Acesso remoto** – com acesso remoto, o usuário agora pode controlar seus acessórios de automação inicial homekit habilitados quando eles estão fora da casa em um local remoto. No iOS 8, isso só era suportado se o usuário tivesse uma 3ª TV da Apple em casa. No iOS 9, essa limitação é elevada e o acesso remoto é suportado por meio do iCloud e do HAP (HomeKit acessório Protocol).
- **Novos recursos de ble (Bluetooth de baixa energia)** -o homekit agora dá suporte a mais tipos de acessório que podem se comunicar por meio do protocolo ble (Bluetooth de baixa energia). Usando o túnel seguro do HAP, um acessório HomeKit pode expor outro acessório Bluetooth por Wi-Fi (se estiver fora do intervalo Bluetooth). No iOS 9, os acessórios do BLE têm suporte total para notificações e metadados.
- **Novas categorias de acessório** – a Apple adicionou as seguintes novas categorias de acessório no Ios 9: Folhas de janela, portas motoras e janelas, sistemas de alarme, sensores e comutadores programáveis.

Para obter mais informações sobre os novos recursos do HomeKit no iOS 9, consulte o [índice de homekit](https://developer.apple.com/homekit/) da Apple e [o que há de novo no](https://developer.apple.com/videos/wwdc/2015/?id=210) vídeo do homekit.

## <a name="summary"></a>Resumo

Este artigo introduziu o HomeKit Home Automation Framework da Apple. Ele mostrou como configurar e configurar dispositivos de teste usando o simulador de acessório HomeKit e como criar um aplicativo Xamarin. iOS simples para descobrir, comunicar-se com e controlar dispositivos de automação doméstica usando o HomeKit.

## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [O que há de novo no iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guia do HomeKitDeveloper](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [Diretrizes de interface do usuário do HomeKit](https://developer.apple.com/homekit/ui-guidelines/)
- [Referência da estrutura do HomeKit](https://developer.apple.com/library/ios/home_kit_framework_ref)
