---
title: Autenticação de impressão digital
description: 'Este guia aborda como adicionar autenticação por impressão digital, introduzida no Android 6.0, para um aplicativo xamarin. Android.'
ms.prod: xamarin
ms.assetid: 6742D874-4988-4516-A946-D5C714B20A10
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
---

# <a name="fingerprint-authentication"></a>Autenticação de impressão digital

_Este guia aborda como adicionar autenticação por impressão digital, introduzida no Android 6.0, para um aplicativo xamarin. Android._


## <a name="fingerprint-authentication-overview"></a>Visão geral de autenticação por impressão digital

A chegada de scanners de impressões digitais em dispositivos Android fornece aplicativos com uma alternativa para o método tradicional de nome de usuário e senha de autenticação do usuário. O uso de impressões digitais para autenticar um usuário torna possível para um aplicativo de incorporar a segurança é menos intrusiva que um nome de usuário e senha.

As APIs FingerprintManager dispositivos de destino com um scanner de impressão digital e estiver executando a API nível 23 (Android 6.0) ou superior. As APIs são encontradas no `Android.Hardware.Fingerprints` namespace. A biblioteca de suporte ao Android v4 fornece versões das APIs destinadas a versões mais antigas do Android impressões digitais. A compatibilidade APIs são encontradas na `Android.Support.v4.Hardware.Fingerprint` namespace, são distribuídos por meio de [pacote do NuGet Xamarin.Android.Support.v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/).

O [FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html) (e sua contraparte de biblioteca de suporte, [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)) é a classe principal para usar a impressão digital da verificação de hardware. Essa classe é um wrapper de SDK do Android em torno do serviço de nível de sistema que gerencia as interações com o próprio hardware. Ele é responsável por iniciar o scanner de impressão digital e responder aos comentários do scanner. Essa classe tem uma interface bem simples com apenas três membros:

* **`Authenticate`** &ndash; Esse método irá inicializar o scanner de hardware e iniciar o serviço em segundo plano, aguardando o usuário digitalizar sua impressão digital.
* **`EnrolledFingerprints`** &ndash; Essa propriedade retornará `true` se o usuário tiver registrado impressões digitais de uma ou mais com o dispositivo.
* **`HardwareDetected`** &ndash; Essa propriedade é usada para determinar se o dispositivo dá suporte à verificação de impressão digital.

O `FingerprintManager.Authenticate` método é usado por um aplicativo Android para iniciar o scanner de impressão digital. O trecho a seguir está um exemplo de como invocá-lo usando a APIs de compatibilidade da biblioteca de suporte:

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

Este guia discute como usar o `FingerprintManager` APIs para aprimorar um aplicativo Android com autenticação por impressão digital. Ele aborda como criar uma instância e criar um `CryptoObject` para ajudar a proteger os resultados usando o scanner de impressão digital. Vamos examinar como um aplicativo deve subclasse `FingerprintManager.AuthenticationCallback` e responder aos comentários usando o scanner de impressão digital. Por fim, veremos como registrar uma impressão digital em um emulador ou dispositivo Android e como usar **adb** para simular uma verificação de impressão digital.

## <a name="requirements"></a>Requisitos

Autenticação por impressão digital requer o Android 6.0 (API nível 23) ou superior e um dispositivo com o scanner de impressão digital. 

Uma impressão digital já deve ser registrada com o dispositivo para cada usuário que deve ser autenticado. Isso envolve configurar um bloqueio de tela que usa uma senha, PIN, padrão de passar o dedo ou reconhecimento facial. É possível simular algumas das funcionalidades de autenticação por impressão digital no emulador do Android.  Para obter mais informações sobre esses dois tópicos, consulte o [registrar uma impressão digital](enrolling-fingerprint.md) seção. 






## <a name="related-links"></a>Links relacionados

- [Aplicativo de exemplo do guia de impressão digital](https://developer.xamarin.com/samples/monodroid/FingerprintGuide/)
- [Exemplo de caixa de diálogo de impressão digital](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog/)
- [Solicitando permissões em tempo de execução](https://developer.android.com/training/permissions/requesting.html)
- [android.hardware.fingerprint](https://developer.android.com/reference/android/hardware/fingerprint/package-summary.html)
- [android.support.v4.hardware.fingerprint](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/package-summary.html)
- [Android.Content.Context](https://developer.xamarin.com/api/type/Android.Content.Context/)
- [API de impressão digital e pagamentos (vídeo)](https://youtu.be/VOn7VrTRlA4)
