---
title: Esquemas de namespace personalizado XAML emXamarin.Forms
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 41a95b1a82ab8aa1f6938e5a2bcdebcef368e72d
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138158"
---
# <a name="xaml-custom-namespace-schemas-in-xamarinforms"></a>Esquemas de namespace personalizado XAML emXamarin.Forms

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-customnamespaceschemas)

Os tipos em uma biblioteca podem ser referenciados em XAML declarando um namespace XAML para a biblioteca, com a declaração de namespace especificando o nome do namespace CLR (Common Language Runtime) e um nome de assembly:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:MyCompany.Controls;assembly=MyCompany.Controls">
    ...
</ContentPage>
```

No entanto, a especificação de um namespace CLR e de um nome de assembly em uma `xmlns` definição pode ser estranha e propenso a erros. Além disso, várias declarações de namespace XAML poderão ser necessárias se a biblioteca contiver tipos em vários namespaces.

Uma abordagem alternativa é definir um esquema de namespace personalizado, como `http://mycompany.com/schemas/controls` , que é mapeado para um ou mais namespaces CLR. Isso permite que uma única declaração de namespace XAML referencie todos os tipos em um assembly, mesmo se eles estiverem em namespaces diferentes. Ele também permite uma única declaração de namespace XAML para referenciar tipos em vários assemblies.

Para obter mais informações sobre namespaces XAML, consulte [namespaces XAML Xamarin.Forms em ](namespaces.md).

## <a name="defining-a-custom-namespace-schema"></a>Definindo um esquema de namespace personalizado

O aplicativo de exemplo contém uma biblioteca que expõe alguns controles simples, como `CircleButton` :

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

Todos os controles na biblioteca residem no `MyCompany.Controls` namespace. Esses controles podem ser expostos a um assembly de chamada por meio de um esquema de namespace personalizado.

Um esquema de namespace personalizado é definido com a `XmlnsDefinitionAttribute` classe, que especifica o mapeamento entre um namespace XAML e um ou mais namespaces CLR. O `XmlnsDefinitionAttribute` usa dois argumentos: o nome do namespace XAML e o nome do namespace CLR. O nome do namespace XAML é armazenado na `XmlnsDefinitionAttribute.XmlNamespace` propriedade e o nome do namespace CLR é armazenado na `XmlnsDefinitionAttribute.ClrNamespace` propriedade.

> [!NOTE]
> A `XmlnsDefinitionAttribute` classe também tem uma propriedade chamada `AssemblyName` , que pode ser definida opcionalmente como o nome do assembly. Isso só é necessário quando um namespace CLR referenciado de um `XmlnsDefinitionAttribute` está em um assembly externo.

O `XmlnsDefinitionAttribute` deve ser definido no nível do assembly no projeto que contém os namespaces CLR que serão mapeados no esquema de namespace personalizado. O exemplo a seguir mostra o arquivo **AssemblyInfo.cs** do aplicativo de exemplo:

```csharp
using Xamarin.Forms;
using MyCompany.Controls;

[assembly: Preserve]
[assembly: XmlnsDefinition("http://mycompany.com/schemas/controls", "MyCompany.Controls")]
```

Esse código cria um esquema de namespace personalizado que mapeia a `http://mycompany.com/schemas/controls` URL para o `MyCompany.Controls` namespace CLR. Além disso, o `Preserve` atributo é especificado no assembly, para garantir que o vinculador preserve todos os tipos no assembly.

> [!IMPORTANT]
> O `Preserve` atributo deve ser aplicado a classes no assembly que são mapeadas por meio do esquema de namespace personalizado ou aplicadas ao assembly inteiro.

O esquema de namespace personalizado pode então ser usado para a resolução de tipo em arquivos XAML.

## <a name="consuming-a-custom-namespace-schema"></a>Consumindo um esquema de namespace personalizado

Para consumir tipos do esquema de namespace personalizado, o compilador XAML requer que haja uma referência de código do assembly que consome os tipos para o assembly que define os tipos. Isso pode ser feito adicionando uma classe que contém um `Init` método ao assembly que define os tipos que serão consumidos por meio de XAML:

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

O `Init` método pode então ser chamado a partir do assembly que consome tipos do esquema de namespace personalizado:

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
> A falha ao incluir tal referência de código fará com que o compilador XAML não consiga localizar o assembly que contém os tipos de esquema de namespace personalizado.

Para consumir o `CircleButton` controle, um namespace XAML é declarado, com a declaração de namespace especificando a URL do esquema de namespace personalizado:

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

`CircleButton`as instâncias podem então ser adicionadas ao [`ContentPage`](xref:Xamarin.Forms.ContentPage) declarando-as com o `controls` prefixo do namespace.

Para localizar os tipos de esquema de namespace personalizado, o Xamarin.Forms pesquisará assemblies referenciados em busca de `XmlnsDefinitionAttribute` instâncias. Se o `xmlns` atributo de um elemento em um arquivo XAML corresponder ao `XmlNamespace` valor da propriedade em um `XmlnsDefinitionAttribute` , Xamarin.Forms o tentará usar o `XmlnsDefinitionAttribute.ClrNamespace` valor da propriedade para a resolução do tipo. Se a resolução de tipo falhar, o Xamarin.Forms continuará a tentar a resolução de tipo com base em quaisquer instâncias de correspondência adicionais `XmlnsDefinitionAttribute` .

O resultado é que duas `CircleButton` instâncias são exibidas:

![Botões de círculo](custom-namespace-schemas-images/circle-buttons.png "Botões de círculo")

## <a name="related-links"></a>Links relacionados

- [Esquemas de namespace personalizados (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-customnamespaceschemas)
- [Prefixos recomendados de namespace do XAML](custom-prefix.md)
- [Namespaces XAML emXamarin.Forms](namespaces.md)
