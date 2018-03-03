---
title: "Autenticação de impressão digital"
description: "Este guia descreve como adicionar autenticação de impressão digital, introduzida no Android 6.0, para um aplicativo xamarin."
ms.topic: article
ms.prod: xamarin
ms.assetid: 6742D874-4988-4516-A946-D5C714B20A10
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 79f5f81e11f62359c3b951500d4ab5cbd63fb507
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="fingerprint-authentication"></a>Autenticação de impressão digital

_Este guia descreve como adicionar autenticação de impressão digital, introduzida no Android 6.0, para um aplicativo xamarin._


## <a name="fingerprint-authentication-overview"></a>Visão geral de autenticação de impressão digital

A chegada de scanners de impressão digital em dispositivos Android fornece aplicativos com uma alternativa para o método tradicional de nome de usuário e senha de autenticação do usuário. O uso de impressões digitais para autenticar um usuário permite que um aplicativo incorporar a segurança é intrusiva menor que um nome de usuário e senha.

As APIs FingerprintManager dispositivos de destino com um scanner de impressão digital e estiver executando a API nível 23 (Android 6.0) ou superior. As APIs são encontradas no `Android.Hardware.Fingerprints` namespace. A biblioteca de suporte Android v4 fornece versões das APIs destinam-se para versões mais antigas do Android impressões digitais. A compatibilidade APIs são encontrados no `Android.Support.v4.Hardware.Fingerprint` namespace, são distribuídos por meio de [pacote NuGet Xamarin.Android.Support.v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/).

O [FingerprintManager](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html) (e sua contraparte da biblioteca de suporte [FingerprintManagerCompat](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)) é a classe principal para usar a impressão digital da verificação de hardware. Essa classe é um wrapper de SDK do Android em torno do serviço de nível de sistema que gerencia as interações com o hardware em si. Ele é responsável para iniciar o scanner de impressão digital e responder aos comentários do scanner. Essa classe tem uma interface bastante simples com apenas três membros:

* **`Authenticate`** &ndash; Esse método será inicializar o scanner de hardware e iniciar o serviço em segundo plano, aguardando o usuário verificar sua impressão digital.
* **`EnrolledFingerprints`** &ndash; Esta propriedade retornará `true` se o usuário tiver registrado impressões digitais de um ou mais com o dispositivo.
* **`HardwareDetected`** &ndash; Esta propriedade é usada para determinar se o dispositivo dá suporte à verificação de impressão digital.

O `FingerprintManager.Authenticate` método é usado por um aplicativo do Android para iniciar o verificador de impressão digital. O trecho a seguir é um exemplo de como invocá-lo usando a APIs de compatibilidade da biblioteca de suporte:

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

Este guia discutir como usar o `FingerprintManager` APIs para aprimorar a um aplicativo do Android com autenticação de impressões digitais. Ele aborda como criar uma instância e criar um `CryptoObject` para ajudar a proteger os resultados usando o scanner de impressão digital. Vamos examinar como um aplicativo deve subclasse `FingerprintManager.AuthenticationCallback` e responder aos comentários usando o scanner de impressão digital. Finalmente, veremos como registrar uma impressão digital em um dispositivo Android ou o emulador e como usar **adb** para simular uma verificação de impressão digital.

## <a name="requirements"></a>Requisitos

Requer a autenticação de impressões digitais Android 6.0 (API nível 23) ou superior e um dispositivo com um scanner de impressão digital. 

Uma impressão digital já deve estar registrada com o dispositivo para cada usuário que será autenticado. Isso envolve a configuração de um bloqueio de tela que usa uma senha, PIN, passe o dedo padrão ou reconhecimento facial. É possível simular algumas das funcionalidades de autenticação de impressão digital em um emulador Android.  Para obter mais informações sobre esses dois tópicos, consulte o [registrar uma impressão digital](enrolling-fingerprint.md) seção. 






## <a name="related-links"></a>Links relacionados

- [Aplicativo de exemplo do guia de impressão digital](https://developer.xamarin.com/samples/monodroid/FingerprintGuide/)
- [Exemplo de caixa de diálogo de impressão digital](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog/)
- [Solicitando permissões em tempo de execução](http://developer.android.com/training/permissions/requesting.html)
- [android.hardware.fingerprint](http://developer.android.com/reference/android/hardware/fingerprint/package-summary.html)
- [android.support.v4.hardware.fingerprint](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/package-summary.html)
- [Android.Content.Context](https://developer.xamarin.com/api/type/Android.Content.Context/)
- [API de impressão digital e pagamentos (vídeo)](https://youtu.be/VOn7VrTRlA4)
