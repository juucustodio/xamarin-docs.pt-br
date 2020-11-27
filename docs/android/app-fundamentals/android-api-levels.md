---
title: Entendendo os níveis da API do Android
description: O Xamarin. Android tem várias configurações de nível de API do Android que determinam a compatibilidade do seu aplicativo com várias versões do Android. Este guia explica o que essas configurações significam, como configurá-las e o efeito que elas têm em seu aplicativo em tempo de execução.
ms.prod: xamarin
ms.assetid: 58CB7B34-3140-4BEB-BE2E-209928C1878C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/21/2018
ms.openlocfilehash: bb41a6e4ea477ed0a7932df441678e53a304aa35
ms.sourcegitcommit: 8fa0cb9ccbc107d697aa5b9113a4e5d1e75d6eb9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2020
ms.locfileid: "96303095"
---
# <a name="understanding-android-api-levels"></a>Noções básicas sobre níveis da API do Android

_O Xamarin. Android tem várias configurações de nível de API do Android que determinam a compatibilidade do seu aplicativo com várias versões do Android. Este guia explica o que essas configurações significam, como configurá-las e o efeito que elas têm em seu aplicativo em tempo de execução._

## <a name="quick-start"></a>Início rápido

O Xamarin. Android expõe três configurações de projeto de nível de API do Android:

- [Estrutura](#framework) &ndash; de destino Especifica qual estrutura usar na criação do aplicativo. Esse nível de API é usado no momento da *compilação* pelo Xamarin. Android.

- Versão mínima do [Android](#minimum) &ndash; Especifica a versão mais antiga do Android para a qual você deseja que seu aplicativo dê suporte. Esse nível de API é usado em tempo de *execução* pelo Android.

- Versão do Android de [destino](#target) &ndash; Especifica a versão do Android na qual seu aplicativo se destina a ser executado. Esse nível de API é usado em tempo de *execução* pelo Android.

Antes de configurar um nível de API para seu projeto, você deve instalar os componentes da plataforma SDK para esse nível de API. Para obter mais informações sobre como baixar e instalar SDK do Android componentes, consulte [SDK do Android setup](~/android/get-started/installation/android-sdk.md).

> [!NOTE]
> A partir de agosto de 2020, o console do Google Play requer que os novos aplicativos tenham o nível da API de destino 29 (Android 10,0) ou superior.
Os aplicativos existentes são necessários para direcionar o nível da API 29 ou superior a partir de novembro de 2020. Para obter mais informações, consulte [requisitos de nível de API de destino para o console de reprodução](https://support.google.com/googleplay/android-developer/answer/9859152#targetsdk) em "criar e configurar seu aplicativo" na documentação do console de reprodução.

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Normalmente, todos os três níveis de API do Xamarin. Android são definidos com o mesmo valor. Na página do **aplicativo** , defina **compilar usando a versão do Android (estrutura de destino)** para a versão de API estável mais recente (ou, no mínimo, para a versão do Android que tem todos os recursos necessários).
Na captura de tela a seguir, a estrutura de destino é definida como **Android 7,1 (nível de API 25-nougat)**:

[![Padrões de versão da estrutura de destino para compilar usando a versão do Android](android-api-levels-images/vs-defaults-sml.png)](android-api-levels-images/vs-defaults.png#lightbox)

Na página **manifesto do Android** , defina a versão mínima do Android para **usar compilar usando a versão do SDK** e defina a versão do Android de destino com o mesmo valor que a versão do Framework de destino (na captura de tela a seguir, a estrutura do Android de destino será definida como **Android 7,1 (nougat)**):

[![Versões mínima e de destino do Android definidas para a versão do Framework de destino](android-api-levels-images/vs-manifest-defaults-sml.png)](android-api-levels-images/vs-manifest-defaults.png#lightbox)

Se você quiser manter a compatibilidade com versões anteriores com uma versão anterior do Android, defina a **versão mínima do Android como destino** para a versão mais antiga do Android à qual você deseja que seu aplicativo dê suporte. (Observe que a API nível 14 é o nível mínimo de API necessário para [serviços de Google Play e suporte firebase](https://android-developers.googleblog.com/2016/11/google-play-services-and-firebase-for-android-will-support-api-level-14-at-minimum.html).) A configuração de exemplo a seguir dá suporte a versões do Android do nível de API 14 por meio da API nível 25:

[![Compilar usando a API nível 25 nougat, versão mínima do Android definida como API nível 14](android-api-levels-images/vs-minimum-sml.png)](android-api-levels-images/vs-minimum.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Normalmente, todos os três níveis de API do Xamarin. Android são definidos com o mesmo valor. Defina a **estrutura de destino** para a versão de API estável mais recente (ou, no mínimo, para a versão do Android que tem todos os recursos necessários). Para definir a **estrutura de destino**, navegue até **criar > geral** nas **Opções de projeto**. Na captura de tela a seguir, a estrutura de destino está definida para **usar a plataforma instalada mais recente (8,0)**:

[![Definição padrão da estrutura de destino para usar a plataforma instalada mais recente](android-api-levels-images/xs-default-target-sml.png)](android-api-levels-images/xs-default-target.png#lightbox)

As configurações de versão mínima e de destino do Android podem ser encontradas em **criar > aplicativo Android** em **Opções de projeto**. Defina a versão mínima do Android para a **versão do Framework de destino de uso automático** e defina a versão do Android de destino como o mesmo valor da versão do Framework de destino. Na captura de tela a seguir, a estrutura do Android de destino é definida como **android 8,0 (API nível 26)** para corresponder à configuração da estrutura de destino acima:

[![Definindo os níveis de destino e estrutura nas opções de projeto](android-api-levels-images/xs-default-app-sml.png)](android-api-levels-images/xs-default-app.png#lightbox)

Se você quiser manter a compatibilidade com versões anteriores com uma versão anterior do Android, altere a **versão mínima do Android** para a versão mais antiga do Android à qual você deseja que seu aplicativo dê suporte. Observe que a API nível 14 é o nível mínimo de API necessário para [serviços de Google Play e suporte firebase](https://android-developers.googleblog.com/2016/11/google-play-services-and-firebase-for-android-will-support-api-level-14-at-minimum.html).
Por exemplo, a configuração a seguir dá suporte a versões do Android como o nível 14 da API:

[![Versões mínimas e de destino definidas como automático-usar a versão da estrutura de destino](android-api-levels-images/xs-minimum-sml.png)](android-api-levels-images/xs-minimum.png#lightbox)

-----

Se seu aplicativo oferecer suporte a várias versões do Android, seu código deverá incluir verificações de tempo de execução para garantir que seu aplicativo funcione com a configuração de versão mínima do Android (consulte [verificações de tempo de execução para versões do Android](#runtimechecks) abaixo para obter detalhes). Se você estiver consumindo ou criando uma biblioteca, consulte [bibliotecas e níveis de API](#libraries) abaixo para obter as práticas recomendadas em definindo configurações de nível de API para bibliotecas.

## <a name="android-versions-and-api-levels"></a>Versões e níveis de API do Android

À medida que a plataforma Android evolui e novas versões do Android são lançadas, cada versão do Android recebe um identificador inteiro exclusivo, chamado de *nível de API*. Portanto, cada versão do Android corresponde a um único nível de API do Android. Como os usuários instalam aplicativos no mais antigo, bem como nas versões mais recentes do Android, os aplicativos Android do mundo real devem ser projetados para funcionar com vários níveis de API do Android.

### <a name="android-versions"></a>Versões do Android

Cada versão do Android passa por vários nomes:

- A versão do Android, como **android 9,0**
- Um nome de código (ou sobremesa), como _pizza_
- Um nível de API correspondente, como **nível de API 28**

Um nome de código do Android pode corresponder a várias versões e níveis de API (como visto na tabela abaixo), mas cada versão do Android corresponde a exatamente um nível de API.

Além disso, o Xamarin. Android define *códigos de versão de compilação* que são mapeados para os níveis de API do Android conhecidos no momento. A tabela a seguir pode ajudá-lo a converter entre o nível da API, a versão do Android, o nome do código e o código da versão build do Xamarin. Android (os códigos de versão do Build são definidos no `Android.OS` namespace):

[!include[](~/android/includes/api-levels.md)]

Como essa tabela indica, novas versões do Android são lançadas com frequência, &ndash; às vezes, mais de uma versão por ano. Como resultado, o universo de dispositivos Android que podem executar seu aplicativo inclui uma ampla variedade de versões mais antigas e mais recentes do Android. Como você pode garantir que seu aplicativo será executado de forma consistente e confiável em tantas versões diferentes do Android? Os níveis de API do Android podem ajudá-lo a gerenciar esse problema.

### <a name="android-api-levels"></a>Níveis da API do Android

Cada dispositivo Android é executado em exatamente *um* nível de API &ndash; esse nível de API é garantido como exclusivo por versão da plataforma Android. O nível de API identifica precisamente a versão do conjunto de API que seu aplicativo pode chamar; Ele identifica a combinação de elementos de manifesto, permissões, etc. que você codifica em relação a um desenvolvedor. O sistema de níveis de API do Android ajuda o Android a determinar se um aplicativo é compatível com uma imagem do sistema Android antes de instalar o aplicativo em um dispositivo.

Quando um aplicativo é compilado, ele contém as seguintes informações de nível de API:

- O nível da API de *destino* do Android no qual o aplicativo foi criado para ser executado.

- O nível *mínimo* de API do Android que um dispositivo Android deve ter para executar seu aplicativo. 

Essas configurações são usadas para garantir que a funcionalidade necessária para executar o aplicativo corretamente esteja disponível no dispositivo Android no momento da instalação. Caso contrário, o aplicativo será impedido de ser executado nesse dispositivo. Por exemplo, se o nível de API de um dispositivo Android for menor do que o nível mínimo de API que você especificar para seu aplicativo, o dispositivo Android impedirá que o usuário instale seu aplicativo.

## <a name="project-api-level-settings"></a>Configurações de nível de API do projeto

As seções a seguir explicam como usar o Gerenciador do SDK para preparar seu ambiente de desenvolvimento para os níveis de API que você deseja direcionar, seguidos por explicações detalhadas de como configurar a *estrutura de destino*, a *versão mínima do Android* e as configurações de destino do *Android* no Xamarin. Android.

### <a name="android-sdk-platforms"></a>Plataformas SDK do Android

Antes de selecionar um nível de destino ou de API mínimo no Xamarin. Android, você deve instalar o SDK do Android versão da plataforma que corresponde a esse nível de API. O intervalo de opções disponíveis para a estrutura de destino, a versão mínima do Android e a versão de destino do Android é limitado ao intervalo de SDK do Android versões que você instalou. Você pode usar o Gerenciador do SDK para verificar se as versões necessárias do SDK do Android estão instaladas e pode usá-las para adicionar novos níveis de API necessários para seu aplicativo. Se você não estiver familiarizado com a instalação de níveis de API, consulte [SDK do Android instalação](~/android/get-started/installation/android-sdk.md).

<a name="framework"></a>

### <a name="target-framework"></a>Estrutura de Destino

A *estrutura de destino* (também conhecida como `compileSdkVersion` ) é a versão específica da estrutura do Android (nível de API) para a qual seu aplicativo é compilado no momento da compilação. Essa configuração especifica quais APIs seu aplicativo *espera* usar quando é executado, mas não tem nenhum efeito sobre quais APIs estão realmente disponíveis para seu aplicativo quando ele é instalado. Como resultado, alterar a configuração da estrutura de destino não altera o comportamento do tempo de execução.

A estrutura de destino identifica quais versões de biblioteca seu aplicativo está vinculado a &ndash; essa configuração determina quais APIs você pode usar em seu aplicativo. Por exemplo, se você quiser usar o método [NotificationBuilder. setcategory](xref:Android.App.Notification.Builder.SetCategory*) que foi introduzido no Android 5,0 pirulito, você deve definir a estrutura de destino como **API nível 21 (pirulito)** ou posterior. Se você definir a estrutura de destino do projeto como um nível de API, como **KitKat (API nível 19)** e tentar chamar o `SetCategory` método em seu código, receberá um erro de compilação.

É recomendável que você sempre compile com a versão *mais recente* da estrutura de destino disponível. Isso fornece mensagens de aviso úteis para as APIs preteridas que podem ser chamadas pelo seu código. Usar a versão mais recente do Framework de destino é especialmente importante quando você usa as versões mais recentes da biblioteca de suporte &ndash; cada biblioteca espera que seu aplicativo seja compilado no nível mínimo de API de suporte ou superior da biblioteca.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Para acessar a configuração da estrutura de destino no Visual Studio, abra as propriedades do projeto no **Gerenciador de soluções** e selecione a página do **aplicativo** :

[![Página de aplicativo de propriedades do projeto](android-api-levels-images/vs-target-framework-sml.png)](android-api-levels-images/vs-target-framework.png#lightbox)

Defina a estrutura de destino selecionando um nível de API no menu suspenso em **compilar usando a versão do Android** , conforme mostrado acima.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Para acessar a configuração da estrutura de destino no Visual Studio para Mac, clique com o botão direito do mouse no nome do projeto e selecione **Opções**; Isso abre a caixa de diálogo **Opções do projeto** . Nesta caixa de diálogo, navegue até **Build > geral** , como mostrado aqui:

[![Seção criar geral da página Opções do projeto](android-api-levels-images/xs-target-framework-sml.png)](android-api-levels-images/xs-target-framework.png#lightbox)

Defina a estrutura de destino selecionando um nível de API no menu suspenso à direita da estrutura de **destino** , conforme mostrado acima.

-----

<a name="minimum"></a>

### <a name="minimum-android-version"></a>Versão mínima do Android

A *versão mínima do Android* (também conhecida como `minSdkVersion` ) é a versão mais antiga do sistema operacional Android (ou seja, o nível de API mais baixo) que pode instalar e executar seu aplicativo. Por padrão, um aplicativo só pode ser instalado em dispositivos que correspondem à configuração da estrutura de destino ou superior; se a configuração de versão mínima do Android for *menor* do que a configuração da estrutura de destino, seu aplicativo também poderá ser executado em versões anteriores do Android. Por exemplo, se você definir a estrutura de destino como **android 7,1 (nougat)** e definir a versão mínima do Android para **Android 4.0.3 (gelo de sorvete)**, seu aplicativo poderá ser instalado em qualquer plataforma do nível de API 15 para o nível de API 25, inclusive.

Embora seu aplicativo possa criar e instalar com êxito nessa variedade de plataformas, isso não garante que ela seja *executada* com êxito em todas essas plataformas. Por exemplo, se seu aplicativo estiver instalado no **android 5,0 (pirulito)** e seu código chamar uma API que está disponível somente no **Android 7,1 (nougat)** e mais recente, seu aplicativo receberá um erro de tempo de execução e possivelmente falhará. Portanto, seu código deve garantir &ndash; que, em tempo de execução, &ndash; ele chame apenas as APIs com suporte no dispositivo Android em que ele está sendo executado. Em outras palavras, seu código deve incluir verificações de tempo de execução explícitas para garantir que seu aplicativo use APIs mais recentes somente em dispositivos que são recentes o suficiente para dar suporte a elas.
[As verificações de tempo de execução para versões do Android](#runtimechecks), posteriormente neste guia, explicam como adicionar essas verificações de tempo de execução ao seu código.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Para acessar a configuração de versão mínima do Android no Visual Studio, abra as propriedades do projeto no **Gerenciador de soluções** e selecione a página **manifesto do Android** . No menu suspenso, em **versão mínima do Android** , você pode selecionar a versão mínima do Android para seu aplicativo:

[![Opção mínima de Android para destino definida como compilar usando a versão do SDK](android-api-levels-images/vs-minimum-version-sml.png)](android-api-levels-images/vs-minimum-version.png#lightbox)

Se você selecionar **usar compilar usando a versão do SDK**, a versão mínima do Android será a mesma que a configuração da estrutura de destino.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Para acessar a versão mínima do Android no Visual Studio para Mac, clique com o botão direito do mouse no nome do projeto e selecione **Opções**; Isso abre a caixa de diálogo **Opções do projeto** . Navegue para **compilar > aplicativo Android**.
Usando o menu suspenso à direita da **versão mínima do Android**, você pode definir a versão mínima do Android para seu aplicativo:

[![Versão mínima do Android definida como automática-usar a versão da estrutura de destino](android-api-levels-images/xs-minimum-version-sml.png)](android-api-levels-images/xs-minimum-version.png#lightbox)

Se você selecionar **usar automaticamente a &ndash; versão do Framework de destino**, a versão mínima do Android será a mesma que a configuração da estrutura de destino.

-----

<a name="target"></a>

### <a name="target-android-version"></a>Versão do Android de destino

A *versão de destino do Android* (também conhecida como `targetSdkVersion` ) é o nível de API do dispositivo Android em que o aplicativo espera ser executado. O Android usa essa configuração para determinar se os comportamentos de compatibilidade devem ser habilitados, &ndash; isso garante que seu aplicativo continue a funcionar da maneira esperada. O Android usa a configuração de destino da versão do Android do seu aplicativo para descobrir quais alterações de comportamento podem ser aplicadas ao seu aplicativo sem dividi-lo (é assim que o Android fornece compatibilidade com versões posteriores).

A estrutura de destino e a versão de destino do Android, embora tenham nomes muito semelhantes, não são a mesma coisa. A configuração de estrutura de destino comunica informações de nível de API de destino ao Xamarin. Android para uso no *momento da compilação*, enquanto a versão de destino do Android comunica informações de nível da API de destino ao Android para uso em *tempo de execução* (quando o aplicativo está instalado e em execução em um dispositivo).

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Para acessar essa configuração no Visual Studio, abra as propriedades do projeto no **Gerenciador de soluções** e selecione a página **manifesto do Android** . No menu suspenso em **versão de destino do Android** , você pode selecionar a versão de destino do Android para seu aplicativo:

[![Versão de destino do Android definida para compilar usando a versão do SDK](android-api-levels-images/vs-target-version-sml.png)](android-api-levels-images/vs-target-version.png#lightbox)

Recomendamos que você defina explicitamente a versão de destino do Android para a versão mais recente do Android que você usa para testar seu aplicativo. O ideal é que ele seja definido para a versão mais recente do SDK do Android &ndash; isso permite que você use novas APIs antes de trabalhar com as alterações de comportamento. Para a maioria dos desenvolvedores, *não* recomendamos definir a versão de destino do Android para **usar compilar usando a versão do SDK**.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Para acessar essa configuração em Visual Studio para Mac, clique com o botão direito do mouse no nome do projeto e selecione **Opções**; Isso abre a caixa de diálogo **Opções do projeto** . Navegue para **compilar > aplicativo Android**. Usando o menu suspenso à direita da **versão de destino do Android**, você pode definir a versão de destino do Android para seu aplicativo:

[![Versão do Android de destino definida como automática-usar versão da estrutura de destino](android-api-levels-images/xs-target-version-sml.png)](android-api-levels-images/xs-target-version.png#lightbox)

Recomendamos que você defina explicitamente a versão de destino do Android para a versão mais recente do Android que você usa para testar seu aplicativo. O ideal é que ele seja definido para a versão mais recente do SDK do Android disponível &ndash; isso permite que você use novas APIs antes de trabalhar com as alterações de comportamento. Para a maioria dos desenvolvedores, não recomendamos definir a versão de destino do Android para o **uso automático da versão do Framework de destino**.

-----

Em geral, a versão de destino do Android deve ser limitada pela versão mínima do Android e a estrutura de destino. Ou seja:

**Versão mínima do Android <= Target versão do Android <= estrutura de destino**

Para obter mais informações sobre os níveis de SDK, consulte a documentação do [SDK de uso](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html) do Android.

<a name="runtimechecks"></a>

## <a name="runtime-checks-for-android-versions"></a>Verificações de tempo de execução para versões do Android

À medida que cada nova versão do Android é lançada, a API da estrutura é atualizada para fornecer funcionalidades novas ou substitutas. Com poucas exceções, a funcionalidade de API de versões anteriores do Android é postergada para versões mais recentes do Android sem modificações. Como resultado, se seu aplicativo for executado em um nível de API do Android específico, ele normalmente será capaz de ser executado em um nível de API do Android posterior sem modificações. Mas e se você também quiser executar seu aplicativo em versões anteriores do Android?

Se você selecionar uma versão mínima do Android que seja *inferior* à sua configuração de estrutura de destino, algumas APIs poderão não estar disponíveis para seu aplicativo em tempo de execução. No entanto, seu aplicativo ainda pode ser executado em um dispositivo anterior, mas com funcionalidade reduzida. Para cada API que não está disponível em plataformas Android correspondentes à sua configuração de versão mínima do Android, seu código deve verificar explicitamente o valor da `Android.OS.Build.VERSION.SdkInt` propriedade para determinar o nível de API da plataforma em que o aplicativo está sendo executado. Se o nível da API for *inferior* à versão mínima do Android que dá suporte à API que você deseja chamar, o código terá de encontrar uma maneira de funcionar corretamente sem fazer essa chamada à API.

Por exemplo, vamos supor que queremos usar o método [NotificationBuilder. setcategory](xref:Android.App.Notification.Builder.SetCategory*) para categorizar uma notificação ao ser executado em **Android 5,0 pirulito** (e posterior), mas ainda queremos que nosso aplicativo seja executado em versões anteriores do android, como **Android 4,1 Jelly Bean** (onde `SetCategory` não está disponível). Referindo-se à tabela de versão do Android no início deste guia, vemos que o código de versão da compilação para **Android 5,0 pirulito** é `Android.OS.BuildVersionCodes.Lollipop` . Para dar suporte a versões mais antigas do Android em que o `SetCategory` não está disponível, nosso código pode detectar o nível da API em tempo de execução e chamar condicionalmente `SetCategory` somente quando o nível da API for maior ou igual ao código de versão da compilação pirulito:

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop)
{
    builder.SetCategory(Notification.CategoryEmail);
}
```

Neste exemplo, a estrutura de destino do aplicativo é definida como **android 5,0 (nível da API 21)** e sua versão mínima do Android é definida como **Android 4,1 (API nível 16)**. Como `SetCategory` o está disponível no nível da API `Android.OS.BuildVersionCodes.Lollipop` e posteriormente, este código de exemplo chamará `SetCategory` somente quando estiver realmente disponível &ndash; ele *não* tentará chamar `SetCategory` quando o nível da API for 16, 17, 18, 19 ou 20. A funcionalidade é reduzida nessas versões anteriores do Android somente na medida em que as notificações não são classificadas corretamente (porque elas não são categorizadas por tipo), ainda que as notificações ainda sejam publicadas para alertar o usuário. Nosso aplicativo ainda funciona, mas sua funcionalidade é um pouco menor.

Em geral, a verificação da versão do Build ajuda o seu código a decidir em tempo de execução entre as coisas da nova maneira em relação à forma antiga. Por exemplo:

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

Não há uma regra rápida e simples que explica como reduzir ou modificar a funcionalidade do aplicativo quando ele é executado em versões mais antigas do Android que não têm uma ou mais APIs. Em alguns casos (como no `SetCategory` exemplo acima), é suficiente omitir a chamada à API quando ela não estiver disponível. No entanto, em outros casos, talvez seja necessário implementar a funcionalidade alternativa para quando `Android.OS.Build.VERSION.SdkInt` o for detectado ser menor do que o nível de API que seu aplicativo precisa para apresentar sua experiência ideal.

<a name="libraries"></a>

## <a name="api-levels-and-libraries"></a>Bibliotecas e níveis de API

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Ao criar um projeto de biblioteca Xamarin. Android (como uma biblioteca de classes ou uma biblioteca de associações), você pode configurar apenas a estrutura de destino definindo &ndash; a versão mínima do Android e as configurações de versão do Android de destino não estão disponíveis. Isso ocorre porque não há nenhuma página de **manifesto do Android** :

[![Somente a opção compilar usando a versão do Android está disponível](android-api-levels-images/vs-library-options-sml.png)](android-api-levels-images/vs-library-options.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Quando você cria um projeto de biblioteca Xamarin. Android, não há nenhuma página de **aplicativo Android** , na qual você pode configurar a versão mínima do Android e a versão do Android de destino &ndash; a versão mínima do Android e as configurações de versão do Android de destino não estão disponíveis.
Isso ocorre porque não há **compilação > página do aplicativo Android** :

[![Compilar página geral sem opções de versão mínima e de destino](android-api-levels-images/xs-library-options-sml.png)](android-api-levels-images/xs-library-options.png#lightbox)

-----

A versão mínima do Android e as configurações do Android de destino não estão disponíveis porque a biblioteca resultante não é um aplicativo autônomo &ndash; . a biblioteca pode ser executada em qualquer versão do Android, dependendo do aplicativo com o qual ele está empacotado. Você pode especificar como a biblioteca deve ser *compilada*, mas não pode prever em qual nível de API de plataforma a biblioteca será executada. Com isso em mente, as seguintes práticas recomendadas devem ser observadas ao consumir ou criar bibliotecas:

- **Ao consumir uma biblioteca** &ndash; do Android Se você estiver consumindo uma biblioteca do Android em seu aplicativo, certifique-se de definir a configuração da estrutura de destino do aplicativo como um nível de API que seja *pelo menos tão alto quanto* a configuração da estrutura de destino da biblioteca.

- **Ao criar uma biblioteca** &ndash; do Android Se você estiver criando uma biblioteca do Android para uso por outros aplicativos, certifique-se de definir sua configuração de estrutura de destino como o nível mínimo de API que precisa para compilar.

Essas práticas recomendadas são recomendadas para ajudar a evitar a situação em que uma biblioteca tenta chamar uma API que não está disponível em tempo de execução (o que pode fazer com que o aplicativo falhe). Se você for um desenvolvedor de biblioteca, deverá se esforçar para restringir o uso de chamadas à API para um subconjunto pequeno e bem estabelecido da área total da superfície da API. Isso ajuda a garantir que sua biblioteca possa ser usada com segurança em uma grande variedade de versões do Android.

## <a name="summary"></a>Resumo

Este guia explicou como os níveis de API do Android são usados para gerenciar a compatibilidade de aplicativos em diferentes versões do Android. Ele forneceu etapas detalhadas para configurar a estrutura de *destino* do Xamarin. Android, a *versão mínima do Android* e as configurações do projeto de *versão do Android de destino* . Ele forneceu instruções para usar o Gerenciador de SDK do Android para instalar pacotes do SDK, incluindo exemplos de como escrever código para lidar com diferentes níveis de API em tempo de execução e explicou como gerenciar níveis de API ao criar ou consumir bibliotecas Android. Ele também forneceu uma lista abrangente que relaciona os níveis de API aos números de versão do Android (como Android 4,4), nomes de versão do Android (como KitKat) e códigos de versão de Build do Xamarin. Android.

## <a name="related-links"></a>Links Relacionados

- [Configuração do SDK do Android](~/android/get-started/installation/android-sdk.md)
- [Alterações de ferramentas da CLI do SDK](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [Escolhendo seu compileSdkVersion, minSdkVersion e targetSdkVersion](https://medium.com/google-developers/picking-your-compilesdkversion-minsdkversion-targetsdkversion-a098a0341ebd)
- [O que é o nível de API?](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html#ApiLevels)
- [Códigos de código, marcas e números de Build](https://source.android.com/source/build-numbers)
