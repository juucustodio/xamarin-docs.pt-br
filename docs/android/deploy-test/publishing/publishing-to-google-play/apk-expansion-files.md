---
title: Arquivos de Expansão APK
ms.prod: xamarin
ms.assetid: DB7E38E8-3C4E-5191-27EA-22DE63044FE2
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 38568fa9258c7e3de2c3333cdca5dc7d5867319c
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117090"
---
# <a name="apk-expansion-files"></a>Arquivos de Expansão APK

Alguns aplicativos (alguns jogos, por exemplo) exigem mais recursos e ativos que podem ser fornecidos no limite de tamanho máximo de aplicativo Android imposto pelo Google Play. Esse limite depende da versão do Android à qual seu APK está destinado:

-  100 MB para APKs destinados a Android 4.0 ou superior (API nível 14 ou superior).
-  50 MB para APKs destinados a Android 3.2 ou inferior (API nível 13 ou superior).

Para superar essa limitação, o Google Play hospedará e distribuirá dois *arquivos de expansão* para acompanharem um APK, permitindo que um aplicativo indiretamente exceda esse limite. 

Na maioria dos dispositivos, quando um aplicativo é instalado, arquivos de expansão serão baixados junto com o APK e serão salvos no local de armazenamento compartilhado (cartão SD ou partição montada em USB) no dispositivo. Em alguns dispositivos mais antigos, os arquivos de expansão podem não ser instalados automaticamente com o APK. Nessas situações, é necessário que o aplicativo contenha código que baixe os arquivos de expansão quando o usuário executar os aplicativos pela primeira vez.

Arquivos de expansão são tratados como *obb (blobs binários opacos)* e podem ter até 2 GB. O Android não executa nenhum processamento especial nesses arquivos depois que eles são baixados &ndash; os arquivos podem estar em qualquer formato adequado ao aplicativo. Conceitualmente, a abordagem recomendada para arquivos de expansão é a seguinte:

-   **Expansão principal** &ndash; Esse arquivo é o arquivo de expansão principal para recursos e ativos que não cabem no limite de tamanho do APK. O arquivo de expansão principal deve conter os ativos primários de que um aplicativo precisa e raramente deve ser atualizado.
-   **Expansão de patch** &ndash; Destina-se a pequenas atualizações ao arquivo de expansão principal. Esse arquivo pode ser atualizado. É responsabilidade do aplicativo executar quaisquer patches ou atualizações necessários desse arquivo.


Os arquivos de expansão devem ser carregados ao mesmo tempo em que o APK é carregado.
O Google Play não permite que um arquivo de expansão seja carregado a um APK existente nem que APKs existentes sejam atualizados. Se for necessário atualizar um arquivo de expansão, um novo APK deverá ser carregado com o `versionCode` atualizado.


## <a name="expansion-file-storage"></a>Armazenamento de arquivo de expansão

Quando os arquivos forem baixados para um dispositivo, eles serão armazenados em **_shared-store_/Android/obb/_package-name_**:

-   **_shared-store_** &ndash; Esse é o diretório especificado por `Android.OS.Environment.ExternalStorageDirectory`.
-   **_package-name_** &ndash; Esse é o nome do pacote do aplicativo no estilo Java.


Após o download, arquivos de expansão não devem ser movidos, alterados, renomeados nem excluídos de seu local no dispositivo. Fazer isso fará os arquivos de expansão serem baixados novamente, excluindo os arquivos antigos. Além disso, o diretório de arquivos de expansão deve conter somente os arquivos do pacote de expansão.

Os arquivos de expansão não oferecem nenhuma segurança nem proteção ao conteúdo &ndash; outros aplicativos ou usuários podem acessar os arquivos salvos no armazenamento compartilhado.

Se for necessário descompactar um arquivo de expansão, os arquivos descompactados deverão ser armazenados em um diretório separado, como um em `Android.OS.Environment.ExternalStorageDirectory`.

Uma alternativa a extrair os arquivos de um arquivo de expansão é ler os ativos ou os recursos diretamente do arquivo de expansão. O arquivo de expansão nada mais é do que um arquivo zip que pode ser usado com um `ContentProvider` apropriado. A [Android.Play.ExpansionLibrary](https://github.com/mattleibow/Android.Play.ExpansionLibrary) contém um assembly, [System.IO.Compression.Zip](https://github.com/mattleibow/Android.Play.ExpansionLibrary/tree/master/System.IO.Compression.Zip), que inclui um `ContentProvider` que permitirá acesso direto do arquivo a alguns arquivos de mídia. Se os arquivos de mídia estiverem sendo empacotados em um arquivo zip, chamadas de reprodução de mídia poderão usar arquivos zip diretamente sem necessidade de descompactá-los. Os arquivos de mídia não devem ser compactados quando adicionados ao arquivo zip. 


### <a name="filename-format"></a>Formato de nome de arquivo

Quando os arquivos de expansão forem baixados, o Google Play usará o seguinte esquema para nomear a expansão:

    [main|patch].<expansion-version>.<package-name>.obb

Os três componentes desse esquema são:

-   `main` ou `patch` &ndash; Especifica se esse é o arquivo de expansão principal ou de patch. Pode haver apenas um de cada.
-   `<expansion-version>` &ndash; É um inteiro que corresponde a `versionCode` do APK ao qual o arquivo foi associado inicialmente.
-   `<package-name>` &ndash; Esse é o nome do pacote do aplicativo no estilo Java.


Por exemplo, se a versão do APK for 21 e o nome do pacote for `mono.samples.helloworld`, o arquivo de expansão principal será denominado **main.21.mono.samples.helloworld**.


## <a name="download-process"></a>Processo de download

Quando um aplicativo é instalado do Google Play, os arquivos de expansão devem ser baixados e salvos junto com o APK. Em determinadas situações, isso pode não acontecer ou os arquivos de expansão podem ser excluídos. Para lidar com essa condição, um aplicativo precisa verificar se os arquivos de expansão existem e então baixá-los, se necessário. O fluxograma a seguir exibe o fluxo de trabalho recomendado desse processo:

[![Fluxograma de expansão de APK](apk-expansion-files-images/apkexpansion.png)](apk-expansion-files-images/apkexpansion.png#lightbox)

Quando um aplicativo é iniciado, ele deve verificar se os arquivos de expansão apropriados existem no dispositivo atual. Se não existirem, o aplicativo deve fazer uma solicitação [Licenciamento de Aplicativos](http://developer.android.com/google/play/licensing/index.html) do Google Play. Essa verificação é feita usando a *LVL (Biblioteca de Verificação de Licença)* e deve ser feita tanto para aplicativos gratuitos quanto para licenciados. A LVL é usada principalmente por aplicativos pagos para impor restrições de licença. No entanto, o Google estendeu a LVL para que possa ser usada com bibliotecas de expansão também. Aplicativos gratuitos precisam realizar a verificação de LVL, mas podem ignorar as restrições de licença. A solicitação da LVL é responsável por fornecer as seguintes informações sobre os arquivos de expansão que o aplicativo requer: 

-   **Tamanho do Arquivo** &ndash; Os tamanhos dos arquivos de expansão são usados como parte da verificação que determina se os arquivos de expansão corretos já foram baixados ou não.
-   **Nomes de Arquivo** &ndash; O nome de arquivo (no dispositivo atual) com o qual os pacotes de expansão devem ser salvos.
-   **URL para Download** &ndash; A URL que deve ser usada para baixar os pacotes de expansão. Isso é exclusivo para cada download e expirará logo após ser fornecido.


Depois da execução da verificação de LVL, o aplicativo deve baixar os arquivos de expansão, levando em consideração os seguintes pontos como parte do download:

-  O dispositivo pode não ter espaço suficiente para armazenar os arquivos de expansão.
-  Se não houver Wi-Fi disponível, o usuário deverá poder pausar ou cancelar o download para evitar encargos de dados indesejados.
-  Os arquivos de expansão são baixados em segundo plano para evitar bloquear interações do usuário.
-  Enquanto o download ocorre em segundo plano, um indicador de progresso deve ser exibido.
-  Os erros que ocorrem durante o download são processados normalmente e recuperáveis.



## <a name="architectural-overview"></a>Visão geral da arquitetura

Quando a atividade principal inicia, ela verifica se os arquivos de expansão são baixados. Se os arquivos forem baixados, sua validade deverá ser verificada.

Se os arquivos de expansão não tiverem sido baixados ou se os arquivos atuais forem inválidos, novos arquivos de expansão deverão ser baixados. Um serviço vinculado é criado como parte do aplicativo. Quando a atividade principal do aplicativo é iniciada, ela usa o serviço vinculado para executar uma verificação com relação aos serviços de Licenciamento do Google para descobrir os nomes do arquivo de expansão e a URL dos arquivos a serem baixados. O serviço associado então baixará os arquivos em um thread em segundo plano.

Para facilitar o esforço necessário para integrar os arquivos de expansão em um aplicativo, o Google criou várias bibliotecas em Java. As bibliotecas em questão são:

-   **Biblioteca do Downloader** &ndash; Essa é uma biblioteca que reduz o esforço necessário para integrar os arquivos de expansão em um aplicativo. A biblioteca baixará os arquivos de expansão em um serviço em segundo plano, exibirá notificações de usuário, lidará com problemas de conectividade de rede, retomará downloads, etc.
-   **LVL (Biblioteca de Verificação de Licença)** &ndash; Uma biblioteca para fazer e processar chamadas aos serviços de Licenciamento de Aplicativos. Também poderá ser usada para realizar verificações de licenciamento para ver se o aplicativo está autorizado para uso no dispositivo.
-   **Biblioteca Zip de Expansão de APK (opcional)** &ndash; Se os arquivos de expansão estiverem em um arquivo zip, essa biblioteca atuará como um provedor de conteúdo e permitirá que um aplicativo leia os recursos e ativos diretamente no arquivo zip sem precisar expandir o arquivo zip.


Essas bibliotecas foram portadas para C# e estão disponíveis sob a licença do Apache 2.0. Para integrar rapidamente arquivos de expansão a um aplicativo existente, essas bibliotecas podem ser adicionadas a um aplicativo Xamarin.Android existente. O código está disponível na [Android.Play.ExpansionLibrary](https://github.com/mattleibow/Android.Play.ExpansionLibrary) no GitHub.
