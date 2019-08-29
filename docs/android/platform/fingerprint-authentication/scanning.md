---
title: Verificando impressões digitais
ms.prod: xamarin
ms.assetid: 1CDDC096-77E0-47B3-BE0B-8953E2DDACD3
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/23/2016
ms.openlocfilehash: 15afd5b1812e0423097e889cd8c2558ca01a8074
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70119751"
---
# <a name="scanning-for-fingerprints"></a>Verificando impressões digitais

Agora que vimos como preparar um aplicativo Xamarin. Android para usar a autenticação de impressão digital, vamos voltar ao `FingerprintManager.Authenticate` método e discutir seu lugar na autenticação de impressão digital do Android 6,0. Uma visão geral rápida do fluxo de trabalho para autenticação de impressão digital é descrita nesta lista:

1. Invoke `FingerprintManager.Authenticate`, passando uma `CryptoObject` e uma `FingerprintManager.AuthenticationCallback` instância. O `CryptoObject` é usado para garantir que o resultado da autenticação de impressão digital não tenha sido violado. 
2. Subclasse a classe [fingerprintmanager. AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html) . Uma instância dessa classe será fornecida quando a `FingerprintManager` autenticação de impressão digital for iniciada. Quando o scanner de impressão digital for concluído, ele invocará um dos métodos de retorno de chamada nessa classe.
3. Escreva o código para atualizar a interface do usuário para permitir que ele saiba que o dispositivo iniciou o scanner de impressão digital e está aguardando a interação do usuário. 
4. Quando o scanner de impressão digital for concluído, o Android retornará resultados para o aplicativo invocando um método na `FingerprintManager.AuthenticationCallback` instância que foi fornecida na etapa anterior.
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

Vamos discutir cada um desses parâmetros no `Authenticate` método um pouco mais detalhadamente:

- O primeiro parâmetro é um objeto de _criptografia_ que será usado pelo verificador de impressão digital para ajudar a autenticar os resultados de uma verificação de impressão digital. Esse objeto pode ser `null`, nesse caso, o aplicativo precisa confiar cego que nada foi adulterado com os resultados da impressão digital. É recomendável que um `CryptoObject` seja instanciado e fornecido para o `FingerprintManager` em vez de NULL. [Criar um criptobject](~/android/platform/fingerprint-authentication/creating-a-cryptoobject.md) explicará em detalhes como instanciar um `CryptoObject` com base em `Cipher`um.
- O segundo parâmetro é sempre zero. A documentação do Android identifica isso como conjunto de sinalizadores e, provavelmente, é reservada para uso futuro. 
- O terceiro parâmetro, `cancellationSignal` é um objeto usado para desligar o scanner de impressão digital e cancelar a solicitação atual. Este é um [CancellationSignal do Android](https://developer.android.com/reference/android/os/CancellationSignal.html), e não um tipo do .NET Framework.
- O quarto parâmetro é obrigatório e é uma classe que faz a subclasse da `AuthenticationCallback` classe abstract. Os métodos nessa classe serão invocados para sinalizar para os clientes `FingerprintManager` quando o for concluído e quais são os resultados. Como há muito a entender sobre a implementação do `AuthenticationCallback`, ele será abordado na [própria seção](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md).
- O quinto parâmetro é uma instância `Handler` opcional. Se um `Handler` objeto for fornecido, o `FingerprintManager` usará o `Looper` desse objeto ao processar as mensagens do hardware de impressão digital. Normalmente, uma não precisa fornecer um `Handler`, o fingerprintmanager usará o `Looper` do aplicativo.

## <a name="cancelling-a-fingerprint-scan"></a>Cancelando uma verificação de impressão digital

Pode ser necessário que o usuário (ou o aplicativo) cancele a verificação de impressão digital depois de ser iniciado. Nessa situação, invoque o [`IsCancelled`](https://developer.android.com/reference/android/os/CancellationSignal.html#isCanceled()) método [`CancellationSignal`](https://developer.android.com/reference/android/os/CancellationSignal.html) no que foi fornecido para `FingerprintManager.Authenticate` quando foi chamado para iniciar a verificação de impressão digital.

Agora que já vimos o `Authenticate` método, vamos examinar alguns dos parâmetros mais importantes com mais detalhes. Em primeiro lugar, veremos como [responder a retornos de chamada de autenticação](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md), que discutirão como criar uma subclasse do fingerprintmanager [. AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html), permitindo que um aplicativo Android reaja aos resultados fornecidos pelo scanner de impressão digital.




## <a name="related-links"></a>Links relacionados

- [CancellationSignal](https://developer.android.com/reference/android/os/CancellationSignal.html)
- [FingerprintManager.AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [FingerprintManager.CryptoObject](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
