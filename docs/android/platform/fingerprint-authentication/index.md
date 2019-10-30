---
title: Autenticação de impressão digital
description: Este guia discute como adicionar a autenticação de impressão digital, introduzida no Android 6,0, a um aplicativo Xamarin. Android.
ms.prod: xamarin
ms.assetid: 6742D874-4988-4516-A946-D5C714B20A10
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 4a4b6ee7a123683a9d5a140c46c0b3542767ffa3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027518"
---
# <a name="fingerprint-authentication"></a>Autenticação de impressão digital

_Este guia discute como adicionar a autenticação de impressão digital, introduzida no Android 6,0, a um aplicativo Xamarin. Android._

## <a name="fingerprint-authentication-overview"></a>Visão geral da autenticação de impressão digital

A chegada de scanners de impressão digital em dispositivos Android fornece aos aplicativos uma alternativa ao método de nome de usuário/senha tradicional de autenticação de usuários. O uso de impressões digitais para autenticar um usuário possibilita que um aplicativo incorpore segurança menos invasiva do que um nome de usuário e senha.

As APIs de impressões digitais direcionam dispositivos com um scanner de impressão digital e estão executando o nível de API 23 (Android 6,0) ou superior. As APIs são encontradas no namespace `Android.Hardware.Fingerprints`. A biblioteca de suporte do Android v4 fornece versões das APIs de impressão digital destinadas a versões mais antigas do Android. As APIs de compatibilidade são encontradas no namespace `Android.Support.v4.Hardware.Fingerprint`, são distribuídas por meio do [pacote NuGet Xamarin. Android. support. v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/).

O [fingerprintid](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html) (e seu equivalente da biblioteca de suporte, [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)) é a classe principal para usar o hardware de verificação de impressão digital. Essa classe é um wrapper SDK do Android em volta do serviço de nível do sistema que gerencia as interações com o próprio hardware. Ele é responsável por iniciar o scanner de impressão digital e responder aos comentários do verificador. Essa classe tem uma interface razoavelmente simples com apenas três membros:

- **`Authenticate`** &ndash; esse método inicializará o scanner de hardware e iniciará o serviço em segundo plano, aguardando o usuário verificar sua impressão digital.
- **`EnrolledFingerprints`** &ndash; essa propriedade retornará `true` se o usuário tiver registrado uma ou mais impressões digitais com o dispositivo.
- **`HardwareDetected`** &ndash; essa propriedade é usada para determinar se o dispositivo dá suporte à verificação de impressão digital.

O método `FingerprintManager.Authenticate` é usado por um aplicativo Android para iniciar o scanner de impressão digital. O trecho a seguir é um exemplo de como chamá-lo usando as APIs de compatibilidade de biblioteca de suporte:

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

Este guia explicará como usar as APIs de `FingerprintManager` para aprimorar um aplicativo Android com autenticação de impressão digital. Ele abordará como instanciar e criar um `CryptoObject` para ajudar a proteger os resultados do scanner de impressão digital. Vamos examinar como um aplicativo deve ser subclasse `FingerprintManager.AuthenticationCallback` e responder a comentários do scanner de impressão digital. Por fim, veremos como registrar uma impressão digital em um dispositivo Android ou emulador e como usar o **ADB** para simular uma verificação de impressão digital.

## <a name="requirements"></a>Requisitos

A autenticação de impressão digital requer Android 6,0 (API nível 23) ou superior e um dispositivo com um scanner de impressão digital. 

Uma impressão digital já deve estar inscrita no dispositivo para cada usuário que deve ser autenticado. Isso envolve a configuração de um bloqueio de tela que usa uma senha, PIN, padrão de toque ou reconhecimento facial. É possível simular algumas das funcionalidades de autenticação de impressão digital em um Android Emulator.  Para obter mais informações sobre esses dois tópicos, consulte a seção [registrando uma impressão digital](enrolling-fingerprint.md) . 

## <a name="related-links"></a>Links relacionados

- [Aplicativo de exemplo de guia de impressão digital](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fingerprintguide)
- [Exemplo de caixa de diálogo de impressão digital](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)
- [Solicitando permissões em tempo de execução](https://developer.android.com/training/permissions/requesting.html)
- [Android. hardware. impressão digital](https://developer.android.com/reference/android/hardware/fingerprint/package-summary.html)
- [Android. support. v4. hardware. Fingerprint](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/package-summary.html)
- [Android. Content. Context](xref:Android.Content.Context)
- [API de impressão digital e de pagamentos (vídeo)](https://youtu.be/VOn7VrTRlA4)
