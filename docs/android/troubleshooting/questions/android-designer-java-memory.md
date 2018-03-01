---
title: "Ajustando os parâmetros de memória do Java para o designer do Android"
ms.topic: article
ms.prod: xamarin
ms.assetid: 62FAF21C-8090-4AF3-9D88-05A4CFCAFFDC
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 354a750fc57fd28754aea902d293e75b65d63997
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="adjusting-java-memory-parameters-for-the-android-designer"></a>Ajustando os parâmetros de memória do Java para o designer do Android

Os parâmetros de memória padrão que são usados ao iniciar o `java` processo para o Android designer pode ser incompatível com algumas configurações do sistema.

Começando com o Xamarin Studio 5.7.2.7 (e posterior, o Visual Studio para Mac) e o Xamarin para Visual Studio 3.9.344 essas configurações podem ser personalizadas em uma base por projeto.

## <a name="new-android-designer-properties-and-corresponding-java-options"></a>Novas propriedades de designer Android e opções de Java correspondentes

Os seguintes nomes de propriedade correspondem de java indicado [opção de linha de comando](http://docs.oracle.com/javase/7/docs/technotes/tools/windows/java.html)

- **AndroidDesignerJavaRendererMinMemory** -Xms

- **AndroidDesignerJavaRendererMaxMemory** -Xmx

- **AndroidDesignerJavaRendererPermSize** -XX:MaxPermSize


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  Abra a solução no Visual Studio.

2.  Selecione cada projeto Android um por um no Gerenciador de soluções e clique em [Mostrar todos os arquivos](https://msdn.microsoft.com/en-us/library/4afxey9h.aspx) duas vezes em cada projeto. Você pode ignorar os projetos que não contêm `.axml` arquivos de layout. Essa etapa garante que cada diretório do projeto conterá um `.csproj.user` arquivo.

3.  Encerre o Visual Studio.

4.  Localize o `.csproj.user` arquivo para cada um dos projetos da etapa 2.

5.  Editar cada `.csproj.user` em um editor de texto.

6.  Adicionar uma ou todas as novas propriedades de memória designer Android dentro de um `<PropertyGroup>` elemento. Você pode usar um existente `<PropertyGroup>` ou criar um novo. Aqui está um exemplo completo `.csproj.user` arquivo que inclui todos os atributos de 3 definido para seus valores padrão:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Project ToolsVersion="12.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
       <PropertyGroup>
         <ProjectView>ProjectFiles</ProjectView>
       </PropertyGroup>
       <PropertyGroup>
         <AndroidDesignerJavaRendererMinMemory>128m</AndroidDesignerJavaRendererMinMemory>
         <AndroidDesignerJavaRendererMaxMemory>750m</AndroidDesignerJavaRendererMaxMemory>
         <AndroidDesignerJavaRendererPermSize>350m</AndroidDesignerJavaRendererPermSize>
       </PropertyGroup>
    </Project>
    ```

7.  Salve e feche todos os arquivos `.csproj.user` arquivos.

8.  Reinicie o Visual Studio e reabra a solução.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1.  Abra sua solução no Visual Studio para Mac garantir que o diretório da solução contiver um `.userprefs` arquivo.

2.  Feche o Visual Studio para Mac.

3.  Localize o `.userprefs` arquivo no diretório da solução.

4.  Editar o `.userprefs` em um editor de texto.

5.  Localize o elemento XML existente com o seguinte formato. A última parte do nome deste elemento corresponderá ao nome do seu projeto: "AndroidApplication1" neste exemplo:

    ```xml
    <MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >
    ```

6.  Se o `<MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >` o elemento não existe, criá-lo em qualquer lugar dentro de circunscrição `<Properties>` elemento. Certifique-se de substituir "AndroidApplication1" com o nome do seu projeto.

7.  Adicione uma ou todas as novas propriedades de memória designer Android como atributos no elemento. Aqui está um exemplo completo `.userprefs` arquivo que inclui todos os atributos de 3 definido para seus valores padrão:

    ```xml
    <Properties StartupItem="AndroidApplication1\AndroidApplication1.csproj">
      <MonoDevelop.Ide.Workspace ActiveConfiguration="Debug" PreferredExecutionTarget="Android.SelectDevice" />
      <MonoDevelop.Ide.Workbench />
      <MonoDevelop.Ide.DebuggingService.Breakpoints>
        <BreakpointStore />
      </MonoDevelop.Ide.DebuggingService.Breakpoints>
      <MonoDevelop.Ide.DebuggingService.PinnedWatches />
      <MonoDevelop.Ide.ItemProperties.AndroidApplication1 AndroidDesignerJavaRendererMinMemory="128m" AndroidDesignerJavaRendererMaxMemory="750m" AndroidDesignerJavaRendererPermSize="350m" />
    </Properties>
    ```

8.  Repita as etapas 5 a 7 para cada projeto Android na solução que contém qualquer `.axml` arquivos de layout. (Ou seja, adicione um `<MonoDevelop.Ide.ItemProperties.ProjectName>` elemento para cada projeto.)

9.  Salve e feche o `.userprefs` arquivo.

10. Reinicie o Visual Studio para Mac e reabra a solução.

-----

