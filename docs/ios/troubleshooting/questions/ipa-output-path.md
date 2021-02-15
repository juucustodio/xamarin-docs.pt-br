---
title: Posso alterar o caminho de saída do arquivo IPA?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F5E5DCC6-F7CC-48E2-89E8-709E9C269502
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 303d7d58cc0274b8d9f82c33c9f153b9fd00269f
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437132"
---
# <a name="can-i-change-the-output-path-of-the-ipa-file"></a>Posso alterar o caminho de saída do arquivo IPA?

## <a name="for-cycle-7-and-higher"></a>Para o ciclo 7 e superior
Sim, você pode usar destinos do MSBuild personalizados para conseguir isso. A opção mais fácil é provavelmente copiar o `.ipa` arquivo depois que ele tiver sido compilado.

Essas etapas funcionarão para qualquer projeto iOS que usa o mecanismo de compilação do MSBuild no Mac ou no Windows. (Observação: todos os projetos de API Unificada usam o mecanismo de compilação do MSBuild.)

1. Abra o `.csproj` arquivo para o projeto de aplicativo Ios em um editor de texto e, em seguida, adicione as seguintes linhas no final (imediatamente antes da marca de fechamento `</Project>` ):

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

- A `CreateIpaDependsOn` propriedade é definida no `Xamarin.iOS.Common.targets` arquivo que faz parte do Xamarin. Ios. Ele se comporta conforme descrito na seção [substituindo destinos predefinidos](/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process#overriding-predefined-targets) do artigo [como: estender o processo de compilação do Visual Studio](/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process).

- Você pode usar uma tarefa de **movimentação** em vez de uma tarefa de **cópia** , se preferir. Se você escolher essa opção e estiver criando no Windows, será necessário usar o nome de tarefa totalmente qualificado `<Microsoft.Build.Tasks.Move>` para evitar uma ambiguidade com as tarefas de compilação XamarinVS.

## <a name="for-versions-before-xamarin-studio-6005174--xamarin-for-visual-studio-410530"></a>Para versões anteriores a Xamarin Studio 6.0.0.5174 | Xamarin para Visual Studio 4.1.0.530

Sim, você pode usar destinos do MSBuild personalizados para conseguir isso. A opção mais fácil é provavelmente copiar o `.ipa` arquivo depois que ele tiver sido compilado.

Essas etapas funcionarão para qualquer projeto iOS que usa o mecanismo de compilação do MSBuild no Mac ou no Windows. (Observação: todos os projetos de API Unificada usam o mecanismo de compilação do MSBuild.)

1. Abra o `.csproj` arquivo para o projeto de aplicativo Ios em um editor de texto e, em seguida, adicione as seguintes linhas no final (imediatamente antes da marca de fechamento `</Project>` ).

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

2. Defina o `DestinationFolder` para a pasta de saída desejada. Como de costume, você pode usar as propriedades do MSBuild (como `$(OutputPath)` ) nesse argumento, se desejar.

## <a name="notes"></a>Observações

- A `CreateIpaDependsOn` propriedade é definida no `Xamarin.iOS.Common.targets` arquivo que faz parte do Xamarin. Ios. t se comporta conforme descrito na seção [substituindo destinos predefinidos](/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process#overriding-predefined-targets) do artigo [como: estender o processo de compilação do Visual Studio](/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process).

- Você pode usar uma tarefa de **movimentação** em vez de uma tarefa de **cópia** , se preferir. Se você escolher essa opção e estiver criando no Windows, será necessário usar o nome de tarefa totalmente qualificado `<Microsoft.Build.Tasks.Move>` para evitar uma ambiguidade com as tarefas de compilação XamarinVS.