---
title: Vinculação de aplicativo no Android
description: Este guia discutirá como o Android 6,0 dá suporte à vinculação de aplicativos, uma técnica que permite que os aplicativos móveis respondam a URLs em sites. Ele discutirá o que é a vinculação de aplicativos, como implementar a vinculação de aplicativos em um aplicativo Android 6,0 e como configurar um site para conceder permissões ao aplicativo móvel para um domínio.
ms.prod: xamarin
ms.assetid: 48174E39-19FD-43BC-B54C-9AF11D4B1F91
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: d65e8fabff88489571bba9d03379ff605a6ed0fe
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757725"
---
# <a name="app-linking-in-android"></a>Vinculação de aplicativo no Android

_Este guia discutirá como o Android 6,0 dá suporte à vinculação de aplicativos, uma técnica que permite que os aplicativos móveis respondam a URLs em sites. Ele discutirá o que é a vinculação de aplicativos, como implementar a vinculação de aplicativos em um aplicativo Android 6,0 e como configurar um site para conceder permissões ao aplicativo móvel para um domínio._

## <a name="app-linking-overview"></a>Visão geral da vinculação de aplicativos

Os aplicativos móveis não ficam mais ativos em &ndash; um silo em muitos casos, eles são componentes importantes de suas empresas, juntamente com seus sites. É desejável que as empresas conectem diretamente seus aplicativos móveis e de presença na Web, com links em um site que inicia aplicativos móveis e exibem conteúdo relevante no aplicativo móvel. *Vinculação de aplicativo* (também conhecido como *vinculação profunda*) é uma técnica que permite que um dispositivo móvel responda a um URI e inicie um aplicativo móvel que corresponda a esse URI.

O Android lida com a vinculação de aplicativos por meio do *sistema* &ndash; de intenção quando o usuário clica em um link em um navegador móvel, o navegador móvel irá despachar uma intenção que o Android delegará a um aplicativo registrado. Por exemplo, clicar em um link em um site de culinária abriria um aplicativo móvel associado a esse site e exibirá uma receita específica para o usuário. Se houver mais de um aplicativo registrado para lidar com essa intenção, o Android gerará o que é conhecido como uma *caixa de diálogo de desambiguidade* que solicitará a um usuário qual aplicativo selecionar o aplicativo que deve lidar com a intenção, por exemplo:

![Captura de tela de exemplo de uma caixa de diálogo de desambiguidade](app-linking-images/01-disambiguation-dialog.png)

O Android 6,0 melhora isso usando a manipulação automática de links. É possível que o Android registre automaticamente um aplicativo como o manipulador padrão para um URI &ndash; que o aplicativo iniciará automaticamente e navegue diretamente para a atividade relevante. Como o Android 6,0 decide manipular um clique de URI depende dos seguintes critérios:

1. **Um aplicativo existente já está associado ao URI** &ndash; O usuário pode já ter associado um aplicativo existente a um URI. Nesse caso, o Android continuará a usar esse aplicativo.
2. **Nenhum aplicativo existente está associado ao URI, mas um aplicativo de suporte está instalado** &ndash; Nesse cenário, o usuário não especificou um aplicativo existente, portanto, o Android usará o aplicativo de suporte instalado para lidar com a solicitação.
3. **Nenhum aplicativo existente está associado ao URI, mas muitos aplicativos de suporte estão instalados** &ndash; Como há vários aplicativos que dão suporte ao URI, a caixa de diálogo de desambiguidade será exibida e o usuário deverá selecionar qual aplicativo tratará o URI.

Se o usuário não tiver aplicativos instalados que ofereçam suporte ao URI e um for instalado posteriormente, o Android definirá esse aplicativo como o manipulador padrão para o URI depois de verificar a associação com o site associado ao URI.

Este guia discutirá como configurar um aplicativo Android 6,0 e como criar e publicar o arquivo de links de ativos digitais para dar suporte à vinculação de aplicativos no Android 6,0.

## <a name="requirements"></a>Requisitos

Este guia requer o Xamarin. Android 6,1 e um aplicativo que tem como alvo o Android 6,0 (API nível 23) ou superior.

A vinculação de aplicativo é possível em versões anteriores do Android usando o [pacote NuGet de rebites](https://www.nuget.org/packages/Rivets/) da loja de componentes do Xamarin. O pacote de rebites não é compatível com a vinculação de aplicativo no Android 6,0; Ele não dá suporte à vinculação de aplicativos Android 6,0.

## <a name="configuring-app-linking-in-android-60"></a>Configurando a vinculação de aplicativos no Android 6,0

A configuração de links de aplicativo no Android 6,0 envolve duas etapas principais:

1. **Adicionando um ou mais filtros de tentativa para o URI do site** &ndash; os filtros de intenção guiam o Android em como lidar com uma URL clique em um navegador móvel.
2. **Publicando um arquivo *JSON do Digital Asset links* no site** &ndash; , esse é um arquivo que é carregado em um site e é usado pelo Android para verificar a relação entre o aplicativo móvel e o domínio do site. Sem isso, o Android não pode instalar o aplicativo como o identificador padrão para URIS; o usuário deve fazer isso manualmente.

<a name="configure-intent-filter" />

### <a name="configuring-the-intent-filter"></a>Configurando o filtro de intenção

É necessário configurar um filtro de intenção que mapeia um URI (ou um conjunto de URIs) de um site para uma atividade em um aplicativo Android. No Xamarin. Android, essa relação é estabelecida com a adornação de uma atividade com o [IntentFilterAttribute](xref:Android.App.IntentFilterAttribute). O filtro de intenção deve declarar as seguintes informações:

- **`Intent.ActionView`** &ndash; Isso registrará o filtro de intenção para responder às solicitações para exibir informações
- **`Categories`** O filtro de intenção deve registrar tanto o **[intuito. CategoryBrowsable](xref:Android.Content.Intent.CategoryBrowsable)** quanto o **[intuito. CategoryDefault](xref:Android.Content.Intent.CategoryDefault)** para poder lidar corretamente com o URI da Web. &ndash;
- **`DataScheme`** O filtro de intenção deve `http` declarar e/ `https`ou. &ndash; Esses são os únicos dois esquemas válidos.
- **`DataHost`** &ndash; Esse é o domínio do qual os URIs serão originados.
- **`DataPathPrefix`** &ndash; Esse é um caminho opcional para os recursos no site.
- **`AutoVerify`** &ndash; O`autoVerify` atributo informa ao Android para verificar a relação entre o aplicativo e o site. Isso será discutido mais abaixo.

O exemplo a seguir mostra como usar o [IntentFilterAttribute](xref:Android.App.IntentFilterAttribute) para manipular links de `https://www.recipe-app.com/recipes` e `http://www.recipe-app.com/recipes`para:

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

O Android verificará todos os hosts identificados pelos filtros de intenção em relação ao arquivo de ativos digitais no site antes de registrar o aplicativo como o manipulador padrão para um URI. Todos os filtros de intenção devem passar na verificação antes que o Android possa estabelecer o aplicativo como o manipulador padrão.

### <a name="creating-the-digital-assets-link-file"></a>Criando o arquivo de link de ativos digitais

O aplicativo Android 6,0 – a vinculação exige que o Android Verifique a associação entre o aplicativo e o site antes de definir o aplicativo como o manipulador padrão para o URI. Essa verificação ocorrerá quando o aplicativo for instalado pela primeira vez. O arquivo de *links de ativos digitais* é um arquivo JSON que é hospedado pelos domínios da Web(s) relevantes.

> [!NOTE]
> O `android:autoVerify` atributo deve ser definido pelo filtro &ndash; de intenção, caso contrário, o Android não executará a verificação.

O arquivo é colocado pelo webmaster do domínio no local **https://domain/.well-known/assetlinks.json** .

O arquivo de ativo digital contém os metadados necessários para que o Android Verifique a associação. Um arquivo **assetlinks. JSON** tem os seguintes pares de chave-valor:

- `namespace`&ndash; o namespace do aplicativo Android.
- `package_name`&ndash; o nome do pacote do aplicativo Android (declarado no manifesto do aplicativo).
- `sha256_cert_fingerprints`&ndash; as impressões digitais SHA256 do aplicativo assinado. Consulte o guia [localizando a assinatura MD5 ou SHA1 do repositório de chaves](~/android/deploy-test/signing/keystore-signature.md) para obter mais informações sobre como obter a impressão digital SHA1 de um aplicativo.

O trecho a seguir é um exemplo de **assetlinks. JSON** com um único aplicativo listado:

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

É possível registrar mais de uma impressão digital SHA256 para dar suporte a diferentes versões ou compilações do seu aplicativo. Esse próximo arquivo **assetlinks. JSON** é um exemplo de registro de vários aplicativos:

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

O [site do Google Digital Asset links](https://developers.google.com/digital-asset-links/tools/generator) tem uma ferramenta online que pode ajudar na criação e no teste do arquivo de ativos digitais.

### <a name="testing-app-links"></a>Testando links de aplicativo

Depois de implementar links de aplicativo, as várias partes devem ser testadas para garantir que funcionem conforme o esperado.

É possível confirmar se o arquivo de ativos digitais está formatado corretamente e hospedado usando a API de links de ativos digitais do Google, conforme mostrado neste exemplo:

```html
https://digitalassetlinks.googleapis.com/v1/statements:list?source.web.site=
  https://<WEB SITE ADDRESS>:&relation=delegate_permission/common.handle_all_urls
```

Há dois testes que podem ser executados para garantir que os filtros de intenção tenham sido configurados corretamente e que o aplicativo esteja definido como o manipulador padrão para um URI:

1. O arquivo de ativo digital está adequadamente hospedado conforme descrito acima. O primeiro teste expedirá uma intenção que o Android deve redirecionar para o aplicativo móvel. O aplicativo do Android deve iniciar e exibir a atividade registrada para a URL. Em um prompt de comando, digite:

    ```shell
    $ adb shell am start -a android.intent.action.VIEW \
        -c android.intent.category.BROWSABLE \
        -d "http://<domain1>/recipe/scalloped-potato"
    ```

2. Exibe as políticas de tratamento de links existentes para os aplicativos instalados em um determinado dispositivo. O comando a seguir despejará uma lista de políticas de link para cada usuário no dispositivo com as informações a seguir. No prompt de comando, digite o seguinte comando:

    ```shell
    $ adb shell dumpsys package domain-preferred-apps
    ```

    - **`Package`** &ndash; O nome do pacote do aplicativo.
    - **`Domain`** &ndash; Os domínios (separados por espaços) cujos links da Web serão manipulados pelo aplicativo
    - **`Status`** &ndash; Este é o status atual de tratamento de links para o aplicativo. Um valor de **sempre** significa que o aplicativo `android:autoVerify=true` declarou e passou na verificação do sistema. Ele é seguido por um número hexadecimal que representa o registro do sistema Android da preferência.

    Por exemplo:

    ```shell
    $ adb shell dumpsys package domain-preferred-apps

    App linkages for user 0:
    Package: com.android.vending
    Domains: play.google.com market.android.com
    Status: always : 200000002
    ```

## <a name="summary"></a>Resumo

Este guia abordou como a vinculação de aplicativos funciona no Android 6,0. Em seguida, ele abordou como configurar um aplicativo Android 6,0 para dar suporte e responder a links de aplicativo. Ele também abordou como testar a vinculação de aplicativos em um aplicativo Android.

## <a name="related-links"></a>Links relacionados

- [Encontrando a assinatura MD5 ou SHA1 do repositório de chaves](~/android/deploy-test/signing/keystore-signature.md)
- [Atividades e intenções](https://university.xamarin.com/classes#4)
- [AppLinks](http://applinks.org/)
- [Links de ativos digitais do Google](https://developers.google.com/digital-asset-links/)
- [Gerador de lista de instruções e testador](https://developers.google.com/digital-asset-links/tools/generator)
