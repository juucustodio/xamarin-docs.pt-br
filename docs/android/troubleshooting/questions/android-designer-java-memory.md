---
title: Ajustar os parâmetros de memória do Java para o Android Designer
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 62FAF21C-8090-4AF3-9D88-05A4CFCAFFDC
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/02/2018
ms.openlocfilehash: 05d72c2b9cea3972a8173ea0656f5a84c2b3d51c
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69523498"
---
# <a name="adjusting-java-memory-parameters-for-the-android-designer"></a>Ajustar os parâmetros de memória do Java para o Android Designer

Os parâmetros de memória padrão que são usados ao iniciar `java` o processo para o Android designer podem ser incompatíveis com algumas configurações do sistema.

A partir do Xamarin Studio 5.7.2.7 (e posterior, Visual Studio para Mac) e do Ferramentas do Visual Studio para o Xamarin 3.9.344, essas configurações podem ser personalizadas em uma base por projeto.

## <a name="new-android-designer-properties-and-corresponding-java-options"></a>Novas propriedades do designer do Android e opções Java correspondentes

Os seguintes nomes de propriedade correspondem à opção de [linha de comando](http://docs.oracle.com/javase/7/docs/technotes/tools/windows/java.html) Java indicada

- **AndroidDesignerJavaRendererMinMemory** -Xms

- **AndroidDesignerJavaRendererMaxMemory** -Xmx

- **AndroidDesignerJavaRendererPermSize** -XX:MaxPermSize


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Abra a solução no Visual Studio.

2. Selecione cada projeto do Android um por um no Gerenciador de Soluções e clique em [Mostrar todos os arquivos](https://docs.microsoft.com/previous-versions/visualstudio/visual-studio-2008/4afxey9h(v=vs.90)) duas vezes em cada projeto. Você pode ignorar projetos que não contêm nenhum `.axml` arquivo de layout. Esta etapa garantirá que cada diretório do projeto contenha um `.csproj.user` arquivo.

3. Encerre o Visual Studio.

4. Localize o `.csproj.user` arquivo para cada um dos projetos da etapa 2.

5. Edite `.csproj.user` cada arquivo em um editor de texto.

6. Adicione qualquer uma ou todas as novas propriedades de memória do designer do `<PropertyGroup>` Android dentro de um elemento. Você pode usar um existente `<PropertyGroup>` ou criar um novo. Aqui está um arquivo de `.csproj.user` exemplo completo que inclui todos os 3 atributos definidos para seus valores padrão:

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

7. Salve e feche todos os arquivos atualizados `.csproj.user` .

8. Reinicie o Visual Studio e reabra sua solução.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Abra sua solução no Visual Studio para Mac para garantir que o diretório da solução `.userprefs` contenha um arquivo.

2. Encerre Visual Studio para Mac.

3. Localize o `.userprefs` arquivo no diretório da solução.

4. Edite `.userprefs` o arquivo em um editor de texto.

5. Localize o elemento XML existente com o formato a seguir. A última parte deste nome de elemento corresponderá ao nome do seu projeto: "AndroidApplication1" neste exemplo:

    ```xml
    <MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >
    ```

6. Se o `<MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >` elemento não existir, crie-o em qualquer lugar dentro do `<Properties>` elemento delimitador. Certifique-se de substituir "AndroidApplication1" pelo nome do seu projeto.

7. Adicione uma ou todas as novas propriedades de memória do designer do Android como atributos no elemento. Aqui está um arquivo de `.userprefs` exemplo completo que inclui todos os 3 atributos definidos para seus valores padrão:

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

8. Repita as etapas de 5-7 para cada projeto do Android na solução que `.axml` contém qualquer arquivo de layout. (Ou seja, adicione um `<MonoDevelop.Ide.ItemProperties.ProjectName>` elemento para cada projeto.)

9. Salve e feche o `.userprefs` arquivo.

10. Reinicie Visual Studio para Mac e reabra sua solução.

-----

