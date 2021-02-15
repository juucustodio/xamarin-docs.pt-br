---
title: Compilação XAML no Xamarin.Forms
description: Este artigo explica como o XAML pode ser compilado opcionalmente diretamente na IL (linguagem intermediária) com o Xamarin.Forms compilador XAML (XAMLC).
ms.prod: xamarin
ms.assetid: 9A2D10A6-5DFC-485F-A75A-2F7B98314025
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/03/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8d53f80372062f4830d92213110f01d005f92018
ms.sourcegitcommit: 4f274920d1fe906cda0bf83b8e928b3b50147d40
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509878"
---
# <a name="xaml-compilation-in-xamarinforms"></a>Compilação XAML no Xamarin.Forms

_Opcionalmente, XAML pode ser compilado direto na IL (linguagem intermediária) com o compilador XAML (XAMLC)._

A compilação XAML oferece vários benefícios:

- Executa verificação de tempo de compilação de XAML, notificando o usuário de quaisquer erros.
- Elimina parte da carga e do tempo de instanciação para elementos XAML.
- Ajuda a reduzir o tamanho do arquivo do assembly final não incluindo mais arquivos .XAML.

A compilação XAML é desabilitada por padrão na estrutura. No entanto, ele está habilitado nos modelos para novos projetos. Ele pode ser explicitamente habilitado ou desabilitado ( `XamlCompilationOptions.Skip` ) no nível do assembly e da classe adicionando o [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) atributo.

O exemplo de código a seguir demonstra como habilitar a compilação XAML no nível do assembly:

```csharp
using Xamarin.Forms.Xaml;
...
[assembly: XamlCompilation (XamlCompilationOptions.Compile)]
namespace PhotoApp
{
  ...
}
```

Embora o atributo possa ser colocado em qualquer lugar, um bom lugar para colocá-lo está em **AssemblyInfo.cs**.

Neste exemplo, a verificação em tempo de compilação de todos os XAML contidos no assembly será executada, com erros XAML sendo relatados em tempo de compilação em vez de tempo de execução. Portanto, o `assembly` prefixo para o [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) atributo especifica que o atributo se aplica a todo o assembly.

> [!NOTE]
> O [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) atributo e a [`XamlCompilationOptions`](xref:Xamarin.Forms.Xaml.XamlCompilationOptions) Enumeração residem no `Xamarin.Forms.Xaml` namespace, que deve ser importado para usá-los.

O exemplo de código a seguir demonstra como habilitar a compilação XAML no nível de classe:

```csharp
using Xamarin.Forms.Xaml;
...
[XamlCompilation (XamlCompilationOptions.Compile)]
public class HomePage : ContentPage
{
  ...
}
```

Neste exemplo, a verificação de tempo de compilação do XAML para a `HomePage` classe será executada e os erros relatados como parte do processo de compilação.

> [!NOTE]
> Associações compiladas podem ser habilitadas para melhorar o desempenho de vinculação de dados em Xamarin.Forms aplicativos. Para saber mais, confira [Associações compiladas](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

## <a name="related-links"></a>Links Relacionados

- [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute)
- [`XamlCompilationOptions`](xref:Xamarin.Forms.Xaml.XamlCompilationOptions)
