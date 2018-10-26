---
title: Posso alterar o caminho de saída do arquivo IPA?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F5E5DCC6-F7CC-48E2-89E8-709E9C269502
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 1c3c3a63de40a63f040870505b086d67fe160773
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113736"
---
# <a name="can-i-change-the-output-path-of-the-ipa-file"></a>Posso alterar o caminho de saída do arquivo IPA?

## <a name="for-cycle-7-and-higher"></a>Para o ciclo de 7 e superior
Sim, você pode usar destinos personalizados do MSBuild para fazer isso. A opção mais fácil é provavelmente copiar o `.ipa` arquivo após ele ter sido compilado.

Essas etapas funcionará para qualquer projeto do iOS que usa o mecanismo de build do MSBuild no Mac ou Windows. (Observação: todos os projetos de API unificada usam o mecanismo de build do MSBuild.)

1. Abra o `.csproj` do arquivo para o projeto de aplicativo do iOS em um editor de texto e, em seguida, adicione as seguintes linhas ao final (imediatamente antes do fechamento `</Project>` marca):
    
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

2. Defina o DestinationFolder para a pasta de saída desejada. Como de costume, você pode usar propriedades do MSBuild (como $(OutputPath)) dentro desse argumento se desejar.

## <a name="notes"></a>Observações
- O `CreateIpaDependsOn` propriedade está definida no `Xamarin.iOS.Common.targets` arquivo que é parte do xamarin. IOS. Ele se comporta conforme descrito em *substituindo 'DependsOn' propriedades* nos [ https://msdn.microsoft.com/library/ms366724.aspx ](https://msdn.microsoft.com/library/ms366724.aspx).

- Você pode usar um **mover** tarefa em vez de uma **cópia** tarefa se a sua preferência. Se você escolher a opção e você estiver compilando no Windows, você precisará usar o nome totalmente qualificado tarefas `<Microsoft.Build.Tasks.Move>` para evitar ambiguidade com o XamarinVS as tarefas de compilação.

## <a name="for-versions-before-xamarin-studio-6005174--xamarin-for-visual-studio-410530"></a>Para versões anteriores do Xamarin Studio 6.0.0.5174 | Xamarin para Visual Studio 4.1.0.530

Sim, você pode usar destinos personalizados do MSBuild para fazer isso. A opção mais fácil é provavelmente copiar o `.ipa` arquivo após ele ter sido compilado.

Essas etapas funcionará para qualquer projeto do iOS que usa o mecanismo de build do MSBuild no Mac ou Windows. (Observação: todos os projetos de API unificada usam o mecanismo de build do MSBuild.)

1. Abra o `.csproj` do arquivo para o projeto de aplicativo do iOS em um editor de texto e, em seguida, adicione as seguintes linhas ao final (imediatamente antes do fechamento `</Project>` marca).

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

2. Defina o `DestinationFolder` para a pasta de saída desejada. Como de costume, você pode usar as propriedades do MSBuild (como `$(OutputPath)`) dentro desse argumento se desejar.

## <a name="notes"></a>Observações
- O `CreateIpaDependsOn` propriedade está definida no `Xamarin.iOS.Common.targets` arquivo que é parte do xamarin. IOS. Ele se comporta conforme descrito em *propriedades "DependsOn" de substituição* nos [ https://msdn.microsoft.com/library/ms366724.aspx ](https://msdn.microsoft.com/library/ms366724.aspx).

- Você pode usar um **mover** tarefa em vez de uma **cópia** tarefa se a sua preferência. Se você escolher a opção e você estiver compilando no Windows, você precisará usar o nome totalmente qualificado tarefas `<Microsoft.Build.Tasks.Move>` para evitar ambiguidade com o XamarinVS as tarefas de compilação.
