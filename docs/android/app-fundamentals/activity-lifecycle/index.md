---
title: Ciclo de vida de atividade
description: Atividades são um componente fundamental dos aplicativos do Android e eles podem existir em vários estados diferentes. O ciclo de vida da atividade começa com instanciação e termina com a destruição e inclui muitos estados entre. Quando uma atividade muda de estado, o método de evento de ciclo de vida apropriado é chamado, notificando a atividade de alteração de estado iminente e permitindo que ele executar o código para adaptar-se a alteração. Este artigo examina o ciclo de vida de atividades e explica a responsabilidade se uma atividade tem durante cada uma dessas alterações de estado para ser parte de um aplicativo com bom comportamento e confiável.
ms.prod: xamarin
ms.assetid: 05B34788-F2D2-4347-B66B-40AFD7B1D167
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/28/2018
ms.openlocfilehash: f35f3e59d8b669795ade3d370894e45866cea1ff
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="activity-lifecycle"></a>Ciclo de vida de atividade

_Atividades são um componente fundamental dos aplicativos do Android e eles podem existir em vários estados diferentes. O ciclo de vida da atividade começa com instanciação e termina com a destruição e inclui muitos estados entre. Quando uma atividade muda de estado, o método de evento de ciclo de vida apropriado é chamado, notificando a atividade de alteração de estado iminente e permitindo que ele executar o código para adaptar-se a alteração. Este artigo examina o ciclo de vida de atividades e explica a responsabilidade se uma atividade tem durante cada uma dessas alterações de estado para ser parte de um aplicativo com bom comportamento e confiável._

## <a name="activity-lifecycle-overview"></a>Visão geral do ciclo de vida da atividade

As atividades são um conceito de programação incomum específico do Android. No desenvolvimento de aplicativos tradicionais normalmente há um método estático principal, que é executado para iniciar o aplicativo. Com Android, no entanto, as coisas são diferentes; Aplicativos do Android podem ser iniciados por meio de qualquer atividade registrada em um aplicativo. Na prática, a maioria dos aplicativos terá apenas uma atividade específica que é especificada como o ponto de entrada do aplicativo. No entanto, se um aplicativo falhar, ou é encerrada pelo sistema operacional, o sistema operacional pode tentar reiniciar o aplicativo na última atividade aberta ou qualquer outro local dentro da pilha de atividade anterior.
Além disso, o sistema operacional pode pausar atividades quando não estiver ativos e recupere-os se ele está com pouco memória. Uma consideração cuidadosa deve ser feita para permitir que o aplicativo restaurar corretamente o estado que uma atividade for reiniciada, especialmente se que atividade depende de dados de atividades anteriores.

O ciclo de vida da atividade é implementado como uma coleção de chamadas de métodos, o sistema operacional em todo o ciclo de vida de uma atividade. Esses métodos permitem que os desenvolvedores a implementar a funcionalidade que é necessária para atender os requisitos de gerenciamento de estado e o recurso de seus aplicativos.

É extremamente importante para o desenvolvedor do aplicativo analisar os requisitos de cada atividade para determinar quais métodos expostos pelo ciclo de vida da atividade precisam ser implementado. Falha ao fazer isso pode resultar em instabilidade no aplicativo, falhas, inchaço de recurso e instabilidade no sistema operacional subjacente provavelmente.

Este capítulo examina o ciclo de vida da atividade detalhadamente, incluindo:

-  Estados de atividade
-  Métodos de ciclo de vida
-  Mantém o estado de um aplicativo


Esta seção também inclui um [passo a passo](~/android/app-fundamentals/activity-lifecycle/saving-state.md) que fornecem exemplos práticos sobre como salvar o estado com eficiência durante o ciclo de vida da atividade. No final deste capítulo, você deve ter um entendimento de como o ciclo de vida da atividade e como dar suporte a ele em um aplicativo do Android.

## <a name="activity-lifecycle"></a>Ciclo de vida de atividade

O ciclo de vida da atividade Android consiste em uma coleção de métodos expostos dentro da classe de atividade que oferecem aos desenvolvedores uma estrutura de gerenciamento de recursos. Essa estrutura permite que os desenvolvedores atendem aos requisitos de gerenciamento de estado exclusivo de cada atividade dentro de um aplicativo e lidar adequadamente com o gerenciamento de recursos.

### <a name="activity-states"></a>Estados de atividade

O sistema operacional Android arbitra atividades com base em seu estado. Isso ajuda a Android identificar as atividades que não estão mais em uso, permitindo que o sistema operacional recuperar a memória e recursos. O diagrama a seguir ilustra os estados de que uma atividade pode percorrer durante seu ciclo de vida:

[![Diagrama de estados de atividade](images/image1-sml.png)](images/image1.png#lightbox)

Esses estados podem ser divididos em 4 grupos principais da seguinte maneira:

1.  *Ativa ou em execução* &ndash; atividades são consideradas ativo ou em execução se eles estiverem em primeiro plano, também conhecidas como o topo da pilha de atividade. Isso é considerado a atividade de prioridade mais alta no Android e como tal, só será eliminado pelo sistema operacional em situações extremas, tais como se a atividade tenta usar mais memória que estão disponível no dispositivo como isso pode fazer com que a interface do usuário pare de responder.

1.  *Em pausa* &ndash; quando o dispositivo entra em suspensão ou uma atividade é parcialmente oculta por uma atividade de novo, em tamanho normal ou transparente mas ainda estiver visível, a atividade é considerada em pausa. Atividades em pausa ainda estão ativos, ou seja, manter todas as informações de estado e membro e permanecer conectados para o Gerenciador de janelas. Isso é considerado como a segunda atividade de prioridade mais alta no Android e, como tal, só serão eliminadas pelo sistema operacional se eliminar essa atividade vai satisfazer os requisitos de recursos necessários para manter a atividade ativa/em execução estável e capacidade de resposta.

1.  *Interrompido/Backgrounded* &ndash; atividades que estão completamente obscurecidas por outra atividade são consideradas parada ou em segundo plano.
    Atividades interrompidas ainda tentam manter suas informações de estado e membro para como possíveis, mas paradas atividades são consideradas como a prioridade mais baixa de três estados e, assim, o sistema operacional será kill atividades nesse estado primeiro para atender o recurso requisitos de atividades de prioridade mais altas.

1.  *Reiniciado* &ndash; é possível que uma atividade em qualquer lugar de em pausa para parado no ciclo de vida a ser removido da memória pelo Android. Se o usuário navega de volta para a atividade deve ser reiniciado, restaurado ao estado salvo anteriormente e, em seguida, é exibida para o usuário.


### <a name="activity-re-creation-in-response-to-configuration-changes"></a>Recriação da atividade em resposta às alterações de configuração

Para tornar os assuntos mais complicados, Android gera uma chave mais na combinação de chamada de alterações de configuração. Alterações de configuração são atividade rápida destruição/re-creation ciclos que ocorrem quando a configuração de uma atividade for alterada, como quando o dispositivo está [girado](~/android/app-fundamentals/handling-rotation.md) (e a atividade precisa obter reconstruído em paisagem ou retrato modo), quando o teclado é exibido (e a atividade é apresentada uma oportunidade para redimensionar próprio), ou quando o dispositivo é colocado em um encaixe, entre outros.

Alterações de configuração ainda farão com que as mesmas alterações de estado de atividade que podem ocorrer durante a interrupção e reinicialização de uma atividade. No entanto, para certificar-se de que um aplicativo está respondendo e executa bem durante alterações de configuração, é importante que seja tratado assim que possível. Por isso, o Android tem uma API específica que pode ser usada para manter o estado durante alterações de configuração.
Abordaremos isso mais tarde no [gerenciamento de estado em todo o ciclo de vida](~/android/app-fundamentals/activity-lifecycle/index.md#Managing_State_Throughout_the_Lifecycle) seção.

### <a name="activity-lifecycle-methods"></a>Métodos de ciclo de vida da atividade

O SDK do Android e, por extensão, a estrutura de xamarin fornecem um modelo eficiente para gerenciar o estado de atividades em um aplicativo. Quando a alteração de estado de uma atividade, a atividade é notificada pelo sistema operacional, que chama os métodos específicos em que a atividade. O diagrama a seguir ilustra esses métodos em relação ao ciclo de vida da atividade:

[![Fluxograma de ciclo de vida da atividade](images/image2-sml.png)](images/image2.png#lightbox)

Como desenvolvedor, você pode manipular as alterações de estado, substituindo esses métodos em uma atividade. É importante, no entanto, observe que todos os métodos de ciclo de vida são chamados no thread da interface do usuário e bloquearão o sistema operacional de executar a próxima parte do trabalho de interface do usuário, como ocultar a atividade atual, exibindo uma nova atividade, etc. Como tal, o código nesses métodos deve ser maior brevidade possível fazer com que um aplicativo se sentir o desempenho. As tarefas de longa execução devem ser executadas em um thread em segundo plano.

Vamos examinar cada um desses métodos de ciclo de vida e seu uso:

#### <a name="oncreate"></a>OnCreate

[OnCreate](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) é o primeiro método a ser chamado quando uma atividade é criada.
`OnCreate` sempre é substituído para executar qualquer inicializações de inicialização que podem ser exigidas por uma atividade, como:

-  Criando modos de exibição
-  Inicializando variáveis
-  Associando dados estáticos a listas


`OnCreate` leva um [pacote](https://developer.xamarin.com/api/type/Android.OS.Bundle/) parâmetro, que é um dicionário para armazenar e transmitir informações de estado e objetos entre atividades se o pacote não for null, isso indica a atividade está reiniciando e ele deve restaurar seu estado a partir de instância anterior. O código a seguir demonstra como recuperar valores do pacote de:

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

Uma vez `OnCreate` tiver terminado, chamará Android `OnStart`.

#### <a name="onstart"></a>OnStart

[OnStart](https://developer.xamarin.com/api/member/Android.App.Activity.OnStart/) sempre é chamado pelo sistema após `OnCreate` for concluído. As atividades podem substituir esse método se de que precisam para realizar qualquer direito tarefas específicas antes de uma atividade se torna visível, como atualizar os valores atuais dos modos de exibição dentro da atividade. Android chamará `OnResume` imediatamente depois do método.

#### <a name="onresume"></a>OnResume

As chamadas do sistema [OnResume](https://developer.xamarin.com/api/member/Android.App.Activity.OnResume/) quando a atividade está pronta para começar a interagir com o usuário.
Atividades devem substituir esse método para executar tarefas como:

-  Aumentando a taxa de quadros (uma tarefa comum na construção de jogo)
-  Iniciando animações
-  Aguardando atualizações GPS
-  Exibir alertas relevantes ou caixas de diálogo
-  Manipuladores de eventos externos de transmissão


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

`OnResume` é importante porque qualquer operação que é feito em `OnPause` deve ser não concluído em `OnResume`, pois ele é o único método de ciclo de vida que é garantido para executar após `OnPause` ao colocar a atividade de volta à vida.

#### <a name="onpause"></a>OnPause

[OnPause](https://developer.xamarin.com/api/member/Android.App.Activity.OnPause/) é chamado quando o sistema está prestes a colocar a atividade em segundo plano ou quando a atividade parcialmente fica obscurecida. Atividades devem substituir esse método, se necessário:

-   Confirmar as alterações não salvas para dados persistentes

-   Destruir ou limpar a outros objetos de consumo de recursos

-   Diminuir as taxas de quadros e animações pausas

-   Cancelar o registro de manipuladores de eventos externos ou manipuladores de notificação (ou seja, aqueles que estão vinculados a um serviço). Isso deve ser feito para evitar perdas de memória de atividade.

-   Da mesma forma, se a atividade tem exibidas caixas de diálogo nem alertas, eles devem ser limpos com o `.Dismiss()` método.

Por exemplo, o trecho de código a seguir lançará a câmera, como a atividade não é possível fazer usá-lo enquanto está em pausa:

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

Há dois métodos de ciclo de vida possíveis que serão chamados após `OnPause`:

1.  `OnResume` será chamado se a atividade for a ser retornado para o primeiro plano.
1.  `OnStop` será chamado se a atividade está sendo colocada em segundo plano.


#### <a name="onstop"></a>OnStop

[OnStop](https://developer.xamarin.com/api/member/Android.App.Activity.OnStop/) é chamado quando a atividade não está mais visível para o usuário. Isso acontece quando ocorre um dos seguintes:

-  Uma nova atividade está sendo iniciada e é cobrir essa atividade.
-  Uma atividade existente está sendo colocada em primeiro plano.
-  A atividade está sendo destruída.


`OnStop` não pode sempre ser chamado em situações de memória baixa, como quando o Android é com inanição para recursos e não é possível corretamente em segundo plano a atividade. Por esse motivo, é melhor não dependem de `OnStop` sendo chamado durante a preparação de uma atividade para destruição. Os métodos de ciclo de vida próximos que podem ser chamados depois que esse script será `OnDestroy` se a atividade está sendo desativado, ou `OnRestart` se a atividade estiver vindo de volta para interagir com o usuário.

#### <a name="ondestroy"></a>OnDestroy

[OnDestroy](https://developer.xamarin.com/api/member/Android.App.Activity.OnDestroy/) é o último método é chamado em uma instância de atividade antes de que destruído e completamente removidas da memória. Em situações extremas Android pode eliminar o processo de aplicativo que está hospedando a atividade, o que resultará em `OnDestroy` não está sendo chamado. A maioria das atividades não implementar esse método porque a maioria limpa e desligar tiver sido feito no `OnPause` e `OnStop` métodos. O `OnDestroy` método normalmente é substituído para limpar longa em execução recursos que pode vazar recursos. Um exemplo disso pode ser threads em segundo plano que foram iniciadas em `OnCreate`.

Não haverá nenhuma métodos de ciclo de vida chamados depois que a atividade foi destruída.

#### <a name="onrestart"></a>OnRestart

[OnRestart](https://developer.xamarin.com/api/member/Android.App.Activity.OnRestart/) é chamado depois que a atividade foi interrompida, antes de ele ser iniciado novamente. Um bom exemplo disso seria quando o usuário pressiona o botão página inicial em uma atividade do aplicativo. Quando isso acontece, `OnPause` e, em seguida, `OnStop` métodos são chamados, e a atividade é movida para o plano de fundo, mas não é destruída. Se o usuário, em seguida, para restaurar o aplicativo usando o Gerenciador de tarefas ou um aplicativo semelhante, Android, chamará o `OnRestart` método da atividade.

Não há nenhum diretrizes gerais para o tipo de lógica deve ser implementado em `OnRestart`. Isso ocorre porque `OnStart` sempre é invocada, independentemente se a atividade está sendo criada ou reiniciado, para todos os recursos necessários para a atividade devem ser inicializados em `OnStart`, em vez de `OnRestart`.

O próximo método de ciclo de vida chamado após `OnRestart` será `OnStart`.

### <a name="back-vs-home"></a>Faça vs. Home

Muitos dispositivos Android tem dois botões distintos: um botão "Voltar" e um botão "Home". Um exemplo disso pode ser visto na seguinte captura de tela do Android 4.0.3:

[![Botões Voltar e Home](images/image4-sml.png)](images/image4.png#lightbox)

Há uma diferença sutil entre os dois botões, mesmo que eles parecerão ter o mesmo efeito de colocar um aplicativo em segundo plano. Quando um usuário clica no botão Voltar, são informados Android que elas são realizadas com a atividade. Android destruirá a atividade. Por outro lado, quando o usuário clica no botão página inicial a atividade simplesmente é colocada no plano de fundo &ndash; Android não finalizará a atividade.

<a name="Managing_State_Throughout_the_Lifecycle" />

## <a name="managing-state-throughout-the-lifecycle"></a>Gerenciamento de estado em todo o ciclo de vida

Quando uma atividade é interrompida ou destruída o sistema oferece a oportunidade de salvar o estado da atividade do reidratação posterior.
Esse estado salvo é chamado de estado da instância. Android fornece três opções para armazenar o estado da instância durante o ciclo de vida da atividade:

1. Armazenando valores primitivos em um `Dictionary` conhecido como um [pacote](https://developer.xamarin.com/api/type/Android.OS.Bundle/) que Android usará para salvar o estado.

1. Criando uma classe personalizada que conterá valores complexos, como bitmaps. Android usará esta classe personalizada para salvar o estado.

1. Evitar o ciclo de vida de alteração de configuração e assumindo a responsabilidade completa para manter o estado em que a atividade.


Este guia aborda as duas primeiras opções.



### <a name="bundle-state"></a>Estado do pacote

A primeira opção para salvar o estado da instância é usar um objeto de dicionário de chave/valor conhecido como um [pacote](https://developer.xamarin.com/api/type/Android.OS.Bundle/).
Lembre-se de que quando uma atividade é criada e o `OnCreate` método é passado um pacote como um parâmetro, esse pacote pode ser usado para restaurar o estado da instância. Não é recomendável usar um conjunto de dados mais complexos que não rapidamente ou facilmente serializar de chave/valor pares (como bitmaps); em vez disso, ele deve ser usado para valores simples como cadeias de caracteres.

Uma atividade fornece métodos para ajudá-lo a salvar e recuperar o estado da instância do pacote:

-   [OnSaveInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnSaveInstanceState/p/Android.OS.Bundle/) &ndash; isso é chamado pelo Android quando a atividade está sendo destruída. Atividades podem implementar esse método, se necessário manter os itens de estado de chave/valor.

-   [OnRestoreInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnRestoreInstanceState/p/Android.OS.Bundle/) &ndash; isso é chamado após o `OnCreate` método é concluído e fornece outra oportunidade para uma atividade restaurar o estado após a conclusão da inicialização.

O diagrama a seguir ilustra como esses métodos são usados:

[![Fluxograma de estados de pacote](images/image3-sml.png)](images/image3.png#lightbox)

#### <a name="onsaveinstancestate"></a>OnSaveInstanceState

[OnSaveInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnSaveInstanceState/p/Android.OS.Bundle/) será chamado quando a atividade está sendo interrompida. Ela recebe um parâmetro de pacote que a atividade pode armazenar seu estado em. Quando um dispositivo apresenta uma alteração de configuração, uma atividade pode usar o `Bundle` objeto que é passado para preservar o estado de atividade, substituindo `OnSaveInstanceState`. Por exemplo, considere o seguinte código:

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

O código acima incrementa um número inteiro denominado `c` quando um botão chamado `incrementCounter` é clicado, exibe o resultado em uma `TextView` chamado `output`. Quando ocorre uma alteração de configuração - por exemplo, quando o dispositivo for girado - o código acima poderá perder o valor de `c` porque o `bundle` seria `null`, conforme mostrado na figura a seguir:

[![Exibição não mostra o valor anterior](images/07-sml.png)](images/07.png#lightbox)

Para preservar o valor de `c` neste exemplo, a atividade pode substituir `OnSaveInstanceState`, salvar o valor do conjunto, conforme mostrado abaixo:

```csharp
protected override void OnSaveInstanceState (Bundle outState)
{
  outState.PutInt ("counter", c);
  base.OnSaveInstanceState (outState);
}
```

Agora quando o dispositivo for girado para uma nova orientação, o inteiro é salvo no pacote e é recuperado com a linha:

```csharp
c = bundle.GetInt ("counter", -1);
```

> [!NOTE]
> É importante sempre chamada a implementação base de `OnSaveInstanceState` para que o estado da hierarquia do modo de exibição também pode ser salvas.



##### <a name="view-state"></a>Estado de exibição

Substituindo `OnSaveInstanceState` é um mecanismo apropriado para salvar dados transitórios em uma atividade em alterações de orientação, como o contador no exemplo acima. No entanto, a implementação padrão de `OnSaveInstanceState` cuidará de salvar dados transitórios na interface de usuário para cada modo de exibição, desde que cada exibição tem uma ID atribuída. Por exemplo, digamos que um aplicativo tem um `EditText` elemento definido no XML da seguinte maneira:

```xml
<EditText android:id="@+id/myText"
  android:layout_width="fill_parent"
  android:layout_height="wrap_content"/>
```

Como o `EditText` controle tem um `id` atribuída, quando o usuário insere alguns dados e gira o dispositivo, os dados ainda for exibidos, conforme mostrado abaixo:

[![Os dados são preservados no modo paisagem](images/08-sml.png)](images/08.png#lightbox)

#### <a name="onrestoreinstancestate"></a>OnRestoreInstanceState

[OnRestoreInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnRestoreInstanceState/p/Android.OS.Bundle/) será chamado após `OnStart`. Ele fornece uma atividade a oportunidade de restaurar qualquer estado salvo anteriormente para um pacote durante anterior `OnSaveInstanceState`. Este é o mesmo pacote que é fornecido para `OnCreate`, no entanto.

O código a seguir demonstra como o estado pode ser restaurado no `OnRestoreInstanceState`:

```csharp
protected override void OnRestoreInstanceState(Bundle savedState)
{
    base.OnRestoreSaveInstanceState(savedState);
    var myString = savedState.GetString("myString");
    var myBool = savedState.GetBoolean("myBool");
}
```

Este método existe para fornecer alguma flexibilidade ao redor quando o estado deve ser restaurado. Às vezes é mais apropriado aguardar até que todas as inicializações terminar antes de restaurar o estado da instância. Além disso, uma subclasse de uma atividade existente pode desejar somente determinados valores de restauração do estado de instância. Em muitos casos, não é necessário substituir `OnRestoreInstanceState`, pois a maioria das atividades pode restaurar o estado usando o pacote fornecido para `OnCreate`.

Para obter um exemplo de salvar o estado usando um `Bundle`, consulte o [passo a passo - estado de salvar a atividade](saving-state.md).


#### <a name="bundle-limitations"></a>Limitações de pacote

Embora `OnSaveInstanceState` torna fácil salvar dados transitórios, ele tem algumas limitações:

-   Ele não é chamado em todos os casos. Por exemplo, pressionando **início** ou **novamente** sair de uma atividade não resultará em `OnSaveInstanceState` que está sendo chamado.

-   O pacote é passado para `OnSaveInstanceState` não foi projetado para objetos grandes, como imagens. No caso de objetos grandes, salvar o objeto de [OnRetainNonConfigurationInstance](https://developer.xamarin.com/api/member/Android.App.Activity.OnRetainNonConfigurationInstance/) é preferível, conforme discutido abaixo.

-   Dados salvos usando o pacote serão serializados, que pode levar a atrasos.

Estado do pacote é útil para dados simples que não usam a quantidade de memória, enquanto *dados da instância de configuração não* é útil para dados mais complexas ou dados que é caros recuperar, por exemplo, uma chamada de serviço web ou um complicado consulta de banco de dados. Dados da instância de configuração não é salvo em um objeto, conforme necessário. A próxima seção apresenta `OnRetainNonConfigurationInstance` como uma maneira de preservar os tipos de dados mais complexos por meio de alterações de configuração.


### <a name="persisting-complex-data"></a>Persistência de dados complexos

Além dos dados persistentes no pacote, Android também oferece suporte para salvar dados por meio da substituição [OnRetainNonConfigurationInstance](https://developer.xamarin.com/api/member/Android.App.Activity.OnRetainNonConfigurationInstance/) e retornar uma instância de um `Java.Lang.Object` que contém os dados para manter. Há dois principais benefícios de usar `OnRetainNonConfigurationInstance` para salvar o estado:

-   O objeto retornado do `OnRetainNonConfigurationInstance` executa bem com tipos de dados maiores e mais complexos, como memória mantém esse objeto.

-   O `OnRetainNonConfigurationInstance` método é chamado sob demanda e somente quando necessário. Isso é mais econômico do que usar um cache manual.

Usando `OnRetainNonConfigurationInstance` é adequada para situações em que é caro recuperar os dados várias vezes, como chamadas de serviço web. Por exemplo, considere o seguinte código que pesquisa Twitter:

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

Este código recupera os resultados da web formatada como JSON, analisa-os e, em seguida, apresenta os resultados em uma lista, conforme mostrado na seguinte captura de tela:

[![Resultados exibidos na tela](images/06-sml.png)](images/06.png#lightbox)

Quando uma alteração de configuração ocorre - por exemplo, quando um dispositivo for girado - o código repete o processo. Para reutilizar os resultados originalmente recuperados e não fazem com que as chamadas de rede redundantes, desnecessários, podemos usar `OnRetainNonconfigurationInstance` para salvar os resultados, conforme mostrado abaixo:

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

Agora, quando o dispositivo for girado, os resultados originais são recuperados do `LastNonConfiguartionInstance` propriedade. Neste exemplo, os resultados consistirem de um `string[]` contendo tweets. Como `OnRetainNonConfigurationInstance` requer que um `Java.Lang.Object` retornado, o `string[]` é encapsulado em uma classe que as subclasses `Java.Lang.Object`, conforme mostrado abaixo:

```csharp
class TweetListWrapper : Java.Lang.Object
{
  public string[] Tweets { get; set; }
}
```

Por exemplo, a tentativa de usar um `TextView` como o objeto retornado do `OnRetainNonConfigurationInstance` causarem perda da atividade, conforme ilustrado pelo código a seguir:

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

Nesta seção, nós aprendemos como preservar dados simples com o `Bundle`, e manter os tipos de dados mais complexos com `OnRetainNonConfigurationInstance`.

## <a name="summary"></a>Resumo

O ciclo de vida da atividade Android fornece uma estrutura eficiente para o gerenciamento de estado de atividades em um aplicativo, mas pode ser difícil de entender e implementar. Este capítulo introduziu os diferentes estados que uma atividade pode percorrer durante seu ciclo de vida, bem como os métodos de ciclo de vida que estão associados esses estados. Em seguida, foi fornecida orientação sobre que tipo de lógica deve ser executado em cada um desses métodos.


## <a name="related-links"></a>Links relacionados

- [Tratamento de rotação](~/android/app-fundamentals/handling-rotation.md)
- [Atividade do Android](https://developer.xamarin.com/api/type/Android.App.Activity/)
