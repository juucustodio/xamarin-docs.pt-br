---
title: Criar recursos para variação de telas
ms.prod: xamarin
ms.assetid: 3D17DE45-115C-7192-5685-44F8EEE07DCC
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/28/2018
ms.openlocfilehash: 63cbe556783ffe22512ff5312817d522120bd15e
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670565"
---
# <a name="creating-resources-for-varying-screens"></a>Criando recursos para variação de telas

Android em si é executado em vários dispositivos diferentes, cada qual com uma ampla variedade de resoluções e tamanhos de tela densidades de tela. Android realizará a escala e o redimensionamento para tornar seu aplicativo funcionar nestes dispositivos, mas isso pode resultar em uma experiência de usuário abaixo do ideal. Por exemplo, imagens que apareciam desfocadas, ou pode ser posicionados conforme o esperado em um modo de exibição.


## <a name="concepts"></a>Conceitos

Alguns termos e conceitos são importantes para entender para dar suporte a várias telas.

- **Tamanho da tela** &ndash; a quantidade de espaço físico para a exibição de seu aplicativo

- **Densidade de tela** &ndash; o número de pixels em qualquer determinada área na tela. Unidade de medida típica é pontos por polegada (dpi).

- **Resolução** &ndash; o número total de pixels na tela. Ao desenvolver aplicativos, a resolução não é tão importante quanto a densidade e o tamanho da tela.

- **Independente de densidade de pixel (dp)** &ndash; uma unidade virtual de medida para permitir layouts ser projetado independentes de densidade. Essa fórmula é usada para converter o ponto de distribuição em pixels da tela:

    px &equals; dp &times; dpi &divide; 160

- **Orientação** &ndash; a orientação da tela é considerada como paisagem quando ele for maior do que a altura. Por outro lado, a orientação retrato é quando a tela é mais alta do que largo. A orientação pode alterar durante o tempo de vida de um aplicativo, conforme o usuário gira o dispositivo.

Observe que as três primeiras desses conceitos são inter-relacionados &ndash; aumentar a resolução sem aumentar a densidade aumentará o tamanho da tela. No entanto se são aumentados a densidade e a resolução, em seguida, o tamanho da tela pode permanece inalterado. Essa relação entre o tamanho da tela, densidade e resolução complicam suporte de tela rapidamente.

Para ajudar a lidar com essa complexidade, a estrutura do Android prefere usar *pixels independentes de densidade (dp)* para layouts de tela. Ao usar pixels independentes de densidade, elementos de interface do usuário serão exibida para o usuário tenha o mesmo tamanho físico em telas com diferentes densidades.


## <a name="supporting-various-screen-sizes-and-densities"></a>Suporte a vários tamanhos de tela e densidades

Android manipula a maioria do trabalho para processar os layouts corretamente para cada configuração de tela. No entanto, há algumas ações que podem ser executadas para ajudar o sistema.

O uso de pixels independentes de densidade em vez de pixels reais em layouts é suficiente na maioria dos casos, para garantir a independência de densidade.
Android dimensionará o desenháveis em tempo de execução para o tamanho apropriado.
No entanto, é possível que dimensionamento fará com que bitmaps sejam exibidos desfocado. Para contornar esse problema, fornece recursos alternativos para diferentes densidades. Ao projetar dispositivos para diversas resoluções e densidades de tela, ela será prova mais fácil começar com a resolução mais alta ou densidade imagens e, em seguida, reduzir verticalmente.


### <a name="declare-the-supported-screen-size"></a>Declare o tamanho da tela com suporte

Declarar o tamanho da tela, você garante que somente dispositivos com suporte podem baixar o aplicativo. Isso é feito definindo a [dá suporte a telas](https://developer.android.com/guide/topics/manifest/supports-screens-element.html) elemento o **androidmanifest. XML** arquivo. Esse elemento é usado para especificar quais tamanhos de tela são compatíveis com o aplicativo. Uma determinada tela é considerada como tendo suporte se o aplicativo pode posicionar corretamente os seus layouts para preencher a tela. Usando este elemento de manifesto, o aplicativo não aparecerá na [ *Google Play* ](https://play.google.com/) para dispositivos que não atendem as especificações de tela. No entanto, o aplicativo ainda será executado em dispositivos com telas sem suporte, mas os layouts podem aparecer desfocados e pixelada.

Tela suportadas sixes são declarados na **Properites/AndroidManifest.xml** arquivo da solução:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Manifesto do Android](resources-for-varying-screens-images/01-android-manifest-sml.w1581.png)](resources-for-varying-screens-images/01-android-manifest.w1581.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Manifesto do Android](resources-for-varying-screens-images/01-android-manifest-sml.m761.png)](resources-for-varying-screens-images/01-android-manifest.m761.png#lightbox)

-----

Edite **androidmanifest. XML** incluir [dá suporte a telas](https://developer.android.com/guide/topics/manifest/supports-screens-element.html):

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          android:versionCode="1"
          android:versionName="1.0"
          package="HelloWorld.HelloWorld">
      <uses-sdk android:minSdkVersion="21" android:targetSdkVersion="27" />
      <supports-screens android:resizable="true"
                        android:smallScreens="true"
                        android:normalScreens="true"
                        android:largeScreens="true" />
      <application android:allowBackup="true"
                   android:icon="@mipmap/ic_launcher"
                   android:label="@string/app_name"
                   android:roundIcon="@mipmap/ic_launcher_round"
                   android:supportsRtl="true" android:theme="@style/AppTheme">
  </application>
</manifest>
```

### <a name="provide-alternate-layouts-for-different-screen-sizes"></a>Fornecer layouts alternativos para diferentes tamanhos de tela


Layouts alternativos possibilitam personalizar um modo de exibição para um tamanho específico da tela, alterando o posicionamento ou o tamanho dos elementos de interface do usuário do componente.

Começando com o API nível 13 (Android 3.2), os tamanhos de tela são preteridos em favor do uso o sw*N*qualificador de dp. Esse novo qualificador declara que a quantidade de espaço em um determinado layout precisa. É recomendável que os aplicativos que se destinam a ser executado no Android 3.2 ou superior devem usar esses qualificadores mais recentes.

Por exemplo, se um layout necessário um mínimo de 700 dp da largura da tela, o layout alternativo deve ir em uma pasta **sw700dp layout**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Pasta de layout para a largura de tela de dp 700](resources-for-varying-screens-images/03-layout-sw700dp-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Pasta de layout para a largura de tela de dp 700](resources-for-varying-screens-images/03-layout-sw700dp-xs.png)

-----


Como diretriz, aqui estão alguns números para vários dispositivos:

- **Telefone típico** &ndash; 320 dp: um telefone típico

- **Um tablet 5"/"tweener"dispositivo** &ndash; 480 dp: como a observação da Samsung

- **Um tablet 7"** &ndash; 600 dp: como a Barnes &amp; Nook Noble

- **Um tablet de 10"** &ndash; 720 dp: como o Motorola Xoom

Para aplicativos que os níveis de API de destino até 12 (Android 3.1), os layouts devem ir em diretórios que usam os qualificadores **pequena**/**normal**/**grandes**  / **xlarge** como generalizações dos vários tamanhos de tela que estão disponíveis na maioria dos dispositivos. Por exemplo, na imagem abaixo, há recursos alternativos para os quatro tamanhos de tela diferentes:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Recursos alternativos para quatro tamanhos de tela](resources-for-varying-screens-images/04-layout-large-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Recursos alternativos para quatro tamanhos de tela](resources-for-varying-screens-images/04-layout-large-xs.png)

-----

A seguir está uma comparação de como os qualificadores de tamanho de tela do mais antigos pré-API nível 13 se comparam com pixels independentes de densidade:

- 426 dp x 320 dp é **pequeno**

- 470 dp x 320 dp é **normal**

- 640 dp x 480 dp é **grandes**

- 960 dp x 720 dp é **xlarge**

A tela mais recente dimensionar qualificadores no nível da API 13 e backup têm uma precedência maior do que os qualificadores de tela mais antigas dos níveis de API 12 e inferiores.
Para aplicativos que abrangerão os antigos e novos níveis de API, ele pode ser necessário criar recursos alternativos usando os dois conjuntos de qualificadores, conforme mostrado na seguinte captura de tela:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Recursos alternativos com ambos os qualificadores](resources-for-varying-screens-images/05-both-qualifiers-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Recursos alternativos com ambos os qualificadores](resources-for-varying-screens-images/05-both-qualifiers-xs.png)

-----



### <a name="provide-different-bitmaps-for-different-screen-densities"></a>Fornecer bitmaps diferentes para diferentes densidades de tela

Embora o Android dimensionará bitmaps conforme necessário para um dispositivo, os bitmaps em si não pode elegantemente escalar ou reduzir verticalmente: poderão ficar difusa ou indefinida. Fornecer bitmaps apropriado para a densidade da tela para atenuar esse problema.

Por exemplo, a imagem abaixo é um exemplo de layout e aparência problemas que podem ocorrer quando densidade-especificar recursos não são fornecidos.

![Capturas de tela sem recursos de densidade](resources-for-varying-screens-images/06-density-not-provided.png)

Compare isso com um layout que é projetado com recursos específicos de densidade:

![Capturas de tela com recursos específicos de densidade](resources-for-varying-screens-images/07-density-specific-resources.png)


### <a name="create-varying-density-resources-with-android-asset-studio"></a>Criar recursos de densidade variados com Android Asset Studio

A criação de migrar esses bitmaps de várias densidades pode ser um pouco entediante. Como tal, o Google criou um utilitário on-line que pode reduzir alguns do tédio envolvido na criação desses bitmaps chamada a [ **Android Asset Studio**](https://romannurik.github.io/AndroidAssetStudio/).

[![Android Asset Studio](resources-for-varying-screens-images/08-android-asset-studio-sml.png)](resources-for-varying-screens-images/08-android-asset-studio.png#lightbox)

Este site ajudará com a criação de bitmaps que se destinam as quatro densidades de tela comum, fornecendo uma imagem. Android Asset Studio criará, em seguida, os bitmaps com algumas personalizações e, em seguida, permitir que eles sejam baixados como um arquivo zip.


## <a name="tips-for-multiple-screens"></a>Dicas para várias telas

Android é executado em um número desconcertante dos dispositivos e a combinação de tamanhos de tela e densidades de tela pode parecer assustadora. As dicas a seguir podem ajudar a minimizar o esforço necessário para dar suporte a vários dispositivos:

- **Apenas projetar e desenvolver para o que você precisa** &ndash; há muitos dispositivos diferentes por aí, mas alguns existem em fatores forma raro que podem levar um esforço significativo para projetar e desenvolver para o. O [ **tamanho da tela e densidade** ](https://developer.android.com/resources/dashboard/screens.html) painel é uma página fornecida pelo Google que fornecem dados sobre a divisão da matriz de densidade de tamanhos de tela da tela. Essa divisão fornece informações sobre como o esforço de desenvolvimento em dar suporte a telas.

- **Usar o DPs em vez de Pixels** -Pixels se tornar problemáticos como alterações de densidade da tela. Não codificar os valores de pixel. Evite pixels em favor de dp (pixels independentes de densidade).

- **Evite** [AbsoluteLayout](https://developer.xamarin.com/api/type/Android.Widget.AbsoluteLayout/)
  **sempre que possível** &ndash; ele foi preterido no nível da API 3 (1.5 Android) e resultará em layouts frágil. Ele não deve ser usado. Em vez disso, tente usar os widgets de layout mais flexíveis, como [ **LinearLayout**](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/), [ **RelativeLayout**](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/), ou o novo [ **GridLayout**](https://developer.xamarin.com/api/type/Android.Widget.GridLayout/).

- **Escolher uma orientação de layout como padrão** &ndash; por exemplo, em vez de fornecer recursos alternativos **layout land** e **layout porta**, coloque os recursos para Paisagem no **layout**e os recursos para retrato no **layout porta**.

- **Use LayoutParams para altura e largura** - ao definir os elementos de interface do usuário em um arquivo de layout XML, um aplicativo Android usando o **wrap_content** e **fill_parent** valores terão mais sucesso Certifique-se de uma aparência apropriada em diferentes dispositivos que o uso de pixels ou unidades independentes de densidade. Esses valores de dimensão fazer com que o Android para dimensionar recursos de bitmap conforme apropriado. Por essa mesma razão, unidades independentes de densidade são mais adequadas para quando especificando as margens e preenchimento de elementos de interface do usuário.


## <a name="testing-multiple-screens"></a>Teste várias telas

Um aplicativo do Android deve ser testado em relação a todas as configurações que terão suporte. O ideal é que os dispositivos devem ser testados nos próprios dispositivos mas em muitos casos isso não é possível ou prático.
Nesse caso, o uso do emulador e a instalação de dispositivos virtuais Android para cada configuração de dispositivo será útil.

O SDK do Android fornece alguns emulador capas podem ser usadas para criar AVDs replicará o tamanho, a densidade e a resolução de vários dispositivos.
Da mesma forma, muitos fornecedores de hardware fornecem capas para seus dispositivos.

Outra opção é usar os serviços do serviço de teste.
Esses serviços levar um APK, executá-lo em vários dispositivos diferentes e, em seguida, fornecer comentários como o aplicativo funcionou.
