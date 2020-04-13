---
title: Qual versão do Xamarin.Android adicionou suporte ao Lollipop?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 63B6E10C-098D-4C82-9253-07CA62EA85A5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 9e36189c771ed0c91a6030fd0ab615ab9af4dd52
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73026710"
---
# <a name="what-version-of-xamarinandroid-added-lollipop-support"></a>Qual versão do Xamarin.Android adicionou suporte ao Lollipop?

> [!NOTE]
> Este guia foi originalmente escrito para a pré-visualização do Android L.

- [Xamarin.Android 4.17](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_4/xamarin.android_4.17/index.md) adicionou suporte ao Android L Preview.
- [Xamarin.Android 4.20](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_4/xamarin.android_4.20/index.md) adicionou suporte ao Android Lollipop.

Xamarin só suporta ativamente a versão estável atual das ferramentas Xamarin. As informações abaixo são fornecidas "como estão" para versões mais antigas das ferramentas. Para obter as últimas informações sobre os lançamentos da Xamarin, verifique as [notas de lançamento](https://docs.microsoft.com/xamarin/whats-new/#product-release-notes).

## <a name="missing-androidjar-for-api-level-21-in-android-l-preview"></a>"Faltando android.jar para API Nível 21" no Android L Preview

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

A seguinte mensagem de erro (ou semelhante) pode aparecer:

```cmd
Error 1 Could not find android.jar for API Level 21.
```

Esta mensagem significa que a plataforma Android SDK para API Nível 21 não está instalada. Instale-o no Android SDK Manager (**Ferramentas > Abra o Android SDK Manager...**), ou altere seu projeto Xamarin.Android para direcionar uma versão de API instalada.

Existem algumas soluçãos para esta questão:

1. Altere seu projeto para que ele tenha como alvo a API 19 ou inferior.

2. Renomeie sua pasta android-21 do android-21 para o android-L. (Na melhor das hipóteses, isso só deve ser usado como uma correção temporária, e pode não funcionar muito bem.)

   **%LOCALAPPDATA%\\\\Android\\android-sdk\\plataformas android-21**

3. Rebaixando temporariamente para a visualização de "L" do Android API Nível 21 [1]:

    1. Exclua as **plataformas\\\\\\%LOCALAPPDATA%\\Android android-sdk android-21** 
    2. Extrair [1] em **\\C:&gt;\\Usuários\\\\&lt;\\nome\\de usuário\\AppData Local Android android-sdk plataformas** para criar uma pasta **android-L.**

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

A seguinte mensagem de erro (ou semelhante) pode aparecer:

```bash
/Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets: 
Error: Could not find android.jar for API Level 21.**
```

Isso significa que a plataforma Android SDK para API Nível 21 não está instalada. Instale-o no Android SDK Manager (Ferramentas > SDK Manager...), ou altere seu projeto Xamarin.Android para direcionar uma versão de API instalada.

Existem algumas soluçãos para esta questão:

1. Altere seu projeto para que ele tenha como alvo a API 19 ou inferior.

2. Renomeie sua pasta android-21 do android-21 para o android-L. (Na melhor das hipóteses, isso só deve ser usado como uma correção temporária, e pode não funcionar muito bem.)

   **~/Biblioteca/Desenvolvedor/Xamarin/android-sdk-macosx/android-21**

3. Rebaixando temporariamente para a visualização de "L" do Android API Nível 21 [1]:

    1. Excluir **/Usuários/Nome de usuário/Biblioteca/Desenvolvedor/Xamarin/android-sdk-macosx/android-21**
    2. Extrair [1] em **/Users/username/Library/Developer/Xamarin/android-sdk-macosx** para criar uma pasta **android-L.**

-----

[1] -[https://dl-ssl.google.com/android/repository/android-L_r04.zip](https://dl-ssl.google.com/android/repository/android-L_r04.zip)
