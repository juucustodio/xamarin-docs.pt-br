---
title: Criar recursos para variação de telas
ms.prod: xamarin
ms.assetid: 3D17DE45-115C-7192-5685-44F8EEE07DCC
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/28/2018
ms.openlocfilehash: 49e0de909e2255d850211e51596efdaa43f293ae
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68509365"
---
# <a name="creating-resources-for-varying-screens"></a>Criando recursos para diferentes telas

O Android é executado em vários dispositivos diferentes, cada um com uma ampla variedade de resoluções, tamanhos de tela e densidades de tela. O Android executará o dimensionamento e o redimensionamento para fazer seu aplicativo funcionar nesses dispositivos, mas isso pode resultar em uma experiência de usuário abaixo do ideal. Por exemplo, as imagens podem aparecer borradas ou podem estar posicionadas conforme o esperado em uma exibição.


## <a name="concepts"></a>Conceitos

Alguns termos e conceitos são importantes para entender para dar suporte a várias telas.

- **Tamanho da tela** &ndash; A quantidade de espaço físico para exibir seu aplicativo

- **Densidade da tela** &ndash; O número de pixels em qualquer área especificada na tela. A unidade de medida típica é de pontos por polegada (DPI).

- **Resolução** &ndash; O número total de pixels na tela. Ao desenvolver aplicativos, a resolução não é tão importante quanto o tamanho e a densidade da tela.

- **Pixel independente de densidade (DP)** &ndash; Uma unidade de medida virtual para permitir que os layouts sejam projetados independentemente da densidade. Esta fórmula é usada para converter o DP em pixels da tela:

    PX &equals; DP &times; dpi 160&divide;

- **Orientação** do &ndash; A orientação da tela é considerada paisagem quando é mais larga do que a altura. Por outro lado, a orientação retrato é quando a tela é mais alta do que a largura. A orientação pode ser alterada durante o tempo de vida de um aplicativo enquanto o usuário gira o dispositivo.

Observe que os três primeiros desses conceitos são inter-relacionados, &ndash; aumentando a resolução sem aumentar a densidade aumentará o tamanho da tela. No entanto, se a densidade e a resolução forem aumentadas, o tamanho da tela poderá permanecer inalterado. Essa relação entre o tamanho da tela, a densidade e a resolução complica o suporte de tela rapidamente.

Para ajudar a lidar com essa complexidade, a estrutura do Android prefere usar os *espd (pixels independentes de densidade)* para layouts de tela. Usando pixels independentes de densidade, os elementos da interface do usuário aparecerão para que ele tenha o mesmo tamanho físico em telas com diferentes densidades.


## <a name="supporting-various-screen-sizes-and-densities"></a>Dando suporte a vários tamanhos de tela e densidades

O Android lida com a maior parte do trabalho para renderizar os layouts corretamente para cada configuração de tela. No entanto, há algumas ações que podem ser tomadas para ajudar o sistema a sair.

O uso de pixels independentes de densidade em vez de pixels reais em layouts é suficiente na maioria dos casos para garantir a independência de densidade.
O Android dimensionará o drawables em tempo de execução para o tamanho apropriado.
No entanto, é possível que o dimensionamento faça com que os bitmaps apareçam borrados. Para contornar esse problema, forneça recursos alternativos para as diferentes densidades. Ao criar dispositivos para várias resoluções e densidades de tela, será mais fácil começar com as imagens mais altas de resolução ou de alta capacidade e, em seguida, reduzir verticalmente.


### <a name="declare-the-supported-screen-size"></a>Declarar o tamanho de tela com suporte

A declaração do tamanho da tela garante que apenas os dispositivos com suporte possam baixar o aplicativo. Isso é feito definindo o elemento [suportation-screens](https://developer.android.com/guide/topics/manifest/supports-screens-element.html) no arquivo **AndroidManifest. xml** . Esse elemento é usado para especificar quais tamanhos de tela têm suporte no aplicativo. Uma determinada tela será considerada suportada se o aplicativo puder posicionar corretamente seus layouts para preencher a tela. Usando esse elemento de manifesto, o aplicativo não aparecerá no [*Google Play*](https://play.google.com/) para dispositivos que não atendem às especificações da tela. No entanto, o aplicativo ainda será executado em dispositivos com telas sem suporte, mas os layouts podem parecer borrados e pixels.

As Sixes de tela com suporte são declaradas no arquivo **colocalizadas/AndroidManifest. xml** da solução:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Manifesto do Android](resources-for-varying-screens-images/01-android-manifest-sml.w1581.png)](resources-for-varying-screens-images/01-android-manifest.w1581.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Manifesto do Android](resources-for-varying-screens-images/01-android-manifest-sml.m761.png)](resources-for-varying-screens-images/01-android-manifest.m761.png#lightbox)

-----

Edite **AndroidManifest. xml** para incluir [suporte-telas](https://developer.android.com/guide/topics/manifest/supports-screens-element.html):

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


Layouts alternativos possibilitam personalizar um modo de exibição para um tamanho de tela específico, alterando o posicionamento ou o tamanho dos elementos da interface do usuário do componente.

Começando com o nível de API 13 (Android 3,2), os tamanhos de tela são preteridos em favor do uso do qualificador de DP SW*N*. Esse novo qualificador declara a quantidade de espaço que um determinado layout precisa. É recomendável que os aplicativos que devem ser executados no Android 3,2 ou superior devam usar esses qualificadores mais recentes.

Por exemplo, se um layout exigir um mínimo de 700 DP de largura de tela, o layout alternativo entrará em um layout de pasta **-sw700dp**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Pasta de layout para largura de tela de 700 DP](resources-for-varying-screens-images/03-layout-sw700dp-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Pasta de layout para largura de tela de 700 DP](resources-for-varying-screens-images/03-layout-sw700dp-xs.png)

-----


Como diretriz, aqui estão alguns números para vários dispositivos:

- **Telefone típico** &ndash; 320 DP: um telefone típico

- **Um dispositivo 5 "Tablet/" Tweener "** &ndash; 480 DP: como a observação do Samsung

- **Um 7 "Tablet** 600 DP: como o Nook da &amp;BarnesNoble &ndash;

- **Um Tablet de 10 "** &ndash; 720 DP: como o Motorola Xoom

Para aplicativos que têm como destino os níveis de API de até 12 (Android 3,1), os layouts devem ir em diretórios que usam os qualificadores **Small**//**xlarges** pequenos**normais**/como generalizações de os vários tamanhos de tela que estão disponíveis na maioria dos dispositivos. Por exemplo, na imagem abaixo, há recursos alternativos para os quatro tamanhos de tela diferentes:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Recursos alternativos para quatro tamanhos de tela](resources-for-varying-screens-images/04-layout-large-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Recursos alternativos para quatro tamanhos de tela](resources-for-varying-screens-images/04-layout-large-xs.png)

-----

Veja a seguir uma comparação de como os qualificadores de tamanho de tela mais antigos do nível de pré-API se comparam a pixels independentes de densidade:

- 426 DP x 320 DP é **pequeno**

- 470 DP x 320 DP é **normal**

- 640 DP x 480 DP é **grande**

- 960 DP x 720 DP é **XLarge**

Os qualificadores de tamanho de tela mais recentes no nível de API 13 e acima têm uma precedência mais alta do que os qualificadores de tela mais antigos dos níveis de API 12 e inferiores.
Para aplicativos que irão abranger os níveis de API antigos e novos, pode ser necessário criar recursos alternativos usando os dois conjuntos de qualificadores, conforme mostrado na seguinte captura de tela:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Recursos alternativos com ambos os qualificadores](resources-for-varying-screens-images/05-both-qualifiers-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Recursos alternativos com ambos os qualificadores](resources-for-varying-screens-images/05-both-qualifiers-xs.png)

-----



### <a name="provide-different-bitmaps-for-different-screen-densities"></a>Fornecer bitmaps diferentes para diferentes densidades de tela

Embora o Android dimensione os bitmaps conforme necessário para um dispositivo, os próprios bitmaps podem não ser expandidos ou reduzidos de forma elegante: eles podem se tornar difusas ou borrados. Fornecer bitmaps apropriados para a densidade da tela atenuará esse problema.

Por exemplo, a imagem abaixo é um exemplo de problemas de layout e aparência que podem ocorrer quando os recursos de densidade-especificação não são fornecidos.

![Capturas de tela sem recursos de densidade](resources-for-varying-screens-images/06-density-not-provided.png)

Compare isso com um layout projetado com recursos específicos de densidade:

![Capturas de tela com recursos específicos de densidade](resources-for-varying-screens-images/07-density-specific-resources.png)


### <a name="create-varying-density-resources-with-android-asset-studio"></a>Criar recursos de densidade variável com o Android Asset Studio

A criação desses bitmaps de várias densidades pode ser um pouco entediante. Dessa forma, o Google criou um utilitário online que pode reduzir alguns dos tédio envolvidos na criação desses bitmaps chamados [**Android Asset Studio**](https://romannurik.github.io/AndroidAssetStudio/).

[![Android Asset Studio](resources-for-varying-screens-images/08-android-asset-studio-sml.png)](resources-for-varying-screens-images/08-android-asset-studio.png#lightbox)

Este site ajudará na criação de bitmaps direcionados para as quatro densidades de tela comuns, fornecendo uma imagem. O Android Asset Studio criará os bitmaps com algumas personalizações e, em seguida, permitirá que eles sejam baixados como um arquivo zip.


## <a name="tips-for-multiple-screens"></a>Dicas para várias telas

O Android é executado em um número desconcertante de dispositivos, e a combinação de tamanhos de tela e densidades de tela pode parecer difícil. As dicas a seguir podem ajudar a minimizar o esforço necessário para dar suporte a vários dispositivos:

- **Projete e desenvolva apenas para o que você precisa** &ndash; Há muitos dispositivos diferentes por aí, mas alguns existem em fatores forma raros que podem levar a um esforço significativo para criar e desenvolver para o. O painel [**tamanho da tela e densidade**](https://developer.android.com/resources/dashboard/screens.html) é uma página fornecida pelo Google que fornece dados sobre a divisão da matriz de tamanho da tela/densidade da tela. Essa divisão fornece informações sobre como fazer o desenvolvimento de telas de suporte.

- **Use o DPS em vez de pixels** . os pixels se tornarão problemáticos conforme as alterações na densidade da tela. Não codifique valores de pixel. Evite pixels em favor de DP (pixels independentes de densidade).

- **Evitar** [AbsoluteLayout](xref:Android.Widget.AbsoluteLayout) Sempre que possível&ndash; , ele é preterido no nível de API 3 (Android 1,5) e resultará em layouts frágeis.  
   Ele não deve ser usado. Em vez disso, tente usar widgets de layout mais flexíveis, como [**LinearLayout**](xref:Android.Widget.LinearLayout), [**RelativeLayout**](xref:Android.Widget.RelativeLayout)ou a nova [**GridLayout**](xref:Android.Widget.GridLayout).

- **Escolha uma orientação de layout como padrão**    Por exemplo, em vez de fornecer os recursos alternativos layout-Land e layout-Port, coloque os recursos para paisagem no layout e os recursos para retrato em layout-Port. &ndash;

- **Usar LayoutParams para altura e largura** – ao definir elementos da interface do usuário em um arquivo de layout XML, um aplicativo Android usando os valores **wrap_content** e **fill_parent** terá mais sucesso para garantir uma aparência adequada em diferentes dispositivos do que usando unidades independentes de pixel ou densidade. Esses valores de dimensão fazem com que o Android dimensione os recursos de bitmap conforme apropriado. Por esse mesmo motivo, as unidades independentes de densidade são mais bem reservadas para a especificação de margens e preenchimento de elementos de interface do usuário.


## <a name="testing-multiple-screens"></a>Testando várias telas

Um aplicativo Android deve ser testado em relação a todas as configurações que terão suporte. O ideal é que os dispositivos sejam testados nos próprios dispositivos, mas em muitos casos isso não é possível ou prático.
Nesse caso, o uso da configuração do emulador e dos dispositivos virtuais Android para cada configuração de dispositivo será útil.

O SDK do Android fornece algumas capas do emulador que podem ser usadas para criar AVDs replicará o tamanho, a densidade e a resolução de muitos dispositivos.
Muitos dos fornecedores de hardware, de modo semelhante, fornecem capas para seus dispositivos.

Outra opção é usar os serviços de um serviço de teste de terceiros.
Esses serviços terão um APK, executarão em vários dispositivos diferentes e, em seguida, fornecerão comentários sobre como o aplicativo funcionou.
