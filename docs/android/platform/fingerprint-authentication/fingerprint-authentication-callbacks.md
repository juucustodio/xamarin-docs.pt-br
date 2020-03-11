---
title: Responder a chamadas de autenticação
ms.prod: xamarin
ms.assetid: 6533AFC9-1A1C-4897-A154-4D4ECFE27761
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/06/2017
ms.openlocfilehash: 8dc06740355bd95828e1a1bd8d9d15a2ef37e6b2
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "73027526"
---
# <a name="responding-to-authentication-callbacks"></a>Responder a chamadas de autenticação

O scanner de impressão digital é executado em segundo plano em seu próprio thread e, quando concluído, ele relatará os resultados da verificação invocando um método de `FingerprintManager.AuthenticationCallback` no thread da interface do usuário. Um aplicativo Android deve fornecer seu próprio manipulador que estende essa classe abstrata, implementando todos os seguintes métodos:

- **`OnAuthenticationError(int errorCode, ICharSequence errString)`** &ndash; chamado quando há um erro irrecuperável. Não há nada mais que um aplicativo ou usuário possa fazer para corrigir a situação, exceto possivelmente tente novamente.
- **`OnAuthenticationFailed()`** &ndash; esse método é invocado quando uma impressão digital é detectada, mas não é reconhecida pelo dispositivo.
- **`OnAuthenticationHelp(int helpMsgId, ICharSequence helpString)`** &ndash; chamado quando há um erro recuperável, como o dedo que está sendo transdedodo para o rápido do scanner.
- **`OnAuthenticationSucceeded(FingerprintManagerCompati.AuthenticationResult result)`** &ndash; isso é chamado quando uma impressão digital é reconhecida.

Se um `CryptoObject` foi usado ao chamar `Authenticate`, é recomendável chamar `Cipher.DoFinal` no `OnAuthenticationSuccessful`.
`DoFinal` lançará uma exceção se a codificação tiver sido violada ou inicializada incorretamente, indicando que o resultado do scanner de impressão digital pode ter sido adulterado fora do aplicativo.

> [!NOTE]
> É recomendável manter a classe de retorno de chamada relativamente leve e gratuita da lógica específica do aplicativo. Os retornos de chamada devem agir como um "Cop. de tráfego" entre o aplicativo Android e os resultados do scanner de impressão digital.

## <a name="a-sample-authentication-callback-handler"></a>Um manipulador de retorno de chamada de autenticação de exemplo

A classe a seguir é um exemplo de uma implementação de `FingerprintManager.AuthenticationCallback` mínima: 

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

`OnAuthenticationSucceeded` verifica se um `Cipher` foi fornecido para `FingerprintManager` quando `Authentication` foi invocado. Nesse caso, o método `DoFinal` é chamado na codificação. Isso fecha o `Cipher`, restaurando-o para seu estado original. Se houvesse um problema com a codificação, `DoFinal` gerará uma exceção e a tentativa de autenticação deverá ser considerada como falha.

Os retornos de chamada `OnAuthenticationError` e `OnAuthenticationHelp` recebem um inteiro que indica qual foi o problema. A seção a seguir explica cada um dos possíveis códigos de erro ou de ajuda. Os dois retornos de chamada têm finalidades semelhantes &ndash; para informar ao aplicativo que a autenticação de impressão digital falhou. A diferença entre elas é a gravidade. `OnAuthenticationHelp` é um erro recuperável do usuário, como passar a impressão digital muito rápido; `OnAuthenticationError` é um erro mais grave, como um scanner de impressão digital danificado.

Observe que `OnAuthenticationError` será invocado quando a verificação de impressão digital for cancelada por meio da mensagem de `CancellationSignal.Cancel()`. O parâmetro `errMsgId` terá o valor de 5 (`FingerprintState.ErrorCanceled`). Dependendo dos requisitos, uma implementação do `AuthenticationCallbacks` pode tratar essa situação de forma diferente dos outros erros. 

`OnAuthenticationFailed` é invocado quando a impressão digital foi verificada com êxito, mas não corresponde a nenhuma impressão digital registrada com o dispositivo. 

## <a name="help-codes-and-error-message-ids"></a>Códigos de ajuda e IDs de mensagem de erro 

Uma lista e descrição dos códigos de erro e códigos de ajuda podem ser encontrados na [documentação SDK do Android](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html#FINGERPRINT_ACQUIRED_GOOD) para a classe fingerprintmanager. O Xamarin. Android representa esses valores com o `Android.Hardware.Fingerprints.FingerprintState` enum:

- **`AcquiredGood`** &ndash; (valor 0) a imagem adquirida foi boa.

- **`AcquiredImagerDirty`** &ndash; (valor 3) a imagem de impressão digital estava muito ruidosa devido a uma sujeira suspeita ou detectada no sensor. Por exemplo, é razoável retorná-la após várias `AcquiredInsufficient` ou a detecção real de sujeira no sensor (pixels presos, faixas, etc.). Espera-se que o usuário execute uma ação para limpar o sensor quando ele é retornado.

- **`AcquiredInsufficient`** &ndash; (valor 2) a imagem de impressão digital estava muito ruidosa para ser processada devido a uma condição detectada (ou seja, uma capa seca) ou um sensor possivelmente sujo (consulte `AcquiredImagerDirty`.

- **`AcquiredPartial`** &ndash; (valor 1) apenas uma imagem de impressão digital parcial foi detectada. Durante o registro, o usuário deve ser informado sobre o que precisa ocorrer para resolver esse problema, por exemplo, &ldquo;Pressione firmemente o sensor.&rdquo;

- **`AcquiredTooFast`** &ndash; (valor 5) a imagem de impressão digital estava incompleta devido ao movimento rápido. Embora seja principalmente apropriado para sensores de matriz linear, isso também poderia acontecer se o dedo fosse movido durante a aquisição. O usuário deve ser solicitado a mover o dedo mais devagar (linear) ou deixar o dedo no sensor mais longo.

- **`AcquiredToSlow`** &ndash; (valor 4) a imagem de impressão digital ficou ilegível devido à falta de movimento. Isso é mais apropriado para sensores de matriz linear que exigem um movimento de dedo.

- **`ErrorCanceled`** &ndash; (valor 5) a operação foi cancelada porque o sensor de impressão digital não está disponível. Por exemplo, isso pode acontecer quando o usuário é alternado, o dispositivo está bloqueado ou outra operação pendente impede ou desabilita.

- **`ErrorHwUnavailable`** &ndash; (valor 1) o hardware está indisponível. Tente novamente depois.

- **`ErrorLockout`** &ndash; (valor 7) a operação foi cancelada porque a API está bloqueada devido a muitas tentativas.

- **`ErrorNoSpace`** &ndash; (valor 4) estado de erro retornado para operações como registro; a operação não pode ser concluída porque não há armazenamento suficiente restante para concluir a operação.

- **`ErrorTimeout`** &ndash; (valor 3) estado de erro retornado quando a solicitação atual está sendo executada por muito tempo. Isso destina-se a impedir que programas Aguardem o sensor de impressão digital indefinidamente. O tempo limite é específico da plataforma e do sensor, mas geralmente é de cerca de 30 segundos.

- **`ErrorUnableToProcess`** &ndash; (valor 2) estado de erro retornado quando o sensor não pôde processar a imagem atual.

## <a name="related-links"></a>Links relacionados

- [Codificação](https://docs.oracle.com/javase/7/docs/api/javax/crypto/Cipher.html)
- [AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [AuthenticationCallback](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.AuthenticationCallback.html)
