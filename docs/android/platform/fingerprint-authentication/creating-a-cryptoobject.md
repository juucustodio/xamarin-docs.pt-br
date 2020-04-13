---
title: Criando um CryptoObject
ms.prod: xamarin
ms.assetid: 4D159B80-FFF4-4136-A7F0-F8A5C6B86838
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 871058d1c128b37a0f2e77b43587139efb433de1
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "75487770"
---
# <a name="creating-a-cryptoobject"></a>Criando um CryptoObject

A integridade dos resultados de autenticação de &ndash; impressão digital é importante para um aplicativo é como o aplicativo conhece a identidade do usuário. É teoricamente possível que malwares de terceiros interceptem e adulterem os resultados devolvidos pelo scanner de impressões digitais. Esta seção discutirá uma técnica para preservar a validade dos resultados das impressões digitais. 

O `FingerprintManager.CryptoObject` é um invólucro em torno das `FingerprintManager` APIs de criptografia Java e é usado para proteger a integridade da solicitação de autenticação. Normalmente, `Javax.Crypto.Cipher` um objeto é o mecanismo para criptografar os resultados do scanner de impressões digitais. O `Cipher` objeto em si usará uma chave criada pelo aplicativo usando as APIs do armazenamento de chaves do Android.

Para entender como todas essas classes funcionam juntas, vamos primeiro olhar para `CryptoObject`o seguinte código que demonstra como criar um , e depois explicar com mais detalhes:

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
            cipher.Init(CipherMode.EncryptMode, key);
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

O código de amostra `Cipher` criará um novo para cada um, `CryptoObject`usando uma chave que foi criada pelo aplicativo. A chave é `KEY_NAME` identificada pela variável que foi `CryptoObjectHelper` definida no início da aula. O `GetKey` método tentará recuperar a chave usando as APIs do Android Keystore. Se a chave não existir, `CreateKey` então o método criará uma nova chave para o aplicativo.

A cifra é instanciada com `Cipher.GetInstance`uma chamada para, tendo uma _transformação_ (um valor de cadeia que diz à cifra como criptografar e descriptografar dados). A chamada `Cipher.Init` para concluir a inicialização da cifra fornecendo uma chave do aplicativo. 

É importante perceber que existem algumas situações em que o Android pode invalidar a chave: 

- Uma nova impressão digital foi registrada no dispositivo.
- Não há impressões digitais inscritas no dispositivo.
- O usuário desativou o bloqueio da tela.
- O usuário alterou o bloqueio de tela (o tipo do bloqueio de tela ou o PIN/padrão usado).

Quando isso `Cipher.Init` acontecer, [`KeyPermanentlyInvalidatedException`](https://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html)vai jogar um . O código de amostra acima prenderá essa exceção, excluirá a chave e criará uma nova.

A próxima seção discutirá como criar a chave e armazená-la no dispositivo.

## <a name="creating-a-secret-key"></a>Criando uma chave secreta

A `CryptoObjectHelper` classe usa [`KeyGenerator`](xref:Javax.Crypto.KeyGenerator) o Android para criar uma chave e armazená-la no dispositivo. A `KeyGenerator` classe pode criar a chave, mas precisa de alguns meta-dados sobre o tipo de chave para criar. Essas informações são fornecidas por [`KeyGenParameterSpec`](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html) uma instância da classe. 

A `KeyGenerator` é instanciado `GetInstance` usando o método de fábrica. O código de exemplo usa o [_Advanced Encryption Standard_](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) _(AES)_ como algoritmo de criptografia. O AES dividirá os dados em blocos de um tamanho fixo e criptografará cada um desses blocos.

Em seguida, um `KeyGenParameterSpec` é `KeyGenParameterSpec.Builder`criado usando o . O `KeyGenParameterSpec.Builder` pacote envolve as seguintes informações sobre a chave a ser criada:

- O nome da chave.
- A chave deve ser válida tanto para criptografar quanto para descriptografar.
- No código de `BLOCK_MODE` amostra, o conjunto é`KeyProperties.BlockModeCbc`definido como _Cipher Block Chaining_ ( ), o que significa que cada bloco é XORed com o bloco anterior (criando dependências entre cada bloco). 
- O `CryptoObjectHelper` usa [_o Public Key Cryptography Standard #7_](https://tools.ietf.org/html/rfc2315) _(PKCS7)_ para gerar os bytes que vão bloquear os blocos para garantir que todos eles sejam do mesmo tamanho.
- `SetUserAuthenticationRequired(true)`significa que a autenticação do usuário é necessária antes que a chave possa ser usada.

Uma `KeyGenParameterSpec` vez criado, ele é usado `KeyGenerator`para inicializar o , que irá gerar uma chave e armazená-la com segurança no dispositivo. 

## <a name="using-the-cryptoobjecthelper"></a>Usando o CryptoObjectHelper

Agora que o código de amostra encapsulou grande parte da lógica para criar um `CryptoWrapper` na `CryptoObjectHelper` classe, vamos revisitar o código desde o início deste guia e usar o `CryptoObjectHelper` para criar a Cifra e iniciar um scanner de impressões digitais: 

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

Agora que vimos como criar `CryptoObject`um , vamos passar `FingerprintManager.AuthenticationCallbacks` para ver como os são usados para transferir os resultados do serviço de scanner de impressões digitais para um aplicativo Android.

## <a name="related-links"></a>Links relacionados

- [Codificação](xref:Javax.Crypto.Cipher)
- [Digitalmanager.CryptoObject](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [Keygenerator](xref:Javax.Crypto.KeyGenerator)
- [KeyGenParameterSpec](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html)
- [KeyGenParameterSpec.Builder](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.Builder.html)
- [ChaveDefinitivamenteInvalidaExceção](https://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html)
- [Propriedades-chave](https://developer.android.com/reference/android/security/keystore/KeyProperties.html)
- [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)
- [RFC 2315 - PCKS #7](https://tools.ietf.org/html/rfc2315)
