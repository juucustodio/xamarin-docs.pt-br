---
title: Metadados de associações de Java
description: C#o código no Xamarin. Android chama bibliotecas Java por meio de associações, que são um mecanismo que abstrai os detalhes de nível baixo que são especificados na interface nativa Java (JNI). O Xamarin. Android fornece uma ferramenta que gera essas associações. Essas ferramentas permitem que o desenvolvedor controle como uma associação é criada usando metadados, que permite procedimentos como a modificação de namespaces e a renomeação de membros. Este documento discute como os metadados funcionam, resume os atributos aos quais os metadados dão suporte e explica como resolver problemas de ligação modificando esses metadados.
ms.prod: xamarin
ms.assetid: 27CB3C16-33F3-F580-E2C0-968005A7E02E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: f0f15647e439381c2aa291234b444d668d1b63d0
ms.sourcegitcommit: 2e5a6b8bcd1a073b54604f51538fd108e1c2a8e5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68869351"
---
# <a name="java-bindings-metadata"></a>Metadados de associações de Java

_C#o código no Xamarin. Android chama bibliotecas Java por meio de associações, que são um mecanismo que abstrai os detalhes de nível baixo que são especificados na interface nativa Java (JNI). O Xamarin. Android fornece uma ferramenta que gera essas associações. Essas ferramentas permitem que o desenvolvedor controle como uma associação é criada usando metadados, que permite procedimentos como a modificação de namespaces e a renomeação de membros. Este documento discute como os metadados funcionam, resume os atributos aos quais os metadados dão suporte e explica como resolver problemas de ligação modificando esses metadados._


## <a name="overview"></a>Visão geral

Uma **biblioteca de associação Java** do Xamarin. Android tenta automatizar grande parte do trabalho necessário para associar uma biblioteca Android existente com a ajuda de uma ferramenta, às vezes conhecida como o _gerador de associações_. Ao associar uma biblioteca Java, o Xamarin. Android inspecionará as classes Java e gerará uma lista de todos os pacotes, tipos e membros que serão associados. Essa lista de APIs é armazenada em um arquivo XML que pode ser encontrado no  **\{diretório do projeto} \obj\Release\api.xml** para uma compilação de **versão** e no  **\{diretório do projeto} \obj\Debug\api.xml** para uma compilação de **depuração** .

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

Neste exemplo, o **API. xml** declara uma classe no `android` pacote chamado `Manifest` que estende o `java.lang.Object`.

Em muitos casos, a assistência humana é necessária para fazer com que a API Java sinta mais ".NET como" ou corrija problemas que impeçam o assembly de associação de compilação. Por exemplo, pode ser necessário alterar nomes de pacote Java para namespaces do .NET, renomear uma classe ou alterar o tipo de retorno de um método.

Essas alterações não são obtidas modificando o **API. xml** diretamente.
Em vez disso, as alterações são registradas em arquivos XML especiais fornecidos pelo modelo de biblioteca de associação Java. Ao compilar o assembly de associação do Xamarin. Android, o gerador de associações será influenciado por esses arquivos de mapeamento ao criar o assembly de associação

Esses arquivos de mapeamento XML podem ser encontrados na pasta transformações do projeto:

-   O **Metadata. xml** &ndash; permite que as alterações sejam feitas na API final, como alterar o namespace da Associação gerada. 

-   **EnumFields. xml** &ndash; contém o mapeamento entre as `int` constantes Java C# `enums` e. 

-   O **EnumMethods. xml** &ndash; permite alterar os parâmetros do método e retornar `int` tipos de C# `enums` constantes Java para. 

O arquivo **Metadata. xml** é a mais importação desses arquivos, pois permite alterações de uso geral na associação, como:

-   Renomeando namespaces, classes, métodos ou campos para que sigam as convenções .NET. 

-   Removendo namespaces, classes, métodos ou campos que não são necessários. 

-   Movendo classes para namespaces diferentes. 

-   Adicionar classes de suporte adicionais para fazer o design da Associação seguir os padrões do .NET Framework. 

Permite passar para discutir **Metadata. xml** mais detalhadamente.


## <a name="metadataxml-transform-file"></a>Arquivo de transformação Metadata. xml

Como já aprendimos, o arquivo **Metadata. xml** é usado pelo gerador de associações para influenciar a criação do assembly de associação.
O formato de metadados usa a sintaxe [XPath](https://www.w3.org/TR/xpath/) e é quase idêntico aos *metadados GAPI* descritos no guia de [metadados do GAPI](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata) . Essa implementação é quase uma implementação completa do XPath 1,0 e, portanto, dá suporte a itens no padrão 1,0. Esse arquivo é um poderoso mecanismo baseado em XPath para alterar, adicionar, ocultar ou mover qualquer elemento ou atributo no arquivo de API. Todos os elementos de regra na especificação de metadados incluem um atributo de caminho para identificar o nó ao qual a regra deve ser aplicada. As regras são aplicadas na seguinte ordem:

* **adicionar nó** &ndash; Anexa um nó filho ao nó especificado pelo atributo Path.
* **attr** &ndash; Define o valor de um atributo do elemento especificado pelo atributo Path.
* **remover nó** &ndash; Remove os nós que correspondem a um XPath especificado.

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

-   `interface`&ndash; Usado para localizar uma interface java. por exemplo,. `/interface[@name='AuthListener']`

-   `class`&ndash; Usado para localizar uma classe. por exemplo,. `/class[@name='MapView']`

-   `method`&ndash; Usado para localizar um método em uma classe ou interface java. por exemplo,. `/class[@name='MapView']/method[@name='setTitleSource']`

-   `parameter`&ndash; Identifique um parâmetro para um método. p.`/parameter[@name='p0']`



### <a name="adding-types"></a>Adicionando tipos

O `add-node` elemento informará ao projeto de associação Xamarin. Android para adicionar uma nova classe wrapper a **API. xml**. Por exemplo, o trecho a seguir direcionará o gerador de associação para criar uma classe com um construtor e um único campo:

```xml
<add-node path="/api/package[@name='org.alljoyn.bus']">
    <class abstract="false" deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="true" visibility="public" extends="java.lang.Object">
        <constructor deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="false" type="org.alljoyn.bus.AuthListener.AuthRequest" visibility="public" />
        <field name="p0" type="org.alljoyn.bus.AuthListener.Credentials" />
    </class>
</add-node>
```


### <a name="removing-types"></a>Removendo tipos

É possível instruir o gerador de associações do Xamarin. Android para ignorar um tipo Java e não associá-lo. Isso é feito adicionando um `remove-node` elemento XML ao arquivo **Metadata. xml** :

```xml
<remove-node path="/api/package[@name='{package_name}']/class[@name='{name}']" />
```

### <a name="renaming-members"></a>Renomeando Membros

A renomeação de membros não pode ser feita editando diretamente o arquivo **API. xml** , pois o Xamarin. Android requer os nomes de JNI (interface nativa do Java) originais. Portanto, o `//class/@name` atributo não pode ser alterado; se for, a associação não funcionará.

Considere o caso em que desejamos renomear `android.Manifest`um tipo,.
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

Observe que a classe wrapper foi renomeada para `NewName`, enquanto o tipo Java original ainda `Manifest`é. Não é mais possível que a classe de associação Xamarin. Android acesse os métodos em `android.Manifest`; a classe wrapper está associada a um tipo Java não existente.

Para alterar corretamente o nome gerenciado de um tipo encapsulado (ou método), é necessário definir o `managedName` atributo, conforme mostrado neste exemplo:

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="managedName">NewName</attr>
```

<a name="Renaming_EventArg_Wrapper_Classes" />

#### <a name="renaming-eventarg-wrapper-classes"></a>Renomeando `EventArg` classes de wrapper

Quando o gerador de associação do Xamarin. Android `onXXX` identifica um método setter para um _tipo_de C# ouvinte `EventArgs` , um evento e uma subclasse serão gerados para dar suporte a uma API .net flavoured para o padrão de ouvinte baseado em Java. Como exemplo, considere a classe Java e o método a seguir:

```xml
com.someapp.android.mpa.guidance.NavigationManager.on2DSignNextManuever(NextManueverListener listener);
```

O Xamarin. Android removerá o `on` prefixo do método setter e, em `2DSignNextManuever` vez disso, usará como base `EventArgs` para o nome da subclasse. A subclasse terá um nome semelhante a:

```csharp
NavigationManager.2DSignNextManueverEventArgs
```

Este não é um nome C# de classe legal. Para corrigir esse problema, o autor da associação deve usar `argsType` o atributo e fornecer um C# nome válido para `EventArgs` a subclasse:
 
```xml
<attr path="/api/package[@name='com.someapp.android.mpa.guidance']/
    interface[@name='NavigationManager.Listener']/
    method[@name='on2DSignNextManeuver']" 
    name="argsType">NavigationManager.TwoDSignNextManueverEventArgs</attr>
```

 

## <a name="supported-attributes"></a>Atributos com suporte

As seções a seguir descrevem alguns dos atributos para transformar APIs Java.

### <a name="argstype"></a>argsType

Esse atributo é colocado em métodos setter para nomear `EventArg` a subclasse que será gerada para dar suporte a ouvintes Java. Isso é descrito em mais detalhes abaixo na seção renomeando as [classes de wrapper eventArg](#Renaming_EventArg_Wrapper_Classes) posteriormente neste guia.

### <a name="eventname"></a>eventName

Especifica um nome para um evento. Se estiver vazio, ele inibe a geração de eventos.
Isso é descrito mais detalhadamente na seção título renomeando [classes de wrapper eventArg](#Renaming_EventArg_Wrapper_Classes).

### <a name="managedname"></a>managedname

Isso é usado para alterar o nome de um pacote, classe, método ou parâmetro. Por exemplo, para alterar o nome da classe `MyClass` Java para: `NewClassName`

```xml
<attr path="/api/package[@name='com.my.application']/class[@name='MyClass']" 
    name="managedName">NewClassName</attr>
```

O exemplo a seguir ilustra uma expressão XPath para renomear `java.lang.object.toString` o `Java.Lang.Object.NewManagedName`método como:

```xml
<attr path="/api/package[@name='java.lang']/class[@name='Object']/method[@name='toString']" 
    name="managedName">NewMethodName</attr>
```

### <a name="managedtype"></a>ManagedType

`managedType`é usado para alterar o tipo de retorno de um método. Em algumas situações, o gerador de associações inferirá incorretamente o tipo de retorno de um método Java, o que resultará em um erro de tempo de compilação. Uma solução possível nessa situação é alterar o tipo de retorno do método.

Por exemplo, o gerador de associações acredita que o método `de.neom.neoreadersdk.resolution.compareTo()` Java deve retornar um `int`, o que resulta no erro de mensagem **de erro CS0535: Deprecia. NEOM. Neoreadersdk. Resolution ' não implementa o membro de interface ' Java. lang. IComparable. CompareTo (Java. lang. Object**) '. O trecho a seguir demonstra como alterar o tipo de parâmetro do método C# gerado de um `DE.Neom.Neoreadersdk.Resolution` para um `Java.Lang.Object`: 

```xml
<attr path="/api/package[@name='de.neom.neoreadersdk']/
    class[@name='Resolution']/
    method[@name='compareTo' and count(parameter)=1 and
    parameter[1][@type='de.neom.neoreadersdk.Resolution']]/
    parameter[1]" name="managedType">Java.Lang.Object</attr> 
```

### <a name="managedreturn"></a>managedReturn

Altera o tipo de retorno de um método. Isso não altera o atributo de retorno (pois as alterações nos atributos de retorno podem resultar em alterações incompatíveis na assinatura JNI). No exemplo a seguir, o tipo de retorno do `append` método é alterado de `SpannableStringBuilder` para `IAppendable` (lembre- C# se de que o não oferece suporte a tipos de retorno covariantes):

```xml
<attr path="/api/package[@name='android.text']/
    class[@name='SpannableStringBuilder']/
    method[@name='append']" 
    name="managedReturn">Java.Lang.IAppendable</attr>
```

### <a name="obfuscated"></a>ofuscados

As ferramentas que ofuscam as bibliotecas Java podem interferir no gerador de associação do Xamarin. Android e sua C# capacidade de gerar classes de wrapper. As características das classes ofuscadas incluem: 

* O nome da classe inclui **$** um, ou seja, **uma classe $.**
* O nome da classe está totalmente comprometido com caracteres minúsculos, ou seja, **a. Class**

Este trecho de código é um exemplo de como gerar um tipo "não ofuscado C# ":

```xml
<attr path="/api/package[@name='{package_name}']/class[@name='{name}']" 
    name="obfuscated">false</attr>
```

### <a name="propertyname"></a>propertyName

Esse atributo pode ser usado para alterar o nome de uma propriedade gerenciada.

Um caso especializado de usar `propertyName` envolve a situação em que uma classe Java tem apenas um método getter para um campo. Nessa situação, o gerador de associação desejaria criar uma propriedade somente gravação, algo desencorajado no .NET. O trecho a seguir mostra como "remover" as propriedades do .net definindo a `propertyName` como uma cadeia de caracteres vazia:

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

Esse atributo é usado para alterar a visibilidade de uma classe, método ou propriedade. Por exemplo, pode ser necessário promover um método Java `protected` para que ele seja o wrapper `public`correspondente C# :

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method --> 
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

## <a name="enumfieldsxml-and-enummethodsxml"></a>EnumFields. xml e EnumMethods. xml

Há casos em que as bibliotecas do Android usam constantes inteiras para representar Estados que são passados para propriedades ou métodos das bibliotecas. Em muitos casos, é útil associar essas constantes de inteiro a enums no C#. Para facilitar esse mapeamento, use os arquivos **EnumFields. xml** e **EnumMethods. xml** em seu projeto de associação. 

### <a name="defining-an-enum-using-enumfieldsxml"></a>Definindo uma enumeração usando EnumFields. xml

O arquivo **EnumFields. xml** contém o mapeamento entre as `int` constantes Java C# `enums`e. Vamos pegar o exemplo a seguir de um C# enum que está sendo criado para um `int` conjunto de constantes: 

```xml 
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit">
    <field jni-name="UNIT_SECOND" clr-name="Second" value="0" />
    <field jni-name="UNIT_METER" clr-name="Meter" value="1" />
    <field jni-name="UNIT_MILIWATT_HOURS" clr-name="MilliwattHour" value="2" />
</mapping>
```

Aqui `SKRealReachSettings` , tiramos a classe Java e definimos uma C# enumeração `SKMeasurementUnit` chamada no namespace `Skobbler.Ngx.Map.RealReach`. As `field` entradas definem o nome da constante Java (exemplo `UNIT_SECOND`), o nome da entrada de enumeração (exemplo `Second`) e o valor inteiro representado por ambas as entidades (exemplo `0`). 

### <a name="defining-gettersetter-methods-using-enummethodsxml"></a>Definindo métodos getter/setter usando EnumMethods. xml

O arquivo **EnumMethods. xml** permite alterar os parâmetros do método e retornar tipos `int` de constantes C# `enums`Java para. Em outras palavras, ele mapeia a leitura e a gravação C# de enums (definidas no arquivo **EnumFields. xml** ) para constantes `int` `get` e `set` métodos Java.

Dada a `SKRealReachSettings` Enumeração definida acima, o seguinte arquivo **EnumMethods. xml** definiria o getter/setter para esta enumeração:

```xml
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings">
    <method jni-name="getMeasurementUnit" parameter="return" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
    <method jni-name="setMeasurementUnit" parameter="measurementUnit" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
</mapping>
```

A primeira `method` linha mapeia o valor de retorno do método `getMeasurementUnit` Java para a `SKMeasurementUnit` enumeração. A segunda `method` linha mapeia o primeiro parâmetro `setMeasurementUnit` do para o mesmo enum.

Com todas essas alterações em vigor, você pode usar o código a seguir no Xamarin. Android para definir `MeasurementUnit`: 

```csharp
realReachSettings.MeasurementUnit = SKMeasurementUnit.Second;
```


## <a name="summary"></a>Resumo

Este artigo abordou como o Xamarin. Android usa metadados para transformar uma definição de API do formato *Google* *AOSP*. Depois de abranger as alterações que são possíveis usando *Metadata. xml*, ele examinou as limitações encontradas ao renomear Membros e apresentou a lista de atributos XML com suporte, descrevendo quando cada atributo deve ser usado.



## <a name="related-links"></a>Links relacionados

- [Trabalhando com JNI](~/android/platform/java-integration/working-with-jni.md)
- [Associação de uma biblioteca Java](~/android/platform/binding-java-library/index.md)
- [Metadados do GAPI](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
