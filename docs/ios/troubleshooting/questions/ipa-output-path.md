---
title: Posso alterar o caminho de saída do arquivo IPA?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F5E5DCC6-F7CC-48E2-89E8-709E9C269502
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 6df481688bfaa1e23cc56e6e34586f23d8ab9da6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031032"
---
# <a name="can-i-change-the-output-path-of-the-ipa-file"></a>Posso alterar o caminho de saída do arquivo IPA?

## <a name="for-cycle-7-and-higher"></a>Para o ciclo 7 e superior
Sim, você pode usar destinos do MSBuild personalizados para conseguir isso. A opção mais fácil é, provavelmente, copiar o arquivo `.ipa` depois que ele tiver sido compilado.

Essas etapas funcionarão para qualquer projeto iOS que usa o mecanismo de compilação do MSBuild no Mac ou no Windows. (Observação: todos os projetos de API Unificada usam o mecanismo de compilação do MSBuild.)

1. Abra o arquivo `.csproj` para o projeto de aplicativo iOS em um editor de texto e, em seguida, adicione as seguintes linhas no final (imediatamente antes da marca de `</Project>` de fechamento):

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

## <a name="notes"></a>Anotações

- A propriedade `CreateIpaDependsOn` é definida no arquivo `Xamarin.iOS.Common.targets` que faz parte do Xamarin. iOS. Ele se comporta conforme descrito na seção [substituindo destinos predefinidos](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process#overriding-predefined-targets) do artigo [como: estender o processo de compilação do Visual Studio](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process).

- Você pode usar uma tarefa de **movimentação** em vez de uma tarefa de **cópia** , se preferir. Se você escolher essa opção e estiver criando no Windows, será necessário usar o nome de tarefa totalmente qualificado `<Microsoft.Build.Tasks.Move>` para evitar uma ambiguidade com as tarefas de compilação XamarinVS.

## <a name="for-versions-before-xamarin-studio-6005174--xamarin-for-visual-studio-410530"></a>Para versões anteriores a Xamarin Studio 6.0.0.5174 | Xamarin para Visual Studio 4.1.0.530

Sim, você pode usar destinos do MSBuild personalizados para conseguir isso. A opção mais fácil é, provavelmente, copiar o arquivo `.ipa` depois que ele tiver sido compilado.

Essas etapas funcionarão para qualquer projeto iOS que usa o mecanismo de compilação do MSBuild no Mac ou no Windows. (Observação: todos os projetos de API Unificada usam o mecanismo de compilação do MSBuild.)

1. Abra o arquivo `.csproj` para o projeto de aplicativo iOS em um editor de texto e, em seguida, adicione as seguintes linhas no final (imediatamente antes da marca de `</Project>` de fechamento).

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

2. Defina o `DestinationFolder` para a pasta de saída desejada. Como de costume, você pode usar as propriedades do MSBuild (como `$(OutputPath)`) nesse argumento, se desejar.

## <a name="notes"></a>Anotações

- A propriedade `CreateIpaDependsOn` é definida no arquivo `Xamarin.iOS.Common.targets` que faz parte do Xamarin. iOS. t se comporta conforme descrito na seção [substituindo destinos predefinidos](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process#overriding-predefined-targets) do artigo [como: estender o processo de compilação do Visual Studio](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process).

- Você pode usar uma tarefa de **movimentação** em vez de uma tarefa de **cópia** , se preferir. Se você escolher essa opção e estiver criando no Windows, será necessário usar o nome de tarefa totalmente qualificado `<Microsoft.Build.Tasks.Move>` para evitar uma ambiguidade com as tarefas de compilação XamarinVS.
