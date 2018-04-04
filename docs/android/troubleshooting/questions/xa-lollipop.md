---
title: Qual versão do xamarin adicionado suporte de pirulito?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 63B6E10C-098D-4C82-9253-07CA62EA85A5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 065c68a373f67bb352b59dc88ef89daec8b51ef8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="what-version-of-xamarinandroid-added-lollipop-support"></a>Qual versão do xamarin adicionado suporte de pirulito?

**Observação:** originalmente, este guia foi elaborado para a visualização de L Android.

-   [Xamarin 4.17](https://developer.xamarin.com/releases/android/xamarin.android_4/xamarin.android_4.17/) adicionado suporte do Android visualização de L.
-   [Xamarin 4.20](https://developer.xamarin.com/releases/android/xamarin.android_4/xamarin.android_4.20/) adicionado suporte de pirulito Android.

Xamarin dá suporte a apenas ativamente a versão estável atual das ferramentas do Xamarin. As informações a seguir são fornecidas "como-é" para versões mais antigas das ferramentas. Para obter as informações mais recentes sobre as versões do Xamarin, verifique [aqui](http://releases.xamarin.com/).

## <a name="missing-androidjar-for-api-level-21-in-android-l-preview"></a>"Ausente android.jar para nível de API 21" na visualização do Android L

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

A seguinte mensagem de erro (ou semelhante) pode aparecer:

```cmd
Error 1 Could not find android.jar for API Level 21.
```

Essa mensagem significa que a plataforma do SDK do Android para 21 de nível de API não está instalada. Instale-o no Gerenciador de SDK do Android (Ferramentas > Abrir Android SDK Manager...), ou alterar o projeto xamarin para direcionar uma versão de API que está instalada.

Há algumas soluções alternativas para esse problema:

1. Alterar o projeto para que ele tem como alvo 19 da API ou inferior.

2. Renomeie a pasta android-21 de 21 de android para o android-L. (Na melhor das hipóteses, só deve ser usado como uma correção temporária e ele pode não funcionar muito bem.)

   **%LOCALAPPDATA%\\Android\\android-sdk\\platforms\\android-21**

3. Fazer o downgrade temporariamente para a visualização de 21 de nível de API Android "L" [1]:

    1.  Excluir o **% LOCALAPPDATA %\\Android\\android sdk\\plataformas\\android-21** 
    2.  Extrair [1] no **c:\\usuários\\<username>\\AppData\\Local\\Android\\android sdk\\plataformas** para criar um **android-L** pasta.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

A seguinte mensagem de erro (ou semelhante) pode aparecer:

```bash
/Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets: 
Error: Could not find android.jar for API Level 21.**
```

Isso significa que a plataforma do SDK do Android para 21 de nível de API não está instalada. Instale-o no Gerenciador de SDK do Android (Ferramentas > Gerenciador de SDK...), ou alterar o projeto xamarin para direcionar uma versão de API que está instalada.

Há algumas soluções alternativas para esse problema:

1. Alterar o projeto para que ele tem como alvo 19 da API ou inferior.

2. Renomeie a pasta android-21 de 21 de android para o android-L. (Na melhor das hipóteses, só deve ser usado como uma correção temporária e ele pode não funcionar muito bem.)

   **~/Library/Developer/Xamarin/android-sdk-macosx/android-21**

3. Fazer o downgrade temporariamente para a visualização de 21 de nível de API Android "L" [1]:

    1.  Excluir **/Users/username/Library/Developer/Xamarin/android-sdk-macosx/android-21**
    2.  Extrair [1] no **/Users/username/Library/Developer/Xamarin/android-sdk-macosx** para criar um **android-L** pasta.

-----


[1] - [https://dl-ssl.google.com/android/repository/android-L_r04.zip](https://dl-ssl.google.com/android/repository/android-L_r04.zip)
