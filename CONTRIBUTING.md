---
ms.openlocfilehash: 0071fcc72844e4816e707c5828464859e09db68c
ms.sourcegitcommit: 2503da076966d12a8bdbf7af209662b74913a15c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78261612"
---
# <a name="contributing"></a>Participante

Obrigado por seu interesse em contribuir para a documentação do Xamarin!

Esta página aborda o processo básico para atualizar o conteúdo da [documentação do Xamarin](https://docs.microsoft.com/xamarin).

- [Contrato de Licença do Colaborador](LICENSE)

## <a name="process-for-contributing"></a>Processo de contribuição

### <a name="small-changes--edits"></a>Pequenas alterações e edições

Para fazer correções e atualizações pequenas você pode clicar no botão **Editar** em qualquer página e usar o site do GitHub para contribuir ou seguir estas etapas:

1. Crie fork do repositório `MicrosoftDocs/xamarin-docs`.

2. Crie um `branch` para suas alterações.

3. Escreva seu conteúdo. Veja o [modelo](contributing-guidelines/template.md) e o [guia de estilo](contributing-guidelines/voice-tone.md).

4. Envie uma PR (solicitação de pull) de seu branch `MicrosoftDocs/xamarin-docs/live`.

5. Faça as atualizações necessárias em seu branch, conforme discutido com a equipe por meio da PR.

6. Os mantenedores mesclarão sua PR depois que os comentários forem aplicados e sua alteração estiver boa. Ela será exibida no site docs.microsoft.com logo depois.

> [!NOTE]
> Se a PR estiver resolvendo um problema existente, adicione a palavra-chave `Fixes #Issue_Number` à mensagem de confirmação ou à descrição da PR para que o problema possa ser encerrado automaticamente quando a PR for mesclada. Para obter mais informações, confira [Closing issues via commit messages](https://help.github.com/articles/closing-issues-via-commit-messages/) (Encerrando problemas por meio de mensagens de confirmação).

### <a name="big-changes-or-new-content"></a>Grandes alterações ou novo conteúdo

Para novo conteúdo e grandes contribuições [abra um problema](https://github.com/MicrosoftDocs/xamarin-docs/issues) descrevendo o artigo que você deseja escrever e como ele se relaciona com o conteúdo existente. O conteúdo dentro da pasta do docs é organizado em seções que são organizadas por área de produto (por exemplo, Android e iOS). Tente determinar a pasta correta para seu novo conteúdo. 

**Obtenha comentários sobre sua proposta por meio do problema antes de começar a escrever.**

No caso de um novo tópico, você poderá usar o [arquivo de modelo](../contributing-guidelines/template.md) como ponto de partida. Ele contém as diretrizes de escrita e também explica os metadados necessários para cada artigo, como informações do autor.

Para imagens e outros recursos estáticos, adicione-os à subpasta chamada **\<mypage>-images**. Se você estiver criando uma pasta para o conteúdo, adicione uma pasta de imagens à nova pasta.

#### <a name="example-structure"></a>Exemplo de estrutura

```
docs
    /android
        mypage.md
        /mypage-images
            some-image.png
```

Siga a sintaxe de Markdown apropriada. Confira o [guia de estilo](../contributing-guidelines/template.md) para obter mais informações.

As etapas de envio reais serão as mesmas que as das pequenas alterações ([acima](#process-for-contributing)).

A equipe do Xamarin examinará a PR e informará (por meio de comentários na PR) se a alteração está boa ou se é necessário fazer outras atualizações/alterações para que ela seja aprovada.

Em seguida, depois que os comentários forem aplicados e sua alteração estiver boa, os mantenedores mesclarão a PR.

Em um determinado ritmo, efetuaremos push de todas as confirmações do branch mestre para o site ativo e, em seguida, você poderá ver sua colaboração em https://docs.microsoft.com/xamarin/.

### <a name="contributing-to-international-content"></a>Contribuindo para conteúdo internacional

Atualmente, não são aceitas contribuições para conteúdo de MT (convertidas no computador) por enquanto. Em um esforço para melhorar a qualidade do conteúdo de MT, fizemos a transição para um mecanismo de MT neural. Aceitamos e incentivamos contribuições para conteúdo de tecnologia de tradução humana (HT), que é usado para treinar o mecanismo do neural. Assim, ao longo do tempo, as contribuições para o conteúdo da HT melhorarão a qualidade do HT e do MT. Os tópicos de MT terão uma isenção informando que parte do tópico pode ser MT e o botão **Editar** não será exibido quando for desabilitado.

## <a name="dos-and-donts"></a>O que FAZER e o que NÃO FAZER

Abaixo, há uma lista curta de regras de orientação que você deve considerar ao contribuir para a documentação do .NET.

- **NÃO FAZER:** não nos surpreenda com solicitações de pull grandes. Em vez disso, registre um problema e inicie uma discussão para que possamos concordar sobre o que fazer antes de você gastar muito tempo nisso.
- **FAZER:** leia o [guia de estilo](contributing-guidelines/template.md) e as diretrizes sobre [voz e tom](contributing-guidelines/voice-tone.md).
- **FAZER:** use o arquivo de [modelo](contributing-guidelines/template.md) como o ponto de partida do seu trabalho.
- **FAZER:** crie um branch separado em seu fork antes de trabalhar nos artigos.
- **FAZER:** siga o [fluxo de trabalho do GitHub Flow](https://guides.github.com/introduction/flow/).
- **FAZER:** faça postagem em blogs e no Twitter (ou em outros) sobre suas contribuições, com frequência.

> [!NOTE]
> Você poderá observar que, no momento, alguns dos tópicos não estão seguindo todas as diretrizes especificadas aqui e também no [guia de estilo](contributing-guidelines/template.md). Estamos trabalhando para atingir a consistência em todo o site. 
