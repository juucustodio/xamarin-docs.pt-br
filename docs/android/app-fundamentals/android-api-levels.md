---
title: "Noções básicas sobre níveis de API do Android"
description: "Xamarin tem várias configurações de nível de API do Android que determinam a compatibilidade do aplicativo com várias versões do Android. Este guia explica o que significam que essas configurações, como configurá-los e o efeito que eles têm em seu aplicativo em tempo de execução."
ms.topic: article
ms.prod: xamarin
ms.assetid: 58CB7B34-3140-4BEB-BE2E-209928C1878C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 907af0948e9d081f05cc201c49f94629a513c935
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="understanding-android-api-levels"></a>Noções básicas sobre níveis de API do Android

_Xamarin tem várias configurações de nível de API do Android que determinam a compatibilidade do aplicativo com várias versões do Android. Este guia explica o que significam que essas configurações, como configurá-los e o efeito que eles têm em seu aplicativo em tempo de execução._


## <a name="quick-start"></a>Início rápido

Xamarin expõe três configurações de nível de projeto de API do Android:

-   [Estrutura de destino](#framework) &ndash; Especifica a estrutura para usar na criação de seu aplicativo. Esse nível de API é usada em *compilar* tempo por xamarin.

-   [Versão mínima do Android](#minimum) &ndash; Especifica a versão do Android mais antiga que você deseja que seu aplicativo para dar suporte. Esse nível de API é usada em *executar* tempo pelo Android.

-   [Destino de versão do Android](#target) &ndash; Especifica a versão do Android que é seu aplicativo deve ser executados em. Esse nível de API é usada em *executar* tempo pelo Android.

Antes de configurar um nível de API para seu projeto, você deve instalar os componentes de plataforma SDK para esse nível de API. Para obter mais informações sobre como baixar e instalar os componentes do SDK do Android, consulte [configuração do Android SDK](~/android/get-started/installation/android-sdk.md).

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Normalmente, todos os três níveis de API do xamarin são definidos com o mesmo valor. Sobre o **aplicativo** , defina **compilar usando a versão do Android (estrutura de destino)** para a versão mais recente da API estável (ou, no mínimo, para a versão do Android que tem todos os recursos necessários).
Captura de tela a seguir, a estrutura de destino é definida como **7.1 Android (API nível 25 - Nougat)**:

[![Destino padrão é versão do Framework compilação usando a versão do Android](android-api-levels-images/vs-defaults-sml.png)](android-api-levels-images/vs-defaults.png#lightbox)

No **manifesto do Android** , defina a versão do Android mínimo **Use compilar com SDK versão** e defina a versão de destino Android para o mesmo valor que a versão do Framework de destino (no exemplo a seguir captura de tela, a estrutura de destino Android é definida como **7.1 Android (Nougat)**):

[![Versões de mínimo e o destino Android definido para a versão do Framework de destino](android-api-levels-images/vs-manifest-defaults-sml.png)](android-api-levels-images/vs-manifest-defaults.png#lightbox)

Se você quiser manter a compatibilidade com versões anteriores com uma versão anterior do Android, defina **mínimo Android versão destino** para a versão mais antiga do Android que você deseja que seu aplicativo para dar suporte. (Observe que 14 de nível de API é o nível de API mínimo necessário para [serviços do Google Play e suporte de Firebase](https://android-developers.googleblog.com/2016/11/google-play-services-and-firebase-for-android-will-support-api-level-14-at-minimum.html).) A configuração de exemplo a seguir oferece suporte a versões Android na 14 de nível de API por meio do nível de API 25:

[![Compilar usando o nível de API 25 Nougat, versão do Android do mínimo definido como 14 de nível de API](android-api-levels-images/vs-minimum-sml.png)](android-api-levels-images/vs-minimum.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Normalmente, todos os três níveis de API do xamarin são definidos com o mesmo valor. Definir **framework de destino** para a versão mais recente da API estável (ou, no mínimo, para a versão do Android que tem todos os recursos necessários). Para definir o **framework de destino**, navegue até **Build > geral** no **opções de projeto**. Captura de tela a seguir, a estrutura de destino é definida como **usar a mais recente plataforma instalada (8.0)**:

[![Estrutura de destino padrão é a plataforma de uso mais recente instalado](android-api-levels-images/xs-default-target-sml.png)](android-api-levels-images/xs-default-target.png#lightbox)

As configurações de versão mínima e o destino Android podem ser encontradas em **Build > aplicativo Android** na **opções de projeto**. Defina a versão do Android mínimo **automático - versão do framework de destino use** e defina a versão de destino Android para o mesmo valor que a versão do Framework de destino. Captura de tela a seguir, a estrutura de destino Android está definida como **8.0 Android (API nível 26)** para corresponder à configuração do Framework de destino acima:

[![Configurando os níveis de destino e do framework nas opções de projeto](android-api-levels-images/xs-default-app-sml.png)](android-api-levels-images/xs-default-app.png#lightbox)

Se você quiser manter a compatibilidade com versões anteriores com uma versão anterior do Android, alterar **versão do Android mínimo** para a versão mais antiga do Android que você deseja que seu aplicativo para dar suporte. Observe que 14 de nível de API é o nível de API mínimo necessário para [serviços do Google Play e suporte de Firebase](https://android-developers.googleblog.com/2016/11/google-play-services-and-firebase-for-android-will-support-api-level-14-at-minimum.html).
Por exemplo, a configuração a seguir oferece suporte a versões Android mais cedo 14 de nível de API:

[![Mínimo e versões de destino definidas como automático - usar a versão do framework de destino](android-api-levels-images/xs-minimum-sml.png)](android-api-levels-images/xs-minimum.png#lightbox)

-----


Se seu aplicativo dá suporte a várias versões Android, seu código deverá incluir verificações de tempo de execução para garantir que seu aplicativo funcione com a configuração de versão mínima Android (consulte [verificações de tempo de execução para versões Android](#runtimechecks) abaixo para obter detalhes). Se você estiver consumindo ou criar uma biblioteca, consulte [níveis de API e bibliotecas](#libraries) abaixo para práticas recomendadas na API de configuração de nível de configurações de bibliotecas.



## <a name="android-versions-and-api-levels"></a>Versões do Android e níveis de API

Como a plataforma Android evolui e são lançadas novas versões Android, cada versão do Android é atribuído um identificador inteiro exclusivo, chamado de *nível API*. Portanto, cada versão do Android corresponde a um único nível de API do Android. Como os usuários instalar aplicativos em versões anteriores, bem como a mais recentes do Android, aplicativos do Android reais devem ser criados para trabalhar com vários níveis de API do Android.


### <a name="android-versions"></a>Versões do Android

Cada versão do Android passa por vários nomes:

-   A versão do Android, como **7.1 Android**
-   Um nome de código, como _Nougat_
-   Nível de uma API correspondente, como **25 de nível de API**

Um nome de código Android pode corresponder a várias versões e níveis de API (como visto na lista abaixo), mas cada versão do Android corresponde exatamente um nível de API.

Além disso, define o xamarin *códigos de versão de compilação* que mapeiam para os níveis de API do Android atualmente conhecidos. A lista a seguir pode ajudá-lo a converter entre o nível de API, versão do Android, nome de código e xamarin código da versão de compilação.

-   **27 de API (Android 8.1)** &ndash; _Oreos_, lançado em dezembro de 2017. Compilar o código da versão `Android.OS.BuildVersionCodes.OMr1`

-   **26 de API (Android 8.0)** &ndash; _Oreos_, lançada em agosto de 2017. Compilar o código da versão `Android.OS.BuildVersionCodes.O`

-   **25 de API (Android 7.1)** &ndash; _Nougat_, lançado em dezembro de 2016. Compilar o código da versão `Android.OS.BuildVersionCodes.NMr1`

-   **24 de API (Android 7.0)** &ndash; _Nougat_, lançada em agosto de 2016. Compilar o código da versão `Android.OS.BuildVersionCodes.N`

-   **23 de API (Android 6.0)** &ndash; _Marshmallow_, lançada em agosto de 2015. Compilar o código da versão `Android.OS.BuildVersionCodes.M`

-   **22 de API (Android 5.1)** &ndash; _pirulito_, lançado em março de 2015. Compilar o código da versão `Android.OS.BuildVersionCodes.LollipopMr1`

-   **21 de API (Android 5.0)** &ndash; _pirulito_, lançada em novembro de 2014. Compilar o código da versão `Android.OS.BuildVersionCodes.Lollipop`

-   **20 de API (Android 4.4W)** &ndash; _Kitkat inspeção_, lançada em junho de 2014. Compilar o código da versão `Android.OS.BuildVersionCodes.KitKatWatch`

-   **19 de API (Android 4.4)** &ndash; _Kitkat_, lançada em outubro de 2013. Compilar o código da versão `Android.OS.BuildVersionCodes.KitKat`

-   **18 de API (Android 4.3)** &ndash; _Jelly Bean_, lançada em julho de 2013. Compilar o código da versão `Android.OS.BuildVersionCodes.JellyBeanMr2`

-   **17 de API (Android 4.2-4.2.2)** &ndash; _Jelly Bean_, lançada em novembro de 2012. Compilar o código da versão `Android.OS.BuildVersionCodes.JellyBeanMr1`

-   **16 de API (Android 4.1-4.1.1)** &ndash; _Jelly Bean_, lançada em junho de 2012. Compilar o código da versão `Android.OS.BuildVersionCodes.JellyBean`

-   **15 de API (Android 4.0.3-4.0.4)** &ndash; _sorvete Sandwich_, lançado em dezembro de 2011. Compilar o código da versão `Android.OS.BuildVersionCodes.IceCreamSandwichMr1`

-   **API 14 (Android 4.0-4.0.2)** &ndash; _sorvete Sandwich_, lançada em outubro de 2011. Compilar o código da versão `Android.OS.BuildVersionCodes.IceCreamSandwich`

-   **13 de API (Android 3.2)** &ndash; _Honeycomb_, lançada em junho de 2011. Compilar o código da versão `Android.OS.BuildVersionCodes.HoneyCombMr2`

-   **12 de API (Android 3.1)** &ndash; _Honeycomb_, lançado em maio de 2011. Compilar o código da versão `Android.OS.BuildVersionCodes.HoneyCombMr1`

-   **11 de API (Android 3.0. x)** &ndash; _Honeycomb_, lançada em fevereiro de 2011. Compilar o código da versão `Android.OS.BuildVersionCodes.HoneyComb`

-   **10 de API (Android 2.3.3-2.3.4)** &ndash; _biscoito_, lançada em fevereiro de 2011. Compilar o código da versão `Android.OS.BuildVersionCodes.GingerBreadMr1`

-   **9 de API (Android 2.3-2.3.2)** &ndash; _biscoito_, lançada em novembro de 2010. Compilar o código da versão `Android.OS.BuildVersionCodes.GingerBread`

-   **8 de API (Android 2.2)** &ndash; _Froyo_, lançada em junho de 2010. Compilar o código da versão `Android.OS.BuildVersionCodes.Froyo`

-   **7 de API (Android 2.1. x)** &ndash; _Eclair_, lançado em janeiro de 2010. Compilar o código da versão `Android.OS.BuildVersionCodes.EclairMr1`

-   **6 de API (Android 2.0.1)** &ndash; _Eclair_, lançado em dezembro de 2009. Compilar o código da versão `Android.OS.BuildVersionCodes.Eclair01`

-   **5 de API (2.0 Android)** &ndash; _Eclair_, lançada em novembro de 2009. Compilar o código da versão `Android.OS.BuildVersionCodes.Eclair`

-   **4 de API (Android 1.6)** &ndash; _rosca_, lançada em setembro de 2009. Compilar o código da versão `Android.OS.BuildVersionCodes.Donut`

-   **3 de API (Android 1.5)** &ndash; _Cupcake_, lançado em maio de 2009. Compilar o código da versão `Android.OS.BuildVersionCodes.Cupcake`

-   **API 2 (Android 1.1)** &ndash; _Base_, lançada em fevereiro de 2009. Compilar o código da versão `Android.OS.BuildVersionCodes.Base11`

-   **API 1 (Android 1.0)** &ndash; _Base_, lançada em outubro de 2008. Compilar o código da versão `Android.OS.BuildVersionCodes.Base`


Como essa lista indica, Android são lançadas novas versões frequentemente &ndash; , às vezes, várias versões por ano. Como resultado, o universo de dispositivos com Android que pode executar seu aplicativo inclui uma ampla variedade de versões Android mais antigas e mais recentes. Como você pode garantir que o aplicativo será executado consistente e confiável em muitas versões diferentes do Android? Níveis de API do Android podem ajudá-lo a gerenciar esse problema.


### <a name="android-api-levels"></a>Níveis de API do Android

Cada dispositivo Android é executada em exatamente *um* nível API &ndash; esse nível de API é garantido como exclusivo por versão de plataforma Android. O nível de API identifica com precisão a versão do conjunto de API que o seu aplicativo pode chamar; Ele identifica a combinação de elementos de manifesto, permissões, etc. código em relação a como um desenvolvedor. Sistema do Android dos níveis de API ajuda Android determinar se um aplicativo é compatível com uma imagem do sistema Android antes de instalar o aplicativo em um dispositivo.

Quando um aplicativo é criado, ele contém as seguintes informações de nível de API:

-   O *destino* nível de API do Android que o aplicativo é compilado para executar.

-   O *mínimo* nível de API do Android que um dispositivo Android deve ter para executar seu aplicativo. 

Essas configurações são usadas para garantir que a funcionalidade necessária para executar o aplicativo corretamente está disponível no dispositivo Android no momento da instalação. Caso contrário, o aplicativo está bloqueado em execução no dispositivo. Por exemplo, se o nível de API de um dispositivo Android é menor do que o nível mínimo de API que você especificar para seu aplicativo, o dispositivo Android impedirá o usuário instalando o aplicativo.


## <a name="project-api-level-settings"></a>Configurações de nível de API do projeto

As seções a seguir explicam como usar o Gerenciador de SDK para preparar o ambiente de desenvolvimento para os níveis de API que você deseja como destino, seguido de explicações detalhadas sobre como configurar *Framework de destino*, *mínimo Versão do Android*, e *versão destino Android* configurações no xamarin.


### <a name="android-sdk-platforms"></a>Plataformas SDK do Android

Antes de selecionar um nível de API mínima ou de destino em xamarin, você deve instalar a versão da plataforma SDK do Android que corresponde a esse nível de API. A faixa de opções disponíveis para a estrutura de destino, versão do Android mínimo e versão do Android do destino é limitada para as versões de intervalo do SDK do Android que você instalou. Você pode usar o Gerenciador de SDK para verificar se as versões necessárias do SDK do Android estão instaladas, e você pode usá-lo para adicionar quaisquer novos níveis de API que você precisa para seu aplicativo. Se você não estiver familiarizado com como instalar os níveis de API, consulte [configuração do Android SDK](~/android/get-started/installation/android-sdk.md).

<a name="framework" />

### <a name="target-framework"></a>Estrutura de Destino

O *Framework de destino* (também conhecido como `compileSdkVersion`) é a versão de específico do Android framework (API nível) que seu aplicativo é compilado para em tempo de compilação. Essa configuração especifica quais as APIs que seu aplicativo *espera* para usar quando ele é executado, mas não tem nenhum efeito nas quais APIs estão realmente disponíveis para seu aplicativo quando ele está instalado. Como resultado, a alteração da configuração da estrutura de destino não altera o comportamento de tempo de execução.

A estrutura de destino identifica quais versões de biblioteca que seu aplicativo está vinculado em relação a &ndash; determina quais APIs que você pode usar em seu aplicativo. Por exemplo, se você quiser usar o [NotificationBuilder.SetCategory](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetCategory/p/System.String/) método foi introduzido no pirulito do Android 5.0, você deve definir a estrutura de destino para **21 de nível de API (pirulito)** ou posterior. Se você definir o Framework de destino do projeto para uma API nível como **19 de nível de API (KitKat)** e tentar chamar o `SetCategory` método no seu código, você receberá um erro de compilação.

É recomendável que você sempre compilar com o *mais recente* versão do Framework de destino disponível. Isso é útil mensagens de aviso para quaisquer APIs obsoletas que podem ser chamados por seu código. Usando a versão mais recente do Framework de destino é especialmente importante quando você usar as versões mais recentes de biblioteca de suporte &ndash; cada biblioteca espera que seu aplicativo para ser compilada no nível de API mínima da biblioteca de suporte ou maior. 

> [!NOTE]
> A partir de agosto de 2018, o Console do Google reproduzir exigirá que o nível de API 26 (Android 8.0) de destino novos aplicativos ou superior.
Aplicativos existentes precisarão 26 de nível de API ou superior novembro de 2018 a partir de destino. Para obter mais informações, consulte [melhorando o desempenho no Google Play para anos ficar e segurança de aplicativo](https://android-developers.googleblog.com/2017/12/improving-app-security-and-performance.html).


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para acessar a configuração da estrutura de destino no Visual Studio, abra as propriedades do projeto no **Solution Explorer** e selecione o **aplicativo** página:

[![Página de propriedades do projeto de aplicativo](android-api-levels-images/vs-target-framework-sml.png)](android-api-levels-images/vs-target-framework.png#lightbox)

Definir a estrutura de destino, selecionando um nível de API no menu suspenso em **compilar usando a versão do Android** como mostrado acima.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Para acessar a configuração da estrutura de destino no Visual Studio para Mac, clique no nome do projeto e selecione **opções**; este abre o **opções de projeto** caixa de diálogo. Na caixa de diálogo, navegue até **Build > geral** conforme mostrado aqui:

[![Criar uma seção geral da página de opções de projeto](android-api-levels-images/xs-target-framework-sml.png)](android-api-levels-images/xs-target-framework.png#lightbox)

Definir a estrutura de destino, selecionando um nível de API no menu suspenso à direita do **framework de destino** como mostrado acima.

-----


<a name="minimum" />

### <a name="minimum-android-version"></a>Versão mínima do Android

O *versão do Android mínimo* (também conhecido como `minSdkVersion`) é a versão mais antiga do sistema operacional Android (ou seja, o menor nível de API) que pode instalar e executar o aplicativo. Por padrão, um aplicativo só pode ser instalado em dispositivos corresponder à configuração do Framework de destino ou superior; Se a configuração de versão mínima Android for *inferior* que a configuração da estrutura de destino, o aplicativo também pode executar em versões anteriores do Android. Por exemplo, se você definir a estrutura de destino como **7.1 Android (Nougat)** e defina a versão do Android mínimo para **Android 4.0.3 (sorvete Sandwich)**, seu aplicativo pode ser instalado em qualquer plataforma de API nível 15 para o nível de API 25, inclusive.

Embora o seu aplicativo pode criar e instalar esse intervalo de plataformas com êxito, isso não garante-se com êxito *executar* em todas essas plataformas. Por exemplo, se seu aplicativo está instalado em **Android 5.0 (pirulito)** e seu código chama uma API que está disponível apenas no **7.1 Android (Nougat)** e mais recente, o aplicativo obterá um erro de tempo de execução e possivelmente falhar. Portanto, seu código deve garantir &ndash; em tempo de execução &ndash; que ele chama apenas essas APIs que são suportados por que ele está em execução no dispositivo Android. Em outras palavras, seu código deve incluir verificações de tempo de execução explícito para garantir que seu aplicativo usa APIs do mais recente somente em dispositivos que são recentes o suficiente para dar suporte a elas.
[Verificações de tempo de execução para versões Android](#runtimechecks), mais adiante neste guia, explica como adicionar essas verificações de tempo de execução ao seu código.


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para acessar a configuração de versão mínima Android no Visual Studio, abra as propriedades do projeto no **Solution Explorer** e selecione o **manifesto do Android** página. No menu suspenso em **versão do Android mínimo** você pode selecionar a versão do Android mínimo para o seu aplicativo:

[![Android mínimo para a opção de destino definida para compilar usando a versão do SDK](android-api-levels-images/vs-minimum-version-sml.png)](android-api-levels-images/vs-minimum-version.png#lightbox)

Se você selecionar **Use compilar com SDK versão**, a versão do Android mínimo será o mesmo que a configuração da estrutura de destino.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Para acessar a configuração da estrutura de destino no Visual Studio para Mac, clique no nome do projeto e selecione **opções**; este abre o **opções de projeto** caixa de diálogo. Navegue até **Build > aplicativo Android**.
Usando o menu suspenso à direita do **versão do Android mínimo**, você pode definir a versão do Android mínimo para seu aplicativo:

[![Versão mínima do Android definido como automático - versão do framework de destino de uso](android-api-levels-images/xs-minimum-version-sml.png)](android-api-levels-images/xs-minimum-version.png#lightbox)

Se você selecionar **automático &ndash; usar a versão do framework de destino**, a versão do Android mínimo será o mesmo que a configuração da estrutura de destino.

-----


<a name="target" />

### <a name="target-android-version"></a>Versão destino do Android

O *versão destino do Android* (também conhecido como `targetSdkVersion`) é a API de nível do dispositivo Android onde o aplicativo espera para executar. Android usa essa configuração para determinar se deseja habilitar todos os comportamentos compatibilidade &ndash; Isso garante que o aplicativo continua a funcionar da maneira esperada. Android usa a configuração da versão Android do destino do seu aplicativo para descobrir quais alterações de comportamento podem ser aplicadas ao seu aplicativo sem dividi-lo (Isso é como o Android fornece compatibilidade com versões posteriores).

A estrutura de destino e a versão de destino Android, embora tenha nomes similares, não são a mesma coisa. A configuração da estrutura de destino transmite informações nível API de destino para xamarin para uso em *tempo de compilação*, enquanto a versão de destino Android transmite informações nível API de destino para o Android para uso em  *tempo de execução* (quando o aplicativo é instalado e em execução em um dispositivo).

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para acessar essa configuração no Visual Studio, abra as propriedades do projeto no **Solution Explorer** e selecione o **manifesto do Android** página. No menu suspenso em **versão destino Android** você pode selecionar a versão de destino Android para o seu aplicativo:

[![Versão do Android destino definido para compilar usando a versão do SDK](android-api-levels-images/vs-target-version-sml.png)](android-api-levels-images/vs-target-version.png#lightbox)

É recomendável que você defina explicitamente a versão do Android do destino para a versão mais recente do Android que você pode usar para testar seu aplicativo. Idealmente, ele deve ser definido para a versão mais recente do SDK do Android &ndash; Isso permite que você use as novas APIs antes de realizar as alterações de comportamento. Para a maioria dos desenvolvedores, nós *não* recomendável definir a versão de destino Android com **Use compilar com SDK versão**.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Para acessar a configuração da estrutura de destino no Visual Studio para Mac, clique no nome do projeto e selecione **opções**; este abre o **opções de projeto** caixa de diálogo. Navegue até **Build > aplicativo Android**.
Usando o menu suspenso à direita do **versão destino Android**, você pode definir a versão de destino Android para o seu aplicativo:

[![Versão do Android destino definido como automático - versão do framework de destino de uso](android-api-levels-images/xs-target-version-sml.png)](android-api-levels-images/xs-target-version.png#lightbox)

É recomendável que você defina explicitamente a versão do Android do destino para a versão mais recente do Android que você pode usar para testar seu aplicativo. Idealmente, ele deve ser definido para a versão de SDK do Android mais recente disponível &ndash; Isso permite que você use as novas APIs antes de realizar as alterações de comportamento. Para a maioria dos desenvolvedores, não é recomendável definir a versão de destino Android com **automático - versão do framework de destino use**.

-----

Em geral, a versão do Android destino deve ser limitada a versão mínima do Android e a estrutura de destino. Ou seja:

**Versão mínima do Android < = versão destino do Android < = Framework de destino**

Para obter mais informações sobre os níveis SDK, consulte o desenvolvedor Android [usa sdk](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html) documentação.


<a name="runtimechecks" />

## <a name="runtime-checks-for-android-versions"></a>Verificações de tempo de execução para versões do Android

Como cada nova versão do Android for lançada, a estrutura de API é atualizada para oferecer novos ou funcionalidade de substituição. Com poucas exceções, funcionalidade da API de versões anteriores do Android é transmitida em versões mais recentes de Android sem modificações. Como resultado, se seu aplicativo é executado em um determinado nível de API do Android, ele geralmente será capaz de executar em um nível de API do Android posterior sem modificações. Mas e se você também deseja executar seu aplicativo em versões anteriores do Android?

Se você selecionar uma versão mínima Android *inferior* que a configuração da estrutura de destino, algumas APIs podem não estar disponíveis para seu aplicativo em tempo de execução. No entanto, seu aplicativo ainda pode executar em um dispositivo anterior, mas com funcionalidade reduzida. Para cada API não está disponível em plataformas Android correspondente para a configuração de versão mínima Android, seu código deve verificar explicitamente o valor da `Android.OS.Build.VERSION.SdkInt` propriedade para determinar o nível de API da plataforma que o aplicativo está em execução. Se o nível de API é *inferior* que a versão do Android mínimo que oferece suporte a API que você deseja chamar, em seguida, seu código deve encontrar uma forma de funcionar corretamente sem fazer essa chamada à API.

Por exemplo, vamos supor que queremos usar o [NotificationBuilder.SetCategory](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetCategory/p/System.String/) método para categorizar uma notificação quando executado em **pirulito do Android 5.0** (e posterior), mas ainda desejamos nosso aplicativo Executar como em versões anteriores do Android **Bean de Jelly Android 4.1** (onde `SetCategory` não está disponível). Consultando a tabela de versão do Android no início deste guia, podemos ver que o código da versão de compilação para **pirulito do Android 5.0** é `Android.OS.BuildVersionCodes.Lollipop`. Para dar suporte a versões mais antigas do Android onde `SetCategory` é não está disponível, nosso código pode detectar o nível de API em tempo de execução e chamar condicionalmente `SetCategory` somente quando o nível de API é maior que ou igual ao código de versão de compilação de pirulito:

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop)
{
    builder.SetCategory(Notification.CategoryEmail);
}
```

Neste exemplo, a estrutura de destino do nosso aplicativo é definida como **Android 5.0 (API nível 21)** e sua versão do Android mínimo é definido como **Android 4.1 (API nível 16)**. Porque `SetCategory` está disponível no nível de API `Android.OS.BuildVersionCodes.Lollipop` e versões posteriores, esse código de exemplo chamará `SetCategory` somente quando ele for realmente disponível &ndash; ele irá *não* tentar chamar `SetCategory` quando a API nível é 16, 17, 18, 19 ou 20. A funcionalidade é reduzida nessas versões anteriores Android somente na medida em que as notificações não são classificadas corretamente (porque eles não são categorizados por tipo), embora as notificações ainda estão publicadas para alertar o usuário. Nosso aplicativo ainda funciona, mas sua funcionalidade é um pouco reduzida.

Em geral, a verificação de versão de compilação ajuda a seu código decidir em tempo de execução entre fazer algo a nova maneira versus a forma antiga. Por exemplo:

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop)
{
    // Do things the Lollipop way
}
else
{
    // Do things the pre-Lollipop way
}
```

Não há nenhuma regra rápida e simple que explica como reduzir ou modificar a funcionalidade do aplicativo quando ele é executado em versões mais antigas de Android que estão faltando uma ou mais APIs. Em alguns casos (como no `SetCategory` exemplo acima), basta simplesmente omitir a chamada à API quando ele não está disponível. Entretanto, em outros casos, talvez seja necessário implementar a funcionalidade alternativa quando `Android.OS.Build.VERSION.SdkInt` é detectado para ser menor do que a API de nível de que seu aplicativo precisa apresentar seu ideal.

<a name="libraries" />

## <a name="api-levels-and-libraries"></a>Níveis de API e bibliotecas

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Quando você cria um projeto de biblioteca do xamarin (como uma biblioteca de classe ou de uma biblioteca de associações), você pode configurar apenas a definição da estrutura de destino &ndash; a versão do Android mínimo e as configurações de versão de destino Android não estão disponíveis. Isso ocorre porque não há nenhum **manifesto do Android** página:

[![Apenas a compilação usando a opção de versão do Android está disponível](android-api-levels-images/vs-library-options-sml.png)](android-api-levels-images/vs-library-options.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Quando você cria um projeto de biblioteca de xamarin, não há nenhum **aplicativo Android** página onde você pode configurar a versão do Android mínimo e a versão de destino Android &ndash; a versão do Android mínimo e o destino Configurações de versão do Android não estão disponíveis.
Isso ocorre porque não há nenhum **Build > aplicativo Android** página):

[![Página geral sem opções de versão mínima e o destino de compilação](android-api-levels-images/xs-library-options-sml.png)](android-api-levels-images/xs-library-options.png#lightbox)

-----

A versão do Android mínimo e configurações de versão de destino Android não estão disponíveis porque a biblioteca resultante não é um aplicativo autônomo &ndash; biblioteca pode ser executada em qualquer versão do Android, dependendo do aplicativo que é empacotado com. Você pode especificar como a biblioteca deve ser *compilado*, mas você não pode prever qual plataforma de nível de API a biblioteca será executada. Com isso em mente, práticas recomendadas a seguir devem ser observadas ao consumo ou criar bibliotecas:

-   **Ao consumir uma biblioteca Android** &ndash; se está consumindo uma biblioteca Android em seu aplicativo, certifique-se de definir a configuração para uma API nível que é de estrutura de destino do aplicativo *pelo menos mais alto* o destino Configuração do Framework da biblioteca.

-   **Ao criar uma biblioteca Android** &ndash; se você estiver criando uma biblioteca Android para uso por outros aplicativos, certifique-se de definir a configuração de sua estrutura de destino para o nível mínimo de API que ele precisa para compilar.

Essas práticas recomendadas são recomendadas para ajudar a evitar a situação em que uma biblioteca de tenta chamar uma API que não está disponível em tempo de execução (o que pode fazer com que o aplicativo falhar). Se você for um desenvolvedor de biblioteca, você deve tentar restringir o uso de chamadas de API para um subconjunto pequeno e bem conhecida da área de superfície total da API. Fazer isso ajuda a garantir que sua biblioteca pode ser usada com segurança em um maior versões de intervalo do Android.


## <a name="summary"></a>Resumo

Este guia explicou como níveis de API do Android são usados para gerenciar a compatibilidade do aplicativo em diferentes versões do Android. Ele fornecido etapas detalhadas para configurar o xamarin *Framework de destino*, *versão do Android mínimo*, e *versão destino Android* configurações do projeto. Ele fornecidas instruções para usar o Gerenciador de SDK do Android para instalar os pacotes do SDK, incluídos exemplos de como escrever código para lidar com diferentes níveis de API em tempo de execução e explicou como gerenciar níveis de API ao criar ou consumir bibliotecas Android. Também forneceu uma lista abrangente que relaciona os níveis de API para números de versão do Android (por exemplo, o Android 4.4), nomes de versão do Android (como Kitkat) e códigos de versão de build xamarin.


## <a name="related-links"></a>Links relacionados

- [Configuração do SDK do Android](~/android/get-started/installation/android-sdk.md)
- [Ferramentas do SDK CLI alterações](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [Escolhendo seu compileSdkVersion, minSdkVersion e targetSdkVersion](https://medium.com/google-developers/picking-your-compilesdkversion-minsdkversion-targetsdkversion-a098a0341ebd)
- [O que é o nível de API?](http://developer.android.com/guide/topics/manifest/uses-sdk-element.html#ApiLevels)
- [Codinome, marcas e números de compilação](https://source.android.com/source/build-numbers)
