---
title: Responder a chamadas de autenticação
ms.prod: xamarin
ms.assetid: 6533AFC9-1A1C-4897-A154-4D4ECFE27761
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/06/2017
ms.openlocfilehash: 17a1c94ad3b9bde67537ea7113352f0fc10d2a08
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110108"
---
# <a name="responding-to-authentication-callbacks"></a>Responder a chamadas de autenticação

O scanner de impressão digital é executado em segundo plano em seu próprio thread, e quando ele for concluído relatará os resultados da varredura, invocando um método de `FingerprintManager.AuthenticationCallback` no thread da interface do usuário. Um aplicativo do Android deve fornecer seu próprio manipulador que estende essa classe abstrata, implementar os seguintes métodos:

* **`OnAuthenticationError(int errorCode, ICharSequence errString)`** &ndash; Chamado quando ocorre um erro irrecuperável. Não há nada mais que um aplicativo ou usuário possa fazer para corrigir a situação, exceto que, possivelmente, tente novamente.
* **`OnAuthenticationFailed()`** &ndash; Esse método é invocado quando uma impressão digital foi detectada, mas não é reconhecida pelo dispositivo.
* **`OnAuthenticationHelp(int helpMsgId, ICharSequence helpString)`** &ndash; Chamado quando há um erro recuperável, como o dedo na tela para rápida sobre o verificador.
* **`OnAuthenticationSucceeded(FingerprintManagerCompati.AuthenticationResult result)`** &ndash; Isso é chamado quando uma impressão digital foi reconhecida.

Se um `CryptoObject` foi usado ao chamar `Authenticate`, é recomendável chamar `Cipher.DoFinal` em `OnAuthenticationSuccessful`.
`DoFinal` lançará uma exceção se a criptografia tiver sido violada ou inicializada de modo inadequado, indicando que o resultado do scanner de impressão digital pode ter sido adulterado com fora do aplicativo.


> [!NOTE]
> É recomendável manter o retorno de chamada classe relativamente leve e gratuita da lógica específica do aplicativo. Os retornos de chamada devem agir como um "cop de tráfego" entre o aplicativo Android e os resultados usando o scanner de impressão digital.

## <a name="a-sample-authentication-callback-handler"></a>Um manipulador de retorno de chamada de autenticação de exemplo

A classe a seguir está um exemplo de um mínimo `FingerprintManager.AuthenticationCallback` implementação: 

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

`OnAuthenticationSucceeded` verifica se um `Cipher` foi fornecido para `FingerprintManager` quando `Authentication` foi invocado. Nesse caso, o `DoFinal` método é chamado em criptografado. Isso fecha o `Cipher`, restaurá-lo ao seu estado original. Se houve um problema com a criptografia, em seguida, `DoFinal` lançará uma exceção e a tentativa de autenticação deve ser considerada com falha.

O `OnAuthenticationError` e `OnAuthenticationHelp` retornos de chamada cada recebem um inteiro que indica qual era o problema. A seção a seguir explica cada uma das possíveis ajuda ou códigos de erro. Dois retornos de chamada têm finalidades semelhantes &ndash; para informar o aplicativo que a autenticação por impressão digital falhou. Diferenças entre eles é gravidade. `OnAuthenticationHelp` é um erro recuperável de usuário, como o dedo para a impressão digital muito rápida; `OnAuthenticationError` é mais um erro grave, como um scanner de impressão digital danificado.

Observe que `OnAuthenticationError` será invocado quando a verificação de impressão digital é cancelada por meio de `CancellationSignal.Cancel()` mensagem. O `errMsgId` parâmetro terá o valor de 5 (`FingerprintState.ErrorCanceled`). Dependendo dos requisitos, uma implementação do `AuthenticationCallbacks` pode tratar essa situação de forma diferente do que os outros erros. 

`OnAuthenticationFailed` é invocado quando a impressão digital foi verificada com êxito, mas não correspondeu a nenhuma impressão digital registrado com o dispositivo. 

## <a name="help-codes-and-error-message-ids"></a>Ajuda de códigos e as Ids de mensagem de erro 

Uma lista e descrição dos códigos de erro e códigos de Ajuda podem ser encontrados na [documentação do SDK do Android](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html#FINGERPRINT_ACQUIRED_GOOD) para a classe FingerprintManager. Xamarin. Android representa esses valores com o `Android.Hardware.Fingerprints.FingerprintState` enum:


-   **`AcquiredGood`** &ndash; (valor 0) A imagem adquirida foi BOM.


-   **`AcquiredImagerDirty`** &ndash; (valor 3) A imagem de impressão digital foi muito ruidosa devido a sujeira suspeita ou detectada no sensor. Por exemplo, é razoável retornar isso após vários `AcquiredInsufficient` ou detecção real de sujeira no sensor do (pixels presos, espaços, etc.). O usuário deve tomar medidas para limpar o sensor de quando isso é retornado.


-   **`AcquiredInsufficient`** &ndash; (valor 2) A imagem de impressão digital foi muito ruidosa processar devido a uma condição detectada (ou seja, dry capa) ou um sensor possivelmente sujo (consulte `AcquiredImagerDirty`.



-   **`AcquiredPartial`** &ndash; (valor 1) Apenas uma imagem de impressão digital parcial foi detectada. Durante o registro, o usuário deve ser informado sobre o que precisa acontecer resolver esse problema, por exemplo, &ldquo;pressione firmemente no sensor.&rdquo;



-   **`AcquiredTooFast`** &ndash; (valor 5) A imagem de impressão digital estava incompleta devido ao movimento rápido. Embora principalmente apropriado para os sensores de matriz linear, isso também pode ocorrer se o dedo foi movido durante a aquisição. O usuário deve ser solicitado a mova o dedo mais lento (linear) ou deixar o dedo no sensor de mais tempo.




-   **`AcquiredToSlow`** &ndash; (valor de 4) A imagem de impressão digital foi ilegível devido à falta de movimento. Isso é mais apropriado para os sensores de matriz linear que exigem um movimento do dedo.



-   **`ErrorCanceled`** &ndash; (valor 5) A operação foi cancelada porque o sensor de impressão digital não está disponível. Por exemplo, isso pode acontecer quando o usuário é alternado, o dispositivo está bloqueado ou outra operação pendente impede ou desabilita a ele.



-   **`ErrorHwUnavailable`** &ndash; (valor 1) O hardware não está disponível. Tente novamente mais tarde.




-   **`ErrorLockout`** &ndash; (valor de 7) A operação foi cancelada porque a API está bloqueada devido a muitas tentativas.




-   **`ErrorNoSpace`** &ndash; (valor de 4) Estado de erro retornado para operações como registro; a operação não pode ser concluída porque não há armazenamento suficiente restante para concluir a operação.



-   **`ErrorTimeout`** &ndash; (valor 3) Estado de erro retornado quando a solicitação atual está em execução muito longa. Isso serve para impedir que programas esperar indefinidamente para o sensor de impressão digital. O tempo limite é a plataforma e específicos do sensor, mas geralmente é cerca de 30 segundos.



-   **`ErrorUnableToProcess`** &ndash; (valor 2) Estado de erro retornado quando o sensor não pôde processar a imagem atual.



## <a name="related-links"></a>Links relacionados

- [Codificação](https://docs.oracle.com/javase/7/docs/api/javax/crypto/Cipher.html)
- [AuthenticationCallback](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [AuthenticationCallback](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.AuthenticationCallback.html)
