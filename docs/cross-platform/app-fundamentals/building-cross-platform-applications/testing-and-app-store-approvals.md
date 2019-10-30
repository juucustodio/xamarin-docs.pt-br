---
title: Parte 6 – Teste e aprovações da app store
description: Este documento descreve como testar um aplicativo de plataforma cruzada no dispositivo, gerenciar casos de teste, automatizar testes, executar testes de unidade e trabalhar com o processo de envio do aplicativo.
ms.prod: xamarin
ms.assetid: 46E0578A-7EB9-C105-ABB0-A043E501F36B
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 07eab1e8229cd6600bb8abdd324cb3eb12a8f886
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016850"
---
# <a name="part-6---testing-and-app-store-approvals"></a>Parte 6 – Teste e aprovações da app store

## <a name="testing"></a>Testes

Muitos aplicativos (até mesmo aplicativos Android, em algumas lojas) terão que passar um processo de aprovação antes de serem publicados; Portanto, o teste é essencial para garantir que seu aplicativo chegue ao mercado (o que sozinha é bem sucedido com seus clientes). O teste pode assumir muitas formas, desde testes de unidade de nível de desenvolvedor até o gerenciamento de testes beta em uma ampla variedade de hardware.

### <a name="test-on-all-platforms"></a>Testar em todas as plataformas

Há pequenas diferenças entre o que o .NET dá suporte em dispositivos Windows Phone, Tablet e desktop, bem como limitações no iOS que impedem que o código dinâmico seja gerado de forma dinâmica. Planeje o teste do código em várias plataformas enquanto você o desenvolve, ou agende o tempo para Refatorar e atualize a parte do modelo do seu aplicativo no final do projeto.

É sempre uma boa prática usar o simulador/emulador para testar várias versões do sistema operacional e também recursos/configurações de dispositivo diferentes.

Você também deve testar o máximo possível de dispositivos de hardware físicos diferentes.

#### <a name="devices-in-cloud"></a>Dispositivos na nuvem

O celular e o ecossistema do Tablet estão crescendo o tempo todo, tornando impossível testar o número cada vez maior de dispositivos disponíveis. Para resolver esse problema, vários serviços oferecem a capacidade de controlar remotamente vários dispositivos diferentes para que os aplicativos possam ser instalados e testados sem a necessidade de investir diretamente em muitos hardwares.

O [app Center Test](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest) oferece uma maneira fácil de testar aplicativos Ios e Android em centenas de dispositivos diferentes.

### <a name="test-management"></a>Gerenciamento de testes

Ao testar aplicativos dentro de sua organização ou gerenciar um programa beta com usuários externos, há dois desafios:

- **Distribuição** – gerenciar o processo de provisionamento (especialmente para dispositivos IOS) e obter versões atualizadas do software para os testadores.
- **Comentários** – coleta de informações sobre o uso do aplicativo e informações detalhadas sobre quaisquer erros que possam ocorrer.

Há vários serviços que ajudam a resolver esses problemas, fornecendo a infraestrutura incorporada ao seu aplicativo para coletar e relatar o uso e os erros, além de simplificar o processo de provisionamento para ajudar a inscrever e gerenciar testadores e seus dispositivos .

O [Visual Studio app Center](/appcenter/) oferece uma solução para esses problemas, fornecendo distribuição de versão de teste, relatório de falhas e informações sofisticadas de uso do aplicativo.

### <a name="test-automation"></a>Automação de teste

O Xamarin [UITest](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest) pode ser usado para criar scripts de teste da interface do usuário automatizados que podem ser executados localmente ou carregados no [teste de App Center](https://docs.microsoft.com/appcenter/test-cloud/).

## <a name="unit-testing"></a>Teste de unidade

### <a name="touchunit"></a>Touch. Unit

O Xamarin. iOS inclui uma estrutura de teste de unidade chamada Touch. Unit que segue os testes de escrita de estilo JUnit/NUnit.

Consulte nosso [teste de unidade com a documentação do Xamarin. Ios](~/ios/deploy-test/touch.unit.md) para obter detalhes sobre como escrever testes e executar Touch. Unit.

### <a name="andrunit"></a>Andr. Unit

Há um equivalente de código-fonte aberto de Touch. Unit para Android chamado Andr. Unit. Você pode baixá-lo do [GitHub](https://github.com/spouliot/Andr.Unit) e ler sobre a ferramenta no [blog de@spouliot](https://spouliot.wordpress.com/2011/10/30/andr-unit-joins-the-family/).

## <a name="app-store-approvals"></a>Aprovações da loja de aplicativos

A Apple e a Microsoft operam a única loja em suas plataformas: a loja de aplicativos e o Marketplace, respectivamente. Ambos bloqueiam seus dispositivos e implementam um processo de revisão de aplicativo rigoroso para controlar a qualidade dos aplicativos disponíveis para download. A natureza aberta do Android significa que há várias opções de armazenamento que vão desde a reprodução do Google, que é amplamente disponível e não tem nenhum processo de análise, até o AppStore da Amazon para esforços específicos de hardware e Android, como aplicativos Samsung que têm distribuição mais limitada e implemente um processo de aprovação.

Esperar que um aplicativo seja revisado pode ser muito estressante-as pressões de negócios geralmente significam que os aplicativos são enviados para aprovação com muito pouca margem de erros antes de uma data de lançamento "direcionada". O próprio processo pode levar até duas semanas e não é necessariamente transparente: há comentários limitados sobre o progresso do seu aplicativo até que ele seja finalmente rejeitado ou aprovado. A rejeição pode significar falta de uma janela de marketing de oportunidade, especialmente se ocorrer mais de uma vez e semanas passarem entre a data de início original e quando o aplicativo for finalmente aprovado.

### <a name="be-prepared"></a>Estar preparado

A primeira peça de aviso: planeje o início do aplicativo com antecedência e faça concessões para uma possível rejeição e reenvio. Cada loja exige que você crie uma conta antes de enviar seu aplicativo-faça isso o mais cedo possível.
Embora a inscrição de Google Play leve apenas alguns minutos se seus aplicativos forem gratuitos, o processo ficará muito mais envolvido se você estiver vendendo-los e precisar fornecer informações bancárias e fiscais. Os processos da Apple e da Microsoft são muito mais envolvidos do que o Google, pode levar uma semana ou mais para que sua conta seja aprovada, portanto, dessa vez em seus planos de lançamento.

Depois que sua conta tiver sido aprovada, você estará pronto para enviar um aplicativo. O processo real para enviar aplicativos é abordado na seguinte documentação:

- [Publicação na iOS App Store da Apple](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [Preparando um aplicativo para Google Play](~/android/deploy-test/publishing/publishing-to-google-play/index.md)
- Os desenvolvedores do Windows devem visitar o [centro de desenvolvimento do Windows](https://developer.microsoft.com/windows/windows-apps) para ler sobre como enviar seus aplicativos.

O restante desta seção aborda as coisas que você deve levar em consideração para garantir que seu aplicativo seja aprovado sem nenhum quaisquer sustos.

### <a name="quality"></a>Qualidade

Parece óbvio, mas os aplicativos muitas vezes são rejeitados porque não atendem a um determinado nível de qualidade: afinal, esse é o motivo pelo qual os armazenamentos organizados têm um processo de aprovação em primeiro lugar!

Falhas são um motivo comum para rejeição. Se for muito fácil fazer o aplicativo falhar, é garantido que ele seja rejeitado. A maioria dos desenvolvedores não enviam seus aplicativos com a expectativa de que eles falham, ainda assim, geralmente fazem. Teste seu aplicativo cuidadosamente antes de enviá-lo, concentrando-se não apenas em garantir que tudo funcione, mas também que você lida com cenários de erro de dispositivos móveis comuns, como problemas de rede e restrições de recursos, como memória ou espaço de armazenamento. Use o simulador e os dispositivos físicos para testar, independentemente de como o código seja executado em um simulador, apenas um dispositivo pode demonstrar o desempenho real de um aplicativo. Use tantos dispositivos diferentes quantos você encontrar e inscreva uma equipe de testadores beta se você puder-os serviços de terceiros podem ajudar a gerenciar a distribuição beta e os comentários.

Todos os sistemas operacionais móveis eliminarão um aplicativo que não inicia com rapidez suficiente. O período de tempo permitido varia, mas, em geral, os aplicativos devem ser responsivos em alguns segundos e usar tarefas em segundo plano para realizar qualquer trabalho que demore mais. Os aplicativos que levam muito tempo para carregar ou não estão respondendo o suficiente no uso regular serão rejeitados. Sempre forneça comentários do usuário quando algo estiver acontecendo em segundo plano ou o aplicativo parece ter falhado e, mais uma vez, ser rejeitado.

### <a name="check-your-edge-cases"></a>Verifique seus casos de borda

Uma interceptação comum para os desenvolvedores está falhando em lidar com casos de borda, especialmente aqueles que exigem a reconfiguração de seu simulador ou dispositivo para serem testados corretamente. Pode ser fácil esquecer que nem todos os clientes vão "permitir" que seu aplicativo acesse sua localização porque, depois que o desenvolvedor aceitou a solicitação uma vez, ele nunca será solicitado novamente. As permissões e o uso de rede são especificamente focados durante o processo de aprovação, o que significa que uma pequena supervisão nessas áreas pode resultar em rejeição.

A lista a seguir é um bom ponto de partida para verificar os casos de borda que podem ter sido perdidos:

- **Os clientes podem ' negar ' acesso aos serviços** – especialmente no Ios, o acesso a dados como informações de localização geográfica só será fornecido se o usuário conceder permissão para seu aplicativo. Os testadores de aplicativos devem reinstalar frequentemente o aplicativo em seu estado inicial e não permitir nenhuma solicitação de permissão para garantir que o aplicativo se comporta adequadamente. Ative e desative a permissão para verificar o comportamento correto à medida que os clientes mudarem de ideia.
- **Os clientes estão em qualquer lugar** – não presuma que um aplicativo será usado apenas na cidade ou no país em que foi desenvolvido! O código que funciona com coordenadas de GPS, valores de data e hora e moedas pode ser afetado pelo local e pelas configurações de localidade do cliente. Todas as plataformas oferecem um simulador que permite especificar locais e localidades diferentes – usá-lo para testar locais em outros hemisférios e com culturas que formatam datas e moedas de maneira diferente. Os valores de latitude e longitude podem ser positivos ou negativos, o separador decimal pode ser um ponto ou uma vírgula, e as datas podem ser formatadas de inúmeras formas.
- **Conexões de rede lentas** – os desenvolvedores de aplicativos geralmente trabalham em um ' mundo ideal ' de conectividade de rede rápida e sempre em funcionamento, o que obviamente não é o caso no mundo real. O teste com conectividade de rede lenta (como uma conexão 3G ruim), bem como sem acesso à rede, é essencial para garantir que você não envie um aplicativo de bugs. O processo de aprovação sempre incluirá um teste com o dispositivo no modo avião, portanto, certifique-se de que você já testou isso por conta própria.
- **Variação de hardware** – Lembre-se de testar o hardware mais antigo e mais lento para o qual você planeja dar suporte. Há dois aspectos que podem afetar seu aplicativo: desempenho, que pode ser inutilizável em um dispositivo mais antigo e suporte para recursos de hardware, como uma câmera, microfone, GPS, giroscópio ou outro componente opcional. Os aplicativos devem degradar normalmente (e não falhar) quando um componente não está disponível.

### <a name="guidelines-are-more-than-just-a-guide"></a>As diretrizes são mais do que apenas um ' guia '

A Apple é famosa por ser estrito sobre a adesão às suas diretrizes de interface humana, uma vez que uma das principais forças de sua plataforma é a consistência (e o aumento percebido na usabilidade). A Microsoft adotou uma abordagem semelhante com os aplicativos do Windows que implementam o [sistema de design fluente](https://microsoft.com/design/fluent). O processo de aprovação para ambas as plataformas envolverá seu aplicativo sendo avaliado para sua adesão à filosofia de design relevante.

Isso não quer dizer que a inovação da interface do usuário não é suportada nem incentivada, mas há certas coisas que você não deve fazer "ou seu aplicativo será rejeitado.

No iOS, isso inclui o uso inadequado de ícones internos ou o uso de outras metáforas bem estabelecidas de maneira não consistente; por exemplo, usando o ícone ' compor ' para qualquer coisa que não seja a criação de um novo conteúdo.

Os desenvolvedores do Windows devem ter um cuidado semelhante. um erro comum é a falha em dar suporte corretamente ao botão voltar do hardware de acordo com as diretrizes da Microsoft.

Incentive seus designers a ler e seguir as diretrizes de design para cada plataforma.

### <a name="implementing-platform-specific-features"></a>Implementando recursos específicos da plataforma

As coisas são um pouco mais rígidas quando se trata de implementar serviços específicos da plataforma, especialmente no iOS. Para evitar a rejeição automática da Apple, há algumas regras a serem seguidas com os seguintes recursos do iOS:

- **Compras no aplicativo** – os aplicativos não devem implementar mecanismos de pagamento externos para produtos digitais, incluindo a moeda do jogo, recursos do aplicativo, assinaturas da revista e muito mais. os aplicativos iOS devem usar o serviço do iTunes da Apple para esse tipo de funcionalidade. Há um brecha como o leitor de Kindle e alguns aplicativos baseados em assinatura permitem que você compre conteúdo em outro lugar que é anexado a uma "conta" que você pode acessar por meio do aplicativo; no entanto, nesse caso, o aplicativo não deve conter links ou referências para o processo de compra fora do aplicativo (ou, mais uma vez, ele será rejeitado).
- **backup do icloud** – com o advento do icloud, os revisores da Apple são muito mais estritos em relação a como os aplicativos usam o armazenamento (para garantir que a experiência de backup remoto do cliente seja agradável). Os aplicativos que desperdiçam espaço de armazenamento capaz de fazer backup podem ser rejeitados, portanto, use a pasta de cache adequadamente e siga as outras diretrizes relacionadas ao armazenamento da Apple.
- **Newsstand** – os aplicativos de jornal e de revista são uma ótima opção para o Newsstand da Apple, no entanto, os aplicativos devem implementar pelo menos uma assinatura de renovação automática e dar suporte ao download em segundo plano para serem aprovados.
- **Mapas** – é cada vez mais comum adicionar sobreposições e outros recursos aos mapas móveis, no entanto, tenha cuidado para não obscurecer as informações de "créditos" do mapa (como o logotipo do Google no iOS5), pois isso resultará em rejeição.

### <a name="manage-your-metadata"></a>Gerenciar seus metadados

Além dos problemas técnicos óbvios que podem resultar em um aplicativo sendo rejeitado, há alguns aspectos mais sutis de seu envio que podem resultar em rejeição, especialmente em relação aos metadados (descrição, palavras-chave e imagens de marketing) que você Envie com seu aplicativo para exibição na loja de aplicativos ou no Marketplace.

- **Imagens** – siga as diretrizes da plataforma para ícones de aplicativos e armazene imagens. Não use imagens marcadas, nós vimos que os aplicativos são rejeitados porque seus ícones apresentavam um desenho de um iPhone!
- **Marcas registradas** – Evite usar marcas comerciais diferentes da sua. Os aplicativos foram negados para mencionar marcas registradas na descrição do aplicativo ou mesmo nas palavras-chave na loja de aplicativos da Apple.
- **Descrição** – não use a palavra ' beta ' ou, de qualquer forma, indique que o aplicativo não está pronto para o momento principal. Não mencione outras plataformas móveis (mesmo que seu aplicativo seja entre plataformas). Mais importante, verifique se o aplicativo faz exatamente o que você diz que ele faz. Se você listar vários recursos em sua descrição, ele teria mais óbvio como usar cada um desses recursos ou você receberá uma rejeição "o recurso mencionado na descrição do aplicativo não está implementado".

Coloque o máximo de esforço nos metadados do aplicativo como em desenvolvimento e teste. Os aplicativos são rejeitados para violações secundárias nos metadados, portanto, vale a pena dedicar o tempo para obtê-lo certo.

### <a name="app-stores-not-for-everyone"></a>Lojas de aplicativos: não para todos

O foco principal dos armazenamentos em cada plataforma é a distribuição do consumidor-a capacidade de alcançar o máximo possível de clientes. No entanto, nem todos os aplicativos são destinados a consumidores, há uma base rápida em crescimento de aplicativos internos e de extranet que exigem distribuição limitada para funcionários, fornecedores ou clientes. Esses aplicativos não são "para venda" e não precisam de aprovação, já que o desenvolvedor controla a distribuição para um grupo de usuários fechado.
O suporte para esse tipo de implantação varia de acordo com a plataforma.

O Android oferece mais flexibilidade nesse aspecto: os aplicativos podem ser instalados diretamente de uma URL ou anexo de email (contanto que a configuração do dispositivo permita isso). Isso significa que é trivial criar e distribuir aplicativos corporativos internos ou publicar aplicativos para clientes ou fornecedores específicos.

A Apple fornece uma opção de implantação interna para os desenvolvedores registrados no programa corporativo do desenvolvedor do iOS, que ignora o processo de aprovação da loja de aplicativos e permite que as empresas distribuam aplicativos internos para seus funcionários.
Infelizmente, essa licença não atende à necessidade de distribuição de aplicativo semelhante à extranet para outros grupos fechados de clientes ou fornecedores. [Implantação Enterprise (e ad hoc)](~/ios/deploy-test/app-distribution/ipa-support.md)

### <a name="app-store-summary"></a>Resumo da loja de aplicativos

O processo de revisão pode ser assustador, mas como o restante do ciclo de vida de desenvolvimento, você pode ajudar a garantir o sucesso com um pouco de planejamento e atenção para os detalhes. Tudo isso se resume a algumas etapas simples: ler e entender as diretrizes de interface do usuário que você deve obedecer, siga as regras se estiver implementando recursos específicos da plataforma, teste totalmente (em seguida, teste mais algum) e, finalmente, certifique-se de que os metadados do aplicativo está correto antes de enviar.

Uma última palavra de conselhos para os desenvolvedores que estão publicando na Google Play: a falta de processo de aprovação pode parecer que torna seu trabalho mais fácil, mas seus clientes serão ainda mais exigentes do que uma equipe de análise. Siga estas diretrizes como se seu aplicativo pudesse ser rejeitado, caso contrário, será seus clientes fazendo a rejeição.
