---
title: HomeKit no xamarin. IOS
description: HomeKit é a estrutura da Apple para controlar os dispositivos de automação residencial. Este artigo apresenta o HomeKit e aborda os acessórios de teste de configuração no simulador de Acessórios do HomeKit e escrever um aplicativo simples do xamarin. IOS para interagir com esses Acessórios.
ms.prod: xamarin
ms.assetid: 90C0C553-916B-46B1-AD52-1E7332792283
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 6793190fa3278455a00d7ea08ab52a643c369a35
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57672814"
---
# <a name="homekit-in-xamarinios"></a>HomeKit no xamarin. IOS

_HomeKit é a estrutura da Apple para controlar os dispositivos de automação residencial. Este artigo apresenta o HomeKit e aborda os acessórios de teste de configuração no simulador de Acessórios do HomeKit e escrever um aplicativo simples do xamarin. IOS para interagir com esses Acessórios._

[![](homekit-images/accessory01.png "Aplicativo habilitado para um exemplo HomeKit")](homekit-images/accessory01.png#lightbox)

Apple introduziu o HomeKit no iOS 8 como uma maneira de integrar perfeitamente vários dispositivos de automação residencial fornecedores uma variedade de em uma unidade única e coerente. Promovendo um protocolo comum para descobrir, configurar e controlar os dispositivos de automação residencial, HomeKit permite que os dispositivos de fornecedores não relacionados ao trabalhar em conjunto, tudo sem a necessidade de coordenar os esforços de fornecedores individuais.

Com o HomeKit, você pode criar um aplicativo xamarin. IOS que controla a qualquer dispositivo HomeKit habilitado sem o uso de fornecedor fornecido APIs ou aplicativos. Como usar o HomeKit, você pode fazer o seguinte:

- Descubra novos dispositivos de automação residencial do HomeKit habilitado e adicioná-los para um banco de dados que será mantido em todos os dispositivos iOS do usuário.
- Instalar, configurar, exibir e controlar qualquer dispositivo em que o HomeKit _Home banco de dados de configuração_.
- Se comunicar com qualquer dispositivo HomeKit pré-configurado e comando-los para executar as ações individuais ou trabalham em conjunto, como ativar a todas as luzes na cozinha.

Além de servir dispositivos no início configuração banco de dados para aplicativos do HomeKit habilitado, o HomeKit fornece acesso a comandos de voz Siri. Dado um programa de instalação do HomeKit adequadamente configurado, o usuário pode emitir comandos de voz, como "Siri, ligue as luzes na sala."

<a name="Home-Configuration-Database" />

## <a name="the-home-configuration-database"></a>O banco de dados de configuração inicial

HomeKit organiza todos os dispositivos de automação em um determinado local em uma coleção de início. Esta coleção fornece uma maneira para o usuário agrupar seus dispositivos de automação residencial unidades logicamente organizadas com rótulos significativos e legível por humanos.

A coleção de início é armazenada em uma página inicial configuração do banco de dados que serão automaticamente sincronizados e de backup em todos os dispositivos iOS do usuário. HomeKit fornece as seguintes classes para trabalhar com o banco de dados do início configuração:

- `HMHome` -Este é o contêiner de nível superior que mantém todas as informações e configurações para todos os dispositivos de automação residencial em um único local físico (por exemplo. uma única família residência). O usuário pode ter mais de uma residência, como sua página inicial do principal e uma casa de férias. Ou elas podem ter diferentes "casas" na mesma propriedade, como a casa principal e uma casa de convidado ao longo de garagem. De qualquer forma, pelo menos um `HMHome` objeto _deve_ ser configurado e armazenados antes de qualquer outra informação HomeKit pode ser inserida.
- `HMRoom` -Embora opcional, uma `HMRoom` permite que o usuário defina salas específicas dentro de uma casa (`HMHome`), como: Cozinha, banheiro, garagem ou sala de estar. O usuário pode agrupar todos os dispositivos de automação residencial em um local específico na sua casa em um `HMRoom` e agir nelas como uma unidade. Por exemplo, solicitando que o Siri para desativar as luzes garagem.
- `HMAccessory` – Isso representa um indivíduo, HomeKit física habilitado o dispositivo de automação que foi instalado na natureza do usuário (como um termostato inteligente). Cada `HMAccessory` for atribuído a um `HMRoom`. Se o usuário não tiver configurado qualquer salas, HomeKit atribui Acessórios para uma sala de padrão especial.
- `HMService` – Representa um serviço fornecido por um determinado `HMAccessory`, como o estado ativado/desativado de uma luz ou sua cor (se há suporte para alteração de cor). Cada `HMAccessory` pode ter mais de um serviço, como um privilégio portão que também inclui uma luz. Além disso, um determinado `HMAccessory` pode ter serviços, como atualização de firmware, que estão fora do controle de usuário.
- `HMZone` -Permite que o usuário agrupa uma coleção de `HMRoom` objetos em zonas lógicas, como quartinho, Downstairs ou porão. Embora opcional, isso permite interações como Perguntar Siri para ativar todos da luz downstairs off.

<a name="Provisioning-a-HomeKit-App" />

## <a name="provisioning-a-homekit-app"></a>Provisionar um aplicativo do HomeKit

Por causa dos requisitos de segurança impostos pelo HomeKit, um aplicativo xamarin. IOS que usa a estrutura HomeKit deve ser configurado corretamente no Portal do desenvolvedor da Apple e no arquivo de projeto xamarin. IOS.

Faça o seguinte:

1. Faça logon na [Portal do desenvolvedor Apple](https://developer.apple.com).
2. Clique em **certificados, identificadores e perfis**.
3. Se você ainda não fez isso, clique em **identificadores** e crie uma ID para seu aplicativo (por exemplo, `com.company.appname`), ou editar sua ID existente.
4. Certifique-se de que o **HomeKit** serviço foi verificado para a ID especificada: 

    [![](homekit-images/provision01.png "Habilitar o serviço do HomeKit para a ID especificada")](homekit-images/provision01.png#lightbox)
5. Salve as alterações.
4. Clique em **perfis de provisionamento** > **desenvolvimento** e criar um novo perfil para seu aplicativo de provisionamento de desenvolvimento: 

    [![](homekit-images/provision02.png "Criar um novo perfil para o aplicativo de provisionamento de desenvolvimento")](homekit-images/provision02.png#lightbox)
5. Baixe e instale o novo perfil de provisionamento ou usar o Xcode para baixar e instalar o perfil de.
6. Edite as opções de projeto do xamarin. IOS e certifique-se de que você está usando o perfil de provisionamento que você acabou de criar: 

    [![](homekit-images/provision03.png "Selecione o perfil de provisionamento recém-criado")](homekit-images/provision03.png#lightbox)
7. Em seguida, edite sua **Info. plist** de arquivo e certifique-se de que você está usando a ID do aplicativo que foi usado para criar o perfil de provisionamento: 

    [![](homekit-images/provision04.png "Defina a ID do aplicativo ")](homekit-images/provision04.png#lightbox)
8. Por fim, edite sua **Entitlements. plist** do arquivo e certifique-se de que o **HomeKit** direito tiver sido selecionado: 

    [![](homekit-images/provision05.png "Habilitar os direitos do HomeKit")](homekit-images/provision05.png#lightbox)
9. Salve as alterações a todos os arquivos.

Com essas configurações em vigor, o aplicativo agora está pronto para acessar as APIs de estrutura HomeKit. Para obter informações detalhadas sobre o provisionamento, consulte nosso [aprovisionamento](~/ios/get-started/installation/device-provisioning/index.md) e [provisionamento do seu aplicativo](~/ios/get-started/installation/device-provisioning/index.md) guias.

> [!IMPORTANT]
> Teste de um aplicativo do HomeKit habilitado requer um dispositivo iOS reais que foi configurado corretamente para desenvolvimento. HomeKit não pode ser testado do simulador de iOS.

## <a name="the-homekit-accessory-simulator"></a>O simulador de Acessórios do HomeKit

Para fornecer uma maneira de testar todos os dispositivos de automação residencial possíveis e serviços, sem a necessidade de ter um dispositivo físico, a Apple criou o _simulador de Acessórios do HomeKit_. Usando esse simulador, você pode instalar e configurar dispositivos virtuais do HomeKit.

### <a name="installing-the-simulator"></a>Instalando o simulador

A Apple fornece o simulador de Acessórios do HomeKit como um download separado do Xcode, portanto, você precisará instalá-lo antes de continuar.

Faça o seguinte:

1. Em um navegador da web, visite [Downloads para desenvolvedores da Apple](https://developer.apple.com/download/more/?name=for%20Xcode)
2. Baixe o **ferramentas adicionais para Xcode xxx** (onde xxx é a versão do Xcode instalada): 

    [![](homekit-images/simulator01.png "Baixe as ferramentas adicionais para o Xcode")](homekit-images/simulator01.png#lightbox)
3. Abra a imagem de disco e instalar as ferramentas em seu **aplicativos** directory.

Com o simulador de Acessórios do HomeKit instalado, Acessórios virtuais podem ser criados para teste.

### <a name="creating-virtual-accessories"></a>Criando Acessórios Virtual

Para iniciar o simulador de Acessórios do HomeKit e criar algumas Acessórios virtuais, faça o seguinte:

1. Na pasta de aplicativos, inicie o simulador de Acessórios do HomeKit: 

    [![](homekit-images/simulator02.png "O simulador de Acessórios do HomeKit")](homekit-images/simulator02.png#lightbox)
2. Clique o **+** botão e selecione **novo acessório...** : 

    [![](homekit-images/simulator03.png "Adicionar um novo acessório")](homekit-images/simulator03.png#lightbox)
3. Preencha as informações sobre o novo acessório e clique no **concluir** botão: 

    [![](homekit-images/simulator04.png "Preencha as informações sobre o novo acessório")](homekit-images/simulator04.png#lightbox)
4. Clique o **Adicionar serviço...** botão e selecione um tipo de serviço na lista suspensa: 

    [![](homekit-images/simulator05.png "Selecione um tipo de serviço na lista suspensa")](homekit-images/simulator05.png#lightbox)
5. Fornecer um **nome** para o serviço e clique o **concluir** botão: 

    [![](homekit-images/simulator06.png "Insira um nome para o serviço")](homekit-images/simulator06.png#lightbox)
6. Você pode fornecer características opcionais para um serviço clicando o **característica adicionar** botão e configurando as configurações necessárias: 

    [![](homekit-images/simulator07.png "Configurando as configurações necessárias")](homekit-images/simulator07.png#lightbox)
7. Repita as etapas acima para criar um de cada tipo de dispositivo de automação residencial virtual que dá suporte ao HomeKit.

Com alguns exemplo virtuais Acessórios do HomeKit criado e configurado, agora você pode consumir e controlar esses dispositivos de seu aplicativo xamarin. IOS.

## <a name="configuring-the-infoplist-file"></a>Configurando o arquivo Info. plist

Novidade no iOS 10 (e superior), o desenvolvedor precisará adicionar o `NSHomeKitUsageDescription` chave para o aplicativo `Info.plist` de arquivo e forneça uma cadeia de caracteres declarando por que o aplicativo deseja acessar o banco de dados do usuário HomeKit. Essa cadeia de caracteres será apresentada para o usuário na primeira vez que executar o aplicativo:

[![](homekit-images/info01.png "A caixa de diálogo de permissão do HomeKit")](homekit-images/info01.png#lightbox)

Para definir essa chave, faça o seguinte:

1. Clique duas vezes o `Info.plist` do arquivo na **Gerenciador de soluções** para abri-lo para edição.
2. Na parte inferior da tela, alterne para o **origem** modo de exibição.
3. Adicione um novo **entrada** à lista.
4. Na lista suspensa, selecione **privacidade – descrição de uso do HomeKit**: 

    [![](homekit-images/info02.png "Selecione privacidade – descrição de uso do HomeKit")](homekit-images/info02.png#lightbox)
5. Insira uma descrição de por que o aplicativo deseja acessar o banco de dados do usuário HomeKit: 

    [![](homekit-images/info03.png "Insira uma descrição")](homekit-images/info03.png#lightbox)
6. Salve as alterações no arquivo.

> [!IMPORTANT]
> Falha ao definir a `NSHomeKitUsageDescription` principais na `Info.plist` arquivo resultará no aplicativo _falhando silenciosamente_ (que está sendo fechado pelo sistema em tempo de execução) sem erros quando executado no iOS 10 (ou superior).

## <a name="connecting-to-homekit"></a>Conectar-se ao HomeKit

Para se comunicar com o HomeKit, seu aplicativo xamarin. IOS precisa primeiro criar uma instância da `HMHomeManager` classe. O Gerenciador de início é o ponto de entrada central no HomeKit e é responsável por fornecer uma lista de residências disponíveis, atualizar e manter essa lista e retornando o usuário _primário Home_.

O `HMHome` objeto contém todas as informações sobre uma casa oferecem incluindo qualquer salas, grupos ou zonas que podem conter, juntamente com os acessórios de automação residencial que foram instalados. Antes de todas as operações podem ser executadas em HomeKit, pelo menos um `HMHome` deve ser criada e atribuída como a página inicial do primário.

Seu aplicativo é responsável por verificando a existência de uma casa de primário e criar e atribuir uma se não existir.

### <a name="adding-a-home-manager"></a>Adicionar um Gerenciador de base

Para adicionar o reconhecimento do HomeKit a um aplicativo xamarin. IOS, edite o **AppDelegate.cs** arquivo para editá-lo e torná-lo semelhante ao seguinte:

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

Quando o aplicativo é executado pela primeira vez, o usuário será solicitado se deseja permitir que ele acessar suas informações de HomeKit:

[![](homekit-images/home01.png "O usuário será solicitado se deseja permitir que ele acessar suas informações de HomeKit")](homekit-images/home01.png#lightbox)

Se o usuário responde **Okey**, em seguida, o aplicativo será capaz de trabalhar com seus acessórios do HomeKit caso contrário, isso não ocorrerá e todas as chamadas para HomeKit falhará com um erro.

Com o Gerenciador Home no lugar, em seguida o aplicativo será necessário ver se uma página inicial do primário tiver sido configurado e se não estiver, fornecem uma maneira para criar e atribuir um usuário.

### <a name="accessing-the-primary-home"></a>Acessando a página inicial do primário

Como mencionado acima, uma casa primário deve ser criada e configurada antes de HomeKit está disponível e é responsabilidade do aplicativo para fornecer uma maneira para o usuário criar e atribuir uma casa primário se um já não existe.

Quando seu aplicativo pela primeira vez é iniciado ou retorna do plano de fundo, ele precisa monitorar o `DidUpdateHomes` eventos do `HMHomeManager` classe para verificar a existência de uma página inicial do primário. Se não existir, ele deve fornecer uma interface do usuário criar um.

O código a seguir pode ser adicionado a um controlador de exibição para verificar a página inicial do primário:

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

Quando o Gerenciador de Home fizer uma conexão com o HomeKit, o `DidUpdateHomes` evento será disparado, qualquer residências existentes serão carregadas à coleção do gerente de residências e domésticos primário será carregado, se disponível.

### <a name="adding-a-primary-home"></a>Adição de uma casa primário

Se o `PrimaryHome` propriedade do `HMHomeManager` é `null` depois que um `DidUpdateHomes` evento, você precisa fornecer uma maneira para o usuário criar e atribuir uma casa primário antes de continuar.

Normalmente, o aplicativo apresentará um formulário para o usuário nomear uma nova casa que, em seguida, é passada para o Gerenciador de base para instalação como a página inicial do primário. Para o **HomeKitIntro** aplicativo de exemplo, uma exibição modal foi criado no Designer de IOS e chamado pelo `AddHomeSegue` segue a partir da interface principal do aplicativo.

Ele fornece um campo de texto para o usuário insira um nome para a nova casa e um botão para adicionar a página inicial. Quando o usuário toca a **adicionar Home** botão, o código a seguir chama o Gerenciador de base para adicionar a página inicial:

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

O `AddHome` método tentará criar nova casa e retorná-lo para a rotina de retorno de chamada específico. Se o `error` propriedade não é `null`, ocorreu um erro e ele deve ser apresentado ao usuário. Os erros mais comuns são causados por um nome de base não exclusivo ou o Gerenciador de base não conseguir se comunicar com o HomeKit.

Se a página inicial foi criada com êxito, você precisará chamar o `UpdatePrimaryHome` método para definir a nova casa como a página inicial do primário. Novamente, se o `error` propriedade não é `null`, ocorreu um erro e ele deve ser apresentado ao usuário.

Você também deve monitorar a página inicial do Gerenciador `DidAddHome` e `DidRemoveHome` eventos e atualização de interface do usuário do aplicativo conforme necessário.

> [!IMPORTANT]
> O `AlertView.PresentOKAlert` método usado no código de exemplo acima é uma classe auxiliar no aplicativo HomeKitIntro que torna o trabalho com o iOS alertas mais fácil.


## <a name="finding-new-accessories"></a>Localizando novos acessórios

Depois de uma casa primário foi definida ou carregada do Gerenciador de base, o aplicativo xamarin. IOS poderá chamar o `HMAccessoryBrowser` para localizar os acessórios de automação residencial novo e adicioná-los para uma casa.

Chame o `StartSearchingForNewAccessories` método para iniciar a procura por novos acessórios e o `StopSearchingForNewAccessories` método quando terminar.

> [!IMPORTANT]
> `StartSearchingForNewAccessories` não deve ser deixado em execução por longos períodos de tempo, porque isso afetará negativamente vida útil da bateria e o desempenho do dispositivo iOS. A Apple sugere chamada `StopSearchingForNewAccessories` após um minuto ou pesquisando somente quando a interface do usuário localizar de acessório é apresentada ao usuário.

O `DidFindNewAccessory` evento será chamado quando novos acessórios são descobertos e eles serão adicionados para o `DiscoveredAccessories` lista no navegador acessório.

O `DiscoveredAccessories` lista conterá uma coleção de `HMAccessory` objetos que definem um dê HomeKit habilitado dispositivo automação residencial e seus serviços disponíveis, como as luzes ou controle de porta de garagem.

Depois que o novo acessório foi encontrado, ele deve ser apresentado ao usuário e então, pode selecioná-lo e adicioná-lo para uma casa. Exemplo:

[![](homekit-images/accessory01.png "Localizando um novo acessório")](homekit-images/accessory01.png#lightbox)

Chamar o `AddAccessory` para adicionar o acessório selecionado à coleção da página inicial. Por exemplo:

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

Se o `err` propriedade não é `null`, ocorreu um erro e ele deve ser apresentado ao usuário. Caso contrário, o usuário será solicitado a inserir o código de instalação para o dispositivo a ser adicionado:

[![](homekit-images/accessory02.png "Insira o código de configuração para o dispositivo a ser adicionado")](homekit-images/accessory02.png#lightbox)

No simulador de Acessórios do HomeKit esse número pode ser encontrado sob o **código de configuração** campo:

[![](homekit-images/accessory03.png "O campo de código de configuração no simulador de Acessórios do HomeKit")](homekit-images/accessory03.png#lightbox)

Acessórios do HomeKit real, o código de configuração será impressa ou em um rótulo no próprio dispositivo, na caixa do produto ou no manual do usuário do acessório.

Você deve monitorar o navegador acessório `DidRemoveNewAccessory` evento e o usuário de atualização de interface para remover um acessório da lista disponível depois que o usuário tiver adicionado à sua coleção Home.

## <a name="working-with-accessories"></a>Trabalhando com os acessórios

Uma vez uma casa de primário e foi estabelecida e Acessórios foram adicionados a ele, você pode apresentar uma lista de Acessórios (e, opcionalmente, salas) para trabalhar com o usuário.

O `HMRoom` objeto contém todas as informações sobre uma determinada sala e os acessórios que pertencem a ela. Salas, opcionalmente, podem ser organizadas em uma ou mais zonas. Um `HMZone` contém todas as informações sobre uma determinada região e todas as salas que pertencem a ela.

Para este exemplo, estamos vai ser mantendo o coisas simples e funcionam com os acessórios da casa diretamente, em vez de organizando-os em salas ou zonas.

O `HMHome` objeto contém uma lista de acessório atribuído que pode ser apresentada ao usuário no seu `Accessories` propriedade. Por exemplo:

[![](homekit-images/accessory04.png "Um acessório de exemplo")](homekit-images/accessory04.png#lightbox)

Formulário aqui, o usuário pode selecionar um acessório determinado e trabalhar com os serviços que ele oferece.

## <a name="working-with-services"></a>Trabalhar com os serviços

Quando o usuário interage com um determinado dispositivo habilitado automação residencial HomeKit, normalmente é por meio dos serviços que ele oferece. O `Services` propriedade do `HMAccessory` classe contém uma coleção de `HMService` oferece de um dispositivo de objetos que definem os serviços.

Os serviços são coisas como termostatos, luzes, garagem de portas de garagens, comutadores ou bloqueios. Alguns dispositivos (como um portão) fornece mais de um serviço, como uma luz e a capacidade de abrir ou fechar uma porta.

Os serviços específicos que fornece um acessório determinado, além de cada acessório contém um `Information Service` que define as propriedades, como nome, fabricante, modelo e número de série.

### <a name="accessory-service-types"></a>Tipos de serviço acessório

Os seguintes tipos de serviço estão disponíveis por meio de `HMServiceType` enum:

- **AccessoryInformation** -fornece informações sobre o dispositivo determinado automação residencial (acessório).
- **AirQualitySensor** -define um sensor de qualidade de ar.
- **Bateria** -define o estado da bateria do acessório.
- **CarbonDioxideSensor** -define um Sensor de dióxido de carbono.
- **CarbonMonoxideSensor** -define um Sensor monóxido de carbono.
- **ContactSensor** -define um sensor de contato (como uma janela que está sendo aberto ou fechado).
- **Porta** -define um sensor de estado de porta (como aberta ou fechada).
- **Ventilador** -define um ventilador controlado remoto.
- **GarageDoorOpener** -define um portão.
- **HumiditySensor** -define um sensor de umidade.
- **LeakSensor** -define um sensor de vazamento (como para uma máquina de lavar ou Aquecedor de água quente).
- **Lâmpada** -define uma luz de maneira autônoma ou uma luz que faz parte de outro acessório (como um portão).
- **LightSensor** -define um sensor de luz.
- **LockManagement** -define um serviço que gerencia um bloqueio de porta automatizado.
- **LockMechanism** -define um bloqueio controlado remoto (como um bloqueio de porta).
- **MotionSensor** -define um sensor de movimento.
- **OccupancySensor** -define um sensor de ocupação.
- **Tomada** -define uma tomada de parede controlado remoto.
- **SecuritySystem** -define um sistema de segurança inicial.
- **StatefulProgrammableSwitch** -define uma opção programável que permanece em um estado oferecem uma vez disparado (como um comutador invertido).
- **StatelessProgrammableSwitch** -define uma opção programável que retorna ao estado inicial após ser disparado (como um botão).
- **SmokeSensor** -define um sensor de fumaça.
- **Alternar** -define uma chave liga/desliga, como um comutador de parede padrão.
- **Sensor de temperatura** -define um sensor de temperatura.
- **Termostato** -define um termostato inteligente usado para controlar um sistema HVAC.
- **Janela** -define uma janela automatizada cascata remotamente ser aberta ou fechada.
- **WindowCovering** -define uma janela controlada remotamente, abordando como persianas que podem ser abertas ou fechadas.

### <a name="displaying-service-information"></a>Exibindo informações de serviço

Depois que você carregar uma `HMAccessory` você pode consultar o indivíduo `HNService` objetos ele oferece e exibir essas informações para o usuário:

[![](homekit-images/accessory05.png "Exibindo informações de serviço")](homekit-images/accessory05.png#lightbox)

Você deve sempre deve verificar a `Reachable` propriedade de um `HMAccessory` antes de tentar trabalhar com ele. Um acessório pode ser inacessível o usuário não está dentro do intervalo do dispositivo ou se ele foi desconectado.

Depois que um serviço tiver sido selecionado, o usuário pode exibir ou modificar uma ou mais características desse serviço para monitorar ou controlar um dispositivo de determinada automação residencial.

<a name="Working-with-Characteristics" />

## <a name="working-with-characteristics"></a>Trabalhando com características

Cada `HMService` objeto pode conter uma coleção de `HMCharacteristic` objetos que podem fornecer informações sobre o estado do serviço (como uma porta que está sendo aberto ou fechado) ou permitir que o usuário ajustar um estado (como definir a cor de uma luz).

`HMCharacteristic` não só fornece informações sobre uma característica e seu estado, mas também fornece métodos para trabalhar com o estado por meio _metadados de característica_ (`HMCharacteristisMetadata`). Esses metadados podem fornecer as propriedades (como intervalos de valores mínimo e máximo) que são úteis ao exibir as informações para o usuário ou permitindo que eles modificar estados.

O `HMCharacteristicType` enum fornece um conjunto de valores de metadados de característica que pode ser definida ou modificada da seguinte maneira:

 - AdminOnlyAccess
 - AirParticulateDensity
 - AirParticulateSize
 - AirQuality
 - AudioFeedback
 - BatteryLevel
 - Brilho
 - CarbonDioxideDetected
 - CarbonDioxideLevel
 - CarbonDioxidePeakLevel
 - CarbonMonoxideDetected
 - CarbonMonoxideLevel
 - CarbonMonoxidePeakLevel
 - ChargingState
 - ContactState
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
 - PositionState
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
 - Versão

### <a name="working-with-a-characteristics-value"></a>Trabalhando com valor de uma característica

Para garantir que o aplicativo você tem o estado mais recente de uma característica de determinado, chame o `ReadValue` método da `HMCharacteristic` classe. Se o `err` propriedade não é `null`, ocorreu um erro e ele pode ou não pode ser apresentado ao usuário.

A característica `Value` propriedade contém o estado atual da característica determinado como uma `NSObject`e como tal, não pode ser trabalhado diretamente no C#.

Para ler o valor, a seguinte classe auxiliar foi adicionada para o **HomeKitIntro** aplicativo de exemplo:

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

A linha acima converte o valor em uma `float` que pode ser usado no Xamarin C# código.

Para modificar uma `HMCharacteristic`, chame seu `WriteValue` método e encapsular o novo valor em um `NSObject.FromObject` chamar. Por exemplo:

```csharp
Characteristic.WriteValue(NSObject.FromObject(value),(err) =>{
    // Was there an error?
    if (err!=null) {
        // Yes, inform user
        AlertView.PresentOKAlert("Update Error",err.LocalizedDescription,Controller);
    }
});
```

Se o `err` propriedade não é `null`, um erro ocorreu e deve ser apresentado ao usuário.

### <a name="testing-characteristic-value-changes"></a>Testar as alterações de valor de característica

Ao trabalhar com `HMCharacteristics` e Acessórios simulados, as modificações a `Value` propriedade pode ser monitorada dentro do simulador de Acessórios do HomeKit.

Com o **HomeKitIntro** aplicativo em execução no Hardware do dispositivo, as alterações ao valor de uma característica de iOS reais deve ser visto quase instantaneamente no simulador de Acessórios do HomeKit. Por exemplo, alterando o estado de uma luz no aplicativo do iOS:

[![](homekit-images/test01.png "Alterando o estado de uma luz em um aplicativo iOS")](homekit-images/test01.png#lightbox)

Deve alterar o estado da luz no simulador de Acessórios do HomeKit. Se o valor não for alterado, verifique o estado da mensagem de erro durante a gravação de novos valores de característica e certifique-se de que o acessório é ainda pode ser acessado.

## <a name="advanced-homekit-features"></a>Recursos avançados do HomeKit

Este artigo abordou os recursos básicos necessários para trabalhar com os acessórios do HomeKit em um aplicativo xamarin. IOS. No entanto, há vários recursos avançados do HomeKit que não são abordados nesta introdução:

- **Salas** -Acessórios do HomeKit habilitada podem, opcionalmente, organizados em salas pelo usuário final. Isso permite que HomeKit presentes Acessórios de forma que seja fácil de entender e trabalhar com o usuário. Para obter mais informações sobre a criação e manutenção de ambientes, consulte da Apple [HMRoom](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMRoom_Class/index.html#//apple_ref/occ/cl/HMRoom) documentação.
- **As zonas** -salas, opcionalmente, podem ser organizados em zonas pelo usuário final. Uma zona se refere a uma coleção de ambientes nos quais o usuário pode tratar como uma única unidade. Por exemplo: Quartinho, Downstairs ou porão. Novamente, isso permite que o HomeKit apresentar e trabalhar com os acessórios de uma maneira que faça sentido para o usuário final. Para obter mais informações sobre como criar e manter as zonas, consulte da Apple [HMZone](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMZone_Class/index.html#//apple_ref/occ/cl/HMZone) documentação.
- **Ações e ação define** -ações modificar características de acessório do serviço e podem ser agrupadas em conjuntos. Conjuntos de ações atuam como scripts para controlar um grupo de Acessórios e coordenar suas ações. Por exemplo, um script de "Assistir TV" talvez feche as janelas, dim as luzes e ativar a televisão e seu sistema de som. Para obter mais informações sobre como criar e manter conjuntos de ações e ações, consulte da Apple [HMAction](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMAction_Class/index.html#//apple_ref/occ/cl/HMAction) e [HMActionSet](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMActionSet_Class/index.html#//apple_ref/occ/cl/HMActionSet) documentação.
- **Gatilhos** – um gatilho pode ativar um ou mais ação definida quando um determinado conjunto de condições foram atendidas. Por exemplo, ative a luz portch e bloquear todas as portas externas quando fica escuro fora. Para obter mais informações sobre como criar e manter os gatilhos, consulte da Apple [HMTrigger](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMTrigger_Class/index.html#//apple_ref/occ/cl/HMTrigger) documentação.

Como esses recursos usam as mesmas técnicas apresentadas acima, eles devem ser fáceis de implementar pelo seguinte da Apple [guia HomeKitDeveloper](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html), [diretrizes de Interface de usuário do HomeKit](https://developer.apple.com/homekit/ui-guidelines/) e [ Referência de estrutura HomeKit](https://developer.apple.com/library/ios/home_kit_framework_ref).

## <a name="homekit-app-review-guidelines"></a>Diretrizes de revisão de aplicativo do HomeKit

Antes de enviar um HomeKit habilitado aplicativo xamarin. IOS no iTunes Connect para lançamento na iTunes App Store, certifique-se de que você siga as diretrizes da Apple para aplicativos do HomeKit habilitado:

 - Objetivo de principal do aplicativo _deve_ ser automação residencial se usando a estrutura HomeKit.
 - Texto de marketing do aplicativo deve notificar os usuários que HomeKit está sendo usado e eles devem fornecer uma política de privacidade.
 - Coleta de informações do usuário ou usar o HomeKit para publicidade é estritamente proibida.

Completo, examine as diretrizes, consulte da Apple [diretrizes de examinar da App Store](https://developer.apple.com/app-store/review/guidelines/).

## <a name="whats-new-in-ios-9"></a>O que há de novo no iOS 9

Apple fez as seguintes alterações e adições ao HomeKit para iOS 9:

- **Manutenção de objetos existentes** - se de um acessório existente é modificado, o Gerenciador de início (`HMHomeManager`) irá informá-lo do item específico que foi modificado.
- **Identificadores persistentes** -todas as classes relevantes do HomeKit agora incluem um `UniqueIdentifier` propriedade para identificar exclusivamente um determinado item entre HomeKit habilitada aplicativos (ou instâncias do mesmo aplicativo).
- **Gerenciamento de usuário** -adicionado a um controlador de exibição interno para fornecer gerenciamento de usuários sobre os usuários que têm acesso aos dispositivos HomeKit na casa do usuário primário.
- **Recursos de usuário** - HomeKit usuários agora têm um conjunto de privilégios que controlam quais funções eles são capazes de usar o HomeKit e HomeKit habilitado Acessórios. Seu aplicativo só deve exibir recursos relevantes para o usuário atual. Por exemplo, um somente os administradores devem ser capaz de manter a outros usuários.
- **Predefinidas cenas** -cenas predefinidas foram criadas para quatro eventos comuns que ocorrem para o usuário médio do HomeKit: Começar a trabalhar, deixe, retornar, vá para a base. Esses cenas predefinidas não podem ser excluídas de uma casa.
- **Nos bastidores e Siri** -Siri tem mais suporte para cenas no iOS 9 e pode reconhecem o nome de qualquer cena definido em HomeKit. Um usuário pode executar uma cena falando seu nome para o Siri.
- **Categorias de acessório** -um conjunto de categorias predefinidas foi adicionado a todos os acessórios e ajuda a identificar o tipo de acessório que está sendo adicionado para uma casa ou trabalhado dentro de seu aplicativo. Essas novas categorias estão disponíveis durante a configuração de acessório.
- **Suporte de Apple Watch** - HomeKit agora está disponível para o watchOS e o Apple Watch será capaz de controle de dispositivos habilitados para HomeKit sem um iPhone que está sendo perto do relógio. HomeKit para watchOS suporta os seguintes recursos: Exibindo casas, controlando os acessórios e em execução nos bastidores.
- **Novo tipo de gatilho de evento** - além dos gatilhos do tipo de temporizador com suporte no iOS 8, iOS 9 agora dá suporte a gatilhos de evento com base no estado de acessório (por exemplo, dados de sensor) ou localização geográfica. Usam gatilhos de evento `NSPredicates` para definir condições para sua execução.
- **Acesso remoto** -com o acesso remoto, o usuário agora é capaz de controle de seu HomeKit habilitado Home Acessórios de automação quando estiverem fora da casa em um local remoto. No iOS 8, isso tinha suporte apenas se o usuário tivesse uma geração 3ª Apple TV em casa. No iOS 9, essa limitação foi eliminada e acesso remoto tem suporte por meio do iCloud e o protocolo de Acessórios do HomeKit (HAP).
- **Novas capacidades de baixa energia BLE (Bluetooth)** -HomeKit agora dá suporte a mais tipos de acessórios que podem se comunicar por meio do protocolo de baixa energia BLE (Bluetooth). Usando o encapsulamento do HAP seguro, um acessório do HomeKit pode expor outro acessório do Bluetooth por Wi-Fi (se ele está fora do intervalo de Bluetooth). No iOS 9, BLE Acessórios tem suporte completo para notificações e metadados.
- **Novas categorias de acessório** -Apple adicionadas as seguintes novas categorias de acessório no iOS 9: Persianas, se portas e Windows, sistemas de alarme, sensores e Switches programáveis.

Para obter mais informações sobre os novos recursos do HomeKit no iOS 9, consulte da Apple [índice HomeKit](https://developer.apple.com/homekit/) e [o que há de novo no HomeKit](https://developer.apple.com/videos/wwdc/2015/?id=210) vídeo.

## <a name="summary"></a>Resumo

Este artigo apresentou a estrutura de automação residencial HomeKit da Apple. Ele mostrou como instalar e configurar dispositivos de teste usando o simulador de Acessórios do HomeKit e como criar um aplicativo simples do xamarin. IOS para descobrir, comuniquem e controlar os dispositivos de automação residencial usando HomeKit.



## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [O que há de novo no iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guia HomeKitDeveloper](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [Diretrizes de Interface do usuário do HomeKit](https://developer.apple.com/homekit/ui-guidelines/)
- [Referência de estrutura HomeKit](https://developer.apple.com/library/ios/home_kit_framework_ref)
