---
title: Resolvendo erros de instalação da biblioteca
description: Em alguns casos, você pode receber erros ao instalar bibliotecas de suporte do Android. Este guia fornece soluções alternativas para alguns erros comuns.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 2AE68ACE-8496-445D-BF17-5E4097D4AE35
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/14/2018
ms.openlocfilehash: a54c69ff708ff7438ef1a8fd14c17e77b5375039
ms.sourcegitcommit: f52aa66de4d07bc00931ac8af791d4c33ee1ea04
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/19/2018
ms.locfileid: "31538085"
---
# <a name="resolving-library-installation-errors"></a>Resolvendo erros de instalação da biblioteca

_Em alguns casos, você pode receber erros ao instalar bibliotecas de suporte do Android. Este guia fornece soluções alternativas para alguns erros comuns._

## <a name="overview"></a>Visão geral

Ao criar um projeto de aplicativo xamarin, poderão ocorrer erros de compilação ao Visual Studio ou o Visual Studio para Mac tentar baixar e instalar bibliotecas de dependência. Muitos desses erros são causados por problemas de conectividade de rede, corrupção de arquivos ou problemas de controle de versão. Este guia descreve os erros de instalação de biblioteca de suporte mais comuns e fornece as etapas para resolver esses problemas e obter seu projeto de aplicativo criá-lo novamente. 

 
 
## <a name="errors-while-downloading-m2repository"></a>Erros durante o download de m2Repository

Você pode ver **m2repository** erros ao fazer referência a um pacote do NuGet dos serviços do Android bibliotecas de suporte ou o Google Play. Essa mensagem de erro é semelhante à exibida a seguir:

```shell
Download failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\22.2.1\content directory.
```

Este exemplo é para **android\_m2repository\_r16**, mas você pode ver essa mesma mensagem de erro para uma versão diferente, como **android\_m2repository\_r18**  ou **android\_m2repository\_r25**. 



### <a name="automatic-recovery-from-m2repository-errors"></a>Recuperação automática de erros de m2repository 

Geralmente, esse problema pode ser corrigido a biblioteca um problema de exclusão e recriação de acordo com estas etapas: 

1. Navegue até o diretório de biblioteca de suporte no seu computador:

    -   No Windows, bibliotecas de suporte estão localizadas em **c:\\usuários\\_username_\\AppData\\Local\\Xamarin**. 

    -   No Mac OS X, bibliotecas de suporte estão localizadas em **/Users/_username_/.local/share/Xamarin**. 

2. Localize a pasta de biblioteca e a versão correspondente à mensagem de erro. Por exemplo, a pasta de biblioteca e versão para a mensagem de erro acima está localizada em **Android.Support.v4\\22.2.1**:

    [![Biblioteca de suporte do local da pasta de exemplo para 22.2.1](resolving-library-installation-errors-images/01-example-location.png)](resolving-library-installation-errors-images/01-example-location.png#lightbox)

3. Exclua o conteúdo da pasta de versão. Certifique-se de remover o **. zip** arquivo, bem como a **conteúdo** e **inserido** subpastas dentro dessa pasta. Para a mensagem de erro de exemplo mostrada acima, os arquivos e subdiretórios mostrados nesta captura de tela (**conteúdo**, **inserido**, e **android_m2repository_r16.zip**) são para excluídos:

    [![Pasta de biblioteca de suporte a conteúdo de exemplo de 22.2.1](resolving-library-installation-errors-images/02-example-folder-vs.png)](resolving-library-installation-errors-images/02-example-folder-vs.png#lightbox)

   Observe que é importante excluir o *todo* conteúdo dessa pasta. Embora essa pasta pode conter inicialmente o "ausente" **android\_m2repository\_r16.zip** arquivo, esse arquivo pode ter sido parcialmente baixado ou corrompido.

4. Recompile o projeto &ndash; fizer isso, o processo de compilação fazer o download novamente a biblioteca ausente.

Na maioria dos casos, essas etapas serão resolver o erro de compilação e permitem que você continue. Se excluir essa biblioteca não resolver o erro de compilação, você deve baixar e instalar manualmente o **android\_m2repository\_r_nn_.zip** arquivo conforme descrito na próxima seção. 



### <a name="manually-downloading-m2repository"></a>Baixar manualmente m2repository

Se você tentou usar as etapas de recuperação automática acima e ainda tiver erros de compilação, você pode baixar manualmente o **android\_m2repository\_r_nn_.zip** arquivo (usando um navegador da web) e instalá-lo de acordo com as seguintes etapas. Esse procedimento também é útil se você não tem acesso à internet no computador de desenvolvimento, mas é possível baixar o arquivo usando um computador diferente. 

1.  Baixe o **android\_m2repository\_r_nn_.zip** arquivo que corresponde à mensagem de erro &ndash; links são fornecidos na lista a seguir (juntamente com o hash MD5 correspondente de cada link URL):

    -   [android\_m2repository\_r33.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r33.zip) &ndash; 5FB756A25962361D17BBE99C3B3FCC44

    -   [android\_m2repository\_r32.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip) &ndash; F16A3455987DBAE5783F058F19F7FCDF

    -   [android\_m2repository\_r31.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r31.zip) &ndash; 99A8907CE2324316E754A95E4C2D786E

    -   [android\_m2repository\_r30.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r30.zip) &ndash; 05AD180B8BDC7C21D6BCB94DDE7F2C8F

    -   [android\_m2repository\_r29.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r29.zip) &ndash; 2A3A8A6D6826EF6CC653030E7D695C41

    -   [android\_m2repository\_r28.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r28.zip) &ndash; 17BE247580748F1EDB72E9F374AA0223

    -   [android\_m2repository\_r27.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r27.zip) &ndash; C9FD4FCD69D7D12B1D9DF076B7BE4E1C

    -   [android\_m2repository\_r26.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r26.zip) &ndash; 8157FC1C311BB36420C1D8992AF54A4D

    -   [android\_m2repository\_r25.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip) &ndash; 0B3F1796C97C707339FB13AE8507AF50

    -   [android\_m2repository\_r24.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r24.zip) &ndash; 8E3C9EC713781EDFE1EFBC5974136BEA

    -   [android\_m2repository\_r23.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r23.zip) &ndash; D5BB66B3640FD9B9C6362C9DB5AB0FE7

    -   [android\_m2repository\_r22.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r22.zip) &ndash; 96659D653BDE0FAEDB818170891F2BB0

    -   [android\_m2repository\_r21.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r21.zip) &ndash; CD3223F2EFE068A26682B9E9C4B6FBB5

    -   [android\_m2repository\_r20.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r20.zip) &ndash; 650E58DF02DB1A832386FA4A2DE46B1A

    -   [android\_m2repository\_r19.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r19.zip) &ndash; 263B062D6EFAA8AEE39E9460B8A5851A

    -   [android\_m2repository\_r18.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r18.zip) &ndash; 25947AD38DCB4865ABEB61522FAFDA0E

    -   [android\_m2repository\_r17.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r17.zip) &ndash; 49054774F44AE5F35A6BA9D3C117EFD8

    -   [android\_m2repository\_r16.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip) &ndash; 0595E577D19D31708195A83087881EE6

    Se o **m2repository** arquivo não é mostrado nesta tabela, você pode criar a URL de download acrescentando **https://dl-ssl.google.com/android/repository/** com o nome do **m2repository** para baixar. Por exemplo, use  **https://dl-ssl.google.com/android/repository/android \_m2repository\_r10.zip** baixar **android\_m2repository\_r10.zip**.

2.  Renomeie o arquivo para o hash MD5 correspondente da URL de download, conforme mostrado na tabela acima. Por exemplo, se você tiver baixado **android\_m2repository\_r25.zip**, renomeá-lo como **0B3F1796C97C707339FB13AE8507AF50.zip**. Se o hash MD5 para a URL de download do arquivo baixado não é mostrado na tabela, você pode usar um [online gerador de MD5](http://www.webconfs.com/online-md5-generator.php) para converter a URL em uma cadeia de caracteres de hash MD5. 

3.  Copie o arquivo para o Xamarin **fechar** pasta: 

    -   No Windows, essa pasta está localizada em **c:\\usuários\\***username***\\AppData\\Local\\Xamarin\\fechar**. 

    -   No Mac OS X, essa pasta está localizada em **/Users/***username***/.local/share/Xamarin/zips**. 

    Por exemplo, a captura de tela a seguir ilustra o resultado quando **android\_m2repository\_r16.zip** é baixado e renomeado para o hash MD5 do seu URL de download no Windows:

    [![Exemplo de repositório r16.zip que está sendo renomeado para 0595E577D19D31708195A83087881EE6.zip](resolving-library-installation-errors-images/03-md5-rename-vs.png)](resolving-library-installation-errors-images/03-md5-rename-vs.png#lightbox)


Se esse procedimento não resolver o erro de compilação, você deve baixar manualmente o **android\_m2repository\_r_nn_.zip** de arquivos, descompacte-o e instale o seu conteúdo conforme descrito na próxima seção. 


### <a name="manually-downloading-and-installing-m2repository-files"></a>Manualmente baixando e instalando arquivos m2repository

O processo manual totalmente para a recuperação de **m2repository** erros infere baixando o **android\_m2repository\_r_nn_.zip** arquivo (usando um navegador da web), descompactando ele e copiar seu conteúdo para o diretório de biblioteca de suporte no seu computador. O exemplo a seguir, vamos recuperará desta mensagem de erro: 

```shell
Unzipping failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\23.1.1\content directory.
```

Use as seguintes etapas para baixar **m2repository** e instalar seu conteúdo:

1.  Exclua o conteúdo da pasta biblioteca correspondente à mensagem de erro. Por exemplo, na mensagem de erro acima você excluirá o conteúdo de **c:\\usuários\\***username***\\AppData\\Local\\Xamarin\\ Android.Support.v4\\23.1.1.0**. 
    Conforme descrito anteriormente, você deve excluir todo o conteúdo deste diretório:

    [![Excluindo o conteúdo incorporado e o 23.1.1.0 android_m2repository pastas pasta](resolving-library-installation-errors-images/04-delete-contents-vs.png)](resolving-library-installation-errors-images/04-delete-contents-vs.png#lightbox)

2.  Baixe o **android\_m2repository\_r_nn_.zip** arquivo do Google que corresponde ao erro de mensagem (consulte a tabela na seção anterior para links).

3.  Extrair essa **. zip** arquivamento em qualquer local (como a área de trabalho). Isso deve criar um diretório que corresponde ao nome do **. zip** arquivamento. Dentro dessa pasta, você deve encontrar uma subpasta chamada **m2repository**: 

    [![pasta m2repository encontrada no arquivo zip extraídos](resolving-library-installation-errors-images/05-m2repository-vs.png)](resolving-library-installation-errors-images/05-m2repository-vs.png#lightbox)

4.  No diretório de biblioteca com versão limpos na etapa 1, crie novamente o **conteúdo** e **inserido** subdiretórios. Por exemplo, a captura de tela a seguir ilustra **conteúdo** e **inserido** subdiretórios que está sendo criados no **23.1.1.0** pasta **android \_m2repository\_r25.zip**: 

    [![Criar conteúdo e pastas incorporadas no 23.1.1.0 pasta](resolving-library-installation-errors-images/06-recreate-folders-vs.png)](resolving-library-installation-errors-images/06-recreate-folders-vs.png#lightbox)

5.  Cópia **m2repository** da **. zip** para o **conteúdo** diretório que você criou na etapa anterior: 

    [![Captura de tela de m2repository copiado para a pasta 23.1.1.0/content](resolving-library-installation-errors-images/07-copied-m2repository-vs.png)](resolving-library-installation-errors-images/07-copied-m2repository-vs.png#lightbox)

6.  No **. zip** directory, navegue até **m2repository\\com\\android\\suporte\\suporte v4** e abra a pasta correspondente o número de versão criados acima (neste exemplo, **23.1.1**):

    [![Listagem de exemplo de arquivos contidos na pasta support-v4/23.1.1](resolving-library-installation-errors-images/08-zip-contents-vs.png)](resolving-library-installation-errors-images/08-zip-contents-vs.png#lightbox)

7.  Copiar todos os arquivos nessa pasta para o **inserido** diretório criado na etapa 4:

    [![Exemplo de arquivos copiados para a pasta 23.1.1.0/embedded](resolving-library-installation-errors-images/09-copied-vs.png)](resolving-library-installation-errors-images/09-copied-vs.png#lightbox)

8.  Verifique se que todos os arquivos são copiados. O **inserido** diretório agora deve conter os arquivos como **. jar**, **. aar**, e **.pom**.

9.  Descompacte o conteúdo de qualquer extraído **. aar** arquivos para o **inserido** directory. No Windows, acrescente um **. zip** extensão para o **. aar** de arquivo, abra-o e copie o conteúdo para o **inserido** directory.
    Em macOS, descompacte o **. aar** arquivo usando o **Descompacte** comando no Terminal (por exemplo, **Descompacte file.aar**).

Neste ponto, você instalou manualmente os componentes ausentes e o projeto deve compilar sem erros. Se não, verifique se você baixou o **m2repository** **. zip** arquivar a versão que corresponde exatamente à versão na mensagem de erro e verifique se que você tenha instalado o seu conteúdo no Corrija locais, conforme descrito nas etapas acima. 



## <a name="summary"></a>Resumo 

Este artigo explicou como se recuperar de erros comuns que podem ocorrer durante o download automático e a instalação das bibliotecas de dependência. Ele descrito como excluir a biblioteca problemática e recompilar o projeto como uma maneira de baixar novamente e reinstale a biblioteca. Ele descrito como baixar a biblioteca e instalá-lo no **fechar** pasta. Ele também descrito um procedimento mais envolvido para manualmente baixando e instalando os arquivos necessários como uma maneira de solucionar problemas que não podem ser resolvidos por meio de forma automática. 
