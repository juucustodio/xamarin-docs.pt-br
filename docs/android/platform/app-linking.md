---
title: Vinculação de aplicativo no Android
description: Este guia discute como Android 6.0 dá suporte a aplicativo-vinculando, uma técnica que permite que aplicativos móveis responda a URLs nos sites. Ele abordará vinculação qual aplicativo é como implementar a vinculação de aplicativo em um aplicativo do Android 6.0 e como configurar um site da Web para conceder permissões ao aplicativo móvel para um domínio.
ms.prod: xamarin
ms.assetid: 48174E39-19FD-43BC-B54C-9AF11D4B1F91
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: dd4ba236df8e5993c7f7ed86393eb66ce01db595
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111259"
---
# <a name="app-linking-in-android"></a>Vinculação de aplicativo no Android

_Este guia discute como Android 6.0 dá suporte a aplicativo-vinculando, uma técnica que permite que aplicativos móveis responda a URLs nos sites. Ele abordará vinculação qual aplicativo é como implementar a vinculação de aplicativo em um aplicativo do Android 6.0 e como configurar um site da Web para conceder permissões ao aplicativo móvel para um domínio._

## <a name="app-linking-overview"></a>Visão geral de vinculação do aplicativo

Aplicativos móveis não residem em silos &ndash; em muitos casos, eles são um componentes importantes de seus negócios, juntamente com seu site. É desejável que as empresas conectem uniformemente a sua presença na web e aplicativos móveis, com links em um site de inicialização de aplicativos móveis e exibindo conteúdo relevante no aplicativo móvel. *Aplicativo-vinculando* (também conhecido como *vinculação profunda*) é uma técnica que permite que um dispositivo móvel responder a um URI e iniciar um aplicativo móvel que corresponde ao URI.

Android manipula a vinculação de aplicativo por meio de *sistema de intenção* &ndash; quando o usuário clica em um link em um navegador móvel, o navegador móvel enviará uma intenção Android será delegado para um aplicativo registrado. Por exemplo, clicar em um link em um site de culinária abra um aplicativo móvel que está associado esse site e exibir uma receita específica ao usuário. Se não houver mais de um aplicativo registrado para lidar com essa intenção, em seguida, Android irá gerar o que é conhecido como um *caixa de diálogo de Desambiguidade* que solicitará que um usuário selecionar o aplicativo que deve tratar a intenção, para qual aplicativo exemplo:

![Captura de tela de exemplo de uma caixa de diálogo de Desambiguidade](app-linking-images/01-disambiguation-dialog.png)

Android 6.0 melhorou isso, usando a manipulação automática de link. É possível para o Android registrar automaticamente um aplicativo como o manipulador padrão para um URI &ndash; o aplicativo automaticamente iniciará e navegar diretamente para a atividade relevante. Como o Android 6.0 decide lidar com um clique URI depende dos critérios a seguir:

1. **Um aplicativo existente já está associado ao URI** &ndash; o usuário pode ter já está associado um aplicativo existente com um URI. Nesse caso, o Android continuará a usar esse aplicativo.
2. **Nenhum aplicativo existente está associado com o URI, mas um aplicativo de suporte está instalado** &ndash; nesse cenário, o usuário não especificou um aplicativo existente, para que o Android usará o aplicativo de suporte instalado para manipular a solicitação.
3. **Nenhum aplicativo existente está associado com o URI, mas muitos aplicativos de suporte estão instalados** &ndash; porque há vários aplicativos que dão suporte a URI, a caixa de diálogo de Desambiguidade será exibida e o usuário deve selecionar qual aplicativo será lidar com o URI.

Se o usuário não tem nenhum aplicativo instalado que dão suporte a URI e subsequentemente um está instalado, em seguida, Android definirá esse aplicativo como o manipulador padrão para o URI depois de verificar a associação com o site que está associado ao URI.

Este guia discute como configurar um aplicativo do Android 6.0 e como criar e publicar o arquivo de Links de ativos digitais para dar suporte à vinculação de aplicativo no Android 6.0.

## <a name="requirements"></a>Requisitos

Este guia exige um aplicativo que tem como alvo o Android 6.0 (API nível 23) e xamarin. Android 6.1 ou superior.

Vinculando o aplicativo é possível nas versões anteriores do Android usando o [pacote do NuGet Rivets](https://www.nuget.org/packages/Rivets/) do armazenamento de componente do Xamarin. O pacote rebites não é compatível com a vinculação de aplicativo no Android 6.0; ele não oferece suporte a vinculação de aplicativo do Android 6.0.

## <a name="configuring-app-linking-in-android-60"></a>Configurando o aplicativo de vinculação no Android 6.0

Configurar links de aplicativo no Android 6.0 envolve duas etapas principais:

1. **Adicionando filtros intenção de um ou mais para o URI do site** &ndash; os filtros de intenção guiam Android no como lidar com um clique de URL em um navegador móvel.
2. **Publicando um *JSON de Links ativos digitais* arquivo no site** &ndash; este é um arquivo que é carregado em um site da Web e é usado pelo Android para verificar a relação entre o aplicativo móvel e o domínio do site. Sem isso, Android não é possível instalar o aplicativo como o identificador padrão para o URI; o usuário deve fazer isso manualmente.

<a name="configure-intent-filter" />

### <a name="configuring-the-intent-filter"></a>Configurando o filtro de intenção

É necessário configurar um filtro intencional que mapeia um URI (ou possível um conjunto de URIs) de um site para uma atividade em um aplicativo Android. No xamarin. Android, essa relação é estabelecida pelo adorno de uma atividade com o [IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/). O filtro intencional deve declarar as seguintes informações:

* **`Intent.ActionView`** &ndash; Isso registrará o filtro intencional para responder a solicitações para exibir informações
* **`Categories`** &ndash;  O filtro intencional deve se registrar para ambos **[Intent.CategoryBrowsable](https://developer.xamarin.com/api/field/Android.Content.Intent.CategoryBrowsable/)** e **[Intent.CategoryDefault](https://developer.xamarin.com/api/field/Android.Content.Intent.CategoryDefault/)** para poder corretamente lidar com a URL da web.
* **`DataScheme`** &ndash; O filtro intencional deve declarar `http` e/ou `https`. Esses são os esquemas válidos apenas dois.
* **`DataHost`** &ndash; Esse é o domínio que os URIs serão originadas.
* **`DataPathPrefix`** &ndash; Isso é um caminho opcional para recursos no site.
* **`AutoVerify`** &ndash; O `autoVerify` atributo diz ao Android para verificar a relação entre o aplicativo e o site. Isso será discutido mais abaixo.

O exemplo a seguir mostra como usar o [IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/) para lidar com os links a partir `https://www.recipe-app.com/recipes` e de `http://www.recipe-app.com/recipes`:

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

Android verificará todos os hosts que é identificado pelos filtros intencionais contra o arquivo de ativos digitais no site antes de registrar o aplicativo como o manipulador padrão para um URI. Todos os filtros de intenção devem passar pela verificação para que Android possa estabelecer o aplicativo como o manipulador padrão.

### <a name="creating-the-digital-assets-link-file"></a>Criando o arquivo de Link de ativos digitais

Android 6.0 vinculando o aplicativo requer que o Android Verifique a associação entre o aplicativo e o site antes de configurar o aplicativo como o manipulador padrão para o URI. Essa verificação ocorrerá quando o aplicativo é instalado pela primeira vez. O *Links de ativos digitais* arquivo é um arquivo JSON que é hospedado pela webdomain(s) relevantes.

> [!NOTE]
> O `android:autoVerify` atributo deve ser definido pelo filtro intencional &ndash; caso contrário, o Android não executará a verificação.

O arquivo será colocado, o administrador do domínio no local **https://domain/.well-known/assetlinks.json**.

O arquivo do ativo Digital contém os metadados necessários para o Android verificar a associação. Uma **assetlinks.json** arquivo tem os seguintes pares chave-valor:

* `namespace` &ndash; o namespace do aplicativo Android.
* `package_name` &ndash; o nome do pacote do aplicativo Android (declarado no manifesto do aplicativo).
* `sha256_cert_fingerprints` &ndash; as impressões digitais de SHA256 do aplicativo assinado. Consulte o guia [Localizando seu repositório de chaves assinatura MD5 ou SHA1](~/android/deploy-test/signing/keystore-signature.md) para obter mais informações sobre como obter a impressão digital SHA1 de um aplicativo.

O trecho a seguir está um exemplo de **assetlinks.json** com um único aplicativo listado:

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

É possível registrar mais de uma impressão digital de SHA256 para dar suporte a diferentes versões ou compilações do seu aplicativo. O próximo passo **assetlinks.json** arquivo é um exemplo de registro de vários aplicativos:

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

O [site do Google Digital Asset Links](https://developers.google.com/digital-asset-links/tools/generator) tem uma ferramenta online que pode ajudar na criação e teste o arquivo de ativos digitais.

### <a name="testing-app-links"></a>Teste os Links de aplicativo

Depois de implementar links do aplicativo, as várias partes devem ser testadas para garantir que eles funcionem conforme o esperado.

É possível confirmar que o arquivo de ativos digitais está formatado corretamente e hospedado usando a API de Links ativos digitais do Google, conforme mostrado neste exemplo:

```html
https://digitalassetlinks.googleapis.com/v1/statements:list?source.web.site=
  https://<WEB SITE ADDRESS>:&relation=delegate_permission/common.handle_all_urls
```

Há dois testes que podem ser executadas para garantir que os filtros de intenção tenham sido configurados corretamente e se o aplicativo está definido como o manipulador padrão para um URI:

1.  O arquivo do ativo Digital está hospedado corretamente conforme descrito acima. O primeiro teste enviará uma intenção que Android deve redirecionar para o aplicativo móvel. O aplicativo Android deve iniciar e exibir a atividade registrada para a URL. Em um prompt de comando, digite:

    ```shell
    $ adb shell am start -a android.intent.action.VIEW \
        -c android.intent.category.BROWSABLE \
        -d "http://<domain1>/recipe/scalloped-potato"
    ```

2.  Exiba o link existente tratamento políticas para os aplicativos instalados em um determinado dispositivo. O comando a seguir despejará uma listagem de diretivas de link para cada usuário no dispositivo com as informações a seguir. No prompt de comando, digite o seguinte comando:

    ```shell
    $ adb shell dumpsys package domain-preferred-apps
    ```

    * **`Package`** &ndash; O nome do pacote do aplicativo.
    * **`Domain`** &ndash; Os domínios (separados por espaços) cujos links da web será tratado pelo aplicativo
    * **`Status`** &ndash; Isso é o status atual de manipulação de link para o aplicativo. Um valor de **sempre** significa que o aplicativo tem `android:autoVerify=true` declarado e passar na verificação do sistema. Ele é seguido por um número hexadecimal que representa o registro do sistema Android da preferência.

    Por exemplo:

    ```shell
    $ adb shell dumpsys package domain-preferred-apps

    App linkages for user 0:
    Package: com.android.vending
    Domains: play.google.com market.android.com
    Status: always : 200000002
    ```

## <a name="summary"></a>Resumo

Este guia abordou como vinculação de aplicativo funciona no Android 6.0. Em seguida, ele abordou a como configurar um aplicativo do Android 6.0 para dar suporte e responder aos links de aplicativo. Ele também mostrou como testar a vinculação de aplicativo em um aplicativo Android.


## <a name="related-links"></a>Links relacionados

- [Encontrando a assinatura MD5 ou SHA1 do repositório de chaves](~/android/deploy-test/signing/keystore-signature.md)
- [Atividades e intenções](https://university.xamarin.com/classes#4)
- [AppLinks](http://applinks.org/)
- [Links de ativos digitais do Google](https://developers.google.com/digital-asset-links/)
- [Testador e gerador de lista de instrução](https://developers.google.com/digital-asset-links/tools/generator)
