---
title: Assinatura do Pacote de Aplicativos Android
description: Como assinar o APK (pacote de aplicativo Android) para publicação
ms.prod: xamarin
ms.assetid: 8E3EFBB2-F8AD-C126-5F32-7FD140791E53
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/02/2018
ms.openlocfilehash: 66d2f66f0118e84778b25fc0e872099439510ce2
ms.sourcegitcommit: 898ba8e5140ae32a7df7e07c056aff65f6fe4260
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86226840"
---
# <a name="signing-the-android-application-package"></a>Assinatura do Pacote de Aplicativos Android

Em [Preparar um Aplicativo para Lançamento](~/android/deploy-test/release-prep/index.md), o **Gerenciador de Arquivo Morto** foi usado para build do aplicativo e colocá-lo em um arquivo morto para assinatura e publicação. Esta seção explica como criar uma identidade de assinatura do Android, a criar um novo certificado de assinatura para aplicativos Android e a publicar o *ad-hoc* de aplicativo arquivado no disco. O APK resultante pode ter o sideload realizado em dispositivos Android sem passar por uma loja de aplicativos.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Em [arquivo para publicação](~/android/deploy-test/release-prep/index.md#archive), a caixa de diálogo **canal de distribuição** apresenta duas opções para distribuição. Selecione **Ad Hoc**:

[![Caixa de diálogo canal de distribuição](images/vs/01-distribution-channel-sml.png)](images/vs/01-distribution-channel.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Em [Arquivar para Publicação](~/android/deploy-test/release-prep/index.md#archive), a caixa de diálogo **Assinar e Distribuir...** nos apresentou duas opções de distribuição. Selecione **Ad-Hoc** e clique em **Próximo**:

[![Caixa de diálogo assinar e distribuir](images/xs/01-select-ad-hoc-sml.png)](images/xs/01-select-ad-hoc.png#lightbox)

-----

<a name="newcertvs"></a>
<a name="newcert"></a>
<a name="newcertxs"></a>

## <a name="create-a-new-certificate"></a>Criar um Novo Certificado

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Depois que o **Ad-Hoc** for selecionado, o Visual Studio abrirá a página **Identidade de Assinatura** da caixa de diálogo conforme mostrado na próxima captura de tela. Para publicar o .APK, ele deve primeiro ser assinado com uma chave de assinatura (também conhecida como um certificado).

Um certificado existente pode ser usado ao clicar no botão **Importar** e, em seguida, continuando para [Assinar o APK](#sign-the-apk). Caso contrário, clique no **+** botão para criar um novo certificado:

[![Identidade de assinatura ad hoc](images/vs/02-ad-hoc-signing-identity-vs-sml.png)](images/vs/02-ad-hoc-signing-identity-vs.png#lightbox)

A caixa de diálogo **criar repositório de chaves do Android** é exibida; Use esta caixa de diálogo para criar um novo certificado de assinatura que pode ser usado para assinar aplicativos Android. Insira as informações necessárias (destacadas em vermelho), conforme mostrado nesta caixa de diálogo:

[![Caixa de diálogo Criar repositório de chaves do Android](images/vs/03-create-android-key-store-vs-sml.png)](images/vs/03-create-android-key-store-vs.png#lightbox)

O exemplo a seguir ilustra o tipo de informação que deve ser fornecido. Clique em **Criar** para criar o novo certificado:

[![Criando um novo certificado](images/vs/04-key-store-example-vs-sml.png)](images/vs/04-key-store-example-vs.png#lightbox)

O repositório de chaves resultante reside no seguinte local:

**C:\\Usuários\\*NOMEDEUSUÁRIO*\\AppData\\Local\\Xamarin\\Mono for Android\\Keystore\\*ALIAS*\\*ALIAS*.keystore**

Por exemplo, ao usar **chimp** como alias, as etapas acima podem criar uma nova chave de assinatura no seguinte local:

**C:\\Usuários\\*NOMEDEUSUÁRIO*\\AppData\\Local\\Xamarin\\Mono for Android\\Keystore\\chimp\\chimp.keystore**

> [!IMPORTANT]
> A pasta AppData fica oculta por padrão e talvez seja necessário reocultá-la para acessá-la.
>
> Além disso, não se esqueça de fazer backup do arquivo e da senha do repositório de chaves resultante em um local seguro &ndash; que não esteja incluído na solução. Se perder seu arquivo de repositório de chaves (por exemplo, porque passou a usar outro computador ou reinstalou o Windows), você não conseguirá assinar seu aplicativo com o mesmo certificado das versões anteriores.

Para obter mais informações sobre o repositório de chaves, consulte [Localizando sua Assinatura MD5 ou SHA1 do Repositório de Chaves](~/android/deploy-test/signing/keystore-signature.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Depois de clicar em **Ad-Hoc**, o Visual Studio para Mac abre a caixa de diálogo **Identidade de Assinatura do Android** conforme mostrado na próxima captura de tela. Para publicar o .APK, ele deve primeiro ser assinado com uma chave de assinatura (também conhecida como um certificado). Se um certificado já existir, clique no botão **Importar uma Chave Existente** para importá-lo e, em seguida, vá até [Assinar o APK](#sign-the-apk). Caso contrário, clique no botão **Criar uma Nova Chave** para criar um novo certificado:

[![Diálogo de identidade de assinatura do Android](images/xs/02-android-signing-identity-sml.png)](images/xs/02-android-signing-identity.png#lightbox)

A caixa de diálogo **Criar um Novo Certificado** é usada para criar um novo certificado de assinatura que pode ser usado para assinar aplicativos Android. Clique em **OK** depois de inserir as informações necessárias:

[![Caixa de diálogo Criar novo certificado](images/xs/03-create-new-certificate-sml.png)](images/xs/03-create-new-certificate.png#lightbox)

O repositório de chaves resultante reside no seguinte local:

**~/Library/Developer/Xamarin/Keystore/alias/alias.keystore**

Por exemplo, as etapas acima podem criar uma nova chave de assinatura no seguinte local:

**~/Library/Developer/Xamarin/Keystore/chimp/chimp.keystore**

> [!NOTE]
> Não deixe de fazer backup do arquivo de repositório de chaves resultante e da senha em um local seguro &ndash; isso não está incluído na solução. Se perder seu arquivo de repositório de chaves (por exemplo, porque passou a usar outro computador ou reinstalou o macOS), você não conseguirá assinar seu aplicativo com o mesmo certificado das versões anteriores.

Para obter mais informações sobre o repositório de chaves, consulte [Localizando sua Assinatura MD5 ou SHA1 do Repositório de Chaves](~/android/deploy-test/signing/keystore-signature.md).

-----

## <a name="sign-the-apk"></a>Assinar o APK

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Ao clicar em **Criar**, um novo repositório de chaves (contendo um novo certificado) será salvo e listado em **Identidade de Assinatura** conforme mostrado na seguinte captura de tela. Para publicar um aplicativo no Google Play, clique em **Cancelar** e vá até [Publicar no Google Play](~/android/deploy-test/publishing/publishing-to-google-play/index.md).
Para publicar *ad hoc*, selecione a identidade de assinatura usada para assinar e clique em **Salvar Como** para publicar o aplicativo para distribuição independente. Por exemplo, a identidade de assinatura **chimp** (criada anteriormente) é selecionada nesta captura de tela:

[![Exemplo de identidade de assinatura](images/vs/05-save-as-vs-sml.png)](images/vs/05-save-as-vs.png#lightbox)

Em seguida, o **Gerenciador de Arquivo Morto** exibe o andamento da publicação. Quando o processo de publicação for concluído, a caixa de diálogo **Salvar Como** será aberta para solicitar um local no qual o arquivo .APK gerado será armazenado:

[![Caixa de diálogo Salvar como](images/vs/06-save-as-dialog-vs-sml.png)](images/vs/06-save-as-dialog-vs.png#lightbox)

Navegue até o local desejado e clique em **Salvar**. Se a senha da chave for desconhecida, a caixa de diálogo **Senha da Assinatura** será exibida para solicitar a senha do certificado selecionado:

[![Caixa de diálogo assinar senha](images/vs/07-signing-password-vs-sml.png)](images/vs/07-signing-password-vs.png#lightbox)

Após concluir o processo de assinatura, clique em **Abrir Distribuição**:

[![Botão abrir distribuição](images/vs/08-open-distribution-sml.png)](images/vs/08-open-distribution.png#lightbox)

Isso faz com que o Windows Explorer abra a pasta que contém o arquivo APK gerado. Neste ponto, o Visual Studio compilou o aplicativo Xamarin.Android em um APK que está pronto para distribuição.
A seguinte captura de tela mostra um exemplo de aplicativo pronto para publicar, **MyApp.MyApp.apk**:

[![APK mostrado no Windows Explorer](images/vs/09-generated-app-vs-sml.png)](images/vs/09-generated-app-vs.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Conforme visto aqui, um novo certificado foi adicionado ao repositório de chaves. Para publicar um aplicativo no Google Play, clique em **Cancelar** e vá até [Publicar no Google Play](~/android/deploy-test/publishing/publishing-to-google-play/index.md).
Como alternativa, clique em **Próximo** para publicar o aplicativo *ad hoc* (para distribuição independente) conforme mostrado neste exemplo:

[![Caixa de diálogo assinar e distribuir](images/xs/04-select-identity-sml.png)](images/xs/04-select-identity.png#lightbox)

A caixa de diálogo **Publicar como Ad Hoc** fornece um resumo do aplicativo assinado antes de ser publicado. Se essas informações estiverem corretas, clique em **Publicar**.

[![Caixa de diálogo Publicar como ad hoc](images/xs/05-publish-ad-hoc-sml.png)](images/xs/05-publish-ad-hoc.png#lightbox)

A caixa de diálogo **Arquivo de saída do APK** salvará o APK para o caminho especificado. Clique em **Salvar**.

![Caixa de diálogo Arquivo APK de Saída](images/xs/06-output-apk-file.png)

Em seguida, digite a senha do certificado (a senha que foi usada na caixa de diálogo **Criar um Novo Certificado**) e clique em **OK**:

![Inserir senha de certificado](images/xs/07-signing-certificate.png)

O APK está assinado com o certificado e salvo no local especificado. Clique em **Revelar no Finder**:

[![Diálogo publicação bem-sucedida](images/xs/08-app-is-ready-sml.png)](images/xs/08-app-is-ready.png#lightbox)

Isso abre o Finder no local do arquivo APK assinado:

[![APK mostrado no localizador](images/xs/09-show-in-finder-sml.png)](images/xs/09-show-in-finder.png#lightbox)

O APK está pronto para copiar do Finder e enviar para seu destino final. É recomendável instalar o APK em um dispositivo Android e testá-lo antes da distribuição. Consulte [Publicação Independente](~/android/deploy-test/publishing/publishing-independently.md) para saber sobre como publicar um APK *ad-hoc*.

-----

## <a name="next-steps"></a>Próximas etapas

Depois que o pacote de aplicativo for assinado para versão, ele deve ser publicado. As seguintes seções descrevem várias maneiras de publicar um aplicativo.
