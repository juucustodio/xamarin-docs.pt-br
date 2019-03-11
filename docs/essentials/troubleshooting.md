---
title: 'Xamarin.Essentials: Solução de problemas'
description: Este documento descreve como solucionar problemas encontrados durante o desenvolvimento com a biblioteca do Xamarin.Essentials.
ms.assetid: 2E474FAF-F841-4E3C-B815-F7ABD8EE3361
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: d13589680161de4c9b5d77eef6d5f823cc884136
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57671423"
---
# <a name="xamarinessentials-troubleshooting"></a>Xamarin.Essentials: Solução de problemas

## <a name="error-version-conflict-detected-for-xamarinandroidsupportcompat"></a>Erro: conflito de versão detectado para Xamarin.Android.Support.Compat

O erro a seguir pode ocorrer ao atualizar pacotes NuGet (ou adicionar um novo pacote) com um projeto Xamarin.Forms que usa o Xamarin.Essentials:

```error
NU1107: Version conflict detected for Xamarin.Android.Support.Compat. Reference the package directly from the project to resolve this issue. 
 MyApp -> Xamarin.Essentials 0.8.0-preview -> Xamarin.Android.Support.CustomTabs 27.0.2.1 -> Xamarin.Android.Support.Compat (= 27.0.2.1) 
 MyApp -> Xamarin.Forms 3.1.0.583944 -> Xamarin.Android.Support.v4 25.4.0.2 -> Xamarin.Android.Support.Compat (= 25.4.0.2).
```

O problema são dependências sem correspondência para os dois NuGets. Isso pode ser resolvido adicionando manualmente uma versão específica da dependência (nesse caso, **Xamarin.Android.Support.Compat**) que pode ser compatível com ambas.

Para fazer isso, adicione o NuGet que é a origem do conflito manualmente e use a lista **Versão** para selecionar uma versão específica. No momento, a versão 27.0.2.1 do NuGet do Xamarin.Android.Support.Compat e do Xamarin.Android.Support.Core.Util resolverão esse erro.

Confira [esta postagem no blog](https://redth.codes/how-to-fix-the-dreaded-version-conflict-nuget-error-in-your-xamarin-android-projects/) para obter mais informações e um vídeo sobre como resolver o problema.

Se encontrar problemas ou localizar um bug, relate o que ocorreu no [repositório GitHub do Xamarin.Essentials GitHub](https://github.com/xamarin/Essentials).
