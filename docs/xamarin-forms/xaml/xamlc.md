---
title: Compilação de XAML no xamarin. Forms
description: Este artigo explica como o XAML pode ser opcionalmente compilado diretamente na linguagem intermediária (IL) com o compilador XAML xamarin. Forms (XAMLC).
ms.prod: xamarin
ms.assetid: 9A2D10A6-5DFC-485F-A75A-2F7B98314025
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 01/21/2016
ms.openlocfilehash: 0128fecebe9f6ba8f55e965a8fa65787d03d9ded
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245739"
---
# <a name="xaml-compilation-in-xamarinforms"></a>Compilação de XAML no xamarin. Forms

_XAML pode ser compilado, opcionalmente, diretamente em linguagem intermediária (IL) com o compilador XAML (XAMLC)._

XAMLC oferece vários benefícios:

- Executa verificação de tempo de compilação de XAML, notificando o usuário de quaisquer erros.
- Elimina parte da carga e do tempo de instanciação para elementos XAML.
- Ajuda a reduzir o tamanho do arquivo do assembly final não incluindo mais arquivos .XAML.

O XAMLC é desabilitado por padrão para garantir a compatibilidade com versões anteriores. Ela pode ser habilitada no nível de assembly e a classe adicionando o `XamlCompilation` atributo.

O exemplo de código a seguir demonstra como habilitar XAMLC no nível de assembly:

```csharp
using Xamarin.Forms.Xaml;
...
[assembly: XamlCompilation (XamlCompilationOptions.Compile)]
namespace PhotoApp
{
  ...
}
```

Neste exemplo, verificação de tempo de compilação de todos os XAML contidos o `PhotoApp` namespace será executado com erros XAML que está sendo relatados no tempo de compilação em vez de tempo de execução.
O `assembly` do prefixo para o `XamlCompilation` atributo especifica que o atributo se aplica ao assembly inteiro.

O exemplo de código a seguir demonstra como habilitar XAMLC no nível de classe:

```csharp
using Xamarin.Forms.Xaml;
...
[XamlCompilation (XamlCompilationOptions.Compile)]
public class HomePage : ContentPage
{
  ...
}
```

Neste exemplo, verificando o XAML para tempo de compilação de `HomePage` classe será executada e erros são relatados como parte do processo de compilação.

> [!NOTE]
> O `XamlCompilation` atributo e o `XamlCompilationOptions` enumeração residem no `Xamarin.Forms.Xaml` namespace, que deve ser importado para usá-los.


## <a name="related-links"></a>Links relacionados

- [XamlCompilation](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.XamlCompilationAttribute/)
- [XamlCompilationOptions](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.XamlCompilationOptions/)
