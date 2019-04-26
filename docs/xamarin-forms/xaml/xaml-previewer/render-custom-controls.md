---
title: Renderizar controles personalizados no pré-visualizador XAML
description: Este artigo descreve como mostrar seus controles personalizados no pré-visualizador XAML.
ms.prod: xamarin
ms.assetid: 4D795372-CB8F-48F4-B63D-845E44B261F7
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/27/2019
ms.openlocfilehash: 977c29312e0be8b92f216c224414f9bd03f8562d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60875971"
---
# <a name="render-custom-controls-in-the-xaml-previewer"></a>Renderizar controles personalizados no pré-visualizador XAML

_Controles personalizados às vezes, não funcionam conforme o esperado no pré-visualizador XAML. Use as diretrizes neste artigo para entender as limitações de visualização de seus controles personalizados._

## <a name="basic-preview-mode"></a>Modo de visualização básico

Mesmo se você ainda não tiver criado seu projeto, o pré-visualizador XAML será renderizado suas páginas. Até que você cria, qualquer controle que depende de lógica mostrará seu tipo base do xamarin. Forms. Quando seu projeto é compilado, o pré-visualizador XAML tentar mostrar controles personalizados com processamento em tempo de design habilitado. Se o processamento falhar, ela mostrará o tipo base do xamarin. Forms.

## <a name="enable-design-time-rendering-for-custom-controls"></a>Habilitar o processamento em tempo de design para controles personalizados

Se você fazer seus próprios controles personalizados ou use os controles de uma biblioteca de terceiros, o pré-visualizador pode exibi-los incorretamente. Controles personalizados devem aceitar renderização para aparecer no visualizador, se você escreveu o controle ou importou de uma biblioteca de tempo de design. Com controles que você criou, adicione a [ `[DesignTimeVisible(true)]` ](xref:System.ComponentModel.DesignTimeVisibleAttribute) para sua classe do controle para mostrá-lo no visualizador:

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

Use [classe de base de ImageCirclePlugin de James Montemagno](https://github.com/jamesmontemagno/ImageCirclePlugin/blob/master/src/ImageCircle/CircleImage.shared.cs) como exemplo.


## <a name="skiasharp-controls"></a>Controles de SkiaSharp

Atualmente, os controles de SkiaSharp só têm suporte quando você está visualizando no iOS. Eles não serão renderizadas na visualização do Android.

## <a name="troubleshooting"></a>Solução de problemas

### <a name="check-your-xamarinforms-version"></a>Verifique a versão do xamarin. Forms
Verifique se você tem pelo menos 3.6 do xamarin. Forms instalado. Você pode atualizar sua versão do xamarin. Forms no NuGet.

### <a name="even-with-designtimevisibletrue-my-custom-control-isnt-rendering-properly"></a>Mesmo com `[DesignTimeVisible(true)]`, meu controle personalizado não estiver renderizando corretamente.
Controles personalizados que dependem muito do code-behind ou back-end de dados sempre não funcionam no pré-visualizador XAML. Você pode tentar:
* Movendo o controle para que ele não inicializar se [o modo de design está habilitado](index.md#detect-design-mode)
* Configurando [dados de tempo de design](design-time-data.md) para mostrar dados fictícios de back-end

### <a name="the-xaml-previewer-shows-the-error-custom-controls-arent-rendering-properly"></a>O pré-visualizador XAML mostra o erro "Controles personalizados não renderizar corretamente"
Tente limpar e recriar o projeto, ou fechar e reabrir o arquivo XAML.
