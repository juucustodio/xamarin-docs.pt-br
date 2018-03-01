---
title: "Compilação de XAML"
description: "Opcionalmente, XAML pode ser compilado direto na IL (linguagem intermediária) com o compilador XAML (XAMLC)."
ms.topic: article
ms.prod: xamarin
ms.assetid: 9A2D10A6-5DFC-485F-A75A-2F7B98314025
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 01/21/2016
ms.openlocfilehash: 3afb7608838a2c34f143d0563b50f03ad7f6ecf4
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="xaml-compilation"></a>Compilação de XAML

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
> **Observação**: O `XamlCompilation` atributo e o `XamlCompilationOptions` enumeração residem no `Xamarin.Forms.Xaml0` namespace, que deve ser importado para usá-los.


## <a name="related-links"></a>Links relacionados

- [XamlCompilation](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.XamlCompilationAttribute/)
- [XamlCompilationOptions](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.XamlCompilationOptions/)
