---
title: "Noções básicas sobre os conceitos de SiriKit"
description: "Este artigo aborda os principais conceitos que serão necessários para trabalhar com SiriKit em um aplicativo xamarin."
ms.topic: article
ms.prod: xamarin
ms.assetid: 99EC5C1E-484F-4371-8555-58C9F60DE37F
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 36d8e27ce06e38e1cf652558bfb5a83f572e4403
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="understanding-sirikit-concepts"></a>Noções básicas sobre os conceitos de SiriKit

_Este artigo aborda os principais conceitos que serão necessários para trabalhar com SiriKit em um aplicativo xamarin._


Novo para iOS 10, SiriKit permite que um aplicativo xamarin fornecer os serviços que são acessíveis para o usuário usando o Siri e o aplicativo de mapas em um dispositivo iOS. Essa funcionalidade é fornecida em um ou mais da extensão do aplicativo usando o novo **tentativas** e **propósitos de interface do usuário** estruturas.

SiriKit permite que um aplicativo iOS fornecem serviços que são acessíveis para o usuário usando o Siri e o aplicativo de mapas em um dispositivo iOS usando extensões de aplicativo e o novo **tentativas** e **propósitos de interface do usuário** estruturas.

Siri funciona com o conceito de **domínios**, saber de grupos de ações para tarefas relacionadas. Cada interação com o aplicativo com Siri deve estar em um de seus domínios de serviço conhecido da seguinte maneira:

- Áudio ou vídeo chamada.
- Reserva uma jornada.
- Gerenciando exercícios.
- Sistema de mensagens.
- Pesquisando fotos.
- Enviar ou receber pagamentos.

Quando o usuário faz uma solicitação de Siri que envolvem um dos serviços de extensão do aplicativo, SiriKit envia a extensão de um **intenção** objeto que descreve a solicitação do usuário junto com os dados de suporte. A extensão do aplicativo, em seguida, gera apropriada **resposta** de objeto para o determinado **intenção**, detalhando como a extensão pode lidar com a solicitação.

## <a name="the-intents-and-intents-ui-extensions"></a>As extensões de interface do usuário de tentativas e tentativas

Siri e o aplicativo de mapas interagem com os serviços do aplicativo por meio de dois tipos diferentes de extensões de aplicativo:

- **Extensão de tentativas** -Siri fornece e mapas com o aplicativo de conteúdo e executa as tarefas necessárias para cumprir qualquer tentativas com suporte.
- **Extensão de interface do usuário de tentativas** -fornece uma interface do usuário personalizada que será exibido para o conteúdo do aplicativo dentro de Siri ou mapas.

O aplicativo deve fornecer uma extensão de tentativas para dar suporte a SiriKit e é responsável por fornecer informações Siri e mapas podem apresentar ao usuário e para lidar com propósitos.

Criar uma extensão de interface do usuário de tentativas é opcional, desde que o Siri normalmente manipula todas as interações do usuário e tem uma interface de usuário padrão e interno para apresentar informações em cada um dos domínios com suporte. Ao fornecer uma extensão de interface do usuário de tentativas, o aplicativo pode usar o **intenção de interface do usuário** framework para apresentar um personalizado, Avançado, Interface do usuário apresentando o aplicativo da identidade visual e informações adicionais.

## <a name="siri-and-the-maps-app-role"></a>Siri e a função de aplicativo de mapas

As solicitações do usuário falada são processados e analisados semanticamente por Siri que transforma essas solicitações em tentativas acionáveis que podem controlar as extensões de intenção de idioma.

Maps usa extensões de intenção do aplicativo para exibir informações na interface do mapa em resposta a ações do usuário. Como solicitar próximos restaurantes ou Obtendo revisões de restaurante do aplicativo.

Siri e mapas gerenciam todas as interações do usuário e exibem os resultados usando a interface padrão do sistema. A função de extensões de aplicativo é principalmente fornecer os dados que são exibidos. Opcionalmente, o aplicativo pode fornecer uma extensão de interface do usuário de tentativas e apresentar uma interface do usuário personalizada para aprimorar a interface do sistema padrão.

## <a name="interacting-with-siri-via-sirikit"></a>Interagir com Siri Via SiriKit

Esta seção apresentará uma visão geral de como SiriKit permite que o usuário interaja com o aplicativo usando o Siri. Para este exemplo, usaremos o aplicativo MonkeyChat falso:

[ ![](understanding-sirikit-images/monkeychat01.png "O ícone de MonkeyChat")](understanding-sirikit-images/monkeychat01.png)

MonkeyChat mantém seu próprio catálogo de contato de amigos do usuário, cada um associado a um nome de tela (como Bobo, por exemplo) e permite que o usuário enviar texto chats para cada friend por seu nome de tela.

Há muitas maneiras que o usuário pode iniciar uma interação com o aplicativo, já que diferentes pessoas podem tornar a mesma solicitação de diversas formas.

Por exemplo, se o usuário deseja enviar uma mensagem para seus amigos Bobo, eles podem ter a seguinte conversa com Siri:

<table width="100%" border="1px">
<tr>
    <td width="50%"><b>Siri</b></td>
    <td width="50%"><b>Usuário</b></td>
</tr>
<tr>
    <td></td>
    <td>"Ei Siri, enviar uma mensagem de MonkeyChat"</td>
</tr>
<tr>
    <td>"A quem?"</td>
    <td></td>
</tr>
<tr>
    <td></td>
    <td>"Bobo"</td>
</tr>
<tr>
    <td>"O que você deseja dizer ao Bobo?"</td>
    <td></td>
</tr>
<tr>
    <td></td>
    <td>"Envie bananas mais"</td>
</tr>
</table>

Outra pessoa pode fazer a solicitação mesmo com uma conversa diferente:

<table width="100%" border="1px">
<tr>
    <td width="50%"><b>Siri</b></td>
    <td width="50%"><b>Usuário</b></td>
</tr>
<tr>
    <td></td>
    <td>"Enviar uma mensagem para Bobo em MonkeyChat"</td>
</tr>
<tr>
    <td>"O que você deseja dizer ao Bobo?"</td>
    <td></td>
</tr>
<tr>
    <td></td>
    <td>"Envie bananas mais"</td>
</tr>
</table>

E outro usuário pode fazer uma solicitação ainda mais curta:

<table width="100%" border="1px">
<tr>
    <td width="50%"><b>Siri</b></td>
    <td width="50%"><b>Usuário</b></td>
</tr>
<tr>
    <td></td>
    <td>"MonkeyChat Bobo envie bananas mais"</td>
</tr>
<tr>
    <td>"Okey, enviando mensagem envie bananas mais para Bobo em Monkeychat"</td>
    <td></td>
</tr>
</table>

Ou, até mesmo fazer a mesma solicitação em um idioma diferente:

<table width="100%" border="1px">
<tr>
    <td width="50%"><b>Siri</b></td>
    <td width="50%"><b>Usuário</b></td>
</tr>
<tr>
    <td></td>
    <td>"MonkeyChat Bobo s'il vous plaît envoyer além de bananes"</td>
</tr>
<tr>
    <td>"Oui, envoyer de plaît envoi mensagem s'il vous mais de bananes à Bobo sur Monkeychat"</td>
    <td></td>
</tr>
</table>

Outro usuário ainda pode ser muito detalhado em seu conversa:

<table width="100%" border="1px">
<tr>
    <td width="50%"><b>Siri</b></td>
    <td width="50%"><b>Usuário</b></td>
</tr>
<tr>
    <td></td>
    <td>"Ei Siri, poderá não me um favor e inicie o aplicativo MonkeyChat para enviar um texto com a mensagem, envie bananas mais"</td>
</tr>
<tr>
    <td>"A quem?"</td>
    <td></td>
</tr>
<tr>
    <td></td>
    <td>"Meu pal de melhor Bobo"</td>
</tr>
</table>

Além disso, há muitas maneiras Siri pode responder a uma solicitação, alguns com base em como a solicitação foi feita:

- **Mantendo o botão início** -Siri fornecerá mais respostas visual com comentários textual limitado.
- **Por "Ei Siri"** -Siri será mais textual e fornecer respostas visual menos.

Siri também está ajustado para atender às necessidades de acessibilidade do usuário e de interagir e responder com essas necessidades.

Independentemente de como é feita uma solicitação ou como o Siri responde à solicitação, Siri trata a conversa com o usuário e o aplicativo (por meio de suas extensões) fornece a funcionalidade.

Quando o usuário faz uma solicitação textual do Siri, estas são as etapas seguidas Siri:

[ ![](understanding-sirikit-images/monkeychat02.png "As etapas seguidas Siri")](understanding-sirikit-images/monkeychat02.png)

1. Primeiro, Siri leva o áudio do usuário **fala** e a converte em texto.
2. Em seguida, o texto é convertido em um **intenção**, estruturado de representação de solicitação do usuário.
3. Com base na intenção, Siri levará **ação** para executar a solicitação do usuário.
4. Por fim, apresentará Siri **respostas** (visual e textual) para o usuário com base na ação tomada.

Há três maneiras principais que o aplicativo pode fazer parte de conversa do usuário com Siri:

[ ![](understanding-sirikit-images/monkeychat03.png "Os três modos principais que o aplicativo pode fazer parte de conversa usuários com Siri")](understanding-sirikit-images/monkeychat03.png)

1. **Vocabulário** -isso é como o aplicativo informa Siri as palavras que ele precisa saber para interagir com ela.
2. **Lógica de aplicativo** - são as ações e respostas que tenham o aplicativo com base em determinadas tentativas.
3. **Interface do usuário** -esta é a interface de usuário personalizada opcional que o aplicativo pode fornecer as respostas em.

### <a name="example"></a>Exemplo

Considerando as informações acima, examine como a seguir conversa interagiria com o aplicativo MonkeyChat:

<table width="100%" border="1px">
<tr>
    <td width="50%"><b>Siri</b></td>
    <td width="50%"><b>Usuário</b></td>
</tr>
<tr>
    <td></td>
    <td>"Ei Siri, enviar uma mensagem para Bobo em MonkeyChat"</td>
</tr>
<tr>
    <td>"O que você deseja dizer ao Bobo?"</td>
    <td></td>
</tr>
<tr>
    <td></td>
    <td>"Envie bananas mais"</td>
</tr>
</table>

A primeira função em que o aplicativo recebe a conversa é Siri compreender voz do usuário:

[ ![](understanding-sirikit-images/monkeychat04.png "Ajudando Siri entender a fala de usuários")](understanding-sirikit-images/monkeychat04.png)

Siri não tem o nome "Bobo" em seu banco de dados, mas o aplicativo e compartilha essas informações com Siri por meio de seu vocabulário. O aplicativo também ajuda Siri reconhecer que Bobo é um destinatário, pois ele especificado para Siri como um *entre em contato com*.

Siri sabe o que mais é necessário para enviar uma mensagem que um destinatário, para verificar rapidamente com a extensão do aplicativo para ver se uma mensagem requer conteúdo. Como MonkeyChat faz, o usuário com a pergunta responderá Siri: *"O que você deseja dizer ao Bobo?"*

No exemplo acima, o usuário respondeu, *"Envie bananas mais"*, que o Siri será agrupar em um estruturado **intenção**:

[ ![](understanding-sirikit-images/monkeychat05.png "Siri unirá a resposta do usuário em uma tentativa de estruturado")](understanding-sirikit-images/monkeychat05.png)

A intenção estruturada conterá as seguintes informações:

- **Domínio:** mensagens
- **Propósito:** sendMessage
- **Destinatário:** Bobo
- **Conteúdo:** envie bananas mais

Cada domínio tem como conjunto de saber *ações* que podem ser executadas neles e com base no domínio e a ação, zero para muitos parâmetros podem ser incluídos na intenção é enviada ao aplicativo.

A intenção é enviada para a extensão do aplicativo para processamento. Como resultado do processamento a intenção, o aplicativo irá gerar um **IntentResponse** que serão incluídos com a intenção e incluir parâmetros que descrevem o que fez o aplicativo do aplicativo com a intenção.

Cada IntentResponse também incluirá um **código de resposta** que informa que o Siri se o aplicativo foi capaz de concluir a solicitação ou não. Alguns domínios tem códigos de resposta de erro muito específicas que podem ser enviados também.

Por fim, o IntentResponse incluirá um `NSUserActivity` (como aqueles usados para dar suporte a mão Off). O `NSUserActivity` será usado para iniciar o aplicativo se a resposta precisa deles para deixar o ambiente Siri e insira o aplicativo para ser concluída. 

Siri automaticamente criará um apropriado `NSUserActivity` para iniciar o aplicativo e o recebimento de onde o usuário parou no ambiente Siri. No entanto, o aplicativo pode fornecer sua própria `NSUserActivity` com informações personalizadas, se necessário.

Depois que o aplicativo tiver processado a intenção e retornou uma resposta para Siri, em seguida, apresenta os resultados para o usuário (verbalmente e visualmente):

[ ![](understanding-sirikit-images/monkeychat06.png "Os resultados apresentados ao usuário verbalmente e visualmente")](understanding-sirikit-images/monkeychat06.png)

Siri tem resposta interna várias Interfaces de usuário para cada um dos domínios disponíveis para o aplicativo. No entanto, como MonkeyChat forneceu uma extensão de interface do usuário intenção opcional, ele é usado para apresentar os resultados da conversa para o usuário no exemplo acima.

## <a name="the-intent-lifecycle"></a>O ciclo de vida intencional

Há três tarefas principais que a extensão do aplicativo precisa executar ao lidar com propósitos:

[ ![](understanding-sirikit-images/monkeychat07.png "O ciclo de vida intencional")](understanding-sirikit-images/monkeychat07.png)

1. O aplicativo deve **resolver** cada parâmetro em um evento. Como resultado, o aplicativo chamará resolva várias vezes (uma vez por cada parâmetro) e, às vezes, várias vezes no mesmo parâmetro até que o aplicativo e o usuário concordarem com o que está sendo solicitado.
2. O aplicativo deve **confirmar** que pode lidar com a intenção solicitada e informar Siri sobre o resultado esperado.
3. Por fim, o aplicativo deve **tratar** a intenção e execute as etapas para obter o resultado solicitado.

### <a name="the-resolve-stage"></a>O estágio de resolução

O estágio de resolução ajuda Siri a entender os valores que o usuário forneceu e garante que, na verdade, significa que o usuário é o que acontece quando a intenção é processada pelo aplicativo. 

Neste estágio também fornece uma oportunidade para o aplicativo influenciar o comportamento do Siri durante a conversa com o usuário. Para fazer isso, o aplicativo fornecerá uma **resposta resolução**. Há um número de resposta predefinido para os diferentes tipos de dados que reconheça o Siri.

O mais comum a resposta de resolução do aplicativo será **êxito**, que significa que o aplicativo correspondeu a parte de dados específica de um parâmetro (como nome de tela do usuário) para uma parte das informações que ele conheça.

Pode haver momentos em que o aplicativo precisa para confirmar se uma determinada solicitação corresponde a informação correta que ele sabe sobre. Nesses casos, ele enviará um **ConfirmationRequired** resposta para fazer uma pergunta Sim ou não para o usuário, como *"Enviar mensagem para Bobo excelentes?"*

Pode haver outros casos em que o aplicativo exige que o usuário escolher dentre uma lista curta de opções. Nesse caso, o aplicativo fornecerá uma **ambiguidades** resposta com uma lista de opções de dois a dez para o usuário à sua escolha, como: 

```csharp
Who do you want to message?

* Bobo the Great
* Bobo Jr.
* Little Bobo
```

Siri tratará o usuário fazer a seleção, verbalmente ou interagindo com o Siri UI, e o resultado será enviado de volta para o aplicativo.

Em outros casos, pode não haver informações suficientes para o aplicativo resolver o parâmetro ou pode haver muitas correspondências para resolver usando ambiguidades (como 80 usuários com Bobo em seu nome). Nesses casos, o aplicativo enviará um **NeedsMoreDetails** resposta e Siri solicitará ao usuário para ser mais específico.

Se o usuário não fornecer um valor que é necessário para que ele a intenção do processo, ele pode enviar um **NeedsValue** resposta ter Siri solicitar ao usuário para o valor.

Se o aplicativo não dá suporte a um valor que o usuário tenha dado para um parâmetro específico, que pode enviar o **UnsupportedWithReason** resposta para fornecer um motivo por que o valor não tem suporte. Siri, em seguida, solicitará ao usuário para um valor completamente novo e fornecido-los a razão é necessária.

Por fim de usar o **NotRequired** resposta para informar que o Siri que o aplicativo não requer um valor para um determinado parâmetro. Se o usuário fornece um mesmo assim, ele simplesmente será ignorado pelo Siri.

### <a name="the-confirm-stage"></a>A fase de confirmação

O estágio de confirmar tem duas finalidades:

- Para informar Siri o resultado esperado de lidar com um propósito de forma que o Siri pode informar ao usuário o que vai acontecer.
- Fornece uma verificação de oportunidade todos os estados necessários o aplicativo pode precisar concluir a solicitação apresentada pelo usuário, como tendo dinheiro suficiente no banco para efetuar o pagamento solicitado.

O aplicativo fornecerá uma **resposta intenção** da etapa de confirmação, que deve ser populada com informações do aplicativo tiver disponíveis para Siri possa se comunicá-lo efetivamente com o usuário.

Com base no tipo de ação e o domínio, Siri pode solicitar a confirmação do usuário para, como a antes de enviar um pagamento ou uma jornada de reserva.

### <a name="the-handle-stage"></a>O estágio de identificador

O estágio de lidar com é a parte mais importante de trabalhar com uma intenção porque ele é o ponto em que o aplicativo atende a solicitação do usuário executando a tarefa que foi solicitado a fazer.

Exatamente como fazia no estágio de confirmação, o aplicativo precisa fornecer o máximo possível de informações sobre o resultado para que Siri pode se relacionar isso ao usuário. Às vezes, essas informações serão apresentadas visualmente ou outras ocasiões, Siri simplesmente irá falá-la volta para o usuário. 

Pode haver vezes quando o aplicativo pode exigir mais tempo para processar uma determinada solicitação, como atrasos de chamada de rede ou se precisar de uma pessoa ao vivo atender à solicitação (como concluir e enviar um pedido ou dirigir um carro para o local do usuário). Quando Siri está aguardando uma resposta do aplicativo, ele exibirá uma interface de usuário de espera para o usuário informando que o aplicativo está processando a solicitação.

Idealmente, o aplicativo deve fornecer uma resposta a Siri dentro de duas a três segundos no máximo. Se o aplicativo souber que uma determinada resposta vai levar mais tempo para processar, ele precisa enviar um **em andamento** código de resposta para o Siri. Siri, em seguida, informará ao usuário que o aplicativo está processando a solicitação em segundo plano e continuará a fazer isso, mesmo se ele deixar o ambiente Siri.

## <a name="adding-sirikit-to-the-app"></a>Adicionando SiriKit ao aplicativo

Com SiriKit no iOS 10, a Apple criou dois novos pontos de extensão:

- **Extensão de tentativas** - fornece Siri com o conteúdo do aplicativo e executa as tarefas necessárias para cumprir qualquer tentativas com suporte.
- **Extensão de interface do usuário de tentativas** -fornece uma interface do usuário personalizada que será exibido para o conteúdo de aplicativos dentro de Siri. 

Também é uma API para fornecer palavras e frases para Siri para auxiliar no reconhecimento na forma de:

- **Aplicativo vocabulário** -palavras e frases que são comuns a todos os usuários do aplicativo.
- **Usuário vocabulário** -palavras e frases que são exclusivos para um usuário de determinado aplicativo.

## <a name="the-intents-extension"></a>A extensão de tentativas

A extensão de tentativas é responsável por gerenciar as principais interações entre o aplicativo e o Siri da seguinte maneira:

[ ![](understanding-sirikit-images/intents01.png "A extensão de tentativas")](understanding-sirikit-images/intents01.png)

A extensão de intenção pode dar suporte a um ou mais tentativas, é responsabilidade do desenvolvedor para decidir como desejam implementar SiriKit no aplicativo. O desenvolvedor também pode adicionar uma extensão de intenção separado para cada tentativa de precisar ser manipulada.  Dito isso, que o desenvolvedor de limitar o número de intenção de extensões para que o Siri não tem vários processos abrir contra o aplicativo que exige mais memória e tempo para tratar as solicitações de Apple.

O desenvolvedor deve estar ciente de que a extensão de intenção de ser executado em segundo plano enquanto Siri está ativa. Isso permite que o Siri transportar ativamente em uma conversa com o usuário enquanto ainda se comunicar com a extensão ao processar as informações sobre a solicitação.

## <a name="privacy-and-security-considerations"></a>Considerações sobre segurança e privacidade

Apple obteve grandes medidas para garantir que um usuário particular informações são seguras ao trabalhar com Siri e como tal, existem várias interações que exigem que o usuário faça logon dispositivo iOS. Por exemplo, ao solicitar uma jornada ou pagamento.

Além disso, há comportamentos específicos que o aplicativo pode querer limitar para o usuário que está sendo conectado ao dispositivo. Nessas situações, o aplicativo pode solicitar o **restringir enquanto bloqueado** comportamento. Isso é feito por meio de uma configuração de `Info.plist` arquivo.

A estrutura de autenticação Local está disponível para a intenção de extensão para que o aplicativo pode pedir ao usuário para obter informações de autenticação adicional, mesmo se o dispositivo já está desbloqueado.

Por fim, pagamento da Apple está disponível para a intenção de extensão para o aplicativo poderá concluir uma transação usando o pagamento da Apple e a folha de pagamento da Apple interna será exibida acima da interface Siri.

Além disso, a Apple deseja certificar-se de que os usuários sabe quando eles estão enviando informações para um aplicativo de terceiros 3º e como tal, o usuário **deve** diga o nome específico do aplicativo (conforme especificado no nome de exibição do pacote do aplicativo) ao fazer uma solicitação.

Apple projetou Siri realizarem naturais, fluidas conversas com o usuário e, por isso, o nome do pacote do aplicativo podem ser usada em muitas partes da fala, onde ele se encaixa naturalmente na solicitação do usuário.

Uma das tarefas mais comuns que os usuários fará é "verbify" nome do aplicativo, em outras palavras, levando o nome do aplicativo e usá-lo como um verbo em uma solicitação. Por exemplo, *"MonkeyChat Bobo esses foram bananas excelentes."*

## <a name="the-intents-ui-extension"></a>A extensão de interface do usuário de tentativas

A extensão de interface do usuário de tentativas apresenta a oportunidade de exibir a interface do usuário do aplicativo e identidade visual na experiência de Siri e fazer com que os usuários se sentir conectados ao aplicativo. Com essa extensão, o aplicativo pode colocar a marca, bem como visual e outras informações para a transcrição.

[ ![](understanding-sirikit-images/intents02.png "Exemplo de saída de extensão de tentativas de IU")](understanding-sirikit-images/intents02.png)

A extensão de interface do usuário de tentativas sempre retornará um `UIViewController` e o aplicativo pode adicionar qualquer coisa que desejar dentro do controlador de exibição como mostrando informações adicionais que ultrapassa a resposta inicial. Tentativas de interface do usuário também pode atualizar o usuário com o status de um evento de execução longa, como quanto tempo mais levará a uma jornada carro para alcançar o local de compartilhamento.

A extensão de interface do usuário de tentativas será sempre exibida juntamente com outro conteúdo Siri como o ícone do aplicativo e o nome na parte superior da interface do usuário ou, com base na intenção, botões (como enviar ou Cancelar) podem ser exibidos na parte inferior.

Há alguns casos em que o aplicativo pode substituir as informações que Siri exibe para o usuário por padrão, como mensagens ou onde o aplicativo pode substituir a experiência de padrão com um personalizado para o aplicativo é mapeado.

> [!IMPORTANT]
> **Observação:** enquanto é possível adicionar elementos interativos, como `UIButtons` ou `UITextFields` para a extensão de interface do usuário de intenção `UIViewController`, esses são estritamente proibidos como a UI de intenção de não interativo e o usuário não conseguirá interagir com eles.

É totalmente opcional para o aplicativo fornecer uma extensão de interface do usuário intenção desde que o Siri contém um conjunto padrão de interface do usuário para cada tipo de intenção. Além disso, as interfaces de tentativas da interface do usuário estão disponíveis apenas para certos propósitos que Apple foi considerado seria útil para o usuário.

## <a name="adding-sirikit-vocabulary"></a>Adicionando SiriKit vocabulário

A parte final do implementando SiriKit se encontra dentro do aplicativo, fornecendo o vocabulário necessário. Muitos aplicativos têm maneiras exclusivas de descrever as informações para o usuário e que o usuário fornecerá informações para o aplicativo de maneiras exclusivas.

Por isso, Siri requer assistência do aplicativo para entender as palavras e frases exclusivos para o aplicativo. Alguns desses frases farão parte do aplicativo para que cada usuário saberá e compreendê-los. Ainda outros serão exclusivos para um determinado usuário do aplicativo.

### <a name="app-specific-vocabulary"></a>Vocabulário específico do aplicativo

O vocabulário específico de aplicativo define as palavras e frases específicas que serão conhecidas para todos os usuários do aplicativo, como tipos de veículo ou nomes de treinamento. Como esses são parte do aplicativo, elas são definidas em um `AppIntentVocabulary.plist` arquivo como parte do pacote de aplicativo principal. Além disso, essas palavras e frases devem ser localizados.

Há várias partes de um vocabulário `AppIntentVocabulary.plist` arquivo:

- **Aplicativo de exemplo usa** -elas fornecem um conjunto de casos de uso comuns para as solicitações que o usuário pode fazer do aplicativo. Por exemplo: *"Iniciar um treinamento com MonkeyFit".*
- **Parâmetros** -elas fornecem um conjunto de tipos de parâmetro não padrão específicos para o aplicativo. Por exemplo, nomes treinamento para o aplicativo MonkeyFit. Eles consistem em:
    - **Frase** -permite que o aplicativo definir termos exclusivos para o aplicativo. Por exemplo: o tipo de treinamento "Bananarific" para o aplicativo MonkeyFit. 
    - **Pronúncia** -fornece dicas de pronúncia para Siri como uma ortografia fonética simple para uma determinada frase. Por exemplo, "ba nana ri ficos".
    - **Exemplo** -fornece um exemplo de como usar a frase determinada no aplicativo. Por exemplo, *"Iniciar um Bananarific em MonkeyFit"*.

Para obter mais informações, consulte da Apple [referência de formato de arquivo do aplicativo vocabulário](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CustomVocabularyKeys.html#//apple_ref/doc/uid/TP40016875-CH10-SW1).

### <a name="user-specific-vocabulary"></a>Vocabulário específico do usuário

O vocabulário específico do usuário irá fornecer palavras ou frases que são exclusivos para usuários individuais do aplicativo. Essas serão fornecidas em tempo de execução do aplicativo principal (e não as extensões de aplicativo) como um conjunto ordenado de termos em uma prioridade de uso mais significativa para os usuários, com os termos mais importantes no início da lista.

Veja o exemplo de aplicativo MonkeyChat apresentado a seguir. MonkeyChat mantém uma lista de todos os contatos do usuário, ele enviará para Siri via o vocabulário específico do usuário. Isso também mantém uma lista dos 10 contatos mais recentes que o usuário tem mensagem e tem um conjunto de contatos favorito para cada usuário. Neste exemplo, os Contatos Favoritos devem ser no início de nosso vocabulário específico de usuário, seguido pelos contatos recentes, em seguida, o restante dos contatos do usuário.

Os seguintes tipos de informações são suportados pelo vocabulário específico do usuário:

- Nome de contato.
- Nomes de treinamento.
- Nomes de álbuns de fotos.
- Palavras-chave a foto.

Se o aplicativo utiliza o catálogo de endereços do iOS, o aplicativo não terá qualquer ação, como essas informações já estão disponíveis para Siri. O aplicativo só precisa fornecer nomes de contato se o aplicativo tem seu próprio banco de dados exclusivo de contatos.

Ao projetar o vocabulário, fornece somente os valores necessários que os usuários conhece e importam. Evite fornecer informações como números de telefone ou endereços de email.

O aplicativo também precisa atualizar Siri imediatamente quando vocabulário específico do usuário é alterado. Os usuários estão acostumados a solicitação de informações do Siri no instante em que ele foi adicionado ao seu dispositivo iOS. Por exemplo, se o usuário adiciona um novo contato no aplicativo, envie essas informações para Siri assim que o usuário salvá-lo.

Além disso, o aplicativo _deve_ excluir informações de vocabulário Siri imediatamente desde que um usuário pode se tornar upset se eles excluído uma informação, mas Siri ainda estava reconhecendo-ele horas ou dias mais tarde.

> [!IMPORTANT]
> **Observação:** o aplicativo deve remover completamente o vocabulário específico do usuário do Siri se o usuário optar por redefinir o aplicativo ou se eles logout.

## <a name="sirikit-permissions"></a>Permissões de SiriKit

A parte final do SiriKit gira em torno de permissões. Assim como com outros recursos do iOS (como fotos, câmera ou contatos), os usuários têm que conceder permissão explícita para o aplicativo para se comunicar com o Siri.

O aplicativo é capaz de fornecer uma cadeia de caracteres que define quais informações ele fornecer a Siri e fornecer um motivo sobre por que o usuário deve conceder esse acesso. 

Apple sugere que o aplicativo deve solicitar permissão do usuário para usar Siri na primeira vez que o usuário abre o aplicativo depois de atualizar para o iOS 10. Isso é para que usuários saber sobre a integração de Siri e podem pré-aprovados uso antes que eles tomem sua primeira solicitação.

## <a name="sirikit-and-maps"></a>SiriKit e mapas

SiriKit é uma parte integrante do iOS e usa a estrutura de tentativas maior adicionada ao iOS 10. A estrutura de tentativas foi projetada para compartilhar ações comuns e compartilhadas e tentativas com outras partes do sistema.

A estrutura de tentativas ultrapassa apenas Siri integração e fornece outros recursos como a integração de contatos em que o aplicativo pode se tornar o telefonia padrão ou o aplicativo de mensagens para contatos específicos. Tentativas também fornecem integração profunda com CallKit para fornecer aos usuários a melhor experiência possível de VOIP.

O aplicativo de mapas em iOS 10 adicionou recursos como jornada de compartilhamento em que o usuário pode agendar uma jornada diretamente dentro de mapas de interface do usuário. SiriKit fornece um ponto de extensão comum com mapas para que propósitos jornada de compartilhamento (e outros) podem ser compartilhados entre Siri e mapas. 

Isso significa que se o aplicativo adotou as extensões SiriKit, ele também receberá a integração de mapas gratuitamente. 

## <a name="designing-a-great-siri-experience"></a>Criando uma ótima experiência de Siri

Criar uma excelente experiência de usuário ao integrar um aplicativo Siri é diferente de projetar uma interface de usuário de aplicativo grande. Diferentemente normais situações em que o usuário está interagindo com o aplicativo diretamente no tela, ao usar Siri existe são muitas vezes, quando nenhuma interface visual é visível em todos os. Por exemplo, quando o usuário iniciou a conversa com *"Ei Siri"*.

### <a name="how-siri-helps-the-developer"></a>Como o Siri Ajuda do desenvolvedor

Ao criar interações do aplicativo com Siri, o aplicativo estará criando uma *conversacional Interface*, que significa que o contexto é derivado da conversa que está tendo Siri com o usuário em nome do aplicativo.

Na ausência de uma referência visual, o usuário deve controlar das informações que estão sendo apresentadas em seu cabeçalho. Por isso, Siri apresenta as bare mínimo informações necessárias para realizar a tarefa do usuário deseja realizar.

A Interface de conversação é modelada pelas perguntas e respostas do usuário e Siri durante a conversa. Portanto, é importante considerar como o Siri faz perguntas e responde ao criar esta interface.

Veja o exemplo a seguir do usuário que cria uma mensagem Siri pode responder com a pergunta, *"Pronto para enviá-lo?"* . O usuário pode responder de várias maneiras diferentes, como *"Enviar"*, *"Cancelar"* ou até mesmo algo totalmente não relacionados a essa pergunta. Não importa como continua a conversa, Siri tratá-la para o aplicativo e somente enviá-lo as informações relevantes quando estiver disponível.

Há várias maneiras diferentes que um usuário pode iniciar uma conversa com Siri:

- Selecionando o dispositivo, pressionando o botão de início. Nessa situação Siri apresentará interfaces visual mais e menos respostas textual.
- Dizendo *"Ei Siri"* e iniciar uma conversa mãos livre. Nessa situação Siri será visual menor e mais textual.
- Usando recursos de acessibilidade, como bluetooth habilitado auxílios de audição onde a interface do usuário devem ser adaptado para um usuário com necessidades especiais.
- Usar o carro reproduzir em que o usuário precisa manter atenção cujo objetivo é definir mantendo o mínimo de distrações.

### <a name="how-the-developer-helps-siri"></a>Como o desenvolvedor ajuda Siri

Ao integrar um aplicativo com Siri, o desenvolvedor precisa testar essa integração com frequência e certifique-se de que elas fazem muitas solicitações diferentes solicitando a mesma parte de tarefa ou informações de várias maneiras diferentes possíveis.

Desde que nenhum duas pessoas pense da mesma forma, é essencial que o desenvolvedor obtém tantos testadores de beta diferentes possíveis para ajudar a ajustar a integração de Siri. Os usuários podem solicitar informações ou fazer solicitações de maneiras o desenvolvedor nunca apesar do e esse ajuste fino pode ajudar a garantir que o grupo mais amplo de usuários tenha uma ótima experiência usando o seu aplicativo com o Siri.

Em situações diferentes e ambientes de teste. Inicie conversas com Siri em todas as maneiras possíveis garantir que essas conversas permanecem fluido e naturais. Testar em locais onde o usuário provavelmente usarão o aplicativo, como em uma academia cheia.

Certifique-se de que o aplicativo está fornecendo todas as informações que o Siri precisa para representar corretamente a solicitação e o resultado para o usuário. Isso é especialmente verdadeiro ao usar Siri em uma situação mãos livre.

### <a name="siri-design-guidelines"></a>Diretrizes de Design de Siri

Sempre Lembre-se de que o Siri é ter uma conversa com o usuário em nome do aplicativo. O desenvolvedor quer certo de que essa conversa permanece como fluido e natural quanto possível.

Como eles fariam com qualquer conversa importante, o desenvolvedor precisa garantir o seguinte:

- Se o aplicativo está preparado para a conversa.
- O aplicativo de escuta exatamente o que o usuário está tentando realizar.
- Se o aplicativo solicita as perguntas certas nos momentos apropriados.
- Se o aplicativo responde à solicitação com as informações que o usuário está procurando.

#### <a name="preparing-for-the-conversation"></a>Preparando para a conversa

A primeira coisa a ser lembrado é que os usuários do aplicativo não precisa ser exatamente como o desenvolvedor. Eles podem vir de planos de fundo diferentes falam idiomas diferentes ou têm necessidades especiais ao trabalhar com o aplicativo.

Além disso, desde que o desenvolvedor projetado e criado o aplicativo, eles profundo, tem conhecimento profundo do aplicativo e seu funcionamento interno e funções que um usuário típico não terá. Portanto o desenvolvedor pode solicitar solicitação do Siri Diferentemente de um usuário normal.

É por isso é essencial ter muitas pessoas diferentes possível interagir com o aplicativo por meio de Siri. Os usuários podem fazer solicitações do aplicativo por meio de Siri que o desenvolvedor nunca foi considerado de ou em maneiras que não considere o desenvolvedor.

#### <a name="ensure-the-app-is-a-good-listener"></a>Certifique-se de que o aplicativo é um bom ouvinte

O desenvolvedor precisa garantir que o aplicativo é um bom ouvinte e está recebendo as especificações da conversa que atendem às expectativas do usuário. Mas também é possível que eles podem não ter fornecido todas as informações que o aplicativo precisa para atingir a tarefa solicitada.

Há várias maneiras que o aplicativo pode lidar com essa situação:

- **Escolher um bom padrão para o valor ausente** - por exemplo, uma aplicativo de compartilhamento de jornada pode padrão para o local atual do usuário se eles não especificam onde eles querem ser retiradas do.
- **Fazer uma estimativa razoável** -usando informações específicas que o aplicativo tenha coletado no usuário, o aplicativo pode ser capaz de criar e estimativa razoável nas informações ausentes, como preencher um número de celular ausente de informações de contato do usuário. No entanto, tome cuidado para evitar surpresas incorretas, como escolher a opção mais cara, etc.
- **Prompt para mais informações sobre** -o aplicativo pode ter Siri solicitar ao usuário para o valor ausente. No entanto, a chave aqui é manter as conversas simples e direto. Os usuários rapidamente se tornará frustrados se eles tiverem responder várias perguntas para obter sua solicitação.
- **Manipular informações incorretas normalmente** -o usuário pode fornecer um valor que o aplicativo não estava esperando ou que ele não pode manipular no contexto fornecido. Certifique-se de que o aplicativo se relaciona o usuário de uma maneira que torna mais clara e fácil corrigir essa situação.

Quando o aplicativo é apresentado com um único valor em questão, a melhor maneira de lidar com isso é ter Siri pedir ao usuário a confirmação. Por exemplo, *"Você quis Bobo excelentes?"* , que pode responder com uma resposta Sim ou não.

Quando há uma situação onde várias opções possíveis poderiam ser corretas para um único valor, ambiguidades é o método preferencial de manipulação. Nessa situação Siri pode solicitar que o usuário com até dez opções possíveis para escolher. Por exemplo:

```csharp
Who do you want to send the message to?

* Bobo the Great!
* Bobo Jr.
* Little Bobo
```

Se ainda estiver em questão, ter Siri solicitar que o usuário forneça uma resposta totalmente nova, mais específica para um determinado valor.

#### <a name="request-final-confirmation"></a>Solicitar a confirmação Final

Antes do aplicativo, na verdade, executa a tarefa para atender a solicitação do usuário, Siri irá verificar com a extensão do aplicativo para garantir que tudo está em vigor. Por exemplo, o usuário tem dinheiro suficiente em sua conta para efetuar o pagamento solicitado?

Além disso, o aplicativo precisa garantir que ela fornece todas as informações possíveis para Siri para que possa apresentá-lo para o usuário e confirme que a tarefa prestes a ser executada atende às suas expectativas.

Depois que o usuário confirmou a solicitação e o aplicativo executou-lo, o aplicativo precisa novamente, certifique-se de que ele tem fornecido todos os resultados para Siri para que ele pode relacioná-los para o usuário.

#### <a name="responding-to-the-request"></a>Respondendo à solicitação

Siri tem várias Interfaces de usuário internas para cada um dos domínios e ações que ele conheça. No entanto, quando apropriado, o aplicativo pode fornecer uma extensão de interface do usuário personalizada intenção para enriquecer a experiência do usuário, apresentando a identidade visual do aplicativo e a interface do usuário ou mais informações do que estava presente na solicitação.

Dito isso, moderação deve ser usada durante a criação de interfaces personalizadas para Siri. Normalmente, o usuário é desejado obter uma tarefa específica feita mais rápido possível e não deseja ser sobrecarregado com informações desnecessárias.

Também cuidado para garantir que a interface do usuário personalizada procura e responde corretamente em todos os dispositivos iOS diferentes e orientações que o usuário pode ter ou usar o dispositivo.

Quando apropriado, use a API SiriKit para ocultar todas as informações redundantes já está presentes no padrão Siri UI. Ainda, certifique-se de que o aplicativo é ainda fornecem as informações para Siri para que pode apresentar-verbalmente um situações mãos livre.

Pode haver situações em que o Siri iniciará o aplicativo para atender a solicitação do usuário, como a apresentação de fotos que o usuário solicitou. Nessas situações, não surpresa para o usuário. Exiba as informações esperadas sem a necessidade de etapas intermediárias ou interação adicional necessária. Nunca exibir informações ou executar uma tarefa que o usuário não está esperando.

### <a name="polishing-the-design"></a>Fazer o acabamento o Design

Há várias etapas que sugerem Apple para aprimorar o design das Interfaces de conversação. Primeiro, é fornecendo claras e concisos vocabulário e usar maiusculas exemplos para Siri.

Uma das maneiras que um usuário descobre o aplicativo está iniciando uma conversa com Siri e solicitar, *"O que você pode fazer?"* Siri mostra várias coisas diferentes pode fazer, incluindo o aplicativo do desenvolvedor e o herói exemplo casos de uso que ele foi fornecido por meio de seu `plist` arquivo.

Como escrever casos de uso de bom exemplo:

- Certifique-se de que os exemplos incluem o nome do aplicativo.
- Manter o exemplo curto e a ponto.
- Fornecem vários exemplos para cada as tentativas de que o aplicativo aceita.
- Priorize as tentativas e os exemplos dentro deles baseados nos casos de uso mais comuns para o aplicativo.
- Certifique-se de que o aplicativo fornece exemplos localizados.
- Certifique-se de que cada exemplo funciona conforme esperado no aplicativo.
- Evitar o endereçamento Siri nos exemplos, portanto não inclua texto como *"Ei Siri..."*
- Evitar qualquer pleasantries desnecessários como *","* ou *"Obrigado"*.

Reserve um tempo apropriado para explorar e testar como o aplicativo pode formatar a conversa que está tendo Siri com o usuário em seu nome. Certifique-se de falar com usuários típicos durante o processo, como suas interações com e expectativas do aplicativo podem ser alterados ao longo do tempo.

Sempre Lembre-se de testar o aplicativo em diferentes situações e todos os métodos diferentes para invocar uma conversa com o Siri. Teste nos locais do mundo real usuário pode estar usando o aplicativo, fora do escritório e o suporte técnico.

Se esforçam para ter conversas com Siri (em nome do aplicativo) fluidos, natural e "sinta-se apenas à direita". 

## <a name="summary"></a>Resumo

Este artigo abordou os principais conceitos necessários para usar SiriKit e mostrado que ele pode interagir com os aplicativos xamarin para fornecer os serviços que são acessíveis para o usuário usando o Siri e o aplicativo de mapas em um dispositivo iOS.




## <a name="related-links"></a>Links relacionados

- [Exemplo de ElizaChat](https://developer.xamarin.com/samples/monotouch/ios10/ElizaChat/)
- [Guia de programação de SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Referência de estrutura de tentativas](https://developer.apple.com/reference/intents)
- [Referência do Framework de interface do usuário de tentativas](https://developer.apple.com/reference/intentsui)
