---
title: Noções básicas sobre os conceitos de SiriKit
description: Este documento descreve os principais conceitos necessários para trabalhar com SiriKit em um aplicativo Xamarin. iOS. Por exemplo, ele discute tentativas e extensões de interface do usuário, permissões de SiriKit, criação de uma ótima experiência e muito mais.
ms.prod: xamarin
ms.assetid: 99EC5C1E-484F-4371-8555-58C9F60DE37F
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/02/2017
ms.openlocfilehash: 74c45a4e8880e1b9c98d11dd4874a4ecf569f0c0
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433793"
---
# <a name="understanding-sirikit-concepts"></a>Noções básicas sobre os conceitos de SiriKit

_Este artigo aborda os principais conceitos que serão necessários para trabalhar com SiriKit em um aplicativo Xamarin. iOS._

Novo no iOS 10, o SiriKit permite que um aplicativo Xamarin. iOS forneça serviços que são acessíveis para o usuário usando o Siri e o aplicativo Maps em um dispositivo iOS. Essa funcionalidade é fornecida em uma ou mais extensões de aplicativo usando as novas estruturas de **interface do usuário** de **tentativas** e intenções.

O SiriKit permite que um aplicativo iOS forneça serviços que podem ser acessados pelo usuário usando o Siri e o aplicativo Maps em um dispositivo iOS usando extensões de aplicativo e **as novas estruturas** de **interface do usuário** de intenções e intenções.

O Siri trabalha com o conceito de **domínios**, grupos de ações conhecidas para tarefas relacionadas. Cada interação que o aplicativo tem com siri deve se enquadrar em um de seus domínios de serviço conhecidos da seguinte maneira:

- Chamada de áudio ou vídeo.
- Reserva de um Ride.
- Gerenciando exercícios.
- Mensagens.
- Pesquisando fotos.
- Enviando ou recebendo pagamentos.

Quando o usuário faz uma solicitação de Siri envolvendo um dos serviços da extensão do aplicativo, o SiriKit envia a extensão um objeto de **intenção** que descreve a solicitação do usuário junto com quaisquer dados de suporte. Em seguida, a extensão do aplicativo gera o objeto de **resposta** apropriado para a **intenção**determinada, detalhando como a extensão pode lidar com a solicitação.

## <a name="the-intents-and-intents-ui-extensions"></a>As extensões de interface do usuário de tentativas e intenções

Tanto o Siri quanto o aplicativo Maps interagem com os serviços do aplicativo por meio de dois tipos diferentes de extensões de aplicativo:

- **Extensão de tentativas** – fornece Siri e mapeia com o conteúdo do aplicativo e executa as tarefas necessárias para atender a quaisquer tentativas com suporte.
- **Extensão de interface do usuário de tentativas** – fornece uma interface do usuário personalizada que será exibida para o conteúdo do aplicativo dentro de siri ou Maps.

O aplicativo deve fornecer uma extensão de tentativas para dar suporte a SiriKit e é responsável por fornecer informações que o Siri e os mapas podem apresentar ao usuário e para lidar com as intenções.

A criação de uma extensão de interface do usuário de tentativas é opcional, pois Siri geralmente lida com toda a interação do usuário e tem uma interface de usuário interna padrão para apresentar informações em cada um dos domínios com suporte. Ao fornecer uma extensão de interface do usuário de intenções, o aplicativo pode usar a estrutura **da interface do** usuário da intenção para apresentar uma interface de usuário avançada e personalizada, apresentando a identidade visual do aplicativo e informações adicionais.

## <a name="siri-and-the-maps-app-role"></a>Siri e a função de aplicativo Maps

As solicitações faladas do usuário são o idioma processado e analisado semanticamente pelo Siri, que transforma essas solicitações em tentativas acionáveis que as extensões de intenção podem manipular.

O Maps usa as extensões de intenção do aplicativo para exibir informações na interface do mapa em resposta às ações do usuário. Como solicitar restaurantes próximos ou obter as revisões do restaurante do aplicativo.

O Siri e o Maps gerenciam todas as interações do usuário e exibem os resultados usando a interface do sistema padrão. A função extensões de aplicativo é principalmente para fornecer os dados que são exibidos. Opcionalmente, o aplicativo pode fornecer uma extensão de interface do usuário de tentativas e apresentar uma interface do usuário personalizada para aprimorar a interface do sistema padrão.

## <a name="interacting-with-siri-via-sirikit"></a>Interagindo com siri via SiriKit

Esta seção apresentará uma visão geral de como o SiriKit permite que o usuário interaja com o aplicativo usando o Siri. Para fins deste exemplo, usaremos o aplicativo MonkeyChat falso:

[![O ícone de MonkeyChat](understanding-sirikit-images/monkeychat01.png)](understanding-sirikit-images/monkeychat01.png#lightbox)

MonkeyChat mantém seu próprio livro de contatos dos amigos do usuário, cada um associado a um nome de tela (como bobo, por exemplo) e permite que o usuário envie chats de texto para cada amigo pelo nome da tela.

Há muitas maneiras pelas quais o usuário pode iniciar uma interação com o aplicativo, já que pessoas diferentes podem fazer a mesma solicitação em vários formatos diferentes.

Por exemplo, se o usuário quisesse enviar uma mensagem para seu amigo bobo, ela poderá ter a seguinte conversa com siri:

_Usuário: Ei Siri, envie uma mensagem MonkeyChat._<br />
_Siri: para quem?_<br />
_Usuário: bobo._<br />
_Siri: o que você deseja dizer para bobo?_<br />
_Usuário: envie mais bananas._<br />

Outra pessoa pode fazer a mesma solicitação com uma conversa diferente:

_Usuário: envie uma mensagem para bobo em MonkeyChat._<br />
_Siri: o que você deseja dizer para bobo?_<br />
_Usuário: envie mais bananas._<br />

E outro usuário pode fazer uma solicitação ainda mais curta:

_Usuário: MonkeyChat bobo envie mais bananas._<br />
_Siri: Ok, enviando mensagem, envie mais bananas para bobo no Monkeychat._<br />

Ou até mesmo fazer a mesma solicitação em um idioma diferente:

_User: MonkeyChat bobo s'il vous plaît envoyer Plus de bananes._<br />
_Siri: Oui, envoi Message s'il vous plaît envoyer Plus de bananes to bobo sur Monkeychat._<br />

Ainda assim, outro usuário pode ser muito detalhado em sua conversa:

_Usuário: Ei Siri, você pode fazer um favor e iniciar o aplicativo MonkeyChat para enviar um texto com a mensagem. envie mais bananas._<br />
_Siri: para quem?_<br />
_Usuário: minha melhor PAL de bobo._<br />

Além disso, há muitas maneiras pelas quais Siri pode responder a uma solicitação, algumas com base em como a solicitação foi feita:

- **Ao manter o botão página inicial, o** Siri fornecerá mais respostas visuais com comentários verbal limitados.
- **Por "Ei Siri"** -Siri será mais verbal e fornecerá menos respostas visuais.

O Siri também é ajustado para atender às necessidades de acessibilidade do usuário e irá interagir e responder com base nessas necessidades.

Não importa como uma solicitação é feita ou como o Siri responde à solicitação, o Siri manipula a conversa com o usuário e o aplicativo (por meio de suas extensões) fornece a funcionalidade.

Quando o usuário faz uma solicitação verbal de Siri, estas são as etapas que o Siri irá seguir:

[![As etapas que o Siri irá seguir](understanding-sirikit-images/monkeychat02.png)](understanding-sirikit-images/monkeychat02.png#lightbox)

1. Primeiro, o Siri pega o áudio da **fala** do usuário e o converte em texto.
2. Em seguida, o texto é convertido em uma **intenção**, uma representação estruturada da solicitação do usuário.
3. Com base na intenção, o Siri tomará **medidas** para executar a solicitação do usuário.
4. Por fim, o Siri apresentará as **respostas** (Visual e textual) ao usuário com base na ação executada.

Há três maneiras principais pelas quais o aplicativo pode participar da conversa do usuário com o Siri:

[![As três maneiras principais que o aplicativo pode participar da conversa de usuários com o Siri](understanding-sirikit-images/monkeychat03.png)](understanding-sirikit-images/monkeychat03.png#lightbox)

1. **Vocabulário** -é assim que o aplicativo diz Siri as palavras que ele precisa saber para interagir com ele.
2. **Lógica do aplicativo** -essas são as ações e respostas que o aplicativo executará com base em determinadas tentativas.
3. **Interface do usuário** -essa é a interface de usuário personalizada opcional na qual o aplicativo pode fornecer suas respostas.

### <a name="example"></a>Exemplo

Dadas as informações acima, examine como a conversa a seguir interagiria com o aplicativo MonkeyChat:

_Usuário: Ei Siri, envie uma mensagem para bobo em MonkeyChat._<br />
_Siri: o que você deseja dizer para bobo?_<br />
_Usuário: envie mais bananas._<br />

A primeira função que o aplicativo assume na conversa é ajudar a Siri a entender a fala do usuário:

[![Ajudando a Siri a entender a fala dos usuários](understanding-sirikit-images/monkeychat04.png)](understanding-sirikit-images/monkeychat04.png#lightbox)

Siri não tem o nome "bobo" em seu banco de dados, mas o aplicativo faz e compartilhou essas informações com siri por meio de seu vocabulário. O aplicativo também ajuda a Siri a reconhecer que o bobo é um destinatário, já que ele o especificou para Siri como um *contato*.

Siri sabe que mais é necessário enviar uma mensagem do que apenas um destinatário, portanto, ele verificará rapidamente com a extensão do aplicativo para ver se uma mensagem requer conteúdo. Como o MonkeyChat faz, o Siri responderá ao usuário com a pergunta: *"o que você deseja dizer para bobo?"*

No exemplo acima, o usuário respondeu, *"envie mais bananas"*, que Siri será agrupado em uma **intenção**estruturada:

[![Siri irá agrupar a resposta do usuário em uma intenção estruturada](understanding-sirikit-images/monkeychat05.png)](understanding-sirikit-images/monkeychat05.png#lightbox)

A intenção estruturada conterá as seguintes informações:

- **Domínio:** Mensagens
- **Intenção:** SendMessage
- **Destinatário:** Bobo
- **Conteúdo:** Envie mais bananas

Cada domínio tem como conjunto de *ações* conhecidas que podem ser executadas dentro deles e com base no domínio e na ação, zero a muitos parâmetros pode ser incluído na intenção enviada ao aplicativo.

A intenção é então enviada para a extensão do aplicativo para processamento. Como resultado do processamento da intenção, o aplicativo irá gerar um **IntentResponse** que será agrupado com a intenção e incluirá parâmetros que descrevem o que o aplicativo fez com a intenção.

Cada IntentResponse também incluirá um **código de resposta** que informa Siri se o aplicativo foi capaz de concluir a solicitação ou não. Alguns domínios têm códigos de resposta de erro muito específicos que também podem ser enviados.

Por fim, o IntentResponse incluirá um `NSUserActivity` (como aqueles usados para dar suporte à entrega). O `NSUserActivity` será usado para iniciar o aplicativo se a resposta exigir que ele deixe o ambiente Siri e insira o aplicativo para concluí-lo.

O Siri criará automaticamente um apropriado `NSUserActivity` para iniciar o aplicativo e retirar onde o usuário parou no ambiente do Siri. No entanto, o aplicativo pode fornecer seu próprio `NSUserActivity` com informações personalizadas, se necessário.

Depois que o aplicativo tiver processado a intenção e retornado uma resposta para Siri, ele apresentará os resultados ao usuário (de forma verbal e Visual):

[![Os resultados apresentados ao usuário tanto de forma verbal quanto Visual](understanding-sirikit-images/monkeychat06.png)](understanding-sirikit-images/monkeychat06.png#lightbox)

O Siri tem várias interfaces de usuário de resposta internas para cada um dos domínios disponíveis para o aplicativo. No entanto, como MonkeyChat forneceu uma extensão de interface do usuário de intenção opcional, ela é usada para apresentar os resultados da conversa para o usuário no exemplo acima.

## <a name="the-intent-lifecycle"></a>O ciclo de vida da intenção

Há três tarefas principais que a extensão do aplicativo precisará executar ao lidar com tentativas:

[![O ciclo de vida da intenção](understanding-sirikit-images/monkeychat07.png)](understanding-sirikit-images/monkeychat07.png#lightbox)

1. O aplicativo deve **resolver** todos os parâmetros em um evento. Como resultado, o aplicativo chamará resolver várias vezes (uma vez por cada parâmetro) e, às vezes, várias vezes no mesmo parâmetro até que o aplicativo e o usuário concordem com o que está sendo solicitado.
2. O aplicativo deve **confirmar** que ele pode lidar com a intenção solicitada e informar Siri sobre o resultado esperado.
3. Por fim, o aplicativo deve **lidar com** a intenção e executar as etapas para obter o resultado solicitado.

### <a name="the-resolve-stage"></a>O estágio resolver

O estágio resolver ajuda a Siri a entender os valores que o usuário forneceu e garante que o que o usuário realmente significava é o que acontecerá quando a intenção for processada pelo aplicativo.

Esse estágio também fornece uma oportunidade para que o aplicativo influencie o comportamento do Siri durante a conversa com o usuário. Para fazer isso, o aplicativo fornecerá uma **resposta de resolução**. Há uma série de respostas predefinidas para os diferentes tipos de dados que o Siri compreende.

A resposta de resolução mais comum do aplicativo será **bem-sucedida**, o que significa que o aplicativo correspondeu à parte específica dos dados de um parâmetro (como nome da tela do usuário) a uma informação sobre a qual ele sabe.

Pode haver ocasiões em que o aplicativo precisa confirmar que uma determinada solicitação corresponde à informação correta que ele sabe. Nesses casos, ele enviará uma resposta **ConfirmationRequired** para fazer uma pergunta Sim ou não para o usuário, como *"Enviar mensagem para bobo o ótimo?"*

Pode haver outros casos em que o aplicativo exigirá que o usuário escolha uma lista curta de opções. Nesse caso, o aplicativo fornecerá uma resposta de **desambiguidade** com uma lista de duas a dez opções para que o usuário escolha, como:

```csharp
Who do you want to message?

* Bobo the Great
* Bobo Jr.
* Little Bobo
```

O Siri tratará o usuário fazendo a seleção, de forma verbal ou interagindo com a interface do usuário do Siri, e o resultado será enviado de volta para o aplicativo.

Em outros casos, pode não haver informações suficientes para o aplicativo resolver o parâmetro ou pode haver muitas correspondências para resolver usando a desambiguidade (como 80 usuários com bobo em seu nome). Nesse caso, o aplicativo enviará uma resposta **NeedsMoreDetails** e Siri solicitará que o usuário seja mais específico.

Se o usuário não fornecer um valor necessário para que ele processe a intenção, ele poderá enviar uma resposta de **necessário** para que o Siri solicite ao usuário o valor.

Se o aplicativo não der suporte a um valor fornecido pelo usuário para um parâmetro específico, ele poderá enviar a resposta **UnsupportedWithReason** para fornecer um motivo pelo qual o valor não tinha suporte. Siri, em seguida, solicitará ao usuário um valor completamente novo e, considerando-os, o motivo é necessário.

Por fim, use a resposta não **necessária** para informar ao Siri que o aplicativo não exige um valor para um determinado parâmetro. Se o usuário fornecer um mesmo assim, ele simplesmente será ignorado por Siri.

### <a name="the-confirm-stage"></a>O estágio de confirmação

O estágio confirmar tem duas finalidades:

- Para dizer ao Siri o resultado esperado de lidar com uma intenção para que Siri possa informar ao usuário o que vai acontecer.
- Fornece uma verificação de oportunidade de todos os Estados necessários que o aplicativo pode precisar para concluir a solicitação apresentada pelo usuário, como ter dinheiro suficiente no banco para fazer o pagamento solicitado.

O aplicativo fornecerá uma **resposta de intenção** da etapa confirmar, que deve ser preenchida com o máximo de informações que o aplicativo disponibilizou para que o Siri possa se comunicar com eficiência com o usuário.

Com base no tipo de domínio e de ação, o Siri pode solicitar a confirmação do usuário, como antes de enviar um pagamento ou reservar um Ride.

### <a name="the-handle-stage"></a>O estágio do identificador

O estágio do identificador é a parte mais importante do trabalho com uma intenção, pois é o ponto em que o aplicativo atende a solicitação do usuário executando a tarefa que foi solicitada.

Assim como fazia no estágio Confirm, o aplicativo precisa fornecer o máximo possível de informações sobre o resultado, de modo que Siri possa relacioná-lo ao usuário. Às vezes, essas informações serão apresentadas visualmente ou outras vezes Siri simplesmente as falarão de volta ao usuário.

Pode haver ocasiões em que o aplicativo pode exigir tempo extra para processar uma determinada solicitação, como atrasos de chamada de rede ou se uma pessoa ao vivo precisa atender à solicitação (como concluir e enviar um pedido ou direcionar um carro para o local do usuário). Quando Siri estiver aguardando uma resposta do aplicativo, ele exibirá uma interface de usuário em espera para o usuário informando que o aplicativo está processando a solicitação.

O ideal é que o aplicativo forneça uma resposta para Siri dentro de dois a três segundos. Se o aplicativo sabe que uma determinada resposta levará mais tempo para ser processada, ele precisa enviar um código de resposta de **InProgress** para Siri. O Siri, em seguida, informará ao usuário que o aplicativo está processando a solicitação em segundo plano e continuará a fazer isso mesmo que deixe o ambiente Siri.

## <a name="adding-sirikit-to-the-app"></a>Adicionando SiriKit ao aplicativo

Com o SiriKit no iOS 10, a Apple criou dois novos pontos de extensão:

- **Extensão de tentativas** – fornece Siri com o conteúdo do aplicativo e executa as tarefas necessárias para atender a quaisquer tentativas com suporte.
- **Extensão da interface do usuário de tentativas** – fornece uma interface do usuário personalizada que será exibida para o conteúdo dos aplicativos dentro do Siri.

Também há uma API para fornecer palavras e frases a serem Siridas para auxiliar no reconhecimento na forma de:

- **Vocabulário de aplicativo** -palavras e frases que são comuns a todos os usuários do aplicativo.
- **Vocabulário do usuário** -palavras e frases que são exclusivas de um determinado usuário do aplicativo.

## <a name="the-intents-extension"></a>A extensão de tentativas

A extensão de tentativas é responsável por lidar com as interações principais entre o aplicativo e o Siri da seguinte maneira:

[![A extensão de tentativas](understanding-sirikit-images/intents01.png)](understanding-sirikit-images/intents01.png#lightbox)

A extensão de intenção pode dar suporte a uma ou mais intenções, cabe ao desenvolvedor decidir como desejam implementar SiriKit no aplicativo. O desenvolvedor também poderia adicionar uma extensão de intenção separada para cada tentativa que precisa ser tratada.  Dito isso, a Apple solicita que o desenvolvedor limite o número de extensões de intenção para que o Siri não tenha vários processos abertos em relação ao aplicativo, o que exige mais memória e tempo para lidar.

O desenvolvedor também deve estar ciente de que a extensão de intenção será executada em segundo plano enquanto o Siri estiver ativo. Isso permite que o Siri realize ativamente uma conversa com o usuário enquanto ainda se comunica com a extensão para processar informações sobre a solicitação.

## <a name="privacy-and-security-considerations"></a>Considerações de privacidade e segurança

A Apple assumiu grandes medidas para garantir que uma informação privada do usuário seja segura ao trabalhar com siri e, como tal, há várias interações que exigem que o usuário esteja conectado no dispositivo iOS. Por exemplo, ao solicitar uma Ride ou fazer um pagamento.

Além disso, há comportamentos específicos que o aplicativo talvez queira limitar ao usuário que está sendo conectado ao dispositivo. Para essas situações, o aplicativo pode solicitar o comportamento **restrito enquanto bloqueado** . Isso é feito por meio de uma configuração no `Info.plist` arquivo.

A estrutura de autenticação local está disponível para a extensão de intenção para que o aplicativo possa solicitar informações de autenticação adicionais ao usuário, mesmo que o dispositivo já esteja desbloqueado.

Por fim, Apple Pay está disponível para a extensão de intenção para que o aplicativo possa concluir uma transação usando Apple Pay e a planilha de Apple Pay interna será exibida acima da interface Siri.

Além disso, a Apple deseja garantir que os usuários saibam quando estão enviando informações para um aplicativo de terceiros e, como tal, o usuário **deve** dizer o nome específico do aplicativo (conforme especificado no nome de exibição do pacote do aplicativo) ao fazer uma solicitação.

A Apple criou o Siri para realizar conversas naturais e fluentes com o usuário e, por isso, o nome do pacote do aplicativo pode ser usado em muitas partes da fala, onde quer que caibam naturalmente na solicitação do usuário.

Uma das coisas comuns que os usuários farão é "verbifyr" o nome do aplicativo, em outras palavras, pegar o nome do aplicativo e usá-lo como um verbo em uma solicitação. Por exemplo, *"MonkeyChat bobo esses eram ótimos bananas".*

## <a name="the-intents-ui-extension"></a>A extensão da interface do usuário de tentativas

A extensão da interface do usuário de tentativas apresenta a oportunidade de trazer a interface do usuário e a identidade visual da experiência do Siri e fazer com que os usuários se sintam conectados ao aplicativo. Com essa extensão, o aplicativo pode trazer a marca, bem como o Visual e outras informações para a transcrição.

[![Saída de extensão de interface do usuário de tentativas de exemplo](understanding-sirikit-images/intents02.png)](understanding-sirikit-images/intents02.png#lightbox)

A extensão da interface do usuário de tentativas sempre retornará um `UIViewController` e o aplicativo poderá adicionar qualquer coisa que goste dentro do controlador de exibição, como mostrar informações adicionais que vão além da resposta inicial. A interface de usuário de tentativas também pode atualizar o usuário com o status de um evento de execução longa, como quanto mais tempo levará um carro de compartilhamento para alcançar seu local.

A extensão da interface do usuário de tentativas sempre será exibida junto com outro conteúdo do Siri, como o ícone do aplicativo e o nome na parte superior da interface do usuário ou, com base na intenção, os botões (como enviar ou cancelar) podem ser exibidos na parte inferior.

Há algumas instâncias em que o aplicativo pode substituir as informações que o Siri está exibindo para o usuário por padrão, como mensagens ou mapas, onde o aplicativo pode substituir a experiência padrão por uma personalizada para o aplicativo.

> [!IMPORTANT]
> Embora seja possível adicionar elementos interativos, como `UIButtons` ou `UITextFields` até a extensão da interface do usuário da intenção `UIViewController` , eles são estritamente proibidos como a interface do usuário da intenção em não interativo e o usuário não poderá interagir com eles.

É totalmente opcional para o aplicativo fornecer uma extensão de interface do usuário de intenção, pois Siri contém um conjunto padrão de interface do usuário para cada tipo de tentativa. Além disso, as interfaces de interface do usuário de intenções só estão disponíveis para determinados propósitos que a Apple considerou ser útil para o usuário.

## <a name="adding-sirikit-vocabulary"></a>Adicionando o vocabulário SiriKit

A parte final da implementação de SiriKit está dentro do aplicativo, fornecendo o vocabulário necessário. Muitos aplicativos têm maneiras exclusivas de descrever informações para o usuário e maneiras exclusivas de que o usuário fornecerá informações para o aplicativo.

Por isso, o Siri exige que a assistência do aplicativo entenda as palavras e frases exclusivas do aplicativo. Algumas dessas frases serão parte do aplicativo para que cada usuário as conheça e entenda. Ainda assim, outras serão exclusivas para um determinado usuário do aplicativo.

### <a name="app-specific-vocabulary"></a>Vocabulário específico do aplicativo

O vocabulário específico do aplicativo define as palavras e frases específicas que serão conhecidas por todos os usuários do aplicativo, como tipos de veículo ou nomes de ginástica. Como eles fazem parte do aplicativo, eles são definidos em um `AppIntentVocabulary.plist` arquivo como parte do pacote do aplicativo principal. Além disso, essas palavras e frases devem ser localizadas.

Há várias partes em um arquivo de vocabulário `AppIntentVocabulary.plist` :

- **Uso de aplicativo de exemplo** -eles fornecem um conjunto de casos de uso comuns para as solicitações que o usuário pode fazer do aplicativo. Por exemplo: *"iniciar um treinamento com MonkeyFit".*
- **Parâmetros** -fornecem um conjunto de tipos de parâmetro não padrão específicos para o aplicativo. Por exemplo, os nomes de ginástica para o aplicativo MonkeyFit. Consistem em:
  - **Frase** – permite que o aplicativo defina termos exclusivos para o aplicativo. Por exemplo: o tipo de ginástica "Bananarific" para o aplicativo MonkeyFit.
  - **Pronúncia** – fornece dicas de pronúncia para Siri como uma grafia fonética simples para uma determinada frase. Por exemplo, "BA Nana ri ficos".
  - **Exemplo** – fornece um exemplo de como usar a frase especificada no aplicativo. Por exemplo, *"iniciar um Bananarific em MonkeyFit"*.

Para obter mais informações, consulte referência de [formato de arquivo de vocabulário do aplicativo](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CustomVocabularyKeys.html#//apple_ref/doc/uid/TP40016875-CH10-SW1)da Apple.

### <a name="user-specific-vocabulary"></a>Vocabulário específico do usuário

O vocabulário específico do usuário vai fornecer palavras ou frases que sejam exclusivas para usuários individuais do aplicativo. Eles serão fornecidos em tempo de execução no aplicativo principal (não nas extensões de aplicativo) como um conjunto ordenado de termos solicitados em uma prioridade de uso mais significativa para os usuários, com os termos mais importantes no início da lista.

Veja o exemplo do aplicativo MonkeyChat apresentado acima. MonkeyChat mantém uma lista de todos os contatos do usuário, que ele enviará ao Siri por meio do vocabulário específico do usuário. Ele também mantém uma lista dos 10 contatos mais recentes que o usuário recebeu e tem um conjunto de contatos favoritos para cada usuário. Para este exemplo, os contatos favoritos devem estar no início do vocabulário específico do usuário, seguidos pelos contatos recentes e o restante dos contatos do usuário.

Os seguintes tipos de informações têm suporte do vocabulário específico do usuário:

- Nomes de contato.
- Nomes de ginástica.
- Nomes de álbuns de fotos.
- Palavras-chave de foto.

Se o aplicativo depender do catálogo de endereços do iOS, o aplicativo não precisará realizar nenhuma ação, pois essas informações já estão disponíveis para Siri. O aplicativo só precisa fornecer nomes de contato se o aplicativo tiver seu próprio banco de dados exclusivo de contatos.

Ao criar o vocabulário, forneça apenas os valores necessários que os usuários conhecem e se preocupam. Evite fornecer informações como números de telefone ou endereços de email.

O aplicativo também precisa atualizar o Siri imediatamente quando o vocabulário específico do usuário muda. Os usuários estão acostumados a solicitar informações de Siri o Instant it foi adicionado ao dispositivo iOS. Por exemplo, se o usuário adicionar um novo contato no aplicativo, envie essas informações para Siri assim que o usuário salvá-la.

O mais importante é que o aplicativo _deve_ excluir informações do vocabulário Siri imediatamente, uma vez que um usuário poderia se sentir se excluísse uma informação, mas o Siri ainda estava reconhecendo as horas ou os dias mais tarde.

> [!IMPORTANT]
> O aplicativo deve remover todo o vocabulário específico do usuário de Siri se o usuário optar por redefinir o aplicativo ou se ele fizer logoff.

## <a name="sirikit-permissions"></a>Permissões de SiriKit

A parte final do SiriKit é centralizada em relação às permissões. Assim como o uso de outros recursos do iOS (como fotos, câmera ou contatos), os usuários precisam conceder permissão explícita para que o aplicativo se comunique com o Siri.

O aplicativo é capaz de fornecer uma cadeia de caracteres que define quais informações ele fornecerá ao Siri e fornece um motivo para o motivo pelo qual o usuário deve conceder esse acesso.

A Apple sugere que o aplicativo deve solicitar permissão do usuário para usar o Siri na primeira vez que o usuário abrir o aplicativo após a atualização para o iOS 10. Isso é para que os usuários saibam sobre a integração do Siri e possam ter o uso previamente aprovado antes de fazer sua primeira solicitação.

## <a name="sirikit-and-maps"></a>SiriKit e mapas

SiriKit é parte integrante do iOS e usa a estrutura de tentativas maior adicionada ao iOS 10. A estrutura de intenções foi projetada para compartilhar ações comuns e compartilhadas e intenções com outras partes do sistema.

A estrutura de intenções vai além da integração apenas Siri e fornece outros recursos, como a integração de contatos, em que o aplicativo pode se tornar o aplicativo de telefonia ou de mensagens padrão para contatos específicos. As intenções também fornecem uma profunda integração com o CallKit para fornecer aos usuários a melhor experiência de VOIP possível.

O aplicativo Maps no iOS 10 adicionou recursos, como o compartilhamento de Rides, onde o usuário pode agendar uma Ride diretamente dentro da interface do usuário do Maps. O SiriKit fornece um ponto de extensão comum com mapas, portanto, o compartilhamento de Rides (e outros) tentativas pode ser compartilhado entre Siri e mapas.

Isso significa que, se o aplicativo tiver adotado as extensões SiriKit, ele também obterá a integração de mapas gratuitamente.

## <a name="designing-a-great-siri-experience"></a>Criando uma ótima experiência de Siri

A criação de uma ótima experiência do usuário ao integrar um aplicativo ao Siri é diferente de criar uma grande interface do usuário do aplicativo. Ao contrário das situações normais em que o usuário está interagindo com o aplicativo diretamente na tela, ao usar o Siri, há muitas vezes em que nenhuma interface visual é visível. Por exemplo, quando o usuário iniciou a conversa com *"Ei Siri"*.

### <a name="how-siri-helps-the-developer"></a>Como o Siri ajuda o desenvolvedor

Ao criar as interações de um aplicativo com o Siri, o aplicativo criará uma *interface de conversa*, o que significa que o contexto é derivado da conversa que o Siri está tendo com o usuário em nome do aplicativo.

Na ausência de uma referência visual, o usuário deve manter o controle das informações apresentadas em seu cabeçalho. Por isso, o Siri apresenta as informações mínimas necessárias para alcançar a tarefa que o usuário está querendo realizar.

A interface de conversação é moldada pelas perguntas e respostas do usuário e do Siri durante a conversa. Portanto, é importante pensar sobre como o Siri faz perguntas e responde ao criar essa interface.

Veja o exemplo a seguir do usuário que está criando uma mensagem, Siri pode responder com a pergunta *"pronto para enviá-la?"*. O usuário pode responder de várias maneiras diferentes, como *"enviá-la"*, *"Cancelar"* ou mesmo algo totalmente não relacionado a essa pergunta. Não importa como a conversa é reproduzida, o Siri a tratará para o aplicativo e só enviará a ele as informações relevantes conforme ele se tornar disponível.

Há várias maneiras diferentes pelas quais um usuário pode iniciar uma conversa com siri:

- Ao selecionar o dispositivo, pressione o botão página inicial. Nessa situação, o Siri apresentará mais interfaces visuais e menos respostas verbal.
- Dizendo *"Ei Siri"* e iniciando uma conversa sem intervenções. Nessa situação, Siri será menos visual e mais verbal.
- O uso de recursos de acessibilidade, como os auxílios de audição habilitados para Bluetooth, em que a interface do usuário será adaptada para uma usuária com necessidades especiais.
- Usando o carro, onde o usuário precisa manter sua atenção focada na condução, mantendo o mínimo de distrações.

### <a name="how-the-developer-helps-siri"></a>Como o desenvolvedor ajuda a Siri

Ao integrar um aplicativo com o Siri, o desenvolvedor precisa testar essa integração com frequência e garantir que elas estejam fazendo muitas solicitações diferentes solicitando a mesma informação ou tarefa de tantas maneiras diferentes quanto possível.

Como não há duas pessoas pensam, é essencial que o desenvolvedor obtenha o máximo de testadores beta diferentes possível para ajudar a ajustar a integração do Siri. Os usuários podem solicitar informações ou fazer solicitações de maneira que o desenvolvedor nunca e esse ajuste possa ajudar a garantir que o grupo mais amplo de usuários tenha uma ótima experiência ao usar seu aplicativo com o Siri.

Teste em diferentes situações e ambientes. Inicie as conversas com o Siri de todas as formas possíveis para garantir que essas conversas fiquem fluidas e naturais. Teste em locais onde o usuário mais do que provavelmente estava usando o aplicativo, como em um Gym lotado.

Certifique-se de que o aplicativo está fornecendo todas as informações que o Siri precisa para representar corretamente a solicitação e o resultado para o usuário. Isso é especialmente verdadeiro ao usar o Siri em uma situação de mão livre.

### <a name="siri-design-guidelines"></a>Diretrizes de design de Siri

Lembre-se sempre de que o Siri está tendo uma conversa com o usuário em nome do aplicativo. O desenvolvedor não quer ter certeza de que essa conversa permaneça o mais fluida e natural possível.

Como faria com qualquer conversa importante, o desenvolvedor precisa garantir o seguinte:

- Se o aplicativo está preparado para a conversa.
- O aplicativo ouve exatamente o que o usuário está tentando realizar.
- Que o aplicativo solicita as perguntas apropriadas nos horários apropriados.
- O aplicativo responde à solicitação com as informações que o usuário está buscando.

#### <a name="preparing-for-the-conversation"></a>Preparando para a conversa

A primeira coisa a se lembrar é que os usuários do aplicativo não vão ser exatamente como o desenvolvedor. Eles podem vir de diferentes planos de fundo, falar em diferentes idiomas ou ter necessidades especiais ao trabalhar com o aplicativo.

Além disso, como o desenvolvedor criou e construiu o aplicativo, ele tem um conhecimento profundo e profundo do aplicativo e de seus trabalhos internos e funções que um usuário típico não terá. Portanto, o desenvolvedor pode solicitar a solicitação de Siri de forma diferente de um usuário normal.

É por isso que é essencial ter tantas pessoas diferentes quanto possível interagir com o aplicativo por meio de siri. Os usuários podem fazer solicitações do aplicativo por meio de Siri que o desenvolvedor nunca pensou ou de maneiras que o desenvolvedor não considere.

#### <a name="ensure-the-app-is-a-good-listener"></a>Verifique se o aplicativo é um bom ouvinte

O desenvolvedor precisa garantir que o aplicativo seja um bom ouvinte e esteja obtendo as especificidades da conversa que atendem às expectativas do usuário. Mas também é possível que eles possam não ter fornecido todas as informações que o aplicativo exige para alcançar a tarefa solicitada.

Há várias maneiras pelas quais o aplicativo pode lidar com essa situação:

- **Escolha um bom padrão para o valor ausente** -por exemplo, um aplicativo de compartilhamento de Ride pode usar como padrão o local atual do usuário se eles não especificassem de onde desejavam ser coletados.
- **Faça uma estimativa** informada – usando informações específicas que o aplicativo coletou no usuário, o aplicativo pode ser capaz de tomar e instruído a adivinhar as informações que faltam, como preencher um número móvel ausente das informações de contato do usuário. No entanto, deve-se ter cuidado para evitar surpresas ruins, como escolher a opção mais cara etc.
- **Solicitar mais informações** -o aplicativo pode fazer com que o Siri solicite o valor ausente ao usuário. No entanto, a chave aqui é manter as conversas simples e até o ponto. Os usuários ficarão frustrados rapidamente se precisarem responder a várias perguntas para obter a solicitação.
- **Manipule incorretamente informações normalmente** – o usuário pode fornecer um valor que o aplicativo não estava esperando ou que ele não pode manipular no contexto fornecido. Verifique se o aplicativo relaciona essa situação com o usuário de uma forma que torna claro e fácil para que eles sejam corrigidos.

Quando o aplicativo é apresentado com um único valor que está em questão, a maneira preferida de lidar com isso é fazer com que o Siri peça confirmação ao usuário. Por exemplo, *"você quis dizer que Bobou o ótimo?"*, que pode responder com uma simples sim ou sem resposta.

Quando há uma situação em que várias opções possíveis podem estar corretas para um único valor, a Desambigüidade é o método de tratamento preferencial. Nessa situação, o Siri pode solicitar ao usuário até dez opções possíveis para sua escolha. Por exemplo:

```csharp
Who do you want to send the message to?

* Bobo the Great!
* Bobo Jr.
* Little Bobo
```

Se ainda estiver em questão, faça com que o Siri solicite ao usuário que forneça uma resposta totalmente nova e mais específica para um determinado valor.

#### <a name="request-final-confirmation"></a>Solicitar confirmação final

Antes que o aplicativo realmente execute a tarefa para atender à solicitação do usuário, o Siri verificará com a extensão do aplicativo para garantir que tudo esteja em vigor. Por exemplo, o usuário tem dinheiro suficiente em sua conta para fazer o pagamento solicitado?

Além disso, o aplicativo precisa garantir que ele esteja fornecendo todas as informações possíveis para Siri para que possa apresentá-lo ao usuário e confirmar que a tarefa a ser executada atenda às suas expectativas.

Depois que o usuário confirmar a solicitação e o aplicativo a tiver executado, o aplicativo precisará novamente garantir que ele tenha fornecido todos os resultados para Siri para que possa relacioná-los ao usuário.

#### <a name="responding-to-the-request"></a>Respondendo à solicitação

O Siri tem várias interfaces de usuário internas para cada um dos domínios e ações que ele conhece. No entanto, quando apropriado, o aplicativo pode fornecer uma extensão de interface do usuário de intenção personalizada para enriquecer a experiência do usuário apresentando a identidade visual do aplicativo e a IU ou mais informações do que estavam presentes na solicitação.

Dito isso, o retentor deve ser usado ao criar interfaces personalizadas para Siri. Normalmente, o usuário está querendo ter uma tarefa específica realizada o mais rápido possível e não quer ser sobrecarregado com informações desnecessárias.

Também deve-se tomar cuidado para garantir que a interface do usuário personalizada pareça e responda corretamente em todos os diferentes dispositivos e orientações do iOS que o usuário pode ter ou usando o dispositivo.

Quando apropriado, use a API SiriKit para ocultar todas as informações redundantes já presentes na interface do usuário do Siri padrão. Ainda assim, certifique-se de que o aplicativo ainda está fornecendo as informações para Siri para que possa apresentá-las verbalmente em situações sem intervenções.

Pode haver situações em que o Siri iniciará o aplicativo para atender à solicitação do usuário, como apresentar as fotos solicitadas pelo usuário. Nessas situações, não surpreenda o usuário. Exibe as informações esperadas sem ter etapas intermediárias ou uma interação adicional necessária. Nunca exiba informações ou execute uma tarefa que o usuário não está esperando.

### <a name="polishing-the-design"></a>Aprimore o design

Há várias etapas que a Apple sugere para aprimorar o design das interfaces de conversação. Primeiro, está fornecendo exemplos claros e concisos de vocabulário e de caso de uso para Siri.

Uma das maneiras de um usuário descobrir o aplicativo é iniciar uma conversa com siri e perguntar *"o que você pode fazer?"* O Siri mostrará várias coisas diferentes que ele pode fazer, incluindo o aplicativo do desenvolvedor e os casos de uso do Hero de exemplo que ele forneceu por meio de seu `plist` arquivo.

Como escrever um bom exemplo de casos de uso:

- Verifique se os exemplos incluem o nome do aplicativo.
- Mantenha o exemplo curto e para o ponto.
- Forneça vários exemplos para cada uma das tentativas às quais o aplicativo dá suporte.
- Priorize as intenções e os exemplos dentro delas com base nos casos de uso mais comuns do aplicativo.
- Verifique se o aplicativo fornece exemplos localizados.
- Certifique-se de que cada exemplo fornecido funcione conforme o esperado no aplicativo.
- Evite resolver Siri nos exemplos, portanto, não inclua texto como *"Ei Siri..."*
- Evite qualquer pleasantries desnecessária, como *"favor"* ou *"Obrigado"*.

Tome o tempo apropriado para explorar e experimentar como o aplicativo pode moldar a conversa que o Siri está tendo com o usuário em seu nome. Certifique-se de se comunicar com usuários típicos durante todo o processo, pois suas interações com e as expectativas do aplicativo podem mudar ao longo do tempo.

Lembre-se sempre de testar o aplicativo em situações diferentes e em todos os métodos diferentes para invocar uma conversa com o Siri. Teste em locais reais do mundo que o usuário pode estar usando o aplicativo, longe do escritório e da mesa.

Sinta-se à vontade para que as conversas com Siri (em nome do aplicativo) sejam fluidos, naturais e "estejam à direita".

## <a name="summary"></a>Resumo

Este artigo abordou os principais conceitos necessários para usar o SiriKit e mostrar que ele pode interagir com os aplicativos Xamarin. iOS para fornecer serviços que podem ser acessados pelo usuário usando o Siri e o aplicativo Maps em um dispositivo iOS.

## <a name="related-links"></a>Links Relacionados

- [Exemplo de ElizaChat](/samples/xamarin/ios-samples/ios10-elizachat)
- [Guia de programação do SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Referência de estrutura de tentativas](https://developer.apple.com/reference/intents)
- [Referência da estrutura da interface do usuário de tentativas](https://developer.apple.com/reference/intentsui)