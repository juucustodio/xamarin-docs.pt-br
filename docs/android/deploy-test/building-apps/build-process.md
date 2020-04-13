---
title: Processo de build
ms.prod: xamarin
ms.assetid: 3BE5EE1E-3FF6-4E95-7C9F-7B443EE3E94C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/06/2020
ms.openlocfilehash: bce2b6f29129894ed446100c87b5e92d3572ed2f
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78946268"
---
# <a name="build-process"></a>Processo de build

## <a name="overview"></a>Visão geral

O processo de compilação xamarin.Android é responsável por `AndroidAsset`colar `AndroidResource`tudo em conjunto: [ `Resource.designer.cs`gerar, ](~/android/internals/api-design.md)suportar as ações de [construção,](#Build_Actions)gerar [invólucros para Android](~/android/platform/java-integration/android-callable-wrappers.md)e gerar um `.apk` para execução em dispositivos Android.

## <a name="application-packages"></a>Pacotes de Aplicativos

Em termos gerais, há dois tipos de pacotes de aplicativos do Android (arquivos `.apk`) que o sistema de build do Xamarin.Android pode gerar:

- Builds de **versão**, que são totalmente independentes e não requerem pacotes adicionais para executar. Esses são os pacotes que seriam fornecidos para uma loja de aplicativos.

- Builds de **depuração**, que não o são.

Não coincidentemente, eles correspondem ao `Configuration` do MSBuild que produz o pacote.

### <a name="shared-runtime"></a>Runtime compartilhado

O *runtime compartilhado* é um par de pacotes Android adicionais que fornecem a biblioteca de classes base (`mscorlib.dll`, etc.) e a biblioteca de associações do Android (`Mono.Android.dll`, etc.). Builds de depuração contam com o runtime compartilhado em vez de incluir os assemblies da biblioteca de classes base e da biblioteca de associações dentro do pacote do aplicativo Android, permitindo que o pacote de depuração seja menor.

O runtime compartilhado pode ser desabilitado em builds de depuração, definindo a propriedade `$(AndroidUseSharedRuntime)` para `False`.

<a name="Fast_Deployment" />

### <a name="fast-deployment"></a>Fast Deployment

O *Fast Deployment* trabalha em conjunto com o runtime compartilhado para reduzir ainda mais o tamanho do pacote do aplicativo Android. Isso é feito não agrupando assemblies do aplicativo dentro do pacote. Em vez disso, eles são copiados para o destino por meio de `adb push`. Esse processo agiliza o ciclo de build/implantação/depuração porque se *somente* assemblies são alterados, o pacote não é reinstalado. Em vez disso, apenas os assemblies atualizados são sincronizados novamente ao dispositivo de destino.

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

- **BuildAndStartAotProfiling** &ndash; Constrói o aplicativo com um profiler AOT incorporado, `$(AndroidAotProfilerPort)`define a porta TCP do profiler e inicia a atividade padrão.

  A porta TCP `9999`padrão é .

  Adicionado em Xamarin.Android 10.2.

- **Clean** &ndash; remove todos os arquivos gerados pelo processo de build.

- **FinishAotPerfil** &ndash; coleta os dados do profiler AOT do dispositivo ou `$(AndroidAotProfilerPort)` emulador `$(AndroidAotCustomProfilePath)`através da porta TCP e os grava para .

  Os valores padrão para `9999` a `custom.aprof`porta e o perfil personalizado são e .

  Para passar opções adicionais `aprofutil` `$(AProfUtilExtraOptions)` para, configurá-las na propriedade.

  Isso é equivalente a:

  ```
  aprofutil $(AProfUtilExtraOptions) -s -v -f -p $(AndroidAotProfilerPort) -o "$(AndroidAotCustomProfilePath)"
  ```

  Adicionado em Xamarin.Android 10.2.

- **Instalar** &ndash; Instala o pacote no dispositivo padrão ou dispositivo virtual.

- **SignAndroidPackage** &ndash; cria e assina o pacote (`.apk`). Use com `/p:Configuration=Release` para gerar pacotes de "Versão" independentes.

- **StartAndroidActivity** &ndash; Inicia a atividade padrão no dispositivo ou no emulador em execução. Para iniciar uma atividade `$(AndroidLaunchActivity)` diferente, defina a propriedade para o nome da atividade.

  Isso é equivalente a `adb shell am start @PACKAGE_NAME@/$(AndroidLaunchActivity)`.

  Adicionado em Xamarin.Android 10.2.

- **StopAndroidPackage** &ndash; Interrompe completamente o pacote de aplicativos no dispositivo ou no emulador em execução.

  Isso é equivalente a `adb shell am force-stop @PACKAGE_NAME@`.

  Adicionado em Xamarin.Android 10.2.

- **Desinstalar** &ndash; Desinstale o pacote do dispositivo padrão ou do dispositivo virtual.

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

Uma palavra de cautela sobre a extensão do processo de compilação: Se não estiver escrita corretamente, as extensões de compilação podem afetar o desempenho da sua compilação, especialmente se elas forem executadas em cada compilação. É altamente recomendável que você leia a [documentação](https://docs.microsoft.com/visualstudio/msbuild/msbuild) do MSBuild antes de implementar essas extensões.

- **AfterGenerateAndroidManifest** &ndash; Os destinos listados nesta propriedade serão executados diretamente após o destino `_GenerateJavaStubs` interno. É aqui que o arquivo `AndroidManifest.xml` é gerado no `$(IntermediateOutputPath)`. Portanto, se quiser fazer alguma modificação no arquivo `AndroidManifest.xml` gerado, você poderá fazê-lo usando esse ponto de extensão.

  Adicionado no Xamarin.Android 9.4.

- **BeforeGenerateAndroidManifest** &ndash; Os destinos listados nesta propriedade serão executados diretamente antes de `_GenerateJavaStubs`.

  Adicionado no Xamarin.Android 9.4.

## <a name="build-properties"></a>Propriedades de build

Propriedades do MSBuild controlam o comportamento dos destinos. Elas são especificadas no arquivo de projeto, por exemplo, **MyApp.csproj**, dentro de um [elemento PropertyGroup do MSBuild](https://docs.microsoft.com/visualstudio/msbuild/propertygroup-element-msbuild).

- **Configuration** &ndash; especifica a configuração de build a ser usada, como "Debug" ou "Release". A propriedade Configuration é usada para determinar os valores padrão de outras propriedades que determinam o comportamento de destino. Configurações adicionais podem ser criadas dentro de seu IDE.

  *Por padrão*, a configuração `Debug` fará com que os destinos `Install` e `SignAndroidPackage` criem um pacote do Android menor, que exigirá a presença de outros arquivos e pacotes para funcionar.

  A configuração padrão `Release` fará com que os destinos `Install` e `SignAndroidPackage` criem um pacote Android *autônomo*, que pode ser usado sem a instalação de outros pacotes ou arquivos.

- **DebugSymbols** &ndash; um valor booliano que determina se o pacote Android é *depurável*, em combinação com a propriedade `$(DebugType)`. Um pacote depurável contém símbolos de depuração, define o atributo `//application/@android:debuggable` para `true` e adiciona automaticamente a permissão `INTERNET` para um depurador possa ser anexado ao processo. Um aplicativo é depurável se `DebugSymbols` é `True` *e* `DebugType` é a cadeia de caracteres vazia ou `Full`.

- **DebugType** &ndash; especifica o [tipo de símbolos de depuração](https://docs.microsoft.com/visualstudio/msbuild/csc-task) para gerar como parte do build, o que também afeta se o aplicativo é ou não depurável. Os valores possíveis incluem:

  - **Full**: todos os símbolos são gerados. Se a propriedade do MSBuild `DebugSymbols` também é `True`, o pacote do aplicativo é depurável.

  - **PdbOnly**: os símbolos "PDB" são gerados. O pacote do aplicativo *não* será depurável.

  Se `DebugType` não está definida ou é uma cadeia de caracteres vazia, a propriedade `DebugSymbols` controla se o Aplicativo é depurável.

  - **AndroidGenerateLayoutBindings** &ndash; Habilita a geração do [code-behind de layout](https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/LayoutCodeBehind.md) se definido como `true` ou desabilita completamente se definido como `false`. O valor padrão é `false`.

### <a name="install-properties"></a>Propriedades de instalação

Propriedades de instalação controlam o comportamento dos destinos `Install` e `Uninstall`.

- **AdbTarget** &ndash; especifica o dispositivo de destino do Android no qual o pacote Android pode ser instalado ou do qual ele pode ser removido. O valor desta propriedade é o mesmo que a [ `adb` opção Dispositivo de destino:](https://developer.android.com/tools/help/adb.html#issuingcommands)

  ```bash
  # Install package onto emulator via -e
  # Use `/Library/Frameworks/Mono.framework/Commands/msbuild` on OS X
  MSBuild /t:Install ProjectName.csproj /p:AdbTarget=-e
  ```

### <a name="packaging-properties"></a>Propriedades de empacotamento

Propriedades de empacotamento controlam a criação do pacote Android e são usadas pelos destinos `Install` e `SignAndroidPackage`.
As [Propriedades de Assinatura](#Signing_Properties) também são relevantes ao empacotar aplicativos de Versão.

- **AndroidAotProfiles** &ndash; Uma propriedade de string que permite ao desenvolvedor adicionar perfis AOT a partir da linha de comando. É uma lista de caminhos absolutos separados por ponto e vírgula ou vírgula.

  Adicionado em Xamarin.Android 10.1.

- **AndroidApkDigestAlgorithm** &ndash; Um valor de string que especifica `jarsigner -digestalg`o algoritmo de digestão para usar com .

  O valor padrão é `SHA-256`. No Xamarin.Android 10.0 e anteriormente, o valor padrão era `SHA1`.

  Adicionado no Xamarin.Android 9.4.

- **AndroidApkSignerAlémargumentos** &ndash; Uma propriedade de string que permite ao `apksigner` desenvolvedor fornecer argumentos adicionais à ferramenta.

  Adicionado no Xamarin.Android 8.2.

- **AndroidApkSigningAlgorithm** &ndash; Um valor de cadeia de caracteres que especifica o algoritmo de assinatura para usar com `jarsigner -sigalg`.

  O valor padrão é `SHA256withRSA`. No Xamarin.Android 10.0 e anteriormente, o valor padrão era `md5withRSA`.

  Adicionado no Xamarin.Android 8.2.

- **AndroidApplication** &ndash; um valor booliano que indica se o projeto é para um aplicativo Android (`True`) ou para um projeto de biblioteca Android (`False` ou ausente).

  Apenas um projeto com `<AndroidApplication>True</AndroidApplication>` pode estar presente em um pacote Android. (Infelizmente isso é ainda não verificado, que pode resultar em erros sutis e bizarros relacionados aos recursos do Android.)

- **AndroidApplicationJavaClass** &ndash; o nome de classe Java completo a ser usado no lugar de `android.app.Application` quando uma classe herda de [Android.App.Application](xref:Android.App.Application).

  Essa propriedade geralmente é definida por *outras* propriedades, como a `$(AndroidEnableMultiDex)` propriedade MSBuild.

  Adicionado no Xamarin.Android 6.1.

- **AndroidBinUtilsPath** &ndash; Um caminho para um diretório contendo os [binutils][binutils] android tais como `ld`, o linker nativo e `as`, o montador nativo. Essas ferramentas fazem parte do NdK do Android e também estão incluídas na instalação do Xamarin.Android.

  O valor padrão é `$(MonoAndroidBinDirectory)\ndk\`.

  Adicionado em Xamarin.Android 10.0.

  [binutils]: https://android.googlesource.com/toolchain/binutils/

- **AndroidBoundExceptionType** &ndash; Um valor de string que especifica como as exceções devem ser propagadas quando um tipo fornecido pelo Xamarin.Android implementa um tipo ou interface .NET em termos de tipos java, por `Android.Runtime.InputStreamInvoker` exemplo `System.IO.Stream`e , ou `Android.Runtime.JavaDictionary` e `System.Collections.IDictionary`.

  - `Java`: O tipo de exceção Java original é propagado como está.

    Isso significa que, `InputStreamInvoker` por exemplo, `System.IO.Stream` não implementa adequadamente a API porque `Java.IO.IOException` pode ser lançada em `Stream.Read()` vez de `System.IO.IOException`.

    Este é o comportamento de propagação de exceção em todas as versões do Xamarin.Android antes do 10.2.

    Este é o valor padrão em Xamarin.Android 10.2.

  - `System`: O tipo de exceção Java original é capturado e embrulhado em um tipo de exceção .NET apropriado.

    Isso significa que, `InputStreamInvoker` por exemplo, implementa corretamente `System.IO.Stream`e `Stream.Read()` *não* lançará `Java.IO.IOException` instâncias.  (Ele pode, `System.IO.IOException` em vez `Java.IO.IOException` disso, lançar um que tem um como o `Exception.InnerException` valor.)

    Este será o valor padrão no Xamarin.Android 11.0.

  Adicionado em Xamarin.Android 10.2.

- **AndroidBuildApplicationPackage** &ndash; um valor booliano que indica se o pacote (.apk) deve ou não ser criado e assinado. Definir esse valor como `True` é equivalente a usar o destino de build [SignAndroidPackage](#Build_Targets).

  O suporte para essa propriedade foi adicionado após o Xamarin.Android 7.1.

  Essa propriedade é `False` por padrão.

- **AndroidBundleConfigurationFile** &ndash; Especifica um nome de arquivo para `bundletool` usar como um arquivo de [configuração][bundle-config-format] para quando construir um Pacote de Aplicativos para Android. Este arquivo controla alguns aspectos de como os APKs são gerados a partir do pacote, como em quais dimensões o pacote é dividido para produzir APKs. Observe que o Xamarin.Android configura algumas dessas configurações automaticamente, incluindo a lista de extensões de arquivo para deixar descompactado.

  Esta propriedade só `$(AndroidPackageFormat)` é relevante `aab`se estiver definida como .

  Adicionado em Xamarin.Android 10.3.

  [bundle-config-format]: https://developer.android.com/studio/build/building-cmdline#bundleconfig

- **AndroidDexTool** &ndash; uma propriedade de estilo de enumeração com os valores válidos `dx` ou `d8`. Indica qual compilador [dex][dex] do Android é usado durante o processo de build do Xamarin.Android.
  Atualmente, o padrão é `dx`. Para obter mais informações, confira nossa documentação sobre o [D8 e R8][d8-r8].

  [dex]: https://source.android.com/devices/tech/dalvik/dalvik-bytecode
  [d8-r8]: https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/D8andR8.md

- **AndroidEnableDesugar** &ndash; uma propriedade booliana que determina se `desugar` está habilitado. O Android não é compatível com todos os recursos de Java 8 no momento, e a cadeia de ferramentas padrão implementa novos recursos de linguagem ao executar transformações de código de bytes, chamadas `desugar`, na saída do compilador `javac`. O padrão será `False` se `AndroidDexTool=dx` for usado e será `True` se `AndroidDexTool=d8` for usado.

- **AndroidEnableGooglePlayStoreChecks** &ndash; Uma propriedade bool que permite que os desenvolvedores desabilitem as seguintes verificações do Google Play Store: XA1004, XA1005 e XA1006. Isso é útil para os desenvolvedores que não pretendem trabalhar com a Google Play Store e não desejam executar essas verificações.

  Adicionado no Xamarin.Android 9.4.

- **AndroidEnableMultiDex** &ndash; uma propriedade booliana que determina se o suporte a Multi-Dex será usado no `.apk` final.

  O suporte para essa propriedade foi adicionado no Xamarin.Android 5.1.

  Essa propriedade é `False` por padrão.

- **AndroidEnablePreloadAssemblies** &ndash; Uma propriedade booleana que controla se todos os conjuntos gerenciados empacotados dentro do pacote de aplicativos são carregados durante a inicialização do processo ou não.

  Quando definidos como `True`, todos os assemblies agrupados dentro do pacote de aplicativos serão carregados durante a inicialização do processo, antes que qualquer aplicativo de código seja invocado.
  Esse processo é consistente com aquilo que o Xamarin.Android fazia em versões anteriores ao Xamarin.Android 9.2.

  Quando definido como `False`, os assemblies somente serão carregados conforme necessário.
  Isso faz com que os aplicativos iniciem mais rapidamente e também é mais consistente com a semântica da área de trabalho do .NET.  Para ver a economia de tempo, defina a Propriedade do Sistema `debug.mono.log` para incluir `timing` e procure a mensagem `Finished loading assemblies: preloaded` dentro de `adb logcat`.

  Aplicativos ou bibliotecas que usam *require* injeção de dependência `True` podem exigir que `AppDomain.CurrentDomain.GetAssemblies()` essa propriedade seja se, por sua vez, exigir que retornem todos os conjuntos dentro do pacote de aplicativos, mesmo que a montagem não tenha sido necessária de outra forma.

  Por padrão, o valor será definido como `True`.

  Adicionado no Xamarin.Android 9.2.

- **AndroidEnableProfiledAot** &ndash; Uma propriedade booleana que determina se os perfis AOT são ou não usados durante a compilação Ahead-of-Time.

  Os perfis são listados no grupo de itens `AndroidAotProfile`. Este ItemGroup contém perfis padrão. Ele pode ser substituído removendo os perfis existentes e adicionando seus próprios perfis AOT.

  O suporte para essa propriedade foi adicionado no Xamarin.Android 9.4.

  Essa propriedade é `False` por padrão.

- **AndroidEnableSGenConcurrent** &ndash; Uma propriedade booleana que determina se o [coletor GC simultâneo](https://www.mono-project.com/docs/about-mono/releases/4.8.0/#concurrent-sgen) da Mono será ou não usado.

  O suporte para essa propriedade foi adicionado no Xamarin.Android 7.2.

  Essa propriedade é `False` por padrão.

- **AndroidErrorOnCustomJavaObject** &ndash; Uma propriedade booleana que `Android.Runtime.IJavaObject` 
  determina se os tipos podem implementar *sem* também herdar `Java.Lang.Object` ou `Java.Lang.Throwable`:

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

- **AndroidExtraAotOptions** &ndash; Uma propriedade de string que permite passar opções adicionais para o compilador Mono durante a `Aot` tarefa para projetos que têm ou `$(AndroidEnableProfiledAot)` `$(AotAssemblies)` definidos para `true`. O valor da seqüência da propriedade é adicionado ao arquivo de resposta ao chamar o compilador cruzado Mono.

  Em geral, esta propriedade deve ser deixada em branco, mas em certos cenários especiais pode fornecer flexibilidade útil.

  Observe que esta propriedade é `$(AndroidAotAdditionalArguments)` diferente da propriedade relacionada. Essa propriedade coloca argumentos separados por `--aot` comma na opção do compilador Mono. `$(AndroidExtraAotOptions)`em vez disso, passa opções `--verbose` `--debug` completas de espaço autônomo, como ou para o compilador.

  Adicionado em Xamarin.Android 10.2.

- **AndroidFastDeploymentType** &ndash; Uma lista de valores separados por `:` (dois pontos) para controlar quais tipos podem ser implantados no [diretório da Implantação Rápida](#Fast_Deployment) no dispositivo de destino quando a propriedade `$(EmbedAssembliesIntoApk)` do MSBuild é `False`. Se um recurso é implantado por Fast Deployment, ele *não* é inserido no `.apk` gerado, o que pode acelerar os tempos de implantação. (Quanto mais é implantado rapidamente, menos `.apk` freqüentemente as necessidades a serem reconstruídas e o processo de instalação podem ser mais rápidos.) Os valores válidos incluem:

  - `Assemblies`: implantar os assemblies do aplicativo.

  - `Dexes`: implantar arquivos `.dex`, recursos do Android e ativos do Android. **Esse valor pode ser usado *somente* em dispositivos que executam o Android 4.4 ou posterior (API-19).**

  O valor padrão é `Assemblies`.

  **Experimental.** Adicionado no Xamarin.Android 6.1.

- **AndroidGenerateJniMarshalMethods** &ndash; uma propriedade booliana que permite a geração dos métodos de marshal de JNI como parte do processo de build. Ela reduz consideravelmente o uso de System.Reflection no código do auxiliar de associação.

  Por padrão, ela será definida como False. Se os desenvolvedores desejarem usar o novo recurso de métodos de marshal JNI, eles poderão definir

  ```xml
  <AndroidGenerateJniMarshalMethods>True</AndroidGenerateJniMarshalMethods>
  ```

  no .csproj deles. Como alternativa, forneça a propriedade na linha de comando por meio de

  ```
  /p:AndroidGenerateJniMarshalMethods=True
  ```

  **Experimental.** Adicionado no Xamarin.Android 9.2.
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

- **AndroidHttpClientHandlerType** &ndash; Controla a implementação padrão `System.Net.Http.HttpMessageHandler` que será usada pelo construtor padrão `System.Net.Http.HttpClient`. O valor é um nome de `HttpMessageHandler` tipo qualificado para [`System.Type.GetType(string)`](https://docs.microsoft.com/dotnet/api/system.type.gettype?view=netcore-2.0#System_Type_GetType_System_String_)montagem de uma subclasse, adequado para uso com .
  Os valores mais comuns para essa propriedade são:

  - `Xamarin.Android.Net.AndroidClientHandler`: Use as APIs Android Java para realizar solicitações de rede. Isso permite acessar as URLs TLS 1.2 quando a versão do Android subjacente dá suporte ao TLS 1.2. Somente versões Android 5.0 e posteriores fornecem suporte confiável ao TLS 1.2 por meio de Java.

    Isso corresponde **à** opção Android nas páginas de propriedade do Visual Studio e à opção **AndroidClientHandler** no Visual Studio para páginas de propriedade Mac.

    O novo assistente de projeto seleciona esta opção para novos projetos quando a **versão mínima para Android** estiver configurada para o Android **5.0 (Lollipop)** ou superior no Visual Studio ou quando **a Target Platforms** estiver definida como **Mais Recente e Maior** em Visual Studio para Mac.

  - Unset/the empty string: Isso é equivalente a`System.Net.Http.HttpClientHandler, System.Net.Http`

    Isso corresponde à opção **Padrão** nas páginas de propriedade do Visual Studio.

    O novo assistente de projeto seleciona essa opção para novos projetos quando a **Versão mínima do Android** está configurada como **Android 4.4.87** ou versão mais antiga no Visual Studio ou quando o campo **Plataformas de destino** está definido como **Desenvolvimento Moderno** ou **Máxima compatibilidade** no Visual Studio para Mac.

  - `System.Net.Http.HttpClientHandler, System.Net.Http`: Use o `HttpMessageHandler`gerenciado .

    Isso corresponde à opção **Gerenciado** nas páginas de propriedade do Visual Studio.

  > [!NOTE]
  > Se o suporte ao TLS 1.2 for necessário *or* em versões android antes do `System.Net.WebClient` 5.0, ou `$(AndroidTlsProvider)` se o suporte ao TLS 1.2 for necessário com as APIs relacionadas, então deve ser usado.

  > [!NOTE]
  > O suporte para esta [ `XA_HTTP_CLIENT_HANDLER_TYPE` ](~/android/deploy-test/environment.md)propriedade funciona definindo a variável ambiente .
  > Um valor `$XA_HTTP_CLIENT_HANDLER_TYPE` encontrado em um arquivo com uma ação de build de `@(AndroidEnvironment)` terá precedência.

  Adicionado no Xamarin.Android 6.1.

- **AndroidLinkMode** &ndash; especifica qual tipo de [vinculação](~/android/deploy-test/linker.md) deve ser realizada em assemblies contidos no pacote Android. Usado somente em projetos de aplicativo Android. O valor padrão é *SdkOnly*. Os valores válidos são:

  - **None**: nenhuma tentativa de vinculação ocorrerá.

  - **SdkOnly**: a vinculação ocorrerá apenas nas bibliotecas de classe base, não nos assemblies do usuário.

  - **Full**: a vinculação ocorrerá nas bibliotecas de classe base e nos assemblies do usuário.

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

- **AndroidLintEnabled** &ndash; Uma propriedade bool que permite `lint` ao desenvolvedor executar a ferramenta android como parte do processo de embalagem.

  - **AndroidLintEnabledIssues** &ndash; Uma lista, separada por vírgulas, de problemas de lint a serem habilitados.

  - **AndroidLintDisabledIssues** &ndash; uma lista, separada por vírgulas, de problemas de lint a serem desabilitados.

  - **AndroidLintCheckIssues** &ndash; Uma lista, separada por vírgulas, de problemas de lint a serem verificados.
    Observação: somente esses problemas serão verificados.

  - **AndroidLintConfig** &ndash; essa é uma ação de build para um arquivo de configuração de estilo de lint. Ela pode ser usada para habilitar/desabilitar problemas a serem verificados. Vários arquivos podem usar essa ação de build pois seu conteúdo será mesclado.

  Consulte [A Ajuda de Linha](https://developer.android.com/studio/write/lint) `lint` para obter mais detalhes sobre a ferramenta android.

- **AndroidManagedSymbols** &ndash; uma propriedade booliana que controla se pontos de sequência são gerados para que o nome do arquivo e as informações de número de linha possam ser extraídos dos rastreamentos de pilha `Release`.

  Adicionado no Xamarin.Android 6.1.

- **AndroidManifest** &ndash; especifica um nome de arquivo a ser usado como modelo para o [`AndroidManifest.xml`](~/android/platform/android-manifest.md) do aplicativo.
  Durante o build, quaisquer outros valores necessários serão mesclados para produzir o `AndroidManifest.xml` propriamente dito.
  O `$(AndroidManifest)` deve conter o nome do pacote no atributo `/manifest/@package`.

- **AndroidManifestMerger** &ndash; especifica a implementação para a fusão de arquivos *AndroidManifest.xml.* Esta é uma propriedade no `legacy` estilo enum onde `manifestmerger.jar` seleciona a implementação C# original e seleciona a implementação Java do Google.

  O valor padrão `legacy`é atualmente . Isso mudará `manifestmerger.jar` para em uma versão futura para alinhar o comportamento com o Android Studio.

  A fusão do `xmlns:tools="http://schemas.android.com/tools"` Google permite o suporte para como descrito na [documentação][manifest-merger]do Android .

  Apresentado em Xamarin.Android 10.2

  [manifest-merger]: https://developer.android.com/studio/build/manifest-merge

- **AndroidMultiDexClassListExtraArgs** &ndash; Uma propriedade de string que permite que `com.android.multidex.MainDexListBuilder` os desenvolvedores passem argumentos adicionais para o momento da geração do `multidex.keep` arquivo.

  Um caso específico é se você estiver recebendo o erro a seguir durante a compilação de `dx`.

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

  Quando `$(AndroidPackageFormat)` é definido como `aab`, outras propriedades do MSBuild são definidas, que são necessárias para os Lote de Aplicativo do Android:

  - `$(AndroidUseAapt2)` é `True`.
  - `$(AndroidUseApkSigner)` é `False`.
  - `$(AndroidCreatePackagePerAbi)` é `False`.

  [apk]: https://en.wikipedia.org/wiki/Android_application_package
  [bundle]: https://developer.android.com/platform/technology/app-bundle

- **AndroidPackageNamingPolicy** &ndash; Uma propriedade no estilo enum para especificar os nomes do pacote Java do código-fonte Java gerado.

  No Xamarin.Android 10.2 e posterior, o `LowercaseCrc64`único valor suportado é .

  No Xamarin.Android 10.1, `LowercaseMD5` também estava disponível um valor transitório que permitia mudar de volta para o estilo original do nome do pacote Java, como usado no Xamarin.Android 10.0 e anteriormente. Essa opção foi removida no Xamarin.Android 10.2 para melhorar a compatibilidade com ambientes de construção que têm a conformidade FIPS aplicada.

  Adicionado em Xamarin.Android 10.1.

- **AndroidR8JarPath** &ndash; o caminho para `r8.jar` a ser usado com o compilador dex r8 e o redutor. O padrão é um caminho na instalação do Xamarin.Android. Para obter mais informações, confira nossa documentação sobre o [D8 e R8][d8-r8].

- **AndroidSdkBuildToolsVersion** &ndash; o pacote de ferramentas de build do Android SDK fornece as ferramentas **aapt** e **zipalign**, entre outras. Várias versões diferentes do pacote de ferramentas de build podem ser instaladas simultaneamente. O pacote de ferramentas de build escolhido para empacotamento é criado procurando e usando uma versão de ferramentas de build "preferencial", se uma está presente; se a versão "preferencial" *não* está presente, o pacote de ferramentas de build com a versão mais alta entre os instalados é usado.

  A propriedade do MSBuild `$(AndroidSdkBuildToolsVersion)` contém a versão das ferramentas de build preferencial. O sistema de build do Xamarin.Android fornecerá um valor padrão em `Xamarin.Android.Common.targets`, que poderá ser substituído no arquivo de projeto para escolher uma versão de ferramentas de build alternativa se, por exemplo, o aapt mais recente estiver falhando enquanto uma versão anterior do aapt sabidamente funciona.

- **AndroidSupportedAbis** &ndash; uma propriedade de cadeia de caracteres que contém uma lista delimitada por ponto e vírgula (`;`) de ABIs que devem ser incluídos no `.apk`.

  Os valores compatíveis incluem:

  - `armeabi-v7a`
  - `x86`
  - `arm64-v8a`: requer o Xamarin.Android 5.1 e posterior.
  - `x86_64`: requer o Xamarin.Android 5.1 e posterior.

- **AndroidTlsProvider** &ndash; um valor de cadeia de caracteres que especifica qual provedor TLS deve ser usado em um aplicativo. Os valores possíveis são:

  - Unset/the empty string: In Xamarin.Android 7.3 and `btls`superior, isto é equivalente a .

    no Xamarin.Android 7.1, isso é equivalente a `legacy`.

    Isso corresponde à **configuração Padrão** nas páginas de propriedade do Visual Studio.

  - `btls`: usar [Boring SSL](https://boringssl.googlesource.com/boringssl) para a comunicação por TLS com [HttpWebRequest](xref:System.Net.HttpWebRequest).

    Isso permite usar o TLS 1.2 em todas as versões do Android.

    Isso corresponde à configuração **Nativa TLS 1.2+** nas páginas de propriedade do Visual Studio.

  - `legacy`: No Xamarin.Android 10.1 e anterior, use a implementação histórica gerenciada do SSL para interação de rede. Isso *não* é compatível com a TLS 1.2.

    Isso corresponde à configuração **TLS 1.0 gerenciada** nas páginas de propriedade do Visual Studio.

    No Xamarin.Android 10.2 e posterior, esse `btls` valor é ignorado e a configuração é usada.

  - `default`: É improvável que este valor seja usado em projetos Xamarin.Android. Em vez disso, o valor recomendado a ser usado é a cadeia de caracteres vazia, que corresponde à configuração **padrão** nas páginas de propriedades do Visual Studio.

    O valor `default` não é oferecido nas páginas de propriedades do Visual Studio.

    Atualmente, isso é equivalente a `legacy`.

  Adicionado no Xamarin.Android 7.1.

- **AndroidUseApkSigner** &ndash; Uma propriedade booliana que permite ao desenvolvedor usar a ferramenta `apksigner` em vez da `jarsigner`.

    Adicionado no Xamarin.Android 8.2.

- **AndroidUseDefaultAotProfile** &ndash; Uma propriedade bool que permite ao desenvolvedor suprimir o uso dos perfis AOT padrão.

  Para suprimir os perfis AOT padrão, `false`defina a propriedade como .

  Adicionado em Xamarin.Android 10.1.

- **AndroidUseLegacyVersionCode** &ndash; Uma propriedade booliana que permitirá ao desenvolvedor reverter o cálculo versionCode para o comportamento anterior do Xamarin.Android 8.2. Isso deve ser usado SOMENTE por desenvolvedores com aplicativos existentes na Google Play Store. É altamente recomendável que a nova propriedade `$(AndroidVersionCodePattern)` seja usada.

  Adicionado no Xamarin.Android 8.2.

- **AndroidUseManagedDesignTimeResourceGenerator** &ndash; Uma propriedade booleana que mudará o tempo de projeto é `aapt`criada para usar o analisador de recursos gerenciado em vez de .

  Adicionado no Xamarin.Android 8.1.

- **AndroidUseSharedRuntime** &ndash; uma propriedade booliana que determina se os *pacotes de tempo de execução compartilhado* são necessários para executar o aplicativo no dispositivo de destino. Depender dos pacotes de runtime compartilhado permite que o pacote do aplicativo seja menor, acelerando o processo de criação e implantação de pacote e resultando em um ciclo de build/implantação/depuração mais rápido.

  Essa propriedade deve ser `True` para builds de depuração e `False` para projetos de versão.

- **AndroidVersionCodePattern** &ndash; uma propriedade de cadeia de caracteres que permite ao desenvolvedor personalizar o `versionCode` no manifesto.
  Confira [Criar um código de versão para o APK](~/android/deploy-test/building-apps/abi-specific-apks.md) para obter informações sobre como decidir um `versionCode`.

  Alguns exemplos: se `abi` for `armeabi` e `versionCode` no manifesto for `123`, `{abi}{versionCode}` produzirá um versionCode de `1123` quando `$(AndroidCreatePackagePerAbi)` for True; caso contrário, gerará um valor de 123.
  Se `abi` é `x86_64` e `versionCode` no manifesto é `44`. Isso produzirá `544` quando `$(AndroidCreatePackagePerAbi)` for True; caso contrário, gerará um valor de `44`.

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

  - **versionCode** &ndash; Usa o código `Properties\AndroidManifest.xml`da versão diretamente de .

  Você pode definir itens personalizados usando a propriedade `$(AndroidVersionCodeProperties)` (definida a seguir).

  Por padrão o valor será definido como `{abi}{versionCode:D6}`. Se um desenvolvedor quiser manter o comportamento anterior, você poderá configurar a propriedade `$(AndroidUseLegacyVersionCode)` como `true` para substituir o padrão

  Adicionado no Xamarin.Android 7.2.

- **AndroidVersionCodeProperties** &ndash; Uma propriedade de cadeia de caracteres que permite ao desenvolvedor definir itens personalizados para usar com o `AndroidVersionCodePattern`. Eles estão na forma de um par `key=value`. Todos os itens no `value` devem ser valores inteiros. Por exemplo: `screen=23;target=$(_AndroidApiLevel)`. Como você pode ver, você pode fazer uso de propriedades do MSBuild existentes ou personalizadas na cadeia de caracteres.

  Adicionado no Xamarin.Android 7.2.

- **AotAssemblies** &ndash; Uma propriedade booleana que determina se as assembléias serão ou não antecipadas `.apk`compiladas em código nativo e incluídas no .

  O suporte para essa propriedade foi adicionado no Xamarin.Android 5.1.

  Essa propriedade é `False` por padrão.

- **EmbedAssembliesIntoApk** &ndash; Uma propriedade booleana que determina se os conjuntos do aplicativo devem ou não ser incorporados no pacote do Aplicativo.

  Essa propriedade deve ser `True` para builds de versão e `False` para builds de depuração. Ele *pode* precisar ser `True` em builds de depuração se o Fast Deployment não é compatível com o dispositivo de destino.

  Quando essa propriedade é `False`, a propriedade `$(AndroidFastDeploymentType)` do MSBuild também controla o que será inserido no `.apk`, o que pode afetar os tempos de implantação e de recompilação.

- **HabilitarLLVM** &ndash; Uma propriedade booleana que determina se o LLVM será ou não usado quando o Adiantado compilou os conjuntos em código nativo.

  O NDK do Android deve ser instalado para construir um projeto que tenha essa propriedade ativada.

  O suporte para essa propriedade foi adicionado no Xamarin.Android 5.1.

  Essa propriedade é `False` por padrão.

  Essa propriedade é ignorada, a menos que a propriedade `$(AotAssemblies)` do MSBuild seja `True`.

- **EnableProguard** &ndash; Uma propriedade booleana que determina se o [proguard](https://developer.android.com/tools/help/proguard.html) é ou não executado como parte do processo de embalagem para vincular o código Java.

  O suporte para essa propriedade foi adicionado no Xamarin.Android 5.1.

  Essa propriedade é `False` por padrão.

  Quando `True`, [ProguardConfiguration](#ProguardConfiguration) arquivos serão `proguard` usados para controlar a execução.

- **JavaMaximumHeapSize** &ndash; Especifica o valor **java** 
   `-Xmx` do valor do parâmetro `.dex` java a ser usado ao construir o arquivo como parte do processo de embalagem. Se não for especificado, a opção `-Xmx` fornecerá **java** com um valor de `1G`. Foi verificado que isso normalmente é necessário no Windows em comparação com outras plataformas.

  Especificar esta propriedade é [ `_CompileDex` `java.lang.OutOfMemoryError` ](https://bugzilla.xamarin.com/show_bug.cgi?id=18327)necessário se o alvo lançar um .

  Personalize o valor alterando:

  ```xml
  <JavaMaximumHeapSize>1G</JavaMaximumHeapSize>
  ```

- **JavaOptions** &ndash; especifica as opções de linha de comando adicionais a serem passadas para **java** ao compilar o arquivo `.dex`.

- **LinkerDumpDependencies** &ndash; uma propriedade booliana que permite a geração do arquivo de dependências do vinculador. Esse arquivo pode ser usado como entrada para a ferramenta [illinkanalyzer](https://github.com/mono/linker/blob/master/src/analyzer/README.md).

  O valor padrão é False.

- **MandroidI18n** &ndash; especifica o suporte à internacionalização incluído com o aplicativo, tal como tabelas de ordenação e de classificação. O valor é uma lista separada por vírgula ou ponto e vírgula de um ou mais dos seguintes valores que não diferenciam maiúsculas e minúsculas:

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

- **MonoSymbolArchive** &ndash; Uma propriedade booliana que controla se artefatos `.mSYM` são criados para uso posterior com `mono-symbolicate`, para extrair informações &ldquo;reais&rdquo; de nome do arquivo e de número de linha dos rastreamentos de pilha de versão.

  Isso é True por padrão para aplicativos de &ldquo;versão&rdquo; que têm os símbolos de depuração habilitados:`$(EmbedAssembliesIntoApk)` é True, `$(DebugSymbols)` é True e `$(Optimize)` é True.

  Adicionado no Xamarin.Android 7.1.

### <a name="binding-project-build-properties"></a>Propriedades de build do projeto de associação

As seguintes propriedades de MSBuild são usadas com [projetos de associação](~/android/platform/binding-java-library/index.md):

- **AndroidClassParser** &ndash; uma propriedade de cadeia de caracteres que controla como os arquivos `.jar` são analisados. Os valores possíveis incluem:

  - **class-parse**: usa `class-parse.exe` para analisar o código de bytes Java diretamente, sem a assistência de uma JVM. Esse valor é experimental.

  - **jar2xml**: usar `jar2xml.jar` para usar a reflexão do Java para extrair tipos e membros de um arquivo `.jar`.

  As vantagens de `class-parse` sobre `jar2xml` são:

  - `class-parse`é capaz de extrair nomes de parâmetros do código de bytes Java que `javac -g`contém símbolos *de depuração* (por exemplo, bytecode compilado com ).

  - `class-parse` não "ignora" classes que herdam, ou contêm, membros de tipos não resolvidos.

  **Experimental.** Adicionado no Xamarin.Android 6.0.

  O valor padrão é `jar2xml`.

  O valor padrão será alterado em uma versão futura.

- **AndroidCodegenTarget** &ndash; uma propriedade de cadeia de caracteres que controla a ABI de destino de geração de código. Os valores possíveis incluem:

  - **XamarinAndroid**: Usa a API de ligação JNI presente desde Mono para Android 1.0. Assemblies de associação criados com Xamarin.Android 5.0 ou posterior podem ser executados apenas no Xamarin.Android 5.0 ou posterior (adições de API/ABI), mas o *código-fonte* é compatível com o das versões anteriores do produto.

  - **XAJavaInterop1**: usar Java.Interop para invocações de JNI. Assemblies de associação usando `XAJavaInterop1` só podem compilar e executar com o Xamarin.Android 6.1 ou posterior. O Xamarin.Android 6.1 e os posteriores associam `Mono.Android.dll` com esse valor.

    Os benefícios de `XAJavaInterop1` incluem:

    - Assemblies menores.

    - Cache em `jmethodID` para invocações de método `base`, desde que todos os outros tipos de associação na hierarquia de herança sejam compilados com o `XAJavaInterop1` ou posterior.

    - Cache em `jmethodID` de construtores do Java Callable Wrapper para subclasses gerenciadas.

    O valor padrão é `XAJavaInterop1`.

### <a name="resource-properties"></a>Propriedades de recurso

Propriedades do recurso controlam a geração do arquivo `Resource.designer.cs`, que fornece acesso a recursos do Android.

- **AndroidAapt2CompileExtraArgs** &ndash; especifica opções de linha de comando adicionais a serem passadas para o comando **aapt2 compile** durante o processamento de recursos e ativos do Android.

  Adicionado no Xamarin.Android 9.1.

- **AndroidAapt2LinkExtraArgs** &ndash; especifica opções de linha de comando adicionais a serem passadas ao comando **aapt2 link** durante o processamento de ativos e recursos do Android.

  Adicionado no Xamarin.Android 9.1.

- **AndroidExplicitCrunch** &ndash; se você estiver criando um aplicativo com um número muito grande de desenháveis locais, um build (ou rebuild) inicial pode levar minutos para ser concluído. Para acelerar o processo de build, tente incluir essa propriedade e configurá-la como `True`. Quando essa propriedade é definida, o processo de build pré-compacta os arquivos PNG.

  Observação: essa opção não é compatível com a opção `$(AndroidUseAapt2)`. Se a `$(AndroidUseAapt2)` estiver habilitada, essa funcionalidade estará desabilitada. Se você quiser continuar usando esse recurso, defina `$(AndroidUseAapt2)` como `False`.

  **Experimental.** Adicionado no Xamarin.Android 7.0.

- **O AndroidResgenExtraArgs** &ndash; especifica opções adicionais de linha de comando para passar para o comando **aapt** ao processar ativos e recursos android.

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

- **AndroidDebugStoreType** &ndash; Especifica o formato de arquivo `debug.keystore`da loja de chaves a ser usado para . Usa como padrão `pkcs12`.

  Adicionado em Xamarin.Android 10.2.

- **AndroidKeyStore** &ndash; um valor booliano que indica se as informações de autenticação personalizadas devem ser usadas. O valor padrão é `False`, que significa que a chave de assinatura de depuração padrão será usada para assinar pacotes.

- **AndroidSigningKeyAlias** &ndash; especifica o alias para a chave no repositório de chaves. Este é o valor de **keytool-alias** usado ao criar o repositório de chaves.

- **AndroidSigningKeyPass** &ndash; especifica a senha da chave de dentro do arquivo do repositório de chaves. Esse é o valor digitado quando `keytool` solicita **Insira a senha da chave para $(AndroidSigningKeyAlias)**.

  No Xamarin.Android 10.0 e anteriormente, essa propriedade só suporta senhas de texto simples.

  No Xamarin.Android 10.1 e posterior, `env:` esta `file:` propriedade também suporta e prefixos que podem ser usados para especificar uma variável de ambiente ou arquivo que contenha a senha. Essas opções fornecem uma maneira de impedir que a senha apareça em registros de compilação.

  Por exemplo, para usar uma variável de ambiente chamada *AndroidSigningPassword*:

  ```xml
  <PropertyGroup>
      <AndroidSigningKeyPass>env:AndroidSigningPassword</AndroidSigningKeyPass>
  </PropertyGroup>
  ```

  Para usar um arquivo `C:\Users\user1\AndroidSigningPassword.txt`localizado em:

  ```xml
  <PropertyGroup>
      <AndroidSigningKeyPass>file:C:\Users\user1\AndroidSigningPassword.txt</AndroidSigningKeyPass>
  </PropertyGroup>
  ```

  > [!NOTE]
  > O `env:` prefixo não `$(AndroidPackageFormat)` é suportado quando é definido como `aab`.

- **AndroidSigningKeyStore** &ndash; especifica o nome do arquivo do repositório de chaves criado por `keytool`. Isso corresponde ao valor fornecido à opção **keytool -keystore**.

- **AndroidSigningStorePass** &ndash; especifica a senha para `$(AndroidSigningKeyStore)`. Esse é o valor fornecido a `keytool` ao criar o arquivo de repositório de chaves e ao solicitar **Insira a senha do repositório de chaves:**.

  No Xamarin.Android 10.0 e anteriormente, essa propriedade só suporta senhas de texto simples.

  No Xamarin.Android 10.1 e posterior, `env:` esta `file:` propriedade também suporta e prefixos que podem ser usados para especificar uma variável de ambiente ou arquivo que contenha a senha. Essas opções fornecem uma maneira de impedir que a senha apareça em registros de compilação.

  Por exemplo, para usar uma variável de ambiente chamada *AndroidSigningPassword*:

  ```xml
  <PropertyGroup>
      <AndroidSigningStorePass>env:AndroidSigningPassword</AndroidSigningStorePass>
  </PropertyGroup>
  ```

  Para usar um arquivo `C:\Users\user1\AndroidSigningPassword.txt`localizado em:

  ```xml
  <PropertyGroup>
      <AndroidSigningStorePass>file:C:\Users\user1\AndroidSigningPassword.txt</AndroidSigningStorePass>
  </PropertyGroup>
  ```

  > [!NOTE]
  > O `env:` prefixo não `$(AndroidPackageFormat)` é suportado quando é definido como `aab`.

- **JarsignerTimestampAuthorityCertificateAlias** &ndash; Esta propriedade permite que você especifique um alias no armazenamento de chaves para uma autoridade de carimbo de tempo.
  Consulte a documentação de suporte ao [carimbo de tempo da assinatura](https://docs.oracle.com/javase/8/docs/technotes/guides/security/time-of-signing.html) java para obter mais detalhes.

  ```xml
  <PropertyGroup>
      <JarsignerTimestampAuthorityCertificateAlias>Alias</JarsignerTimestampAuthorityCertificateAlias>
  </PropertyGroup>
  ```

- **JarsignerTimestampAuthorityUrl** &ndash; Esta propriedade permite especificar uma URL para um serviço de autoridade de carimbo de tempo. Isso pode ser usado `.apk` para garantir que sua assinatura inclua um carimbo de data e hora.
  Consulte a documentação de suporte ao [carimbo de tempo da assinatura](https://docs.oracle.com/javase/8/docs/technotes/guides/security/time-of-signing.html) java para obter mais detalhes.

  ```xml
  <PropertyGroup>
      <JarsignerTimestampAuthorityUrl>http://example.tsa.url</JarsignerTimestampAuthorityUrl>
  </PropertyGroup>
  ```

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

### <a name="androidresourceanalysisconfig"></a>AndroidResourceAnalysisConfig

A ação `AndroidResourceAnalysisConfig` Build marca um arquivo como um arquivo de configuração de nível de gravidade para a ferramenta de diagnóstico de layout Xamarin Android Designer. Atualmente, isso é usado apenas no editor de layout e não para mensagens de construção.

Consulte a [documentação de Análise de Recursos do Android](https://aka.ms/androidresourceanalysis) para obter mais detalhes.

Adicionado em Xamarin.Android 10.2.

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
