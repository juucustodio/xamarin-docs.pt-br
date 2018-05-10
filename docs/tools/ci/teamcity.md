---
title: Usando a cidade de equipe com o Xamarin
description: Este guia discutir as etapas envolvidas em usar TeamCity para compilar aplicativos móveis e, em seguida, enviá-los para Xamarin Test Cloud.
ms.prod: xamarin
ms.assetid: AC2626CB-28A7-4808-B2A9-789D67899546
author: topgenorth
ms.author: toopge
ms.date: 03/23/2017
ms.openlocfilehash: 32338bc89df2ef7ee4426482b1967861f0c0e058
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2018
---
# <a name="using-team-city-with-xamarin"></a>Usando a cidade de equipe com o Xamarin

_Este guia discutir as etapas envolvidas em usar TeamCity para compilar aplicativos móveis e, em seguida, enviá-los para Xamarin Test Cloud._

Como discutido o [Introdução à integração contínua](~/tools/ci/intro-to-ci.md) guia, CI (integração contínua) é uma prática útil durante o desenvolvimento de aplicativos móveis de qualidade. Há muitas opções viáveis para software de servidor de integração contínua; Este guia se concentrará em [TeamCity](http://www.jetbrains.com/teamcity/) da JetBrains.

Há várias permutações diferentes de uma instalação TeamCity. A seguir está uma lista de alguns deles:

- **Serviço Windows** – nesse cenário, TeamCity começa quando o Windows é inicializado como um serviço do Windows. Ele deve estar combinado com um Host de compilação do Mac para compilar todos os aplicativos iOS.

- **Iniciar o Daemon nos X** – conceitualmente, isso é muito semelhante à execução como um serviço do Windows descrito na etapa anterior. Por padrão, as compilações serão executadas sob a conta raiz.

- **Conta de usuário nos X** – é possível executar TeamCity em uma conta de usuário que inicia a cada vez que o usuário fizer logon.

Dos cenários anteriores, executando TeamCity sob uma conta de usuário nos X é a mais simples e mais fácil de instalação.

Há várias etapas envolvidas na configuração do TeamCity:

- **Instalando TeamCity** – a instalação do TeamCity não é abordada neste guia. Este guia pressupõe que TeamCity está instalado e em execução em uma conta de usuário. Instruções sobre [instalando TeamCity](http://confluence.jetbrains.com/display/TCD8/Installation) pode ser encontrado no [TeamCity 8 documentação](http://confluence.jetbrains.com/display/TCD8/TeamCity+Documentation) por JetBrains.

- **Preparando o servidor de compilação** – essa etapa envolve a instalação do software necessário, ferramentas, e os certificados necessários para criar aplicativos móveis e prepará-los para distribuição.

- **Criando Script de compilação de um** – essa etapa não é estritamente necessária, mas um script de compilação é útil para a criação de aplicativos autônomos. Usando um script de compilação ajuda com solução de problemas de compilação que podem surgir e fornece uma maneira consistente e reproduzível de criar os binários de distribuição, mesmo se não for praticada de integração contínua.

- **Criando um projeto de TeamCity** – depois que as três etapas anteriores forem concluídas, é necessário criar um projeto de TeamCity que irá conter todos os metadados necessários para recuperar o código-fonte, os projetos de compilação e enviar os testes do Xamarin Test Cloud.

## <a name="requirements"></a>Requisitos

Experiência com [Xamarin Test Cloud](https://developer.xamarin.com/guides/testcloud) é necessária.

Familiaridade com TeamCity 8.1 é necessária. A instalação do TeamCity está além do escopo deste documento. Presume-se que TeamCity está instalado nos X Mavericks e está sendo executado sob uma conta de usuário normal e não a conta raiz.

O servidor de compilação deve ser um computador autônomo, executando o OS X, que é dedicado a integração contínua. Idealmente, o servidor de compilação não será responsável por outras funções, como um servidor de banco de dados, um servidor web ou uma estação de trabalho do desenvolvedor.

> [!IMPORTANT]
> Este guia não abrange uma instalação de "configurações" do Xamarin.

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="preparing-the-build-server"></a>Preparando o servidor de compilação

Uma etapa crucial na configuração de um servidor de compilação é instalar todas as ferramentas necessárias, software e certificados para criar os aplicativos móveis. É importante que o servidor de compilação ser capaz de compilar a solução móvel e executar testes. Para minimizar problemas de configuração, as ferramentas e o software devem ser instaladas na mesma conta de usuário que está hospedando o TeamCity. A seguir está uma lista do que é necessário:

1. **O Visual Studio para Mac** – isso inclui o xamarin e xamarin.
2. **Logon para o armazenamento do componente Xamarin** – isso é uma etapa opcional e só é necessário se o seu aplicativo usa os componentes do armazenamento de componente Xamarin. Log proativamente para o repositório de componente neste momento impedirá problemas quando uma compilação TeamCity tenta compilar o aplicativo.
3. **Xcode** – Xcode é necessário a compilação e entre aplicativos do iOS.
4. **Ferramentas de linha de comando do Xcode** – isso é descrito na etapa 1 da seção de instalação do [atualização Ruby com rbenv](https://developer.xamarin.com/guides/testcloud/calabash/updating-ruby-using-rbenv/) guia.
5. **Assinatura de perfis de provisionamento e identidade** – importar os certificados e provisionamento de perfil por meio do XCode. Consulte o guia da Apple em [exportar identidades de assinatura e perfis de provisionamento](https://developer.apple.com/library/ios/recipes/xcode_help-accounts_preferences/articles/export_signing_assets.html) para obter mais detalhes.
6. **Repositório de chaves Android** – copie o repositório de chaves Android necessários para um diretório que o usuário TeamCity tem acesso, ou seja, `~/Documents/keystores/MyAndroidApp1`.
7. **Calabash** – essa é uma etapa opcional, se seu aplicativo tiver testes escritos usando Calabash. Consulte o [Calabash instalando nos X Mavericks](https://developer.xamarin.com/guides/testcloud/calabash/osx-installation/) guia e o [atualização Ruby com rbenv](https://developer.xamarin.com/guides/testcloud/calabash/updating-ruby-using-rbenv/) guia para obter mais informações.

O diagrama a seguir ilustra a todos esses componentes:

![](teamcity-images/image1.png "Este diagrama ilustra todos esses componentes")

Depois que todos os softwares instalados, login para a conta de usuário e confirme se o software foi instalado corretamente e se está funcionando. Isso deve envolver compilar a solução e enviando o aplicativo teste de nuvem. Isso pode ser bastante simplificado ao executar o script de compilação, conforme descrito na próxima seção.

## <a name="create-a-build-script"></a>Criar um Script de compilação

Embora seja possível totalmente TeamCity lidar com todos os aspectos da compilação e do envio de aplicativos móveis para a nuvem de teste por si só, é altamente recomendável criar um script de compilação. Um script de compilação oferece as seguintes vantagens:

1. **Documentação** – um script de compilação serve como uma forma de documentação sobre como o software é criado. Isso remove alguns "mágica" que está associado ao implantar o aplicativo e permite que os desenvolvedores se concentre na funcionalidade.
1. **Capacidade de repetição** – um script de compilação garante que cada vez que o aplicativo é compilado e implantado, ele acontece exatamente da mesma maneira, independentemente de quem ou o que faz o trabalho. Essa consistência repeatable remove quaisquer problemas ou erros que podem surgir em devido a uma compilação executada incorretamente ou erro humano.
1. **Controle de versão** – um script de compilação pode ser incluído no sistema de controle de origem. Isso significa que as alterações do script de compilação podem ser controladas, monitoradas e corrigidas se forem encontrados erros ou imprecisões.
1. **Preparar o ambiente** – um script de compilação pode incluir a lógica para instalar qualquer necessário 3º dependências de terceiros. Isso irá garantir que os aplicativos criados com os componentes apropriados.

O script de compilação pode ser tão simple quanto um arquivo do Powershell (no Windows) ou um script de bash (nos X). Ao criar o script de compilação, há várias opções para linguagens de script:

- [**Rake** ](https://github.com/jimweirich/rake) – isso é uma linguagem específica de domínio (DSL) para a criação de projetos, com base em Ruby. Rake tem a vantagem de popularidade e um rico ecossistema de bibliotecas.

- [**psake** ](https://github.com/psake/psake) – essa é uma biblioteca do Windows Powershell para criar software

- [**FORJAR** ](http://fsharp.github.io/FAKE/) – este é uma DSL com base em F #, que torna possível utilizar bibliotecas .NET existentes, se necessário.

A linguagem de script é usada depende de suas necessidades e preferências. O [TaskyPro Calabash](https://github.com/xamarin/test-cloud-samples/tree/master/TaskyPro/TaskyPro-Calabash) exemplo contém um exemplo de uso Rake como uma [criar script](https://github.com/xamarin/test-cloud-samples/blob/master/TaskyPro/TaskyPro-Calabash/Rakefile).

> [!NOTE]
> É possível usar um sistema de compilação baseadas em XML, como o MSBuild ou NAnt, mas esses falta a expressividade e a facilidade de manutenção de uma DSL dedicado à criação de software.

### <a name="parameterizing-the-build-script"></a>Parametrizando Script de compilação

O processo de criação e teste de software requer informações que devem ser mantidas em segredo. Em particular criar um APK pode exigir uma senha para o armazenamento de chaves e/ou o alias de chave no armazenamento de chaves. Da mesma forma, a nuvem de teste exige uma chave de API que é exclusiva para o desenvolvedor. Esses tipos de valores não devem ser rígidos codificado no script de compilação. Em vez disso, eles devem ser passados como variáveis de script de compilação.

Menos confidenciais são valores, como o ID do dispositivo ou a ID do dispositivo Android que identificar quais dispositivos de teste de nuvem deve usar para teste do iOS é executado. Eles não são valores que precisam ser protegidos, mas eles podem alterar de compilação a compilação.

Armazenar esses tipos de variáveis de fora do script de compilação também torna mais fácil compartilhar o script de compilação de uma organização, com os desenvolvedores, por exemplo. Os desenvolvedores podem usar o mesmo script como o servidor de compilação, mas podem usar seus próprios armazenamentos de chaves e chaves de API.

Há duas opções possíveis para armazenar esses valores confidenciais:

- **Um arquivo de configuração** – para proteger a chave de API de nuvem de teste, esse valor não deve ser verificado para controle de versão. O arquivo pode ser criado para cada máquina. Como os valores são lidos a partir desse arquivo depende de linguagem de script usada.

- **Variáveis de ambiente** – podem ser facilmente definidos em uma base por máquina e ared independente da linguagem de script subjacente.

Há vantagens e desvantagens de cada uma dessas opções. TeamCity funciona bem com variáveis de ambiente para que este guia recomende essa técnica ao criar scripts de compilação.

### <a name="build-steps"></a>Etapas de compilação

O script de compilação deve ser capaz de executar as seguintes etapas:

- **Compilar o aplicativo** – isso inclui a assinatura do aplicativo com o perfil de provisionamento correto.

- **Enviar o aplicativo Xamarin Test Cloud** – isso inclui a assinatura e zip alinhando APK com a chave de armazenamento apropriado.

Essas duas etapas serão explicadas em mais detalhes abaixo.

#### <a name="compiling-a-xamarinios-application"></a>Compilando um aplicativo xamarin

[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

#### <a name="compiling-a-xamarinandroid-application"></a>Compilando um aplicativo xamarin

Para compilar um aplicativo do Android, use **xbuild** (ou **msbuild** no Windows):

```bash
/Library/Frameworks/Mono.framework/Commands/xbuild /t:SignAndroidPackage /p:Configuration=Release /path/to/android.csproj
```

Observe que para compilar o aplicativo Xamarin Android **xbuild** usa o projeto e que para criar o aplicativo iOS **xbuild** requer que a solução.

#### <a name="submitting-xamarinuitests-to-test-cloud"></a>Enviando Xamarin.UITests à nuvem de teste

UITests são enviados usando-se a `test-cloud.exe` aplicativo, conforme mostrado no trecho a seguir:

```bash
test-cloud.exe <path-to-apk-or-ipa-file> <test-cloud-team-api-key> --devices <device-selection-id> --assembly-dir <path-to-tests-containing-test-assemblies> --nunit-xml report.xml --user <email>
```

Quando o teste é executado, os resultados de teste serão retornados na forma de um arquivo XML de estilo de NUnit chamado **report.xml**. TeamCity exibirá as informações no Log de compilação.

Para obter mais informações sobre como enviar UITests à nuvem de teste, consulte o guia do Xamarin em [enviando testes para teste nuvem](https://developer.xamarin.com/guides/testcloud/uitest/working-with/submitting-tests-to-xamarin-test-cloud/).

#### <a name="submitting-calabash-tests-to-test-cloud"></a>Enviando Calabash testes para testar a nuvem

Testes de Calabash são enviadas usando o `test-cloud` gem, conforme mostrado no trecho a seguir:

```bash
test-cloud submit /path/to/APK-or-IPA <test-cloud-team-api-key> --devices <device-id> --user <email>
```
Para enviar um aplicativo do Android para testar a nuvem, é necessário primeiro recrie o servidor de teste APK usando calabash android:

```bash
$ calabash-android build </path/to/signed/APK>
$ test-cloud submit /path/to/APK <test-cloud-team-api-key> --devices <ANDROID_DEVICE_ID> --profile=android --config=config/cucumber.yml --pretty
```
Para obter mais informações sobre como enviar testes Calabash, consulte o guia do Xamarin em [testes Calabash enviando Test Cloud](https://developer.xamarin.com/guides/testcloud/calabash/working-with/submitting-tests-to-xamarin-test-cloud/).

## <a name="creating-a-teamcity-project"></a>Criando um projeto de TeamCity

Quando TeamCity está instalado e o Visual Studio para Mac pode compilar o projeto, é hora de criar um projeto no TeamCity para compilar o projeto e enviá-lo à nuvem de teste.

1. Iniciada pelo logon no TeamCity por meio do navegador da web. Navegue até o projeto raiz:

    ![Navegue até o projeto raiz](teamcity-images/image2.png "navegue para o projeto raiz") sob o projeto raiz, crie um novo projeto de subtipo:

    ![Navegue até o projeto raiz do projeto sob a raiz, crie um novo projeto sub](teamcity-images/image3.png "navegue para o projeto raiz do projeto sob a raiz, crie um novo projeto de subseção")
2. Quando o subprojeto tiver sido criado, adicione uma nova configuração de compilação:

    ![Quando o subprojeto tiver sido criado, adicione uma nova configuração de compilação](teamcity-images/image5.png "quando o subprojeto tiver sido criado, adicione uma nova configuração de compilação")
3. Anexe um projeto de VCS para a configuração de compilação. Isso é feito por meio da tela de configuração de controle de versão:

    ![Isso é feito por meio da tela de configuração de controle de versão](teamcity-images/image6.png "isso é feito por meio da tela de configuração de controle de versão")

    Se não houver nenhum projeto VCS criado, você tem a opção para criar um da página nova raiz VCS mostrada abaixo:

    ![Se não houver nenhum projeto VCS criado, você tem a opção para criar uma página nova raiz VCS](teamcity-images/image7.png "se não houver nenhum projeto VCS criado, você tem a opção para criar uma página nova raiz VCS")

    Depois que a raiz de VCS tenha sido anexada, TeamCity será check-out do projeto e tente automaticamente detectam etapas de compilação. Se você estiver familiarizado com TeamCity, você pode selecionar uma das etapas de compilação detectado. É seguro ignorar as etapas de compilação detectados por enquanto.

4. Em seguida, configure um gatilho de compilação. Isso irá enfileirar uma compilação quando determinadas condições forem atendidas, como quando um usuário confirma o código para o repositório. Captura de tela a seguir mostra como adicionar um gatilho de compilação:

    ![Esta captura de tela mostra como adicionar um gatilho de compilação](teamcity-images/image8.png "nesta captura de tela mostra como adicionar um gatilho de compilação") um exemplo de configuração de um gatilho de compilação pode ser visto na captura de tela a seguir:

    ![Um exemplo de configuração de um gatilho de compilação pode ser visto nesta captura de tela](teamcity-images/image9.png "um exemplo de configuração de um gatilho de compilação pode ser visto nesta captura de tela")

5. A seção anterior, a parametrização de Script de compilação, sugerido armazenar alguns valores de variáveis de ambiente. Essas variáveis podem ser adicionadas para a configuração de compilação por meio da tela de parâmetros. Adicione as variáveis para a chave de API de nuvem de teste, a ID do dispositivo iOS e a ID do dispositivo Android conforme mostrado na captura de tela abaixo:

    ![Adicione as variáveis para a chave de API de nuvem de teste, a ID do dispositivo iOS e a ID do dispositivo Android](teamcity-images/image11.png "adicionar as variáveis para a chave de API de nuvem de teste, a ID do dispositivo iOS e a ID do dispositivo Android")

6. A etapa final é adicionar uma etapa de compilação que invocará o script de compilação para compilar o aplicativo e enfileirar o aplicativo teste de nuvem. Captura de tela a seguir está um exemplo de uma etapa de compilação que usa um arquivo Rake para criar um aplicativo:

    ![Esta captura de tela é um exemplo de uma etapa de compilação que usa um arquivo Rake para criar um aplicativo](teamcity-images/image12.png "nesta captura de tela é um exemplo de uma etapa de compilação que usa um arquivo Rake para criar um aplicativo")

7. Neste ponto, a configuração de compilação for concluída. É uma boa ideia para disparar uma compilação para confirmar que o projeto está configurado corretamente. É uma boa maneira de fazer isso confirmar uma alteração pequena, insignificante no repositório. TeamCity deve detectar a confirmação e iniciar uma compilação.

8. Assim que a compilação for concluída, verifique o log de compilação e verificar se há problemas ou avisos com a compilação que exigem atenção.

## <a name="summary"></a>Resumo

Este guia abordou como usar TeamCity para criação de aplicativos móveis Xamarin e, em seguida, enviá-los para a nuvem de teste. Discutimos a criação de um script de compilação para automatizar o processo de compilação. O script de compilação cuida de compilação do aplicativo, enviando a nuvem de teste e aguardando os resultados

Em seguida, abordamos como criar um projeto no TeamCity que irá enfileirar uma compilação sempre que um desenvolvedor confirma o código e chamará o script de compilação.

## <a name="related-links"></a>Links relacionados

- [Envio de testes para nuvem Xamarin Test (UITest)](https://developer.xamarin.com~/testcloud/uitest/working-with/submitting-tests-to-xamarin-test-cloud/)
- [Envio de testes para nuvem Xamarin Test (Calabash)](https://developer.xamarin.com~/testcloud/calabash/working-with/submitting-tests-to-xamarin-test-cloud/)
- [Instalando e configurando TeamCity](http://confluence.jetbrains.com/display/TCD8/Installing+and+Configuring+the+TeamCity+Server)
