---
title: Compilação de XAML no xamarin. Forms
description: Este artigo explica como o XAML pode ser opcionalmente compilado diretamente em linguagem intermediária (IL) com o compilador do xamarin. Forms XAML (XAMLC).
ms.prod: xamarin
ms.assetid: 9A2D10A6-5DFC-485F-A75A-2F7B98314025
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/22/2018
ms.openlocfilehash: 9567f3ad8d748a94a03cd1c86254072d4ba3bbdc
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563518"
---
# <a name="xaml-compilation-in-xamarinforms"></a>Compilação de XAML no xamarin. Forms

_Pode ser compilado, opcionalmente, o XAML diretamente em linguagem intermediária (IL) com o compilador XAML (XAMLC)._

Compilação de XAML oferece vários benefícios:

- Executa verificação de tempo de compilação de XAML, notificando o usuário de quaisquer erros.
- Elimina parte da carga e do tempo de instanciação para elementos XAML.
- Ajuda a reduzir o tamanho do arquivo do assembly final não incluindo mais arquivos .XAML.

Compilação de XAML está desabilitada por padrão, para garantir a compatibilidade com versões anteriores. Ele pode ser habilitado no nível de classe e do assembly adicionando a [ `XamlCompilation` ](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) atributo.

O exemplo de código a seguir demonstra como habilitar compilação de XAML no nível de assembly:

```csharp
using Xamarin.Forms.Xaml;
...
[assembly: XamlCompilation (XamlCompilationOptions.Compile)]
namespace PhotoApp
{
  ...
}
```

Neste exemplo, a verificação de todas as do XAML contido dentro do assembly de tempo de compilação será executado, com erros XAML que está sendo relatados no tempo de compilação em vez de tempo de execução. Portanto, o `assembly` de prefixo para o [ `XamlCompilation` ](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) atributo especifica que o atributo se aplica a todo o assembly.

> [!NOTE]
> O [ `XamlCompilation` ](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) atributo e o [ `XamlCompilationOptions` ](xref:Xamarin.Forms.Xaml.XamlCompilationOptions) enumeração residem no `Xamarin.Forms.Xaml` namespace, que deve ser importado para usá-los.

O exemplo de código a seguir demonstra como habilitar compilação de XAML no nível da classe:

```csharp
using Xamarin.Forms.Xaml;
...
[XamlCompilation (XamlCompilationOptions.Compile)]
public class HomePage : ContentPage
{
  ...
}
```

Neste exemplo, a verificação de que o XAML para o tempo de compilação a `HomePage` classe será executado e erros são relatados como parte do processo de compilação.

> [!NOTE]
> Associações compiladas podem ser habilitadas para melhorar o desempenho de associação de dados em aplicativos xamarin. Forms. Para obter mais informações, consulte [compilado associações](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

## <a name="related-links"></a>Links relacionados

- [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute)
- [`XamlCompilationOptions`](xref:Xamarin.Forms.Xaml.XamlCompilationOptions)
