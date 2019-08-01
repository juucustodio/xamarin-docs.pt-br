---
title: Desempenho de plataforma cruzada
description: Este documento descreve várias técnicas que podem ser usadas para melhorar o desempenho de um aplicativo móvel. Ele aborda o Criador de Perfil, o recurso IDisposable, as referências fracas, o coletor de lixo SGen, as técnicas de redução de tamanho e muito mais.
ms.prod: xamarin
ms.assetid: 9ce61f18-22ac-4b93-91be-5b499677d661
author: asb3993
ms.author: amburns
ms.date: 03/24/2017
ms.openlocfilehash: 2665e45277eccaedbbbbd77f6c7c0ca47700d950
ms.sourcegitcommit: c2bffcdee5a7c619280c6b9032ba90e4df8b63d9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68533018"
---
# <a name="cross-platform-performance"></a>Desempenho de plataforma cruzada

O baixo desempenho de aplicativo se apresenta de várias maneiras. Ele pode fazer com que o aplicativo pareça não responder, deixar a rolagem lenta ou reduzir a vida útil da bateria. No entanto, a otimização do desempenho engloba mais do que apenas a implementação de um código eficiente. A experiência do usuário quanto ao desempenho do aplicativo também deve ser considerada. Por exemplo, garantir que as operações sejam executadas sem impedir o usuário de realizar outras atividades pode ajudar a melhorar a experiência do usuário.

<a name="profiler" />

## <a name="use-the-profiler"></a>Usar o Profiler

Ao desenvolver um aplicativo, é importante tentar otimizar código apenas após ele ter sido analisado. Criação de perfil é uma técnica para determinar onde as otimizações de código terão o maior efeito na redução de problemas de desempenho. O criador de perfil controla o uso de memória do aplicativo e registra o tempo de execução dos métodos no aplicativo. Esses dados ajudam a navegar pelos caminhos de execução do aplicativo e o custo da execução do código, para que as melhores oportunidades de otimização possam ser descobertas.

O Xamarin Profiler medirá, avaliará e ajudará a localizar problemas relacionados ao desempenho em um aplicativo. Ele pode ser usado para analisar aplicativos Xamarin.iOS e Xamarin.Android do Visual Studio para Mac ou Visual Studio. Para obter mais informações sobre o Xamarin Profiler, veja [Introdução ao Xamarin Profiler](~/tools/profiler/index.md).

As melhores práticas a seguir são recomendadas ao analisar um aplicativo:

- Evite a criação de perfil de um aplicativo em um simulador, pois o simulador pode distorcer o desempenho do aplicativo.
- Idealmente, a criação de perfil deve ser executada em uma variedade de dispositivos, pois tomar medidas de desempenho em um único dispositivo nem sempre mostrará as características de desempenho de outros dispositivos. No entanto, no mínimo, de criação de perfil deve ser executada em um dispositivo que tem a menor especificação antecipada.
- Feche todos os outros aplicativos para garantir que o impacto total do aplicativo que está sendo analisado está sendo medido, em vez de o de outros aplicativos.

<a name="idisposable" />

## <a name="release-idisposable-resources"></a>Liberar recursos IDisposable

A interface `IDisposable` fornece um mecanismo para liberar recursos. Ele fornece um método `Dispose` que deve ser implementado para liberar recursos explicitamente. `IDisposable` não é um destruidor e só deve ser implementado nas seguintes circunstâncias:

- Quando a classe tem recursos não gerenciados. Recursos não gerenciados típicos que exigem liberação incluem arquivos, fluxos e conexões de rede.
- Quando a classe tem recursos `IDisposable` gerenciados.

Os consumidores de tipo podem chamar a implementação `IDisposable.Dispose` para liberar recursos quando a instância não é mais necessária. Há duas abordagens para fazer isso:

- Encapsulando o objeto `IDisposable` em uma instrução `using`.
- Encapsulando a chamada `IDisposable.Dispose` em um bloco `try`/`finally`.

### <a name="wrapping-the-idisposable-object-in-a-using-statement"></a>Encapsulando o objeto IDisposable em uma instrução using

O exemplo de código a seguir mostra como encapsular um objeto `IDisposable` em uma instrução `using`:

```csharp
public void ReadText (string filename)
{
  ...
  string text;
  using (StreamReader reader = new StreamReader (filename)) {
    text = reader.ReadToEnd ();
  }
  ...
}
```

A classe `StreamReader` implementa `IDisposable` e a instrução `using` fornece uma sintaxe conveniente, que chama o método `StreamReader.Dispose` no objeto `StreamReader` antes que ele saia do escopo. Dentro do bloco `using`, o objeto `StreamReader` é somente leitura e não pode ser reatribuído. A instrução `using` garante que o método `Dispose` seja chamado mesmo se ocorrer uma exceção, pois o compilador implementa a IL (linguagem intermediária) em um bloco `try`/`finally`.

### <a name="wrapping-the-call-to-idisposabledispose-in-a-tryfinally-block"></a>Encapsulamento da chamada para IDisposable.Dispose em um bloco Try/Finally

O exemplo de código a seguir mostra como encapsular a chamada para `IDisposable.Dispose` em um bloco `try`/`finally`:

```csharp
public void ReadText (string filename)
{
  ...
  string text;
  StreamReader reader = null;
  try {
    reader = new StreamReader (filename);
    text = reader.ReadToEnd ();
  } finally {
    if (reader != null) {
      reader.Dispose ();
    }
  }
  ...
}
```

A classe `StreamReader` implementa `IDisposable` e o bloco `finally` chama o método `StreamReader.Dispose` para liberar o recurso.

Para obter mais informações, veja [Interface IDisposable](xref:System.IDisposable).

<a name="events" />

## <a name="unsubscribe-from-events"></a>Cancelar assinatura de eventos

Para evitar vazamentos de memória, os eventos devem ser cancelados antes que o objeto do assinante seja descartado. Até que a assinatura do evento seja cancelada, o delegado para o evento no objeto de publicação tem uma referência para o delegado que encapsula o manipulador de eventos do assinante. Desde que o objeto de publicação contenha essa referência, a coleta de lixo não recuperará a memória do objeto de assinante.

O exemplo de código a seguir mostra como cancelar a assinatura de um evento:

```csharp
public class Publisher
{
  public event EventHandler MyEvent;

  public void OnMyEventFires ()
  {
    if (MyEvent != null) {
      MyEvent (this, EventArgs.Empty);
    }
  }
}

public class Subscriber : IDisposable
{
  readonly Publisher publisher;

  public Subscriber (Publisher publish)
  {
    publisher = publish;
    publisher.MyEvent += OnMyEventFires;
  }

  void OnMyEventFires (object sender, EventArgs e)
  {
    Debug.WriteLine ("The publisher notified the subscriber of an event");
  }

  public void Dispose ()
  {
    publisher.MyEvent -= OnMyEventFires;
  }
}
```

A classe `Subscriber` cancela a assinatura do evento em seu método `Dispose`.

Ciclos de referência também podem ocorrer ao usar manipuladores de eventos e sintaxe lambda, já que expressões lambda podem fazer referência a objetos e mantê-los ativos. Portanto, uma referência para o método anônimo pode ser armazenada em um campo e usada para cancelar a inscrição do evento, conforme mostrado no exemplo de código a seguir:

```csharp
public class Subscriber : IDisposable
{
  readonly Publisher publisher;
  EventHandler handler;

  public Subscriber (Publisher publish)
  {
    publisher = publish;
    handler = (sender, e) => {
      Debug.WriteLine ("The publisher notified the subscriber of an event");
    };
    publisher.MyEvent += handler;
  }

  public void Dispose ()
  {
    publisher.MyEvent -= handler;
  }
}
```

O campo `handler` mantém a referência para o método anônimo e é usado para assinatura e cancelamento de assinatura do evento.

<a name="weakreferences" />

## <a name="use-weak-references-to-prevent-immortal-objects"></a>Usar referências fracas para impedir a criação de objetos imortais

> [!NOTE]
> Os desenvolvedores iOS devem revisar a documentação sobre [evitar referências circulares no iOS](~/ios/deploy-test/performance.md#avoid-strong-circular-references) para garantir que os aplicativos usem a memória com eficiência.

<a name="lazy" />

## <a name="delay-the-cost-of-creating-objects"></a>Atrasar o custo de criação de objetos

A inicialização lenta pode ser usada para adiar a criação de um objeto até que ele seja usado pela primeira vez. Essa técnica é usada principalmente para melhorar o desempenho, evitar a computação e reduzir os requisitos de memória.


Considere o uso da inicialização lenta para objetos com custo de criação elevado nesses dois cenários:

- O aplicativo não pode usar o objeto.
- Outras operações caras devem ser concluídas antes que o objeto seja criado.

A classe `Lazy<T>` é usada para definir um tipo inicializado lentamente, conforme demonstrado no exemplo de código a seguir:

```csharp
void ProcessData(bool dataRequired = false)
{
  Lazy<double> data = new Lazy<double>(() =>
  {
    return ParallelEnumerable.Range(0, 1000)
                 .Select(d => Compute(d))
                 .Aggregate((x, y) => x + y);
  });

  if (dataRequired)
  {
    if (data.Value > 90)
    {
      ...
    }
  }
}

double Compute(double x)
{
  ...
}
```

A inicialização lenta ocorre na primeira vez que a propriedade `Lazy<T>.Value` é acessada. O tipo encapsulado é criado e retornado no primeiro acesso, então é armazenado para eventuais acessos futuros.

Para obter mais informações sobre a inicialização lenta, veja [Inicialização lenta](https://msdn.microsoft.com/library/dd997286(v=vs.110).aspx).

<a name="async" />

## <a name="implement-asynchronous-operations"></a>Implementar operações assíncronas

O .NET fornece versões assíncronas de muitas de suas APIs. Ao contrário das APIs síncronas, as APIs assíncronas certificam-se de que o thread de execução ativo nunca bloqueie o thread de chamada por uma quantidade significativa de tempo. Portanto, ao chamar uma API do thread de interface do usuário, use a API assíncrona se ela estiver disponível. Isso manterá o thread de interface do usuário desbloqueado, o que ajudará a melhorar a experiência do usuário com o aplicativo.

Além disso, as operações de execução longa devem ser executadas em um thread em segundo plano, para evitar o bloqueio do thread de interface do usuário. O .NET fornece as palavras-chave `async` e `await` que permitem a gravação de código assíncrono, que executa operações de execução longa em um thread em segundo plano e acessa os resultados após a conclusão. No entanto, embora as operações de execução longa possam ser executadas de forma assíncrona com a palavra-chave `await`, isso não garante que a operação será executada em um thread em segundo plano. Em vez disso, é possível fazer com que ela seja executada desse modo passando a operação de execução longa para `Task.Run`, conforme mostrado no exemplo de código a seguir:

```csharp
public class FaceDetection
{
  ...
  async void RecognizeFaceButtonClick(object sender, EventArgs e)
  {
    await Task.Run(() => RecognizeFace ());
    ...
  }

  async Task RecognizeFace()
  {
    ...
  }
}
```

O método `RecognizeFace` é executado em um thread em segundo plano, com o método `RecognizeFaceButtonClick` esperando até que o método `RecognizeFace` seja concluído antes de continuar.

Operações de execução longa também devem dar suporte a cancelamento. Por exemplo, continuar a executar uma operação de execução longa poderá tornar-se desnecessário se o usuário navegar dentro do aplicativo. O padrão para implementar o cancelamento é o seguinte:

- Crie uma instância de `CancellationTokenSource`. Essa instância gerenciará e enviará notificações de cancelamento.
- Passe o valor da propriedade `CancellationTokenSource.Token` para cada tarefa que deve ser cancelável.
- Forneça um mecanismo para cada tarefa responder ao cancelamento.
- Chame o método `CancellationTokenSource.Cancel` para fornecer uma notificação de cancelamento.

> [!IMPORTANT]
> A classe `CancellationTokenSource` implementa a interface `IDisposable` e então o método `CancellationTokenSource.Dispose` deve ser invocado uma vez que o trabalho com a instância `CancellationTokenSource` for concluído.



Para obter mais informações, veja [Visão geral do suporte assíncrono](~/cross-platform/platform/async.md).

<a name="sgen" />

## <a name="use-the-sgen-garbage-collector"></a>Usar o coletor de lixo SGen

Linguagens gerenciadas como o C# usam a coleta de lixo para recuperar a memória alocada para objetos que não estão mais em uso. Os dois coletores de lixo usados pela plataforma Xamarin são:

- [**SGen**](https://www.mono-project.com/docs/advanced/garbage-collector/sgen/) – esse é um coletor de lixo geracional e é o coletor de lixo padrão na plataforma Xamarin.
- [**Boehm**](http://www.hboehm.info/gc/) – esse é um coletor de lixo conservador, não geracional. É o coletor de lixo padrão usado para aplicativos Xamarin.iOS que usam a API clássica.

SGen utiliza um dos três heaps para alocar espaço para objetos:

-  **O Berçário** – é onde os novos objetos pequenos são alocados. Quando o berçário ficar sem espaço, ocorrerá uma coleta de lixo secundária. Todos os objetos ativos serão movidos para o heap principal.
-  **Heap Principal** – é aqui que são mantidos os objetos de execução longa. Se não houver memória suficiente no heap principal, uma coleta de lixo principal ocorrerá. Se uma coleta de lixo principal falhar liberar em memória suficiente, o SGen solicitará mais memória ao sistema.
-  **Espaço de objeto grande** – aqui são mantidos os objetos que exigem mais de 8.000 bytes. Objetos grandes não começarão no berçário, mas em vez disso serão alocados nesse heap.

Uma das vantagens de SGen é que o tempo necessário para executar uma coleta de lixo secundária é proporcional ao número de novos objetos ativos que foram criados desde a última coleta de lixo secundária. Isso reduzirá o impacto de coleta de lixo sobre o desempenho de um aplicativo, porque essas coletas de lixo secundárias levarão menos tempo do que uma coleta de lixo principal. Coletas de lixo principais ainda ocorrem, mas com menos frequência.

### <a name="reducing-pressure-on-the-garbage-collector"></a>Redução da pressão sobre o coletor de lixo

Quando SGen inicia uma coleta de lixo, ele interromperá os threads do aplicativo enquanto ele recupera a memória. Enquanto a memória está sendo recuperada, o aplicativo pode ter uma pequena pausa ou apresentar intermitências na interface do usuário. O quão perceptível essa pausa é depende de dois fatores:

1. **Frequência** – frequência ocorre a coleta de lixo. A frequência de coletas de lixo aumentará à medida que mais memória for alocada entre coletas.
1. **Duração** – quanto tempo levará cada coleta de lixo individual. Isso é aproximadamente proporcional ao número de objetos ativos que estão sendo coletados.

Coletivamente, isso significa que, se vários objetos forem alocados, mas não permanecerem ativos, haverá muitas coletas de lixo curtas. Por outro lado, se novos objetos forem alocados lentamente e os objetos permanecerem ativos, haverá menos coletas de lixo, mas elas serão mais demoradas.

Para reduzir a pressão sobre o coletor de lixo, siga estas diretrizes:

- Evite a coleta de lixo em loops apertados usando pools de objeto. Isso é particularmente relevante para jogos, que precisam criar a maioria de seus objetos com antecedência.
- Libere explicitamente recursos como fluxos, conexões de rede, blocos grandes de memória e arquivos quando eles não forem mais necessários. Para obter mais informações, veja [Liberar recursos IDisposable](#idisposable).
- Cancele o registro de manipuladores de eventos quando eles não forem mais necessários para tornar os objetos colecionáveis. Para obter mais informações, consulte [Unsubscribe from Events](#events) (Cancelar assinatura de eventos).

<a name="linker" />

## <a name="reduce-the-size-of-the-application"></a>Reduzir o tamanho do aplicativo

É importante compreender o processo de compilação em cada plataforma para entender de onde vem o tamanho do executável de um aplicativo:

- Aplicativos iOS passam por compilação AOT (Ahead Of Time) para linguagem assembly do ARM. O .NET framework está incluído e as classes não utilizadas são eliminadas somente quando a opção de vinculador apropriada está habilitada.
- Aplicativos Android são compilados em IL (linguagem intermediária) e empacotados com o MonoVM e a compilação JIT (Just-In-Time). Classes de estrutura não utilizadas são eliminadas somente quando a opção de vinculador apropriada está habilitada.
- Aplicativos do Windows Phone são compilados para IL e executados segundo o tempo de execução interno.

Além disso, se um aplicativo fizer uso extensivo de genéricos, o tamanho final do executável aumentará ainda mais, uma vez que ele conterá versões compiladas nativamente das possibilidades genéricas.

Para ajudar a reduzir o tamanho dos aplicativos, a plataforma Xamarin inclui um vinculador como parte das ferramentas de build. Por padrão o vinculador está desabilitado e deve ser habilitado nas opções do projeto do aplicativo. No momento do build, ele executará uma análise estática do aplicativo para determinar quais tipos e membros são realmente usados pelo aplicativo. Ele então removerá quaisquer tipos e métodos não utilizados do aplicativo.

A captura de tela a seguir mostra as opções do vinculador no Visual Studio para Mac para um projeto do Xamarin.iOS:

![](memory-perf-best-practices-images/linker-options-ios.png)

A captura de tela a seguir mostra as opções do vinculador no Visual Studio para Mac para um projeto do Xamarin.Android:

![](memory-perf-best-practices-images/linker-options-droid.png)

O vinculador fornece três diferentes configurações para controlar seu comportamento:

-  **Não vincular** – nenhum método nem tipo não utilizado será removido pelo vinculador. Por motivos de desempenho, essa é a configuração padrão para builds de depuração.
-  **Vincular somente Assemblies de SDKs/SDK da Estrutura** – essa configuração reduzirá o tamanho apenas dos assemblies que são enviados via Xamarin. O código do usuário não será afetado.
-  **Vincular Todos os Assemblies** – essa é uma otimização mais agressiva que tem como destino o código do usuário e assemblies do SDK. Para associações, isso removerá os campos de suporte não utilizados e tornará cada instância (ou objetos associados) mais leves, consumindo menos memória.

O *Vincular Todos os Assemblies* deve ser usado com cuidado, pois pode interromper o aplicativo de maneiras inesperadas. A análise estática realizada pelo vinculador pode não identificar corretamente todo o código que é necessário, resultando na remoção de uma quantidade excessiva de código do aplicativo compilado. Essa situação se manifestará somente em tempo de execução quando o aplicativo falhar. Por isso, é importante testar um aplicativo depois de alterar o comportamento do vinculador.

Se o teste revelar que o vinculador removeu incorretamente uma classe ou método, será possível marcar tipos ou métodos que não são referenciados estaticamente, mas são exigidos pelo aplicativo pelo uso de um dos seguintes atributos:

-  `Xamarin.iOS.Foundation.PreserveAttribute` – esse atributo é para projetos Xamarin.iOS.
-  `Android.Runtime.PreserveAttribute` – esse atributo é para projetos Xamarin.Android.

Por exemplo, pode ser necessário preservar os construtores padrão de tipos que são instanciados dinamicamente. Além disso, o uso da serialização XML pode exigir que as propriedades de tipos sejam preservadas.

Para obter mais informações, veja [Vinculador para iOS](~/ios/deploy-test/linker.md) e [Vinculador para Android](~/android/deploy-test/linker.md).

### <a name="additional-size-reduction-techniques"></a>Técnicas adicionais de redução de tamanho

Há uma grande variedade de arquiteturas de CPU que alimentam dispositivos móveis. Portanto, o Xamarin.iOS e o Xamarin.Android produzem *binários FAT*, que contêm uma versão compilada do aplicativo para cada arquitetura de CPU. Isso garante que um aplicativo móvel possa ser executado em um dispositivo, independentemente da arquitetura da CPU.

As etapas a seguir podem ser usadas para reduzir ainda mais o tamanho do executável do aplicativo:

- Certifique-se de que um build de versão é produzido.
- Reduza o número de arquiteturas para as quais o aplicativo é compilado, para evitar que um binário FAT seja produzido.
- Certifique-se de que o compilador LLVM está sendo usado para gerar um executável mais otimizado.
- Reduza o tamanho do código gerenciado do aplicativo. Isso pode ser feito habilitando o vinculador em cada assembly (*Vincular todos* para projetos iOS e *Vincular todos os assemblies* para projetos Android).

Aplicativos Android também podem ser divididos em um APK separado para cada ABI ("arquitetura").
Saiba mais nesta postagem no blog: [Como reduzir o tamanho do aplicativo Android](http://motzcod.es/post/112072508362/how-to-keep-your-android-app-size-down).

<a name="optimizeimages" />

## <a name="optimize-image-resources"></a>Otimizar os recursos de imagem

As imagens são alguns dos recursos mais caros que os aplicativos usam e, geralmente, são capturadas em alta resolução. Enquanto isso cria imagens vibrantes cheias de detalhes, aplicativos que exibem tais imagens normalmente exigem mais recursos da CPU para decodificar a imagem e mais memória para armazenar a imagem decodificada. É dispendioso decodificar uma imagem de alta resolução na memória sendo que ele será reduzido para um tamanho menor para exibição. Em vez disso, reduza o volume de memória e o uso de CPU criando versões de imagens armazenadas com várias resoluções, próximas dos tamanhos de exibição previstos. Por exemplo, uma imagem exibida em uma exibição de lista provavelmente deve ter uma resolução menor do que uma imagem exibida em tela inteira. Além disso, versões reduzidas das imagens de alta resolução podem ser carregadas para exibi-las de modo eficiente, com impacto mínimo sobre a memória. Para obter mais informações, veja [Loading Large Bitmaps Efficiently](https://github.com/xamarin/recipes/tree/master/Recipes/android/resources/general/load_large_bitmaps_efficiently) (Como carregar bitmaps de modo eficiente).

Independentemente da resolução da imagem, os recursos de imagem de exibição podem aumentar significativamente o volume de memória do aplicativo. Portanto, eles só devem ser criados quando necessário e devem ser liberados assim que o aplicativo não exigi-los mais.

<a name="activationperiod" />

## <a name="reduce-the-application-activation-period"></a>Reduzir o período de ativação do aplicativo

Todos os aplicativos têm um *período de ativação*, que é o tempo entre quando o aplicativo é iniciado e quando o aplicativo está pronto para uso. O período de ativação permite que os usuários tenham a primeira impressão do aplicativo. Portanto, é importante reduzir o período de ativação e a percepção que os usuários têm dele, para que o aplicativo cause uma primeira impressão favorável.

Antes de um aplicativo exibir sua interface do usuário inicial, ele deve fornecer uma tela inicial para indicar ao usuário que o aplicativo está sendo iniciado. Se o aplicativo não puder exibir rapidamente sua interface do usuário inicial, a tela inicial deverá usada para informar ao usuário o progresso durante o período de ativação, a fim de oferecer uma garantia de que o aplicativo não parou. Essa garantia pode ser uma barra de progresso ou um controle semelhante.

Durante o período de ativação os aplicativos executam a lógica de ativação, que geralmente inclui o carregamento e o processamento de recursos. O período de ativação pode ser reduzido, garantindo que os recursos necessários sejam empacotados no aplicativo, em vez de serem recuperados remotamente. Por exemplo, em algumas circunstâncias, pode ser apropriado durante o período de ativação carregar dados de espaço reservado armazenados localmente. Em seguida, depois que a interface do usuário inicial é exibida e o usuário é capaz de interagir com o aplicativo, os dados de espaço reservado podem ser substituídos progressivamente de uma fonte remota. Além disso, a lógica de ativação do aplicativo deve executar apenas o trabalho necessário para permitir que o usuário comece a usar o aplicativo. Isso pode ajudar se atrasar o carregamento de assemblies adicionais, já que assemblies são carregados na primeira vez em que eles são usados.

<a name="webservicecommunication" />

## <a name="reduce-web-service-communication"></a>Reduzir a comunicação de serviços Web

Conectar-se de um aplicativo a um serviço Web pode ter um impacto no desempenho do aplicativo. Por exemplo, um aumento do uso da largura de banda de rede resultará em um aumento no uso da bateria do dispositivo. Além disso, os usuários podem estar usando o aplicativo em um ambiente com largura de banda limitada. Portanto, é sensato limitar a utilização de largura de banda entre um aplicativo e um serviço Web.

É uma abordagem para reduzir a utilização da largura de banda de um aplicativo é compactar os dados antes de transferi-los por uma rede. No entanto, o uso adicional de CPU gerado pelo processo de compactação também pode resultar em um uso maior da bateria. Portanto, essa compensação deve ser avaliada cuidadosamente antes de decidir se é necessário mover os dados compactados por uma rede.

Outra questão a considerar é o formato dos dados que se movem entre um aplicativo e um serviço Web. Os dois principais formatos são XML (Extensible Markup Language) e JSON (JavaScript Object Notation). XML é um formato de troca de dados baseado em texto que gera conteúdos de dados relativamente grandes, porque ele contém um grande número de caracteres de formatação. JSON é um formato de troca de dados baseado em texto que gera conteúdos de dados compactos, o que resulta em redução de requisitos de largura de banda ao enviar dados e receber dados. Portanto, JSON é geralmente o formato preferencial para aplicativos móveis.

Recomenda-se usar DTOs (objetos de transferência de dados) ao transferir dados entre um aplicativo e um serviço Web. Um DTO contém um conjunto de dados para transferência pela rede. Utilizando DTOs, mais dados podem ser transmitidos em uma única chamada remota, o que pode ajudar a reduzir o número de chamadas remotas feitas pelo aplicativo. Em geral, a quantidade de tempo utilizada em uma chamada remota com um conteúdo maior de dados é similar àquela utilizada em uma chamada que contém apenas um conteúdo de dados pequeno.

Dados recuperados do serviço Web devem ser armazenados em cache localmente, com os dados armazenados em cache sendo utilizados em vez de repetidamente recuperados do serviço Web. No entanto, ao adotar essa abordagem, uma estratégia adequada de cache deve ser implementada para atualizar os dados no cache local se eles forem alterados no serviço Web.

## <a name="summary"></a>Resumo

Esse artigo descreve e discute técnicas para aumentar o desempenho dos aplicativos criados usando a plataforma Xamarin. Coletivamente, essas técnicas podem reduzir de forma considerável a quantidade de trabalho que está sendo executado por uma CPU e a quantidade de memória consumida por um aplicativo.

## <a name="related-links"></a>Links relacionados

- [Desempenho do Xamarin.iOS](~/ios/deploy-test/performance.md)
- [Desempenho do Xamarin.Android](~/android/deploy-test/performance.md)
- [Introdução ao Xamarin Profiler](~/tools/profiler/index.md)
- [Desempenho do Xamarin.Forms](~/xamarin-forms/deploy-test/performance.md)
- [Visão geral do suporte assíncrono](~/cross-platform/platform/async.md)
- [IDisposable](xref:System.IDisposable)
