---
title: Namespaces XAML no xamarin. Forms
description: XAML usa o atributo xmlns XML para declarações de namespace. Este artigo apresenta a sintaxe do namespace XAML e demonstra como declarar um namespace XAML para um tipo de acesso.
ms.prod: xamarin
ms.assetid: C03B5553-B199-4A19-9F0F-E5BCE1DB268F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/21/2018
ms.openlocfilehash: d307c128826775e6d4f7129c79e17522e7e05d6a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61176515"
---
# <a name="xaml-namespaces-in-xamarinforms"></a>Namespaces XAML no xamarin. Forms

_XAML usa o atributo xmlns XML para declarações de namespace. Este artigo apresenta a sintaxe do namespace XAML e demonstra como declarar um namespace XAML para um tipo de acesso._

## <a name="overview"></a>Visão geral

Há duas declarações de namespace XAML que sempre estão dentro do elemento raiz de um arquivo XAML. A primeira define o namespace padrão, conforme mostrado no exemplo de código XAML a seguir:

```csharp
xmlns="http://xamarin.com/schemas/2014/forms"
```

O namespace padrão especifica que elementos definidos no arquivo XAML sem prefixo se referem às classes do xamarin. Forms, tal como [ `ContentPage` ](xref:Xamarin.Forms.ContentPage).

A segunda declaração de namespace usa o `x` prefixo, conforme mostrado no exemplo de código XAML a seguir:

```csharp
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
```

XAML usa prefixos de declarar namespaces não-padrão, com o prefixo que está sendo usado ao fazer referência a tipos no namespace. O `x` declaração de namespace Especifica que os elementos definidos dentro do XAML com um prefixo de `x` são usados para elementos e atributos que são intrínsecos para XAML (especificamente, a especificação de XAML de 2009).

A tabela a seguir descreve o `x` atributos de namespace com suporte de xamarin. Forms:

|Constructo|Descrição|
|--- |--- |
|`x:Arguments`|Especifica argumentos de construtor para um construtor não padrão ou para uma declaração de objeto do método de fábrica.|
|`x:Class`|Especifica o nome de namespace e classe para uma classe definida em XAML. O nome da classe deve corresponder ao nome de classe do arquivo code-behind. Observe que essa construção só pode aparecer no elemento raiz de um arquivo XAML.|
|`x:DataType`|Especifica o tipo do objeto que o elemento XAML e seus filhos, irá se vincular.|
|`x:FactoryMethod`|Especifica um método de fábrica que pode ser usado para inicializar um objeto.|
|`x:FieldModifier`|Especifica o nível de acesso para os campos gerados para elementos nomeados do XAML.|
|`x:Key`|Especifica uma chave exclusiva definida pelo usuário para cada recurso em um `ResourceDictionary`. O valor da chave é usado para recuperar o recurso XAML e normalmente é usado como o argumento para o `StaticResource` extensão de marcação.|
|`x:Name`|Especifica um nome de objeto de tempo de execução para o elemento XAML. Configuração `x:Name` é semelhante a declarar uma variável no código.|
|`x:TypeArguments`|Especifica os argumentos de tipo genérico para o construtor de um tipo genérico.|

Para obter mais informações sobre o `x:DataType` atributo, consulte [compilado associações](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md). Para obter mais informações sobre o `x:FieldModifier` atributo, consulte [modificadores de campo](~/xamarin-forms/xaml/field-modifiers.md). Para obter mais informações sobre o `x:Arguments`, `x:FactoryMethod`, e `x:TypeArguments` atributos, consulte [passando argumentos em XAML](~/xamarin-forms/xaml/passing-arguments.md).

> [!NOTE]
> Além dos atributos de namespace listados acima, o xamarin. Forms também inclui extensões de marcação que podem ser consumidas por meio de `x` prefixo de namespace. Para obter mais informações, consulte [extensões de marcação XAML consumindo](~/xamarin-forms/xaml/markup-extensions/consuming.md).

No XAML, as declarações de namespace herdam de elemento pai para o elemento filho. Portanto, ao definir um namespace no elemento raiz de um arquivo XAML, todos os elementos dentro desse arquivo herdam a declaração de namespace.

## <a name="declaring-namespaces-for-types"></a>Declarando Namespaces para tipos

Tipos podem ser referenciadas em XAML, declarando um namespace XAML com um prefixo, com a declaração de namespace, especificando o nome do namespace de Common Language Runtime (CLR) e, opcionalmente, um nome de assembly. Isso é obtido com a definição de valores para as seguintes palavras-chave na declaração de namespace:

- **CLR-namespace:** ou **usando:** – o namespace do CLR declarado dentro do assembly que contém os tipos a serem expostos como elementos XAML. Essa palavra-chave é necessária.
- **assembly =** – o assembly que contém o namespace CLR referenciado. Esse valor é o nome do assembly, sem a extensão de arquivo. O caminho para o assembly deve ser estabelecido como uma referência no arquivo de projeto que contém o arquivo XAML que fazem referência a assembly. Essa palavra-chave pode ser omitido se o **clr-namespace** valor está dentro do mesmo assembly que o código do aplicativo que faz referência os tipos.

Observe que o caractere separando o `clr-namespace` ou `using` token de seu valor é um dois-pontos, enquanto o caractere que separa o `assembly` token do seu valor é um sinal de igual. O caractere a ser usado entre dois tokens é um ponto e vírgula.

O exemplo de código a seguir mostra uma declaração de namespace XAML:

```xaml
<ContentPage ... xmlns:local="clr-namespace:HelloWorld" ...>
  ...
</ContentPage>
```

Como alternativa, isso pode ser escrito como:

```xaml
<ContentPage ... xmlns:local="using:HelloWorld" ...>
  ...
</ContentPage>
```

O `local` prefixo é uma convenção usada para indicar que os tipos no namespace são locais para o aplicativo. Como alternativa, se os tipos estão em um assembly diferente, o nome do assembly deve também ser definido na declaração de namespace, conforme demonstrado no exemplo de código XAML a seguir:

```xaml
<ContentPage ... xmlns:behaviors="clr-namespace:Behaviors;assembly=BehaviorsLibrary" ...>
  ...
</ContentPage>
```

O prefixo de namespace é especificado, em seguida, ao declarar uma instância de um tipo de um namespace importado, como demonstrado no exemplo de código XAML a seguir:

```xaml
<ListView ...>
  <ListView.Behaviors>
    <behaviors:EventToCommandBehavior EventName="ItemSelected" ... />
  </ListView.Behaviors>
</ListView>
```

Para obter informações sobre como definir um esquema personalizado de namespace, consulte [esquemas personalizados de Namespace XAML](custom-namespace-schemas.md).

## <a name="summary"></a>Resumo

Este artigo introduziu a sintaxe do namespace XAML e demonstrou como declarar um namespace XAML para um tipo de acesso. XAML usa a `xmlns` atributo XML para tipos e declarações de namespace pode ser referenciado em XAML, declarando um namespace XAML com um prefixo.

## <a name="related-links"></a>Links relacionados

- [Passando argumentos em XAML](~/xamarin-forms/xaml/passing-arguments.md)
