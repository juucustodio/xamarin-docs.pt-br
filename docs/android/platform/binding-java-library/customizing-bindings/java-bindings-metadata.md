---
title: Metadados de associações de Java
description: O código C# em Xamarin.Android chama bibliotecas Java através de vinculações, que são um mecanismo que abstrai os detalhes de baixo nível especificados na Interface Nativa Java (JNI). Xamarin.Android fornece uma ferramenta que gera essas ligações. Essa ferramenta permite ao desenvolvedor controlar como uma vinculação é criada usando metadados, o que permite procedimentos como modificar namespaces e renomear membros. Este documento discute como os metadados funcionam, resume os atributos que os metadados suportam e explica como resolver problemas de vinculação modificando esses metadados.
ms.prod: xamarin
ms.assetid: 27CB3C16-33F3-F580-E2C0-968005A7E02E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 25a5d79084f7caa78eec4011c047bd19a63ef748
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "75487783"
---
# <a name="java-bindings-metadata"></a>Metadados de associações de Java

_O código C# em Xamarin.Android chama bibliotecas Java através de vinculações, que são um mecanismo que abstrai os detalhes de baixo nível especificados na Interface Nativa Java (JNI). Xamarin.Android fornece uma ferramenta que gera essas ligações. Essa ferramenta permite ao desenvolvedor controlar como uma vinculação é criada usando metadados, o que permite procedimentos como modificar namespaces e renomear membros. Este documento discute como os metadados funcionam, resume os atributos que os metadados suportam e explica como resolver problemas de vinculação modificando esses metadados._

## <a name="overview"></a>Visão geral

A Xamarin.Android **Java Binding Library** tenta automatizar grande parte do trabalho necessário para vincular uma biblioteca Android existente com a ajuda de uma ferramenta às vezes conhecida como Gerador de _Vinculações_. Ao vincular uma biblioteca Java, o Xamarin.Android inspecionará as classes Java e gerará uma lista de todos os pacotes, tipos e membros que serão vinculados. Esta lista de APIs é armazenada em um arquivo XML que pode ser encontrado no ** \{diretório do projeto}\obj\Release\api.xml** para uma **compilação RELEASE** e no ** \{diretório de projeto}\obj\Debug\api.xml** para uma compilação **DEBUG.**

![Localização do arquivo api.xml na pasta obj/Debug](java-bindings-metadata-images/java-bindings-metadata-01.png)

O Gerador de Vinculações usará o arquivo **api.xml** como uma diretriz para gerar as classes necessárias de invólucro C#. O conteúdo deste arquivo XML é uma variação do formato do _Projeto De Código Aberto android_ do Google.
O trecho a seguir é um exemplo do conteúdo de **api.xml**:

```xml
<api>
    <package name="android">
        <class abstract="false" deprecated="not deprecated" extends="java.lang.Object"
            extends-generic-aware="java.lang.Object" 
            final="true" 
            name="Manifest" 
            static="false" 
            visibility="public">
            <constructor deprecated="not deprecated" final="false"
                name="Manifest" static="false" type="android.Manifest"
                visibility="public">
            </constructor>
        </class>
...
</api>
```

Neste exemplo, **api.xml** declara uma `android` classe `Manifest` no pacote `java.lang.Object`nomeado que estende o .

Em muitos casos, a assistência humana é necessária para fazer com que a API Java se sinta mais ".NET like" ou para corrigir problemas que impeçam a montagem de compilar. Por exemplo, pode ser necessário alterar nomes de pacotes Java para .NET namespaces, renomear uma classe ou alterar o tipo de retorno de um método.

Essas alterações não são obtidas modificando **api.xml** diretamente.
Em vez disso, as alterações são registradas em arquivos XML especiais fornecidos pelo modelo Java Binding Library. Ao compilar o conjunto de vinculações Xamarin.Android, o Gerador de Vinculações será influenciado por esses arquivos de mapeamento ao criar o conjunto de vinculação

Esses arquivos de mapeamento XML podem ser encontrados na pasta **Transformações** do projeto:

- **MetaData.xml** &ndash; Permite que alterações sejam feitas na API final, como alterar o namespace da vinculação gerada. 

- **EnumFields.xml** &ndash; Contém o `int` mapeamento entre as `enums` constantes Java e C# . 

- **EnumMethods.xml** &ndash; Permite alterar parâmetros do `int` método e `enums` tipos de retorno de constantes Java para C# . 

O arquivo **MetaData.xml** é a maior importação desses arquivos, pois permite alterações gerais na vinculação, tais como:

- Renomeando namespaces, classes, métodos ou campos para que sigam as convenções .NET. 

- Removendo namespaces, classes, métodos ou campos que não são necessários. 

- Movendo classes para diferentes espaços de nome. 

- Adicionando classes de suporte adicionais para fazer com que o design da vinculação siga os padrões de estrutura .NET. 

Vamos continuar a discutir **Metadata.xml** com mais detalhes.

## <a name="metadataxml-transform-file"></a>Metadata.xml Transformar arquivo

Como já aprendemos, o arquivo **Metadata.xml** é usado pelo Gerador de Vinculações para influenciar a criação do conjunto de vinculações.
O formato de metadados usa a sintaxe [XPath](https://www.w3.org/TR/xpath/) e é quase idêntico ao *GAPI Metadata* descrito no guia [GAPI Metadata.](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata) Essa implementação é quase uma implementação completa do XPath 1.0 e, portanto, suporta itens da norma 1.0. Este arquivo é um poderoso mecanismo baseado no XPath para alterar, adicionar, ocultar ou mover qualquer elemento ou atributo no arquivo API. Todos os elementos de regra na especificação de metadados incluem um atributo de caminho para identificar o nó ao qual a regra deve ser aplicada. As regras são aplicadas na seguinte ordem:

- **add-node** &ndash; Apêndice um nó de criança ao nó especificado pelo atributo path.
- **attr** &ndash; Define o valor de um atributo do elemento especificado pelo atributo de caminho.
- **remove-nó** &ndash; Remove os nós correspondentes a um XPath especificado.

A seguir, um exemplo de um arquivo **Metadata.xml:**

```xml
<metadata>
    <!-- Normalize the namespace for .NET -->
    <attr path="/api/package[@name='com.evernote.android.job']" 
        name="managedName">Evernote.AndroidJob</attr>

    <!-- Don't  need these packages for the Xamarin binding/public API --> 
    <remove-node path="/api/package[@name='com.evernote.android.job.v14']" />
    <remove-node path="/api/package[@name='com.evernote.android.job.v21']" />

    <!-- Change a parameter name from the generic p0 to a more meaningful one. -->
    <attr path="/api/package[@name='com.evernote.android.job']/class[@name='JobManager']/method[@name='forceApi']/parameter[@name='p0']" 
        name="name">api</attr>
</metadata>
```

A seguir, lista alguns dos elementos XPath mais comumente usados para as API Java:

- `interface`&ndash; Usado para localizar uma interface Java. Por exemplo: `/interface[@name='AuthListener']`.

- `class`&ndash; Usado para localizar uma classe. Por exemplo: `/class[@name='MapView']`.

- `method`&ndash; Usado para localizar um método em uma classe ou interface Java. Por exemplo: `/class[@name='MapView']/method[@name='setTitleSource']`.

- `parameter`&ndash; Identifique um parâmetro para um método. Por exemplo: `/parameter[@name='p0']`

### <a name="adding-types"></a>Adicionando tipos

O `add-node` elemento dirá ao projeto de vinculação Xamarin.Android para adicionar uma nova classe de invólucro ao **api.xml**. Por exemplo, o seguinte trecho direcionará o Gerador de Vinculação para criar uma classe com um construtor e um único campo:

```xml
<add-node path="/api/package[@name='org.alljoyn.bus']">
    <class abstract="false" deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="true" visibility="public" extends="java.lang.Object">
        <constructor deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="false" type="org.alljoyn.bus.AuthListener.AuthRequest" visibility="public" />
        <field name="p0" type="org.alljoyn.bus.AuthListener.Credentials" />
    </class>
</add-node>
```

### <a name="removing-types"></a>Removendo tipos

É possível instruir o Gerador de Vinculações Xamarin.Android para ignorar um tipo Java e não ligá-lo. Isso é feito `remove-node` adicionando um elemento XML ao arquivo **metadata.xml:**

```xml
<remove-node path="/api/package[@name='{package_name}']/class[@name='{name}']" />
```

### <a name="renaming-members"></a>Renomeando membros

A renomeação de membros não pode ser feita editando diretamente o arquivo **api.xml** porque xamarin.Android requer os nomes originais da Interface Nativa java (JNI). Portanto, o `//class/@name` atributo não pode ser alterado; se for, a ligação não funcionará.

Considere o caso em que queremos `android.Manifest`renomear um tipo, .
Para isso, podemos tentar editar diretamente **api.xml** e renomear a classe assim:

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="name">NewName</attr>
```

Isso resultará no Gerador de Vinculações criando o seguinte código C# para a classe de invólucro:

```csharp
[Register ("android/NewName")]
public class NewName : Java.Lang.Object { ... }
```

Observe que a classe wrapper `NewName`foi renomeada para , `Manifest`enquanto o tipo Java original ainda está . Não é mais possível que a classe de vinculação Xamarin.Android acesse quaisquer métodos em `android.Manifest`; a classe de invólucro está vinculada a um tipo Java inexistente.

Para alterar corretamente o nome gerenciado de um tipo (ou `managedName` método embrulhado), é necessário definir o atributo como mostrado neste exemplo:

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="managedName">NewName</attr>
```

<a name="Renaming_EventArg_Wrapper_Classes" />

#### <a name="renaming-eventarg-wrapper-classes"></a>Renomeando `EventArg` classes de wrapper

Quando o gerador de ligação Xamarin.Android identificar um `onXXX` método setter para um tipo de _ouvinte,_ um evento C# e `EventArgs` uma subclasse serão gerados para suportar uma API sabor .NET para o padrão de ouvinte baseado em Java. Como exemplo, considere a seguinte classe e método Java:

```xml
com.someapp.android.mpa.guidance.NavigationManager.on2DSignNextManuever(NextManueverListener listener);
```

Xamarin.Android soltará `on` o prefixo do método `2DSignNextManuever` setter e usará `EventArgs` como base para o nome da subclasse. A subclasse será nomeada algo semelhante a:

```csharp
NavigationManager.2DSignNextManueverEventArgs
```

Este não é um nome de classe C# legal. Para corrigir esse problema, o `argsType` autor vinculante deve usar o `EventArgs` atributo e fornecer um nome C# válido para a subclasse:

```xml
<attr path="/api/package[@name='com.someapp.android.mpa.guidance']/
    interface[@name='NavigationManager.Listener']/
    method[@name='on2DSignNextManeuver']" 
    name="argsType">NavigationManager.TwoDSignNextManueverEventArgs</attr>
```

## <a name="supported-attributes"></a>Atributos suportados

As seções a seguir descrevem alguns dos atributos para transformar APIs Java.

### <a name="argstype"></a>argsType

Este atributo é colocado em métodos setter para nomear a `EventArg` subclasse que será gerada para suportar ouvintes Java. Isso é descrito com mais detalhes abaixo na seção [Renomeando EventArg Wrapper Classes](#Renaming_EventArg_Wrapper_Classes) mais tarde neste guia.

### <a name="eventname"></a>eventName

Especifica um nome para um evento. Se vazio, inibe a geração de eventos.
Isso é descrito com mais detalhes na seção título [Renomeando EventArg Wrapper Classes](#Renaming_EventArg_Wrapper_Classes).

### <a name="managedname"></a>gerenciadoNome

Isso é usado para alterar o nome de um pacote, classe, método ou parâmetro. Por exemplo, para alterar o `MyClass` `NewClassName`nome da classe Java para :

```xml
<attr path="/api/package[@name='com.my.application']/class[@name='MyClass']" 
    name="managedName">NewClassName</attr>
```

O exemplo seguinte ilustra uma expressão XPath `java.lang.object.toString` `Java.Lang.Object.NewManagedName`para renomear o método para :

```xml
<attr path="/api/package[@name='java.lang']/class[@name='Object']/method[@name='toString']" 
    name="managedName">NewMethodName</attr>
```

### <a name="managedtype"></a>gerenciadoType

`managedType`é usado para alterar o tipo de retorno de um método. Em algumas situações, o Gerador de Vinculações inferirá incorretamente o tipo de retorno de um método Java, o que resultará em um erro de tempo de compilação. Uma solução possível nesta situação é alterar o tipo de retorno do método.

Por exemplo, o Gerador de Vinculações acredita que o método `de.neom.neoreadersdk.resolution.compareTo()` Java deve retornar e `int` tomar `Object` como parâmetros, o que resulta na mensagem de erro Erro **CS0535: 'DE. Neom.Neoreadersdk.Resolution' não implementa o membro da interface 'Java.Lang.IComparável.CompareTo(Java.Lang.Object)'**. O trecho a seguir demonstra como alterar o tipo do primeiro parâmetro `DE.Neom.Neoreadersdk.Resolution` do `Java.Lang.Object`método C# gerado de a para a: 

```xml
<attr path="/api/package[@name='de.neom.neoreadersdk']/
    class[@name='Resolution']/
    method[@name='compareTo' and count(parameter)=1 and
    parameter[1][@type='de.neom.neoreadersdk.Resolution']]/
    parameter[1]" name="managedType">Java.Lang.Object</attr> 
```

### <a name="managedreturn"></a>gerenciadoRetorno

Altera o tipo de retorno de um método. Isso não altera o atributo de retorno (já que alterações nos atributos de retorno podem resultar em alterações incompatíveis na assinatura JNI). No exemplo a seguir, o `append` tipo de `SpannableStringBuilder` `IAppendable` retorno do método é alterado de para (lembre-se de que C# não suporta tipos de retorno covariante):

```xml
<attr path="/api/package[@name='android.text']/
    class[@name='SpannableStringBuilder']/
    method[@name='append']" 
    name="managedReturn">Java.Lang.IAppendable</attr>
```

### <a name="obfuscated"></a>Ofuscado

Ferramentas que ofuscam bibliotecas Java podem interferir com o Xamarin.Android Binding Generator e sua capacidade de gerar classes de wrapper C#. As características das classes ofuscadas incluem: 

- O nome da **$** classe inclui uma , ou seja, **uma classe$.**
- O nome da classe está totalmente comprometido de caracteres minúsculos, ou seja, **uma classe**

Este trecho é um exemplo de como gerar um tipo C# "não ofuscado":

```xml
<attr path="/api/package[@name='{package_name}']/class[@name='{name}']" 
    name="obfuscated">false</attr>
```

### <a name="propertyname"></a>propertyName

Este atributo pode ser usado para alterar o nome de uma propriedade gerenciada.

Um caso especializado `propertyName` de uso envolve a situação em que uma classe Java tem apenas um método getter para um campo. Nesta situação, o Gerador de Vinculação gostaria de criar uma propriedade somente para gravação, algo que é desencorajado em .NET. O trecho a seguir mostra como "remover" as `propertyName` propriedades .NET definindo a seqüência de string vazia:

```xml
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='setResourceDescriptor' 
    and count(parameter)=1 
    and parameter[1][@type='java.lang.String']]" 
    name="propertyName"></attr>
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='getResourceDescriptor' 
    and count(parameter)=0]" 
    name="propertyName"></attr>
```

Observe que os métodos setter e getter ainda serão criados pelo Gerador de Vinculações.

### <a name="sender"></a>remetente

Especifica qual parâmetro de um método `sender` deve ser o parâmetro quando o método é mapeado para um evento. O valor `true` pode `false`ser ou . Por exemplo:

```xml
<attr path="/api/package[@name='android.app']/
    interface[@name='TimePickerDialog.OnTimeSetListener']/
    method[@name='onTimeSet']/
    parameter[@name='view']" 
    name="sender">true</ attr>
```

### <a name="visibility"></a>visibility

Este atributo é usado para alterar a visibilidade de uma classe, método ou propriedade. Por exemplo, pode ser necessário `protected` promover um método Java para que seu `public`invólucro C# correspondente seja:

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method --> 
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

## <a name="enumfieldsxml-and-enummethodsxml"></a>EnumFields.xml e EnumMethods.xml

Há casos em que bibliotecas Android usam constantes inteiras para representar estados que são passados para propriedades ou métodos das bibliotecas. Em muitos casos, é útil ligar essas constantes inteiras a enums em C#. Para facilitar esse mapeamento, use os arquivos **EnumFields.xml** e **EnumMethods.xml** em seu projeto de vinculação. 

### <a name="defining-an-enum-using-enumfieldsxml"></a>Definindo um Enum usando EnumFields.xml

O arquivo **EnumFields.xml** contém `int` o mapeamento entre `enums`as constantes Java e C# . Vamos tomar o seguinte exemplo de um enum C# `int` sendo criado para um conjunto de constantes: 

```xml 
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit">
    <field jni-name="UNIT_SECOND" clr-name="Second" value="0" />
    <field jni-name="UNIT_METER" clr-name="Meter" value="1" />
    <field jni-name="UNIT_MILIWATT_HOURS" clr-name="MilliwattHour" value="2" />
</mapping>
```

Aqui pegamos a classe `SKRealReachSettings` Java e definimos `SKMeasurementUnit` um enum `Skobbler.Ngx.Map.RealReach`C# chamado no namespace . As `field` entradas definem o nome da `UNIT_SECOND`constante Java (exemplo), o `Second`nome da entrada enum (exemplo), `0`e o valor inteiro representado por ambas as entidades (exemplo ). 

### <a name="defining-gettersetter-methods-using-enummethodsxml"></a>Definindo métodos Getter/Setter usando EnumMethods.xml

O arquivo **EnumMethods.xml** permite alterar parâmetros `int` do método `enums`e tipos de retorno de constantes Java para C# . Em outras palavras, ele mapeia a leitura e escrita de enums C# `get` `set` (definido no arquivo **EnumFields.xml)** para java `int` constante e métodos.

Dado `SKRealReachSettings` o enum definido acima, o seguinte arquivo **EnumMethods.xml** definiria o getter/setter para este enum:

```xml
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings">
    <method jni-name="getMeasurementUnit" parameter="return" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
    <method jni-name="setMeasurementUnit" parameter="measurementUnit" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
</mapping>
```

A `method` primeira linha mapeia `getMeasurementUnit` o `SKMeasurementUnit` valor de retorno do método Java para o enum. A `method` segunda linha mapeia `setMeasurementUnit` o primeiro parâmetro do mesmo enum.

Com todas essas alterações no lugar, você pode usar o código `MeasurementUnit`seguinte em Xamarin.Android para definir o : 

```csharp
realReachSettings.MeasurementUnit = SKMeasurementUnit.Second;
```

## <a name="summary"></a>Resumo

Este artigo discutiu como o Xamarin.Android usa metadados para transformar uma definição de API a partir do formato *Google* *AOSP*. Depois de cobrir as alterações possíveis usando *Metadata.xml,* examinou as limitações encontradas ao renomear membros e apresentou a lista de atributos XML suportados, descrevendo quando cada atributo deve ser usado.

## <a name="related-links"></a>Links relacionados

- [Trabalhando com jni](~/android/platform/java-integration/working-with-jni.md)
- [Associação de uma biblioteca Java](~/android/platform/binding-java-library/index.md)
- [Metadados GAPI](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
