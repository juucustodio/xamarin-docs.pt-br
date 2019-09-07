---
title: Fontes
ms.prod: xamarin
ms.assetid: 3F543FC5-FDED-47F8-8D2C-481FCC98BFDA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/09/2018
ms.openlocfilehash: 4d91ba7b71f2eb61d003700269675c785cbfb0c8
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70755035"
---
# <a name="fonts"></a>Fontes

## <a name="overview"></a>Visão geral

Começando com o nível de API 26, o SDK do Android permite que as fontes sejam tratadas como recursos, assim como em layouts ou drawables. A [biblioteca de suporte do Android 26 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/26.1.0.1) irá backport as novas APIs de fonte para os aplicativos que visam a API de nível 14 ou superior.

Após a API voltados 26 ou a instalação da biblioteca de suporte do Android V26, há duas maneiras de usar fontes em um aplicativo Android:

1. **Empacotar a fonte como um recurso do Android** &ndash; isso garante que a fonte esteja sempre disponível para o aplicativo, mas aumentará o tamanho do APK.
2. **Baixar as fontes** O Android também dá suporte ao download de uma fonte de um _provedor de fontes._ &ndash; O provedor de fontes verifica se a fonte já está no dispositivo. Se necessário, a fonte será baixada e armazenada em cache no dispositivo. Essa fonte pode ser compartilhada entre vários aplicativos.

Fontes semelhantes (ou uma fonte que pode ter vários estilos diferentes) podem ser agrupadas em _famílias de fontes_. Isso permite aos desenvolvedores especificar determinados atributos da fonte, como seu peso, e o Android selecionará automaticamente a fonte apropriada da família de fontes.

A biblioteca de suporte do Android V26 irá backport o suporte a fontes para o nível de API 26. Ao direcionar os níveis de API mais antigos, é necessário declarar `app` o namespace XML e nomear os vários atributos de fonte `android:` usando o namespace `app:` e o namespace. Se apenas o `android:` namespace for usado, as fontes não serão exibidas para dispositivos que executam a API nível 25 ou menos. Por exemplo, esse trecho de código XML declara um novo recurso de [_família de fontes_](#font_families) que funcionará no nível de API 14 e superior:

```xml
<?xml version="1.0" encoding="utf-8"?>
<font-family
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

     <font  android:font="@font/sourcesanspro_regular"
            android:fontStyle="normal"
            android:fontWeight="400"
            app:font="@font/sourcesanspro_regular"
            app:fontStyle="normal"
            app:fontWeight="400" />

</font-family>
```

Desde que as fontes sejam fornecidas a um aplicativo Android de uma maneira apropriada, elas podem ser aplicadas a um widget de interface do usuário definindo o [ `fontFamily` atributo](https://developer.android.com/reference/android/widget/TextView.html#attr_android:fontFamily). Por exemplo, o trecho a seguir demonstra como exibir uma fonte em um TextView:

```xml
<TextView
    android:text="The quick brown fox jumped over the lazy dog."
    android:fontFamily="@font/sourcesanspro_regular"
    app:fontFamily="@font/sourcesanspro_regular"
    android:textAppearance="?android:attr/textAppearanceLarge"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

Este guia primeiro discutirá como usar fontes como um recurso do Android e, em seguida, continuará a discutir como baixar fontes em tempo de execução.

## <a name="fonts-as-a-resource"></a>Fontes como um recurso

Empacotar uma fonte em um APK Android garante que ela esteja sempre disponível para o aplicativo. Um arquivo de fonte (um. TTF ou a. Arquivo OTF) é adicionado a um aplicativo Xamarin. Android, assim como qualquer outro recurso, copiando arquivos para um subdiretório na pasta **Resources** de um projeto Xamarin. Android. Os recursos de fontes são mantidos em um subdiretório de **fontes** da pasta de **recursos** do projeto.

> [!NOTE]
> As fontes devem ter uma **ação de compilação** de **AndroidResource** ou não serão empacotadas no apk final. A ação de compilação deve ser definida automaticamente pelo IDE.

Quando há muitos arquivos de fonte semelhantes (por exemplo, a mesma fonte com pesos ou estilos diferentes), é possível agrupá-los em uma família de fontes.

<a name="font_families" />

### <a name="font-families"></a>Famílias de fontes

Uma família de fontes é um conjunto de fontes que têm pesos e estilos diferentes. Por exemplo, pode haver arquivos de fonte separados para fontes em negrito ou itálico. A família de fontes é definida `font` por elementos em um arquivo XML que é mantido no diretório de **recursos/fontes** . Cada família de fontes deve ter seu próprio arquivo XML.

Para criar uma família de fontes, primeiro adicione todas as fontes à pasta **recursos/fonte** . Em seguida, crie um novo arquivo XML na pasta Font para a família de fontes. O nome do arquivo XML não tem nenhuma afinidade ou relação com as fontes que estão sendo referenciadas; o arquivo de recurso pode ser qualquer nome de arquivo de recurso do Android válido. Esse arquivo XML terá um elemento raiz `font-family` que contém um ou mais `font` elementos. Cada `font` elemento declara os atributos de uma fonte.

O XML a seguir é um exemplo de uma família de fontes para a fonte de _fontes San pro_ que define muitos pesos de fontes diferentes. Isso é salvo como arquivo na pasta de **recursos/fonte** chamada **sourcesanspro. xml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<font-family xmlns:android="http://schemas.android.com/apk/res/android"
             xmlns:app="http://schemas.android.com/apk/res-auto">
    <font android:font="@font/sourcesanspro_regular"
          android:fontStyle="normal"
          android:fontWeight="400"
          app:font="@font/sourcesanspro_regular"
          app:fontStyle="normal"
          app:fontWeight="400" />
    <font android:font="@font/sourcesanspro_bold"
          android:fontStyle="normal"
          android:fontWeight="800"
          app:font="@font/sourcesanspro_bold"
          app:fontStyle="normal"
          app:fontWeight="800" />
    <font android:font="@font/sourcesanspro_italic"
          android:fontStyle="italic"
          android:fontWeight="400"
          app:font="@font/sourcesanspro_italic"
          app:fontStyle="italic"
          app:fontWeight="400" />
</font-family>
```

O `fontStyle` atributo tem dois valores possíveis:

- **normal** &ndash; uma fonte normal
- **itálico** &ndash; uma fonte em itálico

O `fontWeight` atributo corresponde ao atributo de `font-weight` CSS e se refere à espessura da fonte. Esse é um valor no intervalo de 100 a 900. A lista a seguir descreve os valores comuns de espessura da fonte e seu nome:

- **Thin** &ndash; 100
- **Luz extra** &ndash; 200
- **Claro** &ndash; 300
- **Normal** &ndash; 400
- **Média** &ndash; 500
- **Seminegrito** &ndash; 600
- **Negrito** &ndash; 700
- **Negrito extra** &ndash; 800
- **Preto** &ndash; 900

Depois que uma família de fontes tiver sido definida, ela poderá ser usada declarativamente definindo `fontFamily`os `textStyle`atributos, `fontWeight` e no arquivo de layout.  Por exemplo, o trecho de código XML a seguir define uma fonte de peso de 600 (normal) e um estilo de texto em itálico:

```xml
<TextView
    android:text="Sans Source Pro semi-bold italic, 600 weight, italic"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:fontFamily="@font/sourcesanspro"
    android:textAppearance="?android:attr/textAppearanceLarge"
    android:gravity="center_horizontal"
    android:fontWeight="600"
    android:textStyle="italic"
    />
```

### <a name="programmatically-assigning-fonts"></a>Atribuindo fontes programaticamente

As fontes podem ser definidas de forma programática [`Resources.GetFont`](https://developer.android.com/reference/android/content/res/Resources.html#getFont(int)) usando o método para [`Typeface`](https://developer.android.com/reference/android/graphics/Typeface.html) recuperar um objeto. Muitas exibições têm `TypeFace` uma propriedade que pode ser usada para atribuir a fonte ao widget. Este trecho de código mostra como definir a fonte de forma programática em um TextView:

```csharp
Android.Graphics.Typeface typeface = this.Resources.GetFont(Resource.Font.caveat_regular);
textView1.Typeface = typeface;
textView1.Text = "Changed the font";
```

O `GetFont` método carregará automaticamente a primeira fonte dentro de uma família de fontes.  Para carregar uma fonte que corresponda a um estilo específico, use `Typeface.Create` o método. Esse método tentará carregar uma fonte que corresponda ao estilo especificado. Por exemplo, esse trecho tentará carregar um objeto em negrito `Typeface` de uma família de fontes definida em **recursos/fontes**:

```csharp
var typeface = Typeface.Create("<FONT FAMILY NAME>", Android.Graphics.TypefaceStyle.Bold);
textView1.Typeface = typeface;
```

## <a name="downloading-fonts"></a>Baixando fontes

Em vez de empacotar as fontes como um recurso de aplicativo, o Android pode baixar fontes de uma fonte remota. Isso terá o efeito desejável de reduzir o tamanho do APK.

As fontes são baixadas com a assistência de um _provedor de fontes_. Esse é um provedor de conteúdo especializado que gerencia o download e o cache de fontes para todos os aplicativos no dispositivo. O Android 8,0 inclui um provedor de fontes para baixar fontes do [repositório de fontes do Google](http://fonts.google.com). Esse provedor de fontes padrão é reportado para o nível 14 da API com a biblioteca de suporte do Android V26.

Quando um aplicativo faz uma solicitação para uma fonte, o provedor de fontes primeiro verifica se a fonte já está no dispositivo. Caso contrário, ele tentará baixar a fonte. Se a fonte não puder ser baixada, o Android usará a fonte padrão do sistema. Depois que a fonte for baixada, ela estará disponível para todos os aplicativos no dispositivo, não apenas para o aplicativo que fez a solicitação inicial.

Quando é feita uma solicitação para baixar uma fonte, o aplicativo não consulta diretamente o provedor de fontes. Em vez disso, os [`FontsContract`](https://developer.android.com/reference/android/provider/FontsContract.html) [`FontsContractCompat`](https://developer.android.com/reference/android/support/v4/provider/FontsContractCompat.html) aplicativos usarão uma instância da API (ou se a biblioteca de suporte 26 estiver sendo usada).  

O Android 8,0 dá suporte ao download de fontes de duas maneiras diferentes:

1. **Declarar fontes que podem ser baixadas como um recurso** &ndash; Um aplicativo pode declarar fontes para download no Android por meio de arquivos de recurso XML. Esses arquivos conterão todos os metadados que o Android precisa para baixar de forma assíncrona as fontes quando o aplicativo for iniciado e armazená-las em cache no dispositivo.
2. **Programaticamente** &ndash; As APIs no nível 26 da API do Android permitem que um aplicativo Baixe as fontes programaticamente, enquanto o aplicativo está em execução. Os aplicativos criarão `FontRequest` um objeto para uma determinada fonte e passarão esse objeto para `FontsContract` a classe. O `FontsContract` pega o `FontRequest` e recupera a fonte de um _provedor de fontes_. O Android baixará a fonte de forma síncrona. Um exemplo de criação de `FontRequest` um será mostrado posteriormente neste guia.

Independentemente da abordagem usada, os arquivos de recursos devem ser adicionados ao aplicativo Xamarin. Android antes que as fontes possam ser baixadas. Primeiro, as fontes devem ser declaradas em um arquivo XML no diretório de **recursos/fontes** como parte de uma família de fontes. Este trecho de código é um exemplo de como baixar fontes da [coleção de fonte aberta de fontes do Google](https://fonts.google.com) usando o provedor de fontes padrão fornecido com o Android 8,0 (ou a biblioteca de suporte V26):

```xml
<?xml version="1.0" encoding="utf-8"?>
<font-family xmlns:android="http://schemas.android.com/apk/res/android"
             xmlns:app="http://schemas.android.com/apk/res-auto"
             android:fontProviderAuthority="com.google.android.gms.fonts"
             android:fontProviderPackage="com.google.android.gms"
             android:fontProviderQuery="VT323"
             android:fontProviderCerts="@array/com_google_android_gms_fonts_certs"
             app:fontProviderAuthority="com.google.android.gms.fonts"
             app:fontProviderPackage="com.google.android.gms"
             app:fontProviderQuery="VT323"
             app:fontProviderCerts="@array/com_google_android_gms_fonts_certs"
>
</font-family>
```

O `font-family` elemento contém os seguintes atributos, declarando as informações que o Android exige para baixar as fontes:

1. **fontProviderAuthority** &ndash; A autoridade do provedor de fontes a ser usada para a solicitação.
2. **fontPackage** &ndash; O pacote para o provedor de fontes a ser usado para a solicitação. Isso é usado para verificar a identidade do provedor.
3. **fontQuery** &ndash; Esta é uma cadeia de caracteres que ajudará o provedor de fontes a localizar a fonte solicitada. Os detalhes sobre a consulta de fonte são específicos para o provedor de fontes. A [`QueryBuilder`](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/DownloadableFonts/QueryBuilder.cs) classe no aplicativo de exemplo de [fontes baixáveis](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/) fornece algumas informações sobre o formato de consulta para fontes da coleção de fonte aberta fontes do Google.
4. **fontProviderCerts** &ndash; Uma matriz de recursos com a lista de conjuntos de hashes para os certificados com os quais o provedor deve ser assinado.

Depois que as fontes são definidas, pode ser necessário fornecer informações sobre os _certificados de fonte_ envolvidos no download.

### <a name="font-certificates"></a>Certificados de fonte

Se o provedor de fontes não estiver pré-instalado no dispositivo ou se o aplicativo estiver usando a `Xamarin.Android.Support.Compat` biblioteca, o Android exigirá os certificados de segurança do provedor de fontes. Esses certificados serão listados em um arquivo de recurso de matriz que é mantido no diretório de **recursos/valores** .

Por exemplo, o XML a seguir é denominado **Resources/Values/fonts_cert. xml** e armazena os certificados para o provedor de fontes do Google:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <array name="com_google_android_gms_fonts_certs">
        <item>@array/com_google_android_gms_fonts_certs_dev</item>
        <item>@array/com_google_android_gms_fonts_certs_prod</item>
    </array>
    <string-array name="com_google_android_gms_fonts_certs_dev">
        <item>
            MIIEqDCCA5CgAwIBAgIJANWFuGx90071MA0GCSqGSIb3DQEBBAUAMIGUMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEQMA4GA1UEChMHQW5kcm9pZDEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDEiMCAGCSqGSIb3DQEJARYTYW5kcm9pZEBhbmRyb2lkLmNvbTAeFw0wODA0MTUyMzM2NTZaFw0zNTA5MDEyMzM2NTZaMIGUMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEQMA4GA1UEChMHQW5kcm9pZDEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDEiMCAGCSqGSIb3DQEJARYTYW5kcm9pZEBhbmRyb2lkLmNvbTCCASAwDQYJKoZIhvcNAQEBBQADggENADCCAQgCggEBANbOLggKv+IxTdGNs8/TGFy0PTP6DHThvbbR24kT9ixcOd9W+EaBPWW+wPPKQmsHxajtWjmQwWfna8mZuSeJS48LIgAZlKkpFeVyxW0qMBujb8X8ETrWy550NaFtI6t9+u7hZeTfHwqNvacKhp1RbE6dBRGWynwMVX8XW8N1+UjFaq6GCJukT4qmpN2afb8sCjUigq0GuMwYXrFVee74bQgLHWGJwPmvmLHC69EH6kWr22ijx4OKXlSIx2xT1AsSHee70w5iDBiK4aph27yH3TxkXy9V89TDdexAcKk/cVHYNnDBapcavl7y0RiQ4biu8ymM8Ga/nmzhRKya6G0cGw8CAQOjgfwwgfkwHQYDVR0OBBYEFI0cxb6VTEM8YYY6FbBMvAPyT+CyMIHJBgNVHSMEgcEwgb6AFI0cxb6VTEM8YYY6FbBMvAPyT+CyoYGapIGXMIGUMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEQMA4GA1UEChMHQW5kcm9pZDEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDEiMCAGCSqGSIb3DQEJARYTYW5kcm9pZEBhbmRyb2lkLmNvbYIJANWFuGx90071MAwGA1UdEwQFMAMBAf8wDQYJKoZIhvcNAQEEBQADggEBABnTDPEF+3iSP0wNfdIjIz1AlnrPzgAIHVvXxunW7SBrDhEglQZBbKJEk5kT0mtKoOD1JMrSu1xuTKEBahWRbqHsXclaXjoBADb0kkjVEJu/Lh5hgYZnOjvlba8Ld7HCKePCVePoTJBdI4fvugnL8TsgK05aIskyY0hKI9L8KfqfGTl1lzOv2KoWD0KWwtAWPoGChZxmQ+nBli+gwYMzM1vAkP+aayLe0a1EQimlOalO762r0GXO0ks+UeXde2Z4e+8S/pf7pITEI/tP+MxJTALw9QUWEv9lKTk+jkbqxbsh8nfBUapfKqYn0eidpwq2AzVp3juYl7//fKnaPhJD9gs=
        </item>
    </string-array>
    <string-array name="com_google_android_gms_fonts_certs_prod">
        <item>
            MIIEQzCCAyugAwIBAgIJAMLgh0ZkSjCNMA0GCSqGSIb3DQEBBAUAMHQxCzAJBgNVBAYTAlVTMRMwEQYDVQQIEwpDYWxpZm9ybmlhMRYwFAYDVQQHEw1Nb3VudGFpbiBWaWV3MRQwEgYDVQQKEwtHb29nbGUgSW5jLjEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDAeFw0wODA4MjEyMzEzMzRaFw0zNjAxMDcyMzEzMzRaMHQxCzAJBgNVBAYTAlVTMRMwEQYDVQQIEwpDYWxpZm9ybmlhMRYwFAYDVQQHEw1Nb3VudGFpbiBWaWV3MRQwEgYDVQQKEwtHb29nbGUgSW5jLjEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDCCASAwDQYJKoZIhvcNAQEBBQADggENADCCAQgCggEBAKtWLgDYO6IIrgqWbxJOKdoR8qtW0I9Y4sypEwPpt1TTcvZApxsdyxMJZ2JORland2qSGT2y5b+3JKkedxiLDmpHpDsz2WCbdxgxRczfey5YZnTJ4VZbH0xqWVW/8lGmPav5xVwnIiJS6HXk+BVKZF+JcWjAsb/GEuq/eFdpuzSqeYTcfi6idkyugwfYwXFU1+5fZKUaRKYCwkkFQVfcAs1fXA5V+++FGfvjJ/CxURaSxaBvGdGDhfXE28LWuT9ozCl5xw4Yq5OGazvV24mZVSoOO0yZ31j7kYvtwYK6NeADwbSxDdJEqO4k//0zOHKrUiGYXtqw/A0LFFtqoZKFjnkCAQOjgdkwgdYwHQYDVR0OBBYEFMd9jMIhF1Ylmn/Tgt9r45jk14alMIGmBgNVHSMEgZ4wgZuAFMd9jMIhF1Ylmn/Tgt9r45jk14aloXikdjB0MQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEUMBIGA1UEChMLR29vZ2xlIEluYy4xEDAOBgNVBAsTB0FuZHJvaWQxEDAOBgNVBAMTB0FuZHJvaWSCCQDC4IdGZEowjTAMBgNVHRMEBTADAQH/MA0GCSqGSIb3DQEBBAUAA4IBAQBt0lLO74UwLDYKqs6Tm8/yzKkEu116FmH4rkaymUIE0P9KaMftGlMexFlaYjzmB2OxZyl6euNXEsQH8gjwyxCUKRJNexBiGcCEyj6z+a1fuHHvkiaai+KL8W1EyNmgjmyy8AW7P+LLlkR+ho5zEHatRbM/YAnqGcFh5iZBqpknHf1SKMXFh4dd239FJ1jWYfbMDMy3NS5CTMQ2XFI1MvcyUTdZPErjQfTbQe3aDQsQcafEQPD+nqActifKZ0Np0IS9L9kR/wbNvyz6ENwPiTrjV2KRkEjH78ZMcUQXg0L3BYHJ3lc69Vs5Ddf9uUGGMYldX3WfMBEmh/9iFBDAaTCK
        </item>
    </string-array>
</resources>
```

Com esses arquivos de recurso em vigor, o aplicativo é capaz de baixar as fontes.

### <a name="declaring-downloadable-fonts-as-resources"></a>Declarando fontes baixáveis como recursos

Ao listar as fontes que podem ser baixadas no **AndroidManifest. xml**, o Android baixará as fontes de forma assíncrona quando o aplicativo for iniciado pela primeira vez. As próprias fontes são listadas em um arquivo de recurso de matriz, semelhante a esta:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <array name="downloadable_fonts" translatable="false">
        <item>@font/vt323</item>
    </array>
</resources>
```

Para baixar essas fontes, elas precisam ser declaradas em **AndroidManifest. xml** adicionando `meta-data` como `application` um filho do elemento. Por exemplo, se as fontes que podem ser baixadas forem declaradas em um arquivo de recurso em **Resources/Values/downloadable_fonts. xml**, esse trecho teria que ser adicionado ao manifesto:

```xml
<meta-data android:name="downloadable_fonts" android:resource="@array/downloadable_fonts" />
```

### <a name="downloading-a-font-with-the-font-apis"></a>Baixando uma fonte com as APIs de fonte

É possível baixar uma fonte programaticamente, instanciando um [`FontRequest`](https://developer.android.com/reference/android/support/v4/provider/FontRequest.html) objeto e passando-o para `FontContractCompat.RequestFont` o método. O `FontContractCompat.RequestFont` método primeiro verificará se a fonte existe no dispositivo e, se necessário, consultará o provedor de fonte de forma assíncrona e tentará baixar a fonte do aplicativo. Se `FontRequest` o não puder baixar a fonte, o Android usará a fonte padrão do sistema.

Um `FontRequest` objeto contém informações que serão usadas pelo provedor de fontes para localizar e baixar uma fonte. Um `FontRequest` exige quatro partes de informação:

1. **Autoridade do provedor de fontes** &ndash; A autoridade do provedor de fontes a ser usada para a solicitação.
2. **Pacote de fontes** &ndash; O pacote para o provedor de fontes a ser usado para a solicitação. Isso é usado para verificar a identidade do provedor.
3. **Consulta de fonte** &ndash; Esta é uma cadeia de caracteres que ajudará o provedor de fontes a localizar a fonte solicitada. Os detalhes sobre a consulta de fonte são específicos para o provedor de fontes. Os detalhes da cadeia de caracteres são específicos para o provedor de fontes. A [`QueryBuilder`](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/DownloadableFonts/QueryBuilder.cs) classe no aplicativo de exemplo de [fontes baixáveis](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/) fornece algumas informações sobre o formato de consulta para fontes da coleção de fonte aberta fontes do Google.
4. **Certificados do provedor de fontes** &ndash; Uma matriz de recursos com a lista de conjuntos de hashes para os certificados com os quais o provedor deve ser assinado.

Este trecho de código é um exemplo de instanciação `FontRequest` de um novo objeto:

```csharp
FontRequest request = new FontRequest("com.google.android.gms.fonts", "com.google.android.gms", <FontToDownload>, Resource.Array.com_google_android_gms_fonts_certs);
```

No trecho `FontToDownload` anterior, há uma consulta que ajudará a fonte da coleção de código aberto de fontes do Google.

Antes de passar `FontRequest` o para `FontContractCompat.RequestFont` o método, há dois objetos que devem ser criados:

- **`FontsContractCompat.FontRequestCallback`** &ndash; Esta é uma classe abstrata que deve ser estendida. É um retorno de chamada que será invocado `RequestFont` quando for concluído. Um aplicativo Xamarin. Android deve ser subclasse `FontsContractCompat.FontRequestCallback` e `OnTypefaceRequestFailed` substituir e `OnTypefaceRetrieved`, fornecendo as ações a serem executadas quando o download falhar ou for bem-sucedida, respectivamente.
- **`Handler`** Esse é um `Handler` que será usado pelo `RequestFont` para baixar a fonte em um thread, se necessário. &ndash; As fontes **não** devem ser baixadas no thread da interface do usuário.

Este trecho de código é um exemplo C# de uma classe que fará o download de uma fonte de forma assíncrona de uma coleção de código aberto de fontes do Google. Ele implementa a `FontRequestCallback` interface e gera um C# evento quando `FontRequest` tiver terminado.

```csharp
public class FontDownloadHelper : FontsContractCompat.FontRequestCallback
{
    // A very simple font query; replace as necessary
    public static readonly String FontToDownload = "Courgette";

    Android.OS.Handler Handler = null;

    public event EventHandler<FontDownloadEventArg> FontDownloaded = delegate
    {
        // just an empty delegate to avoid null reference exceptions.  
    };

    public void DownloadFonts(Context context)
    {
        FontRequest request = new FontRequest("com.google.android.gms.fonts", "com.google.android.gms",FontToDownload , Resource.Array.com_google_android_gms_fonts_certs);
        FontsContractCompat.RequestFont(context, request, this, GetHandlerThreadHandler());
    }

    public override void OnTypefaceRequestFailed(int reason)
    {
        base.OnTypefaceRequestFailed(reason);
        FontDownloaded(this, new FontDownloadEventArg(null));
    }

    public override void OnTypefaceRetrieved(Android.Graphics.Typeface typeface)
    {
        base.OnTypefaceRetrieved(typeface);
        FontDownloaded(this, new FontDownloadEventArg(typeface));
    }

    Handler GetHandlerThreadHandler()
    {
        if (Handler == null)
        {
            HandlerThread handlerThread = new HandlerThread("fonts");
            handlerThread.Start();
            Handler = new Handler(handlerThread.Looper);
        }
        return Handler;
    }
}

public class FontDownloadEventArg : EventArgs
{
    public FontDownloadEventArg(Android.Graphics.Typeface typeface)
    {
        Typeface = typeface;
    }
    public Android.Graphics.Typeface Typeface { get; private set; }
    public bool RequestFailed
    {
        get
        {
            return Typeface != null;
        }
    }
}
```

Para usar esse auxiliar, um novo `FontDownloadHelper` é criado e um manipulador de eventos é atribuído:  

```csharp
var fontHelper = new FontDownloadHelper();

fontHelper.FontDownloaded += (object sender, FontDownloadEventArg e) =>
{
    //React to the request
};
fontHelper.DownloadFonts(this); // this is an Android Context instance.
```

## <a name="summary"></a>Resumo

Este guia abordou as novas APIs no Android 8,0 para dar suporte a fontes e fontes para download como recursos. Ele abordou como inserir as fontes existentes em um APK e usá-las em um layout. Ele também abordou como o Android 8,0 dá suporte ao download de fontes de um provedor de fontes, seja de forma programática ou declarando os metadados de fonte nos arquivos de recurso.

## <a name="related-links"></a>Links relacionados

- [fontFamily](https://developer.android.com/reference/android/widget/TextView.html#attr_android:fontFamily)
- [FontConfig](https://developer.android.com/reference/android/text/FontConfig.html)
- [FontRequest](https://developer.android.com/reference/android/support/v4/provider/FontRequest.html)
- [FontsContractCompat](https://developer.android.com/reference/android/support/v4/provider/FontsContractCompat.html)
- [Resources.GetFont](https://developer.android.com/reference/android/content/res/Resources.html#getFont(int))
- [Typeface](https://developer.android.com/reference/android/graphics/Typeface.html)
- [NuGet da biblioteca de suporte do Android 26](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/)
- [Usando fontes no Android](https://www.youtube.com/watch?v=TfB-TsLFJdM)
- [Especificação de espessura de fonte de CSS](https://www.w3.org/TR/css-fonts-3/#font-weight-numeric-values)
- [Coleção de código aberto de fontes do Google](https://fonts.google.com/)
- [Sans pro de origem](https://fonts.google.com/specimen/Source+Sans+Pro)
