---
title: Usando a cidade de equipe com o Xamarin
description: Este guia discute as etapas envolvidas com o uso TeamCity para compilar aplicativos móveis e, em seguida, enviá-los ao Xamarin Test Cloud.
ms.prod: xamarin
ms.assetid: AC2626CB-28A7-4808-B2A9-789D67899546
author: lobrien
ms.author: laobri
ms.date: 03/23/2017
ms.openlocfilehash: a3cb79f33b64d933aa8ab4d3555479cc16238992
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61218814"
---
# <a name="using-team-city-with-xamarin"></a>Usando a cidade de equipe com o Xamarin

_Este guia discute as etapas envolvidas com o uso TeamCity para compilar aplicativos móveis e, em seguida, enviá-los ao Xamarin Test Cloud._

Conforme discutido na [Introdução à integração contínua](~/tools/ci/intro-to-ci.md) guia, CI (integração contínua) é uma prática útil durante o desenvolvimento de aplicativos móveis de qualidade. Há muitas opções viáveis para software de servidor de integração contínua; Este guia se concentrará em [TeamCity](http://www.jetbrains.com/teamcity/) da JetBrains.

Há várias permutações diferentes de uma instalação do TeamCity. A seguir está uma lista de alguns deles:

- **Serviço Windows** – nesse cenário, o TeamCity começa quando o Windows é inicializado como um serviço do Windows. Ele deve ser emparelhado com um Host de Build do Mac para compilar todos os aplicativos iOS.

- **Iniciar o Daemon nos X** – conceitualmente, isso é muito semelhante à execução como um serviço do Windows descrito na etapa anterior. Por padrão, as compilações serão executadas sob a conta raiz.

- **Conta de usuário nos X** – é possível executar o TeamCity sob uma conta de usuário que inicia a cada vez que o usuário fizer logon.

Dos cenários anteriores, TeamCity em execução em uma conta de usuário nos X é a mais simples e mais fácil para a instalação.

Há várias etapas envolvidas na configuração do TeamCity:

- **Instalando o TeamCity** – a instalação do TeamCity não é abordada neste guia. Este guia pressupõe que o TeamCity é instalado e em execução em uma conta de usuário. Instruções sobre [instalando o TeamCity](http://confluence.jetbrains.com/display/TCD8/Installation) podem ser encontradas na [TeamCity 8 documentação](http://confluence.jetbrains.com/display/TCD8/TeamCity+Documentation) da JetBrains.

- **Preparando o servidor de compilação** – essa etapa envolve a instalação do software necessário, ferramentas, e certificados necessários para criar aplicativos móveis e prepará-las para distribuição.

- **Criando Script de compilação de um** – essa etapa não é estritamente necessária, mas um script de compilação é útil para a criação de aplicativos autônomos. Usar um script de compilação ajudará a solução de problemas de compilação que podem surgir e fornece uma maneira consistente e reproduzível para criar os binários para distribuição, mesmo que não é praticada de integração contínua.

- **Criando um projeto de TeamCity** – depois que as três etapas anteriores forem concluídas, devemos criar um projeto do TeamCity que conterá todos os metadados necessários para recuperar o código-fonte, compile os projetos e enviar os testes ao Xamarin Test Cloud.

## <a name="requirements"></a>Requisitos

Experiência com o [Xamarin Test Cloud](https://developer.xamarin.com/guides/testcloud) é necessária.

Familiaridade com o TeamCity 8.1 é necessária. A instalação do TeamCity está além do escopo deste documento. Supõe-se que o TeamCity é instalado em OS X Mavericks e está em execução na conta de usuário normal e não a conta raiz.

O servidor de compilação deve ser um computador autônomo, executando o OS X, que é dedicado a integração contínua. O ideal é que o servidor de compilação não será responsável por quaisquer outras funções, como um servidor de banco de dados, o servidor web ou a estação de trabalho do desenvolvedor.

> [!IMPORTANT]
> Este guia não abrange uma "sem periféricos" instalação do Xamarin.

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="preparing-the-build-server"></a>Preparando o servidor de compilação

É uma etapa crucial na configuração de um servidor de compilação instalar todas as ferramentas necessárias, software e certificados para compilar aplicativos móveis. É importante que o servidor de compilação seja capaz de compilar a solução móvel e executar todos os testes. Para minimizar problemas de configuração, as ferramentas e software devem ser instaladas na mesma conta de usuário que está hospedando o TeamCity. A seguir está uma lista do que é necessário:

1. **O Visual Studio para Mac** – isso inclui o xamarin. IOS e xamarin. Android.
2. **Logon para a Store do componente Xamarin** – essa é uma etapa opcional e só é necessário se o seu aplicativo usa componentes do armazenamento de componente do Xamarin. Proativamente registro em log para o repositório do componente neste momento impedirá que os problemas quando um build TeamCity tenta compilar o aplicativo.
3. **Xcode** – Xcode é necessário para compilação e entre aplicativos do iOS.
4. **Ferramentas de linha de comando do Xcode** – isso é descrito na etapa 1 da seção de instalação dos [atualizando Ruby com rbenv](https://developer.xamarin.com/guides/testcloud/calabash/updating-ruby-using-rbenv/) guia.
5. **Perfis de provisionamento e a identidade de assinatura** – importe os certificados e provisionamento de perfil por meio do XCode. Consulte no guia da Apple [exportar identidades de assinatura e perfis de provisionamento](https://developer.apple.com/library/ios/recipes/xcode_help-accounts_preferences/articles/export_signing_assets.html) para obter mais detalhes.
6. **Repositório de chaves Android** – copiar o repositório de chaves Android necessários para um diretório que o usuário TeamCity tem acesso, ou seja, `~/Documents/keystores/MyAndroidApp1`.
7. **Calabash** – essa é uma etapa opcional, se seu aplicativo tem testes escritos usando o Calabash. Consulte a [instalando o Calabash nos X Mavericks](https://developer.xamarin.com/guides/testcloud/calabash/osx-installation/) guia e o [atualizando Ruby com rbenv](https://developer.xamarin.com/guides/testcloud/calabash/updating-ruby-using-rbenv/) guia para obter mais informações.

O diagrama a seguir ilustra a todos esses componentes:

![](teamcity-images/image1.png "Este diagrama ilustra a todos esses componentes")

Depois que todo o software foi instalado, faça logon conta de usuário e confirme se todo o software foi instalado corretamente e está funcionando. Isso deve envolver a compilar a solução e enviando o aplicativo ao Test Cloud. Isso pode ser bastante simplificado, executando o script de compilação, conforme descrito na próxima seção.

## <a name="create-a-build-script"></a>Criar um Script de compilação

Embora seja totalmente possível para TeamCity lidar com todos os aspectos da compilação e enviar os aplicativos móveis no Test Cloud por si só, é altamente recomendável criar um script de compilação. Um script de compilação fornece as seguintes vantagens:

1. **Documentação** – um script de compilação serve como uma forma de documentação sobre como o software é criado. Isso remove algumas "mágicas" que está associado com a implantação do aplicativo e permite que os desenvolvedores a se concentrar na funcionalidade.
1. **Capacidade de repetição** – um script de build garante que cada vez que o aplicativo é compilado e implantado, ele acontece exatamente da mesma maneira, independentemente de quem ou o que faz o trabalho. Essa consistência repetível remove quaisquer problemas ou erros que podem surgir devido a uma compilação executada incorretamente ou erro humano.
1. **Controle de versão** – um script de compilação pode ser incluído no sistema de controle de origem. Isso significa que as alterações para o script de compilação podem ser rastreadas, monitoradas e corrigidas se erros ou imprecisões forem encontradas.
1. **Preparar o ambiente** – um script de compilação pode incluir a lógica para instalar qualquer necessária 3ª dependências de terceiros. Isso garantirá que os aplicativos sejam criados com os componentes apropriados.

O script de compilação pode ser tão simple quanto um arquivo do Powershell (no Windows) ou um script de bash (nos X). Ao criar o script de compilação, há várias opções de linguagens de script:

- [**Rake** ](https://github.com/jimweirich/rake) – isso é uma linguagem específica de domínio (DSL) para a criação de projetos, com base em Ruby. Rake tem a vantagem de popularidade e um rico ecossistema de bibliotecas.

- [**psake** ](https://github.com/psake/psake) – essa é uma biblioteca do Windows Powershell para a criação de software

- [**FORJAR** ](http://fsharp.github.io/FAKE/) – isso é uma DSL com base em F# que torna possível utilizar bibliotecas .NET existentes, se necessário.

Qual linguagem de script é usada depende de suas necessidades e preferências. O [TaskyPro Calabash](https://github.com/xamarin/test-cloud-samples/tree/master/TaskyPro/TaskyPro-Calabash) exemplo contém um exemplo de uso Rake como uma [script da build](https://github.com/xamarin/test-cloud-samples/blob/master/TaskyPro/TaskyPro-Calabash/Rakefile).

> [!NOTE]
> É possível usar um sistema de compilação baseada em XML, como o MSBuild ou NAnt, mas esses falta a expressividade e a facilidade de manutenção de uma DSL dedicado à criação de software.

### <a name="parameterizing-the-build-script"></a>Parametrizar o Script de compilação

O processo de criação e teste de software requer informações que devem ser mantidas em segredo. Em particular a criação de um APK pode exigir uma senha para o repositório de chaves e/ou o alias de chave no repositório de chaves. Da mesma forma, o Test Cloud requer uma chave de API que é exclusiva para um desenvolvedor. Esses tipos de valores não devem ser difícil codificado no script de compilação. Em vez disso, eles devem ser passados como variáveis no script de compilação.

Menos confidenciais são valores como ID do dispositivo ou a ID do dispositivo Android que identificam quais dispositivos de teste de nuvem deve usar para testar o iOS é executado. Esses não são valores que precisam ser protegidos, mas eles podem alterar de compilação para compilação.

Armazenar esses tipos de variáveis fora do script de compilação também torna mais fácil de compartilhar, por exemplo, o script de compilação dentro de uma organização, com os desenvolvedores. Os desenvolvedores podem usar o mesmo script como o servidor de compilação, mas podem usar seus próprios armazenamentos de chave e chaves de API.

Há duas opções possíveis para armazenar esses valores confidenciais:

- **Um arquivo de configuração** – para proteger a chave de API de nuvem de teste, esse valor não deve ser verificado no controle de versão. O arquivo pode ser criado para cada máquina. Como valores são lidos a partir desse arquivo depende a linguagem de script usada.

- **Variáveis de ambiente** – podem ser facilmente definidos em uma base por máquina e partilhada independente da linguagem de script subjacente.

Há vantagens e desvantagens em cada uma dessas opções. O TeamCity funciona bem com variáveis de ambiente, portanto, este guia será recomendável que essa técnica durante a criação de scripts de compilação.

### <a name="build-steps"></a>Etapas de build

O script de compilação deve ser capaz de executar as seguintes etapas:

- **Compilar o aplicativo** – isso inclui a assinatura do aplicativo com o perfil de provisionamento correto.

- **Envie o aplicativo ao Xamarin Test Cloud** – isso inclui a assinatura e zip alinhando o APK com a chave de armazenamento apropriado.

Essas duas etapas serão explicadas mais detalhadamente abaixo.

#### <a name="compiling-a-xamarinios-application"></a>Compilar um aplicativo xamarin. IOS

[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

#### <a name="compiling-a-xamarinandroid-application"></a>Compilar um aplicativo xamarin. Android

Para compilar um aplicativo do Android, use **xbuild** (ou **msbuild** no Windows):

```bash
/Library/Frameworks/Mono.framework/Commands/xbuild /t:SignAndroidPackage /p:Configuration=Release /path/to/android.csproj
```

Observe que, para compilar o aplicativo Xamarin Android **xbuild** usa o projeto e que, para compilar o aplicativo do iOS **xbuild** requer que a solução.

#### <a name="submitting-xamarinuitests-to-test-cloud"></a>Enviando uitests ao Test Cloud

UITests são enviadas usando o `test-cloud.exe` aplicativo, conforme mostrado no trecho a seguir:

```bash
test-cloud.exe <path-to-apk-or-ipa-file> <test-cloud-team-api-key> --devices <device-selection-id> --assembly-dir <path-to-tests-containing-test-assemblies> --nunit-xml report.xml --user <email>
```

Quando o teste é executado, os resultados do teste serão retornados na forma de um arquivo XML de estilo de NUnit chamado **Report**. TeamCity exibirá as informações no Log de compilação.

Para obter mais informações sobre como enviar UITests ao Test Cloud, consulte este guia em [uitests Preparando para o Upload](/appcenter/test-cloud/preparing-for-upload/uitest/).

#### <a name="submitting-calabash-tests-to-test-cloud"></a>Enviando Calabash testes para testar a nuvem

Testes de Calabash são enviadas usando o `test-cloud` gem, conforme mostrado no trecho a seguir:

```bash
test-cloud submit /path/to/APK-or-IPA <test-cloud-team-api-key> --devices <device-id> --user <email>
```
Para enviar um aplicativo do Android no Test Cloud, é necessário primeiro recrie o servidor de teste do APK usando o calabash android:

```bash
$ calabash-android build </path/to/signed/APK>
$ test-cloud submit /path/to/APK <test-cloud-team-api-key> --devices <ANDROID_DEVICE_ID> --profile=android --config=config/cucumber.yml --pretty
```
Para obter mais informações sobre como enviar testes Calabash, consulte Guia do Xamarin em [enviando de testes de Calabash no Test Cloud](https://developer.xamarin.com/guides/testcloud/calabash/working-with/submitting-tests-to-xamarin-test-cloud/).

## <a name="creating-a-teamcity-project"></a>Criando um projeto do TeamCity

Depois que o TeamCity é instalado e o Visual Studio para Mac pode compilar seu projeto, é hora de criar um projeto no TeamCity compilar o projeto e enviá-la ao Test Cloud.

1. Iniciado fazendo logon no TeamCity por meio do navegador da web. Navegue até o projeto raiz:

    ![Navegue até o projeto raiz](teamcity-images/image2.png "navegar até o projeto raiz") sob o projeto raiz, crie um novo projeto de subpropriedades:

    ![Navegue até a raiz projeto sob o projeto raiz, crie um novo projeto de subpropriedades](teamcity-images/image3.png "navegar para a raiz projeto sob o projeto raiz, crie um novo projeto de subdiretório")
2. Quando o subprojeto tiver sido criado, adicione uma nova configuração de compilação:

    ![Quando o subprojeto tiver sido criado, adicione uma nova configuração de Build](teamcity-images/image5.png "quando o subprojeto tiver sido criado, adicione uma nova configuração de Build")
3. Anexe um projeto de VCS à configuração de Build. Isso é feito por meio da tela de configuração de controle de versão:

    ![Isso é feito por meio da tela de configuração de controle de versão](teamcity-images/image6.png "isso é feito por meio da tela de configuração de controle de versão")

    Se não houver nenhum projeto VCS criado, você tem a opção de criá-lo na página nova raiz de VCS mostrada abaixo:

    ![Se não houver nenhum projeto VCS criado, você tem a opção para criar uma página nova raiz de VCS](teamcity-images/image7.png "se não houver nenhum projeto VCS criado, você tem a opção para criar uma página nova raiz de VCS")

    Depois que tiver sido anexada na raiz de VCS, TeamCity irá fazer check-out do projeto e tentar auto detectam as etapas de compilação. Se você estiver familiarizado com o TeamCity, em seguida, você pode selecionar uma das etapas de build detectados. É seguro ignorar as etapas de build detectados por enquanto.

4. Em seguida, configure um disparador de compilação. Isso irá enfileirar uma compilação quando determinadas condições forem atendidas, como quando um usuário confirma o código no repositório. Captura de tela a seguir mostra como adicionar um disparador de compilação:

    ![Esta captura de tela mostra como adicionar um disparador de compilação](teamcity-images/image8.png "nesta captura de tela mostra como adicionar um disparador de compilação") um exemplo de configuração de um disparador de compilação pode ser visto na captura de tela a seguir:

    ![Um exemplo de configuração de um disparador de compilação pode ser visto nesta captura de tela](teamcity-images/image9.png "um exemplo de configuração de um disparador de compilação pode ser visto nesta captura de tela")

5. A seção anterior, parametrizar o Script de compilação, sugerida armazenando alguns valores como variáveis de ambiente. Essas variáveis podem ser adicionadas à configuração de compilação por meio da tela de parâmetros. Adicione as variáveis para a chave de API do teste de nuvem, a ID do dispositivo iOS e Android a ID do dispositivo conforme mostrado na captura de tela abaixo:

    ![Adicione as variáveis para a chave de API do teste de nuvem, a ID do dispositivo iOS e a ID do dispositivo Android](teamcity-images/image11.png "adicione as variáveis para a chave de API do teste de nuvem, a ID do dispositivo iOS e a ID do dispositivo Android")

6. A etapa final é adicionar uma etapa de compilação que invocará o script de build para compilar o aplicativo e enfileirar o aplicativo ao Test Cloud. Captura de tela a seguir está um exemplo de uma etapa de compilação que usa um Rakefile para criar um aplicativo:

    ![Esta captura de tela é um exemplo de uma etapa de compilação que usa um Rakefile para criar um aplicativo](teamcity-images/image12.png "nesta captura de tela é um exemplo de uma etapa de compilação que usa um Rakefile para criar um aplicativo")

7. Neste ponto, a configuração de compilação for concluída. É uma boa ideia para disparar uma compilação para confirmar se o projeto está configurado corretamente. Uma boa maneira de fazer isso é confirmar uma alteração de pequena e insignificante no repositório. TeamCity deve detectar a confirmação e iniciar uma compilação.

8. Quando o build for concluído, inspecione o log de compilação e ver se há quaisquer problemas ou avisos com a compilação que exigem atenção.

## <a name="summary"></a>Resumo

Este guia abordou como usar TeamCity para criação de aplicativos móveis do Xamarin e, em seguida, enviá-los ao Test Cloud. Discutimos a criação de um script de compilação para automatizar o processo de compilação. O script de compilação se encarrega de compilar o aplicativo, enviando ao Test Cloud e aguardar os resultados

Em seguida, abordamos como criar um projeto no TeamCity que irá enfileirar uma compilação sempre que um desenvolvedor confirma o código e chamará o script de compilação.

## <a name="related-links"></a>Links relacionados

- [Preparando uitests fpr Upload](/appcenter/test-cloud/preparing-for-upload/uitest/)
- [Instalando e configurando o TeamCity](http://confluence.jetbrains.com/display/TCD8/Installing+and+Configuring+the+TeamCity+Server)
