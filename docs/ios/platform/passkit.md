---
title: PassKit no xamarin. IOS
description: No aplicativo Wallet permite aos usuários armazenar passes digitais em seus dispositivos de iOS. PassKit framework permite aos desenvolvedores interagir com passos de forma programática.
ms.prod: xamarin
ms.assetid: 74B9973B-C1E8-B727-3F6D-59C1F98BAB3A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/13/2018
ms.openlocfilehash: d1c640bef41e875b3bb427d657c9c239e4c3e16d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61191724"
---
# <a name="passkit-in-xamarinios"></a>PassKit no xamarin. IOS

O aplicativo do iOS carteira permite aos usuários armazenar passes digitais em seus dispositivos.
Esses passes são geradas por comerciantes e enviadas ao cliente por email, URLs, ou por meio do aplicativo iOS do comerciante. Esses passes podem representar várias coisas, de tíquetes de filme para cartões fidelidade de passagens aéreas. PassKit framework permite aos desenvolvedores interagir com passos de forma programática.

Este documento apresenta a carteira e usando a API do PassKit com xamarin. IOS.

 [![](passkit-images/image1.png "A carteira armazena e organiza todos os passos de em um telefone")](passkit-images/image1.png#lightbox)

## <a name="requirements"></a>Requisitos

Os recursos de PassKit discutidos neste documento exigem o iOS 6 e o Xcode 4.5 juntamente com o xamarin. IOS 6.0.

## <a name="introduction"></a>Introdução

O problema de chave que resolve PassKit é a distribuição e o gerenciamento de códigos de barras. Alguns exemplos do mundo real de como os códigos de barras são usados atualmente incluem:

-   **Comprando ingressos online do filme** – os clientes normalmente são enviados por email um código de barras que representa seus tíquetes. Esse código de barras é impresso e levado para o cinema para ser examinado em busca de entrada.
-   **Cartões de fidelidade** – os clientes carregam um número de cartões de store específicas diferentes na bolsa ou carteira e para exibição e de verificação quando eles compraram mercadorias.
-   **Cupons** – cupons são distribuídos por email, como páginas da web imprimível, por meio e Letterbox e como os códigos de barras em jornais e revistas. Os clientes colocá-los para um repositório para verificação, para receber os bens, serviços ou descontos em troca.
-   **Controla o embarque passes** – semelhante à compra de um tíquete de filme.

PassKit oferece uma alternativa para cada um desses cenários:

-   **Tíquetes de filme** – após a compra, o cliente adiciona uma passagem de tíquete do evento (via email ou um link de site). Como o tempo para que as abordagens de filme, a passagem de aparecerão automaticamente na tela de bloqueio como um lembrete e na chegada em de cinema a passagem facilmente é recuperada e exibida na carteira para verificação.
-   **Cartões de fidelidade** – em vez de (ou além) fornecer um cartão físico, armazenamentos podem emitir (via email ou após um logon de site) uma passagem de cartão de Store. O armazenamento pode fornecer recursos adicionais, como atualizar o saldo da conta a passagem por meio de notificações por push e usando os serviços de localização geográfica a passagem poderia aparecer automaticamente na tela de bloqueio quando o cliente está próximo de um local de armazenamento.
-   **Cupons** – passagens de cupom podem facilmente ser geradas com características exclusivas para ajudar com o controle e distribuídas por meio de links de email ou site. Cupons baixados automaticamente podem aparecer na tela de bloqueio quando o usuário está perto de um local específico, e/ou em uma determinada data (por exemplo, quando está se aproximando da data de expiração). Como os cupons são armazenados no telefone do usuário, eles sempre são úteis e não seja extraviados. Cupons podem incentivar os clientes para baixar aplicativos complementares, porque links de Store de aplicativo podem ser incorporados a passagem, aumentando o envolvimento com o cliente.
-   **Controla o embarque passes** – depois de um processo de verificação online, o cliente deve receber sua passagem aérea via email ou um link. Um aplicativo complementar fornecidas pelo provedor de transporte poderia incluir o processo de check-in e também permitem que o cliente executar funções adicionais, como escolher sua estação ou refeição. O provedor de transporte pode usar notificações por push para atualizar a passagem, se o transporte está atrasada ou cancelada. Como as abordagens de tempo de embarque a passagem aparecerá na tela de bloqueio como um lembrete e fornecer acesso rápido para a passagem.

Em seu núcleo, PassKit fornece uma maneira simples e conveniente para armazenar e exibir os códigos de barras em seu dispositivo iOS. Com o tempo adicional e a integração de bloqueio de tela de local, notificações por push e aplicativo complementar integração-lo oferece uma base para vendas muito sofisticadas, tíquetes e serviços de cobrança.

## <a name="passkit-ecosystem"></a>Ecossistema do PassKit

PassKit não é apenas uma API em CocoaTouch, em vez disso, ele faz parte de um ecossistema maior de aplicativos, dados e serviços que facilitam o compartilhamento seguro e gerenciamento de códigos de barras e outros dados. Este diagrama de alto nível mostra as diferentes entidades que podem estar envolvidas na criação e uso de passos:

 [![](passkit-images/image2.png "Este diagrama de alto nível mostra as entidades envolvidas na criação e uso de passagens")](passkit-images/image2.png#lightbox)

Cada parte do ecossistema tem uma função claramente definidas:

-   **Wallet** – aplicativo do iOS interno da Apple que armazena e exibe passes. Isso é o único lugar passes são renderizados para uso no mundo real (ou seja o código de barras é exibido, juntamente com todos os dados localizados no passo).
-   **Complementar aplicativos** – iOS 6 aplicativos criados por passam provedores para estender a funcionalidade dos passos de emitir, como a adição de valor a uma placa de armazenamento, alterando o assento em uma passagem aérea ou outra função específicos de negócios. Aplicativos complementares não são necessários para uma passagem para serem úteis.
-   **Seu servidor** – um servidor seguro onde passes podem ser gerados e assinados para distribuição. Seu aplicativo complementar pode se conectar ao seu servidor para gerar novos passa ou solicitar atualizações passes existentes. Opcionalmente, você pode implementar a web passa da API de serviço que chamaria Wallet para atualizar.
-   **Servidores de APNS** – o servidor tem a capacidade de notificar o Wallet de atualizações em uma passagem em um determinado dispositivo usando o APNS. Uma notificação por push ao Wallet, que será, em seguida, entre em contato com seu servidor para obter detalhes sobre a alteração. Aplicativos complementares não precisa implementar APNS para esse recurso (eles podem ouvir o `PKPassLibraryDidChangeNotification` ).
-   **Aplicativos de canal** – aplicativos que não manipulam diretamente os passa (como aplicativos complementares fazem), mas que pode melhorar o seu utilitário reconhecendo passagens e permitindo que eles sejam adicionados ao Wallet. Os clientes de email, navegadores de rede social e outros aplicativos de agregação de dados poderão todos encontrar anexos ou links para passes.

Todo o ecossistema pareça complexo, portanto, vale a pena observar que alguns componentes são opcionais e implementações de PassKit muito mais simples são possíveis.

## <a name="what-is-a-pass"></a>O que é uma passagem?

Uma aprovação é uma coleção de dados que representam um tíquete, cupom ou cartão. Ele pode ser destinado a um único indivíduo (e, portanto, contêm detalhes como uma alocação de número e a estação de voo) ou ele pode ser um token de uso de vários que pode ser compartilhado por qualquer número de usuários (por exemplo, um cupom de desconto). Uma descrição detalhada está disponível da Apple [sobre como transmitir arquivos](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html) documento.

### <a name="types"></a>Tipos

Atualmente, cinco tipos com suporte, que podem ser diferenciados no aplicativo Wallet pela borda da passagem de layout e a parte superior:

-  **Tíquete do evento** – recorte semicircular pequeno.
-   **Passagem de entrada** – cortes no ícone de lado, específico de transporte podem ser especificados (por exemplo. barramento, treinamento, avião).
-   **Store cartão** – arredondado superior, como um cartão de crédito ou débito.
-  **Cupom** – perfuradas na parte superior.
-  **Genérico** – mesmo que o cartão de Store, arredondado superior.


Os tipos de cinco passe são mostrados nesta captura de tela (na ordem: cupom, genérico, armazenar o cartão, passagem aérea e tíquete do evento):

 [![](passkit-images/image3.png "Os tipos de cinco passe são mostrados nesta captura de tela")](passkit-images/image3.png#lightbox)

### <a name="file-structure"></a>Estrutura de arquivos

Um arquivo de passagem é, na verdade, um arquivo ZIP com uma **.pkpass** , que contém alguns específicos arquivos JSON (obrigatórios), uma variedade de imagem de arquivos de extensão (opcional), bem como cadeias de caracteres localizadas (também opcional).

-   **pass.JSON** – obrigatório. Contém todas as informações para a passagem.
-   **Manifest** – obrigatório. Contém hashes SHA1 para cada arquivo no passo, exceto o arquivo de assinatura e este arquivo (manifest. JSON).
-   **assinatura** – obrigatório. Criado ao assinar o `manifest.json` arquivo com o certificado gerado no Portal de provisionamento do iOS.
-  **logo. PNG** – opcional.
-  **Background.PNG** – opcional.
-  **icon. PNG** – opcional.
-  **Arquivos de cadeias de caracteres localizáveis** – opcional.

Estrutura de diretório de um arquivo de passagem é mostrada abaixo (esse é o conteúdo do arquivo ZIP):

 [![](passkit-images/image4.png "Estrutura de diretório de um arquivo de passagem é mostrada aqui")](passkit-images/image4.png#lightbox)

### <a name="passjson"></a>pass.json

JSON é o formato porque passa normalmente é criadas em um servidor – isso significa que o código de geração é independente de plataforma no servidor. Os três principais componentes de informações em cada fase são:

-   **teamIdentifier** – Isso vincula todos os passos que você gerar para sua conta da App Store. Esse valor é visível no Portal de provisionamento do iOS.
-   **passTypeIdentifier** – Registre-se no Portal de provisionamento para o grupo passa juntos (se você produzir mais de um tipo). Por exemplo, um café pode criar um tipo de aprovação de cartão de armazenamento para permitir que seus clientes para receber os créditos de fidelidade, mas também um cupom separado passar o tipo para criar e distribuir cupons de desconto. Esse mesmo café até mesmo pode manter os eventos de música ao vivo e emitir um tíquete do evento passa para aqueles.
-   **serialNumber** – uma cadeia de caracteres exclusiva dentro desta `passTypeidentifier` . O valor é opaco ao Wallet, mas é importante para acompanhar a passagens específicas ao se comunicar com seu servidor.

Há um grande número de outras chaves JSON em cada passagem, um exemplo disso é mostrado abaixo:

``` 
{
   "passTypeIdentifier":"com.xamarin.passkitdoc.banana",  //Type Identifier (iOS Provisioning Portal)
   "formatVersion":1,                                     //Always 1 (for now)
   "organizationName":"Xamarin",                          //The name which appears on push notifications
   "serialNumber":"12345436XYZ",                          //A number for you to identify this pass
   "teamIdentifier":"XXXAAA1234",                         //Your Team ID
   "description":"Xamarin Demo",                          //
   "foregroundColor":"rgb(54,80,255)",                    //color of the data text (note the syntax)
   "backgroundColor":"rgb(209,255,247)",                  //color of the background
   "labelColor":"rgb(255,15,15)",                         //color of label text and icons
   "logoText":"Banana ",                                  //Text that appears next to logo on top
   "barcode":{                                            //Specification of the barcode (optional)
      "format":"PKBarcodeFormatQR",                       //Format can be QR, Text, Aztec, PDF417
      "message":"FREE-BANANA",                            //What to encode in barcode
      "messageEncoding":"iso-8859-1"                      //Encoding of the message
   },
   "relevantDate":"2012-09-15T15:15Z",                    //When to show pass on screen. ISO8601 formatted.
  /* The following fields are specific to which type of pass. The name of this object specifies the type, e.g., boardingPass below implies this is a boarding pass. Other options include storeCard, generic, coupon, and eventTicket */
   "boardingPass":{
/*headerFields, primaryFields, secondaryFields, and auxiliaryFields are arrays of field object. Each field has a key, label, and value*/
      "headerFields":[          //Header fields appear next to logoText
         {
            "key":"h1-label",   //Must be unique. Used by iOS apps to get the data.
            "label":"H1-label", //Label of the field
            "value":"H1"        //The actual data in the field
         },
         {
            "key":"h2-label",
            "label":"H2-label",
            "value":"H2"
         }
      ],
      "primaryFields":[       //Appearance differs based on pass type
         {
            "key":"p1-label",
            "label":"P1-label",
            "value":"P1"
         }
      ],
      "secondaryFields":[     //Typically appear below primaryFields
         {
            "key":"s1-label",
            "label":"S1-label",
            "value":"S1"
         }
      ],
      "auxiliaryFields":[    //Appear below secondary fields
         {
            "key":"a1-label",
            "label":"A1-label",
            "value":"A1"
         }
      ],
      "transitType":"PKTransitTypeAir"  //Only present in boradingPass type. Value can
                                        //Air, Bus, Boat, or Train. Impacts the picture
                                        //that shows in the middle of the pass.
   }
}
```

### <a name="barcodes"></a>Códigos de barras

Somente há suporte para formatos 2D: PDF417, Aztec, QR. Apple declarações que os códigos de barras 1D são inadequados para verificação em uma tela do telefone de luz de fundo.

Texto alternativo exibido abaixo o código de barras é opcional – alguns comerciantes desejam ser capaz de leitura/tipo manualmente.

Codificação de ISO-8859-1 é a mais comum, verificação de qual codificação é usada pelos sistemas de verificação que serão lido seus passes.

### <a name="relevancy-lock-screen"></a>Relevância (tela de bloqueio)

Há dois tipos de dados que podem causar uma passagem a ser exibido na tela de bloqueio:

 **Local**

Até 10 locais pode ser especificado em uma passagem, por exemplo, os repositórios que o cliente visita com frequência, ou o local de um aeroporto ou de cinema. Um cliente pode definir esses locais por meio de um aplicativo complementar ou o provedor poderia determiná-los dos dados de uso (se coletados com a permissão do cliente).

Quando a passagem de é exibida na tela de bloqueio, um limite é calculado para que quando o usuário sai da área a passagem de está oculta da tela de bloqueio. O radius está vinculado para passar o estilo para evitar abusos.

 **Data e hora**

Apenas uma data/hora podem ser especificada em uma passagem. A data e hora é útil para o disparo de lembretes de tela de bloqueio de passagens aéreas e ingressos para eventos.

Podem ser atualizados por push ou por meio da API do PassKit, para que a data/hora pode ser atualizada no caso de um tíquete de uso múltiplo (por exemplo, um tíquete de temporada um teatro ou complexo esportivo).

### <a name="localization"></a>Localização

Converter uma passagem em vários idiomas é semelhante para a localização de um aplicativo iOS – criar diretórios específicos com a linguagem de `.lproj` extensão e colocar os elementos localizados dentro. Traduções de texto devem ser inseridas em um `pass.strings` arquivo, ao mesmo tempo imagens localizadas devem ter o mesmo nome que a imagem que elas substituem na raiz da passagem.

## <a name="security"></a>Segurança

Passa é assinadas com um certificado privado que você gerar no Portal de provisionamento do iOS. As etapas para assinar a passagem são:

1.  Calcular um hash SHA1 para cada arquivo no diretório de passagem (não inclua o `manifest.json` ou `signature` arquivo, nenhum dos quais deve existir neste estágio assim mesmo).
1.  Gravar `manifest.json` como uma lista de chave/valor JSON de cada nome de arquivo com o hash.
1.  Usar o certificado para assinar o `manifest.json` do arquivo e gravar o resultado em um arquivo chamado `signature` .
1.  COMPACTE o tudo e fornecer o arquivo resultante um `.pkpass` extensão de arquivo.


Como sua chave privada é necessária para assinar a passagem, esse processo só deve ser feito em um servidor seguro que você controla. NÃO distribua suas chaves para tentar e gerar passa em um aplicativo.

 
## <a name="configuration-and-setup"></a>Instalação e configuração

Esta seção contém instruções para ajudar a configurar seus detalhes de provisionamento e criar sua primeira passagem.

### <a name="provisioning-passkit"></a>Provisionamento PassKit

Em ordem para uma passagem de inserir a App Store, que deve estar vinculada a uma conta de desenvolvedor. Isso requer duas etapas:

1.  A passagem deve ser registrada usando um identificador exclusivo, chamado de ID de tipo de passar.
1.  Um certificado válido deve ser gerado para assinar a passagem de assinatura digital do desenvolvedor.

Para criar um, faça passar a ID do tipo a seguir.

#### <a name="create-a-pass-type-id"></a>Criar uma ID de tipo de passagem

A primeira etapa é configurar uma ID de tipo de passar para cada diferentes _tipo_ de passe a ter suporte. ID do passe (ou identificador de tipo de passe) cria um identificador exclusivo para a passagem. Usaremos essa ID para vincular a passagem com sua conta de desenvolvedor usando um certificado.

1. No [seção certificados, identificadores e perfis do Portal de provisionamento do iOS](https://developer.apple.com/account/overview.action), navegue até **identificadores** e selecione **passar IDs de tipo** . Em seguida, selecione a **+** botão para criar um novo tipo de aprovação: [![](passkit-images/passid.png "Criar um novo tipo de passagem")](passkit-images/passid.png#lightbox)

2.   Fornecer um **descrição** (nome) e **identificador** (cadeia de caracteres exclusiva) para a passagem. Observe que todas as IDs de tipo passar deve começar com a cadeia de caracteres `pass.` neste exemplo, usamos `pass.com.xamarin.coupon.banana` : [![](passkit-images/register.png "Forneça uma descrição e um identificador")](passkit-images/register.png#lightbox)


3.   Confirme a ID do passe pressionando a **registrar** botão.

#### <a name="generate-a-certificate"></a>Gerar um certificado

Para criar um novo certificado para essa ID de tipo de passe, faça o seguinte:

1.  Selecione a ID do passe recém-criado na lista e clique em **editar** : [![](passkit-images/pass-done.png "Selecione a nova ID de passar na lista")](passkit-images/pass-done.png#lightbox)

    Em seguida, selecione **Create Certificate...** :

    [![](passkit-images/cert-dist.png "Selecione Criar certificado")](passkit-images/cert-dist.png#lightbox)


2.  Siga as etapas para criar um certificado de assinatura CSR (solicitação).
  
3. Pressione a **continuar** botão no portal do desenvolvedor e carregue o CSR para gerar seu certificado.

4. Baixe o certificado e clique duas vezes nele para instalá-lo no seu conjunto de chaves.


Agora que criamos um certificado para esta ID de tipo de passe, a próxima seção descreve como criar uma passagem manualmente.

Para obter mais informações sobre o provisionamento de carteira, consulte o [trabalhando com capacidades](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md) guia.

### <a name="create-a-pass-manually"></a>Criar uma passagem manualmente

Agora que criamos o tipo de passe, pode criar manualmente uma passagem de teste em um dispositivo ou simulador. As etapas para criar uma passagem são:

-  Crie um diretório para conter os arquivos de passagem.
-  Crie um arquivo pass.json que contém todos os dados necessários.
-  Inclua imagens na pasta (se necessário).
-  Calcular o hash SHA1 para todos os arquivos na pasta e gravar em manifest. JSON.
-  Entrada manifest. JSON com o arquivo. p12 de certificado baixado.
-  COMPACTE o conteúdo do diretório e renomear com extensão .pkpass.


Há alguns arquivos de origem na [código de exemplo](https://developer.xamarin.com/samples/monotouch/PassKit/) para este artigo que pode ser usado para gerar uma passagem. Use os arquivos a `CouponBanana.raw` diretório do diretório CreateAPassManually. Os seguintes arquivos estão presentes:

 [![](passkit-images/image18.png "Esses arquivos estão presentes")](passkit-images/image18.png#lightbox)

Abra pass.json e editar o JSON. Você deve atualizar pelo menos o `passTypeIdentifier` e `teamIdentifer` para corresponder à sua conta de desenvolvedor da Apple.

```csharp
"passTypeIdentifier" : "pass.com.xamarin.coupon.banana",
"teamIdentifier" : "?????????",
```

Você deve calcular o hash para cada arquivo e criar o `manifest.json` arquivo. Ele será algo parecido com isso quando você terminar:

```csharp
{
  "icon@2x.png" : "30806547dcc6ee084a90210e2dc042d5d7d92a41",
  "icon.png" : "87e9ffb203beb2cce5de76113f8e9503aeab6ecc",
  "pass.json" : "c83cd1441c17ecc6c5911bae530d54500f57d9eb",
  "logo.png" : "b3cd8a488b0674ef4e7d941d5edbb4b5b0e6823f",
  "logo@2x.png" : "3ccd214765507f9eab7244acc54cc4ac733baf87"
}
```

Em seguida, uma assinatura deve ser gerada para esse arquivo usando o certificado (arquivo. p12) que foi gerado para essa ID do tipo de passagem.

#### <a name="signing-on-a-mac"></a>Entrar em um Mac

Baixe o **materiais de suporte de semente de carteira** da [Downloads do Apple](https://developer.apple.com/downloads/index.action?name=Passbook) site. Use o `signpass` ferramenta transformar sua pasta em uma passagem (Isso também calculará o SHA1 hashes e COMPACTAR a saída em um arquivo .pkpass).

#### <a name="testing"></a>Testes

Se você examinar a saída dessas ferramentas (, definindo o nome do arquivo para. zip e, em seguida, abri-lo), você veria os arquivos a seguir (Observe a adição do `manifest.json` e `signature` arquivos):

 [![](passkit-images/image19.png "Examinando a saída dessas ferramentas")](passkit-images/image19.png#lightbox)

Depois de conectado, zipadas e renomeei o arquivo (por exemplo. para `BananaCoupon.pkpass`) você pode arrastá-lo para o simulador para testar ou enviar por email para si mesmo para recuperar em um dispositivo real. Você deve ver uma tela para **adicionar** a passagem, como este:

 [![](passkit-images/image20.png "Adicionar a tela de passagem")](passkit-images/image20.png#lightbox)

Normalmente, esse processo seria ser automatizado em um servidor, criação de passagem no entanto manual pode ser uma opção para pequenas empresas que estiver criando cupons que não exigem o suporte de um servidor de back-end.

## <a name="wallet"></a>Carteira

Wallet é a parte central do ecossistema do PassKit. Nesta captura de tela mostra o Wallet vazia e a aparência a lista de passagem e passa individuais:

 [![](passkit-images/image21.png "Esta captura de tela mostra o Wallet vazio e a aparência a lista de passagem e passa individuais")](passkit-images/image21.png#lightbox)

Recursos do Wallet incluem:

-  É o único lugar passes são renderizados com seu código de barras para verificação.
-  Usuário pode alterar as configurações de atualizações. Se habilitada, as notificações por push podem disparar atualizações para os dados na fase.
-  Usuário pode habilitar ou desabilitar a integração de tela de bloqueio. Se habilitada, isso permite a passagem apareça automaticamente na tela de bloqueio, com base nos dados de hora e o local de relevantes incorporados na fase.
-  O lado oposto da passagem de dá suporte a puxar para atualizar, se uma URL da web-server é fornecida na fase de JSON.
-  Complementar aplicativos pode ser aberta (ou baixado) se a ID do aplicativo é fornecida na fase de JSON.
-  Passes podem ser excluídos (com uma animação de destruição bonita).

## <a name="adding-passes-into-wallet"></a>Adicionando passa em carteira

Passes podem ser adicionados ao Wallet das seguintes maneiras:

* **Aplicativos de canal** – eles não manipulam passes diretamente, eles simplesmente carregar arquivos de passagem e apresentam ao usuário a opção de adicioná-lo ao Wallet. 

* **Complementar aplicativos** – eles são gravados pelos provedores para distribuir passes e oferecem uma funcionalidade adicional para procurar ou editá-los. Aplicativos xamarin. IOS têm acesso completo à API do PassKit para criar e manipular passes. Passa, em seguida, pode ser adicionadas ao Wallet usando o `PKAddPassesViewController`. Esse processo é descrito mais detalhadamente os **aplicativos complementares** seção deste documento.

### <a name="conduit-applications"></a>Aplicativos de canal

Aplicativos de canal são aplicativos intermediários que podem receber passa em nome do usuário e devem ser programados para reconhecer seu tipo de conteúdo e fornecer funcionalidade para adicionar ao Wallet. Exemplos de aplicativos de canal:

-   **Email** – reconhece anexo como uma passagem.
-   **Safari** – reconhece a passagem de Content-Type, quando um link de URL de passagem é clicado.
-   **Outros aplicativos personalizados** – qualquer aplicativo que recebe anexos ou abrir links (clientes de mídia social, leitores de email, etc.).


Esta captura de tela mostra como **Mail** no iOS 6 reconhece um anexo de passagem e (quando tocadas) oferece aos **Add** -lo ao Wallet.

 [![](passkit-images/image22.png "Esta captura de tela mostra como o email no iOS 6 reconhece um anexo de passagem")](passkit-images/image22.png#lightbox)

 [![](passkit-images/image23.png "Esta captura de tela mostra como o Mail oferece para adicionar um anexo de passagem a carteira")](passkit-images/image23.png#lightbox)

Se você estiver criando um aplicativo que pode ser um condutor de passes, eles podem ser reconhecidos pelo:

-  **Extensão de arquivo** -.pkpass
-  **Tipo de MIME** -application/vnd.apple.pkpass
-  **UTI** – com.apple.pkpass


A operação básica de um aplicativo de canal é recuperar o arquivo de passagem e chamar do PassKit `PKAddPassesViewController` dar ao usuário a opção de adicionar a passagem para a sua carteira. A implementação do controlador de exibição é abordada na próxima seção sobre **complementar aplicativos**.

Não é necessário ser provisionado para uma ID do tipo de passagem específica da mesma forma que os aplicativos complementares possuem aplicativos de canal.

## <a name="companion-applications"></a>Aplicativos complementares

Um aplicativo complementar fornece funcionalidade adicional para trabalhar com passes, incluindo criação de uma passagem, atualizando informações associadas a uma passagem e gerenciando caso contrário, passa associada ao aplicativo.

Aplicativos complementares não devem tentar duplicar os recursos do Wallet. Eles não devem exibir passa para a varredura.

O restante desta seção descreve como criar um complemento aplicativo básico que interage com PassKit.

### <a name="provisioning"></a>Provisionamento

Como o Wallet é uma tecnologia de armazenamento, o aplicativo precisa ser provisionado separadamente e não é possível usar o perfil de provisionamento de equipe ou Wildcard App ID. Consulte a [trabalhando com capacidades](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md) guia para criar uma ID do aplicativo exclusivo e um perfil de provisionamento para o aplicativo Wallet.

### <a name="entitlements"></a>Direitos

O **Entitlements. plist** arquivo deve ser incluído no projeto todos os recente do xamarin. IOS. Para adicionar um novo arquivo Entitlements. plist, siga as etapas a [trabalhando com direitos](~/ios/deploy-test/provisioning/entitlements.md) guia.

Para definir direitos de fazer o seguinte:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Clique duas vezes no **Entitlements. plist** arquivo no painel de soluções para abrir o editor de Entitlements. plist:

![](passkit-images/image31.png "Editor de Entitlements.plst")

Na seção Wallet, selecione a **habilitar carteira** opção

![](passkit-images/image32.png "Habilitar o direito de carteira")


A opção padrão é para seu aplicativo permitir que todos os tipos de passar. No entanto, é possível restringir seu aplicativo e permitir somente um subconjunto de tipos de passe de equipe. Para habilitar isso, selecione a **permitir o subconjunto da equipe passar tipos** e insira o identificador de tipo de passagem do subconjunto que você deseja permitir.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Clique duas vezes o **Entitlements. plist** arquivo para abrir o arquivo de origem XML.

Para adicionar os direitos do Wallet, defina as **propriedade** para `Passbook Identifiers` no menu suspenso, que definirá automaticamente a **tipo** `Array`. Em seguida, defina a cadeia de caracteres **valor** para `$(TeamIdentifierPrefix)*`:

![](passkit-images/image33.png "Habilitar o direito de carteira")

Isso habilitará o aplicativo a permitir todos os tipos de passes. Para restringir seu aplicativo e permitir somente um subconjunto de tipos de passe de equipe, defina o valor de cadeia de caracteres como:

`$(TeamIdentifierPrefix)pass.$(CFBundleIdentifier)`

Em que `pass.$(CFBundleIdentifier)` é a ID de passagem que foi criada [acima](~/ios/platform/passkit.md)

-----

### <a name="debugging"></a>Depuração

Se você tiver problemas ao implantar seu aplicativo, verifique se você está usando o correto **perfil de provisionamento** e que o `Entitlements.plist` está selecionado como o **direitos personalizados** arquivo em que o **assinatura do pacote do iPhone** opções.

Se esse erro ocorrer durante a implantação:

```csharp
Installation failed: Your code signing/provisioning profiles are not correctly configured (error: 0xe8008016)
```

em seguida, a `pass-type-identifiers` matriz de direitos está incorreta (ou não corresponde a **perfil de provisionamento**). Verifique se as IDs de tipo passar e sua ID de equipe estão corretos.

## <a name="classes"></a>Classes

As seguintes classes do PassKit estão disponíveis para aplicativos acessem os passos:

-  **PKPass** – uma instância de uma passagem.
-  **PKPassLibrary** – fornece a API para acessar os passos no dispositivo.
-  **PKAddPassesViewController** – usado para exibir uma passagem para o usuário salve em sua carteira.
-  **PKAddPassesViewControllerDelegate** – os desenvolvedores do xamarin. IOS

## <a name="example"></a>Exemplo

Fazer referência ao projeto PassLibrary na [código de exemplo](https://developer.xamarin.com/samples/monotouch/PassKit/) para este artigo. Ele demonstra as seguintes funções comuns que seriam necessário em um aplicativo complementar do Wallet:

### <a name="check-that-wallet-is-available"></a>Verifique se o Wallet está disponível

Wallet não está disponível no iPad, portanto, aplicativos devem verificar antes de tentar acessar os recursos do PassKit.

```csharp
if (PKPassLibrary.IsAvailable) {
    // create an instance and do stuff...
}
```

### <a name="creating-a-pass-library-instance"></a>Criando uma instância de biblioteca de passagem

A biblioteca do PassKit não é um singleton, os aplicativos devem criar e armazenar para acessar a API do PassKit da instância.

```csharp
if (PKPassLibrary.IsAvailable) {
    library = new PKPassLibrary ();
    // do stuff...
}
```

### <a name="get-a-list-of-passes"></a>Obter uma lista de etapas

Aplicativos podem solicitar uma lista de etapas da biblioteca. Essa lista é filtrada automaticamente pelo PassKit, para que você só poderá ver as passagens que foram criados pela sua ID de equipe e que são listadas em seus direitos.

```csharp
var passes = library.GetPasses ();  // returns PKPass[]
```

Observe que o simulador não filtrar a lista de etapas retornados, portanto, esse método sempre deve ser testado em dispositivos reais. Essa lista pode ser exibida em um UITableView. O [aplicativo de exemplo](https://developer.xamarin.com/samples/monotouch/PassKit/) se parece com isso, depois que dois cupons foram adicionados:

 [![](passkit-images/image29.png "A aparência do aplicativo de exemplo como este, depois que foram adicionados dois cupons")](passkit-images/image29.png#lightbox)

### <a name="displaying-passes"></a>Exibindo Passes

Um conjunto limitado de informações está disponível para a renderização de passagens dentro de aplicativos complementares.

Escolha esse conjunto de propriedades padrão para exibir listas de aprovado, o que o código de exemplo faz.

```csharp
string passInfo =
                "Desc:" + pass.LocalizedDescription
                + "\nOrg:" + pass.OrganizationName
                + "\nID:" + pass.PassTypeIdentifier
                + "\nDate:" + pass.RelevantDate
                + "\nWSUrl:" + pass.WebServiceUrl
                + "\n#" + pass.SerialNumber
                + "\nPassUrl:" + pass.PassUrl;
```

Essa cadeia de caracteres é mostrada como um alerta na [amostra](https://developer.xamarin.com/samples/monotouch/PassKit/):

 [![](passkit-images/image30.png "O alerta selecionado do cupom na amostra")](passkit-images/image30.png#lightbox)

Você também pode usar o `LocalizedValueForFieldKey()` método para recuperar dados de campos passos que você criou (como você saberá sobre quais campos devem ser presente). O exemplo de código não mostra isso.

### <a name="loading-a-pass-from-a-file"></a>Carregar uma passagem de um arquivo

Porque uma passagem só pode ser adicionada ao Wallet com a permissão do usuário, um controlador de exibição deve estar presente para deixá-los a decidir. Esse código é usado na **adicionar** botão no exemplo, para carregar uma passagem pré-criados que está incorporada no aplicativo (você deve substituir isso por um que você tenha assinado):

```csharp
NSData nsdata;
using ( FileStream oStream = File.Open (newFilePath, FileMode.Open ) ) {
        nsdata = NSData.FromStream ( oStream );
}
var err = new NSError(new NSString("42"), -42);
var newPass = new PKPass(nsdata,out err);
var pkapvc = new PKAddPassesViewController(newPass);
NavigationController.PresentModalViewController (pkapvc, true);
```

É exibida para a passagem **Add** e **Cancelar** opções:

 [![](passkit-images/image20.png "A passagem de apresentado com as opções de adicionar e Cancelar")](passkit-images/image20.png#lightbox)

### <a name="replace-an-existing-pass"></a>Substituir uma passagem de existente

Substituir uma passagem existente não requer a permissão do usuário, no entanto, ele falhará se a passagem de ainda não existir.

```csharp
if (library.Contains (newPass)) {
     library.Replace (newPass);
}
```

### <a name="editing-a-pass"></a>Editando uma passagem

PKPass não é mutável, portanto, não é possível atualizar objetos de passagem em seu código. Para alterar os dados em uma passagem de um aplicativo deve ter acesso a um servidor web que pode manter um registro de passagens e gerar um novo arquivo de passagem com valores atualizados que o aplicativo pode ser baixado.

Passagem de criação do arquivo deve ser feita em um servidor porque passes devem ser assinados com um certificado que deve ser mantido privada e segura.

Depois de um arquivo atualizado pass tiver sido gerado, use o `Replace` método para substituir os dados antigos no dispositivo.

### <a name="display-a-pass-for-scanning"></a>Exibir uma passagem de verificação

Como observado anteriormente, somente o Wallet pode exibir uma passagem de verificação. Uma passagem pode ser exibida usando o `OpenUrl` método conforme mostrado:

 `UIApplication.SharedApplication.OpenUrl (p.PassUrl);`

### <a name="receiving-notifications-of-changes"></a>Receber notificações de alterações

Aplicativos podem ouvir para as alterações feitas à biblioteca de passar usando o `PKPassLibraryDidChangeNotification`. Alterações pode ser causadas por notificações de disparo de atualizações em segundo plano, portanto, é uma boa prática para escutar em seu aplicativo.

```csharp
noteCenter = NSNotificationCenter.DefaultCenter.AddObserver (PKPassLibrary.DidChangeNotification, (not) => {
    BeginInvokeOnMainThread (() => {
        new UIAlertView("Pass Library Changed", "Notification Received", null, "OK", null).Show();
        // refresh the list
        var passlist = library.GetPasses ();
        table.Source = new TableSource (passlist, library);
        table.ReloadData ();
    });
}, library);  // IMPORTANT: must pass the library in
```

É importante passar uma instância de biblioteca ao se registrar para a notificação porque PKPassLibrary não é um singleton.

## <a name="server-processing"></a>Processamento do servidor

Uma discussão detalhada da criação de um aplicativo de servidor para dar suporte ao PassKit está além do escopo deste artigo introdutório.

Ver [dotnet passbook](https://github.com/tomasmcguinness/dotnet-passbook) código-fonte aberto C# código do lado do servidor.

## <a name="push-notifications"></a>Notificações por Push

Uma discussão detalhada de como usar notificações por push para atualizar passes está além do escopo deste artigo introdutório.

Você seria necessário para implementar a API REST como definida pela Apple para responder a solicitações da web de carteira quando as atualizações são necessárias.

Consulte da Apple [atualizando uma passagem](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/PassKit_PG/Updating.html#//apple_ref/doc/uid/TP40012195-CH5-SW1) guia para obter mais informações.

## <a name="summary"></a>Resumo

Este artigo introduzido PassKit, descritos alguns dos motivos por que é útil e descritas as diferentes partes que devem ser implementadas para uma solução completa do PassKit. Ele descreveu as etapas necessárias para configurar sua conta de desenvolvedor da Apple para criar é aprovado, o processo para fazer uma passagem manualmente e também como acessar as APIs do PassKit a partir de um aplicativo xamarin. IOS.

## <a name="related-links"></a>Links relacionados

- [Wallet para desenvolvedores](https://developer.apple.com/wallet/)
- [Exemplo de PassKit](https://developer.xamarin.com/samples/monotouch/PassKit/)
- [Guia do desenvolvedor do Wallet](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/PassKit_PG/index.html#//apple_ref/doc/uid/TP40012195-CH1-SW1)
- [Estruturas – Apple Pay e Wallet (vídeos WWDC)](https://developer.apple.com/videos/frameworks/apple-pay-and-wallet)
- [Referência do PassKit Framework](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Framework/_index.html)
- [Referência de serviço Web passbook](https://developer.apple.com/library/prerelease/ios/#documentation/PassKit/Reference/PassKit_WebService/WebService.html)
- [Sobre arquivos de passagem](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html)
- [dotnet-passbook](https://github.com/tomasmcguinness/dotnet-passbook), uma biblioteca de código-fonte aberto para a geração de pacotes de carteira de iOS
- [Introdução ao iOS 6](~/ios/platform/introduction-to-ios6/index.md)
