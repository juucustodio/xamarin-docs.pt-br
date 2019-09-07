---
title: Instalando o Xamarin.Android como um aplicativo de sistema
description: Este guia discute as diferenças entre um aplicativo de sistema e um aplicativo de usuário e como instalar um aplicativo Xamarin.Android como um aplicativo de sistema. Este guia se aplica aos autores de imagens ROM personalizadas do Android. Ele não explicará como criar um ROM personalizado.
ms.prod: xamarin
ms.assetid: 0113143B-7D8D-4C4C-B2F5-B966A2E7CE1F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: 9ef13816dc1bc11eab0105ec223c2ef98366e4e5
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70754000"
---
# <a name="installing-xamarinandroid-as-a-system-app"></a>Instalando o Xamarin.Android como um aplicativo de sistema

_Este guia discute as diferenças entre um aplicativo de sistema e um aplicativo de usuário e como instalar um aplicativo Xamarin.Android como um aplicativo de sistema. Este guia se aplica aos autores de imagens ROM personalizadas do Android. Ele não explicará como criar um ROM personalizado._

## <a name="system-app"></a>Aplicativo de sistema

Autores de imagens ROM personalizadas do Android ou fabricantes de dispositivos Android podem desejar incluir um aplicativo Xamarin.Android como um _aplicativo de sistema_ ao distribuir uma ROM ou um dispositivo. Um aplicativo de sistema é um aplicativo que é considerado importante para o funcionamento do dispositivo ou que oferece uma funcionalidade que o autor do ROM personalizado deseja que esteja sempre disponível.

Aplicativos de sistema são instalados na pasta **/system/app/** (um diretório somente leitura no sistema de arquivos) e não pode ser excluída nem movida pelo usuário, a menos que o usuário tenha acesso à raiz. Por outro lado, um aplicativo que é instalado pelo usuário (normalmente do Google Play ou realizando sideload do aplicativo) é conhecido como um _aplicativo de usuário_. Aplicativos de usuário podem ser excluídos pelo usuário e em muitos casos podem ser movidos para um local diferente no dispositivo (assim como algum tipo de armazenamento externo).

Aplicativos de sistema se comportam exatamente como aplicativos de usuário, mas tem as seguintes exceções notáveis:

- Aplicativos de sistema são atualizáveis, exatamente como um _aplicativo de usuário_ normal. No entanto, como uma cópia do aplicativo sempre existe em **/system/app/** , sempre é possível reverter o aplicativo para a versão original.

- Determinadas permissões somente para sistema que não estão disponíveis para um aplicativo de usuário podem ser concedidas a aplicativos de sistema. É um exemplo de uma permissão somente para sistema é [`BLUETOOTH_PRIVILEGED`](https://developer.android.com/reference/android/Manifest.permission.html#BLUETOOTH_PRIVILEGED), que permite que os aplicativos emparelhem com dispositivos Bluetooth sem nenhuma interação do usuário.

É possível distribuir um aplicativo Xamarin.Android como um aplicativo de sistema. Além de fornecer um APK para o ROM personalizado, há duas bibliotecas compartilhadas, **libmonodroid.so** e **libmonosgen-2.0.so**, que devem ser copiadas manualmente do APK para o sistema de arquivos da imagem ROM. Este guia explica as etapas envolvidas.

## <a name="restrictions"></a>Restrições

Este guia se aplica aos autores de imagens ROM personalizadas do Android. Ele não explicará como criar um ROM personalizado.

Este guia presume familiaridade com o procedimento de [empacotar uma versão APK para um Xamarin.Android](~/android/deploy-test/publishing/index.md) e uma compreensão de [arquiteturas de CPU](~/android/app-fundamentals/cpu-architectures.md) para aplicativos Android.

## <a name="install-a-xamarinandroid-app-as-a-system-app"></a>Instalar o aplicativo Xamarin.Android como um aplicativo de sistema

As etapas a seguir descrevem como instalar um aplicativo Xamarin.Android como um aplicativo de sistema.

1. **Empacotar um APK de lançamento do aplicativo Xamarin.Android** &ndash; isso é descrito mais detalhadamente no guia [Publicar um aplicativo](~/android/deploy-test/publishing/index.md).

2. **Extrair bibliotecas compartilhadas do APK** &ndash; usando qualquer programa utilitário ZIP, abra o arquivo do APK e examine o conteúdo da pasta **/lib/** . Essa pasta terá um subdiretório para cada ABI (_interface binária de aplicativo_) que for compatível com o aplicativo; o conteúdo dessa pasta incluirá todas as bibliotecas compartilhadas que forem exigidas pelo aplicativo nessa ABI específica:

    ![Captura de tela de arquivos .so na pasta armeabi-v7a de taskypro.zip](install-system-app-images/install-system-app-01.png)

   Na captura de tela anterior, há apenas uma ABI compatível (**armeabi-v7a**) contendo os dois arquivos **.so** que são exigidos pelo aplicativo. Observe que só é necessário extrair os arquivos ABI que são apropriados para o dispositivo ou a arquitetura de destino do dispositivo ROM, ou seja, não copie arquivos **.so** da pasta **x86** para um ROM ou dispositivo **armeabi-v7a**.

3. **Copiar arquivos .so para /system/lib** &ndash; copie os arquivos **.so** que foram extraídos do APK na etapa anterior para a pasta **/system/lib/** no ROM do personalizado.

4. **Copiar o arquivo APK/system/app** &ndash; a etapa final é copiar o arquivo APK para a pasta **/system/app** no ROM.

## <a name="summary"></a>Resumo

Este guia discute as diferenças entre um _aplicativo de sistema_ e um _aplicativo de usuário_ e explica como instalar um aplicativo Xamarin.Android como um aplicativo de sistema.

## <a name="related-links"></a>Links relacionados

- [Publicando um aplicativo](~/android/deploy-test/publishing/index.md)
- [Arquiteturas de CPU](~/android/app-fundamentals/cpu-architectures.md)
- [BLUETOOTH_PRIVILEGED](https://developer.android.com/reference/android/Manifest.permission.html#BLUETOOTH_PRIVILEGED)
- [Gerenciamento de ABI](https://developer.android.com/ndk~/abis.html)
