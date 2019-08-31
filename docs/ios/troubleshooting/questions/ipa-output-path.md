---
title: Posso alterar o caminho de saída do arquivo IPA?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F5E5DCC6-F7CC-48E2-89E8-709E9C269502
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 6a51529e5a8c2878e46946608455f409ece9e43a
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2019
ms.locfileid: "70200219"
---
# <a name="can-i-change-the-output-path-of-the-ipa-file"></a>Posso alterar o caminho de saída do arquivo IPA?

## <a name="for-cycle-7-and-higher"></a>Para o ciclo 7 e superior
Sim, você pode usar destinos do MSBuild personalizados para conseguir isso. A opção mais fácil é provavelmente copiar o `.ipa` arquivo depois que ele tiver sido compilado.

Essas etapas funcionarão para qualquer projeto iOS que usa o mecanismo de compilação do MSBuild no Mac ou no Windows. (Observação: todos os projetos de API Unificada usam o mecanismo de compilação do MSBuild.)

1. Abra o `.csproj` arquivo para o projeto de aplicativo Ios em um editor de texto e, em seguida, adicione as seguintes linhas no final ( `</Project>` imediatamente antes da marca de fechamento):

    ```xml
    <PropertyGroup>
        <CreateIpaDependsOn>
        $(CreateIpaDependsOn);
        CopyIpa
        </CreateIpaDependsOn>
    </PropertyGroup>
    
    <Target Name="CopyIpa"
            Condition="'$(OutputType)' == 'Exe'
            And '$(ComputedPlatform)' == 'iPhone'
            And '$(BuildIpa)' == 'true'">
        <Copy
            SourceFiles="$(IpaPackagePath)"
            DestinationFolder="$(OutputPath)"
        />
    </Target>
    ```

2. Defina o DestinationFolder para a pasta de saída desejada. Como de costume, você pode usar as propriedades do MSBuild (como $ (OutputPath)) nesse argumento, se desejar.

## <a name="notes"></a>Observações
- A `CreateIpaDependsOn` propriedade é definida `Xamarin.iOS.Common.targets` no arquivo que faz parte do Xamarin. Ios. Ele se comporta conforme descrito na seção [substituindo destinos](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process#overriding-predefined-targets) predefinidos do artigo [como: Estenda o processo](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process)de compilação do Visual Studio.

- Você pode usar uma tarefa de **movimentação** em vez de uma tarefa de **cópia** , se preferir. Se você escolher essa opção e estiver criando no Windows, será necessário usar o nome `<Microsoft.Build.Tasks.Move>` de tarefa totalmente qualificado para evitar uma ambiguidade com as tarefas de compilação XamarinVS.

## <a name="for-versions-before-xamarin-studio-6005174--xamarin-for-visual-studio-410530"></a>Para versões anteriores a Xamarin Studio 6.0.0.5174 | Xamarin para Visual Studio 4.1.0.530

Sim, você pode usar destinos do MSBuild personalizados para conseguir isso. A opção mais fácil é provavelmente copiar o `.ipa` arquivo depois que ele tiver sido compilado.

Essas etapas funcionarão para qualquer projeto iOS que usa o mecanismo de compilação do MSBuild no Mac ou no Windows. (Observação: todos os projetos de API Unificada usam o mecanismo de compilação do MSBuild.)

1. Abra o `.csproj` arquivo para o projeto de aplicativo Ios em um editor de texto e, em seguida, adicione as seguintes linhas no final ( `</Project>` imediatamente antes da marca de fechamento).

    ```xml
    <PropertyGroup>
        <CreateIpaDependsOn>
            $(CreateIpaDependsOn);
            CopyIpa
        </CreateIpaDependsOn>
    </PropertyGroup>

    <Target Name="CopyIpa"
            Condition="'$(OutputType)' == 'Exe'
            And '$(ComputedPlatform)' == 'iPhone'
            And '$(BuildIpa)' == 'true'">
        <Copy
            SourceFiles="$(OutputPath)$(IpaPackageName)"
            DestinationFolder="/Users/macuser/Desktop/"
        />
    </Target>
    ```

2. Defina o `DestinationFolder` para a pasta de saída desejada. Como de costume, você pode usar as propriedades `$(OutputPath)`do MSBuild (como) nesse argumento, se desejar.

## <a name="notes"></a>Observações
- A `CreateIpaDependsOn` propriedade é definida `Xamarin.iOS.Common.targets` no arquivo que faz parte do Xamarin. Ios. t se comporta conforme descrito na seção [substituindo destinos](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process#overriding-predefined-targets) predefinidos do artigo [como: Estenda o processo](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process)de compilação do Visual Studio.

- Você pode usar uma tarefa de **movimentação** em vez de uma tarefa de **cópia** , se preferir. Se você escolher essa opção e estiver criando no Windows, será necessário usar o nome `<Microsoft.Build.Tasks.Move>` de tarefa totalmente qualificado para evitar uma ambiguidade com as tarefas de compilação XamarinVS.
