---
title: ProGuard
description: O Xamarin.Android ProGuard é um redutor, otimizador e pré-verificador de arquivo de classe Java. Ele detecta e remove o código não utilizado, analisa e otimiza o código de bytes. Este guia explica como funciona o ProGuard, como habilitá-lo em seu projeto e como configurá-lo. Ele também fornece vários exemplos de configurações do ProGuard.
ms.prod: xamarin
ms.assetid: 29C0E850-3A49-4618-9078-D59BE0284D5A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 28f48766dc62ac31fc357410eac27c33c9cae6d8
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "76940688"
---
# <a name="proguard"></a>ProGuard

_Xamarin.Android ProGuard é um psiquiatra de arquivos da classe Java, otimizador e pré-verificador. Detecta e remove códigonão utilizado, analisa e otimiza bytecode. Este guia explica como o ProGuard funciona, como habilitá-lo em seu projeto e como configurá-lo. Ele também fornece vários exemplos de configurações do ProGuard._

## <a name="overview"></a>Visão geral

O ProGuard detecta e remove classes não utilizadas, campos, métodos e atributos de seu aplicativo empacotado. Ele pode fazer o mesmo para bibliotecas referenciadas (isso pode ajudar a evitar o limite de referência de 64k). A ferramenta ProGuard do SDK do Android também otimiza código de bytes e remove instruções de código não utilizadas. O ProGuard lê **jars de entrada** e, em seguida, reduz, otimiza e os verifica previamente. Ele grava os resultados em um ou mais **jars de saída**. 

O ProGuard processa o APK de entrada usando as seguintes etapas: 

1. **Etapa de redução** &ndash; O ProGuard determina recursivamente quais classes e membros de classe são usados. Todas as outras classes e membros de classe são descartados. 

2. **Etapa de otimização** &ndash; O ProGuard otimiza ainda mais o código. 
    Entre outras otimizações, classes e métodos que não são pontos de entrada podem ser transformados em final, particular ou estático, e os parâmetros não usados podem ser removidos e alguns métodos podem ser embutidos. 

3. **Etapa de ofuscação** &ndash; No desenvolvimento nativo do Android, o ProGuard renomeia classes e membros de classe que não são pontos de entrada. Manter os pontos de entrada garante que eles ainda possam ser acessados por seus nomes originais. No entanto, não há suporte para esta etapa no Xamarin.Android porque o aplicativo é compilado para o IL (Linguagem Intermediária).

4. **Etapa de pré-verificação** &ndash; Realiza verificações nos códigos de byte Java antes do runtime e anota os arquivos de classe para o benefício da VM Java. Esta é a única etapa em que não é preciso saber os pontos de entrada. 

Cada uma dessas etapas é *opcional*. Como será explicado na próxima seção, o ProGuard no Xamarin.Android usa apenas um subconjunto dessas etapas. 

## <a name="proguard-in-xamarinandroid"></a>ProGuard no Xamarin.Android

A configuração do ProGuard no Xamarin.Android não ofusca o APK. Na verdade, não é possível habilitar a ofuscação pelo ProGuard (mesmo com o uso de arquivos de configuração personalizada). Portanto, ProGuard no Xamarin.Android executa apenas as etapas de **redução** e **otimização**: 

[![Etapas de encolhimento e otimização](proguard-images/01-xa-chain-sml.png)](proguard-images/01-xa-chain.png#lightbox)

É importante saber esse item antes de usar o ProGuard como ele funciona dentro do `Xamarin.Android` processo de build. Esse processo usa duas etapas separadas: 

1. Vinculador do Xamarin Android

2. ProGuard

Cada uma dessas etapas é descrita abaixo.

### <a name="linker-step"></a>Etapa do vinculador

O vinculador do Xamarin.Android emprega análise estática do seu aplicativo para determinar o seguinte: 

- Quais assemblies são realmente usados.

- Quais tipos são realmente usados.

- Quais membros são realmente usados. 

O vinculador sempre será executado antes da etapa do ProGuard. Por isso, o vinculador pode retirar um assembly/tipo/membro no qual você poderia esperar que o ProGuard fosse executado. (Para obter mais informações sobre a vinculação no Xamarin.Android, consulte [Vinculação no Android](~/android/deploy-test/linker.md).)

### <a name="proguard-step"></a>Etapa do ProGuard

Depois que a etapa de vinculador é concluída com êxito, o ProGuard é executado para remover o código de bytes Java não utilizado. Esta é a etapa que otimiza o APK. 

## <a name="using-proguard"></a>Uso do ProGuard

Para usar o ProGuard em seu projeto de aplicativo, você deve primeiro habilitar o ProGuard. Em seguida, você pode deixar que o processo de compilação do Xamarin.Android use um arquivo de configuração padrão do ProGuard ou você pode criar seu próprio arquivo de configuração personalizado para uso pelo ProGuard. 

### <a name="enabling-proguard"></a>Habilitação do ProGuard

Use as etapas a seguir para habilitar o ProGuard no seu projeto de aplicativo:

1. Verifique se o projeto está definido com a configuração **Versão** (isso é importante porque o vinculador deve ser executado para que o ProGuard seja executado): 

    [![Selecione Configuração de versão](proguard-images/02-set-release-sml.png)](proguard-images/02-set-release.png#lightbox)
   
2. Escolha **ProGuard** na lista suspensa **Redutor de código** na janela **Propriedades > Opções do Android**: 

    [![Encolhimento de código Proguard selecionado](proguard-images/03-enable-proguard-shrinker-sml.png)](proguard-images/03-enable-proguard-shrinker.png#lightbox)

Para a maioria dos aplicativos Xamarin.Android, o arquivo de configuração padrão do ProGuard fornecido pelo Xamarin.Android será suficiente para remover todos (e apenas) os códigos não utilizados. Para exibir a configuração padrão do ProGuard, abra o arquivo em **obj\\Release\\proguard\\proguard_xamarin.cfg**.

O exemplo a seguir ilustra um arquivo gerado **proguard_xamarin.cfg** típico:

```cfg
# This is Xamarin-specific (and enhanced) configuration.

-dontobfuscate

-keep class mono.MonoRuntimeProvider { *; <init>(...); }
-keep class mono.MonoPackageManager { *; <init>(...); }
-keep class mono.MonoPackageManager_Resources { *; <init>(...); }
-keep class mono.android.** { *; <init>(...); }
-keep class mono.java.** { *; <init>(...); }
-keep class mono.javax.** { *; <init>(...); }
-keep class opentk.platform.android.AndroidGameView { *; <init>(...); }
-keep class opentk.GameViewBase { *; <init>(...); }
-keep class opentk_1_0.platform.android.AndroidGameView { *; <init>(...); }
-keep class opentk_1_0.GameViewBase { *; <init>(...); }

-keep class android.runtime.** { <init>(***); }
-keep class assembly_mono_android.android.runtime.** { <init>(***); }
# hash for android.runtime and assembly_mono_android.android.runtime.
-keep class md52ce486a14f4bcd95899665e9d932190b.** { *; <init>(...); }
-keepclassmembers class md52ce486a14f4bcd95899665e9d932190b.** { *; <init>(...); }

# Android's template misses fluent setters...
-keepclassmembers class * extends android.view.View {
   *** set*(***);
}

# also misses those inflated custom layout stuff from xml...
-keepclassmembers class * extends android.view.View {
   <init>(android.content.Context,android.util.AttributeSet);
   <init>(android.content.Context,android.util.AttributeSet,int);
}
```

A próxima seção descreve como criar um arquivo de configuração personalizado do ProGuard. 

### <a name="customizing-proguard"></a>Personalização do ProGuard

Opcionalmente, você pode adicionar um arquivo de configuração personalizado do ProGuard para exercer mais controle sobre as ferramentas do ProGuard. Por exemplo, você talvez queira informar explicitamente ao ProGuard quais classes devem ser mantidas. Para fazer isso, crie um novo arquivo **.cfg** e aplique a ação de build `ProGuardConfiguration` no painel de **Propriedades** do **Gerenciador de Soluções**: 

[![Ação de compilação proguardConfiguration selecionada](proguard-images/04-build-action-sml.png)](proguard-images/04-build-action.png#lightbox)

Lembre-se que esse arquivo de configuração não substitui o arquivo **proguard_xamarin.cfg** do Xamarin.Android já que ambos são usados pelo ProGuard. 

Pode haver casos nos quais o ProGuard não é capaz de analisar corretamente o aplicativo. Ele pode potencialmente eliminar o código que seu aplicativo realmente precisa. Se isso acontecer, você pode adicionar uma linha `-keep` ao seu arquivo de configuração personalizado do ProGuard: 

```
-keep public class MyClass
```

Neste exemplo, `MyClass` é definido como o nome real da classe que você deseja que o ProGuard ignore.

Você também pode registrar seus próprios nomes com anotações do `[Register]` e usar esses nomes para personalizar as regras ProGuard. Você pode registrar nomes de Adapters, Views, BroadcastReceivers, Services, ContentProviders, Activities e Fragments. Para saber mais sobre como usar o atributo personalizado `[Register]`, confira [Trabalhar com JNI](~/android/platform/java-integration/working-with-jni.md).

### <a name="proguard-options"></a>Opções do ProGuard

O ProGuard oferece várias opções que podem ser configuradas para fornecer um melhor controle sobre sua operação. O [Manual do ProGuard](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/index.html#manual/introduction.html) fornece uma documentação de referência completa para o uso de ProGuard. 

O Xamarin.Android dá suporte às seguintes opções do ProGuard: 

- [Opções de entrada/saída](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#iooptions)

- [Manter opções](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#keepoptions)

- [Opções de redução](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#shrinkingoptions)

- [Opções gerais](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#generaloptions)

- [Caminhos de classe](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#classpath)

- [Nomes de arquivos](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#filename)

- [Filtros de arquivo](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#filefilters)

- [Filtros](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#filters)

- [Visão geral das `Keep` opções](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#keepoverview)

- [Manter modificadores de opção](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#keepoptionmodifiers)

- [Especificações de classe](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#classspecification)

As seguintes opções estão *ignoradas* pelo Xamarin.Android:

- [Opções de otimização](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#optimizationoptions)

- [Opções de ofuscação](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#obfuscationoptions) 

- [Opções de pré-verificação](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#preverificationoptions)

## <a name="proguard-and-android-nougat"></a>ProGuard e Android Nougat

Se você estiver tentando usar o ProGuard no Android 7.0 ou posterior, você deverá baixar uma versão mais recente do ProGuard porque o SDK do Android não é fornecido com uma nova versão que é compatível com o JDK 1.8.

Você pode usar esse [pacote do NuGet](https://www.nuget.org/packages/name.atsushieno.proguard.facebook/5.3.0) para instalar uma versão mais recente do `proguard.jar`. Para obter mais informações sobre como atualizar o SDK do Android padrão `proguard.jar`, consulte esta discussão sobre [Stack Overflow](https://stackoverflow.com/questions/39514518/xamarin-android-proguard-unsupported-class-version-number-52-0/39514706#39514706).

Você pode encontrar todas as versões do ProGuard na [página do SourceForge](https://sourceforge.net/projects/proguard/files/). 

## <a name="example-proguard-configurations"></a>Exemplos de configurações do ProGuard

Dois arquivos de configuração de exemplo do ProGuard estão listados abaixo. Observe que, nesses casos, o processo de build do Xamarin.Android fornecerá os jars de **entrada**, **saída** e **biblioteca**. Assim, você pode se concentrar em outras opções, como `-keep`. 

### <a name="a-simple-android-activity"></a>Uma simples atividade do Android

O exemplo a seguir ilustra a configuração de uma atividade Android simples:

```
-injars  bin/classes
-outjars bin/classes-processed.jar
-libraryjars /usr/local/java/android-sdk/platforms/android-9/android.jar

-dontpreverify
-repackageclasses ''
-allowaccessmodification
-optimizations !code/simplification/arithmetic

-keep public class mypackage.MyActivity
```

### <a name="a-complete-android-application"></a>Um aplicativo completo do Android

O exemplo a seguir ilustra a configuração de um aplicativo Android completo:

```
-injars  bin/classes
-injars  libs
-outjars bin/classes-processed.jar
-libraryjars /usr/local/java/android-sdk/platforms/android-9/android.jar

-dontpreverify
-repackageclasses ''
-allowaccessmodification
-optimizations !code/simplification/arithmetic
-keepattributes *Annotation*

-keep public class * extends android.app.Activity
-keep public class * extends android.app.Application
-keep public class * extends android.app.Service
-keep public class * extends android.content.BroadcastReceiver
-keep public class * extends android.content.ContentProvider

-keep public class * extends android.view.View {
public <init>(android.content.Context);
public <init>(android.content.Context, android.util.AttributeSet);
public <init>(android.content.Context, android.util.AttributeSet, int);
public void set*(...);
}

-keepclasseswithmembers class * {
public <init>(android.content.Context, android.util.AttributeSet);
}

-keepclasseswithmembers class * {
public <init>(android.content.Context, android.util.AttributeSet, int);
}

-keepclassmembers class * implements android.os.Parcelable {
static android.os.Parcelable$Creator CREATOR;
}

-keepclassmembers class **.R$* {
public static <fields>;
}
```

## <a name="proguard-and-the-xamarinandroid-build-process"></a>Processo de build do Xamarin.Android e ProGuard

As seções a seguir explicam como o ProGuard é executado durante um build de **Versão** do Xamarin.Android.

### <a name="what-command-is-proguard-running"></a>Qual comando o ProGuard está executando?

O ProGuard é simplesmente um `.jar` fornecido com o SDK do Android. Portanto, ele é invocado em um comando: 

```shell
java -jar proguard.jar options ...
```

### <a name="the-proguard-task"></a>A tarefa do ProGuard

A tarefa do ProGuard foi encontrada dentro do assembly **Xamarin.Android.Build.Tasks.dll**. Ela faz parte do destino `_CompileToDalvikWithDx`, que é uma parte do destino `_CompileDex`. 

A listagem a seguir fornece um exemplo de parâmetros padrão que são gerados depois de criar um novo projeto usando **Arquivo > Novo Projeto**: 

```
ProGuardJarPath = C:\Android\android-sdk\tools\proguard\lib\proguard.jar
AndroidSdkDirectory = C:\Android\android-sdk\
JavaToolPath = C:\Program Files (x86)\Java\jdk1.8.0_92\\bin
ProGuardToolPath = C:\Android\android-sdk\tools\proguard\
JavaPlatformJarPath = C:\Android\android-sdk\platforms\android-25\android.jar
ClassesOutputDirectory = obj\Release\android\bin\classes
AcwMapFile = obj\Release\acw-map.txt
ProGuardCommonXamarinConfiguration = obj\Release\proguard\proguard_xamarin.cfg
ProGuardGeneratedReferenceConfiguration = obj\Release\proguard\proguard_project_references.cfg
ProGuardGeneratedApplicationConfiguration = obj\Release\proguard\proguard_project_primary.cfg
ProGuardConfigurationFiles

    {sdk.dir}tools\proguard\proguard-android.txt;
    {intermediate.common.xamarin};
    {intermediate.references};
    {intermediate.application};
    ;

JavaLibrariesToEmbed = C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\MonoAndroid\v7.0\mono.android.jar
ProGuardJarInput = obj\Release\proguard\__proguard_input__.jar
ProGuardJarOutput = obj\Release\proguard\__proguard_output__.jar
DumpOutput = obj\Release\proguard\dump.txt
PrintSeedsOutput = obj\Release\proguard\seeds.txt
PrintUsageOutput = obj\Release\proguard\usage.txt
PrintMappingOutput = obj\Release\proguard\mapping.txt
```

O exemplo a seguir ilustra um comando ProGuard típico que é executado do IDE:

```cmd
C:\Program Files (x86)\Java\jdk1.8.0_92\\bin\java.exe -jar C:\Android\android-sdk\tools\proguard\lib\proguard.jar -include obj\Release\proguard\proguard_xamarin.cfg -include obj\Release\proguard\proguard_project_references.cfg -include obj\Release\proguard\proguard_project_primary.cfg "-injars 'obj\Release\proguard\__proguard_input__.jar';'C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\MonoAndroid\v7.0\mono.android.jar'" "-libraryjars 'C:\Android\android-sdk\platforms\android-25\android.jar'" -outjars "obj\Release\proguard\__proguard_output__.jar" -optimizations !code/allocation/variable
```

## <a name="troubleshooting"></a>Solução de problemas

### <a name="file-issues"></a>Problemas de arquivo

A seguinte mensagem de erro pode ser exibida quando o ProGuard lê o arquivo de configuração: 

```
Unknown option '-keep' in line 1 of file 'proguard.cfg'
```

Esse problema geralmente ocorre no Windows, pois o arquivo `.cfg` tem codificação incorreta. O ProGuard não processa BOM _(Marca de Ordem de Byte)_ que possa estar presente em arquivos de texto. Se houver um BOM, o ProGuard será encerrado com o erro acima. 

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Para evitar esse problema, edite o arquivo de configuração personalizada de um editor de texto que permitirá que o arquivo seja salvo sem um BOM. Para resolver esse problema, verifique se o seu editor de texto tem sua codificação definida como `UTF-8`. Por exemplo, o editor de texto [Notepad++](https://notepad-plus-plus.org/) pode salvar arquivos sem BOM ao selecionar a **Codificação&gt; no UTF-8 Sem BOM** ao salvar o arquivo. 

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Para evitar esse problema, salve o arquivo de configuração personalizado por meio de um editor de texto que permita omitir a BOM. 

-----

### <a name="other-issues"></a>Outros problemas

A página de [Solução de problemas](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/index.html#manual/troubleshooting.html) do ProGuard aborda problemas comuns que podem ocorrer (e soluções) ao usar o ProGuard.

## <a name="summary"></a>Resumo

Este guia explicou como o ProGuard funciona no Xamarin.Android, como habilitá-lo em seu projeto de aplicativo e como configurá-lo. Ele forneceu configurações de exemplo do ProGuard e descreveu soluções para problemas comuns. Para saber mais sobre a ferramenta ProGuard e Android, consulte [Reduza seus códigos e recursos](https://developer.android.com/tools/help/proguard.html). 

## <a name="related-links"></a>Links relacionados

- [Preparar um Aplicativo para Lançamento](~/android/deploy-test/release-prep/index.md)
