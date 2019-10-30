---
title: PassKit no Xamarin. iOS
description: O aplicativo de carteira permite que os usuários do iOS armazenem passagens digitais em seus dispositivos. A estrutura PassKit permite que os desenvolvedores interajam com os passos programaticamente.
ms.prod: xamarin
ms.assetid: 74B9973B-C1E8-B727-3F6D-59C1F98BAB3A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/13/2018
ms.openlocfilehash: a51ab165d8eac2e3c881871bc71456c8279c5461
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031675"
---
# <a name="passkit-in-xamarinios"></a>PassKit no Xamarin. iOS

O aplicativo de carteira do iOS permite que os usuários armazenem passagens digitais em seus dispositivos.
Esses passos são gerados por comerciantes e enviados ao cliente por email, URLs ou por meio do próprio aplicativo iOS do comerciante. Esses passos podem representar várias coisas, desde tíquetes de filmes até cartões de fidelidade até passagens de tabuleiro. A estrutura PassKit permite que os desenvolvedores interajam com os passos programaticamente.

Este documento apresenta a carteira e o uso da API PassKit com Xamarin. iOS.

 [![](passkit-images/image1.png "The Wallet stores and organizes all the passes on a phone")](passkit-images/image1.png#lightbox)

## <a name="requirements"></a>Requisitos

Os recursos do PassKit discutidos neste documento exigem o iOS 6 e o Xcode 4,5, juntamente com o Xamarin. iOS 6,0.

## <a name="introduction"></a>Introdução

O principal problema que o PassKit resolve é a distribuição e o gerenciamento de códigos de barras. Alguns exemplos do mundo real de como os códigos de barras são usados no momento incluem:

- **Comprando tíquetes de filmes online** – os clientes normalmente recebem um email de um código de barras que representa seus tíquetes. Esse código de barras é impresso e levado para o cinema a ser verificado quanto à entrada.
- **Cartões de fidelidade** – os clientes carregam uma série de cartões específicos de repositório diferentes em sua carteira ou bolsa, para exibição e digitalização ao comprar mercadorias.
- **Cupons** – os cupons são distribuídos por email, como páginas da Web imprimíveis, por meio de letterboxes e de códigos de barras em jornais e revistas. Os clientes os levam para uma loja para verificação, para receber bens, serviços ou descontos em retorno.
- **Passagens de tabuleiro** – semelhante à compra de um tíquete de filme.

O PassKit oferece uma alternativa para cada um desses cenários:

- **Tíquetes de filme** – após a compra, o cliente adiciona uma passagem de tíquete de evento (por email ou um link de site). Como o horário para o filme se aproxima, a passagem aparecerá automaticamente na tela de bloqueio como lembrete e, na chegada do cinema, a passagem será facilmente recuperada e exibida em carteira para verificação.
- **Cartões de fidelidade** – em vez de (ou além de) fornecer um cartão físico, os armazenamentos podem emitir (por email ou depois de um logon de site) uma passagem de cartão de loja. A loja pode fornecer recursos adicionais, como a atualização do saldo da conta na passagem por meio de notificações por push e o uso de serviços de localização geográfica. a passagem pode aparecer automaticamente na tela de bloqueio quando o cliente está próximo de um local de armazenamento.
- **Cupons** – as passagens de cupom podem ser facilmente geradas com características exclusivas para ajudar no rastreamento e distribuídas por email ou links de sites. Os cupons baixados podem aparecer automaticamente na tela de bloqueio quando o usuário está próximo a um local específico e/ou em uma determinada data (por exemplo, quando a data de expiração está se aproximando). Como os cupons são armazenados no telefone do usuário, eles são sempre úteis e não são colocados no local errado. Os cupons podem encorajar os clientes a baixar aplicativos complementares, pois os links da App Store podem ser incorporados ao Pass, aumentando o envolvimento com o cliente.
- **Passagens de placa** – após um processo de check-in online, o cliente receberia sua passagem de tabuleiro por email ou um link. Um aplicativo complementar fornecido pelo provedor de transporte pode incluir o processo de check-in e também permitir que o cliente execute funções adicionais, como escolher sua estação ou refeição. O provedor de transporte pode usar notificações por push para atualizar a passagem se o transporte for atrasado ou cancelado. À medida que o tempo de integração se aproximar, a passagem aparecerá na tela de bloqueio como um lembrete e fornecerá acesso rápido à passagem.

Em seu núcleo, o PassKit fornece uma maneira simples e conveniente de armazenar e exibir códigos de barras em seu dispositivo iOS. Com a integração de tela de bloqueio de tempo e local adicionais, as notificações por push e o aplicativo complementar integram ele oferece uma base para serviços de vendas, de tíquetes e de cobrança muito sofisticados.

## <a name="passkit-ecosystem"></a>Ecossistema PassKit

O PassKit não é apenas uma API no CocoaTouch, em vez disso, ele faz parte de um ecossistema maior de aplicativos, dados e serviços que facilitam o compartilhamento e o gerenciamento seguros de códigos de barras e outros dados. Este diagrama de alto nível mostra as diferentes entidades que podem ser envolvidas na criação e no uso de passagens:

 [![](passkit-images/image2.png "This high level diagram shows the entities involved in creating and using passes")](passkit-images/image2.png#lightbox)

Cada parte do ecossistema tem uma função claramente definida:

- **Carteira** – aplicativo IOS interno da Apple que armazena e exibe passagens. Esse é o único lugar que as passagens são renderizadas para uso no mundo real (caso o código de barras seja exibido, juntamente com todos os dados localizados no passado).
- **Aplicativos complementares** – aplicativos do IOS 6 criados por provedores de passagem para estender a funcionalidade dos passos que eles emitem, como adicionar valor a um cartão de loja, alterar o assento em uma passagem de tabuleiro ou outra função específica de negócios. Os aplicativos complementares não são necessários para que uma passagem seja útil.
- **Seu servidor** – um servidor seguro em que as passagens podem ser geradas e assinadas para distribuição. Seu aplicativo complementar pode se conectar ao seu servidor para gerar novas passagens ou solicitar atualizações para os passos existentes. Opcionalmente, você pode implementar a API do serviço Web que a carteira chamaria para os passos de atualização.
- **Servidores APNS** – o servidor tem a capacidade de notificar a carteira de atualizações a uma passagem em um determinado dispositivo usando o APNS. Envie uma notificação por push para carteira que, em seguida, entrará em contato com o servidor para obter detalhes da alteração. Os aplicativos complementares não precisam implementar o APNS para esse recurso (eles podem escutar o `PKPassLibraryDidChangeNotification`).
- **Aplicativos de canal** – os aplicativos que não manipulam diretamente os passos (como os aplicativos complementares), mas que podem melhorar seu utilitário reconhecendo as passagens e permitindo que elas sejam adicionadas à carteira. Os clientes de email, os navegadores de rede social e outros aplicativos de agregação de dados podem encontrar anexos ou links a serem aprovados.

Todo o ecossistema parece complexo e, portanto, vale a pena observar que alguns componentes são opcionais e muito mais simples são possíveis implementações de PassKit.

## <a name="what-is-a-pass"></a>O que é uma passagem?

Uma passagem é uma coleção de dados que representa um tíquete, cupom ou cartão. Pode ser destinado a um único uso por um indivíduo (e, portanto, conter detalhes como um número de voo e alocação de assentos) ou pode ser um token de uso múltiplo que pode ser compartilhado por qualquer número de usuários (como um cupom de desconto). Uma descrição detalhada está disponível no documento [sobre arquivos de aprovação](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html) da Apple.

### <a name="types"></a>Tipos

Atualmente, os cinco tipos com suporte, que podem ser diferenciados no aplicativo de carteira pelo layout e pela borda superior da passagem:

- **Tíquete de evento** – recorte semicircular pequeno.
- **Aprovação de placa** – entalhes no lado, o ícone específico de transporte pode ser especificado (por exemplo, barramento, trem, avião).
- **Cartão de loja** – arredondado para a parte superior, como um cartão de crédito ou débito.
- **Cupom** – perfurado ao longo da parte superior.
- **Genérico** – igual ao cartão da loja, arredondado para a parte superior.

Os cinco tipos de passagem são mostrados nesta captura de tela (em ordem: cupom, genérico, cartão de loja, aprovação de placa e tíquete de evento):

 [![](passkit-images/image3.png "The five pass types are shown in this screenshot")](passkit-images/image3.png#lightbox)

### <a name="file-structure"></a>Estrutura de arquivos

Um arquivo de passagem é, na verdade, um arquivamento ZIP com uma extensão **. pkpass** , contendo alguns arquivos JSON específicos (obrigatórios), uma variedade de arquivos de imagem (opcional), bem como cadeias de caracteres localizadas (também opcional).

- **Pass. JSON** – obrigatório. Contém todas as informações para a passagem.
- **manifest. JSON** – obrigatório. Contém hashes SHA1 para cada arquivo na passagem, exceto o arquivo de assinatura e este arquivo (manifest. JSON).
- **assinatura** – obrigatório. Criado assinando o arquivo de `manifest.json` com o certificado gerado no portal de provisionamento do iOS.
- **logo. png** – opcional.
- **background. png** – opcional.
- **Icon. png** – opcional.
- **Arquivos de cadeia de caracteres localizáveis** – opcional.

A estrutura de diretório de um arquivo de passagem é mostrada abaixo (este é o conteúdo do arquivo ZIP):

 [![](passkit-images/image4.png "Directory structure of a pass file is shown here")](passkit-images/image4.png#lightbox)

### <a name="passjson"></a>Pass. JSON

JSON é o formato porque as passagens normalmente são criadas em um servidor – isso significa que o código de geração é independente da plataforma no servidor. As três principais informações em cada passagem são:

- **teamIdentifier** – isso vincula todas as passagens que você gera para sua conta da loja de aplicativos. Esse valor é visível no portal de provisionamento do iOS.
- **passTypeIdentifier** – Registre-se no portal de provisionamento para que os grupos passem juntos (se você produzir mais de um tipo). Por exemplo, um café pode criar um tipo de passagem de cartão de loja para permitir que seus clientes ganhem créditos de fidelidade, mas também um tipo de passagem de cupom separado para criar e distribuir cupons de desconto. Essa mesma cafeteria pode até mesmo manter eventos de música ao vivo e emitir passagens de tíquete de evento para eles.
- **SerialNumber** – uma cadeia de caracteres exclusiva nesta `passTypeidentifier`. O valor é opaco para carteira, mas é importante para o acompanhamento de passagens específicas ao se comunicar com o servidor.

Há um grande número de outras chaves JSON em cada passagem, um exemplo do que é mostrado abaixo:

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

### <a name="barcodes"></a>Barras

Há suporte apenas para formatos 2D: PDF417, Aztec, QR. A Apple alega que os códigos de barras 1D não são adaptados para verificação em uma tela de telefone backlit.

O texto alternativo exibido abaixo do código de barras é opcional – alguns comerciantes desejam ser capazes de ler/digitar manualmente.

A codificação ISO-8859-1 é a mais comum, verifique qual codificação é usada pelos sistemas de verificação que lerá seus passos.

### <a name="relevancy-lock-screen"></a>Relevância (tela de bloqueio)

Há dois tipos de dados que podem fazer com que uma passagem seja exibida na tela de bloqueio:

 **Local**

Até 10 locais podem ser especificados em uma passagem, por exemplo, lojas que um cliente visita com frequência ou o local de um cinema ou aeroporto. Um cliente pode definir esses locais por meio de um aplicativo complementar ou o provedor poderia defini-los a partir de dados de uso (se coletados com a permissão do cliente).

Quando a passagem é exibida na tela de bloqueio, uma cerca é calculada para que, quando o usuário sair da área, a passagem fique oculta da tela de bloqueio. O raio está vinculado ao estilo Pass para evitar abusos.

 **Data e hora**

Somente uma data/hora pode ser especificada em uma passagem. A data e a hora são úteis para disparar lembretes de bloqueio de tela para passagens de placa e Tíquetes de evento.

Pode ser atualizado por Push ou por meio da API PassKit, de forma que a data/hora possa ser atualizada no caso de um tíquete de uso múltiplo (como um bilhete de temporada para um teatro ou uma vida complexa).

### <a name="localization"></a>Localização

A tradução de uma passagem em vários idiomas é semelhante à localização de um aplicativo iOS – crie diretórios específicos de linguagem com a extensão `.lproj` e coloque os elementos localizados dentro. As traduções de texto devem ser inseridas em um arquivo `pass.strings`, enquanto as imagens localizadas devem ter o mesmo nome que a imagem que substituem na raiz de passagem.

## <a name="security"></a>Segurança

As passagens são assinadas com um certificado privado que você gera no portal de provisionamento do iOS. As etapas para assinar a passagem são:

1. Calcule um hash SHA1 para cada arquivo no diretório Pass (não inclua o `manifest.json` ou `signature` arquivo, nenhum dos quais deve existir nesse estágio de qualquer maneira).
1. Escreva `manifest.json` como uma lista de chaves/valores JSON de cada nome de arquivo com seu hash.
1. Use o certificado para assinar o arquivo de `manifest.json` e gravar o resultado em um arquivo chamado `signature`.
1. COMPACTe tudo e dê ao arquivo resultante uma extensão de arquivo `.pkpass`.

Como sua chave privada é necessária para assinar a passagem, esse processo só deve ser feito em um servidor seguro que você controla. Não distribua suas chaves para tentar e gerar passagens em um aplicativo.

## <a name="configuration-and-setup"></a>Configuração e configuração

Esta seção contém instruções para ajudar a configurar seus detalhes de provisionamento e criar sua primeira passagem.

### <a name="provisioning-passkit"></a>Provisionando PassKit

Para que uma passagem Insira a loja de aplicativos, ela deve ser vinculada a uma conta de desenvolvedor. Isso requer duas etapas:

1. A passagem deve ser registrada usando um identificador exclusivo, chamado de ID de tipo de passagem.
1. Um certificado válido deve ser gerado para assinar a passagem com a assinatura digital do desenvolvedor.

Para criar uma ID de tipo de passagem, faça o seguinte.

#### <a name="create-a-pass-type-id"></a>Criar uma ID de tipo de passagem

A primeira etapa é configurar uma ID de tipo de passagem para cada _tipo_ diferente de pass a ser suportado. A ID de passagem (ou o identificador de tipo de passagem) cria um identificador exclusivo para a passagem. Usaremos essa ID para vincular a passagem à sua conta de desenvolvedor usando um certificado.

1. Na [seção certificados, identificadores e perfis do portal de provisionamento do IOS](https://developer.apple.com/account/overview.action), navegue até **identificadores** e selecione as IDs de **tipo de passagem** . Em seguida, selecione o botão **+** para criar um novo tipo de passagem:[![](passkit-images/passid.png "Criar um novo tipo de passagem")](passkit-images/passid.png#lightbox)

2. Forneça uma **Descrição** (nome) e um **identificador** (cadeia de caracteres exclusiva) para a passagem. Observe que todas as IDs de tipo de passagem devem começar com a cadeia de caracteres `pass.` neste exemplo, usamos `pass.com.xamarin.coupon.banana`:[![](passkit-images/register.png "Forneça uma descrição e um identificador")](passkit-images/register.png#lightbox)

3. Confirme a ID de passagem pressionando o botão **registrar** .

#### <a name="generate-a-certificate"></a>Gerar um certificado

Para criar um novo certificado para essa ID de tipo de passagem, faça o seguinte:

1. Selecione a ID de passagem criada recentemente na lista e clique em **Editar** :[![](passkit-images/pass-done.png "Selecione a nova ID de passagem na lista")](passkit-images/pass-done.png#lightbox)

    Em seguida, selecione **criar certificado...** :

    [![](passkit-images/cert-dist.png "Select Create Certificate")](passkit-images/cert-dist.png#lightbox)

2. Siga as etapas para criar uma solicitação de assinatura de certificado (CSR).
  
3. Pressione o botão **continuar** no portal do desenvolvedor e carregue o CSR para gerar seu certificado.

4. Baixe o certificado e clique duas vezes nele para instalá-lo em seu conjunto de chaves.

Agora que criamos um certificado para essa ID de tipo Pass, a próxima seção descreve como criar uma passagem manualmente.

Para obter mais informações sobre o provisionamento de carteira, consulte o guia de [trabalho com recursos](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md) .

### <a name="create-a-pass-manually"></a>Criar uma passagem manualmente

Agora que criamos o tipo Pass, podemos criar manualmente uma passagem para o teste no simulador ou em um dispositivo. As etapas para criar uma passagem são:

- Crie um diretório para conter os arquivos aprovados.
- Crie um arquivo Pass. JSON que contenha todos os dados necessários.
- Incluir imagens na pasta (se necessário).
- Calcule hashes SHA1 para cada arquivo na pasta e grave em Manifest. JSON.
- Assine manifest. JSON com o arquivo. p12 do certificado baixado.
- COMPACTe o conteúdo do diretório e renomeie com a extensão. pkpass.

Há alguns arquivos de origem no [código de exemplo](https://docs.microsoft.com/samples/xamarin/ios-samples/passkit) para este artigo que podem ser usados para gerar uma passagem. Use os arquivos no diretório `CouponBanana.raw` do diretório CreateAPassManually Os seguintes arquivos estão presentes:

 [![](passkit-images/image18.png "These files are present")](passkit-images/image18.png#lightbox)

Abra o Pass. JSON e edite o JSON. Você deve, pelo menos, atualizar o `passTypeIdentifier` e `teamIdentifer` para corresponder à sua conta de desenvolvedor da Apple.

```json
"passTypeIdentifier" : "pass.com.xamarin.coupon.banana",
"teamIdentifier" : "?????????",
```

Em seguida, você deve calcular os hashes para cada arquivo e criar o arquivo de `manifest.json`. Ele terá uma aparência semelhante a esta quando você terminar:

```json
{
  "icon@2x.png" : "30806547dcc6ee084a90210e2dc042d5d7d92a41",
  "icon.png" : "87e9ffb203beb2cce5de76113f8e9503aeab6ecc",
  "pass.json" : "c83cd1441c17ecc6c5911bae530d54500f57d9eb",
  "logo.png" : "b3cd8a488b0674ef4e7d941d5edbb4b5b0e6823f",
  "logo@2x.png" : "3ccd214765507f9eab7244acc54cc4ac733baf87"
}
```

Em seguida, uma assinatura deve ser gerada para esse arquivo usando o certificado (arquivo. p12) que foi gerado para essa ID de tipo de passagem.

#### <a name="signing-on-a-mac"></a>Assinando um Mac

Baixe os **materiais de suporte da semente de bolso** no site de [downloads da Apple](https://developer.apple.com/downloads/index.action?name=Passbook) . Use a ferramenta `signpass` para transformar sua pasta em uma passagem (isso também calculará os hashes SHA1 e expedirá a saída para um arquivo. pkpass).

#### <a name="testing"></a>Testes

Se você examinar a saída dessas ferramentas (definindo o nome de arquivo como. zip e, em seguida, abri-la), você verá os seguintes arquivos (Observe a adição dos arquivos `manifest.json` e `signature`):

 [![](passkit-images/image19.png "Examining the output of these tools")](passkit-images/image19.png#lightbox)

Depois de assinar, compactar e renomear o arquivo (por exemplo, para `BananaCoupon.pkpass`) você pode arrastá-lo para o simulador para teste ou enviá-lo por email para você mesmo para recuperar em um dispositivo real. Você deve ver uma tela para **Adicionar** a passagem, da seguinte maneira:

 [![](passkit-images/image20.png "Add the pass screen")](passkit-images/image20.png#lightbox)

Normalmente, esse processo seria automatizado em um servidor, no entanto, a criação manual de passagem pode ser uma opção para pequenas empresas que estão criando apenas cupons que não exigem o suporte de um servidor back-end.

## <a name="wallet"></a>Carteira

A carteira é a parte central do ecossistema PassKit. Esta captura de tela mostra a carteira vazia e como a lista de passagem e as passagens individuais parecem:

 [![](passkit-images/image21.png "This screenshot shows the empty Wallet, and how the pass list and individual passes look")](passkit-images/image21.png#lightbox)

Os recursos de carteira incluem:

- É o único lugar que as passagens são renderizadas com seu código de barras para verificação.
- O usuário pode alterar as configurações de atualizações. Se habilitada, as notificações por push podem disparar atualizações para os dados na passagem.
- O usuário pode habilitar ou desabilitar a integração da tela de bloqueio. Se habilitada, isso permite que a passagem apareça automaticamente na tela de bloqueio, com base no tempo e nos dados de localização relevantes inseridos na passagem.
- O lado inverso da passagem dá suporte a pull para atualização, se uma Web-Server-URL for fornecida no JSON Pass.
- Os aplicativos complementares poderão ser abertos (ou baixados) se a ID do aplicativo for fornecida no JSON de passagem.
- As passagens podem ser excluídas (com uma animação de destruição graciosos).

## <a name="adding-passes-into-wallet"></a>Adicionando passagens em carteira

As passagens podem ser adicionadas à carteira das seguintes maneiras:

- **Aplicativos de canal** – esses não manipulam as passagens diretamente, eles simplesmente carregam arquivos de passagem e apresentam ao usuário a opção de adicioná-los à carteira. 

- **Aplicativos complementares** – eles são escritos por provedores para distribuir passagens e oferecem funcionalidade adicional para procurá-los ou editá-los. Os aplicativos Xamarin. iOS têm acesso completo à API do PassKit para criar e manipular passagens. As passagens podem ser adicionadas à carteira usando o `PKAddPassesViewController`. Esse processo é descrito mais detalhadamente na seção **aplicativos complementares** deste documento.

### <a name="conduit-applications"></a>Aplicativos de canal

Os aplicativos de canal são aplicativos intermediários que podem receber passagens em nome de um usuário e devem ser programados para reconhecer seu tipo de conteúdo e fornecer funcionalidade para adicionar à carteira. Exemplos de aplicativos de canal incluem:

- **Email** – reconhece o anexo como uma passagem.
- **Safari** – reconhece o tipo de conteúdo Pass quando um link de URL de passagem é clicado.
- **Outros aplicativos personalizados** – qualquer aplicativo que receba anexos ou abra links (clientes de mídia social, leitores de email, etc.).

Esta captura de tela mostra como o **email** no Ios 6 reconhece um anexo Pass e as ofertas (quando tocadas) para **adicioná** -lo à carteira.

 [![](passkit-images/image22.png "This screenshot shows how Mail in iOS 6 recognizes a pass attachment")](passkit-images/image22.png#lightbox)

 [![](passkit-images/image23.png "This screenshot shows how Mail offers to add a pass attachment to Wallet")](passkit-images/image23.png#lightbox)

Se você estiver criando um aplicativo que pode ser um canal para passagens, eles podem ser reconhecidos pelo:

- **Extensão de arquivo** -. pkpass
- **Tipo de MIME** – application/vnd. Apple. pkpass
- **UTI** – com. Apple. pkpass

A operação básica de um aplicativo de Conduit é recuperar o arquivo de passagem e chamar o `PKAddPassesViewController` do PassKit para dar ao usuário a opção de adicionar a passagem à sua carteira. A implementação desse controlador de exibição será abordada na próxima seção sobre **aplicativos complementares**.

Os aplicativos de canal não precisam ser provisionados para uma ID de tipo de passagem específica da mesma maneira que os aplicativos complementares.

## <a name="companion-applications"></a>Aplicativos complementares

Um aplicativo complementar fornece funcionalidade adicional para trabalhar com passagens, incluindo a criação de uma passagem, a atualização de informações associadas a uma aprovação e o gerenciamento de passagens associadas ao aplicativo.

Os aplicativos complementares não devem tentar duplicar os recursos de carteira. Eles não se destinam a exibir passagens para verificação.

Este restante desta seção descreve como criar um aplicativo complementar básico que interage com o PassKit.

### <a name="provisioning"></a>Provisionamento

Como a carteira é uma tecnologia de armazenamento, o aplicativo precisa ser provisionado separadamente e não pode usar o perfil de provisionamento de equipe ou a ID do aplicativo curinga. Consulte o guia [trabalhando com recursos](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md) para criar uma ID de aplicativo exclusiva e um perfil de provisionamento para o aplicativo de carteira.

### <a name="entitlements"></a>Direitos

O arquivo **. plist de direitos** deve ser incluído em todos os projetos recentes do Xamarin. Ios. Para adicionar um novo arquivo. plist de direitos, siga as etapas no guia [trabalhando com direitos](~/ios/deploy-test/provisioning/entitlements.md) .

Para definir direitos, faça o seguinte:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Clique duas vezes no arquivo **retitles. plist** na painel de soluções para abrir o editor de direitos. plist:

![](passkit-images/image31.png "Entitlements.plst editor")

Na seção carteira, selecione a opção **habilitar carteira**

![](passkit-images/image32.png "Enable wallet entitlement")

A opção padrão é para seu aplicativo permitir todos os tipos de passagem. No entanto, é possível restringir seu aplicativo e permitir apenas um subconjunto de tipos de passagem de equipe. Para habilitar isso, selecione o **subconjunto de tipos de aprovação de equipe** e insira o identificador de tipo de passagem do subconjunto que você deseja permitir.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Clique duas vezes no arquivo **pretitles. plist** para abrir o arquivo de origem XML.

Para adicionar o direito de carteira, defina a **Propriedade** como `Passbook Identifiers` no menu suspenso, que irá definir automaticamente o **tipo** `Array`. Em seguida, defina o **valor** da cadeia de caracteres para `$(TeamIdentifierPrefix)*`:

![](passkit-images/image33.png "Enable wallet entitlement")

Isso habilitará o aplicativo a permitir todos os tipos de passes. Para restringir seu aplicativo e permitir apenas um subconjunto de tipos de passagem de equipe, defina o valor da cadeia de caracteres para:

`$(TeamIdentifierPrefix)pass.$(CFBundleIdentifier)`

Onde `pass.$(CFBundleIdentifier)` é a ID de aprovação que foi criada [acima](~/ios/platform/passkit.md)

-----

### <a name="debugging"></a>Depuração

Se você tiver problemas para implantar seu aplicativo, verifique se está usando o perfil de **provisionamento** correto e se o `Entitlements.plist` está selecionado como o arquivo de **direitos personalizado** nas opções de assinatura do **pacote do iPhone** .

Se você tiver esse erro ao implantar:

```
Installation failed: Your code signing/provisioning profiles are not correctly configured (error: 0xe8008016)
```

em seguida, a matriz de direitos de `pass-type-identifiers` está incorreta (ou não corresponde ao **perfil de provisionamento**). Verifique se as IDs de tipo de passagem e sua ID de equipe estão corretas.

## <a name="classes"></a>Classes

As seguintes classes PassKit estão disponíveis para os aplicativos acessarem passagens:

- **PKPass** – uma instância de uma passagem.
- **PKPassLibrary** – fornece a API para acessar os passos no dispositivo.
- **PKAddPassesViewController** – usado para exibir uma passagem para que o usuário salve em sua carteira.
- **PKAddPassesViewControllerDelegate** – desenvolvedores do Xamarin. Ios

## <a name="example"></a>Exemplo

Consulte o projeto PassLibrary no código de [exemplo](https://docs.microsoft.com/samples/xamarin/ios-samples/passkit) deste artigo. Ele demonstra as seguintes funções comuns que seriam necessárias em um aplicativo do bolso Companion:

### <a name="check-that-wallet-is-available"></a>Verifique se a carteira está disponível

A carteira não está disponível no iPad, portanto, os aplicativos devem verificar antes de tentar acessar os recursos do PassKit.

```csharp
if (PKPassLibrary.IsAvailable) {
    // create an instance and do stuff...
}
```

### <a name="creating-a-pass-library-instance"></a>Criando uma instância de biblioteca de passagem

A biblioteca PassKit não é um singleton, os aplicativos devem criar e armazenar e instância para acessar a API do PassKit.

```csharp
if (PKPassLibrary.IsAvailable) {
    library = new PKPassLibrary ();
    // do stuff...
}
```

### <a name="get-a-list-of-passes"></a>Obter uma lista de passagens

Os aplicativos podem solicitar uma lista de passagens da biblioteca. Essa lista é filtrada automaticamente pelo PassKit, para que você possa ver apenas as passagens que foram criadas com sua ID de equipe e que estão listadas em seus direitos.

```csharp
var passes = library.GetPasses ();  // returns PKPass[]
```

Observe que o simulador não filtra a lista de passagens retornadas, portanto, esse método sempre deve ser testado em dispositivos reais. Essa lista pode ser exibida em um UITableView. O [aplicativo de exemplo](https://docs.microsoft.com/samples/xamarin/ios-samples/passkit) tem esta aparência após dois cupons terem sido adicionados:

 [![](passkit-images/image29.png "The sample app look like this after two coupons have been added")](passkit-images/image29.png#lightbox)

### <a name="displaying-passes"></a>Exibindo passagens

Um conjunto limitado de informações está disponível para renderização de passagens nos aplicativos complementares.

Escolha nesse conjunto de propriedades padrão para exibir listas de passagens, como faz o exemplo de código.

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

Essa cadeia de caracteres é mostrada como um alerta no [exemplo](https://docs.microsoft.com/samples/xamarin/ios-samples/passkit):

 [![](passkit-images/image30.png "The Coupon Selected alert in the sample")](passkit-images/image30.png#lightbox)

Você também pode usar o método `LocalizedValueForFieldKey()` para recuperar dados de campos nos passos que você criou (já que você saberá quais campos devem estar presentes). O código de exemplo não mostra isso.

### <a name="loading-a-pass-from-a-file"></a>Carregando uma passagem de um arquivo

Como uma passagem só pode ser adicionada à carteira com a permissão do usuário, um controlador de exibição deve ser apresentado para permitir que eles decidam. Esse código é usado no botão **Adicionar** no exemplo, para carregar uma passagem pré-criada inserida no aplicativo (você deve substituí-la por uma que você tenha assinado):

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

A passagem é apresentada com as opções **Adicionar** e **Cancelar** :

 [![](passkit-images/image20.png "The pass presented with Add and Cancel options")](passkit-images/image20.png#lightbox)

### <a name="replace-an-existing-pass"></a>Substituir uma passagem existente

A substituição de uma passagem existente não exige a permissão do usuário; no entanto, ela falhará se a passagem ainda não existir.

```csharp
if (library.Contains (newPass)) {
     library.Replace (newPass);
}
```

### <a name="editing-a-pass"></a>Editando uma passagem

PKPass não é mutável, portanto, você não pode atualizar os objetos Pass em seu código. Para alterar os dados em uma passagem, um aplicativo deve ter acesso a um servidor Web que pode manter um registro de passagens e gerar um novo arquivo de passagem com valores atualizados que o aplicativo pode baixar.

A criação do arquivo Pass deve ser feita em um servidor porque as passagens devem ser assinadas com um certificado que deve ser mantido privado e seguro.

Depois que um arquivo de passagem atualizado tiver sido gerado, use o método `Replace` para substituir os dados antigos no dispositivo.

### <a name="display-a-pass-for-scanning"></a>Exibir uma passagem para verificação

Conforme observado anteriormente, somente a carteira pode exibir uma passagem para verificação. Uma passagem pode ser exibida usando o método `OpenUrl`, conforme mostrado:

 `UIApplication.SharedApplication.OpenUrl (p.PassUrl);`

### <a name="receiving-notifications-of-changes"></a>Recebendo notificações de alterações

Os aplicativos podem escutar alterações feitas na biblioteca Pass usando o `PKPassLibraryDidChangeNotification`. As alterações podem ser causadas por notificações que disparam atualizações em segundo plano, portanto, é uma prática recomendada ouvi-las em seu aplicativo.

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

É importante passar uma instância de biblioteca ao registrar-se para a notificação porque PKPassLibrary não é um singleton.

## <a name="server-processing"></a>Processamento de servidor

Uma discussão detalhada sobre a criação de um aplicativo de servidor para dar suporte ao PassKit está além do escopo deste artigo introdutório.

Consulte [dotnet-Passbook](https://github.com/tomasmcguinness/dotnet-passbook) código do C# lado do servidor de código-fonte aberto.

## <a name="push-notifications"></a>Notificações por Push

Uma discussão detalhada sobre o uso de notificações por push para atualizar passagens está além do escopo deste artigo introdutório.

Seria necessário implementar a API do tipo REST definida pela Apple para responder às solicitações da Web da carteira quando forem necessárias atualizações.

Consulte [a atualização de um](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/PassKit_PG/Updating.html#//apple_ref/doc/uid/TP40012195-CH5-SW1) guia de aprovação da Apple para obter mais informações.

## <a name="summary"></a>Resumo

Este artigo introduziu o PassKit, descreveu alguns dos motivos pelos quais ele é útil e descreveu as diferentes partes que devem ser implementadas para uma solução PassKit completa. Ele descreveu as etapas necessárias para configurar sua conta de desenvolvedor da Apple para criar passagens, o processo para fazer uma passagem manualmente e também como acessar as APIs PassKit de um aplicativo Xamarin. iOS.

## <a name="related-links"></a>Links relacionados

- [Carteira para desenvolvedores](https://developer.apple.com/wallet/)
- [Exemplo de PassKit](https://docs.microsoft.com/samples/xamarin/ios-samples/passkit)
- [Guia do desenvolvedor de carteira](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/PassKit_PG/index.html#//apple_ref/doc/uid/TP40012195-CH1-SW1)
- [Estruturas – Apple Pay e carteira (vídeos WWDC)](https://developer.apple.com/videos/frameworks/apple-pay-and-wallet)
- [Referência da estrutura do PassKit](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Framework/_index.html)
- [Referência de serviço Web Passbook](https://developer.apple.com/library/prerelease/ios/#documentation/PassKit/Reference/PassKit_WebService/WebService.html)
- [Sobre a passagem de arquivos](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html)
- [dotnet-Passbook](https://github.com/tomasmcguinness/dotnet-passbook), uma biblioteca de código aberto para gerar pacotes de carteira de Ios
- [Introdução ao iOS 6](~/ios/platform/introduction-to-ios6/index.md)
