---
title: Parte 6 – Teste e aprovações da app store
description: Este documento descreve como testar um aplicativo de plataforma cruzada no dispositivo, gerenciar casos de teste, automatizar os testes, executar testes de unidade e percorrer o processo de envio do aplicativo.
ms.prod: xamarin
ms.assetid: 46E0578A-7EB9-C105-ABB0-A043E501F36B
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 0faf7c9e4ff7c96cdfd25ab6d6658726ef247b32
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61275401"
---
# <a name="part-6---testing-and-app-store-approvals"></a>Parte 6 – Teste e aprovações da app store

## <a name="testing"></a>Testes

Muitos aplicativos (aplicativos Android até mesmo, em algumas lojas) serão necessário passar um processo de aprovação antes de serem publicados; para que o teste é essencial para garantir que seu aplicativo atinge o mercado (sem falar em terá êxito com seus clientes). Testes podem assumir várias formas, nível de desenvolvedor do teste de unidade para gerenciar testes beta em uma ampla variedade de hardware.

### <a name="test-on-all-platforms"></a>Em todas as plataformas de teste

Há pequenas diferenças entre o .NET dá suporte no Windows phone, tablet e dispositivos da área de trabalho, bem como as limitações no iOS que impedem o código dinâmico seja gerado em tempo real. O plano de teste do código em várias plataformas, como desenvolvê-lo, ou agendar o tempo para Refatorar e atualizar a parte do modelo do seu aplicativo no final do projeto.

É sempre uma boa prática usar o simulador/emulador para testar várias versões do sistema operacional e também recursos/configurações de dispositivos diferentes.

Você também deve testar em tantos dispositivos de hardware físico diferente, como é possível.

#### <a name="devices-in-cloud"></a>Dispositivos na nuvem

O ecossistema móvel de telefone e tablet está aumentando o tempo todo, tornando impossível testar no número crescente de dispositivos disponíveis. Para resolver esse problema, que um número de serviços oferece a capacidade de controlar remotamente dispositivos diferentes, para que os aplicativos podem ser instalados e testados sem a necessidade de investir diretamente em lotes de hardware.

[Teste do App Center](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest) oferece uma maneira fácil de testar aplicativos iOS e Android em centenas de dispositivos diferentes.

### <a name="test-management"></a>Gerenciamento de testes

Ao testar aplicativos dentro de sua organização ou gerenciar um programa beta com usuários externos, há dois desafios:

- **Distribuição** – gerenciar o processo de provisionamento (especialmente para dispositivos iOS) e obter as versões atualizadas do software para os testadores.
- **Comentários** – coletar informações sobre o uso do aplicativo e informações detalhadas sobre quaisquer erros que podem ocorrer.


Há uma série de serviços ajuda a resolver esses problemas, fornecendo a infraestrutura que está incorporada ao seu aplicativo para coletar e relatar sobre o uso e erros e também simplificando o processo de provisionamento para ajudar a inscrever-se e gerenciar seus dispositivos e testadores .

[Visual Studio App Center](/appcenter/) oferece uma solução para esses problemas, fornecendo a distribuição de versão de teste, o relatório de falhas e informações de uso de aplicativos sofisticados.

### <a name="test-automation"></a>Automação de teste

Xamarin [UITest](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest) pode ser usado para criar scripts de teste que podem ser executados localmente ou carregados de interface do usuário automatizados [App Center testar](https://docs.microsoft.com/appcenter/test-cloud/).

## <a name="unit-testing"></a>Teste de unidade

### <a name="touchunit"></a>Touch.Unit

Xamarin. IOS inclui uma estrutura de teste de unidade chamada Touch que segue o estilo do JUnit/NUnit escrever testes.

Consulte nosso [Unit Testing com o xamarin. IOS](~/ios/deploy-test/touch.unit.md) documentação para obter detalhes sobre como escrever testes e executar Touch.

### <a name="andrunit"></a>Andr.Unit

Há um equivalente de código-fonte aberto do touch para o Android chamado Andr.Unit. Você pode baixá-lo partir [github](https://github.com/spouliot/Andr.Unit) e leia sobre a ferramenta no [ @spouliotdo blog](http://spouliot.wordpress.com/2011/10/30/andr-unit-joins-the-family/).

## <a name="app-store-approvals"></a>Aprovações da App Store

Apple e a Microsoft operam o repositório somente em suas plataformas: a App Store e o Marketplace, respectivamente. Ambos bloquear seus dispositivos e implementam um processo de revisão de aplicativo rigorosos para controlar a qualidade de aplicativos disponíveis para download. Natureza do Android aberta significa que há várias opções de armazenamento que vão do Google Play, que é amplamente disponível e não tem nenhum processo de revisão, a loja de aplicativos da Amazon para esforços de Android e específicos de hardware, como aplicativos Samsung que distribuição mais limitada e implementar um processo de aprovação.

Aguardando um aplicativo a ser revisado pode ser muito desgastante - pressões de negócios geralmente significam aplicativos são enviados para aprovação com muito pouco margem de erro antes de uma data de lançamento "destino". O próprio processo pode levar até duas semanas e não é necessariamente transparente: há comentários limitado sobre o andamento do seu aplicativo até que finalmente é rejeitado ou aprovado. Rejeição pode significar faltando uma janela de marketing de oportunidade, especialmente se ele ocorre mais de uma vez e passam de semanas entre a data de lançamento original e quando o aplicativo finalmente é aprovado.

### <a name="be-prepared"></a>Prepare-se

A primeira parte do conselho: planejar antecipadamente o lançamento do seu aplicativo e fazer concessões para um possível rejeição e envio novamente. Cada repositório exige que você criar uma conta antes de enviar seu aplicativo - fazer isso mais cedo possível.
Enquanto a inscrição do Google Play demora apenas alguns minutos se seus aplicativos são gratuitos, o processo obtém muito mais envolvido se você estiver vendendo-los e precisa fornecer serviços bancários e informações fiscais. Apple e processos da Microsoft sejam muito mais envolvido do que do Google, pode levar uma semana ou mais para acessar sua conta aprovada então fatorar esse tempo em seus planos de lançamento.

Depois que sua conta tiver sido aprovada, você está pronto para enviar um aplicativo. O processo real de envio de aplicativos é abordado na documentação a seguir:

- [Publicando em iOS da Apple App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [Preparar um aplicativo para o Google Play](~/android/deploy-test/publishing/publishing-to-google-play/index.md)
- Os desenvolvedores do Windows devem visitar a [Centro de desenvolvimento do Windows](https://developer.microsoft.com/windows/windows-apps) para ler sobre o envio de seus aplicativos.

O restante desta seção aborda as coisas que você deve levar em consideração para garantir que seu aplicativo é aprovado sem quaisquer interrupções.

### <a name="quality"></a>Qualidade

Isso parece óbvio, mas aplicativos muitas vezes ser rejeitados porque elas não atendem a um determinado nível de qualidade: Afinal de contas, isso é o motivo por que os armazenamentos de curados tem um processo de aprovação em primeiro lugar!

Falhas são um motivo comum para rejeição. Se ele é muito fácil cometer o travamento do aplicativo, há garantia de que a serem rejeitadas. A maioria dos desenvolvedores não enviam seus aplicativos com a expectativa de que eles vai falhar, mas eles normalmente fazem. Teste seu aplicativo completamente antes de enviá-lo, concentrando-se de não apenas em tornar-se de que tudo está funcionando, mas também que você lidar com cenários de erro móvel comuns, como problemas de rede e restrições de recursos, como memória ou espaço de armazenamento. Usar o simulador e dispositivos físicos para testar - independentemente de quão bem o código é executado em um simulador, apenas um dispositivo pode demonstrar o desempenho real de um aplicativo. Use tantos dispositivos diferentes, como você pode localizar e inscrever-se uma equipe de testadores beta se é possível - serviços de terceiros podem ajudar a gerenciar a distribuição beta e comentários.

Todos os sistemas operacionais móveis finalizará a um aplicativo que não comece rápido o suficiente. O período de tempo permitido varia, mas em geral aplicativos devem ter como objetivo do tempo de resposta em alguns segundos e usar tarefas em segundo plano para fazer qualquer trabalho que seria mais demorado. Aplicativos que levam muito tempo para carregar ou não responde no uso normal não estiver serão rejeitadas. Sempre fornece comentários do usuário quando algo está acontecendo em segundo plano, ou o aplicativo parecerá ter falhado e mais uma vez, são rejeitadas.

### <a name="check-your-edge-cases"></a>Verifique seus casos de borda

Uma armadilha comum para desenvolvedores está falhando em casos de borda do endereço, especialmente aquelas que exigem reconfigurar seu simulador ou dispositivo para testar adequadamente. É fácil esquecer que nem todo cliente será "Permitir" do seu aplicativo acesse sua localização, porque depois que o desenvolvedor aceitou a solicitação de uma vez, nunca é solicitado novamente. Permissões e uso de rede são especificamente fiquei concentrados no durante o processo de aprovação, o que significa que um pequeno erro nessas áreas pode resultar na rejeição.

A lista a seguir é um bom ponto de partida para a verificação de casos de borda que podem ter sido perdidos:

- **Os clientes podem 'Negar' acesso aos serviços** – especialmente no iOS, acesso a dados, como informações de localização geográfica são fornecidas apenas se o usuário concede permissão ao seu aplicativo. Testadores de aplicativo com frequência devem reinstalar o aplicativo em seu estado inicial e não permitir quaisquer solicitações de permissão para garantir que o aplicativo se comporta de forma adequada. Ative a permissão e desligada para verificar o comportamento correto conforme os clientes mudem de ideia.
- **Os clientes estão em todos os lugares** – não presuma que um aplicativo só será usado na cidade ou país em que ele foi desenvolvido! Todos os códigos que funcionam com as coordenadas GPS, valores de data e hora e moedas podem ser afetado por configurações de localidade e o local do cliente. Todas as plataformas oferecem um simulador que permite especificar diferentes locais e localidades - usá-lo para locais de teste em outros hemisférios e com culturas que formatam datas e moedas de maneira diferente. Valores de Latitude e longitude podem ser positivo ou negativo, o separador decimal pode ser um ponto ou uma vírgula e datas podem ser formatadas como uma grande variedade de formas - lidar com ele!
- **Conexões de rede lentas** – os desenvolvedores de aplicativos geralmente trabalham em um 'mundo ideal' de forma rápida, sempre trabalhar conectividade de rede, o que, obviamente, não é o caso no mundo real. Teste com a conectividade de rede lenta (por exemplo, uma conexão 3G ruim), bem como sem acesso de rede é fundamental para garantir que você não enviar um aplicativo cheio de bugs. O processo de aprovação será sempre incluir um teste com o dispositivo no modo de avião, portanto, certifique-se de que você tenha testado que você mesmo.
- **Varia de hardware** – Lembre-se de testar o hardware mais antigo, mais lentas que você planeja oferecer suporte. Há dois aspectos que podem afetar seu aplicativo: desempenho, que pode ser usado em um dispositivo mais antigo e suporte para recursos de hardware, como uma câmera, microfone, GPS, giroscópio ou outro componente opcional. Aplicativos devem degradar normalmente (e não falhe) quando um componente está indisponível.

### <a name="guidelines-are-more-than-just-a-guide"></a>As diretrizes são mais do que apenas um 'guia'

Apple é famosa por ser rigoroso com aderência às diretrizes da Interface humana como uma das principais vantagens de sua plataforma é a consistência (e o aumento percebido na usabilidade). Microsoft adotou uma abordagem semelhante com aplicativos do Windows implementando a interface do usuário do estilo Metro. O processo de aprovação para ambas as plataformas envolverá a seu aplicativo que está sendo avaliado para a sua conformidade com a filosofia de design relevantes.

Isso não quer dizer que inovação de interface do usuário não tem suporte ou incentivada, mas existem certas coisas que você "simplesmente não deve fazer" ou seu aplicativo será rejeitado.

No iOS, isso inclui o uso indevido internos ícones ou usando outras bem estabelecidas metáforas de maneira não-consistente; Por exemplo, usando o ícone 'compor' para algo diferente de criar o novo conteúdo.

Os desenvolvedores do Windows devem ser da mesma forma cuidadosos; um erro comum está falhando suportar adequadamente o voltar do hardware de botão de acordo com as diretrizes da Microsoft.

Incentive seus designers para ler e seguir as diretrizes de design para cada plataforma.

### <a name="implementing-platform-specific-features"></a>Implementando recursos específicos à plataforma

As coisas são um pouco mais rígidas que diz respeito à implementação específica da plataforma de serviços, especialmente no iOS. Para evitar a rejeição de automático pela Apple, há algumas regras a seguir com os seguintes recursos do iOS:

- **Compras no aplicativo** – os aplicativos não devem implementar mecanismos de pagamento externo para produtos digitais, incluindo a moeda do jogo, recursos de aplicativo, assinaturas de revista e muito mais. aplicativos iOS devem usar o serviço com base no iTunes para esse tipo de funcionalidade da Apple. Não há uma brecha - aplicativos, como o leitor Kindle e alguns aplicativos baseados em assinatura permitem que você comprar o conteúdo em outro lugar que é anexado a uma "conta" que pode ser acessado por meio do aplicativo, porém nesse caso, o aplicativo não deve conter links ou referências à fora do aplicativo processo de compra (ou, mais uma vez, ela será rejeitada).
- **backup do iCloud** – com o advento do iCloud, os revisores da Apple são muito mais estritos sobre como os aplicativos usam armazenamento (para garantir a experiência de backup remoto do cliente é agradável). Aplicativos que desperdiçam capaz de backup do espaço de armazenamento pode obter rejeitado, portanto, use a pasta de Cache adequadamente e siga Apple do outras diretrizes relacionadas ao armazenamento.
- **Newsstand** – jornais e revistos aplicativos são uma excelente opção para Apple Newsstand, no entanto, aplicativos devem implementar pelo menos um a renovação automática assinatura e suporte de download em segundo plano para ser aprovado.
- **Mapeia** – é cada vez mais comum para adicionar sobreposições e outros recursos para mapas móveis, porém não cuidado ocultar o mapa 'créditos' informações (como o logotipo do Google em iOS5) pois isso resultará na rejeição.

### <a name="manage-your-metadata"></a>Gerenciar seus metadados

Além dos problemas técnicos óbvios que podem resultar em um aplicativo que está sendo rejeitado, há alguns mais sutis aspectos do seu envio que pode resultar na rejeição, especialmente em torno de metadados (Descrição, as palavras-chave e imagens de marketing) que você Envie com seu aplicativo para exibição no Marketplace ou Store do aplicativo.

- **Imagens** – siga as diretrizes da plataforma para os ícones de aplicativo e armazenar imagens. Não use imagens de marca registradas, já vimos aplicativos obterem rejeitados porque seus ícones em Destaque um desenho de um iPhone!
- **Marcas comerciais** – Evite usar marcas comerciais diferentes do seu próprio. Aplicativos foi negados para mencionar registradas na descrição do aplicativo ou até mesmo em das palavras-chave App Store da Apple.
- **Descrição** – não use a palavra 'beta' ou de qualquer forma indicar que o aplicativo não está pronto para o horário nobre. Não mencione a outras plataformas móveis (mesmo que seu aplicativo é uma plataforma cruzada). Mais importante, certificar-se de que o aplicativo faz exatamente o que você diz que ele faz. Se você listar um monte de recursos em sua descrição, ele deve ser óbvio como usar cada um desses recursos, ou você obterá uma rejeição "mencionado na descrição do aplicativo de recurso não foi implementado".

Coloque muito esforço nos metadados do aplicativo em desenvolvimento e teste. Aplicativos obter rejeitados para infrações secundárias nos metadados, portanto, vale a pena demorando acertar.

### <a name="app-stores-not-for-everyone"></a>Lojas de aplicativos: Não para todos

O principal foco das lojas em cada plataforma é a distribuição de consumidor - a capacidade alcançar clientes tantos quanto possível. No entanto, nem todos os aplicativos são destinados a consumidores, há um rápido crescimento base de aplicativos internamente e extranet como exigem a distribuição de limitado para funcionários, fornecedores ou clientes. Esses aplicativos não são "para venda" e não precisam de aprovação, desde a distribuição de controles de desenvolvedor a um grupo de usuários de fechado.
Suporte para esse tipo de implantação varia por plataforma.

Android oferece mais flexibilidade em relação a isso: aplicativos podem ser instalados diretamente de um anexo de email ou URL (desde que ele permite a configuração do dispositivo). Isso significa que é comum para criar e distribuir aplicativos corporativos internos ou publicar aplicativos para clientes específicos ou fornecedores.

A Apple fornece uma opção de implantação interna para os desenvolvedores registrados no programa corporativo do desenvolvedor, que ignora o processo de aprovação da App Store e permite que as empresas distribuir aplicativos internamente para os funcionários do iOS.
Infelizmente essa licença não atender à necessidade de distribuição de aplicativos de extranet semelhante a outros grupos fechados de clientes ou fornecedores. [Enterprise (e Ad Hoc) implantação](~/ios/deploy-test/app-distribution/ipa-support.md)

### <a name="app-store-summary"></a>Resumo da App Store

O processo de revisão pode ser assustador, mas como o restante do ciclo de vida de desenvolvimento você pode ajudar a garantir o sucesso com algum planejamento e atenção aos detalhes. Tudo se resume a algumas etapas simples: ler e entender as diretrizes de interface do usuário você deve cumprir para seguir as regras, se você estiver implementando recursos específicos à plataforma, testar minuciosamente (em seguida, testar mais alguns) e, finalmente, verifique se os metadados de aplicativo está correto antes de enviar.

Uma última palavra de aviso para os desenvolvedores publicando no Google Play: a falta de processo de aprovação pode parecer que ela facilita seu trabalho - mas seus clientes será ainda mais exigentes do que uma equipe de revisão. Siga estas diretrizes que seu aplicativo pode ser rejeitado, caso contrário, será seus clientes fazendo o rejeitar.
