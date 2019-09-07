---
title: 'Erro de IBTool: Não foi possível concluir a operação.'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A804EBC4-2BBF-4A98-A4E8-A455DB2E8A17
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: 39b522af5bdc3587e3d5aa1451ed4879c6af65f5
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769331"
---
# <a name="ibtool-error-the-operation-couldnt-be-completed"></a>Erro de IBTool: Não foi possível concluir a operação.

## <a name="fixed-in-xcode-611"></a>Corrigido no Xcode 6.1.1

A Apple [corrigiu](https://developer.apple.com/library/content/documentation/Xcode/Conceptual/RN-Xcode-Archive/Chapters/xc6_release_notes.html#//apple_ref/doc/uid/TP40016994-CH4-SW1) esse bug `ibtool` no Xcode 6.1.1, portanto, atualizar para o Xcode 6.1.1 ou superior é a correção mais fácil.

* * *

## <a name="description-of-the-problem"></a>Descrição do problema

O `ibtool` comando no Xcode 6,0 tinha um bug no os X 10,10 Yosemite. O Xamarin. IOS usa o `ibtool` Xcode para compilar storyboards `XIB` e arquivos.

Mais informações sobre o bug em relação ao Xcode podem ser encontradas no seguinte Stack Overflow post:[https://stackoverflow.com/questions/25754763/cant-open-storyboard](https://stackoverflow.com/questions/25754763/cant-open-storyboard)

### <a name="error-message"></a>Mensagem de erro

> Não foi possível abrir o documento "MainStoryboard. Storyboard". Não foi possível concluir a operação. (erro com. Apple. InterfaceBuilder-1.)

## <a name="workarounds-for-xcode-60"></a>Soluções alternativas (para o Xcode 6,0)

### <a name="option-1-manage-all-uiimageviewimage-properties-in-code"></a>Opção 1: Gerenciar todas `UIImageView.Image` as propriedades no código

Em vez de definir `Image` a propriedade `UIImageView` de a no storyboard ou `.xib` no arquivo, você pode definir a propriedade em um dos métodos de substituição do ciclo de vida da exibição no controlador de exibição ( `ViewDidLoad()`por exemplo, em). Consulte também [trabalhando com imagens](~/ios/app-fundamentals/images-icons/index.md) para obter dicas sobre `UIImage.FromBundle()` como usar `UIImage.FromFile()`o vs.

### <a name="option-2-move-all-of-the-image-resources-to-the-top-level-resources-folder"></a>Opção 2: Mover todos os recursos de imagem para a pasta de nível `Resources` superior

Depois de mover as imagens para a pasta de `Resources` nível superior, será necessário atualizar o storyboard e `.xib` os arquivos para usar os novos caminhos de imagem.

### <a name="option-3-set-the-logicalname-for-any-problematic-image-assets-so-they-are-copied-to-the-top-level-of-theapp-bundle"></a>Opção 3: Defina o `LogicalName` para quaisquer ativos de imagem problemática para que eles sejam copiados para o nível`.app` superior do pacote

Por exemplo, digamos que o `.csproj` arquivo original contém a seguinte entrada:

`<BundleResource Include="Resources\Images\image.png" />`

Você pode alterar esse elemento e adicionar um `LogicalName` para que a imagem seja copiada para o nível superior `.app` do pacote:

```xml
<BundleResource Include="Resources\Images\image.png">
    <LogicalName>image.png</LogicalName>
</BundleResource>
```

Em Visual Studio para Mac o `LogicalName` também pode ser definido usando o `Resource ID` campo para a imagem em **Exibir > pads > Propriedades**. (Consulte também: [https://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545](https://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545))

Após essa alteração, será necessário atualizar o storyboard e `.xib` os arquivos para usar os novos caminhos de imagem de nível superior. Visual Studio para Mac atualizará automaticamente a lista de preenchimentos automáticos para `Image` a propriedade no designer do Ios. No Visual Studio, você precisará editar o caminho manualmente. O designer do iOS exibirá isso como uma imagem ausente, mas o projeto será compilado e executado corretamente.

### <a name="next-steps"></a>Próximas etapas

Para obter mais assistência, entrar em contato conosco ou, se esse problema permanecer mesmo depois de utilizar as informações acima, consulte [quais opções de suporte estão disponíveis para o Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obter informações sobre opções de contato, sugestões, bem como como arquivar um novo bug, se necessário . 
