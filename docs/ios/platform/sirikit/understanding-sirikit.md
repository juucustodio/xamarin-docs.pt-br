---
title: Noções básicas sobre conceitos de SiriKit
description: Este documento descreve os principais conceitos necessários para trabalhar com SiriKit em um aplicativo xamarin. IOS. Por exemplo, ele aborda as intenções e extensões de interface do usuário de tentativas, SiriKit permissões, criação de uma ótima experiência e muito mais.
ms.prod: xamarin
ms.assetid: 99EC5C1E-484F-4371-8555-58C9F60DE37F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/02/2017
ms.openlocfilehash: b72246968c9b321329e56fd51eaaa98a1625922e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123070"
---
# <a name="understanding-sirikit-concepts"></a>Noções básicas sobre conceitos de SiriKit

_Este artigo aborda os principais conceitos que serão necessários para trabalhar com SiriKit em um aplicativo xamarin. IOS._


Novo para o iOS 10, o SiriKit permite que um aplicativo xamarin. IOS fornecer serviços que são acessíveis para o usuário usando o Siri e o aplicativo de mapas em um dispositivo iOS. Essa funcionalidade é fornecida em um ou mais a extensão do aplicativo usando o novo **intenções** e **IU de Intents** estruturas.

SiriKit permite que um aplicativo iOS fornecer serviços que são acessíveis para o usuário usando o Siri e o aplicativo de mapas em um dispositivo iOS usando extensões de aplicativo e o novo **intenções** e **IU de Intents** estruturas.

Siri funciona com o conceito de **domínios**, sabe de grupos de ações para tarefas relacionadas. Cada interação que o aplicativo tem com Siri deve se enquadram em um de seus domínios de serviço conhecido da seguinte maneira:

- Chamada de vídeo ou de áudio.
- Uma jornada de reserva.
- Gerenciando exercícios físicos.
- Sistema de mensagens.
- Pesquisando fotos.
- Enviar ou receber pagamentos.

Quando o usuário faz uma solicitação do Siri que envolvem um dos serviços de aplicativo da extensão, SiriKit envia a extensão de um **intenção** objeto que descreve a solicitação do usuário, juntamente com quaisquer dados de suporte. A extensão de aplicativo, em seguida, gera apropriado **resposta** do objeto para o determinado **intenção**, detalhando como a extensão pode lidar com a solicitação.

## <a name="the-intents-and-intents-ui-extensions"></a>As intenções e propósitos extensões de interface do usuário

Siri e o aplicativo de mapas interagem com os serviços do aplicativo por meio de dois tipos diferentes de extensões de aplicativo:

- **Extensão de tentativas** -fornece Siri e mapas com o aplicativo de conteúdo e executa as tarefas necessárias para cumprir qualquer intenções com suporte.
- **Extensão de interface do usuário de tentativas** -fornece uma interface do usuário personalizada que será exibido para o conteúdo do aplicativo dentro da Siri ou mapas.

O aplicativo deve fornecer uma extensão de tentativas para dar suporte a SiriKit e é responsável por fornecer informações que Siri e mapas podem apresentar ao usuário e para lidar com as intenções.

Criar uma extensão de interface do usuário de tentativas é opcional, já que Siri geralmente lida com toda a interação de usuário e tem uma interface do usuário padrão e internos para apresentar informações em cada um dos domínios com suporte. Fornecendo uma extensão de interface do usuário de tentativas, o aplicativo pode usar o **intenção de interface do usuário do** framework para apresentar uma rica e personalizado Interface do usuário que contam com a identidade visual do aplicativo e informações adicionais.

## <a name="siri-and-the-maps-app-role"></a>Siri e a função de aplicativo de mapas

As solicitações do usuário falado são processados e analisados semanticamente pelo Siri que transforma essas solicitações em intenções acionáveis que pode lidar com a intenção de extensões do idioma.

Maps usa extensões de intenção do aplicativo para exibir informações na interface do mapa em resposta às ações do usuário. Como solicitar nas proximidades de restaurantes ou Obtendo revisões de restaurante do aplicativo.

Siri e mapas de gerenciar todas as interações do usuário e exibem os resultados usando a interface padrão do sistema. A função de extensões de aplicativo é principalmente fornecer os dados que são exibidos. Opcionalmente, o aplicativo pode fornecer uma extensão de interface do usuário de tentativas e apresentar uma interface do usuário personalizada para aprimorar a interface padrão do sistema.

## <a name="interacting-with-siri-via-sirikit"></a>Interagindo com Siri por meio de SiriKit

Esta seção apresentará uma visão geral de como o SiriKit permite que o usuário interaja com o aplicativo usando o Siri. Para este exemplo, usaremos o aplicativo MonkeyChat fictício:

[![](understanding-sirikit-images/monkeychat01.png "O ícone de MonkeyChat")](understanding-sirikit-images/monkeychat01.png#lightbox)

MonkeyChat mantém seu próprio catálogo de contato de amigos do usuário, cada um associado com um nome de tela (como Bobo, por exemplo) e permite que o usuário enviar bate-papos do texto para cada amigo por seu nome de tela.

Há muitas maneiras que o usuário pode iniciar uma interação com o aplicativo, uma vez que pessoas diferentes podem fazer a mesma solicitação de várias formas diferentes.

Por exemplo, se o usuário quisesse enviar uma mensagem para o seu amigo Bobo, eles podem ter a conversa seguir com Siri:

_Usuário: Ei Siri, enviar uma mensagem MonkeyChat._<br />
_Siri: A quem?_<br />
_Usuário: Bobo._<br />
_Siri: O que você deseja dizer ao Bobo?_<br />
_Usuário: Envie Bananas mais._<br />

Outra pessoa pode fazer a mesma solicitação com uma conversa diferente:

_Usuário: Envie uma mensagem para Bobo em MonkeyChat._<br />
_Siri: O que você deseja dizer ao Bobo?_<br />
_Usuário: Envie bananas mais._<br />

E outro usuário pode fazer uma solicitação ainda mais curta:

_Usuário: MonkeyChat Bobo envie bananas mais._<br />
_Siri: Okey, envio de mensagem, envie mais bananas para Bobo em Monkeychat._<br />

Ou, até mesmo fazer a mesma solicitação em um idioma diferente:

_Usuário: MonkeyChat Bobo s'il vous plaît envoyer além de bananes._<br />
_Siri: Oui, envoyer de plaît envoi mensagem s'il vous além de bananes à Bobo sur Monkeychat._<br />

Ainda outro usuário pode ser muito detalhado em sua conversa:

_Usuário: Ei Siri, pode você faça um favor mim e inicie o aplicativo MonkeyChat para enviar um texto com a mensagem, envie bananas mais._<br />
_Siri: A quem?_<br />
_Usuário: Meu melhor pal Bobo._<br />

Além disso, há muitas maneiras que Siri pode responder a uma solicitação, alguns com base em como a solicitação foi feita:

- **Mantendo o botão Home** -Siri fornecerá mais respostas visual com comentários verbais limitado.
- **Por "Ei Siri"** -Siri será mais verbal e fornecer respostas visual menos.

Siri também está ajustado para atender às necessidades de acessibilidade do usuário e de interagir e responder com base nessas necessidades.

Não importa como é feita uma solicitação ou como o Siri responde à solicitação, Siri manipula a conversa com o usuário e o aplicativo (por meio de suas extensões) fornece a funcionalidade.

Quando o usuário faz uma solicitação verbal do Siri, estas são as etapas que Siri seguirá:

[![](understanding-sirikit-images/monkeychat02.png "As etapas que Siri seguirá")](understanding-sirikit-images/monkeychat02.png#lightbox)

1. Primeiro, Siri leva o áudio do usuário **fala** e o converte em texto.
2. Em seguida, o texto é convertido em um **intenção**, um estruturado a representação de solicitação do usuário.
3. Com base na intenção, Siri levará **ação** para executar a solicitação do usuário.
4. Por fim, apresentará Siri **respostas** (tanto visual quanto verbal) para o usuário com base na ação tomada.

Há três maneiras principais que o aplicativo pode fazer parte de conversação do usuário com Siri:

[![](understanding-sirikit-images/monkeychat03.png "As três maneiras principais que o aplicativo pode fazer parte de conversa que os usuários com Siri")](understanding-sirikit-images/monkeychat03.png#lightbox)

1. **Vocabulário** -isso é como o aplicativo informa Siri as palavras que ele precisa saber para interagir com ele.
2. **Lógica de aplicativo** – essas são as ações e as respostas que levará o aplicativo com base em determinadas intenções.
3. **Interface do usuário** -essa é a interface de usuário personalizada opcional que o aplicativo pode fornecer suas respostas em.

### <a name="example"></a>Exemplo

Considerando as informações acima, examine como a conversa a seguir interagiria com o aplicativo MonkeyChat:

_Usuário: Ei Siri, enviar uma mensagem Bobo em MonkeyChat._<br />
_Siri: O que você deseja dizer ao Bobo?_<br />
_Usuário: Envie bananas mais._<br />

A primeira função de que o aplicativo recebe a conversa é ajudar a Siri entender a voz do usuário:

[![](understanding-sirikit-images/monkeychat04.png "Ajudando Siri entender a fala de usuários")](understanding-sirikit-images/monkeychat04.png#lightbox)

Siri não tem o nome "Bobo" em seu banco de dados, mas o aplicativo faz e compartilhou essas informações com Siri por meio de seu vocabulário. O aplicativo também ajuda a Siri reconhecer que Bobo é um destinatário, pois ele especificado-los para Siri como uma *entre em contato com*.

Siri sabe o que mais é necessário para enviar uma mensagem que um destinatário, portanto, rapidamente, ele verificará com a extensão de aplicativo para ver se uma mensagem precisa de conteúdo. Uma vez que MonkeyChat faz, Siri responderá ao usuário com a pergunta: *"O que você deseja dizer ao Bobo?"*

No exemplo acima, o usuário tiver respondido, *"Envie mais bananas"*, que Siri empacotará em um estruturado **intenção**:

[![](understanding-sirikit-images/monkeychat05.png "Siri será empacotar a resposta do usuário em uma intenção a estruturadas")](understanding-sirikit-images/monkeychat05.png#lightbox)

A intenção a estruturadas conterá as seguintes informações:

- **Domínio:** mensagens
- **Intenção:** sendMessage
- **O destinatário:** Bobo
- **Conteúdo:** envie mais bananas

Cada domínio possui como conjunto de saber *ações* que podem ser executadas neles e com base em domínio e a ação, zero a muitos parâmetros podem ser incluídos na intenção enviada ao aplicativo.

A intenção é enviada para a extensão de aplicativo para processamento. Como resultado de processar a intenção, o aplicativo gerará uma **IntentResponse** que será agrupado com a intenção e incluir parâmetros que descrevem o que o aplicativo fez com a intenção.

Cada IntentResponse também incluirá uma **código de resposta** que informa que o Siri se o aplicativo não conseguiu concluir a solicitação ou não. Alguns domínios têm códigos de resposta de erro muito específicos que podem ser enviados também.

Por fim, o IntentResponse incluirá um `NSUserActivity` (como aquelas usadas para dar suporte à mão Off). O `NSUserActivity` será usado para iniciar o aplicativo se a resposta precisa deles para deixar o ambiente de Siri e entrar no aplicativo para concluí-la. 

Siri compilará automaticamente apropriado `NSUserActivity` para iniciar o aplicativo e a retirada em que o usuário parou no ambiente do Siri. No entanto, o aplicativo pode fornecer sua própria `NSUserActivity` com informações personalizadas, se necessário.

Depois que o aplicativo processou a intenção e retornou uma resposta para Siri, em seguida, apresenta os resultados para o usuário (verbalmente e visualmente):

[![](understanding-sirikit-images/monkeychat06.png "Os resultados apresentados ao usuário verbalmente e visualmente")](understanding-sirikit-images/monkeychat06.png#lightbox)

Siri tem várias Interfaces de usuário de resposta interna para cada um dos domínios disponíveis para o aplicativo. No entanto, desde que MonkeyChat forneceu uma extensão de interface do usuário de intenção opcional, ele é usado para apresentar os resultados da conversa para o usuário no exemplo acima.

## <a name="the-intent-lifecycle"></a>O ciclo de vida de intenção

Há três tarefas principais que a extensão do aplicativo precisará executar ao lidar com as intenções:

[![](understanding-sirikit-images/monkeychat07.png "O ciclo de vida de intenção")](understanding-sirikit-images/monkeychat07.png#lightbox)

1. O aplicativo deve **resolver** cada parâmetro em um evento. Como resultado, o aplicativo chamará resolva várias vezes (uma vez por cada parâmetro) e, às vezes, várias vezes no mesmo parâmetro até que o aplicativo e o usuário concordarem sobre o que está sendo solicitado.
2. O aplicativo deve **confirmar** que pode lidar com a intenção solicitada e conte Siri sobre o resultado esperado.
3. Por fim, o aplicativo deve **manipular** a intenção e execute as etapas para alcançar o resultado solicitado.

### <a name="the-resolve-stage"></a>O estágio de resolução

O estágio de resolução ajuda Siri a entender os valores que o usuário tenha fornecido e garante que, na verdade, significa que o usuário é o que acontece quando a intenção é processada pelo aplicativo. 

Esse estágio também fornece uma oportunidade para o aplicativo influenciar o comportamento da Siri durante a conversa com o usuário. Para fazer isso, o aplicativo fornecerá uma **resolução resposta**. Há uma série de resposta predefinida para os diferentes tipos de dados que compreende Siri.

O mais comum a resolução de resposta do aplicativo serão **sucesso**, que significa que o aplicativo correspondente a parte de dados específica de um parâmetro (como nome de tela do usuário) para uma parte das informações que ele sabe sobre.

Pode haver vezes quando o aplicativo precisa para confirmar que uma determinada solicitação corresponde a parte correta das informações que sabe sobre. Nesses casos, ele enviará uma **ConfirmationRequired** resposta uma pergunta Sim ou não para o usuário, como *"Enviar a mensagem para Bobo excelentes?"*

Pode haver outros casos em que o aplicativo exige que o usuário escolher dentre uma lista curta de opções. Nesse caso, o aplicativo fornecerá uma **Desambiguidade** resposta com uma lista de opções de dois a dez para o usuário à sua escolha, como: 

```csharp
Who do you want to message?

* Bobo the Great
* Bobo Jr.
* Little Bobo
```

Siri manipulará o usuário que fez a seleção verbalmente ou interagindo com a UI do Siri e o resultado será enviado de volta para o aplicativo.

Em outros casos, pode não haver informações suficientes para que o aplicativo resolver o parâmetro ou pode haver muitas correspondências para resolver o uso desambiguidade (por exemplo, 80 usuários com Bobo em seu nome). Nesses casos, o aplicativo enviará uma **NeedsMoreDetails** Siri e resposta solicitará ao usuário para ser mais específico.

Se o usuário não fornecer um valor que é necessário para que ele a intenção do processo, ele pode enviar uma **NeedsValue** resposta ter Siri solicitar ao usuário para o valor.

Se o aplicativo não dá suporte a um valor que o usuário tenha dado a um parâmetro específico, ele poderá enviar o **UnsupportedWithReason** resposta forneçam um motivo por que o valor não era suportado. Siri solicitará ao usuário um valor completamente novo e dado-los o motivo pelo qual ele é necessário.

Use, por fim, o **NotRequired** resposta para informar que o Siri que o aplicativo não exige um valor para um determinado parâmetro. Se o usuário fornece um mesmo assim, ele será simplesmente ignorado pelo Siri.

### <a name="the-confirm-stage"></a>O estágio de confirmar

A fase de confirmação tem duas finalidades:

- Para informar ao Siri o resultado esperado de lidar com uma intenção para que o Siri pode informar ao usuário o que vai acontecer.
- Fornece uma verificação de oportunidade de todos os estados necessários o aplicativo pode precisar concluir a solicitação apresentada pelo usuário, como ter dinheiro suficiente no banco para efetuar o pagamento solicitado.

O aplicativo fornecerá uma **intenção resposta** da etapa de confirmação, que deve ser preenchido com conforme a quantidade de informações do aplicativo tem disponível para que o Siri pode comunicá-la efetivamente com o usuário.

Com base no tipo de ação e de domínio, Siri pode solicitar que o usuário para confirmação, como antes de enviar um pagamento ou uma jornada de reserva.

### <a name="the-handle-stage"></a>O estágio de identificador

O estágio de manipular é a parte mais importante de trabalhar com uma intenção porque ele é o ponto em que o aplicativo atende a solicitação do usuário, executando a tarefa que foi solicitado a fazer.

Exatamente como fazia no estágio de confirmar, o aplicativo precisa fornecer o máximo possível de informações sobre o resultado para que o Siri pode relacionar isso ao usuário. Às vezes, essas informações serão apresentadas visualmente ou outras vezes Siri simplesmente falará ela volta para o usuário. 

Pode haver vezes quando o aplicativo pode exigir mais tempo para processar uma determinada solicitação, como atrasos de chamada de rede ou se precisa de uma pessoa ao vivo atender à solicitação (como concluir e enviar um pedido ou dirigir um carro para o local do usuário). Quando o Siri está aguardando uma resposta de aplicativo, ele exibirá uma interface de usuário aguardando para o usuário informando que o aplicativo está processando a solicitação.

O ideal é que o aplicativo deve fornecer uma resposta a Siri dentro de duas a três segundos no máximo. Se o aplicativo sabe que uma determinada resposta vai levar mais tempo para processar, ele precisa para enviar uma **InProgress** código de resposta para Siri. Siri, em seguida, informará ao usuário que o aplicativo está processando a solicitação em segundo plano e continuará a fazê-lo, mesmo se ele deixar o ambiente do Siri.

## <a name="adding-sirikit-to-the-app"></a>Adicionando SiriKit ao aplicativo

Com SiriKit no iOS 10, a Apple criou dois novos pontos de extensão:

- **Extensão de tentativas** - fornece Siri com o conteúdo do aplicativo e executa as tarefas necessárias para cumprir qualquer intenções com suporte.
- **Extensão de interface do usuário de tentativas** -fornece uma interface do usuário personalizada que será exibido para o conteúdo dos aplicativos dentro do Siri. 

Também é uma API para fornecer palavras e frases para Siri para auxiliar no reconhecimento na forma de:

- **Aplicativo vocabulário** -palavras e frases que são comuns a todos os usuários do aplicativo.
- **Usuário vocabulário** -palavras e frases que são exclusivos para um usuário de determinado aplicativo.

## <a name="the-intents-extension"></a>A extensão de tentativas

A extensão de tentativas é responsável por gerenciar as principais interações entre o aplicativo e o Siri da seguinte maneira:

[![](understanding-sirikit-images/intents01.png "A extensão de tentativas")](understanding-sirikit-images/intents01.png#lightbox)

A extensão de intenção pode dar suporte a um ou mais tentativas, cabe ao desenvolvedor decidir como desejam implementar SiriKit no aplicativo. O desenvolvedor também pode adicionar uma extensão de intenção separada para cada tentativa que precisam ser manipuladas.  Dito isso, Apple solicita que o desenvolvedor limitar o número de extensões de intenção para que o Siri não tem vários processos abrir no aplicativo, que exige mais memória e mais tempo para lidar com.

O desenvolvedor também deve estar ciente de que a extensão de intenção serão executados em segundo plano enquanto Siri está ativo. Isso permite que o Siri transportar ativamente em uma conversa com o usuário durante a comunicação ainda com a extensão para processar as informações sobre a solicitação.

## <a name="privacy-and-security-considerations"></a>Considerações de segurança e privacidade

Apple tomou medidas excelentes para garantir que um usuário particular informações estejam protegidas ao trabalhar com Siri e como tal, há várias interações que exigem que o usuário estar conectado no dispositivo iOS. Por exemplo, ao solicitar uma jornada ou fazer um pagamento.

Além disso, há comportamentos específicos que o aplicativo talvez queira limitar ao usuário que está sendo conectado no dispositivo. Nessas situações, o aplicativo pode solicitar a **restringir quando bloqueado** comportamento. Isso é feito por meio de uma configuração no `Info.plist` arquivo.

A estrutura de autenticação Local está disponível para a extensão de intenção para que o aplicativo pode solicitar ao usuário informações de autenticação adicional, mesmo se o dispositivo já está desbloqueado.

Por fim, a Apple Pay está disponível para a extensão de intenção para que o aplicativo pode concluir uma transação usando o Apple Pay e a planilha interna do Apple Pay será exibido acima da interface da Siri.

Além disso, a Apple deseja certificar-se de que os usuários sabe quando eles estão enviando informações para um aplicativo de terceiros 3ª e como tal, o usuário **deve** diga o nome específico do aplicativo (conforme especificado no nome de exibição do pacote do aplicativo) ao fazer uma solicitação.

Apple projetou Siri realizarem naturais e fluidas conversas com o usuário e, por isso, o nome do pacote do aplicativo pode ser usado em muitas partes da fala, independentemente de onde ele se encaixa naturalmente na solicitação do usuário.

Uma das tarefas mais comuns que os usuários farão é "verbify" o nome do aplicativo, em outras palavras, pegar o nome do aplicativo e usá-lo como um verbo em uma solicitação. Por exemplo, *"MonkeyChat Bobo foi um ótimos bananas."*

## <a name="the-intents-ui-extension"></a>A extensão de interface do usuário de tentativas

A extensão de interface do usuário de tentativas apresenta a oportunidade para trazer a interface do usuário do aplicativo e a identidade visual na experiência de Siri e fazer com que os usuários se sinta conectado ao aplicativo. Com essa extensão, o aplicativo pode trazer o marca como visual e outras informações para a transcrição.

[![](understanding-sirikit-images/intents02.png "Exemplo de saída de extensão de interface do usuário de tentativas")](understanding-sirikit-images/intents02.png#lightbox)

A extensão de interface do usuário de tentativas sempre retornará um `UIViewController` e o aplicativo pode adicionar qualquer coisa que ele gosta de dentro do controlador de exibição, como mostrando informações adicionais que vai além de resposta inicial. A interface do usuário intenções também pode atualizar o usuário com o status de um evento de execução longa, como quanto mais demorada será uma jornada de compartilhamento de carro para acessar sua localização.

A extensão de interface do usuário de tentativas será sempre exibida juntamente com outros tipos de conteúdo, como o ícone do aplicativo e o nome na parte superior da interface do usuário Siri ou, com base na intenção, botões (como enviar ou Cancelar) podem ser exibido na parte inferior.

Há alguns casos em que o aplicativo pode substituir as informações que Siri exibe para o usuário por padrão, como mensagens ou mapeia onde o aplicativo pode substituir a experiência padrão com um personalizado para o aplicativo.

> [!IMPORTANT]
> Embora seja possível adicionar elementos interativos, como `UIButtons` ou `UITextFields` para a extensão de interface do usuário de intenção `UIViewController`, esses são estritamente proibidos como a intenção da interface do usuário no não interativo e o usuário não será capaz de interagir com eles.

Ele é totalmente opcional para o aplicativo fornecer uma extensão de interface do usuário de intenção de uma vez que o Siri contém um conjunto padrão de interface do usuário para cada tipo de intenção. Além disso, as interfaces de IU de Intents só estão disponíveis para certos intenções que Apple foi considerado seria útil para o usuário.

## <a name="adding-sirikit-vocabulary"></a>Adicionando o vocabulário de SiriKit

A parte final do implementar SiriKit se encontra dentro do aplicativo, fornecendo o vocabulário necessário. Muitos aplicativos têm maneiras exclusivas de descrever informações ao usuário e que o usuário fornecerá informações para o aplicativo de maneiras exclusivas.

Por isso, o Siri requer assistência do aplicativo para entender as palavras e frases exclusivos para o aplicativo. Alguns dessas frases fará parte do aplicativo para que todos os usuários saberá e compreendê-los. Ainda outros serão exclusivos para um determinado usuário do aplicativo.

### <a name="app-specific-vocabulary"></a>Vocabulário específico do aplicativo

O vocabulário específico do aplicativo define as palavras e frases específicas que serão conhecidos para todos os usuários do aplicativo, como tipos de veículo ou nomes de treinamento. Como eles são parte do aplicativo, eles são definidos em um `AppIntentVocabulary.plist` arquivo como parte do pacote de aplicativo principal. Além disso, essas palavras e frases devem ser localizadas.

Há várias partes para um vocabulário `AppIntentVocabulary.plist` arquivo:

- **Aplicativo de exemplo usa** -eles fornecem um conjunto de casos de uso comuns para as solicitações que o usuário possa fazer do aplicativo. Por exemplo: *"Começar uma ginástica com MonkeyFit".*
- **Parâmetros** -eles fornecem um conjunto de tipos de parâmetro fora do padrão específicos para o aplicativo. Por exemplo, nomes ginástica para o aplicativo MonkeyFit. Eles consistem em:
    - **Frase** -permite que o aplicativo definir termos exclusivos para o aplicativo. Por exemplo: o tipo de treinamento "Bananarific" para o aplicativo MonkeyFit. 
    - **Pronúncia** -fornece dicas de pronúncia a Siri como uma ortografia fonética simple para uma determinada frase. Por exemplo, "ba nana ri especí".
    - **Exemplo** -fornece um exemplo de como usar a frase determinada no aplicativo. Por exemplo, *"Começar um Bananarific em MonkeyFit"*.

Para obter mais informações, consulte da Apple [referência de formato de arquivo do aplicativo vocabulário](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CustomVocabularyKeys.html#//apple_ref/doc/uid/TP40016875-CH10-SW1).

### <a name="user-specific-vocabulary"></a>Vocabulário específico do usuário

O vocabulário específico do usuário irá fornecer palavras ou frases que são exclusivos para usuários individuais do aplicativo. Eles serão fornecidos em tempo de execução do aplicativo principal (e não as extensões de aplicativo) como um conjunto ordenado de termos ordenados na prioridade de uso mais significativa para os usuários, com os termos mais importantes no início da lista.

Vejamos o exemplo do aplicativo MonkeyChat apresentado acima. MonkeyChat mantém uma lista de todos os contatos do usuário, que estará enviando a Siri via o vocabulário específico do usuário. Ele também mantém uma lista de 10 contatos mais recentes que o usuário tem da mensagem e ele tem um conjunto de contatos favorito para cada usuário. Neste exemplo, os Contatos Favoritos devem ser no início de nosso vocabulário específico do usuário, seguido de contatos recentes, em seguida, o restante dos contatos do usuário.

Os seguintes tipos de informações têm suporte pelo vocabulário específico do usuário:

- Nomes de contato.
- Nomes de treinamento.
- Nomes de álbuns de fotos.
- Palavras-chave de fotos.

Se o aplicativo utiliza o catálogo de endereços do iOS, o aplicativo não terá realizar qualquer ação, pois essa informação já estará disponível para Siri. O aplicativo só precisa fornecer nomes de contato, se o aplicativo tem seu próprio banco de dados exclusivo de contatos.

Ao criar o vocabulário, apenas fornecem os valores necessários que os usuários conhecem e importam. Evite fornecer informações como números de telefone ou endereços de email.

O aplicativo também precisa atualizar Siri imediatamente quando vocabulário específico do usuário é alterado. Os usuários estão acostumados a solicitação de informações do Siri no instante em que ele foi adicionado ao seu dispositivo iOS. Por exemplo, se o usuário adiciona um novo contato no aplicativo, envie essas informações para Siri assim que o usuário salvá-lo.

Mais importante, o aplicativo _deve_ excluir informações de vocabulário Siri imediatamente, pois um usuário pode se tornar aborrecido se eles excluído uma informação, mas Siri ainda estava reconhecendo-ele horas ou dias mais tarde.

> [!IMPORTANT]
> O aplicativo deve remover todos o vocabulário específico do usuário do Siri se o usuário optar por redefinir o aplicativo ou se eles logout.

## <a name="sirikit-permissions"></a>Permissões de SiriKit

A parte final do SiriKit é centralizada em torno de permissões. Como usar outros recursos do iOS (por exemplo, fotos, câmera ou contatos), os usuários precisam conceder permissão explícita para o aplicativo para se comunicar com o Siri.

O aplicativo é capaz de fornecer uma cadeia de caracteres que define quais informações ela fornecerá a Siri e fornecer uma razão sobre por que o usuário deve conceder esse acesso. 

A Apple sugere que o aplicativo deve solicitar permissão do usuário para usar o tempo de Siri o primeiro que usuário abre o aplicativo depois de atualizar para o iOS 10. Isso é para que os usuários conhecem sobre a integração do Siri e podem pré-aprovadas uso antes de sua primeira solicitação.

## <a name="sirikit-and-maps"></a>Mapas e SiriKit

SiriKit é parte integrante do iOS e utiliza a estrutura intenções maior adicionada ao iOS 10. A estrutura de tentativas foi projetada para compartilhar as ações comuns e compartilhadas e intenções com outras partes do sistema.

O framework intenções vai além de apenas Siri integração e fornece outros recursos, como a integração de contatos que o aplicativo pode se tornar a telefonia padrão ou um aplicativo de mensagens para contatos específicos. Intenções também fornecem integração profunda com CallKit para fornecer aos usuários a melhor experiência possível de VOIP.

O aplicativo de mapas no iOS 10 adicionou recursos como compartilhamento de corrida em que o usuário pode reservar uma jornada diretamente dentro de mapas de interface do usuário. SiriKit fornece um ponto de extensão comum com mapas, para que as intenções de compartilhamento (e outros) de corrida podem ser compartilhadas entre Siri e mapas. 

Isso significa que se o aplicativo adotou as extensões de SiriKit, ela também obterá a integração de mapas gratuitamente. 

## <a name="designing-a-great-siri-experience"></a>Criando uma ótima experiência de Siri

Projetar uma excelente experiência de usuário ao integrar um aplicativo no Siri é diferente de criação de uma interface de usuário grande aplicativo. Diferentemente normais situações em que o usuário está interagindo com o aplicativo diretamente no tela, ao usar Siri lá são muitas vezes, quando não há interface visual é visível em todos os. Por exemplo, quando o usuário iniciou a conversa com *"Ei Siri"*.

### <a name="how-siri-helps-the-developer"></a>Como o Siri ajuda o desenvolvedor

Ao criar interações do aplicativo com Siri, o aplicativo estará criando uma *Interface de conversação*, que significa que o contexto é derivado de conversa que Siri está tendo com o usuário em nome do aplicativo.

Na ausência de uma referência visual, o usuário deve controlar das informações que estão sendo apresentadas em suas cabeças. Por isso, Siri apresenta as bare informações mínimas necessárias para realizar a tarefa de usuário é que queiram realizar.

A Interface de conversação é formatada pelas perguntas e respostas do usuário e Siri durante a conversa. Portanto, é importante pensar sobre como o Siri faz perguntas e responde ao criar essa interface.

Veja o exemplo a seguir do usuário que cria uma mensagem, Siri pode responder com a pergunta, *"Pronto para enviá-lo?"* . O usuário pode responder de várias maneiras diferentes, como *"Enviar"*, *"Cancelar"* ou até mesmo algo totalmente não relacionadas a essa pergunta. Independentemente de como a conversa sejam reproduzidos, Siri será tratá-la para o aplicativo e apenas envie-as informações relevantes conforme ele se torna disponível.

Há várias maneiras diferentes que um usuário pode iniciar uma conversa com Siri:

- Selecionando o dispositivo, pressionando o botão página inicial. Nessa situação Siri apresentará interfaces visual mais e menos respostas verbais.
- Dizendo *"Ei Siri"* e iniciar uma conversa mãos livres. Nessa situação Siri será visual menor e mais verbal.
- Usando recursos de acessibilidade, como o bluetooth habilitado ajudas aguardamos seu contato em que a interface do usuário devem ser adaptado para um usuário com necessidades especiais.
- Usando a reprodução de carro onde o usuário precisa para manter a atenção concentrados na condução, mantendo o mínimo de distrações.

### <a name="how-the-developer-helps-siri"></a>Como o desenvolvedor ajuda Siri

Ao integrar um aplicativo com Siri, o desenvolvedor precisa testar essa integração com frequência e certifique-se de que eles estão fazendo muitas solicitações diferentes solicitando a mesma parte da tarefa ou informações de maneiras diferentes quantas possível.

Pois não há duas pessoas imagine semelhante, é essencial que o desenvolvedor obtém tantos testadores de beta diferentes quanto possível para ajudar a ajustar a integração do Siri. Os usuários podem solicitar informações ou fazer solicitações em maneiras do desenvolvedor nunca entanto do e esse ajuste fino pode ajudar a garantir que o grupo mais amplo de usuários tenha uma ótima experiência usando seu aplicativo com o Siri.

Em situações diferentes e ambientes de teste. Inicie as conversas com Siri em todas as maneiras possíveis garantir que essas conversas fiquem fluido e naturais. Teste em locais em que o usuário seria mais do que provável estar usando o aplicativo, como em uma academia lotada.

Certifique-se de que o aplicativo está fornecendo todas as informações que o Siri precisa representar corretamente a solicitação e o resultado ao usuário. Isso é especialmente verdadeiro ao usar Siri em uma situação de mãos livres.

### <a name="siri-design-guidelines"></a>Diretrizes de Design da Siri

Lembre-se sempre de que o Siri está tendo uma conversa com o usuário em nome do aplicativo. O desenvolvedor quer não tiver certeza de que essa conversa permanece como fluida e natural quanto possível.

Como eles fariam com qualquer conversa importante, o desenvolvedor precisa garantir que o seguinte:

- Se o aplicativo está preparado para a conversa.
- O aplicativo escuta exatamente o que o usuário está tentando realizar.
- Se o aplicativo solicita as perguntas certas a nos momentos apropriados.
- Se o aplicativo responde à solicitação com as informações que o usuário está procurando.

#### <a name="preparing-for-the-conversation"></a>Preparando para a conversa

A primeira coisa a lembrar é que os usuários do aplicativo não são exatamente como o desenvolvedor. Eles podem vir de diferentes planos de fundo, falam idiomas diferentes ou tiver necessidades especiais ao trabalhar com o aplicativo.

Além disso, uma vez que o desenvolvedor projetou e criou o aplicativo, eles profunda, precisam um conhecimento profundo do aplicativo e seu funcionamento interno e funções que um usuário típico não terá. Portanto, o desenvolvedor pode solicitar solicitação do Siri Diferentemente de um usuário normal.

É por isso é essencial ter como muitas pessoas diferentes possível interagem com o aplicativo por meio do Siri. Os usuários podem fazer solicitações do aplicativo por meio do Siri que o desenvolvedor tem nunca pensou de ou de maneiras que o desenvolvedor não foram consideradas.

#### <a name="ensure-the-app-is-a-good-listener"></a>Verifique se que o aplicativo é um bom ouvinte

O desenvolvedor precisa garantir que o aplicativo é um boa ouvinte e está recebendo as especificidades da conversa que atendem às expectativas do usuário. Mas também é possível que ele podem não ter fornecido todas as informações que o aplicativo precisa para atingir a tarefa solicitada.

Há várias maneiras que o aplicativo pode lidar com essa situação:

- **Escolher um bom padrão para o valor ausente** - por exemplo, uma aplicativo de compartilhamento de corrida pode padronizar para o local atual do usuário se ela não especificou em que eles queriam ser retiradas do.
- **Dar um Palpite** -usando informações específicas que o aplicativo tenha coletado no usuário, o aplicativo pode ser capaz de marca e o palpite sobre as informações ausentes, como preencher um número de celular ausente de informações de contato do usuário. No entanto, tome cuidado para evitar surpresas ruins, como escolher a opção mais cara, etc.
- **Prompt para mais informações** -o aplicativo pode ter Siri solicitar ao usuário o valor ausente. No entanto, a chave aqui é manter as conversas simples e direto. Os usuários serão rapidamente ficam frustrados se eles tiverem responder várias perguntas para atingir a sua solicitação.
- **Tratar normalmente errados** -o usuário pode fornecer um valor que o aplicativo não estava esperando ou que não pode manipular no contexto fornecido. Certifique-se de que o aplicativo se relaciona a essa situação para o usuário de forma que o torna claro e fácil para que eles possam corrigir.

Quando o aplicativo é apresentado com um único valor em questão, a melhor maneira de lidar com isso é ter o Siri pedir ao usuário a confirmação. Por exemplo, *"Você quis dizer Bobo excelentes?"* , que pode responder com uma simple resposta Sim ou não.

Quando há uma situação onde várias opções possíveis poderiam ser corretas para um único valor, desambiguação é o método preferencial de manipulação. Nessa situação Siri pode avisar o usuário com até dez possíveis opções à sua escolha. Por exemplo:

```csharp
Who do you want to send the message to?

* Bobo the Great!
* Bobo Jr.
* Little Bobo
```

Se ainda está em dúvida, têm Siri solicitar ao usuário para fornecer uma resposta totalmente nova e mais específica para um determinado valor.

#### <a name="request-final-confirmation"></a>Solicitar a confirmação Final

Antes do aplicativo realmente executa a tarefa para atender à solicitação do usuário, Siri verificará com a extensão de aplicativo para garantir que tudo o que está em vigor. Por exemplo, o usuário tem dinheiro suficiente em sua conta para efetuar o pagamento solicitado?

Além disso, o aplicativo precisa garantir que ele está fornecendo todas as informações possíveis para Siri para que possa apresentá-lo para o usuário e confirme se a tarefa prestes a ser executada atende às suas expectativas.

Depois que o usuário confirmou a solicitação e o aplicativo executou a ele, o aplicativo precisará novamente, certifique-se de que ele forneceu todos os resultados para Siri para que ele possa relacioná-los ao usuário.

#### <a name="responding-to-the-request"></a>Respondendo à solicitação

Siri tem várias Interfaces de usuário internas para cada um dos domínios e as ações que ele sabe sobre. No entanto, quando apropriado, o aplicativo pode fornecer uma extensão de interface do usuário personalizada intenção para enriquecer a experiência do usuário, apresentando a identidade visual do aplicativo e a interface do usuário ou obter mais informações que estava presente na solicitação.

Dito isso, moderação deve ser usada durante a criação de interfaces personalizadas para Siri. Normalmente, o usuário é quando você deseja concluir uma tarefa específica mais rápido possível e não quer ser sobrecarregado com informações desnecessárias.

É também deve ter cuidado para garantir que a interface do usuário personalizada procura e responde corretamente em todos os dispositivos iOS diferentes e orientações que o usuário pode ter ou ser usando o dispositivo.

Quando apropriado, use a API de SiriKit para ocultar todas as informações redundantes já está presentes no padrão UI Siri. Ainda, certifique-se de que o aplicativo está ainda fornecendo as informações a Siri para que ele pode apresentá-lo verbalmente um situações mãos livres.

Pode haver situações em que o Siri iniciará o aplicativo para atender à solicitação do usuário, como apresentar as fotos que o usuário solicitou. Nessas situações, não se surpreenda ao usuário. Exiba as informações esperadas sem a necessidade de etapas intermediárias ou interação adicional necessária. Nunca exibir informações ou executar uma tarefa que o usuário não está esperando.

### <a name="polishing-the-design"></a>Acabamento o Design

Há várias etapas que Apple sugerem para aprimorar o design das Interfaces de conversação. Primeiro, é fornecendo claras e concisos vocabulário e use exemplos de caso para Siri.

Uma das maneiras que um usuário descobre o aplicativo está iniciando uma conversa com Siri e perguntando, *"O que você pode fazer?"* Siri mostrará várias coisas podem fazer, incluindo o aplicativo do desenvolvedor e o herói de exemplo casos de uso que ele tenha fornecido por meio de seu `plist` arquivo.

Como escrever bons casos de uso:

- Certifique-se de que os exemplos incluem o nome do aplicativo.
- Manter o exemplo curto e a ponto.
- Fornecem vários exemplos para cada uma das intenções compatível no aplicativo.
- Priorize as tentativas e os exemplos dentro deles com base nos casos de uso mais comuns para o aplicativo.
- Certifique-se de que o aplicativo fornece exemplos localizados.
- Certifique-se de que cada dado funciona conforme o esperado no aplicativo de exemplo.
- Evitar Siri de endereçamento nos exemplos, portanto, não inclua o texto, como *"Ei Siri..."*
- Evitar qualquer pleasantries desnecessários, como *","* ou *"thank you"*.

Reserve um tempo apropriado para exploração e experimentação em como o aplicativo pode formatar a conversa que Siri está tendo com o usuário em seu nome. Certifique-se de falar com os usuários comuns em todo o processo, como suas interações com e expectativas do aplicativo podem mudar ao longo do tempo.

Sempre Lembre-se de testar o aplicativo em situações diferentes e todos os diferentes métodos para invocar uma conversa com Siri. Teste em locais do mundo real do usuário pode usar o aplicativo, para longe do escritório e o suporte técnico.

Se esforçar para ter as conversas com Siri (em nome do aplicativo) a ser fluido, natural e "sentir certa". 

## <a name="summary"></a>Resumo

Este artigo abordou os principais conceitos necessários para usar o SiriKit e mostra que ele pode interagir com os aplicativos xamarin. IOS para fornecer serviços que são acessíveis para o usuário usando o Siri e o aplicativo de mapas em um dispositivo iOS.




## <a name="related-links"></a>Links relacionados

- [Exemplo de ElizaChat](https://developer.xamarin.com/samples/monotouch/ios10/ElizaChat/)
- [Guia de programação de SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Referência de estrutura de tentativas](https://developer.apple.com/reference/intents)
- [Referência de estrutura de interface do usuário de tentativas](https://developer.apple.com/reference/intentsui)
