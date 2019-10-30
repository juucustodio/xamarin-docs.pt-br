---
title: Associação de uma biblioteca Java
description: A Comunidade do Android tem muitas bibliotecas Java que você talvez queira usar em seu aplicativo; Este guia explica como incorporar bibliotecas Java em seu aplicativo Xamarin. Android criando uma biblioteca de associações.
ms.prod: xamarin
ms.assetid: B39FF1D5-69C3-8A76-D268-C227A23C9485
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/01/2017
ms.openlocfilehash: d40a23076ec8f405e57ec40de47ec9ad2261d85d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027600"
---
# <a name="binding-a-java-library"></a>Associação de uma biblioteca Java

_A Comunidade do Android tem muitas bibliotecas Java que você talvez queira usar em seu aplicativo; Este guia explica como incorporar bibliotecas Java em seu aplicativo Xamarin. Android criando uma biblioteca de associações._

## <a name="overview"></a>Visão Geral

O ecossistema de biblioteca de terceiros para Android é maciço. Por isso, geralmente faz sentido usar uma biblioteca Android existente do que criar uma nova. O Xamarin. Android oferece duas maneiras de usar essas bibliotecas:

- Crie uma *biblioteca de associações* que encapsula automaticamente a biblioteca com C# wrappers para que você possa invocar o código C# Java por meio de chamadas.

- Use a *interface nativa do Java* (*JNI*) para invocar chamadas diretamente no código da biblioteca Java. JNI é uma estrutura de programação que permite que o código Java chame e seja chamado por aplicativos nativos ou bibliotecas.

Este guia explica a primeira opção: como criar uma *biblioteca de associações* que encapsula uma ou mais bibliotecas Java existentes em um assembly ao qual você pode vincular em seu aplicativo. Para obter mais informações sobre como usar o JNI, consulte [trabalhando com JNI](~/android/platform/java-integration/working-with-jni.md).

O Xamarin. Android implementa associações usando o*MCW*( *wrappers callable) gerenciados* . A MCW é uma ponte JNI que é usada quando o código gerenciado precisa invocar o código Java. Wrappers callable gerenciados também oferecem suporte para a subclasse de tipos Java e para substituir métodos virtuais em tipos Java. Da mesma forma, sempre que o código do tempo de execução do Android (ART) quiser invocar o código gerenciado, ele faz isso por meio de outra ponte JNI conhecida como ACW (Android callable wrappers). Essa [arquitetura](~/android/internals/architecture.md) é ilustrada no diagrama a seguir:

[arquitetura de ponte ![Android JNI](images/architecture.png)](images/architecture.png#lightbox)

Uma biblioteca de associações é um assembly que contém wrappers callable gerenciados para tipos Java. Por exemplo, aqui está um tipo Java, `MyClass`, que desejamos encapsular uma biblioteca de associações:

```java
package com.xamarin.mycode;

public class MyClass
{
    public String myMethod (int i) { ... }
}
```

Depois de gerarmos uma biblioteca de associações para o **. jar** que contém `MyClass`, podemos instanciá-lo e chamar métodos nele C#:

```csharp
var instance = new MyClass ();

string result = instance.MyMethod (42);
```

Para criar essa biblioteca de associações, use o modelo de biblioteca de *associações Java* do Xamarin. Android. O projeto de associação resultante cria um assembly .NET com as classes MCW, os arquivos **. jar** e os recursos para projetos de biblioteca do Android inseridos nele. Você também pode criar bibliotecas de associações para o arquivo morto do Android (. AAR) arquivos e projetos de biblioteca do Android do eclipse. Ao referenciar o assembly de DLL de biblioteca de associações resultante, você pode reutilizar uma biblioteca Java existente em seu projeto Xamarin. Android.

Ao referenciar tipos em sua biblioteca de associação, você deve usar o namespace da sua biblioteca de associação. Normalmente, você adiciona uma diretiva `using` na parte superior de seus C# arquivos de origem que é a versão do namespace do .net do nome do pacote Java. Por exemplo, se o nome do pacote Java para seu Bound **. jar** for o seguinte:

```csharp
com.company.package
```

Em seguida, você colocaria a seguinte instrução `using` na parte superior C# dos seus arquivos de origem para acessar tipos no arquivo **. jar** associado:

```csharp
using Com.Company.Package;
```

Ao associar uma biblioteca Android existente, é necessário manter os seguintes pontos em mente:

- **Há alguma dependência externa para a biblioteca?** &ndash; qualquer dependência de Java exigida pela biblioteca do Android deve ser incluída no projeto Xamarin. Android como um **ReferenceJar** ou como um **EmbeddedReferenceJar**. Todos os assemblies nativos devem ser adicionados ao projeto de associação como um **EmbeddedNativeLibrary**.  

- **Qual versão da API do Android é o destino da biblioteca do Android?** &ndash; não é possível "fazer downgrade" do nível da API do Android; Verifique se o projeto de associação Xamarin. Android está direcionado ao mesmo nível de API (ou superior) que a biblioteca do Android.

- **Qual versão do JDK foi usada para compilar a biblioteca?** &ndash; erros de associação podem ocorrer se a biblioteca do Android foi criada com uma versão diferente do JDK do que em uso pelo Xamarin. Android. Se possível, recompile a biblioteca do Android usando a mesma versão do JDK que é usada pela sua instalação do Xamarin. Android.

## <a name="build-actions"></a>Ações de Build

Ao criar uma biblioteca de associações, você define as *ações de compilação* no **. jar** ou. Os arquivos AAR que você incorpora em seu projeto de biblioteca de associações &ndash; cada ação de compilação determina como o **. jar** ou. O arquivo AAR será inserido em (ou referenciado por) sua biblioteca de associações. A lista a seguir resume essas ações de compilação:

- `EmbeddedJar` &ndash; insere o **. jar** na DLL da biblioteca de associações resultante como um recurso incorporado. Essa é a ação de Build mais simples e mais comumente usada. Use essa opção quando desejar que o **. jar** seja compilado automaticamente no código de bytes e empacotado na biblioteca de associações.

- `InputJar` &ndash; não insere o **. jar** na biblioteca de associações resultante. DLL. Sua biblioteca de associações. A DLL terá uma dependência nesse **. jar** no tempo de execução. Use essa opção quando não desejar incluir o **. jar** em sua biblioteca de associações (por exemplo, por motivos de licenciamento). Se você usar essa opção, deverá garantir que o Input **. jar** esteja disponível no dispositivo que executa seu aplicativo.

- `LibraryProjectZip` &ndash; insere um. AAR o arquivo na biblioteca de associações resultante. DLL. Isso é semelhante a EmbeddedJar, exceto pelo fato de que você pode acessar recursos (bem como código) na associação. Arquivo AAR. Use essa opção quando desejar inserir um. AAR na biblioteca de associações.

- `ReferenceJar` &ndash; especifica um Reference **. jar**: uma Reference **. jar** é um **. jar** que um de seus **. jar** ou. Os arquivos AAR dependem de. Esse Reference **. jar** é usado somente para satisfazer as dependências de tempo de compilação. Quando você usa essa ação de compilação C# , as associações não são criadas para o Reference **. jar** e não são inseridas na biblioteca de associações resultante. DLL. Use essa opção quando você criar uma biblioteca de associações para o Reference **. jar** , mas ainda não tiver feito isso. Essa ação de compilação é útil para empacotar vários s **. jar**(e/ou. AARs) em várias bibliotecas de associações interdependentes.

- `EmbeddedReferenceJar` &ndash; insere um Reference **. jar** na biblioteca de associações resultante. DLL. Use essa ação de compilação quando desejar criar C# associações para o Input **. jar** (ou. AAR) e todos os seus **. jar**(s) de referência em sua biblioteca de associações.

- `EmbeddedNativeLibrary` &ndash; insere um nativo **. assim,** na associação. Essa ação de compilação é usada para arquivos **. so** que são exigidos pelo arquivo **. jar** que está sendo associado. Pode ser necessário carregar manualmente a biblioteca **. portanto,** antes de executar o código da biblioteca Java. Isso é descrito abaixo.

Essas ações de compilação são explicadas com mais detalhes nos guias a seguir.

Além disso, as seguintes ações de compilação são usadas para ajudar a importar a documentação da API C# do Java e convertê-las na documentação XML:

- `JavaDocJar` é usado para apontar para o Jar de arquivo de Javadoc para uma biblioteca Java que está em conformidade com um estilo de pacote Maven (geralmente `FOOBAR-javadoc**.jar**`).
- `JavaDocIndex` é usado para apontar para `index.html` arquivo dentro do HTML de documentação de referência de API.
- `JavaSourceJar` é usado para complementar `JavaDocJar`, para gerar primeiro o JavaDoc de fontes e, em seguida, tratar os resultados como `JavaDocIndex`, para uma biblioteca Java que esteja de acordo com um estilo de pacote Maven (geralmente `FOOBAR-sources**.jar**`).

A documentação da API deve ser a doclet padrão de Java8, Java7 ou Java6 SDK (são todos os formatos diferentes) ou o estilo DroidDoc.

## <a name="including-a-native-library-in-a-binding"></a>Incluindo uma biblioteca nativa em uma associação

Pode ser necessário incluir uma biblioteca **. portanto** em um projeto de associação do Xamarin. Android como parte da Associação de uma biblioteca Java. Quando o código Java encapsulado é executado, o Xamarin. Android não fará a chamada JNI e a mensagem de erro _Java. lang. UnsatisfiedLinkError: método nativo não encontrado:_ aparecerá no logcat para o aplicativo.

A correção para isso é carregar manualmente a biblioteca **. portanto,** com uma chamada para `Java.Lang.JavaSystem.LoadLibrary`. Por exemplo, supondo que um projeto Xamarin. Android tenha uma biblioteca compartilhada **libpocketsphinx_jni. portanto** , incluído no projeto de associação com uma ação de compilação de **EmbeddedNativeLibrary**, o trecho a seguir (executado antes de usar a biblioteca compartilhada) carregará a biblioteca **. so** :

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="adapting-java-apis-to-ceparsl"></a>Adaptando APIs Java para C&eparsl;

O gerador de associação do Xamarin. Android alterará algumas linguagens e padrões de Java para corresponder aos padrões do .NET. A lista a seguir descreve como o Java é C#mapeado para/.net:

- Os _métodos setter/getter_ em Java são _Propriedades_ no .net.

- Os _campos_ em Java são _Propriedades_ no .net.

- _Ouvintes/interfaces de ouvinte_ em Java são _eventos_ no .net. Os parâmetros dos métodos nas interfaces de retorno de chamada serão representados por uma subclasse `EventArgs`.

- Uma _classe aninhada estática_ em Java é uma _classe aninhada_ no .net.

- Uma _classe interna_ em Java é uma _classe aninhada_ com um construtor de C#instância no.

## <a name="binding-scenarios"></a>Cenários de associação

Os guias de cenário de ligação a seguir podem ajudá-lo a associar uma biblioteca Java (ou bibliotecas) para a Incorporation em seu aplicativo:

- [Associação a. JAR](~/android/platform/binding-java-library/binding-a-jar.md) é uma explicação para a criação de bibliotecas de associações para arquivos **. jar** .

- [Associação a. AAR](~/android/platform/binding-java-library/binding-an-aar.md) é uma instrução para criar bibliotecas de associações para o. Arquivos AAR. Leia este passo a passos para saber como associar bibliotecas de Android Studio.

- A [Associação de um projeto de biblioteca do eclipse](~/android/platform/binding-java-library/binding-a-library-project.md) é um passo a passos para criar bibliotecas de associação de projetos de biblioteca do Android. Leia este passo a passos para saber como associar projetos de biblioteca do Android do eclipse.

- A [personalização de associações](~/android/platform/binding-java-library/customizing-bindings/index.md) explica como fazer modificações manuais na associação para resolver erros de compilação e moldar a API resultante para que ela seja mais "C#-semelhante".

- [Solucionar problemas de associações](~/android/platform/binding-java-library/troubleshooting-bindings.md) lista cenários de erro de associação comuns, explica possíveis causas e oferece sugestões para resolver esses erros.

## <a name="related-links"></a>Links relacionados

- [Trabalhando com JNI](~/android/platform/java-integration/working-with-jni.md)
- [Metadados do GAPI](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
- [Usar bibliotecas nativas](~/android/platform/native-libraries.md)
