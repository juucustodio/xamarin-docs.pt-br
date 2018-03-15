#<a name="---"></a>---
título: "Parte 6 – testes e App Store aprovações" MS. Topic: artigo MS. prod: MS. AssetID do xamarin: 46E0578A-7EB9-C105-ABB0-A043E501F36B MS. Technology: xamarin multiplataforma autor: asb3993 Author: amburns MS. Date: 23/03/2017
---

# <a name="part-6---testing-and-app-store-approvals"></a>Parte 6 - teste e aprovações de loja de aplicativos

<a name="Testing" />


## <a name="testing"></a>Testes

Muitos aplicativos (aplicativos Android até mesmo, em algumas lojas) serão necessário passar um processo de aprovação antes de serem publicados; para que o teste é essencial para garantir seu aplicativo atinge o mercado (sem falar for bem-sucedida com seus clientes). Teste pode ter várias formas, do gerenciamento de teste beta em uma ampla variedade de hardware de teste de unidade de nível de desenvolvedor.

 <a name="Test_on_All_Platforms" />


### <a name="test-on-all-platforms"></a>Testar em todas as plataformas

Há pequenas diferenças entre o .NET oferece suporte a no Windows phone, tablet e dispositivos de área de trabalho, bem como limitações no iOS que impedem que o código dinâmico a ser gerado dinamicamente. O plano no teste do código em várias plataformas, como desenvolvê-lo, ou agendar um horário para Refatorar e atualizar a parte do modelo do seu aplicativo no final do projeto.

É sempre uma boa prática para usar o simulador/emulador para testar várias versões do sistema operacional e também recursos/configurações de dispositivos diferentes.

Você também deve testar em tantos dispositivos de hardware físico diferente, como é possível.

 <a name="Devices_in_cloud" />


#### <a name="devices-in-cloud"></a>Dispositivos na nuvem

O ecossistema de telefone e tablet móvel está aumentando o tempo todo, impossibilitando testar o número crescente de dispositivos disponíveis. Para resolver esse problema, que um número de serviços oferece a capacidade de controlar remotamente diferentes dispositivos para que os aplicativos podem ser instalados e testados sem a necessidade de investir diretamente em lotes de hardware.

[Teste de aplicativo Center](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest) oferece uma maneira fácil de testar aplicativos iOS e Android em centenas de diferentes dispositivos.

 <a name="Test_Management" />


### <a name="test-management"></a>Gerenciamento de testes

Ao testar aplicativos em sua organização ou gerenciar um programa beta com usuários externos, há dois desafios:

-   **Distribuição** – gerenciar o processo de provisionamento (especialmente para dispositivos iOS) e obter versões atualizadas do software para os testadores.
-   **Comentários** – coletar informações sobre o uso do aplicativo e informações detalhadas sobre quaisquer erros que podem ocorrer.


Há um número de serviços ajuda a resolver esses problemas, fornecendo a infraestrutura que é criada em seu aplicativo para coletar e relatar os erros e uso e também simplificando o processo de provisionamento para ajudar a inscrição e gerenciar seus dispositivos e testadores .

O [Xamarin Insights visualização](http://xamarin.com/insights) oferece uma solução para a segunda parte desse problema, fornecendo informações de uso de aplicativo de emissão de relatórios e sofisticados de falha.


 <a name="Test_Automation" />


### <a name="test-automation"></a>Automação de teste

Xamarin [UITest](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest) pode ser usado para criar scripts de teste que podem ser executados localmente ou carregados de interface do usuário automatizados [aplicativo Center testar](https://docs.microsoft.com/appcenter/test-cloud/).


 <a name="Unit_Testing" />


## <a name="unit-testing"></a>Teste de unidade

 <a name="Touch.Unit" />


#### <a name="touchunit"></a>Touch.Unit

Xamarin inclui uma estrutura de teste de unidade chamada Touch.Unit que segue o estilo de JUnit/NUnit escrever testes.

Consulte a nossa [testes de unidade com xamarin](~/ios/deploy-test/touch.unit.md) documentação para obter detalhes sobre como escrever testes e em execução Touch.Unit.

 <a name="Andr.Unit" />


#### <a name="andrunit"></a>Andr.Unit

Há um equivalente do código-fonte aberto de Touch.Unit para Android chamado Andr.Unit. Você pode baixá-lo do [github](https://github.com/spouliot/Andr.Unit) e leia sobre a ferramenta no [ @spouliotdo blog](http://spouliot.wordpress.com/2011/10/30/andr-unit-joins-the-family/).

 <a name="Windows_Phone" />


#### <a name="windows-phone"></a>Windows Phone

Aqui estão alguns links para ajudar a instalação teste de unidade para Windows Phone:

-   [http://www.jeff.wilcox.name/2010/05/sl3-utf-bits/](http://www.jeff.wilcox.name/2010/05/sl3-utf-bits/)
-   [http://www.jeff.wilcox.name/2011/06/updated-ut-mango-bits/](http://www.jeff.wilcox.name/2011/06/updated-ut-mango-bits/)
-   [http://www.smartypantscoding.com/a-cheat-sheet-for-unit-testing-silverlight-apps-on-windows-phone-7](http://www.smartypantscoding.com/a-cheat-sheet-for-unit-testing-silverlight-apps-on-windows-phone-7)
-   [http://mobile.dzone.com/articles/unit-testing-your-windows](http://mobile.dzone.com/articles/unit-testing-your-windows)


&nbsp;

 <a name="App_Store_Approvals" />


## <a name="app-store-approvals"></a>Aprovações de loja de aplicativos

Apple e a Microsoft operam o repositório somente nas plataformas do: o repositório de aplicativo e o Marketplace respectivamente. Ambos confinamento seus dispositivos e implementem um processo de revisão de aplicativo rigorosos para controlar a qualidade dos aplicativos disponíveis para download. Natureza aberta do Android significa que há um número de opções de armazenamento desde Play do Google, que é amplamente disponível e não tem nenhum processo de revisão, Appstore do Amazon para esforços Android e específicos de hardware, como aplicativos Samsung distribuição mais limitada e implemente um processo de aprovação.

Esperando por um aplicativo a ser revisado pode ser muito desgastante - pressões de negócios geralmente significam que aplicativos são enviados para aprovação com muito pouco margem de erro antes de uma data de início "destino". O próprio processo pode levar até duas semanas e não é necessariamente transparente: há limitado de comentários sobre o progresso do seu aplicativo até finalmente rejeitada ou aprovada. Rejeição pode significar faltando uma janela de marketing de oportunidade, especialmente se ele ocorre mais de uma vez e semanas passam entre a data de lançamento original e quando o aplicativo é aprovado por último.

 <a name="Be_prepared" />


### <a name="be-prepared"></a>Prepare-se

A primeira parte do aviso: planejar antecipadamente o lançamento do seu aplicativo e fazer concessões para uma possível rejeição e envio novamente. Cada repositório exige que você criar uma conta antes de enviar seu aplicativo-fazer isso mais cedo possível.
Durante a inscrição do Google Play só leva alguns minutos, se seus aplicativos estiver livres, o processo obtém muito mais envolvido se você está vendendo-los e precisa fornecer serviços bancários e informações de imposto. Apple e processos da Microsoft sejam muito mais envolvida que do Google, pode levar uma semana ou mais para obter sua conta aprovada para influenciar neste momento seus planos de inicialização.

Depois de sua conta tiver sido aprovada, você estará pronto para enviar um aplicativo. O processo de envio de aplicativos é abordado na seguinte documentação:

-   [Publicando em iOS da Apple App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
-   [Preparando um aplicativo para o Google Play](~/android/deploy-test/publishing/publishing-to-google-play/index.md)
-  Os desenvolvedores do Windows devem visitar o [Centro de desenvolvimento do Windows](https://developer.microsoft.com/en-us/windows/windows-apps) para ler sobre o envio de seus aplicativos.


O restante desta seção discute as coisas que você deve levar em consideração para garantir que seu aplicativo é aprovado sem quaisquer interrupções.

 <a name="Quality" />


### <a name="quality"></a>Qualidade

Parece óbvio, mas aplicativos geralmente serão recusados porque eles não atendem a um determinado nível de qualidade: Afinal, este é o motivo por que os repositórios estruturados tem um processo de aprovação em primeiro lugar!

Falhas são das razões comuns de rejeição. Se ele é muito fácil de fazer a falha de aplicativo, garantir que sejam rejeitadas. A maioria dos desenvolvedores não enviar seus aplicativos com a expectativa de que eles vai falhar, mas normalmente. Teste seu aplicativo cuidadosamente antes de enviá-lo, concentrando-se de que não apenas em certificando-se que tudo está funcionando, mas também que você lidar com cenários comuns de erro móveis, como problemas de rede e restrições de recursos como memória ou espaço de armazenamento. Use dispositivos físicos e o simulador para testar - independentemente de como o código é executado em um simulador, somente um dispositivo pode demonstrar o desempenho real do aplicativo. Use como vários dispositivos diferentes, como você pode localizar e inscrever-se uma equipe de testadores beta se é possível - serviços de terceiros podem ajudar a gerenciar comentários e distribuição beta.

Todos os sistemas operacionais móveis finalizará a um aplicativo que não iniciam rápido o suficiente. O período de tempo permitido varia, mas em geral a aplicativos devem visar à resposta em alguns segundos e usar tarefas em segundo plano para executar qualquer trabalho que poderia levar mais tempo. Aplicativos que levam muito tempo para carregar ou não responde no uso normal de são serão rejeitadas. Quando algo está acontecendo em segundo plano ou o aplicativo será exibido falharam e mais uma vez, ser rejeitado sempre fornece os comentários do usuário.

 <a name="Check_Your_Edge_Cases" />


### <a name="check-your-edge-cases"></a>Verifique os casos de borda

Uma interceptação comum para os desenvolvedores está falhando para casos de borda do endereço, especialmente aqueles que exigem reconfiguração seu simulador ou dispositivo para testar corretamente. É fácil esquecer que nem todo cliente será "Permitir" seu aplicativo acesse seu local, porque depois que o desenvolvedor aceitou a solicitação de uma vez, eles nunca vai ser solicitados novamente. Permissões e uso de rede são focussed especificamente no durante o processo de aprovação, o que significa que um pequeno supervisão nessas áreas pode resultar na rejeição.

A lista a seguir é um bom ponto de partida para verificar casos de borda que podem ter sido perdidos:

-   **Os clientes podem 'Negar' acesso aos serviços** – especialmente no iOS, o acesso aos dados, como informações de localização geográfica são fornecidas apenas se o usuário concede permissão para seu aplicativo. Testadores aplicativo frequentemente devem reinstalar o aplicativo em seu estado inicial e impedir que as solicitações de permissão para garantir que o aplicativo se comporta adequadamente. Alternar permissão em e desligada para verificar o comportamento correto enquanto os clientes mudar sua ideia.
-   **Os clientes estão em qualquer lugar** – não suponha que um aplicativo só será usado na cidade ou país em que ele foi desenvolvido! Todo código que funciona com as coordenadas GPS, valores de data e hora e moedas pode ser afetado por configurações de localidade e o local do cliente. Todas as plataformas oferecem um simulador que lhe permitem especificar locais diferentes e localidades - usá-lo para locais de teste em outros hemisférios e culturas que formatar datas e moedas de maneira diferente. Valores de Latitude e longitude podem ser positivo ou negativo, o separador decimal pode ser um ponto ou uma vírgula e datas podem ser formatadas como uma série de maneiras - lidar com ele!
-   **Conexão de rede lenta** – os desenvolvedores de aplicativos geralmente trabalham em um 'mundo ideal' de forma rápida, sempre trabalhar conectividade de rede, obviamente, não é o caso do mundo real. Teste com conectividade de rede lenta (por exemplo, uma conexão 3G baixo), bem como sem acesso de rede é essencial para garantir que você não enviar um aplicativo com bugs. O processo de aprovação será sempre incluir um teste com o dispositivo no modo avião, portanto, certifique-se de que você tenha testado que você mesmo.
-   **Varia de hardware** – Lembre-se de testar o hardware mais antigo, mais lento que você planeja oferecer suporte. Há dois aspectos que podem afetar seu aplicativo: desempenho, que pode ser usado em um dispositivo mais antigas e suporte para recursos de hardware, como uma câmera, microfone, GPS, giroscópio ou outros componentes opcionais. Aplicativos devem degradar normalmente (e não falhe) quando um componente está indisponível.


 <a name="Guidelines_are_more_than_just_a_‘guide’" />


### <a name="guidelines-are-more-than-just-a-guide"></a>As diretrizes são mais do que apenas um 'guia'

Apple é famosa por ser estrito sobre conformidade com suas diretrizes de Interface humana como uma das principais vantagens de sua plataforma de consistência (e o aumento percebido de usabilidade). Microsoft obteve uma abordagem semelhante com aplicativos Implementando a interface do usuário do estilo Metro do Windows. O processo de aprovação para ambas as plataformas envolve a seu aplicativo está sendo avaliado em sua conformidade com a filosofia de design relevantes.

Isso não quer dizer que inovação de interface de usuário não tem suporte ou incentivada, mas existem certas coisas que você "apenas não deve fazer" ou seu aplicativo será rejeitado.

No iOS, isso inclui o uso indevido ícones internos ou usando outros metáforas bem estabelecidas de forma não consistente; Por exemplo, usando o ícone 'Redigir' para algo diferente de criar o novo conteúdo.

Os desenvolvedores do Windows devem ser da mesma forma cuidadosos; um erro comum está falhando para suporte o hardware volta corretamente botão de acordo com as diretrizes da Microsoft.

Incentive seus designers ler e seguir as diretrizes de design para cada plataforma.

 <a name="Implementing_Platform-Specific_Features" />


### <a name="implementing-platform-specific-features"></a>Implementando recursos específicos de plataforma

As coisas são um pouco mais rígidas quando se trata de implementação de serviços de plataforma específica, especialmente em iOS. Para evitar rejeição de automático pela Apple, há algumas regras a seguir com os seguintes recursos do iOS:

-   **Compras no aplicativo** – os aplicativos não devem implementar mecanismos de pagamento externo para produtos digitais, incluindo a moeda do jogo, recursos de aplicativo, assinaturas de revista e muito mais. aplicativos iOS devem usar o serviço baseado em iTunes da Apple para este tipo de funcionalidade. Há um loophole - aplicativos que o leitor Kindle e alguns aplicativos com base em assinatura permitem que você adquirir conteúdo em outro local que é anexado a uma "conta" que pode ser acessado por meio do aplicativo, porém nesse caso o aplicativo não deve conter links ou referências a fora do aplicativo processo de compra (ou, mais uma vez, ela será rejeitada).
-   **backup do iCloud** – com o surgimento de iCloud, revisores da Apple são muito mais estrito sobre como os aplicativos usar armazenamento (para garantir a experiência de backup remoto do cliente é agradável). Aplicativos que o espaço de armazenamento de backup pode resíduos pode obter rejeitado, portanto, use a pasta de Cache adequadamente e siga Apple as outras diretrizes relacionadas ao armazenamento.
-   **Newsstand** – jornal e revistos aplicativos são uma excelente opção para Newsstand da Apple, mas os aplicativos devem implementar pelo menos uma renovação automática assinatura e suporte de download em segundo plano para ser aprovado.
-   **Mapeia** – é cada vez mais comum para adicionar sobreposições e outros recursos para mapas móveis, porém não cuidado ocultar o mapa 'créditos' informações (como o logotipo do Google em iOS5), isso resultará na rejeição.


 <a name="Manage_Your_Metadata" />


### <a name="manage-your-metadata"></a>Gerenciar seus metadados

Além dos problemas técnicos óbvios que podem resultar em um aplicativo que estão sendo rejeitado, há aspectos mais sutis de envio que pode resultar na rejeição, especialmente em torno de metadados (Descrição, palavras-chave e imagens de marketing) que você Envie com seu aplicativo para exibição dentro da loja de aplicativos ou do Marketplace.

-   **Imagens** – siga as diretrizes da plataforma de ícones de aplicativo e armazenar imagens. Não use imagens de marca registradas, vimos aplicativos obtenham rejeitados porque seus ícones em Destaque um desenho de um iPhone!
-   **Marcas comerciais** – Evite usar marcas comerciais diferentes do seu próprio. Aplicativos negados para mencionar registradas na descrição do aplicativo ou até mesmo nas palavras-chave na loja de aplicativos da Apple.
-   **Descrição** – não use a palavra 'beta' ou de qualquer forma indicar que o aplicativo não está pronto para a. Não mencione outras plataformas móveis (mesmo se seu aplicativo estiver plataforma cruzada). Mais importante, certifique-se do que aplicativo não exatamente o que diz faz. Se você listar um conjunto de recursos em sua descrição, ele deve ser óbvio como usar cada um desses recursos ou você receberá uma rejeição "mencionado na descrição do aplicativo de recurso não foi implementado".


Coloque o máximo esforço nos metadados do aplicativo em desenvolvimento e teste. Aplicativos recusados para violações secundárias nos metadados para vale a pena demorando corretamente.

 <a name="App_Stores:_Not_For_Everyone" />


### <a name="app-stores-not-for-everyone"></a>Lojas de aplicativos: Não para todos

O foco principal das lojas em cada plataforma é a distribuição de consumidor - a capacidade de alcançar tantos clientes quanto possível. No entanto, nem todos os aplicativos são direcionados consumidores, há uma rápida expansão base de aplicativos internos e de extranet semelhante que exigem a distribuição limitada para funcionários, fornecedores ou clientes. Esses aplicativos não são "para venda" e não precisam de aprovação, desde que a distribuição de controles de desenvolvedor para um grupo fechado de usuários.
Suporte para esse tipo de implantação varia por plataforma.

Android oferece mais flexibilidade em relação a isso: aplicativos podem ser instalados diretamente de um anexo de email ou URL (desde que configuração do dispositivo permite que ele). Isso significa que é comum para criar e distribuir aplicativos corporativos internos ou publicar aplicativos para clientes específicos ou fornecedores.

Apple fornece uma opção de implantação interna aos desenvolvedores registrados no iOS programa para desenvolvedores corporativos, que ignora o processo de aprovação da App Store e permite que as empresas distribuir aplicativos internos para seus funcionários.
Infelizmente esta licença não atender à necessidade de distribuição de aplicativos de extranet semelhante a outros grupos de clientes ou fornecedores de fechado. [Enterprise (e Ad Hoc) implantação](~/ios/deploy-test/app-distribution/ipa-support.md)


 <a name="App_Store_Summary" />


### <a name="app-store-summary"></a>Resumo da loja de aplicativos

O processo de análise pode ser desanimadora, mas como o restante do ciclo de vida de desenvolvimento você pode ajudar a garantir o sucesso com algum planejamento e atenção aos detalhes. Isso afetará a algumas etapas simples: Leia e compreenda as diretrizes de interface de usuário você deve cumprir para seguir as regras, se você estiver implementando recursos específicos de plataforma, teste completamente (e testar outros) e, finalmente, verifique se os metadados de aplicativo está correto antes de enviar.

Uma última palavra de aviso para desenvolvedores de publicação no Google Play: falta de processo de aprovação pode parecer que facilita o trabalho - mas os clientes serão ainda mais exigentes do que uma equipe de análise. Siga estas diretrizes que seu aplicativo pode obter rejeitado, caso contrário, será seus clientes a fazer a rejeição.

