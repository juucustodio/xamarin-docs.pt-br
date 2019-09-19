---
title: Renderizar controles personalizados no previsor XAML
description: Este artigo descreve como mostrar os controles personalizados no previsor XAML.
ms.prod: xamarin
ms.assetid: 4D795372-CB8F-48F4-B63D-845E44B261F7
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/27/2019
ms.openlocfilehash: 57c0fd540ef42c18462b4f989b21bac5ed05dc04
ms.sourcegitcommit: 6b833f44d5fd8dc7ab7f8546e8b7d383e5a989db
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105993"
---
# <a name="render-custom-controls-in-the-xaml-previewer"></a>Renderizar controles personalizados no previsor XAML

_Os controles personalizados às vezes não funcionam conforme o esperado no previsor XAML. Use as diretrizes neste artigo para entender as limitações da visualização de seus controles personalizados._

## <a name="basic-preview-mode"></a>Modo de visualização básica

Mesmo que você não tenha criado seu projeto, o previsor XAML processará suas páginas. Até você compilar, qualquer controle que dependa do code-behind mostrará seu tipo base Xamarin. Forms. Quando o projeto é compilado, o Previewer XAML tentará mostrar controles personalizados com o processamento de tempo de design habilitado. Se o processamento falhar, ele mostrará o tipo base Xamarin. Forms.

## <a name="enable-design-time-rendering-for-custom-controls"></a>Habilitar renderização de tempo de design para controles personalizados

Se você fizer seus próprios controles personalizados ou usar controles de uma biblioteca de terceiros, o visualizador poderá exibi-los incorretamente. Os controles personalizados devem aceitar o processamento de tempo de design para aparecer no visualizador, quer você tenha escrito o controle ou importado de uma biblioteca. Com os controles que você criou, adicione [`[DesignTimeVisible(true)]`](xref:System.ComponentModel.DesignTimeVisibleAttribute) o à classe do seu controle para mostrá-lo no visualizador:

```csharp
namespace MyProject
{
  [DesignTimeVisible(true)]
  public class MyControl : BaseControl
  {
    // Your control's code here
  }

}
```

Use a [classe base ImageCirclePlugin's de James Montemagno](https://github.com/jamesmontemagno/ImageCirclePlugin/blob/master/src/ImageCircle/CircleImage.shared.cs) como exemplo.

## <a name="skiasharp-controls"></a>Controles SkiaSharp

Atualmente, os controles SkiaSharp só têm suporte quando você está visualizando no iOS. Eles não serão renderizados na visualização do Android.

## <a name="troubleshooting"></a>Solução de problemas

### <a name="check-your-xamarinforms-version"></a>Verifique sua versão do Xamarin. Forms
Verifique se você tem pelo menos o Xamarin. Forms 3,6 instalado. Você pode atualizar sua versão do Xamarin. Forms no NuGet.

### <a name="even-with-designtimevisibletrue-my-custom-control-isnt-rendering-properly"></a>Mesmo com `[DesignTimeVisible(true)]`o, meu controle personalizado não é renderizado corretamente.
Os controles personalizados que dependem muito dos dados code-behind ou back-end nem sempre funcionam no previsor XAML. Você pode tentar:

* Movendo o controle para que ele não seja inicializado se o [modo de design estiver habilitado](index.md#detect-design-mode)
* Configurando [dados de tempo de design](design-time-data.md) para mostrar dados falsos do back-end

### <a name="the-xaml-previewer-shows-the-error-custom-controls-arent-rendering-properly"></a>O previsor XAML mostra o erro "os controles personalizados não são renderizados corretamente"
Tente limpar e recompilar seu projeto, ou fechar e reabrir o arquivo XAML.
