---
title: Autenticação de impressão digital
description: Este guia discute como adicionar autenticação de impressão digital, introduzida no Android 6.0, a um aplicativo Xamarin.Android.
ms.prod: xamarin
ms.assetid: 6742D874-4988-4516-A946-D5C714B20A10
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 4a4b6ee7a123683a9d5a140c46c0b3542767ffa3
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027518"
---
# <a name="fingerprint-authentication"></a>Autenticação de impressão digital

_Este guia discute como adicionar autenticação de impressão digital, introduzida no Android 6.0, a um aplicativo Xamarin.Android._

## <a name="fingerprint-authentication-overview"></a>Visão geral da autenticação de impressão digital

A chegada de scanners de impressões digitais em dispositivos Android fornece aos aplicativos uma alternativa ao método tradicional de nome de usuário/senha de autenticação do usuário. O uso de impressões digitais para autenticar um usuário torna possível que um aplicativo incorpore segurança menos intrusiva do que um nome de usuário e senha.

As APIs do FingerprintManager têm como alvo dispositivos com um scanner de impressões digitais e estão executando a API nível 23 (Android 6.0) ou superior. As APIs são `Android.Hardware.Fingerprints` encontradas no namespace. A Biblioteca de Suporte para Android v4 fornece versões das APIs de impressão digital destinadas a versões mais antigas do Android. As APIs de compatibilidade `Android.Support.v4.Hardware.Fingerprint` são encontradas no namespace, são distribuídas através do [pacote Xamarin.Android.Support.v4 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.v4/).

O [FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html) (e sua contrapartida da Biblioteca de Suporte, [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)) é a classe principal para usar o hardware de digitalização de impressões digitais. Esta classe é um invólucro Android SDK em torno do serviço de nível do sistema que gerencia interações com o próprio hardware. É responsável por iniciar o scanner de impressões digitais e por responder ao feedback do scanner. Esta classe tem uma interface bastante simples com apenas três membros:

- **`Authenticate`**&ndash; Este método inicializará o scanner de hardware e iniciará o serviço em segundo plano, esperando que o usuário escaneie sua impressão digital.
- **`EnrolledFingerprints`**&ndash; Esta propriedade `true` retornará se o usuário tiver registrado uma ou mais impressões digitais com o dispositivo.
- **`HardwareDetected`**&ndash; Esta propriedade é usada para determinar se o dispositivo suporta digitalização de impressões digitais.

O `FingerprintManager.Authenticate` método é usado por um aplicativo Android para iniciar o scanner de impressões digitais. O trecho a seguir é um exemplo de como invocá-lo usando as APIs de compatibilidade da Biblioteca de Suporte:

```csharp
// context is any Android.Content.Context instance, typically the Activity 
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
fingerprintManager.Authenticate(FingerprintManager.CryptoObject crypto,
                                int flags,
                                CancellationSignal cancel,
                                FingerprintManagerCompat.AuthenticationCallback callback,
                                Handler handler
                               );
```

Este guia discutirá como `FingerprintManager` usar as APIs para melhorar um aplicativo Android com autenticação de impressão digital. Ele cobrirá como instanciar e criar um `CryptoObject` para ajudar a proteger os resultados do scanner de impressões digitais. Examinaremos como um aplicativo `FingerprintManager.AuthenticationCallback` deve subclasse e responder ao feedback do scanner de impressões digitais. Finalmente, veremos como registrar uma impressão digital em um dispositivo Android ou emulador e como usar **o ADB** para simular uma digitalização de impressões digitais.

## <a name="requirements"></a>Requisitos

A autenticação de impressão digital requer android 6.0 (Nível API 23) ou superior e um dispositivo com um scanner de impressões digitais. 

Uma impressão digital já deve ser inscrita com o dispositivo para cada usuário que deve ser autenticado. Isso envolve a configuração de um bloqueio de tela que usa uma senha, PIN, padrão de deslizamento ou reconhecimento facial. É possível simular algumas das funcionalidades de autenticação de impressão digital em um Emulador Android.  Para obter mais informações sobre esses dois tópicos, consulte a [seção 'Registrando uma impressão digital'.](enrolling-fingerprint.md) 

## <a name="related-links"></a>Links relacionados

- [Aplicativo de amostra de guia de impressão digital](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fingerprintguide)
- [Amostra de diálogo de impressão digital](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)
- [Solicitando permissões em tempo de execução](https://developer.android.com/training/permissions/requesting.html)
- [android.hardware.fingerprint](https://developer.android.com/reference/android/hardware/fingerprint/package-summary.html)
- [android.support.v4.hardware.fingerprint](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/package-summary.html)
- [Android.Content.Context](xref:Android.Content.Context)
- [Impressão digital e API de pagamentos (vídeo)](https://youtu.be/VOn7VrTRlA4)
