---
title: HealthKit no xamarin. IOS
description: Este documento descreve o HealthKit, uma estrutura introduzida no iOS 8 que fornece um repositório de dados centralizado, coordenado e seguro para informações relacionadas à integridade. Ele aborda como provisionar um aplicativo HealthKit e como escrever código que usa a estrutura HealthKit.
ms.prod: xamarin
ms.assetid: E3927A21-507C-43BA-A2AD-957716BA9B52
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 8dcb478b303c4c73f7e73dc018ad56b1301389c8
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830041"
---
# <a name="healthkit-in-xamarinios"></a>HealthKit no xamarin. IOS

Kit de integridade fornece um repositório de dados seguro para as informações do usuário relacionados à integridade. Aplicativos de Kit de integridade podem, com a permissão do usuário explícito, leitura e gravação para esse repositório de dados e receber notificações quando os dados pertinentes são adicionados. Os aplicativos podem apresentar os dados ou usuário pode usar o aplicativo de integridade fornecido da Apple para exibir um painel de todos os seus dados.

Porque os dados relacionados à integridade são tão confidenciais e crucial, Kit de integridade é fortemente tipada, com unidades de medida e uma associação explícita com o tipo de informação que está sendo registrada (por exemplo, o nível de glicose sangue ou frequência cardíaca). Além disso, o Kit de integridade de aplicativos devem usar direitos explícitos, deve solicitar acesso os determinados tipos de informações e o usuário deve conceder explicitamente o acesso ao aplicativo para esses tipos de dados.

Este artigo apresenta:

- Requisitos de segurança do Kit de integridade, incluindo o provisionamento de aplicativo e solicitando permissão do usuário para acessar o banco de dados do Kit de integridade;
- Sistema de tipos do Kit de integridade, o que minimiza a possibilidade de aplicar incorretamente ou interpretação incorreta de dados;
- Gravando no repositório de dados de integridade Kit compartilhados, todo o sistema.

Este artigo não abordará os tópicos mais avançados, como consultar o banco de dados, a conversão entre unidades de medida ou recebimento de notificações de novos dados.

Neste artigo, estamos criando um aplicativo de exemplo para registrar a taxa de coração do usuário:

[![](healthkit-images/image01.png "Um aplicativo de exemplo para registrar a taxa de coração de usuários")](healthkit-images/image01.png#lightbox)

## <a name="requirements"></a>Requisitos

A seguir é necessários para concluir as etapas apresentadas neste artigo:

- **Xcode 7 e iOS 8 (ou superior)** – Xcode e APIs do iOS mais recente da Apple precisa ser instalado e configurado no computador do desenvolvedor.
- **O Visual Studio para Mac ou Visual Studio** – a versão mais recente do Visual Studio para Mac deve ser instalada e configurada no computador do desenvolvedor.
- **iOS 8 (ou superior) dispositivo** – um dispositivo iOS executando a versão mais recente do iOS 8 ou superior para teste.

> [!IMPORTANT]
> Kit de integridade foi introduzido no iOS 8. Atualmente, o Kit de integridade não está disponível no simulador de iOS e a depuração requer conexão a um dispositivo iOS físico.




## <a name="creating-and-provisioning-a-health-kit-app"></a>Criar e provisionar um aplicativo do Kit de integridade
Antes de um aplicativo Xamarin iOS 8 pode usar a API de HealthKit, deve ser adequadamente configurada e provisionado. Esta seção abordará as etapas necessárias para configurar corretamente o seu aplicativo Xamarin.

Aplicativos de Kit de integridade exigem:

- Um explícito **ID do aplicativo**.
- Um **perfil de provisionamento** associado que explícito **ID do aplicativo** e com **integridade Kit** permissões.
- Uma `Entitlements.plist` com um `com.apple.developer.healthkit` propriedade do tipo `Boolean` definido como `Yes`.
- Uma `Info.plist` cujos `UIRequiredDeviceCapabilities` chave contém uma entrada com o `String` valor `healthkit`.
- O `Info.plist` também deve ter as entradas apropriadas de explicação de privacidade: uma `String` explicação para a chave `NSHealthUpdateUsageDescription` se o aplicativo está prestes a escrever dados e uma `String` explicação para a chave `NSHealthShareUsageDescription` se o aplicativo vai ler o Kit de integridade dados.

Para obter mais informações sobre o provisionamento de um aplicativo iOS, o [aprovisionamento](~/ios/get-started/installation/device-provisioning/index.md) artigo do Xamarin **Introdução** série descreve a relação entre o desenvolvedor certificados, IDs do aplicativo, Perfis de provisionamento e direitos de aplicativo.

<a name="explicit-appid" />

### <a name="explicit-app-id-and-provisioning-profile"></a>ID do aplicativo explícita e o perfil de provisionamento

A criação de um valor explícito **ID do aplicativo** e um apropriado **perfil de provisionamento** é feito dentro da Apple [Centro de desenvolvimento do iOS](https://developer.apple.com/devcenter/ios/index.action). 

As atuais **IDs do aplicativo** são listados dentro a [certificados, identificadores e perfis](https://developer.apple.com/account/ios/identifiers/bundle/bundleList.action) seção Centro de desenvolvimento. Muitas vezes, essa lista mostrará **identificação** valores de `*`, indicando que o **ID do aplicativo** - **nome** pode ser usado com qualquer número de sufixos. Tais *Wildcard App IDs* não pode ser usado com o Kit de integridade.
 
Para criar um explícito **ID do aplicativo**, clique no **+** botão no canto superior direito para levá-lo para o **registrar a ID do aplicativo do iOS** página:


[![](healthkit-images/image02.png "Registrando um aplicativo no Portal do desenvolvedor da Apple")](healthkit-images/image02.png#lightbox)

Conforme mostrado na imagem acima, após a criação de uma descrição do aplicativo, use o **ID do aplicativo explícita** seção para criar uma ID para seu aplicativo. No **serviços de aplicativos** seção, verifique **integridade Kit** no **habilitar serviços** seção.

Quando você terminar, pressione a **Continue** botão para registrar o **ID do aplicativo** em sua conta. Você será levado de volta o **certificados, identificadores e perfis** página. Clique em **perfis de provisionamento** levá-lo à lista de perfis de provisionamento atual e, em seguida, clique no **+** botão no canto superior direito para levá-lo para o **adicionar iOS Perfil de provisionamento** página. Selecione o **desenvolvimento de aplicativos iOS** opção e clique em **continuar** para obter o **selecione ID do aplicativo** página. Aqui, selecione explícito **ID do aplicativo** que você especificou anteriormente:


[![](healthkit-images/image03.png "Selecione a ID do aplicativo explícita")](healthkit-images/image03.png#lightbox)

Clique em **continuar** e de trabalho por meio das telas restantes, onde é possível especificar seu **certificados de desenvolvedor**, **dispositivos**e um **nome** para este **perfil de provisionamento**:

[![](healthkit-images/image04.png "Gerando o perfil de provisionamento")](healthkit-images/image04.png#lightbox)

Clique em **gerar** e aguardar a criação de seu perfil. Baixe o arquivo e clique duas vezes nele para instalar no Xcode. Você pode confirmar a instalação de TI em **Xcode > Preferências > contas > Exibir detalhes...** Você deve ver seu perfil de provisionamento just-instalado, e ele deve ter o ícone para o Kit de integridade e outros serviços especiais no seu **direitos** linha:

[![](healthkit-images/image05.png "Exibindo o perfil no Xcode")](healthkit-images/image05.png#lightbox)

<a name="associating-appid" />

### <a name="associating-the-app-id-and-provisioning-profile-with-your-xamarinios-app"></a>Associando a ID do aplicativo e provisionamento de perfil com seu aplicativo xamarin. IOS

Depois que você criou e instalou um apropriado **perfil de provisionamento** conforme descrito, ele normalmente é hora de criar uma solução no Visual Studio para Mac ou Visual Studio. Acesso do Kit de integridade está disponível para qualquer iOS C# ou F# project.

Em vez de percorrer o processo de criação de um projeto do Xamarin iOS 8 manualmente, abra o aplicativo de exemplo conectado a este artigo (que inclui um Storyboard e o código pré-compilado). Para associar o aplicativo de exemplo em seu Kit de integridade habilitada **perfil de provisionamento**, no **painel de soluções**, clique com botão direito no projeto e exiba seu **opções** caixa de diálogo. Alterne para o **aplicativo do iOS** do painel e insira explícito **ID do aplicativo** criado anteriormente como o aplicativo **identificador de pacote**:

[![](healthkit-images/image06.png "Insira a ID do aplicativo explícita")](healthkit-images/image06.png#lightbox)

Agora, alterne para o **assinatura do pacote iOS** painel. Seu recentemente instalado **perfil de provisionamento**, com sua associação ao explícito **ID do aplicativo**, agora estará disponível como a **perfil de provisionamento**:

[![](healthkit-images/image07.png "Selecione o perfil de provisionamento")](healthkit-images/image07.png#lightbox)

Se o **perfil de provisionamento** não estiver disponível, verifique novamente a **identificador de pacote** no **aplicativo iOS** painel versus que especificou no **iOS Centro de desenvolvimento** e que o **perfil de provisionamento** está instalado (**Xcode > Preferências > contas > Exibir detalhes...** ).

Quando o Kit de integridade habilitada **perfil de provisionamento** é selecionado, clique em **Okey** para fechar a caixa de diálogo Opções do projeto.

### <a name="entitlementsplist-and-infoplist-values"></a>Entitlements. plist e valores de info. plist

O aplicativo de exemplo inclui um `Entitlements.plist` arquivo (que é necessário para aplicativos habilitados para o Kit de integridade) e não são incluídos em cada modelo de projeto. Se seu projeto não inclui os direitos, clique com botão direito no projeto, escolha **arquivo > novo arquivo... > iOS > Entitlements. plist** para adicionar um manualmente.

Por fim, sua `Entitlements.plist` deve ter o seguinte par de chave e valor:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>com.apple.developer.HealthKit</key>
    <true/>
</dict>
</plist>

```

Da mesma forma, o `Info.plist` para o aplicativo deve ter um valor de `healthkit` associado com o `UIRequiredDeviceCapabilities` chave:

```xml
<key>UIRequiredDeviceCapabilities</key>
<array>
<string>armv7</string>
    <string>healthkit</string>
</array>

```

O aplicativo de exemplo fornecido com este artigo inclui um pré-configurada `Entitlements.plist` que inclui todas as chaves necessárias.

<a name="programming" />

## <a name="programming-health-kit"></a>Kit de integridade de programação

O repositório de dados de integridade Kit é um repositório de dados privado, especificada pelo usuário que é compartilhado entre aplicativos. Como as informações de integridade são tão confidenciais, o usuário deve executar etapas positivas para permitir o acesso a dados. Esse acesso pode ser parcial (gravação, mas não leitura, o acesso para alguns tipos de dados, mas não para outros, etc.) e pode ser revogado a qualquer momento. Aplicativos de Kit de integridade devem ser escritos de forma defensiva, sabendo que muitos usuários será hesitante sobre como armazenar suas informações relacionadas à integridade.

Dados do Kit de integridade são limitados aos tipos especificados de Apple. Esses tipos são estritamente definidos: alguns, como o tipo de sangue, são limitados aos valores específicos de uma enumeração de Apple fornecida, enquanto outros combinam uma magnitude com uma unidade de medida (por exemplo, gramas, calorias e litros). Até mesmo dados que compartilham uma unidade compatível de medida são diferenciados por seus `HKObjectType`; por exemplo, o sistema de tipos irá capturar uma tentativa incorreta de armazenar um `HKQuantityTypeIdentifier.NumberOfTimesFallen` valor ao campo que espera um `HKQuantityTypeIdentifier.FlightsClimbed` mesmo que ambos usam o `HKUnit.Count` unidade de medida.

Os tipos armazenados no repositório de dados do Kit de integridade são subclasses de `HKObjectType`. `HKCharacteristicType` os objetos armazenam biológicos sexo, tipo de sangue e data de nascimento. No entanto, são mais comuns `HKSampleType` objetos que representam dados de amostragem em um momento específico ou em um período de tempo. 

[![](healthkit-images/image08.png "Gráfico de objetos HKSampleType")](healthkit-images/image08.png#lightbox)

`HKSampleType` é abstrato e tem quatro subclasses concretas. Atualmente, há apenas um tipo de `HKCategoryType` dados, que é a análise da suspensão. A grande maioria dos dados no Kit de integridade são do tipo `HKQuantityType` e armazenam os dados em `HKQuantitySample` objetos, que são criados usando o padrão de design fábrica familiar:

[![](healthkit-images/image09.png "A grande maioria dos dados no Kit de integridade são do tipo HKQuantityType e armazenar seus dados em objetos HKQuantitySample")](healthkit-images/image09.png#lightbox)

`HKQuantityType` tipos de intervalo de `HKQuantityTypeIdentifier.ActiveEnergyBurned` para `HKQuantityTypeIdentifier.StepCount`. 

<a name="requesting-permission" />

### <a name="requesting-permission-from-the-user"></a>Solicitando permissão do usuário

Os usuários finais deve seguir etapas positivas para permitir que um aplicativo ler ou gravar dados do Kit de integridade. Isso é feito por meio do aplicativo de integridade que vem pré-instalado em dispositivos iOS 8. A primeira vez que um aplicativo do Kit de integridade é executado, o usuário é apresentado com um sistema controlado **integridade acesso** caixa de diálogo:

[![](healthkit-images/image10.png "O usuário é apresentado com uma caixa de diálogo de integridade acesso controlado pelo sistema")](healthkit-images/image10.png#lightbox)

Posteriormente, o usuário pode alterar as permissões usando a integridade do aplicativo **fontes** caixa de diálogo:

[![](healthkit-images/image11.png "O usuário pode alterar as permissões usando a caixa de diálogo de fontes de aplicativos integridade")](healthkit-images/image11.png#lightbox)

Como informações de integridade são extremamente confidenciais, os desenvolvedores de aplicativos devem gravar seus programas de forma defensiva, com a expectativa de que permissões serão recusadas e alteradas enquanto o aplicativo está em execução. A linguagem mais comum é solicitar permissões no `UIApplicationDelegate.OnActivated` método e, em seguida, modificar a interface do usuário conforme apropriado.

### <a name="permissions-walkthrough"></a>Instruções passo a passo de permissões

No seu projeto provisionado pelo Kit de integridade, abra o `AppDelegate.cs` arquivo. Observe que a instrução usando `HealthKit`; na parte superior do arquivo.


O código a seguir está relacionado a permissões do Kit de integridade:

```csharp
private HKHealthStore healthKitStore = new HKHealthStore ();

public override void OnActivated (UIApplication application)
{
        ValidateAuthorization ();
}

private void ValidateAuthorization ()
{
        var heartRateId = HKQuantityTypeIdentifierKey.HeartRate;
        var heartRateType = HKObjectType.GetQuantityType (heartRateId);
        var typesToWrite = new NSSet (new [] { heartRateType });
        var typesToRead = new NSSet ();
        healthKitStore.RequestAuthorizationToShare (
                typesToWrite, 
                typesToRead, 
                ReactToHealthCarePermissions);
}

void ReactToHealthCarePermissions (bool success, NSError error)
{
        var access = healthKitStore.GetAuthorizationStatus (HKObjectType.GetQuantityType (HKQuantityTypeIdentifierKey.HeartRate));
        if (access.HasFlag (HKAuthorizationStatus.SharingAuthorized)) {
                HeartRateModel.Instance.Enabled = true;
        } else {
                HeartRateModel.Instance.Enabled = false;
        }
}

```

Todo o código nesses métodos poderia ser feito embutido em `OnActivated`, mas o aplicativo de exemplo usa métodos separados para tornar sua intenção mais clara: `ValidateAuthorization()` tem as etapas necessárias para solicitar acesso aos tipos que estão sendo gravados específica (e leitura, se o aplicativo desejado) e `ReactToHealthCarePermissions()` é um retorno de chamada que é ativado depois que o usuário interagiu com a caixa de diálogo de permissões no Health.app.

O trabalho de `ValidateAuthorization()` é criar o conjunto de `HKObjectTypes` que o aplicativo irá escrever e solicitar autorização para atualizar esses dados. No aplicativo de exemplo, o `HKObjectType` destina-se a chave `KHQuantityTypeIdentifierKey.HeartRate`. Esse tipo é adicionado ao conjunto `typesToWrite`, enquanto o conjunto `typesToRead` for deixada em branco. Esses conjuntos e uma referência para o `ReactToHealthCarePermissions()` retorno de chamada, que é passado para `HKHealthStore.RequestAuthorizationToShare()`.

O `ReactToHealthCarePermissions()` retorno de chamada será chamado depois que o usuário interagiu com a caixa de diálogo de permissões e é passado duas informações: uma `bool` valor que será `true` se o usuário interagiu com a caixa de diálogo de permissões e uma `NSError`que, se não for nulo, indica algum tipo de erro associado ao apresentar a caixa de diálogo de permissões.

> [!IMPORTANT]
> Para ser claro sobre os argumentos para essa função: a _sucesso_ e _erro_ parâmetros não indicam se o usuário tenha concedido permissão para acessar dados de integridade Kit! Eles apenas indicam que o usuário recebeu a oportunidade de permitir o acesso aos dados.

Para confirmar se o aplicativo tem acesso aos dados, o `HKHealthStore.GetAuthorizationStatus()` é usado, passando `HKQuantityTypeIdentifierKey.HeartRate`. Com base no status retornado, o aplicativo habilita ou desabilita a capacidade de inserir dados. Não há nenhuma experiência de usuário padrão para lidar com uma negação de acesso e há muitas opções possíveis. No aplicativo de exemplo, o status é definido em um `HeartRateModel` objeto singleton que, por sua vez, gera eventos relevantes.

## <a name="model-view-and-controller"></a>Modelo, exibição e controlador

Para examinar os `HeartRateModel` objeto singleton, abra o `HeartRateModel.cs` arquivo:

```csharp
using System;
using HealthKit;
using Foundation;

namespace HKWork
{
        public class GenericEventArgs<T> : EventArgs
        {
                public T Value { get; protected set; }
                public DateTime Time { get; protected set; }

                public GenericEventArgs (T value)
                {
                        this.Value = value;
                        Time = DateTime.Now;
                }
        }

        public delegate void GenericEventHandler<T> (object sender,GenericEventArgs<T> args);

        public sealed class HeartRateModel : NSObject
        {
                private static volatile HeartRateModel singleton;
                private static object syncRoot = new Object ();

                private HeartRateModel ()
                {
                }

                public static HeartRateModel Instance {
                        get {
                                //Double-check lazy initialization
                                if (singleton == null) {
                                        lock (syncRoot) {
                                                if (singleton == null) {
                                                        singleton = new HeartRateModel ();
                                                }
                                        }
                                }

                                return singleton;
                        }
                }

                private bool enabled = false;

                public event GenericEventHandler<bool> EnabledChanged;
                public event GenericEventHandler<String> ErrorMessageChanged;
                public event GenericEventHandler<Double> HeartRateStored;

                public bool Enabled { 
                        get { return enabled; }
                        set {
                                if (enabled != value) {
                                        enabled = value;
                                        InvokeOnMainThread(() => EnabledChanged (this, new GenericEventArgs<bool>(value)));
                                }
                        }
                }

                public void PermissionsError(string msg)
                {
                        Enabled = false;
                        InvokeOnMainThread(() => ErrorMessageChanged (this, new GenericEventArgs<string>(msg)));
                }

                //Converts its argument into a strongly-typed quantity representing the value in beats-per-minute
                public HKQuantity HeartRateInBeatsPerMinute(ushort beatsPerMinute)
                {
                        var heartRateUnitType = HKUnit.Count.UnitDividedBy (HKUnit.Minute);
                        var quantity = HKQuantity.FromQuantity (heartRateUnitType, beatsPerMinute);

                        return quantity;
                }
                        
                public void StoreHeartRate(HKQuantity quantity)
                {
                        var bpm = HKUnit.Count.UnitDividedBy (HKUnit.Minute);
                        //Confirm that the value passed in is of a valid type (can be converted to beats-per-minute)
                        if (! quantity.IsCompatible(bpm))
                        {
                                InvokeOnMainThread(() => ErrorMessageChanged(this, new GenericEventArgs<string> ("Units must be compatible with BPM")));
                        }

                        var heartRateId = HKQuantityTypeIdentifierKey.HeartRate;
                        var heartRateQuantityType = HKQuantityType.GetQuantityType (heartRateId);
                        var heartRateSample = HKQuantitySample.FromType (heartRateQuantityType, quantity, new NSDate (), new NSDate (), new HKMetadata());

                        using (var healthKitStore = new HKHealthStore ()) {
                                healthKitStore.SaveObject (heartRateSample, (success, error) => {
                                        InvokeOnMainThread (() => {
                                                if (success) {
                                                        HeartRateStored(this, new GenericEventArgs<Double>(quantity.GetDoubleValue(bpm)));
                                                } else {
                                                        ErrorMessageChanged(this, new GenericEventArgs<string>("Save failed"));
                                                }
                                                if (error != null) {
                                                        //If there's some kind of error, disable 
                                                        Enabled = false;
                                                        ErrorMessageChanged (this, new GenericEventArgs<string>(error.ToString()));
                                                }
                                        });
                                });
                        }
                }
        }
}

```

A primeira seção é o código clichê para a criação de manipuladores e eventos genéricos. A parte inicial do `HeartRateModel` classe também é clichê para a criação de um objeto singleton de thread-safe.

Em seguida, `HeartRateModel` expõe 3 eventos: 

- `EnabledChanged` -Indica que frequência cardíaca armazenamento foi habilitado ou desabilitado (Observe que o armazenamento é inicialmente desabilitado). 
- `ErrorMessageChanged` – Para este aplicativo de exemplo, temos um modelo muito simples de tratamento de erro: uma cadeia de caracteres com o último erro. 
- `HeartRateStored` -Acionado quando uma taxa de coração é armazenada no banco de dados do Kit de integridade.

Observe que a sempre que esses eventos são disparados, isso é feito por meio de `NSObject.InvokeOnMainThread()`, que permite que os assinantes atualizar a interface do usuário. Como alternativa, os eventos poderiam ser documentados como sendo gerados em threads em segundo plano e a responsabilidade de garantir a compatibilidade pode ser deixada para seus manipuladores. Considerações de thread são importantes em aplicativos de Kit de integridade porque muitas das funções, como a solicitação de permissão são assíncronas e executar seus retornos de chamada em threads não principal.

O código específico do Kit de integridade no `HeartRateModel` está em duas funções `HeartRateInBeatsPerMinute()` e `StoreHeartRate()`. 

`HeartRateInBeatsPerMinute()` Converte o argumento em um Kit de integridade fortemente tipada `HKQuantity`. O tipo da quantidade é que o especificado pelo `HKQuantityTypeIdentifierKey.HeartRate` e as unidades da quantidade estão `HKUnit.Count` dividido pelo `HKUnit.Minute` (em outras palavras, a unidade é *supera por minuto*). 

O `StoreHeartRate()` função usa um `HKQuantity` (no aplicativo de exemplo, um criado pelo `HeartRateInBeatsPerMinute()` ). Para validar seus dados, ele usa o `HKQuantity.IsCompatible()` método, que retorna `true` se unidades do objeto podem ser convertidas nas unidades no argumento. Se a quantidade foi criada com `HeartRateInBeatsPerMinute()` isso, obviamente, retornará `true`, mas também retornaria `true` se a quantidade foram criada como, por exemplo, *supera por hora*. Mais comumente, `HKQuantity.IsCompatible()` pode ser usado para validar em massa, distância e energia que um dispositivo ou o usuário pode inserir ou exibir em um sistema de medida (por exemplo, unidades Imperial), mas que pode ser armazenado em outro sistema (como unidades métricas). 

Depois que a compatibilidade da quantidade foi validada, o `HKQuantitySample.FromType()` método de fábrica é usado para criar um tipo mais acentuado `heartRateSample` objeto. `HKSample` objetos têm uma data de início e término; para leituras instantâneas, esses valores devem ser os mesmos, como estão no exemplo. O exemplo também não define nenhum dado de chave-valor seu `HKMetadata` argumento, mas um poderia usar código como o código a seguir para especificar o local do sensor:

```csharp
var hkm = new HKMetadata();
hkm.HeartRateSensorLocation = HKHeartRateSensorLocation.Chest;

```

Uma vez o `heartRateSample` tiver sido criado, o código cria uma nova conexão ao banco de dados com o uso de bloco. Dentro desse bloco, o `HKHealthStore.SaveObject()` método tenta a gravação assíncrona para o banco de dados. A chamada resultante para a expressão lambda dispara eventos relevantes, tanto `HeartRateStored` ou `ErrorMessageChanged`.

Agora que o modelo tenha sido programado, é hora de ver como o controlador reflete o estado do modelo. Abra o `HKWorkViewController.cs` arquivo. O construtor simplesmente conecta o `HeartRateModel` singleton para métodos de manipulação de eventos (novamente, isso poderia ser feito em linha com expressões lambda, mas métodos separados deixar a intenção um pouco mais óbvio):

```csharp
public HKWorkViewController (IntPtr handle) : base (handle)
{
     HeartRateModel.Instance.EnabledChanged += OnEnabledChanged;
     HeartRateModel.Instance.ErrorMessageChanged += OnErrorMessageChanged;
     HeartRateModel.Instance.HeartRateStored += OnHeartBeatStored;
}

```

Aqui estão os manipuladores relevantes:

```csharp
void OnEnabledChanged (object sender, GenericEventArgs<bool> args)
{
        StoreData.Enabled = args.Value;
        PermissionsLabel.Text = args.Value ? "Ready to record" : "Not authorized to store data.";
        PermissionsLabel.SizeToFit ();
}

void OnErrorMessageChanged (object sender, GenericEventArgs<string> args)
{
        PermissionsLabel.Text = args.Value;
}

void OnHeartBeatStored (object sender, GenericEventArgs<double> args)
{
        PermissionsLabel.Text = String.Format ("Stored {0} BPM", args.Value);
}

```

Obviamente, em um aplicativo com um único controlador, seria possível evitar a criação de um objeto de modelo separado e o uso de eventos de fluxo de controle, mas o uso de objetos de modelo é mais apropriado para aplicativos do mundo real.

## <a name="running-the-sample-app"></a>Executando o aplicativo de exemplo

O simulador de iOS não dá suporte para o Kit de integridade. Depuração deve ser feita em um dispositivo físico executando o iOS 8.

Anexe um dispositivo de desenvolvimento provisionado corretamente iOS 8 para o seu sistema. Selecione-o como o destino de implantação no Visual Studio para Mac e no menu, escolha **Executar > Depurar**.

> [!IMPORTANT]
> Erros relacionados ao provisionamento serão exibidos no momento. Para solucionar problemas, examine a criação e provisionamento de uma seção de aplicativo do Kit de integridade acima. Os componentes são: 
>
> - **Centro de desenvolvimento do iOS** -ID do aplicativo explícita & integridade Kit habilitado o perfil de provisionamento. 
> - **Opções do projeto** -o identificador de pacote (ID do aplicativo explícita) e um perfil de provisionamento.
> - **O código-fonte** -Entitlements. plist & Info. plist

Supondo que provisiona foram definida corretamente, seu aplicativo será iniciado. Quando ele atinge seu `OnActivated` método, ele solicitará a autorização do Kit de integridade. Na primeira vez que isso é encontrado pelo sistema operacional, o usuário verá a caixa de diálogo a seguir:


[![](healthkit-images/image12.png "O usuário verá essa caixa de diálogo")](healthkit-images/image12.png#lightbox)

Habilitar seu aplicativo atualizar os dados de taxa de coração e seu aplicativo reaparecerá. O `ReactToHealthCarePermissions` retorno de chamada será ativado de forma assíncrona. Isso fará com que o `HeartRateModel’s` `Enabled` propriedade a ser alterada, que irá gerar o `EnabledChanged` evento, o que fará com que o `HKPermissionsViewController.OnEnabledChanged()` manipulador de eventos em execução, que permite que o `StoreData` botão. O diagrama a seguir mostra a sequência:


[![](healthkit-images/image13.png "Este diagrama mostra a sequência de eventos")](healthkit-images/image13.png#lightbox)

Pressione a **registro** botão. Isso fará com que o `StoreData_TouchUpInside()` manipulador seja executado, que tenta analisar o valor da `heartRate` campo de texto, converter em um `HKQuantity` via discutido anteriormente `HeartRateModel.HeartRateInBeatsPerMinute()` de função e passar essa quantidade para `HeartRateModel.StoreHeartRate()`. Conforme discutido anteriormente, isso tentará armazenar os dados e gerará a um `HeartRateStored` ou `ErrorMessageChanged` eventos.

Clique duas vezes o **Home** botão no seu dispositivo e abra o aplicativo de integridade. Clique o **fontes** guia e você verá o aplicativo de exemplo listado. Selecione-o e não permitir a permissão para atualizar dados de taxa de coração. Clique duas vezes o **Home** botão e volte para seu aplicativo. Mais uma vez, `ReactToHealthCarePermissions()` será chamado, mas desta vez, porque o acesso é negado, o **StoreData** botão ficarão desabilitado (Observe que isso ocorre de forma assíncrona e a alteração na interface do usuário pode estar visível para o usuário final).

## <a name="advanced-topics"></a>Tópicos avançados

Lendo dados do Kit de integridade do banco de dados é muito semelhante à gravação de dados: uma especifica os tipos de dados que alguém está tentando acessar, autorização de solicitações, e se essa autorização é concedida, os dados estão disponíveis, com conversão automática de unidades compatíveis de medida.

Há um número de funções de consulta mais sofisticadas que permitem consultas com base no predicado e consultas que executam atualizações quando os dados relevantes são atualizados. 

Os desenvolvedores de aplicativos de Kit de integridade devem examinar a seção integridade Kit da Apple [aplicativo examinar diretrizes](https://developer.apple.com/app-store/review/guidelines/#healthkit).

Depois que a segurança e os modelos de sistema de tipo são compreendidos, armazenar e ler os dados no banco de dados compartilhado do Kit de integridade são bem simples. Muitas das funções dentro do Kit de integridade operam de forma assíncrona e os desenvolvedores de aplicativos devem gravar seus programas adequadamente.

No momento da elaboração deste artigo, atualmente, não há nenhum equivalente ao Kit de integridade no Android ou Windows Phone.

## <a name="summary"></a>Resumo

Neste artigo, vimos como o Kit de integridade permite que os aplicativos armazenar, recuperar e a integridade de compartilhamento de informações relacionadas, fornecendo também um aplicativo de integridade padrão que permite que o acesso do usuário e controle sobre esses dados. 

Também vimos como privacidade, segurança e integridade de dados estão substituindo preocupações para informações relacionadas à integridade e aplicativos usando o Kit de integridade devem lidar com o aumento na complexidade em aspectos do gerenciamento do aplicativo (provisionamento) (tipo de integridade do Kit de codificação sistema) e (controle de usuário de permissões por meio de caixas de diálogo do sistema e aplicativo de integridade) de experiência do usuário. 

Por fim, temos a dar uma olhada em uma implementação simples do Kit de integridade usando o aplicativo de amostra incluído que grava dados de pulsação para o repositório de integridade Kit e tem um design de reconhecimento assíncrono.

## <a name="related-links"></a>Links relacionados

- [HKWork (amostra)](https://developer.xamarin.com/samples/monotouch/ios8/IntroToHealthKit/)
- [Introdução ao iOS 8](~/ios/platform/introduction-to-ios8.md)
