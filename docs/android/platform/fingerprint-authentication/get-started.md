---
title: Introdução à autenticação por impressão digital
ms.prod: xamarin
ms.assetid: 7BACCB36-8E3E-4E5D-B8EF-56A639839FD2
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/17/2018
ms.openlocfilehash: 3082dfcd6d0ffbc6404a89a10819e60b57b9c61c
ms.sourcegitcommit: 650458de1d362cd7de174cacef7838f0e74426f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/15/2019
ms.locfileid: "58070703"
---
# <a name="getting-started-with-fingerprint-authentication"></a>Introdução à autenticação por impressão digital

Para começar, primeiro, vamos abordar como configurar um projeto xamarin. Android para que o aplicativo seja capaz de usar a autenticação por impressão digital:

1. Atualização **androidmanifest. XML** para declarar as permissões que exigem as APIs de impressão digital.
2. Obtenha uma referência para o `FingerprintManager`.
3. Verifique se o dispositivo é capaz de verificação de impressão digital.

## <a name="requesting-permissions-in-the-application-manifest"></a>Solicitação de permissões no aplicativo do manifesto

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Um aplicativo do Android deve solicitar o `USE_FINGERPRINT` permissão no manifesto. Captura de tela a seguir mostra como adicionar essa permissão ao aplicativo no Visual Studio:

[![Habilitar uso\_impressão digital na tela de manifesto do Android](get-started-images/fingerprint-01-vs.png)](get-started-images/fingerprint-01-vs.png#lightbox) 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Um aplicativo do Android deve solicitar o `USE_FINGERPRINT` permissão no manifesto. Captura de tela a seguir mostra como adicionar essa permissão ao aplicativo no Visual Studio para Mac:

[![Habilitando UseFingerprint na tela de aplicativo do Android](get-started-images/fingerprint-01-xs.png)](get-started-images/fingerprint-01-xs.png#lightbox) 

-----

## <a name="getting-an-instance-of-the-fingerprintmanager"></a>Introdução a uma instância da FingerprintManager

Em seguida, o aplicativo deve obter uma instância de `FingerprintManager` ou o `FingerprintManagerCompat` classe. Para ser compatível com versões mais antigas do Android, um aplicativo do Android deve usar a API de compatibilidade encontradas no pacote de NuGet de suporte do Android v4. O trecho a seguir demonstra como obter o objeto apropriado do sistema operacional: 

```csharp
// Using the Android Support Library v4
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);

// Using API level 23:
FingerprintManager fingerprintManager = context.GetSystemService(Context.FingerprintService) as FingerprintManager;
```  

No trecho anterior, o `context` é qualquer Android `Android.Content.Context`. Normalmente, essa é a atividade que está realizando a autenticação.

## <a name="checking-for-eligibility"></a>Verificar qualificação

Um aplicativo deve executar diversas verificações para garantir que seja possível usar a autenticação por impressão digital. No total, há cinco condições que o aplicativo usa para verificar a qualificação:  

**API nível 23** &ndash; as APIs de impressão digital exigem o nível de API 23 ou superior. O `FingerprintManagerCompat` classe irá encapsular a verificação de nível de API para você. Por esse motivo, é recomendável usar o **biblioteca de suporte ao Android v4** e `FingerprintManagerCompat`; isso irá considerar para a uma dessas verificações.

**Hardware** &ndash; quando o aplicativo é iniciado pela primeira vez, ele deve verificar a presença de um scanner de impressão digital:

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.IsHardwareDetected)
{
    // Code omitted
}
```

**Dispositivo esteja protegido** &ndash; o usuário deve ter um dispositivo protegido com um bloqueio de tela. Se o usuário não tiver protegido o dispositivo com um bloqueio de tela e a segurança é importante para o aplicativo, em seguida, o usuário será notificado que um bloqueio de tela deve ser configurado. O trecho de código a seguir mostra como verificar esse pré-requisito:

```csharp
KeyguardManager keyguardManager = (KeyguardManager) GetSystemService(KeyguardService);
if (!keyguardManager.IsKeyguardSecure)
{
}
```

**Registrados impressões digitais** &ndash; o usuário deve ter pelo menos uma impressão digital registrada com o sistema operacional. Essa verificação de permissão deve ocorrer antes de cada tentativa de autenticação:

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.HasEnrolledFingerprints)
{
    // Can't use fingerprint authentication - notify the user that they need to
    // enroll at least one fingerprint with the device.
}
```

**As permissões** &ndash; o aplicativo deve solicitar permissão do usuário antes de usar o aplicativo. Para o Android 5.0 e inferior, o usuário concede a permissão como uma condição de instalar o aplicativo. Android 6.0 introduziu um novo modelo de permissão que verifica as permissões em tempo de execução. Este trecho de código é um exemplo de como verificar permissões no Android 6.0:

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

Verificando todas essas condições de cada vez, o aplicativo oferece opções de autenticação garantirá que o usuário obtém a melhor experiência de usuário. As alterações e atualizações ao dispositivo ou sistema operacional podem afetar a disponibilidade de autenticação por impressão digital. Se você optar por armazenar em cache os resultados de qualquer uma dessas verificações, certifique-se ater-se aos cenários de atualização.

Para obter mais informações sobre como solicitar permissões no Android 6.0, consulte o guia do Android [solicitando permissões em tempo de execução](https://developer.android.com/training/permissions/requesting.html).

## <a name="related-links"></a>Links relacionados

- [Contexto](https://developer.xamarin.com/api/type/Android.Content.Context/)
- [ContextCompat](https://developer.xamarin.com/api/type/Android.Support.V4.Content.ContextCompat/)
- [KeyguardManager](https://developer.xamarin.com/api/type/Android.App.KeyguardManager/)
- [FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
- [Solicitando permissões em tempo de execução](https://developer.android.com/training/permissions/requesting.html)
