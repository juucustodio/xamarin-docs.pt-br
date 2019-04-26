---
title: Como copiar arquivos de saída de IPA para a pasta-depósito TFS?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B0F1E09E-7315-45BA-B7FF-44D2063EE19C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 74e2f2219dcb0908edce7f109844932639038b25
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61421117"
---
# <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folder"></a>Como copiar arquivos de saída de IPA para a pasta-depósito TFS?

Abra o `.csproj` do arquivo para o projeto de aplicativo do iOS em um editor de texto e, em seguida, adicione as seguintes linhas ao final (imediatamente antes do fechamento `</Project>` marca):

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

- Isso é a mesma técnica geral discutida sobre [posso alterar o caminho de saída do arquivo IPA?](~/ios/troubleshooting/questions/ipa-output-path.md). Os dois pontos importantes são definir `$(TF_BUILD_BINARIESDIRECTORY)` como a pasta de destino e para adicionar uma condição extra tão `CopyIpa` será executado apenas para as compilações do TFS.

- Para obter uma descrição `TF_BUILD_BINARIESDIRECTORY` ver [variáveis de compilação predefinida](https://docs.microsoft.com/azure/devops/pipelines/build/variables).

## <a name="additional-references"></a>Referências adicionais

- [Documentação sobre como instalar o TFS para uso com o Xamarin](https://docs.microsoft.com/azure/devops/repos/tfvc/overview)
- [Tarefa de compilação de DevOps do Azure: Xamarin.Android](https://docs.microsoft.com/azure/devops/pipelines/tasks/build/xamarin-android)
- [Tarefa de compilação de DevOps do Azure: Xamarin.iOS](https://docs.microsoft.com/azure/devops/pipelines/tasks/build/xamarin-ios)

### <a name="next-steps"></a>Próximas etapas

Este documento aborda o comportamento atual a partir de 3.11.666 do Xamarin para Visual Studio e o host de build do xamarin. IOS 8.10.3 no Mac. Para obter mais assistência, entre em contato conosco ou se esse problema permanece mesmo após utilizando as informações acima, consulte [quais opções de suporte estão disponíveis para o Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obter informações sobre opções de contato, sugestões, e também como um novo bug de arquivo se necessário.
