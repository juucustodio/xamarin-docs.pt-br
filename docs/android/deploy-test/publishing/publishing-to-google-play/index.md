---
title: Publicando no Google Play
ms.prod: xamarin
ms.assetid: FB1CC234-3554-8566-48BD-2B9B3A28CC7F
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 04e83fc68218216fe36cce67e43b83e8ad8feaa5
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "80070847"
---
# <a name="publishing-to-google-play"></a>Publicando no Google Play

Embora existam muitos mercados de aplicativo para a distribuição de um aplicativo, o Google Play é indiscutivelmente o maior e mais visitado repositório do mundo para aplicativos Android. O Google Play fornece uma plataforma única para distribuição, publicidade, vendas e análise das vendas de um aplicativo Android.

Esta seção abordará os tópicos específicos para o Google Play, como o registro para se tornar um editor, a coleta de ativos para ajudar o Google Play a promover e anunciar o seu aplicativo, as diretrizes para a classificação do seu aplicativo no Google Play e o uso de filtros para restringir a implantação de um aplicativo em certos dispositivos.

## <a name="requirements"></a>Requisitos

Para distribuir um aplicativo por meio do Google Play, deve ser criada uma conta de desenvolvedor. Isso só precisa ser feito uma vez e envolve uma taxa única de US$ 25.

Todos os aplicativos precisam ser assinados com uma chave de criptografia que expira após 22 de outubro de 2033.

O tamanho máximo para um APK publicado no Google Play é 100 MB. Se um aplicativo exceder esse tamanho, o Google Play permitirá que ativos extras sejam entregues por meio de *Arquivos de expansão de APK*. Os arquivos de expansão Android permitem que o APK tenha 2 arquivos adicionais de até 2 GB de tamanho cada. O Google Play hospedará e distribuirá esses arquivos sem custo adicional. Os arquivos de expansão serão discutidos em outra seção.

O Google Play não está disponível globalmente. Alguns locais podem não ter suporte para a distribuição de aplicativos.

## <a name="becoming-a-publisher"></a>Torne-se um Editor

Para publicar aplicativos no Google Play, é necessário ter uma conta de editor. Para inscrever-se em uma conta de editor siga estas etapas:

1. Visite o [Console de desenvolvedor do Google Play](https://play.google.com/apps/publish).
1. Insira as informações básicas sobre sua identidade de desenvolvedor.
1. Leia e aceite o Contrato de Distribuição do Desenvolvedor para a sua localidade.
1. Pague a taxa de registro de US$ 25.
1. Confirme a verificação por email.
1. Depois que a conta foi criada, é possível publicar aplicativos usando o Google Play.

O Google Play não oferece suporte a todos os países do mundo. As listas mais atualizadas de países podem ser encontradas nos seguintes links:

1. [Locais com suporte para Desenvolvedor &amp; Registro de Comerciante](https://support.google.com/googleplay/android-developer/bin/answer.py?hl=en&amp;answer=150324) &ndash; esta é uma lista de todos os países em que os desenvolvedores podem se registrar como comerciantes e vender aplicativos pagos.

1. [Locais com suporte para distribuição aos usuários do Google Play](https://support.google.com/googleplay/android-developer/bin/answer.py?hl=en&amp;answer=138294) &ndash; esta é uma lista de todos os países em que os aplicativos podem ser distribuídos.

### <a name="preparing-promotional-assets"></a>Preparação de ativos promocionais

Para promover e anunciar efetivamente um aplicativo no Google Play, o Google permite que os desenvolvedores enviem ativos promocionais como capturas de tela, gráficos e vídeos. O Google Play usará esses ativos para anunciar e promover o aplicativo.

#### <a name="launcher-icons"></a>Ícones de inicializador

Um *Ícone de inicializador* é um gráfico que representa um aplicativo. Cada ícone de inicializador deve ser um arquivo PNG de 32 bits com um canal alfa para transparência. Um aplicativo deve ter ícones para todas as densidades de tela generalizadas, conforme descrito na lista abaixo:

- **ldpi** (120 dpi) &ndash; 36 x 36 px
- **mdpi** (160 dpi) &ndash; 48 x 48 px
- **hdpi** (240 dpi) &ndash; 72 x 72 px
- **hxdpi** (320 dpi) &ndash; 96 x 96 px

Os ícones de inicializador são as primeiras coisas vistas por um usuário nos aplicativos no Google Play, portanto tome cuidado para que os ícones de inicializador sejam visualmente atraentes e significativos.

Dicas de ícones de inicializador:

1. **Os ícones do** &ndash; Launcher simples e desordenados devem ser mantidos simples e desordenados. Por isso, deve-se excluir o nome do aplicativo do ícone. Será mais fácil lembrar dos ícones mais simples e de os distinguir em tamanhos menores.

1. **Ícones não devem ser finos** &ndash; Ícones excessivamente finos não se destacarão bem em todos os fundos.

1. **Use os** &ndash; ícones do canal alfa devem fazer uso do canal alfa, e não devem ser imagens com quadro completo.

#### <a name="high-resolution-application-icons"></a>Ícones de aplicativos de alta resolução

Os aplicativos no Google Play requerem uma versão de alta fidelidade do ícone do aplicativo. Ela só é usada pelo Google Play e não substitui o ícone do lançador de aplicativo. As especificações para o ícone de alta resolução são:

1. PNG de 32 bits com um canal alfa
1. 512 x 512 pixels
1. Tamanho máximo de 1024 KB

O [Android Asset Studio](https://romannurik.github.io/AndroidAssetStudio/) é uma ferramenta útil para a criação de ícones de inicializador adequados e para a criação do ícone do aplicativo de alta resolução.

#### <a name="screenshots"></a>Capturas de tela

O Google play requer um mínimo de duas e um máximo de oito capturas de tela para um aplicativo. Eles serão exibidos na página de detalhes de um aplicativo no Google Play.

As especificações para capturas de tela são:

1. 24 bits, PNG ou JPG, sem canal alfa
1. 320w x 480h ou 480w x 800h ou 480w x 854h. Imagens em orientação paisagem serão cortadas.

#### <a name="promotional-graphic"></a>Elemento gráfico promocional

Esta é uma imagem opcional usada pelo Google Play:

1. É uma imagem de 180w x 120h, de 24 bits, PNG ou JPG, sem canal alfa.
1. Sem borda na arte.

#### <a name="feature-graphic"></a>Funcionalidade Gráfica

Usado pela seção em destaque do Google Play. Este elemento gráfico pode ser exibido sozinho sem um ícone do aplicativo.

1. 1024w x 500h, PNG ou JPG, sem canal alfa e sem transparência.
1. Todo o conteúdo importante deve estar em um quadro de 924x500. Os pixels fora desse quadro podem ser recortados para fins estilísticos.
1. Este elemento gráfico pode ser reduzido: usar texto grande e manter os elementos gráficos simples.

#### <a name="video-link"></a>Link de vídeo

Esta é uma URL para um vídeo do YouTube mostrando o aplicativo. O vídeo deve ter entre 30 segundos e 2 minutos de duração e apresentar as melhores partes do seu aplicativo.

### <a name="publishing-to-google-play"></a>Publicando no Google Play

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

O Xamarin Android 7.0 introduz um fluxo de trabalho integrado para a publicação de aplicativos no Google Play do Visual Studio. Se você estiver usando uma versão do Xamarin Android anterior à 7.0, será necessário carregar manualmente seu APK por meio do Console de desenvolvedor do Google Play. Além disso, você deve ter pelo menos um APK já carregado antes de usar o fluxo de trabalho integrado. Se ainda não carregou seu primeiro APK, você deverá carregá-lo manualmente. Para obter mais informações, consulte [Carregando manualmente o APK](~/android/deploy-test/publishing/publishing-to-google-play/manually-uploading-the-apk.md).

[Criar um Novo Certificado](~/android/deploy-test/signing/index.md#newcert) explicou como criar um novo certificado para assinar aplicativos Android. A próxima etapa é publicar um aplicativo assinado no Google Play:

1. Entre na sua conta de Desenvolvedor do Google Play para criar um novo projeto vinculado à sua conta de Desenvolvedor do Google Play.
2. Crie um **Cliente OAuth** que autentica o seu aplicativo.
3. Insira a ID do Cliente e o Segredo do cliente resultantes no Visual Studio.
4. Registre sua conta no Visual Studio.
5. Assine o aplicativo com o seu certificado.
6. Publique o aplicativo assinado no Google Play.

Em [Arquivar para publicação](~/android/deploy-test/release-prep/index.md#archive), a caixa de diálogo **Canal de Distribuição** apresentou duas opções de distribuição: **Ad Hoc** e **Google Play**. Se, em vez disso, for exibida a caixa de diálogo **Identidade de assinatura**, clique em **Voltar** para retornar para a caixa de diálogo **Canal de distribuição**. Selecione **o Google Play**:

[![Diálogo do Canal de Distribuição](images/vs/01-distribution-channel-sml.png)](images/vs/01-distribution-channel.png#lightbox)

Na caixa de diálogo **Identidade de assinatura**, selecione a identidade criada em [Criar um Novo Certificado](~/android/deploy-test/signing/index.md#newcert) e clique em **Continuar**:

[![Assinar diálogo identidade](images/vs/02-select-identity-sml.png)](images/vs/02-select-identity.png#lightbox)

Na caixa de diálogo **Contas do Google Play**, clique no botão **+** para adicionar uma nova conta do Google Play:

[![Diálogo contas do Google Play](images/vs/03-google-play-accounts-sml.png)](images/vs/03-google-play-accounts.png#lightbox)

Na caixa de diálogo **Registrar acesso à API do Google**, você deve fornecer a _ID do cliente_ e o _Segredo do cliente_ que fornecem à API o acesso à sua conta de Desenvolvedor do Google Play:

[![Registre a caixa de diálogo Google API Access](images/vs/04-register-google-api-access-sml.png)](images/vs/04-register-google-api-access.png#lightbox)

A próxima seção explica como criar um novo projeto de API do Google e gerar a _ID do cliente_ e o _Segredo do cliente_ necessários.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

O Visual Studio para Mac tem um fluxo de trabalho integrado para a publicação de aplicativos no Google Play.

[Criar um Novo Certificado](~/android/deploy-test/signing/index.md#newcert) abordou a criação de um novo certificado para assinar aplicativos Android. As etapas a seguir descrevem como publicar um aplicativo Xamarin.Android no Google Play:

1. Entre na sua conta de Desenvolvedor do Google Play para criar um novo projeto vinculado à sua conta de Desenvolvedor do Google Play.
2. Crie um _Cliente OAuth_ que autentica o seu aplicativo.
3. Insira a _ID do Cliente_ e o _Segredo do cliente_ resultantes no Visual Studio para Mac.
4. Registre sua conta no Visual Studio para Mac.
5. Assine o aplicativo com o seu certificado.
6. Publique o aplicativo assinado no Google Play.

Em [Arquivar para publicação](~/android/deploy-test/release-prep/index.md#archive), a caixa de diálogo **Assinar e Distribuir...** apresentou duas opções de distribuição. Selecione **Google Play** e clique em **Avançar**:

[![Selecione a caixa de diálogo distribuição do Android](images/xs/01-select-google-play-sml.png)](images/xs/01-select-google-play.png#lightbox)

Na caixa de diálogo **Conta de API do Google Play**, você deve fornecer a _ID do cliente_ e o _Segredo do cliente_, que fornece à API acesso à sua conta de desenvolvedor do Google Play:

[![Diálogo da conta da API do Google Play](images/xs/02-google-play-api-account-sml.png)](images/xs/02-google-play-api-account.png#lightbox)

A próxima seção explica como criar um novo projeto de API do Google e gerar a _ID do cliente_ e o _Segredo do cliente_ necessários.

-----

#### <a name="create-a-google-api-project"></a>Criar um projeto de API do Google

Primeiro, entre na sua [Conta de Desenvolvedor do Google Play](https://play.google.com/apps/publish).
Se você ainda não tiver uma conta de desenvolvedor do Google Play, consulte [Introdução à publicação](https://developer.android.com/distribute/googleplay/start.html).
Além disso, a [Introdução](https://developers.google.com/android-publisher/getting_started) à API de Desenvolvedor do Google Play explica como usar a API de Desenvolvedor do Google Play. Depois de entrar no Console do Google Play Developer, clique em **CRIAR APLICATIVO**:

[![Criar novo botão de projeto](images/01-create-new-project-sml.png)](images/01-create-new-project.png#lightbox)

Depois de criar o novo projeto, ele será vinculado à sua conta do Google Play Developer Console.

A próxima etapa é criar um Cliente OAuth para o aplicativo (se ainda não foi criado). Quando os usuários solicitarem acesso aos dados particulares deles usando o seu aplicativo, sua ID do Cliente OAuth será usada para autenticar o aplicativo.

Vá para a página **Configurações**.

[![Ícone Configurações](images/02-google-play-developer-console-sml.png)](images/02-google-play-developer-console.png#lightbox)

Na página **Configurações,** selecione **acesso à API** e clique **em CRIAR CLIENTE OAUTH** para criar um novo cliente OAuth:

[![Criar o botão Cliente OAuth](images/03-create-oauth-client-sml.png)](images/03-create-oauth-client.png#lightbox)

Depois de alguns segundos, uma nova ID de Cliente é gerada. Clique em **Exibir no Console de Desenvolvedor do Google** para ver sua nova ID de Cliente no Console de Desenvolvedor do Google:

[![ID do cliente exibido](images/04-generated-client-id-sml.png)](images/04-generated-client-id.png#lightbox)

A ID do Cliente será exibida junto com o nome e a data de criação dela. Clique no ícone **Editar Cliente OAuth** para exibir o Segredo do cliente para o seu aplicativo:

[![Ver credenciais do aplicativo](images/05-google-developer-console-sml.png)](images/05-google-developer-console.png#lightbox)

O nome padrão do cliente OAuth é *Desenvolvedor Android do Google Play*. Esse nome pode ser alterado para o nome do aplicativo Xamarin.Android ou outro nome adequado. Neste exemplo, o nome do cliente OAuth será alterado para o nome do aplicativo, **MyApp**:

[![ID do cliente e segredo exibido](images/06-client-id-and-secret-sml.png)](images/06-client-id-and-secret.png#lightbox)

Clique em **Salvar** para salvar as alterações. Isso retorna à página **Credenciais** onde baixar as credenciais clicando no ícone **Download JSON:**

[![Baixar o ícone JSON](images/07-download-json-sml.png)](images/07-download-json.png#lightbox)

Esse arquivo JSON contém a ID do Cliente e o Segredo do cliente que você pode recortar e colar na caixa de diálogo **Assinar e Distribuir** na próxima etapa.

#### <a name="register-google-api-access"></a>Registrar acesso à API do Google

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Use a ID do Cliente e o Segredo do cliente para preencher a caixa de diálogo **Conta da API do Google Play** no Visual Studio para Mac. É possível fornecer uma descrição para a conta &ndash; isso torna possível o registro de mais de uma conta do Google Play e o upload de futuros APKs em diferentes contas do Google Play. Copie a ID do Cliente e o Segredo do cliente nessa caixa de diálogo e clique em **Registrar**:

[![Registre a caixa de diálogo Google API Access](images/vs/05-enter-client-id-and-secret-sml.png)](images/vs/05-enter-client-id-and-secret.png#lightbox)

Um navegador da Web será aberto e solicitará que você entre na sua conta de Desenvolvedor Android do Google Play (se você ainda não tiver entrado). Depois de entrar, o seguinte aviso é exibido no navegador da Web.
Clique em **Permitir** para autorizar o aplicativo:

[![Autorizar a caixa de diálogo do aplicativo](images/vs/06-authorize-app-sml.png)](images/vs/06-authorize-app.png#lightbox)

#### <a name="publish"></a>Publicar

Depois de clicar em **Permitir,** os relatórios do navegador _receberam código de verificação. Fechando..._ e o aplicativo é adicionado à lista de Contas do Google Play no Visual Studio. Na caixa de diálogo **Contas do Google Play**, clique em **Continuar**:

[![Conta adicionada às Contas do Google Play](images/vs/07-account-added-sml.png)](images/vs/07-account-added.png#lightbox)

Em seguida, a caixa de diálogo **Controle do Google Play** será apresentada. O Google Play oferece cinco faixas possíveis para o upload do seu aplicativo:

- **Interno** &ndash; Usado para distribuir rapidamente seu aplicativo para testes internos e verificações de garantia de qualidade.
- **Alpha** &ndash; Usado para carregar uma versão inicial do seu aplicativo para uma pequena lista de testadores.
- **Beta** &ndash; Usado para carregar uma versão inicial do seu aplicativo para uma lista maior de testadores.
- **Produção** &ndash; Usada para distribuição completa na loja Google Play.
- **Personalizado** &ndash; Usado para testar versões de pré-lançamento do seu aplicativo com usuários específicos, criando uma lista de testadores por endereço de e-mail.

Escolha qual controle do Google Play será usado para fazer upload do aplicativo e clique em **Carregar**.

[![Conta adicionada às Contas do Google Play](images/vs/08-google-play-track-sml.png)](images/vs/07-account-added.png#lightbox)

Para obter mais informações sobre os testes do Google Play, consulte [Configurar testes abertos/fechados/internos](https://support.google.com/googleplay/android-developer/answer/3131213?hl=en).

Em seguida, uma caixa de diálogo será apresentada para inserir a senha do certificado de autenticação.
Digite a senha e clique em **OK**:

[![Assinatura de diálogo de senha](images/vs/09-certificate-password-sml.png)](images/vs/09-certificate-password.png#lightbox)

O **Archive Manager** exibe o progresso do upload:

[![Carregando o progresso do APK](images/vs/10-uploading-apk-sml.png)](images/vs/10-uploading-apk.png#lightbox)

Quando o upload for concluído, o status de conclusão aparece no canto inferior esquerdo do Visual Studio:

[![Mensagem completa do projeto de publicação](images/vs/11-published-sml.png)](images/vs/11-published.png#lightbox)

### <a name="troubleshooting"></a>Solução de problemas

Se você não ver sua faixa personalizada ao selecionar uma faixa do Google Play, certifique-se de ter criado uma versão para essa faixa no Console do Google Play Developer. Para obter instruções sobre como criar uma versão, consulte [Prepare-& lançar lançamentos](https://support.google.com/googleplay/android-developer/answer/7159011?hl=en).

Observe que um APK já deve ter sido enviado para o repositório do Google Play antes do **Publicar no Google Play** funcionar. Se um APK ainda não tiver sido carregado, o Assistente de Publicação exibirá o seguinte erro no painel de **Erros**:

[![Você deve carregar manualmente seu primeiro APK para este aplicativo](images/vs/12-upload-error-sml.png)](images/vs/12-upload-error.png#lightbox)

Quando esse erro ocorrer, carregue manualmente um APK (como uma compilação Ad Hoc) através do Google Play Developer Console e use a caixa de diálogo Do Canal de **Distribuição** para atualizações subsequentes do APK.  Para obter mais informações, consulte [Carregando manualmente o APK](~/android/deploy-test/publishing/publishing-to-google-play/manually-uploading-the-apk.md). O código de versão do APK deve alterar com cada upload, caso contrário, ocorrerá o seguinte erro:

[![APK com código de versão (1) já foi atualizado](images/vs/13-version-code-error-sml.png)](images/vs/13-version-code-error.png#lightbox)

Para resolver esse erro, recompile o aplicativo com um número de versão diferente e reenvie-o para o Google Play por meio da caixa de diálogo **Canal de Distribuição**.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Use a ID do Cliente e o Segredo do cliente para preencher a caixa de diálogo **Conta da API do Google Play** no Visual Studio para Mac. É possível fornecer uma descrição para a conta &ndash; isso torna possível o registro de mais de uma conta do Google Play e o upload de futuros APKs em diferentes contas do Google Play. Copie a ID do Cliente e o Segredo do cliente nessa caixa de diálogo e clique em **Registrar**:

[![Autorizar o diálogo de acesso](images/xs/03-register-sml.png)](images/xs/03-register.png#lightbox)

Se a ID do Cliente e o Segredo do cliente forem aceitos, uma mensagem de **Registro bem-sucedido** será exibida. Clique **em Next**:

[![Registro Mensagem de sucesso](images/xs/04-registration-successful-sml.png)](images/xs/04-registration-successful.png#lightbox)

Na caixa de diálogo **Conta do Google Play**, selecione uma conta do Google e um controle para carregar o aplicativo:

[![Escolha a caixa de diálogo da conta do Google](images/xs/05-choose-google-account-sml.png)](images/xs/05-choose-google-account.png#lightbox)

O Google Play oferece cinco faixas possíveis para o upload do seu aplicativo:

- **Interno** &ndash; Usado para distribuir rapidamente seu aplicativo para testes internos e verificações de garantia de qualidade.
- **Alpha** &ndash; Usado para carregar uma versão inicial do seu aplicativo para uma pequena lista de testadores.
- **Beta** &ndash; Usado para carregar uma versão inicial do seu aplicativo para uma lista maior de testadores.
- **Produção** &ndash; Usada para distribuição completa na loja Google Play.
- **Personalizado** &ndash; Usado para testar versões de pré-lançamento do seu aplicativo com usuários específicos, criando uma lista de testadores por endereço de e-mail.

Para obter mais informações sobre os testes do Google Play, consulte [Configurar testes alfa/beta](https://support.google.com/googleplay/android-developer/answer/3131213?hl=en).

Em seguida, escolha uma identidade de assinatura que será usada para assinar o aplicativo.
Selecione **Usar Chave Existente** para usar uma identidade de assinatura existente, caso contrário, consulte o guia [Criar um Novo Certificado](~/android/deploy-test/signing/index.md#newcert) para saber como criar uma nova chave. Depois de selecionar um certificado para assinar o aplicativo, clique em **Avançar**:

[![Diálogo de identidade de assinatura do Android](images/xs/06-android-signing-identity-sml.png)](images/xs/06-android-signing-identity.png#lightbox)

Agora o aplicativo pode ser carregado no Google Play. A caixa de diálogo **Publicar no Google Play** resume as informações sobre o seu aplicativo &ndash; clique em **Publicar** para publicar seu aplicativo no Google Play:

[![Publique no diálogo Google Play](images/xs/07-publish-to-google-play-sml.png)](images/xs/07-publish-to-google-play.png#lightbox)

### <a name="troubleshooting"></a>Solução de problemas

Se você não ver sua faixa personalizada ao selecionar uma faixa do Google Play para carregar seu aplicativo, certifique-se de ter criado uma versão para essa faixa no Google Play Developer Console. Para obter instruções sobre como criar uma versão, consulte [Prepare-& lançar lançamentos](https://support.google.com/googleplay/android-developer/answer/7159011?hl=en).

Observe que um APK já deve ter sido enviado para o repositório do Google Play antes do **Publicar no Google Play** funcionar. Se um APK não estiver carregado, poderá ocorrer o seguinte erro:

> _O Google Play exige que você carregue manualmente seu primeiro APK para este aplicativo. Você pode usar um APK ad-hoc para isso._

ou

> _Nenhum aplicativo foi encontrado para o nome do pacote especificado. [404]_

Para resolver esse erro, carregue manualmente um APK (como uma compilação Ad Hoc) através do Google Play Developer Console e use a caixa de diálogo **Publicar para o Google Play** para atualizações subsequentes do APK. Para obter informações sobre como carregar um APK manualmente, consulte [Carregar manualmente o APK](~/android/deploy-test/publishing/publishing-to-google-play/manually-uploading-the-apk.md).

-----
