---
title: Qual versão do Xamarin.Android adicionou suporte ao Lollipop?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 63B6E10C-098D-4C82-9253-07CA62EA85A5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 27631d19ea157c1af08666b55a067e729b806434
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453630"
---
# <a name="what-version-of-xamarinandroid-added-lollipop-support"></a>Qual versão do Xamarin.Android adicionou suporte ao Lollipop?

> [!NOTE]
> Este guia foi escrito originalmente para a visualização do Android L.

- [Xamarin. Android 4,17](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_4/xamarin.android_4.17/index.md) adicionou suporte à versão prévia do Android L Preview.
- [Xamarin. Android 4,20](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_4/xamarin.android_4.20/index.md) adicionou suporte a pirulito do Android.

O xamarin só dá suporte ativamente à versão estável atual das ferramentas do Xamarin. As informações abaixo são fornecidas "no estado em que se encontram" para versões mais antigas das ferramentas. Para obter as informações mais recentes sobre versões do Xamarin, consulte as [notas de versão](/xamarin/android/release-notes/).

## <a name="missing-androidjar-for-api-level-21-in-android-l-preview"></a>"Android. jar ausente para a API nível 21" na visualização do Android L

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

A seguinte mensagem de erro (ou semelhante) pode aparecer:

```cmd
Error 1 Could not find android.jar for API Level 21.
```

Essa mensagem significa que a plataforma de SDK do Android para o nível de API 21 não está instalada. Instale-o no Gerenciador de SDK do Android (**ferramentas > abrir o Gerenciador de SDK do Android...**) ou altere seu projeto Xamarin. Android para direcionar uma versão de API que está instalada.

Há algumas soluções alternativas para esse problema:

1. Altere seu projeto para que ele tenha como destino a API 19 ou inferior.

2. Renomeie sua pasta Android-21 do Android-21 para Android-L. (Na melhor das hipóteses, isso só deve ser usado como uma correção temporária e pode não funcionar muito bem.)

   **% LOCALAPPDATA% \\ Android \\ -plataformas do \\ SDK \\ Android-21**

3. Fazer downgrade temporário de volta para a API do Android nível 21 "L" Preview [1]:

    1. Exclua as **plataformas% LocalAppData% \\ Android \\ Android-SDK \\ \\ Android-21** 
    2. Extraia [1] em **C: \\ Users \\ &lt; nome_do_usuário &gt; \\ AppData \\ local Android \\ \\ -SDK \\ plataformas** para criar uma pasta do **Android-L** .

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

A seguinte mensagem de erro (ou semelhante) pode aparecer:

```bash
/Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets: 
Error: Could not find android.jar for API Level 21.**
```

Isso significa que a plataforma de SDK do Android para o nível de API 21 não está instalada. Instale-o no Gerenciador de SDK do Android (ferramentas > Gerenciador do SDK...) ou altere seu projeto Xamarin. Android para direcionar uma versão de API que está instalada.

Há algumas soluções alternativas para esse problema:

1. Altere seu projeto para que ele tenha como destino a API 19 ou inferior.

2. Renomeie sua pasta Android-21 do Android-21 para Android-L. (Na melhor das hipóteses, isso só deve ser usado como uma correção temporária e pode não funcionar muito bem.)

   **~/Library/Developer/Xamarin/android-sdk-macosx/android-21**

3. Fazer downgrade temporário de volta para a API do Android nível 21 "L" Preview [1]:

    1. Excluir **/Users/username/library/Developer/Xamarin/Android-SDK-MacOSX/Android-21**
    2. Extraia [1] em **/Users/username/library/Developer/Xamarin/Android-SDK-MacOSX** para criar uma pasta do **Android-L** .

-----

[1]- [https://dl-ssl.google.com/android/repository/android-L_r04.zip](https://dl-ssl.google.com/android/repository/android-L_r04.zip)