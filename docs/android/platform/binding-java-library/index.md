---
title: Associação de uma biblioteca Java
description: A comunidade Android tem muitas bibliotecas Java que você pode querer usar em seu aplicativo; este guia explica como incorporar bibliotecas Java em seu aplicativo Xamarin.Android criando uma Biblioteca de Vinculações.
ms.prod: xamarin
ms.assetid: B39FF1D5-69C3-8A76-D268-C227A23C9485
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/01/2017
ms.openlocfilehash: d40a23076ec8f405e57ec40de47ec9ad2261d85d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027600"
---
# <a name="binding-a-java-library"></a>Associação de uma biblioteca Java

_A comunidade Android tem muitas bibliotecas Java que você pode querer usar em seu aplicativo; este guia explica como incorporar bibliotecas Java em seu aplicativo Xamarin.Android criando uma Biblioteca de Vinculações._

## <a name="overview"></a>Visão geral

O ecossistema de bibliotecas de terceiros para Android é enorme. Por causa disso, muitas vezes faz sentido usar uma biblioteca Android existente do que criar uma nova. Xamarin.Android oferece duas maneiras de usar essas bibliotecas:

- Crie uma *Biblioteca de Vinculações* que envolve automaticamente a biblioteca com invólucros C# para que você possa invocar o código Java através de chamadas C#.

- Use a *Interface Nativa Java* *(JNI)* para invocar chamadas no código da biblioteca Java diretamente. JNI é uma estrutura de programação que permite que o código Java ligue e seja chamado por aplicativos ou bibliotecas nativas.

Este guia explica a primeira opção: como criar uma *Biblioteca de Vinculações* que envolve uma ou mais bibliotecas Java existentes em um conjunto que você pode vincular em seu aplicativo. Para obter mais informações sobre como usar jni, consulte [Working with JNI](~/android/platform/java-integration/working-with-jni.md).

O Xamarin.Android implementa vinculações usando *Wrappers Callable Gerenciados* *(MCW).* MCW é uma ponte JNI que é usada quando o código gerenciado precisa invocar código Java. Os invólucros de chamada gerenciados também fornecem suporte para subclasse de tipos Java e para sobrepor métodos virtuais em tipos Java. Da mesma forma, sempre que o código android runtime (ART) deseja invocar código gerenciado, ele o faz através de outra ponte JNI conhecida como Android Callable Wrappers (ACW). Esta [arquitetura](~/android/internals/architecture.md) é ilustrada no seguinte diagrama:

[![Arquitetura de ponte Android JNI](images/architecture.png)](images/architecture.png#lightbox)

Uma Biblioteca de Vinculações é um conjunto que contém embalagens callable gerenciadas para tipos Java. Por exemplo, aqui está `MyClass`um tipo Java, que queremos envolver em uma Biblioteca de Vinculações:

```java
package com.xamarin.mycode;

public class MyClass
{
    public String myMethod (int i) { ... }
}
```

Depois de gerarmos uma Biblioteca de `MyClass`Vinculações para o **.jar** que contém, podemos instancia-la e chamar métodos sobre ela a partir de C#:

```csharp
var instance = new MyClass ();

string result = instance.MyMethod (42);
```

Para criar essa Biblioteca de Vinculações, você usa o modelo xamarin.Android *Java Bindings Library.* O projeto de vinculação resultante cria uma montagem .NET com as classes MCW, **arquivo .jar(s)** e recursos para projetos android library incorporados nele. Você também pode criar Bibliotecas de Vinculações para o Android Archive (. Arquivos AAR) e projetos da Biblioteca Eclipse Android. Fazendo referência ao conjunto DLL da Biblioteca de Vinculações resultante, você pode reutilizar uma biblioteca Java existente em seu projeto Xamarin.Android.

Quando você faz referência a tipos em sua Biblioteca de Vinculação, você deve usar o namespace da sua biblioteca de vinculação. Normalmente, você `using` adiciona uma diretiva na parte superior dos arquivos de origem C# que é a versão de namespace .NET do nome do pacote Java. Por exemplo, se o nome do pacote Java para seu **bound .jar** for o seguinte:

```csharp
com.company.package
```

Em seguida, você `using` colocaria a seguinte declaração na parte superior dos arquivos de origem C# para acessar os tipos no arquivo **bound .jar:**

```csharp
using Com.Company.Package;
```

Ao vincular uma biblioteca Android existente, é necessário ter em mente os seguintes pontos:

- **Há alguma dependência externa para a biblioteca?** &ndash;Quaisquer dependências Java exigidas pela biblioteca do Android devem ser incluídas no projeto Xamarin.Android como **um ReferenceJar** ou como um **EmbeddedReferenceJar**. Quaisquer assembléias nativas devem ser adicionadas ao projeto de vinculação como uma **Biblioteca Nativa Incorporada**.  

- **Que versão da API do Android a biblioteca do Android tem como alvo?** &ndash;Não é possível "rebaixar" o nível de API do Android; garantir que o projeto de vinculação Xamarin.Android esteja mirando o mesmo nível de API (ou superior) que a biblioteca do Android.

- **Que versão do JDK foi usada para compilar a biblioteca?** &ndash;Erros de encadernação podem ocorrer se a biblioteca do Android foi construída com uma versão diferente do JDK do que em uso pelo Xamarin.Android. Se possível, recompile a biblioteca android usando a mesma versão do JDK que é usada pela sua instalação do Xamarin.Android.

## <a name="build-actions"></a>Ações de Build

Quando você cria uma Biblioteca de Vinculações, você define *ações de construção* no .jar ou **.jar** ou . Os arquivos AAR que você incorpora &ndash; no projeto da Biblioteca de Vinculações cada ação de compilação determina como o **.jar** ou . O arquivo AAR será incorporado (ou referenciado por) sua Biblioteca de Vinculações. A lista a seguir resume essas ações de construção:

- `EmbeddedJar`&ndash; Incorpora o **.jar** na DLL da Biblioteca de Vinculações resultante como um recurso incorporado. Esta é a ação de construção mais simples e mais comumente usada. Use esta opção quando quiser que o **.jar** seja compilado automaticamente em código de byte e embalado na Biblioteca de Vinculações.

- `InputJar`&ndash; Não incorpora o **.jar** na Biblioteca de Vinculações resultante . Dll. Sua Biblioteca de Vinculações. A DLL terá uma dependência deste **.jar** em tempo de execução. Use essa opção quando não quiser incluir o **.jar** em sua Biblioteca de Vinculações (por exemplo, por razões de licenciamento). Se você usar esta opção, você deve garantir que a entrada **.jar** esteja disponível no dispositivo que executa o seu aplicativo.

- `LibraryProjectZip`&ndash; Incorpora um . Arquivo AAR na Biblioteca de Vinculações resultante . Dll. Isso é semelhante ao EmbeddedJar, exceto que você pode acessar recursos (bem como código) no limite . Arquivo AAR. Use esta opção quando quiser incorporar um . AAR em sua Biblioteca de Vinculações.

- `ReferenceJar`&ndash; Especifica uma referência **.jar**: uma referência **.jar** é um **.jar** que um de seu bound **.jar** ou . Os arquivos AAR dependem. Esta referência **.jar** é usada apenas para satisfazer dependências de tempo de compilação. Quando você usa essa ação de compilação, as vinculações C# não são criadas para o **.jar** de referência e não está incorporada na Biblioteca de Vinculações resultante . Dll. Use esta opção quando você fará uma Biblioteca de Vinculações para a referência **.jar,** mas ainda não o fez. Esta ação de construção é útil para empacotar vários **.jar**s (e/ou . AARs) em várias bibliotecas de vinculações interdependentes.

- `EmbeddedReferenceJar`&ndash; Incorporar uma referência **.jar** na Biblioteca de Vinculações resultante . Dll. Use esta ação de compilação quando quiser criar as ligações C# para a entrada **.jar** (ou . AAR) e todas as suas referências **.jar**(s) em sua Biblioteca de Vinculações.

- `EmbeddedNativeLibrary`&ndash; Incorpora um nativo **.assim** na ligação. Esta ação de compilação é usada para **arquivos .so** que são exigidos pelo arquivo **.jar** sendo vinculado. Pode ser necessário carregar manualmente a biblioteca **.so** antes de executar o código da biblioteca Java. Isso é descrito abaixo.

Essas ações de construção são explicadas com mais detalhes nos seguintes guias.

Além disso, as seguintes ações de compilação são usadas para ajudar a importar documentação da API Java e convertê-las em documentação C# XML:

- `JavaDocJar`é usado para apontar para Javadoc arquivo Jar para uma biblioteca Java `FOOBAR-javadoc**.jar**`que está em conformidade com um estilo de pacote Maven (geralmente ).
- `JavaDocIndex`é usado para `index.html` apontar para arquivo dentro da documentação de referência da API HTML.
- `JavaSourceJar`é usado `JavaDocJar`para complementar, primeiro para gerar JavaDoc `JavaDocIndex`a partir de fontes e, em seguida, tratar `FOOBAR-sources**.jar**`os resultados como, para uma biblioteca Java que se conforma com um estilo de pacote Maven (geralmente ).

A documentação da API deve ser o doclet padrão do Java8, Java7 ou Java6 SDK (todos eles são de formato diferente), ou o estilo DroidDoc.

## <a name="including-a-native-library-in-a-binding"></a>Incluindo uma Biblioteca Nativa em uma Vinculação

Pode ser necessário incluir uma biblioteca **.so** em um projeto de vinculação Xamarin.Android como parte da vinculação de uma biblioteca Java. Quando o código Java embrulhado for executado, o Xamarin.Android falhará em fazer a chamada JNI e a mensagem de erro _java.lang.UnsatisfiedLinkError: Método nativo não encontrado:_ aparecerá no logcat para fora do aplicativo.

A correção para isso é carregar manualmente a `Java.Lang.JavaSystem.LoadLibrary`biblioteca **.so** com uma chamada para . Por exemplo, assumindo que um projeto Xamarin.Android tenha compartilhado a biblioteca **libpocketsphinx_jni.so** incluído no projeto de vinculação com uma ação de compilação do **EmbeddedNativeLibrary**, o seguinte trecho (executado antes de usar a biblioteca compartilhada) carregará a **biblioteca .so:**

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="adapting-java-apis-to-ceparsl"></a>Adaptando ASApIs Java a C&eparsl;

O Gerador de Ligação Xamarin.Android mudará alguns idiomas e padrões Java para corresponder aos padrões .NET. A lista a seguir descreve como java é mapeado para C#/.NET:

- _Os métodos Setter/Getter_ em Java são _Propriedades_ em .NET.

- _Os campos_ em Java são _propriedades_ em .NET.

- _As interfaces de ouvintes/ouvintes_ em Java são _Eventos_ em .NET. Os parâmetros dos métodos nas interfaces de `EventArgs` retorno de chamada serão representados por uma subclasse.

- Uma _classe Aninhado Estático_ em Java é uma _classe aninhada_ em .NET.

- Uma _classe interna_ em Java é uma classe _aninhada_ com um construtor de instâncias em C#.

## <a name="binding-scenarios"></a>Cenários vinculantes

Os seguintes guias de cenário de vinculação podem ajudá-lo a vincular uma biblioteca Java (ou bibliotecas) para incorporação em seu aplicativo:

- [Vinculando a . Jar](~/android/platform/binding-java-library/binding-a-jar.md) é um passo a passo para criar Bibliotecas de Vinculações para arquivos **.jar.**

- [Vinculando um . AAR](~/android/platform/binding-java-library/binding-an-aar.md) é um passo a passo para criar Bibliotecas de Vinculações para . Arquivos AAR. Leia este passo a passo para aprender como vincular bibliotecas android studio.

- [Vincular um Projeto de Biblioteca eclipse](~/android/platform/binding-java-library/binding-a-library-project.md) é um passo a passo para criar bibliotecas vinculativas de Projetos de Bibliotecas Android. Leia este passo a passo para aprender como vincular projetos de biblioteca do Eclipse Android.

- [Personalizar vinculações](~/android/platform/binding-java-library/customizing-bindings/index.md) explica como fazer modificações manuais na vinculação para resolver erros de compilação e moldar a API resultante para que ela seja mais "C#-like".

- [Solução de problemas As vinculações](~/android/platform/binding-java-library/troubleshooting-bindings.md) listam cenários comuns de erro de vinculação, explicam possíveis causas e oferecem sugestões para resolver esses erros.

## <a name="related-links"></a>Links relacionados

- [Trabalhando com jni](~/android/platform/java-integration/working-with-jni.md)
- [Metadados GAPI](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
- [Usar bibliotecas nativas](~/android/platform/native-libraries.md)
