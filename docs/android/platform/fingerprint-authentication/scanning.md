---
title: Verificando impressões digitais
ms.prod: xamarin
ms.assetid: 1CDDC096-77E0-47B3-BE0B-8953E2DDACD3
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/23/2016
ms.openlocfilehash: 61edd0e4b532f18a8fc28502e5bb990703068776
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027488"
---
# <a name="scanning-for-fingerprints"></a>Verificando impressões digitais

Agora que vimos como preparar um aplicativo Xamarin. Android para usar a autenticação de impressão digital, vamos voltar ao método `FingerprintManager.Authenticate` e discutir seu lugar na autenticação de impressão digital do Android 6,0. Uma visão geral rápida do fluxo de trabalho para autenticação de impressão digital é descrita nesta lista:

1. Invocar `FingerprintManager.Authenticate`, passando uma `CryptoObject` e uma instância de `FingerprintManager.AuthenticationCallback`. O `CryptoObject` é usado para garantir que o resultado da autenticação de impressão digital não tenha sido violado. 
2. Subclasse a classe [fingerprintmanager. AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html) . Uma instância dessa classe será fornecida para `FingerprintManager` quando a autenticação de impressão digital for iniciada. Quando o scanner de impressão digital for concluído, ele invocará um dos métodos de retorno de chamada nessa classe.
3. Escreva o código para atualizar a interface do usuário para permitir que ele saiba que o dispositivo iniciou o scanner de impressão digital e está aguardando a interação do usuário. 
4. Quando o scanner de impressão digital for concluído, o Android retornará resultados para o aplicativo invocando um método na instância de `FingerprintManager.AuthenticationCallback` que foi fornecida na etapa anterior.
5. O aplicativo informará o usuário dos resultados da autenticação de impressão digital e reagirá aos resultados conforme apropriado. 

O trecho de código a seguir é um exemplo de um método em uma atividade que iniciará a verificação de impressões digitais:

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

Vamos discutir cada um desses parâmetros no método `Authenticate` com um pouco mais de detalhes:

- O primeiro parâmetro é um objeto de _criptografia_ que será usado pelo verificador de impressão digital para ajudar a autenticar os resultados de uma verificação de impressão digital. Esse objeto pode ser `null`e, nesse caso, o aplicativo precisa confiar indistintamente que nada foi adulterado com os resultados da impressão digital. É recomendável que um `CryptoObject` seja instanciado e fornecido para o `FingerprintManager` em vez de NULL. [Criar um criptobject](~/android/platform/fingerprint-authentication/creating-a-cryptoobject.md) explicará em detalhes como criar uma instância de um `CryptoObject` com base em um `Cipher`.
- O segundo parâmetro é sempre zero. A documentação do Android identifica isso como conjunto de sinalizadores e, provavelmente, é reservada para uso futuro. 
- O terceiro parâmetro, `cancellationSignal` é um objeto usado para desligar o scanner de impressão digital e cancelar a solicitação atual. Este é um [CancellationSignal do Android](https://developer.android.com/reference/android/os/CancellationSignal.html), e não um tipo do .NET Framework.
- O quarto parâmetro é obrigatório e é uma classe que subclasses a classe abstrata `AuthenticationCallback`. Os métodos nessa classe serão invocados para sinalizar para os clientes quando o `FingerprintManager` for concluído e quais são os resultados. Como há muito o que entender sobre a implementação do `AuthenticationCallback`, ele será abordado na [própria seção](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md).
- O quinto parâmetro é uma instância de `Handler` opcional. Se um objeto `Handler` for fornecido, o `FingerprintManager` usará a `Looper` desse objeto ao processar as mensagens do hardware de impressão digital. Normalmente, uma não precisa fornecer um `Handler`, o Fingerprintmanager usará o `Looper` do aplicativo.

## <a name="cancelling-a-fingerprint-scan"></a>Cancelando uma verificação de impressão digital

Pode ser necessário que o usuário (ou o aplicativo) cancele a verificação de impressão digital depois de ser iniciado. Nessa situação, invoque o método [`IsCancelled`](https://developer.android.com/reference/android/os/CancellationSignal.html#isCanceled()) no [`CancellationSignal`](https://developer.android.com/reference/android/os/CancellationSignal.html) fornecido para `FingerprintManager.Authenticate` quando ele foi chamado para iniciar a verificação de impressão digital.

Agora que vimos o método `Authenticate`, vamos examinar alguns dos parâmetros mais importantes com mais detalhes. Em primeiro lugar, veremos como [responder a retornos de chamada de autenticação](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md), que discutirão como criar uma subclasse do [fingerprintmanager. AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html), permitindo que um aplicativo Android reaja aos resultados fornecidos pelo scanner de impressão digital.

## <a name="related-links"></a>Links relacionados

- [CancellationSignal](https://developer.android.com/reference/android/os/CancellationSignal.html)
- [Impressão digitalmanager. AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [Fingerprintlist. Cryptoobject](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat. Cryptoobject](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [Impressão digital](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
