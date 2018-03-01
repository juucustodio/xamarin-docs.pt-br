---
title: "Alterar o caminho de saída do arquivo IPA?"
ms.topic: article
ms.prod: xamarin
ms.assetid: F5E5DCC6-F7CC-48E2-89E8-709E9C269502
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 2cb5ef615bfd965ce3fbd4efbab7669fe12679a4
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="can-i-change-the-output-path-of-the-ipa-file"></a>Alterar o caminho de saída do arquivo IPA?

## <a name="for-cycle-7-and-higher"></a>Para o ciclo de 7 e superior
Sim, você pode usar personalizados destinos do MSBuild para fazer isso. A opção mais fácil é provavelmente copiar o `.ipa` arquivo depois que ele foi criado.

Essas etapas funcionará para qualquer projeto do iOS que usa o mecanismo de compilação do MSBuild no Mac ou Windows. (Observação: todos os projetos de API unificada usam o mecanismo de compilação do MSBuild.)

1. Abra o `.csproj` de arquivos para o projeto de aplicativo do iOS em um editor de texto e, em seguida, adicione as seguintes linhas ao final (imediatamente antes do fechamento `</Project>` marca):
    
    ```
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

2. Defina o DestinationFolder para a pasta de saída desejada. Normalmente, você pode usar propriedades MSBuild (como $(OutputPath)) dentro desse argumento se desejar.

## <a name="notes"></a>Observações
- O `CreateIpaDependsOn` propriedade está definida no `Xamarin.iOS.Common.targets` arquivo que é parte do xamarin. Ele se comporta conforme descrito em *substituindo 'DependsOn' propriedades* na [https://msdn.microsoft.com/en-us/library/ms366724.aspx](https://msdn.microsoft.com/en-us/library/ms366724.aspx).

- Você pode usar um **mover** tarefas em vez de **cópia** tarefa se você preferir. Se você escolher a opção e você está criando no Windows, você precisará usar o nome totalmente qualificado `<Microsoft.Build.Tasks.Move>` para evitar ambiguidade com o XamarinVS as tarefas de compilação.

## <a name="for-versions-before-xamarin-studio-6005174--xamarin-for-visual-studio-410530"></a>Para versões anteriores Xamarin Studio 6.0.0.5174 | Xamarin para Visual Studio 4.1.0.530

Sim, você pode usar personalizados destinos do MSBuild para fazer isso. A opção mais fácil é provavelmente copiar o `.ipa` arquivo depois que ele foi criado.

Essas etapas funcionará para qualquer projeto do iOS que usa o mecanismo de compilação do MSBuild no Mac ou Windows. (Observação: todos os projetos de API unificada usam o mecanismo de compilação do MSBuild.)

1. Abra o `.csproj` de arquivos para o projeto de aplicativo do iOS em um editor de texto e, em seguida, adicione as seguintes linhas ao final (imediatamente antes do fechamento `</Project>` marca).

    ```csharp
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

2. Definir o `DestinationFolder` para a pasta de saída desejada. Normalmente, você pode usar propriedades MSBuild (como `$(OutputPath)`) dentro desse argumento se desejar.

## <a name="notes"></a>Observações
- O `CreateIpaDependsOn` propriedade está definida no `Xamarin.iOS.Common.targets` arquivo que é parte do xamarin. Ele se comporta conforme descrito em *substituindo "DependsOn" propriedades* na [https://msdn.microsoft.com/en-us/library/ms366724.aspx](https://msdn.microsoft.com/en-us/library/ms366724.aspx).

- Você pode usar um **mover** tarefas em vez de **cópia** tarefa se você preferir. Se você escolher a opção e você está criando no Windows, você precisará usar o nome totalmente qualificado `<Microsoft.Build.Tasks.Move>` para evitar ambiguidade com o XamarinVS as tarefas de compilação.
