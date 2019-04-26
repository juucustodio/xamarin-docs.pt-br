---
title: Ajustar os parâmetros de memória do Java para o designer do Android
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 62FAF21C-8090-4AF3-9D88-05A4CFCAFFDC
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/02/2018
ms.openlocfilehash: 9c564789f704180e9acc9f96dcba5e7d6eb20634
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60946732"
---
# <a name="adjusting-java-memory-parameters-for-the-android-designer"></a>Ajustar os parâmetros de memória do Java para o designer do Android

Os parâmetros de memória padrão que são usados ao iniciar o `java` processar para o designer do Android pode ser incompatível com algumas configurações do sistema.

Começando com o Xamarin Studio 5.7.2.7 (e posterior, o Visual Studio para Mac) e ferramentas do Visual Studio para Xamarin 3.9.344, essas configurações podem ser personalizadas em uma base por projeto.

## <a name="new-android-designer-properties-and-corresponding-java-options"></a>Opções de Java correspondentes e novas propriedades do designer Android

Os seguintes nomes de propriedade correspondem do java indicado [opção de linha de comando](http://docs.oracle.com/javase/7/docs/technotes/tools/windows/java.html)

- **AndroidDesignerJavaRendererMinMemory** -Xms

- **AndroidDesignerJavaRendererMaxMemory** -Xmx

- **AndroidDesignerJavaRendererPermSize** -XX:MaxPermSize


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  Abra a solução no Visual Studio.

2.  Selecione cada projeto do Android-individualmente no Gerenciador de soluções e clique em [Show All Files](https://docs.microsoft.com/en-us/previous-versions/visualstudio/visual-studio-2008/4afxey9h(v=vs.90)) duas vezes em cada projeto. Você pode ignorar os projetos que não contêm nenhuma `.axml` arquivos de layout. Essa etapa garante que cada diretório do projeto contém um `.csproj.user` arquivo.

3.  Encerre o Visual Studio.

4.  Localize o `.csproj.user` arquivo para cada um dos projetos da etapa 2.

5.  Edite cada `.csproj.user` em um editor de texto.

6.  Adicionar uma ou todas as novas propriedades de memória de designer Android dentro de um `<PropertyGroup>` elemento. Você pode usar um existente `<PropertyGroup>` ou criar um novo. Aqui está um exemplo completo `.csproj.user` arquivo que inclui todos os 3 atributos definido para seus valores padrão:

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

7.  Salve e feche todas as atualizado `.csproj.user` arquivos.

8.  Reinicie o Visual Studio e reabra a solução.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1.  Abra sua solução no Visual Studio para Mac garantir que o diretório da solução contiver um `.userprefs` arquivo.

2.  Fechar o Visual Studio para Mac.

3.  Localize o `.userprefs` arquivo no diretório da solução.

4.  Editar o `.userprefs` em um editor de texto.

5.  Localize o elemento XML existente com o seguinte formato. A última parte do nome deste elemento corresponderá ao nome do seu projeto: "AndroidApplication1" neste exemplo:

    ```xml
    <MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >
    ```

6.  Se o `<MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >` elemento não existe, crie-o em qualquer lugar dentro de circunscrição `<Properties>` elemento. Certifique-se de substituir "AndroidApplication1" com o nome do seu projeto.

7.  Adicione uma ou todas as novas propriedades de memória de designer Android como atributos no elemento. Aqui está um exemplo completo `.userprefs` arquivo que inclui todos os 3 atributos definido para seus valores padrão:

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

8.  Repita as etapas 5 a 7 para cada projeto do Android na solução que contém qualquer `.axml` arquivos de layout. (Ou seja, adicionar um `<MonoDevelop.Ide.ItemProperties.ProjectName>` elemento para cada projeto.)

9.  Salve e feche o `.userprefs` arquivo.

10. Reinicie o Visual Studio para Mac e reabra a solução.

-----

