---
title: PassKit em xamarin
description: Carteira é um aplicativo iOS do sistema que armazena e exibe os códigos de barras e outras informações para vincular a transações do cliente em seu telefone com 'reais'.
ms.prod: xamarin
ms.assetid: 74B9973B-C1E8-B727-3F6D-59C1F98BAB3A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 0a4fd39e312cf96ac59eae97b1212f001c4ef799
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788342"
---
# <a name="passkit-in-xamarinios"></a>PassKit em xamarin

_Carteira é um aplicativo iOS do sistema que armazena e exibe os códigos de barras e outras informações para vincular a transações do cliente em seu telefone com 'reais'._

Carteira é um aplicativo para iPhones e iPod toca com iOS 6. Ele armazena e exibe os códigos de barras e outras informações para vincular a transações do cliente em seu telefone com 'reais'. Passa é geradas por comerciantes e enviada ao cliente por email, URLs ou de um aplicativo do iOS da loja. Carteira armazena e organiza todos os passos de um telefone e exibe lembretes de passagem na tela de bloqueio dependendo do valor de data/hora ou o local do dispositivo.

Este documento apresenta carteira, usando a API de Kit passar com xamarin e discute como implementar passa em seu servidor.

 [![](passkit-images/image1.png "A carteira armazena e organiza todos os passos de um telefone")](passkit-images/image1.png#lightbox)


## <a name="requirements"></a>Requisitos

Os recursos de armazenamento Kit abordados neste documento exigem iOS 6 e 4.5 Xcode, juntamente com o xamarin 6.0.


## <a name="introduction"></a>Introdução

O problema de chave que passe Kit resolve é a distribuição e o gerenciamento dos códigos de barras. Alguns exemplos do mundo real de como os códigos de barras são usados atualmente incluem:

-   **Comprando ingressos filme online** – os clientes normalmente são enviados por email um código de barras que representa seus bilhetes. Esse código de barras é impresso e levado para a cinema a ser verificado para a entrada.
-   **Cartões de fidelidade** – clientes realizar um número de diferentes placas de armazenamento específico no seu bolso ou carteira, para exibição e de verificação quando eles compram produtos.
-   **Cupons** – cupons são distribuídos por meio de email, como páginas da web pode ser impresso, por meio de letterboxes e como os códigos de barras em jornais e revistas. Os clientes colocá-los para um repositório para verificação, para receber os produtos, serviços ou descontos de retorno.
-   **Embarque passa** – semelhante ao comprar um tíquete de filme.


Kit de passagem oferece uma alternativa para cada um desses cenários:

-   **Permissões de filme** – após a compra, o cliente adiciona uma passagem de tíquete do evento (via email ou um link de site). Como o tempo para as abordagens de filme, a passagem aparecerão automaticamente na tela de bloqueio como um lembrete e na chegada em de cinema a passagem é facilmente recuperada e exibida na carteira de verificação.
-   **Cartões de fidelidade** – em vez de (ou além) fornecendo um cartão físico, repositórios podem emitir (via email ou após um logon de site) uma passagem de cartão de armazenamento. O armazenamento pode fornecer recursos adicionais, como o saldo da conta a passagem por meio de notificações por push de atualização, e usando os serviços de localização geográfica a passagem pode aparecerão automaticamente na tela de bloqueio quando o cliente está perto de um local de armazenamento.
-   **Cupons** – passa de cupom podem facilmente ser gerada com características exclusivas para ajudá-lo com o controle e distribuídas por meio de links de email ou site. Cupons baixados automaticamente podem aparecer na tela de bloqueio quando o usuário está perto de um local específico e/ou em uma determinada data (como quando está se aproximando da data de expiração). Como os cupons são armazenados no telefone do usuário, eles sempre são úteis e não se extravie. Cupons podem incentivar os clientes para baixar aplicativos complementares, como links de loja de aplicativos podem ser incorporados a passagem, aumentando o contrato com o cliente.
-   **Embarque passa** – depois de um processo de verificação online, o cliente deve receber sua fase de entrada via email ou um link. Um aplicativo complementar fornecido pelo provedor de transporte pode incluir o processo de seleção e também permitem que o cliente executar funções adicionais, como escolher a sua estação ou refeição. O provedor de transporte pode usar notificações por push para atualizar a passagem se o transporte está atrasada ou cancelada. Como a hora de embarque abordagens a passagem aparecerá na tela de bloqueio como um lembrete e fornecer acesso rápido para a passagem.


Essencialmente, passar Kit fornece uma maneira simple e conveniente para armazenar e exibir códigos de barras no dispositivo iPhone ou iPod touch. Com o tempo adicional e a integração de tela de bloqueio de local, notificações por push e aplicativo complementar integração-lo oferece uma base para vendas bastante sofisticadas, tíquetes e serviços de cobrança.


## <a name="pass-kit-ecosystem"></a>Ecossistema de Kit de passagem

Kit de passagem não é apenas uma API em CocoaTouch, em vez disso, ele é parte de um maior ecossistema de aplicativos, dados e serviços que facilitam o compartilhamento seguro e gerenciamento dos códigos de barras e outros dados. Este diagrama de alto nível mostra as diferentes entidades que podem estar envolvidas na criação e uso de passos:

 [![](passkit-images/image2.png "Este diagrama de alto nível mostra as entidades envolvidas na criação e uso de passagens")](passkit-images/image2.png#lightbox)

Cada parte do ecossistema tem uma função claramente definida:

-   **Carteira** – aplicativos iOS internos da Apple (para iPhone e iPod touch) que armazena e exibe passa. Este é o único lugar que passa é renderizadas para uso no mundo real (ou seja o código de barras é exibido, juntamente com todos os dados localizados na fase).
-   **Complementar aplicativos** – aplicativos iOS 6 por provedores de passagem para estender a funcionalidade de passos de emitir, como a adição de valor para um cartão de armazenamento, alterando o assento em uma passagem de embarque ou outra função específicos de negócios. Aplicativos complementares não são necessários para uma passagem ser útil.
-   **O servidor** – um servidor seguro onde passa pode ser geradas e assinadas para distribuição. Seu aplicativo complementar pode se conectar ao seu servidor para gerar novos passa ou solicitar atualizações passa existente. Opcionalmente, você pode implementar a API do serviço web que carteira chamada para atualização passa.
-   **Servidores de APNS** – o servidor tem a capacidade de notificar carteira de atualizações para uma passagem em um determinado dispositivo usando APNS. Enviar uma notificação para carteira que será, em seguida, entre em contato com seu servidor para obter detalhes sobre a alteração. Aplicativos complementares não precisará implementar APNS para esse recurso (podem escutar a `PKPassLibraryDidChangeNotification` ).
-   **Aplicativos de canal** – aplicativos que não manipular diretamente os passos (como aplicativos complementares), mas que pode melhorar seu utilitário reconhecer passa e permitindo que eles a ser adicionado à carteira. Os clientes de email, navegadores de rede social e outros aplicativos de agregação de dados podem todos encontrar anexos ou links para passa.


O ecossistema é complexo, portanto é importante observar que alguns componentes são opcionais e implementações de passar Kit muito mais simples são possíveis.

## <a name="what-is-a-pass"></a>O que é uma passagem?

Uma fase é uma coleção de dados que representa uma permissão, cupom ou um cartão. Pode ser a intenção de uso único por uma pessoa (e, portanto, contêm detalhes como uma alocação de número e a estação de voo) ou pode um token de usar vários que pode ser compartilhado por qualquer número de usuários (como um cupom de desconto). Uma descrição detalhada está disponível da Apple [sobre arquivos passar](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html) documento.


### <a name="types"></a>Tipos

No momento cinco tipos com suporte, que podem ser diferenciados no aplicativo carteira pela borda superior e de layout da passagem de:

-  **Tíquete do evento** – recorte semicircular pequeno.
-   **Passagem de entrada** – cortes no ícone de lado, específica de transporte podem ser especificados (por exemplo. barramento, treinamento, avião).
-   **Armazenar cartão** – arredondado superior, como um cartão de crédito ou débito.
-  **Cupom** – perfuradas na parte superior.
-  **Genérica** – mesmo que o cartão de armazenamento, arredondado superior.


Os tipos de cinco passagem são mostrados nesta captura de tela (na ordem: cupom, genérico, armazenar cartão, passagem de embarque e tíquete do evento):

 [![](passkit-images/image3.png "Os tipos de cinco passagem são mostrados nesta captura de tela")](passkit-images/image3.png#lightbox)

### <a name="file-structure"></a>Estrutura de arquivos

Um arquivo de passagem é realmente um arquivo ZIP com um **.pkpass** extensão, que contém alguns JSON arquivos específicos (obrigatório), uma variedade de imagem de arquivos (opcional), bem como cadeias de caracteres localizadas (também opcional).

-   **pass.JSON** – obrigatório. Contém todas as informações para a passagem.
-   **manifest.JSON** – obrigatório. Contém o hash SHA1 para cada arquivo na fase de exceto o arquivo de assinatura e o arquivo (manifest.json).
-   **assinatura** – obrigatório. Criado por assinatura do `manifest.json` arquivo com o certificado gerado no Portal de provisionamento do iOS.
-  **logo.PNG** – opcional.
-  **Background.PNG** – opcional.
-  **icon.PNG** – opcional.
-  **Arquivos de cadeias de caracteres localizáveis** – opcional.


Estrutura de diretório de um arquivo de passagem é mostrada abaixo (Este é o conteúdo do arquivo ZIP):

 [![](passkit-images/image4.png "Estrutura de diretório de um arquivo de passagem é mostrada aqui")](passkit-images/image4.png#lightbox)

### <a name="passjson"></a>pass.json

JSON é o formato porque passa normalmente é criadas em um servidor – isso significa que o código de geração é independente de plataforma no servidor. As três partes principais de informações em cada fase são:

-   **teamIdentifier** – Isso vincula todos os passos que você gerar para sua conta de armazenamento de aplicativo. Esse valor é visível no Portal de provisionamento do iOS.
-   **passTypeIdentifier** – registrar no Portal de provisionamento para o grupo passa juntos (se você produzir mais de um tipo). Por exemplo, uma cafeteria pode criar um tipo de passar de cartão de armazenamento para permitir que seus clientes ganhar créditos de fidelidade, mas também é um tipo de passar de cupom separado para criar e distribuir cupons de desconto. Esse mesmo cafeteria ainda pode manter os eventos de música ao vivo e emita passa de tíquete do evento para aqueles.
-   **serialNumber** – uma cadeia de caracteres exclusiva dentro deste `passTypeidentifier` . O valor é opaco para carteira, mas é importante para acompanhar a passagens específicas ao se comunicar com o servidor.


Há um grande número de outras chaves JSON em cada etapa, um exemplo é mostrado abaixo:

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

Há suporte para formatos somente 2D: PDF417, asteca, QR. Apple declarações que códigos de barras 1D são inadequados para verificação em uma tela de telefone de luz de fundo.

Texto alternativo exibido abaixo o código de barras é opcional – alguns comerciantes desejem ser capaz de ler/tipo manualmente.

Codificação ISO 8859-1 é a seleção mais comuns, qual codificação é usado pelos sistemas de verificação que lerão a passa.

### <a name="relevancy-lock-screen"></a>Relevância (tela de bloqueio)

Há dois tipos de dados que podem causar uma passagem a ser exibida na tela de bloqueio:

 **Local**

Até 10 locais pode ser especificado em uma passagem, por exemplo, lojas que frequentemente visita um cliente ou o local de um cinema ou aeroporto. Um cliente pode definir esses locais por meio de um aplicativo complementar ou o provedor pode determiná-los de dados de uso (se coletados com a permissão do cliente).

Quando a senha é exibida na tela de bloqueio, um limite é calculado para que quando o usuário sai da área a passagem está oculta da tela de bloqueio. O radius está ligado para passar um estilo para evitar o abuso.

 **Data e hora**

Apenas uma data/hora podem ser especificada em uma passagem. A data e hora é útil para disparar lembretes de tela de bloqueio para os passos de embarque e tíquetes de evento.

Podem ser atualizados por push ou por meio da API PassKit, para que a data/hora pode ser atualizada no caso de um tíquete de uso de vários (por exemplo, um tíquete de temporada um teatro ou complexos esportivo).

### <a name="localization"></a>Localização

Converter uma passagem em vários idiomas é semelhante a localização de um aplicativo iOS – criar diretórios específicos com a linguagem de `.lproj` extensão e colocar os elementos localizados dentro. Traduções de texto devem ser inseridas em um `pass.strings` de arquivo, enquanto imagens localizadas devem ter o mesmo nome que a imagem que substituem na raiz de passagem.

## <a name="security"></a>Segurança

Passa é assinadas com um certificado privado que você gerar o Portal de provisionamento do iOS. As etapas para assinar a passagem são:

1.  Calcular um hash SHA1 para cada arquivo no diretório de passagem (não inclua o `manifest.json` ou `signature` arquivo, nenhum dos quais deve existir neste estágio assim mesmo).
1.  Gravar `manifest.json` como uma lista de chave/valor JSON de cada nome de arquivo com o hash.
1.  Usar o certificado para assinar o `manifest.json` de arquivo e gravar o resultado em um arquivo chamado `signature` .
1.  COMPACTE o tudo e forneça o arquivo resultante um `.pkpass` extensão de arquivo.


Como sua chave privada é necessária para assinar a passagem, esse processo só deve ser feito em um servidor seguro que você controle. NÃO distribua as chaves para tentar gerar passa em um aplicativo.

 
## <a name="configuration-and-setup"></a>Instalação e configuração

Esta seção contém instruções para ajudar a configurar seus detalhes de provisionamento e criar sua primeira passagem.

### <a name="provisioning-passkit"></a>Provisionamento PassKit

Em ordem para uma passagem de inserir a loja de aplicativos, ele deve estar vinculado a uma conta de desenvolvedor. Isso requer duas etapas:

1.  A passagem deve ser registrada com um identificador exclusivo, chamado de ID do tipo passar.
1.  Um certificado válido deve ser gerado para assinar a passagem de assinatura digital do desenvolvedor.

Para criar um faça passar a ID do tipo a seguir.


<a name="create-passid"/>

#### <a name="create-a-pass-type-id"></a>Criar uma ID de tipo de passagem

A primeira etapa é configurar uma ID de tipo de passar para cada diferentes _tipo_ de passagem com suporte. A ID de passar (ou o identificador de tipo passar) cria um identificador exclusivo para a passagem. Usaremos essa ID para vincular a passagem com sua conta de desenvolvedor usando um certificado.

1. No [seção certificados, identificadores e perfis do Portal de provisionamento iOS](https://developer.apple.com/account/overview.action), navegue até **identificadores** e selecione **passar IDs de tipo** . Selecione o **+** botão para criar um novo tipo de passagem: [ ![ ] (passkit-images/passid.png "criar um novo tipo de passagem")](passkit-images/passid.png#lightbox)

2.   Forneça um **descrição** (nome) e **identificador** (cadeia de caracteres exclusiva) para a passagem. Observe que todas as IDs de tipo passar deve começar com a cadeia de caracteres `pass.` neste exemplo, usamos `pass.com.xamarin.coupon.banana` : [ ![ ] (passkit-images/register.png "fornecem uma descrição e um identificador")](passkit-images/register.png#lightbox)


3.   Confirme a ID de passar pressionando o **registrar** botão.


<a name="generate" />

#### <a name="generate-a-certificate"></a>Gerar um certificado

Para criar um novo certificado para essa ID de tipo passar, faça o seguinte:

1.  Selecione a ID de passar recém-criada na lista e clique em **editar** : [ ![ ] (passkit-images/pass-done.png "selecione a nova ID de passar na lista")](passkit-images/pass-done.png#lightbox)

    Em seguida, selecione **Create Certificate...** :

    [![](passkit-images/cert-dist.png "Selecione Criar certificado")](passkit-images/cert-dist.png#lightbox)


2.  Siga as etapas para criar uma assinatura de solicitação certificado (CSR).
  
3. Pressione a **continuar** botão no portal do desenvolvedor e carregar o CSR para gerar o certificado.

4. Baixe o certificado e clique duas vezes nele para instalá-lo no seu conjunto de chaves.


Agora que você criou um certificado para essa ID de tipo passar, a próxima seção descreve como criar uma passagem manualmente.

Para obter mais informações sobre o provisionamento para carteira, consulte o [trabalhando com recursos](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md) guia.

 <a name="Create_a_Pass_Manually" />

### <a name="create-a-pass-manually"></a>Criar uma passagem manualmente

Agora que criamos o tipo passar, pode criar manualmente uma passagem de teste em um dispositivo ou o simulador. As etapas para criar uma passagem são:

-  Crie um diretório para os arquivos de passagem.
-  Crie um arquivo de pass.json que contém todos os dados necessários.
-  Inclua imagens na pasta (se necessário).
-  Calcular o hash SHA1 para todos os arquivos na pasta e gravar manifest.json.
-  Assinar manifest.json com o arquivo do certificado baixado. p12.
-  COMPACTE o conteúdo do diretório e renomear com extensão .pkpass.


Há alguns arquivos de origem no código de exemplo para este artigo que pode ser usado para gerar uma passagem. Use os arquivos de `CouponBanana.raw` diretório do diretório CreateAPassManually. Os seguintes arquivos estão presentes:

 [![](passkit-images/image18.png "Esses arquivos estão presentes")](passkit-images/image18.png#lightbox)

Abra pass.json e editar o JSON. Você deve atualizar pelo menos o `passTypeIdentifier` e `teamIdentifer` para corresponder a sua conta de desenvolvedor da Apple.

```csharp
"passTypeIdentifier" : "pass.com.xamarin.coupon.banana",
"teamIdentifier" : "?????????",
```

Você deve calcular o hash para cada arquivo e criar o `manifest.json` arquivo. Ele será parecida com isto quando você terminar:

```csharp
{
  "icon@2x.png" : "30806547dcc6ee084a90210e2dc042d5d7d92a41",
  "icon.png" : "87e9ffb203beb2cce5de76113f8e9503aeab6ecc",
  "pass.json" : "c83cd1441c17ecc6c5911bae530d54500f57d9eb",
  "logo.png" : "b3cd8a488b0674ef4e7d941d5edbb4b5b0e6823f",
  "logo@2x.png" : "3ccd214765507f9eab7244acc54cc4ac733baf87"
}
```

Em seguida uma assinatura deve ser gerada para esse arquivo usando o certificado (arquivo. p12) que foi gerado para a ID do tipo de passagem.

 <a name="Signing_On_a_Mac" />


#### <a name="signing-on-a-mac"></a>Assinatura em um Mac

Baixar o **materiais de suporte de semente carteira** do [Apple Downloads](https://developer.apple.com/downloads/index.action?name=Passbook) site. Use o `signpass` ferramenta para transformar a pasta em uma passagem (Isso também irá calcular a SHA1 hashes e ZIP a saída em um arquivo .pkpass).

 <a name="Signing_On_a_PC" />


#### <a name="signing-on-a-pc"></a>Autenticação em um computador

No exemplo de código para este artigo existe é um projeto chamado `signpassnet` que é executado no .NET no Windows. Ele tenta simular ferramenta da Apple, no entanto, ele inclui muito menos código de validação.

 <a name="Testing" />


#### <a name="testing"></a>Testes

Se você examinar a saída dessas ferramentas (, definindo o nome do arquivo para. zip e, em seguida, abri-lo), você verá os arquivos a seguir (Observe a adição do `manifest.json` e `signature` arquivos):

 [![](passkit-images/image19.png "Examinar a saída dessas ferramentas")](passkit-images/image19.png#lightbox)

Depois de conectado, zipadas e renomear o arquivo (por exemplo. para `BananaCoupon.pkpass`) você pode arrastá-la para o simulador para testar ou email, a mesmo para recuperar em um dispositivo real. Você verá uma tela para **adicionar** passo, como este:

 [![](passkit-images/image20.png "Adicionar a tela de passagem")](passkit-images/image20.png#lightbox)

Normalmente esse processo deve ser automatizado em um servidor, criação de passagem no entanto manual pode ser uma opção para pequenas empresas que estiver criando cupons que não exigem o suporte de um servidor de back-end.

 <a name="Wallet" />

## <a name="wallet"></a>Carteira

Carteira é a parte central do ecossistema de passar Kit. Esta captura de tela mostra a carteira vazia e aparência de lista de passagem e passa individuais:

 [![](passkit-images/image21.png "Esta captura de tela mostra a carteira vazia e aparência de lista de passagem e passa individuais")](passkit-images/image21.png#lightbox)

Recursos da carteira incluem:

-  É o único lugar passa é processadas com o código de barras para verificação.
-  Usuário pode alterar as configurações de atualizações. Se habilitada, as notificações por push podem disparar atualizações para os dados na fase.
-  Usuário pode habilitar ou desabilitar a integração de tela de bloqueio. Se habilitado, isso permite a passagem de aparecem automaticamente em sua tela de bloqueio, com base em dados relevantes de hora e o local inseridos na fase.
-  Verso da passagem de suporta puxe para atualizar, se uma URL da web-server é fornecida em JSON passar.
-  Complementar aplicativos pode ser aberto (ou baixado) se a ID do aplicativo é fornecida em JSON passar.
-  Passa pode ser excluída (com uma animação de destruição bonita).


 <a name="Getting_Passes_into_Wallet" />

## <a name="adding-passes-into-wallet"></a>Adicionando passa em carteira

Passa pode ser adicionada a carteira das seguintes maneiras:

* **Aplicativos de canal** – eles não manipuladas passa diretamente, basta carregar arquivos de passagem e apresentar ao usuário a opção de adicioná-los à carteira. 

* **Complementar aplicativos** – eles são gravados pelos provedores para distribuir passa e oferecer funcionalidade adicional para navegar ou editá-los. Xamarin aplicativos têm acesso completo à API do Kit de passar para criar e manipular passa. Passa pode ser adicionadas à carteira usando o `PKAddPassesViewController`. Esse processo é descrito mais detalhadamente o **aplicativos complementares** seção deste documento.

### <a name="conduit-applications"></a>Aplicativos de canal

Aplicativos de canal são aplicativos intermediários que podem receber passa em nome do usuário e devem ser programados para reconhecer o tipo de conteúdo e fornecer funcionalidade para adicionar a embalagem do. Exemplos de aplicativos de canal:

-   **Email** – reconhece anexo como uma passagem.
-   **Safari** – reconhece o tipo de conteúdo passar quando um link de URL passar é clicado.
-   **Outros aplicativos personalizados** – qualquer aplicativo que recebe anexos ou abrir links (clientes de mídia social, leitores de email, etc.).


Esta captura de tela mostra como **Mail** no iOS 6 reconhece um anexo de passagem e (quando tocadas) oferece a opção de **adicionar** para carteira.

 [![](passkit-images/image22.png "Esta captura de tela mostra como o email no iOS 6 reconhece um anexo de passagem")](passkit-images/image22.png#lightbox)

 [![](passkit-images/image23.png "Esta captura de tela mostra como Mail oferece a opção de adicionar um anexo de passagem para carteira")](passkit-images/image23.png#lightbox)

Se você estiver criando um aplicativo que pode ser um canal de passagens, pode ser reconhecidos por:

-  **Extensão de arquivo** -.pkpass
-  **Tipo MIME** -application/vnd.apple.pkpass
-  **Utilitário** – com.apple.pkpass


A operação básica de um aplicativo de canal é para recuperar o arquivo de passagem e chamar Kit passar `PKAddPassesViewController` para dar ao usuário a opção para adicionar a passagem para seu bolso. A implementação deste controlador de exibição é abordada na próxima seção na **complementar aplicativos**.

Aplicativos de canal não precisa ser provisionado para uma ID de tipo específico passar da mesma forma que os aplicativos complementares.

## <a name="companion-applications"></a>Aplicativos complementares

Um aplicativo complementar fornece funcionalidade adicional para trabalhar com passa, incluindo a criação uma passagem, atualizando informações associadas a uma passagem e gerenciando caso contrário, passa associado ao aplicativo.

Aplicativos complementares não devem tentar duplicar os recursos da carteira. Eles não devem exibir passa para a varredura.

Este restante desta seção descreve como criar um complementar aplicativo básico que interage com o Kit de passar.

### <a name="provisioning"></a>Provisionamento

Como carteira é uma tecnologia de armazenamento, o aplicativo precisa ser provisionado separadamente e não pode usar o perfil de provisionamento de equipe ou Wildcard App ID. Consulte o [trabalhando com recursos](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md) guia para criar um perfil de provisionamento e o ID do aplicativo exclusivo para o aplicativo carteira.

### <a name="entitlements"></a>Direitos

O **Entitlements.plist** arquivo deve ser incluído no projeto de xamarin todos os recente. Para adicionar um novo arquivo Entitlements.plist, siga as etapas a [trabalhando com direitos](~/ios/deploy-test/provisioning/entitlements.md) guia.

Para definir direitos de fazer o seguinte:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Clique duas vezes no **Entitlements.plist** arquivo no painel de solução para abrir o editor de Entitlements.plist:

![](passkit-images/image31.png "Editor de Entitlements.plst")

Na seção carteira, selecione o **carteira habilitar** opção

![](passkit-images/image32.png "Habilitar o direito de carteira")


A opção padrão é para seu aplicativo permitir que todos os tipos de passar. No entanto, é possível restringir o seu aplicativo e permitir que apenas um subconjunto de tipos de passagem de equipe. Para habilitar isso, marque o **Permitir subconjunto da equipe passar tipos** e insira o identificador de tipo de passagem do subconjunto que você deseja permitir.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Clique duas vezes o **Entitlements.plist** arquivo para abrir o arquivo de origem XML.

Para adicionar o direito de carteira, defina o **propriedade** para `Passbook Identifiers` na lista suspensa, que definirá automaticamente a **tipo** `Array`. Em seguida, defina a cadeia de caracteres **valor** para `$(TeamIdentifierPrefix)*`:

![](passkit-images/image33.png "Habilitar o direito de carteira")

Isso habilitará o aplicativo a permitir todos os tipos de passes. Para restringir o seu aplicativo e permitir que apenas um subconjunto de tipos de passagem de equipe, defina o valor de cadeia de caracteres como:

`$(TeamIdentifierPrefix)pass.$(CFBundleIdentifier)`

Onde `pass.$(CFBundleIdentifier)` é a ID de passar que tenha sido criado [acima](~/ios/platform/passkit.md)

-----

### <a name="debugging"></a>Depuração

Se você tiver problemas ao implantar seu aplicativo, verifique se você está usando o correto **perfil de provisionamento de** e que o `Entitlements.plist` é selecionado como o **direitos personalizados** arquivo o **iPhone assinatura de pacote** opções.

Se esse erro ocorrer durante a implantação:

```csharp
Installation failed: Your code signing/provisioning profiles are not correctly configured (error: 0xe8008016)
```

em seguida, o `pass-type-identifiers` matriz de direitos está incorreto (ou não corresponde a **perfil de provisionamento de**). Verifique se as IDs de tipo passar e sua ID da equipe estão corretas.

 <a name="Classes" />

## <a name="classes"></a>Classes

As seguintes classes de passar Kit estão disponíveis para aplicativos acessem os passos:

-  **PKPass** – uma instância de uma passagem.
-  **PKPassLibrary** – fornece a API para acessar os passos no dispositivo.
-  **PKAddPassesViewController** – usado para exibir uma passagem para o usuário salve em seu bolso.
-  **PKAddPassesViewControllerDelegate** – os desenvolvedores do xamarin


## <a name="example"></a>Exemplo

Consulte o projeto PassLibrary no código de exemplo para este artigo. Ele demonstra as seguintes funções comuns que seriam necessário em um aplicativo de complementar carteira:

### <a name="check-that-wallet-is-available"></a>Verifique se carteira está disponível

Carteira não está disponível no iPad, por isso aplicativos devem verificar antes de tentar acessar os recursos do Kit de passar.

```csharp
if (PKPassLibrary.IsAvailable) {
    // create an instance and do stuff...
}
```

### <a name="creating-a-pass-library-instance"></a>Criando uma instância da biblioteca de passagem

A biblioteca de passar Kit não é um singleton, aplicativos devem criar e armazenar e da instância para acessar a API de Kit passar.

```csharp
if (PKPassLibrary.IsAvailable) {
    library = new PKPassLibrary ();
    // do stuff...
}
```

### <a name="get-a-list-of-passes"></a>Obter uma lista de etapas

Aplicativos podem solicitar uma lista de etapas da biblioteca. Essa lista é filtrada automaticamente pelo Kit de passar para que você só poderá ver os passos que tenha sido criada com sua ID de equipe e que são listados em seus direitos.

```csharp
var passes = library.GetPasses ();  // returns PKPass[]
```

Observe que o simulador não filtrar a lista de passagens retornado, para que esse método sempre deve ser testado em dispositivos reais. Essa lista pode ser exibida em um UITableView, a aparência do aplicativo de exemplo desta após dois cupons foram adicionados:

 [![](passkit-images/image29.png "A aparência do aplicativo de exemplo como isso depois que foram adicionados dois cupons")](passkit-images/image29.png#lightbox)


### <a name="displaying-passes"></a>Exibindo passa

Um conjunto limitado de informações está disponível para a renderização de passagens dentro de aplicativos do complemento.

Escolha esse conjunto de propriedades padrão para exibir a lista de etapas, que o código de exemplo faz.

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

Essa cadeia de caracteres é mostrada como um alerta no exemplo:

 [![](passkit-images/image30.png "O alerta selecionado do cupom no exemplo")](passkit-images/image30.png#lightbox)

Você também pode usar o `LocalizedValueForFieldKey()` método para recuperar dados de campos passos que você criou (desde que você saiba quais campos devem ser presente). O exemplo de código não mostrar isso.

### <a name="loading-a-pass-from-a-file"></a>Carregar uma passagem de um arquivo

Como uma passagem somente pode ser adicionada ao carteira com a permissão do usuário, um controlador de exibição deve ser apresentado para mantê-lo a decidir. Esse código é usado no **adicionar** botão no exemplo, para carregar uma passagem predefinida que é inserida no aplicativo (deve substituí-lo com aquele que você tenha assinado):

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

A fase é apresentada com **adicionar** e **Cancelar** opções:

 [![](passkit-images/image20.png "A passagem de conhecer as opções de adicionar e Cancelar")](passkit-images/image20.png#lightbox)

### <a name="replace-an-existing-pass"></a>Substituir uma senha existente

Substituir uma passagem existente não requer a permissão do usuário, no entanto, ele falhará se a senha ainda não existir.

```csharp
if (library.Contains (newPass)) {
     library.Replace (newPass);
}
```

### <a name="editing-a-pass"></a>Editando uma passagem

PKPass não é mutável, assim você não pode atualizar objetos de passagem em seu código. Para alterar os dados em uma passagem de um aplicativo deve ter acesso a um servidor web que podem manter um registro de etapas e gerar um novo arquivo de passagem com valores atualizados que pode baixar o aplicativo.

Fase de criação do arquivo deve ser feita em um servidor porque passa deve ser assinadas com um certificado que deve ser mantido em particular e segura.

Depois de um arquivo atualizado de passagem foi gerado, use o `Replace` método para substituir os dados antigos no dispositivo.

### <a name="display-a-pass-for-scanning"></a>Exibir uma passagem de verificação

Conforme observado anteriormente, somente carteira pode exibir uma passagem de verificação. Uma passagem pode ser exibida usando o `OpenUrl` método conforme mostrado:

 `UIApplication.SharedApplication.OpenUrl (p.PassUrl);`

### <a name="receiving-notifications-of-changes"></a>Receber notificações de alterações

Aplicativos podem escutar alterações sejam feitas para a biblioteca de passar usando o `PKPassLibraryDidChangeNotification`. Alterações podem ser causadas por notificações disparar atualizações em segundo plano, portanto, é uma boa prática para escutar em seu aplicativo.

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

É importante passar uma instância de biblioteca ao registrar para a notificação porque PKPassLibrary não é um singleton.

## <a name="server-processing"></a>Processamento do servidor

Uma discussão detalhada da criação de um aplicativo de servidor para dar suporte a passar Kit está além do escopo deste artigo introdutório.

O código .NET fornecido a *signpassnet* exemplo pode ser usado como base para um método do lado do servidor que pode gerar passa.

Exibição [WWDC vídeo: Introdução ao Caderneta, parte 2](https://developer.apple.com/videos/wwdc/2012/?include=309#309) de 27:00 minutos para obter mais informações.

### <a name="other-resources"></a>Outros recursos

Consulte [dotnet Caderneta](https://github.com/tomasmcguinness/dotnet-passbook) abrir o código-fonte c# do lado do servidor.

## <a name="push-notifications"></a>Notificações por Push

Uma discussão detalhada de como usar notificações por push para atualização passa está além do escopo deste artigo introdutório.

Você seria necessário para implementar a API REST como definida pela Apple para responder às solicitações da web da carteira quando forem necessárias atualizações. O código .NET fornecido a *signpassnet* exemplo pode ser usado como base para gerar novos passa como resultado dessas solicitações.

Exibição [WWDC vídeo: Introdução ao Caderneta, parte 2](https://developer.apple.com/videos/wwdc/2012/?include=309#309) de 27:00 minutos para obter mais informações.

## <a name="summary"></a>Resumo

Este artigo introduzido passar Kit, apresentadas algumas das razões é útil por e descritas as diferentes partes devem ser implementadas para uma solução completa de passar Kit. Ele descritas as etapas necessárias para configurar sua conta de desenvolvedor da Apple para criar passa, o processo de fazer uma passagem manualmente e também como acessar as APIs de Kit passar de um aplicativo xamarin.

## <a name="related-links"></a>Links relacionados

- [CreateAPassManually (exemplo)](https://developer.xamarin.com/samples/PassKit/)
- [Exemplo de PassKit](https://developer.xamarin.com/samples/monotouch/PassKit/)
- [Introdução ao iOS 6](~/ios/platform/introduction-to-ios6/index.md)
- [Guia de programação de senha](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Conceptual/PassKit_PG/Chapters/Introduction.html)
- [Senha para desenvolvedores](https://developer.apple.com/passbook/)
- [Sobre arquivos de passagem](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html)
- [Transmitir estrutura do Kit de referência](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Framework/_index.html)
- [Transmitir estrutura do Kit de referência](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Framework/_index.html)
- [Referência de serviço Web de senha](https://developer.apple.com/library/prerelease/ios/#documentation/PassKit/Reference/PassKit_WebService/WebService.html)
