---
title: Criando um CryptoObject
ms.prod: xamarin
ms.assetid: 4D159B80-FFF4-4136-A7F0-F8A5C6B86838
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 1658934bedce11a42701eb023a42fc9e617b654d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113762"
---
# <a name="creating-a-cryptoobject"></a>Criando um CryptoObject

A integridade dos resultados da autenticação por impressão digital é importante para um aplicativo &ndash; é como o aplicativo sabe a identidade do usuário. É teoricamente possível que o malware de terceiros interceptar e adulterar os resultados retornados pelo scanner de impressão digital. Esta seção abordará uma técnica para preservar a validade dos resultados de impressão digital. 

O `FingerprintManager.CryptoObject` é um wrapper em torno de APIs de criptografia do Java e é usado pelo `FingerprintManager` para proteger a integridade da solicitação de autenticação. Normalmente, um `Javax.Crypto.Cipher` objeto é o mecanismo para criptografar os resultados de scanner de impressão digital. O `Cipher` próprio objeto usará uma chave que é criada pelo aplicativo usando as APIs de armazenamento de chaves Android.

Para entender como todas essas classes funcionam juntos, vamos primeiro examinar o seguinte código que demonstra como criar um `CryptoObject`e, em seguida, explicarei em mais detalhes:

```csharp
public class CryptoObjectHelper
{
    // This can be key name you want. Should be unique for the app.
    static readonly string KEY_NAME = "com.xamarin.android.sample.fingerprint_authentication_key";

    // We always use this keystore on Android.
    static readonly string KEYSTORE_NAME = "AndroidKeyStore";

    // Should be no need to change these values.
    static readonly string KEY_ALGORITHM = KeyProperties.KeyAlgorithmAes;
    static readonly string BLOCK_MODE = KeyProperties.BlockModeCbc;
    static readonly string ENCRYPTION_PADDING = KeyProperties.EncryptionPaddingPkcs7;
    static readonly string TRANSFORMATION = KEY_ALGORITHM + "/" +
                                            BLOCK_MODE + "/" +
                                            ENCRYPTION_PADDING;
    readonly KeyStore _keystore;

    public CryptoObjectHelper()
    {
        _keystore = KeyStore.GetInstance(KEYSTORE_NAME);
        _keystore.Load(null);
    }

    public FingerprintManagerCompat.CryptoObject BuildCryptoObject()
    {
        Cipher cipher = CreateCipher();
        return new FingerprintManagerCompat.CryptoObject(cipher);
    }

    Cipher CreateCipher(bool retry = true)
    {
        IKey key = GetKey();
        Cipher cipher = Cipher.GetInstance(TRANSFORMATION);
        try
        {
            cipher.Init(CipherMode.EncryptMode | CipherMode.DecryptMode, key);
        } catch(KeyPermanentlyInvalidatedException e)
        {
            _keystore.DeleteEntry(KEY_NAME);
            if(retry)
            {
                CreateCipher(false);
            } else
            {
                throw new Exception("Could not create the cipher for fingerprint authentication.", e);
            }
        }
        return cipher;
    }

    IKey GetKey()
    {
        IKey secretKey;
        if(!_keystore.IsKeyEntry(KEY_NAME))
        {
            CreateKey();
        }

        secretKey = _keystore.GetKey(KEY_NAME, null);
        return secretKey;
    }

    void CreateKey()
    {
        KeyGenerator keyGen = KeyGenerator.GetInstance(KeyProperties.KeyAlgorithmAes, KEYSTORE_NAME);
        KeyGenParameterSpec keyGenSpec =
            new KeyGenParameterSpec.Builder(KEY_NAME, KeyStorePurpose.Encrypt | KeyStorePurpose.Decrypt)
                .SetBlockModes(BLOCK_MODE)
                .SetEncryptionPaddings(ENCRYPTION_PADDING)
                .SetUserAuthenticationRequired(true)
                .Build();
        keyGen.Init(keyGenSpec);
        keyGen.GenerateKey();
    }
}
```

O código de exemplo cria um novo `Cipher` para cada `CryptoObject`, usando uma chave que foi criada pelo aplicativo. A chave é identificada pela `KEY_NAME` variável que foi definida no início do `CryptoObjectHelper` classe. O método `GetKey` irá tentar e recuperar a chave usando as APIs do Android Keystore. Se a chave não existir, em seguida, o método `CreateKey` criará uma nova chave para o aplicativo.

A criptografia é instanciada com uma chamada para `Cipher.GetInstance`, sempre levando um _transformação_ (um valor de cadeia de caracteres que informa a codificação como criptografar e descriptografar dados). A chamada para `Cipher.Init` concluirá a inicialização da criptografia, fornecendo uma chave do aplicativo. 

É importante perceber que há algumas situações em que o Android pode invalidar a chave: 

* Uma nova impressão tenha sido registrada com o dispositivo.
* Não há nenhum impressões digitais registradas com o dispositivo.
* O usuário desabilitou o bloqueio de tela.
* O usuário alterou o bloqueio de tela (o tipo do screenlock ou o PIN/padrão usado).

Quando isso acontece, `Cipher.Init` lançará uma [ `KeyPermanentlyInvalidatedException` ](http://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html). O código de exemplo acima interceptar essa exceção, exclua a chave e, em seguida, crie um novo.

A próxima seção discute como criar a chave e armazená-lo no dispositivo.

## <a name="creating-a-secret-key"></a>Criando uma chave secreta

O `CryptoObjectHelper` classe usa o Android [ `KeyGenerator` ](https://developer.xamarin.com/api/type/Javax.Crypto.KeyGenerator/) para criar uma chave e armazená-lo no dispositivo. O `KeyGenerator` classe pode criar a chave, mas precisa alguns metadados sobre o tipo de chave a ser criada. Essas informações são fornecidas por uma instância de [ `KeyGenParameterSpec` ](http://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html) classe. 

Um `KeyGenerator` é instanciado usando o `GetInstance` método de fábrica. O código de exemplo usa o [ _Advanced Encryption Standard_ ](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) (_AES_) como o algoritmo de criptografia. AES será dividir os dados em blocos de tamanho fixo e criptografar cada um desses blocos.

Em seguida, uma `KeyGenParameterSpec` é criado usando o `KeyGenParameterSpec.Builder`. O `KeyGenParameterSpec.Builder` envolve as seguintes informações sobre a chave que deve ser criado:

* O nome da chave.
* A chave deve ser válida para criptografia e descriptografia.
* No código de exemplo a `BLOCK_MODE` é definido como _Cipher Block Chaining_ (`KeyProperties.BlockModeCbc`), que significa que cada bloco recebe XOR com o bloco anterior (Criando dependências entre cada bloco). 
* O `CryptoObjectHelper` usa [ _pública Key Cryptography Standard #7_ ](https://tools.ietf.org/html/rfc2315) (_PKCS7_) para gerar os bytes que preencherá os blocos para garantir que eles sejam todos do mesmo tamanho .
* `SetUserAuthenticationRequired(true)` significa que autenticação de usuário é necessária antes que a chave pode ser usada.

Uma vez a `KeyGenParameterSpec` é criado, ele é usado para inicializar o `KeyGenerator`, que gerará uma chave e armazene-o com segurança no dispositivo. 

## <a name="using-the-cryptoobjecthelper"></a>Usando o CryptoObjectHelper

Agora que o código de exemplo tem encapsulado grande parte da lógica para a criação de um `CryptoWrapper` para o `CryptoObjectHelper` classe, vamos rever o código desde o início deste guia e usar o `CryptoObjectHelper` para criar a codificação e iniciar um scanner de impressão digital: 

```csharp
protected void FingerPrintAuthenticationExample()
{
    const int flags = 0; /* always zero (0) */
    
    CryptoObjectHelper cryptoHelper = new CryptoObjectHelper();
    cancellationSignal = new Android.Support.V4.OS.CancellationSignal();
    
    // Using the Support Library classes for maximum reach
    FingerprintManagerCompat fingerPrintManager = FingerprintManagerCompat.From(this);
    
    // AuthCallbacks is a C# class defined elsewhere in code.
    FingerprintManagerCompat.AuthenticationCallback authenticationCallback = new MyAuthCallbackSample(this);

    // Here is where the CryptoObjectHelper builds the CryptoObject. 
    fingerprintManager.Authenticate(cryptohelper.BuildCryptoObject(), flags, cancellationSignal, authenticationCallback, null);
}
```

Agora que vimos como criar uma `CryptoObject`, permite a movimentação ver como o `FingerprintManager.AuthenticationCallbacks` são usados para transferir os resultados do serviço de scanner de impressão digital para um aplicativo do Android.



## <a name="related-links"></a>Links relacionados

- [Codificação](https://developer.xamarin.com/api/type/Javax.Crypto.Cipher/)
- [FingerprintManager.CryptoObject](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [KeyGenerator](https://developer.xamarin.com/api/type/Javax.Crypto.KeyGenerator/)
- [KeyGenParameterSpec](http://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html)
- [KeyGenParameterSpec.Builder](http://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.Builder.html)
- [KeyPermanentlyInvalidatedException](http://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html)
- [KeyProperties](http://developer.android.com/reference/android/security/keystore/KeyProperties.html)
- [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)
- [RFC 2315 - PCKS #7](https://tools.ietf.org/html/rfc2315)
