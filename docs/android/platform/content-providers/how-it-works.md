---
title: Como os provedores de conteúdo funcionam
ms.prod: xamarin
ms.assetid: B9E2EF89-7EBE-45F5-1ED9-7D2C70BE792C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: e61be6f0189eb825c15fd75764a16706e588ebc9
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "73020512"
---
# <a name="how-content-providers-work"></a>Como os provedores de conteúdo funcionam

Há duas classes envolvidas em uma interação `ContentProvider`:

- O **contentprovider** &ndash; implementa uma API que expõe um conjunto de dados de forma padrão. Os principais métodos são consulta, inserção, atualização e exclusão.

- **Contentresolver retornem** &ndash; um proxy estático que se comunica com um `ContentProvider` para acessar seus dados, seja de dentro do mesmo aplicativo ou de outro aplicativo.

Um provedor de conteúdo é normalmente apoiado por um banco de dados SQLite, mas a API significa que o consumo de código não precisa saber nada sobre o SQL subjacente. As consultas são feitas por meio de um URI usando constantes para referenciar nomes de coluna (para reduzir as dependências na estrutura de dados subjacente) e um `ICursor` é retornado para o código de consumo iterar.

## <a name="consuming-a-contentprovider"></a>Consumindo um ContentProvider

`ContentProviders` expor sua funcionalidade por meio de um URI que é registrado no **AndroidManifest. xml** do aplicativo que publica os dados. Há uma convenção em que o URI e as colunas de dados que são expostas devem estar disponíveis como constantes para facilitar a ligação aos dados. Todos os `ContentProviders` internos do Android fornecem classes de conveniência com constantes que fazem referência à estrutura de dados no namespace [`Android.Providers`](xref:Android.Provider) .

### <a name="built-in-providers"></a>Provedores internos

O Android oferece acesso a uma ampla variedade de dados do sistema e do usuário usando `ContentProviders`:

- O *navegador* &ndash; indicadores e histórico de navegador (requer permissão `READ_HISTORY_BOOKMARKS` e/ou `WRITE_HISTORY_BOOKMARKS`).

- *CallLog* &ndash; chamadas recentes feitas ou recebidas com o dispositivo.

- Os *contatos* &ndash; informações detalhadas da lista de contatos do usuário, incluindo pessoas, telefones, fotos & grupos.

- *MediaStore* &ndash; conteúdo do dispositivo do usuário: áudio (álbuns, artistas, gêneros, listas de reprodução), imagens (incluindo miniaturas) & vídeo.

- *Configurações* &ndash; configurações e preferências do dispositivo de todo o sistema.

- *Userdictionary* &ndash; conteúdo do dicionário definido pelo usuário usado para entrada de texto preditiva.

- A *caixa postal* &ndash; histórico de mensagens de correio de voz.

## <a name="classes-overview"></a>Visão geral de classes

As classes primárias usadas ao trabalhar com um `ContentProvider` são mostradas aqui:

[diagrama de classe de ![do aplicativo provedor de conteúdo e consumindo interações de aplicativo](how-it-works-images/classdiagram1.png)](how-it-works-images/classdiagram1.png#lightbox)

Neste diagrama, o `ContentProvider` implementa consultas e registra os URIS que outros aplicativos usam para localizar dados. O `ContentResolver` atua como um ' proxy ' para os `ContentProvider` (métodos de consulta, inserção, atualização e exclusão). O `SQLiteOpenHelper` contém dados usados pelo `ContentProvider`, mas não é exposto diretamente ao consumo de aplicativos.
O `CursorAdapter` passa o cursor retornado pela `ContentResolver` para exibir em uma `ListView`. O `UriMatcher` é uma classe auxiliar que analisa URIs ao processar consultas.

A finalidade de cada classe é descrita abaixo:

- O **contentprovider** &ndash; implementar os métodos dessa classe abstrata para expor dados. A API é disponibilizada para outras classes e aplicativos por meio do atributo URI que é adicionado à definição de classe.

- **SQLiteOpenHelper** &ndash; ajuda a implementar o repositório de armazenamento do SQLite que é exposto pelo `ContentProvider`.

- **UriMatcher** &ndash; usar `UriMatcher` em sua implementação de `ContentProvider` para ajudar a gerenciar URIs que são usados para consultar o conteúdo.

- **Contentresolver retornem** &ndash; código de consumo usa um `ContentResolver` para acessar uma instância de `ContentProvider`. As duas classes juntos cuidam dos problemas de comunicação entre processos, permitindo que os dados sejam facilmente compartilhados entre os aplicativos. O consumo de código nunca cria uma classe `ContentProvider` explícita; em vez disso, os dados são acessados criando um cursor com base em um URI exposto pelo aplicativo `ContentProvider`.

- **CursorAdapter** &ndash; usar `CursorAdapter` ou `SimpleCursorAdapter` para exibir dados acessados por meio de um `ContentProvider`.

A API `ContentProvider` permite que os consumidores realizem uma variedade de operações nos dados, como:

- Consultar dados para retornar listas ou registros individuais.
- Modificar registros individuais.
- Adicionar novos registros.
- Excluir registros.

Este documento contém um exemplo que usa um `ContentProvider`fornecido pelo sistema, bem como um exemplo simples de somente leitura que implementa uma `ContentProvider`personalizada.
