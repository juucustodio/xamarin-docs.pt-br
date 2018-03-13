---
title: "Solucionando problemas de associações"
description: "Este artigo resume vários erros comuns que podem ocorrer durante a geração de associações, junto com as possíveis causas e as maneiras sugeridas para resolvê-los."
ms.topic: article
ms.prod: xamarin
ms.assetid: BB81FCCF-F7BF-4C78-884E-F02C49AA819A
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 6d31e2a22c63f8d46893dd1928b561e1a06b19b4
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="troubleshooting-bindings"></a>Solucionando problemas de associações

_Este artigo resume vários erros comuns que podem ocorrer durante a geração de associações, junto com as possíveis causas e as maneiras sugeridas para resolvê-los._


## <a name="overview"></a>Visão geral

Associação de uma biblioteca Android (um **. aar** ou um **. jar**) arquivo raramente é uma simples questão; normalmente requer esforço adicional para reduzir problemas resultantes de diferenças entre o Java e .NET.
Esses problemas impedirá xamarin de associação de biblioteca do Android e se apresentam como mensagens de erro no log de compilação. Este guia fornece algumas dicas para solução de problemas, lista alguns dos problemas mais comuns/cenários e fornecer soluções possíveis para associação com êxito a biblioteca Android.

Ao associar uma biblioteca Android existente, é necessário ter em mente os seguintes pontos:

- **As dependências externas para a biblioteca** &ndash; dependências de qualquer Java exigidas pela biblioteca Android devem ser incluídas no projeto xamarin como um **ReferenceJar** ou como um  **EmbeddedReferenceJar**.

- **O nível de API do Android que a biblioteca Android está direcionando** &ndash; não é possível fazer "downgrade" nível de API do Android; Certifique-se de que o projeto de associação xamarin está direcionando a mesma API nível (ou superior) como a biblioteca Android.

- **A versão do JDK Android que foi usado para empacotar a biblioteca Android** &ndash; erros de associação podem ocorrer se a biblioteca Android criada com uma versão diferente do JDK que o uso com o xamarin. Se possível, compile novamente a biblioteca do Android usando a mesma versão do JDK que é usado por sua instalação do xamarin.

É a primeira etapa para solução de problemas com a associação de uma biblioteca do xamarin habilitar [saída de diagnóstico MSBuild](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output).
Depois de habilitar a saída de diagnóstico, recompile o projeto de associação xamarin e examine o log de compilação para localizar dicas sobre a causa do problema.

Também podem ser úteis descompilar biblioteca do Android e examinar os tipos e métodos xamarin está tentando associar. Isso é abordado em mais detalhes posteriormente neste guia.


## <a name="decompiling-an-android-library"></a>Decompilar uma biblioteca Android

Inspecionar as classes e métodos das classes Java pode fornecer informações valiosas que ajudarão a associação de uma biblioteca.
[GUI JD](http://jd.benow.ca/) é um utilitário gráfico que pode exibir o código-fonte Java o **classe** arquivos contidos em um JAR. Ele pode ser executado como um aplicativo autônomo ou como um plug-in IntelliJ ou Eclipse.

Descompilar abrir uma biblioteca Android a **. JAR** arquivo com o descompilador Java. Se a biblioteca é um **. AAR** arquivo, é necessário extrair o arquivo **classes.jar** do arquivo morto. A seguir está uma captura de tela de exemplo do uso de GUI JD para analisar o [Picasso](http://square.github.io/picasso/) JAR:

![Usando o Java descompilador para analisar picasso 2.5.2.jar](troubleshooting-bindings-images/troubleshoot-bindings-01.png)

Depois de você ter descompilado biblioteca do Android, examine o código-fonte. Em geral, procure por:

- **As classes que têm características de ofuscação** &ndash; características das classes ofuscados incluem:

    - O nome de classe inclui um  **$** , ou seja, **um. ' $Class**
    - O nome da classe é totalmente comprometido de letras minúsculas, ou seja, **a.class**      

- **`import` instruções para bibliotecas não referenciadas** &ndash; identificar a biblioteca não referenciada e adicionar essas dependências para o projeto de associação xamarin com um **ação de compilação** de **ReferenceJar**  ou **EmbedddedReferenceJar**.

> [!NOTE]
> Decompilar uma biblioteca Java pode ser proibido ou sujeito a restrições legais com base em leis locais ou a licença sob a qual a biblioteca Java foi publicada. Se necessário, inscrever-se os serviços de um profissional legal antes de tentar descompilar uma biblioteca Java e inspecione o código-fonte.


## <a name="inspect-apixml"></a>Inspecione a API. XML

Como parte da criação de um projeto de associação, xamarin irá gerar um nome de arquivo XML **obj/Debug/api.xml**:

![Api.xml gerado em obj/Debug](troubleshooting-bindings-images/troubleshoot-bindings-02.png)

Esse arquivo fornece uma lista de todas as APIs de Java que xamarin está tentando associar. O conteúdo deste arquivo pode ajudar a identificar tipos ou métodos ausentes, associação duplicada. Embora a inspeção deste arquivo é entediante e demorado, ele pode fornecer para encontrar pistas sobre o que pode estar causando problemas de associação. Por exemplo, **api.xml** poderia revelar que uma propriedade está retornando um tipo inadequado ou que há dois tipos que compartilham o mesmo nome gerenciado.


## <a name="known-issues"></a>Problemas Conhecidos

Esta seção lista alguns dos sintomas ou mensagens de erro comuns que meu ocorrer ao tentar vincular uma biblioteca Android.


### <a name="problem-java-version-mismatch"></a>Problema: Incompatibilidade de versão de Java

Às vezes, tipos não serão gerados ou falhas inesperadas podem ocorrer porque você estiver usando uma versão mais antiga ou mais recente do Java em comparação com o que a biblioteca foi compilada. Recompile a biblioteca Android com a mesma versão do JDK que seu projeto xamarin está usando.


### <a name="problem-at-least-one-java-library-is-required"></a>Problema: pelo menos uma biblioteca de Java é necessária

Você receberá o erro "pelo menos uma biblioteca de Java é necessária,", embora um. JAR foi adicionado.

#### <a name="possible-causes"></a>Possíveis causas:

Verifique se a ação de compilação é definida como `EmbeddedJar`. Como existem várias ações de compilação para. Arquivos JAR (como `InputJar`, `EmbeddedJar`, `ReferenceJar` e `EmbeddedReferenceJar`), o gerador de associação não é possível adivinhar qual delas usar por padrão. Para obter mais informações sobre ações de compilação, consulte [criar ações](~/android/platform/binding-java-library/index.md).


### <a name="problem-binding-tools-cannot-load-the-jar-library"></a>Problema: Ferramentas de associação não é possível carregar o. Biblioteca JAR

O gerador de biblioteca de ligação Falha ao carregar o. Biblioteca JAR.

#### <a name="possible-causes"></a>Possíveis causas

Alguns. Não não possível carregar bibliotecas JAR que usam ofuscação de código (por meio de ferramentas como o Proguard) pelas ferramentas de Java. Desde que a nossa ferramenta faz uso da reflexão Java e o código de bytes do ASM biblioteca de engenharia, essas ferramentas dependentes podem rejeitar as bibliotecas ofuscadas enquanto as ferramentas de tempo de execução Android podem passar. A solução para isso é associar mão essas bibliotecas em vez de usar o gerador de associação.



### <a name="problem-missing-c-types-in-generated-output"></a>Problema: Faltando tipos c# na saída gerada.

A associação **. dll** compilações erros mas alguns tipos de Java ou origem c# gerado não compila devido a um erro informando que há tipos ausentes.

#### <a name="possible-causes"></a>Possíveis causas:

Esse erro pode ocorrer por vários motivos, como listado abaixo:

-   A biblioteca que está sendo associada pode fazer referência a uma biblioteca Java segundo. Se a API pública para a biblioteca associada usa tipos da biblioteca de segundo, você deve fazer referência a uma associação de gerenciado para a segunda biblioteca.

-   É possível que uma biblioteca foi inserida devido a reflexão de Java, como o motivo do erro de carregamento de biblioteca acima, fazendo com que o carregamento inesperado de metadados. Ferramentas do xamarin atualmente não é possível resolver essa situação. Nesse caso, a biblioteca deve ser associada manualmente.

-   Houve um erro em tempo de execução do .NET 4.0 que falha ao carregar assemblies quando ele deve ter. Esse problema foi corrigido no tempo de execução do .NET 4.5.

-   Java permite derivar uma classe pública da classe de não-públicos, mas isso não tem suporte no .NET. Desde que o gerador de associação não gerar associações para classes não-públicos, classes derivadas, como eles não podem ser gerados corretamente. Para corrigir isso, remova a entrada de metadados para as classes derivadas usando o remover nó em **Metadata.xml**, ou corrija os metadados que está fazendo a classe não-públicos pública. Embora a última solução irá criar a associação para que o código-fonte c# criará, a classe não-públicos não deve ser usada.

    Por exemplo:

    ```xml
    <attr path="/api/package[@name='com.some.package']/class[@name='SomeClass']"
        name="visibility">public</attr>
    ```

-   Ferramentas que ofuscar bibliotecas Java podem interferir com o gerador de associação xamarin e sua capacidade de gerar classes de wrapper do c#. O trecho a seguir mostra como atualizar **Metadata.xml** para unobfuscate um nome de classe:

    ```xml
    <attr path="/api/package[@name='{package_name}']/class[@name='{name}']"
        name="obfuscated">false</attr>
    ```

### <a name="problem-generated-c-source-does-not-build-due-to-parameter-type-mismatch"></a>Problema: Origem gerado c# não compila devido a incompatibilidade de tipo de parâmetro

A fonte do c# gerada não compila. Substituído o parâmetro do método tipos não correspondem.

#### <a name="possible-causes"></a>Possíveis causas:

Xamarin inclui uma variedade de campos de Java que são mapeados para enums nas associações de c#. Eles podem causar incompatibilidades de tipo nas associações geradas. Para resolver esse problema, as assinaturas de método criadas a partir do gerador de associação precisam ser modificado para usar as enums. Para mais imformation, consulte [corrigindo Enums](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md).

### <a name="problem-noclassdeffounderror-in-packaging"></a>Problema: NoClassDefFoundError no empacotamento

`java.lang.NoClassDefFoundError` é gerado na etapa de empacotamento.

#### <a name="possible-causes"></a>Possíveis causas:

A razão mais provável para este erro é que uma biblioteca Java obrigatória precisa ser adicionado ao projeto de aplicativo (**. csproj**). . Arquivos JAR não são resolvidos automaticamente. Uma associação de biblioteca Java não é gerada sempre em relação a um assembly de usuário que não existe no emulador ou dispositivo de destino (como Google Maps **maps.jar**). Isso não é o caso de suporte do projeto de biblioteca Android, como a biblioteca. JAR é incorporado na dll de biblioteca. Por exemplo: [4288 Bug](https://bugzilla.xamarin.com/show_bug.cgi?id=4288)

### <a name="problem-duplicate-custom-eventargs-types"></a>Problema: Duplicar tipos personalizados de EventArgs

O build falha devido a tipos de EventArgs personalizados duplicados. Ocorre um erro como este:

```shell
error CS0102: The type `Com.Google.Ads.Mediation.DismissScreenEventArgs' already contains a definition for `p0'
```

#### <a name="possible-causes"></a>Possíveis causas:

Isso ocorre porque há algum conflito entre os tipos de eventos que vêm mais de um tipo de interface "escuta" que compartilha métodos com nomes idênticos. Por exemplo, se houver duas interfaces de Java como visto no exemplo a seguir, o gerador cria `DismissScreenEventArgs` para ambos `MediationBannerListener` e `MediationInterstitialListener`, resultando em erro.

```java
// Java:
public interface MediationBannerListener {
    void onDismissScreen(MediationBannerAdapter p0);
}
public interface MediationInterstitialListener {
    void onDismissScreen(MediationInterstitialAdapter p0);
}
```

Isso ocorre por design, para que nomes longos em tipos de argumento de evento são evitados. Para evitar esses conflitos, é necessária alguma transformação de metadados. Editar [ **Transforms\Metadata.xml** ](https://github.com/xamarin/monodroid-samples/blob/master/AdMob/AdMob/Transforms/Metadata.xml) e adicione um `argsType` atributo em uma das interfaces (ou no método de interface):

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

### <a name="problem-class-does-not-implement-interface-method"></a>Problema: A classe não implementa método de interface

Uma mensagem de erro é gerada, indicando que uma classe gerada não implementa um método que é necessário para uma interface que implementa a classe gerada. No entanto, examinar o código gerado, você pode ver que o método é implementado.

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

Este é um problema que ocorre com a associação de métodos de Java com tipos de retorno covariante. Neste exemplo, o método `Oauth.Signpost.Http.IHttpRequest.UnWrap()` precisa retornar `Java.Lang.Object`. No entanto, o método `Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.UnWrap()` tem um tipo de retorno `HttpURLConnection`. Há duas maneiras de corrigir esse problema:

-   Adicione uma declaração de classe parcial para `HttpURLConnectionRequestAdapter` e implementar explicitamente `IHttpRequest.Unwrap()`:

    ```csharp
    namespace Oauth.Signpost.Basic {
        partial class HttpURLConnectionRequestAdapter {
            Java.Lang.Object OauthSignpost.Http.IHttpRequest.Unwrap() {
                return Unwrap();
            }
        }
    }
    ```

-   Remova a covariância de código c# gerado. Isso envolve a adição a seguinte transformação **Transforms\Metadata.xml** que fará com que o código c# gerado para ter um tipo de retorno `Java.Lang.Object`:

    ```xml
    <attr
        path="/api/package[@name='oauth.signpost.basic']/class[@name='HttpURLConnectionRequestAdapter']/method[@name='unwrap']"
        name="managedReturn">Java.Lang.Object
    </attr>
    ```

### <a name="problem-name-collisions-on-inner-classes--properties"></a>Problema: Nome colisões Classes internas / propriedades

Visibilidade conflitante em objetos herdados.

Em Java, não é necessário que uma classe derivada tem a mesma visibilidade que seu pai. Java apenas será corrigir isso para você. Em c#, que deve ser explícita, portanto você precisa verificar se todas as classes na hierarquia de tem a visibilidade apropriada. O exemplo a seguir mostra como alterar um nome de pacote Java de `com.evernote.android.job` para `Evernote.AndroidJob`:

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

### <a name="problem-a-so-library-required-by-the-binding-is-not-loading"></a>Problema: Um **. SO** biblioteca necessária para a associação não é carregar

Alguns projetos de associação podem também dependem da funcionalidade em um **. SO** biblioteca. É possível que o xamarin não será carregado automaticamente o **. SO** biblioteca. Quando é executado, o código Java encapsulado xamarin falhará fazer a chamada JNI e a mensagem de erro _java.lang.UnsatisfiedLinkError: método nativo não encontrado:_ aparecerão no logcat fora do aplicativo.

A solução é carregar manualmente o **. SO** biblioteca com uma chamada para `Java.Lang.JavaSystem.LoadLibrary`. Por exemplo, supondo que um projeto xamarin compartilhou biblioteca **libpocketsphinx_jni.so** incluído no projeto associação com uma ação de compilação de **EmbeddedNativeLibrary**, o seguinte trecho de código (executado antes de usar a biblioteca compartilhada) carregará o **. SO** biblioteca:

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="summary"></a>Resumo

Neste artigo, vamos listados problemas comuns associados com associações de Java e explicou como resolvê-los.


## <a name="related-links"></a>Links relacionados

- [Projetos de biblioteca](http://developer.android.com/tools/projects/index.html#LibraryProjects)
- [Trabalhando com JNI](~/android/platform/java-integration/working-with-jni.md)
- [Habilitar a saída de diagnóstico](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)
- [Xamarin para desenvolvedores do Android](~/android/get-started/java-developers.md)
- [JD-GUI](http://jd.benow.ca/)
