---
title: "Associação de uma biblioteca de Java"
description: "Comunidade do Android tem muitas bibliotecas de Java que você talvez queira usar em seu aplicativo; Este guia explica como incorporar bibliotecas Java em seu aplicativo xamarin, criando uma biblioteca de associações."
ms.topic: article
ms.prod: xamarin
ms.assetid: B39FF1D5-69C3-8A76-D268-C227A23C9485
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/01/2017
ms.openlocfilehash: f336767cb6aea8bd8c7ce44f6479850a63d473a6
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="binding-a-java-library"></a>Associação de uma biblioteca de Java

_Comunidade do Android tem muitas bibliotecas de Java que você talvez queira usar em seu aplicativo; Este guia explica como incorporar bibliotecas Java em seu aplicativo xamarin, criando uma biblioteca de associações._

## <a name="overview"></a>Visão geral

O ecossistema de biblioteca de terceiros para Android é grande. Por isso, geralmente faz sentido para usar uma biblioteca Android existente que to criar um novo. Xamarin oferece duas maneiras de usar essas bibliotecas:

-   Criar um *biblioteca associações* automaticamente que empacota a biblioteca com c# wrappers para que você pode chamar o código de Java por meio de chamadas do c#.

-   Use o *Interface nativa Java* (*JNI*) para invocar as chamadas no código de biblioteca Java diretamente. JNI é uma estrutura de programação que permite que o código Java chamar e ser chamado por aplicativos nativos ou bibliotecas.

Este guia explica a primeira opção: como criar um *biblioteca associações* que envolve uma ou mais bibliotecas Java existentes em um assembly que você pode vincular a em seu aplicativo. Para obter mais informações sobre como usar JNI, consulte [trabalhando com JNI](~/android/platform/java-integration/working-with-jni.md).

Xamarin implementa associações usando *gerenciados Callable Wrappers* (*MCW*). MCW é uma ponte JNI que é usada quando as necessidades de código gerenciado chamar o código Java. Callable wrappers do gerenciado também oferecem suporte para subclasses tipos Java e substituir métodos virtuais em tipos de Java. Da mesma forma, sempre que o código de tempo de execução Android (arte) deseja invocar código gerenciado, ele faz isso por meio de outra ponte JNI conhecido como Android Callable Wrappers (ACW). Isso [arquitetura](~/android/internals/architecture.md) é ilustrado no diagrama a seguir:

[![Arquitetura de ponte JNI Android](images/architecture.png)](images/architecture.png#lightbox)

Uma biblioteca de associações é um assembly contendo gerenciados Callable Wrappers para tipos de Java. Por exemplo, aqui é um tipo de Java, `MyClass`, que desejamos incluir em uma biblioteca de associações:

```java
package com.xamarin.mycode;

public class MyClass
{
    public String myMethod (int i) { ... }
}
```

Depois que gerar uma biblioteca de ligações para o **. jar** que contém `MyClass`, podemos instanciá-la e chamar métodos na partir de c#:

```csharp
var instance = new MyClass ();

string result = instance.MyMethod (42);
```

Para criar essa biblioteca de associações, use o xamarin *biblioteca de associações de Java* modelo. O projeto resultante da associação cria um assembly .NET com as classes de MCW **. jar** arquivos e recursos de projetos de biblioteca Android inseridos nele. Você também pode criar bibliotecas de associações de arquivo morto Android (. Arquivos AAR) e projetos de biblioteca Android do Eclipse. Referenciando o assembly resultante da DLL de biblioteca de associações, você pode reutilizar uma biblioteca Java existente em seu projeto xamarin.

Ao fazer referência a tipos de sua biblioteca de associação, você deve usar o namespace da sua biblioteca de ligação. Normalmente, você adiciona um `using` diretiva na parte superior do seu código-fonte c# arquivos, isto é, a versão do namespace .NET do nome do pacote Java. Por exemplo, se o pacote Java nomear seu limite **. jar** é o seguinte:

```csharp
com.company.package
```

Em seguida, você deve colocar o seguinte `using` instrução na parte superior dos seus arquivos de origem do c# para acessar tipos no limite **. jar** arquivo:

```csharp
using Com.Company.Package;
```


Ao associar uma biblioteca Android existente, é necessário ter os seguintes pontos em mente:

* **Há quaisquer dependências externas para a biblioteca?** &ndash; Todas as dependências de Java exigidas pela biblioteca do Android devem ser incluídas no projeto xamarin como um **ReferenceJar** ou como um **EmbeddedReferenceJar**. Todos os assemblies nativo devem ser adicionados ao projeto de associação como uma **EmbeddedNativeLibrary**.  

* **Qual versão de API do Android é é o destino de biblioteca Android?** &ndash; Não é possível fazer "downgrade" nível de API do Android; Certifique-se de que o projeto de associação xamarin está direcionando a mesma API nível (ou superior) como a biblioteca Android.

* **Qual versão do JDK foi usado para compilar a biblioteca?** &ndash; Erros de associação podem ocorrer se a biblioteca Android foi criada com uma versão diferente do JDK que em uso por xamarin. Se possível, compile novamente a biblioteca do Android usando a mesma versão do JDK que é usado por sua instalação do xamarin.


## <a name="build-actions"></a>Ações de Build

Quando você cria uma biblioteca de associações, você definir *ações de construção* no **. jar** ou. A arquivos AAR que incorporar o seu projeto de biblioteca de associações &ndash; cada ação de compilação determina como o **. jar** ou. Arquivo AAR será incorporado (ou referenciado por) sua biblioteca de associações. A lista a seguir resume essas ações de construção:

* `EmbeddedJar` &ndash; Incorpora o **. jar** a DLL de biblioteca associações resultante como um recurso inserido. Este é o mais simples e a maioria das ações de compilação usados com frequência. Use esta opção quando desejar que o **. jar** automaticamente compilado no código de bytes e empacotado para a biblioteca de associações.

* `InputJar` &ndash; Não insere o **. jar** na biblioteca de associações resultante. DLL. Sua biblioteca de associações. DLL terá uma dependência nesse **. jar** em tempo de execução. Use essa opção quando você não deseja incluir o **. jar** na sua biblioteca de associações (por exemplo, por motivos de licenciamento). Se você usar essa opção, você deve garantir que a entrada **. jar** está disponível no dispositivo que executa o aplicativo.

* `LibraryProjectZip` &ndash; Insere um. Arquivo AAR na biblioteca de associações resultante. DLL. Isso é semelhante ao EmbeddedJar, exceto que você pode acessar recursos (assim como código) em que o limite. Arquivo AAR. Use essa opção quando quiser inserir um. AAR em sua biblioteca de associações.

* `ReferenceJar` &ndash; Especifica uma referência **. jar**: uma referência **. jar** é um **. jar** que um dos seus associados **. jar** ou. Depende da arquivos AAR. Essa referência **. jar** é usado somente para satisfazer as dependências de tempo de compilação. Quando você usa essa ação de compilação, c# associações não são criadas para a referência **. jar** e ele não será inserido na biblioteca de associações resultante. DLL. Use essa opção quando você fará uma biblioteca de ligações para a referência **. jar** , mas não tiver feito isso ainda. Essa ação de compilação é útil para empacotar várias **. jar**s (e/ou. AARs) em várias bibliotecas de associações interdependentes.

* `EmbeddedReferenceJar` &ndash; Insere uma referência **. jar** na biblioteca de associações resultante. DLL. Use essa ação de compilação quando você deseja criar associações do c# para a entrada **. jar** (ou). AAR) e todos os seu referência **. jar**(s) na sua biblioteca de associações.

* `EmbeddedNativeLibrary` &ndash; Insere um nativo **. SO** para a associação. Essa ação de compilação é usada para **. SO** arquivos necessários para o **. jar** arquivo que está sendo associada. Talvez seja necessário carregar manualmente o **. SO** biblioteca antes de executar o código da biblioteca de Java. Isso é descrito abaixo.

Essas ações são explicadas em mais detalhes nos seguintes guias de compilação.

Além disso, as seguintes ações de compilação são usadas para ajudar a documentação da API do Java de importação e convertê-los em documentação XML c#:

* `JavaDocJar` é usado para apontar para o arquivo Jar do Javadoc para uma biblioteca de Java que está de acordo com um estilo de pacote Maven (geralmente `FOOBAR-javadoc**.jar**`).
* `JavaDocIndex` é usado para apontar para `index.html` arquivo dentro a documentação de referência de API HTML.
* `JavaSourceJar` é usada para complementar `JavaDocJar`para primeiro gerar JavaDoc de fontes e, em seguida, trate os resultados como `JavaDocIndex`, para uma biblioteca de Java que está de acordo com um Maven pacote estilo (geralmente `FOOBAR-sources**.jar**`).

A documentação da API deve ser o doclet padrão de Java8, Java7 ou SDK Java6 (são formato diferente), ou o estilo de DroidDoc.

## <a name="including-a-native-library-in-a-binding"></a>Incluindo uma biblioteca nativa em uma associação

Talvez seja necessário incluir um **. SO** biblioteca em um projeto de associação xamarin como parte da associação a uma biblioteca de Java. Quando é executado, o código Java encapsulado xamarin falhará fazer a chamada JNI e a mensagem de erro _java.lang.UnsatisfiedLinkError: método nativo não encontrado:_ aparecerão no logcat fora do aplicativo.

A solução é carregar manualmente o **. SO** biblioteca com uma chamada para `Java.Lang.JavaSystem.LoadLibrary`. Por exemplo, supondo que um projeto xamarin compartilhou biblioteca **libpocketsphinx_jni.so** incluído no projeto associação com uma ação de compilação de **EmbeddedNativeLibrary**, o seguinte trecho de código (executado antes de usar a biblioteca compartilhada) carregará o **. SO** biblioteca:

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="adapting-java-apis-to-ceparsl"></a>APIs de Java adaptar a C&eparsl;

O gerador de associação xamarin alterará alguns padrões para corresponder aos padrões de .NET e linguagens de Java. A lista a seguir descreve como o Java é mapeado para c# / .NET:

-   _Os métodos Getter/setter_ no Java são _propriedades_ no .NET.

-   _Campos_ no Java são _propriedades_ no .NET.

-   _Interfaces de ouvinte/ouvintes_ no Java são _eventos_ no .NET. Os parâmetros de métodos em interfaces de retorno de chamada serão representados por um `EventArgs` subclasse.

-   Um _classe aninhada estático_ em Java é um _as classes aninhadas_ no .NET.

-   Um _classe interna_ em Java é um _as classes aninhadas_ com um construtor de instância no c#.



## <a name="binding-scenarios"></a>Cenários de associação

Os seguintes guias de cenário de associação podem ajudá-lo a associar a uma biblioteca Java (ou bibliotecas) para implementação em seu aplicativo:

-   [Associação de um. JAR](~/android/platform/binding-java-library/binding-a-jar.md) é um passo a passo para a criação de associações de bibliotecas para **. jar** arquivos.

-   [Associação de um. AAR](~/android/platform/binding-java-library/binding-an-aar.md) é um passo a passo para a criação de associações de bibliotecas para. Arquivos AAR. Leia este passo a passo para aprender a associar a bibliotecas Android Studio.

-   [Associação de um projeto de biblioteca do Eclipse](~/android/platform/binding-java-library/binding-a-library-project.md) é um passo a passo para criar bibliotecas de associação de projetos de biblioteca Android. Leia este passo a passo para aprender a associar projetos de biblioteca Android do Eclipse.

-   [Personalizando associações](~/android/platform/binding-java-library/customizing-bindings/index.md) explica como fazer modificações manuais a associação para resolver erros de compilação e a API resultante da forma para que seja mais "c#-como".

-   [Solucionando problemas de associações](~/android/platform/binding-java-library/troubleshooting-bindings.md) lista cenários comuns de erro de associação, explica as possíveis causas e oferece sugestões para resolver esses erros.


## <a name="related-links"></a>Links relacionados

- [Trabalhando com JNI](~/android/platform/java-integration/working-with-jni.md)
- [Metadados GAPI](http://www.mono-project.com/GAPI#Metadata)
- [Usar bibliotecas nativas](~/android/platform/native-libraries.md)
