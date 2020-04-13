---
title: Responder a chamadas de autenticação
ms.prod: xamarin
ms.assetid: 6533AFC9-1A1C-4897-A154-4D4ECFE27761
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/06/2017
ms.openlocfilehash: 8dc06740355bd95828e1a1bd8d9d15a2ef37e6b2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027526"
---
# <a name="responding-to-authentication-callbacks"></a>Responder a chamadas de autenticação

O scanner de impressões digitais é executado em segundo plano em seu próprio segmento, e `FingerprintManager.AuthenticationCallback` quando ele é concluído, ele relatará os resultados da varredura invocando um método do segmento de UI. Um aplicativo Android deve fornecer seu próprio manipulador que estende esta classe abstrata, implementando todos os seguintes métodos:

- **`OnAuthenticationError(int errorCode, ICharSequence errString)`**&ndash; Chamado quando há um erro irrecuperável. Não há nada mais que um aplicativo ou usuário possa fazer para corrigir a situação, exceto possivelmente tentar novamente.
- **`OnAuthenticationFailed()`**&ndash; Este método é invocado quando uma impressão digital foi detectada, mas não reconhecida pelo dispositivo.
- **`OnAuthenticationHelp(int helpMsgId, ICharSequence helpString)`**&ndash; Chamado quando há um erro recuperável, como o dedo sendo deslizado para jejum sobre o scanner.
- **`OnAuthenticationSucceeded(FingerprintManagerCompati.AuthenticationResult result)`**&ndash; Isso é chamado quando uma impressão digital é reconhecida.

Se `CryptoObject` um foi `Authenticate`usado ao ligar, `Cipher.DoFinal` é `OnAuthenticationSuccessful`recomendável ligar.
`DoFinal`abrirá uma exceção se a cifra foi adulterada ou indevidamente inicializada, indicando que o resultado do scanner de impressões digitais pode ter sido adulterado fora do aplicativo.

> [!NOTE]
> Recomenda-se manter a classe de retorno de chamada relativamente leve e livre de lógica específica do aplicativo. Os retornos de chamada devem atuar como um "policial de trânsito" entre o aplicativo Android e os resultados do scanner de impressões digitais.

## <a name="a-sample-authentication-callback-handler"></a>Um manipulador de chamada de autenticação de amostra

A classe a seguir é `FingerprintManager.AuthenticationCallback` um exemplo de uma implementação mínima: 

```csharp
class MyAuthCallbackSample : FingerprintManagerCompat.AuthenticationCallback
{
    // Can be any byte array, keep unique to application.
    static readonly byte[] SECRET_BYTES = {1, 2, 3, 4, 5, 6, 7, 8, 9};
    // The TAG can be any string, this one is for demonstration.
    static readonly string TAG = "X:" + typeof (SimpleAuthCallbacks).Name;

    public MyAuthCallbackSample()
    {
    }

    public override void OnAuthenticationSucceeded(FingerprintManagerCompat.AuthenticationResult result)
    {
        if (result.CryptoObject.Cipher != null) 
        {
            try
            {
                // Calling DoFinal on the Cipher ensures that the encryption worked.
                byte[] doFinalResult = result.CryptoObject.Cipher.DoFinal(SECRET_BYTES);
    
                // No errors occurred, trust the results.              
            }
            catch (BadPaddingException bpe)
            {
                // Can't really trust the results.
                Log.Error(TAG, "Failed to encrypt the data with the generated key." + bpe);
            }
            catch (IllegalBlockSizeException ibse)
            {
                // Can't really trust the results.
                Log.Error(TAG, "Failed to encrypt the data with the generated key." + ibse);
            }
        }
        else
        {
            // No cipher used, assume that everything went well and trust the results.
        }
    }

    public override void OnAuthenticationError(int errMsgId, ICharSequence errString)
    {
        // Report the error to the user. Note that if the user canceled the scan,
        // this method will be called and the errMsgId will be FingerprintState.ErrorCanceled.
    }

    public override void OnAuthenticationFailed()
    {
        // Tell the user that the fingerprint was not recognized.
    }

    public override void OnAuthenticationHelp(int helpMsgId, ICharSequence helpString)
    {
        // Notify the user that the scan failed and display the provided hint.
    }
}
```

`OnAuthenticationSucceeded`verifica para ver `Cipher` se um `FingerprintManager` `Authentication` foi fornecido para quando foi invocado. Se assim for, o `DoFinal` método é chamado na cifra. Isso fecha `Cipher`o , restaurando-o ao seu estado original. Se houve um problema com a `DoFinal` cifra, então lançará uma exceção e a tentativa de autenticação deve ser considerada como falhou.

Os `OnAuthenticationError` `OnAuthenticationHelp` e os retornos de chamada cada um recebem um inteiro indicando qual era o problema. A seção a seguir explica cada um dos possíveis códigos de ajuda ou erro. Os dois retornos de &ndash; chamada servem para fins semelhantes para informar o aplicativo de que a autenticação de impressão digital falhou. Como eles diferem é em gravidade. `OnAuthenticationHelp`é um erro recuperável do usuário, como deslizar a impressão digital muito rápido; `OnAuthenticationError` é mais um erro grave, como um scanner de impressões digitais danificado.

Observe `OnAuthenticationError` que será invocado quando a digitalização `CancellationSignal.Cancel()` de impressões digitais for cancelada através da mensagem. O `errMsgId` parâmetro terá o valor`FingerprintState.ErrorCanceled`de 5 ( ). Dependendo dos requisitos, uma `AuthenticationCallbacks` implementação pode tratar essa situação de forma diferente dos outros erros. 

`OnAuthenticationFailed`é invocado quando a impressão digital foi digitalizada com sucesso, mas não correspondia a nenhuma impressão digital registrada no dispositivo. 

## <a name="help-codes-and-error-message-ids"></a>Códigos de ajuda e ids de mensagem de erro 

Uma lista e descrição dos códigos de erro e códigos de ajuda podem ser encontrados na documentação do [Android SDK](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html#FINGERPRINT_ACQUIRED_GOOD) para a classe FingerprintManager. Xamarin.Android representa esses valores com o `Android.Hardware.Fingerprints.FingerprintState` enum:

- **`AcquiredGood`**&ndash; (valor 0) A imagem adquirida foi boa.

- **`AcquiredImagerDirty`**&ndash; (valor 3) A imagem da impressão digital era muito barulhento devido à suspeita ou desbloqueio de sujeira no sensor. Por exemplo, é razoável devolver isso `AcquiredInsufficient` após a detecção múltipla ou real de sujeira no sensor (pixels presos, faixas, etc.). Espera-se que o usuário tome medidas para limpar o sensor quando este for devolvido.

- **`AcquiredInsufficient`**&ndash; (valor 2) A imagem da impressão digital era muito barulhento para ser processada devido a uma condição `AcquiredImagerDirty`detectada (ou seja, pele seca) ou um sensor possivelmente sujo (Ver .

- **`AcquiredPartial`**&ndash; (valor 1) Apenas uma imagem parcial de impressão digital foi detectada. Durante a inscrição, o usuário deve ser informado sobre o que &ldquo;precisa acontecer para resolver esse problema, por exemplo, pressionar firmemente no sensor.&rdquo;

- **`AcquiredTooFast`**&ndash; (valor 5) A imagem da impressão digital estava incompleta devido ao movimento rápido. Embora principalmente apropriado para sensores de matriz linear, isso também poderia acontecer se o dedo fosse movido durante a aquisição. O usuário deve ser solicitado a mover o dedo mais lento (linear) ou deixar o dedo no sensor por mais tempo.

- **`AcquiredToSlow`**&ndash; (valor 4) A imagem da impressão digital era ilegível devido à falta de movimento. Isto é mais apropriado para sensores de matriz linear que requerem um movimento de deslizamento.

- **`ErrorCanceled`**&ndash; (valor 5) A operação foi cancelada porque o sensor de impressão digital não está disponível. Por exemplo, isso pode acontecer quando o usuário é comutado, o dispositivo está bloqueado ou outra operação pendente impede ou desativa.

- **`ErrorHwUnavailable`**&ndash; (valor 1) O hardware não está disponível. Tente novamente depois.

- **`ErrorLockout`**&ndash; (valor 7) A operação foi cancelada porque a API está bloqueada devido a muitas tentativas.

- **`ErrorNoSpace`**&ndash; (valor 4) Estado de erro devolvido para operações como inscrição; a operação não pode ser concluída porque não há armazenamento suficiente para concluir a operação.

- **`ErrorTimeout`**&ndash; (valor 3) Estado de erro retornado quando a solicitação atual estiver em execução há muito tempo. O objetivo é evitar que os programas esperem indefinidamente pelo sensor de impressões digitais. O tempo extra é específico da plataforma e do sensor, mas geralmente é de cerca de 30 segundos.

- **`ErrorUnableToProcess`**&ndash; (valor 2) O estado de erro retornou quando o sensor não pôde processar a imagem atual.

## <a name="related-links"></a>Links relacionados

- [Codificação](https://docs.oracle.com/javase/7/docs/api/javax/crypto/Cipher.html)
- [AutenticaçãoCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [AutenticaçãoCallback](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.AuthenticationCallback.html)
