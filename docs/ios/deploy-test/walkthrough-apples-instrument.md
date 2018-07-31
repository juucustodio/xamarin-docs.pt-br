---
title: Passo a passo – Usando a Ferramenta de Instrumentos da Apple
description: Este artigo descreve como usar a Ferramenta de Instrumentos da Apple para diagnosticar problemas de memória em um aplicativo iOS criado com o Xamarin. Demonstra como iniciar o Instruments, tirar instantâneos do heap, analisar o aumento da memória e muito mais.
ms.prod: xamarin
ms.assetid: 8f21db1d-7107-4158-8058-d47e417689a0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: a751488b4063a594904393faa605d36c3414d2ec
ms.sourcegitcommit: 021027b78cb2f8061b03a7c6ae59367ded32d587
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39182176"
---
# <a name="walkthrough---using-apples-instruments-tool"></a>Passo a passo – Usando a Ferramenta de Instrumentos da Apple

_Este artigo explica como usar a ferramenta Instrumentos da Apple para diagnosticar problemas de memória em um aplicativo iOS compilado com o Xamarin. Demonstra como inicializar o Instrumentos, tirar instantâneos do heap e analisar o aumento da memória. Ele também mostra como usar o Instrumentos para exibir e identificar as linhas exatas do código que causam o problema de memória._

Esta página demonstra como usar a **ferramenta de instrumentos do Xcode** para diagnosticar um problema de memória em um aplicativo iOS.
Primeiro, baixe o [exemplo do MemoryDemo](https://developer.xamarin.com/samples/monotouch/Profiling/MemoryDemo/) e abra a solução **antes** no Visual Studio para Mac.

## <a name="diagnosing-the-memory-issues"></a>Diagnosticando problemas de memória

1. No Visual Studio para Mac, inicie **Instrumentos** no item de menu **Ferramentas > Iniciar Instrumentos**.
2. Carregue o aplicativo no dispositivo, escolhendo o item de menu **Executar > Carregar no Dispositivo**.
3. Escolha o modelo **Alocações** (ícone laranja com caixa branca)

    ![](walkthrough-apples-instrument-images/00-allocations-tempate.png "Escolha o modelo de alocações")

4. Selecione o aplicativo **Demonstração de Memória** na lista **Escolher um modelo de criação de perfil para:** na parte superior da janela. Clique no dispositivo iOS para expandir o menu que mostra os aplicativos instalados.

    ![](walkthrough-apples-instrument-images/01-mem-demo.png "Selecione o aplicativo Memory Demo")

5. Pressione o botão **Escolher** (canto inferior direito da janela) para iniciar **Instrumentos**. Este modelo mostrará dois itens no painel superior: Alocações e Controlador de VM.

6. Pressione o botão **Gravar** (círculo vermelho no canto superior esquerdo) em Instrumentos, o que iniciará o aplicativo.

7. Selecione a linha **Controlador de VM** no painel superior (agora que o aplicativo é executado, ele conterá duas seções: Tamanho Residente e Sujo). No painel **Inspetor**, escolha a opção **Mostrar Configurações de Exibição** (o ícone de engrenagem) e marque a caixa de seleção **Instantâneo Automático**, conforme mostrado no canto inferior direito desta captura de tela:

    ![](walkthrough-apples-instrument-images/02-auto-snapshot.png "Escolha a opção Mostrar Configurações de Exibição no ícone de engrenagem e, em seguida, marque a caixa de seleção de instantâneo automático")

8. Selecione a linha **Alocações** no painel superior (agora que o aplicativo é executado, ele indicará *Todas as VMs Anônimas e Heap*)
9. No painel **Inspetor**, escolha a opção **Mostrar Configurações de Vídeo** (o ícone de engrenagem) e clique no botão **Marcar Geração** para estabelecer uma linha de base. Um pequeno sinalizador vermelho será exibido na linha do tempo na parte superior da janela
10. Role o aplicativo e selecione **Marcar Geração** novamente (repetir algumas vezes)
11. Clique no botão **Parar**.
12. Expanda o nó **Geração** com o maior **Crescimento** e classifique por **Crescimento** (decrescente).
13. Altere o painel **Inspetor** para **Mostrar Detalhes Estendidos** (o "E"), que mostra o **Rastreamento de Pilha**.

14. Observe que o nó **&lt;non-object>** mostra um crescimento excessivo de memória. Clique na seta ao lado desse nó para ver mais detalhes – clique com o botão direito do mouse no rastreamento de pilha para adicionar **Local de Origem** ao painel:

    ![](walkthrough-apples-instrument-images/03-mem-growth.png "Adicione o local de origem ao painel")

15. Classifique por **Tamanho** e exiba **Detalhes Estendidos**:

    ![](walkthrough-apples-instrument-images/04-extended-detail.png "Classifique por Tamanho e exiba Detalhes Estendidos")

16. Clique na entrada desejada na pilha de chamadas para ver o código relacionado:

    ![](walkthrough-apples-instrument-images/05-related-code.png "Exibir o código relacionado")

Nesse caso, uma nova imagem é criada e armazenada em uma coleção para cada célula, nem as células de exibição de coleção existente são reutilizadas.

## <a name="resolving-the-memory-issues"></a>Resolvendo problemas de memória

É possível resolver esses problemas e executar novamente o aplicativo por meio de Instrumentos.

Ao declarar uma única instância no nível de classe, a imagem pode ser reutilizada e o objeto de célula pode ser reutilizado por meio de um pool existente em vez de ser criado toda vez, conforme mostrado abaixo:

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
{
    // Dequeue a cell from the reuse pool
    var imageCell = (ImageCell)collectionView.DequeueReusableCell (cellId, indexPath);

    // Reuse the image declared at the class level
    imageCell.ImageView.Image = image;

    return imageCell;
}
```

Agora, quando o aplicativo é executado, o uso de memória é bastante reduzido – o **Crescimento** entre gerações agora é medido em Kib (quilobytes) em vez de MiB (megabytes), como era antes de corrigir o código:

![](walkthrough-apples-instrument-images/06-reduced-memory.png "Mostrar o uso de memória do aplicativo")

O código aprimorado está disponível no [Exemplo de MemoryDemo](https://developer.xamarin.com/samples/monotouch/Profiling/MemoryDemo/) na solução **posterior** no Visual Studio para Mac.

Este blog da comunidade sobre [Coleta de Lixo do Xamarin.iOS](http://c-sharx.net/2015-04-27-xamarin-ios-the-garbage-collector-and-me/) é uma referência útil para lidar com problemas de memória com o Xamarin.iOS.

## <a name="summary"></a>Resumo

Este artigo demonstrou como usar Instrumentos para diagnosticar problemas de memória.
Ele descreveu como iniciar Instrumentos pelo Visual Studio para Mac, carregar o modelo de alocação de memória e passar a usar instantâneos para identificar problemas de memória.
Por fim, o aplicativo foi examinado novamente para verificar se que o problema foi corrigido.

## <a name="related-links"></a>Links relacionados

- [Amostra do Memory Demo](https://developer.xamarin.com/samples/monotouch/Profiling/MemoryDemo/)
- [Coleta de lixo do Xamarin.iOS (publicação do blog)](http://c-sharx.net/2015-04-27-xamarin-ios-the-garbage-collector-and-me/)
