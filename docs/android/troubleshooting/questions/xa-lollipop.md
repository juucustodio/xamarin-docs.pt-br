---
title: Qual versão do Xamarin.Android adicionou suporte ao Lollipop?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 63B6E10C-098D-4C82-9253-07CA62EA85A5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 7e31f9ad46a04b648a6a1f24c075426f7d98a663
ms.sourcegitcommit: 6655cccf9d3be755773c2f774b5918e0b141bf84
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2019
ms.locfileid: "57305601"
---
# <a name="what-version-of-xamarinandroid-added-lollipop-support"></a>Qual versão do Xamarin.Android adicionou suporte ao Lollipop?

**Observação:** Este guia foi escrito originalmente para a visualização do Android L.

-   [Xamarin. Android 4.17](https://developer.xamarin.com/releases/android/xamarin.android_4/xamarin.android_4.17/) adicionou suporte de visualização do Android L.
-   [Xamarin. Android 4.20](https://developer.xamarin.com/releases/android/xamarin.android_4/xamarin.android_4.20/) adicionou suporte ao Android Lollipop.

Xamarin dá suporte ativamente apenas a versão estável atual das ferramentas do Xamarin. As informações abaixo são fornecidas "como-está" para versões mais antigas das ferramentas. Para obter as informações mais recentes sobre versões do Xamarin, verifique [aqui](http://releases.xamarin.com/).

## <a name="missing-androidjar-for-api-level-21-in-android-l-preview"></a>"Ausentes android.jar para API nível 21" na versão prévia do Android L

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

A mensagem de erro a seguir (ou semelhante) pode aparecer:

```cmd
Error 1 Could not find android.jar for API Level 21.
```

Essa mensagem significa que a plataforma do SDK do Android para API nível 21 não está instalada. Instale-o no Gerenciador de SDK do Android (**Ferramentas > Abrir Gerenciador de SDK Android...** ), ou alterar seu projeto xamarin. Android para direcionar uma versão de API que está instalada.

Há algumas soluções alternativas para esse problema:

1. Altere seu projeto para que ele se destina a API 19 ou inferior.

2. Renomeie a pasta android 21 de 21 de android para android-L. (Na melhor das hipóteses, isso só deve ser usado como uma correção temporária, e ele pode não funcionar muito bem em todos os.)

   **%LOCALAPPDATA%\\Android\\android-sdk\\platforms\\android-21**

3. Temporariamente fazer downgrade para a visualização do Android API nível 21 "L" [1]:

    1.  Excluir o **% LOCALAPPDATA %\\Android\\android-sdk\\plataformas\\android-21** 
    2.  Extrair [1] para **c:\\os usuários\\&lt;username&gt;\\AppData\\Local\\Android\\sdk do android\\plataformas**  para criar um **android-L** pasta.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

A mensagem de erro a seguir (ou semelhante) pode aparecer:

```bash
/Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets: 
Error: Could not find android.jar for API Level 21.**
```

Isso significa que a plataforma do SDK do Android para API nível 21 não está instalada. Instale-o no Gerenciador de SDK do Android (Ferramentas > Gerenciador de SDK...), ou alterar seu projeto xamarin. Android para direcionar uma versão de API que está instalada.

Há algumas soluções alternativas para esse problema:

1. Altere seu projeto para que ele se destina a API 19 ou inferior.

2. Renomeie a pasta android 21 de 21 de android para android-L. (Na melhor das hipóteses, isso só deve ser usado como uma correção temporária, e ele pode não funcionar muito bem em todos os.)

   **~/Library/Developer/Xamarin/android-sdk-macosx/android-21**

3. Temporariamente fazer downgrade para a visualização do Android API nível 21 "L" [1]:

    1.  Excluir **/Users/username/Library/Developer/Xamarin/android-sdk-macosx/android-21**
    2.  Extrair [1] para **/Users/username/Library/Developer/Xamarin/android-sdk-macosx** para criar um **android-L** pasta.

-----


[1] - [https://dl-ssl.google.com/android/repository/android-L_r04.zip](https://dl-ssl.google.com/android/repository/android-L_r04.zip)
