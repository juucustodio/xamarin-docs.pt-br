---
title: Localização do aplicativo e recursos de cadeia de caracteres
ms.prod: xamarin
ms.assetid: 374A9DA6-1853-8B98-6954-7FE3F591C07C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: d9d90e371199c8587d61199240523cf0a23f5efd
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61013169"
---
# <a name="application-localization-and-string-resources"></a>Localização do aplicativo e recursos de cadeia de caracteres

Localização do aplicativo é o ato de fornecer recursos alternativos para uma região específica ou a localidade de destino. Por exemplo, você pode fornecer cadeias de caracteres de idioma localizados para vários países, ou você pode alterar as cores ou layout para corresponder a culturas específicas. Android carregará e usar os recursos apropriados para a localidade do dispositivo em tempo de execução sem qualquer alteração no código-fonte.

Por exemplo, a imagem abaixo mostra o mesmo aplicativo em execução em três localidades de dispositivo diferentes, mas o texto exibido em cada botão é específico para a localidade que cada dispositivo é definido como:

[![Exemplos de três diferentes localidades](application-localization-images/01-click-me-sml.png)](application-localization-images/01-click-me.png#lightbox)

Neste exemplo, o conteúdo de um arquivo de layout **Main. axml** pode ter esta aparência:

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

No exemplo acima, a cadeia de caracteres para o botão foi carregada de recursos, fornecendo a ID de recurso para a cadeia de caracteres:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Cadeias de caracteres de recurso para três idiomas](application-localization-images/02-resource-strings-vs.png)
 
# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Cadeias de caracteres de recurso para três idiomas](application-localization-images/02-resource-strings-xs.png)
 
-----
 
## <a name="localizing-android-apps"></a>Localizando aplicativos do Android

Leia as [Introdução à localização](~/cross-platform/app-fundamentals/localization.md) para obter dicas e diretrizes sobre a localização de aplicativos móveis.

O [localizando aplicativos Android](~/android/app-fundamentals/localization.md) guia contém exemplos mais específicos sobre como converter cadeias de caracteres e localizar imagens usando o xamarin. Android.



## <a name="related-links"></a>Links relacionados

- [Localizando aplicativos do Android](~/android/app-fundamentals/localization.md)
- [Visão geral de localização de plataforma cruzada](~/cross-platform/app-fundamentals/localization.md)
