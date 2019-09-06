---
title: Como copiar arquivos de saída do IPA para a pasta de destino do TFS?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B0F1E09E-7315-45BA-B7FF-44D2063EE19C
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: 9c7b7e598f66d930b5e16ef19b8bc108d8b6701a
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291052"
---
# <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folder"></a>Como copiar arquivos de saída do IPA para a pasta de destino do TFS?

Abra o `.csproj` arquivo para o projeto de aplicativo Ios em um editor de texto e, em seguida, adicione as seguintes linhas no final ( `</Project>` imediatamente antes da marca de fechamento):

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
        And '$(BuildIpa)' == 'true'
        And '$(TF_BUILD)' == 'true'">
    <Copy
        SourceFiles="$(OutputPath)$(IpaPackageName)"
        DestinationFolder="$(TF_BUILD_BINARIESDIRECTORY)"
    />
</Target>
```

## <a name="notes"></a>Observações

- Essa é a mesma técnica geral discutida em [posso alterar o caminho de saída do arquivo IPA?](~/ios/troubleshooting/questions/ipa-output-path.md). Os dois pontos importantes são definir `$(TF_BUILD_BINARIESDIRECTORY)` como a pasta de destino e adicionar uma condição extra para que `CopyIpa` sejam executados somente para compilações do TFS.

- Para obter uma descrição `TF_BUILD_BINARIESDIRECTORY` de como obter [variáveis de compilação predefinidas](https://docs.microsoft.com/azure/devops/pipelines/build/variables).

## <a name="additional-references"></a>Referências adicionais

- [Documentação sobre como instalar o TFS para uso com o Xamarin](https://docs.microsoft.com/azure/devops/repos/tfvc/overview)
- [Tarefa de compilação do Azure DevOps: Xamarin.Android](https://docs.microsoft.com/azure/devops/pipelines/tasks/build/xamarin-android)
- [Tarefa de compilação do Azure DevOps: Xamarin.iOS](https://docs.microsoft.com/azure/devops/pipelines/tasks/build/xamarin-ios)

### <a name="next-steps"></a>Próximas etapas

Este documento discute o comportamento atual a partir do Xamarin 3.11.666 para Visual Studio e Xamarin. iOS 8.10.3 no host de Build do Mac. Para obter mais assistência, entrar em contato conosco ou, se esse problema permanecer mesmo depois de utilizar as informações acima, consulte [quais opções de suporte estão disponíveis para o Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obter informações sobre opções de contato, sugestões, bem como como arquivar um novo bug, se necessário .
