---
title: Digitalização de impressões digitais
ms.prod: xamarin
ms.assetid: 1CDDC096-77E0-47B3-BE0B-8953E2DDACD3
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/23/2016
ms.openlocfilehash: 61edd0e4b532f18a8fc28502e5bb990703068776
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027488"
---
# <a name="scanning-for-fingerprints"></a>Digitalização de impressões digitais

Agora que vimos como preparar um aplicativo Xamarin.Android para usar autenticação `FingerprintManager.Authenticate` de impressão digital, vamos voltar ao método, e discutir seu lugar na autenticação de impressão digital do Android 6.0. Uma visão geral rápida do fluxo de trabalho para autenticação de impressões digitais é descrita nesta lista:

1. `FingerprintManager.Authenticate`Invoce, `CryptoObject` passando `FingerprintManager.AuthenticationCallback` um e um exemplo. O `CryptoObject` é usado para garantir que o resultado da autenticação da impressão digital não tenha sido adulterado. 
2. Subclasse a [classe FingerprintManager.AuthenticationCallback.](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html) Um exemplo desta classe será `FingerprintManager` fornecido para quando a autenticação de impressão digital for iniciada. Quando o scanner de impressões digitais estiver concluído, ele invocará um dos métodos de retorno de chamada nesta classe.
3. Escreva código para atualizar a ui para que o usuário saiba que o dispositivo iniciou o scanner de impressões digitais e está esperando a interação do usuário. 
4. Quando o scanner de impressões digitais estiver pronto, o Android `FingerprintManager.AuthenticationCallback` retornará os resultados ao aplicativo invocando um método na instância que foi fornecido na etapa anterior.
5. O aplicativo informará o usuário sobre os resultados da autenticação das impressões digitais e reagirá aos resultados conforme apropriado. 

O seguinte trecho de código é um exemplo de um método em uma Atividade que começará a procurar impressões digitais:

```csharp
protected void FingerPrintAuthenticationExample()
{
    const int flags = 0; /* always zero (0) */

    // CryptoObjectHelper is described in the previous section.
    CryptoObjectHelper cryptoHelper = new CryptoObjectHelper();    
    
    // cancellationSignal can be used to manually stop the fingerprint scanner. 
    cancellationSignal = new Android.Support.V4.OS.CancellationSignal();
    
    FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(this);
    
    // AuthenticationCallback is a base class that will be covered later on in this guide.
    FingerprintManagerCompat.AuthenticationCallback authenticationCallback = new MyAuthCallbackSample(this);

    // Start the fingerprint scanner.
    fingerprintManager.Authenticate(cryptoHelper.BuildCryptoObject(), flags, cancellationSignal, authenticationCallback, null);
}
```

Vamos discutir cada um desses `Authenticate` parâmetros no método com um pouco mais de detalhes:

- O primeiro parâmetro é um objeto _cripto_ que o scanner de impressões digitais usará para ajudar a autenticar os resultados de uma digitalização de impressões digitais. Este objeto `null`pode ser , nesse caso o aplicativo tem que confiar cegamente que nada alterou os resultados da impressão digital. Recomenda-se que `CryptoObject` seja instanciado e fornecido `FingerprintManager` ao invés de nulo. [A criação de um CryptObject](~/android/platform/fingerprint-authentication/creating-a-cryptoobject.md) explicará `CryptoObject` detalhadamente `Cipher`como instanciar um baseado em um .
- O segundo parâmetro é sempre zero. A documentação do Android identifica isso como conjunto de sinalizadores e é provavelmente reservado para uso futuro. 
- O terceiro parâmetro `cancellationSignal` é um objeto usado para desligar o scanner de impressões digitais e cancelar a solicitação atual. Este é um [Android CancelASignal](https://developer.android.com/reference/android/os/CancellationSignal.html), e não um tipo da estrutura .NET.
- O quarto parâmetro é obrigatório e é uma `AuthenticationCallback` classe que subclasse a classe abstrata. Os métodos desta classe serão invocados para `FingerprintManager` sinalizar aos clientes quando o tiver terminado e quais são os resultados. Como há muito a entender sobre `AuthenticationCallback`a implementação do , ele será coberto em [sua própria seção](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md).
- O quinto parâmetro é `Handler` uma instância opcional. Se `Handler` um objeto for `FingerprintManager` fornecido, `Looper` o objeto usará o a partir desse objeto ao processar as mensagens do hardware de impressão digital. Normalmente, não é necessário `Handler`fornecer um , o `Looper` Gerenciador de impressões digitais usará o do aplicativo.

## <a name="cancelling-a-fingerprint-scan"></a>Cancelando uma varredura de impressões digitais

Pode ser necessário que o usuário (ou o aplicativo) cancele a digitalização das impressões digitais após sua iniciação. Nesta situação, [`IsCancelled`](https://developer.android.com/reference/android/os/CancellationSignal.html#isCanceled()) invoque [`CancellationSignal`](https://developer.android.com/reference/android/os/CancellationSignal.html) o método `FingerprintManager.Authenticate` no que foi fornecido para quando foi invocado para iniciar a digitalização das impressões digitais.

Agora que vimos `Authenticate` o método, vamos examinar alguns dos parâmetros mais importantes com mais detalhes. Primeiro, vamos olhar [para responder aos callbacks de autenticação,](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md)que discutirão como subclassificar o [FingerprintManager.AuthenticationCallback,](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)permitindo que um aplicativo Android reaja aos resultados fornecidos pelo scanner de impressões digitais.

## <a name="related-links"></a>Links relacionados

- [Sinal de cancelamento](https://developer.android.com/reference/android/os/CancellationSignal.html)
- [DigitalManager.AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [Digitalmanager.CryptoObject](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [Gerenciador de impressões digitais](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
