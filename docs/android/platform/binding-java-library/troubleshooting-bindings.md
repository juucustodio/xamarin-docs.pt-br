---
title: Solução de problemas de associação
description: Este artigo resume erros comuns do servidor que podem ocorrer ao gerar vinculações, juntamente com possíveis causas e formas sugeridas de resolvê-los.
ms.prod: xamarin
ms.assetid: BB81FCCF-F7BF-4C78-884E-F02C49AA819A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 0c273797d7512f062260e49e0f71fdd1132f037b
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292679"
---
# <a name="troubleshooting-bindings"></a>Solução de problemas de associação

_Este artigo resume erros comuns do servidor que podem ocorrer ao gerar vinculações, juntamente com possíveis causas e formas sugeridas de resolvê-los._

## <a name="overview"></a>Visão geral

Vincular uma biblioteca Android (um arquivo **.aar** ou **um .jar**) raramente é um caso simples; geralmente requer esforço adicional para mitigar problemas que resultam das diferenças entre Java e .NET.
Esses problemas impedirão que o Xamarin.Android vincule a biblioteca do Android e se apresente como mensagens de erro no registro de compilação. Este guia fornecerá algumas dicas para solucionar problemas, listar alguns dos problemas/cenários mais comuns e fornecer soluções possíveis para vincular com sucesso a biblioteca do Android.

Ao vincular uma biblioteca Android existente, é necessário ter em mente os seguintes pontos:

- **As dependências externas para a biblioteca** &ndash; Quaisquer dependências Java exigidas pela biblioteca Android devem ser incluídas no projeto Xamarin.Android como **um ReferenceJar** ou como um **EmbeddedReferenceJar**.

- **O nível de API do Android que a biblioteca android está segmentando** &ndash; Não é possível "rebaixar" o nível da API do Android; garantir que o projeto de vinculação Xamarin.Android esteja mirando o mesmo nível de API (ou superior) que a biblioteca do Android.

- **A versão do Android JDK que foi usada para embalar a biblioteca** &ndash; android erros de encadernação podem ocorrer se a biblioteca do Android foi construída com uma versão diferente do JDK do que a usada pelo Xamarin.Android. Se possível, recompile a biblioteca android usando a mesma versão do JDK que é usada pela sua instalação do Xamarin.Android.

O primeiro passo para solucionar problemas com a vinculação de uma biblioteca Xamarin.Android é ativar a [saída de diagnóstico do MSBuild](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output).
Depois de habilitar a saída de diagnóstico, reconstrua o projeto de vinculação Xamarin.Android e examine o registro de compilação para localizar pistas sobre qual é a causa do problema.

Ele também pode ser útil para descompilar a biblioteca do Android e examinar os tipos e métodos que o Xamarin.Android está tentando vincular. Isso é coberto com mais detalhes mais tarde neste guia.

## <a name="decompiling-an-android-library"></a>Descompilação de uma Biblioteca Android

Inspecionar as classes e métodos das classes Java pode fornecer informações valiosas que ajudarão a vincular uma biblioteca.
[JD-GUI](http://jd.benow.ca/) é um utilitário gráfico que pode exibir código fonte Java a partir dos arquivos **CLASS** contidos em um JAR. Ele pode ser executado como um aplicativo autônomo ou como um plug-in para IntelliJ ou Eclipse.

Para descompilar uma biblioteca Android abra o **. Arquivo JAR** com o descompilador Java. Se a biblioteca é **uma. Arquivo AAR,** é necessário extrair o arquivo **classes.jar** do arquivo de arquivo. A seguir está uma captura de tela de exemplo do uso de JD-GUI para analisar o [Picasso](https://square.github.io/picasso/) JAR:

![Usando o Decompilador Java para analisar picasso-2.5.2.jar](troubleshooting-bindings-images/troubleshoot-bindings-01.png)

Depois de descompilar a biblioteca do Android, examine o código-fonte. De um modo geral, procure:

- **Classes que possuem características de ofuscação** &ndash; Características de classes ofuscadas incluem:

  - O nome da **$** classe inclui uma , ou seja, **uma classe$.**
  - O nome da classe está totalmente comprometido de caracteres minúsculos, ou seja, **uma classe**      

- &ndash; **Build Action** ** `import` instruções para bibliotecas não referenciadas** Identifique a biblioteca não referenciada e adicione essas dependências ao projeto de vinculação Xamarin.Android com uma Ação de Construção de **ReferênciaJar** ou **EmbedddedReferenceJar**.

> [!NOTE]
> A descompilação de uma biblioteca Java pode ser proibida ou sujeita a restrições legais com base em leis locais ou na licença sob a qual a biblioteca Java foi publicada. Se necessário, aliste os serviços de um profissional legal antes de tentar descompilar uma biblioteca Java e inspecionar o código-fonte.

## <a name="inspect-apixml"></a>Inspecione a API. Xml

Como parte da construção de um projeto de vinculação, o Xamarin.Android gerará um nome de arquivo XML **obj/Debug/api.xml**:

![Api.xml gerado em obj/Debug](troubleshooting-bindings-images/troubleshoot-bindings-02.png)

Este arquivo fornece uma lista de todas as APIs Java que o Xamarin.Android está tentando vincular. O conteúdo deste arquivo pode ajudar a identificar quaisquer tipos ou métodos ausentes, vinculação duplicada. Embora a inspeção deste arquivo seja tediosa e demorada, ela pode fornecer pistas sobre o que pode estar causando quaisquer problemas de ligação. Por exemplo, **api.xml** pode revelar que uma propriedade está retornando um tipo inapropriado, ou que existem dois tipos que compartilham o mesmo nome gerenciado.

## <a name="known-issues"></a>Problemas conhecidos

Esta seção listará algumas das mensagens de erro ou sintomas comuns que o meu ocorre ao tentar vincular uma biblioteca Android.

### <a name="problem-java-version-mismatch"></a>Problema: Java Version Mismatch

Às vezes, os tipos não serão gerados ou acidentes inesperados podem ocorrer porque você está usando uma versão mais recente ou mais antiga do Java em comparação com o que a biblioteca foi compilada. Recompile a biblioteca android com a mesma versão do JDK que seu projeto Xamarin.Android está usando.

### <a name="problem-at-least-one-java-library-is-required"></a>Problema: Pelo menos uma biblioteca Java é necessária

Você recebe o erro "pelo menos uma biblioteca Java é necessária", mesmo que um . JAR foi adicionado.

#### <a name="possible-causes"></a>Possíveis causas:

Certifique-se de que `EmbeddedJar`a ação de compilação está definida para . Uma vez que existem múltiplas ações de construção para . Os arquivos JAR `InputJar` `EmbeddedJar`(como `EmbeddedReferenceJar`, e `ReferenceJar` ), o gerador de ligação não podem adivinhar automaticamente qual usar por padrão. Para obter mais informações sobre ações de construção, consulte [Construir ações](~/android/platform/binding-java-library/index.md).

### <a name="problem-binding-tools-cannot-load-the-jar-library"></a>Problema: As ferramentas de vinculação não podem carregar o . Biblioteca JAR

O gerador da biblioteca de encadernação não consegue carregar o . Biblioteca JAR.

#### <a name="possible-causes"></a>Possíveis causas

Alguns. Bibliotecas JAR que usam ofuscação de código (via ferramentas como o Proguard) não podem ser carregadas pelas ferramentas Java. Uma vez que nossa ferramenta faz uso da reflexão Java e da biblioteca de engenharia de código de bytes ASM, essas ferramentas dependentes podem rejeitar as bibliotecas ofuscadas enquanto as ferramentas de tempo de execução do Android podem passar. A solução para isso é ligar manualmente essas bibliotecas em vez de usar o gerador de ligação.

### <a name="problem-missing-c-types-in-generated-output"></a>Problema: Faltando tipos C# na saída gerada.

A vinculação **.dll** é construída, mas perde alguns tipos de Java, ou a fonte C# gerada não é construída devido a um erro afirmando que há tipos ausentes.

#### <a name="possible-causes"></a>Possíveis causas:

Este erro pode ocorrer devido a várias razões listadas abaixo:

- A biblioteca que está sendo vinculada pode fazer referência a uma segunda biblioteca Java. Se a API pública para a biblioteca vinculada usar tipos da segunda biblioteca, você deve referenciar uma vinculação gerenciada para a segunda biblioteca também.

- É possível que uma biblioteca tenha sido injetada devido à reflexão java, semelhante à razão do erro de carga da biblioteca acima, causando o carregamento inesperado de metadados. As ferramentas do Xamarin.Android não podem resolver essa situação no momento. Nesse caso, a biblioteca deve ser ligada manualmente.

- Havia um bug no tempo de execução do .NET 4.0 que não conseguiu carregar os conjuntos quando deveria. Este problema foi corrigido no tempo de execução .NET 4.5.

- Java permite derivar uma classe pública de classe não-pública, mas isso não é suportado em .NET. Uma vez que o gerador de ligação não gera vinculações para classes não públicas, classes derivadas como essas não podem ser geradas corretamente. Para corrigir isso, remova a entrada de metadados para as classes derivadas usando o nó de remoção em **Metadata.xml**ou corrija os metadados que estão tornando a classe não pública pública. Embora esta última solução crie a vinculação para que a fonte C# seja construída, a classe não pública não deve ser usada.

  Por exemplo:

  ```xml
  <attr path="/api/package[@name='com.some.package']/class[@name='SomeClass']"
      name="visibility">public</attr>
  ```

- Ferramentas que ofuscam bibliotecas Java podem interferir com o Xamarin.Android Binding Generator e sua capacidade de gerar classes de wrapper C#. O trecho a seguir mostra como atualizar **Metadata.xml** para ofuscar um nome de classe:

  ```xml
  <attr path="/api/package[@name='{package_name}']/class[@name='{name}']"
      name="obfuscated">false</attr>
  ```

### <a name="problem-generated-c-source-does-not-build-due-to-parameter-type-mismatch"></a>Problema: Fonte C# gerada não é construída devido à incompatibilidade do tipo de parâmetro

A fonte C# gerada não é construída. Os tipos de parâmetros do método substituído não correspondem.

#### <a name="possible-causes"></a>Possíveis causas:

Xamarin.Android inclui uma variedade de campos Java que são mapeados para enums nas ligações C#. Estes podem causar incompatibilidades de tipo nas ligações geradas. Para resolver isso, as assinaturas do método criadas a partir do gerador de ligação precisam ser modificadas para usar os enums. Para obter mais informações, consulte [Corrigindo Enums](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md).

### <a name="problem-noclassdeffounderror-in-packaging"></a>Problema: NoClassDefFoundError na embalagem

`java.lang.NoClassDefFoundError`é jogado na etapa de embalagem.

#### <a name="possible-causes"></a>Possíveis causas:

A razão mais provável para este erro é que uma biblioteca Java obrigatória precisa ser adicionada ao projeto de aplicação (**.csproj**). . Os arquivos JAR não são resolvidos automaticamente. Nem sempre é gerada uma vinculação de biblioteca Java contra um conjunto de usuários que não existe no dispositivo de destino ou emulador (como o Google Maps **maps.jar**). Este não é o caso para o suporte ao projeto Android Library, como a biblioteca . Jar está embutido na biblioteca dll. Por exemplo: [Bug 4288](https://bugzilla.xamarin.com/show_bug.cgi?id=4288)

### <a name="problem-duplicate-custom-eventargs-types"></a>Problema: Duplicar tipos de EventArgs personalizados

A compilação falha devido à duplicação de tipos de EventArgs personalizados. Ocorre um erro como este:

```shell
error CS0102: The type `Com.Google.Ads.Mediation.DismissScreenEventArgs' already contains a definition for `p0'
```

#### <a name="possible-causes"></a>Possíveis causas:

Isso porque há algum conflito entre tipos de eventos que vêm de mais de um tipo de interface "ouvinte" que compartilha métodos com nomes idênticos. Por exemplo, se houver duas interfaces Java como visto `DismissScreenEventArgs` no `MediationBannerListener` exemplo `MediationInterstitialListener`abaixo, o gerador cria para ambos e, resultando no erro.

```java
// Java:
public interface MediationBannerListener {
    void onDismissScreen(MediationBannerAdapter p0);
}
public interface MediationInterstitialListener {
    void onDismissScreen(MediationInterstitialAdapter p0);
}
```

Isto é por design para que nomes longos sobre os tipos de argumento de eventos sejam evitados. Para evitar esses conflitos, é necessária alguma transformação de metadados. Editar **Transforma\Metadata.xml** e `argsType` adicionar um atributo em qualquer uma das interfaces (ou no método de interface):

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

### <a name="problem-class-does-not-implement-interface-method"></a>Problema: A classe não implementa o método de interface

Uma mensagem de erro é produzida indicando que uma classe gerada não implementa um método necessário para uma interface que a classe gerada implementa. No entanto, olhando para o código gerado, você pode ver que o método é implementado.

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

Este é um problema que ocorre com métodos Java de ligação com tipos de retorno covariantes. Neste exemplo, o `Oauth.Signpost.Http.IHttpRequest.UnWrap()` método `Java.Lang.Object`precisa retornar . No entanto, o `Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.UnWrap()` método `HttpURLConnection`tem um tipo de retorno de . Existem duas maneiras de corrigir esse problema:

- Adicionar uma declaração `HttpURLConnectionRequestAdapter` parcial de `IHttpRequest.Unwrap()`classe para e implementar explicitamente:

  ```csharp
  namespace Oauth.Signpost.Basic {
      partial class HttpURLConnectionRequestAdapter {
          Java.Lang.Object OauthSignpost.Http.IHttpRequest.Unwrap() {
              return Unwrap();
          }
      }
  }
  ```

- Remova a covariância do código C# gerado. Isso envolve adicionar a seguinte transformação a **Transforms\Metadata.xml,** o que `Java.Lang.Object`fará com que o código C# gerado tenha um tipo de retorno de :

  ```xml
  <attr
      path="/api/package[@name='oauth.signpost.basic']/class[@name='HttpURLConnectionRequestAdapter']/method[@name='unwrap']"
      name="managedReturn">Java.Lang.Object
  </attr>
  ```

### <a name="problem-name-collisions-on-inner-classes--properties"></a>Problema: Colisões de nomes em classes internas / propriedades

Visibilidade conflitante em objetos herdados.

Em Java, não é necessário que uma classe derivada tenha a mesma visibilidade que seu pai. Java vai consertar isso para você. Em C#, isso tem que ser explícito, então você precisa ter certeza de que todas as classes na hierarquia têm a visibilidade apropriada. O exemplo a seguir mostra como `com.evernote.android.job` alterar `Evernote.AndroidJob`um nome de pacote Java de :

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

### <a name="problem-a-so-library-required-by-the-binding-is-not-loading"></a>Problema: Uma **biblioteca .so** exigida pela vinculação não está carregando

Alguns projetos de vinculação também podem depender da funcionalidade em uma biblioteca **.so.** É possível que o Xamarin.Android não carregue automaticamente a biblioteca **.so.** Quando o código Java embrulhado for executado, o Xamarin.Android falhará em fazer a chamada JNI e a mensagem de erro _java.lang.UnsatisfiedLinkError: Método nativo não encontrado:_ aparecerá no logcat para fora do aplicativo.

A correção para isso é carregar manualmente a `Java.Lang.JavaSystem.LoadLibrary`biblioteca **.so** com uma chamada para . Por exemplo, assumindo que um projeto Xamarin.Android tenha compartilhado a biblioteca **libpocketsphinx_jni.so** incluído no projeto de vinculação com uma ação de compilação do **EmbeddedNativeLibrary**, o seguinte trecho (executado antes de usar a biblioteca compartilhada) carregará a **biblioteca .so:**

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="summary"></a>Resumo

Neste artigo, listamos problemas comuns de solução de problemas associados ao Java Bindings e explicamos como resolvê-los.

## <a name="related-links"></a>Links relacionados

- [Projetos de Biblioteca](https://developer.android.com/tools/projects/index.html#LibraryProjects)
- [Trabalhando com jni](~/android/platform/java-integration/working-with-jni.md)
- [Habilite a saída de diagnóstico](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)
- [Xamarin para Desenvolvedores Android](~/android/get-started/java-developers.md)
- [JD-GUI](http://jd.benow.ca/)
