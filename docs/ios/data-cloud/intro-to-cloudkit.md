---
title: CloudKit no Xamarin. iOS
description: Este documento descreve como trabalhar com o CloudKit no Xamarin. iOS. Ele fornece uma visão geral do CloudKit e discute como habilitá-lo, a API de conveniência do CloudKit, a escalabilidade, as contas de usuário e os ambientes de desenvolvimento e produção.
ms.prod: xamarin
ms.assetid: 66B207F2-FAA0-4551-B43B-3DB9F620C397
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 05/11/2016
ms.openlocfilehash: 240b3c1547231ebbea568f4d5d10407ec560390b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70763393"
---
# <a name="cloudkit-in-xamarinios"></a>CloudKit no Xamarin. iOS

A estrutura CloudKit simplifica o desenvolvimento de aplicativos que acessam o iCloud. Isso inclui a recuperação de dados de aplicativos e direitos de ativos, bem como a capacidade de armazenar informações de aplicativos com segurança. Esse kit oferece aos usuários uma camada de anonimato, permitindo o acesso a aplicativos com suas IDs do iCloud sem compartilhar informações pessoais.

Os desenvolvedores podem se concentrar em seus aplicativos do lado do cliente e deixar o iCloud eliminar a necessidade de escrever a lógica do aplicativo no lado do servidor. O CloudKit fornece autenticação, bancos de dados públicos e privados, além de serviços de armazenamento de ativos e data Structured.

> [!IMPORTANT]
> A Apple [fornece ferramentas](https://developer.apple.com/support/allowing-users-to-manage-data/) para ajudar os desenvolvedores a lidar adequadamente com o GDPR (Regulamento Geral sobre a Proteção de Dados) da União Europeia.

## <a name="requirements"></a>Requisitos

O seguinte é necessário para concluir as etapas apresentadas neste artigo:

- **Xcode e o SDK do IOS – as** APIs Xcode e Ios 8 da Apple precisam ser instaladas e configuradas no computador do desenvolvedor.
- **Visual Studio para Mac** – a versão mais recente do Visual Studio para Mac deve ser instalada e configurada no dispositivo do usuário.
- **dispositivo IOS 8** – um dispositivo IOS executando a versão mais recente do IOS 8 para teste.

## <a name="what-is-cloudkit"></a>O que é o CloudKit?

CloudKit é uma maneira de fornecer ao desenvolvedor acesso aos servidores iCloud. Ele fornece a base para a unidade iCloud e para a biblioteca de fotos do iCloud. O CloudKit tem suporte em dispositivos Mac OS X e iOS da Apple.

 [![](intro-to-cloudkit-images/image1.png "Como o CloudKit tem suporte em dispositivos Mac OS X e iOS da Apple")](intro-to-cloudkit-images/image1.png#lightbox)

O CloudKit usa a infraestrutura de conta do iCloud. Se houver um usuário conectado em uma conta do iCloud no dispositivo, o CloudKit usará sua ID para identificar o usuário. Se nenhuma conta estiver disponível, o acesso limitado somente leitura será fornecido.

O CloudKit dá suporte ao conceito de bancos de dados públicos e privados. Os bancos de dados públicos fornecem uma "sopa" de todos aqueles que o usuário tem acesso. Os bancos de dados privados destinam-se a armazenar as informações privadas associadas a um usuário específico.

O CloudKit dá suporte a dados estruturados e em massa. Ele é capaz de lidar com transferências de arquivos grandes diretamente. O CloudKit cuida da transferência eficiente de arquivos grandes para e dos servidores iCloud em segundo plano, liberando o desenvolvedor para se concentrar em outras tarefas.

> [!NOTE]
> É importante observar que o CloudKit é uma _tecnologia de transporte_. Ele não fornece nenhuma persistência; Ele só permite que um aplicativo envie e receba informações dos servidores com eficiência.

No momento da elaboração deste artigo, a Apple está inicialmente fornecendo CloudKit gratuitamente com um limite alto de largura de banda e capacidade de armazenamento. Para projetos ou aplicativos maiores com uma grande base de usuários, a Apple foi dedicada de que uma escala de preços acessível será fornecida.

## <a name="enabling-cloudkit-in-a-xamarin-application"></a>Habilitando CloudKit em um aplicativo Xamarin

Antes que um aplicativo Xamarin possa utilizar a estrutura CloudKit, o aplicativo deve ser provisionado corretamente conforme detalhado nos guias [trabalhando com recursos](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md) e [trabalhando com direitos](~/ios/deploy-test/provisioning/entitlements.md)

1. Abra o projeto no Visual Studio para Mac ou no Visual Studio.
2. No **Gerenciador de soluções**, abra o arquivo **info. plist** e verifique se o **identificador do pacote** corresponde ao que foi definido na **ID do aplicativo** criada como parte do provisionamento configurado:

    [![](intro-to-cloudkit-images/image26a.png "Insira o identificador do pacote")](intro-to-cloudkit-images/image26a-orig.png#lightbox "Info.plist file displaying Bundle Identifier")

3. Role para baixo até a parte inferior do arquivo **info. plist** e selecione **modos de segundo plano habilitados**, **atualizações de local** e **notificações remotas**:

    [![](intro-to-cloudkit-images/image27a.png "Selecionar modos de segundo plano habilitados, atualizações de local e notificações remotas")](intro-to-cloudkit-images/image27a-orig.png#lightbox "Info.plist file displaying background modes")
4. Clique com o botão direito do mouse no projeto do iOS na solução e selecione **Opções**.
5. Selecione **assinatura de pacote do IOS**, selecione a **identidade do desenvolvedor** e o **perfil de provisionamento** criados acima.
6. Verifique se os **direitos. plist** incluem o **icloud** , o **armazenamento de chave-valor** e o **CloudKit** .
7. Verifique se o **contêiner onipresença** existe para o aplicativo (como criado acima). Exemplo: `iCloud.com.your-company.CloudKitAtlas`
8. Salve as alterações no arquivo.

Com essas configurações em vigor, o aplicativo agora está pronto para acessar as APIs do CloudKit Framework.

## <a name="cloudkit-api-overview"></a>Visão geral da API do CloudKit

Antes de implementar o CloudKit em um aplicativo Xamarin iOS, este artigo vai abordar os conceitos básicos da estrutura CloudKit, que incluirá os seguintes tópicos:

1. **Contêineres** – silos isolados de comunicações do icloud.
2. **Bancos de dados** – público e privado estão disponíveis para o aplicativo.
3. **Registros** – o mecanismo no qual os dados estruturados são movidos para e de CloudKit.
4. **Zonas de registro** – são grupos de registros.
5. **Identificadores de registro** – são totalmente normalizados e representam o local específico do registro.
6. **Referência** – forneça relações pai-filho entre registros relacionados em um determinado banco de dados.
7. **Ativos** – permitir que arquivos de dados grandes e não estruturados sejam carregados no iCloud e associados a um determinado registro.

### <a name="containers"></a>Contêineres

Um determinado aplicativo em execução em um dispositivo iOS é sempre executado em outros aplicativos e serviços no dispositivo. No dispositivo cliente, o aplicativo será silodo ou colocado em área restrita de alguma forma. Em alguns casos, essa é uma área restrita literal e, em outros, o aplicativo está simplesmente em execução no próprio espaço de memória.

O conceito de pegar um aplicativo cliente e executá-lo separado de outros clientes é muito eficiente e oferece as seguintes vantagens:

1. **Segurança** – um aplicativo não pode interferir em outros aplicativos cliente ou no próprio sistema operacional.
1. **Estabilidade** – se o aplicativo cliente falhar, ele não poderá retirar outros aplicativos do sistema operacional.
1. **Privacidade** – cada aplicativo cliente tem acesso limitado às informações pessoais armazenadas no dispositivo.

O CloudKit foi projetado para fornecer as mesmas vantagens que as listadas acima e aplicá-las para trabalhar com informações baseadas em nuvem:

 [![](intro-to-cloudkit-images/image31.png "Aplicativos CloudKit se comunicam usando contêineres")](intro-to-cloudkit-images/image31.png#lightbox)

Assim como o aplicativo é um dos muitos em execução no dispositivo, portanto, as comunicações do aplicativo com o iCloud é um-de-muitos. Cada um desses diferentes silos de comunicação são chamados de contêineres.

Os contêineres são expostos na estrutura CloudKit por `CKContainer` meio da classe. Por padrão, um aplicativo se comunica com um contêiner e esse contêiner separa os dados para esse aplicativo. Isso significa que vários aplicativos podem armazenar informações na mesma conta do iCloud, mas essas informações nunca serão intermisturadas.

A Containerização de dados do iCloud também permite ao CloudKit encapsular informações do usuário. Dessa forma, o aplicativo terá algum acesso limitado à conta do iCloud e às informações de usuário armazenadas no, tudo isso enquanto ainda protege a privacidade e a segurança do usuário.

Os contêineres são totalmente gerenciados pelo desenvolvedor do aplicativo por meio do portal do WWDR. O namespace do contêiner é global em todos os desenvolvedores da Apple, portanto, o contêiner não deve ser exclusivo apenas para os aplicativos de um desenvolvedor específico, mas para todos os desenvolvedores e aplicativos da Apple.

A Apple sugere usar a notação inversa de DNS ao criar o namespace para contêineres de aplicativos. Exemplo:

```csharp
iCloud.com.company-name.application-name
```

Embora os contêineres sejam, por padrão, vinculado um-para-um a um determinado aplicativo, eles podem ser compartilhados entre aplicativos. Assim, vários aplicativos podem coordenar em um único contêiner. Um único aplicativo também pode se comunicar com vários contêineres.

### <a name="databases"></a>Bancos de dados

Uma das principais funções do CloudKit é pegar o modelo de dados de um aplicativo e fazer a replicação desse modelo para os servidores iCloud. Algumas informações destinam-se ao usuário que a criou, outras informações são dados públicos que podem ser criados por um usuário para uso público (como uma revisão de restaurante) ou podem ser informações que o desenvolvedor publicou para o aplicativo. Em ambos os casos, o público não é apenas um único usuário, mas é uma comunidade de pessoas.

 [![](intro-to-cloudkit-images/image32.png "Diagrama de contêiner CloudKit")](intro-to-cloudkit-images/image32.png#lightbox)

Dentro de um contêiner, primeiro e principal é o banco de dados público. É aí que todas as informações públicas residem e Mingles. Além disso, há vários bancos de dados privados individuais para cada usuário do aplicativo.

Ao executar em um dispositivo iOS, o aplicativo terá acesso apenas às informações para o usuário conectado no momento. Portanto, a exibição do aplicativo do contêiner será a seguinte:

 [![](intro-to-cloudkit-images/image33.png "A exibição de aplicativos do contêiner")](intro-to-cloudkit-images/image33.png#lightbox)

Ele só pode ver o banco de dados público e o banco de dados privado associado ao usuário do iCloud conectado no momento.

Os bancos de dados são expostos na estrutura CloudKit por meio `CKDatabase` da classe. Cada aplicativo tem acesso a dois bancos de dados: o banco de dados público e o privado.

O contêiner é o ponto de entrada inicial em CloudKit. O código a seguir pode ser usado para acessar o banco de dados público e privado do contêiner padrão do aplicativo:

```csharp
using CloudKit;
...

public CKDatabase PublicDatabase { get; set; }
public CKDatabase PrivateDatabase { get; set; }
...

// Get the default public and private databases for
// the application
PublicDatabase = CKContainer.DefaultContainer.PublicCloudDatabase;
PrivateDatabase = CKContainer.DefaultContainer.PrivateCloudDatabase;
```

Estas são as diferenças entre os tipos de banco de dados:

||Banco de dados público|Banco de dados privado|
|---|--- |--- |
|**Tipo de dados**|Dados compartilhados|Dados do usuário atual|
|**Cota**|Contabilizado na cota do desenvolvedor|Contabilizado na cota do usuário|
|**Permissões padrão**|Legível pelo mundo|Legível pelo usuário|
|**Editando permissões**|Funções de painel do iCloud por meio de um nível de classe de registro|N/D|

### <a name="records"></a>Registros

Os contêineres contêm bancos de dados e, dentro de bancos de dados, são registros. Registros são o mecanismo no qual os dados estruturados são movidos para e de CloudKit:

 [![](intro-to-cloudkit-images/image34.png "Contêineres armazenam bancos de dados e dentro de bancos de dados são registros")](intro-to-cloudkit-images/image34.png#lightbox)

Os registros são expostos na estrutura CloudKit por meio `CKRecord` da classe, que encapsula os pares chave-valor. Uma instância de um objeto em um aplicativo é equivalente a `CKRecord` a no CloudKit. Além disso, cada `CKRecord` um possui um tipo de registro, que é equivalente à classe de um objeto.

Os registros têm um esquema just-in-time, portanto, os dados são descritos para CloudKit antes de serem enviados para processamento. A partir desse ponto, o CloudKit interpretará as informações e tratará a logística do armazenamento e da recuperação do registro.

A `CKRecord` classe também dá suporte a uma grande variedade de metadados. Por exemplo, um registro contém informações sobre quando ele foi criado e o usuário que o criou. Um registro também contém informações sobre quando ele foi modificado pela última vez e o usuário que o modificou.

Os registros contêm a noção de uma marca de alteração. Esta é uma versão anterior de uma revisão de um determinado registro. A marca de alteração é usada como uma maneira leve de determinar se o cliente e o servidor têm a mesma versão de um determinado registro.

Conforme mencionado acima, `CKRecords` empacote os pares chave-valor e, como tal, os seguintes tipos de dados podem ser armazenados em um registro:

1. `NSString`
1. `NSNumber`
1. `NSData`
1. `NSDate`
1. `CLLocation`
1. `CKReferences`
1. `CKAssets`

Além dos tipos de valor único, um registro pode conter uma matriz homogênea de qualquer um dos tipos listados acima.

O código a seguir pode ser usado para criar um novo registro e armazená-lo em um banco de dados:

```csharp
using CloudKit;
...

private const string ReferenceItemRecordName = "ReferenceItems";
...

var newRecord = new CKRecord (ReferenceItemRecordName);
newRecord ["name"] = (NSString)nameTextField.Text;
await CloudManager.SaveAsync (newRecord);
```

### <a name="record-zones"></a>Zonas de registro

Os registros não existem por si mesmos em um determinado banco de dados – grupos de registros existem juntos dentro de uma zona de registro. As zonas de registro podem ser consideradas como tabelas em um banco de dados relacional tradicional:

 [![](intro-to-cloudkit-images/image35.png "Grupos de registros existem juntos dentro de uma zona de registro")](intro-to-cloudkit-images/image35.png#lightbox)

Pode haver vários registros dentro de uma determinada zona de registro e várias zonas de registro em um determinado banco de dados. Cada banco de dados contém uma zona de registro padrão:

 [![](intro-to-cloudkit-images/image36.png "Cada banco de dados contém uma zona de registro padrão e uma zona personalizada")](intro-to-cloudkit-images/image36.png#lightbox)

É aí que os registros são armazenados por padrão. Além disso, as zonas de registro personalizado podem ser criadas. As zonas de registro representam a granularidade base na qual as confirmações atômicas e Controle de Alterações são feitas.

## <a name="record-identifiers"></a>Identificadores de registro

Os identificadores de registro são representados como uma tupla, contendo um nome de registro fornecido pelo cliente e a zona na qual o registro existe. Os identificadores de registro têm as seguintes características:

- Eles são criados pelo aplicativo cliente.
- Eles são totalmente normalizados e representam o local específico do registro.
- Ao atribuir a ID exclusiva de um registro em um banco de dados externo ao nome do registro, eles podem ser usados para fazer a ponte de bancos de dados locais que não são armazenados no CloudKit.

Quando os desenvolvedores criam novos registros, eles podem optar por passar um identificador de registro. Se um identificador de registro não for especificado, um UUID será criado automaticamente e atribuído ao registro.

Quando os desenvolvedores criam novos identificadores de registro, eles podem optar por especificar a zona de registro à qual cada registro pertencerá. Se nenhum for especificado, a zona de registro padrão será usada.

Os identificadores de registro são expostos na estrutura CloudKit por `CKRecordID` meio da classe. O código a seguir pode ser usado para criar um novo identificador de registro:

```csharp
var recordID =  new CKRecordID("My Record");
```

### <a name="references"></a>Referências

As referências fornecem relações entre registros relacionados em um determinado banco de dados:

 [![](intro-to-cloudkit-images/image37.png "As referências fornecem relações entre registros relacionados em um determinado banco de dados")](intro-to-cloudkit-images/image37.png#lightbox)

No exemplo acima, os pais têm filhos para que o filho seja um registro filho do registro pai. A relação passa do registro filho para o registro pai e é referenciada como *referência regressiva*.

As referências são expostas na estrutura CloudKit `CKReference` por meio da classe. Eles são uma maneira de permitir que o servidor iCloud entenda a relação entre os registros.

As referências fornecem o mecanismo por trás de exclusões em cascata. Se um registro pai for excluído do banco de dados, todos os registros filho (conforme especificado em uma relação) também serão automaticamente excluídos do banco de dados.

> [!NOTE]
> Os ponteiros do pendente são uma possibilidade ao usar o CloudKit. Por exemplo, no momento em que o aplicativo obteve uma lista de ponteiros de registro, selecionou um registro e, em seguida, solicitou o registro, talvez ele não exista mais no banco de dados. Um aplicativo deve ser codificado para lidar com essa situação normalmente.

Embora não seja necessário, referências traseiras são preferenciais ao trabalhar com a estrutura CloudKit. A Apple ajustou o sistema para tornar esse o tipo de referência mais eficiente.

Ao criar uma referência, o desenvolvedor pode fornecer um registro que já está na memória ou criar uma referência a um identificador de registro. Se estiver usando um identificador de registro e a referência especificada não existia no banco de dados, um ponteiro pendente seria criado.

Veja a seguir um exemplo de criação de uma referência em relação a um registro conhecido:

```csharp
var reference = new CKReference(newRecord, new CKReferenceAction());
```

### <a name="assets"></a>Comerciais

Os ativos permitem que um arquivo de dados grandes e não estruturados seja carregado no iCloud e associado a um determinado registro:

 [![](intro-to-cloudkit-images/image38.png "Os ativos permitem que um arquivo de dados grandes e não estruturados seja carregado no iCloud e associado a um determinado registro")](intro-to-cloudkit-images/image38.png#lightbox)

No cliente, é criado `CKRecord` um que descreve o arquivo que será carregado no servidor icloud. Um `CKAsset` é criado para conter o arquivo e é vinculado ao registro que o descreve.

Quando o arquivo é carregado no servidor, o registro é colocado no banco de dados e o arquivo é copiado em um banco de dados de armazenamento em massa especial. Um link é criado entre o ponteiro de registro e o arquivo carregado.

Os ativos são expostos na estrutura CloudKit por meio `CKAsset` da classe e são usados para armazenar dados grandes e não estruturados. Como o desenvolvedor nunca quer ter dados grandes e não estruturados na memória, os ativos são implementados usando arquivos em disco.

Os ativos são de propriedade dos registros, o que permite que os ativos sejam recuperados do iCloud usando o registro como um ponteiro. Dessa forma, o servidor pode coletar ativos quando o registro proprietário do ativo é excluído.

Como `CKAssets` o tem como objetivo lidar com arquivos de dados grandes, a Apple desenvolveu o CloudKit para carregar e baixar os ativos com eficiência.

O código a seguir pode ser usado para criar um ativo e associá-lo ao registro:

```csharp
var fileUrl = new NSUrl("LargeFile.mov");
var asset = new CKAsset(fileUrl);
newRecord ["name"] = asset;
```

Agora, abordamos todos os objetos fundamentais dentro de CloudKit. Os contêineres são associados a aplicativos e contêm bancos de dados. Os bancos de dados contêm registros que são agrupados em zonas de registro e apontados por identificadores de registro. As relações pai-filho são definidas entre registros usando referências. Por fim, arquivos grandes podem ser carregados e associados a registros usando ativos.

## <a name="cloudkit-convenience-api"></a>API de conveniência CloudKit

A Apple oferece dois conjuntos de API diferentes para trabalhar com CloudKit:

- **API operacional** – oferece cada recurso único do CloudKit. Para aplicativos mais complexos, essa API fornece controle refinado sobre CloudKit.
- **API de conveniência** – oferece um subconjunto comum e pré-configurado de recursos do CloudKit. Ele fornece uma solução de acesso fácil e conveniente para incluir a funcionalidade CloudKit em um aplicativo iOS.

A API de conveniência é geralmente a melhor opção para a maioria dos aplicativos iOS e a Apple sugere começar com ele. O restante desta seção abordará os seguintes tópicos de API de conveniência:

- Salvando um registro.
- Buscando um registro.
- Atualizando um registro.

### <a name="common-setup-code"></a>Código de instalação comum

Antes de começar a usar a API de conveniência do CloudKit, há um código de instalação padrão necessário. Comece modificando o arquivo do `AppDelegate.cs` aplicativo e faça com que ele se pareça com o seguinte:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Foundation;
using UIKit;
using CloudKit;

namespace CloudKitAtlas
{
    [Register ("AppDelegate")]
    public partial class AppDelegate : UIApplicationDelegate
    {
        #region Computed Properties
        public override UIWindow Window { get; set;}
        public CKDatabase PublicDatabase { get; set; }
        public CKDatabase PrivateDatabase { get; set; }
        #endregion

        #region Override Methods
        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            application.RegisterForRemoteNotifications ();

            // Get the default public and private databases for
            // the application
            PublicDatabase = CKContainer.DefaultContainer.PublicCloudDatabase;
            PrivateDatabase = CKContainer.DefaultContainer.PrivateCloudDatabase;

            return true;
        }

        public override void RegisteredForRemoteNotifications (UIApplication application, NSData deviceToken)
        {
            Console.WriteLine ("Registered for Push notifications with token: {0}", deviceToken);
        }

        public override void FailedToRegisterForRemoteNotifications (UIApplication application, NSError error)
        {
            Console.WriteLine ("Push subscription failed");
        }

        public override void ReceivedRemoteNotification (UIApplication application, NSDictionary userInfo)
        {
            Console.WriteLine ("Push received");
        }
        #endregion
    }
}
```

O código acima expõe os bancos de dados CloudKit públicos e privados como atalhos para facilitar o trabalho com eles no restante do aplicativo.

Em seguida, adicione o seguinte código a qualquer exibição ou contêiner de exibição que usará CloudKit:

```csharp
using CloudKit;
...

#region Computed Properties
public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
#endregion
```

Isso adiciona um atalho para chegar ao `AppDelegate` e acessar os atalhos de banco de dados públicos e privados criados acima.

Com esse código em vigor, vamos dar uma olhada na implementação da API de conveniência do CloudKit em um aplicativo Xamarin iOS 8.

### <a name="saving-a-record"></a>Salvando um registro

Usando o padrão apresentado acima ao discutir os registros, o código a seguir criará um novo registro e usará a API de conveniência para salvá-lo no banco de dados público:

```csharp
private const string ReferenceItemRecordName = "ReferenceItems";
...

// Create a new record
var newRecord = new CKRecord (ReferenceItemRecordName);
newRecord ["name"] = (NSString)nameTextField.Text;

// Save it to the database
ThisApp.PublicDatabase.SaveRecord(newRecord, (record, err) => {
    // Was there an error?
    if (err != null) {
        ...
    }
});
```

Três itens a serem observados sobre o código acima:

1. Ao chamar o `SaveRecord` método `PublicDatabase`do, o desenvolvedor não precisa especificar como os dados são enviados, em qual zona ele está sendo gravado, etc. A API de conveniência está cuidando de todos esses detalhes.
1. A chamada é assíncrona e fornece uma rotina de chamada de retorno quando a chamada é concluída, seja com êxito ou falha. Se a chamada falhar, uma mensagem de erro será fornecida.
1. O CloudKit não fornece armazenamento/persistência local; é apenas um meio de transferência. Assim, quando é feita uma solicitação para salvar um registro, ele é imediatamente enviado aos servidores iCloud.

> [!NOTE]
> Devido à natureza "com perdas" das comunicações de rede móvel, onde as conexões são constantemente descartadas ou interrompidas, uma das primeiras considerações que o desenvolvedor deve fazer ao trabalhar com CloudKit é o tratamento de erros.

### <a name="fetching-a-record"></a>Buscando um registro

Com um registro criado e armazenado com êxito no servidor iCloud, use o seguinte código para recuperar o registro:

```csharp
// Create a record ID and fetch the record from the database
var recordID = new CKRecordID("MyRecordName");
ThisApp.PublicDatabase.FetchRecord(recordID, (record, err) => {
    // Was there an error?
    if (err != null) {
        ...
    }
});
```

Assim como no salvamento do registro, o código acima é assíncrono, simples e requer um excelente tratamento de erros.

### <a name="updating-a-record"></a>Atualizando um registro

Depois que um registro é obtido dos servidores iCloud, o código a seguir pode ser usado para modificar o registro e salvar as alterações no banco de dados:

```csharp
// Create a record ID and fetch the record from the database
var recordID = new CKRecordID("MyRecordName");
ThisApp.PublicDatabase.FetchRecord(recordID, (record, err) => {
    // Was there an error?
    if (err != null) {

    } else {
        // Modify the record
        record["name"] = (NSString)"New Name";

        // Save changes to database
        ThisApp.PublicDatabase.SaveRecord(record, (r, e) => {
            // Was there an error?
            if (e != null) {
                 ...
            }
        });
    }
});
```

O `FetchRecord` método `CKRecord` de retornará um se a chamada tiver sido bem-sucedida. `PublicDatabase` O aplicativo então modifica o registro e chama `SaveRecord` novamente para gravar as alterações de volta no banco de dados.

Esta seção mostrou o ciclo típico que um aplicativo usará ao trabalhar com a API de conveniência do CloudKit. O aplicativo salvará registros no iCloud, recuperará esses registros do iCloud, modificará os registros e salvará essas alterações novamente no iCloud.

## <a name="designing-for-scalability"></a>Projetando para escalabilidade

Até agora, este artigo examinou o armazenamento e a recuperação do modelo de objeto inteiro de um aplicativo dos servidores iCloud, toda vez que vai trabalhar. Embora essa abordagem funcione bem com uma pequena quantidade de dados e uma base de usuários muito pequena, ela não é bem dimensionada quando a quantidade de informações e/ou a base de usuários aumenta.

### <a name="big-data-tiny-device"></a>Big data, pequeno dispositivo

Quanto mais popular um aplicativo se torna, mais dados no banco e o menos viável é ter um cache desses dados inteiros no dispositivo. As técnicas a seguir podem ser usadas para resolver esse problema:

- **Mantenha os grandes dados na nuvem – o** CloudKit foi projetado para lidar com grandes volumes de dados com eficiência.
- O **cliente só deve exibir uma fatia desses dados** – desative o mínimo de dados necessários para lidar com qualquer tarefa em um determinado momento.
- As **exibições de cliente podem ser alteradas** – como cada usuário tem preferências diferentes, a fatia de dados que está sendo exibida pode ser alterada de usuário para usuário e a exibição individual de qualquer fatia específica do usuário pode ser diferente.
- **O cliente usa consultas para concentrar o ponto de vista** – as consultas permitem que o usuário exiba um pequeno subconjunto de um conjunto de um maior que existe na nuvem.

### <a name="queries"></a>Consultas

Conforme mencionado acima, as consultas permitem que o desenvolvedor selecione um pequeno subconjunto do conjunto de grandes que existe na nuvem. As consultas são expostas na estrutura CloudKit `CKQuery` por meio da classe.

Uma consulta combina três coisas diferentes: um tipo de registro `RecordType`(), um predicado ( `NSPredicate`) e, opcionalmente, um `NSSortDescriptors`descritor de classificação (). O CloudKit dá suporte `NSPredicate`à maior parte do.

#### <a name="supported-predicates"></a>Predicados com suporte

O CloudKit dá suporte aos seguintes `NSPredicates` tipos de ao trabalhar com consultas:

1. Registros correspondentes em que o nome é igual a um valor armazenado em uma variável:

    ```csharp
    NSPredicate.FromFormat(string.Format("name = '{0}'", recordName))
    ```

2. Permite que a correspondência seja baseada em um valor de chave dinâmica, para que a chave não precise ser conhecida no momento da compilação:

    ```csharp
    NSPredicate.FromFormat(string.Format("{0} = '{1}'", key, value))
    ```

3. Registros correspondentes em que o valor do registro é maior que o valor especificado:

    ```csharp
    NSPredicate.FromFormat(string.Format("start > {0}", (NSDate)date))
    ```

4. Registros correspondentes em que a localização do registro está dentro de 100 metros do local determinado:

    ```csharp
    var location = new CLLocation(37.783,-122.404);
    var predicate = NSPredicate.FromFormat(string.Format("distanceToLocation:fromLocation(Location,{0}) < 100", location));
    ```

5. O CloudKit dá suporte a uma pesquisa com tokens. Essa chamada criará dois tokens, um `after` para e outro `session`para. Ele retornará um registro que contém esses dois tokens:

    ```csharp
    NSPredicate.FromFormat(string.Format("ALL tokenize({0}, 'Cdl') IN allTokens", "after session"))
    ```

6. O CloudKit dá suporte a predicados `AND` compostos que ingressaram usando o operador.

    ```csharp
    NSPredicate.FromFormat(string.Format("start > {0} AND name = '{1}'", (NSDate)date, recordName))
    ```

#### <a name="creating-queries"></a>Criando consultas

O código a seguir pode ser usado para criar `CKQuery` um em um aplicativo Xamarin Ios 8:

```csharp
var recordName = "MyRec";
var predicate = NSPredicate.FromFormat(string.Format("name = '{0}'", recordName));
var query = new CKQuery("CloudRecords", predicate);
```

Primeiro, ele cria um predicado para selecionar somente os registros que correspondem a um determinado nome. Em seguida, ele cria uma consulta que selecionará os registros do tipo de registro fornecido que correspondem ao predicado.

#### <a name="performing-a-query"></a>Executando uma consulta

Depois que uma consulta tiver sido criada, use o seguinte código para executar a consulta e processar os registros retornados:

```csharp
var recordName = "MyRec";
var predicate = NSPredicate.FromFormat(string.Format("name = {0}", recordName));
var query = new CKQuery("CloudRecords", predicate);

ThisApp.PublicDatabase.PerformQuery(query, CKRecordZone.DefaultRecordZone().ZoneId, (NSArray results, NSError err) => {
    // Was there an error?
    if (err != null) {
       ...
    } else {
        // Process the returned records
        for(nint i = 0; i < results.Count; ++i) {
            var record = (CKRecord)results[i];
        }
    }
});
```

O código acima usa a consulta criada acima e a executa no banco de dados público. Como nenhuma zona de registro é especificada, todas as zonas são pesquisadas. Se nenhum erro ocorreu, uma matriz de `CKRecords` será retornada correspondendo aos parâmetros da consulta.

A maneira de pensar sobre as consultas é que elas são sondas e são ótimas na divisão por meio de grandes conjuntos de altos. No entanto, as consultas não são adequadas para conjuntos de valores grandes, principalmente, estáticos devido aos seguintes motivos:

- Eles são ruins para a vida útil da bateria do dispositivo.
- Eles são ruins para o tráfego de rede.
- Eles são ruins para a experiência do usuário porque as informações que eles veem são limitadas pela frequência com que o aplicativo está sondando o banco de dados. Os usuários hoje esperam notificações por push quando algo é alterado.

### <a name="subscriptions"></a>Assinaturas

Ao lidar com conjuntos de valores grandes, principalmente estáticos, a consulta não deve ser executada no dispositivo cliente, ela deve ser executada no servidor em nome do cliente. A consulta deve ser executada em segundo plano e deve ser executada após cada gravação de registro único, seja pelo dispositivo atual ou outro dispositivo tocando no mesmo banco de dados.

Por fim, uma notificação por push deve ser enviada a todos os dispositivos conectados ao banco de dados quando a consulta do lado do servidor é executada.

As assinaturas são expostas na estrutura CloudKit por meio `CKSubscription` da classe. Eles combinam um tipo de `RecordType`registro (), um `NSPredicate`predicado () e uma `Push`notificação por push da Apple ().

> [!NOTE]
> Os envios por push do CloudKit são ligeiramente aumentados, pois contêm uma carga contendo informações específicas do CloudKit, como o que causou o envio do push.

#### <a name="how-subscriptions-work"></a>Como as assinaturas funcionam

Antes de implementar a C# assinatura no código, vamos fazer uma rápida visão geral de como as assinaturas funcionam:

 [![](intro-to-cloudkit-images/image39.png "Uma visão geral de como as assinaturas funcionam")](intro-to-cloudkit-images/image39.png#lightbox)

O gráfico acima mostra o processo de assinatura típico da seguinte maneira:

1. O dispositivo cliente cria uma nova assinatura que contém o conjunto de condições que irá disparar a assinatura e uma notificação por push que será enviada quando o gatilho ocorrer.
2. A assinatura é enviada ao banco de dados onde é adicionada à coleção de assinaturas existentes.
3. Um segundo dispositivo cria um novo registro e salva esse registro no banco de dados.
4. O banco de dados pesquisa a lista de assinaturas para ver se o novo registro corresponde a qualquer uma de suas condições.
5. Se uma correspondência for encontrada, a notificação por push será enviada ao dispositivo que registrou a assinatura com informações sobre o registro que fez com que ele fosse disparado.

Com esse conhecimento em vigor, vamos examinar a criação de assinaturas em um aplicativo Xamarin iOS 8.

#### <a name="creating-subscriptions"></a>Criando assinaturas

O código a seguir pode ser usado para criar uma assinatura:

```csharp
// Create a new subscription
DateTime date;
var predicate = NSPredicate.FromFormat(string.Format("start > {0}", (NSDate)date));
var subscription = new CKSubscription("RecordType", predicate, CKSubscriptionOptions.FiresOnRecordCreation);

// Describe the type of notification
var notificationInfo = new CKNotificationInfo();
notificationInfo.AlertLocalizationKey = "LOCAL_NOTIFICATION_KEY";
notificationInfo.SoundName = "ping.aiff";
notificationInfo.ShouldBadge = true;

// Attach the notification info to the subscription
subscription.NotificationInfo = notificationInfo;
```

Primeiro, ele cria um predicado que fornece a condição para disparar a assinatura. Em seguida, ele cria a assinatura em um tipo de registro específico e define a opção de quando o gatilho é testado. Por fim, ele define o tipo de notificação que ocorrerá quando a assinatura for disparada e anexará a assinatura.

### <a name="saving-subscriptions"></a>Salvando assinaturas

Com a assinatura criada, o seguinte código irá salvá-lo no banco de dados:

```csharp
// Save the subscription to the database
ThisApp.PublicDatabase.SaveSubscription(subscription, (s, err) => {
    // Was there an error?
    if (err != null) {

    }
});
```

Usando a API de conveniência, a chamada é assíncrona, simples e fornece tratamento fácil de erros.

#### <a name="handling-push-notifications"></a>Manipulando notificações por push

Se o desenvolvedor tiver usado anteriormente o APS (Apple Push Notifications), o processo de lidar com as notificações geradas pelo CloudKit deve ser familiar.

No, substitua a `ReceivedRemoteNotification` classe da seguinte maneira: `AppDelegate.cs`

```csharp
public override void ReceivedRemoteNotification (UIApplication application, NSDictionary userInfo)
{
    // Parse the notification into a CloudKit Notification
    var notification = CKNotification.FromRemoteNotificationDictionary (userInfo);

    // Get the body of the message
    var alertBody = notification.AlertBody;

    // Was this a query?
    if (notification.NotificationType == CKNotificationType.Query) {
        // Yes, convert to a query notification and get the record ID
        var query = notification as CKQueryNotification;
        var recordID = query.RecordId;
    }
}
```

O código acima solicita que o CloudKit analise o userInfo em uma notificação CloudKit. Em seguida, as informações são extraídas sobre o alerta. Por fim, o tipo de notificação é testado e a notificação é manipulada de acordo.

Esta seção mostrou como responder aos Big data, pequeno problema de dispositivo apresentado acima usando consultas e assinaturas. O aplicativo deixará seus grandes dados na nuvem e usará essas tecnologias para fornecer modos de exibição para esse DataSet.

## <a name="cloudkit-user-accounts"></a>CloudKit contas de usuário

Conforme observado no início deste artigo, o CloudKit é criado sobre a infraestrutura do iCloud existente. A seção a seguir abordará, em detalhes, como as contas são expostas a um desenvolvedor usando a API do CloudKit.

### <a name="authentication"></a>Autenticação

Ao lidar com contas de usuário, a primeira consideração é a autenticação. O CloudKit dá suporte à autenticação por meio do usuário conectado no momento no dispositivo do iCloud. A autenticação ocorre em segundo plano e é tratada pelo iOS. Dessa forma, os desenvolvedores nunca precisam se preocupar com os detalhes da implementação da autenticação. Eles só testam para ver se um usuário está conectado.

### <a name="user-account-information"></a>Informações da conta de usuário

O CloudKit fornece as seguintes informações de usuário para o desenvolvedor:

- **Identidade** – uma maneira de identificar exclusivamente o usuário.
- **Metadados** – a capacidade de salvar e recuperar informações sobre os usuários.
- **Privacidade** – todas as informações são tratadas em um oportuno atento à privacidade. Nada é exposto, a menos que o usuário concorde com ele.
- **Descoberta** – oferece aos usuários a capacidade de descobrir seus amigos que estão usando o mesmo aplicativo.

Em seguida, veremos esses tópicos detalhadamente.

#### <a name="identity"></a>Identidade

Como mencionado acima, o CloudKit fornece uma maneira para o aplicativo identificar exclusivamente um determinado usuário:

 [![](intro-to-cloudkit-images/image40.png "Identificar exclusivamente um determinado usuário")](intro-to-cloudkit-images/image40.png#lightbox)

Há um aplicativo cliente em execução nos dispositivos de um usuário e todos os bancos de dados particulares do usuário específico dentro do contêiner CloudKit. O aplicativo cliente será vinculado a um desses usuários específicos. Isso se baseia no usuário que está conectado no iCloud localmente no dispositivo.

Como isso é proveniente do iCloud, há um armazenamento de apoio avançado de informações do usuário. E como o iCloud está realmente hospedando o contêiner, ele pode correlacionar os usuários. No gráfico acima, o usuário cuja conta `user@icloud.com` do icloud está vinculada ao cliente atual.

Em um contêiner por contêiner, uma ID de usuário exclusiva e gerada aleatoriamente é criada e associada à conta do iCloud do usuário (endereço de email). Essa ID de usuário é retornada ao aplicativo e pode ser usada de qualquer maneira que o desenvolvedor se comportar.

> [!NOTE]
> Aplicativos diferentes em execução no mesmo dispositivo para o mesmo usuário do iCloud terão IDs de usuário diferentes, pois eles estão conectados a diferentes contêineres CloudKit.

O código a seguir obtém a ID de usuário CloudKit para o usuário do iCloud conectado no momento no dispositivo:

```csharp
public CKRecordID UserID { get; set; }
...

// Get the CloudKit User ID
CKContainer.DefaultContainer.FetchUserRecordId ((recordID, err) => {
    // Was there an error?
    if (err!=null) {
        Console.WriteLine("Error: {0}", err.LocalizedDescription);
    } else {
        // Save user ID
        UserID = recordID;
    }
});
```

O código acima está solicitando que o contêiner CloudKit forneça a ID do usuário conectado no momento. Como essas informações são provenientes do servidor iCloud, a chamada é assíncrona e o tratamento de erros é necessário.

#### <a name="metadata"></a>Metadados

Cada usuário no CloudKit tem metadados específicos que os descrevem. Esses metadados são representados como um registro CloudKit:

 [![](intro-to-cloudkit-images/image41.png "Cada usuário no CloudKit tem metadados específicos que os descrevem")](intro-to-cloudkit-images/image41.png#lightbox)

Olhando dentro do banco de dados privado para um usuário específico de um contêiner, há um registro que define esse usuário. Há muitos registros de usuário dentro do banco de dados público, um para cada usuário do contêiner. Um deles terá uma ID de registro que corresponde à ID de registro do usuário conectado no momento.

Os registros de usuário no banco de dados público são legíveis para o mundo. Eles são tratados, na maior parte, como um registro comum e têm um tipo de `CKRecordTypeUserRecord`. Esses registros são reservados pelo sistema e não estão disponíveis para consultas.

Use o código a seguir para acessar um registro de usuário:

```csharp
public CKRecord UserRecord { get; set; }
...

// Get the user's record
PublicDatabase.FetchRecord(UserID, (record ,er) => {
    //was there an error?
    if (er != null) {
        Console.WriteLine("Error: {0}", er.LocalizedDescription);
    } else {
        // Save the user record
        UserRecord = record;
    }
});
```

O código acima está solicitando que o banco de dados público retorne o registro de usuário para o usuário que o ID acessado acima. Como essas informações são provenientes do servidor iCloud, a chamada é assíncrona e o tratamento de erros é necessário.

#### <a name="privacy"></a>Privacidade

O CloudKit era design, por padrão, para proteger a privacidade do usuário conectado no momento. Nenhuma informação de identificação pessoal sobre o usuário é exposta por padrão. Há alguns casos em que o aplicativo precisará de informações limitadas sobre o usuário.

Nesses casos, o aplicativo pode solicitar que o usuário divulgue essas informações. Uma caixa de diálogo será apresentada ao usuário solicitando sua aceitação de expor suas informações de conta.

#### <a name="discovery"></a>Descoberta

Supondo que o usuário como aceito para permitir o acesso limitado ao aplicativo às suas informações de conta de usuário, eles podem ser detectáveis para outros usuários do aplicativo:

 [![](intro-to-cloudkit-images/image42.png "Um usuário pode ser detectável para outros usuários do aplicativo")](intro-to-cloudkit-images/image42.png#lightbox)

O aplicativo cliente está se comunicando com um contêiner e o contêiner está conversando no iCloud para acessar as informações do usuário. O usuário pode fornecer um endereço de email e a descoberta pode ser usada para obter informações sobre o usuário. Opcionalmente, a ID de usuário também pode ser usada para descobrir informações sobre o usuário.

O CloudKit também fornece uma maneira de descobrir informações sobre qualquer usuário que possa ser amigos do usuário conectado no momento, consultando o catálogo de endereços inteiro. O processo CloudKit efetuará pull do catálogo de contatos do usuário e usará os endereços de email para ver se ele pode encontrar outros usuários do aplicativo que correspondam a esses endereços.

Isso permite que o aplicativo aproveite o livro de contatos do usuário sem fornecer acesso a ele ou pedindo que o usuário aprove o acesso aos contatos. Em nenhum momento, as informações de contato disponibilizadas para o aplicativo, somente o processo CloudKit tem acesso.

Para recapitular, há três tipos diferentes de entradas disponíveis para a descoberta de usuário:

- **ID de registro de usuário** – a descoberta pode ser feita em relação à ID de usuário atualmente conectada no usuário CloudKit.
- **Endereço de email do usuário** – o usuário pode fornecer um endereço de email e ele pode ser usado para descoberta.
- **Catálogo de contatos** – o catálogo de endereços do usuário pode ser usado para descobrir usuários do aplicativo que têm o mesmo endereço de email, conforme listado em seus contatos.

A descoberta de usuário retornará as seguintes informações:

- **ID de registro de usuário** -a ID exclusiva de um usuário no banco de dados público.
- **First e Last Name** -conforme armazenado no banco de dados público.

Essas informações serão retornadas somente para usuários que tenham optado por descoberta.

O código a seguir descobrirá informações sobre o usuário atualmente conectado ao iCloud no dispositivo:

```csharp
public CKDiscoveredUserInfo UserInfo { get; set; }
...

// Get the user's metadata
CKContainer.DefaultContainer.DiscoverUserInfo(UserID, (info, e) => {
    // Was there an error?
    if (e != null) {
        Console.WriteLine("Error: {0}", e.LocalizedDescription);
    } else {
        // Save the user info
        UserInfo = info;
    }
});
```

Use o código a seguir para consultar todos os usuários no catálogo de contatos:

```csharp
// Ask CloudKit for all of the user's friends information
CKContainer.DefaultContainer.DiscoverAllContactUserInfos((info, er) => {
    // Was there an error
    if (er != null) {
        Console.WriteLine("Error: {0}", er.LocalizedDescription);
    } else {
        // Process all returned records
        for(int i = 0; i < info.Count(); ++i) {
            // Grab a user
            var userInfo = info[i];
        }
    }
});
```

Nesta seção, abordamos as quatro áreas principais de acesso à conta de um usuário que o CloudKit pode fornecer a um aplicativo. Desde obter a identidade e os metadados do usuário até as políticas de privacidade criadas no CloudKit e, por fim, a capacidade de descobrir outros usuários do aplicativo.

## <a name="the-development-and-production-environments"></a>Os ambientes de desenvolvimento e produção

O CloudKit fornece ambientes de desenvolvimento e produção separados para dados e tipos de registro de um aplicativo. O ambiente de desenvolvimento é um ambiente mais flexível que está disponível somente para membros de uma equipe de desenvolvimento. Quando um aplicativo adiciona um novo campo a um registro e salva esse registro no ambiente de desenvolvimento, o servidor atualiza as informações de esquema automaticamente.

O desenvolvedor pode usar esse recurso para fazer alterações em um esquema durante o desenvolvimento, o que poupa tempo. Uma limitação é que, depois que um campo tiver sido adicionado a um registro, o tipo de dados associado a esse campo não poderá ser alterado programaticamente. Para alterar o tipo de um campo, o desenvolvedor deve excluir o campo no [painel do CloudKit](https://icloud.developer.apple.com/dashboard/) e adicioná-lo novamente com o novo tipo.

Antes de implantar o aplicativo, o desenvolvedor pode migrar seu esquema e dados para o ambiente de produção usando o **painel do CloudKit**. Durante a execução no ambiente de produção, o servidor impede que um aplicativo altere o esquema programaticamente. O desenvolvedor ainda pode fazer alterações com o **painel do CloudKit** , mas as tentativas de adicionar campos a um registro no ambiente de produção resultam em erros.

> [!NOTE]
> O simulador do iOS funciona apenas com o **ambiente de desenvolvimento**. Quando o desenvolvedor está pronto para testar um aplicativo em um **ambiente de produção**, é necessário um dispositivo IOS físico.

## <a name="shipping-a-cloudkit-enabled-app"></a>Enviando um aplicativo habilitado para CloudKit

Antes de enviar um aplicativo que usa CloudKit, ele precisará ser configurado para direcionar o **ambiente de produção CloudKit** ou o aplicativo será rejeitado pela Apple.

Faça o seguinte:

1. No Visual Studio para ma, compile o aplicativo para o**dispositivo IOS**de **versão** > :

    [![](intro-to-cloudkit-images/shipping01.png "Compilar o aplicativo para a versão")](intro-to-cloudkit-images/shipping01.png#lightbox)

2. No menu **Compilar** , selecione **arquivo morto**:

    [![](intro-to-cloudkit-images/shipping02.png "Selecionar arquivo morto")](intro-to-cloudkit-images/shipping02.png#lightbox)

3. O **arquivo** será criado e exibido no Visual Studio para Mac:

    [![](intro-to-cloudkit-images/shipping03.png "O arquivo será criado e exibido")](intro-to-cloudkit-images/shipping03.png#lightbox)

4. Inicie o **Xcode**.
5. No menu **janela** , selecione **organizador**:

    [![](intro-to-cloudkit-images/shipping04.png "Selecionar organizador")](intro-to-cloudkit-images/shipping04.png#lightbox)

6. Selecione o arquivo morto do aplicativo e clique no botão **Exportar...** :

    [![](intro-to-cloudkit-images/shipping05.png "O arquivo morto do aplicativo")](intro-to-cloudkit-images/shipping05.png#lightbox)

7. Selecione um método para exportar e clique no botão **Avançar** :

    [![](intro-to-cloudkit-images/shipping06.png "Selecionar um método para exportar")](intro-to-cloudkit-images/shipping06.png#lightbox)

8. Selecione a **equipe de desenvolvimento** na lista suspensa e clique no botão **escolher** :

    [![](intro-to-cloudkit-images/shipping07.png "Selecione a equipe de desenvolvimento na lista suspensa")](intro-to-cloudkit-images/shipping07.png#lightbox)

9. Selecione **produção** na lista suspensa e clique no botão **Avançar** :

    [![](intro-to-cloudkit-images/shipping08.png "Selecione produção na lista suspensa")](intro-to-cloudkit-images/shipping08.png#lightbox)

10. Examine a configuração e clique no botão **Exportar** :

    [![](intro-to-cloudkit-images/shipping09.png "Examinar a configuração")](intro-to-cloudkit-images/shipping09.png#lightbox)

11. Escolha um local para gerar o arquivo de `.ipa` aplicativo resultante.

O processo é semelhante para enviar o aplicativo diretamente para o iTunes Connect, basta clicar no botão **Enviar...** em vez da exportação... Depois de selecionar um arquivo morto na janela do organizador.

## <a name="when-to-use-cloudkit"></a>Quando usar o CloudKit

Como vimos neste artigo, o CloudKit fornece uma maneira fácil de um aplicativo armazenar e recuperar informações dos servidores iCloud. Dito isso, o CloudKit não está obsoleto ou não substitui nenhuma das ferramentas ou estruturas existentes.

### <a name="use-cases"></a>Casos de uso

Os seguintes casos de uso devem ajudar o desenvolvedor a decidir quando usar uma estrutura ou tecnologia específica do iCloud:

- **repositório de chave-valor do icloud** – mantém uma pequena quantidade de dados atualizada de forma assíncrona e é ótimo para trabalhar com as preferências do aplicativo. No entanto, ele é restrito a uma quantidade muito pequena de informações.
- **unidade icloud** – criada sobre as APIs de documentos do icloud existentes e fornece uma API simples para sincronizar dados não estruturados do sistema de arquivos. Ele fornece um cache offline completo no Mac OS X e é ótimo para aplicativos centrados no documento.
- **dados principais do icloud** – permite que os dados sejam replicados entre todos os dispositivos do usuário. Os dados são de usuário único e ótimo para manter dados estruturados e privados em sincronia.
- **CloudKit** – fornece dados públicos estrutura e em massa e é capaz de lidar com grandes volumes de grande porte e grandes arquivos não estruturados. Ele está vinculado à conta do iCloud do usuário e fornece transferência de dados direcionada ao cliente.

Ao manter esses casos de uso em mente, o desenvolvedor deve escolher a tecnologia correta do iCloud para fornecer a funcionalidade de aplicativo necessária e fornecer boa escalabilidade para o crescimento futuro.

## <a name="summary"></a>Resumo

Este artigo abordou uma breve introdução à API do CloudKit. Ele mostrou como provisionar e configurar um aplicativo Xamarin iOS para usar o CloudKit. Ele abordou os recursos da API de conveniência do CloudKit. Ele mostra como criar um aplicativo habilitado para CloudKit para escalabilidade usando consultas e assinaturas. E, finalmente, ele mostrou as informações de conta de usuário que são expostas a um aplicativo pelo CloudKit.

## <a name="related-links"></a>Links relacionados

- [CloudKitAtlas (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-cloudkitatlas)
- [Introdução ao iOS 8](~/ios/platform/introduction-to-ios8.md)
- [Criando um perfil de provisionamento](~/ios/get-started/installation/device-provisioning/index.md)
