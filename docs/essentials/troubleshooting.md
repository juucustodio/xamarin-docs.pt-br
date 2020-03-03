---
title: 'Xamarin.Essentials: Solução de problemas'
description: Este documento descreve como solucionar problemas encontrados durante o desenvolvimento com a biblioteca do Xamarin.Essentials.
ms.assetid: 2E474FAF-F841-4E3C-B815-F7ABD8EE3361
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.openlocfilehash: 2bd537a782b7090207b09ca02c5dfe5c4422a9ad
ms.sourcegitcommit: fec87846fcb262fc8b79774a395908c8c8fc8f5b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77545132"
---
# <a name="xamarinessentials-troubleshooting"></a>Xamarin.Essentials: Solução de problemas

## <a name="error-version-conflict-detected-for-xamarinandroidsupportcompat"></a>Erro: conflito de versão detectado para Xamarin.Android.Support.Compat

O erro a seguir pode ocorrer ao atualizar pacotes NuGet (ou adicionar um novo pacote) com um projeto Xamarin.Forms que usa o Xamarin.Essentials:

```error
NU1107: Version conflict detected for Xamarin.Android.Support.Compat. Reference the package directly from the project to resolve this issue. 
 MyApp -> Xamarin.Essentials 1.3.1 -> Xamarin.Android.Support.CustomTabs 28.0.0.3 -> Xamarin.Android.Support.Compat (= 28.0.0.3) 
 MyApp -> Xamarin.Forms 3.1.0.583944 -> Xamarin.Android.Support.v4 25.4.0.2 -> Xamarin.Android.Support.Compat (= 25.4.0.2).
```

O problema são dependências sem correspondência para os dois NuGets. Isso pode ser resolvido adicionando manualmente uma versão específica da dependência (nesse caso, **Xamarin.Android.Support.Compat**) que pode ser compatível com ambas.

Para fazer isso, adicione o NuGet que é a origem do conflito manualmente e use a lista **Versão** para selecionar uma versão específica. No momento, a versão 28.0.0.3 do NuGet do Xamarin.Android.Support.Compat e do Xamarin.Android.Support.Core.Util resolverão esse erro.

Confira [esta postagem no blog](https://redth.codes/how-to-fix-the-dreaded-version-conflict-nuget-error-in-your-xamarin-android-projects/) para obter mais informações e um vídeo sobre como resolver o problema.

Se encontrar problemas ou localizar um bug, relate o que ocorreu no [repositório GitHub do Xamarin.Essentials GitHub](https://github.com/xamarin/Essentials).
