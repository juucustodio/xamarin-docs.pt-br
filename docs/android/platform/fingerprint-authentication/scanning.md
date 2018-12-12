---
title: Verificação de impressões digitais
ms.prod: xamarin
ms.assetid: 1CDDC096-77E0-47B3-BE0B-8953E2DDACD3
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/23/2016
ms.openlocfilehash: 843a3a8a347e3de48efbbbba3a1088c617e9e7cb
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898726"
---
# <a name="scanning-for-fingerprints"></a>Verificação de impressões digitais

Agora que vimos como preparar um aplicativo xamarin. Android para usar autenticação por impressão digital, vamos retornar para o `FingerprintManager.Authenticate` método e discutir seu lugar em que a autenticação de impressão digital do Android 6.0. Uma rápida visão geral do fluxo de trabalho para autenticação por impressão digital é descrita nesta lista:

1. Invocar `FingerprintManager.Authenticate`, passando uma `CryptoObject` e um `FingerprintManager.AuthenticationCallback` instância. O `CryptoObject` é usado para garantir que o resultado da autenticação por impressão digital não foi violado. 
2. Subclasse de [FingerprintManager.AuthenticationCallback](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html) classe. Uma instância dessa classe será fornecida para `FingerprintManager` quando de impressão digital é iniciado de autenticação. Quando terminar o scanner de impressão digital, ele invocará um dos métodos de retorno de chamada nessa classe.
3. Escreva código para atualizar a interface do usuário para permitir que o usuário sabe que o dispositivo foi iniciado o scanner de impressão digital e está aguardando a interação do usuário. 
4. Quando é feito o scanner de impressão digital, Android retornará resultados para o aplicativo invocando um método no `FingerprintManager.AuthenticationCallback` instância que foi fornecida na etapa anterior.
5. O aplicativo será informar ao usuário sobre os resultados da autenticação por impressão digital e reagir aos resultados conforme apropriado. 

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

Vamos falar sobre cada um desses parâmetros no `Authenticate` método um pouco mais detalhadamente:

* O primeiro parâmetro é um _crypto_ que use o scanner de impressão digital para ajudar a autenticar os resultados de uma verificação de impressão digital do objeto. Esse objeto pode ser `null`, caso em que o aplicativo precisa confiar cegamente que nada violados os resultados de impressão digital. É recomendável que um `CryptoObject` ser instanciado e fornecidos para o `FingerprintManager` em vez de null. [Criando um CryptObject](~/android/platform/fingerprint-authentication/creating-a-cryptoobject.md) explica detalhadamente como criar uma instância de um `CryptoObject` com base em um `Cipher`.
* O segundo parâmetro é sempre zero. Documentação do Android identifica como o conjunto de sinalizadores e é mais provável é reservada para uso futuro. 
* O terceiro parâmetro, `cancellationSignal` é um objeto usado para desativar o scanner de impressão digital e cancelar a solicitação atual. Esse é um [CancellationSignal Android](http://developer.android.com/reference/android/os/CancellationSignal.html)e não um tipo do .NET framework.
* O quarto parâmetro é obrigatório e é uma classe que pode efetuar subclasses de `AuthenticationCallback` classe abstrata. Métodos nessa classe serão invocados para sinalizar para clientes quando o `FingerprintManager` terminou e quais são os resultados. Pois não há muita coisa para entender sobre como implementar o `AuthenticationCallback`, serão abordado em [é a própria seção](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md).
* O quinto parâmetro é um recurso opcional `Handler` instância. Se um `Handler` objeto for fornecido, o `FingerprintManager` usará o `Looper` do objeto ao processar as mensagens do hardware de impressão digital. Normalmente, um não precisa fornecer um `Handler`, o FingerprintManager usará o `Looper` do aplicativo.

## <a name="cancelling-a-fingerprint-scan"></a>Cancelando uma verificação de impressão digital

Talvez seja necessário para o usuário (ou o aplicativo) cancelar a verificação de impressão digital, depois que ele foi iniciado. Nessa situação, invocar o [ `IsCancelled` ](http://developer.android.com/reference/android/os/CancellationSignal.html#isCanceled()) método sobre o [ `CancellationSignal` ](http://developer.android.com/reference/android/os/CancellationSignal.html) que foi fornecido para `FingerprintManager.Authenticate` quando ele foi chamado para iniciar a varredura de impressão digital.

Agora que temos visto o `Authenticate` método, vamos examinar alguns dos parâmetros mais importantes em mais detalhes. Primeiro, vamos examinar [responder a chamadas de autenticação](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md), que discutiremos como subclasse a [FingerprintManager.AuthenticationCallback](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html), permitindo que um aplicativo Android reagir à resultados fornecidos pelo scanner de impressão digital.




## <a name="related-links"></a>Links relacionados

- [CancellationSignal](http://developer.android.com/reference/android/os/CancellationSignal.html)
- [FingerprintManager.AuthenticationCallback](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [FingerprintManager.CryptoObject](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [FingerprintManager](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
