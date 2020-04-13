---
title: Vinculação de aplicativos no Android
description: Este guia discutirá como o Android 6.0 suporta o link de aplicativos, uma técnica que permite que aplicativos móveis respondam a URLs em sites. Ele discutirá o que é vinculação de aplicativos, como implementar o link de aplicativos em um aplicativo Android 6.0 e como configurar um site para conceder permissões ao aplicativo móvel para um domínio.
ms.prod: xamarin
ms.assetid: 48174E39-19FD-43BC-B54C-9AF11D4B1F91
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: af90c286d2bb960a9f78547dd15c3d98a69529ae
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "75487822"
---
# <a name="app-linking-in-android"></a>Vinculação de aplicativos no Android

_Este guia discutirá como o Android 6.0 suporta o link de aplicativos, uma técnica que permite que aplicativos móveis respondam a URLs em sites. Ele discutirá o que é vinculação de aplicativos, como implementar o link de aplicativos em um aplicativo Android 6.0 e como configurar um site para conceder permissões ao aplicativo móvel para um domínio._

## <a name="app-linking-overview"></a>Visão geral do link de aplicativos

Os aplicativos móveis não vivem mais &ndash; em um silo, em muitos casos, eles são um componente importante de seus negócios, juntamente com seu site. É desejável que as empresas conectem perfeitamente sua presença web e aplicativos móveis, com links em um site lançando aplicativos móveis e exibindo conteúdo relevante no aplicativo móvel. *O app-linking* (também chamado *de deep-linking)* é uma técnica que permite que um dispositivo móvel responda a um URI e lance um aplicativo móvel que corresponda a esse URI.

O Android lida com a vinculação de aplicativos através do *sistema* &ndash; de intenções quando o usuário clica em um link em um navegador móvel, o navegador móvel enviará uma intenção que o Android delegará para um aplicativo registrado. Por exemplo, clicar em um link em um site de culinária abriria um aplicativo móvel associado a esse site e exibiria uma receita específica para o usuário. Se houver mais de um aplicativo registrado para lidar com essa intenção, então o Android levantará o que é conhecido como *diálogo de desambiguação* que perguntará a um usuário qual aplicativo selecionará o aplicativo que deve lidar com a intenção, por exemplo:

![Exemplo de captura de tela de uma caixa de diálogo de desambiguação](app-linking-images/01-disambiguation-dialog.png)

O Android 6.0 melhora isso usando o manuseio automático do link. É possível que o Android registre automaticamente um aplicativo &ndash; como o manipulador padrão de um URI, o aplicativo iniciará automaticamente e navegará diretamente para a Atividade relevante. Como o Android 6.0 decide lidar com um clique uri depende dos seguintes critérios:

1. **Um aplicativo existente já está associado ao URI** &ndash; O usuário pode já ter associado um aplicativo existente a um URI. Nesse caso, o Android continuará a usar esse aplicativo.
2. **Nenhum aplicativo existente está associado ao URI, mas um aplicativo de suporte é instalado** &ndash; Neste cenário, o usuário não especificou um aplicativo existente, então o Android usará o aplicativo de suporte instalado para lidar com a solicitação.
3. **Nenhum aplicativo existente está associado ao URI, mas muitos aplicativos de suporte estão instalados** &ndash; Como existem vários aplicativos que suportam o URI, a caixa de diálogo de desambiguação será exibida e o usuário deve selecionar qual aplicativo irá lidar com o URI.

Se o usuário não tiver nenhum aplicativo instalado que suporte o URI e um for instalado posteriormente, o Android definirá esse aplicativo como o manipulador padrão para o URI depois de verificar a associação com o site associado ao URI.

Este guia discutirá como configurar um aplicativo Android 6.0 e como criar e publicar o arquivo Digital Asset Links para suportar o vinculação de aplicativos no Android 6.0.

## <a name="requirements"></a>Requisitos

Este guia requer Xamarin.Android 6.1 e um aplicativo que tem como alvo o Android 6.0 (Nível API 23) ou superior.

O app-linking é possível em versões anteriores do Android usando o [pacote Rebite NuGet](https://www.nuget.org/packages/Rivets/) da loja De Componentes Xamarin. O pacote Rebites não é compatível com o link de aplicativo no Android 6.0; ele não suporta a vinculação do aplicativo Android 6.0.

## <a name="configuring-app-linking-in-android-60"></a>Configuração de App-Linking no Android 6.0

A configuração de links de aplicativos no Android 6.0 envolve dois passos principais:

1. **Adicionando um ou mais filtros de intenção para o site Uri os** &ndash; filtros de intenção guiam o Android em como lidar com um clique de URL em um navegador móvel.
2. **Publicando um arquivo *JSON de Digital Asset Links* no site** &ndash; este é um arquivo que é carregado em um site e é usado pelo Android para verificar a relação entre o aplicativo móvel e o domínio do site. Sem isso, o Android não pode instalar o aplicativo como o cabo padrão para URI's; o usuário deve fazê-lo manualmente.

<a name="configure-intent-filter" />

### <a name="configuring-the-intent-filter"></a>Configuração do filtro de intenções

É necessário configurar um filtro de intenção que mapeie um URI (ou possível um conjunto de URIs) de um site para uma atividade em um aplicativo Android. No Xamarin.Android, essa relação é estabelecida adornando uma atividade com o [IntentFilterAttribute](xref:Android.App.IntentFilterAttribute). O filtro de intenções deve declarar as seguintes informações:

- **`Intent.ActionView`**&ndash; Isso registrará o filtro de intenções para responder às solicitações de visualização de informações
- **`Categories`**&ndash; O filtro de intenções deve registrar **[intenção.CategoryBrowsable](xref:Android.Content.Intent.CategoryBrowsable)** e **[Intention.CategoryDefault](xref:Android.Content.Intent.CategoryDefault)** para poder lidar adequadamente com o URI da Web.
- **`DataScheme`**&ndash; O filtro de `http` intenções `https`deve declarar e/ou . Estes são os dois únicos esquemas válidos.
- **`DataHost`**&ndash; Este é o domínio do qual as URIs se originarão.
- **`DataPathPrefix`**&ndash; Este é um caminho opcional para recursos no site.
- **`AutoVerify`**&ndash; O `autoVerify` atributo diz ao Android para verificar a relação entre o aplicativo e o site. Isso será discutido mais abaixo.

O exemplo a seguir mostra como usar o [IntentFilterAttribute](xref:Android.App.IntentFilterAttribute) para lidar com links de `https://www.recipe-app.com/recipes` e de `http://www.recipe-app.com/recipes`:

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

O Android verificará cada host identificado pelos filtros de intenção contra o Arquivo de Ativos Digitais no site antes de registrar o aplicativo como o manipulador padrão de um URI. Todos os filtros de intenção devem passar pela verificação antes que o Android possa estabelecer o aplicativo como o manipulador padrão.

### <a name="creating-the-digital-assets-link-file"></a>Criando o arquivo de link de ativos digitais

O link de aplicativo do Android 6.0 requer que o Android verifique a associação entre o aplicativo e o site antes de definir o aplicativo como o manipulador padrão para o URI. Essa verificação ocorrerá quando o aplicativo for instalado pela primeira vez. O arquivo *Digital Assets Links* é um arquivo JSON hospedado pelos domínios web relevantes.

> [!NOTE]
> O `android:autoVerify` atributo deve ser &ndash; definido pelo filtro de intenção caso contrário o Android não realizará a verificação.

O arquivo é colocado pelo webmaster do **https://domain/.well-known/assetlinks.json**domínio no local .

O Arquivo de Ativos Digitais contém os meta-dados necessários para que o Android verifique a associação. Um arquivo **assetlinks.json** tem os seguintes pares de valor de chave:

- `namespace`&ndash; o namespace do aplicativo para Android.
- `package_name`&ndash; o nome do pacote do aplicativo para Android (declarado no manifesto do aplicativo).
- `sha256_cert_fingerprints`&ndash; as impressões digitais SHA256 do aplicativo assinado. Consulte o guia Encontrar a [Assinatura MD5 ou SHA1 do seu Keystore](~/android/deploy-test/signing/keystore-signature.md) para obter mais informações sobre como obter a impressão digital SHA1 de um aplicativo.

O seguinte trecho é um exemplo de **assetlinks.json** com um único aplicativo listado:

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

É possível registrar mais de uma impressão digital SHA256 para suportar diferentes versões ou compilações do seu aplicativo. Este próximo arquivo **assetlinks.json** é um exemplo de registro de vários aplicativos:

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

O [site do Google Digital Asset Links](https://developers.google.com/digital-asset-links/tools/generator) possui uma ferramenta online que pode auxiliar na criação e teste do arquivo Digital Assets.

### <a name="testing-app-links"></a>Testando links de aplicativos

Depois de implementar links de aplicativos, as várias peças devem ser testadas para garantir que funcionem como esperado.

É possível confirmar que o arquivo Digital Assets está devidamente formatado e hospedado usando a API Digital Asset Links do Google, como mostrado neste exemplo:

```html
https://digitalassetlinks.googleapis.com/v1/statements:list?source.web.site=
  https://<WEB SITE ADDRESS>:&relation=delegate_permission/common.handle_all_urls
```

Existem dois testes que podem ser realizados para garantir que os filtros de intenção foram configurados corretamente e que o aplicativo é definido como o manipulador padrão de um URI:

1. O Arquivo de Ativos Digitais está devidamente hospedado conforme descrito acima. O primeiro teste enviará uma intenção que o Android deve redirecionar para o aplicativo móvel. O aplicativo para Android deve iniciar e exibir a Atividade registrada para a URL. Em um tipo de prompt de comando:

    ```shell
    $ adb shell am start -a android.intent.action.VIEW \
        -c android.intent.category.BROWSABLE \
        -d "http://<domain1>/recipe/scalloped-potato"
    ```

2. Exibir as políticas de manipulação de links existentes para os aplicativos instalados em um determinado dispositivo. O comando a seguir dispensará uma lista de políticas de link para cada usuário no dispositivo com as seguintes informações. No prompt de comando, digite o comando a seguir:

    ```shell
    $ adb shell dumpsys package domain-preferred-apps
    ```

    - **`Package`**&ndash; O nome do pacote do aplicativo.
    - **`Domain`**&ndash; Os domínios (separados por espaços) cujos links da Web serão manipulados pelo aplicativo
    - **`Status`**&ndash; Este é o status atual de manipulação de links para o aplicativo. Um valor de **sempre** significa `android:autoVerify=true` que o aplicativo declarou e passou pela verificação do sistema. É seguido por um número hexadecimal representando o registro de preferência do sistema Android.

    Por exemplo:

    ```shell
    $ adb shell dumpsys package domain-preferred-apps

    App linkages for user 0:
    Package: com.android.vending
    Domains: play.google.com market.android.com
    Status: always : 200000002
    ```

## <a name="summary"></a>Resumo

Este guia discutiu como o app-linking funciona no Android 6.0. Em seguida, cobriu como configurar um aplicativo Android 6.0 para suportar e responder a links de aplicativos. Ele também discutiu como testar a vinculação de aplicativos em um aplicativo Android.

## <a name="related-links"></a>Links relacionados

- [Encontrando a assinatura MD5 ou SHA1 do repositório de chaves](~/android/deploy-test/signing/keystore-signature.md)
- [AppLinks](https://developers.facebook.com/docs/applinks)
- [Links de ativos digitais do Google](https://developers.google.com/digital-asset-links/)
- [Gerador e testador da lista de declarações](https://developers.google.com/digital-asset-links/tools/generator)
