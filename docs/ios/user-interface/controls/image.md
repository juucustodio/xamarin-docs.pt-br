---
title: Exibindo imagens com o xamarin. IOS
description: Este documento descreve como exibir imagens no xamarin. IOS. Ele aborda a adição de imagens a um aplicativo programaticamente ou por meio do Designer do iOS.
ms.prod: xamarin
ms.assetid: 67CA8DB6-769D-42BB-A137-3AF933789FE1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/13/2018
ms.openlocfilehash: 25c5595fac2ea2aa7a87d6640cc6b5c399ab5e5e
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827811"
---
# <a name="displaying-images-with-xamarinios"></a>Exibindo imagens com o xamarin. IOS

Adicionando imagens ao seu aplicativo requer duas etapas: primeiro, adicionar imagens ao projeto. em seguida, adicione controles e código para exibi-los em uma tela. Consulte a [trabalhando com imagens](~/ios/app-fundamentals/images-icons/index.md) do artigo para obter mais cobertura da imagem tratamento no xamarin. IOS.

## <a name="adding-images-to-your-app"></a>Adicionando imagens ao seu aplicativo

Imagens podem ser adicionadas a qualquer pasta no Visual Studio para solução de Mac e se o **ação de compilação** é definido como **conteúdo** em seguida, o arquivo será incluído em seu aplicativo e pode ser exibido.

O Visual Studio para Mac também dá suporte a um diretório especial chamado **recursos** também podem conter arquivos de imagem. Arquivos na pasta de recursos devem ter o **ação de compilação** definido como **BundleResource**.

Esta captura de tela mostra a **ação de compilação** opções que aparecem quando um arquivo é clicado:

 [![](image-images/image30a.png "Menu de ação de compilação")](image-images/image30a.png#lightbox)

O Visual Studio para Mac normalmente deverá escolher o correto **ação de compilação** automaticamente, mas você deve estar atento essas configurações, especialmente se você mover arquivos em seu projeto.

### <a name="adding-an-image-file"></a>Adicionando um arquivo de imagem

Para adicionar um arquivo de imagem ao seu projeto, primeiro clique com botão direito no projeto e escolha **adicionar arquivos...**

 [![](image-images/image31a.png "Adicionar arquivos... menu")](image-images/image31a.png#lightbox)

Selecione a imagem (ou imagens) que deseja incluir na caixa de diálogo de arquivo padrão. Ação de build padrão para imagens serão **BundleResource** – não substituir esse valor, a menos que você tenha um motivo específico.

 [![](image-images/image32a.png "Adicionar caixa de diálogo de arquivos")](image-images/image32a.png#lightbox)

A imagem será adicionada ao seu projeto e está disponível para ser carregado e exibido no código. Esta captura de tela mostra uma imagem adicionada a um projeto de aplicativo do iOS:

 [![](image-images/image33a.png "Imagem no projeto")](image-images/image33a.png#lightbox)

### <a name="what-is-the-resources-directory"></a>O que é o diretório de recursos?

Arquivos colocados na **recursos** diretório são tratados Diferentemente de arquivos regulares – o conteúdo da **recursos** pasta são copiados para a raiz do aplicativo e pode ser referenciado a partir daí em seu código. Isso pode ser útil por vários motivos:

-  Armazenar as imagens definidas nas propriedades do aplicativo, como as imagens de inicialização padrão e ícones do aplicativo.
-  Armazenar outras imagens e arquivos separadamente do código, elas são mais fáceis de gerenciar (subdiretórios são preservados quando o conteúdo do diretório de recursos é copiado).


O **recursos** diretório é especialmente útil em um projeto de biblioteca, uma vez que o código pode assumir que essas imagens serão copiadas para a raiz do aplicativo de consumo, facilitando a bibliotecas de código compartilhado de gravação que exigem imagem, som, vídeo, XML ou outros arquivos.

O **recursos** diretório deve ser assim chamado e todos os arquivos devem ter a ação de build definida como **BundleResource**.

## <a name="displaying-the-image"></a>Exibindo a imagem

No Designer do iOS, use uma **modo de exibição de imagem** para exibir uma imagem ou série animado de imagens. O **modo de exibição de imagem** ícone da caixa de ferramentas é mostrado abaixo:

 [![](image-images/image35a.png "ImageView na caixa de ferramentas")](image-images/image35.png#lightbox)

Arraste o **modo de exibição de imagem** da **caixa de ferramentas** no controlador de exibição. Em seguida, em **modo de exibição de imagem > imagem** a lista suspensa fornecerá uma lista de todos os arquivos de imagem disponível em seu projeto. Selecione qualquer um desses adicioná-lo ao seu modo de exibição de imagem.

 [![](image-images/image36a.png "ImageView na caixa de ferramentas")](image-images/image36.png#lightbox)

### <a name="displaying-the-image-programmatically"></a>Exibindo a imagem de forma programática

Porque **SF Monkey.jpg** está localizado na raiz do **recursos** directory estará disponível em tempo de execução na raiz do pacote do aplicativo. Para exibir essa imagem em um controle de exibição de imagem, use o seguinte código:

```csharp
imageview1.Image = UIImage.FromBundle("SF Monkey.png");
```

Se tivesse colocamos a imagem no **Pics/recursos/SF Monkey.jpg**, em seguida, inclua o código a **Pics** pasta no caminho:

```csharp
imageview1.Image = UIImage.FromBundle("Pics/SF Monkey.png");
```

Arquivo de recurso nunca faz referência é necessário incluir o **recursos** pasta.

## <a name="related-links"></a>Links relacionados

- [Controles (amostra)](https://developer.xamarin.com/samples/monotouch/Controls/)
