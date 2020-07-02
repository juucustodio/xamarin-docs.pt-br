---
title: Solução de problemas de associação
description: Este artigo resume os erros comuns de servidor que podem ocorrer ao gerar associações, juntamente com possíveis causas e formas sugeridas de resolvê-las.
ms.prod: xamarin
ms.assetid: BB81FCCF-F7BF-4C78-884E-F02C49AA819A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 3ec01d9322d4b6706fccd0959ae5944f71e82664
ms.sourcegitcommit: a3f13a216fab4fc20a9adf343895b9d6a54634a5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85853025"
---
# <a name="troubleshooting-bindings"></a>Solução de problemas de associação

> [!IMPORTANT]
> No momento, estamos investigando o uso de associação personalizada na plataforma Xamarin. Faça [**esta pesquisa**](https://www.surveymonkey.com/r/KKBHNLT) para informar os futuros esforços de desenvolvimento.

_Este artigo resume os erros comuns de servidor que podem ocorrer ao gerar associações, juntamente com possíveis causas e formas sugeridas de resolvê-las._

## <a name="overview"></a>Visão geral

Associar um arquivo de biblioteca do Android (um **. aar** ou um **. jar**) raramente é um sórdida direto; Geralmente, é necessário um esforço adicional para atenuar os problemas resultantes das diferenças entre Java e .NET.
Esses problemas impedirão o Xamarin. Android de ligar a biblioteca do Android e apresentarem-se como mensagens de erro no log de compilação. Este guia fornecerá algumas dicas para solucionar problemas, listar alguns dos problemas/cenários mais comuns e fornecer soluções possíveis para ligar com êxito a biblioteca do Android.

Ao ligar uma biblioteca Android existente, é necessário ter em mente os seguintes pontos:

- **As dependências externas para a biblioteca** &ndash; Todas as dependências de Java exigidas pela biblioteca do Android devem ser incluídas no projeto Xamarin. Android como um **ReferenceJar** ou como um **EmbeddedReferenceJar**.

- **O nível da API do Android que a biblioteca do Android é voltados** &ndash; Não é possível "fazer downgrade" do nível da API do Android; Verifique se o projeto de associação Xamarin. Android está direcionado ao mesmo nível de API (ou superior) que a biblioteca do Android.

- **A versão do Android JDK que foi usada para empacotar a biblioteca** &ndash; do Android Erros de associação podem ocorrer se a biblioteca do Android foi criada com uma versão diferente do JDK do que a que está sendo usada pelo Xamarin. Android. Se possível, recompile a biblioteca do Android usando a mesma versão do JDK que é usada pela sua instalação do Xamarin. Android.

A primeira etapa para solucionar problemas de associação a uma biblioteca Xamarin. Android é habilitar a [saída de diagnóstico do MSBuild](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output).
Depois de habilitar a saída de diagnóstico, recompile o projeto de associação do Xamarin. Android e examine o log de compilação para localizar pistas sobre a causa do problema.

Ele também pode provar útil para descompilar a biblioteca do Android e examinar os tipos e métodos que o Xamarin. Android está tentando associar. Isso é abordado em mais detalhes posteriormente neste guia.

## <a name="decompiling-an-android-library"></a>Descompilando uma biblioteca do Android

Inspecionar as classes e os métodos das classes Java pode fornecer informações valiosas que auxiliarão na vinculação de uma biblioteca.
[JD-GUI](http://jd.benow.ca/) é um utilitário gráfico que pode exibir o código-fonte do Java dos arquivos de **classe** contidos em um jar. Ele pode ser executado como um aplicativo autônomo ou como um plug-in para IntelliJ ou eclipse.

Para descompilar uma biblioteca Android, abra o **. Arquivo JAR** com o descompilador Java. Se a biblioteca for um **. Arquivo AAR** , é necessário extrair o arquivo **classes. jar** do arquivo morto. Veja a seguir um exemplo de captura de tela do uso de JD-GUI para analisar o jar [Picasso](https://square.github.io/picasso/) :

![Usando o compilador Java para analisar Picasso-2.5.2. jar](troubleshooting-bindings-images/troubleshoot-bindings-01.png)

Depois de descompilar a biblioteca do Android, examine o código-fonte. Em geral, procure por:

- **Classes que têm características de ofuscação** &ndash; As características das classes ofuscadas incluem:

  - O nome da classe inclui um **$** , ou seja, **uma classe $.**
  - O nome da classe está totalmente comprometido com caracteres minúsculos, ou seja, **a. Class**      

- ** `import` instruções para bibliotecas** &ndash; não referenciadas Identifique a biblioteca não referenciada e adicione essas dependências ao projeto de associação Xamarin. Android com uma **ação de compilação** de **ReferenceJar** ou **EmbedddedReferenceJar**.

> [!NOTE]
> A descompilação de uma biblioteca Java pode ser proibida ou sujeita a restrições legais com base em leis locais ou na licença sob a qual a biblioteca Java foi publicada. Se necessário, inscreva os serviços de um profissional legal antes de tentar descompilar uma biblioteca Java e inspecionar o código-fonte.

## <a name="inspect-apixml"></a>Inspecionar API.XML

Como parte da criação de um projeto de associação, o Xamarin. Android irá gerar um nome de arquivo XML **obj/Debug/api.xml**:

![Gerado api.xml em obj/Debug](troubleshooting-bindings-images/troubleshoot-bindings-02.png)

Esse arquivo fornece uma lista de todas as APIs Java que o Xamarin. Android está tentando associar. O conteúdo desse arquivo pode ajudar a identificar quaisquer tipos ou métodos ausentes, associação duplicada. Embora a inspeção desse arquivo seja entediante e demorada, ela pode fornecer pistas sobre o que pode estar causando problemas de ligação. Por exemplo, **api.xml** pode revelar que uma propriedade está retornando um tipo inadequado ou que há dois tipos que compartilham o mesmo nome gerenciado.

## <a name="known-issues"></a>Problemas conhecidos

Esta seção listará algumas das mensagens de erro ou sintomas comuns que ocorrem ao tentar associar uma biblioteca do Android.

### <a name="problem-java-version-mismatch"></a>Problema: incompatibilidade de versão do Java

Às vezes, os tipos não serão gerados ou falhas inesperadas poderão ocorrer porque você está usando uma versão mais recente ou mais antiga do Java em comparação com a qual a biblioteca foi compilada. Recompile a biblioteca do Android com a mesma versão do JDK que seu projeto do Xamarin. Android está usando.

### <a name="problem-at-least-one-java-library-is-required"></a>Problema: pelo menos uma biblioteca Java é necessária

Você recebe o erro "pelo menos uma biblioteca Java é necessária", mesmo que haja um. JAR foi adicionado.

#### <a name="possible-causes"></a>Possíveis causas:

Verifique se a ação de compilação está definida como `EmbeddedJar` . Como há várias ações de compilação para o. Arquivos jar (como `InputJar` , `EmbeddedJar` `ReferenceJar` e `EmbeddedReferenceJar` ), o gerador de associação não pode adivinhar automaticamente qual deles usar por padrão. Para obter mais informações sobre ações de compilação, consulte [Build Actions](~/android/platform/binding-java-library/index.md).

### <a name="problem-binding-tools-cannot-load-the-jar-library"></a>Problema: as ferramentas de associação não podem carregar o. Biblioteca JAR

Falha do gerador de biblioteca de associação ao carregar o. Biblioteca JAR.

#### <a name="possible-causes"></a>Possíveis causas

Qualquer. Bibliotecas JAR que usam ofuscação de código (via ferramentas como PROGuard) não podem ser carregadas pelas ferramentas Java. Como nossa ferramenta usa a reflexão de Java e a biblioteca de engenharia de código de bytes ASM, essas ferramentas dependentes podem rejeitar as bibliotecas ofuscadas enquanto as ferramentas de tempo de execução do Android podem passar. A solução alternativa para isso é vincular manualmente essas bibliotecas em vez de usar o gerador de associação.

### <a name="problem-missing-c-types-in-generated-output"></a>Problema: tipos C# ausentes na saída gerada.

O Binding **. dll** cria, mas perde alguns tipos de Java, ou a fonte C# gerada não é compilada devido a um erro informando que há tipos ausentes.

#### <a name="possible-causes"></a>Possíveis causas:

Esse erro pode ocorrer por vários motivos, conforme listado abaixo:

- A biblioteca que está sendo associada pode fazer referência a uma segunda biblioteca de Java. Se a API pública para a biblioteca associada usar tipos da segunda biblioteca, você também deverá fazer referência a uma associação gerenciada para a segunda biblioteca.

- É possível que uma biblioteca tenha sido injetada devido à reflexão do Java, semelhante ao motivo do erro de carregamento da biblioteca acima, causando o carregamento inesperado de metadados. As ferramentas do Xamarin. Android não podem resolver essa situação no momento. Nesse caso, a biblioteca deve ser associada manualmente.

- Houve um bug no tempo de execução do .NET 4,0 que não carregou assemblies quando deveria ter. Esse problema foi corrigido no tempo de execução do .NET 4,5.

- O Java permite derivar uma classe pública de classe não pública, mas não há suporte para isso no .NET. Como o gerador de associação não gera associações para classes não públicas, classes derivadas como essas não podem ser geradas corretamente. Para corrigir isso, remova a entrada de metadados para essas classes derivadas usando o Remove-node no **Metadata.xml**ou corrija os metadados que estão tornando a classe não pública pública. Embora a última solução crie a associação para que a origem do C# seja compilada, a classe não pública não deve ser usada.

  Por exemplo:

  ```xml
  <attr path="/api/package[@name='com.some.package']/class[@name='SomeClass']"
      name="visibility">public</attr>
  ```

- As ferramentas que ofuscam as bibliotecas Java podem interferir no gerador de associação do Xamarin. Android e sua capacidade de gerar classes de wrapper C#. O trecho a seguir mostra como atualizar **Metadata.xml** para desofuscar um nome de classe:

  ```xml
  <attr path="/api/package[@name='{package_name}']/class[@name='{name}']"
      name="obfuscated">false</attr>
  ```

### <a name="problem-generated-c-source-does-not-build-due-to-parameter-type-mismatch"></a>Problema: a fonte C# gerada não é compilada devido à incompatibilidade de tipo de parâmetro

A fonte C# gerada não é compilada. Os tipos de parâmetro do método substituído não correspondem.

#### <a name="possible-causes"></a>Possíveis causas:

O Xamarin. Android inclui uma variedade de campos Java que são mapeados para enums nas associações do C#. Elas podem causar incompatibilidades de tipo nas associações geradas. Para resolver isso, as assinaturas de método criadas a partir do gerador de associação precisam ser modificadas para usar as enums. Para obter mais informações, consulte [corrigindo enums](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md).

### <a name="problem-noclassdeffounderror-in-packaging"></a>Problema: NoClassDefFoundError no empacotamento

`java.lang.NoClassDefFoundError`é lançada na etapa de empacotamento.

#### <a name="possible-causes"></a>Possíveis causas:

O motivo mais provável para esse erro é que uma biblioteca Java obrigatória precisa ser adicionada ao projeto de aplicativo (**. csproj**). . Os arquivos JAR não são resolvidos automaticamente. Uma associação de biblioteca Java nem sempre é gerada em relação a um assembly de usuário que não existe no dispositivo ou emulador de destino (como Google Maps **Maps. jar**). Esse não é o caso do suporte a projetos de biblioteca do Android, como a biblioteca. O JAR é inserido na biblioteca dll. Por exemplo: [Bug 4288](https://bugzilla.xamarin.com/show_bug.cgi?id=4288)

### <a name="problem-duplicate-custom-eventargs-types"></a>Problema: tipos de EventArgs personalizados duplicados

A compilação falha devido a tipos duplicados de EventArgs personalizados. Um erro como este ocorre:

```shell
error CS0102: The type `Com.Google.Ads.Mediation.DismissScreenEventArgs' already contains a definition for `p0'
```

#### <a name="possible-causes"></a>Possíveis causas:

Isso ocorre porque há algum conflito entre os tipos de evento provenientes de mais de um tipo de "ouvinte" de interface que compartilha métodos com nomes idênticos. Por exemplo, se houver duas interfaces Java, como visto no exemplo abaixo, o gerador cria `DismissScreenEventArgs` para `MediationBannerListener` e `MediationInterstitialListener` , resultando no erro.

```java
// Java:
public interface MediationBannerListener {
    void onDismissScreen(MediationBannerAdapter p0);
}
public interface MediationInterstitialListener {
    void onDismissScreen(MediationInterstitialAdapter p0);
}
```

Isso é por design, de forma que nomes longos em tipos de argumento de evento sejam evitados. Para evitar esses conflitos, é necessária uma transformação de metadados. Edite **Transforms\Metadata.xml** e adicione um `argsType` atributo em uma das interfaces (ou no método de interface):

```xml
<attr path="/api/package[@name='com.google.ads.mediation']/
        interface[@name='MediationBannerListener']/method[@name='onDismissScreen']"
        name="argsType">BannerDismissScreenEventArgs</attr>

<attr path="/api/package[@name='com.google.ads.mediation']/
        interface[@name='MediationInterstitialListener']/method[@name='onDismissScreen']"
        name="argsType">IntersitionalDismissScreenEventArgs</attr>

<attr path="/api/package[@name='android.content']/
        interface[@name='DialogInterface.OnClickListener']"
        name="argsType">DialogClickEventArgs</attr>
```

### <a name="problem-class-does-not-implement-interface-method"></a>Problema: a classe não implementa o método de interface

Uma mensagem de erro é produzida indicando que uma classe gerada não implementa um método necessário para uma interface implementada pela classe gerada. No entanto, observando o código gerado, você pode ver que o método é implementado.

Aqui está um exemplo do erro:

```shell
obj\Debug\generated\src\Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.cs(8,23):
error CS0738: 'Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter' does not
implement interface member 'Oauth.Signpost.Http.IHttpRequest.Unwrap()'.
'Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.Unwrap()' cannot implement
'Oauth.Signpost.Http.IHttpRequest.Unwrap()' because it does not have the matching
return type of 'Java.Lang.Object'
```

#### <a name="possible-causes"></a>Possíveis causas:

Esse é um problema que ocorre com a associação de métodos Java com tipos de retorno covariantes. Neste exemplo, o método `Oauth.Signpost.Http.IHttpRequest.UnWrap()` precisa retornar `Java.Lang.Object` . No entanto, o método `Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.UnWrap()` tem um tipo de retorno de `HttpURLConnection` . Há duas maneiras de corrigir esse problema:

- Adicione uma declaração de classe parcial para `HttpURLConnectionRequestAdapter` e Implemente explicitamente `IHttpRequest.Unwrap()` :

  ```csharp
  namespace Oauth.Signpost.Basic {
      partial class HttpURLConnectionRequestAdapter {
          Java.Lang.Object OauthSignpost.Http.IHttpRequest.Unwrap() {
              return Unwrap();
          }
      }
  }
  ```

- Remova a covariância do código C# gerado. Isso envolve adicionar a seguinte transformação a **Transforms\Metadata.xml** que fará com que o código C# gerado tenha um tipo de retorno de `Java.Lang.Object` :

  ```xml
  <attr
      path="/api/package[@name='oauth.signpost.basic']/class[@name='HttpURLConnectionRequestAdapter']/method[@name='unwrap']"
      name="managedReturn">Java.Lang.Object
  </attr>
  ```

### <a name="problem-name-collisions-on-inner-classes--properties"></a>Problema: conflitos de nome em classes/propriedades internas

Visibilidade conflitante em objetos herdados.

Em Java, não é necessário que uma classe derivada tenha a mesma visibilidade que seu pai. O Java apenas corrigirá isso para você. No C#, isso deve ser explícito, portanto, você precisa garantir que todas as classes na hierarquia tenham a visibilidade apropriada. O exemplo a seguir mostra como alterar um nome de pacote Java de `com.evernote.android.job` para `Evernote.AndroidJob` :

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

### <a name="problem-a-so-library-required-by-the-binding-is-not-loading"></a>Problema: A **. portanto,** a biblioteca exigida pela associação não está carregando

Alguns projetos de associação também podem depender da funcionalidade em uma biblioteca **. portanto** . É possível que o Xamarin. Android não carregue automaticamente a biblioteca **. portanto** . Quando o código Java encapsulado é executado, o Xamarin. Android não fará a chamada JNI e a mensagem de erro _Java. lang. UnsatisfiedLinkError: método nativo não encontrado:_ aparecerá no logcat para o aplicativo.

A correção para isso é carregar manualmente a biblioteca **. portanto** com uma chamada para `Java.Lang.JavaSystem.LoadLibrary` . Por exemplo, supondo que um projeto Xamarin. Android tenha uma biblioteca compartilhada **libpocketsphinx_jni. portanto** , incluído no projeto de associação com uma ação de compilação de **EmbeddedNativeLibrary**, o trecho a seguir (executado antes de usar a biblioteca compartilhada) carregará a biblioteca **. so** :

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="summary"></a>Resumo

Neste artigo, listamos os problemas de solução de problemas comuns associados a associações Java e explicamos como resolvê-los.

## <a name="related-links"></a>Links Relacionados

- [Projetos de biblioteca](https://developer.android.com/tools/projects/index.html#LibraryProjects)
- [Trabalhando com JNI](~/android/platform/java-integration/working-with-jni.md)
- [Habilitar saída de diagnóstico](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)
- [Xamarin para desenvolvedores Android](~/android/get-started/java-developers.md)
- [JD-GUI](http://jd.benow.ca/)
