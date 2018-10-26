---
title: Solução de problemas de associações
description: Este artigo resume os vários erros comuns que podem ocorrer durante a geração de associações, juntamente com as possíveis causas e as maneiras sugeridas para resolvê-los.
ms.prod: xamarin
ms.assetid: BB81FCCF-F7BF-4C78-884E-F02C49AA819A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: f54da980834b44bbca7dc8619943769f8f429a7a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115283"
---
# <a name="troubleshooting-bindings"></a>Solução de problemas de associações

_Este artigo resume os vários erros comuns que podem ocorrer durante a geração de associações, juntamente com as possíveis causas e as maneiras sugeridas para resolvê-los._


## <a name="overview"></a>Visão geral

Associação de uma biblioteca Android (um **. aar** ou um **. jar**) arquivo raramente é uma questão simples; geralmente requer esforço adicional para atenuar os problemas resultantes de diferenças entre Java e .NET.
Esses problemas impedirá que o xamarin. Android associando a biblioteca do Android e se apresentam como mensagens de erro no log de compilação. Este guia fornece algumas dicas para solução de problemas, listam alguns dos problemas/cenários mais comuns e fornecem soluções possíveis para associação com êxito a biblioteca Android.

Ao associar uma biblioteca Android existente, é necessário ter em mente os seguintes pontos:

- **As dependências externas para a biblioteca** &ndash; dependências de Java Any exigidas pela biblioteca do Android devem ser incluídas no projeto xamarin. Android como um **ReferenceJar** ou como um  **EmbeddedReferenceJar**.

- **O nível de API do Android que a biblioteca do Android está querendo** &ndash; não é possível fazer "downgrade" o nível de API do Android; Certifique-se de que o projeto de associação do xamarin. Android está direcionando a mesma API nível (ou superior) como a biblioteca Android.

- **A versão do JDK Android que foi usado para empacotar a biblioteca do Android** &ndash; erros de associação podem ocorrer se a biblioteca Android foi criada com uma versão diferente do JDK em uso pelo xamarin. Android. Se possível, recompile a biblioteca do Android usando a mesma versão do JDK que é usado pela sua instalação do xamarin. Android.

A primeira etapa para solucionar problemas com a associação de uma biblioteca do xamarin. Android é para habilitar [resultados de diagnóstico do MSBuild](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output).
Depois de habilitar a saída de diagnóstico, recompile o projeto de associação do xamarin. Android e examine o log de compilação para localizar pistas sobre o que é a causa do problema.

Também pode ser útil descompilar a biblioteca do Android e examinar os tipos e métodos que o xamarin. Android está tentando associar. Isso é abordado em mais detalhes posteriormente neste guia.


## <a name="decompiling-an-android-library"></a>Descompilação uma biblioteca Android

Inspecionar as classes e métodos das classes Java pode fornecer informações valiosas que auxiliará na associação a uma biblioteca.
[GUI JD](http://jd.benow.ca/) é um utilitário gráfico que pode exibir o código-fonte Java do **classe** arquivos contidos em um JAR. Ele pode ser executado como um aplicativo autônomo ou como um plug-in IntelliJ ou Eclipse.

A descompilação abrir uma biblioteca Android o **. JAR** arquivo com o descompilador de Java. Se a biblioteca é um **. AAR** arquivo, é necessário extrair o arquivo **classes.jar** do arquivo morto. A seguir está uma captura de tela de exemplo de como usar a GUI do JD para analisar a [Picasso](http://square.github.io/picasso/) JAR:

![Usando o Java descompilador para analisar picasso 2.5.2.jar](troubleshooting-bindings-images/troubleshoot-bindings-01.png)

Depois de você ter descompilado a biblioteca do Android, examine o código-fonte. Em termos gerais, procure:

- **Classes que têm características de ofuscação** &ndash; características das classes ofuscadas incluem:

    - O nome de classe inclui uma **$**, ou seja, **um. ' $Class**
    - O nome de classe seja comprometido inteiramente de caracteres em letras minúsculas, ou seja, **a.class**      

- **`import` instruções para as bibliotecas não referenciadas** &ndash; identificar a biblioteca não referenciada e adicione essas dependências ao projeto xamarin. Android associação com um **Build Action** de **ReferenceJar**  ou **EmbedddedReferenceJar**.

> [!NOTE]
> Descompilação uma biblioteca Java pode ser proibido ou sujeitos às restrições legais com base em leis locais ou a licença sob as quais a biblioteca Java foi publicada. Se necessário, inscrever-se os serviços de um profissional legal antes de tentar descompilar uma biblioteca Java e inspecione o código-fonte.


## <a name="inspect-apixml"></a>Inspecione a API. XML

Como parte da criação de um projeto de associação, o xamarin. Android irá gerar um nome de arquivo XML **obj/Debug/api.xml**:

![Api.xml gerado em obj/depurar](troubleshooting-bindings-images/troubleshoot-bindings-02.png)

Esse arquivo fornece uma lista de todas as APIs de Java que o xamarin. Android está tentando associar. O conteúdo desse arquivo pode ajudar a identificar todos os tipos ou métodos ausente, duplicar associação. Embora a inspeção deste arquivo é tedioso e demorado, ele pode fornecer para encontrar pistas sobre o que pode estar causando problemas de associação. Por exemplo, **api.xml** poderia revelar que uma propriedade está retornando um tipo inadequado ou que há dois tipos que compartilham o mesmo nome gerenciado.


## <a name="known-issues"></a>Problemas Conhecidos

Esta seção lista alguns dos sintomas ou mensagens de erro comuns que meu ocorrer durante a tentativa de associar uma biblioteca Android.


### <a name="problem-java-version-mismatch"></a>Problema: Incompatibilidade de versão de Java

Às vezes, tipos de não ser gerados ou falhas inesperadas podem ocorrer porque você estiver usando uma versão mais antiga ou mais recente do Java em comparação comparada o que a biblioteca foi compilada com. Recompile a biblioteca Android com a mesma versão do JDK que seu projeto xamarin. Android estiver usando.


### <a name="problem-at-least-one-java-library-is-required"></a>Problema: pelo menos uma biblioteca Java é necessária

Você receberá o erro "pelo menos uma biblioteca Java é necessária,", embora um. JAR foi adicionado.

#### <a name="possible-causes"></a>Possíveis causas:

Verifique se a ação de compilação é definida como `EmbeddedJar`. Uma vez que há várias ações de build para. Arquivos JAR (como `InputJar`, `EmbeddedJar`, `ReferenceJar` e `EmbeddedReferenceJar`), o gerador de associação não é possível adivinhar qual delas usar por padrão. Para obter mais informações sobre ações de build, consulte [ações de Build](~/android/platform/binding-java-library/index.md).


### <a name="problem-binding-tools-cannot-load-the-jar-library"></a>Problema: Ferramentas de ligação não é possível carregar o. Biblioteca JAR

O gerador de biblioteca de ligação Falha ao carregar o. Biblioteca JAR.

#### <a name="possible-causes"></a>Possíveis causas

Alguns. Bibliotecas JAR que usam a ofuscação de código (por meio de ferramentas como o Proguard) não podem ser carregadas pelas ferramentas do Java. Uma vez que a nossa ferramenta faz uso da reflexão do Java e o código de bytes do ASM biblioteca de engenharia, essas ferramentas dependentes podem rejeitar as bibliotecas ofuscadas enquanto as ferramentas de tempo de execução Android podem passar. A solução para isso é associar manualmente essas bibliotecas em vez de usar o gerador de associação.



### <a name="problem-missing-c-types-in-generated-output"></a>Problema: Ausente C# tipos de saída gerada.

A associação **. dll** builds, mas perder alguns tipos de Java ou gerado C# fonte não compila devido a um erro informando que há tipos ausentes.

#### <a name="possible-causes"></a>Possíveis causas:

Esse erro pode ocorrer por várias razões, conforme listado abaixo:

-   A biblioteca que está sendo associada pode fazer referência a uma biblioteca Java segundo. Se a API pública para a biblioteca associada usa tipos da biblioteca de segundo, você deve fazer referência a uma associação de gerenciado para a segunda biblioteca.

-   É possível que uma biblioteca foi inserida devido a reflexão do Java, semelhante ao motivo para o erro de carregamento de biblioteca acima, fazendo com que o carregamento inesperado de metadados. Ferramentas do xamarin. Android no momento, não é possível resolver essa situação. Nesse caso, a biblioteca deve ser vinculada manualmente.

-   Havia um bug no tempo de execução do .NET 4.0 que falharam ao carregar assemblies quando ele deve ter. Esse problema foi corrigido no tempo de execução do .NET 4.5.

-   Java permite derivando uma classe pública de classe não pública, mas isso não é suportado no .NET. Uma vez que o gerador de associação não gere associações para classes não-públicos, classes derivadas, como eles não podem ser gerados corretamente. Para corrigir esse problema, remova a entrada dessas classes derivadas usando o remover nó em metadados **Metadata**, ou corrija os metadados que está fazendo a classe não pública público. Embora a última solução será criar uma associação para que o C# compilará o código-fonte, a classe não público não deve ser usada.

    Por exemplo:

    ```xml
    <attr path="/api/package[@name='com.some.package']/class[@name='SomeClass']"
        name="visibility">public</attr>
    ```

-   Ferramentas de que o ofuscar bibliotecas Java podem interferir com o gerador de associação do xamarin. Android e sua capacidade de gerar C# classes de wrapper. O trecho a seguir mostra como atualizar **Metadata** para unobfuscate um nome de classe:

    ```xml
    <attr path="/api/package[@name='{package_name}']/class[@name='{name}']"
        name="obfuscated">false</attr>
    ```

### <a name="problem-generated-c-source-does-not-build-due-to-parameter-type-mismatch"></a>Problema: Gerado C# código-fonte não será compilado devido à incompatibilidade de tipo de parâmetro

Gerado C# código-fonte não será compilado. Substituído o parâmetro do método de tipos não coincidem.

#### <a name="possible-causes"></a>Possíveis causas:

Xamarin. Android inclui uma variedade de campos de Java que são mapeados para enums no C# associações. Eles podem causar incompatibilidades de tipo nas associações geradas. Para resolver esse problema, as assinaturas de método criadas pelo gerador de associação precisam ser modificados para usar as enums. Para obter mais informações, consulte [corrigindo Enums](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md).

### <a name="problem-noclassdeffounderror-in-packaging"></a>Problema: NoClassDefFoundError no empacotamento

`java.lang.NoClassDefFoundError` será gerada na etapa de empacotamento.

#### <a name="possible-causes"></a>Possíveis causas:

O motivo mais provável desse erro é que uma biblioteca Java obrigatória precisa ser adicionado ao projeto de aplicativo (**. csproj**). . Arquivos JAR não são resolvidos automaticamente. Uma associação de biblioteca Java não é sempre gerada em relação a um assembly de usuário que não existe no emulador ou dispositivo de destino (como o Google Maps **maps.jar**). Isso não é o caso de suporte de projeto de biblioteca do Android, como a biblioteca. JAR é incorporado na dll de biblioteca. Por exemplo: [4288 Bug](https://bugzilla.xamarin.com/show_bug.cgi?id=4288)

### <a name="problem-duplicate-custom-eventargs-types"></a>Problema: Duplicar tipos personalizados de EventArgs

O build falha devido a tipos personalizados de EventArgs duplicados. Ocorre um erro como este:

```shell
error CS0102: The type `Com.Google.Ads.Mediation.DismissScreenEventArgs' already contains a definition for `p0'
```

#### <a name="possible-causes"></a>Possíveis causas:

Isso ocorre porque há algum conflito entre os tipos de eventos que vêm de mais de um tipo de "ouvinte" de interface que compartilha os métodos com nomes idênticos. Por exemplo, se houver duas interfaces de Java como visto no exemplo a seguir, o gerador cria `DismissScreenEventArgs` para ambos `MediationBannerListener` e `MediationInterstitialListener`, resultando em erro.

```java
// Java:
public interface MediationBannerListener {
    void onDismissScreen(MediationBannerAdapter p0);
}
public interface MediationInterstitialListener {
    void onDismissScreen(MediationInterstitialAdapter p0);
}
```

Isso ocorre por design, de modo que os nomes longos em tipos de argumento de evento são evitados. Para evitar esses conflitos, é necessária alguma transformação de metadados. Edite [ **Transforms\Metadata.xml** ](https://github.com/xamarin/monodroid-samples/blob/master/AdMob/AdMob/Transforms/Metadata.xml) e adicione um `argsType` atributo em qualquer uma das interfaces (ou no método de interface):

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

Uma mensagem de erro é gerada, indicando que uma classe gerada não implementa um método que é necessário para uma interface que implementa a classe gerada. No entanto, observando o código gerado, você pode ver que o método é implementado.

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

Isso é um problema que ocorre com métodos Java de associação com tipos de retorno covariante. Neste exemplo, o método `Oauth.Signpost.Http.IHttpRequest.UnWrap()` precisa retornar `Java.Lang.Object`. No entanto, o método `Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.UnWrap()` tem um tipo de retorno `HttpURLConnection`. Há duas maneiras de corrigir esse problema:

-   Adicionar uma declaração de classe parcial para `HttpURLConnectionRequestAdapter` e implemente explicitamente `IHttpRequest.Unwrap()`:

    ```csharp
    namespace Oauth.Signpost.Basic {
        partial class HttpURLConnectionRequestAdapter {
            Java.Lang.Object OauthSignpost.Http.IHttpRequest.Unwrap() {
                return Unwrap();
            }
        }
    }
    ```

-   Remover a covariância de gerado C# código. Isso envolve adicionar a seguinte transformação **Transforms\Metadata.xml** que fará com que o gerado C# código para ter um tipo de retorno `Java.Lang.Object`:

    ```xml
    <attr
        path="/api/package[@name='oauth.signpost.basic']/class[@name='HttpURLConnectionRequestAdapter']/method[@name='unwrap']"
        name="managedReturn">Java.Lang.Object
    </attr>
    ```

### <a name="problem-name-collisions-on-inner-classes--properties"></a>Problema: Nomeie colisões em Classes internas / propriedades

Visibilidade conflitante em objetos herdados.

Em Java, não é necessário que uma classe derivada tem a mesma visibilidade que seu pai. Java apenas será corrigir isso para você. No C#, que deve ser explícita, portanto, você precisará certificar-se todas as classes na hierarquia tem a visibilidade apropriada. O exemplo a seguir mostra como alterar um nome de pacote do Java `com.evernote.android.job` para `Evernote.AndroidJob`:

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

### <a name="problem-a-so-library-required-by-the-binding-is-not-loading"></a>Problema: Um **. SO** biblioteca exigida pela associação não é carregar

Alguns projetos de associação também podem depender de funcionalidade em um **. SO** biblioteca. É possível que o xamarin. Android não será carregado automaticamente a **. SO** biblioteca. Quando executa o código encapsulado do Java, xamarin. Android não consegue fazer a chamada JNI e a mensagem de erro _java.lang.UnsatisfiedLinkError: método nativo não encontrado:_ será exibido o logcat-out para o aplicativo.

A correção para isso é para carregar manualmente os **. SO** biblioteca com uma chamada para `Java.Lang.JavaSystem.LoadLibrary`. Por exemplo, supondo que um projeto xamarin. Android tem biblioteca compartilhada **libpocketsphinx_jni.so** incluído no projeto de associação com uma ação de build **EmbeddedNativeLibrary**, o trecho a seguir (executado antes de usar a biblioteca compartilhada) carregará o **. SO** biblioteca:

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="summary"></a>Resumo

Neste artigo, estamos listados problemas comuns de solução de problemas associados com associações de Java e explicou como resolvê-los.


## <a name="related-links"></a>Links relacionados

- [Projetos de biblioteca](http://developer.android.com/tools/projects/index.html#LibraryProjects)
- [Trabalhar com JNI](~/android/platform/java-integration/working-with-jni.md)
- [Habilitar a saída de diagnóstico](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)
- [Xamarin para desenvolvedores do Android](~/android/get-started/java-developers.md)
- [JD-GUI](http://jd.benow.ca/)
