---
title: Carregando o APK Manualmente
ms.prod: xamarin
ms.assetid: 1309C251-ABF0-4412-B1F5-200DC8321A9D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/15/2018
ms.openlocfilehash: b54296da3f1253b77b63363583c34e1548cd6abc
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76724078"
---
# <a name="manually-uploading-the-apk"></a>Carregando o APK Manualmente

Na primeira vez que um APK for enviado ao Google Play (ou se uma versão anterior do Xamarin.Android for usada), o APK deverá ser carregado manualmente por meio do [Console do Desenvolvedor do Google Play](https://play.google.com/apps/publish).
Esse guia explica as etapas necessárias para esse processo.

## <a name="google-play-developer-console"></a>Console do Desenvolvedor do Google Play

Depois que o APK tiver sido compilado e os ativos promocionais preparados, o aplicativo deverá ser carregado no Google Play. Isso é feito ao fazer logon no [Console do Desenvolvedor do Google Play](https://play.google.com/apps/publish) mostrado a seguir. Clique no botão **Publicar um Aplicativo Android no Google Play** para inicializar o processo de distribuição de um aplicativo.

[![Console do Desenvolvedor do Google Play](manually-uploading-the-apk-images/00-google-play-developer-console-sml.png)](manually-uploading-the-apk-images/00-google-play-developer-console.png#lightbox)

Se você já tiver um aplicativo existente registrado com o Google Play, clique no botão **Adicionar novo aplicativo**:

[![Botão Adicionar novo aplicativo](manually-uploading-the-apk-images/01-existing-app-sml.png)](manually-uploading-the-apk-images/01-existing-app.png#lightbox)

Quando a caixa de diálogo **ADICIONAR NOVO APLICATIVO** for exibida, insira o nome do aplicativo e clique em **Carregar APK**:

[![Botão Carregar APK](manually-uploading-the-apk-images/02-add-new-application-sml.png)](manually-uploading-the-apk-images/02-add-new-application.png#lightbox)

A próxima tela permite que o aplicativo seja publicado para testes alfa, testes beta ou para produção. No exemplo a seguir, a guia **TESTES ALFA** está selecionada. Visto que o **MyApp** não usa serviços de licenciamento, o botão **Obter chave de licença** não precisa ser clicado nesse exemplo. Aqui, o botão **Carregar seu primeiro APK em Alfa** é clicado para publicar no canal Alfa:

[![Botão Carregar seu primeiro APK para Alfa](manually-uploading-the-apk-images/03-upload-to-alpha-sml.png)](manually-uploading-the-apk-images/03-upload-to-alpha.png#lightbox)

A caixa de diálogo **CARREGAR NOVO APK EM ALFA** é exibida. O APK pode ser carregado ao clicar no botão **Procurar arquivos** ou ao arrastar e soltar o APK:

[![Caixa de diálogo Carregar novo APK para Alfa](manually-uploading-the-apk-images/04-upload-dialog-sml.png)](manually-uploading-the-apk-images/04-upload-dialog.png#lightbox)

Certifique-se de carregar o APK pronto para liberação que deverá ser distribuído.
A próxima caixa de diálogo indica o progresso do upload do APK:

[![Indicação de Progresso do Upload](manually-uploading-the-apk-images/05-upload-progress-sml.png)](manually-uploading-the-apk-images/05-upload-progress.png#lightbox)

Depois que o APK for carregado, será possível selecionar um método de teste:

[![Caixa de diálogo Escolher um Método de Teste](manually-uploading-the-apk-images/06-select-testing-method-sml.png)](manually-uploading-the-apk-images/06-select-testing-method.png#lightbox)

Para obter mais informações sobre o teste do aplicativo, confira o guia do Google [Set up alpha/beta tests](https://support.google.com/googleplay/android-developer/answer/3131213?hl=en) (Configurar testes alfa/beta).

Depois que o APK for carregado, ele será salvo como um rascunho. Ele não poderá ser publicado até que mais detalhes sejam fornecidos ao Google Play, conforme descrito a seguir.

## <a name="store-listing"></a>Listagem da Loja

Clique em **Listagem da Loja** no **Console do Desenvolvedor do Google Play** para inserir as informações que o Google Play exibirá aos usuários potenciais do aplicativo:

[![Caixa de diálogo Listagem da Loja](manually-uploading-the-apk-images/07-store-listing-sml.png)](manually-uploading-the-apk-images/07-store-listing.png#lightbox)

### <a name="graphics-assets"></a>Elementos Gráficos Ativos

Role para baixo até a seção **GRÁFICOS ATIVOS** da página **Listagem da Loja**:

[![Caixa de diálogo Ativos Gráficos](manually-uploading-the-apk-images/08-graphic-assets-sml.png)](manually-uploading-the-apk-images/08-graphic-assets.png#lightbox)

Todos os ativos promocionais que foram preparados anteriormente são carregados nessa seção. São fornecidas diretrizes sobre quais ativos promocionais deverão ser oferecidos e em qual formato eles deverão ser disponibilizados.

### <a name="categorization"></a>Categorização

Após a seção **GRÁFICOS ATIVOS**, selecione o tipo e a categoria do aplicativo na seção chamada **CATEGORIZAÇÃO**:

[![Seção Categorização](manually-uploading-the-apk-images/09-categorization-sml.png)](manually-uploading-the-apk-images/09-categorization.png#lightbox)

A classificação de conteúdo é abordada após a próxima seção.

### <a name="contact-details"></a>Detalhes do Contato

A seção final dessa página é uma seção de **DETALHES DO CONTATO**. Essa seção é usada para coletar informações de contato sobre o desenvolvedor do aplicativo:

[![Seção Detalhes do Contato](manually-uploading-the-apk-images/10-contact-details-sml.png)](manually-uploading-the-apk-images/10-contact-details.png#lightbox)

É possível fornecer uma URL para a política de privacidade do aplicativo na seção **POLÍTICA de PRIVACIDADE**, conforme indicado acima.

## <a name="content-rating"></a>Classificação de Conteúdo

Clique em **Classificação de Conteúdo** no **Console do Desenvolvedor do Google Play**. Nessa página, especifique a classificação de conteúdo para seu aplicativo. O Google Play exige que todos os aplicativos especifiquem uma classificação de conteúdo. Clique no botão **Continuar** para concluir o questionário de classificação de conteúdo:

[![Seção Classificação de Conteúdo](manually-uploading-the-apk-images/11-content-rating-sml.png)](manually-uploading-the-apk-images/11-content-rating.png#lightbox)

Todos os aplicativos no Google Play devem ser classificados de acordo com o sistema de classificação do Google Play. Além da classificação de conteúdo, todos os aplicativos devem aderir à [Política de Conteúdo do Desenvolvedor](https://www.android.com/us/developer-content-policy.html) do Google.

A seguir estão listados os quatro níveis do sistema de classificação do Google Play e são fornecidas algumas diretrizes, como os recursos ou conteúdo que exigem ou impõem o nível de classificação:

- **Todos** &ndash; não pode acessar, publicar nem compartilhar dados de localização. Não pode hospedar nenhum conteúdo gerado pelo usuário. Não pode habilitar a comunicação entre usuários.

- **Maturidade baixa** &ndash; aplicativos que acessam dados de localização, mas não os compartilham. Representações de violência leve ou animada.

- **Maturidade média** &ndash; referências a drogas, álcool ou tabaco. Temas de jogos de azar ou jogos de azar simulados. Conteúdo inflamatório. Profanidade ou humor grosseiro. Referências sugestivas ou sexuais.
    Violência intensa fantasiosa. Violência realista. Permite que os usuários localizem uns aos outros. Permite que os usuários se comuniquem entre si.
    Compartilhamento dos dados de localização de um usuário.

- **Maturidade alta** &ndash; um foco no consumo ou na venda de álcool, tabaco ou drogas. Um foco em referências sugestivas ou sexuais. Violência gráfica.

Os itens na lista de maturidade média são subjetivos, de modo que é possível que uma diretriz que pareça ditar uma classificação de maturidade média seja suficientemente intensa para merecer uma classificação de maturidade alta.

## <a name="pricing-amp-distribution"></a>Preços &amp; Distribuição

Clique em **Preços e Distribuição** no **Console do Desenvolvedor do Google Play**. Nessa página, defina um preço, caso o aplicativo seja pago.
Como alternativa, o aplicativo pode ser distribuído gratuitamente para todos os usuários. Uma vez que um aplicativo for especificado como gratuito, ele deverá permanecer gratuito.
O Google Play não permitirá que um aplicativo gratuito seja transformado em um aplicativo pago (no entanto, é possível vender conteúdo com faturamento no aplicativo em um aplicativo gratuito). O Google Play permitirá que um aplicativo pago seja transformado em um aplicativo gratuito a qualquer momento.

É necessário configurar uma conta comercial antes de publicar um aplicativo pago. Para fazer isso, clique em **Configurar uma conta comercial** e siga as instruções.

[![Caixa de diálogo Preços e Distribuição](manually-uploading-the-apk-images/12-pricing-sml.png)](manually-uploading-the-apk-images/12-pricing.png#lightbox)

### <a name="manage-countries"></a>Gerenciar Países

A próxima seção, **Gerenciar Países**, fornece controle sobre em quais países um aplicativo poderá ser distribuído:

[![Caixa de diálogo Gerenciar países](manually-uploading-the-apk-images/13-manage-countries-sml.png)](manually-uploading-the-apk-images/13-manage-countries.png#lightbox)

### <a name="other-information"></a>Outras Informações

Role mais para baixo para especificar se o aplicativo contém anúncios. Além disso, a seção **CATEGORIAS DO DISPOSITIVO** fornece opções para distribuir o aplicativo para o Android Wear, o Android TV ou o Android Auto, caso queira fazê-lo:

[![Seção Contém Anúncios](manually-uploading-the-apk-images/14-contains-ads-sml.png)](manually-uploading-the-apk-images/14-contains-ads.png#lightbox)

Após essa seção, há outras opções que podem ser selecionadas, como a alternativa **Projetado para famílias**, bem como distribuir o aplicativo por intermédio do Google Play for Education.

### <a name="consent"></a>Consentimento

Na parte inferior da página **Preços &amp; Distribuição**, está a seção **CONSENTIMENTO**.
Essa é uma seção obrigatória, utilizada para declarar que o aplicativo atende às [Diretrizes de Conteúdo do Android](https://play.google.com/about/restricted-content/) e reconhecer que o aplicativo está sujeito às leis de exportação dos Estados Unidos:

[![Seção Consentimento](manually-uploading-the-apk-images/15-consent-sml.png)](manually-uploading-the-apk-images/15-consent.png#lightbox)

Há muito mais sobre a publicação de um aplicativo Xamarin.Android do que pode ser abordado nesse guia.
Para obter mais informações sobre como publicar seu aplicativo no Google Play, confira [Welcome to the Google Play Developer Console Help Center](https://support.google.com/googleplay/android-developer#topic=3450769) (Bem-vindo ao Centro de Ajuda do Console do Desenvolvedor do Google Play).

## <a name="google-play-filters"></a>Filtros do Google Play

Quando os usuários buscam por aplicativos no site do Google Play, eles podem pesquisar todos os aplicativos publicados. Quando os usuários navegam no Google Play de um dispositivo Android, os resultados variam um pouco. Os resultados serão filtrados de acordo com a compatibilidade com o dispositivo que está sendo usado. Por exemplo, se um aplicativo tiver que enviar mensagens SMS, o Google Play não mostrará tal aplicativo para nenhum dispositivo que não possa enviar mensagens SMS. Os filtros que são aplicados a uma pesquisa são criados:

1. Da configuração de hardware do dispositivo.
2. Das declarações no arquivo de manifesto dos aplicativos.
3. Da operadora utilizada (se houver).
4. Do local do dispositivo.

É possível adicionar elementos ao manifesto do aplicativo para ajudar a controlar como o aplicativo é filtrado na Google Play Store. A seguir estão listados os elementos e atributos do manifesto que podem ser usados para filtrar aplicativos:

- [supports-screen](https://developer.android.com/guide/topics/manifest/supports-screens-element.html) &ndash; o Google Play usará os atributos para determinar se um aplicativo poderá ser implantado em um dispositivo com base no tamanho da tela.
    O Google Play pressupõe que o Android pode adaptar um layout menor a telas maiores, mas não vice-versa. Desse modo, um aplicativo que declara o suporte para telas normais aparecerá em pesquisas por telas grandes, mas não em pesquisas por telas pequenas. Se um aplicativo Xamarin.Android não fornecer um elemento `<supports-screen>` no arquivo de manifesto, o Google Play presumirá que todos os atributos têm valor true e que o aplicativo dá suporte a todos os tamanhos de tela. Esse elemento deve ser adicionado manualmente ao **AndroidManifest.xml**.

- [uses-configuration](https://developer.android.com/guide/topics/manifest/uses-configuration-element.html) &ndash; esse elemento de manifesto é usado para solicitar determinados recursos de hardware, como o tipo de teclado, dispositivos de navegação, uma tela sensível ao toque etc. Esse elemento deve ser adicionado manualmente ao **AndroidManifest.xml**.

- [uses-feature](https://developer.android.com/guide/topics/manifest/uses-feature-element.html) &ndash; esse elemento de manifesto declara os recursos de hardware ou software que um dispositivo deve ter para que o aplicativo funcione. Esse atributo é apenas informativo. O Google Play não exibirá o aplicativo em dispositivos que não satisfaçam esse filtro. Ainda é possível instalar o aplicativo por outros meios (manualmente ou baixando). Esse elemento deve ser adicionado manualmente ao **AndroidManifest.xml**.

- [uses-library](https://developer.android.com/guide/topics/manifest/uses-library-element.html) &ndash; esse elemento especifica que certas bibliotecas compartilhadas devem estar presentes no dispositivo, por exemplo, Google Maps. Esse elemento também pode ser especificado com o `Android.App.UsesLibraryAttribute`. Por exemplo:

    ```csharp
    [assembly: UsesLibrary("com.google.android.maps", true)]
    ```

- [uses-permission](https://developer.android.com/guide/topics/manifest/uses-permission-element.html) &ndash; esse elemento é usado para inferir determinados recursos de hardware necessários para a execução do aplicativo e que talvez não tenham sido adequadamente declarados com um elemento `<uses-feature>`. Por exemplo, se um aplicativo solicitar permissão para usar a câmera, o Google Play presumirá que os dispositivos devem ter uma câmera, mesmo se não houver nenhum elemento `<uses-feature>` declarando a câmera. Esse elemento pode ser definido com o `Android.App.UsesPermissionsAttribute`. Por exemplo:

    ```csharp
    [assembly: UsesPermission(Manifest.Permission.Camera)]
    ```

- [uses-sdk](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html) &ndash; o elemento é usado para declarar o nível mínimo da API do Android necessário para o aplicativo. Esse elemento pode ser definido nas opções de um projeto Xamarin.Android.

- [compatible-screens](https://developer.android.com/guide/topics/manifest/compatible-screens-element.html) &ndash; esse elemento é usado para filtrar os aplicativos que não correspondem nem à densidade, nem ao tamanho da tela especificados por este elemento. A maioria dos aplicativos não deve usar este filtro. Ele destina-se a jogos de alto desempenho ou aplicativos específicos que exigiram controles estritos na distribuição do aplicativo. O atributo `<support-screen>` mencionado acima é preferível.

- [supports-gl-texture](https://developer.android.com/guide/topics/manifest/supports-gl-texture-element.html) &ndash; esse elemento é usado para declarar as formações de compactação de textura GL que o aplicativo exige. A maioria dos aplicativos não deve usar este filtro. Ele destina-se a jogos de alto desempenho ou aplicativos específicos que exigiram controles estritos na distribuição do aplicativo.

Para obter mais informações sobre como configurar o manifesto do aplicativo, confira o tópico sobre o [Manifesto do Aplicativo](https://developer.android.com/guide/topics/manifest/manifest-intro.html) do Android.
