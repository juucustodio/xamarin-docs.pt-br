---
title: O ciclo de vida do desenvolvimento de software móvel
description: Este documento descreve o ciclo de vida de desenvolvimento de software móvel, oferecendo informações sobre o design da experiência do usuário, o design da interface do usuário, o desenvolvimento, a estabilização, a distribuição e muito mais.
ms.prod: xamarin
ms.assetid: 420c5fdf-4610-4e71-9db5-fe894c961924
author: conceptdev
ms.author: crdun
ms.date: 11/22/2016
ms.openlocfilehash: 99be69abb11640418917d4d2cb9738a0a843d6d0
ms.sourcegitcommit: 32c7cf8b0d00464779e4b0ea43e2fd996632ebe0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68290151"
---
# <a name="mobile-software-development-lifecycle"></a>O ciclo de vida do desenvolvimento de software móvel

Criar aplicativos móveis pode ser tão fácil quanto abrir o Visual Studio, reunir alguns elementos, fazer alguns testes rápidos e enviar para uma App Store – tudo em uma tarde. Ou pode ser um processo extremamente complexo que envolve um design inicial rigoroso, teste de usabilidade, teste de garantia de qualidade em milhares de dispositivos, um ciclo de vida completo beta e, em seguida, implantação de várias maneiras diferentes.

Neste documento, faremos um exame introdutório minucioso da criação de aplicativos móveis, incluindo:

1.   **Processo** – o processo de desenvolvimento de software é chamado de SDLC (ciclo de vida de desenvolvimento do software). Vamos examinar todas as fases do SDLC em relação ao desenvolvimento de aplicativos móveis, incluindo: Concepção, Design, Desenvolvimento, Estabilização, Implantação e Manutenção.
1.   **Considerações** – há várias considerações ao criar aplicativos móveis, especialmente em contraste com aplicativos da área de trabalho ou Web tradicionais. Examinaremos essas considerações e como eles afetam o desenvolvimento para dispositivos móveis.

Este documento destina-se às perguntas fundamentais sobre o desenvolvimento de aplicativos móveis, tanto para os novos desenvolvedores de aplicativos quanto para os experientes. Ele utiliza uma abordagem bastante abrangente para introduzir a maioria dos conceitos que você executará durante todo o SDLC (ciclo de vida de desenvolvimento do software). No entanto, este documento pode não se aplicar a todos. Se você está ansioso para começar a criar aplicativos, é recomendável avançar para o guia de [Introdução ao desenvolvimento móvel](~/cross-platform/get-started/introduction-to-mobile-development.md) e, em seguida, voltar para este documento mais tarde.

## <a name="mobile-development-software-lifecycle"></a>Ciclo de vida do software de desenvolvimento móvel

O ciclo de vida de desenvolvimento móvel praticamente não é diferente do SDLC para aplicativos Web ou da área de trabalho. Assim como ocorre com estes, geralmente há 5 partes principais do processo:

1.   **Concepção** – todos os aplicativos começam com uma ideia. Essa ideia geralmente é refinada para tornar-se uma base sólida para um aplicativo.
1.   **Design** – a fase de design consiste em definir a UX (experiência do usuário) do aplicativo, por exemplo, como é o layout geral, como ele funciona, etc., bem como transformar essa experiência do Usuário em um design de UI (Interface do Usuário) adequado, geralmente com a Ajuda de um designer gráfico.
1.   **Desenvolvimento** – geralmente a fase com uso mais intensivo de recursos, costuma ser a verdadeira criação do aplicativo.
1.   **Estabilização** – quando o desenvolvimento é suficiente, o setor de garantia de qualidade geralmente começa a testar o aplicativo e os erros são corrigidos. Muitas vezes, um aplicativo entra em uma fase beta limitada na qual um público mais amplo recebe uma oportunidade para usá-lo, fornecer comentários e informar sobre eventuais alterações.
1.  **Implantação**

Muitas vezes, várias dessas partes são sobrepostas; por exemplo, é comum que o desenvolvimento esteja em andamento enquanto a interface do usuário está sendo finalizada, sendo que ele pode até mesmo servir de referência para o design dessa interface do usuário. Além disso, um aplicativo pode estar entrando em uma fase de estabilização ao mesmo tempo que os novos recursos estão sendo adicionados a uma nova versão.

Além disso, essas fases podem ser usadas em qualquer número de metodologias SDLC como Ágil, Espiral, Cascata, etc.

Cada uma dessas fases será explicada mais detalhadamente nas seções a seguir.

### <a name="inception"></a>Concepção

A ubiquidade e o nível de interação que as pessoas têm com dispositivos móveis significam que quase todo mundo tem uma ideia para um aplicativo móvel. Com os dispositivos móveis, abre-se um modo totalmente novo de interagir com a computação, a Web e até mesmo a infraestrutura empresarial.

O estágio de concepção trata-se de definir e refinar a ideia para um aplicativo.
Para criar um aplicativo com êxito, é importante fazer algumas perguntas fundamentais. Aqui estão algumas coisas a considerar antes de publicar um aplicativo em uma das lojas de aplicativos públicas:

-   **Vantagem competitiva** – já existem aplicativos semelhantes por aí? Nesse caso, como este aplicativo diferencia dos outros?

Para aplicativos que serão distribuídos em uma empresa:

-   **Integração com a infraestrutura** – qual infraestrutura existente ele estenderá ou com qual se integrará?

Além disso, os aplicativos devem ser avaliados no contexto do fator forma móvel:

-   **Valor** – que valor esse aplicativo traz os usuários? Como eles o usarão?
-   **Formulário/mobilidade** – como esse aplicativo funcionará em um fator forma móvel? Como adicionar valor usando tecnologias como reconhecimento de local, a câmera, etc.?

Para ajudar a criar a funcionalidade de um aplicativo, pode ser útil definir Atores e [Casos de Uso](https://en.wikipedia.org/wiki/Use_case). Os atores são funções dentro de um aplicativo e geralmente são usuários. Os casos de uso normalmente são ações ou propósitos.

Por exemplo, um aplicativo de controle de tarefa pode ter dois Atores: *Usuário* e *Amigo*. Um Usuário pode *criar uma tarefa* e *compartilhar uma tarefa* com um Amigo. Nesse caso, uma tarefa de criar e compartilhar uma tarefa são dois casos de uso distintos que, em conjunto com os atores, informará quais telas você precisa criar, bem como quais entidades de negócios e lógica precisarão ser desenvolvidas.

Depois que um número apropriado de casos de uso e atores tiver sido capturado, é muito mais fácil de começar a criar um aplicativo. O desenvolvimento pode então se concentrar em como criar o aplicativo, em vez do que o aplicativo é ou deve fazer.

### <a name="designing-mobile-applications"></a>Criando aplicativos móveis

Depois de terem sido determinados os recursos e funcionalidade do aplicativo, a próxima etapa é começar a tentar resolver a UX ou experiência do usuário.

#### <a name="ux-design"></a>Design da experiência do usuário

Geralmente, a experiência do usuário é feita por meio de delineados ou modelos com um dos vários [kits de ferramentas de design](https://docs.microsoft.com/windows/uwp/design/downloads/). Os modelos permitem criar uma experiência do usuário sem necessidade de se preocupar com o design da interface do usuário real:

 [![](introduction-to-mobile-sdlc-images/balsamiq.png "Geralmente, a experiência do usuário é feita por meio de delineados ou modelos usando ferramentas como Balsamiq")](introduction-to-mobile-sdlc-images/balsamiq.png#lightbox)

Ao criar modelos de experiência do usuário, é importante considerar as diretrizes de interface para as diversas plataformas às quais o aplicativo se destinará. O aplicativo deve ficar "confortável" todas as plataformas. As diretrizes de design oficiais para cada plataforma são:

1.   **Apple** -  [diretrizes de interface humana](https://developer.apple.com/ios/human-interface-guidelines/overview/themes/)
1.   **Android** – [diretrizes de design](https://developer.android.com/design/index.html)
1.   **UWP** – [Noções básicas de design da UWP](https://docs.microsoft.com/windows/uwp/design/basics/)

Por exemplo, cada aplicativo tem uma metáfora para mudar entre seções em um aplicativo. O iOS usa uma barra de guias na parte inferior da tela, o Android usa uma barra de guias na parte superior da tela e a UWP usa a exibição [Pivô ou guia](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/tabs-pivot).

Além disso, o próprio hardware também determina decisões de experiência do usuário. Por exemplo, dispositivos iOS não têm nenhum botão *voltar* físico e, portanto, introduzem a metáfora do Controlador de Navegação:

 ![](introduction-to-mobile-sdlc-images/01-navigation-controller.png "Os dispositivos iOS não têm botão voltar físico e, portanto, introduzem a metáfora do Controlador de Navegação")

Além disso, o fator forma também influencia decisões de experiência do usuário. Um tablet tem muito mais espaço e, portanto, pode exibir mais informações. Muitas vezes, aquilo que exige várias telas em um telefone é compactado em uma única em um tablet:

 [![](introduction-to-mobile-sdlc-images/iphone-vs-ipad.png "Muitas vezes, aquilo que exige várias telas em um telefone é compactado em uma única em um tablet")](introduction-to-mobile-sdlc-images/iphone-vs-ipad.png#lightbox)

E devido à grande variedade de fatores de forma existentes, geralmente há fatores forma de médio porte (entre um telefone e um tablet) que você também pode usar como destino.

#### <a name="user-interface-ui-design"></a>Design da IU (Interface do Usuário)

Depois que a experiência do usuário é determinada, a próxima etapa é criar o design de interface do usuário. Enquanto a experiência do usuário é normalmente composta apenas de modelos preto e brancos, a fase de Design de Interface do Usuário é o momento em que cores, gráficos, etc. são introduzidos e finalizados. Gastar tempo com um bom design de interface do usuário é importante e geralmente os aplicativos mais populares têm um design profissional.

Assim como ocorre com a experiência do usuário, é importante entender o que cada plataforma tem sua própria linguagem de design, portanto, um aplicativo bem projetado ainda pode ter uma aparência diferente em cada plataforma:

 [![](introduction-to-mobile-sdlc-images/multiplatform-1.png "Um aplicativo bem projetado pode ter aparências diferentes em cada plataforma")](introduction-to-mobile-sdlc-images/multiplatform-1.png#lightbox)

### <a name="development"></a>Desenvolvimento

A fase de desenvolvimento normalmente começa bem no início. Na verdade, assim que uma ideia tem algum amadurecimento na fase conceitual/de inspiração, muitas vezes é desenvolvido um protótipo funcional que valida a funcionalidade, suposições e ajuda a fornecer uma compreensão do escopo do trabalho.

No restante dos tutoriais, vamos nos concentrar principalmente na fase de desenvolvimento.

### <a name="stabilization"></a>Estabilização

A estabilização é o processo de corrigir os bugs do seu aplicativo. Não apenas do ponto de vista funcional, por exemplo: "Ele falha quando eu clico nesse botão", mas também em termos de Usabilidade e Desempenho. É melhor iniciar estabilização muito cedo no processo de desenvolvimento para que as correções possam ocorrer antes de se tornarem dispendiosas. Normalmente, aplicativos passam pelos estágios de *Protótipo*, *Alfa*, *Beta* e *Versão Release Candidate*. Pessoas diferentes definem esses estágios diferentemente, mas eles geralmente seguem o padrão a seguir:

1.   **Protótipo** – o aplicativo ainda está em fase de prova de conceito e apenas a funcionalidade básica ou partes específicas do aplicativo estão funcionando. Há bugs importantes presentes.
1.   **Alfa** – o código da funcionalidade básica geralmente está pronto (compilado, mas não totalmente testado). Ainda há bugs importantes presentes, as funcionalidades secundárias podem ainda não estar presentes.
1.   **Beta** – a maior parte da funcionalidade agora está completa e houve pelo menos testes leves e alguma correção de bugs. Problemas conhecidos importantes podem ainda estar presentes.
1.   **Versão Release Candidate** – toda a funcionalidade está concluída e testada. Salvo por novos bugs, o aplicativo é um candidato para liberação para uso.

Nunca é cedo demais para começar a testar um aplicativo. Por exemplo, se um grande problema for encontrado no estágio de protótipo, a experiência do usuário do aplicativo ainda poderá ser modificada para adequar-se a ele. Se um problema de desempenho for encontrado no estágio de alfa, será cedo suficiente para modificar a arquitetura antes que muito código tenha sido criado com base em suposições falsas.

Normalmente, conforme um aplicativo avança mais em seu ciclo de vida, ele é disponibilizado para que mais pessoas o experimentem, testem, forneçam comentários, etc. Por exemplo, os aplicativos de protótipo só podem ser mostrados ou disponibilizados para os principais stakeholders, enquanto os aplicativos em versão release candidate podem ser distribuídos aos clientes que se inscrevam para ter acesso antecipado.

Para os primeiros testes e implantação em um número relativamente pequeno de dispositivos, geralmente a implantação direta de um computador de desenvolvimento é suficiente. No entanto, conforme o público-alvo aumenta, isso pode rapidamente tornar-se complicado. Assim, há uma série de opções de implantação de teste por aí que tornam esse processo muito mais fácil ao permitir que você convide pessoas para um pool de teste, libere builds pela web e forneça ferramentas que permitam obter comentários do usuário.

Para testes e implantação, é possível usar o [App Center](https://appcenter.ms/) para criação, teste, versão e monitoramento de aplicativos de forma contínua.

### <a name="distribution"></a>Distribuição

Quando o aplicativo tiver sido estabilizado, é hora de disponibilizá-lo ao público. Há várias opções de distribuição diferentes, dependendo da plataforma.

#### <a name="ios"></a>iOS

Aplicativos Xamarin.iOS e Objective-C são distribuídos exatamente do mesmo modo:

1.   **Apple App Store** – a Apple App Store é um repositório de aplicativos online disponível globalmente, incorporado no Mac OS X por meio do iTunes. Ele é de longe o método de distribuição mais popular para aplicativos e permite que os desenvolvedores insiram seus aplicativos no mercado e distribuam-nos online com o mínimo de esforço.
1.   **Implantação interna** – a implantação interna destina-se à distribuição interna dos aplicativos corporativos que não estão disponíveis publicamente por meio da App Store.
1.   **Implantação do Ad-Hoc** – a implantação Ad-hoc é destinada principalmente para desenvolvimento e teste e permite que você implante a um número limitado de dispositivos provisionados adequadamente. Quando você implanta em um dispositivo por meio do Xcode ou Visual Studio para Mac, isso é conhecido como implantação ad-hoc.

#### <a name="android"></a>Android

Todos os aplicativos Android devem ser assinados antes de serem distribuídos. Os desenvolvedores assinam seus aplicativos usando seu próprio certificado protegido por uma chave privada. Esse certificado pode fornecer uma cadeia de autenticidade que vincula um desenvolvedor de aplicativos aos aplicativos que ele tenha criado e lançado.
Deve-se observar que, embora um certificado de desenvolvimento para Android possa ser assinado por uma autoridade de certificação reconhecida, a maioria dos desenvolvedores não optam por utilizar esses serviços e preferem eles próprios assinarem os seus certificados. A principal finalidade dos certificados é diferenciar entre diferentes desenvolvedores e aplicativos.
O Android usa essas informações para ajudá-lo a imposição da delegação de permissões entre aplicativos e componentes em execução no SO Android.

Ao contrário de outras plataformas móveis populares, o Android adota uma abordagem muito aberta para a distribuição de aplicativos. Os dispositivos não são bloqueados para uma única loja de aplicativos aprovada.
Em vez disso, todos são livres para criar uma loja de aplicativos e a maioria dos telefones Android permite que os aplicativos sejam instalados dessas lojas de terceiros.

Isso permite aos desenvolvedores o uso de um canal de distribuição para seus aplicativos que, apesar de potencialmente maior, é mais complexo. [Google Play](https://play.google.com/store?hl=en) é a loja de aplicativos oficial do Google, mas há muitas outras. Algumas populares são:

1.  [AppBrain](http://www.appbrain.com/)
1.  [Amazon App Store para Android](http://www.amazon.com/mobile-apps/b?ie=UTF8&amp;node=2350149011)
1.  [Handango](http://www.handango.com/)
1.  [GetJar](http://www.getjar.com/)

#### <a name="uwp"></a>UWP

Aplicativos UWP são distribuídos aos usuários por meio da Microsoft Store. Os desenvolvedores enviam seus aplicativos para aprovação e depois disso eles aparecem na loja. Para saber mais sobre como publicar aplicativos do Windows, consulte a documentação de [Publicação](https://docs.microsoft.com/windows/uwp/publish/) da UWP.

## <a name="mobile-development-considerations"></a>Considerações sobre desenvolvimento móvel

Embora o desenvolvimento de aplicativos móveis não seja fundamentalmente diferente de desenvolvimento de área de trabalho/Web tradicional em termos de arquitetura ou processo, há algumas considerações sobre as quais devemos estar atentos.

### <a name="common-considerations"></a>Considerações comuns

#### <a name="multitasking"></a>Multitarefa

Há dois desafios significativos para execução multitarefa (ter vários aplicativos em execução ao mesmo tempo) em um dispositivo móvel. Primeiro, devido à limitação do espaço de tela, é difícil exibir vários aplicativos simultaneamente. Portanto, em dispositivos móveis, apenas um aplicativo por vez pode ficar em primeiro plano. Segundo, manter vários aplicativos abertos e executando tarefas podem usar a energia da bateria rapidamente.

Cada plataforma lida com multitarefa de modo diferente, o que exploraremos daqui a pouco.

#### <a name="form-factor"></a>Fator forma

Dispositivos móveis geralmente se encaixam em duas categorias, telefones e tablets, com alguns dispositivos de transição intermediários. O desenvolvimento desses fatores forma é geralmente muito semelhante, no entanto, a criação de aplicativos para eles pode ser muito diferente.
Smartphones têm espaço de tela muito limitado e tablets, embora maiores, ainda são dispositivos móveis com menos espaço de tela até mesmo do que a maioria dos laptops. Por isso, controles de interface do usuário da plataforma móvel foram projetados especificamente para serem eficazes em fatores forma menores.

#### <a name="device-and-operating-system-fragmentation"></a>Fragmentação do sistema operacional e dispositivo

É importante levar em conta diferentes dispositivos em todo o ciclo de vida de desenvolvimento de software:

1.   **Conceitualização e planejamento** – lembre-se que o hardware e os recursos variam de dispositivo para dispositivo, um aplicativo que depende de alguns recursos pode não funcionar corretamente em certos dispositivos. Por exemplo, nem todos os dispositivos têm câmeras, portanto, se você estiver criando um aplicativo de mensagens de vídeo, alguns dispositivos podem ser capazes de reproduzir vídeos, mas não de capturá-los.
1.   **Design** – durante a criação da UX (experiência do usuário) de um aplicativo, preste atenção às diferentes proporções e tamanhos de tela em todos os dispositivos. Além disso, ao criar a IU (interface do usuário) de um aplicativo, diferentes resoluções de tela devem ser consideradas.
1.   **Desenvolvimento** – ao usar um recurso do código, a presença do recurso deve sempre ser testada primeiro. Por exemplo, antes de usar um recurso de dispositivo, como uma câmera, sempre consultar primeiro o sistema operacional quanto à presença do recurso. Em seguida, ao inicializar o recurso/dispositivo, certifique-se de solicitar ao SO o que tem suporte atualmente com relação a esse dispositivo e, em seguida, use essas configurações.
1.   **Teste** – é incrivelmente importante testar o aplicativo cedo e frequentemente em dispositivos reais. Até mesmo dispositivos com as mesmas especificações de hardware podem variar muito em seu comportamento.

#### <a name="limited-resources"></a>Recursos limitados

Dispositivos móveis tornam-se mais e mais eficientes o tempo todo, mas são dispositivos móveis com funcionalidades limitadas em comparação com computadores desktop ou notebooks. Por exemplo, desenvolvedores de aplicativos para computadores desktop geralmente não se preocupam com recursos de memória; eles estão acostumados a ter memória física e virtual em grandes quantidades, enquanto em dispositivos móveis você pode rapidamente consumir toda a memória disponível apenas ao carregar um punhado de imagens de alta qualidade.

Além disso, aplicativos de processamento intensivo como jogos ou reconhecimento de texto podem sobrecarregar de verdade a CPU móvel e afetar negativamente o desempenho do dispositivo.

Por causa de considerações como essa, é importante escrever código com inteligência e implantar antecipadamente e com frequência para dispositivos reais, a fim de validar a capacidade de resposta.

### <a name="ios-considerations"></a>Considerações do iOS

#### <a name="multitasking"></a>Multitarefa

Multitarefa é rigidamente controlada no iOS e há uma série de regras e comportamentos com os quais seu aplicativo deverá estar em conformidade quando outro aplicativo vier para o primeiro plano, caso contrário, seu aplicativo será encerrado pelo iOS.

#### <a name="device-specific-resources"></a>Recursos específicos do dispositivo

Dentro de um fator forma particular, o hardware pode variar significativamente entre diferentes modelos. Por exemplo, alguns dispositivos têm uma câmera voltada para a parte traseira, alguns também têm uma câmera voltada para a frente e alguns não têm nenhuma.

Alguns dispositivos mais antigos (iPhone 3G e anteriores) nem mesmo permitem a execução multitarefa.

Devido a essas diferenças entre modelos de dispositivo, é importante verificar a presença de um recurso antes de tentar usá-lo.

#### <a name="os-specific-constraints"></a>Restrições específicas do SO

Para certificar-se de que os aplicativos sejam responsivos e seguros, o iOS impõe uma série de regras às quais os aplicativos devem obedecer. Além das regras relativas à execução multitarefa, há uma série de métodos de evento dos quais seu aplicativo deve retornar em um determinado período de tempo, caso contrário, que ele será encerrado pelo iOS.

Também vale a pena observar que os aplicativos são executados no que é conhecido como uma área restrita, um ambiente que impõe restrições de segurança que restringem o que seu aplicativo pode acessar. Por exemplo, um aplicativo pode ler e gravar seu próprio diretório, mas se tentar gravar no diretório de outro aplicativo, ele será encerrado.

### <a name="android-considerations"></a>Considerações sobre o Android

#### <a name="multitasking"></a>Multitarefa

Execução multitarefa no Android tem dois componentes; o primeiro é o ciclo de vida da atividade. Cada tela em um aplicativo Android é representada por uma atividade e há um conjunto específico de eventos que ocorrem quando um aplicativo é colocado em segundo plano ou vem para o primeiro plano. É necessário seguir esse ciclo de vida para criar aplicativos responsivos e com bom comportamento. Para obter mais informações, veja o guia [Ciclo de vida de atividade](~/android/app-fundamentals/activity-lifecycle/index.md).

O segundo componente da execução multitarefa no Android é o uso de Serviços.
Os Serviços são processos de execução longa que existem independentemente de um aplicativo e são usados para executar processos enquanto o aplicativo está em segundo plano. Para obter mais informações, veja o guia [Criando serviços](~/android/app-fundamentals/services/index.md).

#### <a name="many-devices-and-many-form-factors"></a>Muitos dispositivos e muitos fatores forma

O Google não impõe limites quanto a quais dispositivos podem executar o sistema operacional Android. Esse paradigma aberto resulta em um ambiente de produtos populado por uma grande variedade de dispositivos diferentes com hardware, taxas de proporção e resoluções de tela, recursos do dispositivo e funcionalidades muito diferentes.

Devido à fragmentação extrema de dispositivos Android, a maioria das pessoas escolhe os 5 ou 6 dispositivos mais populares para os quais projetar e testar e dá prioridade a eles.

#### <a name="security-considerations"></a>Considerações sobre segurança

Todos os aplicativos no SO Android executam sob uma identidade distinta e isolada, com permissões limitadas. Por padrão, os aplicativos podem fazer muito pouco. Por exemplo, sem permissões especiais, um aplicativo não pode enviar uma mensagem de texto, determinar o estado do telefone ou até mesmo acessar a Internet! Para acessar esses recursos, os aplicativos devem especificar no seu arquivo de manifesto do aplicativo quais permissões eles desejam e quando eles serão instalados. O SO lê essas permissões, notifica o usuário que o aplicativo está solicitando essas permissões e, em seguida, permite que o usuário continue ou cancele a instalação.
Essa é uma etapa essencial no modelo de distribuição Android por causa do modelo aberto de repositório de aplicativos, já que os aplicativos não são coletados do modo que seriam para iOS, por exemplo. Para obter uma lista de permissões de aplicativo, veja o artigo de referência [Permissões de Manifesto](https://developer.android.com/reference/android/Manifest.permission.html) na documentação do Android.

### <a name="windows-considerations"></a>Considerações do Windows

#### <a name="multitasking"></a>Multitarefa

A execução multitarefa na UWP tem duas partes: o ciclo de vida de páginas e aplicativos e os processos em segundo plano. Cada tela em um aplicativo é uma instância de uma classe de página, que tem eventos associados que estão sendo tornados ativos ou inativos (com regras especiais para lidar com o estado inativo ou sendo "marcados para exclusão").

A segunda parte é fornecer agentes de segundo plano para tarefas de processamento, mesmo quando o aplicativo não está em execução em primeiro plano.

#### <a name="device-capabilities"></a>Funcionalidades do dispositivo

Embora o hardware da UWP seja bastante homogêneo, ainda há componentes opcionais que, portanto, exigem consideração especial durante a codificação. As funcionalidades opcionais do hardware incluem a câmera, a bússola e o giroscópio. Há também uma classe especial de memória insuficiente (256 MB) que requer consideração especial ou os desenvolvedores podem optar por não dar suporte a memória insuficiente.

#### <a name="security-considerations"></a>Considerações sobre segurança

Para saber mais sobre considerações de segurança importantes na UWP, consulte a documentação de [Segurança](https://docs.microsoft.com/windows/uwp/security/).

## <a name="summary"></a>Resumo

Este guia forneceu uma introdução para o SDLC, pois ele diz respeito ao desenvolvimento móvel. Ele introduziu considerações gerais para a criação de aplicativos móveis e examinou uma série de considerações específicas da plataforma incluindo design, teste e implantação.

## <a name="next-steps"></a>Próximas etapas

- [O que é o Xamarin?](~/cross-platform/get-started/introduction-to-mobile-development.md)
- [Introdução ao Xamarin](~/get-started/index.yml)
- [Como compartilhar o código entre plataformas](~/cross-platform/app-fundamentals/index.md)
