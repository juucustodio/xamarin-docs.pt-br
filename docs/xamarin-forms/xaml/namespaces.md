---
title: Namespaces XAML
description: XAML usa o atributo xmlns XML para declarações de namespace. Este artigo apresenta a sintaxe do namespace XAML e demonstra como declarar um namespace XAML para um tipo de acesso.
ms.prod: xamarin
ms.assetid: C03B5553-B199-4A19-9F0F-E5BCE1DB268F
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 07/10/2017
ms.openlocfilehash: ecf255d2fc6937e93258eaa021f79152cb7b1c7f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="xaml-namespaces"></a>Namespaces XAML

_XAML usa o atributo xmlns XML para declarações de namespace. Este artigo apresenta a sintaxe do namespace XAML e demonstra como declarar um namespace XAML para um tipo de acesso._

## <a name="overview"></a>Visão geral

Há duas declarações de namespace XAML que sempre estão dentro do elemento raiz de um arquivo XAML. A primeira define o namespace padrão, conforme mostrado no exemplo de código XAML a seguir:

```csharp
xmlns="http://xamarin.com/schemas/2014/forms"
```

O namespace padrão especifica que os elementos definidos no arquivo XAML sem prefixo referem-se às classes do xamarin. Forms, como [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/).

A segunda declaração de namespace usa o `x` do prefixo, conforme mostrado no exemplo de código XAML a seguir:

```csharp
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
```

XAML usa prefixos para declarar namespaces não padrão, com o prefixo que está sendo usado ao referenciar tipos no namespace. O `x` declaração de namespace Especifica que os elementos definidos em XAML com um prefixo de `x` são usadas para elementos e atributos que são intrínsecos XAML (especificamente, a especificação de XAML de 2009).

A tabela a seguir descreve o `x` atributos do namespace xamarin. Forms com suporte:

|Constructo|Descrição|
|--- |--- |
|`x:Arguments`|Especifica argumentos de construtor para um construtor não padrão, ou para uma declaração de objeto do método de fábrica.|
|`x:Class`|Especifica o nome de namespace e classe para uma classe definida em XAML. O nome da classe deve corresponder ao nome de classe do arquivo code-behind. Observe que esse constructo só pode aparecer no elemento raiz de um arquivo XAML.|
|`x:FactoryMethod`|Especifica um método de fábrica que pode ser usado para inicializar um objeto.|
|`x:Key`|Especifica uma chave exclusiva definida pelo usuário para cada recurso em um `ResourceDictionary`. O valor da chave é usado para recuperar o recurso XAML e normalmente é usado como argumento para o `StaticResource` extensão de marcação.|
|`x:Name`|Especifica um nome de objeto de tempo de execução para o elemento XAML. Configuração `x:Name` é semelhante ao declarar uma variável no código.|
|`x:TypeArguments`|Especifica os argumentos de tipo genérico para o construtor de um tipo genérico.|

Para obter mais informações sobre o `x:Arguments`, `x:FactoryMethod`, e `x:TypeArguments` atributos, consulte [passando argumentos em XAML](~/xamarin-forms/xaml/passing-arguments.md).

Em XAML, declarações de namespace herdam do elemento pai para o elemento filho. Portanto, ao definir um namespace no elemento raiz de um arquivo XAML, todos os elementos dentro desse arquivo herdam a declaração de namespace.

## <a name="declaring-namespaces-for-types"></a>Declarando Namespaces para tipos

Tipos podem ser referenciadas em XAML, declarando um namespace XAML com um prefixo, com a declaração de namespace, especificando o nome do namespace Common Language Runtime (CLR) e, opcionalmente, um nome de assembly. Isso é feito definindo valores para as seguintes palavras-chave na declaração de namespace:

- **CLR-namespace:** ou **usando:** – namespace CLR declarado dentro do assembly que contém os tipos para expor como elementos XAML. Esta palavra-chave é necessária.
- **assembly =** – o assembly que contém o namespace CLR referenciado. Esse valor é o nome do assembly, sem a extensão de arquivo. O caminho para o assembly deve ser estabelecido como uma referência no arquivo de projeto que contém o arquivo XAML que irá referenciar o assembly. Esta palavra-chave pode ser omitido se a **clr-namespace** valor está dentro do mesmo assembly como o código do aplicativo que faz referência os tipos.

Observe que o caractere separando o `clr-namespace` ou `using` token do seu valor é um vírgula, enquanto o caractere separando o `assembly` token do seu valor é um sinal de igual. O caractere a ser usado entre os dois tokens é um ponto e vírgula.

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

O `local` prefixo é uma convenção usada para indicar que os tipos no namespace são locais para o aplicativo. Como alternativa, se os tipos em um assembly diferente, o nome do assembly deve também ser definido na declaração de namespace, conforme demonstrado no exemplo de código XAML a seguir:

```xaml
<ContentPage ... xmlns:behaviors="clr-namespace:Behaviors;assembly=BehaviorsLibrary" ...>
  ...
</ContentPage>
```

O prefixo de namespace é especificado quando declarar uma instância de um tipo de um namespace importado, como demonstrado no exemplo de código XAML a seguir:

```xaml
<ListView ...>
  <ListView.Behaviors>
    <behaviors:EventToCommandBehavior EventName="ItemSelected" ... />
  </ListView.Behaviors>
</ListView>
```

## <a name="summary"></a>Resumo

Este artigo introduziu a sintaxe do namespace XAML e demonstrou como declarar um namespace XAML para um tipo de acesso. XAML usa o `xmlns` atributo XML para tipos e declarações de namespace pode ser referenciado em XAML declarando um namespace XAML com um prefixo.


## <a name="related-links"></a>Links relacionados

- [Passando argumentos em XAML](~/xamarin-forms/xaml/passing-arguments.md)
