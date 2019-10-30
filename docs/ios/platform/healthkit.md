---
title: HealthKit no Xamarin. iOS
description: Este documento descreve o HealthKit, uma estrutura introduzida no iOS 8 que fornece um repositório de dados centralizado, coordenado e seguro para informações relacionadas à integridade. Ele discute como provisionar um aplicativo HealthKit e como escrever código que usa a estrutura HealthKit.
ms.prod: xamarin
ms.assetid: E3927A21-507C-43BA-A2AD-957716BA9B52
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 21f10c7771e1c30eabb3f42a161c6d563a5327f3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032394"
---
# <a name="healthkit-in-xamarinios"></a>HealthKit no Xamarin. iOS

O kit de integridade fornece um repositório de dados seguro para as informações relacionadas à integridade do usuário. Os aplicativos do kit de integridade podem, com a permissão explícita do usuário, a leitura e a gravação nesse repositório de dados e recebem notificações quando é adicionado um dado pertinente. Os aplicativos podem apresentar os dados, ou o usuário pode usar o aplicativo de integridade fornecido pela Apple para exibir um painel de todos os seus dados.

Como os dados relacionados à integridade são tão sensíveis e cruciais, o kit de integridade é fortemente tipado, com unidades de medida e uma associação explícita com o tipo de informações que estão sendo registradas (por exemplo, nível de glicose de sangue ou taxa de coração). Além disso, os aplicativos do kit de integridade devem usar direitos explícitos, devem solicitar acesso a tipos específicos de informações e o usuário deve conceder explicitamente ao aplicativo acesso a esses tipos de dados.

Este artigo apresentará:

- Requisitos de segurança do kit de integridade, incluindo provisionamento de aplicativos e solicitação de permissão de usuário para acessar o banco de dados do kit de integridade;
- Sistema de tipos do kit de integridade, que minimiza a possibilidade de aplicação de de incorreta ou interpretação de dados incorretamente;
- Gravando no repositório de armazenamento do kit de integridade compartilhado em todo o sistema.

Este artigo não abordará tópicos mais avançados, como a consulta do banco de dados, a conversão entre unidades de medida ou o recebimento de notificações de novos recursos.

Neste artigo, criaremos um aplicativo de exemplo para registrar a taxa de coração do usuário:

[![](healthkit-images/image01.png "A sample application to record the users heart rate")](healthkit-images/image01.png#lightbox)

## <a name="requirements"></a>Requisitos

Os itens a seguir são necessários para concluir as etapas apresentadas neste artigo:

- **Xcode 7 e Ios 8 (ou superior)** – as APIs do Xcode e do Ios mais recentes da Apple precisam ser instaladas e configuradas no computador do desenvolvedor.
- **Visual Studio para Mac ou Visual Studio** – a versão mais recente do Visual Studio para Mac deve ser instalada e configurada no computador do desenvolvedor.
- **dispositivo IOS 8 (ou superior)** – um dispositivo IOS executando a versão mais recente do IOS 8 ou superior para teste.

> [!IMPORTANT]
> O kit de integridade foi introduzido no iOS 8. Atualmente, o Health kit não está disponível no simulador de iOS e a depuração requer conexão com um dispositivo iOS físico.

## <a name="creating-and-provisioning-a-health-kit-app"></a>Criando e provisionando um aplicativo de kit de integridade
Antes que um aplicativo Xamarin iOS 8 possa usar a API HealthKit, ele deve ser configurado e provisionado corretamente. Esta seção abordará as etapas necessárias para configurar corretamente seu aplicativo Xamarin.

Os aplicativos do kit de integridade exigem:

- Uma **ID de aplicativo**explícita.
- Um **perfil de provisionamento** associado a essa ID explícita do **aplicativo** e com as permissões do **Kit de integridade** .
- Um `Entitlements.plist` com uma propriedade `com.apple.developer.healthkit` do tipo `Boolean` definido como `Yes`.
- Um `Info.plist` cuja chave de `UIRequiredDeviceCapabilities` contém uma entrada com o valor de `String` `healthkit`.
- O `Info.plist` também deve ter as entradas de explicação de privacidade apropriadas: uma explicação de `String` para a chave `NSHealthUpdateUsageDescription` se o aplicativo for gravar dados e uma explicação de `String` para a chave `NSHealthShareUsageDescription` se o aplicativo for ler os dados do kit de integridade.

Para saber mais sobre como provisionar um aplicativo iOS, o artigo [provisionamento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md) na série **introdução** do Xamarin descreve a relação entre os certificados de desenvolvedor, as IDs de aplicativo, os perfis de provisionamento e os direitos de aplicativo.

<a name="explicit-appid" />

### <a name="explicit-app-id-and-provisioning-profile"></a>ID explícita do aplicativo e perfil de provisionamento

A criação de uma **ID de aplicativo** explícita e um **perfil de provisionamento** apropriado é feito no centro de [desenvolvimento do IOS](https://developer.apple.com/devcenter/ios/index.action)da Apple. 

Suas **IDs de aplicativo** atuais são listadas na seção [certificados, identificadores & perfis](https://developer.apple.com/account/ios/identifiers/bundle/bundleList.action) do centro de desenvolvimento. Geralmente, essa lista mostrará valores de **ID** de `*`, indicando que a **id do aplicativo** - **nome** pode ser usada com qualquer número de sufixos. Essas *IDs de aplicativo curinga* não podem ser usadas com o kit de integridade.

Para criar uma **ID de aplicativo**explícita, clique no botão **+** no canto superior direito para levá-lo para a página **registrar ID do aplicativo IOS** :

[![](healthkit-images/image02.png "Registering an app on the Apple Developer Portal")](healthkit-images/image02.png#lightbox)

Conforme mostrado na imagem acima, depois de criar uma descrição do aplicativo, use a seção **ID explícita do aplicativo** para criar uma ID para seu aplicativo. Na seção **serviços de aplicativos** , verifique **Kit de integridade** na seção **habilitar serviços** .

Quando terminar, pressione o botão **continuar** para registrar a ID do **aplicativo** em sua conta. Você será levado de volta à página **certificados, identificadores e perfis** . Clique em **perfis de provisionamento** para levá-lo para a lista de seus perfis de provisionamento atuais e clique no botão **+** no canto superior direito para levá-lo à página **Adicionar perfil de provisionamento do IOS** . Selecione a opção de **desenvolvimento de aplicativo do IOS** e clique em **continuar** para acessar a página **selecionar ID do aplicativo** . Aqui, selecione a ID explícita do **aplicativo** que você especificou anteriormente:

[![](healthkit-images/image03.png "Select the explicit App ID")](healthkit-images/image03.png#lightbox)

Clique em **continuar** e trabalhe nas telas restantes, em que você especificará seus **certificados de desenvolvedor**, **dispositivos**e um **nome** para este perfil de **provisionamento**:

[![](healthkit-images/image04.png "Generating the Provisioning Profile")](healthkit-images/image04.png#lightbox)

Clique em **gerar** e aguardar a criação do seu perfil. Baixe o arquivo e clique duas vezes nele para instalar o no Xcode. Você pode confirmar que está instalado em **Xcode > preferências > contas > exibir detalhes...** Você deve ver seu perfil de provisionamento recém instalado e deve ter o ícone do Health kit e qualquer outro serviço especial em sua linha de **direitos** :

[![](healthkit-images/image05.png "Viewing the profile in Xcode")](healthkit-images/image05.png#lightbox)

<a name="associating-appid" />

### <a name="associating-the-app-id-and-provisioning-profile-with-your-xamarinios-app"></a>Associando a ID do aplicativo e o perfil de provisionamento com seu aplicativo Xamarin. iOS

Depois de criar e instalar um perfil de **provisionamento** apropriado conforme descrito, normalmente seria hora de criar uma solução no Visual Studio para Mac ou no Visual Studio. O acesso ao kit de integridade está disponível C# para F# qualquer Ios ou projeto.

Em vez de percorrer o processo de criação de um projeto do Xamarin iOS 8 manualmente, abra o aplicativo de exemplo anexado a este artigo (que inclui um storyboard e código predefinidos). Para associar o aplicativo de exemplo ao seu perfil de **provisionamento**habilitado do kit de integridade, na **painel de soluções**, clique com o botão direito do mouse no projeto e ative sua caixa de diálogo de **Opções** . Alterne para o painel de **aplicativo do IOS** e insira a ID explícita do **aplicativo** que você criou anteriormente como o **identificador do pacote**do aplicativo:

[![](healthkit-images/image06.png "Enter the explicit App ID")](healthkit-images/image06.png#lightbox)

Agora, alterne para o painel de **assinatura do pacote do IOS** . Seu **perfil de provisionamento**recentemente instalado, com sua associação à ID explícita do **aplicativo**, agora estará disponível como o **perfil de provisionamento**:

[![](healthkit-images/image07.png "Select the Provisioning Profile")](healthkit-images/image07.png#lightbox)

Se o **perfil de provisionamento** não estiver disponível, verifique o **identificador do pacote** no painel de **aplicativos do IOS** em vez do especificado no centro de desenvolvimento do **Ios** e se o **perfil de provisionamento** está instalado (**Xcode Preferências de > > contas > Exibir detalhes...** ).

Quando o **perfil de provisionamento** habilitado para kit de integridade estiver selecionado, clique em **OK** para fechar a caixa de diálogo opções de projeto.

### <a name="entitlementsplist-and-infoplist-values"></a>Valores de direitos. plist e info. plist

O aplicativo de exemplo inclui um arquivo de `Entitlements.plist` (que é necessário para aplicativos habilitados para o kit de integridade) e não é incluído em todos os modelos de projeto. Se o seu projeto não incluir direitos, clique com o botão direito do mouse em seu projeto, escolha **arquivo > novo arquivo... > iOS > os direitos. plist** para adicionar um manualmente.

Por fim, seu `Entitlements.plist` deve ter o seguinte par de chave e valor:

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

Da mesma forma, o `Info.plist` para o aplicativo deve ter um valor de `healthkit` associado à chave de `UIRequiredDeviceCapabilities`:

```xml
<key>UIRequiredDeviceCapabilities</key>
<array>
<string>armv7</string>
    <string>healthkit</string>
</array>

```

O aplicativo de exemplo fornecido com este artigo inclui um `Entitlements.plist` pré-configurado que inclui todas as chaves necessárias.

<a name="programming" />

## <a name="programming-health-kit"></a>Kit de integridade de programação

O armazenamento de informações do kit de integridade é um repositório de armazenamento privado específico do usuário que é compartilhado entre aplicativos. Como as informações de integridade são tão confidenciais, o usuário deve executar etapas positivas para permitir o acesso a dados. Esse acesso pode ser parcial (gravação, mas não lido, acesso a alguns tipos de dados, mas não a outros, etc.) e pode ser revogado a qualquer momento. Os aplicativos do kit de integridade devem ser escritos de forma defensiva, sabendo que muitos usuários serão hesitars sobre o armazenamento de suas informações relacionadas à integridade.

Os dados do kit de integridade são limitados aos tipos especificados pela Apple. Esses tipos são estritamente definidos: alguns, como tipo de sangue, são limitados aos valores específicos de uma enumeração fornecida pela Apple, enquanto outros combinam uma magnitude com uma unidade de medida (como gramas, calorias e litros). Até mesmo dados que compartilham uma unidade de medida compatível são diferenciados por seus `HKObjectType`; por exemplo, o sistema de tipos capturará uma tentativa incorreta de armazenar um valor de `HKQuantityTypeIdentifier.NumberOfTimesFallen` em um campo que espera um `HKQuantityTypeIdentifier.FlightsClimbed` mesmo que ambos usem a unidade de medida `HKUnit.Count`.

Os tipos armazenada no armazenamento de Datakit de integridade são todas subclasses de `HKObjectType`. `HKCharacteristicType` objetos armazenam sexo biológico, tipo de sangue e data de nascimento. No entanto, mais comuns são os objetos `HKSampleType`, que representam os dados que são amostrados em um momento específico ou em um período de tempo. 

[![](healthkit-images/image08.png "HKSampleType objects chart")](healthkit-images/image08.png#lightbox)

`HKSampleType` é abstract e tem quatro subclasses concretas. Atualmente, há apenas um tipo de dados de `HKCategoryType`, que é a análise de suspensão. A grande maioria dos dados no kit de integridade são do tipo `HKQuantityType` e armazena seus dados em `HKQuantitySample` objetos, que são criados usando o padrão de design de fábrica familiar:

[![](healthkit-images/image09.png "The large majority of data in Health Kit are of type HKQuantityType and store their data in HKQuantitySample objects")](healthkit-images/image09.png#lightbox)

os tipos de `HKQuantityType` variam de `HKQuantityTypeIdentifier.ActiveEnergyBurned` para `HKQuantityTypeIdentifier.StepCount`. 

<a name="requesting-permission" />

### <a name="requesting-permission-from-the-user"></a>Solicitando permissão do usuário

Os usuários finais devem seguir etapas positivas para permitir que um aplicativo leia ou grave dados do kit de integridade. Isso é feito por meio do aplicativo de integridade que vem pré-instalado em dispositivos iOS 8. Na primeira vez em que um aplicativo do kit de integridade é executado, o usuário recebe uma caixa de diálogo de **acesso de integridade** controlado pelo sistema:

[![](healthkit-images/image10.png "The user is presented with a system-controlled Health Access dialog")](healthkit-images/image10.png#lightbox)

Posteriormente, o usuário pode alterar as permissões usando a caixa de diálogo **origens** do aplicativo de integridade:

[![](healthkit-images/image11.png "The user can change permissions using Health apps Sources dialog")](healthkit-images/image11.png#lightbox)

Como as informações de integridade são extremamente confidenciais, os desenvolvedores de aplicativos devem escrever seus programas de forma defensiva, com a expectativa de que as permissões serão recusadas e alteradas enquanto o aplicativo estiver em execução. O idioma mais comum é solicitar permissões no método `UIApplicationDelegate.OnActivated` e, em seguida, modificar a interface do usuário conforme apropriado.

### <a name="permissions-walkthrough"></a>Instruções de permissões

No seu kit de integridade-projeto provisionado, abra o arquivo `AppDelegate.cs`. Observe a instrução usando `HealthKit`; na parte superior do arquivo.

O código a seguir está relacionado às permissões do Health Kit:

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

Todo o código nesses métodos pode ser feito embutido em `OnActivated`, mas o aplicativo de exemplo usa métodos separados para tornar suas intenções mais claras: `ValidateAuthorization()` tem as etapas necessárias para solicitar acesso aos tipos específicos que estão sendo gravados (e lidos, se o aplicativo desejado) e `ReactToHealthCarePermissions()` é um retorno de chamada que é ativado depois que o usuário interage com a caixa de diálogo de permissões no Health. app.

O trabalho de `ValidateAuthorization()` é criar o conjunto de `HKObjectTypes` que o aplicativo irá gravar e solicitar autorização para atualizar esses dados. No aplicativo de exemplo, o `HKObjectType` é para o `KHQuantityTypeIdentifierKey.HeartRate`de chave. Esse tipo é adicionado ao conjunto `typesToWrite`, enquanto o `typesToRead` definido é deixado vazio. Esses conjuntos e uma referência ao retorno de chamada `ReactToHealthCarePermissions()` são passados para `HKHealthStore.RequestAuthorizationToShare()`.

O retorno de chamada `ReactToHealthCarePermissions()` será chamado depois que o usuário tiver interagindo com a caixa de diálogo de permissões e passar duas informações: um valor de `bool` que será `true`do se o usuário tiver interagindo com a caixa de diálogo de permissões e um `NSError` que , se não for NULL, indica algum tipo de erro associado à apresentação da caixa de diálogo de permissões.

> [!IMPORTANT]
> Para estar claro sobre os argumentos para esta função: os parâmetros de _êxito_ e _erro_ não indicam se o usuário concedeu permissão para acessar os dados do kit de integridade! Eles indicam apenas que o usuário tem a oportunidade de permitir o acesso aos dados.

Para confirmar se o aplicativo tem acesso aos dados, o `HKHealthStore.GetAuthorizationStatus()` é usado, passando `HKQuantityTypeIdentifierKey.HeartRate`. Com base no status retornado, o aplicativo habilita ou desabilita a capacidade de inserir dados. Não há nenhuma experiência de usuário padrão para lidar com uma negação de acesso e há muitas opções possíveis. No aplicativo de exemplo, o status é definido em um `HeartRateModel` objeto singleton que, por sua vez, gera eventos relevantes.

## <a name="model-view-and-controller"></a>Modelo, exibição e controlador

Para examinar o `HeartRateModel` objeto singleton, abra o arquivo `HeartRateModel.cs`:

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

A primeira seção é um código clichê para a criação de manipuladores e eventos genéricos. A parte inicial da classe `HeartRateModel` também é clichê para a criação de um objeto singleton thread-safe.

Em seguida, `HeartRateModel` expõe 3 eventos: 

- `EnabledChanged`-indica que o armazenamento de taxa de coração foi habilitado ou desabilitado (Observe que o armazenamento está inicialmente desabilitado). 
- `ErrorMessageChanged`-para este aplicativo de exemplo, temos um modelo de tratamento de erros muito simples: uma cadeia de caracteres com o último erro. 
- `HeartRateStored`-gerado quando uma taxa de coração é armazenada no banco de dados do kit de integridade.

Observe que sempre que esses eventos são acionados, isso é feito por meio de `NSObject.InvokeOnMainThread()`, o que permite aos assinantes atualizar a interface do usuário. Como alternativa, os eventos podem ser documentados como sendo gerados em threads em segundo plano e a responsabilidade de garantir a compatibilidade pode ser deixada para seus manipuladores. As considerações de thread são importantes nos aplicativos do kit de integridade porque muitas das funções, como a solicitação de permissão, são assíncronas e executam seus retornos de chamada em threads não principais.

O código específico do kit de integridade no `HeartRateModel` está nas duas funções `HeartRateInBeatsPerMinute()` e `StoreHeartRate()`. 

`HeartRateInBeatsPerMinute()` converte seu argumento em um kit de integridade fortemente tipado `HKQuantity`. O tipo da quantidade é especificado pelo `HKQuantityTypeIdentifierKey.HeartRate` e as unidades da quantidade são `HKUnit.Count` dividida por `HKUnit.Minute` (em outras palavras, a unidade é de *batida por minuto*). 

A função `StoreHeartRate()` usa uma `HKQuantity` (no aplicativo de exemplo, uma criada por `HeartRateInBeatsPerMinute()`). Para validar seus dados, ele usa o método `HKQuantity.IsCompatible()`, que retorna `true` se as unidades do objeto puderem ser convertidas em unidades no argumento. Se a quantidade tiver sido criada com `HeartRateInBeatsPerMinute()` isso, obviamente, retornará `true`, mas também retornará `true` se a quantidade tiver sido criada como, por exemplo, de *batidas por hora*. Mais comumente, `HKQuantity.IsCompatible()` pode ser usado para validar a massa, a distância e a energia que o usuário ou um dispositivo pode inserir ou exibir em um sistema de medida (como unidades Imperial), mas que pode ser armazenado em outro sistema (como unidades métricas). 

Depois que a compatibilidade da quantidade tiver sido validada, o método de fábrica `HKQuantitySample.FromType()` será usado para criar um objeto de `heartRateSample` fortemente tipado. `HKSample` objetos têm uma data de início e de término; para leituras instantâneas, esses valores devem ser iguais, como no exemplo. O exemplo também não define nenhum dado de valor chave em seu `HKMetadata` argumento, mas é possível usar um código como o código a seguir para especificar o local do sensor:

```csharp
var hkm = new HKMetadata();
hkm.HeartRateSensorLocation = HKHeartRateSensorLocation.Chest;

```

Depois que o `heartRateSample` tiver sido criado, o código criará uma nova conexão com o banco de dados com o bloco Using. Dentro desse bloco, o método `HKHealthStore.SaveObject()` tenta a gravação assíncrona no banco de dados. A chamada resultante para a expressão lambda dispara eventos relevantes, seja `HeartRateStored` ou `ErrorMessageChanged`.

Agora que o modelo foi programado, é hora de ver como o controlador reflete o estado do modelo. Abra o arquivo `HKWorkViewController.cs`. O Construtor simplesmente conecta os `HeartRateModel` singleton aos métodos de manipulação de eventos (novamente, isso pode ser feito embutido em expressões lambda, mas métodos separados tornam a intenção um pouco mais óbvia):

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

Obviamente, em um aplicativo com um único controlador, seria possível evitar a criação de um objeto de modelo separado e o uso de eventos para o fluxo de controle, mas o uso de objetos de modelo é mais apropriado para aplicativos do mundo real.

## <a name="running-the-sample-app"></a>Executando o aplicativo de exemplo

O simulador do iOS não dá suporte ao kit de integridade. A depuração deve ser feita em um dispositivo físico que executa o iOS 8.

Anexe um dispositivo de Desenvolvimento iOS 8 provisionado corretamente ao seu sistema. Selecione-o como o destino de implantação em Visual Studio para Mac e, no menu, escolha **executar > depurar**.

> [!IMPORTANT]
> Erros relacionados ao provisionamento surgirão neste ponto. Para solucionar problemas de erros, examine a seção criando e provisionando um aplicativo do kit de integridade acima. Os componentes são: 
>
> - **centro de desenvolvimento do IOS** -ID do aplicativo explícito & o perfil de provisionamento habilitado para o kit de integridade. 
> - **Opções de projeto** – identificador de pacote (ID do aplicativo explícito) & perfil de provisionamento.
> - **Código-fonte** -direitos. plist & info. plist

Supondo que as provisões tenham sido definidas corretamente, o aplicativo será iniciado. Quando ele atingir seu método de `OnActivated`, ele solicitará a autorização do kit de integridade. Na primeira vez que isso for encontrado pelo sistema operacional, o usuário receberá a seguinte caixa de diálogo:

[![](healthkit-images/image12.png "The user will be presented with this dialog")](healthkit-images/image12.png#lightbox)

Habilite seu aplicativo para atualizar os dados de taxa de coração e seu aplicativo será exibido novamente. O retorno de chamada `ReactToHealthCarePermissions` será ativado de forma assíncrona. Isso fará com que a propriedade `HeartRateModel’s` `Enabled` seja alterada, o que irá gerar o evento `EnabledChanged`, o que fará com que o manipulador de eventos `HKPermissionsViewController.OnEnabledChanged()` seja executado, o que habilita o botão `StoreData`. O diagrama a seguir mostra a sequência:

[![](healthkit-images/image13.png "This diagram shows the sequence of events")](healthkit-images/image13.png#lightbox)

Pressione o botão **gravar** . Isso fará com que o manipulador de `StoreData_TouchUpInside()` seja executado, o que tentará analisar o valor do campo de texto `heartRate`, converter em um `HKQuantity` por meio da função `HeartRateModel.HeartRateInBeatsPerMinute()` discutida anteriormente e passará essa quantidade para `HeartRateModel.StoreHeartRate()`. Como discutido anteriormente, isso tentará armazenar os dados e irá gerar um evento `HeartRateStored` ou `ErrorMessageChanged`.

Clique duas vezes no botão **página inicial** do dispositivo e abra o aplicativo de integridade. Clique na guia **fontes** e você verá o aplicativo de exemplo listado. Escolha e desautorizar permissão para atualizar dados de taxa de coração. Clique duas vezes no botão **página inicial** e alterne de volta para seu aplicativo. Mais uma vez, `ReactToHealthCarePermissions()` será chamado, mas desta vez, como o acesso é negado, o botão **StoreData** será desabilitado (Observe que isso ocorre de forma assíncrona e a alteração na interface do usuário pode estar visível para o usuário final).

## <a name="advanced-topics"></a>Tópicos avançados

A leitura de dados do Health kit Database é muito semelhante à gravação de dados: um especifica os tipos de dados que um está tentando acessar, solicita autorização e, se essa autorização for concedida, os dados estarão disponíveis, com a conversão automática para unidades compatíveis do mensura.

Há várias funções de consulta mais sofisticadas que permitem consultas e consultas baseadas em predicado que executam atualizações quando dados relevantes são atualizados. 

Os desenvolvedores de aplicativos do kit de integridade devem examinar a seção Health kit das [diretrizes de revisão de aplicativo](https://developer.apple.com/app-store/review/guidelines/#healthkit)da Apple.

Depois que os modelos de segurança e tipo de sistema são compreendidos, o armazenamento e a leitura de dados no banco de dado do kit de integridade compartilhado são bem simples. Muitas das funções do kit de integridade operam de forma assíncrona e os desenvolvedores de aplicativos devem escrever seus programas adequadamente.

No momento da elaboração deste artigo, não há nenhum equivalente ao kit de integridade no Android ou Windows Phone.

## <a name="summary"></a>Resumo

Neste artigo, vimos como o Health kit permite que os aplicativos armazenem, recuperem e compartilhem informações relacionadas à integridade, fornecendo também um aplicativo de integridade padrão que permite que o usuário acesse e controle esses dados. 

Também vimos como a privacidade, a segurança e a integridade dos dados estão substituindo as preocupações de informações e aplicativos relacionados à integridade usando o Health kit deve lidar com o aumento na complexidade nos aspectos do gerenciamento de aplicativos (provisionamento), codificação (tipo do kit de integridade sistema) e experiência do usuário (controle do usuário de permissões por meio de caixas de diálogo do sistema e aplicativo de integridade). 

Por fim, vamos dar uma olhada em uma implementação simples do kit de integridade usando o aplicativo de exemplo incluído que grava dados de pulsação na loja do kit de integridade e tem um design com reconhecimento assíncrono.

## <a name="related-links"></a>Links relacionados

- [HKWork (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-introtohealthkit)
- [Introdução ao iOS 8](~/ios/platform/introduction-to-ios8.md)
