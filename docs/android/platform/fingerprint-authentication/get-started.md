---
title: Começando com autenticação de impressão digital
ms.prod: xamarin
ms.assetid: 7BACCB36-8E3E-4E5D-B8EF-56A639839FD2
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/17/2018
ms.openlocfilehash: 746a096f93036e63b29bc917826259f88426cead
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020280"
---
# <a name="getting-started-with-fingerprint-authentication"></a>Começando com autenticação de impressão digital

Para começar, vamos primeiro cobrir como configurar um projeto Xamarin.Android para que o aplicativo seja capaz de usar autenticação de impressão digital:

1. Atualize **o AndroidManifest.xml** para declarar as permissões que as APIs de impressão digital requerem.
2. Obter uma referência `FingerprintManager`ao .
3. Verifique se o dispositivo é capaz de digitalizar impressões digitais.

## <a name="requesting-permissions-in-the-application-manifest"></a>Solicitando permissões no Manifesto de Solicitação

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Um aplicativo Android `USE_FINGERPRINT` deve solicitar a permissão no manifesto. A captura de tela a seguir mostra como adicionar essa permissão ao aplicativo no Visual Studio:

[![Habilitação\_USE FINGERPRINT na tela do Manifesto do Android](get-started-images/fingerprint-01-vs.png)](get-started-images/fingerprint-01-vs.png#lightbox) 

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Um aplicativo Android `USE_FINGERPRINT` deve solicitar a permissão no manifesto. A captura de tela a seguir mostra como adicionar essa permissão ao aplicativo no Visual Studio para Mac:

[![Habilitação do UseFingerprint na tela do aplicativo para Android](get-started-images/fingerprint-01-xs.png)](get-started-images/fingerprint-01-xs.png#lightbox) 

-----

## <a name="getting-an-instance-of-the-fingerprintmanager"></a>Obtendo uma instância do Gerenciador de Impressões Digitais

Em seguida, o aplicativo deve `FingerprintManager` obter `FingerprintManagerCompat` uma instância da classe ou da classe. Para ser compatível com versões mais antigas do Android, um aplicativo Android deve usar a API de compatibilidade encontrada no pacote Android Support v4 NuGet. O trecho a seguir demonstra como obter o objeto apropriado do sistema operacional: 

```csharp
// Using the Android Support Library v4
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);

// Using API level 23:
FingerprintManager fingerprintManager = context.GetSystemService(Context.FingerprintService) as FingerprintManager;
```  

No trecho anterior, o `context` é `Android.Content.Context`qualquer Android . Normalmente esta é a Atividade que está realizando a autenticação.

## <a name="checking-for-eligibility"></a>Verificando a elegibilidade

Um aplicativo deve realizar várias verificações para garantir que seja possível usar autenticação de impressão digital. No total, existem cinco condições que o aplicativo usa para verificar a elegibilidade:  

**API nível 23** &ndash; As APIs de impressão digital requerem nível DePI 23 ou superior. A `FingerprintManagerCompat` classe encerrará a verificação de nível de API para você. Por essa razão, recomenda-se o uso `FingerprintManagerCompat`da Biblioteca de Suporte android **v4** e ; isso vai explicar o um desses cheques.

**Hardware** &ndash; Quando o aplicativo é iniciado pela primeira vez, ele deve verificar a presença de um scanner de impressões digitais:

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.IsHardwareDetected)
{
    // Code omitted
}
```

**Dispositivo está protegido** &ndash; O usuário deve ter o dispositivo protegido com um bloqueio de tela. Se o usuário não tiver protegido o dispositivo com um bloqueio de tela e a segurança for importante para o aplicativo, então o usuário deve ser notificado de que um bloqueio de tela deve ser configurado. O seguinte trecho de código mostra como verificar este pré-requiste:

```csharp
KeyguardManager keyguardManager = (KeyguardManager) GetSystemService(KeyguardService);
if (!keyguardManager.IsKeyguardSecure)
{
}
```

**Impressões digitais inscritas** &ndash; O usuário deve ter pelo menos uma impressão digital registrada no sistema operacional. Esta verificação de permissão deve ocorrer antes de cada tentativa de autenticação:

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.HasEnrolledFingerprints)
{
    // Can't use fingerprint authentication - notify the user that they need to
    // enroll at least one fingerprint with the device.
}
```

**Permissões** &ndash; O aplicativo deve solicitar permissão ao usuário antes de usar o aplicativo. Para android 5.0 e inferior, o usuário concede a permissão como condição para instalar o aplicativo. O Android 6.0 introduziu um novo modelo de permissão que verifica permissões em tempo de execução. Este trecho de código é um exemplo de como verificar permissões no Android 6.0:

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

Verificar todas essas condições cada vez que o aplicativo oferece opções de autenticação garantirá que o usuário obtenha a melhor experiência do usuário. Alterações ou upgrades em seu dispositivo ou sistema operacional podem afetar a disponibilidade de autenticação de impressão digital. Se você optar por armazenar os resultados de qualquer uma dessas verificações, certifique-se de atender a cenários de upgrade.

Para obter mais informações sobre como solicitar permissões no Android 6.0, consulte o guia do Android [solicitando permissões em Run-Time](https://developer.android.com/training/permissions/requesting.html).

## <a name="related-links"></a>Links relacionados

- [Contexto](xref:Android.Content.Context)
- [KeyguardManager](xref:Android.App.KeyguardManager)
- [ContextCompat](https://developer.android.com/reference/android/support/v4/content/ContextCompat)
- [Gerenciador de impressões digitais](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
- [Solicitando permissões em tempo de execução](https://developer.android.com/training/permissions/requesting.html)
