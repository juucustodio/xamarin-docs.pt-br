---
Título: "etapas de instalação-entrar com a Apple para Xamarin.Forms " Descrição: "entrar com a instalação da Apple difere dependendo das diferentes plataformas para as quais seu aplicativo móvel se destina".
MS. Prod: xamarin MS. AssetID: 8F712802-395B-469B-B5BE-C927AD1A8391 MS. Technology: xamarin-Forms autor: davidortinau MS. Author: daortin MS. Date: 09/10/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="setup-sign-in-with-apple-for-xamarinforms"></a>Instalação entre com a Apple paraXamarin.Forms

Este guia aborda a série de etapas necessárias para configurar seus aplicativos de plataforma cruzada para que sejam avançados de entrar com a Apple. Embora a configuração da Apple seja direta no portal do desenvolvedor da Apple, etapas adicionais são necessárias para criar uma relação segura entre o Android e a Apple. 

## <a name="apple-developer-setup"></a>Instalação do desenvolvedor da Apple

Antes de usar a entrada com a Apple em seus aplicativos, você precisará abordar algumas etapas de instalação na seção [certificados, identificadores & perfis](https://developer.apple.com/account/resources/) do portal do desenvolvedor da Apple.

### <a name="apple-sign-in-domain"></a>Domínio de entrada da Apple

Registre seu nome de domínio e verifique-o com a Apple na seção [mais](https://developer.apple.com/account/resources/services/list) da seção *certificados, identificadores & perfis* .

![Mais seções](sign-in-images/readme-signin-domain-configure.png)

Adicione seu domínio e clique em **registrar**.

![formulário de registro de domínio](sign-in-images/readme-signin-domain-more.png)

> [!NOTE]
> Se você vir um erro sobre o seu domínio não estar em conformidade com o SPF, você precisará adicionar um registro TXT do DNS do SPF ao seu domínio e aguardar sua propagação antes de continuar: o SPF TXT pode ser semelhante a este:`v=spf1 a a:myapp.com -all`

Em seguida, você precisará verificar a propriedade do domínio clicando em **baixar** para recuperar o `apple-developer-domain-association.txt` arquivo e carregá-lo na `.well-known` pasta do site do seu domínio.

Depois que o `.well-known/apple-developer-domain-association.txt` arquivo for carregado e acessível, você poderá clicar em **verificar** para que a Apple Verifique sua propriedade de domínio.

> [!NOTE]
> A Apple verificará a propriedade com `https://` . Verifique se você tem a instalação do SSL e se o arquivo está acessível por meio de uma URL segura.

Este processo foi concluído com êxito antes de continuar.

## <a name="setup-your-app-id"></a>Configurar sua ID do aplicativo

Na seção [identificadores](https://developer.apple.com/account/resources/identifiers/list) , crie um novo identificador e escolha IDs de **aplicativo**. Se você já tiver uma ID do aplicativo, escolha editá-la em vez disso.

![Criar uma nova ID do aplicativo](sign-in-images/readme-appid-create.png)

Habilite **a entrada com a Apple**. Provavelmente, você desejará usar a opção **habilitar como ID do aplicativo primário** .

![Habilitar a entrada com a Apple](sign-in-images/readme-appid-signin.png)

Salve as alterações de ID do aplicativo.

## <a name="create-a-service-id"></a>Criar uma ID de serviço

Na seção [identificadores](https://developer.apple.com/account/resources/identifiers/list/serviceId) , crie um novo identificador e escolha IDs de **serviço**.

![Criar uma nova ID de serviço](sign-in-images/readme-serviceid-create.png)

Dê à sua ID de serviços uma descrição e um identificador.  Esse identificador será seu `ServerId` .  Certifique-se de habilitar a **entrada com a Apple**.

Antes de continuar, clique em **Configurar** ao lado da opção _entrar com a Apple_ que você habilitou.

No painel de configuração, verifique se a **ID correta do aplicativo primário** está selecionada.

Em seguida, escolha o **domínio da Web** que você configurou anteriormente.

Por fim, adicione uma ou mais **URLs de retorno**.  Qualquer um `redirect_uri` que você use posteriormente deve ser registrado aqui exatamente como você o usa.  Certifique-se de incluir o `http://` ou `https://` na URL ao inseri-lo.

> [!NOTE]
> Para fins de teste, não é possível usar `127.0.0.1` `localhost` o ou o, mas você pode usar outros domínios, como `local.test` .  Se você optar por fazer isso, poderá editar o arquivo do computador `hosts` para resolver esse domínio fictício para seu endereço IP local.

![Configurar sua entrada na Apple](sign-in-images/readme-serviceid-configure.png)

Salve suas alterações quando terminar.

## <a name="create-a-key-for-your-services-id"></a>Criar uma chave para sua ID de serviços

Na seção [chaves](https://developer.apple.com/account/resources/authkeys/list) , crie uma nova **chave**.

Dê um nome à sua chave e habilite a **entrada com a Apple**.

![Criar uma nova chave](sign-in-images/readme-key-create.png)

Clique em **Configurar** ao lado de _entrar com a Apple_.

Verifique se a **ID do aplicativo primário** correta está selecionada e clique em **salvar**.

Clique em **continuar** e **Registre-se** para criar a nova chave.

Em seguida, você terá apenas uma oportunidade de baixar a chave que acabou de gerar.  Clique em **Download**.

![Chave de download](sign-in-images/readme-key-download.png)

Além disso, anote sua **ID de chave** nesta etapa. Isso será usado para o `KeyId` mais tarde.

Você terá baixado um `.p8` arquivo de chave.  Você pode abrir esse arquivo no bloco de notas ou VSCode para ver o conteúdo do texto.  Eles devem ter uma aparência semelhante a:

```
---
title: "Setup Steps - Sign In with Apple for Xamarin.Forms"
description: Sign In with Apple setup differs depending on the different platforms your mobile application targets.
ms.prod: xamarin
ms.assetid: 8F712802-395B-469B-B5BE-C927AD1A8391
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 09/10/2019
no-loc:
- "Xamarin.Forms"
- "Xamarin.Essentials"

-----END PRIVATE KEY-----
```

Nomeie essa chave `P8FileContents` e mantenha-a em um local seguro. Você o usará ao integrar esse serviço em seu aplicativo móvel.

## <a name="summary"></a>Resumo

Este artigo descreveu as etapas necessárias para configurar a entrada com a Apple para uso em seus Xamarin.Forms aplicativos.

## <a name="related-links"></a>Links relacionados

- [Entre com as diretrizes da Apple](https://developer.apple.com/design/human-interface-guidelines/sign-in-with-apple/overview/)
  