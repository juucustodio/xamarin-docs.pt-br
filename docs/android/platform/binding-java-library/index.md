---
title: Associação de uma biblioteca Java
description: A comunidade do Android tem muitas bibliotecas de Java que você talvez queira usar em seu aplicativo. Este guia explica como incorporar bibliotecas Java em seu aplicativo xamarin. Android, criando uma biblioteca de associações.
ms.prod: xamarin
ms.assetid: B39FF1D5-69C3-8A76-D268-C227A23C9485
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/01/2017
ms.openlocfilehash: c41aecf5f8c65ad5bfba5361b77d7c7fc047cda4
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171606"
---
# <a name="binding-a-java-library"></a>Associação de uma biblioteca Java

_A comunidade do Android tem muitas bibliotecas de Java que você talvez queira usar em seu aplicativo. Este guia explica como incorporar bibliotecas Java em seu aplicativo xamarin. Android, criando uma biblioteca de associações._

## <a name="overview"></a>Visão geral

O ecossistema de bibliotecas de terceiros para o Android é enorme. Por isso, com frequência faz sentido usar uma biblioteca Android existente que to, crie um novo. Xamarin. Android oferece duas maneiras de usar essas bibliotecas:

-   Criar um *biblioteca de associações* que ajusta automaticamente a biblioteca com C# código de wrappers para que você pode invocar o Java por meio de C# chamadas.

-   Use o *Interface nativa Java* (*JNI*) para invocar chamadas no código de biblioteca Java diretamente. JNI é uma estrutura de programação que permite que o código Java chamar e ser chamados por aplicativos nativos ou bibliotecas.

Este guia explica a primeira opção: como criar uma *biblioteca de associações* que envolve uma ou mais bibliotecas Java existentes em um assembly que você pode vincular a em seu aplicativo. Para obter mais informações sobre como usar JNI, consulte [trabalhar com JNI](~/android/platform/java-integration/working-with-jni.md).

Xamarin. Android implementa associações usando *gerenciados Callable Wrappers* (*MCW*). MCW é uma ponte JNI que é usada quando o código gerenciado deve invocar o código Java. Callable wrappers do gerenciados também fornecem suporte para tipos Java da subclasse e para substituir métodos virtuais em tipos Java. Da mesma forma, sempre que o código de tempo de execução Android (arte) deseja invocar código gerenciado, isso é feito por meio de outra ponte JNI conhecido como Android Callable Wrappers (ACW). Isso [arquitetura](~/android/internals/architecture.md) é ilustrado no diagrama a seguir:

[![Arquitetura de ponte JNI Android](images/architecture.png)](images/architecture.png#lightbox)

Uma biblioteca de associações é um assembly que contém o gerenciado Callable Wrappers para tipos Java. Por exemplo, aqui está um tipo de Java, `MyClass`, que desejamos encapsular em uma biblioteca de associações:

```java
package com.xamarin.mycode;

public class MyClass
{
    public String myMethod (int i) { ... }
}
```

Depois que podemos gerar uma biblioteca de associações para o **. jar** que contém `MyClass`, podemos criar uma instância dela e chamar métodos de C#:

```csharp
var instance = new MyClass ();

string result = instance.MyMethod (42);
```

Para criar a biblioteca de associações, use o xamarin. Android *biblioteca de associações de Java* modelo. O projeto resultante da associação cria um assembly .NET com as classes de MCW **. jar** arquivo (s) e recursos para projetos de biblioteca Android inseridos nele. Você também pode criar bibliotecas de associações de arquivo morto do Android (. Arquivos AAR) e projetos de biblioteca Android do Eclipse. Ao referenciar o assembly resultante da DLL da biblioteca de associações, você pode reutilizar uma biblioteca Java existente em seu projeto xamarin. Android.

Ao referenciar tipos em sua biblioteca de ligação, você deve usar o namespace da sua biblioteca de ligação. Normalmente, você adiciona uma `using` diretiva na parte superior do seu C# arquivos de origem que é a versão do .NET namespace do nome do pacote de Java. Por exemplo, se o pacote Java Nomeie seu limite **. jar** é o seguinte:

```csharp
com.company.package
```

Em seguida, você poderia colocar o seguinte `using` instrução na parte superior do seu C# arquivos de origem para acessar os tipos em que o limite **. jar** arquivo:

```csharp
using Com.Company.Package;
```


Ao associar uma biblioteca Android existente, é necessário ter os seguintes pontos em mente:

* **Há dependências externas para a biblioteca?** &ndash; Quaisquer dependências de Java exigidas pela biblioteca do Android devem ser incluídas no projeto xamarin. Android como um **ReferenceJar** ou como um **EmbeddedReferenceJar**. Todos os assemblies nativos devem ser adicionados ao projeto de associação como uma **EmbeddedNativeLibrary**.  

* **Qual versão da API do Android é que o destino da biblioteca do Android?** &ndash; Não é possível fazer o "downgrade" nível de API do Android; Certifique-se de que o projeto de associação do xamarin. Android está direcionando a mesma API nível (ou superior) como a biblioteca Android.

* **Qual versão do JDK foi usado para compilar a biblioteca?** &ndash; Erros de associação podem ocorrer se a biblioteca Android foi criada com uma versão diferente do JDK que em uso pelo xamarin. Android. Se possível, recompile a biblioteca do Android usando a mesma versão do JDK que é usado pela sua instalação do xamarin. Android.


## <a name="build-actions"></a>Ações de Build

Quando você cria uma biblioteca de associações, você definir *ações de build* sobre o **. jar** ou. Arquivos AAR que você incorporar em seu projeto de biblioteca de associações &ndash; cada ação de compilação determina como o **. jar** ou. Arquivo AAR será incorporado (ou referenciado pelo) sua biblioteca de associações. A lista a seguir resume essas ações de construção:

* `EmbeddedJar` &ndash; Insere o **. jar** a DLL de biblioteca de associações resultante como um recurso inserido. Isso é o mais simples e a maioria dos ação de compilação usados. Use esta opção quando desejar que o **. jar** automaticamente compilados em código de byte e empacotados na biblioteca de associações.

* `InputJar` &ndash; Não insere o **. jar** para a biblioteca de associações resultante. DLL. Sua biblioteca de associações. DLL têm uma dependência nessa **. jar** em tempo de execução. Use essa opção quando você não deseja incluir o **. jar** em sua biblioteca de associações (por exemplo, por motivos de licenciamento). Se você usar essa opção, você deve garantir que a entrada **. jar** está disponível no dispositivo que executa seu aplicativo.

* `LibraryProjectZip` &ndash; Insere um. Arquivo AAR para a biblioteca de associações resultante. DLL. Isso é semelhante ao EmbeddedJar, exceto que você pode acessar recursos (bem como código) em que o limite. Arquivo AAR. Use essa opção quando quiser inserir um. AAR em sua biblioteca de associações.

* `ReferenceJar` &ndash; Especifica uma referência **. jar**: uma referência **. jar** é um **. jar** que um dos seu limite **. jar** ou. Arquivos AAR depende. Esta referência **. jar** é usado apenas para satisfazer as dependências de tempo de compilação. Quando você usa essa ação de compilação, C# associações não são criadas para a referência **. jar** e ele não será inserido na biblioteca de associações resultante. DLL. Use essa opção quando você fará uma biblioteca de associações para a referência **. jar** , mas não tiver feito isso ainda. Essa ação de compilação é útil para empacotar vários **. jar**s (e/ou. AARs) em várias bibliotecas de associações interdependentes.

* `EmbeddedReferenceJar` &ndash; Insere uma referência **. jar** para a biblioteca de associações resultante. DLL. Use essa ação de compilação quando você deseja criar C# associações de entrada para o **. jar** (ou). AAR) e todos os seu referência **. jar**(s) em sua biblioteca de associações.

* `EmbeddedNativeLibrary` &ndash; Insere um nativo **. SO** na vinculação. Essa ação de compilação é usada para **. SO** arquivos que são necessários para o **. jar** do arquivo que está sendo associada. Talvez seja necessário carregar manualmente os **. SO** biblioteca antes de executar o código da biblioteca de Java. Isso é descrito abaixo.

Essas ações são explicadas em mais detalhes nos seguintes guias de compilação.

Além disso, as seguintes ações de build são usadas para ajudar a importação de documentação da API do Java e convertê-los em C# documentação XML:

* `JavaDocJar` é usado para apontar para o arquivo Jar do Javadoc para uma biblioteca de Java que está em conformidade com um estilo de pacote do Maven (geralmente `FOOBAR-javadoc**.jar**`).
* `JavaDocIndex` é usado para apontar para `index.html` arquivo dentro a documentação de referência de API HTML.
* `JavaSourceJar` é usado para complementar `JavaDocJar`, para gerar primeiro JavaDoc de fontes e, em seguida, trate os resultados como `JavaDocIndex`, para uma biblioteca de Java que está em conformidade com um Maven estilo de pacote (geralmente `FOOBAR-sources**.jar**`).

A documentação da API deve ser o doclet padrão de Java8, Java7 ou o SDK Java6 (estão todos diferente formato), ou o estilo de DroidDoc.

## <a name="including-a-native-library-in-a-binding"></a>Incluindo uma biblioteca nativa em uma associação

Talvez seja necessário incluir um **. SO** biblioteca em um projeto de associação do xamarin. Android como parte de uma biblioteca Java de associação. Quando executa o código encapsulado do Java, xamarin. Android não consegue fazer a chamada JNI e a mensagem de erro _java.lang.UnsatisfiedLinkError: método nativo não encontrado:_ será exibido o logcat-out para o aplicativo.

A correção para isso é para carregar manualmente os **. SO** biblioteca com uma chamada para `Java.Lang.JavaSystem.LoadLibrary`. Por exemplo, supondo que um projeto xamarin. Android tem biblioteca compartilhada **libpocketsphinx_jni.so** incluído no projeto de associação com uma ação de build **EmbeddedNativeLibrary**, o trecho a seguir (executado antes de usar a biblioteca compartilhada) carregará o **. SO** biblioteca:

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="adapting-java-apis-to-ceparsl"></a>Adaptação de acordo com as APIs de Java para o C&eparsl;

O gerador de associação do xamarin. Android será alterado algumas linguagens Java e padrões para corresponder aos padrões de .NET. A lista a seguir descreve como o Java é mapeado para C#/.NET:

-   _Os métodos Getter/setter_ em Java estão _propriedades_ no .NET.

-   _Campos_ em Java estão _propriedades_ no .NET.

-   _Interfaces de ouvinte/ouvintes_ em Java estão _eventos_ no .NET. Os parâmetros de métodos em interfaces de retorno de chamada serão representados por um `EventArgs` subclasse.

-   Um _classe aninhada estáticos_ no Java é um _classes aninhadas_ no .NET.

-   Uma _classe interna_ no Java é um _classes aninhadas_ com um construtor de instância no C#.



## <a name="binding-scenarios"></a>Cenários de associação

Os guias de cenário de associação a seguir podem ajudar você a vincular uma biblioteca Java (ou bibliotecas) para incorporação em seu aplicativo:

-   [Associando um. JAR](~/android/platform/binding-java-library/binding-a-jar.md) é um passo a passo para a criação de bibliotecas de associações para **. jar** arquivos.

-   [Associando um. AAR](~/android/platform/binding-java-library/binding-an-aar.md) um passo a passo para a criação de bibliotecas de associações para. Arquivos AAR. Leia este passo a passo para saber como associar a bibliotecas do Android Studio.

-   [Associação de um projeto de biblioteca do Eclipse](~/android/platform/binding-java-library/binding-a-library-project.md) um passo a passo para criar bibliotecas de associação de projetos de biblioteca Android. Leia este passo a passo para saber como associar projetos de biblioteca Android do Eclipse.

-   [Personalização de associações](~/android/platform/binding-java-library/customizing-bindings/index.md) explica como fazer modificações manuais para a associação para resolver erros de compilação e a API resultante da forma para que ele seja mais "C#-como".

-   [Solução de problemas de associações](~/android/platform/binding-java-library/troubleshooting-bindings.md) lista cenários comuns de erro de associação, explica as causas possíveis e oferece sugestões para resolver esses erros.


## <a name="related-links"></a>Links relacionados

- [Trabalhar com JNI](~/android/platform/java-integration/working-with-jni.md)
- [Metadados GAPI](http://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
- [Usar bibliotecas nativas](~/android/platform/native-libraries.md)
