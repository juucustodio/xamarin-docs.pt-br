---
title: Vinculação de aplicativo no Android
description: Este guia aborda como Android 6.0 oferece suporte a aplicativo vinculação, uma técnica que permite que aplicativos móveis responder a URLs de sites. Ele abordará vinculando o aplicativo é, a implementação de vinculação de aplicativo em um aplicativo do Android 6.0 e como configurar um site para conceder permissões ao aplicativo móvel para um domínio.
ms.prod: xamarin
ms.assetid: 48174E39-19FD-43BC-B54C-9AF11D4B1F91
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 84185bb616597ee62a35c1acacc5e3664f500c21
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2018
---
# <a name="app-linking-in-android"></a>Vinculação de aplicativo no Android

_Este guia aborda como Android 6.0 oferece suporte a aplicativo vinculação, uma técnica que permite que aplicativos móveis responder a URLs de sites. Ele abordará vinculando o aplicativo é, a implementação de vinculação de aplicativo em um aplicativo do Android 6.0 e como configurar um site para conceder permissões ao aplicativo móvel para um domínio._

## <a name="app-linking-overview"></a>Visão geral de vinculação do aplicativo

Aplicativos móveis não residem em um silo &ndash; em muitos casos, eles são um componentes importantes de seus negócios, juntamente com seu site. É recomendável que as empresas se conectar diretamente sua presença na web e aplicativos móveis, com links em um site iniciando os aplicativos móveis e exibindo conteúdo relevante no aplicativo móvel. *Vinculação de aplicativo* (também conhecido como *vinculação profundo*) é uma técnica que permite que um dispositivo móvel responder a um URI e iniciar um aplicativo móvel que corresponde a esse URI.

Android manipula a vinculação de aplicativo por meio de *sistema intenção* &ndash; quando o usuário clica em um link em um navegador móvel, o navegador móvel enviará um propósito de Android delegará a um aplicativo registrado. Por exemplo, clicando em um link em um site de culinária abra um aplicativo móvel que está associado ao site e exibir uma receita específica para o usuário. Se houver mais de um aplicativo registrado para lidar com essa intenção e Android gerará o que é conhecido como um *diálogo ambiguidades* que pedirá um usuário que o aplicativo para selecionar o aplicativo que deve lidar com a intenção de exemplo:

![Captura de tela de exemplo de uma caixa de diálogo ambiguidades](app-linking-images/01-disambiguation-dialog.png)

Android 6.0 aprimora isso usando a manipulação automática de link. É possível para o Android registrar automaticamente um aplicativo como o manipulador padrão para um URI &ndash; o aplicativo é automaticamente iniciados e navegar diretamente para a atividade relevante. Como o Android 6.0 decide lidar com um clique URI depende dos critérios a seguir:

1. **Um aplicativo existente já está associado ao URI** &ndash; o usuário pode ter já está associado um aplicativo existente com um URI. Nesse caso, o Android continuará a usar esse aplicativo.
2. **Nenhum aplicativo existente está associado ao URI, mas um aplicativo de suporte está instalado** &ndash; neste cenário, o usuário não especificou um aplicativo existente, para Android usará o aplicativo instalado de suporte para manipular a solicitação.
3. **Nenhum aplicativo existente está associado ao URI, mas muitos aplicativos de suporte são instalados** &ndash; porque há vários aplicativos que dão suporte a URI, será exibida a caixa de diálogo ambiguidades e o usuário deve selecionar qual aplicativo será manipule o URI.

Se o usuário não tem nenhum aplicativo instalado que suportam o URI e subsequentemente um está instalado, em seguida, Android definirá esse aplicativo como o manipulador padrão para o URI depois de verificar a associação com o site que está associado ao URI.

Este guia sobre como configurar um aplicativo do Android 6.0 e como criar e publicar o arquivo de Links ativos digitais para dar suporte a vinculação de aplicativo no Android 6.0.

## <a name="requirements"></a>Requisitos

Este guia requer 6.1 xamarin e um aplicativo que tem como alvo o Android 6.0 (API nível 23) ou superior.

Vinculação de aplicativo é possível nas versões anteriores do Android usando o [pacote NuGet Rivets](https://www.nuget.org/packages/Rivets/) o Xamarin no armazenamento de componentes. O pacote rebites não é compatível com a vinculação de aplicativo no Android 6.0; ele não oferece suporte a vinculação de aplicativo do Android 6.0.

## <a name="configuring-app-linking-in-android-60"></a>Configurando a vinculação de aplicativo no Android 6.0

Configurar links de aplicativo no Android 6.0 envolve duas etapas principais:

1. **Adicionando filtros intenção de um ou mais para o URI do site** &ndash; os filtros intenção guia Android no como lidar com um clique de URL em um navegador móvel.
2. **Publicando um *JSON de Links ativos digitais* arquivo no site** &ndash; este é um arquivo que é carregado em um site e é usado pelo Android para verificar a relação entre o aplicativo móvel e o domínio do site. Sem isso, Android não é possível instalar o aplicativo como o identificador padrão para o URI. o usuário deve fazer isso manualmente.

<a name="configure-intent-filter" />

### <a name="configuring-the-intent-filter"></a>Configurando o filtro de tentativa

É necessário configurar um filtro intenção que mapeia um URI (ou um conjunto de URIs de possíveis) de um site para uma atividade em um aplicativo do Android. Em xamarin, essa relação é estabelecida pelo adorno de uma atividade com o [IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/). O filtro intenção deve declarar as seguintes informações:

* **`Intent.ActionView`** &ndash; Isso registrará o filtro tentativa para responder a solicitações para exibir informações
* **`Categories`** &ndash;  O filtro intenção deve registrar os dois **[Intent.CategoryBrowsable](https://developer.xamarin.com/api/field/Android.Content.Intent.CategoryBrowsable/)** e **[Intent.CategoryDefault](https://developer.xamarin.com/api/field/Android.Content.Intent.CategoryDefault/)** para poder corretamente lidar com a URL da web.
* **`DataScheme`** &ndash; O filtro intenção deve declarar `http` e/ou `https`. Esses são os esquemas válidos apenas dois.
* **`DataHost`** &ndash; Este é o domínio que os URIs será proveniente de.
* **`DataPathPrefix`** &ndash; Este é um caminho opcional para recursos no site.
* **`AutoVerify`** &ndash; O `autoVerify` atributo informa Android para verificar a relação entre o aplicativo e o site. Isso será abordado mais adiante.

O exemplo a seguir mostra como usar o [IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/) para lidar com links de `https://www.recipe-app.com/recipes` e `http://www.recipe-app.com/recipes`:

```csharp
[IntentFilter(new [] { Intent.ActionView },
              Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
              DataScheme = "http",
              DataHost = "recipe-app.com",
              DataPathPrefix = "/recipe",
              AutoVerify=true)]
public class RecipeActivity : Activity
{
    // Code for the activity omitted
}
```

Android verificará se cada host que é identificado pelos filtros intencionais no arquivo de ativos digitais no site antes de registrar o aplicativo como o manipulador padrão para um URI. Todos os filtros de tentativa devem passar pela verificação para que Android possa estabelecer o aplicativo como o manipulador padrão.

### <a name="creating-the-digital-assets-link-file"></a>Criando o arquivo de vínculo de ativos digitais

Android 6.0 vinculando o aplicativo requer que o Android Verifique a associação entre o aplicativo e o site antes de configurar o aplicativo como o manipulador padrão para o URI. Essa verificação ocorrerá quando o aplicativo é instalado pela primeira vez. O *Links ativos digitais* arquivo é um arquivo JSON que é hospedado pelo webdomain(s) relevantes.

> [!NOTE]
> O `android:autoVerify` atributo deve ser definido pelo filtro intenção &ndash; contrário Android não executará a verificação.

O arquivo é colocado pelo administrador do domínio no local **https://domain/.well-known/assetlinks.json**.

O arquivo de ativo Digital contém metadados necessários para o Android verificar a associação. Um **assetlinks.json** arquivo tem os seguintes pares chave-valor:

* `namespace` &ndash; o namespace do aplicativo Android.
* `package_name` &ndash; o nome do pacote do aplicativo Android (declarado no manifesto do aplicativo).
* `sha256_cert_fingerprints` &ndash; as impressões SHA256 do aplicativo assinado. Consulte o guia de [localizando o armazenamento de chaves MD5 ou SHA1 assinatura](~/android/deploy-test/signing/keystore-signature.md) para obter mais informações sobre como obter a impressão digital SHA1 de um aplicativo.

O trecho a seguir está um exemplo de **assetlinks.json** com um único aplicativo listadas:

```json
[
   {
      "relation":[
         "delegate_permission/common.handle_all_urls"
      ],
      "target":{
         "namespace":"android_app",
         "package_name":"com.example",
         "sha256_cert_fingerprints":[
            "14:6D:E9:83:C5:73:06:50:D8:EE:B9:95:2F:34:FC:64:16:A0:83:42:E6:1D:BE:A8:8A:04:96:B2:3F:CF:44:E5"
         ]
      }
   }
]
```

É possível registrar mais de uma impressão digital de SHA256 para dar suporte a diferentes versões ou versões do seu aplicativo. Este próximo **assetlinks.json** arquivo é um exemplo de registro de vários aplicativos:

```json
[
   {
      "relation":[
         "delegate_permission/common.handle_all_urls"
      ],
      "target":{
         "namespace":"android_app",
         "package_name":"example.com.puppies.app",
         "sha256_cert_fingerprints":[
            "14:6D:E9:83:C5:73:06:50:D8:EE:B9:95:2F:34:FC:64:16:A0:83:42:E6:1D:BE:A8:8A:04:96:B2:3F:CF:44:E5"
         ]
      }
   },
   {
      "relation":[
         "delegate_permission/common.handle_all_urls"
      ],
      "target":{
         "namespace":"android_app",
         "package_name":"example.com.monkeys.app",
         "sha256_cert_fingerprints":[
            "14:6D:E9:83:C5:73:06:50:D8:EE:B9:95:2F:34:FC:64:16:A0:83:42:E6:1D:BE:A8:8A:04:96:B2:3F:CF:44:E5"
         ]
      }
   }
]
```

O [site Links ativos digitais de Google](https://developers.google.com/digital-asset-links/tools/generator) tem uma ferramenta on-line que pode ajudá-lo a criar e testar o arquivo de ativos digitais.

### <a name="testing-app-links"></a>Teste de Links de aplicativo

Depois de implementar links de aplicativo, as várias partes devem ser testadas para garantir que funcionem conforme o esperado.

É possível confirmar se o arquivo de ativos digitais está formatado corretamente e hospedado usando a API de Links ativos digitais do Google, conforme mostrado neste exemplo:

```html
https://digitalassetlinks.googleapis.com/v1/statements:list?source.web.site=
  https://<WEB SITE ADDRESS>:&relation=delegate_permission/common.handle_all_urls
```

Há dois testes que podem ser executadas para garantir que os filtros de tentativa foi configurados corretamente e se o aplicativo está definido como o manipulador padrão para um URI:

1.  O arquivo de ativo Digital é hospedado corretamente conforme descrito acima. O primeiro teste enviará uma intenção que Android deve redirecionar para o aplicativo móvel. O aplicativo do Android deve iniciar e exibir a atividade registrada para a URL. Em um prompt de comando, digite:

    ```shell
    $ adb shell am start -a android.intent.action.VIEW \
        -c android.intent.category.BROWSABLE \
        -d "http://<domain1>/recipe/scalloped-potato"
    ```

2.  Exiba o link existente tratamento políticas para os aplicativos instalados em um determinado dispositivo. O comando a seguir irá despejar uma lista de políticas de link para cada usuário no dispositivo com as informações a seguir. No prompt de comando, digite o seguinte comando:

    ```shell
    $ adb shell dumpsys package domain-preferred-apps
    ```

    * **`Package`** &ndash; O nome do pacote do aplicativo.
    * **`Domain`** &ndash; Os domínios (separados por espaços) cujos links da web será tratado pelo aplicativo
    * **`Status`** &ndash; Este é o status atual de tratamento de link para o aplicativo. Um valor de **sempre** significa que o aplicativo tem `android:autoVerify=true` declarado e passar na verificação do sistema. Ele é seguido por um número hexadecimal que representa o registro do sistema Android da preferência.

    Por exemplo:

    ```shell
    $ adb shell dumpsys package domain-preferred-apps

    App linkages for user 0:
    Package: com.android.vending
    Domains: play.google.com market.android.com
    Status: always : 200000002
    ```

## <a name="summary"></a>Resumo

Este guia discutidos funciona como vinculação de aplicativo no Android 6.0. Ele coberto, em seguida, como configurar um aplicativo do Android 6.0 para dar suporte e responder aos links de aplicativo. Ele também descreve como testar a vinculação de aplicativo em um aplicativo do Android.


## <a name="related-links"></a>Links relacionados

- [Encontrando a assinatura MD5 ou SHA1 do repositório de chaves](~/android/deploy-test/signing/keystore-signature.md)
- [Atividades e tentativas](https://university.xamarin.com/classes#4)
- [AppLinks](http://applinks.org/)
- [Links de ativos digitais do Google](https://developers.google.com/digital-asset-links/)
- [Testador e gerador da lista de instrução](https://developers.google.com/digital-asset-links/tools/generator)
