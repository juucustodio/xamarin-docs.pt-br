---
title: Localização do aplicativo e recursos de cadeia de caracteres
ms.prod: xamarin
ms.assetid: 374A9DA6-1853-8B98-6954-7FE3F591C07C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: 7191c37e81da728dfda21da2eaecc95f7b58a401
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025088"
---
# <a name="application-localization-and-string-resources"></a>Localização do aplicativo e recursos de cadeia de caracteres

A localização do aplicativo é o ato de fornecer recursos alternativos para direcionar uma região ou localidade específica. Por exemplo, você pode fornecer cadeias de caracteres de idioma localizadas para vários países ou pode alterar cores ou layout para corresponder a culturas específicas. O Android carregará e usará os recursos apropriados para a localidade do dispositivo em tempo de execução sem nenhuma alteração no código-fonte.

Por exemplo, a imagem abaixo mostra o mesmo aplicativo em execução em três localidades de dispositivo diferentes, mas o texto exibido em cada botão é específico para a localidade para a qual cada dispositivo está definido:

[![exemplos de três localidades diferentes](application-localization-images/01-click-me-sml.png)](application-localization-images/01-click-me.png#lightbox)

Neste exemplo, o conteúdo de um arquivo de layout, **Main. axml** é semelhante ao seguinte:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
   android:orientation="vertical"
   android:layout_width="fill_parent"
   android:layout_height="fill_parent"
   >
<Button  
   android:id="@+id/myButton"
   android:layout_width="fill_parent"
   android:layout_height="wrap_content"
android:text="@string/hello"
   />
</LinearLayout>
```

No exemplo acima, a cadeia de caracteres do botão foi carregada a partir dos recursos fornecendo a ID de recurso para a cadeia de caracteres:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Cadeias de caracteres de recurso para três idiomas](application-localization-images/02-resource-strings-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Cadeias de caracteres de recurso para três idiomas](application-localization-images/02-resource-strings-xs.png)

-----

## <a name="localizing-android-apps"></a>Localizando aplicativos Android

Leia a [introdução à localização](~/cross-platform/app-fundamentals/localization.md) para obter dicas e orientações sobre como localizar aplicativos móveis.

O guia [Localizando aplicativos Android](~/android/app-fundamentals/localization.md) contém exemplos mais específicos sobre como converter cadeias de caracteres e localizar imagens usando o Xamarin. Android.

## <a name="related-links"></a>Links relacionados

- [Localizando aplicativos Android](~/android/app-fundamentals/localization.md)
- [Visão geral da localização entre plataformas](~/cross-platform/app-fundamentals/localization.md)
