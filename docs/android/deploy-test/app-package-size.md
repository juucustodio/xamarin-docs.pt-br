---
title: Tamanho dos pacotes de aplicativos
description: "Este artigo examina as partes que constituem um pacote do aplicativo do Xamarin.Android e as estratégias associadas que podem ser usadas para a implantação eficiente do pacote durante a depuração e as etapas de lançamento de desenvolvimento."
ms.topic: article
ms.prod: xamarin
ms.assetid: 8D70CDDD-3D3C-9949-8045-AB8F93D18E74
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/05/2018
ms.openlocfilehash: 0fff4de7420bceda8c15ae33b03886eb6b332aeb
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="application-package-size"></a>Tamanho dos pacotes de aplicativos

_Este artigo examina as partes que constituem um pacote do aplicativo do Xamarin.Android e as estratégias associadas que podem ser usadas para a implantação eficiente do pacote durante a depuração e as etapas de lançamento de desenvolvimento._


## <a name="overview"></a>Visão geral

Xamarin.Android usa uma variedade de mecanismos para minimizar o tamanho do pacote, mas mantendo uma depuração e um processo de implantação eficientes. Neste artigo, examinamos a versão do Xamarin.Android e o fluxo de trabalho de implantação de depuração, bem como o modo como a plataforma do Xamarin.Android assegura o build e lançamento de pacotes de aplicativos pequenos.


## <a name="release-packages"></a>Pacotes de versão

Para enviar um aplicativo totalmente independente, o pacote deve incluir o aplicativo, as bibliotecas associadas, o conteúdo, o tempo de execução Mono e os assemblies necessários da biblioteca BCL (biblioteca de classes base). Por exemplo, se usássemos o modelo "Hello World" padrão, o conteúdo de um build de pacote completo teria esta aparência:

[ ![Tamanho do pacote antes de vinculador](app-package-size-images/hello-world-package-size-before-linker.png)](app-package-size-images/hello-world-package-size-before-linker.png)

15,8 MB é um tamanho de download maior do que gostaríamos. O problema são as bibliotecas BCL, pois elas incluem mscorlib, System e Mono.Android, que fornecem muitos dos componentes necessários para executar o aplicativo. No entanto, elas também fornecem funcionalidades que você pode não estar usando em seu aplicativo, portanto, pode ser preferível excluir esses componentes.

Quando compilamos um aplicativo para distribuição, executamos um processo conhecido como vinculação, que examina o aplicativo e remove qualquer código que não é usado diretamente. Esse processo é semelhante à funcionalidade que a [Coleta de Lixo](~/android/internals/garbage-collection.md) fornece para memória alocada no heap. Mas, em vez de operar em objetos, a vinculação opera em seu código. Por exemplo, há um namespace inteiro em System.dll para enviar e receber email, mas se seu aplicativo não usa essa funcionalidade, o código está apenas ocupando espaço. Depois de executar o vinculador no aplicativo Olá, Mundo, nosso pacote agora está parecido com isto:

[ ![Tamanho do pacote depois do vinculador](app-package-size-images/hello-world-package-size-after-linker.png)](app-package-size-images/hello-world-package-size-after-linker.png)

Como podemos ver, isso remove uma quantidade significativa de BCL que não estava sendo usada. Observe que o tamanho final da BCL depende do que o aplicativo realmente usa. Por exemplo, se olhamos um aplicativo de exemplo mais significativo chamado ApiDemo, podemos ver que o componente de BCL aumentou de tamanho, porque a ApiDemo usa uma parte maior da BCL do que o Hello, World:

![Tamanho de pacote de ApiDemo depois da vinculação](app-package-size-images/api-demo-package-size-after-linker.png)

Conforme ilustrado aqui, o tamanho do pacote do aplicativo geralmente será aproximadamente 2,9 MB maior do que seu aplicativo e as respectivas dependências.


## <a name="debug-packages"></a>Pacotes de depuração

As coisas são tratadas de forma ligeiramente diferente para builds de depuração. Ao reimplantar repetidamente em um dispositivo, um aplicativo precisa ser tão rápido quanto possível, portanto, podemos otimizar pacotes de depuração para velocidade de implantação, em vez de tamanho.

O Android é relativamente lento para copiar e instalar um pacote, por isso, queremos que o tamanho do pacote seja o menor possível. Conforme abordado acima, uma maneira possível para minimizar o tamanho do pacote é por meio do vinculador. A vinculação é lenta, no entanto, e geralmente queremos implantar apenas as partes do aplicativo que foram alteradas desde a última implantação. Para fazer isso, separamos os componentes principais do Xamarin.Android do nosso aplicativo.

Na primeira vez que depuramos no dispositivo, copiamos dois pacotes grandes chamados *tempo de execução compartilhado* e *plataforma compartilhada*. O tempo de execução compartilhado contém o tempo de execução Mono e a BCL, enquanto a plataforma compartilhada contém os assemblies específicos de nível da API do Android:

[ ![Tamanho de pacote de tempo de execução compartilhado](app-package-size-images/shared-runtime-package-size.png)](app-package-size-images/shared-runtime-package-size.png)

A ação de copiar estes componentes principais é realizada apenas uma vez, já que usa uma grande quantidade de tempo, mas permite que eles sejam usados por quaisquer aplicativos subsequentes em execução no modo de depuração. Por fim, podemos copiar o aplicativo propriamente dito, que é pequeno e rápido:

![O aplicativo propriamente dito é pequeno](app-package-size-images/hello-world-debug-application-no-link.png)

### <a name="fast-assembly-deployment"></a>Implantação de Assembly Rápida

A opção de build *Implantação de Assembly Rápida* pode ser usada para reduzir ainda mais o tamanho do pacote de instalação de depuração, não incluindo os assemblies no pacote do aplicativo, instalando os assemblies diretamente no dispositivo somente uma vez e copiando somente os arquivos que foram modificados desde a última implantação.

Para habilitar a *Implantação de Assembly Rápida*, faça o seguinte:

1.  Clique com o botão direito do mouse no Projeto do Android no Gerenciador de Soluções e selecione **Opções**.

2.  Na caixa de diálogo Opções de Projeto, selecione **Build do Android**:  

    ![Opções do Projeto Build do Android](app-package-size-images/fastdev0.png)

3.  Marque as caixas de seleção **Usar tempo de execução Mono compartilhado** e **Implantação de Assembly Rápida**:  

    ![Caixas de seleção selecionadas na guia Empacotamento](app-package-size-images/fastdev.png)

4.  Clique no botão **OK** para salvar as alterações e fechar a caixa de diálogo Opções de Projeto.


Na próxima vez que o aplicativo for compilado para depuração, os assemblies serão instalados diretamente no dispositivo (se já não tiverem sido) e um pacote do aplicativo menor (que não inclua os assemblies) será instalado no dispositivo. Isso reduzirá o tempo necessário para deixar as alterações ao aplicativo funcionando e prontas para serem testadas.

Suportando a primeira implantação demorada do tempo de execução compartilhado e da plataforma compartilhada, toda vez que fazemos uma alteração ao aplicativo, é possível implantar a nova versão com rapidez e facilidade, de modo que temos um ciclo rápido de alteração/implantação/execução.


## <a name="summary"></a>Resumo

Neste artigo, examinamos as facetas do empacotamento de perfil de depuração e lançamento do Xamarin.Android. Além disso, examinamos as estratégias que a plataforma Mono para Android usa para facilitar a implantação de pacote eficiente durante os estágios de depuração e liberação do desenvolvimento.
