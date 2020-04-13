---
title: Ajustar os parâmetros de memória do Java para o Android Designer
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 62FAF21C-8090-4AF3-9D88-05A4CFCAFFDC
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/02/2018
ms.openlocfilehash: 9c9b9f5a205a2eef7db9f27e8d09b10ce65a4318
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027051"
---
# <a name="adjusting-java-memory-parameters-for-the-android-designer"></a>Ajustar os parâmetros de memória do Java para o Android Designer

Os parâmetros de memória padrão `java` que são usados ao iniciar o processo para o designer Android podem ser incompatíveis com algumas configurações do sistema.

Começando com Xamarin Studio 5.7.2.7 (e posteriormente, Visual Studio para Mac) e Visual Studio Tools para Xamarin 3.9.344, essas configurações podem ser personalizadas por projeto.

## <a name="new-android-designer-properties-and-corresponding-java-options"></a>Novas propriedades do designer do Android e opções java correspondentes

Os seguintes nomes de propriedade correspondem à opção de [linha de comando](https://docs.oracle.com/javase/7/docs/technotes/tools/windows/java.html) java indicada

- **AndroidDesignerJavaRendererMinMemory** -Xms

- **AndroidDesignerJavaRendererMaxMemoryMemory** -Xmx

- **AndroidDesignerJavaRendererPermSize** -XX:MaxPermSize

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Abra sua solução no Visual Studio.

2. Selecione cada projeto Android um por um no Solution Explorer e clique em [Mostrar todos os arquivos](https://docs.microsoft.com/previous-versions/visualstudio/visual-studio-2008/4afxey9h(v=vs.90)) duas vezes em cada projeto. Você pode pular projetos que `.axml` não contêm arquivos de layout. Esta etapa garantirá que cada `.csproj.user` diretório do projeto contenha um arquivo.

3. Encerre o Visual Studio.

4. Localize `.csproj.user` o arquivo de cada um dos projetos a partir da etapa 2.

5. Edite `.csproj.user` cada arquivo em um editor de texto.

6. Adicione qualquer ou todas as novas propriedades `<PropertyGroup>` de memória do designer android dentro de um elemento. Você pode usar `<PropertyGroup>` um existente ou criar um novo. Aqui está um `.csproj.user` arquivo de exemplo completo que inclui todos os 3 atributos definidos em seus valores padrão:

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

7. Salve e feche todos `.csproj.user` os arquivos atualizados.

8. Reinicie o Visual Studio e reabra sua solução.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Abra sua solução no Visual Studio para Mac `.userprefs` para garantir que o diretório de soluções contenha um arquivo.

2. Saia do Visual Studio para o Mac.

3. Localize `.userprefs` o arquivo no diretório de soluções.

4. Edite `.userprefs` o arquivo em um editor de texto.

5. Localize o elemento XML existente com o seguinte formato. A última parte deste nome do elemento corresponderá ao nome do seu projeto: "AndroidApplication1" neste exemplo:

    ```xml
    <MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >
    ```

6. Se `<MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >` o elemento não existir, crie-o `<Properties>` em qualquer lugar dentro do elemento de fechamento. Certifique-se de substituir "AndroidApplication1" pelo nome do seu projeto.

7. Adicione qualquer ou todas as novas propriedades de memória do designer android como atributos no elemento. Aqui está um `.userprefs` arquivo de exemplo completo que inclui todos os 3 atributos definidos em seus valores padrão:

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

8. Repita as etapas 5-7 para cada `.axml` projeto Android na solução que contém quaisquer arquivos de layout. (Ou seja, `<MonoDevelop.Ide.ItemProperties.ProjectName>` adicionar um elemento para cada projeto.)

9. Salve e feche o arquivo `.userprefs`.

10. Reinicie o Visual Studio para Mac e reabra sua solução.

-----
