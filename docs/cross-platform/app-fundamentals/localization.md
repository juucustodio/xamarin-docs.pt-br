---
title: Localização da interface do usuário do aplicativo
description: Este documento descreve os conceitos de plataforma cruzada de internacionalização e localização e examina como eles afetam o design do aplicativo.
ms.prod: xamarin
ms.assetid: CC6847B2-23FB-4EDE-9F7E-EF29DD46A5C5
author: conceptdev
ms.author: crdun
ms.date: 03/22/2017
ms.openlocfilehash: 9e0a6569710ca3ad10f57d7145a97af92574427c
ms.sourcegitcommit: 76f930ce63b193ca3f7f85f768b031e59cb342ec
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71198445"
---
# <a name="localization"></a>Localização

Este guia apresenta os conceitos por trás da *internacionalização* e *localização* e links para instruções sobre como produzir aplicativos móveis do Xamarin usando esses conceitos.

Se você quiser pular diretamente para os detalhes técnicos da localização de aplicativos do Xamarin, comece com um destes artigos de instruções específicos da plataforma:

- Localização entre plataformas do [**Xamarin. Forms**](~/xamarin-forms/app-fundamentals/localization/index.md) usando arquivos resx.
- Localização da plataforma nativa do [**Xamarin. Ios**](~/ios/app-fundamentals/localization/index.md) .
- Localização da plataforma nativa do [**Xamarin. Android**](~/android/app-fundamentals/localization.md) .

## <a name="i18n-and-l10n"></a>i18n e l10n

A *internacionalização* é o processo de tornar seu código capaz de exibir diferentes idiomas e adaptar sua exibição para localidades diferentes (como formatação de número e de data). Isso também é conhecido como *globalização*.

A *localização* é a etapa a seguir – criando recursos (como cadeias de caracteres e imagens) para cada idioma e agrupando-os com o aplicativo internacionalizar.

A internacionalização geralmente é reduzida para i18n – abreviação de 18 letras entre "i" e "n". A localização é, de forma semelhante, reduzida para l10n – por 10 letras entre "L" e "n".

## <a name="overview"></a>Visão geral

Este documento apresenta os conceitos associados à internacionalização e à localização e como eles se aplicam ao desenvolvimento de aplicativos móveis em geral.
Ao projetar e criar um aplicativo, as coisas que você pode ter inserido anteriormente, mas que devem ser parametrizadas para localização, incluem:

- Texto e layouts de tela,
- Ícones, gráficos e cores,
- Arquivos de vídeo e som,
- Texto dinâmico e formatação de texto (como números, moeda e datas),
- Alterações de layout para idiomas RTL (da direita para a esquerda) e
- Classificação de dados.

Independentemente de quais plataformas móveis seu aplicativo se destina a essas dicas ajudará você a criar um aplicativo localizado de alta qualidade.

## <a name="design-considerations"></a>Considerações de design

Arquitetar um aplicativo para que seja possível localizar seu conteúdo é chamado de internacionalização. Fazer a internacionalização corretamente é mais do que apenas permitir que diferentes cadeias de caracteres de idioma sejam carregadas em tempo de execução – um aplicativo bem projetado deve permitir que todos os recursos sejam alterados com base no idioma e na localidade (incluindo imagens, sons e vídeos) e possam adaptar-se formatação e layout para lidar com diferentes cadeias de caracteres de tamanho.

Esta seção aborda algumas considerações de design a serem levadas em conta ao criar um aplicativo internacionalizado.

### <a name="layouts-and-string-length"></a>Layouts e comprimento da cadeia de caracteres

As cadeias de caracteres chinês e japonês podem ser muito curtas – às vezes, um ou dois personagens podem ser significativos o suficiente para um rótulo de campo de entrada.

As cadeias de caracteres do alemão (por exemplo) podem ser muito longas; às vezes, uma palavra relativamente curta em inglês se torna muito longa em outras linguagens, ficando recortada ou refluindo de forma inesperada o layout.

Compare os comprimentos de cadeia de caracteres para alguns itens na tela inicial do iOS em inglês, alemão e japonês:

[![](localization-images/language-compare-sml.png "Tamanho da cadeia de caracteres alemão versus japonês")](localization-images/language-compare.png#lightbox)

Observe que **as configurações** em inglês (8 caracteres) exigem 13 caracteres para a tradução do alemão, mas apenas 2 caracteres em Japonês.

Os layouts onde o rótulo de exibição e o campo de entrada são lado a lado são difíceis de trabalhar com quando o comprimento do rótulo pode variar muito. Geralmente, um layout em que o rótulo é exibido acima de um campo é mais fácil de localizar porque a largura total da tela está disponível tanto para o rótulo quanto para a entrada.

Como regra geral, se você estiver criando layouts fixos (especialmente elementos lado a lado), permita pelo menos 50% mais largura do que suas cadeias de caracteres em inglês exigem para rótulos e texto. Isso não resolverá todos os problemas, mas fornecerá um buffer que funcionará em muitos casos.

### <a name="input-validation"></a>Validação de entrada

Cuidado com as suposições ao escrever regras de validação. Pode parecer válido exigir uma entrada de campo de texto para "exigir" pelo menos três caracteres em inglês, uma vez que uma única letra raramente tem qualquer significado. Em chinês e japonês, no entanto, um único caractere pode ser uma entrada válida e uma mensagem de validação "pelo menos 3 caracteres é necessária" não faz sentido para esses idiomas.

Outras tarefas aparentemente simples, como validar um endereço de email ou URL do site, se tornam mais complicadas com os caracteres não são limitados ao subconjunto ASCII.

Escreva suas regras de validação com internacionalização em mente – escolha as regras menos restritivas ou grave a lógica para que ela funcione de forma diferente para cada idioma.

### <a name="images-and-color"></a>Imagens e cores

Nem toda imagem precisa ser alterada com base na escolha de idioma de um usuário. Muitos ícones ou fotos serão adequados para todos os usuários, não importa qual linguagem eles falam.
No entanto, alguns recursos fazem sentido localizar, como:

- Imagens que descrevem pessoas ou locais específicos – seu aplicativo pode se sentir mais relevante para os usuários se ele mostrar pessoas/locais locais.
- Ícones – alguns iconografia podem ser específicos de cultura e você pode facilitar o uso do seu aplicativo localizando as imagens para refletir a compreensão local.
- Cores – algumas culturas entendem cores de forma diferente – vermelho pode significar um aviso em uma região, mas boa sorte em outra. Verifique com alto-falantes nativos ao projetar seu aplicativo para determinar se você deve criar um mecanismo para localizar cores.

### <a name="videos-and-sound"></a>Vídeos e som

Vídeos e sons apresentam desafios especiais ao localizar um aplicativo, porque embora seja relativamente fácil obter cadeias de caracteres convertidas, gravar várias faixas de voz ou clipes de vídeo pode ser caro e difícil.

Várias cópias de arquivos de vídeo e de som também podem aumentar significativamente o tamanho do seu aplicativo (especialmente se você estiver localizando em um grande número de idiomas ou tiver muitos arquivos de mídia). Você pode considerar baixar apenas os ativos de idioma necessários depois que o usuário tiver instalado seu aplicativo, mas isso também pode resultar em uma experiência de usuário ruim em redes lentas.

Muitas vezes, há várias maneiras de resolver problemas de localização – o mais importante é considerá-los antecipadamente e garantir que seu aplicativo tenha sido projetado para cuidar deles.

### <a name="dates-times-numbers-and-currency"></a>Datas, horas, números e moeda

Se você estiver usando funções de formatação do .NET, lembre-se de especificar a cultura para que separadores decimais sejam analisados corretamente (e evite que exceções de conversão sejam lançadas). Por exemplo, 1,99 e 1, 99 são representações decimais válidas dependendo da sua localidade.

Quando os dados são provenientes de uma fonte conhecida (ou seja, do seu próprio código ou de um serviço Web que você controla), você pode codificar um identificador de cultura que corresponda à formatação, como InvariantCulture, que funcionará para a formatação padrão do idioma inglês.

```csharp
double.Parse("1,999.99", CultureInfo.InvariantCulture);
```

Se os dados estiverem sendo inseridos pelo usuário do aplicativo, analise-os usando uma instância CultureInfo que reflete sua localidade:

```csharp
double.Parse("1 999,99", CultureInfo.CreateSpecificCulture("fr-FR"));
```

Consulte os artigos [analisando cadeias de caracteres numéricas](https://msdn.microsoft.com/library/xbtzcc4w(v=vs.110).aspx) e [data e hora de análise](https://msdn.microsoft.com/library/2h3syy57(v=vs.110).aspx) do MSDN para obter informações adicionais.

<a name="rtl" />

### <a name="right-to-left-rtl-languages"></a>Idiomas da direita para a esquerda (RTL)

Algumas linguagens, como árabe, Hebraico e urdu (por exemplo), são lidas da direita para a esquerda.
Os aplicativos que dão suporte a esses idiomas devem usar designs de tela que se adaptem a leitores da direita para a esquerda, por exemplo:

- O texto deve estar alinhado à direita.
- Os rótulos devem aparecer à direita dos campos de entrada.
- O posicionamento do botão padrão geralmente é invertido.
- O toque e a animação de navegação hierárquica (e outras metáforas de navegação e animações) que usam a direção para o contexto também devem ser revertidos.

O iOS e o Android dão suporte a layouts da direita para a esquerda e à renderização de fontes, com recursos internos que ajudam a fazer os ajustes acima. O Xamarin. Forms não oferece suporte automaticamente à renderização RTL.

### <a name="sorting"></a>Classificação

Diferentes idiomas definem a ordem de classificação de seus alfabetos de forma diferente, mesmo quando usam o mesmo conjunto de caracteres.

Consulte os [detalhes da comparação de cadeias de caracteres](https://msdn.microsoft.com/library/dd465121(v=vs.110).aspx#the_details_of_string_comparison) nas [práticas recomendadas para usar cadeias no .NET Framework](https://msdn.microsoft.com/library/dd465121(v=vs.110).aspx) para obter um exemplo em que Language (CultureInfo) afeta a ordem de classificação.

É improvável que os recursos de banco de dados internos nas plataformas móveis ofereçam suporte à ordenação de classificação específica de idioma, para que você possa ser solicitado a implementar código adicional em sua lógica de negócios.

### <a name="text-search"></a>Pesquisa de texto

Certifique-se de escrever e testar seu algoritmo de pesquisa com vários idiomas em mente. As coisas a serem consideradas incluem:

- Preenchimento automático – se você tiver criado uma função de preenchimento automático, certifique-se de que as fontes de origem sejam sugestões relevantes para o idioma do usuário.
- Consulta de correspondência para dados – as consultas de pesquisa inseridas em um idioma específico serão executadas em apenas conteúdo escrito nesse idioma ou em relação a todo o conteúdo em seu aplicativo?
- Lematização – se sua pesquisa for criada para pesquisar palavras semelhantes, raízes do Word e outras otimizações de pesquisa, serão essas otimizações criadas para todos os idiomas aos quais você dá suporte?
- Classificação – Verifique se os resultados estão classificados corretamente (veja acima).

### <a name="data-from-external-sources"></a>Dados de fontes externas

Muitos aplicativos baixam dados de fontes externas, do Twitter e RSS feeds para o clima, notícias ou preços de ações. Ao exibi-lo para um usuário, você precisa considerar a possibilidade de exibir uma tela de informações irrelevantes ou ilegíveis.

Há algumas estratégias que você pode usar para tentar e garantir que seu aplicativo exiba os dados relevantes para o usuário:

- Fontes diferentes – seu aplicativo pode baixar os dados de uma fonte diferente dependendo do idioma ou da localidade do usuário. Notícias de localidade, os preços de clima e de estoque podem fazer mais sentido do que algo baixado de um feed norte-americano.
- Exibição localizada – se você estiver exibindo um Twitter ou um feed de fotos, deverá exibir os metadados (como o tempo gasto) em seu próprio idioma, mesmo que o próprio conteúdo permaneça no idioma original.
- Tradução – você pode criar uma opção de conversão em seu aplicativo para fazer uma tradução automática dos dados de entrada. Isso pode ser automático ou a critério do usuário – apenas lembre-se de notificar o usuário se isso estiver ocorrendo, já que as traduções da máquina nunca são perfeitos!

Isso também pode afetar links externos para faixas de áudio ou vídeos – ao projetar seu aplicativo, não se esqueça de planejar antecipadamente o conteúdo traduzido ou garantir que os usuários sejam adequadamente informados pela interface do usuário quando o conteúdo não for apresentado em seus idioma.

### <a name="dont-over-translate"></a>Não sobretraduzir

Algumas cadeias de caracteres em seu aplicativo podem não precisar de tradução ou, na pior das hipóteses, precisam de atenção especial pelo tradutor. Os exemplos podem incluir:

- URLs – se você listar uma URL, ela poderá ou não precisar ser ajustada por idioma. Por exemplo, facebook.com não requer tradução, ele detecta automaticamente a linguagem no site principal. Outros sites têm conteúdo específico de localidade e talvez você queira oferecer uma URL diferente, como yahoo.com versus yahoo.fr ou yahoo.it.
- Números de telefone – especialmente aqueles com códigos de país ou números diferentes para chamadores que falam um idioma específico.
- Detalhes de contato – endereços e outras informações podem variar por idioma ou localidade.
- Marcas comerciais & nomes de produtos – algumas cadeias de caracteres não precisam ser traduzidas porque são sempre escritas na mesma linguagem.

Por fim, não se esqueça de incluir instruções detalhadas para o tradutor se determinadas cadeias de caracteres exigirem tratamento especial.

### <a name="formatted-text"></a>Texto formatado

Geralmente não é um problema com aplicativos móveis porque cadeias de caracteres geralmente não são formatadas de forma avançada. No entanto, se o Rich Text (como formatação em negrito ou itálico) for necessário em seu aplicativo, verifique se o tradutor sabe como inserir a formatação, se os arquivos de cadeias de caracteres o armazenam corretamente e se estão formatados corretamente antes de serem exibidos para o usuário (ou seja, não deixe acidentalmente os próprios códigos de formatação são apresentados ao usuário).

## <a name="translation-tips"></a>Dicas de tradução

A tradução das cadeias de caracteres usadas por um aplicativo é considerada parte do processo de localização. Normalmente, essa tarefa será terceirizada para um serviço de tradução e executada por uma equipe multilíngue que pode não conhecer seu aplicativo ou sua empresa.

As dicas a seguir ajudarão você a produzir cadeias de caracteres mais fáceis de serem traduzidas com precisão e, portanto, melhorar a qualidade dos seus aplicativos localizados.

### <a name="localize-complete-strings-not-words"></a>Localizar cadeias de caracteres completas, não palavras

Às vezes, os desenvolvedores tomam a abordagem de tentar especificar palavras únicas ou "trechos de código" para que possam usá-las novamente em todo o aplicativo. Por exemplo, para o texto "você tem 5 mensagens". Eles podem especificar as seguintes cadeias de caracteres para tradução

**Inadequado**:

```csharp
"You have"
"no"
"message"
"messages"
```

e tente criar a frase correta imediatamente no código usando a concatenação de cadeia de caracteres:

**Inadequado**:

```csharp
"You have" + " " + numMsgs + " " + "messages"
"You have" + " no " + "messages"
```

**Isso não é recomendado** porque ele não funciona necessariamente para todas as linguagens e será difícil para o tradutor entender o contexto de cada segmento curto. Ele também leva a reutilização de cadeias de caracteres traduzidas, o que poderá causar problemas posteriormente se eles forem usados em diferentes contextos (e depois forem atualizados).

### <a name="allow-for-parameter-re-ordering"></a>Permitir reordenação de parâmetro

Algumas linguagens de programação exigem sintaxe extra para especificar a ordem dos parâmetros em uma cadeia de caracteres, no entanto, o .NET já dá suporte ao conceito de espaços reservados numerados, portanto

**Bom**:

```csharp
"a {0} b {1} cde {3}"
```

pode ser traduzido o seguinte (onde a posição e a ordem dos espaços reservados são alteradas)

```csharp
"{2} {3} f g h {0}"
```

e os tokens serão ordenados como o tradutor pretendido. Certifique-se de incluir uma explicação do que cada espaço reservado contém ao enviar a cadeia de caracteres para um tradutor.

### <a name="use-multiple-strings-for-cardinality"></a>Usar várias cadeias de caracteres para cardinalidade

Evite cadeias `"You have {0} message/s."` de caracteres como usar cadeias de caracteres específicas para cada Estado para fornecer uma melhor experiência do usuário:

**Bom**:

```csharp
"You have no messages."
"You have 1 message."
"You have 2 messages."
"You have {0} messages."
```

Você precisará escrever código em seu aplicativo para avaliar o número que está sendo exibido e escolher a cadeia de caracteres apropriada. Algumas plataformas (incluindo iOS e Android) têm recursos internos para escolher automaticamente a melhor cadeia de caracteres do plural com base nas preferências do idioma/localidade atual.

### <a name="allowing-for-gender"></a>Permitindo o gênero

As linguagens baseadas em latim às vezes usam palavras diferentes, dependendo do gênero do assunto. Se seu aplicativo sabe sobre o gênero, você deve permitir que as cadeias de caracteres traduzidas reflitam isso.

Também há um caso mais óbvio, até mesmo em inglês, em que as cadeias de caracteres referem-se a uma pessoa ou usuário específico do seu aplicativo. Por exemplo, alguns sites mostram mensagens como `"Bob commented on his post"` , portanto, você precisa de cadeias de caracteres para um sexo masculino, fêmea e não binário ou desconhecido:

**Bom**:

```csharp
"{0} commented on his post"
"{0} commented on her post"
"{0} commented on their post"
```

### <a name="dont-reuse-strings"></a>Não reutilizar cadeias de caracteres

Ou com mais precisão, não reutilize cadeias de caracteres apenas porque elas são semelhantes quando a cadeia de caracteres tem uma finalidade ou significado diferente.

Por exemplo: Imagine que você tenha uma opção liga/desliga em seu aplicativo e o controle switch precise que o texto para ' on ' e ' off ' seja localizado. Você também exibe o valor dessa configuração em outro lugar no aplicativo em um rótulo de texto. Você deve usar cadeias de caracteres diferentes para a exibição de comutador em comparação com o status do comutador (mesmo se eles forem a mesma cadeia de caracteres no idioma padrão) – por exemplo:

- "On" – exibido no próprio comutador
- "Off" – exibido no próprio comutador
- "On" – exibido em um rótulo
- "Off" – exibido em um rótulo

Isso fornece a máxima flexibilidade para o Tradutor:

- Por motivos de design, talvez o comutador em si use "on" e "off", mas o rótulo de exibição use maiúsculas e minúsculas "on" e "off".
- Algumas linguagens podem precisar que o valor da opção seja abreviado para caber no controle de interface do usuário, enquanto a palavra completa (traduzida) pode aparecer no rótulo.
- Como alternativa, para alguns idiomas, a renderização do comutador pode ser usar "I" e "O" para uma familiaridade cultural, mas você ainda pode querer que o rótulo Leia "ligado" ou "desativado".

### <a name="translation-services"></a>Serviços de tradução

#### <a name="machine-translation"></a>Tradução automática

Para criar recursos de tradução em seu aplicativo, considere o [API de tradução de texto do Azure](https://azure.microsoft.com/services/cognitive-services/translator-text-api/).

Para fins de teste, você pode usar uma das muitas ferramentas de tradução online para incluir algum texto localizado em seu aplicativo durante o desenvolvimento:

- [Tradutor do Bing](https://www.bing.com/translator/)
- [Tradução do Google](http://translate.google.com/)

Há muitos outros disponíveis. A qualidade da tradução automática geralmente não é considerada boa o suficiente para liberar um aplicativo sem primeiro ser revisado e testado por tradutores profissionais ou palestrantes nativos.

#### <a name="professional-translation"></a>Tradução profissional

Também há serviços profissionais de tradução que utilizarão suas cadeias de caracteres e as distribuirão para seus próprios tradutores, fornecendo a você traduções concluídas por uma taxa.

Um dos melhores serviços conhecidos é o [Lionbridge](http://www.lionbridge.com/). A maioria dos serviços profissionais oferece suporte a todos os tipos de arquivo comuns, incluindo cadeias de caracteres, XML, RESX e POT/PO.

## <a name="summary"></a>Resumo

Este artigo introduziu alguns dos conceitos com os quais você deve estar familiarizado antes de internacionalizar seu aplicativo e, em seguida, localizar seus recursos e também abordou como alterar as preferências de idioma de cada plataforma.

Esses conceitos podem ser aplicados às várias técnicas específicas de plataforma e de internacionalização entre plataformas que são possíveis com o Xamarin.

Continue lendo os detalhes técnicos da plataforma em que você está interessado:

- Localização entre plataformas do [Xamarin. Forms](~/xamarin-forms/app-fundamentals/localization/index.md) usando arquivos resx.
- Localização da plataforma nativa do [Xamarin. Ios](~/ios/app-fundamentals/localization/index.md) .
- Localização da plataforma nativa do [Xamarin. Android](~/android/app-fundamentals/localization.md) .

## <a name="related-links"></a>Links relacionados

- [Visão geral da localização da Apple](https://developer.apple.com/internationalization/)
- [Lista de verificação de localização do Android](https://developer.android.com/distribute/tools/localization-checklist.html)
- [Práticas recomendadas para o desenvolvimento de aplicativos preparados para o mundo (MSDN)](https://msdn.microsoft.com/library/w7x1y988%28v=vs.90%29.aspx)
