---
title: Usando o Team City com Xamarin
description: Este guia discutirá as etapas envolvidas com o uso do TeamCity para compilar aplicativos móveis e, em seguida, submetê-los ao App Center Test.
ms.prod: xamarin
ms.assetid: AC2626CB-28A7-4808-B2A9-789D67899546
author: davidortinau
ms.author: daortin
ms.date: 04/01/2020
ms.openlocfilehash: 6ecd453180e8c392ba7d7527778617eb40950a9e
ms.sourcegitcommit: 6f3281a32017cfcebadde8a2d6e10651a277828f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80587463"
---
# <a name="using-team-city-with-xamarin"></a>Usando o Team City com Xamarin

_Este guia discutirá as etapas envolvidas com o uso do TeamCity para compilar aplicativos móveis e, em seguida, submetê-los ao App Center Test._

Conforme discutido no guia [introdução à integração contínua, a](~/tools/ci/intro-to-ci.md) integração contínua (CI) é uma prática útil no desenvolvimento de aplicações móveis de qualidade. Existem muitas opções viáveis para software de servidor de integração contínua; este guia se concentrará no [TeamCity](https://www.jetbrains.com/teamcity/) da JetBrains.

Existem várias permutações diferentes de uma instalação do TeamCity. A lista a seguir descreve algumas dessas permutações:

- **Windows Service** – Neste cenário, o TeamCity é iniciado quando o Windows é inicializado como um Serviço windows. Ele deve ser emparelhado com um Mac Build Host para compilar quaisquer aplicativos iOS.

- **Launch Daemon no OS X** – Conceitualmente, isso é semelhante ao executado como um Windows Service descrito na etapa anterior. Por padrão, as compilações serão executadas sob a conta raiz.

- **Conta de usuário no OS X** – É possível executar o TeamCity em uma conta de usuário que é iniciada cada vez que o usuário faz login.

Dos cenários anteriores, executar o TeamCity sob uma conta de usuário no OS X é o mais simples e fácil de configurar.

Existem várias etapas envolvidas na criação do TeamCity:

- **Instalação do TeamCity** – A instalação do TeamCity não está coberta neste guia. Este guia pressupõe que o TeamCity esteja instalado e executado sob uma conta de usuário. Instruções sobre [a instalação do TeamCity](https://confluence.jetbrains.com/display/TCD8/Installation) podem ser encontradas na [documentação TeamCity 8](https://confluence.jetbrains.com/display/TCD8/TeamCity+Documentation) pela JetBrains.

- **Preparação do Build Server** – Esta etapa envolve a instalação dos softwares, ferramentas e certificados necessários para construir aplicativos móveis e prepará-los para distribuição.

- **Criar um script de construção** – Esta etapa não é estritamente necessária, mas um script de compilação é uma ajuda útil para construir aplicativos sem vigilância. O uso de um script de compilação ajudará a solucionar problemas de construção que possam surgir e fornece uma maneira consistente e repetitiva de criar os binários para distribuição, mesmo que a integração contínua não seja praticada.

- **Criando um projeto TeamCity** – Uma vez concluídas as três etapas anteriores, devemos criar um projeto TeamCity que conterá todos os meta-dados necessários para recuperar o código-fonte, compilar os projetos e enviar os testes para o Teste do Centro de Aplicativos.

## <a name="requirements"></a>Requisitos

É necessária experiência com [o App Center Test.](https://docs.microsoft.com/appcenter/test-cloud/)

A familiaridade com o TeamCity 8.1 é necessária. A instalação do TeamCity está além do escopo deste documento. Presume-se que o TeamCity está instalado no OS X Mavericks e está sendo executado sob uma conta de usuário regular e não na conta raiz.

O servidor de compilação deve ser um computador autônomo, executando o OS X, que é dedicado à integração contínua. Idealmente, o servidor de compilação não será responsável por outras funções, como um servidor de banco de dados, servidor web ou estação de trabalho do desenvolvedor.

> [!IMPORTANT]
> Este guia não cobre uma instalação "sem cabeça" de Xamarin.

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="preparing-the-build-server"></a>Preparando o Build Server

Um passo crucial na configuração de um servidor de compilação é instalar todas as ferramentas, softwares e certificados necessários para construir os aplicativos móveis. É importante que o servidor de compilação possa compilar a solução móvel e executar quaisquer testes. Para minimizar problemas de configuração, o software e as ferramentas devem ser instalados na mesma conta de usuário que está hospedando o TeamCity. A lista a seguir detalha o que é necessário:

1. **Visual Studio para Mac** – Isso inclui Xamarin.iOS e Xamarin.Android.
2. **Faça login na Loja de Componentes Xamarin** – Esta etapa é opcional e só é necessária se o aplicativo usar componentes da loja de componentes Xamarin. O login ativo na loja component neste momento evitará quaisquer problemas quando uma compilação do TeamCity tentar compilar o aplicativo.
3. **Xcode** – O Xcode é necessário para compilar e assinar aplicativos iOS.
4. **Ferramentas de linha de comando Xcode** – Isso é descrito na etapa 1 da seção Instalação do Ruby atualizando com o guia [rbenv.](https://github.com/calabash/calabash-ios/wiki)
5. **Assinatura de perfis de provisionamento de identidade & provisionamento** – Importe os certificados e o perfil de provisionamento via XCode. Consulte o guia da Apple sobre [exportação de identidades de assinatura e perfis de provisionamento](https://developer.apple.com/library/ios/recipes/xcode_help-accounts_preferences/articles/export_signing_assets.html) para obter mais detalhes.
6. **Lojas de chaves Android** – Copie as lojas de chaves do Android necessárias `~/Documents/keystores/MyAndroidApp1`para um diretório ao que o usuário do TeamCity tenha acesso, ou seja.
7. **Calabash** – Esta é uma etapa opcional se a sua aplicação tiver testes escritos usando Calabash. Consulte o [guia Installing Calabash no OS X Mavericks](https://github.com/calabash/calabash-ios/wiki) e o Guia De Atualização ruby com o guia [rbenv](https://github.com/calabash/calabash-ios/wiki) para obter mais informações.

O diagrama a seguir ilustra todos esses componentes:

![](teamcity-images/image1.png "This diagram illustrates all of these components")

Uma vez que todo o software esteja instalado, faça login na conta do usuário e confirme se todo o software está instalado e funcionando corretamente. Isso deve envolver a compilação da solução e o envio do aplicativo para o App Center Test. Isso pode ser simplificado executando o script de compilação, conforme descrito na próxima seção.

## <a name="create-a-build-script"></a>Criar um script de construção

Embora seja possível para a TeamCity lidar com todos os aspectos da compilação e envio de aplicativos móveis para o App Center Test por si só; é recomendado criar um script de compilação. Um script de compilação oferece as seguintes vantagens:

1. **Documentação** – Um script de compilação serve como uma forma de documentação sobre como o software é construído. Isso remove parte da "magia" que está associada à implantação do aplicativo e permite que os desenvolvedores se concentrem na funcionalidade.
1. **Repetibilidade** – Um script de compilação garante que cada vez que o aplicativo é compilado e implantado, ele acontece da mesma forma, independentemente de quem ou o que faz o trabalho. Esta consistência repetível remove quaisquer problemas ou erros que possam aparecer devido a uma compilação executada incorretamente ou erro humano.
1. **Versionamento** – Um script de compilação pode ser incluído no sistema de controle de origem. Isso significa que as alterações no script de compilação podem ser rastreadas, monitoradas e corrigidas se erros ou imprecisões forem encontrados.
1. **Prepare o ambiente** – Um script de compilação pode incluir lógica para instalar quaisquer dependências de terceiros necessárias. Isso garantirá que as aplicações sejam construídas com os componentes adequados.

O script de compilação pode ser tão simples quanto um arquivo PowerShell (no Windows) ou um script bash (no OS X). Ao criar o script de compilação, existem várias opções para scripting de idiomas:

- [**Rake**](https://github.com/jimweirich/rake) – esta é uma Linguagem Específica de Domínio (DSL) para projetos de construção, baseada em Ruby. Rake tem a vantagem da popularidade e um rico ecossistema de bibliotecas.

- [**psake**](https://github.com/psake/psake) – esta é uma biblioteca do Windows PowerShell para construir software

- [**FAKE**](https://fsharp.github.io/FAKE/) – este é um DSL baseado em F# que torna possível o uso de bibliotecas .NET existentes, se necessário.

Qual linguagem de script é usada depende de suas preferências e requisitos.

> [!NOTE]
> É possível usar um sistema de construção baseado em XML, como o MSBuild ou o NAnt, mas estes não têm a expressividade e a manutenção de um DSL dedicado à construção de software.

### <a name="parameterizing-the-build-script"></a>Parametrização do script de construção

O processo de construção e teste de software requer informações que devem ser mantidas em segredo. A criação de um APK pode exigir uma senha para o armazenamento de chaves e/ou o alias de chave no armazenamento de chaves. Da mesma forma, o App Center Test requer uma [chave de API](/appcenter/api-docs/) exclusiva para um desenvolvedor. Esses tipos de valores não devem ser codificados no script de compilação. Em vez disso, eles devem ser passados como variáveis para o script de compilação.

Menos sensíveis são valores como o ID do dispositivo iOS ou o ID do dispositivo Android que identificam quais dispositivos o App Center deve usar para testes executados. Estes não são valores que precisam ser protegidos, mas podem mudar de construção para construção.

Armazenar esses tipos de variáveis fora do script de compilação também facilita o compartilhamento do script de compilação dentro de uma organização, com desenvolvedores, por exemplo. Os desenvolvedores podem usar exatamente o mesmo script que o servidor de compilação, mas podem usar suas próprias keystores e [chaves de API](/appcenter/api-docs/).

Existem duas opções possíveis para armazenar esses valores sensíveis:

- **Um arquivo de configuração** – Para proteger a [chave da API,](/appcenter/api-docs/) esse valor não deve ser verificado no controle da versão. O arquivo pode ser criado para cada máquina. A forma como os valores são lidos a partir deste arquivo depende da linguagem de scriptusada.

- **Variáveis de ambiente** – elas podem ser facilmente definidas por máquina e são independentes da linguagem de scriptsubjacente.

Há vantagens e desvantagens para cada uma dessas escolhas. TeamCity funciona bem com variáveis de ambiente, então este guia recomendará essa técnica ao criar scripts de construção.

### <a name="build-steps"></a>Construir etapas

O script de compilação deve fazer as seguintes etapas:

- **Compilar o aplicativo** – Isso inclui assinar o aplicativo com o perfil de provisionamento correto.

- **Envie o aplicativo para Xamarin Test Cloud** – Isso inclui assinar e zip alinhando o APK com o keystore apropriado.

Estas duas etapas serão explicadas com mais detalhes abaixo.

#### <a name="compiling-a-xamarinios-application"></a>Compilando um aplicativo Xamarin.iOS

[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

#### <a name="compiling-a-xamarinandroid-application"></a>Compilando um aplicativo Xamarin.Android

Para compilar um aplicativo Android, use **xbuild** (ou **msbuild** no Windows):

```bash
/Library/Frameworks/Mono.framework/Commands/xbuild /t:SignAndroidPackage /p:Configuration=Release /path/to/android.csproj
```
Compilar o aplicativo android **xbuild** usa o projeto, enquanto o aplicativo iOS **xbuild** usa a solução.

#### <a name="submitting-xamarinuitests-to-app-center"></a>Enviando testes xamarin.ui para o App Center

Os testes de ui são enviados usando o [APP Center CLI](https://github.com/microsoft/appcenter-cli), conforme mostrado no trecho a seguir:

```bash
appcenter test run uitest --app <TEAM-NAME/APP-NAME> --devices <DEVICE_SET> --token <API_KEY> --app-path <appname.APK-or-appname.IPA> --merge-nunit-xml report.xml --build-dir pathToUITestBuildDir
```

Quando o teste for executado, os resultados do teste serão devolvidos na forma de um arquivo XML estilo NUnit chamado **report.xml**. TeamCity exibirá as informações no Registro de Compilação.

Para obter mais informações sobre como enviar testes de interface do usuário para o App Center, consulte [Preparando aplicativos Xamarin.Android](/appcenter/test-cloud/uitest/preparing-for-upload-android) ou [preparando aplicativos Xamarin.iOS](/appcenter/test-cloud/uitest/preparing-for-upload-ios).

#### <a name="submitting-calabash-tests-to-app-center"></a>Enviando testes de Calabash para o App Center

Os testes de Calabash são submetidos usando o [App Center CLI](https://github.com/microsoft/appcenter-cli), conforme mostrado no trecho a seguir:

```bash
appcenter test run calabash --app <TEAM-NAME/APP-NAME> --devices <DEVICE_SET> --token <API_KEY> --app-path <appname.APK-or-appname.IPA> --project-dir pathToProjectDir
```

Para enviar um aplicativo Android para o App Center Test, é necessário primeiro reconstruir o servidor de teste APK usando calabash-android:

```bash
$ calabash-android build </path/to/signed/APK>
$ appcenter test run calabash --app <TEAM-NAME/APP-NAME> --devices <DEVICE_SET> --token <API_KEY> --app-path <appname.APK> --project-dir pathToProjectDir
```

Para obter mais informações sobre o envio de testes de Calabash, consulte o guia de Xamarin sobre [o envio de testes de Calabash para test cloud](https://github.com/calabash/calabash-ios/wiki).

## <a name="creating-a-teamcity-project"></a>Criando um Projeto TeamCity

Uma vez que o TeamCity seja instalado e o Visual Studio para Mac possa construir seu projeto, é hora de criar um projeto no TeamCity para construir o projeto e enviá-lo ao App Center.

1. Começou fazendo login no TeamCity através do navegador da Web. Navegue até o Projeto Raiz:

    ![Navegue até o Projeto Raiz](teamcity-images/image2.png "Navegue até o Projeto Raiz") Abaixo do Projeto Raiz, crie um novo subprojeto:

    ![Navegue até o projeto raiz abaixo do projeto raiz, crie um novo subprojeto](teamcity-images/image3.png "Navegue até o projeto raiz abaixo do projeto raiz, crie um novo subprojeto")
2. Uma vez que o subprojeto seja criado, adicione uma nova configuração de compilação:

    ![Uma vez que o subprojeto seja criado, adicione uma nova configuração de compilação](teamcity-images/image5.png "Uma vez que o subprojeto tenha sido criado, adicione uma nova configuração de compilação")
3. Anexar um projeto VCS à configuração de compilação. Isso é feito através da tela configuração de controle de versão:

    ![Isso é feito através da tela configuração de controle de versão](teamcity-images/image6.png "Isso é feito através da tela configuração de controle de versão")

    Se não houver nenhum projeto VCS criado, você pode criar um a partir da página Raiz do Novo VCS mostrada abaixo:

    ![Se não houver nenhum projeto VCS criado, você pode criar um a partir da página Raiz do Novo VCS](teamcity-images/image7.png "Se não houver nenhum projeto VCS criado, você tem a opção de criar um a partir da página Raiz do Novo VCS")

    Uma vez que a raiz VCS tenha sido anexada, o TeamCity verificará o projeto e tentará detectar automaticamente as etapas de compilação. Se você estiver familiarizado com o TeamCity, então você pode selecionar uma das etapas de compilação detectadas. É seguro ignorar as etapas de construção detectadas por enquanto.

4. Em seguida, configure um gatilho de compilação. Isso enfileirará uma compilação quando determinadas condições forem atendidas, como quando um usuário compromete o código no repositório. A captura de tela a seguir mostra como adicionar um gatilho de compilação:

    ![Esta captura de tela mostra como adicionar um gatilho de compilação](teamcity-images/image8.png "Esta captura de tela mostra como adicionar um gatilho de compilação") Um exemplo de configuração de um gatilho de compilação pode ser visto na captura de tela a seguir:

    ![Um exemplo de configuração de um gatilho de compilação pode ser visto nesta captura de tela](teamcity-images/image9.png "Um exemplo de configuração de um gatilho de compilação pode ser visto nesta captura de tela")

5. A seção anterior, Parametrizando o Script de Compilação, sugeriu armazenar alguns valores como variáveis de ambiente. Essas variáveis podem ser adicionadas à configuração de compilação através da tela Parâmetros. Adicione as variáveis para a [chave de API](/appcenter/api-docs/)do App Center, o ID do dispositivo iOS e o ID do dispositivo Android, conforme mostrado na captura de tela abaixo:

    ![Adicione as variáveis para a chave de aPI de teste do App Center, o ID do dispositivo iOS e o ID do dispositivo Android](teamcity-images/image11.png "Adicione as variáveis para a chave de API da nuvem de teste, o ID do dispositivo iOS e o ID do dispositivo Android")

6. O passo final é adicionar uma etapa de compilação que invoque o script de compilação para compilar o aplicativo e enfileirar o aplicativo para o Teste do Centro de Aplicativos. A captura de tela a seguir é um exemplo de uma etapa de compilação que usa um Rakefile para construir um aplicativo:

    ![Esta captura de tela é um exemplo de uma etapa de compilação que usa um Rakefile para construir um aplicativo](teamcity-images/image12.png "Esta captura de tela é um exemplo de uma etapa de compilação que usa um Rakefile para construir um aplicativo")

7. Neste ponto, a configuração de compilação está completa. É uma boa idéia ativar uma compilação para confirmar que o projeto está devidamente configurado. Uma boa maneira de fazer isso é cometer uma pequena e insignificante mudança no repositório. TeamCity deve detectar o commit e iniciar uma compilação.

8. Uma vez concluída a compilação, inspecione o registro de construção e veja se há algum problema ou aviso com a construção que requerem atenção.

## <a name="summary"></a>Resumo

Este guia cobriu como usar o TeamCity para criar aplicativos Xamarin Mobile e, em seguida, enviá-los para o Teste do App Center. Discutimos a criação de um script de compilação para automatizar o processo de compilação. O script de compilação cuida da compilação do aplicativo, submetendo-se ao Teste do Centro de Aplicativos e aguardando os resultados.

Em seguida, cobrimos como criar um projeto no TeamCity que enfileirará uma compilação cada vez que um desenvolvedor compromete o código e chamará o script de compilação.

## <a name="related-links"></a>Links relacionados

- [Preparando aplicativos Xamarin.Android](/appcenter/test-cloud/uitest/preparing-for-upload-android)
- [Preparando aplicativos Xamarin.iOS](/appcenter/test-cloud/uitest/preparing-for-upload-ios)
- [Instalação e Configuração do TeamCity](https://confluence.jetbrains.com/display/TCD8/Installing+and+Configuring+the+TeamCity+Server)
