---
title: Ciclo de vida de atividade
description: As atividades são um bloco de construção fundamental de aplicativos Android e podem existir em vários Estados diferentes. O ciclo de vida da atividade começa com a instanciação e termina com a destruição e inclui muitos Estados no between. Quando uma atividade muda de estado, o método de evento do ciclo de vida apropriado é chamado, notificando a atividade da alteração de estado iminente e permitindo que ele execute o código para se adaptar a essa alteração. Este artigo examina o ciclo de vida das atividades e explica a responsabilidade de uma atividade durante cada uma dessas alterações de estado fazer parte de um aplicativo confiável e bem comparado.
ms.prod: xamarin
ms.assetid: 05B34788-F2D2-4347-B66B-40AFD7B1D167
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/28/2018
ms.openlocfilehash: 2472086c700a6b2a93a4a1a834d7a7d3e6e635ce
ms.sourcegitcommit: 8fa0cb9ccbc107d697aa5b9113a4e5d1e75d6eb9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2020
ms.locfileid: "96303051"
---
# <a name="activity-lifecycle"></a>Ciclo de vida de atividade

_As atividades são um bloco de construção fundamental de aplicativos Android e podem existir em vários Estados diferentes. O ciclo de vida da atividade começa com a instanciação e termina com a destruição e inclui muitos Estados no between. Quando uma atividade muda de estado, o método de evento do ciclo de vida apropriado é chamado, notificando a atividade da alteração de estado iminente e permitindo que ele execute o código para se adaptar a essa alteração. Este artigo examina o ciclo de vida das atividades e explica a responsabilidade de uma atividade durante cada uma dessas alterações de estado fazer parte de um aplicativo confiável e bem comparado._

## <a name="activity-lifecycle-overview"></a>Visão geral do ciclo de vida da atividade

As atividades são um conceito de programação incomum específico ao Android. No desenvolvimento de aplicativos tradicionais, geralmente há um método estático Main, que é executado para iniciar o aplicativo. Com o Android, no entanto, as coisas são diferentes; Os aplicativos Android podem ser iniciados por meio de qualquer atividade registrada dentro de um aplicativo. Na prática, a maioria dos aplicativos terá apenas uma atividade específica que é especificada como o ponto de entrada do aplicativo. No entanto, se um aplicativo falhar ou for encerrado pelo sistema operacional, o sistema operacional poderá tentar reiniciar o aplicativo na última atividade aberta ou em qualquer outro lugar dentro da pilha de atividades anterior.
Além disso, o sistema operacional pode pausar atividades quando elas não estiverem ativas e recuperá-las se estiver com pouca memória. Deve-se fazer uma consideração cuidadosa para permitir que o aplicativo restaure corretamente seu estado caso uma atividade seja reiniciada, especialmente se essa atividade depender de dados de atividades anteriores.

O ciclo de vida da atividade é implementado como uma coleção de métodos que o sistema operacional chama durante todo o ciclo de vida de uma atividade. Esses métodos permitem que os desenvolvedores implementem a funcionalidade necessária para atender aos requisitos de gerenciamento de estado e de recursos de seus aplicativos.

É extremamente importante que o desenvolvedor do aplicativo analise os requisitos de cada atividade para determinar quais métodos expostos pelo ciclo de vida da atividade precisam ser implementados. A falha em fazer isso pode resultar em instabilidade do aplicativo, falhas, inchar de recursos e, possivelmente, até mesmo instabilidade do sistema operacional subjacente.

Este capítulo examina o ciclo de vida da atividade em detalhes, incluindo:

- Estados de atividade
- Métodos de ciclo de vida
- Retendo o estado de um aplicativo

Esta seção também inclui uma [explicação](~/android/app-fundamentals/activity-lifecycle/saving-state.md) que fornece exemplos práticos sobre como salvar com eficiência o estado durante o ciclo de vida da atividade. Ao final deste capítulo, você deve ter uma compreensão do ciclo de vida da atividade e de como dar suporte a ele em um aplicativo Android.

## <a name="activity-lifecycle"></a>Ciclo de vida de atividade

O ciclo de vida da atividade do Android consiste em uma coleção de métodos expostos na classe Activity que fornecem ao desenvolvedor uma estrutura de gerenciamento de recursos. Essa estrutura permite que os desenvolvedores atendam aos requisitos exclusivos de gerenciamento de estado de cada atividade em um aplicativo e manipulem adequadamente o gerenciamento de recursos.

### <a name="activity-states"></a>Estados de atividade

O sistema operacional Android arbitra atividades com base em seu estado. Isso ajuda o Android a identificar atividades que não estão mais em uso, permitindo que o sistema operacional recupere memória e recursos. O diagrama a seguir ilustra os Estados pelos quais uma atividade pode passar durante seu tempo de vida:

[![Diagrama de Estados de atividade](images/image1-sml.png)](images/image1.png#lightbox)

Esses Estados podem ser divididos em quatro grupos principais da seguinte maneira:

1. *Ativo ou em execução* &ndash; As atividades são consideradas ativas ou em execução se estiverem em primeiro plano, também conhecidas como a parte superior da pilha de atividades. Essa é considerada a atividade de prioridade mais alta no Android e, como tal, só será eliminada pelo sistema operacional em situações extremas, como se a atividade tentar usar mais memória do que está disponível no dispositivo, pois isso pode fazer com que a interface do usuário não responda.

1. *Em pausa* &ndash; Quando o dispositivo passa para o estado de suspensão ou uma atividade ainda é visível, mas parcialmente ocultada por uma atividade nova, não de tamanho máximo ou transparente, a atividade é considerada pausada. As atividades em pausa ainda estão ativas, ou seja, elas mantêm todas as informações de estado e membro e permanecem anexadas ao Gerenciador de janelas. Essa é considerada a segunda atividade de prioridade mais alta no Android e, como tal, só será eliminada pelo sistema operacional se a eliminação dessa atividade atender aos requisitos de recursos necessários para manter a atividade ativa/em execução estável e responsiva.

1. *Parado/em segundo plano* &ndash; As atividades que são completamente obscurecidas por outra atividade são consideradas interrompidas ou em segundo plano.
    As atividades interrompidas ainda tentam reter suas informações de estado e membro pelo tempo máximo possível, mas as atividades interrompidas são consideradas como a prioridade mais baixa dos três Estados e, como tal, o sistema operacional eliminará as atividades nesse estado primeiro para atender aos requisitos de recursos de atividades de prioridade mais alta.

1. *Reiniciado* &ndash; É possível que uma atividade que seja colocada em pausa seja interrompida no ciclo de vida para ser removida da memória pelo Android. Se o usuário navega de volta para a atividade, ele deve ser reiniciado, restaurado para o estado salvo anteriormente e, em seguida, exibido para o usuário.

### <a name="activity-re-creation-in-response-to-configuration-changes"></a>Re-Creation de atividade em resposta a alterações de configuração

Para tornar as coisas mais complicadas, o Android gera mais uma chave inglesa na combinação chamada de alterações de configuração. As alterações de configuração são ciclos rápidos de destruição/recriação de atividade que ocorrem quando a configuração de uma atividade é alterada, como quando o dispositivo é [girado](~/android/app-fundamentals/handling-rotation.md) (e a atividade precisa ser recriada no modo paisagem ou retrato), quando o teclado é exibido (e a atividade é apresentada com uma oportunidade de redimensionamento a si mesmo) ou quando o dispositivo é colocado em um Dock,

As alterações de configuração ainda causam as mesmas alterações de estado de atividade que ocorreram durante a interrupção e a reinicialização de uma atividade. No entanto, para garantir que um aplicativo se sinta responsivo e funcione bem durante alterações de configuração, é importante que eles sejam tratados o mais rápido possível. Por isso, o Android tem uma API específica que pode ser usada para persistir o estado durante as alterações de configuração.
Abordaremos isso mais tarde no [estado de gerenciamento durante a seção de ciclo de vida](~/android/app-fundamentals/activity-lifecycle/index.md#Managing_State_Throughout_the_Lifecycle) .

### <a name="activity-lifecycle-methods"></a>Métodos do ciclo de vida da atividade

A SDK do Android e, por extensão, a estrutura Xamarin. Android fornece um modelo poderoso para gerenciar o estado das atividades em um aplicativo. Quando o estado de uma atividade é alterado, a atividade é notificada pelo sistema operacional, que chama métodos específicos nessa atividade. O diagrama a seguir ilustra esses métodos em relação ao ciclo de vida da atividade:

[![Fluxograma do ciclo de vida da atividade](images/image2-sml.png)](images/image2.png#lightbox)

Como desenvolvedor, você pode manipular alterações de estado substituindo esses métodos em uma atividade. No entanto, é importante observar que todos os métodos do ciclo de vida são chamados no thread da interface do usuário e impedirão que o sistema operacional execute a próxima parte do trabalho da interface do usuário, como ocultar a atividade atual, exibir uma nova atividade etc. Assim, o código nesses métodos deve ser o mais breve possível para fazer com que um aplicativo fique bem executado. Todas as tarefas de execução longa devem ser executadas em um thread em segundo plano.

Vamos examinar cada um desses métodos de ciclo de vida e seu uso:

#### <a name="oncreate"></a>OnCreate

[OnCreate](xref:Android.App.Activity.OnCreate*) é o primeiro método a ser chamado quando uma atividade é criada.
`OnCreate` é sempre substituído para executar todas as inicializações de inicialização que podem ser exigidas por uma atividade, como:

- Criando exibições
- Inicializando variáveis
- Associando dados estáticos a listas

`OnCreate` usa um parâmetro de [pacote](xref:Android.OS.Bundle) , que é um dicionário para armazenar e passar informações de estado e objetos entre atividades se o pacote não for nulo, isso indica que a atividade está sendo reiniciada e deve restaurar seu estado da instância anterior. O código a seguir ilustra como recuperar valores do pacote:

```csharp
protected override void OnCreate(Bundle bundle)
{
   base.OnCreate(bundle);

   string intentString;
   bool intentBool;

   if (bundle != null)
   {
      intentString = bundle.GetString("myString");
      intentBool = bundle.GetBoolean("myBool");
   }

   // Set our view from the "main" layout resource
   SetContentView(Resource.Layout.Main);
}
```

Uma vez `OnCreate` concluído, o Android chamará `OnStart` .

#### <a name="onstart"></a>OnStart

[OnStart](xref:Android.App.Activity.OnStart) é sempre chamado pelo sistema após a `OnCreate` conclusão. As atividades podem substituir esse método se precisarem executar qualquer tarefa específica imediatamente antes que uma atividade se torne visível, como atualizar os valores atuais das exibições na atividade. O Android será chamado `OnResume` imediatamente após esse método.

#### <a name="onresume"></a>OnContinue

O sistema chama [onresume](xref:Android.App.Activity.OnResume) quando a atividade está pronta para começar a interagir com o usuário.
As atividades devem substituir esse método para executar tarefas como:

- Taxas de quadros de aumento de escala (uma tarefa comum no desenvolvimento de jogos)
- Iniciando animações
- Ouvindo atualizações de GPS
- Exibir alertas ou caixas de diálogo relevantes
- Conectar manipuladores de eventos externos

Por exemplo, o trecho de código a seguir mostra como inicializar a câmera:

```csharp
public void OnResume()
{
    base.OnResume(); // Always call the superclass first.

    if (_camera==null)
    {
        // Do camera initializations here
    }
}
```

`OnResume` é importante porque qualquer operação feita no `OnPause` deve ser desfeita no `OnResume` , já que é o único método de ciclo de vida que é garantido para ser executado depois `OnPause` de voltar à vida da atividade.

#### <a name="onpause"></a>OnPause

[Onpaus](xref:Android.App.Activity.OnPause) é chamado quando o sistema está prestes a colocar a atividade em segundo plano ou quando a atividade se torna parcialmente obscurecida. As atividades devem substituir esse método se precisarem:

- Confirmar alterações não salvas em dados persistentes

- Destruir ou limpar outros objetos que consomem recursos

- Taxas de quadros de rampa e pausar animações

- Cancelar o registro de manipuladores de eventos externos ou manipuladores de notificação (ou seja, aqueles que estão vinculados a um serviço). Isso deve ser feito para evitar vazamentos de memória de atividade.

- Da mesma forma, se a atividade tiver exibido quaisquer caixas de diálogo ou alertas, eles deverão ser limpos com o `.Dismiss()` método.

Por exemplo, o trecho de código a seguir liberará a câmera, pois a atividade não poderá usá-la enquanto estiver em pausa:

```csharp
public void OnPause()
{
    base.OnPause(); // Always call the superclass first

    // Release the camera as other activities might need it
    if (_camera != null)
    {
        _camera.Release();
        _camera = null;
    }
}
```

Há dois métodos de ciclo de vida possíveis que serão chamados após `OnPause` :

1. `OnResume` será chamado se a atividade for retornada para o primeiro plano.
1. `OnStop` será chamado se a atividade estiver sendo colocada em segundo plano.

#### <a name="onstop"></a>OnStop

[OnStop](xref:Android.App.Activity.OnStop) é chamado quando a atividade não está mais visível para o usuário. Isso acontece quando ocorre uma das seguintes situações:

- Uma nova atividade está sendo iniciada e está cobrindo essa atividade.
- Uma atividade existente está sendo trazida para o primeiro plano.
- A atividade está sendo destruída.

`OnStop` Nem sempre pode ser chamado em situações de pouca memória, como quando o Android está sem problemas para recursos e não pode fazer o plano de fundo da atividade corretamente. Por esse motivo, é melhor não confiar na `OnStop` chamada ao preparar uma atividade para destruição. Os próximos métodos de ciclo de vida que podem ser chamados depois disso serão `OnDestroy` se a atividade estiver desaparecendo ou `OnRestart` se a atividade voltar a interagir com o usuário.

#### <a name="ondestroy"></a>OnDestroy

[OnDestroy](xref:Android.App.Activity.OnDestroy) é o método final que é chamado em uma instância de atividade antes de ser destruído e removido completamente da memória. Em situações extremas, o Android pode eliminar o processo do aplicativo que está hospedando a atividade, o que resultará em `OnDestroy` não ser invocado. A maioria das atividades não implementará esse método, pois a maioria das limpeza e desligamento foi feita nos `OnPause` `OnStop` métodos e. O `OnDestroy` método é normalmente substituído para limpar tarefas de execução longa que podem vazar recursos. Um exemplo disso pode ser threads em segundo plano que foram iniciados no `OnCreate` .

Não haverá nenhum método de ciclo de vida chamado depois que a atividade tiver sido destruída.

#### <a name="onrestart"></a>Restart

[Restart](xref:Android.App.Activity.OnRestart) é chamado depois que sua atividade for interrompida antes de ser iniciada novamente. Um bom exemplo disso seria quando o usuário pressionasse o botão página inicial enquanto estiver em uma atividade no aplicativo. Quando isso acontece `OnPause` `OnStop` , os métodos são chamados e a atividade é movida para o plano de fundo, mas não é destruída. Se o usuário fosse então restaurar o aplicativo usando o Gerenciador de tarefas ou um aplicativo semelhante, o Android chamará o `OnRestart` método da atividade.

Não há diretrizes gerais para as quais o tipo de lógica deve ser implementado `OnRestart` . Isso ocorre porque `OnStart` o é sempre invocado independentemente de a atividade estar sendo criada ou reiniciada, portanto, todos os recursos exigidos pela atividade devem ser inicializados em `OnStart` , em vez de `OnRestart` .

O próximo método de ciclo de vida chamado depois `OnRestart` será `OnStart` .

### <a name="back-vs-home"></a>Voltar versus página inicial

Muitos dispositivos Android têm dois botões distintos: um botão "voltar" e um botão "início". Um exemplo disso pode ser visto na seguinte captura de tela do Android 4.0.3:

[![Botões voltar e página inicial](images/image4-sml.png)](images/image4.png#lightbox)

Há uma diferença sutil entre os dois botões, mesmo que eles pareçam ter o mesmo efeito de colocar um aplicativo em segundo plano. Quando um usuário clica no botão voltar, ele está informando ao Android que eles são concluídos com a atividade. O Android destruirá a atividade. Por outro lado, quando o usuário clica no botão página inicial, a atividade é simplesmente colocada em segundo plano o &ndash; Android não encerrará a atividade.

<a name="Managing_State_Throughout_the_Lifecycle"></a>

## <a name="managing-state-throughout-the-lifecycle"></a>Gerenciando o estado durante todo o ciclo de vida

Quando uma atividade é interrompida ou destruída, o sistema fornece uma oportunidade de salvar o estado da atividade para reidratação posteriores.
Esse estado salvo é conhecido como estado de instância. O Android fornece três opções para armazenar o estado da instância durante o ciclo de vida da atividade:

1. Armazenar valores primitivos em um `Dictionary` conhecido como um [pacote](xref:Android.OS.Bundle) que será usado pelo Android para salvar o estado.

1. Criar uma classe personalizada que conterá valores complexos, como bitmaps. O Android usará essa classe personalizada para salvar o estado.

1. Burlar o ciclo de vida da alteração de configuração e assumir a responsabilidade completa de manter o estado na atividade.

Este guia aborda as duas primeiras opções.

### <a name="bundle-state"></a>Estado do pacote

A opção principal para salvar o estado da instância é usar um objeto de dicionário de chave/valor conhecido como um [pacote](xref:Android.OS.Bundle).
Lembre-se de que quando uma atividade é criada `OnCreate` , o método é passado como um pacote como parâmetro, esse pacote pode ser usado para restaurar o estado da instância. Não é recomendável usar um pacote para dados mais complexos que não sejam serializados de forma rápida ou fácil em pares de chave/valor (como bitmaps); em vez disso, ele deve ser usado para valores simples como cadeias de caracteres.

Uma atividade fornece métodos para ajudar a salvar e recuperar o estado da instância no pacote:

- [OnSaveInstanceState](xref:Android.App.Activity.OnSaveInstanceState*) &ndash; Isso é invocado pelo Android quando a atividade está sendo destruída. As atividades podem implementar esse método se precisarem persistir quaisquer itens de estado de chave/valor.

- [OnRestoreInstanceState](xref:Android.App.Activity.OnRestoreInstanceState*) &ndash; Isso é chamado depois que o `OnCreate` método é concluído e fornece outra oportunidade para uma atividade restaurar seu estado após a conclusão da inicialização.

O diagrama a seguir ilustra como esses métodos são usados:

[![Fluxograma de Estados de pacote](images/image3-sml.png)](images/image3.png#lightbox)

#### <a name="onsaveinstancestate"></a>OnSaveInstanceState

[OnSaveInstanceState](xref:Android.App.Activity.OnSaveInstanceState*) será chamado quando a atividade estiver sendo interrompida. Ele receberá um parâmetro de pacote no qual a atividade pode armazenar seu estado. Quando um dispositivo passa por uma alteração de configuração, uma atividade pode usar o `Bundle` objeto que é passado para preservar o estado da atividade substituindo `OnSaveInstanceState` . Por exemplo, considere o seguinte código:

```csharp
int c;

protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);

  this.SetContentView (Resource.Layout.SimpleStateView);

  var output = this.FindViewById<TextView> (Resource.Id.outputText);

  if (bundle != null) {
    c = bundle.GetInt ("counter", -1);
  } else {
    c = -1;
  }

  output.Text = c.ToString ();

  var incrementCounter = this.FindViewById<Button> (Resource.Id.incrementCounter);

  incrementCounter.Click += (s,e) => {
    output.Text = (++c).ToString();
  };
}
```

O código acima incrementa um inteiro chamado `c` quando um botão chamado `incrementCounter` é clicado, exibindo o resultado em um `TextView` nome `output` . Quando ocorre uma alteração de configuração-por exemplo, quando o dispositivo é girado, o código acima perderia o valor de `c` porque `bundle` seria `null` , conforme mostrado na figura abaixo:

[![A exibição não mostra o valor anterior](images/07-sml.png)](images/07.png#lightbox)

Para preservar o valor de `c` neste exemplo, a atividade pode ser substituída `OnSaveInstanceState` , salvando o valor no pacote, conforme mostrado abaixo:

```csharp
protected override void OnSaveInstanceState (Bundle outState)
{
  outState.PutInt ("counter", c);
  base.OnSaveInstanceState (outState);
}
```

Agora, quando o dispositivo é girado para uma nova orientação, o inteiro é salvo no pacote e recuperado com a linha:

```csharp
c = bundle.GetInt ("counter", -1);
```

> [!NOTE]
> É importante sempre chamar a implementação base do `OnSaveInstanceState` para que o estado da hierarquia de exibição também possa ser salvo.

##### <a name="view-state"></a>Estado de exibição

A substituição `OnSaveInstanceState` é um mecanismo apropriado para salvar dados transitórios em uma atividade entre alterações de orientação, como o contador no exemplo acima. No entanto, a implementação padrão do se `OnSaveInstanceState` encarregará de salvar dados transitórios na interface do usuário para cada exibição, desde que cada exibição tenha uma ID atribuída. Por exemplo, digamos que um aplicativo tenha um `EditText` elemento definido em XML da seguinte maneira:

```xml
<EditText android:id="@+id/myText"
  android:layout_width="fill_parent"
  android:layout_height="wrap_content"/>
```

Como o `EditText` controle tem um `id` atribuído, quando o usuário insere alguns dados e gira o dispositivo, os dados ainda são exibidos, conforme mostrado abaixo:

[![Os dados são preservados no modo paisagem](images/08-sml.png)](images/08.png#lightbox)

#### <a name="onrestoreinstancestate"></a>OnRestoreInstanceState

[OnRestoreInstanceState](xref:Android.App.Activity.OnRestoreInstanceState*) será chamado após `OnStart` . Ele fornece uma atividade da oportunidade de restaurar qualquer estado salvo anteriormente em um pacote durante o anterior `OnSaveInstanceState` . No entanto, esse é o mesmo pacote fornecido para o `OnCreate` .

O código a seguir demonstra como o estado pode ser restaurado em `OnRestoreInstanceState` :

```csharp
protected override void OnRestoreInstanceState(Bundle savedState)
{
    base.OnRestoreSaveInstanceState(savedState);
    var myString = savedState.GetString("myString");
    var myBool = savedState.GetBoolean("myBool");
}
```

Esse método existe para fornecer alguma flexibilidade em relação a quando o estado deve ser restaurado. Às vezes, é mais apropriado aguardar até que todas as inicializações sejam feitas antes de restaurar o estado da instância. Além disso, uma subclasse de uma atividade existente pode querer apenas restaurar determinados valores do estado da instância. Em muitos casos, não é necessário substituir `OnRestoreInstanceState` , já que a maioria das atividades pode restaurar o estado usando o pacote fornecido para o `OnCreate` .

Para obter um exemplo de como salvar o estado usando um `Bundle` , consulte a explicação sobre como [salvar o estado da atividade](saving-state.md).

#### <a name="bundle-limitations"></a>Limitações de pacote

Embora o `OnSaveInstanceState` facilite o salvamento de dados transitórios, ele tem algumas limitações:

- Ele não é chamado em todos os casos. Por exemplo, pressionar **Home** ou **voltar** para sair de uma atividade não resultará em `OnSaveInstanceState` chamada.

- O pacote transmitido `OnSaveInstanceState` não foi projetado para objetos grandes, como imagens. No caso de objetos grandes, é preferível salvar o objeto de [OnRetainNonConfigurationInstance](xref:Android.App.Activity.OnRetainNonConfigurationInstance) , conforme discutido abaixo.

- Os dados salvos usando o pacote são serializados, o que pode levar a atrasos.

O estado do pacote é útil para dados simples que não usam muita memória, enquanto *dados de instância que não* são de configuração são úteis para dados mais complexos, ou dados que são caros de recuperar, como de uma chamada de serviço da Web ou uma consulta de banco de dado complicada. Os dados da instância sem configuração são salvos em um objeto, conforme necessário. A próxima seção apresenta `OnRetainNonConfigurationInstance` uma maneira de preservar tipos de dados mais complexos por meio de alterações de configuração.

### <a name="persisting-complex-data"></a>Persistindo dados complexos

Além de persistir dados no pacote, o Android também dá suporte ao salvamento de dados, substituindo [OnRetainNonConfigurationInstance](xref:Android.App.Activity.OnRetainNonConfigurationInstance) e retornando uma instância de um `Java.Lang.Object` que contém os dados a serem persistidos. Há dois benefícios principais do uso do `OnRetainNonConfigurationInstance` para salvar o estado:

- O objeto retornado de `OnRetainNonConfigurationInstance` funciona bem com tipos de dados maiores e mais complexos porque a memória retém esse objeto.

- O `OnRetainNonConfigurationInstance` método é chamado sob demanda e somente quando necessário. Isso é mais econômico do que usar um cache manual.

`OnRetainNonConfigurationInstance`O uso do é adequado para cenários em que é caro recuperar os dados várias vezes, como em chamadas de serviço Web. Por exemplo, considere o código a seguir que pesquisa o Twitter:

```csharp
public class NonConfigInstanceActivity : ListActivity
{
  protected override void OnCreate (Bundle bundle)
  {
    base.OnCreate (bundle);
    SearchTwitter ("xamarin");
  }

  public void SearchTwitter (string text)
  {
    string searchUrl = String.Format("http://search.twitter.com/search.json?" + "q={0}&rpp=10&include_entities=false&" + "result_type=mixed", text);

    var httpReq = (HttpWebRequest)HttpWebRequest.Create (new Uri (searchUrl));
    httpReq.BeginGetResponse (new AsyncCallback (ResponseCallback), httpReq);
  }

  void ResponseCallback (IAsyncResult ar)
  {
    var httpReq = (HttpWebRequest)ar.AsyncState;

    using (var httpRes = (HttpWebResponse)httpReq.EndGetResponse (ar)) {
      ParseResults (httpRes);
    }
  }

  void ParseResults (HttpWebResponse httpRes)
  {
    var s = httpRes.GetResponseStream ();
    var j = (JsonObject)JsonObject.Load (s);

    var results = (from result in (JsonArray)j ["results"] let jResult = result as JsonObject select jResult ["text"].ToString ()).ToArray ();

    RunOnUiThread (() => {
      PopulateTweetList (results);
    });
  }

  void PopulateTweetList (string[] results)
  {
    ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.ItemView, results);
  }
}
```

Esse código recupera os resultados da Web formatada como JSON, analisa-os e apresenta os resultados em uma lista, conforme mostrado na seguinte captura de tela:

[![Resultados exibidos na tela](images/06-sml.png)](images/06.png#lightbox)

Quando ocorre uma alteração de configuração-por exemplo, quando um dispositivo é girado, o código repete o processo. Para reutilizar os resultados recuperados originalmente e não causar chamadas de rede redundantes e desnecessárias, podemos usar `OnRetainNonconfigurationInstance` para salvar os resultados, conforme mostrado abaixo:

```csharp
public class NonConfigInstanceActivity : ListActivity
{
  TweetListWrapper _savedInstance;

  protected override void OnCreate (Bundle bundle)
  {
    base.OnCreate (bundle);

    var tweetsWrapper = LastNonConfigurationInstance as TweetListWrapper;

    if (tweetsWrapper != null) {
      PopulateTweetList (tweetsWrapper.Tweets);
    } else {
      SearchTwitter ("xamarin");
    }

    public override Java.Lang.Object OnRetainNonConfigurationInstance ()
    {
      base.OnRetainNonConfigurationInstance ();
      return _savedInstance;
    }

    ...

    void PopulateTweetList (string[] results)
    {
      ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.ItemView, results);
      _savedInstance = new TweetListWrapper{Tweets=results};
    }
}
```

Agora, quando o dispositivo for girado, os resultados originais serão recuperados da `LastNonConfiguartionInstance` propriedade. Neste exemplo, os resultados consistem em um `string[]` Tweets que contém. Como `OnRetainNonConfigurationInstance` o requer que `Java.Lang.Object` seja retornado, o `string[]` é encapsulado em uma classe que subclasses `Java.Lang.Object` , como mostrado abaixo:

```csharp
class TweetListWrapper : Java.Lang.Object
{
  public string[] Tweets { get; set; }
}
```

Por exemplo, tentar usar um `TextView` como o objeto retornado por `OnRetainNonConfigurationInstance` vazará a atividade, conforme ilustrado pelo código abaixo:

```csharp
TextView _textView;

protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);

  var tv = LastNonConfigurationInstance as TextViewWrapper;

  if(tv != null) {
    _textView = tv;
    var parent = _textView.Parent as FrameLayout;
    parent.RemoveView(_textView);
  } else {
    _textView = new TextView (this);
    _textView.Text = "This will leak.";
  }

  SetContentView (_textView);
}

public override Java.Lang.Object OnRetainNonConfigurationInstance ()
{
  base.OnRetainNonConfigurationInstance ();
  return _textView;
}
```

Nesta seção, aprendemos como preservar dados de estado simples com o `Bundle` e persistem tipos de dados mais complexos com o `OnRetainNonConfigurationInstance` .

## <a name="summary"></a>Resumo

O ciclo de vida da atividade do Android fornece uma estrutura poderosa para o gerenciamento de estado das atividades em um aplicativo, mas pode ser difícil de entender e implementar. Este capítulo apresentou os diferentes Estados em que uma atividade pode passar durante seu tempo de vida, bem como os métodos de ciclo de vida associados a esses Estados. Em seguida, as diretrizes foram fornecidas em que tipo de lógica deve ser executada em cada um desses métodos.

## <a name="related-links"></a>Links Relacionados

- [Tratamento de rotação](~/android/app-fundamentals/handling-rotation.md)
- [Atividade do Android](xref:Android.App.Activity)
