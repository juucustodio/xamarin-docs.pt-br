---
title: CloudKit no xamarin. IOS
description: Este documento descreve como trabalhar com CloudKit no xamarin. IOS. Ele fornece uma visão geral do CloudKit e descreve como habilitar a ele, a API de conveniência do CloudKit, escalabilidade, as contas de usuário e ambientes de desenvolvimento e produção.
ms.prod: xamarin
ms.assetid: 66B207F2-FAA0-4551-B43B-3DB9F620C397
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/11/2016
ms.openlocfilehash: daea27472ac7c0578c1cfd79ebd96428212fafb3
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61165082"
---
# <a name="cloudkit-in-xamarinios"></a>CloudKit no xamarin. IOS

A estrutura de CloudKit simplifica o desenvolvimento de aplicativos do iCloud esse acesso. Isso inclui a recuperação de dados de aplicativo e direitos de ativo, bem como sendo capaz de armazenar com segurança informações do aplicativo. Esse kit oferece aos usuários uma camada de anonimato, permitindo o acesso a aplicativos com sua IDs do iCloud sem compartilhar informações pessoais.

Os desenvolvedores podem se concentrar em seus aplicativos do lado do cliente e permitir que o iCloud eliminar a necessidade de escrever a lógica do aplicativo do lado do servidor. CloudKit fornece autenticação, bancos de dados públicos e privados e os dados estruturados e os serviços de armazenamento ativo.

> [!IMPORTANT]
> A Apple [fornece ferramentas](https://developer.apple.com/support/allowing-users-to-manage-data/) para ajudar os desenvolvedores a lidar adequadamente com o GDPR (Regulamento Geral sobre a Proteção de Dados) da União Europeia.

## <a name="requirements"></a>Requisitos

O exemplo a seguir é necessário para concluir as etapas apresentadas neste artigo:

-  **Xcode e o SDK do iOS** – Xcode da Apple e iOS 8 APIs precisam ser instalado e configurado no computador do desenvolvedor.
-  **O Visual Studio para Mac** – a versão mais recente do Visual Studio para Mac deve ser instalada e configurada no dispositivo do usuário.
-  **iOS 8 dispositivo** – um dispositivo iOS executando a versão mais recente do iOS 8 para teste.

## <a name="what-is-cloudkit"></a>O que é CloudKit?

CloudKit é uma maneira de conceder o acesso de desenvolvedor para o iCloud de servidores. Ele fornece a base para o iCloud Drive e biblioteca de fotos do iCloud. CloudKit tem suporte no Mac OS X e Apple iOS dispositivos.

 [![](intro-to-cloudkit-images/image1.png "O suporte do CloudKit no Mac OS X e dispositivos iOS da Apple")](intro-to-cloudkit-images/image1.png#lightbox)

CloudKit usa a infraestrutura de conta do iCloud. Se houver um usuário conectado a um conta no dispositivo no iCloud, CloudKit usará sua ID para identificar o usuário. Se nenhuma conta estiver disponível, acesso somente leitura limitado será fornecido.

CloudKit dá suporte ao conceito de bancos de dados públicos e privados. Bancos de dados públicos fornecem uma "mistura" de todos os dados que o usuário tem acesso ao. Bancos de dados privados destinam-se para armazenar os dados privados associados a um usuário específico.

CloudKit dá suporte a dados estruturados e em massa. Ele é capaz de lidar com grandes transferências de arquivos diretamente. CloudKit se encarrega de transferir com eficiência os arquivos grandes de e para o iCloud servidores em segundo plano, liberando o desenvolvedor se concentre em outras tarefas.

> [!NOTE]
> É importante observar que CloudKit é um _tecnologia de transporte_. Ele não fornece nenhuma persistência; Ele permite somente um aplicativo para enviar e receber informações de servidores com eficiência.

A partir da redação deste artigo, Apple é inicialmente fornecendo CloudKit gratuitamente com um limite de alta capacidade de armazenamento e largura de banda. Para projetos maiores ou aplicativos com uma grande base de usuários, a Apple tem sugerimos que será fornecida uma escala de preço acessível.


## <a name="enabling-cloudkit-in-a-xamarin-application"></a>Habilitando CloudKit em um aplicativo Xamarin

Antes de um aplicativo Xamarin pode utilizar a estrutura do CloudKit, o aplicativo deve ser provisionado corretamente conforme detalhado no [trabalhando com capacidades](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md) e [trabalhando com direitos](~/ios/deploy-test/provisioning/entitlements.md) guias

1.  Abra o projeto no Visual Studio para Mac ou Visual Studio.
2.  No **Gerenciador de soluções**, abra o **Info. plist** de arquivo e verifique se o **identificador de pacote** corresponde ao que foi definido no **App ID**criado como parte do provisionamento do conjunto de:
 
    [![](intro-to-cloudkit-images/image26a.png "Insira o identificador de pacote")](intro-to-cloudkit-images/image26a-orig.png#lightbox "Info.plist file displaying Bundle Identifier")

3.  Role para baixo até a parte inferior da **Info. plist** do arquivo e selecione **modos de segundo plano habilitados**, **atualizações de local** e **notificações remotas**:

    [![](intro-to-cloudkit-images/image27a.png "Selecionar modos de segundo plano habilitado, as atualizações de local e notificações remotas")](intro-to-cloudkit-images/image27a-orig.png#lightbox "Info.plist file displaying background modes")
4.  Clique com botão direito na solução e selecione o projeto do iOS **opções**.
5.  Selecione **assinatura do pacote iOS**, selecione o **identidade de desenvolvedor** e **perfil de provisionamento** criado acima.
6.  Verifique se o **Entitlements. plist** inclui **habilitar iCloud** , **armazenamento chave-valor** e **CloudKit** .
7.  Verifique se o **onipresença contêiner** existe para o aplicativo (conforme criado acima). Exemplo: `iCloud.com.your-company.CloudKitAtlas`
8.  Salve as alterações no arquivo.


Com essas configurações em vigor, o aplicativo agora está pronto para acessar as APIs do CloudKit do Framework.

## <a name="cloudkit-api-overview"></a>Visão geral da API do CloudKit

Antes de implementar CloudKit em um aplicativo Xamarin iOS, este artigo vai abrangem os conceitos básicos do CloudKit Framework, que inclui os seguintes tópicos:

1.  **Contêineres** – isolada silos de comunicações do iCloud.
2.  **Bancos de dados** – privados e públicos estão disponíveis para o aplicativo.
3.  **Registros** – o mecanismo em que dados estruturados são movidos para e do CloudKit.
4.  **Zonas de registro** – são grupos de registros.
5.  **Registrar identificadores** – são totalmente normalizados e representam o local específico do registro.
6.  **Referência** – fornecer relações entre os registros relacionados dentro de um determinado banco de dados de pai-filho.
7.  **Ativos** – permitir que o arquivo de dados grandes e não estruturados para ser carregado com o iCloud e associado a um determinado registro.


### <a name="containers"></a>Contêineres

Um determinado aplicativo em execução em um dispositivo iOS está sempre em execução lado junto a outros aplicativos e serviços no dispositivo. No dispositivo cliente, o aplicativo vai ser em silos ou na área restrita de alguma forma. Em alguns casos, isso é uma área restrita de literal e em outros, o aplicativo é simplesmente executar nela é o espaço de memória próprias.

O conceito de pegar um aplicativo cliente e executá-lo separados de outros clientes é muito eficiente e fornece as seguintes vantagens:

1.  **Segurança** – um aplicativo não consegue interferir em outros aplicativos cliente ou o próprio sistema operacional.
1.  **Estabilidade** – se o aplicativo cliente falhar, ela não pode levar os outros aplicativos do sistema operacional.
1.  **Privacidade** – cada aplicativo cliente tem acesso limitado às informações pessoais armazenados no dispositivo.


CloudKit foi projetado para fornecer as mesmas vantagens, conforme listado acima e aplicá-las a trabalhar com informações baseadas em nuvem:

 [![](intro-to-cloudkit-images/image31.png "CloudKit aplicativos se comunicam usando contêineres")](intro-to-cloudkit-images/image31.png#lightbox)

Assim como o aplicativo que está sendo executado no dispositivo, um dos muitos portanto é comunicações do aplicativo com o iCloud um dos muitos. Cada um desses silos de comunicação diferentes são chamados de contêineres.

Contêineres são expostos no CloudKit Framework por meio de `CKContainer` classe. Por padrão, um aplicativos se comunica com um contêiner e esse contêiner separa os dados para o aplicativo. Isso significa que vários aplicativos podem estar armazenando informações para a mesma conta iCloud, ainda que essas informações nunca serão ser misturadas.

O uso de contêineres de dados do iCloud também permite CloudKit encapsular as informações de usuário. Dessa forma, o aplicativo terá algum acesso limitado à conta do iCloud e as informações do usuário armazenados em todas as e ainda proteger a privacidade e segurança do usuário.

Contêineres são totalmente gerenciados pelo desenvolvedor do aplicativo por meio do portal WWDR. O namespace do contêiner é global com todos os desenvolvedores da Apple, para que o contêiner não deve apenas ser exclusivo aos aplicativos do desenvolvedor determinado, mas para todos os aplicativos e os desenvolvedores da Apple.

A Apple sugere usando a notação inversa de DNS ao criar o namespace para contêineres do aplicativo. Exemplo:

```csharp
iCloud.com.company-name.application-name
```

Enquanto os contêineres são, por padrão, vinculado individualmente para um determinado aplicativo, eles podem ser compartilhados entre aplicativos. Portanto, podem coordenar a vários aplicativos em um único contêiner. Também pode se comunicar com um único aplicativo para vários contêineres.

### <a name="databases"></a>Bancos de dados

Uma das principais funções do CloudKit é levar o modelo de dados do aplicativo e a replicação desse modelo até os servidores do iCloud. Algumas informações destina-se para o usuário que criou, outras informações são dados públicos que podem ser criados por um usuário para uso público (como uma revisão de um restaurante) ou ele pode ser uma informação que o desenvolvedor tenha publicado para o aplicativo. Em ambos os casos, o público-alvo não é apenas um único usuário, mas é uma comunidade de pessoas.

 [![](intro-to-cloudkit-images/image32.png "Diagrama de contêiner do CloudKit")](intro-to-cloudkit-images/image32.png#lightbox)

Dentro de um contêiner, primeiro é o banco de dados público. Isso é onde todas as informações públicas reside e mingles conjunta. Além disso, há vários de bancos de dados privados individuais para cada usuário do aplicativo.

Quando em execução em um dispositivo iOS, o aplicativo só terá acesso às informações para o usuário conectado no momento do iCloud. Portanto, a exibição do aplicativo do contêiner será da seguinte maneira:

 [![](intro-to-cloudkit-images/image33.png "A exibição do contêiner de aplicativos")](intro-to-cloudkit-images/image33.png#lightbox)

Ele só pode ver o banco de dados público e privado associado ao usuário conectado no momento do iCloud do banco de dados.

Bancos de dados são expostos no CloudKit Framework por meio de `CKDatabase` classe. Cada aplicativo tem acesso a dois bancos de dados: o banco de dados público e um privado.

O contêiner é o ponto de entrada inicial CloudKit. O código a seguir pode ser usado para acessar o banco de dados público e privado de contêiner do padrão do aplicativo:

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

Aqui estão as diferenças entre os tipos de banco de dados:

||Banco de dados público|Banco de dados particular|
|---|--- |--- |
|**Tipo de dados**|Dados compartilhados|Dados do usuário atual|
|**Cota**|Contabilizada na cota do desenvolvedor|Contabilizada na cota do usuário|
|**Permissões padrão**|Mundo legível|Legível do usuário|
|**Permissões de edição**|iCloud painel funções por meio de um nível de classe de registro|N/D|

### <a name="records"></a>Registros

Os bancos de dados, e dentro de bancos de dados são registros de retenção de contêineres. Registros são o mecanismo em que dados estruturados são movidos para e do CloudKit:

 [![](intro-to-cloudkit-images/image34.png "Os bancos de dados, e dentro de bancos de dados são registros de retenção de contêineres")](intro-to-cloudkit-images/image34.png#lightbox)

Registros são expostos no CloudKit Framework por meio de `CKRecord` classe, que encapsula os pares chave-valor. Uma instância de um objeto em um aplicativo é equivalente a um `CKRecord` em CloudKit. Além disso, cada `CKRecord` possui um tipo de registro, que é equivalente à classe de um objeto.

Registros têm um esquema just-in-time, para que os dados são descritos ao CloudKit antes que está sendo entregue para processamento. A partir daí, CloudKit irá interpretar as informações e lidar com a logística de armazenar e recuperar o registro.

O `CKRecord` classe também dá suporte a uma ampla gama de metadados. Por exemplo, um registro contém informações sobre quando ele foi criado e o usuário que o criou. Um registro também contém informações sobre quando ele foi modificado pela última vez e o usuário que modificou a ele.

Os registros contêm a noção de uma marca de alteração. Esta é uma versão anterior de uma revisão de um determinado registro. A marca de alteração é usada como uma forma leve de determinar se o cliente e o servidor tem a mesma versão de um determinado registro.

Conforme mencionado acima, `CKRecords` encapsular pares chave-valor e como tal, os seguintes tipos de dados podem ser armazenados em um registro:

1.   `NSString`
1.   `NSNumber`
1.   `NSData`
1.   `NSDate`
1.   `CLLocation`
1.   `CKReferences`
1.   `CKAssets`


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

Não existem registros por si mesmos dentro de um determinado banco de dados – grupos de registros juntos existirem dentro de uma zona do registro. Zonas de registro pode ser pensadas como tabelas em um banco de dados relacionais tradicionais:

 [![](intro-to-cloudkit-images/image35.png "Grupos de registros juntos existirem dentro de uma zona do registro")](intro-to-cloudkit-images/image35.png#lightbox)

Pode haver vários registros em uma determinada zona de registro e várias zonas de registro dentro de um determinado banco de dados. Cada banco de dados contém uma zona de registro padrão:

 [![](intro-to-cloudkit-images/image36.png "Cada banco de dados contém uma zona de registro padrão e personalizadas")](intro-to-cloudkit-images/image36.png#lightbox)

Isso é onde os registros são armazenados por padrão. Além disso, as zonas de registro personalizado podem ser criadas. Registre representam as zonas a granularidade de base em quais confirmações atômicas e controle de alterações é feita.

## <a name="record-identifiers"></a>Identificadores de registro

Identificadores de registro são representados como uma tupla, um cliente que contém ambos fornecidos nome do registro e a região na qual o registro existe. Identificadores de registro têm as seguintes características:

-  Eles são criados pelo aplicativo cliente.
-  Eles são totalmente normalizados e representam o local específico do registro.
-  Ao atribuir a ID exclusiva de um registro em um banco de dados externo para o nome do registro, eles podem ser usados para bancos de dados locais que não são armazenados dentro do CloudKit uma ponte.


Quando os desenvolvedores criam novos registros, eles podem optar por passar um identificador de registro. Se um identificador de registro não for especificado, um UUID automaticamente será criado e atribuído ao registro.

Quando os desenvolvedores criam novos identificadores de registro, eles podem optar por especificar a zona de registro de cada registro pertence. Se nenhum for especificado, a zona de registro padrão será usada.

Identificadores de registro são expostos no CloudKit Framework por meio de `CKRecordID` classe. O código a seguir pode ser usado para criar um novo identificador de registro:

```csharp
var recordID =  new CKRecordID("My Record");
```

### <a name="references"></a>Referências

Referências fornecem as relações entre os registros relacionados dentro de um determinado banco de dados:

 [![](intro-to-cloudkit-images/image37.png "Referências fornecem as relações entre os registros relacionados dentro de um determinado banco de dados")](intro-to-cloudkit-images/image37.png#lightbox)

No exemplo acima, os pais possuem filhos para que o filho é um registro filho do registro pai. A relação vai do registro filho para o registro pai e é conhecida como um *referência de volta*.

As referências são expostas no CloudKit Framework por meio de `CKReference` classe. Eles são uma maneira de permitir que o servidor do iCloud entender a relação entre os registros.

Referências fornecem o mecanismo por trás em cascata exclui. Se um registro pai é excluído do banco de dados, quaisquer registros filho (conforme especificado em uma relação) serão excluídos automaticamente do banco de dados.

> [!NOTE]
> Ponteiros pendentes são uma possibilidade quando usando o CloudKit. Por exemplo, no momento em que o aplicativo buscada uma lista de ponteiros de registros, selecionado um registro e, em seguida, é solicitado para o registro, o Registro talvez não existem mais no banco de dados. Um aplicativo deve ser codificado para lidar com essa situação normalmente.

Embora não seja necessário, são preferenciais referências de volta ao trabalhar com a estrutura do CloudKit. Apple tiver ajustado o sistema para tornar isso o tipo mais eficiente de referência.

Ao criar uma referência, o desenvolvedor pode fornecer um registro que já está na memória ou criar uma referência a um identificador de registro. Se usar um identificador de registro e a referência especificada não existe no banco de dados, um ponteiro pendente seria criado.

Este é um exemplo de como criar uma referência em relação a um registro conhecido:

```csharp
var reference = new CKReference(newRecord, new CKReferenceAction());
```

### <a name="assets"></a>Ativos

Permitem ativos para um arquivo de dados grandes e não estruturados para ser carregado com o iCloud e associado a um determinado registro:

 [![](intro-to-cloudkit-images/image38.png "Permitir que ativos para um arquivo de dados grandes e não estruturados para ser carregado com o iCloud e associado a um determinado registro")](intro-to-cloudkit-images/image38.png#lightbox)

No cliente, um `CKRecord` é criado que descreve o arquivo que será carregado no servidor do iCloud. Um `CKAsset` é criado para conter o arquivo e é vinculado ao registro que o descreve.

Quando o arquivo é carregado para o servidor, o registro é colocado no banco de dados e o arquivo é copiado para um banco de dados do armazenamento em massa especial. É criado um link entre o registro de ponteiro e o arquivo carregado.

Ativos são expostos no CloudKit Framework via o `CKAsset` de classe e são usados para armazenar dados grandes e não estruturados. Como o desenvolvedor nunca deseja ter dados grandes e não estruturados na memória, ativos são implementados usando os arquivos no disco.

Ativos pertencem registros, que permite que os ativos a ser recuperado do iCloud usando o registro como um ponteiro. Dessa forma, o servidor pode ativos de coleta de lixo quando o registro que é proprietário do ativo é excluído.

Porque `CKAssets` devem lidar com arquivos de dados grandes, Apple projetado CloudKit para carregar e baixar os ativos de forma eficiente.

O código a seguir pode ser usado para criar um ativo e associá-la com o registro:

```csharp
var fileUrl = new NSUrl("LargeFile.mov");
var asset = new CKAsset(fileUrl);
newRecord ["name"] = asset;
```

Nós abordamos agora todos os objetos dentro do CloudKit fundamentais. Os contêineres são associados a aplicativos e contêm bancos de dados. Bancos de dados contêm registros que são agrupados em zonas de registro e apontados por identificadores de registro. Relações pai-filho são definidas entre os registros de uso de referências. Por fim, arquivos grandes podem ser carregados e associados a registros de uso de recursos.

## <a name="cloudkit-convenience-api"></a>API de conveniência do CloudKit

A Apple oferece dois diferentes conjuntos de API para trabalhar com CloudKit:

-  **API operacionais** – oferece todos os recursos do CloudKit único. Para aplicativos mais complexos, essa API fornece um controle refinado sobre CloudKit.
-  **API de conveniência** – oferece um subconjunto comum e pré-configurados de recursos do CloudKit. Ele fornece uma solução de acesso fácil e conveniente para incluir funcionalidade CloudKit em um aplicativo iOS.


A API de conveniência é geralmente a melhor opção para a maioria dos aplicativos iOS e a Apple sugere começando com ele. O restante desta seção abordará os seguintes tópicos da API de conveniência:

-  Salvar um registro.
-  Ao buscar um registro.
-  Atualizando um registro.


### <a name="common-setup-code"></a>Código de configuração comum

Antes de começar com a API de conveniência do CloudKit, há alguns códigos de padrão de instalação necessários. Comece modificando o aplicativo `AppDelegate.cs` de arquivo e torná-lo semelhante ao seguinte:

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

O código acima expõe os bancos de dados CloudKit públicos e privados como atalhos para torná-los mais fáceis de trabalhar no restante do aplicativo.

Em seguida, adicione o seguinte código para qualquer contêiner de modo de exibição que estará usando o CloudKit ou o modo de exibição:

```csharp
using CloudKit;
...

#region Computed Properties
public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
#endregion
```

Isso adiciona um atalho para acessar o `AppDelegate` e acessar os atalhos de banco de dados pública e privada criados acima.

Com esse código, vamos dar uma olhada em como implementar a API de conveniência do CloudKit em um aplicativo Xamarin iOS 8.

### <a name="saving-a-record"></a>Salvar um registro

Usando o padrão anterior apresentado ao discutir os registros, o código a seguir cria um novo registro e usar a API de conveniência para salvá-lo para o banco de dados público:

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

Três coisas a observar sobre o código acima:

1.  Chamando o `SaveRecord` método da `PublicDatabase`, o desenvolvedor não precisa especificar como os dados são enviados, o qual zona ele está sendo gravado etc. A API de conveniência é cuidar de todos esses detalhes em si.
1.  A chamada é assíncrona e fornece uma rotina de retorno de chamada quando a chamada é concluída com êxito ou falha. Se a chamada falhar, uma mensagem de erro será fornecida.
1.  CloudKit não fornece armazenamento local/persistência; é apenas um meio de transferência. Portanto, quando é feita uma solicitação para salvar um registro, ela é enviada imediatamente para os servidores do iCloud.


> [!NOTE]
> Devido à natureza das comunicações de rede móvel, onde as conexões estão constantemente sendo descartadas ou interrompida, uma das primeiras considerações a fazer o desenvolvedor deve fazer ao trabalhar com CloudKit é o tratamento de erro "sem perdas".

### <a name="fetching-a-record"></a>Ao buscar um registro

Com um registro criado e armazenado com êxito no servidor do iCloud, use o seguinte código para recuperar o registro:

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

Assim como o registro é salvo, o código acima é assíncrona, simples e requer tratamento de erro excelente.

### <a name="updating-a-record"></a>Atualizando um registro

Depois que um registro foi buscado dos servidores do iCloud, o código a seguir pode ser usado para modificar o registro e salvar as alterações no banco de dados:

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

O `FetchRecord` método da `PublicDatabase` retorna um `CKRecord` se a chamada foi bem-sucedida. O aplicativo, em seguida, modifica o registro e chama `SaveRecord` novamente para gravar as alterações de volta ao banco de dados.

Esta seção mostra o ciclo típico que um aplicativo usará ao trabalhar com a API de conveniência do CloudKit. O aplicativo irá salvar registros com o iCloud, recuperar os registros do iCloud, modificar os registros e salvar essas alterações com o iCloud.

## <a name="designing-for-scalability"></a>Design para escalabilidade

Até agora este artigo analisou armazenar e recuperar o modelo de objeto inteiro de um aplicativo dos servidores do iCloud, toda vez que ele vai ser trabalhado. Embora essa abordagem funciona bem com uma pequena quantidade de dados e uma base de usuários muito pequeno, ele não se adapta bem quando a quantidade de informações e/ou usuário base aumenta.

### <a name="big-data-tiny-device"></a>Big Data, o dispositivo muito pequeno

Mais popular um aplicativo torna-se, mais dados no banco de dados e menos viável é têm um cache de dados inteiros no dispositivo. As técnicas a seguir podem ser usadas para resolver esse problema:

-  **Manter os dados grandes na nuvem** – CloudKit foi projetado para lidar com dados grandes com eficiência.
-  **Cliente só deve ver uma fatia de dados** – derrubar o mínimo necessário de dados necessários para lidar com qualquer tarefa em um determinado momento.
-  **Modos de exibição do cliente podem alterar** – como cada usuário tem preferências diferentes, a fatia de dados que está sendo exibidos pode alterar de usuário para usuário e de usuário individual a exibição de qualquer determinada fatia pode ser diferente.
-  **Cliente usa as consultas para concentrar-se o ponto de Vista** – consultas permitem ao usuário exibir um pequeno subconjunto de um conjunto de dados maior que existe dentro da nuvem.


### <a name="queries"></a>Consultas

Como mencionado acima, as consultas permitem ao desenvolvedor selecionar um pequeno subconjunto do conjunto de dados maior que existe na nuvem. Consultas são expostas no CloudKit Framework por meio de `CKQuery` classe.

Uma consulta combina três coisas diferentes: um tipo de registro ( `RecordType`), um predicado ( `NSPredicate`) e, opcionalmente, um descritor de classificação ( `NSSortDescriptors`). CloudKit dá suporte à maioria dos `NSPredicate`.

#### <a name="supported-predicates"></a>Predicados com suporte

CloudKit suporta os seguintes tipos de `NSPredicates` ao trabalhar com consultas:


1. Onde o nome é igual a um valor armazenado em uma variável de registros correspondentes:
    
    ```
    NSPredicate.FromFormat(string.Format("name = '{0}'", recordName))
    ```
   
2. Permite a correspondência para se basear em um valor de chave dinâmico, para que a chave não precisa ser sabe em tempo de compilação:
    
    ```
    NSPredicate.FromFormat(string.Format("{0} = '{1}'", key, value))
    ```
    
3. Onde o valor do registro é maior que o valor especificado de registros correspondentes:
   
    ```
    NSPredicate.FromFormat(string.Format("start > {0}", (NSDate)date))
    ```

4. Correspondência de registros em que o local do registro é dentro de 100 metros do local fornecido:
    
    ```
    var location = new CLLocation(37.783,-122.404);
    var predicate = NSPredicate.FromFormat(string.Format("distanceToLocation:fromLocation(Location,{0}) < 100", location));
    ```

5. CloudKit dá suporte a uma pesquisa com token. Essa chamada criará dois tokens, outro para `after` e outro para `session`. Ele retornará um registro que contém esses dois tokens:
    
    ```
    NSPredicate.FromFormat(string.Format("ALL tokenize({0}, 'Cdl') IN allTokens", "after session"))
    ```
    
 6. Suporta CloudKit composta predicados Unidos usando o `AND` operador.
    
    ```
    NSPredicate.FromFormat(string.Format("start > {0} AND name = '{1}'", (NSDate)date, recordName))
    ```
    


#### <a name="creating-queries"></a>Criando consultas

O código a seguir pode ser usado para criar um `CKQuery` em um aplicativo Xamarin iOS 8:

```csharp
var recordName = "MyRec";
var predicate = NSPredicate.FromFormat(string.Format("name = '{0}'", recordName));
var query = new CKQuery("CloudRecords", predicate);
```

Primeiro, ele cria um predicado para selecionar apenas os registros que correspondem a um determinado nome. Em seguida, ele cria uma consulta que seleciona os registros de determinado tipo de registro que correspondem ao predicado.

#### <a name="performing-a-query"></a>Executar uma consulta

Quando uma consulta tiver sido criada, use o código a seguir para executar a consulta e processar os registros retornados:

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

O código acima usa a consulta criada acima e ele é executado no banco de dados pública. Como nenhuma zona de registro é especificada, todas as zonas são pesquisadas. Se não ocorreu nenhum erro, uma matriz de `CKRecords` serão retornados os parâmetros da consulta de correspondência.

A maneira de pensar sobre consultas é que eles são pesquisas e são ótimos para divisão por meio de grandes conjuntos de dados. Consultas, no entanto, não são adequadas para conjuntos de dados grandes, quase todo estáticos pelos seguintes motivos:

-  Eles são ruins para a vida útil da bateria do dispositivo.
-  Eles são ruins para tráfego de rede.
-  Eles são ruins para experiência do usuário porque as informações que eles veem são limitadas pela frequência o aplicativo está sondando o banco de dados. Hoje em dia, os usuários esperam notificações por push quando algo mudar.


### <a name="subscriptions"></a>Assinaturas

Ao lidar com conjuntos de dados grandes, quase todo estáticos, a consulta não deve ser executada no dispositivo cliente, ele deve ser executado no servidor em nome do cliente. A consulta deve ser executado em segundo plano e deve ser executada depois de salvar cada registro, seja por dispositivo atual ou outro dispositivo tocar o mesmo banco de dados.

Por fim, uma notificação por push deve ser enviada para cada dispositivo conectado ao banco de dados quando a consulta do lado do servidor é executada.

As assinaturas são expostas no CloudKit Framework por meio de `CKSubscription` classe. Elas combinam um tipo de registro ( `RecordType`), um predicado ( `NSPredicate`) e um Apple Push Notification ( `Push`).

> [!NOTE]
> Envios por push do CloudKit ligeiramente são aumentados, pois eles contêm uma carga que contém informações específicas do CloudKit, como o que causou o envio por push acontecer.

#### <a name="how-subscriptions-work"></a>Como funcionam as assinaturas

Antes de implementar a assinatura no C# de código, vamos dar uma rápida visão geral de como funcionam as assinaturas:

 [![](intro-to-cloudkit-images/image39.png "Uma visão geral de como funcionam as assinaturas")](intro-to-cloudkit-images/image39.png#lightbox)

O gráfico acima mostra o processo de assinatura típica da seguinte maneira:

1.  O dispositivo do cliente cria uma nova assinatura que contém o conjunto de condições que dispararão a assinatura e uma notificação por Push que será enviado quando o gatilho ocorre.
2.  A assinatura é enviada para o banco de dados, onde ele é adicionado à coleção de assinaturas existentes.
3.  Um segundo dispositivo cria um novo registro e salva esse registro ao banco de dados.
4.  Procura o banco de dados por meio de sua lista de assinaturas para ver se o novo registro corresponde a qualquer uma das suas condições.
5.  Se uma correspondência for encontrada, a notificação por Push será enviada para o dispositivo que registrou a assinatura com informações sobre o registro que fez com que ele seja disparado.


Com esse conhecimento em vigor, vamos dar uma olhada na criação de assinaturas em um Xamarin iOS 8 do aplicativo.

#### <a name="creating-subscriptions"></a>Criação de assinaturas

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

Primeiro, ele cria um predicado que fornece a condição para disparar a assinatura. Em seguida, ele cria a assinatura em relação a um tipo específico de registro e define a opção de quando o gatilho é testado. Por fim, ele define o tipo de notificação que ocorrerá quando a assinatura é disparada e a anexa a assinatura.

### <a name="saving-subscriptions"></a>Salvando as assinaturas

Com a assinatura foi criada, o código a seguir irá salvá-lo ao banco de dados:

```csharp
// Save the subscription to the database
ThisApp.PublicDatabase.SaveSubscription(subscription, (s, err) => {
    // Was there an error?
    if (err != null) {

    }
});
```

Usando a API de conveniência, a chamada é assíncrona, simples e fornece a manipulação de erro fácil.

#### <a name="handling-push-notifications"></a>Manipulando notificações de Push

Se o desenvolvedor tenha usado anteriormente notificações por Push da Apple (APS), o processo de lidar com notificações geradas por CloudKit deve ser familiar.

No `AppDelegate.cs`, substitua o `ReceivedRemoteNotification` classe da seguinte maneira:

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

O código acima solicita CloudKit para analisar o userInfo em uma notificação do CloudKit. Em seguida, as informações são extraídas sobre o alerta. Por fim, o tipo de notificação é testado e a notificação é tratada adequadamente.

Esta seção mostra como responder a Big Data, problema de dispositivo muito pequeno apresentado acima usando consultas e assinaturas. O aplicativo será deixar os dados grandes na nuvem e usar essas tecnologias para fornecer modos de exibição para este conjunto de dados.

## <a name="cloudkit-user-accounts"></a>Contas de usuário do CloudKit

Conforme observado no início deste artigo, CloudKit é criada sobre a infraestrutura existente do iCloud. A seguinte seção abordará, detalhadamente, como as contas são expostas a um desenvolvedor usando a API do CloudKit.

### <a name="authentication"></a>Autenticação

Ao lidar com contas de usuário, a primeira questão é a autenticação. CloudKit dá suporte à autenticação por meio do usuário conectado no momento do iCloud no dispositivo. A autenticação ocorre em segundo plano e é tratada pelo iOS. Dessa forma, os desenvolvedores nunca precisam se preocupar sobre os detalhes da implementação da autenticação. Eles apenas testar para ver se um usuário fizer logon.

### <a name="user-account-information"></a>Informações de conta de usuário

CloudKit fornece as seguintes informações de usuário para o desenvolvedor:

-  **Identidade** – uma maneira de identificar com exclusividade o usuário.
-  **Metadados** – a capacidade de salvar e recuperar informações sobre os usuários.
-  **Privacidade** – todas as informações são tratadas em um manor consciente de privacidade. Nada é exposto, a menos que o usuário concordou-se a ele.
-  **Descoberta** – fornece aos usuários a capacidade de detectar a seus amigos que estão usando o mesmo aplicativo.


Em seguida, vamos examinar esses tópicos em detalhes.

#### <a name="identity"></a>Identidade

Como mencionado acima, CloudKit fornece uma maneira para o aplicativo identificar exclusivamente um determinado usuário:

 [![](intro-to-cloudkit-images/image40.png "Identificar exclusivamente um determinado usuário")](intro-to-cloudkit-images/image40.png#lightbox)

Há um aplicativo cliente em execução em dispositivos do usuário e todos os bancos de dados privados de usuário específico dentro do contêiner do CloudKit. O aplicativo cliente vai ser vinculado a um desses usuários específicos. Isso se baseia no usuário que faz logon no iCloud localmente no dispositivo.

Porque isso é proveniente de iCloud, há uma avançada fazendo o armazenamento de informações do usuário. E como iCloud, na verdade, está hospedando o contêiner, ele pode correlacionar os usuários. No gráfico acima, o usuário cuja conta iCloud `user@icloud.com` está vinculada ao cliente atual.

Em uma base de contêiner por contêiner, uma ID de usuário exclusivo gerado aleatoriamente, é criada e associada com a conta do iCloud do usuário (endereço de email). Essa ID de usuário é retornado para o aplicativo e pode ser usado de qualquer forma, que o desenvolvedor julgar mais adequados.

> [!NOTE]
> Diferentes aplicativos em execução no mesmo dispositivo para o mesmo usuário iCloud terá diferentes IDs de usuário, porque eles estão conectados a diferentes contêineres CloudKit.

O seguinte código obtém a ID de usuário do CloudKit para conectada no momento no usuário do iCloud no dispositivo:

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

O código acima está solicitando que o contêiner CloudKit fornecer a ID do usuário conectado no momento. Uma vez que essas informações forem provenientes do servidor do iCloud, a chamada é assíncrona e tratamento de erros é necessário.

#### <a name="metadata"></a>Metadados

Cada usuário do CloudKit tem metadados específicos que descrevem-los. Esses metadados é representado como um registro do CloudKit:

 [![](intro-to-cloudkit-images/image41.png "Cada usuário do CloudKit tem metadados específicos que descrevem-los")](intro-to-cloudkit-images/image41.png#lightbox)

Olhando dentro do banco de dados privados para um usuário específico de um contêiner existe é um registro que define que o usuário. Há muitos registros de usuário dentro do banco de dados público, uma para cada usuário do contêiner. Um deles terá uma ID de registro que corresponde ao conectada no momento na ID de registro. do usuário

Registros de usuário no banco de dados públicos são legíveis do mundo. Eles são tratados, geralmente, como um registro comum e têm um tipo de `CKRecordTypeUserRecord`. Esses registros são reservados pelo sistema e não estão disponíveis para consultas.

Use o seguinte código para acessar um registro de usuário:

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

O código acima está solicitando que o banco de dados públicos para retornar o registro de usuário para o usuário que é acessado acima de ID. Uma vez que essas informações forem provenientes do servidor do iCloud, a chamada é assíncrona e tratamento de erros é necessário.

#### <a name="privacy"></a>Privacidade

CloudKit era de design, por padrão, para proteger a privacidade do usuário conectado no momento. Nenhuma informação de identificação pessoal sobre o usuário é exposta por padrão. Há alguns casos em que o aplicativo exigirá informações limitadas sobre o usuário.

Nesses casos, o aplicativo pode solicitar que o usuário divulgar essas informações. Uma caixa de diálogo será apresentada ao usuário solicitando a participar de expor suas informações de conta.

#### <a name="discovery"></a>Descoberta

Supondo que o usuário como participar no permitindo que o aplicativo acesso limitado a suas informações de conta de usuário, eles podem ser detectáveis por outros usuários do aplicativo:

 [![](intro-to-cloudkit-images/image42.png "Um usuário pode ser detectável por outros usuários do aplicativo")](intro-to-cloudkit-images/image42.png#lightbox)

O aplicativo cliente está se comunicando com um contêiner e o contêiner está se comunicando com o iCloud para acessar informações do usuário. O usuário pode fornecer um endereço de email e a descoberta pode ser usada para obter informações sobre o usuário. Opcionalmente, a ID de usuário também pode ser usada para descobrir informações sobre o usuário.

CloudKit também fornece uma maneira para descobrir informações sobre qualquer usuário que pode ser amigos de conectada no momento no usuário do iCloud, consultando o catálogo de endereços inteira. O processo do CloudKit pull no catálogo de contato do usuário e usar os endereços de email para ver se ele pode encontrar outro usuário do aplicativo que correspondem a esses endereços.

Isso permite que o aplicativo para aproveitar o livro de contato do usuário sem fornecer acesso a ele ou solicitar que o usuário aprovar o acesso aos contatos. Em nenhum momento as informações de contato ficam disponíveis para o aplicativo, somente o processo do CloudKit tem acesso.

Para recapitular, há três tipos diferentes de entradas disponíveis para a descoberta de usuário:

-  **ID de registro de usuário** – descoberta pode ser feita com a ID de usuário de conectada no momento no usuário CloudKit.
-  **Endereço de Email do usuário** – o usuário pode fornecer um endereço de email e ele pode ser usado para descoberta.
-  **Entre em contato com o catálogo** – catálogo de endereços do usuário pode ser usado para descobrir os usuários do aplicativo que têm o mesmo endereço de email, conforme listado em seus contatos.


Descoberta de usuário retornará as seguintes informações:

-  **ID de registro de usuário** -a ID exclusiva de um usuário no banco de dados pública.
-  **Primeiro e último nome** - conforme armazenado no banco de dados pública.


Essa informação será retornada apenas para usuários que têm aceito para descoberta.

O código a seguir vai descobrir informações sobre o usuário conectado no momento no iCloud no dispositivo:

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

Use o seguinte código para consultar todos os usuários no catálogo de contato:

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

Nesta seção, abordamos as quatro áreas principais de acesso a uma conta de usuário que CloudKit pode fornecer a um aplicativo. Ao obter a identidade do usuário e os metadados, as políticas de privacidade, que são criados em CloudKit e, finalmente, a capacidade de detectar a outros usuários do aplicativo.

## <a name="the-development-and-production-environments"></a>Os ambientes de desenvolvimento e produção

CloudKit fornece ambientes de desenvolvimento e produção separados para tipos de registro e os dados de um aplicativo. O ambiente de desenvolvimento é um ambiente mais flexível do que está disponível somente para membros de uma equipe de desenvolvimento. Quando um aplicativo adiciona um novo campo a um registro e salva esse registro no ambiente de desenvolvimento, o servidor atualiza automaticamente as informações de esquema.

O desenvolvedor pode usar esse recurso para fazer alterações a um esquema durante o desenvolvimento, o que economiza tempo. Uma limitação é que depois que um campo foi adicionado a um registro, o tipo de dados associado ao campo não pode ser alterado programaticamente. Para alterar o tipo de um campo, o desenvolvedor deve excluir o campo no [CloudKit painel](https://icloud.developer.apple.com/dashboard/) e adicioná-lo novamente com o novo tipo.

Antes de implantar o aplicativo, o desenvolvedor pode migrar seus dados e esquema para o ambiente de produção usando **CloudKit painel**. Quando executada no ambiente de produção, o servidor impede que um aplicativo alterando o esquema por meio de programação. O desenvolvedor ainda pode fazer alterações com **CloudKit painel** , mas tentar adicionar campos a um registro no resultado do ambiente de produção em erros.

> [!NOTE]
> O iOS Simulator funciona apenas com o **ambiente de desenvolvimento**. Quando o desenvolvedor estiver pronto para testar um aplicativo em um **ambiente de produção**, um dispositivo iOS físico é necessário.


## <a name="shipping-a-cloudkit-enabled-app"></a>Aplicativo habilitado para envio de um CloudKit

Antes de enviar um aplicativo que usa CloudKit, ele precisará ser configurado para o destino o **produção CloudKit ambiente** ou o aplicativo será rejeitado pela Apple.

Faça o seguinte:

1. No Visual Studio para Ma, compilar o aplicativo para **Release** > **dispositivo iOS**: 

    [![](intro-to-cloudkit-images/shipping01.png "Compilar o aplicativo para lançamento")](intro-to-cloudkit-images/shipping01.png#lightbox)

2. Dos **construir** menu, selecione **arquivamento**: 

    [![](intro-to-cloudkit-images/shipping02.png "Selecione arquivo morto")](intro-to-cloudkit-images/shipping02.png#lightbox)

3. O **arquivamento** será criado e exibido no Visual Studio para Mac: 

    [![](intro-to-cloudkit-images/shipping03.png "O arquivo será criado e exibido")](intro-to-cloudkit-images/shipping03.png#lightbox)

4. Inicie o **Xcode**.
5. Dos **janela** menu, selecione **organizador**: 

    [![](intro-to-cloudkit-images/shipping04.png "Selecione o organizador")](intro-to-cloudkit-images/shipping04.png#lightbox)

6. Selecione o arquivo do aplicativo e clique no **exportar...**  botão: 

    [![](intro-to-cloudkit-images/shipping05.png "Arquivamento do aplicativo")](intro-to-cloudkit-images/shipping05.png#lightbox)
    
7. Selecione um método para exportação e clique no **próxima** botão: 

    [![](intro-to-cloudkit-images/shipping06.png "Selecionar um método de exportação")](intro-to-cloudkit-images/shipping06.png#lightbox)

8. Selecione o **equipe de desenvolvimento** na lista suspensa e clique o **escolher** botão: 

    [![](intro-to-cloudkit-images/shipping07.png "Selecione a equipe de desenvolvimento na lista suspensa")](intro-to-cloudkit-images/shipping07.png#lightbox)

9. Selecione **produção** na lista suspensa e clique o **próxima** botão: 

    [![](intro-to-cloudkit-images/shipping08.png "Selecione produção na lista suspensa")](intro-to-cloudkit-images/shipping08.png#lightbox)

10. Examine a configuração e clique o **exportar** botão: 

    [![](intro-to-cloudkit-images/shipping09.png "Examine a configuração")](intro-to-cloudkit-images/shipping09.png#lightbox)

11. Escolha um local para gerar o aplicativo resultante `.ipa` arquivo.

O processo é semelhante para enviar o aplicativo diretamente no iTunes Connect, basta clicar o **enviar...**  botão em vez da exportação... depois de selecionar um arquivo morto na janela do organizador.

## <a name="when-to-use-cloudkit"></a>Quando usar CloudKit

Como vimos neste artigo, CloudKit fornece uma maneira fácil para um aplicativo armazenar e recuperar informações de servidores do iCloud. Dito isso, CloudKit não obsoleto ou substituir qualquer uma das estruturas ou ferramentas existentes.

### <a name="use-cases"></a>Casos de uso

Os casos de uso a seguir devem ajudar o desenvolvedor decidir quando usar uma estrutura de iCloud específico ou a tecnologia:

-  **iCloud Store de chave-valor** – assincronamente mantém uma quantidade pequena de dados atualizado e é ideal para trabalhar com as preferências do aplicativo. No entanto, ele é restrito por uma quantidade muito pequena de informações.
-  **iCloud Drive** – criada sobre o iCloud existente APIs de documentos e fornece uma API simples para sincronizar os dados não estruturados do sistema de arquivos. Ele fornece um cache offline completa no Mac OS X e é excelente para aplicativos centrados em documentos.
-  **Dados básicos do iCloud** – permite que os dados sejam replicados entre todos os dispositivos do usuário. Os dados são ótimos para manter privadas dados estruturados em sincronia e de usuário único.
-  **CloudKit** – fornece ambas as estrutura de dados públicos e em massa e é capaz de lidar com o conjunto de dados grande e não estruturados de arquivos grandes. Associados ao usuário da conta do iCloud e fornece transferência de dados do cliente direcionado.


Mantendo esses casos de uso em mente, o desenvolvedor deve escolher a tecnologia correta do iCloud para fornecer os dois a funcionalidade atual do aplicativo necessário e fornecer boa escalabilidade para futuro crescimento.

## <a name="summary"></a>Resumo

Este artigo cobriu uma rápida introdução à API do CloudKit. Ele mostra como provisionar e configurar um aplicativo Xamarin iOS para usar CloudKit. Ele abordou os recursos da API de conveniência do CloudKit. Ele tem a mostrar como criar um CloudKit habilitado aplicativo escalabilidade usando consultas e assinaturas. E, por fim, mostrou as informações da conta de usuário que são expostas a um aplicativo por CloudKit.

## <a name="related-links"></a>Links relacionados

- [CloudKitAtlas (amostra)](https://developer.xamarin.com/samples/monotouch/ios8/CloudKitAtlas/)
- [Introdução ao iOS 8](~/ios/platform/introduction-to-ios8.md)
- [Criar um perfil de provisionamento](~/ios/get-started/installation/device-provisioning/index.md)
