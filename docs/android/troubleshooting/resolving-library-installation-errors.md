---
title: Resolver Erros de Instalação da Biblioteca
description: Em alguns casos, você pode receber erros durante a instalação das bibliotecas de suporte do Android. Este guia fornece soluções alternativas para alguns erros comuns.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 2AE68ACE-8496-445D-BF17-5E4097D4AE35
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/14/2018
ms.openlocfilehash: 8107a26e090aa920d71146d5f2af8b8365697d6b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757205"
---
# <a name="resolving-library-installation-errors"></a>Resolver Erros de Instalação da Biblioteca

_Em alguns casos, você pode receber erros durante a instalação das bibliotecas de suporte do Android. Este guia fornece soluções alternativas para alguns erros comuns._

## <a name="overview"></a>Visão geral

Ao criar um projeto de aplicativo Xamarin. Android, você poderá obter erros de compilação quando o Visual Studio ou Visual Studio para Mac tentar baixar e instalar bibliotecas de dependências. Muitos desses erros são causados por problemas de conectividade de rede, corrupção de arquivo ou problemas de controle de versão. Este guia descreve os erros de instalação da biblioteca de suporte mais comuns e fornece as etapas para solucionar esses problemas e fazer com que seu projeto de aplicativo seja criado novamente. 

## <a name="errors-while-downloading-m2repository"></a>Erros ao baixar o m2Repository

Você pode ver erros de **m2repository** ao fazer referência a um pacote NuGet das bibliotecas de suporte do Android ou dos serviços de Google Play. Essa mensagem de erro é semelhante à exibida a seguir:

```shell
Download failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\22.2.1\content directory.
```

Este exemplo é para **o\_Android\_m2repository R16**, mas você pode ver essa mesma mensagem de erro para uma versão diferente, como **Android\_\_m2repository R18** ou **Android\_ m2repository\_R25**. 

### <a name="automatic-recovery-from-m2repository-errors"></a>Recuperação automática de erros do m2repository 

Geralmente, esse problema pode ser remediado excluindo a biblioteca problemática e recompilando de acordo com estas etapas: 

1. Navegue até o diretório de biblioteca de suporte no seu computador:

    - No Windows, as bibliotecas de suporte estão localizadas em **\\C:\\Users\\_nome_do_usuário_\\AppData\\local Xamarin**. 

    - Em Mac OS X, as bibliotecas de suporte estão localizadas em **/Users/_username_/.local/share/Xamarin**. 

2. Localize a biblioteca e a pasta de versão correspondentes à mensagem de erro. Por exemplo, a biblioteca e a pasta de versão para a mensagem de erro acima estão localizadas em **Android\\. support. v4 22.2.1**:

    [![Local da pasta de exemplo para a biblioteca de suporte do 22.2.1](resolving-library-installation-errors-images/01-example-location.png)](resolving-library-installation-errors-images/01-example-location.png#lightbox)

3. Exclua o conteúdo da pasta versão. Certifique-se de remover o arquivo **. zip** , bem como o **conteúdo** e os subdiretórios **inseridos** nessa pasta. Para a mensagem de erro de exemplo mostrada acima, os arquivos e subdiretórios mostrados nesta captura de tela (**conteúdo**, **inserido**e **android_m2repository_r16. zip**) devem ser excluídos:

    [![Conteúdo de exemplo da pasta de biblioteca de suporte do 22.2.1](resolving-library-installation-errors-images/02-example-folder-vs.png)](resolving-library-installation-errors-images/02-example-folder-vs.png#lightbox)

   Observe que é importante excluir *todo* o conteúdo dessa pasta. Embora essa pasta inicialmente possa conter o arquivo " **m2repository\_R16\_. zip** " ausente do Android, esse arquivo pode ter sido parcialmente baixado ou corrompido.

4. Recompilar o &ndash; projeto fazendo isso fará com que o processo de compilação Baixe novamente a biblioteca ausente.

Na maioria dos casos, essas etapas resolverão o erro de compilação e permitirão que você continue. Se a exclusão dessa biblioteca não resolver o erro de compilação, você deverá baixar e instalar manualmente **o\_arquivo\_m2repository r_nn_. zip do Android** , conforme descrito na próxima seção. 

### <a name="manually-downloading-m2repository"></a>Baixando o m2repository manualmente

Se você tentou usar as etapas de recuperação automática acima e ainda tiver erros de compilação, poderá baixar manualmente o **arquivo\_m2repository\_r_nn_. zip do Android** (usando um navegador da Web) e instalá-lo de acordo com as etapas a seguir . Esse procedimento também será útil se você não tiver acesso à Internet no computador de desenvolvimento, mas for capaz de baixar o arquivo morto usando um computador diferente. 

1. Baixe o **arquivo\_.\_zip do Android m2repository r_nn_** que corresponde aos links da &ndash; mensagem de erro são fornecidos na lista a seguir (juntamente com o hash MD5 correspondente da URL de cada link):

    - [android\_m2repository\_r33.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r33.zip) &ndash; 5FB756A25962361D17BBE99C3B3FCC44

    - [android\_m2repository\_r32.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip) &ndash; F16A3455987DBAE5783F058F19F7FCDF

    - [android\_m2repository\_r31.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r31.zip) &ndash; 99A8907CE2324316E754A95E4C2D786E

    - [android\_m2repository\_r30.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r30.zip) &ndash; 05AD180B8BDC7C21D6BCB94DDE7F2C8F

    - [android\_m2repository\_r29.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r29.zip) &ndash; 2A3A8A6D6826EF6CC653030E7D695C41

    - [android\_m2repository\_r28.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r28.zip) &ndash; 17BE247580748F1EDB72E9F374AA0223

    - [android\_m2repository\_r27.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r27.zip) &ndash; C9FD4FCD69D7D12B1D9DF076B7BE4E1C

    - [android\_m2repository\_r26.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r26.zip) &ndash; 8157FC1C311BB36420C1D8992AF54A4D

    - [android\_m2repository\_r25.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip) &ndash; 0B3F1796C97C707339FB13AE8507AF50

    - [android\_m2repository\_r24.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r24.zip) &ndash; 8E3C9EC713781EDFE1EFBC5974136BEA

    - [android\_m2repository\_r23.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r23.zip) &ndash; D5BB66B3640FD9B9C6362C9DB5AB0FE7

    - [android\_m2repository\_r22.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r22.zip) &ndash; 96659D653BDE0FAEDB818170891F2BB0

    - [android\_m2repository\_r21.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r21.zip) &ndash; CD3223F2EFE068A26682B9E9C4B6FBB5

    - [android\_m2repository\_r20.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r20.zip) &ndash; 650E58DF02DB1A832386FA4A2DE46B1A

    - [android\_m2repository\_r19.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r19.zip) &ndash; 263B062D6EFAA8AEE39E9460B8A5851A

    - [android\_m2repository\_r18.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r18.zip) &ndash; 25947AD38DCB4865ABEB61522FAFDA0E

    - [android\_m2repository\_r17.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r17.zip) &ndash; 49054774F44AE5F35A6BA9D3C117EFD8

    - [android\_m2repository\_r16.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip) &ndash; 0595E577D19D31708195A83087881EE6

    Se o arquivo **m2repository** não for mostrado nesta tabela, você poderá criar a URL de download, de acordo **https://dl-ssl.google.com/android/repository/** com o nome do **m2repository** a ser baixado. Por exemplo, use **https://dl-ssl.google.com/android/repository/android\_m2repository\_r10.zip** para baixar **o\_ Android\_ m2repository R10. zip**.

2. Renomeie o arquivo para o hash MD5 correspondente da URL de download, conforme mostrado na tabela acima. Por exemplo, se você baixou o **Android\_m2repository\_R25. zip**, renomeie-o para **0B3F1796C97C707339FB13AE8507AF50. zip**. Se o hash MD5 para a URL de download do arquivo baixado não for mostrado na tabela, você poderá usar um [gerador de MD5 online](http://www.webconfs.com/online-md5-generator.php) para converter a URL em uma cadeia de caracteres de hash MD5. 

3. Copie o arquivo para a pasta Xamarin **zips** : 

    - No Windows, essa pasta está localizada em **C:\\Users\\***nome_do_usuário***\\AppData\\\\local\\Xamarin zips**. 

    - Em Mac OS X, essa pasta está localizada em **/Users/***username***/.local/share/Xamarin/zips**. 

    Por exemplo, a captura de tela a seguir ilustra o resultado quando o **Android\_m2repository\_R16. zip** é baixado e renomeado para o hash MD5 de sua URL de download no Windows:

    [![Exemplo do repositório R16. zip que está sendo renomeado para 0595E577D19D31708195A83087881EE6. zip](resolving-library-installation-errors-images/03-md5-rename-vs.png)](resolving-library-installation-errors-images/03-md5-rename-vs.png#lightbox)

Se esse procedimento não resolver o erro de compilação, você deverá baixar manualmente o **arquivo\_Android\_m2repository r_nn_. zip** , descompactá-lo e instalar seu conteúdo, conforme descrito na próxima seção. 

### <a name="manually-downloading-and-installing-m2repository-files"></a>Baixando e Instalando manualmente arquivos m2repository

O processo totalmente manual para a recuperação de erros **m2repository** envolve o download do arquivo **Android\_m2repository\_r_nn_. zip** (usando um navegador da Web), descompactá-lo e copiar seu conteúdo para o suporte diretório de biblioteca no seu computador. No exemplo a seguir, vamos recuperar essa mensagem de erro: 

```shell
Unzipping failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\23.1.1\content directory.
```

Use as etapas a seguir para baixar o **m2repository** e instalar seu conteúdo:

1. Exclua o conteúdo da pasta de biblioteca correspondente à mensagem de erro. Por exemplo, na mensagem de erro acima, você excluiria o conteúdo de **C\\:\\Users\\***nome_do_usuário***\\AppData\\local\\Xamarin Android. support. v423.1.1.0\\** . 
    Conforme descrito anteriormente, você deve excluir todo o conteúdo deste diretório:

    [![Excluindo pastas de conteúdo, inseridas e android_m2repository da pasta 23.1.1.0](resolving-library-installation-errors-images/04-delete-contents-vs.png)](resolving-library-installation-errors-images/04-delete-contents-vs.png#lightbox)

2. Baixe o **arquivo\_.\_zip do Android m2repository r_nn_** do Google que corresponde à mensagem de erro (consulte a tabela na seção anterior para obter links).

3. Extraia esse arquivo **. zip** em qualquer local (como a área de trabalho). Isso deve criar um diretório que corresponda ao nome do arquivo **. zip** . Nesse diretório, você deve encontrar um subdiretório chamado **m2repository**: 

    [![pasta m2repository encontrada no arquivo zip extraído](resolving-library-installation-errors-images/05-m2repository-vs.png)](resolving-library-installation-errors-images/05-m2repository-vs.png#lightbox)

4. No diretório de biblioteca com versão que você limpou na etapa 1, crie novamente o **conteúdo** e os subdiretórios **inseridos** . Por exemplo, a captura de tela a seguir ilustra o **conteúdo** e os subdiretórios **inseridos** que estão sendo criados na pasta **23.1.1.0** para **Android\_m2repository\_R25. zip**: 

    [![Criar conteúdo e pastas inseridas na pasta 23.1.1.0](resolving-library-installation-errors-images/06-recreate-folders-vs.png)](resolving-library-installation-errors-images/06-recreate-folders-vs.png#lightbox)

5. Copie **m2repository** do **. zip** extraído para o diretório de **conteúdo** que você criou na etapa anterior: 

    [![Captura de tela de m2repository copiada para a pasta 23.1.1.0/Content](resolving-library-installation-errors-images/07-copied-m2repository-vs.png)](resolving-library-installation-errors-images/07-copied-m2repository-vs.png#lightbox)

6. No diretório **. zip** extraído, navegue até **m2repository\\com\\\\suporte\\ao Android-v4** e abra a pasta correspondente ao número de versão criado acima (neste exemplo, **23.1.1**):

    [![Exemplo de listagem de arquivos contidos na pasta Support-v4/23.1.1](resolving-library-installation-errors-images/08-zip-contents-vs.png)](resolving-library-installation-errors-images/08-zip-contents-vs.png#lightbox)

7. Copie todos os arquivos nesta pasta para o diretório **inserido** criado na etapa 4:

    [![Exemplo de arquivos copiados para a pasta 23.1.1.0/Embedded](resolving-library-installation-errors-images/09-copied-vs.png)](resolving-library-installation-errors-images/09-copied-vs.png#lightbox)

8. Verifique se todos os arquivos são copiados. O diretório **incorporado** agora deve conter arquivos como **. jar**, **. aar**e **. POM**.

9. Descompacte o conteúdo de qualquer arquivo **. aar** extraído para o diretório **inserido** . No Windows, acrescente uma extensão **. zip** ao arquivo **. aar** , abra-o e copie o conteúdo para o diretório **inserido** .
    No macOS, descompacte o arquivo **. aar** usando o comando **unzip** no terminal (por exemplo, **unzip File. aar**).

Neste ponto, você instalou manualmente os componentes ausentes e seu projeto deve compilar sem erros. Caso contrário, verifique se você baixou a versão de arquivo **m2repository** **. zip** que corresponde exatamente à versão na mensagem de erro e verifique se você instalou seu conteúdo nos locais corretos, conforme descrito nas etapas acima. 

## <a name="summary"></a>Resumo 

Este artigo explicou como se recuperar de erros comuns que podem ocorrer durante o download automático e a instalação de bibliotecas de dependências. Ele descreveu como excluir a biblioteca problemática e recompilar o projeto como uma maneira de baixar novamente e reinstalar a biblioteca. Ele descreveu como baixar a biblioteca e instalá-la na pasta **zips** . Ele também descreveu um procedimento mais envolvido para baixar e instalar manualmente os arquivos necessários como uma maneira de solucionar problemas que não podem ser resolvidos por meio de meios automáticos. 
