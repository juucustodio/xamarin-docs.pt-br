---
title: Esquemas de personalizado de Namespace XAML no xamarin. Forms
description: Um esquema personalizado de namespace XAML pode ser definido com a classe XmlnsDefinitionAttribute, que especifica um mapeamento entre uma URL personalizada e um ou mais namespaces CLR. O esquema de namespace personalizado, em seguida, pode ser usado em declarações de namespace XAML.
ms.prod: xamarin
ms.assetid: FDF201A1-8C35-4569-A728-F9B0A0C5B31A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/21/2018
ms.openlocfilehash: 2e09e89fe17956efaef910638e827b69a5795bc0
ms.sourcegitcommit: 00744f754527e5b55154365f89691caaf1c9d929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57557211"
---
# <a name="xaml-custom-namespace-schemas-in-xamarinforms"></a>Esquemas de personalizado de Namespace XAML no xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/XAML/CustomNamespaceSchemas/)

Tipos em uma biblioteca podem ser referenciados em XAML, declarando um namespace XAML para a biblioteca, com a declaração de namespace, especificando o nome do namespace de Common Language Runtime (CLR) e um nome de assembly:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:MyCompany.Controls;assembly="MyCompany.Controls">
    ...
</ContentPage>
```

No entanto, especificando um nome de namespace e assembly do CLR em um `xmlns` definição pode ser complicada e sujeito a erros. Além disso, várias declarações de namespace XAML podem ser necessárias se a biblioteca contém tipos em vários namespaces.

Uma abordagem alternativa é definir um esquema personalizado de namespace, tal como `http://mycompany.com/schemas/controls`, que é mapeado para um ou mais namespaces CLR. Isso permite que uma única declaração de namespace XAML fazer referência a todos os tipos em um assembly, mesmo se eles estão em namespaces diferentes. Ele também permite que uma única declaração de namespace XAML para tipos de referência em vários assemblies.

Para obter mais informações sobre namespaces XAML, consulte [Namespaces de XAML no xamarin. Forms](namespaces.md).

## <a name="defining-a-custom-namespace-schema"></a>Definir um esquema personalizado de namespace

O aplicativo de exemplo contém uma biblioteca que expõe alguns controles simples, como `CircleButton`:

```csharp
using Xamarin.Forms;

namespace MyCompany.Controls
{
    public class CircleButton : Button
    {
        ...
    }
}
```

Todos os controles na biblioteca residem no `MyCompany.Controls` namespace. Esses controles podem ser expostos a um assembly de chamada por meio de um esquema personalizado de namespace.

Um esquema personalizado de namespace é definido com o `XmlnsDefinitionAttribute` classe, que especifica o mapeamento entre um namespace XAML e um ou mais namespaces CLR. O `XmlnsDefinitionAttribute` leva dois argumentos: o nome do namespace XAML e o nome do namespace CLR. O nome do namespace XAML é armazenado na `XmlnsDefinitionAttribute.XmlNamespace` propriedade e o nome do namespace CLR é armazenada no `XmlnsDefinitionAttribute.ClrNamespace` propriedade.

> [!NOTE]
> O `XmlnsDefinitionAttribute` classe também tem uma propriedade chamada `AssemblyName`, que pode ser definido opcionalmente para o nome do assembly. Isso só é necessário quando um namespace CLR referenciado de um `XmlnsDefinitionAttribute` está em um assembly externo.

O `XmlnsDefinitionAttribute` deve ser definida no nível de assembly no projeto que contém os namespaces CLR que serão mapeados no esquema do namespace personalizado. A exemplo a seguir mostra a **AssemblyInfo.cs** arquivo do aplicativo de exemplo:

```csharp
using Xamarin.Forms;
using MyCompany.Controls;

[assembly: Preserve]
[assembly: XmlnsDefinition("http://mycompany.com/schemas/controls", "MyCompany.Controls")]
```

Esse código cria um esquema personalizado de namespace que mapeia o `http://mycompany.com/schemas/controls` URL para o `MyCompany.Controls` namespace CLR. Além disso, o `Preserve` atributo é especificado no assembly, para garantir que o vinculador preserva todos os tipos no assembly.

> [!IMPORTANT]
> O `Preserve` atributo deve ser aplicado a classes no assembly que são mapeadas por meio do esquema do namespace personalizado ou aplicado ao assembly inteiro.

O esquema do namespace personalizado, em seguida, pode ser usado para resolução de tipo nos arquivos XAML.

## <a name="consuming-a-custom-namespace-schema"></a>Consumindo um esquema personalizado de namespace

Para consumir tipos do esquema do namespace personalizado, o compilador XAML requer que há uma referência de código do assembly que consome os tipos, para o assembly que define os tipos. Isso pode ser feito adicionando uma classe que contém um `Init` método para o assembly que define os tipos que serão consumidos por meio de XAML:

```csharp
namespace MyCompany.Controls
{
    public static class Controls
    {
        public static void Init()
        {
        }
    }
}
```

O `Init` método, em seguida, pode ser chamado do assembly que consome os tipos do esquema do namespace personalizado:

```csharp
using Xamarin.Forms;
using MyCompany.Controls;

namespace CustomNamespaceSchemaDemo
{
    public partial class MainPage : ContentPage
    {
        public MainPage()
        {
            Controls.Init();
            InitializeComponent();
        }
    }
}
```

> [!WARNING]
> Falha ao incluir uma referência de código desse tipo resultará no compilador XAML sendo não é possível localizar o assembly que contém os tipos de esquema do namespace personalizado.

Para consumir o `CircleButton` controle, um namespace XAML é declarado, com a declaração de namespace, especificando a URL de esquema do namespace personalizado:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:controls="http://mycompany.com/schemas/controls"
             x:Class="CustomNamespaceSchemaDemo.MainPage">
    <StackLayout Margin="20,35,20,20">
        ...
        <controls:CircleButton Text="+"
                               BackgroundColor="Fuchsia"
                               BorderColor="Black"
                               CircleDiameter="100" />
        <controls:CircleButton Text="-"
                               BackgroundColor="Teal"
                               BorderColor="Silver"
                               CircleDiameter="70" />
        ...
    </StackLayout>
</ContentPage>
```

`CircleButton` instâncias, em seguida, podem ser adicionadas para o [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) declarando-os com o `controls` prefixo de namespace.

Para localizar o namespace personalizado os tipos de esquema, o xamarin. Forms pesquisará os assemblies referenciados para `XmlnsDefinitionAttribute` instâncias. Se o `xmlns` corresponde ao atributo de um elemento em um arquivo XAML a `XmlNamespace` valor da propriedade em um `XmlnsDefinitionAttribute`, xamarin. Forms tentará usar o `XmlnsDefinitionAttribute.ClrNamespace` para a resolução do tipo de valor da propriedade. Se a resolução de tipo falhar, xamarin. Forms continuará a tentar a resolução de tipo com base em qualquer correspondência adicionais `XmlnsDefinitionAttribute` instâncias.

O resultado é que dois `CircleButton` instâncias são exibidas:

![Botões de círculo](custom-namespace-schemas-images/circle-buttons.png "círculo botões")

## <a name="related-links"></a>Links relacionados

- [Esquemas personalizados de Namespace (amostra)](https://developer.xamarin.com/samples/xamarin-forms/XAML/CustomNamespaceSchemas/)
- [Recomendado prefixos de Namespace XAML](custom-prefix.md)
- [Namespaces XAML no xamarin. Forms](namespaces.md)
