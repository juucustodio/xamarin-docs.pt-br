---
title: Resolver Erros de Instalação da Biblioteca
description: Em alguns casos, você pode obter erros ao instalar bibliotecas de suporte ao Android. Este guia fornece alternativas para alguns erros comuns.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 2AE68ACE-8496-445D-BF17-5E4097D4AE35
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/14/2018
ms.openlocfilehash: e99e12aa73374cc8145fad0eb5aad7f3259e0ca2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292766"
---
# <a name="resolving-library-installation-errors"></a>Resolver Erros de Instalação da Biblioteca

_Em alguns casos, você pode obter erros ao instalar bibliotecas de suporte ao Android. Este guia fornece alternativas para alguns erros comuns._

## <a name="overview"></a>Visão geral

Ao construir um projeto de aplicativo Xamarin.Android, você pode ter erros de compilação quando o Visual Studio ou o Visual Studio para Mac tentam baixar e instalar bibliotecas de dependência. Muitos desses erros são causados por problemas de conectividade de rede, corrupção de arquivos ou problemas de versão. Este guia descreve os erros mais comuns de instalação da biblioteca de suporte e fornece as etapas para contornar esses problemas e obter a construção do projeto do aplicativo novamente.

## <a name="errors-while-downloading-m2repository"></a>Erros ao baixar m2Repository

Você pode ver erros **de m2repository** ao fazer referência a um pacote NuGet das Bibliotecas de Suporte android ou serviços google play. Essa mensagem de erro é semelhante à exibida a seguir:

```shell
Download failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\22.2.1\content directory.
```

Este exemplo é para **android\_m2repository\_r16**, mas você pode ver esta mesma mensagem de erro para uma versão diferente, como android **\_m2repository\_r18** ou android **\_m2repository\_r25**.

### <a name="automatic-recovery-from-m2repository-errors"></a>Recuperação automática de erros de repositório m2

Muitas vezes, esse problema pode ser corrigido excluindo a biblioteca problemática e reconstruindo de acordo com essas etapas:

1. Navegue até o diretório da biblioteca de suporte em seu computador:

    - No Windows, as bibliotecas de suporte estão localizadas em **\\C: Usuários\\nome de\\_usuário_AppData\\Local\\Xamarin**.

    - No Mac OS X, as bibliotecas de suporte estão localizadas em **/Users/_username_/.local/share/Xamarin**.

2. Localize a biblioteca e a pasta de versão correspondentes à mensagem de erro. Por exemplo, a pasta de biblioteca e versão para a mensagem de erro acima está localizada no **Android.Support.v4\\22.2.1**:

    [![Exemplo de localização da pasta para biblioteca de suporte 22.2.1](resolving-library-installation-errors-images/01-example-location.png)](resolving-library-installation-errors-images/01-example-location.png#lightbox)

3. Exclua o conteúdo da pasta de versão. Certifique-se de remover o arquivo **.zip,** bem como o **conteúdo** e subdiretórios **incorporados** dentro desta pasta. Para a mensagem de erro de exemplo mostrada acima, os arquivos e subdiretórios mostrados nesta captura de tela **(conteúdo**, **incorporado**e **android_m2repository_r16.zip)** devem ser excluídos:

    [![Exemplo de conteúdo da pasta de biblioteca de suporte 22.2.1](resolving-library-installation-errors-images/02-example-folder-vs.png)](resolving-library-installation-errors-images/02-example-folder-vs.png#lightbox)

   Observe que é importante excluir *todo* o conteúdo desta pasta. Embora esta pasta possa inicialmente conter o arquivo **"ausente" do\_Android m2repository\_r16.zip,** este arquivo pode ter sido parcialmente baixado ou corrompido.

4. Reconstruir o &ndash; projeto fazendo isso fará com que o processo de compilação faça o download da biblioteca ausente.

Na maioria dos casos, essas etapas resolverão o erro de compilação e permitirão que você continue. Se a exclusão desta biblioteca não resolver o erro de compilação, você deve baixar manualmente e instalar o arquivo **r_nn_.zip do\_Android m2repository,\_** conforme descrito na próxima seção.

### <a name="manually-downloading-m2repository"></a>Download manual de m2repository

Se você tentou usar as etapas de recuperação automática acima e ainda tiver erros de compilação, você pode baixar manualmente o arquivo **\_android m2repository\_r_nn_.zip** (usando um navegador da Web) e instalá-lo de acordo com as etapas a seguir. Este procedimento também é útil se você não tiver acesso à internet em seu computador de desenvolvimento, mas você é capaz de baixar o arquivo usando um computador diferente.

1. Baixe o arquivo **r_nn_.zip\_do Android m2repository\_** que corresponde aos links de mensagem &ndash; de erro na lista a seguir (juntamente com o hash MD5 correspondente da URL de cada link):

    - [android\_m2repository\_r33.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r33.zip) &ndash; 5FB756A25962361D17BBE99C3B3FCC44

    - [android\_m2repository\_r32.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip) &ndash; F16A3455987DBAE5783F058F19F7FCDF

    - [android\_m2repository\_r31.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r31.zip) &ndash; 99A8907CE2324316E754A95E4C2D786E

    - [android\_m2repository\_r30.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r30.zip) &ndash; 05AD180B8BDC7C21D6BCB94DDE7F2C8F

    - [android\_m2repository\_r29.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r29.zip) &ndash; 2A3A8A6D6826EF6CC6530E7D695C41

    - [android\_m2repository\_r28.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r28.zip) &ndash; 17BE247580748F1EDB72E9F374A0223

    - [android\_m2repository\_r27.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r27.zip) &ndash; C9FD4FCD69D7D12B1D9DF076B7BE4E1C

    - [android\_m2repository\_r26.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r26.zip) &ndash; 8157FC1C311BB36420C1D8992AF54A4D

    - [android\_m2repository\_r25.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip) &ndash; 0B3F1796C97C70739FB13AE8507AF50

    - [android\_m2repository\_r24.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r24.zip) &ndash; 8E3C9EC713781EDFE1EFBC5974136BEA

    - [android\_m2repository\_r23.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r23.zip) &ndash; D5BB66B3640FD9B9C6362C9DB5AB0FE7

    - [android\_m2repository\_r22.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r22.zip) &ndash; 96659D653BDE0FAEDB818170891F2BB0

    - [android\_m2repository\_r21.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r21.zip) &ndash; CD3223F2EFE068A26682B9E9C4B6FBB5

    - [android\_m2repository\_r20.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r20.zip) &ndash; 650E58DF02DB1A83286FA4A2DE46B1A

    - [android\_m2repository\_r19.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r19.zip) &ndash; 263B062D6EFAA8AEE3999460B8A5851A

    - [android\_m2repository\_r18.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r18.zip) &ndash; 25947AD38DCB4865ABEB61522FAFDA0E

    - [android\_m2repository\_r17.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r17.zip) &ndash; 49054774F44AE5F35A6BA9D3C117EFD8

    - [android\_m2repository\_r16.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip) &ndash; 0595E577D19D31708195A83087881EE6

    Se o arquivo **m2repository** não for mostrado nesta tabela, você `https://dl-ssl.google.com/android/repository/` poderá criar a URL de download, dependendo do nome do **m2repository** para download. Por exemplo, **https://dl-ssl.google.com/android/repository/android\_m2repository\_r10.zip** use para baixar **android\_\_m2repository r10.zip**.

2. Renomeie o arquivo para o hash MD5 correspondente da URL de download, conforme mostrado na tabela acima. Por exemplo, se você baixou **o\_\_android m2repository r25.zip**, renomeie-o para **0B3F1796C97C707339FB13AE8507AF50.zip**. Se o hash MD5 para a URL de download do arquivo baixado não for mostrado na tabela, você pode usar um [gerador MD5 on-line](http://www.webconfs.com/online-md5-generator.php) para converter a URL em uma string de hash MD5.

3. Copie o arquivo para a pasta **de zips** Xamarin:

    - No Windows, esta pasta está localizada em **\\C: Usuários\\\\nome***de***\\usuário AppData\\Local\\Xamarin zips**.

    - No Mac OS X, esta pasta está localizada em **/Users/***username***/.local/share/Xamarin/zips**.

    Por exemplo, a captura de tela a seguir ilustra o resultado quando **o\_Android M2repository\_r16.zip** é baixado e renomeado para o hash MD5 de sua URL de download no Windows:

    [![Exemplo do repositório r16.zip sendo renomeado para 0595E577D19D31708195A83087881EE6.zip](resolving-library-installation-errors-images/03-md5-rename-vs.png)](resolving-library-installation-errors-images/03-md5-rename-vs.png#lightbox)

Se este procedimento não resolver o erro de compilação, você deve baixar manualmente o arquivo **android\_m2repository\_r_nn_.zip,** descompactá-lo e instalar seu conteúdo conforme descrito na próxima seção.

### <a name="manually-downloading-and-installing-m2repository-files"></a>Baixando e instalando manualmente arquivos m2repositórios

O processo totalmente manual para recuperação de erros **m2repositórios** implica baixar o arquivo **\_android m2repository\_r_nn_.zip** (usando um navegador da Web), descompactá-lo e copiar seu conteúdo para o diretório da biblioteca de suporte em seu computador. No exemplo a seguir, recuperaremos desta mensagem de erro:

```shell
Unzipping failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\23.1.1\content directory.
```

Use as seguintes etapas para baixar **o m2repository** e instalar seu conteúdo:

1. Exclua o conteúdo da pasta biblioteca correspondente à mensagem de erro. Por exemplo, na mensagem de erro acima você excluiria o conteúdo de **C:\\Usuários\\***nome***\\de usuário AppData\\Local\\Xamarin\\Android.Support.v4\\23.1.1.0**.
    Como descrito anteriormente, você deve excluir todo o conteúdo deste diretório:

    [![Excluindo o conteúdo, incorporado e android_m2repository pastas da pasta 23.1.1.0](resolving-library-installation-errors-images/04-delete-contents-vs.png)](resolving-library-installation-errors-images/04-delete-contents-vs.png#lightbox)

2. Baixe o arquivo **\_android m2repository\_r_nn_.zip** do Google que corresponde à mensagem de erro (veja a tabela na seção anterior para links).

3. Extrair este **arquivo .zip** para qualquer local (como a Área de Trabalho). Isso deve criar um diretório que corresponda ao nome do arquivo **.zip.** Dentro deste diretório, você deve encontrar um subdiretório chamado **m2repository**:

    [![pasta m2repository encontrada em arquivo zip extraído](resolving-library-installation-errors-images/05-m2repository-vs.png)](resolving-library-installation-errors-images/05-m2repository-vs.png#lightbox)

4. No diretório de biblioteca em versão que você limpou na etapa 1, recrie o **conteúdo** e os subdiretórios **incorporados.** Por exemplo, a captura de tela a seguir ilustra **conteúdo** e subdiretórios **incorporados** sendo criados na pasta **23.1.1.0** para **android\_m2repository\_r25.zip**:

    [![Criar conteúdo e pastas incorporadas na pasta 23.1.1.0](resolving-library-installation-errors-images/06-recreate-folders-vs.png)](resolving-library-installation-errors-images/06-recreate-folders-vs.png#lightbox)

5. Copiar **m2repository** do **.zip** extraído no diretório de **conteúdo** que você criou na etapa anterior:

    [![Captura de tela do m2repository copiado para 23.1.1.0/pasta de conteúdo](resolving-library-installation-errors-images/07-copied-m2repository-vs.png)](resolving-library-installation-errors-images/07-copied-m2repository-vs.png#lightbox)

6. No diretório **.zip** extraído, navegue até **m2repository\\com\\suporte ao\\\\android-v4** e abra a pasta correspondente ao número da versão criada acima (neste exemplo, **23.1.1**):

    [![Lista de exemplos de arquivos contidos na pasta suporte-v4/23.1.1](resolving-library-installation-errors-images/08-zip-contents-vs.png)](resolving-library-installation-errors-images/08-zip-contents-vs.png#lightbox)

7. Copie todos os arquivos desta pasta para o diretório **incorporado** criado na etapa 4:

    [![Exemplo de arquivos copiados para a pasta 23.1.1.0/embedded](resolving-library-installation-errors-images/09-copied-vs.png)](resolving-library-installation-errors-images/09-copied-vs.png#lightbox)

8. Verifique se todos os arquivos foram copiados. O diretório **incorporado** agora deve conter arquivos como **.jar**, **.aar**e **.pom**.

9. Descompacte o conteúdo de quaisquer arquivos **.aar extraídos** para o diretório **incorporado.** No Windows, apere uma extensão **.zip** para o arquivo **.aar,** abra-o e copie o conteúdo para o diretório **incorporado.**
    No macOS, descompacte o arquivo **.aar** usando o comando **unzip** no Terminal (por exemplo, **descompactar file.aar**).

Neste ponto, você instalou manualmente os componentes ausentes e seu projeto deve ser construído sem erros. Caso assim, verifique se você baixou a versão **m2repository** **.zip** archive que corresponde exatamente à versão na mensagem de erro e verifique se você instalou seu conteúdo nos locais corretos conforme descrito nas etapas acima.

## <a name="summary"></a>Resumo

Este artigo explicou como se recuperar de erros comuns que podem ocorrer durante o download automático e instalação de bibliotecas de dependência. Ele descreveu como excluir a biblioteca problemática e reconstruir o projeto como uma maneira de rebaixar e reinstalar a biblioteca.
Ele descreveu como baixar a biblioteca e instalá-la na pasta **zips.** Ele também descreveu um procedimento mais envolvido para baixar manualmente e instalar os arquivos necessários como uma maneira de contornar problemas que não podem ser resolvidos por meios automáticos.
