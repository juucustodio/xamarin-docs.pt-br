---
title: Namespaces XAML emXamarin.Forms
description: O XAML usa o atributo XML xmlns para declarações de namespace. Este artigo apresenta a sintaxe XAML namespace e demonstra como declarar um namespace XAML para acessar um tipo.
ms.prod: xamarin
ms.assetid: C03B5553-B199-4A19-9F0F-E5BCE1DB268F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/21/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7f35342134767ccdadfab086bfa14f6b610b325d
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84130371"
---
# <a name="xaml-namespaces-in-xamarinforms"></a>Namespaces XAML emXamarin.Forms

_O XAML usa o atributo XML xmlns para declarações de namespace. Este artigo apresenta a sintaxe XAML namespace e demonstra como declarar um namespace XAML para acessar um tipo._

## <a name="overview"></a>Visão geral

Há duas declarações de namespace XAML que estão sempre dentro do elemento raiz de um arquivo XAML. A primeira define o namespace padrão, conforme mostrado no exemplo de código XAML a seguir:

```xaml
xmlns="http://xamarin.com/schemas/2014/forms"
```

O namespace padrão especifica que os elementos definidos no arquivo XAML sem nenhum prefixo se referem a Xamarin.Forms classes, como [`ContentPage`](xref:Xamarin.Forms.ContentPage) .

A segunda declaração de namespace usa o `x` prefixo, conforme mostrado no exemplo de código XAML a seguir:

```xaml
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
```

O XAML usa prefixos para declarar namespaces não padrão, com o prefixo que está sendo usado ao referenciar tipos dentro do namespace. A `x` declaração de namespace Especifica que os elementos definidos no XAML com um prefixo de `x` são usados para elementos e atributos que são intrínsecos ao XAML (especificamente a especificação XAML 2009).

A tabela a seguir descreve os `x` atributos de namespace com suporte no Xamarin.Forms :

|Constructo|Descrição|
|--- |--- |
|`x:Arguments`|Especifica argumentos de construtor para um construtor não padrão ou para uma declaração de objeto de método de fábrica.|
|`x:Class`|Especifica o namespace e o nome de classe para uma classe definida em XAML. O nome da classe deve corresponder ao nome da classe do arquivo code-behind. Observe que esse constructo só pode aparecer no elemento raiz de um arquivo XAML.|
|`x:DataType`|Especifica o tipo do objeto ao qual o elemento XAML e é filho será associado.|
|`x:FactoryMethod`|Especifica um método de fábrica que pode ser usado para inicializar um objeto.|
|`x:FieldModifier`|Especifica o nível de acesso para campos gerados para elementos XAML nomeados.|
|`x:Key`|Especifica uma chave exclusiva definida pelo usuário para cada recurso em um `ResourceDictionary` . O valor da chave é usado para recuperar o recurso XAML e normalmente é usado como o argumento para a `StaticResource` extensão de marcação.|
|`x:Name`|Especifica um nome de objeto de tempo de execução para o elemento XAML. `x:Name`A configuração é semelhante à declaração de uma variável no código.|
|`x:TypeArguments`|Especifica os argumentos de tipo genérico para o construtor de um tipo genérico.|

Para obter mais informações sobre o `x:DataType` atributo, consulte [associações compiladas](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md). Para obter mais informações sobre o `x:FieldModifier` atributo, consulte [modificadores de campo](~/xamarin-forms/xaml/field-modifiers.md). Para obter mais informações sobre `x:Arguments` os `x:FactoryMethod` atributos e, consulte [passando argumentos em XAML](~/xamarin-forms/xaml/passing-arguments.md). Para obter mais informações sobre o `x:TypeArguments` atributo, consulte [genéricos em XAML com Xamarin.Forms ](generics.md).

> [!NOTE]
> Além dos atributos de namespace listados acima, Xamarin.Forms também inclui extensões de marcação que podem ser consumidas por meio do `x` prefixo de namespace. Para obter mais informações, consulte [consumindo extensões de marcação XAML](~/xamarin-forms/xaml/markup-extensions/consuming.md).

Em XAML, as declarações de namespace herdam do elemento pai para o elemento filho. Portanto, ao definir um namespace no elemento raiz de um arquivo XAML, todos os elementos dentro desse arquivo herdam a declaração de namespace.

## <a name="declaring-namespaces-for-types"></a>Declarando namespaces para tipos

Os tipos podem ser referenciados em XAML declarando um namespace XAML com um prefixo, com a declaração de namespace especificando o nome do namespace CLR (Common Language Runtime) e, opcionalmente, um nome de assembly. Isso é feito definindo valores para as seguintes palavras-chave na declaração de namespace:

- **CLR-namespace:** ou **usando:** – o namespace CLR declarado dentro do assembly que contém os tipos a serem expostos como elementos XAML. Essa palavra-chave é necessária.
- **assembly =** – o assembly que contém o namespace CLR referenciado. Esse valor é o nome do assembly, sem a extensão de arquivo. O caminho para o assembly deve ser estabelecido como uma referência no arquivo de projeto que contém o arquivo XAML que fará referência ao assembly. Essa palavra-chave poderá ser omitida se o valor do **namespace CLR** estiver dentro do mesmo assembly que o código do aplicativo que está fazendo referência aos tipos.

Observe que o caractere que separa o `clr-namespace` `using` token ou de seu valor é dois-pontos, enquanto que o caractere separando o `assembly` token de seu valor é um sinal de igual. O caractere a ser usado entre os dois tokens é um ponto-e-vírgula.

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

O `local` prefixo é uma convenção usada para indicar que os tipos dentro do namespace são locais para o aplicativo. Como alternativa, se os tipos estiverem em um assembly diferente, o nome do assembly também deverá ser definido na declaração do namespace, conforme demonstrado no exemplo de código XAML a seguir:

```xaml
<ContentPage ... xmlns:behaviors="clr-namespace:Behaviors;assembly=BehaviorsLibrary" ...>
  ...
</ContentPage>
```

O prefixo de namespace é especificado ao declarar uma instância de um tipo de um namespace importado, conforme demonstrado no exemplo de código XAML a seguir:

```xaml
<ListView ...>
  <ListView.Behaviors>
    <behaviors:EventToCommandBehavior EventName="ItemSelected" ... />
  </ListView.Behaviors>
</ListView>
```

Para obter informações sobre como definir um esquema de namespace personalizado, consulte [esquemas de namespace personalizado XAML](custom-namespace-schemas.md).

## <a name="summary"></a>Resumo

Este artigo introduziu a sintaxe XAML namespace e demonstrou como declarar um namespace XAML para acessar um tipo. O XAML usa o `xmlns` atributo XML para declarações de namespace, e os tipos podem ser referenciados em XAML, declarando um namespace XAML com um prefixo.

## <a name="related-links"></a>Links relacionados

- [Passando argumentos em XAML](~/xamarin-forms/xaml/passing-arguments.md)
- [Genéricos em XAML comXamarin.Forms](generics.md)
