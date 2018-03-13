---
title: Criando recursos para telas variadas
ms.topic: article
ms.prod: xamarin
ms.assetid: 3D17DE45-115C-7192-5685-44F8EEE07DCC
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: fcd77d97d492baee441cfd428e58ea83525f927e
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="creating-resources-for-varying-screens"></a>Criando recursos para telas variadas

Android em si é executado em vários dispositivos diferentes, cada um com uma ampla variedade de densidades de tela, tamanhos de telas e resoluções. Android executará dimensionamento e redimensionamento para tornar seu aplicativo trabalhar nesses dispositivos, mas isso pode resultar em uma experiência de usuário abaixo do ideal. Por exemplo, imagens parecer confuso, imagens podem ocupar espaço na tela demais (ou não há) que faz com que a posição dos elementos de interface do usuário no layout de sobreposição ou ser muito distantes.


## <a name="concepts"></a>Conceitos

Alguns termos e conceitos são importantes para entender para dar suporte a várias telas.

- **Tamanho da tela** &ndash; a quantidade de espaço físico para a exibição de seu aplicativo

- **Tela de densidade** &ndash; o número de pixels em qualquer área determinada na tela. A unidade de medida de típica é pontos por polegada (dpi).

- **Resolução** &ndash; o número total de pixels na tela. Ao desenvolver aplicativos, a resolução não é tão importante quanto a densidade e o tamanho da tela.

- **Independente de densidade de pixels (dp)** &ndash; isso é uma unidade virtual de medidas para permitir layouts criados independentes de densidade. Para converter o ponto de distribuição em pixels da tela, a fórmula a seguir é usada:

    px &equals; dp &times; dpi &divide; 160

- **Orientação** &ndash; a orientação da tela é considerada como paisagem quando for maior que a altura. Por outro lado, a orientação retrato é quando a tela é mais alta do que largo. A orientação pode alterar durante o tempo de vida de um aplicativo como o usuário gira o dispositivo.

Observe que as três primeiras desses conceitos estão relacionadas entre &ndash; aumentar a resolução sem aumentar a densidade aumentará o tamanho da tela. No entanto se aumentam a densidade e a resolução, em seguida, o tamanho de tela pode permanece inalterado. Essa relação entre o tamanho da tela, a densidade e a resolução complicar suporte tela muito rapidamente.

Para ajudar a lidar com essa complexidade, a estrutura do Android prefere usar *independente de densidade de pixels (dp)* para layouts de tela. Usando pixels independentes de densidade, elementos de interface do usuário serão exibido para o usuário tenha o mesmo tamanho físico em telas com diferentes densidades.


## <a name="supporting-various-screen-sizes-and-densities"></a>Suporte a vários tamanhos de telas e densidades

Android manipula a maioria do trabalho para processar os layouts corretamente para cada configuração de tela. No entanto, há algumas ações que podem ser tomadas para ajudar o sistema.

O uso de independente de densidade de pixels em vez de pixels reais em layouts é suficiente na maioria dos casos, para garantir a independência de densidade.
Android dimensionará o drawables em tempo de execução para o tamanho apropriado.
No entanto, é possível que esse escalonamento fará com que bitmaps apareça indefinida. Para evitar isso, pode ser necessário fornecer recursos alternativos para diferentes densidades. Durante a criação de dispositivos para várias resoluções e tela densidades comprove mais fácil começar com a resolução mais alta ou densidade imagens e, em seguida, reduzir. Isso impedirá qualquer obscurecendo ou distorção que resulta de redimensionamento.


### <a name="declare-the-screen-size-the-application-supports"></a>Declare o tamanho da tela da aplicativo dá suporte a

Declarar o tamanho da tela, você garante que somente dispositivos com suporte podem baixar o aplicativo. Isso é feito definindo o [dá suporte a telas](http://developer.android.com/guide/topics/manifest/supports-screens-element.html) elemento o **AndroidManifest.xml** arquivo. Esse elemento é usado para especificar quais tamanhos de tela são compatíveis com o aplicativo. Uma determinada tela é considerada com suporte se o aplicativo pode corretamente-lo do layouts para preencher a tela. Usando esse elemento manifesto, o aplicativo não aparecerá no [ *Google Play* ](https://play.google.com/) para dispositivos que não atende às especificações de tela. No entanto, ainda, o aplicativo será executado em dispositivos com telas sem suporte, mas os layouts parecer confuso e como pixels.

Para fazer isso no xamarin, é necessário primeiro adicionar um **AndroidManifest.xml** se ele ainda não existir o arquivo para o projeto:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Manifesto do Android](resources-for-varying-screens-images/01-android-manifest-vs-sml.png)](resources-for-varying-screens-images/01-android-manifest-vs.png#lightbox)

**AndroidManifest.xml** é adicionada para o **propriedades** directory. O arquivo é editado, em seguida, para incluir [dá suporte a telas](http://developer.android.com/guide/topics/manifest/supports-screens-element.html):

[![Adicionando dá suporte a telas](resources-for-varying-screens-images/02-adding-supports-screens-vs-sml.png)](resources-for-varying-screens-images/02-adding-supports-screens-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Manifesto do Android](resources-for-varying-screens-images/01-android-manifest-xs-sml.png)](resources-for-varying-screens-images/01-android-manifest-xs.png#lightbox)

**AndroidManifest.xml** é adicionada para o **propriedades** directory. O arquivo é editado, em seguida, para incluir [dá suporte a telas](http://developer.android.com/guide/topics/manifest/supports-screens-element.html):

[![Adicionando dá suporte a telas](resources-for-varying-screens-images/02-adding-supports-screens-xs-sml.png)](resources-for-varying-screens-images/02-adding-supports-screens-xs.png#lightbox)

-----

### <a name="provide-alternate-layouts-for-different-screen-sizes"></a>Fornecer Layouts alternativos para diferentes tamanhos de tela

Embora Android será redimensionado de acordo com o tamanho da tela, isso pode não ser suficiente em alguns casos. Pode ser desejável para aumentar o tamanho de alguns elementos de interface do usuário em uma tela maior, ou para alterar o posicionamento dos elementos da interface do usuário para uma tela menor.

A partir da 13 de nível de API (Android 3.2), os tamanhos de tela são preteridos em favor de usando o software*N*qualificador de ponto de distribuição. Esse novo qualificador declara que precisa a quantidade de espaço em um layout específico. É altamente recomendável que os aplicativos que se destinam a executar no Android 3.2 ou posterior devem estar usando estes qualificadores mais recentes.

Por exemplo, se um layout necessário um 700dp mínimo da largura da tela, o layout alternativo deve ir em uma pasta **sw700dp layout**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Pasta de layout para a largura da tela 700dp](resources-for-varying-screens-images/03-layout-sw700dp-vs.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Pasta de layout para a largura da tela 700dp](resources-for-varying-screens-images/03-layout-sw700dp-xs.png)

-----


Como diretriz, aqui estão alguns números para vários dispositivos:

- **Telefone típico** &ndash; 320dp: um telefone típico

- **Um tablet 5"/"tweener"dispositivo** &ndash; 480dp: como a observação da Samsung

- **Um tablet 7"** &ndash; 600dp: como a Barnes &amp; Nook Noble

- **Um tablet 10"** &ndash; 720dp: como o Motorola Xoom

Para aplicativos que os níveis de API de destino até 12 (Android 3.1), os layouts devem ir em diretórios que usam os qualificadores **pequeno**/**normal**/**grande**  / **xlarge** como generalizações de vários tamanhos de tela que estão disponíveis na maioria dos dispositivos. Por exemplo, na imagem abaixo, há recursos alternativos para os quatro diferentes tamanhos de tela:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Recursos alternativos para quatro tamanhos de tela](resources-for-varying-screens-images/04-layout-large-vs.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Recursos alternativos para quatro tamanhos de tela](resources-for-varying-screens-images/04-layout-large-xs.png)

-----

Uma comparação de como os qualificadores de tamanho de tela do mais antigos pre-API nível 13 comparam a pixels independentes de densidade é o seguinte:

- 426dp x 320dp é **pequeno**

- 470dp x 320dp é **normal**

- 640dp x 480dp é **grande**

- é 960dp x 720dp **xlarge**

A tela mais recente qualificadores no nível de API 13 de tamanho e se tem uma precedência maior do que os qualificadores de tela mais antigas dos níveis de API 12 e mais baixo.
Para aplicativos que ocupem antigos e novos níveis de API, pode ser necessário criar recursos alternativos usando os dois conjuntos de qualificadores, conforme mostrado na captura de tela a seguir:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Recursos alternativos com ambos os qualificadores](resources-for-varying-screens-images/05-both-qualifiers-vs.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Recursos alternativos com ambos os qualificadores](resources-for-varying-screens-images/05-both-qualifiers-xs.png)

-----



### <a name="provide-different-bitmaps-for-different-screen-densities"></a>Fornecer Bitmaps diferentes densidades de tela diferente

Embora o Android dimensionará bitmaps conforme necessário para um dispositivo, os bitmaps em si não pode apenas expandir ou reduzir: eles podem se tornar difusa ou indefinida. Fornecer bitmaps apropriado para a densidade da tela para atenuar esse problema.

Por exemplo, a imagem a seguir é um exemplo de layout e aparência problemas que podem ocorrer quando densidade Especifica recursos não forem fornecidos.

![Capturas de tela sem recursos de densidade](resources-for-varying-screens-images/06-density-not-provided.png)

Compare isso com um layout criado com recursos específicos de densidade:

![Capturas de tela com recursos específicos de densidade](resources-for-varying-screens-images/07-density-specific-resources.png)


### <a name="create-varying-density-resources-with-android-asset-studio"></a>Criar vários recursos de densidade com ativo Android Studio

A criação desses bitmaps de várias densidades pode ser um pouco entediante. Como tal, Google criou um utilitário on-line que pode reduzir alguns o tédio envolvido na criação desses bitmaps chamado o [ **Android Studio ativo**](https://romannurik.github.io/AndroidAssetStudio/).

[![Android Asset Studio](resources-for-varying-screens-images/08-android-asset-studio-sml.png)](resources-for-varying-screens-images/08-android-asset-studio.png#lightbox)

Este site ajudam com a criação de bitmaps que as quatro densidades comuns da tela de destino, fornecendo uma imagem. O Android Studio ativo será, em seguida, crie os bitmaps com algumas personalizações e, em seguida, permitir que eles sejam baixados como um arquivo zip.


## <a name="tips-for-multiple-screens"></a>Dicas para várias telas

Android é executado em um número desconcertante de dispositivos, e a combinação de tamanhos de telas e densidades de tela pode parecer uma tarefa difícil. As dicas a seguir podem ajudar a minimizar o esforço necessário para dar suporte a vários dispositivos:

- **Somente o Design e desenvolver para o qual você necessidade** &ndash; há muitos de diferentes dispositivos, mas alguns existem em fatores forma rara que podem demorar muito para projetar e desenvolver para. O [ **tamanho da tela e densidade** ](http://developer.android.com/resources/dashboard/screens.html) painel é uma página fornecida pelo Google que fornecem dados sobre divisão da matriz de densidade de tamanho/tela da tela. Essa análise fornece informações sobre como o esforço de desenvolvimento em dar suporte a telas.

- **Use DPs em vez de Pixels** -Pixels se tornar problemáticas como alterações de densidade de tela. Não codificar valores em pixel. Evite pixels em favor de dp (independente de densidade de pixels).

- **Evite** [AbsoluteLayout](https://developer.xamarin.com/api/type/Android.Widget.AbsoluteLayout/)
  **sempre que possível** &ndash; ele foi preterido no nível de API 3 (1,5 Android) e resultará em layouts frágil. Ele não deve ser usado. Em vez disso, tente usar os widgets de layout mais flexíveis, como [ **LinearLayout**](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/), [ **RelativeLayout**](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/), ou o novo [ **GridLayout**](https://developer.xamarin.com/api/type/Android.Widget.GridLayout/).

- **Escolher uma orientação de layout como padrão** &ndash; por exemplo, em vez de fornecer recursos alternativos **layout Terra** e **layout porta**, coloque os recursos para Paisagem no **layout**e os recursos de retrato em **layout porta**.

- **Use LayoutParams para altura e largura** - ao definir os elementos de interface do usuário em um arquivo de layout XML, um aplicativo do Android usando o **wrap_content** e **fill_parent** valores terão sucesso mais Certifique-se de uma aparência correta em dispositivos que o uso de unidades independentes de pixel ou densidade. Esses valores de dimensão fazer com que o Android escala bitmap recursos conforme apropriado. Por esse mesmo motivo, unidades independentes de densidade são mais adequadas para quando especificando as margens e preenchimento de elementos de interface do usuário.


## <a name="testing-multiple-screens"></a>Testar várias telas

Um aplicativo do Android precisa ser testado em relação a todas as configurações que serão compatíveis. O ideal é dispositivos devem ser testados nos próprios dispositivos, mas em muitos casos isso não é possível ou prático.
Nesse caso, o uso do emulador e a instalação de dispositivos Virtual Android para cada configuração de dispositivo será útil.

O Android SDK fornece algumas emulador capas podem ser usadas para criar do AVD replicará o tamanho, a densidade e a resolução de vários dispositivos.
Da mesma forma, muitos dos fornecedores de hardware fornecem capas para seus dispositivos.

Outra opção é usar os serviços do serviço de teste.
Esses serviços executará um APK, executá-lo em vários dispositivos diferentes e, em seguida, fornecer comentários como o aplicativo funcionou.
