---
title: Ciclo de vida de atividade
description: As atividades são um componente fundamental dos aplicativos do Android e eles podem existir em um número de estados diferentes. O ciclo de vida de atividade começa com a instanciação e termina com a destruição e inclui muitos estados entre os dois. Quando uma atividade muda de estado, o método de evento de ciclo de vida apropriado é chamado, notificando sobre a atividade de alteração de estado do iminente e permitindo que ele executar o código para adaptar-se a essa alteração. Este artigo examina o ciclo de vida de atividades e explica a responsabilidade que uma atividade tem durante cada uma dessas alterações de estado para ser parte de um aplicativo bem comportado e confiável.
ms.prod: xamarin
ms.assetid: 05B34788-F2D2-4347-B66B-40AFD7B1D167
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/28/2018
ms.openlocfilehash: 3592a3027469cb9997d973db53d636ddea9e679d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110876"
---
# <a name="activity-lifecycle"></a>Ciclo de vida de atividade

_As atividades são um componente fundamental dos aplicativos do Android e eles podem existir em um número de estados diferentes. O ciclo de vida de atividade começa com a instanciação e termina com a destruição e inclui muitos estados entre os dois. Quando uma atividade muda de estado, o método de evento de ciclo de vida apropriado é chamado, notificando sobre a atividade de alteração de estado do iminente e permitindo que ele executar o código para adaptar-se a essa alteração. Este artigo examina o ciclo de vida de atividades e explica a responsabilidade que uma atividade tem durante cada uma dessas alterações de estado para ser parte de um aplicativo bem comportado e confiável._

## <a name="activity-lifecycle-overview"></a>Visão geral do ciclo de vida de atividade

As atividades são um conceito de programação incomum específico do Android. No desenvolvimento de aplicativos tradicionais normalmente há um método estático principal, que é executado para iniciar o aplicativo. Com o Android, no entanto, as coisas são diferentes; Aplicativos Android podem ser iniciados por meio de qualquer atividade registrada dentro de um aplicativo. Na prática, a maioria dos aplicativos terá apenas uma atividade específica que é especificada como o ponto de entrada do aplicativo. No entanto, se um aplicativo falha, ou é encerrada pelo sistema operacional, o sistema operacional pode tentar reiniciar o aplicativo em que a última atividade aberta ou de qualquer lugar dentro da pilha de atividade anterior.
Além disso, o sistema operacional pode pausar atividades quando não estiver ativos e recuperá-los, se ele está com pouco memória. Uma consideração cuidadosa deve ser feita para permitir que o aplicativo restaurar corretamente o estado que uma atividade for reiniciada, especialmente se que a atividade depende dos dados de atividades anteriores.

O ciclo de vida de atividade é implementado como uma coleção de chamadas de métodos, o sistema operacional em todo o ciclo de vida de uma atividade. Esses métodos permitem aos desenvolvedores implementar a funcionalidade que é necessária para satisfazer os requisitos de gerenciamento de estado e o recurso de seus aplicativos.

É extremamente importante para o desenvolvedor do aplicativo analisar os requisitos de cada atividade para determinar quais métodos expostos pelo ciclo de vida de atividade precisam ser implementado. Falha ao fazer isso pode resultar em instabilidade do aplicativo, falhas, sobrecarga de recursos e instabilidade do sistema operacional subjacente, possivelmente, até mesmo.

Este capítulo examina o ciclo de vida de atividade em detalhes, incluindo:

-  Estados de atividade
-  Métodos de ciclo de vida
-  Mantendo o estado de um aplicativo


Esta seção também inclui um [instruções passo a passo](~/android/app-fundamentals/activity-lifecycle/saving-state.md) que fornecem exemplos práticos sobre como salvar o estado do ciclo de vida de atividade com eficiência. No final deste capítulo, você deve ter uma compreensão de como o ciclo de vida de atividade e como dar suporte a ele em um aplicativo Android.

## <a name="activity-lifecycle"></a>Ciclo de vida de atividade

O ciclo de vida de atividade do Android consiste em uma coleção de métodos expostos dentro da classe de atividade que fornecem ao desenvolvedor de uma estrutura de gerenciamento de recursos. Essa estrutura permite que os desenvolvedores a atender aos requisitos de gerenciamento de estado exclusivo de cada atividade dentro de um aplicativo e lidar adequadamente com o gerenciamento de recursos.

### <a name="activity-states"></a>Estados de atividade

O sistema operacional Android arbitra atividades com base em seu estado. Isso ajuda a Android identificar atividades que não estão mais em uso, permitindo que o sistema operacional recuperar a memória e recursos. O diagrama a seguir ilustra os estados de que uma atividade pode percorrer durante seu ciclo de vida:

[![Diagrama de estados de atividade](images/image1-sml.png)](images/image1.png#lightbox)

Esses estados podem ser divididos em 4 grupos principais da seguinte maneira:

1.  *Em execução ou ativas* &ndash; atividades são consideradas ativo ou em execução se eles estiverem em primeiro plano, também conhecidas como a parte superior da pilha de atividade. Isso é considerado a atividade de prioridade mais alta no Android e como tal, somente será encerrado pelo sistema operacional em situações extremas, tal como se a atividade tentará usar mais memória do que está disponível no dispositivo como isso poderia fazer com que a interface do usuário pare de responder.

1.  *Em pausa* &ndash; quando o dispositivo entra em suspensão ou uma atividade é parcialmente oculto por uma atividade de novo, em tamanho normal ou transparente mas ainda estiver visível, a atividade é considerada em pausa. Atividades em pausa ainda estão ativos, ou seja, manter todas as informações de estado e de membro e permanecer anexados para o Gerenciador de janelas. Isso é considerado como a segunda atividade de prioridade mais alta no Android e, como tal, somente será encerrada pelo sistema operacional se matando essa atividade vai satisfazer os requisitos de recursos necessários para manter a atividade ativa/em execução estável e responsivo.

1.  *Parado/Backgrounded* &ndash; atividades que são completamente obscurecidas por outra atividade são consideradas parada ou em segundo plano.
    Atividades paradas ainda tentam manter suas informações de estado e membro para desde que possível, mas paradas atividades são consideradas como sendo a prioridade mais baixa de três estados e, dessa forma, o sistema operacional finalizará atividades nesse estado primeiro para satisfazer o recurso requisitos de atividades de prioridade mais alta.

1.  *Reiniciado* &ndash; é possível que uma atividade que está em qualquer lugar de em pausa para parado no ciclo de vida a ser removido da memória pelo Android. Se o usuário navega de volta para a atividade deve ser reiniciado, restaurado ao estado salvo anteriormente e, em seguida, é exibida para o usuário.


### <a name="activity-re-creation-in-response-to-configuration-changes"></a>Recriação da atividade em resposta às alterações de configuração

Para tornar mais complicados de assuntos, Android gera uma chave inglesa de mais na combinação de alterações de configuração de chamada. Alterações de configuração são atividade rápida destruição/re-creation ciclos que ocorrem quando a configuração de uma atividade é alterada, como quando o dispositivo estiver [girado](~/android/app-fundamentals/handling-rotation.md) (e a atividade precisa obter recompilado em paisagem ou retrato modo), quando o teclado é exibido (e a atividade é apresentada a oportunidade de se redimensiona), ou quando o dispositivo for colocado em um encaixe, entre outros.

Alterações de configuração ainda fazer com que as mesmas alterações de estado da atividade que podem ocorrer durante a interrupção e reinicialização de uma atividade. No entanto, para certificar-se de que um aplicativo parece responsivo e executa bem durante alterações de configuração, é importante que ser manipulados assim que possível. Por isso, o Android tem uma API específica que pode ser usada para manter o estado durante alterações de configuração.
Vamos abordar isso mais tarde na [gerenciamento de estado em todo o ciclo de vida](~/android/app-fundamentals/activity-lifecycle/index.md#Managing_State_Throughout_the_Lifecycle) seção.

### <a name="activity-lifecycle-methods"></a>Métodos de ciclo de vida de atividade

O SDK do Android e, por extensão, a estrutura do xamarin. Android fornecem um poderoso modelo para gerenciar o estado das atividades dentro de um aplicativo. Quando o estado de uma atividade está sendo alterado, a atividade é notificada pelo sistema operacional, que chama os métodos específicos em que a atividade. O diagrama a seguir ilustra esses métodos em relação ao ciclo de vida de atividade:

[![Fluxograma de ciclo de vida de atividade](images/image2-sml.png)](images/image2.png#lightbox)

Como desenvolvedor, você pode manipular as alterações de estado por meio da substituição desses métodos dentro de uma atividade. É importante observar, no entanto, que todos os métodos de ciclo de vida são chamados no thread da interface do usuário e bloquearão o sistema operacional de executar a próxima parte do trabalho de interface do usuário, como ocultar a atividade atual, exibindo uma nova atividade, etc. Como tal, o código nesses métodos deve ser o mais breve possível tornar um aplicativo a se sentir o desempenho. As tarefas de longa execução devem ser executadas em um thread em segundo plano.

Vamos examinar cada um desses métodos de ciclo de vida e seu uso:

#### <a name="oncreate"></a>OnCreate

[OnCreate](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) é o primeiro método a ser chamado quando uma atividade é criada.
`OnCreate` sempre é substituído para executar qualquer inicializações de inicialização que podem ser exigidas por uma atividade, como:

-  Criando modos de exibição
-  Inicializando variáveis
-  Associação de dados estáticos a listas


`OnCreate` leva um [pacote](https://developer.xamarin.com/api/type/Android.OS.Bundle/) parâmetro, que é um dicionário para armazenar e transmitir informações de estado e objetos entre atividades se o pacote não for nulo, isso indica que a atividade está reiniciando e ele deve restaurar seu estado a partir de instância anterior. O código a seguir ilustra como recuperar valores do pacote de:

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

[OnStart](https://developer.xamarin.com/api/member/Android.App.Activity.OnStart/) sempre é chamada pelo sistema após `OnCreate` for concluído. As atividades podem substituir este método se eles precisarem realizar qualquer direito de tarefas específicas antes de uma atividade se torna visível como atualizar os valores atuais dos modos de exibição dentro da atividade. Android chamará `OnResume` imediatamente após esse método.

#### <a name="onresume"></a>OnResume

As chamadas do sistema [OnResume](https://developer.xamarin.com/api/member/Android.App.Activity.OnResume/) quando a atividade está pronta para começar a interagir com o usuário.
As atividades devem substituir este método para executar tarefas como:

-  Aumentando a taxas de quadros (uma tarefa comum em jogo construção)
-  Iniciando a animações
-  Escuta de atualizações GPS
-  Exibir alertas relevantes ou caixas de diálogo
-  Conectar manipuladores de evento externo


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

`OnResume` é importante porque qualquer operação que é feito no `OnPause` deve ser não concluído na `OnResume`, uma vez que ele é o único método de ciclo de vida que certamente será executado após `OnPause` ao colocar a atividade de volta à vida.

#### <a name="onpause"></a>OnPause

[OnPause](https://developer.xamarin.com/api/member/Android.App.Activity.OnPause/) é chamado quando o sistema está prestes a colocar a atividade em segundo plano ou quando a atividade torna-se parcialmente obscurecida. As atividades devem substituir este método se necessário:

-   Confirmar as alterações não salvas para dados persistentes

-   Destruir ou limpeza de outros objetos, consumindo recursos

-   Diminuir as taxas de quadro e animações pausas

-   Cancelar o registro de manipuladores de eventos externos ou manipuladores de notificação (ou seja, aquelas que estão vinculados a um serviço). Isso deve ser feito para evitar vazamentos de memória de atividade.

-   Da mesma forma, se a atividade tem exibidas caixas de diálogo ou alertas, eles devem ser limpos com o `.Dismiss()` método.

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

Há dois métodos de ciclo de vida possíveis que serão chamados depois `OnPause`:

1.  `OnResume` será chamado se a atividade deve ser retornado para o primeiro plano.
1.  `OnStop` será chamado se a atividade está sendo colocada em segundo plano.


#### <a name="onstop"></a>OnStop

[OnStop](https://developer.xamarin.com/api/member/Android.App.Activity.OnStop/) é chamado quando a atividade não está mais visível ao usuário. Isso acontece quando ocorre um dos seguintes:

-  Uma nova atividade está sendo iniciada e é cobrir essa atividade.
-  Uma atividade existente é que está sendo colocada em primeiro plano.
-  A atividade está sendo destruída.


`OnStop` não pode sempre ser chamado em situações de memória baixa, como quando o Android está sem recursos e não é possível corretamente em segundo plano da atividade. Por esse motivo, é melhor não dependem de `OnStop` sendo chamado durante a preparação de uma atividade para destruição. Os métodos de ciclo de vida próxima que podem ser chamados depois que esse script será `OnDestroy` se a atividade está desaparecendo ou `OnRestart` se a atividade estiver vindo de volta para interagir com o usuário.

#### <a name="ondestroy"></a>OnDestroy

[O OnDestroy](https://developer.xamarin.com/api/member/Android.App.Activity.OnDestroy/) é o método final que é chamado em uma instância de atividade antes que ele foi destruído e completamente removido da memória. Em situações extremas Android poderá finalizar o processo do aplicativo que está hospedando a atividade, o que resultará em `OnDestroy` não está sendo invocado. A maioria das atividades não implementará este método porque a maioria limpa e desligar foi feito na `OnPause` e `OnStop` métodos. O `OnDestroy` método normalmente é substituído para limpeza de longa execução recursos que pode causar perda de recursos. Um exemplo disso pode ser threads em segundo plano que foram iniciados na `OnCreate`.

Não haverá nenhum método de ciclo de vida chamado depois que a atividade foi destruída.

#### <a name="onrestart"></a>OnRestart

[OnRestart](https://developer.xamarin.com/api/member/Android.App.Activity.OnRestart/) é chamado depois que sua atividade for interrompida, antes de ele ser iniciado novamente. Um bom exemplo disso seria quando o usuário pressiona o botão página inicial enquanto estiver em uma atividade no aplicativo. Quando isso acontece `OnPause` e, em seguida, `OnStop` são chamados de métodos e a atividade é movida para o plano de fundo, mas não será destruída. Se o usuário e em seguida, para restaurar o aplicativo usando o Gerenciador de tarefas ou um aplicativo semelhante, o Android chamará o `OnRestart` método da atividade.

Não há nenhum diretrizes gerais para o tipo de lógica deve ser implementado em `OnRestart`. Isso ocorre porque `OnStart` sempre é invocado, independentemente se a atividade está sendo criada ou que está sendo reiniciado, portanto, todos os recursos exigidos pela atividade devem ser inicializados no `OnStart`, em vez de `OnRestart`.

O próximo método de ciclo de vida chamado após `OnRestart` será `OnStart`.

### <a name="back-vs-home"></a>Faça vs. Home

Muitos dispositivos Android tem dois botões distintas: um botão "Voltar" e um botão "Home". Um exemplo disso pode ser visto na seguinte captura de tela do Android 4.0.3:

[![Botões Voltar e Home](images/image4-sml.png)](images/image4.png#lightbox)

Há uma diferença sutil entre os dois botões, mesmo que eles parecem ter o mesmo efeito de colocar um aplicativo em segundo plano. Quando um usuário clica no botão Voltar, ele estão dizendo ao Android que eles são feitos com a atividade. Android destruirá a atividade. Por outro lado, quando o usuário clica no botão Início a atividade simplesmente é colocada em segundo plano &ndash; Android não finalizará a atividade.

<a name="Managing_State_Throughout_the_Lifecycle" />

## <a name="managing-state-throughout-the-lifecycle"></a>Gerenciando o estado em todo o ciclo de vida

Quando uma atividade é interrompida ou destruída o sistema fornece uma oportunidade de salvar o estado da atividade para reidratação posterior.
Isso salva o estado é chamado de estado da instância. O Android fornece três opções para armazenar o estado da instância durante o ciclo de vida de atividade:

1. Armazenando valores primitivos em um `Dictionary` conhecida como uma [pacote](https://developer.xamarin.com/api/type/Android.OS.Bundle/) que Android usará para salvar o estado.

1. Criando uma classe personalizada que conterá valores complexos, como bitmaps. Android usará essa classe personalizada para salvar o estado.

1. Evitando o ciclo de vida de alteração de configuração e assumir a responsabilidade completa para manter o estado em que a atividade.


Este guia aborda as duas primeiras opções.



### <a name="bundle-state"></a>Estado do pacote

A principal opção para salvar o estado da instância é usar um objeto de dicionário de chave/valor conhecido como um [pacote](https://developer.xamarin.com/api/type/Android.OS.Bundle/).
Lembre-se de que quando uma atividade é criada e o `OnCreate` método recebe um pacote como um parâmetro, esse pacote pode ser usado para restaurar o estado da instância. Não é recomendável usar um pacote de dados mais complexos que não rapidamente ou serializam facilmente pares de chave/valor (como bitmaps); em vez disso, ele deve ser usado para valores simples como cadeias de caracteres.

Uma atividade fornece métodos para ajudá-lo salvando e recuperando o estado da instância no pacote:

-   [OnSaveInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnSaveInstanceState/p/Android.OS.Bundle/) &ndash; isso é invocado pelo Android quando a atividade está sendo destruída. Atividades podem implementar esse método se elas precisam manter os itens de estado de chave/valor.

-   [OnRestoreInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnRestoreInstanceState/p/Android.OS.Bundle/) &ndash; isso é chamado após o `OnCreate` método for concluído e fornece outra oportunidade para uma atividade restaurar seu estado após a conclusão da inicialização.

O diagrama a seguir ilustra como esses métodos são usados:

[![Fluxograma de estados de pacote](images/image3-sml.png)](images/image3.png#lightbox)

#### <a name="onsaveinstancestate"></a>OnSaveInstanceState

[OnSaveInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnSaveInstanceState/p/Android.OS.Bundle/) será chamado quando a atividade está sendo interrompida. Ele recebe um parâmetro de pacote que a atividade pode armazenar seu estado no. Quando um dispositivo apresenta uma alteração de configuração, uma atividade pode usar o `Bundle` objeto que é passado para preservar o estado da atividade, substituindo `OnSaveInstanceState`. Por exemplo, considere o seguinte código:

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

O código acima adiciona um número inteiro denominado `c` quando um botão chamado `incrementCounter` é clicado, exibe o resultado em uma `TextView` denominada `output`. Quando ocorre uma alteração de configuração - por exemplo, quando o dispositivo for girado – o código acima pode perder o valor de `c` porque o `bundle` seria `null`, conforme mostrado na figura abaixo:

[![Exibição não mostra o valor anterior](images/07-sml.png)](images/07.png#lightbox)

Para preservar o valor de `c` neste exemplo, a atividade pode substituir `OnSaveInstanceState`, salvando o valor no pacote, conforme mostrado abaixo:

```csharp
protected override void OnSaveInstanceState (Bundle outState)
{
  outState.PutInt ("counter", c);
  base.OnSaveInstanceState (outState);
}
```

Agora, quando o dispositivo for girado para uma nova orientação, o inteiro é salvo no pacote e é recuperado com a linha:

```csharp
c = bundle.GetInt ("counter", -1);
```

> [!NOTE]
> É importante sempre chamada a implementação base de `OnSaveInstanceState` para que o estado da hierarquia de exibição também pode ser salvos.



##### <a name="view-state"></a>Estado de exibição

Substituindo `OnSaveInstanceState` é um mecanismo apropriado para salvar dados transitórios em uma atividade em alterações de orientação, como o contador no exemplo acima. No entanto, a implementação padrão de `OnSaveInstanceState` se encarregará de salvar dados transitórios na interface do usuário para cada exibição, desde que cada exibição tem uma ID atribuída. Por exemplo, digamos que um aplicativo tem um `EditText` elemento definido em XML da seguinte maneira:

```xml
<EditText android:id="@+id/myText"
  android:layout_width="fill_parent"
  android:layout_height="wrap_content"/>
```

Uma vez que o `EditText` controle tem um `id` atribuída, quando o usuário insere alguns dados e gira o dispositivo, os dados ainda for exibidos, conforme mostrado abaixo:

[![Os dados são preservados no modo paisagem](images/08-sml.png)](images/08.png#lightbox)

#### <a name="onrestoreinstancestate"></a>OnRestoreInstanceState

[OnRestoreInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnRestoreInstanceState/p/Android.OS.Bundle/) será chamado depois `OnStart`. Ele fornece uma atividade a oportunidade de restaurar qualquer estado salvo anteriormente para um pacote durante anterior `OnSaveInstanceState`. Isso é o mesmo pacote que é fornecido para `OnCreate`, no entanto.

O código a seguir demonstra como o estado pode ser restaurado no `OnRestoreInstanceState`:

```csharp
protected override void OnRestoreInstanceState(Bundle savedState)
{
    base.OnRestoreSaveInstanceState(savedState);
    var myString = savedState.GetString("myString");
    var myBool = savedState.GetBoolean("myBool");
}
```

Este método existe para fornecer alguma flexibilidade em torno de quando o estado deve ser restaurado. Às vezes é mais apropriado aguardar até que todas as inicializações são feitas antes de restaurar o estado da instância. Além disso, uma subclasse de uma atividade existente pode desejar somente determinados valores de restauração do estado de instância. Em muitos casos, não é necessário substituir `OnRestoreInstanceState`, uma vez que a maioria das atividades pode restaurar o estado usando o pacote fornecido para `OnCreate`.

Para obter um exemplo de salvar o estado usando um `Bundle`, consulte o [passo a passo: salvando a atividade estado](saving-state.md).


#### <a name="bundle-limitations"></a>Limitações de pacote

Embora `OnSaveInstanceState` torna fácil salvar dados transitórios, ele tem algumas limitações:

-   Ele não é chamado em todos os casos. Por exemplo, pressionar **página inicial** ou **volta** sair de uma atividade não resultará em `OnSaveInstanceState` que está sendo chamado.

-   O pacote é passado para `OnSaveInstanceState` não foi projetado para objetos grandes, como imagens. No caso de objetos grandes, salvar o objeto de [OnRetainNonConfigurationInstance](https://developer.xamarin.com/api/member/Android.App.Activity.OnRetainNonConfigurationInstance/) é preferível, conforme discutido abaixo.

-   Dados salvos usando o pacote são serializados, o que pode levar a atrasos.

Estado do pacote é útil para dados simples que não usam a quantidade de memória, enquanto *dados de instância não-configuração* é útil para dados mais complexo ou dados que é caros recuperar, por exemplo, de uma chamada de serviço web ou um complicado consulta de banco de dados. Dados da instância de configuração não é salvo em um objeto conforme necessário. A próxima seção apresenta `OnRetainNonConfigurationInstance` como uma maneira de preservar os tipos de dados mais complexos por meio de alterações de configuração.


### <a name="persisting-complex-data"></a>Persistência de dados complexos

Além de manter os dados no pacote, Android também dá suporte a salvamento de dados por meio da substituição [OnRetainNonConfigurationInstance](https://developer.xamarin.com/api/member/Android.App.Activity.OnRetainNonConfigurationInstance/) e retornar uma instância de um `Java.Lang.Object` que contém os dados para persistir. Há dois principais benefícios de usar `OnRetainNonConfigurationInstance` para salvar o estado:

-   O objeto retornado do `OnRetainNonConfigurationInstance` executa bem com tipos de dados maiores e mais complexas, como memória mantém esse objeto.

-   O `OnRetainNonConfigurationInstance` método é chamado sob demanda e somente quando necessário. Isso é mais econômico do que usando um cache manual.

Usando `OnRetainNonConfigurationInstance` é adequado para situações em que é caro recuperar os dados várias vezes, como chamadas de serviço web. Por exemplo, considere o seguinte código que pesquisa o Twitter:

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

Esse código recupera os resultados da web formatada como JSON, analisa-os e, em seguida, apresenta os resultados em uma lista, conforme mostrado na seguinte captura de tela:

[![Resultados exibidos na tela](images/06-sml.png)](images/06.png#lightbox)

Quando uma alteração de configuração ocorre - por exemplo, quando um dispositivo for girado - o código repete o processo. Para reutilizar os resultados recuperados originalmente e não causa chamadas de rede desnecessária, redundantes, podemos usar `OnRetainNonconfigurationInstance` para salvar os resultados, conforme mostrado abaixo:

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

Agora, quando o dispositivo for girado, os resultados originais são recuperados do `LastNonConfiguartionInstance` propriedade. Neste exemplo, os resultados consistem em um `string[]` contendo tweets. Uma vez que `OnRetainNonConfigurationInstance` requer que um `Java.Lang.Object` retornado, o `string[]` é encapsulada em uma classe que pode efetuar subclasses `Java.Lang.Object`, conforme mostrado abaixo:

```csharp
class TweetListWrapper : Java.Lang.Object
{
  public string[] Tweets { get; set; }
}
```

Por exemplo, a tentativa de usar um `TextView` como o objeto retornado do `OnRetainNonConfigurationInstance` vazará a atividade, conforme ilustrado pelo código a seguir:

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

Nesta seção aprendemos como preservar os dados de estado simples com o `Bundle`, e manter os tipos de dados mais complexos com `OnRetainNonConfigurationInstance`.

## <a name="summary"></a>Resumo

O ciclo de vida de atividade do Android fornece uma estrutura poderosa para o gerenciamento de estado de atividades dentro de um aplicativo, mas ele pode ser difícil de entender e implementar. Este capítulo introduziu os diferentes estados em que uma atividade pode percorrer durante seu ciclo de vida, bem como os métodos de ciclo de vida que estão associados esses estados. Em seguida, foi fornecidas diretrizes sobre que tipo de lógica deve ser executada em cada um desses métodos.


## <a name="related-links"></a>Links relacionados

- [Tratamento de rotação](~/android/app-fundamentals/handling-rotation.md)
- [Atividade do Android](https://developer.xamarin.com/api/type/Android.App.Activity/)
