---
title: "Introdução à autenticação de impressão digital"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7BACCB36-8E3E-4E5D-B8EF-56A639839FD2
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/08/2018
ms.openlocfilehash: e3c986b03408dae98a5a79f257029c10909aeabd
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="getting-started-with-fingerprint-authentication"></a>Introdução à autenticação de impressão digital

Para começar, primeiro, vamos abordar como configurar um projeto do xamarin para que o aplicativo é capaz de usar a autenticação de impressão digital:

1. Atualização **AndroidManifest.xml** para declarar as permissões que exigem as APIs de impressão digital.
2. Obter uma referência para o `FingerprintManager`.
3. Verifique se o dispositivo é capaz de verificação de impressão digital.

## <a name="requesting-permissions-in-the-application-manifest"></a>Solicitar permissões no aplicativo de manifesto

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Um aplicativo do Android deve solicitar o `USE_FINGERPRINT` permissão no manifesto. Captura de tela a seguir mostra como adicionar essa permissão ao aplicativo no Visual Studio 2015:

[![Habilitar uso\_impressão digital na tela de manifesto do Android](get-started-images/fingerprint-01-vs.png)](get-started-images/fingerprint-01-vs.png#lightbox) 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Um aplicativo do Android deve solicitar o `USE_FINGERPRINT` permissão no manifesto. Captura de tela a seguir mostra como adicionar essa permissão ao aplicativo no Visual Studio para Mac:

[![Habilitando UseFingerprint na tela do aplicativo do Android](get-started-images/fingerprint-01-xs.png)](get-started-images/fingerprint-01-xs.png#lightbox) 

-----

## <a name="getting-an-instance-of-the-fingerprintmanager"></a>Obtendo uma instância do FingerprintManager

Em seguida, o aplicativo deve obter uma instância do `FingerprintManager` ou `FingerprintManagerCompat` classe. Para ser compatível com versões anteriores do Android, um aplicativo do Android deve usar a API de compatibilidade do encontrado no pacote de NuGet de suporte do Android v4. O trecho a seguir demonstra como obter o objeto apropriado do sistema operacional: 

```csharp
// Using the Android Support Library v4
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);

// Using API level 23:
FingerprintManager fingerprintManager = context.GetSystemService(Context.FingerprintService) as FingerprintManager;
```  

No trecho anterior, o `context` é qualquer Android `Android.Content.Context`. Normalmente, essa é a atividade que está executando a autenticação.

## <a name="checking-for-eligibility"></a>Verificação de qualificação

Um aplicativo deve executar diversas verificações para garantir que é possível usar a autenticação de impressão digital. No total, há cinco condições que o aplicativo usa para verificar a elegibilidade:  
 

**API nível 23** &ndash; as APIs de impressão digital exigem o nível de API 23 ou superior. O `FingerprintManagerCompat` classe será ajustado a verificação de nível de API para você. Por esse motivo, é recomendável usar o **biblioteca de suporte Android v4** e `FingerprintManagerCompat`; Isso representará um dessas verificações.

**Hardware** &ndash; quando o aplicativo é iniciado pela primeira vez, ele deve verificar a presença de um scanner de impressão digital:

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.IsHardwareDetected)
{
    // Code omitted
}
```
    
**Dispositivo é protegido** &ndash; o usuário deve ter o dispositivo protegido com um bloqueio de tela. Se o usuário não tiver protegido o dispositivo com um bloqueio de tela e a segurança é importante para o aplicativo, em seguida, o usuário será notificado que um bloqueio de tela deve ser configurado. O trecho de código a seguir mostra como verificar este pre-requiste:

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

**Permissões** &ndash; o aplicativo deve solicitar permissão do usuário antes de usar o aplicativo. Para o Android 5.0 e inferior, o usuário concede a permissão como uma condição de instalar o aplicativo. Android 6.0 introduziu um novo modelo de permissão que verifica as permissões em tempo de execução. Este trecho de código é um exemplo de como verificar permissões Android 6.0:

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
    // http://developer.android.com/training/permissions/requesting.html
}
```

O aplicativo deve verificar condições 3, 4 e 5 sempre que desejar usar a autenticação de impressão digital. As primeiras duas condições podem ser verificadas na primeira vez que um aplicativo é executada em um dispositivo e os resultados salvos (em compartilhados preferências por exemplo).

Para obter mais informações sobre como solicitar permissões no Android 6.0, consulte o guia Android [solicitando permissões em tempo de execução](http://developer.android.com/training/permissions/requesting.html).



## <a name="related-links"></a>Links relacionados

- [Context](https://developer.xamarin.com/api/type/Android.Content.Context/)
- [ContextCompat](https://developer.xamarin.com/api/type/Android.Support.V4.Content.ContextCompat/)
- [KeyguardManager](https://developer.xamarin.com/api/type/Android.App.KeyguardManager/)
- [FingerprintManager](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
- [Solicitando permissões em tempo de execução](http://developer.android.com/training/permissions/requesting.html)
