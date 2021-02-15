---
title: Exibindo imagens com Xamarin. iOS
description: Este documento descreve como exibir imagens no Xamarin. iOS. Ele aborda a adição de imagens a um aplicativo de forma programática ou por meio do designer do iOS.
ms.prod: xamarin
ms.assetid: 67CA8DB6-769D-42BB-A137-3AF933789FE1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/13/2018
ms.openlocfilehash: 2350c068b0a719e8d633c0f96712aec40e7865aa
ms.sourcegitcommit: 513feb0e07558766e3de4a898e53d56b27c20559
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98697469"
---
# <a name="displaying-images-with-xamarinios"></a>Exibindo imagens com Xamarin. iOS

A adição de imagens ao seu aplicativo requer duas etapas: primeiro, adicione as imagens ao seu projeto; em seguida, adicione controles e código para exibi-los em uma tela. Consulte o artigo [trabalhando com imagens](~/ios/app-fundamentals/images-icons/index.md) para obter uma cobertura mais detalhada da manipulação de imagens no Xamarin. Ios.

## <a name="adding-images-to-your-app"></a>Adicionando imagens ao seu aplicativo

As imagens podem ser adicionadas a qualquer pasta em sua solução de Visual Studio para Mac e, se a **ação de compilação** estiver definida como **conteúdo** , o arquivo será incluído com seu aplicativo e poderá ser exibido.

O Visual Studio para Mac também dá suporte a um diretório especial chamado **recursos** que também podem conter arquivos de imagem. Os arquivos na pasta recursos devem ter a **ação de compilação** definida como **BundleResource**.

Esta captura de tela mostra as opções de **ação de compilação** que aparecem quando um arquivo é clicado com o botão direito do mouse:

 [![Menu de ação de Build](image-images/image30a.png)](image-images/image30a.png#lightbox)

O Visual Studio para Mac normalmente escolherá a **ação de Build** correta automaticamente, mas você deve estar ciente dessas configurações, especialmente se você mover arquivos em seu projeto.

### <a name="adding-an-image-file"></a>Adicionando um arquivo de imagem

Para adicionar um arquivo de imagem ao seu projeto, primeiro clique com o botão direito do mouse no projeto e escolha **Adicionar arquivos...**

 [![Adicionar arquivos... AdicionarMenu](image-images/image31a.png)](image-images/image31a.png#lightbox)

Selecione a imagem (ou imagens) que você deseja incluir na caixa de diálogo arquivo padrão. A ação de compilação padrão para imagens será **BundleResource** – não substitua esse valor, a menos que você tenha um motivo específico.

 [![Caixa de diálogo Adicionar arquivos](image-images/image32a.png)](image-images/image32a.png#lightbox)

A imagem será adicionada ao seu projeto e estará disponível para ser carregada e exibida no código. Esta captura de tela mostra uma imagem adicionada a um projeto de aplicativo iOS:

 [![Imagem no projeto](image-images/image33a.png)](image-images/image33a.png#lightbox)

### <a name="what-is-the-resources-directory"></a>O que é o diretório de recursos?

Os arquivos colocados no diretório de **recursos** são tratados diferentemente de arquivos regulares – o conteúdo da pasta **recursos** é copiado para a raiz do aplicativo e pode ser referenciado a partir daí no seu código. Isso pode ser útil por vários motivos:

- Armazenar as imagens configuradas nas propriedades do aplicativo, como as imagens de inicialização padrão e os ícones de aplicativo.
- Armazenar outras imagens e arquivos separadamente do código para que eles sejam mais fáceis de gerenciar (os subdiretórios são preservados quando o conteúdo do diretório de recursos é copiado).

O diretório de **recursos** é especialmente útil em um projeto de biblioteca, pois o código pode assumir que essas imagens serão copiadas para a raiz do aplicativo de consumo, facilitando a gravação de bibliotecas de código compartilhado que exigem imagem, som, vídeo, XML ou outros arquivos.

O diretório de **recursos** deve ser tão nomeado, e todos os arquivos devem ter a ação de compilação definida como **BundleResource**.

## <a name="displaying-the-image"></a>Exibindo a imagem

No designer do iOS, use uma **exibição de imagem** para exibir uma imagem ou uma série animada de imagens. O ícone de **exibição de imagem** da caixa de ferramentas é mostrado abaixo:

 [![Ícone de ImageView na caixa de ferramentas.](image-images/image35a.png)](image-images/image35.png#lightbox)

Arraste o **modo de exibição de imagem** da **caixa de ferramentas** para o controlador de exibição. Em seguida, em **modo de exibição de imagem > imagem** , a lista suspensa fornecerá uma lista de todos os arquivos de imagem disponíveis em seu projeto. Selecione qualquer um deles para adicioná-lo à sua exibição de imagem.

 [![ImageView na caixa de ferramentas](image-images/image36a.png)](image-images/image36.png#lightbox)

### <a name="displaying-the-image-programmatically"></a>Exibindo a imagem programaticamente

Como o **it Monkey.jpg** está localizado na raiz do diretório de **recursos** , ele estará disponível em tempo de execução na raiz do pacote de aplicativos. Para exibir essa imagem em um controle de exibição de imagem, use o seguinte código:

```csharp
imageview1.Image = UIImage.FromBundle("SF Monkey.png");
```

Se tivéssemos colocado a imagem em **/Resources/Pics/SF Monkey.jpg**, o código incluiria a pasta **pics** no caminho:

```csharp
imageview1.Image = UIImage.FromBundle("Pics/SF Monkey.png");
```

As referências de arquivo de recurso nunca precisam incluir a pasta de **recursos** .

## <a name="related-links"></a>Links relacionados

- [Controles (exemplo)](/samples/xamarin/ios-samples/controls)