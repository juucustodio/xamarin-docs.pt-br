---
title: Desinstalando o Xamarin
description: Desinstalando produtos Xamarin de um computador
ms.topic: article
ms.prod: xamarin
ms.assetid: b83a85ec-842a-444c-8f82-c2464eda099b
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 04/08/2017
ms.openlocfilehash: 2c2ba84167924916c3bec27379d33c47e8dab360
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2018
---
# <a name="uninstalling-xamarin"></a>Desinstalando o Xamarin

> [!IMPORTANT]
> Este artigo explica como desinstalar o Xamarin Studio ou outros produtos Xamarin de um computador Mac ou Windows. Para obter informações sobre como desinstalar o Visual Studio para Mac, consulte o guia [Desinstalar](https://docs.microsoft.com/visualstudio/mac/uninstall) em docs.microsoft.com

Há uma série de produtos de Xamarin que permitem o desenvolvimento de aplicativos de plataforma cruzada, incluindo aplicativos autônomos como o Xamarin Studio e extensões para outros aplicativos como o suporte ao Xamarin no Visual Studio.

Este guia explicará como remover a funcionalidade do Xamarin no macOS ou do Visual Studio no Windows:

1.  [Desinstalando o Xamarin Studio](#uninstallxamarinstudio)
  1.  [Desinstalando Mono](#uninstallmono)
  1.  [Desinstalando o Xamarin.Android](#uninstallandroid)
  1.  [Desinstalando o Xamarin.iOS](#uninstallios)
  1.  [Desinstalando o Xamarin.Mac](#uninstallmac)
  2.  [Desinstalação do Workbooks e do Inspector](#uninstallworkbooks)
1.  [Desinstalando o Xamarin do Windows](#uninstallwindows)
  1.  [Desinstalando o Xamarin do Visual Studio 2015 e anteriores](#uninstallvs2015)
  1.  [Desinstalando o Xamarin do Visual Studio 2017](#uninstallvs2017)
1.  [Desinstalando o Visual Studio para Mac](#uninstallvsmac)

Se for necessário reinstalar o Xamarin usando o Instalador Universal, será recomendável sempre que o computador seja reinicializado primeiro.

## <a name="uninstalling-xamarin-on-mac"></a>Desinstalando o Xamarin no Mac

Este guia pode ser usado para desinstalar cada produto individualmente ao navegar até a seção relevante. O conjunto de ferramentas Xamarin inteiro pode ser desinstalado ao seguir todo este guia.

Para obter ajuda com o uso de [Scripts de desinstalação](http://download.xamarin.com/developer/cross-platform/xamarin_uninstall.sh), vá até [Usando o Script de Desinstalação](#uninstallscript) na parte inferior deste guia.

<a name="uninstallxamarinstudio" />

### <a name="uninstall-xamarin-studio"></a>Desinstalar o Xamarin Studio

A primeira etapa da desinstalação do Xamarin Studio em um Mac é localizar **Xamarin Studio.app** no diretório **/Aplicativos** e arrastá-lo para a **Lixeira**. Como alternativa, clique com o botão direito do mouse e selecione **Mover para Lixeira** conforme ilustrado abaixo:

 [![](uninstalling-xamarin-images/image1.png "Como alternativa, clicar com o botão direito do mouse e selecionar Mover para Lixeira conforme ilustrado aqui")](uninstalling-xamarin-images/image1.png#lightbox)

A exclusão desse pacote de aplicativo removerá o Xamarin Studio, no entanto, existem outros arquivos relacionados ao Xamarin ainda em um sistema de arquivos.

Para remover todos os vestígios do Xamarin Studio, os comandos a seguir devem ser executados no Terminal:

```bash
sudo rm -rf "/Applications/Xamarin Studio.app"
rm -rf ~/Library/Caches/XamarinStudio-*
rm -rf ~/Library/Preferences/XamarinStudio-*
rm -rf ~/Library/Logs/XamarinStudio-*
rm -rf ~/Library/XamarinStudio-*
```

<a name="uninstallmono" />

### <a name="uninstall-mono-sdk-mdk"></a>Desinstalar o SDK do Mono (MDK)

O Mono é uma implementação de software livre do .NET Framework da Microsoft usada por todos os Produtos Xamarin — Xamarin.iOS, Xamarin.Android e Xamarin.Mac para permitir o desenvolvimento dessas plataformas em C#.

> [!IMPORTANT]
> Há outros aplicativos fora do Xamarin que também usam Mono, como o Unity. Certifique-se de que não há outras dependências no Mono antes de desinstalá-lo.

Para remover a Estrutura Mono de um computador, execute os seguintes comandos no Terminal:

```bash
sudo rm -rf /Library/Frameworks/Mono.framework
sudo pkgutil --forget com.xamarin.mono-MDK.pkg
sudo rm /etc/paths.d/mono-commands
```

<a name="uninstallandroid" />

### <a name="uninstall-xamarinandroid"></a>Desinstalar o Xamarin.Android

Há um número de itens necessários para a instalação e o uso do Xamarin.Android, como o SDK do Android e o SDK do Java. Mais informações sobre esses componentes necessários estão disponíveis no guia [Instalação Manual](https://docs.microsoft.com/visualstudio/mac/installation/).

Use os comandos a seguir para remover o Xamarin.Android:

```bash
sudo rm -rf /Developer/MonoDroid
rm -rf ~/Library/MonoAndroid
sudo pkgutil --forget com.xamarin.android.pkg
sudo rm -rf /Library/Frameworks/Xamarin.Android.framework
```

#### <a name="uninstall-android-sdk-and-java-sdk"></a>Desinstalar o SDK do Android e o SDK do Java

O SDK do Android é necessário para o desenvolvimento de aplicativos Android. Para remover completamente todas as partes do SDK do Android, localize o arquivo em **~/Library/Developer/Xamarin/** e mova-o para **Lixeira**, conforme ilustrado abaixo:

 [![](uninstalling-xamarin-images/image2.png "Para remover completamente todas as partes do SDK do Android, localizar o arquivo e movê-lo para a Lixeira, conforme ilustrado aqui")](uninstalling-xamarin-images/image2.png#lightbox)

O SDK do Java (JDK) não precisa ser desinstalado, pois já está pré-empacotado como parte do Mac OS X.

<a name="uninstallios" />

### <a name="uninstall-xamarinios"></a>Desinstalar o Xamarin.iOS

O Xamarin.iOS permite desenvolvimento de aplicativos iOS usando C# ou F# com o Xamarin Studio em um Mac.
O Host de Build do Xamarin também foi instalado automaticamente com versões anteriores do Xamarin.iOS para permitir o desenvolvimento do iOS no Visual Studio. Para desinstalar ambos de um computador, siga as etapas abaixo:

Use os seguintes comandos no Terminal para remover todos os arquivos do Xamarin.iOS de um sistema de arquivos:

```bash
rm -rf ~/Library/MonoTouch
sudo rm -rf /Library/Frameworks/Xamarin.iOS.framework
sudo rm -rf /Developer/MonoTouch
sudo pkgutil --forget com.xamarin.monotouch.pkg
sudo pkgutil --forget com.xamarin.xamarin-ios-build-host.pkg
```

#### <a name="uninstall-the-mac-build-host"></a>Desinstalar o Host de Build do Mac

Observação: isso pode já ter sido removido se você tiver atualizado para Xamarin 4. Execute o seguinte comando no Terminal para remover o aplicativo Build Host:

```bash
sudo rm -rf "/Applications/Xamarin.iOS Build Host.app"
```

O processo de Host de Build ou trabalho `launchd` ainda pode estar em execução ou escutando determinadas portas.
Você pode verificar o status executando `launchctl list | grep com.xamarin.mtvs.buildserver` no Terminal.

```bash
sudo launchctl unload /Library/LaunchAgents/com.xamarin.mtvs.buildserver.plist
sudo rm -f /Library/LaunchAgents/com.xamarin.mtvs.buildserver.plist
```

<a name="uninstallmac" />

### <a name="uninstall-xamarinmac"></a>Desinstalar o Xamarin.Mac

Depois que o Xamarin Studio for desinstalado com êxito, o Xamarin.Mac poderá ser removido do seu computador usando os dois comandos a seguir para anular o produto e a licença do seu Mac, respectivamente:

```bash
sudo rm -rf /Library/Frameworks/Xamarin.Mac.framework
rm -rf ~/Library/Xamarin.Mac
```

<a name="uninstallworkbooks" />

### <a name="uninstall-workbooks-and-inspector"></a>Desinstalar o Workbooks e o Inspector

O comando Bash a seguir removerá o Xamarin Inspector e o Workbooks versão 1.2.2 e superior:

```bash
sudo /Library/Frameworks/Xamarin.Interactive.framework/Versions/Current/uninstall
```

Para as versões mais antigas, consulte o guia de desinstalação do [Workbooks](~/tools/workbooks/install.md#uninstall-macos).

### <a name="uninstall-the-xamarin-installer"></a>Desinstalar o Instalador do Xamarin

Use os comandos a seguir para remover todos os vestígios do Instalador Universal do Xamarin:

```bash
rm -rf ~/Library/Caches/XamarinInstaller/
rm -rf ~/Library/Logs/XamarinInstaller/
rm -rf ~/Library/Preferences/Xamarin/
```

<a name="uninstallscript" />

### <a name="using-the-uninstall-script"></a>Usando o Script de Desinstalação

O [script de desinstalação](http://download.xamarin.com/developer/cross-platform/xamarin_uninstall.sh) removerá o Xamarin de um computador. Para usar o script de desinstalação:

1.  Baixe o script de desinstalação e anote o local de download. Por padrão, esse é o diretório **/Downloads**.
1.  Abra **Terminal** e altere o diretório de trabalho para o local em que o script foi baixado:

        $ cd /location/of/file

1. Torne o script executável e execute-o com o **sudo**:

        $ chmod +x ./xamarin_uninstall.sh
        $ sudo ./xamarin_uninstall.sh

1. Por fim, exclua o script de desinstalação.

Neste ponto, o Xamarin deverá ser desinstalado do computador.

<a name="uninstallwindows" />

## <a name="uninstalling-xamarin-on-windows"></a>Desinstalando o Xamarin no Windows

<a name="uninstallvs2015" />

### <a name="visual-studio-2015-and-earlier"></a>Visual Studio 2015 e anteriores

O Xamarin pode ser desinstalado de um computador Windows por meio do **Painel de Controle**. Navegue até **Programas e Recursos** ou **Programas > Desinstalar um Programa** conforme ilustrado abaixo:

 [![](uninstalling-xamarin-images/image3.png "Navegar até Programas e Recursos ou Programas, Desinstalar um Programa conforme ilustrado aqui")](uninstalling-xamarin-images/image3.png#lightbox) [![](uninstalling-xamarin-images/image3.png "Navegar até Programas e Recursos ou Programas, Desinstalar um Programa conforme ilustrado aqui")](uninstalling-xamarin-images/image3.png#lightbox)

Para desinstalar o Xamarin Studio, localize **Xamarin Studio 5.x.x** na lista de programas e clique no botão **Desinstalar**. Para remover a extensão do Xamarin para Visual Studio e os SDKs, localize **Xamarin** na lista de Programas e clique em **Desinstalar**. Isso é ilustrado na captura de tela abaixo:

 [![](uninstalling-xamarin-images/image4a.png "Ilustrados nesta captura de tela")](uninstalling-xamarin-images/image4a.png#lightbox)

Esses programas também podem ser removidos para completar todos os componentes do Xamarin:

-  SDK do Android


  [![](uninstalling-xamarin-images/image5.png "Esses programas também podem ser removidos para todos os componentes do Xamarin")](uninstalling-xamarin-images/image5.png#lightbox)
-  GTK#


  [![](uninstalling-xamarin-images/image6.png "Esses programas também podem ser removidos para todos os componentes do Xamarin")](uninstalling-xamarin-images/image6.png#lightbox)
-  Instalador Universal do Xamarin


 [![](uninstalling-xamarin-images/image7.png "Esses programas também podem ser removidos para todos os componentes do Xamarin")](uninstalling-xamarin-images/image7.png#lightbox)
-  SDK do Java (tenha cuidado ao removê-lo, pois pode haver outras dependências nele)


 [![](uninstalling-xamarin-images/image8.png "Tomar cuidado ao remover o SDK do Java, pois pode haver outras dependências nele")](uninstalling-xamarin-images/image8.png#lightbox)

Para desinstalar completamente o Visual Studio, siga as [Instruções da Microsoft](https://msdn.microsoft.com/library/mt720585.aspx).


<a name="uninstallvs2017" />

## <a name="visual-studio-2017"></a>Visual Studio 2017

O Xamarin pode ser desinstalado do Visual Studio 2017 usando o aplicativo instalador:

1. Use o **menu Iniciar** para abrir o **Instalador do Visual Studio**.

  [![](uninstalling-xamarin-images/vs2017-01-sml.png "Iniciar o Instalador do Visual Studio")](uninstalling-xamarin-images/vs2017-01.png#lightbox)

1. Pressione o botão **Modificar** da instância que deseja alterar.

  [![](uninstalling-xamarin-images/vs2017-02-sml.png "Pressionar o botão Modificar")](uninstalling-xamarin-images/vs2017-02.png#lightbox)

1. Na guia **Cargas de trabalho**, desmarque a opção **Desenvolvimento móvel com .NET** (na seção **Móvel e Jogos**).

  [![](uninstalling-xamarin-images/vs2017-03-sml.png "Desmarcar a carga de trabalho de Desenvolvimento móvel")](uninstalling-xamarin-images/vs2017-03.png#lightbox)

1. Clique no botão **Modificar** na parte inferior direita da janela.
1. O instalador removerá os componentes desmarcados (o Visual Studio 2017 deve ser fechado para que o instalador possa fazer alterações).

  [![](uninstalling-xamarin-images/vs2017-04-sml.png "Pressionar o botão Modificar")](uninstalling-xamarin-images/vs2017-04.png#lightbox)

Componentes individuais do Xamarin (como o criador de perfil ou as pastas de trabalho) podem ser desinstalados, alternando para a guia **Componentes Individuais** na etapa 3 e desmarcando componentes específicos:

[![](uninstalling-xamarin-images/vs2017-components-sml.png "Desinstalar componentes individuais")](uninstalling-xamarin-images/vs2017-components.png#lightbox)

Para desinstalar completamente o Visual Studio 2017, escolha **Desinstalar** no menu de três barras ao lado do botão **Iniciar**.

[![](uninstalling-xamarin-images/vs2017-uninstall-sml.png "Desinstalar o Visual Studio completamente")](uninstalling-xamarin-images/vs2017-uninstall.png#lightbox)

> [!IMPORTANT]
> Se houver duas (ou mais) instâncias do Visual Studio instaladas SxS (lado a lado), como uma versão de lançamento e uma versão prévia, desinstalar uma instância poderá remover algumas funcionalidades do Xamarin de outras instâncias do Visual Studio, incluindo:
>
> - Criador de perfil do Xamarin
> - Pastas de trabalho/Inspetor do Xamarin
> - Simulador do iOS remoto do Xamarin
> - SDK do Bonjour da Apple
>
> Sob determinadas condições, a desinstalação de uma das instâncias SxS pode resultar na remoção incorreta desses recursos. Isso poderá prejudicar o desempenho da plataforma Xamarin nas instâncias do Visual Studio que permanecerem no sistema após a desinstalação da instância SxS.
>
>Isso pode ser resolvido com a execução da opção **Reparar** no instalador do Visual Studio, que reinstalará os componentes ausentes.

<a name="uninstallvsmac" />

## <a name="uninstalling-visual-studio-for-mac"></a>Desinstalando o Visual Studio para Mac

Para desinstalar o Visual Studio para Mac, mas continuar usando o Xamarin Studio, localize **Visual Studio.app** no diretório **/Aplicativos** e arraste-o para a Lixeira. Como alternativa, clique com o botão direito do mouse e selecione **Mover para Lixeira** conforme ilustrado abaixo:

 [![](uninstalling-xamarin-images/image9.png "Clicar com o botão direito do mouse no ícone do Visual Studio e selecionar Mover para Lixeira")](uninstalling-xamarin-images/image9.png#lightbox)

Para desinstalar completamente o Xamarin do seu computador, primeiro exclua o Visual Studio para Mac e, em seguida, siga as etapas listadas na seção [Desinstalar o Xamarin Studio](#uninstallxamarinstudio).

## <a name="summary"></a>Resumo

Neste artigo, examinamos a desinstalação completa do Xamarin de um Mac usando comandos de Terminal, bem como a desinstalação do Xamarin de um computador Windows por meio da opção **Programas e Recursos** (para Visual Studio 2015 e anterior) e usando o **Instalador do Visual Studio** para o Visual Studio 2017.


## <a name="related-links"></a>Links relacionados

- [Script de desinstalação (amostra)](http://download.xamarin.com/developer/cross-platform/xamarin_uninstall.sh)
