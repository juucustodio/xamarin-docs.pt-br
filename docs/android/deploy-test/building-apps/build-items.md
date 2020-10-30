---
title: Compilar itens
description: Este documento listará todos os grupos de itens com suporte no processo de compilação do Xamarin. Android.
ms.prod: xamarin
ms.assetid: 5EBEE1A5-3879-45DD-B1DE-5CD4327C2656
ms.technology: xamarin-android
author: jonpryor
ms.author: jopryo
ms.date: 09/23/2020
ms.openlocfilehash: 8a23e973687ac9f775042685122d558788fc7be7
ms.sourcegitcommit: d1980b2251999224e71c1289e4b4097595b7e261
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "92897436"
---
# <a name="build-items"></a>Compilar itens

Os itens de compilação controlam como um aplicativo Xamarin. Android ou um projeto de biblioteca é compilado.

## <a name="androidasset"></a>AndroidAsset

Dá suporte a [ativos do Android](https://developer.android.com/guide/topics/resources/providing-resources#OriginalFiles), arquivos que seriam incluídos na `assets` pasta em um projeto Java Android.

## <a name="androidaarlibrary"></a>AndroidAarLibrary

A ação de Build de `AndroidAarLibrary` deve ser usada para referenciar `.aar` arquivos diretamente. Essa ação do build será mais usada pelos componentes do Xamarin. É necessário incluir referências a `.aar` arquivos que são necessários para obter Google Play e outros serviços funcionando.

Os arquivos com esta ação de compilação serão tratados de forma semelhante aos recursos incorporados encontrados em projetos de biblioteca. O `.aar` será extraído no diretório intermediário. Em seguida, todos os ativos, recursos e `.jar` arquivos serão incluídos nos grupos de itens apropriados.

## <a name="androidaotprofile"></a>AndroidAotProfile

Usado para fornecer um perfil de AOT, para uso com a AOT guiada por perfil.

## <a name="androidboundlayout"></a>AndroidBoundLayout

Indica que o code-behind deverá ser gerado para o arquivo de layout quando a propriedade `AndroidGenerateLayoutBindings` estiver definida como `false`. Em todos os outros aspectos, ela é idêntica à `AndroidResource` descrita acima. Essa ação pode ser usada **apenas** com arquivos de layout:

```xml
<AndroidBoundLayout Include="Resources\layout\Main.axml" />
```

## <a name="androidenvironment"></a>AndroidEnvironment

Arquivos com uma ação de build `AndroidEnvironment` são usados para [inicializar variáveis de ambiente e propriedades do sistema durante a inicialização do processo](~/android/deploy-test/environment.md).
A ação de build `AndroidEnvironment` pode ser aplicada a vários arquivos, e eles serão avaliados sem nenhuma ordem específica (então não especifique a mesma propriedade de sistema ou variável de ambiente em vários arquivos).

## <a name="androidfragmenttype"></a>AndroidFragmentType

Especifica o tipo totalmente qualificado de padrão a ser usado para todos os elementos de layout `<fragment>` ao gerar o código de associações de layout. O padrão da propriedade é o tipo `Android.App.Fragment` do Android.

## <a name="androidjavalibrary"></a>AndroidJavaLibrary

Arquivos com uma ação de build `AndroidJavaLibrary` são arquivos Java (arquivos `.jar`) que serão incluídos no pacote do Android final.

## <a name="androidjavasource"></a>AndroidJavaSource

Arquivos com uma ação de build `AndroidJavaSource` são código-fonte do Java que será incluído no pacote do Android final.

## <a name="androidlintconfig"></a>AndroidLintConfig

A ação de compilação ' AndroidLintConfig ' deve ser usada em conjunto com o [`$(AndroidLintEnabled)`](~/android/deploy-test/building-apps/build-properties.md#androidlintenabled)
property. Os arquivos com essa ação de build serão mesclados e passados para as ferramentas `lint` do Android. Eles deverão ser arquivos XML que contenham informações sobre quais testes devem ser habilitados e desabilitados.

Confira a [documentação do lint](https://developer.android.com/studio/write/lint) para obter mais detalhes.

## <a name="androidnativelibrary"></a>AndroidNativeLibrary

[Bibliotecas nativas](~/android/platform/native-libraries.md) são adicionadas ao build definindo-se as ações de build delas para `AndroidNativeLibrary`.

Observe que, como o Android é compatível com várias ABIs (interfaces binárias de aplicativo), o sistema de build deve saber para qual ABI a biblioteca nativa é compilada. Há duas maneiras de fazer isso:

1. "Detecção" de caminho.
2. Usando o atributo do item `Abi`.

Com a detecção de caminho, o nome do diretório pai da biblioteca nativa é usado para especificar a ABI usada como destino pela biblioteca. Portanto, se você adicionar `lib/armeabi-v7a/libfoo.so` ao build, em a ABI será "detectada" como `armeabi-v7a`.

### <a name="item-attribute-name"></a>Nome de atributo de item

**Abi** &ndash; especifica a ABI da biblioteca nativa.

```xml
<ItemGroup>
  <AndroidNativeLibrary Include="path/to/libfoo.so">
    <Abi>armeabi-v7a</Abi>
  </AndroidNativeLibrary>
</ItemGroup>
```

## <a name="androidresource"></a>AndroidResource

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
  <MonoAndroidResourcePrefix>Resources;Resources-Debug</MonoAndroidResourcePrefix>
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

## <a name="androidresourceanalysisconfig"></a>AndroidResourceAnalysisConfig

A ação de Build `AndroidResourceAnalysisConfig` marca um arquivo como um arquivo de configuração de nível de severidade para a ferramenta de diagnóstico de layout do Xamarin designer Android. Atualmente, isso é usado apenas no editor de layout e não em mensagens de compilação.

Consulte a [documentação de análise de recursos do Android](../../user-interface/android-designer/diagnostics.md) para obter mais detalhes.

Adicionado no Xamarin. Android 10,2.

## <a name="content"></a>Conteúdo

A ação de build `Content` normal não é compatível (pois ainda não descobrimos como dar suporte a ela sem uma etapa de primeira execução de custo possivelmente alto).

Começando com o Xamarin.Android 5.1, tentar usar a ação de build `@(Content)` resultará em um aviso `XA0101`.

## <a name="linkdescription"></a>LinkDescription

Arquivos com uma ação de build *LinkDescription* são usados para [controlar o comportamento do vinculador](~/cross-platform/deploy-test/linker.md).

## <a name="proguardconfiguration"></a>ProguardConfiguration

Arquivos com uma ação de build *ProguardConfiguration* contêm opções que são usadas para controlar o comportamento de `proguard`. Para obter mais informações sobre essa ação de build, consulte [ProGuard](~/android/deploy-test/release-prep/proguard.md).

Esses arquivos são ignorados, a menos que o [`$(EnableProguard)`](~/android/deploy-test/building-apps/build-properties.md#enableproguard)
A Propriedade MSBuild é `True` .
