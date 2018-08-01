---
title: Java Bindings Metadata
description: O código c# em xamarin chama bibliotecas Java por meio de ligações, que são um mecanismo que abstrai os detalhes de nível inferior que são especificados em Java nativo Interface (JNI). Xamarin fornece uma ferramenta que gera essas associações. Essa ferramenta permite que o controle do desenvolvedor como uma associação é criada usando metadados, que permite que os procedimentos, como modificar os namespaces e renomeação de membros. Este documento discute como funciona a metadados, resume os atributos que os metadados oferece suporte e explica como resolver problemas de associação, modificando esses metadados.
ms.prod: xamarin
ms.assetid: 27CB3C16-33F3-F580-E2C0-968005A7E02E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: 6dea13fcda43cad22b8bea9838bbcb23b97820c7
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30771012"
---
# <a name="java-bindings-metadata"></a>Java Bindings Metadata

_O código c# em xamarin chama bibliotecas Java por meio de ligações, que são um mecanismo que abstrai os detalhes de nível inferior que são especificados em Java nativo Interface (JNI). Xamarin fornece uma ferramenta que gera essas associações. Essa ferramenta permite que o controle do desenvolvedor como uma associação é criada usando metadados, que permite que os procedimentos, como modificar os namespaces e renomeação de membros. Este documento discute como funciona a metadados, resume os atributos que os metadados oferece suporte e explica como resolver problemas de associação, modificando esses metadados._


## <a name="overview"></a>Visão geral

Um xamarin **biblioteca de ligação de Java** tenta automatizar grande parte do trabalho necessário para a associação de uma biblioteca Android existente com a Ajuda de uma ferramenta que às vezes conhecida como o _gerador de associações_. Ao associar uma biblioteca Java, xamarin será inspecionar as classes Java e gerar uma lista de todos os pacotes, tipos e membros que deve ser vinculado. Esta lista de APIs é armazenada em um arquivo XML que pode ser encontrado em  **\{projeto directory}\obj\Release\api.xml** para um **versão** de compilação e no  **\{projeto Directory}\obj\Debug\api.XML** para um **depurar** de compilação.

![Local do arquivo na pasta obj/Debug api.xml](java-bindings-metadata-images/java-bindings-metadata-01.png)

O gerador de associações usará o **api.xml** arquivo como uma diretriz para gerar as classes de wrapper c# necessárias. O conteúdo do arquivo XML é uma variação do Google _Android Abrir projeto de origem_ formato.
O trecho a seguir é um exemplo do conteúdo do **api.xml**:

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

Neste exemplo, **api.xml** declara uma classe no `android` pacote denominado `Manifest` que estende o `java.lang.Object`.

Em muitos casos, assistência humana é necessária para fazer com que a API do Java se sentir mais ".NET como" ou corrigir problemas que impedem que o assembly de associação de compilação. Por exemplo, pode ser necessário alterar os nomes de pacote Java para namespaces do .NET, uma classe de renomear ou alterar o tipo de retorno de um método.

Essas alterações não são obtidas por meio de modificação **api.xml** diretamente.
Em vez disso, as alterações são gravadas em arquivos XML especiais que são fornecidos pelo modelo de biblioteca de ligação de Java. Ao compilar o assembly de associação xamarin, o gerador de associações será influenciado por esses arquivos de mapeamento ao criar o assembly de associação

Esses arquivos de mapeamento XML podem ser encontrados no **transforma** pasta do projeto:

-   **MetaData.xml** &ndash; permite que as alterações sejam feitas para a API final, como alterar o namespace da associação gerado. 

-   **EnumFields.xml** &ndash; contém o mapeamento entre Java `int` constantes e c# `enums` . 

-   **EnumMethods.xml** &ndash; permite alterar os parâmetros de método e tipos de retorno do Java `int` constantes c# `enums` . 

O **MetaData.xml** arquivo é mais importação desses arquivos, pois permite alterações para fins gerais para a associação, como:

-   Renomear namespaces, classes, métodos ou campos para que sigam as convenções do .NET. 

-   Removendo namespaces, classes, métodos ou campos que não são necessários. 

-   Mover classes para namespaces diferentes. 

-   Adicionando classes de suporte adicional para o design da associação siga padrões do .NET framework. 

Permite passar para discutir **Metadata.xml** mais detalhadamente.


## <a name="metadataxml-transform-file"></a>Arquivo de transformação de Metadata.XML

Como já aprendemos, o arquivo **Metadata.xml** é usada pelo gerador de associações para influenciar a criação do assembly de associação.
Usa o formato de metadados [XPath](https://www.w3.org/TR/xpath/) sintaxe e é praticamente idêntica do *GAPI metadados* descrito em [GAPI metadados](http://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata) guia. Essa implementação é quase uma implementação completa de XPath 1.0 e, portanto, dá suporte a itens no 1.0 padrão. Esse arquivo é um mecanismo XPath com base em Avançado para alterar, adicionar, ocultar ou mover qualquer elemento ou atributo no arquivo de API. Todos os elementos de regra nas especificações do metadados incluem um atributo de caminho para identificar o nó ao qual a regra a ser aplicado. As regras são aplicadas na seguinte ordem:

* **Adicionar nó** &ndash; acrescenta um nó filho para o nó especificado pelo atributo path.
* **attr** &ndash; define o valor de um atributo do elemento especificado pelo atributo de caminho.
* **Remover nó** &ndash; remove nós que correspondem a um XPath especificado.

A seguir está um exemplo de um **Metadata.xml** arquivo:

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

A seguinte lista alguns dos elementos usados com mais frequência do XPath para a API do Java:

-   `interface` &ndash; Usado para localizar uma interface de Java. Por exemplo, `/interface[@name='AuthListener']`.

-   `class` &ndash; Usado para localizar uma classe. Por exemplo, `/class[@name='MapView']`.

-   `method` &ndash; Usado para localizar um método em uma interface ou classe Java. Por exemplo, `/class[@name='MapView']/method[@name='setTitleSource']`.

-   `parameter` &ndash; Identifique um parâmetro para um método. Por exemplo `/parameter[@name='p0']`



### <a name="adding-types"></a>Adicionando tipos

O `add-node` elemento informará o projeto de associação xamarin para adicionar uma nova classe wrapper para **api.xml**. Por exemplo, o trecho a seguir direcionará o gerador de associação para criar uma classe com um construtor e um único campo:

```xml
<add-node path="/api/package[@name='org.alljoyn.bus']">
    <class abstract="false" deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="true" visibility="public" extends="java.lang.Object">
        <constructor deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="false" type="org.alljoyn.bus.AuthListener.AuthRequest" visibility="public" />
        <field name="p0" type="org.alljoyn.bus.AuthListener.Credentials" />
    </class>
</add-node>
```


### <a name="removing-types"></a>Removendo tipos

É possível instruir o gerador de associações xamarin para ignorar um tipo Java e não a associá-lo. Isso é feito adicionando uma `remove-node` elemento XML para o **metadata.xml** arquivo:

```xml
<remove-node path="/api/package[@name='{package_name}']/class[@name='{name}']" />
```

### <a name="renaming-members"></a>Renomear membros

Renomear membros não pode ser feito editando diretamente o **api.xml** o arquivo porque o xamarin requer que os nomes de Java nativo Interface (JNI) original. Portanto, o `//class/@name` atributo não pode ser alterado; se for, a associação não funcionará.

Considere o caso em que desejamos para renomear um tipo `android.Manifest`.
Para fazer isso, podemos tentar editar diretamente **api.xml** e renomeie a classe da seguinte forma:

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="name">NewName</attr>
```

Isso fará com que o gerador de associações criando o seguinte código c# para a classe de wrapper:

```csharp
[Register ("android/NewName")]
public class NewName : Java.Lang.Object { ... }
```

Observe que a classe de invólucro foi renomeada para `NewName`, enquanto o tipo de Java original ainda é `Manifest`. Não é possível para a classe de associação xamarin acessar todos os métodos em `android.Manifest`; a classe de invólucro está associada a um tipo de Java inexistente.

Para alterar corretamente o nome gerenciado de um tipo encapsulado (ou método), é necessário definir o `managedName` atributo conforme mostrado neste exemplo:

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="managedName">NewName</attr>
```

<a name="Renaming_EventArg_Wrapper_Classes" />

#### <a name="renaming-eventarg-wrapper-classes"></a>Renomeando `EventArg` Classes Wrapper

Quando o gerador de associação xamarin identifica um `onXXX` método setter para um _tipo de ouvinte_, um evento em c# e `EventArgs` subclasse será gerada dar suporte ao .NET flavoured API para o ouvinte baseado em Java padrão. Por exemplo, considere a seguinte classe Java e o método:

```xml
com.someapp.android.mpa.guidance.NavigationManager.on2DSignNextManuever(NextManueverListener listener);
```

Xamarin descartará o prefixo `on` do método setter e, em vez disso, use `2DSignNextManuever` como base para o nome do `EventArgs` subclasse. A subclasse será nomeada algo semelhante a:

```csharp
NavigationManager.2DSignNextManueverEventArgs
```

Isso não é um nome de classe c# legal. Para corrigir esse problema, o autor de associação deve usar o `argsType` de atributo e fornecer um nome c# válido para o `EventArgs` subclasse:
 
```xml
<attr path="/api/package[@name='com.someapp.android.mpa.guidance']/
    interface[@name='NavigationManager.Listener']/
    method[@name='on2DSignNextManeuver']" 
    name="argsType">NavigationManager.TwoDSignNextManueverEventArgs</attr>
```

 

## <a name="supported-attributes"></a>Atributos com suporte

As seções a seguir descrevem alguns dos atributos para transformar as APIs de Java.

### <a name="argstype"></a>argsType

Esse atributo é colocado em métodos setter para nomear o `EventArg` subclasse que será gerado para oferecer suporte a ouvintes de Java. Isso é descrito com mais detalhes abaixo na seção [renomeando Classes de Wrapper EventArg](#Renaming_EventArg_Wrapper_Classes) posteriormente neste guia.

### <a name="eventname"></a>eventName

Especifica um nome de um evento. Se estiver vazio, ele inibe a geração de eventos.
Isso é descrito mais detalhadamente no título da seção [renomeando Classes de Wrapper EventArg](#Renaming_EventArg_Wrapper_Classes).

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

### <a name="managedtype"></a>managedType

`managedType` é usado para alterar o tipo de retorno de um método. Em algumas situações o gerador de associações incorretamente deduz o tipo de retorno de um método de Java, o que resultará em um erro de tempo de compilação. Uma solução possível nessa situação é alterar o tipo de retorno do método.

Por exemplo, o gerador de associações acredita que o método Java `de.neom.neoreadersdk.resolution.compareTo()` deve retornar um `int`, que resulta na mensagem de erro **CS0535 de erro: ' Ir. Neom.Neoreadersdk.Resolution' não implementa membro de interface 'Java.Lang.IComparable.CompareTo(Java.Lang.Object)'**. O trecho a seguir demonstra como alterar o tipo de retorno do método c# gerado de uma `int` para um `Java.Lang.Object`: 

```xml
<attr path="/api/package[@name='de.neom.neoreadersdk']/
    class[@name='Resolution']/
    method[@name='compareTo' and count(parameter)=1 and
    parameter[1][@type='de.neom.neoreadersdk.Resolution']]/
    parameter[1]"name="managedType">Java.Lang.Object</attr> 
```

### <a name="managedreturn"></a>managedReturn

Altera o tipo de retorno de um método. Isso não altera o atributo de retorno (como as alterações para retornar atributos podem resultar em alterações incompatíveis para a assinatura JNI). No exemplo a seguir, o tipo de retorno do `append` método é alterado de `SpannableStringBuilder` para `IAppendable` (Lembre-se de que c# não oferece suporte a tipos de retorno covariante):

```xml
<attr path="/api/package[@name='android.text']/
    class[@name='SpannableStringBuilder']/
    method[@name='append']" 
    name="managedReturn">Java.Lang.IAppendable</attr>
```

### <a name="obfuscated"></a>ofuscado

Ferramentas que ofuscar bibliotecas Java podem interferir com o gerador de associação xamarin e sua capacidade de gerar classes de wrapper do c#. Características de classes ofuscados incluem: * inclui o nome da classe um **$**, ou seja, **um. ' $Class** * o nome da classe é totalmente comprometido de letras minúsculas, ou seja,  **a.class**

Este trecho de código é um exemplo de como gerar um tipo c# "não ofuscado":

```xml
<attr path="/api/package[@name='{package_name}']/class[@name='{name}']" 
    name="obfuscated">false</attr>
```

### <a name="propertyname"></a>propertyName

Este atributo pode ser usado para alterar o nome de uma propriedade gerenciada.

Um caso especializado do uso de `propertyName` envolve a situação em que uma classe Java tem apenas um método getter para um campo. Nessa situação, o gerador de associação deseja criar uma propriedade somente gravação, algo que não é recomendado em .NET. O trecho a seguir mostra como "remover" as propriedades do .NET, definindo o `propertyName` para uma cadeia de caracteres vazia:

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

Especifica que o parâmetro de um método deve ser o `sender` parâmetro quando o método é mapeado para um evento. O valor pode ser `true` ou `false`. Por exemplo:

```xml
<attr path="/api/package[@name='android.app']/
    interface[@name='TimePickerDialog.OnTimeSetListener']/
    method[@name='onTimeSet']/
    parameter[@name='view']" 
    name="sender">true</ attr>
```

### <a name="visibility"></a>visibilidade

Este atributo é usado para alterar a visibilidade de uma classe, método ou propriedade. Por exemplo, pode ser necessário promover um `protected` método Java para que ele correspondente c# wrapper é `public`:

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method --> 
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

## <a name="enumfieldsxml-and-enummethodsxml"></a>EnumFields.xml e EnumMethods.xml

Há casos em que o Android bibliotecas usam constantes inteiras para representar os estados que são passados para propriedades ou métodos das bibliotecas. Em muitos casos, é útil associar as constantes de inteiro para enums em c#. Para facilitar esse mapeamento, use o **EnumFields.xml** e **EnumMethods.xml** arquivos em seu projeto de associação. 

### <a name="defining-an-enum-using-enumfieldsxml"></a>Definindo um Enum usando EnumFields.xml

O **EnumFields.xml** arquivo contém o mapeamento entre Java `int` constantes e c# `enums`. Vejamos o exemplo a seguir de um enum c# que está sendo criado para um conjunto de `int` constantes: 

```xml 
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit">
    <field jni-name="UNIT_SECOND" clr-name="Second" value="0" />
    <field jni-name="UNIT_METER" clr-name="Meter" value="1" />
    <field jni-name="UNIT_MILIWATT_HOURS" clr-name="MilliwattHour" value="2" />
</mapping>
```

Aqui, levamos classe Java `SKRealReachSettings` e definido um enum c# chamado `SKMeasurementUnit` no namespace `Skobbler.Ngx.Map.RealReach`. O `field` entradas define o nome da constante Java (exemplo `UNIT_SECOND`), o nome da entrada de enum (exemplo `Second`) e o valor de inteiro representado por duas entidades (exemplo `0`). 

### <a name="defining-gettersetter-methods-using-enummethodsxml"></a>Define os métodos Getter/Setter usando EnumMethods.xml

O **EnumMethods.xml** arquivo permite alterar os parâmetros de método e tipos de retorno do Java `int` constantes c# `enums`. Em outras palavras, ele mapeia a leitura e gravação de enums c# (definido no **EnumFields.xml** arquivo) para Java `int` constante `get` e `set` métodos.

Considerando a `SKRealReachSettings` enumeração definida acima, o seguinte **EnumMethods.xml** arquivo definiria o getter/setter para este enum:

```xml
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings">
    <method jni-name="getMeasurementUnit" parameter="return" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
    <method jni-name="setMeasurementUnit" parameter="measurementUnit" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
</mapping>
```

A primeira `method` linha mapeia o valor de retorno do Java `getMeasurementUnit` método para o `SKMeasurementUnit` enum. A segunda `method` linha mapeia o primeiro parâmetro do `setMeasurementUnit` à mesma enumeração.

Todas essas alterações em vigor, você pode usar o código a seguir no xamarin para definir o `MeasurementUnit`: 

```csharp
realReachSettings.MeasurementUnit = SKMeasurementUnit.Second;
```


## <a name="summary"></a>Resumo

Este artigo discutidos como xamarin usa metadados para transformar uma definição de API do *Google* *formato AOSP*. Depois de abordar as alterações que são possíveis usando *Metadata.xml*, ele examinado as limitações encontradas ao renomear membros e apresentá-lo a lista de atributos XML com suporte, que descreve quando cada atributo deve ser usado.



## <a name="related-links"></a>Links relacionados

- [Trabalhando com JNI](~/android/platform/java-integration/working-with-jni.md)
- [Associação de uma biblioteca Java](~/android/platform/binding-java-library/index.md)
- [Metadados GAPI](http://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
