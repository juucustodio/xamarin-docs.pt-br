---
title: Fontes
ms.prod: xamarin
ms.assetid: 3F543FC5-FDED-47F8-8D2C-481FCC98BFDA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/09/2018
ms.openlocfilehash: c7953748e79bd43bc14601c1f0ea05d1a36adf08
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668122"
---
# <a name="fonts"></a>Fontes

## <a name="overview"></a>Visão geral

Começando com o nível de API 26, o SDK do Android permite que fontes sejam tratados como recursos, assim como um layouts ou desenháveis. O [NuGet 26 da biblioteca de suporte ao Android](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/26.1.0.1) será backport a nova fonte da API para esses aplicativos que se destinam a API nível 14 ou superior.

Depois de instalar a biblioteca de suporte ao Android v26 ou direcionado API 26, há duas maneiras de usar fontes em um aplicativo do Android:

1. **Pacote da fonte como um recurso do Android** &ndash; Isso garante que a fonte está sempre disponível para o aplicativo, mas aumentará o tamanho do APK.
2. **Baixar as fontes** &ndash; Android também oferece suporte para baixar uma fonte de uma _provedor de fonte_. O provedor de fonte verifica se a fonte já está no dispositivo. Se necessário, a fonte será baixada e armazenado em cache no dispositivo. Essa fonte pode ser compartilhada entre vários aplicativos.

Fontes semelhantes (ou uma fonte que pode ter vários estilos diferentes) pode ser agrupada em _famílias de fontes_. Isso permite que os desenvolvedores especifiquem determinados atributos de fonte, como o peso de TI, e o Android selecionará automaticamente a fonte apropriada da família de fonte.

A biblioteca de suporte a Android v26 será backport suporte a fontes para o nível de API 26. Ao direcionar os níveis de API mais antigos, é necessário declarar a `app` namespace de XML e nomear os vários atributos de fonte usando o `android:` namespace e o `app:` namespace. Se apenas a `android:` namespace é usado e, em seguida, as fontes não será exibida de dispositivos que executam o nível da API 25 ou menos. Por exemplo, este trecho de código XML declara uma nova [ _família de fontes_ ](#font_families) recursos que funcionam no nível da API 14 e superior:

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

Desde que as fontes são fornecidas para um aplicativo Android de forma adequada, elas podem ser aplicadas a um widget de interface do usuário definindo o [ `fontFamily` atributo](https://developer.android.com/reference/android/widget/TextView.html#attr_android:fontFamily). Por exemplo, o trecho a seguir demonstra como exibir uma fonte em um TextView:

```xml
<TextView
    android:text="The quick brown fox jumped over the lazy dog."
    android:fontFamily="@font/sourcesanspro_regular"
    app:fontFamily="@font/sourcesanspro_regular"
    android:textAppearance="?android:attr/textAppearanceLarge"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

Este guia primeiro discutem como usar fontes como um recurso do Android e, em seguida, passar para discutir como baixar fontes em tempo de execução.

## <a name="fonts-as-a-resource"></a>Fontes como um recurso

Empacotando uma fonte em um APK Android garante que ele esteja sempre disponível para o aplicativo. Um arquivo de fonte (qualquer um. TTF ou um. Arquivo OTF) é adicionado a um aplicativo xamarin. Android, assim como qualquer outro recurso, copiando arquivos para um subdiretório na **recursos** pasta de um projeto xamarin. Android. Recursos de fontes são mantidos em um **font** subpasta da **recursos** pasta do projeto.

> [!NOTE]
> As fontes devem ter uma **ação de compilação** dos **AndroidResource** ou eles não serão empacotados em APK final. A ação de compilação deve ser definida automaticamente pelo IDE.

Quando há muitos arquivos de fonte semelhantes (por exemplo, a mesma fonte com diferentes pesos ou estilos) é possível agrupá-los em uma família de fontes.

<a name="font_families" />

### <a name="font-families"></a>Famílias de fontes

Uma família de fontes é um conjunto de fontes que têm pesos diferentes e estilos. Por exemplo, pode haver arquivos de fontes separadas para fontes de negrito ou itálico. A família de fontes é definida pela `font` elementos em um arquivo XML que é mantido na **recursos/fonte** directory. Cada família de fonte deve ter seu próprio arquivo XML.

Para criar uma família de fontes, primeiro adicione todas as fontes para o **recursos/fonte** pasta. Em seguida, crie um novo arquivo XML na pasta da fonte para a família de fontes. O nome do arquivo XML não tem afinidade ou relação para as fontes que está sendo referenciadas; o arquivo de recurso pode ser qualquer nome de arquivo de recurso legal do Android. Esse arquivo XML terá uma raiz `font-family` elemento que contém um ou mais `font` elementos. Cada `font` elemento declara os atributos de uma fonte.

O XML a seguir está um exemplo de uma família de fontes para o _fontes Sans Pro_ fonte que define os pesos de muitas fontes diferentes. Isso é salvo como arquivo na **recursos/font** pasta denominada **sourcesanspro.xml**:

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

* **normal** &ndash; uma fonte normal
* **Itálico** &ndash; uma fonte em itálico

O `fontWeight` atributo corresponde à CSS `font-weight` de atributo e refere-se para a espessura da fonte. Este é um valor no intervalo de 100 900. A lista a seguir descreve os valores de peso de fonte comuns e seu nome:

* **Thin** &ndash; 100
* **Extra leve** &ndash; 200
* **Light** &ndash; 300
* **Normal** &ndash; 400
* **Médio** &ndash; 500
* **Semi negrito** &ndash; 600
* **Negrito** &ndash; 700
* **Extra negrito** &ndash; 800
* **Black** &ndash; 900

Depois que tiver sido definida uma família de fontes, ele pode ser usado declarativamente, definindo o `fontFamily`, `textStyle`, e `fontWeight` atributos no arquivo de layout.  Por exemplo, o trecho XML a seguir define uma fonte de 600 peso (normal) e um estilo de texto em itálico:

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

### <a name="programmatically-assigning-fonts"></a>Atribuição por meio de programação de fontes

Fontes podem ser definidas programaticamente, usando o [ `Resources.GetFont` ](https://developer.android.com/reference/android/content/res/Resources.html#getFont(int)) método para recuperar um [ `Typeface` ](https://developer.android.com/reference/android/graphics/Typeface.html) objeto. Vários modos de exibição têm um `TypeFace` propriedade que pode ser usada para atribuir a fonte para o widget. Este trecho de código mostra como definir programaticamente a fonte em um TextView:

```csharp
Android.Graphics.Typeface typeface = this.Resources.GetFont(Resource.Font.caveat_regular);
textView1.Typeface = typeface;
textView1.Text = "Changed the font";
```

O `GetFont` método carregará automaticamente a primeira fonte dentro de uma família de fontes.  Para carregar uma fonte que corresponde a um estilo específico, use o `Typeface.Create` método. Esse método tentará carregar uma fonte que corresponde ao estilo especificado. Por exemplo, este trecho de código tentará carregar um negrito `Typeface` objeto a partir de uma família de fontes é definida no **recursos/fontes**:

```csharp
var typeface = Typeface.Create("<FONT FAMILY NAME>", Android.Graphics.TypefaceStyle.Bold);
textView1.Typeface = typeface;
```

## <a name="downloading-fonts"></a>Download de fontes

Em vez de empacotamento de fontes como um recurso de aplicativo, o Android pode baixar fontes de uma fonte remota. Isso terá o efeito desejado de reduzir o tamanho do APK.

As fontes são baixadas com o auxílio de um _provedor de fonte_. Esse é um provedor de conteúdo especializado que gerencia a transferência e o cache de fontes para todos os aplicativos no dispositivo. Android 8.0 inclui um provedor de fonte para fazer o download de fontes do [repositório de fonte do Google](http://fonts.google.com). Este provedor de fonte padrão é retrocompatibilizada para o nível de API 14 com a biblioteca de suporte ao Android v26.

Quando um aplicativo faz uma solicitação para uma fonte, o provedor de fonte primeiro verificará se a fonte já está no dispositivo. Caso contrário, em seguida, ele tentará baixar a fonte. Se a fonte não pode ser baixado, Android, em seguida, usará a fonte padrão do sistema. Quando a fonte foi baixada, ele estará disponível a todos os aplicativos no dispositivo, não apenas o aplicativo que fez a solicitação inicial.

Quando é feita uma solicitação para fazer o download de uma fonte, o aplicativo não consultar diretamente o provedor de fonte. Em vez disso, os aplicativos usarão uma instância das [ `FontsContract` ](https://developer.android.com/reference/android/provider/FontsContract.html) API (ou o [ `FontsContractCompat` ](https://developer.android.com/reference/android/support/v4/provider/FontsContractCompat.html) se o suporte de biblioteca 26 estiver sendo usado).  

Android 8.0 dá suporte a fontes de download de duas maneiras diferentes:

1. **Fontes que pode ser baixado como um recurso de declarar** &ndash; um aplicativo pode declarar fontes que pode ser baixadas do Android por meio de arquivos de recurso XML. Esses arquivos conterá todos os metadados que precisa fazer o download assíncrono as fontes quando o aplicativo é iniciado e armazenam em cache no dispositivo Android.
2. **Por meio de programação** &ndash; APIs no nível de API do Android 26 permitem que um aplicativo baixar as fontes de forma programática, enquanto o aplicativo está em execução. Aplicativos criará um `FontRequest` do objeto para uma determinada fonte e passar esse objeto para o `FontsContract` classe. O `FontsContract` leva a `FontRequest` e recupera a fonte de uma _provedor de fonte_. Android sincronicamente baixará a fonte. Um exemplo de como criar um `FontRequest` será mostrado mais adiante neste guia.

Independentemente da abordagem usada, os arquivos de recursos que devem ser adicionados ao aplicativo xamarin. Android antes de fontes podem ser baixados. Em primeiro lugar, a fonte deve ser declarado em um arquivo XML na **recursos/fonte** diretório como parte de uma família de fontes. Este trecho de código é um exemplo de como baixar fontes do [coleção de código-fonte aberto de fontes de Google](https://fonts.google.com) usando o provedor de fonte padrão que vem com o Android 8.0 (ou biblioteca de suporte v26):

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

O `font-family` elemento contém os seguintes atributos, declarando as informações que o Android requer para baixar as fontes:

1. **fontProviderAuthority** &ndash; a autoridade do provedor da fonte a ser usado para a solicitação.
2. **fontPackage** &ndash; o pacote para o provedor de fonte a ser usado para a solicitação. Isso é usado para verificar a identidade do provedor.
3. **fontQuery** &ndash; é uma cadeia de caracteres que ajudarão o provedor de fonte a localizar a fonte solicitada. Obter detalhes sobre a consulta de fonte são específicos ao provedor de fonte. O [ `QueryBuilder` ](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/DownloadableFonts/QueryBuilder.cs) classe o [fontes para download](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/) aplicativo de exemplo fornece algumas informações sobre o formato da consulta para fontes da coleção de código-fonte aberto de Google fontes.
4. **fontProviderCerts** &ndash; com a lista de conjuntos de hash para os certificados que o provedor deve ser assinado com uma matriz de recursos.

Depois que as fontes são definidas, talvez seja necessário fornecer informações sobre o _certificados fonte_ envolvido com o download.

### <a name="font-certificates"></a>Certificados de fonte

Se o provedor de fonte não é pré-instalado no dispositivo, ou se o aplicativo estiver usando o `Xamarin.Android.Support.Compat` biblioteca, o Android exige que os certificados de segurança do provedor da fonte. Esses certificados serão listados em um arquivo de recurso de matriz que é mantido na **recursos/valores** directory.

Por exemplo, o XML a seguir é denominado **Resources/values/fonts_cert.xml** e armazena os certificados para o provedor de fonte do Google:

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

### <a name="declaring-downloadable-fonts-as-resources"></a>Declarando que pode ser baixadas fontes como recursos

Listando as fontes que pode ser baixadas no **androidmanifest. XML**, Android assincronamente baixará as fontes quando o aplicativo é iniciado pela primeira vez. A fonte do que em si são listados em um arquivo de recurso de matriz, semelhante a esta:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <array name="downloadable_fonts" translatable="false">
        <item>@font/vt323</item>
    </array>
</resources>
```

Para baixar essas fontes, eles precisarão ser declarado em **androidmanifest. XML** adicionando `meta-data` como um filho do `application` elemento. Por exemplo, se as fontes para download são declaradas em um arquivo de recurso no **Resources/values/downloadable_fonts.xml**, em seguida, teria este trecho de código a ser adicionado ao manifesto:

```xml
<meta-data android:name="downloadable_fonts" android:resource="@array/downloadable_fonts" />
```

### <a name="downloading-a-font-with-the-font-apis"></a>Download de uma fonte com as APIs de fonte

É possível baixar programaticamente uma fonte pela instanciação de um [ `FontRequest` ](https://developer.android.com/reference/android/support/v4/provider/FontRequest.html) objeto e passando para o `FontContractCompat.RequestFont` método. O `FontContractCompat.RequestFont` método primeiro verifica se a fonte existe no dispositivo e, em seguida, se necessário será assincronamente consulta o provedor de fonte e tente baixar a fonte para o aplicativo. Se `FontRequest` é não é possível baixar a fonte, em seguida, o Android usará a fonte padrão do sistema.

Um `FontRequest` objeto contém informações que serão usadas pelo provedor de fonte para localizar e baixar uma fonte. Um `FontRequest` requer quatro tipos de informações:

1. **Autoridade de provedor de fonte** &ndash; a autoridade do provedor da fonte a ser usado para a solicitação.
2. **Pacote de fontes** &ndash; o pacote para o provedor de fonte a ser usado para a solicitação. Isso é usado para verificar a identidade do provedor.
3. **Consulta de fonte** &ndash; é uma cadeia de caracteres que ajudarão o provedor de fonte a localizar a fonte solicitada. Obter detalhes sobre a consulta de fonte são específicos ao provedor de fonte. Os detalhes da cadeia de caracteres são específicos ao provedor de fonte. O [ `QueryBuilder` ](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/DownloadableFonts/QueryBuilder.cs) classe o [fontes para download](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/) aplicativo de exemplo fornece algumas informações sobre o formato da consulta para fontes da coleção de código-fonte aberto de Google fontes.
4. **Certificados do provedor de fonte** &ndash; com a lista de conjuntos de hash para os certificados que o provedor deve ser assinado com uma matriz de recursos.

Este trecho de código é um exemplo de instanciar um novo `FontRequest` objeto:

```csharp
FontRequest request = new FontRequest("com.google.android.gms.fonts", "com.google.android.gms", <FontToDownload>, Resource.Array.com_google_android_gms_fonts_certs);
```

No trecho anterior `FontToDownload` é uma consulta que ajudarão a fonte da coleção de código-fonte aberto de fontes de Google.

Antes de passar o `FontRequest` para o `FontContractCompat.RequestFont` método, há dois objetos que devem ser criados:

* **`FontsContractCompat.FontRequestCallback`** &ndash; Esta é uma classe abstrata que deve ser estendida. É um retorno de chamada que será invocado quando `RequestFont` é concluído. Um aplicativo xamarin. Android deve subclasse `FontsContractCompat.FontRequestCallback` e substitua o `OnTypefaceRequestFailed` e `OnTypefaceRetrieved`, fornecendo as ações a serem realizadas quando o download falha ou for bem-sucedida, respectivamente.
* **`Handler`** &ndash; Esse é um `Handler` que será usado pelo `RequestFont` para baixar a fonte em um thread, se necessário. Fontes devem **não** ser baixado no thread da interface do usuário.

Este trecho de código é um exemplo de um C# classe que fará o download assincronamente uma fonte de coleção de código-fonte aberto de fontes de Google. Ele implementa a `FontRequestCallback` interface e gera um C# evento quando `FontRequest` foi concluída.

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

Para usar esse auxiliar, um novo `FontDownloadHelper` é criado, e é atribuído a um manipulador de eventos:  

```csharp
var fontHelper = new FontDownloadHelper();

fontHelper.FontDownloaded += (object sender, FontDownloadEventArg e) =>
{
    //React to the request
};
fontHelper.DownloadFonts(this); // this is an Android Context instance.
```

## <a name="summary"></a>Resumo

Este guia abordou as novas APIs no Android 8.0 para dar suporte a fontes como recursos e fontes para download. Ele abordou como incorporar fontes existentes em um APK e usá-los em um layout. Ele também mostrou como Android 8.0 dá suporte a fontes de download de um provedor de fonte, por meio de programação ou declarando os fonte metadados em arquivos de recurso.

## <a name="related-links"></a>Links relacionados

- [fontFamily](https://developer.android.com/reference/android/widget/TextView.html#attr_android:fontFamily)
- [FontConfig](https://developer.android.com/reference/android/text/FontConfig.html)
- [FontRequest](https://developer.android.com/reference/android/support/v4/provider/FontRequest.html)
- [FontsContractCompat](https://developer.android.com/reference/android/support/v4/provider/FontsContractCompat.html)
- [Resources.GetFont](https://developer.android.com/reference/android/content/res/Resources.html#getFont(int))
- [Typeface](https://developer.android.com/reference/android/graphics/Typeface.html)
- [NuGet da biblioteca 26 de suporte do Android](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/)
- [Usando fontes no Android](https://www.youtube.com/watch?v=TfB-TsLFJdM)
- [Especificação de peso da fonte CSS](https://www.w3.org/TR/css-fonts-3/#font-weight-numeric-values)
- [Coleção de código-fonte aberto de fontes de Google](https://fonts.google.com/)
- [Fonte Sans Pro](https://fonts.google.com/specimen/Source+Sans+Pro)
