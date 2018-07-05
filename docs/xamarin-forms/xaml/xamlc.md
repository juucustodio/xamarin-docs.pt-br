---
title: Compilação de XAML no xamarin. Forms
description: Este artigo explica como o XAML pode ser opcionalmente compilado diretamente em linguagem intermediária (IL) com o compilador do xamarin. Forms XAML (XAMLC).
ms.prod: xamarin
ms.assetid: 9A2D10A6-5DFC-485F-A75A-2F7B98314025
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 07/02/2018
ms.openlocfilehash: 4635857fc850b2985f988f8c20ff854e487f79ed
ms.sourcegitcommit: 081a2d094774c6f75437d28b71d22607e33aae71
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37403397"
---
# <a name="xaml-compilation-in-xamarinforms"></a>Compilação de XAML no xamarin. Forms

_Pode ser compilado, opcionalmente, o XAML diretamente em linguagem intermediária (IL) com o compilador XAML (XAMLC)._

XAMLC oferece vários benefícios:

- Executa verificação de tempo de compilação de XAML, notificando o usuário de quaisquer erros.
- Elimina parte da carga e do tempo de instanciação para elementos XAML.
- Ajuda a reduzir o tamanho do arquivo do assembly final não incluindo mais arquivos .XAML.

O XAMLC é desabilitado por padrão para garantir a compatibilidade com versões anteriores. Ele pode ser habilitado no nível de classe e do assembly, adicionando o `XamlCompilation` atributo.

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

Neste exemplo, a verificação de todas as do XAML contido dentro do assembly de tempo de compilação será executado, com erros XAML que está sendo relatados no tempo de compilação em vez de tempo de execução. Portanto, o `assembly` de prefixo para o `XamlCompilation` atributo especifica que o atributo se aplica a todo o assembly.

O exemplo de código a seguir demonstra como habilitar XAMLC no nível da classe:

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
> O `XamlCompilation` atributo e o `XamlCompilationOptions` enumeração residem no `Xamarin.Forms.Xaml` namespace, que deve ser importado para usá-los.


## <a name="related-links"></a>Links relacionados

- [XamlCompilation](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.XamlCompilationAttribute/)
- [XamlCompilationOptions](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.XamlCompilationOptions/)
