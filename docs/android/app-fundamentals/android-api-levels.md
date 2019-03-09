---
title: Noções básicas sobre níveis de API do Android
description: Xamarin. Android tem várias configurações de nível de API do Android que se determinam a compatibilidade do seu aplicativo com várias versões do Android. Este guia explica o que significa que essas configurações, como configurá-los e o efeito que eles têm em seu aplicativo em tempo de execução.
ms.prod: xamarin
ms.assetid: 58CB7B34-3140-4BEB-BE2E-209928C1878C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2018
ms.openlocfilehash: 8690be7551046a26339f58029da5f3f58e18cd15
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57672580"
---
# <a name="understanding-android-api-levels"></a>Noções básicas sobre níveis da API do Android

_Xamarin. Android tem várias configurações de nível de API do Android que se determinam a compatibilidade do seu aplicativo com várias versões do Android. Este guia explica o que significa que essas configurações, como configurá-los e o efeito que eles têm em seu aplicativo em tempo de execução._


## <a name="quick-start"></a>Início rápido

Xamarin. Android expõe três configurações de projeto de nível de API do Android:

-   [Estrutura de destino](#framework) &ndash; Especifica qual estrutura para usar na criação de seu aplicativo. Esse nível de API é usado no *compilar* tempo pelo xamarin. Android.

-   [Versão mínima do Android](#minimum) &ndash; Especifica a versão mais antiga do Android que você deseja que seu aplicativo para dar suporte. Esse nível de API é usado no *executar* tempo pelo Android.

-   [Versão do Android de destino](#target) &ndash; Especifica a versão do Android que seu aplicativo deve ser executados em. Esse nível de API é usado no *executar* tempo pelo Android.

Antes de configurar um nível de API para seu projeto, você deve instalar os componentes da plataforma SDK para esse nível de API. Para obter mais informações sobre como baixar e instalar os componentes do SDK do Android, consulte [instalação do SDK do Android](~/android/get-started/installation/android-sdk.md).

> [!NOTE]
> A partir de agosto de 2018, o Console do Google Play exige que novos aplicativos de nível de API 26 (Android 8.0) de destino ou superior.
Os aplicativos existentes deverão ter como destino o nível de API 26 ou superior início em novembro de 2018. Para obter mais informações, consulte [aprimorando a segurança de aplicativo e desempenho no Google Play para anos ficar](https://android-developers.googleblog.com/2017/12/improving-app-security-and-performance.html).

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Normalmente, todos os três níveis de API do xamarin. Android são definidos com o mesmo valor. Sobre o **Application** , defina **compilar usando a versão do Android (estrutura de destino)** para a última versão estável do API (ou, no mínimo, para a versão do Android que tem todos os recursos necessários).
Captura de tela a seguir, a estrutura de destino é definida como **Android 7.1 (API nível 25 - Nougat)**:

[![Padrões de versão do Framework para compilar usando a versão do Android de destino](android-api-levels-images/vs-defaults-sml.png)](android-api-levels-images/vs-defaults.png#lightbox)

Sobre o **manifesto do Android** , defina a versão mínima do Android **Use compilar usando a versão do SDK** e defina a versão de destino do Android para o mesmo valor que a versão de estrutura de destino (no exemplo a seguir captura de tela, a estrutura do Android de destino é definida como **Android 7.1 (Nougat)**):

[![Versões de mínimo e o destino do Android definida para a versão do Framework de destino](android-api-levels-images/vs-manifest-defaults-sml.png)](android-api-levels-images/vs-manifest-defaults.png#lightbox)

Se você quiser manter a compatibilidade com versões anteriores com uma versão anterior do Android, defina **versão mínima do Android para o destino** para a versão mais antiga do Android que você deseja que seu aplicativo para dar suporte. (Observe que a API nível 14 é o nível de API mínimo necessário para [serviços do Google Play e o suporte do Firebase](https://android-developers.googleblog.com/2016/11/google-play-services-and-firebase-for-android-will-support-api-level-14-at-minimum.html).) A configuração de exemplo a seguir dá suporte a versões do API nível 14 do Android por meio do nível 25 da API:

[![Compilar usando a API nível 25 Nougat, versão mínima do Android definida como API nível 14](android-api-levels-images/vs-minimum-sml.png)](android-api-levels-images/vs-minimum.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Normalmente, todos os três níveis de API do xamarin. Android são definidos com o mesmo valor. Definir **estrutura de destino** para a última versão estável do API (ou, no mínimo, para a versão do Android que tem todos os recursos necessários). Para definir a **estrutura de destino**, navegue até **Build > geral** no **opções de projeto**. Captura de tela a seguir, a estrutura de destino é definida como **usar a mais recente plataforma instalada (8.0)**:

[![Estrutura de destino padrão é usar a plataforma mais recente instalada](android-api-levels-images/xs-default-target-sml.png)](android-api-levels-images/xs-default-target.png#lightbox)

As configurações de versão mínimo e o destino do Android podem ser encontradas em **compilar > aplicativo Android** na **opções de projeto**. Defina a versão mínima do Android para **automático - use a versão do target framework** e defina a versão de destino do Android para o mesmo valor que a versão do Framework de destino. Captura de tela a seguir, a estrutura do Android de destino é definida como **Android 8.0 (API nível 26)** para corresponder à configuração de estrutura de destino acima:

[![Definindo níveis de destino e o framework nas opções do projeto](android-api-levels-images/xs-default-app-sml.png)](android-api-levels-images/xs-default-app.png#lightbox)

Se você quiser manter a compatibilidade com versões anteriores com uma versão anterior do Android, altere **versão mínima do Android** para a versão mais antiga do Android que você deseja que seu aplicativo para dar suporte. Observe que a API nível 14 é o nível de API mínimo necessário para [serviços do Google Play e o suporte do Firebase](https://android-developers.googleblog.com/2016/11/google-play-services-and-firebase-for-android-will-support-api-level-14-at-minimum.html).
Por exemplo, a configuração a seguir dá suporte a versões do Android mais cedo API nível 14:

[![Mínimo e versões de destino definidas como automático - use a versão do framework de destino](android-api-levels-images/xs-minimum-sml.png)](android-api-levels-images/xs-minimum.png#lightbox)

-----


Se seu aplicativo dá suporte a várias versões do Android, seu código deve incluir verificações em tempo de execução para garantir que seu aplicativo funciona com a configuração de versão mínima do Android (consulte [verificações de tempo de execução para versões do Android](#runtimechecks) abaixo para obter detalhes). Se você estiver consumindo ou criando uma biblioteca, consulte [níveis de API e bibliotecas](#libraries) abaixo para práticas recomendadas na API de configuração de nível configurações para bibliotecas.



## <a name="android-versions-and-api-levels"></a>Versões do Android e níveis de API

Como a plataforma Android evolui e são lançadas novas versões de Android, cada versão do Android é atribuído um identificador inteiro exclusivo, chamado de *nível de API*. Portanto, cada versão do Android corresponde a um único nível de API do Android. Porque os usuários instalarem aplicativos em versões mais antigas, bem como mais recentes do Android, os aplicativos Android reais devem ser criados para trabalhar com vários níveis de API do Android.


### <a name="android-versions"></a>Versões do Android

Cada versão do Android passa por vários nomes:

-   A versão do Android, tais como **9.0 Android**
-   Um código (ou sobremesas) nome, como _pizza_
-   Nível de uma API correspondente, como **28 de nível de API**

Um nome de código Android pode corresponder a várias versões e níveis de API (como visto na tabela a seguir), mas cada versão do Android corresponde a exatamente um nível de API.

Além disso, o xamarin. Android define *compilar códigos de versão* que são mapeados para os níveis de API do Android conhecidos no momento. A tabela a seguir pode ajudá-lo a converter entre o nível da API, a versão do Android, nome do código e código de versão de build do xamarin. Android (códigos de versão de build são definidos na `Android.OS` namespace):

[!include[](~/android/includes/api-levels.md)]

Como esta tabela indica, Android são lançadas novas versões com frequência &ndash; , às vezes, mais de uma versão por ano. Como resultado, o universo de dispositivos Android que pode executar seu aplicativo inclui uma ampla variedade de versões mais antigas e mais recentes do Android. Como você pode garantir que seu aplicativo será executado consistente e confiável em muitas versões diferentes do Android? Níveis de API do Android podem ajudá-lo a gerenciar esse problema.


### <a name="android-api-levels"></a>Níveis de API do Android

Cada dispositivo Android é executado no exatamente *uma* nível de API &ndash; esse nível de API é garantido como sendo exclusivo por versão da plataforma Android. O nível da API identifica com precisão a versão do conjunto de API que seu aplicativo pode chamar; Ele identifica a combinação de elementos do manifesto, permissões, etc. que você codifique em relação a como um desenvolvedor. Sistema do Android dos níveis de API ajuda a Android determinar se um aplicativo é compatível com uma imagem do sistema Android antes de instalar o aplicativo em um dispositivo.

Quando um aplicativo é compilado, ele contém as seguintes informações de nível de API:

-   O *destino* nível da API do Android que o aplicativo é criado para ser executado.

-   O *mínimo* nível de API do Android que um dispositivo Android deve ter para executar seu aplicativo. 

Essas configurações são usadas para garantir que a funcionalidade necessária para executar o aplicativo corretamente está disponível no dispositivo Android no momento da instalação. Caso contrário, o aplicativo esteja bloqueado para execução no dispositivo. Por exemplo, se o nível de API de um dispositivo Android é menor do que o nível mínimo de API que você especificar para seu aplicativo, o dispositivo Android impedirá que o usuário de instalar seu aplicativo.


## <a name="project-api-level-settings"></a>Configurações de nível de projeto de API

As seções a seguir explicam como usar o Gerenciador de SDK para preparar o ambiente de desenvolvimento para os níveis de API de destino, seguido de explicações detalhadas sobre como configurar o *estrutura de destino*, *mínimo Versão do Android*, e *versão destino Android* configurações no xamarin. Android.


### <a name="android-sdk-platforms"></a>Plataformas do Android SDK

Antes de selecionar um nível mínimo de API ou de destino no xamarin. Android, você deve instalar a versão da plataforma do SDK do Android que corresponde a esse nível de API. A faixa de opções disponíveis para a estrutura de destino, a versão mínima do Android e versão de destino do Android é limitada às versões do intervalo do SDK do Android que você instalou. Você pode usar o Gerenciador de SDK para verificar se as versões do SDK do Android necessárias são instaladas, e você pode usá-lo para adicionar quaisquer novos níveis de API que você precisa para seu aplicativo. Se você não estiver familiarizado com como instalar os níveis de API, consulte [instalação do SDK do Android](~/android/get-started/installation/android-sdk.md).

<a name="framework" />

### <a name="target-framework"></a>Estrutura de Destino

O *estrutura de destino* (também conhecido como `compileSdkVersion`) é a versão de estrutura específico do Android (API nível) que seu aplicativo é compilado em tempo de compilação. Essa configuração especifica quais APIs o seu aplicativo *espera* usar quando ele é executado, mas não tem nenhum efeito sobre quais APIs ficam efetivamente disponíveis para seu aplicativo quando ele está instalado. Como resultado, a alteração da configuração de estrutura de destino não altera o comportamento de tempo de execução.

A estrutura de destino identifica quais versões de biblioteca que seu aplicativo está vinculado em relação a &ndash; essa configuração determina quais APIs que você pode usar em seu aplicativo. Por exemplo, se você quiser usar o [NotificationBuilder.SetCategory](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetCategory/p/System.String/) método que foi introduzido no Android 5.0 Lollipop, você deve definir a estrutura de destino para **API nível 21 (Lollipop)** ou posterior. Se você definir o Framework de destino do seu projeto para uma API nível como **API nível 19 (KitKat)** e tentar chamar o `SetCategory` método em seu código, você receberá um erro de compilação.

É recomendável que você sempre compilar com o *mais recente* versão disponível da estrutura de destino. Isso fornece mensagens de aviso úteis para quaisquer APIs obsoletas que podem ser chamados pelo seu código. Usando a versão mais recente da estrutura de destino é especialmente importante quando você usa as versões mais recentes de biblioteca de suporte &ndash; cada biblioteca espera que seu aplicativo seja compilado no nível de API mínima da biblioteca de suporte ou maior. 


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Para acessar a configuração da estrutura de destino no Visual Studio, abra as propriedades do projeto no **Gerenciador de soluções** e selecione o **aplicativo** página:

[![Página de propriedades do projeto de aplicativo](android-api-levels-images/vs-target-framework-sml.png)](android-api-levels-images/vs-target-framework.png#lightbox)

Definir a estrutura de destino selecionando um nível de API no menu suspenso em **compilar usando a versão Android** conforme mostrado acima.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Para acessar a configuração de estrutura de destino no Visual Studio para Mac, clique com botão direito no nome do projeto e selecione **opções**; esse é aberta a **opções de projeto** caixa de diálogo. Nesta caixa de diálogo, navegue até **Build > geral** conforme mostrado aqui:

[![Crie uma seção geral da página de opções de projeto](android-api-levels-images/xs-target-framework-sml.png)](android-api-levels-images/xs-target-framework.png#lightbox)

Definir a estrutura de destino selecionando um nível de API no menu à direita da lista suspensa **estrutura de destino** conforme mostrado acima.

-----


<a name="minimum" />

### <a name="minimum-android-version"></a>Versão mínima do Android

O *versão mínima do Android* (também conhecido como `minSdkVersion`) é a versão mais antiga do SO Android (ou seja, o menor nível de API) que pode instalar e executar seu aplicativo. Por padrão, um aplicativo só pode ser instalado em dispositivos que corresponder à configuração da estrutura de destino ou superior; Se for a configuração de versão mínima do Android *inferior* que a configuração da estrutura de destino, o seu aplicativo também pode executar em versões anteriores do Android. Por exemplo, se você definir a estrutura de destino como **Android 7.1 (Nougat)** e defina a versão mínima do Android para **Android 4.0.3 (Ice Cream Sandwich)**, seu aplicativo pode ser instalado em qualquer plataforma de API nível 15 a API nível 25, inclusive.

Embora o seu aplicativo com êxito pode compilar e instalar nessa variedade de plataformas, isso não garante-se com êxito *executar* em todas essas plataformas. Por exemplo, se seu aplicativo está instalado no **Android 5.0 (Lollipop)** e seu código chama uma API que está disponível somente nas **Android 7.1 (Nougat)** e mais recente, seu aplicativo obterá um erro de tempo de execução e possivelmente falhar. Portanto, seu código deve garantir &ndash; em tempo de execução &ndash; que ele chama apenas das APIs que são compatíveis com o dispositivo Android que ela está sendo executada. Em outras palavras, seu código deve incluir verificações de tempo de execução explícita para garantir que seu aplicativo usa APIs mais recentes somente em dispositivos que são suficientemente recentes para dar suporte a eles.
[Tempo de execução verifica para versões do Android](#runtimechecks), mais adiante neste guia, explica como adicionar essas verificações de tempo de execução ao seu código.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Para acessar a configuração de versão mínima do Android no Visual Studio, abra as propriedades do projeto no **Gerenciador de soluções** e selecione o **manifesto do Android** página. No menu suspenso em **versão mínima do Android** você pode selecionar a versão mínima do Android para o seu aplicativo:

[![Opção de destino definida para compilar usando a versão do SDK do Android mínimo](android-api-levels-images/vs-minimum-version-sml.png)](android-api-levels-images/vs-minimum-version.png#lightbox)

Se você selecionar **Use compilar usando a versão do SDK**, a versão mínima do Android será o mesmo que a configuração da estrutura de destino.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Para acessar a versão mínima do Android no Visual Studio para Mac, clique com botão direito no nome do projeto e selecione **opções**; esse é aberta a **opções de projeto** caixa de diálogo. Navegue até **compilar > aplicativo Android**.
Usando o menu suspenso à direita da vírgula **versão mínima do Android**, você pode definir a versão mínima do Android para o seu aplicativo:

[![Versão mínima do Android é definido como automático - versão do framework de destino de uso](android-api-levels-images/xs-minimum-version-sml.png)](android-api-levels-images/xs-minimum-version.png#lightbox)

Se você selecionar **automáticas &ndash; usar a versão do framework de destino**, a versão mínima do Android será o mesmo que a configuração da estrutura de destino.

-----


<a name="target" />

### <a name="target-android-version"></a>Versão do Android de destino

O *versão destino Android* (também conhecido como `targetSdkVersion`) é a API do nível do dispositivo Android onde o aplicativo espera executar. Android usa essa configuração para determinar se deseja habilitar os comportamentos de compatibilidade &ndash; Isso garante que seu aplicativo continue a funcionar da maneira esperada. Android usa a configuração de versão de destino do Android do seu aplicativo para descobrir quais alterações de comportamento podem ser aplicadas ao seu aplicativo sem quebrá-la (Isso é como o Android fornece compatibilidade com versões posteriores).

A estrutura de destino e a versão de destino do Android, tendo nomes muito semelhantes, não são a mesma coisa. A configuração da estrutura de destino se comunica informações nível de API de destino para xamarin. Android para uso no *tempo de compilação*, enquanto a versão de destino do Android se comunica informações nível de API de destino do Android para uso em  *tempo de execução* (quando o aplicativo está instalado e em execução em um dispositivo).

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Para acessar essa configuração no Visual Studio, abra as propriedades do projeto no **Gerenciador de soluções** e selecione o **manifesto do Android** página. No menu suspenso em **versão destino Android** você pode selecionar a versão de destino do Android para o seu aplicativo:

[![Versão Android de destino definido para compilar usando a versão do SDK](android-api-levels-images/vs-target-version-sml.png)](android-api-levels-images/vs-target-version.png#lightbox)

É recomendável que você definir explicitamente a versão de destino do Android para a versão mais recente do Android que você pode usar para testar seu aplicativo. O ideal é que ele deve ser definido para a versão mais recente do SDK do Android &ndash; Isso permite que você use as novas APIs antes de trabalhar por meio das alterações de comportamento. Para a maioria dos desenvolvedores, estamos *não tiver* recomendável definir a versão de destino do Android com **Use compilar usando a versão do SDK**.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Para acessar essa configuração no Visual Studio para Mac, clique com botão direito no nome do projeto e selecione **opções**; esse é aberta a **opções de projeto** caixa de diálogo. Navegue até **compilar > aplicativo Android**. Usando o menu suspenso à direita da vírgula **versão destino Android**, você pode definir a versão de destino do Android para o seu aplicativo:

[![Versão do Android de destino é definido como automático - versão do framework de destino de uso](android-api-levels-images/xs-target-version-sml.png)](android-api-levels-images/xs-target-version.png#lightbox)

É recomendável que você definir explicitamente a versão de destino do Android para a versão mais recente do Android que você pode usar para testar seu aplicativo. O ideal é que ele deve ser definido para a versão mais recente do SDK do Android &ndash; Isso permite que você use as novas APIs antes de trabalhar por meio das alterações de comportamento. Para a maioria dos desenvolvedores, não é recomendável definir a versão de destino do Android com **automático - use a versão do target framework**.

-----

Em geral, a versão do Android de destino deve ser limitada pela versão mínima do Android e a estrutura de destino. Ou seja:

**Versão mínima do Android < = versão destino Android < = a estrutura de destino**

Para obter mais informações sobre os níveis de SDK, consulte o desenvolvedor do Android [sdk usa](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html) documentação.


<a name="runtimechecks" />

## <a name="runtime-checks-for-android-versions"></a>Tempo de execução verifica para versões do Android

Conforme cada nova versão do Android for lançada, a estrutura de API é atualizada para fornecer novos ou funcionalidade de substituição. Com poucas exceções, funcionalidade da API de versões anteriores do Android é repassada para versões mais recentes do Android sem modificações. Como resultado, se seu aplicativo for executado em um determinado nível de API do Android, ele geralmente será capaz de executar em um nível de API do Android mais recente sem modificações. Mas e se você também deseja executar seu aplicativo em versões anteriores do Android?

Se você selecionar uma versão mínima do Android *inferior* que a configuração da estrutura de destino, algumas APIs podem não estar disponíveis para seu aplicativo em tempo de execução. No entanto, seu aplicativo ainda pode executar em um dispositivo anterior, mas com funcionalidade reduzida. Para cada API não está disponível em plataformas Android correspondente à sua configuração de versão mínima do Android, seu código deve verificar explicitamente o valor da `Android.OS.Build.VERSION.SdkInt` propriedade para determinar o nível da API da plataforma em que o aplicativo está sendo executado. Se o nível de API estiver *inferior* que a versão mínima do Android que ofereça suporte a API que você deseja chamar e, em seguida, seu código precisa encontrar uma maneira para funcionar corretamente sem fazer essa chamada à API.

Por exemplo, vamos supor que queremos usar o [NotificationBuilder.SetCategory](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetCategory/p/System.String/) método para categorizar uma notificação quando em execução no **Android 5.0 Lollipop** (e posterior), mas ainda queremos que nosso aplicativo para Executar como em versões anteriores do Android **Android 4.1 Jelly Bean** (onde `SetCategory` não está disponível). Referindo-se à tabela de versão do Android no início deste guia, podemos ver que o código de versão de compilação para **Android 5.0 Lollipop** é `Android.OS.BuildVersionCodes.Lollipop`. Para dar suporte a versões mais antigas do Android where `SetCategory` é não está disponível, nosso código pode detectar o nível de API em tempo de execução e chama condicionalmente `SetCategory` somente quando o nível de API é maior que ou igual ao código de versão de compilação de pirulito:

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop)
{
    builder.SetCategory(Notification.CategoryEmail);
}
```

Neste exemplo, a estrutura de destino do nosso aplicativo é definida como **Android 5.0 (API nível 21)** e sua versão mínima do Android é definido como **Android 4.1 (API nível 16)**. Porque `SetCategory` está disponível no nível da API `Android.OS.BuildVersionCodes.Lollipop` e versões posteriores, esse código de exemplo chamará `SetCategory` apenas quando ele está realmente disponível &ndash; ele irá *não* tentar chamar `SetCategory` quando a API nível é 16, 17, 18, 19 ou 20. A funcionalidade é reduzida nessas versões Android anteriormente somente na medida em que as notificações não são classificadas corretamente (porque eles não são categorizados por tipo), ainda que as notificações ainda estão publicadas para alertar o usuário. Nosso aplicativo ainda funciona, mas sua funcionalidade será ligeiramente menor.

Em geral, a verificação de versão de compilação ajuda a seu código decida em tempo de execução entre fazendo algo a nova maneira versus a maneira antiga. Por exemplo:

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

Não há nenhuma regra rápida e simple que explica como reduzir ou modificar a funcionalidade do aplicativo quando ele é executado em versões mais antigas do Android que estão faltando uma ou mais APIs. Em alguns casos (como no `SetCategory` exemplo acima), é suficiente omitir a chamada à API quando ele não está disponível. No entanto, em outros casos, talvez seja necessário implementar a funcionalidade alternativa para quando `Android.OS.Build.VERSION.SdkInt` é detectado para ser menor do que a API de nível de que seu aplicativo precisa para apresentar sua experiência ideal.

<a name="libraries" />

## <a name="api-levels-and-libraries"></a>Bibliotecas e níveis de API

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Quando você cria um projeto de biblioteca do xamarin. Android (como uma biblioteca de classes ou uma biblioteca de associações), você pode configurar somente a definição da estrutura de destino &ndash; a versão mínima do Android e as configurações de versão de destino do Android não estão disponíveis. Isso é porque não há nenhuma **manifesto do Android** página:

[![Somente a compilação usando a opção de versão do Android está disponível](android-api-levels-images/vs-library-options-sml.png)](android-api-levels-images/vs-library-options.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Quando você cria um projeto de biblioteca do xamarin. Android, não há nenhuma **aplicativo Android** página onde você pode configurar a versão mínima do Android e a versão de destino do Android &ndash; a versão mínima do Android e o destino Configurações de versão do Android não estão disponíveis.
Isso é porque não há nenhuma **compilar > aplicativo Android** página:

[![Página geral sem opções de versão mínimo e o destino de build](android-api-levels-images/xs-library-options-sml.png)](android-api-levels-images/xs-library-options.png#lightbox)

-----

A versão mínima do Android e as configurações de versão de destino do Android não estão disponíveis porque a biblioteca resultante não é um aplicativo autônomo &ndash; a biblioteca pode ser executada em qualquer versão do Android, dependendo do aplicativo que é empacotado com. Você pode especificar como a biblioteca deve ser *compilado*, mas você não pode prever qual plataforma de nível de API a biblioteca será executada. Com isso em mente, as seguintes práticas recomendadas devem ser observadas ao consumir ou criação de bibliotecas:

-   **Ao consumir uma biblioteca Android** &ndash; se você está consumindo uma biblioteca Android no seu aplicativo, certifique-se de definir a configuração para uma API nível que é de estrutura de destino do seu aplicativo *pelo menos tão alto quanto* o destino Configuração de estrutura da biblioteca.

-   **Ao criar uma biblioteca Android** &ndash; se você estiver criando uma biblioteca Android para uso por outros aplicativos, certifique-se de definir sua configuração de estrutura de destino para o nível mínimo de API que precisa para compilar.

Essas práticas recomendadas são sugeridas para ajudar a evitar a situação em que uma biblioteca tenta chamar uma API que não está disponível em tempo de execução (o que pode causar a falha no aplicativo). Se você for um desenvolvedor de biblioteca, você deve se esforçar para restringir o uso de chamadas à API a um subconjunto pequeno e bem estabelecida de área de superfície de API total. Isso ajuda a garantir que sua biblioteca pode ser usada com segurança em um versões mais amplo do intervalo do Android.


## <a name="summary"></a>Resumo

Este guia explicou como os níveis de API do Android são usados para gerenciar a compatibilidade de aplicativo entre diferentes versões do Android. Ele fornecidas etapas detalhadas para configurar o xamarin. Android *estrutura de destino*, *versão mínima do Android*, e *versão destino Android* configurações do projeto. Ele fornecidas instruções para usar o Gerenciador de SDK do Android para instalar os pacotes do SDK, incluídos exemplos de como escrever código para lidar com diferentes níveis de API em tempo de execução e explicou como gerenciar os níveis de API ao criar ou consumir bibliotecas Android. Também forneceu uma lista abrangente que relaciona os níveis de API para números de versão do Android (por exemplo, o Android 4.4), nomes de versão do Android (como Kitkat) e códigos de versão de build do xamarin. Android.


## <a name="related-links"></a>Links relacionados

- [Configuração do SDK do Android](~/android/get-started/installation/android-sdk.md)
- [Alterações de ferramentas da CLI do SDK](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [Separação sua compileSdkVersion, minSdkVersion e targetSdkVersion](https://medium.com/google-developers/picking-your-compilesdkversion-minsdkversion-targetsdkversion-a098a0341ebd)
- [O que é o nível de API?](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html#ApiLevels)
- [Codinome, marcas e números de compilação](https://source.android.com/source/build-numbers)
