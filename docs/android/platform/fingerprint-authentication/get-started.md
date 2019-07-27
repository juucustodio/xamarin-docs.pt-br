---
title: Introdução com autenticação de impressão digital
ms.prod: xamarin
ms.assetid: 7BACCB36-8E3E-4E5D-B8EF-56A639839FD2
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/17/2018
ms.openlocfilehash: c433d4d7920b024795e2e8344b452e25d8f58cf4
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510641"
---
# <a name="getting-started-with-fingerprint-authentication"></a>Introdução com autenticação de impressão digital

Para começar, vamos primeiro abordar como configurar um projeto Xamarin. Android para que o aplicativo possa usar a autenticação de impressão digital:

1. Atualize **AndroidManifest. xml** para declarar as permissões que as APIs de impressão digital exigem.
2. Obtenha uma referência para o `FingerprintManager`.
3. Verifique se o dispositivo é capaz de fazer a verificação de impressão digital.

## <a name="requesting-permissions-in-the-application-manifest"></a>Solicitando permissões no manifesto do aplicativo

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Um aplicativo Android deve solicitar a `USE_FINGERPRINT` permissão no manifesto. A captura de tela a seguir mostra como adicionar essa permissão ao aplicativo no Visual Studio:

[![Habilitando\_o uso de impressão digital na tela de manifesto do Android](get-started-images/fingerprint-01-vs.png)](get-started-images/fingerprint-01-vs.png#lightbox) 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Um aplicativo Android deve solicitar a `USE_FINGERPRINT` permissão no manifesto. A captura de tela a seguir mostra como adicionar essa permissão ao aplicativo no Visual Studio para Mac:

[![Habilitando o UseFingerprint na tela do aplicativo Android](get-started-images/fingerprint-01-xs.png)](get-started-images/fingerprint-01-xs.png#lightbox) 

-----

## <a name="getting-an-instance-of-the-fingerprintmanager"></a>Obtendo uma instância do Fingerprintmanager

Em seguida, o aplicativo deve obter uma instância do `FingerprintManager` ou da `FingerprintManagerCompat` classe. Para ser compatível com versões mais antigas do Android, um aplicativo Android deve usar a API de compatibilidade encontrada no pacote NuGet do suporte do Android v4. O trecho a seguir demonstra como obter o objeto apropriado do sistema operacional: 

```csharp
// Using the Android Support Library v4
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);

// Using API level 23:
FingerprintManager fingerprintManager = context.GetSystemService(Context.FingerprintService) as FingerprintManager;
```  

No trecho anterior, o `context` é qualquer Android. `Android.Content.Context` Normalmente, essa é a atividade que está executando a autenticação.

## <a name="checking-for-eligibility"></a>Verificando a qualificação

Um aplicativo deve executar várias verificações para garantir que seja possível usar a autenticação de impressão digital. No total, há cinco condições que o aplicativo usa para verificar a elegibilidade:  

**Nível de API 23** &ndash; As APIs de impressão digital exigem o nível de API 23 ou superior. A `FingerprintManagerCompat` classe encapsulará a verificação de nível de API para você. Por esse motivo, é recomendável usar a **biblioteca de suporte do Android v4** e `FingerprintManagerCompat`; isso considerará uma dessas verificações.

**Hardware** do &ndash; Quando o aplicativo é iniciado pela primeira vez, ele deve verificar a presença de um scanner de impressão digital:

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.IsHardwareDetected)
{
    // Code omitted
}
```

O **dispositivo está protegido** &ndash; O usuário deve ter o dispositivo protegido com um bloqueio de tela. Se o usuário não tiver protegido o dispositivo com um bloqueio de tela e a segurança for importante para o aplicativo, o usuário deverá ser notificado de que um bloqueio de tela deve ser configurado. O trecho de código a seguir mostra como verificar este requiste:

```csharp
KeyguardManager keyguardManager = (KeyguardManager) GetSystemService(KeyguardService);
if (!keyguardManager.IsKeyguardSecure)
{
}
```

**Impressões digitais registradas** &ndash; O usuário deve ter pelo menos uma impressão digital registrada com o sistema operacional. Essa verificação de permissão deve ocorrer antes de cada tentativa de autenticação:

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.HasEnrolledFingerprints)
{
    // Can't use fingerprint authentication - notify the user that they need to
    // enroll at least one fingerprint with the device.
}
```

**Permissões** do &ndash; O aplicativo deve solicitar permissão do usuário antes de usar o aplicativo. Para Android 5,0 e inferior, o usuário concede a permissão como uma condição de instalação do aplicativo. O Android 6,0 introduziu um novo modelo de permissão que verifica as permissões em tempo de execução. Este trecho de código é um exemplo de como verificar permissões no Android 6,0:

```csharp
// The context is typically a reference to the current activity.
Android.Content.PM.Permission permissionResult = ContextCompat.CheckSelfPermission(context, Manifest.Permission.UseFingerprint);
if (permissionResult == Android.Content.PM.Permission.Granted)
{
    // Permission granted - go ahead and start the fingerprint scanner.
}
else
{
    // No permission. Go and ask for permissions and don't start the scanner. See
    // https://developer.android.com/training/permissions/requesting.html
}
```

A verificação de todas essas condições sempre que o aplicativo oferece opções de autenticação garantirá que o usuário obtenha a melhor experiência do usuário. Alterações ou atualizações em seu dispositivo ou sistema operacional podem afetar a disponibilidade da autenticação de impressão digital. Se você optar por armazenar em cache os resultados de qualquer uma dessas verificações, certifique-se de atender aos cenários de atualização.

Para obter mais informações sobre como solicitar permissões no Android 6,0, consulte o guia do Android [solicitando permissões em tempo de execução](https://developer.android.com/training/permissions/requesting.html).

## <a name="related-links"></a>Links relacionados

- [Noticioso](xref:Android.Content.Context)
- [KeyguardManager](xref:Android.App.KeyguardManager)
- [ContextCompat](https://developer.android.com/reference/android/support/v4/content/ContextCompat)
- [FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
- [Solicitando permissões em tempo de execução](https://developer.android.com/training/permissions/requesting.html)
