---
title: Metadados de associações de Java
description: C#o código no Xamarin. Android chama bibliotecas Java por meio de associações, que são um mecanismo que abstrai os detalhes de nível baixo que são especificados na interface nativa Java (JNI). O Xamarin. Android fornece uma ferramenta que gera essas associações. Essas ferramentas permitem que o desenvolvedor controle como uma associação é criada usando metadados, que permite procedimentos como a modificação de namespaces e a renomeação de membros. Este documento discute como os metadados funcionam, resume os atributos aos quais os metadados dão suporte e explica como resolver problemas de ligação modificando esses metadados.
ms.prod: xamarin
ms.assetid: 27CB3C16-33F3-F580-E2C0-968005A7E02E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 1f06601b2b419141b4bd44677826df4e64a831fc
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020563"
---
# <a name="java-bindings-metadata"></a>Metadados de associações de Java

_C#o código no Xamarin. Android chama bibliotecas Java por meio de associações, que são um mecanismo que abstrai os detalhes de nível baixo que são especificados na interface nativa Java (JNI). O Xamarin. Android fornece uma ferramenta que gera essas associações. Essas ferramentas permitem que o desenvolvedor controle como uma associação é criada usando metadados, que permite procedimentos como a modificação de namespaces e a renomeação de membros. Este documento discute como os metadados funcionam, resume os atributos aos quais os metadados dão suporte e explica como resolver problemas de ligação modificando esses metadados._

## <a name="overview"></a>Visão Geral

Uma **biblioteca de associação Java** do Xamarin. Android tenta automatizar grande parte do trabalho necessário para associar uma biblioteca Android existente com a ajuda de uma ferramenta, às vezes conhecida como o _gerador de associações_. Ao associar uma biblioteca Java, o Xamarin. Android inspecionará as classes Java e gerará uma lista de todos os pacotes, tipos e membros que serão associados. Essa lista de APIs é armazenada em um arquivo XML que pode ser encontrado em **\{diretório do projeto} \obj\Release\api.xml** para uma compilação de **versão** e em **\{diretório do projeto} \Obj\Debug\api.xml** para uma compilação de **depuração** .

![Local do arquivo API. xml na pasta obj/Debug](java-bindings-metadata-images/java-bindings-metadata-01.png)

O gerador de associações usará o arquivo **API. xml** como uma diretriz para gerar as classes C# wrapper necessárias. O conteúdo desse arquivo XML é uma variação do formato de projeto de software livre _Android_ do Google.
O trecho a seguir é um exemplo do conteúdo de **API. xml**:

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

Neste exemplo, o **API. xml** declara uma classe no pacote de `android` chamado `Manifest` que estende o `java.lang.Object`.

Em muitos casos, a assistência humana é necessária para fazer com que a API Java sinta mais ".NET como" ou corrija problemas que impeçam o assembly de associação de compilação. Por exemplo, pode ser necessário alterar nomes de pacote Java para namespaces do .NET, renomear uma classe ou alterar o tipo de retorno de um método.

Essas alterações não são obtidas modificando o **API. xml** diretamente.
Em vez disso, as alterações são registradas em arquivos XML especiais fornecidos pelo modelo de biblioteca de associação Java. Ao compilar o assembly de associação do Xamarin. Android, o gerador de associações será influenciado por esses arquivos de mapeamento ao criar o assembly de associação

Esses arquivos de mapeamento XML podem ser encontrados na pasta **transformações** do projeto:

- O &ndash; **Metadata. xml** permite que as alterações sejam feitas na API final, como alterar o namespace da Associação gerada. 

- **EnumFields. xml** &ndash; contém o mapeamento entre as constantes `int` Java C# e`enums`. 

- **EnumMethods. xml** &ndash; permite a alteração de parâmetros de método e tipos de retorno de C# constantes de `int` Java para`enums`. 

O arquivo **Metadata. xml** é a mais importação desses arquivos, pois permite alterações de uso geral na associação, como:

- Renomeando namespaces, classes, métodos ou campos para que sigam as convenções .NET. 

- Removendo namespaces, classes, métodos ou campos que não são necessários. 

- Movendo classes para namespaces diferentes. 

- Adicionar classes de suporte adicionais para fazer o design da Associação seguir os padrões do .NET Framework. 

Permite passar para discutir **Metadata. xml** mais detalhadamente.

## <a name="metadataxml-transform-file"></a>Arquivo de transformação Metadata. xml

Como já aprendimos, o arquivo **Metadata. xml** é usado pelo gerador de associações para influenciar a criação do assembly de associação.
O formato de metadados usa a sintaxe [XPath](https://www.w3.org/TR/xpath/) e é quase idêntico aos *metadados GAPI* descritos no guia de [metadados do GAPI](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata) . Essa implementação é quase uma implementação completa do XPath 1,0 e, portanto, dá suporte a itens no padrão 1,0. Esse arquivo é um poderoso mecanismo baseado em XPath para alterar, adicionar, ocultar ou mover qualquer elemento ou atributo no arquivo de API. Todos os elementos de regra na especificação de metadados incluem um atributo de caminho para identificar o nó ao qual a regra deve ser aplicada. As regras são aplicadas na seguinte ordem:

- **adicionar nó** &ndash; acrescenta um nó filho ao nó especificado pelo atributo Path.
- **attr** &ndash; define o valor de um atributo do elemento especificado pelo atributo Path.
- Remove **-node** &ndash; remove nós que correspondem a um XPath especificado.

Veja a seguir um exemplo de um arquivo **Metadata. xml** :

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

Veja a seguir uma lista de alguns dos elementos XPath usados com mais frequência para as APIs do Java:

- `interface` &ndash; usado para localizar uma interface java. por exemplo, `/interface[@name='AuthListener']`.

- `class` &ndash; usado para localizar uma classe. por exemplo, `/class[@name='MapView']`.

- `method` &ndash; usado para localizar um método em uma classe ou interface java. por exemplo, `/class[@name='MapView']/method[@name='setTitleSource']`.

- `parameter` &ndash; identificar um parâmetro para um método. por exemplo, `/parameter[@name='p0']`

### <a name="adding-types"></a>Adicionando tipos

O elemento `add-node` informará ao projeto de associação Xamarin. Android para adicionar uma nova classe wrapper a **API. xml**. Por exemplo, o trecho a seguir direcionará o gerador de associação para criar uma classe com um construtor e um único campo:

```xml
<add-node path="/api/package[@name='org.alljoyn.bus']">
    <class abstract="false" deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="true" visibility="public" extends="java.lang.Object">
        <constructor deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="false" type="org.alljoyn.bus.AuthListener.AuthRequest" visibility="public" />
        <field name="p0" type="org.alljoyn.bus.AuthListener.Credentials" />
    </class>
</add-node>
```

### <a name="removing-types"></a>Removendo tipos

É possível instruir o gerador de associações do Xamarin. Android para ignorar um tipo Java e não associá-lo. Isso é feito adicionando um elemento XML `remove-node` ao arquivo **Metadata. xml** :

```xml
<remove-node path="/api/package[@name='{package_name}']/class[@name='{name}']" />
```

### <a name="renaming-members"></a>Renomeando Membros

A renomeação de membros não pode ser feita editando diretamente o arquivo **API. xml** , pois o Xamarin. Android requer os nomes de JNI (interface nativa do Java) originais. Portanto, o atributo `//class/@name` não pode ser alterado; Se for, a associação não funcionará.

Considere o caso em que desejamos renomear um tipo, `android.Manifest`.
Para fazer isso, podemos tentar editar o **API. xml** diretamente e renomear a classe da seguinte forma:

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="name">NewName</attr>
```

Isso fará com que o gerador de associações crie o seguinte C# código para a classe de wrapper:

```csharp
[Register ("android/NewName")]
public class NewName : Java.Lang.Object { ... }
```

Observe que a classe wrapper foi renomeada para `NewName`, enquanto o tipo Java original ainda `Manifest`. Não é mais possível que a classe de associação Xamarin. Android acesse quaisquer métodos em `android.Manifest`; a classe wrapper está associada a um tipo de Java não existente.

Para alterar corretamente o nome gerenciado de um tipo encapsulado (ou método), é necessário definir o atributo `managedName`, conforme mostrado neste exemplo:

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="managedName">NewName</attr>
```

<a name="Renaming_EventArg_Wrapper_Classes" />

#### <a name="renaming-eventarg-wrapper-classes"></a>Renomeando `EventArg` classes de wrapper

Quando o gerador de associação do Xamarin. Android identifica um método setter `onXXX` para um tipo de C# _ouvinte_, um evento e uma subclasse de `EventArgs` serão gerados para dar suporte a uma API .net flavoured para o padrão de ouvinte baseado em Java. Como exemplo, considere a classe Java e o método a seguir:

```xml
com.someapp.android.mpa.guidance.NavigationManager.on2DSignNextManuever(NextManueverListener listener);
```

O Xamarin. Android removerá o prefixo `on` do método setter e, em vez disso, usará `2DSignNextManuever` como base para o nome da subclasse `EventArgs`. A subclasse terá um nome semelhante a:

```csharp
NavigationManager.2DSignNextManueverEventArgs
```

Este não é um nome C# de classe legal. Para corrigir esse problema, o autor da associação deve usar o atributo `argsType` e fornecer um C# nome válido para a subclasse `EventArgs`:

```xml
<attr path="/api/package[@name='com.someapp.android.mpa.guidance']/
    interface[@name='NavigationManager.Listener']/
    method[@name='on2DSignNextManeuver']" 
    name="argsType">NavigationManager.TwoDSignNextManueverEventArgs</attr>
```

## <a name="supported-attributes"></a>Atributos com suporte

As seções a seguir descrevem alguns dos atributos para transformar APIs Java.

### <a name="argstype"></a>argstype

Esse atributo é colocado em métodos setter para nomear a subclasse `EventArg` que será gerada para dar suporte a ouvintes Java. Isso é descrito em mais detalhes abaixo na seção [renomeando as classes de wrapper eventArg](#Renaming_EventArg_Wrapper_Classes) posteriormente neste guia.

### <a name="eventname"></a>eventName

Especifica um nome para um evento. Se estiver vazio, ele inibe a geração de eventos.
Isso é descrito mais detalhadamente na seção título [renomeando classes de wrapper eventArg](#Renaming_EventArg_Wrapper_Classes).

### <a name="managedname"></a>managedname

Isso é usado para alterar o nome de um pacote, classe, método ou parâmetro. Por exemplo, para alterar o nome da classe Java `MyClass` para `NewClassName`:

```xml
<attr path="/api/package[@name='com.my.application']/class[@name='MyClass']" 
    name="managedName">NewClassName</attr>
```

O exemplo a seguir ilustra uma expressão XPath para renomear o método `java.lang.object.toString` para `Java.Lang.Object.NewManagedName`:

```xml
<attr path="/api/package[@name='java.lang']/class[@name='Object']/method[@name='toString']" 
    name="managedName">NewMethodName</attr>
```

### <a name="managedtype"></a>ManagedType

`managedType` é usado para alterar o tipo de retorno de um método. Em algumas situações, o gerador de associações inferirá incorretamente o tipo de retorno de um método Java, o que resultará em um erro de tempo de compilação. Uma solução possível nessa situação é alterar o tipo de retorno do método.

Por exemplo, o gerador de associações acredita que o método Java `de.neom.neoreadersdk.resolution.compareTo()` deve retornar um `int`, o que resulta na mensagem de erro **erro CS0535: ' de. NEOM. Neoreadersdk. Resolution ' não implementa o membro de interface ' Java. lang. IComparable. CompareTo (Java. lang. Object) '** . O trecho a seguir demonstra como alterar o tipo de parâmetro do método C# gerado de um`DE.Neom.Neoreadersdk.Resolution`para um `Java.Lang.Object`: 

```xml
<attr path="/api/package[@name='de.neom.neoreadersdk']/
    class[@name='Resolution']/
    method[@name='compareTo' and count(parameter)=1 and
    parameter[1][@type='de.neom.neoreadersdk.Resolution']]/
    parameter[1]" name="managedType">Java.Lang.Object</attr> 
```

### <a name="managedreturn"></a>managedReturn

Altera o tipo de retorno de um método. Isso não altera o atributo de retorno (pois as alterações nos atributos de retorno podem resultar em alterações incompatíveis na assinatura JNI). No exemplo a seguir, o tipo de retorno do método `append` é alterado de `SpannableStringBuilder` para `IAppendable` (lembre- C# se de que o não oferece suporte a tipos de retorno covariantes):

```xml
<attr path="/api/package[@name='android.text']/
    class[@name='SpannableStringBuilder']/
    method[@name='append']" 
    name="managedReturn">Java.Lang.IAppendable</attr>
```

### <a name="obfuscated"></a>ofuscados

As ferramentas que ofuscam as bibliotecas Java podem interferir no gerador de associação do Xamarin. Android e sua C# capacidade de gerar classes de wrapper. As características das classes ofuscadas incluem: 

- O nome da classe inclui um **$** , ou seja, **uma classe $.**
- O nome da classe está totalmente comprometido com caracteres minúsculos, ou seja, **a. Class**

Este trecho de código é um exemplo de como gerar um tipo "não ofuscado C# ":

```xml
<attr path="/api/package[@name='{package_name}']/class[@name='{name}']" 
    name="obfuscated">false</attr>
```

### <a name="propertyname"></a>propertyName

Esse atributo pode ser usado para alterar o nome de uma propriedade gerenciada.

Um caso especializado de usar `propertyName` envolve a situação em que uma classe Java tem apenas um método getter para um campo. Nessa situação, o gerador de associação desejaria criar uma propriedade somente gravação, algo desencorajado no .NET. O trecho a seguir mostra como "remover" as propriedades do .NET definindo o `propertyName` como uma cadeia de caracteres vazia:

```xml
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='setResourceDescriptor' 
    and count(parameter)=1 
    and parameter[1][@type='java.lang.String']]" 
    name="propertyName"></attr>
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='getResourceDescriptor' 
    and count(parameter)=0]" 
    name="propertyName"></attr>
```

Observe que os métodos setter e getter ainda serão criados pelo gerador de associações.

### <a name="sender"></a>Sender

Especifica qual parâmetro de um método deve ser o `sender` parâmetro quando o método é mapeado para um evento. O valor pode ser `true` ou `false`. Por exemplo:

```xml
<attr path="/api/package[@name='android.app']/
    interface[@name='TimePickerDialog.OnTimeSetListener']/
    method[@name='onTimeSet']/
    parameter[@name='view']" 
    name="sender">true</ attr>
```

### <a name="visibility"></a>visibilidade

Esse atributo é usado para alterar a visibilidade de uma classe, método ou propriedade. Por exemplo, pode ser necessário promover um `protected` método Java para que o wrapper correspondente C# seja`public`:

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method --> 
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

## <a name="enumfieldsxml-and-enummethodsxml"></a>EnumFields. xml e EnumMethods. xml

Há casos em que as bibliotecas do Android usam constantes inteiras para representar Estados que são passados para propriedades ou métodos das bibliotecas. Em muitos casos, é útil associar essas constantes de inteiro a enums no C#. Para facilitar esse mapeamento, use os arquivos **EnumFields. xml** e **EnumMethods. xml** em seu projeto de associação. 

### <a name="defining-an-enum-using-enumfieldsxml"></a>Definindo uma enumeração usando EnumFields. xml

O arquivo **EnumFields. xml** contém o mapeamento entre as constantes `int` Java C# e`enums`. Vamos pegar o exemplo a seguir de um C# enum que está sendo criado para um conjunto de`int`constantes: 

```xml 
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit">
    <field jni-name="UNIT_SECOND" clr-name="Second" value="0" />
    <field jni-name="UNIT_METER" clr-name="Meter" value="1" />
    <field jni-name="UNIT_MILIWATT_HOURS" clr-name="MilliwattHour" value="2" />
</mapping>
```

Aqui, tiramos a classe Java `SKRealReachSettings` e definimos C# uma enumeração chamada`SKMeasurementUnit`no namespace`Skobbler.Ngx.Map.RealReach`. As entradas de `field` definem o nome da constante Java (exemplo `UNIT_SECOND`), o nome da entrada de enumeração (exemplo `Second`) e o valor inteiro representado por ambas as entidades (exemplo `0`). 

### <a name="defining-gettersetter-methods-using-enummethodsxml"></a>Definindo métodos getter/setter usando EnumMethods. xml

O arquivo **EnumMethods. xml** permite a alteração de parâmetros de método e tipos de retorno de C# constantes de `int` Java para`enums`. Em outras palavras, ele mapeia a leitura e a gravação C# de enums (definidas no arquivo **EnumFields. xml** ) para Java `int`constante`get`e métodos de`set`.

Considerando a `SKRealReachSettings` Enumeração definida acima, o seguinte arquivo **EnumMethods. xml** definiria o getter/setter para esta enumeração:

```xml
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings">
    <method jni-name="getMeasurementUnit" parameter="return" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
    <method jni-name="setMeasurementUnit" parameter="measurementUnit" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
</mapping>
```

A primeira linha de `method` mapeia o valor de retorno do método Java `getMeasurementUnit` para o `SKMeasurementUnit` enum. A segunda linha de `method` mapeia o primeiro parâmetro do `setMeasurementUnit` para o mesmo enum.

Com todas essas alterações em vigor, você pode usar o código a seguir no Xamarin. Android para definir o `MeasurementUnit`: 

```csharp
realReachSettings.MeasurementUnit = SKMeasurementUnit.Second;
```

## <a name="summary"></a>Resumo

Este artigo abordou como o Xamarin. Android usa metadados para transformar uma definição de API do formato *Google* *AOSP*. Depois de abranger as alterações que são possíveis usando *Metadata. xml*, ele examinou as limitações encontradas ao renomear Membros e apresentou a lista de atributos XML com suporte, descrevendo quando cada atributo deve ser usado.

## <a name="related-links"></a>Links relacionados

- [Trabalhando com JNI](~/android/platform/java-integration/working-with-jni.md)
- [Associação de uma biblioteca Java](~/android/platform/binding-java-library/index.md)
- [Metadados do GAPI](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
