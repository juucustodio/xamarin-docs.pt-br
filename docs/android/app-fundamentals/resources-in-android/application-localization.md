---
title: Localização do aplicativo e os recursos de cadeia de caracteres
ms.prod: xamarin
ms.assetid: 374A9DA6-1853-8B98-6954-7FE3F591C07C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/30/2017
ms.openlocfilehash: cfb127500f919b61788087465700dfed213d5eb2
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="application-localization-and-string-resources"></a>Localização do aplicativo e os recursos de cadeia de caracteres

Localização do aplicativo é o ato de fornecer recursos alternativos para uma região específica ou a localidade de destino. Por exemplo, você pode fornecer cadeias de caracteres de idioma traduzido para vários países, ou você pode alterar as cores ou layout para corresponder culturas específicas. Android carregará e usar os recursos apropriados para a localidade do dispositivo em tempo de execução sem nenhuma alteração ao código-fonte.

Por exemplo, a imagem abaixo mostra o mesmo aplicativo é executado em três locais de dispositivos diferentes, mas o texto exibido em cada botão é específico para a localidade que cada dispositivo é definido como:

[![Exemplos de três localidades diferentes](application-localization-images/01-click-me-sml.png)](application-localization-images/01-click-me.png#lightbox)

Neste exemplo, o conteúdo de um arquivo de layout, **Main.axml** parecida com esta:

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

No exemplo acima, a cadeia de caracteres para o botão foi carregada a partir dos recursos, fornecendo a ID de recurso para a cadeia de caracteres:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Cadeias de caracteres de recurso para três idiomas](application-localization-images/02-resource-strings-vs.png)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Cadeias de caracteres de recurso para três idiomas](application-localization-images/02-resource-strings-xs.png)
 
-----
 
## <a name="localizing-android-apps"></a>Localizando aplicativos Android

Leitura de [Introdução à localização](~/cross-platform/app-fundamentals/localization.md) para obter dicas e diretrizes sobre a localização de aplicativos móveis.

O [localizando aplicativos Android](~/android/app-fundamentals/localization.md) guia contém exemplos mais específicos sobre como converter cadeias de caracteres e localizar imagens usando o xamarin.



## <a name="related-links"></a>Links relacionados

- [Localizando aplicativos Android](~/android/app-fundamentals/localization.md)
- [Visão geral de localização de plataforma cruzada](~/cross-platform/app-fundamentals/localization.md)
