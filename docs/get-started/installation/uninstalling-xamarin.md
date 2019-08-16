---
title: Desinstalando o Xamarin
description: Este documento descreve como desinstalar o Xamarin do Mac e do Windows. Ele fornece instruções específicas de como desinstalar o Mono, o Xamarin.Android, o Xamarin.iOS e outras ferramentas.
ms.prod: xamarin
ms.assetid: b83a85ec-842a-444c-8f82-c2464eda099b
author: asb3993
ms.author: amburns
ms.date: 04/08/2017
ms.openlocfilehash: 97b870d96a2ffc599e010e049554b51b550a7311
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69521588"
---
# <a name="uninstalling-xamarin"></a>Desinstalando o Xamarin

Este guia explica como remover o Xamarin do macOS ou do Visual Studio no Windows.

Se for necessário reinstalar o Xamarin usando o Instalador Universal, será recomendável sempre que o computador seja reinicializado primeiro.

## <a name="uninstalling-xamarin-on-macos"></a>Desinstalando o Xamarin no macOS

Este guia pode ser usado para desinstalar cada produto individualmente ao navegar até a seção relevante. O conjunto de ferramentas Xamarin inteiro, que inclui os produtos listados, pode ser desinstalado ao seguir todo este guia:

- [Mono](#uninstallmono)
- [Xamarin.Android](#uninstallandroid)
- [Xamarin.iOS](#uninstallios)
- [Xamarin.Mac](#uninstallmac)
- [Pastas de trabalho](#uninstallworkbooks)
- [Xamarin Profiler](#uninstallprofiler)
- [Instalador](#uninstallinstaller)

> [!TIP]
> Fornecemos um [script de desinstalação](https://raw.githubusercontent.com/MicrosoftDocs/visualstudio-docs/master/mac/resources/uninstall-vsmac.sh) para uso ao remover o Xamarin do seu computador macOS. Para obter mais informações sobre como usar o script, confira a seção [Usando o script de desinstalação](#uninstallscript) neste guia.

### <a name="uninstalling-visual-studio-for-mac"></a>Desinstalando o Visual Studio para Mac

A primeira etapa da desinstalação do Xamarin em um Mac é localizar **Visual Studio.app** no diretório **/Aplicativos** e arrastá-lo para a **Lixeira**. Como alternativa, clique com botão direito do mouse e selecione **Mover para Lixeira** conforme é ilustrado na imagem a seguir:

![Mova o aplicativo do Visual Studio para a lixeira](uninstalling-xamarin-images/uninstall-image1.png)

Excluir esse pacote de aplicativo removerá o Visual Studio para Mac. Porém, é possível que outros arquivos relacionados ao Xamarin ainda permaneçam em um sistema de arquivos.

Para remover todos os vestígios do Visual Studio para Mac, execute os comandos a seguir no Terminal:

```bash
sudo rm -rf "/Applications/Visual Studio.app"
rm -rf ~/Library/Caches/VisualStudio
rm -rf ~/Library/Preferences/VisualStudio
rm -rf ~/Library/Preferences/Visual\ Studio
rm -rf ~/Library/Logs/VisualStudio
rm -rf ~/Library/VisualStudio
rm -rf ~/Library/Preferences/Xamarin/
rm -rf ~/Library/Developer/Xamarin
rm -rf ~/Library/Application\ Support/VisualStudio
rm -rf ~/Library/Application\ Support/VisualStudio/7.0/LocalInstall/Addins/
```

> [!NOTE]
> Para obter informações sobre como desinstalar o Visual Studio para Mac, consulte o guia [Desinstalar](https://docs.microsoft.com/visualstudio/mac/uninstall) em docs.microsoft.com

<a name="uninstallmono" />

### <a name="uninstall-mono-sdk-mdk"></a>Desinstalar o SDK do Mono (MDK)

O Mono é uma implementação de software livre do .NET Framework usado por todos os Produtos Xamarin – Xamarin.iOS, Xamarin.Android e Xamarin.Mac para permitir o desenvolvimento dessas plataformas em C#.

> [!WARNING]
> Há outros aplicativos fora do Xamarin que também usam Mono, como o Unity. Certifique-se de que não há outras dependências no Mono antes de desinstalá-lo.

Para remover a Estrutura Mono, execute os seguintes comandos no Terminal:

```bash
sudo rm -rf /Library/Frameworks/Mono.framework
sudo pkgutil --forget com.xamarin.mono-MDK.pkg
sudo rm /etc/paths.d/mono-commands
```

<a name="uninstallandroid" />

### <a name="uninstall-xamarinandroid"></a>Desinstalar o Xamarin.Android

Há inúmeros itens que são necessários ao usar Xamarin.Android, como o SDK do Android e o SDK do Java, que precisam ser removidos ao desinstalar o Xamarin.Android. Esta seção orientará você para desinstalar todas as partes necessárias.

Para remover o Xamarin.Android, execute os seguintes comandos no Terminal:

```bash
sudo rm -rf /Developer/MonoDroid
rm -rf ~/Library/MonoAndroid
sudo pkgutil --forget com.xamarin.android.pkg
sudo rm -rf /Library/Frameworks/Xamarin.Android.framework
```

#### <a name="uninstall-android-sdk-and-java-sdk"></a>Desinstalar o SDK do Android e o SDK do Java

O SDK do Android é necessário para o desenvolvimento de aplicativos Android. Para remover completamente todas as partes do SDK do Android, localize o arquivo em **~/Library/Developer/Xamarin/** e mova-o para a **Lixeira**.

O SDK do Java (JDK) não precisa ser desinstalado, pois já está pré-empacotado como parte do Mac OS X.

#### <a name="uninstall-android-avd"></a>Desinstalar o Android AVD

> [!WARNING]
> Há outros aplicativos fora do Visual Studio para Mac que também usam o Android AVD e esses componentes Android adicionais, como o Android Studio.
> Remover esse diretório pode causar falhas em projetos no Android Studio. 

Para remover os Android AVDs e componentes Android adicionais, use o seguinte comando:

```bash
rm -rf ~/.android
```

Para remover apenas os Android AVDs, use o seguinte comando:

```bash
rm -rf ~/.android/avd
```

<a name="uninstallios" />

### <a name="uninstall-xamarinios"></a>Desinstalar o Xamarin.iOS

O Xamarin.iOS permite desenvolver aplicativos iOS usando C# ou F#. Para desinstalar o Xamarin.iOS de um computador, siga as etapas abaixo:

Para remover todos os arquivos do Xamarin.iOS, use os seguintes comandos no Terminal:

```bash
rm -rf ~/Library/MonoTouch
sudo rm -rf /Library/Frameworks/Xamarin.iOS.framework
sudo rm -rf /Developer/MonoTouch
sudo pkgutil --forget com.xamarin.monotouch.pkg
sudo pkgutil --forget com.xamarin.xamarin-ios-build-host.pkg
sudo pkgutil --forget com.xamarin.xamarin.ios.pkg
```

<a name="uninstallmac" />

### <a name="uninstall-xamarinmac"></a>Desinstalar o Xamarin.Mac

O Xamarin.Mac pode ser removido do seu computador usando os dois comandos a seguir para eliminar o produto e a licença do seu Mac, respectivamente:

```bash
sudo rm -rf /Library/Frameworks/Xamarin.Mac.framework
rm -rf ~/Library/Xamarin.Mac
```

<a name="uninstallworkbooks" />

### <a name="uninstall-workbooks"></a>Desinstalar o Workbooks

Para remover o Xamarin Workbooks versão 1.2.2 e superiores, use os seguintes comandos no Terminal:

```bash
sudo /Library/Frameworks/Xamarin.Interactive.framework/Versions/Current/uninstall
```

Para as versões mais antigas, consulte o guia de desinstalação do [Workbooks](~/tools/workbooks/install.md#uninstall-macos).

<a name="uninstallprofiler" />

### <a name="uninstall-the-xamarin-profiler"></a>Desinstale o Xamarin Profiler

Para remover o Xamarin Profiler, use os seguintes comandos no Terminal:

```bash
sudo rm -rf "/Applications/Xamarin Profiler.app"
```

<a name="uninstallinstaller" />

### <a name="uninstall-the-xamarin-installer"></a>Desinstalar o Instalador do Xamarin

Para remover todos os vestígios do Instalador Universal do Xamarin, use os seguintes comandos:

```bash
rm -rf ~/Library/Caches/XamarinInstaller/
rm -rf ~/Library/Caches/VisualStudioInstaller/
rm -rf ~/Library/Logs/XamarinInstaller/
rm -rf ~/Library/Logs/VisualStudioInstaller/
rm -rf ~/Library/Preferences/Xamarin/
rm -rf "~/Library/Preferences/Visual Studio/"
```

<a name="uninstallscript" />

### <a name="using-the-uninstall-script"></a>Usando o Script de Desinstalação

Esse script de desinstalação permite que você desinstale o Visual Studio para Mac e seus componentes Xamarin associados de uma só vez.

O script contém a maioria dos comandos encontrados no artigo. Há duas omissões principais do script que não estão incluídas devido a possíveis dependências externas:

- Desinstalando Mono
- Desinstalando o Android AVD

Para executar o script, execute as seguintes etapas:

1. Clique com botão direito do mouse no script e selecione Salvar Como… para salvar o arquivo no seu Mac.

2. Abra **Terminal** e altere o diretório de trabalho para o local em que o script foi baixado:

    ```
    cd /location/of/file
    ```

3. Torne o script executável e execute-o com o **sudo**:

    ```
    chmod +x ./xamarin_uninstall.sh
    sudo ./xamarin_uninstall.sh
    ```

4. Por fim, exclua o script de desinstalação.

Neste ponto, o Xamarin deverá ser desinstalado do computador.

<a name="uninstallwindows" />

## <a name="uninstalling-xamarin-on-windows"></a>Desinstalando o Xamarin no Windows

O Xamarin é compatível com:

- [Visual Studio 2019 e Visual Studio 2017](#uninstallvs2017)
- [Visual Studio 2015](#uninstallvs2015)
- [Visual Studio 2013](#uninstallvs2015) [**sem suporte**]
- [Xamarin Studio](#uninstallxamarinstudio) [**sem suporte**]

<a name="uninstallvs2017" />

### <a name="visual-studio-2019-and-visual-studio-2017"></a>Visual Studio 2019 e Visual Studio de 2017

O Xamarin é desinstalado do Visual Studio 2019 e do Visual Studio 2017 usando o aplicativo instalador:

1. Use o **menu Iniciar** para abrir o **Instalador do Visual Studio**.

2. Pressione o botão **Modificar** da instância que deseja alterar.

    [![](uninstalling-xamarin-images/vs2017-02-sml.png "Pressionar o botão Modificar")](uninstalling-xamarin-images/vs2017-02.png#lightbox)

3. Na guia **Cargas de trabalho**, desmarque a opção **Desenvolvimento móvel com .NET** (na seção **Móvel e Jogos**).

    [![](uninstalling-xamarin-images/vs2017-03-sml.png "Desmarcar a carga de trabalho de Desenvolvimento móvel")](uninstalling-xamarin-images/vs2017-03.png#lightbox)

4. Clique no botão **Modificar** na parte inferior direita da janela.

5. O instalador removerá os componentes desmarcados (o Visual Studio 2017 deve ser fechado para que o instalador possa fazer alterações).

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
>Isso é resolvido executando a opção **Reparar** no instalador do Visual Studio, o que irá reinstalar os componentes ausentes.


## <a name="uninstalling-older-and-unsupported-products"></a>Desinstalando produtos mais antigos e sem suporte

<a name="uninstallvs2015"></a>

### <a name="visual-studio-2015-and-earlier"></a>Visual Studio 2015 e anteriores

Para desinstalar completamente o Visual Studio 2015, use [a resposta de suporte em visualstudio.com](https://visualstudio.microsoft.com/vs/support/vs2015/uninstall-visual-studio-2015/).

O Xamarin pode ser desinstalado de um computador Windows por meio do **Painel de Controle**. Navegue até **Programas e Recursos** ou **Programas > Desinstalar um Programa** conforme ilustrado abaixo:

 [![](uninstalling-xamarin-images/image3.png "Navegue até Programas e Recursos ou Programas Desinstalar um Programa conforme ilustrado aqui")](uninstalling-xamarin-images/image3.png#lightbox) 

No Painel de Controle, desinstale qualquer um dos seguintes itens que estiverem presentes:

- Xamarin
- Xamarin para Windows
- Xamarin.Android
- Xamarin.iOS
- Xamarin para Visual Studio

No Explorer, exclua todos os arquivos restantes nas pastas de extensão do Xamarin para Visual Studio (todas as versões, incluindo Arquivos de Programas e Arquivos de Programas (x86)):

``` 
C:\Program Files*\Microsoft Visual Studio 1*.0\Common7\IDE\Extensions\Xamarin
```

Exclua o diretório de cache do componente MEF do Visual Studio, que deve estar localizado em:

``` 
%LOCALAPPDATA%\Microsoft\VisualStudio\1*.0\ComponentModelCache
```

Verifique no diretório **VirtualStore** para ver se o Windows pode ter armazenado arquivos de sobreposição para os diretórios **Extensões\Xamarin** ou **ComponentModelCache** em:

``` 
%LOCALAPPDATA%\VirtualStore
```

Abra o editor do registro (regedit) e procure a seguinte chave:

``` 
HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\SharedDlls
```

Localize e exclua as entradas que correspondem a esse padrão:

``` 
C:\Program Files*\Microsoft Visual Studio 1*.0\Common7\IDE\Extensions\Xamarin
```

Procure por esta chave:

``` 
HKEY_CURRENT_USER\Software\Microsoft\VisualStudio\1*.0\ExtensionManager\PendingDeletions
```

Exclua as entradas que parecem ser relacionadas ao Xamarin. Por exemplo, qualquer item que contém os termos `mono` ou `xamarin`.

Abra um prompt de comando cmd.exe como administrador e execute os comandos `devenv /setup` e `devenv /updateconfiguration` para cada versão instalada do Visual Studio. Por exemplo, para o Visual Studio 2015:

```cmd
"%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /setup
"%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /updateconfiguration
```

<a name="uninstallxamarinstudio"></a>

### <a name="uninstall-xamarin-studio-on-windows"></a>Desinstalar o Xamarin Studio no Windows

O Xamarin Studio é desinstalado de um computador Windows por meio do **Painel de Controle**. Navegue até **Programas e Recursos** ou **Programas > Desinstalar um Programa** 

Para desinstalar o Xamarin Studio, localize **Xamarin Studio 5.x.x** na lista de programas e clique no botão **Desinstalar**. 

### <a name="uninstall-xamarin-studio-on-mac"></a>Desinstalar o Xamarin Studio no Mac

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

## <a name="summary"></a>Resumo

Este artigo fornece instruções sobre como desinstalar completamente o Xamarin de um Mac com o uso de comandos do Terminal. Há também instruções sobre como desinstalar o Xamarin de um computador Windows por meio da opção **Programas e Recursos** (para Visual Studio 2015 e anterior) e usando o **Instalador do Visual Studio** para Visual Studio 2017.


## <a name="related-links"></a>Links relacionados

- [Script de desinstalação (amostra)](https://raw.githubusercontent.com/MicrosoftDocs/visualstudio-docs/master/mac/resources/uninstall-vsmac.sh)
