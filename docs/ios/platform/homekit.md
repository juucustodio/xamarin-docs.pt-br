---
title: HomeKit em xamarin
description: HomeKit é a estrutura da Apple para controlar os dispositivos de automação inicial. Este artigo apresenta HomeKit e abrange Configurando Acessórios de teste no simulador acessório HomeKit e gravar um aplicativo simples do xamarin para interagir com esses Acessórios.
ms.prod: xamarin
ms.assetid: 90C0C553-916B-46B1-AD52-1E7332792283
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 0dfc6e9ba5098df66a72292d6c8b89ea1bbd1f97
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787455"
---
# <a name="homekit-in-xamarinios"></a>HomeKit em xamarin

_HomeKit é a estrutura da Apple para controlar os dispositivos de automação inicial. Este artigo apresenta HomeKit e abrange Configurando Acessórios de teste no simulador acessório HomeKit e gravar um aplicativo simples do xamarin para interagir com esses Acessórios._

[![](homekit-images/accessory01.png "Aplicativo habilitado para um exemplo HomeKit")](homekit-images/accessory01.png#lightbox)

Apple introduziu HomeKit no iOS 8 como uma maneira de integrar vários dispositivos de automação inicial de fornecedores de uma variedade de perfeitamente em uma unidade única e coerente. Promovendo um protocolo comum para a descoberta, configuração e controle de dispositivos de automação inicial HomeKit permite que os dispositivos de fornecedores não relacionados para trabalhar juntos, tudo sem a necessidade de coordenar os esforços de fornecedores individuais.

Com HomeKit, você pode criar um aplicativo xamarin que controla a qualquer dispositivo HomeKit habilitado sem o uso de fornecedor fornecidos APIs ou aplicativos. Usando HomeKit, você pode fazer o seguinte:

- Descubra novos dispositivos de automação inicial HomeKit habilitado e adicioná-los para um banco de dados que será mantido em todos os dispositivos do iOS do usuário.
- Instalar, configurar, exibir e controlar qualquer dispositivo no HomeKit _início banco de dados de configuração_.
- Se comunicar com qualquer dispositivo HomeKit pré-configurado e comandos para executar ações individuais ou trabalham em conjunto, como ativar a todas as luzes na cozinha.

Além de servir dispositivos no início configuração de banco de dados para aplicativos HomeKit habilitado, HomeKit fornece acesso a comandos de voz Siri. Devido a uma configuração de HomeKit adequadamente configurada, o usuário pode emitir comandos de voz, como "Siri, ative as luzes na sala de estar."

<a name="Home-Configuration-Database" />

## <a name="the-home-configuration-database"></a>O banco de dados de configuração inicial

HomeKit organiza todos os dispositivos de automação em um local específico em uma coleção de início. Esta coleção fornece uma maneira de agrupar seus dispositivos de automação inicial unidades logicamente organizadas com rótulos significativos, legível ao usuário.

A coleção de início é armazenada em um início configuração de banco de dados que serão automaticamente sincronizados e de backup em todos os dispositivos do iOS do usuário. HomeKit fornece as seguintes classes para trabalhar com o banco de dados do início configuração:

- `HMHome` -Este é o contêiner de nível superior que contém todas as informações e configurações para todos os dispositivos de automação inicial em um único local físico (por exemplo. uma única família residência). O usuário pode ter mais de uma residência, como sua principal inicial e uma casa de férias. Ou eles podem ter diferentes "contém" na mesma propriedade, como a casa principal e uma casa de convidado sobre a garagem. De qualquer forma, pelo menos um `HMHome` objeto _deve_ ser configurado e armazenados antes de qualquer outra informação HomeKit pode ser inserida.
- `HMRoom` -While opcional, um `HMRoom` permite ao usuário definir salas específicas dentro de uma casa (`HMHome`), como: cozinha, banheiro, garagem ou sala de estar. O usuário pode agrupar todos os dispositivos de automação inicial em um local específico em sua casa em um `HMRoom` e agir sobre eles, como uma unidade. Por exemplo, solicitando Siri para desativar as luzes garagem.
- `HMAccessory` -Isso representa um indivíduo, HomeKit físico habilitado o dispositivo de automação que foi instalado no residência do usuário (como termostato inteligente). Cada `HMAccessory` é atribuído a um `HMRoom`. Se o usuário não tiver configurado qualquer salas, HomeKit atribui Acessórios a uma sala padrão especial.
- `HMService` -Representa um serviço fornecido por um determinado `HMAccessory`, como o estado ativado/desativado de clara ou sua cor (se há suporte para alteração de cor). Cada `HMAccessory` pode ter mais de um serviço, como um privilégio portão que também inclui uma luz. Além disso, um determinado `HMAccessory` pode ter serviços, como atualização de firmware, que estão fora do controle de usuário.
- `HMZone` -Permite que o usuário agrupar um conjunto de `HMRoom` objetos em zonas lógicas, como quartinho, Downstairs ou porão. Embora opcional, isso permite interações como pedindo Siri para ativar todos da luz downstairs off.

<a name="Provisioning-a-HomeKit-App" />

## <a name="provisioning-a-homekit-app"></a>Provisionamento de um aplicativo de HomeKit

Por causa dos requisitos de segurança impostos pelo HomeKit, um aplicativo xamarin que usa a estrutura de HomeKit deve ser configurado corretamente no Portal do desenvolvedor Apple e no arquivo de projeto xamarin.

Faça o seguinte:

1. Faça logon na [Portal do desenvolvedor Apple](http://developer.apple.com).
2. Clique em **certificados, identificadores e perfis**.
3. Se você ainda não fez isso, clique em **identificadores** e crie uma ID para seu aplicativo (por exemplo, `com.company.appname`), ou editar sua ID existente.
4. Certifique-se de que o **HomeKit** serviço foi verificado para a ID especificada: 

    [![](homekit-images/provision01.png "Habilitar o serviço HomeKit da identificação fornecida")](homekit-images/provision01.png#lightbox)
5. Salve as alterações.
4. Clique em **perfis de provisionamento** > **desenvolvimento** e criar um novo desenvolvimento provisionamento de perfil para seu aplicativo: 

    [![](homekit-images/provision02.png "Criar um novo perfil para o aplicativo de provisionamento de desenvolvimento")](homekit-images/provision02.png#lightbox)
5. Baixe e instale o novo perfil de provisionamento ou use Xcode para baixar e instalar o perfil.
6. Edite as opções de projeto xamarin e certifique-se de que você está usando o perfil de provisionamento que você acabou de criar: 

    [![](homekit-images/provision03.png "Selecione o perfil de provisionamento que acabou de criar")](homekit-images/provision03.png#lightbox)
7. Em seguida, edite o **Info. plist** de arquivo e certifique-se de que você está usando a ID do aplicativo que foi usado para criar o perfil de provisionamento: 

    [![](homekit-images/provision04.png "Definir a ID de aplicativo ")](homekit-images/provision04.png#lightbox)
8. Por fim, edite seu **Entitlements.plist** de arquivos e certifique-se de que o **HomeKit** direito foi selecionado: 

    [![](homekit-images/provision05.png "Habilitar o direito de HomeKit")](homekit-images/provision05.png#lightbox)
9. Salve as alterações para todos os arquivos.

Com essas configurações em vigor, o aplicativo está pronto para acessar as APIs de Framework HomeKit. Para obter informações detalhadas sobre o provisionamento, consulte nosso [aprovisionamento](~/ios/get-started/installation/device-provisioning/index.md) e [de provisionamento do seu aplicativo](~/ios/get-started/installation/device-provisioning/index.md) guias.

> [!IMPORTANT]
> Testar um aplicativo HomeKit habilitado requer um dispositivo iOS real que foi configurado corretamente para o desenvolvimento. HomeKit não pode ser testada a partir do simulador de iOS.

## <a name="the-homekit-accessory-simulator"></a>O simulador HomeKit acessório

Para fornecer uma maneira de testar todos os dispositivos de automação inicial possíveis e serviços, sem a necessidade de ter um dispositivo físico, Apple criado o _HomeKit acessório simulador_. Usando essa simulador, você pode instalar e configurar dispositivos HomeKit virtuais.

### <a name="installing-the-simulator"></a>Instalando o simulador

Apple fornece o simulador de acessório HomeKit como um download separado do Xcode, portanto, será necessário instalá-lo antes de continuar.

Faça o seguinte:

1. Em um navegador da web, visite [Downloads para desenvolvedores da Apple](https://developer.apple.com/download/more/?name=for%20Xcode)
2. Baixe o **ferramentas adicionais para Xcode xxx** (onde xxx é a versão do Xcode que você instalou): 

    [![](homekit-images/simulator01.png "Baixar as ferramentas adicionais para Xcode")](homekit-images/simulator01.png#lightbox)
3. Abrir a imagem de disco e instalar as ferramentas no seu **aplicativos** directory.

Com o simulador de acessório HomeKit instalado, Acessórios virtuais podem ser criados para teste.

### <a name="creating-virtual-accessories"></a>Criando Acessórios Virtual

Para iniciar o simulador de acessório HomeKit e criar algumas Acessórios virtuais, faça o seguinte:

1. Na pasta de aplicativos, inicie o simulador de acessório HomeKit: 

    [![](homekit-images/simulator02.png "O simulador HomeKit acessório")](homekit-images/simulator02.png#lightbox)
2. Clique o **+** botão e selecione **novo acessório...** : 

    [![](homekit-images/simulator03.png "Adicionar um novo acessório")](homekit-images/simulator03.png#lightbox)
3. Preencha as informações sobre o novo Acessórios e clique no **concluir** botão: 

    [![](homekit-images/simulator04.png "Preencha as informações sobre o novo acessório")](homekit-images/simulator04.png#lightbox)
4. Clique o **Adicionar serviço.** botão e selecione um tipo de serviço na lista suspensa: 

    [![](homekit-images/simulator05.png "Selecione um tipo de serviço na lista suspensa")](homekit-images/simulator05.png#lightbox)
5. Forneça um **nome** para o serviço e clique no **concluir** botão: 

    [![](homekit-images/simulator06.png "Insira um nome para o serviço")](homekit-images/simulator06.png#lightbox)
6. Você pode fornecer características opcionais para um serviço clicando o **adicionar característica** botão e configurando as configurações necessárias: 

    [![](homekit-images/simulator07.png "Configurando as configurações necessárias")](homekit-images/simulator07.png#lightbox)
7. Repita as etapas acima para criar um de cada tipo de dispositivo virtual automação inicial que dá suporte a HomeKit.

Com alguns exemplo virtual HomeKit Acessórios criado e configurado, você pode consumir e controlar esses dispositivos de seu aplicativo xamarin.

## <a name="configuring-the-infoplist-file"></a>Configurando o arquivo Info. plist

Novo para iOS 10 (e superior), o desenvolvedor precisa adicionar o `NSHomeKitUsageDescription` chave para o aplicativo `Info.plist` de arquivo e forneça uma cadeia de caracteres declarando por que o aplicativo deseja acessar o banco de dados do usuário HomeKit. Essa cadeia de caracteres será apresentada para o usuário na primeira vez que executar o aplicativo:

[![](homekit-images/info01.png "A caixa de diálogo de permissão HomeKit")](homekit-images/info01.png#lightbox)

Para definir essa chave, faça o seguinte:

1. Clique duas vezes o `Info.plist` arquivo o **Gerenciador de soluções** para abri-lo para edição.
2. Na parte inferior da tela, alterne para o **fonte** exibição.
3. Adicionar um novo **entrada** à lista.
4. Na lista suspensa, selecione **privacidade - descrição do uso de HomeKit**: 

    [![](homekit-images/info02.png "Selecione privacidade - descrição do uso de HomeKit")](homekit-images/info02.png#lightbox)
5. Insira uma descrição de por que o aplicativo deseja acessar o banco de dados do usuário HomeKit: 

    [![](homekit-images/info03.png "Insira uma descrição")](homekit-images/info03.png#lightbox)
6. Salve as alterações no arquivo.

> [!IMPORTANT]
> Falha ao definir o `NSHomeKitUsageDescription` chave no `Info.plist` arquivo resultará no aplicativo _silenciosamente falhando_ (que está sendo fechado pelo sistema em tempo de execução) sem erro quando executado no iOS 10 (ou superior).

## <a name="connecting-to-homekit"></a>Conectando-se a HomeKit

Para se comunicar com HomeKit, seu aplicativo xamarin precisa primeiro criar uma instância do `HMHomeManager` classe. O Gerenciador de início é o ponto de entrada central HomeKit e é responsável por fornecer uma lista de residências disponíveis, atualizar e manter essa lista e retornando o usuário _inicial primário_.

O `HMHome` objeto contém todas as informações sobre uma casa dê incluindo salas, grupos ou zonas que pode conter, juntamente com quaisquer Acessórios de automação inicial que foram instalados. Antes de todas as operações podem ser executadas em HomeKit, pelo menos um `HMHome` deve ser criado e atribuído como o primário.

Seu aplicativo é responsável por verificando se existe uma casa primário e criar e atribuir uma se não estiver.

### <a name="adding-a-home-manager"></a>Adicionar um Gerenciador de base

Para adicionar o reconhecimento de HomeKit para um aplicativo xamarin, edite o **appdelegate. CS** arquivo para editá-lo e torná-lo a aparência a seguir:

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

Quando o aplicativo for executado pela primeira vez, o usuário será solicitado se deseja permiti-lo acessar suas informações de HomeKit:

[![](homekit-images/home01.png "O usuário será solicitado se deseja permiti-lo acessar suas informações de HomeKit")](homekit-images/home01.png#lightbox)

Se o usuário responder **Okey**, em seguida, o aplicativo poderá trabalhar com seus acessórios HomeKit caso contrário, ele não será e todas as chamadas para HomeKit falhará com um erro.

Com o Gerenciador Home no lugar, em seguida o aplicativo precisará ver se uma casa primário tiver sido configurada e se não, fornecem uma maneira para o usuário criar e atribuir um.

### <a name="accessing-the-primary-home"></a>Acessando a página inicial do primário

Como mencionado acima, uma casa primário deve ser criada e configurada antes de HomeKit está disponível e é responsabilidade do aplicativo para fornecer uma maneira para que o usuário criar e atribuir uma casa primário se ainda não existir.

Quando seu aplicativo é iniciado pela primeira vez ou retorna do plano de fundo, ele precisa monitorar o `DidUpdateHomes` evento o `HMHomeManager` classe para verificar a existência de uma casa primário. Se não existir, ele deverá fornecer uma interface do usuário criar uma.

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

Quando o Gerenciador de início faz uma conexão HomeKit, o `DidUpdateHomes` evento será disparado, qualquer casas existentes serão carregadas para a coleção do gerente de casas e a página inicial do primário será carregada, se disponível.

### <a name="adding-a-primary-home"></a>Adicionando uma casa primário

Se o `PrimaryHome` propriedade do `HMHomeManager` é `null` após um `DidUpdateHomes` eventos, você precisa fornecer uma maneira para que o usuário criar e atribuir uma casa primário antes de continuar.

Normalmente, o aplicativo apresentará um formulário para o usuário para uma nova casa que, em seguida, é passada para o Gerenciador de base para configurar como o primário. Para o **HomeKitIntro** criado no Designer de IOS e chamado pelo aplicativo de exemplo, uma exibição restrita a `AddHomeSegue` atender a interface principal do aplicativo.

Ele fornece um campo de texto para o usuário insira um nome para a nova casa e um botão para adicionar a página inicial. Quando o usuário toca o **adicionar Home** botão, o código a seguir chama o Gerenciador de base para adicionar a página inicial:

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

O `AddHome` método tentará criar nova casa e retorná-lo para a rotina de retorno de chamada fornecido. Se o `error` propriedade não é `null`, ocorreu um erro e ele deve ser apresentado ao usuário. Os erros mais comuns são causados por um nome de base não-exclusivo ou o Gerenciador de base não conseguir se comunicar com HomeKit.

Se a página inicial foi criada com êxito, você precisa chamar o `UpdatePrimaryHome` método para definir a página inicial do novo como o primário. Novamente, se o `error` propriedade não é `null`, ocorreu um erro e ele deve ser apresentado ao usuário.

Você também deve monitorar o início do Gerenciador de `DidAddHome` e `DidRemoveHome` eventos e atualização de interface do usuário do aplicativo conforme necessário.

> [!IMPORTANT]
> O `AlertView.PresentOKAlert` método usado no código de exemplo acima é uma classe auxiliar no aplicativo HomeKitIntro que torna o trabalho com o iOS alertas mais fácil.


## <a name="finding-new-accessories"></a>Localizando novos acessórios

Depois de uma casa primária foi definida ou carregada do Gerenciador de base, seu aplicativo xamarin pode chamar o `HMAccessoryBrowser` para localizar quaisquer novos acessórios de automação inicial e adicioná-los para uma casa.

Chamar o `StartSearchingForNewAccessories` método para iniciar a procura por novos acessórios e `StopSearchingForNewAccessories` método quando terminar.

> [!IMPORTANT]
> `StartSearchingForNewAccessories` não deve ser deixado em execução por longos períodos de tempo, porque isso afetará negativamente vida útil da bateria e o desempenho do dispositivo iOS. Apple sugere chamada `StopSearchingForNewAccessories` após um minuto ou pesquisar somente quando a interface de usuário do acessório localizar é apresentada ao usuário.

O `DidFindNewAccessory` evento será chamado quando forem descobertos novos acessórios e eles serão adicionados para o `DiscoveredAccessories` lista no navegador acessório.

O `DiscoveredAccessories` lista contém uma coleção de `HMAccessory` objetos que definem um dê HomeKit habilitado dispositivo de automação inicial e seus serviços disponíveis, como luzes ou controle de acesso à garagem.

Depois que o novo acessório foi encontrado, ele deve ser apresentado ao usuário e portanto pode selecioná-lo e adicioná-lo para uma casa. Exemplo:

[![](homekit-images/accessory01.png "Localizando um novo acessório")](homekit-images/accessory01.png#lightbox)

Chamar o `AddAccessory` para adicionar o acessório selecionado à coleção do início. Por exemplo:

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

[![](homekit-images/accessory02.png "Insira o código de instalação para o dispositivo a ser adicionado")](homekit-images/accessory02.png#lightbox)

No simulador acessório HomeKit esse número pode ser encontrado sob o **código de instalação** campo:

[![](homekit-images/accessory03.png "O campo de código de instalação no simulador HomeKit acessório")](homekit-images/accessory03.png#lightbox)

Para Acessórios HomeKit reais, o código de instalação será impressa ou em um rótulo no dispositivo, na caixa de produto ou no manual do usuário do acessório.

Você deve monitorar o navegador acessório `DidRemoveNewAccessory` interface de eventos e atualize o usuário para remover um acessório da lista disponível depois que o usuário adicionou à sua coleção início.

## <a name="working-with-accessories"></a>Trabalhando com Acessórios

Uma vez uma casa primário e foi estabelecida e Acessórios foram adicionados a ele, você pode apresentar uma lista de Acessórios (e, opcionalmente, salas) para trabalhar com o usuário.

O `HMRoom` objeto contém todas as informações sobre um determinado espaço e os acessórios que pertencem a ele. Salas, opcionalmente, podem ser organizadas em uma ou mais zonas. Um `HMZone` contém todas as informações sobre uma determinada região e todas as salas que pertencem a ele.

Para este exemplo, é será manter as coisas simples e trabalhar com Acessórios de uma página inicial diretamente, em vez de organizando-os em salas ou zonas.

O `HMHome` objeto contém uma lista de acessório atribuído que pode ser apresentada ao usuário no seu `Accessories` propriedade. Por exemplo:

[![](homekit-images/accessory04.png "Um acessório de exemplo")](homekit-images/accessory04.png#lightbox)

Formulário aqui, o usuário pode selecionar um acessório determinado e trabalhar com os serviços que ele oferece.

## <a name="working-with-services"></a>Trabalhar com os serviços

Quando o usuário interage com um determinado dispositivo habilitado automação residencial HomeKit, é normalmente por meio dos serviços que ele oferece. O `Services` propriedade o `HMAccessory` classe contém uma coleção de `HMService` oferece de um dispositivo de objetos que definem os serviços.

Os serviços são coisas como luzes, termostatos, garagem abridores de porta, comutadores ou bloqueios. Alguns dispositivos (como um portão) fornecem mais de um serviço, como a luz e a capacidade de abrir ou fechar uma porta.

Além dos serviços específicos que fornece um acessório determinado, cada acessório contém um `Information Service` que define as propriedades, como nome, fabricante, modelo e número de série.

### <a name="accessory-service-types"></a>Tipos de serviço de acessório

Os tipos de serviço a seguir estão disponíveis por meio de `HMServiceType` enum:

- **AccessoryInformation** -fornece informações sobre o dispositivo de automação inicial especificado (Acessórios).
- **AirQualitySensor** -define um sensor de qualidade do ar.
- **Bateria** -define o estado da bateria do acessório.
- **CarbonDioxideSensor** -define um Sensor de dióxido de carbono.
- **CarbonMonoxideSensor** -define um Sensor monóxido de carbono.
- **ContactSensor** -define um sensor de contato (por exemplo, uma janela que está sendo aberto ou fechado).
- **Porta** -define um sensor de estado de porta (como aberto ou fechado).
- **Ventilador** -define um ventilador controlado remoto.
- **GarageDoorOpener** -define um portão.
- **HumiditySensor** -define um sensor de umidade.
- **LeakSensor** -define um sensor de escape (como um acesso aquecedor ou da máquina de lavar).
- **Lâmpada** -define uma luz autônomo ou clara que faz parte de outro acessório (como um portão).
- **LightSensor** -define um sensor de luz.
- **LockManagement** -define um serviço que gerencia um bloqueio de porta automatizada.
- **LockMechanism** -define um bloqueio remoto controlado (como um bloqueio de porta).
- **MotionSensor** -define um sensor de movimento.
- **OccupancySensor** -define um sensor de ocupação.
- **Tomada** -define uma tomada controlado remoto.
- **SecuritySystem** -define um sistema de segurança de base.
- **StatefulProgrammableSwitch** -define uma opção programável que permanece em um estado proporcionam uma vez disparado (como um comutador invertido).
- **StatelessProgrammableSwitch** -define uma opção programável que retorna ao estado inicial após a que está sendo disparado (como um botão).
- **SmokeSensor** -define um sensor superficial.
- **Alternar** -define uma chave liga/desliga, como um comutador de parede padrão.
- **Sensor de temperatura** -define um sensor de temperatura.
- **Termostato** -define termostato inteligente usado para controlar um sistema HVAC.
- **Janela** -define uma janela automatizada açúcar remotamente ser aberta ou fechada.
- **WindowCovering** -define uma janela controlada remotamente que abrangem como persianas que podem ser abertas ou fechadas.

### <a name="displaying-service-information"></a>Exibindo informações de serviço

Depois de carregar um `HMAccessory` você pode consultar o indivíduo `HNService` objetos ele oferece e exibir essas informações para o usuário:

[![](homekit-images/accessory05.png "Exibindo informações de serviço")](homekit-images/accessory05.png#lightbox)

Você deve sempre deve verificar o `Reachable` propriedade de um `HMAccessory` antes de tentar trabalhar com ele. Um acessório pode ser inacessível o usuário não está no intervalo do dispositivo ou se foi desconectado.

Quando um serviço tiver sido selecionado, o usuário pode exibir ou modificar uma ou mais características desse serviço monitorar ou controlar um dispositivo de automação inicial especificado.

<a name="Working-with-Characteristics" />

## <a name="working-with-characteristics"></a>Trabalhando com características

Cada `HMService` objeto pode conter uma coleção de `HMCharacteristic` objetos que podem fornecer informações sobre o estado do serviço (como uma porta que está sendo aberto ou fechado) ou permitir que o usuário ajustar um estado (como definir a cor de uma luz).

`HMCharacteristic` não só fornece informações sobre uma característica e seu estado, mas também fornece métodos para trabalhar com o estado via _característica metadados_ (`HMCharacteristisMetadata`). Esses metadados podem fornecer propriedades (como intervalos de valores mínimo e máximo) que são úteis para exibir informações para o usuário ou permitindo modificar estados.

O `HMCharacteristicType` enum fornece um conjunto de valores de metadados de característica que podem ser definidas ou modificadas da seguinte maneira:

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
 - logs
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
 - Saturação
 - número de série
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

Para garantir que seu aplicativo tem o estado mais recente de uma característica determinado, chame o `ReadValue` método o `HMCharacteristic` classe. Se o `err` propriedade não é `null`, ocorreu um erro e ele pode ou não pode ser apresentado ao usuário.

A característica `Value` propriedade contém o estado atual da característica fornecido como um `NSObject`e assim não pode ser editado diretamente em c#.

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

A linha acima converte o valor em uma `float` que pode ser usado no código do c# Xamarin.

Para modificar um `HMCharacteristic`, chame seu `WriteValue` método e colocar o novo valor em uma `NSObject.FromObject` chamar. Por exemplo:

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

### <a name="testing-characteristic-value-changes"></a>Testando alterações de valor de característica

Ao trabalhar com `HMCharacteristics` e Acessórios simulados, as modificações de `Value` propriedade pode ser monitorada no simulador HomeKit acessório.

Com o **HomeKitIntro** aplicativo em execução no Hardware do dispositivo, as alterações ao valor de uma característica do iOS real deve ser considerado quase instantaneamente, no simulador HomeKit acessório. Por exemplo, alterar o estado de uma luz no aplicativo do iOS:

[![](homekit-images/test01.png "Alterar o estado de uma luz em um aplicativo iOS")](homekit-images/test01.png#lightbox)

Deve alterar o estado da luz no simulador HomeKit acessório. Se o valor não for alterada, verifique o estado da mensagem de erro durante a gravação de novos valores de característica e certifique-se de que o acessório ainda estiver acessível.

## <a name="advanced-homekit-features"></a>Recursos avançados de HomeKit

Este artigo abordou os recursos básicos necessários para trabalhar com Acessórios HomeKit em um aplicativo xamarin. No entanto, há vários recursos avançados de HomeKit que não são abordados nesta introdução:

- **Salas** -HomeKit habilitado Acessórios pode, opcionalmente, organizados em salas pelo usuário final. Isso permite que HomeKit Acessórios presentes de uma maneira fácil de entender e trabalhar com o usuário. Para obter mais informações sobre como criar e manter salas, consulte da Apple [HMRoom](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMRoom_Class/index.html#//apple_ref/occ/cl/HMRoom) documentação.
- **Zonas** -salas, opcionalmente, podem ser organizados em zonas pelo usuário final. Uma zona se refere a uma coleção das salas em que o usuário pode tratar como uma única unidade. Por exemplo: quartinho, Downstairs ou porão. Novamente, isso permite que HomeKit apresentar e trabalhar com Acessórios de uma maneira que faça sentido para o usuário final. Para obter mais informações sobre como criar e manter zonas, consulte da Apple [HMZone](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMZone_Class/index.html#//apple_ref/occ/cl/HMZone) documentação.
- **Ações e ação define** -ações modificar serviço acessório características e podem ser agrupados em conjuntos. Conjuntos de ações atuam como scripts para controlar a um grupo de Acessórios e coordenar suas ações. Por exemplo, um script "Assistir TV" pode fechar as janelas, dim luzes e ativar televisão e seu sistema de som. Para obter mais informações sobre como criar e manter as ações e conjuntos de ações, consulte da Apple [HMAction](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMAction_Class/index.html#//apple_ref/occ/cl/HMAction) e [HMActionSet](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMActionSet_Class/index.html#//apple_ref/occ/cl/HMActionSet) documentação.
- **Gatilhos** - um gatilho pode ativar um ou mais ação definida quando um determinado conjunto de condições foram atendidas. Por exemplo, ative a luz portch e bloquear todas as portas externas quando obtém escuro fora. Para obter mais informações sobre como criar e manter gatilhos, consulte da Apple [HMTrigger](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMTrigger_Class/index.html#//apple_ref/occ/cl/HMTrigger) documentação.

Como esses recursos usam as mesmas técnicas apresentadas acima, eles deverão ser fácil de implementar a seguir da Apple [HomeKitDeveloper guia](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html), [diretrizes de Interface de usuário HomeKit](https://developer.apple.com/homekit/ui-guidelines/) e [ Referência do Framework HomeKit](https://developer.apple.com/library/ios/home_kit_framework_ref).

## <a name="homekit-app-review-guidelines"></a>Diretrizes de revisão de aplicativo HomeKit

Antes de enviar um HomeKit aplicativo xamarin habilitado para conectar-se de iTunes versão na iTunes App Store, certifique-se de que você siga as diretrizes da Apple para aplicativos HomeKit habilitado:

 - Objetivo principal do aplicativo _deve_ ser automação inicial se usando a estrutura HomeKit.
 - Texto de marketing do aplicativo deve notificar os usuários que HomeKit está sendo usado e que eles devem fornecer uma política de privacidade.
 - Coletando informações do usuário ou usando HomeKit para publicidade é proibida.

Para o completo diretrizes de revisão, consulte da Apple [diretrizes da App Store examine](https://developer.apple.com/app-store/review/guidelines/).

## <a name="whats-new-in-ios-9"></a>Novidades no iOS 9

Apple tem feitas as seguintes alterações e adições HomeKit para iOS 9:

- **Manutenção de objetos existentes** - se um acessório existente for modificado, o Gerenciador de início (`HMHomeManager`) informam do item específico que foi modificado.
- **Identificadores persistentes** -todas as classes de HomeKit relevantes agora incluem um `UniqueIdentifier` propriedade para identificar exclusivamente um determinado item entre HomeKit habilitada aplicativos (ou instâncias do mesmo aplicativo).
- **Gerenciamento de usuário** -adicionado a um controlador de modo de exibição interno para fornecer gerenciamento de usuário sobre os usuários que têm acesso aos dispositivos HomeKit em casa do usuário primário.
- **Recursos de usuário** - HomeKit usuários agora têm um conjunto de privilégios que controlam quais funções que eles sejam capazes de usar em HomeKit e HomeKit habilitado Acessórios. Seu aplicativo deve exibir apenas os recursos relevantes para o usuário atual. Por exemplo, um somente os administradores devem ser capaz de manter a outros usuários.
- **Predefinidos cenas** -cenas predefinidas foram criadas para quatro eventos comuns que ocorrem para o usuário HomeKit médio: começar, deixe, retornar, acesse a base. Esses cenas predefinidas não podem ser excluídas de uma casa.
- **Nos bastidores e Siri** -Siri tem mais suporte para cenas no iOS 9 e pode reconhecem o nome de qualquer cena definido em HomeKit. Um usuário pode executar uma cena falando seu nome para o Siri.
- **Categorias de acessório** -um conjunto de categorias predefinidas foi adicionado a todos os acessórios e ajuda a identificar o tipo de acessório que está sendo adicionado a uma página inicial ou trabalhado de dentro de seu aplicativo. Essas novas categorias estão disponíveis durante a configuração de acessório.
- **O suporte da Apple Watch** - HomeKit agora está disponível para watchOS e a Apple Watch poderá controlar dispositivos habilitados para HomeKit sem um iPhone sendo próximo inspeção. HomeKit para watchOS suporta os seguintes recursos: exibindo casas, controlando Acessórios e plano de execução.
- **Novo tipo de gatilho de evento** - além dos gatilhos timer tipo com suporte no iOS 8, iOS 9 agora dá suporte a disparadores de evento com base no estado de acessório (como dados de sensor) ou localização geográfica. Usam os disparadores de evento `NSPredicates` para definir condições para sua execução.
- **Acesso remoto** -com acesso remoto, o usuário agora é capaz de controlar seu HomeKit habilitado início Acessórios de automação quando estiverem fora da casa em um local remoto. No iOS 8, isso era compatível apenas se o usuário tinha uma geração 3º Apple TV em casa. No iOS 9, essa limitação é eliminada e acesso remoto não é suportado pelo iCloud e o protocolo de acessório HomeKit (HAP).
- **Novas habilidades de Bluetooth baixa energia (var)** -HomeKit agora dá suporte a mais tipos de acessórios que podem se comunicar por meio do protocolo de Bluetooth baixa energia (var). Usando o encapsulamento de HAP seguro, um acessório HomeKit pode expor outro acessório de Bluetooth por Wi-Fi (se ele está fora do intervalo de Bluetooth). No iOS 9, Bilitar Acessórios tem suporte completo para notificações e metadados.
- **Novas categorias de acessório** -Apple adicionadas as seguintes novas categorias de acessório no iOS 9: persianas, portas motorizada e Windows, sistemas de alarme, sensores e Switches programáveis.

Para obter mais informações sobre os novos recursos HomeKit no iOS 9, consulte da Apple [HomeKit índice](https://developer.apple.com/homekit/) e [o que há de novo no HomeKit](https://developer.apple.com/videos/wwdc/2015/?id=210) vídeo.

## <a name="summary"></a>Resumo

Este artigo apresentou a estrutura de automação inicial HomeKit da Apple. Ele mostrou como instalar e configurar dispositivos de teste usando o simulador de acessório HomeKit e como criar um aplicativo simples do xamarin para descobrir, se comunicar com e controlar os dispositivos de automação inicial usando HomeKit.



## <a name="related-links"></a>Links relacionados

- [Exemplos do iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [O que há de novo no iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guia HomeKitDeveloper](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [Diretrizes de Interface do usuário HomeKit](https://developer.apple.com/homekit/ui-guidelines/)
- [Referência HomeKit Framework](https://developer.apple.com/library/ios/home_kit_framework_ref)
