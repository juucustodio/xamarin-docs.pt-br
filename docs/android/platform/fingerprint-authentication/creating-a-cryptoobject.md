---
title: Criando um Cryptoobject
ms.prod: xamarin
ms.assetid: 4D159B80-FFF4-4136-A7F0-F8A5C6B86838
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 871058d1c128b37a0f2e77b43587139efb433de1
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487770"
---
# <a name="creating-a-cryptoobject"></a>Criando um Cryptoobject

A integridade dos resultados da autenticação de impressão digital é importante para um aplicativo &ndash; é como o aplicativo sabe a identidade do usuário. Teoricamente, é possível que o malware de terceiros intercepte e viole os resultados retornados pelo scanner de impressão digital. Esta seção discutirá uma técnica para preservar a validade dos resultados da impressão digital. 

O `FingerprintManager.CryptoObject` é um wrapper em volta das APIs de Java Cryptography e é usado pelo `FingerprintManager` para proteger a integridade da solicitação de autenticação. Normalmente, um objeto `Javax.Crypto.Cipher` é o mecanismo para criptografar os resultados do scanner de impressão digital. O objeto `Cipher` em si usará uma chave que é criada pelo aplicativo usando as APIs do repositório de chaves do Android.

Para entender como essas classes funcionam juntas, vamos primeiro examinar o código a seguir, que demonstra como criar um `CryptoObject`e, em seguida, explicar com mais detalhes:

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

O código de exemplo criará um novo `Cipher` para cada `CryptoObject`, usando uma chave que foi criada pelo aplicativo. A chave é identificada pela variável `KEY_NAME` que foi definida no início da classe `CryptoObjectHelper`. O método `GetKey` tentará recuperar a chave usando as APIs do repositório de chaves do Android. Se a chave não existir, o método `CreateKey` criará uma nova chave para o aplicativo.

A codificação é instanciada com uma chamada para `Cipher.GetInstance`, fazendo uma _transformação_ (um valor de cadeia de caracteres que informa ao Cipher como criptografar e descriptografar dados). A chamada para `Cipher.Init` concluirá a inicialização da codificação fornecendo uma chave do aplicativo. 

É importante perceber que há algumas situações em que o Android pode invalidar a chave: 

- Uma nova impressão digital foi registrada com o dispositivo.
- Não há impressões digitais registradas no dispositivo.
- O usuário desabilitou o bloqueio de tela.
- O usuário alterou o bloqueio de tela (o tipo de screenlock ou o PIN/padrão usado).

Quando isso acontecer, `Cipher.Init` gerará um [`KeyPermanentlyInvalidatedException`](https://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html). O código de exemplo acima interceptará essa exceção, excluirá a chave e, em seguida, criará uma nova.

A próxima seção explicará como criar a chave e armazená-la no dispositivo.

## <a name="creating-a-secret-key"></a>Criando uma chave secreta

A classe `CryptoObjectHelper` usa o [`KeyGenerator`](xref:Javax.Crypto.KeyGenerator) do Android para criar uma chave e armazená-la no dispositivo. A classe `KeyGenerator` pode criar a chave, mas precisa de alguns metadados sobre o tipo de chave a ser criado. Essas informações são fornecidas por uma instância da classe [`KeyGenParameterSpec`](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html) . 

Uma `KeyGenerator` é instanciada usando o método de fábrica de `GetInstance`. O código de exemplo usa o [_criptografia AES_](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) (_AES_) como o algoritmo de criptografia. O AES interromperá os dados em blocos de um tamanho fixo e criptografará cada um desses blocos.

Em seguida, um `KeyGenParameterSpec` é criado usando o `KeyGenParameterSpec.Builder`. O `KeyGenParameterSpec.Builder` encapsula as seguintes informações sobre a chave a ser criada:

- O nome da chave.
- A chave deve ser válida para criptografia e descriptografia.
- No código de exemplo, o `BLOCK_MODE` é definido como _encadeamento de bloco de codificação_ (`KeyProperties.BlockModeCbc`), o que significa que cada bloco é XORed com o bloco anterior (Criando dependências entre cada bloco). 
- O `CryptoObjectHelper` usa_PKCS7_( [_Public Key Cryptography Standard #7_](https://tools.ietf.org/html/rfc2315) ) para gerar os bytes que irão preencher os blocos para garantir que eles sejam do mesmo tamanho.
- `SetUserAuthenticationRequired(true)` significa que a autenticação do usuário é necessária antes que a chave possa ser usada.

Depois que o `KeyGenParameterSpec` é criado, ele é usado para inicializar o `KeyGenerator`, que irá gerar uma chave e armazená-la com segurança no dispositivo. 

## <a name="using-the-cryptoobjecthelper"></a>Usando o CryptoObjectHelper

Agora que o código de exemplo encapsulava grande parte da lógica para criar um `CryptoWrapper` na classe `CryptoObjectHelper`, Vamos revisitar o código do início deste guia e usar o `CryptoObjectHelper` para criar a codificação e iniciar um scanner de impressão digital: 

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

Agora que vimos como criar um `CryptoObject`, vamos continuar a ver como os `FingerprintManager.AuthenticationCallbacks` são usados para transferir os resultados do serviço de scanner de impressão digital para um aplicativo Android.

## <a name="related-links"></a>Links Relacionados

- [Codificação](xref:Javax.Crypto.Cipher)
- [FingerprintManager.CryptoObject](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [KeyGenerator](xref:Javax.Crypto.KeyGenerator)
- [KeyGenParameterSpec](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html)
- [KeyGenParameterSpec.Builder](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.Builder.html)
- [KeyPermanentlyInvalidatedException](https://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html)
- [KeyProperties](https://developer.android.com/reference/android/security/keystore/KeyProperties.html)
- [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)
- [RFC 2315-PCKS #7](https://tools.ietf.org/html/rfc2315)
