---
title: Desinstalar o Xamarin
description: Este documento descreve como desinstalar o Xamarin do Visual Studio no Windows.
ms.prod: xamarin
ms.assetid: b83a85ec-842a-444c-8f82-c2464eda099b
author: conceptdev
ms.author: crdun
ms.date: 01/22/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b4d644591ab85f185709e7bd53353580a6578d77
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84570104"
---
# <a name="uninstall-xamarin-from-visual-studio"></a>Desinstalar o Xamarin do Visual Studio

Este guia explica como remover o Xamarin do Visual Studio no Windows.

<a name="uninstallvs2017"></a>

## <a name="visual-studio-2019-and-visual-studio-2017"></a>Visual Studio 2019 e Visual Studio de 2017

O Xamarin é desinstalado do Visual Studio 2019 e do Visual Studio 2017 usando o aplicativo instalador:

1. Use o **menu Iniciar** para abrir o **Instalador do Visual Studio**.

2. Pressione o botão **Modificar** da instância que deseja alterar.

    [![](uninstalling-xamarin-images/vs2017-02-sml.png "Press the modify button")](uninstalling-xamarin-images/vs2017-02.png#lightbox)

3. Na guia **Cargas de trabalho**, desmarque a opção **Desenvolvimento móvel com .NET** (na seção **Móvel e Jogos**).

    [![](uninstalling-xamarin-images/vs2017-03-sml.png "Uncheck the Mobile Development workload")](uninstalling-xamarin-images/vs2017-03.png#lightbox)

4. Clique no botão **Modificar** na parte inferior direita da janela.

5. O instalador removerá os componentes desmarcados (o Visual Studio 2017 deve ser fechado para que o instalador possa fazer alterações).

    [![](uninstalling-xamarin-images/vs2017-04-sml.png "Press the Modify button")](uninstalling-xamarin-images/vs2017-04.png#lightbox)

Componentes individuais do Xamarin (como o criador de perfil ou pastas de trabalho) podem ser desinstalados alternando para a guia **componentes individuais** na etapa 3 e desmarcando componentes específicos:

[![](uninstalling-xamarin-images/vs2017-components-sml.png "Uninstall individual components")](uninstalling-xamarin-images/vs2017-components.png#lightbox)

Para desinstalar completamente o Visual Studio 2017, escolha **Desinstalar** no menu de três barras ao lado do botão **Iniciar**.

[![](uninstalling-xamarin-images/vs2017-uninstall-sml.png "Uninstall Visual Studio completely")](uninstalling-xamarin-images/vs2017-uninstall.png#lightbox)

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

<a name="uninstallvs2015"></a>

## <a name="visual-studio-2015-and-earlier"></a>Visual Studio 2015 e anteriores

Para desinstalar completamente o Visual Studio 2015, use [a resposta de suporte em visualstudio.com](https://visualstudio.microsoft.com/vs/support/vs2015/uninstall-visual-studio-2015/).

O Xamarin pode ser desinstalado de um computador Windows por meio do **Painel de Controle**. Navegue até **Programas e Recursos** ou **Programas > Desinstalar um Programa** conforme ilustrado abaixo:

 [![](uninstalling-xamarin-images/image3.png "Navigate to Programs and Features or Programs  Uninstall a Program as illustrated here")](uninstalling-xamarin-images/image3.png#lightbox)

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
