---
title: Processo de build
ms.prod: xamarin
ms.assetid: 3BE5EE1E-3FF6-4E95-7C9F-7B443EE3E94C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/22/2019
ms.openlocfilehash: 982517a0072b130a0c34acb903b47d7e961ec2a5
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69887811"
---
# <a name="build-process"></a>Processo de build

## <a name="overview"></a>Visão geral

O processo de build do Xamarin.Android é responsável por juntar tudo isto: [gerar `Resource.designer.cs`](~/android/internals/api-design.md), dar suporte a `AndroidAsset`, `AndroidResource` e outras [ações de build](#Build_Actions), gerar [wrappers que podem ser chamados pelo Android](~/android/platform/java-integration/android-callable-wrappers.md) e gerar um `.apk` para execução em dispositivos Android.

## <a name="application-packages"></a>Pacotes de aplicativos

Em termos gerais, há dois tipos de pacotes de aplicativos do Android (arquivos `.apk`) que o sistema de build do Xamarin.Android pode gerar:

- Builds de **versão**, que são totalmente independentes e não requerem pacotes adicionais para executar. Esses são os pacotes que seriam fornecidos para uma loja de aplicativos.

- Builds de **depuração**, que não o são.

Não coincidentemente, eles correspondem ao `Configuration` do MSBuild que produz o pacote.

### <a name="shared-runtime"></a>Tempo de execução compartilhado

O *tempo de execução compartilhado* é um par de pacotes Android adicionais que fornecem a biblioteca de classes base (`mscorlib.dll`, etc.) e a biblioteca de associações do Android (`Mono.Android.dll`, etc.). Builds de depuração contam com o tempo de execução compartilhado em vez de incluir os assemblies da biblioteca de classes base e da biblioteca de associações dentro do pacote do aplicativo Android, permitindo que o pacote de depuração seja menor.

O tempo de execução compartilhado pode ser desabilitado em builds de depuração, definindo a propriedade `$(AndroidUseSharedRuntime)` para `False`.

<a name="Fast_Deployment" />

### <a name="fast-deployment"></a>Fast Deployment

O *Fast Deployment* trabalha em conjunto com o tempo de execução compartilhado para reduzir ainda mais o tamanho do pacote do aplicativo Android. Isso é feito não agrupando assemblies do aplicativo dentro do pacote. Em vez disso, eles são copiados para o destino por meio de `adb push`. Esse processo agiliza o ciclo de build/implantação/depuração porque se *somente* assemblies são alterados, o pacote não é reinstalado. Em vez disso, apenas os assemblies atualizados são sincronizados novamente ao dispositivo de destino.

O Fast Deployment reconhecidamente falha em dispositivos que bloqueiam a sincronização de `adb` ao diretório `/data/data/@PACKAGE_NAME@/files/.__override__`.

O Fast Deployment é habilitado por padrão e pode ser desabilitado em builds de depuração, definindo a propriedade `$(EmbedAssembliesIntoApk)` para `True`.


## <a name="msbuild-projects"></a>Projetos do MSBuild

O processo de build do Xamarin.Android baseia-se no MSBuild, que também é o formato de arquivo de projeto usado pelo Visual Studio para Mac e pelo Visual Studio.
Em geral, os usuários não precisarão editar os arquivos do MSBuild manualmente &ndash; o IDE cria projetos totalmente funcionais e os atualiza com eventuais alterações feitas, além de invocar destinos de build automaticamente conforme necessário.

Usuários avançados talvez queiram realizar ações sem suporte na interface gráfica do IDE, portanto, o processo de build pode ser personalizado editando-se o arquivo de projeto diretamente.
Esta página documenta somente os recursos e personalizações específicos do Xamarin.Android &ndash; muitas outras coisas são possíveis com os itens, propriedades e destinos normais do MSBuild.

<a name="Build_Targets" />

## <a name="build-targets"></a>Destinos de build

Os destinos de build a seguir são definidos para projetos de Xamarin.Android:

- **Build** &ndash; compila o pacote.

- **Clean** &ndash; remove todos os arquivos gerados pelo processo de build.

- **Install** &ndash; Instala o pacote no dispositivo padrão ou virtual.

- **Uninstall** &ndash; Desinstala o pacote do dispositivo padrão ou virtual.

- **SignAndroidPackage** &ndash; cria e assina o pacote (`.apk`). Use com `/p:Configuration=Release` para gerar pacotes de "Versão" independentes.

- **UpdateAndroidResources** &ndash; atualiza o arquivo `Resource.designer.cs`. Esse destino geralmente é chamado pelo IDE quando novos recursos são adicionados ao projeto.

## <a name="build-extension-points"></a>Criar pontos de extensão

O sistema de build Xamarin.Android expõe alguns pontos de extensão públicos para os usuários que desejam se conectar ao nosso processo de build. Para usar um desses pontos de extensão, você precisará adicionar seu destino personalizado à propriedade apropriada do MSBuild em um `PropertyGroup`. Por exemplo:

```xml
<PropertyGroup>
   <AfterGenerateAndroidManifest>
      $(AfterGenerateAndroidManifest);
      YourTarget;
   </AfterGenerateAndroidManifest>
</PropertyGroup>
```

Uma palavra de cautela sobre a extensão do processo de build: Se não forem gravadas corretamente, as extensões de build poderão afetar seu desempenho de build, especialmente se forem executadas em todos os builds. É altamente recomendável que você leia a [documentação](https://docs.microsoft.com/visualstudio/msbuild/msbuild) do MSBuild antes de implementar essas extensões.

-   **AfterGenerateAndroidManifest** &ndash; Os destinos listados nesta propriedade serão executados diretamente após o destino `_GenerateJavaStubs` interno. É aqui que o arquivo `AndroidManifest.xml` é gerado no `$(IntermediateOutputPath)`. Portanto, se quiser fazer alguma modificação no arquivo `AndroidManifest.xml` gerado, você poderá fazê-lo usando esse ponto de extensão.

    Adicionado no Xamarin.Android 9.4.

-   **BeforeGenerateAndroidManifest** &ndash; Os destinos listados nesta propriedade serão executados diretamente antes de `_GenerateJavaStubs`.

    Adicionado no Xamarin.Android 9.4.

## <a name="build-properties"></a>Propriedades de build

Propriedades do MSBuild controlam o comportamento dos destinos. Elas são especificadas no arquivo de projeto, por exemplo, **MyApp.csproj**, dentro de um [elemento PropertyGroup do MSBuild](https://docs.microsoft.com/visualstudio/msbuild/propertygroup-element-msbuild).

- **Configuration** &ndash; especifica a configuração de build a ser usada, como "Debug" ou "Release". A propriedade Configuration é usada para determinar os valores padrão de outras propriedades que determinam o comportamento de destino. Configurações adicionais podem ser criadas dentro de seu IDE.

    *Por padrão*, a configuração `Debug` fará com que os destinos `Install` e `SignAndroidPackage` criem um pacote do Android menor, que exigirá a presença de outros arquivos e pacotes para funcionar.

    A configuração padrão `Release` fará com que os destinos `Install` e `SignAndroidPackage` criem um pacote Android *autônomo*, que pode ser usado sem a instalação de outros pacotes ou arquivos.

- **DebugSymbols** &ndash; um valor booliano que determina se o pacote Android é *depurável*, em combinação com a propriedade `$(DebugType)`. Um pacote depurável contém símbolos de depuração, define o atributo `//application/@android:debuggable` para `true` e adiciona automaticamente a permissão `INTERNET` para um depurador possa ser anexado ao processo. Um aplicativo é depurável se `DebugSymbols` é `True` *e* `DebugType` é a cadeia de caracteres vazia ou `Full`.

- **DebugType** &ndash; especifica o [tipo de símbolos de depuração](https://docs.microsoft.com/visualstudio/msbuild/csc-task) para gerar como parte do build, o que também afeta se o aplicativo é ou não depurável. Os possíveis valores incluem:

    - **Full**: todos os símbolos são gerados. Se a propriedade do MSBuild `DebugSymbols` também é `True`, o pacote do aplicativo é depurável.

    - **PdbOnly**: os símbolos "PDB" são gerados. O pacote do aplicativo *não* será depurável.

    Se `DebugType` não está definida ou é uma cadeia de caracteres vazia, a propriedade `DebugSymbols` controla se o Aplicativo é depurável.

    - **AndroidGenerateLayoutBindings** &ndash; Habilita a geração do [code-behind de layout](https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/LayoutCodeBehind.md) se definido como `true` ou desabilita completamente se definido como `false`. O valor padrão é `false`.

### <a name="install-properties"></a>Propriedades de instalação

Propriedades de instalação controlam o comportamento dos destinos `Install` e `Uninstall`.

- **AdbTarget** &ndash; especifica o dispositivo de destino do Android no qual o pacote Android pode ser instalado ou do qual ele pode ser removido. O valor dessa propriedade o mesmo da [opção de dispositivo de destino `adb`](https://developer.android.com/tools/help/adb.html#issuingcommands):

    ```bash
    # Install package onto emulator via -e
    # Use `/Library/Frameworks/Mono.framework/Commands/msbuild` on OS X
    MSBuild /t:Install ProjectName.csproj /p:AdbTarget=-e
    ```


### <a name="packaging-properties"></a>Propriedades de empacotamento

Propriedades de empacotamento controlam a criação do pacote Android e são usadas pelos destinos `Install` e `SignAndroidPackage`.
As [Propriedades de Assinatura](#Signing_Properties) também são relevantes ao empacotar aplicativos de Versão.

- **AndroidApkDigestAlgorithm** &ndash; Um valor de cadeia de caracteres que especifica o algoritmo digest para usar com `jarsigner -digestalg`.

    O valor padrão é `SHA1` para APKs e `SHA-256` para lotes de aplicativos.

    Adicionado no Xamarin.Android 9.4.

- **AndroidApkSignerAdditionalArguments** &ndash; Uma propriedade de cadeia de caracteres que permite ao desenvolvedor fornecer argumentos adicionais à ferramenta `apksigner`.

    Adicionado no Xamarin.Android 8.2.

- **AndroidApkSigningAlgorithm** &ndash; Um valor de cadeia de caracteres que especifica o algoritmo de assinatura para usar com `jarsigner -sigalg`.

    O valor padrão é `md5withRSA` para APKs e `SHA256withRSA` para lotes de aplicativos.

    Adicionado no Xamarin.Android 8.2.

- **AndroidApplication** &ndash; Um valor booliano que indica se o projeto é para um aplicativo Android (`True`) ou para um projeto de biblioteca Android (`False` ou ausente).

    Apenas um projeto com `<AndroidApplication>True</AndroidApplication>` pode estar presente em um pacote Android. (Infelizmente isso é ainda não verificado, que pode resultar em erros sutis e bizarros relacionados aos recursos do Android.)

- **AndroidApplicationJavaClass** &ndash; O nome de classe Java completo a ser usado no lugar de `android.app.Application` quando uma classe herda de [Android.App.Application](xref:Android.App.Application).

    Geralmente, essa propriedade é definida por *outras* propriedades, como `$(AndroidEnableMultiDex)` do MSBuild.

    Adicionado no Xamarin.Android 6.1.

- **AndroidBuildApplicationPackage** &ndash; um valor booliano que indica se o pacote (.apk) deve ou não ser criado e assinado. Definir esse valor como `True` é equivalente a usar o destino de build [SignAndroidPackage](#Build_Targets).

    O suporte para essa propriedade foi adicionado após o Xamarin.Android 7.1.

    Essa propriedade é `False` por padrão.

- **AndroidDexTool** &ndash; uma propriedade de estilo de enumeração com os valores válidos `dx` ou `d8`. Indica qual compilador [dex][dex] do Android é usado durante o processo de build do Xamarin.Android.
    Atualmente, o padrão é `dx`. Para obter mais informações, confira nossa documentação sobre o [D8 e R8][d8-r8].

    [dex]: https://source.android.com/devices/tech/dalvik/dalvik-bytecode
    [d8-r8]: https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/D8andR8.md

- **AndroidEnableDesugar** &ndash; uma propriedade booliana que determina se `desugar` está habilitado. O Android não é compatível com todos os recursos de Java 8 no momento, e a cadeia de ferramentas padrão implementa novos recursos de linguagem ao executar transformações de código de bytes, chamadas `desugar`, na saída do compilador `javac`. O padrão será `False` se `AndroidDexTool=dx` for usado e será `True` se `AndroidDexTool=d8` for usado.

- **AndroidEnableGooglePlayStoreChecks** &ndash; Uma propriedade bool que permite aos desenvolvedores desabilitar as seguintes verificações da Google Play Store: XA1004, XA1005 e XA1006. Isso é útil para os desenvolvedores que não pretendem trabalhar com a Google Play Store e não desejam executar essas verificações.

    Adicionado no Xamarin.Android 9.4.

- **AndroidEnableMultiDex** &ndash; Uma propriedade booliana que determina se o suporte a Multi-Dex será usado no `.apk` final ou não.

    O suporte para essa propriedade foi adicionado no Xamarin.Android 5.1.

    Essa propriedade é `False` por padrão.

- **AndroidEnablePreloadAssemblies** &ndash; Uma propriedade booliana que controla se todos os assemblies gerenciados agrupados dentro do pacote de aplicativos são ou não carregados durante a inicialização do processo.

    Quando definidos como `True`, todos os assemblies agrupados dentro do pacote de aplicativos serão carregados durante a inicialização do processo, antes que qualquer aplicativo de código seja invocado.
    Esse processo é consistente com aquilo que o Xamarin.Android fazia em versões anteriores ao Xamarin.Android 9.2.

    Quando definido como `False`, os assemblies somente serão carregados conforme necessário.
    Isso faz com que os aplicativos iniciem mais rapidamente e também é mais consistente com a semântica da área de trabalho do .NET.  Para ver a economia de tempo, defina a Propriedade do Sistema `debug.mono.log` para incluir `timing` e procure a mensagem `Finished loading assemblies: preloaded` dentro de `adb logcat`.

    Os aplicativos ou as bibliotecas que usam injeção de dependência podem *exigir* que essa propriedade seja `True` se, por sua vez, exigirem que `AppDomain.CurrentDomain.GetAssemblies()` retorne todos os assemblies no pacote de aplicativos, mesmo se o assembly não for necessário.

    Por padrão, o valor será definido como `True`.

    Adicionado no Xamarin.Android 9.2.

- **AndroidEnableProfiledAot** &ndash; Uma propriedade booliana que determina se os perfis AOT são ou não usados durante a compilação Ahead of Time.

    Os perfis são listados no grupo de itens `AndroidAotProfile`. Este ItemGroup contém perfis padrão. Ele pode ser substituído removendo os perfis existentes e adicionando seus próprios perfis AOT.

    O suporte para essa propriedade foi adicionado no Xamarin.Android 9.4.

    Essa propriedade é `False` por padrão.

- **AndroidEnableSGenConcurrent** &ndash; Uma propriedade booliana que determina se o [coletor de GC simultâneo](https://www.mono-project.com/docs/about-mono/releases/4.8.0/#concurrent-sgen) do Mono será ou não usado.

    O suporte para essa propriedade foi adicionado no Xamarin.Android 7.2.

    Essa propriedade é `False` por padrão.

- **AndroidErrorOnCustomJavaObject** &ndash; Uma propriedade booliana que determina se os tipos podem implementar `Android.Runtime.IJavaObject` *sem* também herdar de `Java.Lang.Object` ou `Java.Lang.Throwable`:

    ```csharp
    class BadType : IJavaObject {
        public IntPtr Handle {
            get {return IntPtr.Zero;}
        }

        public void Dispose()
        {
        }
    }
    ```

    quando for True, esses tipos gerarão um erro XA4212, caso contrário, um aviso XA4212 será gerado.

    O suporte para essa propriedade foi adicionado no Xamarin.Android 8.1.

    Essa propriedade é `True` por padrão.

- **AndroidFastDeploymentType** &ndash; Uma lista de valores separados por `:` (dois pontos) para controlar quais tipos podem ser implantados no [diretório da Implantação Rápida](#Fast_Deployment) no dispositivo de destino quando a propriedade `$(EmbedAssembliesIntoApk)` do MSBuild é `False`. Se um recurso é implantado por Fast Deployment, ele *não* é inserido no `.apk` gerado, o que pode acelerar os tempos de implantação. (Quanto mais rápido ele é implantado, menor a frequência com que o `.apk` precisa ser recriado, acelerando assim o processo de instalação.) Os valores válidos incluem:

    - `Assemblies`: implantar assemblies do aplicativo.

    - `Dexes`: implantar arquivos `.dex`, recursos do Android e ativos do Android. **Esse valor pode ser usado *somente* em dispositivos que executam o Android 4.4 ou posterior (API-19).**

    O valor padrão é `Assemblies`.

    **Experimental**. Adicionado no Xamarin.Android 6.1.

- **AndroidGenerateJniMarshalMethods** &ndash; uma propriedade booliana que permite a geração dos métodos de marshal de JNI como parte do processo de build. Ela reduz consideravelmente o uso de System.Reflection no código do auxiliar de associação.

    Por padrão, ela será definida como False. Se os desenvolvedores desejarem usar o novo recurso de métodos de marshal JNI, eles poderão definir

    ```xml
    <AndroidGenerateJniMarshalMethods>True</AndroidGenerateJniMarshalMethods>
    ```

    no .csproj deles. Como alternativa, forneça a propriedade na linha de comando por meio de

    ```
    /p:AndroidGenerateJniMarshalMethods=True
    ```

    **Experimental**. Adicionado no Xamarin.Android 9.2.
    O valor padrão é False.

- **AndroidGenerateJniMarshalMethodsAdditionalArguments** &ndash; uma propriedade de cadeia de caracteres que pode ser usada para adicionar mais parâmetros à invocação de `jnimarshalmethod-gen.exe`.  Isso é útil para a depuração, para que opções como `-v`, `-d` ou `--keeptemp` possam ser usadas.

    O valor padrão é uma cadeia de caracteres vazia. Ele pode ser definido no arquivo .csproj ou na linha de comando. Por exemplo:

    ```xml
    <AndroidGenerateJniMarshalMethodsAdditionalArguments>-v -d --keeptemp</AndroidGenerateJniMarshalMethodsAdditionalArguments>
    ```

    ou:

    ```
    /p:AndroidGenerateJniMarshalMethodsAdditionalArguments="-v -d --keeptemp"
    ```

    Adicionado no Xamarin.Android 9.2.

- **AndroidHttpClientHandlerType** &ndash; Controla a implementação padrão `System.Net.Http.HttpMessageHandler` que será usada pelo construtor padrão `System.Net.Http.HttpClient`. O valor é um nome de tipo qualificado pelo assembly de uma subclasse `HttpMessageHandler`, adequado para usar com [`System.Type.GetType(string)`](https://docs.microsoft.com/dotnet/api/system.type.gettype?view=netcore-2.0#System_Type_GetType_System_String_).
    Os valores mais comuns para essa propriedade são:

    - `Xamarin.Android.Net.AndroidClientHandler`: Use as APIs Java do Android para executar solicitações de rede. Isso permite acessar as URLs TLS 1.2 quando a versão do Android subjacente dá suporte ao TLS 1.2. Somente versões Android 5.0 e posteriores fornecem suporte confiável ao TLS 1.2 por meio de Java.

        Isso corresponde à opção do **Android** nas páginas de propriedades do Visual Studio e à opção **AndroidClientHandler** nas páginas de propriedades de Visual Studio para Mac.

        O novo assistente de projeto seleciona essa opção para novos projetos quando a **Versão mínima do Android** está configurada como **Android 5.0 (Lollipop)** ou mais recente no Visual Studio ou quando **Plataformas de destino** está definido como **Melhor e Mais Recente** no Visual Studio para Mac.

    - Não definido/a cadeia de caracteres está vazia: Isso é equivalente a `System.Net.Http.HttpClientHandler, System.Net.Http`

        Isso corresponde à opção **Padrão** nas páginas de propriedades do Visual Studio.

        O novo assistente de projeto seleciona essa opção para novos projetos quando a **Versão mínima do Android** está configurada como **Android 4.4.87** ou mais antiga no Visual Studio ou quando **Plataformas de destino** está definido como **Desenvolvimento Moderno** ou **Máxima Compatibilidade** no Visual Studio para Mac.

    - `System.Net.Http.HttpClientHandler, System.Net.Http`: Use o `HttpMessageHandler` gerenciado.

       Isso corresponde à opção **Gerenciado** nas páginas de propriedades do Visual Studio.

    *Observação*: Se a compatibilidade com o TLS 1.2 for necessária nas versões do Android anteriores à 5.0 *ou* com o `System.Net.WebClient` e as APIs relacionadas, o `$(AndroidTlsProvider)` deverá ser usado.

    *Observação*: o suporte para esta propriedade funciona por meio da definição da [variável de ambiente `XA_HTTP_CLIENT_HANDLER_TYPE`](~/android/deploy-test/environment.md).
    Um valor `$XA_HTTP_CLIENT_HANDLER_TYPE` encontrado em um arquivo com uma ação de build de `@(AndroidEnvironment)` terá precedência.

    Adicionado no Xamarin.Android 6.1.

- **AndroidLinkMode** &ndash; especifica qual tipo de [vinculação](~/android/deploy-test/linker.md) deve ser realizada em assemblies contidos no pacote Android. Usado somente em projetos de aplicativo Android. O valor padrão é *SdkOnly*. Os valores válidos são:

    - **Nenhum**: não será tentada nenhuma vinculação.

    - **SdkOnly**: a vinculação será executada apenas nas bibliotecas de classe base, e não nos assemblies do usuário.

    - **Full**: a vinculação será executada nas bibliotecas de classe base e nos assemblies do usuário.

        > [!NOTE]
        > o uso de um valor `AndroidLinkMode` definido como *Full* normalmente resulta em aplicativos com falha, principalmente quando a reflexão é usada. Evite usar isso, a menos que você *realmente* saiba o que está fazendo.

    ```xml
    <AndroidLinkMode>SdkOnly</AndroidLinkMode>
    ```

- **AndroidLinkSkip** &ndash; especifica uma lista delimitada por ponto e vírgula (`;`) de nomes de assembly, sem extensões de arquivo, de assemblies que não devem ser vinculados. Usado somente em projetos de aplicativo Android.

    ```xml
    <AndroidLinkSkip>Assembly1;Assembly2</AndroidLinkSkip>
    ```

- **AndroidLinkTool** &ndash; uma propriedade de estilo de enumeração com os valores válidos `proguard` ou `r8`. Indica qual redutor de código é usado para o código Java. Atualmente, o padrão é uma cadeia de caracteres vazia ou `proguard` quando `$(AndroidEnableProguard)` é `True`. Para obter mais informações, confira nossa documentação sobre o [D8 e R8][d8-r8].

    [d8-r8]: https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/D8andR8.md

- **AndroidLintEnabled** &ndash; Uma propriedade booliana que permite ao desenvolvedor executar a ferramenta `lint` do Android como parte do processo de empacotamento.

    - **AndroidLintEnabledIssues** &ndash; Uma lista, separada por vírgulas, de problemas de lint a serem habilitados.

    - **AndroidLintDisabledIssues** &ndash; uma lista, separada por vírgulas, de problemas de lint a serem desabilitados.

    - **AndroidLintCheckIssues** &ndash; Uma lista, separada por vírgulas, de problemas de lint a serem verificados.
        Observação: somente esses problemas serão verificados.

    - **AndroidLintConfig** &ndash; essa é uma ação de build para um arquivo de configuração de estilo de lint. Ela pode ser usada para habilitar/desabilitar problemas a serem verificados. Vários arquivos podem usar essa ação de build pois seu conteúdo será mesclado.

    Confira a [Ajuda do Lint](https://developer.android.com/studio/write/lint) para obter mais detalhes sobre as ferramentas `lint` do Android.

- **AndroidManagedSymbols** &ndash; Uma propriedade booliana que controla se pontos de sequência são gerados para que o nome do arquivo e as informações de número de linha possam ser extraídos dos rastreamentos de pilha `Release`.

    Adicionado no Xamarin.Android 6.1.

- **AndroidManifest** &ndash; especifica um nome de arquivo a ser usado como modelo para o [`AndroidManifest.xml`](~/android/platform/android-manifest.md) do aplicativo.
    Durante o build, quaisquer outros valores necessários serão mesclados para produzir o `AndroidManifest.xml` propriamente dito.
    O `$(AndroidManifest)` deve conter o nome do pacote no atributo `/manifest/@package`.

- **AndroidMultiDexClassListExtraArgs** &ndash; uma propriedade de cadeia de caracteres que permite que os desenvolvedores passem argumentos adicionais para o `com.android.multidex.MainDexListBuilder` ao gerar o arquivo `multidex.keep`.

    Um caso específico é se você estiver recebendo o seguinte erro durante a compilação de `dx`.

    ```
    com.android.dex.DexException: Too many classes in --main-dex-list, main dex capacity exceeded
    ```

    Se você estiver recebendo esse erro, adicione o seguinte ao .csproj.

    ```xml
    <DxExtraArguments>--force-jumbo </DxExtraArguments>
    <AndroidMultiDexClassListExtraArgs>--disable-annotation-resolution-workaround</AndroidMultiDexClassListExtraArgs>
    ```

    Assim, a etapa `dx` poderá ter êxito.

    Adicionado no Xamarin.Android 8.3.

- **AndroidPackageFormat** &ndash; Uma propriedade em estilo de enumeração com os valores válidos `apk` ou `aab`. Isso indica se você deseja empacotar o aplicativo Android como um [arquivo APK][apk] ou um [Lote de Aplicativo do Android][bundle]. Os Lote de Aplicativo são um novo formato para builds de `Release` destinados ao envio no Google Play. Atualmente, este valor assume o padrão `apk`.

    Quando `$(AndroidPackageFormat)` é definido como `aab`, outras propriedades do MSBuild são definidas, que são necessárias para o Lote de Aplicativo do Android:

    - `$(AndroidUseAapt2)` é `True`.
    - `$(AndroidUseApkSigner)` é `False`.
    - `$(AndroidCreatePackagePerAbi)` é `False`.

[apk]: https://en.wikipedia.org/wiki/Android_application_package
[bundle]: https://developer.android.com/platform/technology/app-bundle

- **AndroidR8JarPath** &ndash; o caminho para `r8.jar` a ser usado com o compilador dex r8 e o redutor. O padrão é um caminho na instalação do Xamarin.Android. Para obter mais informações, confira nossa documentação sobre o [D8 e R8][d8-r8].

- **AndroidSdkBuildToolsVersion** &ndash; o pacote de ferramentas de build do Android SDK fornece as ferramentas **aapt** e **zipalign**, entre outras. Várias versões diferentes do pacote de ferramentas de build podem ser instaladas simultaneamente. O pacote de ferramentas de build escolhido para empacotamento é criado procurando e usando uma versão de ferramentas de build "preferencial", se uma está presente; se a versão "preferencial" *não* está presente, o pacote de ferramentas de build com a versão mais alta entre os instalados é usado.

    A propriedade do MSBuild `$(AndroidSdkBuildToolsVersion)` contém a versão das ferramentas de build preferencial. O sistema de build do Xamarin.Android fornecerá um valor padrão em `Xamarin.Android.Common.targets`, que poderá ser substituído no arquivo de projeto para escolher uma versão de ferramentas de build alternativa se, por exemplo, o aapt mais recente estiver falhando enquanto uma versão anterior do aapt sabidamente funciona.

- **AndroidSupportedAbis** &ndash; Uma propriedade de cadeia de caracteres que contém uma lista delimitada por ponto e vírgula (`;`) de ABIs que devem ser incluídos no `.apk`.

    Os valores compatíveis incluem:

    - `armeabi-v7a`
    - `x86`
    - `arm64-v8a`: requer o Xamarin.Android 5.1 e posterior.
    - `x86_64`: requer o Xamarin.Android 5.1 e posterior.

- **AndroidTlsProvider** &ndash; um valor de cadeia de caracteres que especifica qual provedor TLS deve ser usado em um aplicativo. Os possíveis valores são:

    - Não definido/a cadeia de caracteres está vazia: no Xamarin.Android 7.3 (ou versão mais recente), isso é equivalente a `btls`.

        no Xamarin.Android 7.1, isso é equivalente a `legacy`.

        Isso corresponde à configuração **Padrão** nas páginas de propriedades do Visual Studio.

    - `btls`: use [Boring SSL](https://boringssl.googlesource.com/boringssl) para a comunicação TLS com [HttpWebRequest](xref:System.Net.HttpWebRequest).

        Isso permite usar o TLS 1.2 em todas as versões do Android.

        Isso corresponde à configuração **TLS Nativo 1.2+** nas páginas de propriedades do Visual Studio.

    - `legacy`: use a implementação SSL gerenciada histórica para interação na rede. Isso *não* é compatível com a TLS 1.2.

        Isso corresponde à configuração **TLS Gerenciado 1.0** nas páginas de propriedades do Visual Studio.

    - `default`: é improvável que esse valor seja usado em projetos do Xamarin.Android. Em vez disso, o valor recomendado a ser usado é a cadeia de caracteres vazia, que corresponde à configuração **padrão** nas páginas de propriedades do Visual Studio.

        O valor `default` não é oferecido nas páginas de propriedades do Visual Studio.

        Atualmente, isso é equivalente a `legacy`.

    Adicionado no Xamarin.Android 7.1.

- **AndroidUseApkSigner** &ndash; Uma propriedade booliana que permite ao desenvolvedor usar a ferramenta `apksigner` em vez da `jarsigner`.

    Adicionado no Xamarin.Android 8.2.

- **AndroidUseLegacyVersionCode** &ndash; Uma propriedade booliana que permitirá ao desenvolvedor reverter o cálculo versionCode para o comportamento anterior do Xamarin.Android 8.2. Isso deve ser usado SOMENTE por desenvolvedores com aplicativos existentes na Google Play Store. É altamente recomendável que a nova propriedade `$(AndroidVersionCodePattern)` seja usada.

    Adicionado no Xamarin.Android 8.2.

- **AndroidUseManagedDesignTimeResourceGenerator** &ndash; Uma propriedade booliana que alterna os builds de tempo de design para usar o analisador de recurso gerenciado em vez de `aapt`.

    Adicionado no Xamarin.Android 8.1.

- **AndroidUseSharedRuntime** &ndash; uma propriedade booliana que determina se os *pacotes de tempo de execução compartilhado* são necessários para executar o aplicativo no dispositivo de destino. Depender dos pacotes de tempo de execução compartilhado permite que o pacote do aplicativo seja menor, acelerando o processo de criação e implantação de pacote e resultando em um ciclo de build/implantação/depuração mais rápido.

    Essa propriedade deve ser `True` para builds de depuração e `False` para projetos de versão.

- **AndroidVersionCodePattern** &ndash; uma propriedade de cadeia de caracteres que permite ao desenvolvedor personalizar o `versionCode` no manifesto.
    Confira [Criar um código de versão para o APK](~/android/deploy-test/building-apps/abi-specific-apks.md) para obter informações sobre como decidir um `versionCode`.

    Alguns exemplos: se `abi` for `armeabi` e `versionCode` no manifesto for `123`, `{abi}{versionCode}` produzirá um versionCode de `1123` quando `$(AndroidCreatePackagePerAbi)` for True; caso contrário, gerará um valor de 123.
    Se `abi` é `x86_64` e `versionCode` no manifesto é `44`. Isso produzirá `544` quando `$(AndroidCreatePackagePerAbi)` for True; caso contrário, gerará um valor de `44`.

    Se nós incluíssemos uma cadeia de caracteres de formato de preenchimento esquerdo `{abi}{versionCode:0000}`, isso produziria `50044` porque estamos aplicando preenchimento esquerdo ao `versionCode` com `0`. Como alternativa, você pode usar o preenchimento decimal, como `{abi}{versionCode:D4}`
    que tem o mesmo resultado do exemplo anterior.

    Somente os formatos de cadeias de caracteres de preenchimento '0' e 'Dx' são compatíveis, já que o valor PRECISA ser um inteiro.

    Itens chave pré-definidos

    - **abi** &ndash; Insere a abi direcionada para o aplicativo
        - 2 &ndash; `armeabi-v7a`
        - 3 &ndash; `x86`
        - 4 &ndash; `arm64-v8a`
        - 5 &ndash; `x86_64`

    - **minSDK** &ndash; Insere o valor mínimo compatível do SDK encontrado no `AndroidManifest.xml`, ou então `11` se não há nenhum definido.

    - **versionCode** &ndash; Usa o código da versão diretamente do `Properties\AndroidManifest.xml`.

    Você pode definir itens personalizados usando a propriedade `$(AndroidVersionCodeProperties)` (definida a seguir).

    Por padrão o valor será definido como `{abi}{versionCode:D6}`. Se um desenvolvedor quiser manter o comportamento anterior, você poderá configurar a propriedade `$(AndroidUseLegacyVersionCode)` como `true` para substituir o padrão

    Adicionado no Xamarin.Android 7.2.

- **AndroidVersionCodeProperties** &ndash; Uma propriedade de cadeia de caracteres que permite ao desenvolvedor definir itens personalizados para usar com o `AndroidVersionCodePattern`. Eles estão na forma de um par `key=value`. Todos os itens no `value` devem ser valores inteiros. Por exemplo: `screen=23;target=$(_AndroidApiLevel)`. Como você pode ver, você pode fazer uso de propriedades do MSBuild existentes ou personalizadas na cadeia de caracteres.

    Adicionado no Xamarin.Android 7.2.

- **AotAssemblies** &ndash; Uma propriedade booliana que determina se os assemblies serão ou não compilados Ahead of Time em código nativo e incluídos no `.apk`.

    O suporte para essa propriedade foi adicionado no Xamarin.Android 5.1.

    Essa propriedade é `False` por padrão.

- **EmbedAssembliesIntoApk** &ndash; Uma propriedade booliana que determina se os assemblies do aplicativo devem ser inseridos no pacote do aplicativo ou não.

    Essa propriedade deve ser `True` para builds de versão e `False` para builds de depuração. Ele *pode* precisar ser `True` em builds de depuração se o Fast Deployment não é compatível com o dispositivo de destino.

    Quando essa propriedade é `False`, a propriedade `$(AndroidFastDeploymentType)` do MSBuild também controla o que será inserido no `.apk`, o que pode afetar os tempos de implantação e recompilação.

- **EnableLLVM** &ndash; Uma propriedade booliana que determina se o LLVM será ou não usado ao compilar assemblies Ahead of Time em código nativo.

    O suporte para essa propriedade foi adicionado no Xamarin.Android 5.1.

    Essa propriedade é `False` por padrão.

    Essa propriedade é ignorada, a menos que a propriedade `$(AotAssemblies)` do MSBuild seja `True`.

- **EnableProguard** &ndash; Uma propriedade booliana que determina se [proguard](https://developer.android.com/tools/help/proguard.html) é ou não executado como parte do processo de empacotamento para vincular o código Java.

    O suporte para essa propriedade foi adicionado no Xamarin.Android 5.1.

    Essa propriedade é `False` por padrão.

    Quando for `True`, os arquivos de [ProguardConfiguration](#ProguardConfiguration) serão usados para controlar a execução de `proguard`.

- **JavaMaximumHeapSize** &ndash; Especifica o valor do parâmetro **java**`-Xmx` a ser usado ao criar o arquivo `.dex` como parte do processo de empacotamento. Se não for especificado, a opção `-Xmx` fornecerá **java** com um valor de `1G`. Foi verificado que isso normalmente é necessário no Windows em comparação com outras plataformas.

    Especificar essa propriedade será necessário se o destino [`_CompileDex` gerar um `java.lang.OutOfMemoryError`](https://bugzilla.xamarin.com/show_bug.cgi?id=18327).

    Personalize o valor alterando:

    ```xml
    <JavaMaximumHeapSize>1G</JavaMaximumHeapSize>
    ```

- **JavaOptions** &ndash; Especifica as opções de linha de comando adicionais a serem passadas para **java** ao compilar o arquivo `.dex`.

- **LinkerDumpDependencies** &ndash; uma propriedade booliana que permite a geração do arquivo de dependências do vinculador. Esse arquivo pode ser usado como entrada para a ferramenta [illinkanalyzer](https://github.com/mono/linker/blob/master/src/analyzer/README.md).

    O valor padrão é False.

- **MandroidI18n**&ndash; especifica o suporte à internacionalização incluído com o aplicativo, tal como tabelas de ordenação e de classificação. O valor é uma lista separada por vírgula ou ponto e vírgula de um ou mais dos seguintes valores que não diferenciam maiúsculas e minúsculas:

    - **Nenhum**: não incluir nenhuma codificação adicional.

    - **All**: incluir todas as codificações disponíveis.

    - **CJK**: incluir codificações de chinês, japonês e coreano, como *Japonês (EUC)* \[enc-jp, CP51932\], *japonês (Shift-JIS)* \[iso-2022-jp, shift\_jis, CP932\], *japonês (JIS)* \[CP50220\], *chinês simplificado (GB2312)* \[gb2312, CP936\], *coreano (UHC)* \[ks\_c\_5601-1987, CP949\], *coreano (EUC)* \[euc-kr, CP51949\], *chinês tradicional (Big5)* \[big5, CP950\] e *chinês simplificado (GB18030)* \[GB18030, CP54936\].

    - **MidEast**: incluir codificações do Oriente Médio, como *turco (Windows)* \[iso-8859-9, CP1254\], *hebraico (Windows)* \[windows-1255, CP1255\], *árabe (Windows)* \[windows-1256, CP1256\], *árabe (ISO)* \[iso-8859-6, CP28596\], *hebraico (ISO)* \[iso-8859-8, CP28598\], *latim 5 (ISO)* \[iso-8859-9, CP28599\] e *hebraico (ISO alternativa)* \[iso-8859-8, CP38598\].

    - **Other**: incluir outras codificações como *cirílico (Windows)* \[CP1251\], *báltico (Windows)* \[iso-8859-4, CP1257\], *vietnamita (Windows)* \[CP1258\], *cirílico (KOI8-R)* \[koi8-r, CP1251\], *ucraniano (KOI8-U)* \[koi8-u, CP1251\], *báltico (ISO)* \[iso-8859-4, CP1257\], *cirílico (ISO)* \[iso-8859-5, CP1251\], *ISCII devanágari* \[x-iscii-de, CP57002\], *ISCII bengalês* \[x-iscii-be, CP57003\], *ISCII tâmil* \[x-iscii-ta, CP57004\], *ISCII télugo* \[x-iscii-te, CP57005\], *ISCII assamês* \[x-iscii-as, CP57006\], *ISCII odia* \[x-iscii-or, CP57007\], *ISCII canarim* \[x-iscii-ka, CP57008\], *ISCII malaiala* \[x-iscii-ma, CP57009\], *ISCII guzerate* \[x-iscii-gu, CP57010\], *ISCII panjabi* \[x-iscii-pa, CP57011\] e *tailandês (Windows)* \[CP874\].

    - **Rare**: incluir codificações raras, tais como *IBM EBCDIC (turco)* \[CP1026\], *IBM EBCDIC (Open Systems Latim 1)* \[CP1047\], *IBM EBCDIC (EUA-Canadá com Euro)* \[CP1140\], *IBM EBCDIC (Alemanha com Euro)* \[CP1141\], *IBM EBCDIC (Dinamarca/Noruega com Euro)* \[CP1142\], *IBM EBCDIC (Finlândia/Suécia com Euro)* \[CP1143\], *IBM EBCDIC (Itália com Euro)* \[CP1144\], *IBM EBCDIC (América Latina/Espanha com Euro)* \[CP1145\], *IBM EBCDIC (Reino Unido com Euro)* \[CP1146\], *IBM EBCDIC (França com Euro)* \[CP1147\], *IBM EBCDIC (Internacional com Euro)* \[CP1148\], *IBM EBCDIC (islandês com Euro)* \[CP1149\], *IBM EBCDIC (Alemanha)* \[CP20273\], *IBM EBCDIC (Dinamarca/Noruega)* \[CP20277\], *IBM EBCDIC (Finlândia/Suécia)* \[CP20278\], *IBM EBCDIC (Itália)* \[CP20280\], *IBM EBCDIC (América Latina/Espanha)* \[CP20284\], *IBM EBCDIC (Reino Unido)* \[CP20285\], *IBM EBCDIC (katakana japonês estendido)* \[CP20290\], *IBM EBCDIC (França)* \[CP20297\], *IBM EBCDIC (árabe)* \[CP20420\], *IBM EBCDIC (hebraico)* \[CP20424\], *IBM EBCDIC (islandês)* \[CP20871\], *IBM EBCDIC (cirílico – servo, búlgaro)* \[CP21025\], *IBM EBCDIC (EUA-Canadá)* \[CP37\], *IBM EBCDIC (Internacional)* \[CP500\], *árabe (ASMO 708)* \[CP708\], *Centro-europeu (DOS)* \[CP852\] *, cirílico (DOS)* \[CP855\], *turco (DOS)* \[CP857\], *europeu ocidental (DOS com Euro)* \[CP858\], *hebraico (DOS)* \[CP862\], *árabe (DOS)* \[CP864\], *russo (DOS)* \[CP866\], *grego (DOS)* \[CP869\], *IBM EBCDIC (latim 2)* \[CP870\] e *IBM EBCDIC (grego)* \[CP875\].

    - **West**: incluir codificações ocidentais, como *europeu ocidental (Mac)* \[macintosh, CP10000\], *islandês (Mac)* \[x-mac-islandês, CP10079\], *Centro-europeu (Windows)* \[iso-8859-2, CP1250\], *europeu ocidental (Windows)* \[iso-8859-1, CP1252\], *grego (Windows)* \[iso-8859-7, CP1253\], *Centro-europeu (ISO)* \[iso 8859-2, CP28592\], *latim 3 (ISO)* \[iso-8859-3, CP28593\], *grego (ISO)* \[iso-8859-7, CP28597\], *latim 9 (ISO)* \[iso-8859-15, CP28605\], *OEM Estados Unidos* \[CP437\], *Europeu Ocidental (DOS)* \[CP850\], *português (DOS)* \[CP860\], *islandês (DOS)* \[CP861\], *francês canadense (DOS)* \[CP863\] e *nórdico (DOS)* \[CP865\].


    ```xml
    <MandroidI18n>West</MandroidI18n>
    ```

- **MonoSymbolArchive** &ndash; Uma propriedade booliana que controla se artefatos `.mSYM` são criados para uso posterior com `mono-symbolicate`, para extrair informações &ldquo;reais&rdquo; de nome do arquivo e de número de linha dos rastreamentos de pilha de versão.

    Isso é True por padrão para aplicativos de &ldquo;Versão&rdquo; que têm os símbolos de depuração habilitados: `$(EmbedAssembliesIntoApk)` é True, `$(DebugSymbols)` é True e `$(Optimize)` é True.

    Adicionado no Xamarin.Android 7.1.

### <a name="binding-project-build-properties"></a>Propriedades de build do projeto de associação

As seguintes propriedades de MSBuild são usadas com [projetos de associação](~/android/platform/binding-java-library/index.md):

- **AndroidClassParser** &ndash; uma propriedade de cadeia de caracteres que controla como os arquivos `.jar` são analisados. Os possíveis valores incluem:

    - **class-parse**: usa `class-parse.exe` para analisar o código de bytes Java diretamente, sem a assistência de uma JVM. Esse valor é experimental.


    - **jar2xml**: usa `jar2xml.jar` para usar a reflexão do Java para extrair tipos e membros de um arquivo `.jar`.

    As vantagens de `class-parse` sobre `jar2xml` são:

    - `class-parse` é capaz de extrair nomes de parâmetro do código de bytes Java que contém símbolos de *depuração* (por exemplo, código de bytes compilado com `javac -g`).

    - `class-parse` não "ignora" classes que herdam ou contêm membros de tipos não resolvíveis.

    **Experimental**. Adicionado no Xamarin.Android 6.0.

    O valor padrão é `jar2xml`.

    O valor padrão será alterado em uma versão futura.

- **AndroidCodegenTarget** &ndash; uma propriedade de cadeia de caracteres que controla a ABI de destino de geração de código. Os possíveis valores incluem:

    - **XamarinAndroid**: usa a API de associação de JNI presente desde o Mono para Android 1.0. Assemblies de associação criados com Xamarin.Android 5.0 ou posterior podem ser executados apenas no Xamarin.Android 5.0 ou posterior (adições de API/ABI), mas o *código-fonte* é compatível com o das versões anteriores do produto.

    - **XAJavaInterop1**: usar o Java.Interop para invocações de JNI. Assemblies de associação usando `XAJavaInterop1` só podem compilar e executar com o Xamarin.Android 6.1 ou posterior. O Xamarin.Android 6.1 e os posteriores associam `Mono.Android.dll` com esse valor.

        Os benefícios de `XAJavaInterop1` incluem:

        - Assemblies menores.

        - Cache em `jmethodID` para invocações de método `base`, desde que todos os outros tipos de associação na hierarquia de herança sejam compilados com o `XAJavaInterop1` ou posterior.

        - Cache em `jmethodID` de construtores do Java Callable Wrapper para subclasses gerenciadas.

        O valor padrão é `XAJavaInterop1`.


### <a name="resource-properties"></a>Propriedades de recurso

Propriedades do recurso controlam a geração do arquivo `Resource.designer.cs`, que fornece acesso a recursos do Android.

- **AndroidAapt2CompileExtraArgs** &ndash; Especifica opções de linha de comando adicionais a serem passadas para o comando **aapt2 compile** durante o processamento de recursos e ativos do Android.

    Adicionado no Xamarin.Android 9.1.

- **AndroidAapt2LinkExtraArgs** &ndash; Especifica opções de linha de comando adicionais a serem passadas ao comando **aapt2 link** durante o processamento de ativos e recursos do Android.

    Adicionado no Xamarin.Android 9.1.

- **AndroidExplicitCrunch** &ndash; se você estiver criando um aplicativo com um número muito grande de desenháveis locais, um build (ou rebuild) inicial pode levar minutos para ser concluído. Para acelerar o processo de build, tente incluir essa propriedade e configurá-la como `True`. Quando essa propriedade é definida, o processo de build pré-compacta os arquivos PNG.

    Observação: essa opção não é compatível com a opção `$(AndroidUseAapt2)`. Se a `$(AndroidUseAapt2)` estiver habilitada, essa funcionalidade estará desabilitada. Se você quiser continuar usando esse recurso, defina `$(AndroidUseAapt2)` como `False`.

    **Experimental**. Adicionado no Xamarin.Android 7.0.

- **AndroidResgenExtraArgs** &ndash; Especifica as opções de linha de comando adicionais a serem passadas para o comando **aapt** ao processar ativos e recursos do Android.

- **AndroidResgenFile** &ndash; especifica o nome do arquivo de recurso para gerar. O modelo padrão define isso como `Resource.designer.cs`.

- **AndroidUseAapt2** &ndash; uma propriedade booliana que permite que o desenvolvedor controle o uso da ferramenta `aapt2` para empacotamento.
    Por padrão, ela será definida como false e usaremos `aapt`.
    Se o desenvolvedor quiser usar a nova funcionalidade `aapt2`, ele poderá definir

    ```xml
    <AndroidUseAapt2>True</AndroidUseAapt2>
    ```

    no .csproj deles. Como alternativa, forneça a propriedade na linha de comando por meio de

    ```
    /p:AndroidUseAapt2=True
    ```

    Adicionado no Xamarin.Android 8.3.

- **MonoAndroidResourcePrefix** &ndash; especifica um *prefixo de caminho* que é removido do início dos nomes de arquivo com uma ação de build de `AndroidResource`. Isso é para permitir a alteração do local em que os recursos estão localizados.

    O valor padrão é `Resources`. Altere isso para `res` para a estrutura do projeto Java.

<a name="Signing_Properties" />

### <a name="signing-properties"></a>Propriedades de assinatura

As propriedades de assinatura controlam como o pacote do aplicativo é assinado, para que ele possa ser instalado em um dispositivo Android. Para permitir uma iteração de build mais rápida, as tarefas do Xamarin.Android não assinam pacotes durante o processo de build, porque assinar é algo muito lento. Em vez disso, eles são assinados (se necessário) antes da instalação ou durante a exportação, pelo IDE ou pelo destino de build *Install*. Invocar o destino *SignAndroidPackage* produzirá um pacote com o sufixo `-Signed.apk` no diretório de saída.

Por padrão, o destino da assinatura gera uma nova chave de assinatura de depuração, se necessário. Se você quiser usar uma chave específica, por exemplo, em um servidor de build, as seguintes propriedades de MSBuild podem ser usadas:

- **AndroidDebugKeyAlgorithm** &ndash; Especifica o algoritmo padrão para ser usado para o `debug.keystore`. Usa como padrão `RSA`.

- **AndroidDebugKeyAlgorithm** &ndash; Especifica a validade padrão para ser usada para o `debug.keystore`. Usa como padrão `10950`, `30 * 365` ou `30 years`.

- **AndroidKeyStore** &ndash; um valor booliano que indica se as informações de autenticação personalizadas devem ser usadas. O valor padrão é `False`, que significa que a chave de assinatura de depuração padrão será usada para assinar pacotes.

- **AndroidSigningKeyAlias** &ndash; especifica o alias para a chave no repositório de chaves. Este é o valor de **keytool-alias** usado ao criar o repositório de chaves.

- **AndroidSigningKeyPass** &ndash; especifica a senha da chave de dentro do arquivo do repositório de chaves. Esse é o valor digitado quando `keytool` solicita **Insira a senha da chave para $(AndroidSigningKeyAlias)** .

- **AndroidSigningKeyStore** &ndash; especifica o nome do arquivo do repositório de chaves criado por `keytool`. Isso corresponde ao valor fornecido à opção **keytool -keystore**.

- **AndroidSigningStorePass** &ndash; especifica a senha para `$(AndroidSigningKeyStore)`. Esse é o valor fornecido a `keytool` ao criar o arquivo de repositório de chaves e ao solicitar **Insira a senha do repositório de chaves:** .

Por exemplo, considere a seguinte invocação de `keytool`:

```shell
$ keytool -genkey -v -keystore filename.keystore -alias keystore.alias -keyalg RSA -keysize 2048 -validity 10000
Enter keystore password: keystore.filename password
Re-enter new password: keystore.filename password
...
Is CN=... correct?
  [no]:  yes

Generating 2,048 bit RSA key pair and self-signed certificate (SHA1withRSA) with a validity of 10,000 days
        for: ...
Enter key password for keystore.alias
        (RETURN if same as keystore password): keystore.alias password
[Storing filename.keystore]
```

Para usar o armazenamento de chaves gerado acima, use o grupo de propriedades:

```xml
<PropertyGroup>
    <AndroidKeyStore>True</AndroidKeyStore>
    <AndroidSigningKeyStore>filename.keystore</AndroidSigningKeyStore>
    <AndroidSigningStorePass>keystore.filename password</AndroidSigningStorePass>
    <AndroidSigningKeyAlias>keystore.alias</AndroidSigningKeyAlias>
    <AndroidSigningKeyPass>keystore.alias password</AndroidSigningKeyPass>
</PropertyGroup>
```

<a name="Build_Actions" />

## <a name="build-actions"></a>Ações de Build

*Ações de build* são [aplicadas a arquivos](https://docs.microsoft.com/visualstudio/msbuild/common-msbuild-project-items) dentro do projeto e controlam como o arquivo é processado.


### <a name="androidaarlibrary"></a>AndroidAarLibrary

A ação do build de `AndroidAarLibrary` deve ser usada para referenciar diretamente a arquivos .aar. Essa ação do build será mais usada pelos componentes do Xamarin. Especificamente para incluir referências aos arquivos .aar que são necessários para que a Google Play e outros serviços funcionem.

Os arquivos com essa ação do build serão tratados de forma semelhante aos recursos inseridos encontrados em projetos de biblioteca. Os arquivos .aar serão extraídos para o diretório intermediário. Em seguida, todos os ativos, recursos e arquivos .jar serão incluídos nos grupos de itens adequados.


### <a name="androidboundlayout"></a>AndroidBoundLayout

Indica que o code-behind deverá ser gerado para o arquivo de layout quando a propriedade `AndroidGenerateLayoutBindings` estiver definida como `false`. Em todos os outros aspectos, ela é idêntica à `AndroidResource` descrita acima. Essa ação pode ser usada **apenas** com arquivos de layout:

```xml
<AndroidBoundLayout Include="Resources\layout\Main.axml" />
```


<a name="AndroidEnvironment" />

### <a name="androidenvironment"></a>AndroidEnvironment

Arquivos com uma ação de build `AndroidEnvironment` são usados para [inicializar variáveis de ambiente e propriedades do sistema durante a inicialização do processo](~/android/deploy-test/environment.md).
A ação de build `AndroidEnvironment` pode ser aplicada a vários arquivos, e eles serão avaliados sem nenhuma ordem específica (então não especifique a mesma propriedade de sistema ou variável de ambiente em vários arquivos).


### <a name="androidfragmenttype"></a>AndroidFragmentType

Especifica o tipo totalmente qualificado de padrão a ser usado para todos os elementos de layout `<fragment>` ao gerar o código de associações de layout. O padrão da propriedade é o tipo `Android.App.Fragment` do Android.


### <a name="androidjavalibrary"></a>AndroidJavaLibrary

Arquivos com uma ação de build `AndroidJavaLibrary` são arquivos Java (arquivos `.jar`) que serão incluídos no pacote do Android final.


### <a name="androidjavasource"></a>AndroidJavaSource

Arquivos com uma ação de build `AndroidJavaSource` são código-fonte do Java que será incluído no pacote do Android final.


### <a name="androidlintconfig"></a>AndroidLintConfig

A ação de build 'AndroidLintConfig' deve ser usada em conjunto com a propriedade de build `AndroidLintEnabled`. Os arquivos com essa ação de build serão mesclados e passados para as ferramentas `lint` do Android. Eles deverão ser arquivos XML que contenham informações sobre quais testes devem ser habilitados e desabilitados.

Confira a [documentação do lint](https://developer.android.com/studio/write/lint) para obter mais detalhes.


### <a name="androidnativelibrary"></a>AndroidNativeLibrary

[Bibliotecas nativas](~/android/platform/native-libraries.md) são adicionadas ao build definindo-se as ações de build delas para `AndroidNativeLibrary`.

Observe que, como o Android é compatível com várias ABIs (interfaces binárias de aplicativo), o sistema de build deve saber para qual ABI a biblioteca nativa é compilada. Há duas maneiras de fazer isso:

1. "Detecção" de caminho.
2. Usando o atributo do item `Abi`.

Com a detecção de caminho, o nome do diretório pai da biblioteca nativa é usado para especificar a ABI usada como destino pela biblioteca. Portanto, se você adicionar `lib/armeabi-v7a/libfoo.so` ao build, em a ABI será "detectada" como `armeabi-v7a`.


#### <a name="item-attribute-name"></a>Nome de atributo de item

**Abi** &ndash; especifica a ABI da biblioteca nativa.

```xml
<ItemGroup>
  <AndroidNativeLibrary Include="path/to/libfoo.so">
    <Abi>armeabi-v7a</Abi>
  </AndroidNativeLibrary>
</ItemGroup>
```


### <a name="androidresource"></a>AndroidResource

Todos os arquivos com uma ação de build *AndroidResource* são compilados em recursos do Android durante o processo de build e tornados acessíveis por meio de `$(AndroidResgenFile)`.

```xml
<ItemGroup>
  <AndroidResource Include="Resources\values\strings.xml" />
</ItemGroup>
```

Usuários mais avançados talvez queiram usar diferentes recursos em configurações diferentes, mas com o mesmo caminho efetivo. Isso pode ser alcançado tendo vários diretórios do recurso e tendo arquivos com os mesmos caminhos relativos dentro desses diferentes diretórios, além de usar condições do MSBuild para incluir condicionalmente arquivos diferentes em configurações diferentes. Por exemplo:

```xml
<ItemGroup Condition="'$(Configuration)'!='Debug'">
  <AndroidResource Include="Resources\values\strings.xml" />
</ItemGroup>
<ItemGroup  Condition="'$(Configuration)'=='Debug'">
  <AndroidResource Include="Resources-Debug\values\strings.xml"/>
</ItemGroup>
<PropertyGroup>
  <MonoAndroidResourcePrefix>Resources;Resources-Debug<MonoAndroidResourcePrefix>
</PropertyGroup>
```

**LogicalName** &ndash; especifica o caminho do recurso explicitamente. Permite o &ldquo;uso de nomes alternativos&rdquo; para arquivos para que eles estejam disponíveis como vários nomes de recurso distintos.

```xml
<ItemGroup Condition="'$(Configuration)'!='Debug'">
  <AndroidResource Include="Resources/values/strings.xml"/>
</ItemGroup>
<ItemGroup Condition="'$(Configuration)'=='Debug'">
  <AndroidResource Include="Resources-Debug/values/strings.xml">
    <LogicalName>values/strings.xml</LogicalName>
  </AndroidResource>
</ItemGroup>
```


### <a name="content"></a>Conteúdo

A ação de build `Content` normal não é compatível (pois ainda não descobrimos como dar suporte a ela sem uma etapa de primeira execução de custo possivelmente alto).

Começando com o Xamarin.Android 5.1, tentar usar a ação de build `@(Content)` resultará em um aviso `XA0101`.


### <a name="linkdescription"></a>LinkDescription

Arquivos com uma ação de build *LinkDescription* são usados para [controlar o comportamento do vinculador](~/cross-platform/deploy-test/linker.md).


<a name="ProguardConfiguration" />

### <a name="proguardconfiguration"></a>ProguardConfiguration

Arquivos com uma ação de build *ProguardConfiguration* contêm opções que são usadas para controlar o comportamento de `proguard`. Para obter mais informações sobre essa ação de build, consulte [ProGuard](~/android/deploy-test/release-prep/proguard.md).

Esses arquivos são ignorados, a menos que a propriedade `$(EnableProguard)` do MSBuild seja `True`.


## <a name="target-definitions"></a>Definições de destino

As partes do processo de build específicas do Xamarin.Android são definidas em `$(MSBuildExtensionsPath)\Xamarin\Android\Xamarin.Android.CSharp.targets`, mas destinos normais específicos a uma linguagem, tais como *Microsoft.CSharp.targets*, também são necessários para compilar o assembly.

As seguintes propriedades de build devem ser definidas antes de importar quaisquer destinos de linguagem de programação:

```xml
<PropertyGroup>
  <TargetFrameworkIdentifier>MonoDroid</TargetFrameworkIdentifier>
  <MonoDroidVersion>v1.0</MonoDroidVersion>
  <TargetFrameworkVersion>v2.2</TargetFrameworkVersion>
</PropertyGroup>
```

Todos esses destinos e propriedades podem ser incluídos para C# importando *Xamarin.Android.CSharp.targets*:

```xml
<Import Project="$(MSBuildExtensionsPath)\Xamarin\Android\Xamarin.Android.CSharp.targets" />
```

Esse arquivo pode ser facilmente adaptado para outras linguagens de programação.
