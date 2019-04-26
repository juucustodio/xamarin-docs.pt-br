---
title: Metadados de associações de Java
description: C#o código no xamarin. Android chama bibliotecas Java por meio de associações, que são um mecanismo que abstrai os detalhes de baixo nível que são especificados no Java JNI (Interface nativa). Xamarin. Android fornece uma ferramenta que gera essas associações. Essa ferramenta permite que o controle do desenvolvedor como uma associação é criada por meio de metadados, que permite que os procedimentos, como namespaces de modificar e renomear os membros. Este documento discute como funciona a metadados, resume os atributos que os metadados dá suporte e explica como resolver problemas de associação, modificando a esses metadados.
ms.prod: xamarin
ms.assetid: 27CB3C16-33F3-F580-E2C0-968005A7E02E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 06a7a3b00934b7a2f3eeb4fcfa6fc90071901ba0
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60955650"
---
# <a name="java-bindings-metadata"></a>Metadados de associações de Java

_C#o código no xamarin. Android chama bibliotecas Java por meio de associações, que são um mecanismo que abstrai os detalhes de baixo nível que são especificados no Java JNI (Interface nativa). Xamarin. Android fornece uma ferramenta que gera essas associações. Essa ferramenta permite que o controle do desenvolvedor como uma associação é criada por meio de metadados, que permite que os procedimentos, como namespaces de modificar e renomear os membros. Este documento discute como funciona a metadados, resume os atributos que os metadados dá suporte e explica como resolver problemas de associação, modificando a esses metadados._


## <a name="overview"></a>Visão geral

Um xamarin. Android **biblioteca de associações de Java** tenta automatizar grande parte do trabalho necessário para associar uma biblioteca Android existente com a Ajuda de uma ferramenta que às vezes conhecida como o _gerador de associações_. Ao associar uma biblioteca Java, xamarin. Android irá inspecionar as classes Java e gerar uma lista de todos os pacotes, tipos e membros que deve ser associado. Essa lista de APIs é armazenada em um arquivo XML que pode ser encontrado em  **\{projeto directory}\obj\Release\api.xml** para um **versão** build e em  **\{projeto Directory}\obj\Debug\api.XML** para um **depurar** compilar.

![Local do arquivo na pasta obj/Debug api.xml](java-bindings-metadata-images/java-bindings-metadata-01.png)

O gerador de associações usará o **api.xml** arquivo como uma diretriz para gerar o necessário C# classes de wrapper. O conteúdo desse arquivo XML é uma variação do Google _Android Open Source Project_ formato.
O trecho a seguir está um exemplo do conteúdo do **api.xml**:

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

Neste exemplo, **api.xml** declara uma classe na `android` pacote denominado `Manifest` que se estende a `java.lang.Object`.

Em muitos casos, assistência humana é necessária para fazer com que a API do Java se sentir mais ".NET, como" ou para corrigir problemas que impedem o assembly de associação da compilação. Por exemplo, pode ser necessário alterar os nomes de pacote de Java para namespaces do .NET, uma classe de renomear ou alterar o tipo de retorno de um método.

Essas alterações não são obtidas por meio da modificação **api.xml** diretamente.
Em vez disso, as alterações são registradas em arquivos XML especiais que são fornecidos pelo modelo de biblioteca de vinculação de Java. Ao compilar o assembly de associação do xamarin. Android, o gerador de associações será influenciado por esses arquivos de mapeamento ao criar o assembly de associação

Esses arquivos de mapeamento XML podem ser encontrados na **transforma** pasta do projeto:

-   **Metadata** &ndash; permite que alterações sejam feitas para a API final, como alterar o namespace da associação gerada. 

-   **EnumFields.xml** &ndash; contém o mapeamento entre o Java `int` constantes e C# `enums` . 

-   **EnumMethods.xml** &ndash; permite alterar os parâmetros de método e tipos de retorno do Java `int` constantes para C# `enums` . 

O **Metadata** arquivo é mais a importação desses arquivos, pois permite que as alterações de finalidade geral para a associação, como:

-   Renomear namespaces, classes, métodos ou campos para que eles seguem as convenções do .NET. 

-   Removendo namespaces, classes, métodos ou campos que não são necessários. 

-   Movendo classes a namespaces diferentes. 

-   Adicionando classes de suporte adicional para o design da associação siga os padrões do .NET framework. 

Permite passar para discutir **Metadata** em mais detalhes.


## <a name="metadataxml-transform-file"></a>Arquivo de transformação de Metadata. XML

Conforme já aprendemos, o arquivo **Metadata** é usada pelo gerador de associações para influenciar a criação do assembly de associação.
Usa o formato de metadados [XPath](https://www.w3.org/TR/xpath/) sintaxe e é quase idêntico de *GAPI metadados* descrito na [GAPI metadados](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata) guia. Essa implementação é quase uma implementação completa do XPath 1.0 e, portanto, dá suporte a itens no 1.0 padrão. Esse arquivo é um mecanismo XPath com base em Avançado para alterar, adicionar, ocultar ou mover qualquer elemento ou atributo no arquivo de API. Todos os elementos de regra nas especificações de metadados incluem um atributo de caminho para identificar o nó ao qual a regra deve ser aplicada. As regras são aplicadas na seguinte ordem:

* **Adicionar nó** &ndash; acrescenta um nó filho para o nó especificado pelo atributo de caminho.
* **attr** &ndash; define o valor de um atributo do elemento especificado pelo atributo de caminho.
* **Remover nó** &ndash; remove nós que correspondem um XPath especificado.

A seguir está um exemplo de uma **Metadata** arquivo:

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

A seguinte lista alguns dos elementos do XPath mais comumente usados para a API de Java:

-   `interface` &ndash; Usado para localizar uma interface Java. Por exemplo, `/interface[@name='AuthListener']`.

-   `class` &ndash; Usado para localizar uma classe. Por exemplo, `/class[@name='MapView']`.

-   `method` &ndash; Usado para localizar um método em uma classe de Java ou interface. Por exemplo, `/class[@name='MapView']/method[@name='setTitleSource']`.

-   `parameter` &ndash; Identifica um parâmetro para um método. Por exemplo `/parameter[@name='p0']`



### <a name="adding-types"></a>Adição de tipos

O `add-node` elemento informará o projeto de associação do xamarin. Android para adicionar uma nova classe de wrapper para **api.xml**. Por exemplo, o trecho a seguir direcionará o gerador de associação para criar uma classe com um construtor e um único campo:

```xml
<add-node path="/api/package[@name='org.alljoyn.bus']">
    <class abstract="false" deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="true" visibility="public" extends="java.lang.Object">
        <constructor deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="false" type="org.alljoyn.bus.AuthListener.AuthRequest" visibility="public" />
        <field name="p0" type="org.alljoyn.bus.AuthListener.Credentials" />
    </class>
</add-node>
```


### <a name="removing-types"></a>Removendo tipos

É possível instruir o gerador de associações do xamarin. Android para ignorar um tipo de Java e não a associá-lo. Isso é feito pela adição de um `remove-node` elemento XML para o **Metadata** arquivo:

```xml
<remove-node path="/api/package[@name='{package_name}']/class[@name='{name}']" />
```

### <a name="renaming-members"></a>Renomear os membros

Renomear os membros não pode ser feito editando diretamente o **api.xml** arquivo porque o xamarin. Android exige que os nomes originais do Java JNI (Interface nativa). Portanto, o `//class/@name` atributo não pode ser alterado; se for, a associação não funcionará.

Considere o caso em que desejamos renomear um tipo `android.Manifest`.
Para fazer isso, podemos tentar editar diretamente **api.xml** e renomeie a classe desta forma:

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="name">NewName</attr>
```

Isso resultará no gerador de associações de criar as seguintes C# código para a classe de wrapper:

```csharp
[Register ("android/NewName")]
public class NewName : Java.Lang.Object { ... }
```

Observe que a classe de wrapper foi renomeada para `NewName`, enquanto o tipo Java original ainda é `Manifest`. Ele não é mais possível para a classe de associação do xamarin. Android acessar todos os métodos no `android.Manifest`; a classe de wrapper é associada a um tipo de Java inexistente.

Para alterar corretamente o nome gerenciado de um tipo encapsulado (ou método), é necessário definir o `managedName` atributo conforme mostrado neste exemplo:

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="managedName">NewName</attr>
```

<a name="Renaming_EventArg_Wrapper_Classes" />

#### <a name="renaming-eventarg-wrapper-classes"></a>Renomeando `EventArg` Classes Wrapper

Quando o gerador de associação do xamarin. Android identifica um `onXXX` método setter para um _tipo de ouvinte_, um C# eventos e `EventArgs` subclasse será gerada dar suporte a um .NET flavoured API para o baseado em Java padrão de ouvinte. Por exemplo, considere a seguinte classe de Java e o método:

```xml
com.someapp.android.mpa.guidance.NavigationManager.on2DSignNextManuever(NextManueverListener listener);
```

Xamarin. Android descartará o prefixo `on` do método setter e, em vez disso, use `2DSignNextManuever` como a base para o nome da `EventArgs` subclasse. A subclasse será nomeada algo semelhante a:

```csharp
NavigationManager.2DSignNextManueverEventArgs
```

Isso não é um legal C# nome de classe. Para corrigir esse problema, o autor de associação deve usar o `argsType` de atributo e fornecer um válido C# nome para a `EventArgs` subclasse:
 
```xml
<attr path="/api/package[@name='com.someapp.android.mpa.guidance']/
    interface[@name='NavigationManager.Listener']/
    method[@name='on2DSignNextManeuver']" 
    name="argsType">NavigationManager.TwoDSignNextManueverEventArgs</attr>
```

 

## <a name="supported-attributes"></a>Atributos com suporte

As seções a seguir descrevem alguns dos atributos para transformar as APIs de Java.

### <a name="argstype"></a>argsType

Esse atributo é colocado em métodos setter para nomear o `EventArg` subclasse que será gerado para dar suporte a Java ouvintes. Isso é descrito mais detalhadamente abaixo na seção [renomeação de Classes de Wrapper EventArg](#Renaming_EventArg_Wrapper_Classes) posteriormente neste guia.

### <a name="eventname"></a>eventName

Especifica um nome para um evento. Se estiver vazio, inibe a geração de eventos.
Isso é descrito mais detalhadamente no título da seção [renomeação de Classes de Wrapper EventArg](#Renaming_EventArg_Wrapper_Classes).

### <a name="managedname"></a>managedName

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

`managedType` é usado para alterar o tipo de retorno de um método. Em algumas situações o gerador de associações incorretamente irá inferir o tipo de retorno de um método de Java, o que resultará em um erro de tempo de compilação. Uma possível solução nessa situação é alterar o tipo de retorno do método.

Por exemplo, o gerador de associações acredita que o método Java `de.neom.neoreadersdk.resolution.compareTo()` deve retornar um `int`, que resulta na mensagem de erro **CS0535 do erro: ' ALEMANHA. Neom.Neoreadersdk.Resolution' não implementa membro de interface 'Java.Lang.IComparable.CompareTo(Java.Lang.Object)'**. O trecho a seguir demonstra como alterar o tipo de retorno de gerado C# método de um `int` para um `Java.Lang.Object`: 

```xml
<attr path="/api/package[@name='de.neom.neoreadersdk']/
    class[@name='Resolution']/
    method[@name='compareTo' and count(parameter)=1 and
    parameter[1][@type='de.neom.neoreadersdk.Resolution']]/
    parameter[1]"name="managedType">Java.Lang.Object</attr> 
```

### <a name="managedreturn"></a>managedReturn

Altera o tipo de retorno de um método. Isso não altera o atributo de retorno (como as alterações para retornar os atributos podem resultar em alterações incompatíveis para a assinatura JNI). No exemplo a seguir, o tipo de retorno do `append` método é alterado de `SpannableStringBuilder` à `IAppendable` (Lembre-se de que C# não oferece suporte a tipos de retorno covariante):

```xml
<attr path="/api/package[@name='android.text']/
    class[@name='SpannableStringBuilder']/
    method[@name='append']" 
    name="managedReturn">Java.Lang.IAppendable</attr>
```

### <a name="obfuscated"></a>ofuscado

Ferramentas de que o ofuscar bibliotecas Java podem interferir com o gerador de associação do xamarin. Android e sua capacidade de gerar C# classes de wrapper. As características das classes ofuscadas incluem: 

* O nome de classe inclui uma **$**, ou seja, **um. ' $Class**
* O nome de classe seja comprometido inteiramente de caracteres em letras minúsculas, ou seja, **a.class**

Este trecho de código é um exemplo de como gerar um "não ofuscado" C# tipo:

```xml
<attr path="/api/package[@name='{package_name}']/class[@name='{name}']" 
    name="obfuscated">false</attr>
```

### <a name="propertyname"></a>propertyName

Esse atributo pode ser usado para alterar o nome de uma propriedade gerenciada.

Um caso especializado do uso de `propertyName` envolve a situação em que uma classe Java tem apenas um método getter para um campo. Nessa situação, o gerador de associação deseja criar uma propriedade somente gravação, algo que não é recomendado no .NET. O trecho a seguir mostra como "remover" as propriedades do .NET, definindo o `propertyName` para uma cadeia de caracteres vazia:

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

### <a name="sender"></a>Remetente

Especifica qual parâmetro de um método deve ser o `sender` parâmetro quando o método é mapeado para um evento. O valor pode ser `true` ou `false`. Por exemplo:

```xml
<attr path="/api/package[@name='android.app']/
    interface[@name='TimePickerDialog.OnTimeSetListener']/
    method[@name='onTimeSet']/
    parameter[@name='view']" 
    name="sender">true</ attr>
```

### <a name="visibility"></a>visibilidade

Esse atributo é usado para alterar a visibilidade de uma classe, método ou propriedade. Por exemplo, pode ser necessário promover uma `protected` método Java para que ele correspondente do C# é do wrapper `public`:

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method --> 
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

## <a name="enumfieldsxml-and-enummethodsxml"></a>EnumFields.xml e EnumMethods.xml

Há casos em que o Android bibliotecas usam constantes de inteiro para representar estados que são passados para propriedades ou métodos das bibliotecas. Em muitos casos, é útil vincular essas constantes de inteiro para enums em C#. Para facilitar esse mapeamento, use o **EnumFields.xml** e **EnumMethods.xml** arquivos em seu projeto de associação. 

### <a name="defining-an-enum-using-enumfieldsxml"></a>Definindo uma enumeração usando EnumFields.xml

O **EnumFields.xml** arquivo contém o mapeamento entre o Java `int` constantes e C# `enums`. Vejamos o exemplo a seguir de um C# enum que está sendo criado para um conjunto de `int` constantes: 

```xml 
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit">
    <field jni-name="UNIT_SECOND" clr-name="Second" value="0" />
    <field jni-name="UNIT_METER" clr-name="Meter" value="1" />
    <field jni-name="UNIT_MILIWATT_HOURS" clr-name="MilliwattHour" value="2" />
</mapping>
```

Aqui é que nós tiramos a classe de Java `SKRealReachSettings` e definido um C# enum chamado `SKMeasurementUnit` no namespace `Skobbler.Ngx.Map.RealReach`. O `field` entradas define o nome da constante Java (exemplo `UNIT_SECOND`), o nome da entrada de enumeração (exemplo `Second`) e o valor de inteiro representado por ambas as entidades (exemplo `0`). 

### <a name="defining-gettersetter-methods-using-enummethodsxml"></a>Definindo os métodos Getter/Setter usando EnumMethods.xml

O **EnumMethods.xml** arquivo permite alterar os parâmetros de método e tipos de retorno do Java `int` constantes para C# `enums`. Em outras palavras, ele mapeia a leitura e gravação de C# enums (definidos na **EnumFields.xml** arquivo) para Java `int` constante `get` e `set` métodos.

Dada a `SKRealReachSettings` enum definido acima, o seguinte **EnumMethods.xml** arquivo definiria o getter/setter para essa enum:

```xml
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings">
    <method jni-name="getMeasurementUnit" parameter="return" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
    <method jni-name="setMeasurementUnit" parameter="measurementUnit" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
</mapping>
```

A primeira `method` linha mapeia o valor retornado de Java `getMeasurementUnit` método para o `SKMeasurementUnit` enum. A segunda `method` linha mapeia o primeiro parâmetro do `setMeasurementUnit` à mesma enumeração.

Com todas essas alterações realizadas, você pode usar o código a seguir no xamarin. Android para definir o `MeasurementUnit`: 

```csharp
realReachSettings.MeasurementUnit = SKMeasurementUnit.Second;
```


## <a name="summary"></a>Resumo

Esse artigo discutiu como o xamarin. Android usa metadados para transformar uma definição de API do *Google* *formato AOSP*. Depois de abordar as alterações que são possíveis usando *Metadata*, ele examinou as limitações encontradas ao renomear os membros e ela apresentada a lista de atributos com suporte do XML, que descreve quando cada atributo deve ser usado.



## <a name="related-links"></a>Links relacionados

- [Trabalhar com JNI](~/android/platform/java-integration/working-with-jni.md)
- [Associação de uma biblioteca Java](~/android/platform/binding-java-library/index.md)
- [Metadados GAPI](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
