---
title: Visual do xamarin. Forms
description: Este artigo apresenta o Visual do xamarin. Forms, que renderiza modos de exibição de forma idêntica ou em grande parte de forma idêntica, no iOS e Android.
ms.prod: xamarin
ms.assetid: 80BF9C72-AC28-4AAF-9DDD-B60CBDD1CD59
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/12/2018
ms.openlocfilehash: df2351e35817a6468fed236752eea8e5ad11024f
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53061880"
---
# <a name="xamarinforms-visual"></a>Visual do xamarin. Forms

![Visualizar](~/media/shared/preview.png)

_Este artigo apresenta o Visual do xamarin. Forms, que renderiza modos de exibição de forma idêntica ou em grande parte de forma idêntica, no iOS e Android._

Muitos desenvolvedores desejam criar aplicativos xamarin. Forms que se parecem idênticos ou praticamente idênticas, no iOS e Android. Xamarin. Forms 4.0-pre1 inclui um mecanismo para renderizadores adicionais que implementam uma aparência visual, com aceitando a aparência por meio de aplicativos, incluindo um `Visual` propriedade:

```xaml
<ContentPage ...
             Visual="Material">
    ...
</ContentPage>    
```

Os renderizadores que implementam a aparência visual, em seguida, são usados para modos de exibição de processador, em vez dos renderizadores de padrão. Em tempo de seleção do renderizador, o `Visual` propriedade do modo de exibição é inspecionada e incluída no processo de seleção do renderizador. Além disso, se o `Visual` propriedade é alterada em tempo de execução, o renderizador especificado é recriado, juntamente com todos os filhos.

> [!IMPORTANT]
> O `Visual` propriedade está definida na `VisualElement` classe, com modos de exibição herdando a `Visual` valor da propriedade de seus pais. Portanto, definir a `Visual` propriedade em um `ContentPage` garante que todas as exibições com suporte na página usarão essa aparência visual. Além disso, o `Visual` propriedade pode ser substituída em uma exibição.

Xamarin. Forms 4.0-pre1 inclui uma aparência visual experimental com base no design de material, com os renderizadores sendo conhecidos como os renderizadores de material. Os renderizadores de material estão atualmente incluídos para as seguintes exibições no iOS e Android:

- [`Button`](xref:Xamarin.Forms.Button)
- [`Entry`](xref:Xamarin.Forms.Entry)
- [`Frame`](xref:Xamarin.Forms.Frame)
- [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)

Funcionalmente, os renderizadores de material não são diferentes para os renderizadores de padrão. No entanto, eles atualmente são experimentais e só pode ser usados, adicionando a seguinte linha de código para seus `AppDelegate` classe no iOS, ou para seus `MainActivity` classe no Android, antes de chamar `Forms.Init`:

```csharp
Forms.SetFlags("Visual_Experimental");
```

Além disso, no iOS, seu projeto de plataforma deve ter o [Xamarin.iOS.MaterialComponents](https://www.nuget.org/packages/Xamarin.iOS.MaterialComponents/) pacote NuGet instalado. No Android, o Visual funciona apenas com API 29, seu projeto de plataforma deve usar v28 das bibliotecas de suporte e definir seu tema para herdar de um tema de Material componentes ou continuar herdar de um tema AppCompat durante a adição de alguns novos atributos de tema para o tema. Para obter mais informações, consulte [Introdução aos componentes de Material para Android](https://github.com/material-components/material-components-android/blob/master/docs/getting-started.md).

As capturas de tela a seguir mostram uma interface do usuário que inclui quatro modos de exibição para o qual material renderizadores existem, mas renderizados usando os processadores de padrão:

[![Padrão renderizadores](visual-images/default-renderers.png "exibições usando renderizadores padrão")](visual-images/default-renderers-large.png#lightbox)

As capturas de tela a seguir mostram a mesma interface do usuário renderizado usando os processadores de materiais:

[![Os renderizadores de material](visual-images/material-renderers.png "exibições usando renderizadores de material")](visual-images/material-renderers-large.png#lightbox)

> [!NOTE]
> Com processadores de material os controles renderizados permanecem controles nativos e, portanto, ainda haverá diferenças de interface de usuário entre plataformas para áreas como sombras, fontes, cores e elevação.

## <a name="related-links"></a>Links relacionados

- [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
