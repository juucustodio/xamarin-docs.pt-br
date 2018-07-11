---
title: 'Xamarin.Essentials: solução de problemas'
description: Este documento descreve como solucionar problemas encontrados durante o desenvolvimento com a biblioteca Xamarin.Essentials.
ms.assetid: 2E474FAF-F841-4E3C-B815-F7ABD8EE3361
author: jamesmontemagno
ms.author: jamont
ms.date: 06/26/2018
ms.openlocfilehash: 8cb18ab029d2fd161c60fda7e130f319b8f0c3ab
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37947368"
---
# <a name="xamarinessentials-troubleshooting"></a>Xamarin.Essentials: solução de problemas

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

## <a name="error-version-conflict-detected-for-xamarinandroidsupportcompat"></a>Erro: Conflito de versão detectado para Xamarin.Android.Support.Compat

O seguinte erro pode ocorrer quando Atualizando pacotes do NuGet (ou adicionando um novo pacote) com um projeto xamarin. Forms que usa Xamarin.Essentials:

```
NU1107: Version conflict detected for Xamarin.Android.Support.Compat. Reference the package directly from the project to resolve this issue. 
 MyApp -> Xamarin.Essentials 0.8.0-preview -> Xamarin.Android.Support.CustomTabs 27.0.2.1 -> Xamarin.Android.Support.Compat (= 27.0.2.1) 
 MyApp -> Xamarin.Forms 3.1.0.583944 -> Xamarin.Android.Support.v4 25.4.0.2 -> Xamarin.Android.Support.Compat (= 25.4.0.2).
```

O problema é dependências incompatíveis para os dois NuGets. Isso pode ser resolvido adicionando manualmente uma versão específica da dependência (neste caso **Xamarin.Android.Support.Compat**) que pode dar suporte a ambos.

Para fazer isso, adicione o NuGet que é a origem do conflito manualmente e usar o **versão** lista para selecionar uma versão específica. Atualmente, versão 27.0.2.1 do Xamarin.Android.Support.Compat & Xamarin.Android.Support.Core.Util NuGet resolverá esse erro.

Consulte a [esta postagem de blog](https://redth.codes/how-to-fix-the-dreaded-version-conflict-nuget-error-in-your-xamarin-android-projects/) para obter mais informações e um vídeo sobre como resolver o problema.

Se deparar com problemas ou localizar um bug, informe-na [repositório Xamarin.Essentials GitHub](http://github.com/xamarin/Essentials).
