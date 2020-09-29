---
title: Propriedades de build
description: Este documento listará todas as propriedades com suporte no processo de compilação do Xamarin. Android.
ms.prod: xamarin
ms.assetid: FC0DBC08-EBCB-4D2D-AB3F-76B54E635C22
ms.technology: xamarin-android
author: jonpryor
ms.author: jopryo
ms.date: 09/21/2020
ms.openlocfilehash: aeb0cca9ead1a0f0a3f5b1dec88b2470289cd589
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454930"
---
# <a name="build-properties"></a>Propriedades de build

As propriedades do MSBuild controlam o comportamento dos [destinos](~/android/deploy-test/building-apps/build-targets.md).
Eles são especificados dentro do arquivo de projeto, por exemplo **MyApp. csproj**, em um [PropertyGroup do MSBuild](/visualstudio/msbuild/propertygroup-element-msbuild).

## <a name="adbtarget"></a>AdbTarget

A `$(AdbTarget)` propriedade especifica o dispositivo de destino do Android no qual o pacote do Android pode ser instalado ou removido.
O valor dessa propriedade é o mesmo que a [ `adb` opção de dispositivo de destino](https://developer.android.com/tools/help/adb.html#issuingcommands).

## <a name="aftergenerateandroidmanifest"></a>AfterGenerateAndroidManifest

Os destinos do MSBuild listados nesta propriedade serão executados diretamente após o `_GenerateJavaStubs` destino interno, que é onde o `AndroidManifest.xml` arquivo é gerado no `$(IntermediateOutputPath)` . Se você quiser fazer modificações no `AndroidManifest.xml` arquivo gerado, poderá fazer isso usando esse ponto de extensão.

Adicionado no Xamarin.Android 9.4.

## <a name="androidaapt2compileextraargs"></a>AndroidAapt2CompileExtraArgs

Especifica opções de linha de comando adicionais para passar para o comando de **compilação aapt2** ao processar ativos e recursos do Android.

Adicionado no Xamarin.Android 9.1.

## <a name="androidaapt2linkextraargs"></a>AndroidAapt2LinkExtraArgs

Especifica opções de linha de comando adicionais para passar para o comando de **link aapt2** ao processar ativos e recursos do Android.

Adicionado no Xamarin.Android 9.1.

## <a name="androidaotcustomprofilepath"></a>AndroidAotCustomProfilePath

O arquivo que `aprofutil` deve ser criado para manter os dados do criador de perfil.

## <a name="androidaotprofiles"></a>AndroidAotProfiles

Uma propriedade de cadeia de caracteres que permite ao desenvolvedor adicionar perfis de AOT a partir da linha de comando. É um ponto-e-vírgula ou uma lista de caminhos absolutos separados por vírgulas.
Adicionado no Xamarin. Android 10,1.

## <a name="androidaotprofilerport"></a>AndroidAotProfilerPort

A porta que `aprofutil` deve se conectar ao obter dados de criação de perfil.

## <a name="androidapkdigestalgorithm"></a>AndroidApkDigestAlgorithm

Um valor de cadeia de caracteres que especifica o algoritmo de resumo a ser usado com `jarsigner -digestalg` .

O valor padrão é `SHA-256`. No Xamarin. Android 10,0 e anterior, o valor padrão era `SHA1` .

Adicionado no Xamarin.Android 9.4.

## <a name="androidapksigneradditionalarguments"></a>AndroidApkSignerAdditionalArguments

Uma propriedade de cadeia de caracteres que permite ao desenvolvedor fornecer argumentos adicionais para a `apksigner` ferramenta.

Adicionado no Xamarin.Android 8.2.

## <a name="androidapksigningalgorithm"></a>AndroidApkSigningAlgorithm

Um valor de cadeia de caracteres que especifica o algoritmo de assinatura a ser usado com `jarsigner -sigalg` .

O valor padrão é `SHA256withRSA`. No Xamarin. Android 10,0 e anterior, o valor padrão era `md5withRSA` .

Adicionado no Xamarin.Android 8.2.

## <a name="androidapplication"></a>AndroidApplication

Um valor booliano que indica se o projeto é para um aplicativo Android ( `True` ) ou para um projeto de biblioteca Android ( `False` ou não está presente).

Apenas um projeto com `<AndroidApplication>True</AndroidApplication>` pode estar presente em um pacote Android. (Infelizmente isso é ainda não verificado, que pode resultar em erros sutis e bizarros relacionados aos recursos do Android.)

## <a name="androidapplicationjavaclass"></a>AndroidApplicationJavaClass

O nome completo da classe Java a ser usado no lugar de `android.app.Application` quando uma classe herda de [Android. app. Application](xref:Android.App.Application).

Essa propriedade geralmente é definida por *outras* Propriedades, como a `$(AndroidEnableMultiDex)` Propriedade MSBuild.

Adicionado no Xamarin.Android 6.1.

## <a name="androidbinutilspath"></a>AndroidBinUtilsPath

Um caminho para um diretório que contém o [binutils][binutils] do Android, como `ld` o vinculador nativo, e `as` o Assembler nativo. Essas ferramentas fazem parte do Android NDK e também estão incluídas na instalação do Xamarin. Android.

O valor padrão é `$(MonoAndroidBinDirectory)\ndk\`.

Adicionado no Xamarin. Android 10,0.

[binutils]: https://android.googlesource.com/toolchain/binutils/

## <a name="androidboundexceptiontype"></a>AndroidBoundExceptionType

Um valor de cadeia de caracteres que especifica como as exceções devem ser propagadas quando um tipo de Xamarin. Android implementa um tipo ou interface .NET em termos de tipos de Java, por exemplo `Android.Runtime.InputStreamInvoker` `System.IO.Stream` , e, ou `Android.Runtime.JavaDictionary` e `System.Collections.IDictionary` .

- `Java`: O tipo de exceção Java original é propagado como está.

  Isso significa que, por exemplo, `InputStreamInvoker` o não implementa corretamente a `System.IO.Stream` API, pois `Java.IO.IOException` pode ser lançada de `Stream.Read()` em vez de `System.IO.IOException` .

  Esse é o comportamento de propagação de exceção em todas as versões do Xamarin. Android antes de 11,1.

  Esse é o valor padrão no Xamarin. Android 11,1.

- `System`: O tipo de exceção Java original é capturado e encapsulado em um tipo de exceção .NET apropriado.

  Isso significa que, por exemplo, `InputStreamInvoker` implementa corretamente `System.IO.Stream` e `Stream.Read()` *não* vai gerar `Java.IO.IOException` instâncias.  (Em vez disso, ele pode lançar um `System.IO.IOException` que tem um `Java.IO.IOException` como `Exception.InnerException` valor.)

  Isso se tornará o valor padrão no .NET 6,0.

Adicionado no Xamarin. Android 10,2.

## <a name="androidbuildapplicationpackage"></a>AndroidBuildApplicationPackage

Um valor booliano que indica se é para criar e assinar o pacote (. apk). Definir esse valor como `True` é equivalente a usar o parâmetro [`SignAndroidPackage`](~/android/deploy-test/building-apps/build-targets.md#install)
destino da compilação.

O suporte para essa propriedade foi adicionado após o Xamarin.Android 7.1.

Essa propriedade é `False` por padrão.

## <a name="androidbundleconfigurationfile"></a>AndroidBundleConfigurationFile

Especifica um FileName para usar como um [arquivo de configuração][bundle-config-format] para a `bundletool` criação de um pacote de aplicativo do Android. Esse arquivo controla alguns aspectos de como os APKs são gerados a partir do pacote, como em quais dimensões o pacote é dividido para produzir APKs. Observe que o Xamarin. Android configura algumas dessas configurações automaticamente, incluindo a lista de extensões de arquivo a deixarem descompactadas.

Essa propriedade só será relevante se [`$(AndroidPackageFormat)`](#androidpackageformat) for definido como `aab` .

Adicionado no Xamarin. Android 10,3.

[bundle-config-format]: https://developer.android.com/studio/build/building-cmdline#bundleconfig

## <a name="androidclassparser"></a>AndroidClassParser

Uma propriedade de cadeia de caracteres que controla como `.jar` os arquivos são analisados. Os valores possíveis incluem:

- **class-parse**: usa `class-parse.exe` para analisar o código de bytes Java diretamente, sem a assistência de uma JVM. Esse valor é experimental.

- **jar2xml**: usar `jar2xml.jar` para usar a reflexão do Java para extrair tipos e membros de um arquivo `.jar`.

As vantagens de `class-parse` sobre `jar2xml` são:

- `class-parse` pode extrair nomes de parâmetro de código de bytes Java que contém símbolos de *depuração* (código de bytes compilado com `javac -g` ).

- `class-parse` não "ignora" classes que herdam, ou contêm, membros de tipos não resolvidos.

**Experimental**. Adicionado no Xamarin.Android 6.0.

O valor padrão é `jar2xml`.

O suporte para `jar2xml` é obsoleto e o suporte para `jar2xml` será removido como parte do .net 6.

## <a name="androidcodegentarget"></a>AndroidCodegenTarget

Uma propriedade de cadeia de caracteres que controla o ABI de destino de geração de código.
Os valores possíveis incluem:

- **XamarinAndroid**: usa a API de associação JNI presente desde o mono para Android 1,0. Assemblies de associação criados com Xamarin.Android 5.0 ou posterior podem ser executados apenas no Xamarin.Android 5.0 ou posterior (adições de API/ABI), mas o *código-fonte* é compatível com o das versões anteriores do produto.

- **XAJavaInterop1**: usar Java.Interop para invocações de JNI. Assemblies de associação usando `XAJavaInterop1` só podem compilar e executar com o Xamarin.Android 6.1 ou posterior. O Xamarin.Android 6.1 e os posteriores associam `Mono.Android.dll` com esse valor.

Os benefícios de `XAJavaInterop1` incluem:

- Assemblies menores.

- Cache em `jmethodID` para invocações de método `base`, desde que todos os outros tipos de associação na hierarquia de herança sejam compilados com o `XAJavaInterop1` ou posterior.

- Cache em `jmethodID` de construtores do Java Callable Wrapper para subclasses gerenciadas.

O valor padrão é `XAJavaInterop1`.

## <a name="androidcreatepackageperabi"></a>AndroidCreatePackagePerAbi

Uma propriedade booliana que determina se um *conjunto* de arquivos--em por Abi especificado em [`$(AndroidSupportedAbis)`](#androidsupportedabis) --deve ser criado em vez de ter suporte para todos os Abis em um único `.apk` .

Consulte também o guia de [criação de APKs específico da Abi](~/android/deploy-test/building-apps/abi-specific-apks.md) .

## <a name="androiddebugkeyalgorithm"></a>AndroidDebugKeyAlgorithm

Especifica o algoritmo padrão a ser usado para o `debug.keystore` . O padrão é `RSA`.

## <a name="androiddebugkeyvalidity"></a>AndroidDebugKeyValidity

Especifica a validade padrão a ser usada para o `debug.keystore` . Usa como padrão `10950`, `30 * 365` ou `30 years`.

## <a name="androiddebugstoretype"></a>AndroidDebugStoreType

Especifica o formato de arquivo de repositório de chaves a ser usado para o `debug.keystore` . O padrão é `pkcs12`.

Adicionado no Xamarin. Android 10,2.

## <a name="androiddextool"></a>AndroidDexTool

Uma propriedade do estilo de enumeração com valores válidos de `dx` ou `d8` . Indica qual compilador [dex][dex] do Android é usado durante o processo de build do Xamarin.Android.
Atualmente, o padrão é `dx`. Para obter mais informações, confira nossa documentação sobre o [D8 e R8][d8-r8].

[dex]: https://source.android.com/devices/tech/dalvik/dalvik-bytecode
[d8-r8]: https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/D8andR8.md

## <a name="androidenabledesugar"></a>AndroidEnableDesugar

Uma propriedade booliana que determina se o `desugar` está habilitado. O Android não é compatível com todos os recursos de Java 8 no momento, e a cadeia de ferramentas padrão implementa novos recursos de linguagem ao executar transformações de código de bytes, chamadas `desugar`, na saída do compilador `javac`. O padrão será `False` se estiver usando e usará `AndroidDexTool=dx` como padrão `True` se estiver usando [`$(AndroidDexTool)`](#androiddextool) = `d8` .

## <a name="androidenablegoogleplaystorechecks"></a>AndroidEnableGooglePlayStoreChecks

Uma propriedade bool que permite aos desenvolvedores desabilitar as seguintes verificações de Google Play Store: XA1004, XA1005 e XA1006. Isso é útil para os desenvolvedores que não pretendem trabalhar com a Google Play Store e não desejam executar essas verificações.

Adicionado no Xamarin.Android 9.4.

## <a name="androidenablemultidex"></a>AndroidEnableMultiDex

Uma propriedade booliana que determina se o suporte Dex será usado no final `.apk` .

O suporte para essa propriedade foi adicionado no Xamarin.Android 5.1.

Essa propriedade é `False` por padrão.

## <a name="androidenablepreloadassemblies"></a>AndroidEnablePreloadAssemblies

Uma propriedade booliana que controla se todos os assemblies gerenciados agrupados no pacote de aplicativo são carregados durante a inicialização do processo ou não.

Quando definidos como `True`, todos os assemblies agrupados dentro do pacote de aplicativos serão carregados durante a inicialização do processo, antes que qualquer aplicativo de código seja invocado.
Esse processo é consistente com aquilo que o Xamarin.Android fazia em versões anteriores ao Xamarin.Android 9.2.

Quando definido como `False`, os assemblies somente serão carregados conforme necessário.
Isso faz com que os aplicativos iniciem mais rapidamente e também é mais consistente com a semântica da área de trabalho do .NET.  Para ver a economia de tempo, defina a Propriedade do Sistema `debug.mono.log` para incluir `timing` e procure a mensagem `Finished loading assemblies: preloaded` dentro de `adb logcat`.

Aplicativos ou bibliotecas que usam injeção de dependência podem *exigir* que essa propriedade seja `True` se, por sua vez, exigir que `AppDomain.CurrentDomain.GetAssemblies()` o retorne todos os assemblies no pacote de aplicativos, mesmo se o assembly não fosse necessário.

Por padrão, o valor será definido como `True`.

Adicionado no Xamarin.Android 9.2.

## <a name="androidenableprofiledaot"></a>AndroidEnableProfiledAot

Uma propriedade booliana que determina se os perfis de AOT são usados durante a compilação antecipada.

Os perfis são listados em [`@(AndroidAotProfile)`](~/android/deploy-test/building-apps/build-items.md#androidaotprofile)
grupo de itens. Este ItemGroup contém perfis padrão. Ele pode ser substituído removendo os um existente e adicionando seus próprios perfis de AOT.

O suporte para essa propriedade foi adicionado no Xamarin.Android 9.4.

Essa propriedade é `False` por padrão.

## <a name="androidenablesgenconcurrent"></a>AndroidEnableSGenConcurrent

Uma propriedade booliana que determina se o coletor de [GC simultâneo](https://www.mono-project.com/docs/about-mono/releases/4.8.0/#concurrent-sgen) do mono será usado.

O suporte para essa propriedade foi adicionado no Xamarin.Android 7.2.

Essa propriedade é `False` por padrão.

## <a name="androiderroroncustomjavaobject"></a>AndroidErrorOnCustomJavaObject

Uma propriedade booliana que determina se os tipos podem ser implementados `Android.Runtime.IJavaObject` 
 *sem* também herdar de `Java.Lang.Object` ou `Java.Lang.Throwable` :

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

Quando for verdadeiro, esses tipos gerarão um erro XA4212, caso contrário, um aviso de XA4212 será gerado.

O suporte para essa propriedade foi adicionado no Xamarin.Android 8.1.

Essa propriedade é `True` por padrão.

## <a name="androidexplicitcrunch"></a>AndroidExplicitCrunch

Não tem mais suporte no Xamarin. Android 11,0.

## <a name="androidextraaotoptions"></a>AndroidExtraAotOptions

Uma propriedade de cadeia de caracteres que permite passar opções adicionais para o compilador mono durante a `Aot` tarefa para projetos que têm o [`$(AndroidEnableProfiledAot)`](#androidenableprofiledaot) ou o [`$(AotAssemblies)`](#aotassemblies) definido como `true` .
O valor da cadeia de caracteres da propriedade é adicionado ao arquivo de resposta ao chamar o compilador cruzado mono.

Em geral, essa propriedade deve ser deixada em branco, mas em determinados cenários especiais ele pode fornecer uma flexibilidade útil.

Observe que essa propriedade é diferente da propriedade Related `$(AndroidAotAdditionalArguments)` . Essa propriedade coloca argumentos separados por vírgula na `--aot` opção do compilador mono. `$(AndroidExtraAotOptions)` em vez disso, o passa por opções completas separadas por espaço autônomo como `--verbose` ou `--debug` para o compilador.

Adicionado no Xamarin. Android 10,2.

## <a name="androidfastdeploymenttype"></a>AndroidFastDeploymentType

Uma `:` lista de valores separados por dois-pontos para controlar quais tipos podem ser implantados no [diretório de implantação rápida](~/android/deploy-test/building-apps/build-process.md#Fast_Deployment) no dispositivo de destino quando a [`$(EmbedAssembliesIntoApk)`](#embedassembliesintoapk) Propriedade do MSBuild é `False` . Se um recurso é implantado por Fast Deployment, ele *não* é inserido no `.apk` gerado, o que pode acelerar os tempos de implantação. (Quanto mais rápido for implantado, menos frequentemente `.apk` será necessário recriar e o processo de instalação poderá ser mais rápido). Os valores válidos incluem:

- `Assemblies`: implantar os assemblies do aplicativo.

- `Dexes`: implantar arquivos `.dex`, recursos do Android e ativos do Android. **Esse valor pode ser usado *somente* em dispositivos que executam o Android 4.4 ou posterior (API-19).**

O valor padrão é `Assemblies`.

**Experimental**. Adicionado no Xamarin.Android 6.1.

## <a name="androidgeneratejnimarshalmethods"></a>AndroidGenerateJniMarshalMethods

Uma propriedade bool que habilita a geração de métodos de Marshal JNI como parte do processo de compilação. Isso reduz significativamente o `System.Reflection` uso no código auxiliar de associação.

Por padrão, ela será definida como False. Se os desenvolvedores desejarem usar o novo recurso de métodos de marshal JNI, eles poderão definir

```xml
<AndroidGenerateJniMarshalMethods>True</AndroidGenerateJniMarshalMethods>
```

em seu `.csproj` . Como alternativa, forneça a propriedade na linha de comando por meio de

```shell
/p:AndroidGenerateJniMarshalMethods=True
```

**Experimental**. Adicionado no Xamarin.Android 9.2.
O valor padrão é False.

## <a name="androidgeneratejnimarshalmethodsadditionalarguments"></a>AndroidGenerateJniMarshalMethodsAdditionalArguments

Uma propriedade de cadeia de caracteres que pode ser usada para adicionar parâmetros adicionais à `jnimarshalmethod-gen.exe` invocação.  Isso é útil para a depuração, para que opções como `-v`, `-d` ou `--keeptemp` possam ser usadas.

O valor padrão é uma cadeia de caracteres vazia. Ele pode ser definido no `.csproj` arquivo ou na linha de comando. Por exemplo:

```xml
<AndroidGenerateJniMarshalMethodsAdditionalArguments>-v -d --keeptemp</AndroidGenerateJniMarshalMethodsAdditionalArguments>
```

ou:

```shell
/p:AndroidGenerateJniMarshalMethodsAdditionalArguments="-v -d --keeptemp"
```

Adicionado no Xamarin.Android 9.2.

## <a name="androidgeneratelayoutbindings"></a>AndroidGenerateLayoutBindings

Habilita a geração de [código de layout-behind](https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/LayoutCodeBehind.md) se definido como `true` ou desabilita completamente se definido como `false` . O valor padrão é `false`.

## <a name="androidhttpclienthandlertype"></a>AndroidHttpClientHandlerType

Controla a `System.Net.Http.HttpMessageHandler` implementação padrão que será usada pelo `System.Net.Http.HttpClient` construtor padrão. O valor é um nome de tipo qualificado por assembly de uma `HttpMessageHandler` subclasse, adequado para uso com [`System.Type.GetType(string)`](/dotnet/api/system.type.gettype#System_Type_GetType_System_String_) .
Os valores mais comuns para essa propriedade são:

- `Xamarin.Android.Net.AndroidClientHandler`: Use as APIs Java do Android para executar solicitações de rede. Isso permite acessar as URLs TLS 1.2 quando a versão do Android subjacente dá suporte ao TLS 1.2. Somente versões Android 5.0 e posteriores fornecem suporte confiável ao TLS 1.2 por meio de Java.

  Isso corresponde à opção do **Android** nas páginas de propriedades do Visual Studio e na opção **AndroidClientHandler** nas páginas de propriedades de Visual Studio para Mac.

  O assistente de novo projeto seleciona essa opção para novos projetos quando a **versão mínima do Android** é configurada para **Android 5,0 (pirulito)** ou superior no Visual Studio ou quando as **plataformas de destino** são definidas para o **mais recente e o melhor** em Visual Studio para Mac.

- Indefinição/a cadeia de caracteres vazia: isso é equivalente a `System.Net.Http.HttpClientHandler, System.Net.Http`

  Isso corresponde à opção **padrão** nas páginas de propriedades do Visual Studio.

  O novo assistente de projeto seleciona essa opção para novos projetos quando a **Versão mínima do Android** está configurada como **Android 4.4.87** ou versão mais antiga no Visual Studio ou quando o campo **Plataformas de destino** está definido como **Desenvolvimento Moderno** ou **Máxima compatibilidade** no Visual Studio para Mac.

- `System.Net.Http.HttpClientHandler, System.Net.Http`: Use o gerenciado `HttpMessageHandler` .

  Isso corresponde à opção **gerenciada** nas páginas de propriedades do Visual Studio.

> [!NOTE]
> Se o suporte a TLS 1,2 for necessário em versões do Android anteriores à 5,0 *ou* se o suporte a TLS 1,2 for necessário com o `System.Net.WebClient` e as APIs relacionadas, [`$(AndroidTlsProvider)`](#androidtlsprovider) deverá ser usado.

> [!NOTE]
> O suporte para essa propriedade funciona definindo a [ `XA_HTTP_CLIENT_HANDLER_TYPE` variável de ambiente](~/android/deploy-test/environment.md).
> Um `$XA_HTTP_CLIENT_HANDLER_TYPE` valor encontrado em um arquivo com uma ação de compilação de [`@(AndroidEnvironment)`](~/android/deploy-test/building-apps/build-items.md#androidenvironment)
> terão precedência.

Adicionado no Xamarin.Android 6.1.

## <a name="androidkeystore"></a>AndroidKeyStore

Um valor booliano que indica se informações de assinatura personalizadas devem ser usadas. O valor padrão é `False`, que significa que a chave de assinatura de depuração padrão será usada para assinar pacotes.

## <a name="androidlaunchactivity"></a>AndroidLaunchActivity

A atividade do Android a ser iniciada.

## <a name="androidlinkmode"></a>AndroidLinkMode

Especifica qual tipo de [vinculação](~/android/deploy-test/linker.md) deve ser executada em assemblies contidos no pacote do Android. Usado somente em projetos de aplicativo Android. O valor padrão é *SdkOnly*. Os valores válidos são:

- **None**: nenhuma tentativa de vinculação ocorrerá.

- **SdkOnly**: a vinculação ocorrerá apenas nas bibliotecas de classe base, não nos assemblies do usuário.

- **Full**: a vinculação ocorrerá nas bibliotecas de classe base e nos assemblies do usuário.

  > [!NOTE]
  > o uso de um valor `AndroidLinkMode` definido como *Full* normalmente resulta em aplicativos com falha, principalmente quando a reflexão é usada. Evite usar isso, a menos que você *realmente* saiba o que está fazendo.

```xml
<AndroidLinkMode>SdkOnly</AndroidLinkMode>
```

## <a name="androidlinkskip"></a>AndroidLinkSkip

Especifica uma lista delimitada por ponto-e-vírgula ( `;` ) de nomes de assembly, sem extensões de arquivo, de assemblies que não devem ser vinculados. Usado somente em projetos de aplicativo Android.

```xml
<AndroidLinkSkip>Assembly1;Assembly2</AndroidLinkSkip>
```

## <a name="androidlinktool"></a>AndroidLinkTool

Uma propriedade do estilo de enumeração com valores válidos de `proguard` ou `r8` . Indica qual redutor de código é usado para o código Java. Atualmente, o padrão é uma cadeia de caracteres vazia ou `proguard` quando `$(AndroidEnableProguard)` é `True`. Para obter mais informações, confira nossa documentação sobre o [D8 e R8][d8-r8].

[d8-r8]: https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/D8andR8.md

## <a name="androidlintenabled"></a>AndroidLintEnabled

Uma propriedade bool que permite ao desenvolvedor executar a ferramenta do Android `lint` como parte do processo de empacotamento.

Quando `$(AndroidLintEnabled)` = true, as seguintes propriedades são usadas:

- [`$(AndroidLintEnabledIssues)`](#androidlintenabledissues):
- [`$(AndroidLintDisabledIssues)`](#androidlintdisabledissues):
- [`$(AndroidLintCheckIssues)`](#androidlintcheckissues):

As seguintes ações de compilação também podem ser usadas:

- [`@(AndroidLintConfig)`](~/android/deploy-test/building-apps/build-items.md#androidlintconfig):

Consulte a [ajuda de fiapos](https://developer.android.com/studio/write/lint) para obter mais detalhes sobre as ferramentas do Android `lint` .

## <a name="androidlintenabledissues"></a>AndroidLintEnabledIssues

Essa propriedade só é usada quando [`$(AndroidLintEnabled)`](#androidlintenabled) = true.

Uma lista separada por vírgulas de problemas de fiapos a serem habilitados.

## <a name="androidlintdisabledissues"></a>AndroidLintDisabledIssues

Essa propriedade só é usada quando [`$(AndroidLintEnabled)`](#androidlintenabled) = true.

Uma lista separada por vírgulas de problemas de fiapos a serem desabilitados.

## <a name="androidlintcheckissues"></a>AndroidLintCheckIssues

Essa propriedade só é usada quando [`$(AndroidLintEnabled)`](#androidlintenabled) = true.

Uma lista separada por vírgulas de problemas de fiapos a serem verificados.

Observação: somente esses problemas serão verificados.

## <a name="androidmanagedsymbols"></a>AndroidManagedSymbols

Uma propriedade booliana que controla se os pontos de sequência são gerados para que as informações de nome de arquivo e de número de linha possam ser extraídas dos `Release` rastreamentos de pilha.

Adicionado no Xamarin.Android 6.1.

## <a name="androidmanifest"></a>AndroidManifest

Especifica um nome de arquivo a ser usado como modelo para o aplicativo [`AndroidManifest.xml`](~/android/platform/android-manifest.md) .
Durante o build, quaisquer outros valores necessários serão mesclados para produzir o `AndroidManifest.xml` propriamente dito.
O `$(AndroidManifest)` deve conter o nome do pacote no atributo `/manifest/@package`.

## <a name="androidmanifestmerger"></a>AndroidManifestMerger

Especifica a implementação para mesclar *AndroidManifest.xml* arquivos. Essa é uma propriedade do tipo enum em que `legacy` o seleciona a implementação do C# original e `manifestmerger.jar` seleciona a implementação Java do Google.

O valor padrão é no momento `legacy` . Isso será alterado para `manifestmerger.jar` em uma versão futura para alinhar o comportamento com Android Studio.

A fusão do Google permite suporte para `xmlns:tools="http://schemas.android.com/tools"` , conforme descrito na [documentação do Android][manifest-merger].

Introduzido no Xamarin. Android 10,2

[manifest-merger]: https://developer.android.com/studio/build/manifest-merge

## <a name="androidmanifestplaceholders"></a>AndroidManifestPlaceholders

Uma lista separada por ponto-e-vírgula de pares de substituição de chave-valor para *AndroidManifest.xml*, em que cada par tem o formato `key=value` .

Por exemplo, um valor de propriedade de `assemblyName=$(AssemblyName)` define um `${assemblyName}` espaço reservado que pode aparecer em *AndroidManifest.xml*:

```xml
<application android:label="${assemblyName}"
```

Isso fornece uma maneira de inserir variáveis do processo de compilação no arquivo de *AndroidManifest.xml* .

## <a name="androidmultidexclasslistextraargs"></a>AndroidMultiDexClassListExtraArgs

Uma propriedade de cadeia de caracteres que permite aos desenvolvedores passar argumentos adicionais para o `com.android.multidex.MainDexListBuilder` ao gerar o `multidex.keep` arquivo.

Um caso específico é se você estiver recebendo o erro a seguir durante a compilação de `dx`.

```text
com.android.dex.DexException: Too many classes in --main-dex-list, main dex capacity exceeded
```

Se você estiver recebendo esse erro, poderá adicionar o seguinte ao `.csproj` .

```xml
<DxExtraArguments>--force-jumbo </DxExtraArguments>
<AndroidMultiDexClassListExtraArgs>--disable-annotation-resolution-workaround</AndroidMultiDexClassListExtraArgs>
```

Assim, a etapa `dx` poderá ter êxito.

Adicionado no Xamarin.Android 8.3.

## <a name="androidpackageformat"></a>AndroidPackageFormat

Uma propriedade do estilo de enumeração com valores válidos de `apk` ou `aab` . Isso indica se você deseja empacotar o aplicativo Android como um [arquivo APK][apk] ou um [Lote de Aplicativo do Android][bundle]. Os Lote de Aplicativo são um novo formato para builds de `Release` destinados ao envio no Google Play. Atualmente, este valor assume o padrão `apk`.

Quando `$(AndroidPackageFormat)` é definido como `aab`, outras propriedades do MSBuild são definidas, que são necessárias para os Lote de Aplicativo do Android:

- [`$(AndroidUseAapt2)`](~/android/deploy-test/building-apps/build-properties.md#androiduseaapt2) é `True` .
- [`$(AndroidUseApkSigner)`](#androiduseapksigner) é `False` .
- [`$(AndroidCreatePackagePerAbi)`](#androidcreatepackageperabi) é `False` .

[apk]: https://en.wikipedia.org/wiki/Android_application_package
[bundle]: https://developer.android.com/platform/technology/app-bundle

## <a name="androidpackagenamingpolicy"></a>AndroidPackageNamingPolicy

Uma propriedade de enum-Style para especificar os nomes de pacote Java do código-fonte Java gerado.

No Xamarin. Android 10,2 e posterior, o único valor com suporte é `LowercaseCrc64` .

No Xamarin. Android 10,1, `LowercaseMD5` também estava disponível um valor de transição que permitia voltar para o estilo de nome de pacote Java original, como usado no Xamarin. Android 10,0 e anterior. Essa opção foi removida no Xamarin. Android 10,2 para melhorar a compatibilidade com ambientes de compilação que têm conformidade com o FIPS aplicada.

Adicionado no Xamarin. Android 10,1.

## <a name="androidr8ignorewarnings"></a>AndroidR8IgnoreWarnings

Especifica a `-ignorewarnings` regra do PROGuard para `r8` . Isso permite `r8` continuar com a compilação Dex, mesmo que determinados avisos sejam encontrados. O padrão é `True` , mas pode ser definido como `False` para impor um comportamento mais estrito. Consulte o [manual do PROGuard](https://www.guardsquare.com/products/proguard/manual/usage) para obter detalhes.

Adicionado no Xamarin. Android 10,3.

## <a name="androidr8jarpath"></a>AndroidR8JarPath

O caminho a ser `r8.jar` usado com o Dex de R8-Compiler e o recolhido. O padrão é um caminho na instalação do Xamarin.Android. Para obter mais informações, confira nossa documentação sobre o [D8 e R8][d8-r8].

## <a name="androidresgenextraargs"></a>AndroidResgenExtraArgs

Especifica opções de linha de comando adicionais para passar para o comando **AAPT** ao processar ativos e recursos do Android.

## <a name="androidresgenfile"></a>AndroidResgenFile

Especifica o nome do arquivo de recurso a ser gerado. O modelo padrão define isso como `Resource.designer.cs`.

## <a name="androidsdkbuildtoolsversion"></a>AndroidSdkBuildToolsVersion

O pacote de ferramentas de Build SDK do Android fornece as ferramentas **AAPT** e **zipalign** , entre outras. Várias versões diferentes do pacote de ferramentas de build podem ser instaladas simultaneamente. O pacote de ferramentas de build escolhido para empacotamento é criado procurando e usando uma versão de ferramentas de build "preferencial", se uma está presente; se a versão "preferencial" *não* está presente, o pacote de ferramentas de build com a versão mais alta entre os instalados é usado.

A propriedade do MSBuild `$(AndroidSdkBuildToolsVersion)` contém a versão das ferramentas de build preferencial. O sistema de build do Xamarin.Android fornecerá um valor padrão em `Xamarin.Android.Common.targets`, que poderá ser substituído no arquivo de projeto para escolher uma versão de ferramentas de build alternativa se, por exemplo, o aapt mais recente estiver falhando enquanto uma versão anterior do aapt sabidamente funciona.

## <a name="androidsigningkeyalias"></a>AndroidSigningKeyAlias

Especifica o alias para a chave no keystore. Este é o valor de **keytool-alias** usado ao criar o repositório de chaves.

## <a name="androidsigningkeypass"></a>AndroidSigningKeyPass

Especifica a senha da chave no arquivo de keystore. Esse é o valor digitado quando `keytool` solicita **Insira a senha da chave para $(AndroidSigningKeyAlias)**.

No Xamarin. Android 10,0 e anteriores, essa propriedade só dá suporte a senhas de texto sem formatação.

No Xamarin. Android 10,1 e posterior, essa propriedade também dá suporte a `env:` `file:` prefixos e que podem ser usados para especificar uma variável de ambiente ou um arquivo que contenha a senha. Essas opções fornecem uma maneira de impedir que a senha apareça nos logs de compilação.

Por exemplo, para usar uma variável de ambiente chamada *AndroidSigningPassword*:

```xml
<PropertyGroup>
    <AndroidSigningKeyPass>env:AndroidSigningPassword</AndroidSigningKeyPass>
</PropertyGroup>
```

Para usar um arquivo localizado em `C:\Users\user1\AndroidSigningPassword.txt` :

```xml
<PropertyGroup>
    <AndroidSigningKeyPass>file:C:\Users\user1\AndroidSigningPassword.txt</AndroidSigningKeyPass>
</PropertyGroup>
```

> [!NOTE]
> `env:`Não há suporte para o prefixo quando [`$(AndroidPackageFormat)`](#androidpackageformat) é definido como `aab` .

## <a name="androidsigningkeystore"></a>AndroidSigningKeyStore

Especifica o nome do arquivo de repositório de chaves criado por `keytool` . Isso corresponde ao valor fornecido à opção **keytool -keystore**.

## <a name="androidsigningstorepass"></a>AndroidSigningStorePass

Especifica a senha para [`$(AndroidSigningKeyStore)`](#androidsigningkeystore) .
Esse é o valor fornecido a `keytool` ao criar o arquivo de repositório de chaves e ao solicitar **Insira a senha do repositório de chaves:**.

No Xamarin. Android 10,0 e anteriores, essa propriedade só dá suporte a senhas de texto sem formatação.

No Xamarin. Android 10,1 e posterior, essa propriedade também dá suporte a `env:` `file:` prefixos e que podem ser usados para especificar uma variável de ambiente ou um arquivo que contenha a senha. Essas opções fornecem uma maneira de impedir que a senha apareça nos logs de compilação.

Por exemplo, para usar uma variável de ambiente chamada *AndroidSigningPassword*:

```xml
<PropertyGroup>
    <AndroidSigningStorePass>env:AndroidSigningPassword</AndroidSigningStorePass>
</PropertyGroup>
```

Para usar um arquivo localizado em `C:\Users\user1\AndroidSigningPassword.txt` :

```xml
<PropertyGroup>
    <AndroidSigningStorePass>file:C:\Users\user1\AndroidSigningPassword.txt</AndroidSigningStorePass>
</PropertyGroup>
```

> [!NOTE]
> `env:`Não há suporte para o prefixo quando [`$(AndroidPackageFormat)`](#androidpackageformat) é definido como `aab` .

## <a name="androidsupportedabis"></a>AndroidSupportedAbis

Uma propriedade de cadeia de caracteres que contém uma lista delimitada por ponto-e-vírgula ( `;` ) de abis que deve ser incluída no `.apk` .

Os valores compatíveis incluem:

- `armeabi-v7a`
- `x86`
- `arm64-v8a`: requer o Xamarin.Android 5.1 e posterior.
- `x86_64`: requer o Xamarin.Android 5.1 e posterior.

## <a name="androidtlsprovider"></a>AndroidTlsProvider

Um valor de cadeia de caracteres que especifica qual provedor TLS deve ser usado em um aplicativo. Os valores possíveis são:

- Indefinição/a cadeia de caracteres vazia: no Xamarin. Android 7,3 e superior, isso é equivalente a `btls` .

  no Xamarin.Android 7.1, isso é equivalente a `legacy`.

  Isso corresponde à configuração **padrão** nas páginas de propriedades do Visual Studio.

- `btls`: usar [Boring SSL](https://boringssl.googlesource.com/boringssl) para a comunicação por TLS com [HttpWebRequest](xref:System.Net.HttpWebRequest).

  Isso permite usar o TLS 1.2 em todas as versões do Android.

  Isso corresponde à configuração **nativa do TLS 1.2 +** nas páginas de propriedades do Visual Studio.

- `legacy`: No Xamarin. Android 10,1 e anterior, use a implementação de SSL gerenciado por histórico para interação de rede. Isso *não* é compatível com a TLS 1.2.

  Isso corresponde à configuração de **TLS 1,0 gerenciada** nas páginas de propriedades do Visual Studio.

  No Xamarin. Android 10,2 e posterior, esse valor é ignorado e a `btls` configuração é usada.

- `default`: Esse valor é improvável de ser usado em projetos Xamarin. Android. Em vez disso, o valor recomendado a ser usado é a cadeia de caracteres vazia, que corresponde à configuração **padrão** nas páginas de propriedades do Visual Studio.

  O valor `default` não é oferecido nas páginas de propriedades do Visual Studio.

  Atualmente, isso é equivalente a `legacy`.

Adicionado no Xamarin.Android 7.1.

## <a name="androiduseaapt2"></a>AndroidUseAapt2

Uma propriedade booliana que permite ao desenvolvedor controlar o uso da `aapt2` ferramenta para empacotamento.
Por padrão, isso será false e o Xamarin. Android usará `aapt` .
Se o desenvolvedor quiser usar a nova `aapt2` funcionalidade, adicione:

```xml
<AndroidUseAapt2>True</AndroidUseAapt2>
```

em seu `.csproj` . Como alternativa, forneça a propriedade na linha de comando:

```shell
/p:AndroidUseAapt2=True
```

Adicionado no Xamarin.Android 8.3.

## <a name="androiduseapksigner"></a>AndroidUseApkSigner

Uma propriedade bool que permite ao desenvolvedor usar a `apksigner` ferramenta em vez de `jarsigner` .

Adicionado no Xamarin.Android 8.2.

## <a name="androidusedefaultaotprofile"></a>AndroidUseDefaultAotProfile

Uma propriedade bool que permite ao desenvolvedor suprimir o uso dos perfis de AOT padrão.

Para suprimir os perfis de AOT padrão, defina a propriedade como `false` .

Adicionado no Xamarin. Android 10,1.

## <a name="androiduselegacyversioncode"></a>AndroidUseLegacyVersionCode

Uma propriedade booliana que permite ao desenvolvedor reverter o cálculo de versionCode para seu comportamento anterior do Xamarin. Android 8,2. Isso deve ser usado SOMENTE por desenvolvedores com aplicativos existentes na Google Play Store. É altamente recomendável que a nova [`$(AndroidVersionCodePattern)`](#androidversioncodepattern) propriedade seja usada.

Adicionado no Xamarin.Android 8.2.

## <a name="androidusemanageddesigntimeresourcegenerator"></a>AndroidUseManagedDesignTimeResourceGenerator

Uma propriedade booliana que alternará as compilações de tempo de design para usar o analisador de recursos gerenciado em vez de `aapt` .

Adicionado no Xamarin.Android 8.1.

## <a name="androidusesharedruntime"></a>AndroidUseSharedRuntime

Uma propriedade booliana que determina se os *pacotes de tempo de execução compartilhados* são necessários para executar o aplicativo no dispositivo de destino. Depender dos pacotes de runtime compartilhado permite que o pacote do aplicativo seja menor, acelerando o processo de criação e implantação de pacote e resultando em um ciclo de build/implantação/depuração mais rápido.

Essa propriedade deve ser `True` para builds de depuração e `False` para projetos de versão.

## <a name="androidversioncodepattern"></a>AndroidVersionCodePattern

Uma propriedade de cadeia de caracteres que permite ao desenvolvedor personalizar o `versionCode` no manifesto.
Confira [Criar um código de versão para o APK](~/android/deploy-test/building-apps/abi-specific-apks.md) para obter informações sobre como decidir um `versionCode`.

exemplos de ome, se `abi` for `armeabi` e `versionCode` nos s manifest `123` , `{abi}{versionCode}` mal produzirão um versionCode de `1123` quando `$(AndroidCreatePackagePerAbi)` s true, caso contrário produzirá um valor de 123.
f `abi` é `x86_64` e `versionCode` no manifesto s `44` . Isso produzirá `544` quando `$(AndroidCreatePackagePerAbi)` s true, caso contrário produzirá um valor de `44` .

Se nós incluíssemos uma cadeia de caracteres de formato de preenchimento esquerdo `{abi}{versionCode:0000}`, isso produziria `50044` porque estamos aplicando preenchimento esquerdo ao `versionCode` com `0`. Como alternativa, você pode usar o preenchimento decimal, como `{abi}{versionCode:D4}`
que tem o mesmo resultado do exemplo anterior.

Somente os formatos de cadeias de caracteres de preenchimento '0' e 'Dx' são compatíveis, já que o valor PRECISA ser um inteiro.

Itens chave pré-definidos

- **abi**  &ndash; Insere a abi direcionada para o aplicativo
  - 2 &ndash;`armeabi-v7a`
  - 3 &ndash;`x86`
  - 4 &ndash;`arm64-v8a`
  - 5 &ndash;`x86_64`

- **minSDK**  &ndash; insere o valor mínimo compatível do SDK encontrado no `AndroidManifest.xml`, ou então `11` se não há nenhum definido.

- **versionCode** &ndash; Usa o código de versão diretamente do `Properties\AndroidManifest.xml` .

Você pode definir itens personalizados usando a propriedade `$(AndroidVersionCodeProperties)` (definida a seguir).

Por padrão o valor será definido como `{abi}{versionCode:D6}`. Se um desenvolvedor quiser manter o comportamento anterior, você poderá configurar a propriedade `$(AndroidUseLegacyVersionCode)` como `true` para substituir o padrão

Adicionado no Xamarin.Android 7.2.

## <a name="androidversioncodeproperties"></a>AndroidVersionCodeProperties

Uma propriedade de cadeia de caracteres que permite ao desenvolvedor definir itens personalizados para usar com o [`$(AndroidVersionCodePattern)`](#androidversioncodepattern) .
Eles estão na forma de um par `key=value`. Todos os itens no `value` devem ser valores inteiros. Por exemplo: `screen=23;target=$(_AndroidApiLevel)`. Como você pode ver, você pode fazer uso de propriedades do MSBuild existentes ou personalizadas na cadeia de caracteres.

Adicionado no Xamarin.Android 7.2.

## <a name="aotassemblies"></a>AotAssemblies

Uma propriedade booliana que determina se os assemblies terão ou não o tempo de vida compilado no código nativo e incluídos no `.apk` .

O suporte para essa propriedade foi adicionado no Xamarin.Android 5.1.

Essa propriedade é `False` por padrão.

## <a name="aprofutilextraoptions"></a>AProfUtilExtraOptions

Opções extras a serem passadas para `aprofutil` .

## <a name="beforegenerateandroidmanifest"></a>BeforeGenerateAndroidManifest

Os destinos do MSBuild listados nesta propriedade serão executados diretamente antes `_GenerateJavaStubs` .

Adicionado no Xamarin.Android 9.4.

## <a name="configuration"></a>Configuração

Especifica a configuração de compilação a ser usada, como "debug" ou "Release". A propriedade Configuration é usada para determinar os valores padrão de outras propriedades que determinam o comportamento de destino. Configurações adicionais podem ser criadas dentro de seu IDE.

*Por padrão*, a `Debug` configuração resultará na [`Install`](~/android/deploy-test/building-apps/build-targets.md#install)
e [`SignAndroidPackage`](~/android/deploy-test/building-apps/build-targets.md#signandroidpackage)
destinos que criam um pacote Android menor que requer a presença de outros arquivos e pacotes para operar.

A `Release` configuração padrão resultará na [`Install`](~/android/deploy-test/building-apps/build-targets.md#install)
e [`SignAndroidPackage`](~/android/deploy-test/building-apps/build-targets.md#signandroidpackage)
destinos que criam um pacote Android que *é autônomo*e podem ser usados sem a instalação de outros pacotes ou arquivos.

## <a name="debugsymbols"></a>DebugSymbols

Um valor booliano que determina se o pacote do Android é *depurável*, em combinação com a [`$(DebugType)`](#debugtype) propriedade.
Um pacote depurável contém símbolos de depuração, define o [ `//application/@android:debuggable` atributo](https://developer.android.com/guide/topics/manifest/application-element#debug) como `true` e adiciona automaticamente o[`INTERNET`](https://developer.android.com/reference/android/Manifest.permission#INTERNET)
permissão para que um depurador possa ser anexado ao processo. Um aplicativo é depurável se `DebugSymbols` é `True` *e* `DebugType` é a cadeia de caracteres vazia ou `Full`.

## <a name="debugtype"></a>DebugType

Especifica o [tipo de símbolos de depuração](/visualstudio/msbuild/csc-task) a serem gerados como parte da compilação, o que também afeta se o aplicativo é depurável. Os valores possíveis incluem:

- **Full**: todos os símbolos são gerados. Se o [`DebugSymbols`](#debugsymbols)
  A Propriedade MSBuild também é `True` , então, o pacote de aplicativos é depurável.

- **PdbOnly**: os símbolos "PDB" são gerados. O pacote de aplicativos não é depurável.

Se `DebugType` não está definida ou é uma cadeia de caracteres vazia, a propriedade `DebugSymbols` controla se o Aplicativo é depurável.

## <a name="embedassembliesintoapk"></a>EmbedAssembliesIntoApk

Uma propriedade booliana que determina se os assemblies do aplicativo devem ou não ser inseridos no pacote de aplicativos.

Essa propriedade deve ser `True` para builds de versão e `False` para builds de depuração. Ele *pode* precisar ser `True` em builds de depuração se o Fast Deployment não é compatível com o dispositivo de destino.

Quando essa propriedade for `False` , então o [`$(AndroidFastDeploymentType)`](#androidfastdeploymenttype)
A Propriedade MSBuild também controla o que será inserido no `.apk` , o que pode afetar a implantação e os tempos de recriação.

## <a name="enablellvm"></a>EnableLLVM

Uma propriedade booliana que determina se LLVM será usado ou não em compilação antecipada de assemblies em código nativo.

O Android NDK deve ser instalado para criar um projeto que tenha essa propriedade habilitada.

O suporte para essa propriedade foi adicionado no Xamarin.Android 5.1.

Essa propriedade é `False` por padrão.

Essa propriedade é ignorada a menos que a [`$(AotAssemblies)`](#aotassemblies) Propriedade do MSBuild seja `True` .

## <a name="enableproguard"></a>EnableProguard

Uma propriedade booliana que determina se o [PROGuard](https://developer.android.com/tools/help/proguard.html) é executado como parte do processo de empacotamento para vincular o código Java.

O suporte para essa propriedade foi adicionado no Xamarin.Android 5.1.

Essa propriedade é `False` por padrão.

Quando `True` , os arquivos [@ (ProguardConfiguration)](~/android/deploy-test/building-apps/build-items.md#proguardconfiguration) serão usados para controlar `proguard` a execução.

## <a name="javamaximumheapsize"></a>JavaMaximumHeapSize

Especifica o valor do valor do parâmetro **Java** 
 `-Xmx` a ser usado ao compilar o `.dex` arquivo como parte do processo de empacotamento. Se não for especificado, a opção `-Xmx` fornecerá **java** com um valor de `1G`. Foi verificado que isso normalmente é necessário no Windows em comparação com outras plataformas.

A especificação dessa propriedade será necessária se o [ `_CompileDex` destino lançar `java.lang.OutOfMemoryError` um ](https://bugzilla.xamarin.com/show_bug.cgi?id=18327).

Personalize o valor alterando:

```xml
<JavaMaximumHeapSize>1G</JavaMaximumHeapSize>
```

## <a name="javaoptions"></a>Javaoptions

Especifica opções de linha de comando adicionais para passar para **Java** ao compilar o `.dex` arquivo.

## <a name="jarsignertimestampauthoritycertificatealias"></a>JarsignerTimestampAuthorityCertificateAlias

Essa propriedade permite que você especifique um alias no keystore para uma autoridade de carimbo de data/hora.
Consulte a documentação de [suporte do carimbo de data/hora da assinatura](https://docs.oracle.com/javase/8/docs/technotes/guides/security/time-of-signing.html) Java para obter mais detalhes.

```xml
<PropertyGroup>
    <JarsignerTimestampAuthorityCertificateAlias>Alias</JarsignerTimestampAuthorityCertificateAlias>
</PropertyGroup>
```

## <a name="jarsignertimestampauthorityurl"></a>JarsignerTimestampAuthorityUrl

Essa propriedade permite que você especifique uma URL para um serviço de autoridade de carimbo de data/hora. Isso pode ser usado para verificar se a `.apk` assinatura inclui um carimbo de data/hora.
Consulte a documentação de [suporte do carimbo de data/hora da assinatura](https://docs.oracle.com/javase/8/docs/technotes/guides/security/time-of-signing.html) Java para obter mais detalhes.

```xml
<PropertyGroup>
    <JarsignerTimestampAuthorityUrl>http://example.tsa.url</JarsignerTimestampAuthorityUrl>
</PropertyGroup>
```

## <a name="linkerdumpdependencies"></a>LinkerDumpDependencies

Uma propriedade bool que habilita a geração de arquivo de dependências do vinculador. Esse arquivo pode ser usado como entrada para a ferramenta [illinkanalyzer](https://github.com/mono/linker/blob/master/src/analyzer/README.md).

O valor padrão é False.

## <a name="mandroidi18n"></a>MandroidI18n

Especifica o suporte à internacionalização incluído com o aplicativo, como agrupamento e classificação de tabelas. O valor é uma lista separada por vírgula ou ponto e vírgula de um ou mais dos seguintes valores que não diferenciam maiúsculas e minúsculas:

- **None**: não incluir nenhuma codificação adicional.

- **All**: incluir todas as codificações disponíveis.

- **CJK**: incluir codificações de Chinês, Japonês e Coreano, tais como *Japonês (EUC)* \[enc-jp, CP51932\], *Japonês (Shift-JIS)* \[iso-2022-jp, shift\_jis, CP932\], *Japonês (JIS)* \[CP50220\], *Chinês Simplificado (GB2312)* \[gb2312, CP936\], *Coreano (UHC)* \[ks\_c\_5601-1987, CP949\], *Coreano (EUC)* \[euc-kr, CP51949\], *Chinês Tradicional (Big5)* \[big5, CP950\] e *Chinês Simplificado (GB18030)* \[GB18030, CP54936\].

- **Oriente Médio**: incluir codificações do oriente médio, tais como *Turco (Windows)* \[iso-8859-9, CP1254\], *Hebraico (Windows)* \[windows-1255, CP1255\], *Árabe (Windows)* \[windows-1256, CP1256\], *Árabe (ISO)* \[iso-8859-6, CP28596\], *Hebraico (ISO)* \[iso-8859-8, CP28598\], *Latim 5 (ISO)* \[iso-8859-9, CP28599\] e *Hebraico (alternativa ao ISO)* \[iso-8859-8, CP38598\].

- **Outros**: incluir outras codificações como *Cirílico (Windows)* \[CP1251\], *Báltico (Windows)* \[iso-8859-4, CP1257\], *Vietnamita (Windows)* \[CP1258\], *Cirílico (KOI8-R)* \[koi8-r, CP1251\], *Ucraniano (KOI8-U)* \[koi8-u, CP1251\], *Báltico (ISO)* \[iso-8859-4, CP1257\], *Cirílico (ISO)* \[iso-8859-5, CP1251\], *ISCII Davenagari* \[x-iscii-de, CP57002\], *ISCII Bengalês* \[x-iscii-be, CP57003\], *ISCII Tâmil* \[x-iscii-ta, CP57004\], *ISCII Télugo* \[x-iscii-te, CP57005\], *ISCII Assamês* \[x-iscii-as, CP57006\], *ISCII Odia* \[x-iscii-or, CP57007\], *ISCII Canarim* \[x-iscii-ka, CP57008\], *ISCII Malaiala* \[x-iscii-ma, CP57009\], *ISCII Guzerate* \[x-iscii-gu, CP57010\], *ISCII Panjabi* \[x-iscii-pa, CP57011\] e *Tailandês (Windows)* \[CP874\].

- **Raro**: incluir codificações raras, tais como *IBM EBCDIC (Turco)* \[CP1026\], *IBM EBCDIC (Open Systems Latim 1)* \[CP1047\], *IBM EBCDIC (EUA-Canadá com Euro)* \[CP1140\], *IBM EBCDIC (Alemanha com Euro)* \[CP1141\], *IBM EBCDIC (Dinamarca/Noruega com Euro)* \[CP1142\], *IBM EBCDIC (Finlândia/Suécia com Euro)* \[CP1143\], *IBM EBCDIC (Itália com Euro)* \[CP1144\], *IBM EBCDIC (América Latina/Espanha com Euro)* \[CP1145\], *IBM EBCDIC (Reino Unido com Euro)* \[CP1146\], *IBM EBCDIC (França com Euro)* \[CP1147\], *IBM EBCDIC (Internacional com Euro)* \[CP1148\], *IBM EBCDIC (Islandês com Euro)* \[CP1149\], *IBM EBCDIC (Alemanha)* \[CP20273\], *IBM EBCDIC (Dinamarca/Noruega)* \[CP20277\], *IBM EBCDIC (Finlândia/Suécia)* \[CP20278\], *IBM EBCDIC (Itália)* \[CP20280\], *IBM EBCDIC (América Latina/Espanha)* \[CP20284\], *IBM EBCDIC (Reino Unido)* \[CP20285\], *IBM EBCDIC (Katakana Japonês Estendido)* \[CP20290\], *IBM EBCDIC (França)* \[CP20297\], *IBM EBCDIC (Árabe)* \[CP20420\], *IBM EBCDIC (Hebraico)* \[CP20424\], *IBM EBCDIC (Islandês)* \[CP20871\], *IBM EBCDIC (Cirílico – Servo, Búlgaro)* \[CP21025\], *IBM EBCDIC (EUA-Canadá)* \[CP37\], *IBM EBCDIC (Internacional)* \[CP500\], *Árabe (ASMO 708)* \[CP708\], *Centro-europeu (DOS)* \[CP852\]*, Cirílico (DOS)* \[CP855\], *Turco (DOS)* \[CP857\], *Europeu Ocidental (DOS com Euro)* \[CP858\], *Hebraico (DOS)* \[CP862\], *Árabe (DOS)* \[CP864\], *Russo (DOS)* \[CP866\], *Grego (DOS)* \[CP869\], *IBM EBCDIC (Latim 2)* \[CP870\] e *IBM EBCDIC (Grego)* \[CP875\].

- **Oeste**: incluir codificações ocidentais, tais como *Europeu Ocidental (Mac)* \[macintosh, CP10000\], *Islandês (Mac)* \[x-mac-islandês, CP10079\], *Centro-europeu (Windows)* \[iso 8859-2, CP1250\], *Europeu Ocidental (Windows)* \[iso 8859-1, CP1252\], *Grego (Windows)* \[iso-8859-7, CP1253\], *Centro-europeu (ISO)* \[iso 8859-2, CP28592\], *Latim 3 (ISO)* \[iso 8859-3, CP28593\], *Grego (ISO)* \[iso-8859-7, CP28597\], *Latim 9 (ISO) * \[iso 8859-15, CP28605\], *OEM Estados Unidos* \[CP437\], *Europeu Ocidental (DOS)* \[CP850\], *Português (DOS)* \[CP860\], *Islandês (DOS)* \[CP861\], *Francês Canadense (DOS)* \[CP863\], e *Nórdico (DOS)* \[CP865\].

```xml
<MandroidI18n>West</MandroidI18n>
```

## <a name="monoandroidresourceprefix"></a>MonoAndroidResourcePrefix

Especifica um *prefixo de caminho* que é removido do início de nomes de File, com uma ação de compilação de `AndroidResource` . Isso é para permitir a alteração do local em que os recursos estão localizados.

O valor padrão é `Resources`. Altere isso para `res` para a estrutura do projeto Java.

## <a name="monosymbolarchive"></a>MonoSymbolArchive

Uma propriedade booliana que controla se os `.mSYM` artefatos são criados para uso posterior com `mono-symbolicate` , para extrair &ldquo; &rdquo; informações de nome de arquivo real e de número de linha dos rastreamentos de pilha de liberação.

Isso é verdadeiro por padrão para &ldquo; aplicativos de versão &rdquo; que têm símbolos de depuração habilitados: [`$(EmbedAssembliesIntoApk)`](#embedassembliesintoapk) é verdadeiro, [`$(DebugSymbols)`](~/android/deploy-test/building-apps/build-properties.md#debugsymbols)
é verdadeiro e [`$(Optimize)`](/visualstudio/msbuild/common-msbuild-project-properties)
é verdadeiro.

Adicionado no Xamarin.Android 7.1.