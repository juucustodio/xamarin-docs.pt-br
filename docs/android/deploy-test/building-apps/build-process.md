---
title: Processo de build
description: Este documento fornecerá uma visão geral do processo de compilação do Xamarin. Android.
ms.prod: xamarin
ms.assetid: 3BE5EE1E-3FF6-4E95-7C9F-7B443EE3E94C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/11/2020
ms.openlocfilehash: 4a89cfbb2406b6a5cda125044d43736dfa02d791
ms.sourcegitcommit: 01ccefd54c0ced724784dbe1aec9ecfc9b00e633
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92630225"
---
# <a name="build-process"></a>Processo de build

O processo de compilação do Xamarin. Android é responsável por colar tudo em conjunto: [gerando `Resource.designer.cs` ](~/android/internals/api-design.md), dando suporte a [`@(AndroidAsset)`](~/android/deploy-test/building-apps/build-items.md#androidasset) , [`@(AndroidResource)`](~/android/deploy-test/building-apps/build-items.md#androidresource) e outras [ações de compilação](~/android/deploy-test/building-apps/build-items.md), gerando [wrappers](~/android/platform/java-integration/android-callable-wrappers.md)que podem ser chamados por Android e gerando um `.apk` para execução em dispositivos Android.

## <a name="application-packages"></a>Pacotes de Aplicativos

Em termos gerais, há dois tipos de pacotes de aplicativos do Android (arquivos `.apk`) que o sistema de build do Xamarin.Android pode gerar:

- Builds de **versão** , que são totalmente independentes e não requerem pacotes adicionais para executar. Esses são os pacotes que seriam fornecidos para uma loja de aplicativos.

- Builds de **depuração** , que não o são.

Não coincidentemente, eles correspondem ao `Configuration` do MSBuild que produz o pacote.

## <a name="shared-runtime"></a>Runtime compartilhado

O *runtime compartilhado* é um par de pacotes Android adicionais que fornecem a biblioteca de classes base (`mscorlib.dll`, etc.) e a biblioteca de associações do Android (`Mono.Android.dll`, etc.). Builds de depuração contam com o runtime compartilhado em vez de incluir os assemblies da biblioteca de classes base e da biblioteca de associações dentro do pacote do aplicativo Android, permitindo que o pacote de depuração seja menor.

O tempo de execução compartilhado pode ser desabilitado em compilações de depuração definindo o [`$(AndroidUseSharedRuntime)`](~/android/deploy-test/building-apps/build-properties.md#androidusesharedruntime)
Propriedade para `False` .

<a name="Fast_Deployment"></a>

## <a name="fast-deployment"></a>Fast Deployment

O *Fast Deployment* trabalha em conjunto com o runtime compartilhado para reduzir ainda mais o tamanho do pacote do aplicativo Android. Isso é feito não agrupando assemblies do aplicativo dentro do pacote. Em vez disso, eles são copiados para o destino por meio de `adb push`. Esse processo agiliza o ciclo de build/implantação/depuração porque se *somente* assemblies são alterados, o pacote não é reinstalado. Em vez disso, apenas os assemblies atualizados são sincronizados novamente ao dispositivo de destino.

O Fast Deployment reconhecidamente falha em dispositivos que bloqueiam a sincronização de `adb` ao diretório `/data/data/@PACKAGE_NAME@/files/.__override__`.

O Fast Deployment é habilitado por padrão e pode ser desabilitado em builds de depuração, definindo a propriedade `$(EmbedAssembliesIntoApk)` para `True`.

## <a name="msbuild-projects"></a>Projetos do MSBuild

O processo de build do Xamarin.Android baseia-se no MSBuild, que também é o formato de arquivo de projeto usado pelo Visual Studio para Mac e pelo Visual Studio.
Em geral, os usuários não precisarão editar os arquivos do MSBuild manualmente &ndash; o IDE cria projetos totalmente funcionais e os atualiza com eventuais alterações feitas, além de invocar destinos de build automaticamente conforme necessário.

Usuários avançados talvez queiram realizar ações sem suporte na interface gráfica do IDE, portanto, o processo de build pode ser personalizado editando-se o arquivo de projeto diretamente.
Esta página documenta somente os recursos e personalizações específicos do Xamarin.Android &ndash; muitas outras coisas são possíveis com os itens, propriedades e destinos normais do MSBuild.

<a name="Build_Targets"></a>

## <a name="binding-projects"></a>Associando projetos

As seguintes propriedades de MSBuild são usadas com [projetos de associação](~/android/platform/binding-java-library/index.md):

- [`$(AndroidClassParser)`](~/android/deploy-test/building-apps/build-properties.md#androidclassparser)
- [`$(AndroidCodegenTarget)`](~/android/deploy-test/building-apps/build-properties.md#androidcodegentarget)

## <a name="resourcedesignercs-generation"></a>`Resource.designer.cs` Geração

As seguintes propriedades do MSBuild são usadas para controlar a geração do `Resource.designer.cs` arquivo:

- [`$(AndroidAapt2CompileExtraArgs)`](~/android/deploy-test/building-apps/build-properties.md#androidaapt2compileextraargs)
- [`$(AndroidAapt2LinkExtraArgs)`](~/android/deploy-test/building-apps/build-properties.md#androidaapt2linkextraargs)
- [`$(AndroidExplicitCrunch)`](~/android/deploy-test/building-apps/build-properties.md#androidexplicitcrunch)
- [`$(AndroidR8IgnoreWarnings)`](~/android/deploy-test/building-apps/build-properties.md#androidr8ignorewarnings)
- [`$(AndroidResgenExtraArgs)`](~/android/deploy-test/building-apps/build-properties.md#androidresgenextraargs)
- [`$(AndroidResgenFile)`](~/android/deploy-test/building-apps/build-properties.md#androidresgenfile)
- [`$(AndroidUseAapt2)`](~/android/deploy-test/building-apps/build-properties.md#androiduseaapt2)
- [`$(MonoAndroidResourcePrefix)`](~/android/deploy-test/building-apps/build-properties.md#monoandroidresourceprefix)

## <a name="signing-properties"></a>Propriedades de assinatura

As propriedades de assinatura controlam como o pacote do aplicativo é assinado, para que ele possa ser instalado em um dispositivo Android. Para permitir uma iteração de build mais rápida, as tarefas do Xamarin.Android não assinam pacotes durante o processo de build, porque assinar é algo muito lento. Em vez disso, eles são assinados (se necessário) antes da instalação ou durante a exportação, pelo IDE ou pelo destino de build *Install* . Invocar o destino *SignAndroidPackage* produzirá um pacote com o sufixo `-Signed.apk` no diretório de saída.

Por padrão, o destino da assinatura gera uma nova chave de assinatura de depuração, se necessário. Se você quiser usar uma chave específica, por exemplo, em um servidor de compilação, as seguintes propriedades do MSBuild serão usadas:

- [`$(AndroidDebugKeyAlgorithm)`](~/android/deploy-test/building-apps/build-properties.md#androiddebugkeyalgorithm)
- [`$(AndroidDebugKeyValidity)`](~/android/deploy-test/building-apps/build-properties.md#androiddebugkeyvalidity)
- [`$(AndroidDebugStoreType)`](~/android/deploy-test/building-apps/build-properties.md#androiddebugstoretype)
- [`$(AndroidKeyStore)`](~/android/deploy-test/building-apps/build-properties.md#androidkeystore)
- [`$(AndroidSigningKeyAlias)`](~/android/deploy-test/building-apps/build-properties.md#androidsigningkeyalias)
- [`$(AndroidSigningKeyPass)`](~/android/deploy-test/building-apps/build-properties.md#androidsigningkeypass)
- [`$(AndroidSigningKeyStore)`](~/android/deploy-test/building-apps/build-properties.md#androidsigningkeystore)
- [`$(AndroidSigningStorePass)`](~/android/deploy-test/building-apps/build-properties.md#androidsigningstorepass)
- [`$(JarsignerTimestampAuthorityCertificateAlias)`](~/android/deploy-test/building-apps/build-properties.md#jarsignertimestampauthoritycertificatealias)
- [`$(JarsignerTimestampAuthorityUrl)`](~/android/deploy-test/building-apps/build-properties.md#jarsignertimestampauthorityurl)

### <a name="keytool-option-mapping"></a>`keytool` Mapeamento de opção

Considere a seguinte `keytool` invocação:

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

Os pontos de extensão incluem:

- [' $ (AfterGenerateAndroidManifest)](~/android/deploy-test/building-apps/build-properties.md#aftergenerateandroidmanifest)
- [' $ (BeforeGenerateAndroidManifest)](~/android/deploy-test/building-apps/build-properties.md#beforegenerateandroidmanifest)

Uma advertência sobre como estender o processo de compilação: se não for escrito corretamente, as extensões de compilação poderão afetar o desempenho da compilação, especialmente se forem executadas em cada compilação. É altamente recomendável que você leia a [documentação](/visualstudio/msbuild/msbuild) do MSBuild antes de implementar essas extensões.

## <a name="target-definitions"></a>Definições de destino

As partes do processo de build específicas do Xamarin.Android são definidas em `$(MSBuildExtensionsPath)\Xamarin\Android\Xamarin.Android.CSharp.targets`, mas destinos normais específicos a uma linguagem, tais como *Microsoft.CSharp.targets* , também são necessários para compilar o assembly.

As seguintes propriedades de build devem ser definidas antes de importar quaisquer destinos de linguagem de programação:

```xml
<PropertyGroup>
  <TargetFrameworkIdentifier>MonoDroid</TargetFrameworkIdentifier>
  <MonoDroidVersion>v1.0</MonoDroidVersion>
  <TargetFrameworkVersion>v2.2</TargetFrameworkVersion>
</PropertyGroup>
```

Todos esses destinos e propriedades podem ser incluídos para C# importando *Xamarin.Android.CSharp.targets* :

```xml
<Import Project="$(MSBuildExtensionsPath)\Xamarin\Android\Xamarin.Android.CSharp.targets" />
```

Esse arquivo pode ser facilmente adaptado para outras linguagens de programação.
