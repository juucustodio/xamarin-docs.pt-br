---
title: Destinos de build
description: Este documento listará todos os destinos com suporte no processo de compilação do Xamarin. Android.
ms.prod: xamarin
ms.assetid: 17DE89FF-F316-4620-B865-EF6E0963A29C
ms.technology: xamarin-android
author: jonpryor
ms.author: jopryo
ms.date: 09/17/2020
ms.openlocfilehash: 4482e6c4bfe2a6952d59d896b7c79ca82432b42b
ms.sourcegitcommit: 38496cfd4d30fd40a011011f303a31de639bd699
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91247250"
---
# <a name="build-targets"></a>Destinos de build

Os seguintes destinos de compilação são definidos para projetos Xamarin. Android.

## <a name="build"></a>Build

Cria o código-fonte dentro de um projeto e todas as dependências.

Esse destino *não* cria um pacote Android ( `.apk` arquivo).
Para criar um pacote do Android, use [SignAndroidPackage](#signandroidpackage) o destino SignAndroidPackage *ou* defina a propriedade [' $ (AndroidBuildApplicationPackage)](~/android/deploy-test/building-apps/build-properties.md#androidbuildapplicationpackage) como true ao compilar:

```shell
msbuild /p:AndroidBuildApplicationPackage=True App.sln
```

## <a name="buildandstartaotprofiling"></a>BuildAndStartAotProfiling

Cria o aplicativo com um criador de perfil de AOT incorporado, define a porta TCP do criador de perfil como [`$(AndroidAotProfilerPort)`](~/android/deploy-test/building-apps/build-properties.md#androidaotprofilerport) e inicia a atividade padrão.

A porta TCP padrão é `9999` .

Adicionado no Xamarin. Android 10,2.

## <a name="clean"></a>Clean

Remove todos os arquivos gerados pelo processo de compilação.

## <a name="finishaotprofiling"></a>FinishAotProfiling

Deve ser chamado *após* o destino [BuildAndStartAotProfiling](#buildandstartaotprofiling) .

Coleta os dados do criador de perfil do AOT do dispositivo ou do emulador por meio da porta TCP [`$(AndroidAotProfilerPort)`](~/android/deploy-test/building-apps/build-properties.md#androidaotprofilerport)
e os grava no [`$(AndroidAotCustomProfilePath)`](~/android/deploy-test/building-apps/build-properties.md#androidaotcustomprofilepath) .

Os valores padrão para porta e perfil personalizado são `9999` e `custom.aprof` .

Para passar opções adicionais para `aprofutil` o, defina-as no [`$(AProfUtilExtraOptions)`](~/android/deploy-test/building-apps/build-properties.md#aprofutilextraoptions)
property.

Isso é equivalente a:

```shell
aprofutil $(AProfUtilExtraOptions) -s -v -f -p $(AndroidAotProfilerPort) -o "$(AndroidAotCustomProfilePath)"
```

Adicionado no Xamarin. Android 10,2.

## <a name="install"></a>Instalar

[Cria, assina](#signandroidpackage)e instala o pacote do Android no dispositivo ou dispositivo virtual padrão.

A [`$(AdbTarget)`](~/android/deploy-test/building-apps/build-properties.md#adbtarget) propriedade especifica o dispositivo de destino do Android no qual o pacote do Android pode ser instalado ou removido.

```bash
# Install package onto emulator via -e
# Use `/Library/Frameworks/Mono.framework/Commands/msbuild` on OS X
MSBuild /t:Install ProjectName.csproj /p:AdbTarget=-e
```

## <a name="signandroidpackage"></a>SignAndroidPackage

Cria e assina o arquivo de pacote do Android ( `.apk` ).

Use com `/p:Configuration=Release` para gerar pacotes de "Versão" independentes.

## <a name="startandroidactivity"></a>StartAndroidActivity

Inicia a atividade padrão no dispositivo ou no emulador em execução.

Para iniciar uma atividade diferente, defina o [`$(AndroidLaunchActivity)`](~/android/deploy-test/building-apps/build-properties.md#androidlaunchactivity)
Propriedade para o nome da atividade.

Isso é equivalente a:

```shell
adb shell am start @PACKAGE_NAME@/$(AndroidLaunchActivity)
```

Adicionado no Xamarin. Android 10,2.

## <a name="stopandroidpackage"></a>StopAndroidPackage

Interrompe completamente o pacote de aplicativos no dispositivo ou no emulador em execução.

Isso é equivalente a:

```shell
adb shell am force-stop @PACKAGE_NAME@
```

Adicionado no Xamarin. Android 10,2.

## <a name="uninstall"></a>Desinstalar

Desinstala o pacote do Android do dispositivo ou dispositivo virtual padrão.

A [`$(AdbTarget)`](~/android/deploy-test/building-apps/build-properties.md#adbtarget) propriedade especifica o dispositivo de destino do Android no qual o pacote do Android pode ser instalado ou removido.

## <a name="updateandroidresources"></a>UpdateAndroidResources

Atualiza o `Resource.designer.cs` arquivo.

Esse destino geralmente é chamado pelo IDE quando novos recursos são adicionados ao projeto.
