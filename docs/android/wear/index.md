---
title: Android Wear
description: Criando aplicativos para dispositivos Android de vestir.
ms.topic: article
ms.prod: xamarin
ms.assetid: 3BE4A128-2D88-4500-9E48-20375EA99A49
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: ac83b74f39497333de7aa80079784adf61bf2e65
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="android-wear"></a>Android Wear

Desgaste Android é uma versão do Android que foi projetado para dispositivos vestir como relógios. Esta seção inclui instruções sobre como instalar e configurar as ferramentas necessárias para o desenvolvimento de desgaste, passo a passo para criar seu primeiro dispositivo de desgaste e uma lista de exemplos que você pode consultar para criar seus próprios aplicativos de uso.

##  <a name="getting-startedandroidwearget-startedindexmd"></a>[Introdução](~/android/wear/get-started/index.md)

Apresenta desgaste Android, descreve como instalar e configurar o computador para o desenvolvimento de desgaste e fornece etapas para ajudá-lo a criar e executar seu primeiro aplicativo do Android desempenham em um emulador ou dispositivo de desgaste.

##  <a name="user-interfaceandroidwearuser-interfaceindexmd"></a>[Interface do Usuário](~/android/wear/user-interface/index.md)

Explica o Android desgaste controla e fornece links para exemplos que demonstram como usar esses controles.

##  <a name="platform-featuresandroidwearplatformindexmd"></a>[Recursos da plataforma](~/android/wear/platform/index.md)

Documentos nesta seção abordam os recursos específicos do desgaste Android. Aqui você encontrará um tópico que descreve como criar um WatchFace.

##  <a name="screen-sizesandroidwearscreen-sizesmd"></a>[Tamanhos de tela](~/android/wear/screen-sizes.md)

Visualize e otimizar sua interface de usuário para os tamanhos de tela disponíveis.

##  <a name="deployment--testingandroidweardeploy-testindexmd"></a>[Implantação e Teste](~/android/wear/deploy-test/index.md)

Explica como implantar seu aplicativo Android usam a um dispositivo Android desgaste ou emulador Android configurado para uso. Também inclui informações sobre como configurar uma conexão Bluetooth entre o computador de desenvolvimento e um dispositivo Android e dicas de depuração.



## <a name="samples"></a>Exemplos

Você pode encontrar um número de [exemplos](https://developer.xamarin.com/samples/android/Android%20Wear/) usando desgaste Android (ou vá diretamente para [github](https://github.com/xamarin/monodroid-samples/tree/master/wear)). 

<table align="center" border="1" cellpadding="1" cellspacing="1">
  <thead>
      <th>
          <strong>Exemplo</strong>
      </th>
      <th>
          <strong>Descrição</strong>
      </th>
      <th>
          <strong>captura de tela</strong>
      </th>
  </thead>
  <tbody>
  <tr>
      <td valign="top">
          <a href="https://developer.xamarin.com/samples/SkeletonWear/">SkeletonWear</a>
      </td>
      <td valign="top">
Um exemplo simples de Noções básicas de vestir projetos, incluindo GridViewPager e notificações interativas.
      </td>
      <td>
          <img src="Images/skeleton.png" class="tableimg">
      </td>
  </tr>
  <tr>
      <td valign="top">
          <a href="https://developer.xamarin.com/samples/WatchViewStub/">WatchViewStub</a>
      </td>
      <td valign="top">
Uma demonstração simple do controle WatchViewStub detectar forma de tela e carrega automaticamente o layout correto.
Consulte como WatchViewStub funciona no <b>Resources/layout/main_actvity.xml</b> layout.
      </td>
      <td>
          <img src="Images/watchview.png" class="tableimg">
      </td>
  </tr>
  <tr>
      <td valign="top">
          <a href="https://developer.xamarin.com/samples/RecipeAssistant/">RecipeAssistant</a>
      </td>
      <td valign="top">
Demonstração de desgaste páginas de notificação, na forma de etapas de receita. As notificações são criadas em <b>RecipeService.cs</b>.
      </td>
      <td>
          <img src="Images/recipeassist.png" class="tableimg">
      </td>
  </tr>
  <tr>
      <td valign="top">
          <a href="https://developer.xamarin.com/samples/ElizaChat/">ElizaChat</a>
      </td>
      <td valign="top">
Exemplo divertido de interagir com um assistente"pessoal" chamado Eliza, usando notificações de desgaste interativas para criar uma conversa usando respostas e padronizadas.
      </td>
      <td>
          <img src="Images/eliza.png" class="tableimg">
      </td>
  </tr>
  <tr>
      <td valign="top">
          <a href="https://developer.xamarin.com/samples/GridViewPager/">GridViewPager</a>
      </td>
      <td valign="top">
GridViewPager implementa o padrão 2D de navegação, em que o usuário obtém verticalmente e horizontalmente para percorrer as opções e conteúdo.
      </td>
      <td>
          <img src="Images/gridviewpager.png" class="tableimg">
      </td>
  </tr>
  <tr>
      <td valign="top">
          <a href="https://developer.xamarin.com/samples/monodroid/wear/WatchFace">WatchFace</a>
      </td>
      <td valign="top">
          <b>WatchFace</b> é uma face do relógio de personalizado com estilo analógico hora, minuto e segundo mãos. Este exemplo demonstra como criar um serviço de face de observação que desenha a hora atual e eventos de alteração de modo de ambiente de identificadores e visibilidade. Ele inclui um receptor de difusão que escuta de alterações de fuso horário e atualiza automaticamente o tempo.
      </td>
      <td>
          <img src="Images/watchface.png" class="tableimg">
      </td>
  </tr>
  </tbody>
</table>

##  <a name="videos"></a>Vídeos

Check-out de vídeo esses links que abordam xamarin com desgaste de suporte.

<table align="center" border="0" cellpadding="1" cellspacing="1">
    <tr>
        <td>
        <a href="http://blog.xamarin.com/webinar-recording-android-l-and-so-much-more/"><img src="Images/video-android-l.png" border="0" /></td>
        <td><a href="http://blog.xamarin.com/webinar-recording-android-l-and-so-much-more/">L Android e muito mais</a>
        <br />
A visualização do desenvolvedor L Android introduziu uma infinidade de novas APIs para desenvolvedores aproveitar, incluindo o Material de Design, notificações e novas animações, para citar alguns.</td>
    </tr>
    <tr>
        <td>
        <a href="https://www.youtube.com/watch?v=80H8tXByZQc"><img src="Images/video-eyes-ears.png" border="0" /></td>
        <td><a href="https://www.youtube.com/watch?v=80H8tXByZQc">C# é em meu ouvidos em meus olhos: Google Glass e desgaste Android</a>
        <br />
Computação vestir pode parecer algo do futuro (ou um episódio de Inspetor Gadget), mas muitas pessoas já estão adotando futuro hoje! Os desenvolvedores do c# sabem isso e já tem as ferramentas e habilidades aproveitamento da capacidade de vestir dispositivos (Evolve 2014).</td>
    </tr>
    <tr>
        <td>
        <a href="https://www.youtube.com/watch?v=Gpqc2XZIQfU"><img src="Images/video-whats-new.png" border="0" /></td>
        <td><a href="https://www.youtube.com/watch?v=Gpqc2XZIQfU">O que há de novo no xamarin</a>
        <br />
        <i>L Android, desgaste Android, TV Android, Android automática, Design de Material e arte; o que isso significa para você como um desenvolvedor de Xamarin? </i> de evoluir 2014.</td>
    </tr>
</table>


<!--

March 18
http://blog.xamarin.com/android-wear/

August 14
http://blog.xamarin.com/android-l-developer-preview-android-wear-support/

August 27
http://blog.xamarin.com/tips-for-your-first-android-wear-app/

Watch Face
https://github.com/Redth/Xamarin.Wear.WatchFace
-->
