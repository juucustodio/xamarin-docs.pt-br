---
title: Ajustar os parâmetros de memória do Java para o Android Designer
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 62FAF21C-8090-4AF3-9D88-05A4CFCAFFDC
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/02/2018
ms.openlocfilehash: 5a692a931bfcdc1e8eee534de3adfff0de688891
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457894"
---
# <a name="adjusting-java-memory-parameters-for-the-android-designer"></a>Ajustar os parâmetros de memória do Java para o Android Designer

Os parâmetros de memória padrão que são usados ao iniciar o `java` processo para o Android designer podem ser incompatíveis com algumas configurações do sistema.

A partir do Xamarin Studio 5.7.2.7 (e posterior, Visual Studio para Mac) e do Ferramentas do Visual Studio para o Xamarin 3.9.344, essas configurações podem ser personalizadas em uma base por projeto.

## <a name="new-android-designer-properties-and-corresponding-java-options"></a>Novas propriedades do designer do Android e opções Java correspondentes

Os seguintes nomes de propriedade correspondem à opção de [linha de comando](https://docs.oracle.com/javase/7/docs/technotes/tools/windows/java.html) Java indicada

- **AndroidDesignerJavaRendererMinMemory** -XMS

- **AndroidDesignerJavaRendererMaxMemory** -Xmx

- **AndroidDesignerJavaRendererPermSize** -XX: MaxPermSize

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Abra sua solução no Visual Studio.

2. Selecione cada projeto do Android um por um no Gerenciador de Soluções e clique em [Mostrar todos os arquivos](/previous-versions/visualstudio/visual-studio-2008/4afxey9h(v=vs.90)) duas vezes em cada projeto. Você pode ignorar projetos que não contêm nenhum `.axml` arquivo de layout. Esta etapa garantirá que cada diretório do projeto contenha um `.csproj.user` arquivo.

3. Encerre o Visual Studio.

4. Localize o `.csproj.user` arquivo para cada um dos projetos da etapa 2.

5. Edite cada `.csproj.user` arquivo em um editor de texto.

6. Adicione qualquer uma ou todas as novas propriedades de memória do designer do Android dentro de um `<PropertyGroup>` elemento. Você pode usar um existente `<PropertyGroup>` ou criar um novo. Aqui está um arquivo de exemplo completo `.csproj.user` que inclui todos os 3 atributos definidos para seus valores padrão:

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

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Abra sua solução no Visual Studio para Mac para garantir que o diretório da solução contenha um `.userprefs` arquivo.

2. Encerre Visual Studio para Mac.

3. Localize o `.userprefs` arquivo no diretório da solução.

4. Edite o `.userprefs` arquivo em um editor de texto.

5. Localize o elemento XML existente com o formato a seguir. A última parte deste nome de elemento corresponderá ao nome do seu projeto: "AndroidApplication1" neste exemplo:

    ```xml
    <MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >
    ```

6. Se o `<MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >` elemento não existir, crie-o em qualquer lugar dentro do elemento delimitador `<Properties>` . Certifique-se de substituir "AndroidApplication1" pelo nome do seu projeto.

7. Adicione uma ou todas as novas propriedades de memória do designer do Android como atributos no elemento. Aqui está um arquivo de exemplo completo `.userprefs` que inclui todos os 3 atributos definidos para seus valores padrão:

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

8. Repita as etapas de 5-7 para cada projeto do Android na solução que contém qualquer `.axml` arquivo de layout. (Ou seja, adicione um `<MonoDevelop.Ide.ItemProperties.ProjectName>` elemento para cada projeto.)

9. Salve e feche o arquivo `.userprefs`.

10. Reinicie Visual Studio para Mac e reabra sua solução.

-----